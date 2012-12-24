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

- 不熟 Ruby。可以用 Ruby 本身提供的 method 一行就解決的事情，自己寫了迴圈處理，造成了 object 大量的被 clone 出來浪費記憶體。




