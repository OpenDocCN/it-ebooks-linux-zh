# 4.__ 与''的差在哪

## shell 十三问之 4：""(双引号)与''(单引号)差在哪？

* * *

还是回到我们的`command line`来吧...

经过前面两章的学习，应该很清楚当你在`shell prompt`后面敲打键盘, 直到按下`Enter`键的时候，你输入的文字就是`command line`了， 然后`shell`才会以进程的方式执行你所交给它的命令。 但是，你又可知道：你在`command line`中输入的每一个文字， 对`shell`来说，是有类别之分的呢？

简单而言，(我不敢说精确的定义，注 1), `command line`的每一个`charactor`, 分为如下两种：

*   literal：也就是普通的纯文字，对`shell`来说没特殊功能；
*   meta: 对`shell`来说，具有特定功能的特殊保留元字符。

> **Note:**
> 
> 对于`bash shell`在处理`comamnd line`的顺序说明， 请参考 O'Reilly 出版社的**Learning the Bash Shell，2nd Edition**， 第 177-180 页的说明，尤其是 178 页的流程图：Figure 7-1 ...

`literal`没什么好谈的， 像 abcd、123456 这些"文字"都是 literal...(so easy? ^_^) 但 meta 却常使我们困惑...(confused?) 事实上，前两章，我们在`command line`中已碰到两个 似乎每次都会碰到的 meta：

*   `IFS`：有`space`或者`tab`或者`Enter`三者之一组成(我们常用 space)
*   `CR`: 由`Enter`产生；

`IFS`是用来拆解`command line`中每一个词(word)用的， 因为`shell command line`是按词来处理的。 而`CR`则是用来结束`command line`用的，这也是为何我们敲`Enter`键， 命令就会跑的原因。

除了常用的`IFS`与`CR`, 常用的 meta 还有：

| meta 字符 | meta 字符作用 |
| --- | --- |
| = | 设定变量 |
| $ | 作变量或运算替换(请不要与`shell prompt`混淆) | 命令 |
| > | 输出重定向(重定向 stdout) |
| < | 输入重定向(重定向 stdin) |
| \ |  | 命令管道 |
| & | 重定向 file descriptor 或将命令至于后台(bg)运行 |
| () | 将其内部的命令置于 nested subshell 执行，或用于运算或变量替换 |
| {} | 将期内的命令置于 non-named function 中执行，或用在变量替换的界定范围 |
| ; | 在前一个命令执行结束时，而忽略其返回值，继续执行下一个命令 |
| && | 在前一个命令执行结束时，若返回值为 true，继续执行下一个命令 |
| \ | \ |  | 在前一个命令执行结束时，若返回值为 false，继续执行下一个命令 |
| ! | 执行 histroy 列表中的命令 |
| ... | ... |

假如我们需要在`command line`中将这些保留元字符的功能关闭的话， 就需要 quoting 处理了。

在`bash`中，常用的 quoting 有以下三种方法：

*   hard quote：''(单引号)，凡在 hard quote 中的所有 meta 均被关闭；
*   soft quote：""(双引号)，凡在 soft quote 中大部分 meta 都会被关闭，但某些会保留(如$);
*   escape: \ (反斜杠)，只有在紧接在 escape(跳脱字符)之后的单一 meta 才被关闭；

> **Note:**
> 
> 在 soft quote 中被豁免的具体 meta 清单，我不完全知道， 有待大家补充，或通过实践来发现并理解。

下面的例子将有助于我们对 quoting 的了解：

```
$ A=B C #空白符未被关闭，作为 IFS 处理
$ C：command not found.
$ echo $A

$ A="B C" #空白符已被关掉，仅作为空白符
$ echo $A
B C 
```

在第一个给 A 变量赋值时，由于空白符没有被关闭， `command line` 将被解释为： `A=B 然后碰到<IFS>，接着执行 C 命令` 在第二次给 A 变量赋值时，由于空白符被置于 soft quote 中， 因此被关闭，不在作为`IFS`； `A=B<space>C` 事实上，空白符无论在 soft quote 还是在 hard quote 中， 均被关闭。Enter 键字符亦然：

