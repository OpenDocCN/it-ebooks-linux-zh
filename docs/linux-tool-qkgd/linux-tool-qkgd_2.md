## 1.1\. 配置

查询可用的配置选项:

```
./configure --help

```

配置路径:

```
./configure --prefix=/usr/local/snmp

```

–prefix 是配置使用的最常用选项，设置程序安装的路径；

## 1.2\. 编译

编译使用 make 编译:

```
make -f myMakefile

```

通过-f 选项显示指定需要编译的 makefile；如果待使用 makefile 文件在当前路径，且文件名为以下几个，则不用显示指定：

makefile Makefile

### makefile 编写的要点

*   必须满足第一条规则，满足后停止
*   除第一条规则，其他无顺序

### makefile 中的全局自变量

*   $@目标文件名
*   @^所有前提名，除副本
*   @＋所有前提名，含副本
*   @＜一个前提名
*   @？所有新于目标文件的前提名
*   @*目标文件的基名称

注解

系统学习 makefile 的书写规则，请参考 跟我一起学 makefile [[1]](#id10)

### 更多选择 CMake

CMake 是一个跨平台的安装（编译）工具，可以用简单的语句来描述所有平台的安装(编译过程)。他能够输出各种各样的 makefile 或者 project 文件。使用 CMake，能够使程序员从复杂的编译连接过程中解脱出来。它使用一个名为 CMakeLists.txt 的文件来描述构建过程,可以生成标准的构建文件,如 Unix/Linux 的 Makefile 或 Windows Visual C++ 的 projects/workspaces 。

### 编译依赖的库

makefile 编译过程中所依赖的非标准库和头文件路径需要显示指明:

```
CPPFLAGS -I 标记非标准头文件存放路径
LDFLAGS  -L 标记非标准库存放路径

```

如果 CPPFLAGS 和 LDFLAGS 已在用户环境变量中设置并且导出（使用 export 关键字），就不用再显示指定；

```
make -f myMakefile LDFLAGS='-L/var/xxx/lib -L/opt/mysql/lib'
    CPPFLAGS='-I/usr/local/libcom/include -I/usr/local/libpng/include'

```

警告

链接多库时，多个库之间如果有依赖，需要注意书写的顺序，右边是左边的前提；

### g++编译

```
g++ -o unixApp unixApp.o a.o b.o

```

选项说明：

*   -o:指明生成的目标文件
*   -g：添加调试信息
*   -E: 查看中间文件

应用：查询宏展开的中间文件：

在 g++的编译选项中，添加 -E 选项，然后去掉-o 选项 ，重定向到一个文件中即可:

```
g++ -g -E unixApp.cpp  -I/opt/app/source > midfile

```

查询应用程序需要链接的库:

```
$ldd myprogrammer
    libstdc++.so.6 => /usr/lib64/libstdc++.so.6 (0x00000039a7e00000)
    libm.so.6 => /lib64/libm.so.6 (0x0000003996400000)
    libgcc_s.so.1 => /lib64/libgcc_s.so.1 (0x00000039a5600000)
    libc.so.6 => /lib64/libc.so.6 (0x0000003995800000)
    /lib64/ld-linux-x86-64.so.2 (0x0000003995400000)

```

注解

关于 ldd 的使用细节，参见 ldd 查看程序依赖库

## 1.3\. 安装

安装做的工作就简单多了，就是将生成的可执行文件拷贝到配置时设置的初始路径下:

```
$make install

```

其实 **install** 就是 makefile 中的一个规则，打开 makefile 文件后可以查看程序安装的所做的工作；

## 1.4\. 总结

configure make install g++

| [[1]](#id6) | 陈皓 跟我一起写 Makefile [`scc.qibebt.cas.cn/docs/linux/base/%B8%FA%CE%D2%D2%BB%C6%F0%D0%B4Makefile-%B3%C2%F0%A9.pdf`](http://scc.qibebt.cas.cn/docs/linux/base/%B8%FA%CE%D2%D2%BB%C6%F0%D0%B4Makefile-%B3%C2%F0%A9.pdf) | © 版权所有 2014, Colin http://blog.me115.com. 由 [Sphinx](http://sphinx-doc.org/) 1.3.5 创建。

## 2.1\. 进程调试

### gdb 程序交互调试

GDB 是一个由 GNU 开源组织发布的、UNIX/LINUX 操作系统下的、基于命令行的、功能强大的程序调试工具。

对于一名 Linux 下工作的 c++程序员，gdb 是必不可少的工具；

GDB 中的命令固然很多，但我们只需掌握其中十个左右的命令，就大致可以完成日常的基本的程序调试工作。

以下从一个完整的调试过程简单说明最基本的几个命令;

```
$gdb programmer     # 启动 gdb
>break main         # 设置断点
>run                # 运行调试程序
>next               # 单步调试
>print var1         # 在调试过程中，我们需要查看当前某个变量值的时候，使用 print 命令打印该值
>list               # 显示当前调试处的源代码
>info b             # 显示当前断点设置情况

```

当你完成了第一个程序调试之后，你当然会需要更多的命令：关于 gdb 常用命令及各种调试方法详见 gdb 调试利器 ;

同时，你需要更高效的调试：常用的调试命令都会有单字符的缩写，使用缩写更方便；同时，直接敲回车表示重复执行上一步命令；这在单步调试时非常有用；

### pstack 跟踪栈空间

pstack 是一个脚本工具，可显示每个进程的栈跟踪。pstack 命令必须由相应进程的属主或 root 运行。其核心实现就是使用了 gdb 以及 thread apply all bt 命令;

语法:

```
$pstrack <program-pid>

```

示例:

```
$ pstack 4551
Thread 7 (Thread 1084229984 (LWP 4552)):
#0  0x000000302afc63dc in epoll_wait () from /lib64/tls/libc.so.6
#1  0x00000000006f0730 in ub::EPollEx::poll ()
#2  0x00000000006f172a in ub::NetReactor::callback ()
#3  0x00000000006fbbbb in ub::UBTask::CALLBACK ()
#4  0x000000302b80610a in start_thread () from /lib64/tls/libpthread.so.0
#5  0x000000302afc6003 in clone () from /lib64/tls/libc.so.6
#6  0x0000000000000000 in ?? ()

```

### strace 分析系统调用

strace 常用来跟踪进程执行时的系统调用和所接收的信号。在 Linux 世界，进程不能直接访问硬件设备，当进程需要访问硬件设备(比如读取磁盘文件，接收网络数据等等)时，必须由用户态模式切换至内核态模式，通过系统调用访问硬件设备。strace 可以跟踪到一个进程产生的系统调用,包括参数，返回值，执行消耗的时间。

完整程序:

```
strace -o output.txt -T -tt -e trace=all -p 28979

```

跟踪 28979 进程的所有系统调用（-e trace=all），并统计系统调用的花费时间，以及开始时间（以可视化的时分秒格式显示），最后将记录结果存在 output.txt 文件里面。

查看进程正在做什么(实时输出进程执行系统调用的情况):

```
$strace -p <process-pid>

```

关于 strace 的详细介绍，详见 strace 跟踪进程中的系统调用 ;

## 2.2\. 目标文件分析

### nm

nm 用来列出目标文件的符号清单。

```
$nm myProgrammer
08049f28 d _DYNAMIC
08049ff4 d _GLOBAL_OFFSET_TABLE_
080484dc R _IO_stdin_used
         w _Jv_RegisterClasses
08049f18 d __CTOR_END__
08049f14 d __CTOR_LIST__
08049f20 D __DTOR_END__
08049f1c d __DTOR_LIST__
080485e0 r __FRAME_END__
08049f24 d __JCR_END__
08049f24 d __JCR_LIST__
0804a014 A __bss_start
0804a00c D __data_start
08048490 t __do_global_ctors_aux
08048360 t __do_global_dtors_aux
0804a010 D __dso_handle
         w __gmon_start__
08048482 T __i686.get_pc_thunk.bx
08049f14 d __init_array_end
08049f14 d __init_array_start
08048480 T __libc_csu_fini
08048410 T __libc_csu_init
         U __libc_start_main@@GLIBC_2.0
0804a014 A _edata
0804a01c A _end
080484bc T _fini
080484d8 R _fp_hw
080482b4 T _init
08048330 T _start
0804a014 b completed.6086
0804a00c W data_start
0804a018 b dtor_idx.6088
080483c0 t frame_dummy
080483e4 T main
         U printf@@GLIBC_2.0

```

这些包含可执行代码的段称为正文段。同样地，数据段包含了不可执行的信息或数据。另一种类型的段，称为 BSS 段，它包含以符号数据开头的块。对于 nm 命令列出的每个符号，它们的值使用十六进制来表示（缺省行为），并且在该符号前面加上了一个表示符号类型的编码字符。

常见的各种编码包括：

*   A 表示绝对 (absolute)，这意味着不能将该值更改为其他的连接；
*   B 表示 BSS 段中的符号；
*   C 表示引用未初始化的数据的一般符号。

可以将目标文件中所包含的不同的部分划分为段。段可以包含可执行代码、符号名称、初始数据值和许多其他类型的数据。有关这些类型的数据的详细信息，可以阅读 UNIX 中 nm 的 man 页面，其中按照该命令输出中的字符编码分别对每种类型进行了描述。

在目标文件阶段，即使是一个简单的 Hello World 程序，其中也包含了大量的细节信息。nm 程序可用于列举符号及其类型和值，但是，要更仔细地研究目标文件中这些命名段的内容，需要使用功能更强大的工具。

其中两种功能强大的工具是 objdump 和 readelf 程序。

注解

关于 nm 工具的参数说明及更多示例详见 nm 目标文件格式分析 ;

### objdump

ogjdump 工具用来显示二进制文件的信息，就是以一种可阅读的格式让你更多地了解二进制文件可能带有的附加信息。

```
$objdump -d myprogrammer
a.out:     file format elf32-i386

Disassembly of section .init:

080482b4 <_init>:
 80482b4:   53                      push   %ebx
 80482b5:   83 ec 08                sub    $0x8,%esp
 80482b8:   e8 00 00 00 00          call   80482bd <_init+0x9>
 80482bd:   5b                      pop    %ebx
 80482be:   81 c3 37 1d 00 00       add    $0x1d37,%ebx
 80482c4:   8b 83 fc ff ff ff       mov    -0x4(%ebx),%eax
 80482ca:   85 c0                   test   %eax,%eax
 80482cc:   74 05                   je     80482d3 <_init+0x1f>
 80482ce:   e8 3d 00 00 00          call   8048310 <__gmon_start__@plt>
 80482d3:   e8 e8 00 00 00          call   80483c0 <frame_dummy>
 80482d8:   e8 b3 01 00 00          call   8048490 <__do_global_ctors_aux>
 80482dd:   83 c4 08                add    $0x8,%esp
 80482e0:   5b                      pop    %ebx
 80482e1:   c3                      ret

Disassembly of section .plt:
...

```

每个可执行代码段将在需要特定的事件时执行，这些事件包括库的初始化和该程序本身主入口点。

对于那些着迷于底层编程细节的程序员来说，这是一个功能非常强大的工具，可用于研究编译器和汇编器的输出。细节信息，比如这段代码中所显示的这些信息，可以揭示有关本地处理器本身运行方式的很多内容。对该处理器制造商提供的技术文档进行深入的研究，您可以收集关于一些有价值的信息，通过这些信息可以深入地了解内部的运行机制，因为功能程序提供了清晰的输出。

注解

关于 objdump 工具的参数说明及更多示例详见 objdump 二进制文件分析 ;

### readelf

这个工具和 objdump 命令提供的功能类似，但是它显示的信息更为具体，并且它不依赖 BFD 库(BFD 库是一个 GNU 项目，它的目标就是希望通过一种统一的接口来处理不同的目标文件）；

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
  ...

```

ELF Header 为该文件中所有段入口显示了详细的摘要。在列举出这些 Header 中的内容之前，您可以看到 Header 的具体数目。在研究一个较大的目标文件时，该信息可能非常有用。

除了所有这些段之外，编译器可以将调试信息放入到目标文件中，并且还可以显示这些信息。输入下面的命令，仔细分析编译器的输出（假设您扮演了调试程序的角色）:

```
$readelf --debug-dump a.out | more

```

调试工具，如 GDB，可以读取这些调试信息，并且当程序在调试器中运行的同时，您可以使用该工具显示更具描述性的标记，而不是对代码进行反汇编时的原始地址值。

注解

关于 readelf 工具的参数说明及更多示例详见 readelf elf 文件格式分析 ;

### size 查看程序内存占用

size 这个工具用来查看程序运行时各个段的实际内存占用:

```
$size a.out
text           data     bss     dec     hex filename
1146            256       8    1410     582 a.out

```

### file 文件类型查询

这个工具用于查看文件的类型；

比如我们在 64 位机器上发现了一个 32 位的库，链接不上，这就有问题了：

```
$file a.out
a.out: ELF 64-bit LSB executable, AMD x86-64, version 1 (SYSV), for GNU/Linux 2.6.9, dynamically linked (uses shared libs), for GNU/Linux 2.6.9, not stripped

```

也可以查看 Core 文件是由哪个程序生成:

```
$file core.22355

```

### strings 查询数据中的文本信息

一个文件中包含二进制数据和文本数据，如果只需要查看其文本信息，使用这个命令就很方便；过滤掉非字符数据，将文本信息输出:

```
$strings <objfile>

```

### fuser 显示文件使用者

显示所有正在使用着指定的 file, file system 或者 sockets 的进程信息;

```
$fuser -m -u redis-server
redis-server: 11552rce(weber) 22912rce(weber) 25501rce(weber)

```

使用了-m 和-u 选项，用来查找所有正在使用 redis-server 的所有进程的 PID 以及该进程的 OWNER；

fuser 通常被用在诊断系统的”resource busy”问题。如果你希望 kill 所有正在使用某一指定的 file, file system or sockets 的进程的时候，你可以使用-k 选项:

```
$fuser –k /path/to/your/filename

```

### xxd 十六进制显示数据

以十六进制方式显示文件，只显示文本信息:

```
$xxd a.out
0000000: 7f45 4c46 0101 0100 0000 0000 0000 0000  .ELF............
0000010: 0200 0300 0100 0000 3083 0408 3400 0000  ........0...4...
0000020: 3c11 0000 0000 0000 3400 2000 0900 2800  <.......4\. ...(.
0000030: 1e00 1b00 0600 0000 3400 0000 3480 0408  ........4...4...
0000040: 3480 0408 2001 0000 2001 0000 0500 0000  4... ... .......
0000050: 0400 0000 0300 0000 5401 0000 5481 0408  ........T...T...
...

```

### od

通常使用 od 命令查看特殊格式的文件内容。通过指定该命令的不同选项可以以十进制、八进制、十六进制和 ASCII 码来显示文件。

参数说明：

-A 指定地址基数，包括：

*   d 十进制
*   o 八进制（系统默认值）
*   x 十六进制
*   n 不打印位移值

-t 指定数据的显示格式，主要的参数有：

*   c ASCII 字符或反斜杠序列
*   d 有符号十进制数
*   f 浮点数
*   o 八进制（系统默认值为 02）
*   u 无符号十进制数
*   x 十六进制数

除了选项 c 以外的其他选项后面都可以跟一个十进制数 n，指定每个显示值所包含的字节数。

说明：od 命令系统默认的显示方式是八进制，这也是该命令的名称由来（Octal Dump）。但这不是最有用的显示方式，用 ASCII 码和十六进制组合的方式能提供更有价值的信息输出。

以十六进制和字符同时显示:

```
$od -Ax -tcx4 a.c
000000   #   i   n   c   l   u   d   e       <   s   t   d   i   o   .
              636e6923        6564756c        74733c20        2e6f6964
000010   h   >  \n  \n   v   o   i   d       m   a   i   n   (   )  \n
              0a0a3e68        64696f76        69616d20        0a29286e
000020   {  \n  \t   i   n   t       i       =       5   ;  \n  \t   p
              69090a7b        6920746e        35203d20        70090a3b
000030   r   i   n   t   f   (   "   h   e   l   l   o   ,   %   d   "
              746e6972        68222866        6f6c6c65        2264252c
000040   ,   i   )   ;  \n   }  \n
              3b29692c        000a7d0a
000047

```

以字符方式显示:

```
$od -c a.c
0000000   #   i   n   c   l   u   d   e       <   s   t   d   i   o   .
0000020   h   >  \n  \n   v   o   i   d       m   a   i   n   (   )  \n
0000040   {  \n  \t   i   n   t       i       =       5   ;  \n  \t   p
0000060   r   i   n   t   f   (   "   h   e   l   l   o   ,   %   d   "
0000100   ,   i   )   ;  \n   }  \n
0000107

```

注：类似命令还有 hexdump（十六进制输出） © 版权所有 2014, Colin http://blog.me115.com. 由 [Sphinx](http://sphinx-doc.org/) 1.3.5 创建。

## 3.1\. 分析系统瓶颈

系统响应变慢，首先得定位大致的问题出在哪里，是 IO 瓶颈、CPU 瓶颈、内存瓶颈还是程序导致的系统问题；

使用 top 工具能够比较全面的查看我们关注的点:

```
$top
    top - 09:14:56 up 264 days, 20:56,  1 user,  load average: 0.02, 0.04, 0.00
    Tasks:  87 total,   1 running,  86 sleeping,   0 stopped,   0 zombie
    Cpu(s):  0.0%us,  0.2%sy,  0.0%ni, 99.7%id,  0.0%wa,  0.0%hi,  0.0%si,  0.2%st
    Mem:    377672k total,   322332k used,    55340k free,    32592k buffers
    Swap:   397308k total,    67192k used,   330116k free,    71900k cached
    PID USER      PR  NI  VIRT  RES  SHR S %CPU %MEM    TIME+  COMMAND
    1 root      20   0  2856  656  388 S  0.0  0.2   0:49.40 init
    2 root      20   0     0    0    0 S  0.0  0.0   0:00.00 kthreadd
    3 root      20   0     0    0    0 S  0.0  0.0   7:15.20 ksoftirqd/0
    4 root      RT   0     0    0    0 S  0.0  0.0   0:00.00 migration/

```

进入交互模式后:

*   输入 M，进程列表按内存使用大小降序排序，便于我们观察最大内存使用者使用有问题（检测内存泄漏问题）;
*   输入 P，进程列表按 CPU 使用大小降序排序，便于我们观察最耗 CPU 资源的使用者是否有问题；

top 第三行显示当前系统的，其中有两个值很关键:

*   %id：空闲 CPU 时间百分比，如果这个值过低，表明系统 CPU 存在瓶颈；
*   %wa：等待 I/O 的 CPU 时间百分比，如果这个值过高，表明 IO 存在瓶颈；

## 3.2\. 分析内存瓶颈

查看内存是否存在瓶颈，使用 top 指令看比较麻烦，而 free 命令更为直观:

```
[/home/weber#]free
             total       used       free     shared    buffers     cached
Mem:        501820     452028      49792      37064       5056     136732
-/+ buffers/cache:     310240     191580
Swap:            0          0          0
[/home/weber#]top
top - 17:52:17 up 42 days,  7:10,  1 user,  load average: 0.02, 0.02, 0.05
Tasks:  80 total,   1 running,  79 sleeping,   0 stopped,   0 zombie
%Cpu(s):  0.0 us,  0.0 sy,  0.0 ni,100.0 id,  0.0 wa,  0.0 hi,  0.0 si,  0.0 st
KiB Mem:    501820 total,   452548 used,    49272 free,     5144 buffers
KiB Swap:        0 total,        0 used,        0 free.   136988 cached Mem

```

top 工具显示了 free 工具的第一行所有信息，但真实可用的内存，还需要自己计算才知道; 系统实际可用的内存为 free 工具输出第二行的 free+buffer+cached；也就是第三行的 free 值 191580；关于 free 命令各个值的详情解读，请参考这篇文章 free 查询可用内存 ;

如果是因为缺少内存，系统响应变慢很明显，因为这使得系统不停的做换入换出的工作;

进一步的监视内存使用情况，可使用 vmstat 工具，实时动态监视操作系统的内存和虚拟内存的动态变化。 参考： vmstat 监视内存使用情况 ;

## 3.3\. 分析 IO 瓶颈

如果 IO 存在性能瓶颈，top 工具中的%wa 会偏高；

进一步分析使用 iostat 工具:

```
/root$iostat -d -x -k 1 1
Linux 2.6.32-279.el6.x86_64 (colin)   07/16/2014      _x86_64_        (4 CPU)

Device:         rrqm/s   wrqm/s     r/s     w/s    rkB/s    wkB/s avgrq-sz avgqu-sz   await  svctm  %util
sda               0.02     7.25    0.04    1.90     0.74    35.47    37.15     0.04   19.13   5.58   1.09
dm-0              0.00     0.00    0.04    3.05     0.28    12.18     8.07     0.65  209.01   1.11   0.34
dm-1              0.00     0.00    0.02    5.82     0.46    23.26     8.13     0.43   74.33   1.30   0.76
dm-2              0.00     0.00    0.00    0.01     0.00     0.02     8.00     0.00    5.41   3.28   0.00

```

*   如果%iowait 的值过高，表示硬盘存在 I/O 瓶颈。
*   如果 %util 接近 100%，说明产生的 I/O 请求太多，I/O 系统已经满负荷，该磁盘可能存在瓶颈。
*   如果 svctm 比较接近 await，说明 I/O 几乎没有等待时间；
*   如果 await 远大于 svctm，说明 I/O 队列太长，io 响应太慢，则需要进行必要优化。
*   如果 avgqu-sz 比较大，也表示有大量 io 在等待。

更多参数说明请参考 iostat 监视 I/O 子系统 ;

## 3.4\. 分析进程调用

通过 top 等工具发现系统性能问题是由某个进程导致的之后，接下来我们就需要分析这个进程；继续 查询问题在哪；

这里我们有两个好用的工具： pstack 和 pstrace

pstack 用来跟踪进程栈，这个命令在排查进程问题时非常有用，比如我们发现一个服务一直处于 work 状态（如假死状态，好似死循环），使用这个命令就能轻松定位问题所在；可以在一段时间内，多执行几次 pstack，若发现代码栈总是停在同一个位置，那个位置就需要重点关注，很可能就是出问题的地方；

示例：查看 bash 程序进程栈:

```
/opt/app/tdev1$ps -fe| grep bash
tdev1   7013  7012  0 19:42 pts/1    00:00:00 -bash
tdev1  11402 11401  0 20:31 pts/2    00:00:00 -bash
tdev1  11474 11402  0 20:32 pts/2    00:00:00 grep bash
/opt/app/tdev1$pstack 7013
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

```

而 strace 用来跟踪进程中的系统调用；这个工具能够动态的跟踪进程执行时的系统调用和所接收的信号。是一个非常有效的检测、指导和调试工具。系统管理员可以通过该命令容易地解决程序问题。

参考： strace 跟踪进程中的系统调用 ;

## 3.5\. 优化程序代码

优化自己开发的程序，建议采用以下准则:

1.  二八法则：在任何一组东西中，最重要的只占其中一小部分，约 20%，其余 80%的尽管是多数，却是次要的；在优化实践中，我们将精力集中在优化那 20%最耗时的代码上，整体性能将有显著的提升；这个很好理解。函数 A 虽然代码量大，但在一次正常执行流程中，只调用了一次。而另一个函数 B 代码量比 A 小很多，但被调用了 1000 次。显然，我们更应关注 B 的优化。
2.  编完代码，再优化；编码的时候总是考虑最佳性能未必总是好的；在强调最佳性能的编码方式的同时，可能就损失了代码的可读性和开发效率；

### gprof 使用步骤

1.  用 gcc、g++、xlC 编译程序时，使用-pg 参数，如：g++ -pg -o test.exe test.cpp 编译器会自动在目标代码中插入用于性能测试的代码片断，这些代码在程序运行时采集并记录函数的调用关系和调用次数，并记录函数自身执行时间和被调用函数的执行时间。
2.  执行编译后的可执行程序，如：./test.exe。该步骤运行程序的时间会稍慢于正常编译的可执行程序的运行时间。程序运行结束后，会在程序所在路径下生成一个缺省文件名为 gmon.out 的文件，这个文件就是记录程序运行的性能、调用关系、调用次数等信息的数据文件。
3.  使用 gprof 命令来分析记录程序运行信息的 gmon.out 文件，如：gprof test.exe gmon.out 则可以在显示器上看到函数调用相关的统计、分析信息。上述信息也可以采用 gprof test.exe gmon.out> gprofresult.txt 重定向到文本文件以便于后续分析。

关于 gprof 的使用案例，请参考 [[f1]](#f1) ;

## 3.6\. 其它工具

调试内存泄漏的工具 valgrind，感兴趣的朋友可以 google 了解；

OProfile: Linux 平台上的一个功能强大的性能分析工具,使用参考 [[f2]](#f2) ;

除了上面介绍的工具，还有一些比较全面的性能分析工具，比如 sar（Linux 系统上默认不安装，需要手动安装下）； 将 sar 的常驻监控工具打开后，能够收集比较全面的性能分析数据；

关于 sar 的使用，参考 sar 找出系统瓶颈的利器 ;

| [[f1]](#id7) | C++的性能优化实践 [`www.cnblogs.com/me115/archive/2013/06/05/3117967.html`](http://www.cnblogs.com/me115/archive/2013/06/05/3117967.html) |

| [[f2]](#id9) | 用 OProfile 彻底了解性能 [`www.ibm.com/developerworks/cn/linux/l-oprof/`](http://www.ibm.com/developerworks/cn/linux/l-oprof/) |

| [f3] | Linux 上的 free 命令详解 [`www.cnblogs.com/coldplayerest/archive/2010/02/20/1669949.html`](http://www.cnblogs.com/coldplayerest/archive/2010/02/20/1669949.html) | © 版权所有 2014, Colin http://blog.me115.com. 由 [Sphinx](http://sphinx-doc.org/) 1.3.5 创建。