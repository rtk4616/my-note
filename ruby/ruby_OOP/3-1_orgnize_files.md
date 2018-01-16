**重新組織你的程式檔案**  
小標題：  

1. 爲什麼要組織你的檔案？
2. 分拆檔案
3. 載入檔案 - `require`, `require_relative`
4. 淺談物件的相依性
5. Recap

***

# 爲什麼要組織你的檔案？
1. 因爲你的程式碼會愈寫愈長
2. 你的專案會愈來愈大
3. 一旦有錯，可能要改很多檔案

***

# 分拆檔案  
`一個 class 獨立成一個 Ruby 檔案 (.rb)`  
> Rails 也是這樣做！！！

舉例：  
class Monster 的檔案名稱就叫做 monster.rb  
*全部小寫*

class HolyKnight 的檔案名稱則叫做 holy_knight.rb  
*兩個單字中間用 底線劃開*

***

# 載入檔案 `require` `require_relative` 介紹
## 這個章節要跟下一節一起看。。。
### 僅介紹 關鍵字 require 跟 require_relative 用法

假設我們今天要執行一個程式 叫 play  
程式檔案架構長這樣：

```rb
play.rb _ monster.rb (class Monster)
        _ hero.rb   (class Hero)
        _ holy_knight.rb (class HolyKnight)
        _ mage.rb    (class Mage)
```

執行 play.rb 前 要去載入他的那些 class files  
他們通常會被放在同一個資料夾裡  

## 如何讓 Ruby 載入檔案？ Part.1

```
require 跟 require_relative，最大的差別不是要不要加 "./"，而是基準目錄不同。
require 是從你執行的地方開始算。
require_relative 是從寫這行的檔案所在位置開始算。

所以你只要換目錄執行同樣的程式，require就會找不到檔案。
require_relative 因為是從同一個位置開始找所以不會被影響到。
```

1. 關鍵字： `require`
* 加上 "./" 說明要連結的檔案在同一個資料夾內
* 可以直接寫上檔案路徑(指絕對路徑？)
* 副檔名(.rb)不影響結果 `(強烈建議要寫，沒寫會超像資料夾)`

```rb
require "./hero"
require "./hero.rb"
```

2. 關鍵字：`require_relative`
* 該檔案的`相對位置`，也就是同一個資料夾內，無需加上 `"./"`
* 副檔名(.rb)一樣不影響結果 `(強烈建議要寫，沒寫會超像資料夾)`

```rb
require_relative "hero"
require_relative "hero.rb"
```

3. 實際的載入檔案寫法：
**(官方投影片P.16左邊有錯...，程式碼是對的)**

```rb
require "./monster.rb"
require "./hero.rb"
require_relative "holy_knight.rb"
require_relative "mage.rb"
```

hmm...這節就這樣就沒了？
請繼續往下一節前進，範例可以先無視

範例參考：  
你的 require 若非必要應該讓他單純點  
官方是爲了示範才用這麼多寫法  

```rb
require "./monster.rb"
require "./hero.rb"
require "./holy_knight.rb"
require "./mage.rb"

# 建立一個有不同職業 (包括英雄，神聖武士與魔法師)的團隊
hero = Hero.new("Robinhood", 100, 20)
holy_knight = HolyKnight.new("Howard", 100, 30)

mage = Mage.new("Merlin", 60, 20, 10)

# 印出英雄隊伍
team = Hero.all

puts "You have #{Hero.count} heros in your team:"

team.each do |hero|
  puts "#{hero.name} - Class:#{hero.class}, HP:#{hero.hp} "
end
```

***
## 如何讓 Ruby 載入檔案？ Part.2
### 淺談物件的相依性 `Dependency`

* subclass `依賴` superclass 的屬性和方法

```rb
class :
  Hero _ Mage
       _ HolyKnight

說明：
Mage 繼承 Hero
Mage 相依(依賴)於 Hero (Mage 對 Hero 有相依性)
Mage 需要 Hero 的程式碼才能執行

```

### ...投影片從根(superclass)講起比較合適

因此：
如果要使用 (class Mage) mage.rb，就需要載入(class Hero) hero.rb

範例：在 test.rb 創造 Mage 實例 (instance)

mage.rb 會長這樣 :
* 執行 mage.rb 時，require 會去檢查 hero.rb 載入沒  
(官方投影片有錯：class Mage `< Hero` 漏了繼承 Hero 了...)  

```rb
require "./hero.rb"

class Mage < Hero
  # attributes
  # methods
end
```

在 test.rb 創造 Mage 實例 (instance)  
* 單純使用 Mage 時，不必再 `require "/.hero.rb"`
test.rb 會長這樣 :

```rb
# require "./hero.rb"
require "./mage.rb"

mage = Mage.new("Peter", 100, 30, 10)

```

<!-- 時間記錄：3-1, 花了一小時 -->
