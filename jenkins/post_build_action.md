# 專案建置後發送通知

由於我們希望 Jenkins 能夠無時無刻自動地幫我們處理大部分的工作，因此在建置後我們可以利用插件讓 Jenkins 主動通知我們建置結果。

#### E-mail 條件式通知

現在重新打開 `ansible_lint` 的專案配置，在 `Post-build Actions` 的欄位下點擊 `Add post-build action`，並選擇 `E-mail Notification`：

![email_notification_01](https://github.com/tsoliangwu0130/learn-ansible-and-jenkins-in-30-days/blob/master/images/email_notification_01.png?raw=true)

這個欄位是 Jenkins 初始預設提供給使用者的 E-mail 通知配置，我們可以在這個欄位設定建置完後的通知名單，並選擇被標記為 `unstable` 狀態的專案是否要寄送通知、或是選擇是否要寄送信件通知造成建置失敗的開發人員，配置畫面如下：

![email_notification_02](https://github.com/tsoliangwu0130/learn-ansible-and-jenkins-in-30-days/blob/master/images/email_notification_02.png?raw=true)

然而，這樣的 E-mail 通知在現實的專案中功能是十分缺乏的，舉例來說，我們很多時候會希望只有在建置失敗的時候才要收到通知、或是在建置成功時通知某些特定聯絡人，甚至希望能夠客製化通知內容等等。所以我們在一開始建議安裝的插件列表中安裝了一個 [email-ext](https://wiki.jenkins-ci.org/display/JENKINS/Email-ext+plugin) 的插件，這個插件將大大地擴展我們使用 E-mail 發送通知的使用彈性。

在 `Add post-build action` 中選擇 `Editable Email Notification`：

![email_notification_03](https://github.com/tsoliangwu0130/learn-ansible-and-jenkins-in-30-days/blob/master/images/email_notification_03.png?raw=true)

我們可以看到這個插件比起之前的基本 E-mail 通知強大了不少，我們可以在這裡面根據不同需求做非常細節地調整：

![email_notification_04](https://github.com/tsoliangwu0130/learn-ansible-and-jenkins-in-30-days/blob/master/images/email_notification_04.png?raw=true)

值得留意的是，在 `Advanced Settings` 下的 `Triggers` 裡，我們可以設定觸發通知的條件，比如當建置失敗時通知的名單跟建置成功時通知的名單：

![email_notification_05](https://github.com/tsoliangwu0130/learn-ansible-and-jenkins-in-30-days/blob/master/images/email_notification_05.png?raw=true)

這在現實中的專案是非常重要的功能，隨著專案的數量增加以及各專案優先程度的不同，如何聰明地使用開發工具 (e.g. Jenkins) 來幫助我們有系統地管理專案是非常重要且必備的技能。由於這個章節的教學並沒有太多技術成分，大部分內容都蠻直覺且易使用，在這個章節就不做太多冗餘的介紹，讀者自行研究嘗試即可。