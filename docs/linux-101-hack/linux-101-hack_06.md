# 第六章 - 压缩和打包

这一章介绍了压缩打包之类的命令技巧.

你会在传输大文件以及"脱裤"的时候用到的 :)

# Hack-45 Zip 命令基础

## Zip 命令基础

`zip`打包, 是最好用的了, 不仅命令简单, 压缩率也不低哦~

基础语法:

```
zip {.zip file-name} {file-names} 
```

如果要打包的文件/文件夹下还有文件夹怎么办? 递归啊!

```
zip -r var-log-dir.zip /var/log/ # -r 开关, 开启递归选项 
```

那解压呢?

也是相当的简单..

```
unzip zipfile.zip 
```

直接用`unzip`就好了!

那如果只是想看看 zip 包里面的内容而不解压呢?

加一个`-l`的参数就好了!

```
unzip -l zipfile.zip 
```

# Hack-46 Zip 命令进阶

## Zip 命令进阶

`zip`一共有 10 个压缩等级:

*   `0` 最低的等级, 只是打包而不做任何压缩
*   `1` 压缩率很低, 但是速度很快
*   `6` 默认压缩等级
*   `9` 最高等级的压缩!速度很慢, 但压缩率是最高的. 作者认为, 如果不是压缩一个很大的文件, 用最高等级是最好的选择. ( 不敢苟同啊... 我一直认为默认的是最优的 :D

下面看一下各个压缩等级有什么不同:

这是要操作的文件:

```
➤ seq 9999999 > big_file
➤ l big_file 
-rw-rw-r-- 1 mr mr 76M  1 月  5 17:04 big_file
➤ 
```

然后看一下压缩效果:

```
➤ zip 0.zip big_file 
  adding: big_file (deflated 73%)
➤ zip -0 0.zip big_file 
updating: big_file (stored 0%)
➤ zip 6.zip big_file 
  adding: big_file (deflated 73%)
➤ zip 9.zip big_file 
  adding: big_file (deflated 73%)
➤
➤ ls -l *.zip
-rw-rw-r-- 1 mr mr 78889054  1 月  5 17:05 0.zip
-rw-rw-r-- 1 mr mr 21230796  1 月  5 17:05 6.zip
-rw-rw-r-- 1 mr mr 21230796  1 月  5 17:05 9.zip 
```

#### 验证一个压缩包

有时候我们想验证一个压缩包里的文件是否完整, 以及, 这是不是我们想要的压缩包, 那你就会用到`-t`这个参数:

```
➤ unzip -t 9.zip 
Archive:  9.zip
    testing: big_file                 OK
No errors detected in compressed data of 9.zip. 
```

# Hack-47 给压缩包加个锁

## 给压缩包加个锁

我们知道`RAR`可以添加密码, 其实, `ZIP`也可以的.

命令:

```
zip -P mysecurepwd var-log-protected.zip /var/log/* 
```

如果你觉得这样明文记录密码不好的话, 还可以交互式的输入密码:

```
zip -e var-log-protected.zip /var/log/*
Enter password:
Verify password:
updating: var/log/acpid (deflated 81%)
updating: var/log/anaconda.log (deflated 79%) 
```

当然, 输入的密码是不可见的.

当你解压这个带锁的压缩包的时候就会要求你输入密码:

```
unzip
Archive:
var-log-protected.zip
var-log-protected.zip
[var-log-protected.zip] var/log/acpid password: 
```

# Hack-48 Tar 命令

## Tar 命令

除了`zip`这个简单实用的压缩工具以外, Linux 上应用最广泛的还是这个`tar`.

命令语法:

```
tar [options] [tar-archive-name] [other-file-names] 
```

常用的选项有这么几个:

*   `c` 创建一个归档文件
*   `v` 显示详细过程
*   `f` 后接要创建的文件名
*   `z` 压缩(.gz)
*   `j` 压缩(.bz2)
*   `x` 解压
*   `t` 测试(也就是查看里面都有啥, 并不解压)

#### 1.简单的创建一个归档文件(不压缩)

```
tar cvf /tmp/my_home_directory.tar /home/jsmith 
```

这里的`option`前面加不加`-`(连字符)都行, 推荐还是加上, 更美观, 也符合基本认知.

#### 2.查看某个压缩包里的内容(列出文件)

```
tar tvf /tmp/my_home_directory.tar 
```

#### 3.解压某个压缩包(提取文件)

```
tar xvf /tmp/my_home_directory.tar 
```

#### 4.解压文件到特定的目录

```
tar xvfz /tmp/my_home_directory.tar.gz –C /tmp/some_dir 
```

#### 扩展阅读

[Additional Tar Examples](http://www.thegeekstuff.com/2010/04/unix-tar-command-examples/)

# Hack-49 Tar 压缩

## Tar 压缩

`tar`可以和`gzip`, `bzip2`组合使用, 也就是边打包,边压缩.

上一篇说了打包, 这一篇就介绍压缩.

其中压缩有两个软件, 一个是`gzip`, 创建的是`.gz`的文件, 另一个是`bzip2`,创建的是`.bz2`的文件, 两者有什么区别呢? 可以简单地理解为,在相同的条件下, `.bz2`压缩率更高, 更能节省空间.

#### gzip 压缩

```
$ tar cvfz /tmp/my_home_directory.tar.gz /home/jsmith #压缩
$ tar xvfz /tmp/my_home_directory.tar.gz #解压
$ tar tvfz /tmp/my_home_directory.tar.gz 
```

还记得这些参数都是什么意思吗?

不记得就往回翻翻看~

#### bzip2 压缩

```
$ tar cvfj /tmp/my_home_directory.tar.bz2 /home/jsmith #压缩
$ tar xvfj /tmp/my_home_directory.tar.bz2 #解压
$ tar tvfj /tmp/my_home_directory.tar.bz2 
```

看出两者有何区别了么?

# Hack-50 Bz* 命令

## Bz* 命令

`bzip2`是用来压缩和解压文件的一个命令, 它最大的优点在于超高的压缩效率.

`bzip2`VS`gzip`

*   `bzip2`压缩率更高
*   `gzip`速度更快
*   在高压缩率下`bzip2`的速度更块一些(相比`gzip`)

#### 压缩某个文件

```
➤ ls -l catshadow
-rwxrwxr-x 1 mr mr 8.7K 11 月 11 15:49 catshadow*
➤ bzip2 catshadow
➤ ls -l catshadow.bz2 
-rwxrwxr-x 1 mr mr 2.8K 11 月 11 15:49 catshadow.bz2* 
```

然后你就会发现原来的文件不见惹...

#### 在压缩文件(.bz2)里面搜索 (bzgrep)

如果你压缩了某个日志文件, 想搜索里面的某条信息, 那你是不是应该先解压, 再用`grep`搜索?

然而,这条命令就把两者组合起来了:

```
bzgrep grep-options -e pattern filename 
```

我觉着这是不符合 Unix 哲学的, 把自己的事情做好就行了, 可是话又说回来, 你怎么知道我想做什么事呢?

```
➤ seq 99999 > numbers
➤ l numbers 
-rw-rw-r-- 1 mr mr 576K  1 月  5 20:28 numbers
➤ bzip2 numbers 
➤ bzgrep 233 numbers.bz2 
233
1233
2233
2330
2331
...
... 
```

直接在文件里面搜索.

#### 查看压缩文件的内容(不解压)

同样, 我们不解压也可以直接查看压缩文件的内容:

```
bzcat numbers.bz2 
```

或者:

```
bzless numbers.bz2 
```

或者:

```
bzmore numbers.bz2 
```

#### bz 找不同

还有`bzcmp` 和 `bzdiff` 这两个命令, 是用来找不同的.

```
$ cmp System.txt.001 System.txt.002
System.txt.001 System.txt.002 differ: byte 20, line 2
$ bzcmp System.txt.001.bz2 System.txt.002.bz2
- /tmp/bzdiff.csgqG32029 differ: byte 20, line 2 
```

```
$ bzdiff System.txt.001.bz2 System.txt.002.bz2
2c2
< 0: ERR: Mon Sep 27 12:19:34 2010: gs(11153/1105824064):
[chk_sqlcode.scp:92]: Database: ORA-01654: unable to
extend index OPC_OP.OPCX
_ANNO_NUM by 64 in tablespace OPC_INDEX1
---
> 0: ERR: Wed Sep 22 09:59:42 2010:
gs(11153/47752677794640): [chk_sqlcode.scp:92]: Database:
ORA-01653: unable to extend table OPC_OP.
OPC_HIST_MESSAGES by 64 in tablespace OPC_6
4,5c4
< Retry. (OpC51-22)
< Database: ORA-01654: unable to extend index
OPC_OP.OPCX_ANNO_NUM by 64 in tablespace OPC_INDEX1
---
> 0: ERR: Wed Sep 22 09:59:47 2010:
gs(11153/47752677794640): [chk_sqlcode.scp:92]: Database:
ORA-01653: unable to extend table OPC_OP.
OPC_HIST_MESSAGES by 64 in tablespace OPC_6 
```

这一部分我介绍的有点少, 是因为我本人用 bz 系列不是很多, 为了追求速度, 都是用`tar`的, 而且日常生活中也遇不到对比两个压缩文件内容的情况...

# Hack-51 Cpio 命令

## Cpio 命令

这个`cpio`我是第一次听说, 如有不妥的地方还请大家指正.

`cpio`命令是用来处理归档文件的, 这里的归档文件包括 `.cpio` , `.tar`

> cpio stands for “copy in, copy out”.

说的很明白, 复制进来, 复制出去. 果真 Linux 的软件命名都是根据内容来的, 直观易懂.

它可以干三种事:

1.  把文件复制到某个归档文件中
2.  从某个归档文件中提取文件

`cpio`从标准输入中读取文件列表, 创建一个归档文件后把这些文件都输入到里面, 最后再输出到标准输出中(或者重定向).

##### 创建 cpio 归档

```
➤ cd test/
➤ ls
cal_random.sh  catshadow.c   numbers.bz2  vpnn
catshadow.bz2  helloword.py  test.php
➤ ls | cpio -ov > test.cpio  # o-创建归档文件
cal_random.sh
catshadow.bz2
catshadow.c
helloword.py
numbers.bz2
test.php
vpnn
248 blocks
➤ ls -l test.cpio 
-rw-rw-r-- 1 mr mr 124K  1 月  5 20:46 test.cpio 
```

正如你所看到的, 把`ls` 列出的文件通过管道传递给`cpio`后, `cpio`将他们压缩, 然后我们再通过重定向,导入到了`test.cpio`文件中.

#### 提取 cpio 中的文件

接着上一个目录中的内容, 我们新建一个目录, 把文件提取出来:

```
➤ mkdir cpio
mkdir: created directory ‘cpio’
➤ cd cpio/
➤ ls
➤ cpio -idv < ../test.cpio # i-从归档文件中提取
cal_random.sh
catshadow.bz2
catshadow.c
helloword.py
numbers.bz2
test.cpio
test.php
vpnn
494 blocks
➤ ls
cal_random.sh  catshadow.c   numbers.bz2  test.php
catshadow.bz2  helloword.py  test.cpio    vpnn
➤ 
```

看到了么, `cpio`从标准输入中读取了归档文件, 然后把里面的文件提取了出来.

#### 归档特定的文件

```
➤ find . -iname *.c -print | cpio -ov >/tmp/c_files.cpio
./catshadow.c
1 block
➤ 
```

这里没啥好说的, 就是利用了`find`而已.

#### 用 cpio 创建 tar 文件

我们可以用`cpio`创建一个`.tar`类型的文件:

```
ls | cpio -ov -H tar -F sample.tar 
```

殊途同归.

怎样提取呢?

```
cpio -idv -F sample.tar 
```

用上面这个.

我们可以看到, 除了利用重定向, 我们还可以用`-F`的参数来定义所要操作的文件.

还有, 不解压查看`tar`文件里面的文件名:

```
cpio -it -F sample.tar 
```

作者还列举了几个不常用的:

1.将符号链接所指向的内容打包:

```
ls | cpio -oLv >/tmp/test.cpio 
```

2.保留文件的修改时间

```
ls | cpio -omv >/tmp/test.cpio 
```

3.拷贝文件夹

```
$ mkdir /mnt/out
$ cd objects
$ find . -depth | cpio -pmdv /mnt/out 
```

个人感觉`cpio`像是一个文件流操作器, 压缩也好, 解压也好, 复制也好, 都是以一种数据流的形式进行操作.

#### 扩展阅读

*   [Additional cpio Examples](http://www.thegeekstuff.com/2009/07/how-to-view-modify-and-recreate-initrd-img/)
*   [讨论](http://www.thegeekstuff.com/2010/08/cpio-utility/)