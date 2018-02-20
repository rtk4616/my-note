增加 User Model 後的待辦(更新)事項：
1. 待解決舊的 User 資料 name 欄位爲 nil 的問題：3.3 的另一個手動做法。
  1. seed 與 fake data 是否因應使用者欄位增加而需要更新？
    1. seed 應該不用？admin 帳號的部分可能要。
    2. fake data 需要更新。
2. 調整 user name 欄位需要出現的地方:
  1. 更新餐廳 show 頁面評論的使用者名稱。
  2. 導覽列的部分
3. 客製化 Devise 註冊功能，讓使用者在註冊時就能新增 name 欄位。
  1. 註冊時的欄位屬性是否需爲必填？
  2. 必填與不必填的做法都要會，這邊應該跟之前加在 model 的 validates_presence_of 用法不同。(錯 XD)
  3. 不更改原本的註冊流程也能讓新/舊使用者也能自動擁有 name 欄位的方法，讓使用者註冊成功後才去修改 name 。

---
1. 待解決舊的 User 資料 name 欄位爲 nil 的問題：3.3 的另一個手動做法。  
在 rails console 執行：
```rb
@user = User.all
@user.each do |user|
  if user.name == nil || user.name == ""
    user.name = user.email.split("@").first
    user.save
  end
end
```

記得 `user.save`。  
記得 `user.save`。  
記得 `user.save`。  

這樣子就能手動讓舊會員的 name 欄位都有值了。

---
1-1-1. 更新 seed 種子資料
1-1-2. 更新 fake data

---
2. 調整 user name 欄位需要出現的地方:
  1. 更新餐廳 show 頁面評論的使用者名稱。
  <!-- 我的餐廳評論頁面寫在哪裏啊 XD 不是餐廳的 show 嗎？沒錯啦，看到 admin 的去了。 -->
  2. 導覽列的 hi, XXX 部分。  
  更新 views/layouts/application.html.erb

3. 客製化 Devise 註冊功能，讓使用者在註冊時就能新增 name 欄位。
[Devise github](https://github.com/plataformatec/devise)

真的是佛心來著，而且還有佛心使用前教學：  
If you are building your first Rails application, we recommend you `do not` use Devise.  
> 新手請不要用這個 Gem XD，先去學其他的東西再來碰 Devise。

而且連這些 `其他的東西` 他也都幫你列好了，還三個...。

最重要的是他沒有列官網的教學 XDDD，看來 Michael Hartl's online book 真的不錯。

> 列入坑裏。

值得深思啊，教材到目前爲止真的讓我覺得許多地方還是太過於"速成"了。

devise team 他們的想法跟胡立大大的比較像，先理解原理再去用懶人包，而不是先用懶人包其他的之後再說。

Devise is composed of 10 modules.  

Devise 由十個 module 組成的...。
> 我想這就是不推薦新手碰的原因吧，太複雜了。

WIKI how to 裏面，一堆好資訊啊!!

介紹詳細多了...

OK，客製化的方法有懶人法跟非懶人法，教材是用懶人法。

非懶人法，主要分兩個部分，view 跟 controller 。

懶人法要加額外的參數，就到 ApplicationController 設定一個 simple before filter。

before filter = before_action + protected method

編輯 `controllers/application_controller.rb`
```rb
# add devise before filter to add parameters to User
before_action :configure_permitted_parameters, if: :devise_controller?

# Devise 客製化屬性
def configure_permitted_parameters
  devise_parameter_sanitizer.permit(:sign_up, keys: [:name])
  devise_parameter_sanitizer.permit(:account_update, keys: [:name])
end
```

`:sign_up, keys: [:name]`：意思是讓使用者除了能用原本的 email 登入之外，也能用 `name` 登入？(這樣名字屬性是不是也要唯一？)。

恩...，devise 範例是只有 :sign_up，沒有 `:account_update`，而教材是兩個都寫，從官方列表來看，似乎是用途跟權限都不太一樣。

這個部分還是我在瞎猜而已，OK 乖乖去找資料就翻到中文化的大大啦 XD，還是架在 github 的部落格，他說是翻譯筆記，我覺得超棒。

https://andyyou.github.io/2015/04/04/devise/

目前使用者會碰到 devise 註冊資料只有在註冊的時候而已吧？
> No, devise 已經連修改密碼的頁面也就是(編輯使用者)都弄好了。

Strong Parameters 核可參數
- 核可參數提供了一個介面來防止使用者亂送參數，阻止送 Action Controller 的參數被用在 Model 中，除非參數被加到白名單，此外參數也可以被註記為 required 必須。

- 當您客製化了您的 view，最終您可能要加入新的屬性(attributes)到您的 form。Rails 4 把參數的防護措施從 Model 移到了 Controller，造成 devise 也把處理機制換到了 controller

- 在 devise 只有三個 action 允許您設定參數是否允許通過傳到 model，這是因為必要的防護機制。它們的名稱和 permitted parameters 允許參數預設值如下

* sign_in(Devise::SessionsController#create) - 只允許驗證 key 通過(如 email)
* sign_up(Devise::RegistrationsController#create) - key 和 password, password_confirmation 三個
* account_update(Devise::RegistrationsController#update) - key, password, password_confirmation, current_password

噢噢，password_confirmation 是註冊時要使用者輸入第二次密碼確認的參數。

而 current_password 就是要讓使用者確認資料修改而再次要求輸入密碼的參數。

<!-- 找到了啦，受不了，命名太爛讓我出神，WTF，沒去檢查我的錯。 -->
如同字面上解釋的 sign_up 就是註冊時放行的資料，如果沒有寫明`devise_parameter_sanitizer.permit(:sign_up, keys: [:name])`：那註冊時填的名字就不會存到資料庫。

:account_update 同理，都確認完畢。

`autofocus: true` 這個參數能在載入畫面時，直接讓遊標顯示在指定的輸入欄位。

autocomplete 不清楚。

---
### 3-1 & 3-2 讓 name 欄位爲必填
編輯 `apps/models/user.rb`

`validates_presence_of :name`

---
### 3-3
不更改原本的註冊流程也能讓新/舊使用者也能自動擁有 name 欄位的方法，讓使用者註冊成功後才去修改 name 。

編輯 `apps/models/user.rb`

```rb
class User < ApplicationRecord
  before_save :initialize_name

  def initialize_name
    if self.name == '' || self.name == nil
      self.name = self.email.split("@").first
    end
  end
end
```

在回存資料庫之前, 先判斷 name 這個欄位有沒有填? 是不是為空? 如果沒有填的話, 取 email 小老鼠前面的字串預設為使用者名稱
