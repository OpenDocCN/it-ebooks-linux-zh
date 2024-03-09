## 1.1\. 概述

在 linux 终端，面对命令不知道怎么用，或不记得命令的拼写及参数时，我们需要求助于系统的帮助文档； linux 系统内置的帮助文档很详细，通常能解决我们的问题，我们需要掌握如何正确的去使用它们；

*   在只记得部分命令关键字的场合，我们可通过 man -k 来搜索；
*   需要知道某个命令的简要说明，可以使用 whatis；而更详细的介绍，则可用 info 命令；
*   查看命令在哪个位置，我们需要使用 which；
*   而对于命令的具体参数及使用方法，我们需要用到强大的 man；

下面介绍这些命令；

## 1.2\. 命令使用

### 查看命令的简要说明

简要说明命令的作用（显示命令所处的 man 分类页面）:

```
$whatis command

```

正则匹配:

```
$whatis -w "loca*"

```

更加详细的说明文档:

```
$info command

```

### 使用 man

查询命令 command 的说明文档:

```
$man command
eg：man date

```

使用 page up 和 page down 来上下翻页

在 man 的帮助手册中，将帮助文档分为了 9 个类别，对于有的关键字可能存在多个类别中， 我们就需要指定特定的类别来查看；（一般我们查询 bash 命令，归类在 1 类中）；

man 页面所属的分类标识(常用的是分类 1 和分类 3)

```
(1)、用户可以操作的命令或者是可执行文件
(2)、系统核心可调用的函数与工具等
(3)、一些常用的函数与数据库
(4)、设备文件的说明
(5)、设置文件或者某些文件的格式
(6)、游戏
(7)、惯例与协议等。例如 Linux 标准文件系统、网络协议、ASCⅡ，码等说明内容
(8)、系统管理员可用的管理条令
(9)、与内核有关的文件

```

前面说到使用 whatis 会显示命令所在的具体的文档类别，我们学习如何使用它

```
eg:
$whatis printf
printf               (1)  - format and print data
printf               (1p)  - write formatted output
printf               (3)  - formatted output conversion
printf               (3p)  - print formatted output
printf [builtins]    (1)  - bash built-in commands, see bash(1)

```

我们看到 printf 在分类 1 和分类 3 中都有；分类 1 中的页面是命令操作及可执行文件的帮助；而 3 是常用函数库说明；如果我们想看的是 C 语言中 printf 的用法，可以指定查看分类 3 的帮助：

```
$man 3 printf

$man -k keyword

```

查询关键字 根据命令中部分关键字来查询命令，适用于只记住部分命令的场合；

eg：查找 GNOME 的 config 配置工具命令:

```
$man -k GNOME config| grep 1

```

对于某个单词搜索，可直接使用/word 来使用: /-a; 多关注下 SEE ALSO 可看到更多精彩内容

### 查看路径

查看程序的 binary 文件所在路径:

```
$which command

```

eg:查找 make 程序安装路径:

```
$which make
/opt/app/openav/soft/bin/make install

```

查看程序的搜索路径:

```
$whereis command

```

当系统中安装了同一软件的多个版本时，不确定使用的是哪个版本时，这个命令就能派上用场；

### 总结

whatis info man which whereis © 版权所有 2014, Colin http://blog.me115.com. 由 [Sphinx](http://sphinx-doc.org/) 1.3.5 创建。

## 2.1\. 创建和删除

*   创建：mkdir
*   删除：rm
*   删除非空目录：rm -rf file 目录
*   删除日志 rm *log (等价: $find ./ -name “*log” -exec rm {} ;)
*   移动：mv
*   复制：cp (复制目录：cp -r )

查看当前目录下文件个数:

```
$find ./ | wc -l

```

复制目录:

```
$cp -r source_dir  dest_dir

```

## 2.2\. 目录切换

*   找到文件/目录位置：cd
*   切换到上一个工作目录： cd -
*   切换到 home 目录： cd or cd ~
*   显示当前路径: pwd
*   更改当前工作路径为 path: $cd path

## 2.3\. 列出目录项

*   显示当前目录下的文件 ls
*   按时间排序，以列表的方式显示目录项 ls -lrt

以上这个命令用到的频率如此之高，以至于我们需要为它建立一个快捷命令方式:

在.bashrc 中设置命令别名:

```
alias lsl='ls -lrt'
alias lm='ls -al|more'

```

这样，使用 lsl，就可以显示目录中的文件按照修改时间排序；以列表方式显示；

*   给每项文件前面增加一个 id 编号(看上去更加整洁):

    ```
    >ls | cat -n

    ```

    > 1 a 2 a.out 3 app 4 b 5 bin 6 config

注：.bashrc 在/home/你的用户名/ 文件夹下，以隐藏文件的方式存储；可使用 ls -a 查看；

## 2.4\. 查找目录及文件 find/locate

搜寻文件或目录:

```
$find ./ -name "core*" | xargs file

```

查找目标文件夹中是否有 obj 文件:

```
$find ./ -name '*.o'

```

递归当前目录及子目录删除所有.o 文件:

```
$find ./ -name "*.o" -exec rm {} \;

```

find 是实时查找，如果需要更快的查询，可试试 locate；locate 会为文件系统建立索引数据库，如果有文件更新，需要定期执行更新命令来更新索引库:

```
$locate string

```

寻找包含有 string 的路径:

```
$updatedb

```

与 find 不同，locate 并不是实时查找。你需要更新数据库，以获得最新的文件索引信息。

## 2.5\. 查看文件内容

查看文件：cat vi head tail more

显示时同时显示行号:

```
$cat -n

```

按页显示列表内容:

```
$ls -al | more

```

只看前 10 行:

```
$head - 10 **

```

显示文件第一行:

```
$head -1 filename

```

