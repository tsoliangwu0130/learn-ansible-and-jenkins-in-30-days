# 使用 View 管理 Jenkins 工作專案

在 Jenkins 章節的尾聲，最後介紹一個使用 Jenkins 的視景 (view) 管理工作專案的小技巧給讀者。當專案數目隨著時間增加時，透過 view 的分類可以幫助我們在瀏覽不同專案時更有效率。

#### 新建 Ansible View

在 Jenkins 的管理主頁面下，點選專案列表上面的 `+` 標籤新增專案分類如下：

![jenkins_view_01](https://github.com/tsoliangwu0130/learn-ansible-and-jenkins-in-30-days/blob/master/images/jenkins_view_01.png?raw=true)

接著，根據需求調整 view 的細節，並將指定專案分類在該 view 分類下：

![jenkins_view_02](https://github.com/tsoliangwu0130/learn-ansible-and-jenkins-in-30-days/blob/master/images/jenkins_view_02.png?raw=true)

儲存變更後回到主頁，我們可以看到現在多了一個 `Ansible` 的 view 分類：

![jenkins_view_03](https://github.com/tsoliangwu0130/learn-ansible-and-jenkins-in-30-days/blob/master/images/jenkins_view_03.png?raw=true)

當 Jenkins 管理的工作專案有一定數量後，我們常常會在專案列表內被大量的專案搞得暈頭轉向。善用 view 來管理 Jenkins 的各個專案，並根據需求給予適當的說明分類，將有效地幫助我們在操作 Jenkins 及進行持續整合的工作管理時更有系統及效率。