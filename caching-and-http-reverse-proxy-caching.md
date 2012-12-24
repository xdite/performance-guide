# Caching & HTTP Reverse Proxy Caching


不管是用什麼語言什麼架構做出來的網站，Scaling 很重要的一點的原則就是儘量 Cache Everything。讓每個 request 都去 hit db、用程式即時去產生頁面，對整體資源來說是相當昂貴的一件事。因此這兩個部分要儘量都用 Cache 做掉。

Caching 可粗分為 DB Caching 和 Webpage Caching。

## DB Caching

MySQL 本身就有 Query Cache 的機制，不過這裡談的是如何用 memcache cache 住 query result，減輕對 DB 的壓力。

基本的想法是，要做 Read-Through 和 Write-Through。Read-Through 的意思是：程式要拿 result set 必須要先去問 memcache 有沒有資料，有的話直接拿回去用，沒有的話才從 DB 直接拿資料，然後 cache 在 memcache 裡。而 Write-Thorugh 的作法是，當 object 被 新建、更新、刪除時，被 cache 住的部分也要同步被更新到。

<del>聽起來很簡單，但實作起來就 ….。幸好，我們有 cache money 這一套 plugin。它是一套 write-through and read-through caching library for ActiveRecord。要做這件事就相對比較無痛。</del>

目前 Rails 的 Cache 內建實作 write-through & ead-through

<del>還有一套 Rails Community 常用的 cache library 是 cache_fu ，作用主要是可以對 cache 加上 expire time 的處理。</del>

目前 Rails 的 Cache 內建實作 expire time

另外，Rails 有 API 可支援直接對 Cache ( memcache ）的讀寫操作。請閱讀[Scaling Rails – 第八章 Memcached](http://blog.xdite.net/?p=1029)

## Webpage Caching

Webpage Caching 又分三種：

* Page Caching（整頁）
* Action Caching
* Fragment Caching。


## Page Caching

Page Caching 談的就是把整頁的內容，cache 成 html / xml / json，塞進 Cachestore（是的，Rails 有 Cachestore 的設計，你可以在 config 裡指定 cache 是塞到 filestore、memory 還是memcahe 等等…）。

推薦閱讀：

* [Scaling Rails - 第二章 Page Caching](Scaling Rails - 第二章 Page Caching) 
* [Scaling Rails - 第五章 Advanced Page Caching](http://blog.xdite.net/?p=1020)

但是只要是 Cache 就會有 Cache Expiration 的問題。

如果在 controller 裡，一個一個針對資料變動的 action 做 page 的 expiration，程式很快的就會變髒。

比較好的方式是引進 Sweeper 的設計。Sweeper 是 Observer 的一種，可以同時 Observe controller 和 model。

推薦閱讀：

* [Scaling Rails - 第三章 Cache Expiration](http://blog.xdite.net/?p=1016)

## Action Caching

Action Caching 和 Page Caching 有什麼不一樣呢？ 

Page Caching 通常是用在同一頁面，但須要吐針對不同條件式需要不同結果的 action，比如說身份判別。當該頁針對 一般 user 和 anonymous 是吐出不同資訊時，就需要用到 Action Caching。

Action Caching 是配合 filter 去實做。在這個 case 中，如果沒登入就直接吐已經 cache 過的結果或將 client 重導到 Login 頁，通過驗證的再向 mongrel 要。

推薦閱讀：[Scaling Rails - 第六章 Action Caching ](http://blog.xdite.net/?p=1022)

那什麼時候會用到 Fragment Caching 呢？

當這個頁面很多區塊需要分別 Cache 起來，就拿一般有側邊欄設計的網站來說，很多側邊欄是用 partial 實作的。

同時，不少列表部分也是用 partial 搭配 collection 去做的。這時候就是使用 Fragment Caching 的使用時機。

延伸閱讀：[Scaling Rails - 第七章 Fragment Caching ](http://blog.xdite.net/?p=1027)

一樣的，只要是 Cache 就會遇上 Expiration 的問題。這實在相當棘手。<del>不過我在 Fragment Caching 這一塊，是改用了不同於 Scaling Rails 系列的作法，我用了 ihower 寫的 Handcache 和 Yehuda 的 Moneta 來做到對 cache 加上 expire time 的機制，使用 Moneta 也可以擴充使用更多不同的 cache backend，而不僅止於 memcache。</del> 

我已改用 Cells 實作

以上談的都是程式裡內部做的 Cache，接下來要談的是外部的 Cache。一般實務上的作法都是用使用 Load Balancer 加上 HTTP Reverse Proxy 實做。

窮人版的作法有幾種：

1. <del>使用 mongrel 做 web server, 利用 apache 做 rond-robbin 以及 Reverse Proxy。</del>
2. <del>使用 mongrel 做 web server, 利用 ngninx 做 load balancer（有個 module 叫 fair proxy） 以及 Reverse Proxy。</del>
3. 利用 Passenger (mod_rails）本身可以做到類似的架構，它有個 global queueing 的機制，蠻多設定可以調的，建議直接看 [Passenger 的 document](http://www.modrails.com/documentation/Users%20guide%20Apache.html#PassengerUseGlobalQueue)。
4. 架 [HAProxy](http://haproxy.1wt.eu/) 當 load balancer（[教學](http://plog.longwin.com.tw/my_note-unix/2009/03/23/haproxy-ha-load-balance-2009)），後面自己架 [Squid](http://www.squid-cache.org/) 或 [Varnish](http://varnish-cache.org/) 當 Reverse Proxy。

延伸閱讀：[Scaling Rails - 第十二章 Jesse Newland & Deployment](http://blog.xdite.net/?p=1054)

不過，其實如果如果你的量已經夠大了。每個月淨賺幾百萬，但是苦在於 RD 不夠或者是 SA 不夠強能堆出/維護日漸龐大的架構。應該做的事是買一台硬體的 Load Balancer (如: F5) 比較實際…。