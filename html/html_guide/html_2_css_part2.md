# CSS 選擇器
類型：  
- type
  - input[type="submit"] {}
- class 類別
  - .class {}
- descendant 繼承
  - h1 p {}
- psuedo-class
  - a:hover {}
- ID 獨一ID
  - #id {}

## class selector 類別選擇器
基礎用法：  
<p class="light"> 加上 class 屬性的段落元素 </p>  

.light {

}

<div class="button"> 來自 div 的按鈕 </div>

.button {

}

---
### 疊加 class selector 效果
<span class=btn test"> 來自 span 的按鈕 </span>

.btn {}

.test {}

下面(後面的CSS設定會蓋過前面的)

---
### ID selector
多用於 區隔獨一無二的元件

#### 基礎用法
```html
<div id="nav">
  <a class="nav-item">選項A</a>
  <a class="nav-item">選項B</a>
  <a class="nav-item">選項C</a>
</div>
```

```css
#nav {

}
```

#### 利用 id selector 獨一性，讓超連結連到 id selector 元素

這在 markdown 好像挺常用的，我還蠻喜歡這個功能的

能連到某網頁的指定段落。

```html
<div id="contact-me">
  聯絡我
</div>

<!-- 同一張網頁寫法 -->
<a href="#contact-me">聯絡我</a>

<!-- 跨網頁檔案寫法 -->
<a href="/index.html#contact-me">聯絡我</a>
```

---
### 繼承選擇器
```css
.dark-bg p {
  color: white;
}
```

選擇在 dark-bg class 下的 p 標籤，將顏色指定爲 白色。

---
### 基礎樣式設定 reset
```css
h1, h2, h3, h4, h5, h6, p {
  margin: 0px;
  padding: 0px;
}
```
