# 多對多 Model 關聯

一個 User 可以追蹤很多使用者。

一個 User 可以被很多使用者追蹤。

| id | user_id | user_id |
| :- | :- |:-|
|1|4|7|

我們的關係紀錄表會像上表那樣子：記錄 user_id = 4 的使用者 追蹤了 user_id = 7 的使用者。

會發現欄位名稱相同，所以需要自訂一個名稱，保留主動那邊的名稱 `user_id`。

把被追蹤者的 id 改成 `follow_target_id`。

關係紀錄表命名爲 `followships` ， s 複數是慣例，跟英文文法沒有什麼關係。

| id | user_id | follow_target_id |
| :- | :- |:-|
|1|4|7|

## 定義資料模型
- Model `Followship`  
- Table `followships`  
- FK  `user_id`, `follow_target_id`

## 設定關聯方法
- 取得使用者的追蹤記錄  
`@user.followships`

- 使用者追蹤的其他使用者  
`@user.follow_targets`

## 實作 Model
`rails g model followships`

```rb
class CreateFollowships < ActiveRecord::Migration[5.1]
  def change
    create_table :followships do |t|
      t.integer :user_id
      t.integer :follow_target_id

      t.timestamps
    end
  end
end
```

## 設定 Model 關聯：語法詳解
先複習關聯設定的語法，針對 `has_many, belongs_to, has_many :through`。

**這些方法都是在自訂名稱**
有分`依照慣例`，或是`客製化`來決定要給哪些參數給 Rails 。

如果是依照慣例那可能都不用給(之後會舉例)。

如果不是那就要注意要給哪些參數了。

- has_many
  - class_name
  - primary_key
  - foreign_key

- belongs_to
  - class_name
  - primary_key
  - foreign_key

- has_many :through
  - source

---
### User-Favorite-Restaurant 收藏餐廳功能關聯
**替 User Model 新增一對多關聯**

#### 關於 has_many：`User`-Favorite-Restaurant

```rb
class User < ApplicationRecord
  # 一個使用者可以收藏很多餐廳
  has_many(
    # 關聯方法命名
    :favorites,

    # 關聯資料表名稱
    class_name: "Favorite",

    # 自己的主鍵，要指向對方的哪一個外鍵
    primary_key: "id",

    # 對方的外鍵
    foreign_key: "user_id"
  )     
end

# 因爲有依照慣例，因此我們之前都只有寫：
  has_many :favorites
```

`自己的主鍵，要指向對方的哪一個外鍵欄位。`

`要拿哪一個我們的主鍵當對方的外鍵。`

`依照慣例？`

就是外鍵欄位名稱是 "`該 model name`\_`id`"

- users table

| id  | name |  |
| :- | :- |:-|
|(PK) | | |

- favorites

| id | user_id |  |
|:-|:-|:-|
| | FK from users table | |

理論上，class_name, primary_key, foreign_key 都要指定，但是 Rails 會判斷如果有依照慣例的話，那這些參數都不需要寫。

接下來是 Restaurant Model 的 has_many：
```rb
class Restaurant < ApplicationRecord
  has_many(
    # 關聯方法命名
    :favorites,

    # 關聯資料表名稱
    class_name: "Favorite",

    # 自己的主鍵，要指向對方的哪一個外鍵
    primary_key: "id",

    # 對方的外鍵
    foreign_key: "restaurant_id"
  )
end

# 因爲有依照慣例，因此我們之前都只有寫：
  has_many :favorites
```

- restaurants table

| id | name |  |
|:-|:-|:-|
|(PK)| | |

- favorites table

| id | user_id | restaurant_id |
|:-|:-|:-|
| | FK from users table | FK form restaurants table |

---
#### 關於 belongs_to：User-`Favorite`-Restaurant

```rb
class Favorite < ApplicationRecord
  belongs_to(
    # 方法命名
    :user,
    # 關聯資料表名稱
    class_name: "User",
    # 自己的外鍵要對應到哪個對方的主鍵
    foreign_key: "user_id",
    # 對方的主鍵
    primary_key: "id"
  )

  belongs_to(
    # 方法命名
    :restaurant,
    # 關聯資料表名稱
    class_name: "Restaurant",
    # 自己的外鍵要對應到哪個對方的主鍵
    foreign_key: "restaurant_id",
    # 對方的主鍵
    primary_key: "id"
  )
end

# 因爲都有依照慣例因此：
  belongs_to :user
  belongs_to :restaurant
```

