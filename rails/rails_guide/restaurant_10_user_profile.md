# 使用者 個人資訊頁面 User Profile
<!-- 這邊的教材說實話，令人感到意外的 好，明顯不同人做的，我還以爲會是放生流。 -->
- 根據客戶(教材)規格要求，想辦法生出程式碼符合預期結果
- 我們使用者登入系統是靠 Devise 套件做的，研究並練習客製化使用者註冊欄位

要求：
- 在前台導覽列上方，新增一個 個人資訊(Profile)連結
  - 此連結會連到 個人資料頁面 (users#show)
  - 點擊 Edit Profile 後，使用者可以編輯個人資料 (users#edit)
  - 使用者只能編輯自己的資料

資料模型規格：
- name, string, 使用者名稱
- intro, text, 使用者自我介紹
- avatar, string, 使用者大頭貼相片

路由設計：
- users#show
- users#edit
- users#update

還有指定頁面樣式設計以及測試內容

延伸挑戰：
<!-- 還幫你都先列出來 -->
- 餐廳 show 頁面評論的使用者名稱，能否一併更新？
  - 調整使用者 name 屬性需要出現的地方。
- 之前已經(註冊)存在的使用者資料，欄位爲 nil，如何解決這個問題？
- 客製化 Devise 註冊程序。

---
### 確認現況
其實這部分一開始就能做只是我們選擇先跳過
- User model，已有 devise 欄位 以及 comments 關聯
- users controller
- users routes
- users views

應該都要重新弄過，而且過去應該也有做過幾次了。

Model > Route / Controller > View

---
### User Model
婀，不對，有 User model 了

`rails g migration add_columns_to_User`

新增三個指定欄位
```rb
def change
  # add 3 columns to User
  add_column :users, :name, :string
  add_column :users, :intro, :text
  # 使用者大頭貼
  add_column :users, :avatar, :string
end
```

---
### User Route
應該是要在現有的欄位上改就行
`resources :users, only: [:show, :edit, :update]`


---
### User Controller
`rails g controller users`

參考 restaurants_controller.rb
```rb
class UsersController < ApplicationController
  before_action find_user, only: [:show, :edit, :update]

  def show
    # find_user
  end

  def edit
    # find_user
  end

  def update
    # find_user
    if @user.update(user_params)
      flash[:notice] = "User data was successfully updated."
      redirect_to user_path(@user.id)
    else
      flash.now[:alert] = "User was failed to update!"
      render :edit
    end
  end

  private
  # for before_action
  def find_user
    @user = User.find(params[:id])
    return @user
  end

  # for strong parameters
  def user_params
    params.require(:user).permit(:name, :intro, :avatar)
  end
end
```

實際這樣寫起來感覺真的超快的

---
### User View
這應該是最麻煩的部分吧。

在前台導覽列上方，新增一個 個人資訊(Profile)連結：  
編輯 `app/views/layouts/application.html.erb`

<li><%= link_to("Profile", user_path(current_user.id)) %></li>

- show.html.erb  
  > before_action `:`find_user, only: [:show, :edit, :update]  

  注意那個冒號...
- edit.html.erb

看排版是左邊是大頭貼，右邊是文字資料 + edit 按鈕

所以可以用表格？

我覺得先別想一次到位，我還沒這麼厲害

先把該有的資料顯示出來

再一個一個美化

`touch app/views/users/show.html.erb`
```erb

使用者大頭貼 <br />
<%= image_tag @user.avatar, width: "300px" if @user.avatar %>

<h1>使用者名稱：<%= @user.name %></h1>
<h3>使用者信箱：<%= @user.email %></h3>
<h4>信用者自我介紹：<%= @user.intro %></h4>

<!-- 若使用者檢視其他使用者頁面時，不顯示編輯按鈕 -->
<% if current_user == @user %>
  <%= link_to 'Edit', edit_user_path(@user.id) %>
<% end %>

```

`touch app/views/users/edit.html.erb`  
```erb
<h3>Edit Profile</h3>

<%= form_for @user do |f| %>
  <%= f.label :name, "Name" %>
  <%= f.text_field :name, class: "form-control" %>

  <%= f.label :intro, "自我介紹" %>
  <%= f.text_area :intro, class: "form-control" %>

  <%= f.label :avatar, "大頭貼上傳" %>
  <%= image_tag @user.avatar if @user.avatar? %>
  <%= f.file_field :avatar %>

  <%= f.submit "Update", class: "btn btn-primary" %>
  <%= link_to "Cancel", user_path(@user.id), class: "btn btn-default" %>
<% end %>

```

結果 admin show 頁面就卡有點久。

而且若大頭貼爲空就會出現問題，教材是先給個方塊佔著位置，我是先弄個 if。

OK，抄了點 code 跟 debug 後，基礎流程還算可以，只是美化跟細節要特別注意。

先 commit 我自己的版本，然後來記錄一下，看了教材的 commit 之後，發現這次編輯順序很值得參考：
- 設定路由。
- 新增 user model attributes。
- 編輯 controller，就那個 before_action 後面的 method 記得加 `:` 冒號。
- 再來是 view 的部分：
  - show：不要想一次到位，把 view 切多個部分比較好，沒有資料的地方先用東西擋著是個好主意(像是上傳圖片的部分)，可以先注重在整體排版跟基礎資料顯示，所以沒有的資料甚至先不顯示也是OK的。
  - 導覽列的連結要去 layouts/application.rb 增加，資料就是應用 current_user。
  - edit 與 update。
  - 圖片上傳功能複習：
    - 要到 model 去 載入 gem 的圖片上傳器，若想跟之前餐廳圖片分開的話要重新下指令：使用 CarrierWave 上的工具包，產生一個圖片上傳器(Uploader)，並命名此 Uploader 爲 `avatar`：  
    `rails g uploader avatar`
    - 我是暫時先用之前的。
    - 然後 show 顯示大頭貼的語法以及 上傳大頭貼的語法都很簡單。
  - 權限控制：使用者只能編輯自己的 Profile，這個要在兩個地方改，一個是 view ，另一個是 controller，善用 current_user 與 @user 驗證。  
    `@user == current_user`
  - view 頁面 bootstrap 美化，這個部分真的有點太捆擾我，最混亂&不熟的部分，甚至還要特地花時間回去複習。

還有因應這些改動而導致先前的某些部分也要更動：  
增加 User Model 後的待辦(更新)事項：  
1. 調整 user name 欄位需要出現的地方
  1. 更新餐廳 show 頁面評論的使用者名稱。
  2. 待補充。。。
2. 待解決舊的 User 資料 name 欄位爲 nil 的問題：3.3 的另一個手動做法。
  1. seed 與 fake data 是否因應使用者欄位增加而需要更新？
  2. seed 應該不用？admin 帳號的部分可能要。
  3. fake data 需要更新。
3. 客製化 Devise 註冊功能，讓使用者在註冊時就能新增 name 欄位。
  1. 註冊時的欄位屬性是否需爲必填？
  2. 必填與不必填的做法都要會，這邊應該跟之前加在 model 的 validates_presence_of 用法不同。
  3. 不更改原本的註冊流程也能讓新/舊使用者也能自動擁有 name 欄位的方法，讓使用者註冊成功後才去修改 name 。
