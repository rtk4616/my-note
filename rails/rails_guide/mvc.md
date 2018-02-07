#### MVC 架構

MVC 是 Model, View, Controller 的簡稱，是一種軟體架構模式(architectural pattern)。

### 認識 MVC
* Model(資料模型)：操作資料庫。
* View(輸出介面)：呈現畫面資料。
* Controller(控制器)：控制樞紐，Model，View 還有 Route 都會跟他互動。

### MVC 資料流向

基礎簡易版本：Controller -> Model -> Controller -> View -> Controller

較詳細的版本：User -> Route -> Controller -> Model -> Database -> Model -> Controller -> View -> Controller

有很多 MVC 示意圖都是畫三四個方塊，然後彼此之間放箭頭表示資料的流向，我覺得那樣對初期來說太混亂了，先簡單點線性化就好。

簡單敘述：
1. User 在瀏覽器輸入網址
2. Route 接收網址後轉送給指定的 Controller
3. Controller 分析網址並執行對應的行爲，傳送要求給 Model
4. Model 根據要求操作 Database (資料庫)
5. Database 回傳所需資料給 Model
6. Model 把資料回傳給 Controller
7. Controller 把需要呈現的資料傳給 View
8. View 把呈現輸出畫面資料傳給 Controller
9. Controller 展示最終的結果給使用者

### MVC + Route 編寫順序
1. Model -> Controller -> View -> Route
2. Route -> Controller -> Model -> View
3. Model -> Route -> Controller -> View

兩種教學順序我都碰過，第一種 Model 開頭就是先處理資料格式，大致都是照 MVC 實際跑得流程下去寫。  

第二種是符合實際運作的順序，個人覺得順序挺重要的，可以觀察到 Model 優先權 > View，Controller 幾乎都是第二個就會開始碰，其實在編寫的過程中，會一直回頭到 Controller 改寫。

第三種(by Rong)：

```
先寫 Model ，你的資料是獨立的，他要先存在。
再來是依序是 RCV。
Route 先決定有哪些使用資料的方式。
Controller 是那些使用方式的內容。
最後 View 是出來的樣子。
所以如果是同一種資料的不同用法，就只會再加 RCV，不會動 Model。
```

___

補充資料：

#### Model logic
`Model logic`是用於表達與執行`商業邏輯`。  
> `model logic` ~= `business logic` 嗎？  
小好奇，去google一下，結果出來的都不叫 `model logic` 而是 `logic model` ...

好吧，keyword 就是 `model`, `business`, `logic`。

從`商業邏輯`來思考應該比較合適，也就是說商品爲了要賣出去，出自產品本身的需求或是規則即稱爲商業邏輯。  
`商業邏輯是獨立於網頁設計的`。  

像是購物類型的網站：VIP會員購物九折，訂單抽過某金額免運，生日會員發送折價券，都屬於商業邏輯。  
那我們的網頁爲了要配合商業邏輯就需要有相應的設計。  

#### Controller logic
> 丟去 google，跳出來比較多的似乎是 `MVC logic`。

我想 `controller logic` 就是 MV`'C'` 架構裡的 `Controller`的 logic 吧。  

如果把 controller 想成遙控器，那 `controller logic` 就會處理：  
你按了什麼按鈕？(送什麼參數給 controller ？)  
要對電視操作什麼部分的功能？(操作哪個 model？)  
該去哪個頻道？(該顯示哪個 view？)  

跟網頁相關的例子：  
使用者是否需要登入認證後才能看到網頁內容？

通常初學者都會把程式碼放在 controller，先讓網頁整個動起來，接著再重構。

## 爲什麼要認識 MVC？
* 重複使用已寫好的程式碼：同一套 model 不同的 view，可以因應各種場合的需求。
* 更容易維護程式碼：MVC三個區塊彼此獨立，不破壞原有架構下即可進行擴充或修改。
* 方便團隊合作：可將工程師，設計師的負責部分切開。
