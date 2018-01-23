## Ruby on Rails 重新開始 Part D, Read

來源：[Ruby on Rails 初試啼聲(中)](https://medium.com/@weilihmen/ruby-on-rails-%E5%88%9D%E8%A9%A6%E5%95%BC%E8%81%B2-%E4%B8%AD-d872b8a6d44c)

目前進度：
* [x] index
* [x] new
* [x] create
* [ ] edit
* [ ] update
* [ ] show
* [ ] destroy

到這個階段你可能會覺得我們的首頁似乎有點空？而且每次要自己在網頁上打 0.0.0.0:3000/users/new 去建立新使用者，我們在建立其他 action 的同時也會適情況幫首頁做點調整。

CRUD 的 Create 完成了，接下來輪到 Read 啦，具體來說就是希望顯示單一使用者的詳細資料。

在哪裡顯示？在首頁每筆使用者資料後面做個"檢視"的連結按鈕。

列點目標吧：  
1. 首頁 view 建立 '建立新使用者的' 連結。
2. 建立 controller#show
3. 建立 show 的 view
4. 首頁 view 建立 '檢視' 連結。

---

### Part 1-1, 首頁新增建立使用者連結
編輯 views/index.html.erb

在最後補上

```html
<%= link_to("新增使用者", new_user_path) %>
````

[link_to](http://api.rubyonrails.org/v5.1/classes/ActionView/Helpers/UrlHelper.html#method-i-link_to)：

```rb
link_to(name = nil, options = nil, html_options = nil, &block)
Creates an anchor element of the given name using a URL created by the set of options.
```

透過一個URL建立一個定位元件。

所以第一個參數是超連結要顯示的文字，第二個就是他指向的目標了 `new_user_path`。

還記得我們上一節用的 redirect_to users_path 重新導向首頁嗎？  
一樣下指令 `rails routes` 觀察 action new 的 `Prefix`：  
new_user

因此我們的程式碼就是 `link_to("新增使用者", new_user_path)`，就這樣子。

---
### Part1-2, 建立 controller#show method
編輯 controllers/users_controller.rb

```rb
# 對應到 action #show
def show
  @user = User.find_by(id: params[:id])
  # 與上面個結果相同
  # @user = User.find(params[:id])
end
```

> 這程式碼一打完我就知道爲什麼作者要選純文字資料了，單純多了。

顯示單一使用者的詳細資料，所以首先我們要先把那一位使用者找出來。

從哪裡找？從資料庫 User class，我在這裏也要想一下在哪(回去翻我的資料庫筆記)，id 欄位是預設一定會有的，回想一下或是回頭翻一下我們在 controller 的 private method 寫的資料輸入限制。

```
關於 id ：
Primary Key
主鍵(Primary Key)是能代表該資料表裡 Record 唯一性的 Attibute
每個資料表都一定要有一個 primary key 欄位(Field)

所以 rails 資料庫預設就會有 :id 欄位。

關於資料輸入限制的欄位是我們補上的 :name, :mail, :gender, :age
```

怎麼找？[find官方文件](http://api.rubyonrails.org/v5.1/classes/ActiveRecord/FinderMethods.html#method-i-find)

```rb
find(*args)
Find by id - This can either be a specific id (1), a list of ids (1, 5, 6), or an array of ids ([5, 6, 10]).

find_by(arg, *args)
Finds the first record matching the specified conditions. There is no implied ordering so if order matters, you should specify it yourself.
```

我們從例子來看：  
@user = User.find(params[:id])
根據官方文件所述，find method 預設就是搜尋 id 欄位，所以我們把欄位值 `params[:id]` 丟給他就行，他會回傳 `record`回來。

User.find_by(id: params[:id])  
find_by(條件1, 條件2, 條件3...)，你可以一直給條件讓他去篩出你要的資料。

**注意 find_by 參數細節**
```rb
如果你寫 find_by(id params[:id]) 這樣會有 error，仔細觀察才發現一些很特別的細節，這個參數應該是一個 Hash(待查證)，所以中間不能加逗號(我本來以爲是兩個參數)，現在才發現有點特殊(我對Hash還太不敏感)。

所以讓他們連在一起是OK的：`find_by(id: params[:id])`
```

OK，現在我們找到要顯示的資料了，接下來就是如何顯示這些資料。

---
### Part1-3, 建立 show 的 view
建立並編輯 views/users/show.html.erb

```html
使用者姓名：<%= @user.name %>
使用者信箱：<%= @user.mail %>
使用者性別：<%= @user.gender %>
使用者年齡：<%= @user.age %>

<%= link_to("回到首頁", users_path) %>
```

到目前爲止應該沒什麼問題了吧？ @user 的資訊，我們在 controller show method 已經找好了。

---
### Part1-4, 首頁 view 建立 '檢視' 連結。
編輯 views/users/index.html.erb

我們把檢視連結加在首頁使用者資訊的後面：

```html
<!-- 顯示使用者在資料庫裡的資料 -->
<% @users.each do |user| %>
<tr>
  <td><%= user.name %></td>
  <td><%= user.mail %></td>
  <td><%= user.gender %></td>
  <td><%= user.age %></td>

  <!-- 加上 檢視，編輯，刪除 三個連結 -->
  <td>
    <%= link_to "檢視", user_path(user) %>
  </td>

</tr>
<% end %>
```

這邊也是使用 link_to 建立連結，但是可以觀察到連結目標的部分是 `user_path(user)` ，跟我們之前的都不相同。  
這時候就是下 `rails routes` 的時候啦。

可以觀察到 show 的 Prefix 是 `user`，URI則是 `/users/:id`。

這邊要解釋 `user_path(user)` 我還真的想不太出來，應該是慣例用法，照理說應該是要給 `user.id` 吧？來試試看。  

`user_path(user.id)`

猜對了！這樣檢視連結的 url 給的也是對的，而且沒 error。

驗證方式就是把給的參數給別的：`user_path(user.age)`。  
會產生 缺乏 keys 的 error：

```
ActionController::UrlGenerationError in Users#index
No route matches {:action=>"show", :controller=>"users", :id=>nil}, missing required keys: [:id] .
```

先這樣子吧，要究其原因大概要 google "rails show user_path(user.id) id" 之類的。

請記得下 `rails server` 然後去瀏覽器實際測試你的成果是否有照你的想法跑唷。

CRUD 的 Create 跟 Read 都完成啦！

目前進度：
* [x] index
* [x] new
* [x] create
* [ ] edit
* [ ] update
* [x] show
* [ ] destroy
