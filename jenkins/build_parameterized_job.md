# 參數化建置

即便是同一個專案，有時候我們也會希望可以傳遞一些不同的變數來當做該次的建置參數。這時候我們就可以利用 Jenkins 的參數化建置來幫我們增加彈性。

#### 專案組態設定

建立一個新的 Free-Style 專案作為練習，任意命名完後進入專案組態。接著，在 **General** 的設定下勾選**參數化建置**的選項，如下圖所示，可以看到在這裡我們可以自由選擇各種不同的參數型態。

![](https://github.com/tsoliangwu0130/learn-ansible-and-jenkins-in-30-days/blob/master/images/jenkins-parameterized-build-01.png?raw=true)

無論選擇哪一種參數型態，Jenkins 在運作上都是將這些參數以環境參數的方式讓該次建置可以直接使用。由於使用各種不同參數在操作上沒有什麼太大的差異，這邊我就用**選擇**的參數型態來做為這次參數建置的示範：

![](https://github.com/tsoliangwu0130/learn-ansible-and-jenkins-in-30-days/blob/master/images/jenkins-parameterized-build-02.png?raw=true)

稍微解釋一下每個欄位的用途：

1. 名稱：`VAR` 即為接下來在建置過程中我們要呼叫這個參數的參數名稱
2. 選項：每一行代表一個選項的值，第一行為預設選項。
3. 說明：對於參數選項的詳細描述。

接著，到**建置**欄位新增建置步驟。這裡我們一樣選擇**執行 Shell** 的選項，並在**指令**欄位內新增：

```shell
echo "Building Environment: $VAR"
```

如同之前提到的，Jenkins 會將我們設定的參數以環境變數的方式作用在這次的建置作業中，所以在參數的使用上也相當直覺。完成後點擊**儲存**離開。

#### 建置結果

回到專案建置頁面後，我們會看到原本的**馬上建置**按鈕現在變成 **Build with Parameters** 了：

![](https://github.com/tsoliangwu0130/learn-ansible-and-jenkins-in-30-days/blob/master/images/jenkins-parameterized-build-03.png?raw=true)

點選 **Build with Parameters** 後，在下拉式選單中我們可以任意選擇這次建置作業的參數。接著，隨便選擇一個參數後點選**建置**：

![](https://github.com/tsoliangwu0130/learn-ansible-and-jenkins-in-30-days/blob/master/images/jenkins-parameterized-build-04.png?raw=true)

建置完成後，在 **Console Output** 的結果中就可以看到 Jenkins 成功地根據我們讓使用者傳入的參數進行建置：

![](https://github.com/tsoliangwu0130/learn-ansible-and-jenkins-in-30-days/blob/master/images/jenkins-parameterized-build-04.png?raw=true)
