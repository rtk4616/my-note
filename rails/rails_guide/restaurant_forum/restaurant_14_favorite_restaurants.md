# 收藏我的最愛餐廳功能
<!-- 筆記的量少好多啊 -->

## Favorite Model
對喔，真的需要弄 Join Table 了，看來 EXCEL 之類有表格的東西比較適合我。

`rails g model favorite`

<!-- 這邊我會覺得怪怪的，應該是因爲這次跳太快了 -->

通常是先說：
- 使用者能收藏很多餐廳
- 餐廳能被很多使用者收藏
> 所以餐廳跟使用者是多對多的關係  
接著在使用者跟餐廳中間加入 Join Table：`Favorite`

替 favorite 增加關聯：
- `使用者`有很多`收藏`過的`餐廳`
  - User has_many Favorite
  - Favorite belongs_to User
- `餐廳`可以被很多`使用者` `收藏`
  - Restaurant has_many Favorite
  - Favorite belongs_to Restaurant

> 多對多的關係，將 favorites table，增加 user_id 與 restaurant_id 欄位。

接著到 model 裡去實現關聯：  
這次有幾個不同的地方，在 model user.rb：

```rb
# 使用者收藏很多餐廳的多對多關聯
has_many :favorites, dependent: :destroy

has_many :favorited_restaurants, through: :favorites, source: :restaurant
```

- 第一行讓 User 物件可以得到：`object.favorites` 等關聯方法。
- 爲什麼這裡不是指定 `has_many :restaurants` 呢？因爲已經在前面有一組多對多關聯設定過了：
  - 使用者有很多評論過的餐廳，餐廳有很多評論者。
> 爲了避免重複，因此用不同的 method 名字。

<!-- 所以不該這樣設定？當初應該也要設定成 `commented_restaurants` 會比較好？哦，也有同學有一樣的想法 -->
- `has_many :commented_restaurants, through: comments, source: :restaurant`

不過這時候應該有其他不少地方要改。

而且，當初在餐廳那邊只有單方向的多對多關聯而已。

這次在餐廳也設定的 `has_many :favorited_users, through: :favorites, source: :user`。

不知道有什麼特別用途，在餐廳頁面顯收藏此餐廳的使用者？或是拿來檢查是否爲收藏過此餐廳的使用者來決定：收藏的按鈕顯示文字與動作？

**猜對啦~**

---
## Favorite Route
要先思考之前提的 按下收藏按鈕，以及按下取消收藏按鈕要的動作
- Favorite，餐廳被加入使用者的收藏清單。
  - 新增一筆收藏記錄
- Unfavorite，餐廳被移出使用者的收藏清單。
  - 取消收藏記錄

而又因爲 favorite 與 unfavorite action 不需要樣板，所以用 `POST` 直接送出結果，不是 `GET`。

```rb
resources :restaurants, only: [:index, :show] do
  # else code

  member do
    # 其他程式碼
    post :favorite
    post :unfavorite
  end
end
```

產生的 routes
```log
favorite_restaurant POST   /restaurants/:id/favorite(.:format)                restaurants#favorite
unfavorite_restaurant POST   /restaurants/:id/unfavorite(.:format)              restaurants#unfavorite
```

這樣就能對應到 restaurant controller 的 .favorite 與 .unfavorite action 。

---
### Favorite controller action
```rb
  # 收藏餐廳與取消收藏餐廳
  ## POST /restaurant/:id/favorite
  def favorite
    @restaurant = Restaurant.find(params[:id])

    # Rails 會判斷需要的外鍵資訊
    @restaurant.favorites.create!(user: current_user)
    # 上一行結果與 @restaurant.favorites.create!(user_id: current_user.id) 結果想同

    # 導回上一頁
    redirect_back(fallback_location: root_path)
  end

  ## POST /restaurant/:id/unfavorite
  def unfavorite
    @restaurant = Restaurant.find(params[:id])
    favorites = Favorite.where(restaurant_id: @restaurant.id, user_id: current_user.id)
    # favorites = Favorite.where(restaurant: @restaurant, user: current_user)

    favorites.destroy_all
    redirect_back(fallback_location: root_path)
  end
```

