# 字符串操作

-    [前言](#toc_1903_12123_1)
-    [字符串的屬性](#toc_1903_12123_2)
    -    [字符串的類型](#toc_1903_12123_3)
        -    [範例：數字或者數字組合](#toc_1903_12123_4)
        -    [範例：字符組合（小寫字母、大寫字母、兩者的組合）](#toc_1903_12123_5)
        -    [範例：字母和數字的組合](#toc_1903_12123_6)
        -    [範例：空格或者 Tab 鍵等](#toc_1903_12123_7)
        -    [範例：匹配郵件地址](#toc_1903_12123_8)
        -    [範例：匹配 URL 地址(以 http 鏈接為例）](#toc_1903_12123_9)
        -    [範例：判斷字符是否為可打印字符](#toc_1903_12123_10)
    -    [字符串的長度](#toc_1903_12123_11)
        -    [範例：計算某個字符串的長度](#toc_1903_12123_12)
        -    [範例：計算某些指定字符或者字符組合的個數](#toc_1903_12123_13)
        -    [範例：統計單詞個數](#toc_1903_12123_14)
-    [字符串的顯示](#toc_1903_12123_15)
    -    [範例：在屏幕控制字符顯示位置、顏色、背景等](#toc_1903_12123_16)
    -    [範例：在屏幕的某個位置動態顯示當前系統時間](#toc_1903_12123_17)
    -    [範例：過濾掉某些控制字符串](#toc_1903_12123_18)
-    [字符串的存儲](#toc_1903_12123_19)
    -    [範例：把字符串拆分成字符串數組](#toc_1903_12123_20)
-    [字符串常規操作](#toc_1903_12123_21)
    -    [取子串](#toc_1903_12123_22)
        -    [範例：按照位置取子串](#toc_1903_12123_23)
        -    [範例：匹配字符求子串](#toc_1903_12123_24)
    -    [查詢子串](#toc_1903_12123_25)
        -    [範例：查詢子串在目標串中的位置](#toc_1903_12123_26)
        -    [範例：查詢子串，返回包含子串的行](#toc_1903_12123_27)
    -    [子串替換](#toc_1903_12123_28)
        -    [範例：把變量 var 中的空格替換成下劃線](#toc_1903_12123_29)
    -    [插入子串](#toc_1903_12123_30)
        -    [範例：在 var 字符串的空格之前或之後插入一個下劃線](#toc_1903_12123_31)
    -    [刪除子串](#toc_1903_12123_32)
        -    [範例：把 var 字符串中所有的空格給刪除掉。](#toc_1903_12123_33)
    -    [子串比較](#toc_1903_12123_34)
    -    [子串排序](#toc_1903_12123_35)
    -    [子串進制轉換](#toc_1903_12123_36)
    -    [子串編碼轉換](#toc_1903_12123_37)
-    [字符串操作進階](#toc_1903_12123_38)
    -    [正則表達式](#toc_1903_12123_39)
        -    [範例：處理 URL 地址](#toc_1903_12123_40)
        -    [範例：匹配某個文件中的特定範圍的行](#toc_1903_12123_41)
    -    [處理格式化的文本](#toc_1903_12123_42)
        -    [範例：選取指定列](#toc_1903_12123_43)
        -    [範例：文件關聯操作](#toc_1903_12123_44)
-    [參考資料](#toc_1903_12123_45)
-    [後記](#toc_1903_12123_46)


<span id="toc_1903_12123_1"></span>
## 前言

忙活了一個禮拜，終於等到週末，可以空下來寫點東西。

之前已經完成[《數值運算》][100]和[《布爾運算》][101]，這次輪到介紹*字符串操作* 。咱們先得弄明白兩個內容：

[100]: 01-chapter2.markdown
[101]: 01-chapter3.markdown

- 什麼是字符串？
- 對字符串有哪些操作？

下面是"在線新華字典"的解釋：

> 字符串:
> 簡稱“串”。有限字符的序列。數據元素為字符的線性表，是一種數據的邏輯結構。在計算機中可有不同的存儲結構。在串上可進行求子串、插入字符、刪除字符、置換字符等運算。

而字符呢？

> 字符:
> 計算機程序設計及操作時使用的符號。包括字母、數字、空格符、提示符及各種專用字符等。

照這樣說，之前介紹的[數值運算][100]中的數字，[布爾運算][101]中的真假值，都是以字符的形式呈現出來的，是一種特別的字符，對它們的運算只不過是字符操作的特例罷了。而這裡將研究一般字符的運算，它具有非常重要的意義，因為對我們來說，一般的工作都是處理字符而已。這些運算實際上將圍繞上述兩個定義來做，它們包括：

- 找出字符或者字符串的類型，是數字、字母還是其他特定字符，是可打印字符，還是不可打印字符（一些控制字符）。

- 找出組成字符串的字符個數和字符串的存儲結構（比如數組）。

- 對串的常規操作：求子串、插入字符、刪除字符、置換字符、字符串的比較等。

- 對串的一些比較複雜而有趣的操作，這裡將在最後介紹一些有趣的範例。

<span id="toc_1903_12123_2"></span>
## 字符串的屬性

<span id="toc_1903_12123_3"></span>
### 字符串的類型

字符有可能是數字、字母、空格、其他特殊字符，而字符串有可能是它們中的一種或者多種的組合，在組合之後還可能形成具有特定意義的字符串，諸如郵件地址，URL地址等。

<span id="toc_1903_12123_4"></span>
#### 範例：數字或者數字組合

    $ i=5;j=9423483247234;
    $ echo $i | grep -q "^[0-9]$"
    $ echo $?
    0
    $ echo $j | grep -q "^[0-9]\+$"
    $ echo $?
    0

<span id="toc_1903_12123_5"></span>
#### 範例：字符組合（小寫字母、大寫字母、兩者的組合）

    $ c="A"; d="fwefewjuew"; e="fewfEFWefwefe"
    $ echo $c | grep -q "^[A-Z]$"
    $ echo $d | grep -q "^[a-z]\+$"
    $ echo $e | grep -q "^[a-zA-Z]\+$"

<span id="toc_1903_12123_6"></span>
#### 範例：字母和數字的組合

    $ ic="432fwfwefeFWEwefwef"
    $ echo $ic | grep -q "^[0-9a-zA-Z]\+$"

<span id="toc_1903_12123_7"></span>
#### 範例：空格或者 Tab 鍵等

    $ echo " " | grep " "
    $ echo -e "\t" | grep "[[:space:]]" #[[:space:]]會同時匹配空格和TAB鍵
    $ echo -e " \t" | grep "[[:space:]]"
    $ echo -e "\t" | grep "" #為在鍵盤上按下TAB鍵，而不是字符

<span id="toc_1903_12123_8"></span>
#### 範例：匹配郵件地址

    $ echo "test2007@lzu.cn" | grep "[0-9a-zA-Z\.]*@[0-9a-zA-Z\.]"
    test2007@lzu.cn

<span id="toc_1903_12123_9"></span>
#### 範例：匹配 URL 地址(以 http 鏈接為例）

    $ echo "http://news.lzu.edu.cn/article.jsp?newsid=10135" | grep "^http://[0-9a-zA-Z\./=?]\+$"
    http://news.lzu.edu.cn/article.jsp?newsid=10135

說明：

-   `/dev/null` 和 `/dev/zero` 設備非常有趣，都猶如黑洞，什麼東西掉進去都會消失殆盡；後者還是個能源箱，總能從那裡取到0，直到退出
-   `[[:space:]]` 是 `grep` 用於匹配空格或 TAB 鍵字符的標記，其他標記請查幫助：`man grep`
-   上面都是用 `grep` 來進行模式匹配，實際上 `sed`，`awk` 都可用來做模式匹配，關於匹配中用到的正則表達式知識，請參考後面的相關資料
-   如果想判斷字符串是否為空，可判斷其長度是否為零，可通過 `test` 命令的 `-z` 選項來實現，具體用法見 `test` 命令，`man test`

<span id="toc_1903_12123_10"></span>
#### 範例：判斷字符是否為可打印字符

    $ echo "\t\n" | grep "[[:print:]]"
    \t\n
    $ echo $?
    0
    $ echo -e "\t\n" | grep "[[:print:]]"
    $ echo $?
    1

<span id="toc_1903_12123_11"></span>
### 字符串的長度

除了組成字符串的字符類型外，字符串還有哪些屬性呢？組成字符串的字符個數。

下面我們來計算字符串的長度，即所有字符的個數，並簡單介紹幾種求字符串中指定字符個數的方法。

<span id="toc_1903_12123_12"></span>
#### 範例：計算某個字符串的長度

即計算所有字符的個數，計算方法五花八門，擇其優著而用之：

    $ var="get the length of me"
    $ echo ${var}     # 這裡等同於$var
    get the length of me
    $ echo ${#var}
    20
    $ expr length "$var"
    20
    $ echo $var | awk '{printf("%d\n", length($0));}'
    20
    $ echo -n $var |  wc -c
    20

<span id="toc_1903_12123_13"></span>
#### 範例：計算某些指定字符或者字符組合的個數

    $ echo $var | tr -cd g | wc -c
    2
    $ echo -n $var | sed -e 's/[^g]//g' | wc -c
    2
    $ echo -n $var | sed -e 's/[^gt]//g' | wc -c
    5

<span id="toc_1903_12123_14"></span>
#### 範例：統計單詞個數

更多相關信息見[《數值計算》][100]的*單詞統計* 相關範例。

    $ echo $var | wc -w
    5
    $ echo "$var" | tr " " "\n" | grep get | uniq -c
    1
    $ echo "$var" | tr " " "\n" | grep get | wc -l
    1

說明：

`${}` 操作符在 Bash 裡頭是一個“大牛”，能勝任相當多的工作，具體就看網中人的[《Shell十三問》之`$(())`與`$()` 還有`${}`差在哪？"](http://bbs.chinaunix.net/forum.php?mod=viewthread&tid=218853&page=7#pid1617953)吧。

<span id="toc_1903_12123_15"></span>
## 字符串的顯示

接下來討論如何控制字符在終端的顯示。

<span id="toc_1903_12123_16"></span>
### 範例：在屏幕控制字符顯示位置、顏色、背景等

    $ echo -e "\033[31;40m" #設置前景色為黑色，背景色為紅色
    $ echo -e "\033[11;29H Hello, World\!" #在屏幕的第11行，29列開始打印字符串Hello,World!

<span id="toc_1903_12123_17"></span>
### 範例：在屏幕的某個位置動態顯示當前系統時間

    $ while :; do echo -e "\033[11;29H "$(date "+%Y-%m-%d %H:%M:%S"); done

<span id="toc_1903_12123_18"></span>
### 範例：過濾掉某些控制字符串

用 `col` 命令過濾某些控制字符，在處理諸如 `script`，`screen` 等截屏命令的輸出結果時，很有用。

    $ screen -L
    $ cat /bin/cat
    $ exit
    $ cat screenlog.0 | col -b   # 把一些控制字符過濾後，就可以保留可讀的操作日誌

<span id="toc_1903_12123_19"></span>
## 字符串的存儲

在我們看來，字符串是一連串的字符而已，但是為了操作方便，我們往往可以讓字符串呈現出一定的結構。在這裡，我們不關心字符串在內存中的實際存儲結構，僅僅關係它呈現出來的邏輯結構。比如，這樣一個字符串： `get
 the length of me"`，我們可以從不同的方面來呈現它。

-  通過字符在串中的位置來呈現它

這樣我們就可以通過指定位置來找到某個子串。這在 C 語言中通常可以利用指針來做。而在 Shell 編程中，有很多可用的工具，諸如 `expr`，`awk` 都提供了類似方法來實現子串的查詢動作。兩者都幾乎支持模式匹配 `match` 和完全匹配 `index`。這在後面的字符串操作中將詳細介紹。

-  根據某個分割符來取得字符串的各個部分

這裡最常見的就是行分割符、空格或者 `TAB` 分割符了，前者用來當行號，我們似乎已經司空見慣了，因為我們的編輯器就這樣“莫名”地處理著行分割符（在 UNIX 下為 `\\n`，在其他系統下有一些不同，比如 Windows 下為 `\r\n` ）。而空格或者 `TAB` 鍵經常用來分割數據庫的各個字段，這似乎也是司空見慣的事情。

正因為這樣，所以產生了大量優秀的行編輯工具，諸如 `grep`，`awk`，`sed` 等。在“行內”（姑且這麼說吧，就是處理單行，即字符串中不再包含行分割符）的字符串分割方面，`cut` 和 `awk` 提供了非常優越的“行內”（處理單行）處理能力。

-  更方便地處理用分割符分割好的各個部分

同樣是用到分割符，但為了更方便的操作分割以後的字符串的各個部分，我們抽象了“數組”這麼一個數據結構，從而讓我們更加方便地通過下標來獲取某個指定的部分。 `bash` 提供了這麼一種數據結構，而優秀的 `awk` 也同樣提供了它，我們這裡將簡單介紹它們的用法。

<span id="toc_1903_12123_20"></span>
### 範例：把字符串拆分成字符串數組

-   Bash 提供的數組數據結構，以數字為下標的，和 C 語言從 0 開始的下標一樣

        $ var="get the length of me"
        $ var_arr=($var)    #把字符串var存放到字符串數組var_arr中，默認以空格作為分割符
        $ echo ${var_arr[0]} ${var_arr[1]} ${var_arr[2]} ${var_arr[3]} ${var_arr[4]}
        get the length of me
        $ echo ${var_arr[@]}    #整個字符串，可以用*代替@，下同
        get the length of me
        $ echo ${#var_arr[@]}   #類似於求字符串長度，`#`操作符也可用來求數組元素個數
        5

    也可以直接給某個數組元素賦值

        $ var_arr[5]="new_element"
        $ echo ${var_arr[5]}
        6
        $ echo ${var_arr[5]}
        new_element

    Bash 實際上還提供了一種類似於“數組”的功能，即 `for i in`，它可以很方便地獲取某個字符串的各個部分，例如：

        $ for i in $var; do echo -n $i"_"; done
        get_the_length_of_me_

-   `awk` 裡的數組，注意比較它和 `Bash` 裡的數組的異同

    `split` 把一行按照空格分割，存放到數組 `var\_arr` 中，並返回數組長度。注意：這裡第一個元素下標不是 0，而是 1

        $ echo $var | awk '{printf("%d %s\n", split($0, var_arr, " "), var_arr[1]);}'
        5 get

    實際上，上述操作很類似 `awk` 自身的行處理功能： `awk` 默認把一行按照空格分割為多個域，並可以通過 `$1`，`$2`，`$3`  `...` 來獲取，`$0` 表示整行。

    這裡的 `NF` 是該行的域的總數，類似於上面數組的長度，它同樣提供了一種通過類似“下標”訪問某個字符串的功能。

        $ echo $var | awk '{printf("%d | %s %s %s %s %s | %s\n", NF, $1, $2, $3, $4, $5, $0);}'
        5 | get the length of me | get the length of me

    `awk` 的“數組”功能何止於此呢，看看它的 `for` 引用吧，注意，這個和 `Bash` 裡頭的 `for` 不太一樣，`i` 不是元素本身，而是下標：

        $ echo $var | awk '{split($0, var_arr, " "); for(i in var_arr) printf("%s ",var_arr[i]);}'
        of me get the length
        4 5 1 2 3

    另外，從上述結果可以看到，經過 `for` 處理後，整個結果沒有按照原理的字符順序排列，不過如果僅僅是迭代出所有元素這個同樣很有意義。

`awk` 還有更“厲害”的處理能力，它的下標可以不是數字，可以是字符串，從而變成了“關聯”數組，這種“關聯”在某些方面非常方便。
比如，把某個文件中的某個系統調用名根據另外一個文件中的函數地址映射表替換成地址，可以這麼實現：

    $ cat symbol
    sys_exit
    sys_read
    sys_close
    $ ls /boot/System.map*
    $ awk '{if(FILENAME ~ "System.map") map[$3]=$1; else {printf("%s\n", map[$1])}}' \
        /boot/System.map-2.6.20-16-generic symbol
    c0129a80
    c0177310
    c0175d80

另外，awk還支持用delete函數刪除某個數組元素。如果某些場合有需要的話，別忘了awk還支持二維數組。

<span id="toc_1903_12123_21"></span>
## 字符串常規操作

字符串操作包括取子串、查詢子串、插入子串、刪除子串、子串替換、子串比較、子串排序、子串進制轉換、子串編碼轉換等。

<span id="toc_1903_12123_22"></span>
### 取子串

取子串的方法主要有：

- 直接到指定位置求子串
- 字符匹配求子串

<span id="toc_1903_12123_23"></span>
#### 範例：按照位置取子串

比如從什麼位置開始，取多少個字符

    $ var="get the length of me"
    $ echo ${var:0:3}
    get
    $ echo ${var:(-2)}   # 方向相反呢
    me

    $ echo `expr substr "$var" 5 3` #記得把$var引起來，否則expr會因為空格而解析錯誤
    the

    $ echo $var | awk '{printf("%s\n", substr($0, 9, 6))}'
    length

`awk` 把 `$var` 按照空格分開為多個變量，依次為 `$1`，`$2`，`$3`，`$4`，`$5`

    $ echo $var | awk '{printf("%s\n", $1);}'
    get
    $ echo $var | awk '{printf("%s\n", $5);}'
    me

差點略掉`cut`小工具，它用起來和awk類似，`-d`指定分割符，如同`awk`用`-F`指定分割符一樣；`-f`指定“域”，如同awk的`$數字`。

    $ echo $var | cut -d" " -f 5

<span id="toc_1903_12123_24"></span>
#### 範例：匹配字符求子串

用 Bash 內置支持求字串：

    $ echo ${var%% *} #從右邊開始計算，刪除最左邊的空格右邊的所有字符
    get
    $ echo ${var% *} #從右邊開始計算，刪除第一個空格右邊的所有字符
    get the length of
    $ echo ${var##* }  #從左邊開始計算，刪除最右邊的空格左邊的所有字符
    me
    $ echo ${var#* }  #從左邊開始計算，刪除第一個空格左邊的所有字符
    the length of me

刪除所有 `空格＋字母組合` 的字符串：

    $ echo $var | sed 's/ [a-z]*//g'
    get
    $ echo $var | sed 's/[a-z]* //g'
    me

`sed` 有按地址（行）打印(p)的功能，記得先用 `tr` 把空格換成行號：

    $ echo $var | tr " " "\n" | sed -n 1p
    get
    $ echo $var | tr " " "\n" | sed -n 5p
    me

`tr` 也可以用來取子串，它可以類似`#`和 `%` 來“拿掉”一些字符串來實現取子串：

    $ echo $var | tr -d " "
    getthelengthofme
    $ echo $var | tr -cd "[a-z]" #把所有的空格都拿掉了，僅僅保留字母字符串，注意-c和-d的用法
    getthelengthofme

說明：

-   `%` 和`#`刪除字符的方向不一樣，前者在右，後者在左，`%%` 和 `%`， `##` 和 `#` 的方向是前者是最大匹配，後者是最小匹配。（好的記憶方法見網中人的鍵盤記憶法：`#`，`$`，`%` 是鍵盤依次從左到右的三個鍵）
-   `tr` 的 `-c` 選項是 `complement` 的縮寫，即 `invert`，而 `-d` 選項是刪除，`tr -cd "[a-z]"` 這樣一來就變成保留所有的字母

對於字符串的截取，實際上還有一些命令，如果 `head`，`tail` 等可以實現有意思的功能，可以截取某個字符串的前面、後面指定的行數或者字節數。例如：

    $ echo "abcdefghijk" | head -c 4
    abcd
    $ echo -n "abcdefghijk" | tail -c 4
    hijk

<span id="toc_1903_12123_25"></span>
### 查詢子串

子串查詢包括：

- 返回符合某個模式的子串本身
- 返回子串在目標串中的位置

準備：在進行下面的操作之前，請準備一個文件 test.txt，裡頭有內容 "consists of"，用於下面的範例。

<span id="toc_1903_12123_26"></span>
#### 範例：查詢子串在目標串中的位置

`expr index`貌似僅僅可以返回某個字符或者多個字符中第一個字符出現的位置

    $ var="get the length of me"
    $ expr index "$var" t
    3

awk卻能找出字串，match還可以匹配正則表達式

    $ echo $var | awk '{printf("%d\n", match($0,"the"));}'
    5

<span id="toc_1903_12123_27"></span>
#### 範例：查詢子串，返回包含子串的行

`awk`，`sed` 都可以實現這些功能，但是 `grep` 最擅長

    $ grep "consists of" test.txt   # 查詢文件包含consists of的行，並打印這些行
    $ grep "consists[[:space:]]of" -n -H test.txt # 打印文件名，子串所在行的行號和該行的內容
    $ grep "consists[[:space:]]of" -n -o test.txt # 僅僅打印行號和匹配到的子串本身的內容

    $ awk '/consists of/{ printf("%s:%d:%s\n",FILENAME, FNR, $0)}' text  #看到沒？和grep的結果一樣
    $ sed -n -e '/consists of/=;/consists of/p' text #同樣可以打印行號

說明：

-   `awk`，`grep`，`sed` 都能通過模式匹配查找指定字符串，但它們各有所長，將在後續章節中繼續使用和比較它們，進而發現各自優點
-   在這裡姑且把文件內容當成了一個大的字符串，在後面章節中將專門介紹文件操作，所以對文件內容中存放字符串的操作將會有更深入的分析和介紹

<span id="toc_1903_12123_28"></span>
### 子串替換

子串替換就是把某個指定的子串替換成其他的字符串，這裡蘊含了“插入子串”和“刪除子串”的操作。例如，想插入某個字符串到某個子串之前，就可以把原來的子串替換成”子串+新的字符串“，如果想刪除某個子串，就把子串替換成空串。不過有些工具提供了一些專門的用法來做插入子串和刪除子串的操作，所以呆夥還會專門介紹。另外，要想替換掉某個子串，一般都是先找到子串（查詢子串），然後再把它替換掉，實質上很多工具在使用和設計上都體現了這麼一點。

<span id="toc_1903_12123_29"></span>
#### 範例：把變量 var 中的空格替換成下劃線

用 `{}` 運算符，還記得麼？網中人的教程

    $ var="get the length of me"
    $ echo ${var/ /_}        #把第一個空格替換成下劃線
    get_the length of me
    $ echo ${var// /_}       #把所有空格都替換成下劃線
    get_the_length_of_me

用 `awk`，`awk` 提供了轉換的最小替換函數 `sub` 和全局替換函數 `gsub`，類似 `/` 和 `//`

    $ echo $var | awk '{sub(" ", "_", $0); printf("%s\n", $0);}'
    get_the length of me
    $ echo $var | awk '{gsub(" ", "_", $0); printf("%s\n", $0);}'
    get_the_length_of_me

用 `sed`，子串替換可是 `sed` 的特長：

    $ echo $var | sed -e 's/ /_/'    #s <= substitude
    get_the length of me
    $ echo $var | sed -e 's/ /_/g'   #看到沒有，簡短兩個命令就實現了最小匹配和最大匹配g <= global
    get_the_length_of_me

有忘記 `tr` 命令麼？可以用替換單個字符的：

    $ echo $var | tr " " "_"
    get_the_length_of_me
    $ echo $var | tr '[a-z]' '[A-Z]'   #這個可有意思了，把所有小寫字母都替換為大寫字母
    GET THE LENGTH OF ME

說明： `sed` 還有很有趣的標籤用法呢，下面再介紹吧。

有一種比較有意思的字符串替換是：整個文件行的倒置，這個可以通過 `tac` 命令實現，它會把文件中所有的行全部倒轉過來。在某種意義上來說，排序實際上也是一個字符串替換。

<span id="toc_1903_12123_30"></span>
### 插入子串

在指定位置插入子串，這個位置可能是某個子串的位置，也可能是從某個文件開頭算起的某個長度。通過上面的練習，我們發現這兩者之間實際上是類似的。

公式：插入子串=把"old子串"替換成"old子串+new子串"或者"new子串+old子串"

<span id="toc_1903_12123_31"></span>
#### 範例：在 var 字符串的空格之前或之後插入一個下劃線

用{}：

    $ var="get the length of me"
    $ echo ${var/ /_ }        #在指定字符串之前插入一個字符串
    get_ the length of me
    $ echo ${var// /_ }
    get_ the_ length_ of_ me
    $ echo ${var/ / _}        #在指定字符串之後插入一個字符串
    get _the length of me
    $ echo ${var// / _}
    get _the _length _of _me

其他的還用演示麼？這裡主要介紹sed怎麼用來插入字符吧，因為它的標籤功能很有趣
說明：`(` 和 `)` 將不匹配到的字符串存放為一個標籤，按匹配順序為`\1`,`\2`...

    $ echo $var | sed -e 's/\( \)/_\1/'
    get_ the length of me
    $ echo $var | sed -e 's/\( \)/_\1/g'
    get_ the_ length_ of_ me
    $ echo $var | sed -e 's/\( \)/\1_/'
    get _the length of me
    $ echo $var | sed -e 's/\( \)/\1_/g'
    get _the _length _of _me

看看 `sed` 的標籤的順序是不是 `\1`,`\2`...，看到沒？`\2` 和 `\1` 調換位置後，`the` 和 `get` 的位置掉換了：

    $ echo $var | sed -e 's/\([a-z]*\) \([a-z]*\) /\2 \1 /g'
    the get of length me

`sed` 還有專門的插入指令，`a` 和 `i`，分別表示在匹配的行後和行前插入指定字符

    $ echo $var | sed '/get/a test'
    get the length of me
    test
    $ echo $var | sed '/get/i test'
    test
    get the length of me

<span id="toc_1903_12123_32"></span>
### 刪除子串

刪除子串：應該很簡單了吧，把子串替換成“空”（什麼都沒有）不就變成了刪除麼。還是來簡單複習一下替換吧。

<span id="toc_1903_12123_33"></span>
#### 範例：把 var 字符串中所有的空格給刪除掉。

鼓勵：這樣一替換不知道變成什麼單詞啦，誰認得呢？但是中文卻是連在一起的，所以中文有多難，你想到了麼？原來你也是個語言天才，而英語並不可怕，你有學會它的天賦，只要有這個打算。

再用 `{}`

    $ echo ${var// /}
    getthelengthofme

再用 `awk`

    $ echo $var | awk '{gsub(" ","",$0); printf("%s\n", $0);}'

再用 `sed`

    $ echo $var | sed 's/ //g'
    getthelengthofme

還有更簡單的 `tr` 命令，`tr` 也可以把空格給刪除掉，看

    $ echo $var | tr -d " "
    getthelengthofme

如果要刪除第一個空格後面所有的字符串該怎麼辦呢？還記得 `{}` 的 `#` 和 `%` 用法麼？如果不記得，回到這節的開頭開始複習吧。（實際上刪除子串和取子串未嘗不是兩種互補的運算呢，刪除掉某些不想要的子串，也就同時取得另外那些想要的子串——這個世界就是一個“二元”的世界，非常有趣）

<span id="toc_1903_12123_34"></span>
### 子串比較

這個很簡單：還記得 `test` 命令的用法麼？ `man test` 。它可以用來判斷兩個字符串是否相等。另外，有發現“字符串是否相等”和“字符串能否跟另外一個字符串匹配 `"` 兩個問題之間的關係嗎？如果兩個字符串完全匹配，那麼這兩個字符串就相等了。所以呢，上面用到的字符串匹配方法，也同樣可以用到這裡。

<span id="toc_1903_12123_35"></span>
### 子串排序

差點忘記這個重要內容了，子串排序可是經常用到，常見的有按字母序、數字序等正序或反序排列。 `sort` 命令可以用來做這個工作，它和其他行處理命令一樣，是按行操作的，另外，它類似 `cut` 和 `awk`，可以指定分割符，並指定需要排序的列。

    $ var="get the length of me"
    $ echo $var | tr ' ' '\n' | sort   #正序排
    get
    length
    me
    of
    the
    $ echo $var | tr ' ' '\n' | sort -r #反序排
    the
    of
    me
    length
    get
    $ cat > data.txt
    1 2 3 4 5 6 7 8 9 10 11 12 13 14 15
    41 45 44 44 26 44 42 20 20 38 37 25 45 45 45
    16 17 18 19 20 21 22 23 24 25 26 27 28 29 30
    44 20 30 39 35 38 38 28 25 30 36 20 24 32 33
    31 32 33 34 35 36 37 38 39 40 41 42 43 44 45
    41 33 51 39 20 20 44 37 38 39 42 40 37 50 50
    46 47 48 49 50 51 52 53 54 55 56
    42 43 41 42 45 42 19 39 75 17 17
    $ cat data.txt | sort -k 2 -n
    1 2 3 4 5 6 7 8 9 10 11 12 13 14 15
    16 17 18 19 20 21 22 23 24 25 26 27 28 29 30
    44 20 30 39 35 38 38 28 25 30 36 20 24 32 33
    31 32 33 34 35 36 37 38 39 40 41 42 43 44 45
    41 33 51 39 20 20 44 37 38 39 42 40 37 50 50
    42 43 41 42 45 42 19 39 75 17 17
    41 45 44 44 26 44 42 20 20 38 37 25 45 45 45
    46 47 48 49 50 51 52 53 54 55 56

<span id="toc_1903_12123_36"></span>
### 子串進制轉換

如果字母和數字字符用來計數，那麼就存在進制轉換的問題。在[《數值計算》][100]一節，已經介紹了 `bc` 命令，這裡再複習一下。

    $ echo "ibase=10;obase=16;10" | bc
    A

說明： `ibase` 指定輸入進制，`obase` 指出輸出進制，這樣通過調整 `ibase` 和 `obase`，你想怎麼轉就怎麼轉啦！

<span id="toc_1903_12123_37"></span>
### 子串編碼轉換

什麼是字符編碼？這個就不用介紹了吧，看過那些亂七八糟顯示的網頁麼？大多是因為瀏覽器顯示時的”編碼“和網頁實際採用的”編碼“不一致導致的。字符編碼通常是指：把一序列”可打印“字符轉換成二進制表示，而字符解碼呢則是執行相反的過程，如果這兩個過程不匹配，則出現了所謂的”亂碼“。

為了解決”亂碼“問題呢？就需要進行編碼轉換。在 Linux 下，我們可以使用 `iconv` 這個工具來進行相關操作。這樣的情況經常在不同的操作系統之間移動文件，不同的編輯器之間交換文件的時候遇到，目前在 Windows 下常用的漢字編碼是 `gb2312`，而在 Linux 下則大多采用 `utf8` 。

    $ nihao_utf8=$(echo "你好")
    $ nihao_gb2312=$(echo $nihao_utf8 | iconv -f utf8 -t gb2312)

<span id="toc_1903_12123_38"></span>
## 字符串操作進階

實際上，在用 Bash 編程時，大部分時間都是在處理字符串，因此把這一節熟練掌握非常重要。

<span id="toc_1903_12123_39"></span>
### 正則表達式

<span id="toc_1903_12123_40"></span>
#### 範例：處理 URL 地址

URL 地址(URL（Uniform Resoure Locator：統一資源定位器）是WWW頁的地址)幾乎是我們日常生活的玩伴，我們已經到了無法離開它的地步啦，對它的操作很多，包括判斷 URL 地址的有效性，截取地址的各個部分（服務器類型、服務器地址、端口、路徑等）並對各個部分進行進一步的操作。

下面我們來具體處理這個URL地址：ftp://anonymous:ftp@mirror.lzu.edu.cn/software/scim-1.4.7.tar.gz

    $ url="ftp://anonymous:ftp@mirror.lzu.edu.cn/software/scim-1.4.7.tar.gz"

匹配URL地址，判斷URL地址的有效性

    $ echo $url | grep "ftp://[a-z]*:[a-z]*@[a-z\./-]*"

截取服務器類型

    $ echo ${url%%:*}
    ftp
    $ echo $url | cut -d":" -f 1
    ftp

截取域名

    $ tmp=${url##*@} ; echo ${tmp%%/*}
    mirror.lzu.edu.cn

截取路徑

    $ tmp=${url##*@} ; echo ${tmp%/*}
    mirror.lzu.edu.cn/software

截取文件名

    $ basename $url
    scim-1.4.7.tar.gz
    $ echo ${url##*/}
    scim-1.4.7.tar.gz

截取文件類型（擴展名）

    $ echo $url | sed -e 's/.*[0-9].\(.*\)/\1/g'
    tar.gz

<span id="toc_1903_12123_41"></span>
#### 範例：匹配某個文件中的特定範圍的行

先準備一個測試文件README

    Chapter 7 -- Exercises

    7.1 please execute the program: mainwithoutreturn, and print the return value
    of it with the command "echo $?", and then compare the return of the printf
    function, they are the same.

    7.2 it will depend on the exection mode, interactive or redirection to a file,
    if interactive, the "output" action will accur after the \n char with the line
    buffer mode, else, it will be really "printed" after all of the strings have
    been stayed in the buffer.

    7.3 there is no another effective method in most OS. because argc and argv are
    not global variables like environ.

然後開始實驗，

打印出答案前指定行範圍：第 7 行到第 9 行，剛好找出了第 2 題的答案

    $ sed -n 7,9p README
    7.2 it will depend on the exection mode, interactive or redirection to a file,
    if interactive, the "output" action will accur after the \n char with the line
    buffer mode, else, it will be really "printed" after all of the strings have

其實，因為這個文件內容格式很有特色，有更簡單的辦法

    $ awk '/7.2/,/^$/ {printf("%s\n", $0);}' README
    7.2 it will depend on the exection mode, interactive or redirection to a file,
    if interactive, the "output" action will accur after the \n char with the line
    buffer mode, else, it will be really "printed" after all of the strings have
    been stayed in the buffer.

有了上面的知識，就可以非常容易地進行這些工作啦：修改某個文件的文件名，比如調整它的編碼，下載某個網頁裡頭的所有 `pdf` 文檔等。這些就作為練習自己做吧。

<span id="toc_1903_12123_42"></span>
### 處理格式化的文本

平時做工作，大多數時候處理的都是一些“格式化”的文本，比如類似 `/etc/passwd` 這樣的有固定行和列的文本，也有類似 `tree` 命令輸出的那種具有樹形結構的文本，當然還有其他具有特定結構的文本。

關於樹狀結構的文本的處理，可以參考我早期寫的另外一篇博客文章：[源碼分析：靜態分析 C 程序函數調用關係圖](http://www.tinylab.org/callgraph-draw-the-calltree-of-c-functions/)

實際上，只要把握好特性結構的一些特點，並根據具體的應用場合，處理起來就不會困難。

下面來介紹具體文本的操作，以 `/etc/passwd` 文件為例。關於這個文件的幫忙和用法，請通過 `man 5 passwd` 查看。下面對這個文件以及相關的文件進行一些有意義的操作。

<span id="toc_1903_12123_43"></span>
#### 範例：選取指定列

選取/etc/passwd文件中的用戶名和組ID兩列

    $ cat /etc/passwd | cut -d":" -f1,4

選取/etc/group文件中的組名和組ID兩列

    $ cat /etc/group | cut -d":" -f1,3

<span id="toc_1903_12123_44"></span>
#### 範例：文件關聯操作

如果想找出所有用戶所在的組，怎麼辦？

    $ join -o 1.1,2.1 -t":" -1 4 -2 3 /etc/passwd /etc/group
    root:root
    bin:bin
    daemon:daemon
    adm:adm
    lp:lp
    pop:pop
    nobody:nogroup
    falcon:users

說明： `join` 命令用來連接兩個文件，有點類似於數據庫的兩個表的連接。 `-t` 指定分割符，`-1 4 -2 3` 指定按照第一個文件的第 4 列和第二個文件的第 3 列，即組 `ID` 進行連接，`-o`
`1.1,2.1` 表示僅僅輸出第一個文件的第一列和第二個文件的第一列，這樣就得到了我們要的結果，不過，可惜的是，這個結果並不準確，再進行下面的操作，你就會發現：

    $ cat /etc/passwd | sort -t":" -n -k 4 > /tmp/passwd
    $ cat /etc/group | sort -t":" -n -k 3 > /tmp/group
    $ join -o 1.1,2.1 -t":" -1 4 -2 3 /tmp/passwd /tmp/group
    halt:root
    operator:root
    root:root
    shutdown:root
    sync:root
    bin:bin
    daemon:daemon
    adm:adm
    lp:lp
    pop:pop
    nobody:nogroup
    falcon:users
    games:users

可以看到這個結果才是正確的，所以以後使用 `join` 千萬要注意這個問題，否則採取更保守的做法似乎更能保證正確性，更多關於文件連接的討論見參考後續資料。

上面涉及到了處理某格式化行中的指定列，包括截取（如 `SQL` 的 `select` 用法），連接（如 `SQL` 的 `join` 用法），排序（如 `SQL` 的 `order by` 用法），都可以通過指定分割符來拆分某個格式化的行，另外，“截取”的做法還有很多，不光是 `cut`，`awk`，甚至通過 `IFS` 指定分割符的 `read` 命令也可以做到，例如：

    $ IFS=":"; cat /etc/group | while read C1 C2 C3 C4; do echo $C1 $C3; done

因此，熟悉這些用法，我們的工作將變得非常靈活有趣。

到這裡，需要做一個簡單的練習，如何把按照列對應的用戶名和用戶 ID 轉換成按照行對應的，即把類似下面的數據：

    $ cat /etc/passwd | cut -d":" -f1,3 --output-delimiter=" "
    root 0
    bin 1
    daemon 2

轉換成：

    $ cat a
    root    bin     daemon
    0       1       2

並轉換回去，有什麼辦法呢？記得諸如 `tr`，`paste`，`split` 等命令都可以使用。

參考方法：

-  正轉換：先截取用戶名一列存入文件 `user`，再截取用戶 `ID` 存入 `id`，再把兩個文件用 `paste -s` 命令連在一起，這樣就完成了正轉換
-  逆轉換：先把正轉換得到的結果用 `split -1` 拆分成兩個文件，再把兩個拆分後的文件用 `tr` 把分割符 `\t` 替換成 `\n`，只有用 `paste` 命令把兩個文件連在一起，這樣就完成了逆轉換。

<span id="toc_1903_12123_45"></span>
## 參考資料

-   《高級 Bash 腳本編程指南》之操作字符串，之指定變量的類型
-   [《Shell十三問》之`$(())`與`$()`還有`${}`差在哪？](http://bbs.chinaunix.net/forum.php?mod=viewthread&tid=218853&page=7#pid1617953)
-   [Regular Expressions - User guide](http://www.zytrax.com/tech/web/regex.htm)
-   Regular Expression Tutorial
-   [Grep Tutorial](http://www.panix.com/~elflord/unix/grep.html)
-   [Sed Tutorial](http://www.panix.com/~elflord/unix/sed.html)
-   awk Tutorial
-   sed Tutorial
-   [An awk Primer](http://www.vectorsite.net/tsawk.html)
-   [一些奇怪的 UNIX 指令名字的由來](http://www.linuxsir.org/bbs/showthread.php?t=24264)
-   [磨練構建正則表達式模式的技能](http://www.ibm.com/developerworks/cn/aix/library/au-expressions.html)
-   [基礎11：文件分類、合併和分割(sort,uniq,join,cut,paste,split)](http://blog.chinaunix.net/u/9465/showart_144700.html)
-   [使用 Linux 文本工具簡化數據的提取](http://linux.chinaunix.net/docs/2006-09-22/2803.shtml)
-   [SED 單行腳本快速參考（Unix流編輯器）](http://sed.sourceforge.net/sed1line_zh-CN.html)

<span id="toc_1903_12123_46"></span>
## 後記

-  這一節本來是上個禮拜該弄好的，但是這些天太忙了，到現在才寫好一個“初稿”，等到有時間再補充具體的範例。這一節的範例應該是最最有趣的，所有得好好研究一下幾個有趣的範例。
-  寫完上面的部分貌似是 1 點多，剛 `check` 了一下錯別字和語法什麼的，再添加了一節，即“字符串的存儲結構”，到現在已經快 `half past 2` 啦，晚安，朋友們。
-  26 號，添加“子串進制轉換”和“子串編碼轉換”兩小節以及一個處理 `URL` 地址的範例。

