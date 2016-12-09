# 撰寫第一個 Ansible Role

在了解 playbook 的基本架構與運行方式後，我會在接下來的章節內介紹如何使用 Ansible 搭建起 [Jenkins](https://jenkins.io/) 的運行環境。透過實際的例子，相信讀者會對操作 Ansible 將會更加熟練與靈活。然而，我並不會在這個章節內過度著墨 Jenkins 的部分。關於 Jenkins 持續整合的使用，會在未來的章節內做更詳細的介紹。

#### 嘗試手動安裝 Jenkins

首先，讓我們先試著不要透過 Ansible，手動安裝 Jenkins 在我們的主機上。因為在這系列文章中，我們用的是 Ubuntu 的 box 來運行虛擬機，因此根據 Jenkins [官方的 Ubuntu 安裝教學](https://wiki.jenkins-ci.org/display/JENKINS/Installing+Jenkins+on+Ubuntu)，我們可以直接透過系統內建的 apt 套件管理進行 Jenkins 安裝：

先透過 `vagrant ssh` 登入我們的虛擬主機：

```shell
$ vagrant ssh

Welcome to Ubuntu 12.04 LTS (GNU/Linux 3.2.0-23-generic x86_64)

 * Documentation:  https://help.ubuntu.com/
New release '14.04.5 LTS' available.
Run 'do-release-upgrade' to upgrade to it.

Welcome to your Vagrant-built virtual machine.
Last login: Fri Dec  9 03:10:01 2016 from 10.0.2.2
```

接著跟著官方的安裝教學進行安裝：

```shell
$ wget -q -O - https://pkg.jenkins.io/debian/jenkins-ci.org.key | sudo apt-key add -
$ sudo sh -c 'echo deb http://pkg.jenkins.io/debian-stable binary/ > /etc/apt/sources.list.d/jenkins.list'
$ sudo apt-get update
$ sudo apt-get install jenkins
```

當在終端機輸出內容的最後看到以下訊息，就代表現在 Jenkins 已經成功安裝在我們的遙控節點上了：

```shell
Setting up jenkins (2.19.4) ...
 * Starting Jenkins Continuous Integration Server jenkins                                                                                                                  [ OK ]
Processing triggers for libc-bin ...
ldconfig deferred processing now taking place
```

根據官方的說明，當 Jenkins 安裝好以後，我們可以透過 `port 8080` 對 Jenkins 進行存取操作，因此，我們可以嘗試打開瀏覽器，並在網址列輸入 `http://localhost:8080/` 看看 Jenkins 是否已經正確運行在主機上，結果如下：

![localhost_8080_unable_to_connect](https://github.com/tsoliangwu0130/learn-ansible-and-jenkins-in-30-days/raw/master/images/localhost_8080_unable_to_connect.png)

顯然地，我們還不可以透過 `http://localhost:8080/` 存取 Jenkins。這是因為雖然 Jenkins 已經被安裝完成，但是它目前是運行在我們的 Vagrant 虛擬主機內。在還沒有調整 `Vagrantfile` 的設定前，我們是無法在控制主機上相對應 的 port 上看到 Jenkins 的介面的。

雖然目前我們還不能從主控端使用瀏覽器存取 Jenkins，但我們可以使用 [cURL](https://en.wikipedia.org/wiki/CURL) 這個指令從遙控主機內部對 Jenkins 傳送 [HTTP 請求資訊 (HTTP request message)](https://zh.wikipedia.org/wiki/%E8%B6%85%E6%96%87%E6%9C%AC%E4%BC%A0%E8%BE%93%E5%8D%8F%E8%AE%AE)：

```shell
$ curl http://localhost:8080

The program 'curl' is currently not installed.  You can install it by typing:
sudo apt-get install curl
```

雖然 cURL 這個指令在 Unix / Linux 系統的主機上常常被開發人員大量使用，可是很不幸地，並不是所有的 box 或作業系統都有預先安裝好這個工具。為了能夠順利將 cURL 安裝在遙控節點上並將所有安裝的過程自動化，我們可以試著利用 Ansible 完成所有安裝程序。

#### 什麼是 Ansible Role？

我們在前面的章節內學習了如何撰寫 Ansible playbook，並將我們的工作清單以 task 的方式在 playbook 中表列下來。然而，如果 Ansible 只能做到這樣的程度，充其量我們只能說這是一個比較方便閱讀的 Shell script 罷了。若是今天我們清單中的任務有上百個，這樣我們的 playbook 也可能會變得非常冗長，就算語法再如何易讀，整體而言 playbook 還是會變得十分難以理解。另外，很多時候其實我們會希望有部分的部署內容是可以被其他不同的 playbook 重新使用。舉例來說，很多服務都可以直接使用 pip 這個套件管理來進行安裝，我們並不會希望在每一個不同的 playbook 中都要重新定義一次 pip 的安裝方法。因此，為了解決上述的問題，Ansible 提供了我們在撰寫自動化腳本時一個[角色 (role)](http://docs.ansible.com/ansible/playbooks_roles.html) 的概念。我們可以透過撰寫屬於自己的 role 來讓所有 playbook 重複使用，藉此提升透過 Ansible 自動化的靈活度。

#### 我的第一個 role

考量到由於安裝 cURL 的這個工作很可能會在未來頻繁地被不同的 playbook 重複使用，因此，我會選擇把安裝 cURL 的方法寫成一個可以重複被利用的 role ，而非 playbook 中的一個 task。

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

在這個結構下， `curl` 就是我們的第一個 role 的名稱，而這個 role 的工作流程就會被我們定義在下面的 `tasks/main.yml` 之中。接著打開 `curl/tasks/main.yml` 並在其中寫入以下內容：

```yml
---
  - name: install curl
    apt:
      name: curl
    when: ansible_distribution == 'Ubuntu' or ansible_distribution == 'Debian' 
```

我們在這個 role 的內容中呼叫了 Ansible 內建模組 [apt](http://docs.ansible.com/ansible/apt_module.html)，並加上了一個系統[判斷條件](http://docs.ansible.com/ansible/playbooks_conditionals.html)。由於 Ubuntu 跟 Debian 這兩種主要的 Linux 作業系統預設都是搭載 apt 做為套件管理，所以當我們今天只要運行這個 role，並判斷遙控主機是這兩個作業系統之一的時候我們就會運行上面的任務。

接著，打開我們的 `playbook.yml`，並修改為以下內容：

```shell
---
- hosts: ironman
  roles:
    - { role: curl, become: true }
```

我們刪除了之前用來測試的 ping 劇碼 (play)，並在這個 playbook 中告訴 Ansible 我們想要執行 `curl` 這個我們剛定義好的 role。其中要特別注意的是，[become](http://docs.ansible.com/ansible/become.html) 代表我們要升高當前使用者權限 （等效於 Unix / Linux 中的 `sudo` 指令）來運行當前工作。


最後，重新運行我們的 playbook，並得到以下結果：

```shell
PLAY [ironman] *****************************************************************

TASK [setup] *******************************************************************
ok: [ironman]

PLAY RECAP *********************************************************************
ironman                    : ok=1    changed=0    unreachable=0    failed=0
```

雖然 playbook 被正確運行了，可是很顯然地，我們用來安裝 cURL 的 role 並沒有被其呼叫成功。

#### 什麼是 ansible.cfg？

從上面的結果中，我們可以發現雖然 playbook 已經成功被執行，但 Ansible 並沒有辦法找到我們剛剛新建 role 的位置。因此，我們必須透過設定 [Ansible 的配置檔案](http://docs.ansible.com/ansible/intro_configuration.html) - `ansible.cfg` 來設置 [role 路徑 (path)](http://docs.ansible.com/ansible/intro_configuration.html#roles-path)。

新增一個檔案 `ansible.cfg` 並加入以下內容：

```
roles_path = ./roles
```

雖然只有短短一行，但 Ansible 就會依此路徑去找到我們 role 的存放位置。接著重新執行我們的 playbook：

```shell
PLAY [ironman] *****************************************************************

TASK [setup] *******************************************************************
ok: [ironman]

TASK [curl : install curl] *****************************************************
changed: [ironman]

PLAY RECAP *********************************************************************
ironman                    : ok=2    changed=1    unreachable=0    failed=0
```

看起來好像成功了！現在讓我們登入進去遙控節點看看是否 cURL 已經可以運作了：

```shell
$ curl -I http://localhost:8080

HTTP/1.1 403 Forbidden
Date: Fri, 09 Dec 2016 09:03:44 GMT
X-Content-Type-Options: nosniff
Set-Cookie: JSESSIONID.4689751a=1o67mouadl3zhq80h5b2xxxu6;Path=/;HttpOnly
Expires: Thu, 01 Jan 1970 00:00:00 GMT
Content-Type: text/html;charset=UTF-8
X-Hudson: 1.395
X-Jenkins: 2.19.4
X-Jenkins-Session: e765255c
X-You-Are-Authenticated-As: anonymous
X-You-Are-In-Group:
X-Required-Permission: hudson.model.Hudson.Administer
Content-Length: 677
Server: Jetty(9.2.z-SNAPSHOT)
```

先姑且不論結果是否是我們要的，至少我們現在確定 cURL 已經被 Ansible 正確安裝到遙控節點上了！

若點進官方配置文件的[說明頁面](http://docs.ansible.com/ansible/intro_configuration.html)，可以發現除了 `roles_path` 之外還有一大堆參數可以依據使用者的需求進行非常有彈性的配置。其中值得注意的是，在這個示範裡面我是將 `ansible.cfg` 這個配置檔案置於我們工作資料夾之下，目前具體資料夾結構如下：

```shell
workspace
├── Vagrantfile
├── ansible.cfg
├── inventory
├── playbook.yml
└── roles
    └── curl
        └── tasks
            └── main.yml
```

但從官方文件中，我們可以發現除了將配置檔案放置於工作目錄底下外，還有其他幾種不同的配置方式：

1. 設置環境變量 (ANSIBLE_CONFIG)
2. 置於當前目錄下的 `ansible.cfg`（這次範例中使用的方法）
3. 置於根目錄下的 `.ansible.cfg`
4. 置於 `/etc/ansible/` 下的 `ansible.cfg`

若我們同時分別有不同的配置文件存在在系統中，Ansible 會依照上述順序逐一查找文件，若找到其中一個，就會依其設定進行配置，並不再繼續查找。
