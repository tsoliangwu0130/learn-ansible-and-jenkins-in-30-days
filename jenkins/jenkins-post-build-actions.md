# 建置後動作

建置作業完成之後，我們還可以讓使用**建置後動作**來告訴 Jenkins 依據建置結果的不同該採取何種動作。

#### 電子郵件通知

進入專案組態，在**建置後動作**的欄位下選擇**電子郵件通知**：

![](https://github.com/tsoliangwu0130/learn-ansible-and-jenkins-in-30-days/blob/master/images/jenkins-post-build-actions-01.png?raw=true)

這個欄位是 Jenkins 初始預設提供給使用者的 E-mail 通知配置，我們可以在這個欄位設定建置完成後的收件人清單，並在建置失敗、不穩定或是恢復穩定時寄送通知：

![](https://github.com/tsoliangwu0130/learn-ansible-and-jenkins-in-30-days/blob/master/images/jenkins-post-build-actions-02.png?raw=true)

然而，這樣的 E-mail 通知在現實的專案中仍舊過於陽春。舉例來說，我們很多時候會希望只有在建置失敗的時候才要收到通知、或是在建置成功時通知某些特定聯絡人，甚至希望能夠客製化通知內容等等。因此，我們接下來會透過 [Email Extension](https://plugins.jenkins.io/email-ext) 這個插件來擴展我們使用 E-mail 發送通知的使用彈性。由於這個插件也是 Jenkins 預設的建議安裝插件，所以如果一開始有安裝建議插件的讀者就不用另行做安裝了。

回到 Jenkins 首頁，點擊**管理 Jenkins** 下的**設定系統**，我們會看到有一個** 擴充電子郵件通知**的欄位：

![](https://github.com/tsoliangwu0130/learn-ansible-and-jenkins-in-30-days/blob/master/images/jenkins-post-build-actions-03.png?raw=true)

在這裡我們可以輸入全域的 E-mail 寄送範本及相關設定。接著，回到專案組態裡，在**建置後動作**中選擇**可編式電子郵件通知**：

![](https://github.com/tsoliangwu0130/learn-ansible-and-jenkins-in-30-days/blob/master/images/jenkins-post-build-actions-04.png?raw=true)

在這裡除了可以直接利用呼叫環境變數的方式調用剛剛在全域設定裡的收件人清單、信件內容模板等變數之外，也可以根據專案的不同做額外的調整。另外，在 **Advanced Settings** 裡，我們還可以找到一個名為 **Triggers** 的欄位：

![](https://github.com/tsoliangwu0130/learn-ansible-and-jenkins-in-30-days/blob/master/images/jenkins-post-build-actions-05.png?raw=true)

這裡可以選擇各種不同觸發通知的條件。依據建置狀態的不同讓 Jenkins 主動寄送通知給相關的收件人，不但幫我們可以過濾掉一些不相關的建置通知外，也可以在問題發生時有效地讓負責人員能在第一時間就將問題解決。

#### Slack Notification

由於現在使用 [Slack](https://slack.com/) 作為溝通平台的開發團隊越來越多，所以將 Jenkins 的建置結果傳送到 Slack 頻道內也是一個相當方便的通知做法。讀者可以先自行在 Slack 申請一個帳號並建立一個 workspace 作為測試。

申請好帳號及 workspace 後，在瀏覽器上瀏覽 `https://{workspace-url}.slack.com/apps` 並搜尋 **Jenkins CI** 這個應用程式進行安裝：

![](https://github.com/tsoliangwu0130/learn-ansible-and-jenkins-in-30-days/blob/master/images/jenkins-post-build-actions-06.png?raw=true)

安裝好後，選擇想要將 Jenkins 通知傳送到哪一個頻道 (channel) 內：

![](https://github.com/tsoliangwu0130/learn-ansible-and-jenkins-in-30-days/blob/master/images/jenkins-post-build-actions-07.png?raw=true)

這邊我以 **#general** 這個頻道作為示範，如果讀者想要建立一個獨立的頻道來接收訊息也完全沒有問題。完成後點選 **Add Jenkins CI integration**，接著應該就會看到非常詳細的 Jenkins 端設定教學：

![](https://github.com/tsoliangwu0130/learn-ansible-and-jenkins-in-30-days/blob/master/images/jenkins-post-build-actions-08.png?raw=true)

在 **Step 3** 中，我們會看到這裡有一個 **Base URL** 與 **Integration Token**，這是等等要串接 Jenkins 時會用到的資訊。接著點擊最下面的 **Save Settings** 後儲存離開。

接著回到 Jenkins 的**管理外掛程式**頁面，選擇 **Slack Notification Plugin** 點擊安裝：

![](https://github.com/tsoliangwu0130/learn-ansible-and-jenkins-in-30-days/blob/master/images/jenkins-post-build-actions-09.png?raw=true)

> 註：如果是直接使用在 Ansible 章節的範例來安裝 Jenkins，這個插件已經在 [plugins.txt](https://github.com/tsoliangwu0130/my-ansible/blob/master/roles/docker-jenkins/files/plugins.txt#L53) 中被我預先搭載了。讀者可以根據自己的需求修改這份安裝列表，或是利用 Ansible 的 [jenkins_plugin](http://docs.ansible.com/ansible/latest/jenkins_plugin_module.html) 模組來管理 Jenkins 插件。

安裝好之後，回到**管理 Jenkins** 下的**設定系統**，找到 **Global Slack Notifier Settings**，並依序將剛剛在 Slack 安裝應用程式中的 **Base URL** 與 **Integration Token** 分別填入對應位置：

![](https://github.com/tsoliangwu0130/learn-ansible-and-jenkins-in-30-days/blob/master/images/jenkins-post-build-actions-10.png?raw=true)

這裡也可以設定預設的通知頻道，設定完後可以點選 **Test Connection** 來測試連線。如果設定一切都沒意外，應該就會在 Slack 裡看到 Jenkins 的測試通知如下：

![](https://github.com/tsoliangwu0130/learn-ansible-and-jenkins-in-30-days/blob/master/images/jenkins-post-build-actions-11.png?raw=true)

設定完成後點選**儲存**離開。最後，回到專案**組態**設定，在**建置後動作**的欄位選擇 **Slack Notification**：

![](https://github.com/tsoliangwu0130/learn-ansible-and-jenkins-in-30-days/blob/master/images/jenkins-post-build-actions-12.png?raw=true)

在這裡我們可以選擇要建置作業在何種情況下發送通知，同時如果有不同於全域的 Slack 設定，可以在**進階**中覆寫掉。依據需求選擇好建置情況後，點選**儲存**離開組態設定，並執行建置。建置完成後，我們應該會在 Slack 頻道中看到這次的建置結果類似下圖的效果：

![](https://github.com/tsoliangwu0130/learn-ansible-and-jenkins-in-30-days/blob/master/images/jenkins-post-build-actions-13.png?raw=true)

這樣一來，Slack 與 Jenkins 的整合就大功告成了！

#### Publish Over SSH

除了發送通知外，Jenkins 也可以在建置後透過 SSH 替我們將建置產物傳送至目標伺服器上，以下將簡單介紹如何實現這樣的功能。

##### 使用 Vagrant 模擬目標伺服器

因為在這個章節中我們會需要另外一台主機來擔任部署伺服器的角色，我們可以利用 Vagrant 先創建另外一台伺服器。編輯 `Vagrantfile` 添加第二台主機：

```ruby
Vagrant.configure("2") do |config|
	# server config
	config.vm.define :server do |server_config|
		server_config.vm.box = "bento/ubuntu-14.04"
		server_config.vm.network "forwarded_port", guest: 8080, host: 8080

		# run playbook from Vagrant host
		server_config.vm.provision "ansible" do |ansible|
			ansible.playbook = "jenkins-ansible-lint.yml"
		end
	end

    # target config
    config.vm.define :target do |target_config|
        target_config.vm.box = "bento/ubuntu-14.04"
    end
end
```

我們稍微改寫了一下原本的 `Vagrantfile` 。現在我們定義了第二台虛擬主機，並將其命名為 `target`。由於這台主機並不需要安裝 Jenkins，所以我們只需要單純地告訴 Vagrant 它的作業系統即可。設置完畢後運行 `vagrant reload` 重新啟動主機並載入新的 `Vagrantfile`。重啟完畢後透過 `vagrant ssh-config` 查看虛擬機狀況：

```
Host server
  HostName 127.0.0.1
  User vagrant
  Port 2222
  UserKnownHostsFile /dev/null
  StrictHostKeyChecking no
  PasswordAuthentication no
  IdentityFile /path/to/private_key
  IdentitiesOnly yes
  LogLevel FATAL

Host target
  HostName 127.0.0.1
  User vagrant
  Port 2200
  UserKnownHostsFile /dev/null
  StrictHostKeyChecking no
  PasswordAuthentication no
  IdentityFile /path/to/private_key
  IdentitiesOnly yes
  LogLevel FATAL
```

沒有意外的話可以看到現在我們已經有兩台虛擬機準備好了。做好基本的伺服器配置後，我們就可以接著利用 Jenkins 操作接下來的步驟了。


##### 安裝 Publish Over SSH 插件

回到 Jenkins 插件管理頁面，搜尋 **Publish Over SSH** 這個插件並進行安裝：

![](https://github.com/tsoliangwu0130/learn-ansible-and-jenkins-in-30-days/blob/master/images/jenkins-post-build-actions-14.png?raw=true)

##### SSH 金鑰配對

安裝完成後，將兩台主機的 SSH 金鑰進行配對。因為現在我們的 `Vagrantfile` 裡定義了兩台主機，因為在使用 `vagrant ssh` 時我們要在後面加上主機名稱才知道要登入哪一台，舉例來說，我想要在 target 的主目錄下建立一個 jenkins_artifacts 的資料夾可以這樣做：

```shell
$ vagrant ssh target

Welcome to Ubuntu 14.04.5 LTS (GNU/Linux 3.13.0-128-generic x86_64)

 * Documentation:  https://help.ubuntu.com/

  System information as of Wed Jan  3 01:58:53 UTC 2018

  System load:  0.01              Processes:           89
  Usage of /:   2.7% of 38.02GB   Users logged in:     0
  Memory usage: 6%                IP address for eth0: 10.0.2.15
  Swap usage:   0%

  Graph this data and manage this system at:
    https://landscape.canonical.com/

$ mkdir jenkins_artifacts
```

建立好資料夾後記得還要與 server 主機進行 SSH 金鑰配對。我們可以在 sever 的虛擬機上使用以下指令來確認 server 可以使用 SSH 登入至 target 虛擬機上：

```shell
$ vagrant ssh server
$ ssh vagrant@10.0.2.15
$ Are you sure you want to continue connecting (yes/no)? yes
```

如果一切順利這時候就應該會在 server 端上看到我們成功使用 vagrant 的身份登入 target 虛擬機了。

> 如果是使用 Ansible 實戰安裝 Jenkins 的讀者，由於我是將 Jenkins 運行在 Docker 內，所以在登入 server 虛擬機後，還需要透過以下指令進入運行 Jenkins 的容器：
> ```
> $ sudo docker exec -it docker-jenkins /bin/bash
> ```
> 登入以後再依正常方式進行 SSH 配對即可。

##### 在 Jenkins 上新增 SSH 伺服器

配對完成後，我們現在要在 Jenkins 上新增一個 SSH 伺服器。回到 Jenkins 組態管理介面，並找到 **Publish over SSH** 欄位，點選 **Add** 來新增目標伺服器：

![](https://github.com/tsoliangwu0130/learn-ansible-and-jenkins-in-30-days/blob/master/images/jenkins-post-build-actions-15.png?raw=true)

其中有幾個重點欄位要特別注意：

1. **Name**: SSH 伺服器名稱。
2. **Hostname**: 目標伺服器的 IP 位置。雖然從 `vagrant ssh-config` 的指令中我們看到 HostName 是 `127.0.0.1`，但 Vagrant 會預設自動幫所有虛擬機的 localhost IP 轉介成 `10.0.2.2`，以便我們若要從本機端操作虛擬機的時候不至於與本機衝突。
3. **Username**: 伺服器登入者身份，預設為 `vagrant`。
4. **Remote Directory**: 透過 SSH 傳輸檔案的傳輸目的地。
5. **Passphrase / Password**: 登入目標伺服器的密碼，預設也是 `vagrant`。
6. **Port**: 由於所有本地端的 Vagrant 虛擬機預設 IP 都是 `10.0.2.2`，因此我們必須在這邊強調不同伺服器對應的 port 口。從上面的 `vagrant ssh-config` 中我們可以看到 Jenkins 安裝主機被分配的 port 是 `2222`，而 target 主機的 port 則是 `2200`。

設定完成後，點選 **Test Configuration** 測試連線，如果出現 **Success** 的訊息，就表示我們現在已經正確將 SSH Server 添加至 Jenkins 伺服器管理列表中了，接著儲存設定後離開。

##### 設定專案建置後動作

在一切就緒後，最後，我們要在專案內設定相關作業。進入專案組態設定後，修改建置動作為：

```shell
echo "Building Environment: $VAR" > env_var.txt
```

我們在這裡將建置結果輸出為 `env_var.txt` 以模擬一個建置產物。接著在**建置後動作**的欄位下，選擇 **Send build artifacts over SSH**。在 **SSH Server** 的下拉式選單中可以選擇剛剛設定好的 SSH 主機，若我們今天有多個部署主機，可以在下拉式選單中進行更改。另外，在 **Transfers** 的欄位裡我們可以設定要傳輸的檔案、結構目錄以及是否要對傳輸檔案進行其他動作：

![](https://github.com/tsoliangwu0130/learn-ansible-and-jenkins-in-30-days/blob/master/images/jenkins-post-build-actions-16.png?raw=true)

設定完成後點選**儲存**離開並建置專案。從建置過程裡我們可以看到 Jenkins 在建置完成後透過了 SSH 傳送了一個檔案至 **target** 伺服器上：

![](https://github.com/tsoliangwu0130/learn-ansible-and-jenkins-in-30-days/blob/master/images/jenkins-post-build-actions-17.png?raw=true)

我們可以登入至我們的 target 主機上，確認檔案是否有被正確傳送：

```shell
$ vagrant ssh target
$ ls /home/vagrant/jenkins_artifacts

env_var.txt
```

正如我們預期的，建置產物已經正確被傳送到我們的部署伺服器上了！這邊要特別注意的是，雖然檔案會被傳送到目標伺服器上，但在原本的工作專案目錄 (workspace) 下還是會有一份原始建物存在，若不希望建置產物留在 Jenkins 伺服器上，記得要另外在組態內進行調整。
