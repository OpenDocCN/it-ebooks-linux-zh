# 16.end

## shell 十三问之 16：学习总结与原帖目录

* * *

本人(markdown 译者)是解决工作中 shell 脚本的一个问题， 偶尔的一次机会遇到了 CU 论坛中这样一个神贴：**shell 十三问**.

shell 十三问是 CU 的 shell 版的台湾的**网中人**是 2003 年用繁体发布的。 第一次读到 shell 十三问，由于是繁体，第一感觉有点抵触， 但是还是耐着性子读完了一贴，没想到竟然读懂了， 而且还被**网中人**的幽默的写作风格，独到的思维方式， 循序渐进的认识事物的过程所折服。

尽管帖子是 10 多年前写的，今天看来也几乎没有一点过时的感觉。 从这个方面来说，shell 十三问应该 shell 的(思想)精华本质所在， 就像武功的内功心法，可能我说的点过， 但是我曾经看过一本 shell 脚本学习指南，看完后的感觉，还是有感念很朦胧， 而 shell 十三问是我最容易理解和接受的，这也是我整理的 Markdown 版本初衷。 为什么不让好东西让更多的人熟知呢，恰好年前项目管理开始迁移到 git 上， 在 git 上认识一个好东西 Markdown，用它可以很简单地整理出条例清晰篇章。 在年假的时候，觉得这个假期该做点什么， 毕竟马总都说了，改变世界，不如改变自己。

本人整理的 [简体中文 Markdown 版本的 shell 十三问][shell-markdown] 的链接地址： [`github.com/wzb56/13_questions_of_shell`](https://github.com/wzb56/13_questions_of_shell)

网中人的 CU 原帖[shell 十三问](http://bbs.chinaunix.net/thread-218853-1-1.html)地址：[`bbs.chinaunix.net/thread-218853-1-1.html`](http://bbs.chinaunix.net/thread-218853-1-1.html)

我简单将原文整理如下：

我在 CU 的日子并不长，有幸在 shell 版上与大家结缘。 除了跟前辈学习到不少技巧之外，也常看到不少朋友的问题。 然而，在众多问题中，我发现许多瓶颈都源于 shell 的基础而已。 每次要解说，却总有千言万语不知从何而起之感......

这次，我不是来回答，而是准备了关于 shell 基础的十三个问题要问大家。 希望的 shell 的学习者们能够通过寻找答案的过程，好好的将 shell 基础打扎实一点。

当然了，这些问题我也会逐一解说一遍。 只是，我不敢保证什么时候能够完成这趟任务。

除了时间关系外，个人功力实在有限，很怕匆忙间误导观众就糟糕了。 若能抛砖引玉，诱得，其他前辈出马补充，那才是功德一件。

### shell 十三问：

1.  [为何叫做 shell?](http://bbs.chinaunix.net/forum.php?mod=viewthread&tid=218853&page=2#pid1454336)

2.  [shell prompt(PS1) 与 Carriage Return(CR) 的关系？](http://bbs.chinaunix.net/forum.php?mod=viewthread&tid=218853&page=2#pid1467910) (2008-10-30 02:05 最后更新)

3.  [別人 echo、你也 echo ，是问 echo 知多少？](http://bbs.chinaunix.net/viewthread.php?tid=218853&extra=&page=3#pid1482452)( 2008-10-30 02:08 最后更新)

4.  [" "(双引号) 与 ' '(单引号)差在哪？](http://bbs.chinaunix.net/viewthread.php?tid=218853&extra=&page=4#pid1511745) (2008-10-30 02:07 最后更新)

5.  [var=value 在 export 前后差在哪？](http://bbs.chinaunix.net/viewthread.php?tid=218853&extra=&page=5#pid1544391) (2008-10-30 02:12 最后更新)

6.  [exec 跟 source 差在哪?](http://bbs.chinaunix.net/viewthread.php?tid=218853&extra=&page=6#pid1583329) (2008-10-30 02:17 最后更新)

7.  [( ) 与 { } 差在哪？](http://bbs.chinaunix.net/viewthread.php?tid=218853&extra=&page=6#pid1595135)

8.  [$(( )) 与 $( ) 还有${ } 差在哪？](http://bbs.chinaunix.net/viewthread.php?tid=218853&extra=&page=7#pid1617953) (2008-10-30 02:20 最后更新)

9.  [$@ 与 $* 差在哪？](http://bbs.chinaunix.net/viewthread.php?tid=218853&extra=&page=7#pid1628522)

10.  [&& 与 || 差在哪？](http://bbs.chinaunix.net/viewthread.php?tid=218853&extra=&page=7#pid1634118) (2008-10-30 02:21 最后更新)

11.  [> 与 < 差在哪？](http://bbs.chinaunix.net/viewthread.php?tid=218853&extra=&page=7#pid1636825) (2008-10-30 02:24 最后更新)

12.  [你要 if 还是 case 呢？](http://bbs.chinaunix.net/viewthread.php?tid=218853&extra=&page=8#pid1679488) (2008-10-30 02:25 最后更新)

13.  [for what? while 与 until 差在哪？](http://bbs.chinaunix.net/viewthread.php?tid=218853&extra=&page=8#pid1692457) (2008-10-30 02:26 最后更新)

14.  [](http://bbs.chinaunix.net/viewthread.php?tid=218853&extra=&page=16#pid2930144)[](#fn_)跟 [! ] 差在哪？

15.  [Part-I: Wildcard](http://bbs.chinaunix.net/viewthread.php?tid=218853&extra=&page=16#pid2930144) (2008-10-30 02:25 最後更新)

16.  [Part-II Regular Expression](http://bbs.chinaunix.net/viewthread.php?tid=218853&extra=&page=16#pid2934852) (2008-10-30 02:26 最后更新)

* * *

说明：

1.  欢迎大家补充/扩充问题。

2.  我接触电脑的中文名称时是在台湾，因此一些术语或与大陆不同，请自行转换。

3.  我会不定时"逐题"说明(以 Linux 上的 bash 为环境) 同时，也会在任何时候进行无预警的修改。请读者自行留意。

4.  本人于本系列所发表的任文章均可自由以电子格式(非印刷)引用、修改、转载， 且不必注明出处(若能注明 CU 更佳)。当然，若有错漏或不当结果，本人也不负任何责任。

5.  若有人愿意整理成册且付印者，本人仅保留著作权，版权收益之 30% 須捐赠于 CU 论坛管理者，剩余不究。

* * *

建议參考谈论:

1.  shaoping0330 兄关于变量替换的补充：（链接在改版后已经失效）

2.  shaoping0330 兄[关于 RE](http://bbs.chinaunix.net/forum/viewtopic.php?t=393964) 的说明:

3.  关于 nested subshell 的讨论:（链接在改版后已经失效）

4.  [关于 IFS 的讨论](http://bbs.chinaunix.net/forum/viewtopic.php?t=512925):

* * *

*   感谢 lkydeer 兄整理 [word/pdf 版本](http://bbs.chinaunix.net/viewthread.php?tid=963890&extra=page%3D2)方便大家参考：