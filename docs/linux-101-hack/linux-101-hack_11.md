# 第十一章 - Bash 脚本

~~翻译的进度越来越快了, 我看今天就能把这本书搞完了~ 开心~~~

这一章介绍了几个关于 bash 脚本的知识和技巧.

# Hack-84 关于.bash_*的执行顺序

## 关于.bash_*的执行顺序

这一篇介绍了下面这些文件的执行顺序:

*   `/etc/profile`
*   `~/.bash_profile`
*   `~/.bashrc`
*   `~/.bash_login`
*   `~/.profile`
*   `~/.bash_logout`

#### 交互式 shell 的执行顺序

```
execute /etc/profile
IF ~/.bash_profile exists THEN
    execute ~/.bash_profile
ELSE
    IF ~/.bash_login exist THEN
        execute ~/.bash_login
    ELSE
        IF ~/.profile exist THEN
            execute ~/.profile
        END IF
    END IF
END IF 
```

登出时:

```
IF ~/.bash_logout exists THEN
    execute ~/.bash_logout
END IF 
```

需要注意的是`/etc/bashrc`是由`~/.bashrc`执行的:

```
# cat ~/.bashrc
if [ -f /etc/bashrc ]; then
    . /etc/bashrc
fi 
```

#### non-login shell 的顺序

科普 nologin shell

