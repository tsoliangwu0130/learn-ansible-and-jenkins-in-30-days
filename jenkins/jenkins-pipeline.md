# Pipeline 專案

在對 Jenkins 有基本的認識後，接下來的章節內我將介紹如何使用 Jenkins 的 Pipeline 專案來將強化我們的建置流程，並以此深化產品的持續整合，甚至更進一步實踐持續交付與持續部署。

#### 何謂 Pipeline 專案

在前面的章節裡，我們都是使用 Free-Style 類型的專案來設計建置作業。雖然 Jenkins 的確提供了相當完善的功能以及足夠多的外掛，來讓我們可以隨時根據需求做相當有彈性的調整，但當隨著我們的專案規模持續擴大，建置作業與建置作業開始有更高程度的依賴關係時，原本的 Free-Style 專案會漸漸顯得不敷使用。因此，在 Jenkins 2.0 後的版本釋出了 Pipeline 這個概念。透過 Pipeline 專案，我們可以用 [Groovy](http://groovy-lang.org/) 這個相當容易理解的語言來撰寫建置腳本，以此將不同的獨立專案做邏輯上的連結，並實現更加複雜的建置邏輯。

#### 建立第一個 Pipeline 專案

為了瞭解 Pipeline 專案是什麼，我們可以試著將之前的專案改寫成 Pipeline 的類型，並藉此學習其工作流程與設計方式。首先，建立一個新的專案，這次記得選擇 **Pipeline** 類型的專案：

![](https://github.com/tsoliangwu0130/learn-ansible-and-jenkins-in-30-days/blob/master/images/jenkins-pipeline-01.png?raw=true)

建立好專案後會直接進入專案組態畫面。乍看之下跟一般的 Free-Style 專案並沒有太大的不同，但仔細看會發現中間少了類似建置動作或是建置後動作等欄位，取而代之的是一個叫做 **Pipeline** 的欄位：

![](https://github.com/tsoliangwu0130/learn-ansible-and-jenkins-in-30-days/blob/master/images/jenkins-pipeline-02.png?raw=true)

前面有稍微提到，Pipeline 的建置腳本是基於 Groovy 這個程式語言上所設計的。雖然是這樣說，但實際上會使用到的語法都相當基礎，加上 Jenkins 也提供了非常多範例程式碼片段 (code snippet)，因此若在之前從未接觸過 Groovy 也完全不需要擔心。

在 **Script** 的區塊中是定義建置腳本的地方，我們可以點擊下面的 **Pipeline Syntax** 來查找 Pipeline 腳本的寫法，或甚至自動產生一些常用的建置流程。在 **Snippet Generator** 的標籤下，我們可以看到右邊有一個程式碼片段的自動產生器。在 **Sample Step** 的下拉式選單中可以看到有適用各種不同情況的範例可以選擇，這裡我們先選擇 **git: Git**。選擇後會看到下面跑出了一個非常類似之前 Free-Style 專案中的建置欄位，我們依序填入對應值後點擊下面的 **Generate Pipeline Script**：

![](https://github.com/tsoliangwu0130/learn-ansible-and-jenkins-in-30-days/blob/master/images/jenkins-pipeline-03.png?raw=true)

如上圖所示，我們可以看到 Jenkins 在下面自動產生了這段程式碼：

```groovy
git 'https://github.com/tsoliangwu0130/my-ansible.git'
```

這段程式碼就是在 Pipeline 建置腳本裡使用 Git 來提取專案原始碼的對應程式碼，我們只需要將這段程式碼複製到剛剛專案組態下的腳本欄位即可。除了 **git: Git** 以外，再選取 **node: Allocate node** 產生程式碼片段。在 Pipeline 中，我們同樣可以利用之前學過的 agent node 來分配建置作業，這邊我使用 master 作為這次的建置節點：

![](https://github.com/tsoliangwu0130/learn-ansible-and-jenkins-in-30-days/blob/master/images/jenkins-pipeline-04.png?raw=true)

最後，建立執行 Shell script 程式碼片段：

![](https://github.com/tsoliangwu0130/learn-ansible-and-jenkins-in-30-days/blob/master/images/jenkins-pipeline-05.png?raw=true)

將三個程式碼片段組合後的成果如下：

```groovy
node('master') {
    git 'https://github.com/tsoliangwu0130/my-ansible.git'

    sh '''for file in $(find . -type f -name "*.yml")
    do
	    ansible-lint $file
    done'''
}
```

將其填入專案組態後儲存離開並執行專案建置。若沒有意外，我們應該可以在建置結果頁面看到專案建置成功。雖然這是一個 Pipeline 專案，但因為我們還沒有設置任何其他參數，所以建置結果理論上會與之前的專案狀態一致。

#### Agent (Node), Stage and Step

藉由上面的例子，大概可以稍微想像 Pipeline 類型專案運作的模式。更具體的說，其實 Pipeline 專案主要是由三個核心概念組成，分別是：

1. **Agent / Node - 節點**

    Agent 也可以稱為 node，就如同上一章介紹的一樣，我們可以在 Pipeline 專案中指定在建置作業的哪些部分使用特定**節點**以及其工作環境來執行建置作業。

2. **Stage - 階段**

    由於 Pipeline 專案的目的就是要將建置作業打造成如同流水線一樣順暢的流程，所以 stage 在 Pipeline 裡代表的就是建置**階段**。

3. **Step - 步驟**

    在定義階段後，我們可以更近一步定義在該階段下的建置**步驟**，舉凡是 `git 'https://github.com/tsoliangwu0130/my-ansible.git'` 或是 `echo "Hello Jenkins"` 等都可以是建置步驟。

若以這三個核心概念為基礎，我們可以將上面的範例改寫成類似以下這樣：

```groovy
pipeline {
    agent {
        node {
            label 'master'
        }
    }
    stages {
        stage('Checkout') {
            steps {
                git(url: 'https://github.com/tsoliangwu0130/my-ansible', branch: 'master')
            }
        }
        stage('Build') {
            steps {
                sh '''for file in $(find . -type f -name"*.yml")
                do
	               ansible-lint $file
                done'''
            }
        }
        stage('Delivery') {
            steps {
                sh 'echo \'Publish artifact over SSH.\''
            }
        }
    }
}
```

通常來說，我們會將這種定義好的 Pipeline 建置腳本儲存在 `Jenkinsfile` 檔案內來做管理。我們可以根據專案的需求來任意擴增專案的建置階段，如上面的例子，我建立了一個 Delivery 的建置階段來將建置產物傳遞到目標伺服器上。但因為這個章節主要是介紹 Pipeline 專案的邏輯，所以這邊只用了 `echo` 來模擬建置步驟。建置腳本設定好之後儲存離開，並執行建置。這次我們應該會看到不一樣的結果如下：

![](https://github.com/tsoliangwu0130/learn-ansible-and-jenkins-in-30-days/blob/master/images/jenkins-pipeline-06.png?raw=true)

在定義了 stage 後，我們會發現 Jenkins 在建置過程中建立了一個視覺化的 stage view 告訴我們在不同的階段花費了多少時間，每個階段的建置狀態也用不同的顏色做標示。若將滑鼠移至不同的建置階段上還可以看到每個建置階段各自的 console output：

![](https://github.com/tsoliangwu0130/learn-ansible-and-jenkins-in-30-days/blob/master/images/jenkins-pipeline-07.png?raw=true)

至此，相信讀者多少可以體會 Pipeline 類型的專案能為開發團隊帶來多大的效益。相較於 Free-Style 專案，Pipeline 專案可以實現的建置邏輯又更加複雜，彈性也更大，因此若能掌握好 Pipeline 專案的設計，在持續整合的實踐上絕對會事半功倍。

#### Blue Ocean 專案

有鑑於 Pipeline 專案的成功，Jenkins 在 [2016](https://jenkins.io/blog/2016/05/26/introducing-blue-ocean/) 年首次釋出了 [Blue Ocean](https://jenkins.io/projects/blueocean/) 專案，進一步地提升了 Pipeline 的使用體驗。與其說 Blue Ocean 是一個全新的計畫，不如說它是 Jenkins Pipeline 的強化版。Blue Ocean 不但重新設計了 Jenkins 的使用介面，在操作上，對開發人員而言也更加友善、直覺，而也由於 Blue Ocean 釋出已有一段時間，穩定性的表現也比剛釋出時來得好許多。因此，接下來我將簡單介紹如何使用 Blue Ocean 來升級 Jenkins 的使用體驗。

> 註：目前 Blue Ocean 在中文化上還有許多地方尚未完成，因此若是使用中文瀏覽器的讀者目前在介面的翻譯上可能還是會看到一些簡體中文及中國用語。

Blue Ocean 的安裝方式相當簡單，我們可以直接透過 Jenkins 的插件管理系統直接安裝 Blue Ocean 的插件來使用：

![](https://github.com/tsoliangwu0130/learn-ansible-and-jenkins-in-30-days/blob/master/images/jenkins-pipeline-08.png?raw=true)

![](https://github.com/tsoliangwu0130/learn-ansible-and-jenkins-in-30-days/blob/master/images/jenkins-pipeline-09.png?raw=true)

由於 Blue Ocean 是相當龐大的插件，因此需要安裝的依賴插件也不少。在安裝過程完成後重新啟動 Jenkins，這時候在 Jenkins 主頁面應該會看到看到多出了 **Open Blue Ocean** 這個選項：

![](https://github.com/tsoliangwu0130/learn-ansible-and-jenkins-in-30-days/blob/master/images/jenkins-pipeline-10.png?raw=true)

點擊進入後，我們會被重新導向到一個全新的 Blue Ocean 介面：

![](https://github.com/tsoliangwu0130/learn-ansible-and-jenkins-in-30-days/blob/master/images/jenkins-pipeline-11.png?raw=true)

在這裡我們依然可以看到之前建立的專案。點擊專案可以輕鬆看到歷次專案紀錄以及每一次專案紀錄的細節：

![](https://github.com/tsoliangwu0130/learn-ansible-and-jenkins-in-30-days/blob/master/images/jenkins-pipeline-12.png?raw=true)

![](https://github.com/tsoliangwu0130/learn-ansible-and-jenkins-in-30-days/blob/master/images/jenkins-pipeline-13.png?raw=true)

除了一般的 Free-Style 專案，在 Pipeline 專案下的 stage view 也被直接升級成更清楚的流程圖：

![](https://github.com/tsoliangwu0130/learn-ansible-and-jenkins-in-30-days/blob/master/images/jenkins-pipeline-14.png?raw=true)

因此對於原本就有管理 Pipeline 專案的使用者而言，直接使用 Blue Ocean 基本上是完全無痛的。除此之外，對於要開發新的 Pipeline 專案的使用者而言，在 Blue Ocean 的設計下現在幾乎不用自己手動寫任何 Groovy 語法。作為示範，讓我們回到 Blue Ocean 首頁，並點選右上角 **新的 Pipeline** 來建立新的建置作業。接著，我們會被一步一步引導設填入需要的資訊：

![](https://github.com/tsoliangwu0130/learn-ansible-and-jenkins-in-30-days/blob/master/images/jenkins-pipeline-15.png?raw=true)

![](https://github.com/tsoliangwu0130/learn-ansible-and-jenkins-in-30-days/blob/master/images/jenkins-pipeline-16.png?raw=true)

> 註：建立 GitHub Access Token 時記得至少要選取所有 **repos** 以及 **user:email** 的 scopes。

![](https://github.com/tsoliangwu0130/learn-ansible-and-jenkins-in-30-days/blob/master/images/jenkins-pipeline-17.png?raw=true)

依序填完所需資訊後，若專案下沒有 **Jenkinsfile**，Blue Ocean 便會將頁面導向到 Pipeline 流程建置。首先選擇執行節點：

![](https://github.com/tsoliangwu0130/learn-ansible-and-jenkins-in-30-days/blob/master/images/jenkins-pipeline-18.png?raw=true)

接著點擊第二個小圓圈設定建置階段，如同前面介紹的一樣，每個建置階段可以由多個建置步驟組合而成，而在 Blue Ocean 友善的介面下，我們可以直接從 **Add Step** 的下拉式選單中選擇我們希望執行的動作：

![](https://github.com/tsoliangwu0130/learn-ansible-and-jenkins-in-30-days/blob/master/images/jenkins-pipeline-19.png?raw=true)

接著將剩下的 stage 依序依照需求都設置好，最後點擊右上角 **Save** 儲存離開。接著我們會看到以下提示：

![](https://github.com/tsoliangwu0130/learn-ansible-and-jenkins-in-30-days/blob/master/images/jenkins-pipeline-20.png?raw=true)

這表示 Jenkins 會將這個 Pipeline 配置好後自動產生的 `Jenkinsfile`，也就是 Pipeline 建置流程，直接提交到該專案目錄下作為紀錄。最後點擊 **Save & run** 來執行建置：

![](https://github.com/tsoliangwu0130/learn-ansible-and-jenkins-in-30-days/blob/master/images/jenkins-pipeline-21.png?raw=true)

我們可以看到效果跟我們之前使用原始 Pipeline 專案完全一致。唯一不同的是這次我們完全省去手動查找 Groovy 與 Pipeline 對應語法的繁瑣步驟，只透過 Blue Ocean 簡潔的 UI 就輕鬆打造一個全新的 Pipeline 專案。在 Blue Ocean 問世後，使用 Pipeline 專案實現持續整合的難度又再大大降低了，相信這對所有想要使用 Jenkins 實踐持續整合的團隊絕對是一大福音。
