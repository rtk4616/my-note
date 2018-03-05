增加
- 所有餐廳最新動態消息 `Feeds`：
  - 顯示餐廳最新評論
  - 顯示最近新增的餐廳
- 在 show 頁面顯示每一家餐廳的優惠活動 `Dashboard` (儀表板) 按鈕
  - 其實就是顯示這家餐廳的詳細評論數據：星等，評價，評論數量...blablabla 相關數據分析。

# 設定路由
## 最新動態(Feeds)的路由
Verb  Controller Action   URI                 預設用途
GET   restaurants#feeds   restaurants/feeds   顯示所有餐廳最新動態
> 我們需要新的路由，這個動態消息牽扯到所有的餐廳，因此用 `collection` 集合路由。

--
### 顯示每一家餐廳的優惠活動(Dashboard)
Verb  Controller Action       URI                         預設用途
GET   restaurants#dashboard   restaurant/:id/dashboard    顯示特定餐廳的優惠活動(儀表板)
> 使用成員路由 `member`。

---
### 路由設定
```rb
resources :restaurants, only: [:index, :show] do
  resources :comments, only: [:create, :destroy]

  # 瀏覽所有餐廳的最新動態
  collection do
    get :feeds
  end

  # 瀏覽個別餐廳的 Dashboard
  member do
    get :dashboard
  end
end
```

---
### 餐廳最新動態 controller
我們要顯示最新的十筆評論跟最新的十間餐廳。

這邊有機會出現資料庫 model 間關聯上關於刪除的瑕疵：  
`資料庫有些餐廳被刪掉了，但是評論還在。`

因此，應要在關聯設定時一併加入：資料表相依的設定：  
```rb
class Restaurant < ApplicationRecord
  has_many :comments, dependent: :destroy
end
```

這邊難得解釋得挺清楚的：
搭累 has_many 時，`:dependent` 有五種可用的參數：
- :destroy
- :delete_all
- :nullify
- :restrict_with_exception
- :restrict_with_error

相關的 destroy method 就要簡單加個 if 條件判斷。
- (user) has_many :comments, dependent: :restrict_with_error
  - 但是我的 user 目前不給刪除啊，啊！ devise 那邊印象有。
- (category) has_many :restaurants, dependent: :restrict_with_error
  - 到 admin category 設定相關顯示訊息。  

---
### 餐廳最新動態 view
bootstrap:  
- 選單標籤 Nav Tabs
- 內容排版 Panels
- Grid system ，分成兩邊

QQ，我有進步，看著 bootstrap 範例就可以完成。

---
### 每間餐廳的 dashboard
<!-- 只做這種程度是不是別放啦？或是改用其他例子，按鈕也沒做？XD -->

---
### 作業
【Ruby on Rails 指南】裡，對兩個選項說明如下（LINK）：

:restrict_with_exception：有關聯物件的話，向擁有者拋出異常。
:restrict_with_error：有關聯物件的話，向擁有者拋出錯誤。
英文原文為（LINK）：

:restrict_with_exception causes an exception to be raised if there are any associated records
:restrict_with_error causes an error to be added to the owner if there are any associated objects
請你根據上述實驗的結果，描述什麼是「拋出異常」，而什麼是「拋出錯誤」？請盡可能使用實驗的操作結果來表達，避免使用抽象化的概念術語。

拋出異常︰
直接進入錯誤畫面，告訴你這樣操作不行，並把相關的錯誤都呈現在畫面上。如同Q2的圖片。

拋出錯誤︰
產生錯誤訊息，因此需要在 app\views\admin\categories 內，預先寫好 @category.errors ，才能在發現問題時，以 message 的形式告訴使用者
使用者仍在原來終端的畫面上，不會進入錯誤畫面。
-------------------------
個人心得︰
error 比較人性，因為使用者只會產生︰「奇怪，怎麼不能刪除？」，總比進入紅色的畫面來的好太多了，看到會覺得，該不會哪裡爆炸了吧(大誤)。
但是如果從管理者的角度來看，exception 會比較方便，不用多一道指令碼，相較之下省時間。

---
