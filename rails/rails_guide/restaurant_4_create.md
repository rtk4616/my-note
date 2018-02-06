### 補齊需要的 controller method：CRUD
> 看到同學推薦的 atom erb snippets，趕緊試用：
`command + shift + .` 循環切換。
只能說讚，相見恨晚系列 +1。

### CREATE 一筆餐廳資料
#### 完成 index.html.erb
erb-snippets 快捷鍵練習

#### 完成 new.html.erb
關於 `<%= form_for [:admin, @restaurant] do |f| %>`
這邊的用法跟之前的`form_for(@user) do |f|`不一樣是因爲我們在 routes 那邊有用 `namespace :admin do ... end`。
* 本來的寫法 `form_for @restaurant do |f|`，`form_for` 會依據 `@restaurant` 自動判斷可能指向：
1.新增：new_restaurant_path
2.修改：edit_restaurant_path(@restauran.id)

* 而 `[:admin, @restaurant]` 這樣的寫法 `form_for` 會自動判斷，可能指向
1.新增：new\_`admin_`restaurant\_path
2.修改：edit\_`admin_`restaurant_path(@restaurant)
這樣的寫法才能符合 `namespace :admin` routes 的設定。

### **Strong Parameters**
`params.require(:restaurant).permit(:name, :opening_hours, :tel, :address, :description)`：
在讀取表單時，基於安全考量，必須對傳入的參數做特別處理，這是 Rails 強制要求的。
- 必須將寫入參數的程序，從 new action(method) 裡獨立出來，宣告成另一個私有方法：這裡依慣例命名爲 restaurant_params。
- 使用 params 取出從 Client 端送進 Request packets 裡，controller 所需的參數。
- 使用 require(:object_name) 拿出表單資料。
- 使用 permit 允許指定的屬性資料傳入 Model。

### 表單驗證必填欄位
#### 在 Model 加入驗證程序
將 name 欄位設定爲使用這必須填寫，也就是說如果使用者沒填寫該欄位就按下送出鈕，我們就不會將這筆資料送入資料庫。
與資料庫的互動由 Model 來負責，因此編輯
`app/models/restaurant.rb`：
```rb
class Restaurant < ApplicationRecord
  # 設定欄位爲必填
  validates_presence_of :name
end
```
> 跟資料有關，因此去 Model 設定。

### 在表單上加入錯誤提示
當使用者點下送出將 @restaurant 送進 Model 時，Model 發現 @restaurant.name 沒有內容，Model 會因此把物件退回 Controller，同時將錯誤訊息的提示字串存進 @restaurant.errors 裡。

#### controller 觸發錯誤提示
回顧一下 controller create method 的內容：
```rb
def create
  @restaurant = Restaurant.new(restaurant_params)
  if @restaurant.save
    flash[:notice] = "Restaurant was successfully created."
    redirect_to admin_restaurants_path
  else
    # 觸發在表單上加入錯誤提示
    flash.now[:alert] = "Restaurant was failed to create!"
    render :new
  end
end
```

當儲存表單資料失敗時，會有錯誤提示訊息 `flash.now[:alert]`，而 render :new 的話則是將頁面資料保留並還給使用者再次輸入。

`flash`：flash 通常寫在 redirect 之前，但實際上都有作用。
因爲 flash 要跳頁才有作用，所以 flash ，一般不會搭配 render，因爲 render 是沒有跳頁的，而是把使用者填寫的資料保存下來並讓 view 再顯示一次，所以爲了要讓 flash 有作用，我們在這邊使用 `flash.now` 的寫法。

#### 顯示錯誤提示給使用者
編輯 `app/views/admin/restaurants/new.html.erb`
```html
<!-- 欄位錯誤訊息提示 -->
<% if @restaurant.errors.any? %>
  <h2>Some errors occur here!</h2>
  <ul>
    <% @restaurant.errors.full_messages.each do |msg| %>
      <li><%= msg %></li>
    <% end %>
  </ul>
<% end %>
```

