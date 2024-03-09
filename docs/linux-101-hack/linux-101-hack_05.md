# 第五章 - PS* 介绍

这一章介绍了`PS1`, `PS2`, `PS3`, `PS4`, 以及`PROMPT_COMMAND`.

所谓的这些`PS`,实际上就是我们看到的提示符, 比如在终端上你看到的`name@host~ $`.

各位看官且跟我走~

# Hack-38 PS1

## PS1

`PS1`就是你每次打开终端, 首先显示的提示符, 比如 `kity@cat ~$`.

在你的`~/.bashrc`中已经定义了默认的`PS1`:

```
➤ cat .bashrc | grep 'PS1'
#    PS1='${debian_chroot:+($debian_chroot)}\[\033[01;32m\]\u@\h\[\033[00m\]:\[\033[01;34m\]\w\[\033[00m\]\$ '
#    PS1='${debian_chroot:+($debian_chroot)}\u@\h:\w\$ '
    PS1="\[\e]0;${debian_chroot:+($debian_chroot)}\u@\h: \w\a\]$PS1"
➤ 
```

那你就会说, 哎, 为啥你的终端和我的不一样啊, 你的怎么是个箭头呢?

那是因为我修改过呀:

```
if [ `whoami` == root ]; then
    PS1='\[\033[01;32m\]\u\[\033[00m\]:\[\033[01;34m\]\[\033[10;31m\]# \[\033[00;31m\]\[\033[00m\]'
else
    PS1='\[\033[00;31m\]➤ \[\033[00;31m\]\[\033[00m\]'
fi 
```

是不是很 low...

言归正传, 解释一下上面的每个参数都代表什么:

*   `\u` 代表用户名, 取决于`whoami`
*   `\h` 主机名,取决于`hostname`
*   `\w` 当前目录的绝对地址, 取决于`pwd`

还有好多作者没有介绍的, 我在此把他写的另一篇文章放上来:

