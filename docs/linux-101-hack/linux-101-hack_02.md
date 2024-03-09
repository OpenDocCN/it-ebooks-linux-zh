# 第二章 - 基本命令

基本命令包含了大部分系统操作常用的命令，这本书一共写了 20 个。

我在翻译的时候会尽量按照作者的本意，不过偶尔也会加点作料，让它们更有味道～

毕竟这只是个开篇，所以也没必要啰嗦。

各位看官瞧好吧 :P

# Hack-7 Grep

## Grep 命令

先说句题外话免得我最后忘了：`ag`，貌似比`grep`更强大。

> grep - print lines matching a pattern

通俗的说，就是查找。

怎么查找呢？看语法说明:

`grep [options] pattern [files]`

比如在`/etc/passwd`里面查找 **root**

```
➤ grep root /etc/passwd
root:x:0:0:root:/root:/bin/bash
➤ 
```

> 查找 **bash**

```
➤ grep bash /etc/passwd
root:x:0:0:root:/root:/bin/bash
mr:x:1000:1000:mr,,,:/home/mr:/bin/bash 
```

> **-v** 反向查找，即查找不带 pattern 的内容

如果还是用文件的话，输出的东西就多了，所以不在此演示。

> **-c** 只是看下有多少行匹配到了

```
➤ grep -cv ':' /etc/passwd
0
➤ grep -c bash /etc/passwd
2
➤ 
```

### 更多选项

> **-i** 忽略大小写 忽略 pattern 的大小写
> 
> **-r** 递归搜索 在某个目录下搜索全部匹配的文件
> 
> **-l** 只显示文件名 不显示匹配到的行
> 
> **-E** 扩展模式 后面可接正则表达式,更强大

### 扩展阅读

