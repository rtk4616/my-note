### 理解與應用 super class 與 sub class 的概念
* 上一個小節，教的是繼承基本用法，以及 method 改寫  
  但是 method 改寫是把繼承過來的 method 把本來的 method 整個覆蓋掉  
  那有沒有辦法繼承原本的 method 又同時新增東西上去？
  * 可以！用 `super`

* class Apple 繼承 class Box  
  `class Apple < Box`
* super class 與 sub class 的關係：  
  `class Box` 是 `class Apple` 的 `super` class  
  `class Apple` 是 `class Box` 的 `sub` class

* 一樣先寫 super class Hero：

```rb
class Hero
  attr_accessor :name, :hp, :ap

  def initialize(name, hp, ap)
    @name = name
    @hp = hp
    @ap = ap
    @alive = true
  end

  def attack(enemy)
    puts "I'm a method in Hero class!!!"
    puts "#{@name} 對 #{enemy.name} 使用物理攻擊 造成 #{@ap} 點傷害！！"
  end
end
```

* 再來寫 sub class Mage：
1. 我們只想增加一個 attribute `mp`
2. 這邊示範直接讓 Mage 攻擊 Hero 5次  
   前3次使用魔法攻擊，後面2次使用物理攻擊，攻擊五次後停手  

PS. 改寫 attack method，讓 Mage 能用魔法攻擊  
   並在沒魔的時候用一般物理攻擊，留給各位練習(可以複製2-4的code來改就行)

```rb
class Mage < Hero
  # 1. 增加 attribute mp
  attr_accessor :name, :hp, :ap, :mp, :count

  def initialize(name, hp, ap, mp)
    # 本來的寫法，整個重寫，並新增一行給 mp：
    # @name = name
    # @hp = hp
    # @ap = ap
    # @mp = mp
    # @alive = true

    # 用關鍵字 super():括號後面就放你想要繼承的 attribute，並用逗號隔開
    # 意思就是去呼叫 Hero.initialize(name, hp, ap)
    # 記得不是 super.initialize(name, hp, ap)
    # 不要跟 self 用法搞混

    # Q：沒在 self.initialize 裡的 @alive 要不要寫？
    # A：不用！！！會繼承過來

    super(name, hp, ap)
    @mp = mp
    @count = 0
  end

  # 2. 改寫 attack method
  def attack(enemy)
    # 讓 Mage 使用魔法攻擊
    if @count < 3
      damege = @mp
      puts "#{@name} 使用魔法攻擊 對 #{enemy.name} 造成 #{damege} 點傷害"
    else
      # 跟改寫 initialize 一樣
      # 用 super(enemy) 去呼叫 Hero.attack(enemy)
      super(enemy)
    end
  end
end

# 這邊示範直接讓 Mage 攻擊 Hero 5次
# 前3次使用魔法攻擊，後面2次使用物理攻擊，攻擊五次後停手
hero = Hero.new("英雄", 100, 30)
mage = Mage.new("法師", 80, 10, 87)

while mage.count < 5
  mage.attack(hero)

  mage.count = mage.count + 1
end
```
