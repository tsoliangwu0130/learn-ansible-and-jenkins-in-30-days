# 使用機密檔案維護 Jenkins 的操作安全性

在真正的專案開發過程中，我們很多時候會遇到需要提供設定檔 (configuration file) 來驅動專案建置的狀況。舉例來說，如果今天我們要開發一個 API Demo 的應用程式，為了使開發的產品可以順利對 API 發送請求，我們可能要提供一組 API token 在發送時作為驗證使用。這種類似於 API token 的資料往往會需要我們特別去註冊或是申請才可以得到，所以這種資料並不適合放在公開平台上且需要被妥善保管。從前面的章節中我們可以知道，若是透過 Jenkins 來幫助我們自動化地建置專案，所有專案相關的檔案都會被存放在 `workspace` 個工作目錄下
，這意味著若今天我們希望 Jenkins 幫我們建置這種類型的專案，如果不做設定上的調整，專案建置後所有與專案相關的機密檔案都可以被所有 Jenkins 的使用者透過 `workspace` 資料夾輕易地瀏覽，而這就是一個系統上的安全漏洞。為了避免這種狀況，我將在本章介紹如何安全地在 Jenkins 中使用機密檔案以同時兼顧專案的建置流程與系統安全性。

#### 利用 Jenkins 的建置環境來設定機密檔案

進入 `ansible_lint` 的專案配置，在 `Build Environment` 的欄位下，勾選 `Use secret text(s) or file(s)` 的選項：

![use_secret_file_for_safety_01](https://github.com/tsoliangwu0130/learn-ansible-and-jenkins-in-30-days/blob/master/images/use_secret_file_for_safety_01.png?raw=true)

我們會看到這時候介面跳出了一個 `Bindings` 的欄位，在該欄位下點開下拉式選單，選取 `Secret file`：

![use_secret_file_for_safety_02](https://github.com/tsoliangwu0130/learn-ansible-and-jenkins-in-30-days/blob/master/images/use_secret_file_for_safety_02.png?raw=true)

在這個欄位中，如同前面章節介紹過的建置參數一樣，我們要先給我們的機密檔案一個變數名字 (e.g. `CONFIGURATION`) 以便接下來的操作。命名完後，點選 `Credentials` 下的 `Add`，接著選擇 `Jenkins`：

![use_secret_file_for_safety_03](https://github.com/tsoliangwu0130/learn-ansible-and-jenkins-in-30-days/blob/master/images/use_secret_file_for_safety_03.png?raw=true)

接下來我們會進入 `Jenkins Credentials Provider` 的彈出視窗，我們可以在這裡新增機密檔案與管理權限範圍 (scope)。我們可以試著在桌面建立一個 `configuration.txt` 的檔案並輸入以下內容作為測試：

```shell
API TOKEN: abc123xyz
```

接著透過網路介面上傳至 `Jenkins Credentials Provider` 內如下：

![use_secret_file_for_safety_04](https://github.com/tsoliangwu0130/learn-ansible-and-jenkins-in-30-days/blob/master/images/use_secret_file_for_safety_04.png?raw=true)

點選 `Add` 後我們應該就可以看到機密檔案已經被新增至清單中：

![use_secret_file_for_safety_05](https://github.com/tsoliangwu0130/learn-ansible-and-jenkins-in-30-days/blob/master/images/use_secret_file_for_safety_05.png?raw=true)

接著，我們試著在 `Build` 中加入一行 `cat $CONFIGURATION` 指令看看有沒有辦法透過環境變數來正確讀取設定檔的內容：

![use_secret_file_for_safety_06](https://github.com/tsoliangwu0130/learn-ansible-and-jenkins-in-30-days/blob/master/images/use_secret_file_for_safety_06.png?raw=true)

設定完畢後，儲存並建置專案，接著到 `Console Output` 觀察建置結果：

![use_secret_file_for_safety_07](https://github.com/tsoliangwu0130/learn-ansible-and-jenkins-in-30-days/blob/master/images/use_secret_file_for_safety_07.png?raw=true)

Jenkins 正確地透過我們設置的機密檔案讀取到了內容（當然，在一般的專案過程中你並不該這樣使用），同時，在 `Console Output` 中也會將機密檔案的檔名用星號的方式馬賽克處理掉。接著，我們可以去 `workspace` 中觀察一下該檔案是否有被儲存：

![use_secret_file_for_safety_08](https://github.com/tsoliangwu0130/learn-ansible-and-jenkins-in-30-days/blob/master/images/use_secret_file_for_safety_08.png?raw=true)

從 `workspace` 的目錄下我們可以看到雖然 `configuration.txt` 參與了這次的建置過程，但在建置完成後 Jenkins 就會將該檔案從我們的專案工作目錄中自動移除，以避免不相關的使用者能夠隨意存取機密檔案。
