# 撰寫第一個 Playbook

#### 什麼是 Ansible Playbook？

當確認 Ansible 已經正確地安裝在控制主機上之後，我們現在就可以準備透過 Ansible 對遙控節點進行部署了。然而，我們要如何告訴 Ansible 我們想要對遙控節點做些什麼呢？在 Ansible 的世界裡，我們是透過編寫劇本 (playbook) 來告訴 Ansible 接下來需要做的事項。由於 Ansible 的劇本是用 [YAML (YAML Ain't Markup Language)](http://yaml.org/) 這種語言來撰寫，而這個語言最大的特色就是具有高度可讀性，因此無論有沒有程式語言的基礎，理論上任何人在看到一份好的 Ansible playbook 的時候應該都是非常容易理解及著手修改維護的。

#### 我的第一個 Playbook

以下先用一個非常簡單的範例來介紹 playbook 的運作方式。我們先創建一個 playbook 檔案，將其命名為 `playbook.yml` 並置於工作目錄下 (e.g. `workspace/playbook.yml`)。接著，開啟檔案並輸入以下內容：

```yaml
---
- hosts: ironman
  tasks:
    # task 1
    - name: test connection
      ping:
      register: message

    # task 2
    - name: print debug message
      debug:
        msg: "{{ message }}"
```

相信大家就算從來沒有寫過 Ansible 的 playbook，應該還是不難猜出這份 playbook 在做些什麼。簡單來說，我們在這份 playbook 中定義了任務清單 `tasks` 以及部署對象 `hosts: ironman`。這也是為什麼在之前的章節中我們提到了最好把每一個虛擬主機都做命名。主機命名後，我們可以直接透過主機的名稱直接進行操作（我們會在接下來的章節中提到若不透過 Vagrant 要如何命名主機）。除此之外，我們可以透過 `name` 這個標籤替任務清單中的每個 task 分別命名。在接下來運行 playbook 的過程中若發生錯誤，我們也會比較清楚是在哪一個環節上出了問題。

在這個 playbook 中，我們定義了兩個 task：

1. test connection

	我們在這個 task 中，呼叫了 Ansible 的內建測試模組 (module) - [ping](http://docs.ansible.com/ansible/ping_module.html)。這個模組的目的非常類似在學習每個語言一開始練習的 ["Hello World"](https://zh.wikipedia.org/wiki/Hello_World) 程式。其主要是用來測試操控主機是否可以正確地與遙控節點進行溝通，如果連線正常，遙控節點就會回傳一個 "pong" 的訊息給控制主機。接著，我們透過另一個 Ansible 內建模組 - [register](http://docs.ansible.com/ansible/playbooks_variables.html#registered-variables) 把遙控節點回傳的訊息存成變數 (variable) 的形式。

	_注意：此處的 "ping" 並非建立在 [ICMP 協定](https://zh.wikipedia.org/wiki/%E4%BA%92%E8%81%94%E7%BD%91%E6%8E%A7%E5%88%B6%E6%B6%88%E6%81%AF%E5%8D%8F%E8%AE%AE)下的 "[ping](https://zh.wikipedia.org/wiki/Ping)" 指令，只是純 Ansible 開發的一個簡單測試模組。_

2. print debug message

	在上一個 task 中，我們已經把 ping 後的結果存在 `message` 這個變數中了。我們可以利用 [debug](http://docs.ansible.com/ansible/debug_module.html) 這個模組把儲存的訊息輸出到我們的終端機上。在 Ansible 中，若要調用儲存變數，我們必須在變數名稱外加上兩個大括弧 - {{ }} 來告訴 Ansible 大括弧內的是一個變數 （[如果變數在描述句 (statement) 的開頭，還必須要額外加上一個雙引號 - ""](http://docs.ansible.com/ansible/playbooks_variables.html#hey-wait-a-yaml-gotcha)）。

定義好了我們的第一個 playbook 後，接下來就是如何運行我們寫好的 playbook 啦！
