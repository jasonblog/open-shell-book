# 文件操作

-    [前言](#toc_11738_32168_1)
-    [文件的各種屬性](#toc_11738_32168_2)
    -    [文件類型](#toc_11738_32168_3)
        -    [範例：在命令行簡單地區分各類文件](#toc_11738_32168_4)
        -    [範例：簡單比較它們的異同](#toc_11738_32168_5)
        -    [範例：普通文件再分類](#toc_11738_32168_6)
    -    [文件屬主](#toc_11738_32168_7)
        -    [範例：修改文件的屬主](#toc_11738_32168_8)
        -    [範例：查看文件的屬主](#toc_11738_32168_9)
        -    [範例：分析文件屬主實現的背後原理](#toc_11738_32168_10)
    -    [文件權限](#toc_11738_32168_11)
        -    [範例：給文件添加讀、寫、可執行權限](#toc_11738_32168_12)
        -    [範例：授權普通用戶執行root所屬命令](#toc_11738_32168_13)
        -    [範例：給重要文件加鎖](#toc_11738_32168_14)
    -    [文件大小](#toc_11738_32168_15)
        -    [範例：查看普通文件和鏈接文件](#toc_11738_32168_16)
        -    [範例：查看設備文件](#toc_11738_32168_17)
        -    [範例：查看目錄](#toc_11738_32168_18)
    -    [文件訪問、更新、修改時間](#toc_11738_32168_19)
    -    [文件名](#toc_11738_32168_20)
-    [文件的基本操作](#toc_11738_32168_21)
    -    [範例：創建文件](#toc_11738_32168_22)
    -    [範例：刪除文件](#toc_11738_32168_23)
    -    [範例：複製文件](#toc_11738_32168_24)
    -    [範例：修改文件名](#toc_11738_32168_25)
    -    [範例：編輯文件](#toc_11738_32168_26)
    -    [範例：壓縮／解壓縮文件](#toc_11738_32168_27)
    -    [範例：文件搜索（文件定位）](#toc_11738_32168_28)
-    [參考資料](#toc_11738_32168_29)
-    [後記](#toc_11738_32168_30)


<span id="toc_11738_32168_1"></span>
## 前言

這周來探討文件操作。

在日常學習和工作中，總是在不斷地和各種文件打交道，這些文件包括普通文本文件，可以執行的程序，帶有控制字符的文檔、存放各種文件的目錄、網絡套接字文件、設備文件等。這些文件又具有諸如屬主、大小、創建和修改日期等各種屬性。文件對應文件系統的一些數據塊，對應磁盤等存儲設備的一片連續空間，對應於顯示設備卻是一些具有不同形狀的字符集。

在這一節，為了把關注點定位在文件本身，不會深入探討文件系統以及存儲設備是如何組織文件的（在後續章節再深入探討），而是探討對它最熟悉的一面，即把文件當成是一系列的字符（一個 `byte`）集合看待。因此之前介紹的[《 Shell 編程範例之字符串操作》][100]在這裡將會得到廣泛的應用，關於普通文件的讀寫操作已經非常熟練，那就是“重定向”，這裡會把這部分獨立出來介紹。關於文件在 Linux 下的“數字化”（文件描述符）高度抽象，“一切皆為文件”的哲學在 Shell 編程裡也得到了深刻的體現。

[100]: 01-chapter4.markdown

下面先來介紹文件的各種屬性，然後介紹普通文件的一般操作。

<span id="toc_11738_32168_2"></span>
## 文件的各種屬性

首先通過文件的結構體來看看文件到底有哪些屬性：

    struct stat {
	    dev_t st_dev; /* 設備   */
	    ino_t st_ino; /* 節點   */
	    mode_t st_mode; /* 模式   */
	    nlink_t st_nlink; /* 硬連接 */
	    uid_t st_uid; /* 用戶ID */
	    gid_t st_gid; /* 組ID   */
	    dev_t st_rdev; /* 設備類型 */
	    off_t st_off; /* 文件字節數 */
	    unsigned long  st_blksize; /* 塊大小 */
	    unsigned long st_blocks; /* 塊數   */
	    time_t st_atime; /* 最後一次訪問時間 */
	    time_t st_mtime; /* 最後一次修改時間 */
	    time_t st_ctime; /* 最後一次改變時間(指屬性) */
    };

下面逐次來了解這些屬性，如果需要查看某個文件屬性，用 `stat` 命令就可，它會按照上面的結構體把信息列出來。另外，`ls` 命令在跟上一定參數後也可以顯示文件的相關屬性，比如 `-l` 參數。

<span id="toc_11738_32168_3"></span>
### 文件類型

文件類型對應於上面的 `st_mode`, 文件類型有很多，比如常規文件、符號鏈接（硬鏈接、軟鏈接）、管道文件、設備文件(符號設備、塊設備)、socket文件等，不同的文件類型對應不同的功能和作用。

<span id="toc_11738_32168_4"></span>
#### 範例：在命令行簡單地區分各類文件

    $ ls -l
    total 12
    drwxr-xr-x 2 root root 4096 2007-12-07 20:08 directory_file
    prw-r--r-- 1 root root    0 2007-12-07 20:18 fifo_pipe
    brw-r--r-- 1 root root 3, 1 2007-12-07 21:44 hda1_block_dev_file
    crw-r--r-- 1 root root 1, 3 2007-12-07 21:43 null_char_dev_file
    -rw-r--r-- 2 root root  506 2007-12-07 21:55 regular_file
    -rw-r--r-- 2 root root  506 2007-12-07 21:55 regular_file_hard_link
    lrwxrwxrwx 1 root root   12 2007-12-07 20:15 regular_file_soft_link -> regular_file
    $ stat directory_file/
      File: `directory_file/'
      Size: 4096            Blocks: 8          IO Block: 4096   directory
    Device: 301h/769d       Inode: 521521      Links: 2
    Access: (0755/drwxr-xr-x)  Uid: (    0/    root)   Gid: (    0/    root)
    Access: 2007-12-07 20:08:18.000000000 +0800
    Modify: 2007-12-07 20:08:18.000000000 +0800
    Change: 2007-12-07 20:08:18.000000000 +0800
    $ stat null_char_dev_file
      File: `null_char_dev_file'
      Size: 0               Blocks: 0          IO Block: 4096   character special file
    Device: 301h/769d       Inode: 521240      Links: 1     Device type: 1,3
    Access: (0644/crw-r--r--)  Uid: (    0/    root)   Gid: (    0/    root)
    Access: 2007-12-07 21:43:38.000000000 +0800
    Modify: 2007-12-07 21:43:38.000000000 +0800
    Change: 2007-12-07 21:43:38.000000000 +0800

說明：通過 `ls` 命令結果每行的第一個字符可以看到，它們之間都不相同，這正好反應了不同文件的類型。 `d` 表示目錄，`-` 表示普通文件（或者硬鏈接），`l` 表示符號鏈接，`p` 表示管道文件，`b` 和 `c` 分別表示塊設備和字符設備（另外 `s` 表示 `socket` 文件）。在 `stat` 命令的結果中，可以在第二行的最後找到說明，從上面的操作可以看出，`directory_file` 是目錄，`stat` 命令的結果中用 `directory` 表示，而 `null_char_dev_file` 它則用 `character special file` 說明。

<span id="toc_11738_32168_5"></span>
#### 範例：簡單比較它們的異同

通常只會用到目錄、普通文件、以及符號鏈接，很少碰到其他類型的文件，不過這些文件還是各有用處的，如果要做嵌入式開發或者進程通信等，可能會涉及到設備文件、有名管道（FIFO）。下面通過簡單的操作來反應它們之間的區別（具體原理會在下一節[《Shell 編程範例之文件系統》][101]介紹，如果感興趣，也可以提前到網上找找設備文件的作用、塊設備和字符設備的區別、以及驅動程序中如何編寫相關設備驅動等）。

[101]: 01-chapter6.markdown

對於普通文件：就是一系列字符的集合，所以可以讀、寫等

    $ echo "hello, world" > regular_file
    $ cat regular_file
    hello, world

在目錄中可以創建新文件，所以目錄還有叫法：文件夾，到後面會分析目錄文件的結構體，它實際上存放了它下面的各個文件的文件名。

    $ cd directory_file
    $ touch file1 file2 file3

對於有名管道，操作起來比較有意思：如果要讀它，除非有內容，否則阻塞；如果要寫它，除非有人來讀，否則阻塞。它常用於進程通信中。可以打開兩個終端 `terminal1` 和 `terminal2`，試試看：

    terminal1$ cat fifo_pipe #剛開始阻塞在這裡，直到下面的寫動作發生，才打印test字符串
    terminal2$ echo "test" > fifo_pipe

關於塊設備，字符設備，設備文件對應於 `/dev/hda1` 和 `/dev/null`，如果用過 U 盤，或者是寫過簡單的腳本的話，這樣的用法應該用過：
:-)

    $ mount hda1_block_dev_file /mnt #掛載硬盤的第一個分區到/mnt下（關於掛載的原理，在下一節討論）
    $ echo "fewfewfef" > /dev/null   #/dev/null像個黑洞，什麼東西丟進去都消失殆盡

最後兩個文件分別是 `regular_file` 文件的硬鏈接和軟鏈接，去讀寫它們，他們的內容是相同的，不過去刪除它們，他們卻互不相干，硬鏈接和軟鏈接又有何不同呢？前者可以說就是原文件，後者呢只是有那麼一個 `inode`，但沒有實際的存儲空間，建議用 `stat` 命令查看它們之間的區別，包括它們的 `Blocks`，`inode` 等值，也可以考慮用 `diff` 比較它們的大小。

    $ ls regular_file*
    ls regular_file* -l
    -rw-r--r-- 2 root root 204800 2007-12-07 22:30 regular_file
    -rw-r--r-- 2 root root 204800 2007-12-07 22:30 regular_file_hard_link
    lrwxrwxrwx 1 root root     12 2007-12-07 20:15 regular_file_soft_link -> regular_file
    $ rm regular_file      # 刪除原文件
    $ cat regular_file_hard_link   # 硬鏈接還在，而且裡頭的內容還有呢
    fefe
    $ cat regular_file_soft_link
    cat: regular_file_soft_link: No such file or directory

雖然軟鏈接文件本身還在，不過因為它本身不存儲內容，所以讀不到東西，這就是軟鏈接和硬鏈接的區別。

需要注意的是，硬鏈接不可以跨文件系統，而軟鏈接則可以。另外，也不允許給目錄創建硬鏈接。

<span id="toc_11738_32168_6"></span>
#### 範例：普通文件再分類

文件類型從 Linux 文件系統那麼一個級別分了以上那麼多類型，不過普通文件還是可以再分的（根據文件內容的”數據結構“分），比如常見的文本文件，可執行的 `ELF` 文件，`odt` 文檔，`jpg` 圖片格式，`swap` 分區文件，`pdf` 文件。除了文本文件外，它們大多是二進制文件，有特定的結構，因此需要有專門的工具來創建和編輯它們。關於各類文件的格式，可以參考相關文檔標準。不過非常值得深入瞭解 Linux 下可執行的 `ELF` 文件的工作原理，如果有興趣，建議閱讀一下參考資料中和 `ELF` 文件相關部分，這一部分對於嵌入式 Linux 工程師至關重要。

雖然各類普通文件都有專屬的操作工具，但是還是可以直接讀、寫它們，這裡先提到這麼幾個工具，回頭討論細節。

-   `od` ：以八進制或者其他格式“導出”文件內容。
-   `strings` ：讀出文件中的字符（可打印的字符）
-   `gcc`，`gdb`，`readelf`，objdump` 等： `ELF` 文件分析、處理工具（`gcc` 編譯器、 `gdb` 調試器、 `readelf` 分析 ELF 文件，`objdump` 反編譯工具）

再補充一個非常重要的命令，`file`，這個命令用來查看各類文件的屬性。和 `stat` 命令相比，它可以進一步識別普通文件，即 `stat` 命令顯示的 `regular file` 。因為 `regular file` 可以有各種不同的結構，因此在操作系統的支持下得到不同的解釋，執行不同的動作。雖然，Linux 下，文件也會加上特定的後綴以便用戶能夠方便地識別文件的類型，但是 Linux 操作系統根據文件頭識別各類文件，而不是文件後綴，這樣在解釋相應的文件時就更不容易出錯。下面簡單介紹 `file` 命令的用法。

    $ file ./
    ./: directory
    $ file /etc/profile
    /etc/profile: ASCII English text
    $ file /lib/libc-2.5.so
    /lib/libc-2.5.so: ELF 32-bit LSB shared object, Intel 80386, version 1 (SYSV), not stripped
    $ file /bin/test
    /bin/test: ELF 32-bit LSB executable, Intel 80386, version 1 (SYSV), dynamically linked (uses shared libs), stripped
    $ file /dev/hda
    /dev/hda: block special (3/0)
    $ file /dev/console
    /dev/console: character special (5/1)
    $ cp /etc/profile .
    $ tar zcf profile.tar.gz profile
    $ file profile.tar.gz
    profile.tar.gz: gzip compressed data, from Unix, last modified: Tue Jan  4 18:53:53 2000
    $ mkfifo fifo_test
    $ file fifo_test
    fifo_test: fifo (named pipe)

更多用法見 `file` 命令的手冊，關於 `file` 命令的實現原理，請參考 `magic` 的手冊（看看 `/etc/file/magic` 文件，瞭解什麼是文件的 `magic number` 等）。

<span id="toc_11738_32168_7"></span>
### 文件屬主

Linux 作為一個多用戶系統，為多用戶使用同一個系統提供了極大的方便，比如對於系統上的文件，它通過屬主來區分不同的用戶，以便分配它們對不同文件的操作權限。為了更方便地管理，文件屬主包括該文件所屬用戶，以及該文件所屬的用戶組，因為用戶可以屬於多個組。先來簡單介紹 Linux 下用戶和組的管理。

Linux 下提供了一組命令用於管理用戶和組，比如用於創建用戶的 `useradd` 和 `groupadd`，用於刪除用戶的 `userdel` 和 `groupdel`，另外，`passwd` 命令用於修改用戶密碼。當然，Linux 還提供了兩個相應的配置，即 `/etc/passwd` 和 `/etc/group`，另外，有些系統還把密碼單獨放到了配置文件 `/etc/shadow` 中。關於它們的詳細用法請參考後面的資料，這裡不再介紹，僅介紹文件和用戶之間的一些關係。

<span id="toc_11738_32168_8"></span>
#### 範例：修改文件的屬主

    $ chown 用戶名:組名 文件名

如果要遞歸地修改某個目錄下所有文件的屬主，可以添加 `-R` 選項。

從本節開頭列出的文件結構體中，可以看到僅僅有用戶 `ID` 和組 `ID` 的信息，但 `ls -l` 的結果卻顯示了用戶名和組名信息，這個是怎麼實現的呢？下面先看看 `-n` 的結果：

<span id="toc_11738_32168_9"></span>
#### 範例：查看文件的屬主

    $ ls -n regular_file
    -rw-r--r-- 1 0 0 115 2007-12-07 23:45 regular_file
    $ ls -l regular_file
    -rw-r--r-- 1 root root 115 2007-12-07 23:45 regular_file

<span id="toc_11738_32168_10"></span>
#### 範例：分析文件屬主實現的背後原理

可以看到，`ls -n` 顯示了用戶 `ID` 和組 `ID`，而 `ls -l` 顯示了它們的名字。還記得上面提到的兩個配置文件 `/etc/passwd` 和 `/etc/group` 文件麼？它們分別存放了用戶 `ID` 和用戶名，組 `ID` 和組名的對應關係，因此很容易想到 `ls -l` 命令在實現時是如何通過文件結構體的 `ID` 信息找到它們對應的名字信息的。如果想對 `ls -l` 命令的實現有更進一步的瞭解，可以用 `strace` 跟蹤看看它是否讀取了這兩個配置文件。

    $ strace -f -o strace.log ls -l regular_file
    $ cat strace.log | egrep "passwd|group|shadow"
    2989  open("/etc/passwd", O_RDONLY)     = 3
    2989  open("/etc/group", O_RDONLY)      = 3

說明： `strace` 可以用來跟蹤系統調用和信號。如同 `gdb` 等其他強大的工具一樣，它基於系統的 `ptrace` 系統調用實現。

實際上，把屬主和權限分開介紹不太好，因為只有它們兩者結合才使得多用戶系統成為可能，否則無法隔離不同用戶對某個文件的操作，所以下面來介紹文件操作權限。

<span id="toc_11738_32168_11"></span>
### 文件權限

從 `ls -l` 命令的結果的第一列的後 9 個字符中，可以看到類似這樣的信息 `rwxr-xr-x`，它們對應於文件結構體的 `st_mode` 部分（`st_mode` 包含文件類型信息和文件權限信息兩部分）。這類信息可以分成三部分，即 `rwx`，`r-x`，`r-x`，分別對應該文件所屬用戶、所屬組、其他組對該文件的操作權限，如果有 `rwx` 中任何一個表示可讀、可寫、可執行，如果為 `-` 表示沒有這個權限。對應地，可以用八進制來表示它，比如 `rwxr-xr-x` 就可表示成二進制 111101101，對應的八進制則為 755 。正因為如此，要修改文件的操作權限，也可以有多種方式來實現，它們都可通過 `chmod` 命令來修改。

<span id="toc_11738_32168_12"></span>
#### 範例：給文件添加讀、寫、可執行權限

比如，把 `regular_file` 的文件權限修改為所有用戶都可讀、可寫、可執行，即 `rwxrwxrwx`，也可表示為 111111111，翻譯成八進制，則為 777 。這樣就可以通過兩種方式修改這個權限。

    $ chmod a+rwx regular_file

或

    $ chmod 777 regular_file

說明： `a` 指所用用戶，如果只想給用戶本身可讀可寫可執行權限，那麼可以把 `a` 換成 `u` ；而 `+` 就是添加權限，相反的，如果想去掉某個權限，用 `-`，而 `rwx` 則對應可讀、可寫、可執行。更多用法見 `chmod` 命令的幫助。

實際上除了這些權限外，還有兩個涉及到安全方面的權限，即 `setuid/setgid` 和只讀控制等。

如果設置了文件（程序或者命令）的 `setuid/setgid` 權限，那麼用戶將可用 `root` 身份去執行該文件，因此，這將可能帶來安全隱患；如果設置了文件的只讀權限，那麼用戶將僅僅對該文件將有可讀權限，這為避免諸如 `rm -rf` 的“可惡”操作帶來一定的庇佑。

<span id="toc_11738_32168_13"></span>
#### 範例：授權普通用戶執行root所屬命令

默認情況下，系統是不允許普通用戶執行 `passwd` 命令的，通過 `setuid/setgid`，可以授權普通用戶執行它。

    $ ls -l /usr/bin/passwd
    -rwx--x--x 1 root root 36092 2007-06-19 14:59 /usr/bin/passwd
    $ su      #切換到root用戶，給程序或者命令添加“粘著位”
    $ chmod +s /usr/bin/passwd
    $ ls -l /usr/bin/passwd
    -rws--s--x 1 root root 36092 2007-06-19 14:59 /usr/bin/passwd
    $ exit
    $ passwd #普通用戶通過執行該命令，修改自己的密碼

說明：

> `setuid` 和 `setgid` 位是讓普通用戶可以以 `root` 用戶的角色運行只有 `root` 帳號才能運行的程序或命令。

雖然這在一定程度上為管理提供了方便，比如上面的操作讓普通用戶可以修改自己的帳號，而不是要 `root` 帳號去為每個用戶做這些工作。關於 `setuid/setgid` 的更多詳細解釋，請參考最後推薦的資料。

<span id="toc_11738_32168_14"></span>
#### 範例：給重要文件加鎖

只讀權限示例：給重要文件加鎖（添加不可修改位 [immutable]))，以避免各種誤操作帶來的災難性後果（例如 `:`
`rm -rf`）

    $ chattr +i regular_file
    $ lsattr regular_file
    ----i-------- regular_file
    $ rm regular_file    #加immutable位後就無法對文件進行任何“破壞性”的活動啦
    rm: remove write-protected regular file `regular_file'? y
    rm: cannot remove `regular_file': Operation not permitted
    $ chattr -i regular_file #如果想對它進行常規操作，那麼可以把這個位去掉
    $ rm regular_file

說明： `chattr` 可以用於設置文件的特殊權限，更多用法請參考 `chattr` 的幫助。

<span id="toc_11738_32168_15"></span>
### 文件大小

文件大小對於普通文件而言就是文件內容的大小，而目錄作為一個特殊的文件，它存放的內容是以目錄結構體組織的各類文件信息，所以目錄的大小一般都是固定的，它存放的文件個數自然也就有上限，即它的大小除以文件名的長度。設備文件的“文件大小”則對應設備的主、次設備號，而有名管道文件因為特殊的讀寫性質，所以大小常是 0 。硬鏈接（目錄文件不能創建硬鏈接）實質上是原文件的一個完整的拷貝，因此，它的大小就是原文件的大小。而軟鏈接只是一個 `inode`，存放了一個指向原文件的指針，因此它的大小僅僅是原文件名的字節數。下面我們通過演示增加記憶。

<span id="toc_11738_32168_16"></span>
#### 範例：查看普通文件和鏈接文件

原文件，鏈接文件文件大小的示例：

    $ echo -n "abcde" > regular_file   #往regular_file寫入5字節
    $ ls -l regular_file*
    -rw-r--r-- 2 root root  5 2007-12-08 15:28 regular_file
    -rw-r--r-- 2 root root  5 2007-12-08 15:28 regular_file_hard_file
    lrwxrwxrwx 1 root root 12 2007-12-07 20:15 regular_file_soft_link -> regular_file
    lrwxrwxrwx 1 root root 22 2007-12-08 15:21 regular_file_soft_link_link -> regular_file_soft_link
    $ i="regular_file"
    $ j="regular_file_soft_link"
    $ echo ${#i} ${#j}   #軟鏈接存放的剛好是它們指向的原文件的文件名的字節數
    12 22

<span id="toc_11738_32168_17"></span>
#### 範例：查看設備文件

設備號對應的文件大小：主、次設備號

    $ ls -l hda1_block_dev_file
    brw-r--r-- 1 root root 3, 1 2007-12-07 21:44 hda1_block_dev_file
    $ ls -l null_char_dev_file
    crw-r--r-- 1 root root 1, 3 2007-12-07 21:43 null_char_dev_file

補充：主 `(major)、次 `(minor)設備號的作用有不同。當一個設備文件被打開時，內核會根據主設備號（`major number`）去查找在內核中已經以主設備號註冊的驅動（可以 `cat /proc/devices` 查看已經註冊的驅動號和主設備號的對應情況），而次設備號（`minor number`）則是通過內核傳遞給了驅動本身（參考《The Linux Primer》第十章）。因此，對於內核而言，通過主設備號就可以找到對應的驅動去識別某個設備，而對於驅動而言，為了能夠更復雜地訪問設備，比如訪問設備的不同部分（如硬件通過分區分成不同部分，而出現 `hda1`，`hda2`，`hda3` 等），比如產生不同要求的隨機數（如 `/dev/random` 和 `/dev/urandom` 等）。

<span id="toc_11738_32168_18"></span>
#### 範例：查看目錄

目錄文件的大小，為什麼是這樣呢？看看下面的目錄結構體的大小，目錄文件的 Block 中存放了該目錄下所有文件名的入口。

    $ ls -ld directory_file/
    drwxr-xr-x 2 root root 4096 2007-12-07 23:14 directory_file/

目錄的結構體如下：

    struct dirent {
	    long d_ino;
	    off_t d_off;
	    unsigned short d_reclen;
	    char d_name[NAME_MAX+1]; /* 文件名稱 */
    }

<span id="toc_11738_32168_19"></span>
### 文件訪問、更新、修改時間

文件的時間屬性可以記錄用戶對文件的操作信息，在系統管理、判斷文件版本信息等情況下將為管理員提供參考。因此，在閱讀文件時，建議用 `cat` 等閱讀工具，不要用編輯工具 `vim` 去閱讀，因為即使沒有做任何修改操作，一旦執行了保存命令，將修改文件的時間戳信息。

<span id="toc_11738_32168_20"></span>
### 文件名

文件名並沒有存放在文件結構體內，而是存放在它所在的目錄結構體中。所以，在目錄的同一級別中，文件名必須是唯一的。

<span id="toc_11738_32168_21"></span>
## 文件的基本操作

對於文件，常見的操作包括創建、刪除、修改、讀、寫等。關於各種操作對應的“背後動作”將在下一章[《Shell編程範例之文件系統操作》][101]詳細分析。

<span id="toc_11738_32168_22"></span>
### 範例：創建文件

`socket` 文件是一類特殊的文件，可以通過 C 語言創建，這裡不做介紹（暫時不知道是否可以用命令直接創建），其他文件將通過命令創建。

    $ touch regular_file      #創建普通文件
    $ mkdir directory_file     #創建目錄文件，目錄文件裡頭可以包含更多文件
    $ ln regular_file regular_file_hard_link  #硬鏈接，是原文件的一個完整拷比
    $ ln -s regular_file regular_file_soft_link  #類似一個文件指針，指向原文件
    $ mkfifo fifo_pipe   #或者通過 "mknod fifo_pipe p" 來創建，FIFO滿足先進先出的特點
    $ mknod hda1_block_dev_file b 3 1  #塊設備
    $ mknod null_char_dev_file c 1 3   #字符設備

創建一個文件實際上是在文件系統中添加了一個節點（`inode)，該節點信息將保存到文件系統的節點表中。更形象地說，就是在一顆樹上長了一顆新的葉子（文件）或者枝條（目錄文件，上面還可以長葉子的那種），這些可以通過 `tree` 命令或者 `ls` 命令形象地呈現出來。文件系統從日常使用的角度，完全可以當成一顆倒立的樹來看，因為它們太像了，太容易記憶啦。

    $ tree 當前目錄

或者

    $ ls 當前目錄

<span id="toc_11738_32168_23"></span>
### 範例：刪除文件

刪除文件最直接的印象是這個文件再也不存在了，這同樣可以通過 `ls` 或者 `tree` 命令呈現出來，就像樹木被砍掉一個分支或者摘掉一片葉子一樣。實際上，這些文件刪除之後，並不是立即消失了，而是僅僅做了刪除標記，因此，如果刪除之後，沒有相關的磁盤寫操作把相應的磁盤空間“覆蓋”，那麼原理上是可以恢復的（雖然如此，但是這樣的工作往往很麻煩，所以在刪除一些重要數據時，請務必三思而後行，比如做好備份工作），相應的做法可以參考後續資料。

具體刪除文件的命令有 `rm`，如果要刪除空目錄，可以用 `rmdir` 命令。例如：

    $ rm regular_file
    $ rmdir directory_file
    $ rm -r directory_file_not_empty

`rm` 有兩個非常重要的參數，一個是 `-f`，這個命令是非常“野蠻的”，它估計給很多 Linux user 帶來了痛苦，另外一個是 `-i`，這個命令是非常“溫柔的”，它估計讓很多用戶感覺煩躁不已。用哪個還是根據您的“心情”吧，如果做好了充分的備份工作，或者採取了一些有效避免災難性後果的動作的話，您在做這些工作的時候就可以放心一些啦。

<span id="toc_11738_32168_24"></span>
### 範例：複製文件

文件的複製通常是指文件內容的“臨時”複製。通過這一節開頭的介紹，我們應該瞭解到，文件的硬鏈接和軟鏈接在某種意義上說也是“文件的複製”，前者同步複製文件內容，後者在讀寫的情況下同步“複製”文件內容。例如：

用 `cp` 命令常規地複製文件（複製目錄需要 `-r` 選項）

    $ cp regular_file regular_file_copy
    $ cp -r diretory_file directory_file_copy

創建硬鏈接（`link` 和 `copy` 不同之處是後者是同步更新，前者則不然，複製之後兩者不再相關）

    $ ln regular_file regular_file_hard_link

創建軟鏈接

    $ ln -s regular_file regluar_file_soft_link

<span id="toc_11738_32168_25"></span>
### 範例：修改文件名

修改文件名實際上僅僅修改了文件名標識符。可以通過 `mv` 命令來實現修改文件名操作（即重命名）。

    $ mv regular_file regular_file_new_name

<span id="toc_11738_32168_26"></span>
### 範例：編輯文件

編輯文件實際上是操作文件的內容，對應普通文本文件的編輯，這裡主要涉及到文件內容的讀、寫、追加、刪除等。這些工作通常會通過專門的編輯器來做，這類編輯器有命令行下的 `vim` 、 `emacs` 和圖形界面下的 `gedit,kedit` 等。如果是一些特定的文件，會有專門的編輯和處理工具，比如圖像處理軟件 `gimp`，文檔編輯軟件 `OpenOffice` 等。這些工具一般都會有專門的教程。

下面主要簡單介紹 Linux 下通過重定向來實現文件的這些常規的編輯操作。

創建一個文件並寫入 `abcde`

    $ echo "abcde" > new_regular_file

再往上面的文件中追加一行 `abcde`

    $ echo "abcde" >> new_regular_file

按行讀一個文件

    $ while read LINE; do echo $LINE; done < test.sh

提示：如果要把包含重定向的字符串變量當作命令來執行，請使用 `eval` 命令，否則無法解釋重定向。例如，

    $ redirect="echo \"abcde\" >test_redirect_file"
    $ $redirect   #這裡會把>當作字符 > 打印出來，而不會當作 重定向 解釋
    "abcde" >test_redirect_file
    $ eval $redirect    #這樣才會把 > 解釋成 重定向
    $ cat test_redirect_file
    abcde

<span id="toc_11738_32168_27"></span>
### 範例：壓縮／解壓縮文件

壓縮和解壓縮文件在一定意義上來說是為了方便文件內容的傳輸，不過也可能有一些特定的用途，比如內核和文件系統的映像文件等（更多相關的知識請參考後續資料）。

這裡僅介紹幾種常見的壓縮和解壓縮方法：

tar

    $ tar -cf file.tar file   #壓縮
    $ tar -xf file.tar    #解壓

gz

    $ gzip  -9 file
    $ gunzip file

tar.gz

    $ tar -zcf file.tar.gz file
    $ tar -zxf file.tar.gz

bz2

    $ bzip2 file
    $ bunzip2 file

tar.bz2

    $ tar -jcf file.tar.bz2 file
    $ tar -jxf file.tar.bz2

通過上面的演示，應該已經非常清楚 `tar`，`bzip2，`bunzip2，`gzip，`gunzip` 命令的角色了吧？如果還不清楚，多操作和比較一些上面的命令，並查看它們的手冊： `man tar`...

<span id="toc_11738_32168_28"></span>
### 範例：文件搜索（文件定位）

文件搜索是指在某個目錄層次中找出具有某些屬性的文件在文件系統中的位置，這個位置如果擴展到整個網絡，那麼可以表示為一個 `URL` 地址，對於本地的地址，可以表示為 `file://+` 本地路徑。本地路徑在 Linux 系統下是以 `/` 開頭，例如，每個用戶的家目錄可以表示為： `file:///home/` 。下面僅僅介紹本地文件搜索的一些辦法。

`find` 命令提供了一種“及時的”搜索辦法，它根據用戶的請求，在指定的目錄層次中遍歷所有文件直到找到需要的文件為止。而 `updatedb+locate` 提供了一種“快速的”的搜索策略，`updatedb` 更新併產生一個本地文件數據庫，而 `locate` 通過文件名檢索這個數據庫以便快速找到相應的文件。前者支持通過各種文件屬性進行搜索，並且提供了一個接口（`-exec` 選項）用於處理搜索後的文件。因此為“單條命令”腳本的愛好者提供了極大的方便，不過對於根據文件名的搜索而言，`updatedb+locate` 的方式在搜索效率上會有明顯提高。下面簡單介紹這兩種方法：

`find` 命令基本使用演示

    $ find ./ -name "*.c" -o -name "*.h"  #找出所有的C語言文件，-o是或者
    $ find ./ \( -name "*.c" -o -name "*.h" \) -exec mv '{}' ./c_files/ \;
    # 把找到的文件移到c_files下，這種用法非常有趣

上面的用法可以用 `xargs` 命令替代

    $ find ./ -name "*.c" -o -name "*.h" | xargs -i mv '{}' ./c_files/
    # 如果要對文件做更復雜的操作，可以考慮把mv改寫為你自己的處理命令，例如，我需要修

改所有的文件名後綴為大寫。

    $ find ./ -name "*.c" -o -name "*.h" | xargs -i ./toupper.sh '{}' ./c_files/

`toupper.sh` 就是我們需要實現的轉換小寫為大寫的一個處理文件，具體實現如下：

    $ cat toupper.sh
    #!/bin/bash

    # the {} will be expended to the current line and becomen the first argument of this script
    FROM=$1
    BASENAME=${FROM##*/}

    BASE=${BASENAME%.*}
    SUFFIX=${BASENAME##*.}

    TOSUFFIX="$(echo $SUFFIX | tr '[a-z]' '[A-Z]')"
    TO=$2/$BASE.$TOSUFFIX
    COM="mv $FROM $TO"
    echo $COM
    eval $COM

`updatedb+locate` 基本使用演示

    $ updatedb #更新庫
    $ locate find*.gz #查找包含find字符串的所有gz壓縮包

實際上，除了上面兩種命令外，Linux 下還有命令查找工具：`which` 和 `whereis`，前者用於返回某個命令的全路徑，而後者用於返回某個命令、源文件、`man 文件的路徑。例如，查找 `find` 命令的絕對路徑：

    $ which find
    /usr/bin/find
    $ whereis find
    find: /usr/bin/find /usr/X11R6/bin/find /usr/bin/X11/find /usr/X11/bin/find /usr/man/man1/find.1.gz /usr/share/man/man1/find.1.gz /usr/X11/man/man1/find.1.gz

需要提到的是，如果想根據文件的內容搜索文件，那麼 `find` 和 `updatedb+locate` 以及 `which`，`whereis` 都無能為力啦，可選的方法是 `grep`，`sed` 等命令，前者在加上 `-r` 參數以後可以在指定目錄下文件中搜索指定的文件內容，後者再使用 `-i` 參數後，可以對文件內容進行替換。它們的基本用法在前面的章節中已經詳細介紹了，這裡就不再贅述。

值得強調的是，這些命令對文件的操作非常有意義。它們在某個程度上把文件系統結構給抽象了，使得對整個文件系統的操作簡化為對單個文件的操作，而單個文件如果僅僅考慮文本部分，那麼最終卻轉化成了之前的字符串操作，即上一節討論過的內容。為了更清楚地瞭解文件的組織結構，文件之間的關係，在下一節將深入探討文件系統。

<span id="toc_11738_32168_29"></span>
## 參考資料

-   [從文件 I/O 看 Linux 的虛擬文件系統](http://www.ibm.com/developerworks/cn/linux/l-cn-vfs/)
-   [Linux 文件系統剖析](http://www.ibm.com/developerworks/cn/linux/l-linux-filesystem/index.html?ca=drs-cn)
-   [《Linux 核心》第九章 文件系統](http://man.chinaunix.net/tech/lyceum/linuxK/fs/filesystem.html)
-   [Linux Device Drivers, 3rd Edition](http://lwn.net/Kernel/LDD3/)
-   [技巧：Linux I/O 重定向的一些小技巧](http://www.ibm.com/developerworks/cn/linux/l-iotips/index.html)
-   Intel 平臺下 Linux 中 ELF 文件動態鏈接的加載、解析及實例分析:
    - [part1](http://www.ibm.com/developerworks/cn/linux/l-elf/part1/index.html),
    - [part2](http://www.ibm.com/developerworks/cn/linux/l-elf/part2/index.html)
-   [Shell 腳本調試技術](http://www.ibm.com/developerworks/cn/linux/l-cn-shell-debug/index.html)
-   [ELF 文件格式及程序加載執行過程總彙](http://www.linuxsir.org/bbs/thread206356.html)
-   [Linux下 C 語言編程——文件的操作](http://fanqiang.chinaunix.net/a4/b2/20010508/113315.html)
-   ["Linux下 C 語言編程" 的 文件操作部分](http://www.mwjx.com/aboutfish/private/book/linux_c.txt)
-   [Filesystem Hierarchy Standard](http://www.pathname.com/fhs/pub/fhs-2.3.html#INTRODUCTION)
-   [學會恢復 Linux系統裡被刪除的 Ext3 文件](http://tech.ccidnet.com/art/237/20070720/1150559_1.html)
-   [使用mc恢復被刪除文件](http://bbs.tech.ccidnet.com/read.php?tid=48372)
-   [linux ext3 誤刪除及恢復原理](http://www.linuxdiyf.com/viewarticle.php?id=30866)
-   [Linux壓縮／解壓縮方式大全](http://www.cnblogs.com/eoiioe/archive/2008/09/20/1294681.html)
-   [Everything is a byte](http://www.reteam.org/papers/e56.pdf)

<span id="toc_11738_32168_30"></span>
## 後記

-   考慮到文件和文件系統的重要性，將把它分成三個小節來介紹：文件、文件系統、程序與進程。在“文件”這一部分，主要介紹文件的基本屬性和常規操作，在“文件系統”那部分，將深入探討 Linux 文件系統的各個部分（包括 Linux 文件系統的結構、具體某個文件系統的大體結構分析、底層驅動的工作原理），在“程序與進程”一節將專門討論可執行文件的相關內容（包括不同的程序類型、加載執行過程、不同進程之間的交互[命令管道和無名管道、信號通信]、對進程的控制等）
-   有必要討論清楚 目錄大小 的含義，另外，最好把一些常規的文件操作全部考慮到，包括文件的讀、寫、執行、刪除、修改、複製、壓縮／解壓縮等
-   下午剛從上海回來，比賽結果很“糟糕”，不過到現在已經不重要了，關鍵是通過決賽發現了很多不足，發現了設計在系統開發中的關鍵角色，並且發現了上海是個美麗的城市，上交也是個美麗的大學。回來就開始整理這個因為比賽落下了兩週的 Blog
-   12月15日，添加文件搜索部分內容
