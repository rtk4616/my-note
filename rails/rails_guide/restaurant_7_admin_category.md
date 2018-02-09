# 實作 admin 分類功能
前面我們把關聯 seed data 跟 FK 以及 fake data 都設定好了，現在我們來讓後台也顯示這些資料。

## 瀏覽餐廳時可看見分類：index/show
已知 category， 未知：catogory.name：可以透過 FK 去把 category 找出來 `category.category.name`，不能用`category.category_id.name`，我天真的以爲可以但是實際上到 category_id return value 之後就會停了，所以是找不到 name 的。

---
### 爲 Restaurant view 加入分類欄位 index.html.erb
編輯 view/admin/category/index.html.erb
```html
<!-- 新增表格欄位 -->
<th>Category</th>

<!-- 表格內容 -->
<td><%= category.category.name %></td>
```

### 更新 Restaurant show.html.erb
<%= category.category.name %>

---
## 新增或修改餐廳時可選擇分類：new/edit

<!-- 真的挺討厭從 view 下手的 -->
### 讓 category_id 欄位可以被存取
編輯 app/controllers/admin/categorys_controller.rb
```rb
def category_params
  params.require(:category).permit(:name, :opening_hours, :tel, :address, :description, :image, :category_id)
end
```
這樣子才能通過欄位驗證。

---
### 爲 Restaurant new/edit view 新增分類下拉選單
`collection_select`：這個 Rails helper 可以實現下拉選單，從字面上解釋就是 `從物件集合產生下拉選單`，物件集合就是到 `rails c`，下 `Category.all` 回傳的那些就是物件集合。

<!-- 這時候才教搜尋 API...。 -->

collection_select(object, method, collection, value_method, text_method, options = {}, html_options = {})
<!-- 還不錯，還有講解 -->
- object：指定要操作的物件。:category
- method：指定要操作的方法。:category_id          ？
- collection：指定特定物件集合產生 <option> 標籤。Category.all
- value_method：呼叫 Category 的特定方法，來產生 <option value=?> 的值。:id
- text_method：呼叫 Category 的特定方法，來產生 <option value=?> 的文字內容。:name
- options = {}：其他選單設定。option = {prompt: true}
- html_options = {}。其他 HTML 屬性設定。html_options = {class: "form-control"}

<!-- 哭了，竟然有講解我有問題的地方 -->
* 爲什麼 category, id, name 會被稱爲 method 呢？因爲 Ruby 讀取物件屬性的時候都用 method 呼叫的，category.category_id，自然到忘了這件事，請記得這個 method 同時也是指向資料表的欄位，所以傳入的會是 `:category` 而不是 `@category`。

編輯 app/views/admin/category/\_form.html.erb：
```html
<%= form_for [:admin, @category] do |f| %>
...
<%= f.collection_select(
  :category_id,
  Category.all,
  :id,
  :name,
  {prompt: true},
  {class: "form-control"}
) %>
...
<%= end %>
```

OK，神奇的地方來了，不是七個參數嗎，怎麼只有六個，第一個參數勒？  
關鍵在 `f.`collection_select，因爲 form_for 已經有傳入 @category，這邊就可以用這樣的方法。

那我們如果不這麼做，把 `f.` 拿掉，然後傳入 `:category` 會發生什麼事？在 edit 一樣會有選單出來。  
我覺得我還是會用這個做法，因爲我應該會忘記要用 `f.collection_select` 這樣的寫法。

---
有同學分享，還有一個沒解釋的 {prompt: true} 的部分，這是預設當選單沒有值的時候：在 new action 頁面，會顯示 `Please select`，要換成指定字串就是： prompt: "選我選我"。

---
## 設定分類 Category 的 Route 與 Controller
<!-- 到這邊開始我才發現，前面都是回去修改原本已經有的 `admin Restaurant` 餐廳部分，僅把分類內容加進去，對喔，是 seed data 影想的結果，讓我以爲不用做了，但是事實上那不會是實際結果，因此這時候才要回頭來寫 Category 的 Route 與 CRUD -->
- 設定 Route 與 Controller
- 建立首頁 index 瀏覽功能
- 在首頁建立 new 與 create 功能
- edit 與 update 功能
- delete 功能

