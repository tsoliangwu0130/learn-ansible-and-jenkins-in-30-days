# 使用 template 部署 Jenkins 設定

在上一個章節中透過 Ansible 安裝好所有 Jenkins 建議插件後，現在剩下建立 admin 使用者就能完全自動化我們的 Jenkins 安裝流程了，然而，在建立自動化系統的時候，真的只需要把所有任務翻譯成自動化腳本就好了嗎？

#### 所有步驟都適合被自動化嗎？

我們在初始化 Jenkins 的過程中透過了網頁介面新增了我們的 admin 使用者。而這個使用者的資料被存放於 `/var/lib/jenkins/users/admin/config.xml` 的檔案中（在 Jenkins 中，我們是以 [XML (Extensible Markup Language)](https://zh.wikipedia.org/zh-tw/XML) 的格式存放所有設定檔）：

```xml
<?xml version='1.0' encoding='UTF-8'?>
<user>
  <fullName>Admin</fullName>
  <properties>
    <jenkins.security.ApiTokenProperty>
      <apiToken>tUKagIUGUAaJJjO9X/xx6i9HrT/YbvMkc3Ba+pN/Xc2Vpng0oqoNXjBP1uNKsI5X</apiToken>
    </jenkins.security.ApiTokenProperty>
    <com.cloudbees.plugins.credentials.UserCredentialsProvider_-UserCredentialsProperty plugin="credentials@2.1.10">
      <domainCredentialsMap class="hudson.util.CopyOnWriteMap$Hash"/>
    </com.cloudbees.plugins.credentials.UserCredentialsProvider_-UserCredentialsProperty>
    <hudson.plugins.emailext.watching.EmailExtWatchAction_-UserProperty plugin="email-ext@2.52">
      <triggers/>
    </hudson.plugins.emailext.watching.EmailExtWatchAction_-UserProperty>
    <hudson.model.MyViewsProperty>
      <views>
        <hudson.model.AllView>
          <owner class="hudson.model.MyViewsProperty" reference="../../.."/>
          <name>All</name>
          <filterExecutors>false</filterExecutors>
          <filterQueue>false</filterQueue>
          <properties class="hudson.model.View$PropertyList"/>
        </hudson.model.AllView>
      </views>
    </hudson.model.MyViewsProperty>
    <hudson.model.PaneStatusProperties>
      <collapsed/>
    </hudson.model.PaneStatusProperties>
    <hudson.search.UserSearchProperty>
      <insensitiveSearch>false</insensitiveSearch>
    </hudson.search.UserSearchProperty>
    <hudson.security.HudsonPrivateSecurityRealm_-Details>
      <passwordHash>#jbcrypt:$2a$10$lkFzobXmlTOLAQS2zNFpcOrs5Jqgp6Ce9OVakQOeltvdazMzvZY7y</passwordHash>
    </hudson.security.HudsonPrivateSecurityRealm_-Details>
    <hudson.tasks.Mailer_-UserProperty plugin="mailer@1.18">
      <emailAddress>admin@example.com</emailAddress>
    </hudson.tasks.Mailer_-UserProperty>
  </properties>
</user>
```

我們可以從瀏覽器上訪問 `http://localhost:9080/me/configure` 來看到以上設定檔的視覺畫面：

![]()

在這個檔案內存放了所有 `admin` 這個使用者的所有資料。其中 `fullName` 跟 `emailAddress` 這兩個標籤下的值與我們當初建立使用者時輸入的資料完全一致。不過，在這個檔案中，我們可以注意到還有兩個特殊的標籤：`apiToken` 以及 `passwordHash`。API token 是

