### 常用指令

```bash
# 初始化 model
rails g model User
# 產生 migration file
rails g migrate migration_name(add_id_to_user)

# 進行 migrate (up method)
rails db:migrate

# 進行 rollback (down method), 回復至上一個 migration
rails db:rollback

# 回復前 n 個 migration
rails db:rollback STEP=n

# 查看目前資料庫 Migration 版本
rails db:version

# 顯示目前 migration 狀況, 可以觀察 Status(Up or Down), Migration ID(檔名流水號), Migration Name
rails db:migrate:status

# 執行 db/seeds.rb 載入種子資料
rails db:seed
```
___

### Migration
Migration 是一個「資料庫變更的紀錄檔」

這個記錄檔在生成時會自動產生戳記
你的資料庫每執行一次 migration 都會紀錄戳記
所以資料庫會知道你執行過哪一個 migration、哪個還沒

db:migrate 就是讓資料庫「執行還沒執行過的 migration」
執行過的 migration 則會自動略過

`一個 migration file 對於資料庫來說只會被執行一次`

來源：[Ruby on Rails-談 Migration 概念與用法](https://medium.com/@weilihmen/ruby-on-rails-%E8%AB%87migration%E6%A6%82%E5%BF%B5%E8%88%87%E7%94%A8%E6%B3%95-22a52714f51f)

一般有兩種方法產生 migration file:  
初始化 Model 時 `rails g model User`  
產生指定 migration file `rails g migrate add_id_to_user`  

### 編輯 Migration File
migration file 會在 `db/migrate/yyyymmddhhmmss_migration_name.rb`。

我們透過 migration file 來與資料庫互動。

`rails g model User` 初始化產生的 migration file 與 `rails g migrate add_id_to_user` 的會不一樣。  
因爲剛創造的 Model 還沒有建立資料表(table)，所以 migrate_file 預設就有修改資料表的function `create_table`。  

而 `rails g migrate add_id_to_user` 產生的 migration file 就僅有空白的 change method。

##### 常用的 migration function
資料表操作：

```rb
# 新增資料表
create_table(table_name, options)
# 移除資料表
drop_table(name)
# 修改資料表名稱
rename_table(old_table_name, new_table_name)
```

資料表欄位操作：

```rb
# 新增欄位
add_column(table_name, column_name, type, options)
# 修改欄位名稱
rename_column(table_name, old_column_name, new_column_name)
# 修改欄位設定
change_column(table_name, column_name, type, options)
# 移除欄位
remove_column(table_name, column_name)
```

欄位索引操作

```rb
# 新增索引
add_index(table_name, column_number?, options)
# 移除索引
remove_index(table_name, index)
```

外鍵(foreign key)操作

```rb
# 新增 foreign key
add_foreign_key(origin_table_name, target_table_name, options)
# 移除 foreign key
remove_foreign_key(origin_table_name, target_table_name, options)
```
___
### rails db:`migrate` 與 rails db:`rollback` 介紹
介紹 migration file 裡的 `change`, `up`, `down` method：  
`change` = up or down  
`up` = db:migrate 時執行  
`down` = db:rollbcak 時執行  

預設在 migration file 裡的 `change` 會根據你的指令轉換成相應的動作。  
也就是說你寫了 up 要做的事情， rails 會在 rollback 時自動轉換成對應的 down 要做的事。

up, down method 似乎比較常用且清楚。
___
### Rails Database Schema
`Schema`(綱要) 是定義與描述資料庫裡的資料表與欄位。

Rails 在執行過 `rails db:migrate` 後，才會產生 schema 檔案。  
檔案在 db/schema.rb，可以觀察目前資料庫的現況。

___
### ActiveRecord
透過 ActiveRecord 我們不用寫 SQL 語法就能操作資料庫。
