## 1.1\. 启动 gdb

对 C/C++程序的调试，需要在编译前就加上-g 选项:

```
$g++ -g hello.cpp -o hello

```

调试可执行文件:

```
$gdb <program>

```

program 也就是你的执行文件，一般在当前目录下。

调试 core 文件(core 是程序非法执行后 core dump 后产生的文件):

```
$gdb <program> <core dump file>
$gdb program core.11127

```

调试服务程序:

```
$gdb <program> <PID>
$gdb hello 11127

```

如果你的程序是一个服务程序，那么你可以指定这个服务程序运行时的进程 ID。gdb 会自动 attach 上去，并调试他。program 应该在 PATH 环境变量中搜索得到。

## 1.2\. gdb 交互命令

启动 gdb 后，进入到交互模式，通过以下命令完成对程序的调试；注意高频使用的命令一般都会有缩写，熟练使用这些缩写命令能提高调试的效率；

### 运行

*   run：简记为 r ，其作用是运行程序，当遇到断点后，程序会在断点处停止运行，等待用户输入下一步的命令。
*   continue （简写 c ）：继续执行，到下一个断点处（或运行结束）
*   next：（简写 n），单步跟踪程序，当遇到函数调用时，也不进入此函数体；此命令同 step 的主要区别是，step 遇到用户自定义的函数，将步进到函数中去运行，而 next 则直接调用函数，不会进入到函数体内。
*   step （简写 s）：单步调试如果有函数调用，则进入函数；与命令 n 不同，n 是不进入调用的函数的
*   until：当你厌倦了在一个循环体内单步跟踪时，这个命令可以运行程序直到退出循环体。
*   until+行号： 运行至某行，不仅仅用来跳出循环
*   finish： 运行程序，直到当前函数完成返回，并打印函数返回时的堆栈地址和返回值及参数值等信息。
*   call 函数(参数)：调用程序中可见的函数，并传递“参数”，如：call gdb_test(55)
*   quit：简记为 q ，退出 gdb

### 设置断点

*   break n （简写 b n）:在第 n 行处设置断点

    （可以带上代码路径和代码名称： b OAGUPDATE.cpp:578）

*   b fn1 if a＞b：条件断点设置

*   break func（break 缩写为 b）：在函数 func()的入口处设置断点，如：break cb_button

*   delete 断点号 n：删除第 n 个断点

*   disable 断点号 n：暂停第 n 个断点

*   enable 断点号 n：开启第 n 个断点

*   clear 行号 n：清除第 n 行的断点

*   info b （info breakpoints） ：显示当前程序的断点设置情况

*   delete breakpoints：清除所有断点：

### 查看源代码

*   list ：简记为 l ，其作用就是列出程序的源代码，默认每次显示 10 行。
*   list 行号：将显示当前文件以“行号”为中心的前后 10 行代码，如：list 12
*   list 函数名：将显示“函数名”所在函数的源代码，如：list main
*   list ：不带参数，将接着上一次 list 命令的，输出下边的内容。

### 打印表达式

*   print 表达式：简记为 p ，其中“表达式”可以是任何当前正在被测试程序的有效表达式，比如当前正在调试 C 语言的程序，那么“表达式”可以是任何 C 语言的有效表达式，包括数字，变量甚至是函数调用。
*   print a：将显示整数 a 的值
*   print ++a：将把 a 中的值加 1,并显示出来
*   print name：将显示字符串 name 的值
*   print gdb_test(22)：将以整数 22 作为参数调用 gdb_test() 函数
*   print gdb_test(a)：将以变量 a 作为参数调用 gdb_test() 函数
*   display 表达式：在单步运行时将非常有用，使用 display 命令设置一个表达式后，它将在每次单步进行指令后，紧接着输出被设置的表达式及值。如： display a
*   watch 表达式：设置一个监视点，一旦被监视的“表达式”的值改变，gdb 将强行终止正在被调试的程序。如： watch a
*   whatis ：查询变量或函数
*   info function： 查询函数
*   扩展 info locals： 显示当前堆栈页的所有变量

### 查询运行信息

*   where/bt ：当前运行的堆栈列表；
*   bt backtrace 显示当前调用堆栈
*   up/down 改变堆栈显示的深度
*   set args 参数:指定运行时的参数
*   show args：查看设置好的参数
*   info program： 来查看程序的是否在运行，进程号，被暂停的原因。

### 分割窗口

*   layout：用于分割窗口，可以一边查看代码，一边测试：
*   layout src：显示源代码窗口
*   layout asm：显示反汇编窗口
*   layout regs：显示源代码/反汇编和 CPU 寄存器窗口
*   layout split：显示源代码和反汇编窗口
*   Ctrl + L：刷新窗口

注解

交互模式下直接回车的作用是重复上一指令，对于单步调试非常方便；

## 1.3\. 更强大的工具

### cgdb

cgdb 可以看作 gdb 的界面增强版,用来替代 gdb 的 gdb -tui。cgdb 主要功能是在调试时进行代码的同步显示，这无疑增加了调试的方便性，提高了调试效率。界面类似 vi，符合 unix/linux 下开发人员习惯;如果熟悉 gdb 和 vi，几乎可以立即使用 cgdb。 © 版权所有 2014, Colin http://blog.me115.com. 由 [Sphinx](http://sphinx-doc.org/) 1.3.5 创建。

## 3.1\. 命令参数

*   -a 列出打开文件存在的进程
*   -c<进程名> 列出指定进程所打开的文件
*   -g 列出 GID 号进程详情
*   -d<文件号> 列出占用该文件号的进程
*   +d<目录> 列出目录下被打开的文件
*   +D<目录> 递归列出目录下被打开的文件
*   -n<目录> 列出使用 NFS 的文件
*   -i<条件> 列出符合条件的进程。（4、6、协议、:端口、 @ip ）
*   -p<进程号> 列出指定进程号所打开的文件
*   -u 列出 UID 号进程详情
*   -h 显示帮助信息
*   -v 显示版本信息

## 3.2\. 使用实例

### 实例 1：无任何参数

```
$lsof| more
COMMAND     PID      USER   FD      TYPE             DEVICE SIZE/OFF       NODE NAME
init          1      root  cwd       DIR              253,0     4096          2 /
init          1      root  rtd       DIR              253,0     4096          2 /
init          1      root  txt       REG              253,0   150352    1310795 /sbin/init
init          1      root  mem       REG              253,0    65928    5505054 /lib64/libnss_files-2.12.so
init          1      root  mem       REG              253,0  1918016    5521405 /lib64/libc-2.12.so
init          1      root  mem       REG              253,0    93224    5521440 /lib64/libgcc_s-4.4.6-20120305.so.1
init          1      root  mem       REG              253,0    47064    5521407 /lib64/librt-2.12.so
init          1      root  mem       REG              253,0   145720    5521406 /lib64/libpthread-2.12.so
...

```

说明：

lsof 输出各列信息的意义如下：

*   COMMAND：进程的名称

*   PID：进程标识符

*   PPID：父进程标识符（需要指定-R 参数）

*   USER：进程所有者

*   PGID：进程所属组

*   FD：文件描述符，应用程序通过文件描述符识别该文件。如 cwd、txt 等:

    ```
    （1）cwd：表示 current work dirctory，即：应用程序的当前工作目录，这是该应用程序启动的目录，除非它本身对这个目录进行更改
    （2）txt ：该类型的文件是程序代码，如应用程序二进制文件本身或共享库，如上列表中显示的 /sbin/init 程序
    （3）lnn：library references (AIX);
    （4）er：FD information error (see NAME column);
    （5）jld：jail directory (FreeBSD);
    （6）ltx：shared library text (code and data);
    （7）mxx ：hex memory-mapped type number xx.
    （8）m86：DOS Merge mapped file;
    （9）mem：memory-mapped file;
    （10）mmap：memory-mapped device;
    （11）pd：parent directory;
    （12）rtd：root directory;
    （13）tr：kernel trace file (OpenBSD);
    （14）v86  VP/ix mapped file;
    （15）0：表示标准输出
    （16）1：表示标准输入
    （17）2：表示标准错误
    一般在标准输出、标准错误、标准输入后还跟着文件状态模式：r、w、u 等
    （1）u：表示该文件被打开并处于读取/写入模式
    （2）r：表示该文件被打开并处于只读模式
    （3）w：表示该文件被打开并处于
    （4）空格：表示该文件的状态模式为 unknow，且没有锁定
    （5）-：表示该文件的状态模式为 unknow，且被锁定
    同时在文件状态模式后面，还跟着相关的锁
    （1）N：for a Solaris NFS lock of unknown type;
    （2）r：for read lock on part of the file;
    （3）R：for a read lock on the entire file;
    （4）w：for a write lock on part of the file;（文件的部分写锁）
    （5）W：for a write lock on the entire file;（整个文件的写锁）
    （6）u：for a read and write lock of any length;
    （7）U：for a lock of unknown type;
    （8）x：for an SCO OpenServer Xenix lock on part      of the file;
    （9）X：for an SCO OpenServer Xenix lock on the      entire file;
    （10）space：if there is no lock.

    ```

*   TYPE：文件类型，如 DIR、REG 等，常见的文件类型:

    ```
    （1）DIR：表示目录
    （2）CHR：表示字符类型
    （3）BLK：块设备类型
    （4）UNIX： UNIX 域套接字
    （5）FIFO：先进先出 (FIFO) 队列
    （6）IPv4：网际协议 (IP) 套接字

    ```

*   DEVICE：指定磁盘的名称

*   SIZE：文件的大小

*   NODE：索引节点（文件在磁盘上的标识）

*   NAME：打开文件的确切名称

### 实例 2：查找某个文件相关的进程

```
$lsof /bin/bash
COMMAND     PID USER  FD   TYPE DEVICE SIZE/OFF    NODE NAME
mysqld_sa  2169 root txt    REG  253,0   938736 4587562 /bin/bash
ksmtuned   2334 root txt    REG  253,0   938736 4587562 /bin/bash
bash      20121 root txt    REG  253,0   938736 4587562 /bin/bash

```

### 实例 3：列出某个用户打开的文件信息

```
   $lsof -u username

-u 选项，u 是 user 的缩写

```

### 实例 4：列出某个程序进程所打开的文件信息

```
$lsof -c mysql

```

-c 选项将会列出所有以 mysql 这个进程开头的程序的文件，其实你也可以写成 lsof | grep mysql, 但是第一种方法明显比第二种方法要少打几个字符；

### 实例 5：列出某个用户以及某个进程所打开的文件信息

```
$lsof  -u test -c mysql

```

### 实例 6：通过某个进程号显示该进程打开的文件

```
$lsof -p 11968

```

### 实例 7：列出所有的网络连接

```
$lsof -i

```

### 实例 8：列出所有 tcp 网络连接信息

```
$lsof -i tcp

$lsof -n -i tcp
COMMAND     PID  USER   FD   TYPE  DEVICE SIZE/OFF NODE NAME
svnserve  11552 weber    3u  IPv4 3799399      0t0  TCP *:svn (LISTEN)
redis-ser 25501 weber    4u  IPv4  113150      0t0  TCP 127.0.0.1:6379 (LISTEN)

```

### 实例 9：列出谁在使用某个端口

```
$lsof -i :3306

```

### 实例 10：列出某个用户的所有活跃的网络端口

```
$lsof -a -u test -i

```

### 实例 11：根据文件描述列出对应的文件信息

```
$lsof -d description(like 2)

```

示例:

```
$lsof -d 3 | grep PARSER1
tail      6499 tde    3r   REG    253,3   4514722     417798 /opt/applog/open/log/HOSTPARSER1_ERROR_141217.log.001

```

说明： 0 表示标准输入，1 表示标准输出，2 表示标准错误，从而可知：所以大多数应用程序所打开的文件的 FD 都是从 3 开始

### 实例 12：列出被进程号为 1234 的进程所打开的所有 IPV4 network files

```
$lsof -i 4 -a -p 1234

```

### 实例 13：列出目前连接主机 nf5260i5-td 上端口为：20，21，80 相关的所有文件信息，且每隔 3 秒重复执行

```
lsof -i @nf5260i5-td:20,21,80 -r 3

``` © 版权所有 2014, Colin http://blog.me115.com. 由 [Sphinx](http://sphinx-doc.org/) 1.3.5 创建。

## 4.1\. 命令参数

*   a 显示所有进程
*   -a 显示同一终端下的所有程序
*   -A 显示所有进程
*   c 显示进程的真实名称
*   -N 反向选择
*   -e 等于“-A”
*   e 显示环境变量
*   f 显示程序间的关系
*   -H 显示树状结构
*   r 显示当前终端的进程
*   T 显示当前终端的所有程序
*   u 指定用户的所有进程
*   -au 显示较详细的资讯
*   -aux 显示所有包含其他使用者的行程
*   -C<命令> 列出指定命令的状况
*   –lines<行数> 每页显示的行数
*   –width<字符数> 每页显示的字符数
*   –help 显示帮助信息
*   –version 显示版本显示

