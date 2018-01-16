# 以私有方法隱藏內部邏輯

> Rails 常用

1. 什麼是 `private method`
2. 設定 `private method`
3. 實作 `private method`
4. Recap
___
## 什麼是 private method

* 無法直接使用(呼叫)
* 必須透過一樣在 class 內的` public method ` 呼叫才能使用

## 爲什麼要限制 method 不讓物件直接取用？
因爲：
* 你的程式不會址有你在使用(有可能被誤用)
* 有些細節使用者不需要知道(有可能被盜用)

Ex:

```rb
account.suspend     # 作廢帳號
user.reset_password # 重設密碼
```

看實際 private method 例子：

```rb
class MyClass
  def my_public_method
    my_private_method
  end

  private # 這行之後的東西都沒辦法直接呼叫

  def my_private_method
    puts "I'm in the private method!!!"
  end

end

my_class = MyClass.new
my_class.my_public_method # OK

my_class.my_private_method # error
# private method `my_private_method' called for
# <MyClass:0x00007fac8196ddb0> (NoMethodError)
```
___

## 另一個設定 private method 的語法

```rb
class MyClass
  def my_public_method  # 透過 public method 使用 private method
    my_private_method
  end

  def my_private_method
    puts "使用私有方法"
  end

  private :my_private_method  # 設定要寫在 my_private_method 下面
end

example = MyClass.new
example.my_public method # => "使用私有方法"
```

* 將 public method 設定爲 private method
* 用 ":" 表示用 symbol 設定
* 必須在想設定的 method 下方註明：

`private :my_private_method`

___
## 實際修改：上一章的code

* 物件生死應該交給物件本身決定(die method)  
不該直接讓其他物件決定

這教材就設定得不錯

<!-- 不到 30 分鐘結束，Rails 看好幾次了...  -->
