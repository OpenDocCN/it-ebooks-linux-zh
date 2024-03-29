# 6.exec 跟 source 差在哪 _

## shell 十三问之 6：exec 跟 source 差在哪？

* * *

这次让我们从 CU shell 版的一个实例帖子来谈起吧： (论坛改版后，原链接已经失效)

例中的提问原文如下：

> **帖子提问:**
> 
> cd /etc/aa/bb/cc 可以执行 但是把这条命令放入 shell 脚本后，shell 脚本不执行！ 这是什么原因？

意思是：运行 shell 脚本，并没有移动到/etc/aa/bb/cc 目录。

我当时如何回答暂时别去深究，先让我们了解一下进程 (process)的概念好了。

首先，我们所执行的任何程序，都是父进程(parent process)产生的一个 子进程(child process),子进程在结束后，将返回到父进程去。 此现象在 Linux 中被称为`fork`。

(为何要称为 fork 呢？ 嗯，画一下图或许比较好理解...^_^)

当子进程被产生的时候，将会从父进程那里获得一定的资源分配、及 (更重要的是)继承父进程的环境。

让我们回到上一章所谈到的"环境变量"吧： **所谓环境变量其实就是那些会传给子进程的变量**。 简单而言, "遗传性"就是区分本地变量与环境变量的决定性指标。 然而，从遗传的角度来看，我们不难发现环境变量的另一个重要特征： **环境变量只能从父进程到子进程单向传递。 换句话说：在子进程中环境如何变更，均不会影响父进程的环境。**

接下来，在让我们了解一下 shell 脚本(shell script)的概念. 所谓 shell script 讲起来很简单，就是将你平时在 shell prompt 输入的多行 `command line`, 依序输入到一个文件文件而已。

再结合以上两个概念(process + script)，那应该不难理解如下的这句话的意思了： 正常来说，当我们执行一个 shell script 时，其实是先产生一个 sub-shell 的子进程， 然后 sub-shell 再去产生命令行的子进程。 然则，那让我们回到本章开始时，所提到的例子在重新思考：

> **帖子提问:**
> 
> cd /etc/aa/bb/cc 可以执行 但是把这条命令放入 shell 脚本后，shell 脚本不执行！ 这是什么原因？

意思是：运行 shell 脚本，并没有移动到/etc/aa/bb/cc 目录。

我当时的答案是这样的：

> 因为，我们一般跑的 shell script 是用 sub-shell 去执行的。 从 process 的概念来看，是 parent process 产生一个 child process 去执行， 当 child 结束后，返回 parent, 但 parent 的环境是不会因 child 的改变而改变的。 所谓的环境变量元数很多，如 effective id(euid)，variable, working dir 等等... 其中的 working dir($PWD) 正是楼主的疑问所在： 当用 sub-shell 来跑 script 的话，sub-shell 的$pwd 会因为 cd 而变更， 但返回 primary shell 时，$PWD 是不会变更的。

能够了解问题的原因及其原理是很好的，但是？ 如何解决问题，恐怕是我们更应该感兴趣的是吧？

那好，接下来，再让我们了解一下`source`命令好了。 当你有了`fork`的概念之后，要理解`soruce`就不难：

所谓`source`，就是让 script 在当前 shell 内执行、 而不是产生一个 sub-shell 来执行。 由于所有执行结果均在当前 shell 内执行、而不是产生一个 sub-shell 来执行。

因此, 只要我们原本单独输入的 script 命令行，变成`source`命令的参数， 就可轻而易举地解决前面提到的问题了。

比方说，原本我们是如此执行 script 的：

```
$ ./my_script.sh 
```

现在改成这样既可：

```
$ source ./my_script.sh 
```

或者：

```
$ . ./my_script.sh 
```

说到这里，我想，各位有兴趣看看`/etc`底下的众多设定的文件， 应该不难理解它们被定义后，如何让其他 script 读取并继承了吧？

若然，日后，你有机会写自己的 script， 应也不难专门指定一个设定的文件以供不同的 script 一起"共用"了... ^_^

okay,到这里，若你搞懂`fork`与`source`的不同， 那接下来再接受一个挑战：

> 那`exec`又与`source`/`fork`有何不同呢？

哦...要了解`exec`或许较为复杂，尤其是扯上`File Decscriptor`的话... 不过，简单来说：

> `exec` 也是让 script 在同一个进程上执行，但是原有进程则被结束了。 简言之，原有进程能否终止，就是`exec`与`source`/`fork`的最大差异了。

嗯，光是从理论去理解，或许没那么好消化， 不如动手"实践+思考"来得印象深刻哦。

下面让我们为两个简单的 script，分别命名为 1.sh 以及 2.sh

1.sh

```
#!/bin/bash 

A=B 
echo "PID for 1.sh before exec/source/fork:$$"

export A
echo "1.sh: \$A is $A"

case $1 in
        exec)
                echo "using exec..."
                exec ./2.sh ;;
        source)
                echo "using source..."
                . ./2.sh ;;
        *)
                echo "using fork by default..."
                ./2.sh ;;
esac

echo "PID for 1.sh after exec/source/fork:$$"
echo "1.sh: \$A is $A" 
```

2.sh

```
#!/bin/bash

echo "PID for 2.sh: $$"
echo "2.sh get \$A=$A from 1.sh"

A=C
export A
echo "2.sh: \$A is $A" 
```

然后分别跑如下参数来观察结果：

```
$ ./1.sh fork
$ ./1.sh source
$ ./1.sh exec 
```

好了，别忘了仔细比较输出结果的不同及背后的原因哦... 若有疑问，欢迎提出来一起讨论讨论~~~~

happy scripting！ ^_^