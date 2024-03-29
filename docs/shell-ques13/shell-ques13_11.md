# 11._ 与 _ 差在哪

## shell 十三问之 11：>与< 差在哪？

* * *

这次的题目，之前我在 CU 的 shell 版说明过了： (原帖的连接在论坛改版后，已经失效) 这次我就不重写了，将帖子的内容“抄”下来就是了...

#### 1\. 文件描述符(fd, File Descriptor)

* * *

谈到`I/O redirection`,不妨先让我们认识一下`File Descriptor`(`fd`，文件描述符)。

进程的运算，在大部分情况下，都是进行数据(data)的处理， 这些数据从哪里，读进来？又输出到哪里呢？ 这就是 file descriptor(fd)的功用了。

在 shell 的进程中，最常使用的`fd`大概有三个，分别为:

*   0：standard Input (`STDIN`)
*   1: standard output(`STDOUT`)
*   2: standard Error output （`STDERR`）

在标准情况下，这些 fd 分别跟如下设备(device)关联：

*   `stdin`(0): keyboard
*   `stdout`(1): monitor
*   `stderr`(2): monitor

> **Tips:** linux 中的文件描述符(fd)用整数表示。 linux 中任何一个进程都默认打开三个文件, 这三个文件对应的文件描述符分别是：0, 1, 2; 即 stdin, stdout, stderr.

我们可以用如下命令测试一下：

```
$ mail -s test root
this is a test mail。
please skip.
^d (同时按下 ctrl 跟 d 键) 
```

很明显，`mail`进程所读进的数据，就是从 `stdin` 也就是 keyboard 读进的。 不过，不见得每个进程的`stdin`都跟`mail`一样 从`keyboard`读进，因为进程的作者可以从文件参数读进`stdin`， 如：

```
$ cat /etc/passwd 
```

但，要是`cat`之后没有文件参数则如何呢？ 哦， 请你自己玩玩看...^_^

```
$ cat 
```

> **Tips:**
> 
> 请留意数据输出到哪里去了， 最后别忘了按`ctrl+d`(`^d`), 退出 stdin 输入。

至于`stdout`与`stderr`，嗯...等我有空再续吧...^_^ 还是，有哪位前辈来玩接龙呢？

相信，经过上一个练习后， 你对`stdin`与`stdout`应该不难理解了吧？ 然后，让我们看看`stderr`好了。

事实上，`stderr`没什么难理解的： 说白了就是“错误信息”要往哪里输出而已... 比方说, 若读进的文件参数不存在的， 那我们在 monitor 上就看到了：

```
$ ls no.such.file
ls: no.such.file: No such file or directory 
```

若同一个命令，同时成生`stdout`与`stderr`呢？ 那还不简单，都送到 monitor 来就好了：

```
$ touch my.file
$ ls my.file on.such.file
ls: no.such.file: No such file or directory
my.file 
```

okay, 至此，关于 fd 及其名称、还有相关联的设备， 相信你已经没问题了吧？

* * *

#### 2\. I/O 重定向(I/O Redirection)

* * *

那好，接下来让我们看看如何改变这些 fd 的预设数据通道。

*   用`<` 来改变读进的数据通道(stdin),使之从指定的文件读进。
*   用`>` 来改变输出的数据通道(stdout，stderr),使之输出到指定的文件。

* * *

##### 2.1 输入重定向`n<`(input redirection)

* * *

比方说：

```
$ cat < my.file 
```

就是从 my.file 读入数据

```
$ mail -s test root < /etc/passwd 
```

则是从/etc/passwd 读入...

这样一来，stdin 将不再是从 keyboard 读入， 而是从指定的文件读入了...

严格来说，**`<`符号之前需要指定一个 fd 的(之前不能有空白)，但因为 0 是`<`的预设值，因此，`<`与`0<`是一样的***。

okay，这样好理解了吧？

