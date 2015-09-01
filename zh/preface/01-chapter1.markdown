# 簡介

-    [背景](#toc_18682_17841_1)
-    [現狀](#toc_18682_17841_2)
-    [計劃](#toc_18682_17841_3)


<span id="toc_18682_17841_1"></span>
## 背景

早在 2007 年 11 月，為了系統地學習和總結 Shell 編程，作者專門制定了一個 Shell 編程範例的總結計劃，當時的計劃是：

> 這個系列將以面向“對象”（即我們操作的對象）來展開，並引入大量的實例，這樣有助於讓我們真正去學以致用，並在用的過程中提高興趣。所以這個系列將不會專門介紹 Shell 的語法， 而是假設讀者對 Shell 編程有了一定的基礎。
>
> 另外，該系列到最後可能會涵蓋：數值、邏輯值、字符串、文件、進程、文件系統等所有我們可以操作的“對象”，這個操作對象也將從低級到高級，進而上升到網絡層面，整個通過各種方式連接起來的計算機的集合。實際上這也未嘗不是在摸索 UNIX 的哲學，那"K.I.S.S"（Keep It Simple, Stupid）蘊藏的巨大能量。

> —— 摘自《蘭大開源社區 >> 腳本編程 >> Shell 編程範例》

2008 年 4 月底，整個系列大部分內容和框架基本完成，後來由於實習和工作原因，並沒有持續完善。不過相關章節卻獲得了較好的反響，很多熱心網友有大量評論和轉載，例如，在百度文庫轉載的一份《Shell編程範例之字符串操作》的訪問量已接近 3000。說明整個系列還是有比較大的閱讀群體。

<span id="toc_18682_17841_2"></span>
## 現狀

考慮到整個 Linux 世界的蓬勃發展，Shell 的使用環境越來越多，相關使用群體會不斷增加，所以最近已經將該系列重新整理，並以自由書籍的方式發佈，以便惠及更多的讀者。

整個系列已經用 [Markdown](http://www.tinylab.org/start-posting-with-markdown/) 重新組織，併發布到了 [泰曉科技|TinyLab.org](http://tinylab.org)，可以通過[TinyLab.org](http://tinylab.org)各文章右上角的 Print/PDF 插件直接下載所有章節的 PDF 版本。

整理到[TinyLab.org](http://tinylab.org)的索引篇是：[《Shell編程範例之索引篇》](http://www.tinylab.org/shell-programming-paradigm-series-index-review/)，其內容結構如下：

- [Shell編程範例之開篇](http://www.tinylab.org/shell-programming-paradigm-begins-with/) (更新時間：2007-07-21)
- [Shell編程範例之數值運算](http://www.tinylab.org/shell-numeric-calculation/) (更新時間：2007-11-9)
- [Shell編程範例之布爾運算](http://www.tinylab.org/shell-programming-paradigm-of-boolean-operations/) (更新時間：2007-10-30)
- [Shell編程範例之字符串操作](http://www.tinylab.org/shell-programming-paradigm-of-string-manipulation/) (更新時間：2007-11-21)
- [Shell編程範例之文件操作](http://www.tinylab.org/shell-programming-paradigms-of-file-operations/) (更新時間：2007-12-5)
- [Shell編程範例之文件系統操作](http://www.tinylab.org/shell-programming-paradigm-in-file-system-operations/) (更新時間：2007-12-29)
- [Shell編程範例之進程操作](http://www.tinylab.org/shell-programming-paradigm-of-process-operations/) (更新時間：2008-02-22)
- [Shell編程範例之網絡操作](http://www.tinylab.org/shell-programming-paradigm-of-network-operations/) (更新時間：2008-04-19)
- [Shell編程範例之總結篇](http://www.tinylab.org/summary-of-shell-programming-paradigm-article/) (更新時間：2008-07-21)<br>

最近，基於一個 Markdown 的[開源書籍模版](http://github.com/larrycai/kaiyuanbook)，已經把該系列整理成了自由書籍，並維護在 TinyLab 的[項目倉庫](https://github.com/tinyclub/open-shell-book)中。項目相關信息如下：

- 項目首頁：<http://www.tinylab.org/pleac-shell/>
- 代碼倉庫：[https://github.com/tinyclub/open-shell-book.git](https://github.com/tinyclub/open-shell-book)

<span id="toc_18682_17841_3"></span>
## 計劃

後續除了繼續在 [泰曉科技|TinyLab.org](http://tinylab.org) 以 Blog 形式持續更新外，還打算重新規劃、增補整個系列，並以自由書籍的方式持續維護，並通過 [TinLab.org](http://tinylab.org) 平臺接受讀者的反饋，直到正式發行出版。

歡迎大家指出本書初稿中的不足，甚至參與到相關章節的寫作、校訂和完善中來。

如果有時間和興趣，歡迎參與。可以通過 [泰曉科技](http://www.tinylab.org/about/) 聯繫我們，或者直接關注微博[@泰曉科技](http://weibo.com/tinylaborg)並私信我們。
