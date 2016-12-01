# 使用 Vagrant 模擬測試環境

由於在 DevOps 的實務操作上我們常常會同時操作多台機器，所以在正式進入接下來的主題前，我想要先花一點篇幅介紹如何使用 [Vagrant](https://www.vagrantup.com/) 來模擬我們的測試環境。

#### Vagrant 是什麼？

在部署軟體服務的階段，開發人員常常會利用虛擬主機來模擬及配置開發環境。Vagrant 就是基於這樣的需求產生的一個服務。與傳統使用 [VirtualBox](https://www.virtualbox.org/) 透過圖形使用者介面 (Graphical User Interface, GUI) 操作虛擬主機有一點不同的是，Vagrant 主要是使用命令列介面 (command-line interface, CLI) 來與虛擬主機做溝通。因此，我們在接下來的章節中將會運用大量的命令列來進行操作。

#### Vagrant 及相關軟體安裝

1. [Vagrant](https://www.vagrantup.com/downloads.html)
2. [[Optional] Vagrant Manager for macOS](https://github.com/lanayotech/vagrant-manager/releases)
3. [[Optional] Vagrant Manager for Windows](https://github.com/lanayotech/vagrant-manager-windows/releases)
4. [[Optional] VirtualBox](https://www.virtualbox.org/wiki/Downloads)

以上除了 [Vagrant](https://www.vagrantup.com/downloads.html) 本身必須要安裝外，其他軟體讀者可以根據情況自行斟酌是否要安裝。另外，在未來的章節內，我將主要以 macOS 作為我們的主要作業系統。

在 Vagrant 安裝完成後，在終端機 (terminal) 內輸入以下指令：

```
$ vagrant version
```

若出現類似以下結果，表示 Vagrant 已經安裝成功囉！

```
Installed Version: 1.9.0
Latest Version: 1.9.0

You're running an up-to-date version of Vagrant!
```

#### 開始使用 Vagrant

使用 Vagrant 的另一大優點就是大家都可以把自己習慣的開發環境打包給其他人使用。而這些打包後的作業系統在 Vagrant 的世界內就稱為 [Vagrant boxes](https://www.vagrantup.com/docs/boxes.html)。讀者可以依據自己的需求在 [public Vagrant box catalog](https://atlas.hashicorp.com/boxes/search) 上搜尋適合的 box 來使用。值得一提的是官方特別推薦使用 [Bento boxes](https://atlas.hashicorp.com/bento) 這個列表內中的 boxes，除了因為開發者大多皆為知名軟體工作者，其品質相對穩定外，各專案也都已於 GitHub 上開源。

作為示範，我將使用官方釋出的輕量級 box -- hashicorp/precise64 (Ubuntu 12.04 (32 and 64-bit))來作為接下來的標準 box。

首先，我們先在桌面上建立一個 workspace 給這次的專案使用。

```
$ mkdir ~/Desktop/workspace
```

接下來，將資料夾目錄切換至 workspace 中並初始化我們的 Vagrant box

```
$ cd ~/Desktop/workspace
$ vagrant init hashicorp/precise64

A `Vagrantfile` has been placed in this directory. You are now
ready to `vagrant up` your first virtual environment! Please read
the comments in the Vagrantfile as well as documentation on
`vagrantup.com` for more information on using Vagrant.
```

你可能已經注意到 Vagrant 在 workspace 中初始化了一個 `Vagrantfile`，不過我們暫時先不用管它。接著執行指令來啟動我們的第一台虛擬機：

```
$ vagrant up

Bringing machine 'default' up with 'virtualbox' provider...
==> default: Importing base box 'hashicorp/precise64'...
==> default: Matching MAC address for NAT networking...
==> default: Checking if box 'hashicorp/precise64' is up to date...
==> default: Setting the name of the VM: workspace_default_1480634214083_66742
==> default: Fixed port collision for 22 => 2222. Now on port 2200.
==> default: Clearing any previously set network interfaces...
==> default: Preparing network interfaces based on configuration...
    default: Adapter 1: nat
==> default: Forwarding ports...
    default: 22 (guest) => 2200 (host) (adapter 1)
==> default: Booting VM...
==> default: Waiting for machine to boot. This may take a few minutes...
    default: SSH address: 127.0.0.1:2200
    default: SSH username: vagrant
    default: SSH auth method: private key
    default:
    default: Vagrant insecure key detected. Vagrant will automatically replace
    default: this with a newly generated keypair for better security.
    default:
    default: Inserting generated public key within guest...
    default: Removing insecure key from the guest if it's present...
    default: Key inserted! Disconnecting and reconnecting using new SSH key...
==> default: Machine booted and ready!
==> default: Checking for guest additions in VM...
    default: The guest additions on this VM do not match the installed version of
    default: VirtualBox! In most cases this is fine, but in rare cases it can
    default: prevent things such as shared folders from working properly. If you see
    default: shared folder errors, please make sure the guest additions within the
    default: virtual machine match the version of VirtualBox you have installed on
    default: your host and reload your VM.
    default:
    default: Guest Additions Version: 4.2.0
    default: VirtualBox Version: 5.0
==> default: Mounting shared folders...
    default: /vagrant => /Users/tsoliang/Desktop/workspace
```

最後，透過 [SSH](https://zh.wikipedia.org/wiki/Secure_Shell) 登入虛擬機：

```
$ vagrant ssh

Welcome to Ubuntu 12.04 LTS (GNU/Linux 3.2.0-23-generic x86_64)

 * Documentation:  https://help.ubuntu.com/
New release '14.04.5 LTS' available.
Run 'do-release-upgrade' to upgrade to it.

Welcome to your Vagrant-built virtual machine.
Last login: Fri Sep 14 06:23:18 2012 from 10.0.2.2
vagrant@precise64:~$
```

看到以上訊息就表示你已經成功用 `vagrant` 這個使用者的身份登入你的第一台虛擬機了！