```
$ A=`B
> C
> '
$ echo "$A"
B
C 
```

在上例中，由于`enter`被置于 hard quote 当中，因此不再作为`CR`字符来处理。 这里的`enter`单纯只是一个断行符号(new-line)而已， 由于`command line`并没得到`CR`字符， 因此进入第二个`shell prompt`(`PS2`, 以>符号表示)， `command line`并不会结束，直到第三行， 我们输入的`enter`并不在 hard quote 里面， 因此没有被关闭， 此时，`command line`碰到`CR`字符，于是结束，交给 shell 来处理。

上例的`Enter`要是被置于 soft quote 中的话，`CR`字符也会同样被关闭：

```
$ A="B
> C
> "
$ echo $A
B C 
```

然而，由于 `echo $A`时的变量没有置于 soft quote 中， 因此，当变量替换完成后，并作命令行重组时，`enter`被解释为`IFS`， 而不是 new-line 字符。

同样的，用 escape 亦可关闭 CR 字符：

```
$ A=B\
> C\
>
$ echo $A
BC 
```

上例中的，第一个`enter`跟第二个`enter`均被 escape 字符关闭了， 因此也不作为`CR`来处理，但第三个`enter`由于没有被 escape， 因此，作为`CR`结束`command line`。 但由于`enter`键本身在 shell meta 中特殊性，在 \ escape 字符后面 仅仅取消其`CR`功能， 而不保留其 IFS 功能。

你或许发现光是一个`enter`键所产生的字符，就有可能是如下这些可能：

*   CR
*   IFS
*   NL(New Line)
*   FF(Form Feed)
*   NULL
*   ...

至于，什么时候解释为什么字符，这个我就没法去挖掘了， 或者留给读者君自行慢慢摸索了...^-^

至于 soft quote 跟 hard quote 的不同，主要是对于某些 meta 的关闭与否，以$来做说明：

```
$ A=B\ C
$ echo "$A"
B C
$ echo '$A'
$A 
```

在第一个`echo`命令行中，$被置于 soft quote 中，将不被关闭， 因此继续处理变量替换， 因此，`echo`将 A 的变量值输出到屏幕，也就是"B C"的结果。

在第二个`echo`命令行中，$被置于 hard quote 中，则被关闭， 因此，$只是一个$符号，并不会用来做变量替换处理， 因此结果是$符号后面接一个 A 字母：$A.

> **练习与思考:** 如下结果为何不同？
> 
> tips: 单引号和双引号，在 quoting 中均被关闭了。

```
$ A=B\ C
$ echo '"$A"'  #最外面的是单引号
"$A"
$ echo "'$A'"  #最外面的是双引号
'B C' 
```

* * *

在 CU 的 shell 版里，我发现很多初学者的问题， 都与 quoting 的理解有关。 比方说，若我们在 awk 或 sed 的命令参数中， 调用之前设定的一些变量时，常会问及为何不能的问题。

要解决这些问题，关键点就是：**区分出 shell meta 与 command meta**

前面我们提到的那些 meta，都是在 command line 中有特殊用途的， 比方说{}就是将一系列的 command line 置于不具名的函数中执行(可简单视为 command block)， 但是，awk 却需要用{}来区分出 awk 的命令区段(BEGIN,MAIN,END). 若你在 command line 中如此输入：

```
$ awk {print $0} 1.txt 
```

由于{}在 shell 中并没有关闭，那 shell 就将{print $0}视为 command block， 但同时没有`;`符号作命令分隔，因此，就出现 awk 语法错误结果。

要解决之，可用 hard quote:

```
awk '{print $0}' 
```

上面的 hard quote 应好理解，就是将原来的 {、<space class="calibre24">、$、}这几个 shell meta 关闭， 避免掉在 shell 中遭到处理，而完整的成为 awk 的参数中 command meta。</space>

> **Note:**
> 
> awk 中使用的$0 是 awk 中内建的 field nubmer，而非 awk 的变量， awk 自身的变量无需使用$.

要是理解了 hard quote 的功能，在来理解 soft quote 与 escape 就不难：

```
awk "{print \$0}" 1.txt
awk \{print \$0\} 1.txt 
```

然而，若要你改变 awk 的$0 的 0 值是从另一个 shell 变量中读进呢？ 比方说：已有变量$A 的值是 0， 那如何在`command line`中解决 awk 的$$A 呢？ 你可以很直接否定掉 hard quote 的方案：

```
$ awk '{print $$A}' 1.txt 
```

那是因为$A 的$在 hard quote 中是不能替换变量的。

聪明的读者(如你！)，经过本章的学习，我想，你应该可以理解为 为何我们可以使用如下操作了吧：

```
A=0
awk "{print \$$A}" 1.txt
awk  \{print\ \$$A\} 1.txt
awk '{print $'$A'}' 1.txt
awk '{print $'"$A"'}' 1.txt 
```

或许，你能给出更多方案... ^_^

更多练习：

*   [`bbs.chinaunix.net/forum/viewtopic.php?t=207178`](http://bbs.chinaunix.net/forum/viewtopic.php?t=207178) 一个关于 read 命令的小问题： 很早以前觉得很奇怪：执行 read 命令，然后读取用户输入给变量赋值， 但如果输入是以空格键开始的话，这空格会被忽略，比如：

    ```
    read a  #输入：    abc
    echo "$a" #只输出 abc 
    ```

    原因: 变量 a 的值，从终端输入的值是以 IFS 开头，而这些 IFS 将被 shell 解释器忽略(trim)。 应该与 shell 解释器分词的规则有关；

```
read a  #输入：\ \ \ abc
echo "$a" #只输出 abc 
```

需要将空格字符转义

> **Note:**
> 
> IFS Internal field separators, normally space, tab, and newline (see Blank Interpretation section). ...... Blank Interpretation After parameter and command substitution, the results of substitution
> are scanned for internal field separator characters (those found in IFS) and split into distinct arguments where such characters are found. Explicit null arguments ("" or '') are retained.
> Implicit null arguments(those resulting from parameters that have no values) are removed. (refre to: man sh)

解决思路：

1.  shell command line 主要是将整行 line 给分解(break down)为每一个单词(word);
2.  而词与词之间的分隔符就是 IFS (Internal Field Seperator)。
3.  shell 会对 command line 作处理(如替换，quoting 等), 然后再按词重组。(注：别忘了这个重组特性)
4.  当你用 IFS 来事开头一个变量值，那 shell 会先整理出这个词，然后在重组 command line。 5.然而，你将 IFS 换成其他，那 shell 将视你哪些 space/tab 为“词”，而不是 IFS。那在重组时，可以得到这些词。

若你还是不理解，那来验证一下下面这个例子：

```
$ A="  abc" 
$ echo $A
abc
$ echo "$A" #note1
   abc
