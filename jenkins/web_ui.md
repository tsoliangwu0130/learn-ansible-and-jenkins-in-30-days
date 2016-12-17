# 初覽 Jenkins 介面

在以 admin 的身份透過 `http://localhost:9080` 登入後，我們可以進入 Jenkins 的主要操作介面。一般來說，我們都是透過網路介面進行 Jenkins 的操作。在未來的章節內，若我們有進行 Jenkins 設定，我還是會篇幅介紹如何利用 Ansible 來將相關設定自動配置好。

#### Jenkins 介面基本介紹

在這個章節內，我會簡單依序介紹 Jenkins 介面提供的功能：

![jenkins_05](https://github.com/tsoliangwu0130/learn-ansible-and-jenkins-in-30-days/blob/master/images/jenkins_05.png?raw=true)

1. **New Item**

	在 Jenkins 中，我們會透過建置工作 (job) 來監測追蹤我們的產品專案。我們可以從主控頁面左邊的 `New item` 新增來不同型態的專案。

	![jenkins_new_item](https://github.com/tsoliangwu0130/learn-ansible-and-jenkins-in-30-days/blob/master/images/jenkins_new_item.png?raw=true)

	一般來說，比較常用到的會是 `Free style project` 以及 `Pipeline` 這兩種類型，我們會在未來的章節做更詳細的介紹。

2. **People**

	在 `People` 這個標籤底下我們可以瀏覽所有 Jenkins 成員的活動情形。

	![jenkins_people](https://github.com/tsoliangwu0130/learn-ansible-and-jenkins-in-30-days/blob/master/images/jenkins_people.png?raw=true)

	由於我們只有創建過 admin 這個角色，所以目前在這個頁面下只能瀏覽 admin 的操作記錄。

3. **Build History**

	在 `Build History` 中，我們可以瀏覽 Jenkins 中各個專案的建置紀錄。

	![jenkins_build_history](https://github.com/tsoliangwu0130/learn-ansible-and-jenkins-in-30-days/blob/master/images/jenkins_build_history.png?raw=true)

	除了透過網頁瀏覽建置紀錄，我們也可以在這個頁面下將建置紀錄輸出成 XML 的格式作為分析使用。

4. **Manage Jenkins**

	這是 Jenkins 中相當重要的一個管理頁面，一般而言只有 admin 的角色有權利存取這個頁面。

	![jenkins_manage_jenkins](https://github.com/tsoliangwu0130/learn-ansible-and-jenkins-in-30-days/blob/master/images/jenkins_manage_jenkins.png?raw=true)

	我們可以在 `Manage Jenkins` 的子頁面下進行諸多功能，從細節配置、安裝插件、憑證管理到使用者的權限劃分等等，都是在 `Manage Jenkins` 的頁面下進行操作。由於在這個頁面下涵蓋的功能十分廣泛，我們會在未來操作到的時候再進行各自更詳細的介紹。

5. **My Views**

	這個頁面非常類似於一開始的首頁，我們可以在 `My Views` 的頁面下觀看目前所有運行中專案的狀態。

	![jenkins_my_view](https://github.com/tsoliangwu0130/learn-ansible-and-jenkins-in-30-days/blob/master/images/jenkins_my_view.png?raw=true)

6. **Credentials**

	在 `Credentials` 頁面下管理著所有 Jenkins 的相關憑證設定。

	![jenkins_credentials](https://github.com/tsoliangwu0130/learn-ansible-and-jenkins-in-30-days/blob/master/images/jenkins_credentials.png?raw=true)

	Jenkins 作為一個持續整合的服務，不可避免的是常會需要設置一些憑證設定，以存取諸如原始碼託管服務的權限，因此，在這個頁面下我們可以非常清楚地對所有設定過的憑證進行管理。同樣地，這個頁面的存取權限通常也僅限於 admin 使用者。