## 4.2\. 输出列的含义

*   F 代表这个程序的旗标 (flag)， 4 代表使用者为 super user
*   S 代表这个程序的状态 (STAT)，关于各 STAT 的意义将在内文介绍
*   UID 程序被该 UID 所拥有
*   PID 进程的 ID
*   PPID 则是其上级父程序的 ID
*   C CPU 使用的资源百分比
*   PRI 这个是 Priority (优先执行序) 的缩写，详细后面介绍
*   NI 这个是 Nice 值，在下一小节我们会持续介绍
*   ADDR 这个是 kernel function，指出该程序在内存的那个部分。如果是个 running 的程序，一般就是 “-“
*   SZ 使用掉的内存大小
*   WCHAN 目前这个程序是否正在运作当中，若为 - 表示正在运作
*   TTY 登入者的终端机位置
*   TIME 使用掉的 CPU 时间。
*   CMD 所下达的指令为何

## 4.3\. 使用实例

### 实例 1：显示所有进程信息

```
[root@localhost test6]# ps -A
PID TTY          TIME CMD
1 ?        00:00:00 init
2 ?        00:00:01 migration/0
3 ?        00:00:00 ksoftirqd/0
4 ?        00:00:01 migration/1
5 ?        00:00:00 ksoftirqd/1
6 ?        00:29:57 events/0
7 ?        00:00:00 events/1
8 ?        00:00:00 khelper
49 ?        00:00:00 kthread
54 ?        00:00:00 kblockd/0
55 ?        00:00:00 kblockd/1
56 ?        00:00:00 kacpid
217 ?        00:00:00 cqueue/0
……省略部分结果

```

### 实例 2：显示指定用户信息

```
[root@localhost test6]# ps -u root
PID TTY          TIME CMD
1 ?        00:00:00 init
2 ?        00:00:01 migration/0
3 ?        00:00:00 ksoftirqd/0
4 ?        00:00:01 migration/1
5 ?        00:00:00 ksoftirqd/1
6 ?        00:29:57 events/0
7 ?        00:00:00 events/1
8 ?        00:00:00 khelper
49 ?        00:00:00 kthread
54 ?        00:00:00 kblockd/0
55 ?        00:00:00 kblockd/1
56 ?        00:00:00 kacpid
……省略部分结果

```

### 实例 3：显示所有进程信息，连同命令行

```
[root@localhost test6]# ps -ef
UID        PID  PPID  C STIME TTY          TIME CMD
root         1     0  0 Nov02 ?        00:00:00 init [3]
root         2     1  0 Nov02 ?        00:00:01 [migration/0]
root         3     1  0 Nov02 ?        00:00:00 [ksoftirqd/0]
root         4     1  0 Nov02 ?        00:00:01 [migration/1]
root         5     1  0 Nov02 ?        00:00:00 [ksoftirqd/1]
root         6     1  0 Nov02 ?        00:29:57 [events/0]
root         7     1  0 Nov02 ?        00:00:00 [events/1]
root         8     1  0 Nov02 ?        00:00:00 [khelper]
root        49     1  0 Nov02 ?        00:00:00 [kthread]
root        54    49  0 Nov02 ?        00:00:00 [kblockd/0]
root        55    49  0 Nov02 ?        00:00:00 [kblockd/1]
root        56    49  0 Nov02 ?        00:00:00 [kacpid]

```

### 实例 4： ps 与 grep 组合使用，查找特定进程

```
[root@localhost test6]# ps -ef|grep ssh
root      2720     1  0 Nov02 ?        00:00:00 /usr/sbin/sshd
root     17394  2720  0 14:58 ?        00:00:00 sshd: root@pts/0
root     17465 17398  0 15:57 pts/0    00:00:00 grep ssh

```

### 实例 5：将与这次登入的 PID 与相关信息列示出来

```
[root@localhost test6]# ps -l
F S   UID   PID  PPID  C PRI  NI ADDR SZ WCHAN  TTY          TIME CMD
4 S     0 17398 17394  0  75   0 - 16543 wait   pts/0    00:00:00 bash
4 R     0 17469 17398  0  77   0 - 15877 -      pts/0    00:00:00 ps

```

### 实例 6：列出目前所有的正在内存中的程序

```
[root@localhost test6]# ps aux
USER       PID %CPU %MEM    VSZ   RSS TTY      STAT START   TIME COMMAND
root         1  0.0  0.0  10368   676 ?        Ss   Nov02   0:00 init [3]
root         2  0.0  0.0      0     0 ?        S<   Nov02   0:01 [migration/0]
root         3  0.0  0.0      0     0 ?        SN   Nov02   0:00 [ksoftirqd/0]
root         4  0.0  0.0      0     0 ?        S<   Nov02   0:01 [migration/1]
root         5  0.0  0.0      0     0 ?        SN   Nov02   0:00 [ksoftirqd/1]
root         6  0.0  0.0      0     0 ?        S<   Nov02  29:57 [events/0]
root         7  0.0  0.0      0     0 ?        S<   Nov02   0:00 [events/1]
root         8  0.0  0.0      0     0 ?        S<   Nov02   0:00 [khelper]
root        49  0.0  0.0      0     0 ?        S<   Nov02   0:00 [kthread]
root        54  0.0  0.0      0     0 ?        S<   Nov02   0:00 [kblockd/0]
root        55  0.0  0.0      0     0 ?        S<   Nov02   0:00 [kblockd/1]
root        56  0.0  0.0      0     0 ?        S<   Nov02   0:00 [kacpid]

``` © 版权所有 2014, Colin http://blog.me115.com. 由 [Sphinx](http://sphinx-doc.org/) 1.3.5 创建。

#0  0x00000039958c5620 in __read_nocancel () from /lib64/libc.so.6
#1  0x000000000047dafe in rl_getc ()
#2  0x000000000047def6 in rl_read_key ()
#3  0x000000000046d0f5 in readline_internal_char ()
#4  0x000000000046d4e5 in readline ()
#5  0x00000000004213cf in ?? ()
#6  0x000000000041d685 in ?? ()
#7  0x000000000041e89e in ?? ()
#8  0x00000000004218dc in yyparse ()
#9  0x000000000041b507 in parse_command ()
#10 0x000000000041b5c6 in read_command ()
#11 0x000000000041b74e in reader_loop ()
#12 0x000000000041b2aa in main ()

``` © 版权所有 2014, Colin http://blog.me115.com. 由 [Sphinx](http://sphinx-doc.org/) 1.3.5 创建。

## 6.1\. 输出参数含义

每一行都是一条系统调用，等号左边是系统调用的函数名及其参数，右边是该调用的返回值。 strace 显示这些调用的参数并返回符号形式的值。strace 从内核接收信息，而且不需要以任何特殊的方式来构建内核。

```
$strace cat /dev/null
execve("/bin/cat", ["cat", "/dev/null"], [/* 22 vars */]) = 0
brk(0)                                  = 0xab1000
access("/etc/ld.so.nohwcap", F_OK)      = -1 ENOENT (No such file or directory)
mmap(NULL, 8192, PROT_READ|PROT_WRITE, MAP_PRIVATE|MAP_ANONYMOUS, -1, 0) = 0x7f29379a7000
access("/etc/ld.so.preload", R_OK)      = -1 ENOENT (No such file or directory)
...

```

## 6.2\. 参数

```
-c 统计每一系统调用的所执行的时间,次数和出错的次数等.
-d 输出 strace 关于标准错误的调试信息.
-f 跟踪由 fork 调用所产生的子进程.
-ff 如果提供-o filename,则所有进程的跟踪结果输出到相应的 filename.pid 中,pid 是各进程的进程号.
-F 尝试跟踪 vfork 调用.在-f 时,vfork 不被跟踪.
-h 输出简要的帮助信息.
-i 输出系统调用的入口指针.
-q 禁止输出关于脱离的消息.
-r 打印出相对时间关于,,每一个系统调用.
-t 在输出中的每一行前加上时间信息.
-tt 在输出中的每一行前加上时间信息,微秒级.
-ttt 微秒级输出,以秒了表示时间.
-T 显示每一调用所耗的时间.
-v 输出所有的系统调用.一些调用关于环境变量,状态,输入输出等调用由于使用频繁,默认不输出.
-V 输出 strace 的版本信息.
-x 以十六进制形式输出非标准字符串
-xx 所有字符串以十六进制形式输出.
-a column
设置返回值的输出位置.默认 为 40.
-e expr
指定一个表达式,用来控制如何跟踪.格式如下:
[qualifier=][!]value1[,value2]...
qualifier 只能是 trace,abbrev,verbose,raw,signal,read,write 其中之一.value 是用来限定的符号或数字.默认的 qualifier 是 trace.感叹号是否定符号.例如:
-eopen 等价于 -e trace=open,表示只跟踪 open 调用.而-etrace!=open 表示跟踪除了 open 以外的其他调用.有两个特殊的符号 all 和 none.
注意有些 shell 使用!来执行历史记录里的命令,所以要使用\\.
-e trace=set
只跟踪指定的系统 调用.例如:-e trace=open,close,rean,write 表示只跟踪这四个系统调用.默认的为 set=all.
-e trace=file
只跟踪有关文件操作的系统调用.
-e trace=process
只跟踪有关进程控制的系统调用.
-e trace=network
跟踪与网络有关的所有系统调用.
-e strace=signal
跟踪所有与系统信号有关的 系统调用
-e trace=ipc
跟踪所有与进程通讯有关的系统调用
-e abbrev=set
设定 strace 输出的系统调用的结果集.-v 等与 abbrev=none.默认为 abbrev=all.
-e raw=set
将指 定的系统调用的参数以十六进制显示.
-e signal=set
指定跟踪的系统信号.默认为 all.如 signal=!SIGIO(或者 signal=!io),表示不跟踪 SIGIO 信号.
-e read=set
输出从指定文件中读出 的数据.例如:
-e read=3,5
-e write=set
输出写入到指定文件中的数据.
-o filename
将 strace 的输出写入文件 filename
-p pid
跟踪指定的进程 pid.
-s strsize
指定输出的字符串的最大长度.默认为 32.文件名一直全部输出.
-u username
以 username 的 UID 和 GID 执行被跟踪的命令

```

## 6.3\. 命令实例

### 跟踪可执行程序

```
strace -f -F -o ~/straceout.txt myserver

```

-f -F 选项告诉 strace 同时跟踪 fork 和 vfork 出来的进程，-o 选项把所有 strace 输出写到~/straceout.txt 里 面，myserver 是要启动和调试的程序。

### 跟踪服务程序

```
strace -o output.txt -T -tt -e trace=all -p 28979

```

跟踪 28979 进程的所有系统调用（-e trace=all），并统计系统调用的花费时间，以及开始时间（并以可视化的时分秒格式显示），最后将记录结果存在 output.txt 文件里面。 © 版权所有 2014, Colin http://blog.me115.com. 由 [Sphinx](http://sphinx-doc.org/) 1.3.5 创建。

## 7.1\. IPC 资源查询

### 查看系统使用的 IPC 资源

```
$ipcs

------ Shared Memory Segments --------
key        shmid      owner      perms      bytes      nattch     status

------ Semaphore Arrays --------
key        semid      owner      perms      nsems
0x00000000 229376     weber      600        1

------ Message Queues --------
key        msqid      owner      perms      used-bytes   messages

```

分别查询 IPC 资源:

```
$ipcs -m 查看系统使用的 IPC 共享内存资源
$ipcs -q 查看系统使用的 IPC 队列资源
$ipcs -s 查看系统使用的 IPC 信号量资源

```

### 查看 IPC 资源被谁占用

示例：有个 IPCKEY(51036)，需要查询其是否被占用；

1.  首先通过计算器将其转为十六进制:

    ```
    51036 -> c75c

    ```

2.  如果知道是被共享内存占用:

    ```
    $ipcs -m | grep c75c
    0x0000c75c 40403197   tdea3    666        536870912  2

    ```

3.  如果不确定，则直接查找:

    ```
    $ipcs | grep c75c
    0x0000c75c 40403197   tdea3    666        536870912  2
    0x0000c75c 5079070    tdea3    666        4

    ```

## 7.2\. 系统 IPC 参数查询

```
ipcs -l

------ Shared Memory Limits --------
max number of segments = 4096
max seg size (kbytes) = 4194303
max total shared memory (kbytes) = 1073741824
min seg size (bytes) = 1

------ Semaphore Limits --------
max number of arrays = 128
max semaphores per array = 250
max semaphores system wide = 32000
max ops per semop call = 32
semaphore max value = 32767

------ Messages: Limits --------
max queues system wide = 2048
max size of message (bytes) = 524288
default max size of queue (bytes) = 5242880

