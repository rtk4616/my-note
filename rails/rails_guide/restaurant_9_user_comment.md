# 餐廳使用者留言(餐廳 show 頁面)
讓使用者可以在個別餐廳資料頁面(restaurants/show)，可以`新增`，`瀏覽`，`刪除`評論。
<!-- 編輯可以自己練習，應該就跟餐廳分類一樣的模式 -->

### 建立留言(Comment)資料模型
`rails g model Comment`

### 設計 comments table
| Primary Key |       | Foreign Key | Foreign Key |
| :------------- | :------------- | :- | :- |
| id  | content | restaurant_id   | user_id |
|1   |   |   |   |
|2   |   |   |   |
|3   |   |   |   ||

- id：PK，Rails 預設會建立。
- content：使用者評論內容，形態爲 text。
- restaurant_id，作爲FK，讓評論可以和 restaurants table 建立關聯。
- user_id，作爲FK，讓評論可以和 users table 建立關聯。

```rb
class CreateComments < ActiveRecord::Migration[5.1]
  def change
    create_table :comments do |t|
      t.text :content
      t.integer :restaurant_id
      t.integer :user_id

      t.timestamps
    end
  end
end
```

`rails db:migrate`

觀察 schema 確認。

---
### 設定使用者留言與使用者以及餐廳資料表關聯
使用者留言產生的關聯：
- 評論屬於某位使用者，每位使用者可能有很多評論。
  * `comment belongs_to :user`
  * `user has_many :comments`
- 評論屬於某家餐廳，每家餐廳可能有很多評論。
  * `comment belongs_to :restaurant`
  * `restaurant has_many :comment`

各自到 app/models/comment.rb, user.rb, restaurant.rb 加入資料表關聯設定。
comment.rb：
- belongs_to :user
- belongs_to :restaurant
user.rb
- has_many :comments
restaurant.rb
- has_many :comments

`rails c `測試：
```rb
# 新增一筆餐廳評論
restaurant = Restaurant.all.sample
restaurant.comments.create(content: "test", user: User.all.sample)

# 新增一筆使用者評論
user = User.all.sample
user.comments.create(content: "test2", restaurant: Restaurant.all.sample)

# 查詢某位使用者評論
user.comments

# 查詢某餐廳評論
restaurants.comments
```

---
## 設定評論 Route 與 Controller
- 宣告 Route 巢狀資源(Nested Resources)來取得必要的父層資源。

### 設定 comments route
根據 User stories，目前我們需要 create 和 destroy 兩種資源：
- 使用者可以在餐廳個別資料下留評論。 comments#create
- 使用者可以在餐廳個別資料下瀏覽其他人的評論。
- 管理者可以刪除任何人的評論。 comments#destroy

可以觀察到使用者的`評論`是屬於某間`特定餐廳`的資料，因此 route 設計可以讓 comments 依附在 restaurants 底下(巢狀資源 Nested Resources)。

> 爲什麼只有 :create，那 :new 呢？
前面餐廳分類的部分好像也沒有 new action ?(真的耶，是我當時沒發現的關係。。。)

- new 沒有修改資料的行爲，new 會從 Model 得到一筆空白 Record，包含了欄位資料等等，用的 HTTP Verb => GET。
- create 則是基於已有的資料，傳送回 server，請求新增一筆記錄，用的 HTTP Verb => POST。
> 所以在生成回應表單的時候(new.html.erb)，是由 `new action` 開始，顯示出的 `new.html` 包含需要的資料表欄位資訊，讓使用者填入資料，使用者按下送出鈕之後，將欄位訊息送給 `create action` 完成新增一筆資料。  
> 上面所提到的 new 與 `我們目前新增評論的情況不一樣，新增評論並不是使用者送出的 一個 request 到 Rails server`，而是在 restaurant 的 show.html 生成的 request ，也就是說使用者按下送出鈕會去找 restaurants 而不是 comments 的 new。

<!-- 下一節再複習一次很不錯啊。 -->

因此，還是有 new ，但是只是 `Comment.new` 不是 comments#new action，會在 restaurants#show 執行。

```rb
resources :restaurants, only: [:index, :show] do
  resources :comments, only: [:create, :destroy]
end
```

---
## 設定 comments controller
`rails g controller comments`

### comments#create
- 運用巢狀路由與 `form_for` 表單，新增一筆資料
- 透過 has_many 關聯方法新增資料
- 判讀新增資料的 server log

因爲是在 restaurants show 頁面新增 comment 資料，因此需要在 show 頁面補點變數：  
```rb
def show
  @restaurant = Restaurant.find(params[:id])
  # 新增使用者評論
  @comment = Comment.new
end
```

接著到 comments_controller.rb：
```rb
class CommentsController < ApplicationController
  def create
    # 帶入 :restaurant_id 確認餐廳物件，觀察 rails routes restaurant_comments 的 URI pattern
    @restaurant = Restaurant.find(params[:restaurant_id])

    # Strong Parameters 驗證
    @comment = @restaurant.comments.build(comment_params)

    # 取得另一個 FK：讓現在登入的使用者爲評論的使用者
    @comment.user = current_user
    @comment.save!

    redirect_to restaurant_path(@restaurant.id)
  end

  def destroy

  end

  private
  def comment_params
    params.require(:comment).permit(:content)
  end
end
```

