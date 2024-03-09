# 第十三章 - 额外的技巧

# 让 cd 对大小写不敏感

## 让 cd 对大小写不敏感

默认的`cd`是对大小写敏感的:

```
➤ ls
hello  Hello  hi  Hi
➤ cd h
hello/ hi/    
➤ cd h

➤ cd H
Hello/ Hi/    
➤ cd H 
```

虽然这是 Linux 与 Windows 阵营的不同, 但有时候还是不要区分大小写的好, 毕竟我们有`tab`补全嘛.

所以:

```
bind "set completion-ignore-case on" 
```

这条命令的作用就是让补全不区分大小写

```
➤ bind "set completion-ignore-case on"
➤ cd h
hello/ Hello/ hi/    Hi/    
➤ cd h 
```

你可以把他写到`.bashrc`里面,以长久生效.

如果觉得还是区分大小写比较好呢, 那就再把他关闭:

```
bind "set completion-ignore-case off" 
```

或者从`.bashrc`里面删除上面那句就好了.

# SSH 长连接

## SSH 长连接

我们知道怎样可以在连接 ssh 远程服务器的时候不输入密码, 但是每次连接的时候都建立一个新的连接未免过于啰嗦, 尤其是经常时不时就登上服务器的时候, 每次连接都耗费了我们的耐心.

这个时候, 如果 SSH 像 TCP 长连接那样就好了, 一直建立着, 不会断开.

你别说, 还真有.

不过名字就不叫 SSH 长连接了, 英文名叫 "SSH’s ControlMaster".

也就是一个小管家, 管理着你的那些连接. 比如, 你登陆了服务器 A, 当你退出服务器 A 的时候, 小管家并不会断开连接, 尽管你看到确实是已经与服务器断开了. 当你再次登陆服务器 A 的时候, 你就回发现速度明显快了许多, 而且第一次登陆时欢迎的 banner 也不见了, 这是为什么? 就是因为小管家一直在连接着服务器, 在你看来, 你已经与服务器断开连接了, 但是服务器却认为你一直在线. 而且你用 `ss -ant`查看的时候也会发现连接是建立着的.

那怎么做呢?

在`~/.ssh/config`文件里, 添加如下几句话:

```
Host *
ControlMaster auto
ControlPath ~/.ssh/master-%r@%h:%p 
```

解释一下都是什么意思:

*   `Host *` 对于所有主机都让小管家托管
*   `ControlMaster auto` 开启小管家的自动控制
*   `ControlPath` 托管的凭据在哪儿(一切皆文件)

当然, 你也可以指定仅适用于某些主机:

```
HOST 192.168.0.*
HOST *.com 
```

如此之类的.

然后, 贴一下我的配置文件:

```
TCPKeepAlive=yes
ServerAliveInterval=15
ServerAliveCountMax=6
Compression=yes
ControlMaster auto
ControlPath /tmp/%r@%h:%p
ControlPersist yes 
```

刚才更新 github 的时候建立了一个连接:

```
➤ l git@github.com\:22 
srw------- 1 mr mr 0  1 月 19 18:12 git@github.com:22=
➤ 
```

我看可以看到, 这是一个 socket 文件, 并且:

```
➤ ss -ant4 | grep -E ':22'
ESTAB      0      0      10.177.46.227:40182              192.30.252.131:22                 
➤ 
```

也是可以看到这个已经建立的连接的. 节省了以后再次建立连接的时间. 真的很快 :)

# RAR 命令

## RAR 命令

虽说 RAR 是商业软件, 但是并不妨碍我们使用它啊.

Linux 下也有`rar`命令呢!

#### 建立一个 rar 压缩包

语法:

```
rar a {.rar file-name} {file-name} 
```

很简单:

```
➤ rar a sh.rar *.sh

RAR 5.30 beta 2   Copyright (c) 1993-2015 Alexander Roshal   4 Aug 2015
Trial version             Type RAR -? for help

Evaluation copy. Please register.

Creating archive sh.rar

Adding    4.sh                                                        OK 
Adding    cal_random.sh                                               OK 
Adding    cmd.sh                                                      OK 
Adding    ifs.sh                                                      OK 
Adding    redirect.sh                                                 OK 
Adding    std.sh                                                      OK 
Done
➤ 
```

支持正则, 支持递归.

#### 解压 rar 压缩包

解压也很简单:

```
➤ unrar e sh.rar

UNRAR 5.30 beta 2 freeware      Copyright (c) 1993-2015 Alexander Roshal

Extracting from sh.rar

Extracting  4.sh                                                      OK 
Extracting  cal_random.sh                                             OK 
Extracting  cmd.sh                                                    OK 
Extracting  ifs.sh                                                    OK 
Extracting  redirect.sh                                               OK 
Extracting  std.sh                                                    OK 
All OK
➤ 
```

#### 列出压缩包内的内容

```
➤ unrar l sh.rar

UNRAR 5.30 beta 2 freeware      Copyright (c) 1993-2015 Alexander Roshal

Archive: sh.rar
Details: RAR 4

 Attributes      Size     Date    Time   Name
----------- ---------  ---------- -----  ----
 -rw-rw-r--        25  2016-01-18 16:10  4.sh        
 -rw-rw-r--       542  2015-12-24 14:08  cal_random.sh
 -rw-rw-r--        41  2016-01-18 11:02  cmd.sh      
 -rw-rw-r--        27  2016-01-17 19:19  ifs.sh      
 -rw-rw-r--        50  2016-01-18 13:24  redirect.sh 
 -rw-rw-r--        56  2016-01-18 11:08  std.sh      
----------- ---------  ---------- -----  ----
                  741                    6

➤ 
```

