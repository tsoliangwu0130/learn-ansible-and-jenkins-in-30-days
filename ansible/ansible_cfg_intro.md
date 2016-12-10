# 配置 ansible.cfg

#### 什麼是 ansible.cfg？

從上面的結果中，我們可以發現雖然 playbook 已經成功被執行，但 Ansible 並沒有辦法找到我們剛剛新建 role 的位置。因此，我們必須透過設定 [Ansible 的配置檔案](http://docs.ansible.com/ansible/intro_configuration.html) - `ansible.cfg` 來設置 [role 路徑 (path)](http://docs.ansible.com/ansible/intro_configuration.html#roles-path)。

新增一個檔案 `ansible.cfg` 並加入以下內容：

```
[ironman]
roles_path = ./roles
```

雖然只有短短兩行，但 Ansible 就會依此路徑去找到我們 role 的存放位置。接著重新執行我們的 playbook：

```shell
PLAY [ironman] *****************************************************************

TASK [setup] *******************************************************************
ok: [ironman]

TASK [curl : install curl] *****************************************************
changed: [ironman]

PLAY RECAP *********************************************************************
ironman                    : ok=2    changed=1    unreachable=0    failed=0
```

看起來好像成功了！現在讓我們登入進去遙控節點看看是否 cURL 已經可以運作了：

```shell
$ curl -I http://localhost:8080

HTTP/1.1 403 Forbidden
Date: Fri, 09 Dec 2016 09:03:44 GMT
X-Content-Type-Options: nosniff
Set-Cookie: JSESSIONID.4689751a=1o67mouadl3zhq80h5b2xxxu6;Path=/;HttpOnly
Expires: Thu, 01 Jan 1970 00:00:00 GMT
Content-Type: text/html;charset=UTF-8
X-Hudson: 1.395
X-Jenkins: 2.19.4
X-Jenkins-Session: e765255c
X-You-Are-Authenticated-As: anonymous
X-You-Are-In-Group:
X-Required-Permission: hudson.model.Hudson.Administer
Content-Length: 677
Server: Jetty(9.2.z-SNAPSHOT)
```

先姑且不論結果是否是我們要的，至少我們現在確定 cURL 已經被 Ansible 正確安裝到遙控節點上了！

若點進官方配置文件的[說明頁面](http://docs.ansible.com/ansible/intro_configuration.html)，可以發現除了 `roles_path` 之外還有一大堆參數可以依據使用者的需求進行非常有彈性的配置。其中值得注意的是，在這個示範裡面我是將 `ansible.cfg` 這個配置檔案置於我們工作資料夾之下，目前具體資料夾結構如下：

```shell
workspace
├── Vagrantfile
├── ansible.cfg
├── inventory
├── playbook.yml
└── roles
    └── curl
        └── tasks
            └── main.yml
```

但從官方文件中，我們可以發現除了將配置檔案放置於工作目錄底下外，還有其他幾種不同的配置方式：

1. 設置環境變量 (ANSIBLE_CONFIG)
2. 置於當前目錄下的 `ansible.cfg`（這次範例中使用的方法）
3. 置於根目錄下的 `.ansible.cfg`
4. 置於 `/etc/ansible/` 下的 `ansible.cfg`

若我們同時分別有不同的配置文件存在在系統中，Ansible 會依照上述順序逐一查找文件，若找到其中一個，就會依其設定進行配置，並不再繼續查找。
