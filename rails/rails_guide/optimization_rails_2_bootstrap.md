### 在 Rails 套用 bootstrap-SASS 美化頁面

> Sass 是一個將腳本解析成 CSS 的腳本語言，即 SassScript。

#### Part1, Rails 安裝 bootstrap 所需的 Gem
編輯 Gemfile：

```rb
# gem 'sass-rails' '>= 3.2' # 這個應該已經裝了，檢查一下
gem 'bootstrap-sass', '~> 3.3.7' # 使用介於 3.3.0 ~ 3.3.9 範圍內的版本
gem 'jquery-rails'
```

經過檢查，我目前裝的 'sass-rails' 版本是 '~> 5.0'，先試試看吧。

改好存檔之後下 `bundle install`。

---
#### Part2, 讓 Rails 套用 bootstrap
編輯 `app/assets/stylesheets/application.css`

```
/* 刪除  */
*= require_self
*= require_tree

<!-- 分隔線 -->

<!-- 然後在最下面補上: (別忘了句尾的分號) -->
@import 'bootstrap-sprockets';
@import 'bootstrap';
```

你可能會發現這個檔案的結構非常特殊，全部都是註解，那我們改註解有用嗎？

#### 然後編輯完後還要改副檔名！！！  
`將 application.css 改爲 application.scss`

---
#### Part3, 載入 bootstrap 的 Javascript
編輯 `app/assets/javascript/application.js`

在最後加上：
```js
//= require jquery
//= require bootstrap-sprockets
```

看了這個檔案的改法我就默默的去把 application.scss 的那兩個註解刪掉了，看來是真的有用。

---
#### Part4, 重開 rails server
你應該知道 Ctrl + c 可以中斷本來在執行的命令。

再下一次 `rails server`，然後去看看網頁有沒有什麼不同或錯誤。

我個人是這樣啓動 rails server 的：
```bash
# 啓動 rails server
rails server &

# 關閉 rails server
# 用 ps 觀察 pid (process id)
ps
# 用 kill + pid 停止 rails server
kill (number)
```

OK，沒有不同，我猜是忘了改檔名的關係，沒錯，把 `application.scss` 檔名改好就發現首頁格式有變了。

---
#### Part5, 套用 bootstrap 美化 view
bootstrap 已經內建許多美化過的網頁架構，而我們現在要做的則是在 view 檔案找到要美化的位置並把他們加上去。

列個美化清單：
1. index.html.erb
2. new.html.erb 與 edit.html.erb
3. show.html.erb

---
##### Part 5-1, 美化 index table
編輯 app/views/users/index.html.erb  
美化 table 比較簡單，修改一行即可：

```html
<table class="table table-striped table-condensed">
```

我們讓這個 table 的 class 爲 table, table-striped, table-condensed。  
`table-striped`：常見的用深淺色區分每行的斑馬線效果。  
`table-condensed`：減少表格留白的區塊，讓內容緊密一些(效果不明顯？)。

---
##### Part 5-2, 用 form container 與 form-group 美化 new 與 edit 的表單

我們來美化 `編輯` 跟 `建立` 新使用者的頁面：  
編輯 app/views/users/edit.html.erb

```html
<h1>編輯使用者資料</h1>
<div class="form container">
  <%= form_for(@user) do |f| %>
    <div class="form-group">
      <%= f.label :name, "姓名" %>
      <%= f.text_field :name, class: "form-control" %>
    </div>

    <div class="form-group">
      <%= f.label :mail, "信箱" %>
      <%= f.text_field :mail, class: "form-control" %>
    </div>

    <div class="form-group">
      <%= f.label :gender, "性別" %>
      <%= f.text_field :gender, class: "form-control" %>
    </div>

    <div class="form-group">
      <%= f.label :age, "年齡" %>
      <%= f.text_field(:age, class: "form-control") %>
    </div>

    <%= f.submit() %><br />
  <% end %>
</div>
```

簡介一下我加了哪些東西跟怎麼做的：
1. 將 `<div class="form container"> ... </div>` 包住整個 form_for ，接著整理縮排，把 code 選取起來然後按 tab 向後縮排，按 shift + tab 向前縮排。
2. 在 f.label 行前空一行，在 f.text_field 行後空一行，接著按 command + 左鍵在 `f.label 行前空的那一行`，你會發現你的輸入遊標有四個，想解除的話 按 esc 或是 滑鼠點個地方就解除了。
3. 輸入 `<div class="form-group">`，接著按方向鍵移動輸入的位置到 `text_field :欄位` 之後。
4. 輸入 `, class: "form-control"`，再用方向鍵移動遊標到下一行。
5. 輸入 `</div>`，再次整理縮排後，完成。
6. 下 rails server 去開瀏覽器看看有沒有套用格式成功吧。
7. 複製這個頁面到 new 或是 edit 的 view 。

---
##### Part 5-3, 美化 show 頁面
我還沒學其他美化頁面的方法，所以先把 show 頁面改成 `表格`。  
編輯 app/views/users/show.html.erb

```html
<table class="table table-striped table-condensed">
  <thead>
    <tr>
      <td>使用者姓名 </td>
      <td>使用者信箱 </td>
      <td>使用者性別 </td>
      <td>使用者年齡</td>
    </tr>
  </thead>
  <tbody>
    <td><%= @user.name %> </td>
    <td><%= @user.mail %> </td>
    <td><%= @user.gender %> </td>
    <td><%= @user.age %> </td>
  </tbody>
</table>

<%= link_to("回到首頁", users_path) %>
```

恩...，這個美化結果不怎麼理想之後再改善吧。  
這邊我覺得最主要是要練習 command + 左鍵的應用，補標籤的速度非常快。  
美化就到這邊吧，我目前是想走後端的，所以暫時不會想再深入下去。

猜對了，之後接 `restaurant_5_gems.md` 即可。