$ old_IFS=$IFS
$ IFS=;
$ echo $A
   abc
$ IFS=$old_IFS
$ echo $A
abc 
```

> **Note:**
> 
> 1.  这里是用 soft quoting 将里面的 space 关闭，使之不是 meta(IFS)， 而是一个 literal(white space);
>     
>     
> 2.  IFS=; 意义是将 IFS 设置为空字符，因为;是 shell 的元字符(meta);

问题二：为什么多做了几个分号，我想知道为什么会出现空格呢？

```
$ a=";;;test"                              
$ IFS=";"                                  
$ echo $a                                  
   test                                                                         
$ a="   test"                              
$ echo $a                                  
   test                                                                         
$ IFS=" "                                  
$ echo $a                                  
test 
```

解答：

这个问题，出在`IFS=;`上。 因为这个`;`在问题一中的 command line 上是一个 meta, 并非`";"`符号本身。 因此，`IFS=;`是将 IFS 设置为 null charactor (不是 space、tab、newline)。

要不是试试下面这个代码片段：

```
$ old_IFS=$IFS
$ read A
;a;b;c
$ echo $A
;a;b;c
$ IFS=";"  #Note2
$ echo $A
a b c 
```

> **Note:**
> 
> 要关闭`;`可用`";"`或者`';'`或者`\;`。

*   [`bbs.chinaunix.net/forum/viewtopic.php?t=216729`](http://bbs.chinaunix.net/forum/viewtopic.php?t=216729)

思考问题二：文本处理：读文件时，如何保证原汁原味。

```
cat file | while read i
do
   echo $i
done 
```

文件 file 的行中包含若干空，经过 read 只保留不重复的空格。 如何才能所见即所得。

```
cat file | while read i
do
   echo "X${i}X"
done 
```

从上面的输出，可以看出 read，读入是按整行读入的; 不能原汁原味的原因：

1.  如果行的起始部分有 IFS 之类的字符，将被忽略;
2.  `echo $i`的解析过程中，首先将$i 替换为字符串， 然后对 echo 字符串中字符串分词，然后命令重组，输出结果; 在分词，与命令重组时，可能导致多个相邻的 IFS 转化为一个;

```
cat file | while read i
do
  echo "$i"
done 
```

以上代码可以解决原因 2 中的，command line 的分词和重组导致 meta 字符丢失； 但仍然解决不了原因 1 中，read 读取行时，忽略行起始的 IFS meta 字符。

回过头来看上面这个问题：为何要原汁原味呢？ cat 命令就是原汁原味的，只是 shell 的 read、echo 导致了某些 shell 的 meta 字符丢失;

如果只是 IFS meta 的丢失，可以采用如下方式： 将 IFS 设置为 null，即`IFS=;`, 在此再次重申此处`;`是 shell 的 meta 字符,而不是 literal 字符; 因此要使用 literal 的 `;`应该是`\;` 或者关闭 meta 的(soft/hard) quoting 的`";"`或者`';'`。

因此上述的解决方案是：

```
old_IFS=$IFS
IFS=; #将 IFS 设置为 null
cat file | while read i
do
  echo "$i"
done
IFS=old_IFS #恢复 IFS 的原始值 
```

现在，回过头来看这个问题，为什么会有这个问题呢； 其本源的问题应该是没有找到解决原始问题的最合适的方法， 而是采取了一个迂回的方式来解决了问题；

因此，我们应该回到问题的本源，重新审视一下，问题的本质。 如果要精准的获取文件的内容，应该使用 od 或者 hexdump 会更好些。