```

以上输出显示，目前这个系统的允许的最大内存为 1073741824kb；最大可使用 128 个信号量，每个消息的最大长度为 524288bytes；

## 7.3\. 修改 IPC 系统参数

以 linux 系统为例，在 root 用户下修改/etc/sysctl.conf 文件，保存后使用 sysctl -p 生效:

```
$cat /etc/sysctl.conf
# 一个消息的最大长度
kernel.msgmax = 524288

# 一个消息队列上的最大字节数
# 524288*10
kernel.msgmnb = 5242880

#最大消息队列的个数
kernel.msgmni=2048

#一个共享内存区的最大字节数
kernel.shmmax = 17179869184

#系统范围内最大共享内存标识数
kernel.shmmni=4096

#每个信号灯集的最大信号灯数 系统范围内最大信号灯数 每个信号灯支持的最大操作数 系统范围内最大信号灯集数
#此参数为系统默认，可以不用修改
#kernel.sem = <semmsl> <semmni>*<semmsl> <semopm> <semmni>
kernel.sem = 250 32000 32 128

```

显示输入不带标志的 ipcs：的输出:

```
$ipcs
IPC status from /dev/mem as of Mon Aug 14 15:03:46 1989
T    ID         KEY        MODE       OWNER     GROUP
Message Queues:
q       0    0x00010381 -Rrw-rw-rw-   root      system
q   65537    0x00010307 -Rrw-rw-rw-   root      system
q   65538    0x00010311 -Rrw-rw-rw-   root      system
q   65539    0x0001032f -Rrw-rw-rw-   root      system
q   65540    0x0001031b -Rrw-rw-rw-   root      system
q   65541    0x00010339--rw-rw-rw-    root      system
q       6    0x0002fe03 -Rrw-rw-rw-   root      system
Shared Memory:
m   65537    0x00000000 DCrw-------   root      system
m  720898    0x00010300 -Crw-rw-rw-   root      system
m   65539    0x00000000 DCrw-------   root      system
Semaphores:
s  131072    0x4d02086a --ra-ra----   root      system
s   65537    0x00000000 --ra-------   root      system
s 1310722    0x000133d0 --ra-------   7003      30720

```

## 7.4\. 清除 IPC 资源

使用 ipcrm 命令来清除 IPC 资源：这个命令同时会将与 ipc 对象相关联的数据也一起移除。当然，只有 root 用户，或者 ipc 对象的创建者才有这项权利；

ipcrm 用法:

```
ipcrm -M shmkey  移除用 shmkey 创建的共享内存段
ipcrm -m shmid    移除用 shmid 标识的共享内存段
ipcrm -Q msgkey  移除用 msqkey 创建的消息队列
ipcrm -q msqid  移除用 msqid 标识的消息队列
ipcrm -S semkey  移除用 semkey 创建的信号
ipcrm -s semid  移除用 semid 标识的信号

```

清除当前用户创建的所有的 IPC 资源:

```
ipcs -q | awk '{ print "ipcrm -q "$2}' | sh > /dev/null 2>&1;
ipcs -m | awk '{ print "ipcrm -m "$2}' | sh > /dev/null 2>&1;
ipcs -s | awk '{ print "ipcrm -s "$2}' | sh > /dev/null 2>&1;

```

## 7.5\. 综合应用

### 查询 user1 用户环境上是否存在积 Queue 现象

1.  查询队列 Queue:

    ```
    $ipcs -q

    ------ Message Queues --------
    key        msqid      owner      perms      used-bytes   messages
    0x49060005 58261504   user1    660        0            0
    0x4f060005 58294273   user1    660        0            0
    ...

    ```

2.  找出第 6 列大于 0 的服务:

    ```
    $ ipcs -q |grep user1 |awk '{if($5>0) print $0}'
    0x00000000 1071579324 user1       644        1954530      4826
    0x00000000 1071644862 user1       644        1961820      4844
    0x00000000 1071677631 user1       644        1944810      4802
    0x00000000 1071710400 user1       644        1961820      4844

    ``` © 版权所有 2014, Colin http://blog.me115.com. 由 [Sphinx](http://sphinx-doc.org/) 1.3.5 创建。

## 8.1\. top 命令交互操作指令

下面列出一些常用的 top 命令操作指令

> *   q：退出 top 命令
> *   <Space>：立即刷新
> *   s：设置刷新时间间隔
> *   c：显示命令完全模式
> *   t:：显示或隐藏进程和 CPU 状态信息
> *   m：显示或隐藏内存状态信息
> *   l：显示或隐藏 uptime 信息
> *   f：增加或减少进程显示标志
> *   S：累计模式，会把已完成或退出的子进程占用的 CPU 时间累计到父进程的 MITE+
> *   P：按%CPU 使用率排行
> *   T：按 MITE+排行
> *   M：按%MEM 排行
> *   u：指定显示用户进程
> *   r：修改进程 renice 值
> *   kkill：进程
> *   i：只显示正在运行的进程
> *   W：保存对 top 的设置到文件^/.toprc，下次启动将自动调用 toprc 文件的设置。
> *   h：帮助命令。
> *   q：退出

注：强调一下，使用频率最高的是 P、T、M，因为通常使用 top，我们就想看看是哪些进程最耗 cpu 资源、占用的内存最多； 注：通过”shift + >”或”shift + <”可以向右或左改变排序列 如果只需要查看内存：可用 free 命令。只查看 uptime 信息（第一行），可用 uptime 命令；

## 8.2\. 实例

### 实例 1：多核 CPU 监控

在 top 基本视图中，按键盘数字“1”，可监控每个逻辑 CPU 的状况；

```
[rdtfr@bl685cb4-t ^]$ top
top - 09:10:44 up 20 days, 16:51,  4 users,  load average: 3.82, 4.40, 4.40
Tasks: 1201 total,  10 running, 1189 sleeping,   0 stopped,   2 zombie
Cpu0  :  1.3%us,  2.3%sy,  0.0%ni, 96.4%id,  0.0%wa,  0.0%hi,  0.0%si,  0.0%st
Cpu1  :  1.3%us,  2.6%sy,  0.0%ni, 96.1%id,  0.0%wa,  0.0%hi,  0.0%si,  0.0%st
Cpu2  :  1.0%us,  2.0%sy,  0.0%ni, 92.5%id,  0.0%wa,  0.0%hi,  4.6%si,  0.0%st
Cpu3  :  3.9%us,  7.8%sy,  0.0%ni, 83.2%id,  0.0%wa,  0.0%hi,  5.2%si,  0.0%st
Cpu4  :  4.2%us, 10.4%sy,  0.0%ni, 63.8%id,  0.0%wa,  0.0%hi, 21.5%si,  0.0%st
Cpu5  :  6.8%us, 12.7%sy,  0.0%ni, 80.5%id,  0.0%wa,  0.0%hi,  0.0%si,  0.0%st
Cpu6  :  2.9%us,  7.2%sy,  0.0%ni, 85.3%id,  0.0%wa,  0.0%hi,  4.6%si,  0.0%st
Cpu7  :  6.2%us, 13.0%sy,  0.0%ni, 75.3%id,  0.0%wa,  0.0%hi,  5.5%si,  0.0%st
Mem:  32943888k total, 32834216k used,   109672k free,   642704k buffers
Swap: 35651576k total,  5761928k used, 29889648k free, 16611500k cached

```

### 实例 2：高亮显示当前运行进程

```
在 top 基本视图中,按键盘“b”（打开/关闭加亮效果）；

```

### 实例 3：显示完整的程序命令

命令：top -c

```
[rdtfr@bl685cb4-t ^]$ top -c
top - 09:14:35 up 20 days, 16:55,  4 users,  load average: 5.77, 5.01, 4.64
Tasks: 1200 total,   5 running, 1192 sleeping,   0 stopped,   3 zombie
Cpu(s):  4.4%us,  6.0%sy,  0.0%ni, 83.8%id,  0.2%wa,  0.0%hi,  5.5%si,  0.0%st
Mem:  32943888k total, 32842896k used,   100992k free,   591484k buffers
Swap: 35651576k total,  5761808k used, 29889768k free, 16918824k cached
PID USER      PR  NI  VIRT  RES  SHR S %CPU %MEM    TIME+  COMMAND
2013 apache    18   0  403m  88m 5304 S 25.0  0.3   6:37.44 /usr/sbin/httpd
18335 pubtest   22   0 65576  996  728 R  7.8  0.0   0:00.24 netstat -naltp
16499 rdtfare   15   0 13672 2080  824 R  2.6  0.0   0:00.38 top -c
29684 rdtfare   15   0 1164m 837m  14m S  2.3  2.6 148:47.54 ./autodata data1.txt
12976 pubtest   18   0  238m 9000 1932 S  1.6  0.0 439:28.44 tscagent -s TOEV_P

```

### 实例 4：显示指定的进程信息

命令：top -p pidid

```
/opt/app/tdv1/config#top -p 17265
top - 09:17:34 up 455 days, 17:55,  2 users,  load average: 3.76, 4.56, 4.46
Tasks:   1 total,   0 running,   1 sleeping,   0 stopped,   0 zombie
Cpu(s):  7.8%us,  1.9%sy,  0.0%ni, 89.2%id,  0.0%wa,  0.1%hi,  1.0%si,  0.0%st
Mem:   8175452k total,  8103988k used,    71464k free,   268716k buffers
Swap:  6881272k total,  4275424k used,  2605848k free,  6338184k cached
PID USER      PR  NI  VIRT  RES  SHR S %CPU %MEM    TIME+  COMMAND
17265 tdv1      15   0 56504  828  632 S  0.0  0.0 195:53.25 redis-server

```

指定进程信息有多个时，需要结合其它工具将回车替换为,（-p 支持 pid,pid,pid 语法）

命令：top -p pgrep MULTI_PROCESS | tr “\n” ”,” | sed ‘s/,$//’

```
/opt/app/tdv1$top -p `pgrep java | tr "\\n" "," | sed 's/,$//'`
top - 14:05:31 up 53 days,  2:43,  9 users,  load average: 0.29, 0.34, 0.22
Tasks:   3 total,   0 running,   3 sleeping,   0 stopped,   0 zombie
Cpu(s):  5.9%us,  8.2%sy,  0.0%ni, 86.0%id,  0.0%wa,  0.0%hi,  0.0%si,  0.0%st
Mem:  66082088k total, 29512860k used, 36569228k free,   756352k buffers
Swap: 32767992k total,  1019900k used, 31748092k free, 15710284k cached

  PID USER      PR  NI  VIRT  RES  SHR S %CPU %MEM    TIME+  COMMAND                                          27855 rdtfare   20   0 4454m 1.3g 5300 S  0.7  2.0 338:31.37 java
 2034 jenkins   20   0 18.3g 5.2g 5284 S  0.3  8.2  56:02.38 java                                             12156 rdtfare   20   0 4196m 1.2g  12m S  0.3  2.0  86:34.62 java

```

## 8.3\. 更强大的工具

### htop

htop 是一个 Linux 下的交互式的进程浏览器，可以用来替换 Linux 下的 top 命令。

与 Linux 传统的 top 相比，htop 更加人性化。它可让用户交互式操作，支持颜色主题，可横向或纵向滚动浏览进程列表，并支持鼠标操作。

与 top 相比，htop 有以下优点：

*   可以横向或纵向滚动浏览进程列表，以便看到所有的进程和完整的命令行。
*   在启动上，比 top 更快。
*   杀进程时不需要输入进程号。
*   htop 支持鼠标操作。 © 版权所有 2014, Colin http://blog.me115.com. 由 [Sphinx](http://sphinx-doc.org/) 1.3.5 创建。

## 10.1\. vmstat 的语法

　　vmstat [-V] [-n] [delay [count]]

> *   -V 表示打印出版本信息；
> *   -n 表示在周期性循环输出时，输出的头部信息仅显示一次；
> *   delay 是两次输出之间的延迟时间；
> *   count 是指按照这个时间间隔统计的次数。

```
/root$vmstat 5 5
procs -----------memory---------- ---swap-- -----io---- --system-- -----cpu-----
r  b   swpd   free   buff  cache   si   so    bi    bo   in   cs us sy id wa st
6  0      0 27900472 204216 28188356    0    0     0     9    1    2 11 14 75  0  0
9  0      0 27900380 204228 28188360    0    0     0    13 33312 126221 22 20 58  0  0
2  0      0 27900340 204240 28188364    0    0     0    10 32755 125566 22 20 58  0  0