接著再到 restaurants/show.html.erb 把新增評論加到最後：  
```html

<!-- for user comment -->
<div class="container">
  <div class="row">
    <!-- 新增評論表單 -->
    <%= form_for [@restaurant, @comment] do |f| %>
      <div class="form-group">
        <%= f.text_area :content, placeholder: "請留言", class: "form-control" %>
      </div>

      <div class="form-group">
        <%= f.submit class: "btn btn-primary" %>
        <%= f.button "Clear Comment", type: :reset, class: "btn btn-default" %>
      </div>
    <% end %>

  </div>
</div>
```

記得表單陣列參數裡的 `實例變數順序` 會影響後續送出表單的目的地路徑：  
- 若寫成 [@comment, @restaurant] 會輸出 `comment_restaurant_path`，就會出現 NoMethodError。

---
### 瀏覽餐廳 comments
確認需要的項目與資料：
- 評論者名稱：我們的 user 目前沒有 name 欄位，老實說挺奇怪的 XD
- 評論內容：透過關聯方法直接取值，不用再去 controller 設定，因此只要有`@restaurant`就行了，`@restaurant.comments`。
- 評論時間：可以從 預設的 `created_at` 下手，然後可以回去看一下當初 todolist 怎麼寫的(還順便發現錯誤 XD)，常見的方式還有用 time_ago_in_words。

編輯 `restaurants/show.html.erb`，放在新增留言上面：
```html
<!-- 顯示留言 -->
<% @restaurant.comments.each do |comment| %>
<div>
  <h4><%= comment.user.email.split("@").first %></h4>
  <p><%= simple_format comment.content %></p>
  <p class="text-muted">
    <em><%= comment.created_at.strftime("%Y-%m-%d")
 %></em>
  </p>
</div>
<% end %>
```

---
### 管理員可以刪除使用者評論
有兩個地方可以驗證：
- controller 追加管理員權限驗證。
- view show.html.erb 也能用 Ruby 條件判斷要不要顯示刪除按鈕。

#### comments_controller.rb
```rb
def destroy
  if current_user.admin?
    # 先找到目前瀏覽的餐廳，因爲待會把留言刪了就沒辦法找到本來在瀏覽哪個餐廳了
    @restaurant = Restaurant.find(params[:restaurant_id])

    @comment = Comment.find(params[:id])
    @comment.destroy

    # 重新導向 到 當前的餐廳 show 頁面
    redirect_to restaurant_path(@restaurant.id)
  end
end
```

```html
<% if current_user.admin? %>
  <%= link_to "Delete", restaurant_comment_path(@restaurant, comment), method: :delete %>
<% end %>
```

---
## 產生假的 User 與 Comment 資料
<!-- 有給參考的 CODE，我覺得還行。 -->
假資料目前分成三個部分：
- 假餐廳資料，已完成！改指令名稱即可：`rails dev:fake_restaurants`
- 假使用者，待完成：`rails dev:fake_users`
- 假評論，待完成：`rails dev:fake_comments`
  - 要注意 comments 有與另外兩個資料表關聯，因此兩個 FK：`restaurant_id` 與 `user_id` 欄位不能為 `nil`。

編輯 `lib/tasks/dev.rake`
```rb

  # for fake user data
  task fake_users: :environment do
    # 這邊 User 砍下去，admin 也會被砍掉，能用 except 保留嗎？或是之後再用 rake db:seed
    User.destroy_all

    10.times do
      # 忘記欄位回去翻 schema.db
      User.create!(
        email: FFaker::Internet.email,

        # 神奇的事情發生了，我去 schema 看 明明這欄位就叫 `encrypted_password`
        # 但是錯誤回報是給 ActiveRecord::RecordInvalid: Validation failed: Password can't be blank
        # 是 Password
        # encrypted_password: FFaker::Name.unique.name
        password: FFaker::Name.unique.name
      )
    end

    puts "Rake and FFaker have created fake users."
    puts "Now we have #{User.count} users."
  end

  # for fake user comment
  task fake_comments: :environment do
    Comment.destroy_all

    # 要記得 comment 無法獨立存在，因爲 comment 一定是 user 對某個 restaurant 的評語
    # 因此思考跟切入的點要注意
    # 這個做法是逐一掃過每間餐廳爲每個餐廳建立 1 個評論，共做 3 次，因此每間餐廳會有 3 筆評論
    # 有同學分享其他想法跟做法，讚
    3.times do
      Restaurant.all.each do |restaurant|
        restaurant.comments.create!(
          user_id: User.all.sample.id,
          content: FFaker::Lorem.sentence
        )
      end
    end

    puts "Rake and FFaker have created fake comments."
    puts "Now we have #{Comment.count} comments."
  end

```

記得 push 到 heroku 的時候還要執行

```bash
heroku run rails db:migrate
heroku run rails dev:fake_restaurants
heroku run rails dev:fake_users
heroku run rails dev:fake_comments
heroku run rails db:seed
```

發現了一些 假資料 跟 seed 資料上的順序問題，分類跟餐廳和使用者有關聯，因此 seed 餐廳分類跟 admin 帳號資料要先跑。

跑完才去建假餐廳資料，因爲目前設計 假餐廳資料建立時，要先去隨機選取餐廳分類，所以餐廳分類要先出來。

這樣子的話，順序會變成：
```bash
rails dev:fake_users
rails db:seed
rails dev:fake_restaurants
rails dev:fake_comments
```