恩。。。有幾個有疑問的地方。

關聯寫法看起來好不直覺，而且好多語法糖。
1. 關聯與不用關聯的寫法
```rb
  # 從餐廳關聯到收藏，已有餐廳資訊，因此給 使用者資訊即可
  @restaurant.favorites.create!(user_id: current_user.id)  
  # 從 Favorite model 不走關聯路線建立資料
= Favorite.create(restaurant_id: @restaurant.id, user_id: current_user.id)  
  # 從使用者關聯到收藏，已有使用者資訊，因此給餐廳資訊即可
= current_user.favorites.create(restaurant_id: @restaurnt.id)
```

2. 不用 `redirect_to` 而是用 `redirect_back` 的理由：  
導回上一頁必須使用 redirect_back ，並搭配 fallback_location 設定當 rails 找不到上一頁在哪裡時，統一導向指定頁面(首頁)。

3. 爲什麼是用 favorites.`destroy_all` 來刪除收藏記錄？  
因爲 where 會回傳物件集合，不一定只有一筆資料，所以刪除物件集合要用 destroy_all。  

啊，可是不合裡啊，一組使用者和餐廳，只會對應到一筆收藏記錄：  
難到這個 where 寫法搜尋出來的會是聯集？  
```rb
favorite = Favortite.where(restaurant_id: @restaurant.id, user_id: current_user.id).first
favorite.destroy
```

還有一個可能性，就是我在頁面跑好前，一直按 Favorite 鈕，這樣就會一直建立收藏記錄，這時候就真的不一定只有一筆了，因爲這個部分還沒設計，因此先用 destroy_all。

**實驗如下**  
更正，就算只有一筆 Favorite 記錄也不能用 .destroy method。  
因爲上面那個搜尋方法回傳的是：  
```log
Favorite Load (0.2ms)  SELECT  "favorites".* FROM "favorites" WHERE "favorites"."restaurant_id" = ? AND "favorites"."user_id" = ? LIMIT ?  [["restaurant_id", 1599], ["user_id", 44], ["LIMIT", 11]]
=> #<ActiveRecord::Relation [#<Favorite id: 7, user_id: 44, restaurant_id: 1599, created_at: "2018-02-27 07:24:22", updated_at: "2018-02-27 07:24:22">]>
```

這種情況下 destroy 會發現：  
```log
irb(main):016:0> fav.destroy  
ArgumentError: wrong number of arguments (given 0, expected 1)
	from (irb):16
```

而不是：  
```log
irb(main):012:0> fav = Favorite.where(restaurant_id:1599, user_id:44).sample
  Favorite Load (0.3ms)  SELECT "favorites".* FROM "favorites" WHERE "favorites"."restaurant_id" = ? AND "favorites"."user_id" = ?  [["restaurant_id", 1599], ["user_id", 44]]
=> #<Favorite id: 7, user_id: 44, restaurant_id: 1599, created_at: "2018-02-27 07:24:22", updated_at: "2018-02-27 07:24:22">
```

---
### Favorite view
一樣顯示在 餐廳 show 頁面裡。
```html
<!-- for favorite -->
<%= link_to "Favorite", favorite_restaurant_path(@restaurant.id), method: :destroy, class: "btn btn-primary" %>

<%= link_to "Unfavorite", unfavorite_restaurant_path(@restaurant.id), method: :post, class: "btn btn-info" %>
```
這裡超白癡的，在驗證時浪費很多時間。

<!-- 因爲 action 裏面沒有什麼東西要執行的，而且很快就返回首頁，所以 log 會有兩段會有兩段會有兩段，喔靠，不夠專心的後果。。。 -->

---
### 如何避免資料重複建立：避免使用者重複(點擊按鈕)建立餐廳收藏記錄
<!-- 教材真的不補 -->
同學分享了幾種做法：
1. 在 view 那邊讓一個按鈕按過之後，被按鈕觸發的動作跑完前，都無法再被重複按。  
`data: { disable_with: "Submitting..." }`  
disable_with: 後面接的東西，會在執行按鈕動作時顯示，使用者這時候還是能點按鈕，只是你去 terminal 觀察，底層不會有重複得資料寫入動作被執行的問題。

