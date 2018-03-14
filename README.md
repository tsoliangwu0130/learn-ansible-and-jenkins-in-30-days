# 30 天入門 Ansible 及 Jenkins [![MIT licensed](https://img.shields.io/badge/license-MIT-blue.svg)](LICENSE)

在軟體開發領域中，IT 自動化 (automation) 及持續整合 (continuous integration, CI) 是 [DevOps](https://zh.wikipedia.org/wiki/DevOps) 精神中相當重要的兩個部分。尤其當團隊開始導入敏捷開發 (agile development) 等概念時，這兩項技能往往可以讓實踐更加事半功倍。作為入門手冊，我將會在接下來 30 天內透過 [Ansible](https://www.ansible.com/) 與 [Jenkins](https://jenkins.io/) 這兩個非常熱門的開源軟體來分別介紹上述的概念。

#### 為什麼需要 IT 自動化？

如果讀者有過任何開發軟體的實務經驗，相信除了開發產品本身的過程中需要花相當多心力外，在產品部署的環節也絕對是令大部分人頭痛的一個部分。其中環境的搭建及參數的設置往往會因為幾個疏忽導致產品無法像在開發時一樣正常運作。除此之外，一個完整的運作環境通常都不會只是寫幾個簡單的 Shell script 就可以輕鬆搭建，尤其當需要部署的主機不只一台的時候，重複性的工作更是會花費我們大量的時間。大多數的時候我們還會因為伺服器提供的作業環境不同、或其他種種限制而必須採取適當的調整。因此，IT 自動化在這時候就顯得格外重要。透過 IT 自動化，不但可以幫助開發人員有效減少部署產品所需時間外，還可以在有限度的修改下分別針對不同部署環境做相當程度的彈性調整，進而將時間有效節剩下來，並讓開發人員專注在更重要的事項上。

#### 為什麼需要持續整合？

而在環境成功搭建後，對服務本身的維護及監控也是在開發流程中相當重要一環。當我們從原始碼代管服務 (e.g. GitHub) 上取得原始碼後，如何確保產品在發布前品質無虞，一直以來都是開發人員需要細思的課題之一。由於現在大多數的開發團隊都會透過版本控制系統來提交並整合開發人員們各自的修改，若在合併分支時沒有將合併衝突 (conflict) 處理恰當，或是合併程式碼後產生了某些邏輯上的隱性地雷，往往會到產品發布以後才意識到發生了不可預期的錯誤。在持續整合，甚至是持續交付 (continuous delivery, CD)、持續部署 (continuous deployment, CD) 的機制下，我們可以透過高頻的整合、測試並分析程式碼品質，在最短的時間內發現問題及其發生點，進而確保確保產品每一次的發布都是穩定且高品質的。

***

**《 30 天入門 Ansible 及 Jenkins 》** 系列文章同步更新於：

* [GitBook]( https://www.gitbook.com/book/tsoliangwu0130/learn-ansible-and-jenkins-in-30-days/)
* [GitHub]( https://github.com/tsoliangwu0130/learn-ansible-and-jenkins-in-30-days)
* [2018 iT 邦幫忙鐵人賽](https://ithelp.ithome.com.tw/users/20103346/ironman/1473)