```

## 10.2\. 字段说明

Procs（进程）:

*   r: 运行队列中进程数量
*   b: 等待 IO 的进程数量

Memory（内存）:

*   swpd: 使用虚拟内存大小
*   free: 可用内存大小
*   buff: 用作缓冲的内存大小
*   cache: 用作缓存的内存大小

Swap:

*   si: 每秒从交换区写到内存的大小
*   so: 每秒写入交换区的内存大小

IO：（现在的 Linux 版本块的大小为 1024bytes）

*   bi: 每秒读取的块数
*   bo: 每秒写入的块数

system：

*   in: 每秒中断数，包括时钟中断
*   cs: 每秒上下文切换数

CPU（以百分比表示）

*   us: 用户进程执行时间(user time)
*   sy: 系统进程执行时间(system time)
*   id: 空闲时间(包括 IO 等待时间)
*   wa: 等待 IO 时间 © 版权所有 2014, Colin http://blog.me115.com. 由 [Sphinx](http://sphinx-doc.org/) 1.3.5 创建。

## 11.1\. 命令格式

iostat[参数][时间][次数]

## 11.2\. 命令功能

通过 iostat 方便查看 CPU、网卡、tty 设备、磁盘、CD-ROM 等等设备的活动情况, 负载信息。

## 11.3\. 命令参数

*   -C 显示 CPU 使用情况
*   -d 显示磁盘使用情况
*   -k 以 KB 为单位显示
*   -m 以 M 为单位显示
*   -N 显示磁盘阵列(LVM) 信息
*   -n 显示 NFS 使用情况
*   -p[磁盘] 显示磁盘和分区的情况
*   -t 显示终端和 CPU 的信息
*   -x 显示详细信息
*   -V 显示版本信息

## 11.4\. 工具实例

### 实例 1：显示所有设备负载情况

```
/root$iostat
Linux 2.6.32-279.el6.x86_64 (colin)   07/16/2014      _x86_64_        (4 CPU)

avg-cpu:  %user   %nice %system %iowait  %steal   %idle
10.81    0.00   14.11    0.18    0.00   74.90

Device:            tps   Blk_read/s   Blk_wrtn/s   Blk_read   Blk_wrtn
sda               1.95         1.48        70.88    9145160  437100644
dm-0              3.08         0.55        24.34    3392770  150087080
dm-1              5.83         0.93        46.49    5714522  286724168
dm-2              0.01         0.00         0.05      23930     289288

```

cpu 属性值说明：

*   %user：CPU 处在用户模式下的时间百分比。
*   %nice：CPU 处在带 NICE 值的用户模式下的时间百分比。
*   %system：CPU 处在系统模式下的时间百分比。
*   %iowait：CPU 等待输入输出完成时间的百分比。
*   %steal：管理程序维护另一个虚拟处理器时，虚拟 CPU 的无意识等待时间百分比。
*   %idle：CPU 空闲时间百分比。

注：如果%iowait 的值过高，表示硬盘存在 I/O 瓶颈，%idle 值高，表示 CPU 较空闲，如果%idle 值高但系统响应慢时，有可能是 CPU 等待分配内存，此时应加大内存容量。%idle 值如果持续低于 10，那么系统的 CPU 处理能力相对较低，表明系统中最需要解决的资源是 CPU。

disk 属性值说明：

*   rrqm/s: 每秒进行 merge 的读操作数目。即 rmerge/s
*   wrqm/s: 每秒进行 merge 的写操作数目。即 wmerge/s
*   r/s: 每秒完成的读 I/O 设备次数。即 rio/s
*   w/s: 每秒完成的写 I/O 设备次数。即 wio/s
*   rsec/s: 每秒读扇区数。即 rsect/s
*   wsec/s: 每秒写扇区数。即 wsect/s
*   rkB/s: 每秒读 K 字节数。是 rsect/s 的一半，因为每扇区大小为 512 字节。
*   wkB/s: 每秒写 K 字节数。是 wsect/s 的一半。
*   avgrq-sz: 平均每次设备 I/O 操作的数据大小 (扇区)。
*   avgqu-sz: 平均 I/O 队列长度。
*   await: 平均每次设备 I/O 操作的等待时间 (毫秒)。
*   svctm: 平均每次设备 I/O 操作的服务时间 (毫秒)。
*   %util: 一秒中有百分之多少的时间用于 I/O 操作，即被 io 消耗的 cpu 百分比

备注：如果 %util 接近 100%，说明产生的 I/O 请求太多，I/O 系统已经满负荷，该磁盘可能存在瓶颈。如果 svctm 比较接近 await，说明 I/O 几乎没有等待时间；如果 await 远大于 svctm，说明 I/O 队列太长，io 响应太慢，则需要进行必要优化。如果 avgqu-sz 比较大，也表示有当量 io 在等待。

### 实例 2：定时显示所有信息

```
/root$iostat 2 3
Linux 2.6.32-279.el6.x86_64 (colin)   07/16/2014      _x86_64_        (4 CPU)

avg-cpu:  %user   %nice %system %iowait  %steal   %idle
10.81    0.00   14.11    0.18    0.00   74.90

Device:            tps   Blk_read/s   Blk_wrtn/s   Blk_read   Blk_wrtn
sda               1.95         1.48        70.88    9145160  437106156
dm-0              3.08         0.55        24.34    3392770  150088376
dm-1              5.83         0.93        46.49    5714522  286728384
dm-2              0.01         0.00         0.05      23930     289288

avg-cpu:  %user   %nice %system %iowait  %steal   %idle
22.62    0.00   19.67    0.26    0.00   57.46

Device:            tps   Blk_read/s   Blk_wrtn/s   Blk_read   Blk_wrtn
sda               2.50         0.00        28.00          0         56
dm-0              0.00         0.00         0.00          0          0
dm-1              3.50         0.00        28.00          0         56
dm-2              0.00         0.00         0.00          0          0

avg-cpu:  %user   %nice %system %iowait  %steal   %idle
22.69    0.00   19.62    0.00    0.00   57.69

Device:            tps   Blk_read/s   Blk_wrtn/s   Blk_read   Blk_wrtn
sda               0.00         0.00         0.00          0          0
dm-0              0.00         0.00         0.00          0          0
dm-1              0.00         0.00         0.00          0          0
dm-2              0.00         0.00         0.00          0          0

```

说明：每隔 2 秒刷新显示，且显示 3 次

### 实例 3：查看 TPS 和吞吐量

```
/root$iostat -d -k 1 1
Linux 2.6.32-279.el6.x86_64 (colin)   07/16/2014      _x86_64_        (4 CPU)

Device:            tps    kB_read/s    kB_wrtn/s    kB_read    kB_wrtn
sda               1.95         0.74        35.44    4572712  218559410
dm-0              3.08         0.28        12.17    1696513   75045968
dm-1              5.83         0.46        23.25    2857265  143368744
dm-2              0.01         0.00         0.02      11965     144644

```

*   tps：该设备每秒的传输次数（Indicate the number of transfers per second that were issued to the device.）。“一次传输”意思是“一次 I/O 请求”。多个逻辑请求可能会被合并为“一次 I/O 请求”。“一次传输”请求的大小是未知的。
*   kB_read/s：每秒从设备（drive expressed）读取的数据量；
*   kB_wrtn/s：每秒向设备（drive expressed）写入的数据量；
*   kB_read：读取的总数据量；kB_wrtn：写入的总数量数据量；

这些单位都为 Kilobytes。

上面的例子中，我们可以看到磁盘 sda 以及它的各个分区的统计数据，当时统计的磁盘总 TPS 是 1.95，下面是各个分区的 TPS。（因为是瞬间值，所以总 TPS 并不严格等于各个分区 TPS 的总和）

### 实例 4：查看设备使用率（%util）和响应时间（await）

```
/root$iostat -d -x -k 1 1
Linux 2.6.32-279.el6.x86_64 (colin)   07/16/2014      _x86_64_        (4 CPU)

Device:         rrqm/s   wrqm/s     r/s     w/s    rkB/s    wkB/s avgrq-sz avgqu-sz   await  svctm  %util
sda               0.02     7.25    0.04    1.90     0.74    35.47    37.15     0.04   19.13   5.58   1.09
dm-0              0.00     0.00    0.04    3.05     0.28    12.18     8.07     0.65  209.01   1.11   0.34
dm-1              0.00     0.00    0.02    5.82     0.46    23.26     8.13     0.43   74.33   1.30   0.76
dm-2              0.00     0.00    0.00    0.01     0.00     0.02     8.00     0.00    5.41   3.28   0.00

```

*   rrqm/s： 每秒进行 merge 的读操作数目.即 delta(rmerge)/s
*   wrqm/s： 每秒进行 merge 的写操作数目.即 delta(wmerge)/s
*   r/s： 每秒完成的读 I/O 设备次数.即 delta(rio)/s
*   w/s： 每秒完成的写 I/O 设备次数.即 delta(wio)/s
*   rsec/s： 每秒读扇区数.即 delta(rsect)/s
*   wsec/s： 每秒写扇区数.即 delta(wsect)/s
*   rkB/s： 每秒读 K 字节数.是 rsect/s 的一半,因为每扇区大小为 512 字节.(需要计算)
*   wkB/s： 每秒写 K 字节数.是 wsect/s 的一半.(需要计算)
*   avgrq-sz：平均每次设备 I/O 操作的数据大小 (扇区).delta(rsect+wsect)/delta(rio+wio)
*   avgqu-sz：平均 I/O 队列长度.即 delta(aveq)/s/1000 (因为 aveq 的单位为毫秒).
*   await： 平均每次设备 I/O 操作的等待时间 (毫秒).即 delta(ruse+wuse)/delta(rio+wio)
*   svctm： 平均每次设备 I/O 操作的服务时间 (毫秒).即 delta(use)/delta(rio+wio)
*   %util： 一秒中有百分之多少的时间用于 I/O 操作,或者说一秒中有多少时间 I/O 队列是非空的，即 delta(use)/s/1000 (因为 use 的单位为毫秒)

如果 %util 接近 100%，说明产生的 I/O 请求太多，I/O 系统已经满负荷，该磁盘可能存在瓶颈。 idle 小于 70% IO 压力就较大了，一般读取速度有较多的 wait。 同时可以结合 vmstat 查看查看 b 参数(等待资源的进程数)和 wa 参数(IO 等待所占用的 CPU 时间的百分比，高过 30%时 IO 压力高)。

另外 await 的参数也要多和 svctm 来参考。差的过高就一定有 IO 的问题。

avgqu-sz 也是个做 IO 调优时需要注意的地方，这个就是直接每次操作的数据的大小，如果次数多，但数据拿的小的话，其实 IO 也会很小。如果数据拿的大，才 IO 的数据会高。也可以通过 avgqu-sz × ( r/s or w/s ) = rsec/s or wsec/s。也就是讲，读定速度是这个来决定的。

svctm 一般要小于 await (因为同时等待的请求的等待时间被重复计算了)，svctm 的大小一般和磁盘性能有关，CPU/内存的负荷也会对其有影响，请求过多也会间接导致 svctm 的增加。await 的大小一般取决于服务时间(svctm) 以及 I/O 队列的长度和 I/O 请求的发出模式。如果 svctm 比较接近 await，说明 I/O 几乎没有等待时间；如果 await 远大于 svctm，说明 I/O 队列太长，应用得到的响应时间变慢，如果响应时间超过了用户可以容许的范围，这时可以考虑更换更快的磁盘，调整内核 elevator 算法，优化应用，或者升级 CPU。

队列长度(avgqu-sz)也可作为衡量系统 I/O 负荷的指标，但由于 avgqu-sz 是按照单位时间的平均值，所以不能反映瞬间的 I/O 洪水。

形象的比喻：

*   r/s+w/s 类似于交款人的总数
*   平均队列长度(avgqu-sz)类似于单位时间里平均排队人的个数
*   平均服务时间(svctm)类似于收银员的收款速度
*   平均等待时间(await)类似于平均每人的等待时间
*   平均 I/O 数据(avgrq-sz)类似于平均每人所买的东西多少
*   I/O 操作率 (%util)类似于收款台前有人排队的时间比例

设备 IO 操作:总 IO(io)/s = r/s(读) +w/s(写)

平均等待时间=单个 I/O 服务器时间*(1+2+...+请求总数-1)/请求总数

每秒发出的 I/0 请求很多,但是平均队列就 4,表示这些请求比较均匀,大部分处理还是比较及时。 © 版权所有 2014, Colin http://blog.me115.com. 由 [Sphinx](http://sphinx-doc.org/) 1.3.5 创建。

## 12.1\. 追溯过去的统计数据

默认情况下，sar 从最近的 0 点 0 分开始显示数据；如果想继续查看一天前的报告；可以查看保存在/var/log/sysstat/下的 sa 日志； 使用 sar 工具查看:

```
$sar -f /var/log/sysstat/sa28 \| head sar -r -f
/var/log/sysstat/sa28

