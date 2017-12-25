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

#### roles/pip - Error Handling

同樣的，由於在 Docker 安裝的過程中，會需要使用 pip 來安裝一些套件 (package)，因此在 docker 裡面又再度調用了 [pip](https://github.com/tsoliangwu0130/my-ansible/blob/master/roles/pip/tasks/main.yml) 這個 role。最後，因為 pip 並沒有任何前置作業，所以 Ansible 就會開始從 [tasks/main.yml](https://github.com/tsoliangwu0130/my-ansible/blob/master/roles/pip/tasks/main.yml) 執行定義的任務：

```yml
---
- name: Check if pip is already installed
  command: pip --version
  ignore_errors: true
  changed_when: false
  register: pip_is_installed

- name: Install pip
  apt:
    name: python-pip
    update_cache: yes
  when: pip_is_installed.rc != 0
```

跟之前的例子不一樣的是，在這裡我替這個 role 加上了一個執行[條件](http://docs.ansible.com/ansible/latest/playbooks_conditionals.html#the-when-statement)。如果讀者有試著運行我們在前面章節撰寫的 pip 安裝流程，應該會發現安裝 pip 其實需要花費不少的時間。所以設計上我希望在安裝 pip 之前先透過 `pip --version` 的指令來確認 pip 是否已經安裝，如果已經安裝，就跳過接下來的安裝步驟。我並不希望每一次 Ansible 都強制將其更新、然後重新安裝一次。

這裡利用了 Ansible 的 [error handling](http://docs.ansible.com/ansible/latest/playbooks_error_handling.html) 來做指令檢查。因為如果 pip 並沒有被安裝而我們仍然執行了 `pip --version` 這條指令，會導致 Ansible 抱怨 pip 並沒有被正確安裝，接著在該任務失敗之後中斷整個安裝步驟。為了避免以上狀況，我們在第一個 task 下了參數 `ignore_errors: yes` 來通知 Ansible 若該任務失敗就[忽略](http://docs.ansible.com/ansible/latest/playbooks_error_handling.html#ignoring-failed-commands)它，但依然透過前面使用過的 `register` 技巧來把執行結果儲存在 `pip_is_installed` 的變數裡，並依據該結果來判斷是否要執行 pip 安裝。

#### roles/docker - Loop

在安裝完 pip 之後，Ansible 緊接著就會回來執行 docker 這個 role。在這個任務清單中，大致上來說執行了以下幾個步驟：

1. 由於我們要使用 [https://download.docker.com/linux/ubuntu/gpg](https://github.com/tsoliangwu0130/my-ansible/blob/master/roles/docker/defaults/main.yml#L3) 來當作 Docker 在 apt 上的 keyserver，因此在新增這個 key 之前需要先在 Ubuntu 上安裝一些套件來讓 apt 可以使用 [HTTPS](https://en.wikipedia.org/wiki/HTTPS)：

    ```yml {% raw %}
    - name: Install packages to allow apt to use a repository over HTTPS
      apt:
        name: "{{ item }}"
        update_cache: yes
      with_items:
        - apt-transport-https
        - ca-certificates
        - software-properties-common
    {% endraw %}
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

2. 接著依序加入 Docker 的 apt_key 和 apt_repository，然後開始執行 Docker 的套件安裝。
3. 透過 pip 安裝 docker-py 這個 Python 套件，這樣接下來的 role 才可以使用 Ansible 的 [docker](http://docs.ansible.com/ansible/latest/docker_module.html) module 來操作 Docker。讀者在查找 Ansible module 的時候要特別注意一下該模組是否有使用上的需求 (requirements)，因為並非所有 Ansible 的 module 都是預設可用的 (e.g. [docker](http://docs.ansible.com/ansible/latest/docker_module.html), [jenkins_job](http://docs.ansible.com/ansible/latest/jenkins_job_module.html))。
4. 最後，建立 docker 的主目錄 (home directory)。至此，Docker 的安裝就順利完成了。

#### roles/docker-jenkins - Template

安裝好 Docker 以後，最後終於來到 [docker-jenkins](https://github.com/tsoliangwu0130/my-ansible/tree/master/roles/docker-jenkins) 這個 role 了。在 [tasks/main.yml](https://github.com/tsoliangwu0130/my-ansible/blob/master/roles/docker-jenkins/tasks/main.yml) 的一開始，我使用了 `include` 來調用了 [tasks/setup.yml](https://github.com/tsoliangwu0130/my-ansible/blob/master/roles/docker-jenkins/tasks/setup.yml) 來進行一些前置作業。這種做法可以有效讓我們將任務清單根據用途做一個簡單的區分，並只將主要輪廓定義在 main.yml 內，這樣一來增加易讀性，二來也可以減少未來在維護上的難度。

> 註：雖然 `include` 描述句在 Ansible 2.4 版後已經被官方棄用 (deprecate)，但現階段還是可以使用。然而，筆者仍然建議讀者儘速將 `include` 使用 `import_tasks` 以及 `include_tasks` 做替換。更多細節可以參考這份[官方文件](http://docs.ansible.com/ansible/latest/playbooks_reuse.html#creating-reusable-playbooks)。

在 setup.yml 中，先依序建立了 `docker-jenkins` 的工作目錄，並將 `files/plugin.txt` 複製到該目錄下。這個 plugin 清單是 Jenkins 在初始安裝過程中需要被安裝的列表，在等等的安裝過程會被使用到。接著，我們透過了 Ansible 的 [template](http://docs.ansible.com/ansible/latest/template_module.html) 來將 [security.groovy](https://github.com/tsoliangwu0130/my-ansible/blob/master/roles/docker-jenkins/templates/security.groovy.j2) 以及 [Dockerfile](https://github.com/tsoliangwu0130/my-ansible/blob/master/roles/docker-jenkins/templates/Dockerfile.j2) 這兩個檔案複製到 managed node 上。

1. security.groovy.j2

    這個檔案的主要功能是讓我們在使用 Ansible 部署 Jenkins 的時候可以直接初始化一個系統管理者，而不用中斷部署流程來手動進行使用者設定。還記得我們在運行 playbook 時傳遞的 [jenkins_admin_user 以及 jenkins_admin_pass](https://github.com/tsoliangwu0130/my-ansible/blob/master/docker-jenkins.yml#L5-L7) 這兩個變數嗎？透過 template 的模組，我們就可以將之前定義的變數套用到這個[初始化腳本](https://github.com/tsoliangwu0130/my-ansible/blob/master/roles/docker-jenkins/templates/security.groovy.j2#L10-L11)。這樣一來，我們就可以這個 role 分享給開發團隊成員，而不用擔心系統管理者的資料不小心洩漏出去。

2. Dockerfile.j2

    這個檔案定義了 Docker 要如何建立我們的容器映像檔 (image)，某種程度上與 Vagrantfile 蠻類似的。在[這裡](https://github.com/tsoliangwu0130/my-ansible/blob/master/roles/docker-jenkins/templates/Dockerfile.j2#L5-L11)我還利用了 Jinja2 的[條件語句](http://jinja.pocoo.org/docs/2.10/templates/#if)來判斷變數是否有定義。

接著，將所需檔案都準備好之後，接著回到 docker-jenkins，使用 [docker-image](http://docs.ansible.com/ansible/latest/docker_image_module.html#docker-image) 以及 [docker_container](http://docs.ansible.com/ansible/latest/docker_container_module.html#docker-container) 這兩個在 Ansible 2.1 後釋出的模組來直接操作 Docker image 的建立與運行容器。

最後，因為當 Jenkins 在安裝完成後，會需要約 10 至 30 秒的時間來作啟動，所以我們希望透過下面這個任務：

```yml
- name: Wait for jenkins to come up
  uri:
    url: "{{jenkins_url}}"
  register: jenkins_url_result
  until: jenkins_url_result.status == 200
  retries: 5
  delay: 10
```

來確保若還有接下來的部署任務，在進入下一個任務前 Jenkins 已經正確運行，進而避免當需要直接操作 Jenkins 時產生沒有回應的問題。