### 設定 Category route
```rb
# 後台 routes
namespace :admin, path: "akjdhkajsdh" do
  resources :categorys
  resources :categories
  root "categorys#index"
end
```

category 也是 admin 才有權限更改，因此加在 namespace :admin 之後，category 記得要複數。

---
### 建立 Category controller
`rails g controller admin::categories`  
注意 category 要複數。

#### 替 CategoriesController 加入認證程序
編輯 `app/controllers/admin/categories_controller.rb`  
```rb
class Admin::CategoriesController < ApplicationController
  before_action :authenticate_usre!
  before_action :autheticate_admin
end
```

---
### 建立 index 頁面
Controller
- 新增 index aciton，宣告 @categories
View
- 新增 index.html.erb
- 編寫 <table> 表格 HTML 架構
- 傳入 @categories
- 用 .each 讀取 @categories
<!-- 這次還有直接給小項目，不錯，View 的部分我直接拿 admin/categorys 的來改吧。 -->
View 改法：  

```bash
$ cp categorys/* categories/
$ atom categories/*

# 搜尋 categorys 取代爲 categories
# 搜尋 category 取代爲 category
# 搜尋 Restaurant 取代爲 Category
# 拿掉一些不必要的部分
# kaminari 分頁還要另外設定東西，因此先註解掉，而且用 html 註解 erb 內容，還是會執行？
# 最後用 <%#  %> 才成功
```

有人有提到兩個 index.html.erb 應該也要能共用才對，做法跟 edit 與 new 很像，他是用 `<%= render(:partial => "admin/common/tab") %>`，共用檔案就是 common/\_tab.html.erb。

---
### 建立 admin/category 與 admin/category 切換連結
在各自的 index.html.erb 加入：
```html
<div>
  <%= link_to 'Restaurant', admin_categorys_path %> |
  <%= link_to 'Category', admin_categories_path %>
</div>
```

---
### 新增 Category
因爲分類的內容單純，而且資料表欄位只有一欄，我們這邊只在分類首頁用簡單的表單跟一個送出按鈕表達 new 頁面，因此會有些細節不一樣，因爲我們沒有到 new 的頁面去，而是在首頁做 new 功能。

<!-- 這個錯誤示範用意不錯，但是我覺得也能算是編輯順序不太對，從 controller 下手，我覺得才是比較合理的 -->

**記得我們在首頁新增分類。**

```rb
def create
  @category = Category.new(category_params)
  if @category.save
    flash[:notice] = "Category was successfully created."
    redirect_to admin_categories_path
  else
    flash[:notice] = "Category was failed to create."
    @categories = Category.all
    render :index
  end
end

private
def category_params
  params.require(:category).permit(:name)
end
```
記得傳入表單的資料要經過 `Strong Parameter` 處理。

跟之前不一樣的地方是因爲我們是在首頁新增分類，因此當新增分類失敗的時候，我們要重新 render index 樣板，需要再額外傳入 index 所需的 `@categories`。

不過，爲什麼呢？同學分享的回覆很棒喔：我們要 render 的樣板 `index.html.erb` 是在 `View`，而寫在 categorys_controller.rb 的 index action 是屬於 `Controller`。  

因此 `在 create action 裡，要求重新 render :index` ，可以理解成 `由 Controller 中的 create action 走到 View 時，render index.html.erb 後，傳到使用者的瀏覽器`。  

**這個 render 過程中並沒有經過 index action，因此`拿不到 index action 裡的 @categorys`。**

**只要是在 controller 裡的 action，執行到結尾 end 的時候預設都會觸發 render method，將 action 對應的 view 頁面 render 後，把畫面送給使用者。**

---
### 加入 Category Model 欄位驗證
讓 name 欄位必須要填寫才能存入資料庫，編輯 `app/models/category.rb`
```rb
class Category < ApplicationRecord
  # Let user must input category name
  validates_presence_of :name

  # A category has many categorys.
  has_many :categorys
end
```

