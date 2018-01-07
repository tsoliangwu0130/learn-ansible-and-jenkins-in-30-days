# Jenkins 插件推薦

在 Jenkins 系列文章的尾聲，筆者在這邊推薦一些比較有名、或是我自己蠻常使用的插件給讀者作為參考，希望可以讀者透過這些插件來打造真正符合自身團隊需求的 Jenkins 環境。

#### [HipChat](https://plugins.jenkins.io/hipchat)

如同前面介紹過的 [Slack Notification](https://plugins.jenkins.io/slack) 這個插件一樣，HipChat 這個插件同樣提供了傳送建置結果至 [HipChat](https://www.stride.com/) 頻道內的功能，對於主要使用 HipChat 作為溝通軟體的團隊來說這個插件絕對是一個不可多得的好物。

#### [JIRA](https://plugins.jenkins.io/jira)

有在使用任務追蹤或是專案管理系統的開發團隊想必對於  [JIRA](https://jira.atlassian.com/) 這套軟體或多或少都略有耳聞，尤其以作為一個[敏捷開發](https://zh.wikipedia.org/wiki/%E6%95%8F%E6%8D%B7%E8%BD%AF%E4%BB%B6%E5%BC%80%E5%8F%91)的工具而言，JIRA 更是箇中翹楚。在 Jenkins 當中，我們可以透過這個插件來將 Jenkins 的持續整合報告與 JIRA 的任務追縱來做整合。

#### [Job Configuration History](https://plugins.jenkins.io/jobConfigHistory)

這個插件可以幫助我們追蹤專案組態的修改歷史，同時也提供類似 [git-diff](https://git-scm.com/docs/git-diff) 的功能來讓我們查看每個版本間的差異。以一個 Jenkins 專案維護者的角度而言這個插件實在是相當實用。

#### [Log Parser](https://plugins.jenkins.io/log-parser)

這個插件可以讓我們透過撰寫 parsing rule file 來自動透過建置作業的輸出結果來更改專案建置結果的狀態。比方來說若建置結果出現 **[Error]** 的字樣，即將該次建置標示為建置失敗。由於有些開發工具即便在建置專案失敗時也不會傳送恰當的 exit code，導致有時候 Jenkins 無法透過 error code 來判斷該次建置是否成功。因此，我們可以簡單透過這個插件來檢查建置的輸出結果，以確認該次專案的建置狀態是否正確。

#### [Role-based Authorization Strategy](https://plugins.jenkins.io/role-strategy)

隨著參與使用 Jenkins 的團隊人數逐漸擴張，要如何有效管控使用者在 Jenkins 上的操作權限也就成了一門學問。這個插件可以讓我們在 Jenkins 上替所有使用者劃分群組角色，並根據不同角色給予特定權限。除了全域劃分外，我們還可以根據特定專案來設置不同的角色權限。
