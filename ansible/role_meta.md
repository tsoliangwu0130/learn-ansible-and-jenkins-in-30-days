# 建立 Ansible Role 的依賴關係

在完成第一個簡單的 role 之後，讓我們試著把安裝 Jenkins 的步驟也自動化。

#### 自動化 Jenkins 安裝

讓我們重新複習一下 Jenkins 在 Ubuntu 上的安裝方式：

```shell
$ wget -q -O - https://pkg.jenkins.io/debian/jenkins-ci.org.key | sudo apt-key add -
$ sudo sh -c 'echo deb http://pkg.jenkins.io/debian-stable binary/ > /etc/apt/sources.list.d/jenkins.list'
$ sudo apt-get update
$ sudo apt-get install jenkins
```

簡單來說，我們會從 Jenkins 官方釋出的 [Debian 套件庫](https://pkg.jenkins.io/debian/)中將[密鑰](https://wiki.debian.org/SecureApt) (key) 加入本地，並新增對應的倉庫 (repository) 入口。最後，再透過 apt 這個套件管理進行 Jenkins 的安裝。現在讓我們把上面的指令翻譯成 Ansible 的腳本：

```yml
---
  - name: add jenkins key
    apt_key:
      url: http://pkg.jenkins-ci.org/debian/jenkins-ci.org.key
      state: present

  - name: add jenkins repository
    apt_repository:
      repo: deb http://pkg.jenkins-ci.org/debian binary/
      state: present

  - name: install jenkins
    apt:
      name: jenkins
      update_cache: yes
```

其中 [apt_key](http://docs.ansible.com/ansible/apt_key_module.html) 及 [apt_repository](http://docs.ansible.com/ansible/apt_repository_module.html) 都是 Ansible 已經內建的兩個模組，我們可以直接利用它們進行密鑰及倉庫的新增，更多細節操作可以在官方的文件上查看。而在 `install jenkins` 這個 task 之下的 `update_cache` 其實就是等效於我們在一般 Ubuntu / Debian 系統下使用 `apt-get update` 的指令，若在操作 Ansible 的 [apt](http://docs.ansible.com/ansible/apt_module.html) 這個模組時使用了 `update_cache: yes`，這就意味著我們會先將遙控節點上的 apt 套件管理進行升級才做接下來的動作。

解釋完要做的事以後，這次我也要同時將整個安裝 Jenkins 的流程寫成另外一個 role。一樣的，在工作資料夾下依照以下結構新增 `jenkins` 這個 role，並在 `roles/jenkins/tasks/main.yml` 中輸入以上內容：

```shell
workspace
├── Vagrantfile
├── ansible.cfg
├── inventory
├── playbook.yml
└── roles
    ├── curl
    │   └── tasks
    │       └── main.yml
    └── jenkins
        └── tasks
            └── main.yml
```

現在我們有兩個 roles 了。回到我們的 `playbook.yml` 作以下的調整，並運行 playbook：

```yml
---
- hosts: ironman
  roles:
    - { role: curl, become: true }
    - { role: jenkins, become: true }
```

結果如下：

```shell
==> ironman: Running provisioner: ansible...
    ironman: Running ansible-playbook...

PLAY [ironman] *****************************************************************

TASK [setup] *******************************************************************
ok: [ironman]

TASK [curl : install curl] *****************************************************
changed: [ironman]

TASK [jenkins : add jenkins key] ***********************************************
changed: [ironman]

TASK [jenkins : add jenkins repository] ****************************************
changed: [ironman]

TASK [jenkins : install jenkins] ***********************************************
changed: [ironman]

PLAY RECAP *********************************************************************
ironman                    : ok=5    changed=4    unreachable=0    failed=0
```

如預期的一樣，我們透過 Ansible 呼叫兩個不同的自定義 role (`curl`, `jenkins`)，並依序對遙控主機進行安裝。到這裡，讀者應該就可以開始感受到為什麼我們會需要撰寫各式不同的 role 。透過建立不同的 role，我們可以任意在不同的需求下重新利用我們曾經寫過的 role。舉例來說，如果今天我們要開發一個新的 playbook，其內容要在部署 API 的伺服器上面同時也安裝 Jenkins 這個服務，我們就不需要在新的 playbook 中又重新編寫一次安裝的流程，唯一需要做的就是在 playbook 中呼叫我們已經寫好用來安裝 Jenkins 的 role 即可。

在 Ansible 中，所有的 role 都是獨立的，若今天有其中一個服務需要被更新，我們不用遍查所有 playbook 去更新跟該服務有關的片段，我們只需要找到負責部署該服務的 role 並做調整，所有其他呼叫過這個 role 的 playbook 等於都自動同時更新了。

#### Ansible Role 的依賴關係

現在讓我們重新思考另外一個問題，除了逐一呼叫 role 之外，我們有沒有其他更優雅的方式來定義自動化部署的步驟呢？舉例來說，如果我們希望在每一次安裝 Jenkins 的伺服器上，都同時安裝 cURL 這個工具，有沒有其他更簡單明瞭的方式呢？

答案是有的，在 Ansible 的世界中，我們可以透過定義 role 與 role 之間的依賴關係 ([role dependencies](http://docs.ansible.com/ansible/playbooks_roles.html#role-dependencies)) 來提升每一個 role 的可讀性。就以上面的例子而言，我們希望所有部署 Jenkins 的伺服器都預先搭載好 cURL 這個工具，若用 Ansible 的角度思考，這就意味著我們希望 `jenkins` 這個 role 依賴於 `curl` 這個 role。我們可以在每一個 role 的資料夾中透過 `meta` 這個子目錄定義這樣的依賴關係，具體步驟如下：

依照以下結構新增檔案：

```shell
workspace
├── Vagrantfile
├── ansible.cfg
├── inventory
├── playbook.yml
└── roles
    ├── curl
    │   └── tasks
    │       └── main.yml
    └── jenkins
        ├── meta
        │   └── main.yml
        └── tasks
            └── main.yml
```

在 `roles/jenkins/meta/main.yml` 中輸入以下內容：

```yml
---
  dependencies:
    - { role: curl, become: yes }
```

回到 `playbook.yml` 中刪除原本呼叫 `curl` role 的部分，只留下 `jenkins`：

```yml
---
- hosts: ironman
  roles:
    - { role: jenkins, become: yes }
```

重新運行 playbook.yml，結果如下：

```shell
==> ironman: Running provisioner: ansible...
    ironman: Running ansible-playbook...

PLAY [ironman] *****************************************************************

TASK [setup] *******************************************************************
ok: [ironman]

TASK [curl : install curl] *****************************************************
ok: [ironman]

TASK [jenkins : add jenkins key] ***********************************************
ok: [ironman]

TASK [jenkins : add jenkins repository] ****************************************
ok: [ironman]

TASK [jenkins : install jenkins] ***********************************************
changed: [ironman]

PLAY RECAP *********************************************************************
ironman                    : ok=5    changed=1    unreachable=0    failed=0
```

結果完全一樣！根據 role 的新結構，我們每次只要呼叫 `jenkins` 這個 role，在安裝 Jenkins 之前，Ansible 就會自動先幫我們把所有定義在 `meta` 中的服務依賴安裝好。若未來在 `jenkins` 這個 role 上要新增或移除服務依賴，我們只需要回到 `meta` 中做修改即可。

透過定義 role 之間的依賴關係，我們可以在 playbook 中只單純呼叫需要的 role，而不用回到底層思考運行這個 role 之前我們還需要安裝什麼額外的服務。這樣的設計邏輯不但可以將每一個 role 都視為已經完全可以用的工具，在維護的角度上也使安裝結構更佳清晰易懂。開發人員過去往往因為繁瑣的安裝流程難免疏忽其中幾個前置服務安裝，進而導致後面部署流程發生的錯誤，透過這樣的設計流程，將可以徹底被解決。

