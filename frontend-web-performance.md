# Front-end web performance

一般來說，在測量用網頁開一張網頁需要多少時間時，其實會發現瓶頸大多是落於 loading 頁面上所需要的 data，而非 Server 產生頁面的速度上。

開一張網頁，loading 頁面所需要的東西要花 3 秒，可是 loading 頁面本身只需要 0.2 秒，加起來需要 3.2 秒。

tune Rails Application tune 了老半天，才將 0.2 秒降低成 0.1 秒。

但是針對 Client Side 部分隨便 Tuning 一下，本來需要 3 秒可能馬上降到 1 秒不到。所以有時候先 Tune 這部分反而是比較合算的。

而使用 [YSlow](http://developer.yahoo.com/yslow/) 這個工具，基本上就能幫忙檢測出很多網站上的問題和給出實際的建議。

[Best Practices for Speeding Up Your Web Site](http://developer.yahoo.com/performance/rules.html) 提供了很多相當好的建議：

以下是我基於 YSlow ，能利用 Rails 本身或網路上已有的工具 / 技術給出的實作建議：

## Minimize HTTP Requests / Optimize CSS Sprites

過多的 http request 是 loading page 效率低落的主要原因之一，造成這種問題的原因多半出自於開啟該頁面需要下載的靜態檔案太多（css 和 js 太多支、css 裡用到的 images 太多張）。

### 解法：

在一般網站架構的情形，可用 YUI Compressor 做到打包多支 css / js 成一支檔案的目標，減少 http request，但是每次在 production 的現實操作中，deploy code 都要手動做一次，忘了做就會有災難發生，一般的解法可能是利用 vim 的 script 做到編輯完自動打包 。而在 rails 裡，helper 可以幫你自動做到打包的動作。

``` rhtml

<%= javascript_include_tag , "product", "cart" ,"chckecout" , :cache => "shop" %>
<%= stylesheet_link_tag :all , :cache => true %>

```

至於 CSS Sprites，是一種技巧，將原本很多張的小圖合併成一張圖，再利用 CSS 定位切割，用來解決 css 裡 images 太多張的問題。網路上搜尋 auto css sprite 就有一大堆相關的工具。不過這裡要特別講一個最近才新出的 Ruby Gem：Auto Sprite，可搭配 Rails 自動處理這件事 ..

## Use a Content Delivery Network / Split Components Across Domains

Serve Static File 對一些 httpd 是一件很痛的事。因此在實務上，通常會以拆不同 domain、不同 server 的技巧，將靜態檔案 Server 與 Application Server 拆開。把靜態檔案上 Reverse Proxy 或 CDN 處理。上 CDN 除了可以減輕對 httpd 的壓力，還可以更多其他明顯的好處：提供 delivery 檔案的速度品質（尤其是服務的對象是 global）、有的廠商會幫忙上 gzip（甚至多幫忙處理 IE6 下 gzip 的問題）等等…

但是切換 static file 的 server，如果在架構上面沒設計好，換 server 就要大幅改寫 application 裡的 code，是很傷開發成本的一件事。

### 解法：

這一點 Rails 很聰明的幫開發者想到了，只要在 config 裡設定

``` ruby

config.action_controller.asset_host = ‘http://asset.example.org"

```

靜態檔案的來源通通都會自動改成 asset.example.org。更進階的，browser 同時間只能對同一 domain 最多兩個 persistent connections，所以實務上還要將靜態檔案 server 拆成多個 domain，加速平行下載。這一點 Rails 也想到了，

``` ruby

config.action_controller.asset_host = ‘http://asset%d.example.org"

```

就可以同時分散到 asset0-3 去。

另外，上了 CDN 後，靜態檔案在 client-side 的 flush 又是一個問題。這一點 Rails 也是自動處理好了，一般 Rails Application 所產生的 html，靜態檔案檔名的部分通常會有一串數字 ?123456，這就是用來解決 browser cache 住靜態檔案的問題，利用後面 query string 的不同，讓 browser 以為跟原先之前 cache 住的檔案不同而重新下載。由 Rails 的 helper 自動加上，數字是最後修改時間的 unixtime。

## Add an Expires or a Cache-Control Header / Configure ETags

這是 Client Caching 技巧之一

`header["Cache-Control"] = "max-age=600"`，就是 Content 在 600 秒內都是 valid 的，600 秒內都不用重抓。

除非 broswer 送出 refresh 指令。要做到這件事：只要在 action 裡簡單的添加一行：

``` ruby

expires_in 10.minutes

``` ruby

至於如果最前面的 web server 是 apache 的話，還有一招是在 public/stylesheets 和 public/javascripts 下放置一個有以下內容的 .htaccess 。

`Header add Cache-Control "max-age=86400"

至於 etag 以及 last-modified，都已經整理在以前寫的 Scaling Rails – 第十章 Client-side Caching 的文章裡了。

## Minify JavaScript and CSS

在 Minimize HTTP Requests 這章裡，已經提過了 Rails 的 helper 內建了打包功能，但這個打包功能只有純打包沒有幫壓。於是有人開發了一個 plugin： Smurf 搭配 Rails helper 原有的機制，做到打包並壓縮這件事。

最後，關於 Rails Front-end web performance 的 Scaling 的閱讀材料，我推薦閱讀以下資料：

* Yehuda 的 [Making Rails Even Faster by Default](http://en.oreilly.com/velocityfall09/public/schedule/detail/11221)
* [Scaling Rails : Client-side Caching](http://railslab.newrelic.com/2009/02/25/episode-10-client-side-caching)
* [Scaling Rails : Advanced HTTP Caching](http://railslab.newrelic.com/2009/02/26/episode-11-advanced-http-caching)

至於一般 General 的 Front-end web performance scaling 我推薦的是：

* High Performance Web Page
* Steve Souders 的兩本書：[High Performance Web Sites](http://oreilly.com/catalog/9780596529307) 以及 [Even Faster Website](http://oreilly.com/catalog/9780596522308/)
* Steve Souders 老人家的 [blog](http://www.stevesouders.com/blog/)