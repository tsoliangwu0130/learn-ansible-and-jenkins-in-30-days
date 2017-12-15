# 透過 Ansible 運行 playbook

#### Ansible 如何運行 playbook？

要透過 Ansible 來運行 playbook 相當容易，我們只要使用 `ansible-playbook <playbook>` 的指令就可以輕易運行我們編寫好的 playbook：

```shell
$ ansible-playbook playbook.yml
```

執行完畢後我們應該可以在終端機上看到以下的輸出：

```
PLAY RECAP *********************************************************************
```

然而，這個結果是正確的嗎？

#### 什麼是 inventory file？

在看到輸出結果後，我相信大部分的讀者可能會納悶為什麼輸出結果沒有任何執行成功或失敗的資訊。從終端機的結果我們可以知道 Ansible 什麼任務都沒有做，這樣的結果也不是我們所預期的。在解決這問題之前，讓我們先回憶一下我們之前撰寫的 `playbook.yml`：

```yml
---
- hosts: server
  tasks:
    # task 1
    - name: test connection
      ping:
      register: message

    # task 2
    - name: print debug message
      debug:
        msg: "{{ message }}"
```

從這份 playbook 中，我們可以很清楚地看到 Ansible 應該要對 `server` 這台主機進行任務清單中定義的任務。在之前 [Vagrant 介紹](vagrant/tutorial.md)中，我們的確對運行起來的主機進行了命名，然而，我們卻從來沒有告訴 Ansible 過哪一台主機是 `server`。因此，解決這個問題最直接的方法就是定義一個目錄 (inventory) 並讓 Ansible 參照，這樣 Ansible 才會知道該對哪一台主機做什麼事。

為了知道 `server` 這台主機的詳細資料，我們可以透過 `vagrant ssh-config` 這個指令來列出主機的相關資訊：

```shell
$ vagrant ssh-config

Host server
  HostName 127.0.0.1
  User vagrant
  Port 2222
  UserKnownHostsFile /dev/null
  StrictHostKeyChecking no
  PasswordAuthentication no
  IdentityFile /Users/tsoliang/Desktop/workspace/.vagrant/machines/server/virtualbox/private_key
  IdentitiesOnly yes
  LogLevel FATAL
```

接著新增一個檔案並命名為 `inventory` ，然後在檔案內寫入以下內容：

```
[server]
127.0.0.1 ansible_port=2222 ansible_user=vagrant
```

根據 `vagrant ssh-config` 列出來的主機資訊，我們在 `inventory` 中依序定義了 `server` 這台主機的連線 IP、port 入口以及登入使用者的身份。現在，讓我們重新執行運行指令，並將這份主機清單指定給 Ansible 參照：

```shell
$ ansible-playbook -i inventory playbook.yml
```

現在，輸出結果如下：

```
PLAY [server] ******************************************************************

TASK [setup] *******************************************************************
fatal: [127.0.0.1]: UNREACHABLE! => {"changed": false, "msg": "Failed to connect to the host via ssh: Permission denied (publickey,password).\r\n", "unreachable": true}
  to retry, use: --limit @/Users/tsoliang/Desktop/workspace/playbook.retry

PLAY RECAP *********************************************************************
127.0.0.1                  : ok=0    changed=0    unreachable=1    failed=0
```

透過 inventory file 的配置，我們可以輕易的將不同部署環境做有系統的整理。舉例來說，在軟體開發實務上根據開發階段的不同，我們常常會有[多種不同的部署環境](https://en.wikipedia.org/wiki/Deployment_environment)，好比說最常見的 testing, development 以及 production。其中，每個環境可能又有不止一台的主機需要被維護，因此我們可以將每個環境需要配置的細節清楚定義在不同的 inventory file 中，然後透過類似以下指令來透過 Ansible 真正實現一鍵部署：

```shell
$ ansible-playbook -i devl-inventory devl-playbook.yml
$ ansible-playbook -i prod-inventory prod-playbook.yml
```

關於 inventory file 更進一步的寫法，可以參考[官方文件](http://docs.ansible.com/ansible/latest/intro_inventory.html)。現在 Ansible 知道誰是 `server` 了！不過，看起來 Ansible 似乎並沒有將我們 playbook 中定義內容成功地部署到 `server` 主機上。

#### 設定 PRIVATE_KEY_FILE

從上面的錯誤訊息中我們可以知道部署失敗的原因發生在 SSH 連線失敗。還記得我們說過使用 Ansible 操作 managed node 必須要透過 SSH 連線嗎？一般來說，我們如果要透過 SSH 存取主機 (由於篇幅的限制，我們在這次的教學文中就不對 SSH 設定著墨太深，有興趣的讀者可以上網搜尋相關資料或是參考 [GitHub](https://help.github.com/articles/generating-an-ssh-key/) 的 SSH 設定教學) 我們會預設將產生出來的公用金鑰 (public key) 存放在 `~/.ssh/` 的路徑下，然後再手動將這組金鑰加到我們想要授權的服務列表 (authorized_keys) 上。不過，因為我們是使用 Vagrant 建立起一台虛擬主機作為練習，Vagrant 其實已經事先幫我們把控制主機與遙控節點做好 SSH 配對了，所以我們並不需要手動設定這個部分。從上面的 `vagrant ssh-config` 輸出的結果中，我們可以看到一個特殊的參數：

```
IdentityFile /Users/tsoliang/Desktop/workspace/.vagrant/machines/server/virtualbox/private_key
```

這個參數告訴了我們這台虛擬主機 private key 的存放位置，我們只需要透過以下指令，將這個檔案的路徑指定給 Ansible，我們就可以成功運行我們的 playbook 囉！

```shell
$ ansible-playbook \
  --private-key /Users/tsoliang/Desktop/workspace/.vagrant/machines/server/virtualbox/private_key \
  -i inventory playbook.yml
```

運行結果如下：

```
PLAY [server] ******************************************************************

TASK [Gathering Facts] *********************************************************
ok: [127.0.0.1]

TASK [test connection] *********************************************************
ok: [127.0.0.1]

TASK [print debug message] *****************************************************
ok: [127.0.0.1] => {
    "msg": {
        "changed": false,
        "failed": false,
        "ping": "pong"
    }
}

PLAY RECAP *********************************************************************
127.0.0.1                  : ok=3    changed=0    unreachable=0    failed=0
```

大功告成！我們成功接收到從 `server` 回傳回來的 "pong" 了！在未來的章節內，我們還會介紹如何透過 `ansible.cfg` 這個檔案的設定來告訴 Ansible SSH 金鑰以及 inventory file 的位置，來讓整個部署流程看起來更加簡潔也讓開發人員更易維護。
