# 使用參數建置專案

由於 Jenkins 可以替我們定時建置專案，但如果建置過程會產生建置產物 (artifact)，我們往往會在 Jenkins 的伺服器端產生大量名稱類似的產物，這對部署的人員會產生一定程度的困擾。因此，我們可以使用 Jenkins 的環境變數或是建置參數參與建置過程，甚至我們命名管理建置產物。

#### 參數化建制專案

在 Jenkins 中，我們可以在每一次的專案建置之前提供開發人員不同的建置選項以便管理專案。舉例來說，如果我們的產品在正式上線前要先經過開發階段 (Dev) 的測試，若順利通過才可以部署到正式產品的伺服器上 (Prod)，我們可以在建置過程中加入一些客製化參數讓不同階段的產物有獨特的名字容易區分。

回到 `ansible_lint` 的專案配置，在 `General` 的欄位下勾選 `This project is parameterized` 的選項並點選 `Add Parameter`：

![parameterize_job_01](https://github.com/tsoliangwu0130/learn-ansible-and-jenkins-in-30-days/blob/master/images/parameterize_job_01.png?raw=true)

我們可以看到 Jenkins 提供非常多種參數化專案的方法，讀者可以依照符合需求選擇適合的選項。作為示範，選擇 `Choice Parameter` 如下並配置如下：

![parameterize_job_02](https://github.com/tsoliangwu0130/learn-ansible-and-jenkins-in-30-days/blob/master/images/parameterize_job_02.png?raw=true)

在 `Choices` 的選項中我們提供了 `Dev` 與 `Prod` 兩種不同的選項給建置人員選擇使用，但由於我們在配置的過程中還不知道到時候配置人員會選擇哪一個參數作為建置選項，所以我們可以將這個參數命名為 `STAGE`，並在接下來的建置過程中用 `$STAGE` 的方式操作這個參數。另外，同樣我們盡可能在所有配置的過程加入詳細的敘述，以免造成未來開發人員的困擾。

接著，在 `Build` 的欄位中稍微調整一下建置腳本如下：

![parameterize_job_03](https://github.com/tsoliangwu0130/learn-ansible-and-jenkins-in-30-days/blob/master/images/parameterize_job_03.png?raw=true)

我們現在將 Ansible-lint 的檢查結果儲存在一個 .txt 的檔案，並將檔名根據我們建置時選擇的參數以及當前系統時間做命名分類。設定完成後儲存變更，我們可以看到現在建置專案的頁面已經改變：

![parameterize_job_04](https://github.com/tsoliangwu0130/learn-ansible-and-jenkins-in-30-days/blob/master/images/parameterize_job_04.png?raw=true)

由於專案的建置方式已經被參數化，所以我們必須在建置時提供一個參數給本專案。現在，讓我們試著用 `Prod` 作為參數建置專案。下拉式選單點開後，選擇 `Prod` 然後點選 `Build`。建置完成後，點選選單列的 `Workspace` 觀察 artifact 是否有正確被命名：

![parameterize_job_05](https://github.com/tsoliangwu0130/learn-ansible-and-jenkins-in-30-days/blob/master/images/parameterize_job_05.png?raw=true)

我們可以看到 Jenkins 在專案工作目錄下根據我們選擇的參數及伺服器時間產生了該次專案的 artifact。所以，就算 Jenkins 會持續自動地幫我們重複整合原始碼，我們也可以大量地運用這個技巧來管理專案。
