```html
<!DOCTYPE html>
<html>
  <head>
    <meta charset="utf-8">
    <title>標題</title>
  </head>
  <body>
    <h1>我要學會 html！</h1>
  </body>
</html>
```

- 標題：h1, h2, h3, h4, h5, h6
- 文字段落：p
- 清單：ul, ol, li
  - 沒有順序的清單(unordered list)：ul 定義範圍，li 標示項目
  - 有數字順序的清單(ordered list)： ol 定義範圍， li 標示項目
  - <ul class="list-unstyled"> 讓項目符號消失。
- 強調語氣：em, strong
  - em 斜體
  - strong 粗體
- 換行：br
  - <br />
- 水平線：hr
  - <hr />
- 超連結：a
  - `<a href="https://tw.alphacamp.co" target="_blank"> 超連結在新視窗開啓網頁</a>`
  - `<a href="https://tw.alphacamp.co" target="_self"> 超連結在目前視窗開啓網頁</a>`
- 圖片：img
  - `<img src="images/logo.png" alt="logo"`,
    - img 沒有結尾標籤
    - src 插入圖片的位置
    - alt 沒有圖片時的替代文字
- 區域：div, span
- 表格：table, th, tr, td
  - th: table header (欄位名稱):此 標 記 中 的 文 字 內 定 為 向 中 (center) 對 齊 且 加 粗 (bold) 的 屬 性 ， 您 可 自 行 更 修
  - tr: table row
  - td: table data
- 表單：form, label, input
- 容器(box)：div, span

http://web.thu.edu.tw/hzed/www/tag.htm

<!-- 沒想到前100名還真的有回頭看。。。是因為進度到了? -->

常用小圖示套件：
- font awesome 套件：提供製作成字型的圖示，不過據說網頁會變卡。
- Glyphicons 也很有名。

假圖片網站：
- Placeholder.com
- Lorempixel

---
### bootstrap grid system 網格系統
bootstrap 用 12 等分的做法。

所以數字就代表那個元素會佔幾等分。

而爲了不同裝置的螢幕尺寸而有不同的代號能個別做設定：
- lg, >=1200px, 大螢幕
- md, >=992px, 小螢幕
- sm, >=768px, 平板直向
- xs, all, 手機直向

表格也能用這個控制 在 thead 的欄位設定即可

rails :
simple_format 可以將一些特殊符號轉為html，例如\n換行符號轉為html的<br>
