# Partition Component using SOA

也推薦閱讀同樣一份投影片，SOA 的內容從 P.91 開始。什麼是 SOA 呢？SOA 的全名是 Service Oriented Architectures.

<http://www.slideshare.net/ihower/distributed-ruby-and-rails>

## SOA 的定義
* SOA is a way to design complex applications by splitting out major components into individual services and communicating via APIs。
* a service is a vertical slice of functionality: database, application code and caching layer 。

## 為什麼談 Scaling 要扯上 SOA 呢？

1. 當一套系統日漸龐大時，一定會開始出現需要將各樣 Component 做水平拆分的動作。就比如說，為了 Scale DB 方面的表現，可能就會做 Database Sharding。但 DB Sharding 會造成新的問題，Application 必須配合 DB Sharding 做處理，也許當初用自己寫的 ORM 可以處理掉這個問題。但是系統更大了以後，需要以 其他語言/ Framework / 架構去擴充 Application，另外一個問題就產生了：原先的 ORM 可能不能直接使用，這時候要直接 access db ？還是用新語言重寫一套 ORM？但是，ORM 維護的不同步性會不會造成靈異現象呢？ 重新刻第二套第三套輪子是否有必要呢？

2. 我有一套 Bussiness Web Application，做的還不賴，想要拿來再外面架一套當分站，action 和 view 的架構大致上都不變，model 有一些關於 buisness 的部分 稍稍不同。這個不同說大不大，但說小也不小。比如說我開個賣電子書的網站好了，A 站收費是 a 模式，老闆突然說想開個 B 站，計價採 b 模式。也許為了趕上線，我們用了大量的 copy code 辦到了這件事。但是因為這網站實在太賺錢了，又要開 C 站….請問是要繼續 copy code 嗎？

遇上這兩種場景，我想 RD 應該都會想哭。如果真的繼續造輪子 / copy code 下去，擴充 + 維護難度會變得相當的高。

3. 假如今天外部廠商想要與我們合作，我們為了合作，必須開放部分資料讓對方可進行讀寫。能讓他直接讀寫我們的 DB 嗎？當然不行。給他們我們的 ORM Lib 嗎？也不行，ORM 可能也是商業機密。而且 ORM 可能也沒有關於 Security 和 權限方面的控管。那我要針對這次合作案，寫出一次性的 API 讓他使用嗎？好像也不對，有點太昂貴了….

4. 我今天想對 DB query 出來的 result 做 cache，到底是要在 cache 在哪裡呢？application 層？ORM 層？混合在以上場景裡，好像在哪裡做都不對…..

所以這就是為什麼做 Scaling 也要配合做出 SOA 的架構。

以 API 和 WebServices 的模式，降低不同系統介接的難度，提供一致性的存取介面，並且在這一層就可以把該做的 Cache 和Security 做上去。如果不做 SOA，很多系統根本想 Scale 都 Scale 不上去….

====
坊間關於 SOA 的書籍和資料很多，因為 SOA 已經是一門獨立的學問了。這裡我舉 SOA for Rails 的部分就好，這個議題，我推薦閱讀：

[Service Oriented Design With Ruby and Rails](http://www.informit.com/store/product.aspx?isbn=0321700104)
[Enterprise Rails](http://oreilly.com/catalog/9780596515201)
[Enterprise Recipes with Ruby and Rails](http://www.pragprog.com/titles/msenr/enterprise-recipes-with-ruby-and-rails)
