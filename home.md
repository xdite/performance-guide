大致上可規類幾個方向：

1. [[Rails performance]]
2. [[Front-end web performance]]
3. [[Caching & HTTP Reverse Proxy Caching]]
4. [[Asynchrony Processing (Message queue)]]
5. [[Partition Component using SOA]]
6. [[Distributed Filesystem / Database , Database Optimization, NoSQL]]

1. Rails (Application) performance

Rails 這個框架本身的 Performance 並不在這個議題討論之內。原因是 Rails 2 本身的 req/s 已經在 framework 界的表現算是不錯，還電掉一堆 PHP framework，而且對於 Rails 本體的表現，很多時候 Application 純開發者是沒有什麼作為能力的。Tuning 的原則建立在於認定 Rails 能夠有一定的效率表現，因此方向改於著重於避免設計出爛架構、寫出爛 code 造成效率低落，以較為良好的架構設計達到避免將壓力加諸於 Rails 身上的目標。

* 換掉 Ruby 版本

如果要提升 Rails framework 的速度，換掉 Ruby VM 是最快的。但如果還是希望使用純 C 寫的 Ruby ( gem compatible 問題），可考慮使用 Ruby Enterprise Edition (REE)。光換成 REE，記憶體用量馬上就少上 1/3，而速度也快上 1/3。

關於 REE 能夠做到這件事的原理，已經寫在 FAQ 裡，而他們在 2009 年底也在 Google Tech Talk 上給了一場相關的 talk。

[關鍵字: Ruby VM ]

* 抓出 slow action / slow ruby code，並針對這些部分改善

[ 可安裝 NewRelic RPM 抓出內部的問題。 ]

很多時候，Rails Application 的緩慢，並不是 Rails 這個 framework 產生的問題。而是開發者 *完全* 不熟 Rails API 或者是 Ruby 這個語言 本身，或者是濫用 Rails 的開發方便度寫出爛架構所造成的問題。常見的情形有以下幾種狀況：

- 不熟 Ruby。可以用 Ruby 本身提供的 method 一行就解決的事情，自己寫了迴圈處理，造成了 object 大量的被 clone 出來浪費記憶體。

解法：閱讀 Writing Efficient Ruby Code，以及閱讀 The Well-Grounded Rubyist（ Ruby for Rails 的新版）、Refactoring: Ruby Edition

- 不熟 Rails API。比如說：不熟 ActiveRecord，導致一個簡單 action，就產生了極大量的 query。或者是不懂做 counter cache，造成了 database 極大的壓力；大量使用 render :partial，卻不知道 render :partial 是極其昂貴的，應該把常用的 partial cache 住。

解法：閱讀 Advanced ActiveRecord 、Advanced Active Record Techniques: Best Practice Refactoring、That’s Not a Memory Leak, It’s Bloat、Rails Code Review、Railsrescue Handbook

- plugin 的錯。plugin 作者並不是全知全能，他們開發 plugin 只是為了順手解決自己手邊遇到的問題。你安裝的 plugin 若是熱門 plugin，效能上應該可能沒什麼問題，因為大多已經在許多人的 production 上千錘百鍊過，有問題也幾乎 patch 掉了。但若是比較冷門或比較新的，可能這個 plugin 就是造成你 performance 低落的元兇。

- 特殊 action 造成的效能低落。在 Rails Application 上，有一個常見功能是開發者的痛，就是上傳檔案的問題。上傳檔案這個行為，常常會造成 block 住整個站的現象。而 Merb 這個 Framework，當初就是設計來解決 Upload File 的問題的。很痛的還有寄信的這個功能，Rails 的 AR Mailer 真的頗廢 :/ ，它原始的設計初衷是要讓開發者在 Framework 輕鬆寫出寄信功能之用的，不過總會有天兵開發者會寫出在 action 裡一次寄一千封這種會死人的事….

解法：將效率低落的 action，諸如 upload 功能。利用 Metal 的機制，bypass 到其他 framework 或者是直接用 Rack middleware 做掉。如果你的 Web Server 最前面是 Nginx 的話，有人寫了一個十分有用的 upload module，解決了這個問題。至於寄信或者需要耗費大量資源的 action（如擷圖、縮圖、縮影片），應該用 queue + worker 加上 3rd party service 做掉。比如說寄信的方式，我就會推薦用 Delayed Job + Madmimi gem 解決。網站截圖就直接丟 bluega 處理。

- API 不應該直接使用 Rails 實作。API 通常多屬於邏輯簡單但 Request 數量又很大的行為，request 打進 Rails 本身就會造成一定量的負擔。這部分的實作部分也建議 bypass 到其他 framework 、直接用 Rack middleware 做掉，或乾脆切出去用其他語言/架構 implement。

至於更多 profiling memory usage 以及 measure code efficiency 的工具，ihower 將在 Ruby Tuesday #10 講到，這方面就交給他了，它的主題是 Rails Performance。而我本人也會在 Ruby Tuesday #10 講一場 Scaling Rails Site。