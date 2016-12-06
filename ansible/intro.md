# Ansible 介紹

#### Ansible 是什麼？

相信大家應該都有過重灌電腦的經驗。每一次在電腦重新安裝後，我們都需要花大量的時間把平常常用的軟體一一重新裝上，有些軟體可能還要進行個別的微調，而這樣繁瑣的步驟同樣也會發生在軟體部署的伺服器上。為了運行開發出來的軟體，開發人員們往往需要要花費大量的時間在伺服器上安裝所有所需的套件 (packages) 或是服務依賴 (dependency)，而在安裝的過程中若有疏忽，還很有可能造成部署程式發生無預期的錯誤。因此，自動化就是因為這樣的需求產生的一種概念。若我們可以將每一次部署的步驟寫成一個自動化的標準作業程序 (Standard Operating Procedures, SOP)，除了可以有效縮短每次的部署時間及降低出錯率外，在未來需要升級部署環境，也會相對容易許多。而 [Ansible](https://www.ansible.com/) 就是目前業界最常使用的自動化工具之一。

#### 為什麼要選擇 Ansible？

目前業界中主流的自動化部署工具除了 Ansible 之外還有 [Chef](https://www.chef.io/chef/)、[Otter](http://inedo.com/otter)、[Puppet](https://puppet.com/)、[SaltStack](https://saltstack.com/) 等等。每一個陣營都有大量的擁護者以及各自的優缺點，其實在這個問題上並沒有誰絕對優於誰的答案。我之所以會選擇 Ansible 作為自動化的工具，主要原因是因為 Ansible 真的容易學習許多。在一開始接觸自動化的過程中，其實我最先學習的是 Chef 這套工具。雖然 Chef 的確也是一個功能相當完善且強大的工具，但說實話這套軟體的學習曲線相較於 Ansible 真的是陡峭蠻多的。在選擇工具的過程中，我相信最重要的考量應該在於我們能不能夠在最短的時間內掌握這個工具並有效地解決我們所遇到的問題。在這樣的前提下，Ansible 就成為了我們開發團隊的首選了。除了容易學習之外，Ansible 本身在 [GitHub](https://github.com/ansible) 上也是一個開源的專案。有了大量開發人員的幫助及回饋，Ansible 這套軟體的成長速度其實是真的相當驚人的。

#### Ansible 的需求

由於 Ansible 是使用 Python 開發的一套免費軟體，要透過 Ansible 在遙控節點 (remote node) 上配置環境，唯二的條件只有：

1. 安裝 Python (2.6 版或更新)
2. 透過 [SSH](https://en.wikipedia.org/wiki/Secure_Shell) 進行連線

在現在大部分的 Linux 主機上，基本上 Python 已經都是基本配備了，所以這樣的要求等於沒有要求。我們只需要在安裝好 Ansible 的主機 (control machine) 上透過 SSH 與遙控節點溝通，就可以輕鬆做到一鍵部署啦！