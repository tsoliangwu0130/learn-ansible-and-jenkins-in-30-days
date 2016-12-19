# 使用 Ansible 部署 Jenkins Job

在上一個章節中，我們簡單知道了如何使用 Jenkins 的網路介面來建立一個建置專案。在這個章節裡，我們要試著將這個過程自動化為一個 Ansible 的 role。

#### 工作環境目錄與工作配置目錄

在專案示範中，我們利用了 `pwd` 及 `touch` 這兩個指令來讓 Jenkins 回傳工作環境目錄 (workspace) 的實際位置 (`/var/lib/jenkins/workspace/my_first_jenkins_job`) 至終端機上，並在該目錄下建立 `helloworld.out` 這個檔案。

我們可以登入到遙控主機中觀察此路徑下的目錄結構：

```shell
$ ls -al /var/lib/jenkins/workspace/my_first_jenkins_job

total 8
drwxr-xr-x 2 jenkins jenkins 4096 Dec 19 01:51 .
drwxr-xr-x 3 jenkins jenkins 4096 Dec 18 03:26 ..
-rw-r--r-- 1 jenkins jenkins    0 Dec 19 01:52 helloworld.out
```

從目錄內容中我們可以很清楚地看到所有專案的工作內容，都會被存在這個路徑下，包含我們透過 `touch` 指令產生的 `helloworld.out` 檔案。


