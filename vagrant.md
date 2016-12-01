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

