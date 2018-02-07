# Part E 替 Restaurant 擴充一些功能透過 幾個 Gems
## 上傳照片功能 - CarrierWave
### 爲了新增照片而增加 Restaurant 的資料庫欄位
`rails g migration add_image_to_restaurants`  

編輯 migration file：
```rb
class AddImageToRestaurants < ActiveRecord::Migration[5.1]
  def change
    add_column :restaurants, :image, :string
  end
end
```

這裡我有在思考要不要特別去找/確認能夠在 change method 裡 rollback 的 method，目前有印象不行的是 `change_column`，想 rollback 請自行修改 migraiton file 成 def up & down 的形式。

> `add_column`：可以 rollback，應該是比較單純的操作才行。

`rails db:migrate`

觀察 schema.rb

---
### 安裝 Gem : CarrierWave
編輯 `Gemfile`  
```rb
# For upload image
gem 'carrierwave'
```

存檔，`bundle install`

command + f 搜尋 carrierwave log 能發現：
Fetching carrierwave 1.2.2  
Installing carrierwave 1.2.2

---
### 使用 CarrierWave
#### 產生 PhotoUploader
使用 CarrierWave 上的工具包，產生一個圖片上傳器(Uploader)，並命名此 Uploader 爲 `photo`：  
`rails g uploader photo`

---
#### 將 PhotoUploader 掛載到 Restaurant Model
由於要上傳餐廳照片，這個 uploader 必須被掛載到 Restaurant Model，請編輯 `app/models/restaurant.rb`:

```rb
# 掛載 carrierwave upload for upload image
mount_uploader :image, PhotoUploader
```

---
### 更新 Rails 專案程式碼
#### 擴充表單允許項目:更新 controller
因爲表單傳入時有經過 `Strong Parameter` 處理程序。  

我們需要將 `image` 加入 permit 允許清單裡。
編輯 `app/controllers/admin/restaurants_controller.rb`
```rb
def restaurant_params
  params.require(:restaurant).permit(:name, :opening_hours, :tel, :address, :description, :image)
end
```

---
#### 擴充前端表單欄位:更新 view
我們需要修改前端的使用者操作界面：`new`跟`edit`，讓使用者可以上傳圖片。

因爲之前有經過重構，因此編輯一個檔案就好  `app/views/admin/restaurants/_form.html.erb` ：  
```html
<%= f.label :image, "Photo" %>
<%= image_tag @restaurant.image, width: '300px' if @restaurant.image? %>
<%= f.file_field :image %>
```

- `f.label` 會產生一個標籤，這個標籤可以協助使用者選取同一個標籤的元件。
- `image_tag @restaurant.image` 會產生一個 <img> 標籤，並根據 @restaurant.image 的內容，帶入需要的屬性。
- `width: '300px'` 在上述 <img> 標籤裡，追加 width="300px" 的屬性與值。
- `if @restaurant.image?` 查看該餐廳物件的 image 屬性是否有內容，有就回傳 True，沒有就回傳 False，`Rails 裡所有的物件屬性都有 "attribute_name?" 的方法`。
- `f.file_field` 會產生一個上傳檔案的按鈕，對應的 HTML 爲 `<input type="file" name="restaurant[image]" id="restaurant_image" />`

別忘了我們只有改 new 跟 edit，show 還沒改。

---
### 上傳圖片測試
#### 記得重啓 rails server 伺服器
沒有重啓的話，會遇到 uninitialized constant Restaurant::PhotoUploader error message。  
重開 rails server 才能重新載入設定。

想看圖片要暫時到 edit 去看，show 還沒改。

---
### 更新 show 頁面顯示上傳圖片
`<%= image_tag @restaurant.image, width: "300px" %>`

---
### 考慮是否將照片儲存庫加入 git 忽略清單
若爲私人照片，爲避免洩漏隱私，請將 `public/uploads` 此目錄加入 。gitignore 裡。
因爲 CarrierWave 預設會將照片放到 `public/uploads` 底下。

---
## 載入測試用資料：FFaker
### [練習] 編寫 Rake：建立自動化執行的任務腳本
練習讓 rails 自行新增 500 筆測試資料，之後再透過 FFaker 產生測試資料。  
任務：自動增加 500 筆 測試數據。

