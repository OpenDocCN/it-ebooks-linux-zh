# 解读错误信息

# 6\. 解读错误信息

本章解释使用 SystemTap 过程中常见的错误信息。

# 解析和文法错误

# 6.1\. 解析和文法错误

解析和文法错误发生在 SystemTap 解析脚本和编译成 C 代码时。举个例子，把无效的值赋给变量或数组时，会报类型错误。

**parse error: expected foo, saw bar**

脚本存在语法或排版错误。SystemTap 会探测到脚本中存在的不正确结构，并指出有问题的探针。 举个例子，下面的 SystemTap 脚本是有问题的，里面的探针缺了处理程序：

```
probe vfs.read
probe vfs.write 
```

尝试运行这个脚本，它会报告以下的错误信息，声称第 2 行第 1 列不应该是`probe`关键字。

```
parse error: expected one of '. , ( ? ! { = +='
    saw: keyword at perror.stp:2:1
1 parse error(s). 
```

**parse error: embedded code in unprivileged script**

脚本中嵌入了不安全的 C 代码。SystemTap 允许你通过`%{...%}`代码块嵌入 C 代码，以便于定义适合的 tapset。然而，这样做是不安全的，一旦你真的在脚本里这么做了，SystemTap 用这个错误来警告你。

如果你确信你的做法是安全的，并且拥有`stapdev`权限（或 root 权限），可以带上`-g`选项，以“guru”模式运行脚本来消除这个报错。（`stap -g script`）

**semantic error: type mismatch for identifier 'foo' ... string vs. long**

脚本中的函数`foo`使用了错误的类型（比如`%s`或`%d`）。在下面的例子中，格式标志符应该是`%s`而不是`%d`，因为`execname()`函数返回一个字符串：

```
probe syscall.open
{
  printf ("%d(%d) open\n", execname(), pid())
} 
```

**semantic error: unresolved type for identifier 'foo'**

你使用了一个变量（identifier），但是没办法推导出它的类型（数值或字符串）。举个例子，如果你在一个`printf`语句中使用了从未赋过值的变量，就会遇到这样的错误。

**semantic error: Expecting symbol or array index expression**

SystemTap 不能完成某个赋值操作，因为这个操作的接收者不合理。下面的示例代码就会发生这个错误：

```
probe begin { printf("x") = 1 } 
```

**while searching for arity N function, semantic error: unresolved function call**

脚本中的函数调用或数组索引表达式使用了不合理的参数个数。在 SystemTap，*arity*可以指数组的索引个数，也可以指函数的参数个数。

**semantic error: array locals not supported, missing global declaration?**

在脚本中使用了一个数组，却没有把它定义成全局变量（SystemTap 脚本中，全局变量可以定义在使用的位置之后）。如果一个数组使用的索引个数不一致，也会报告相似的错误。（SystemTap 中数组可以使用一组值作为索引，而不仅仅是一个下标）

**semantic error: variable 'foo' modified during 'foreach' iteration**

用`foreach`迭代数组`foo`的同时修改了该数组（赋新的值或使用了 delete）。如果在`foreach`迭代的过程中对数组`foo`调用了函数，也会显示这个错误。

**semantic error: probe point mismatch at position N, while resolving probe point foo**

SystemTap 无法找到事件或 SystemTap 函数`foo`的定义。通常意味着 SystemTap 在 tapset 库中找不到匹配 foo 的项。`N`表示错误的行号和列号。

**semantic error: no match for probe point, while resolving probe point foo**

SystemTap 因为一些原因不能解析事件或处理函数`foo`。比如说脚本包含事件`kernel.function("something")`，而`something`并不存在。在某些时候，这个错误也意味着脚本中包含不存在的内核文件名或源代码行号。

**semantic error: unresolved target-symbol expression**

脚本中的一个处理程序用到了某个目标变量（target variable)，但这个目标变量无法解析。这个错误意味着该目标变量在处理程序的上下文里不存在。也许是编译器把代码优化掉了。

**semantic error: libdwfl failure**

在处理调试信息的时候遇到一个问题。在大多数情况下，这个错误的产生源于安装的`kernel-debuginfo`包没有完全匹配要探测的内核。也许是安装的`kernel-debuginfo`包中存在某些完整性或正确性问题。

**semantic error: cannot find foo debuginfo**

SystemTap 找不到适合的`kernel-debuginfo`包。

# 运行时错误和警告

# 6.2\. 运行时错误和警告

运行时错误和警告发生在 SystemTap 安装了检测代码并开始收集数据的时候。

**⁠WARNING: Number of errors: N, skipped probes: M**

在运行时出错并/或跳过某些探针。由于诸如给定时间不足以执行完处理程序的原因，某些探针没有得到执行，`N`和`M`就是这些探针的数目。

**⁠division by 0**

代码里出现了除零错误。

**⁠aggregate element not found**

在一个空的聚集变量上调用除`@count`以外的提取函数。这就跟除零差不多。关于聚集变量的更多信息，参见 3.5 数组操作中的“使用聚集变量”部分。

**⁠aggregation overflow**

聚集变量数组中包含太多的键。（译注：前文有提及，数组的索引中最多只能使用九个键）

**⁠MAXNESTING exceeded**

过多的嵌套函数调用。默认函数调用层级是 10。可以使用`-DMAXNESTING=NN`重编译脚本来修改这个限制。

**⁠MAXACTION exceeded**

处理程序太长了。默认一个探针的处理程序里面最多只能执行 1000 个语句。可以使用`--DMAXACTION=NN`或`-DMAXACTION_INTERRUPTIBLE=NN`重编译脚本来修改这个限制。

**⁠kernel/user string copy fault at ADDR**

处理程序试图把一个来自内核或用户空间的字符串拷贝到无效地址`ADDR`。

**pointer dereference fault**

指针解引用时发生了一个错误，可能发生在诸如计算目标变量的时候。