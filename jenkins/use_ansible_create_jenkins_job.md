# 使用 Ansible 部署 Jenkins 工作專案

在上一個章節中，我們簡單知道了如何使用 Jenkins 的網路介面來建立一個建置專案。在這個章節裡，我們要試著將這個過程自動化為一個 Ansible 的 role。

#### 工作環境目錄與工作設定目錄

在專案示範中，我們利用了 `pwd` 及 `touch` 這兩個指令來讓 Jenkins 回傳**工作環境目錄 (workspace)** 的實際位置 (`/var/lib/jenkins/workspace/<job_name>`) 至終端機上，並在該目錄下建立 `helloworld.out` 這個檔案。

我們可以登入到遙控主機中觀察此路徑下的目錄結構：

```shell
$ ls -al /var/lib/jenkins/workspace/my_first_jenkins_job

total 8
drwxr-xr-x 2 jenkins jenkins 4096 Dec 19 01:51 .
drwxr-xr-x 3 jenkins jenkins 4096 Dec 18 03:26 ..
-rw-r--r-- 1 jenkins jenkins    0 Dec 19 01:52 helloworld.out
```

從目錄內容中我們可以很清楚地看到所有專案的工作內容，都會被存在這個路徑下，包含我們透過 `touch` 指令產生的 `helloworld.out` 檔案。在所有的 Jenkins 專案中，若有原始碼的建置流程，預設都會將從原始碼託管服務的原始碼下載到這個位置。

除此之外，每個 Jenkins 專案都還有另外一個**工作設定目錄**，來專門存放各專案的配置檔及建置紀錄，其路徑如下：`/var/lib/jenkins/jobs/<job_name>`。目前該目錄有以下內容：

```shell
$ ls -al /var/lib/jenkins/jobs/my_first_jenkins_job/

total 20
drwxr-xr-x 3 jenkins jenkins 4096 Dec 19 01:52 .
drwxr-xr-x 3 jenkins jenkins 4096 Dec 19 01:51 ..
drwxr-xr-x 3 jenkins jenkins 4096 Dec 19 01:52 builds
-rw-r--r-- 1 jenkins jenkins  611 Dec 19 01:52 config.xml
lrwxrwxrwx 1 jenkins jenkins   22 Dec 19 01:52 lastStable -> builds/lastStableBuild
lrwxrwxrwx 1 jenkins jenkins   26 Dec 19 01:52 lastSuccessful -> builds/lastSuccessfulBuild
-rw-r--r-- 1 jenkins jenkins    2 Dec 19 01:52 nextBuildNumber
```

其中 `config.xml` 就存放著這次我們透過 Jenkins 網頁介面配置的專案設置，我們可以使用 `cat` 指令來輸出這個配置檔內容：

```shell
$ cat /var/lib/jenkins/jobs/my_first_jenkins_job/config.xml
```

我們也可以直接透過瀏覽器訪問 `http://localhost:9080/job/my_first_jenkins_job/config.xml` 得到同樣的配置檔內容。注意到以下 `config.xml` 中的 `builders` 標籤，這次範例專案的執行指令就被存放在這個區塊中。

```xml
<project>
	<description/>
	<keepDependencies>false</keepDependencies>
	<properties/>
	<scm class="hudson.scm.NullSCM"/>
	<canRoam>true</canRoam>
	<disabled>false</disabled>
	<blockBuildWhenDownstreamBuilding>false</blockBuildWhenDownstreamBuilding>
	<blockBuildWhenUpstreamBuilding>false</blockBuildWhenUpstreamBuilding>
	<triggers/>
	<concurrentBuild>false</concurrentBuild>
	<builders>
		<hudson.tasks.Shell>
			<command>pwd touch helloworld.out</command>
		</hudson.tasks.Shell>
	</builders>
	<publishers/>
	<buildWrappers/>
</project>
```

主要來說，一般工作專案會需要特別注意以上兩種目錄，在自動化部署的過程中，我們也大多數都是會操作到以上兩個目錄。

#### 如何使用 Ansible 自動化部署 Jenkins 工作專案？

我們現在可以知道所有的 Jenkins 配置檔都存放在 `/var/lib/jenkins/jobs/<job_name>/config.xml` 的路徑下。在自動化部署的過程中，我們只需要將這個配置檔放置到這個位置並重啟 Jenkins 即可。

