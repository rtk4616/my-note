## Ruby on Rails 從零開始 Part A, 基本元件

首先，你應該對 Ruby 物件導向 以及 資料庫 有點熟吸了，不熟的話請先去碰。

原文來源：[Ruby on Rails 初試啼聲(上)](https://medium.com/@weilihmen/ruby-on-rails-%E5%88%9D%E8%A9%A6%E5%95%BC%E8%81%B2-%E4%B8%8A-c30026fdec42)

之前意外發現的好教材，來試試看不同的口味。

### 建立rails專案與基本測試

```
rails new demosite

rails server
```

可以從 0.0.0.0:3000 或是 localhost:3000, 觀察是否有成功畫面。

### 瞭解 MVC 架構

一開始推薦理解`MVC`，Model，View，Controller，是 rails 裡非常重要的架構。

哦哦，他只著重在 `M` `V` `C`，這三個部分，好主意耶，保持專注，畢竟一開始要知道的東西真的太多了。  
所以，route 跟 database 就先帶過。

單純看順序的話，Controller 先開始分派任務，接著是 Model 進行資料操作，再來是 View 將呈現畫面的結構回傳給 Controlloer，最後 Controller 再把資料套進模板。

所以簡易的流程順序可以記成是 `C > M > C > V > C` 。  
(除了跟controller logic有關外，這樣我就理解爲什麼我會想把code都塞在controller，哈)

### 教學大綱：MCVR
假設，我們想在頁面 `0.0.0.0:3000/users` ，觀看我們建立的所有使用者資料  
Part1, 設定 Model  
Part2, 設定 Controller  
Part3, 設定 View  
Part4, 設定 Route  

從 model 開始，我想有個原因是單純吧，或者是觀察上面的簡易流程 C`MCV`C，取中間這段沒重複的又有全部元素的部分。

而實務上，是從 Route 開始的，因此順序會是 `RCMCVC` ，取開頭`RCMV`這段有全部元素的部分(M後的C拿掉)。  
我的另一個筆記從這個順序來試試好了。

### 第一部分，先運用 Model 產生使用者資料
`rails generate model User` 根據 CoC，這邊的資料名(User)要用大寫開頭單數。

可以再將 generate 簡寫成 g, `rails g model User`

這時候會產生一個 migration file `???_create_users.rb` (create_users 是 rails 取的)

我們要透過它來跟 Model 互動

所以假如說今天我們希望 User(使用者)有下列四個欄位：  
name, mail, gender, age

接著修改 `db/migrate/???_create_users.rb`

```rb
class CreateUsers < ActiveRecord::Migration[5.1]
  def change
    create_table :users do |t|
      t.string :name
      t.string :mail
      t.string :gender
      t.integer :age

      t.timestamps
    end
  end
end
```

完成之後，輸入 `rails db:migrate`，沒有 error 訊息的話，Model 到這個步驟暫時沒事了。

### 第二部分，產生 Controller
先用指令產生 Controller(慣例是複數，剛才 model 是單數大寫)  
`rails g controller users`

會建立 app/controllers/users_controller.rb

如果對剛才的 MVC 還有點印象：`C > M > C > V > C`，應該可以理解 controller 相較另外兩個部分而言，會複雜很多。  
因爲他是樞紐。

一般來說，會學習編寫 Controller 的四大功能 `CRUD`。
* Create
* Read
* Update
* Destroy

(我把這邊闡述章節順序的內容移到上面，因爲我覺得寫得很好，哈)

教學先從 Read 開始，把首頁先做出來。

___

先想像一下，首頁要顯示哪些資料？  
假設我們現在要顯示所有使用者的資訊。
___

首先，編輯 `app/controllers/users_controller.rb`

```rb
class UsersController < ApplicationController
  def index
    @users = User.all
    # User 是我們建立的資料庫，還有印象吧？大寫單數
  end
end
```

index method 對應到 Route 的 users#index action 。
> 講到 action 是不是從 Route 開始比較好。
<!-- 勘誤：此處原文 photos#index，應爲 users#index？
檔案路徑建議開頭不要加 '/' ?-->

可以先記成 Route 會從使用者在瀏覽器輸入 `0.0.0.0:3000/users`  後  
會透過 Controller 去找 app/controllers/users_controller.rb  裡的 index method  
而 index method 裡的 User.all 會透過 Model 去資料庫把所有使用者資料撈出來存到 @users  
以及透過 View 去找 app/views/users/index.html.erb  

@users 實體變數的部分，有跑完OOP章節應該會有感覺才對。  

```rb
@users = User.all
```

首先注意到 我們在編級的這個檔案 `users_controller.rb` 本身也是一個 class 物件。  
爲了在這個 class 創造出來的 instance 間傳遞變數，我們用實例變數 `@users` 。  
而 `User.all` 則是我們呼叫 `User class` 裡的 `self.all class method`，把所有使用者的資料撈出來。

恩，這邊要講的完整就有點複雜了。  
不過必須說，有感覺多了...，跟之前比起來。  
感謝這篇教學筆記，讓我自己跟着把上面那些話打出來。  

### Part3，產生 View
這個 view，我們暫時先手動創建檔案  
app/views/users 這個資料夾到目前應該已經建立好了  
那我們去這個資料夾底下創一個檔案 `index.html.erb`

```erb
<table>
  <thead>
    <tr>
      <td>姓名</td>
      <td>信箱</td>
      <td>性別</td>
      <td>年齡</td>
    </tr>
  </thead>
  <tbody>
    <% @users.each do |user| %>
    <tr>
      <td><%= user.name %></td>
      <td><%= user.mail %></td>
      <td><%= user.gender %></td>
      <td><%= user.age %></td>
    </tr>
    <% end %>
  </tbody>
</table>
```

這裡作者的解釋才讓我注意到一些細節。  

> Erb 不用 \<html>, \<body>, \<head> 等內容  
> Rails 應用了版型 Layout 的概念  
> 在 View 裡只需要關注內容，不用在意版型  
> 版型設定都在 views/layout 裡，預設使用 application.html.erb

> Controller 在顯示畫面時  
> 會把 layout:`application.html.erb` 與 內容： `index.html.erb`  
結合在一起後呈現給使用者  

### Part4，設定 Route
先用手刻法吧，之後會比較有FU

編輯 `config/routes.rb`

```rb
Rails.application.routes.draw do
  # For details on the DSL available within this file, see http://guides.rubyonrails.org/routing.html
  get "/users", to: "users#index"
end
```

這時候再下指令:`rails s`，接著去瀏覽器輸入`0.0.0.0:3000/users`  
正常的話舊能夠看到：  

```
姓名	信箱	性別	年齡
```

因爲還沒有資料，所以只有標題。

這系列教學的1/3就完成了！

感覺不錯啊，沒有把一堆有的沒的尬在一起。
