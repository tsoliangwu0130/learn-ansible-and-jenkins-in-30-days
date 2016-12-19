# 建置第一個工作專案

在對 Jenkins 的介面有初步認識後，我們可以開始試著建置我們的第一個工作專案。

#### 建置專案介紹

在 Jenkins 首頁下，點擊 `New item` 的標籤頁來建立新的專案。任意命名專案名稱，並選取 `Freestyle project` 如下圖所示：

![my_first_jenkins_job_01](https://github.com/tsoliangwu0130/learn-ansible-and-jenkins-in-30-days/blob/master/images/my_first_jenkins_job_01.png?raw=true)

點選確認後，我們會進入專案細節設置的頁面：

![my_first_jenkins_job_02](https://github.com/tsoliangwu0130/learn-ansible-and-jenkins-in-30-days/blob/master/images/my_first_jenkins_job_02.png?raw=true)

依序我們可以看到建置專案的大致細節如下：

1. **General**

	![my_first_jenkins_job_general](https://github.com/tsoliangwu0130/learn-ansible-and-jenkins-in-30-days/blob/master/images/my_first_jenkins_job_general.png?raw=true)

	我們可以在 `General` 標籤下 做專案的一般配置，從專案名稱、描述、使用自訂專案目錄到終止專案都可以在這裡進行設定。

2. **Source Code Management**

	![my_first_jenkins_job_scm](https://github.com/tsoliangwu0130/learn-ansible-and-jenkins-in-30-days/blob/master/images/my_first_jenkins_job_scm.png?raw=true)

	若我們將我們的程式碼放在原始碼託管服務上，我們可以在這個地方進行相關設置。例如倉庫 (repository) 網址及指定要建置的分支版本。

3. **Build Triggers**

	![my_first_jenkins_job_build_trigger](https://github.com/tsoliangwu0130/learn-ansible-and-jenkins-in-30-days/blob/master/images/my_first_jenkins_job_build_trigger.png?raw=true)

	我們可以在 `Build Triggers` 的標籤下設置專案建置觸發器。若我們並不希望專案定期被建置，舉例來說我們希望我們只有在原始碼被更改時才要建置專案，也可以在這裡進行更改。

4. **Build Environment**

	![my_first_jenkins_job_build_environment](https://github.com/tsoliangwu0130/learn-ansible-and-jenkins-in-30-days/blob/master/images/my_first_jenkins_job_build_environment.png?raw=true)

	有時候我們也會需要替專案設置不同的環境參數，或添加服務設定檔至專案目錄，我們就可以在這個地方依據需求進行操作。

5. **Build**

	![my_first_jenkins_job_build](https://github.com/tsoliangwu0130/learn-ansible-and-jenkins-in-30-days/blob/master/images/my_first_jenkins_job_build.png?raw=true)

	這個部分就是 Jenkins 專案最核心的一個部分。我們可以在這裡描述我們要如何建置這個專案，無論透過 Jenkins 的支援模組、單純運行 Shell script 或甚至組合多個不同建置步驟都可以在這裡被詳細定義。

6. **Post-build Actions**

	![my_first_jenkins_job_post_build_action](https://github.com/tsoliangwu0130/learn-ansible-and-jenkins-in-30-days/blob/master/images/my_first_jenkins_job_post_build_action.png?raw=true)

	在建置完成後，我們可以在 `Post-build Actions` 的區塊中定義建置後動作。我們可以在這裡進行 E-mail 通知的發送，或是透過安裝通訊軟體的插件 (e.g. [HipChat](https://www.hipchat.com/)) 來發送通知到開發者頻道內。除此之外，若專案建置完成後有產生建置產物，我們也可以在這裡使用 SSH 傳送到指定伺服器上。

#### 我的第一個建置工作

我們會用一個非常簡單的例子來作為我們的專案建置介紹。在剛剛建置專案的頁面裡，在 `Build` 的標籤下新增建置步驟，並選擇 `Execute shell`：

![my_first_jenkins_job_pwd_touch](https://github.com/tsoliangwu0130/learn-ansible-and-jenkins-in-30-days/blob/master/images/my_first_jenkins_job_pwd_touch.png?raw=true)

在 `Execute shell` 下試著輸入以下命令作為建置指令：

```shell
$ pwd
$ touch helloworld.out
```

我們在建置步驟內，先呼叫 `pwd` 指令來回傳當前工作目錄並輸出至終端機上，接著在工作目錄下新增一個 `helloworld.out` 的空白檔案作為測試。設置好後儲存專案，進入當前專案建置頁面：

![my_first_jenkins_job_03](https://github.com/tsoliangwu0130/learn-ansible-and-jenkins-in-30-days/blob/master/images/my_first_jenkins_job_03.png?raw=true)

點擊左邊 `Build Now` 來立即建置這個專案後，我們可以在左下角 `Build History` 看到此專案的第一次建置已經完成：

![my_first_jenkins_job_04](https://github.com/tsoliangwu0130/learn-ansible-and-jenkins-in-30-days/blob/master/images/my_first_jenkins_job_04.png?raw=true)

接著點擊 `Build History` 下 `#1` 的建置紀錄，並進入以下介面：

![my_first_jenkins_job_05](https://github.com/tsoliangwu0130/learn-ansible-and-jenkins-in-30-days/blob/master/images/my_first_jenkins_job_05.png?raw=true)

我們可以在這個頁面下瀏覽該次建置的紀錄。其中，建置的詳細流程及終端機輸出即時地記錄在 `Console Output` 的子頁面下：

![my_first_jenkins_job_06](https://github.com/tsoliangwu0130/learn-ansible-and-jenkins-in-30-days/blob/master/images/my_first_jenkins_job_06.png?raw=true)

我們可以從 `Console Output` 中看到這次建置是以 admin 管理員的身份進行操作。這次建置過程中運行了 `pwd` 這個指令，並成功回傳專案所在位置：`/var/lib/jenkins/workspace/my_first_jenkins_job`。

最後，回到 Jenkins 控制主頁面，我們可以看到以下畫面：

![jenkins_07](https://github.com/tsoliangwu0130/learn-ansible-and-jenkins-in-30-days/blob/master/images/jenkins_07.png?raw=true)

在主控頁面下，可以看到現在我們已經有了第一個專案工作，並紀錄了最近一次的成功或失敗的建置，以及建置所花費的時間。
