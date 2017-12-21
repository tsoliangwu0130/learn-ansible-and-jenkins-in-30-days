# Ansible 的最佳實踐

#### 什麼是最佳實踐 (Best Practice)？

因為 Ansible 給予開發者那麼大的彈性，隨著參與開發的人數增加，或者專案規模的擴大，若沒有一個參考的標準，很容易產生因為多種撰寫風格的混淆或是結構過於鬆散等因素，最後導致專案最後走向難以維護死胡同裡的情況。其實，現今大部分的程式語言、框架或者開發工具都會提供一套最佳實踐 (best practice) 的文件給開發者們作為設計參考，當然，身為熱門開源專案的 Ansible 也不例外。

然而，我並不認為所謂的 best practice 是用來限制開發人員都不可以有任何彈性的緊箍咒。根據背景的不同，每個開發團隊本來就有可能有屬於自己的一套風格。Best practice 只是某種程度上提供了一個建議，來讓不同的開發團隊都可以在最快的時間內建立出最有效且有一定品質的產品。就算是單人工作者，也可以透過 best practice 的概念來迅速掌握網路上不同專案的邏輯及開發目的。

#### Ansible 的最佳實踐

在 Ansible 官方提供的 [best practice 文件](http://docs.ansible.com/ansible/latest/playbooks_best_practices.html)中，從專案架構、inventory file 的撰寫到 task 之間是否需要斷行都有描述。我會建議讀者可以花時間稍微瀏覽一下，這對未來 Ansible 設計的掌握會很有幫助。接下來，我會用我在自己的 GitHub 上的一個[小專案](https://github.com/tsoliangwu0130/my-ansible)來介紹怎麼使用 Ansible 的最佳實踐來設計自己的 Ansible 專案。

#### 專案架構

一般來說，大部分的 Ansible 專案都蠻常使用 best practice 的 [directory layout](http://docs.ansible.com/ansible/latest/playbooks_best_practices.html#directory-layout) 來做為專案結構的參考。（其中 Ansbile 也提供了另一種風格 - [alternative directory layout](http://docs.ansible.com/ansible/latest/playbooks_best_practices.html#alternative-directory-layout)，與 directory layout 主要差異在於 inventory file 的結構。如果在不同環境下的共用環境變數較少的話，使用這種結構會相對好管理。）

對比於我自己在 GitHub 上的專案的結構：

```
my-ansible/
├── LICENSE
├── README.md
├── Vagrantfile
├── ansible.cfg
|
├── inventory
|
├── docker-jenkins.yml
├── jenkins-ansible-lint.yml
├── playbook_example.yml
|
└── roles/
    ├── docker/
    ├── docker-jenkins/
    │   ├── defaults/
    │   │   └── main.yml
    │   ├── files/
    │   │   └── plugins.txt
    │   ├── meta/
    │   │   └── main.yml
    │   ├── tasks/
    │   │   ├── dependencies.yml
    │   │   ├── main.yml
    │   │   └── setup.yml
    │   └── templates/
    │       ├── Dockerfile.j2
    │       └── security.groovy.j2
    ├── jenkins-ansible-lint/
    └── pip/
```

* 根目錄
    * 舉凡 `LICENSE`, `README.md`, `Vagrantfile` 以及 `ansible.cfg` 這類的說明、設定檔案，我都習慣將他們直接放在根目錄下。
    * 另外，由於在這個專案內我只有一個 inventory file，所以我也直接將其擺放在根目錄下。
    * 除此之外，這個 Ansbile 專案內，我還放了三份不同的 playbook。
* `roles/`

    在這裡我存放了所有跟這個專案有關的 roles。由於每個 role 的結構類似，所以我以其中的 `docker-jenkins` 這個 role 來做簡單介紹：

    * `defaults/`

        在這個資料夾下，通常被用來定義優先度較低的變數。習慣上我會將只跟這個 role 相關的變數放在 `vars/` 的資料夾下來做使用目的上的區分。

    * `files/`

        這個資料夾內通常存放著這個 role 在部署過程中會需要使用或執行的檔案。

    * `templates/`

        由於 Ansible 是用 Python 開發的一個自動化工具，所以也原生支援 `Jinja2` 這套模版引擎 (templating engine)。與 `files/` 下的檔案不同，這個資料夾下的檔案通常都以 `.j2` 作為副檔名，並會根據執行環境、傳遞參數等等的不同，來對 template file 做某種程度上調整。

    * `tasks/`

        整個 role 中最核心的部分。這裡面定義了這個 role 的運行邏輯以及部署任務。在這個資料夾下可以不只有一個檔案，然後在 `main.yml` 裡呼叫其他檔案。

    * `meta/`

        `meta/` 資料夾內定義了這個 role 的依賴 (dependencies) 關係。以這個 `docker-jenkins` 為例，`docker` 這個 role 就是其依賴。在運行時，會先運行 `meta/` 內定義的 dependencies 才接著執行 role 本身的任務。我們可以充分利用這個特色來重複組合及利用已經定義好的 role，避免[重造輪子](https://zh.wikipedia.org/wiki/%E9%87%8D%E9%80%A0%E8%BD%AE%E5%AD%90)。

    * `handlers/`

        我們會將 [handlers](http://docs.ansible.com/ansible/latest/playbooks_intro.html#handlers-running-operations-on-change) 事件存放在這個目錄下。雖然這個專案並沒有使用到任何 handler，但 handler 是 Ansible 中非常好用的一個功能。我們可以根據 Ansible 運行時 tasks 狀態的改變來通知 (notify) handlers。最重要的特色是，無論有多少個 task notify 同一個 handler，該 handler 都只會運行一次。非常常見的 handler 的實踐之一就是在檔案被修改後重啟 services。

大致上來說，這就是一個專案基本上比較常見的結構。隨著專案規模的擴展，往往複雜程度也會隨之提高。因此維持一個好的習慣，對於開發者管理專案，以及幫助其他團隊成員理解設計邏輯有相當大的好處。
