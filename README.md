# 30 天入門 Ansible 及 Jenkins-CI [![MIT licensed](https://img.shields.io/badge/license-MIT-blue.svg)](LICENSE)

IT 自動化管理及持續整合是 [DevOps](https://zh.wikipedia.org/wiki/DevOps) 領域中扮演著舉足輕重的兩大神兵利器。我將在接下來的三十天內介紹及整合 [Ansible](https://www.ansible.com/) 及 [Jenkins-CI](https://jenkins.io/) 這兩項工具，並與大家分享這一年來我學習自動化組態管理及持續整合的心得。

#### 為什麼需要 IT 自動化？

如果讀者有過任何開發軟體的實務經驗，相信除了開發產品本身的過程中需要花相當多心力外，在產品部署的環節也絕對是令大部分人頭痛的一個部分。其中環境的搭建及參數的設置往往會因為幾個疏忽導致產品無法正常運作。除此之外，一個完整的運作環境通常都不會只是寫幾個簡單的 Shell scipt 或 Python script 就可以輕鬆搭建的。大多數的時候我們還會因為伺服器提供的作業環境不同、或其他種種限制而必須採取適當的調整。因此，IT 自動化 (IT Automation) 在這時候就顯得格外重要。IT 自動化不但可以幫助開發人員在部署產品時確保環境搭建跟開發環境一致，進而有效地減少部署環境時出錯的機率，另外，一個好的自動化工具也可以讓使用者安全地對部屬環境進行升級及維護。

#### 為什麼需要持續整合?

而在環境成功搭建後，對服務本身的維護及監控也是在開發流程中相當重要一環。當我們從原始碼代管服務 (e.g. GitHub) 上取得原始碼後，如何確保產品在發布前品質無虞一直以來都是開發人員需要細思的課題之一。由於現在大多數的開發團隊都會透過版本控制系統來提交並整合開發人員們各自的修改，在合併分支時若衝突沒有處理恰當，往往在產品發布後才會意識到發生了不可預期的錯誤。因此，透過使用持續整合 (Continuous Integration) 的技術，我們將可以在產品發布前定期地做各種測試及分析，以確保軟體每一次的發布更新都是穩定且高品質的。

#### 小結

作為一個 API Developer，因為過去太頻繁地在 API 發布前遇到上述的窘境，所以今年初我花了一些時間摸索了自動化及持續整合系統。在接下來的三十天內，我將會一一介紹如何使用 [Ansible](https://www.ansible.com/) 及 [Jenkins](https://jenkins.io/) 這兩項工具來實現 IT 自動化及持續整合，同時也希望可以將我這一年來摸索的經驗與大家分享。


#### [Update project 2018]

After one year, there are some new features I learned which I think is worth to be documented and some concepts in this GitBook need to be updated/corrected. Refer to this [project](https://github.com/tsoliangwu0130/learn-ansible-and-jenkins-in-30-days/projects) to follow-up related tasks. Feel free to send any pull requests if you have any suggestions :)
