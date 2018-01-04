# 節點介紹

由於 Jenkins 主要是用來幫助我們進行持續整合的工作，因此除了建置作業本身的表現之外，我們也必須考量如何在合理範圍內分配 Jenkins 的工作負載量 (workload) 以確保 Jenkins 可以保持在最佳的運作狀態下。在這個章節內，我將簡單介紹如何透過 Jenkins 的節點 (node) 概念來有效地進行任務分配。

#### 管理節點

在**管理 Jenkins** 的頁面下，找到並進入**管理節點**子頁面：

![](https://github.com/tsoliangwu0130/learn-ansible-and-jenkins-in-30-days/blob/master/images/jenkins-node-01.png?raw=true)

在這個頁面下，我們可以看到現在各個節點的使用狀況，目前我們只有一個 master 節點，也就是 Jenkins 主機本身。接著點選左邊的**新增節點**，並給與這個節點一個名稱：

![](https://github.com/tsoliangwu0130/learn-ansible-and-jenkins-in-30-days/blob/master/images/jenkins-node-02.png?raw=true)

進入下一步後，我們就可以開始設定這個 agent node。由於這邊大部分的欄位都蠻簡單明瞭，所以讀者可以根據自身需求設定即可。作為介紹，我這邊選擇使用最簡單的 **Launch agent via Java Web Start** 的啟動模式來操作 agent node，並將其**遠端檔案系統根目錄**設定在 `/Users/tsoliang/Desktop/testing-agent` 暫存目錄下：

![](https://github.com/tsoliangwu0130/learn-ansible-and-jenkins-in-30-days/blob/master/images/jenkins-node-03.png?raw=true)

除了 node 本身的設定外，我們也可以看到在下方還可以根據不同的節點來設置不同的環境變數或工具，這在未來管理節點上有相當大的幫助。設定完成後點選**儲存**離開。回到管理節點頁面後，我們應該會看到下面的狀態：

![](https://github.com/tsoliangwu0130/learn-ansible-and-jenkins-in-30-days/blob/master/images/jenkins-node-04.png?raw=true)

在整個頁面的左下角，可以看到現在已經有 master 跟 Testing Agent 這兩個節點在等待建置作業了。然而，因為我們尚未啟動 Testing Agent 這個節點，所以它目前還是處於離線不可用的狀態。為了啟動它，點擊該節點進入以下畫面：

![](https://github.com/tsoliangwu0130/learn-ansible-and-jenkins-in-30-days/blob/master/images/jenkins-node-05.png?raw=true)

此時可以看到 Jenkins 提供了兩種方式讓我們運行該節點，第一個方式相當容易，只要點擊 **Launch** 按鈕後，瀏覽器會自動下載一個 **slave-agent.jnlp** 的檔案，接著只要在 agent node 的主機上點擊這個檔案運行即可。但有時候我們無法在 slave 節點上使用瀏覽器，因此第二個方法可以讓我們透過使用命令列來啟動節點。首先先點擊上圖中用紅色框起來的 `slave.jar` 檔，下載完成後使用命令切換到下載目錄下執行整行指令，接著應該會看到類似以下訊息：

```
Jan 03, 2018 9:56:17 PM hudson.remoting.jnlp.Main createEngine
INFO: Setting up slave: Testing Agent
Jan 03, 2018 9:56:17 PM hudson.remoting.jnlp.Main$CuiListener <init>
INFO: Jenkins agent is running in headless mode.
Jan 03, 2018 9:56:17 PM hudson.remoting.jnlp.Main$CuiListener status
INFO: Locating server among [http://localhost:8080/]
Jan 03, 2018 9:56:17 PM org.jenkinsci.remoting.engine.JnlpAgentEndpointResolver resolve
INFO: Remoting server accepts the following protocols: [JNLP4-connect, CLI2-connect, JNLP-connect, Ping, CLI-connect, JNLP2-connect]
Jan 03, 2018 9:56:17 PM hudson.remoting.jnlp.Main$CuiListener status
INFO: Agent discovery successful
  Agent address: localhost
  Agent port:    50000
  Identity:      11:92:94:9d:fd:c0:cb:fd:00:79:d9:30:a4:97:55:d6
Jan 03, 2018 9:56:17 PM hudson.remoting.jnlp.Main$CuiListener status
INFO: Handshaking
Jan 03, 2018 9:56:17 PM hudson.remoting.jnlp.Main$CuiListener status
INFO: Connecting to localhost:50000
Jan 03, 2018 9:56:17 PM hudson.remoting.jnlp.Main$CuiListener status
INFO: Trying protocol: JNLP4-connect
Jan 03, 2018 9:56:17 PM hudson.remoting.jnlp.Main$CuiListener status
INFO: Remote identity confirmed: 11:92:94:9d:fd:c0:cb:fd:00:79:d9:30:a4:97:55:d6
Jan 03, 2018 9:56:18 PM hudson.remoting.jnlp.Main$CuiListener status
INFO: Connected
```

這代表現在運行這行指令的主機已經成功成為 Testing Agent 這個 agent 節點了，回到節點管理頁面，應該會看到剛剛的離線警告已經消失：

![](https://github.com/tsoliangwu0130/learn-ansible-and-jenkins-in-30-days/blob/master/images/jenkins-node-06.png?raw=true)

最後，回到任一專案的組態設定，在 **General** 欄位下勾選 **限制專案執行節點**，並填入剛剛的節點標籤：

![](https://github.com/tsoliangwu0130/learn-ansible-and-jenkins-in-30-days/blob/master/images/jenkins-node-07.png?raw=true)

設定完成後儲存離開，並執行專案建置。如果一切順利，我們應該可以在 Jenkins 主頁面下看到 agent node 被成功指派建置作業了：

![](https://github.com/tsoliangwu0130/learn-ansible-and-jenkins-in-30-days/blob/master/images/jenkins-node-08.png?raw=true)

透過管理節點來有效將工作分派給其他主機，是在管理 Jenkins 上相當好用的技巧。建議讀者可以試著多建立幾個節點進行練習，並根據需求來進行調整。
