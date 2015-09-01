# 數值運算

-    [前言](#toc_22467_18587_1)
-    [整數運算](#toc_22467_18587_2)
    -    [範例：對某個數加 1](#toc_22467_18587_3)
    -    [範例：從 1 加到某個數](#toc_22467_18587_4)
    -    [範例：求模](#toc_22467_18587_5)
    -    [範例：求冪](#toc_22467_18587_6)
    -    [範例：進制轉換](#toc_22467_18587_7)
    -    [範例：ascii 字符編碼](#toc_22467_18587_8)
-    [浮點運算](#toc_22467_18587_9)
    -    [範例：求 1 除以 13，保留 3 位有效數字](#toc_22467_18587_10)
    -    [範例：餘弦值轉角度](#toc_22467_18587_11)
    -    [範例：有一組數據，求人均月收入最高家庭](#toc_22467_18587_12)
-    [隨機數](#toc_22467_18587_13)
    -    [範例：獲取一個隨機數](#toc_22467_18587_14)
    -    [範例：隨機產生一個從 0 到 255 之間的數字](#toc_22467_18587_15)
-    [其他運算](#toc_22467_18587_16)
    -    [範例：獲取一系列數](#toc_22467_18587_17)
    -    [範例：統計字符串中各單詞出現次數](#toc_22467_18587_18)
    -    [範例：統計指定單詞出現次數](#toc_22467_18587_19)
-    [小結](#toc_22467_18587_20)
-    [資料](#toc_22467_18587_21)
-    [後記](#toc_22467_18587_22)


<span id="toc_22467_18587_1"></span>
## 前言

從本文開始，打算結合平時積累和進一步實踐，通過一些範例來介紹Shell編程。因為範例往往能夠給人以學有所用的感覺，而且給人以動手實踐的機會，從而激發人的學習熱情。

考慮到易讀性，這些範例將非常簡單，但是實用，希望它們能夠成為我們解決日常問題的參照物或者是“茶餘飯後”的小點心，當然這些“點心”肯定還有值得探討、優化的地方。

更復雜有趣的例子請參考 [Advanced Bash-Scripting Guide][2] (一本深入學習 Shell 腳本藝術的書籍)。

 [2]: http://www.tldp.org/LDP/abs/html/

該系列概要：

* 目的：享受用 Shell 解決問題的樂趣；和朋友們一起交流和探討。
* 計劃：先零散地寫些東西，之後再不斷補充，最後整理成冊。
* 讀者：熟悉 Linux 基本知識，如文件系統結構、常用命令行工具、Shell 編程基礎等。
* 建議：看範例時，可參考[《Shell基礎十二篇》][3]和[《Shell十三問》][4]。
* 環境：如沒特別說明，該系列使用的 Shell 將特指 Bash，版本在 3.1.17 以上。
* 說明：該系列不是依據 Shell 語法組織，而是面向某些潛在的操作對象和操作本身，它們反應了現實應用。當然，在這個過程中肯定會涉及到 Shell 的語法。

 [3]: http://bbs.chinaunix.net/forum.php?mod=viewthread&tid=2198159
 [4]: http://bbs.chinaunix.net/thread-218853-1-1.html
 [5]: http://bbs.chinaunix.net/forum.php?mod=forumdisplay&fid=24&page=1

這一篇打算討論一下 Shell 編程中的基本數值運算，這類運算包括：

* 數值（包括整數和浮點數）間的加、減、乘、除、求冪、求模等
* 產生指定範圍的隨機數
* 產生指定範圍的數列

Shell 本身可以做整數運算，複雜一些的運算要通過外部命令實現，比如 `expr`，`bc`，`awk` 等。另外，可通過 `RANDOM` 環境變量產生一個從 0 到 32767 的隨機數，一些外部工具，比如 `awk` 可以通過 `rand()` 函數產生隨機數。而 `seq` 命令可以用來產生一個數列。下面對它們分別進行介紹。

<span id="toc_22467_18587_2"></span>
## 整數運算

<span id="toc_22467_18587_3"></span>
### 範例：對某個數加 1

```
$ i=0;
$ ((i++))
$ echo $i
1

$ let i++
$ echo $i
2

$ expr $i + 1
3
$ echo $i
2

$ echo $i 1 | awk '{printf $1+$2}'
3
```

說明： `expr` 之後的 `$i`，`+`，1 之間有空格分開。如果進行乘法運算，需要對運算符進行轉義，否則 Shell 會把乘號解釋為通配符，導致語法錯誤； `awk` 後面的 `$1` 和 `$2` 分別指 `$i` 和 1，即從左往右的第 1 個和第 2 個數。

用 Shell 的內置命令查看各個命令的類型如下：

```
$ type type
type is a shell builtin
$ type let
let is a shell builtin
$ type expr
expr is hashed (/usr/bin/expr)
$ type bc
bc is hashed (/usr/bin/bc)
$ type awk
awk is /usr/bin/awk
```

從上述演示可看出： `let` 是 Shell 內置命令，其他幾個是外部命令，都在 `/usr/bin` 目錄下。而 `expr` 和 `bc` 因為剛用過，已經加載在內存的 `hash` 表中。這將有利於我們理解在上一章介紹的腳本多種執行方法背後的原理。

說明：如果要查看不同命令的幫助，對於 `let` 和 `type` 等 Shell 內置命令，可以通過 Shell 的一個內置命令 `help` 來查看相關幫助，而一些外部命令可以通過 Shell 的一個外部命令 `man` 來查看幫助，用法諸如 `help let`，`man expr` 等。

<span id="toc_22467_18587_4"></span>
### 範例：從 1 加到某個數

```
#!/bin/bash
# calc.sh

i=0;
while [ $i -lt 10000 ]
do
	((i++))
done
echo $i
```

說明：這裡通過 `while [ 條件表達式 ]; do .... done` 循環來實現。`-lt` 是小於號 `<`，具體見 `test` 命令的用法：`man test`。

如何執行該腳本？

辦法一：直接把腳本文件當成子 Shell （Bash）的一個參數傳入

```
$ bash calc.sh
$ type bash
bash is hashed (/bin/bash)
```

辦法二：是通過 `bash` 的內置命令 `.` 或 `source` 執行

```
$ . ./calc.sh
```

或

```
$ source ./calc.sh
$ type .
. is a shell builtin
$ type source
source is a shell builtin
```

辦法三：是修改文件為可執行，直接在當前 Shell 下執行

```
$ chmod ./calc.sh
$ ./calc.sh
```

下面，逐一演示用其他方法計算變量加一，即把 `((i++))` 行替換成下面的某一個：

```
let i++;

i=$(expr $i + 1)

i=$(echo $i+1|bc)

i=$(echo "$i 1" | awk '{printf $1+$2;}')
```

比較計算時間如下：

```
$ time calc.sh
10000

real    0m1.319s
user    0m1.056s
sys     0m0.036s
$ time calc_let.sh
10000

real    0m1.426s
user    0m1.176s
sys     0m0.032s
$  time calc_expr.sh
1000

real    0m27.425s
user    0m5.060s
sys     0m14.177s
$ time calc_bc.sh
1000

real    0m56.576s
user    0m9.353s
sys     0m24.618s
$ time ./calc_awk.sh
100

real    0m11.672s
user    0m2.604s
sys     0m2.660s
```

說明： `time` 命令可以用來統計命令執行時間，這部分時間包括總的運行時間，用戶空間執行時間，內核空間執行時間，它通過 `ptrace` 系統調用實現。

通過上述比較可以發現 `(())` 的運算效率最高。而 `let` 作為 Shell 內置命令，效率也很高，但是 `expr`，`bc`，`awk` 的計算效率就比較低。所以，在 Shell 本身能夠完成相關工作的情況下，建議優先使用 Shell 本身提供的功能。但是 Shell 本身無法完成的功能，比如浮點運算，所以就需要外部命令的幫助。另外，考慮到 Shell 腳本的可移植性，在性能不是很關鍵的情況下，不要使用某些 Shell 特有的語法。

`let`，`expr`，`bc` 都可以用來求模，運算符都是 `%`，而 `let` 和 `bc` 可以用來求冪，運算符不一樣，前者是 `**`，後者是 `^` 。例如：

<span id="toc_22467_18587_5"></span>
### 範例：求模

```
$ expr 5 % 2
1

$ let i=5%2
$ echo $i
1

$ echo 5 % 2 | bc
1

$ ((i=5%2))
$ echo $i
1
```

<span id="toc_22467_18587_6"></span>
### 範例：求冪

```
$ let i=5**2
$ echo $i
25

$ ((i=5**2))
$ echo $i

25
$ echo "5^2" | bc
25
```

<span id="toc_22467_18587_7"></span>
### 範例：進制轉換

進制轉換也是比較常用的操作，可以用 `Bash` 的內置支持也可以用 `bc` 來完成，例如把 8 進制的 11 轉換為 10 進制，則可以：

```
$ echo "obase=10;ibase=8;11" | bc -l
9

$ echo $((8#11))
9
```

上面都是把某個進制的數轉換為 10 進制的，如果要進行任意進制之間的轉換還是 `bc` 比較靈活，因為它可以直接用 `ibase` 和 `obase` 分別指定進制源和進制轉換目標。

<span id="toc_22467_18587_8"></span>
### 範例：ascii 字符編碼

如果要把某些字符串以特定的進製表示，可以用 `od` 命令，例如默認的分隔符 `IFS` 包括空格、 `TAB` 以及換行，可以用 `man ascii` 佐證。

```
$ echo -n "$IFS" | od -c
0000000      t  n
0000003
$ echo -n "$IFS" | od -b
0000000 040 011 012
0000003
```

<span id="toc_22467_18587_9"></span>
## 浮點運算

`let` 和 `expr` 都無法進行浮點運算，但是 `bc` 和 `awk` 可以。

<span id="toc_22467_18587_10"></span>
### 範例：求 1 除以 13，保留 3 位有效數字

```
$ echo "scale=3; 1/13"  | bc
.076

$ echo "1 13" | awk '{printf("%0.3fn",$1/$2)}'
0.077
```

說明： `bc` 在進行浮點運算時需指定精度，否則默認為 0，即進行浮點運算時，默認結果只保留整數。而 `awk` 在控制小數位數時非常靈活，僅僅通過 `printf` 的格式控制就可以實現。

補充：在用 `bc` 進行運算時，如果不用 `scale` 指定精度，而在 `bc` 後加上 `-l` 選項，也可以進行浮點運算，只不過這時的默認精度是 20 位。例如：

```
$ echo 1/13100 | bc -l
.00007633587786259541
```

<span id="toc_22467_18587_11"></span>
### 範例：餘弦值轉角度

用 `bc -l` 計算，可以獲得高精度：

```
$ export cos=0.996293; echo "scale=100; a(sqrt(1-$cos^2)/$cos)*180/(a(1)*4)" | bc -l
4.934954755411383632719834036931840605159706398655243875372764917732
5495504159766011527078286004072131
```

當然也可以用 `awk` 來計算：

```
$ echo 0.996293 | awk '{ printf("%s\n", atan2(sqrt(1-$1^2),$1)*180/3.1415926535);}'
4.93495
```

<span id="toc_22467_18587_12"></span>
### 範例：有一組數據，求人均月收入最高家庭

在這裡隨機產生了一組測試數據，文件名為 `income.txt`。

```
1 3 4490
2 5 3896
3 4 3112
4 4 4716
5 4 4578
6 6 5399
7 3 5089
8 6 3029
9 4 6195
10 5 5145
```

說明：上面的三列數據分別是家庭編號、家庭人數、家庭月總收入。

分析：為了求月均收入最高家庭，需要對後面兩列數進行除法運算，即求出每個家庭的月均收入，然後按照月均收入排序，找出收入最高家庭。

實現：

```
#!/bin/bash
# gettopfamily.sh

[ $# -lt 1 ] && echo "please input the income file" && exit -1
[ ! -f $1 ] && echo "$1 is not a file" && exit -1

income=$1
awk '{
	printf("%d %0.2fn", $1, $3/$2);
}' $income | sort -k 2 -n -r
```

說明：

* `[ $# -lt 1 ]`：要求至少輸入一個參數，`$#` 是 Shell 中傳入參數的個數
* `[ ! -f $1 ]`：要求輸入參數是一個文件，`-f` 的用法見 `test` 命令，`man test`
* `income=$1`：把輸入參數賦給 income 變量，再作為 `awk` 的參數，即需處理的文件
* `awk`：用文件第三列除以第二列，求出月均收入，考慮到精確性，保留了兩位精度
* `sort -k 2 -n -r`：這裡對結果的 `awk` 結果的第二列 `-k 2`，即月均收入進行排序，按照數字排序 `-n`，並按照遞減的順序排序 `-r`。

演示：

```
$ ./gettopfamily.sh income.txt
7 1696.33
9 1548.75
1 1496.67
4 1179.00
5 1144.50
10 1029.00
6 899.83
2 779.20
3 778.00
8 504.83
```

補充：之前的 `income.txt` 數據是隨機產生的。在做一些實驗時，往往需要隨機產生一些數據，在下一小節，我們將詳細介紹它。這裡是產生 `income.txt` 數據的腳本：

```
#!/bin/bash
# genrandomdata.sh

for i in $(seq 1 10)
do
	echo $i $(($RANDOM/8192+3)) $((RANDOM/10+3000))
done
```

說明：上述腳本中還用到`seq`命令產生從1到10的一列數，這個命令的詳細用法在該篇最後一節也會進一步介紹。

<span id="toc_22467_18587_13"></span>
## 隨機數

環境變量 `RANDOM` 產生從 0 到 32767 的隨機數，而 `awk` 的 `rand()` 函數可以產生 0 到 1 之間的隨機數。

<span id="toc_22467_18587_14"></span>
### 範例：獲取一個隨機數

```
$ echo $RANDOM
81

$ echo "" | awk '{srand(); printf("%f", rand());}'
0.237788
```

說明： `srand()` 在無參數時，採用當前時間作為 `rand()` 隨機數產生器的一個 `seed` 。

<span id="toc_22467_18587_15"></span>
### 範例：隨機產生一個從 0 到 255 之間的數字

可以通過 `RANDOM` 變量的縮放和 `awk` 中 `rand()` 的放大來實現。

```
$ expr $RANDOM / 128

$ echo "" | awk '{srand(); printf("%d\n", rand()*255);}'
```

思考：如果要隨機產生某個 IP 段的 IP 地址，該如何做呢？看例子：友善地獲取一個可用的 IP 地址。

```
#!/bin/bash
# getip.sh -- get an usable ipaddress automatically
# author: falcon &lt;zhangjinw@gmail.com>
# update: Tue Oct 30 23:46:17 CST 2007

# set your own network, default gateway, and the time out of "ping" command
net="192.168.1"
default_gateway="192.168.1.1"
over_time=2

# check the current ipaddress
ping -c 1 $default_gateway -W $over_time
[ $? -eq 0 ] && echo "the current ipaddress is okey!" && exit -1;

while :; do
	# clear the current configuration
	ifconfig eth0 down
	# configure the ip address of the eth0
	ifconfig eth0 \
		$net.$(($RANDOM /130 +2)) \
		up
	# configure the default gateway
	route add default gw $default_gateway
	# check the new configuration
	ping -c 1 $default_gateway -W $over_time
	# if work, finish
	[ $? -eq 0 ] && break
done
```

說明：如果你的默認網關地址不是 `192.168.1.1`，請自行配置 `default_gateway`（可以用 `route -n` 命令查看），因為用 `ifconfig` 配置地址時不能配置為網關地址，否則你的IP地址將和網關一樣，導致整個網絡不能正常工作。

<span id="toc_22467_18587_16"></span>
## 其他運算

其實通過一個循環就可以產生一系列數，但是有相關工具為什麼不用呢！`seq` 就是這麼一個小工具，它可以產生一系列數，你可以指定數的遞增間隔，也可以指定相鄰兩個數之間的分割符。

<span id="toc_22467_18587_17"></span>
### 範例：獲取一系列數

```
$ seq 5
1
2
3
4
5
$ seq 1 5
1
2
3
4
5
$ seq 1 2 5
1
3
5
$ seq -s: 1 2 5
1:3:5
$ seq 1 2 14
1
3
5
7
9
11
13
$ seq -w 1 2 14
01
03
05
07
09
11
13
$ seq -s: -w 1 2 14
01:03:05:07:09:11:13
$ seq -f "0x%g" 1 5
0x1
0x2
0x3
0x4
0x5
```

一個比較典型的使用 `seq` 的例子，構造一些特定格式的鏈接，然後用 `wget` 下載這些內容：

```
$ for i in `seq -f"http://thns.tsinghua.edu.cn/thnsebooks/ebook73/%02g.pdf" 1 21`;do wget -c $i; done
```

或者

```
$ for i in `seq -w 1 21`;do wget -c "http://thns.tsinghua.edu.cn/thnsebooks/ebook73/$i"; done
```

補充：在 `Bash` 版本 3 以上，在 `for` 循環的 `in` 後面，可以直接通過 `{1..5}` 更簡潔地產生自 1 到 5 的數字（注意，1 和 5 之間只有兩個點），例如：

```
$ for i in {1..5}; do echo -n "$i "; done
1 2 3 4 5
```

<span id="toc_22467_18587_18"></span>
### 範例：統計字符串中各單詞出現次數

我們先給單詞一個定義：由字母組成的單個或者多個字符系列。

首先，統計每個單詞出現的次數：

```
$ wget -c http://tinylab.org
$ cat index.html | sed -e "s/[^a-zA-Z]/\n/g" | grep -v ^$ | sort | uniq -c
```

接著，統計出現頻率最高的前10個單詞：

```
$ wget -c http://tinylab.org
$ cat index.html | sed -e "s/[^a-zA-Z]/\n/g" | grep -v ^$ | sort | uniq -c | sort -n -k 1 -r | head -10
    524 a
    238 tag
    205 href
    201 class
    193 http
    189 org
    175 tinylab
    174 www
    146 div
    128 title
```

說明：

* `cat index.html`: 輸出 index.html 文件裡的內容
* `sed -e "s/[^a-zA-Z]/\n/g"`: 把非字母字符替換成空格，只保留字母字符
* `grep -v ^$`: 去掉空行
* `sort`: 排序
* `uniq -c`：統計相同行的個數，即每個單詞的個數
* `sort -n -k 1 -r`：按照第一列 `-k 1` 的數字 `-n` 逆序 `-r` 排序
* `head -10`：取出前十行

<span id="toc_22467_18587_19"></span>
### 範例：統計指定單詞出現次數

可以考慮採取兩種辦法：

* 只統計那些需要統計的單詞
* 用上面的算法把所有單詞的個數都統計出來，然後再返回那些需要統計的單詞給用戶

不過，這兩種辦法都可以通過下面的結構來實現。先看辦法一：

```
#!/bin/bash
# statistic_words.sh

if [ $# -lt 1 ]; then
	echo "Usage: basename $0 FILE WORDS ...."
	exit -1
fi

FILE=$1
((WORDS_NUM=$#-1))

for n in $(seq $WORDS_NUM)
do
	shift
	cat $FILE | sed -e "s/[^a-zA-Z]/\n/g" \
		| grep -v ^$ | sort | grep ^$1$ | uniq -c
done
```

說明：

* `if 條件部分`：要求至少兩個參數，第一個單詞文件，之後參數為要統計的單詞
* `FILE=$1`: 獲取文件名，即腳本之後的第一個字符串
* `((WORDS_NUM=$#-1))`：獲取單詞個數，即總的參數個數 `$#` 減去文件名參數（1個）
* `for 循環部分`：首先通過 `seq` 產生需要統計的單詞個數系列，`shift` 是 Shell 內置變量（請通過 `help shift` 獲取幫助)，它把用戶從命令行中傳入的參數依次往後移動位置，並把當前參數作為第一個參數即 `$1`，這樣通過 `$1`就可以遍歷用戶所有輸入的單詞（仔細一想，這裡貌似有數組下標的味道）。你可以考慮把 `shift` 之後的那句替換成 `echo $1` 測試 `shift` 的用法

演示：

```
$ chmod +x statistic_words.sh
$ ./statistic_words.sh index.html tinylab linux python
    175 tinylab
     43 linux
      3 python
```

再看辦法二，我們只需要修改 `shift` 之後的那句即可：

```
#!/bin/bash
# statistic_words.sh

if [ $# -lt 1 ]; then
	echo "ERROR: you should input 2 words at least";
	echo "Usage: basename $0 FILE WORDS ...."
	exit -1
fi

FILE=$1
((WORDS_NUM=$#-1))

for n in $(seq $WORDS_NUM)
do
	shift
	cat $FILE | sed -e "s/[^a-zA-Z]/\n/g" \
		| grep -v ^$ | sort | uniq -c | grep " $1$"
done
```

演示：

```
$ ./statistic_words.sh index.html tinylab linux python
    175 tinylab
     43 linux
      3 python
```

說明：很明顯，辦法一的效率要高很多，因為它提前找出了需要統計的單詞，然後再統計，而後者則不然。實際上，如果使用 `grep` 的 `-E` 選項，我們無須引入循環，而用一條命令就可以搞定：

```
$ cat index.html | sed -e "s/[^a-zA-Z]/\n/g" | grep -v ^$ | sort | grep -E "^tinylab$|^linux$" | uniq -c
     43 linux
    175 tinylab
```

或者

```
$ cat index.html | sed -e "s/[^a-zA-Z]/\n/g" | grep -v ^$ | sort | egrep  "^tinylab$|^linux$" | uniq -c
     43 linux
    175 tinylab
```

說明：需要注意到 `sed` 命令可以直接處理文件，而無需通過 `cat` 命令輸出以後再通過管道傳遞，這樣可以減少一個不必要的管道操作，所以上述命令可以簡化為：

```
$ sed -e "s/[^a-zA-Z]/\n/g" index.html | grep -v ^$ | sort | egrep  "^tinylab$|^linux$" | uniq -c
     43 linux
    175 tinylab
```

所以，可見這些命令 `sed`，`grep`，`uniq`，`sort` 是多麼有用，它們本身雖然只完成簡單的功能，但是通過一定的組合，就可以實現各種五花八門的事情啦。對了，統計單詞還有個非常有用的命令 `wc -w`，需要用到的時候也可以用它。

補充：在 [Advanced Bash-Scripting Guide][2] 一書中還提到 `jot` 命令和 `factor` 命令，由於機器上沒有，所以沒有測試，`factor` 命令可以產生某個數的所有素數。如：

```
$ factor 100
100: 2 2 5 5
```


<span id="toc_22467_18587_20"></span>
## 小結

到這裡，Shell 編程範例之數值計算就結束啦。該篇主要介紹了：

* Shell 編程中的整數運算、浮點運算、隨機數的產生、數列的產生
* Shell 的內置命令、外部命令的區別，以及如何查看他們的類型和幫助
* Shell 腳本的幾種執行辦法
* 幾個常用的 Shell 外部命令： `sed`，`awk`，`grep`，`uniq`，`sort` 等
* 範例：數字遞增；求月均收入；自動獲取 `IP` 地址；統計單詞個數
* 其他：相關用法如命令列表，條件測試等在上述範例中都已涉及，請認真閱讀之

如果您有時間，請溫習之。

<span id="toc_22467_18587_21"></span>
## 資料

* [Advanced Bash-Scripting Guide][2]
* [shell 十三問][4]
* [shell 基礎十二篇][3]
* SED 手冊
* AWK 使用手冊
* 幾個 Shell 討論區
    * [LinuxSir.org][6]
    * [ChinaUnix.net][7]

 [6]: http://www.linuxsir.org/bbs/forumdisplay.php?f=60
 [7]: http://bbs.chinaunix.net/forum-24-1.html

<span id="toc_22467_18587_22"></span>
## 後記

大概花了 3 個多小時才寫完，目前是 23:33，該回宿舍睡覺啦，明天起來修改錯別字和補充一些內容，朋友們晚安！

10 月 31 號，修改部分措辭，增加一篇統計家庭月均收入的範例，添加總結和參考資料，並用附錄所有代碼。

Shell 編程是一件非常有趣的事情，如果您想一想：上面計算家庭月均收入的例子，然後和用 `M$ Excel` 來做這個工作比較，你會發現前者是那麼簡單和省事，而且給您以運用自如的感覺。
