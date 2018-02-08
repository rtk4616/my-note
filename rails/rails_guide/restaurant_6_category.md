git br s15  
git co s15  
git 練習

---
# 建立餐廳分類 Category Model
`rails g model Category`

接著在 create_table 底下新增 name 欄位。  
然後繼續在 change method 底下編輯 `add_column :restaurants. :category_id, :integer`，同時在 restaurants table 增加 FK，在多的那邊增加少的 FK。

分類 `一` 對 `多` 個餐廳，一個分類可能含有多個餐廳。

`rails db:migrate`

> create_table 與 add_column 都能夠 rollback。

觀察 `schema.rb`，`Category` 單數能自動轉成複數 `categories table`。

---
## 載入 Category 種子資料
### What is Seed Data ？
種子資料是爲了讓使用者能夠進行物件初始化的資料：比如這家餐廳屬於`什麼`分類，這個 `分類內容` 我們網站管理員需要先提供，使用者在應用程式初始化時才能夠選擇。
<!-- 解釋的有點爛。 -->

檔案是在 `db/seeds.rb` 執行指令是 `rake db:seed` (記得是單數)。

編輯 `db/seeds.rb` ：
```rb
# Create Category seed data
Category.destroy_all

category_list = [
  { name: "台式料理" },
  { name: "中式料理" },
  { name: "日本料理" },
  { name: "墨西哥料理" },
  { name: "美式料理" },
  { name: "越南料理" },
  { name: "複合式料理" }
]

category_list.each do |category|
  Category.create( name: category[:name])
end

puts "Default category seed data created!"

# Default admin account
User.create(email: "admin@test.com", password: "123123", role: "admin")
puts "Default admin account created!"
```

先把舊的分類資料清乾淨，避免重複建立。  

接著建立一個含有多個 hash 的陣列，接著建立我們的預設餐廳分類項目。

然後預先創立後台管理員帳號，這樣才不用手動建立新使用者然後設定 role 欄位。

載入種子資料：`rails db:seed`

---
## 資料表關聯實作
### 練習跨資料表的操作
在建立資料表關聯前，先來練習跨資料表如何操作。

我們的 restaurants table 尚已經建立了 category_id 欄位也就是 FK 外鍵，可以用來查找該 restaurant 是屬於哪個 category。

#### 從餐廳找餐廳分類
先拿到餐廳資料，要找對應的分類：
```rb
# 找特定 Restaurant 資料
@restaurant = Restaurant.find(params[:id])

# 用 Restaurant FK 找 特定 category
@category = Category.find(@restaurant.category_id)
```
---
#### 找同分類下的餐廳
```rb
# 找特定 Category 資料
@category = Category.find(params[:id])

# 用 Category PK 對應到 Restaurant FK，找出所有同分類的餐廳資料
@all_restaurants = Restaurant.where(category_id: @category.id)

### 刪除分類與該分類下的所有餐廳
# 每一個餐廳抓出來刪除
@all_restaurants.each do |restaurant|
  restaurant.destroy
end

# 最後再刪除該筆 分類資料
@category.destroy
```

## 建立資料表關聯
爲什麼要建立資料表關聯呢？因爲建立之後就能夠用新 method：
```rb
# 從餐廳找分類
@restaurant.category

# 找同分類下的餐廳
@category.restaurants

# 刪除某分類下的所有餐廳 (所以建立關聯之後，destroy 除了把自己刪除還會連帶刪除有關聯的資料？)
@category.destroy
```

可以觀察到新 method `簡潔` 很多。

### 設定資料表關聯
先確定兩個資料表間的關係：
- 一個 `分類` 下會有很多家 `餐廳`
  - `Category has many restaurants.`  
- 每一個 `餐廳` 都會屬於某個特定`分類`
  - `A restaurant belongs to specific category.`

設定資料表關聯就是在各自的 Model 編輯：  
`app/models/category.rb`

```rb
class Category < ApplicationRecord
  # A category has many restaurants.
  has_many :restaurants
end
```

`app/models/category.rb`

```rb
class Restaurant < ApplicationRecord
  # A restaurant belongs to specific category.
  belongs_to :category

  # 設定 name 欄位爲必填
  validates_presence_of :name

  # 掛載 carrierwave upload for upload image
  mount_uploader :image, PhotoUploader
end
```

除了很口語化之外，後面接的參數單數複數也很講究：
- has_many 後面接的是複數。
- belongs_to 後面接的是單數。

要留意當 Restaurant Model 底下的 belongs_to :category 執行之後，Rails 會強制要求 Restaurant 的 FK 不能為 nil，因此接下來建立的每一筆 Restaurant instance，在 Model 層都會驗證 `category_id`，如果設計上需要讓外鍵空白，則需要加上 `optional: true` 參數。

---
### Restaurant belongs_to :category 設定後新增的關聯方法
```rb
# 找出與 @restaurant 關聯的 Category instance
@restaurant.category

# 將 @restaurant 的 category_id ，設定爲所屬 Category instance 的主鍵。
@restaurant.category=

# 類似 new method，建立一個和 @restaurant 有關聯的 Category instance，但是沒存入資料庫
@restaurant.build_category()

# 類似 create 方法，練立一個和 @restaurant 有關聯的 Category instance，若通過驗證就存入資料庫。
@restaurant.create_category()

# 同上，但是如果驗證失敗就終止程序。
@restaurant.create_category!()
```