那要是用两个`<`，即`<<`又是啥呢？ **这是所谓的`here document`, 它可以让我们输入一段文本， 直到读到`<<` 后指定的字符串**。

比方说：

```
$ cat <
```

这样的话, `cat`会读入 3 个句子， 而无需从 keyboard 读进数据且要等到(ctrl+d, ^d)结束输入。

* * *

##### 2.2 重定向输出`>n`(output redirection)

* * *

当你搞懂了`0<` 原来就是改变`stdin`的数据输入通道之后， 相信要理解如下两个 redirection 就不难了：

*   `1>` #改变 stdout 的输出通道；
*   `2>` #改变 stderr 的输出通道；

两者都是将原来输出到 monitor 的数据， 重定向输出到指定的文件了。

**由于 1 是`>`的预设值， 因此，`1>`与`>`是相同的，都是改变`stdout`**.

用上次的 ls 的例子说明一下好了:

```
$ ls my.file no.such.file 1>file.out
ls: no.such.file: No such file or directory 
```

这样 monitor 的输出就只剩下`stderr`的输出了， 因为`stdout`重定向输出到文件 file.out 去了。

```
$ ls my.file no.such.file 2>file.err
my.file 
```

这样 monitor 就只剩下了`stdout`, 因为`stderr`重定向输出到文件 file.err 了。

```
$ ls my.file no.such.file 1>file.out 2>file.err 
```

这样 monitor 就啥也没有了， 因为`stdout`与`stderr`都重定向输出到文件了。

呵呵，看来要理解`>`一点也不难啦是不？ 没骗你吧？ ^_^ **不过有些地方还是要注意一下的**。

```
$ ls my.file no.such.file 1>file.both 2>file.both 
```

假如`stdout`(1)与`stderr`(2)都同时在写入 file.both 的话， 则是采取"覆盖"的方式：后来写入覆盖前面的。

让我们假设一个`stdout`与`stderr`同时写入到 file.out 的情形好了；

*   首先`stdout`写入 10 个字符
*   然后`stderr`写入 6 个字符

那么，这时原本的`stdout`输出的 10 个字符， 将被`stderr`输出的 6 个字符覆盖掉了。

那如何解决呢？所谓山不转路转，路不转人转嘛， 我们可以换一个思维： 将`stderr`导进`stdout` 或者将`stdout`导进到`stderr`, 而不是大家在抢同一份文件，不就行了。 bingo 就是这样啦：

*   2>&1 #将`stderr`并进`stdout`输出
*   1>&2 或者 >&2 #将`stdout`并进`stderr`输出。

于是，前面的错误操作可以改写为:

```
$ ls my.file no.such.file 1>file.both 2>&1
$ ls my.file no.such.file 2>file.both >&2 
```

这样，不就皆大欢喜了吗？ ~~~ ^_^

不过，光解决了同时写入的问题还不够， 我们还有其他技巧需要了解的。 故事还没有结束，别走开广告后，我们在回来....

* * *

##### 2.3 I/O 重定向与 linux 中的`/dev/null`

* * *

okay，这次不讲 I/O Redirection, 请佛吧... (有没有搞错？`网中人`是否头壳烧坏了？...)嘻~~~^_^

学佛的最高境界，就是"四大皆空"。 至于是空哪四大块，我也不知，因为我还没有到那个境界.. 这个“空”字,却非常值得反复把玩： ---色即是空，空即是色 好了，施主要是能够领会"空"的禅意，那离修成正果不远了。

在 linux 的文件系统中，有个设备文件: `/dev/null`. 许多人都问过我，那是什么玩意儿？ 我跟你说好了，那就是"空"啦。

没错空空如也的空就是 null 了... 请问施主是否忽然有所顿悟了呢？ 然则恭喜了。

这个 null 在 I/O Redirection 中可有用的很呢？

*   将 fd `1`跟 fd `2`重定向到/dev/null 去，就可忽略 stdout, stderr 的输出。
*   将 fd `0`重定向到/dev/null，那就是读进空(nothing).

