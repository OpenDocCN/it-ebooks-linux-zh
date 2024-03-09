# 9.$@与$_ 差在哪

## shell 十三问之 9：$@与$*差在哪？

* * *

要说$@与$*之前， 需得先从 shell script 的 positional parameter 谈起...

我们都已经知道变量(variable)是如何定义和替换的， 这个不再多讲了。

#### 1\. shell script 的 positional parameter

* * *

但是，我们还需要知道有些变量是 shell 内定的， 且其名称是我们不能随意修改的。 其中，就有 positional parameter 在内。

在 shell script 中，我们可用$0, $1, $2, $3 ... 这样的变量分别提取命令行中的如下部分:

```
script_name parameter1 parameter2 parameter3 ... 
```

我们很容易就能猜出, `$0`就是代表 shell script 名称(路径)本身， 而`$1`就是其后的第一个参数，如此类推...

须得留意的是`IFS`的作用, 也就是`IFS`被 quoting 处理后， 那么 positional parameter 也会改变。

如下例：

```
my.sh p1 "p2 p3" p4 
```

由于 p2 与 p3 之间的空白键被 soft quoting 所关闭了， 因此，my.sh 的中$2 是"p2 p3",而$3 则是 p4...

还记得前两章，我们提到 function 时， 我们不是说过，它是 script 中的 script 吗？^_^

是的，function 一样可以读取自己的(有别于 script 的) positional parameter, 唯一例外的是$0 而已。

举例而言： 假设 my.sh 里有一个函数(function)叫 my_fun, 若在 script 中跑`my_fun fp1 fp2 fp3`, 那么，function 内的$0 就是 my.sh，而$1 是 fp1 而不是 p1 了...

不如写个简单的 my.sh script 看看吧：

```
#!/bin/bash

my_fun() {
    echo '$0 inside function is '$0
    echo '$1 inside function is '$1
    echo '$2 inside function is '$2
}

echo '$0 outside function is '$0
echo '$1 outside function is '$1
echo '$2 outside function is '$2

my_fun fp1 "fp2 fp3" 
```

然后在 command line 中跑一下 script 就知道了：

```
chmod 755 my.sh

./my.sh p1 "p2 p3"
$0 outside function is ./my.sh
$1 outside function is p1
$2 outside function is p2 p3
$0 inside function is ./my.sh
$1 inside function is fp1
$2 inside function is fp2 fp3 
```

然而，在使用 positional parameter 的时候， 我们要注意一些陷阱哦：

**$10 不是替换第 10 个参数， 而是替换第一个参数，然后在补一个 0 于其后;**

也就是说， `my.sh one two three four five six seven eight nine ten` 这样的 command line, my.sh 里的$10 不是 ten 而是 one0 哦...小心小心 要抓到 ten 的话，有两种方法：

*   方法一：使用我们上一章介绍的${}, 也就是用${10}即可。

*   方法二：就是 shift 了。

用通俗的说法来说， **所谓的 shift 就是取消 positional parameter 中最左边的参数($0 不受影响)**。 其预设值为 1，也就是 shift 或 shift 1 都是取消$1, 而原本的$2 则变成$1, $3 则变成$2... 那亲爱的读者，你说要 shift 掉多少个参数， 才可用$1 取得到${10} 呢？ ^_^

okay，当我们对 positional parameter 有了基本的概念之后， 那再让我们看看其他相关变量吧。

#### 2\. shell script 的 positional parameter 的 number

* * *

先是$#, 它可抓出 positional parameter 的数量。 以前面的`my.sh p1 "p2 p3"`为例： 由于"p2 p3"之间的`IFS`是在 soft quote 中， 因此，$#就可得到的值是 2. 但如果 p2 与 p3 没有置于 quoting 中话， 那$#就可得到 3 的值了。 同样的规则，在 function 中也是一样。

因此，我们常在 shell script 里用如下方法， 测试 script 是否有读进参数：

```
[ $# = 0 ] 
```

假如为 0, 那就表示 script 没有参数，否则就是带有参数...

#### 3\. shell script 中的$@与$*

* * *

接下来就是**$@与$*: 精确来讲，两者只有在 soft quote 中才有差异， 否则，都表示“全部参数” ($0 除外)**。

若在 comamnd line 上， 跑`my.sh p1 "p2 p3" p4`的话， 不管$@还是$*, 都可得到 p1 p2 p3 p4 就是了。

但是，**如果置于 soft quote 中的话：**

*   **"$@"则可得到 "p1" "p2 p3" "p4" 这三个不同字段(word);**
*   **"$*"则可得到 "p1 p2 p3 p4" 这一整个单一的字段。**

我们修改一下前面的 my.sh，使之内容如下：

```
#!/bin/bash

my_fun() {
    echo "$#"
}

echo 'the number of parameter in "$@" is ' $(my_fun "$@")
echo 'the number of parameter in "$*" is ' $(my_fun "$*") 
```

然后再执行:

```
./my.sh p1 "p2 p3" p4 
```

就知道，$@与$*差在哪了... ^_^