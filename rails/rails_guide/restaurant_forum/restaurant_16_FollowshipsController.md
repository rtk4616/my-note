# 設定 Route 與 FollowshipController
- 美食達人頁面：列出所有的使用者，可以在此頁面上進行追蹤/取消追蹤
- 追蹤按鈕與功能
- 取消追蹤按鈕與功能

## followship route 設定
因爲會列出使用者，會用到 user#index 這個 Controller Action

### 設定美食達人頁面 route
```rb
# 將 :index 加入開啓項目
resources :users, only: [:index, :show, :edit, :update]
```

### 設定追蹤/取消追蹤 route
其實可以仿造之前用 member 的方式在 user 後面設定 route。

這裡我們用老方法：  
`resources :followship, only: [:create, destroy]`

---
### 設定 FollowshipController 與 view 基礎
`rails g controller followships`

#### User 總表 與基礎資料顯示
*user* controller 部分：  

```rb
def index
  @users = User.all
end
```

view 的部分，編輯 `users/index.html.erb`：  

```html
<ul>
  <% @users.each do |target_user| %>
    <li>
      <%= target_user.name %>
      <%= link_to "Follow" %>
    </li>
  <% end %>
</ul>
```

<!-- 結果我想不出此處的 path ...，果然很多同學提問，我覺得這個示範就有點硬要？因爲 button  url_path 寫法，與我們之前學到的慣例都不同。 -->

*後來想想，這邊取名要留意，取 user 太容易跟 當外鍵的 user_id 或是 操作網頁的使用者 current_user 搞混。因此從 user 改成 target_user*

---
### 實作 Follow action 按鈕
按下 Follow button 後，rails 已 POST 方法導向 followships#create，完成一筆追蹤記錄的新增。需要兩個資料：  
- user_id：發出追蹤要求的 user，也就是目前登入的使用者 `current_user`。
- follow_target_id：使用者想要追蹤的人，這個資訊來自在 `@users.each` 設定的變數 `target_user`

<!-- 等等，followship#create 跟 followship#destroy 都還沒做吧？ -->

Follow 按鈕：  
`<%= link_to "Follow", followships_path(follow_target_id: target_user.id), method: :post %>`

- followships_path 是 followships#create 對應網址的 Helper，我們在這個 Helper 後傳入一筆參數 `follow_target_id: target_user.id`
  - (也可以寫成 follow_target_id: target_user，只要寫入物件，Rails 會自動抓取主鍵的 value)。
- 這樣的 link_to，會產生 `http://localhost:3000/followships?follow_target_id={target_user.id}`這樣的網址。
- controller 實作 create action 時，就可以用 `params[:follow_target_id]` 取出網址攜帶的 target_user.id。

---
### 加入 Model 驗證：每個 User 只能追蹤另一個 User 一次
`特定的 user_id 下，每個 follow_target_id 都是獨立的。`

`Ruby on Rails 指南：用 uniqueness 方法，搭配 :scope 限制範圍。`

編輯 app/models/followship.rb

```rb
class Followship < ApplicationRecord
  # 每一筆追蹤記錄，屬於發出追蹤的使用者。
  belongs_to :user

  # 每一筆追蹤記錄，屬於被追蹤的使用者。
  belongs_to :follow_target, class_name: "User"

  # 在同一個 user_id 下，每個 follow_target_id 都是獨立的。
  validates :follow_target_id, uniqueness: { scope: :user_id }
end
```

**差點忘記，按鈕要做防止重複點選。**
`<%= link_to "Follow", followships_path(follow_target_id: target_user.id), method: :post, data: { disable_with: "Waiting..." } %>`

---
### 實作 followship#create
- 產生一個新的 Followship 物件，並設定兩個外鍵：user_id 與 follow_target_id
- 將攜帶資料的 Followship 物件存入資料庫。
- 如果通過 Model 驗證，成功儲存到資料庫裡，導回同一頁，並顯示成功訊息。
- 如果 Model 驗證失敗，也導回同一頁，並將錯誤訊息顯示給使用者。

```rb
class FollowshipsController < ApplicationController
  def create
    @followship = current_user.followships.build(follow_target_id: params[:follow_target_id])

    if @followship.save
      flash[:notice] = "Successfully followed."
      redirect_back(fallback_location: root_path)
    else
      flash[:alert] = @followship.errors.full_messages.to_sentence
      redirect_back(fallback_location: root_path)
    end
  end
end
```

- 產生新的 Followship 物件，build 是 new 的代稱。
- params[:follow_target_id] 會回傳一個數字。
- `current_user.followships.build(follow_target_id: params[:follow_target_id])`
  - 這樣寫是希望能貼近語義：`爲 current_user 產生一筆追蹤記錄，他追蹤的人 id 是 。。。`


---
### followship#destroy 與 "Unfollow" 按鈕

`<%= link_to "Unfollow", followship_path(target_user.id), method: :delete %>`

- 在 followships table 上找出一筆 user_id = current_user.id
  - follow_target_id = params[:id]