編輯 `lib/tasks/dev.rake` ，副檔名爲 `rake` 表示該檔案爲一則 Rake 腳本：
```rb
namespace :dev do
  task fake: :environment do
    Restaurant.destroy_all

    500.times do |i|
      Restaurant.create!(name: "restaurant#{i.to_s}",
        opening_hours: "11:30",
        tel: "02-1234-5678",
        address: "my_address",
        description: "Here is test description of the restaurant."
      )
    end
    puts "Rake has created fake restaurants."
    puts "Now we have #{Restaurant.count} restaurants data."
  end
end
```

- 定義 rails 指令爲 rails dev:fake
- `namespace :dev` 讓指令出現前綴字詞，像是 `rails db:migrate` 以及 `rails dev:fake`，有助於指令結構化管理。
- `task fake: :enviroment`，也可以寫成 `task :fake => :environment` 定義這個任務腳本的 rails 指令爲 `rails dev:fake`。 `:environment` 爲環境變數，加入 :environment 可以讓 Rake 腳本 與專案 Model 與資料庫互動，不然就只是在 Rake 寫 Ruby 程式(其他 Rails 元件都摸不到該 Rake 腳本)。

- 大量產生 Restaurant 資料
- `Restaurant.destroy_all`：清除資料庫所有的資料。
- 產生500筆資料。
- `Restaurant.create!`：`create` 與 `create!`的差異，用 create 建立完資料之後，還要特地檢查資料數量是否正確，用 `create!` 的話，一但遇到資料庫無法寫入資料庫的情況，就會丟出錯誤，讓程序終止，馬上就可以檢查，不用像 `create` 還要等 500 筆跑完。
- 變化 name: 欄位， #{i.to_s} 由數字轉爲字串。
- 沒有要產生 image 欄位，僅產生純文字檔。

---
### 執行 rails dev:fake 產生假資料
`rails dev:fake`

因爲有 puts 東西，所以在終端機會顯示 puts 內容。

`rails s` 觀察。

---
## 安裝 Gem ：FFaker
`gem 'ffaker'`

這邊有提示說，因爲純粹是測試資料，所以可以放到 Gemfile 的 `group :development` block，不過我沒猜錯的話，這應該是要當 demo 的展示資料，所以還是放在一般區域就好。

`bundle install`

---
### 更新 dev.rake 腳本檔案
編輯 `lib/tasks/dev.rake`，改成透過 FFaker 的方法：
```rb
namespace :dev do
  task fake: :environment do
    Restaurant.destroy_all

    500.times do |i|
      Restaurant.create!(name: FFaker::Name.first_name,
        opening_hours: FFaker::Time.datetime,
        tel: FFaker::PhoneNumber.short_phone_number,
        address: FFaker::Address.street_address,
        description: FFaker::Lorem.paragraph
      )
    end
    puts "Rake and FFaker have created fake restaurants."
    puts "Now we have #{Restaurant.count} restaurants data."
  end
end
```

---
### 執行 rails dev:fake 透過 Rake 與 FFaker 產生測試資料
`rails dev:fake`

