* 本節重點：inherent 繼承  
繼承：一個 class A 從某 class B 繼承，意思就是保留 class B 的所有設定，以 class B 爲基準去修改 class A。

### 1. inherent 繼承
* 繼承專屬符號： `<`

- 首先，我們先把一個 class B 寫出來：

```rb
class Box
  attr_accessor :name

  def initialize
    @name = name
  end

  def method
    puts "I'm a method in class Box."
  end
end
```

- 接著我們讓 `class Apple` 繼承 `class Box`：

```rb
class Apple < Box # 繼承 class Box 的 attributes 和 methods
  # 這樣子就繼承成功了

end

# 繼承後，你就可以在 Apple 產生出來的實例使用 Box 的 method 了
apple = Apple.new
apple.method
```

###2. `method overriding` 覆寫 method
- 如標題所述，如果 `class Apple` 想改 `method` 怎麼辦？  
  直接改它！！！他會把結果覆寫過去

* method overriding
* 後面的同名 method
* 覆蓋了前面的 method
* Ruby 會直接執行後面的 method

#### 請注意覆寫 method 是把整個 method 都重寫

```rb
class Apple < Box
  def method
    puts "I'm a method in class Apple !!!!!!"
  end
end
```
