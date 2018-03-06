<!-- 第二天進度應該可以加速了吧？ -->
第一天照理說應該要完成 55/7 = 8 項測試
昨天少一項，因此進度真的很差也很亂。

照著筆記跑我覺得還 OK 嗎？

婀，不 OK 唷

我是不是要先把推文的建立弄出來？跟餐廳論壇不太一樣的地方在於
tweets 的 admin 似乎不用做太多事

# **CREATE 一筆推文資料**
- view 需要表單填寫
- controller 需要設定 Strong parameters
  - create 需要存檔
  - 存檔成功或失敗 給予提示

是不是一開始就要設定關聯了？
_一個使用者能有很多推文_
推文跟上一個學的 餐廳 comment 最像了吧

好多事情要做，卻不知道從哪下手比較好
_先把GEM bootstrap 裝一裝吧_  
有在考慮要不要試用新版的 bootstrap 4.0

哦，有 github 大大弄教學啦，照著弄成功
步驟跟本來的有點不一樣
有惹
**安裝 bootstrap 4.0**
也拖太久
1100 才裝好 = =

下一步勒？先弄 tweet = comment ?
還是先弄假資料，不過 CRUD 的話，要先 create 吧，還是假資料也算 create 哈哈哈

太混亂了，也就五個 model ，核心是 tweet
不過我們最先弄的應該是 user？
所以：
1. users
  1. 目前已經能夠註冊登入(依靠 devise)
  2. 已經能夠要求登入
  3. 已經能夠要求登入並判斷是否爲管理員 admin
  2. devise 註冊及登入頁面美化？
    1. 登入頁面美化(devise/sessions/new.html.erb)
    1. 註冊頁面美化(devise/registration/new.html.erb edit.html.erb)
    2. 導覽列連結美化
  3. 使用者資料瀏覽(show) 與編輯(edit + update)
    1. 使用者編輯按鈕位置在`個人 tweets 頁面` /user/:id/tweets (目前暫時放在導覽列)，已創好 *done*
    2. controller action #edit *done*
    3. controller action #update *done*
2. tweets
3. replies
4. likes
5. followships

這樣列好多了。。。

*bootstrap 4.0 更動*
- 4.0 navbar-text 要用 span ， p 會讓 navbar-text 後面的格式跑掉
- navbar 靠右用：原本的 pull-right 與 navbar-right 都沒反應
  - <ul class="nav navbar-nav ml-auto">
**遇到問題直接 google 比較快，效率高太多了**
不然一個導覽列弄半小時，像話嗎？

---
*遇到 undefined method `user_path' form_for @user*
似乎算是非常常見的問題，結果是 routes users#create 沒寫到 = =，搞笑。

---
OK，所以 user model 算告一段落。

再來是 tweets 類似於餐廳評論
- tweets
  - routes 目前只有 create 跟 index, destroy 跟 edit 不需要？
  - model 關聯
    - tweets 屬於 使用者
      - tweet belongs_to user
    - 一個使用者能有很多個推文
      - user has_many tweets
  - controller
    - create done
  - view

tweet#create 卡很久，思路明顯不清晰。 還猜錯 code 的意思，餐廳評論跟使用者還有餐廳都有關係(`多對多`)，不只是跟使用者而已，所以 form_for [@restaurant, @comment] 那邊會不一樣。

而餐廳分類跟餐廳，在新增餐廳的時候的 form_for [:admin, @restaurant] 的 :admin 是因爲 routes 在 admin/restaurant 之後
### tweets#create
找出觸發 create 的使用者，但是 params 沒參數，這時候用 current_user 就有值？
但是在 index 會沒有？是因爲 index 不是由 使用者去觸發的嗎？他只是單純的 GET
> 究竟何時才能用 current_user ? 目前是能動了，不過算是賽道的

user 與 tweet 只是單純的 一對多？
要類比是拿餐廳分類跟餐聽來類比。

使用者-推文回覆-推文，才是多對多，才能參考使用者-餐廳評論-餐廳的例子

*使用者可以在首頁新增推文*