> 有需要其他資料就到 [FFaker github](https://github.com/ffaker/ffaker/blob/master/REFERENCE.md)  
> 像是 opening_hours 其實不太裡想。

---
## 加入分頁：Kaminari
資料越多，每一頁載入的速度會越慢，因此透過分頁機制可以控制每個分頁的讀取量。

Kaminari gem 是實作分頁時最多人用的套件，他可以自行設定一頁顯示幾筆資料，並且提供前端的分頁導覽器。

### 安裝 Gem : Kaminari
`gem 'kaminari'`

稍微喵一下 log 可以注意到：

```log
Fetching kaminari-core 1.1.1
Installing kaminari-core 1.1.1
Fetching kaminari-actionview 1.1.1
Installing kaminari-actionview 1.1.1
Fetching kaminari-activerecord 1.1.1
Installing kaminari-activerecord 1.1.1
Fetching kaminari 1.1.1
Installing kaminari 1.1.1
```

---
### 修改 controller 因應 Kaminari
編輯 `restaurants_controller.rb`：
```rb
def index
  @restaurants = Restaurant.page(params[:page]).per(10)
end
```

- page：Kaminari 提供的方法，根據分頁序列來篩選對應資料。
- params[:page]：瀏覽器請求網頁時帶入的參數(頁數)。
當網址中出現 `?page=2` 時，即表示分頁爲第二頁，即 params[:page] = 2。
- per：Kaminari 提供的方法，決定一頁有`幾筆資料`。

---
### 修改 View
編輯 `app/views/admin/restaurants/index.html.erb`
`<%= paginate @restaurants %>`

這行程式碼在的地方就會產生`分頁列表`。

---
### 產生 Kaminari 樣板指令
`rails g kaminari:views` Kaminari 也有像 Devise 的樣板指令，不過我們希望用 Bootstrap 套版的樣式，因此等完成 Bootstrap 後，再一併執行。

> 看到有人提出編輯某筆資料後，如何能夠回到那筆資料在的分頁：`session[:return_to] = request.referer.present? ? request.referer : root_path`，使用 session 儲存上一頁地址，還有考慮若使用者是直接連到該頁面時的情形(沒有上一頁資訊)。

---
### 安裝並設定 Bootstrap gem
#### 安裝 bootstrap-sass 與 sass-rails 並指定版本
編輯 Gemfile：
```rb
# Use Bootstrap for beautiful view
gem 'bootstrap-sass', '~> 3.3.7'
# Use SCSS for stylesheets
gem 'sass-rails', '>= 3.2'
```

本來的 Gemfile 就有 `sass-rails` 的設定了，暫時依照教案的降版本(？)。

`bundle install`

#### 修改 application.css 的副檔名
Rails 會透過 `app/assets/stylesheets/application.css` 來載入專案需要的 CSS 檔案，因爲安裝了 Bootstrap，我們需要將 `CSS` 格式檔案改爲 `SCSS` 。

`mv app/assets/stylesheets/application.css app/assets/stylesheets/application.scss`

#### 編輯 application.scss
```scss
刪除 `*= require_self` 以及 `*= require_tree .`
/* 在最下方加入 */
@import "bootstrap-sprockets";
@import "bootstrap";
```

bootstrap-sprockets 必須要在 bootstrap 之前載入。

#### 安裝 jQuery for bootstrap javascript
`gem 'jquery-rails'`

`bundle install`

> 因爲 bootstrap 有用到 jQuery

#### 載入 Bootstrap JavaScript
編輯 `app/assets/javascripts/application.js`

```js
//= require jquery
//= require bootstrap-sprockets
```

#### Bootstrap 8個套版清單示範
- 導覽列套用 Navbar 樣式，change navbar
- 替 Flash message 套用 Alert 樣式，use Alert components for flash message
- 讓 Flash message 出現右上角取消按鈕，use Dismissible alerts
- 導覽列追加首頁按鈕以及後台按鈕，add root link and admin link to navbar
- 後台餐廳總表改成表格，add table style for admin restaurants/index
- 美化餐廳詳情(show)頁面，styled restaurant show page
- 美化新增餐廳與編輯餐廳表單，styled restaurant new and edit form
- 產生 Kaminari 分頁樣板，add kaminari paging views with bootstrap

---
#### 產生 Kaminari 分頁樣板
`rails g kaminari:views bootstrap3`

這算是超神奇指令了吧，什麼都不用改，還真的這樣就有樣式了...，我本來以爲其他 bootstrap 也該這樣。

---
### deploy app 交作業
[參考我自己的筆記 XD](https://azraeil.gitbooks.io/my-note/content/rails/rails_guide/deploy_rails_app_to_heroku.html)

還有發現 bug，以及新的想法，就是筆記上面的內容一定要是對的，遇到的問題之後補充沒問題，不然回頭爬錯誤的筆記又遇到一次會昏倒。

部署完了，還沒完，還得弄新使用者。

```bash
heroku run rails console
# 找到剛才註冊的使用者
user = User.last
user.role = "admin"
user.save
user.role
```

不知道有沒有別的操作方法？  
先在本機創好 admin 再 push 過去？資料庫不同吶。

而且還需要假資料，因爲跟本機資料庫不是同一個：  
`heroku run rails dev:fake`


### debug 練習
```html
<% form_for  [:admin, @restaurant] do |f| %>
```

`form_for` 記得要用 `<%=  %>` 網頁才會顯示表單。