2. 設定一個 user has_many favorites 的關聯，檢查有沒有 favorite 過。  
<!-- 這個太不清楚，跳過。 -->

3. 在 controller 用 exists? 檢查某元素是否存在。  
```rb
def favorite
  @restaurant = Restaurant.find(params[:id])
  if @restaurant.favorites.exists?(user_id: current_user.id)

  else
    # Rails 會判斷需要的外鍵資訊
    @restaurant.favorites.create!(user_id: current_user.id)
    # 上一行結果與 @restaurant.favorites.create!(user: current_user) 結果一致

    # 導回上一頁
    redirect_back(fallback_location: root_path)
  end
end
```

這個解法似乎會有其它問題，因爲 create method 還是會被點擊觸發，資源還是會被佔用，是能夠防止資料被重複寫入。

看來還是 方法1. 比較理想。

---
### 在 model 裡寫一個 method 來檢查收藏記錄是否存在
```rb
class Restaurant < ApplicationController
  # else

  # for favorite restaurant check
  def is_favorited?(user)
    # 如果這家餐廳有被該使用者收藏，回傳 True
    self.favorited_users.include?(user)
  end
end
```

#### 利用這個 method 控制 favorite/unfavorite button：  
```html
<!-- for favorite -->
<% if @restaurant.is_favorited?(current_user) %>
  <%= link_to "Unfavorite", unfavorite_restaurant_path(@restaurant.id), method: :post, class: "btn btn-info" %>
<% else %>
  <%= link_to "Favorite", favorite_restaurant_path(@restaurant.id), method: :destroy, class: "btn btn-primary" %>
<% end %>
```

---
### 可能不只一個地方需要這個按鈕，練習把這段收藏按鈕寫成 partial
例如假設我們在首頁的九宮格餐廳頁面也要放這個收藏按鈕。  

但是首頁用的變數名稱是 `restaurant`，不是 show 裡的 `@restaurant`。

如何用同一個 Partial 來滿足兩個變數不同的樣板需求？

**在引用的 render 方法後，使用 locals:{} 選項，爲 Partial 裡的區域變數指定內容。**   
也就是說在本來的 show.html.erb 裡：  
`<%= render partial: "shared/favorite", locals: {restaurant: @restaurant} %>`  

在 index.html.erb 裡：  
`<%= render partial: "shared/favorite", locals: {restaurant: restaurant} %>`

我們把 favorite button 按鈕要共用的 code 移到 `shared/_favorite.html.erb`:  
```html
<!-- for favorite button partial -->
<% if restaurant.is_favorited?(current_user) %>
  <%= link_to "Unfavorite", unfavorite_restaurant_path(restaurant.id), method: :post, class: "btn btn-info" %>
<% else %>
  <%= link_to "Favorite", favorite_restaurant_path(restaurant.id), method: :destroy, class: "btn btn-primary" %>
<% end %>
```

---
### 使用者 Like/ Unlike 餐廳
OK，竟然有一個新章節是重新練習上述的東西一次
.....好吧，是該要練習沒錯。

然後開一個 branch 練習一次多對多的 RMVC。

完成，耗時 46mins，細節很多。

---
### 計算收藏數量最多的前 TOP 10 人氣餐廳
- 在最新動態右邊新增 "TOP 10 人氣餐廳"
  - 網址：`/restaurants/ranking`
- 顯示收藏數最多的十筆資料
  - 假資料需要 更新
- 點擊畫面上的 Favortie/Unfavorite 按鈕時，會重新計算 "收藏數" 。
  - 此收藏數存在 restaurants table 的 `favorites_count` 欄位。
  - 這是灌立命名：計算關聯物件數量的欄位，慣例會命名爲 {table_name}_count。

---
#### Ranking Route
因爲不是針對每筆資料的路徑，所以用 collection。

```rb
# TOP 10 Ranking restaurants
collection do
  get :ranking
end
```

---
#### Ranking column
`rails g migration add_favorites_count_to_restaurant`  

