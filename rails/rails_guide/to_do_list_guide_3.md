# Rails App ToDoList Part2, Controller
## 前言
編輯各個頁面的額外需求(method)，我是拿上一個 Rails App 練習來改應該挺快的。

```rb
def index
  today = Time.now.strftime("%m-%d-%Y")

  @undone_tasks = Task.where.(done: false).where("deadline>?", Date.yesterday).order(:deadline)

  @done_tasks = Task.where(done: true)

  @expired_tasks = Task.where(done: false).where("deadline<?", Date.today).order(:deadline)
end
```

這裡我們一行一行說明：
1. today = Time.now.strftime("%m-%d-%Y")  
這行的用意就是去找出今天的日期，而後面的 `strftime` [官方介紹](https://ruby-doc.org/core-2.1.5/Time.html#method-i-strftime)。  
strftime( string ) → string click to toggle source  
Formats time according to the directives in the given format string.  
或是在 irb 裏面查詢測試：
```bash
irb(main):005:0> irb
irb#1(main):001:0> Time.now
=> 2018-02-01 22:42:37 +0800
irb#1(main):002:0> --help

Enter the method name you want to look up.
You can use tab to autocomplete.
Enter a blank line to exit.

>> strftime
```
2. @undone_tasks = Task.where.(done: false).where("deadline>?", Date.yesterday).order(:deadline)  
@undone_tasks：實例變數，未完成的任務。  
Task.where.(done: false)：從 Task 資料表找出還沒完成的任務。  
.where("deadline>?", Date.yesterday)：又接了一個 where 是而且的意思，而且 deadline(截止日期) > 昨天，要理解 `今天 > 昨天`，因爲今天比昨天多過了一天，而截止日期 > 昨天 的意思就是 `還沒截止(過期)`。  
.order(:deadline)：資料用截止日期的順序來排序。  
把條件集合在一起：在 Task 裡挑出 `尚未完成` 而且 `還沒過期` 的資料並用截止日期排列。
3. @done_tasks = Task.where(done: true)：在 Task 裡找出已完成任務。
4. @expired_tasks = Task.where.(done: false).where("deadline<?",Date.today)：找出尚未完成而且過期的任務。  
截止日期 < 今天 的意思就是今天過得比截止日期還要久，也就是已經過了截止日期。
