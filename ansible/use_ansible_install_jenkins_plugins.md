# 使用 Ansible 安裝 Jenkins 插件

#### 了解 Jenkins 初始化流程

在自動化 Jenkins 部署過程前，讓我們先稍微了解一下 Jenkins 的初始化過程。在遙控節點安裝好 Jenkins 並成功運行後，我們可以在控制主機上使用瀏覽器透過 `http://localhost:9080` 進入 Jenkins 的初始畫面：

![jenkins_index_9080](https://github.com/tsoliangwu0130/learn-ansible-and-jenkins-in-30-days/blob/master/images/jenkins_index_9080.png?raw=true)

Jenkins 在安裝好後，會先自動幫管理員產生一組初始密碼，並將其存放於 `/var/lib/jenkins/secrets/initialAdminPassword` 的檔案中。其中，`/var/lib/jenkins/` 這個路徑是 Jenkins 的根目錄位置。由於 Jenkins 在安裝的過程中將所有 Jenkins 的相關檔案所有權都指定給 `jenkins` 這個系統用戶，所以我們可以將登入使用者切換成 `jenkins` 並在遙控主機中透過以下指令取得預設密碼：

```shell
$ sudo -u jenkins -i
$ cat /var/lib/jenkins/secrets/initialAdminPassword
```

取得預設密碼後，將其貼入剛剛的頁面中，接著我們會到這個頁面：

![jenkins_01](https://github.com/tsoliangwu0130/learn-ansible-and-jenkins-in-30-days/blob/master/images/jenkins_01.png?raw=true)

在一開始，我們可以先選取 **Install suggested plugins** 就好。未來若有需要，還是可以隨時新增其他的插件。

![jenkins_02](https://github.com/tsoliangwu0130/learn-ansible-and-jenkins-in-30-days/blob/master/images/jenkins_02.png?raw=true)

待所有安裝完成，我們可以在這個頁面建立我們的 admin 帳戶：

![jenkins_03](https://github.com/tsoliangwu0130/learn-ansible-and-jenkins-in-30-days/blob/master/images/jenkins_03.png?raw=true)

最後，當我們看到以下畫面，就表示 Jenkins 已經完成所有基本設定了：

![jenkins_04](https://github.com/tsoliangwu0130/learn-ansible-and-jenkins-in-30-days/blob/master/images/jenkins_04.png?raw=true)

![jenkins_05](https://github.com/tsoliangwu0130/learn-ansible-and-jenkins-in-30-days/blob/master/images/jenkins_05.png?raw=true)

#### Jenkins 是如何安裝插件的？

在設定完畢後，使用 `jenkins` 使用者進入遙控節點，我們可以發現在 Jenkins 跟目錄下有一個叫做 `plugins` 的資料夾，裡面有許多副檔名為 `.jpi` 的檔案，而這些檔案就是 Jenkins 插件的原始檔案。[簡單來說](https://wiki.jenkins-ci.org/display/JENKINS/Plugins#Plugins-Byhand)，在我們安裝 Jenkins 插件時，其實 Jenkins 就只是從官方[插件下載頁面](http://updates.jenkins-ci.org/download/plugins/) 中下載這些檔案，並置於 `plugins` 中而已。

#### 使用 Ansible 自動安裝 Jenkins 插件

在我們的 Ansible `jenkins` role 中依照以下結構新增檔案 `plugins_install.yml`：

```shell
workspace
├── Vagrantfile
├── ansible.cfg
├── inventory
├── playbook.yml
└── roles
    ├── ansible-lint
    │   ├── meta
    │   │   └── main.yml
    │   └── tasks
    │       └── main.yml
    ├── curl
    │   └── tasks
    │       └── main.yml
    ├── git
    │   └── tasks
    │       └── main.yml
    ├── jenkins
    │   ├── meta
    │   │   └── main.yml
    │   └── tasks
    │       ├── install_plugins.yml
    │       └── main.yml
    └── pip
        └── tasks
            └── main.yml
```

讀者可能已經注意到我們這次並沒有直接將任務加至 `main.yml` 中，這是因為我們並不希望每一個 task 的檔案過於冗長，以免檔案未來難以維護。比較好的做法是透過 Ansible 的 [include](http://docs.ansible.com/ansible/playbooks_roles.html#task-include-files-and-encouraging-reuse) 描述來在 task 檔案中呼叫其他的 task 以讓我們的檔案更好閱讀。

在 `roles/jenkins/tasks/main.yml` 中，加入 `include` 描述：

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

  - name: install plugins
    include: install_plugins.yml
    become_user: jenkins
```
