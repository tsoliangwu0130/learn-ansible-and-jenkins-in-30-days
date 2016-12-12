# 設定 Vagrant forward port

在前面的幾個章節內，我們只有簡單的利用 cURL 測試 Jenkins 是否已經正確安裝。為了能在瀏覽器上正確顯示 Jenkins 的操作介面，現在我們要透過更新 `Vagrantfile` 來讓主機端的瀏覽器可以訪問 Jenkins 位於 `localhost` 的服務。

#### Vagrant port forward

由於 Jenkins 預設是運行在主機的 `port 8080`，因此我們必須試著把遙控節點的 `port 8080` 廣播到控制主機上。不過，由於 `port 8080` 是一個非常容易被其他服務使用的 port，因此為了避免衝突，我們將利用 `forwarded_port` 這個參數來將遙控節點上的 `port 8080` 映射到控制主機上的 `port 9080`。

在 `Vagrantfile` 中，直接在 `config.vm.define "ironman"` 該行下面新增以下設定：

```ruby
config.vm.network "forwarded_port", guest: 8080, host: 9080
```

這樣表示我們可以直接在控制主機上使用 `port 9080` 來訪問遙控節點上 `port 8080` 的服務。現在重新啟動 Vagrant 來讓新設定生效：

```shell
$ vagrant reload

==> ironman: Attempting graceful shutdown of VM...
==> ironman: Checking if box 'hashicorp/precise64' is up to date...
==> ironman: Clearing any previously set forwarded ports...
==> ironman: Clearing any previously set network interfaces...
==> ironman: Preparing network interfaces based on configuration...
    ironman: Adapter 1: nat
==> ironman: Forwarding ports...
    ironman: 8080 (guest) => 9080 (host) (adapter 1)
    ironman: 22 (guest) => 2222 (host) (adapter 1)
==> ironman: Booting VM...
==> ironman: Waiting for machine to boot. This may take a few minutes...
    ironman: SSH address: 127.0.0.1:2222
    ironman: SSH username: vagrant
    ironman: SSH auth method: private key
    ironman: Warning: Remote connection disconnect. Retrying...
==> ironman: Machine booted and ready!
==> ironman: Checking for guest additions in VM...
    ironman: The guest additions on this VM do not match the installed version of
    ironman: VirtualBox! In most cases this is fine, but in rare cases it can
    ironman: prevent things such as shared folders from working properly. If you see
    ironman: shared folder errors, please make sure the guest additions within the
    ironman: virtual machine match the version of VirtualBox you have installed on
    ironman: your host and reload your VM.
    ironman:
    ironman: Guest Additions Version: 4.2.0
    ironman: VirtualBox Version: 5.0
==> ironman: Mounting shared folders...
    ironman: /vagrant => /Users/tsoliang/Desktop/workspace
==> ironman: Machine already provisioned. Run `vagrant provision` or use the `--provision`
==> ironman: flag to force provisioning. Provisioners marked to run always will still run.
```

我們可以從上面資訊中看到 Vagrant 已經幫我們把遙控節點上的 `port 8080` 廣播到控制主機的 `port 9080` 了。現在嘗試透過瀏覽器訪問 `http://localhost:9080` 就應該可以順利進入 Jenkins 的初始化頁面了：

![jenkins_index_9080](https://github.com/tsoliangwu0130/learn-ansible-and-jenkins-in-30-days/blob/master/images/jenkins_index_9080.png?raw=true)
