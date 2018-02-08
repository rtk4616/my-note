# 實作 admin 分類功能
前面我們把關聯 seed data 跟 FK 以及 fake data 都設定好了，現在我們來讓後台也顯示這些資料。

## 瀏覽餐廳時可看見分類：index/show
已知 restaurant， 未知：catogory.name：可以透過 FK 去把 category 找出來 `restaurant.category.name`，不能用`restaurant.category_id.name`，我天真的以爲可以但是實際上到 category_id return value 之後就會停了，所以是找不到 name 的。

---
### 爲 view 加入分類欄位 index.html.erb
編輯 view/admin/restaurant/index.html.erb
```html
<!-- 新增表格欄位 -->
<th>Category</th>

<!-- 表格內容 -->
<td><%= restaurant.category.name %></td>
```

### 更新 show.html.erb
<%= restaurant.category.name %>

---
## 新增或修改餐廳時可選擇分類：new/edit

<!-- 真的挺討厭從 view 下手的 -->
### 讓 category_id 欄位可以被存取
編輯 app/controllers/admin/restaurants_controller.rb
```rb
def restaurant_params
  params.require(:restaurant).permit(:name, :opening_hours, :tel, :address, :description, :image, :category_id)
end
```
這樣子才能通過欄位驗證。

---
### 爲 view 新增分類下拉選單
`collection_select`：這個 Rails helper 可以實現下拉選單，從字面上解釋就是 `從物件集合產生下拉選單`，物件集合就是到 `rails c`，下 `Category.all` 回傳的那些就是物件集合。

<!-- 這時候才教搜尋 API...。 -->

collection_select(object, method, collection, value_method, text_method, options = {}, html_options = {})
<!-- 還不錯，還有講解 -->
- object：指定要操作的物件。:restaurant
- method：指定要操作的方法。:category_id          ？
- collection：指定特定物件集合產生 <option> 標籤。Category.all
- value_method：呼叫 Category 的特定方法，來產生 <option value=?> 的值。:id
- text_method：呼叫 Category 的特定方法，來產生 <option value=?> 的文字內容。:name
- options = {}：其他選單設定。option = {prompt: true}
- html_options = {}。其他 HTML 屬性設定。html_options = {class: "form-control"}

<!-- 哭了，竟然有講解我有問題的地方 -->
* 爲什麼 category, id, name 會被稱爲 method 呢？因爲 Ruby 讀取物件屬性的時候都用 method 呼叫的，restaurant.category_id，自然到忘了這件事，請記得這個 method 同時也是指向資料表的欄位，所以傳入的會是 `:restaurant` 而不是 `@restaurant`。

編輯 app/views/admin/restaurant/\_form.html.erb：
```html
<%= form_for [:admin, @restaurant] do |f| %>
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
關鍵在 `f.`collection_select，因爲 form_for 已經有傳入 @restaurant，這邊就可以用這樣的方法。

那我們如果不這麼做，把 `f.` 拿掉，然後傳入 `:restaurant` 會發生什麼事？在 edit 一樣會有選單出來。  
我覺得我還是會用這個做法，因爲我應該會忘記要用 `f.collection_select` 這樣的寫法。

還有一個沒解釋的 {prompt: true} 的部分，這是預設當選單沒有值的時候：new action，會顯示 `Please select`，要換成指定字串就是： prompt: "選我選我"。
