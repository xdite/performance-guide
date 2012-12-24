
## NoSQL


對岸 JavaEye 的站長 Robbin 寫過一篇[「NoSQL數據庫探討之一 － 為什麼要用非關係數據庫？」](http://robbin.javaeye.com/blog/524977)。整理了為什麼世界上比較大型的 Web 2.0 Site 要捨棄 RDBMS 轉而開發/使用 NoSQL 的原因：

1. High performance – 對數據庫高並發讀寫的需求
2. Huge Storage – 對海量數據的高效率存儲和訪問的需求
3. High Scalability && High Availability- 對數據庫的高可擴展性和高可用性的需求

這些 production 網站，對 DBMS 特性的要求 ：

1.數據庫事務一致性需求
2.數據庫的寫實時性和讀實時性需求
3.對複雜的SQL查詢，特別是多表關聯查詢的需求

以及市面上滿足這些需求的各種 NoSQL （依照分類）以及簡單介紹，相當值得一看。

另外 High Scalability 也寫了一篇關於 NoSQL 的整理 [Paper: High Performance Scalable Data Stores](http://highscalability.com/blog/2010/2/25/paper-high-performance-scalable-data-stores.html)
