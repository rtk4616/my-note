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
- color: green; 顏色
- background-color: pink; 背景顏色
- display: block; 元素排列方式
  - inline:
    - inline box 尺寸取決於內容，不隨意改變。
    - inline box 緊鄰前一個物件，不另起新行。
  - block:
    - block box 佔滿整個橫列，寬度與瀏覽器一樣。
    - block box 後面的元素也會自動在新的一行。
    - block box 高度與內容一樣。
    - block box 高度跟寬度都可以用 CSS 控制。
  - display: none; 讓個別 HTML 元素從網頁上消失。
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

### 定義盒子的屬性
