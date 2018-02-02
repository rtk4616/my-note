# Rails App ToDoList Part4, View
## 前言
回顧一下我們(你)當初的構想：

```html
未完成
待辦事項名稱(Name)    截止時間(Deadline)  說明(Description)
1.            完成  檢視  編輯  刪除
2.            完成  檢視  編輯  刪除
3.            完成  檢視  編輯  刪除

已完成
待辦事項名稱(Name)    截止時間(Deadline)  說明(Description)
1.            檢視  編輯  刪除
2.            檢視  編輯  刪除
3.            檢視  編輯  刪除

已過期
待辦事項名稱(Name)    截止時間(Deadline)  說明(Description)
1.            檢視  編輯
```

### ToDoList View, Part1 把 new, edit, show 頁面完成

```bash
$ cd views/tasks/
$ touch edit.html.erb
$ touch index.html.erb
$ touch new.html.erb
$ touch show.html.erb
```

格式與上一次的練習一樣，改變數跟頁面名稱而已。

---
### ToDoList View, Part2 把 index 頁面完成

架構也很簡單：  

```html
<!-- 未完成待辦清單 -->
<table class="table table-striped table-condensed">
  <thead>
    <tr>
      <td>未完成待辦事項</td>
      <td>截止日期</td>
      <td>說明</td>
      <td>操作</td>
    </tr>
  </thead>
  <tbody>
    <% @undone_tasks.each do |task| %>
    <tr>
      <td><%= task.name %></td>
      <td><%= task.deadline %></td>
      <td><%= task.description %></td>

      <td>
        <%= link_to "完成", task_path(task.id), method: "done" %>
        <%= link_to "檢視", task_path(task.id) %>
        <%= link_to "編輯", edit_task_path(task.id) %>
        <%= link_to "刪除", task_path(task.id), method: "delete",
            data: {confirm: "Delete Task #{task.name}, Are you sure ? "} %>
      </td>

    </tr>
    <% end %>
  </tbody>
</table>

<%= link_to "新增待辦事項", new_task_path %> <br />
<%= notice %>

<!-- 已完成待辦清單 -->
<table class="table table-striped table-condensed">
  <thead>
    <tr>
      <td>已完成待辦事項</td>
      <td>截止日期</td>
      <td>說明</td>
      <td>操作</td>
    </tr>
  </thead>
  <tbody>

    <% @done_tasks.each do |task| %>
    <tr>
      <td><%= task.name %></td>
      <td><%= task.deadline %></td>
      <td><%= task.description %></td>

      <td>
        <%= link_to "檢視", task_path(task.id) %>
        <%= link_to "編輯", edit_task_path(task.id) %>
        <%= link_to "刪除", task_path(task.id), method: "delete",
            data: {confirm: "Delete Task #{task.name}, Are you sure ? "} %>
      </td>

    </tr>
    <% end %>
  </tbody>
</table>

<!-- 已過期待辦清單 -->
<table class="table table-striped table-condensed">
  <thead>
    <tr>
      <td>已過期待辦事項</td>
      <td>截止日期</td>
      <td>說明</td>
      <td>操作</td>
    </tr>
  </thead>
  <tbody>

    <% @expired_tasks.each do |task| %>
    <tr>
      <td><%= task.name %></td>
      <td><%= task.deadline %></td>
      <td><%= task.description %></td>

      <td>
        <%= link_to "檢視", task_path(task.id) %>
        <%= link_to "編輯", edit_task_path(task.id) %>
      </td>
    </tr>
    <% end %>
  </tbody>
</table>
```

首先會遇到的問題是 `<%= link_to "完成", done_task_path(task.id), method: "done" %>`，done method (完成按鈕)執行時會遇到：

```log
Routing Error
No route matches [POST] "/tasks/1"
```

看一下 `rails routes`：
```log
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

<%= link_to "完成", task_path(task.id), method: "done" %> 是我單純測試用的寫法，觀察了一下行爲，網址是 `tasks/1` 與我們希望的 `tasks/1/done` 不符，因此來試試改成 `done_task_path(task.id)`。

成功！

簡單的待辦事項 ToDoList App 就這樣完成啦。

```log
git add .
git commit
git push github todolist
```

---
### 待解決的問題
1. 首頁好醜，就算有套 bootstrap，還是沒有對齊
2. 新增頁面或是編輯頁面輸入的資料有誤時，render 似乎有問題。
3. 編輯待完成事項，截止日期留白之後，送出能通過，回首頁就消失了。