```

![../_images/sar1.png](img/sar1.png)

## 12.2\. 查看 CPU 使用率

sar -u : 默认情况下显示的 cpu 使用率等信息就是 sar -u；

![../_images/sar2.png](img/sar2.png)

可以看到这台机器使用了虚拟化技术，有相应的时间消耗； 各列的指标分别是:

*   %user 用户模式下消耗的 CPU 时间的比例；
*   %nice 通过 nice 改变了进程调度优先级的进程，在用户模式下消耗的 CPU 时间的比例
*   %system 系统模式下消耗的 CPU 时间的比例；
*   %iowait CPU 等待磁盘 I/O 导致空闲状态消耗的时间比例；
*   %steal 利用 Xen 等操作系统虚拟化技术，等待其它虚拟 CPU 计算占用的时间比例；
*   %idle CPU 空闲时间比例；

## 12.3\. 查看平均负载

sar -q: 查看平均负载

指定-q 后，就能查看运行队列中的进程数、系统上的进程大小、平均负载等；与其它命令相比，它能查看各项指标随时间变化的情况；

*   runq-sz：运行队列的长度（等待运行的进程数）
*   plist-sz：进程列表中进程（processes）和线程（threads）的数量
*   ldavg-1：最后 1 分钟的系统平均负载 ldavg-5：过去 5 分钟的系统平均负载
*   ldavg-15：过去 15 分钟的系统平均负载

![../_images/sar3.png](img/sar3.png)

## 12.4\. 查看内存使用状况

sar -r： 指定-r 之后，可查看物理内存使用状况；

![../_images/sar4.png](img/sar4.png)

*   kbmemfree：这个值和 free 命令中的 free 值基本一致,所以它不包括 buffer 和 cache 的空间.
*   kbmemused：这个值和 free 命令中的 used 值基本一致,所以它包括 buffer 和 cache 的空间.
*   %memused：物理内存使用率，这个值是 kbmemused 和内存总量(不包括 swap)的一个百分比.
*   kbbuffers 和 kbcached：这两个值就是 free 命令中的 buffer 和 cache.
*   kbcommit：保证当前系统所需要的内存,即为了确保不溢出而需要的内存(RAM+swap).
*   %commit：这个值是 kbcommit 与内存总量(包括 swap)的一个百分比.

## 12.5\. 查看页面交换发生状况

sar -W：查看页面交换发生状况

页面发生交换时，服务器的吞吐量会大幅下降；服务器状况不良时，如果怀疑因为内存不足而导致了页面交换的发生，可以使用这个命令来确认是否发生了大量的交换；

![../_images/sar5.png](img/sar5.png)

*   pswpin/s：每秒系统换入的交换页面（swap page）数量
*   pswpout/s：每秒系统换出的交换页面（swap page）数量

要判断系统瓶颈问题，有时需几个 sar 命令选项结合起来；

*   怀疑 CPU 存在瓶颈，可用 sar -u 和 sar -q 等来查看
*   怀疑内存存在瓶颈，可用 sar -B、sar -r 和 sar -W 等来查看
*   怀疑 I/O 存在瓶颈，可用 sar -b、sar -u 和 sar -d 等来查看

## 12.6\. 安装

1.  有的 linux 系统下，默认可能没有安装这个包，使用 apt-get install sysstat 来安装；
2.  安装完毕，将性能收集工具的开关打开： vi /etc/default/sysstat

> 设置 ENABLED=”true”

3.  启动这个工具来收集系统性能数据： /etc/init.d/sysstat start

## 12.7\. sar 参数说明

*   -A 汇总所有的报告
*   -a 报告文件读写使用情况
*   -B 报告附加的缓存的使用情况
*   -b 报告缓存的使用情况
*   -c 报告系统调用的使用情况
*   -d 报告磁盘的使用情况
*   -g 报告串口的使用情况
*   -h 报告关于 buffer 使用的统计数据
*   -m 报告 IPC 消息队列和信号量的使用情况
*   -n 报告命名 cache 的使用情况
*   -p 报告调页活动的使用情况
*   -q 报告运行队列和交换队列的平均长度
*   -R 报告进程的活动情况
*   -r 报告没有使用的内存页面和硬盘块
*   -u 报告 CPU 的利用率
*   -v 报告进程、i 节点、文件和锁表状态
*   -w 报告系统交换活动状况
*   -y 报告 TTY 设备活动状况 © 版权所有 2014, Colin http://blog.me115.com. 由 [Sphinx](http://sphinx-doc.org/) 1.3.5 创建。

## 13.1\. 参数说明

*   -a –all 全部 Equivalent to: -h -l -S -s -r -d -V -A -I

*   -h –file-header 文件头 Display the ELF file header

*   -l –program-headers 程序 Display the program headers

*   –segments An alias for –program-headers

*   -S –section-headers 段头 Display the sections’ header

*   | `--sections` | An alias for –section-headers |

*   -e –headers 全部头 Equivalent to: -h -l -S

*   -s –syms 符号表 Display the symbol table

*   | `--symbols` | An alias for –syms |

*   -n –notes 内核注释 Display the core notes (if present)

*   -r –relocs 重定位 Display the relocations (if present)

*   -u –unwind Display the unwind info (if present)

*   -d –dynamic 动态段 Display the dynamic segment (if present)

*   -V –version-info 版本 Display the version sections (if present)

*   -A –arch-specific CPU 构架 Display architecture specific information (if any).

*   -D –use-dynamic 动态段 Use the dynamic section info when displaying symbols

*   -x –hex-dump=<number> 显示 段内内容 Dump the contents of section <number>

*   -w[liaprmfFso] or

*   -I –histogram Display histogram of bucket list lengths

*   -W –wide 宽行输出 Allow output width to exceed 80 characters

*   -H –help Display this information

*   -v –version Display the version number of readelf

## 13.2\. 示例

想知道一个应用程序的可运行的架构平台:

```
$readelf -h main| grep Machine

```

-h 选项将显示文件头的概要信息，从里面可以看到，有很多有用的信息：

```
$readelf -h main
ELF Header:
Magic:   7f 45 4c 46 02 01 01 00 00 00 00 00 00 00 00 00
Class:                             ELF64
Data:                              2 s complement, little endian
Version:                           1 (current)
OS/ABI:                            UNIX - System V
ABI Version:                       0
Type:                              EXEC (Executable file)
Machine:                           Advanced Micro Devices X86-64
Version:                           0x1
Entry point address:               0x400790
Start of program headers:          64 (bytes into file)
Start of section headers:          5224 (bytes into file)
Flags:                             0x0
Size of this header:               64 (bytes)
Size of program headers:           56 (bytes)
Number of program headers:         8
Size of section headers:           64 (bytes)
Number of section headers:         29
Section header string table index: 26

```

一个编译好的应用程序，想知道其编译时是否使用了-g 选项（加入调试信息）:

```
$readelf -S main| grep debug

```

用-S 选项是显示所有段信息；如果编译时使用了-g 选项，则会有 debug 段;

查看.o 文件是否编入了调试信息（编译的时候是否加了-g):

```
$readelf -S Shpos.o | grep debug

```

## 13.3\. 完整输出

readelf 输出的完整内容:

```
$readelf -all a.out
ELF Header:
  Magic:   7f 45 4c 46 01 01 01 00 00 00 00 00 00 00 00 00
  Class:                             ELF32
  Data:                              2's complement, little endian
  Version:                           1 (current)
  OS/ABI:                            UNIX - System V
  ABI Version:                       0
  Type:                              EXEC (Executable file)
  Machine:                           Intel 80386
  Version:                           0x1
  Entry point address:               0x8048330
  Start of program headers:          52 (bytes into file)
  Start of section headers:          4412 (bytes into file)
  Flags:                             0x0
  Size of this header:               52 (bytes)
  Size of program headers:           32 (bytes)
  Number of program headers:         9
  Size of section headers:           40 (bytes)
  Number of section headers:         30
  Section header string table index: 27

Section Headers:
  [Nr] Name              Type            Addr     Off    Size   ES Flg Lk Inf Al
  [ 0]                   NULL            00000000 000000 000000 00      0   0  0
  [ 1] .interp           PROGBITS        08048154 000154 000013 00   A  0   0  1
  [ 2] .note.ABI-tag     NOTE            08048168 000168 000020 00   A  0   0  4
  [ 3] .note.gnu.build-i NOTE            08048188 000188 000024 00   A  0   0  4
  [ 4] .gnu.hash         GNU_HASH        080481ac 0001ac 000020 04   A  5   0  4
  [ 5] .dynsym           DYNSYM          080481cc 0001cc 000050 10   A  6   1  4
  [ 6] .dynstr           STRTAB          0804821c 00021c 00004c 00   A  0   0  1
  [ 7] .gnu.version      VERSYM          08048268 000268 00000a 02   A  5   0  2
  [ 8] .gnu.version_r    VERNEED         08048274 000274 000020 00   A  6   1  4
  [ 9] .rel.dyn          REL             08048294 000294 000008 08   A  5   0  4
  [10] .rel.plt          REL             0804829c 00029c 000018 08   A  5  12  4
  [11] .init             PROGBITS        080482b4 0002b4 00002e 00  AX  0   0  4
  [12] .plt              PROGBITS        080482f0 0002f0 000040 04  AX  0   0 16
  [13] .text             PROGBITS        08048330 000330 00018c 00  AX  0   0 16
  [14] .fini             PROGBITS        080484bc 0004bc 00001a 00  AX  0   0  4
  [15] .rodata           PROGBITS        080484d8 0004d8 000011 00   A  0   0  4
  [16] .eh_frame_hdr     PROGBITS        080484ec 0004ec 000034 00   A  0   0  4
  [17] .eh_frame         PROGBITS        08048520 000520 0000c4 00   A  0   0  4
  [18] .ctors            PROGBITS        08049f14 000f14 000008 00  WA  0   0  4
  [19] .dtors            PROGBITS        08049f1c 000f1c 000008 00  WA  0   0  4
  [20] .jcr              PROGBITS        08049f24 000f24 000004 00  WA  0   0  4
  [21] .dynamic          DYNAMIC         08049f28 000f28 0000c8 08  WA  6   0  4
  [22] .got              PROGBITS        08049ff0 000ff0 000004 04  WA  0   0  4
  [23] .got.plt          PROGBITS        08049ff4 000ff4 000018 04  WA  0   0  4
  [24] .data             PROGBITS        0804a00c 00100c 000008 00  WA  0   0  4
  [25] .bss              NOBITS          0804a014 001014 000008 00  WA  0   0  4
  [26] .comment          PROGBITS        00000000 001014 00002a 01  MS  0   0  1
  [27] .shstrtab         STRTAB          00000000 00103e 0000fc 00      0   0  1
  [28] .symtab           SYMTAB          00000000 0015ec 000410 10     29  45  4
  [29] .strtab           STRTAB          00000000 0019fc 0001f9 00      0   0  1
Key to Flags:
  W (write), A (alloc), X (execute), M (merge), S (strings)
  I (info), L (link order), G (group), T (TLS), E (exclude), x (unknown)
  O (extra OS processing required) o (OS specific), p (processor specific)

There are no section groups in this file.

Program Headers:
  Type           Offset   VirtAddr   PhysAddr   FileSiz MemSiz  Flg Align
  PHDR           0x000034 0x08048034 0x08048034 0x00120 0x00120 R E 0x4
  INTERP         0x000154 0x08048154 0x08048154 0x00013 0x00013 R   0x1
      [Requesting program interpreter: /lib/ld-linux.so.2]
  LOAD           0x000000 0x08048000 0x08048000 0x005e4 0x005e4 R E 0x1000
  LOAD           0x000f14 0x08049f14 0x08049f14 0x00100 0x00108 RW  0x1000
  DYNAMIC        0x000f28 0x08049f28 0x08049f28 0x000c8 0x000c8 RW  0x4
  NOTE           0x000168 0x08048168 0x08048168 0x00044 0x00044 R   0x4
  GNU_EH_FRAME   0x0004ec 0x080484ec 0x080484ec 0x00034 0x00034 R   0x4
  GNU_STACK      0x000000 0x00000000 0x00000000 0x00000 0x00000 RW  0x4
  GNU_RELRO      0x000f14 0x08049f14 0x08049f14 0x000ec 0x000ec R   0x1

 Section to Segment mapping:
  Segment Sections...
   00
   01     .interp
   02     .interp .note.ABI-tag .note.gnu.build-id .gnu.hash .dynsym .dynstr .gnu.version .gnu.version_r .rel.dyn .rel.plt .init .plt .text .fini .rodata .eh_frame_hdr .eh_frame
   03     .ctors .dtors .jcr .dynamic .got .got.plt .data .bss
   04     .dynamic
   05     .note.ABI-tag .note.gnu.build-id
   06     .eh_frame_hdr
   07
   08     .ctors .dtors .jcr .dynamic .got

Dynamic section at offset 0xf28 contains 20 entries:
  Tag        Type                         Name/Value
 0x00000001 (NEEDED)                     Shared library: [libc.so.6]
 0x0000000c (INIT)                       0x80482b4
 0x0000000d (FINI)                       0x80484bc
 0x6ffffef5 (GNU_HASH)                   0x80481ac
 0x00000005 (STRTAB)                     0x804821c
 0x00000006 (SYMTAB)                     0x80481cc
 0x0000000a (STRSZ)                      76 (bytes)
 0x0000000b (SYMENT)                     16 (bytes)
 0x00000015 (DEBUG)                      0x0
 0x00000003 (PLTGOT)                     0x8049ff4
 0x00000002 (PLTRELSZ)                   24 (bytes)
 0x00000014 (PLTREL)                     REL
 0x00000017 (JMPREL)                     0x804829c
 0x00000011 (REL)                        0x8048294
 0x00000012 (RELSZ)                      8 (bytes)
 0x00000013 (RELENT)                     8 (bytes)
 0x6ffffffe (VERNEED)                    0x8048274
 0x6fffffff (VERNEEDNUM)                 1
 0x6ffffff0 (VERSYM)                     0x8048268
 0x00000000 (NULL)                       0x0

