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