[看这里看这里!](http://www.thegeekstuff.com/2008/09/bash-shell-ps1-10-examples-to-make-your-linux-prompt-like-angelina-jolie/)

算是个扩展阅读吧!

# Hack-39 PS2

## PS2

`PS2`是当你敲命令换行的时候所提示你输入的字符, 比如:

```
➤ echo "hi
> hello world!
> "
hi
hello world!

➤ 
```

在这里, '`>`'就是`PS2`, 如果更改`PS2`的值之后:

```
➤ PS2='#'
➤ echo "hi
#hello world!
#"
hi
hello world!

➤ 
```

就会这样了~

你可以按照你的喜好改成别的字符或是字符串.

# Hack-40 PS3

## PS3

`PS3`这个变量只存在于一个地方, 即`select`选择的时候, 提示输入选择的内容:

```
ramesh@dev-db ~> cat ps3.sh
select i in mon tue wed exit
do
    case $i in
        mon) echo "Monday";;
        tue) echo "Tuesday";;
        wed) echo "Wednesday";;
        exit) exit;;
    esac
done
ramesh@dev-db ~> ./ps3.sh
1) mon
2) tue
3) wed
4) exit
#? 1
Monday
#? 4 
```

默认的提示符是`#?`.

然后我们更改一下:

```
ramesh@dev-db ~> cat ps3.sh
PS3="Select a day (1-4): "
select i in mon tue wed exit
do
    case $i in
        mon) echo "Monday";;
        tue) echo "Tuesday";;
        wed) echo "Wednesday";;
        exit) exit;;
    esac
done
ramesh@dev-db ~> ./ps3.sh
1) mon
2) tue
3) wed
4) exit
Select a day (1-4): 1
Monday
Select a day (1-4): 4 
```

如果不在文件中更改的话, 也可以`export`一下(还是用第一个未更改过的脚本):

```
➤ cat ps3.sh 
select i in mon tue wed exit
do
    case $i in
        mon) echo "Monday";;
        tue) echo "Tuesday";;
        wed) echo "Wednesday";;
        exit) exit;;
    esac
done
➤ export PS3='yoooo--> '
➤ bash ps3.sh 
1) mon
2) tue
3) wed
4) exit
yoooo--> 2
Tuesday
yoooo--> 4
➤ 
```

# Hack-41 PS4

## PS4

这可不是游戏机哦~

`PS4`这个变量存在于调试过程中, 也就是开起了`set -x`之后:

```
➤ cat ps4.sh
set -x
echo "PS4 demo script"
ls -l /etc/ | wc -l
du -sh .
➤ 
➤ bash ps4.sh
+ echo 'PS4 demo script'
PS4 demo script
+ ls -l /etc/
+ wc -l
285
+ du -sh .
4.9M    . 
```

默认的`PS4`是一个加号.

下面更改一下

```
➤ export PS4='$0.$LINENO+ '
➤ bash ps4.sh
ps4.sh.3+ echo 'PS4 demo script'
PS4 demo script
ps4.sh.4+ ls -l /etc/
ps4.sh.4+ wc -l
285
ps4.sh.5+ du -sh .
4.9M    .
➤ 
```

其中`$0`是脚本名字, `$LINENO`是命令所在的行号.

# Hack-42 PROMPT_COMMAND

## PROMPT_COMMAND

`PROMPT_COMMAND`指的是当命令运行结束后所输出的字符.

比如:

```
➤ echo $PROMPT_COMMAND
echo -ne "\033]0;${USER}@${HOSTNAME}: ${PWD/$HOME/~}\007"
➤ echo -ne "\033]0;${USER}@${HOSTNAME}: ${PWD/$HOME/~}\007"
➤ 
➤ 
```

这个是啥也没有的输出...

咱改一改:

```
➤ PROMPT_COMMAND='echo "Hello world!"'
Hello world!
➤ whoami
mr
Hello world!
➤ pwd
/home/mr/test
Hello world!
➤ date
2016 年 01 月 04 日 星期一 22:17:24 CST
Hello world!
➤ 
```

看到了? 把`PROMPT_COMMAND`改成"Hello world!"之后, 每次命令结束都会再输出一个"Hello world!", 我们可以在这里做一点小动作:

作者把它改成了时间:

`export PROMPT_COMMAND="date +%H:%M:%S"`

```
➤ pwd
/home/mr/test
22:21:34
➤ whoami
mr
22:21:36
➤ 
```

我觉着没卵用, 倒不如这样好玩:

先自定义一个函数:

```
function ttt() { [[ $? -eq 0 ]] && echo -n yes || echo -n no; } 
```

然后:

```
export PROMPT_COMMAND="ttt" 
```

这样每次命令完成都有反馈啦~

(虽然也没什么卵用...

# Hack-43 自定义 PS1

## 自定义 PS1

#### 1.在提示符里输出用户名,主机名, 当前目录:

```
export PS1="\u@\h \W> " 
```

其中:

*   `\W` 是当前目录的 basename, 也就是目录名, 不带绝对路径的.

其他的在之前已经说过, 不再重复.

#### 2.在提示符里输出当前时间:

```
export PS1="\u@\h [\$(date +%H:%M:%S)]> " 
```

`PS1`中可以带命令, 正如上面的例子, 输出时便会附带当前时间.

上面的`$(date +%H:%M:%S)`可以替换为: `\t`

或者用`\@`输出当前的小时和分钟.

#### 3.显示任何命令:

其实,这个说法并不是很准确, 因为自定义的命令不会运行, 除非像上一篇那样, 在外部定义了一个自己的命令, 否则, 只会输出第一次命令运行得到的结果, 听起来可能有点啰嗦, 你自己动手试一下就好了.

这里再多说几个:

......

妈蛋, 原作者说的都是些啥啊, 越来越水了... 没用的就不翻译了.

说点自己的经验:

1.  这些变量是类似于一个子 shell 运行的, 外部命令不会对内部产生影响
2.  变量可以是一条命令, 但是这条命令必须是系统自带的, 自己写的函数不会起作用.
3.  自己在外部写的函数会在里面被引用, 不知道是替换还是什么, 总之能够运行.
4.  不动手试一下你就不知道我说的是什么...

#### 4.用内部已有的代码自定义 PS1

如果你看过之前的那篇文章(Hack-38 的扩展阅读部分), 这里的东西就当是复习了.

先列举一下那些内部代码, 类似于`\n`代表换行符一样:

*   `\a` 响铃
*   `\d` 日期
*   `\D{format}` 自定义的日期
*   `\e` 逃逸字符
*   `\h` 主机名(前半部分)
*   `\H` 主机名(完整的)
*   `\j` 当前 shell 下的后台 job 数量, 相当于`jobs`
*   `\l` shell 终端的 basename... (这个都给定义了...
*   `|n` 换行
*   `\r` 你知道`\r`和`\n`的区别嘛 (这个是回车, 上面的是换行哦~)
*   `\s` shell 的名字
*   `\t` 24 小时制的时间 - HH:MM:SS
*   `\T` 12 小时制的时间 - HH:MM:SS
*   `\@` 12 小时制带上下午的时间 - am/pm (真啰嗦啊...
*   `\A` 24 小时制的时间 - HH:MM
*   `\u` 当前用户名
*   `\v` 当前 Bash 的版本号 (我真是醉了...
*   `\V` Bash 的发布版本号 4.3.42 (可以理解为较长的那个
*   `\w` 当前目录(绝对路径)
*   `\W` 当前目录的短名字 (可以理解为目录名
*   `\!` 这条命令在历史记录中的编号
*   `\#` 这条命令在当前 shell 中的编号
*   `\$` 如果`$UID -eq 0`那么这个就输出`#`, 否则输出`$`
*   `\nnn` nnn 表示一个八进制的数字, 整体就表示这个八进制的字符
*   `\\` 一个反斜杠
*   `\[` 转义开中括号
*   `\]` 转义闭中括号

#### 5.在`PS1`中运行自定义 function

哈哈, 我翻译`PROMPT_COMMAND`那一部分的时候还没看到这里呢, 所以不算剧透哦, 因为我也不知道作者写了这一部分, 而且上面的在`PS*`变量中自定义功能可是我举一反三得来的哦~

所以, 这里作者说的是定义了一个外部`function`, 然后在从`PS1`里面调用.

这样你的选择就多了去了, 随你想干什么, bash 都满足你哦~ 哈哈~

#### 6.在`PS1`中运行脚本

在`PS1`变量中既然可以运行命令, 那么同样也可以运行脚本.

假如你在`~/bin/totalfilesize.sh`中存放着一个内容如下的脚本:

```
#!/bin/bash
for filesize in $(ls -l . | grep "^-" | awk '{print $5}')
do
let totalsize=$totalsize+$filesize
done
echo -n "$totalsize" 
```

正如你所看到的, 这个脚本的作用是计算当前目录下文件的大小.

然后我们将`PS1`的值改掉:`export PS1="\u@\h [\$(totalfilesize.sh) bytes]> "`

那么每当你敲回车的时候都会看到当前目录下的文件总大小:

```
ramesh@dev-db [534 bytes]> cd /etc/mail
ramesh@dev-db [167997 bytes]> 
```

PS:可以把脚本内容改成:

```
ls -l | awk '/^-/ { sum+=$5 } END { printf sum }' 
```

这样会简练一些.

# Hack-44 给点颜色给 PS1

## 给点颜色给 PS1

这里介绍了一堆关于彩色显示的代码...

#### 1.字体颜色

用下面的代码可以让`PS1`变成蓝色:

```
export PS1="\e0;34m\u@\h \w> \e[m " 
```

知道这是为什么嘛?

因为我们在中间嵌入了颜色代码:`\e[0;34m` 以及关闭颜色的代码:`\e[m`

下面的命令会让颜色变得更亮, 还带有加粗效果:

```
export PS1="\e[1;34m\u@\h \w> \e[m " 
```

慢慢介绍里面的东西都是什么意思:

*   `\e[` 颜色开启
*   `x;ym` 颜色的类型: x,y 自己定义
*   `\e[m` 关闭颜色

下面是一张颜色代码表:

| 颜色 | 代码 |
| --- | --- |
| 黑色 | 0;30 |
| 蓝色 | 0;34 |
| 绿色 | 0;32 |
| 青色 | 0;36 |
| 红色 | 0;31 |
| 紫色 | 0;35 |
| 棕色 | 0;33 |

如果把上面代码中的`0`替换成`1`就会使颜色加深, 字体加粗.

#### 2.字体背景色

我们不仅可以更改显示的字体的颜色, 我们还可以改变字体的背景颜色:

```
export PS1="\e[47m\u@\h \w> \e[m " 
```

上面的命令使我们得到了一个浅灰色的背景.

#### 3.组合

没错, 我们当然可以把上两种结果组合起来, 实现任何你想要的结果:

```
export PS1="\e[0;34m\e[47m\u@\h \w> \e[m "
` 
```

比如利用上面的代码就可以得到一个字体颜色为蓝色, 字体背景色为浅灰的效果.

下面是我自己截的图, 不要看花眼...

![promopt_color

每一行命令都对应它下一行开头的颜色.

这里还是希望你亲自实验一下, 而不是看一遍...

#### 4.`tput` 命令

说实话, 我对这里也是第一次见, 长知识了.

例子:

```
$ export PS1="\[$(tput bold)$(tput setb 4)$(tput setaf
7)\]\u@\h:\w $ \[$(tput sgr0)\]" 
```

其中:

*   `tput setb [1-7]` - 设置背景色
*   `tput setf [1-7]` - 设置前景色
*   `tput setab [1-7]` - 用 ANSI escape 设置背景色(我查了,但不懂...)
*   `tput setaf [1-7]` - 用 ANSI escape 设置前景色

前两个好使, 就算用`echo`输出也是很漂亮的.

还有一些设置字体的选项:

*   `tput bold` 粗体
*   `tput dim` 亮度减半
*   `tput smul` 开启下划线
*   `tput rmul` 关闭下划线
*   `tput rev` 颜色反转, 类似于高对比度那种
*   `tput smso` 开启高对比度模式
*   `tput rmso` 关闭高对比度模式
*   `tput sgr0` 关闭所有特效

颜色代码:

*   `0` – 黑色
*   `1` – 红色
*   `2` – 绿色
*   `3` – 黄色
*   `4` – 蓝色
*   `5` – 洋红
*   `6` – 青色
*   `7` - 白色

#### 扩展阅读

[9 UNIX / Linux tput Examples: Control Your Terminal Color and Cursor](http://www.thegeekstuff.com/2011/01/tput-command-examples/)