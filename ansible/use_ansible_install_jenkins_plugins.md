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

在設定完畢後，使用 `jenkins` 使用者進入遙控節點，我們可以發現在 Jenkins 跟目錄下有一個叫做 `plugins` 的資料夾，裡面有許多副檔名為 `.jpi` 或是 `.hpi` 的檔案，而這些檔案就是 Jenkins 插件的原始檔案。[簡單來說](https://wiki.jenkins-ci.org/display/JENKINS/Plugins#Plugins-Byhand)，在我們安裝 Jenkins 插件時，其實 Jenkins 就只是從官方[插件下載頁面](http://updates.jenkins-ci.org/download/plugins/) 中下載這些檔案，並置於 `plugins` 中而已。在運行 Jenkins 後，Jenkins 會自己從這個資料夾中找到插件原始檔進行自動安裝。因此，自動化這個過程我們唯一需要做的，就是把這些原始檔複製到 /`var/lib/jenkins/plugins` 下即可。

#### 使用 Ansible 自動安裝 Jenkins 插件

在我們的 Ansible `jenkins` role 中依照以下結構新增檔案 `plugins_install.yml`：

```shell
roles/jenkins
├── meta
│   └── main.yml
└── tasks
    ├── install_plugins.yml
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
```

接著，在 `roles/jenkins/tasks/plugins_install.yml` 中加入以下內容：

```yml
---
  - name: create plugins directory
    file:
      path: /var/lib/jenkins/plugins/
      state: directory
      owner: jenkins
      group: jenkins

  - name: download plugins
    get_url:
      url: http://updates.jenkins-ci.org/latest/{{ item }}.hpi
      dest: /var/lib/jenkins/plugins/{{ item }}.hpi
      owner: jenkins
      group: jenkins
    with_items:
      - ace-editor
      - antisamy-markup-formatter
      - ant
      - bouncycastle-api
      - branch-api
      - build-timeout
      - cloudbees-folder
      - credentials
      - credentials-binding
      - display-url-api
      - durable-task
      - email-ext
      - external-monitor-job
      - git-client
      - github-api
      - github-branch-source
      - github
      - github-organization-folder
      - git
      - git-server
      - gradle
      - handlebars
      - icon-shim
      - jquery-detached
      - junit
      - ldap
      - mailer
      - mapdb-api
      - matrix-auth
      - matrix-project
      - momentjs
      - pam-auth
      - pipeline-build-step
      - pipeline-graph-analysis
      - pipeline-input-step
      - pipeline-milestone-step
      - pipeline-rest-api
      - pipeline-stage-step
      - pipeline-stage-view
      - plain-credentials
      - resource-disposer
      - scm-api
      - script-security
      - ssh-credentials
      - ssh-slaves
      - structs
      - subversion
      - timestamper
      - token-macro
      - windows-slaves
      - workflow-aggregator
      - workflow-api
      - workflow-basic-steps
      - workflow-cps-global-lib
      - workflow-cps
      - workflow-durable-task-step
      - workflow-job
      - workflow-multibranch
      - workflow-scm-step
      - workflow-step-api
      - workflow-support
      - ws-cleanup

  - name: restart jenkins
    service:
      name: jenkins
      state: restarted
```

由於初始化的 Jenkins 在還沒有使用網頁介面進行設定前 `/var/lib/jenkins/plugins` 這個資料夾是不會被建立的，因此我們必須使 Ansible 先建立這個資料夾來存放我們的插件。接著，透過官方插件的載點依序下載所有 Jenkins 推薦插件。由於每一個插件都有各自的插件依賴，舉例來說，[git](https://wiki.jenkins-ci.org/display/JENKINS/Git+Plugin) 這個 Jenkins 插件的依賴就有 `workflow-scm-step`, `credentials`, `git-client `, `mailer`, `matrix-project`, `promoted-builds`, `scm-api`, `ssh-credentials`, `token-macro ` 以及 `parameterized-trigger` 這麼多項，為了讓 `git` 這個插件正常運作，我們必須要將所有插件依賴也一並下載至 `/var/lib/jenkins/plugins` 資料夾。若是透過網頁安裝這些插件，Jenkins 在安裝指定插件前會自動把所需的插件下載完成，這也是為什麼在這個插件安裝清單中我們會看到遠比在網頁介面看到安裝插件來得多的原因。為了節省讀者一一查找插件依賴的時間，讀者可以自行複製上面的插件清單進行安裝。在未來，若有新的插件需要安裝，我們都必須確定該插件的依賴也有同時被安裝至伺服器上。最後，重啟 Jenkins 這個服務就大功告成啦！

運行後結果如下：

```shell
TASK [jenkins : create plugins directory] **************************************
changed: [ironman]

TASK [jenkins : download plugins] **********************************************
changed: [ironman] => (item=ace-editor)
changed: [ironman] => (item=antisamy-markup-formatter)
changed: [ironman] => (item=ant)
changed: [ironman] => (item=bouncycastle-api)
changed: [ironman] => (item=branch-api)
changed: [ironman] => (item=build-timeout)
changed: [ironman] => (item=cloudbees-folder)
changed: [ironman] => (item=credentials-binding)
changed: [ironman] => (item=display-url-api)
changed: [ironman] => (item=durable-task)
changed: [ironman] => (item=email-ext)
changed: [ironman] => (item=external-monitor-job)
changed: [ironman] => (item=git-client)
changed: [ironman] => (item=github-api)
changed: [ironman] => (item=github-branch-source)
changed: [ironman] => (item=github)
changed: [ironman] => (item=github-organization-folder)
changed: [ironman] => (item=git)
changed: [ironman] => (item=git-server)
changed: [ironman] => (item=gradle)
changed: [ironman] => (item=handlebars)
changed: [ironman] => (item=icon-shim)
changed: [ironman] => (item=jquery-detached)
changed: [ironman] => (item=junit)
changed: [ironman] => (item=ldap)
changed: [ironman] => (item=mailer)
changed: [ironman] => (item=mapdb-api)
changed: [ironman] => (item=matrix-auth)
changed: [ironman] => (item=matrix-project)
changed: [ironman] => (item=momentjs)
changed: [ironman] => (item=pam-auth)
changed: [ironman] => (item=pipeline-build-step)
changed: [ironman] => (item=pipeline-graph-analysis)
changed: [ironman] => (item=pipeline-input-step)
changed: [ironman] => (item=pipeline-milestone-step)
changed: [ironman] => (item=pipeline-rest-api)
changed: [ironman] => (item=pipeline-stage-step)
changed: [ironman] => (item=pipeline-stage-view)
changed: [ironman] => (item=plain-credentials)
changed: [ironman] => (item=resource-disposer)
changed: [ironman] => (item=scm-api)
changed: [ironman] => (item=script-security)
changed: [ironman] => (item=ssh-credentials)
changed: [ironman] => (item=ssh-slaves)
changed: [ironman] => (item=structs)
changed: [ironman] => (item=subversion)
changed: [ironman] => (item=timestamper)
changed: [ironman] => (item=token-macro)
changed: [ironman] => (item=windows-slaves)
changed: [ironman] => (item=workflow-aggregator)
changed: [ironman] => (item=workflow-api)
changed: [ironman] => (item=workflow-basic-steps)
changed: [ironman] => (item=workflow-cps-global-lib)
changed: [ironman] => (item=workflow-cps)
changed: [ironman] => (item=workflow-durable-task-step)
changed: [ironman] => (item=workflow-job)
changed: [ironman] => (item=workflow-multibranch)
changed: [ironman] => (item=workflow-scm-step)
changed: [ironman] => (item=workflow-step-api)
changed: [ironman] => (item=workflow-support)
changed: [ironman] => (item=ws-cleanup)

TASK [jenkins : restart jenkins] ***********************************************
changed: [ironman]
```

#### 使用變數增加 role 的易讀性

雖然我們的 role 現在可以正確運作了，但讀者從這次的教學中應該有注意到 `/var/lib/jenkins` 這個 Jenkins 的根目錄路徑以及 `/var/lib/jenkins/plugins` 插件安裝路徑在這個 role 中被重複使用多次，其實我們可以透過 Ansible 提供的[變數](http://docs.ansible.com/ansible/playbooks_variables.html#variables) 來取代我們在 role 中一再重複描述的路徑位置，在未來的維護上若有更新也會相對容易許多。

在 `jenkins` 的 role 中依照以下結構新增檔案：

```shell
roles/jenkins/
├── defaults
│   └── main.yml
├── meta
│   └── main.yml
└── tasks
    ├── install_plugins.yml
    └── main.yml
```

並在 `defaults/main.yml` 中新增兩個變數：

```yml
---
jenkins_home: /var/lib/jenkins
jenkins_plugins_path: "{{ jenkins_home }}/plugins"
```

每次 Ansible 運行這個 role 就會先進來這個資料夾閱讀變數，若在接下來的內容裡看到 `jenkins_home` 及 `jenkins_plugins_path` 兩個變數就會自動跟我們的定義翻譯成對應值。在 Ansible 中，變數也是可以被覆蓋的，較晚宣告的變數可以取代較早宣告的變數，因此在命名變數時要特別注意這一點。變數讀取順序可以在[官方文件](http://docs.ansible.com/ansible/playbooks_variables.html#variable-precedence-where-should-i-put-a-variable)中查詢，這裡就不再多做敘述。

最後，使用我們定義的變數，重新更新 `tasks/install_plugins.yml`：

```shell
---
  - name: create plugins directory
    file:
      path: "{{ jenkins_plugins_path }}"
      state: directory
      owner: jenkins
      group: jenkins

  - name: download plugins
    get_url:
      url: http://updates.jenkins-ci.org/latest/{{ item }}.hpi
      dest: "{{ jenkins_plugins_path }}/{{ item }}.hpi"
      owner: jenkins
      group: jenkins
    with_items:
      - ace-editor
      ... 下略
```