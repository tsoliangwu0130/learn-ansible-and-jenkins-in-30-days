# 撰寫第一個 Ansible Role

在了解 playbook 的基本架構與運行方式後，我會在接下來的章節內介紹如何使用 Ansible 搭建起 [Jenkins](https://jenkins.io/) 的運行環境。透過實際的例子，相信讀者會對操作 Ansible 將會更加熟練與靈活。

#### 使用 Docker 運行 Jenkins

安裝 Jenkins 有許多方法，根據作業環境的不同也有可能會有安裝上的差異。這次我會介紹如何在 managed node 上安裝 [Docker](https://www.docker.com/) 這個容器技術工具，並將我們的 Jenkins 部署在容器之下運行。

使用容器技術來部署產品有相當多好處，一來是容器通常非常輕量級，啟動一個容器所需的資源都遠比運行一整個虛擬機例如 Vagrant 來的少許多，因此使用容器來分享開發環境及部署產品是業界中越來越常見的手法。除此之外，由於容器之間的資源是互相隔離的，這樣也同時可以避免應用程式之間常見的資源互相污染的狀況發生。礙於篇幅限制，Docker 容器的教學就不在這裡多加著墨，關於更進一步的介紹有興趣的讀者可以自行在網路上搜尋。

要使用 Docker 運行 Jenkins 我們需要建立一個 Docker 的映像檔 (image)，我們當然大可以從頭自己撰寫一個 `Dockerfile` 來建立容器環境，但所幸 Jenkins 也在 Docker Hub 上釋出了[官方的 Jenkins image](https://hub.docker.com/_/jenkins/)，所以對使用者來說，所有繁瑣的安裝細節都已經被 Docker 包辦，我們剩下要做的就只剩：

1. 在 managed node 上安裝 Docker
2. 下載 Jenkins image
3. 在 Docker container 內運行 Jenkins

#### 什麼是 Ansible Role？

我們在前面的章節內學習了如何撰寫 Ansible playbook，並將我們的工作清單以 task 的方式在 playbook 中表列下來。然而，如果 Ansible 只能做到這樣的程度，充其量我們只能說這是一個比較方便閱讀的 Shell script 罷了。若是今天我們清單中的任務有上百個，這樣我們的 playbook 也可能會變得非常冗長，就算語法再如何易讀，整體而言 playbook 還是會變得十分難以理解。另外，很多時候其實我們會希望有部分的部署內容是可以被其他不同的 playbook 重新使用。舉例來說，很多服務都可以直接使用 [pip](https://en.wikipedia.org/wiki/Pip_(package_manager) 這個套件管理來進行安裝，我們並不會希望在每一個不同的 playbook 中都要重新定義一次 pip 的安裝方法。因此，為了解決上述的問題，Ansible 提供了我們在撰寫自動化腳本時一個[角色 (role)](http://docs.ansible.com/ansible/playbooks_roles.html) 的概念。我們可以透過撰寫屬於自己的 role 來讓所有 playbook 重複使用，藉此提升透過 Ansible 自動化的靈活度。

#### 我的第一個 role

考量到在安裝 Docker 的過程中會需要用到 [cURL](https://en.wikipedia.org/wiki/CURL) 這項工具，同時，這個工具很可能會在未來頻繁地被不同的 playbook 重複使用，因此，我們在這裡就來介紹如何透過 Ansible 來安裝 cURL ，並將其寫成一個可以重複被利用的 role ，而非 playbook 中的一個 task。

讓我們在工作資料夾下依照以下結構新增檔案 (新增 `roles/curl/main.yml`)：

```shell
workspace
├── Vagrantfile
├── inventory
├── playbook.yml
└── roles
    └── curl
        └── tasks
            └── main.yml
```

在這個結構下， `curl` 就是我們的第一個 role 的名稱，而這個 role 的工作流程就會被我們定義在下面的 `tasks/main.yml` 之中。現在打開 `curl/tasks/main.yml` 並在其中寫入以下內容：

```yml
---
  - name: install curl
    apt:
      name: curl
```

我們在這個 role 的內容中呼叫了 Ansible 內建模組 [apt](http://docs.ansible.com/ansible/apt_module.html)，並利用它直接進行 cURL 的安裝。接著，打開我們的 `playbook.yml`，並修改為以下內容：

```shell
---
- hosts: server
  roles:
    - { role: curl, become: yes }
```

我們刪除了之前用來測試的 ping 劇碼 (play)，並在這個 playbook 中告訴 Ansible 我們想要執行 `curl` 這個我們剛定義好的 role。其中要特別注意的是，[become](http://docs.ansible.com/ansible/become.html) 代表我們要升高當前使用者權限 （等效於 Unix / Linux 中的 `sudo` 指令）來運行當前工作。


最後，重新運行我們的 playbook，並得到以下結果：

```shell
PLAY [server] *****************************************************************

TASK [setup] *******************************************************************
ok: [server]

PLAY RECAP *********************************************************************
server                    : ok=1    changed=0    unreachable=0    failed=0
```

雖然 playbook 被正確運行了，可是很顯然地，我們用來安裝 cURL 的 role 並沒有被其呼叫成功。
