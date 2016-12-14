# 使用 template 建立 Jenkins 使用者

#### 了解 Jenkins 初始化流程

在自動化 Jenkins 部署過程前，讓我們先稍微了解一下 Jenkins 的初始化過程。在遙控節點安裝好 Jenkins 並成功運行後，我們可以在控制主機上使用瀏覽器透過 `http://localhost:9080` 進入 Jenkins 的初始畫面：

![jenkins_index_9080](https://github.com/tsoliangwu0130/learn-ansible-and-jenkins-in-30-days/blob/master/images/jenkins_index_9080.png?raw=true)

Jenkins 在安裝好後，會先自動幫管理員產生一組初始密碼，並將其存放於 `/var/lib/jenkins/secrets/initialAdminPassword` 的檔案中。其中，`/var/lib/jenkins/` 這個路徑是 Jenkins 的根目錄位置。我們可以在遙控主機中透過以下指令取得預設密碼：

```shell
$ sudo cat /var/lib/jenkins/secrets/initialAdminPassword
```

取得預設密碼後，將其貼入剛剛的頁面中，接著我們會到這個頁面：

![jenkins_01](https://github.com/tsoliangwu0130/learn-ansible-and-jenkins-in-30-days/blob/master/images/jenkins_01.png?raw=true)

在一開始，我們可以先選取 **Install suggested plugins** 就好。未來若有需要，還是可以隨時新增其他的插件。

