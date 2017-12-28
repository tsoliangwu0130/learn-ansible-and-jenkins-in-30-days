# 原始碼管理與建置觸發程序

Jenkins 作為一個持續整合的工具，與原始碼管理系統的整合尤其重要。在這個章節內，我們會介紹如何在 Jenkins 上透過原始碼管理 (source code management, SCM) 系統，例如 GitHub 來獲得專案的原始碼，並設置建置觸發程序 (build triggers) 來實踐持續整合。

接下來我一樣會使用[這個專案](https://github.com/tsoliangwu0130/my-ansible)來當作範例。我要利用 [ansible-lint](https://github.com/willthames/ansible-lint) 這個語法檢查工具來自動檢查我的專案是否都有符合規範。因此，為了讓 Jenkins 可以使用 ansible-lint，我們必須在 Jenkins 運行的環境下先安裝好這個工具。如果是跟著[實戰 Ansible](https://tsoliangwu0130.gitbooks.io/learn-ansible-and-jenkins-in-30-days/content/ansible/practical-ansible.html) 的方式安裝 Jenkins 的話，眼尖的讀者可能發現我在 [docker-jenkins/defaults/main.yml](https://github.com/tsoliangwu0130/my-ansible/blob/master/roles/docker-jenkins/defaults/main.yml#L14) 這裡已經先在運行的容器內安裝好 ansible-lint 了。當然，如果你希望運行的是更乾淨或更客製化的環境，可以在這裡任意刪除或增加套件。

#### 建置專案

在 Jenkins 運行環境下安裝好 ansible-lint 後，回到 Jenkins 管理首頁建立一個新的 Free-Style 專案。

#### 原始碼管理

建立完成後，在原始碼管理的欄位選擇 **Git**，並填入 repository URL：

![](https://github.com/tsoliangwu0130/learn-ansible-and-jenkins-in-30-days/blob/master/images/jenkins-git-01.png?raw=true)

這邊是使用 HTTPS 連線來存取專案。但如果是有存取限制的專案，例如 private repo，我們會看到類似以下連線失敗的畫面：

![](https://github.com/tsoliangwu0130/learn-ansible-and-jenkins-in-30-days/blob/master/images/jenkins-git-02.png?raw=true)

我們可以在下面的 **Credentials** 欄位選擇 `Add > Jenkins` 後，在 **Kind** 的欄位選擇 **Username with password**，並輸入你的 GitHub 帳號密碼：

![](https://github.com/tsoliangwu0130/learn-ansible-and-jenkins-in-30-days/blob/master/images/jenkins-git-03.png?raw=true)

![](https://github.com/tsoliangwu0130/learn-ansible-and-jenkins-in-30-days/blob/master/images/jenkins-git-04.png?raw=true)

輸入完成後，選擇剛剛建立的憑證 (credential)，就可以發現剛剛的連線失敗警告已經消失了：

![](https://github.com/tsoliangwu0130/learn-ansible-and-jenkins-in-30-days/blob/master/images/jenkins-git-05.png?raw=true)

另外，如果今天想要透過 SSH 來存取專案，我們應該會遇到狀況如下：

![](https://github.com/tsoliangwu0130/learn-ansible-and-jenkins-in-30-days/blob/master/images/jenkins-git-06.png?raw=true)

這是因為我們的 Jenkins 還沒與 GitHub 做 SSH 的金鑰配對，所以 GitHub 拒絕 Jenkins 透過 SSH 存取。解決這個方式最簡單的作法就是在 Jenkins 主機下[建立 SSH 金鑰](https://help.github.com/articles/generating-a-new-ssh-key-and-adding-it-to-the-ssh-agent/)，並[將公開金鑰 key 加入你的 GitHub 帳戶中](https://help.github.com/articles/adding-a-new-ssh-key-to-your-github-account/)。在配對完成後，回到 Jenkins 專案的 **Credentials** 欄位下並選擇 `Add > Jenkins`。這次在 **Kind** 的欄位選擇 **SSH Username with private key**，並依序填入剛剛 SSH 憑證的資料後點選 **Add** 離開：

![](https://github.com/tsoliangwu0130/learn-ansible-and-jenkins-in-30-days/blob/master/images/jenkins-git-07.png?raw=true)

選擇剛剛新建立的憑證，如果沒有意外現在就可以用 SSH 來存取專案囉！

![](https://github.com/tsoliangwu0130/learn-ansible-and-jenkins-in-30-days/blob/master/images/jenkins-git-08.png?raw=true)


在這裡讀者也可以自由選擇要從哪一個分支 (branch) 來進行建置。如果在 **Branches to build** 處留白，Jenkins 自動偵測專案下的所有 branches。更多細節可以點選旁邊的 **?** 查看。

![](https://github.com/tsoliangwu0130/learn-ansible-and-jenkins-in-30-days/blob/master/images/jenkins-git-09.png?raw=true)

#### 建置觸發程序

由於現在我們並沒有定義任何建置程序，所以除非我們手動操作 Jenkins，不然 Jenkins 並不會主動幫我們進行建置。因此，在**建置觸發程序**這個欄位內，我們可以自由設定我們希望 Jenkins 何時自動幫我們建置專案。根據專案屬性的不同，我們可以採取不同的建置時機。比較常見的有以下兩種做法：

* 定期建置

    在 Jenkins 中，我們是採取 [Cron Format](http://www.nncron.ru/help/EN/working/cron-format.htm) 的方式來定義建置行程。簡單來說，Cron Format 共分五個欄位，欄位與欄位之間可用空白或 Tab 鍵做區隔：

    1. 分 (minute)：0 - 59 分
    2. 時 (hour)： 0 - 23 時
    3. 日 (day of month)： 1 - 31 日
    4. 月 (month)：1 - 12 月
    5. 星期 (day of week)：星期 0 - 7 （其中 0 與 7 都代表星期天）

    舉例而言，若我們希望每隔 15 分鐘就建置一次當前專案，我們可以在定義規則裡填入 `H/15 * * * *` 如下：

    ![](https://github.com/tsoliangwu0130/learn-ansible-and-jenkins-in-30-days/blob/master/images/jenkins-trigger-01.png?raw=true)

    除了上述的定義外，我們還可以點擊旁邊的 **?** 標誌來查看更多較彈性的 Cron Format 寫法。

* GitHub hook trigger for GITScm polling

    這種觸發方式在實踐持續整合時非常實用。除了定期建置外，我們也可以讓 Jenkins 自動監控當在原始碼專案有任何 push event 發生時就進行建置。為了要使用這種方式建置，有以下幾個步驟需要設定：

    1. 新增 GitHub personal access token：

        1. 進入 GitHub 首頁，點選右上角下拉式選單，點選 **Settings**

            ![](https://github.com/tsoliangwu0130/learn-ansible-and-jenkins-in-30-days/blob/master/images/jenkins-trigger-02.png?raw=true)

        2. 點選左邊的 **Developer settings**
        3. 點選左邊的 **Personal access tokens**
        4. 點選右上角的 **Generate new token**
        5. 輸入 token 的描述並勾選 **repo** scope 後，點選 **Generate token** 離開

            ![](https://github.com/tsoliangwu0130/learn-ansible-and-jenkins-in-30-days/blob/master/images/jenkins-trigger-03.png?raw=true)

        6. 將產生的 token 複製下來

    2. 進入 Jenkins 專案組態頁面，到原始碼管理的地方將 **Repository URL** 從原本的 `https://github.com/tsoliangwu0130/my-ansible.git` 修改為 `https://{github-token}@github.com/twistresources/arbys.git`。其中 `github-token` 是在第一步建立出來的 GitHub token。接著，在下面**建置觸發程序**的地方勾選 **GitHub hook trigger for GITScm polling** 並儲存離開。

    ![](https://github.com/tsoliangwu0130/learn-ansible-and-jenkins-in-30-days/blob/master/images/jenkins-trigger-04.png?raw=true)

    3. 最後，在 GitHub 上整合 Jenkins

        1. 到 GitHub 被建置專案的頁面下點選 **Settings** 標籤
        2. 點選左邊的 **Integration & services**
        3. 點選右下方 **Add service** 下拉式選單，並選取 **Jenkins (GitHub plugin)**

            ![](https://github.com/tsoliangwu0130/learn-ansible-and-jenkins-in-30-days/blob/master/images/jenkins-trigger-05.png?raw=true)

        4. 輸入 **Jenkins hook url** 後點選 **Add service** 離開

            ![](https://github.com/tsoliangwu0130/learn-ansible-and-jenkins-in-30-days/blob/master/images/jenkins-trigger-07.png?raw=true)

        > 註：由於我們現在是將 Jenkins 運行在本機端，所以 Jenkins URL 是 `http://localhost:8080`。然而，GitHub 並不知道誰是 localhost，為了練習，我們可以透過 [ngrok](https://ngrok.com/) 這套簡單的小工具來暫時將 `localhost:8080` 推送到網路上。ngrok 的使用方式非常簡單，只要將檔案下載下來後，透過 command line 切換到下載目錄，並執行：

        > ```shell
        > $ ./ngrok http 8080
        > ```

        > 應該就可以看到類似以下畫面：             ![](https://github.com/tsoliangwu0130/learn-ansible-and-jenkins-in-30-days/blob/master/images/jenkins-trigger-06.png?raw=true)
        > 其中 `http://c84557ae.ngrok.io` 就是我們這次將 `localhost:8080` 推送網路上的網址。

    如此一來，Jenkins 就可以自動監控專案的變化並自動執行建置了。讀者可以根據使用上的需求，同時搭配定時建置以及自動監測的建置模式來建置專案，來徹底實踐持續整合的概念。

#### 建置

在這個範例裡，加入以下 Shell script 來建置專案：

```shell
for file in $(find . -type f -name "*.yml")
do
	ansible-lint $file
done
```

最後，點擊**儲存**來離開專案組態的設定。

#### 建置結果

如我們預期的，每隔 15 分鐘 Jenkins 就自動執行一次專案建置：

![](https://github.com/tsoliangwu0130/learn-ansible-and-jenkins-in-30-days/blob/master/images/jenkins-trigger-08.png?raw=true)

點擊第一個建置，我們也可以看到該次建置是由計時器所發動的：

![](https://github.com/tsoliangwu0130/learn-ansible-and-jenkins-in-30-days/blob/master/images/jenkins-trigger-09.png?raw=true)

作為測試，我在 09:57 的時候 push 了一個 commit 到 GitHub 上，雖然當下並非建置的排程時間，但由於我們還有設置自動監測的觸發建置條件，因此 Jenkins 仍然執行了第四次的建置，而我們在建置頁面上也可以看到是 GitHub 上的哪位使用者觸發這次建置的：

![](https://github.com/tsoliangwu0130/learn-ansible-and-jenkins-in-30-days/blob/master/images/jenkins-trigger-10.png?raw=true)
