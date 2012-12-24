# Distributed Filesystem
基本上如果你的網站規模不到一定規模（這邊的定義是，用到幾十台機器），是不需要去研究 Distributed Filesystem / Distributed Database 的。不過我還是稍微聊一下它們的使用情境好了。

Distributed Filesystem 我們在做完網站後，可以使用 Load Balancer 把 request 分散到各台 web-front 機器上，但隨之就會產生一個問題，全站上的靜態檔案要怎樣讓這麼多台的 web-front 存取呢？基本想法當然是用 NFS，但是隨著網站成長，很快的 NFS 就不夠擔當這種重責大任了。於是另一種簡單的作法又出現了，用大量的 rsync + script 將檔案複製到多台 來*暫時* 解決 NFS 不夠用的情況。這個方法勉強夠用，但是會隨之產生架構維護上的難度缺乏整體的管理與監控…。而且在有大量小檔案需要同步，或者是檔案數量到達了百萬級、千萬級時，純用 rsync 非常的傷…

所以這時候才會用上 Distributed Filesystem 來解決這個問題。DFS 的作用是讓 Application 不用理會檔案資源實際會存在哪裡，往裡扔就對了，接下來的事情 DFS 會自己處理掉。Opensource 中比較出名的 Distributed Filesystem 當屬 Hadoop Distributed File System 。

HDFS 想做到的是 Google File System (GFS）能提供的：

1. 易於擴充的分散式檔案系統
2. 可運作於廉價的普通硬體上，又可以提供硬體錯誤容忍能力
3. 給大量的用戶提供總體性能較高的服務

解決以上情景會遇到的問題，同時又提供擴充性、移植性、資料一致性，而且支援相當大的資料規模（Perabytes）。Facebook 本身也是用 HDFS。

有關於這部分的閱讀資料，可參考國網中心的 Hadoop Distributed File System 這份投影片。

而 Distributed Database 這個主題 DK 講過也講的蠻清楚了，我就不重複寫一遍了。最重要的目標是 CAP theorem 的 Eventual Consistent（資料最終一致性）。比較知名的 Distributed Database 有 HBase 、Cassandra、Voldemort 等等….
