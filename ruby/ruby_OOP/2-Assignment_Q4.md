#### Q4

略微不同的Q4  

讓 attack 付出代價(體力)  

會用到 Hero 跟 Monster class  
回頭複製Q1來改寫：  

跟 Q3 一樣 一下子就寫完了

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

    # 增加 stamina points attribute
    @sp = 3
  end

  # 攻擊體力消耗 method
  def use_stamina
    @sp = @sp - 1
  end

  # 改寫 attack method
  def attack(enemy)
    # 改寫攻擊條件
    if @sp > 0
      self.use_stamina
      damage = rand(@ap)
      enemy.hp = enemy.hp - damage
      puts "#{@name} 發動攻擊"
      puts "#{enemy.name} 受到 #{damage} 點的傷害"
      puts "#{enemy.name} 剩下 #{enemy.hp} 點 HP"
      puts ""

    else # 除了 @sp > 0 的情況都會跑到這來，也就是 @sp <= 0 時
      puts "#{@name} 體力不足，無法攻擊，休息一回合，恢復 2 sp"
      puts ""
      @sp = @sp + 2 # 這邊官方範例算是陷阱吧，不然就是寫錯了
    end

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

    # 增加 stamina points attribute
    @sp = 3
  end

  # 攻擊體力消耗 method
  def use_stamina
    @sp = @sp - 1
  end

  # 改寫 attack method
  def attack(enemy)
    # 改寫攻擊條件
    if @sp > 0
      self.use_stamina
      damage = rand(@ap)
      enemy.hp = enemy.hp - damage
      puts "#{@name} 發動攻擊"
      puts "#{enemy.name} 受到 #{damage} 點的傷害"
      puts "#{enemy.name} 剩下 #{enemy.hp} 點 HP"
      puts ""

    else # 除了 @sp > 0 的情況都會跑到這來，也就是 @sp <= 0 時
      puts "#{@name} 體力不足，無法攻擊，休息一回合，恢復 2 sp"
      @sp = @sp + 2 # 這邊官方範例算是陷阱吧，不然就是寫錯了
      puts ""
    end

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
