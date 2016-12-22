# 定時建置 Jenkins 工作專案

對於持續整合的服務而言，最重要的特色之一就是能夠幫我們定期地建置專案，以確保原始碼的高品質。不過目前我們還沒有設定任何自動建置的觸發器，所以在這個章節，我將簡單介紹 Jenkins 最常用的定期建置觸發器。

#### 週期性建置工作專案

現在重新打開 `ansible_lint` 的專案配置，看到 `Build Triggers` 的欄位：

![jenkins_ansible_lint_09](https://github.com/tsoliangwu0130/learn-ansible-and-jenkins-in-30-days/blob/master/images/jenkins_ansible_lint_09.png?raw=true)

在這個欄位內，我們可以自由設定我們希望 Jenkins 何時幫我們建置專案。根據專案屬性的不同，我們可以採取不同地定時建置。在 Jenkins 中，我們是採取 [Cron Format](http://www.nncron.ru/help/EN/working/cron-format.htm) 的方式來定義建置規則。

在 `Build Triggers` 下勾選 `Build periodically`：

![jenkins_ansible_lint_10](https://github.com/tsoliangwu0130/learn-ansible-and-jenkins-in-30-days/blob/master/images/jenkins_ansible_lint_10.png?raw=true)

我們可以看到目前我們並沒有定義任何規則，所以除非我們手動操作 Jenkins，不然 Jenkins 並不會主動幫我們進行建置。簡單來說，Cron Format 共分五個欄位，欄位與欄位之間可用空白或 TAB 做區隔：

1. 分 (MINUTE)：0-59 分
2. 時 (HOUR)： 0-23 時
3. 日 (DOM)： 1-31 日
4. 月 (MONTH)：1-12 月
5. 星期 (DOW)：星期 0-7 （其中 0 與 7 都代表星期天） 

除了上述的定義外，我們還可以點擊旁邊的 **?** 標誌來查看更多較彈性的 Cron Format 寫法。舉例而言，若我們希望每隔 15 分鐘就建置一次當前專案，我們可以在定義規則裡填入 `H/15 * * * *`，專案自動建置結果如下：

![jenkins_ansible_lint_11](https://github.com/tsoliangwu0130/learn-ansible-and-jenkins-in-30-days/blob/master/images/jenkins_ansible_lint_11.png?raw=true)

從上面的結果可以看到 Jenkins 自動根據我們定義的規則進行建置：Jenkins 於 7:39 AM 進行第一次建置，15 分鐘後於 7:54 AM 時進行了第二次建置。我們在定義完規則後可以完全撇開這個專案，專心進行其他的工作，而這也正是持續整合最大的好處。除此之外，Jenkins 還提供了許多建置觸發器，例如只當 GitHub 上的專案被修改時才進行建置。Jenkins 並不限制使用者一次只能一次擁有一個建置觸發條件，若有需要，讀者可以自行組合多個建置條件，以達到完整的持續整合目的。
