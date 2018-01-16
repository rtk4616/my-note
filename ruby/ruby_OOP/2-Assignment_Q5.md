##### Q5 進階題？

不不不，別被唬住了

這題一看不用看提示就該知道重點：  
"Champion 的 attack，damage 的計算方式是：  
所有 Hero 的實例數量 * 50"

記得教材最後的 class method .count 嗎？  
搭配 `class variable` `self` `陣列的 .count`  
去算出從這個 (Hero) class 出去的實例有幾個

值得注意的是因爲 Champion 是繼承 Hero 的
所以數量也會增加到 class variable 裡

不熟就回頭去翻筆記吧

有練習過你應該還會有印象

我一樣直接複製Q1的來改

---

* 我自己發現待解達的問題是：   
本來想把我的 class variable `@@all_hero` 換成  instance variable  `@all_hero`   
想說讓 count 單純去算 純 Hero 的實例數量(以本題來說就會是兩個)  
且不包含 Champion 的

結果 會有 error ：  
```
in `initialize': undefined method `<<' for nil:NilClass (NoMethodError)
```

意思應該是 `<<` 前面的東西 @all_hero 是 nil 所以沒有 `<<` method

我在 class 外面換成全域變數 $test 就能用，好怪  
不清楚爲什麼不行  
不在 method 裡，初始化的 instance variable 會有問題？  
推論是跟創建實例 instance 時  
class method 裡的 code 執行順序有關係  

instance variable 的優先權可能沒這麼高

OK，經過測試，是我不夠熟悉 instance variable：  
如果在 method 外面 定義 instance variable  
會傳不進 method 裡  
這對我來說算是個重要發現 XD  

應該算基本知識，回頭去補充筆記XD

```rb
class Hero
  # 仿造教材 使用 class variable
  @all_hero = ["test"]

  attr_accessor :name, :hp

  def initialize(name, hp, ap)
    @name = name
    @hp = hp
    @ap = ap
    @alive = true

    puts "yooooooo"
    puts "@all_hero = #{@all_hero}" # 這個 @all_hero 印不出來

    @all_hero << self

    @damage = rand(@ap)
  end

  # ...
  # 後面的code省略
```

補上我這題的答案：
```rb
# 創建一個名爲 Orc [獸人] 的 Class
# (本題目是接著教材範例之後，每個題目都是獨立分開的)

# 新增一個 class ，名爲 Orc，這個 class 會繼承 Monster
# 要繼承先來去 copy 之前 在 2-4 練習打的 code 吧
# 如果你不熟，我建議整段重打

class Hero
  # 仿造教材 使用 class variable
  @all_hero = ["test"]

  attr_accessor :name, :hp

  def initialize(name, hp, ap)
    @name = name
    @hp = hp
    @ap = ap
    @alive = true

    puts "yooooooo"
    puts "@all_hero = #{@all_hero}"
    # @all_hero = []
    # 創建 instance 的時候把 instance 加入 class variable
    @all_hero << self

    @damage = rand(@ap)
  end

  # 定義一個 class method 計算 instance 數量
  def self.count
    return @@all_hero.count
  end

  def attack(enemy)
    enemy.hp = enemy.hp - @damage
    puts "Hero #{@name} 發動攻擊"
    puts "#{enemy.name} 受到 #{@damage} 點的傷害"
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
    puts "Hero #{@name} 被打倒了"
    @alive = false
  end
end

class Monster
  attr_accessor :name, :hp

  def initialize(name, hp, ap)
    @name = name
    @hp = hp
    @ap = ap
    @alive = true
    @damage = rand(@ap)
  end

  def attack(enemy)
    # damage = rand(@ap)  # 註解掉本來的設計
    # enemy.hp = enemy.hp - damage
    enemy.hp = enemy.hp - @damage
    puts "Monster #{@name} 發動攻擊"
    puts "#{enemy.name} 受到 #{@damage} 點的傷害"
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
    puts "Monster #{@name} 被打倒了"
    @alive = false
  end
end

# 新增一個 class ，名爲 Orc，這個 class 會繼承 Monster
class Orc < Monster
  # 需求：
  # 新增一個 attack method 給 Orc
  # Orc 的 attack 比 Monster 強
  # 計算 damage 的方式跟 HolyKnight 的一樣(攻擊力兩倍)

  # 也就是說我們只有要改 攻擊力 屬性，其他不變
  # 你要整段 attack method 重寫嗎？好像只能重寫？
  # 用 super.attack(enemy)的話 damage的值
  # 又被改回去了(會用 class Monster 的 damage)

  # 因此可能要將 damage 也設計成一個 instance variable 才行
  # 回頭將 Monster 的 damage 都改成 @damage
  # 並把宣告 @damage 的位置拉到 initialize method 去

  # 並能在初始化的時候指定攻擊數值
  # 利用繼承 以及 super 的特性，留下我們不改的
  # 只修改我們要的
  def initialize(name, hp, ap)
    super(name, hp, ap)
    # Q：沒在 self.initialize 裡的 @alive 要不要寫？
    # A：不用！！！會繼承過來
    # super(name, hp, ap) 會去呼叫 Monster 的 initialize method

    # 修改 @damage，大功告成！！
    # 利用 Ruby 後面會覆蓋前面的這個設定
    @damage = 2 * @ap
  end

  # 無腦改法：
  # def attack(enemy)
  #   damage = rand(@ap..2*@ap)
  #   enemy.hp = enemy.hp - damage
  #   puts "Monster #{@name} 發動攻擊"
  #   puts "#{enemy.name} 受到 #{damage} 點的傷害"
  #   puts "#{enemy.name} 剩下 #{enemy.hp} 點 HP"
  #   puts ""
  #
  #   if enemy.hp <= 0
  #     enemy.die
  #   end
  # end
end

# 創一個 class Champion
class Champion < Hero
  # 改寫 damage，這裏我仿造Q1的精簡改法
  # 只是當初是改在 initialize 這次我修在這裏
  def attack(enemy)
    # 把題目要求寫進來
    @damage = Hero.count * 50
    puts "#{@name} 獲得了英雄之力！攻擊力獲得加成！！"
    super(enemy)
  end
end

hero1 = Hero.new("Roger", 100, 30)
hero2 = Hero.new("Ben", 100, 30)

champion = Champion.new("Alex", 100, 30)

monster = Monster.new("Bigfoot", 100, 30)


while champion.is_alive? && monster.is_alive?
  monster.attack(champion)

  if champion.hp <= 0
    break
  end

  champion.attack(monster)
end

```
