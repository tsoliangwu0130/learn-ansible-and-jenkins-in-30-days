# 配置 ansible.cfg

我們在上一個章節中在 Ansible 預設查找的路徑下建立了我們的第一個 role 並成功執行部署。然而，若今天我們希望 Ansible 根據我們自己定義的路徑來執行 role 呢？以筆者自身的經驗而言，根據 role 屬性的不同，可能有些 role 我們認為較為是通用的就會放在 public repo 上，但有些 roles 則因為安全性的考量，所以需要放在 private repo 裡。我們並不希望將這兩類的 roles 全部丟到同一個路徑下，這樣既不好管理也容易產生混淆。那在這樣的情況下，我們有辦法透過任何方式來解決這個問題嗎？

#### 什麼是 ansible.cfg？

答案顯然是有的。在 Ansible 中，我們可以透過配置 Ansible 的組態檔案 - [ansible.cfg](http://docs.ansible.com/ansible/intro_configuration.html)，來指定 Ansible 要去哪裡查找 [role 的路徑](http://docs.ansible.com/ansible/intro_configuration.html#roles-path)。

在工作目錄新增一個檔案 `ansible.cfg` 並加入以下內容：

```
[defaults]
roles_path = /path/to/roles
```

如此一來，Ansible 除了預設查找路徑外，還會依此指定路徑去找到我們 role 的存放位置。假設有多個 role 路徑需要設定，我們還可以透過 `:` 來串接不同 role 的存放路徑，比如：

```
[defaults]
roles_path = /path/to/public_roles:/path/to/private_roles
```

#### ansible.cfg 還可以做什麼？

若點進官方配置文件的[說明頁面](http://docs.ansible.com/ansible/intro_configuration.html)，可以發現除了 `roles_path` 之外還有一大堆參數可以依據使用者的需求進行非常有彈性的配置。還記得我們在之前的章節中，必須要在運行 `ansible-playbook` 的時候同時加上 `-i` 以及 `--private-key` 的參數來告訴 Ansible 我們的 inventory file 跟 SSH 金鑰的存放位置嗎？我們並不想要在每次運行都要輸入這一大串資料，因此我們可以在 ansible.cfg 裡定義 `inventory` 跟 `private_key_file` 來方便我們管理這類資訊：

```
[defaults]
private_key_file = /path/to/private_key
inventory = /path/to/inventory
roles_path = /path/to/roles
```

定義完成後，在未來我們只需要運行 `ansible-playbook playbook.yml` 簡單一行指令就可以輕鬆使用 Ansible 來進行部署拉！

其中值得注意的是，在這個示範裡面我是將 `ansible.cfg` 這個配置檔案置於我們工作資料夾之下，目前具體資料夾結構如下：

```
workspace
├── Vagrantfile
├── ansible.cfg
├── inventory
├── playbook.yml
└── roles
    └── pip
        └── tasks
            └── main.yml
```

但從官方文件中，我們可以發現除了將配置檔案放置於工作目錄底下外，還有其他幾種不同的配置方式：

1. 設置環境變量 (ANSIBLE_CONFIG)
2. 置於當前目錄下的 `ansible.cfg`（這次範例中使用的方法）
3. 置於根目錄下的 `.ansible.cfg`
4. 置於 `/etc/ansible/` 下的 `ansible.cfg`

若我們同時分別有不同的配置文件存在在系統中，Ansible 會依照上述順序逐一查找文件，若找到其中一個，就會依其設定進行配置，並不再繼續查找。
