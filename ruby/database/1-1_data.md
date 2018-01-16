## 1-1 資料庫概念
* 什麼是資料？
* 什麼是資料形態？
* 什麼是資料表？
* 什麼是資料表的結構？
___
### 什麼是資料？
* 一筆資料由多個屬性(Attributes)組成
___
### 什麼是資料形態 Data Types？
* 每筆資料的屬性都有不同的資料形態：

舉例：一張照片的資料形態

```rb
標題(title) - 字串 string,
日期(date) - date
敘述(description) - text
```
___
### 什麼是資料表 `Table`？
* 資料表是一堆資料的集合，A set of data

* 常見的資料表：  
通訊錄：由多個聯絡人組成的資料表。
___
### 什麼是資料表的結構 Table Structure？
* Cell
* Record(Row)
* Field(Column)

### `Cell`
在資料表 `Table`裡，最小的資料單位叫 `Cell`

舉例：

|name|email|
|:-:|:-:|
|cell|cell|
|cell|cell|

### `Record` 記錄 (Row)
* `橫/水平`的資料叫 `Record `  
> 不建議記`行`或`列`，因爲英文把`行`，`列`都叫做 Row  
因此記`橫的`或是`水平的`資料叫 Row 比較好

* 一個 Row 代表同時資料具有多個屬性：  
舉例：同時具有 name, email 屬性

(markdown 要合併 table 似乎有點麻煩，先這樣表示)

|name . email|
|:--:|
|record(Row)|
|record(Row)|

### `Field` 欄位 (Column)
* `垂直`的資料叫 `Field`，中文叫`欄位`
* 一個 Field 代表該群資料所有同一個屬性
* `Field` 也叫做 `Column`, `Attribute`

(以下表格請把 field1 彼此合併, field2 同理)
|name|email|
|:-:|:-:|
|field1|field2|
|filed1|field2|

舉例：  
在 Table 裡加上 id Field：
|id|name|email|
|-|-|-|
|1|A|a@a|
|2|B|b@b|
|3|C|c@c|

* 所以 `Record` 與 `Field` 的交集就是 `Cell`

<!-- 本章節耗時 72mins, 比預測久，markdown 表格 摸了一段時間 -->
