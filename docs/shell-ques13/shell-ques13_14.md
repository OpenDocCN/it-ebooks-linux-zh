# 14.wildcard

## shell 十三问之 14: ^([](#fn_))跟[! ]差在哪？ (wildcard)

* * *

这个题目说穿了， 就是要探讨 Wildcard 与 Regular Expression 的差别的。 这也是很多初学 shell 的朋友很容易混淆的地方。

首先，让我们回到十三问之第 2 问， 再一次将我们提到的 command line format 温习一次：

```
command_name options arguments 
```

同时，也再来理解一下，我在第五章所提到的变量替换的特性：

```
先替换，再重组 command line! 
```

有了这个两个基础后，再让我们来看 Wildcard 是什么回事吧。

### Part-I Wildcard （通配符）

* * *

首先，

```
`Wildcard` 也是属于 `command line` 的处理工序，作用于 `arguments` 里的 `path` 之上。 
```

没错，它不用在`command_name`，也不用在`options`上。 而且，若 argument 不是 path 的话，那也与 wildcard 无关。

换句更为精确的定义来讲，

```
 `wildcard`是一种命令行的路径扩展(path expansion)功能。 
```

提到这个扩展，那就不要忘了 command line 的“重组”特性了！

是的，这与`变量替换`(variable subtitution)及 `命令替换`(command substitution)的重组特性是一样的。

也就是在`wildcard`进行扩展后， 命令行会先完成重组，才会交给 shell 来处理。

了解了`wildcard`的扩展与重组特性后， 接下来，让我们了解一些常见的 wildcard 吧。

| wildcard | 功能 |
| --- | --- |
| * | 匹配 0 个或多个字符 |
| ? | 匹配任意单一字符 |
| [list] | 匹配 list 中任意单一字符 |
| [!list] | 匹配不在 list 中任意单一字符 |
| {string1,string2,...} | 匹配 string1 或者 stsring2 或者(...)中其一字符串 |

Note: list 中可以指定单个字符，如 abcd, 也可以指定 ASCII 字符的起止范围，如 a-d。 即[abcd] 与 [a-d] 是等价的，称为一个自定义的字符类。

例如：

```
a*b     # a 与 b 之间可以有任意个字符（0 个或多个），如 aabcb, axyzb, a012b,ab 等。
a?b     # a 与 b 之间只能有一个字符，但该字符可以任意字符，如 aab, abb, acb, azb 等。
a[xyz]b # a 与 b 之间只能有一个字符，但这个字符只能是 x 或者 y 或者 z，如：axb, ayb, azb 这三个。
a[!0-9]b# a 与 b 之间只能有一个字符，但这个字符不能是阿拉伯数字，如 aab，ayb，a-b 等。
a{abc,xyz,123}b # a 与 b 之间只能是 abc 或者 xyz 或者 123 这三个字串之一，扩展后是 aabcb，axyzb，a123b。 
```

1.  `[! ]` 中的`!` 只有放在第一位时，才有取反的功效。 eg: `[!a]*` 表示当前目录下不以 a 开头的路径名称； `/tmp/[a\!]*`表示/tmp 目录下所有以 a 或者 ! 开头的路径名称；

    思考：为何!前面要加\呢？提示是十三问之 4.

2.  `[ - ]`中`-`左右两边均有字符时，才表示一个范围，否则,仅作`-`(减号)字符来处理。 举例： `/tmp/*[-z]/[a-zA-Z]*` 表示/tmp 目录下所有以 z 或者-结尾的子目录中， 以英文字母(不分大小写)开头的目录名称。

3.  以*或?开头的 wildcard 不能匹配隐藏文件(即以.开头的文件名)。 eg: `*.txt`并不能匹配`.txt`但能匹配 1.txt 这样的路径名。 但 1*txt 及 1?txt 均可匹配 1.txt 这样的路径名。

基本上，要掌握 wildcard 并不难， 只要多加练习，再勤于思考，就能灵活运用了。

再次提醒：

```
别忘了 wildcard 的"扩展" + "重组" 这个重要特性，而且只作用在 argument 的 path 上。 
```

比方说， 假如当前目录下有： a.txt b.txt c.txt 1.txt 2.txt 3.txt 这几个文件。

当我们在命令行中执行`ls -l [0-9].txt`的命令行时， 因为 wildcard 处于 argument 的位置上，

于是根据匹配的路径，扩展为: 1.txt 2.txt 3.txt， 在重组出`ls -l 1.txt 2.txt 3.txt` 这样的命令行。

因此，你在命令行上敲 `ls -l [0-9].txt` 与 `ls -l 1.txt 2.txt 3.txt` 输出的结果是一样， 原因就是在于此。