- 若 @restaurant 內有任何錯誤訊息：`@restaurant.errors.any?`，就顯示一段<h2>內容告訴使用者有錯誤發生，`any?`是語義化寫法，和之前用到的 present? 相似，只要 `@restaurant.errors` 不是 nil，不爲空就回傳 `True`。
- `full_messages`：這個 method 會自動幫我們把 Model 送進來的 `@restaurant.errors` 訊息變成完整的句子(有這麼神？真的)。
- 因爲錯誤訊息可能不只一個，因此用 each do...end 寫法取出。
- 最後撰寫 HTML 來格式化，這裡主要使用 <ul> 與 <li> 來整理。

OK，create 暫時到這邊告一段落。

---
### 測試 new 與 create method
`rails s`

測試創建新餐廳以及觀察 name 欄位留空的錯誤訊息。
```log
Restaurant was failed to create!

Oops! We need fix something here：
Name can't be blank
```
真的會自動顯示與欄位相關的訊息！

---
### Read 一筆餐廳資料
根據 routes.rb 裡的設定，我們可以透過 http://0.0.0.0:3000/admin/restaurants/:id。  
連進 `admin/restaurants#show`：  
- 網址中有 `:id`，可以用 `params` method 取出。
- 有了 `:id` ，可以用 Restaurant.find，透過 `Restaurant Model`的 find method ，去 restaurants table 裡，把特定一筆資料撈出來。
- 將撈出的該比資料存入 @restaurant。
- 在 show action 將結束時，呼叫對應的 View 樣板 show.html.erb。
- 在樣板裡，利用 ERB 標籤取出 @restaurant 的內容，放置到 HTML 裡。
- 因此，Controller 作爲 Model 與 View 的中間人，讓前端樣板可以動態顯示資料的變化。

### 編寫 Admin::RestaurantsController 裡的 show action
```rb
class Admin::RestaurantsController < ApplicationController
  #

  def show
    @restaurant = Restaurant.find(params[:id])
  end

  #
end
```

---
### EDIT 一筆餐廳資料
根據 routes.rb 裡的設定，我們可以透過 `http://0.0.0.0:3000/admin/restaurants/:id/edit` 連進 `admin/restaurants#edit`，執行 edit action：  
- 網址中有 `:id`，我們可以用 params 取出。
- 有了 `:id`，我們可以用 Restaurant.find，透過 Restaurant Model，呼叫 find 方法，去 restaurant talbe 裡，把該 `:id` 的特定資料取出。
- 將撈出的資料存入 @restaurant。
- 當 edit 將結束時，呼叫對應的 View 樣板 `edit.html.erb`。
- 在樣板裡，利用 ERB標籤 取出 @restaurant 的內容，放置到 HTML裡。

我們目前設計的 show 與 edit method，都要尋找 restaurant.id，因此我們可以另外宣告一個 `find_restaurant`。  
並在 controller 開頭加入 `before_action` ，呼叫 `find_restaurant`，並限定只能給 `show` 與 `edit`。

---
### 用 Partial 重構 New 與 Edit 表單
因爲 edit 與 new 的表單是一樣的，我們也會直接把 new.html.erb 的內容移到 partial，然後分別在 new 和 edit 來引用。  
- 在 app/views/admin/restaurants 下，新增名爲 `_form.html.erb` 的檔案。
- 將 new.html.erb 程式碼複製到 `_form.html.erb`。
- new.html.erb 只留下 `<%= render partial: "form" %>`。
- edit.html.erb 同樣加上 `<%= render partial: "form" %>`。

> `form_for` 能夠依據我們傳入的 `@restaurant` 是否帶有資料，如果有資料就是對應到 update action ，如果沒有資料就是 create action，而提交(submit)的按鈕文字也會自動產生。

