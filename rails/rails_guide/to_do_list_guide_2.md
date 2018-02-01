# Rails App ToDoList Part2, Routes
## 前言
參考攻略：[Ruby on Rails 初試啼聲(下)](https://medium.com/@weilihmen/ruby-on-rails-%E5%88%9D%E8%A9%A6%E5%95%BC%E8%81%B2-%E4%B8%8B-ce3e9f27515c)

作者第二部分就先從 Route 開始下手了，很合理，通常第二部分不是 Routes 就是 Controller。

支線任務：[爲你自己學 Ruby on Rails Routes](https://railsbook.tw/chapters/11-routes.html)

書中舉例網址：

```html
https://test.com/name/123
```

Rails 會根據 config/routes.rb 裡的檔案內容，比對上面網址後面的 `/name/123` 要去找哪個 action。

而初試啼聲作者的 routes，要分兩個階段看：  

### Routes Part1, 首頁設定
編輯 `config/routes.rb`，先把首頁弄好

```rb
Rails.application.routes.draw do
  # For details on the DSL available within this file, see http://guides.rubyonrails.org/routing.html
  # get "/users", to: "users#index"
  get "/", to: "tasks#index"
end
```

這個設定是當使用者在瀏覽器輸入 https://0.0.0.0/ 的時候，routes 會把網頁導向 controller 的 index action。

`rails routes` 觀察 Routes 設定。

這樣子我們的首頁就設定完成了。

```log
Prefix Verb URI Pattern Controller#Action
       GET  /           tasks#index
```

### Routes Part2, 整體路由設定

因爲這邊是我非常不熟悉的地方，先觀察一下作者是怎麼寫的：

```rb
resources :tasks do
  member do
    put :done
  end
end
```

ok，關鍵字是 :tasks 後面的 do...done，還有裡面的 `member`。

我們先分段看：
```rb
Rails.application.routes.draw do
  # For details on the DSL available within this file, see http://guides.rubyonrails.org/routing.html
  # get "/users", to: "users#index"
  get "/", to: "tasks#index"
  resouces :tasks
end
```

觀察一下這樣子的 routes 會長怎麼樣：
```log
$ rails routes
   Prefix Verb   URI Pattern               Controller#Action
          GET    /                         tasks#index
    tasks GET    /tasks(.:format)          tasks#index
          POST   /tasks(.:format)          tasks#create
 new_task GET    /tasks/new(.:format)      tasks#new
edit_task GET    /tasks/:id/edit(.:format) tasks#edit
     task GET    /tasks/:id(.:format)      tasks#show
          PATCH  /tasks/:id(.:format)      tasks#update
          PUT    /tasks/:id(.:format)      tasks#update
          DELETE /tasks/:id(.:format)      tasks#destroy
```

就是我們之前有練習過的部分加上額外的首頁設定。

接著請參閱 [爲你自己學 Ruby on Rails Routes](https://railsbook.tw/chapters/11-routes.html#resources)。

我們的情境跟龍哥文章內容所說的 "確認訂單"(完成 task) 或是 "取消訂單"(這個狀態我們似乎暫時不需要) 更改`訂單狀態`的 routes 設計，也就是說我們要額外給`完成任務(task)`這個按鈕也有對應的 URI Pattern 與 Controller#Action，這邊要稍微消化一下龍哥的教材哦。

有兩種方法可以嘗試，一種是 `collection` 和另一種是 `member`。

collection 目前理解是比較像是對整體狀態搜尋時在用的，例如文中所舉例的 `檢視所有已經取消的訂單`，那麼我們之後或許可以來個 `檢視所有已完成/已過期的任務` 。

member 的話比較符合我們需要的，文中舉的例子是 `確認第二號訂單` 以及 `取消第3號訂單`，而我們的需求則是 `完成第？號任務`：
```rb
# 完成第 3 號任務
POST /tasks/3/done
```

因此我們的 routes 會長這樣：
```log
$ rails routes
   Prefix Verb   URI Pattern               Controller#Action
          GET    /                         tasks#index
done_task POST   /tasks/:id/done(.:format) tasks#done
    tasks GET    /tasks(.:format)          tasks#index
          POST   /tasks(.:format)          tasks#create
 new_task GET    /tasks/new(.:format)      tasks#new
edit_task GET    /tasks/:id/edit(.:format) tasks#edit
     task GET    /tasks/:id(.:format)      tasks#show
          PATCH  /tasks/:id(.:format)      tasks#update
          PUT    /tasks/:id(.:format)      tasks#update
          DELETE /tasks/:id(.:format)      tasks#destroy
```

仿照書中的例子我們產生了新的 route：`done_task POST   /tasks/:id/done(.:format) tasks#done`，在使用者輸入 `/tasks/3/done` 的時候，routes 就會去找對應的 controller#done method。

Routes 到這邊就先告一段落囉。  
我們下一節前往編輯 Controller。

而關於作者用的 `put :done` 語法，未來有機會再研究。
