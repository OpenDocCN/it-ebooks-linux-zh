# 3.别人 echo 你也 echo;是问 echo 知多少

## shell 十三问之 3：别人 echo、你也 echo，是问 echo 知多少？

* * *

承接上一章介绍的`command line`, 这里我们用`echo`这个命令加以进一步说明。

> **温习** 标准的`command line`三个组成部分：`command_name option argument`

`echo`是一个非常简单、直接的 Linux 命令：

```
$echo argument 
```

`echo`将 argument 送出到`标准输出`(`stdout`),通常是在监视器(monitor)上输出。

> **Note：**
> 
> 在 linux 系统中任何一个进程默认打开三个文件：stdin、stdout、stderr.
> 
> stdin 标准输入
> 
> stdout 标准输出
> 
> stderr 标准错误输出

为了更好理解，不如先让我们先跑一下`echo`命令好了：

```
$echo

$ 
```

你会发现只有一个空白行，然后又回到了`shell prompt`上了。 这是因为`echo`在预设上，在显示完 argument 之后，还会送出以一个换行符号 (`new-line charactor`). 但是上面的 command `echo`并没有任何 argument，那结果就只剩一个换行符号。 若你要取消这个换行符号， 可以利用`echo`的`-n` 选项:

```
$echo -n
$ 
```

不妨让我们回到`command line`的概念上来讨论上例的 echo 命令好了： `command line`只有 command_name(`echo`)及 option(`-n`),并没有显示任何`argument`。

要想看看`echo`的`argument`，那还不简单接下来，你可以试试如下的输入：

```
$echo first line
first line
$echo -n first line
first line $ 
```

以上两个`echo`命令中，你会发现`argument`的部分显示在你的屏幕， 而换行符则视 `-n` 选项的有无而别。 很明显的，第二个`echo`由于换行符被取消了， 接下来的`shell prompt`就接在输出结果的同一行了... ^_^。

事实上，`echo`除了`-n` 选项之外，常用选项有：

*   -e: 启用反斜杠控制字符的转换(参考下表)
*   -E: 关闭反斜杠控制字符的转换(预设如此)
*   -n: 取消行末的换行符号(与-e 选项下的\c 字符同意)

关于`echo`命令所支持的反斜杠控制字符如下表：

|  | 转义字符 | 字符的意义 |
| --- | --- | --- |
|  | \a | ALERT / BELL(从系统的喇叭送出铃声) |  |
|  | \b | BACKSPACE, 也就是向左退格键 |  |
|  | \c | 取消行末之换行符号 |
|  | \E | ESCAPE, 脱字符键 |  |
|  | \f | FORMFEED, 换页字符 |  |
|  | \n | NEWLINE, 换行字符 |  |
|  | \r | RETURN, 回车键 |  |
|  | \t | TAB, 表格跳位键 |  |
|  | \v | VERTICAL TAB, 垂直表格跳位键 |  |
|  | \n | ASCII 八进制编码(以 x 开头的为十六进制)，此处的 n 为数字 |
|  | \ | 反斜杠本身 |  |

> **Note：** 上述表格的资料来自 O'Reilly 出版社的**Learning the Bash Shell, 2nd Ed**.

或许，我们可以通过实例来了解`echo`的选项及控制字符：

例一：

```
$ echo -e "a\tb\tc\n\d\te\tf"
a    b    c
d    e    f
$ 
```

上例中，用\t 来分割 abc 还有 def，及用\n 将 def 换至下一行。

例二：

```
$echo -e "\141\011\142\011\143\012\144\011\145\011\146"
a    b    c
d    e    f 
```

与例一中结果一样，只是使用 ASCII 八进制编码。

例三：

```
$echo -e "\x61\x09\x62\x09\x63\x0a\x64\x09\x65\x09\x66"
a    b    c
d    e    f 
```

与例二差不多，只是这次换用 ASCII 的十六进制编码。

例四：

```
$echo -ne "a\tb\tc\nd\te\bf\a"
a       b       c
d       f $ 
```

因为 e 字母后面是退格键(\b)，因此输出结果就没有 e 了。 在结束的时听到一声铃响，是\a 的杰作。 由于同时使用了-n 选项，因此`shell prompt`紧接在第二行之后。 若你不用-n 的话，那你在\a 后再加个\c，也是同样的效果。

事实上，在日后的`shell`操作及`shell script`设计上， `echo`命令是最常被使用的命令之一。 比方说，使用`echo`来检查变量值：

```
$ A=B
$ echo $A
B
$ echo $?
0 
```

> **Note:** 关于变量的概念，我们留到以下的两章跟大家说明。

好了，更多的关于`command line`的格式， 以及`echo`命令的选项， 请您自行多加练习、运用了...