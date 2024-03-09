# 10.&&与 __ 差在哪

## shell 十三问之 10：&& 与 || 差在哪？

* * *

好不容易，进入了两位数的章节了... 一路走来，很辛苦吧？也很快乐吧？ ^_^

在解答本章题目之前，先让我们了解一个概念： return value。

我们在 shell 下跑的每一个 command 或 function， 在结束的时候都会传回父进程一个值，称为 `return value`。

在 shell command line 中可用`$?`， 这个变量得到最"新"的一个`return value`， 也就是刚刚结束的那个进程传回的值。

`Return Value`(RV)的取值为 0-255 之间， 由进程或者 script 的作者自行定义：

*   若在 script 里，用 exit RV 来指定其值; 若没有指定, 在结束时，以最后一个命令的 RV，为 script 的 RV 值。

*   若在 function 里，则用 return RV 来代替 exit RV 即可。

**`Return Value`的作用：用来判断进程的退出状态(exit status)**. 进程的退出状态有两种：

*   0 值为"真"(true)
*   非 0 值为"假"(false)

举个例子来说明好了： 假设当前目录内有一个 my.file 的文件， 而 no.file 是不存在的：

```
$ touch my.file
$ ls my.file
$ echo $? #first echo
0
$ ls no.file
ls: no.file: No such file or directory
$ echo $?     #second echo
1
$ echo $?     #third echo
0 
```

上例的：

*   第一个 echo 是关于`ls my.file`的 RV，可得到 0 的值，因此为 true。
*   第二个 echo 是关于`ls no.file`的 RV，得到非 0 的值，因此为 false。
*   第三个 echo 是关于`echo $?`的 RV，得到 0 值， 因此为 true。

请记住： 每一个 command 在结束时，都会返回`return value`，不管你跑什么命令... 然而，有一个命令却是“专门”用来测试某一条而返回`return value`， 以供 true 或 false 的判断， 它就是`test`命令。

若你用的是 bash， 请在 command line 下， 打`man test`，或者 `man bash` 来了解这个`test`的用法。 这是你可用作参考的最精准的文件了，要是听别人说的，仅作参考就好...

下面，我只简单作一些辅助说明，其余的一律以 `man`为准： 首先，`test`的表达式，我们称为 expression，其命令格式有两种：

```
test expression 
```

或者

```
[ expression ] 
```

> **Note:**
> 
> 请务必注意 `[]` 之间的空白键!

用哪一种格式无所谓，都是一样的效果。 (我个人比较喜欢后者...)

其次，bash 的`test`目前支持的测试对象只有三种：

*   string：字符串，也就是纯文字。
*   integer：整数(0 或正整数、不含负数或小数)
*   file: 文件

请初学者，一定要搞清楚这三者的差异， 因为`test`所使用的 expression 是不一样的。

以 A=123 这个变量为例：

*   `[ "$A" = 123 ]` #是字符串测试，测试$A 是不是 1、2、3 这三个字符。

*   `[ "$A" -eq 123 ]` #是整数测试，以测试$A 是否等于 123.

*   `[-e "$A" ]` #文件测试，测试 123 这份文件是否存在.

第三， 当 expression 测试为“真”时， `test`就返回 0(true)的`return value`; 否则，返回非 0(false).

若在 expression 之前加一个`!`(感叹号)，则在 expression 为假时，return value 为 0, 否则, return value 为非 0 值。

同时，`test`也允许多重复合测试：

*   expression1 -a expression2 #当两个 expression 都为 true，返回 0，否则，返回非 0；
*   expression1 -o expression2 #当两个 expression 均为 false 时，返回非 0，否则，返回 0；

例如：

```
[ -d "$file"  -a  -x "$file" ] 
```

表示当$file 是一个目录，且同时具有 x 权限时，test 才会为 true。

第四，在 command line 中使用`test`时，请别忘记命令行的“重组”特性， 也就是在碰到 meta 时，会先处理 meta，在重新组建命令行。 (这个概念在第二章和第四章进行了反复强调)

比方说， 若`test`碰到变量或者命令替换时， 若不能满足 expression 的格式时，将会得到语法错误的结果。

举例来说好了：

