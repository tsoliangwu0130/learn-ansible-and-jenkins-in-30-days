# 撰寫屬於你的 Ansible Role

在了解 playbook 的基本架構與運行方式後，我會在接下來的章節內介紹如何使用 Ansible 搭建起 [Jenkins](https://jenkins.io/) 的運行環境。透過實際的例子，相信讀者會對操作 Ansible 將會更加熟練與靈活。然而，我並不會在這個章節內過度著墨 Jenkins 的部分。關於 Jenkins 持續整合的使用，會在未來的章節內做更詳細的介紹。

#### 什麼是 Ansible Role？

我們在前面的章節內學習了如何撰寫 Ansible playbook，我們可以將我們希望 Ansible 做的事項以 task 的方式在 playbook 中表列下來。然而，如果 Ansible 只能做到這樣的程度，充其量我們只能說這是一個比較方便閱讀的 Shell script 罷了。若是今天我們清單中的任務有上百個，這樣我們的 playbook 也可能會變得非常冗長，就算語法再如何易讀，整體而言 playbook 還是會變得十分難以理解。另外，很多時候其實我們會希望有部分的部署內容是可以被其他不同的 playbook 重新使用的。舉例來說，很多服務都可以直接使用 pip 這個套件管理來進行安裝，我們並不會希望在每一個不同的 playbook 中都要重新定義一次 pip 的安裝方法。因此，為了解決上述的問題，Ansible 提供了我們在撰寫自動化腳本時一個[角色 (role)](http://docs.ansible.com/ansible/playbooks_roles.html) 的概念。我們可以透過撰寫屬於自己的 role 來讓所有 playbook 重複使用，藉此提升透過 Ansible 自動化的靈活度。

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

```
Setting up jenkins (2.19.4) ...
 * Starting Jenkins Continuous Integration Server jenkins                                                                                                                  [ OK ]
Processing triggers for libc-bin ...
ldconfig deferred processing now taking place
```

根據官方的說明，當 Jenkins 安裝好以後，我們可以透過 `port 8080` 對 Jenkins 進行存取操作，因此，我們可以嘗試打開瀏覽器，並在網址列輸入 `http://localhost:8080/` 看看 Jenkins 是否已經正確運行在主機上，結果如下：

![localhost_8080_unable_to_connect](images/localhost_8080_unable_to_connect)

顯然地，我們還不可以透過 `http://localhost:8080/` 存取 Jenkins。這是因為雖然 Jenkins 已經被安裝完成，但是它目前是運行在我們的 Vagrant 虛擬主機內。在還沒有調整 `Vagrantfile` 的設定前，我們是無法在控制主機上相對應 的 port 上看到 Jenkins 的介面的。

雖然目前我們還不能從主控端使用瀏覽器存取 Jenkins，但我們可以使用 [cURL](https://en.wikipedia.org/wiki/CURL) 這個指令從遙控主機內部對 Jenkins 傳送 [HTTP 請求資訊 (HTTP request message)](https://zh.wikipedia.org/wiki/%E8%B6%85%E6%96%87%E6%9C%AC%E4%BC%A0%E8%BE%93%E5%8D%8F%E8%AE%AE)：

```shell
$ curl http://localhost:8080

The program 'curl' is currently not installed.  You can install it by typing:
sudo apt-get install curl
```

