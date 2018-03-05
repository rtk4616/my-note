上一篇，我們記錄的是使用者的追蹤記錄

透過 @user.follow_targets，找出 @user 追蹤的人。

這一節 我們要來設計用 @user.follower 找出 追隨 @user 的人。

複習 User model @user.follow_targets：  
```rb
has_many :followships

has_many :follow_targets, through: :followships
```

當我們呼叫 @user.follow_targets 時，rails 會先去 followships table ( FK = user_id ) 用外鍵搜尋 @user 的追蹤記錄，然後再透過追蹤記錄把 follow_target_id 找出來。

現在我們要反過來找出追蹤 @user 的有哪些人。

很類似我們建立 follow_targets 方法時的步驟：  
```rb
# 我們先把所有參數列出來：
has_many(
  # 關聯方法命名
  :inverse_followships,

  # 關聯資料表名稱
  class_name: "Followship",

  # 自己的主鍵，要指向對方的哪一個外鍵
  primary_key: "id",

  # 對方的外鍵：找出被追蹤者 = PK 的資料
  foreign_key: "follow_target_id"
)

# 觀察哪些有依照慣例，僅 PK 依照慣例
# 設定反向追蹤記錄，找出追隨使用者的有哪些人
has_many :inverse_followships, class: "Followship", foreign_key: "follow_target_id"
```

再來，找出記錄之後，要回 users table 找出那些人：  
```rb
# 找出追蹤者的資料
has_many :followers, through: :inverse_followships, source: :user
```

接下來要實際建立資料測試一下，並顯示出來，可以透過 user.follwers 。

---
### Followship 前端部分

然後教材要求 把下列資料都顯示在使用者頁面
- 評論過的餐廳：@commented_restaurants 在右上
- 收藏過的餐廳：@favorited_restaurants 在右下
- 追蹤了誰：@follow_targets (@followings) 在左上
- 追蹤自己的人：@followers，這可能要手動創了 在左下

這次弄比較久的有圖片強制縮小，變圓圈很直覺，`img-circle` 我還直接猜到 XD。

還有 followings 與 follwers 列表的部分，本來還以爲要用 table 結果是 unordered list 與 list-group。

---
_rails 課程到這邊告一個段落_

練習筆記也打在這邊吧。練習絕對是必要的。
1. 先回顧一下 follow/unfollow 流程。
2. 臨摹成 friend/unfriend。
3. 好友關係區，inverse_friendship 設計，找出將我加爲好友的人。


看了教材才發現，有要求命名：Friendship
- How do I reverse a 'rails generate'?
- rails destroy controller lalala
- rails destroy model yadayada
- rails destroy scaffold hohoho

Rails 3.2 adds a new d shortcut to the command, so now you can write:

- rails d controller lalala
- rails d model yadayada
- rails d scaffold hohoho

所以我需要先反向刪掉 我的 model   
`rails d model Friend`

`rails g model Friendship`

```rb
class CreateFriendships < ActiveRecord::Migration[5.1]
  def change
    create_table :friendships do |t|
      # 設定外鍵名稱
      # 來自 User model
      t.integer :user_id

      # 來自 Friendship model
      # 暫時這樣命名
      t.integer :friend_target_id

      t.timestamps
    end
  end
end
```

---
### 設定 Model 關聯：

- has_many
  - 一個使用者與其他使用者建立朋友關係
  - User model
  - user has_many friendships
- belongs_to
  - 朋友關係屬於使用者
  - Friendship model
  - friendship belongs_to user
  - *朋友關係也屬於被使用者結交的朋友*
  - friendship belongs_to friend_target
- has_many :through
  - 使用者能透過朋友關係，擁有很多朋友
  - User model
  - user has_many friend_targets, through: :friendships

設定完之後，就能夠用關聯方法：
- 用 `@user.friendships` 找出 @user 的交友記錄。
- 用 `@user.friend_targets` 找出 @user 的交友記錄裡有哪些人。

照著之前的 followship 弄完，真快才2x分鐘。。。

---
### 追加目標

view 的頁面指定要新的 route path 跟 view 頁面

- 資料方面：設計一個 inverse_friendship 關聯方法，找出誰把我當成朋友。
- 在 User Model 新增 all_friends 方法，找出不重複的好友清單：主動加入 + 相別人提出的好友關係。
- 在 user show 頁面，顯示 我的好友頁面，用 member 設定爲 users#friend_list。

先弄 route，再弄關聯方法 ，最後 all_friends 方法

- route 路徑爲：friend_list_user_path(current_user.id)
- inverse_friendship 關聯設定
- all_friends method
- user friends view
