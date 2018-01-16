### Class 練習
重點整理：
* 爲英雄 Hero，怪獸 Monster 設計 `class`

* 讓英雄和怪獸戰鬥
  * 創建一個 hero instance 和 monster instance
  * Hero先攻擊，接着輪流攻擊彼此
  * 戰鬥`至`其中一方倒下爲止

* 用 `puts` 描述戰鬥過程和結果：

```rb
  puts "#{@name} 發動攻擊！"
  puts "#{enemy.name} 受到 #{damage} 點的傷害"
  puts "#{enemy.name} 剩下 #{enemy.hp} 點 HP"
  puts ""   # 空格區分段落
```

* 觀察上面的 log訊息 設計戰鬥過程：  
  確定誰是攻擊方`@name`，誰是受攻擊方：`敵人(enemy)`  
  攻擊：對受攻擊方造成`傷害(damage)`，受到攻擊的那一方`HP`會減少  

* 在 die method 加上描述：  
`puts "#{@name} 被打倒了"`

* 使用 `if` 來判斷戰鬥結果，用 `break` 中斷 `while` loop

class設計提示：  
`Attributes`:  
* name
* hp (health point 生命值)
* ap (attack point 攻擊值)
* alive (是否存活)

`Methods`:
* initialize
  * 放入上面的 attributes
* attack
* is_alive?
* die

## 透過上述資料開始練習 Coding, GO！  
- 如果有用到`rand`，請注意要想辦法驗證兩種可能結果  
  確保你的程式邏輯是對的  
- 並觀察戰鬥訊息有沒有出現不合理的地方：  
  Ex：某一方死亡但是仍在戰鬥  
---
- 沒頭緒或想不出來或卡關，可以先看參考答案(攻略)  
- 但是一定要作筆記`你應該要能夠理解攻略裡的每一個字`  
  (看過攻略你就會很有印象怎麼寫了)
- 然後隔一段時間不看攻略，再來挑戰練習coding  
- 確認自己每個細節都有注意到並真的學會  
