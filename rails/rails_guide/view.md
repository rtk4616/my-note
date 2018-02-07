### 常用指令

```
rails view -h
```
___
### 基礎 erb 語法

Erb =

```erb
<table>
  <thead>
    <tr>
      <td>姓名</td>
      <td>信箱</td>
      <td>性別</td>
      <td>年齡</td>
    </tr>
  </thead>
  <tbody>
    <% @users.each do |user| %>
    <tr>
      <td><%= user.name %></td>
      <td><%= user.mail %></td>
      <td><%= user.gender %></td>
      <td><%= user.age %></td>
    </tr>
    <% end %>
  </tbody>
</table>
```

介紹 僅有 table，沒有 html, head, body 標籤。

<% %> 與 <%= %> 的差異。

累積幾個範例？

erb code 要與 轉換後的 html code 一起看。

我記得有個不用全部在 html.erb file 編輯 rb 語法的方法。

___

link_to

controller action

\_path, \_url 差異

Helper
