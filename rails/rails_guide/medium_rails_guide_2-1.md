## Ruby on Rails 重新開始 Part B, Create

來源：[Ruby on Rails 初試啼聲(中)](https://medium.com/@weilihmen/ruby-on-rails-%E5%88%9D%E8%A9%A6%E5%95%BC%E8%81%B2-%E4%B8%AD-d872b8a6d44c)

在初試啼聲(上)我們已經把 首頁 `#index`, Read 的部分完成一半了(顯示使用者資訊)，接下來來把 CRUD 完成。

這邊還是建議照著 MVC 流程跑，View 不建議先編輯，Route 用 resorces 很快，Model 也 OK 了，那我們還是先從 Controller 開始思考跟編輯吧。

因爲你在思考整個流程的同時，其實都是不斷的在 MVC 的流程跑：Route -> Controller -> Model -> Controller -> View -> Controller。

簡單敘述：
1. User 在瀏覽器輸入網址
2. Route 接收網址後轉送給指定的 Controller
3. Controller 分析網址並執行對應的行爲，傳送要求給 Model
4. Model 根據要求操作 Database (資料庫)
5. Database 回傳所需資料給 Model
6. Model 把資料回傳給 Controller
7. Controller 把需要呈現的資料傳給 View
8. View 把呈現輸出畫面資料傳給 Controller
9. Controller 展示最終的結果給使用者

> 你必須要 100% 知道你自己現在到底在做什麼，我覺得這是關鍵，不然都照著 code 打沒有思考只是浪費時間而已。

| CRUD | Description | Controller#Action|
| :------------- | :------------- |:-|
|Create|建立使用者|new - 建立新使用者的資訊(姓名，信箱，信別，年齡)|
|Create|上傳新使用者資訊|create - 上傳新使用者   |
|Read   |使用者可以一次觀看所有使用者資料   |index - [首頁] 顯示所有使用者資訊(在主畫面)   |
|Read   |使用者可以觀看其中一個使用者的詳細資訊   |show - 瀏覽一個指定的使用者詳細資料   |
|Update   |使用者可以編輯一個使用者資訊   |edit - 編輯一個使用者資訊   |
|Update   |使用者編輯完一個使用者資訊後可以更新   |update - 上傳更新後的使用者資訊到資料庫   |
|Delete   |使用者可以刪除不要的使用者資訊   |destroy - 刪除不想要的照片   |

簡單的說：四個主要功能，會被切成另外七個小功能 (#action)，我們目前的任務要把這七個 action 一個一個完成。

* [x] index
* [ ] new
* [ ] create
* [ ] edit
* [ ] update
* [ ] show
* [ ] destroy

我們大抵照著資料的創建順序 CRUD 開始做吧。

### Part1，第一步 `Create` = `#new` + `#create`，新建新使用者資料

從前面的表格可以發現創造使用者分成兩個部分： new 與 create。  
`new` 創造出一個變數跟新增使用者頁面讓使用者輸入資料。  
`create` 將新增使用者頁面的資料送到資料庫儲存。  

到目前爲止，我們能列出幾個任務：
1. controller 寫 new method。
2. view 建立一個 新增使用者的頁面。
3. controller 寫 create method。

#### Part1-1. controller 寫 new method
> Route -> `Controller` -> Model -> Controller -> View -> Controller。

編輯 `app/controllers/users_controller.rb` 增加一小段 code。

```rb
def new
  @user = User.new
end
```

很特別吧？其實就跟我們創造 instance 的行爲一樣，只是創造這個 `@user` instance 之後 Model 會把這筆資料存到資料庫去。

#### Part1-2. view 建立一個 新增使用者的頁面
> Route -> Controller -> Model -> Controller -> `View` -> Controller。

編輯 `app/views/users/new.html.erb`，要自己建立檔案。

這個小節資訊比較多，請慢慢消化，建議消化完畢再繼續走下去，不然照著 code 打，其實學不到什麼。

```erb
<h1>新增使用者！！！</h1>

<%= form_for(@user) do |f| %>
  <%= f.label :name, "姓名" %>
  <%= f.text_field :name %> <br />

  <%= f.label :mail, "信箱" %>
  <%= f.text_field :mail %> <br />

  <%= f.label :gender, "性別" %>
  <%= f.text_field :gender %> <br />

  <%= f.label :age, "年齡" %>
  <%= f.text_field :age %> <br />

  <%= f.submit %> <br />
<% end %>
```

測試：在你的 Rails 專案資料夾下 `rails server`，接著到瀏覽器輸入 0.0.0.0:3000/users/new，看看成果。

幾個待搞懂的關鍵字 `form_for(@user)`, `f.label`, `f.text_field`, `f.submit`。  

還有那些 label 後面接的東西： `:name`, `:mail`, `:gender`, `:age` 各自是代表什麼？應該是代表 `:user` 資料表內的欄位(field)。

這些都跟最終的 html 有關，我們先去瀏覽器打開開發者模式，找網頁原始碼：

```rb
<form class="new_user" id="new_user" action="/users" accept-charset="UTF-8" method="post"><input name="utf8" type="hidden" value="✓"><input type="hidden" name="authenticity_token" value="loR7d3l620AjJHsaWRTZVke4YcuhXSTBFJ3SxU2df9sABqnUvQESAgbZexEAdROv9yjoZJqv0hOKDGblXW5Tcw==">
  <label for="user_name">姓名</label>
  <input type="text" name="user[name]" id="user_name"> <br>

  <label for="user_mail">信箱</label>
  <input type="text" name="user[mail]" id="user_mail"> <br>

  <label for="user_gender">性別</label>
  <input type="text" name="user[gender]" id="user_gender"> <br>

  <label for="user_age">年齡</label>
  <input type="text" name="user[age]" id="user_age"> <br>

  <input type="submit" name="commit" value="Create User" data-disable-with="Create User"> <br>
</form>
```

1.`form_for(@user)`：這個是 Rails 的 Helper(輔助方法)，我們利用 form_for 來建立表單來讓使用者創造或是更新 @user 的內容，[官方文件](http://api.rubyonrails.org/v5.1/classes/ActionView/Helpers/FormHelper.html#method-i-form_for) 內容非常豐富。

```rb
form_for(record, options = {}, &block)

Creates a form  that allows the user to create or update the attributes of a specific model object.
```

`<%= form_for(@user) do |f| %>`

form_for 會綁定 model 物件，也就是 @user ，form_for 會自動幫你帶入 @user 的物件內容。

---

2.`f`：

```
The variable `f` yielded to the block is a FormBuilder object that incoporates the knowledge about the model object represented by "@user" passed to "form_for".

Methods defined on the FormBuilder are used to generate fields bound to this model.

Thus, for example,
<%= f.text_field :name %>
will get expanded to
<%= text_field @user, :name %>
the html format would be
<input type="text" name="user[name]" id="user_name">
```

---

3.`f.label`：[官方文件](http://api.rubyonrails.org/v5.1/classes/ActionView/Helpers/FormBuilder.html#method-i-label)!  
```erb
label(method, text = nil, options = {}, &block)

<%= f.label :name, "姓名" %>
會被轉換成
<label for="user_name">姓名</label>
```

html `label for="user_name"` 的功用是協助使用者在點選到該標籤時，可以透過for的屬性值，去連動具有相同id屬性值的標籤，[來源](http://seanphpbook.blogspot.tw/2012/01/html-label.html)。

從這個例子來看的話，就是使用者要輸入"姓名"這個欄位的資料時，他不一定要點`輸入方框`，使用者點前面 label `姓名` 的部分也能協助使用者選到同樣 `id(user_name)` 的物件，而在這個例子就是輸入方框了。

---

4.`f.text_field`：[官方文件](http://api.rubyonrails.org/v5.1/classes/ActionView/Helpers/FormHelper.html#method-i-text_field)

```rb
text_field(object_name, method, options = {})

Returns an input tag of the “text” type tailored for accessing a specified attribute (identified by method) on an object assigned to the template (identified by object). Additional options on the input tag can be passed as a hash with options. These options will be tagged onto the HTML as an HTML element attribute as in the example shown.
```
就是輸入文字的方框。

另一個常用的是能容納多行內容的 [text_area](http://api.rubyonrails.org/v5.1/classes/ActionView/Helpers/FormHelper.html#method-i-text_area)，兩個都可以指定大小。

`<%= f.text_area(:age) %> <br />`

> 值得注意的是，text_field 第一個參數 `:name` 是要有在 Model 裡的？也就是你讓使用者輸入的資料要存到哪去。

---

5.`f.submit`：

```rb
submit(value = nil, options = {})
Add the submit button for the given form.
When no value is given, it checks if the object is a new resource or not to create the proper lable.
```

作者補充說明：
> 因爲在 controller 中 new method 內容是 `@user = User.new` ，
> 所以 submit method 會去找 HTTP POST 方法，不用另外指定送出的目的地，
> 也不用設定 submit 按鈕上的顯示文字，預設就會是 `Create User`，
> 這些就是遵循 Rails 慣例所帶來的方便。

而你如果想要指定顯示內容的話就在第一個參數(value)補個字串就行：  
`<%= f.submit("test") %>`

呼，到這邊你還沒放棄的話，請繼續加油，我覺得還是要弄懂這些程式碼`大概`做了哪些事情，不然未知+未知+...，一直疊加下去的模糊知識，最後是堆不出什麼立體的東西來的，希望到這邊你已經有做了些類似這篇的筆記了。

下一個小節是要把另一個 controller `create` method 完成，目前我們完成了：首頁，新增使用者頁面，接下來我們要把使用者在`新增使用者`頁面所輸入的資料存到資料庫。
