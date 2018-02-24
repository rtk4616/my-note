Bootstrap是目前最流行的前端設計框架，讓開發者也可以很輕鬆的進行網頁排版，也很有多現成的Theme可以套用。要在Rails使用Bootstrap，請安裝bootstrap-sass
> 哦，原來有 theme。

如果搭配分頁套件kaminari的話，執行rails generate kaminari:views bootstrap3就會產生對應的kaminari樣板。

<!-- 沒猜錯的話應該有不少懶人用法才對。 -->

### bootstrap 表單
form bootstrap 套用法：  
- 在包住整個欄位的 div 加入 `class="form-group"`
- 在輸入欄位的部分加入 `class: "form-control"` ，它可以幫助你統一並調整輸入欄位的大小，讓你的網頁在跨瀏覽器與跨裝置時，都能達到一致的視覺效果。
```erb
<div class="form-group">
  <%= f.label :name, "待辦事項名稱" %>
  <%= f.text_field :name, class: "form-control" %>
</div>
```

不能懶人的把整個表單加入 form-group，會全部擠在一起，沒辦法清楚顯示

---
### bootstrap 表格
`<table class="table table-striped table-condensed">`
- table： 讓 bootstrap 知道這是表格，沒有填的話，後面的樣式無法套用。
- table-striped：斑馬紋效果，一深一淺。
- table-condensed：縮減一半表格的留白區域，讓表格間距縮小。
- table-bordered：框線，說實話不好看。
- table-hover：class adds a hover effect (grey background color) on table rows. 滑鼠移動效果

所以主要是顏色跟一些效果，像我目前遇到的不整齊還不知道怎麼解
表格外面加入：  

`<div class="container">  </div>` 似乎非常常用，原來是 bootstrap 專用。。。
- container 是非滿版，container-fluid 是滿版。
- 而 container 或是 container-fluid 左右都有 15px 的 padding，要取消間距，就加 `<div class="row"> </div>`

---
### bootstrap 按鈕
`<%= link_to "Cancel", tasks_path, class: "btn" %>`
這樣就有按鈕的感覺了，要顏色的話應該要再疊其他的 class。

沒錯：
.btn-default
.btn-primary
.btn-success
.btn-info
.btn-warning
.btn-danger
.btn-link

#### Button Sizes
The classes that define the different sizes are:

.btn-lg
.btn-md
.btn-sm
.btn-xs

#### Block Level Buttons
.btn-block

#### Active/Disable Buttons
.active  
.disabled  

`<%= f.submit() %>` 改法跟 link_to 一樣，只是不用先加逗號：  
婀，不對，是參數要加在`()`括號裡： `<%= f.submit(class: "btn btn-primary") %>` 。
`<%= f.submit class: "btn btn-primary" %>`

---
### 導覽列
<!-- 六角學院中文化太神啦！但是是 4.0 的。 = = -->

恩，重新練習吧，回到過去 + 創 branch 練習。

```bash
git co <指定SHA1>
git br bs_practice
```
搞定，開始真實練習 bootstrap

#### 第一個介紹的是 .navbar-brand 品牌
4.0 的版本似乎比較簡潔

我應該要先 commit 一次範例然後再開始改，這樣才比較好理解解改了哪些。