### 編輯 Update action
當使用者在 edit 表單按下送出按鈕時，表單會根據 `@restaurant` 判斷要將資料送往 `http://localhost:3000/admin/restaurant/:id`，且使用 PATCH 動作，依據 routes.rb 設定，會觸發 admin/restaurants#update：
- 網址中有 `:id`，我們可以用 params 取出。
- 有了 `:id`，可以用 `Restaurant.find` ，透過 Restaurant Model 的 find 方法，到 restaurants table 裡，把該 `:id` 的資料撈出來。
- 將該比資料存入 @restaurant。
- 對 @restaurant 呼叫 update 方法，並根據表單傳來的新內容，更新資料表裡的資料。

### 測試 edit 與 update 以及 new
`rails s`
- 測試 new，用 render 方法後是否運作正常，name 刻意留空。
- 測試 edit，name 刻意留空後，update 會顯示：
```html
Restaurant was failed to update!

Oops! We need fix something here：
Name can't be blank
```

---
### Destroy 一筆餐廳資料
依據 routes.rb 設定，我們透過 `http://0.0.0.0:3000/admin/restaurants/:id` 會觸發 admin/restaurants#destroy：
- 網址有 `:id`，我們可以用 params 方法取出。
- 因爲有 `:id`，我們可以用 Restaurant.find 透過 Restaurant Model，呼叫 find method 到 restaurants table 裡，把該 `:id` 的資料取出。
- 將取出的資料存入 `@restaurant`。
- 呼叫 destroy method 將該筆資料刪除。

#### 編輯 destroy method
```rb
  def destroy
    flash[:alert] = "#{restaurant.name} was deleted!"
    @restaurant.destroy
    redirect_to admin_restaurants_path
  end
```

這裡我有小調動順序，將 flash 移至第一行(本來在最後)，因爲我想顯示該 @restaurant.name 。

#### 在首頁觸發 destroy
編輯 index.html.erb
```html
<%= link_to "Delete", admin_restaurant_path(restaurant.id), method: :delete, data: {confirm: "Are you sure to Delete #{restaurant.name} ?"} %>
```

**由於 destroy 按鈕是單純看 URL Helper，destroy 的網址與 show 一模一樣，爲了區分，需要指定 method 爲 `delete`**

所以如果寫成 `method: :get` 就會變 show method(已實測)。

不寫呢？也會是 show 。

> 請注意這邊的 method 應爲 rails routes 的 `Verb` 部分，而不是 controller 的 method。

這讓我想要確認當初我實現 ToDoList 的完成按鈕是怎麼做的 XD。

哦，那時候有改 routes，有弄出新的 Prefix，所以條件有點不一樣：
```html
$ rails routes
   Prefix Verb   URI Pattern               Controller#Action
          GET    /                         tasks#index
done_task POST   /tasks/:id/done(.:format) tasks#done
```

當初在 ToDoList 首頁 view 的部分：  
`<%= link_to "完成", done_task_path(task.id), method: "done" %>`  
method 那邊是 "done"，這不是 controller method 嗎？我是不是該打 `:post`，結論是都可以，難道說可以不用打？好像是耶因爲 `/tasks/:id/done` 在我的 routes 裡是唯一的，要打的話應該要打 `:post` ？對，沒錯，should be `symbol of HTTP verb`。

[link_to 文件連結](http://api.rubyonrails.org/v5.1/classes/ActionView/Helpers/UrlHelper.html#method-i-link_to)

> link_to(name = nil, options = nil, html_options = nil, &block)
Creates an anchor element of the given name using a URL created by the set of options.

> method: symbol of HTTP verb -   
This modifier will dynamically create an HTML form and immediately submit the form for processing using the HTTP verb specified.  
Useful for having links perform a POST operation in dangerous actions like deleting a record (which search bots can follow while spidering your site).  
Supported verbs are :post, :delete, :patch, and :put.  
Note that if the user has JavaScript disabled, the request will fall back to using GET.  
If href: '#' is used and the user has JavaScript disabled clicking the link will have no effect.  
If you are relying on the POST behavior, you should check for it in your controller's action by using the request object's methods for post?, delete?, patch?, or put?.

下一篇，我們來嘗試用幾個 gems，擴充我們網站的功能。
