# 餐廳論壇 PartB User Model
## 透過 Devise 套件建立登入系統
### 安裝 Devise 熱門 rails gem(套件)
編輯 Gemfile：
```log
# For user login
gem 'devise'
存檔，然後至終端機：
$ bundle install
$ rails g devise:install
Running via Spring preloader in process 8203
      create  config/initializers/devise.rb
      create  config/locales/devise.en.yml
===============================================================================

Some setup you must do manually if you haven't yet:

  1. Ensure you have defined default url options in your environments files. Here
     is an example of default_url_options appropriate for a development environment
     in config/environments/development.rb:

       config.action_mailer.default_url_options = { host: 'localhost', port: 3000 }

     In production, :host should be set to the actual host of your application.

  2. Ensure you have defined root_url to *something* in your config/routes.rb.
     For example:

       root to: "home#index"

  3. Ensure you have flash messages in app/views/layouts/application.html.erb.
     For example:

       <p class="notice"><%= notice %></p>
       <p class="alert"><%= alert %></p>

  4. You can copy Devise views (for customization) to your app by running:

       rails g devise:views

===============================================================================
```
然後會提示還有四個`一定`要手動完成的設定：
1. 設定寄信時預設的網站網址。(先跳過)
2. 路由設定首頁。(上一節已完成)
3. 在共同樣板中(layouts/application.html.erb)，加入動態提示訊息。(之後介紹)
4. 產生 devise 提供的預設樣板(views)，以便進一步客製化。(現在開始)

---
### 透過 devise 建立 user model
#### 產生 devise 提供的預設樣板(views)，以便進一步客製化。  
`rails g devise:views`

`rails g devise user`
```rb
$ rails g devise user
Running via Spring preloader in process 8695
      invoke  active_record
      create    db/migrate/20180203034130_devise_create_users.rb
      create    app/models/user.rb
      invoke    test_unit
      create      test/models/user_test.rb
      create      test/fixtures/users.yml
      insert    app/models/user.rb
       route  devise_for :users
```
`rails g devise user` 與 `rails g model user` 還挺像的，除了都有 migration file 外，
devise 還有特地至 route 最前面 加入 `devise_for :users` 設定。

#### 編輯 user migration file
```rb
# frozen_string_literal: true

class DeviseCreateUsers < ActiveRecord::Migration[5.1]
  def change
    create_table :users do |t|
      ## Database authenticatable
      t.string :email,              null: false, default: ""
      t.string :encrypted_password, null: false, default: ""

      ## Recoverable
      t.string   :reset_password_token
      t.datetime :reset_password_sent_at

      ## Rememberable
      t.datetime :remember_created_at

      ## Trackable
      t.integer  :sign_in_count, default: 0, null: false
      t.datetime :current_sign_in_at
      t.datetime :last_sign_in_at
      t.string   :current_sign_in_ip
      t.string   :last_sign_in_ip

      ## Confirmable
      # t.string   :confirmation_token
      # t.datetime :confirmed_at
      # t.datetime :confirmation_sent_at
      # t.string   :unconfirmed_email # Only if using reconfirmable

      ## Lockable
      # t.integer  :failed_attempts, default: 0, null: false # Only if lock strategy is :failed_attempts
      # t.string   :unlock_token # Only if unlock strategy is :email or :both
      # t.datetime :locked_at

      # for admin config
      t.string :role


      t.timestamps null: false
    end

    add_index :users, :email,                unique: true
    add_index :users, :reset_password_token, unique: true
    # add_index :users, :confirmation_token,   unique: true
    # add_index :users, :unlock_token,         unique: true
  end
end
```

在 create_table 裡增加欄位：
```rb
# for admin config
t.string :role
```

記得要下 `rails db:migrate`，並檢查 schema。

很好，我沒有把 :role 欄位寫到 migraiton file    
`rails db:rollback`：不愧是 devise，rollback 成功。  
再編輯一次 migration file，再 migrate 一次，成功。

---
### 設定 user controller 登入認證程序
編輯 app/controllers/restaurants_controller.rb  
最上面加入 `before_action :authenticate_user!`

這時候就能下 `rails s`，並去瀏覽器測試了，瀏覽器輸入 `0.0.0.0:3000` 後發現會被導向至  `0.0.0.0:3000/users/sign_in` ，登入頁面就這樣完成了...。

可以直接註冊，註冊完似乎會以登入狀態導向到首頁。

想測試就是 開無痕模式 再用剛才登入的資訊測試一次，因爲我們的登入登出按鈕還沒做 XD。

登入畫面是 Devise 預設的登入樣板：`app/views/devise/sessions/new.html.erb`，想美化可以從這個檔案下手。

---
### 加入使用者登入登出按鈕(使用者功能列)
使用者功能列隨著使用者登入或登出而變化。

而網站的功能列，一般是在整個網站的所有頁面都會顯示一樣的內容。
> 因此，要加入或修改整個應用程式的功能列，我們不會在一般對應 controller action 的樣板裡修改，而是會在整個應用程式的層次做修改：`app/views/layouts/application.html.erb`

```html
<% if current_user %>
  Hi, <%= current_user.email %>
  <%= link_to('登出', destroy_user_session_path, method: :delete) %>  |
  <%= link_to('修改密碼', edit_user_registration_path) %>
<% else %>
  <%= link_to('註冊', new_user_registration_path) %>  |
  <%= link_to('登入', new_user_session_path) %>
<% end %>
```

哦，不過我的 `application.html.erb` 裡面已經有東西了欸，我就加在 <body> 裡吧。

`rails s` 測試一下，成功。

<!-- 這邊竟然有邏輯介紹，我快哭了 -->
1. 在一開始先判段 current_user 是否有回傳使用者的 User 資料？  
`current_user` 是 Devise 定義的 help method。如果請求進入網頁的人是登入狀態，current_user 就會回傳該使用者的 User instance，若沒有登入，就回傳 `nil` (此時就會走到 else 路線)。
2. link_to 後面的參數是 `URL helper`，這是我們執行 `rails g devise user` 在 routes 加入的 `devise_for :user`所產生的。

---
### 在 view 加入使用者登入登出提示訊息
| Rails Helper | controller write | view read |
| :------------- | :------------- |:-|
| notice | flash[:notice] = "登入/登出成功" | <%= notice %> |
| alert  | flash[:alert] = "登入失敗/密碼錯誤" | <%= alert %> |

這寫法跟我之前試的有一些不一樣，不過 devise 已經準備好 controller 裡的 `登入提示訊息` 了，因此我們只需要去  
`app/views/layouts/application.html.erb`：  
```html
<p class="notice"><%= notice %></p>
<p class="alert"><%= alert %></p>
```

我是加在使用者功能列底下。

登入會有 `Signed in successfully.` 訊息。

密碼錯誤會有 `Invalid Email or password.` 訊息。
其他的還沒試出來。

下一篇我們來設計如何驗證網站管理員登入。