Relocation section '.rel.dyn' at offset 0x294 contains 1 entries:
 Offset     Info    Type            Sym.Value  Sym. Name
08049ff0  00000206 R_386_GLOB_DAT    00000000   __gmon_start__

Relocation section '.rel.plt' at offset 0x29c contains 3 entries:
 Offset     Info    Type            Sym.Value  Sym. Name
0804a000  00000107 R_386_JUMP_SLOT   00000000   printf
0804a004  00000207 R_386_JUMP_SLOT   00000000   __gmon_start__
0804a008  00000307 R_386_JUMP_SLOT   00000000   __libc_start_main

There are no unwind sections in this file.

Symbol table '.dynsym' contains 5 entries:
   Num:    Value  Size Type    Bind   Vis      Ndx Name
     0: 00000000     0 NOTYPE  LOCAL  DEFAULT  UND
     1: 00000000     0 FUNC    GLOBAL DEFAULT  UND printf@GLIBC_2.0 (2)
     2: 00000000     0 NOTYPE  WEAK   DEFAULT  UND __gmon_start__
     3: 00000000     0 FUNC    GLOBAL DEFAULT  UND __libc_start_main@GLIBC_2.0 (2)
     4: 080484dc     4 OBJECT  GLOBAL DEFAULT   15 _IO_stdin_used

Symbol table '.symtab' contains 65 entries:
   Num:    Value  Size Type    Bind   Vis      Ndx Name
     0: 00000000     0 NOTYPE  LOCAL  DEFAULT  UND
     1: 08048154     0 SECTION LOCAL  DEFAULT    1
     2: 08048168     0 SECTION LOCAL  DEFAULT    2
     3: 08048188     0 SECTION LOCAL  DEFAULT    3
     4: 080481ac     0 SECTION LOCAL  DEFAULT    4
     5: 080481cc     0 SECTION LOCAL  DEFAULT    5
     6: 0804821c     0 SECTION LOCAL  DEFAULT    6
     7: 08048268     0 SECTION LOCAL  DEFAULT    7
     8: 08048274     0 SECTION LOCAL  DEFAULT    8
     9: 08048294     0 SECTION LOCAL  DEFAULT    9
    10: 0804829c     0 SECTION LOCAL  DEFAULT   10
    11: 080482b4     0 SECTION LOCAL  DEFAULT   11
    12: 080482f0     0 SECTION LOCAL  DEFAULT   12
    13: 08048330     0 SECTION LOCAL  DEFAULT   13
    14: 080484bc     0 SECTION LOCAL  DEFAULT   14
    15: 080484d8     0 SECTION LOCAL  DEFAULT   15
    16: 080484ec     0 SECTION LOCAL  DEFAULT   16
    17: 08048520     0 SECTION LOCAL  DEFAULT   17
    18: 08049f14     0 SECTION LOCAL  DEFAULT   18
    19: 08049f1c     0 SECTION LOCAL  DEFAULT   19
    20: 08049f24     0 SECTION LOCAL  DEFAULT   20
    21: 08049f28     0 SECTION LOCAL  DEFAULT   21
    22: 08049ff0     0 SECTION LOCAL  DEFAULT   22
    23: 08049ff4     0 SECTION LOCAL  DEFAULT   23
    24: 0804a00c     0 SECTION LOCAL  DEFAULT   24
    25: 0804a014     0 SECTION LOCAL  DEFAULT   25
    26: 00000000     0 SECTION LOCAL  DEFAULT   26
    27: 00000000     0 FILE    LOCAL  DEFAULT  ABS crtstuff.c
    28: 08049f14     0 OBJECT  LOCAL  DEFAULT   18 __CTOR_LIST__
    29: 08049f1c     0 OBJECT  LOCAL  DEFAULT   19 __DTOR_LIST__
    30: 08049f24     0 OBJECT  LOCAL  DEFAULT   20 __JCR_LIST__
    31: 08048360     0 FUNC    LOCAL  DEFAULT   13 __do_global_dtors_aux
    32: 0804a014     1 OBJECT  LOCAL  DEFAULT   25 completed.6086
    33: 0804a018     4 OBJECT  LOCAL  DEFAULT   25 dtor_idx.6088
    34: 080483c0     0 FUNC    LOCAL  DEFAULT   13 frame_dummy
    35: 00000000     0 FILE    LOCAL  DEFAULT  ABS crtstuff.c
    36: 08049f18     0 OBJECT  LOCAL  DEFAULT   18 __CTOR_END__
    37: 080485e0     0 OBJECT  LOCAL  DEFAULT   17 __FRAME_END__
    38: 08049f24     0 OBJECT  LOCAL  DEFAULT   20 __JCR_END__
    39: 08048490     0 FUNC    LOCAL  DEFAULT   13 __do_global_ctors_aux
    40: 00000000     0 FILE    LOCAL  DEFAULT  ABS a.c
    41: 08049f14     0 NOTYPE  LOCAL  DEFAULT   18 __init_array_end
    42: 08049f28     0 OBJECT  LOCAL  DEFAULT   21 _DYNAMIC
    43: 08049f14     0 NOTYPE  LOCAL  DEFAULT   18 __init_array_start
    44: 08049ff4     0 OBJECT  LOCAL  DEFAULT   23 _GLOBAL_OFFSET_TABLE_
    45: 08048480     2 FUNC    GLOBAL DEFAULT   13 __libc_csu_fini
    46: 08048482     0 FUNC    GLOBAL HIDDEN    13 __i686.get_pc_thunk.bx
    47: 0804a00c     0 NOTYPE  WEAK   DEFAULT   24 data_start
    48: 00000000     0 FUNC    GLOBAL DEFAULT  UND printf@@GLIBC_2.0
    49: 0804a014     0 NOTYPE  GLOBAL DEFAULT  ABS _edata
    50: 080484bc     0 FUNC    GLOBAL DEFAULT   14 _fini
    51: 08049f20     0 OBJECT  GLOBAL HIDDEN    19 __DTOR_END__
    52: 0804a00c     0 NOTYPE  GLOBAL DEFAULT   24 __data_start
    53: 00000000     0 NOTYPE  WEAK   DEFAULT  UND __gmon_start__
    54: 0804a010     0 OBJECT  GLOBAL HIDDEN    24 __dso_handle
    55: 080484dc     4 OBJECT  GLOBAL DEFAULT   15 _IO_stdin_used
    56: 00000000     0 FUNC    GLOBAL DEFAULT  UND __libc_start_main@@GLIBC_
    57: 08048410    97 FUNC    GLOBAL DEFAULT   13 __libc_csu_init
    58: 0804a01c     0 NOTYPE  GLOBAL DEFAULT  ABS _end
    59: 08048330     0 FUNC    GLOBAL DEFAULT   13 _start
    60: 080484d8     4 OBJECT  GLOBAL DEFAULT   15 _fp_hw
    61: 0804a014     0 NOTYPE  GLOBAL DEFAULT  ABS __bss_start
    62: 080483e4    40 FUNC    GLOBAL DEFAULT   13 main
    63: 00000000     0 NOTYPE  WEAK   DEFAULT  UND _Jv_RegisterClasses
    64: 080482b4     0 FUNC    GLOBAL DEFAULT   11 _init

Histogram for `.gnu.hash' bucket list length (total of 2 buckets):
 Length  Number     % of total  Coverage
      0  1          ( 50.0%)
      1  1          ( 50.0%)    100.0%

Version symbols section '.gnu.version' contains 5 entries:
 Addr: 0000000008048268  Offset: 0x000268  Link: 5 (.dynsym)
  000:   0 (*local*)       2 (GLIBC_2.0)     0 (*local*)       2 (GLIBC_2.0)
  004:   1 (*global*)

Version needs section '.gnu.version_r' contains 1 entries:
 Addr: 0x0000000008048274  Offset: 0x000274  Link: 6 (.dynstr)
  000000: Version: 1  File: libc.so.6  Cnt: 1
  0x0010:   Name: GLIBC_2.0  Flags: none  Version: 2

Notes at offset 0x00000168 with length 0x00000020:
  Owner                 Data size   Description
  GNU                  0x00000010   NT_GNU_ABI_TAG (ABI version tag)
    OS: Linux, ABI: 2.6.15

Notes at offset 0x00000188 with length 0x00000024:
  Owner                 Data size   Description
  GNU                  0x00000014   NT_GNU_BUILD_ID (unique build ID bitstring)
    Build ID: 17fb9651029b6a8543bfafec9eea23bd16454e65

```

关于 ELF 文件格式的参考：[`www.cnblogs.com/xmphoenix/archive/2011/10/23/2221879.html`](http://www.cnblogs.com/xmphoenix/archive/2011/10/23/2221879.html) © 版权所有 2014, Colin http://blog.me115.com. 由 [Sphinx](http://sphinx-doc.org/) 1.3.5 创建。

## 14.1\. 常用参数说明

*   -f 显示文件头信息
*   -D 反汇编所有 section (-d 反汇编特定 section)
*   -h 显示目标文件各个 section 的头部摘要信息
*   -x 显示所有可用的头信息，包括符号表、重定位入口。-x 等价于 -a -f -h -r -t 同时指定。
*   -i 显示对于 -b 或者 -m 选项可用的架构和目标格式列表。
*   -r 显示文件的重定位入口。如果和-d 或者-D 一起使用，重定位部分以反汇编后的格式显示出来。
*   -R 显示文件的动态重定位入口，仅仅对于动态目标文件有意义，比如某些共享库。
*   -S 尽可能反汇编出源代码，尤其当编译的时候指定了-g 这种调试参数时，效果比较明显。隐含了-d 参数。
*   -t 显示文件的符号表入口。类似于 nm -s 提供的信息

## 14.2\. 示例

查看本机目标结构（使用大端还是小端存储）:

```
$objdump -i

```

反汇编程序:

```
$objdump -d main.o

```

显示符号表入口:

```
$objdump  -t main.o

```

希望显示可用的简洁帮助信息，直接输入 objdump 即可；（objdump -H) © 版权所有 2014, Colin http://blog.me115.com. 由 [Sphinx](http://sphinx-doc.org/) 1.3.5 创建。

## 15.1\. 选项说明

*   -a 或–debug-syms：显示所有的符号，包括 debugger-only symbols。
*   -B：等同于–format=bsd，用来兼容 MIPS 的 nm。
*   -C 或–demangle：将低级符号名解析(demangle)成用户级名字。这样可以使得 C++函数名具有可读性。
*   –no-demangle：默认的选项，不需要将低级符号名解析成用户级名。
*   -D 或–dynamic：显示动态符号。该任选项仅对于动态目标(例如特定类型的共享库)有意义。
*   -f format：使用 format 格式输出。format 可以选取 bsd、sysv 或 posix，该选项在 GNU 的 nm 中有用。默认为 bsd。
*   -g 或–extern-only：仅显示外部符号。
*   -n、-v 或–numeric-sort：按符号对应地址的顺序排序，而非按符号名的字符顺序。
*   -p 或–no-sort：按目标文件中遇到的符号顺序显示，不排序。
*   -P 或–portability：使用 POSIX.2 标准输出格式代替默认的输出格式。等同于使用任选项-f posix。
*   -s 或–print-armap：当列出库中成员的符号时，包含索引。索引的内容包含：哪些模块包含哪些名字的映射。
*   -r 或–reverse-sort：反转排序的顺序(例如，升序变为降序)。
*   –size-sort：按大小排列符号顺序。该大小是按照一个符号的值与它下一个符号的值进行计算的。
*   –target=bfdname：指定一个目标代码的格式，而非使用系统的默认格式。
*   -u 或–undefined-only：仅显示没有定义的符号(那些外部符号)。
*   –defined-only:仅显示定义的符号。
*   -l 或–line-numbers：对每个符号，使用调试信息来试图找到文件名和行号。
*   -V 或–version：显示 nm 的版本号。
*   –help：显示 nm 的选项。

## 15.2\. 符号说明

对于每一个符号来说，其类型如果是小写的，则表明该符号是 local 的；大写则表明该符号是 global(external)的。

*   A 该符号的值是绝对的，在以后的链接过程中，不允许进行改变。这样的符号值，常常出现在中断向量表中，例如用符号来表示各个中断向量函数在中断向量表中的位置。

*   B 该符号的值出现在非初始化数据段(bss)中。例如，在一个文件中定义全局 static int test。则该符号 test 的类型为 b，位于 bss section 中。其值表示该符号在 bss 段中的偏移。一般而言，bss 段分配于 RAM 中。

