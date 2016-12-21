# 使用 Jenkins 維護 Ansible GitHub Repository

接下來，我們可以開始試著用 Jenkins 替我們維護放在原始碼代管服務上真正的專案。為了介紹方便，我將以我個人的 [GitHub repository](https://github.com/tsoliangwu0130/tsoliang-ansible) 作為範例。

#### 新建 ansible_lint 工作專案

進入 Jenkins 網頁介面，並新增一個名為 `ansible_lint` 的 Freestyle 專案。

1. 在 `General` 標籤下輸入專案細節。詳細的專案描述有助於其他參與的開發人員能在最短時間內了解這個專案的內容。

	![jenkins_ansible_lint_01](https://github.com/tsoliangwu0130/learn-ansible-and-jenkins-in-30-days/blob/master/images/jenkins_ansible_lint_01.png?raw=true)

2. 在 `Source Code Management` 中，選擇 `Git` 做為我們的版本控制系統，並設定 GitHub 專案的細節。必須注意的是，在 `Repository` 下的 `Repository URL` 並非是網址列顯示的網址。我們可以在 GitHub 專案上點擊 `Clone or download` 的連結得到 repo 的 SSH / HTTPS URL：

	 ![jenkins_ansible_lint_02](https://github.com/tsoliangwu0130/learn-ansible-and-jenkins-in-30-days/blob/master/images/jenkins_ansible_lint_02.png?raw=true)

	將 GitHub Repository 連結貼入 Jenkins 專案中如下：

	![jenkins_ansible_lint_03](https://github.com/tsoliangwu0130/learn-ansible-and-jenkins-in-30-days/blob/master/images/jenkins_ansible_lint_03.png?raw=true)

	這時候我們會發現 Jenkins 馬上跳出了紅色的警告訊息。訊息告訴我們還沒有權限存取這個 Repository (Permission denied)，這是因為我們的遙控節點還沒有產生跟 GitHub 帳號配對的 SSH 密鑰。所以，我們必須登入我們的遙控節點，並新增一組新的 SSH 密鑰，並加至 GitHub 帳號中。因為本篇教學的重點並非 SSH，礙於篇幅關係，請讀者自行跟著 [GitHub 官方教學](https://help.github.com/articles/generating-an-ssh-key/)操作新增 SSH 密鑰即可。配對成功後，警告訊息就會消失如下圖所示：

	![jenkins_ansible_lint_04](https://github.com/tsoliangwu0130/learn-ansible-and-jenkins-in-30-days/blob/master/images/jenkins_ansible_lint_04.png?raw=true)

3. 接著，在 `Build` 中新增一個 `Execute shell` 的建置步驟，因為我們在前面的章節透過 Ansible 將 Ansible-lint 這套語法檢查器安裝在我們的 Jenkins 伺服器上了，因此我們可以直接使用命令列操作 Ansible-lint 來檢查當前 repository 中的所有 roles：

	![jenkins_ansible_lint_05](https://github.com/tsoliangwu0130/learn-ansible-and-jenkins-in-30-days/blob/master/images/jenkins_ansible_lint_05.png?raw=true)

4. 設置好後儲存專案，並回到專案頁面。我們可以看到現在左邊的導覽列出現了一個 GitHub 的小標示。這是因為我們在一開始就將這個專案設置成 GitHub 專案了，如果我們點擊該標示就會回到專案在 GitHub 上的[原始位置](https://github.com/tsoliangwu0130/learn-ansible-and-jenkins-in-30-days)，這樣當 Jenkins 維護的專案數目多起來以後，開發人員在維護時也能夠非常清楚地分辨現在這個工作專案的原始專案是哪一個。

	![jenkins_ansible_lint_06](https://github.com/tsoliangwu0130/learn-ansible-and-jenkins-in-30-days/blob/master/images/jenkins_ansible_lint_06.png?raw=true)

5. 另外，若點擊畫面中央的 `Workspace` 連結，我們可以看到這個 Jenkins 專案的工作目錄細節：

	![jenkins_ansible_lint_07](https://github.com/tsoliangwu0130/learn-ansible-and-jenkins-in-30-days/blob/master/images/jenkins_ansible_lint_07.png?raw=true)

	這裡顯示的內容與遙控節點下的 `/var/lib/jenkins/workspace/ansible_lint` 並無二致。透過網路介面，我們可以在每一次運行完 Jenkins 的專案後進入 workspace 檢查是否有敏感資料遺留。

6. 最後，在 Jenkins 中運行這個專案：

	![jenkins_ansible_lint_08](https://github.com/tsoliangwu0130/learn-ansible-and-jenkins-in-30-days/blob/master/images/jenkins_ansible_lint_08.png?raw=true)

	我們可以在 `Console Output` 中看到 Jenkins 運行結果，Jenkins 正確地從 GitHub 原始位置上將專案複製 (clone) 至工作目錄，並利用 Ansible-lint 逐一檢查目錄下的每一個 role。我故意在 `roles/jenkins/handlers/main.yml` 中留了一個後綴空白，Ansible-lint 也可以正確地找到該行錯誤，並將這次的建置標示為失敗。

	_注意：因為 `roles/jenkins/handlers/main.yml` 在 `jenkins` 與 `my_first_jenkins_job` 中都有被呼叫到，Ansible-lint 會依序跟著每個 role 的定義邏輯走過一次，所以才會在 `Console Output` 中輸出兩次同樣的 `ANSIBLE0002` 錯誤。在這裡是為了避免寫太過複雜的語法導致讀者模糊焦點。一般來說，我會寫一個簡單的 Shell script 或是 Python script 來整理 Ansible-lint 的輸出內容，同時除了檢查 role 以外也會檢查所有的 playbook。_