---
### Category has_many :restaurants 設定後新增的關聯方法
```rb
# 找出和 @category 關聯的所有 Restaurant instance
@category.restaurants

# 建立指定的 Restaurant instance 與 @category 關聯
@category.restaurants << @restaurant, ...

# 刪除指定的 Restaurant instance 與 @category 關聯
@category.restaurants.delete(@restaurant, ...)

# 刪除指定的 @restaurant
@category.restaurants.destroy(@restaurant)

# 將 @category 的關鍵物件改爲你指定的？集合，原有的關聯將逐個檢查要移出關聯或是建立新關聯
@category.restaurants=

# 取得 @category 下的所有 Restaurant instance ID
@category.restaurant_ids

# 把和 @category 所有相關的 Restaurant 物件刪除
@category.restaurants.clear

# 查看關聯物件的數量
@category.restaurants.size

# 在 @category 的關聯集合中查找指定ID物件(餐廳？)
@category.restaurants.find()

# 同上，但是可以組合更多搜尋條件
@category.restaurants.where()

# 檢查有無關聯物件存在，若有就回傳 True
@category.restaurants.exist?

# 檢查有無關聯物件存在，若沒有就回傳 False
@category.restaurants.empty?

#
@category.restaurants.build()
@category.restaurants.create
@category.restaurants.create!()
```

看來是預設了非常多常用的方法啊。

---
### 其他的關聯設定選項
1. `dependent: :destroy`：表示在刪除 @category 時，一併刪除所有關聯的 @restaurants。

```rb
class Category < ApplicationRecord
  has_many :restaurants, dependent: :destroy
end
```

2. `belongs_to :category, optional: true`：
要留意當 Restaurant Model 底下的 belongs_to :category 執行之後，Rails 會強制要求 Restaurant 的 FK 不能為 nil，因此接下來建立的每一筆 Restaurant instance，在 Model 層都會驗證 `category_id`，如果設計上需要讓外鍵空白，則需要加上 `optional: true` 參數。

```rb
class Restaurant < ApplicationRecord
  belongs_to :category, optional: true
end
```

---
不錯的問題：  
還有同學提到了：需不需要特別定義 foreign key 的問題(我們只有新增FK欄位，但是並沒有特別指定該欄位爲FK)：慣例優於設定 works。

`@restaurant.build_category()` 與 `@restaurant.create_category()` 的使用情境。有同學分享像是當個人資料切成好幾個頁面要填寫的時候，build_category 就會派上用場，因爲要到最後一頁才需要驗證與儲存。

create_category 則屬於較直觀的用法，一頁資料填完儲存。

---
### 更新測試用 FFaker 資料
因爲資料結構改變了，所以測試資料的結構也須要更新。

編輯 `lib/tasks/dev.rake`:
```rb
# version 2.1 new table structure with Ruby and FFaker
namespace :dev do
  task fake: :environment do
    Restaurant.destroy_all

    500.times do |i|
      Restaurant.create!(name: FFaker::Name.first_name,
        opening_hours: FFaker::Time.datetime,
        tel: FFaker::PhoneNumber.short_phone_number,
        address: FFaker::Address.street_address,
        description: FFaker::Lorem.paragraph,
        # for FK category_id
        category: Category.all.sample
      )
    end

    puts "Rake and FFaker have created fake restaurants."
    puts "Now we have #{Restaurant.count} restaurants data."

  end
end
```

`category:  Category.all.sample`：就是增加 FK 欄位的資料，從所有的分類中(Category.all)，自動取出(.sample) 一個項目(@category.id)回傳，藉以讓 FK 欄位有了模擬的值。

這邊欄位是寫`category:`，不過 Rails 會自動判斷爲 `category_id`，`Category.all.sample` 同理。

也可以改寫成 `category_id: Category.all.sample.id`。

---
### 測試模擬資料與練習應用關聯查找資料
測試 `rails dev:fake`，`rails c` 檢查 `Restaurant.all`。
1. 從餐廳找分類：  
restaurant = Restaurant.all.sample  
restaurant.category
2. 從分類找到同一分類下的餐廳：  
category = Category.all.sample  
category.restaurants
3. 建立一筆餐廳資料，並加入某個分類：  
category = Category.all.sample  
restaurant = Restaurant.new(name: "測試")  
category.restaurants << restaurant
4. 建立一筆新餐廳資料，不指定外鍵時，不允許存入資料庫。  
restaurant = Restaurant.create(name: "測試")  
restaurant.errors.messages  
```log
irb(main):012:0> restaurant = Restaurant.create(name: "測試")
   (0.1ms)  begin transaction
   (0.1ms)  rollback transaction
=> #<Restaurant id: nil, name: "測試", tel: nil, address: nil, opening_hours: nil, description: nil, created_at: nil, updated_at: nil, image: nil, category_id: nil>
irb(main):013:0> restaurant.errors.messages
=> {:category=>["must exist"]}
```

有人分享：

1. `category.restaurants << restaurant`，這樣的寫法就不需要下 `category.save` 了，Rails 會存。
2. 將 PK 欄位歸零以及所有資料庫初始化：  
`rake db:reset`  
根據 schema 重建 tables，並載入 seed data，這應該算初始化資料庫了吧，因此假資料的話之後還是要自己跑 `rake dev:fake`。
3. `不讓 Rails 自動建立PK`，這個應該不常用吧：
```rb
create_table(:table_name, :id => false) do |t|
  t.integer :id, :options => 'PRIMARY KEY'
end
```
3. 客製化自己要的功能(其實與 dev:fake 意思一樣吧)  
清空所有資料並重建要下這些指令(蠻多值得研究的)：
- rake log:clear
- rake tmp:clear
- rake db:drop
- rake db:create
- rake db:migrate
- rake db:seed
編輯 `lib/task/rebuild.rake` ：
```rb
namespace :dev do
  # desc : Describe the next rake task.
  desc "Rebuild system."
  task :build => [ "tmp:clear", "log:clear", "db:drop", "db:create", "db:migrate", "dev:build", "db:seed" ]
end
```