> 当我们需要一个不允许登陆但是允许使用系统资源的用户时，就用到了 nologin shell（`/usr/sbin/nologin`）, 比如在 `/etc/passwd`中的各种系统账户，当登陆的 shell 为 nologin shell 时，会提示你此账户不允许登陆，而`/bin/false`则是连登陆都不让登陆，只会返回 False。 具体请看 :[whats-the-difference-between-sbin-nologin-and-bin-false](http://unix.stackexchange.com/questions/10852/whats-the-difference-between-sbin-nologin-and-bin-false)

```
IF ~/.bashrc exists THEN
    execute ~/.bashrc
END IF 
```

#### 测试.

作者在这里做了个小测试, 就是在不同的文件下写了一个`PS1`, 看看哪个被覆盖, 其实这样做很啰嗦的, 不如在每个文件下输出一点特殊的东西, 输出什么呢? 就输出自己的文件名, 在`~/.bashrc`里面就加一句"`echo .bashrc >> ~/order`", 在`~/.bash_login`里面就加一句"`echo bash_login >> ~/order`", 以此类推, 最后只要看看`~/order`里面是什么就好了.

好麻烦的... 你感兴趣的话自己做吧, 我就不演示了哈~

# Hack-85 For 循环

## For 循环

一般的 for 循环都是这种形式的:

```
for i in {1..9}; do
    echo $i
done 
```

但是还有一种形式, 也许你不知道:

```
for (( expr1; expr2; expr3 ))
do
    commands
done 
```

这就是类似 C 语言的格式.

上面输出 1-9 的例子就可以写成:

```
for (( i=1; 1 < 10; i++ ))
do
    echo $i
done 
```

甚至允许空条件:

```
#!/bin/bash
i=1
for (( ; ; ))
do
    sleep $i
    echo "Number: $((i++))"
done 
```

```
$ ./for11.sh
Number: 1
Number: 2
Number: 3 
```

多重操作:

```
#!/bin/bash
for ((i=1, j=10; i <= 5 ; i++, j=j+5))
do
echo "Number $i: $j"
done 
```

```
$ ./for12.sh
Number 1: 10
Number 2: 15
Number 3: 20
Number 4: 25
Number 5: 30 
```

# Hack-86 Shell 调试

## Shell 调试

没错, shell 也可以调试.

看这样一个脚本:

```
$ cat filesize.sh
#!/bin/bash
for filesize in $(ls -l . | grep "^-" | awk '{print $5}')
do
let totalsize=$totalsize+$filesize
done
echo "Total file size in current directory: $totalsize" 
```

运行结果如下:

```
$ ./filesize.sh
Total file size in current directory: 652 
```

添加调试信息:

```
$ cat filesize.sh
#!/bin/bash
set -xv   ## 注意这里!
for filesize in $(ls -l . | grep "^-" | awk '{print $5}')
do
let totalsize=$totalsize+$filesize
done
echo "Total file size in current directory: $totalsize" 
```

输出结果如下:

```
$ ./fs.sh
++ ls -l .
++ grep '^-'
++ awk '{print $5}'
+ for filesize in '$(ls -l . | grep "^-" | awk '\''{print
$5}'\'')'
+ let totalsize=+178
+ for filesize in '$(ls -l . | grep "^-" | awk '\''{print
$5}'\'')'
+ let totalsize=178+285
+ for filesize in '$(ls -l . | grep "^-" | awk '\''{print
$5}'\'')'
+ let totalsize=463+189
+ echo 'Total file size in current directory: 652'
Total file size in current directory: 652 
```

每次执行一条命令都会输出对应的命令和结果.

除了上面的方法, 还可以这样调试:

```
$ bash -xv filesize.sh 
```

直接在运行的时候调试.

很有用的我会乱说~

# Hack-87 引号

## 引号

引号的作用是什么呢?

看这样一个例子:

```
➤ echo hello world!
hello world!
➤ echo "hello world!"
hello world!
➤ 
```

看起来没什么区别是吧?

那这样呢?

```
➤ echo hello ; world!
hello
world!: command not found
➤ echo "hello ; world!"
hello ; world!
➤ 
```

中间加了一个特殊字符`;`就报错了, 但是在添加了引号之后又成功执行了, 为什么?

引号的作用之一是确定参数.

再上面的例子中, 我们用分号隔开了`hello world!`, 导致`echo`只知道 hello 是他的参数, 而不管 world! 了. 但是我们用引号引起来之后就取消了分号的效果. 把 `hello ; world!` 作为一个整体的参数传递给`echo`.

#### 单引号和双引号

跟大多数编程语言一样, 单引号里面的变量不予解析扩展, 双引号扩展变量:

```
➤ i=888
➤ echo $i
888
➤ echo "$i"
888
➤ echo '$i'
$i
➤ 
```

看出区别了么? 单引号里面是什么, 输出就是什么.而双引号则把变量的值扩展了.

(也许是这些我都知道了, 所以我觉着这里讲的内容都比较肤浅... 各位看官自便~)

# Hack-88 在 Shell 脚本中读文件内容

## 在 Shell 脚本中读文件内容

这个名字好长... 其实也没什么东西, 无非是重定向和 read 的组合拳. 不过嘛, 这一拳还是很给力的 :)

假设有这样一个文件:

```
$ cat employees.txt
Emma Thomas:100:Marketing
Alex Jason:200:Sales
Madison Randy:300:Product Development
Sanjay Gupta:400:Support
Nisha Singh:500:Sales 
```

还记得这个文件吗 ~

然后我们写一个脚本:

```
$ vi read-employees.sh
#!/bin/bash
IFS=:
echo "Employee Names:"
echo "---------------"
while read name empid dept
do
echo "$name is part of $dept department"
done < ~/employees.txt 
```

然后我们执行一下:

```
$ chmod u+x read-employees.sh

$ ./read-employees.sh
Employee Names:
---------------
Emma Thomas is part of Marketing department
Alex Jason is part of Sales department
Madison Randy is part of Product Development department
Sanjay Gupta is part of Support department
Nisha Singh is part of Sales department 
```

好玩吗?

解释一下:

`IFS`表示的是分隔符, 因为我们给的文件里面就是用这个冒号来分隔的. 然后重定向将 txt 文件里面的内容都给了 read, 每次读一行, 一行读三个. 然后 echo 再把 read 读到的内容输出出来.

事情就是这么个事情,情况就是这么个情况.