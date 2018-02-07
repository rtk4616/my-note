### 常用指令

```
rails routes
```

Route 與 Controller 要一起看。

### Routes 手動設定
Rails Routes 的設定檔在 `config/routes.rb`。

```rb
Rails.application.routes.draw do
  get "/users", to: "users#index"
end
```

當使用者輸入：http://0.0.0.0:3000/users 這段網址時，  
就會觸發 Routes 將指定的動作 `index` 傳給 Controller，  
讓 Controller 去做相對應的事情。

### 用 Resources 設定 Routes
上一小節要一個一個動作手動設定(通常有8種actions要設定)，用 resources 就可以快速套用慣例的 routes 設定。  
慣例的 routes 設定的主要是精神是 REST 與 RESTful，下一節介紹。

```rb
Rails.application.routes.draw do
  resources :users
end
```

接著下 rails routes 指令觀察，rails 已經完成了哪些 routes action pairs。

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

### REST 與 RESTful
REST核心概念把每個網址都當作`資源(resource)`來看，對同一個資源做不同的動作(HTTP `Verb`)會得到不同的結果。

符合 REST 概念設計的網址又稱作 RESTful Route。

可以從上面 `rails routes` 指令顯示的例子觀察：

```
$ rails routes
   Prefix Verb   URI Pattern               Controller#Action
    users GET    /users(.:format)          users#index
          POST   /users(.:format)          users#create
```

Prefix 都是 `users`，也就是 http://0.0.0.0:3000/users ，這樣的網址，重點在後面的 `/users`。  
而 Verb 一個是 GET 另一個是 POST，再往 Controller#Action 觀察，可以發現各自對應到不同的 action，controller 是一樣的。  
* GET   對應到 users#index，就會去 app/controllers/users_controller.rb 找 index method 執行。  
* POST  對應到 users#create，就會去 app/controllers/users_controller.rb 找 create method 執行。

### `Prefix` 的用處

"Prefix" + "\_path" =   

"Prefix" + "\_url" =

### `URI Pattern` 的用處
URI 的 I 是 `identifier` 標示，就是整串網址 URL 扣除 DNS 後留下的部分。

舉例：users#index URI 是 `/users(.:format)`， .:format ~~指的是網頁格式吧？~~ 通常是 .html (可以省略不寫)。

所以 index action 換成完整的網址會是 `https://0.0.0.0:3000/users.html`
