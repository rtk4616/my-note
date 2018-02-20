# 顯示使用者評論過的餐廳
- 使用 `has_many :through`，來建立 User 與 Restaurant 間的多對多關係。
- 跨資料表搜尋
- Bootstrap 套版
- CSS 排版

## 實作需求
- 在個人資料(Profile)下方，顯示 使用者評論過的餐廳列表。
- 列表不能有重複的。
- 點擊餐廳可以連線到餐廳頁面。

### 建立多對多關聯
`使用者評論過的餐廳`：  
- 一個 User 評論過很多 Restaurant：一個 User 有很多 Comment
- 一個 Restaurant 被很多個 User 評論：一個 Restaurant 有很多 Comment

這是多對多的關係：User-1_M-Comment-M_1-Restaruant

我們目前在 `app/models/user.rb` 只有 `一對多` 設定，只有跟 Comment 的關聯：
```rb
# User has_many comments.
has_many :comments
```

現在我們希望 User 與 Restaurant 建立關聯：
```rb
# 實現多對多關聯
has_many :restaurants, through: :comments
```

到這邊，回頭去翻每個 model 裡的關聯設定，我覺得我真的需要每次增加 Model 就得要更新關聯圖了 XD，不然一定忘記。

多對多關聯設定好之後，就可以在 UserController 的 show action 透過關聯取得 `評論過的餐廳資料`：
```rb
def show
  # before_action
  # 使用者評論過的餐廳
  @commented_restaurants = @user.restaurants
end
```

這樣子我們就取得要顯示的餐廳資料了，至於可能重複的部分，或許可以透過 unique 方法？不知道是不是 FFaker gem 獨有的。

歐，還有提示當初解 Leetcode 有一題有解到，關鍵是排序，可能可以用 id 排？

我覺得我先弄後端的部分好了，結果先用文字呈現(反正我的假圖片也還沒上)，這樣子假資料評論的部分也要刻意增多讓他們重複才行。
> 不用，用 admin 帳號對某間餐廳留幾個評論即可。

哇，光研究個 method 就拖好久，餐廳是否重複怎麼判斷，先排序，然後判斷現在跟過去的是否一樣？  
有 uniq method

但是 find 好像只會回傳一筆，回傳所有的他又只會搜尋 id

```rb
@user = User.find_by(name:"Admin")
@user.restaurants.each do |restaurant|
  puts restaurant.name
end
```

OK，這樣會有重複的，`@user.restaurants.uniq.each do |restaurant|`，用 uniq method 搞定重複的部分。

阿這個部分也可以直接寫在 controller 裡，資料有了，來處理 View。

---
### 使用者評論過的餐廳 View
顯示餐廳名字並做成連結

```html
<% @commented_restaurants.each do |restaurant| %>
  <%= link_to "#{restaurant.name}", restaurant_path(restaurant.id) %>
  <br>
<% end %>
```

---
### 15 章總結
正好六天，但是 Bootstrap 前端的坑，複雜度太高惹，可能要對前端重新做筆記了。
