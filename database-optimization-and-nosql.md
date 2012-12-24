
# Database Optimization, NoSQL

## Database Optimization

這方面的主題能談的非常多。而且每個主題也都相當博大精深。

既然這系列的重點是 Reading Martrial，我就挑重點講好了。

首先是：[MySQL 的調校 (軟硬體、版本、設定)](http://blog.gslin.org/archives/2009/09/13/2088/)，這一段 DK 也已經專門整理過一篇[文章](http://blog.gslin.org/archives/2009/07/25/2065/)了。內容是有關於機器、硬碟的挑選，架構的設計。挑選適合 MySQL 使用的 FileSystem ，my.cnf 的調校。

而跟 MySQL 各方面都不是很熟，看原文書又覺得很痛苦的人。我推薦看對岸簡朝陽寫的 [MySQL 性能調優與架構設計](http://www.china-pub.com/195636) 這一本書。基本上如果想瞭解 MySQL 的各樣基礎知識甚至進階知識，這本書大部分都含括了，而且寫的相當深入淺出….

架構設計上的需求，可直接看這本書的第三篇 (Ch12-Ch18)，以及 [High Performance MySQL](http://oreilly.com/catalog/9780596003067) 這本書。追求極致的 Performance Optimizaion 可追 [MySQL Performance Blog](http://www.mysqlperformanceblog.com/) 這個 blog …

DB 永遠是 Web Application 的瓶頸。而使用 ActiveRecord 想對 query optimize 的幾個 tips 是：

1. 不要忘記打 index，這一點很常在寫 model code ，寫著寫嗨了就忘記了，忘記打 index 可能會造成 slow query，可用 [rails_indexes](http://github.com/eladmeidar/rails_indexes) 這個 plugin。
2. 只 SELECT 你要的資料，而非 SELECT *。這一點可以透過 scrooge 這個 plugin 辦到。
3. 避免產生 n+1 query。
4. 記得加 counter_cache。（ 3. 與 4. 都可用 [bullet](http://github.com/flyerhzm/bullet) 這個 plugin 抓出來 ）
5. 記得打開 my.cnf 裡面記錄 slow query log 的選項，然後善用 EXPLAIN 抓出真正的原因。
6. 儘量使用 find_in_batch，而不要使用迴圈跑 find(i)
7. 少用 join，多用幾次 select 做到相同的事。


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
