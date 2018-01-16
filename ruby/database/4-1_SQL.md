### 認識 SQL 與 SQL 語法
* 什麼是 SQL？  
`Structured Query Language`
讀取與儲存資料庫資料的程式語言

#### SQL的用途：
1. 定義資料庫
2. 修改資料庫結構
3. 讀取資料庫的資料
4. 修改資料庫的資料
___

#### 查詢 Table 裡的資料

**Select**

#### SELECT 語法
* 最基本的查詢 Table 語法 `SELECT * FROM products`
* SELECT 和 FROM 是一組的

`SELECT *`：  
SELECT：選擇 attributes  

* 選擇所有的 attribute
\*(星號)：代表 所有 attributes
FROM：選擇 Table
選擇了 products Table

* 選擇單一的 attribute  
`SELECT name FROM products`：  
只選擇 name 這個 attribute

* 選擇多個 attributes 用 `,` 隔開  
`SELECT name, price FROM products`

* 加上選擇的條件 `WHERE` `條件`  
EX1:
`SELECT name, price FROM products WHERE price < 100`  
EX2:
`SELECT name, price FROM products WHERE name = 'IPhone X'`

* `WHERE` attibute `BETWEEN` A `AND` B 語法：  
設定查詢 price 介於 A 與 B 之間  
`WHERE name, price FROM products WHERE price BETWEEN 0 AND 60000`

* 使用 LIKE 尋找相關字眼：  
* 找出 name 裡，以 Macbook 爲開頭的資料：  
`SELECT name, price FROM products WHERE name LIKE 'Macbook%'`

* 找出 name 裡，以 Pro 爲結尾的 name  
`SELECT name, price FROM products WHERE name LIKE '%Pro'`

* 使用 `AND` 把兩種條件結合：  
`SELECT name, price FROM products WHERE price < 6000 AND name LIKE 'Macbook%'`
> AND 是而且的意思，兩邊的條件都要達成

* 使用 `OR` 把兩種條件結合，同上

* 查詢符合多個 name 的條件：  
`SELECT name, price FROM products WHERE name IN ('Macbook Pro', 'IPhone X')`

* `ORDER BY` A 排序，根據 A 進行排序：  
`SELECT name, price FROM products WHERE price < 60000 ORDER BY price`

* 字串處理，`Left(name, 1)`：從name欄位的左邊取出第一個字  
`SELECT name, Left(name, 1) FROM products`

<!-- 累計2hr45mins, 此章節花了 1hr10mins -->
