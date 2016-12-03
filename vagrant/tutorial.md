# Vagrant 基本設定

#### 如何管理 Vagrant boxes？
 
如果在網路上找到了一個適合的 box (e.g. [bento/debian-8.6](https://atlas.hashicorp.com/bento/boxes/debian-8.6)) 想要下載並新增 (add) 到本機，可以使用 `vagrant box add <box_name> <box_url>` 並選擇自己需要的版本：

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
hashicorp/precise64               (virtualbox, 1.1.0)
```

同理，如果要移除 (remove) 不再需要的 box，可以使用 `vagrant box remove <box_name>`：

```shell
$ vagrant box remove bento/debian-8.6

Removing box 'bento/debian-8.6' (v2.3.0) with provider 'virtualbox'...
```

確定一下 box 已經被成功移除：

```shell
$ vagrant box list

hashicorp/precise64               (virtualbox, 1.1.0)
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
  # boxes at https://atlas.hashicorp.com/search.
  config.vm.box = "hashicorp/precise64"

  # Disable automatic box update checking. If you disable this, then
  # boxes will only be checked for updates when the user runs
  # `vagrant box outdated`. This is not recommended.
  # config.vm.box_check_update = false

  # Create a forwarded port mapping which allows access to a specific port
  # within the machine from a port on the host machine. In the example below,
  # accessing "localhost:8080" will access port 80 on the guest machine.
  # config.vm.network "forwarded_port", guest: 80, host: 8080

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

  # Define a Vagrant Push strategy for pushing to Atlas. Other push strategies
  # such as FTP and Heroku are also available. See the documentation at
  # https://docs.vagrantup.com/v2/push/atlas.html for more information.
  # config.push.define "atlas" do |push|
  #   push.app = "YOUR_ATLAS_USERNAME/YOUR_APPLICATION_NAME"
  # end

  # Enable provisioning with a shell script. Additional provisioners such as
  # Puppet, Chef, Ansible, Salt, and Docker are also available. Please see the
  # documentation for more information about their specific syntax and use.
  # config.vm.provision "shell", inline: <<-SHELL
  #   apt-get update
  #   apt-get install -y apache2
  # SHELL
end
```

簡單來說，`Vagrantfile` 就是每一台的虛擬機的規格表。每次我們啟動或部署 Vagrant 虛擬機時就是依據這一份設置文件 (configuration file) 來進行定義的。雖然乍看之下這個檔案長得有些驚悚，但仔細研究後會發現目前真正有用到的配置其實只有短短幾行而已：

```shell
Vagrant.configure("2") do |config|
  config.vm.box = "hashicorp/precise64"
end
```

而這段程式碼就是告訴 Vagrant 我們想要用 `hashicorp/precise64` 當作我們這台虛擬機的 box。我們會在未來的章節隨著需要逐步將需要配置的參數陸續加上，但我還是強烈建議讀者可以稍微閱讀一下 `Vagrantfile` 裡的註解配置，這對操作 Vagrant 虛擬機有相當大的幫助。

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

我們可以清楚發現雖然虛擬機已經成功運行，但主機名稱顯示為 `default`。這種模糊的名稱在管理主機數量多起來後常常會造成開發者的困擾。為了避免混淆，我們可以先用 `vagrant halt` 將目前的虛擬機暫停或是使用 `vagrant destroy` 直接摧毀 (destroy)。接下來，在 `Vagrantfile` 中加入下列設置來替我們的虛擬機命名：

```ruby
config.vm.define "ironman_ansible_jenkins"
```

重新啟動 (up) 一個新的主機：

```shell
$ vagrant up

Bringing machine 'ironman_ansible_jenkins' up with 'virtualbox' provider...
==> ironman_ansible_jenkins: Importing base box 'hashicorp/precise64'...
==> ironman_ansible_jenkins: Matching MAC address for NAT networking...
==> ironman_ansible_jenkins: Checking if box 'hashicorp/precise64' is up to date...
==> ironman_ansible_jenkins: Setting the name of the VM: workspace_ironman_ansible_jenkins_1480738218546_20931
==> ironman_ansible_jenkins: Clearing any previously set network interfaces...
==> ironman_ansible_jenkins: Preparing network interfaces based on configuration...
    ironman_ansible_jenkins: Adapter 1: nat
==> ironman_ansible_jenkins: Forwarding ports...
    ironman_ansible_jenkins: 22 (guest) => 2222 (host) (adapter 1)
==> ironman_ansible_jenkins: Booting VM...
==> ironman_ansible_jenkins: Waiting for machine to boot. This may take a few minutes...
    ironman_ansible_jenkins: SSH address: 127.0.0.1:2222
    ironman_ansible_jenkins: SSH username: vagrant
    ironman_ansible_jenkins: SSH auth method: private key
    ironman_ansible_jenkins:
    ironman_ansible_jenkins: Vagrant insecure key detected. Vagrant will automatically replace
    ironman_ansible_jenkins: this with a newly generated keypair for better security.
    ironman_ansible_jenkins:
    ironman_ansible_jenkins: Inserting generated public key within guest...
    ironman_ansible_jenkins: Removing insecure key from the guest if it's present...
    ironman_ansible_jenkins: Key inserted! Disconnecting and reconnecting using new SSH key...
==> ironman_ansible_jenkins: Machine booted and ready!
==> ironman_ansible_jenkins: Checking for guest additions in VM...
    ironman_ansible_jenkins: The guest additions on this VM do not match the installed version of
    ironman_ansible_jenkins: VirtualBox! In most cases this is fine, but in rare cases it can
    ironman_ansible_jenkins: prevent things such as shared folders from working properly. If you see
    ironman_ansible_jenkins: shared folder errors, please make sure the guest additions within the
    ironman_ansible_jenkins: virtual machine match the version of VirtualBox you have installed on
    ironman_ansible_jenkins: your host and reload your VM.
    ironman_ansible_jenkins:
    ironman_ansible_jenkins: Guest Additions Version: 4.2.0
    ironman_ansible_jenkins: VirtualBox Version: 5.0
==> ironman_ansible_jenkins: Mounting shared folders...
    ironman_ansible_jenkins: /vagrant => /Users/tsoliang/Desktop/workspace
```

再檢查一次虛擬機運行狀況：

```shell
$ vagrant status

Current machine states:

ironman_ansible_jenkins   running (virtualbox)

The VM is running. To stop this VM, you can run `vagrant halt` to
shut it down forcefully, or you can run `vagrant suspend` to simply
suspend the virtual machine. In either case, to restart it again,
simply run `vagrant up`.
```

正如我們預期的，虛擬機有自己的名字拉！
