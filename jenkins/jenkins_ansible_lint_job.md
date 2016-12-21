# 使用 Jenkins 維護 Ansible Git Repo

接下來，我們可以開始試著用 Jenkins 替我們維護放在原始碼代管服務上真正的專案。為了介紹方便，我將以我個人的 [GitHub repository](https://github.com/tsoliangwu0130/tsoliang-ansible) 作為範例。

#### 新建 Jenkins 工作專案

進入 Jenkins 網頁介面，並新增一個名為 `ansible_lint` 的 Freestyle 專案。

1. 在 `General` 標籤下輸入專案細節。詳細的專案描述有助於其他參與的開發人員能在最短時間內了解這個專案的內容。

	![jenkins_ansible_lint_01](https://github.com/tsoliangwu0130/learn-ansible-and-jenkins-in-30-days/blob/master/images/jenkins_ansible_lint_01.png?raw=true)