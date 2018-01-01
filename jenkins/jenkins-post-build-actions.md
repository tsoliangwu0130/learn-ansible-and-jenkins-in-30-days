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
