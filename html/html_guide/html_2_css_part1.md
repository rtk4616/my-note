```css
h1 {
  color: #ff6600
}
```

- h1:選擇器
- color:屬性
- #ff6600:屬性值

### CSS 宣告通常獨立放在 `style.css`，再從 html 引入
```html
<!DOCTYPE html>
<html>
  <head>
    <meta charset="utf-8">
    <title>CSS 宣告</title>
    <link rel="stylesheet" href="style.css">
  </head>
  <body>
    <p>CSS test</p>
  </body>
</html>
```

```css
p {
  color: black;
  font-size: 10pt;
}
```
- link：後設元素(meta element)，只能放在標頭裡，可以載入外部檔案。
- rel="stylesheet"：告訴 HTML文件 載入的是 樣式表，所以瀏覽器舊知到要用讀CSS的方式去處裡載入的內容。
- herf="style.css"：描述路徑，讓瀏覽器找的到檔案。

### CSS 整理與合併
```css
h1 {
  font-family: "Helvetica", "Arial", sans-serif;
  color: green;
}

h2 {
  font-family: "Helvetica", "Arial", sans-serif;
  color: blue;
}
```

可以整理成

```css
h1, h2 {
  font-family: "Helvetica", "Arial", sans-serif;
}

h1 {
  color: green;
}

h2 {
  color: blue;
}
```

### 容器的排列方式：inline v.s. block


## css
- font-family: "Helvetica", "Arial", sans-serif; 指定字形
- font-weight: 700; 設定文字粗細，預設爲 400;
- color: green; 顏色
- background-color: pink; 背景顏色
  - background-color: transparent;
