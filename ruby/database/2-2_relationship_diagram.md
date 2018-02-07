## 繪製實體關聯圖 Entity Relationship Diagram
* 可以繪製出實體關聯圖，並把實體關聯圖轉換成資料表

* 我們很難在一開始就知道 app 所需的資料庫到底具有哪些資料表  
很容易在設計資料庫時遺漏掉重要的資料，或是忽略資料之間的關聯
* 畫ERD圖可以讓我們從無到有，逐漸看到一個資料庫的全貌  
因而能去確定資料之間的關係，讓工程師在寫CODE時，能對資料結構跟關聯有個全貌，確認有無遺漏。

<!-- 簡單發現圖可能可以用 Xmind 畫，可能還是用專用軟體好，章節下方有討論串 -->

### 實體關聯圖
Entity Relationship Diagram, ERD  
是一種資料庫設計圖的格式


### 繪製實體關聯圖

* 新增實體(Entity, Table name)，實體用方形，裡面寫上名稱
* 屬性(Attribute)用圓形，裡面寫上名稱
* 在 Entity 與 Attribute 之間畫上一條線
* 在 Primary Key 下畫上底線
* 重複上述步驟直到所有實體完成
* 如果兩個實體間有關係，在實體之間畫上一條線
* 觀察彼此的關係，在線上兩端加上 1 或是 M

### 從實體關聯圖製作 Table
* Table 名稱通常是複數，然後字母全部都是小寫
* 根據 M 所在的位置補上 Foreign Key

<!-- 時間累計 4hr20mins, 本章節耗時 35mins -->

---
## 繪製學生選課ERD

這個章節算是從無到有生出ERD

上個章節是直接有給定屬性

1. 繪製實體
2. 繪製實體的關係
3. 拆解多對多關係
4. 新增實體的屬性
5. 設定實體的主鍵
6. 整理成教簡潔的格式

[學生選課需求]：
* `學生`要選`課`
* `老師`要教`課`

學生-課程-老師
```rb
Student-Class-Teacher
```

### 學生和課程是什麼關係？
* 一個學生可以選多門課：

```rb
Student_1-M_Class
```

* 一門課可以被多個學生選：

```rb
Student_M-1_Class
```

#### 學生與課程是多對多的關係

### 老師和課程是什麼關係？
* 一個老師能教多門課：  
`Class-M_1-Teacher`

* 先假設一門課只有一個老師

#### 老師跟課程是一對多的關係

## 拆解多對多，加入一個 join table
* 把 `多對多` 拆解成兩個 `一對多`

* 在 學生與課程之間插入一個 join table `Enrollment (選課記錄)`

* 學生(一)對選課記錄(多)：
`Student-1_M-Enrollment`

* 選課記錄(多)對課程(一)：
`Enrollment-M_1-Class`

* 全部串起來之後：
`Student-1_M-Enrollment-M_1-Class-M_1-Teacher`

<!-- 本章節累計時間40mins-->

---
## 本章節重點：
* 拆解一個`多對多_1`後發現：
* 這個`多對多_1` = 多對一 + `多對多_2`
* 出現第二個 `多對多_2`

我覺得重點就是搞清楚實體(Table)之間的關係

* 電子商務的需求：
* 使用者可以購買商品
* 使用者可以透過分類瀏覽商品

### 從需求中辨識可能的實體
* 使用者可以購買商品
* 使用者可以透過分類瀏覽商品

#### 基本實體

* 一個 User 可以購買多個 Product  
* 一個 Product 也可以被很多 User 購買  

`User-M_M-Product`

* 一個 Category 下有多個 Product
* 一個 Product 只會屬於一個 Category

`Product-M_1-Category`

### 拆解 `User-M_M-Product`
* 加入 join table `Order`
* 一個 User 有多個 Order
* 一個 Order 屬於一個 User

`User-1_M-Order`

* 一個 Product 可以在很多個 Order 出現
* **但是** User 可以在一個 Order 裡購買多個 Product
* 第二個多對多出現了，這時候該怎麼做？

`Order-M_M-Product`

* 再加入一個 join table `OrderedProduct`
* 一個 Product 有很多 OrderedProduct
* 一個 OrderedProduct 源於一個 Product

`Order-1_M-OrderedProduct-M_1-Product`

<!-- **這種硬要加 join table 的方式，在 3-1， Q5 也用得到，先生出來之後再補** -->

* 確定關係之後，後面就是例行公事了

<!-- 到此章節累計時間 1hr35mins, 本章節花了55mins -->