---
### 顯示分類欄位驗證的錯誤提示訊息
```html
<% if @category.errors.any? %>
  <%= @category.errors.full_messages.to_sentence %>
<% end %>
```

<!-- 佛心分類跟回顧來著。還有影片跟投影片提示，看看吧。 -->

```rb
# 進 console 試試
rails console
# 不給 name 欄位建立 @category 觸發錯誤
@category = Category.create
# 錯誤訊息會記錄在：
@category.errors
# 而我們需要顯示的錯誤訊息在：
@category.errors.messages
# 將內容從 HASH 轉成 陣列格式
@category.errors.full_messages
# 將陣列內容組合成一個連貫的句子，回傳字串
@category.errors.full_messages.to_sentence
```

---
### 建立分類的編輯與更新功能
跟 new 一樣，我們這邊也不另外做頁面給 edit action 了，點擊 edit 之後直接在分類首頁的新增欄位編輯。

因此有些相應的改變需要編輯：  
- edit 按鈕。
- 按下 edit 按鈕後，表單要有該欄位的 name 內容。
- 送出按鈕要從 create 變成 update。

<!-- 這裡又是先動 View 才用 controller，不過似乎是不得已。 -->
#### 在餐廳分類首頁的編輯與更新 index View
**有沒有注意到我們之前的 new 跟 edit 頁面的提交按鈕 f.submit 會自動切換？**
`form_for 會根據傳入的 @category 來產生相對應的內容`：  

```html
<%= form_for [:admin, @category] do |f| %>
  <%= f.text_field :name %>
  <%= f.submit %>
<%= end %>
```
- new 頁面：`@category = Category.new`
  - 產生空的 Category object 與 `Create Category` 按鈕
- edit 頁面：`@category = Category.find(params[:id])`
  - 顯示欲編輯的 分類內容以及 `Update Category 按鈕`

差別就是 `params[:id]` 的有無來判斷，因此我們的 edit 連結，觸發 edit action 的時候就要傳入 id 參數：  

`<%= link_to "Edit", admin_categories_path(id: category.id) %>`  

- 什麼竟然是餐廳分類首頁的路徑：`admin_categories_path`，對，因爲我們沒有要去 `edit 頁面`，還是在首頁編輯。
- 多傳入了一個參數 `id: category.id`，有了這個 ID 參數，form_for 就會判斷成 edit action。

---
#### 在餐廳分類首頁執行編輯與更新 Controller index method
在 controller 也要進行相應的調整，必須根據有無傳入 `params[:id]` 來判斷實例變數的形態：  
```rb
def index
  @categorires = Category.all

  # For create or update Category
  if params[:id]
    @category = Category.find(params[:id])
  else
    @category = Category.new
  end
end
```

---
### 在分類首頁 執行 update method
跟 create method 很像：
```rb
def update

  @category = Category.find(params[:id])

  if @category.update(category_params)
    flash[:notice] = "Category data was successfully updated."
    redirect_to admin_categories_path
  else
    flash.now[:alert] = "Category was failed to update!"
    @categories = Category.all
    render :index
  end
end
```

Q&A：這邊跟之前不一樣的地方是這裡沒有用 before_action 找 @category 給 需要用的 method ，所以自己要補。 by 耍笨去參考 restauarnts.controller.rb 但是發現 update method 卻沒有寫的笨蛋。

---
### 刪除餐廳分類
<!-- 官方怎麼愛上這種先 view，再補 controller 的順序啊，我好不習慣。 -->

```rb
def destroy
  @category = Category.find(params[:id])
  @category.destroy
  flash[:alert] = "Category was successfully deleted."
  redirect_to admin_categories_path
end
```

```html
<%= link_to 'Delete', admin_category_path(category.id), method: :delete, data: { confirm: "Are you sure?"} %>
```

---
### 重構
有個新關鍵字 `protect_from_forgery`

還有 before_action 後面一定要加 :only ？
