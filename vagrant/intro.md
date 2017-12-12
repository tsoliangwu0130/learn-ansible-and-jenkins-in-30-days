# Vagrant 介紹

#### Vagrant 是什麼？

在部署軟體服務的階段，開發人員常常會利用虛擬主機來模擬及配置開發環境。Vagrant 就是基於這樣的需求產生的一個服務。與傳統使用 [VirtualBox](https://www.virtualbox.org/) 透過圖形使用者介面 (Graphical User Interface, GUI) 操作虛擬主機有一點不同的是，Vagrant 主要是使用命令列介面 (command-line interface, CLI) 來與虛擬主機做溝通。因此，我們在接下來的章節中將會運用大量的命令列來進行操作。

#### Vagrant 及相關軟體安裝

1. [Vagrant](https://www.vagrantup.com/downloads.html)
2. [[Optional] Vagrant Manager for macOS](https://github.com/lanayotech/vagrant-manager/releases)
3. [[Optional] Vagrant Manager for Windows](https://github.com/lanayotech/vagrant-manager-windows/releases)
4. [[Optional] VirtualBox](https://www.virtualbox.org/wiki/Downloads)

以上除了 Vagrant 本身必須要安裝外，其他軟體讀者可以根據情況自行斟酌是否要進行安裝。另外，在未來的章節內，我將主要以 macOS 作為我們的作業系統來進行操作。

在 Vagrant 安裝完成後，在終端機 (terminal) 內輸入以下指令：

```shell
$ vagrant version
```

若出現類似以下結果，表示 Vagrant 已經安裝成功囉！

```shell
Installed Version: 2.0.1
Latest Version: 2.0.1

You're running an up-to-date version of Vagrant!
```

#### 開始使用 Vagrant

使用 Vagrant 的另一大優點就是大家都可以把自己習慣的開發環境打包給其他人使用。而這些打包後的作業系統在 Vagrant 的世界內就稱為 [Vagrant boxes](https://www.vagrantup.com/docs/boxes.html)。讀者可以依據自己的需求在 [public Vagrant box catalog](https://atlas.hashicorp.com/boxes/search) 上搜尋適合的 box 來使用。值得一提的是官方特別推薦使用 [Bento boxes](https://atlas.hashicorp.com/bento) 這個列表內中的 boxes，除了因為開發者大多皆為知名軟體工作者，其品質相對穩定外，各專案也都已於 GitHub 上開源。作為示範，我將使用 bento 中的 `bento/ubuntu-14.04` 來作為接下來的標準 box。

首先，我們可以先在桌面上建立一個 workspace 給這次的專案使用：

```shell
$ mkdir ~/Desktop/workspace
```

接下來，將資料夾目錄切換至 workspace 中並根據我們選擇的 box 來初始化我們的 Vagrant 虛擬機：

```shell
$ cd ~/Desktop/workspace
$ vagrant init bento/ubuntu-14.04
```

你可能已經注意到此時 Vagrant 在 workspace 中初始化了一個 `Vagrantfile`，不過我們暫時先不用管它。
接著，啟動我們的第一台虛擬機：

```shell
$ vagrant up
```

因為第一次啟動必須要下載整份 box 的檔案，所以可能會花幾分鐘的時間來啟動。在啟動完成後，透過 [SSH](https://zh.wikipedia.org/wiki/Secure_Shell) 來登入該虛擬機：

```shell
$ vagrant ssh
```

登入後，若看到以下訊息就表示你已經成功用 `vagrant` 這個使用者的身份登入你的第一台虛擬機囉！

```
Welcome to Ubuntu 14.04.5 LTS (GNU/Linux 3.13.0-32-generic x86_64)

 * Documentation:  https://help.ubuntu.com/

  System information as of Tue Dec 12 00:36:28 UTC 2017

  System load:  0.15              Processes:           88
  Usage of /:   2.7% of 38.02GB   Users logged in:     0
  Memory usage: 7%                IP address for eth0: 10.0.2.15
  Swap usage:   0%

  Graph this data and manage this system at:
    https://landscape.canonical.com/

vagrant@vagrant:~$
```
