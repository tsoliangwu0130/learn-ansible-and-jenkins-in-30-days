# 使用 SSH 傳送建置產物

相信至此，讀者對於在本機上操作 Jenkins 已經有一定程度的掌握了。如果今天我們希望利用 Jenkins 幫我們把專案建置後的產物部署到我們的另外一台主機的指定路徑下，這又該怎麼做呢？

#### 使用 Vagrant 模擬目標伺服器

因為在這個章節中我們會需要另外一台主機來擔任被部署伺服器的角色，我們可以利用 Vagrant 先創建另外一台伺服器。打開 `Vagrantfile` 並作以下更動：

```ruby
Vagrant.configure("2") do |config|
	# Server: ironman
	config.vm.define :ironman do |ironman_config|
		ironman_config.vm.box = "hashicorp/precise64"
		ironman_config.vm.network "forwarded_port", guest: 8080, host: 9080

		# run Ansible playbook from Vagrant host
		ironman_config.vm.provision "ansible" do |ansible|
			ansible.playbook = "playbook.yml"
		end
	  end

	# Server: ironman_target
	config.vm.define :ironman_target do |ironman_target_config|
		ironman_target_config.vm.box = "hashicorp/precise64"
	  end	
end
```

我們稍微改寫了一下原本的 `Vagrantfile` 。現在我們定義了第二台虛擬主機，並將其命名為 `ironman_target`。由於這台主機並不需要安裝 Jenkins，所以我們只需要單純地告訴 Vagrant 它的作業系統即可。設置完畢後運行 `vagrant up` 啟動主機，並查看主機狀況如下：

```shell
$ vagrant ssh-config

Host ironman
  HostName 127.0.0.1
  User vagrant
  Port 2222
  UserKnownHostsFile /dev/null
  StrictHostKeyChecking no
  PasswordAuthentication no
  IdentityFile /Users/tsoliang/Desktop/workspace/.vagrant/machines/ironman/virtualbox/private_key
  IdentitiesOnly yes
  LogLevel FATAL

Host ironman_target
  HostName 127.0.0.1
  User vagrant
  Port 2200
  UserKnownHostsFile /dev/null
  StrictHostKeyChecking no
  PasswordAuthentication no
  IdentityFile /Users/tsoliang/Desktop/workspace/.vagrant/machines/ironman_target/virtualbox/private_key
  IdentitiesOnly yes
  LogLevel FATAL
```

做好基本的伺服器配置後，我們就可以接著利用 Jenkins 操作接下來的步驟了。

#### 安裝 Publish Over SSH 插件

我們這次要介紹的是如何使用 Jenkins 的插件 - [Publish Over SSH](https://wiki.jenkins-ci.org/display/JENKINS/Publish+Over+SSH+Plugin) 來實現透過 SSH 傳送建置產物的需求，因此，我們可以透過 Jenkins 的網路介面先進行插件安裝。

回到 Jenkins 主控制介面，點選 `Manage Jenkins` 的 `Manage Plugins`，並於 `Available` 的標籤頁下搜尋 `Publish Over SSH` 插件。勾選插件後，點擊安裝如下：

![publish_over_ssh_01](https://github.com/tsoliangwu0130/learn-ansible-and-jenkins-in-30-days/blob/master/images/publish_over_ssh_01.png?raw=true)

進入安裝過程後，勾選 `Restart Jenkins when installation is complete and no jobs are running`，來重啟 Jenkins：

![publish_over_ssh_02](https://github.com/tsoliangwu0130/learn-ansible-and-jenkins-in-30-days/blob/master/images/publish_over_ssh_02.png?raw=true)

插件安裝完成並重新啟動後，我們可以在 `Installed` 的標籤下看到插件已經安裝完成：

![publish_over_ssh_03](https://github.com/tsoliangwu0130/learn-ansible-and-jenkins-in-30-days/blob/master/images/publish_over_ssh_03.png?raw=true)

現在插件也已安裝就緒，接著，我們可以開始準備進行伺服器配對了。