- background-image: url(../images/profile.jpg)
- background-size: cover; 填滿整個元素。
- background-position: center; 讓背景水平與垂直置中對齊。
- background-repeat: no-repeat; 不重複出現。
- background-attachment: fixed; 創造出 `視差` (Parallax) 效果，就是背景(圖片)不跟著頁面內容垂直滾動。[示範連結](https://youtu.be/9DJhL1cB-Dk?t=7m52s)
- display: block; 元素排列方式
  - inline:
    - inline box 尺寸取決於內容，不隨意改變。
    - inline box 緊鄰前一個物件，不另起新行。
  - block:
    - block box 佔滿整個橫列，寬度與瀏覽器一樣。
    - block box 後面的元素也會自動在新的一行。
    - block box 高度與內容一樣。
    - block box 高度跟寬度都可以用 CSS 控制。
  - inline-block; 以 inline 方式排列，又保留 block 的特性，讓我們可以設定 padding 和 margin。
  - display: none; 讓個別 HTML 元素從網頁上消失。
  - table
  - table-cell
* 操作盒子的屬性：
- padding 內縮
  - padding: 50px; 四邊皆內縮 50px。
  - padding: [top] [right] [bottom] [left] (順序是順時鐘)
  - padding: 10px 15px 20px 30px
    - padding-top: 10px;
    - padding-bottom: 20px;
    - padding-left: 30px;
    - padding-right: 15px;
  - padding: 50px 20px; 垂直 50px, 水平 20px。
- border: [寬度] [形式] [顏色]; 邊框線條
- border: 1px solid #ff6600;
  - solid 實心線條
  - dotted 虛線
  - none 無線條
- margin 外擴
  - margin-bottom: 50px;
- form 表單
  - `<form action=""></form>`
  - `<input type="text" name="name" class="form-style" placeholder="input your name" required>`
    - type: input 元素類型。
    - name: input 元素名稱。
    - placeholder: 輸入欄位的文字提示。
    - required: 設定欄位爲必填。
    - value: 可以設定輸入欄位的預設值，或是按鈕的文字。
  <!-- 教材這邊竟然沒弄成信件寄出。 -->
  - `<textarea name="textarea" id="message" cols="30" rows="10" placeholder="write message" required></textarea>`
    - textarea: 輸入文字區域。
    - col: 設定寬度。
    - rows: 設定行數。
  - border: none; 取消原有的線框。
  - boder-bottom: 1px solid #333; 設定下方線框的樣式。記得要先取消才能設定。
  - outline: none; 取消點選輸入框後會產生的外框。

---
### 操作盒子的屬性
由內而外：
- 內容 content：被 HTML 標籤包圍的內容。
- 內縮 padding：內容到 border 之間的留白區域。
- 邊框 border：padding 與 margin 之間的線條，方便確認 padding 大小。
- 外擴 margin：border 以外的空間，也是兩個元素間的距離。

---
### Margin collapse 網頁元素高度崩壞
```css
test {
  margin-bottom: 50px;
  margin-top: 25px;
}
```

兩個 block 元素間的高度會是 50px，而不是 50+25px

要在中間加入一個高度不爲零的元素才能避免 `margin` 被吸收。

margin collapse：  
當沒有負數的時候，collapse 原則是取最大值。
當有負數時，會變成最大值和最小值相加。

---
### 定義盒子的尺寸
所有元素都有 `width` 和 `height` 兩種屬性。但是預設的情況下，width 和 height 只代表 `內容` 的尺寸。

這個時候容器真正的尺寸是 元素的長寬 + padding + border。

這個 CSS 預設的設定就是 `box-sizing: content-box;`。

而另一個就是 `box-sizing: border-box;`。

```CSS
* {
  box-sizing: border-box;
}
```

用 * 選取所有元素。

---
### 元素的對齊與置中
- 用 text-align 控制 inline 元素對齊。


```css
body {
  /* 靠左 */
  text-align: left;

  /* 靠右 */
  text-align: right;

  /* 置中 */
  text-align: center;
}
```

<!-- 原來這邊當初就教得很混亂 XD，靠左靠右也講一下，我三個放一起 atom html preview 還當掉，哦，助教也是用 atom 自動完成吧！ -->

---

- 使用 auto-margins 使 block 元素置中。
- float 使 block 元素 左右對齊。
- flexbox ：完全控制對齊？


```html
<!DOCTYPE html>
<html>

  <head>
    <meta charset="utf-8">
    <title>對齊練習</title>
    <style>
      div {
        background-color: gray;
        padding: 20px;
        text-align: center;

        width: 200px;
        box-sizing: border-box;
      }
    </style>
  </head>

  <body>
    <div>
      <p>置中練習</p>
    </div>
  </body>
</html>
```

整個 block 置中：

```html
<!DOCTYPE html>
<html>

  <head>
    <meta charset="utf-8">
    <title>對齊練習</title>
    <style>
      div {
        background-color: gray;
        padding: 20px;

        /* 文字 p元素 置中 */
        text-align: center;

        width: 200px;
        box-sizing: border-box;

        /* block 置中，水平方向爲 auto */
        margin: 0 auto;
      }
    </style>
  </head>

  <body>
    <div>
      <p>置中練習</p>
    </div>
  </body>

</html>
```

http://www.oxxostudio.tw/articles/201502/css-vertical-align-7methods.html

---
### 用 float 對 block 元素 排版

<!-- 不得不說有小動畫好多了...。 -->

```html
<!DOCTYPE html>
<html>
  <head>
    <meta charset="utf-8">
    <title>float 練習</title>
    <style media="screen">
      .left {
        background-color: green;
        height: 100px;
        width: 200px;
      }

      .right {
        background-color: #CCCCCC;
        height: 300px;
        width: 400px;
      }

      .bottom {
        background-color: yellow;
        height: 80px;
      }
    </style>
  </head>

  <body>
    <h1>float 練習</h1>
    <div class="left">
      LEFT
    </div>

    <div class="right">
      RIGHT
    </div>

    <div class="bottom">
      BOTTOM
    </div>
  </body>
</html>
```

<!-- 大哥不對啊，你 class 這時候還沒教好嗎？ -->

- 對 class="left" 元素，使用 float 排版，block 元素將不再佔滿一行。
  - float: none;
  - float: left;
  - float: right;

如果讓元素的 float 設定都在相同邊，就會有排隊的效果

---
### 用 clear 讓元素保持在原位置，無視其他 float 元素影響。
`clear: both;`

both 就是同時 clear float: left; 與 float: right; 的元素。

---
### 用 position 定位元素
定位模型：
- static：預設值，無設定。
- relative 相對定位：從 static 原點(左上方) 進行相對位移。  
  原位置仍然佔用空間。
- absolute：從上層某個已用 postition 座標的原點來算。
- fixed：已左上角爲原點移動，並且不會隨著瀏覽器卷軸移動。

postition ：
- 水平定位：left or right
- 垂直定位：top or bottom
- z-index：元素重疊時，依 z-index 決定圖層先後順序。

---
### 使用 pseudo-classes 僞類別做特效
- :visited - 點過的連結。
- :hover - 滑鼠移過的地方。
- :focus - 滑鼠所在的表單輸入框。
  - rails 也有用到(笑)。
- :first-child - 第一個子元素。
- :last-child - 最後一個子元素。

用法：  
```css
.button-group a:hover {

}
```

---
### 使用 pseudo-element (僞元素) 設定 clearfix
- clearfix：解決設定元素浮動之後跑版的問題
- ::first-line 選取第一行。
- ::first-letter 選取第一個字。
- ::before 選取元素的前面的東西。
- ::after 選取元素後面的東西。
- ::selection 選取文字反白後。

```css
.clearfix::after {
  contet: '';
  display: table;
  clear: both;
}
```

---
### html 元素 + class 選擇器
```css
h3.resume-subtitle {
  font-size: 20px;
}
```

對應到的 html ：
```html
<h3 class="resume-subtitle"> test </h3>
```
