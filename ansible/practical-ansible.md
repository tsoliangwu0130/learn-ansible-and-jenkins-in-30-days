# 實戰 Ansible

對 Ansible 有了初步認識了以後，接下來我會用前面提過的[這個專案](https://github.com/tsoliangwu0130/my-ansible)來在 managed node 上安裝並運行 Jenkins。雖然一定還有很多改進的空間，但希望可以透過這個真實的例子，來介紹一些我在開發 Ansible 上的設計概念以及常用技巧。

#### Vagrantfile

由於在實務上，我們很常會碰到要同時操作多台主機的情況（例如同時有 web server 以及 database server ），因此我會習慣在 `Vagrantfile` 裡面將不同主機的配置做[區隔](https://www.vagrantup.com/docs/multi-machine/#defining-multiple-machines)。

除此之外，讀者可以看到我在這個專案的 [Vagrantfile](https://github.com/tsoliangwu0130/my-ansible/blob/master/Vagrantfile) 裡還多配置了以下這一行：

```ruby
server_config.vm.network "forwarded_port", guest: 8080, host: 8080
```

這是因為當 Jenkins 服務啟動時，預設運行的埠口 (port) 是 8080，但由於 Jenkins 是運行在 Vagrant 虛擬機中，我們必須將虛擬機的 port 8080 轉發 (forward) 到本機端的 port 8080 上，我們才可以透過本機端的瀏覽器來訪問 Jenkins。如果本機端的 8080 已經有其他服務在使用，讀者可以自行調整 `host: 8080` 到想要 forward 的 port 上。

#### inventory & ansible.cfg

在本專案中的 [inventory](https://github.com/tsoliangwu0130/my-ansible/blob/master/inventory) 以及 [ansible.cfg](https://github.com/tsoliangwu0130/my-ansible/blob/master/ansible.cfg) 非常單純，在前面幾個章節內的介紹就大概有涵蓋到這裡的配置，所以在這裡就不再重述。

#### playbook

我們這次要運行的是 [docker-jenkins.yml](https://github.com/tsoliangwu0130/my-ansible/blob/master/docker-jenkins.yml) 這份 playbook。這個 playbook 的主要功能就是透過 Ansible 在 `server` 這個 managed node 上啟動一個 Docker 容器，並在該容器內運行 Jenkins。

眼尖的讀者可能發現我在 playbook 內只定義了一個運行的 role - [docker-jenkins](https://github.com/tsoliangwu0130/my-ansible/tree/master/roles/docker-jenkins)，但在這個 role 的[任務流程](https://github.com/tsoliangwu0130/my-ansible/blob/master/roles/docker-jenkins/tasks/main.yml)內並沒有提到如何安裝 Docker。這是因為我已經將 Docker 的安裝流程定義在 [docker](https://github.com/tsoliangwu0130/my-ansible/tree/master/roles/docker) 這個 role 裡面了。我不希望 Docker 的安裝步驟被寫死在 docker-jenkins 中，這樣的做法在未來若有其他服務也需要安裝 Docker 的話，會比較方便我們重複利用。我在 docker-jenkins 裡的 [meta/](https://github.com/tsoliangwu0130/my-ansible/blob/master/roles/docker-jenkins/meta/main.yml#L3) 資料下中定義了這個 role 的依賴關係，所以雖然今天我在 playbook 中只調用了 docker-jenkins，但 Ansbile 依然會先執行所有 meta 中的任務。如果讀者不習慣這樣的做法，也可以將 playbook 改寫成以下這樣：

```yml
---
- hosts: server
  roles:
    - { role: docker, become: yes }
    - { role: docker-jenkins, become: yes }
  vars:
    - jenkins_admin_user: admin
    - jenkins_admin_pass: admin
```

可能有部分讀者會覺得這樣的寫法比較清楚，因為這兩種做法是完全等效的，所以選擇一個自己比較喜歡的風格即可。

另外，在 playbook 中我還定義了 `vars` 這個群組變數。這裡面的變數是用來初始化 Jenkins 的預設管理者 (admin) 的。這裡讀者可能會有的疑問是，在之前的章節中，不是有提過變數可以被定義每個 role 資料下的 `defaults/` 或是 `vars/` 中嗎？為什麼這裡又需要在 playbook 中定義其他變數呢？這是因為很多時候我們都會將開發好的 role 分享給團隊的其他成員，甚至是在網路上開源，這時候如果將變數寫死在 role 的 `defaults/` 或是 `vars/` 中，一來除了會大大降低這個 role 的運用彈性外，二來也有可能會不小心將一些機密資料 (e.g. API token 或是 user account/password) 暴露在要分享的 role 之中。

因此，為了兼顧 Ansible 的彈性及安全性，我會習慣把這類比較敏感的變數資料透過 playbook 傳遞到 role 當中。然後在 repository 內建立一個類似 [playbook_example](https://github.com/tsoliangwu0130/my-ansible/blob/master/playbook_example.yml) 的指引，或是在 README 文件中提示使用者運行該 playbook 需要設定的變數及注意事項。

#### roles

在運行 playbook 後，根據定義的規則，Ansible 會直接調用 [docker-jenkins](https://github.com/tsoliangwu0130/my-ansible/tree/master/roles/docker-jenkins) 這個 role。然而，如同我在之前提到的一樣，因為這個 role 有其依賴，所以 Ansible 在執行這個 role 之前，會先依序將 `meta/` 下的所有 `dependencies` 運行一遍，而在這裡被呼叫 role 的就是 [docker](https://github.com/tsoliangwu0130/my-ansible/tree/master/roles/docker)。

##### 變數與迴圈 - roles/docker

因為 docker 是一個獨立的 role，所以在這個 role 底下並沒有 `meta/` 目錄的需要。如果沒有任何前置作業，Ansible 就會去 [tasks/main.yml](https://github.com/tsoliangwu0130/my-ansible/blob/master/roles/docker/tasks/main.yml) 裡面開始執行定義的任務。

在這個任務清單中，大致上來說執行了以下幾個步驟：

1. 由於我們要使用 [https://download.docker.com/linux/ubuntu/gpg](https://github.com/tsoliangwu0130/my-ansible/blob/master/roles/docker/defaults/main.yml#L3) 來當作 Docker 在 apt 上的 keyserver，因此在新增這個 key 之前需要先在 Ubuntu 上安裝一些套件 (package) 來讓 apt 可以使用 [HTTPS](https://en.wikipedia.org/wiki/HTTPS)：

    ```yml
    - name: Install packages to allow apt to use a repository over HTTPS
      apt:
        name: "{{ item }}"
        update_cache: yes
      with_items:
        - apt-transport-https
        - ca-certificates
        - software-properties-common
    ```

    其中請特別留意，`with_items` 與 `item` 是 Ansible 中的標準[迴圈 (loop)](http://docs.ansible.com/ansible/latest/playbooks_loops.html#standard-loops) 寫法。如果今天有多個項目需要依次迭代 (iterate)，就可以用這樣的寫法來實現迴圈。這是在 Ansible 開發中一定會一直使用到的一種技巧。另外，如果每一次的迭代有不只一個變數，還可以運用下面這種技巧：

    ```yml {% raw %}
    - name: add several users
      user:
        name: "{{ item.name }}"
        state: present
        groups: "{{ item.groups }}"
      with_items:
        - { name: 'testuser1', groups: 'wheel' }
        - { name: 'testuser2', groups: 'root' }
    {% endraw %}
    ```

    這樣一來，在第一次的迭代中 `item.name` 與 `item.groups` 分別會被迭代成 `testuser1` 與 `wheel`，但在第二次的迭代中則會變成 `testuser2` 與 `root`。

    另外還要補充一點，`{% raw %}{{ item }}{% endraw %}` 周圍的兩個大括號是 Ansible 基於 Jinja2 系統下[使用變數](http://docs.ansible.com/ansible/latest/playbooks_variables.html#using-variables-about-jinja2)的方式。若變數是在描敘句的開頭，則還需要再[加上兩個引號 (quote)](http://docs.ansible.com/ansible/latest/playbooks_variables.html#hey-wait-a-yaml-gotcha)。
