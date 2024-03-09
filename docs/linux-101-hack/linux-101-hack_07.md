# 第七章 - 历史命令

这一部分介绍了三个关于`history`的技巧, 或是介绍.

用好了历史命令, 会让你的工作效率大大增加!

# Hack-52 History 命令

## History 命令

> 你敲的每一个命令都会被忠实地记录下来 --- 我.

上面说的也不完全对啦, 因为这个"忠实"会被各种方法破解...

首先你要有一个历史记录的概念, 然后你要知道这些记录存放到哪儿, 最后你要知道这些记录可以被删除, 修改.

#### 搜索历史命令

快捷键 `Ctrl + r`

非常建议你使用这个命令, 因为当你曾经输过一个很长的命令之后, 当你再次想输入这个命令的时候, 你就可以按下这个快捷键, 然后键入那条长命令的关键词, 然后就会显示出含有那个关键词的命令, 每次按下这个键都会再往上搜一个.

( 还有一些其他的快捷键, 在我的博客上: [HERE](http://wrfly.kfd.me/Bash%E5%BF%AB%E6%8D%B7%E9%94%AE%E7%9B%B8%E5%85%B3/) )

#### 重复上一次的命令

1.  向上的方向键
2.  两个叹号: `!!`
3.  还有这个: `!-1`
4.  快捷键`Ctrl+p`

最好用的还是方向键, 不是么~

#### 从历史记录中执行某个命令

还是沿袭上一个中的`!-n`模式, 其中`n`是一个编号.

```
# history | more
1 service network restart
2 exit
3 id
4 cat /etc/redhat-release
# !4
cat /etc/redhat-release 
```

作者给出的例子中, 执行了编号为`4`的命令.

#### 执行曾经的命令中特定开头的

假设你的部分历史命令如下:

```
 1719  find . -type f 
 1720  find . -type f | cpio -o > test.cpio
 1721  find .  | cpio -o > test.cpio
 1722  ls
 1723  l
 1724  du -h 
```

那么, 怎样重复执行 1721 条呢? 除了利用`!-1721`这么麻烦的方法, 我们还可以用`!f`这样的姿势.

因为开头的`f`是离着最后一条命令最近的, 所以`!f`就执行了它.

#### 清空历史记录

```
 history -c 
```

#### 从历史记录中截取参数

在下面的例子中, `!!:$` 等价于上一条命令的最后一个参数:

```
# ls anaconda-ks.cfg
anaconda-ks.cfg
# vi !!:$
vi anaconda-ks.cfg 
```

下面的例子中, `!^`则等价于上一条命令的第一个参数:

```
# cp anaconda-ks.cfg anaconda-ks.cfg.bak
anaconda-ks.cfg
# vi !^
vi anaconda-ks.cfg 
```

#### 从特定的历史记录中截取特定的参数

下面的例子中, `!cp:2` 等价于当前历史记录中, 最后一个以`cp`开头的命令的第二个参数; `!cp:$`则等价于当前历史记录中最后一个以`cp`开头的命令的最后一个参数.

是不是有点啰嗦...

```
# cp ~/longname.txt /really/a/very/long/path/long-
filename.txt
... ... ...
# ls -l !cp:2
ls -l /really/a/very/long/path/long-filename.txt
# ls -l !cp:$
ls -l /really/a/very/long/path/long-filename.txt 
```

# Hack-53 和历史命令相关的变量

## 和历史命令相关的变量

#### 在历史记录中显示时间

我们可以用`HISTTIMEFORMAT`这个变量来定义显示历史记录时的时间参数:

```
➤ export HISTTIMEFORMAT='%F %T '
➤ history 
... ... ...
 1740  2016-01-05 22:45:28 man history 
 1741  2016-01-05 22:45:33 export HISTTIMEFORMAT='%F %T'
 1742  2016-01-05 22:45:34 history 
 1743  2016-01-05 22:45:38 export HISTTIMEFORMAT='%F %T '
 1744  2016-01-05 22:45:40 history 
➤ 
```

你也可以用下面的别名来定义显示历史命令的数量:

```
alias h1='history 10'
alias h2='history 20'
alias h3='history 30' 
```

#### 改变历史记录的大小限制.

在`!/.bashrc`中有这两个变量控制者 bash 储存的历史命令的数量:

```
# vi ~/.bash_profile
HISTSIZE=450
HISTFILESIZE=450 
```

他们的含义就跟他们的名字一样.

#### 用`HISTFILE`改变存放历史命令的文件.

```
# vi ~/.bash_profile
HISTFILE=/root/.commandline_warrior 
```

( 都是些没有太多用处的东西, 尽管可定制化水平很高...

#### 用`HISTCONTROL`来删除重复的历史记录

下面的例子中, 有三个`pwd`命令, 那么在`history`中就会显示三次`pwd`, 有点不那么人性化.

```
# pwd
/
# pwd
/
# pwd
/
# history | tail -4
44 pwd
45 pwd
46 pwd
47 history | tail -4 
```

所以我们可以这样修改:

```
export HISTCONTROL=ignoredups 
```

然后就不会出现相邻的重复记录了~

#### 在历史命令中去重

如果你还嫌弃历史记录中的那些不相邻的重复记录, 你可以用这个:

```
export HISTCONTROL=erasedups 
```

#### 不记录某些命令

(这个一般是默认的)

```
export HISTCONTROL=ignorespace 
```

这样历史记录中就不会储存以空格开头的命令了.

#### 禁止记录历史命令

如果你不想记录任何历史命令的话, 可以这样做:

```
export HISTSIZE=0 
```

把历史命令记录的大小设置为**0**

( 还可以把历史记录的保存文件改成`/dev/null`, 殊途同归 ~)

#### 不记录某些命令 II

```
export HISTIGNORE="pwd:ls:ls –ltr:" 
```

这种方法的效果是, 历史记录不会记录这几个命令.

#### 扩展阅读

[15 个例子](http://www.thegeekstuff.com/2008/08/15-examples-to-master-linux-command-line-history/)

忽然想到, 这些扩展阅读也都是英文的啊.... 不知道各位看官有没有兴趣看呢?

# Hack-54 History 扩展

## History 扩展

用这个功能可以选择特定的历史记录, 不论是修改还是立即执行, 都可以完成.

这个扩展以`!`(叹号)开头.

*   `!!` 重复上一条命令
*   `!10` 重复历史记录中第 10 条命令
*   `!-2` 重复历史记录中倒数第二条命令
*   `!string` 重复历史记录中最后一条以`string`开头的命令
*   `!?string` 重复历史记录中最后一条包含`string`的命令
*   `^str1^str2^` 把上一条命令中的`str1`替换成`str2`, 然后再执行
*   `!!:$` 扩展成上一条命令的最后一个参数 ( 完全可以用`Alt+.`快捷键啊! )
*   `!string:n` 扩展成最后一条以`string`开头的命令的第`n`个参数....

#### `!?string`栗子

假设你曾经执行过这样一条命令:

```
$ /usr/local/apache2/bin/apachectl restart 
```

然后过了一会儿你想重复这个命令, 然后你这样做:

```
$ !apache
-bash: !apache: event not found 
```

哔~ 找不到!

当然, 因为曾经的那条命令并不是以`apache`开头的, 但是:

```
$ !?apache
/usr/local/apache2/bin/apachectl restart 
```

这样就会执行, 因为这条命令包含了`apache`这个字.

#### `^str1^str2^`栗子

作者给的例子太牵强, 我给个实用的:

```
➤ cat cal_random.sh 
#!/bin/bash
echo hi
test(){
    for i in {1..9};do
        echo $i;
    done
}
... ... ...
exit 0
➤ ^cat^less
less cal_random.sh 
➤ 
```

哈, 这个也不实用, 因为要达到这样的目的有好多方法, 而替换这一种实在太麻烦了.

#### `!!:$`栗子

废话不说, 上代码 ~!

```
➤ cp cal_random.sh cal_random.sh.bak
➤ head -n 2 !!:$
head -n 2 cal_random.sh.bak
#!/bin/bash
echo hi
➤ 
```

在这个例子中, `!!:$`被扩展成了上一条命令的最后一个参数, 可以理解为正则的`$`表示最后一样.

#### `!string:n` 栗子

曾经执行了这样一条命令

```
cat file1 file2 file3 
```

然后你想截取其中的第二个参数,

那么你就可以这样(确保最后一条以 cat 开头的命令是它):

```
ls !cat:2 
```

上面这些东西用好了真的很方便! 尽管我一直用快捷键...