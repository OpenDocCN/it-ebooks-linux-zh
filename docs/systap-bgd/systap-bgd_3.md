# 工作细节

# 3\. 工作细节

SystemTap 允许用户仅需编写和重用简单的脚本即可获取 Linux 繁多的运行数据。通过 SystemTap 脚本，你可以又好又快地提取数据、过滤数据、汇总数据。诊断复杂的性能问题（或功能问题）再也不是难事。

整个 SystemTap 脚本所做的，无非就是声明感兴趣的事件，然后添加对应的处理程序。当 SystemTap 脚本运行时，SystemTap 会监控声明的事件；一旦事件发生，Linux 内核会临时切换到对应的处理程序，完成后再重拾原先的工作。

可供监控的事件种类繁多：进入/退出某个函数，定时器到期，会话终止，等等。处理程序由一组 SystemTap 语句构成，指明事件发生后要做的工作。其中包括从事件上下文中提取数据，存储到内部变量中，输出结果。

# 结构

# 3.1\. 结构

SystemTap 脚本运行时，会启动一个对应的 SystemTap 会话。整个会话大致流程如下：

首先，SystemTap 会检查脚本中用到的`tapset`，确保它们都存在于 tapset 库中（通常是`/usr/share/systemtap/tapset/`）。然后 SystemTap 会把找到的`tapset`替换成在 tapset 库中对应的定义。（译注：tapset 是 tap（听诊器）的集合，指一些预定义的 SystemTap 事件或函数。完整的 tapset 列表见 [`sourceware.org/systemtap/tapsets/`](https://sourceware.org/systemtap/tapsets/) ）

SystemTap 接着会把脚本转化成 C 代码，运行系统的 C 编译器编译出一个内核模块。完成这一步的工具包含在 systemtap 包中（详见第 2.1 节，“安装和配置”）

SystemTap 随即加载该模块，并启用脚本中所有的探针（包括事件和对应的处理程序）。这一步由 system-runtime 包的`staprun`完成。（详见第 2.1 节，“安装和配置”）

每当被监控的事件发生，对应的处理程序就会被执行。

一旦 SystemTap 会话终止，探针会被禁用，内核模块也会被卸载。

这一串流程皆始于一个简单的命令行程序：`stap`。这个程序包揽了 SystemTap 主要的功能。要想了解关于`stap`的更多信息，请`man stap`（前提是你的机器上已经安装了 SystemTap）

# 脚本

# 3.2\. 脚本

在大多数情况下，SystemTap 脚本是每个 SystemTap 会话的基石。SystemTap 脚本决定了需要收集的信息类型，也决定了对收集到的信息的处理方式。

在本章的开头曾经提到过，SystemTap 脚本由两部分组成：事件和处理程序。一旦 SystemTap 会话准备就绪，SystemTap 会监控操作系统中特定的事件，并在事件发生的时候触发对应的处理程序。

> 一个事件和它对应的处理程序合称探针。一个 SystemTap 脚本可以有多个探针。 一个探针的处理程序部分通常称之为探针主体（probe body）

以应用开发的方式类比，使用事件和处理程序就像在程序的特定位置插入打日志的语句。每当程序运行时，这些日志会帮助你查看程序执行的流程。

SystemTasp 脚本允许你在无需重新编译代码，即可插入检测指令，而且处理程序也不限于单纯地打印数据。事件会触发对应的处理程序；对应的处理程序记录下感兴趣的数据，并以你指定的格式输出。

SystemTap 脚本的后缀是`.stp`，并以这样的语句表示一个探针：

```
probe   event {statements} 
```

译注：如果你写过 awk 脚本，应该会感觉似曾相识。

SystemTap 支持给一个探针指定多个事件；每个事件以逗号隔开。如果给某一个探针指定了多个事件，只要其中一个事件发生，SystemTap 就会执行对应的处理程序。

每个探针有自己对应的语句块。语句块由花括号（`{}`）括住，包含事件发生时需要执行的所有语句。SystemTap 会顺序执行这些语句；语句间通常不需要特殊的分隔符或终止符。

> SystemTap 脚本的语句块使用跟 C 语言一样的语法。语句块内允许嵌套。

SystemTap 允许你编写函数来提取探针间公共的逻辑。所以，与其在多个探针间复制粘贴重复的语句，你不如把它们放入函数中，就像：

```
function function_name(arguments) {statements}

probe event {function_name(arguments)} 
```

当探针被触发时，`function_name`中的语句会被执行。`arguments`是传递给函数的可选的入参。

> 本节仅仅是粗略地介绍下 SystemTap 脚本的结构。要想了解更详细的内容，最好坚持读到第五章，SystemTap 脚本集锦；其中的每一节都会详细介绍一个脚本，包含它所监控的事件、它的处理程序和输出内容。

## 事件

SystemTap 事件大致分为两类：同步事件和异步事件。

### 同步事件

同步事件会在任意进程执行到内核特定位置时触发。你可以用它来作为其它事件的参照点，毕竟同步事件有着清晰的上下文信息。

同步事件包括：

**syscall.system_call**

进入名为`system_call`的系统调用。如果想要监控的是退出某个系统调用的事件，在后面添加`.return`。举个例子，要想监控进入和退出系统调用`close`的事件，应该使用`syscall.close`和`syscall.close.return`。

**vfs.file_operation**

进入虚拟文件系统（VFS）名为`file_operation`的文件操作。跟系统调用事件一样，在后面添加`.return`可以监控对应的退出事件。 译注：`file_operation`取值的范畴，取决于当前内核中`struct file_operations`的定义的操作（可能位于`include/linux/fs.h`中，版本不同位置会不一样，建议上[`lxr.free-electrons.com/ident`](http://lxr.free-electrons.com/ident) 查找`file_operations`）。

**kernel.function("function")**

进入名为`function`的内核函数。举个例子，`kernel.function("sys_open")`即内核函数`sys_open`被调用时所触发的事件。同样，`kernel.function("sys_open").return`会在`sys_open`函数调用返回时被触发。

在定义探测事件时，可以使用像`*`这样的通配符。你也可以用内核源码文件名限定要跟踪的函数。看下面的例子：

> ```
> probe kernel.function("*@net/socket.c") { }
> probe kernel.function("*@net/socket.c").return { } 
> ```

在上面的例子中，第一个探针会监控`net/socket.c`中的所有函数的调用。第二个会监控所有这些函数的退出。注意在这个例子里，处理程序是空的；所以，即使事件被触发了，什么也不会发生。 译注：例子中用的是探测内核源码中的函数的语法。完整的语法是`func_name@file_name[:line_num]`，由函数名、文件名、行号三部分组成。其中函数名在例子中为`*`，匹配任意函数。行号是可选的，在上面的例子里就被忽略掉了。如果想指定某个范围内的函数，如从行 x 到 y，使用`:x-y`这样格式作为行号。

**kernel.trace("tracepoint")**

到达名为`tracepoint`的静态内核探测点（tracepoint）。较新的内核（>= 2.6.30）包含了特定事件的检测代码。这些事件一般会被标记成静态内核探测点。一个例子是，`kernel.trace("kfree_skb")`表示内核释放了一个网络缓冲区的事件。（译注：想知道当前内核设置了哪些静态内核探测点吗？你需要运行`sudo perf list`。）

**module("module").function("function")**

进入指定模块`module`的函数`function`。举个例子：

> ```
> probe module("ext3").function("*") { }
> probe module("ext3").function("*").return { } 
> ```

上面例子的第一个探针，会在每个 ext3 模块中的函数被调用时触发。第二个探针会在函数退出时触发。一切就跟`kernel.function()`一样。

系统内的所有内核模块通常都在`/lib/modules/kernel_version`，其中`kernel_version`取当前内核版本号。模块的后缀名为`.ko`。 （译注：在该路径下使用`find -name '*.ko' -printf '%f\n' | sed 's/\.ko$//'`可列出所有的内核模块）

### 异步事件

异步事件跟特定的指令或代码的位置无关。 这部分事件主要包含计数器、定时器和其它类似的东西。

**begin**

SystemTap 会话的启动事件，会在脚本开始时触发。

**end**

SystemTap 会话的结束事件，会在脚本结束时触发。

**timer events**

用于周期性执行某段处理程序。举个例子：

> probe timer.s(4) { printf("hello world\n") }

上面的例子中，每隔 4 秒就会输出`hello world`。还可以使用其它规格的定时器：

```
timer.ms(milliseconds)
timer.us(microseconds)
timer.ns(nanoseconds)
timer.hz(hertz)
timer.jiffies(jiffies) 
```

定时事件总是跟其它事件搭配使用。其它事件负责收集信息，而定时事件定期输出当前状况，让你看到数据随时间的变化情况。

> 限于篇幅，还有些 SystemTap 事件就不再一一介绍了。如果你想了解更多内容，请`man stapprobes`。该 man page 中的`SEE ALSO`一节，包括了通往其它 man page 的链接，你还可以随之找到某些特定子系统和组件所支持的事件。

## 处理程序

看一下下面的示例脚本：

```
probe begin
{
  printf ("hello world\n")
  exit ()
} 
```

在上面的例子中，每当会话开始时，`begin`事件会触发`{}`内的处理程序，输出`hello world`加一个换行符，然后退出。

> SystemTap 脚本会一直运行，直到执行了`exit()`函数。如果你想中途退出一个脚本，可以用`Ctrl+c`中断。

**printf**

`printf()`是最简单的 SystemTap 函数之一，可以跟许多函数搭配使用，用来输出数据。通常我们会这样调用`printf()`：

```
printf ("format string\n", arguments) 
```

`format string`指明`arguments`输出的格式。在前面的例子里，printf 语句内没有指定 format 格式符。在格式字符串（format string）中，你可以用`%s`表示字符串，`%d`表示数字。格式字符串中可以包含多个格式符，每个格式符对应一个参数；每个参数之间用逗号隔开。

> SystemTap 的 printf 语句跟 C 的 printf 语句，无论在语法还是在格式字符串上都差不多。

下面让我们再看多一个例子：

```
probe syscall.open
{
  printf ("%s(%d) open\n", execname(), pid())
} 
```

在上面的例子中，SystemTap 会在每次`open`被调用时，输出调用程序的名字和 PID，外加`open`这个词。该探针输出的结果看上去会是这样：

```
vmware-guestd(2206) open
hald(2360) open
hald(2360) open
hald(2360) open
df(3433) open
df(3433) open
df(3433) open
hald(2360) open 
```

你可以在`printf()`里使用其他的 SystemTap 函数。比如上面的例子中就用到`execname()`（获取触发事件的进程名）和`pid()`（当前进程 ID）。

下面列出常用的 SystemTap 函数：

**tid()**

当前的 tid（thread id）。

**uid()**

当前的 uid。

**cpu()**

当前的 CPU 号

**gettimeofday_s()**

自 epoch 以来的秒数

**ctime()**

将上一个函数返回的秒数转化成时间字符串

**pp()**

返回描述当前处理的探测点的字符串

**thread_indent()**

你可以用这个函数来组织你的输出结果。这个函数接受一个表示缩进差额的参数，用来更新当前线程的“缩进计数器”（其实就是用于缩进的空格数）。它返回的是加了足够缩进的标识字符串。 这个标识字符串包括一个时间戳（表示自从该线程首次调用`thread_indent()`以来所经过的毫秒数），一个进程名，一个 tid。由此可以清晰地看出函数的调用次序和调用层级，和每次调用时的间隔。 如果一个函数调用后随即退出，很容易就能看出被触发的两个事件是相关的。然而，在大多数情况下，一个函数调用和退出之间，往往会有调用其他别的函数。通过缩进，可以相对更清晰地看出某个函数调用和退出的时机。

看一下下面使用`thread_indent()`的例子：

```
probe kernel.function("*@net/socket.c").call
{
  printf ("%s -> %s\n", thread_indent(1), probefunc())
}
probe kernel.function("*@net/socket.c").return
{
  printf ("%s <- %s\n", thread_indent(-1), probefunc())
} 
```

它输出的结果大概是这个样子的，注意箭头前面的空格数:

```
0 ftp(7223): -> sys_socketcall
1159 ftp(7223):  -> sys_socket
2173 ftp(7223):   -> __sock_create
2286 ftp(7223):    -> sock_alloc_inode
2737 ftp(7223):    <- sock_alloc_inode
3349 ftp(7223):    -> sock_alloc
3389 ftp(7223):    <- sock_alloc
3417 ftp(7223):   <- __sock_create
4117 ftp(7223):   -> sock_create
4160 ftp(7223):   <- sock_create
4301 ftp(7223):   -> sock_map_fd
4644 ftp(7223):    -> sock_map_file
4699 ftp(7223):    <- sock_map_file
4715 ftp(7223):   <- sock_map_fd
4732 ftp(7223):  <- sys_socket
4775 ftp(7223): <- sys_socketcall 
```

上面的输出包含如下信息：

*   自从该线程首次调用`thread_indent()`以来所经过的毫秒数。
*   进程名和 PID。
*   用于缩进的若干个空格。以上三项均为`thread_indent()`的输出。
*   `->`表示函数调用，`<-`表示函数退出。
*   触发事件的函数名。

**name**

返回系统调用的名字。这个变量只能在`syscall.system_call`触发的处理程序中使用。

**target()**

当你通过`stap script -x PID`或`stap script -c command`来执行某个脚本`script`时，`target()`会返回你指定的 PID 或命令名。举个例子：

```
probe syscall.* {
  if (pid() == target())
    printf("%s\n", name)
} 
```

当上面的例子中的脚本带命令行参数`-x PID`运行时，它会监控所有的系统调用（`syscall.*`），并输出其中由指定进程所触发的系统调用。 你当然可以把上面例子中的`target()`替换成你想要指定的 PID。不过使用`target()`让你的脚本可以重用。现在你只需在运行时指定 PID，而无需每次都修改掉硬编码的 PID 值。

要想了解更多关于 SystemTap 函数的信息，请`man stapfuncs`。

# 处理程序的基本结构

# 3.3\. 处理程序的基本结构

SystemTap 支持在处理程序中使用一些基本的结构。它们的语法基本上类似于 C 或 awk。了解最常用的一些结构，有助于你写出更清晰的 SystemTap 脚本。

## 变量

处理程序里面当然可以使用变量，你所需的不过是给它取个好名字，把函数或表达式的值赋给它，然后就可以使用它了。SystemTap 可以自动判定变量的类型。举个例子，如果你用`gettimeofday_s()`给变量`foo`赋值，那么`foo`就是数值类型的，可以在`printf()`中通过`%d`输出。 变量默认只能在其所定义的探针内可用。这意味着变量的生命周期仅仅是处理程序的某次运行。不过你也可以在探针外定义变量，并使用`global`修饰它们，这样就能在探针间共享变量了。 ⁠

```
global count_jiffies, count_ms
probe timer.jiffies(100) { count_jiffies ++ }
probe timer.ms(100) { count_ms ++ }
probe timer.ms(12345)
{
  hz=(1000*count_jiffies) / count_ms
  printf ("jiffies:ms ratio %d:%d => CONFIG_HZ=%d\n",
    count_jiffies, count_ms, hz)
  exit ()
} 
```

在上面的例子中，`timer-jiffies.stp`通过累加 jiffies 和 milliseconds，来求出内核的`CONFIG_HZ`配置。`global`语句使得`count_jiffies`和`count_ms`在每个探针中可用。

> 在上面的例子中，我们用`++`来将变量的值加一。如下探针中，`count_jiffies`每隔 100 jiffies 会自增 1:
> 
> ```
> probe timer.jiffies(100) { count_jiffies ++ } 
> ```
> 
> SystemTap 知道`count_jiffies`是一个整数。那是因为`count_jiffies`没有被赋予一个初始值，所以它的值默认为零。

## 目标变量（Target Variables）

跟内核代码相关的事件，如`kernel.function("function")`和`kernel.statement("statement")`，允许使用目标变量获取这部分代码中可访问到的变量的值。你可以使用`-L`选项来列出特定探测点下可用的目标变量。如果已经安装了内核调试信息，你可以通过这个命令获取`vfs_read`中可用的目标变量：

```
stap -L 'kernel.function("vfs_read")' 
```

它会有类似如下的输出：

```
kernel.function("vfs_read@fs/read_write.c:277") $file:struct file* $buf:char* $count:size_t $pos:loff_t* 
```

每个目标变量前面都以`$`开头，并以`:`加变量类型结尾。上面的输出表示，`vfs_read`函数入口处有三个变量可用：`$file`（指向描述文件的结构体）、`$buf`（指向接收读取的数据的用户空间缓冲区）、`$count`（读取的字节数），和`$pos`（读开始的位置）。 对于那些不属于本地变量的变量，像是全局变量或一个在文件中定义的静态变量，可以用`@var("varname@src/file.c")`获取。 SystemTap 会保留目标变量的类型信息，并且允许通过`->`访问其中的成员。跟 C 语言不同的是，`->`既可以用来访问指针指向的值，也可以用来访问子结构体中的成员。在获取复杂结构体中的信息时，`->`可以链式使用。举个例子，`fs/file_table.c`中的静态目标变量`files_stat`存储着一些当前文件系统中可调节的参数。我们为了获取其中的一个域，可以这么写：

```
stap -e 'probe kernel.function("vfs_read") {
           printf ("current files_stat max_files: %d\n",
                   @var("files_stat@fs/file_table.c")->max_files);
           exit(); }' 
```

会有类似如下的输出：

```
current files_stat max_files: 386070 
```

有许多函数可以通过指向基本类型的指针获取内核空间对应地址上的数据，在此一一列出。在第 4.2 节，我们还会谈到获取用户空间数据的类似函数。

**kernel_char(address)**

从内核空间地址中获取 char 变量

**kernel_short(address)**

从内核空间地址中获取 short 变量

**kernel_int(address)**

从内核空间地址中获取 int 变量

**kernel_long(address)**

从内核空间地址中获取 long 变量

**kernel_string(address)**

从内核空间地址中获取字符串

**kernel_string_n(address, n)**

从内核空间地址中获取长为 n 的字符串

### 整齐打印目标变量（Pretty Printing Target Variables）

某些场景中，我们可能需要输出当前可访问的各种变量，以便于记录底层的变化。SystemTap 提供了一些操作，可以生成描述特定目标变量的字符串：

**$$vars**

输出作用域内每个变量的值。等价于`sprintf("parm1=%x ... parmN=%x var1=%x ... varN=%x", parm1, ..., parmN, var1, ..., varN)`。如果变量的值在运行时找不到，输出`=?`。

**$$locals**

同`$$vars`，只输出本地变量。

**$$parms**

同`$$vars`，只输出函数入参。

**$$return**

仅在带`return`的探针中可用。如果被监控的函数有返回值，它等价于`sprintf("return=%x", $return)`，否则为空字符串。

下面的例子中，我们会输出`vfs_read`的入参：

```
stap -e 'probe kernel.function("vfs_read") {printf("%s\n", $$parms); exit(); }' 
```

`vfs_read`的入参有四个：`file`，`buf`，`count`，和`pos`。`$$params`会给这些入参生成描述字符串。在这个例子里，四个变量都是指针。下面是之前的命令的输出：

```
file=0xffff8800b40d4c80 buf=0x7fff634403e0 count=0x2004 pos=0xffff8800af96df48 
```

关输出个地址值没什么用啊。要想输出指针指向的值，我们可以加上`$`后缀。下面的命令使用`$`后缀来输出`vfs_read`入参的实际值：

```
stap -e 'probe kernel.function("vfs_read") {printf("%s\n", $$parms$); exit(); }' 
```

输出的结果：

```
file={.f_u={...}, .f_path={...}, .f_op=0xffffffffa06e1d80, .f_lock={...}, .f_count={...}, .f_flags=34818, .f_mode=31, .f_pos=0, .f_owner={...}, .f_cred=0xffff88013148fc80, .f_ra={...}, .f_version=0, .f_security=0xffff8800b8dce560, .private_data=0x0, .f_ep_links={...}, .f_mapping=0xffff880037f8fdf8} buf="" count=8196 pos=-131938753921208 
```

只使用`$`后缀的话，是不会展开结构体里面嵌套的结构体的。要想展开嵌套的结构体，你需要使用`$$`后缀。下面是一个使用`$$`的例子：

```
stap -e 'probe kernel.function("vfs_read") {printf("%s\n", $$parms$$); exit(); }' 
```

注意`$$`的输出，会受到字符串最长长度的限制。来自上面命令的输出，就因此被截断了：

```
file={.f_u={.fu_list={.next=0xffff8801336ca0e8, .prev=0xffff88012ded0840}, .fu_rcuhead={.next=0xffff8801336ca0e8, .func=0xffff88012ded0840}}, .f_path={.mnt=0xffff880132fc97c0, .dentry=0xffff88001a889cc0}, .f_op=0xffffffffa06f64c0, .f_lock={.raw_lock={.slock=196611}}, .f_count={.counter=2}, .f_flags=34818, .f_mode=31, .f_pos=0, .f_owner={.lock={.raw_lock={.lock=16777216}}, .pid=0x0, .pid_type=0, .uid=0, .euid=0, .signum=0}, .f_cred=0xffff880130129a80, .f_ra={.start=0, .size=0, .async_size=0, .ra_pages=32, . 
```

## 条件语句

有些时候，你写的 SystemTap 脚本较为复杂，可能需要用上条件语句。SystemTap 支持 C 风格的条件语句，另外还支持`foreach (VAR in ARRAY) {}`形式的遍历。

## 命令行参数

通过`$`或`@`加个数字的形式可以访问对应位置的命令行参数。用`$`会把用户输入当作整数，用`@`会把用户输入当作字符串。

```
probe kernel.function(@1) { }
probe kernel.function(@1).return { } 
```

上面的脚本期望用户把要监控的函数作为命令行参数传递进来。你可以让脚本接受多个命令行参数，分别命名为`@1`，`@2`等等，按用户输入的次序逐个对应。

# 关联数组

# 3.4\. 关联数组

SystemTap 支持关联数组。关联数组就像其它编程语言中的 map/dict/hash，你可以把它看作由互不相同的键所组成的数组，每个键都有一个关联的值。

关联数组需要定义为全局变量。访问关联数组的值的语法跟 awk 类似，就是`array_name[index_expression]`。 这里的`array_name`指关联数组的名字，`index_expression`指数组中某个唯一的键。比如在下面的例子中，我们需要在数组`foo`中存 tom、dick、harry 三个人的年龄，可以这么写：

```
foo["tom"] = 23
foo["dick"] = 24
foo["harry"] = 25 
```

在一个数组语句中你最多可以指定**九个**表达式，每个表达式间以`,`隔开。这样做可以给单个键附加多个信息。下面一行代码中，数组`device`的键包含五个表达式：进程 PID，可执行程序名，用户 UID，父进程 PID，和字符串“W”。`devname`值关联到这个键上面。

```
device[pid(),execname(),uid(),ppid(),"W"] = devname 
```

> 所有的关联数组都必须是全局变量，不管它们是否使用在多个探针内。

# 数组操作

# 3.5\. 数组操作

本节将列举 SystemTap 中若干常用的数组操作。

## 设置给定键的值

使用`=`来设置给定键所对应的值，正如：

```
foo[tid()] = gettimeofday_s() 
```

SystemTap 会把`tid()`的结果作为一个键，并把`gettimeofday_s()`的结果赋给这个键。如果这个键已经存在`foo`中，原先关联的值会被覆盖掉。

## 获取给定键的值

使用`array_name[index_expression]`可以获取对应键上的值。比如：

```
delta = gettimeofday_s() - foo[tid()] 
```

> 如果数组中没有`index_expression`对应的键，默认情况下它会返回 0（在数值计算中）或者空字符串（在字符串操作中）。

## 自增给定键的值

使用`++`来增加对应键上的值，比如：`array_name[index_expression] ++`。在下面的例子里，每次`vfs.read`都会把当前进程名所关联的值加一：

```
probe vfs.read
{
  reads[execname()] ++
} 
```

你可以用`if (index_expression in array_name)`来判断数组是否有指定的键。

## 遍历数组中的多个元素

一旦已经收集了足够的信息到数组里，你往往需要去遍历它。正如上面的例子中，在收集了各个进程的读次数后，你可能需要遍历它，输出每个进程的结果。那该怎么做呢？

最好的方法就是使用`foreach`语句。看下这个例子：

```
global reads
probe vfs.read
{
  reads[execname()] ++
}

probe timer.s(3)
{
  foreach (count in reads)
    printf("%s : %d \n", count, reads[count])
} 
```

在第二个探针中的`foreach`语句里，`count`引用了`reads`的键，所以可以通过`reads[count]`读取对应键所关联的值。

在这个`foreach`语句里面，我们依次遍历`reads`的每个值。假如我们不想遍历整个数组，或者想指定遍历的顺序，该怎么做呢？你可以给数组名加个后缀`+`来表示按升序遍历，或`-`按降序遍历。另外，你可以用`limit`加一个数字来限制迭代的次数。 看下这个类似于上一个探针的例子：

```
probe timer.s(3)
{
  foreach (count in reads- limit 10)
    printf("%s : %d \n", count, reads[count])
} 
```

上面的`foreach`语句会按关联的值降序遍历数组。`limit 10`表示`foreach`语句只会迭代 10 次（也即输出最高的 10 个值）。

## 清除数组或数组中某个元素

有时，你需要清除数组值某个值，或者清空整个数组以便于在另一个探针值重用。在之前统计`vfs.read`的例子里，每三秒统计一次各个进程的调用读操作的次数。如果要想统计三秒内各个进程的数据，需要每三秒清空一次数组。你可以使用`delete`运算符来删除数组中的某个元素，或整个数组。看下下面的例子：

```
global reads
probe vfs.read
{
  reads[execname()] ++
}

probe timer.s(3)
{
  foreach (count in reads)
    printf("%s : %d \n", count, reads[count])
  delete reads
} 
```

在上面的例子中，第二个探针仅输出三秒内每个进程的读次数。这里的`delete`语句清空了整个`reads`数组。

## 使用聚集变量（use aggregates）

有时候你需要快速处理新的数值，并且数据量较大，这时候可以考虑使用聚集变量（aggregates），因为它实现了对数据的流式处理。聚集变量可以用作全局变量，也可以用作数组中的值。使用`<<<`运算符可以往聚集变量中添加新数据。

```
global reads
probe vfs.read
{
  reads[execname()] <<< $count
} 
```

假设在上面的例子中，`$count`的值是一段时间内当前进程的读次数。`<<<`会把`$count`的值存储到`reads`数组`execname()`关联的聚集变量中。请注意，我们是把值存储在聚集变量里面；它们既没有加到原来的值上，也没有覆盖掉原来的值。可以这么说，就像是`reads`数组值每个键都有多个关联的值，并且探针的每次触发都会添加新的值。

要想从聚集变量中获取汇总的结果，使用这样的语法`@extractor(variable/array index expression)`。`extractor`可以取以下的函数：

**count**

返回`variable/array index expression`中存储的数值的数目。以上面为例，`@count(reads[execname()])`返回对应进程的聚集变量所存储的数据数。

**sum**

返回`variable/array index expression`中存储的数值的和。以上面为例，`@count(reads[execname()])`返回对应进程的读总数。

**min**

返回`variable/array index expression`中存储的数值的最小值。

**max**

返回`variable/array index expression`中存储的数值的最大值。

**avg**

返回`variable/array index expression`中存储的数值的数目。

你可以使用多重索引表达式在数组里关联一个聚集变量（最多使用 9 个索引）。这么做的好处在于，你可以在数组中附加更多的上下文信息。举个例子：

```
global reads
probe vfs.read
{
  reads[execname(),pid()] <<< 1
}

probe timer.s(3)
{
  foreach([var1,var2] in reads)
    printf("%s (%d) : %d \n", var1, var2, @count(reads[var1,var2]))
} 
```

在上面的例子中，第一个探针记录每个进程的`vfs.read`次数。跟之前的例子不同的是，这里的数组同时使用进程名和 PID 作为索引。

在第二个探针里，我们使用`foreach`语句遍历并输出每个进程的数据。注意这里我们分别使用`var1`和`var2`来引用进程名和 PID。

# Tapsets

## 3.6\. Tapsets

tapsets 是一些包含常用的探针和函数的内置脚本，你可以在 SystemTap 脚本中复用它们。当用户运行一个 SystemTap 脚本时，SystemTap 会检测脚本中的事件和处理程序，并在翻译脚本成 C 代码之前，加载用到的 tapset。（可以回顾下本章开头所讲到的，SystemTap 会话的启动过程） 就像 SystemTap 脚本一样，tapset 的拓展名也是`.stp`。默认情况下 tapset 位于`/usr/share/systemtap/tapset/`。跟 SystemTap 脚本不同的是，tapset 不能被直接运行；它只能作为库使用。 tapset 库让用户能够在更高的抽象层次上定义事件和函数。tapset 提供了一些常用的内核函数的别名，这样用户就不需要记住完整的内核函数名了（尤其是有些函数名可能会因内核版本的不同而不同）。另外 tapset 也提供了常用的辅助函数，比如之前我们见过的`thread_indent()`。