比方说，我们在执行一个进程时，会同时输出到 stdout 与 stderr， 假如你不想看到 stderr(也不想存到文件)， 那就可以：

```
$ ls my.file no.such.file 2>/dev/null
my.file 
```

若要相反：只想看到 stderr 呢？ 还不简单将 stdout，重定向的/dev/null 就行：

```
$ ls my.file no.such.file >/dev/null
ls: no.such.file: No such file or directory 
```

那接下来，假如单纯的只跑进程，而不想看到任何输出呢？ 哦，这里留了一手，上次没讲的法子,专门赠与有缘人... ^_^ 除了用 `>/dev/null 2>&1`之外，你还可以如此：

```
$ ls my.file no.such.file &>/dev/null 
```

> **Tips:**
> 
> 将&>换成>&也行！

* * *

##### 2.4 重定向输出 append (`>>`)

* * *

okay？ 请完佛，接下来，再让我们看看如下情况：

```
$ echo "1" > file.out
$ cat file.out
1
$ echo "2" > file.out
$ cat file.out
2 
```

看来，我们在重定向 stdout 或 stderr 进一个文件时， 似乎永远只能获得最后一次的重定向的结果. 那之前的内容呢？

呵呵，要解决这个问题，很简单啦，将`>`换成`>>` 就好了；

```
$ echo "3" >> file.out
$ cat file.out
2
3 
```

如此一来，被重定向的文件的之前的内容并不会丢失， 而新的内容则一直追加在最后面去。so easy?...

但是，只要你再次使用`>`来重定向输出的话， 那么，原来文件的内容被 truncated(清洗掉)。 这是，你要如何避免呢？ ----备份， yes，我听到了，不过，还有更好的吗？ 既然与施主这么有缘分，老衲就送你一个锦囊妙法吧：

```
$ set -o noclobber
$ echo "4" > file.out
-bash：file: cannot overwrite existing file. 
```

那，要如何取消这个限制呢? 哦，将`set -o`换成 `set +o`就行了：

```
$ set +o noclobber
$ echo "5" > file.out
$ cat file.out
5 
```

再问：那有办法不取消而又“临时”改写目标文件吗？ 哦，佛曰：不可告也。 啊，~~~开玩笑的，开玩笑啦~~~^_^， 哎，早就料到人心是不足的了

```
$ set -o noclobber
$ echo "6" >| file.out
$ cat file.out
6 
```

留意到没有： **在`>`后面加个`|`就好， 注意： `>`与`|`之间不能有空白哦**...

* * *

##### 2.5 I/O Redirection 的优先级

* * *

呼....(深呼吸吐纳一下吧)~~~ ^_^ 再来还有一个难题要你去参透呢:

```
$ echo "some text here" >file
$ cat < file
some text here
$cat < file >file.bak
$cat < file.bak
some text here
$cat < file >file 
```

嗯？注意到没有？ ---怎么最后那个 cat 命令看到 file 是空的呢？ why？ why？ why？

前面提到：`$cat < file > file`之后， 原本有内容的文件，结果却被清空了。 要理解这个现象其实不难， 这只是 priority 的问题而已： **在 IO Redirection 中, stdout 与 stderr 的管道先准备好， 才会从 stdin 读入数据。** 也就是说，在上例中，`>file`会将 file 清空， 然后才读入 `< file`。 但这时候文件的内容已被清空了，因此就变成了读不进任何数据。

哦，~~~原来如此~~~^_^ 那...如下两例又如何呢？

```
$ cat <> file
$ cat < file >>file 
```

嗯...同学们，这两个答案就当练习题喽， 下课前交作业。

