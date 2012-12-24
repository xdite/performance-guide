# Rails (Application) performance

Rails 這個框架本身的 Performance 並不在這個議題討論之內。原因是 Rails 3 本身的 req/s 已經在 framework 界的表現算是不錯。而且對於 Rails 本體的表現，很多時候 Application 純開發者是沒有什麼作為能力的。

Tuning 的原則建立在於認定 Rails 能夠有一定的效率表現，因此方向改於著重於避免設計出爛架構、寫出爛 code 造成效率低落，以較為良好的架構設計達到避免將壓力加諸於 Rails 身上的目標。

## 換掉 Ruby 版本

現在 Ruby 1.9.3 已有不錯的速度水準。

## 抓出 slow action / slow ruby code，並針對這些部分改善

### 解法：使用 NewRelic RPM 抓出內部的問題

很多時候，Rails Application 的緩慢，並不是 Rails 這個 framework 產生的問題。

而是開發者 *不熟 Rails API* 或者是 *不熟 Ruby* 語言本身，又或者是濫用 Rails 的開發方便度寫出爛架構所造成的問題。

常見的情形有以下幾種狀況：

#### 不熟 Ruby。可以用 Ruby 本身提供的 method 一行就解決的事情，自己寫了迴圈處理，造成了 object 大量的被 clone 出來浪費記憶體。

建議閱讀以下補充讀物：

* 閱讀 [Writing Efficient Ruby Code](http://ihower.tw/blog/archives/1691)
* 閱讀 [The Well-Grounded Rubyist](http://www.manning.com/black2/)
* [Refactoring: Ruby Edition](http://www.informit.com/store/product.aspx?isbn=0321604180)

#### gem 的錯。

gem 作者並不是全知全能，開發 gem 的初衷只是為了順手解決自己手邊遇到的問題。

你安裝的 gem 若是熱門 gem，效能上應該可能沒什麼問題，因為大多已經在許多人的 production 上千錘百鍊過，有問題也幾乎 patch 掉了。

但若是比較冷門或比較新的，可能 gem 就是造成 performance 低落的元兇。

#### 特殊 action 造成的效能低落。

在 Rails Application 上，有一個常見功能是開發者的痛，就是上傳檔案的問題。

上傳檔案這個行為，常常會造成 block 住整個站的現象。而 Merb 這個 Framework，當初就是設計來解決 Upload File 的問題的。

很痛的還有寄信的這個功能，Rails 的 AR Mailer ，它原始的設計初衷是要讓開發者在 Framework 輕鬆寫出寄信功能之用的，不過總會有開發者會寫出在 action 裡一次寄一千封這種會死人的事….


#### API 直接使用 Rails 實作。

API 通常多屬於邏輯簡單但 Request 數量又很大的行為，request 打進 Rails 本身就會造成一定量的負擔。這部分的實作部分也建議 bypass 到其他 framework 、直接用 Rack middleware 做掉，或乾脆切出去用其他語言/架構 implement。


