#### Ruby on Rails 前言
這篇是我這個新手本人的 Ruby on Rails 踩雷心得，建議碰 Ruby on Rails 的順序/里程碑，以及提一些建議的事前準備事項。

`備註`：學習沒有速成這件事(有疑問請洽"刻意練習")，如果你不擔心學習曲線異常陡峭，再繼續碰 Rails，Rails 不是給新手碰的，甚至 Ruby 本身可能就不適合當第一個程式語言，如果能重來，我會繼續走 python 或是 php 學習路線，這是我砸了不少錢買 Rails 線上課程以及花了三個月以上的血淚經驗，雖然說程式語言只是個工具，但是沒必要一開始就打BOSS打到棄坑對吧？先有基本等級跟裝備再去挑戰不遲也會有更多樂趣。

剩下的碎碎唸請至 github 看原檔。

<!--
我純粹是因爲放不下拔下我砸的錢跟時間，不然從其他真新手身上觀察，也是棄坑居多(碰 Ruby 就棄了)。

`簡易學習心得`：你一個新手站在新手村的溫泉裡(能自動恢復HP的地方)跟 Rails 這隻BOSS單挑，所以假設你不會死(棄坑)或是能夠無限次數復活，那你要面對的可能就是永無止盡的折磨，身上穿的是新手裝，你唯一的武器是 google，初期漫無目的的找資料非常耗費時間。

`痛苦太多，收穫太少。` 這個濃縮心得就是出自一款初期是糞遊戲的 Diablo 3，跟我砸錢的課程很像。  

BOSS攻略？自己找，我看了三四份以上吧，花了好久試了很多才找到比較適合的，最適合你的攻略我相信還是你自己思考後重寫過的，學習過程千萬記得要寫筆記，再懶都要寫，我碰 Rails 的前半段都只在 google 跟敲 code(自我催眠)，時間晃過去之後，你問自己學了什麼？uh...Nothing，如果你有寫筆記，你至少會有筆記能夠快速回憶跟複習甚至重構...。 -->

取材參考：
- [PTT [心得] 給網站初學者的建議：用RoR非常辛苦 系列文章](https://www.ptt.cc/bbs/Soft_Job/M.1455884032.A.A44.html)
- [Ruby不是給新手用的 by Jokercatz](https://www.youtube.com/watch?v=6XUnYRB0Zpo&t=2690s)
- 爲你自己學 Ruby，實戰聖經
- [[胡立] 從零到一：程式導師實驗教學計畫](https://medium.com/@hulitw/mentor-program-b5f96ae1eed1)
- [Ruby on Rails 系列教材](https://medium.com/@weilihmen/ruby-on-rails-%E5%88%9D%E8%A9%A6%E5%95%BC%E8%81%B2-%E4%B8%8A-c30026fdec42)

___
#### 行前準備
- 先找範例操作過一次，接著學習寫筆記，推薦用 markdown 語法。
- 學習 Ruby 基礎：各類變數，印東西，條件判斷，邏輯運算子，迴圈，iteration，縮排，method，symbol，hash...等。
- 特別要先學習的是 `class` 與 `物件導向設計`，因爲 Rails 從頭到尾都在用，沒先理解很容易會卡關卡到崩潰。
- 再來是`資料庫`，基本建議就是Rails會用到的又能夠先獨立學習的都先學一學。
- git 基本指令。

(光是上面那些部分就要花很多時間，因爲我初期沒有寫筆記，造成記憶非常混亂無法整理，白白浪費了更多時間，因此我把寫筆記放在第一項。)

> 還有寫程式練習絕對不會用選擇題練習，切記，如果你的課程裏有大量的選擇題練習...，你可能要特別小心了，把那些時間拿去寫筆記吧！

___
#### Rails 學習藍圖
- 確實瞭解 MVC 架構
  - 留意編寫 MVC 和 Route 的順序(總之 View 不會是第一個)
    - MCVR
    - RCMV
    - MRCV
    - business logic, controller logic
  - Controller
    - CRUD Controller Actions
  - Model
    - Migration
    - Schema
    - ActiveRecord
  - View
    - layout
- 瞭解 Route
  - 手動設定
  - REST 與 RESTful
- Rails 檔案結構
