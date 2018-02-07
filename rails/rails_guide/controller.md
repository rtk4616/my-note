### 常用指令

```
rails g controller users
```

## CRUD Controller Actions
CRUD: Create, Read, Update, Delete.  

CRUD 是操作資料最常見的 actions，而 action 寫在 Rails 的 Controller 裡，因此也被稱爲 Controller#Action。

舉個例子：

| CRUD | Description | Controller#Action|
| :------------- | :------------- |:-|
|Create|建立使用者|New - 建立新使用者的資訊(姓名，信箱，信別，年齡)|
|Create|上傳新使用者資訊|Create - 上傳新使用者   |
|Read   |使用者可以一次觀看所有使用者資料   |Index - 顯示所有使用者資訊(在主畫面)   |
|Read   |使用者可以觀看其中一個使用者的詳細資訊   |Show - 瀏覽一個指定的使用者詳細資料   |
|Update   |使用者可以編輯一個使用者資訊   |Edit - 編輯一個使用者資訊   |
|Update   |使用者編輯完一個使用者資訊後可以更新   |Update - 上傳更新後的使用者資訊到資料庫   |
|Delete   |使用者可以刪除不要的使用者資訊   |Destroy - 刪除不想要的照片   |

每個 Action 都會對應到 Controller(也就是 app/controllers/users_controller.rb) 裡的 method。

### View 的 四個 CRUD 頁面
4個 CRUD 操作，可以對應到 7個 Controller#Action，實際上會對應到 4個 views 的 html.erb 檔案：
- index.html
- show.html
- new.html
- edit.html

剩下沒有對應到的 Controller#Action - #Create, #Update, #Destroy 只要做相對應的功能即可，不需要頁面。

### Routes 的 CRUD設定

而這些 Controller#Action 與 rails routes 設定互相對應：

```
$ rails routes
   Prefix Verb   URI Pattern               Controller#Action
    users GET    /users(.:format)          users#index
          POST   /users(.:format)          users#create
 new_user GET    /users/new(.:format)      users#new
edit_user GET    /users/:id/edit(.:format) users#edit
     user GET    /users/:id(.:format)      users#show
          PATCH  /users/:id(.:format)      users#update
          PUT    /users/:id(.:format)      users#update
          DELETE /users/:id(.:format)      users#destroy
```

互相對照可以發線 Verb = GET ，才有製作網頁頁面的需求。