```html
<!-- 使用者工具列(導覽列)： -->
<nav class="navbar navbar-default">
  <div class="container-fluid">
    <!-- Brand and toggle get grouped for better mobile display -->
    <div class="navbar-header">
      <button type="button" class="navbar-toggle collapsed" data-toggle="collapse" data-target="#bs-example-navbar-collapse-1" aria-expanded="false">
        <span class="sr-only">Toggle navigation</span>
        <span class="icon-bar"></span>
        <span class="icon-bar"></span>
        <span class="icon-bar"></span>
      </button>
      <!-- <a class="navbar-brand" href="#">Brand</a> -->
      <%= link_to "餐廳評論網", root_path, class: "navbar-brand" %>
    </div>

    <!-- Collect the nav links, forms, and other content for toggling -->
    <div class="collapse navbar-collapse" id="bs-example-navbar-collapse-1">

      <!-- pull-right 跟 navbar-right 似乎都能把元素往右推，順序就是從右邊數過來 -->
      <ul class="nav navbar-nav navbar-right">
        <!-- <li class="active"><a href="#">Link 1<span class="sr-only">(current)</span></a></li>
        <li><a href="#">Link2</a></li> -->

        <!-- 所以建立按鈕/連結地方是就是透過沒有數字的清單項目 <li> 放連結 </li> -->
        <% if current_user %>
          <!-- 文字內容需要要另外處理，不然似乎套不到導覽列的格式，用 <li></li> 也可以 -->
          <p class="navbar-text">Hi, <%= current_user.email %></p>

          <% if current_user.admin? %>
            <li><%= link_to 'Admin後台', admin_restaurants_path %></li>
          <% end %>

          <li>
            <%= link_to('修改密碼', edit_user_registration_path) %>
          </li>
          <li>
            <%= link_to('登出', destroy_user_session_path, method: :delete) %>
          </li>

        <% else %>
          <li>
            <%= link_to('註冊', new_user_registration_path) %>
          </li>
          <li>
            <%= link_to('登入', new_user_session_path) %>
          </li>
        <% end %>


        <!-- 下拉選單 -->
        <!-- <li class="dropdown">
          <a href="#" class="dropdown-toggle" data-toggle="dropdown" role="button" aria-haspopup="true" aria-expanded="false">Dropdown1 <span class="caret"></span></a>
          <ul class="dropdown-menu">
            <li><a href="#">Action</a></li>
            <li><a href="#">Another action</a></li>
            <li><a href="#">Something else here</a></li>
            <li role="separator" class="divider"></li>
            <li><a href="#">Separated link</a></li>
            <li role="separator" class="divider"></li>
            <li><a href="#">One more separated link</a></li>
          </ul>
        </li> -->
      </ul>
      <!-- 搜尋表單跟按鈕 -->
      <!-- <form class="navbar-form navbar-left">
        <div class="form-group">
          <input type="text" class="form-control" placeholder="Search">
        </div>
        <button type="submit" class="btn btn-default">Submit</button>
      </form> -->

      <!-- 噢噢 這邊是示範靠右的連結 -->
      <!-- <ul class="nav navbar-nav navbar-right">
        <li><a href="#">Link3</a></li>
        <li class="dropdown">
          <a href="#" class="dropdown-toggle" data-toggle="dropdown" role="button" aria-haspopup="true" aria-expanded="false">Dropdown2 <span class="caret"></span></a>
          <ul class="dropdown-menu">
            <li><a href="#">Action</a></li>
            <li><a href="#">Another action</a></li>
            <li><a href="#">Something else here</a></li>
            <li role="separator" class="divider"></li>
            <li><a href="#">Separated link</a></li>
          </ul>
        </li>
      </ul> -->

      <!-- 這邊學習了，果然需要在後面這邊註明 -->
    </div><!-- /.navbar-collapse -->
  </div><!-- /.container-fluid -->
</nav>
```

---
### flash message/alerts
基本用法：
```html
<div class="alert alert-success" role="alert">
  <%= notice %>
</div>
```
直接套用官網的範例發現，他的 block 預設就會直接顯示，不管有沒有訊息。

因此我們要先判斷有沒有 flash 訊息再顯示：

```html
<% if flash[:notice] %>
  <div class="alert alert-success" role="alert">
    <%= notice %>
  </div>
<% end %>
```

附加可關閉的按鈕 `x`：  
- 補上 .alert-dismissible
- 加上關閉訊息按鈕
```html
<% if flash[:notice] %>
  <div class="alert alert-warning alert-dismissible" role="alert">
    <button type="button" class="close" data-dismiss="alert" aria-label="Close"><span aria-hidden="true">&times;</span></button>
    <%= notice %>
  </div>
<% end %>
```

---
### well
bootstrap 內容框美化

---
### image
.img-responsive：Makes an image responsive (will scale nicely to the parent element)，讓圖片能跟著裝置螢幕大小等比例變化。
