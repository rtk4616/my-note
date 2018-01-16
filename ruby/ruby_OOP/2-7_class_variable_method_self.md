本節重點：  
* 認識類別變數 class variable：`@@variable`  
* 認識類別方法 class method，認識關鍵字 `self`：`def self.method_name`  
* 在class用self呼叫當下的物件：`@@variable << self`

1. 類別變數 class variable
<!-- 龍哥沒特別講 class variable，有講 class method -->
* 在 `(super) class/sub class` 都能夠用的變數：

| 程式碼  |        類型       |     生存範圍|
| :-: | :-: | :-: |  
|`name`  |  local variable   |   method  |
|`@name` |  instance variable |  object  |
|`@@name`|  class variable    |  class  |

* class variable 用法：
* `@@class_variable`
* class variable 可以在 (super) class 以及 sub class 使用  
(注意教材先只教妳怎麼寫 class variable，又用到 self ，搞得很雜)
- 建議看我的筆記，單純很多：

* class variable 測試：
```rb
class CVtest
  @@test = [123]

  def initialize(name, number)
    @name = name
    @number = number

    # puts "#{@@test} "
  end

  def ohh
    puts "@@test is a class variable, this method is in CVtest."
    puts "@@test = #{@@test}"
  end
end

# puts CVtest.test
# 會產生 NoMethodError:
# private method `test' called for CVtest:Class
# 推測 class variable 很有機會是用 private method 實作的

Yaa = CVtest.new("Bang", 87)
Yaa.ohh

class CVtest2 < CVtest
  def qwe
    puts "@@test is a class variable, this method is in CVtest2."
    puts "@@test = #{@@test}"
  end
end

cvtest2 = CVtest2.new("Test2", 88)
cvtest2.qwe()
```

2. 類別方法 class method
* [回顧] 實例方法 instance method

```rb
# define instance method
class Hero
  def attack
    puts "This is an instance method !!!"
  end
end
hero = Hero.new()
# call instance method
hero.attack
```

* 類別方法 class method：`self.method_name`  
* 關鍵字`self`：指的就是 `object` 本身
* 不用創建 instance 就能直接呼叫 class method

```rb
# class method
class Yoo
  def self.test
    puts "This is a class method !!!"
  end
end

# call class method，不用創建 instance 就能直接呼叫
Yoo.test
```

* 另一種 class method 寫法 by 龍哥

```rb
# 如果一個 class 有很多 class method 就可以這樣寫
class Ya
  class << self
    def another_test
      puts "This is also a class method test~~"
    end
  end
end

Ya.another_test
```

* 使用 class variable 來管理所有的 objects
* 使用 class method 來存取 class variable
* class variable 作爲 objects 共有的 Data
* class method 可以當成 function 輸入輸出使用

###### 接下來我們要來模仿並實作很常見的 class method .all 跟 .count
* 在 Rails 會看到

```rb
# 模仿&實作 class method .all 與 .count
class CM_all_count
  # 初始化 class variable
  @@class_variable = []

  attr_accessor :p1, :p2, :p3

  def initialize(p1, p2, p3)
    @p1 = p1
    @p2 = p2
    @p3 = p3
    @@class_variable << self
  end

  def self.all
    return @@class_variable
  end

  def self.count
    return @@class_variable.length
  end
end

instance1 = CM_all_count.new(123, "qwe", "第一個實例")
instance2 = CM_all_count.new(456, "ert", "第二個實例")
instance2 = CM_all_count.new(789, "asd", "第三個實例")

# 計算有幾個 instance
puts "all_instances have #{CM_all_count.count} instances."

# 使用 class method .all 回傳所有 instance
all_instances = CM_all_count.all

# 印出所有 instance 的訊息：
# 用 each 把 all_instances 存放的資料依序取出並用 puts 印出來
# 順便用 內建 method class 去確認 該 instance 是哪個 class
all_instances.each do |instance|
  puts "Class:#{instance.class},
  p1 = #{instance.p1},
  p2 = #{instance.p2},
  p3 = #{instance.p3}"
end
```