*   C 该符号为 common。common symbol 是未初始话数据段。该符号没有包含于一个普通 section 中。只有在链接过程中才进行分配。符号的值表示该符号需要的字节数。例如在一个 c 文件中，定义 int test，并且该符号在别的地方会被引用，则该符号类型即为 C。否则其类型为 B。

*   D 该符号位于初始化数据段中。一般来说，分配到 data section 中。

    例如：定义全局 int baud_table[5] = {9600, 19200, 38400, 57600, 115200}，会分配到初始化数据段中。

*   G 该符号也位于初始化数据段中。主要用于 small object 提高访问 small data object 的一种方式。

*   I 该符号是对另一个符号的间接引用。

*   N 该符号是一个 debugging 符号。

*   R 该符号位于只读数据区。

    *   例如定义全局 const int test[] = {123, 123};则 test 就是一个只读数据区的符号。
    *   值得注意的是，如果在一个函数中定义 const char *test = “abc”, const char test_int = 3。使用 nm 都不会得到符号信息，但是字符串”abc”分配于只读存储器中，test 在 rodata section 中，大小为 4。

*   S 符号位于非初始化数据区，用于 small object。

*   T 该符号位于代码区 text section。

*   U 该符号在当前文件中是未定义的，即该符号的定义在别的文件中。

    例如，当前文件调用另一个文件中定义的函数，在这个被调用的函数在当前就是未定义的；但是在定义它的文件中类型是 T。但是对于全局变量来说，在定义它的文件中，其符号类型为 C，在使用它的文件中，其类型为 U。

*   V 该符号是一个 weak object。

*   W The symbol is a weak symbol that has not been specifically tagged as a weak object symbol.

*   ? 该符号类型没有定义

*库或对象名* 如果您指定了 -A 选项，则 nm 命令只报告与该文件有关的或者库或者对象名。

## 15.3\. 示例

1.  寻找特殊标识

有时会碰到一个编译了但没有链接的代码，那是因为它缺失了标识符；这种情况，可以用 nm 和 objdump、readelf 命令来查看程序的符号表；所有这些命令做的工作基本一样；

比如连接器报错有未定义的标识符；大多数情况下，会发生在库的缺失或企图链接一个错误版本的库的时候；浏览目标代码来寻找一个特殊标识符的引用:

```
nm -uCA *.o | grep foo

```

-u 选项限制了每个目标文件中未定义标识符的输出。-A 选项用于显示每个标识符的文件名信息；对于 C++代码，常用的还有-C 选项，它也为解码这些标识符；

注解

objdump、readld 命令可以完成同样的任务。等效命令为： $objdump -t $readelf -s

2.  列出 a.out 对象文件的静态和外部符:

    ```
    $nm -e a.out

    ```

3.  以十六进制显示符号大小和值并且按值排序符号:

    ```
    $nm -xv a.out

    ```

4.  显示 libc.a 中所有 64 位对象符号，忽略所有 32 位对象:

    ```
    $nm -X64 /usr/lib/libc.a

    ``` © 版权所有 2014, Colin http://blog.me115.com. 由 [Sphinx](http://sphinx-doc.org/) 1.3.5 创建。

## 17.1\. 命令格式

wget [参数] [URL 地址]

## 17.2\. 命令参数：

### 启动参数：

*   -V, –version 显示 wget 的版本后退出
*   -h, –help 打印语法帮助
*   -b, –background 启动后转入后台执行
*   -e, –execute=COMMAND 执行’.wgetrc’格式的命令，wgetrc 格式参见/etc/wgetrc 或~/.wgetrc

### 记录和输入文件参数

*   -o, –output-file=FILE 把记录写到 FILE 文件中
*   -a, –append-output=FILE 把记录追加到 FILE 文件中
*   -d, –debug 打印调试输出
*   -q, –quiet 安静模式(没有输出)
*   -v, –verbose 冗长模式(这是缺省设置)
*   -nv, –non-verbose 关掉冗长模式，但不是安静模式
*   -i, –input-file=FILE 下载在 FILE 文件中出现的 URLs
*   -F, –force-html 把输入文件当作 HTML 格式文件对待
*   -B, –base=URL 将 URL 作为在-F -i 参数指定的文件中出现的相对链接的前缀

–sslcertfile=FILE 可选客户端证书 –sslcertkey=KEYFILE 可选客户端证书的 KEYFILE –egd-file=FILE 指定 EGD socket 的文件名

### 下载参数

*   -bind-address=ADDRESS 指定本地使用地址(主机名或 IP，当本地有多个 IP 或名字时使用)
*   -t, –tries=NUMBER 设定最大尝试链接次数(0 表示无限制).
*   -O –output-document=FILE 把文档写到 FILE 文件中
*   -nc, –no-clobber 不要覆盖存在的文件或使用.#前缀
*   -c, –continue 接着下载没下载完的文件
*   -progress=TYPE 设定进程条标记
*   -N, –timestamping 不要重新下载文件除非比本地文件新
*   -S, –server-response 打印服务器的回应
*   -T, –timeout=SECONDS 设定响应超时的秒数
*   -w, –wait=SECONDS 两次尝试之间间隔 SECONDS 秒
*   -waitretry=SECONDS 在重新链接之间等待 1…SECONDS 秒
*   -random-wait 在下载之间等待 0…2*WAIT 秒
*   -Y, -proxy=on/off 打开或关闭代理
*   -Q, -quota=NUMBER 设置下载的容量限制
*   -limit-rate=RATE 限定下载输率

### 目录参数

*   -nd –no-directories 不创建目录
*   -x, –force-directories 强制创建目录
*   -nH, –no-host-directories 不创建主机目录
*   -P, –directory-prefix=PREFIX 将文件保存到目录 PREFIX/…
*   -cut-dirs=NUMBER 忽略 NUMBER 层远程目录

### HTTP 选项参数

*   -http-user=USER 设定 HTTP 用户名为 USER.
*   -http-passwd=PASS 设定 http 密码为 PASS
*   -C, –cache=on/off 允许/不允许服务器端的数据缓存 (一般情况下允许)
*   -E, –html-extension 将所有 text/html 文档以.html 扩展名保存
*   -ignore-length 忽略 ‘Content-Length’头域
*   -header=STRING 在 headers 中插入字符串 STRING
*   -proxy-user=USER 设定代理的用户名为 USER
*   proxy-passwd=PASS 设定代理的密码为 PASS
*   referer=URL 在 HTTP 请求中包含 ‘Referer: URL’头
*   -s, –save-headers 保存 HTTP 头到文件
*   -U, –user-agent=AGENT 设定代理的名称为 AGENT 而不是 Wget/VERSION
*   no-http-keep-alive 关闭 HTTP 活动链接 (永远链接)
*   cookies=off 不使用 cookies
*   load-cookies=FILE 在开始会话前从文件 FILE 中加载 cookie
*   save-cookies=FILE 在会话结束后将 cookies 保存到 FILE 文件中

### FTP 选项参数

*   -nr, –dont-remove-listing 不移走 ‘.listing’文件
*   -g, –glob=on/off 打开或关闭文件名的 globbing 机制
*   passive-ftp 使用被动传输模式 (缺省值).
*   active-ftp 使用主动传输模式
*   retr-symlinks 在递归的时候，将链接指向文件(而不是目录)

### 递归下载参数

*   -r, –recursive 递归下载－－慎用!

*   -l, –level=NUMBER 最大递归深度 (inf 或 0 代表无穷)

*   -delete-after 在现在完毕后局部删除文件

*   -k, –convert-links 转换非相对链接为相对链接

*   -K, –backup-converted 在转换文件 X 之前，将之备份为 X.orig

*   -m, –mirror 等价于 -r -N -l inf -nr

*   -p, –page-requisites 下载显示 HTML 文件的所有图片

    递归下载中的包含和不包含(accept/reject)：

*   -A, –accept=LIST 分号分隔的被接受扩展名的列表

*   -R, –reject=LIST 分号分隔的不被接受的扩展名的列表

*   -D, –domains=LIST 分号分隔的被接受域的列表

*   -exclude-domains=LIST 分号分隔的不被接受的域的列表

*   -follow-ftp 跟踪 HTML 文档中的 FTP 链接

*   -follow-tags=LIST 分号分隔的被跟踪的 HTML 标签的列表

*   -G, –ignore-tags=LIST 分号分隔的被忽略的 HTML 标签的列表

*   -H, –span-hosts 当递归时转到外部主机

*   -L, –relative 仅仅跟踪相对链接

*   -I, –include-directories=LIST 允许目录的列表

*   -X, –exclude-directories=LIST 不被包含目录的列表

*   -np, –no-parent 不要追溯到父目录

wget -S –spider url 不下载只显示过程

## 17.3\. 使用实例

### 实例 1：使用 wget 下载单个文件

```
$wget http://www.minjieren.com/wordpress-3.1-zh_CN.zip

```

说明：以上例子从网络下载一个文件并保存在当前目录，在下载的过程中会显示进度条，包含（下载完成百分比，已经下载的字节，当前下载速度，剩余下载时间）。

### 实例 2：使用 wget -O 下载并以不同的文件名保存

```
$wget -O wordpress.zip http://www.minjieren.com/download.aspx?id=1080

```

wget 默认会以最后一个符合”/”的后面的字符来命令，对于动态链接的下载通常文件名会不正确。

### 实例 3：使用 wget –limit -rate 限速下载

```
$wget --limit-rate=300k http://www.minjieren.com/wordpress-3.1-zh_CN.zip

```

当你执行 wget 的时候，它默认会占用全部可能的宽带下载。但是当你准备下载一个大文件，而你还需要下载其它文件时就有必要限速了。

### 实例 4：使用 wget -c 断点续传

```
$wget -c http://www.minjieren.com/wordpress-3.1-zh_CN.zip

```

使用 wget -c 重新启动下载中断的文件，对于我们下载大文件时突然由于网络等原因中断非常有帮助，我们可以继续接着下载而不是重新下载一个文件。需要继续中断的下载时可以使用-c 参数。

### 实例 5：使用 wget -b 后台下载

```
$wget -b http://www.minjieren.com/wordpress-3.1-zh_CN.zip
Continuing in background, pid 1840.
Output will be written to 'wget-log'.

```

对于下载非常大的文件的时候，我们可以使用参数-b 进行后台下载。

你可以使用以下命令来察看下载进度:

```
$tail -f wget-log

```

### 实例 6：伪装代理名称下载

```
wget --user-agent="Mozilla/5.0 (Windows; U; Windows NT 6.1; en-US) AppleWebKit/534.16 (KHTML, like Gecko) Chrome/10.0.648.204 Safari/534.16" http://www.minjieren.com/wordpress-3.1-zh_CN.zip

```

有些网站能通过根据判断代理名称不是浏览器而拒绝你的下载请求。不过你可以通过–user-agent 参数伪装。

### 实例 7：使用 wget -i 下载多个文件

首先，保存一份下载链接文件,接着使用这个文件和参数-i 下载:

```
$cat > filelist.txt
url1
url2
url3
url4

$wget -i filelist.txt

```

### 实例 8：使用 wget –mirror 镜像网站

```
$wget --mirror -p --convert-links -P ./LOCAL URL

```

下载整个网站到本地

*   -miror:开户镜像下载
*   -p:下载所有为了 html 页面显示正常的文件
*   -convert-links:下载后，转换成本地的链接
*   -P ./LOCAL：保存所有文件和目录到本地指定目录

### 实例 9: 使用 wget -r -A 下载指定格式文件

```
$wget -r -A.pdf url

```

可以在以下情况使用该功能：

*   下载一个网站的所有图片
*   下载一个网站的所有视频
*   下载一个网站的所有 PDF 文件

### 实例 10：使用 wget FTP 下载

```
$wget ftp-url
$wget --ftp-user=USERNAME --ftp-password=PASSWORD url

```

可以使用 wget 来完成 ftp 链接的下载

*   使用 wget 匿名 ftp 下载：wget ftp-url
*   使用 wget 用户名和密码认证的 ftp 下载:wget –ftp-user=USERNAME –ftp-password=PASSWORD url

## 17.4\. 编译安装

使用如下命令编译安装:

```
tar zxvf wget-1.9.1.tar.gz
cd wget-1.9.1
./configure
make
make install

``` © 版权所有 2014, Colin http://blog.me115.com. 由 [Sphinx](http://sphinx-doc.org/) 1.3.5 创建。

## 18.1\. 命令格式：

scp [参数] [原路径] [目标路径]

## 18.2\. 命令参数：

