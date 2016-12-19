# 使用 Ansible 部署 Jenkins Job

在上一個章節中，我們簡單知道了如何使用 Jenkins 的網路介面來建立一個建置專案。在這個章節裡，我們要試著將這個過程自動化為一個 Ansible 的 role。

#### 

在專案示範中，我們利用了 `pwd` 這個指令來讓 Jenkins 回傳工作目錄的實際位置至終端機上，而其工作目錄為 `/var/lib/jenkins/workspace/my_first_jenkins_job`。這個目錄存放著我們

我們可以登入到遙控主機中觀察此路徑下的目錄結構：

```shell
$ ls -al /var/lib/jenkins/workspace/my_first_jenkins_job

```


