什麼？你說寫作業也要寫筆記？  
對，學任何東西都要寫筆記  
其實是因爲我懶懶病發作，突然不想寫作業，但是我知道不練習肯定不行...  
不過難得終於不用想題目了  
就做吧 ：P

* 做完Q1後的心得：獲益良多！但是要動腦！！！

Q1：需要用到之前練習的 code，OK 題目難度還行  
你看完題目後就應該要有點知道怎麼做的感覺了  
終於有像樣的題目了  
寫程式就是把你心裏的 `虛擬程式碼(pseudo code)` 打成 `Ruby 程式碼`  

附上我的兩個解答：
* 我的想法都打在 code 的註解裡了
* 主要就是練習掌握 Ruby 後面的 code 會覆蓋前面的這個特性
1. 無腦覆蓋改 Orc attack method
2. 利用 instance variable (`@damage`) 跟 `super` 做小幅度的更改  
用這個改法更能體會到繼承的厲害之處

```rb
# 創建一個名爲 Orc [獸人] 的 Class
# (本題目是接著教材範例之後，每個題目都是獨立分開的)

# 新增一個 class ，名爲 Orc，這個 class 會繼承 Monster
# 要繼承先來去 copy 之前 在 2-4 練習打的 code 吧
# 如果你不熟，我建議整段重打

class Hero
  attr_accessor :name, :hp

  def initialize(name, hp, ap)
    @name = name
    @hp = hp
    @ap = ap
    @alive = true
  end

  def attack(enemy)
    damage = rand(@ap)
    enemy.hp = enemy.hp - damage
    puts "Hero #{@name} 發動攻擊"
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
    # puts "#{enemy.name} 受到 #{damage} 點的傷害"
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
    # A：不用！！！super(name, hp, ap)就會繼承過來
    # 如果沒用 super 就要寫，因爲你已經覆寫 initialize method 了！

    # super(name, hp, ap) 會去呼叫 Monster 的 initialize method

    # 修改 @damage，大功告成！！
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

# 題目要求
orc = Orc.new("Bigfoot", 100, 30)
hero = Hero.new("Tim", 100, 30)

while orc.is_alive? && hero.is_alive?
  orc.attack(hero)

  if hero.hp <= 0
    break
  end

  hero.attack(orc)
end

```
