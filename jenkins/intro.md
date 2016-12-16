# Jenkins 介紹

#### Jenkins 是什麼？

持續整合的觀念在近幾年來越來越被開發人員所重視。透過持續整合的實現，我們可以針對每一次產品的修改，或是週期性地對產品進行各種單元 (unit testing)、整合測試 (integration testing) 以確保版本控制系統上的原始碼隨時可用。同時，我們也可以透過持續整合的工具替我們建置 (build) 軟體服務，並在建置完成後產生報表分析或其他通知的動作。

其中，Jenkins 是目前市面上主流的持續整合代表服務 CI (Continuous Integration) 服務之一，其前身為昇陽電腦 (Sun Microsystems) 中的 [Hudson](https://zh.wikipedia.org/wiki/Hudson_(%E8%BD%AF%E4%BB%B6)) 專案。身為一個開源專案，Jenkins 有著非常龐大的社群支持，因此 Jenkins 的更新速度也相當快。除此之外，Jenkins 也提供相當多插件來支援不同的專案開發 (e.g. [Gradle](https://gradle.org/), [Grails](https://grails.org/), [Maven](https://maven.apache.org/), etc.)，在使用上可以說是相當容易上手。

#### 如何安裝 Jenkins？

[Jenkins](https://jenkins.io/)其實已經在官網釋出大部分作業系統的安裝檔，甚至還提供了 [Docker](https://www.docker.com/) 容器 (container) 的映像檔 (image)，因此在手動安裝上可以說是幾乎沒有任何難度。正如同前面章節提到的，身為開源軟體，Jenkins 的改版速度幾乎每個禮拜就會有一到二個新版本釋出 (weekly release)。除了每週的新版本外，Jenkins 也提供了穩定版本 (Long-Term Support, LTS) 的下載。穩定版本是每十二個月中最穩定的版本，因此，在選擇安裝的版本上可以根據開發團隊的需求挑選適合的版本來進行安裝。

我在之前的章節中已經簡單地介紹了如何使用 Ansible 來安裝 Jenkins 至我們的遙控主機上，接下來，我會繼續沿用這套 playbook / role 來進行 Jenkins 的教學，並繼續強化我們的 `jenkins` role。