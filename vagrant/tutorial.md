# Vagrant 基本設定

#### 如何管理 Vagrant boxes？

如果在網路上找到了一個適合的 box (e.g. [bento/debian-8.6](https://atlas.hashicorp.com/bento/boxes/debian-8.6)) 想要下載並新增到本機，可以使用 `vagrant box add <box_name> <box_url>` 並選擇自己需要的版本，舉例來說：

```shell
$ vagrant box add bento/debian-8.6 https://atlas.hashicorp.com/bento/boxes/debian-8.6

==> box: Loading metadata for box 'https://atlas.hashicorp.com/bento/boxes/debian-8.6'
This box can work with multiple providers! The providers that it
can work with are listed below. Please review the list and choose
the provider you will be working with.

1) parallels
2) virtualbox
3) vmware_desktop

Enter your choice: 2
==> box: Adding box 'bento/debian-8.6' (v2.3.0) for provider: virtualbox
    box: Downloading: https://atlas.hashicorp.com/bento/boxes/debian-8.6/versions/2.3.0/providers/virtualbox.box
==> box: Successfully added box 'bento/debian-8.6' (v2.3.0) for 'virtualbox'!
```

下載完成後，檢查一下目前所有安裝在本機的 Vagrant boxes：

```shell
$ vagrant box list

bento/debian-8.6                  (virtualbox, 2.3.0)
bento/ubuntu-14.04                (virtualbox, 201708.22.0)
```

同理，如果要移除不再需要的 box，可以使用 `vagrant box remove <box_name>`：

```shell
$ vagrant box remove bento/debian-8.6

Removing box 'bento/debian-8.6' (v2.3.0) with provider 'virtualbox'...
```

透過 `vagrant box list` 確定一下 box 已經被成功移除：

```shell
$ vagrant box list

bento/ubuntu-14.04 (virtualbox, 201708.22.0)
```

#### 透過 Vagrantfile 配置測試環境

還記得在上一個章節內我們在初始化 Vagrant 時系統自動產生的 `Vagrantfile` 嗎？現在把這個檔案打開，你應該會看到以下結果：

```ruby
# -*- mode: ruby -*-
# vi: set ft=ruby :

# All Vagrant configuration is done below. The "2" in Vagrant.configure
# configures the configuration version (we support older styles for
# backwards compatibility). Please don't change it unless you know what
# you're doing.
Vagrant.configure("2") do |config|
  # The most common configuration options are documented and commented below.
  # For a complete reference, please see the online documentation at
  # https://docs.vagrantup.com.

  # Every Vagrant development environment requires a box. You can search for
  # boxes at https://vagrantcloud.com/search.
  config.vm.box = "bento/ubuntu-14.04"

  # Disable automatic box update checking. If you disable this, then
  # boxes will only be checked for updates when the user runs
  # `vagrant box outdated`. This is not recommended.
  # config.vm.box_check_update = false

  # Create a forwarded port mapping which allows access to a specific port
  # within the machine from a port on the host machine. In the example below,
  # accessing "localhost:8080" will access port 80 on the guest machine.
  # NOTE: This will enable public access to the opened port
  # config.vm.network "forwarded_port", guest: 80, host: 8080

  # Create a forwarded port mapping which allows access to a specific port
  # within the machine from a port on the host machine and only allow access
  # via 127.0.0.1 to disable public access
  # config.vm.network "forwarded_port", guest: 80, host: 8080, host_ip: "127.0.0.1"

  # Create a private network, which allows host-only access to the machine
  # using a specific IP.
  # config.vm.network "private_network", ip: "192.168.33.10"

  # Create a public network, which generally matched to bridged network.
  # Bridged networks make the machine appear as another physical device on
  # your network.
  # config.vm.network "public_network"

  # Share an additional folder to the guest VM. The first argument is
  # the path on the host to the actual folder. The second argument is
  # the path on the guest to mount the folder. And the optional third
  # argument is a set of non-required options.
  # config.vm.synced_folder "../data", "/vagrant_data"

  # Provider-specific configuration so you can fine-tune various
  # backing providers for Vagrant. These expose provider-specific options.
  # Example for VirtualBox:
  #
  # config.vm.provider "virtualbox" do |vb|
  #   # Display the VirtualBox GUI when booting the machine
  #   vb.gui = true
  #
  #   # Customize the amount of memory on the VM:
  #   vb.memory = "1024"
  # end
  #
  # View the documentation for the provider you are using for more
  # information on available options.

  # Enable provisioning with a shell script. Additional provisioners such as
  # Puppet, Chef, Ansible, Salt, and Docker are also available. Please see the
  # documentation for more information about their specific syntax and use.
  # config.vm.provision "shell", inline: <<-SHELL
  #   apt-get update
  #   apt-get install -y apache2
  # SHELL
end
```

簡單來說，`Vagrantfile` 就是每一台的虛擬機的規格表。雖然乍看之下這個檔案長得有些驚悚，但仔細研究後會發現目前真正有用到的配置其實只有以下短短幾行而已：

```ruby
Vagrant.configure("2") do |config|
  config.vm.box = "bento/ubuntu-14.04"
end
```

而這段程式碼就是告訴 Vagrant 我們想要用 `bento/ubuntu-14.04` 當作我們這台虛擬機的 box。每次我們啟動或部署 Vagrant 虛擬機時就是依據這一份設置文件來進行配置。其他在註解內的文字是一般我們在開發時常常用到的配置選項，隨著開發需要的不同，我們可以在這個配置文件中客製化我們的開發環境，並分享給團隊其他成員。

#### 如何管理 Vagrant 虛擬機？

首先，我們可以使用 `vagrant status` 來確認當前 Vagrant 主機的運作狀況：

```shell
$ vagrant status

Current machine states:

default                   running (virtualbox)

The VM is running. To stop this VM, you can run `vagrant halt` to
shut it down forcefully, or you can run `vagrant suspend` to simply
suspend the virtual machine. In either case, to restart it again,
simply run `vagrant up`.
```

我們可以清楚發現雖然虛擬機已經成功運行，但主機名稱顯示為 `default`。這種模糊的名稱在管理主機數量多起來後常常會造成開發者的困擾。為了避免混淆，我們可以先用 `vagrant halt` 將目前的虛擬機暫停或是使用 `vagrant destroy` 直接摧毀。接下來，在 `Vagrantfile` 中加入下列設置來替我們的虛擬機命名：

```ruby
config.vm.define "server"
```

接著在重新啟動一個新的主機後檢查其狀況：

```shell
$ vagrant up
$ vagrant status

Current machine states:

server                    running (virtualbox)

The VM is running. To stop this VM, you can run `vagrant halt` to
shut it down forcefully, or you can run `vagrant suspend` to simply
suspend the virtual machine. In either case, to restart it again,
simply run `vagrant up`.
```

將虛擬器命名除了方便我們在開發時清楚了解每個虛擬主機的功用，我們也可以直接透過每個主機的別名直接進行操作。
