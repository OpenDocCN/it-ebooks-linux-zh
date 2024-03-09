# 用户空间探测

# 4\. 用户空间探测

SystemTap 诞生的最初使命，是探测内核空间。由于许多情况下用户空间探测有助于诊断问题，SystemTap 从 0.6 版本开始也支持探测用户空间的进程。SystemTap 可以探测用户空间进程内函数的调用和退出，可以探测用户代码中预定义的标记，可以探测用户进程的事件。

SystemTap 进行用户空间探测需要 uprobes 模块。如果你的 Linux 内核版本大于等于 3.5, 它已经内置了`uprobes`。要想验证当前内核是否原生支持 uprobes，运行下面命令：

```
grep CONFIG_UPROBES /boot/config-`uname -r` 
```

如果当前内核集成了 uprobes，就会输出以下内容：

```
CONFIG_UPROBES=y 
```

如果你的内核版本小于 3.5, SystemTap 会自动构建 uprobes 模块。不过，SystemTap 的用户空间事件跟踪功能依然需要你的内核支持 utrace 拓展。可以从这个链接获取更多关于 utrace 的细节：[`sourceware.org/systemtap/wiki/utrace`](http://sourceware.org/systemtap/wiki/utrace) 。要想验证当前内核是否提供了必要的 utrace 支持，在终端中输入下面的命令：

```
grep CONFIG_UTRACE /boot/config-`uname -r` 
```

如果当前内核支持用户空间探测，就会输出以下内容：

```
CONFIG_UTRACE=y 
```

# 用户空间事件

# 4.1\. 用户空间事件

所有的用户空间事件都以`process`开头。你可以通过进程 ID 指定要检测的进程，也可以通过可执行文件名的路径名指定。SystemTap 会查看系统的`PATH`环境变量，所以你既可以使用绝对路径，也可以使用在命令行中运行可执行文件时所用的名字。

由于 SystemTap 静态分析放置探针的位置时离不开调试信息，一些用户空间事件需要给定 PID 或可执行文件的路径（以下将两者统称为`PATH`）。不过大多数`process`事件中，PID 和可执行文件路径名都是可选的。下面列出的事件都需要进程 ID 或可执行文件的路径。不在其中的`process`事件不需要 PID 和可执行文件路径名。

**process("PATH").function("function")**

进入可执行文件`PATH`的用户空间函数`function`。该事件相当于内核空间中的`kernel.function("function")`。它允许使用通配符和`.return`后缀。

**process("PATH").statement("statement")**

代码中第一次执行`statement`的地方。该事件相当于内核空间中的`kernel.statement("statement")`。

**process("PATH").mark("marker")**

在`PATH`中定义的静态探测点。你可以使用通配符，在单个探针中指定多个探测点。有些静态探测点中 允许使用编了号（numbered）的参数（`$1`，`$2`等等）。 有些用户空间下的可执行程序提供了这些静态探测点，比如 Java。大多数提供了静态探测点的程序也一并给这些探测点提供了易于使用的别名。下面是 x86_64 Java hotspot 虚拟机中的一个例子：

```
probe hotspot.gc_begin =
  process("/usr/lib/jvm/java-1.6.0-openjdk-1.6.0.0.x86_64/jre/lib/amd64/server/libjvm.so").mark("gc__begin") 
```

**process("PATH").begin**

创建了一个用户空间下的进程。你可以限定某个进程 ID 或可执行文件的路径，如果不限定，任意进程的创建都会触发该事件。

**process("PATH").thread.begin**

创建了一个用户空间下的线程。你可以限定某个进程 ID 或可执行文件的路径。

**process("PATH").end**

销毁了一个用户空间下的进程。你可以限定某个进程 ID 或可执行文件的路径。

**process("PATH").thread.end**

销毁了一个用户空间下的线程。你可以限定某个进程 ID 或可执行文件的路径。

**process("PATH").syscall**

一个用户空间进程调用了系统调用。可以通过上下文变量`$syscall`获取系统调用号。还可以通过`$arg1`到`$arg6`分别获取前六个参数。添加`return`后缀后会捕获退出系统调用的事件。在`syscall.return`中，可以通过上下文变量`$return`获取返回值。 你可以用某个进程 ID 或可执行文件的路径进行限定。

# 访问用户空间目标变量

# 4.2\. 访问用户空间目标变量

你可以访问用户空间目标变量，所用的语法与第 3.3 节第二部分，“目标变量”中访问内核空间的语法相同。在 Linux 中，用户代码和内核代码使用的地址空间是隔绝的。不过 SystemTap 可以在使用`->`运算符时找到恰当的地址空间。

对于指向基本类型（如整数和字符串）的指针，可以使用下列的函数访问用户空间的数据。每个函数的第一个参数都是指向数据的指针（`address`）。

**user_char(address)**

从当前用户进程中获取地址对应的字符数据。

**user_short(address)**

从当前用户进程中获取地址对应的 short 型数据。

**user_int(address)**

从当前用户进程中获取地址对应的 int 型数据。

**user_long(address)**

从当前用户进程中获取地址对应的 long 型数据。

**user_string(address)**

从当前用户进程中获取地址对应的字符串数据。

**user_string_n(address, n)**

从当前用户进程中获取地址对应的字符串数据，取前 n 字节。

译注：这些函数都是在`process(PATH).xxx`事件的处理程序中使用的。当前用户进程指的就是`PATH`。如

```
process(@1).syscall {
    ...
    user_string(field) # field 指向@1 地址空间中的某个地址
} 
```

# 用户空间栈回溯

# 4.3\. 用户空间栈回溯

`pp`（probe point）函数可以返回触发当前处理程序的事件名（包含展开了的通配符和别名）。如果该事件与特定的函数相关，`pp`的输出会包括触发了该事件的函数名。然而，许多情况下触发同一个事件的函数可能来自于程序中不同的模块；特别是在该函数位于某个共享库的情况下。还好 SystemTap 提供了用户空间栈的回溯（backtrace）功能，便于查看事件是怎么被触发的。

编译器优化代码时会消除栈帧指针（stack frame pointers），这将混淆用户空间栈回溯的结果。所以要想查看栈回溯，需要有编译器生成的调试信息。SystemTap 用户空间栈回溯机制可以利用这些调试信息来重建栈回溯的现场，不过该功能当前只实现在 32 位和 64 位 x86 处理器上，还不支持其他架构的处理器。要想使用这些调试信息来重建栈回溯，给可执行文件加上`-d executable`选项，并给共享库加上`-ldd`选项。

举个例子，你可以使用`ubacktrace`（user-space backtrace）函数来输出`ls`命令中`xmalloc`函数的调用情况。如果你已经安装了`ls`命令的 debuginfo，下面的 SystemTap 命令会在`xmalloc`函数调用时输出栈回溯的结果：

```
stap -d /bin/ls --ldd \
-e 'probe process("ls").function("xmalloc") {print_usyms(ubacktrace())}' \
-c "ls /" 
```

译注：要想成功运行上面的命令，你需要安装 coreutils 的 debuginfo。具体安装方式请根据自己用的发行版搜索一下。如果你跟我一样用的也是 Ubuntu，可以看下[askubuntu 上这个回答](http://askubuntu.com/questions/427318/how-can-i-install-a-debug-build-for-coreutils)，运行`sudo apt-get install coreutils-dbgsym`。

运行后，上面的命令会有类似下面的输出：

```
bin dev   lib     media  net         proc   sbin     sys  var
boot    etc   lib64   misc   op_session  profilerc  selinux  tmp
cgroup  home  lost+found  mnt    opt         root   srv  usr
 0x4116c0 : xmalloc+0x0/0x20 [/bin/ls]
 0x4116fc : xmemdup+0x1c/0x40 [/bin/ls]
 0x40e68b : clone_quoting_options+0x3b/0x50 [/bin/ls]
 0x4087e4 : main+0x3b4/0x1900 [/bin/ls]
 0x3fa441ec5d : __libc_start_main+0xfd/0x1d0 [/lib64/libc-2.12.so]
 0x402799 : _start+0x29/0x2c [/bin/ls]
 0x4116c0 : xmalloc+0x0/0x20 [/bin/ls]
 0x4116fc : xmemdup+0x1c/0x40 [/bin/ls]
 0x40e68b : clone_quoting_options+0x3b/0x50 [/bin/ls]
 0x40884a : main+0x41a/0x1900 [/bin/ls]
 0x3fa441ec5d : __libc_start_main+0xfd/0x1d0 [/lib64/libc-2.12.so]
 ... 
```

关于在用户空间栈回溯中可用的函数的更多内容，请查看`ucontext-symbols.stp`和`ucontext-unwind.stp`两个 tapset。上述 tapset 中的函数的描述信息也可以在[SystemTap Tapset Reference Manual](https://sourceware.org/systemtap/tapsets/)找到。