`add_column :restaurants, :favorites_count, :integer, {default: 0}`

記得給個預設值。

----
#### Ranking method in restaurant controller

找出前十個收藏數最多的餐廳

所以我要先去把每個餐廳的收藏數弄好？ restaurant.favorites_count

這個數我好像還沒有去 favorite 或 unfavorite method 設定
對喔，我是不是要讓他初始數爲零，而且還需要 reset 使用者收藏數量統計，不然收藏沒計算到，不收藏 -1 後就變成負數了。

----
#### 每間餐廳的收藏數，先搞定

```rb
def favorite
  # for top 10 ranking
  @restaurant.favorites_count = @restaurant.favorites_count + 1
  @restaurant.save  
end

def unfavorite
  # for top 10 ranking
  @restaurant.favorites_count = @restaurant.favorites_count - 1
  @restaurant.save
end
```

重點是記得要 save。

然後去 show 頁面補個顯示收藏人數：  
`<p class="pull-right">收藏人數：<%= @restaurant.favorites_count %></p>`

---
### 找出收藏數最多的前十間餐廳
會用到 Restaurant .where(收藏數>=1?) 跟 .order 用收藏數升冪排序，接著只取十間。  
http://api.rubyonrails.org/v5.1/classes/ActiveRecord/QueryMethods.html#method-i-order

```rb
# 這樣子可以取出全部 而且用 升冪排列，預設是降冪。
Restaurant.order(favorites_count: :desc).each do |x|
  puts "favorites_count = #{x.favorites_count}"
end

# 好像能用 first(10)，就只取前十個？沒錯
Restaurant.order(favorites_count: :desc).first(3)

# 前十筆餐廳資料就搞定了
def ranking
  @restaurants = Restaurant.order(favorites_count: :desc).first(10)
end
```

也能用 **.limit(10)**

---
### TOP10 人氣餐廳 分頁按鈕
發現似乎要用共用 code 不然好煩，新增一頁就每個頁面都要改分頁按鈕。

而且我有印象有同學提出分享。

叫啥方法來著，render Partial。

不過先土法煉鋼完成，之後再補？

***別太執著大小，先大概喬好位置，值是正確的之後就能去看看參考解答***

弄了兩個多小時，雛形已經大致完成，但是有幾個地方還有 BUG，

而且測試需要
- dev:fake_favorites, 假的使用者收藏記錄
- dev:favorites_reset, 用 Favorite.destroy_all 重置 favorties_table  
  跟把 Restaurant table 的 favorites_column 欄位歸零。

這兩個好像可以寫在一起。

---
### 對照教材答案
最大的不同是計算 restaurant.favorites_count 的方式 XDD。

我是用土法煉鋼的方法：使用者按一下就加一下，跟 favorites table 沒有關聯。

所以我在重置 Favorite 之後，還要另外寫 funciton 去每間餐廳的 favorites_count 歸零。

這樣不太好，在開發測試時就很容易會有不同步的問題。

因此要更新成教材的答案。
_而且計算的方法還是寫在 restaurant model，一開始想說怎麼會找不到 self method。_

---
上述都是手動更新的方式，但是這個 method 已經常用到 Rails 獨立給他一個關聯參數:   `counter_cache: true`  

編輯 `app/models/favorite.rb`  

```rb
# counter_cache 可以自動回傳 size method 的結果(計算關聯物件的數量)
# 並把這個結果更新在關聯 Model 的欄位：{table_name}_count
# 所以在這邊就會是：favorites_count
belongs_to :restaurant, counter_cache: true
```

> 這個語法糖甜到會蛀牙啊。。。

還真的沒執行。找不到 bug 欸 。。。

真是詭異的 bug，只能說有好有壞，可能因爲本來給值的關係，影響到數字，所以還是要清空或 reset 後，再來測試。

---
> 我自己還有個 bug ，某間餐廳的圖片 size 會自動縮小，原因不明。

找到了，應該是我寬度設定有問題，這個 bug 觸發條件是 餐廳的敘述文字比較少的時候會發生。

這個 bug 有時候還無法複製現象出來。
