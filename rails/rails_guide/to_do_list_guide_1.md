# Rails App ToDoList Part1, Overview and Model

## 前言
我想要用 Rails 練習做一個待辦清單 TO DO LIST：   
(第二個 Rails app 練習範例)  
先簡單想像一下我的ToDoList會長怎麼樣：

```html
待辦事項  時間  動作

未完成
1.            完成  編輯  刪除
2.            完成  編輯  刪除
3.            完成  編輯  刪除

已完成
1.            編輯  刪除
2.            編輯  刪除
3.            編輯  刪除
```

這個簡單的待辦事項跟我們的從零開始的章節結構非常像，就是多了一個 `已完成` 或是 `未完成` 的欄位，還有那個 `完成` 待辦事項後的按鈕，對 `未完成的待辦事項` 按了 `完成` 之後，那個待辦事項就要被轉移到 `已完成區`。

這是我對於 TO DO LIST 的初步簡單想法。

---
## 追加條件
接下來要練習的 to do list 追加一點條件限制：  
1. 欄位是 name, deadline, description。
- name：待辦事項名稱。
- description：待辦事項說明。
- deadline：a time or day by which something must be done，待辦事項完成期限。
2. 使用者可以對待辦事項進行 完成，檢視，編輯，刪除。
3. 如果時間超過 deadline 而未完成該待辦事項則該待辦事項不能被刪除並移動到過期區。
4. [補充] 用 bootstrap 美化 to do list。

好，那我想像的待辦清單會長這樣：

```html
未完成
待辦事項名稱(Name)    截止時間(Deadline)  說明(Desciption)
1.            完成  檢視  編輯  刪除
2.            完成  檢視  編輯  刪除
3.            完成  檢視  編輯  刪除

已完成
待辦事項名稱(Name)    截止時間(Deadline)  說明(Desciption)
1.            檢視  編輯  刪除
2.            檢視  編輯  刪除
3.            檢視  編輯  刪除

已過期
待辦事項名稱(Name)    截止時間(Deadline)  說明(Desciption)
1.            檢視  編輯
```

可以把一些新的需要學習的項目列個清單：
Deadline 欄位：除了記錄截止時間之外，還要讓過期的待辦事項自動移至過期區，而過期區的待辦事項沒有 完成 與 刪除(以及編輯？)按鈕。  

待辦事項的狀態(Status)：
1. 未完成，完成按鈕：完成後移至完成區(或是打勾)，到這裡我才發現會不會弄得太複雜了，不過概念都很相似。
2. 已完成，與我們的上一個範例最像的區域。
3. 已過期，跟 deadline 以及 現在時間有關係的欄位。

列表之後發現，我們除了要多增加一個 deadline 欄位之外，還需要一個記錄待辦事項狀態(Status)的欄位。  
Status 可能可以這樣分辨：  
1：表示已完成。
0：表示未完成。
-1：表示已過期。

---
## git branch
我想了一下，從這篇筆記開始我似乎就是想拿上一個練習的來改，也算合理(省時間)，所以這邊要練習用 git branch 開發，並把上一個練習貼個標籤貼紙 1.0.0。

支線任務：git 複習與筆記。  

```bash
# 查看 SHA1 碼
git log --oneline

# 貼上標籤
git tag tag_name -a <SHA1>

# 開新分支給 todolist
git branch todolist

# 切換到新分支
git checkout todolist
```

好，接下來，開始一步一步改寫 Rails。