关于`[ string1 = string2 ]`这个 test 格式， 在等号两边必须要有字符串，其中包括空串(null 串,可用 soft quote 或者 hard quote 取得)。

假如$A 目前没有定义，或被定义为空字符串的话， 那如下的用法将会失败：

```
$ unset A
$ [ $A = abc ]
[: =: unary oprator expected 
```

这是因为命令行碰到$这个 meta 时，会替换$A 的值， 然后，再重组命令行，那就变成了： `[ = abc ]`, 如此一来，=的左边就没有字符串存在了， 因此，造成 test 的语法错误。 但是，下面这个写法则是成立的。

```
$ [ "$A" = abc ]
$ echo $?
1 
```

这是因为命令行重组后的结果为： `[ "" = abc ]`, 由于等号的左边我们用 soft quote 得到一个空串， 而让 test 的语法得以通过...

读者诸君，请务必留意这些细节哦， 因为稍一不慎，将会导致`test`的结果变了个样。 若您对`test`还不是很有经验的话， 那在使用 test 时，不妨先采用如下这一个"法则":

**若在`test`中碰到变量替换，用 soft quote 是最保险的***。

若你对 quoting 不熟的话，请重新温习第四章的内容吧...^_^

okay, 关于更多的`test`的用法，老话一句：请看其 man page (`man test`)吧！^_^

虽然洋洋洒洒读了一大堆，或许你还在嘀咕...那...那个`return value`有啥用？

问得好: 告诉你：return value 的作用可大了， 若你想要你的 shell 变"聪明"的话，就全靠它了： 有了 return value， 我们可以让 shell 根据不同的状态做不同的事情...

这时候，才让我来揭晓本章的答案吧~~~~^_^

`&&` 与 `||` 都是用来"组建" 多个 command line 用的；

*   `command1 && command2` # command2 只有在 command1 的 RV 为 0(true)的条件下执行。
*   `command1 || command2` # command2 只有在 command1 的 RV 为非 0(false)的条件下执行。

以例子来说好了：

```
$ A=123
$ [ -n "$A" ] && echo "yes! it's true."
yes! it's true.
$ unset A
$ [ -n "$A" ] && echo "yes! it's true."
$ [ -n "$A" ] || echo "no, it's Not true."
no, it's Not true 
```

> **Note:**
> 
> `[ -n string ]`是测试 string 长度大于 0, 则为 true。

上例中，第一个`&&`命令之所以会执行其右边的`echo`命令， 是因为上一个`test`返回了 0 的 RV 值； 但第二个，就不会执行，因为`test`返回了非 0 的结果... 同理，`||`右边的`echo`会被执行，却正是因为左边的`test`返回非 0 所引起的。

事实上，我们在同一个命令行中，可用多个`&&` 或 `||` 来组建呢。

```
$ A=123
$ [ -n "$A" ] && echo "yes! it's true." || echo "no, it's Not ture."
yes! it's true.
$ unset A
$ [ -n "$A" ] && echo "yes! it's true." || echo "no, it's Not ture."
no, it's Not true 
```

怎样，从这一刻开始，你是否觉得我们的 shell 是“很聪明”的呢？ ^_^

好了，最后布置一道练习题给大家做做看： 下面的判断是：当$A 被赋值时，在看看其是否小于 100，否则输出 too big！

```
$ A=123
$ [ -n "$A" ] && [ "$A" -lt 100 ] || echo 'too big!'
$ too big! 
```

若我取消 A，照理说，应该不会输出文字啊，(因为第一个条件不成立)。

```
$ unset A
$ [ -n "$A" ] && [ "$A" -lt 100 ] || echo 'too big!'
$ too big! 
```

为何上面的结果也可得到呢？ 又如何解决呢？

> **Tips:**
> 
> 修改的方法有很多种， 其中一种方法可以利用第七章中介绍过 `command group`...

快告诉我答案，其余免谈....

解决方法 1：`sub-shell`：

```
$ unset A
$ [ -n "$A" ] && ( [ "$A" -lt 100 ] || echo 'too big!' ) 
```

解决方法二：`command group`:

```
$ unset A
$ [ -n "$A" ] && { [ "$A" -lt 100 ] || echo 'too big!'} 
```