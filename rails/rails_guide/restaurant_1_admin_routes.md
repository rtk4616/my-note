這章節重點應該是後臺的建立與設計

會學習到的套件：
- 登入網站：Devise
- 上傳餐廳照片：CarrierWave
- 分頁：Kaminari
- 餐廳資料：FFaker

怎麼 GOOGLE 登入功能：ruby gem user login

<!-- 竟然有講到我有興趣的東西： -->
**在 Rails 裡，各種名稱設定都習慣用符號(symbol)的格式來儲存，而在 Rails 裡大多數的 symbol 與 string 都可以互相轉換，慣例比較常用 symbol 的原因是因爲能省記憶體(印象中)。**

---
# 餐廳論壇 PartA Restaurant Model
## 開始建立與備份 rails 專案
### 從頭開始的話
```rb
rails new <project_name>
bundle install
rails server
# 開瀏覽器測試
```

### 建立分支？
```rb
# 先切到比較乾淨的 branch
git co master
git branch restaurant
git checkout restaurant
# 清理跟整理一些舊的檔案
會發現 rails db:migrate:status 會有些異常狀況，就是我的 master 其實是比較舊的 rails app
照理說不會 migrate 這麼多次才對，因此放棄再次建立 branch 這個想法。

所以還是從頭開始比較好，我就上 github 另外開了一個 orgnization ，這樣我的 repo 就不用一直開新的了？
試試看
```

```rb
rails new restaurant
cd restaurant
bundle install
rails s
# 到瀏覽器連線測試確認版本資訊
Rails version: 5.1.4
Ruby version: 2.4.2 (x86_64-darwin15)
ctrl + c
git add .
git commit
git remote add github <Your_Repo>
git push -u github master
git log --oneline
git tag tag_name <SHA1> -a
git branch res_forum
git checkout res_forum
```

## 建立管理後臺
### 建立 restaurant model 與 restaurant table
rails g model restaurant
### 設計 restaurant table
```rb
class CreateRestaurants < ActiveRecord::Migration[5.1]
  def change
    create_table :restaurants do |t|
      t.string :name
      t.string :tel
      t.string :address
      t.string :opening_hours
      t.text :description

      t.timestamps
    end
  end
end
```

rails db:migrate
---
### 建立網站前台入口
編輯 routes.rb，設定首頁 `root "restaurant#index"`

rails g controller restaurants

編輯 restaurant_controller.rb，建立 暫時空白的 index method

cd app/views/restaurants/
touch index.html.erb
atom index.html.erb
編輯 index.html.erb，<h1>餐廳前臺</h1>

---
### 建立網站後台入口
上一節是我們之前練習過的，這一節要來寫後臺管理網站。

因爲前臺與後臺需求不同，因此 route, controller, view 的設定也都不一樣。