*   -1 强制 scp 命令使用协议 ssh1
*   -2 强制 scp 命令使用协议 ssh2
*   -4 强制 scp 命令只使用 IPv4 寻址
*   -6 强制 scp 命令只使用 IPv6 寻址
*   -B 使用批处理模式（传输过程中不询问传输口令或短语）
*   -C 允许压缩。（将-C 标志传递给 ssh，从而打开压缩功能）
*   -p 留原文件的修改时间，访问时间和访问权限。
*   -q 不显示传输进度条。
*   -r 递归复制整个目录。
*   -v 详细方式显示输出。scp 和 ssh(1)会显示出整个过程的调试信息。这些信息用于调试连接，验证和配置问题。
*   -c cipher 以 cipher 将数据传输进行加密，这个选项将直接传递给 ssh。
*   -F ssh_config 指定一个替代的 ssh 配置文件，此参数直接传递给 ssh。
*   -i identity_file 从指定文件中读取传输时使用的密钥文件，此参数直接传递给 ssh。
*   -l limit 限定用户所能使用的带宽，以 Kbit/s 为单位。
*   -o ssh_option 如果习惯于使用 ssh_config(5)中的参数传递方式，
*   -P port 注意是大写的 P, port 是指定数据传输用到的端口号
*   -S program 指定加密传输时所使用的程序。此程序必须能够理解 ssh(1)的选项。

## 18.3\. 使用说明

### 从本地服务器复制到远程服务器

复制文件:

```
$scp local_file remote_username@remote_ip:remote_folder
$scp local_file remote_username@remote_ip:remote_file
$scp local_file remote_ip:remote_folder
$scp local_file remote_ip:remote_file

```

指定了用户名，命令执行后需要输入用户密码；如果不指定用户名，命令执行后需要输入用户名和密码；

复制目录:

```
$scp -r local_folder remote_username@remote_ip:remote_folder
$scp -r local_folder remote_ip:remote_folder

```

第 1 个指定了用户名，命令执行后需要输入用户密码； 第 2 个没有指定用户名，命令执行后需要输入用户名和密码；

注解

从远程复制到本地的 scp 命令与上面的命令一样，只要将从本地复制到远程的命令后面 2 个参数互换顺序就行了。

## 18.4\. 使用示例

### 实例 1：从远处复制文件到本地目录

```
$scp root@10.6.159.147:/opt/soft/demo.tar /opt/soft/

```

说明： 从 10.6.159.147 机器上的/opt/soft/的目录中下载 demo.tar 文件到本地/opt/soft/目录中

### 实例 2：从远处复制到本地

```
$scp -r root@10.6.159.147:/opt/soft/test /opt/soft/

```

说明： 从 10.6.159.147 机器上的/opt/soft/中下载 test 目录到本地的/opt/soft/目录来。

### 实例 3：上传本地文件到远程机器指定目录

```
$scp /opt/soft/demo.tar root@10.6.159.147:/opt/soft/scptest

```

说明： 复制本地 opt/soft/目录下的文件 demo.tar 到远程机器 10.6.159.147 的 opt/soft/scptest 目录

### 实例 4：上传本地目录到远程机器指定目录

```
$scp -r /opt/soft/test root@10.6.159.147:/opt/soft/scptest

```

说明： 上传本地目录 /opt/soft/test 到远程机器 10.6.159.147 上/opt/soft/scptest 的目录中 © 版权所有 2014, Colin http://blog.me115.com. 由 [Sphinx](http://sphinx-doc.org/) 1.3.5 创建。

## 19.1\. 命令格式

crontab [-u user] [ -e | -l | -r ]

## 19.2\. 命令参数

*   -u user：用来设定某个用户的 crontab 服务；
*   file：file 是命令文件的名字,表示将 file 做为 crontab 的任务列表文件并载入 crontab。如果在命令行中没有指定这个文件，crontab 命令将接受标准输入（键盘）上键入的命令，并将它们载入 crontab。
*   -e：编辑某个用户的 crontab 文件内容。如果不指定用户，则表示编辑当前用户的 crontab 文件。
*   -l：显示某个用户的 crontab 文件内容，如果不指定用户，则表示显示当前用户的 crontab 文件内容。
*   -r：从/var/spool/cron 目录中删除某个用户的 crontab 文件，如果不指定用户，则默认删除当前用户的 crontab 文件。
*   -i：在删除用户的 crontab 文件时给确认提示。

## 19.3\. crontab 的文件格式

分 时 日 月 星期 要运行的命令

*   第 1 列分钟 1～59
*   第 2 列小时 1～23（0 表示子夜）
*   第 3 列日 1～31
*   第 4 列月 1～12
*   第 5 列星期 0～6（0 表示星期天）
*   第 6 列要运行的命令

## 19.4\. 常用方法

### 创建一个新的 crontab 文件

向 cron 进程提交一个 crontab 文件之前，首先要设置环境变量 EDITOR。cron 进程根据它来确定使用哪个编辑器编辑 crontab 文件。9 9 %的 UNIX 和 LINUX 用户都使用 vi，如果你也是这样，那么你就编辑$HOME 目录下的. profile 文件，在其中加入这样一行:

```
EDITOR=vi; export EDITOR

```

然后保存并退出。不妨创建一个名为<user> cron 的文件，其中<user>是用户名，例如， davecron。在该文件中加入如下的内容。

```
# (put your own initials here)echo the date to the console every
# 15minutes between 6pm and 6am
0,15,30,45 18-06 * * * /bin/echo 'date' > /dev/console

```

保存并退出。注意前面 5 个域用空格分隔。

在上面的例子中，系统将每隔 1 5 分钟向控制台输出一次当前时间。如果系统崩溃或挂起，从最后所显示的时间就可以一眼看出系统是什么时间停止工作的。在有些系统中，用 tty1 来表示控制台，可以根据实际情况对上面的例子进行相应的修改。为了提交你刚刚创建的 crontab 文件，可以把这个新创建的文件作为 cron 命令的参数:

```
$ crontab davecron

```

现在该文件已经提交给 cron 进程，它将每隔 1 5 分钟运行一次。同时，新创建文件的一个副本已经被放在/var/spool/cron 目录中，文件名就是用户名(即 dave)。

### 列出 crontab 文件

使用-l 参数列出 crontab 文件:

```
$ crontab -l
0,15,30,45,18-06 * * * /bin/echo `date` > dev/tty1

```

可以使用这种方法在$HOME 目录中对 crontab 文件做一备份:

```
$ crontab -l > $HOME/mycron

```

这样，一旦不小心误删了 crontab 文件，可以用上一节所讲述的方法迅速恢复。

### 编辑 crontab 文件

如果希望添加、删除或编辑 crontab 文件中的条目，而 EDITOR 环境变量又设置为 vi，那么就可以用 vi 来编辑 crontab 文件:

```
$ crontab -e

```

可以像使用 vi 编辑其他任何文件那样修改 crontab 文件并退出。如果修改了某些条目或添加了新的条目，那么在保存该文件时， cron 会对其进行必要的完整性检查。如果其中的某个域出现了超出允许范围的值，它会提示你。 我们在编辑 crontab 文件时，没准会加入新的条目。例如，加入下面的一条：

```
# DT:delete core files,at 3.30am on 1,7,14,21,26,26 days of each month
30 3 1,7,14,21,26 * * /bin/find -name 'core' -exec rm {} \;

```

保存并退出。

注解

最好在 crontab 文件的每一个条目之上加入一条注释，这样就可以知道它的功能、运行时间，更为重要的是，知道这是哪位用户的定时作业。

### 删除 crontab 文件

```
$crontab -r

```

## 19.5\. 使用实例

### 实例 1：每 1 分钟执行一次 myCommand

```
* * * * * myCommand

```

### 实例 2：每小时的第 3 和第 15 分钟执行

```
3,15 * * * * myCommand

```

### 实例 3：在上午 8 点到 11 点的第 3 和第 15 分钟执行

```
3,15 8-11 * * * myCommand

```

### 实例 4：每隔两天的上午 8 点到 11 点的第 3 和第 15 分钟执行

```
3,15 8-11 */2  *  * myCommand

```

### 实例 5：每周一上午 8 点到 11 点的第 3 和第 15 分钟执行

```
3,15 8-11 * * 1 myCommand

```

### 实例 6：每晚的 21:30 重启 smb

```
30 21 * * * /etc/init.d/smb restart

```

### 实例 7：每月 1、10、22 日的 4 : 45 重启 smb

```
45 4 1,10,22 * * /etc/init.d/smb restart

```

### 实例 8：每周六、周日的 1 : 10 重启 smb

```
10 1 * * 6,0 /etc/init.d/smb restart

```

### 实例 9：每天 18 : 00 至 23 : 00 之间每隔 30 分钟重启 smb

```
0,30 18-23 * * * /etc/init.d/smb restart

```

### 实例 10：每星期六的晚上 11 : 00 pm 重启 smb

```
0 23 * * 6 /etc/init.d/smb restart

```

### 实例 11：每一小时重启 smb

```
* */1 * * * /etc/init.d/smb restart

```

### 实例 12：晚上 11 点到早上 7 点之间，每隔一小时重启 smb

```
* 23-7/1 * * * /etc/init.d/smb restart

```

## 19.6\. 使用注意事项

### 注意环境变量问题

有时我们创建了一个 crontab，但是这个任务却无法自动执行，而手动执行这个任务却没有问题，这种情况一般是由于在 crontab 文件中没有配置环境变量引起的。

在 crontab 文件中定义多个调度任务时，需要特别注环境变量的设置，因为我们手动执行某个任务时，是在当前 shell 环境下进行的，程序当然能找到环境变量，而系统自动执行任务调度时，是不会加载任何环境变量的，因此，就需要在 crontab 文件中指定任务运行所需的所有环境变量，这样，系统执行任务调度时就没有问题了。

不要假定 cron 知道所需要的特殊环境，它其实并不知道。所以你要保证在 shelll 脚本中提供所有必要的路径和环境变量，除了一些自动设置的全局变量。所以注意如下 3 点：

1.  脚本中涉及文件路径时写全局路径；

2.  脚本执行要用到 java 或其他环境变量时，通过 source 命令引入环境变量，如:

    ```
    cat start_cbp.sh
    !/bin/sh
    source /etc/profile
    export RUN_CONF=/home/d139/conf/platform/cbp/cbp_jboss.conf
    /usr/local/jboss-4.0.5/bin/run.sh -c mev &

    ```

3）当手动执行脚本 OK，但是 crontab 死活不执行时,很可能是环境变量惹的祸，可尝试在 crontab 中直接引入环境变量解决问题。如:

```
0 * * * * . /etc/profile;/bin/sh /var/www/java/audit_no_count/bin/restart_audit.sh

```

### 注意清理系统用户的邮件日志

每条任务调度执行完毕，系统都会将任务输出信息通过电子邮件的形式发送给当前系统用户，这样日积月累，日志信息会非常大，可能会影响系统的正常运行，因此，将每条任务进行重定向处理非常重要。 例如，可以在 crontab 文件中设置如下形式，忽略日志输出:

```
0 */3 * * * /usr/local/apache2/apachectl restart >/dev/null 2>&1

```

“/dev/null 2>&1”表示先将标准输出重定向到/dev/null，然后将标准错误重定向到标准输出，由于标准输出已经重定向到了/dev/null，因此标准错误也会重定向到/dev/null，这样日志输出问题就解决了。

### 系统级任务调度与用户级任务调度

系统级任务调度主要完成系统的一些维护操作，用户级任务调度主要完成用户自定义的一些任务，可以将用户级任务调度放到系统级任务调度来完成（不建议这么做），但是反过来却不行，root 用户的任务调度操作可以通过”crontab –uroot –e”来设置，也可以将调度任务直接写入/etc/crontab 文件，需要注意的是，如果要定义一个定时重启系统的任务，就必须将任务放到/etc/crontab 文件，即使在 root 用户下创建一个定时重启系统的任务也是无效的。

### 其他注意事项

新创建的 cron job，不会马上执行，至少要过 2 分钟才执行。如果重启 cron 则马上执行。

当 crontab 失效时，可以尝试/etc/init.d/crond restart 解决问题。或者查看日志看某个 job 有没有执行/报错 tail -f /var/log/cron。

千万别乱运行 crontab -r。它从 Crontab 目录（/var/spool/cron）中删除用户的 Crontab 文件。删除了该用户的所有 crontab 都没了。

在 crontab 中%是有特殊含义的，表示换行的意思。如果要用的话必须进行转义%，如经常用的 date ‘+%Y%m%d’在 crontab 里是不会执行的，应该换成 date ‘+%Y%m%d’。

更新系统时间时区后需要重启 cron,在 ubuntu 中服务名为 cron:

```
$service cron restart

```

ubuntu 下启动、停止与重启 cron:

```
$sudo /etc/init.d/cron start
$sudo /etc/init.d/cron stop
$sudo /etc/init.d/cron restart

``` © 版权所有 2014, Colin http://blog.me115.com. 由 [Sphinx](http://sphinx-doc.org/) 1.3.5 创建。

# 索引

**P** | **R**

## P

|  
Python 提高建议

PEP 287

 |

## R

|  
RFC

RFC 2822

 |

© 版权所有 2014, Colin http://blog.me115.com. 由 [Sphinx](http://sphinx-doc.org/) 1.3.5 创建。