显示文件倒数第五行:

```
$tail -5 filename

```

查看两个文件间的差别:

```
$diff file1 file2

```

动态显示文本最新信息:

```
$tail -f crawler.log

```

## 2.6\. 查找文件内容

使用 egrep 查询文件内容:

```
egrep '03.1\/CO\/AE' TSF_STAT_111130.log.012
egrep 'A_LMCA777:C' TSF_STAT_111130.log.035 > co.out2

```

## 2.7\. 文件与目录权限修改

*   改变文件的拥有者 chown
*   改变文件读、写、执行等属性 chmod
*   递归子目录修改： chown -R tuxapp source/
*   增加脚本可执行权限： chmod a+x myscript

## 2.8\. 给文件增加别名

创建符号链接/硬链接:

```
ln cc ccAgain :硬连接；删除一个，将仍能找到；
ln -s cc ccTo :符号链接(软链接)；删除源，另一个无法使用；（后面一个 ccTo 为新建的文件）

```

## 2.9\. 管道和重定向

*   批处理命令连接执行，使用 |
*   串联: 使用分号 ;
*   前面成功，则执行后面一条，否则，不执行:&&
*   前面失败，则后一条执行: ||

```
ls /proc && echo  suss! || echo failed.

```

能够提示命名是否执行成功 or 失败；

与上述相同效果的是:

```
if ls /proc; then echo suss; else echo fail; fi

```

重定向:

```
ls  proc/*.c > list 2> &l 将标准输出和标准错误重定向到同一文件；

```

等价的是:

```
ls  proc/*.c &> list

```

清空文件:

```
:> a.txt

```

重定向:

```
echo aa >> a.txt

```

## 2.10\. 设置环境变量

启动帐号后自动执行的是 文件为 .profile，然后通过这个文件可设置自己的环境变量；

安装的软件路径一般需要加入到 path 中:

```
PATH=$APPDIR:/opt/app/soft/bin:$PATH:/usr/local/bin:$TUXDIR/bin:$ORACLE_HOME/bin;export PATH

```

## 2.11\. Bash 快捷输入或删除

快捷键:

```
Ctl-U   删除光标到行首的所有字符,在某些设置下,删除全行
Ctl-W   删除当前光标到前边的最近一个空格之间的字符
Ctl-H   backspace,删除光标前边的字符
Ctl-R   匹配最相近的一个文件，然后输出

```

## 2.12\. 综合应用

查找 record.log 中包含 AAA，但不包含 BBB 的记录的总数:

```
cat -v record.log | grep AAA | grep -v BBB | wc -l

```

## 2.13\. 总结

文件管理，目录的创建、删除、查询、管理: mkdir rm mv

文件的查询和检索: find locate

查看文件内容：cat vi tail more

管道和重定向: ; | && > © 版权所有 2014, Colin http://blog.me115.com. 由 [Sphinx](http://sphinx-doc.org/) 1.3.5 创建。

## 3.1\. find 文件查找

查找 txt 和 pdf 文件:

```
find . \( -name "*.txt" -o -name "*.pdf" \) -print

```

正则方式查找.txt 和 pdf:

```
find . -regex  ".*\(\.txt|\.pdf\)$"

```

-iregex： 忽略大小写的正则

否定参数 ,查找所有非 txt 文本:

```
find . ! -name "*.txt" -print

```

指定搜索深度,打印出当前目录的文件（深度为 1）:

```
find . -maxdepth 1 -type f

```

### 定制搜索

*   按类型搜索

```
find . -type d -print  //只列出所有目录

```

-type f 文件 / l 符号链接 / d 目录

find 支持的文件检索类型可以区分普通文件和符号链接、目录等，但是二进制文件和文本文件无法直接通过 find 的类型区分出来；

file 命令可以检查文件具体类型（二进制或文本）:

```
$file redis-cli  # 二进制文件
redis-cli: ELF 64-bit LSB executable, x86-64, version 1 (SYSV), dynamically linked (uses shared libs), for GNU/Linux 2.6.9, not stripped
$file redis.pid  # 文本文件
redis.pid: ASCII text

```

所以,可以用以下命令组合来实现查找本地目录下的所有二进制文件:

```
ls -lrt | awk '{print $9}'|xargs file|grep  ELF| awk '{print $1}'|tr -d ':'

```

