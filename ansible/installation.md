# Ansible 安裝

#### 什麼是控制主機 (Control Machine) 及被控節點 (Managed Node)？

在 Ansible 裡，我們會把所有機器的角色做以下兩種區分：

1. **控制主機 (Control Machine)**：顧名思義，這類主機可以透過運行 Ansible 的劇本 (playbook) 對被控節點進行部署。
2. **被控節點 (Managed Node)**：也稱為遙控節點 (Remote Node)。相對於控制主機，這類節點就是我們透過 Ansible 進行部署的對象。

對於這兩種不同的對象，在安裝 / 使用 Ansible 的時候也有不同的需求。

#### 如何安裝 Ansible 在 control machine 上？

由於 Ansible 是一套開源的軟體，所以在目前大部分的主流作業系統上都已經可以透過對應的套件管理 (package manager) 進行安裝了。以下列出幾個我主要比較常用作業系統的安裝方法：

##### macOS

* [Pip Installs Packages / Pip Installs Python (pip - 官方推薦)](https://pip.pypa.io/en/stable/installing/)

	```shell
	$ sudo pip install ansible
	```

* [Homebrew (brew)](http://brew.sh/)

	```shell
	$ sudo brew install ansible
	```

##### CentOS

* [Yellowdog Updater, Modified (yum)](http://yum.baseurl.org/)

	```shell
	$ sudo yum -y install epel-release
	$ sudo yum -y update
	$ sudo yum -y install ansible
	```

##### Ubuntu

* [Advanced Packaging Tool (apt)](https://help.ubuntu.com/lts/serverguide/apt.html)

	```shell
	$ sudo apt-get install software-properties-common
	$ sudo apt-add-repository ppa:ansible/ansible
	$ sudo apt-get update
	$ sudo apt-get install ansible
	```

##### Debian

* [Advanced Packaging Tool (apt)](https://wiki.debian.org/Apt)

	在 `/etc/apt/sources.list` 中加入下面這行：

	```
	deb http://ppa.launchpad.net/ansible/ansible/ubuntu trusty main
	```

	在終端機中執行：

	```shell
	$ sudo apt-get update
	$ sudo apt-get install ansible
	```

安裝好以後，確認 Ansible 已經安裝完成：

```shell
$ ansible --version

ansible 2.4.2.0
```

#### 如何安裝 Ansible 在 managed node 上？

不需要！透過 Ansible 管理的 managed node 完全不需要安裝 Ansible。如上個章節所述，我們只需要確保這個節點可以透過 SSH 與 control machine 溝通，並已安裝 Python 2.6 以上的版本就可以透過 control machine 來進行部署及管理了。

#### [Optional] 安裝 Ansible-lint

在編寫程式的時候，我都會習慣去找對應語言的 [linter](https://zh.wikipedia.org/wiki/Lint) 來習慣該語言的風格。雖然 Ansbile 並不是一種程式語言，但它也有一個對應的 linter - [Ansible-lint](https://github.com/willthames/ansible-lint)。我會建議讀者可以安裝這個小工具，它可以幫助你確保寫出來的腳本品質是有一定水準的。

``` shell
$ sudo pip install ansible-lint
```

檢查版本：

``` shell
$ ansible-lint --version

ansible-lint 3.4.19
```
