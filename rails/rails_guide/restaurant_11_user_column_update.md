增加 User Model 後的待辦(更新)事項：
1. 待解決舊的 User 資料 name 欄位爲 nil 的問題：3.3 的另一個手動做法。
  1. seed 與 fake data 是否因應使用者欄位增加而需要更新？
    1. seed 應該不用？admin 帳號的部分可能要。
    2. fake data 需要更新。
2. 調整 user name 欄位需要出現的地方:
  1. 更新餐廳 show 頁面評論的使用者名稱。
  2. 待補充。。。
3. 客製化 Devise 註冊功能，讓使用者在註冊時就能新增 name 欄位。
  1. 註冊時的欄位屬性是否需爲必填？
  2. 必填與不必填的做法都要會，這邊應該跟之前加在 model 的 validates_presence_of 用法不同。
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

3.
