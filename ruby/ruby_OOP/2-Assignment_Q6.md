##### Q6 算是比較稍微雜一點
快速看過題目找重點：  
1. class method : `self.all` 教材也有示範過
2. 條件：三對三，`隨機` `輪流` 互相攻擊，直到某邊隊伍全滅

應該有FU吧，沒有 Leetcode 難想  

別被`程式碼提示誤導就是`

要求 Champion, Hero, Monster  
就拿 Q5 的 code 來改吧：  

最後會出現問題應該是邏輯上的處理：  
因爲死掉要從隊伍內移除，這件事可能要稍微想一下  
A 攻擊 B，考慮B有沒有死掉
如果B掛了，移除B隊伍 並進行下一輪

如果B沒掛，換
B 攻擊 A，考慮A有沒有死掉
如果A掛了，就移出隊伍

**所以隨機選擇 rand(不能用常數)，因爲隊伍數量會因爲HP歸零而減少成員**

**還有die method 需要多一個動作：把HP=0的成員，移出隊伍陣列**


我覺得應該能寫得再簡單漂亮一點  
不過就先這樣啦：  

```rb
class Hero
  # 仿造教材 使用 class variable
  @@all_hero = []

  attr_accessor :name, :hp

  def initialize(name, hp, ap)
    @name = name
    @hp = hp
    @ap = ap
    @alive = true

    # 創建 instance 的時候把 instance 加入 class variable
    @@all_hero << self

    @damage = rand(@ap)
  end

  # 定義一個 class method 計算 instance 數量
  def self.count
    return @@all_hero.count
  end

  def self.all
    return @@all_hero
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
    puts "Hero #{@name} 被打倒了！！！"
    @alive = false
    @@all_hero.delete(self)
  end
end

class Monster
  @@all_monster = []

  attr_accessor :name, :hp

  def initialize(name, hp, ap)
    @name = name
    @hp = hp
    @ap = ap
    @alive = true
    @damage = rand(@ap)

    @@all_monster << self
  end

  def self.count
    return @@all_monster.count
  end

  def self.all
    return @@all_monster
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
    @@all_monster.delete(self)
  end
end

# 新增一個 class ，名爲 Orc，這個 class 會繼承 Monster
class Orc < Monster
  def initialize(name, hp, ap)
    super(name, hp, ap)
    @damage = 2 * @ap
  end
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

def remove_member(target, team_number)
  if target.is_alive? ==  false
    target.delete_at(team_number)
  end
end

hero1 = Hero.new("Roger", 100, 30)
hero2 = Hero.new("Ben", 100, 30)

champion = Champion.new("Alex", 100, 30)

monster1 = Monster.new("Goblin", 100, 30)
monster2 = Monster.new("Troll", 100, 30)
monster3 = Monster.new("Gremlin", 100, 30)

# 把 instance 存入 變數
hero_team = Hero.all

monster_team = Monster.all

# 一直戰鬥直到某方隊伍全滅
while hero_team.count != 0 && monster_team.count != 0
  hero_team = Hero.all
  monster_team = Monster.all

  # 隨機選擇隊伍，記得不能用常數
  random_hero = hero_team[ rand(Hero.count) ]
  random_monster = monster_team[ rand(Monster.count) ]

  # 決定誰先攻擊, 0: hero, 1: monster
  attack_order =  rand(1)

  if attack_order == 0
    random_hero.attack(random_monster)

    if random_monster.is_alive? == true
      random_monster.attack(random_hero)
    end

  else
    random_monster.attack(random_hero)

    if !random_hero.is_alive? == true
      random_hero.attack(random_monster)
    end
  end

end
```
