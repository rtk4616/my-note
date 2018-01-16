# RailsFun 資料庫概論筆記
[影片連結](https://www.youtube.com/watch?v=bgDPEnhzGuc&index=19&list=PLJ6M-k9dQEQ3VsyOZQwjZ5GdjaLJH3eB_)

* 資料庫等於放資料的地方  
EX：RDBMS，NoSQL

* 有些資料庫只能放索引值(不算是資料庫)  
EX：索引/搜尋引擎(內建 index),  
Sphinx, Lucene/Solr

### 介紹 RDBMS  02:00
* Relational DataBase Management System

* SQL 出生前的世界，`混沌`

> 你有聽過純文字文件嗎？

* CSV

```rb
DB
Table
Row         SQL 結構
Column
Data
```

* SQL包裝很吃效能，NoSQL 才水漲船高

* 最大缺點 P Key Table F Key Table

* 所有資料 `<join>` 所有資料

* 建 index 來解決這些問題

* RDBMS 另一個缺點之一 RDBMS Load Balancing

* Only RDBMS？ 能解的東西有限，都要會
___
### NoSQL 11:00
* 開始前，建議先看 `CAP theorem`：
> 通常資料庫只能符合下列兩個優點，第三個沒辦法

- 一致性，Consistency：所有節點在同一時間具有相同的數據

- 可用性，Availability：保證每個請求不管成功或者失敗都有回應

- 分隔容忍，Partition Tolerance：系統中任意信息的遺失或失敗不會影響系統的繼續運作

* NoSQL 有很多流派，解決的問題都不同，大概分三類：
1. 執行速度：天下武功，唯快不破  
吃我的記憶體啦  
代表 SSDB，其他的還有 Memcache, Redis, Aerospike

* Mem`cache`：把 Key => Value 都丟到記憶體去
* 優點：超快
- 缺點：無持久性，無法搜尋，無資料結構
> Memcache 當快取用就好

* Redis：有資料結構，hash, list, set, sorted set
* 優點：只比 Memcache 慢，也是丟到記憶體，有結構化資料
- 缺點：持久性不高，產品模式不建議搜尋 Key

***重要資料還是透過硬體存取避免風險***

* SSDB：建議使用SSD，可以用 redis client

* Aerospike
* 優點：很快，持久性高，部分結構化資料
- 缺點：邪教，Linux only，有需要再測
___
2. 資料容量：海納百川，有容乃大  
蝦兵蟹將能打倒大鯨魚

* HBase：Java, Hadoop, 碰Java就會碰到這個

* MongoDB：JavaScript 生產線一條龍，全世界最大的JSON檔  
多人使用好用

* ElasticSearch：Java, 但沒那麼Java, 其實是搜尋/索引引擎，沒那麼好用  
資料處理比MySQL快

### 這一派特別要提的：`mapreduce()`
* 硬碟--->Map--->Shuffle--->Reduce--->Data

* 通則：
- Schema free 不代表沒有 Schema，要自己定義
- 處理資料要有一致性
- 時間成本VS硬體成本
- 機器數量偏多，要學會資料/資源分配技能
- BigData != BigGarbage
___

3. 特殊用途：無相神功，無法無天  
是誰住在深海的大鳳梨裡

* CouchDB
- RESTful API
- the View
- M to M sync

### 實際演練，直接看官方DOC即可開始玩  29:00
* Create DataBase
* Create data
* 編輯 mapreduce()

* 總結：沒有最佳解，只有最適合的解

## Open your mind

<!-- 影片36mins, 耗時約兩倍, 74mins -->