> **Tips:** 我们了解到`>file`能够快速把文件 file 清空； 或者使用`:>file`同样可以清空文件， `:>file`与`>file`的功能： 若文件 file 存在，则将 file 清空; 否则，创建空文件 file (等效于`touch file`); 二者的差别在于`>file`的方式不一定在所有的 shell 的都可用。
> 
> `exec 5<>file; echo "abcd" >&5; cat <&5` 将 file 文件的输入、输出定向到文件描述符 5， 从而描述符 5 可以接管 file 的输入输出； 因此，`cat <>file`等价于`cat < file`。
> 
> 而`cat < file >>file`则使 file 内容成几何级数增长。

好了， I/O Redirection 也快讲完了， sorry,因为我也只知道这么多而已啦~~~嘻~~~^_^ 不过，还有一样东东是一定要讲的，各位观众(请自行配乐~!#@$%): 就是`pipe line`也。

* * *

##### 2.6 管道(pipe line)

* * *

谈到`pipe line`，我相信不少人都不会陌生： 我们在很多 command line 上常看到`|`符号就是 pipe line 了。

不过，pipe line 究竟是什么东东呢？ 别急别急...先查一下英文字典，看看 pipe 是什么意思？ 没错他就是“水管”的意思... 那么，你能想象一下水管是怎样一个根接一根的吗？ 又， 每根水管之间的 input 跟 output 又如何呢？ 灵光一闪：原来 pipe line 的 I/O 跟水管的 I/O 是一模一样的： **上一个命令的 stdout 接到下一个命令的 stdin 去了** 的确如此。不管在 command line 上使用了多少个 pipe line， 前后两个 command 的 I/O 是彼此连接的 (恭喜：你终于开放了 ^_^ )

不过...然而...但是... ...stderr 呢？ 好问题不过也容易理解： 若水管漏水怎么办？ 也就是说：在 pipe line 之间, 前一个命令的 stderr 是不会接进下一个命令的 stdin 的， 其输出，若不用 2>file 的话，其输出在 monitor 上来。 这点请你在 pipe line 运用上务必要注意的。

那，或许你有会问: **有办法将 stderr 也喂进下一个命令的 stdin 吗？** (贪得无厌的家伙)，方法当然是有的，而且，你早已学习过了。 提示一下就好：**请问你如何将 stderr 合并进 stdout 一同输出呢？ 若你答不出来，下课后再来问我...(如果你脸皮足够厚的话...)

或许，你仍意犹未尽，或许，你曾经碰到过下面的问题： 在`cmd1 | cmd2 | cmd3 | ...` 这段 pipe line 中如何将 cmd2 的输出保存到一个文件呢？

若你写成`cmd1 | cmd2 >file | cmd3`的话， 那你肯定会发现`cmd3`的 stdin 是空的，(当然了，你都将 水管接到别的水池了) 聪明的你或许会如此解决：

```
cmd1 | cmd2 >file; cmd3 < file 
```

是的，你可以这样做，但最大的坏处是： file I/O 会变双倍，在 command 执行的整个过程中， file I/O 是最常见的最大效能杀手。 凡是有经验的 shell 操作者，都会尽量避免或降低 file I/O 的频度。

那上面问题还有更好的方法吗？ 有的，那就是`tee`命令了。 **所谓的`tee`命令是在不影响原本 I/O 的情况下， 将 stdout 赋值到一个文件中去。** 因此，上面的命令行，可以如此执行：

```
cmd1 | cmd2 | tee file | cmd3 
```

在预设上，`tee`会改写目标文件， 若你要改为追加内容的话，那可用-a 参数选项。

基本上，pipe line 的应用在 shell 操作上是非常广泛的。 尤其是在 text filtering 方面， 如，cat, more, head, tail, wc, expand, tr, grep, sed, awk...等等文字处理工具。 搭配起 pipe line 来使用，你会觉得 command line 原来活得如此精彩的。 常让人有“众里寻他千百度，蓦然回首，那人却在灯火阑珊处”之感...

好了，关于 I/O Redirection 的介绍就到此告一段落。 若日后，有空的话，在为大家介绍其他在 shell 上好玩的东西。