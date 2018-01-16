`Q2` 又要回頭 copy 教材惹，沒關係我們先看下去  
...  
OK，本題一樣是基本的 class 繼承練習  
阿一樣記得不要被一些奇怪的單字混淆重點  
但是有點無聊，幸好我沒練習火球術  
可以發揮你的創意

~%！$#@，Q2 `程式碼提示` 也錯太大了吧  
都不知道怎麼吐槽了  

程式碼提示(正確版)：用 super(enemy) 串接 Monster 的 attack method  

```rb
def attack
  if mp >= 3
    # 請寫上執行 DarkMage 的 dark_force
  else
    # 執行原來 Monster 的 attack
    super(enemy)
  end
end
```

我的版本：

```rb
# 繼承 Monster，OK，來複製 class Monster
class Monster
  attr_accessor :name, :hp

  def initialize(name, hp, ap)
    @name = name
    @hp = hp
    @ap = ap
    @alive = true
  end

  def attack(enemy)
    damage = rand(@ap) #*10
    enemy.hp = enemy.hp - damage
    puts "#{@name} 發動攻擊"
    puts "#{enemy.name} 受到 #{damage} 點的傷害"
    puts "#{enemy.name} 剩下 #{enemy.hp} 點 HP"
    puts ""

    if enemy.hp <= 0
      enemy.die
    end
  end

  def is_alive?
    return @alive
  end

  def die
    puts "#{@name} 被打倒了"
    @alive = false
  end
end

class DarkMage < Monster
  # 因爲要新增 attribute，所以這行要寫
  attr_accessor :name, :hp, :ap, :mp

  # 新增 attribute mp
  def initialize(name, hp, ap, mp)
    super(name, hp, ap)
    @mp = mp
  end

  # 新增 method ，用 mp 攻擊，模仿一般物理攻擊寫法
  def dark_force(enemy)
    damage = @ap * 1.5
    @mp = @mp - 3
    enemy.hp = enemy.hp - damage
    puts "#{@name} 發動攻擊！"
    puts "#{@name} 對 #{enemy.name} 造成魔法傷害 #{damage} 點！"
    puts "#{enemy.name} 剩下 #{enemy.hp} 點生命值。"
    puts ""

    if enemy.hp <= 0
      enemy.die
    end
  end

  # 修改 attack method，有足夠魔力的時候使用魔法攻擊
  # 沒有的話，用物理攻擊
  def attack(enemy)
    if @mp >= 3
      # 這行應該會有問題，如何在 method 呼叫其他 method ？
      # 耶？成功了耶，就是用 self.method_name 這樣呼叫喔？
      # 重新看/整理筆記 2-6 印象好薄弱...沒寫練習有差 XD
      # 結果沒想到在 class 內部 這樣呼叫就行
      # 簡單說我把物理攻擊 super(enemy)跟 魔法攻擊 dark_force 整個分開了
      # 而不是單獨先針對 damage 傷害改變
      # 都可以，其實要看整體設計再去研究哪種寫法比較適合要求
      self.dark_force(enemy)
    else
      super(enemy)
    end
  end

end

dark_mage = DarkMage.new("Morgana", 100, 30, 8)

# 這裏我偷懶不寫 class Mage 啦，一樣的東西
mage = DarkMage.new("Merlin", 100, 20, 7)

# 戰鬥
while dark_mage.is_alive? && mage.is_alive?
  dark_mage.attack(mage)
  if mage.hp <= 0
    break
  end

  mage.attack(dark_mage)
end
```
