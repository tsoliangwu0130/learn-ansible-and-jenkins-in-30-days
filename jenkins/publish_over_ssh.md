# 使用 SSH 傳送建置產物

相信至此，讀者對於在本機上操作 Jenkins 已經有一定程度的掌握了。如果今天我們希望利用 Jenkins 幫我們把專案建置後的產物部署到我們的另外一台主機的指定路徑下，這又該怎麼做呢？

#### 使用 Vagrant 模擬目標伺服器

因為在這個章節中我們會需要另外一台主機來擔任被部署伺服器的角色，我們可以利用 Vagrant 先創建另外一台伺服器。打開 `Vagrantfile` 並作以下更動：

```ruby
Vagrant.configure("2") do |config|
	# Server: ironman
	config.vm.define :ironman do |ironman_config|
		ironman_config.vm.box = "hashicorp/precise64"
		ironman_config.vm.network "forwarded_port", guest: 8080, host: 8080

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

#### 進行主機 SSH 配對

因為現在 `Vagrantfile` 裡面定義了兩台主機，所以我們在登入主機時必須告訴 Vagrant 我們要登入哪一台主機。首先，透過以下指令登入我們的 Jenkins 安裝主機，並以 `jenkins` 使用者的身份取得 SSH 公鑰：

```shell
$ vagrant ssh ironman
$ sudo -u jenkins -i
$ cat .ssh/id_rsa.pub
```

接著，開啟一個新的終端機並登入目標部署伺服器，然後使用 [vi](https://zh.wikipedia.org/zh-tw/Vi) 編輯器將剛剛從 Jenkins 安裝主機上取得的公鑰加入授權密鑰清單 (authorized keys) 中：

```shell
$ vagrant ssh ironman_target
$ vi .ssh/authorized_keys
```

#### 在 Jenkins 上新增目標伺服器

在配對完成後，我們現在要在 Jenkins 上新增一個 SSH 伺服器。回到 Jenkins 主控制介面，點選 `Manage Jenkins` 下的 `Configure System`，因為我們在前面已經把 `Publish over SSH` 這個插件安裝好了，所以在這個頁面的最下方現在多出了 `Publish over SSH` 這個設定欄位：

![publish_over_ssh_04](https://github.com/tsoliangwu0130/learn-ansible-and-jenkins-in-30-days/blob/master/images/publish_over_ssh_04.png?raw=true)

點選 `SSH Servers` 旁的 `Add` 來新增目標伺服器如下：

![publish_over_ssh_05](https://github.com/tsoliangwu0130/learn-ansible-and-jenkins-in-30-days/blob/master/images/publish_over_ssh_05.png?raw=true)

其中有幾個重點欄位要特別注意：

1. **Hostname**: 目標伺服器的 IP 位置。雖然從 `vagrant ssh-config` 的指令中我們看到 HostName 是 `127.0.0.1`，但 Vagrant 會自動幫所有虛擬機的 localhost IP 轉介成 `10.0.2.2`，以便我們若要從本機端操作虛擬機的時候不至於與本機衝突。
2. **Username**: 伺服器登入者身份，預設為 `vagrant`。
3. **Remote Directory**: 透過 SSH 傳輸檔案的傳輸目的地。
4. **Passphrase / Password**: 登入目標伺服器的密碼，預設也是 `vagrant`。
5. **Port**: 由於所有 Vagrant 產生的虛擬機預設 IP 都是 `10.0.2.2`，因此我們必須在這邊強調不同伺服器對應的 port 口。從上面的 `vagrant ssh-config` 中我們可以看到 Jenkins 安裝主機被分配的 port 是 `2222`，而目標主機的 port 則是 `2200`。

設定完成後，點選 `Test Configuration` 測試連線：

![publish_over_ssh_06](https://github.com/tsoliangwu0130/learn-ansible-and-jenkins-in-30-days/blob/master/images/publish_over_ssh_06.png?raw=true)

若如上圖一般出現 `Success` 的訊息，就表示我們現在已經正確將 SSH Server 添加至 Jenkins 伺服器管理列表中了，接著儲存設定後離開。

#### 在專案中設定建置後部署動作

在一切就緒後，最後，我們要在專案內設定相關作業。進入 `ansible_lint` 的專案配置，在 `Post-build Actions` 的欄位下，點選 `Send build artifacts over SSH` 的選項如下：

![publish_over_ssh_07](https://github.com/tsoliangwu0130/learn-ansible-and-jenkins-in-30-days/blob/master/images/publish_over_ssh_07.png?raw=true)

接著設置 `SSH Server` 的設定欄位：

![publish_over_ssh_08](https://github.com/tsoliangwu0130/learn-ansible-and-jenkins-in-30-days/blob/master/images/publish_over_ssh_08.png?raw=true)

我們可以看到 Jenkins 已經自動將我們剛剛設定的 `ironman_target` 伺服器代入部署主機的 `Name` 欄位中了，若我們今天有多個部署主機，可以在下拉式選單中進行更改。另外，在 `Transfers` 的欄位裡，我們還需要設定部署的動作。在這邊簡單的設定 `*.txt` 將所有 txt 檔都都當作目標傳送至部署伺服器。設定完成後，儲存變更並重建專案，接著到 `Console Output` 中觀看建置結果：

![publish_over_ssh_09](https://github.com/tsoliangwu0130/learn-ansible-and-jenkins-in-30-days/blob/master/images/publish_over_ssh_09.png?raw=true)

從建置過程裡我們可以看到 Jenkins 在建置完成後透過了 SSH 傳送了一個檔案至 `ironman_target` 部署伺服器上。接著我們可以登入至我們的 `ironman_target`  部署伺服器上確認檔案是否有被正確傳送：

```shell
$ vagrant ssh ironman_target
$ ls -al | grep *.txt

-rw-rw-r-- 1 vagrant vagrant    0 Dec 27 02:27 Dev-27-12-16-result.txt
```

沒有錯！建置產物已經正確被傳送到我們的部署伺服器上了！要特別注意的是，雖然檔案會被傳送到目標伺服器上，但在原本的工作專案目錄 (workspace) 下建置產物還是會有一份備份存在，若不希望建置產物留在 Jenkins 伺服器上，記得要另外在設定內進行調整。
