### 重點是寫一個 class，並使用 initialize 去初始化物件，設定 Attributes

* initialize 與 new method 的關係

```rb
class Monster
  def initialize

  end
end
monster = Monster.new
# new method 對應到 method initialize !!!!!!!
# 這應該也是 ruby 懶吧(笑)
```

* 在 initialize method 內新增 name attribute

```rb
class Monster
  def initialize(name)    # 傳入第一個資料爲 name
    @name = name          # 用 name 設定 attribute @name  
  end                     # 不這樣設定的話變數 name 無法在 class 內傳遞/使用
end

monster = Monster.new("Bang") # 從外面創建實例並設定 @name
```

認識 `attr_accessor`(在Rails裡看過N次，但是我依然沒去理解的東西)：  
緣由：

```rb
class Monster
  # 從`class`外部，透過實例執行 `method` 讀取 `@name`
  # 呼叫 monster.name 會拿到 @name 的資料也就是 "Bone"
  def name
    @name
  end

  # 從物件外部把資料寫入 `@name`
  # 這裏的 name= 分開就會有錯歐，把 = 當字串看待，問號，驚歎號同理。
  # new_name 就是 `name=` method 的輸入參數
  def name=(new_name)
    @name = new_name
  end

  # 上面那段code是因爲要讓你從外部把資料寫入 @name
  # name= 是方法名稱
  # monster.name= ("Bone")
  # 就會把 "Bone" 寫入 @name
  # 而把括號省略看起來是：
  # monster.name= "Bone"
  # 會令 @name = "Bone"
end
```

因爲上面這樣子的需求實在太多了：
1. 創建實例後，要去撈物件的 attribute，就是透過物件裏的 method ，回傳 @name。
2. 透過實例更改 attribute ，一樣要透過 method 去設定。

所以 Ruby 知道大家很懶，把本來要"六行"的東西，改寫成"一行"：

```rb
class Monster

  attr_accessor :name  # 生成能存取該 attribute 的 method

  def initialize(name) # 初始化
    @name = name
  end

end
```