*   按时间搜索

    *   -atime 访问时间 (单位是天，分钟单位则是-amin，以下类似）
    *   -mtime 修改时间 （内容被修改）
    *   -ctime 变化时间 （元数据或权限变化）

最近第 7 天被访问过的所有文件:

```
find . -atime 7 -type f -print

```

最近 7 天内被访问过的所有文件:

```
find . -atime -7 -type f -print

```

查询 7 天前被访问过的所有文件:

```
find . -atime +7 type f -print

```

*   按大小搜索：

w 字 k M G 寻找大于 2k 的文件:

```
find . -type f -size +2k

```

按权限查找:

```
find . -type f -perm 644 -print //找具有可执行权限的所有文件

```

按用户查找:

```
find . -type f -user weber -print// 找用户 weber 所拥有的文件

```

### 找到后的后续动作

*   删除

删除当前目录下所有的 swp 文件:

```
find . -type f -name "*.swp" -delete

```

另一种语法:

```
find . type f -name "*.swp" | xargs rm

```

*   执行动作（强大的 exec）

将当前目录下的所有权变更为 weber:

```
find . -type f -user root -exec chown weber {} \;

```

注：{}是一个特殊的字符串，对于每一个匹配的文件，{}会被替换成相应的文件名；

将找到的文件全都 copy 到另一个目录:

```
find . -type f -mtime +10 -name "*.txt" -exec cp {} OLD \;

```

*   结合多个命令

如果需要后续执行多个命令，可以将多个命令写成一个脚本。然后 -exec 调用时执行脚本即可:

```
-exec ./commands.sh {} \;

```

### -print 的定界符

默认使用’\n’作为文件的定界符；

-print0 使用’\0’作为文件的定界符，这样就可以搜索包含空格的文件；

## 3.2\. grep 文本搜索

```
grep match_patten file // 默认访问匹配行

```

常用参数

*   -o 只输出匹配的文本行 **VS** -v 只输出没有匹配的文本行

*   -c 统计文件中包含文本的次数

    grep -c “text” filename

*   -n 打印匹配的行号

*   -i 搜索时忽略大小写

*   -l 只打印文件名

在多级目录中对文本递归搜索(程序员搜代码的最爱）:

```
grep "class" . -R -n

```

匹配多个模式:

```
grep -e "class" -e "vitural" file

```

grep 输出以 0 作为结尾符的文件名（-z）:

```
grep "test" file* -lZ| xargs -0 rm

```

综合应用：将日志中的所有带 where 条件的 sql 查找查找出来:

```
cat LOG.* | tr a-z A-Z | grep "FROM " | grep "WHERE" > b

```

查找中文示例：工程目录中 utf-8 格式和 gb2312 格式两种文件，要查找字的是中文；

1.  查找到它的 utf-8 编码和 gb2312 编码分别是 E4B8ADE69687 和 D6D0CEC4

2.  查询:

    ```
    grep：grep -rnP "\xE4\xB8\xAD\xE6\x96\x87|\xD6\xD0\xCE\xC4" *即可

    ```

汉字编码查询：[`bm.kdd.cc/`](http://bm.kdd.cc/)

## 3.3\. xargs 命令行参数转换

xargs 能够将输入数据转化为特定命令的命令行参数；这样，可以配合很多命令来组合使用。比如 grep，比如 find； - 将多行输出转化为单行输出

```
cat file.txt| xargs

```

n 是多行文本间的定界符

*   将单行转化为多行输出

```
cat single.txt | xargs -n 3

```

-n：指定每行显示的字段数

xargs 参数说明

*   -d 定义定界符 （默认为空格 多行的定界符为 n）
*   -n 指定输出为多行
*   -I {} 指定替换字符串，这个字符串在 xargs 扩展时会被替换掉,用于待执行的命令需要多个参数时
*   -0：指定 0 为输入定界符

示例:

```
cat file.txt | xargs -I {} ./command.sh -p {} -1

#统计程序行数
find source_dir/ -type f -name "*.cpp" -print0 |xargs -0 wc -l

#redis 通过 string 存储数据，通过 set 存储索引，需要通过索引来查询出所有的值：
./redis-cli smembers $1  | awk '{print $1}'|xargs -I {} ./redis-cli get {}

```

## 3.4\. sort 排序

字段说明

*   -n 按数字进行排序 VS -d 按字典序进行排序
*   -r 逆序排序
*   -k N 指定按第 N 列排序

示例:

```
sort -nrk 1 data.txt
sort -bd data // 忽略像空格之类的前导空白字符

```

## 3.5\. uniq 消除重复行

*   消除重复行

```
sort unsort.txt | uniq

```

*   统计各行在文件中出现的次数

```
sort unsort.txt | uniq -c

```

*   找出重复行

```
sort unsort.txt | uniq -d

```

可指定每行中需要比较的重复内容：-s 开始位置 -w 比较字符数

## 3.6\. 用 tr 进行转换

*   通用用法

```
echo 12345 | tr '0-9' '9876543210' //加解密转换，替换对应字符
cat text| tr '\t' ' '  //制表符转空格

```

*   tr 删除字符

```
cat file | tr -d '0-9' // 删除所有数字

```

-c 求补集

```
cat file | tr -c '0-9' //获取文件中所有数字
cat file | tr -d -c '0-9 \n'  //删除非数字数据

```

*   tr 压缩字符

tr -s 压缩文本中出现的重复字符；最常用于压缩多余的空格:

```
cat file | tr -s ' '

```

*   字符类

tr 中可用各种字符类：

*   alnum：字母和数字
*   alpha：字母
*   digit：数字
*   space：空白字符
*   lower：小写
*   upper：大写
*   cntrl：控制（非可打印）字符
*   print：可打印字符

使用方法：tr [:class:] [:class:]

```
tr '[:lower:]' '[:upper:]'

```

## 3.7\. cut 按列切分文本

*   截取文件的第 2 列和第 4 列

```
cut -f2,4 filename

```

*   去文件除第 3 列的所有列

```
cut -f3 --complement filename

```

*   -d 指定定界符

```
cat -f2 -d";" filename

```

*   cut 取的范围

    *   N- 第 N 个字段到结尾
    *   -M 第 1 个字段为 M
    *   N-M N 到 M 个字段

*   cut 取的单位

    *   -b 以字节为单位
    *   -c 以字符为单位
    *   -f 以字段为单位（使用定界符）

示例:

```
cut -c1-5 file //打印第一到 5 个字符
cut -c-2 file  //打印前 2 个字符

```

截取文本的第 5 到第 7 列

```
$echo string | cut -c5-7

```

## 3.8\. paste 按列拼接文本

将两个文本按列拼接到一起;

```
cat file1
1
2

cat file2
colin
book

paste file1 file2
1 colin
2 book

```

默认的定界符是制表符，可以用-d 指明定界符:

```
paste file1 file2 -d ","
1,colin
2,book

```

## 3.9\. wc 统计行和字符的工具

```
$wc -l file // 统计行数

$wc -w file // 统计单词数

$wc -c file // 统计字符数

```

## 3.10\. sed 文本替换利器

*   首处替换

```
sed 's/text/replace_text/' file   //替换每一行的第一处匹配的 text

```

*   全局替换

```
sed 's/text/replace_text/g' file

```

默认替换后，输出替换后的内容，如果需要直接替换原文件,使用-i:

```
sed -i 's/text/repalce_text/g' file

```

*   移除空白行

```
sed '/^$/d' file

```

*   变量转换

已匹配的字符串通过标记&来引用.

```
echo this is en example | sed 's/\w+/[&]/g'
$>[this]  [is] [en] [example]

```

*   子串匹配标记

第一个匹配的括号内容使用标记 1 来引用

```
sed 's/hello\([0-9]\)/\1/'

```

*   双引号求值

sed 通常用单引号来引用；也可使用双引号，使用双引号后，双引号会对表达式求值:

```
sed 's/$var/HLLOE/'

```

当使用双引号时，我们可以在 sed 样式和替换字符串中指定变量；

```
eg:
p=patten
r=replaced
echo "line con a patten" | sed "s/$p/$r/g"
$>line con a replaced

```

*   其它示例

字符串插入字符：将文本中每行内容（ABCDEF） 转换为 ABC/DEF:

```
sed 's/^.\{3\}/&\//g' file

```

## 3.11\. awk 数据流处理工具

*   awk 脚本结构

```
awk ' BEGIN{ statements } statements2 END{ statements } '

```

*   工作方式

1.执行 begin 中语句块；

2.从文件或 stdin 中读入一行，然后执行 statements2，重复这个过程，直到文件全部被读取完毕；

3.执行 end 语句块；

### print 打印当前行

*   使用不带参数的 print 时，会打印当前行

```
echo -e "line1\nline2" | awk 'BEGIN{print "start"} {print } END{ print "End" }'

```

*   print 以逗号分割时，参数以空格定界;

```
echo | awk ' {var1 = "v1" ; var2 = "V2"; var3="v3"; \
print var1, var2 , var3; }'
$>v1 V2 v3

```

*   使用-拼接符的方式（”“作为拼接符）;

```
echo | awk ' {var1 = "v1" ; var2 = "V2"; var3="v3"; \
print var1"-"var2"-"var3; }'
$>v1-V2-v3

```

### 特殊变量： NR NF $0 $1 $2

NR:表示记录数量，在执行过程中对应当前行号；

NF:表示字段数量，在执行过程总对应当前行的字段数；

$0:这个变量包含执行过程中当前行的文本内容；

$1:第一个字段的文本内容；

$2:第二个字段的文本内容；

```
echo -e "line1 f2 f3\n line2 \n line 3" | awk '{print NR":"$0"-"$1"-"$2}'

```

*   打印每一行的第二和第三个字段

```
awk '{print $2, $3}' file

```

*   统计文件的行数

```
awk ' END {print NR}' file

```

*   累加每一行的第一个字段

```
echo -e "1\n 2\n 3\n 4\n" | awk 'BEGIN{num = 0 ;
print "begin";} {sum += $1;} END {print "=="; print sum }'

```

### 传递外部变量

```
var=1000
echo | awk '{print vara}' vara=$var #  输入来自 stdin
awk '{print vara}' vara=$var file # 输入来自文件

```

### 用样式对 awk 处理的行进行过滤

```
awk 'NR < 5' #行号小于 5
awk 'NR==1,NR==4 {print}' file #行号等于 1 和 4 的打印出来
awk '/linux/' #包含 linux 文本的行（可以用正则表达式来指定，超级强大）
awk '!/linux/' #不包含 linux 文本的行

```

### 设置定界符

使用-F 来设置定界符（默认为空格）:

```
awk -F: '{print $NF}' /etc/passwd

```

### 读取命令输出

使用 getline，将外部 shell 命令的输出读入到变量 cmdout 中:

```
echo | awk '{"grep root /etc/passwd" | getline cmdout; print cmdout }'

```

### 在 awk 中使用循环

```
for(i=0;i<10;i++){print $i;}
for(i in array){print array[i];}

```

eg:以下字符串，打印出其中的时间串:

```
2015_04_02 20:20:08: mysqli connect failed, please check connect info
$echo '2015_04_02 20:20:08: mysqli connect failed, please check connect info'|awk -F ":" '{ for(i=1;i<=;i++) printf("%s:",$i)}'
>2015_04_02 20:20:08:  # 这种方式会将最后一个冒号打印出来
$echo '2015_04_02 20:20:08: mysqli connect failed, please check connect info'|awk -F':' '{print $1 ":" $2 ":" $3; }'
>2015_04_02 20:20:08   # 这种方式满足需求

```

而如果需要将后面的部分也打印出来(时间部分和后文分开打印):

```
$echo '2015_04_02 20:20:08: mysqli connect failed, please check connect info'|awk -F':' '{print $1 ":" $2 ":" $3; print $4;}'
>2015_04_02 20:20:08
>mysqli connect failed, please check connect info

```

以逆序的形式打印行：(tac 命令的实现）:

```
seq 9| \
awk '{lifo[NR] = $0; lno=NR} \
END{ for(;lno>-1;lno--){print lifo[lno];}
} '

```

### awk 结合 grep 找到指定的服务，然后将其 kill 掉

```
ps -fe| grep msv8 | grep -v MFORWARD | awk '{print $2}' | xargs kill -9;

```

### awk 实现 head、tail 命令

*   head

```
awk 'NR<=10{print}' filename

```

*   tail

```
awk '{buffer[NR%10] = $0;} END{for(i=0;i<11;i++){ \
print buffer[i %10]} } ' filename

```

### 打印指定列

*   awk 方式实现

```
ls -lrt | awk '{print $6}'

```

*   cut 方式实现

```
ls -lrt | cut -f6

```

### 打印指定文本区域

*   确定行号

```
seq 100| awk 'NR==4,NR==6{print}'

```

*   确定文本

打印处于 start_pattern 和 end_pattern 之间的文本:

```
awk '/start_pattern/, /end_pattern/' filename

```

示例:

```
seq 100 | awk '/13/,/15/'
cat /etc/passwd| awk '/mai.*mail/,/news.*news/'

```

### awk 常用内建函数

index(string,search_string):返回 search_string 在 string 中出现的位置

sub(regex,replacement_str,string):将正则匹配到的第一处内容替换为 replacement_str;

match(regex,string):检查正则表达式是否能够匹配字符串；

length(string)：返回字符串长度

```
echo | awk '{"grep root /etc/passwd" | getline cmdout; print length(cmdout) }'

```

printf 类似 c 语言中的 printf，对输出进行格式化:

```
seq 10 | awk '{printf "->%4s\n", $1}'

```

## 3.12\. 迭代文件中的行、单词和字符

### 1\. 迭代文件中的每一行

*   while 循环法

```
while read line;
do
echo $line;
done < file.txt

改成子 shell:
cat file.txt | (while read line;do echo $line;done)

```

*   awk 法

```
cat file.txt| awk '{print}'

```

### 2.迭代一行中的每一个单词

```
for word in $line;
do
echo $word;
done

```

### 3\. 迭代每一个字符

${string:start_pos:num_of_chars}：从字符串中提取一个字符；(bash 文本切片）

${#word}:返回变量 word 的长度

```
for((i=0;i<${#word};i++))
do
echo ${word:i:1);
done

```

以 ASCII 字符显示文件:

```
$od -c filename

``` © 版权所有 2014, Colin http://blog.me115.com. 由 [Sphinx](http://sphinx-doc.org/) 1.3.5 创建。

## 4.1\. 查看磁盘空间

查看磁盘空间利用大小:

```
df -h

```

-h: human 缩写，以易读的方式显示结果（即带单位：比如 M/G，如果不加这个参数，显示的数字以 B 为单位）

```
$df -h
/opt/app/todeav/config#df -h
Filesystem            Size  Used Avail Use% Mounted on
/dev/mapper/VolGroup00-LogVol00
2.0G  711M  1.2G  38% /
/dev/mapper/vg1-lv2    20G  3.8G   15G  21% /opt/applog
/dev/mapper/vg1-lv1    20G   13G  5.6G  70% /opt/app

```

查看当前目录所占空间大小:

```
du -sh

```

*   -h 人性化显示
*   -s 递归整个目录的大小

```
$du -sh
653M

```

查看当前目录下所有子文件夹排序后的大小:

```
for i in `ls`; do du -sh $i; done | sort
或者：
du -sh `ls` | sort

```

## 4.2\. 打包/ 压缩

在 linux 中打包和压缩和分两步来实现的；

**打包**

打包是将多个文件归并到一个文件:

```
tar -cvf etc.tar /etc <==仅打包，不压缩！

```

*   -c :打包选项
*   -v :显示打包进度
*   -f :使用档案文件

注：有的系统中指定参数时不需要在前面加上-，直接使用 tar xvf

示例：用 tar 实现文件夹同步，排除部分文件不同步:

```
tar --exclude '*.svn' -cvf - /path/to/source | ( cd /path/to/target; tar -xf -)

```

**压缩**

```
$gzip demo.txt

```

生成 demo.txt.gz

## 4.3\. 解包/解压缩

**解包**

```
tar -xvf demo.tar

```

-x 解包选项

解压后缀为 .tar.gz 的文件 1\. 先解压缩，生成**.tar:

```
$gunzip    demo.tar.gz

```

2.  解包:

    ```
    $tar -xvf  demo.tar
    $bzip2 -d demo.tar.bz2

    ```

bz2 解压:

```
tar jxvf demo.tar.bz2

```

如果 tar 不支持 j，则同样需要分两步来解包解压缩，使用 bzip2 来解压，再使用 tar 解包:

```
bzip2 -d  demo.tar.bz2
tar -xvf  demo.tar

```

-d decompose,解压缩

tar 解压参数说明：

*   -z 解压 gz 文件
*   -j 解压 bz2 文件
*   -J 解压 xz 文件

## 4.4\. 总结

查看磁盘空间 df -h

查看目录大小 du -sh

打包 tar -cvf

解包 tar -xvf

压缩 gzip

解压缩 gunzip bzip © 版权所有 2014, Colin http://blog.me115.com. 由 [Sphinx](http://sphinx-doc.org/) 1.3.5 创建。

## 5.1\. 查询进程

查询正在运行的进程信息

```
$ps -ef

```

eg:查询归属于用户 colin115 的进程

```
$ps -ef | grep colin115
$ps -lu colin115

```

查询进程 ID（适合只记得部分进程字段）

```
$pgrep 查找进程

eg:查询进程名中含有 re 的进程
[/home/weber#]pgrep -l re
2 kthreadd
28 ecryptfs-kthrea
29515 redis-server

```

以完整的格式显示所有的进程

```
$ps -ajx

```

显示进程信息，并实时更新

```
$top

```

查看端口占用的进程状态：

```
lsof -i:3306

```

查看用户 username 的进程所打开的文件

```
$lsof -u username

```

查询 init 进程当前打开的文件

```
$lsof -c init

```

查询指定的进程 ID(23295)打开的文件：

```
$lsof -p 23295

```

查询指定目录下被进程开启的文件（使用+D 递归目录）：

```
$lsof +d mydir1/

```

## 5.2\. 终止进程

杀死指定 PID 的进程 (PID 为 Process ID)

```
$kill PID

```

杀死相关进程

```
kill -9 3434

```

杀死 job 工作 (job 为 job number)

```
$kill %job

```

## 5.3\. 进程监控

查看系统中使用 CPU、使用内存最多的进程；

```
$top
(->)P

```

输入 top 命令后，进入到交互界面；接着输入字符命令后显示相应的进程状态：

对于进程，平时我们最常想知道的就是哪些进程占用 CPU 最多，占用内存最多。以下两个命令就可以满足要求:

```
P：根据 CPU 使用百分比大小进行排序。
M：根据驻留内存大小进行排序。
i：使 top 不显示任何闲置或者僵死进程。

```

这里介绍最使用的几个选项,对于更详细的使用，详见 top linux 下的任务管理器 ;

## 5.4\. 分析线程栈

使用命令 pmap，来输出进程内存的状况，可以用来分析线程堆栈；

```
$pmap PID

eg:
[/home/weber#]ps -fe| grep redis
weber    13508 13070  0 08:14 pts/0    00:00:00 grep --color=auto redis
weber    29515     1  0  2013 ?        02:55:59 ./redis-server redis.conf
[/home/weber#]pmap 29515
29515:   ./redis-server redis.conf
08048000    768K r-x--  /home/weber/soft/redis-2.6.16/src/redis-server
08108000      4K r----  /home/weber/soft/redis-2.6.16/src/redis-server
08109000     12K rw---  /home/weber/soft/redis-2.6.16/src/redis-server

```

## 5.5\. 综合运用

将用户 colin115 下的所有进程名以 av_ 开头的进程终止:

```
ps -u colin115 |  awk '/av_/ {print "kill -9 " $1}' | sh

```

将用户 colin115 下所有进程名中包含 HOST 的进程终止:

```
ps -fe| grep colin115|grep HOST |awk '{print $2}' | xargs kill -9;

```

## 5.6\. 总结

ps top lsof kill pmap © 版权所有 2014, Colin http://blog.me115.com. 由 [Sphinx](http://sphinx-doc.org/) 1.3.5 创建。

## 6.1\. 监控 CPU

查看 CPU 使用率

```
$sar -u

eg:
$sar -u 1 2
[/home/weber#]sar -u 1 2
Linux 2.6.35-22-generic-pae (MyVPS)     06/28/2014      _i686_  (1 CPU)

09:03:59 AM     CPU     %user     %nice   %system   %iowait    %steal     %idle
09:04:00 AM     all      0.00      0.00      0.50      0.00      0.00     99.50
09:04:01 AM     all      0.00      0.00      0.00      0.00      0.00    100.00

```

后面的两个参数表示监控的频率，比如例子中的 1 和 2，表示每秒采样一次，总共采样 2 次；

查看 CPU 平均负载

```
$sar -q 1 2

```

sar 指定-q 后，就能查看运行队列中的进程数、系统上的进程大小、平均负载等；

## 6.2\. 查询内存

查看内存使用状况 sar 指定-r 之后，可查看内存使用状况;

```
$sar -r 1 2
09:08:48 AM kbmemfree kbmemused  %memused kbbuffers  kbcached  kbcommit   %commit  kbactive   kbinact
09:08:49 AM     17888    359784     95.26     37796     73272    507004     65.42    137400    150764
09:08:50 AM     17888    359784     95.26     37796     73272    507004     65.42    137400    150764
Average:        17888    359784     95.26     37796     73272    507004     65.42    137400    150764

```

查看内存使用量

```
$free -m

```

## 6.3\. 查询页面交换

查看页面交换发生状况 页面发生交换时，服务器的吞吐量会大幅下降；服务器状况不良时，如果怀疑因为内存不足而导致了页面交换的发生，可以使用 sar -W 这个命令来确认是否发生了大量的交换；

```
$sar -W 1 3

```

## 6.4\. 查询硬盘使用

查看磁盘空间利用情况

```
$df -h

```

查询当前目录下空间使用情况

```
du -sh  -h 是人性化显示 s 是递归整个目录的大小

```

查看该目录下所有文件夹的排序后的大小

```
for i in `ls`; do du -sh $i; done | sort
或者
du -sh `ls`

```

## 6.5\. 综合应用

当系统中 sar 不可用时，可以使用以下工具替代：linux 下有 vmstat、Unix 系统有 prstat

eg： 查看 cpu、内存、使用情况： vmstat n m （n 为监控频率、m 为监控次数）

```
[/home/weber#]vmstat 1 3
procs -----------memory---------- ---swap-- -----io---- -system-- ----cpu----
r  b   swpd   free   buff  cache   si   so    bi    bo   in   cs us sy id wa
0  0  86560  42300   9752  63556    0    1     1     1    0    0  0  0 99  0
1  0  86560  39936   9764  63544    0    0     0    52   66   95  5  0 95  0
0  0  86560  42168   9772  63556    0    0     0    20  127  231 13  2 84  0

```

使用 watch 工具监控变化 当需要持续的监控应用的某个数据变化时，watch 工具能满足要求； 执行 watch 命令后，会进入到一个界面，输出当前被监控的数据，一旦数据变化，便会高亮显示变化情况；

eg：操作 redis 时，监控内存变化：

```
$watch -d -n 1 './redis-cli info | grep memory'
(以下为 watch 工具中的界面内容，一旦内存变化，即实时高亮显示变化）
Every 1.0s: ./redis-cli info | grep memory                                                                  Mon Apr 28 16:10:36 2014

used_memory:45157376
used_memory_human:43.07M
used_memory_rss:47628288
used_memory_peak:49686080
used_memory_peak_human:47.38M

```

## 6.6\. 总结

top / sar / free / watch

## 6.7\. 附录

关于 sar 的使用详解参考：sar 找出系统瓶颈的利器 © 版权所有 2014, Colin http://blog.me115.com. 由 [Sphinx](http://sphinx-doc.org/) 1.3.5 创建。

## 7.1\. 查询网络服务和端口

netstat 命令用于显示各种网络相关信息，如网络连接，路由表，接口状态 (Interface Statistics)，masquerade 连接，多播成员 (Multicast Memberships) 等等。

列出所有端口 (包括监听和未监听的):

```
netstat -a

```

列出所有 tcp 端口:

```
netstat -at

```

列出所有有监听的服务状态:

```
netstat -l

```

使用 netstat 工具查询端口:

```
$netstat -antp | grep 6379
tcp        0      0 127.0.0.1:6379          0.0.0.0:*               LISTEN      25501/redis-server

$ps 25501
  PID TTY      STAT   TIME COMMAND
25501 ?        Ssl   28:21 ./redis-server ./redis.conf

```

lsof（list open files）是一个列出当前系统打开文件的工具。在 linux 环境下，任何事物都以文件的形式存在，通过文件不仅仅可以访问常规数据，还可以访问网络连接和硬件。所以如传输控制协议 (TCP) 和用户数据报协议 (UDP) 套接字等； 在查询网络端口时，经常会用到这个工具。

查询 7902 端口现在运行什么程序:

```
#分为两步
#第一步，查询使用该端口的进程的 PID；
    $lsof -i:7902
    COMMAND   PID   USER   FD   TYPE    DEVICE SIZE NODE NAME
    WSL     30294 tuapp    4u  IPv4 447684086       TCP 10.6.50.37:tnos-dp (LISTEN)

#查到 30294
#使用 ps 工具查询进程详情：
$ps -fe | grep 30294
tdev5  30294 26160  0 Sep10 ?        01:10:50 tdesl -k 43476
root     22781 22698  0 00:54 pts/20   00:00:00 grep 11554

```

注解

以上介绍 lsof 关于网络方面的应用，这个工具非常强大，需要好好掌握，详见 lsof 一切皆文件 ;

## 7.2\. 网络路由

查看路由状态:

```
$route -n

```

发送 ping 包到地址 IP:

```
$ping IP

```

探测前往地址 IP 的路由路径:

```
$traceroute IP

```

DNS 查询，寻找域名 domain 对应的 IP:

```
$host domain

```

反向 DNS 查询:

```
$host IP

```

## 7.3\. 镜像下载

直接下载文件或者网页:

```
wget url

```

常用选项:

*   –limit-rate :下载限速
*   -o：指定日志文件；输出都写入日志；
*   -c：断点续传

## 7.4\. ftp sftp lftp ssh

SSH 登陆:

```
$ssh ID@host

```

ssh 登陆远程服务器 host，ID 为用户名。

ftp/sftp 文件传输:

```
$sftp ID@host

```

登陆服务器 host，ID 为用户名。sftp 登陆后，可以使用下面的命令进一步操作：

*   get filename # 下载文件
*   put filename # 上传文件
*   ls # 列出 host 上当前路径的所有文件
*   cd # 在 host 上更改当前路径
*   lls # 列出本地主机上当前路径的所有文件
*   lcd # 在本地主机更改当前路径

lftp 同步文件夹(类似 rsync 工具):

```
lftp -u user:pass host
lftp user@host:~> mirror -n

```

## 7.5\. 网络复制

将本地 localpath 指向的文件上传到远程主机的 path 路径:

```
$scp localpath ID@host:path

```

以 ssh 协议，遍历下载 path 路径下的整个文件系统，到本地的 localpath:

```
$scp -r ID@site:path localpath

```

## 7.6\. 总结

netstat lsof route ping host wget sftp scp © 版权所有 2014, Colin http://blog.me115.com. 由 [Sphinx](http://sphinx-doc.org/) 1.3.5 创建。

## 8.1\. 用户

### 添加用户

```
$useradd -m username

```

该命令为用户创建相应的帐号和用户目录/home/username；

用户添加之后，设置密码：

密码以交互方式创建:

```
$passwd username

```

### 删除用户

```
$userdel -r username

```

不带选项使用 userdel，只会删除用户。用户的家目录将仍会在/home 目录下。要完全的删除用户信息，使用-r 选项；

帐号切换 登录帐号为 userA 用户状态下，切换到 userB 用户帐号工作:

```
$su userB

```

进入交互模型，输入密码授权进入；

## 8.2\. 用户的组

### 将用户加入到组

默认情况下，添加用户操作也会相应的增加一个同名的组，用户属于同名组； 查看当前用户所属的组:

```
$groups

```

一个用户可以属于多个组，将用户加入到组:

```
$usermod -G groupNmame username

```

变更用户所属的根组(将用加入到新的组，并从原有的组中除去）:

```
$usermod -g groupName username

```

### 查看系统所有组

系统的所有用户及所有组信息分别记录在两个文件中：/etc/passwd , /etc/group 默认情况下这两个文件对所有用户可读：

查看所有用户及权限:

```
$more /etc/passwd

```

查看所有的用户组及权限:

```
$more /etc/group

```

## 8.3\. 用户权限

使用 ls -l 可查看文件的属性字段，文件属性字段总共有 10 个字母组成，第一个字母表示文件类型，如果这个字母是一个减号”-”,则说明该文件是一个普通文件。字母”d”表示该文件是一个目录，字母”d”,是 dirtectory(目录)的缩写。 后面的 9 个字母为该文件的权限标识，3 个为一组，分别表示文件所属用户、用户所在组、其它用户的读写和执行权限； 例如:

```
[/home/weber#]ls -l /etc/group
-rwxrw-r-- colin king 725 2013-11-12 15:37 /home/colin/a

```

表示这个文件对文件拥有者 colin 这个用户可读写、可执行；对 colin 所在的组（king）可读可写；对其它用户只可读；

### 更改读写权限

使用 chmod 命令更改文件的读写权限，更改读写权限有两种方法，一种是字母方式，一种是数字方式

字母方式:

```
$chown userMark(+|-)PermissionsMark

```

userMark 取值：

*   u：用户
*   g：组
*   o：其它用户
*   a：所有用户

PermissionsMark 取值：

*   r:读
*   w：写
*   x：执行

例如:

```
$chmod a+x main         对所有用户给文件 main 增加可执行权限
$chmod g+w blogs        对组用户给文件 blogs 增加可写权限

```

数字方式：

数字方式直接设置所有权限，相比字母方式，更加简洁方便；

使用三位八进制数字的形式来表示权限，第一位指定属主的权限，第二位指定组权限，第三位指定其他用户的权限，每位通过 4(读)、2(写)、1(执行)三种数值的和来确定权限。如 6(4+2)代表有读写权，7(4+2+1)有读、写和执行的权限。

例如:

```
$chmod 740 main     将 main 的用户权限设置为 rwxr-----

```

### 更改文件或目录的拥有者

```
$chown username dirOrFile

```

使用-R 选项递归更改该目下所有文件的拥有者:

```
$chown -R weber server/

```

## 8.4\. 环境变量

bashrc 与 profile 都用于保存用户的环境信息，bashrc 用于交互式 non-loginshell，而 profile 用于交互式 login shell。

/etc/profile，/etc/bashrc 是系统全局环境变量设定~/.profile，~/.bashrc 用户目录下的私有环境变量设定

当登入系统获得一个 shell 进程时，其读取环境设置脚本分为三步:

1.  首先读入的是全局环境变量设置文件/etc/profile，然后根据其内容读取额外的文档，如/etc/profile.d 和/etc/inputrc
2.  读取当前登录用户 Home 目录下的文件~/.bash_profile，其次读取~/.bash_login，最后读取~/.profile，这三个文档设定基本上是一样的，读取有优先关系
3.  读取~/.bashrc

~/.profile 与~/.bashrc 的区别:

*   这两者都具有个性化定制功能
*   ~/.profile 可以设定本用户专有的路径，环境变量，等，它只能登入的时候执行一次
*   ~/.bashrc 也是某用户专有设定文档，可以设定路径，命令别名，每次 shell script 的执行都会使用它一次

例如，我们可以在这些环境变量中设置自己经常进入的文件路径，以及命令的快捷方式：

```
.bashrc
alias m='more'
alias cp='cp -i'
alias mv='mv -i'
alias ll='ls -l'
alias lsl='ls -lrt'
alias lm='ls -al|more'

log=/opt/applog/common_dir
unit=/opt/app/unittest/common

.bash_profile
. /opt/app/tuxapp/openav/config/setenv.prod.sh.linux
export PS1='$PWD#'

```

通过上述设置，我们进入 log 目录就只需要输入 cd $log 即可；

## 8.5\. 总结

useradd passwd userdel usermod chmod chown .bashrc .bash_profile © 版权所有 2014, Colin http://blog.me115.com. 由 [Sphinx](http://sphinx-doc.org/) 1.3.5 创建。

## 9.1\. 系统管理

### 查询系统版本

查看 Linux 系统版本:

```
$uname -a
$lsb_release -a

```

查看 Unix 系统版本：操作系统版本:

```
$more /etc/release

```

### 查询硬件信息

查看 CPU 使用情况:

```
$sar -u 5 10

```

查询 CPU 信息:

```
$cat /proc/cpuinfo

```

查看 CPU 的核的个数:

```
$cat /proc/cpuinfo | grep processor | wc -l

```

查看内存信息:

```
$cat /proc/meminfo

```

显示内存 page 大小（以 KByte 为单位）:

```
$pagesize

```

显示架构:

```
$arch

```

### 设置系统时间

显示当前系统时间:

```
$date

```

设置系统日期和时间(格式为 2014-09-15 17:05:00):

```
$date -s 2014-09-15 17:05:00
$date -s 2014-09-15
$date -s 17:05:00

```

设置时区:

```
选择时区信息。命令为：tzselect
根据系统提示，选择相应的时区信息。

```

强制把系统时间写入 CMOS（这样，重启后时间也正确了）:

```
$clock -w

```

警告

设置系统时间需要 root 用户权限.

格式化输出当前日期时间:

```
$date +%Y%m%d.%H%M%S
>20150512.173821

```

## 9.2\. IPC 资源管理

### IPC 资源查询

查看系统使用的 IPC 资源:

```
$ipcs

------ Shared Memory Segments --------
key        shmid      owner      perms      bytes      nattch     status

------ Semaphore Arrays --------
key        semid      owner      perms      nsems
0x00000000 229376     weber      600        1

------ Message Queues --------
key        msqid      owner      perms      used-bytes   messages

```

查看系统使用的 IPC 共享内存资源:

```
$ipcs -m

```

查看系统使用的 IPC 队列资源:

```
$ipcs -q

```

查看系统使用的 IPC 信号量资源:

```
$ipcs -s

```

应用示例：查看 IPC 资源被谁占用

有个 IPCKEY：51036 ，需要查询其是否被占用；

1.  首先通过计算器将其转为十六进制:

    51036 -> c75c

2.  如果知道是被共享内存占用:

    ```
    $ipcs -m | grep c75c
    0x0000c75c 40403197   tdea3    666        536870912  2

    ```

3.  如果不确定，则直接查找:

    ```
    $ipcs | grep c75c
    0x0000c75c 40403197   tdea3    666        536870912  2
    0x0000c75c 5079070    tdea3    666        4

    ```

### 检测和设置系统资源限制

显示当前所有的系统资源 limit 信息:

```
ulimit – a

```

对生成的 core 文件的大小不进行限制:

```
ulimit – c unlimited

```

## 9.3\. 总结

uname sar arch date ipcs ulimit © 版权所有 2014, Colin http://blog.me115.com. 由 [Sphinx](http://sphinx-doc.org/) 1.3.5 创建。