---
#### has_many :through
`User`-Favorite-`Restaurant`

`has_many :through` 多對多關聯專用，因此會跟 has_many 的參數選項區分開來

我們希望從 user 這邊透過關聯拿到 restaurant 的資料。

```rb
class User < ApplicationRecord
  has_many :favorites

  # 設定多對多關聯，透過 favorites 拿到 restaruants 的資料
  # Rails 會去 favorites table 找設定，然後發現 restaurants 方法的資料
  has_many :restaurants, through: :favortites
end

class Favorite < ApplicationRecord
  belongs_to :user
  belongs_to :restaurant
end

# 不過如果我們這時候爲了符合語義，將 restaurants 方法改名
class User < ApplicationRecord
  has_many :favorites

  # 這個時候到 favorites table 發現 沒有 "favorite_restaurant" 這個方法
  has_many :favorite_restaurants, through: :favorites

  # 因此，這時候要加上 source 參數
  # source 指向 來源的關聯方法名稱，這樣 rails 才找得到
  has_many :favorite_restaurants, through: :favorites, source: :restaurant
end
```

依照上述原則，我們來設定 followships

---
### User-Followship 追蹤關係 設定
- has_many
  - User 有很多追蹤關係 (followships)

- belongs_to
  - Followship 屬於 User (他追蹤了誰)
  - Followship 也屬於被 User 追蹤的人 (他)

- has_many :through
  - User 追蹤了很多的人 (follow_targets)

#### 設定使用者有很多追蹤關係 has_many
```rb
class User < ApplicationRecord
  # 先把所有參數寫上
  has_many(
    # 關聯方法命名
    :followships,
    # 關聯資料表名稱
    class_name: "Followship",
    # 自己的主鍵
    primary_key: "id",
    # 對方的外鍵
    foreign_key: "user_id"
  )

  # 有符合慣例，因此可以省略參數
  has_many :followships
end
```

---
#### 設定 Followship 屬於 User
```rb
class Followship < ApplicationRecord
  # Followship 屬於 User
  # 都有符合慣例，因此省略其他參數
  belongs_to :user
end
```

---
#### 設定 Followship 也屬於 User 追蹤的人
```rb
class Followship < ApplicationRecord
  # Followship 追蹤記錄屬於 User
  # 都有符合慣例，因此省略其他參數
  belongs_to :user

  # Followship 追蹤記錄也屬於被 User 追蹤的人
  belongs_to(
    # 關聯方法命名
    :follow_target,
    # 關聯資料表名稱 (不符合慣例)
    class_name: "User",
    # 我們的外鍵名稱 (雖然不是 Model名稱(followship)+id，但是關聯方法名稱 :follow_target 跟我們外鍵的命名方式一致，rails 也認得出來，因此不用保留 )
    foreign_key: "follow_target_id",
    # 對方的主鍵名稱
    primary_key: "id"
  )

  # 因此最後會剩下
  # Followship 追蹤記錄也屬於被 User 追蹤的人
  belongs_to :follow_target, class_name: "User"
end
```

---
#### 設定 User 追蹤了很多的人(follow_targets)
我們之前命名爲 `follow_tragets` 的方法會找出該 user 追蹤的所有人。

```rb
# 把 Followship Model 關聯設定放在上面參考
class Followship < ApplicationRecord
  belongs_to :user
  belongs_to :follow_target, class_name: "User"
end

# User Model 關聯設定
class User < ApplicationRecord
  has_many :followships

  # User 追蹤了很多的人(follow_targets),
  # user.follow_targets 會撈出 user 追蹤的人
  # 從 User model 出發，透過 followships table，找到 follow_targets
  # Rails 會判斷單數複數，因此 source 參數可以省略。
  has_many :follow_targets, through: followships, source: :follow_target

  # 最終結果
  has_many :follow_targets, through: followships
end
```

**還沒完！**

---
#### 設定 刪除 user 時連帶刪除 追蹤記錄的資料

```rb
class User < ApplicationRecord
  has_many :followships, dependent: :destroy

  has_many :follow_targets, through: followships
end
```

如此一來，就能夠用關聯方法：
- 用 @user.followships 找出 @user 所有的追蹤記錄。
- 用 @user.follow_targets 找出 @user 追蹤了哪些人。

<!-- 終於結束，連帶把之前的坑補起來。 -->
