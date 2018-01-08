# Jenkins 插件推薦

在 Jenkins 系列文章的尾聲，筆者在這邊推薦一些比較有名、或是我自己蠻常使用的插件給讀者作為參考，希望可以讀者透過這些插件來打造真正符合自身團隊需求的 Jenkins 環境。

#### [Ansible](https://plugins.jenkins.io/ansible)

可以在 Jenkins 的建置步驟中使用 Ansible 的插件。除了使用 Ansible 來部署 Jenkins 環境外，我們同樣也可以透過這個插件讓 Jenkins 操作 Ansible 對目標伺服器進行自動化部署。

#### [Blue Ocean](https://plugins.jenkins.io/blueocean)

筆者在 [Pipeline 章節](https://tsoliangwu0130.gitbooks.io/learn-ansible-and-jenkins-in-30-days/content/jenkins/jenkins-pipeline.html)中介紹過的插件。Blue Ocean 除了重新設計了 Jenkins 的操作介面外，在 Pipeline 專案開發流程的優化上也有相當不錯的表現。若有使用 Pipeline 專案需求的團隊，Blue Ocean 絕對是一個值得一試的選項。

#### [Conditional BuildStep](https://plugins.jenkins.io/conditional-buildstep)

這個插件可以讓我們替專案的建置步驟加上觸發建置條件，例如檢查工作目錄下是否有某檔案存在，當存在時進行 A 建置步驟，若否，則進行 B 建置步驟。在使用上相當直覺也相當方便的一個插件。

#### [Docker](https://plugins.jenkins.io/docker-plugin)

隨著容器技術漸漸成為業界主流，使用 Jenkins 來操作容器也成為一個必要的需求。這個插件提供了 Docker 使用者相當完善的使用介面，無論是 Docker 映像檔的建立，或是 Docker 容器的運行管理都可以透過這個插件在建置流程中來進行操作。

#### [Email Extension](https://plugins.jenkins.io/email-ext)

在[建置後動作](https://tsoliangwu0130.gitbooks.io/learn-ansible-and-jenkins-in-30-days/content/jenkins/jenkins-post-build-actions.html)章節中曾經介紹過的插件。這個插件更近一步擴充了 Jenkins 原生 Email 通知的使用彈性。除了依據專案的劃分來設計不同的收件人清單、通知郵件模板外，也可以根據專案的建置狀態的不同來傳送更精準的 Email 通知。

#### [Environment Injector](https://plugins.jenkins.io/envinject)

這個插件可以讓我們輕鬆控制及管理建置流程中的各個環境變數，並橫跨不同的建置階段。舉例來說，我們可以將從 SCM 提取的 commit number 存入一個環境變數中，待建置結束後，再將這個 commit number 作為建置後輸出報告的變數使用。除此之外，在每一次建置作業內，我們都可以查看該次建置作業使用的環境變數分別有哪些。筆者個人認為這算是 Jenkins 必裝的插件之一。

#### [HipChat](https://plugins.jenkins.io/hipchat)

如同前面介紹過的 [Slack Notification](https://plugins.jenkins.io/slack) 這個插件一樣，HipChat 這個插件同樣提供了傳送建置結果至 [HipChat](https://www.stride.com/) 頻道內的功能，對於主要使用 HipChat 作為溝通軟體的團隊來說這個插件絕對是一個不可多得的好物。

#### [JIRA](https://plugins.jenkins.io/jira)

有在使用任務追蹤或是專案管理系統的開發團隊想必對於 [JIRA](https://jira.atlassian.com/) 這套軟體或多或少都略有耳聞，尤其以作為一個[敏捷開發](https://zh.wikipedia.org/wiki/%E6%95%8F%E6%8D%B7%E8%BD%AF%E4%BB%B6%E5%BC%80%E5%8F%91)的工具而言，JIRA 更是箇中翹楚。在 Jenkins 當中，我們可以透過這個插件來將 Jenkins 的持續整合報告與 JIRA 的任務追縱來做整合。

#### [Job Configuration History](https://plugins.jenkins.io/jobConfigHistory)

這個插件可以幫助我們追蹤專案組態的修改歷史，同時也提供類似 [git-diff](https://git-scm.com/docs/git-diff) 的功能來讓我們查看每個版本間的差異。以一個 Jenkins 專案維護者的角度而言這個插件實在是相當實用。

#### [Log Parser](https://plugins.jenkins.io/log-parser)

這個插件可以讓我們透過撰寫 parsing rule file 來自動透過建置作業的輸出結果來更改專案建置結果的狀態。比方來說若建置結果出現 `[Error]` 的字樣，即將該次建置標示為建置失敗。由於有些開發工具即便在建置專案失敗時也不會傳送恰當的 exit code，導致有時候 Jenkins 無法透過 error code 來判斷該次建置是否成功。因此，我們可以簡單透過這個插件來檢查建置的輸出結果，以確認該次專案的建置狀態是否正確。

#### [Multijob](https://plugins.jenkins.io/jenkins-multijob-plugin)

這個插件允許我們建立 **MultiJob** 類型的專案。有點類似於 Pipeline 專案，我們也可以在這種類型的專案下替建置作業設置建置階段，每個建置階段下可以有多個建置作業，而在每一個建置階段完成後才會進入下一個建置階段。不同的是，在這種類型專案下的建置作業是彼此獨立的，在建置作業運行時是採取平行建置的狀態，彼此之間並不會互相依賴。

#### [Publish Over SSH](https://plugins.jenkins.io/publish-over-ssh)

筆者同樣在[建置後動作](https://tsoliangwu0130.gitbooks.io/learn-ansible-and-jenkins-in-30-days/content/jenkins/jenkins-post-build-actions.html)章節內有介紹到的插件。這個插件可以允許我們將建置後產物透過 SSH 傳送至目標伺服器上，對於持續部署或持續交付的實踐上是相當實用的一個插件。

#### [Role-based Authorization Strategy](https://plugins.jenkins.io/role-strategy)

隨著參與使用 Jenkins 的團隊人數逐漸擴張，要如何有效管控使用者在 Jenkins 上的操作權限也就成了一門學問。這個插件可以讓我們在 Jenkins 上替所有使用者劃分群組角色，並根據不同角色給予特定權限。除了全域劃分外，我們還可以根據特定專案來設置不同的角色權限。
