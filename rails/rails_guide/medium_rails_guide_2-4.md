## Ruby on Rails 重新開始 Part D

來源：[Ruby on Rails 初試啼聲(中)](https://medium.com/@weilihmen/ruby-on-rails-%E5%88%9D%E8%A9%A6%E5%95%BC%E8%81%B2-%E4%B8%AD-d872b8a6d44c)

目前進度：
* [x] index
* [x] new
* [x] create
* [ ] edit
* [ ] update
* [x] show
* [ ] destroy

CRUD，接下來輪到 Update 啦！就是讓使用者可以修改之前建立的資料並且將修改後的結果存到資料庫。

1. 編輯 edit controller method
2. 編輯 edit 的 show 頁面
3. 編輯 update controller method ~~(作者漏了這個部分XD)~~
4. 編輯 首頁的 view ，增加`編輯`按鈕

---
### Part1-1, 編輯 edit controller method
編輯 controllers/users_controller.rb

```rb
# 對應到 action #edit
def edit
  @user = User.find_by(id:params[:id])
end
```

你可能會由有疑問怎麼跟 show method 一樣？沒錯，我們畢竟也是要先找出我們要修的'那筆'資料出來，然後再把它顯示給使用者編輯，所以格式會跟 new method 很像(本範例是 99% 一樣)。

---
### Part1-2, 編輯 edit 的 show 頁面
建立 views/users/edit.html.erb

```html
<h1>編輯使用者資料</h1>
<%= form_for(@user) do |f| %>
  <%= f.label :name, "姓名" %>
  <%= f.text_field :name %> <br/>

  <%= f.label :mail, "信箱" %>
  <%= f.text_field :mail %> <br/>

  <%= f.label :gender, "性別" %>
  <%= f.text_field :gender %> <br/>

  <%= f.label :age, "年齡" %>
  <%= f.text_field(:age) %> <br />

  <%= f.submit() %><br />
<% end %>
```

我是去複製 new.html.erb 接著把標題改掉而已。

> 作者簡單解釋這邊的 Rails 慣例，我覺得有些地方怪怪的，簡單地說就是 Rails 在生成 edit 頁面的同時，把使用者的資料也一起放上去了。

> 而 submit 去找 edit action 對應到的 Verb 是 PUT/PATCH 所以按鈕上的文字就是 "Update User"。

---
### Part1-3, 編輯 update controller method
編輯 controllers/users_controller.rb

```rb
# 對應到 action #update
def update
  @user = User.find_by(id:params[:id])
  @user.update_attributes(user_params)

  # 更新完成後重新將網頁導向到 該 user 的 show 頁面，所以這邊給的連結是 user_path(@user.id)
  redirect_to user_path(@user.id)
end
```

第一行用使用者資料 id 去找該使用者的資料並存成 instance variable，應該沒問題吧？

第二行用 `update_attributes` method 更新 資料庫欄位值，將使用者修改過的資料儲存起來。

第三行在 rails 更新完成後重新將網頁導向到 該 user 的 show 頁面，所以這邊給的連結是 user_path(@user.id)，參數給該使用者的 id。

---
### Part1-4, 編輯 首頁的 view ，增加`編輯`按鈕
編輯 views/users/index.html.erb

```html
<%= link_to "檢視", user_path(user.id) %>
<%= link_to "編輯", edit_user_path(user.id) %>
```

連結部分的話，一樣老樣子觀察 `rails routes`，Prefix 是 `edit_user`，所以路徑是 edit_user_path(user.id)，參數則一樣是 該使用者的 id，而網址則會是 `/users/id/edit`。

去試試你的成果吧！！！

---

回顧一下，到目前爲止，我們完成了：

* [x] index
* [x] new
* [x] create
* [x] edit
* [x] update
* [x] show
* [ ] destroy

~~1. 編輯 edit controller method~~
~~2. 編輯 edit 的 show 頁面~~
~~3. 編輯 update controller method~~
~~4. 編輯 首頁的 view ，增加 `編輯`按鈕~~

CRUD 就剩下最後一個 `Destroy` 刪除使用者的功能啦！！！一樣列一下目標。  
1. 建立 controller destroy method
2. 編輯 首頁的 view ，增加 `刪除` 按鈕
3. 編輯 使用者的 edit 頁面，也增加一個 `刪除` 按鈕
---

### Part2-1, 建立 controller destroy method
編輯 controllers/users_controller.rb

```rb
# 對應到 action #destroy
def destroy
  @user = User.find_by(id:params[:id])
  @user.destroy

  # 刪除使用者資料後，將網頁重新導向至首頁
  redirect_to users_path
end
```

一樣先找到要刪除的 user資料，接著用 destroy method 刪除他，然後將頁面重新導向至首頁。

這裡爲什麼要用 users_url ？ 我測試用 users_path 也 OK 捏，而且導向的目標(首頁)也還是自己的 domain name。

---
### Part2-2, 編輯 首頁的 view ，增加 `刪除` 按鈕
編輯 views/users/index.html.erb

```html
<%= link_to "編輯", edit_user_path(user.id) %>
<%= link_to "刪除", user_path(user.id), method: "delete", data: {confirm: "Delete User #{user.name}, Are you sure?"} %>
```

destroy 這邊用的是直接連接呼叫 controller method 的方法，前面 link_to 連結設定應該沒問題吧？

`method: "delete"`：這段 code 會直接去 controller 呼叫我們上一節寫的 delete method。

而後面的 `data: {confirm: "Delete User #{user.name}, Are you sure?"}`  
則是在執行刪除 method 前，再問使用者一次確定要不要刪除，拿掉這段 code 的話就會直接刪掉該筆使用者資訊了。

### 結語
## 恭喜你！！！

* [x] index
* [x] new
* [x] create
* [x] edit
* [x] update
* [x] show
* [ ] destroy

你完成了 Rails 最基本的 CRUD 結構，希望這些筆記對你有些許幫助，不過老話一句，要讓自己懂的教學攻略還是自己來寫吧！！！

不瞞你說，我從接觸 ruby 跟 rails 接近四個月才寫得出這四篇我的個人學習筆記。

如果有合適的導師帶領跟合理的進度管理，我覺得兩個月就該要能寫出來了，最大的關鍵就是要一步一步弄懂那些模糊不清的部分，並且作筆記記錄下來！

作筆記記錄下來！
作筆記記錄下來！
作筆記記錄下來！

這太重要了，學習途中太多突發狀況，你很難完全記得你昨天做過了哪些事情，更別說上個月了。
