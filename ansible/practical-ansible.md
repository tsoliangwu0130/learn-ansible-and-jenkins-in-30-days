# 實戰 Ansible

對 Ansible 有了初步認識了以後，接下來我會用前面提過的[這個專案](https://github.com/tsoliangwu0130/my-ansible)來在主機上安裝並運行 Jenkins。雖然一定還有很多改進的空間，但希望可以透過這個真實的例子，來介紹一些我在開發 Ansible 上的設計概念以及常用技巧。

#### Vagrantfile

由於在實務上，我們很常會碰到要同時操作多台主機的情況（例如同時有 web server 以及 database server ）因此我會習慣在 `Vagrantfile` 裡面將不同主機的配置做[區隔](https://www.vagrantup.com/docs/multi-machine/#defining-multiple-machines)。

除此之外，讀者可以看到我在這個專案的 [Vagrantfile](https://github.com/tsoliangwu0130/my-ansible/blob/master/Vagrantfile) 裡還多配置了以下這一行：

```ruby
server_config.vm.network "forwarded_port", guest: 8080, host: 8080
```

這是因為當 Jenkins 服務啟動時，預設運行的埠口 (port) 是 8080，但由於 Jenkins 是運行在 Vagrant 虛擬機中，我們必須將虛擬機的 port 8080 轉發 (forward) 到本機端的 port 8080 上，我們才可以透過本機端的瀏覽器來訪問 Jenkins。如果本機端的 8080 已經有其他服務在使用，讀者可以自行調整 `host: 8080` 到想要 forward 的 port 上。

#### inventory & ansible.cfg

在本專案中的 [inventory](https://github.com/tsoliangwu0130/my-ansible/blob/master/inventory) 以及 [ansible.cfg](https://github.com/tsoliangwu0130/my-ansible/blob/master/ansible.cfg) 非常單純，在前面幾個章節內的介紹就大概有涵蓋到這裡的配置，所以在這裡就不再重述。

#### playbook

我們這次要運行的是 [docker-jenkins.yml](https://github.com/tsoliangwu0130/my-ansible/blob/master/docker-jenkins.yml) 這份 playbook。這個 playbook 的主要功能就是透過 Ansible 在名為 `server` 的 managed node 上啟動一個 Docker 容器，並在該容器內運行 Jenkins。

眼尖的讀者可能發現我在 playbook 內只定義了一個運行的 role: [docker-jenkins](https://github.com/tsoliangwu0130/my-ansible/tree/master/roles/docker-jenkins)，但在這個 role 的[任務流程](https://github.com/tsoliangwu0130/my-ansible/blob/master/roles/docker-jenkins/tasks/main.yml)內並沒有提到如何安裝 Docker。這是因為我已經將 Docker 的安裝流程定義在另外一個 [docker](https://github.com/tsoliangwu0130/my-ansible/tree/master/roles/docker) 這個 role 裡面了。我不希望 Docker 的安裝步驟被寫死在 docker-jenkins 中，這樣的做法在未來若有其他服務也需要安裝 Docker 會比較方便我重複利用。我在 docker-jenkins 裡的 [meta/](https://github.com/tsoliangwu0130/my-ansible/blob/master/roles/docker-jenkins/meta/main.yml#L3) 中定義了這個 role 的依賴關係，所以雖然今天我只在 playbook 中呼叫 docker-jenkins 這個 role，但 Ansbile 依然會先執行 meta 內的 role。如果讀者不喜歡這樣的做法，也可以將 playbook 改寫成以下這樣：

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

另外，在 playbook 中我還定義了 `vars` 這個群組變數。這裡面的變數是用來初始化 Jenkins 的預設管理者 (admin) 的。這裡讀者可能會有的疑問是，在之前的章節中，不是有提過變數可以被定義每個 role 資料下的 `defaults/` 或是 `vars/` 中嗎？為什麼這裡又需要在 playbook 中定義其他變數呢？這是因為很多時候我們都會將開發好的 role 分享給開發團隊的其他成員甚至是在網路上開源，這時候如果將變數寫死在 role 的 `defaults/` 或是 `vars/` 中，一來除了會大大降低這個 role 的運用彈性外，二來也有可能會不小心將一些機密資料 (e.g. API token 或是 user account/password) 暴露在分享的 role 當中。

因此，為了兼顧 Ansible 的彈性及安全性，我會習慣把這類比較敏感的變數資料透過 playbook 傳遞到 role 當中。然後在 repository 內建立一個類似 [playbook_example](https://github.com/tsoliangwu0130/my-ansible/blob/master/playbook_example.yml) 的指引，或是在 README 文件中提示使用者運行該 playbook 需要設定的變數及注意事項。
