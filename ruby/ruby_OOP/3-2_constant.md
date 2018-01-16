# 以常數限制並限制輸入參數

1. 什麼是常數？
2. 設定常數
3. 呼叫常數
4. 用常數限制變數的最大值
5. Recap (翻新)
___
## 什麼是常數？
Constant 不變的，持續的 XD 上英文課

* 常數就是一個設定好了就不變的參數

## 設定常數
* 規定：第一個英文字母要大寫
(class 的命名也是一種常數)

* 慣例：全部大寫

```rb
可以在 ruby 內看到，常數跟變數的顏色是不一樣的
CONSTANT = "test"
variable = "yo"
```
___
## 從物件內部呼叫常數：

```rb
class Monster

  MAX_HP = 100

  def show_max_hp
    puts MAX_HP
  end
end

monster = Monster.new
monster.show_max_hp # => 100
```

* 被 show_max_hp method 呼叫時，常數在 class Monster 內部
* 所以可以直接使用

## 從物件內部呼叫常數

> Rails 很常用！！！

* 可以直接呼叫 class 內的常數
* 要說明常數來源
* 用法：

```rb
常數來源::常數名稱
```

```rb
class Monster
  MAX_HP = 100
end

# 從物件外部呼叫常數
puts Monster::MAX_HP
```
___
## 限制變數的最大值
看例子：

```rb
class Monster

  def initialize(name, hp, ap)
    @name = name
    @hp = hp
    @ap = ap
    @alive = alive
  end
end

Monster.new("Bone", 100, 30)
```

* 我們希望限制變數 hp 跟 ap 的最大值：

```rb
class Monster
  MAX_HP = 100
  MAX_AP = 50

  def initialize(name, hp, ap)
    @name = name

    # 限制 hp 最大值：
    if hp > MAX_HP # 這邊條件判斷別用成 @hp 喔！
      @hp = MAX_HP
    else
      @hp = hp
    end

    # 限制 ap 最大值：
    if ap > MAX_AP
      @ap = MAX_AP
    else
      @ap = ap
    end

    @alive = alive
  end
end
```

後面還有兩個實際例子
留給各位參考
<!-- 花一小時 -->
