# 撰寫第一個 Ansible Playbook

#### 什麼是 Ansible Playbook？

當確認 Ansible 已經正確地安裝在 control machine 上之後，我們現在就可以準備透過 Ansible 對 managed node 進行部署了。然而，我們要如何告訴 Ansible 我們想要對 managed node 做些什麼呢？在 Ansible 的世界裡，我們是透過編寫劇本 (playbook) 來告訴 Ansible 接下來需要做的事項。由於 Ansible 的 playbook 是使用 [YAML (YAML Ain't Markup Language)](http://yaml.org/) 這種標記語言來撰寫，而這個語言最大的特色就是具有高度可讀性，因此無論有沒有程式語言的基礎，理論上任何人在看到一份好的 Ansible playbook 的時候應該都是非常容易理解及著手修改維護的。

#### 我的第一個 playbook

以下先用一個非常簡單的範例來介紹 playbook 的運作方式。我們先創建一個 playbook 檔案，將其命名為 `playbook.yml` 並置於工作目錄下 (e.g. `workspace/playbook.yml`)。接著，開啟檔案並輸入以下內容：

```yml
---
- hosts: server
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

相信大家就算從來沒有寫過 Ansible 的 playbook，應該還是不難猜出這份 playbook 在做些什麼。簡單來說，我們在這份 playbook 中定義了任務清單 `tasks` 以及部署對象 `hosts: server`。這也是為什麼在之前的章節中我們提到了最好把每一個虛擬主機都做命名。主機命名後，我們可以直接透過主機的名稱直接進行操作（我們會在接下來的章節中提到若不透過 Vagrant 要如何命名主機）。除此之外，我們可以透過 `name` 這個標籤替任務清單中的每個 task 分別命名。在接下來運行 playbook 的過程中若發生錯誤，我們也會比較清楚是在哪一個環節上出了問題。

在這個 playbook 中，我們定義了兩個 task：

1. test connection

	我們在這個 task 中，呼叫了 Ansible 的內建測試模組 (module) - [ping](http://docs.ansible.com/ansible/ping_module.html)。這個模組的目的非常類似在學習每個語言一開始練習的 ["Hello World"](https://zh.wikipedia.org/wiki/Hello_World) 程式。其主要是用來測試 control machine 是否可以正確地與 managed node 進行溝通，如果連線正常，遙控節點就會回傳一個 "pong" 的訊息給控制主機。接著，我們透過另一個 Ansible 內建模組 - [register](http://docs.ansible.com/ansible/playbooks_variables.html#registered-variables) 把 managed node 回傳的訊息儲存在 `message` 這個變數 (variable) 中。

	_注意：此處的 ping 並非建立在 [ICMP 協定](https://zh.wikipedia.org/wiki/%E4%BA%92%E8%81%94%E7%BD%91%E6%8E%A7%E5%88%B6%E6%B6%88%E6%81%AF%E5%8D%8F%E8%AE%AE)下的 [ping](https://zh.wikipedia.org/wiki/Ping) 指令，只是純 Ansible 開發的一個簡單測試模組。_

2. print debug message

	在上一個 task 中，我們已經把 ping 後的結果存在 `{% raw %}{{ message }}{% endraw %}` 這個變數中了。我們可以利用 [debug](http://docs.ansible.com/ansible/debug_module.html) 這個模組把儲存的訊息輸出到我們的終端機上。周圍的兩個大括號是 Ansible 基於 Jinja2 系統下[使用變數](http://docs.ansible.com/ansible/latest/playbooks_variables.html#using-variables-about-jinja2)的方式。若變數是在描敘句的開頭，則還需要再[加上兩個引號 (quote)](http://docs.ansible.com/ansible/latest/playbooks_variables.html#hey-wait-a-yaml-gotcha)。

在定義好了我們的第一個 playbook 後，接下來就是如何運行我們寫好的 playbook 啦！

#### [Optional] 利用 Ansible-lint 來檢查 playbook

上一個章節中，筆者有提到 [Ansible-lint](https://github.com/willthames/ansible-lint) 這個語法提示工具，如果沒有安裝的讀者可以自行跳過這個部分。使用 Ansible-lint 的方法相當簡單，只要在終端機中利用 `ansible-lint` 輸入需要檢查的檔案 (e.g. `playbook.yml`) 即可：

```shell
$ ansible-lint playbook.yml
```

如果沒有看到任何輸出就表示你的 playbook 完全沒有問題！不過為了展示 Ansible-lint 的提示效果，我在 playbook 的第五行句尾加上一個空白：

```yml
    - name: test connection
                           ~
```

接著，重新輸入剛剛的指令：
```
$ ansible-lint playbook.yml

[ANSIBLE0002] Trailing whitespace
playbook.yml:5
    - name: test connection
```

我們可以清楚地看到 Ansible-lint 告訴我們在 `playbook.yml:5` 第五行的地方有個後綴空白 (trailing whitespace)，為了保持程式碼的簡潔，我們可以將其刪除。


#### [Optional] Sublime 插件 - Syntax highlighting for Ansible files

如果讀者使用的編輯器是 [Sublime Text](https://www.sublimetext.com/)，在這裡推薦一個不錯的語法高亮器 (Syntax Highlighting) 給大家。因為在 Sublime 中，語法高亮並沒有原生支援 Ansible 的語法，所以我們只能預設使用 YAML 來加亮語法。[Syntax highlighting for Ansible files](https://github.com/clifford-github/sublime-ansible) 這套插件補強了 Ansible 在 Sublime 上的顯示效果，非常推薦使用 Sublime 的讀者安裝這套插件。

![sublime_ansible](https://github.com/tsoliangwu0130/learn-ansible-and-jenkins-in-30-days/raw/master/images/sublime_ansible.png "Sublime Plugin for Ansible")

#### [Optional] Atom 插件 - language-ansible + linter-ansible-linting

與上述 Sublime 插件相當類似，若讀者使用 [Atom](https://atom.io/) 作為開發編輯器，也可以考慮安裝 `language-ansible` 與 `linter-ansible-linting` 來作為語法高亮以及即時 linter 作為輔助。除此之外，Atom 上還提供了相當多強大的插件例如 `autocomplete-ansible` 等等，讀者可以根據需求自行安裝。
