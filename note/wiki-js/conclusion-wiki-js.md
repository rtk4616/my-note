先讓我弄一下WIN10 RIME輸入法

OK，拖了好久，趕緊先幫 `wiki.js` 給個結論

Q：`wiki.js` 有什麼功能呢？
A：
* 美觀：用 node.js 架的網頁框架
  - 網站主題渲染(rending)
* 自動用 git 備份文章(wiki本體沒有備份)
* 提供使用者帳密登入服務，使用者資訊存入資料庫 mongoDB
* 有提供第三方服務使用者登入
* 網頁後臺管理

明顯的缺點：
1. 左邊欄位真的做得有點爛，能參考的樣本很多，但是目前還是很爛
2. 檔名跟資料夾名稱有限制，不能有底線跟大寫字元，不然預設不顯示(這部分我debug超久...，想說爲啥我的舊筆記都丟不進wiki.js裏)
3. 要架server，server 要連資料庫跟 git 不熟都要debug 一陣子

### 總結
試用過後覺得還是用gitbook就好啦(笑)，乖乖把不需要的部分都拿掉  
平時寫作平臺不管怎麼樣一定會用類似`dropbox`的服務  
不然太煩了  
這時候才發覺架server有架server的好處  

或許之後有空也可以試試 [MDwiki](http://dynalon.github.io/mdwiki/#!index.md)  

寫筆記新家的漫漫長路(笑)
