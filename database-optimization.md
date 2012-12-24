
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
