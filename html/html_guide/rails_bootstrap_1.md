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

---
### bootstrap 表格
`<table class="table table-striped table-condensed">`
- table： 讓 bootstrap 知道這是表格，沒有填的話，後面的樣式無法套用。
- table-striped：斑馬紋效果，一深一淺。
- table-condensed：縮減一半表格的留白區域，讓表格間距縮小。
- table-bordered：框線，說實話不好看。
- table-hover：class adds a hover effect (grey background color) on table rows. 滑鼠移動效果

所以主要是顏色跟一些效果，像我目前遇到的不整齊還不知道怎麼解

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

`<%= f.submit() %>` 改法要再研究。

---
### 導覽列
<!-- 。。。原來只是 舉例的頁面太複雜 & 太醜，一坨CODE真心看不下去。
w3schhols 的就好非常多，我暈倒。
bootstrap 官方文件也很簡潔，到底貼那連結做啥。 -->
六角學院中文化太神啦！
