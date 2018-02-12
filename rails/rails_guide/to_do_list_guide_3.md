# Rails App ToDoList Part3, Controller
## 前言

`rails g controller tasks`

### ToDoList Controller Part1, index method

```rb
def index
  today = Time.now.strftime("%m-%d-%Y")

  @undone_tasks = Task.where(done: false).where("deadline>?", Date.yesterday).order(:deadline)

  @done_tasks = Task.where(done: true)

  @expired_tasks = Task.where(done: false).where("deadline<?", Date.today).order(:deadline)
end
```

這裡我們一行一行說明：
1. today = Time.now.strftime("%m-%d-%Y")  
這行的用意就是去找出今天的日期，而後面的 `strftime` [官方介紹](https://ruby-doc.org/core-2.1.5/Time.html#method-i-strftime)。  
strftime( string ) → string.
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
2. @undone_tasks = Task.where(done: false).where("deadline>?", Date.yesterday).order(:deadline)  
@undone_tasks：實例變數，未完成的任務。  
Task.where(done: false)：從 Task 資料表找出還沒完成的任務。  
.where("deadline>?", Date.yesterday)：又接了一個 where 是而且的意思，而且 deadline(截止日期) > 昨天，要理解 `今天 > 昨天`，因爲今天比昨天多過了一天，而截止日期 > 昨天 的意思就是 `還沒截止(過期)`。  
.order(:deadline)：資料用截止日期的順序來排序。  
把條件集合在一起：在 Task 裡挑出 `尚未完成` 而且 `還沒過期` 的資料並用截止日期排列。
3. @done_tasks = Task.where(done: true)：在 Task 裡找出已完成任務。
4. @expired_tasks = Task.where(done: false).where("deadline<?",Date.today)：找出尚未完成而且過期的任務。  
截止日期 < 今天 的意思就是今天過得比截止日期還要久，也就是已經過了截止日期。

> 小題醒：where 後面記得不要多一個 `.`，也就是別寫成 `where.` ，會有很多意外狀況發生...。

相關文件：  
[where method](http://api.rubyonrails.org/v5.1/classes/ActiveRecord/QueryMethods.html#method-i-where)  
[ActiveRecord::QueryMethods::WhereChain < Object](http://api.rubyonrails.org/v5.1/classes/ActiveRecord/QueryMethods/WhereChain.html)

---
作者補充：剛剛的 where 語法，都是透過 Active Record 去查詢資料庫。  
Active Record 是個管理 Model 的單元，負責管理儲存在資料庫裡的物件，提供許多方法可以讓你和 Models 互動。
[連結](http://guides.rubyonrails.org/active_record_querying.html)

---
### ToDoList Controller Part2, done method
OK，接下來我們來完成 done method：
```rb
def done
  @task = Task.find(params[:id])
  @task.update_attribute(:done, true)
  redirect_to tasks_path
end
```

可以發現這寫法跟我們上一個 Rails app 的 update method 非常的像。  
這邊不一樣的地方在於我們之前是更新所有欄位，現在我們只更新一個欄位：  
> 請特別注意這邊的 update_attribute 是 `單數`  
update_attribute(name, value)  
Updates a single attribute and saves the record.  
This is especially useful for boolean flags on existing records.  

`@task.update_attribute(:done, true)`  
官方文件還特別註明，非常適合用來更新 true or false 欄位。

完成按鈕(done method)：找出那個要更新 :done 欄位的任務，將 :done 欄位更新爲 true，將頁面重新導向至首頁。

---
### ToDoList Controller Part3, 完成其他 method
你可以重新來或是複製前面的練習來改，我是選擇看著打。

```rb
class TasksController < ApplicationController
  def index
    today = Time.now.strftime("%Y-%m-%d")
    # puts "Today is #{today} !!!!"
    @undone_tasks = Task.where(done: false).where("deadline>?", Date.yesterday).order(:deadline)

    @done_tasks = Task.where(done: true)

    @expired_tasks = Task.where(done: false).where("deadline<?", Date.today).order(:deadline)
  end

  def new
    @task = Task.new
  end

  def create
    @task = Task.new(task_params)

    if @task.save
      redirect_to(tasks_path, notice: "建立待辦事項成功！")
    else
      render :new
    end
  end

  def done
    @task = Task.find(params[:id])
    @task.update_attribute(:done, true)
    redirect_to tasks_path
  end

  def show
    @task = Task.find_by(id: params[:id])
  end

  def edit
    @task = Task.find_by(id: params[:id])
  end

  def update
    @task = Task.find_by(id: params[:id])
    if @task.update_attributes(task_params)
      redirect_to task_path(@task.id)
    else
      render :edit
    end
  end

  def destroy
    @task = Task.find_by(id: params[:id])
    @task.destroy

    redirect_to tasks_path
  end

  private
  def task_params
    params.require(:task).permit(:name, :deadline, :description)
  end
end
```

controller 的部分就暫時這樣吧。