在 Ansible 2.2 版本以前，我們必須要自己利用 Ansible template 的模組來複製我們的設定檔至指定目錄，可能還有許多細節必須要自己做微調。但在 Ansible 2.2 之後，Ansible 釋出了一個新的模組 - [jenkins_job](https://docs.ansible.com/ansible/jenkins_job_module.html)，由於使用方式非常直覺，我們可以直接呼叫這個模組來節省部署 Jenkins 的時間。不過，由於使用這個模組需要額外一些額外的套件，所以我們必須在 `roles/jenkins/tasks/main.yml` 下加入以下 inculde 描述：

```yml
  - name: install jenkins_job module
    include: jenkins_job_dependencies.yml
```

並在 `roles/jenkins/tasks` 中新建 `jenkins_job_dependencies.yml`：

```yml
  - name: install jenkins_job dependencies (via apt-get)
    apt:
      name: "{{ item }}"
    with_items:
      - libxml2-dev
      - libxslt-dev
      - python-dev

  - name: install jenkins_job dependencies (via pip)
    pip:
      name: "{{ item }}"
    with_items:
      - lxml
      - python-jenkins
```

另外，因為並不是每一個安裝 Jenkins 的伺服器都會需要部署同樣的工作專案，所以我們會將不同的 Jenkins 工作專案獨立成不同的 role，以維持彈性。

依照以下結構新增 `my_first_jenkins_job` 的 role：

```shell
workspace
├── Vagrantfile
├── ansible.cfg
├── inventory
├── playbook.yml
└── roles
    ├── ansible-lint
    ├── curl
    ├── git
    ├── jenkins
    ├── my_first_jenkins_job
    │   ├── meta
    │   │   └── main.yml
    │   └── tasks
    │       └── main.yml
    └── pip
```

在 `roles/my_first_jenkins_job/meta/main.yml` 中添加 `jenkins` 為其服務依賴：

```yml
---
  dependencies:
    - { role: jenkins, become: yes }
```

接著，在 `roles/my_first_jenkins_job/tasks/main.yml` 中加入以下內容：

```yml
---
  - name: create jenkins job - my_first_jenkins_job
    jenkins_job:
      config: "{{ lookup('template', 'templates/config.xml.j2') }}"
      name: my_first_jenkins_job
      url: "http://localhost:8080"
      user: admin
      password: admin
```

我們在這個任務中除了定義了 Jenkins 的使用者資訊、密碼、存取網址，還必須描述設定檔參照路徑。因此，我們現在也必須在對應路徑 (`roles/my_first_jenkins_job/templates/`) 下把我們剛剛在遙控主機上的 `config.xml` 貼上，並重新命名為 `config.xml.j2`。其中，`j2` 代表著 Ansible 預設使用的模板引擎 (template engine) - [Jinja2](http://jinja.pocoo.org/)。這個模板引擎提供了開發人員諸多開發便利性，它讓我們在部署及設定檔案時有非常大的彈性進行維護及升級，由於篇幅關係，更詳細的 [Ansible 官方教學](https://docs.ansible.com/ansible-container/container_yml/template.html)請讀者自行查閱。

_注意： 將使用者帳號及密碼寫入 Ansible role 是一個非常危險的行為，在後面的章節我們會利用 Ansible playbook 將這種敏感資料分開。另外，這裡使用 port 8080 而非 port 9080 是因為我們現在是利用 Ansible 在遙控節點上進行部署，而非從外部透過瀏覽器訪問 Jenkins。_

最後，更改我們的 playbook：

```yml
---
- hosts: ironman
  roles:
    - { role: my_first_jenkins_job, become: yes }
```

運行 playbook 後得到以下結果：

```shell
...上略
TASK [jenkins : restart jenkins] ***********************************************
changed: [ironman]

TASK [jenkins : install jenkins_job dependencies (via apt-get)] ****************
changed: [ironman] => (item=[u'libxml2-dev', u'libxslt-dev', u'python-dev'])

TASK [jenkins : install jenkins_job dependencies (via pip)] ********************
changed: [ironman] => (item=lxml)
changed: [ironman] => (item=python-jenkins)

TASK [my_first_jenkins_job : delete jenkins job - my_first_jenkins_job] ********
fatal: [ironman]: FAILED! => {"changed": false, "failed": true, "msg": "Unable to validate if job exists, HTTP Error 503: Service Unavailable for http://localhost:8080"}
	to retry, use: --limit @/Users/tsoliang/Desktop/workspace/playbook.retry
```

雖然流程完全正確，但我們依舊在部署過程中發生錯誤了。不要害怕！當我們在編寫任何自動化腳本，或甚至開發軟體時遇到錯誤訊息 (error message) 是非常正常且頻繁的情形，雖然可能一開始往往會不太理解發生錯誤的理由，但讓我們試著去閱讀錯誤訊息，我們通常可以在其中發現一些蛛絲馬跡。所以，為什麼這次部署會失敗呢？

#### 使用 Handler 來重啟 Jenkins 服務

從上面的錯誤訊息我們可以發現，

