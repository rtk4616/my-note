## Ruby on Rails 重新開始 Part C, Create

來源：[Ruby on Rails 初試啼聲(中)](https://medium.com/@weilihmen/ruby-on-rails-%E5%88%9D%E8%A9%A6%E5%95%BC%E8%81%B2-%E4%B8%AD-d872b8a6d44c)

目前進度：
* [x] index
* [x] new
* [ ] create
* [ ] edit
* [ ] update
* [ ] show
* [ ] destroy

~~1. controller 寫 new method~~  
~~2. view 建立一個 新增使用者的頁面~~  
3.controller 寫 create method

#### Part1-3, controller 寫 create method
Route -> `Controller` -> Model -> Controller -> View -> Controller。

編輯 `app/controllers/users_controller.rb`

```rb
# 對應到 action #create
def create
  @user = User.new(user_params)
  @user.save
end

# 用 private user_params method 限制輸入參數
private
def user_params
  params.require(:user).permit(:name, :mail, :gender, :age)
end
```

這是最簡易簡易的版本，幾個關鍵字：`user_params`, `save`, `params`, `private`, `.require`。  
有幾個問題：params 哪來的？ 是預設用來儲存的參數變數嗎？一定要這樣寫嗎？  
直接用 params 變數會遇到什麼問題嗎？  
> 會遇到 `ActiveModel::ForbiddenAttributesError in UsersController#create`

教學原文解釋：
```
因爲 Rails 會避免沒有限制的資料(參數)進入 Model 資料庫，如果沒有限制，有心人士可以很輕易地改寫資料庫上甚至是伺服器系統參數。
所以 Rails 預設就會擋沒被處理過的資料，因而產生 `ActiveModel::ForbiddenAttributesError in UsersController#create`。
```

`user_params`：private method 用來隱藏限制規則。  
private 權限的話，請去龍哥的教材看看。  

`save`：預設存進資料庫的 method，拿掉就不會儲存。  

`params`：[官方文件](http://api.rubyonrails.org/v5.1/classes/ActionDispatch/Http/Parameters.html#method-i-parameters)。  

```rb
params()
Returns both GET and POST parameters in a single hash.
```

`params` 這個應該是很常用 `Instance Public methods`，是 parameters 的簡稱。

`.require().permit()`：  
require(:user) 可以幫我們確認使用者上傳的參數名為 user，防止誤傳。  
.permit(:name, :mail, :gender, :age) 用來過濾參數的欄位只有這幾個其他的我們都不要。

```rb
params.require(:user).permit(:name, :mail, :gender, :age)
```

測試：在你的 Rails 專案資料夾下 `rails server`，接著到瀏覽器輸入 0.0.0.0:3000/users/new，試試看建立新使用者資料。

應該有發現按鈕(create method)點下去能成功(要手動到首頁 0.0.0.0:3000/users 去看有沒有顯示)，但是 create user 之後，畫面沒變還是在建立使用者頁面。  
這個要我們自己做。

##### 建立使用者後將頁面導向首頁
編輯 app/controllers/users_controller.rb

```rb
# 對應到 action #create
def create
  @user = User.new(user_params)
  if @user.save
    redirect_to users_path, notice: "Succceed!"
  else
    render :new
  end
end

# 限制輸入參數
private
def user_params
  params.require(:user).permit(:name, :mail, :gender, :age)
end
```

關鍵字：`redirect_to`, `users_path`, `notice:`, `render`。

`redirect_to`：瀏覽器重新導向指定頁面。

```rb
redirect_to(options = {}, response_status = {})

Redirects the browser to the target specified in options.
```

redirect_to(users_path)：將頁面重新導向至 users_path，將頁面導向首頁。

---

`users_path`：這個東西要拆成兩個看，一個是 `users`，另一個是 `_path`。

請你下指令 rails routes，觀察首頁的 Prefix：`users`。

Prefix + \_path = 相對路徑。
Prefix + \_url = 絕對路徑。

使用時機我們先不討論，先看個例子。

```
users_url # => http://localhost:3000/users
users_path  # => /users
```

users_url 會有完整網頁位址跟協定。  
users_path 則只有相對目前網頁 domain( http://localhost:3000 ) 後的路徑而已。

[使用時機與解釋討論串](https://stackoverflow.com/questions/2350539/what-is-the-difference-between-url-and-path-while-using-the-routes-in-rails)

```
簡單節錄：
The usage of _url is required in controller(server-side) and _path are engouh in the view(browser-end).
```

**注意 user's' 要複數，不然會有 error(ActionController::UrlGenerationError in UsersController#create)。**

---

`notice:`：Convenience accessor for flash[:notice].

恩...，搜尋了一下，發現 `notice: "Succceed!"` 這行，似乎沒有效果，因此用關鍵字 `rails redirect_to notice not work`搜尋，簡單地說，原本的 code `redirect_to(users_path, notice: "建立使用者資料成功~~~~~~!")`，應該是希望成功建立新使用者後，要顯示後面那串訊息，但是那串訊息發現 在導向過去的頁面(users_path) view 那邊還要加上 `<%= notice %>` 才會有效果，是導向過去指定頁面之後才會顯示。

有興趣可以編輯 `views/index.html.erb`：把 <%= notice %> 加在最後一行，測試方式就是建立新使用者成功後能發現首頁最後一行有多顯示你在 notice 後面補的訊息，重新整理或是重新連線到首頁的話就會消失。

---

[render](http://api.rubyonrails.org/v5.1/classes/ActionView/Helpers/RenderingHelper.html#method-i-render)：

```
render(options = {}, locals = {}, &block) Link
Returns the result of a render that's dictated by the options hash
```

中文可以翻成"渲染"，功用就是保留使用者輸入過的表單資料，但是因爲輸入資料不符合所需，因此希望使用者接著他剛才輸入過的資料繼續編輯，而不是請使用者整個頁面的資料都重寫。

所以 `render :new` 的意思就是：請記得我們這個階段還在，controller#create，我們剛才讓使用者輸入的 @user 資訊還在，因此 render 保留這些資訊並把這些資訊套到指定頁面 :new (users/new.html.erb) ，最後顯示給使用者，讓使用者再次編輯。

不過，目前這個階段我們沒對使用者輸入的資料進行更嚴格的限制，所以我們之後再討論如何觸發 render 。

<!--
所以這段是不是先不加就好？暫時沒辦法實際測試？

原文作者的第四步，重要欄位要填也只有給參考例子...。

後面的寫好記得補個連結。
-->

---

恭喜我們 把 Create 完成啦！！！

目前進度：
* [x] index
* [x] new
* [x] create
* [ ] edit
* [ ] update
* [ ] show
* [ ] destroy

~~1. controller 寫 new method~~  
~~2. view 建立一個 新增使用者的頁面~~  
~~3.controller 寫 create method~~

CRUD 的 Create 完成了，接下來輪到 Read 啦。
