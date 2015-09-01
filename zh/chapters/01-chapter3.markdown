# 布爾運算

-    [前言](#toc_17877_9500_1)
-    [常規的布爾運算](#toc_17877_9500_2)
    -    [在 Shell 下如何進行邏輯運算](#toc_17877_9500_3)
        -    [範例：true or false](#toc_17877_9500_4)
        -    [範例：與運算](#toc_17877_9500_5)
        -    [範例：或運算](#toc_17877_9500_6)
        -    [範例：非運算，即取反](#toc_17877_9500_7)
    -    [Bash 裡頭的 true 和 false 是我們通常認為的 1 和 0 麼？](#toc_17877_9500_8)
        -    [範例：返回值 v.s. 邏輯值](#toc_17877_9500_9)
        -    [範例：查看 true 和 false 幫助和類型](#toc_17877_9500_10)
-    [條件測試](#toc_17877_9500_11)
    -    [條件測試基本使用](#toc_17877_9500_12)
        -    [範例：數值測試](#toc_17877_9500_13)
        -    [範例：字符串測試](#toc_17877_9500_14)
        -    [範例：文件測試](#toc_17877_9500_15)
    -    [各種邏輯測試的組合](#toc_17877_9500_16)
        -    [範例：如果 a，b，c 都等於下面對應的值，那麼打印 YES，通過 -a 進行與測試](#toc_17877_9500_17)
        -    [範例：測試某個“東西”是文件或者目錄，通過 -o 進行“或”運算](#toc_17877_9500_18)
        -    [範例：測試某個“東西”是否為文件，測試 `!` 非運算](#toc_17877_9500_19)
    -    [比較 -a 與 &&, -o 與 ||， ! test 與 test !](#toc_17877_9500_20)
        -    [範例：要求某文件可執行且有內容，用 -a 和 && 分別實現](#toc_17877_9500_21)
        -    [範例：要求某個字符串要麼為空，要麼和某個字符串相等](#toc_17877_9500_22)
        -    [範例：測試某個數字不滿足指定的所有條件](#toc_17877_9500_23)
-    [命令列表](#toc_17877_9500_24)
    -    [命令列表的執行規律](#toc_17877_9500_25)
        -    [範例：如果 ping 通 www.lzu.edu.cn，那麼打印連通信息](#toc_17877_9500_26)
    -    [命令列表的作用](#toc_17877_9500_27)
        -    [範例：在腳本里判斷程序的參數個數，和參數類型](#toc_17877_9500_28)
-    [小結](#toc_17877_9500_29)


<span id="toc_17877_9500_1"></span>
## 前言

上個禮拜介紹了[Shell編程範例之數值運算][100]，對 Shell 下基本數值運算方法做了簡單的介紹，這周將一起探討布爾運算，即如何操作“真假值”。

[100]: 01-chapter2.markdown

在 Bash 裡有這樣的常量(實際上是兩個內置命令，在這裡我們姑且這麼認為，後面將介紹)，即 true 和 false，一個表示真，一個表示假。對它們可以進行與、或、非運算等常規的邏輯運算，在這一節，我們除了討論這些基本邏輯運算外，還將討論Shell編程中的**條件測試**和**命令列表**，並介紹它們和布爾運算的關係。

<span id="toc_17877_9500_2"></span>
## 常規的布爾運算

這裡主要介紹 `Bash` 裡頭常規的邏輯運算，與、或、非。

<span id="toc_17877_9500_3"></span>
### 在 Shell 下如何進行邏輯運算

<span id="toc_17877_9500_4"></span>
#### 範例：true or false

單獨測試 `true` 和 `false`，可以看出 `true` 是真值，`false` 為假

    $ if true;then echo "YES"; else echo "NO"; fi
    YES
    $ if false;then echo "YES"; else echo "NO"; fi
    NO

<span id="toc_17877_9500_5"></span>
#### 範例：與運算

    $ if true && true;then echo "YES"; else echo "NO"; fi
    YES
    $ if true && false;then echo "YES"; else echo "NO"; fi
    NO
    $ if false && false;then echo "YES"; else echo "NO"; fi
    NO
    $ if false && true;then echo "YES"; else echo "NO"; fi
    NO

<span id="toc_17877_9500_6"></span>
#### 範例：或運算

    $ if true || true;then echo "YES"; else echo "NO"; fi
    YES
    $ if true || false;then echo "YES"; else echo "NO"; fi
    YES
    $ if false || true;then echo "YES"; else echo "NO"; fi
    YES
    $ if false || false;then echo "YES"; else echo "NO"; fi
    NO

<span id="toc_17877_9500_7"></span>
#### 範例：非運算，即取反

    $ if ! false;then echo "YES"; else echo "NO"; fi
    YES
    $ if ! true;then echo "YES"; else echo "NO"; fi
    NO

可以看出 `true` 和 `false` 按照我們對邏輯運算的理解進行著，但是為了能夠更好的理解 Shell 對邏輯運算的實現，我們還得弄清楚，`true` 和 `false` 是怎麼工作的？

<span id="toc_17877_9500_8"></span>
### Bash 裡頭的 true 和 false 是我們通常認為的 1 和 0 麼？

回答是：否。

<span id="toc_17877_9500_9"></span>
#### 範例：返回值 v.s. 邏輯值

`true` 和 `false` 它們本身並非邏輯值，它們都是 Shell 的內置命令，只是它們的返回值是一個“邏輯值”：

    $ true
    $ echo $?
    0
    $ false
    $ echo $?
    1

可以看到 `true` 返回了 0，而 `false` 則返回了 1 。跟我們離散數學裡學的真值 1 和 0 並不是對應的，而且相反的。

<span id="toc_17877_9500_10"></span>
#### 範例：查看 true 和 false 幫助和類型

    $ help true false
    true: true
         Return a successful result.
    false: false
         Return an unsuccessful result.
    $ type true false
    true is a shell builtin
    false is a shell builtin

說明：`$?` 是一個特殊變量，存放有上一次進程的結束狀態（退出狀態碼）。

從上面的操作不難聯想到在 C 語言程序設計中為什麼會強調在 `main` 函數前面加上 `int`，並在末尾加上 `return 0` 。因為在 Shell 裡，將把 0 作為程序是否成功結束的標誌，這就是 Shell 裡頭 `true` 和 `false` 的實質，它們用以反應某個程序是否正確結束，而並非傳統的真假值（1 和 0），相反地，它們返回的是 0 和 1 。不過慶幸地是，我們在做邏輯運算時，無須關心這些。

<span id="toc_17877_9500_11"></span>
## 條件測試

從上節中，我們已經清楚地瞭解了 Shell 下的“邏輯值”是什麼：是進程退出時的返回值，如果成功返回，則為真，如果不成功返回，則為假。

而條件測試正好使用了 `test` 這麼一個指令，它用來進行數值測試（各種數值屬性測試）、字符串測試（各種字符串屬性測試）、文件測試（各種文件屬性測試），我們通過判斷對應的測試是否成功，從而完成各種常規工作，再加上各種測試的邏輯組合後，將可以完成更復雜的工作。

<span id="toc_17877_9500_12"></span>
### 條件測試基本使用

<span id="toc_17877_9500_13"></span>
#### 範例：數值測試

    $ if test 5 -eq 5;then echo "YES"; else echo "NO"; fi
    YES
    $ if test 5 -ne 5;then echo "YES"; else echo "NO"; fi
    NO

<span id="toc_17877_9500_14"></span>
#### 範例：字符串測試

    $ if test -n "not empty";then echo "YES"; else echo "NO"; fi
    YES
    $ if test -z "not empty";then echo "YES"; else echo "NO"; fi
    NO
    $ if test -z "";then echo "YES"; else echo "NO"; fi
    YES
    $ if test -n "";then echo "YES"; else echo "NO"; fi
    NO

<span id="toc_17877_9500_15"></span>
#### 範例：文件測試

    $ if test -f /boot/System.map; then echo "YES"; else echo "NO"; fi
    YES
    $ if test -d /boot/System.map; then echo "YES"; else echo "NO"; fi
    NO

<span id="toc_17877_9500_16"></span>
### 各種邏輯測試的組合

<span id="toc_17877_9500_17"></span>
#### 範例：如果 a，b，c 都等於下面對應的值，那麼打印 YES，通過 -a 進行"與"測試

    $ a=5;b=4;c=6;
    $ if test $a -eq 5 -a $b -eq 4 -a $c -eq 6; then echo "YES"; else echo "NO"; fi
    YES

<span id="toc_17877_9500_18"></span>
#### 範例：測試某個“東西”是文件或者目錄，通過 -o 進行“或”運算

    $ if test -f /etc/profile -o -d /etc/profile;then echo "YES"; else echo "NO"; fi
    YES

<span id="toc_17877_9500_19"></span>
#### 範例：測試某個“東西”是否為文件，測試 `!` 非運算

    $ if test ! -f /etc/profile; then echo "YES"; else echo "NO"; fi
    NO

上面僅僅演示了 `test` 命令一些非常簡單的測試，你可以通過 `help test` 獲取 `test` 的更多用法。需要注意的是，`test` 命令內部的邏輯運算和 Shell 的邏輯運算符有一些區別，對應的為 `-a` 和 `&&`，`-o` 與 `||`，這兩者不能混淆使用。而非運算都是 `!`，下面對它們進行比較。

<span id="toc_17877_9500_20"></span>
### 比較 -a 與 &&, -o 與 ||， ! test 與 test !

<span id="toc_17877_9500_21"></span>
#### 範例：要求某文件可執行且有內容，用 -a 和 && 分別實現

    $ cat > test.sh
    #!/bin/bash
    echo "test"
    [CTRL+D]  # 按下組合鍵CTRL與D結束cat輸入，後同，不再註明
    $ chmod +x test.sh
    $ if test -s test.sh -a -x test.sh; then echo "YES"; else echo "NO"; fi
    YES
    $ if test -s test.sh && test -x test.sh; then echo "YES"; else echo "NO"; fi
    YES

<span id="toc_17877_9500_22"></span>
#### 範例：要求某個字符串要麼為空，要麼和某個字符串相等

    $ str1="test"
    $ str2="test"
    $ if test -z "$str2" -o "$str2" == "$str1"; then echo "YES"; else echo "NO"; fi
    YES
    $ if test -z "$str2" || test "$str2" == "$str1"; then echo "YES"; else echo "NO"; fi
    YES

<span id="toc_17877_9500_23"></span>
#### 範例：測試某個數字不滿足指定的所有條件

    $ i=5
    $ if test ! $i -lt 5 -a $i -ne 6; then echo "YES"; else echo "NO"; fi
    YES
    $ if ! test $i -lt 5 -a $i -eq 6; then echo "YES"; else echo "NO"; fi
    YES

很容易找出它們的區別，`-a` 和 `-o` 作為測試命令的參數用在測試命令的內部，而 `&&` 和 `||` 則用來運算測試的返回值，`!` 為兩者通用。需要關注的是：

-  有時可以不用 `!` 運算符，比如 `-eq` 和 `-ne` 剛好相反，可用於測試兩個數值是否相等； `-z` 與 `-n` 也是對應的，用來測試某個字符串是否為空
-  在 `Bash` 裡，`test` 命令可以用[] 運算符取代，但是需要注意，[` 之後與 `] 之前需要加上額外的空格
-  在測試字符串時，所有變量建議用雙引號包含起來，以防止變量內容為空時出現僅有測試參數，沒有測試內容的情況

下面我們用實例來演示上面三個注意事項：

-   `-ne` 和 `-eq` 對應的，我們有時候可以免去 `!` 運算

        $ i=5
        $ if test $i -eq 5; then echo "YES"; else echo "NO"; fi
        YES
        $ if test $i -ne 5; then echo "YES"; else echo "NO"; fi
        NO
        $ if test ! $i -eq 5; then echo "YES"; else echo "NO"; fi
        NO

-   用 `[ ]` 可以取代 `test`，這樣看上去會“美觀”很多

        $ if [ $i -eq 5 ]; then echo "YES"; else echo "NO"; fi
        YES
        $ if [ $i -gt 4 ] && [ $i -lt 6 ]; then echo "YES"; else echo "NO"; fi
        YES

-   記得給一些字符串變量加上 `""`，記得 `[` 之後與 `]` 之前多加一個空格

        $ str=""
        $ if [ "$str" = "test"]; then echo "YES"; else echo "NO"; fi
        -bash: [: missing `]'
        NO
        $ if [ $str = "test" ]; then echo "YES"; else echo "NO"; fi
        -bash: [: =: unary operator expected
        NO
        $ if [ "$str" = "test" ]; then echo "YES"; else echo "NO"; fi
        NO

到這裡，**條件測試**就介紹完了，下面介紹**命令列表**，實際上在上面我們已經使用過了，即多個test命令的組合，通過 `&&`，`||` 和 `!` 組合起來的命令序列。這種命令序列可以有效替換 `if/then` 的條件分支結構。這不難想到我們在 C 語言程序設計中經常做的如下的選擇題（很無聊的例子，但是有意義）：下面是否會打印 `j`，如果打印，將打印什麼？

    #include <stdio.h>
    int main()
    {
    	int i, j;

    	i=5;j=1;
    	if ((i==5) && (j=5))  printf("%d\n", j);

    	return 0;
    }

很容易知道將打印數字 5，因為 `i==5` 這個條件成立，而且隨後是 `&&`，要判斷整個條件是否成立，我們得進行後面的判斷，可是這個判斷並非常規的判斷，而是先把 `j` 修改為 5，再轉換為真值，所以條件為真，打印出 5 。因此，這句可以解釋為：如果 `i` 等於 5，那麼把 `j` 賦值為 5，如果 `j` 大於 1 （因為之前已經為真），那麼打印出 `j` 的值。這樣用 `&&` 連結起來的判斷語句替代了兩個 `if` 條件分支語句。

正是基於邏輯運算特有的性質，我們可以通過 `&&`，`||` 來取代 `if/then` 等條件分支結構，這樣就產生了命令列表。

<span id="toc_17877_9500_24"></span>
## 命令列表

<span id="toc_17877_9500_25"></span>
### 命令列表的執行規律

命令列表的執行規律符合邏輯運算的運算規律，用 `&&` 連接起來的命令，如果前者成功返回，將執行後面的命令，反之不然；用 `||` 連接起來的命令，如果前者成功返回，將不執行後續命令，反之不然。

<span id="toc_17877_9500_26"></span>
#### 範例：如果 ping 通 www.lzu.edu.cn，那麼打印連通信息

    $ ping -c 1 www.lzu.edu.cn -W 1 && echo "=======connected======="

非常有趣的問題出來了，即我們上面已經提到的：為什麼要讓 C 程序在 `main()` 函數的最後返回 0 ？如果不這樣，把這種程序放入命令列表會有什麼樣的結果？你自己寫個簡單的 C 程序，然後放入命令列表看看。

<span id="toc_17877_9500_27"></span>
### 命令列表的作用

有時用命令列表取代 `if/then` 等條件分支結構可以省掉一些代碼，而且使得程序比較美觀、易讀，例如：

<span id="toc_17877_9500_28"></span>
#### 範例：在腳本里判斷程序的參數個數，和參數類型

    #!/bin/bash

    echo $#
    echo $1
    if [ $# -eq 1 ] && (echo $1 | grep ^[0-9]*$ >/dev/null);then
    	echo "YES"
    fi

說明：上例要求參數個數為 1 並且類型為數字。

再加上 `exit 1`，我們將省掉 `if/then` 結構

    #!/bin/bash

    echo $#
    echo $1
    ! ([ $# -eq 1 ] && (echo $1 | grep ^[0-9]*$ >/dev/null)) && exit 1

    echo "YES"

這樣處理後，對程序參數的判斷僅僅需要簡單的一行代碼，而且變得更美觀。

<span id="toc_17877_9500_29"></span>
## 小結

這一節介紹了 Shell 編程中的邏輯運算，條件測試和命令列表。
