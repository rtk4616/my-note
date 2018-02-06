# 餐廳論壇 PartC admin login
## admin controller 登入驗證設計
### 如果專案只有一個 controller
編輯 `app/controllers/admin/restaurants_controller.rb`  
```rb
class Admin::RestaurantsController < ApplicationController
  before_action :authenticate_user!
  before_action :authenticate_admin
  def index

  end

  private
  def authenticate_admin

  end
end
```

`authenticate_user!`：我們上一節介紹過了，Devise 套件提供的驗證登入方法，確定使用者有登入，有回傳 User instance。  
`authenticate_admin`：本節重點，驗證使用者是否爲管理員，驗證 instance 的 role 欄位是否有 "admin"。

接下來我們要談的是 `private method authenticate_admin` 放的位置，請注意到我們這個 Admin::RestaurantsController class 是從 `ApplicationController` 繼承來的。

因此如果我們 `把這個 private method 放在 ApplicationController` 就可以讓未來新增的 controller 都能用了，因爲他們都會從 ApplicationController 繼承。

### 如果專案有多個 controller
#### 在 application_controller.rb 加入驗證Admin的方法
編輯 `app/controllers/application_controller.rb`：
```rb
class ApplicationController < ActionController::Base
  protect_from_forgery with: :exception

  private
  # for admin authentication
  def authenticate_admin
    unless current_user.admin?
      flash[:alert] = "Not allow!"
      redirect_to root_path
    end
  end
end
```

除非 current_user 爲 admin，否則：
* 給一個 Not allow 警告訊息
* 跳回首頁

`.admin?` 是待會我們要寫的 User instance method(因爲 current_user 會回傳 User instance，所以該 instance 可以用來呼叫 .admin?)，.admin? 會回傳 true 或 false。

#### 設計 .admin? instance method
編輯 `app/models/user.rb`
```rb
class User < ApplicationRecord
  # Include default devise modules. Others available are:
  # :confirmable, :lockable, :timeoutable and :omniauthable
  devise :database_authenticatable, :registerable,
         :recoverable, :rememberable, :trackable, :validatable

  # for authenticate_admin method check role column value
  def admin?
    return self.role == "admin"
  end
end
```

編輯 `app/controllers/admin/restaurants_controller.rb`  
```rb
class Admin::RestaurantsController < ApplicationController
  before_action :authenticate_user!
  before_action :authenticate_admin

  def index

  end
end
```

### 建立網站管理員身份
因爲一般的使用者申請完的 role 欄位一定是 nil。

因此我們要手動賦予 role = "admin"。

```bash
rails console
# 找到剛才註冊的使用者
user = User.last
user.role = "admin"
user.save
user.role
```

`rails s` 進行測試：
- 一般使用者 登入後台頁面，會跳到前台而且有`Not allow!`的提示訊息。
- admin 登入後台頁面，登入會到後台。

不論是輸入 0.0.0.0:3000/ 還是 http://0.0.0.0:3000/akjdhkajsdh admin 頁面，登入頁面都是 `http://0.0.0.0:3000/users/sign_in`。

---
