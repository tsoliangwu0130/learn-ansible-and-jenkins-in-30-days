# 我的第一個 Jenkins Job

在成功進入 Jenkins 管理頁面後，讓我們透過建立我們的第一個簡單工作 (job) 來告訴我們檔案系統的即時使用狀況，並藉此對 Jenkins 有初步的了解：

1. 點擊**新增作業 (new item) **或是**建立新工作 (create new jobs) **來創建 job：

	![](https://github.com/tsoliangwu0130/learn-ansible-and-jenkins-in-30-days/blob/master/images/jenkins-job-01.png?raw=true)

2. 填入 job 的專案名稱並建立專案：

	![](https://github.com/tsoliangwu0130/learn-ansible-and-jenkins-in-30-days/blob/master/images/jenkins-job-02.png?raw=true)

	可以看到在這裡我們有非常多不同的專案類型可以選擇。以筆者目前使用經驗而言，**Free-Style** 以及 **Pipeline** 這兩種類型的專案基本上就涵蓋了大部分的需求。**Free-Style** 類型的專案提供了非常大的彈性讓使用者來做原始碼管理以及建置。如果建置的流程涉及到多個專案，則可以透過 **Pipeline** 類型的專案來組合及定義建置邏輯。值得一提的是最近在 Jenkins 官方釋出了 [Blue Ocean](https://jenkins.io/projects/blueocean/) 這個插件後，Pipeline 專案變得更視覺、更好管理。在未來章節內筆者也會透過範例來逐一介紹不同專案的特色及功能。在這裡選擇 **Free-Style** 專案後點擊 **OK**：

3. 設定專案組態：

	接下來進入專案的組態設定。作為良好的習慣，我會替每個專案加上描述：

	![](https://github.com/tsoliangwu0130/learn-ansible-and-jenkins-in-30-days/blob/master/images/jenkins-job-03.png?raw=true)

	接著，在**建置**的欄位下拉式選單中選擇 `新增建置步驟 > 執行 Shell`：

	![](https://github.com/tsoliangwu0130/learn-ansible-and-jenkins-in-30-days/blob/master/images/jenkins-job-04.png?raw=true)

	並在**指令**欄位中輸入指令 `df -h` 如下圖：

	![](https://github.com/tsoliangwu0130/learn-ansible-and-jenkins-in-30-days/blob/master/images/jenkins-job-05.png?raw=true)

	這裡我們透過了配置**執行 Shell** 這個建置步驟來告訴 Jenkins，未來如果只要這個專案被建置，就執行 `df -h` 這行指令。作為範例介紹，這裡只簡單了使用了一行指令，但讀者當然可以試著在這個欄位裡撰寫更為複雜的 Shell script 作為建置步驟。

	設定完成後點擊**儲存**離開。

4. 專案組態設置完成後，我們可以點擊**馬上建置**來建置剛剛建立好的專案，如果設定上沒有問題，應該會在左下角**建置歷程**這邊看到我們的第一個建置紀錄：

	![](https://github.com/tsoliangwu0130/learn-ansible-and-jenkins-in-30-days/blob/master/images/jenkins-job-06.png?raw=true)

	檢查該建置紀錄，並點擊 **Console Output** 可以看到我們這次的建置結果如下：

	![](https://github.com/tsoliangwu0130/learn-ansible-and-jenkins-in-30-days/blob/master/images/jenkins-job-07.png?raw=true)

	![](https://github.com/tsoliangwu0130/learn-ansible-and-jenkins-in-30-days/blob/master/images/jenkins-job-08.png?raw=true)

	如此一來，我們的第一個 Jenkins Job 就設定完成囉！Jenkins 的確執行了我們設定的指令並成功地將系統的使用狀況呈現在終端機輸出上。回到 Jenkins 的管理頁面，我們現在應該也可以看到我們剛剛設定的專案現在也出現首頁：

	![](https://github.com/tsoliangwu0130/learn-ansible-and-jenkins-in-30-days/blob/master/images/jenkins-job-09.png?raw=true)

以上就是我們第一個簡單的專案建置流程。當然，Jenkins 可以做的遠遠不止這些，在未來的章節內，我們還會透過搭配不同的 plugin 來大幅強化使用 Jenkins，並以此實現完整的持續整合。