- 刪除該筆資料
- 導回上一頁，並顯示 flash message 給使用者

```rb
  def destroy
    @followship = current_user.followships.where(follow_target_id: params[:id]).first
    @followship.destroy
    flash[:notice] = "Followship destroyed!"
    redirect_back(fallback_location: root_path)
  end
```

<!-- 。。。。。根據同學的提問以及我個人的想法，我應該是會創 git branch 去把 resources 改成 member 的形式，主要是 create 的部分，很不合慣例，助教也回覆說改成 member ...，有空的話，總之未來別這樣自找麻煩。 -->

---
### 最後，依狀態顯示 follow/Unfollow 按鈕
- 如果 user 列表列出的是 `current_user` 自己的 `target_user`，不會顯示任何按鈕與資訊。
- 沒有追蹤過的 user 顯示 `Follow` 按鈕
- 已經追蹤過的 user 顯示 `unfollow` 按鈕

#### 對 current_user 不顯示 current_user 按鈕與使用者資訊
```html
<% if user != current_user %>
  <!-- Follow 按鈕 -->

  <!-- Unfollow 按鈕 -->
<% end %>
```

#### 依狀態顯示 Follow/Unfollow 按鈕
- 在 User Model 寫一個判斷方法 is_followed? 來檢查是否追蹤記錄已經存在。
- 利用 is_followed 在 `樣板` 來決定顯示 Follow/Unfollow

```rb
  # 檢查追蹤使用者記錄是否存在
  def is_followed?
    self.follow_targets.include?(target_user)
  end
```

這邊要注意的是 include 不像 find 或是 find_by ，他是直接拿整個使用者的資料去比對，因爲self.follow_targets 是回傳所有使用者追蹤過的人。

```html
<ul>
  <% @users.each do |target_user| %>
    <% if target_user != current_user %>
      <li>
        <%= target_user.name %>
          <% if current_user.is_followed?(target_user) %>
            <%= link_to "Unfollow", followship_path(target_user.id), method: :delete %>
          <% else %>
            <%= link_to "Follow", followships_path(follow_target_id: target_user.id), method: :post, data: { disable_with: "Waiting..." } %>
          <% end %>
      </li>
    <% end %>
  <% end %>
</ul>

```

### 最後，把按鈕程式碼提取成 Partial

編輯 `app/views/shared/_follow.html.erb`

```html
<% if current_user.is_followed?(target_user) %>
  <%= link_to "Unfollow", followship_path(target_user.id), method: :delete %>
<% else %>
  <%= link_to "Follow", followships_path(follow_target_id: target_user.id), method: :post, data: { disable_with: "Waiting..." } %>
<% end %>
```

---
### 假資料需要更新

套用樣板前還要先讓 User 有照片：  
- 然後就遇到本來用的好好的 `name: FFaker::Name.unique.name.first_name,`，開始有 error，好像是 FFaker 產生的字串出現問題：從 "Test.name" 變成 "Test name" 所以無法選擇 first_name。
- 而另一個問題是，舊的使用者不知道爲啥沒被刪掉？
  - comment 評論的關係？
  - 如果 User 已有評論，就不允許刪除帳號 (刪除時拋出 Error)  
  has_many :comments, dependent: :restrict_with_error
  - 這邊設定是不是不合理？當初應該只是爲了測試？
  - 這樣子刪除東西還要重新排順序，不然會不給刪，而且應該要直接跳出 error 比較好才是。
  - 還是說都要乖乖手動先刪？
  - 也就是說要先從底下的資料 評論>使用者>餐廳>分類 這樣刪上去？
    - 改成 has_many :comments, dependent: :destroy
    - 再跑一次 rake dev:fake_users 成功，所以我的 admin account 也被刪了。這一點是不是要避免？
    - 然後我跑 rake db:seed 發現餐廳分類就重複了。。。
    - 這樣也太累，是我 假資料分太細了嗎？而且似乎要有提示訊息。
    - 就是如果刪除資料時有錯誤，程式就該要停了。

_這坑留著吧。_

---
### 前端套版
恩，，，算是擠出來了

比較特別的應該是：  
```html
<div class="container">
  <div class="row">
    <ul class="nav nav-tabs">
      <li role="presentation" class="<%= "active" if current_page?(restaurants_path) %>">
        <%= link_to "首頁", restaurants_path %>
      </li>

      <li role="presentation" class="<%= "active" if current_page?(feeds_restaurants_path) %>">
        <%= link_to "最新消息", feeds_restaurants_path %>
      </li>

      <li role="presentation" class="<%= "active" if current_page?(ranking_restaurants_path) %>">
        <%= link_to "TOP10 人氣餐廳", ranking_restaurants_path %>
      </li>

      <li role="presentation" class="<%= "active" if current_page?(users_path) %>">
        <%= link_to "美食獵人", users_path %>
      </li>
    </ul>

  </div>
</div>

```

- 如何設定條件出現 class="active"，卡蠻久的，一開始還很天才的在 link_to 那邊加，然後發現沒反應，current_page? 似乎一定要搭配問號。