---
## 改寫前的準備
參考攻略：[Ruby on Rails 初試啼聲(下)](https://medium.com/@weilihmen/ruby-on-rails-%E5%88%9D%E8%A9%A6%E5%95%BC%E8%81%B2-%E4%B8%8B-ce3e9f27515c)

蠻有趣的我的想法有稍微跟上作者，只是順序不大一樣。

作者是先研究 Ruby 以及 Rails 裡 Time 的用法，我還是不太有辦法照他的順序，所以我照我自己的順序搭配著跑。
> Route -> Controller -> Model -> Controller -> View -> Controller。

1. Model
2. Controller
3. Route
4. View

---
## ToDoList Model
根據我們想像的雛形思考我們 Model 資料庫所需要的資料類型：  
```html
未完成
待辦事項名稱(Name)    截止時間(Deadline)  說明(Desciption)
1.            完成  檢視  編輯  刪除
2.            完成  檢視  編輯  刪除
3.            完成  檢視  編輯  刪除

已完成
待辦事項名稱(Name)    截止時間(Deadline)  說明(Desciption)
1.            檢視  編輯  刪除
2.            檢視  編輯  刪除
3.            檢視  編輯  刪除

已過期
待辦事項名稱(Name)    截止時間(Deadline)  說明(Desciption)
1.            檢視  編輯
```

我們資料庫需要的欄位：
1. name 是 string 字串
2. deadline，Rails 關於時間或是日期要怎麼存，這時候就要查資料了，[攻略的連結](https://stackoverflow.com/questions/28293835/how-to-add-a-date-attribute-in-rails-models)。  
  從此得知 deadline(時間)的資料形態可以用 date。
3. description 也是 string 字串。
4. 還有我們需要一個欄位儲存該 todolist 是否完成(done)，我們用 boolean (True or False)。
5. 攻略有個 finaldate 也是存成 string ，沒說明用意，我們繼續看下去，有需要再用 rails g migration <migration_name>，修改 migration file 就好了。

### 建立 ToDoList Model, Part1
```rb
rails g model Task
```

編輯 db/migrate/time_create_tasks.rb：

```rb
class CreateTasks < ActiveRecord::Migration[5.1]
  def change
    create_table :tasks do |t|
      t.date "deadline"
      t.text "description"
      t.boolean "done"

      t.timestamps
    end
  end
end
```

`rails db:migrate`

去 schema.db 觀察確認一下欄位是否正常。

---
### 建立 ToDoList Model, Part2 設定欄位預設值
`rails g migration add_default_value_to_done`
```rb
class AddDefaultValueToDone < ActiveRecord::Migration[5.1]
  def change
    # 修改欄位型別：change_column(table_name, column_name, type, options)
    change_column(:tasks, :done, :boolean, :default => false)
  end
end
```

讓 :done 欄位的預設值爲 false。

rails db:migrate

觀察一下 schema.rb 是否正常。

---
### 建立 ToDoList Model, Part3 Git 提交備份
OK，記得 commit 一下。
```bash
git status > temp01
vim temp01
# 進入 vim 後用 dd ，刪除不必要的行數
git add `cat temp01`
git status
git commit
git remote -v # git remote 也可
git push github todolist
```

---

---
### [其他] 我的 rails migrate debug 練習筆記
我是從上一個練習Rails的範例修改來的，所以會用 `rails g migration edit_ToDoList_model`。
> 我個人覺得這樣做除了跟攻略會略有差異之外，還可以順便複習上一個例子。

在想要怎麼改之前，我們要先觀察一下我們的 Model database 現在長怎麼樣。  
觀察 `db/migrate/schema`

```rb
ActiveRecord::Schema.define(version: 20180118074716) do

  create_table "users", force: :cascade do |t|
    t.string "name"
    t.string "mail"
    t.string "gender"
    t.integer "age"
    t.datetime "created_at", null: false
    t.datetime "updated_at", null: false
  end

end
```

從 schema 我們可以看到我們要保留的欄位(只有name)跟不需要的欄位(都拿掉)。

> 這樣改還有個問題就是本來的資料會？我們繼續跑下去。

這時候要回頭做一下筆記，migration file 裡常用的 method。

add_column、rename_column 以及 remove_column，接下來為各位介紹更多常見的方法：  
(先看資料表欄位控制的部分)
```rb
# 資料表控制
新增資料表：create_table(table_name, option)
移除資料表：drop_table(table_name)
修改資料表名稱：rename_table(old_table_name, new_table_name)

# 資料表欄位控制
新增欄位：add_column(table_name, column_name, type, options)
修改欄位名稱：rename_column(table_name, old_column_name, new_column_name)
修改欄位型別：change_column(table_name, column_name, type, options)
移除欄位：remove_column(table_name, column_name)

# 欄位索引
新增索引：add_index(table_name, columns, options)
移除索引：remove_index(table_name, index)

# 外鍵控制
新增外鍵：add_foreign_key(from_table_name, to_table_name, options)
刪除外鍵：remove_foreign_key(from_table_name, to_table_name, options)
```

編輯 db/migrate/<time>\_edit_to_do_list_model.rb

```rb
# 這裡我想要用一次比較舊的方法，不用 change method
# 我想知道爲什麼要改用 change，而且這樣比較能熟悉 migrate 與 rollback
# 我們用 up 與 down method，先寫 migrate(up)要做什麼，就去寫 rollback(down)的部分。
# 我沒猜錯的話，symbol(:users) 與 string("users") 應該是不同的東西，所以我待會這樣執行會有錯。
def up
  # 好問題我的 table name 會是什麼？觀察 schema 那邊的 create_table 後面的參數。
  remove_column(:users, :mail, :gender, :age)

  add_column(:users, :deadline, :date)
  add_column(:users, :description, :text)
  add_column(:users, :done, :boolean)
end

def down
  add_column(:users, :mail, :string)
  add_column(:users, :gender, :string)
  add_column(:users, :age, :integer)

  remove_column(:users, :deadline, :description, :done)
end
```

編輯完成就下 `rails db:migrate` 試試看吧！

竟然成功了，沒 error，Rails 太強了吧，請去檢查你的 `schema.rb`。

如果你照我上面那樣打，你的 Schema 可能會長這樣：

```rb
ActiveRecord::Schema.define(version: 20180131115135) do

  create_table "users", force: :cascade do |t|
    t.string "name"
    t.string "gender"
    t.integer "age"
    t.datetime "created_at", null: false
    t.datetime "updated_at", null: false
    t.date "deadline"
    t.text "description"
    t.boolean "done"
  end

end
```

有發現什麼異常事情嗎？那個 gender 與 age 欄位沒刪除成功，只有第一個參數 :mail 刪掉了。  
(觀察 rails log 應該也會發現)

也就是說 `remove_column(table_name, column_name)`，只接受第一個 column_name 參數，後面的會無視。

那現在該怎麼辦呢？
1. `rails db:rollback`，然後再去編輯剛才的 migration file？
2. `rails g migration migration_name`，再編輯一個新的 migration file 來處裡這個情況。
3. 用 `git checkout .`。

選擇 1. 的話，可以因爲我不想留下有問題的程式碼，也可以觀察 up down method 是否有寫對。

我的 \_edit_to_do_list_model.rb：

```rb
class EditToDoListModel < ActiveRecord::Migration[5.1]
  def up
    remove_column(:users, :mail)
    remove_column(:users, :gender)
    remove_column(:users, :age)

    add_column(:users, :deadline, :date)
    add_column(:users, :description, :text)
    add_column(:users, :done, :boolean)
  end

  def down
    add_column(:users, :mail, :string)
    add_column(:users, :gender, :string)
    add_column(:users, :age, :integer)

    remove_column(:users, :deadline)
    remove_column(:users, :description)
    remove_column(:users, :done)
  end
end
```

`rails db:rollback` go：

```rb
$ rails db:rollback
== 20180131115135 EditToDoListModel: reverting ================================
-- add_column(:users, :mail, :string)
   -> 0.0009s
-- add_column(:users, :gender, :string)
rails aborted!
StandardError: An error has occurred, this and all later migrations canceled:

SQLite3::SQLException: duplicate column name: gender: ALTER TABLE "users" ADD "gender" varchar
```

請注意是 `20180131115135` 這個版本的 migration file 有問題。  

還有隨時回去重新開啓 `schema.rb`，確認目前是在哪個 `version: 20180131115135`。

這邊我費了一番功夫才 debug 成功。

不，因爲我 rails db:migrate 的 up 沒全部成功，造成現在執行 rollback 要把欄位加回來的時候發現有重複的欄位，因而造成 error。

這時候再回頭去改有問題的 migration file 是沒有用的。

因此，只能選擇 `rails g migration migration_name`，再編輯一個新的 migration file 來處裡這個情況。

`rails g migration drop_gender_age_column`

```rb
class DropGenderAgeColumn < ActiveRecord::Migration[5.1]
  def up
    remove_column(:users, :gender)
    remove_column(:users, :age)
  end
end
```

這次我們就只寫 up 是因爲：我還希望再 rollback 到我們上一個檔案 `<time>\_edit_to_do_list_model.rb`，重新跑過確認一次那樣寫的 migration file 是 OK 的，所以我希望這個 drop_gender_age_column 檔案 rollback 的時候，不要執行任何動作，因爲他的功用只需要把現在的資料庫修正完成。

Debug 到最後 schema 裡的資料庫欄位都正確之後，測試沒寫 down method rollback 時會不會執行東西：

```rb
$ rails db:rollback
== 20180131133405 DropGenderAgeColumn: reverting ==============================
== 20180131133405 DropGenderAgeColumn: reverted (0.0000s) =====================
```

這時候去看 schema.rb 的 version 就會是我們第一個建立的 migration file。

`這時候把 \drop_gender_age_column.rb 刪除，但是我之後發現這會產生另一個問題，因此要先去確認情況。`

請先下 `rails db:migrate:status`：
```rb
$ rails db:migrate:status


 Status   Migration ID    Migration Name
--------------------------------------------------
   up     20180118074716  Create users
   up     20180131115135  Edit to do list model
   up     20180131133405  ********** NO FILE **********
```

可以發現我們過去已經做過 migration 的狀態，假設你跟我一樣在這時候把最後一個 migration file 刪除了，那你接下來下的 `rails db:migrate` 或是 `rails db:rollback` 都不會有任何反應，也沒有其他提示訊息。

下了 rails db:migrate:status 才發現 Rails 不會跳過 NO FILE。

因此我的做法是：  
`$ touch db/migrate/20180131133405_drop_gender_age_column.rb`

回頭去把那個 migration file 建立起來，這時候做筆記就很有好處了，裡面的 method 留空。

```rb
$ rails db:migrate:status


 Status   Migration ID    Migration Name
--------------------------------------------------
   up     20180118074716  Create users
   up     20180131115135  Edit to do list model
   up     20180131133405  Drop gender age column
```

這樣子 rails 就有抓到 migration file 了。  
這時候我們小心一點：  
1. 先執行一次 rails db:rollback。
2. 執行 `rails db:migrate:status`。
```rb
rails db:migrate:status

 Status   Migration ID    Migration Name
--------------------------------------------------
   up     20180118074716  Create users
   up     20180131115135  Edit to do list model
  down    20180131133405  Drop gender age column
```
3. 這次先不刪除那個 migration file 改名就好。
4. 再輸入 `rails db:migrate:status`。
```
Status   Migration ID    Migration Name
--------------------------------------------------
  up     20180118074716  Create users
  up     20180131115135  Edit to do list model
```
5. 成功啦！YA！這時候才確認能把那個檔案刪除。

一樣，再對 `Edit to do list model` 測試一次 rollback 與 migrate。

完成...，照理說這個 model 現階段應該算是非常簡單，只是我對 migrate 還不夠熟悉。  
所以才會有這麼多狀況，經驗值增加啦。

其實過程中有再去翻龍哥的教材，真的是棒...。

恭喜你， Model 完成啦！

### Routes Part1
編輯 `config/routes.rb`，先把首頁弄好

```rb
Rails.application.routes.draw do
  # For details on the DSL available within this file, see http://guides.rubyonrails.org/routing.html
  # get "/users", to: "users#index"
  get "/", to: "users#index"
end
```

> 直到這一步我才發現似乎 model 還是重新創過換個 table 名字比較好，昨天怎麼沒注意到，那個 `users#index` 會跟慣例不相符，資料庫也不一定要同一個一直改，這樣複雜度可能也會提高太多。

所以...model 重新來過：

```rb
# 確認 migration status
$ rails db:migrate:status
Status   Migration ID    Migration Name
--------------------------------------------------
  up     20180118074716  Create users
  up     20180131115135  Edit to do list model

# rollback 一個 migration
$ rails db:rollback

# 再確認一次 migration status
$ rails db:migrate:status
Status   Migration ID    Migration Name
--------------------------------------------------
  up     20180118074716  Create users
 down    20180131115135  Edit to do list model

# ok 可以把 Edit to do list model 更名了

# 再確認一次 migration status
$ rails db:migrate:status
Status   Migration ID    Migration Name
--------------------------------------------------
  up     20180118074716  Create users

# OK，搞定，簡單看一眼 schema.rb，確認資料庫情形
```
