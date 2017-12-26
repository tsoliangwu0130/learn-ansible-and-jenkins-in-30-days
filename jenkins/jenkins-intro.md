# Jenkins 介紹

#### Jenkins 是什麼？

持續整合、持續交付 (CI/CD) 的觀念在近幾年來越來越被開發人員所重視。透過 CI/CD 概念的實踐，我們可以針對每一次產品的修改，或是週期性地對產品進行各種單元 (unit testing) 或整合測試 (integration testing)。若產品發生狀況，我們可以藉此在第一時間內找出發生問題的最接近位置。同時，我們也可以透過持續整合的工具替我們建置 (build) 服務，並在建置完成後產生報表分析或做自動通知等其他的動作。

其中，Jenkins 是目前市面上主流的持續整合工具之一，其前身為昇陽電腦 (Sun Microsystems) 中的 [Hudson](https://zh.wikipedia.org/wiki/Hudson_(%E8%BD%AF%E4%BB%B6)) 專案。身為一個開源專案，Jenkins 有著非常龐大的社群支持，因此 Jenkins 的更新速度也相當快。除此之外，Jenkins 也提供相當多插件來支援不同的專案開發 (e.g. [Gradle](https://gradle.org/), [Grails](https://grails.org/), [Maven](https://maven.apache.org/), etc.)，在使用上可以說是相當容易上手。

#### 如何安裝 Jenkins？

[Jenkins](https://jenkins.io/) 其實已經在官網釋出大部分作業系統的安裝檔，甚至還提供了 [Docker](https://www.docker.com/) 容器 (container) 的映像檔 (image)，因此在手動安裝上可以說是幾乎沒有任何難度。正如同前面提到的，身為開源軟體，Jenkins 的改版速度幾乎每個禮拜就會有一到二個新版本釋出 (weekly release)。除了每週的新版本外，Jenkins 也提供了穩定版本 (Long-Term Support, LTS) 的下載。穩定版本是每十二個月中最穩定的版本，因此，在選擇安裝的版本上可以根據開發團隊的需求挑選適合的版本來進行安裝。

若非使用前一章節透過 Ansible 進行安裝的讀者，在安裝好 Jenkins 後應該會依序看到以下畫面：

1. 使用初始管理密碼 (initial admin password) 解鎖 Jenkins：

    ![](https://github.com/tsoliangwu0130/learn-ansible-and-jenkins-in-30-days/blob/master/images/jenkins-01.png?raw=true)

    讀者可以在 Jenkins 主機裡透過以下指令找到初始管理密碼：

    ```
    $ cat /var/lib/jenkins/secrets/initialAdminPassword
    ```

2. 將密碼複製貼入後，Jenkins 會詢問是否要安裝 plugins。在這裡可以選 Install suggested plugins 就好：

    > 註：之前在[實戰 Ansible](../ansible/practical-ansible.md) 中提到過的 [plugins.txt](https://github.com/tsoliangwu0130/my-ansible/blob/master/roles/docker-jenkins/files/plugins.txt) 就是安裝這裡所有的 suggested plugins。

    ![](https://github.com/tsoliangwu0130/learn-ansible-and-jenkins-in-30-days/blob/master/images/jenkins-02.png?raw=true)

    ![](https://github.com/tsoliangwu0130/learn-ansible-and-jenkins-in-30-days/blob/master/images/jenkins-03.png?raw=true)

3. 待安裝完成後，就會要求使用者建立第一個系統管理員如下圖：

    ![](https://github.com/tsoliangwu0130/learn-ansible-and-jenkins-in-30-days/blob/master/images/jenkins-04.png?raw=true)

4. 設定完成後就可以進入到 Jenkins 主畫面囉：

    ![](https://github.com/tsoliangwu0130/learn-ansible-and-jenkins-in-30-days/blob/master/images/jenkins-05.png?raw=true)

    ![](https://github.com/tsoliangwu0130/learn-ansible-and-jenkins-in-30-days/blob/master/images/jenkins-06.png?raw=true)

讀者有沒有再一次感受到使用 Ansible 實現自動化的美好呢？省去多餘的點擊、來來回回地尋找初始化密碼以及手動輸入一堆資料，只要一鍵，Jenkins 就輕鬆部署啦！