*   [15 个实用的 Grep 命令](http://www.thegeekstuff.com/2009/03/15-practical-unix-grep-command-examples/)
*   [强大的 'Z' 命令系列(Zcat, Zgrep, Zless, Zdiff )](http://www.thegeekstuff.com/2009/05/zcat-zless-zgrep-zdiff-zcmp-zmore-gzip-file-operations-on-the-compressed-files/)
*   [Grep 组合表达式](http://www.thegeekstuff.com/2011/10/grep-or-and-not-operators/)

# Hack-8 Grep 与正则表达式

## Grep 与正则表达式

其实学过正则表达式之后这些就不算啥事儿了,建议先学一下正则然后再来看.

不过直接看下面的更功利一些,毕竟看完了就可以直接用了.

下面这个是测试文的内容:

```
➤ cat -n fortune
    1    All generalizations are false, including this one.
    2            -- Mark Twain
    3    Many pages make a thick book, except for pocket Bibles which are on very
    4    very thin paper.
    5    
    6    Remark of Dr. Baldwin's concerning upstarts: We don't care to eat toadstools
    7    that think they are truffles.
    8            -- Mark Twain, "Pudd'nhead Wilson's Calendar"
    9    
    10    Sheriff Chameleotoptor sighed with an air of weary sadness, and then
    11    turned to Doppelgutt and said 'The Senator must really have been on a
    12    bender this time -- he left a party in Cleveland, Ohio, at 11:30 last
    13    night, and they found his car this morning in the smokestack of a British
    14    aircraft carrier in the Formosa Straits.'
    15            -- Grand Panjandrum's Special Award, 1985 Bulwer-Lytton 
```

#### 匹配句首 '^'

```
➤ grep -ni '^A' fortune
1:All generalizations are false, including this one.
14:aircraft carrier in the Formosa Straits.' 
```

> **-n** 的意思是显示行号

#### 匹配句尾 '$'

```
➤ grep -ni '\.$' fortune
1:All generalizations are false, including this one.
4:very thin paper.
7:that think they are truffles. 
```

这里的 **'\.$'** 是以 **.** 字符结尾的意思, 中间的反斜杠是逃逸字符,起转义的效果

#### 匹配空行 '^$'

```
➤ grep '^$' fortune -n
5:
9: 
```

#### 任意单个字符 '.'

```
➤ grep -n '.re' fortune
1:All generalizations are false, including this one.
3:Many pages make a thick book, except for pocket Bibles which are on very
6:Remark of Dr. Baldwin's concerning upstarts: We don't care to eat toadstools
7:that think they are truffles.
11:turned to Doppelgutt and said 'The Senator must really have been on a 
```

> **\b** 的意思是单词分界线,也许是空格,也许是 tab

```
➤ grep -n '\b.re\b' fortune
1:All generalizations are false, including this one.
3:Many pages make a thick book, except for pocket Bibles which are on very
7:that think they are truffles. 
```

#### 零次或多次重复 '*'

```
➤ grep -n 'l*' fortune
1:All generalizations are false, including this one.
2:        -- Mark Twain
3:Many pages make a thick book, except for pocket Bibles which are on very
4:very thin paper.
5:
6:Remark of Dr. Baldwin's concerning upstarts: We don't care to eat toadstools
7:that think they are truffles.
8:        -- Mark Twain, "Pudd'nhead Wilson's Calendar"
9:
10:Sheriff Chameleotoptor sighed with an air of weary sadness, and then
11:turned to Doppelgutt and said 'The Senator must really have been on a
12:bender this time -- he left a party in Cleveland, Ohio, at 11:30 last
13:night, and they found his car this morning in the smokestack of a British
14:aircraft carrier in the Formosa Straits.'
15:        -- Grand Panjandrum's Special Award, 1985 Bulwer-Lytton 
```

显示的不是很好,因为没有高亮什么的.

其实这一部分完全可以当做正则来讲, 毕竟正则才是核心, grep 只是一个工具, 怎么用,还是看你的能力如何了.

曾经看过一个 30 分钟入门正则表达式, 个人感觉挺不错的, 因为我也是从那里入的门, 所以也推荐看这一部分的人也去搜下那个, 作者写的够简单易懂了.

这一段写的有点长, 但是, 当你学会正则的时候你就会发现, 这东西真棒! (这是我 BB 的, 跟原作者无关哈...)

### 扩展阅读

*   [正则与 Grep (part I)](http://www.thegeekstuff.com/2011/01/regular-expressions-in-grep-command/)
*   [正则与 Grep (part II)](http://www.thegeekstuff.com/2011/01/advanced-regular-expressions-in-grep-command-with-10-examples-%E2%80%93-part-ii/)

# Hack-9 Find 命令

## Find 命令

Find 命令是一个常用的搜索命令,可以找到任何你想找到的文件.

基本语法:`find [pathnames] [conditions]`

#### 按照文件名查找

```
➤ find . -name '*py*' #支持正则表达式
./hello-world.py
./49.pyc
./helloword.py
./helloword.pyc
./49-2.py
./49.py 
```

#### 按文件大小查找

```
➤ find . -type f -size +200 #默认是 KB,也可以改成 100M 或者 1G
./kis.tar.bz2
./kismet/Kismet-20151220-13-28-40-1.pcapdump
./kismet/Kismet-20151220-13-28-40-1.netxml
./kismet/Kismet-20151220-13-28-40-1.nettxt
./kismet.tar.gz
./system.map 
```

> -type 指定文件类型

| type | 文件类型 |
| --- | --- |
| f | 普通文件 |
| d | 目录文件 |
| b | 随机存储的设备文件，如硬盘，光盘等存储设备 |
| c | 持续输入的设备文件，如鼠标，键盘 |
| p | 有名管道 |
| l | 链接文件(link) |
| s | socket 文件 |

#### 按修改时间查找

查找 60 天之前修改过的文件

```
➤ find . -mtime +60
./test.php
➤ ls -l test.php 
-rw-rw-r-- 1 mr mr 0 12 月 12  2001 test.php #我之前用 touch 修改过它 
```

#### 查找到了文件就删除

```
find / -type f -name *.tar.gz -size +100M -exec rm -f {} \; 
```

来我们详细说下这条命令, 首先是一个`-type`, 指定了文件类型, 然后跟着一个`-name`, 告诉 find 要找什么文件, 再接着是一个 size,规定了要找的文件的大小, 最后的 `-exec` 是执行某项命令, 命令是`rm -rf`, 参数是 `{}`, 这个`{}`是啥意思? 它代表的就是 find 查找到的文件, 最后的`\;`是告诉 find, 这条语句已经结束了,后面的事儿就不用它管了.

其实上面的语句完全可以用另一个参数来代替, `-delete`参数.

完整的命令如下:`find / -type f -name *.tar.gz -size +100M -delete`

### 扩展阅读

*   [Find 基础命令 I](http://www.thegeekstuff.com/2009/03/15-practical-linux-find-command-examples/)
*   [Find 进阶命令 II](http://www.thegeekstuff.com/2009/06/15-practical-unix-linux-find-command-examples-part-2/)

# Hak-10 重定向

## 重定向

其实重定向这里没什么好说的, 不过为了照顾新手以及满足原作者凑数的目的,还是在这里扯一扯.

如果你看过鸟哥的私房菜,你就不要再继续看下去了.

第一个要说的是标准输出重定向 **>**

```
➤ cat helloword.py
#!/usr/bin/python

print "Hello world!"

➤ 
```

```
➤ cat helloword.py > /dev/null
➤ 
```

第二个例子把输出的信息都重定向到 **/dev/null/** 了

> /dev/null 可以理解为无底洞, 所有进入里面的数据流都会消失~

上面例子中的箭头就是重定向符号, 还有其他的重定向符号,比如 **2>**, **&>**, 你就会问了,有没有 **1>** 呢? 当然有啊, **1>** 就是 **>**, 只是把 1 省略了而已.

如果要了解重定向的相关知识, 还请搜索 "linux 重定向".

实在没啥可讲的...

# Hack-11 Join 命令

## Join 命令

这个命令挺有用的, 用来把两个文件合并到一起.

> join - join lines of two files on a common field

比如, 下面有两个文件:

```
➤ cat join_1
1 a
2 b
3 c
4 d
5 e
➤ cat join_2
1 A
2 B
3 C
4 D
5 E
➤ 
```

然后用`join`把他们合起来:

```
➤ join join_1 join_2
1 a A
2 b B
3 c C
4 d D
5 e E 
```

有什么特殊要求嘛? 当然有, 要求就是两个文件有相同的字段.

# Hack-12 Tr 命令

## 　tr 命令

`tr`的全称是 translate, 翻译, 或者转换, 随你怎么理解.

下面看一下它的功能:

```
➤ cat join_1
1 a
2 b
3 c
4 d
5 e
➤ cat join_1 | tr a-z A-Z 
1 A
2 B
3 C
4 D
5 E
➤ tr a-z A-Z < join_1 
1 A
2 B
3 C
4 D
5 E
➤ 
```

看到了吗? `tr`就是转换字符的, 不仅可以转换大小写哦, 还能删除, 替换等一些操作.

比如:

```
➤ echo hello world | tr -d world
he 
➤ 
➤ echo hello world | tr -s e a
hallo world
➤ 
```

不过删除替换都比较鸡肋, 不如`sed`好用. 毕竟术业有专攻.

# Hack-13 Xargs 命令

## Xargs 命令

Xargs 是用来做什么的呢? OK, 先看下他能干什么:

```
➤ ls
1  2  3  4
➤ ls | xargs ls -l
-rw-rw-r-- 1 mr mr 0 12 月 26 20:46 1
-rw-rw-r-- 1 mr mr 0 12 月 26 20:46 2
-rw-rw-r-- 1 mr mr 0 12 月 26 20:46 3
-rw-rw-r-- 1 mr mr 0 12 月 26 20:46 4 
```

看懂了? 看懂才怪了.

`xargs`的作用是把输出的内容当做参数传递给下一个命令, 比如:

```
➤ ls | xargs cat
11111
222
33333
444
➤ 
```

`ls`的内容是当先文件夹下的文件, 然后`xargs`把`1 2 3 4`这些`ls`输出的东西都传递给了`cat`,然后就是上面的效果啦.

我再把作者给的几个例子放到下面, 看聪明的你能不能知道他们是干什么用的呢? 记得不懂的地方问`man`哦.

1.  `find ~ -name ‘*.log’ -print0 | xargs -0 rm -f`
2.  `find /etc -name "*.conf" | xargs ls –l`
3.  `cat url-list.txt | xargs wget –c`
4.  `find / -name *.jpg -type f -print | xargs tar -cvzf images.tar.gz`
5.  `ls *.jpg | xargs -n1 -i cp {} /external-hard-drive/directory`

# Hack-14 Sort 命令

## Sort 命令

一般来说命令的命名都是跟命令的作用有关的, 嗯, 我莫名想到了 thefuck ... (逃

`sort`就是用来排序的, 没错, 给文件内容排序:

```
➤ for i in {1..10};do echo $((RANDOM/20)) >> random; done
➤ cat random 
1164
1522
69
1470
1232
642
1494
12
543
685
➤ 
```

然后我们`sort`一下:

```
➤ sort random 
1164
12
1232
1470
1494
1522
543
642
685
69
➤ sort -n random 
12
69
543
642
685
1164
1232
1470
1494
1522
➤ 
```

是不是很好玩? 最后一个数字竟然是 69 ... 我好污...

再来个例子:

```
➤ cat passwd 
root:x:0:0
daemon:x:1:1
bin:x:2:2
sys:x:3:3
sync:x:4:65534
games:x:5:60
man:x:6:12
lp:x:7:7
mail:x:8:8
news:x:9:9
➤ 
```

我们先不带任何参数的排一下序:

```
➤ sort passwd 
bin:x:2:2
daemon:x:1:1
games:x:5:60
lp:x:7:7
mail:x:8:8
man:x:6:12
news:x:9:9
root:x:0:0
sync:x:4:65534
sys:x:3:3
➤ 
```

看到了吗, `sort`按照首字母排序了.

这次我们按照最后一个字段进行排序:

```
➤ sort -t : -k 4 passwd 
root:x:0:0
daemon:x:1:1
man:x:6:12
bin:x:2:2
sys:x:3:3
games:x:5:60
sync:x:4:65534
lp:x:7:7
mail:x:8:8
news:x:9:9 
```

好像不太对, 再加个参数呢:

```
➤ sort -n -t : -k 4 passwd 
root:x:0:0
daemon:x:1:1
bin:x:2:2
sys:x:3:3
lp:x:7:7
mail:x:8:8
news:x:9:9
man:x:6:12
games:x:5:60
sync:x:4:65534 
```

是不是看起来爽多了?

作者的这个例子很不错:

```
sort -t . -k 1,1n -k 2,2n -k 3,3n -k 4,4n /etc/hosts
127.0.0.1 localhost.localdomain localhost
192.168.100.101 dev-db.thegeekstuff.com dev-db
192.168.100.102 prod-db.thegeekstuff.com prod-db
192.168.101.20 dev-web.thegeekstuff.com dev-web
192.168.101.21 prod-web.thegeekstuff.com prod-web 
```

把 IP 地址排序, 可以研究下他是怎么做到的.

最后原作者还介绍了几套组合拳:

*   ps –ef | sort #给进程排序
*   ls -al | sort -nk 5 #根据文件大小进行排序
*   ls -al | sort -nrk 5 #根据文件大小反向排序

注意,这里跟原著有差别哦, 因为我按照原著上的并没有成功, 我的系统是 Ubuntu 15.10

# Hack-15 Uniq 命令

## Uniq 命令

这个命令也很好理解, 就是去重.

比如我又这样一个文件.

```
➤ cat uniq_file
1
1
2
2
3
3
4
4
5 
```

然后用`uniq`把里面的内容去重:

```
➤ uniq uniq_file
1
2
3
4
5
➤ 
```

查看 `man uniq` 获取更多.

# Hack-16 Cut 命令

## Cut 命令

`cut`命令是用来切割数据的, 没错, 竖着割.

刚才搞到三蛋的库子, 那么我就拿这个数据库做例子吧 :)

首先看文件里有什么:

```
➤ cat pass 
ahmed:sales@samaaegy.net:197.44.60.223:Ahmed2542604
ahmed:sales@spctec.com:41.196.193.71:AAHMEDEMAD944405
Ahmed:sales@time2timegroup.com:2.49.209.135:a13c14zI1Nwdck3a
ahmed:sales1@safetymisr.com.eg:197.44.60.223:ahmed2542604
ahmed:salesway@msn.com:216.241.47.198:hggih;fv
Ahmed:salfiste.hacker@gmail.com:41.225.249.155:suffeitula.10@
ahmed:salhexp@gmail.com:217.55.82.202:magicmasterz01
ahmed:salihomer681@yahoo.com:2.89.211.62:123qwe
Ahmed:salikok@live.com:87.109.81.252:faraz54321
ahmed:salma.ali1993@yahoo.com:217.26.255.72:asdf1234
ahmed:salma_love8226@yahoo.com:41.238.81.106:medo0125593126
ahmed:salmad123@gmail.com:81.192.238.123:sonunigam1
ahmed:salmamalikmother@gmail.com:65.49.14.11:love-you2
ahmed:salman.a7med@gmail.com:41.137.61.65:azerty123654789
ahmed:salmankhan200007@gmail.com:65.49.14.71:123asdzxc
ahmed:salmanlakho813@gmail.com:182.182.14.9:2k11swe75 
```

好, 里面是以 `用户名:邮箱:IP 地址:密码` 这种格式存储的, 那么,如果我们要想提取处里面所有的密码该怎么办呢?

这就是`cut`的威力了:

```
➤ cut -d : -f 4 pass 
Ahmed2542604
AAHMEDEMAD944405
a13c14zI1Nwdck3a
ahmed2542604
hggih;fv
suffeitula.10@
magicmasterz01
123qwe
faraz54321
asdf1234
medo0125593126
sonunigam1
love-you2
azerty123654789
123asdzxc
2k11swe75
➤ 
```

详细解释一下这条命令, 其中`-d`参数后面跟的是分隔符, 我们看到, 里面的数据都是用`:`分隔的, 所以用`-d :`把每一列分开, 然后的`-f`代表第几列, `f`也就是`filed`的意思. 由于密码是在第 4 个字段, 所以这里是`-f 4`.

提取用户名+密码呢?

```
➤ cut -d : -f 1,4 pass #用','分隔我们要选取的字段
ahmed:Ahmed2542604
ahmed:AAHMEDEMAD944405
Ahmed:a13c14zI1Nwdck3a
ahmed:ahmed2542604
ahmed:hggih;fv
Ahmed:suffeitula.10@
ahmed:magicmasterz01
ahmed:123qwe
Ahmed:faraz54321
ahmed:asdf1234
ahmed:medo0125593126
ahmed:sonunigam1
ahmed:love-you2
ahmed:azerty123654789
ahmed:123asdzxc
ahmed:2k11swe75 
```

提取邮箱后面的呢?

```
➤ cut -d : -f 2- pass #用'-'表示从哪儿到哪儿,如果不填则表示最后(或者最前)
sales@samaaegy.net:197.44.60.223:Ahmed2542604
sales@spctec.com:41.196.193.71:AAHMEDEMAD944405
sales@time2timegroup.com:2.49.209.135:a13c14zI1Nwdck3a
sales1@safetymisr.com.eg:197.44.60.223:ahmed2542604
salesway@msn.com:216.241.47.198:hggih;fv
salfiste.hacker@gmail.com:41.225.249.155:suffeitula.10@
salhexp@gmail.com:217.55.82.202:magicmasterz01
salihomer681@yahoo.com:2.89.211.62:123qwe
salikok@live.com:87.109.81.252:faraz54321
salma.ali1993@yahoo.com:217.26.255.72:asdf1234
salma_love8226@yahoo.com:41.238.81.106:medo0125593126
salmad123@gmail.com:81.192.238.123:sonunigam1
salmamalikmother@gmail.com:65.49.14.11:love-you2
salman.a7med@gmail.com:41.137.61.65:azerty123654789
salmankhan200007@gmail.com:65.49.14.71:123asdzxc
salmanlakho813@gmail.com:182.182.14.9:2k11swe75 
```

`cut`不仅可以用分隔符来把数据分开, 还可以按照字符分开,比如,我们要提取里面第 8 个字符,就可以这样:

```
➤ cut -c 8 pass 
a
a
a
a
a
a
a
a
a
a
a
a
a
a
a
a
➤ 
```

或者第 2 到第 8 个字符:

```
➤ cut -c 2-8 pass 
hmed:sa
hmed:sa
hmed:sa
hmed:sa
hmed:sa
hmed:sa
hmed:sa
hmed:sa
hmed:sa
hmed:sa
hmed:sa
hmed:sa
hmed:sa
hmed:sa
hmed:sa
hmed:sa
➤ 
```

虽然上面的数据看起来没有意义, 但`cut`用好了会节省你很多的时间.

# Hack-17 Stat 命令

## Stat 命令

`stat`命令是用来查看文件详细信息的:

```
➤ stat pass 
  File: ‘pass’
  Size: 870           Blocks: 8          IO Block: 4096   regular file
Device: fc01h/64513d    Inode: 5013885     Links: 1
Access: (0664/-rw-rw-r--)  Uid: ( 1000/      mr)   Gid: ( 1000/      mr)
Access: 2015-12-27 20:31:13.127178405 +0800
Modify: 2015-12-27 20:31:10.959155945 +0800
Change: 2015-12-27 20:31:10.959155945 +0800
 Birth: -
➤ 
```

扩展阅读:

[Stat - 获取文件信息](http://www.thegeekstuff.com/2009/07/unix-stat-command-how-to-identify-file-attributes/)

# Hack-18 Diff 命令

## Diff 命令

`diff`是用来找不同的 :)

使用很简单:

`diff [options] file1 file2`

比如我又两个文件:

```
➤ cat diff1
1
2
a
➤ cat diff2
1
2
3
4
➤ 
```

然后我`diff`一下:

```
➤ diff diff1 diff2
3c3,4
< a # < 表示在第二个文件中缺失的部分
---
> 3 # > 表示在第一个文件中缺失的部分
> 4
➤ 
```

上面的结果中:

`---`上面的内容是`diff1`文件的, `---`下面的内容则是`diff2`文件的内容.

那么`3c3,4`代表的是什么?

[stackexchange](http://unix.stackexchange.com/questions/81998/understaning-of-diff-output)上面有人说可以忽略...

但是第三个答案说的就比较详细:

> `3d2` and `5a5` denote line numbers affected and which actions were performed. `d` stands for deletion, `a` stands for adding (and `c` stands for changing). the number on the left of the character is the line number in file1.txt, the number on the right is the line number in file2.txt. So `3d2` tells you that the 3rd line in file1.txt was deleted and has the line number 2 in file2.txt (or better to say that after deletion the line counter went back to line number 2). `5a5` tells you that the we started from line number 5 in file1.txt (which was actually empty after we deleted a line in previous action), added the line and this added line is the number 5 in file2.txt.

但其实知道了也没卵用... 不如`vimdiff`或者`diff -u`更直观.

扩展阅读:

*   [About diff](http://www.thegeekstuff.com/2010/06/linux-file-diff-utilities/)
*   [Vimdiff](http://www.thegeekstuff.com/2010/06/vimdiff-file-diff-tool/)

# Hack-19 Ac 命令

## Ac 命令

先要安装`acct` - `#sudo apt-get install acct`

然后...

1.  `ac -d` 显示当先登录用户的使用时间
2.  `ac -p` 显示所有用户的登陆使用时间
3.  `ac -d xxxx` 显示 xxx 用户的登陆使用时间

感觉用处不是很大, 除非你的机子是多用户共同操作的.

有一种情况除外, 可以查看有没有未经允许的登陆, 被黑了好查证, 不过... 被黑之后这种文件一般都不存在了...

# Hack-20 让命令在后台执行

## 让命令在后台执行

后台执行命令有很多方法,比如:

### 1\. 使用 `&`

在你想要后台执行的命令最后加上一个`&`就能让命令在后台运行了.

```
➤ htop &
[1] 12781
➤ ps
  PID TTY          TIME CMD
12554 pts/5    00:00:00 bash
12781 pts/5    00:00:00 htop
12782 pts/5    00:00:00 ps
➤ 
```

怎样把再它调出来呢? 用`fg`命令. 还可以用`jobs`命令查看当前终端上运行的后台程序.

### 2.使用 `nohup`

> nohup - run a command immune to hangups, with output to a non-tty

这是正统的后台运行.

`nohup ./my-shell-script.sh &`

默认程序的输出内容会保存到当前目录下的`nohup.out`文件中, 所以一般运行的时候都是这样(如果你不需要查看运行结果的话):

`nohup ./my-shell-script.sh &> /dev/null &`

### 3.使用 `screen`

这也是后台运行常用的工具, 比起`nohup`, 它可以随时随地查看运行情况, 并且进行操作, 更多关于`screen`的介绍请看: [Linux 技巧：使用 screen 管理你的远程会话](http://wrfly.kfd.me/screen%20in%20linux/ "我转载的..")

其实还有一个比`screen`更酷炫的软件, 叫 `tmux`, 有兴趣可以去了解下哦~

### 4\. 使用 `at`

这真是巧妙, 作者能想到这一点我也是佩服, `at`一般都是作为计划任务来使用的, 但是这样也能起到后台运行的效果.

再明天上午十点执行备份脚本: `at -f backup.sh 10 am tomorrow`

或者现在就执行: `at -f backup.sh now` # **-f** 的意思是执行后面的文件

查看任务队列用`atq`

删除任务可以用 `adrm [number_of_task]` 或者 `ad -d [number_of_task]`

### 5.使用`watch`

这个说起来就有点牵强了, 毕竟`watch`不是干这个的呀.

原作说的是持续运行某条命令, 凑活写上吧..

每隔五秒执行一次`df -h`, 如果不加`-n 5`的话, 默认是两秒, `watch -n 5 'df -h'`

### 扩展阅读

*   [bg, fg, &, Ctrl-Z – 5 Examples to Manage Unix Background Jobs](http://www.thegeekstuff.com/2010/05/unix-background-job/)
*   [Unix Nohup: Run a Command or Shell-Script Even after You Logout](http://linux.101hacks.com/unix/nohup-command/ "hey")
*   [Screen Command Examples: Get Control of Linux / Unix Terminal](http://www.thegeekstuff.com/2010/07/screen-command-examples/)
*   [at, atq, atrm, batch Commands using 9 Examples](http://www.thegeekstuff.com/2010/06/at-atq-atrm-batch-command-examples/)

# Hack-21 Sed 替换基础

## Sed 替换基础

这里介绍了`sed`的替换技巧

首先看一下用法:

```
sed 's/REGEXP/REPLACEMENT/FLAGS' filename 
```

其中:

*   **s** 表示执行替换操作
    *   常用的还有 **d** 表示删除
*   **/** 表示分隔符
    *   分隔符还可以用 **@** 或者 **%** 或者 **;** 或者 **:** 表示
*   **REGEXP** 表示匹配的正则
*   **REPLACEMENT** 表示要替换的内容
*   **FLAGS** 表示标志
    *   **g** 全局替换, 从头到尾都换
    *   **n** 可以是任意数字, 表示的是替换第 n 次出现的地方
    *   **p** 打印两次匹配到的内容, 即,如果替换成功则打印两遍
    *   **i** 忽略大小写的匹配
    *   **w** 后面跟个文件, 如果产生了替换,则将替换后的结果输出到文件中

然后我们来看这样一个文件:

```
➤ cat sed.txt
# Instruction Guides
1. Linux Sysadmin, Linux Scripting etc.
2. Databases - Oracle, mySQL etc.
3. Security (Firewall, Network, Online Security etc)
4. Storage in Linux
5. Productivity (Too many technologies to explore, not
much time available)
#
Additional FAQS
6. Windows- Sysadmin, reboot etc. 
```

### 1\. 把第一个 'Linux' 替换成 'Linux-Unix'

```
➤ sed 's/Linux/Linux-Unix/' sed.txt
# Instruction Guides
1. Linux-Unix Sysadmin, Linux Scripting etc.
2. Databases - Oracle, mySQL etc.
3. Security (Firewall, Network, Online Security etc)
4. Storage in Linux-Unix
5. Productivity (Too many technologies to explore, not
much time available)
#
Additional FAQS
6. Windows- Sysadmin, reboot etc. 
```

这个例子很简单, 不多说了.

### 2\. 把 'Linux' 全部替换成 'Linux-Unix'

```
➤ sed 's/Linux/Linux-Unix/g' sed.txt #注意,这里是全部替换哦,所以有个 g(global)
# Instruction Guides
1. Linux-Unix Sysadmin, Linux-Unix Scripting etc.
2. Databases - Oracle, mySQL etc.
3. Security (Firewall, Network, Online Security etc)
4. Storage in Linux-Unix
5. Productivity (Too many technologies to explore, not
much time available)
#
Additional FAQS
6. Windows- Sysadmin, reboot etc. 
```

### 3.仅把第二个 'Linux' 换成 'Linux-Unix'

```
➤ sed 's/Linux/Linux-Unix/2' sed.txt 
# Instruction Guides
1. Linux Sysadmin, Linux-Unix Scripting etc.
2. Databases - Oracle, mySQL etc.
3. Security (Firewall, Network, Online Security etc)
4. Storage in Linux
5. Productivity (Too many technologies to explore, not
much time available)
#
Additional FAQS
6. Windows- Sysadmin, reboot etc. 
```

看到了吗, 这里的标志位是`2`哦~

### 4\. 全部替换＋打印替换内容到屏幕＋保存输出到文件

```
➤ sed -n 's/Linux/Linux-Unix/gpw output' sed.txt 
1. Linux-Unix Sysadmin, Linux-Unix Scripting etc.
4. Storage in Linux-Unix
------
➤ cat output 
1. Linux-Unix Sysadmin, Linux-Unix Scripting etc.
4. Storage in Linux-Unix 
```

这次的例子有点奇怪, 标志位是`gpw`我们已经知道`g`代表的是全部替换,那么`p`和`w`又是啥? 通过`info sed`我们可以查到:

> `p' - If the substitution was made, then print the new pattern space.
> 
> `w FILE-NAME' - If the substitution was made, then write out the result to the named file.

现在你懂了嘛? 不懂? 查词典去!

#### 5.仅仅替换特殊的行

```
➤ sed '/\-/s/\-.*//g' sed.txt 
# Instruction Guides
1. Linux Sysadmin, Linux Scripting etc.
2. Databases 
3. Security (Firewall, Network, Online Security etc)
4. Storage in Linux
5. Productivity (Too many technologies to explore, not
much time available)
#
Additional FAQS
6. Windows 
```

看到这个命令与其他的不同了吗?

没错, 就是在`s`前面加了点东西, 加的东西就是我们要匹配的行, 这次加的是`\-`, 所以就替换了`-`后面的内容. 举一反三, 你是不是可以想出别的方案呢? 试试看!

### 6.删除某些字符

```
➤ sed 's/\(.....\)\(...\)$/\2/g' sed.txt 
# Instructiodes
1. Linux Sysadmin, Linux Scripttc.
2. Databases - Oracle, mytc.
3. Security (Firewall, Network, Online Securtc)
4. Storage nux
5. Productivity (Too many technologies to explnot
much time avle)
#
AdditioAQS
6. Windows- Sysadmin, rebtc. 
```

看懂了吗? 没有? 好, 那看下一个:

```
➤ sed 's/...$//g' sed.txt 
# Instruction Gui
1. Linux Sysadmin, Linux Scripting e
2. Databases - Oracle, mySQL e
3. Security (Firewall, Network, Online Security e
4. Storage in Li
5. Productivity (Too many technologies to explore, 
much time availab
#
Additional F
6. Windows- Sysadmin, reboot e 
```

这个应该看懂了吧?

上一个例子是删除了最后三个字符, 上上一个例子是删除了倒数第八到第五个字符, 也就是, 把最后八个字符换成了最后三个字符, 有点绕? 多想一会儿!

再来一个删除 html 标签的:

```
➤ echo "This <b> is </b> an <i>example</i>." | sed -e 's/<[^>]*>//g'
This  is  an example. 
```

最后一个例子:

```
➤ sed 's/#.*//g' sed.txt 

1. Linux Sysadmin, Linux Scripting etc.
2. Databases - Oracle, mySQL etc.
3. Security (Firewall, Network, Online Security etc)
4. Storage in Linux
5. Productivity (Too many technologies to explore, not
much time available)

Additional FAQS
6. Windows- Sysadmin, reboot etc. 
```

替换显得太难看了, 不如直接删除:

```
➤ sed '/#.*/d' sed.txt 
1. Linux Sysadmin, Linux Scripting etc.
2. Databases - Oracle, mySQL etc.
3. Security (Firewall, Network, Online Security etc)
4. Storage in Linux
5. Productivity (Too many technologies to explore, not
much time available)
Additional FAQS
6. Windows- Sysadmin, reboot etc. 
```

`sed`是个好东西, 光`info sed`就有 98K 这么大, 好好看看咯~ (我没看完...逃...

### 扩展阅读

*   [Advanced Sed Substitution Examples](http://www.thegeekstuff.com/2009/10/unix-sed-tutorial-advanced-sed-substitution-examples/)
*   [Sed Tutorial](http://www.thegeekstuff.com/2009/09/unix-sed-tutorial-replace-text-inside-a-file-using-substitute-command/)

# Hack-22 Awk 简介

## Awk 简介

### 简介

> **Awk** stands for the names of its authors “Aho, Weinberger, and Kernighan”

**Awk**也是一种编程语言, 他能让你格式化数据以及生成特定格式的报告.(翻译的好糙...)

总之, 你只需要记住它是用来格式化数据的就 OK 了.

`Awk`更像是一个过滤器, 它把读入的数据一行一行的过滤, 匹配你想要的内容, 如果匹配到了, 那就格式化输出, 匹配不到的就忽略.

`Awk`有一些特性:

*   它把数据视为 '记录' 和 '字段'
*   它也有变量, 条件和循环.
*   它有数学操作符和字符串操作符
*   它能生成格式化的报告
*   它从标准输入读取数据, 把过滤之后的数据从标准输出打印出来, 不处理空文件.

基本语法:

```
awk '/search pattern1/ {Actions} /search pattern2/ {Actions}' file 
```

其中:

*   `search pattern`是要匹配的字符串
*   `Actions`是匹配到之后所进行的动作
*   它能处理多个匹配和行为
*   `file`是值输入文件
*   单引号的作用是防止里面的特殊字符被 shell 处理

### **Awk**的工作方法

1.  `Awk`每次从输入文件中读取一行.
2.  对于每一行, 如果匹配到了相应的内容, 就会执行相应的动作.
3.  如果匹配不到, 就什么也不做.
4.  在上面的语法中, 匹配项和动作有一即可.
5.  如果没有给出匹配项, Awk 就会把每一行按照给定的动作执行
6.  如果动作没有给出, 默认是打印.
7.  如果大括号里面是空的, 那就什么也不干.(因为这样就代表给出了一个空的动作)
8.  在大括号里的每一个动作需要用分号(`;`)隔开.

### 然后我们开始举栗子:

先是有这样一个文件:

```
➤ cat awk.txt 
100 Thomas Manager Sales $5,000
200 Jason Developer Technology $5,500
300 Sanjay Sysadmin Technology $7,000
400 Nisha Manager Marketing $9,500
500 Randy DBA Technology $6,000 
```

#### 打印每一行

```
➤ awk '{print;}' awk.txt
100 Thomas Manager Sales $5,000
200 Jason Developer Technology $5,500
300 Sanjay Sysadmin Technology $7,000
400 Nisha Manager Marketing $9,500
500 Randy DBA Technology $6,000
➤ 
```

这里我们没有给出匹配项, 仅给出了动作,`print`打印.

#### 打印匹配到的行

```
➤ awk '/Thomas/;/Nisha/' awk.txt
100 Thomas Manager Sales $5,000
400 Nisha Manager Marketing $9,500 
```

这里跟原著有点不太一样, 原著是用回车把每一个要匹配的内容分开的, 但是这样不好修改, 看起来也怪怪的, 所以, 不如直接用分号把他们隔开啦~

上面的栗子打印了匹配到那两个名字的行(记录).

#### 打印特定的字段(列)

这个例子打印了第 2 和第 5 个字段.

```
➤ awk '{print $2,$5}' awk.txt
Thomas $5,000
Jason $5,500
Sanjay $7,000
Nisha $9,500
Randy $6,000 
```

再看下面这个例子:

```
➤ awk '{print $3,$NF}' awk.txt
Manager $5,000
Developer $5,500
Sysadmin $7,000
Manager $9,500
DBA $6,000 
```

有点不一样了是吧? `$NF`的意思是最后一个字段.

每个字段都要用逗号(`,`)分开.

#### 开始和结束

`awk`有一种特定的语法, 开始和结束.

```
BEGIN { Actions}
{ACTION} # 文件中的每一行默认的动作
END { Actions } 
```

有什么用呢? 正如表述所言, `开始`定义了输出一开始的动作,而`结束`则定义了输出结束时的动作.

比如:

```
➤ awk 'BEGIN {print "Name\t Designation\tDepartment\tSalary";} {print $2,"\t",$3,"\t",$4,"\t",$NF;} END{print "Report Generated\n--------------";}' awk.txt 
Name     Designation    Department    Salary
Thomas      Manager      Sales      $5,000
Jason      Developer      Technology      $5,500
Sanjay      Sysadmin      Technology      $7,000
Nisha      Manager      Marketing      $9,500
Randy      DBA      Technology      $6,000
Report Generated
-------------- 
```

如果太长了, 中间可以用回车隔开:

```
$ awk 'BEGIN {print
"Name\tDesignation\tDepartment\tSalary";}
> {print $2,"\t",$3,"\t",$4,"\t",$NF;}
> END{print "Report Generated\n--------------";
> }' employee.txt
Name Designation Department Salary
Thomas Manager Sales $5,000
Jason Developer Technology $5,500
Sanjay Sysadmin Technology $7,000
Nisha Manager Marketing $9,500
Randy DBA Technology $6,000
Report Generated
-------------- 
```

#### 条件语句

`awk`也有条件语句, 比如比较大小:

```
➤ awk '$1 >= 200' awk.txt 
200 Jason Developer Technology $5,500
300 Sanjay Sysadmin Technology $7,000
400 Nisha Manager Marketing $9,500
500 Randy DBA Technology $6,000 
```

上面的例子是打印第一个字段大于等于 200 的行.

再看下面这个例子, 是对字符串进行比较的:

```
➤ awk '$4 ~ /Tech/' awk.txt 
200 Jason Developer Technology $5,500
300 Sanjay Sysadmin Technology $7,000
500 Randy DBA Technology $6,000 
```

有点像`[[ ]]`里面的正则匹配, 好像就是正则匹配...

```
➤ awk '$4 ~ /[tT][abcde]/' awk.txt 
200 Jason Developer Technology $5,500
300 Sanjay Sysadmin Technology $7,000
500 Randy DBA Technology $6,000 
```

有点意思了,是吧!

#### 计数!

`awk`既然是一种编程语言, 那基本的计数也是应该的:

```
➤ awk 'BEGIN { count=0;} $4 ~ /Tech/ { count++; } END { print "Number of employees in Technology Dept=",count;}' awk.txt 
Number of employees in Technology Dept= 3 
```

```
➤ awk 'BEGIN { count=0;}
> $4 ~ /Tech/ { count++; } 
> END { print "Number of employees in Technology Dept=",count;}' awk.txt
Number of employees in Technology Dept= 3 
```

#### 附言

`awk`作为一门编程语言, 这点介绍仅仅是皮毛而已, 就像, 虽然你学了点 C 语言,能输出个 12345 了, 可 C 能做的可不是这么简单. :)

(我也只是学了点皮毛...)

#### 扩展阅读

*   [Awk 入门](http://www.thegeekstuff.com/2010/01/awk-introduction-tutorial-7-awk-print-examples/)
*   [理解 Awk 变量](http://www.thegeekstuff.com/2010/01/awk-tutorial-understand-awk-variables-with-3-practical-examples/)
*   [8 个 Awk 内置变量](http://www.thegeekstuff.com/2010/01/8-powerful-awk-built-in-variables-fs-ofs-rs-ors-nr-nf-filename-fnr/)
*   [7 个 Awk 操作符](http://www.thegeekstuff.com/2010/02/unix-awk-operators/)
*   [Awk 数组](http://www.thegeekstuff.com/2010/03/awk-arrays-explained-with-5-practical-examples/)

(这些扩展阅读也都是原作者自己写的, 很厉害的一个家伙!

# Hack-23 VIM 基本入门

## VIM 基本入门(移动)

为什么叫 *基本入门* 而不是 *入门* 呢?

因为我觉着我都还没入门...

这里作者主要说的是怎样移动你的光标, 毕竟没有鼠标, 所以快捷键就显得非常重要了, 这里介绍了很多快速移动的快捷键, 希望大家熟记于心.(拿出来装逼也好呀)

作者写了八个方面, 在此列举:

1.  行间移动
2.  屏幕间移动
3.  单词移动
4.  特殊移动
5.  段落间移动
6.  搜索移动
7.  代码间移动
8.  从命令行中移动(command 模式)

由于这些操作都是需要自己动手的, 没办法展示出来(其实还是由办法的, 比如 gif 图,有好多软件可以做到,看[这里](http://askubuntu.com/questions/107726/how-to-create-animated-gif-images-of-a-screencast)的回答; 还有就是用`script`"录"下来,不过那个文件还要传到上面,反正挺麻烦的,不如让各位看官自己动手了,好了,不废话了...)

### 1\. 行间移动

| 按键 | 方向 |
| --- | --- |
| **k** | 向**上**移动 |
| **j** | 向**下**移动 |
| **h** | 向**左**移动 |
| **l** | 向**右**移动 |
| **10j** | **下移 10**行 |
| **5h** | **左移 5**个字母 |
| **0** | 移动到**行首** |
| **^** | 移动到**行首第一个单词** |
| **$** | 移动到**行尾** |
| **g_** | 移动到**行尾第一个单词** |

### 2\. 屏幕间移动

也就是以屏幕为单位的移动啦

| 按键 | 方向 |
| --- | --- |
| **H** | 移动到本屏**首行** |
| **M** | 移动到本屏的**中间** |
| **L** | 移动到本屏的**尾行** |
| **Ctrl + f** | 向**上**移动一个屏幕 |
| **Ctrl + b** | 向**下**移动一个屏幕 |
| **Ctrl + d** | 向**下**移动**半个**屏幕 |
| **Ctrl + u** | 向**上**移动**半个**屏幕 |

### 3\. 特殊移动

下面的是比较特殊的移动方式:

| 按键 | 方向 |
| --- | --- |
| **N%** | 移动到文件的 N%的位置, 比如`50%` |
| **NG** | 移动到文件第 N 行, 比如`6G` |
| **gg** | 移动到文件头 |
| **G** | 移动到文件末尾 |
| **`"** | 移动到上次在"Normal"模式下关闭文件时的地方 |
| **`^** | 移动到上次在"Insert"模式下关闭文件时的地方 |

### 4\. 单词间移动

| 按键 | 方向 |
| --- | --- |
| **e** | 移动到单词末尾 |
| **E** | 移动到大单词末尾 |
| **b** | 移动到上一个小单词 |
| **B** | 移动到上一个大单词 |
| **w** | 移动到下一个小单词 |
| **W** | 移动到下一个大单词 |

**大小写的区别:**

*   大写的移动: 移动的单词为一连串. 比如 `192.168.1.1` – 是**1**个大写的单词
*   小写的移动: 移动的单词以非数字或字母为分界线. 比如 `192.168.1.1` – 是**7**个小写的单词

其实你自己动手操作下就能够知道他们之间的区别了.

### 5\. 段落间移动

所谓"段落",就是用空行隔开的句子段.

| 按键 | 方向 |
| --- | --- |
| **{** | 移动到段首 |
| **}** | 移动到段尾 |

### 6\. 搜索移动

| 按键 | 方向 |
| --- | --- |
| **/text** | 从光标处向下搜索 |
| **?text** | 从光标处向上搜索 |
| ***** | 移动到光标所在单词的下一个位置 |
| **#** | 移动到光标所在单词的上一个位置 |

其实这里说的不全, 因为还有`n`和`N`的存在.`n`是移向下一个搜索目标,`N`是移向上一个搜索目标.

`*`就好像一个组合键, 搜索光标所在位置的单词的同时,又移向了下一个目标. 这对于编辑 html 文档很有用, 闭合标签嘛

`#`类似, 只不过搜索方向不同而已.

### 7\. 代码间移动

这个就是在两个括号之间移动.... 按`%`就可以在两个半闭合的括号来回移动, 所谓 "代码间移动".... 我都觉着这名字很狗血...

### 8\. 从命令行中移动(command 模式)

这个的意思是在打开的时候就移动到某一行, 我不知道怎样起名字,暂且称之为标题中的吧... 狗血就狗血吧...

*   `vim +10 /etc/passwd` # 打开`/etc/passwd`之后,光标在第十行
*   `vim +/install README` # 打开 README 之后,光标在第一个`install`前面(如果有的话)
*   `vim +?bug README` # 打开 README 之后, 光标在最后一个`bug`前面(同上)

### 扩展阅读

*   [8 Essential Vim Editor Navigation Fundamentals](http://www.thegeekstuff.com/2009/03/8-essential-vim-editor-navigation-fundamentals/)
*   [Vim search and replace – 12 powerful find and replace examples.](http://www.thegeekstuff.com/2009/04/vi-vim-editor-search-and-replace-examples/)
*   [How To add bookmarks inside the Vim editor](http://www.thegeekstuff.com/2009/02/how-to-add-bookmarks-inside-vi-and-vim-editor/)
*   [How To record and play inside the Vim editor](http://www.thegeekstuff.com/2009/01/vi-and-vim-macro-tutorial-how-to-record-and-play/)
*   [Correct spelling mistakes automatically inside the Vim Editor](http://www.thegeekstuff.com/2009/03/vim-editor-how-to-correct-spelling-mistakes-automatically/)

# Hack-24 Chmod 命令

## Chmod 命令

感觉作者是在凑数.... `chmod`还不如`chattr`有趣...

#### 三个代表

文件的权限有三个代表, 代表最广大人民....

上面当然是在扯淡, 因为这里没啥好讲的, 每个文件都有自己的属性, 每个属性都有不同的权限, 权限分为三种, 每种代表不同的用户.

*   u 代表用户,也就是文件的所有者 (user)
*   g 代表用户组, 也就是文件的所有组 (group)
*   o 代表其他人, 也就是除上面两者之外的人或者用户组 (others)

#### 三种权限

不同的人,组, 对文件拥有不同的读写权限.

*   r 代表读权限 (read) = 4
*   w 代表写权限 (write) = 2
*   x 代表执行权限 (excute) = 1

如果一个文件是可执行文件, 那么给相应的用户添加`x`后,那个用户就可以执行这个文件. 我们知道,目录也是文件, 而目录的可执行权限就是**进入目录(或者读取目录的内容,或者对目录里的内容进行操作,比如,删除.)**, 如果取消了你对某个目录的可执行权限以后, 你就进不去了, 另外一个误区就是, **如果你对某个目录具有执行权限, 那么你就可以对目录下的内容进行移动, 删除操作, 不管这个文件是属于谁的.**

上面的 `4 2 1` 分别对应某种权限的数字表示, 我们常说的设置权限为`755`,就是让`u`的权限为`7`,`g`的权限为`5`, `o`的权限也为`5`, `7`代表什么呢? 代表`4+2+1`也就是`rwx`权限, 那么`5`也就好解释了,`5=4+1`,也就是`r+x`权限.

#### 下面开始翻译...

1.  给文件的所有者添加执行权限: `chmod u+x filename`
2.  给文件所有者添加读权限,并且给文件所属组添加执行权限: `chmod u+r,g+x filename`
3.  给文件的所有者去除读权限和执行权限: `chmod u-rx filename`
4.  给所有用户(u+g+o)添加文件的执行权限: `chmod a+x filename`
5.  设置某个文件(file2)的权限与另一个文件(file1)相同: `chmod --reference=file1 file2`
6.  递归设置文件权限: `chmod -R 755 dir/`
7.  匹配正则: `chmod u+x *.py`

#### 扩展阅读

[Beginners Guide to File and Directory Permissions](http://www.thegeekstuff.com/2010/04/unix-file-and-directory-permissions/)

我感觉这里啰嗦的东西完全是在凑命令... 建议大家看一下比较冷门的`chattr`, 算式隐藏命令吧, 尤其是 `a`和`i`这两个权限. 很有用的.

# Hack-25 Tail -f -f

## 同时用`tail`查看两个 log 文件

原作者在这里啰里啰嗦的说了一堆, 总结起来就一个技巧:

```
tail -f access.log -f error.log 
```

这就是用`tail`同时查看两个增长的文件, 这样你就不用开启两个终端,或者是用`screen` or `tmux`这些额外工具了.

用处还是蛮大的. 比如在本地调试网站的时候, 这个命令就很有用, 同时查看好几个日志.

# Hack-26 Less 命令

## Less 命令

查看文件的时候, 如果文件不是很小(超过了 ternimal 的高度), 那最好还是用`more`或者`less`来查看.

`less`和`more`的区别在于, `more`只能往下翻, 而`less`允许用户往上翻 :)

而且用`less`查看文件的时候, `less`并不是把文件全部加载到内存中后再输出,而是直接就输出, 相当给力. 如果你又一个大文件(超过 1G), 你想简单地浏览一下时, 还是推荐用`less`.无论从资源消耗还是打开速度,`less`绝对是你的不二选择.

#### less 搜索移动

我们可以在用`less`打开的文件中搜索内容, 命令语法跟`vim`差不多, 都是用`/`向下搜索, `?`向上搜索. `n`搜索下一个,`N`搜索上一个.

这里有个小技巧, 推荐用`?`来搜索, 因为这样可以不用转义`/`, 如果你搜索的内容正好有这个字符的话.

#### less 翻页

我们当然可以使用`PageUp`或者`PageDown`来翻页, 不过假如你足够懒, 不想让手指移动很多路径, 那么你就可以选择下面的方案:

*   `Ctrl + f` 或者 `f` 向前翻一页
*   `Ctrl + b` 或者 `b` 向后翻一页
*   `Ctrl + d` 或者 `d` 向下翻半页
*   `Ctrl + u` 或者 `u` 向上翻半页

#### less 移动

跟`vim`一样, `hjkl`方向移动, 当然,方向键和鼠标滚轮也都好使.

`G`-移动到末尾, `g`-移动到文件头部, `q`或`ZZ`退出.

`10j`往下移动 10 行, `5k`往上移动 5 行.

#### 模仿 `tail -f`

没错, less 强大到它可以追踪文件流, 按下`F`后, 就可以像`tail -f`一样查看文件的变化, `Ctrl + c`可以退出.

#### 其他

`Ctrl + g` 显示当前进度, 文件信息(行数,字节数)

`v` - 这个特别有用, 如果你查看过这个文件后想用你默认的编辑器编辑一下这个文件的话, 那么按一下`v`就可以了~ 很方便.

`h` 显示帮助, 包括各种快捷键的详细介绍.

`&pattern` 显示匹配到 pattern 的行, 正则表达式哟.

#### 标记

如果你浏览到某个地方想要标记一下, 那么按下`m`键后再按下一个标记键,比如, `a`, 那么你就在当前屏幕有了一个名字叫`a`的标记点(**区分大小写的哦, `a`和`A`是不一样的标记点!**).

那怎样返回这个标记呢? 再按下`'`,也就是单引号, 底部就会出现`goto mark:`这样的提示, 按下`a`就回到了`a`标记点, 按下`A`就会到`A`标记点...

然后, 这个跟`VIM`一模一样!!! (`vim`里面标记也是这样的, 还能输入`marks`来查看所有的标记.)

#### 多文件操作

你可以用 `less Textfile Logfile` 来同时打开两个文件,文件之间的切换用`:n`和`:p`,`n`代表`next`,下一个文件;`p`呢, 代表`previous`, 上一个文件.

当然, 在浏览文件的同时也可以打开另一个文件, 输入`:e`,然后就会提示:`Examine:`让你输入文件名, 文件名是可以用`Tab`补全的.

#### 扩展阅读

*   [Less Command: 10 Tips for Effective Navigation](http://www.thegeekstuff.com/2010/02/unix-less-command-10-tips-for-effective-navigation/)
*   [Open & View 10 Different File Types with Linux Less Command](http://www.thegeekstuff.com/2009/04/linux-less-command-open-view-different-files-less-is-more/)

# Hack-27 Wget 下载器

## Wget 下载器

`wget`是 Linux 标配的下载器, 虽然作者说他是 Linux 上最好的选择, 但是还有`aria2` 这个多线程下载神器, 个人感觉要比`wget`好.

在这里, 作者介绍了 15 个`wget`的应用.

#### 1\. 下载单一文件

```
wget "http://url.com/file" 
```

很简单 :)

#### 2\. 将下载的文件重命名

```
wget "http://url.com/file" -O rename 
```

`-O`的选项, 使得下载的文件被重命名了.这个开关很有用, 因为`wget`默认下载的文件是根据 URL 来命名的, 如果 URL 中有很多参数, 那么你下载的文件就会是个乱七八糟的名字, 最常见的是下载百度云...(百度云不开会员就限速!)

#### 3\. 下载限速

我们可以用`--limit-rate`这个选项来限制`wget`的下载速度, 比如:

```
wget --limit-rate=200k "http://url.com/file.tar.bz2" 
```

就会限制下载速度到 200KB/s

#### 4\. 继续下载

这个功能也很常用, 比如, 正下载到一半断网了, 或者网络中断, 那么就可以用`-c`的开关来定义继续下载, continue 嘛!

需要注意的是, 如果不加这个开关, 那么就会重新下载一个新的文件, 并且如果当前目录下有重名的文件, 会在重名文件的后缀名上加一个`.1`, 如果有`.1`了, 就会加个`.2`, 所以这个`-c`的开关很重要呢!

```
wget -c "http://url.com/file.tar.bz2" 
```

#### 5\. 后台下载

这个也很有用, 甬道的开关是`-b` - background

如果开启了这个开关的话, 就不会有任何输出, 但是记录还是要有的, 默认是在当前目录下创建一个`wget-log`的文件, 若你需要指定记录文件的名字呢, 就需要用到`-o`这个选项了, 后面接要保存的文件名, 注意了, 这里是小写的`o`哦!

```
wget -b "http://url.com/downloads.tar" -o downloads.log -O download_file" 
```

#### 6\. 指定下载的 User Agent

用`--user-agent`这个选项可以手动指定我们的 user-agent ,这在目标服务器限制 User Agent 的时候很有用.

```
wget --user-agent="I'm not Wget.." "http://url.com/downloads.tar" 
```

#### 7\. 测试目标文件

我们拿到一个下载地址之后就直接开始下载嘛? 一般情况下是的, 但是不排除我们只想看一下目标文件是什么, 类型啦, 大小啦, 甚至目标文件是否真的存在(返回代码是什么).

这个时候就要用到另一个选项 `--spider`, 没错, 就是蜘蛛, 先让蜘蛛去爬一下, 看看我们要下载的文件成色如何.

```
➤ wget --spider "localhost/user.zip" -O /dev/null
Spider mode enabled. Check if remote file exists.
--2016-01-03 20:21:11--  http://localhost/user.zip
Resolving localhost (localhost)... 127.0.0.1
Connecting to localhost (localhost)|127.0.0.1|:80... connected.
HTTP request sent, awaiting response... 200 OK
Length: 3463466 (3.3M) [application/zip]
Remote file exists.

➤ 
```

上面的就是我们的蜘蛛给我们返回的内容, 比较详细呢!

在看一个错误的(404):

```
➤ wget --spider rabbit/index.html
Spider mode enabled. Check if remote file exists.
--2016-01-03 20:15:48--  http://rabbit/index.html
Resolving rabbit (rabbit)... 127.0.0.1, 127.0.1.1
Connecting to rabbit (rabbit)|127.0.0.1|:80... connected.
HTTP request sent, awaiting response... 404 Not Found
Remote file does not exist -- broken link!!!

➤ 
```

然后服务器端的日志为:

```
127.0.0.1 - - [03/Jan/2016:20:15:48 +0800] "HEAD /index.html HTTP/1.1" 404 0 "-" "Wget/1.16.1 (linux-gnu)" 
```

看到了吗, spider 实质上是向服务器发送了一个 HEAD 请求. 以此查看目标文件的信息.

这样做的用途, 作者也给了示例, 感觉最有用的还是检查书签, 检查一下你收藏栏里的网址是否还健在.

#### 8\. 设定重试次数

有时候网络不好, 下载经常重试, 所以就需要设定 retry 的次数, 可以用`–tries`这个选项来定义:

```
wget --tries=75 "http://url.com/file.tar.bz2" 
```

#### 9\. 批量下载(从文件导入下载链接)

这个功能实际上是从一个文件读取下载链接, 然后慢慢下载...

比如, 有个 url.txt 里面存放着很多下载地址, 那么就可以用 `-i`这个参数来定义:

```
wget -i url.txt 
```

在此安利一波我的杂货仓: http//ipv6.kfd.me 仅限 IPv6 地址访问.

#### 10\. 下载整个网站(爬爬爬)

```
wget --mirror -p --convert-links -P ./LOCAL-DIR WEBSITE-URL 
```

这个... 先一一介绍参数吧:

*   `--mirror` 打开'镜像储存'开关 因为我们是复制整个网站嘛
*   `-p` 下载所有相关文件 为了展示 HTML 页面所必须的文件,css,js,img 啥的
*   `–convert-links` 下载完成之后, 把原始链接转换成相对连接(根据本地环境)
*   `-P ./LOCAL-DIR` 保存到指定的目录下

这个例子实际上就是爬站... 或者说, 窃.

#### 11\. 拒绝下载某种文件

再上面的例子中, 如果你不想下载某种特定类型的文件, 就可以用`--reject`参数来指定要拒绝的文件类型, 比如`--reject=gif`就拒绝下载 gif 文件.

#### 12\. 保存下载日志

上面(第五点)我已经提过了, 在此不再赘述,

参数为 `-o` 小写.

#### 13\. 超过设定大小后自动退出

如果你想让下载的文件不超过 20M, 就可以指定 `-Q`的参数, 如果超过多少兆之后, 就退出下载.

```
wget -Q5m -i url.txt 
```

这个参数生效的条件是, url 必须都要从文件中读取.

#### 14\. 仅下载特定类型的文件

用途:

*   下载某个网站上的所有图片
*   下载某个网站上的所有视频
*   下载某个网站上的所有 PDF 文件

```
wget -r -A.pdf "http://url-to-webpage-with-pdfs.com/" 
```

`-r`表示递归下载, 默认深度为 5.

#### 15\. FTP 登陆下载

```
wget --ftp-user=USERNAME --ftp-password=PASSWORD "DOWNLOAD.com" 
```

`wget`作为一个优秀的下载软件, 功能强大到难以想象, 想知道更多? 问一下`man`吧!