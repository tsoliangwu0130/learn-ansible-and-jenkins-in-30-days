# 建置第一個工作專案

在對 Jenkins 的介面有初步認識後，我們可以開始試著建置我們的第一個工作專案。

#### 如何建置工作專案

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

