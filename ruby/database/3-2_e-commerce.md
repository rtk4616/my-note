## 本章節重點就是：
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

**這種硬要加 join table 的方式，在 3-1， Q5 也用得到，先生出來之後再補**

* 確定關係之後，後面就是例行公事了

<!-- 到此章節累計時間 1hr35mins, 本章節花了55mins -->