### 後台 part1, admin routes 設計
[龍哥的後台網址設計](https://railsbook.tw/chapters/11-routes.html#namespace)  
後台網址的第一種寫法：  

```rb
Rails.application.routes.draw do
  # For details on the DSL available within this file, see http://guides.rubyonrails.org/routing.html
  resources :products, path: "/admin/products"
end
```
在 resources 後面加上新參數：`path: "/admin/products"`。
> 請注意 `:` 冒號的位置。

我們先觀察一下 這樣子 rails routes 會變成怎麼樣：  
```log
$ rails routes
      Prefix Verb   URI Pattern                        Controller#Action
    products GET    /admin/products(.:format)          products#index
             POST   /admin/products(.:format)          products#create
 new_product GET    /admin/products/new(.:format)      products#new
edit_product GET    /admin/products/:id/edit(.:format) products#edit
     product GET    /admin/products/:id(.:format)      products#show
             PATCH  /admin/products/:id(.:format)      products#update
             PUT    /admin/products/:id(.:format)      products#update
             DELETE /admin/products/:id(.:format)      products#destroy
```

這樣子我們的網址前面就多了一層 `/admin`，不過 對應的 controller 以及 Prefix 都還是一樣的，如果我們希望 controller 也能各自對應不同的網址，我們會用第二種寫法：

```rb
Rails.application.routes.draw do
  namespace :admin do
    resources :products
  end
end
```

看一下這時候的 rails routes：
```log
$ rails routes
            Prefix Verb   URI Pattern                        Controller#Action
    admin_products GET    /admin/products(.:format)          admin/products#index
                   POST   /admin/products(.:format)          admin/products#create
 new_admin_product GET    /admin/products/new(.:format)      admin/products#new
edit_admin_product GET    /admin/products/:id/edit(.:format) admin/products#edit
     admin_product GET    /admin/products/:id(.:format)      admin/products#show
                   PATCH  /admin/products/:id(.:format)      admin/products#update
                   PUT    /admin/products/:id(.:format)      admin/products#update
                   DELETE /admin/products/:id(.:format)      admin/products#destroy
```
這時候的 controller 與 Prefix 都能夠各自對應新的網址。

除此之外，網址的部分還能再加強一點安全性，畢竟是網頁的後台，不能太好猜。

```rb
Rails.application.routes.draw do
  namespace :admin, path: "akjdhkajsdh" do
    resources :products
  end
end
```

這樣子的 rails routes 就是：
```log
$ rails routes
            Prefix Verb   URI Pattern                              Controller#Action
    admin_products GET    /akjdhkajsdh/products(.:format)          admin/products#index
                   POST   /akjdhkajsdh/products(.:format)          admin/products#create
 new_admin_product GET    /akjdhkajsdh/products/new(.:format)      admin/products#new
edit_admin_product GET    /akjdhkajsdh/products/:id/edit(.:format) admin/products#edit
     admin_product GET    /akjdhkajsdh/products/:id(.:format)      admin/products#show
                   PATCH  /akjdhkajsdh/products/:id(.:format)      admin/products#update
                   PUT    /akjdhkajsdh/products/:id(.:format)      admin/products#update
                   DELETE /akjdhkajsdh/products/:id(.:format)      admin/products#destroy
```

因此，最終我們的 restaurant forum 的 routes 爲：
```rb
Rails.application.routes.draw do
  # 前台 routes
  resources :restaurants, only: [:index, :show]
  root "restaurants#index"

  # 後台 routes
  namespace :admin, path: "akjdhkajsdh" do
    resources :restaurants
    root "restaurants#index"
  end
end
```
在各自的 resources 後面增加 root 設定。

前台使用者只有 READ index 與 show 頁面的權限。

```log
$ rails routes
               Prefix Verb   URI Pattern                                 Controller#Action
          restaurants GET    /restaurants(.:format)                      restaurants#index
           restaurant GET    /restaurants/:id(.:format)                  restaurants#show
                 root GET    /                                           restaurants#index
    admin_restaurants GET    /akjdhkajsdh/restaurants(.:format)          admin/restaurants#index
                      POST   /akjdhkajsdh/restaurants(.:format)          admin/restaurants#create
 new_admin_restaurant GET    /akjdhkajsdh/restaurants/new(.:format)      admin/restaurants#new
edit_admin_restaurant GET    /akjdhkajsdh/restaurants/:id/edit(.:format) admin/restaurants#edit
     admin_restaurant GET    /akjdhkajsdh/restaurants/:id(.:format)      admin/restaurants#show
                      PATCH  /akjdhkajsdh/restaurants/:id(.:format)      admin/restaurants#update
                      PUT    /akjdhkajsdh/restaurants/:id(.:format)      admin/restaurants#update
                      DELETE /akjdhkajsdh/restaurants/:id(.:format)      admin/restaurants#destroy
           admin_root GET    /akjdhkajsdh(.:format)                      admin/restaurants#index
```

這樣子我們的前後台 routes 設定就完成了。

---
### 後台 part2, admin controllers 與 view 設計
`rails g controller admin::restaurants`  
admin::restaurants 這個寫法會：
1. 在 restaurants_controller.rb 產生：`Admin::RestaurantsController`  
2. 而且在 controllers 與 views 資料夾底下都會有獨立的 `admin` 資料夾。

Class 的名稱會以常數(Constant)的形態儲存，而 `::` 會將兩個常數分開，Admin::RestaurantsController 的字面解釋就是 "一個來自 `Admin` namespace 的 RestaurantsController"。

```bash
cd app/views/admin/restaurants
touch index.html.erb
編輯 index.html.erb 加入 <h1>網頁後台</h1>
rails s 去測試前台與後台是否正常。
```

下一節我們將介紹 Rails Devise gem 套件。
