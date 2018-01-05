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

    在定義階段後，我們可以更近一步定義在該階段下的建置**步驟**。

若以這三個核心概念為基礎，我們可以將上面的範例改寫成以下這樣：

```groovy
pipeline {
    node('master') {
        stage ('Checkout') {
            steps {
                git 'https://github.com/tsoliangwu0130/my-ansible.git'
            }
        }

        stage ('Build') {
            steps {
                sh '''for file in $(find . -type f -name "*.yml")
                do
                    ansible-lint $file
                done > ansible-lint.log'''
            }
        }

        stage ('Delivery') {
            steps {
                echo 'Publish artifact over SSH.'
            }
        }
    }
}
```

這裡我又額外增加了 Delivery 這個建置階段，來將使用 ansible-lint 的結果當作輸出產物遞送到目標伺服器上。通常來說，我們會將這種定義好的 Pipeline 建置腳本儲存在 `Jenkinsfile` 檔案內來做管理。