#### 压缩加密

```
➤ rar a -p"Hello" sh.rar *.sh

RAR 5.30 beta 2   Copyright (c) 1993-2015 Alexander Roshal   4 Aug 2015
Trial version             Type RAR -? for help

Evaluation copy. Please register.

Creating archive sh.rar

Adding    4.sh                                                        OK 
Adding    cal_random.sh                                               OK 
Adding    cmd.sh                                                      OK 
Adding    ifs.sh                                                      OK 
Adding    redirect.sh                                                 OK 
Adding    std.sh                                                      OK 
Done
➤ 
```

# Comm 命令

## Comm 命令

`comm`全名是`compare`, 也就是用来对比文件的.

但是这个对比是有要求的, 要求就是两个文件必须先排好序, 然后`comm`再一行一行的对比.

```
➤ cat 1
1
2
4 #
5 #
➤ cat 2
1
2
3 #
4 #
➤ comm 1 2
        1
        2
    3 #文件 2 的不同
        4
5 #文件 1 的不同
➤ 
```

可以结合`diff`命令对比一下区别:

```
➤ diff 1 2
2a3
> 3
4d4
< 5 
```

下面的更直观一点:

```
➤ diff -u 1 2
--- 1    2016-01-19 19:28:28.979078030 +0800
+++ 2    2016-01-19 19:28:36.059135875 +0800
@@ -1,4 +1,4 @@
 1
 2
+3
 4
-5
➤ 
```

# Tee 命令

## Tee 命令

有时候我们需要把命令输出的内容保存为日志, 还想着实时看到命令输出的内容, 怎么办?

> tee - read from standard input and write to standard output and files

从标准输入中读取, 输出的同时并写入到文件中.

有点小牛 x.

#### 举几个栗子:

1.输出的同时写入到文件中:

```
➤ for i in {1..9};do > $RANDOM; done #创建了 9 个文件
➤ ls #查看一下, 的确创建成功了.
12681  16993  20566  21822  22742  25812  31954  5965  9458
➤ ls | tee files_of_here #然后用 tee 将输出保存到文件中
12681
16993
20566
21822
22742
25812
31954
5965
9458
files_of_here #这里有些奇怪, 为什么 ls 的时候多了一个这样的文件呢?
➤ cat files_of_here 
12681
16993
20566
21822
22742
25812
31954
5965
9458
files_of_here # 解答上面的问题, 因为 ls 和 tee 是同时进行的,
# 所以, tee 创建文件的时候, ls 还没读完整个目录中的文件名,
# 所以才会有一个 files_of_here 这样的文件, 如果还不明白, 看下面的例子.
➤ 
```

再一个例子:

```
➤ ls | tee 000 #我们创建一个 000 的文件
12681
16993
20566
21822
22742
25812
31954
5965
9458
files_of_here
➤ cat 000
12681
16993
20566
21822
22742
25812
31954
5965
9458
files_of_here
➤ ls
000  12681  16993  20566  21822  22742  25812  31954  5965  9458  files_of_here
➤ 
```

看出区别了么? 由于 000 数字比较小, ls 读取的时候先读的 12681 这个, 这个时候 tee 又创建了一个 000 的文件, 可惜 ls 不会再去读比 1 数字还小的文件了, 也就读不到 000 了.

下面还有一个例子:

```
➤ dir
12681  16993  20566  21822  22742  25812  31954  5965  9458  files_of_here
➤ dir | tee 000
000  12681  16993  20566  21822  22742    25812  31954  5965  9458  files_of_here
➤ cat 000
000  12681  16993  20566  21822  22742    25812  31954  5965  9458  files_of_here
➤ ls | tee 00
00
000
12681
16993
20566
21822
22742
25812
31954
5965
9458
files_of_here
➤ ls | tee 0000000
00
000
12681
16993
20566
21822
22742
25812
31954
5965
9458
files_of_here
➤ 
```

感觉到奇怪了么? 想想这是为什么. 这里不讲 dir 和 ls(我也不会...)

2.tee 和管道

本来 tee 就是从管道中读取的, 不过, 他也能够输出到管道中, 起到一个数据中转的作用:

```
➤ cat resout 
Give me a...
... flag!
➤ cat resout | tee content_of_resout | grep flag
... flag!
➤ cat content_of_resout 
Give me a...
... flag!
➤ 
```

3.`tee`和`vim`

如果用`vim`打开了一个没有写权限的文件, 但是你已经写了很多东西, 那该怎么办?

`tee`来拯救你.

在`vim`中命令模式下:

```
:w !sudo tee % 
```

更多的解释在这里:[`stackoverflow.com/questions/2600783/how-does-the-vim-write-with-sudo-trick-work`](http://stackoverflow.com/questions/2600783/how-does-the-vim-write-with-sudo-trick-work)

# Od 命令

## Od 命令

`od`的用途是以某种格式查看文件, 这里的格式, 指的是进制的格式, 比如八进制, 十进制.

看这样一个文件:

```
$ cat sample-file.txt 

abc     de
f     h 
```

用`od`查看:

```
# od -c special-chars.txt 

0000000   a   b   c      \t   d   e  \n   f                      \t   h
0000020  \n
0000021

# od -bc special-chars.txt 

0000000 141 142 143 040 011 144 145 012 146 040 040 040 040 040 011 150
          a   b   c      \t   d   e  \n   f                      \t   h
0000020 012
         \n
0000021 
```

其中:

*   `b` 显示 8 进制
*   `c` 显示原 ASCII 码

(其实除了`od`, 还有更好用的`xxd`, 个人觉着比`od`好一些.)