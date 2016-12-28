# 使用 Role Strategy 插件管理 Jenkins

在使用 Jenkins 的過程中，我們無可避免地會與團隊內外的開發人員共同作業。為了安全性考量，我們常常會將使用者的權限做一些基本的劃分。在這個章節內，我將介紹 [Role Strategy Plugin](https://wiki.jenkins-ci.org/display/JENKINS/Role+Strategy+Plugin) 這個插件來實現角色劃分的需求。

#### 安裝 Role-based Authorization Strategy 插件

在插件管理中安裝 `Role-based Authorization Strategy` 這個插件：

![role_strategy_01](https://github.com/tsoliangwu0130/learn-ansible-and-jenkins-in-30-days/blob/master/images/role_strategy_01.png?raw=true)

由於在前面章節已經介紹過如何安裝插件，這裡就不多做贅述了。安裝完成後可以在已安裝清單中看到插件：

![role_strategy_02](https://github.com/tsoliangwu0130/learn-ansible-and-jenkins-in-30-days/blob/master/images/role_strategy_02.png?raw=true)

#### 設定 Role-Based Strategy

在 Jenkins 控制首頁點選 `Manage Jenkins` 下的 `Configure Global Security`，並設定如下：

![role_strategy_03](https://github.com/tsoliangwu0130/learn-ansible-and-jenkins-in-30-days/blob/master/images/role_strategy_03.png?raw=true)

我們在這裡啟動了安全性檢查，並更改授權方式為 `Role-Based`。除此之外，我們也在這裡開放了使用者申請個人帳號以便管理。作為測試，我們可以先在 `Manage Jenkins` 下的 `Manage Users` 手動新增一個測試使用者：

![role_strategy_04](https://github.com/tsoliangwu0130/learn-ansible-and-jenkins-in-30-days/blob/master/images/role_strategy_04.png?raw=true)

新增完成後可以在使用者列表看到新的使用者：

![role_strategy_05](https://github.com/tsoliangwu0130/learn-ansible-and-jenkins-in-30-days/blob/master/images/role_strategy_05.png?raw=true)

#### 建立新的 Role 來劃分權限

回到 `Manage Jenkins`，點選 `Manage and Assign Roles` 下的 `Manage Roles`：

![role_strategy_06](https://github.com/tsoliangwu0130/learn-ansible-and-jenkins-in-30-days/blob/master/images/role_strategy_06.png?raw=true)

可以看到目前我們只有一開始建立的 `admin` 這個角色，而 `admin` 擁有了全部操作 Jenkins 的權限。若今天我們有了不同的需求，比如 A 部門的開發人員只可以擁有建置專案的權限，我們可以在 `Role to add` 的欄位新增一個角色 (e.g. `A-Developers`) 來劃分權限如下：

![role_strategy_07](https://github.com/tsoliangwu0130/learn-ansible-and-jenkins-in-30-days/blob/master/images/role_strategy_07.png?raw=true)

在這個範例中，我們只給這個角色的使用者整體的閱讀權限，以及專案的基本建置權限。讀者可以自行根據需求調整分配權限。設定完成後儲存離開。

#### 指定 Role 給 Jenkins 使用者

在建立好了不同的 role 以後，我們現在可以分配 role 給我們剛剛建立的使用者。回到 `Manage Jenkins` 中點選 `Manage and Assign Roles` 下的 `Assign Roles`，並在 `User/group to add` 的欄位中新增我們剛剛建立的使用者 `tsoliang`，然後將其角色指定為 `A-Developers`：

![role_strategy_08](https://github.com/tsoliangwu0130/learn-ansible-and-jenkins-in-30-days/blob/master/images/role_strategy_08.png?raw=true)

儲存變更後，點選介面右上角的 `log out` 登出，並重新以 `tsoliang` 的身份登入：

![role_strategy_09](https://github.com/tsoliangwu0130/learn-ansible-and-jenkins-in-30-days/blob/master/images/role_strategy_09.png?raw=true)

從上面的結果我們可以看到當我們以 `tsoliang` 的身份登入回來以後，我們在 `Jenkins` 的控制頁面看不到 `Manage Jenkins` 這些控制 Jenkins 基本設定的欄位了。接著任意點選專案，因為我們剛剛也取消了 `A-Developers` 這個角色的專案配置權限，除了依然可以建置專案之外，我們在這裡也無法對專案的的配置進行變更。
