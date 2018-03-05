# 打造前台瀏覽功能
<!-- 終於回到前台使用者瀏覽功能啦！ -->
起手式：設定前台 Route。

## 根據使用者需求，用 :only 與 :except 限制一般使用者路由資源
- 使用者可以瀏覽餐廳總表與個別的餐廳資料
- 使用者可以在餐廳總表上，依分類來篩選餐廳資料

複習(原來我之前就打過了)：  
`resources :restaurants, only: [:index, :show]`

---
### 建立前台首頁
<!-- 果然，我想說之前 bootstrap 前端美化直接用 github 連結帶過，這裡怎麼會教。
我就 commit 兩個版本吧，一個無 bootstrap 套版版本，一個有。要到 26 章才有想講前端框架，我已經很想用了現在，只想弄後端的東西。
只是連預設 seed 預設圖片都沒教有點那個，感謝同學分享，好多新坑。
-->

首頁 controller 部分：  
```rb
class RestaurantsController < ApplicationController
  def index
    @restaurants = Restaurant.page(params[:page]).per(9)
  end
end
```

這個 page 就是之前 Kaminari 的分頁功能，`.per(9)` 的意思就是每個分頁有九筆資料。

首頁 view 的部分：  
```html
<% @restaurants.each do |restaurant| %>
  <div>
    <%= image_tag restaurant.image %>
    <h3><%= link_to restaurant.name, restaurant_path(restaurant.id) %></h3>
    <p><%= restaurant.category.name %></p>
    <p><%= truncate(restaurant.description, length: 80) %></p>
  </div>
<% end %>

<%= paginate @restaurants %>
```

首頁就好多坑喔：
1. 建立餐廳假資料 seed 圖庫。
<!-- 這個教材沒有說不過去吧...，需要建立懶人包？ -->
2. 1.的 heroku 版本。
3. 點圖片也能進入該餐廳的瀏覽頁面(show) by block do...end。
4. bootstrap 美化。

show 功能也順便完成了

---
### 前台餐廳分類 route 與 controller
<!-- 覺得這邊解釋太少了 -->
category routes 先設定是爲了 link_to 連結網址設定。

controller 的話，算是順便，之後才要建立個別的分類餐廳瀏覽頁面(category show)。

`resources :categories, only: [:show]`  

`rails g controller categories`

我們先在前台餐廳首頁放上分類連結，因此需要分類資料，補在 `restaurants_controller.rb`：
```rb
def index
  # 同時要找出分類以及餐廳資料
  @restaurants = Restaurant.page(params[:page]).per(9)
  @categories  = Category.all
end
```

---
### 前台餐廳首頁補上餐廳分類連結

```html
<ul>
  <li><%= link_to "全部餐廳", root_path %></li>
  <% @categories.each do |category| %>
    <li><%= link_to category.name, category_path(category.id) %></li>
  <% end %>
</ul>
```

<!-- 喔，這樣改筆記 > 改code > 改筆記 > 改 code 會發瘋，先在 code 上弄到好再貼到筆記。 -->

<!-- 貼 code 懶人包：commit diff 的 view 每個都點開，diff 一個螢幕，view 一個螢幕。 -->

<!-- 不知道爲啥我的分頁連結不會置中。。。：因爲我沒有在 application.scss  `@import "style";`...，style.css 也沒有改名。(被課後作業救，笑了) -->


---
### 個別分類瀏覽餐廳頁面
編輯 `categories_controller.rb`：
```rb
def show
  @categories = Category.all
  @category = Category.find(params[:id])
  # 找出該分類底下的餐廳
  @restaurants = @category.restaurants.page(params[:page]).per(9)
end
```

可以注意到 @restauarnts 的取得方式跟 `restaurants_controller` 裡的非常相似，因此這邊的 view 也有許多省事的方法，教材列表的很不錯。

> 不同的資料，相同的排版樣式。

餐廳與餐廳分類 controller 的部分，可能會重複使用的就有 @restauarnts, @category, @categories。

而餐廳與餐廳分類 view 的部分，重複的部分就更多了，大概只有顯示的餐廳資料不同而已。

因此用局部樣板 `Partial` 來完成根據餐廳分類瀏覽頁面，讓 `restaurants/index` 與 `categories/show` 兩個樣板共用某一部分的程式碼。

原來 view 那個 class="active"，是拿來處理選取到的連結底色的...，看老半天一直提，原來要手動改。

<!-- 太多地方用貼 code 的了...，幸好後面作業出的很不錯。 -->

1. 建立局部樣板目錄：  
`mkdir app/views/shared`
2. 移動 restaurants/index.html.erb 程式碼:  
將可以共用的程式碼移至 `_restaurant_list.html.erb`。  
在移走地方補上 `<%= render partial: "shared/restaurant_list" %>`。
3. 在餐廳分類頁面使用分享的局部樣板：  
<%= render partial: "shared/restaurant_list" %>
