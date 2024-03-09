# 部分 I. 起步

*   FreeBSD 入门。

*   安装过程向导。

*   教您 UNIX® 基本知识和基本原理。

*   展示如何在 FreeBSD 上安装大量的第三方应用程序。

*   介绍使用 X，UNIX® 窗口系统， 以及为一些能够提高工作效率的桌面环境配置细节。

我们尝试用最少的页数来保持前言的索引，以至于可以用最少翻页次数将该手册从头至尾读过。

## 第 1 章 介绍

Restructured, reorganized, and parts rewritten by Jim Mock.

## 1.1. 概述

非常感谢您对 FreeBSD 感兴趣！ 下面的章节涵盖了 FreeBSD 项目的各个方面， 比如它的历史、目标、开发模式，等等。

阅读完这章，您将了解：

*   FreeBSD 与其它计算机操作系统的关系。

*   FreeBSD 项目的历史。

*   FreeBSD 项目的目标。

*   FreeBSD 开放源代码开发模式的基础。

*   当然还有：“FreeBSD” 这个名称的由来。

## 1.2. 欢迎来到 FreeBSD 的世界!

FreeBSD 是一个支持 Intel (x86 和 Itanium®)，AMD64， Sun UltraSPARC® 计算机的基于 4.4BSD-Lite 的操作系统。 到其他体系结构的移植也在进行中。 您也可以阅读 FreeBSD 的历史， 或者最新的发行版本。 如果您有意捐助(代码， 硬件，基金)，请看为 FreeBSD 提供帮助这篇文章。

### 1.2.1. FreeBSD 能做些什么？

FreeBSD 有许多非凡的特性。其中一些是：

*   *抢占式多任务*与动态优先级调整确保在应用程序和用户之间平滑公正的分享计算机资源， 即使工作在最大的负载之下。

*   *多用户设备* 使得许多用户能够同时使用同一 FreeBSD 系统做各种事情。 比如， 像打印机和磁带驱动器这样的系统外设， 可以完全地在系统或者网络上的所有用户之间共享， 可以对用户或者用户组进行个别的资源限制， 以保护临界系统资源不被滥用。

*   符合业界标准的强大 *TCP/IP 网络* 支持， 例如 SCTP、 DHCP、 NFS、 NIS、 PPP， SLIP， IPsec 以及 IPv6。 这意味着您的 FreeBSD 主机可以很容易地和其他系统互联， 也可以作为企业的服务器，提供重要的功能， 比如 NFS(远程文件访问)以及 email 服务， 或将您的组织接入 Internet 并提供 WWW，FTP，路由和防火墙(安全)服务。

*   *内存保护*确保应用程序(或者用户)不会相互干扰。 一个应用程序崩溃不会以任何方式影响其他程序。

*   FreeBSD 是一个 *32 位*操作系统 (在 Itanium®，AMD64，和 UltraSPARC® 上是*64 位*)， 并且从开始就是如此设计的。

*   业界标准的 *X Window 系统* (X11R7)为便宜的常见 VGA 显示卡和监视器提供了一个图形化的用户界面(GUI)， 并且完全开放代码。

*   和许多 Linux，SCO，SVR4，BSDI 和 NetBSD 程序的*二进制代码兼容性*

*   数以千计的 *ready-to-run* 应用程序可以从 FreeBSD *ports* 和 *packages* 套件中找到。 您可以顺利地从这里找到， 何须搜索网络？

*   可以在 Internet 上找到成千上万其它 *easy-to-port* 的应用程序。 FreeBSD 和大多数流行的商业 UNIX® 代码级兼容， 因此大多数应用程序不需要或者只要很少的改动就可以编译。

*   页式请求*虚拟内存*和“集成的 VM/buffer 缓存”设计有效地满足了应用程序巨大的内存需求并依然保持其他用户的交互式响应。

*   *SMP* 提供对多处理器的支持。

*   内建了完整的 *C*、 *C++*、 *Fortran* 开发工具。 许多附加的用于高级研究和开发的程序语言， 也可以在通过 ports 和 packages 套件获得。

*   完整的系统*源代码*意味着您对您环境的最大程度的控制。 当您拥有了一个真正的开放系统时， 为什么还要受困于私有的解决方案， 任商业公司摆布呢？

*   丰富的*在线文档*。

*   *不仅如此！*

FreeBSD 基于加州大学伯克利分校计算机系统研究组 (CSRG) 发布的 4.4BSD-Lite， 继承了 BSD 系统开发的优良传统。 除了 CSRG 优秀的工作之外， FreeBSD 项目花费了非常多的时间来优化调整系统， 使其在真实负载情况下拥有最好的性能和可靠性。 在现今， 许多商业巨人正为给 PC 操作系统增加新功能、 提升和改善其可靠性， 以便在其上展开激烈竞争的同时， FreeBSD *现在* 已经能够提供所有这一切了！

FreeBSD 可以提供的应用事实上仅局限于您的想象力。 从软件开发到工厂自动化，从存货控制到遥远的人造卫星天线方位控制， 如果商业的 UNIX® 产品可以做到， 那么就非常有可能您也可以用 FreeBSD 来做！ FreeBSD 也极大地受益于全世界的研究中心和大学开发的数以千计的高质量的应用程序， 这些程序通常只需要很少的花费甚至免费。 可用的商业应用程序， 每天也都在大量地增加。

因为 FreeBSD 自身的源代码是完全公开的， 所以对于特定的应用程序或项目，可以对系统进行最大限度的定制。 这对于大多数主流的商业生产商的操作系统来说几乎是不可能的。 以下是当前人们应用 FreeBSD 的某些程序的例子：

*   *Internet 服务：* FreeBSD 内建的强大的 TCP/IP 网络使它得以成为各种 Internet 服务的理想平台， 比如：

    *   FTP 服务器

    *   World Wide Web 服务器(标准的或者安全的 [SSL])

    *   IPv4 and IPv6 路由

    *   防火墙和 NAT(“IP 伪装”) 网关

    *   电子邮件服务器

    *   USENET 新闻组和电子布告栏系统

    *   还有许多...

    使用 FreeBSD， 您可以容易地从便宜的 386 类 PC 起步，并随着您的企业成长，一路升级到带有 RAID 存储的四路 Xeon 服务器。

*   *教育：* 您是一名计算机科学或者相关工程领域的学生吗？ 学习操作系统，计算机体系结构和网络没有比在 FreeBSD 可提供的体验下动手实践更好的办法了。许多可自由使用的 CAD、数学和图形设计包也使它对于那些主要兴趣是在计算机上完成 *其他*工作的人非常有帮助。

*   *研究：* 有完整的系统源代码，FreeBSD 对于操作系统研究以及其他计算机科学分支都是一个极好的平台。 FreeBSD 可自由获得的本性， 同样可以使处在不同地方的开发团队在开放的论坛上讨论问题、 交流想法与合作开发成为可能， 且不必担心特别的版权协定或者限制。

*   *网络：*需要一个新的路由器？ 一台域名服务器 (DNS)？ 一个隔离您的内部网络的防火墙？ FreeBSD 可以容易的把丢弃在角落不用的 386 或者 486 PC 变成一台完善的带包过滤能力的高级路由器。

*   *X Window 工作站：* FreeBSD 是廉价 X 终端的一种绝佳解决方案， 您可以选择使用免费的 X11 服务器。 与 X 终端不同，如果需要的话 FreeBSD 能够在本地直接运行程序， 因而减少了中央服务器的负担。 FreeBSD 甚至能够在 “无盘” 环境下启动， 这使得终端更为便宜和易于管理。

*   *软件开发：* 基本的 FreeBSD 系统带有包括著名的 GNU C/C++ 编译器和调试工具在内的一整套开发工具。

FreeBSD 可以通过 CD-ROM、DVD， 以及匿名 FTP 以源代码和二进制方式获得。请查看附录 A, *获取 FreeBSD* 了解获取 FreeBSD 的更多细节。

### 1.2.2. 谁在使用 FreeBSD?

FreeBSD 被世界上最大的 IT 公司用作设备和产品的平台， 包括：

*   [Apple](http://www.apple.com/)

*   [Cisco](http://www.cisco.com/)

*   [Juniper](http://www.juniper.net/)

*   [NetApp](http://www.netapp.com/)

FreeBSD 也被用来支持 Internet 上一些最大的站点， 包括：

*   [Yahoo!](http://www.yahoo.com/)

*   [Yandex](http://www.yandex.ru/)

*   [Apache](http://www.apache.org/)

*   [Rambler](http://www.rambler.ru/)

*   [新浪网](http://www.sina.com/)

*   [Pair Networks](http://www.pair.com/)

*   [Sony Japan](http://www.sony.co.jp/)

*   [Netcraft](http://www.netcraft.com/)

*   [NetEase](http://www.163.com/)

*   [Weathernews](http://www.wni.com/)

*   [TELEHOUSE America](http://www.telehouse.com/)

*   [Experts Exchange](http://www.experts-exchange.com/)

等等许多。

## 1.3. 关于 FreeBSD 项目

下面的章节提供了项目的一些背景信息， 包括简要的历史、项目目标、以及项目开发模式。

### 1.3.1. FreeBSD 的简要历史

Contributed by Jordan Hubbard.

FreeBSD 项目起源于 1993 年早期， 部分作为 “Unofficial 386BSD Patchkit” 的副产物，patchkit 的最后 3 个协调维护人是：Nate Williams，Rod Grimes 和我。

我们最初的目标是做出一份 386BSD 的测试版以修正一些 Patchkit 机制无法解决的错误(bug)。 很多人可能还记得早期的项目名称叫做 “386BSD 0.5” 或者 “386BSD Interim” 就是这个原因。

386BSD 是 Bill Jolitz 的操作系统， 到那时已被严重地忽视了一年之久。 由于 Patchkit 在过去的每一天里都在急剧膨胀， 使得对其进行消化吸收变得越来越困难， 因此我们一致同意应该做些事情并决定通过提供这个临时的 “cleanup” 版本来帮助 Bill。 然而，Bill 却在事先没有指出这个项目应该如何开展下去的情况下， 突然决定退出这个项目，最终这个计划只好被迫停止。

没过多久， 我们认为即便没有 Bill 的支持， 项目仍有保留的价值， 因此，我们采用了 David Greenman 的意见，给其命名为 “FreeBSD”。在和当时的几个用户商量后， 我们提出了最初的目标， 而这件事明朗化后， 这个项目就走上了正轨，甚至可能成为现实。 为了拓展 FreeBSD 的发行渠道，我抱着试试看的心态， 联系了光盘商 Walnut Creek CDROM， 以便那些上网不方便的用户得到 FreeBSD。 Walnut Creek CDROM 不仅支持发行 FreeBSD 光盘版的想法， 还为这个计划提供了所需的计算机和高速网络接入。 在那时， 若没有 Walnut Creek CDROM 对一个完全未知的项目的空前信任， FreeBSD 不太可能像它今天这样，影响如此深远， 发展如此快速。

第一个 CD-ROM (以及在整个互联网范围内发行的) 发行版本是 FreeBSD 1.0，于 1993 年 10 月发布。这个版本基于 U.C. Berkeley 的 4.3BSD-Lite(“Net/2”)磁带， 也有许多组件是 386BSD 和自由软件基金会提供的。 对于第一次发行，这算是相当成功了。 在 1994 年 5 月，我们发布了更加成功的 FreeBSD 1.1 版。

在这段时间， 发生了一些意外的情况。 Novell 和 U.C. Berkeley 就 Berkeley Net/2 磁带知识产权的马拉松式的官司达成了和解。 和解中的一部分是 U.C. Berkeley 作出的让步， 令 Net/2 中的一大部分内容成为 “受限的 (encumbered)” 和属于 Novell 知识产权的代码， 而后者在不久前刚刚从 AT&T 收购了这些产权； 作为回报， Berkeley 得到了来自 Novell 的 “许诺”， 在 4.4BSD-Lite 版本正式发布时， 可以声明为不受限的 (unencumbered)， 现有的 Net/2 用户则强烈建议转移到这个版本。 这包括了 FreeBSD， 而我们的项目则被允许在 1994 年 6 月底之前继续发行基于 Net/2 的产品。 根据和解协议， 在最后期限之前我们发布了一个最终版本， 这个版本是 FreeBSD 1.1.5.1。

接下来， FreeBSD 开始了艰苦的从全新的、 不太完整的 4.4BSD-Lite 重新编写自己的过程。 “Lite” 版本中， Berkeley 的 CSRG 删除了用于让系统能够引导的一大部分代码 (由于各种各样的法律需求)， 而当时 4.4 在 Intel 平台的移植版本还有很多工作没有完成。 直到 1994 年 11 月， 我们的项目才完成了这项过渡， 并通过网络以及 CD-ROM (在 12 月底) 上发布了 FreeBSD 2.0。 尽管系统中还有很多比较粗糙的地方， 这个版本还是取得了巨大的成功， 并在 1995 年 6 月发布了更强大和易于安装的 FreeBSD 2.0.5 版本。

我们于 1996 年 8 月发布了 FreeBSD 2.1.5 版本， 它在 ISP 和商业团体中非常流行。 随后， 2.1-STABLE 分支的另一个版本应运而生，它就是 FreeBSD 2.1.7.1，在 1997 年 2 月发布并停止了 2.1-STABLE 的主流开发。现在，它处于维护状态， 仅仅提供安全性的增强和其他严重的错误修补的维护(RELENG_2_1_0)。

FreeBSD 2.2 版作为 RELENG_2_2 分支，于 1996 年 11 月从开发主线 (“-CURRENT”)分出来。 它的第一个完整版(2.2.1)于 1997 年 4 月发布出来。 97 年夏秋之间，顺着 2.2 分支的更进一步的版本在开发。 其最后一版(2.2.8)于 1998 年 11 月发布出来。 第一个官方的 3.0 版本出现在 1998 年 10 月， 意味着 2.2 分支结束的开始。

1999 年 1 月 20 日又出现了新的分支，就是 4.0-CURRENT 和 3.X-STABLE 分支。从 3.X-STABLE 起，3.1 在 1999 年 2 月 15 日发行，3.2 在 1999 年 5 月 15 日，3.3 在 1999 年 9 月 16 日，3.4 在 1999 年 12 月 20 日，3.5 在 2000 年 6 月 24 日，接下来几天后发布了很少的修补升级至 3.5.1，加入了对 Kerberos 安全性方面的修补。 这是 3.X 分支最后一个发行版本。

随后在 2000 年 3 月 13 日出现了一个新的分支， 也就是 4.X-STABLE。 这之后发布了许多的发行版本： 4.0-RELEASE 于 2000 年 3 月发布， 而最后的 4.11-RELEASE 则是在 2005 年 1 月发布的。

期待已久的 5.0-RELEASE 于 2003 年 1 月 19 日正式发布。 这是将近三年的开发的巅峰之作， 同时也标志了 FreeBSD 在先进的多处理器和应用程序线程支持的巨大成就， 并引入了对于 UltraSPARC® 和 `ia64` 平台的支持。 之后于 2003 年 6 月发布了 5.1。 最后一个从 -CURRENT 分支的 5.X 版本是 5.2.1-RELEASE， 它在 2004 年 2 月正式发布。

RELENG_5 于 2004 年 8 月正式创建， 紧随其后的是 5.3-RELEASE， 它是 5-STABLE 分支的标志性发行版。 这个分支的最后一个版本， 5.5-RELEASE 是在 2006 年 5 月发布的。 RELENG_5 分支不会有后续的发行版了。

其后在 2005 年 7 月又建立了 RELENG_6 分支。 而 6.X 分支上的第一个版本， 即 6.0-RELEASE， 则是在 2005 年 11 月发布的。 这个分支的最后一个版本， 6.4-RELEASE 是在 2008 年 11 月 发布的。 RELENG_6 分支上不再会有发布版本了。 这是最后一个支持 Alpha 硬件架构的版本。

RELENG_7 分支于 2007 年 10 月创建。 第一个这个分支的发行版是 7.0-RELEASE， 这个版本是 2008 年 2 月发布的。 最新的 9.3-RELEASE 是在 2012 年 4 月 发布的。 RELENG_7 将不会有其它后续的发布版本。

其后在 2009 年 8 月又建立了 RELENG_8 分支。 8.X 分支的第一个版本， 8.0-RELEASE 是在 2009 年 11 月发布的。 最新的 10.3-RELEASE 于 2012 年 1 月 发布。 RELENG_8 还将会有其它后续的发布版本。

目前， 中长期的开发项目继续在 9.X-CURRENT (主干, trunk) 分支中进行， 而 9.X 的 CD-ROM (当然， 也包括网络) 快照版本可以在 快照服务器 找到。

### 1.3.2. FreeBSD 项目目标

Contributed by Jordan Hubbard.

FreeBSD 项目的目标是无附加条件地提供能够用于任何目的的软件。 我们中的许多人对代码 (以及项目本身) 都有非常大的投入， 因此当然不介意偶尔有一些资金上的补偿， 但我们并没打算坚决地要求得到这类资助。 我们认为我们的首要 “使命” 是为任何人提供代码， 不管他们打算用这些代码做什么， 因为这样代码将能够被更广泛地使用， 从而最大限度地发挥其价值。 我认为这是自由软件最基本的， 同时也是为我们所倡导的一个目标。

我们源代码树中， 以 GNU 公共许可证 (GPL) 或者 GNU 函数库公共许可证 (LGPL) 发布的那些代码带有少许的附加限制， 还好只是强制性的要求开放代码而不是别的。 由于使用 GPL 的软件在商业用途上会增加若干复杂性， 因此，如果可以选择的话， 我们更偏好使用限制相对更宽松的 BSD 版权来发布软件。

### 1.3.3. FreeBSD 开发模式

撰写者 Satoshi Asami.

FreeBSD 的开发是一个非常开放且有有伸缩性的过程， 就像从我们的 贡献者列表里看到的，它是完全由来自全世界的数以百计的贡献者发展起来的。 FreeBSD 的开发基础结构允许数以百计的开发者通过互联网协同工作。 我们也经常关注着那些对我们的计划感兴趣的新开发者和新的创意， 那些有兴趣更进一步参与项目的人只需要在 [FreeBSD 技术讨论邮件列表](http://lists.FreeBSD.org/mailman/listinfo/freebsd-hackers) 联系我们。 [FreeBSD 公告邮件列表](http://lists.FreeBSD.org/mailman/listinfo/freebsd-announce) 对那些希望了解我们工作所涉及到哪些领域的人也是有用的。

无论是独立地工作或者封闭式的团队工作， 了解 FreeBSD 计划和它的开发过程都是有益的：

SVN 和 CVS 代码库

在过去的几年中 FreeBSD 的中央源代码树是由 [CVS](http://ximbiot.com/cvs/wiki/) (并行版本控制系统)来维护的，CVS 是一个与 FreeBSD 捆绑的可自由获得的源代码控制工具。自 2008 年六月起， 这个项目开始转为使用[SVN](http://subversion.tigris.org) (Subversion)。 这次转换被认为是非常必要的，因为 CVS 的对于快速扩展源代码树和历史记录的限制越趋明显。现在主源码库使用 SVN，客户端的工具像 CVSup 和 csup 这些依赖于旧的 CVS 基础结构依然可以使用 ── 因为对于 SVN 源码库的修改会被导回进 CVS。 目前只有中央原代码树是由 SVN 控制的。文档，万维网和 Ports 库还仍旧使用着 CVS。 The primary [repository](http://www.FreeBSD.org/cgi/cvsweb.cgi) resides on a machine in Santa Clara CA, USA 主 [CVS 代码库](http://www.FreeBSD.org/cgi/cvsweb.cgi)放置在美国加利福尼亚州圣克拉拉的一台机器上， 它被复制到全世界的大量镜像站上。包含 -CURRENT 和 -STABLE 的 SVN 树也同样能非常容易的你的机器上。 请参阅 同步你的源码树 获得更多的相关信息。

committer 列表

*committer* 是那些对 CVS 树有*写*权限的人， 他们被授权修改 FreeBSD 的源代码 (术语 “committer” 来自于 [cvs(1)](http://www.FreeBSD.org/cgi/man.cgi?query=cvs&sektion=1&manpath=freebsd-release-ports) 的 `commit` 命令，这个命令用来把新的修改提交给 CVS 代码库)。提交修正的最好方法是使用 [send-pr(1)](http://www.FreeBSD.org/cgi/man.cgi?query=send-pr&sektion=1&manpath=freebsd-release-ports) 命令。如果您发现在系统中出现了一些问题的话， 您也可以通过邮件将它们发送至 FreeBSD committer 的邮件列表。

FreeBSD 核心团队

如果把 FreeBSD 项目看作一家公司，那么 *FreeBSD 核心团队*就相当于董事会。 核心团队的主要任务是提出总体上的发展计划，然后确定一个正确的方向。 邀请那些富有献身精神和可靠的开发者加入到 committer 队伍中来也是核心团队的工作之一， 这些新的成员将作为新核心团队成员和其他人一起继续前进。 当前的核心团队是 2010 年 7 月从 committer 中选举产生的。选举每两年一次。

一些核心团队的成员还负责特定的责任范围， 也就是说他们必须尽力确保某个子系统能工作正常。 FreeBSD 开发者的完整列表和他们的责任范围，请参见 贡献者列表

### 注意:

核心团队的大部分成员加入 FreeBSD 开发的时候都是志愿的， 并没有从项目中获得任何财政上的资助， 所以“承诺”不应该被理解为“支持保证”。 前面所述“董事会”的类推并不十分准确， 或许更好的说法是，他们是一群愿意放弃他们的生活， 投身于 FreeBSD 项目而非选择其个人更好的生活的人！

外围贡献者

事实上，最大的开发团队正是为我们提供反馈和错误修补的用户自己。 FreeBSD 的非集中式的开发者保持联系的主要方式就是预订 [FreeBSD 技术讨论邮件列表](http://lists.FreeBSD.org/mailman/listinfo/freebsd-hackers)，很多事情在那里讨论。查看附录 C, *Internet 上的资源*了解众多 FreeBSD 邮件列表的更多信息。

*FreeBSD 贡献者列表* 很长并在不断增长， 为什么不加入它来为 FreeBSD 做贡献呢？

提供代码不是为这个计划做贡献的唯一方式； 有一个更完整的需要做的事情的列表，可以参见 FreeBSD 项目网站。

总的来说，我们的开发模式好像是一组没有拘束的同心圆。 这种集中式的开发模式，主要是考虑到 FreeBSD *用户*的方便， 同时让他们能很容易地维护同一份软件， 而不会把潜在的贡献者排除在外！ 我们的目标是提供一个包含有大量具有一致性 应用程序的稳定的操作系统， 以利于用户的安装和使用，── 这种模式在完成目标的过程中工作得非常有效。

我们对于那些想要加入，成为 FreeBSD 开发者的期待是： 具有如同当前其他人一样的投入，来确保持续的成功！

### 1.3.4. 最新的 FreeBSD 发行版本

FreeBSD 是一个免费使用且带有完整源代码的基于 4.4BSD-Lite 的系统， 它广泛运行于 Intel i386™、i486™、Pentium®、 Pentium® Pro、 Celeron®、 Pentium® II、 Pentium® III、 Pentium® 4(或者兼容系统)、 Xeon™、 和 Sun UltraSPARC® 的计算机系统上。 它主要以 加州大学伯克利分校 的 CSRG 研究小组的软件为基础，并加入了 NetBSD、OpenBSD、386BSD 以及来自 自由软件基金会 的一些东西。

自从 1994 年末我们的 FreeBSD 2.0 发行以来， FreeBSD 的性能，可定制性，稳定性都有了令人注目的提高。 最大的变化是通过整合虚拟内存/文件系统中的高速缓存改进的虚拟内存系统， 它不仅提升了性能，而且减少了 FreeBSD 对内存的需要， 使得 5 MB 内存成为可接受的最小配置。 其他的改进包括完整的 NIS 客户端和服务器端的支持， 事务式 TCP 协议支持，按需拨号的 PPP，集成的 DHCP 支持，改进的 SCSI 子系统， ISDN 的支持，ATM，FDDI，快速 Gigabit 以太网(1000 Mbit)支持， 提升了最新的 Adaptec 控制器的支持和修补了很多的错误。

除了最基本的系统软件，FreeBSD 还提供了一个拥有成千上万广受欢迎的程序组成的软件的 Ports Collection。 到本书付印时，已有超过 24,000 个 ports (ports 包括从 http(WWW) 服务器到游戏、程序设计语言、编辑器以及您能想到的几乎所有的东西)。 完整的 Ports Collection 大约需要 500 MB 的存储空间。所有的只提供对原始代码的 “修正”。这使得我们能够容易地更新软件， 而且减少了老旧的 1.0 Ports Collection 对硬盘空间的浪费。 要编译一个 port，您只要切换到您想要安装的程序的目录， 输入 `make install`，然后让系统去做剩下的事情。 您要编译的每一个程序完整的原始代码可以从 CD-ROM 或本地 FTP 获得，所以您只需要编译您想要软件的足够的磁盘空间。 几乎大多数的软件都提供了事先编译好的 “package” 以方便安装，对于那些不希望从源代码编译他们自己的 ports 的人只要使用一个简单的命令 (`pkg_add`)就可以安装。 有关 package 和 ports 的更多信息可以在第 5 章 *安装应用程序: Packages 和 Ports*中找到。

您可以在最近的 FreeBSD 主机的 `/usr/share/doc` 目录下找到许多有用的文件来帮助您安装及使用 FreeBSD。 您也可以用一个 HTML 浏览器来查阅本地安装的手册， 使用下面的 URL：

FreeBSD 使用手册

`/usr/share/doc/handbook/index.html`

FreeBSD FAQ

`/usr/share/doc/faq/index.html`

您也可以查看在 `[`www.FreeBSD.org/`](http://www.FreeBSD.org/)` 的主站上的副本。

## 第 2 章 安装 FreeBSD

结构、组织重整, 部分重写 Jim Mock.sysinstall 操作流程、屏幕抓图以及一般性文件 Randy Pratt.

## 2.1. 概述

FreeBSD 提供了一个以文字为主，简单好用的安装程序，叫做 sysinstall 。这是 FreeBSD 默认使用的安装程序； 厂商如果想，也可以提供适合自己需要的安装程序。本章说明如何使用 sysinstall 来安装 FreeBSD。

学习完本章之后，您将会知道：

*   如何制作 FreeBSD 安装磁盘

*   FreeBSD 如何参照及分割您的硬盘

*   如何启动 sysinstall.

*   在执行 sysinstall 时您将要回答的问题、 问题代表什么意义，以及该如何回答它们。

在阅读本章之前，您应该：

*   阅读您要安装的 FreeBSD 版本所附的硬件支持列表以确定您的硬件有没有被支持。

### 注意:

一般来说，此安装说明是针对 i386™ (“PC 兼容机”) 体系结构的电脑。如果有其它体系结构的安装说明， 我们将一并列出。虽然本文档经常保持更新， 但有可能与您安装版本上所带的说明文档有些许出入。 在这里建议您使用本说明文章作为一般性的安装指导参考手册。

## 2.2. 硬件需求

### 2.2.1. 最小配置

安装 FreeBSD 所需的最小硬件配置， 随 FreeBSD 版本和硬件架构不同而有所不同。

在接下来的几节中， 给出了这些信息的一些总结。随您安装 FreeBSD 的方式不同， 可能需要使用软驱或为 FreeBSD 支持的 CDROM 驱动器， 有时候也可能需要的是一块网卡。 这将在 第 2.3.7 节 “准备引导介质” 中进行介绍。

#### 2.2.1.1. FreeBSD/i386 和 FreeBSD/pc98

FreeBSD/i386 和 FreeBSD/pc98 版本， 都需要 486 或更高的处理器，以及至少 24 MB 的 RAM。 您需要至少 150 MB 的空闲硬盘空间， 才能完成最小的安装配置。

### 注意:

对于老旧的硬件而言， 多数时候， 装配更多的 RAM 和腾出更多的硬盘空间， 要比使用更快的处理器更有用。

#### 2.2.1.2. FreeBSD/amd64

有两类处理器同时能够支持运行 FreeBSD/amd64。 第一种是 AMD64 处理器， 包括 AMD Athlon™64、 AMD Athlon™64-FX、 AMD Opteron™ 以及更高级别的处理器。

能够使用 FreeBSD/amd64 的另一种处理器是包含了采用 Intel® EM64T 架构支持的处理器。 这类处理器包括 Intel® Core™ 2 Duo、 Quad、 以及 Extreme 系列处理器， 以及 Intel® Xeon™ 3000、 5000、 和 7000 系列处理器。

如果您的计算机使用 nVidia nForce3 Pro-150， 则 *必须* 使用 BIOS 配置， 禁用 IO APIC。 如果您没有找到这样的选项， 可能就只能转而禁用 ACPI 了。 Pro-150 芯片组存在一个 bug， 目前我们还没有找到绕过这一问题的方法。

#### 2.2.1.3. FreeBSD/sparc64

要安装 FreeBSD/sparc64， 必须使用它支持的平台 (参见 第 2.2.2 节 “支持的硬件”)。

FreeBSD/sparc64 需要独占一块磁盘。 目前还没有办法与其它操作系统共享一块磁盘。

### 2.2.2. 支持的硬件

支持的硬件列表， 会作为 FreeBSD 发行版本的 FreeBSD 兼容硬件说明提供。 这个文档通常可以在 CDROM 或 FTP 安装文件的顶级目录找到， 它的名字是 `HARDWARE.TXT`， 此外， 在 sysinstall 的 documentation 菜单也可以找到。它针对特定的硬件架构列出了 FreeBSD 已知支持的硬件。 不同发行版本和架构上的硬件支持列表，可以在 FreeBSD 网站的 [发行版信息](http://www.FreeBSD.org/releases/index.html) 页面上找到。

## 2.3. 安装前的准备工作

### 2.3.1. 列出您电脑的硬件清单

在安装 FreeBSD 之前，您应该试着将您电脑中的硬件清单列出来。 FreeBSD 安装程序会将这些硬件(磁盘、网卡、光驱等等) 以及型号及制造厂商列出来。FreeBSD 也会尝试为这些设备找出最适当的 IRQ 及 IO 端口的设定。但是因为 PC 的硬件种类实在太过复杂， 这个步骤不一定总是能成功。这时， 您就可能需要手动更改有问题的设备的设定值。

如果您已经安装了其它的操作系统，如 Windows® 或 Linux， 那么您可以先由这些系统所提供的工具来查看您的设备设定值是怎么分配的。 如果您真的没办法确定某些接口卡用什么设定值，那么您可以检查看看， 说不定它的设定已经标示在卡上。常用的 IRQ 号码为 3、5 以及 7； IO 端口的值通常以 16 进制位表示，例如 Ox330。

我们建议您在安装 FreeBSD 之前把这些信息打印或记录下来，做成表格 的样子也许会比较有帮助，例如：

表 2.1. 硬件设备清单

| 设备名 | IRQ | IO 端口号 | 备注 |
| --- | --- | --- | --- |
| 第一块硬盘 | N/A | N/A | 40 GB，Seagate 制造，第一个 IDE 接口主设备 |
| CDROM | N/A | N/A | 第一个 IDE 接口从设备 |
| 第二块硬盘 | N/A | N/A | 20 GB，IBM 制造, 第二个 IDE 接口主设备 |
| 第一个 IDE 控制器 | 14 | 0x1f0 |   |
| 网卡 | N/A | N/A | Intel® 10/100 |
| Modem | N/A | N/A | 3Com® 56K faxmodem，位于 COM1 口 |
| … |   |   |   |

在清楚地了解了您计算机的配置之后， 需要检查它是否符合您希望安装的 FreeBSD 版本的硬件需求。

### 2.3.2. 备份您的数据

如果您的电脑上面存有重要的数据资料， 那么在安装 FreeBSD 前请确定您已经将这些资料备份了， 并且先测试这些备份文档是否有问题。FreeBSD 安装程序在要写入任何资料到您的硬盘前都会先提醒您确认， 一旦您确定要写入，那么以后就没有反悔的机会。

### 2.3.3. 决定要将 FreeBSD 安装到哪里

如果您想让 FreeBSD 使用整个硬盘，那么请直接跳到下一节。

但是，如果您想让 FreeBSD 跟您已有的系统并存， 那么您必须对您数据存在硬盘的分布方式有深入的了解， 以及其所造成的影响。

#### 2.3.3.1. FreeBSD/i386 体系结构的硬盘分配方式

一个 PC 硬盘可以被细分为许多块。 这些块被称为 *partitions (分区)*。 由于 FreeBSD 内部也有分区的概念，如此命名很容易导致混淆， 因此我们在 FreeBSD 中，将其称为磁盘 slice，或简称为 slices。 例如， FreeBSD 提供的用于操作 PC 磁盘分区的工具 `fdisk` 就将其称为 slice 而不是 partition。 由于设计的原因， 每个硬盘仅支持四个分区； 这些分区叫做 *主分区(Primary partion)*。 为了突破这个限制以便能使用更多的分区，就有了新的分区类型，叫做 *扩展分区(Extended partition)*。 一个硬盘可以拥有一个扩展分区。在扩展分区里可以建立许多个所谓的 *逻辑分区(Logical partitions)*。

每个分区都有其独立的 *分区号(partition ID)*, 用以区分每个分区的数据类型。FreeBSD 分区的分区号为 `165`。

一般而言，每种操作系统都会有自己独特的方式来区别分区。 例如 DOS 及其之后的 Windows®， 会分配给每个主分区及逻辑分区一个 *驱动器字符*， 从 `C:` 开始。

FreeBSD 必须安装在主分区。FreeBSD 可以在这个分区上面存放系统数据或是您建立的任何文件。 然而，如果您有多个硬盘，您也可以在这些硬盘上(全部或部分)建立 FreeBSD 分区。在您安装 FreeBSD 的时候，必须要有一个分区可以给 FreeBSD 使用。 这个分区可以是尚未规划的分区， 或是已经存在且存有数据但您不再需要的分区。

如果您已经用完了您硬盘上的所有分区， 那么您必须使用其它操作系统所提供的工具 (如 DOS 或 Windows® 下的 `fdisk`) 来腾出一个分区给 FreeBSD 使用。

如果您的某个分区有多余的空间，您可以使用它。 但是使用前您需要先整理一下这些分区。

FreeBSD 最小安装需要约 100 MB 的空间，但是这仅是 *非常* 基本的安装， 几乎没有剩下多少空间可以建立您自己的文件。一个较理想的最小安装是 250 MB，不含图形界面；或是 350 MB 以上，包含图形界面。 如果您还需要安装其它的第三方厂商的套件， 那么将需要更多的硬盘空间。

您可以使用类似 PartitionMagic® 这样的商业版本工具， 或类似 GParted 这样的自由软件工具来调整分区尺寸， 从而为 FreeBSD 腾出空间。 PartitionMagic® 和 GParted 都能改变 NTFS 分区的尺寸。 GParted 在许多 Live CD Linux 发行版， 如 [SystemRescueCD](http://www.sysresccd.org/) 中均有提供。

目前已经有报告显示改变 Microsoft® Vista 分区尺寸时会出现问题。 在进行此类操作时， 建议您准备一张 Vista 安装 CDROM。如同其他的磁盘维护操作一样， 强烈建议您事先进行备份。

### 警告:

不当的使用这些工具可能会删掉您硬盘上的数据资料！ 在使用这些工具前确定您有最近的、没问题的备份数据。

例 2.1. 使用已存在的分区

假设您只有一个 4GB 的硬盘，而且已经装了 Windows® 然后您将这个硬盘分成两个分区 `C:` 跟 `D:`，每个分区大小为 2 GB。在 `C:` 分区上存放有 1 GB 的数据、 `D:`分区上存放 0.5 GB 的数据。

这意味着您的盘上有两个分区，一个驱动器符号是一个分区 (如 c:、d:)。 您可以把所有存放在 `D:` 分区上的数据复制到 `C:` 分区, 这样就空出了一个分区(d:)给 FreeBSD 使用。

例 2.2. 缩减已现在的分区

假设您只有一个 4 GB 的硬盘，而且已经装了 Windows®。 您在安装 Windows® 的时候把 4 GB 都给了 `C:` 分区，并且已经使用了 1.5 GB 的空间。您想将剩余空间中的 2 GB 给 FreeBSD 使用。

为了安装 FreeBSD，您必须从下面两种方式中选择一种：

1.  备份 Windows® 的数据资料，然后重新安装 Windows®， 并给 Windows® 分配 2 GB 的空间。

2.  使用上面提及的 PartitionMagic® 来整理或切割您的分区。

### 2.3.4. 收集您的网络配置相关资料

如果您想通过网络(FTP 或是 NFS)安装 FreeBSD， 那么您就必须知道您的网络配置信息。在安装 FreeBSD 的过程中将会提示您输入这些资料，以顺利完成安装过程。

#### 2.3.4.1. 使用以太网或电缆/DSL Modem

如果您通过局域网或是要通过网卡使用电缆/DSL 上网， 那么您必须准备下面的信息：

1.  IP 地址。

2.  默认网关 IP 地址。

3.  主机名称。

4.  DNS 服务器的 IP 地址。

5.  子网掩码。

如果您不知道这些信息， 您可以询问系统管理员或是您的网络服务提供者。 他们可能会说这些信息会由 *DHCP* 自动分配；如果这样的话，请记住这一点就可以了。

#### 2.3.4.2. 使用 Modem 连接

如果您由 ISP 提供的拨号服务上网，您仍然可以通过它安装 FreeBSD，只是会需要很长的时间。

您必须知道：

1.  拨号到 ISP 的电话号码。

2.  您的 modem 是连接到哪个 COM 端口。

3.  您拨号到 ISP 所用的账号和密码。

### 2.3.5. 检查 FreeBSD 发行勘误

虽然我们尽力确保每个 FreeBSD 发行版本的稳定性， 但偶尔也会有一些错误进入发行版。极少数情况下， 这些问题甚至可能会影响安装。 当发现和修正问题之后，它们会列在 FreeBSD 网站中的 [FreeBSD 发行勘误](http://www.FreeBSD.org/releases/10.3R/errata.html) 中。 在您安装之前，应该首先看一看这份勘误表，以了解可能存在的问题。

关于所有释出版本的信息，包括勘误表，可以在 FreeBSD 网站 的 发行版信息 一节中找到。

### 2.3.6. 准备安装介质

FreeBSD 可以通过下面任何一种安装介质进行安装：

安装介质

*   CDROM 或 DVD

*   USB 记忆棒

*   在同一计算机上的 DOS 分区

*   SCSI 或 QIC 磁带

*   软盘

网络

*   通过防火墙的一个 FTP 站点，或使用 HTTP 代理。

*   NFS 服务器

*   一个指定的并行或串行接口

如果您购买了 FreeBSD 的 CD 或 DVD，那么您可以直接进入下一节 (第 2.3.7 节 “准备引导介质”)。

如果您还没有 FreeBSD 的安装文件， 则应按照 第 2.13 节 “准备您自己的安装介质” 来准备。 读完那节之后， 您就可以回到这节并从 第 2.3.7 节 “准备引导介质” 继续了。

### 2.3.7. 准备引导介质

FreeBSD 的安装过程开始于将您的电脑开机进入 FreeBSD 安装环境 ── －并非在其它的操作系统上运行一个程序。 计算机通常使用安装在硬盘上的操作系统进行引导， 也可以配置成使用一张“bootable(可引导)”的软盘进行启动。 大多数现代计算机也都可以从光驱或 USB 盘来引导系统。

### 提示:

如果您有 FreeBSD 的安装光盘或 DVD(或者是您购买的， 或者是您自己准备的。)并且您的计算机可以从光驱进行启动 (通常在 BIOS 中会有 “Boot Order” 或类似的选项可以设置)，那么您就可以跳过此小节。 因为 FreeBSD 光盘及 DVD 光盘都是可以引导的， 用它们开机您不用做什么特别的准备。

要创建引导系统所需的记忆棒， 需按下面的操作进行：

1.  **获取记忆棒映像文件**

    记忆棒映像文件可以从 *`arch`* 对应的 `ISO-IMAGES` 目录， 例如 `ftp://ftp.FreeBSD.org/pub/FreeBSD/releases/arch/ISO-IMAGES/version/FreeBSD-10.3-RELEASE-arch-memstick.img` 获得。 其中， *`arch`* 和 *`version`* 需要替换为您使用的平台和版本。 例如， FreeBSD/i386 10.3-RELEASE 的记忆棒映像位于 `ftp://ftp.FreeBSD.org/pub/FreeBSD/releases/i386/ISO-IMAGES/10.3/FreeBSD-10.3-RELEASE-i386-memstick.img`。

    记忆棒的映像文件扩展名是 `.img`。 在 `ISO-IMAGES/` 目录中提供了多种不同的映像， 您需要根据使用的 FreeBSD 版本， 有时也包括硬件来选择合适的映像。

    ### 重要:

    在继续安装之前， 务必 *备份* 您目前保存在 USB 记忆棒上的数据， 接下来的操作将会 *擦除* 这些数据。

2.  **准备记忆棒**

    ### 警告:

    下面的例子中， 目标记忆棒对应的设备名是 `/dev/da0`。 请小心地确认这是希望覆盖的设备， 否则可能会损坏您的现有数据。

    设置 `kern.geom.debugflags` sysctl 为允许写入目标设备的主引导记录。

    ```
    ``#` sysctl kern.geom.debugflags=16`
    ```

3.  **将映像文件写入记忆棒**

    `.img` 文件 *不是* 直接复制到记忆棒中的那种普通文件。 这个映像是一份包含启动盘全部内容的映像。 这意味着简单地从一个地方复制到另一个地方是 *不能* 赋予其引导系统的能力的。 您必须使用 [dd(1)](http://www.FreeBSD.org/cgi/man.cgi?query=dd&sektion=1&manpath=freebsd-release-ports) 将映像文件直接写入磁盘：

    ```
    # `dd if=FreeBSD-10.3-RELEASE-i386-memstick.img of=/dev/da0 bs=64k`
    ```

一般来说，要建立安装盘(软盘)请依照下列步骤：

1.  **获取开机软盘映像文件**

    ### 重要:

    请注意， 从 FreeBSD 8.0 开始， 我们不再提供软盘映像了。 请参阅前面关于如何使用 USB 记忆棒， 或 CDROM 和 DVD 来安装 FreeBSD 的介绍。

    开机软盘映像文件可以在您的安装介质的 `floppies/` 目录下找到， 另外您也可以从下述网站的 floppies 目录下载： `ftp://ftp.FreeBSD.org/pub/FreeBSD/releases/架构名/版本-RELEASE/floppies/`。 将 *`<架构名>`* 和 *`<版本>`* 替换为您使用的计算机体系结构和希望安装的版本号。 例如，用于安装 i386™ 上的 FreeBSD/i386 9.3-RELEASE 的文件的地址， 应该是 `ftp://ftp.FreeBSD.org/pub/FreeBSD/releases/i386/9.3-RELEASE/floppies/`。

    软盘映像文件的扩展名是 `.flp`。 在 `floppies/` 目录中包括了许多不同的映像文件， 随您安装的 FreeBSD 版本， 某些时候也随硬件的不同， 您需要使用的映像文件可能会有所不同。 您通常会需要四张软盘， 即 `boot.flp`、 `kern1.flp`、 `kern2.flp`， 以及 `kern3.flp`。 请查阅同一目录下的 `README.TXT` 文件以了解关于这些映像文件的最新信息。

    ### 重要:

    您的 FTP 程序必须使用 *二进制模式* 来下载这些映 像文件。有些浏览器只会用 *text* (或*ASCII* ) 模式来传输数据， 用这些浏览器下载的映像文件做成的软盘将无法正常开机。

2.  **准备软盘**

    您必须为您下载的每一个映像文件准备一张软盘。 并且请避免使用到坏掉的软盘。 最简单的方式就是您先将这些软盘格式化， 不要相信所谓的已格式化的软盘。在 Windows® 下的格式化程序不会告诉您出现多少坏块， 它只是简单的标记它们为 “bad” 并且忽略它们。 根据建议您应该使用全新的软盘来存放安装程序。

    ### 重要:

    如果您在安装 FreeBSD 的过程中造成当机、 冻结或是其它怪异现象，第一个要怀疑的就是引导软盘。 请用其它的软盘制作映像文件再试试看。

3.  **将映像文件写入软盘中**

    `.flp` 文件 *并非* 一般的文件，您不能直接将它们复制到软盘上。 事实上它是一张包含完整磁盘内容的映像文件。这表示您 *不能* 简单的使用 DOS 的 copy 命令将文件写到软盘上， 而必须使用特别的工具程序将映像文件直接写到软盘中。

    如果您使用 MS-DOS® 或 Windows® 操作系统来制作引导盘， 那么您可以使用我们提供的 `fdimage` 程序来将映像文件写到软盘中。

    如果您使用的是光盘，假设光盘的驱动器符号为 `E:`，那么请执行下面的命令：

    ```
    E:\> `tools\fdimage floppies\boot.flp A:`
    ```

    重复上述命令以完成每个 `.flp` 文件的写入， 每换一个映像文件都必须更换软盘； 制作好的软盘请注明是使用哪个映像文件做的。 如果您的映像文件存放在不同的地方，请自行修改上面的指令指向您存放 `.flp` 文件的地方。要是您没有 FreeBSD 光盘， 您可以到 FreeBSD 的 FTP 站点 `tools`目录 中下载。

    如果您在 UNIX® 系统上制作软盘(例如其它 FreeBSD 机器)， 您可以使用 [dd(1)](http://www.FreeBSD.org/cgi/man.cgi?query=dd&sektion=1&manpath=freebsd-release-ports) 命令来将映像文件写到软盘中。 如果您用 FreeBSD,可以执行下面的命令：

    ```
    # `dd if=boot.flp of=/dev/fd0`
    ```

    在 FreeBSD 中，`/dev/fd0` 指的是第一个软驱(即 `A:` 驱动器)； `/dev/fd1` 是 `B:` 驱动器,依此类推。其它的 UNIX® 系统可能会用不同的的名称， 这时您就要查阅该系统的说明文件。

您现在可以安装 FreeBSD 了

## 2.4. 开始安装

### 重要:

默认情况下, 安装过程并不会改变任何您硬盘中的数据， 除非您看到下面的讯息：

```
Last Chance: Are you SURE you want continue the installation?

If you're running this on a disk with data you wish to save then WE
STRONGLY ENCOURAGE YOU TO MAKE PROPER BACKUPS before proceeding!

We can take no responsibility for lost disk contents!
```

在看到这最后的警告讯息前您都可以随时离开， 安装程序界面不会变更您的硬盘。如果您发现有任何设定错误， 这时您可以直接将电源关掉而不会造成任何伤害。

### 2.4.1. 开机启动

#### 2.4.1.1. 引导 i386™ 系统

1.  从电脑尚未开机开始说起

2.  将电脑电源打开。刚开始的时候它应该会显示进入系统设置菜单或 BIOS 要按哪个键，常见的是 **F2**、 **F10**、**Del** 或 **Alt**+**S**。不论是要按哪个键，请按它进入 BIOS 设置画面。 有时您的计算机可能会显示一个图形画面，典型的做法是按 **Esc** 将关掉这个图形画面， 以使您能够看到必要的设置信息。

3.  找到设置开机顺序的选项，它的标记为 “Boot Order” 通常会列出一些设备让您选择，例如：`Floppy`、 `CDROM`、`First Hard Disk` 等等。

    如果您要用光盘安装，请选择 CDROM。 如果使用 USB 盘， 或者软盘来引导系统， 也应类似地确认选择了正确的引导设备。 如有疑问， 请参考您的主板说明手册。

    储存设定并离开，系统应该会重新启动。

4.  如果您根据 第 2.3.7 节 “准备引导介质” 制作了 “可引导” 的 USB 记忆棒， 在开机前将其插到计算机上。

    如果您是从光盘安装， 那么开机后请立即将 FreeBSD 光盘放入光驱中。

    ### 注意:

    对于 FreeBSD 7.3 和更早的版本， 可以使用软盘引导， 这些软盘可以根据 第 2.3.7 节 “准备引导介质” 来制作。 其中， `boot.flp` 是启动盘。 引导系统时应使用这张软盘。

    如果您开机后发现计算机引导了先前已经装好的其他操作系统， 请检查：

    1.  是不是软盘或光盘太晚放入而错失开机引导时间。 如果是， 请将它们放入后重新开机。

    2.  BIOS 设定不对，请重新检查 BIOS 的设定。

    3.  您的 BIOS 不支持从这些安装介质引导。

5.  FreeBSD 即将启动。如果您是从光盘引导， 您会见到类似下面的画面：

    ```
    Booting from CD-Rom...
    645MB medium detected
    CD Loader 1.2

    Building the boot loader arguments
    Looking up /BOOT/LOADER... Found
    Relocating the loader and the BTX
    Starting the BTX loader

    BTX loader 1.00 BTX version is 1.02
    Consoles: internal video/keyboard
    BIOS CD is cd0
    BIOS drive C: is disk0
    BIOS drive D: is disk1
    BIOS 636kB/261056kB available memory

    FreeBSD/i386 bootstrap loader, Revision 1.1

    Loading /boot/defaults/loader.conf
    /boot/kernel/kernel text=0x64daa0 data=0xa4e80+0xa9e40 syms=[0x4+0x6cac0+0x4+0x88e9d]
    \
    ```

    如果您从软盘启动， 则应看到类似下面的画面：

    ```
    Booting from Floppy...
    Uncompressing ... done

    BTX loader 1.00  BTX version is 1.01
    Console: internal video/keyboard
    BIOS drive A: is disk0
    BIOS drive C: is disk1
    BIOS 639kB/261120kB available memory

    FreeBSD/i386 bootstrap loader, Revision 1.1

    Loading /boot/defaults/loader.conf
    /kernel text=0x277391 data=0x3268c+0x332a8 |

    Insert disk labelled "Kernel floppy 1" and press any key...
    ```

    请根据提示将 `boot.flp` 软盘取出， 插入 `kern1.flp` 这张盘， 然后按 **Enter**。 您只需从第一张软盘启动， 然后再需要时根据提示插入其他软盘就可以了。

6.  不论是从光盘、 USB 记忆棒或软盘引导， 接下来都会进入 FreeBSD 引导加载器菜单：

    ![FreeBSD Boot Loader Menu](img/boot-loader-menu.png)图 2.1. FreeBSD Boot Loader Menu

    您可以等待十秒， 或按 **Enter**。

#### 2.4.1.2. 引导 SPARC64®

多数 SPARC64® 系统均配置为从硬盘自动引导。 如果希望安装 FreeBSD，就需要从网络或 CDROM 启动了， 这需要首先进入 PROM (OpenFirmware)。

要完成这项工作，首先需要重启系统，并等待出现引导消息。 具体的信息取决于您使用的型号，不过它应该会是类似下面这样：

```
Sun Blade 100 (UltraSPARC-IIe), Keyboard Present
Copyright 1998-2001 Sun Microsystems, Inc.  All rights reserved.
OpenBoot 4.2, 128 MB memory installed, Serial #51090132.
Ethernet address 0:3:ba:b:92:d4, Host ID: 830b92d4.
```

如果您的系统此时开始了从硬盘引导的过程，则需要按下 **L1**+**A** 或 **Stop**+**A**， 或者在串口控制台上发送 `BREAK` (例如， 在 [tip(1)](http://www.FreeBSD.org/cgi/man.cgi?query=tip&sektion=1&manpath=freebsd-release-ports) 或 [cu(1)](http://www.FreeBSD.org/cgi/man.cgi?query=cu&sektion=1&manpath=freebsd-release-ports) 中是 `~#`) 以便进入 PROM 提示符。 它应该是类似下面这样：

```
ok ![1](img/1.png)
`ok {0}` ![2](img/2.png)
```

| ![1](img/#prompt-single) | 这是在只有一颗 CPU 的系统上的提示。 |
| ![2](img/#prompt-smp) | 这是用于 SMP 系统的选项， 这里的数字， 是系统中可用的 CPU 数量。 |

这时， 将 CDROM 插入驱动器， 并在 PROM 提示符后面， 输入 `boot cdrom`。

### 2.4.2. 查看设备探测的结果

前面屏幕显示的最后几百行字会存在缓冲区中以便您查阅。

要浏览缓冲区，您可以按下 **Scroll Lock** 键， 这会开启画面的卷动功能。然后您就可以使用方向键或 **PageUp** 、**PageDown** 键来上下翻阅。 再按一次 **Scroll Lock** 键将停止画面卷动。

在您浏览的时候会看到类似 图 2.2 “典型的设备探测结果”的画面。 真正的结果依照您的电脑装置而有所不同。

```
avail memory = 253050880 (247120K bytes)
Preloaded elf kernel "kernel" at 0xc0817000.
Preloaded mfs_root "/mfsroot" at 0xc0817084.
md0: Preloaded image </mfsroot> 4423680 bytes at 0xc03ddcd4

md1: Malloc disk
Using $PIR table, 4 entries at 0xc00fde60
npx0: <math processor> on motherboard
npx0: INT 16 interface
pcib0: <Host to PCI bridge> on motherboard
pci0: <PCI bus> on pcib0
pcib1:<VIA 82C598MVP (Apollo MVP3) PCI-PCI (AGP) bridge> at device 1.0 on pci0
pci1: <PCI bus> on pcib1
pci1: <Matrox MGA G200 AGP graphics accelerator> at 0.0 irq 11
isab0: <VIA 82C586 PCI-ISA bridge> at device 7.0 on pci0
isa0: <iSA bus> on isab0
atapci0: <VIA 82C586 ATA33 controller> port 0xe000-0xe00f at device 7.1 on pci0
ata0: at 0x1f0 irq 14 on atapci0
ata1: at 0x170 irq 15 on atapci0
uhci0 <VIA 83C572 USB controller> port 0xe400-0xe41f irq 10 at device 7.2 on pci
0
usb0: <VIA 83572 USB controller> on uhci0
usb0: USB revision 1.0
uhub0: VIA UHCI root hub, class 9/0, rev 1.00/1.00, addr1
uhub0: 2 ports with 2 removable, self powered
pci0: <unknown card> (vendor=0x1106, dev=0x3040) at 7.3
dc0: <ADMtek AN985 10/100BaseTX> port 0xe800-0xe8ff mem 0xdb000000-0xeb0003ff ir
q 11 at device 8.0 on pci0
dc0: Ethernet address: 00:04:5a:74:6b:b5
miibus0: <MII bus> on dc0
ukphy0: <Generic IEEE 802.3u media interface> on miibus0
ukphy0: 10baseT, 10baseT-FDX, 100baseTX, 100baseTX-FDX, auto
ed0: <NE2000 PCI Ethernet (RealTek 8029)> port 0xec00-0xec1f irq 9 at device 10.
0 on pci0
ed0 address 52:54:05:de:73:1b, type NE2000 (16 bit)
isa0: too many dependant configs (8)
isa0: unexpected small tag 14
orm0: <Option ROM> at iomem 0xc0000-0xc7fff on isa0
fdc0: <NEC 72065B or clone> at port 0x3f0-0x3f5,0x3f7 irq 6 drq2 on isa0
fdc0: FIFO enabled, 8 bytes threshold
fd0: <1440-KB 3.5” drive> on fdc0 drive 0
atkbdc0: <Keyboard controller (i8042)> at port 0x60,0x64 on isa0
atkbd0: <AT Keyboard> flags 0x1 irq1 on atkbdc0
kbd0 at atkbd0
psm0: <PS/2 Mouse> irq 12 on atkbdc0
psm0: model Generic PS/@ mouse, device ID 0
vga0: <Generic ISA VGA> at port 0x3c0-0x3df iomem 0xa0000-0xbffff on isa0
sc0: <System console> at flags 0x100 on isa0
sc0: VGA <16 virtual consoles, flags=0x300>
sio0 at port 0x3f8-0x3ff irq 4 flags 0x10 on isa0
sio0: type 16550A
sio1 at port 0x2f8-0x2ff irq 3 on isa0
sio1: type 16550A
ppc0: <Parallel port> at port 0x378-0x37f irq 7 on isa0
pppc0: SMC-like chipset (ECP/EPP/PS2/NIBBLE) in COMPATIBLE mode
ppc0: FIFO with 16/16/15 bytes threshold
plip0: <PLIP network interface> on ppbus0
ad0: 8063MB <IBM-DHEA-38451> [16383/16/63] at ata0-master UDMA33
acd0: CD-RW <LITE-ON LTR-1210B> at ata1-slave PIO4
Mounting root from ufs:/dev/md0c
/stand/sysinstall running as init on vty0
```

图 2.2. 典型的设备探测结果

请仔细检查探测结果以确定 FreeBSD 找到所有您期望出现的设备。 如果系统没有找到设备， 则不会将其列出。 定制内核 能够让您为系统添加默认的 `GENERIC` 内核所不支持的设备， 如声卡等。

在 FreeBSD 6.2 和更高版本中， 在探测完系统设备之后， 将显示 图 2.3 “选择国家及地区菜单”。 请使用光标键来选择国家或地区。 接着按 **Enter**， 系统将自动设置地区。 您也可以很容易地退出 sysinstall 程序并从头来过。

![选择国家及地区菜单](img/config-country.png)图 2.3. 选择国家及地区菜单

如果您在国家及地区菜单中选择了 United States (美国)， 则系统会使用标准的美国键盘映射； 如果选择了不同的国家， 则会显示下面的菜单。 使用光标键选择正确的键盘映射， 然后按 **Enter** 来确认。

![选择键盘菜单](img/config-keymap.png)图 2.4. 选择键盘菜单![选择离开 Sysinstall](img/sysinstall-exit.png)图 2.5. 选择离开 Sysinstall

在主界面使用方向键选择 Exit Install 您会看到 如下的信息：

```
                      User Confirmation Requested
         Are you sure you wish to exit? The system will reboot

                            [ Yes ]    No
```

如果此处选择了 [ Yes ] 但 CDROM 还留在光驱里， 则会再次进入安装程序。

如果您是从软盘启动， 则在重启系统之前， 需要将 `boot.flp` 软盘取出。

## 2.5. 介绍 Sysinstall

sysinstall 是 FreeBSD 项目所提供的安装程序。它以 console(控制台)为主， 分为多个菜单及画面让您配置及控制安装过程。

sysinstall 菜单画面由方向键、 **Enter**、**Tab**、**Space**， 以及其它按键所控制。在主画面的 Usage 菜单有这些按键的说明。

要查看这些说明，请将光标移到 Usage 项目，然后 [Select] 按键被选择， 图 2.6 “选取 Sysinstall 主菜单的 Usage 项目”，然后按下 **Enter** 键。

安装画面的使用说明会显示出来，阅读完毕请按 **Enter** 键回到主画面。

![选取 Sysinstall 主菜单的 Usage 项目](img/main1.png)图 2.6. 选取 Sysinstall 主菜单的 Usage 项目

### 2.5.1. 选择 Documentation(说明文件) 菜单

用方向键从主菜单选择 Doc 条目然后按 **Enter**键。

![选择说明文件菜单](img/main-doc.png)图 2.7. 选择说明文件菜单

这将会进入说明文件菜单。

![Sysinstall 说明文件菜单](img/docmenu1.png)图 2.8. Sysinstall 说明文件菜单

阅读这些说明文件很重要。

要阅读一篇文章，请用方向键选取要阅读的文章然后按 **Enter** 键。阅读中再按一下 **Enter** 就会回到说明文件画面。

若要回到主菜单，用方向键选择 Exit 然后按下 **Enter** 键。

### 2.5.2. 选择键盘对应(Keymap)菜单

如果要改变键盘按键的对应方式， 请在主菜单选取 Keymap 然后按 **Enter** 键。一般情况下不改变此项， 除非您使用了非标准键盘或非美国键盘。

![Sysinstall 主菜单](img/main-keymap.png)图 2.9. Sysinstall 主菜单

您可以使用上下键移动到您想使用的键盘对应方式， 然后按下 **Space** 键以选取它；再按 **Space** 键可以取消选取。当您完成后， 请选择 [ OK ] 然后按 **Enter** 键。

这一屏幕只显示出部分列表。选择 [ Cancel ] 按 **Tab** 键将使用默认的键盘对应， 并返回到主菜单

![Sysinstall 键盘对应菜单](img/keymap.png)图 2.10. Sysinstall 键盘对应菜单

### 2.5.3. 安装选项设置画面

选择 Options 然后按 **Enter** 键。

![Sysinstall 主菜单](img/main-options.png)图 2.11. Sysinstall 主菜单![Sysinstall 选项设置](img/options.png)图 2.12. Sysinstall 选项设置

预设值通常可以适用于大部分的使用者，您并不需要改变它们。 版本名称要根据安装的版本进行变化。

目前选择项目的描述会在屏幕下方以蓝底白字显示。 注意其中有一个项目是 Use Defaults(使用默认值) 您可以由此项将所有的设定还原为预设值。

可以按下 **F1** 来阅读各选项的说明。

按 **Q** 键可以回到主画面。

### 2.5.4. 开始进行标准安装

Standard(标准) 安装适用于那些 UNIX® 或 FreeBSD 的初级使用者。用方向键选择 Standard 然后按 **Enter** 键可开始进入标准安装。

![开始进行标准安装](img/main-std.png)图 2.13. 开始进行标准安装

## 2.6. 分配磁盘空间

您的第一个工作就是要分配 FreeBSD 用的硬盘空间以便 sysinstall 先做好一些准备。 为了完成这个工作，您必须先对 FreeBSD 如何找到磁盘信息做一个了解。

### 2.6.1. BIOS 磁盘编号

当您在系统上安装配置 FreeBSD 之前， 有一个重要的事情一定要注意，尤其是当您有多个硬盘的时候。

在 pc 架构，当您跑像 MS-DOS® 或 Microsoft® Windows® 这种跟 BIOS 相关的操作系统的时候，BIOS 有能力改变正常的磁盘顺序， 然后这些操作系统会跟着 BIOS 做改变。这让使用者不一定非要有所谓的 “primary master” 硬盘开机。 许多人发现最简单而便宜备份系统的方式就是再去买一块一模一样的硬盘， 然后定期将数据从第一块硬盘复制到第二个硬盘，使用 Ghost 或 XCOPY。所以，当第一个硬盘死了， 或者是被病毒破坏，或者有坏轨道， 他们可以调整 BIOS 中的开机顺序而直接用第二块硬盘开机。 就像交换硬盘的数据线，但是无需打开机箱。

比较昂贵，配有 SCSI 控制卡的系统通常可以延伸 BIOS 的功能来让 SCSI 设备 (可达七个) 达到类似改变顺序的功能。

习惯于使用这种方式的使用者可能会感到惊讶， 因为在 FreeBSD 中并非如此。FreeBSD 不会参考 BIOS， 而且也不知道所谓的 “BIOS 逻辑磁盘对应” 是怎么回事。这会让人感觉很疑惑， 明明就是一样的硬盘而且资料也完全从另一块复制过来的， 结果却没办法像以前那样用。

当使用 FreeBSD 以前，请将 BIOS 中的硬盘开机顺序调回正常的顺序， 并且以后不要再改变。 如果一定要交换硬盘顺序， 那请用硬件的方式， 打开机箱并调整调线。

Bill 替 Fred 把旧的 Wintel 的机器装上了 FreeBSD。 他装了一台 SCSI 硬盘，ID 是 0，然后把 FreeBSD 装在上面。

Fred 开始使用他新的 FreeBSD 系统；但是过了几天， 他发现这旧的 SCSI 硬盘发生了许多小问题。之后， 他就跟 Bill 说起这件事。

又过了几天，Bill 决定是该解决问题的时候了， 所以他从后面房间的硬盘 “收藏” 中找出了一个一模一样的硬盘，并且经过表面测试后显示这块硬盘没有问题。 因此，Bill 将它的 ID 调成 4，然后安装到 Fred 的机器， 并且将资料从磁盘 0 复制到磁盘 4。现在新硬盘装好了， 而且看起来好像一切正常；所以，Bill 认为现在应该可以开始用它了。 Bill 于是到 SCSI BIOS 中设定 SCSI ID 4 为开机盘，用磁盘 4 重新开机后，一切跑得很顺利。

继续用了几天后，Bill 跟 Fred 决定要来玩点新的： 该将 FreeBSD 升级了。Bill 将 ID 0 的硬盘移除 (因为有问题) 并且又从收藏区中拿了一块一样的硬盘来。然后他用 Fred 神奇的网络 FTP 磁盘将新版的 FreeBSD 安装在这块硬盘上； 安装过程没什么问题发生。

Fred 用了这新版本几天后，觉得它很适合用在工程部门… 是时候将以前放在旧系统的工作资料复制过来了。 因此， Fred 将 ID4 的 SCSI 硬盘 (里面有放着旧系统中复制过来的最新资料) mount 起来，结果竟然发现在 ID4 的硬盘上， 他以前的所有资料都不见了！

资料跑到哪里去了呢？

当初 Bill 将 ID0 硬盘的资料复制到 ID4 的时候， ID4 即成为一个 “新的副本”。 而当他调 SCSI BIOS 设定 ID4 为开机盘，想让系统从 ID4 开机， 这其实只是他自己笨，因为大部分的系统可以直接调 BIOS 而改变开机顺序， 但是 FreeBSD 却会把开机顺序还原成正常的模式，因此，Fred 的 FreeBSD 还是从原来那块 ID0 的硬盘开机的。所有的资料都还在那块硬盘上， 而不是在想象之中的 ID4 硬盘。

幸运的是， 在我们发现这件事的时候那些资料都还在， 我们将这些资料从最早的那块 ID0 硬盘取出来并交还给 Fred， 而 Bill 也由此了解到计算机计数是从 0 开始的。

虽然我们这里的例子使用 SCSI 硬盘， 但是相同的概念也可以套用在 IDE 硬盘上。

### 2.6.2. 使用 FDisk 创建分区

### 注意:

如果不再做改变，数据将会写进硬盘。如果您犯了一个错误想重新开始， 请选择 sysinstall 安装程序的退出按钮(exit)。或按 **U** 键来 Undo 操作。 如果您的操作没有结果， 您总可以重新启动您的计算机来达到您的目的。

当您在 sysinstall 主菜单选择使用标准安装后，您会看到下面的信息：

```
                                 Message
 In the next menu, you will need to set up a DOS-style ("fdisk")
 partitioning scheme for your hard disk. If you simply wish to devote
 all disk space to FreeBSD (overwriting anything else that might be on
 the disk(s) selected) then use the (A)ll command to select the default
 partitioning scheme followed by a (Q)uit. If you wish to allocate only
 free space to FreeBSD, move to a partition marked "unused" and use the
 (C)reate command.
                                [  OK  ]

                      [ Press enter or space ]
```

如屏幕指示，按 **Enter** 键， 然后您就会看到一个列表列出所有在探测设备的时候找到的硬盘。 图 2.14 “选择要分区的硬盘” 范例显示的是有找到两个 IDE 硬盘的情形，这两个硬盘分别为 `ad0` 和 `ad2`。

![选择要分区的硬盘](img/fdisk-drive1.png)图 2.14. 选择要分区的硬盘

您可能正在奇怪，为什么 `ad1` 没有列出来？ 为什么遗失了呢？

试想，如果您有两个 IDE 硬盘，一个是在第一个 Primary master， 一个是 Secondary master，这样会发生什么事呢？ 如果 FreeBSD 依照找到的顺序来为他们命名，如 `ad0` 和 `ad1` 那么就不会有什么问题。

但是，现在问题来了。如果您现在想在 primary slave 加装第三个硬盘， 那么这个硬盘的名称就会是 `ad1`，之前的 `ad1` 就会变成 `ad2`。 这会造成什么问题呢？因为设备的名称 （如 `ad1s1a`）是用来寻找文件系统的， 因此您可能会发现，突然，您有些文件系统从此无法正确地显示出来， 必须修改 FreeBSD 配置文件（译注：/etc/fstab）才可以正确显示。

为了解决这些问题，在配置内核的时候可以叫 FreeBSD 直接用 IDE 设备所在的位置来命名，而不是依据找到的顺序。使用这种方式的话， 在 secondary master 的 IDE 设备就 *永远是* `ad2`，即使您的系统中没有 `ad0` 或 `ad1` 也不受影响。

此为 FreeBSD 内核的默认值，这也是为什么上面的画面只显示 `ad0` 和 `ad2` 的原因。 画面上这台机器的两颗硬盘是装在 primary 及 secondary 的 master 上面； 并没有任何一个硬盘安装在 slave 插槽上。

您应该选择您想安装 FreeBSD 的硬盘，然后按下 [ OK ]。之后 FDisk 就会开始，您会看到类似 图 2.15 “典型的尚未编辑前的 Fdisk 分区表”的画面。

FDisk 的显示画面分为三个部分。

第一部分是画面上最上面两行，显示的是目前所选择的硬盘的信息。 包含它的 FreeBSD 名称、硬盘分布以及硬盘的总容量。

第二部分显示的是目前选择的硬盘上有哪些分区， 每个分区的开始及结束位置、所占容量、FreeBSD 名称、 它们的描述以及类别（sub-type）。此范例显示有两个未使用的小分区， 还有一个大的 FAT 分区， （很可能是 MS-DOS® 或 Windows® 的 `C:` ）， 以及一个扩展分区（在 MS-DOS® 或 Windows® 里面还可以包含逻辑分区）。

第三个部分显示 FDisk 中可用的命令。

![典型的尚未编辑前的 Fdisk 分区表](img/fdisk-edit1.png)图 2.15. 典型的尚未编辑前的 Fdisk 分区表

接下来要做的事跟您要怎么给您的硬盘分区有关。

如果您要让 FreeBSD 使用整个硬盘（稍后您确认要 sysinstall 继续安装后会删除所有这个硬盘上的资料），那么您就可以按 **A** 键（Use Entire Disk ） 目前已有的分区都会被删除，取而代之的是一个小的，标示为 `unused` 的分区，以及一个大的 FreeBSD 分区。之后， 请用方向键将光标移到这个 FreeBSD 分区，然后按 **S** 以将此分区标记为启动分区。 您会看到类似 图 2.16 “Fdisk 分区使用整个硬盘” 的画面。注意，在 `Flags` 栏中的 `A` 记号表示此分区是 *激活* 的， 因而启动将从此分区进行。

要删除现有的分区以便为 FreeBSD 腾出空间， 您可以将光标移动到要删除的分区后按 **D** 键。 然后就可按 **C** 键， 并在弹出的对话框中输入将要创建的分区的大小。 输入合适的大小后按 **Enter** 键。 一般而言， 这个对话框中的初始值是可以分配给该分区的最大值。 它可能是最大的邻接分区或未分配的整个硬盘大小。

如果您已经建立好给 FreeBSD 的分区 （使用像 PartitionMagic®类似的工具）， 那么您可以按下 **C** 键来建立一个新的分区。同样的， 会有对话框询问您要建立的分区的大小。

![Fdisk 分区使用整个硬盘](img/fdisk-edit2.png)图 2.16. Fdisk 分区使用整个硬盘

完成后，按 **Q** 键。您的变更会存在 sysinstall 中， 但是还不会真正写入您的硬盘。

### 2.6.3. 安装多重引导

在这步骤您可以选择要不要安装一个多重引导管理器。 一般而言，如果碰到下列的情形， 您应该选择要安装多重引导管理程序。

*   您有一个以上的硬盘，并且 FreeBSD 并不是安装在第一个硬盘上。

*   除了 FreeBSD，您还有其它的操作系统安装在同一块硬盘上， 所以您需要在开机的时候选择要进入哪一个系统。

如果您在这台机器上只安装一个 FreeBSD 操作系统， 并且安装在第一个硬盘， 那么选择 Standard 安装就可以了。如果您已经使用了一个第三方的多重引导程序， 那么请选择 None。

选择好配置后请按 **Enter**。

![Sysinstall 多重引导管理程序](img/boot-mgr.png)图 2.17. Sysinstall 多重引导管理程序

按下 **F1** 键所显示的在线说明中有讨论一些操作系统共存可能发生的问题。

### 2.6.4. 在其它硬盘上创建分区

如果您的系统上有一个以上的硬盘， 在选择完多重引导管理程序后会再回到选择硬盘的画面。 如果您要将 FreeBSD 安装在多个硬盘上，那么您可以在这里选择其它的硬盘， 然后重复使用 FDisk 来建立分区。

### 重要:

如果您想让 FreeBSD 来管理其它的硬盘， 那么两个硬盘都必须安装 FreeBSD 的多重引导管理程序。

![离开选择硬盘画面](img/fdisk-drive2.png)图 2.18. 离开选择硬盘画面

**Tab** 键可以在您最后选择的硬盘、 [ OK ] 以及 [ Cancel ] 之间进行切换。

用 **Tab** 键将光标移动到 [ OK ] 然后按 **Enter** 键继续安装过程。

### 2.6.5. 使用 bsdlabel 创建分区

您现在必须在刚刚建立好的 slice 中规划一些 label。 请注意，每个 label 的代号是 `a` 到 `h`，另外，习惯上 `b`、 `c` 和 `d` 是有特殊用途的，不应该随意变动。

某些应用程序可以利用一些特殊的分区而达到较好的效果， 尤其是分区分散在不同的硬盘的时候。但是，现在您是第一次安装 FreeBSD， 所以不需要去烦恼如何分割您的硬盘。最重要的是， 装好 FreeBSD 然后学习如何使用它。当您对 FreeBSD 有相当程度的熟悉后， 您可以随时重新安装 FreeBSD，然后改变您分区的方式。

下面的范例中有四个分区 ── 一个是磁盘交换分区，另外三个是文件系统。

表 2.2. 为第一个硬盘分区

| 分区 | 文件系统 | 大小 | 描述 |
| --- | --- | --- | --- |
| `a` | `/` | 1 GB | 这是一个根文件系统（root filesystem）。 任何其它的文件系统都会 挂在根目录（译注：用根目录比较亲切） 下面。 1 GB 对于此目录来说是合理的大小， 因为您往后并不会在这里存放太多的数据； 在安装 FreeBSD 后会用掉约 128 MB 的根目录空间。 剩下的空间是用来存放临时文件用的，同时， 您也应该预留一些空间，因为以后的 FreeBSD 版本可能会需要较多的 `/`（根目录）空间。 |
| `b` | N/A | 2-3 x RAM | `b` 分区为系统磁盘交换分区 （swap space）。选择正确的交换空间大小可是一门学问唷。 一般来说，交换空间的大小应该是您系统上内存（RAM） 大小的 2 到 3 倍。 交换空间至少要有 64 MB。因此， 如果您的电脑上的 RAM 比 32 MB 小， 请将交换空间大小设为 64 MB。如果您有一个以上的硬盘， 您可以在每个硬盘上都配置交换分区。FreeBSD 会利用每个硬盘上的交换空间，这样做能够提高 swap 的性能。 如果是这种情形， 先算出您总共需要的交换空间大小 （如 128 MB），然后除以您拥有的硬盘数目（如 2 块）， 算出的结果就是每个硬盘上要配置的交换空间的大小。 在这个例子中， 每个硬盘的交换空间为 64 MB。 |
| `e` | `/var` | 512 MB 至 4096 MB | `/var` 目录会存放不同长度的文件、 日志以及其它管理用途的文件。大部分这些文件都是 FreeBSD 每天在运行的时候会读取或是写入的。 当这些文件放在另外的文件系统（译注：即/var） 可以避免影响到其它目录下面类似的文件存取机制。 |
| `f` | `/usr` | 剩下的硬盘空间 （至少 8 GB） | 您所有的其它的文件通常都会存在`/usr` 目录以及其子目录下面。 |

### 警告:

上面例子中的数值仅限于有经验的用户使用。 通常我们鼓励用户使用 FreeBSD 分区编辑器中一个叫做 `Auto Defaults`的自动分区布局功能。

如果您要将 FreeBSD 安装在一个以上的硬盘， 那么您必须在您配置的其它分区上再建立分区。 最简单的方式就是在每个硬盘上建立两个分区，一个是交换分区， 一个是文件系统分区。

表 2.3. 为其它磁盘分区

| 分区 | 文件系统 | 大小 | 描述 |
| --- | --- | --- | --- |
| `b` | N/A | 见描述 | 之前提过，交换分区是可以跨硬盘的。但是，即使 `a` 分区没有使用，习惯上还是会把交换分区放在 `b` 分区上。 |
| `e` | /disk*`n`* | 剩下的硬盘空间 | 剩下的空间是一个大的分区，最简单的做法是将之规划为 `a`分区而不是`e`分区。然而， 习惯上`a`分区是保留给根目录 (`/`) 用的。您不一定要遵守这个习惯，但是 sysinstall 会， 所以照着它做会使您的安装比较清爽、干净。 您可以将这些文件系统挂在任何地方，本范例建议将它们挂在 `/diskn` 目录，*`n`* 依据每个硬盘而有所不同， 但是，您喜欢的话也可将它们挂在别的地方。 |

分区的配置完成后，您可以用 sysinstall. 来建立它们了。您会看到下面的信息：

```
                                 Message
 Now, you need to create BSD partitions inside of the fdisk
 partition(s) just created. If you have a reasonable amount of disk
 space (1GB or more) and don't have any special requirements, simply
 use the (A)uto command to allocate space automatically. If you have
 more specific needs or just don't care for the layout chosen by
 (A)uto, press F1 for more information on manual layout.

                                [  OK  ]
                          [ Press enter or space ]
```

按下 **Enter** 键开始 FreeBSD 分区表编辑器，称做 Disklabel。

图 2.19 “Sysinstall Disklabel 编辑器” 显示您第一次执行 Disklabel 的画面。 画面分为三个区域。

前几行显示的是您正在编辑的硬盘以及您正在建立的 slice 位于哪个分区上。（在这里，Disklabel 使用的是 `分区名称` 而不是 slice 名）。 此画面也会显示 slice 还有多少空间可以使用；亦即，有多余的空间， 但是尚未指派分区。

画面中间区域显示已建立的区区，每个分区的文件系统名称、 所占的大小以及一些关于建立这些文件系统的参数选项。

下方的第三区显示在 Disklabel 中可用的按键。

![Sysinstall Disklabel 编辑器](img/disklabel-ed1.png)图 2.19. Sysinstall Disklabel 编辑器

Disklabel 您可以自动配置分区以及给它们预设的大小。 这些默认的分区是由内部的分区尺寸算法根据磁盘的大小计算出的。 您可以按 **A**键使用此功能。您会看到类似 图 2.20 “Sysinstall Disklabel 编辑器-使用自动配置”的画面。根据您硬盘的大小， 自动分配所配置的大小不一定合适。但是没有关系， 您并不一定要使用预设的大小。

### 注意:

默认情况下会给`/tmp` 目录一个独立分区，而不是附属在 `/` 之下。 这样可以避免将一些临时文件放到根目录中（译注： 可能会用完根目录空间）。

![Sysinstall Disklabel 编辑器-使用自动配置](img/disklabel-auto.png)图 2.20. Sysinstall Disklabel 编辑器-使用自动配置

如果您不想使用默认的分区布局， 则需要用方向键移动光标并选中第一个分区， 然后按 **D** 来删除它。 重复这一过程直到删除了所有推荐的分区。

要建立第一个分区 (`a`，作为 `/` ── 根文件系统)， 请确认您已经在屏幕顶部选中了正确的 slice， 然后按 **C**。 接下来将出现一个对话框， 要求您输入新分区的尺寸 (如 图 2.21 “根目录使用空间” 所示)。 您可以输入以块为单位的尺寸，或以 `M` 表示 MB、 `G` 结尾表示 GB， 或者 `C` 表示柱面数的方式来表达尺寸。

![根目录使用空间](img/disklabel-root1.png)图 2.21. 根目录使用空间

如果使用此处显示的默认尺寸， 则会创建一个占满整个 slice 空余空间的 partition。如果希望使用前面例子中描述的 partition 尺寸， 则应按 **Backspace** 键删除这些数字， 并输入 **`512M`**， 如 图 2.22 “编辑要分区大小” 所示。 然后， 按下 [ OK ]。

![编辑要分区大小](img/disklabel-root2.png)图 2.22. 编辑要分区大小

输入完大小后接着问您要建立的分区是文件系统还是交换空间，如 图 2.23 “选择根分区类型”所示。第一个分区是文件系统， 所以确认选择 FS 后按**Enter** 键。

![选择根分区类型](img/disklabel-fs.png)图 2.23. 选择根分区类型

最后，因为您要建立的是一个文件系统，所以必须告诉 Disklabel 这个文件系统要挂接在什么地方，如 图 2.24 “选择根挂接点”所示。根文件系统的挂接点 `/`, 所以请输入 **`/`**,然后按 **Enter**键。

![选择根挂接点](img/disklabel-root3.png)图 2.24. 选择根挂接点

刚刚制作好的分区会显示在画面上。 您应该重复上述的动作以建立其它的分区。当建立交换空间的时候， 系统不会问您要将它挂接在哪里，因为交换空间是不用挂在系统上的。 当您在建立最后一个分区`/usr`的时候， 您可以直接使用默认的大小，即所有此分区剩余的空间。

您最终的 FreeBSD DiskLabel 编辑器画面会类似 图 2.25 “Sysinstall Disklabel 编辑器”, 实际数字按您的选择而有所不同。 按下 **Q** 键完成分区的建立。

![Sysinstall Disklabel 编辑器](img/disklabel-ed2.png)图 2.25. Sysinstall Disklabel 编辑器

## 2.7. 选择要安装的软件包

### 2.7.1. 选择要安装的软件包

安装哪些软件包在很大程度上取决于系统将被用来做什么， 以及有多少可用的磁盘空间。内建的选项包括了运行所需要的最小系统， 到把所有软件包全都装上的常用配置。UNIX® 或 FreeBSD 新手通常直接选择一个设定好的软件包就可以了， 而有经验的使用者则可以考虑自己订制安装哪些软件包。

按下 **F1** 可以看到有关软件包的更多选项信息， 以及它们都包含了哪些软件，之后，可以按 **Enter** 回到软件包选择画面。

如果需要图形用户界面， 则配置 X 服务以及选择默认桌面需要在完成 FreeBSD 之后完成。 关于安装和配置 X 服务的信息， 可以在 第 6 章 *X Window 系统* 找到。

如果需要定制内核， 您还需要选择包含源代码的那个选项。 要了解为什么应该编译和构建新的内核， 请参见 第 9 章 *配置 FreeBSD 的内核*。

显然， 包含所有组件的系统是最万能的。 如果磁盘空间足够， 用光标键选择 图 2.26 “选择软件包” 中的 All 并按 **Enter**。 如果担心磁盘空间不够的话， 则选择最合适的选项。 不要担心选择的是否是最合适的， 因为其他软件包可以在安装完毕后再加入进来。

![选择软件包](img/dist-set.png)图 2.26. 选择软件包

### 2.7.2. 安装 ports 软件包

当选择完您想要安装的部分后，接着会询问您要不要安装 FreeBSD Ports 软件包；Ports 软件包可以让您简单方便地安装软件包。Ports 本身并不包含编辑 软件所需要的程序源代码，而是一个包含自动下载、编辑以及安装的文档集合。 第 5 章 *安装应用程序: Packages 和 Ports* 一章讨论如何使用 Ports.

安装程序并不会检查您是否有足够的硬盘空间, 在选择这一项之前请先确定您有足够的硬盘空间。 目前 FreeBSD 10.3 版本中， FreeBSD Ports Collection 大约占用 500 MB 大小的硬盘空间。 对于近期的版本您可能需要更多一些空间来安装他们。

```
                         User Confirmation Requested
 Would you like to install the FreeBSD Ports Collection?

 This will give you ready access to over 24,000 ported software packages,
 at a cost of around 500 MB of disk space when "clean" and possibly much
 more than that if a lot of the distribution tarballs are loaded
 (unless you have the extra CDs from a FreeBSD CD/DVD distribution
 available and can mount it on /cdrom, in which case this is far less
 of a problem).

 The Ports Collection is a very valuable resource and well worth having
 on your /usr partition, so it is advisable to say Yes to this option.

 For more information on the Ports Collection & the latest ports,
 visit:
     http://www.FreeBSD.org/ports

                              [ Yes ]     No
```

选择 [ Yes ] 将会安装 Ports Collection， 而选择 [ No ] 则将跳过它。 选好后按 **Enter** 继续。 此后， 选择安装的软件包的屏幕将再次出现。

![确认您要安装的软件包](img/dist-set2.png)图 2.27. 确认您要安装的软件包

如果对您的选择感到满意，请选择 Exit 退出，确保[ OK ] 被高亮显示，然后按**Enter** 继续。

## 2.8. 选择您要使用的安装介质

如果要从 CDROM 或 DVD 安装，使用方向键将光标移到 Install from a FreeBSD CD/DVD。确认 [ OK ] 被选取，然后按 **Enter** 开始安装程序。

如果要使用其它的方式安装， 请选择适当的安装介质然后按照屏幕指示进行安装。

按 **F1** 可以显示安装介质的在线说明。按一下 **Enter** 可返回选择安装介质画面。

![选择安装介质](img/media.png)图 2.28. 选择安装介质

### FTP 安装模式:

使用 FTP 安装，有三种方式：主动式（active）FTP、被动式（passive）FTP 或是透过 HTTP 代理服务器。

主动式 FTP： 从 FTP 服务器安装

这个选项将会使所有的 FTP 传输使用 “Active”模式。 这将无法通过防火墙，但是可以使用在那些比较早期， 不支持被动模式的 FTP 站。如果您的连接在使用被动（默认值） 模式卡住了，请换主动模式看看！

被动模式 FTP：通过防火墙从 FTP 服务器安装

此选项会让 sysinstall 使用 “Passive”模式来安装。这使得使用者可以穿过 不允许用非固定 TCP PORTS 连入的防火墙。

FTP 透过 HTTP 代理服务器： 透过 HTTP 代理服务器， 由 FTP 服务器安装

此选项会让 sysinstall 通过 HTTP 协议 （像浏览器一样）连到 proxy 服务器。 proxy 服务器会解释送出的请求，然后通知 FTP 服务器。 因为通过 HTTP 协议，所以可以穿过防火墙。 要用这种方式，您必须指定 proxy 服务器的地址。

对于一个 FTP 代理服务器而言， 通常在使用者登入名称中加入您要登入的服务器的用户名， 加在 “@” 符号后面。然后代理服务器就会 “假装” 成一个真的服务器。例如，假设您要从 `ftp.FreeBSD.org` 安装，通过 FTP 代理服务器 `foo.example.com`， 使用 1234 端口。

在这种情况下，您可以到 options 菜单，将 FTP username 设为 `ftp@ftp.FreeBSD.org`，密码设为您的电子邮件地址。 安装介质部分，指定 FTP (或是被动式 FTP，如果代理服务器支持的话) 以及 URL 为 `ftp://foo.example.com:1234/pub/FreeBSD`。

因为`ftp.FreeBSD.org`的 `/pub/FreeBSD` 目录会被抓取到 `foo.example.com`之下，您就可以从 *这台* 机器 (会从 `ftp.FreeBSD.org` 抓取文件) 安装。

## 2.9. 安装确认

到此为止，可以开始进行安装了， 这也是您避免更动到您的硬盘的最后机会。

```
                       User Confirmation Requested
 Last Chance! Are you SURE you want to continue the installation?

 If you're running this on a disk with data you wish to save then WE
 STRONGLY ENCOURAGE YOU TO MAKE PROPER BACKUPS before proceeding!

 We can take no responsibility for lost disk contents!

                             [ Yes ]    No
```

选择 [ Yes ] 然后按下 **Enter** 确认安装

安装所需的时间会根据您所选择的软件、 安装介质以及您电脑的速度而有所不同。 在安装的过程中会有一些信息来显示目前的进度。

当您看到下面的信息表示已经安装完成了：

```
                               Message

Congratulations! You now have FreeBSD installed on your system.

We will now move on to the final configuration questions.
For any option you do not wish to configure, simply select No.

If you wish to re-enter this utility after the system is up, you may
do so by typing: /usr/sbin/sysinstall.

                                 [ OK ]

                      [  Press enter or space  ]
```

按下 **Enter** 以进行安装后的配置。

选择 [ No ] 然后按 **Enter** 会取消安装，不会对您的系统造成更动。您会看到下面的信息：

```
                                Message
Installation complete with some errors.  You may wish to scroll
through the debugging messages on VTY1 with the scroll-lock feature.
You can also choose "No" at the next prompt and go back into the
installation menus to retry whichever operations have failed.

                                 [ OK ]
```

产生这个信息是因为什么东西也没有安装，按下 **Enter** 后会离开安装程序回到主安装界面。从主安装界面可以退出安装程序。

## 2.10. 安装后的配置

安装成功后， 就可以进行进一步的配置了。 引导新安装的 FreeBSD 系统之后， 使用 `sysinstall` 并选择 Configure。

### 2.10.1. 配置网卡

如果您之前配置用 PPP 通过 FTP 安装，那么这个画面将不会出现； 正像所说的那样，您可以稍后再做配置。

如果想更多的了解网卡或将 FreeBSD 配置为网关或路由器，请参考 Advanced Networking 的相关文章。

```
                      User Confirmation Requested
   Would you like to configure any Ethernet or PPP network devices?

                             [ Yes ]   No
```

如果要配置网卡，请选择 [ Yes ] 然后按 **Enter**。 否则请选择 [ No ] 继续。

![选择网卡设备](img/ed0-conf.png)图 2.29. 选择网卡设备

用方向键选择您要配置的网卡接口，然后按**Enter**。

```
                      User Confirmation Requested
       Do you want to try IPv6 configuration of the interface?

                              Yes   [ No ]
```

目录私人区域网络 IP 协议 IPv4 已经足够，所以选择 [ No ] 然后按 **Enter**。

如果想试试新的 IP 通信协议 IPv6 ， 使用 RA 服务，请选择 [ Yes ] 然后按 **Enter**。 寻找 RA 服务器将会花费几秒的时间。

```
                             User Confirmation Requested
        Do you want to try DHCP configuration of the interface?

                              Yes   [ No ]
```

如果您不需要 DHCP (Dynamic Host Configuration Protocol　动态主机配置协议) ，选择 [ No ] 然后按**Enter**。

选择 [ Yes ] 会执行 dhclient， 如果成功，它会自动将网络配置信息填上。更多的信息请参考 第 30.5 节 “网络自动配置 (DHCP)”") 。

下面的网络配置显示了怎样把以太网设备配置成区域网络网关的角色。

![配置 ed0 接口](img/ed0-conf2.png)图 2.30. 配置 ed0 接口

使用**Tab** 键可以在各个栏目之间进行切换，请输入适当 的信息：

Host（机器名称）

完整的机器名称，例如本例中的 `k6-2.example.com` 。

Domain（域名）

您机器所在的域名称，如本例的 `example.com`

IPv4 Gateway（IPv4 网关）

输入将数据包传送到远端网络的机器 IP 地址。 只有当机器是网络上的一个节点时才要输入。 如果这台机器要作为您局域网的网关， *请将此处设为空白*。IPv4 网关， 也被称作默认网关或默认路由器。

域名服务器

本地网络中的域名服务器的 IP 地址。 本例中假设机器所在的网络中没有域名服务器， 所以填入的是 ISP 提供的域名服务器地址 （`208.163.10.2`。）

IPv4 地址

本机所使用的 IP 地址。本例为 `192.168.0.1`。

子网掩码

在这个局域网中所使用的地址块是 `192.168.0.0` - `192.168.0.255`， 对应的子网掩码是 `255.255.255.0`。

ifconfig 额外参数设定

任何`ifconfig`命令跟网卡接口有关的参数。 本范例中没有。

使用 **Tab** 键选择 [ OK ]然后按 **Enter**键。

```
                      User Confirmation Requested
        Would you like to bring the ed0 interface up right now?

                             [ Yes ]   No
```

选择 [ Yes ] 然后按 **Enter** 将会将机器的网卡转为启用状态。 机器下次启动的时候即可使用。

### 2.10.2. 配置网关

```
                       User Confirmation Requested
       Do you want this machine to function as a network gateway?

                              [ Yes ]    No
```

如果这台机器要作为本地网络和其它机器之间传送数据包的网关，请选择 [ Yes ] 然后按 **Enter**。 如果这台机器只是网络上的普通节点，请选择 [ No ] 并按 **Enter** 继续。

### 2.10.3. 配置网络服务

```
                      User Confirmation Requested
Do you want to configure inetd and the network services that it provides?

                               Yes   [ No ]
```

如果选择 [ No ]， 许多网络服务，如 telnetd 将不会启用。 这样， 远端用户将无法 telnet 进入这台机器。 本机上的用户还是可以 telnet 到远端机器的。

这些服务可以在安装完成后修改`/etc/inetd.conf` 配置文件来启用它们。请参阅 第 30.2.1 节 “总览” 以获得更多的信息。

如果您想现在就配置这些网络服务，请选择 [ Yes ]， 然后会看到下面的信息：

```
                      User Confirmation Requested
The Internet Super Server (inetd) allows a number of simple Internet
services to be enabled, including finger, ftp and telnetd.  Enabling
these services may increase risk of security problems by increasing
the exposure of your system.

With this in mind, do you wish to enable inetd?

                             [ Yes ]   No
```

选择 [ Yes ] 继续。

```
                      User Confirmation Requested
inetd(8) relies on its configuration file, /etc/inetd.conf, to determine
which of its Internet services will be available.  The default FreeBSD
inetd.conf(5) leaves all services disabled by default, so they must be
specifically enabled in the configuration file before they will
function, even once inetd(8) is enabled.  Note that services for
IPv6 must be separately enabled from IPv4 services.

Select [Yes] now to invoke an editor on /etc/inetd.conf, or [No] to
use the current settings.

                             [ Yes ]   No
```

选择 [ Yes ] 将允许您添加网络服务 (或将相应网络服务每行开头的 `#` 除掉即可。)

![编辑 inetd.conf 配置文件](img/edit-inetd-conf.png)图 2.31. 编辑 `inetd.conf`配置文件

在加入您想启用的服务后，按下 **Esc**键会出现一个 对话框可以让您离开以及保存修改。

### 2.10.4. 启用 SSH 登录

```
                      User Confirmation Requested
                  Would you like to enable SSH login?
                           Yes        [  No  ]
```

选择 [ Yes ] 便会启用 [sshd(8)](http://www.FreeBSD.org/cgi/man.cgi?query=sshd&sektion=8&manpath=freebsd-release-ports)， 也就是 OpenSSH 服务程序。 它能够让您以安全的方式从远程访问机器。 如欲了解关于 OpenSSH 的进一步详情， 请参见 第 15.10 节 “OpenSSH”。

### 2.10.5. 匿名 FTP

```
                      User Confirmation Requested
 Do you want to have anonymous FTP access to this machine?

                              Yes    [ No ]
```

#### 2.10.5.1. 不允许匿名 FTP 访问

选择默认的 [ No ] 并按下 **Enter** 键将仍然可以让在这台机器上有账号的用户访问 FTP。

#### 2.10.5.2. 允许匿名 FTP 访问

如果您选择允许匿名 FTP 存取， 那么网络中任何人都可以使用 FTP 来访问您的机器。 在启用匿名访问之前应该考虑网络的安全问题。 如果要知道更多有关网络安全的信息， 请参阅 第 15 章 *安全*。

要启用 FTP 匿名访问，用方向键选择 [ Yes ] 并按 **Enter**键。 系统会给出进一步的确认信息：

```
                       User Confirmation Requested
 Anonymous FTP permits un-authenticated users to connect to the system
 FTP server, if FTP service is enabled.  Anonymous users are
 restricted to a specific subset of the file system, and the default
 configuration provides a drop-box incoming directory to which uploads
 are permitted.  You must separately enable both inetd(8), and enable
 ftpd(8) in inetd.conf(5) for FTP services to be available.  If you
 did not do so earlier, you will have the opportunity to enable inetd(8)
 again later.

 If you want the server to be read-only you should leave the upload
 directory option empty and add the -r command-line option to ftpd(8)
 in inetd.conf(5)

 Do you wish to continue configuring anonymous FTP?

                          [ Yes ]         No
```

这些信息会告诉您 FTP 服务还需要在 `/etc/inetd.conf` 中启用。 假如您希望允许匿名 FTP 连接， 请参见 第 2.10.3 节 “配置网络服务”。 选择 [ Yes ] 并按 **Enter** 继续； 系统将给出下列信息：

![默认的匿名 FTP 配置](img/ftp-anon1.png)图 2.32. 默认的匿名 FTP 配置

使用 **Tab** 在不同的信息字段之间切换， 并填写必要的信息：

UID

用于分配给匿名 FTP 用户的用户 ID。 所有上传的文件的属主都将是这个 ID。

Group

匿名 FTP 用户所在的组。

Comment

用于在 `/etc/passwd` 中描述该用户的说明性信息。

FTP Root Directory

可供匿名 FTP 用户使用的文件所在的根目录。

Upload Subdirectory

匿名 FTP 用户上传的文件的存放位置。

默认的 FTP 根目录将放在 `/var` 目录下。 如果您的 /var 目录空间不足以应付您的 FTP 需求， 您可以将 FTP 的根目录改为 `/usr` 目录下的 `/usr/ftp` 目录。

当您对一切配置都满意后，请按 **Enter** 键继续。

```
                          User Confirmation Requested
         Create a welcome message file for anonymous FTP users?

                              [ Yes ]    No
```

如果您选择 [ Yes ] 并按下 **Enter**键， 系统会自动打开文本编辑器让您编辑 FTP 的欢迎信息。

![编辑 FTP 欢迎信息](img/ftp-anon2.png)图 2.33. 编辑 FTP 欢迎信息

此文本编辑器叫做 `ee`。 按照指示修改信息文本或是稍后再用您喜爱的文本编辑器来修改。 请记住画面下方显示的文件位置。

按 **Esc** 将弹出一个默认为 a) leave editor 的对话框。按 **Enter** 退出并继续。再次按 **Enter** 将保存修改。

### 2.10.6. 配置网络文件系统

网络文件系统 (NFS) 可以让您可以在网络上共享您的文件。 一台机器可以配置成 NFS 服务器、客户端或两者并存。请参考 第 30.3 节 “网络文件系统（NFS）” 以获得更多的信息。

#### 2.10.6.1. NFS 服务器

```
                       User Confirmation Requested
 Do you want to configure this machine as an NFS server?

                              Yes    [ No ]
```

如果您不想安装网络文件系统，请选择 [ No ] 然后按 **Enter**键。

如果您选择 [ Yes ] 将会出现一个对话框提醒您必须先建立一个 `exports` 文件。

```
                               Message
Operating as an NFS server means that you must first configure an
/etc/exports file to indicate which hosts are allowed certain kinds of
access to your local filesystems.
Press [Enter] now to invoke an editor on /etc/exports
                               [ OK ]
```

按 **Enter** 键继续。系统会启动文本编辑器让您编辑 `exports` 文件。

![编辑 exports 文件](img/nfs-server-edit.png)图 2.34. 编辑 `exports`文件

按照指示加入真实输出的文件目录或是稍后用您喜爱的编辑器自行编辑。 请记下画面下方显示的文件名称及位置。

按下 **Esc** 键会出现一具对话框，默认选项是 a) leave editor。按下 **Enter** 离开并继续。

#### 2.10.6.2. NFS 客户端

NFS 客户端允许您的机器访问 NFS 服务器。

```
                       User Confirmation Requested
 Do you want to configure this machine as an NFS client?

                              Yes   [ No ]
```

按照您的需要，选择 [ Yes ] 或 [ No ] 然后按 **Enter**。

### 2.10.7. 配置系统终端

系统提供了几个选项可以让您配置终端的表现方式。

```
                      User Confirmation Requested
       Would you like to customize your system console settings?

                              [ Yes ]  No
```

要查阅及配置这些选项，请选择 [ Yes ] 并按**Enter**。

![系统终端配置选项](img/console-saver1.png)图 2.35. 系统终端配置选项

最常用的选项就是屏幕保护程序了。使用方向键将光标移动到 Saver 然后按 **Enter**。

![屏幕保护程序选项](img/console-saver2.png)图 2.36. 屏幕保护程序选项

选择您想使用的屏幕保护程序，然后按 **Enter**。 之后回到系统终端配置画面。

默认开启屏幕保护程序的时间是 300 秒。如果要更改此时间，请再次选择 Saver 。然后选择 Timeout 并按 **Enter**键。系统会弹出一个对话框如下：

![屏幕保护时间设置](img/console-saver3.png)图 2.37. 屏幕保护时间设置

您可以直接改变这个值，然后选 [ OK ]并按 **Enter** 键回到系统终端配置画面。

![退出系统终端配置](img/console-saver4.png)图 2.38. 退出系统终端配置

选择 Exit 然后按下 **Enter** 键会回到安装后的配置画面。

### 2.10.8. 配置时区

配置您机器的时区可以让系统自动校正任何区域时间的变更， 并且在执行一些跟时区相关的程序时不会出错。

例子中假设此台机器位于美国东部的时区。 请参考您所在的地理位置来配置。

```
                      User Confirmation Requested
          Would you like to set this machine's time zone now?

                            [ Yes ]   No
```

选择 [ Yes ] 并按下 **Enter**键以配置时区。

```
                       User Confirmation Requested
 Is this machine's CMOS clock set to UTC? If it is set to local time
 or you don't know, please choose NO here!

                              Yes   [ No ]
```

这里按照您机器时间的配置，选择 [ Yes ] 或 [ No ] 然后按 **Enter**。

![选择您所处的地理区域](img/timezone1.png)图 2.39. 选择您所处的地理区域

请选择适当的区域然后按 **Enter**。

![选择您所在的国家](img/timezone2.png)图 2.40. 选择您所在的国家

选择您所在的国家然后按 **Enter**。

![选择您所在的时区](img/timezone3.png)图 2.41. 选择您所在的时区

选择您所在的时区然后按 **Enter**。

```
                            Confirmation
            Does the abbreviation 'EDT' look reasonable?

                            [ Yes ]   No
```

检查一下时区的缩写是否正确，如果没错，请按 **Enter** 返回系统安装后的配置画面。

### 2.10.9. Linux 兼容性

### 注意:

这节内容只适用于 FreeBSD 7.X 安装过程， 如果您安装的是 FreeBSD 8.X 或更高版本， 系统不会给出这个提示。

```
                      User Confirmation Requested
          Would you like to enable Linux binary compatibility?

                            [ Yes ]   No
```

选择 [ Yes ] 并按下**Enter** 键， 将允许您在 FreeBSD 中执行 Linux 的软件。安装程序会安装一些为了跟 Linux 兼容的软件包。

如果您是通过 FTP 安装，那么您必须连到网络上。 有时候 FTP 站并不会包含所有的安装软件包（例如 Linux 兼容软件包）； 不过，稍后您还可以再安装这个项目。

### 2.10.10. 配置鼠标

此选项可以让您在终端上使用三键鼠标剪贴文字。 如果您用的鼠标是两个按钮，请参考手册 [moused(8)](http://www.FreeBSD.org/cgi/man.cgi?query=moused&sektion=8&manpath=freebsd-release-ports)； 以取得有关模拟三键鼠标的信息。范例中使用的鼠标不是 USB 接口。 （例如 ps/2 或 com 接口的鼠标）：

```
                      User Confirmation Requested
         Does this system have a PS/2, serial, or bus mouse?

                            [ Yes ]    No 
```

如果您使用的是 PS/2、 串口或 Bus 鼠标，请选择 [ Yes ]， 如果是 USB 鼠标， 则应选择 [ No ] 并按 **Enter**。

![选择鼠标类型](img/mouse1.png)图 2.42. 选择鼠标类型

使用方向键选择 Type 然后按 **Enter**。

![设置鼠标协议](img/mouse2.png)图 2.43. 设置鼠标协议

在这个例子中使用的类型是 ps/2 鼠标，所以可以使用默认的 Auto（自动） 。 您可以用方向键选择合适的项目，确定选择了 [ OK ] 后按 **Enter** 键离开此画面。

![配置鼠标端口](img/mouse3.png)图 2.44. 配置鼠标端口

选择 Port 然后按 **Enter**。

![配置鼠标端口](img/mouse4.png)图 2.45. 配置鼠标端口

假设这台机器用的是 ps/2 鼠标，您可以采用默认的 PS/2 选项。请选择适当的项目然后按 **Enter**。

![启动鼠标服务进程](img/mouse5.png)图 2.46. 启动鼠标服务进程

选择 Enable 然后按 **Enter** 来启动和测试鼠标。

![测试鼠标功能](img/mouse6.png)图 2.47. 测试鼠标功能

鼠标指针可以在屏幕上移动，指明鼠标服务已经正常启用。那么请选择 [ Yes ] 按 **Enter**键。否则鼠标没 有配置成功 ── 选择 [ No ] 并尝试不同的配置 选项。

选择 Exit 并按 **Enter** 退回到系统安装完成后的配置画面。

### 2.10.11. 安装预编译的软件包 (package)

Package 是事先编译好的二进制文件， 因此， 这是安装软件的一种便捷的方式。

在这里作为例子我们将给出安装一个 package 所需的过程。 如果需要， 还可以在这一阶段加入其他 package。 安装完成之后， `sysinstall` 依然可以用来安装其他 package。

```
                     User Confirmation Requested
 The FreeBSD package collection is a collection of hundreds of
 ready-to-run applications, from text editors to games to WEB servers
 and more. Would you like to browse the collection now?

                            [ Yes ]   No
```

选择 [ Yes ] 并按 **Enter** 将进入 package 选择界面：

![选择 Package 类别](img/pkg-cat.png)图 2.48. 选择 Package 类别

在任何时候， 只有当前安装介质上存在的 package 才可以安装。

如果选择了 All 或某个特定的分类， 则系统会列出全部可用的 package。 用光标键移动光棒选中需要的 package， 并按 **Enter**。

系统会显示可供选择的 package：

![选择 Package](img/pkg-sel.png)图 2.49. 选择 Package

如图所示， 我们选择了 bash shell。 您可以根据需要使用 **Space** 键来勾选选定的 package。 在屏幕左下角会给出 package 的简短说明。

反复按下 **Tab** 键， 可以在最后选中的 package、 [ OK ] 和 [ Cancel ] 之间来回切换。

当您把需要的 package 都标记为安装之后， 按一下 **Tab** 切换到 [ OK ]， 随后按下 **Enter** 就可以回到 package 选择菜单了。

左右方向键可以用于在 [ OK ] 和 [ Cancel ] 之间进行切换。 这种方法也可以用来选择 [ OK ]， 随后按下 **Enter** 也可以回到 package 选择菜单。

![安装预编译软件包](img/pkg-install.png)图 2.50. 安装预编译软件包

使用 **Tab** 和左右方向键选择 [ Install ] 并按 **Enter**。 接下来需要确认将要安装的预编译包：

![确认将要安装的预编译包](img/pkg-confirm.png)图 2.51. 确认将要安装的预编译包

选择 [ OK ] 并按下 **Enter** 就可以开始预编译包的安装了。在这个过程中您会看到安装的相关信息， 直到安装完成为止。请留意观察是否有错误信息出现。

在完成预编译包的安装之后， 就进入了最后的配置阶段。 如果您没有选择任何预编译包， 并希望直接进入最后的配置阶段， 则可以选择 Install 来跳过。

### 2.10.12. 添加用户和组

在安装系统的过程中， 您应添加至少一个用户， 以避免直接以 `root` 用户的身份登录。 用以保存其用户数据的根分区通常很小， 因此用 `root` 身份运行程序可能将其迅速填满。 下面的提示信息介绍了这样做可能带来的更大隐患：

```
                     User Confirmation Requested
 Would you like to add any initial user accounts to the system? Adding
 at least one account for yourself at this stage is suggested since
 working as the "root" user is dangerous (it is easy to do things which
 adversely affect the entire system).

                            [ Yes ]   No
```

选择 [ Yes ] 并按 **Enter** 即可开始创建用户的过程。

![选择用户](img/adduser1.png)图 2.52. 选择用户

用箭头键来选择 User 然后按 **Enter**。

![添加用户信息](img/adduser2.png)图 2.53. 添加用户信息

下面的描述信息会出现在屏幕的下方，可以使用 **Tab** 键来切换不同的项目，以便输入相关信息：

Login ID

新用户的登录名（强制性必须写）

UID

这个用户的 ID 编号（如果不写，系统自动添加）

Group

这个用户的登录组名（如果不写，系统自动添加）

Password

这个用户的密码（键入这个需要很仔细！）

Full name

用户的全名（解释、备注）

Member groups

这个用户所在的组

Home directory

用户的主目录（如果不写，系统自动添加）

Login shell

用户登录的 shell（默认是`/bin/sh`）。

你可以将登录 shell 由 `/bin/sh` 改为 `/usr/local/bin/bash`， 以便使用事先以 package 形式安装的 bash shell。不要使用一个不存在的或您不能登录的 shell。 最通用的 shell 是使用 BSD-world 的 C shell， 可以通过指定`/bin/tcsh`来修改。

用户也可以被添加到 `wheel` 组中成了一个超级用户，从而拥有 `root` 权限。

当您感觉满意时，键入 [ OK ] 键， 用户和组管理菜单将会重新出现。

![退出用户和组管理](img/adduser3.png)图 2.54. 退出用户和组管理

如果有其他的需要， 此时还可以添加其他的组。 此外， 还可以通过 `sysinstall` 在安装完成之后添加它们。

当您完成添加用户的时候，选择 Exit 然后键入**Enter** 继续下面的安装。

### 2.10.13. 设置 `root` 密码

```
                        Message
 Now you must set the system manager's password.
 This is the password you'll use to log in as "root".

                         [ OK ]

               [ Press enter or space ]
```

键入 **Enter** 来设置 `root` 密码。

密码必须正确地输入两次。 毋庸讳言， 您需要选择一个不容易忘记的口令。 请注意您输入的口令不会回显， 也不会显示星号。

```
New password:
Retype new password :
```

密码成功键入后，安装将继续。

### 2.10.14. 退出安装

如果您需要设置 其他网络设备， 或需要完成其他的配置工作， 可以在此时或者事后通过 `sysinstall` 来进行配置。

```
                     User Confirmation Requested
 Visit the general configuration menu for a chance to set any last
 options?

                              Yes   [ No ]
```

选择 [ No ] 然后键入 **Enter** 返回到主安装菜单。

![退出安装](img/mainexit.png)图 2.55. 退出安装

选择 [X Exit Install] 然后键入 **Enter**。您可能需要确认是否真的退出安装：

```
                     User Confirmation Requested
 Are you sure you wish to exit? The system will reboot.

                            [ Yes ]   No
```

选择 [ Yes ]。 如果您是从 CDROM 引导的系统， 则会出现下面的提示信息要求您取出光盘：

```
                    Message
 Be sure to remove the media from the drive.

                    [ OK ]
           [ Press enter or space ]
```

在系统开始重启之前， CDROM 驱动器是锁住的。 CDROM 解锁后就可以取出光盘了 (动作要快)。 按 [ OK ] 重启系统。

此后系统将重新启动， 因此请留意是否会出现一些错误信息。 进一步的细节， 请参见 第 2.10.16 节 “FreeBSD 的启动过程”。

### 2.10.15. 配置其他网络服务

原作 Tom Rhodes.

如果之前缺少这一领域的经验， 那么配置网络服务对于新手而言， 很可能会是一件很有挑战的事情。 网络， 包括 Internet， 对于包括 FreeBSD 在内的所有现代操作系统而言都至关重要。 因此， 首先对 FreeBSD 提供的丰富的网络性能加以了解会很有帮助。 在安装过程中了解这些知识， 能够确保用户更好地理解他们可以用到的各种服务。

网络服务是一些可以接收来自网络上任何地方的人所提交的输入信息的程序。 人们一直都在努力确保这些程序不会做任何 “有害的” 事情。 不幸的是， 程序员们并不是十全十美的完人，因此，网络服务程序中的漏洞， 便有可能被攻击者利用来做一些坏事。因而， 只启用那些您知道自己需要的服务就很重要了。如果存在疑问， 那么就最好不要在您发现需要它之前启动任何网络服务。 您可以事后通过再次运行 sysinstall 或直接手工配置 `/etc/rc.conf` 来随时启用这些服务。

选择 Networking 选项将下显示一个类似下面的菜单：

![网络配置之上层配置](img/net-config-menu1.png)图 2.56. 网络配置之上层配置

第一个选项， Interfaces， 已经在前面的 第 2.10.1 节 “配置网卡” 中做过配置， 因此现在可以略过它。

选择 AMD 选项， 将添加对于 BSD 自动挂接程序的支持。 这个程序通常会和 NFS 协议 (详情参见下文) 配合使用，以便自动挂载远程文件系统。 启用它不需要在此时进行特殊的额外配置。

下一行是 AMD Flags 的参数选项。 选择它之后，会弹出一个让您选择 AMD 参数的子菜单。 菜单中包含一系列的选项：

```
-a /.amd_mnt -l syslog /host /etc/amd.map /net /etc/amd.map
```

`-a` 选项用来设置默认的挂接位置，这里使用的是 `/.amd_mnt`目录。`-l` 指定默认的 `日志` 文件； 但是，当使用 `syslogd` 时， 所有在日志中记录的活动， 都会发送到系统日志服务去。 `/host` 用来挂接远程主机上输出的文件系统，而 `/net` 目录则用来挂接从特定 IP 地址输出的文件系统。 `/etc/amd.map` 文件定义了用于 AMD 的默认输出选项。

Anon FTP 允许匿名 FTP 访问。 选中这个选项， 可以使这台机器成为一台匿名 FTP 服务器。 要注意启用这个选项的安全风险。 系统将使用另外的菜单来说明安全风险和进一步的配置。

Gateway 选项可以使将本机配置成为一台以前我们介绍过的网关。 如果您在安装过程中不小心选中了 Gateway， 也可以在这里用这个选项来取消。

Inetd 选项用来配置或完全禁用前面讨论过的 [inetd(8)](http://www.FreeBSD.org/cgi/man.cgi?query=inetd&sektion=8&manpath=freebsd-release-ports) 服务程序。

Mail 用来配置系统默认的 MTA 或邮件传输代理。 选择这个选项将出现下面的菜单：

![选择默认的 MTA](img/mta-main.png)图 2.57. 选择默认的 MTA

这里给您提供了一个安装 MTA 并将其配置为默认值的机会。MTA 是一种能够将邮件头递给本系统或互联网上的用户的邮件服务。

选择 Sendmail 将会安装十分流行的 sendmail 服务， 这也是 FreeBSD 的默认配置。Sendmail local 选项表示将 sendmail 设为默认的 MTA，但禁止其从 Internet 上接收邮件的能力。 此外还有一些其他选项，Postfix 和 Exim 与 Sendmail 的功能类似。 它们两者也可以投递邮件； 不过， 有些用户会喜欢使用它们代替 sendmail MTA。

选择 MTA 或决定不挑选 MTA 之后， 网络配置菜单的下一项将是 NFS client。

NFS client 客户端可以使系统通过 NFS 与服务器进行通信。 NFS 服务器通过 NFS 协议可以使其它在网络上的机器来访问自己的文件系统。 如果这台机器要作为一台独立的服务器，这个选项可以保留不选。 如果启用它， 您在之后还需要进行更多的其他配置； 请参见 第 30.3 节 “网络文件系统（NFS）” 以了解关于配置客户机和服务器的进一步详情。

接下来的 NFS server 选项， 可以让您将本机系统配置为 NFS 服务器。 这会自动将启动 RPC 远程过程调用的信息写入配置文件。 RPC 是一种在多个主机和程序之间进行连接组织的机制。

下一项是 Ntpdate 选项， 它能够处理时间同步。 当选择它后， 会出现一个像下面所似的菜单：

![Ntpdate 配置](img/ntp-config.png)图 2.58. Ntpdate 配置

从这个菜单选择一个离您最近的服务器。 选择较近的服务器，有助于提高时间同步的精度， 因为较远的服务器的连接延迟可能会比较大。

下一个选项是 PCNFSD。 这个选项将安装第三方软件包 [net/pcnfsd](http://www.freebsd.org/cgi/url.cgi?ports/net/pcnfsd/pkg-descr)。 它可以用来为无法自行提供 NFS 认证服务的操作系统， 如微软的 MS-DOS® 提供服务。

滚屏到下一页看一下其它选项：

![网络配置之下层配置](img/net-config-menu2.png)图 2.59. 网络配置之下层配置

[rpcbind(8)](http://www.FreeBSD.org/cgi/man.cgi?query=rpcbind&sektion=8&manpath=freebsd-release-ports)， [rpc.statd(8)](http://www.FreeBSD.org/cgi/man.cgi?query=rpc.statd&sektion=8&manpath=freebsd-release-ports) 和 [rpc.lockd(8)](http://www.FreeBSD.org/cgi/man.cgi?query=rpc.lockd&sektion=8&manpath=freebsd-release-ports) 这三个程序是用来提供远程过程调用 (RPC) 服务的。 `rpcbind` 程序管理 NFS 服务器和客户端的通信， 这是 NFS 正确工作的必要前提。rpc.statd 程序可以和其它主机上 rpc.statd 程序交互， 以提供状态监控。这些状态报告默认情况下会保存到 `/var/db/statd.status` 文件中。 最后的一项是 rpc.lockd 选项， 如果启用，则将提供文件上锁服务。通常将它和 rpc.statd 联用， 以监视哪些主机会请求对文件执行上锁操作， 以及这种操作的频繁程度。 尽管后两项功能对于调试非常有用， 但它们并不是 NFS 服务器和客户端正常运行所必需的。

下一个项目是 Routed，这是一个路由程序。 [routed(8)](http://www.FreeBSD.org/cgi/man.cgi?query=routed&sektion=8&manpath=freebsd-release-ports) 程序管理网络路由表，发现多播路由， 并且支持在网络上与它物理相连的主机来复制它的路由表的请求。 它被广泛地应用在本地网络中并扮演着网关的角色。 当选择它后，一个子菜单会来询问您这个程序的默认位置。 默认的位置已经被定义过， 您可以选择 **Enter** 键， 也可以按下其它的键。 这时会出来另一个菜单来询问您传递给 `routed`程序的参数。 默认的是 `-q` 参数。

接下来是 Rwhod 选项， 选中它会启用 [rwhod(8)](http://www.FreeBSD.org/cgi/man.cgi?query=rwhod&sektion=8&manpath=freebsd-release-ports) 程序在系统初时化的时候。 `rwhod`程序通过网络周期性的广播系统 信息或以“客户”的身份来收集这些信息。 更多的信息可以查看 [ruptime(1)](http://www.FreeBSD.org/cgi/man.cgi?query=ruptime&sektion=1&manpath=freebsd-release-ports) 和 [rwho(1)](http://www.FreeBSD.org/cgi/man.cgi?query=rwho&sektion=1&manpath=freebsd-release-ports) 手册页。

倒数第二个选项是[sshd(8)](http://www.FreeBSD.org/cgi/man.cgi?query=sshd&sektion=8&manpath=freebsd-release-ports) 程序。它可以通过使用 OpenSSH 来提供安全的 shell 服务， 我们推荐通过使用它来使用 telnet 和 FTP 服务。 sshd 服务通过使用加密技术来创建从一台机器到另一台机器的安全连接。

最后有一个 TCP 扩展选项。 这可以用来扩展在 RFC 1323 和 RFC 1644 里定义的 TCP 功能。当许多主机以高速连接本机时，可能会引起某些连接被丢弃。 我们不推荐使用这个选项， 但是当使用独立的主机时可以从它上面得到一些好处。

现在您已经配置完成了网络服务， 您可以滚动屏幕到顶部选择 X Exit 项， 退出进入下一个配置部分， 或简单地选择两次 X Exit 之后选择 [X Exit Install] 来退出 sysinstall。

### 2.10.16. FreeBSD 的启动过程

#### 2.10.16.1. FreeBSD/i386 的启动过程

如果启动正常，您将看到在屏幕上有很多信息滚动， 最后您会看到登录命令行。您可以通过键入 **Scroll-Lock**和使用 **PgUp** 与 **PgDn**来查看信息，再键入 **Scroll-Lock** 回到命令行。

记录信息可能不会显示（缓冲区的限制）。您可以通过键入 `dmesg` 来查看。

使用您在安装过程中设置的用户名/密码来登录。（例子中使用 `rpratt`）。除非必须的时候请不要用 `root` 用户登录。

典型的启动信息：（忽略版本信息）

```
Copyright (c) 1992-2002 The FreeBSD Project.
Copyright (c) 1979, 1980, 1983, 1986, 1988, 1989, 1991, 1992, 1993, 1994
        The Regents of the University of California. All rights reserved.

Timecounter "i8254"  frequency 1193182 Hz
CPU: AMD-K6(tm) 3D processor (300.68-MHz 586-class CPU)
  Origin = "AuthenticAMD"  Id = 0x580  Stepping = 0
  Features=0x8001bf<FPU,VME,DE,PSE,TSC,MSR,MCE,CX8,MMX>
  AMD Features=0x80000800<SYSCALL,3DNow!>
real memory  = 268435456 (262144K bytes)
config> di sn0
config> di lnc0
config> di le0
config> di ie0
config> di fe0
config> di cs0
config> di bt0
config> di aic0
config> di aha0
config> di adv0
config> q
avail memory = 256311296 (250304K bytes)
Preloaded elf kernel "kernel" at 0xc0491000.
Preloaded userconfig_script "/boot/kernel.conf" at 0xc049109c.
md0: Malloc disk
Using $PIR table, 4 entries at 0xc00fde60
npx0: <math processor> on motherboard
npx0: INT 16 interface
pcib0: <Host to PCI bridge> on motherboard
pci0: <PCI bus> on pcib0
pcib1: <VIA 82C598MVP (Apollo MVP3) PCI-PCI (AGP) bridge> at device 1.0 on pci0
pci1: <PCI bus> on pcib1
pci1: <Matrox MGA G200 AGP graphics accelerator> at 0.0 irq 11
isab0: <VIA 82C586 PCI-ISA bridge> at device 7.0 on pci0
isa0: <ISA bus> on isab0
atapci0: <VIA 82C586 ATA33 controller> port 0xe000-0xe00f at device 7.1 on pci0
ata0: at 0x1f0 irq 14 on atapci0
ata1: at 0x170 irq 15 on atapci0
uhci0: <VIA 83C572 USB controller> port 0xe400-0xe41f irq 10 at device 7.2 on pci0
usb0: <VIA 83C572 USB controller> on uhci0
usb0: USB revision 1.0
uhub0: VIA UHCI root hub, class 9/0, rev 1.00/1.00, addr 1
uhub0: 2 ports with 2 removable, self powered
chip1: <VIA 82C586B ACPI interface> at device 7.3 on pci0
ed0: <NE2000 PCI Ethernet (RealTek 8029)> port 0xe800-0xe81f irq 9 at
device 10.0 on pci0
ed0: address 52:54:05:de:73:1b, type NE2000 (16 bit)
isa0: too many dependant configs (8)
isa0: unexpected small tag 14
fdc0: <NEC 72065B or clone> at port 0x3f0-0x3f5,0x3f7 irq 6 drq 2 on isa0
fdc0: FIFO enabled, 8 bytes threshold
fd0: <1440-KB 3.5" drive> on fdc0 drive 0
atkbdc0: <keyboard controller (i8042)> at port 0x60-0x64 on isa0
atkbd0: <AT Keyboard> flags 0x1 irq 1 on atkbdc0
kbd0 at atkbd0
psm0: <PS/2 Mouse> irq 12 on atkbdc0
psm0: model Generic PS/2 mouse, device ID 0
vga0: <Generic ISA VGA> at port 0x3c0-0x3df iomem 0xa0000-0xbffff on isa0
sc0: <System console> at flags 0x1 on isa0
sc0: VGA <16 virtual consoles, flags=0x300>
sio0 at port 0x3f8-0x3ff irq 4 flags 0x10 on isa0
sio0: type 16550A
sio1 at port 0x2f8-0x2ff irq 3 on isa0
sio1: type 16550A
ppc0: <Parallel port> at port 0x378-0x37f irq 7 on isa0
ppc0: SMC-like chipset (ECP/EPP/PS2/NIBBLE) in COMPATIBLE mode
ppc0: FIFO with 16/16/15 bytes threshold
ppbus0: IEEE1284 device found /NIBBLE
Probing for PnP devices on ppbus0:
plip0: <PLIP network interface> on ppbus0
lpt0: <Printer> on ppbus0
lpt0: Interrupt-driven port
ppi0: <Parallel I/O> on ppbus0
ad0: 8063MB <IBM-DHEA-38451> [16383/16/63] at ata0-master using UDMA33
ad2: 8063MB <IBM-DHEA-38451> [16383/16/63] at ata1-master using UDMA33
acd0: CDROM <DELTA OTC-H101/ST3 F/W by OIPD> at ata0-slave using PIO4
Mounting root from ufs:/dev/ad0s1a
swapon: adding /dev/ad0s1b as swap device
Automatic boot in progress...
/dev/ad0s1a: FILESYSTEM CLEAN; SKIPPING CHECKS
/dev/ad0s1a: clean, 48752 free (552 frags, 6025 blocks, 0.9% fragmentation)
/dev/ad0s1f: FILESYSTEM CLEAN; SKIPPING CHECKS
/dev/ad0s1f: clean, 128997 free (21 frags, 16122 blocks, 0.0% fragmentation)
/dev/ad0s1g: FILESYSTEM CLEAN; SKIPPING CHECKS
/dev/ad0s1g: clean, 3036299 free (43175 frags, 374073 blocks, 1.3% fragmentation)
/dev/ad0s1e: filesystem CLEAN; SKIPPING CHECKS
/dev/ad0s1e: clean, 128193 free (17 frags, 16022 blocks, 0.0% fragmentation)
Doing initial network setup: hostname.
ed0: flags=8843<UP,BROADCAST,RUNNING,SIMPLEX,MULTICAST> mtu 1500
        inet 192.168.0.1 netmask 0xffffff00 broadcast 192.168.0.255
        inet6 fe80::5054::5ff::fede:731b%ed0 prefixlen 64 tentative scopeid 0x1
        ether 52:54:05:de:73:1b
lo0: flags=8049<UP,LOOPBACK,RUNNING,MULTICAST> mtu 16384
        inet6 fe80::1%lo0 prefixlen 64 scopeid 0x8
        inet6 ::1 prefixlen 128
        inet 127.0.0.1 netmask 0xff000000
Additional routing options: IP gateway=YES TCP keepalive=YES
routing daemons:.
additional daemons: syslogd.
Doing additional network setup:.
Starting final network daemons: creating ssh RSA host key
Generating public/private rsa1 key pair.
Your identification has been saved in /etc/ssh/ssh_host_key.
Your public key has been saved in /etc/ssh/ssh_host_key.pub.
The key fingerprint is:
cd:76:89:16:69:0e:d0:6e:f8:66:d0:07:26:3c:7e:2d root@k6-2.example.com
 creating ssh DSA host key
Generating public/private dsa key pair.
Your identification has been saved in /etc/ssh/ssh_host_dsa_key.
Your public key has been saved in /etc/ssh/ssh_host_dsa_key.pub.
The key fingerprint is:
f9:a1:a9:47:c4:ad:f9:8d:52:b8:b8:ff:8c:ad:2d:e6 root@k6-2.example.com.
setting ELF ldconfig path: /usr/lib /usr/lib/compat /usr/X11R6/lib
/usr/local/lib
a.out ldconfig path: /usr/lib/aout /usr/lib/compat/aout /usr/X11R6/lib/aout
starting standard daemons: inetd cron sshd usbd sendmail.
Initial rc.i386 initialization:.
rc.i386 configuring syscons: blank_time screensaver moused.
Additional ABI support: linux.
Local package initialization:.
Additional TCP options:.

FreeBSD/i386 (k6-2.example.com) (ttyv0)

login: rpratt
Password:
```

生成 RSA 和 DSA 密钥在比较慢的机器上可能要花很长时间。这只是一个 新安装后的首次启动，以后的启动会变得更快一点。

如果已经完成 X 服务器的配置， 且指定了默认的桌面窗口管理器， 就可以在命令行键入 `startx` 来启动它了。

### 2.10.17. FreeBSD 关机

正确的关闭操作系统是很重要的。不要仅仅关闭电源。 首先，您需要成为一个超级用户，通过键入 `su` 命令来实现。然后输入 `root` 密码。这需要用户是 `wheel` 组的一名成员。然后， 以`root`键入 `shutdown -h now`命令。

```
The operating system has halted.
Please press any key to reboot.
```

当 shutdown 命令发出后，屏幕上出现 “Please press any key to reboot” 信息时，您就可以安全的关闭计算机了。如果按下任意一个键， 计算机将重新启动。

您也能够使用 **Ctrl**+**Alt**+**Del** 组合键来重新启动计算机，但是不推荐使用这个操作。

## 2.11. 常见问题

下面将介绍一些在安装过程中常见的问题，像如何报告发生的问题， 如何双重启动 FreeBSD 和 MS-DOS® 或 Windows®。

### 2.11.1. 当您遇到错误时，应该怎么做？

由于 PC 结构的限制， 硬件检测不可能 100% 地可靠， 但是有些问题是您可以自己解决的。

首先检查一下您使用的 FreeBSD 版本的 [硬件兼容说明](http://www.FreeBSD.org/releases/index.html) 文档看看您使用的是否是被支持的硬件。

如果您使用的硬件是系统支持的，但仍然遇到了死机或其他问题， 则需要联编 定制的内核。 这能够支持默认的 `GENERIC` 内核所不支持的设备。 在引导盘上的内核假定绝大多数的硬件，均为按出厂设置的方式配置了 IRQ、 IO 地址和 DMA 通道。 如果您的硬件重新进行了配置， 则可能需要编辑内核配置， 并重新编译内核， 以便告诉 FreeBSD 到哪里去查找设备。

除此之外，也可能遇到这种情况吗，即探测某种并不存在的设备时， 会干扰到其他设备的检测并使其失败。 这种情况吗下应禁止驱动程序检测可能导致冲突的设备。

### 注意:

有些安装问题可以借助更新硬件的程序来解决，特别是主板的 BIOS 。 大部分的主板制造商都会提供网站给用户下载新的 BIOS 以及提供如何更新的说明。

也有许多制造商强烈建议，除非必要否则不要轻易更新 BIOS 。因为更新的过程 *可能* 会发生问题，进而损害 BIOS 芯片。

### 2.11.2. 使用 MS-DOS® 和 Windows® 文件系统

目前， FreeBSD 尚不支持通过 Double Space™ 程序压缩的文件系统。 因此，如果希望 FreeBSD 访问数据， 则应首先解压缩这些文件系统。 这项工作，可以通过位于 Start> Programs > System Tools 菜单的 Compression Agent 来完成。

FreeBSD 可以支持基于 MS-DOS® 的文件系统 （有时被称为 FAT 文件系统）。 [mount_msdosfs(8)](http://www.FreeBSD.org/cgi/man.cgi?query=mount_msdosfs&sektion=8&manpath=freebsd-release-ports) 命令能够把这样的文件系统挂接到现有的目录结构中， 并允许访问 FAT 文件系统上的内容。 通常我们并不直接使用 [mount_msdosfs(8)](http://www.FreeBSD.org/cgi/man.cgi?query=mount_msdosfs&sektion=8&manpath=freebsd-release-ports) 程序，它一般会在 `/etc/fstab` 中的某一行被调用或者被 [mount(8)](http://www.FreeBSD.org/cgi/man.cgi?query=mount&sektion=8&manpath=freebsd-release-ports) 工具并配合适当的参数来调用。

`/etc/fstab`中一个典型的例子：

```
/dev/ad0sN  /dos  msdosfs rw  0	0
```

### 注意:

`/dos` 目录必须事先存在。 更多关于 `/etc/fstab` 的细节， 请参阅 [fstab(5)](http://www.FreeBSD.org/cgi/man.cgi?query=fstab&sektion=5&manpath=freebsd-release-ports)。

一个使用 [mount(8)](http://www.FreeBSD.org/cgi/man.cgi?query=mount&sektion=8&manpath=freebsd-release-ports) 挂载 MS-DOS® 文件系统的例子：

```
# `mount -t msdosfs /dev/ad0s1 /mnt`
```

在此例子中， MS-DOS® 文件系统位于主硬盘的第一个分区。 您的情况可能与引不同，查看命令 `dmesg` 和 `mount` 的输出。 它们应该可以让您得到足够的分区信息。

### 注意:

FreeBSD 可能使用和其他操作系统不同计数方法来标记磁盘 slices， 特别需要指出的是， MS-DOS® 的扩展分区通常会比 MS-DOS® 主分区被标记为更高的数值。 可以使用 [fdisk(8)](http://www.FreeBSD.org/cgi/man.cgi?query=fdisk&sektion=8&manpath=freebsd-release-ports) 工具来帮助测定哪些 slices 属于 FreeBSD 哪些是属于其他的操作系统。

NTFS 分区也可以通过类似 [mount_ntfs(8)](http://www.FreeBSD.org/cgi/man.cgi?query=mount_ntfs&sektion=8&manpath=freebsd-release-ports) 命令挂接在 FreeBSD 上。

### 2.11.3. 排除故障时的常见问题和解决方法

| **2.11.3.1.** | 我的系统在引导到探测硬件时发生了死机、 安装过程中行为异常， 或没有检测到软驱。 |
|  | FreeBSD 在启动过程中广泛使用了 i386、 amd64 及 ia64 平台提供的 ACPI 服务来检测系统配置。 不幸的是， 在 ACPI 驱动和主板 BIOS 中存在一些 bug。 如果遇到这种情况， 可以在系统引导时禁用 ACPI， 其方法是在第三阶段引导加载器时使用 hint `hint.acpi.0.disabled`： 
```
`set hint.acpi.0.disabled="1"`
```

这一设置会在系统重启之后失效，因此，如果需要的话，您应在 `/boot/loader.conf` 文件中增加 `hint.acpi.0.disabled="1"`。 关于引导加载器的进一步详情， 请参见 第 13.1 节 “概述”。 |
| **2.11.3.2.** | 在硬盘安装 FreeBSD 之后的首次启动时， 内核加载并检测了硬件， 但给出下列消息并停止运行： 
```
changing root device to ad1s1a panic: cannot mount root
```

这是怎么回事？ 我该怎么做？另外引导帮助信息里提到的 `bios_drive:interface(unit,partition)kernel_name` 是什么？ |
|  | 系统在处理引导盘非系统中的第一块盘时有一个由来已久的问题。 BIOS 采用的编号方式有时和 FreeBSD 不一致， 而设法将其变为一样则很难正确地实现。因而， 在发生这种情况时，FreeBSD 可能会需要一些帮助才能找到磁盘。有两种常见的情况， 在这些情况下您都需要手工告诉 FreeBSD 根文件系统模块的位置。 这是通过告诉引导加载器 BIOS 磁盘编号、磁盘类型以及 FreeBSD 中的该种磁盘的编号来实现的。第一种情况是有两块 IDE 硬盘， 分别配置为对应 IDE 总线上的主 (master) 设备， 并希望 FreeBSD 从第二块硬盘上启动。 BIOS 将两块硬盘识别为磁盘 0 和磁盘 1， 而 FreeBSD 则将其分别叫做 `ad0` 和 `ad2`。FreeBSD 位于 BIOS 磁盘 1， 其类型是 `ad` 而 FreeBSD 磁盘编号则是 2， 因此， 您应输入： 
```
`1:ad(2,a)kernel`
```

注意， 如果您的主总线上有从设备， 则这一配置是不必要的 (因为这样配置是错的)。第二种情况是从 SCSI 磁盘启动，但系统中安装了一个或多个 IDE 硬盘。这时，FreeBSD 磁盘编号会比 BIOS 磁盘编号小。如果您有两块 IDE 硬盘， 以及一块 SCSI 硬盘，则 SCSI 硬盘将会是 BIOS 磁盘 2， 类型为 `da` 而 FreeBSD 磁盘编号是 0， 因此， 您应输入：

```
`2:da(0,a)kernel`
```

来告诉 FreeBSD 您希望从 BIOS 磁盘 2 引导， 而它是系统中的第一块 SCSI 硬盘。 假如只有一块 IDE 硬盘， 则应以 `1:` 代替。一旦您确定了应选用的正确配置， 就可以用标准的文本编辑器把它写到 `/boot.config` 文件中了。 除非另行指定， FreeBSD 将使用这个文件的内容， 作为对 `boot:` 提示的默认回应。 |
| **2.11.3.3.** | 在硬盘安装 FreeBSD 之后的首次启动时， Boot Manager 只是给出了 `F?` 的菜单提示， 但并不继续引导过程。 |
|  | 在您安装 FreeBSD 进行到分区编辑器时所设置的磁盘尺寸信息不对。 请回到分区编辑器并指定正确的磁盘尺寸。 这种情况必须重新安装 FreeBSD。如果您无法确定在您机器上的正确尺寸信息，可以用一个小技巧： 在磁盘开始的地方安装一个小的 DOS 分区， 并在其后安装 FreeBSD。 安装程序能够看到这个 DOS 分区， 并利用它推测磁盘的尺寸信息， 这通常会有所帮助。下面的技巧不再推荐使用， 在这里仅供参考： 
> 如果您正准备建立只运行 FreeBSD 的服务器或工作站， 而无需考虑 (之后) 与 DOS、 Linux 或其他操作系统的兼容性， 也可以使用整个硬盘 (分区编辑器中的 A)， 选择 FreeBSD 独占整个硬盘每一个扇区的非标准选项。 这会扫除关于磁盘尺寸的一切烦恼， 但会限制您以后运行 FreeBSD 以外的其他操作系统的能力。

 |
| **2.11.3.4.** | 系统找到了 [ed(4)](http://www.FreeBSD.org/cgi/man.cgi?query=ed&sektion=4&manpath=freebsd-release-ports) 网卡， 但总是报设备超时 (device timeout) 错误。 |
|  | 您的网卡可能使用了与 `/boot/device.hints` 文件中指定的 IRQ 不同的中断请求号。 [ed(4)](http://www.FreeBSD.org/cgi/man.cgi?query=ed&sektion=4&manpath=freebsd-release-ports) 驱动默认情况下并不支持 “软” 配置 (在 DOS 中使用 EZSETUP 配置的值)， 但如果您在网卡的 hints 中指定 `-1`， 便会使用软配置。您应使用网卡的跳线进行硬配置 (根据需要修改内核设置) 或通过 hint `hint.ed.0.irq="-1"` 将 IRQ 指定为 `-1`。 这会告诉内核使用软配置。另一个可能是您的网卡使用 IRQ 9， 这会与 IRQ 2 共用同一中断请求线， 同时也是导致问题的一个常见原因 (特别是 VGA 卡使用 IRQ 2 的时候！)。 您应尽量避免使用 IRQ 2 或 9。 |
| **2.11.3.5.** | 当在 X11 终端中运行 sysinstall 的时候， 黄色的字体相对于浅灰色的背景变得难以阅读。 有没有什么能让这个应用程序提供高对比度的方法？ |
|  | 如果你已经安装了 X11 并且 sysinstall 在 [xterm(1)](http://www.FreeBSD.org/cgi/man.cgi?query=xterm&sektion=1&manpath=freebsd-release-ports) 或者 [rxvt(1)](http://www.FreeBSD.org/cgi/man.cgi?query=rxvt&sektion=1&manpath=freebsd-release-ports) 中默认的颜色使得文字难以辩认， 可以在你的 `~.Xdefaults` 中加入 `XTerm*color7: #c0c0c0` 获得深灰色的背景。 |

## 2.12. 高级安装指南

原作 Valentino Vaschetto.更新 Marc Fonvieille.

这节主要描述在一些特殊情况下如何安装 FreeBSD。

### 2.12.1. 在一个没有显示器或键盘的系统上安装 FreeBSD

这种类型的安装叫做 “headless install（无头安装）”， 因您正要安装 FreeBSD 的机器不是没带显示器，就是没有显卡。 您可能会问那怎么安装？ 可以使用一个串行控制台。 串行控制台基本上是使用另外一台机器来充当主显示设备和键盘。 要这样做，只要执行下面的步骤： 创建安装 USB 记忆棒，请看 第 2.3.7 节 “准备引导介质”一节说明； 此外， 也可下载 ISO 映像文件， 具体请参阅 第 2.13.1 节 “创建一张安装光盘”。

要将安装介质改为使用串口控制台， 需要按下面这些步骤来操作 (如果使用 CDROM 则可跳过第一步)：

1.  **令安装 USB 记忆棒引导并进入串口控制台**

    如果使用刚刚制作的 USB 记忆棒引导系统， 则 FreeBSD 会进入正常的安装模式。 我们希望引导到串口控制台来完成安装。 为了做到这一点， 需要在 FreeBSD 中使用 [mount(8)](http://www.FreeBSD.org/cgi/man.cgi?query=mount&sektion=8&manpath=freebsd-release-ports) 挂载 USB 盘。

    ```
    # `mount /dev/da0a /mnt`
    ```

    ### 注意:

    您需要根据实际情况修改挂点的名称。

    现在挂好了记忆棒， 您需要对其进行配置令其进入串口控制台。 为此， 需要在 USB 记忆棒中的 `loader.conf` 文件中加入下面的这行配置：

    ```
    # `echo 'console="comconsole"' >> /mnt/boot/loader.conf`
    ```

    这样就完成了对 USB 记忆棒的配置， 您应使用 [umount(8)](http://www.FreeBSD.org/cgi/man.cgi?query=umount&sektion=8&manpath=freebsd-release-ports) 命令将其卸下：

    ```
    # `umount /mnt`
    ```

    现在就可以拔下 USB 记忆棒并进入这一过程的第三步了。

2.  **令安装 CD 引导并进入串口控制台**

    如果您直接使用 ISO 映像 (see 第 2.13.1 节 “创建一张安装光盘”) 制作的 CD 引导， 则 FreeBSD 会引导进入正常的安装模式。 我们希望引导到串口控制台来完成安装。 为了做到这一点， 您需要展开、 修改并重新生成 ISO 文件， 然后再刻录光盘。

    在保存例如 `FreeBSD-8.1-RELEASE-i386-disc1.iso` ISO 的 FreeBSD 系统上用 [tar(1)](http://www.FreeBSD.org/cgi/man.cgi?query=tar&sektion=1&manpath=freebsd-release-ports) 工具提取全部文件：

    ```
    # `mkdir /path/to/headless-iso`
    # `tar -C /path/to/headless-iso -pxvf FreeBSD-8.1-RELEASE-i386-disc1.iso`
    ```

    接下来需要对其进行配置令其进入串口控制台。 为此， 需要在从 ISO 映像中提取的 `loader.conf` 文件中加入下面的这行配置：

    ```
    # `echo 'console="comconsole"' >> /path/to/headless-iso/boot/loader.conf`
    ```

    最后， 从修改好的目录树中创建新的 ISO 映像。 这里我们使用通过 [sysutils/cdrtools](http://www.freebsd.org/cgi/url.cgi?ports/sysutils/cdrtools/pkg-descr) port 安装的 [mkisofs(8)](http://www.FreeBSD.org/cgi/man.cgi?query=mkisofs&sektion=8&manpath=freebsd-release-ports) 工具来完成：

    ```
    # `mkisofs -v -b boot/cdboot -no-emul-boot -r -J -V "Headless_install" \
    	    -o Headless-FreeBSD-8.1-RELEASE-i386-disc1.iso /path/to/headless-iso`
    ```

    这样就完成了对 ISO 映像的配置， 您可以使用您熟悉的工具将其刻录到 CD-R 上了。

3.  **连接 Null-modem 线**

    现在需要一根 null-modem 线 来连接两台机器。 只要连接两台机器的串口。 *这里不能使用普通的串口线*， 而必须使用 null-modem 线， 因为它需要一些内部交叉的连线。

4.  **开始启动安装**

    现在可以开始安装了。 将 USB 记忆棒插到您准备进行 headless 安装的机器上， 然后开机。 如果您使用的是 CDROM， 则在开机之后立即将光盘放进光驱。

5.  **连接您的无头机器**

    现在您已经通过[cu(1)](http://www.FreeBSD.org/cgi/man.cgi?query=cu&sektion=1&manpath=freebsd-release-ports)连接到了那台机器。

    ```
    # `cu -l /dev/cuau0`
    ```

    在 FreeBSD 7.X 上应使用下面的命令：

    ```
    # `cu -l /dev/cuad0`
    ```

这样就可以了！ 您现在可以通过 `cu` 会话来控制那台 headless 的机器了。 接着系统会提示选择终端类型。 选择 FreeBSD 彩色控制台并继续安装！

## 2.13. 准备您自己的安装介质

### 注意:

为了避免重复 “FreeBSD disc” 在这里指 FreeBSD CDROM or DVD 那即意味着您要购买或自己制做。

有好几个原因需要您创建自己的 FreeBSD 安装介质。 这可能是物理介质，如磁带，使用 sysinstall 程序找到的安装文件， FTP 站点或 MS-DOS®分区。

例如：

*   您有许多机器连接到本地网络，使用一个 FreeBSD 光盘。 您要使用 FreeBSD 来创建一个本地 FTP 站点， 然后使用这个 FTP 站点来代替连接到 Internet。

*   您有一张 FreeBSD 光盘， FreeBSD 不支持您的 CD/DVD 驱动器， 但 MS-DOS®/Windows® 支持。 您要复制安装文件到一个 DOS 分区， 然后使用这些文件进行安装。

*   您要安装的计算机没有 CD/DVD 驱动器和网卡，但您可以连接一个 “Laplink-style” 串口或并口线缆到那台计算机。

*   您要通过一个磁带机来安装 FreeBSD.

### 2.13.1. 创建一张安装光盘

FreeBSD 的每个发行版本都为每一支持的平台提供至少两张 CDROM 映像 (“ISO images”)。如果您有刻录机， 这些映像文件可以被(“burned”) 成 FreeBSD 的安装光盘。 如果没有刻录机，而上网带宽却很便宜，它也是一种很好的安装方式。

1.  **下载正确的 ISO 映像文件**

    每个版本的 ISO 映像文件都可以从 `ftp://ftp.FreeBSD.org/pub/FreeBSD/ISO-IMAGES-架构名/版本` 或最近的镜像站点下载。选择合适的 *`架构`* 和 *`版本`* 。

    目录中包含下面一些映像文件：

    表 2.4. FreeBSD 7.*`X`* 和 8.*`X`* ISO 映像文件名和含义

    | 文件名 | 包含内容 |
    | --- | --- |
    | `FreeBSD-版本-RELEASE-架构-bootonly.iso` | 这个 CD 映像可以让您从光驱启动并进入安装过程， 但它并不提供用于支持从 CD 直接安装 FreeBSD 所需的文件。 在从 CD 引导之后， 您需要通过网络 (例如从 FTP 服务器) 来完成安装。 |
    | `FreeBSD-版本-RELEASE-架构-dvd1.iso.gz` | 这个 DVD 映像包括用于安装 FreeBSD 操作系统基本组件、 预编译包和文档所需的全部文件。 它也支持引导进入基于 “livefs” 的修复模式。 |
    | `FreeBSD-版本-RELEASE-架构-memstick.img` | 这个映像可以写进 USB 记忆棒， 用于引导系统并完成安装。 它也支持引导进入基于 “livefs” 的修复模式。 这个版本的映像中包含了文档所需要的全部文件， 但不提供其他包。 FreeBSD 7.3 和更早版本中没有这个文件。 |
    | `FreeBSD-版本-RELEASE-架构-disc1.iso` | 这个 CD 映像包含了 FreeBSD 操作系统的基本组件和文档包， 但不包括其它包。 |
    | `FreeBSD-版本-RELEASE-架构-disc2.iso` | 这个 CD 映像包含了能填满光盘的尽可能多的第三方软件包。 在 FreeBSD 8.0 和更高版本中不提供这个映像。 |
    | `FreeBSD-版本-RELEASE-架构-disc3.iso` | 另一个包含了能填满光盘的尽可能多的第三方软件包的 CD 映像。 在 FreeBSD 8.0 和更高版本中不提供这个映像。 |
    | `版本-RELEASE-架构-docs.iso` | FreeBSD 文档。 |
    | `FreeBSD-版本-RELEASE-架构-livefs.iso` | 这个 CD 映像包含了用以支持引导进入基于 “livefs” 的修复模式， 但不包括直接从 CD 安装所需的文件。 |

    ### 注意:

    FreeBSD 7.X 系列在 FreeBSD 7.3 之前的版本， 以及 FreeBSD 8.X 系列在 FreeBSD 8.1 之前的版本使用不同的命名习惯。 它们的 ISO 文件名不使用 `FreeBSD-` 前缀。

    您 *必须* 下载 `bootonly` ISO 映像 (如果有) 或 `disc1` 的映像其中的一个。 没有必要都下载， 因为 `disc1` 映像包含了 `bootonly` ISO 映像中的全部内容。

    如果您的 Internet 带宽很廉价， 则应使用 `bootonly` ISO。 它能安装 FreeBSD， 而您可以根据需要使用 ports/packages 系统来下载并安装第三方软件 (参见 第 5 章 *安装应用程序: Packages 和 Ports*)。

    如果打算安装 FreeBSD 并安装常用的软件包， 则应使用 `dvd1`。

    其它的映像盘也很有用， 但不是必须的， 尤其是在您有高速的网络连接时。

2.  **刻录 CDs**

    您必须把这些映像文件刻录成光盘。 如果您在其它的 FreeBSD 系统上完成此项工作，请看 第 19.6 节 “创建和使用光学介质(CD)”") 得到更多的信息，（特别是 第 19.6.3 节 “burncd” 和 第 19.6.4 节 “cdrecord”）

    如果您在其它的系统平台上执行，您需要相应的刻录软件。 映像文件使用的是标准的 ISO 格式，必须被您的刻录软件所支持。

### 注意:

如果有兴趣制作一张定制的 FreeBSD 版本， 请参考 Release Engineering Article。

### 2.13.2. 为 FreeBSD 安装盘建立局域网 FTP 站点

FreeBSD 光盘的布局和 FTP 站点相同。 这样， 建立局域网 FTP 站点来用于网络上的其它计算机安装 FreeBSD， 就十分的容易。

1.  在要作为 FTP 站点的那台 FreeBSD 机器上， 确定 FreeBSD 磁盘放入光驱中并将它挂在 `/cdrom` 目录中。

    ```
    # `mount /cdrom`
    ```

2.  在 `/etc/passwd` 文件中建立一个可匿名访问 FTP 服务器的账号。 您可以利用 [vipw(8)](http://www.FreeBSD.org/cgi/man.cgi?query=vipw&sektion=8&manpath=freebsd-release-ports) 命令编辑 `/etc/passwd` 文件， 加入下面这一行叙述：

    ```
    ftp:*:99:99::0:0:FTP:/cdrom:/nonexistent
    ```

3.  确定在 `/etc/inetd.conf` 配置文件中开启了 FTP 服务。

任何本地网络中的机器在安装 FreeBSD 选择安装介质时就可以选择透过 FTP 站点，然后选取 “Other” 后输入 **`ftp:// 本地 FTP 服务器`** 即可以透过本地的 FTP 站点来安装 FreeBSD。

### 注意:

如果用作 FTP 客户端的引导介质 (通常是软盘) 与本地局域网的 FTP 站点上的版本不一致， sysinstall 会不允许您完成安装。 如果您使用的版本差距不很大， 并且希望绕过这一判断， 则应进入 Options 菜单， 并将安装包的名字改为 any。

### 警告:

此方式最好使用在有防火墙保护的内部网络。 如果要将此 FTP 服务公开给外面的网际网络（非本地用户）， 您的电脑必须承担被侵入或其它的风险。 我们强烈建议您要有完善的安全机制才这样做。

### 2.13.3. 创建安装软盘

如果您从软盘安装（我们*不*推荐那样做）， 或者是由于不支持硬件或者更简单的理由是因为您坚持要使用软盘安装。 您必须准备几张软盘。

至少这些软盘必须是 1.44 MB 的，用来容纳所有在 `base` (基本系统) 目录下的文件。如果您在 DOS 操作系统下准备就 *必须* 使用 MS-DOS® 的 `FORMAT` 命令来格式化软盘。 如果您使用的是 Windows® 操作系统， 在资源管理器中就可以完成这个工作 (用右键单击 `A:` 驱动器，并选择 “Format”)。

*不要* 指望厂家的预先格式化！ 最好还是亲自进行格式化。 过去用户报告的很多问题都是由于不正确地使用格式化设备所造成的， 所以我们需要在这里着重提一下。

如果您在另外一台 FreeBSD 的机器上做了启动盘的话， 进行格式化是一个不错的主意。 虽然您不需要把每张盘都做成 DOS 文件系统。您也可以使用 `bsdlabel` 和 `newfs` 命令来创建一个 UFS 文件系统，具体操作按下面的顺序进行：

```
# `fdformat -f 1440 fd0.1440`
# `bsdlabel -w fd0.1440 floppy3`
# `newfs -t 2 -u 18 -l 1 -i 65536 /dev/fd0`
```

然后您就可以像其它的文件系统一样挂上和写入这些磁盘。

格式化这些磁盘后，您必须把文件复制到磁盘中。 这些发行文件被分割成刚好可存进五张 1.44 MB 软盘。 检查您所有的磁盘， 找出所有可能适合的文件。 直到您找到所有需要的配置并且将它们以这种方式安置。 第一个配置都应该有一个子目录在磁盘上， 例如： `a:\base\base.aa`、 `a:\base\base.ab`， 等等。

### 重要:

`base.inf` 文件， 也应放在 `base` 的第一张盘上， 因为安装程序需要读取这个文件， 以了解在获得发布包时需要下载多少文件。

一旦您进入选择安装介质的屏幕， 选择 Floppy 将会看到后面的提示符。

### 2.13.4. 从 MS-DOS® 分区安装

如果从 MS-DOS® 分区安装， 您需要将发布文件复制到该分区根目录下的 `freebsd` 目录中。 例如： `c:\freebsd`。 您必须复制一部分 CDROM 或 FTP 上的目录结构， 因此， 如果您从光盘进行复制， 建议使用 DOS 的 `xcopy` 命令。 下面是准备进行 FreeBSD 最小系统安装的例子：

```
C:\> `md c:\freebsd`
C:\> `xcopy e:\bin c:\freebsd\bin\ /s`
C:\> `xcopy e:\manpages c:\freebsd\manpages\ /s`
```

假设 `C:` 盘是您的空闲空间， `E:` 盘是您挂接的 CDROM。

如果您没有光盘驱动器，您可以从以下网站下载发行包。ftp.FreeBSD.org. 每一个发行包都在一个目录中，例如 *base* 发行包可以在 10.3/base/ 目录中找到。

对很多发行包来说，如果您希望从 MS-DOS®分区安装的话 （您有足够的空间），安装 `c:\freebsd` ── 下的每个文件－这个 `BIN` 发行包只是最低限度的要求。

### 2.13.5. 创建一个安装磁带

从磁带安装也许是最简单的方式， 比在线使用 FTP 安装或使用 CDROM 还快。安装的程序假设是简单地被压缩在磁带上。 在您得到所有配置文件后，简单地解开它们，用下面的命令：

```
# `cd /freebsd/distdir`
# `tar cvf /dev/rwt0 dist1 ... dist2`
```

在您安装的时候，您要确定留有足够的空间给临时目录（允许您选择） 来容纳磁带安装时 *全部* 的内容。由于不是随机访问 磁带的，所以这种安装方法需要很多临时空间。

### 注意:

开始安装时，在从软盘启动 *之前*， 磁带机必须已经放在驱动设备中。否则， 安装过程中可能会找不到它。

### 2.13.6. 通过网络安装

可用的网络安装类型有三种。 以太网 (标准的以太网控制器)、 串口 (PPP) 以及 并口 (PLIP (laplink 线缆))。

如果希望以最迅速的方式完成网络安装， 那么以太网适配器当然就是首选！ FreeBSD 支持绝大多数常见 PC 以太网卡； 系统能够支持的网卡 (以及所需的配置) 可以在 FreeBSD 发行版附带的硬件兼容说明中找到。 如果您使用的是系统支持的 PCMCIA 以太网卡， 在为笔记本加电 *之前* 之前一定要把它插好！ 很不幸， FreeBSD 目前并不支持在安装过程中热插 PCMCIA 卡。

此外， 您还需要知道自己的 IP 地址、 网络类型对应的子网掩码， 以及机器名。 如果您正通过 PPP 连接安装而没有固定的静态 IP， 不用怕， 这个 IP 地址会由您的 ISP 自动分配。 您的系统管理员会告诉您进行网络配置所需的信息。 如果您需要通过名字而不是 IP 地址来访问其他主机， 则还需要配置一个域名服务器， 可能还需要一个网关地址 (在使用 PPP 时， 这个地址是服务提供商的 IP 地址)。 如果您希望通过 HTTP 代理服务器来完成 FTP 安装， 还需要知道代理服务器的地址。 如果您不知道这些信息， 则应在进行这种安装 *之前* 向系统管理员或 ISP 询问。

如果您使用一个 MODEM，那您就只有 PPP 这一种选择了。在您安装的过程中， 要确定您能很容易地获得完整且快速的关于您服务提供商的信息。

如果您使用 PAP 或 CHAP 方式连接到您的 ISP， （换句话说，如果您不使用脚本在 Windows®中连接到您的 ISP）， 那么您需要在 ppp 提示符下输入 `dial` 命令。否则，当 PPP 连接者只提供一种最简单的终端模拟器，您必须知道如何使用针对 MODEM 的 “AT commands”拨号到您的 ISP。 想知道更深入的信息可以参考 使用手册中的用户级 PPP 那节 以及 FAQ 。 如果您有一些问题，可以使用 `set log local ...` 命令将日志显示在屏幕上。

您也可以通过并口电缆连接到另外一台 FreeBSD 机器上进行安装，您可以考虑使用 “laplink” 并口电缆进行安装。通过并口安装要比通过串口 （最高 50 kbytes/sec）安装快得多。

#### 2.13.6.1. 通过 NFS 安装之前

NFS 安装方式是非常方便的。只需要简单地将 FreeBSD 文件复制到一台服务器上，然后在安装时选择 NFS 介质。

如果这个服务器要 “特权端口” 才能支持 （如 SUN 的工作站），您需要在安装前在 Options 菜单中设置 `NFS Secure`。

如果你使用了一块低质量的以太网卡比较糟糕， 速度很慢，则应考虑 `NFS Slow`的选项。

为了达到 NFS 安装的目的，这个服务器必须支持 subdir 加载。 例如，如果您的 FreeBSD 10.3 目录存在： `ziggy:/usr/archive/stuff/FreeBSD`，然后 `ziggy` 将必须允许直接挂上 `/usr/archive/stuff/FreeBSD`，而不仅仅是 `/usr` 或 `/usr/archive/stuff`。

在 FreeBSD 的 `/etc/exports` 配置文件中， 是由 `-alldirs` 选项来控制的。其它 NFS 服务器也许有不同的方式。如果您从服务器得到 permission denied 这个信息， 可能是因为您没有正确的启用它。

## 第 3 章 安装 FreeBSD（适用于 9.*`x`* 及以后版本）

重构、 重整及部分重写：Jim Mock.sysinstall 操作流程、 屏幕截图及一般性文字：Randy Pratt.对 bsdinstall 的更新：Gavin Atkinson 和 Warren Block.

## 3.1. 概述

FreeBSD 提供了一个以文字为主、 便于使用的安装程序： 从 FreeBSD 9.0-RELEASE 开始是指 bsdinstall， 而在之前则是指 sysinstall。 本章介绍 bsdinstall 的使用， 有关 sysinstall 的使用参见 第 2 章 *安装 FreeBSD*。

学习完本章之后， 您将知道：

*   如何创建 FreeBSD 安装介质。

*   FreeBSD 如何划分目标硬盘。

*   如何启动 bsdinstall。

*   运行 bsdinstall 时需要回答的问题， 问题的具体含义， 以及应该如何回答。

阅读本章之前， 您应该：

*   查看将要安装的 FreeBSD 版本所附的硬件支持列表， 以确定您的硬件能够被支持。

### 注意:

一般来说， 此安装说明是针对 i386™（“PC 兼容机”） 架构的计算机； 同时也会尽可能地对其他架构下的安装予以说明。 虽然本文档经常更新， 但仍可能与所安装版本上附带的说明文档有些许出入， 因此建议您仅将其作为常规的安装指导。

## 3.2. 硬件需求

### 3.2.1. 最低配置

安装 FreeBSD 所需的最低配置， 随版本及硬件架构而有所不同。

以下几节对这些信息进行了总结。 根据所选的安装方式， 可能需要使用 FreeBSD 支持的 CDROM 或网络适配器， 详见 第 3.3.5 节 “准备安装介质”。

#### 3.2.1.1. FreeBSD/i386

FreeBSD/i386 需要 486 或更快的处理器， 最小 64 MB 的内存， 以及至少 1.1 GB 的硬盘空间。

### 注意:

通常情况下对于老旧的计算机而言， 安装更大的内存和腾出更多的硬盘空间， 会比使用更快的处理器对性能的提升更加明显。

#### 3.2.1.2. FreeBSD/amd64

FreeBSD/amd64 支持两种处理器。 第一种是 AMD64 处理器， 包括 AMD Athlon™64、 AMD Athlon™64-FX、 AMD Opteron™ 以及更高级别的处理器。

能够使用 FreeBSD/amd64 的另一种处理器是采用了 Intel® EM64 架构的处理器。 这类处理器包括 Intel® Core™ 2 Duo、 Quad 和 Extreme 家族， 还包括 Intel® Xeon™ 3000、 5000 和 7000 系列， 以及 Intel® Core™ i3、 i5 和 i7。

对于使用了 nVidia nForce3 Pro-150 的机器， *必须* 在 BIOS 设置中禁用 IO APIC， 如果没有这样的选项就只能转而禁用 ACPI。 因为 Pro-150 芯片组存在 bug，而目前还没有能够规避此问题的方法。

#### 3.2.1.3. FreeBSD/powerpc Apple® Macintosh®

支持所有内建 USB 的 New World Apple® Macintosh® 系统， 同时也为配置多 CPU 的机器提供 SMP 支持。

注意 32 位的内核只能使用内存的前 2 GB，而 PowerMac G3 蓝白机上的 FireWire® 也不被支持。

#### 3.2.1.4. FreeBSD/sparc64

有关 FreeBSD/sparc64 的系统支持， 详见 [FreeBSD/sparc64](http://www.freebsd.org/platforms/sparc.html) 项目。

FreeBSD/sparc64 需要独占一块磁盘。 目前还不支持与其他操作系统共享同一块磁盘。

### 3.2.2. 支持的硬件

FreeBSD 发行版所支持的硬件架构及设备会列在硬件兼容说明文件中， 此文件通常名为 `HARDWARE.TXT`， 位于发行版介质的根目录下。 这些内容也可以在 FreeBSD 网站的 [发行版信息](http://www.FreeBSD.org/releases/index.html) 页面上找到。

## 3.3. 安装前的准备工作

### 3.3.1. 备份您的数据

在将 FreeBSD 安装至目标机器前， 应首先备份其上的重要数据并对备份进行测试。 FreeBSD 安装程序对硬盘做任何改动前都会进行询问， 而一旦操作开始就无法撤销。

### 3.3.2. 决定将 FreeBSD 安装在何处

如果整个硬盘上仅安装 FreeBSD 一个操作系统， 那么请直接跳过此节； 但如果需要让 FreeBSD 与其他操作系统并存， 那么首先应当了解 FreeBSD 的硬盘布局结构。

#### 3.3.2.1. FreeBSD/i386 与 FreeBSD/amd64 的硬盘布局

硬盘可以分割成多个区域， 这些区域称作 *partition（分区）*。

有两种硬盘分区方式。 传统的 *Master Boot Record* (MBR， 主引导记录) 的分区表中可以定义四个 *primary partitions (主分区)*。 (由于历史原因， FreeBSD 中将主分区称作 *slice*。) 为了突破四个分区的限制， 可以将其中一个主分区创建为 *extended partition (扩展分区)*， 并在其中建立 *logical partitions (逻辑分区)*。 正如您看到的那样， 这种方法十分笨拙。

新式的 *GUID Partition Table (GUID 分区表)* (GPT) 提供了更为简便的磁盘分区方法。 与传统的 MBR 分区相比， GPT 功能更为强大。 常见的 GPT 实现可以在一块磁盘上支持多达 128 个分区， 从而无需再采用类似逻辑分区这样迭床架屋的结构。

### 警告:

一些旧式的操作系统， 如 Windows® XP 并不兼容 GPT 分区格式。 如果需要让 FreeBSD 与这样的操作系统共用一块硬盘， 就必须使用 MBR 分区了。

FreeBSD 的标准引导加载器需要使用一个主分区或 GPT 分区。 (有关 FreeBSD 引导过程的详情， 请参阅 第 13 章 *FreeBSD 引导过程*。) 如果所有的主分区或 GPT 分区都已在使用中， 则必须为 FreeBSD 腾出一个来使用。

最小安装的 FreeBSD 只需 1 GB 磁盘空间。 不过， 这是 *非常* 基本的安装， 而且也不会留下多少可用的空间。 比较实用的情况下， 如果不使用图形界面， 最小安装应分配至少 3 GB 的空间， 而使用图形界面， 则应分配至少 5 GB 的空间。 此外， 第三方应用程序可能还需要更多的空间。

有很多 [免费或商业的分区调整工具](http://en.wikipedia.org/wiki/List_of_disk_partitioning_software) 可供使用。 例如， 以 Live CD 形式提供的 [GParted Live](http://gparted.sourceforge.net/livecd.php) 中的 GParted 分区编辑器。 此外， GParted 也可以在许多其它 Linux Live CD 发行版中找到。

### 警告:

磁盘分区程序有可能会破坏现有的数据。 在修改磁盘分区之前， 应先做一次完整的备份并校验其完整性。

调整 Microsoft® Vista 分区大小时可能会遇到一些问题。 如果要这样做， 请提前准备好 Vista 安装光盘。

例 3.1. 使用现有的分区

假设一台安装了 Windows® 的计算机上有一块 40 GB 的硬盘， 分成了两个 20 GB 的分区。 Windows® 将它们分别叫做 `C:` 和 `D:`。 `C:` 分区包含了 10 GB 数据， 而 `D:` 分区包含了 5 GB 数据。

将数据从 `D:` 移动到 `C:`， 就可将第二个分区腾出来供 FreeBSD 使用了。

例 3.2. 缩小现有的分区

假设一台安装了 Windows® 的计算机上有一块 40 GB 的硬盘， 一个大的分区使用了整块磁盘的全部空间。 Windows® 将这个 40 GB 分区叫做 `C:`。 目前占用了 15 GB 空间。 现希望将 Windows® 分区减少到 20 GB， 并将余下的 20 GB 分给 FreeBSD 使用。

可以在以下两种方法中任选一种：

1.  备份 Windows® 数据。 接着， 重新安装 Windows®， 在安装过程中建立一个 20 GB 的分区。

2.  使用类似 GParted 这样的分区调整工具来缩小 Windows® 分区， 并腾出空间给 FreeBSD 使用。

包含不同操作系统的磁盘分区令您能够在任何时候使用其中的一种。 如果希望同时运行多种不同的操作系统， 可以使用在 第 23 章 *虚拟化* 中介绍的方法。

### 3.3.3. 收集网络配置信息

某些 FreeBSD 安装方式需要通过网络连接下载相关文件。 若要连接至以太网 (或电视电缆/DSL 调制解调器上的以太网接口)， 则需要向安装程序提供必要的网络配置信息。

*DHCP* 可以用来提供自动配置网络的信息。 假如没有可用的 DHCP， 则必须从局域网管理员， 或网络服务提供商那里获得必要的配置信息：

网络配置信息

1.  IP 地址

2.  子网掩码

3.  默认网关的 IP 地址

4.  本地网络域名

5.  DNS 服务器的 IP 地址

### 3.3.4. 检查 FreeBSD 发行勘误

尽管 FreeBSD 项目会确保每个发行版尽可能地稳定， 但 bug 总是在所难免。 极少数情况下， 这些 bug 甚至会影响安装。 一旦这些问题被发现并修正后， 就会列在 FreeBSD 网站的 FreeBSD 发行勘误 中。 在安装之前， 应首先检查这些勘误， 以确保安装可以顺利进行。

有关所有发行版的信息及勘误， 可以在 FreeBSD 网站 的 发行版信息 一节中找到。

### 3.3.5. 准备安装介质

FreeBSD 的安装介质包括 CD、 DVD 及 USB 记忆棒。 若要开始安装， 只需使用安装介质引导计算机即可； 注意不能通过在其他操作系统中执行安装程序这种方式进行安装。

标准的安装介质中包含了 FreeBSD 安装所需的全部文件， 除此之外， 还有一种 *bootonly* 安装介质。 这种介质并不在其中直接包含安装所需的全部文件， 而是在需要时通过网络进行下载。 因此， 与标准的安装介质相比， bootonly 安装介质体积更小。

FreeBSD 安装介质的副本可以从 FreeBSD 网站 获取。

### 提示:

如果您已经有 FreeBSD 的安装 CD、 DVD 或 USB 记忆棒， 则可以跳过此节。

FreeBSD 的安装 CD 或 DVD 映像均为可引导的 ISO 文件。 只需要 CD 或 DVD 其中的一种即可完成安装操作。 任选一种在当前操作系统中刻录成可引导光盘即可。

若要创建可引导的记忆棒， 请执行以下操作：

1.  **获取记忆棒映像**

    FreeBSD 9.0-RELEASE 和更高版本的记忆棒映像文件可以在 `ftp://ftp.FreeBSD.org/pub/FreeBSD/releases/arch/arch/ISO-IMAGES/version/FreeBSD-version-RELEASE-arch-memstick.img` 中的 `ISO-IMAGES/` 目录中找到， 其中， *`arch`* 是指要安装的架构， 而 *`version`* 则是指要安装的版本号。 举例来说， FreeBSD/i386 9.0-RELEASE 的记忆棒映像位于 `ftp://ftp.FreeBSD.org/pub/FreeBSD/releases/i386/i386/ISO-IMAGES/9.0/FreeBSD-9.0-RELEASE-i386-memstick.img` 找到。

    ### 提示:

    在 FreeBSD 8.*`X`* 以及更早的版本中， 映像文件的下载位置略有不同。 关于 FreeBSD 8.*`X`* 和更早版本的安装操作请参阅 第 2 章 *安装 FreeBSD*。

    记忆棒映像的扩展名为 `.img`。 在 `ISO-IMAGES/` 目录中提供了多个不同的映像， 可以根据需要的 FreeBSD 版本， 有时也包括安装对象的硬件状况进行选择。

    ### 重要:

    执行以下步骤前， 应 *备份* USB 记忆棒上的数据， 因为之后的操作将 *擦除* 这些数据。

2.  **将映像文件写入记忆棒**

    过程 3.1. 在 FreeBSD 中操作

    ### 警告:

    在下面的例子中， 目标记忆棒对应的设备节点是 `/dev/da0`。 操作前请仔细确认目标设备是否正确， 以免损坏现有的数据。

    *   **使用 [dd(1)](http://www.FreeBSD.org/cgi/man.cgi?query=dd&sektion=1&manpath=freebsd-release-ports) 写入映像**

        扩展名为 `.img` 的映像文件 *不是* 一种普通的文件。 它是对记忆棒上完整内容所做的 *映像*， 因此 *不能* 只是像普通文件一样简单的复制， 而应使用 [dd(1)](http://www.FreeBSD.org/cgi/man.cgi?query=dd&sektion=1&manpath=freebsd-release-ports) 将其直接写入目标设备：

        ```
        # `dd if=FreeBSD-9.0-RELEASE-i386-memstick.img of=/dev/da0 bs=64k`
        ```

    过程 3.2. 在 Windows® 中操作

    ### 警告:

    操作前请确认是否为目标设备选择了正确的驱动器号， 否则可能会覆盖并损坏您的现有数据。

    1.  **获取 Image Writer for Windows®**

        Image Writer for Windows® 是一种能将映像正确写入到记忆棒中的免费应用程序。 从 `[`launchpad.net/win32-image-writer/`](https://launchpad.net/win32-image-writer/)` 下载并将其提取至任意文件夹后即可开始使用。

    2.  **使用 Image Writer 写入映像**

        双击图标 Win32DiskImager 运行程序后， 确定 `Device` 下面显示的驱动器号所对应的是记忆棒。 点击文件夹图标以选择需要写入的映像文件， 然后点击 [ Save ] 接受选择。 在确认所有操作无误且没有其他窗口访问记忆棒后， 点击 [ Write ] 将映像文件写入记忆棒。

### 注意:

系统不再支持从软盘进行安装了。

您现在可以开始安装 FreeBSD 了。

## 3.4. 开始安装

### 重要:

默认情况下， 在您看到下面这条信息之前， 安装程序不会对硬盘数据做任何修改：

```
Your changes will now be written to disk.  If you
have chosen to overwrite existing data, it will
be PERMANENTLY ERASED. Are you sure you want to
commit your changes?
```

在此之前均可安全退出， 抑或您担心进行了某些错误的配置， 也可以直接关闭电源。

### 3.4.1. 开机启动

#### 3.4.1.1. 引导 i386™ 及 amd64 系统

1.  若要使用 第 3.3.5 节 “准备安装介质” 所述的 USB 记忆棒引导， 则应在开机前将其插入计算机。

    若要使用 CDROM 引导， 则应在开机后立刻将其放入计算机。

2.  根据所使用的安装介质， 选择从 CDROM 或 USB 启动。 在 BIOS 设置中， 可以选择特定的引导设备。 大多数系统还可以在启动时选择引导设备， 通常需要按 **F10**、 **F11**、 **F12** 或 **Escape** 键。

3.  如果您的计算机正常启动并加载了现有的操作系统， 那么请检查：

    1.  USB 记忆棒插入过晚或 CDROM 放入过晚， 请将其拔下或取出， 然后重新启动计算机并再次尝试。

    2.  BIOS 设置错误， 请重新设置。

    3.  BIOS 不支持从当前介质启动； 可以使用 [Plop Boot Manager](http://www.plop.at/en/bootmanager.html)， 它能够让老式计算机支持 CD 或 USB 启动。

4.  FreeBSD 将开始启动。 如果使用的是 CDROM， 则会看到类似这样的显示（版本信息可以忽略）：

    ```
    Booting from CD-ROM...
    645MB medium detected
    CD Loader 1.2

    Building the boot loader arguments
    Looking up /BOOT/LOADER... Found
    Relocating the loader and the BTX
    Starting the BTX loader

    BTX loader 1.00 BTX version is 1.02
    Consoles: internal video/keyboard
    BIOS CD is cd0
    BIOS drive C: is disk0
    BIOS drive D: is disk1
    BIOS 636kB/261056kB available memory

    FreeBSD/i386 bootstrap loader, Revision 1.1

    Loading /boot/defaults/loader.conf
    /boot/kernel/kernel text=0x64daa0 data=0xa4e80+0xa9e40 syms=[0x4+0x6cac0+0x4+0x88e9d]
    \
    ```

5.  FreeBSD 引导加载器会显示：

    ![FreeBSD 引导加载器菜单](img/bsdinstall-boot-loader-menu.png)图 3.1. FreeBSD 引导加载器菜单

    您可以等待十秒或按 **Enter** 键。

#### 3.4.1.2. 引导 Macintosh® PowerPC®

在大多数机器上， 开机时按住 **C** 键可以从 CD 启动。 除此之外， 按住 **Command**+**Option**+**O**+**F**， 在非 Apple® 键盘上是 **Windows**+**Alt**+**O**+**F**， 然后在出现的提示符 `0 >` 下输入

```
`boot cd:,\ppc\loader cd:0`
```

对于不带键盘的 Xserves 机器，请参考 [Apple® 支持网站](http://support.apple.com/kb/TA26930) 以了解如何引导至 Open Firmware。

#### 3.4.1.3. 引导 SPARC64®

多数 SPARC64® 系统均设置成了硬盘自启动。 若要安装 FreeBSD， 则应从网络或 CDROM 启动， 这就需要首先进入 PROM（OpenFirmware）。

重启系统后等待引导信息出现， 虽然其具体内容取决于机器型号， 但应该会类似：

```
Sun Blade 100 (UltraSPARC-IIe), Keyboard Present
Copyright 1998-2001 Sun Microsystems, Inc.  All rights reserved.
OpenBoot 4.2, 128 MB memory installed, Serial #51090132.
Ethernet address 0:3:ba:b:92:d4, Host ID: 830b92d4.
```

如果此时系统已经开始从硬盘启动， 那么请按下 **L1**+**A** 或 **Stop**+**A** 或在串口控制台发送 `BREAK`（在 [tip(1)](http://www.FreeBSD.org/cgi/man.cgi?query=tip&sektion=1&manpath=freebsd-release-ports) 或 [cu(1)](http://www.FreeBSD.org/cgi/man.cgi?query=cu&sektion=1&manpath=freebsd-release-ports) 中是 `~#`）以进入 PROM 提示符， 它应该如下所示：

```
ok ![1](img/1.png)
`ok {0}` ![2](img/2.png)
```

| ![1](img/#bsdinstall-prompt-single) | 这是在单 CPU 系统上的提示符。 |
| ![2](img/#bsdinstall-prompt-smp) | 这是在 SMP 系统上的提示符， 其中的数字表示可用的 CPU 个数。 |

现在， 放入 CDROM 并在 PROM 提示符后输入 `boot cdrom`。

### 3.4.2. 查看设备探测结果

为了便于查阅， 屏幕上所显示的最后几百行字符会始终保存在缓冲区里。

若要浏览缓冲区， 可以按下 **Scroll Lock** 键来开启屏幕的滚动功能； 开启后即可使用方向键、 **PageUp** 键或 **PageDown** 键进行翻阅； 再次按下 **Scroll Lock** 键将关闭滚动功能。

浏览时将看到内核进行了设备探测， 其结果类似 图 3.2 “典型的设备探测结果” 中的文本， 但具体内容会因计算机中所包含的设备而有所不同。

```
Copyright (c) 1992-2011 The FreeBSD Project.
Copyright (c) 1979, 1980, 1983, 1986, 1988, 1989, 1991, 1992, 1993, 1994
        The Regents of the University of California. All rights reserved.
FreeBSD is a registered trademark of The FreeBSD Foundation.
FreeBSD 9.0-RELEASE #0 r225473M: Sun Sep 11 16:07:30 BST 2011
    root@psi:/usr/obj/usr/src/sys/GENERIC amd64
CPU: Intel(R) Core(TM)2 Duo CPU     T9400  @ 2.53GHz (2527.05-MHz K8-class CPU)
  Origin = "GenuineIntel"  Id = 0x10676  Family = 6  Model = 17  Stepping = 6
  Features=0xbfebfbff<FPU,VME,DE,PSE,TSC,MSR,PAE,MCE,CX8,APIC,SEP,MTRR,PGE,MCA,CMOV,PAT,PSE36,CLFLUSH,DTS,ACPI,MMX,FXSR,SSE,SSE2,SS,HTT,TM,PBE>
  Features2=0x8e3fd<SSE3,DTES64,MON,DS_CPL,VMX,SMX,EST,TM2,SSSE3,CX16,xTPR,PDCM,SSE4.1>
  AMD Features=0x20100800<SYSCALL,NX,LM>
  AMD Features2=0x1<LAHF>
  TSC: P-state invariant, performance statistics
real memory  = 3221225472 (3072 MB)
avail memory = 2926649344 (2791 MB)
Event timer "LAPIC" quality 400
ACPI APIC Table: <TOSHIB A0064   >
FreeBSD/SMP: Multiprocessor System Detected: 2 CPUs
FreeBSD/SMP: 1 package(s) x 2 core(s)
 cpu0 (BSP): APIC ID:  0
 cpu1 (AP): APIC ID:  1
ioapic0: Changing APIC ID to 1
ioapic0 <Version 2.0> irqs 0-23 on motherboard
kbd1 at kbdmux0
acpi0: <TOSHIB A0064> on motherboard
acpi0: Power Button (fixed)
acpi0: reservation of 0, a0000 (3) failed
acpi0: reservation of 100000, b6690000 (3) failed
Timecounter "ACPI-safe" frequency 3579545 Hz quality 850
acpi_timer0: <24-bit timer at 3.579545MHz> port 0xd808-0xd80b on acpi0
cpu0: <ACPI CPU> on acpi0
ACPI Warning: Incorrect checksum in table [ASF!] - 0xFE, should be 0x9A (20110527/tbutils-282)
cpu1: <ACPI CPU> on acpi0
pcib0: <ACPI Host-PCI bridge> port 0xcf8-0xcff on acpi0
pci0: <ACPI PCI bus> on pcib0
vgapci0: <VGA-compatible display> port 0xcff8-0xcfff mem 0xff400000-0xff7fffff,0xe0000000-0xefffffff irq 16 at device 2.0 on pci0
agp0: <Intel GM45 SVGA controller> on vgapci0
agp0: aperture size is 256M, detected 131068k stolen memory
vgapci1: <VGA-compatible display> mem 0xffc00000-0xffcfffff at device 2.1 on pci0
pci0: <simple comms> at device 3.0 (no driver attached)
em0: <Intel(R) PRO/1000 Network Connection 7.2.3> port 0xcf80-0xcf9f mem 0xff9c0000-0xff9dffff,0xff9fe000-0xff9fefff irq 20 at device 25.0 on pci0
em0: Using an MSI interrupt
em0: Ethernet address: 00:1c:7e:6a:ca:b0
uhci0: <Intel 82801I (ICH9) USB controller> port 0xcf60-0xcf7f irq 16 at device 26.0 on pci0
usbus0: <Intel 82801I (ICH9) USB controller> on uhci0
uhci1: <Intel 82801I (ICH9) USB controller> port 0xcf40-0xcf5f irq 21 at device 26.1 on pci0
usbus1: <Intel 82801I (ICH9) USB controller> on uhci1
uhci2: <Intel 82801I (ICH9) USB controller> port 0xcf20-0xcf3f irq 19 at device 26.2 on pci0
usbus2: <Intel 82801I (ICH9) USB controller> on uhci2
ehci0: <Intel 82801I (ICH9) USB 2.0 controller> mem 0xff9ff800-0xff9ffbff irq 19 at device 26.7 on pci0
usbus3: EHCI version 1.0
usbus3: <Intel 82801I (ICH9) USB 2.0 controller> on ehci0
hdac0: <Intel 82801I High Definition Audio Controller> mem 0xff9f8000-0xff9fbfff irq 22 at device 27.0 on pci0
pcib1: <ACPI PCI-PCI bridge> irq 17 at device 28.0 on pci0
pci1: <ACPI PCI bus> on pcib1
iwn0: <Intel(R) WiFi Link 5100> mem 0xff8fe000-0xff8fffff irq 16 at device 0.0 on pci1
pcib2: <ACPI PCI-PCI bridge> irq 16 at device 28.1 on pci0
pci2: <ACPI PCI bus> on pcib2
pcib3: <ACPI PCI-PCI bridge> irq 18 at device 28.2 on pci0
pci4: <ACPI PCI bus> on pcib3
pcib4: <ACPI PCI-PCI bridge> at device 30.0 on pci0
pci5: <ACPI PCI bus> on pcib4
cbb0: <RF5C476 PCI-CardBus Bridge> at device 11.0 on pci5
cardbus0: <CardBus bus> on cbb0
pccard0: <16-bit PCCard bus> on cbb0
isab0: <PCI-ISA bridge> at device 31.0 on pci0
isa0: <ISA bus> on isab0
ahci0: <Intel ICH9M AHCI SATA controller> port 0x8f58-0x8f5f,0x8f54-0x8f57,0x8f48-0x8f4f,0x8f44-0x8f47,0x8f20-0x8f3f mem 0xff9fd800-0xff9fdfff irq 19 at device 31.2 on pci0
ahci0: AHCI v1.20 with 4 3Gbps ports, Port Multiplier not supported
ahcich0: <AHCI channel> at channel 0 on ahci0
ahcich1: <AHCI channel> at channel 1 on ahci0
ahcich2: <AHCI channel> at channel 4 on ahci0
acpi_lid0: <Control Method Lid Switch> on acpi0
battery0: <ACPI Control Method Battery> on acpi0
acpi_button0: <Power Button> on acpi0
acpi_acad0: <AC Adapter> on acpi0
acpi_toshiba0: <Toshiba HCI Extras> on acpi0
acpi_tz0: <Thermal Zone> on acpi0
attimer0: <AT timer> port 0x40-0x43 irq 0 on acpi0
Timecounter "i8254" frequency 1193182 Hz quality 0
Event timer "i8254" frequency 1193182 Hz quality 100
atkbdc0: <Keyboard controller (i8042)> port 0x60,0x64 irq 1 on acpi0
atkbd0: <AT Keyboard> irq 1 on atkbdc0
kbd0 at atkbd0
atkbd0: [GIANT-LOCKED]
psm0: <PS/2 Mouse> irq 12 on atkbdc0
psm0: [GIANT-LOCKED]
psm0: model GlidePoint, device ID 0
atrtc0: <AT realtime clock> port 0x70-0x71 irq 8 on acpi0
Event timer "RTC" frequency 32768 Hz quality 0
hpet0: <High Precision Event Timer> iomem 0xfed00000-0xfed003ff on acpi0
Timecounter "HPET" frequency 14318180 Hz quality 950
Event timer "HPET" frequency 14318180 Hz quality 450
Event timer "HPET1" frequency 14318180 Hz quality 440
Event timer "HPET2" frequency 14318180 Hz quality 440
Event timer "HPET3" frequency 14318180 Hz quality 440
uart0: <16550 or compatible> port 0x3f8-0x3ff irq 4 flags 0x10 on acpi0
sc0: <System console> at flags 0x100 on isa0
sc0: VGA <16 virtual consoles, flags=0x300>
vga0: <Generic ISA VGA> at port 0x3c0-0x3df iomem 0xa0000-0xbffff on isa0
ppc0: cannot reserve I/O port range
est0: <Enhanced SpeedStep Frequency Control> on cpu0
p4tcc0: <CPU Frequency Thermal Control> on cpu0
est1: <Enhanced SpeedStep Frequency Control> on cpu1
p4tcc1: <CPU Frequency Thermal Control> on cpu1
Timecounters tick every 1.000 msec
hdac0: HDA Codec #0: Realtek ALC268
hdac0: HDA Codec #1: Lucent/Agere Systems (Unknown)
pcm0: <HDA Realtek ALC268 PCM #0 Analog> at cad 0 nid 1 on hdac0
pcm1: <HDA Realtek ALC268 PCM #1 Analog> at cad 0 nid 1 on hdac0
usbus0: 12Mbps Full Speed USB v1.0
usbus1: 12Mbps Full Speed USB v1.0
usbus2: 12Mbps Full Speed USB v1.0
usbus3: 480Mbps High Speed USB v2.0
ugen0.1: <Intel> at usbus0
uhub0: <Intel UHCI root HUB, class 9/0, rev 1.00/1.00, addr 1> on usbus0
ugen1.1: <Intel> at usbus1
uhub1: <Intel UHCI root HUB, class 9/0, rev 1.00/1.00, addr 1> on usbus1
ugen2.1: <Intel> at usbus2
uhub2: <Intel UHCI root HUB, class 9/0, rev 1.00/1.00, addr 1> on usbus2
ugen3.1: <Intel> at usbus3
uhub3: <Intel EHCI root HUB, class 9/0, rev 2.00/1.00, addr 1> on usbus3
uhub0: 2 ports with 2 removable, self powered
uhub1: 2 ports with 2 removable, self powered
uhub2: 2 ports with 2 removable, self powered
uhub3: 6 ports with 6 removable, self powered
ugen2.2: <vendor 0x0b97> at usbus2
uhub8: <vendor 0x0b97 product 0x7761, class 9/0, rev 1.10/1.10, addr 2> on usbus2
ugen1.2: <Microsoft> at usbus1
ada0 at ahcich0 bus 0 scbus1 target 0 lun 0
ada0: <Hitachi HTS543225L9SA00 FBEOC43C> ATA-8 SATA 1.x device
ada0: 150.000MB/s transfers (SATA 1.x, UDMA6, PIO 8192bytes)
ada0: Command Queueing enabled
ada0: 238475MB (488397168 512 byte sectors: 16H 63S/T 16383C)
ada0: Previously was known as ad4
ums0: <Microsoft Microsoft 3-Button Mouse with IntelliEyeTM, class 0/0, rev 1.10/3.00, addr 2> on usbus1
SMP: AP CPU #1 Launched!
cd0 at ahcich1 bus 0 scbus2 target 0 lun 0
cd0: <TEAC DV-W28S-RT 7.0C> Removable CD-ROM SCSI-0 device
cd0: 150.000MB/s transfers (SATA 1.x, ums0: 3 buttons and [XYZ] coordinates ID=0
UDMA2, ATAPI 12bytes, PIO 8192bytes)
cd0: cd present [1 x 2048 byte records]
ugen0.2: <Microsoft> at usbus0
ukbd0: <Microsoft Natural Ergonomic Keyboard 4000, class 0/0, rev 2.00/1.73, addr 2> on usbus0
kbd2 at ukbd0
uhid0: <Microsoft Natural Ergonomic Keyboard 4000, class 0/0, rev 2.00/1.73, addr 2> on usbus0
Trying to mount root from cd9660:/dev/iso9660/FREEBSD_INSTALL [ro]...
```

图 3.2. 典型的设备探测结果

请仔细检查设备探测结果， 以确定 FreeBSD 找到了所有您希望使用的设备。 没有找到的设备并不会在这里列出， 因为默认的 `GENERIC` 内核中不包含它们； 可以通过 内核模块 对这些设备提供支持。

设备探测完成后， 您将看到 图 3.3 “选择安装介质的使用方式”， 表明安装介质共有三种用途： 安装 FreeBSD 、 作为“Live CD”或引导至 FreeBSD 的命令行界面。 请使用方向键选择一项后按 **Enter** 键确认。

![选择安装介质的使用方式](img/bsdinstall-choose-mode.png)图 3.3. 选择安装介质的使用方式

在这里， 请选择 [ Install ] 以运行安装程序。

## 3.5. 介绍 bsdinstall

bsdinstall 是一个基于文本的 FreeBSD 安装程序， 作者是 Nathan Whitehorn， 于 2011 年被 FreeBSD 9.0 采用。

### 注意:

Kris Moore 为 [PC-BSD](http://pcbsd.org) 编写的 pc-sysinstall 也可以用于 [安装 FreeBSD](http://wiki.pcbsd.org/index.php/Use_PC-BSD_Installer_to_Install_FreeBSD)。 虽然有时会同 bsdinstall 混淆， 但实际两者并不相关。

bsdinstall 菜单系统的主要控制键包括方向键、 **Enter** 键、 **Tab** 键、 **Space** 键等。

### 3.5.1. 选择键盘映射

根据当前正在使用的系统控制台， bsdinstall 可能会首先提示选择一种非默认的键盘布局。

![键盘映射选择](img/bsdinstall-keymap-select-default.png)图 3.4. 键盘映射选择

选择了 [ YES ] 后， 将显示下面的键盘选择画面； 否则将不显示此画面而直接使用默认键盘映射。

![键盘选择菜单](img/bsdinstall-config-keymap.png)图 3.5. 键盘选择菜单

使用上/下方向键选择最适合当前系统的键盘映射后， 按 **Enter** 键确认。

### 注意:

按 **Esc** 键以使用默认的键盘映射。 如果不清楚该选择哪一项， 推荐 United States of America ISO-8859-1。

### 3.5.2. 设置主机名

下面， bsdinstall 将提示为新安装的系统设置主机名。

![设置主机名](img/bsdinstall-config-hostname.png)图 3.6. 设置主机名

应该输入完整的主机名， 例如 `machine3.example.com`。

### 3.5.3. 选择要安装的组件

下面， bsdinstall 将提示选择要安装的组件。

![选择要安装的组件](img/bsdinstall-config-components.png)图 3.7. 选择要安装的组件

安装哪些组件很大程度取决于系统用途及可用磁盘空间。 注意， 任何情况下都会安装 FreeBSD 内核及用户空间（统称“基系统”）。

根据安装类型的不同， 某些组件可能不会显示。

可选组件

*   `doc` - 附加文档， 主要是与项目历史相关的内容。 稍后还可以安装 FreeBSD 文档计划所提供的文档。

*   `games` - 一些传统的 BSD 游戏， 包括 fortune 与 rot13 等。

*   `lib32` - 兼容库文件， 用于在 64 位版本的 FreeBSD 上运行 32 位程序。

*   `ports` - FreeBSD 的 ports 集。

    ports 集提供了一种简单而方便的途径来安装软件。 在 ports 集中， 并不包含编译软件所需的源代码， 取而代之的是一组能够自动下载、 编译并安装第三方软件包的文件。 第 5 章 *安装应用程序: Packages 和 Ports* 会讲述如何使用 ports 集。

    ### 警告:

    选择此项时， 必须保证有足够的硬盘空间， 注意安装程序并不会对此进行检查。 FreeBSD 9.0 的 ports 集约需 500 MB 的磁盘空间； 您也可以为稍后的版本预留更大的空间。

*   `src` - 系统源代码。

    FreeBSD 提供了与内核及用户空间有关的完整源代码。 大部分程序并不需要这些源代码， 它们主要用于联编特定软件（例如设备驱动或内核模块）或者 FreeBSD 本身的开发。

    完整的源代码树需要 1 GB 的磁盘空间， 而重新编译整个 FreeBSD 系统则额外还需要 5 GB 的空间。

## 3.6. 通过网络安装

*bootonly* 安装介质中并不会包含所有的安装文件。 如果使用这种介质进行安装， 那么需要的文件就必须通过网络下载。

![通过网络安装](img/bsdinstall-netinstall-files.png)图 3.8. 通过网络安装

根据 第 3.9.2 节 “配置网络接口” 配置了网络连接后， 即可开始选择像站点。 镜像站点上缓存有 FreeBSD 的安装文件， 选择一个更近的镜像站点有助于更快的获取这些文件， 从而减少安装时间。

![选择一个镜像站点](img/bsdinstall-netinstall-mirrorselect.png)图 3.9. 选择一个镜像站点

连接至所选镜像站点并查询到所需文件后， 安装将继续进行。

## 3.7. 分配磁盘空间

FreeBSD 提供了三种方式来分配磁盘空间： *Guided（向导式）* 分区能够自动设置磁盘分区； 而 *Manual（手动式）* 分区则允许高级用户创建自定义分区； 还可以进入 shell 中直接使用类似 [gpart(8)](http://www.FreeBSD.org/cgi/man.cgi?query=gpart&sektion=8&manpath=freebsd-release-ports)、 [fdisk(8)](http://www.FreeBSD.org/cgi/man.cgi?query=fdisk&sektion=8&manpath=freebsd-release-ports) 与 [bsdlabel(8)](http://www.FreeBSD.org/cgi/man.cgi?query=bsdlabel&sektion=8&manpath=freebsd-release-ports) 这样的命令行程序。

![选择分配磁盘空间的方式](img/bsdinstall-part-guided-manual.png)图 3.10. 选择分配磁盘空间的方式

### 3.7.1. 向导式分区

如果机器上配有多块磁盘， 则需要为 FreeBSD 的安装指定目标磁盘。

![从多块磁盘中进行选择](img/bsdinstall-part-guided-disk.png)图 3.11. 从多块磁盘中进行选择

可以将整个磁盘都分配给 FreeBSD， 也可以只分配其中的一部分。 若选择的是 [ Entire Disk ]， 则创建分区布局时会直接使用整个磁盘； 若选择的是 [ Partition ]， 则创建分区时仅会使用磁盘上的空闲空间。

![选择如何创建分区布局](img/bsdinstall-part-entire-part.png)图 3.12. 选择如何创建分区布局

请仔细检查分区布局的创建结果。 如果发现有错误之处， 可以选择 [ Revert ] 来还原之前的分区； 此外， 也可以选择 [ Auto ] 重新让 FreeBSD 自动创建分区。 也可以手动创建、 修改或删除分区。 正确创建了分区之后， 请选择 [ Finish ] 以继续安装。

![检查已创建分区](img/bsdinstall-part-review.png)图 3.13. 检查已创建分区

### 3.7.2. 手动式分区

手动式分区将直接使用分区编辑器进行操作。

![手动创建分区](img/bsdinstall-part-manual-create.png)图 3.14. 手动创建分区

高亮目标驱动器（本例中为 `ada0`）并选择 [ Create ] 以显示 *partitioning scheme（分区方案）* 菜单。

![手动创建分区](img/bsdinstall-part-manual-partscheme.png)图 3.15. 手动创建分区

对于 PC 兼容机来说， GPT 分区通常是最合适的选择， 而某些不兼容 GPT 的老式操作系统则可能需要使用 MBR 分区。 除此之外的分区方案仅用于一些不常见的或其他的老式操作系统。

表 3.1. 分区方案

| 缩写 | 说明 |
| --- | --- |
| APM | [Apple Partition Map， 用于 PowerPC® Macintosh®。](http://support.apple.com/kb/TA21692) |
| BSD | 不带 MBR 的 BSD Label， 有时也称作危险的专用模式， “dangerously dedicated mode”。 请参阅 [bsdlabel(8)](http://www.FreeBSD.org/cgi/man.cgi?query=bsdlabel&sektion=8&manpath=freebsd-release-ports)。 |
| GPT | [GUID 分区表。](http://en.wikipedia.org/wiki/GUID_Partition_Table) |
| MBR | [Master Boot Record， 主引导记录。](http://en.wikipedia.org/wiki/Master_boot_record) |
| PC98 | [MBR 变体， 用于 NEC PC-98 计算机。](http://en.wikipedia.org/wiki/Pc9801) |
| VTOC8 | Volume Table Of Contents， 用于 Sun SPARC64 和 UltraSPARC 计算机。 |

确定了分区方案并创建完成后， 可再次选择 [ Create ] 以创建新的分区。

![手动创建分区](img/bsdinstall-part-manual-addpart.png)图 3.16. 手动创建分区

FreeBSD 的标准 GPT 安装至少会使用三个分区：

标准 FreeBSD GPT 分区

*   `freebsd-boot` - FreeBSD 引导分区， 它必须处于首位。

*   `freebsd-ufs` - FreeBSD 的 UFS 文件系统。

*   `freebsd-swap` - FreeBSD 的交换空间。

也可以同时创建多个文件系统分区。 有些用户会喜欢传统的分区格局， 为 `/`、 `/var`、 `/tmp`， 以及 `/usr` 文件系统分别创建分区。 请参阅 例 3.3 “创建传统的分割式文件系统分区” 中的例子。

可用的 GPT 分区类型可以在 [gpart(8)](http://www.FreeBSD.org/cgi/man.cgi?query=gpart&sektion=8&manpath=freebsd-release-ports) 中找到。

在指定尺寸时， 可以使用常用的缩写： *K* 表示 kilobytes、 *M* 表示 megabytes， 而 *G* 表示 gigabytes。

### 提示:

正确地对齐磁盘扇区能够获取最佳性能。 无论磁盘的每个扇区为 512 字节还是 4K 字节， 将分区大小设置为 4K 字节的倍数都能够确保对齐。 实际操作中， 只要使分区的大小等于 1M 或 1G 的倍数即可。 唯一的例外是 *freebsd-boot* 分区， 目前由于引导代码所限， 此分区不能大于 512K。

若分区包含文件系统，则需要在 Mountpoint 项中为其输入挂载点； 若仅创建了一个 UFS 分区， 则应在此项中输入 `/`。

最后需要输入的是 *Label（标签）* 项， 用于命名所创建的分区。 如果将驱动器连接至不同的控制器或端口， 其名称或编号会发生改变， 但对应的标签并不会变化。 在类似 `/etc/fstab` 这样的文件中， 通过标签引用分区比通过驱动器名加分区编号引用更加灵活， 因为这样引用使系统对硬件的改变更加宽容。 GPT 的标签会在磁盘连接后出现在 `/dev/gpt/` 中； 而其他分区方案中的标签也有不同的功能， 它们会出现在 `/dev/` 中的不同目录里。

### 提示:

为避免冲突， 请给每个文件系统指定独一无二的标签。 与计算机的名称、 用途或位置相关的字符均可添加至标签。 例如， 实验室计算机的 UFS 根目录可以命名为 “labroot” 或 “rootfs-lab”。

例 3.3. 创建传统的分割式文件系统分区

在传统的分区布局中， 目录 `/`、 `/var`、 `/tmp` 及 `/user` 都是位于自己分区上的独立文件系统； 在 GPT 分区方案中也可以创建这样的分区布局。 本例中所使用的是一块 20G 的硬盘， 如果使用更大的硬盘， 建议创建更大的交换或 `/var` 分区。 标签的前缀 `ex` 是指 “example”， 具体操作时您可以使用任何独一无二的字符。

| 分区类型 | 大小 | 挂载点 | 标签 |
| --- | --- | --- | --- |
| `freebsd-boot` | `512K` |   |   |
| `freebsd-ufs` | `2G` | `/` | `exrootfs` |
| `freebsd-swap` | `4G` |   | `exswap` |
| `freebsd-ufs` | `2G` | `/var` | `exvarfs` |
| `freebsd-ufs` | `1G` | `/tmp` | `extmpfs` |
| `freebsd-ufs` | 接受默认值（剩余空间） | `/usr` | `exusrfs` |

创建了自定义分区后， 请选择 [ Finish ] 以继续安装。

## 3.8. 安装确认

下面， 安装程序将真正对硬盘进行写操作， 这也是取消安装的最后机会。

![最后确认](img/bsdinstall-final-confirmation.png)图 3.17. 最后确认

选择 [ Commit ] 并按 **Enter** 键确认安装； 选择 [ Back ] 以返回分区编辑器进行修改； 选择 [ Revert & Exit ] 以退出安装而不修改任何硬盘数据。

根据所选组件、 安装介质和机器速度的不同， 需要的时间会有所变化。 安装时会有一系列信息显示目前的进度。

首先， 安装程序会将分区布局写入磁盘， 并执行 `newfs` 初始化分区。

如果是通过网络安装， bsdinstall 将根据之前所选的组件下载对应的文件。

![获取组件对应的文件](img/bsdinstall-distfile-fetching.png)图 3.18. 获取组件对应的文件

接下来， 会验证这些文件的完整性， 以防止其在下载时损坏或从安装介质中误读。

![验证组件对应的文件](img/bsdinstall-distfile-verifying.png)图 3.19. 验证组件对应的文件

最后， 验证过的组件文件会被提取至磁盘。

![提取组件对应的文件](img/bsdinstall-distfile-extracting.png)图 3.20. 提取组件对应的文件

文件提取全部完成后， bsdinstall 将开始安装后的配置任务（参见 第 3.9 节 “安装后的配置”）。

## 3.9. 安装后的配置

成功安装 FreeBSD 后， 还需要依次进行一些配置。 在重启进入新系统前， 这些配置始终可以通过最终的配置菜单进行修改。

### 3.9.1. 设置 `root` 密码

必须设置 `root` 密码。 请注意输入密码时， 被输入的字符并不会在屏幕上显示， 因此为防止输入错误， 必须再次输入相同的字符。

![设置 root 密码](img/bsdinstall-post-root-passwd.png)图 3.21. 设置 `root` 密码

成功设置密码后， 安装将继续进行。

### 3.9.2. 配置网络接口

### 注意:

如果已经在 *bootonly* 安装时配置过网络接口， 则可略过此步。

这里将显示一个网络接口列表， 其中的接口都是在当前计算机上侦测到的， 请选择一个进行配置。

![选择一个网络接口](img/bsdinstall-configure-network-interface.png)图 3.22. 选择一个网络接口

#### 3.9.2.1. 配置无线网络接口

如果选择了无线网络接口， 则必须输入相关的无线网络验证及安全参数， 以允许其连接至特定的网络。

无线网络是通过 Service Set Identifier（服务集标识符， 简写为 SSID）来表示的， 它是唯一表示无线网络的短字符串。

大多数无线网络都会以加密方式传输数据， 藉此保护信息不被未经授权者查看。 强烈建议采用 WPA2 加密。 旧式的加密类型， 如 WEP， 几乎没有任何安全性可言。

若要连接至一个无线网络， 首先需要扫描无线接入点。

![扫描无线接入点](img/bsdinstall-configure-wireless-scan.png)图 3.23. 扫描无线接入点

扫描完成后， 会列出所有发现的 SSID 以及它们支持的加密类型说明。 如果需要连接的 SSID 没有列出， 请选择 [ Rescan ] 再次扫描。 如果还没有出现， 请检查天线， 或将计算机移至更靠近接入点的地方。 在做过这些改善措施之后， 再重新扫描。

![选择一个无线网络](img/bsdinstall-configure-wireless-accesspoints.png)图 3.24. 选择一个无线网络

选择所要连接的无线网络， 即可输入连接所需的加密信息。 对于 WPA2， 只需输入一个密码 （也叫预共享密钥，( 简称 PSK）。 为安全起见， 在输入框中键入的字符将显示为星号。

![WPA2 设置](img/bsdinstall-configure-wireless-wpa2setup.png)图 3.25. WPA2 设置

在选择了无线网络并输入了连接所需的信息后， 网络配置将继续进行。

#### 3.9.2.2. 配置 IPv4 网络

选择是否使用 IPv4 网络。 这是最常见的网络连接类型。

![选择 IPv4 网络](img/bsdinstall-configure-network-interface-ipv4.png)图 3.26. 选择 IPv4 网络

有两种配置 IPv4 的方式。 *DHCP* 会自动地为网络接口进行正确的配置， 通常情况下， 这是首选的方式。 而 *Static* （静态） 方式则需要手工输入网络的配置信息。

### 注意:

不要随意输入网络的配置信息， 因为这样的话网络就无法正常工作。 请向网络管理员或服务提供商那里取得 第 3.3.3 节 “收集网络配置信息” 所列出的配置信息。

##### 3.9.2.2.1. 使用 DHCP 方式

若存在可用的 DHCP 服务器， 请选择 [ Yes ] 以自动配置网络接口。

![选择 DHCP 配置 IPv4](img/bsdinstall-configure-network-interface-ipv4-dhcp.png)图 3.27. 选择 DHCP 配置 IPv4

##### 3.9.2.2.2. 使用静态配置方式

网络接口的静态配置需要输入相关的 IPv4 配置信息。

![静态配置 IPv4](img/bsdinstall-configure-network-interface-ipv4-static.png)图 3.28. 静态配置 IPv4

*   `IP Address` - IP 地址， 即给当前计算机手动分配的 IPv4 地址。 此地址必须是唯一的， 并且在本地网络上还没有被其他设备使用。

*   `Subnet Mask` - 子网掩码， 用于本地网络。 通常是 `255.255.255.0`。

*   `Default Router`（默认路由） - 网络上默认路由的 IP 地址。 通常， 这是将本地网络连接至 Internet 的路由器或其他网络设备的地址。 也称作 *default gateway （默认网关）*。

#### 3.9.2.3. 配置 IPv6 网络

IPv6 是一种新的网络配置方式。 如果您有可用的 IPv6 连接， 并需要使用它， 选择 [ Yes ] 来开始配置。

![选择 IPv6 网络](img/bsdinstall-configure-network-interface-ipv6.png)图 3.29. 选择 IPv6 网络

IPv6 也有两种配置方式。 *SLAAC* ， 或 *StateLess Address AutoConfiguration* （无状态地址自动配置） 方式能够自动配置正确的网络接口， 而 *Static（静态）* 配置方式则需要手动输入网络信息。

##### 3.9.2.3.1. 使用 Stateless Address Autoconfiguration 方式

SLAAC 允许 IPv6 组件从本地路由器请求自动配置信息， 详情参见 [RFC4862](http://tools.ietf.org/html/rfc4862)。

![选择 SLAAC 配置 IPv6](img/bsdinstall-configure-network-interface-slaac.png)图 3.30. 选择 SLAAC 配置 IPv6

##### 3.9.2.3.2. 使用静态配置方式

网络接口的静态配置需要输入相关的 IPv6 配置信息。

![静态配置 IPv6](img/bsdinstall-configure-network-interface-ipv6-static.png)图 3.31. 静态配置 IPv6

*   `IPv6 Address` （IPv6 地址） - 为当前计算机手工分配的 IP 地址。 这个地址必须是唯一的， 并且没有被其他本地网络设备使用。

*   `Default Router` （默认路由） - 网络上默认路由的地址。 通常， 这是将本地网络连接至 Internet 的路由器或其他网络设备的地址。 也称作 *default gateway （默认网关）*。

#### 3.9.2.4. 配置 DNS

*Domain Name System* （域名系统，简称 *DNS*） 解析器用于主机名和网络地址间的相互转换。 如果使用的是 DHCP 或 SLAAC， 那么其配置很可能已经存在； 否则， 请在 Search 字段中输入本地网络的域名， 在 DNS #1 和 DNS #2 中输入本地 DNS 服务器的 IP 地址。 至少需要配置一个 DNS 服务器。

![DNS 配置](img/bsdinstall-configure-network-ipv4-dns.png)图 3.32. DNS 配置

### 3.9.3. 设置时区

为您的机器设置时区将允许其自动校时， 并正确执行一些与时区相关的操作。

示例中的机器位于美国东部时区。 根据所处的地理位置， 您的选择可能会有所不同。

![选择本地或 UTC 时钟](img/bsdinstall-set-clock-local-utc.png)图 3.33. 选择本地或 UTC 时钟

选择 [ Yes ] 或 [ No ] 以确定机器时钟的配置方式， 然后按 **Enter** 键。 如果您并不知道系统使用的是 UTC 还是本地时间， 请选择 [ No ] 以使用更为常见的本地时间。

![选择地区](img/bsdinstall-timezone-region.png)图 3.34. 选择地区

使用方向键选择合适的地区后按下 **Enter** 键。

![选择国家](img/bsdinstall-timezone-country.png)图 3.35. 选择国家

用方向键选择合适的国家后按下 **Enter** 键。

![选择时区](img/bsdinstall-timezone-zone.png)图 3.36. 选择时区

用方向键选择合适的时区后按下 **Enter** 键。

![确认时区选择](img/bsdinstall-timezone-confirm.png)图 3.37. 确认时区选择

确认时区的缩写是正确的， 然后按 **Enter** 键以继续安装后的配置。

### 3.9.4. 选择需要开启的服务

可以开启额外的系统服务， 它们会在系统启动时自动运行。 所有这些服务都是可选的。

![选择需要开启的服务](img/bsdinstall-config-services.png)图 3.38. 选择需要开启的服务额外的系统服务

*   `sshd` - Secure Shell（即 SSH） 守护进程， 提供安全的远程访问。

*   `moused` - 支持在系统控制台中使用鼠标。

*   `ntpd` - Network Time Protocol（网络时间协议， 简称 NTP） 守护进程， 提供时钟自动同步。

*   `powerd` - 系统电量控制程序， 用于控制电量及节能。

### 3.9.5. 启用崩溃转储

bsdinstall 将询问是否在目标系统上启用崩溃转储。 由于在调试系统时非常有用， 因此鼓励用户尽可能地启用崩溃转储。 选择 [ Yes ] 以启用崩溃转储， 或选择 [ No ] 以不启用崩溃转储。

![启用崩溃转储](img/bsdinstall-config-crashdump.png)图 3.39. 启用崩溃转储

### 3.9.6. 添加用户

在安装过程中， 应至少添加一位普通用户， 而不要始终以 `root` 身份登入。 当以 `root` 身份登入系统时， 系统几乎不会对其操作提供任何限制或保护。 以普通用户身份登录更为安全。

选择 [ Yes ] 来添加新用户。

![添加用户帐号](img/bsdinstall-adduser1.png)图 3.40. 添加用户帐号

为需要添加的用户输入信息。

![输入用户信息](img/bsdinstall-adduser2.png)图 3.41. 输入用户信息用户信息

*   `Username` - 用户名， 即登入时用户所输入的名称。 通常是名的首字母加姓的组合。

*   `Full name` - 用户的全名。

*   `Uid` - 用户 ID。 通常留空以自动分配。

*   `Login group` - 用户组。 通常留空以接受默认取值。

*   `Invite user into other groups?` - 是否同时将用户加入其他权限组？ 如果需要， 请输入权限组名称。

*   `Login class` - 登录类别。 通常留空以接受默认取值。

*   `Shell` - 用户 shell。 在本例中选择的是 [csh(1)](http://www.FreeBSD.org/cgi/man.cgi?query=csh&sektion=1&manpath=freebsd-release-ports)。

*   `Home directory` - 用户主目录。 通常留空以接受默认取值。

*   `Home directory permissions` - 用户主目录的权限。 通常留空以接受默认取值。

*   `Use password-based authentication?` - 是否使用基于密码的认证？ 通常为 “yes”。

*   `Use an empty password?` - 是否使用空密码？ 通常为 “no”。

*   `Use a random password?` - 是否使用随机密码？ 通常为 “no”。

*   `Enter password` - 用户的实际密码。 输入的字符不会在屏幕上显示。

*   `Enter password again` - 必须再次输入密码以进行验证。

*   `Lock out the account after creation?` - 创建后锁定帐号？ 通常为 “no”。

全部信息输入完成后， 系统会显示摘要并询问是否正确。 如果发现了错误， 可以输入 `no` 后进行修改； 如果没有错误， 请输入 `yes` 以创建新用户。

![退出用户与组管理](img/bsdinstall-adduser3.png)图 3.42. 退出用户与组管理

若需添加更多用户， 请在问题“Add another user?”后输入 `yes`； 输入 `no` 以完成用户添加并继续安装。

更多有关用户添加及管理的信息， 请参见 第 14 章 *用户和基本的帐户管理*。

### 3.9.7. 最终配置

所有的安装及配置完成后， 仍有机会对其进行修改。

![最终的配置菜单](img/bsdinstall-finalconfiguration.png)图 3.43. 最终的配置菜单

使用此菜单， 可以在完成安装前添加或修改任何配置。

最终的配置选项

*   `Add User` - 添加用户， 详见 第 3.9.6 节 “添加用户”。

*   `Root Password` - root 密码， 详见 第 3.9.1 节 “设置 `root` 密码”。

*   `Hostname` - 主机名， 详见 第 3.5.2 节 “设置主机名”。

*   `Network` - 网络， 详见 第 3.9.2 节 “配置网络接口”。

*   `Services` - 服务， 详见 第 3.9.4 节 “选择需要开启的服务”.

*   `Time Zone` - 时区， 详见 第 3.9.3 节 “设置时区”。

*   `Handbook` - 手册，将下载并安装 FreeBSD 使用手册（即本书）。

完成了最终配置后， 请选择 Exit 以继续安装。

![手动配置](img/bsdinstall-final-modification-shell.png)图 3.44. 手动配置

bsdinstall 会询问重启前是否还需要额外的配置： 选择 [ Yes ] 进入 shell 做这些配置， 选择 [ No ] 以执行安装的最后一步。

![完成安装](img/bsdinstall-mainexit.png)图 3.45. 完成安装

如果需要进一步的配置或特殊的设置， 可以选择 [ Live CD ] 来进入安装介质的 Live CD 模式。

安装完成后，选择 [ Reboot ] 重启计算机， 并开始使用全新的 FreeBSD 系统。 请不要忘记移除 FreeBSD 的安装 CD、 DVD 或 USB 记忆棒， 否则计算机可能会再次从这些介质启动。

### 3.9.8. FreeBSD 的启动与关闭

#### 3.9.8.1. FreeBSD/i386 的启动

FreeBSD 启动时会显示许多相关信息， 正常情况下屏幕会不断滚动， 而启动完成后则会显示一个登录提示符。 如果需要查看启动时的相关信息， 可以按下 **Scroll-Lock** 键开启 *scroll-back buffer（回滚缓存）*， 然后使用 **PageUp** 键、 **PageDown** 键与方向键行翻阅； 再次按下 **Scroll Lock** 键将关闭回滚缓存并返回正常的屏幕。

在 `login:` 提示符处输入安装时添加的用户名来登录系统， 本例中是 `asample`。 除非有必要，否则请勿作为 `root` 登录。

上述的回滚缓存大小有限， 因而未必全部可见。 登入系统后， 在提示符处输入 `dmesg | less`， 能够查看到绝大部分的启动信息， 查看后按 **q** 键返回命令行。

典型的启动信息（此处略去了版本信息）：

```
Copyright (c) 1992-2011 The FreeBSD Project.
Copyright (c) 1979, 1980, 1983, 1986, 1988, 1989, 1991, 1992, 1993, 1994
        The Regents of the University of California. All rights reserved.
FreeBSD is a registered trademark of The FreeBSD Foundation.

    root@farrell.cse.buffalo.edu:/usr/obj/usr/src/sys/GENERIC amd64
CPU: Intel(R) Core(TM)2 Duo CPU     E8400  @ 3.00GHz (3007.77-MHz K8-class CPU)
  Origin = "GenuineIntel"  Id = 0x10676  Family = 6  Model = 17  Stepping = 6
  Features=0x783fbff<FPU,VME,DE,PSE,TSC,MSR,PAE,MCE,CX8,APIC,SEP,MTRR,PGE,MCA,CMOV,PAT,PSE36,MMX,FXSR,SSE,SSE2>
  Features2=0x209<SSE3,MON,SSSE3>
  AMD Features=0x20100800<SYSCALL,NX,LM>
  AMD Features2=0x1<LAHF>
real memory  = 536805376 (511 MB)
avail memory = 491819008 (469 MB)
Event timer "LAPIC" quality 400
ACPI APIC Table: <VBOX   VBOXAPIC>
ioapic0: Changing APIC ID to 1
ioapic0 <Version 1.1> irqs 0-23 on motherboard
kbd1 at kbdmux0
acpi0: <VBOX VBOXXSDT> on motherboard
acpi0: Power Button (fixed)
acpi0: Sleep Button (fixed)
Timecounter "ACPI-fast" frequency 3579545 Hz quality 900
acpi_timer0: <32-bit timer at 3.579545MHz> port 0x4008-0x400b on acpi0
cpu0: <ACPI CPU> on acpi0
pcib0: <ACPI Host-PCI bridge> port 0xcf8-0xcff on acpi0
pci0: <ACPI PCI bus> on pcib0
isab0: <PCI-ISA bridge> at device 1.0 on pci0
isa0: <ISA bus> on isab0
atapci0: <Intel PIIX4 UDMA33 controller> port 0x1f0-0x1f7,0x3f6,0x170-0x177,0x376,0xd000-0xd00f at device 1.1 on pci0
ata0: <ATA channel 0> on atapci0
ata1: <ATA channel 1> on atapci0
vgapci0: <VGA-compatible display> mem 0xe0000000-0xe0ffffff irq 18 at device 2.0 on pci0
em0: <Intel(R) PRO/1000 Legacy Network Connection 1.0.3> port 0xd010-0xd017 mem 0xf0000000-0xf001ffff irq 19 at device 3.0 on pci0
em0: Ethernet address: 08:00:27:9f:e0:92
pci0: <base peripheral> at device 4.0 (no driver attached)
pcm0: <Intel ICH (82801AA)> port 0xd100-0xd1ff,0xd200-0xd23f irq 21 at device 5.0 on pci0
pcm0: <SigmaTel STAC9700/83/84 AC97 Codec>
ohci0: <OHCI (generic) USB controller> mem 0xf0804000-0xf0804fff irq 22 at device 6.0 on pci0
usbus0: <OHCI (generic) USB controller> on ohci0
pci0: <bridge> at device 7.0 (no driver attached)
acpi_acad0: <AC Adapter> on acpi0
atkbdc0: <Keyboard controller (i8042)> port 0x60,0x64 irq 1 on acpi0
atkbd0: <AT Keyboard> irq 1 on atkbdc0
kbd0 at atkbd0
atkbd0: [GIANT-LOCKED]
psm0: <PS/2 Mouse> irq 12 on atkbdc0
psm0: [GIANT-LOCKED]
psm0: model IntelliMouse Explorer, device ID 4
attimer0: <AT timer> port 0x40-0x43,0x50-0x53 on acpi0
Timecounter "i8254" frequency 1193182 Hz quality 0
Event timer "i8254" frequency 1193182 Hz quality 100
sc0: <System console> at flags 0x100 on isa0
sc0: VGA <16 virtual consoles, flags=0x300>
vga0: <Generic ISA VGA> at port 0x3c0-0x3df iomem 0xa0000-0xbffff on isa0
atrtc0: <AT realtime clock> at port 0x70 irq 8 on isa0
Event timer "RTC" frequency 32768 Hz quality 0
ppc0: cannot reserve I/O port range
Timecounters tick every 10.000 msec
pcm0: measured ac97 link rate at 485193 Hz
em0: link state changed to UP
usbus0: 12Mbps Full Speed USB v1.0
ugen0.1: <Apple> at usbus0
uhub0: <Apple OHCI root HUB, class 9/0, rev 1.00/1.00, addr 1> on usbus0
cd0 at ata1 bus 0 scbus1 target 0 lun 0
cd0: <VBOX CD-ROM 1.0> Removable CD-ROM SCSI-0 device
cd0: 33.300MB/s transfers (UDMA2, ATAPI 12bytes, PIO 65534bytes)
cd0: Attempt to query device size failed: NOT READY, Medium not present
ada0 at ata0 bus 0 scbus0 target 0 lun 0
ada0: <VBOX HARDDISK 1.0> ATA-6 device
ada0: 33.300MB/s transfers (UDMA2, PIO 65536bytes)
ada0: 12546MB (25694208 512 byte sectors: 16H 63S/T 16383C)
ada0: Previously was known as ad0
Timecounter "TSC" frequency 3007772192 Hz quality 800
Root mount waiting for: usbus0
uhub0: 8 ports with 8 removable, self powered
Trying to mount root from ufs:/dev/ada0p2 [rw]...
Setting hostuuid: 1848d7bf-e6a4-4ed4-b782-bd3f1685d551.
Setting hostid: 0xa03479b2.
Entropy harvesting: interrupts ethernet point_to_point kickstart.
Starting file system checks:
/dev/ada0p2: FILE SYSTEM CLEAN; SKIPPING CHECKS
/dev/ada0p2: clean, 2620402 free (714 frags, 327461 blocks, 0.0% fragmentation)
Mounting local file systems:.
vboxguest0 port 0xd020-0xd03f mem 0xf0400000-0xf07fffff,0xf0800000-0xf0803fff irq 20 at device 4.0 on pci0
vboxguest: loaded successfully
Setting hostname: machine3.example.com.
Starting Network: lo0 em0.
lo0: flags=8049<UP,LOOPBACK,RUNNING,MULTICAST> metric 0 mtu 16384
        options=3<RXCSUM,TXCSUM>
        inet6 ::1 prefixlen 128
        inet6 fe80::1%lo0 prefixlen 64 scopeid 0x3
        inet 127.0.0.1 netmask 0xff000000
        nd6 options=21<PERFORMNUD,AUTO_LINKLOCAL>
em0: flags=8843<UP,BROADCAST,RUNNING,SIMPLEX,MULTICAST> metric 0 mtu 1500
        options=9b<RXCSUM,TXCSUM,VLAN_MTU,VLAN_HWTAGGING,VLAN_HWCSUM>
        ether 08:00:27:9f:e0:92
        nd6 options=29<PERFORMNUD,IFDISABLED,AUTO_LINKLOCAL>
        media: Ethernet autoselect (1000baseT <full-duplex>)
        status: active
Starting devd.
Starting Network: usbus0.
DHCPREQUEST on em0 to 255.255.255.255 port 67
DHCPACK from 10.0.2.2
bound to 192.168.1.142 -- renewal in 43200 seconds.
add net ::ffff:0.0.0.0: gateway ::1
add net ::0.0.0.0: gateway ::1
add net fe80::: gateway ::1
add net ff02::: gateway ::1
ELF ldconfig path: /lib /usr/lib /usr/lib/compat /usr/local/lib
32-bit compatibility ldconfig path: /usr/lib32
Creating and/or trimming log files.
Starting syslogd.
No core dumps found.
Clearing /tmp (X related).
Updating motd:.
Configuring syscons: blanktime.
Generating public/private rsa1 key pair.
Your identification has been saved in /etc/ssh/ssh_host_key.
Your public key has been saved in /etc/ssh/ssh_host_key.pub.
The key fingerprint is:
10:a0:f5:af:93:ae:a3:1a:b2:bb:3c:35:d9:5a:b3:f3 root@machine3.example.com
The key's randomart image is:
+--[RSA1 1024]----+
|    o..          |
|   o . .         |
|  .   o          |
|       o         |
|    o   S        |
|   + + o         |
|o . + *          |
|o+ ..+ .         |
|==o..o+E         |
+-----------------+
Generating public/private dsa key pair.
Your identification has been saved in /etc/ssh/ssh_host_dsa_key.
Your public key has been saved in /etc/ssh/ssh_host_dsa_key.pub.
The key fingerprint is:
7e:1c:ce:dc:8a:3a:18:13:5b:34:b5:cf:d9:d1:47:b2 root@machine3.example.com
The key's randomart image is:
+--[ DSA 1024]----+
|       ..     . .|
|      o  .   . + |
|     . ..   . E .|
|    . .  o o . . |
|     +  S = .    |
|    +  . = o     |
|     +  . * .    |
|    . .  o .     |
|      .o. .      |
+-----------------+
Starting sshd.
Starting cron.
Starting background file system checks in 60 seconds.

Thu Oct  6 19:15:31 MDT 2011

FreeBSD/amd64 (machine3.example.com) (ttyv0)

login:
```

在较慢的机器上， 生成 RSA 和 DSA 密钥可能需要一些时间。 这种情况只会在开启了 sshd 的新系统首次启动时发生， 之后的启动速度不受影响。

FreeBSD 默认情况下并不会安装图形环境， 但提供了多种不同的选择。 请参阅 第 6 章 *X Window 系统* 了解详情。

### 3.9.9. 关闭 FreeBSD

正常关闭 FreeBSD 有助于保护数据及系统硬件不受损坏。 不要直接关闭电源。 如果用户是 `wheel` 组的成员， 首先在命令行中输入 `su` 后键入 `root` 密码成为超级用户。 此外， 也可作为 `root` 登录， 然后使用命令 `shutdown -p now`。 这样系统将安全地自行关闭。

虽然也可以使用组合键 **Ctrl**+**Alt**+**Del** 重启系统， 但正常情况下并不推荐这样做。

## 3.10. 故障排除

下面将介绍如何排除基本的安装故障， 例如用户经常报告的问题。

### 3.10.1. 遇到错误时该如何处理

由于 PC 架构的各种限制， 硬件检测不可能 100% 地可靠探测， 然而， 当此类现象发生时， 您有可能可以通过一些操作来自行解决它们。

首先应该根据所安装的 FreeBSD 版本核对 [硬件兼容说明](http://www.FreeBSD.org/releases/index.html) 文档， 以确保其支持您的硬件。

如果使用被支持的硬件时仍遇到了死机或其他问题， 请联编一个 自定义内核， 这样即可为那些 `GENERIC` 内核中不存在的设备提供支持。 引导盘上的内核假定绝大多数硬件的 IRQ、 IO 地址和 DMA 通道均为出厂设置， 如果您的硬件被重新配置过， 就很可能需要修改内核配置文件并重新编译内核， 以支持 FreeBSD 侦测这些硬件。

还可能出现一种情况， 检测某个不存在的设备会导致稍后对其他存在的设备检测失败。 在这种情况下， 应该禁止检测引起冲突的设备所对应的驱动程序。

### 注意:

有些安装问题可以通过更新硬件固件来避免或改善， 尤其是主板。 主板固件通常被称作 BIOS， 大多数主板和计算机制造商都拥有提供升级和相关信息的网站。

制造商通常建议， 除非有类似关键更新这种必要的原因， 否则应避免升级主板 BIOS。 升级过程一旦出现错误， BIOS 信息将遭到破坏， 从而导致计算机无法工作。

### 3.10.2. 故障排除问答

| **3.10.2.1.** | 在启动时， 我的系统在检测硬件时挂起， 或在安装过程中行为异常。 |
|  | 在 i386、 amd64 和 ia64 平台的启动过程中， FreeBSD 广泛使用了 ACPI 服务来检测系统配置， 不幸的是 ACPI 驱动和主板 BIOS 中仍存在一些 bug。 在第三阶段引导加载器中， 可以通过设置 `hint.acpi.0.disabled` 来禁用 ACPI： 
```
`set hint.acpi.0.disabled="1"`
```

这一设置会在系统重启后失效， 因此必须将 `hint.acpi.0.disabled="1"` 添加至文件 `/boot/loader.conf` 中。 关于引导加载器的更多信息， 请参见 第 13.1 节 “概述”。 |

## 第 4 章 UNIX 基础

Rewritten by Chris Shumway.

## 4.1. 概述

下列章节的命令和功能适用于 FreeBSD 操作系统。 同时这里许多内容和一些 类-UNIX® 操作系统相关。 假如您已经熟悉这些内容可跳过不阅读。 假如您是 FreeBSD 新手， 那您应该认真详细地从头到尾读一遍这些章节。

读取这些内容，您将了解：

*   怎样在 FreeBSD 使用 “虚拟控制台”。

*   在 UNIX® 中文件权限如何运作， 以及理解 FreeBSD 中的文件标志。

*   FreeBSD 默认文件系统的架构。

*   FreeBSD 磁盘架构。

*   怎样挂接或卸下文件系统。

*   什么是进程、守护进程、信号。

*   什么是 shell，应当怎样去改变登录进入的默认环境。

*   怎样使用基本的文本编辑器。

*   什么是设备，什么是设备节点。

*   FreeBSD 下，使用的是什么可执行文件格式。

*   怎样使用 man 手册并取得更多资讯。

## 4.2. 虚拟控制台和终端

可以用多种不同的方式使用 FreeBSD， 在文本终端输入命令是其中之一。 通过使用这种方式， 您可以容易地使用 FreeBSD 来获得 UNIX® 操作系统的灵活而强大的功能。 这一节将介绍 “终端” 和 “控制台”， 以及如何在 FreeBSD 中使用它们。

### 4.2.1. 控制台

假如您没有设置 FreeBSD 在启动期间开启图形登录界面， 那么系统将在引导和启动脚本正确运行完成后，给您一个登录的提示。 您会看到类似这样的界面:

```
Additional ABI support:.
Local package initialization:.
Additional TCP options:.

Fri Sep 20 13:01:06 EEST 2002

FreeBSD/i386 (pc3.example.org) (ttyv0)

login:
```

这些信息可能和您的系统稍微有点不同，但不会有很大差别。 最后两行是我们感兴趣的， 理解这一行:

```
FreeBSD/i386 (pc3.example.org) (ttyv0)
```

这一行是您刚才启动的系统信息其中一块， 您所看到的是一个“FreeBSD”控制台， 运行在一个 Intel 或兼容的 x86 体系架构上面[^([1])](#ftn.idp70957424)。 这台计算机的名字 (每台 UNIX® 计算机都有自己的名字) 叫 `pc3.example.org`， 就是现在这个系统控制台──这个 `ttyv0` 终端的样子。

在最后，最后一行一直保持这样:

```
login:
```

这里， 您将可以输入用户名 “username” 并登录到 FreeBSD 系统中。 接下来的一节， 将介绍如何登录系统。

### 4.2.2. 进入 FreeBSD

FreeBSD 是一个多用户多任务的系统， 换句话来说就是一个系统中可以容纳许多不同的用户， 而这些用户都可以同时在这台机器中运行大量的程序。

每一个多用户系统都必须在某方面去区分 “user”， 在 FreeBSD 里 (以及 类-UNIX® 操作系统)， 完成这方面工作是有必要的， 因而， 每位使用者在运行程序之前都必须首先 “登录”， 而每位用户都有与之对应的用户名 (“username”) 和密码 (“password”)。 FreeBSD 会在用户进入之前作出询问这两项信息。

当 FreeBSD 引导并运行完启动脚本之后， [^([2])](#ftn.idp70970992)， 它会给出一个提示， 并要求输入有效的用户名：

```
login:
```

举个例子更容易理解，我们假设您的用户名叫 `john`。 在提示符下输入 `john` 并按 **Enter**， 此时您应该看到这个提示 “password”：

```
login: `john`
Password:
```

现在输入 `john`的密码并按下 **Enter**。 输入密码时是 *不回显的!* 不必为此担心， 这样做是出于安全考虑。

假如您输入的密码是正确的， 这时你应该已进入 FreeBSD， 并可以开始尝试可用的命令了。

您应该看见 MOTD 或者出现一个命令提示符 (`#`、`$` 或 `%` 字符). 这表明您已成功登录进入 FreeBSD。

### 4.2.3. 多个控制台

在一个控制台运行 UNIX® 命令虽说很好， 但 FreeBSD 具有一次运行 多个程序的能力。 仅使用一个控制台只会浪费 FreeBSD 同时运行多任务的能力。 而 “虚拟控制台” 在这方面发挥强大的功能。

FreeBSD 能配置出满足您不同需求的虚拟控制台， 在键盘上您用一组键就能从各个虚拟控制台之间切换。 各个控制台有自己的传输通道， 当您在各个控制台切换时 FreeBSD 会切换到合适的键盘传输通道和显示器传输通道。

FreeBSD 各个控制台之间可利用特殊组键切换并保留原有控制台 [^([3])](#ftn.idp70990448)，您可这样做: **Alt**+**F1**， **Alt**+**F2**， 一直到 **Alt**+**F8** 在 FreeBSD 里切换到其中一个虚拟控制台。

同样地, 您正在从其中某个控制台切换到另一个控制台的时候, FreeBSD 会保存正在使用和恢复将要使用屏幕传输通道。 这种结果形成一种 “错觉”， 您拥有许多“虚拟”屏幕和键盘可以输入很多的命令。 这些程序需要在一个虚拟控制台不能停止运行而又不需要观察它， 它继续运行而您可以切换到其他的虚拟控制台。

### 4.2.4. `/etc/ttys`文件

FreeBSD 虚拟控制台的默认配置为 8 个，但并不是硬性设置， 您可以很容易设置虚拟控制台的个数增多或减少。 虚拟控制台的的编号和设置在 `/etc/ttys` 文件里。

您可以使用 `/etc/ttys` 文件在 FreeBSD 下配置虚拟控制台。 文件里每一未加注释的行都能设置一个终端或虚拟控制台 (当行里含有 `#` 这个字符时不能使用) 。 FreeBSD 默认配置是配置出 9 个虚拟控制台而只能启动 8 个， 以下这些行是 `ttyv` 一起启动:

```
# name  getty                           type    status          comments
#
ttyv0   "/usr/libexec/getty Pc"         cons25  on  secure
# Virtual terminals
ttyv1   "/usr/libexec/getty Pc"         cons25  on  secure
ttyv2   "/usr/libexec/getty Pc"         cons25  on  secure
ttyv3   "/usr/libexec/getty Pc"         cons25  on  secure
ttyv4   "/usr/libexec/getty Pc"         cons25  on  secure
ttyv5   "/usr/libexec/getty Pc"         cons25  on  secure
ttyv6   "/usr/libexec/getty Pc"         cons25  on  secure
ttyv7   "/usr/libexec/getty Pc"         cons25  on  secure
ttyv8   "/usr/X11R6/bin/xdm -nodaemon"  xterm   off secure
```

如果要了解这个文件中每一列的详细介绍， 以及虚拟控制台上所能使用的配置， 请参考联机手册 [ttys(5)](http://www.FreeBSD.org/cgi/man.cgi?query=ttys&sektion=5&manpath=freebsd-release-ports)。

### 4.2.5. 单用户模式的控制台

关于 “单用户模式” 详细介绍在 第 13.6.2 节 “单用户模式” 这里可以找到。 当您运行单用户模式时只能使用一个控制台， 没有多个虚拟控制台可使用。 单用户模式的控制台同也可以在 `/etc/ttys` 文件设置， 可在这行找到要启动的`控制台`：

```
# name  getty                           type    status          comments
#
# If console is marked "insecure", then init will ask for the root password
# when going to single-user mode.
console none                            unknown off secure
```

### 注意:

这个 `console` 已经注释掉, 您可编辑这行把 `secure` 改为 `insecure`。 这样， 当用单用户进入 FreeBSD 时， 它仍然要求提供 `root` 用户的密码。

*在把这个选项改为 `insecure`* 的时候一定要小心， 如果您忘记了 `root`用户的密码， 进入单用户会有点麻烦。 尽管仍然能进入单用户模式， 但如果您不熟悉它就会非常令人头疼。

### 4.2.6. 改变控制台的显示模式

FreeBSD 控制台默认的显示模式可以被调整为 1024x768， 1280x1024， 或者任何你的显卡芯片和显示器所支持的其他尺寸。 要使用一个不同的显示模式， 你必须首先重新编译内核并包含以下 2 个选项：

```
options VESA
options SC_PIXEL_MODE
```

在内核用这 2 个选项编译完成后，你就可以使用 [vidcontrol(1)](http://www.FreeBSD.org/cgi/man.cgi?query=vidcontrol&sektion=1&manpath=freebsd-release-ports) 工具来测定你的硬件支持何种显示模式了。 以 root 身份在控制台键入以下命令来获得一份所支持的显示模式列表。

```
# `vidcontrol -i mode`
```

这个命令的输出是一份你的硬件所支持的显示模式列表。 你可以在以 root 身份在控制台上键入 [vidcontrol(1)](http://www.FreeBSD.org/cgi/man.cgi?query=vidcontrol&sektion=1&manpath=freebsd-release-ports) 命令来改变显示模式：

```
# `vidcontrol MODE_279`
```

如果你对于新的显示模式满意，那么可以把它加入到 `/etc/rc.conf` 使机器在每次启动的时候都能生效， 我们使用了上一个例子中的模式：

```
allscreens_flags="MODE_279"
```

* * *

[^([1])](#idp70957424) 现在理解一下`i386`的含义。 请注意尽管您的 FreeBSD 并非在 Intel 386 CPU 上运行， 但也会显示为 `i386`。 这不是指您的处理器， 而是指处理器的 “体系结构”。

[^([2])](#idp70970992) 启动脚本这些程序在 FreeBSD 在启动过程中运行。 它们的主要功能为其他每方面的运行作好准备， 和运行您的配置所用到的相关环境。

[^([3])](#idp70990448) 关于 FreeBSD 的控制台和键盘设备这些详细资料或使用技巧可在手册里找到: [syscons(4)](http://www.FreeBSD.org/cgi/man.cgi?query=syscons&sektion=4&manpath=freebsd-release-ports)、[atkbd(4)](http://www.FreeBSD.org/cgi/man.cgi?query=atkbd&sektion=4&manpath=freebsd-release-ports)、[vidcontrol(1)](http://www.FreeBSD.org/cgi/man.cgi?query=vidcontrol&sektion=1&manpath=freebsd-release-ports) 和 [kbdcontrol(1)](http://www.FreeBSD.org/cgi/man.cgi?query=kbdcontrol&sektion=1&manpath=freebsd-release-ports)。 我们不在这里详细介绍， 但是爱好者总会在手册里找到详细的答案。

## 4.3. 权限

FreeBSD，是 BSD UNIX® 的延续， 并基于几个关键的 UNIX® 观念。 从一开始就多处提到 FreeBSD 是一个多用户的操作系统， 它能分别处理几个同时工作的用户所分配的毫无关联任务。 并负责为每位用户的硬件设备、 外设、 内存和 CPU 处理时间作出合理安排。

因为系统有能力支持多用户， 在每一方面系统都会作出谁能读、 写和执行的资源权力限制。 这点权限以三个八位元的方式储存着， 一个是表示文件所属者， 一个是表示文件所属群组， 一个是表示其他人。 这些数字以下列方式表示：

| 数值 | 权限 | 目录列表 |
| --- | --- | --- |
| 0 | 不能读，不能写，不能执行 | `---` |
| 1 | 不能读，不能写，可执行 | `--x` |
| 2 | 不能读，可写，不能执行 | `-w-` |
| 3 | 不能读，可写，可执行 | `-wx` |
| 4 | 可读，不能写，不能执行 | `r--` |
| 5 | 可读，不能写，可执行 | `r-x` |
| 6 | 可读，可写，不能执行 | `rw-` |
| 7 | 可读，可写，可执行 | `rwx` |

使用命令的 `-l` ([ls(1)](http://www.FreeBSD.org/cgi/man.cgi?query=ls&sektion=1&manpath=freebsd-release-ports)) 参数可以显示出文件的所属者、 所属组和其他人等属性。 请看以下的例子：

```
% `ls -l`
total 530
-rw-r--r--  1 root  wheel     512 Sep  5 12:31 myfile
-rw-r--r--  1 root  wheel     512 Sep  5 12:31 otherfile
-rw-r--r--  1 root  wheel    7680 Sep  5 12:31 email.txt
...
```

使用 `ls -l` 在每行的开始出现了：

```
-rw-r--r--
```

从左边起的第一个字，告诉我们这个文件是一怎样的文件: 普通文件?目录?特殊设备?socket?或是设备文件? 在这个例子， `-` 表示一个普通文件。 接下来三个字是 `rw-` 是文件拥有者的权限。 再接下来的三个字是 `r--` 是文件所属群组的权限。 最後三个字是 `r--` 是其他人的权限。 以这一个文件为例，他的权限设定是拥有者可以读写这个文件、群组可以读取、 其他使用者也能读取这个文件。 根据上面的表格， 用数字表示这个文件其三部分的权限应该是 `644`。

这样很好，但系统怎样对设备进行权限控制的? 事实上 FreeBSD 将大部份硬件设备当作一个文件看待， 用程序能打开、读取、写入数据就如其他的文件一样。 而设备文件放在 `/dev` 目录。

目录也视为一种文件，也有读取、写入、执行的权限。 但目录的执行权限意义并不与普通文件相同， 实际上执行权限是进入权限。 当一个目录是被标示可以执行的时， 表示可以进入它， 或者换言之， 利用 “cd” (改变当前目录) 进入它。 此外， 这也表示有权进入目录的用户， 可以访问其下的已知名字的文件 (当然目录下的文件也受到访问限制)。

详细方面，想读取一个目录的列表就必须设为可读权限， 同时想删除一个已知的文件，就必须把目录下这个文件设为可写 *和* 执行权限。

还有更多权限设定， 但是他们大多用在特殊状况下如一个 setuid 的执行文件和粘贴性目录， 如果想要得知有关文件权限和如何设定的更多资讯，请看手册[chmod(1)](http://www.FreeBSD.org/cgi/man.cgi?query=chmod&sektion=1&manpath=freebsd-release-ports)。

### 4.3.1. 权限的符号化表示

Contributed by Tom Rhodes.

权限符号，某些时候就是指符号表达式， 使用八进制的字符给目录或文件分配权限。 权限符号的使用语法是 (谁) (作用) (权限)。 看看下列数值的在那些地方所起什么样的作用:

| 选项 | 字母 | 介绍 |
| --- | --- | --- |
| (谁) | u | 用户 |
| (谁) | g | 所属群体 |
| (谁) | o | 其他人 |
| (谁) | a | 所有人 (“全部”) |
| (作用) | + | 增加权限 |
| (作用) | - | 减少权限 |
| (作用) | = | 确定权限 |
| (权限) | r | 可读 |
| (权限) | w | 可写 |
| (权限) | x | 执行 |
| (权限) | t | 粘贴位 |
| (权限) | s | 设置 UID 或 GID |

这些数值 [chmod(1)](http://www.FreeBSD.org/cgi/man.cgi?query=chmod&sektion=1&manpath=freebsd-release-ports) 以习惯标定的。 举个例子，用以下命令阻止其他人访问 *`FILE`*文件:

```
% `chmod go= FILE`
```

如果需要对文件一次进行多项变动， 则可用逗号分开， 在下面的例子中， 将去掉 *`FILE`* 文件的群体和 “全体其他用户” 可写权限， 并为所有人增加可执行权限：

```
% `chmod go-w,a+x FILE`
```

### 4.3.2. FreeBSD 文件标志

Contributed by Tom Rhodes.

在前面所介绍的文件权限的基础之上， FreeBSD 还支持使用 “文件标志”。 这些标志为文件提供了进一步的安全控制机制， 但这些控制并不适用于目录。

这些文件标志提供了针对文件的进一步控制， 帮助确保即使是 `root` 用户也无法删除或修改文件。

文件标志可以通过使用 [chflags(1)](http://www.FreeBSD.org/cgi/man.cgi?query=chflags&sektion=1&manpath=freebsd-release-ports) 工具来修改， 其用户界面很简单。 例如， 要在文件 `file1` 上应用系统禁删标志， 应使用下述命令：

```
# `chflags sunlink file1`
```

要禁用系统禁删标志， 只需在前述命令中的 `sunlink` 标志前加 “no”。 例如：

```
# `chflags nosunlink file1`
```

要显示文件上的标志， 应使用命令 [ls(1)](http://www.FreeBSD.org/cgi/man.cgi?query=ls&sektion=1&manpath=freebsd-release-ports) 的 `-lo` 参数：

```
# `ls -lo file1`
```

输出结果应类似于：

```
-rw-r--r--  1 trhodes  trhodes  sunlnk 0 Mar  1 05:54 file1
```

许多标志只可以由 `root` 用户来增加， 而另一些， 则可以由文件的所有者来增加。 建议管理员仔细阅读 [chflags(1)](http://www.FreeBSD.org/cgi/man.cgi?query=chflags&sektion=1&manpath=freebsd-release-ports) 和 [chflags(2)](http://www.FreeBSD.org/cgi/man.cgi?query=chflags&sektion=2&manpath=freebsd-release-ports) 联机手册， 以对其加深理解。

### 4.3.3. setuid、 setgid 和 sticky 权限

原作 Tom Rhodes.

除了前面已经讨论过的那些权限之外， 还有三个管理员应该知道的权限配置。 它们是 `setuid`、 `setgid` 和 `sticky`。

这些配置对于一些 UNIX® 操作而言很重要， 因为它们能提供一些一般情况下不会授予普通用户的功能。 为了便于理解， 我们首先介绍真实用户 ID (real user ID) 和生效用户 ID (effective user ID)。

真实用户 ID 是拥有或启动进程的用户 UID。 生效 UID 是进程以其身份运行的用户 ID。 举例来说， [passwd(1)](http://www.FreeBSD.org/cgi/man.cgi?query=passwd&sektion=1&manpath=freebsd-release-ports) 工具通常是以发起修改密码的用户身份启动， 也就是说其进程的真实用户 ID 是那个用户的 ID； 但是， 由于需要修改密码数据库， 它会以 `root` 用户作为生效用户 ID 的身份运行。 这样， 普通的非特权用户就可以修改口令， 而不是看到 Permission Denied 错误了。

### 注意:

[mount(8)](http://www.FreeBSD.org/cgi/man.cgi?query=mount&sektion=8&manpath=freebsd-release-ports) 的 `nosuid` 选项可以令系统在不给出任何错误提示的情况下不执行这些程序。 另一方面， 这个选项并不是万无一失的， 正如 [mount(8)](http://www.FreeBSD.org/cgi/man.cgi?query=mount&sektion=8&manpath=freebsd-release-ports) 联机手册所提到的那样， 如果系统中安装了绕过 `nosuid` 的封装程序， 那么这种保护就可以被绕过了。

setuid 权限可以通过在普通权限前面加上一个数字四 (4) 来设置， 如下面的例子所示：

```
# `chmod 4755 suidexample.sh`
```

这样一来， `suidexample.sh` 的权限应该如下面这样：

```
-rwsr-xr-x   1 trhodes  trhodes    63 Aug 29 06:36 suidexample.sh
```

您会注意到， 在原先的属主执行权限的位置变成了 `s`。 这样， 需要提升特权的可执行文件， 例如 `passwd` 就可以正常运行了。

可以打开两个终端来观察这一情形。 在其中一个终端里面， 以普通用户身份启动 `passwd` 进程。 在它等待输入新口令时， 在另一个终端中查看进程表中关于 `passwd` 命令的信息。

在终端 A 中：

```
Changing local password for trhodes
Old Password:
```

在终端 B 中：

```
# `ps aux | grep passwd`
```

```
trhodes  5232  0.0  0.2  3420  1608   0  R+    2:10AM   0:00.00 grep passwd
root     5211  0.0  0.2  3620  1724   2  I+    2:09AM   0:00.01 passwd
```

正如前面所说的那样， `passwd` 是以普通用户的身份启动的， 但其生效 UID 是 `root`。

与此对应， `setgid` 权限的作用， 与 `setuid` 权限类似， 只是当应用程序配合这一设定运行时， 它会被授予拥有文件的那个组的权限。

如果需要在文件上配置 `setgid` 权限， 可以在权限数值前面增加数字二 (2) 来运行 `chmod` 命令， 如下面的例子所示：

```
# `chmod 2755 sgidexample.sh`
```

可以用与前面类似的方法来检视新设定的生效情况， 在组权限的地方的 `s` 表示这一配置已经生效：

```
-rwxr-sr-x   1 trhodes  trhodes    44 Aug 31 01:49 sgidexample.sh
```

### 注意:

在这些例子中， 尽管 shell 脚本也属于可执行文件的一种， 但它们不会以您配置的 EUID 或生效用户 ID 的身份运行。 这是因为 shell 脚本可能无法直接呼叫 [setuid(2)](http://www.FreeBSD.org/cgi/man.cgi?query=setuid&sektion=2&manpath=freebsd-release-ports) 调用。

我们已经讨论了两个特殊权限位 (`setuid` 和 `setgid` 权限位)， 它们让用户在使用程序时能够用到更高的权限， 有时这会削弱系统的安全性。 除了这两个之外， 还有第三个特殊权限位： `sticky bit`， 它能够增强安全性。

当在目录上设置了 `sticky bit` 之后， 其下的文件就只能由文件的所有者删除了。 这个权限设置能够防止用户删除类似 `/tmp` 这样的公共目录中不属于他们的文件。 要应用这种权限， 可以在权限设置前面加上数字一 (1)。 例如：

```
# `chmod 1777 /tmp`
```

现在， 可以用 `ls` 命令来查看效果：

```
# `ls -al / | grep tmp`
```

```
drwxrwxrwt  10 root  wheel         512 Aug 31 01:49 tmp
```

这里的结尾的 `t` 表示了 `sticky bit` 权限。

## 4.4. 目录架构

理解 FreeBSD 的目录层次结构对于建立对系统整体的理解十分重要的基础。 其中， 最重要的概念是根目录， “/”。 这个目录是系统引导时挂接的第一个目录， 它包含了用以准备多用户操作所需的操作系统基础组件。 根目录中也包含了用于在启动时转换到多用户模式之前挂接其他文件系统所需的挂接点。

挂接点 (mount point) 是新增的文件系统在接入现有系统时的起点位置 (通常是根目录)。 在 第 4.5 节 “磁盘组织” 对此进行了详细的阐述。 标准的挂接点包括 `/usr`、 `/var`、 `/tmp`、 `/mnt`， 以及 `/cdrom`。 这些目录通常会在 `/etc/fstab` 文件中提及。 `/etc/fstab` 是一张包含系统中各个文件系统及挂接点的表。 在 `/etc/fstab` 中的绝大多数文件系统都会在启动时由 [rc(8)](http://www.FreeBSD.org/cgi/man.cgi?query=rc&sektion=8&manpath=freebsd-release-ports) 脚本自动挂接， 除非特别指定了 `noauto` 选项。 更多细节请参考 第 4.6.1 节 “`fstab` 文件”。

您可以通过 [hier(7)](http://www.FreeBSD.org/cgi/man.cgi?query=hier&sektion=7&manpath=freebsd-release-ports) 来了解完整的文件系统层次说明。 现在， 让我们先来看一看绝大多数的常见的目录以供参考。

| 目录 | 介绍 |
| --- | --- |
| `/` | 文件系统的根目录。 |
| `/bin/` | 在单个用户和多用户环境下的基本工具目录。 |
| `/boot/` | 在操作系统在启动加载期间所用的程序和配置。 |
| `/boot/defaults/` | 默认每步引导启动的配置内容，请查阅[loader.conf(5)](http://www.FreeBSD.org/cgi/man.cgi?query=loader.conf&sektion=5&manpath=freebsd-release-ports)。 |
| `/dev/` | 设备节点，请查阅 [intro(4)](http://www.FreeBSD.org/cgi/man.cgi?query=intro&sektion=4&manpath=freebsd-release-ports)。 |
| `/etc/` | 系统启动的配置和脚本。 |
| `/etc/defaults/` | 系统默认的启动配置和脚本，请参考 [rc(8)](http://www.FreeBSD.org/cgi/man.cgi?query=rc&sektion=8&manpath=freebsd-release-ports) 。 |
| `/etc/mail/` | 关系到邮件系统运作的配置， 请参考 [sendmail(8)](http://www.FreeBSD.org/cgi/man.cgi?query=sendmail&sektion=8&manpath=freebsd-release-ports)。 |
| `/etc/namedb/` | `named` 配置文件，请参考 [named(8)](http://www.FreeBSD.org/cgi/man.cgi?query=named&sektion=8&manpath=freebsd-release-ports)。 |
| `/etc/periodic/` | 每天、每星期和每月周期性地运行的脚本， 请通过 [cron(8)](http://www.FreeBSD.org/cgi/man.cgi?query=cron&sektion=8&manpath=freebsd-release-ports)查阅 [periodic(8)](http://www.FreeBSD.org/cgi/man.cgi?query=periodic&sektion=8&manpath=freebsd-release-ports)。 |
| `/etc/ppp/` | `ppp`配置文件，请查阅[ppp(8)](http://www.FreeBSD.org/cgi/man.cgi?query=ppp&sektion=8&manpath=freebsd-release-ports)。 |
| `/mnt/` | 由管理员习惯使用挂接点的临时空目录。 |
| `/proc/` | 运行中的文件系统，请参阅 [procfs(5)](http://www.FreeBSD.org/cgi/man.cgi?query=procfs&sektion=5&manpath=freebsd-release-ports) 和 [mount_procfs(8)](http://www.FreeBSD.org/cgi/man.cgi?query=mount_procfs&sektion=8&manpath=freebsd-release-ports)。 |
| `/rescue/` | 用于紧急恢复的一组静态联编的程序； 参见 [rescue(8)](http://www.FreeBSD.org/cgi/man.cgi?query=rescue&sektion=8&manpath=freebsd-release-ports)。 |
| `/root/` | `root`用户的 Home(主)目录。 |
| `/sbin/` | 在单个用户和多用户环境下的存放系统程序和管理所需的基本实用目录。 |
| `/tmp/` | 临时文件。 `/tmp` 目录中的内容， 一般不会在系统重新启动之后保留。 通常会将基于内存的文件系统挂在 `/tmp` 上。 这一工作可以用一系列 tmpmfs 相关的 [rc.conf(5)](http://www.FreeBSD.org/cgi/man.cgi?query=rc.conf&sektion=5&manpath=freebsd-release-ports) 变量来自动完成。 (或者， 也可以在 `/etc/fstab` 增加对应项； 参见 [mdmfs(8)](http://www.FreeBSD.org/cgi/man.cgi?query=mdmfs&sektion=8&manpath=freebsd-release-ports))。 |
| `/usr/` | 存放大多数用户的应用软件。 |
| `/usr/bin/` | 存放实用命令，程序设计工具，和应用软件。 |
| `/usr/include/` | 存放标准 C include 文件. |
| `/usr/lib/` | 存放库文件。 |
| `/usr/libdata/` | 存放各种实用工具的数据文件。 |
| `/usr/libexec/` | 存放系统实用或后台程序 (从另外的程序启动执行)。 |
| `/usr/local/` | 存放本地执行文件， 库文件等等， 同时也是 FreeBSD ports 安装的默认安装目录。 `/usr/local` 在 `/usr` 中的目录布局大体相同， 请查阅 [hier(7)](http://www.FreeBSD.org/cgi/man.cgi?query=hier&sektion=7&manpath=freebsd-release-ports)。 但 man 目录例外， 它们是直接放在 `/usr/local` 而不是 `/usr/local/share` 下的， 而 ports 说明文档在 `share/doc/port`。 |
| `/usr/obj/` | 通过联编 `/usr/src` 得到的目标文件。 |
| `/usr/ports/` | 存放 FreeBSD 的 Ports Collection (可选)。 |
| `/usr/sbin/` | 存放系统后台程序 和 系统工具 (由用户执行)。 |
| `/usr/share/` | 存放架构独立的文件。 |
| `/usr/src/` | 存放 BSD 或者本地源码文件。 |
| `/usr/X11R6/` | 存放 X11R6 可执行文件、 库文件、 配置文件等的目录(可选)。 |
| `/var/` | 多用途日志、 临时或短期存放的， 以及打印假脱机系统文件。 有时会将基于内存的文件系统挂在 `/var` 上。 这一工作可以通过在 [rc.conf(5)](http://www.FreeBSD.org/cgi/man.cgi?query=rc.conf&sektion=5&manpath=freebsd-release-ports) 中设置一系列 varmfs 变量 (或在 `/etc/fstab` 中加入一行配置； 参见 [mdmfs(8)](http://www.FreeBSD.org/cgi/man.cgi?query=mdmfs&sektion=8&manpath=freebsd-release-ports)) 来完成。 |
| `/var/log/` | 存放各种的系统记录文件。 |
| `/var/mail/` | 存放用户 mailbox(一种邮件存放格式)文件。 |
| `/var/spool/` | 各种打印机和邮件系统 spooling(回环)的目录。 |
| `/var/tmp/` | 临时文件。 这些文件在系统重新启动时通常会保留， 除非 `/var` 是一个内存中的文件系统。 |
| `/var/yp/` | NIS 映射。 |

## 4.5. 磁盘组织

FreeBSD 查找文件的最小单位是文件名。 而文件名区分大小写，这就意味着 `readme.txt` 和 `README.TXT` 是两个不相同的文件。 FreeBSD 不凭文件扩展名 (`.txt`) 去识别这个文件是 程序、 文档， 或是其他格式的数据。

各种文件存放在目录里。 一个目录可以为空， 也可以含有多个的文件。一个目录同样可以包含其他的目录， 允许您在一个目录里建立多个不同层次的目录。 这将帮助您轻松地组织您的数据。

文件或目录是由文件名或目录名，加上斜线符号 `/`， 再根据需要在目录名后面加上其他目录的名称。 如果您有一个名为 `foo` 的目录， 它包含另一个目录 `bar`， 后者包括一个叫 `readme.txt` 的文件， 则全名， 或者说到文件的 *路径* 就是 `foo/bar/readme.txt`。

在文件系统里目录和文件的作用是存储数据。 每一个文件系统都有且只有一个顶级目录 *根目录*， 这个根目录则可以容纳其他目录。

您也许在其他的一些操作系统碰到类似这里的情况， 当然也有不同的情况。 举些例子， MS-DOS® 是用 `\` 分隔文件名或目录名， 而 Mac OS® 则使用`:`。

FreeBSD 在路径方面不使用驱动器名符号或驱动器名称， 在 FreeBSD 里您不能这样使用： `c:/foo/bar/readme.txt`。

为了代替(驱动器名符号)， 一个文件系统会指定 *根 文件系统*， 根文件系统的根目录是 `/`。 其他每一个文件系统 *挂接在*根文件系统下。 无论有多少磁盘在 FreeBSD 系统里， 每个磁盘都会以目录的方式加上。

假设您有三个文件系统， 名为 `A`、 `B` 和 `C`。 每个文件系统有一个根目录， 而各自含有两个其他的目录， 名为 `A1`, `A2` ( `B1`, `B2` 和 `C1`, `C2`)。

看看 `A` 这个根文件系统。 假如您用 `ls` 命令来查看这个目录您会见到两个子目录: `A1` 和 `A2`。 这个目录树是这个样子:

![](img/example-dir1.png)

一个文件系统必须挂到另一个文件系统的某一目录， 所以现在假设把 `B` 文件系统挂到 `A1`目录， 那 `B` 根目录因此代替 了 `A1`，而显示出 `B` 目录(的内容)：

![](img/example-dir2.png)

无论`B1` 或 `B2` 目录在那里而延伸出来的路径必须为 `/A1/B1` 或 `/A1/B2`。 而在 `/A1` 里原有的文件会临时隐藏。 想这些文件再出现把 `B` 从 A *挂接释放*。

所有在`B1` 或 `B2` 目录里的文件都可以通过 `/A1/B1` 或 `/A1/B2` 访问。而在 `/A1` 中原有的文件会被临时隐藏，直到 `B` 从 A 上被*卸载* (unmout) 为止。

把 `B` 挂接在 `A2` 那图表的样子就是这样子:

![](img/example-dir3.png)

这个路径分别是 `/A2/B1` 和 `/A2/B2` 。

文件系统能把顶部挂接在另一个文件系统上。 继续这个例子， 把 `C` 文件系统挂接在 `B` 文件系统里的 `B1` 目录， 排列如下:

![](img/example-dir4.png)

或者把 `C` 文件系统挂接在 `A` 文件系统里的`A1`目录：

![](img/example-dir5.png)

假如您熟悉 MS-DOS® 并知道 `join` 命令， 尽管不相同，其实功能是相似的。

这方面不是普通知识而且涉及到您自己所关心的， 当您安装 FreeBSD 并在以后添加新磁盘时， 您必须知到该如何新建文件系统和挂接上。

(FreeBSD 系统)它有一个主要的根文件系统， 不需要另外新建立， 但当需要手工处理时，这是一个有用的知识。

多个文件系统的益处

*   不同的文件系统可用不同的 *挂接参数*。 举些例子， 仔细想一下， 根文件系统能用只读的方式挂接上， 防止不经意删除或编辑到一个危险的文件。 把各用户能写入的文件系统分开， 像`/home`这样， 由另外的文件系统分别用 *nosuid* 参数挂接，这个参数防止 *suid*/*guid* 在执行这个文件系统中的文件时生效， 从而缓解了一些安全问题。

*   FreeBSD 能根据一个文件系统使用的情况自动优化 这个文件系统上的文件布局。 所以对一个存储了大量小文件并会被频繁写入文件系统的优化与一个存储了少量大文件的优化是不同的。 而在一个大的单一文件系统上则无法体现这样的优化。

*   FreeBSD 的文件系统能够在断电时尽可能避免损失。 然而， 在关键点时的电源失效仍然可能会破坏文件系统的结构。 将您的文件系统分成多个有助于分散风险， 并方便备份和恢复。

单一文件系统的益处

*   文件系统是固定大小的。 当安装 FreeBSD 时新建一个文件系统并设定一个大小， 您会在稍后发觉到必须去建一个大的分区。 如果配置不当， 则需要备份、 重新创建文件系统， 然后再恢复数据。

    ### 重要:

    FreeBSD 提供了 [growfs(8)](http://www.FreeBSD.org/cgi/man.cgi?query=growfs&sektion=8&manpath=freebsd-release-ports) 命令。 这使得能够实时地调整文件系统的大小， 因而不再受其限制。

文件系统是和分区一一对应的。 这里的分区和常用的术语分区 (例如， MS-DOS® 分区) 的意思并不一样， 这是由于 FreeBSD 的 UNIX® 传统造成的。 每一个分区使用一个从 `a` 到 `h` 的字母来表示。 每个分区只能包含一个文件系统， 这意味着文件系统通常可以由它们在文件系统目录结构中的挂接点， 或对应的分区字母来表示。

FreeBSD 的 *交换分区* 也需要使用磁盘空间。 交换分区是给 FreeBSD 作 *虚拟内存* 使用的， 这样能令您的计算机有更多的内存可使用， 当 FreeBSD 在运行而内存不够的时候， 它会把其他一些可转移的数据转移到交换分区， 空出内存的位置以供使用。

某些 partitions 的用途是确定的。

| 分区 | 约定 |
| --- | --- |
| `a` | 通常指定为根文件系统 |
| `b` | 通常指定为交换分区 |
| `c` | 通常它和所在的 slice 大小相同。 `c` 分区上工作时必定会影响到事整个 slice (举个例子，坏块扫描器)。 您通常不愿意在这个 partition 建立文件系统。 |
| `d` | 分区 `d` 曾经有特殊的含义， 不过这种意义在现时的系统上已不再适用， 因此 `d` 可以和任何其它普通的分区一样使用了。 |

每一个包含了文件系统的分区被保存在 FreeBSD 称为 *slice* 的部分上。 Slice 是一个 FreeBSD 术语， 通常被叫做分区， 再次强调， 这是由于 FreeBSD 的 UNIX® 背景。 Slices 有其编号， 从 1 到 4。

Slice 编号在设备名后面， 并有一个 `s` 前缀， 从 1 开始。 因此 “da0*s1*” 是第一个 SCSI 驱动器的第一个 slice。 每个磁盘上只能有四个物理的 slices， 但您可以在物理 slice 中使用适当的类型来创建逻辑 slice。 这些扩展 slice 编号从 5 开始， 因此 “ad0*s5*” 是第一个 IDE 磁盘中的第一个 扩展 slice。 文件系统所使用的设备应该占满 slice。

Slices, “专用指定” 物理驱动器， 和其他驱动器都包含 *partitions*， 那几个的 partitions 都是用字母从 `a` 到 `h` 来标定的， 而这些字母都在驱动器名字之后，所以 “da0*a*” 是指首个 da 设备的 a partition， 而那个就是 “专项指定”。 “ad1s3*e*” 是指 IDE 磁盘上第三个 slice 的第五个 partition。

最终，每个磁盘都被系统识别。 一个磁盘名字是用磁盘类型代码和编号来标识的， 它不像 slices，磁盘的编号是由 0 开始的。 对应代码请看这里所列出的表 4.1 “磁盘设备的代码”。

当在 FreeBSD 中指定 partition 名字时， 必须同时包含这个分区的 slice 和磁盘的名字； 类似地， 在指定 slice 时， 也应该给出包含该 slice 的磁盘名字。 可这样列出： 磁盘名称，`s`，slice 编号，和 partition 标定字母。 例子请看 例 4.1 “样例磁盘, Slice, 和 Partition 它们的命名”。

例 4.2 “一个磁盘的布局” 这里显示了一个磁盘的布局，有更清楚的帮助。

在安装 FreeBSD 时，您首先要配置好磁盘 slices， 然后在 FreeBSD 使用的 slice 上建立 partitions。 并在每个 partition 上建立一个文件系统(或交换分区)， 和指定文件系统的挂接位置。

表 4.1. 磁盘设备的代码

| 代码 | 说明 |
| --- | --- |
| `ad` | ATAPI (IDE) 磁盘 |
| `da` | SCSI 直接存取磁盘 |
| `acd` | ATAPI (IDE) 光驱 |
| `cd` | SCSI 光驱 |
| `fd` | 软驱 |

例 4.1. 样例磁盘, Slice, 和 Partition 它们的命名

| 命名 | 说明 |
| --- | --- |
| `ad0s1a` | 在首个 IDE 磁盘(`ad0`)上的 第一个 slice (`s1`)里的 第一个 partition (`a`)。 |
| `da1s2e` | 在第二个 SCSI 磁盘(`da1`)上的 第二个 slice(`s2`)里的 第五个 partition(`e`)。 |

例 4.2. 一个磁盘的布局

从在系统里的首个 IDE 磁盘图表可以显示出 FreeBSD 的见解。 假设磁盘大小为 4 GB，它里面包含了两个 2 GB 大小的 slices (但在 MS-DOS®叫 partitions)。 首个 slice 是一个 MS-DOS®磁盘叫`C:`， 而第二个 slice 是 FreeBSD 配置好的 slice。 FreeBSD 配置好的 slice 有三个 partitions 和另一个交换分区。

这三个 partitions 各自控制一个文件系统。 partition`a` 用于根文件系统， partition`e` 用于 `/var` 目录层， partition`f` 用于 `/usr` 目录层。

![](img/disk-layout.png)

## 4.6. 文件系统的挂接和卸下

这种文件系统就像一棵树那样用`/`确立根部， 是比较理想的文件系统。 而`/dev`、 `/usr` 和其他目录就是根目录的分枝， 另外这些目录可以再分枝，例如`/usr/local`。

应该考虑给某些目录一些空间从而分散文件系统。 `/var` 之下包含目录 `log/`，目录`spool/`， 和不同类型的临时文件，很可能把它塞满。 把什么都塞进根文件系统不是一个好主意， 好的做法是应该把 `/var` 从 `/`分离出去。

另一个要考虑的是，给物理设备或虚拟磁盘这些自带空间的文件系统确定目录结构树。 例如 网络文件系统 或光驱的挂接。

### 4.6.1. `fstab` 文件

在 引导过程 期间， 自动挂上`/etc/fstab`所列出的文件系统。 (除非他们注明为`noauto` 选项)。

`/etc/fstab` 文件包含的各行的列表格式如下:

```
*`device`*       *`/mount-point`* *`fstype`*     *`options`*      *`dumpfreq`*     *`passno`*
```

`device`

设备名称(设备必须存在)， 说明在 第 19.2 节 “设备命名”.

`mount-point`

目录 (目录必须存在)， 用在那个挂接上的文件系统上。

`fstype`

文件系统类型，请通过[mount(8)](http://www.FreeBSD.org/cgi/man.cgi?query=mount&sektion=8&manpath=freebsd-release-ports)查阅。 默认的 FreeBSD 文件系统类型是`ufs`。

`options`

设为可读写文件系统的`rw`选项， 或设为只读文件系统的`ro`选项， 或其他一些选项，可随意选一个。 一个常用的选项 `noauto` 用在不需在引导过程期间挂接的文件系统。 其他的选项在 [mount(8)](http://www.FreeBSD.org/cgi/man.cgi?query=mount&sektion=8&manpath=freebsd-release-ports) 手册里列出。

`dumpfreq`

[dump(8)](http://www.FreeBSD.org/cgi/man.cgi?query=dump&sektion=8&manpath=freebsd-release-ports) 使用这项去决定那个文件系统必须移贮。 假如缺少这项，默认的数值为 0。

`passno`

这一项决定文件系统的检查顺序， 文件系统想跳过检查应将`passno`设为 0。 根文件系统(那个是在每方面开始之前必须检查的) 应该将它的 `passno` 设为 1， 其他文件系统的 `passno` 必须把数值设到大于 1。假如多个文件系统的`passno`的值相同， 那么 [fsck(8)](http://www.FreeBSD.org/cgi/man.cgi?query=fsck&sektion=8&manpath=freebsd-release-ports) 在允许的情况下将尝试并行地去检查文件系统。

请参阅 [fstab(5)](http://www.FreeBSD.org/cgi/man.cgi?query=fstab&sektion=5&manpath=freebsd-release-ports) 联机手册， 以获得关于 `/etc/fstab` 文件格式， 以及其中所包含的选项的进一步信息。

### 4.6.2.  `mount` 命令

这个 [mount(8)](http://www.FreeBSD.org/cgi/man.cgi?query=mount&sektion=8&manpath=freebsd-release-ports) 命令是挂接文件系统的基本运用。

使用最多的基本格式:

```
# `mount device mountpoint`
```

它的选项非常多，而[mount(8)](http://www.FreeBSD.org/cgi/man.cgi?query=mount&sektion=8&manpath=freebsd-release-ports) 手册同样提及， 但常用的都在这里:

挂接的各种选项

`-a`

挂接`/etc/fstab`里所有列出的文件系统。 除非标记为 “noauto” 或作了排除在外的 `-t` 类型标记，或者在这之前已挂上。

`-d`

除了实际上系统调用以外，可以完成任何事情，这个选项是和 `-v`参数一起连在一块使用，可以决定[mount(8)](http://www.FreeBSD.org/cgi/man.cgi?query=mount&sektion=8&manpath=freebsd-release-ports)所做的事情。

`-f`

强制去挂接一个未知的文件系统(会有危险)， 或当把一个文件系统挂接状态由可读写降为只读时，强制撤消可写通道。

`-r`

以只读方式挂接文件系统。 这和在指定了 `-o` 选项配合 `ro` 参数的效果是一样的。

`-t` *`fstype`*

根据给出的文件系统类型挂接文件系统， 假如给于`-a`选项，仅挂接这个类型的文件系统。

“ufs” 是默认的文件系统类型。

`-u`

在文件系统上修改挂接选项。

`-v`

版本模式。

`-w`

以可读写方式挂接文件系统。

The `-o` 选项采用一个逗号分开以下多个选项:

noexec

不允许文件系统上的二进制程序执行。这也是一个有用的安全选项。

nosuid

不允许文件系统上的 setuid 或 setgid 标记生效。这也是一个有用的安全选项。

### 4.6.3.  `umount` 命令

[umount(8)](http://www.FreeBSD.org/cgi/man.cgi?query=umount&sektion=8&manpath=freebsd-release-ports) 命令同样采用一个参数、一个挂接点、一个设备名。 或采用`-a`选项，又或采用`-A`选项。

所有格式都可采用 `-f` 去强行卸下， 或采用`-v` 用那适当的版本。 但警告，采用 `-f`并不是一个好主意， 强行卸下文件系统可能损坏计算机或破坏文件系统上的数据。

`-a` 和 `-A` 会卸下所有已挂接的文件系， 可能通过`-t`后面列出的文件系统进行修改， 但无论如何，`-A`都不会尝试去卸下根文件系统。

## 4.7. 进程

FreeBSD 是一个多任务操作系统。 这就意味着好像一次可以运行一个以上的程序。 每个占用一定时间运行的程序就叫 *进程* (process)。 你运行的每一个命令会至少启动一个新进程，还有很多一直运行着的系统进程， 用以维持系统的正常运作。

每个进程用来标识的一个编号就叫 *进程 ID*， 或叫 *PID*。 而且，就像文件那样，每个进程也有所属用户和所属群体。 所属用户和所属群体使用在这方面:确定这个进程可以打开那些文件和那些设备， 从而在初期使用文件的权限。 多数的进程都有一个父进程， 而进程是依靠父进程来启动的。 例如，假如您把命令输入到 shell 里那 shell 是一个进程，而您运行的各个命令同样是进程， 那么，shell 就是您各个运行进程的父进程。 而这方面有一个例外的进程就叫[init(8)](http://www.FreeBSD.org/cgi/man.cgi?query=init&sektion=8&manpath=freebsd-release-ports)。 `init`始终是首个进程,，所以他的 PID 始终是 1， 而`init`在 FreeBSD 起动时由内核自动启动。

在系统上，有两个命令对进程观察非常有用:[ps(1)](http://www.FreeBSD.org/cgi/man.cgi?query=ps&sektion=1&manpath=freebsd-release-ports) 和 [top(1)](http://www.FreeBSD.org/cgi/man.cgi?query=top&sektion=1&manpath=freebsd-release-ports)。 这个`ps`命令作用是观察当前运行进程的状态， 显示他们的 PID，使用了多少内存，它们启动的命令行。 而`top`命令则是显示所有运行进程，并在以秒计的短时内更新数据。 您能交互式的观察您计算机的工作。

默认情况下， `ps`仅显示出您自己所运行的命令。 例如:

```
% `ps`
  PID  TT  STAT      TIME COMMAND
  298  p0  Ss     0:01.10 tcsh
 7078  p0  S      2:40.88 xemacs mdoc.xsl (xemacs-21.1.14)
37393  p0  I      0:03.11 xemacs freebsd.dsl (xemacs-21.1.14)
48630  p0  S      2:50.89 /usr/local/lib/netscape-linux/navigator-linux-4.77.bi
48730  p0  IW     0:00.00 (dns helper) (navigator-linux-)
72210  p0  R+     0:00.00 ps
  390  p1  Is     0:01.14 tcsh
 7059  p2  Is+    1:36.18 /usr/local/bin/mutt -y
 6688  p3  IWs    0:00.00 tcsh
10735  p4  IWs    0:00.00 tcsh
20256  p5  IWs    0:00.00 tcsh
  262  v0  IWs    0:00.00 -tcsh (tcsh)
  270  v0  IW+    0:00.00 /bin/sh /usr/X11R6/bin/startx -- -bpp 16
  280  v0  IW+    0:00.00 xinit /home/nik/.xinitrc -- -bpp 16
  284  v0  IW     0:00.00 /bin/sh /home/nik/.xinitrc
  285  v0  S      0:38.45 /usr/X11R6/bin/sawfish
```

在这个例子里您可看到，从 [ps(1)](http://www.FreeBSD.org/cgi/man.cgi?query=ps&sektion=1&manpath=freebsd-release-ports) 输出的每一列是有规律的。 `PID` 就是进程 ID，这个较早前已讨论过了。 PID 号的分配由 1 一直上升直到 99999， 当您运行到超过限制时，这些编号会回转分配 (仍在使用中的 PID 不会分配给其他进程)。 `TT`这一列显示了程序运行所在的终端， 目前可以安全地忽略。 `STAT` 显示程序的状态，也可以安全地被忽略。 `TIME`是程序在 CPU 处理时间──运行的时间量， 并不是指您程序启动到现在的所用的时间。 许多程序碰巧遇到某方面在他们之前要花费大量 CPU 处理时间时，他们就必须等候。 最后， `COMMAND` 是运行程序时使所用的命令行。

[ps(1)](http://www.FreeBSD.org/cgi/man.cgi?query=ps&sektion=1&manpath=freebsd-release-ports)支持使用各种选项去改变显示出来的内容， 最有用的一个就是`auxww`。 `a`选项显示出所有运行进程的内容， 而不仅仅是您的进程。 `u`选项显示出进程所归属的用户名字以及内存使用， `x` 选项显示出后台进程。 而 `ww` 选项表示为 [ps(1)](http://www.FreeBSD.org/cgi/man.cgi?query=ps&sektion=1&manpath=freebsd-release-ports) 把每个进程的整个命令行全部显示完， 而不是由于命令行过长就把它从屏幕上截去。

下面和从[top(1)](http://www.FreeBSD.org/cgi/man.cgi?query=top&sektion=1&manpath=freebsd-release-ports)输出是类似的，一个示例式对话就象这样子:

```
% `top`
last pid: 72257;  load averages:  0.13,  0.09,  0.03    up 0+13:38:33  22:39:10
47 processes:  1 running, 46 sleeping
CPU states: 12.6% user,  0.0% nice,  7.8% system,  0.0% interrupt, 79.7% idle
Mem: 36M Active, 5256K Inact, 13M Wired, 6312K Cache, 15M Buf, 408K Free
Swap: 256M Total, 38M Used, 217M Free, 15% Inuse

  PID USERNAME PRI NICE  SIZE    RES STATE    TIME   WCPU    CPU COMMAND
72257 nik       28   0  1960K  1044K RUN      0:00 14.86%  1.42% top
 7078 nik        2   0 15280K 10960K select   2:54  0.88%  0.88% xemacs-21.1.14
  281 nik        2   0 18636K  7112K select   5:36  0.73%  0.73% XF86_SVGA
  296 nik        2   0  3240K  1644K select   0:12  0.05%  0.05% xterm
48630 nik        2   0 29816K  9148K select   3:18  0.00%  0.00% navigator-linu
  175 root       2   0   924K   252K select   1:41  0.00%  0.00% syslogd
 7059 nik        2   0  7260K  4644K poll     1:38  0.00%  0.00% mutt
...
```

这个输出分成两部份。 前面部份(起始前五行) 显示了:运行于最后进程的 PID、 系统负载均衡 (那个是指系统繁忙时的调节方式)、 系统正常运行时间 ( 指从启动算起所用的时间) 和当前时间。 前面部份另外的图表 涉及:多少进程在运行(这个情况是 47)， 多少内存和多少交换分区在使用， 和在不同 CPU 状态里系统消耗多少时间。

在那下面一连串的纵列和从[ps(1)](http://www.FreeBSD.org/cgi/man.cgi?query=ps&sektion=1&manpath=freebsd-release-ports)输出的的内存是相似的。 如以前[ps(1)](http://www.FreeBSD.org/cgi/man.cgi?query=ps&sektion=1&manpath=freebsd-release-ports)一样，您能见到:PID、用户名、CPU 处理时间合计、运行的命令。 [top(1)](http://www.FreeBSD.org/cgi/man.cgi?query=top&sektion=1&manpath=freebsd-release-ports)默认是显示您的进程所用内存空间的合计。 内存空间这里分成两列，一列为总体大小，另一列是必须请求驻留大小是多少内存──总体大小。 而驻留大小实际上是瞬间使用的多少。 在以上那个例子，您会看到那 Netscape®总计需要 30 MB 内存， 但实际只用了 9 MB。

[top(1)](http://www.FreeBSD.org/cgi/man.cgi?query=top&sektion=1&manpath=freebsd-release-ports) 每两秒自动刷新一次，您可以用`s`改变刷新的秒数。

## 4.8. 守护进程，信号和杀死进程

当您运行一个编辑器时它是很容易控制的，告诉它去加载文件它就加载。 您之所以能这样做，是因为编辑器提供这样便利去这样做，和因为有编辑器去附上的*终端*。 一些程序在运行中不需要连续的用户输入，一有机会就从终端里分离到后台去。 例如，一个 web 系统整天都在作 web 请求的响应，他不需要您输入任何东西就能完成， 这个类别的另一个例子就是把 email 的传送。

我们把那些程序叫 *守护进程*。 守护神是希腊神话中的一些人物，非正非邪，他们是些守护小精灵， 大体上为人类作出贡献。 许多类似 web 服务或 mail 服务的系统对于今天仍有用途， 这就是为什么在那么长的时间里，BSD 的吉祥物保持为一双鞋加一把钢叉的守护神模样。

守护进程的程序命名通常在最后加一个 “d”。 BIND 是伯克利互联网域名服务 (而实际执行的程序名称则是 `named`)， Apache web 系统的程序就叫 `httpd`， 在行式打印机上的打印守护进程就是 `lpd`。 这只是一种惯例，不是标准或硬性规定。 例如，为 Sendmail 而应用的主要 mail 守护进程就叫`sendmail`， 却不叫`maild`，这和您推测的一样。

有时可能会需要与守护进程进行通讯。 而 *信号* 则是其中的一种通讯机制。 可以发送信号给守护进程 (或相关的另一些进程) 来与它进行通信， 不同的信号都有自己的数字编号──其中一些有特殊的含义， 其它的则可以被应用程序自己进行解释， 而一般来说， 应用程序的文档会告诉哪些信号会被如何处理。 您只能给所属于您的进程发信号，假如您给其他人的进程发信号， 进程就会用[kill(1)](http://www.FreeBSD.org/cgi/man.cgi?query=kill&sektion=1&manpath=freebsd-release-ports) 或 [kill(2)](http://www.FreeBSD.org/cgi/man.cgi?query=kill&sektion=2&manpath=freebsd-release-ports)权限进行拒绝。 当然,`root` 用户会例外，它能把各种信号发送给每个进程。

在某些情况下，FreeBSD 也会向应用软件发送信号。 假如一个应用软件含有恶意写入并试图去访问内存，那是不可想象的，FreeBSD 会向那个进程发送 *段式违规* 信号 (`SIGSEGV`)。 假如一个应用软件使用[alarm(3)](http://www.FreeBSD.org/cgi/man.cgi?query=alarm&sektion=3&manpath=freebsd-release-ports)系统去进行周期性调用闹钟功能，每当达到时间时， FreeBSD 会向应用软件发送闹钟信号(`SIGALRM`)。

有两个信号可以停止进程:`SIGTERM` 和 `SIGKILL`。 `SIGTERM`比较友好，进程能*捕捉*这个信号， 根据您的需要来关闭程序。在关闭程序之前，您可以结束打开的记录文件和完成正在做的任务。 在某些情况下， 假如进程正在进行作业而且不能中断，那么进程可以忽略这个 `SIGTERM`信号。

对于`SIGKILL`信号，进程是不能忽略的。 这是一个 '“我不管您在做什么,立刻停止”'的信号。 假如您发送`SIGKILL`信号给进程， FreeBSD 就将进程停止在那里。[^([4])](#ftn.idp71652336).

您可能会去使用 `SIGHUP`、 `SIGUSR1` 和 `SIGUSR2`信号。 这都是些通用的信号，各种应用程序都可以应用 在各方面的信号发送。

假如您改变了 web 系统的配置文件──并想 web 系统去重读它的配置， 您可以停止然后再启动`httpd`。但这样做 web 系统会导致一个短暂 的中断周期，那样是不受欢迎的。几乎所有的守护进程在编写时，都会指定对`SIGHUP` 信号进行响应从而重读配置文件。 所以， 最好的方法， 就不是杀死并重启 `httpd`， 而是发一个 `SIGHUP` 信号给它。 因为在这方面没有一个标准，不同的守护进程有不同的用法，所以不了解时应读一下守护进程的文档。

发送信号可用[kill(1)](http://www.FreeBSD.org/cgi/man.cgi?query=kill&sektion=1&manpath=freebsd-release-ports) 命令， 请参考[kill(1)](http://www.FreeBSD.org/cgi/man.cgi?query=kill&sektion=1&manpath=freebsd-release-ports)所列出的例子。

过程 4.1. 发送一个信号给进程

这个例子显示了怎样去发一个信号给[inetd(8)](http://www.FreeBSD.org/cgi/man.cgi?query=inetd&sektion=8&manpath=freebsd-release-ports)。 `inetd`配置文件是`/etc/inetd.conf`， 如果想`inetd` 去重读文件系统的话，可以给它发一个`SIGHUP` 信号。

1.  寻找您要发送信号的进程 ID，可以用[ps(1)](http://www.FreeBSD.org/cgi/man.cgi?query=ps&sektion=1&manpath=freebsd-release-ports) 加 [grep(1)](http://www.FreeBSD.org/cgi/man.cgi?query=grep&sektion=1&manpath=freebsd-release-ports)来完成。 [grep(1)](http://www.FreeBSD.org/cgi/man.cgi?query=grep&sektion=1&manpath=freebsd-release-ports)命令被用在搜索输出方面，搜索您指定的字符串。 这命令是由普通用户来执行的，而[inetd(8)](http://www.FreeBSD.org/cgi/man.cgi?query=inetd&sektion=8&manpath=freebsd-release-ports)是`root`用户运行的， 所以必须给[ps(1)](http://www.FreeBSD.org/cgi/man.cgi?query=ps&sektion=1&manpath=freebsd-release-ports)带上`ax`选项。

    ```
    % `ps -ax | grep inetd`
      198  ??  IWs    0:00.00 inetd -wW
    ```

    得出 [inetd(8)](http://www.FreeBSD.org/cgi/man.cgi?query=inetd&sektion=8&manpath=freebsd-release-ports) PID 号是 198。 有时 `grep inetd` 命令也出现在输出中， 这是因为在这方面 [ps(1)](http://www.FreeBSD.org/cgi/man.cgi?query=ps&sektion=1&manpath=freebsd-release-ports) 也是寻找列表中运行进程。

2.  使用 [kill(1)](http://www.FreeBSD.org/cgi/man.cgi?query=kill&sektion=1&manpath=freebsd-release-ports) 去发送信号。 因为 [inetd(8)](http://www.FreeBSD.org/cgi/man.cgi?query=inetd&sektion=8&manpath=freebsd-release-ports) 是由 `root`启动的， 您必须使用 [su(1)](http://www.FreeBSD.org/cgi/man.cgi?query=su&sektion=1&manpath=freebsd-release-ports) 去 变为 `root` 用户。

    ```
    % `su`
    Password:
    # `/bin/kill -s HUP 198`
    ```

    和大多数 UNIX® 命令一样， [kill(1)](http://www.FreeBSD.org/cgi/man.cgi?query=kill&sektion=1&manpath=freebsd-release-ports) 如果完成了任务, 就不会给出任何消息。 假如您发送信号给一个不属于您的进程， 您会看到 kill: *`PID`*: Operation not permitted. 假如输错了 PID 号，把信号发送到其他进程，那是坏事。 或者您侥幸，把信号发送到不存在的进程， 您会看见 kill: *`PID`*: No such process.

    ### 为什么使用 `/bin/kill`?:

    许多 shell 提供了内建 `kill` 命令， 这样， shell 就能直接发送信号，而不是运行 `/bin/kill`。 这点非常有用， 但不同 shell 有不同的语法来指定发送信号的名字， 与其试图把它们学完倒不如简单地直接使用 `/bin/kill ...`。

发送其他的信号也很相似， 只要在命令行替换 `TERM` 或 `KILL` 就行了。

### 重要:

在系统上随意杀死进程是个坏主意，特别是[init(8)](http://www.FreeBSD.org/cgi/man.cgi?query=init&sektion=8&manpath=freebsd-release-ports)， 它的进程 ID 是 1，它非常特殊。可以运行 `/bin/kill -s KILL 1` 命令来让系统迅速关机。 当您按下 **Return** （回车）键之前， *一定要* 详细检查您运行 [kill(1)](http://www.FreeBSD.org/cgi/man.cgi?query=kill&sektion=1&manpath=freebsd-release-ports) 时所指定的参数。

* * *

[^([4])](#idp71652336) 有点不正确──少数的东西是不能中断的。 例如， 假如进程试图读取网络上另一计算机上的文件， 而那个的计算机会因为某些原因拿走了这个文件， 那这个进程从上述情况来看是 “不能中断”。 最终这个进程会超时，典型的两分钟。一出现超时进程将被杀死。

## 4.9. Shells

在 FreeBSD 里，每日有一大堆工作是在命令行的界面完成的,那就叫做 shell。 一个 shell 的主要功能就是从输入取得命令然后去执行他。 许多的 shell 同样能帮我们完成内建的每日功能，例如:文件管理、文件寻找、命令行编辑、 宏指令和环境变量。FreeBSD 内含了一些 shell，例如:`sh`、Bourne Shell、 `tcsh`和改良过的 C-shell。 另外也有些 shell 也可在 FreeBSD 的 Ports 得到，例如:`zsh`和`bash`。

您想使用哪一种 shell 取决于您的喜好， 假如您是 C 程序设计师，您可能选择一个 C-like shell 例如`tcsh`。 假如您是从 Linux 过来的或是一个命令行的新手，您可能会试一下`bash`。 这一点告诉我们每一个 shell 都有各自的特性，可能适用于您的工作环境，也可能不适用于您的工作环境。

每个 shell 都有一个共通点就是文件名补全。 输入命令或文件名的前几个字，然后按**Tab**键，就能靠 shell 的自动补全功能得出 命令或文件名。这里有一个例子，假设您有两个文件叫 `foobar` 和`foo.bar`，而您想删除 `foo.bar`， 可这样在键盘上输入 `rm fo[Tab].[Tab]`。

那么 shell 就会输出 `rm foo[BEEP].bar`。

这个[BEEP] 是这控制台铃声， 那个是告诉我们它不能完成文件名补全，因为有多个文件名符合。 `foobar` 和 `foo.bar` 都是以 `fo`开头， 它只可以补全到 `foo`。 输入 `.`并再按一次 **Tab**，shell 才把其余的文件名全部显示出来。

另一个特点就是 shell 利用环境变量运行。环境变量是贮存在 shell 环境空间上相对应的键和可变值， 这个空间能够补程序从 shell 里读出，而且包含了许多程序的配置。 这个一个常用环境变量列和其含义的列表：

| 变量 | 说明 |
| --- | --- |
| `USER` | 当前登录进入的用户名。 |
| `PATH` | 搜索程序路径，以两点的冒号分隔开。 |
| `DISPLAY` | 假如有这个变量的话，就是 X11 显示器的网络名称。 |
| `SHELL` | 当前所用的 shell。 |
| `TERM` | 用户终端的名字，通常用在确定终端的能力。 |
| `TERMCAP` | 各种终端功能所用终端分离编码的基本数据项目。 |
| `OSTYPE` | 操作系统类型，默认是 FreeBSD。 |
| `MACHTYPE` | 是指系统上运行的 CPU 体系结构。 |
| `EDITOR` | 用户首选的文本编辑器。 |
| `PAGER` | 用户首选的文本页面调度程序 。 |
| `MANPATH` | 搜索联机手册路径，以两点的冒号分隔开。 |

不同的 shell 设置环境变量也不相同。举个例子， 在如`tcsh` 和 `csh`这样的 C-Style shell， 您必须使用`setenv`去设置环境变量。 而在如`sh`和`bash`这样的 Bourne shell， 您必须使用`export`去设置当前环境变量。 再举个例子，要去设置或改变`EDITOR`环境变量， 在`csh`或`tcsh`下将`EDITOR`设为 `/usr/local/bin/emacs`:

```
% `setenv EDITOR /usr/local/bin/emacs`
```

而在 Bourne shell 下，则是:

```
% `export EDITOR="/usr/local/bin/emacs"`
```

您也可以在命令行上加一个`$`字符在变量之前从而取得环境变量。 举个例子，用`echo $TERM` 就会显示出`$TERM`的设定值， 其实就是 shell 取得`$TERM`并传给`echo`来显示的。

shell 里有许多特别的字符代表着特别的资料，我们把叫做 meta-characters。 最常用的就是`*`字符，它可代表文件名的任何字符。 这些特别字符应用到文件名全域方面。假如，输入 `echo *`和输入 `ls`的效果是相同的，其实就是 shell 取得了全部符合 `*`的文件名，并传给 `echo` 在命令行下显示出来。

为了防止 shell 去分析这些特别字符， 我们可在它之前加一个 `\`字符去说明它只是普通字符。 `echo $TERM`就会显示出您的终端情况， 而 `echo \$TERM` 就会显示出 `$TERM` 这几个字。

### 4.9.1. 改变您用的 Shell

改变您的 Shell 的最简单方法是使用 `chsh` 命令。 执行 `chsh` 将根据您设定的`EDITOR` 环境变量进入到那个编辑器，假如没有设定，就会进入`vi`编辑器。 请改变“Shell:”这行对应值。

您可使用`chsh` 的`-s`选项， 这样就能设置您的 shell 却又不用编辑器。假如您想把 shell 改为`bash` 可用下面的技巧。

```
% `chsh -s /usr/local/bin/bash`
```

### 注意:

您使用的 shells*必须* 在`/etc/shells` 文件里列出。 假如您从 ports 里装一个 shell， 那就不用做这步了。 假如您手工装一个 shell，那就要手工添加进去。

举个例了子，假如您手工把 `bash`装到 `/usr/local/bin`里，您还要进行这一步:

```
# `echo "/usr/local/bin/bash" >> /etc/shells`
```

然后运行`chsh`。

## 4.10. 文本编辑器

FreeBSD 的很多配置都可以通过编辑文本文件来完成。 因此， 最好能熟悉某种文本编辑器。 FreeBSD 基本系统中提供了一些， 您也可以从 Ports Collection 安装其它编辑器。

最容易学的而又简单的编辑器是 ee 编辑器， 是个标准的简易编辑器。 要启动 ee，首先就要在命令行输入 `ee filename`， *`filename`* 是一个要编辑的文件名。 例如，要编辑 `/etc/rc.conf`就要输入 `ee /etc/rc.conf`，在 `ee`的控制内， 编辑器所有功能的操作方法都显示在最上方。 这个`^` 字符代表 键盘上的**Ctrl** 键， 所以`^e` 就是 **Ctrl**+**e**组合键。 假如想离开 ee， 按**Esc**键，就可选择离开编辑器。 当您修改了内容的时候，编辑器会提示您保存。

FreeBSD 本身也带许可多有强大功能的文本编辑器， 例如 vi。还有其他在 FreeBSD Ports 里几种， 像 emacs 和 vim。 这些编辑器有着强大的功能，但同时学习起来比较复杂。 不管怎样，假如您从事文字编辑方面的工作， 学习如 vim 或 emacs 这些有强大功能的编辑器用法， 在长时间工作里会帮您节省不少的时间。

很多需要修改文件或打字输入的应用程序都会自动打开一个文本编辑器。 更改默认使用的编辑器， 请设置 `EDITOR` 环境变量。 参阅 shells 以获取更多详细信息。

## 4.11. 设备和设备节点

在一个系统里，硬件描述通常用法就是一个设备对应一个术语，包括磁盘、打印机、显卡和键盘。 当 FreeBSD 启动过程中，大多数的设备都能探测到并显示出来， 您也可以查阅`/var/run/dmesg.boot`， 引导时所有信息都在里面。

例如， `acd0` 就是 首个 IDE 光盘设备， 而 `kbd0` 则代表键盘。

在 UNIX®操作系统里，大多数设备存在的特殊访问文件就是叫做设备节点， 他们都定位在`/dev`目录里。

### 4.11.1. 建立设备节点

当在系统中添加新设备或将附加设备的支持编译进内核之后， 都必须为其建立设备节点。

#### 4.11.1.1. `DEVFS` (DEVice 文件系统)

这个设备文件系统， 或叫 `DEVFS`， 为内核的设备命名在整体文件系统命名里提供通道， 并不是建立或更改设备节点， `DEVFS`只是为您的特别文件系统进行维护。

请参见 [devfs(5)](http://www.FreeBSD.org/cgi/man.cgi?query=devfs&sektion=5&manpath=freebsd-release-ports) 联机手册以了解更多细节。

## 4.12. 二进制文件格式

要理解为什么 FreeBSD 使用 [elf(5)](http://www.FreeBSD.org/cgi/man.cgi?query=elf&sektion=5&manpath=freebsd-release-ports) 格式， 您必须首先了解一些 UNIX® 系统中的 三种 “主要” 可执行文件格式的有关知识：

*   [a.out(5)](http://www.FreeBSD.org/cgi/man.cgi?query=a.out&sektion=5&manpath=freebsd-release-ports)

    是最古老和“经典的” UNIX® 目标文件格式， 这种格式在其文件的开始处有一个短小而又紧凑的首部， 该首部带有一个魔幻数字，用来标识具体的格式(更多详情参见[a.out(5)](http://www.FreeBSD.org/cgi/man.cgi?query=a.out&sektion=5&manpath=freebsd-release-ports))。 这种格式包含 3 个要装载入内存的段：.text， .data， 和 .bss，以及 一个符号表和一个字符串表。

*   COFF

    SVR3 目标文件格式。其文件头现在包括一个区段表(section table)， 因此除了.text，.data，和.bss 区段以外，您还可以包含其它的区段。

*   [elf(5)](http://www.FreeBSD.org/cgi/man.cgi?query=elf&sektion=5&manpath=freebsd-release-ports)

    COFF 的后继， 其特点是可以有多个区段， 并可以使用 32 位或 64 位的值。 它有一个主要的缺点： ELF 在其设计时假设每个系统体系结构只有一种 ABI。 这种假设事实上相当错误， 甚至在商业化的 SYSV 世界中都是错误的 (它们至少有三种 ABI: SVR4, Solaris, SCO)。

    FreeBSD 试图在某种程度上解决这个问题，它提供一个工具，可以 对一个已知的 ELF 可执行文件 *标识*它所遵从的 ABI 的信息。 更多这方面的知识可以参见手册页[brandelf(1)](http://www.FreeBSD.org/cgi/man.cgi?query=brandelf&sektion=1&manpath=freebsd-release-ports)

FreeBSD 从“经典”阵营中来，因此使用了[a.out(5)](http://www.FreeBSD.org/cgi/man.cgi?query=a.out&sektion=5&manpath=freebsd-release-ports)格式， 众多 BSD 版本的发行(直到 3.X 分支的开始)也证明了这种格式的有效性。 虽然在那以前的某段时间，在 FreeBSD 系统上创建和运行 ELF 格式 的二进制可执行文件(和内核)也是可能的，但 FreeBSD 一开始并不积极“进步” 到使用 ELF 作为其缺省的格式。为什么？噢，当 Linux 阵营完成了 转换到 ELF 格式的痛苦历程后，却发现并不足以由此而放弃 `a.out`可执行文件格式，因为正是由于它们不灵活的， 基于跳转表的共享库机制，使得销售商和开发者们构建共享库非常困难。 直到已有的 ELF 工具提供了一种解决共享库问题的办法， 并被普遍认为是“前进方向”以后，迁徙的代价在 FreeBSD 界才被接受， 并由此完成了迁徙。FreeBSD 的共享库机制其基础更类似于 Sun SunOS™的共享库机制， 并且正因为此，其易用性很好。

那么，为什么会有这么多不同的格式呢？

回溯到蒙昧和黑暗的过去，那时只有简单的硬件。这种简单的硬件支撑了一个简单 和小型的系统。在这样的简单系统上(PDP-11)`a.out`格式 足以胜任表达二进制文件的任务。当人们将 UNIX®从这种简单的系统中移植出来的时候， `a.out`格式被保留了下来，因为对于早期将 UNIX®移植到 Motorola 68k，VAXen 等系统来说，它还是足够可用的。

然后，一些聪明的硬件工程师认为，如果可以让软件完成一些简单的聪明操作， 那么他们就可以在硬件设计中减少若干门电路，并可以让 CPU 核心运行得更快。 当`a.out`格式用于这种新型的硬件系统时(现在我们叫它 RISC)，显得并不合适。因此，人们设计了许多新的格式 以便在这样的硬件系统上能获得比简单的`a.out`格式更优越 的性能。诸如 COFF，ECOFF，还有其它 一些晦涩难懂的格式正是在这个阶段被发明出来的，人们也研究了这些格式的局限性， 慢慢地最终落实到 ELF 格式。

同时，程序的大小变得越来越大，磁盘空间(以及物理内存)相对来说却仍然较小， 因此共享库的概念便产生了。VM 系统也变得越来越复杂了。当所有这些进步都建立在 `a.out`格式的基础上的时候，它的可用性随着每个新特性 的产生就受到了严重考验。并且，人们还希望可以在运行时动态装载某些东西，或者 在初始化代码运行以后可以丢弃部分程序代码，以便节约主存储器和交换区。编程语言 也变得越来越复杂，人们希望可以在 main()函数执行之前自动执行某些代码。为了实现 所有这些功能，人们对`a.out`格式作了很多改动(hack)， 他们在某个阶段里基本也是可行的。随着时间的推移，`a.out`格式 不得不增加大量的代码和复杂度来满足这些需求。虽然 ELF 格式 解决了许多这样的问题，但是从一个可用的系统迁移到另一个系统却是痛苦的。因此 直到继续保留`a.out`格式的代价比迁移到 ELF 格式 的代价还大的时候，人们才会最终转换到 ELF 格式。

然而，随着时间的推移，FreeBSD 系统本身的编译工具(特别是汇编器和装载器) 赖以派生的编译工具，其发展却形成了两个平行的分支。FreeBSD 这个分支增加了共享库， 并修改了一些错误。而原先编写了这些工具的 GNU 人则重写了这些工具，并对交叉编译提供了 更简化的支持，还随意插入了不同格式的支持，等等。虽然很多人希望创建针对 FreeBSD 的 交叉编译器，但他们却并未如愿以偿，因为 FreeBSD 的 as 和 ld 的源代码更为老旧，所以无法完成这个任务。 新的 GNU 工具链(binutils)则确实支持交叉编译，ELF 格式，共享库，C++扩展，等等。并且，由于很多供应商都发布 ELF 格式的 二进制文件，因而让 FreeBSD 能够运行它们将是一个很好的事情。

ELF 格式比`a.out`格式开销要大些，同时也 允许基础系统有更好的扩展性。ELF 格式的有关工具有着更好的维护， 并且提供交叉编译支持，这对许多人来说是很重要的。ELF 格式可能会稍微 慢一些，但很难测量出来。另外，在这两者之间，有许多细节也是不同的，比如它们映射页面的方式， 处理初始化代码的方式，等等。所有这些都不太重要，但这也确实是不同之处。在将来的适当时候， `GENERIC`内核将不再支持`a.out`格式，并且， 当不再需要运行遗留的`a.out`格式程序时，内核也将不再提供对其的支持。

## 4.13. 取得更多的资讯

### 4.13.1. 联机手册

最详细的使用说明文档莫过于 FreeBSD 里的联机手册了。 几乎每一个程序都会附上一份简短说明， 以介绍这个程序的的基本功能以及参数的用法。 我们能通过 `man` 命令来阅读这些说明， 而使用 `man` 命令却是简单的事情:

```
% `man command`
```

`command` 就是您要了解的命令命称。 举个例子，想了解 `ls` 命令就输入:

```
% `man ls`
```

这些在线手册分下列章节:

1.  用户命令。

2.  系统调用以及错误代码。

3.  C 库文件里的函数说明。

4.  设备驱动程序。

5.  文件格式。

6.  游戏以及其他娱乐。

7.  各种资讯。

8.  系统维护以及命令。

9.  内核开发情况。

在某些情况下，同样的主题也会出现在在线手册的不同章节。 举个例子，系统里有`chmod`这个用户命令，而又有个 `chmod()` 系统调用。 在这种情形下，您应当向 `man` 命令指定需要的内容:

```
% `man 1 chmod`
```

这样就会显示出手册里的用户 `chmod` 命令。 传统上，我们在写入文档时把特定详细参考内容在在线手册括号里注明。 所以 [chmod(1)](http://www.FreeBSD.org/cgi/man.cgi?query=chmod&sektion=1&manpath=freebsd-release-ports) 是指 `chmod` 用户命令， 而 [chmod(2)](http://www.FreeBSD.org/cgi/man.cgi?query=chmod&sektion=2&manpath=freebsd-release-ports) 是指系统调用。

如果您已经知道命令的名字，只是不知道要怎样使用的话，那就比较好办。 但您连名字都不知道呢?这个时候您就可以利用 `man` 的搜寻功能， 它会在手册的介绍部份找寻您要搜寻的关键字，它的选项是 `-k`：

```
% `man -k mail`
```

当您使用这个命令的时候，man 会把介绍里含有“mail”关键字 的命令列出来，实际上这和`apropos`命令的功能是相同的。

有时您会看到`/usr/bin` 下有许多命令但不知他们的用途， 您只需这样做:

```
% `cd /usr/bin`
% `man -f *`
```

或者这样做

```
% `cd /usr/bin`
% `whatis *`
```

两个命令是一样的。

### 4.13.2. GNU Info 文件

FreeBSD 许多应用软件以及实用工具来自 Free 软件基金会(FSF)。 作为手册的扩充，这些程序提供了一种更具有活力的超文档说明`info`， 您可用`info`命令来阅读他们。 假如您装上 emacs，也能利用 emacs 的 info 模式来阅读。

使用 [info(1)](http://www.FreeBSD.org/cgi/man.cgi?query=info&sektion=1&manpath=freebsd-release-ports) 这个命令只需简单地输入:

```
% `info`
```

想得到简单介绍， 请按 `h`。 想快速得到的命令说明， 请按 `?`。

## 第 5 章 安装应用程序: Packages 和 Ports

## 5.1. 概述

FreeBSD 将许多系统工具捆绑作为基本系统的一部分。 然而， 要完成实际的工作， 可能还需要安装更多的第三方应用。 FreeBSD 提供了两种补充的技术， 用以在您的系统中安装第三方软件： FreeBSD Ports 套件 (用于从源代码安装)， 以及 packages (用以从预编译的二进制版本安装)。 这两种方法都可以用于从本地介质， 或从网上直接安装您喜欢的应用程序的最新版本。

读完这章，您将了解到:

*   如何安装第三方的二进制软件包。

*   如何使用 ports 套件从源代码构建第三方软件。

*   如何删除先前安装的软件包。

*   如何改动 Ports Collection 里面的一些参数，定制软件使用。

*   如何找到您需要的软件包。

*   如何升级您的应用软件。

## 5.2. 软件安装预览

如果您以前使用过 UNIX® 系统，那典型的第三方软件安装的步骤是像下面描述的：

1.  下载这个软件，软件的发行版可能是源代码格式，或是一个二进制包。

2.  解开软件(其中代表性的是用 [compress(1)](http://www.FreeBSD.org/cgi/man.cgi?query=compress&sektion=1&manpath=freebsd-release-ports), [gzip(1)](http://www.FreeBSD.org/cgi/man.cgi?query=gzip&sektion=1&manpath=freebsd-release-ports), 或 [bzip2(1)](http://www.FreeBSD.org/cgi/man.cgi?query=bzip2&sektion=1&manpath=freebsd-release-ports) 压缩过的 tar 包)。

3.  阅读相关文档，了解如何安装。 (多半一个文件名是`INSTALL`或`README`， 或在`doc/` 目录下的一些文档)

4.  如果软件是以源代码形式发布的，那就需要编译它。可能需要编辑一个 `Makefile`文件, 或运行 `configure`脚本，和其他的一些工作。

5.  测试和安装软件。

如果一切顺利的话，就这么简单。如果您在安装一个软件包时发生一些错误， 您可能需要编辑一下它的代码，以使它能正常工作。

您可以继续使用 “传统的”方式安装软件。 然而， FreeBSD 提供了两种技术： packages 和 ports。 就在写这篇文章的时候， 已经有超过 24,000 个第三方的应用程序可以使用了。

对于任意一个应用程序包，是一个可以下载的 FreeBSD package 文件。这个 FreeBSD package 包含了编译好的的副本， 还有一些配置文件或文档。 一个下载的包文件可以用 FreeBSD 的包管理命令来操作， 例如 [pkg_add(1)](http://www.FreeBSD.org/cgi/man.cgi?query=pkg_add&sektion=1&manpath=freebsd-release-ports)，[pkg_delete(1)](http://www.FreeBSD.org/cgi/man.cgi?query=pkg_delete&sektion=1&manpath=freebsd-release-ports), [pkg_info(1)](http://www.FreeBSD.org/cgi/man.cgi?query=pkg_info&sektion=1&manpath=freebsd-release-ports) 等等。 可以使用一个简单的命令安装一个新的应用程序。

一个 FreeBSD 的 port 是一个可以自动从源代码编译成应用程序的文件集合。

记住，如果您自己来编译的话，需要执行很多步的操作 (解压， 补丁， 编译， 安装)。 这些整理 port 的文件集合包含了系统需要完成这个工作的必需信息。 您可以运行一些简单的命令， 那些源代码就可以自动地下载， 解开， 打补丁， 编译， 直至安装完成。

实际上，ports 系统也能做出被 `pkg_add` 的程序包和不久就要讲到的其他包管理命令来安装的软件包。

Packages 和 ports 是互相 *依赖* 的。 假设您想安装一个依赖于已经安装的特定库的应用程序。 应用程序和那个库都已经应用于 FreeBSD ports 和 packages。 如果您使用 `命令或 ports 系统来添加应用程序， 两个都必须注意库是否被安装， 如果没有， 它会自动先安装库。`

`这里给出的两种技术是很相似的，您可能会奇怪为什么 FreeBSD 会弄出这两种技术。 其实， packages 和 ports 都有它们自己的长处， 使用哪一种完全取决于您自己的喜好。`

`Package Benefits`

*   `一个压缩的 package 通常要比一个压缩的包含源代码的应用程序小得多。`

*   `package 不需要进行额外的编译。 对于大型应用程序如 Mozilla， KDE 或 GNOME 来说这显得尤为重要， 特别是在您的系统资源比较差的情况下。`

*   `package 不需要您知道如何在 FreeBSD 上编译软件的详细过程。`

`Ports Benefits`

*   `package 在编译时通常使用比较保守的选项， 这是为了保证它们能够运行在大多数的系统上。 通过从 port 安装， 您可以细微调整编译选项来产生适合于处理器的代码 (针对于 Pentium 4 或 AMD 的 Athlon CPU)。`

*   `一些软件包已经把与它们相关的能做和不能做的事情的选项都编译进去了。 例如， Apache 可能就配置了很多的选项。 从 port 中安装时， 您不一定要接受默认的选项， 可以自己来设置。`

    `在一些例子中，一个软件有不同的配置存在多个 package。 例如， Ghostscript 存在 `ghostscript` package 和 `ghostscript-nox11` package 两个配置 package， 这取决于您是否安装了 X11 服务器。 这样的调整对 package 是可能的， 但如果一个应用程序有超过一个或两个不同的编译时间选项时， 就不行了。`

*   `一些软件的许可条件禁止采用二进制形式发行。 它们必须带上源代码。`

*   `一些人不信任二进制发行形式。 至少有了源代码， (理论上) 可以亲自阅读它，寻找潜在的问题。`

*   `如果您要自己对软件打补丁，您就需要有源代码。`

*   `一些人喜欢整天围着源代码转， 所以他们喜欢亲自阅读源代码， 修改源代码等等。`

`保持更新 ports， 订阅邮件列表 [FreeBSD ports 邮件列表](http://lists.FreeBSD.org/mailman/listinfo/freebsd-ports) 和递交错误报告 [FreeBSD ports bugs 邮件列表](http://lists.FreeBSD.org/mailman/listinfo/freebsd-ports-bugs)。`

### `警告:`

`安装任何应用程序之前， 应首先检查 `[`vuxml.freebsd.org/`](http://vuxml.freebsd.org/)` 上是否有关于您所安装的应用程序的安全问题报告。`

`您也可以安装 [ports-mgmt/portaudit](http://www.freebsd.org/cgi/url.cgi?ports/ports-mgmt/portaudit/pkg-descr)， 它能够自动地检查已经安装的应用程序的漏洞； 此外， 在您安装程序之前它也会首先检查是否存在已知的漏洞。 另外， 您也可以使用 `portaudit -F -a` 这个命令在安装了某个软件包之后作出检查。`

`这章的其余部分将介绍在 FreeBSD 上如何使用 packages 和 ports 来安装和管理第三方软件。`

 `## 5.3. 寻找您要的应用程序

在您安装任何应用程序之前，需要知道您需要什么，那个应用程序叫什么。

FreeBSD 中可用的应用程序正在不断地增长着。幸运的是， 有许多方法可以找到您所需要的程序:

*   FreeBSD 站点上有一个可以搜索到的当前所有可用的应用程序列表，在 `www.FreeBSD.org/ports/`。 它分很多种类，您既可以通过程序的名称来搜索(如果您知道名字)， 也可以在分类中列出所有可用的应用程序。

*   Dan Langille 维护着网站 FreshPorts，在 `[`www.FreshPorts.org/`](http://www.FreshPorts.org/)`。 FreshPort 时刻 “追踪” 着在 ports 中应用程序的变化。当有任何程序被升级时，他们就会发 email 提醒您。

*   如果您不知道您想要的应用程序的名字，可以通过 (`[`www.freshmeat.net/`](http://www.freshmeat.net/)`) 网站来查找， 如果找到了应用程序， 您可以回 FreeBSD 的主站去看一下这个应用程序是否已经被 port 进去了。

*   如果您知道一个 port 的准确名字， 但需要知道在哪个类别里面能找到它，您可以使用 [whereis(1)](http://www.FreeBSD.org/cgi/man.cgi?query=whereis&sektion=1&manpath=freebsd-release-ports) 这个命令。简单地输入 `whereis file`， *`file`* 就是您想安装的程序名字。 如果系统找到了它， 您将被告知在它在哪里， 例如:

    ```
    # `whereis lsof`
    lsof: /usr/ports/sysutils/lsof
    ```

    结果告诉我们这个命令`lsof` (一个系统配置程序)可以在 `/usr/ports/sysutils/lsof`目录中找到。

*   你可以使用简单的 [echo(1)](http://www.FreeBSD.org/cgi/man.cgi?query=echo&sektion=1&manpath=freebsd-release-ports) 语句来查找某个 port 是否存在于 ports 树中。 例如：

    ```
    # `echo /usr/ports/*/*lsof*`
    /usr/ports/sysutils/lsof
    ```

    Note that this will return any matched files downloaded into the `/usr/ports/distfiles` directory.

    请注意这条命令将会返回下载到 `/usr/ports/distfiles` 目录中所有符合条件的文件。

*   还有另外的一个寻找您需要的 port 的方法--是用 ports collecton 内嵌的搜索机制。要使用这个搜索, 您需要先到 `/usr/ports`目录下面。 在那个目录里面， 运行`make search name=program-name`， *`program-name`* 就是您想寻找的程序名字。 举个例子， 如果您想找 `lsof`：

    ```
    # `cd /usr/ports`
    # `make search name=lsof`
    Port:   lsof-4.56.4
    Path:   /usr/ports/sysutils/lsof
    Info:   Lists information about open files (similar to fstat(1))
    Maint:  obrien@FreeBSD.org
    Index:  sysutils
    B-deps:
    R-deps: 
    ```

    在输出的内容里面您要特别注意包含 “Path:” 的这行将告诉您在哪里可以找到这个 port。 如果要安装此 port， 那其他输出的信息不是必须的， 但是还是显示输出了。

    为了更深入的搜索，您还可以用 `make search key=string`， *`string`*就是您想搜索的部分内容。 它将搜索 port 的名字、 注释， 描述和从属关系， 如果您不知道您想搜索的程序名字， 可以利用它搜索一些关键主题来找到您需要的。

    上面说的这些方法， 搜索的关键字没有大小写区分的。 搜索 “LSOF”的结果将和搜索“lsof”的结果一样。

## 5.4. 使用 Package 系统

Contributed by Chern Lee.

在 FreeBSD 系统上有几种不同的工具用来管理 package：

*   `sysinstall` 工具可以在正在运行的系统上运行， 以完成安装、 删除和列出可用的以及已经安装的预编译软件包的任务。 如欲了解进一步信息， 请参阅 第 2.10.11 节 “安装预编译的软件包 (package)”")。

*   这一节余下的部分将介绍用于管理预编译软件包的命令行工具。

### 5.4.1. 一个 package 的安装

您可以用 [pkg_add(1)](http://www.FreeBSD.org/cgi/man.cgi?query=pkg_add&sektion=1&manpath=freebsd-release-ports) 这个命令从本地文件或网络上的服务器来安装一个 FreeBSD 软件包。

例 5.1. 在本地手动下载一个 package,并安装它

```
# `ftp -a ftp2.FreeBSD.org`
Connected to ftp2.FreeBSD.org.
220 ftp2.FreeBSD.org FTP server (Version 6.00LS) ready.
331 Guest login ok, send your email address as password.
230-
230-     This machine is in Vienna, VA, USA, hosted by Verio.
230-         Questions? E-mail freebsd@vienna.verio.net.
230-
230-
230 Guest login ok, access restrictions apply.
Remote system type is UNIX.
Using binary mode to transfer files.
ftp> `cd /pub/FreeBSD/ports/packages/sysutils/`
250 CWD command successful.
ftp> `get lsof-4.56.4.tgz`
local: lsof-4.56.4.tgz remote: lsof-4.56.4.tgz
200 PORT command successful.
150 Opening BINARY mode data connection for 'lsof-4.56.4.tgz' (92375 bytes).
100% |**************************************************| 92375       00:00 ETA
226 Transfer complete.
92375 bytes received in 5.60 seconds (16.11 KB/s)
ftp> `exit`
# `pkg_add lsof-4.56.4.tgz`
```

如果您没有本地 package 的安装盘 (如 FreeBSD CD-ROM)， 可以执行 [pkg_add(1)](http://www.FreeBSD.org/cgi/man.cgi?query=pkg_add&sektion=1&manpath=freebsd-release-ports) 命令并加上 `-r` 选项。 这将迫使程序自动决定目标文件的正确格式和版本， 然后自动从一个 FTP 站点寻找和安装 package。

```
# `pkg_add -r lsof`
```

上面的例子将下载正确的 package， 而不需要用户的干预就可以安装。 如果您想指定 FreeBSD package 的镜像站点， 替换主站点， 就必须相应地设置 `PACKAGESITE` 这个环境变量， 覆盖原来的设置。 [pkg_add(1)](http://www.FreeBSD.org/cgi/man.cgi?query=pkg_add&sektion=1&manpath=freebsd-release-ports) 使用 [fetch(3)](http://www.FreeBSD.org/cgi/man.cgi?query=fetch&sektion=3&manpath=freebsd-release-ports) 下载文件， 可以使用多种环境变量， 包含 `FTP_PASSIVE_MODE`、 `FTP_PROXY`， 和 `FTP_PASSWORD`。 如果您使用 FTP/HTTP 代理或在防火墙后面， 您可能需要设置这些环境变量。 详细的列表请参考 [fetch(3)](http://www.FreeBSD.org/cgi/man.cgi?query=fetch&sektion=3&manpath=freebsd-release-ports)。上述例子中用 `lsof` 替代了 `lsof-4.56.4`。 当使用远程安装 Package 的时候软件名字不需要包含版本号。 [pkg_add(1)](http://www.FreeBSD.org/cgi/man.cgi?query=pkg_add&sektion=1&manpath=freebsd-release-ports) 将自动的找到这个软件最新的版本。

### 注意:

如果您使用 FreeBSD-CURRENT 或 FreeBSD-STABLE 版本的 FreeBSD， [pkg_add(1)](http://www.FreeBSD.org/cgi/man.cgi?query=pkg_add&sektion=1&manpath=freebsd-release-ports) 将下载您的应用软件的最新版本。 如果您使用 -RELEASE 版本的 FreeBSD, 它将会获得与您的版本相应的软件包版本。 您可以通过修改环境变量 `PACKAGESITE` 来改变这一行为。 例如， 如果您运行 FreeBSD 8.1-RELEASE 系统， 默认情况下 [pkg_add(1)](http://www.FreeBSD.org/cgi/man.cgi?query=pkg_add&sektion=1&manpath=freebsd-release-ports) 将尝试从 `ftp://ftp.freebsd.org/pub/FreeBSD/ports/i386/packages-8.1-release/Latest/` 下载预编译的软件包。 如果您希望强制 [pkg_add(1)](http://www.FreeBSD.org/cgi/man.cgi?query=pkg_add&sektion=1&manpath=freebsd-release-ports) 下载 FreeBSD 8-STABLE 的软件包， 则可以将 `PACKAGESITE` 设置为 `ftp://ftp.freebsd.org/pub/FreeBSD/ports/i386/packages-8-stable/Latest/`。

软件包采用 `.tgz` 和 `.tbz` 两种格式。您可以在 `ftp://ftp.FreeBSD.org/pub/FreeBSD/ports/packages/` 下面或从 FreeBSD 的发行光盘找到， 它在每一个 4CD 的 FreeBSD 发行版的 `/packages`目录中。 软件包的设计规划与 `/usr/ports` 树一致。 每个分类都有自己的目录， 所有的软件包可以在目录 `All`中找到。

软件包系统的目录结构与 ports 的设计规划一致； 它们共同构成了整个 package/port。

### 5.4.2. 软件包的管理

[pkg_info(1)](http://www.FreeBSD.org/cgi/man.cgi?query=pkg_info&sektion=1&manpath=freebsd-release-ports) 是用于列出已安装的所有软件包列表和描述的程序。

```
# `pkg_info`
cvsup-16.1          A general network file distribution system optimized for CV
docbook-1.2         Meta-port for the different versions of the DocBook DTD
...
```

[pkg_version(1)](http://www.FreeBSD.org/cgi/man.cgi?query=pkg_version&sektion=1&manpath=freebsd-release-ports)是一个用来统计所有安装的软件包版本的工具。 它可以用来比较本地 package 的版本与 ports 目录中的当前版本是否一致。

```
# `pkg_version`
cvsup                       =
docbook                     =
...
```

在第二列的符号指出了安装版本的相关时间和本地 ports 目录树中可用的版本。

| 符号 | 含义 |
| --- | --- |
| = | 在本地 ports 树中与已安装的软件包版本相匹配。 |
| < | 已安装的版本要比在 ports 树中的版本旧。 |
| > | 已安装的版本要比在 ports 树中的版本新 (本地的 port 树可能没有更新)。 |
| ? | 已安装的软件包无法在 ports 索引中找到。 (可能发生这种事情，举个例子， 您早先安装的一个 port 从 port 树中移出或改名了) |
| * | 软件包有很多版本。 |
| ! | 已安装的软件包在索引中存有记录， 但是由于某些原因 `pkg_version` 无法比较已安装的软件包与索引中相对应的版本号。 |

### 5.4.3. 删除一个软件包

要删除先前安装的软件 package，只要使用[pkg_delete(1)](http://www.FreeBSD.org/cgi/man.cgi?query=pkg_delete&sektion=1&manpath=freebsd-release-ports) 工具。

```
# `pkg_delete xchat-1.7.1`
```

需要注意的是， [pkg_delete(1)](http://www.FreeBSD.org/cgi/man.cgi?query=pkg_delete&sektion=1&manpath=freebsd-release-ports) 需要提供完整的包名； 如果您只是指定了类似 *`xchat`* 而不是 *`xchat-1.7.1`* 这样的名字， 则它将拒绝执行操作。 不过， 您可以使用 [pkg_version(1)](http://www.FreeBSD.org/cgi/man.cgi?query=pkg_version&sektion=1&manpath=freebsd-release-ports) 来了解安装的 package 的版本。 除此之外， 也可以使用通配符：

```
# `pkg_delete xchat\*`
```

这时， 所有名字以 `xchat` 开头的 package 都会被删掉。

### 5.4.4. 其它

所有已安装的 package 信息都保存在 `/var/db/pkg` 目录下。 安装文件的列表和每个 package 的内容和描述都能在这个目录的相关文件中找到。

## 5.5. 使用 Ports Collection

下面的几个小节中， 给出了关于如何使用 Ports 套件来在您的系统中安装或卸载程序的介绍。 关于可用的 `make` targets 以及环境变量的介绍， 可以在 [ports(7)](http://www.FreeBSD.org/cgi/man.cgi?query=ports&sektion=7&manpath=freebsd-release-ports) 中找到。

### 5.5.1. 获得 Ports Collection

在您能使用 ports 之前， 您必须先获得 Ports Collection ── 本质上是 `/usr/ports` 目录下的一堆 `Makefile`、 补丁和描述文件。

在您安装 FreeBSD 系统的时候， sysinstall 会询问您是否需要安装 Ports Collection。 如果您选择 no， 那您可以用下面的指令来安装 Ports Collection：

过程 5.1. CVSup 方法

保持您本地 Ports 套件最新的一种快捷的方法， 是使用 CVSup 协议来进行更新。 如果您希望了解更多关于 CVSup 的细节， 请参见 使用 CVSup。

### 注意:

在 FreeBSD 系统里对 CVSup 的实现叫作 csup。

在首次运行 csup 之前， 务必确认 `/usr/ports` 是空的！ 如果您之前已经用其他地方安装了一份 Ports 套件， 则 csup 可能不会自动删除已经在上游服务器上删除掉的补丁文件。

1.  运行 `csup`:

    ```
    # `csup -L 2 -h cvsup.FreeBSD.org /usr/share/examples/cvsup/ports-supfile`
    ```

    将 *`cvsup.FreeBSD.org`* 改为离您较近的 CVSup 服务器。 请参见 CVSup 镜像 (第 A.6.7 节 “CVSup 站点”) 中的镜像站点完整列表。

    ### 注意:

    有时可能希望使用自己的 `ports-supfile`， 比如说， 不想每次都通过命令行来指定所使用的 CVSup 服务器。

    1.  这种情况下， 需要以 `root` 身份将 `/usr/share/examples/cvsup/ports-supfile` 复制到新的位置， 例如 `/root` 或您的主目录。

    2.  编辑 `ports-supfile`。

    3.  把 *`CHANGE_THIS.FreeBSD.org`* 修改成离您较近的 CVSup 服务器。 可以参考 CVSup 镜像 (第 A.6.7 节 “CVSup 站点”) 中的镜像站点完整列表。

    4.  接下来按如下的方式运行 `csup`：

        ```
        # `csup -L 2 /root/ports-supfile`
        ```

2.  此后运行 [csup(1)](http://www.FreeBSD.org/cgi/man.cgi?query=csup&sektion=1&manpath=freebsd-release-ports) 命令将下载最近所进行的改动， 并将它们应用到您的 Ports Collection 上， 不过这一过程并不重新联编您系统上的 ports。

过程 5.2. Portsnap 方式

Portsnap 是用于发布 Ports 套件的另一套系统。 请参阅 使用 Portsnap 以了解关于 Portsnap 功能更详细的介绍。

1.  下载压缩的 Ports 套件快照到 `/var/db/portsnap`。 您可以根据需要在这之后关闭 Internet 连接。

    ```
    # `portsnap fetch`
    ```

2.  假如您是首次运行 Portsnap， 则需要将快照释放到 `/usr/ports`：

    ```
    # `portsnap extract`
    ```

    如果您已经有装好的 `/usr/ports` 而您只想更新， 则应执行下面的命令：

    ```
    # `portsnap update`
    ```

过程 5.3. Sysinstall 方式

这种方法需要使用 sysinstall 从安装介质上安装 Ports 套件。 注意， 安装的将是发布发行版时的旧版 Ports 套件。 如果您能访问 Internet， 应使用前面介绍的方法之一。

1.  以 `root` 身份运行 `sysinstall`：

    ```
    # `sysinstall`
    ```

2.  用光标向下选择 Configure， 并按 **Enter**。

3.  向下并选择 Distributions， 按 **Enter**。

4.  选择 ports， 并按 **Space**。

5.  选择 Exit， 并按 **Enter**。

6.  选择所希望的安装介质， 例如 CDROM、 FTP， 等等。

7.  选择 Exit 并按 **Enter**。

8.  按 **X** 退出 sysinstall。

### 5.5.2. 安装 Ports

当提到 Ports Collection 时， 第一个要说明的就是何谓 “skeleton”。 简单地说， port skeleton 是让一个程序在 FreeBSD 上简洁地编译并安装的所需文件的最小组合。 每个 port skeleton 包含：

*   一个 `Makefile`。 `Makefile` 包括好几个部分， 指出应用程序是如何编译以及将被安装在系统的哪些地方。

*   一个 `distinfo` 文件。这个文件包括这些信息： 这些文件用来对下载后的文件校验和进行检查 (使用 [sha256(1)](http://www.FreeBSD.org/cgi/man.cgi?query=sha256&sektion=1&manpath=freebsd-release-ports))， 来确保在下载过程中文件没有被破坏。

*   一个 `files` 目录。 这个目录包括在 FreeBSD 系统上编译和安装程序需要用到的补丁。 这些补丁基本上都是些小文件， 指出特定文件作了哪些修正。 它们都是纯文本的的格式，基本上是这样的 “删除第 10 行” 或 “将第 26 行改为这样 ...”， 补丁文件也被称作 “diffs”， 他们由 [diff(1)](http://www.FreeBSD.org/cgi/man.cgi?query=diff&sektion=1&manpath=freebsd-release-ports) 程序生成。

    这个目录也包含了在编译 port 时要用到的其它文件。

*   一个 `pkg-descr` 文件。 这是一个提供更多细节，有软件的多行描述。

*   一个 `pkg-plist` 文件。 这是即将被安装的所有文件的列表。它告诉 ports 系统在卸载时需要删除哪些文件。

一些 ports 还有些其它的文件， 例如 `pkg-message`。 ports 系统在一些特殊情况下会用到这些文件。 如果您想知道这些文件更多的细节以及 ports 的概要， 请参阅 FreeBSD Porter's Handbook。

port 里面包含着如何编译源代码的指令， 但不包含真正的源代码。 您可以在网上或 CD-ROM 上获得源代码。 源代码可能被开发者发布成任何格式。 一般来说应该是一个被 tar 和 gzip 过的文件， 或者是被一些其他的工具压缩或未压缩的文件。 ports 中这个程序源代码标示文件叫 “distfile”， 安装 FreeBSD port 的方法还不止这两种。

### 注意:

您必须使用 `root` 用户登录后安装 ports。

### 警告:

在安装任何 port 之前， 应该首先确保已经更新到了最新的 Ports Collection， 并检查 `[`vuxml.freebsd.org/`](http://vuxml.freebsd.org/)` 中是否有与那个 port 有关的安全问题。

在安装应用程序之前， 可以使用 portaudit 来自动地检查是否存在已知的安全问题。 这个工具同样可以在 Ports Collection ([ports-mgmt/portaudit](http://www.freebsd.org/cgi/url.cgi?ports/ports-mgmt/portaudit/pkg-descr)) 中找到。 在安装新的 port 之前， 可以考虑先运行一下 `portaudit -F` 来抓取最新的漏洞数据库。 在每天的周期性系统安全检察时， 数据库会被自动更新， 并且会在这之后实施安全审计。 欲了解进一步的情况，请参阅 [portaudit(1)](http://www.FreeBSD.org/cgi/man.cgi?query=portaudit&sektion=1&manpath=freebsd-release-ports) 和 [periodic(8)](http://www.FreeBSD.org/cgi/man.cgi?query=periodic&sektion=8&manpath=freebsd-release-ports)。

Ports 套件假定您有可用的 Internet 连接。 如果您没有， 则需要将 distfile 手工放到 `/usr/ports/distfiles` 中。

要开始操作， 首先进入要安装 port 的目录：

```
# `cd /usr/ports/sysutils/lsof`
```

一旦进入了 `lsof` 的目录，您将会看到这个 port 的结构。 下一步就是 make，或说 “联编” 这个 port。 只需在命令行简单地输入 `make` 命令就可轻松完成这一工作。 做好之后，您可以看到下面的信息：

```
# `make`
>> lsof_4.57D.freebsd.tar.gz doesn't seem to exist in /usr/ports/distfiles/.
>> Attempting to fetch from ftp://lsof.itap.purdue.edu/pub/tools/unix/lsof/.
===>  Extracting for lsof-4.57
...
[extraction output snipped]
...
>> Checksum OK for lsof_4.57D.freebsd.tar.gz.
===>  Patching for lsof-4.57
===>  Applying FreeBSD patches for lsof-4.57
===>  Configuring for lsof-4.57
...
[configure output snipped]
...
===>  Building for lsof-4.57
...
[compilation output snipped]
...
#
```

注意，一旦编译完成，您就会回到命令行。 下一步安装 port， 要安装它只需要在 `make` 命令后跟上一个单词 `install` 即可：

```
# `make install`
===>  Installing for lsof-4.57
...
[installation output snipped]
...
===>   Generating temporary packing list
===>   Compressing manual pages for lsof-4.57
===>   Registering installation for lsof-4.57
===>  SECURITY NOTE:
      This port has installed the following binaries which execute with
      increased privileges.
#
```

一旦您返回到提示符，您就可以运行您刚刚安装的程序了。因为 `lsof` 是一个赋予特殊权限的程序， 因此显示了一个安全警告。 在编译和安装 ports 的时候， 您应该留意任何出现的警告。

删除工作目录是个好主意， 这个目录中包含了全部在编译过程中用到的临时文件。 这些文件不仅会占用宝贵的磁盘空间， 而且可能会给升级新版本的 port 时带来麻烦。

```
# `make clean`
===>  Cleaning for lsof-4.57
#
```

### 注意:

使用 `make install clean` 可以一步完成 `make`、 `make install` 和 `make clean` 这三个分开的步骤的工作。

### 注意:

一些 shell 会缓存环境变量 `PATH` 中指定的目录里的可执行文件， 以加速查找它们的速度。 如果您使用的是这类 shell， 在安装 port 之后可能需要执行 `rehash` 命令， 然后才能运行新安装的那些命令。 这个命令可以在类似 `tcsh` 的 shell 中使用。 对于类似 `sh` 的 shell， 对应的命令是 `hash -r`。 请参见您的 shell 的文档以了解进一步的情况。

某些第三方 DVD-ROM 产品， 如 [FreeBSD Mall](http://www.freebsdmall.com/) 的 FreeBSD Toolkit 中包含了 distfiles。 这些文件可以与 Ports 套件配合使用。 将 DVD-ROM 挂接到 `/cdrom`。 如果您使用不同的挂接点， 则应设置 make 变量 `CD_MOUNTPTS`。 如果盘上有需要的 distfiles， 则会自动使用。

### 注意:

请注意， 少数 ports 并不允许通过 CD-ROM 发行。 这可能是由于下载之前需要填写注册表格， 或者不允许再次发布， 或者有一些其它原因。 如果您希望安装在 CD-ROM 上没有的 port， 就需要在线操作了。

ports 系统使用 [fetch(1)](http://www.FreeBSD.org/cgi/man.cgi?query=fetch&sektion=1&manpath=freebsd-release-ports) 去下载文件， 它有很多可以设置的环境变量， 其中包括 `FTP_PASSIVE_MODE`、 `FTP_PROXY`， 和 `FTP_PASSWORD`。 如果您在防火墙之后，或使用 FTP/HTTP 代理， 您就可能需要设置它们。 完整的说明请看 [fetch(3)](http://www.FreeBSD.org/cgi/man.cgi?query=fetch&sektion=3&manpath=freebsd-release-ports)。

当使用者不是所有时间都能连接上网络， 则可以利用 `make fetch`。 您只要在顶层目录 (`/usr/ports`) 下运行这个命令， 所有需要的文件都将被下载。 这个命令也同样可以在下级类别目录中使用， 例如： `/usr/ports/net`。 注意， 如果一个 port 有一些依赖的库或其他 port， 它将 *不* 下载这些依赖的 port 的 distfile 文件， 如果您想获取所有依赖的 port 的所有 distfile， 请用 `fetch-recursive` 命令代替 `fetch`命令。

### 注意:

您可以在一个类别或在顶级目录编译所有的 port， 或者使用上述提到的 `make fetch`命令。 这样是非常危险的， 因为有一些 port 不能并存。 或者有另一种可能， 一些 port 会安装两个不同的文件， 但是却是相同的文件名。

在一些罕见的例子中， 用户可能需要在除了 `MASTER_SITES` 以外的一个站点(本地已经下载下来的文件)去获得一个文件包。 您可以用以下命令不使用 `MASTER_SITES`:

```
# `cd /usr/ports/directory`
# `make MASTER_SITE_OVERRIDE= \
ftp://ftp.FreeBSD.org/pub/FreeBSD/ports/distfiles/ fetch`
```

在这个例子中，我们把 `MASTER_SITES`这个选项改为了 `ftp.FreeBSD.org/pub/FreeBSD/ports/distfiles/`。

### 注意:

一些 port 允许 (或甚至要求) 您指定编译选项来 启用/禁用 应用程序中非必需的功能， 一些安全选项， 以及其他可以订制的内容。 具有代表性的包括 [www/mozilla](http://www.freebsd.org/cgi/url.cgi?ports/www/mozilla/pkg-descr)、 [security/gpgme](http://www.freebsd.org/cgi/url.cgi?ports/security/gpgme/pkg-descr)、 以及 [mail/sylpheed-claws](http://www.freebsd.org/cgi/url.cgi?ports/mail/sylpheed-claws/pkg-descr)。 如果存在这样的选项， 通常会在编译时给出提示。

#### 5.5.2.1. 改变默认的 Ports 目录

有时， 使用不同的工作临时目录和目标目录可能很有用 (甚至是必要的)。 可以用 `WRKDIRPREFIX` 和 `PREFIX` 这两个变量来改变默认的目录。 例如：

```
# `make WRKDIRPREFIX=/usr/home/example/ports install`
```

将在 `/usr/home/example/ports` 中编译 port 并把所有的文件安装到 `/usr/local`。

```
# `make PREFIX=/usr/home/example/local install`
```

将在 `/usr/ports` 编译它并安装到 `/usr/home/example/local`。

当然，

```
# `make WRKDIRPREFIX=../ports PREFIX=../local install`
```

将包含两种设置 (没有办法在这一页把它写完， 但您应该已经知道怎么回事了)。

另外， 这些变量也可以作为环境变量来设置。 请参考您的 shell 的联机手册上关于如何设置环境变量的说明。

#### 5.5.2.2. 处理 `imake`

一些 port 使用 `imake` (这是 X Window 系统的一部分) 不能正常地配合 `PREFIX`， 它们会坚持把文件安装到 `/usr/X11R6` 下面。 类似地， 一些 Perl port 会忽略 `PREFIX` 并把文件安装到 Perl 的目录中。 让这些 port 尊重 `PREFIX` 是困难甚至是不可能的事情。

#### 5.5.2.3. 重新配置 Ports

当你在编译某些 ports 的时候，可能会弹出一个基于 ncurses 的菜单来让你来选择一些编译选项。 通常用户都希望能够在一个 port 被编译安装了以后还能再次访问这份菜单以添加删除或修改这些选项。 实际上有很多方法来做这件事情。 一个方法进入那个 port 的目录后键入 `make` `config`， 之后便会再次显示出菜单和已选择的项目。 另一个方法是用 `make` `showconfig`， 这会给你显示出所有的配置选项。还有一个方法是执行 `make` `rmconfig`， 这将删除所有已选择的项目。 有关这些选项更详细的内容请参阅 [ports(7)](http://www.FreeBSD.org/cgi/man.cgi?query=ports&sektion=7&manpath=freebsd-release-ports)。

### 5.5.3. 卸载已经安装的 Ports

现在您已经了解了如何安装 ports， 并希望进一步了解如何卸载， 特别是在错误地安装了某个 port 之后。我们将卸载前面例子 (假如您没有注意的话， 是 `lsof`) 中安装的 port。 Ports 可以同 packages 以完全相同的方式 (在 Packages 一节 中进行了介绍) 卸载， 方法是使用 [pkg_delete(1)](http://www.FreeBSD.org/cgi/man.cgi?query=pkg_delete&sektion=1&manpath=freebsd-release-ports) 命令：

```
# `pkg_delete lsof-4.57`
```

### 5.5.4. 升级 Ports

首先， 使用 [pkg_version(1)](http://www.FreeBSD.org/cgi/man.cgi?query=pkg_version&sektion=1&manpath=freebsd-release-ports) 命令来列出 Ports Collection 中提供了更新版本的那些 port：

```
# `pkg_version -v`
```

#### 5.5.4.1. `/usr/ports/UPDATING`

在您更新了 Ports 套件之后， 在升级 port 之前， 应查看 `/usr/ports/UPDATING`。 这个文件中介绍了在升级时用户应注意的问题， 以及一些可能需要进行的操作。 这可能包括更改文件格式、 配置文件位置的变动， 以及与先前版本的兼容性等等。

如果 `UPDATING` 与本书中介绍的内容不同， 请以 `UPDATING` 为准。

#### 5.5.4.2. 使用 Portupgrade 来更新 Ports

portupgrade 工具是设计来简化升级已安装的 port 的操作的。 它通过 [ports-mgmt/portupgrade](http://www.freebsd.org/cgi/url.cgi?ports/ports-mgmt/portupgrade/pkg-descr) port 来提供。 您可以像其它 port 那样， 使用 `make install clean` 命令来安装它：

```
# `cd /usr/ports/ports-mgmt/portupgrade`
# `make install clean`
```

使用 `pkgdb -F` 命令来扫描已安装的 port 的列表， 并修正其所报告的不一致。 在每次升级之前， 有规律地执行它是个好主意。

运行 `portupgrade -a` 时， portupgrade 将开始并升级系统中所安装的所有过时的 ports。 如果您希望在每个升级操作时得到确认， 应指定 `-i` 参数。

```
# `portupgrade -ai`
```

如果您只希望升级某个特定的应用程序， 而非全部可用的 port， 应使用 `portupgrade pkgname`。 如果 portupgrade 应首先升级指定应用程序的话， 则应指定 `-R` 参数。

```
# `portupgrade -R firefox`
```

要使用预编译的 package 而不是 ports 来进行安装， 需要指定 `-P`。 如果指定了这个选项， portupgrade 会搜索 `PKG_PATH` 中指定的本地目录， 如果没有找到， 则从远程站点下载。 如果本地没有找到， 而且远程站点也没有成功地下载预编译包， 则 portupgrade 将使用 ports。 要禁止使用 port， 可以指定 `-PP`。

```
# `portupgrade -PP gnome2`
```

如果只想下载 distfiles (或者， 如果指定了 `-P` 的话， 是 packages) 而不想构建或安装任何东西， 可以使用 `-F`。 要了解更多细节， 请参考 [portupgrade(1)](http://www.FreeBSD.org/cgi/man.cgi?query=portupgrade&sektion=1&manpath=freebsd-release-ports)。

#### 5.5.4.3. 使用 Portmanager 来升级 Ports

Portmanager 是另一个用以简化已安装 port 升级操作的工具。 它可以通过 [ports-mgmt/portmanager](http://www.freebsd.org/cgi/url.cgi?ports/ports-mgmt/portmanager/pkg-descr) port 安装：

```
# `cd /usr/ports/ports-mgmt/portmanager`
# `make install clean`
```

可以通过这个简单的命令来升级所有已安装的 port：

```
# `portmanager -u`
```

如果希望 Portmanager 在进行每步操作之前都给出提示， 应使用 `-ui` 参数。 Portmanager 也可以用来在系统中安装新的 ports。 与通常的 `make install clean` 命令不同，它会在联编和安装您所选择的 port 之前升级所有依赖包。

```
# `portmanager x11/gnome2`
```

如果关于所选 port 的依赖有任何问题， 可以用 Portmanager 来以正确的顺序重新构建它们。 完成之后， 有问题的 port 也将被重新构建。

```
# `portmanager graphics/gimp -f`
```

要了解更多信息， 请参见 [portmanager(1)](http://www.FreeBSD.org/cgi/man.cgi?query=portmanager&sektion=1&manpath=freebsd-release-ports)。

#### 5.5.4.4. 使用 Portmaster 升级 Ports

Portmaster 是另外一个用来升级已安装的 ports 的工具。 Portmaster 被设计成尽可能使用 “基本” 系统中能找到的工具 （它不依赖于其他的 ports） 和 `/var/db/pkg/` 中的信息来检测出需要升级的 ports。你可以在 [ports-mgmt/portmaster](http://www.freebsd.org/cgi/url.cgi?ports/ports-mgmt/portmaster/pkg-descr) 找到它：

```
# `cd /usr/ports/ports-mgmt/portmaster`
# `make install clean`
```

Portmaster groups ports into four categories:

Portmaster 把 ports 分成 4 类：

*   Root ports (不依赖其他的 ports，也不被依赖)

*   Trunk ports (不依赖其他的 ports，但是被其他的 ports 依赖)

*   Branch ports (依赖于其他的 ports，同时也被依赖)

*   Leaf ports (依赖于其他的 ports，但不被依赖)

你可以使用 `-L` 选项列出所有已安装的 ports 和查找存在更新的 ports：

```
# `portmaster -L`
===>>> Root ports (No dependencies, not depended on)
===>>> ispell-3.2.06_18
===>>> screen-4.0.3
        ===>>> New version available: screen-4.0.3_1
===>>> tcpflow-0.21_1
===>>> 7 root ports
...
===>>> Branch ports (Have dependencies, are depended on)
===>>> apache-2.2.3
        ===>>> New version available: apache-2.2.8
...
===>>> Leaf ports (Have dependencies, not depended on)
===>>> automake-1.9.6_2
===>>> bash-3.1.17
        ===>>> New version available: bash-3.2.33
...
===>>> 32 leaf ports

===>>> 137 total installed ports
        ===>>> 83 have new versions available

```

可以使用这个简单的命令升级所有已安装的 ports：

```
# `portmaster -a`
```

### 注意:

Portmaster 默认在删除一个现有的 port 前会做一个备份包。如果新的版本能够被成功安装， Portmaster 将删除备份。 使用 `-b` 后 Portmaster 便不会自动删除备份。加上 `-i` 选项之后 Portmaster 将进入互动模式， 在升级每个 port 以前提示你给予确认。

如果你在升级的过程中发现了错误，你可以使用 `-f` 选项升级/重新编译所有的 ports：

```
# `portmaster -af`
```

同样你也可以使用 Portmaster 往系统里安装新的 ports，升级所有的依赖关系之后并安装新的 port：

```
# `portmaster shells/bash`
```

更多的详细信息请参阅 [portmaster(8)](http://www.FreeBSD.org/cgi/man.cgi?query=portmaster&sektion=8&manpath=freebsd-release-ports)

### 5.5.5. Ports 和磁盘空间

使用 Ports 套件会最终用完磁盘空间。 在通过 ports 联编和安装软件之后，您应记得清理临时的 `work` 目录， 其方法是使用 `make clean` 命令。 您可以使用下面的命令来清理整个 Ports 套件：

```
# `portsclean -C`
```

随着时间的推移， 您可能会在 `distfiles` 目录中积累下大量源代码文件。 您可以手工删除这些文件， 也可以使用下面的命令来删除所有 port 都不引用的文件：

```
# `portsclean -D`
```

除此之外， 也可以用下列命令删去目前安装的 port 没有使用的源码包文件：

```
# `portsclean -DD`
```

### 注意:

这个 `portsclean` 工具是 portupgrade 套件的一部分。

不要忘记删除那些已经安装， 但已不再使用的 ports。 用于自动完成这种工作的一个好工具是 [ports-mgmt/pkg_cutleaves](http://www.freebsd.org/cgi/url.cgi?ports/ports-mgmt/pkg_cutleaves/pkg-descr) port。

## 5.6. 安装之后还要做点什么？

通常，您通过 port 安装完一个软件后,可以阅读它带的一些文档(如果它包含文档的话)， 或需要编辑它的配置文件，来确保这个软件的运行， 或在机器启动的时候启动(如果它是一个服务的话)，等等。

对于不同的软件有着不同的配置步骤。不管怎样， 如果您装好了一个软件，但是不知道下一步怎么办的时候， 这些小技巧可能可以帮助您:

*   使用 [pkg_info(1)](http://www.FreeBSD.org/cgi/man.cgi?query=pkg_info&sektion=1&manpath=freebsd-release-ports) 命令，它能找到安装了哪些文件，以及装在哪里。 举个例子，如果您安装了 FooPackage version 1.0.0, 那么这个命令

    ```
    # `pkg_info -L foopackage-1.0.0 | less`
    ```

    将显示这个软件包安装的所有文件，您要特别注意在`man/`目录里面的文件， 它们可能是手册，`etc/`目录里面的配置文件，以及 `doc/`目录下面更多的文档。

    如果您不确定已经安装好的软件版本，您可以使用这样的命令

    ```
    # `pkg_info | grep -i foopackage`
    ```

    它将会找到所有已安装的软件包名字中包含*`foopackage`* 的软件包。 对于其他的查找， 您只需要在命令行中替换 *`foopackage`*。

*   一旦一些软件手册已被您确认安装，您可以使用 [man(1)](http://www.FreeBSD.org/cgi/man.cgi?query=man&sektion=1&manpath=freebsd-release-ports) 查看它。 同样的，如果有的话，您还可以完整的查看一遍配置文件的示例，以及任何额外的文档。

*   如果应用软件有网站, 您还可以从网站上找到文档，常见问题的解答，或其他更多。 如果您不知道它们的网站地址，请使用下面的命令

    ```
    # `pkg_info foopackage-1.0.0`
    ```

    一个 `WWW:` 行, 如果它存在， 它将提供一个这个应用程序的网站 URL.

*   Ports 如果需要在服务器启动时运行(就像互联网服务器)， 它通常会把一个脚本的样例放入 `/usr/local/etc/rc.d` 目录。为了保证正确性， 您可以查看这个脚本， 并编辑或更改这个脚本的名字。 详情请看启动服务。

## 5.7. 如何处理坏掉的 Ports

如果您发现某个 port 无法正常工作， 有几件事值得尝试， 包括：

1.  在 问题报告数据库 中查找是否有尚未提交的修正。 如果有， 可以使用所提议的修正。

2.  要求 port 的监护人 (maintainer) 提供帮助。 输入 `make maintainer` 或阅读 `Makefile` 查找监护人的电子邮件地址。 请记得把 port 的名字和版本写在邮件里 (`Makefile` 中的 `$FreeBSD:`这一行) 并把错误输出的头几行发给 maintainer。

    ### 注意:

    某些 ports 并非一个人维护， 而是写了一个 邮件列表。 许多， 但并非所有 port， 使用类似 `<freebsd-listname@FreeBSD.org>` 这样的地址。 请在提出问题时考虑这一点。

    特别地， 由 `<ports@FreeBSD.org>` 监护的 port， 实际上并没有人维护。 订阅这个邮件列表的人们会感谢您提供的修正和支持。 我们一直都需要更多志愿者！

    如果您没有得到回应， 则可以使用 [send-pr(1)](http://www.FreeBSD.org/cgi/man.cgi?query=send-pr&sektion=1&manpath=freebsd-release-ports) 来提交问题报告 (请参见 如何撰写 FreeBSD 问题报告)。

3.  修正它！ Porter 手册 中提供了关于 “Ports” 基础设施的详细信息， 通过了解这些内容， 您就能修正偶然坏掉的 port， 或甚至提交自己的 port 了！

4.  从较近的 FTP 站点下载一个编译好的安装包。 “中央的” package collection 在 `ftp.FreeBSD.org` 的 packages 目录中， 但 *在此之前* 请 *事先* 检查一下是否存在较近的 镜像网站！ 通常情况下这些安装包都可以直接使用， 而且应该比自行编译快一些。 安装过程本身可以通过 [pkg_add(1)](http://www.FreeBSD.org/cgi/man.cgi?query=pkg_add&sektion=1&manpath=freebsd-release-ports) 来完成。

## 第 6 章 X Window 系统

根据 X.Org 的 X11 服务修改此文档 Ken Tom 和 Marc Fonvieille.

## 6.1. 概述

FreeBSD 使用 X11 来为用户提供功能强大的图形用户界面。 X11 是一种可以免费使用的 X 视窗系统， 其实现包括 Xorg FreeBSD 中默认使用并受官方支持的 X11 实现即是 Xorg， 它是由 X.Org 基金会开发的 X11 服务， 采用与 FreeBSD 类似的授权。 此外， 也有一些用于 FreeBSD 的商业 X 服务器。

欲了解 X11 所支持的显示卡等硬件， 请访问 [Xorg](http://www.x.org/) 网站。

在阅读完这一章后，您将会了解：

*   X 视窗系统的不同组件，它们是如何协同工作的。

*   如何安装和配置 X11。

*   如何安装和使用不同的窗口管理器。

*   如何在 X11 中使用 TrueType® 字体。

*   如何为您的系统设置图形登录 (XDM)。

在阅读这一章之前，您应该：

*   知道如何安装额外的第三方应用程序(第 5 章 *安装应用程序: Packages 和 Ports*)。

## 6.2. 理解 X

对于那些熟悉其他图形环境，比如 Microsoft® Windows® 或者 Mac OS® 的用户来说，第一次使用 X 可能会感觉很惊讶。

通常您并不需要深入了解各种 X 组件的作用以及它们之间的相互影响， 不过， 了解一些关于它们的基础知识， 有助于更好地利用 X 的强大功能。

### 6.2.1. 为什么要使用 X?

X 不是第一个为 UNIX® 而开发的视窗系统， 但它是最流行的。 X 的原始开发团队在开发 X 之前就已经在另外一个视窗系统上工作了。 那个系统的名字叫做 “W” (就是 “Window”)。X 只是罗马字母中 W 后面 的一个。

X 可以被叫做 “X”, “X Window 系统”, “X11”, 等等。把 X11 称做 “X Windows” 可能会冒犯某些人； 查看 [X(7)](http://www.FreeBSD.org/cgi/man.cgi?query=X&sektion=7&manpath=freebsd-release-ports) 可以了解更多的信息。

### 6.2.2. X 客户机/服务器模型

X 一开始就是针对网络而设计的，所以 采用了 “client-server” 模型。在 X 模型中， “X server” 运行在有键盘，显示器，鼠标的计算机上。 服务器用来管理显示信息，处理来自键盘和鼠标的输入信息， 并与其他输入输出设备交互 (比如作为输入设备的 “tablet”， 或者作为输出设备的投影仪)。 每一个 X 应用程序 (比如 XTerm, 或者 Netscape®) 就是一个 “客户程序 (client)”。 客户程序给服务器发送信息，如 “请在这些坐标上画一个窗口”， 而服务器则返回处理信息， 如 “用户刚刚点击了 OK 按钮”。

如果您家或办公环境中只有一台使用 FreeBSD 的计算机， 就只能在同一台计算机上运行 X server 和 X client 了。 然而， 如果您有很多运行 FreeBSD 的机器， 您可以在您的桌面计算机上运行 X server， 而在比较高档的服务器上运行 X 应用程序。 在这样的环境中， X server 和 X client 之间的通信就可以通过网络来进行。

这可能会让一些人感到困惑， 因为 X 的术语和他们料想的有些不同。 他们以为 “X server” 是运行在功能强大的大型机上的，而 “X client” 是运行在他们桌面上的计算机上的。

记住，X server 是有键盘和显示器的那台计算机，而 X client 是那些显示窗口的程序。

Client 和 server 不一定都要运行在同一种操作系统上， 它们甚至无需在同一种类型的计算机上运行。 在 Microsoft® Windows® 或 Apple 公司的 Mac OS® 上运行 X server 也是可以的， 在它们上面也有很多免费的和商业化的应用程序。

### 6.2.3. 窗口管理器

X 的设计哲学很像 UNIX® 的设计哲学， “tools, not policy”。这就意味着 X 不会试图去规定任务应该如何 去完成，而是，只给用户提供一些工具，至于决定如何使用这些工具是用户自己的 事情。

这套哲学扩展了 X，它不会规定窗口在屏幕上应该是什么样子，要如何移动鼠标， 应该用什么键来切换窗体 (比如， **Alt**+**Tab**按键，在 Microsoft® Windows® 环境中的作用), 每个窗口的工具条应该 看起来像什么，他们是否应该有关闭按钮等等。

实际上，X 行使了一种叫做 “窗口管理器”的应用程序的职责。有很多这样的程序可用： AfterStep, Blackbox, ctwm, Enlightenment, fvwm, Sawfish, twm, Window Maker，等等。每一个窗口管理器 都提供了不同的界面和观感；其中一些还支持 “虚拟桌面”；有一些允许您可以定制一些键来管理您的桌面； 一些有“开始” 按钮，或者其他类似的设计；一些是 “可定制主题的(themeable)”， 通过安装新的主题， 可以完全改变外观。 这些以及很多其他的窗口管理器， 都可以在 Ports Collection 的 `x11-wm` 分类目录里找到。

另外，KDE 和 GNOME 桌面环境都有他们自己的窗口管理器 与桌面集成。

每个窗口管理器也有不同的配置机制；有些需要手工来写配置文件， 而另外一些则可以使用 GUI 工具来完成大部分的配置任务， 举例而言， (Sawfish) 就使用 Lisp 语言书写配置文件。

### 焦点策略:

窗口管理器的另一个特性是鼠标的 “focus policy”。 每个窗口系统都需要有一个选择窗口的方法来接受键盘的输入信息，以及当前 哪个窗口处于可用状态。

您通常比较熟悉的是一个叫做 “click-to-focus” 的焦点策略。 这是 Microsoft® Windows® 使用的典型焦点策略，也就是您在一个窗口上点击 一下鼠标，这个窗口就处于当前可用的状态。

X 不支持一些特殊的焦点策略。确切地说，窗口管理器控制着在什么时候哪个窗口 拥有焦点。不同的窗口管理器支持不同的焦点方案。它们都支持点击即获得焦点， 而且它们中的大多数都支持好几种方案。

最流行的焦点策略：

focus-follows-mouse

鼠标指示器下面的窗口就是获得焦点的窗口。 这个窗口不一定位于其他所有窗口之上。 通过将鼠标移到另一个窗口就可以改变焦点， 而不需要在它上面点击。

sloppy-focus

这种方式是对 focus-follows-mouse 策略的一个小小扩展。对于 focus-follows-mouse， 如果您把鼠标移到了根窗口（或桌面背景）上， 则所有的其它窗口都会失去焦点， 而相关的全部键盘输入也会丢失。 如果选择了 sloppy-focus， 则只有当指针进入新窗口时， 窗口焦点才会发生变化， 而当退出当前窗口时是不会变化的。

click-to-focus

当前窗口由鼠标点击来选择。窗口被“突出显示” ， 出现在所有其他窗口的前面。即使指针被移向了另一个窗口，所有的键盘输入 仍会被这个窗口接收。

许多窗口管理器支持其他的策略，与这些相比又有些变化。您可以看具体 窗口管理器的文档。

### 6.2.4. 窗口部件

提供工具而非策略的 X 方法使得在每个应用程序屏幕上看到的窗口部件得到了 大大的扩展。

“Widget” 只是针对用户接口中所有列举项目的一个术语，它 可以用某种方法来点击或操作；如按钮，复选框，单选按钮，图标，列表框等等。 Microsoft® Windows® 把这些叫做“控件”。

Microsoft® Windows® 和苹果公司的 Mac OS® 都有一个严格的窗口部件策略。 应用程序开发者被建议确保他们的应用程序共享一个普通的所见即所得的用户界面。 对于 X，它并不要求一个特殊的图形风格或一套相结合的窗口部件集。

这样的结果是您不能期望 X 应用程序只拥有一个普通的所见即所得的界面。 有很多的流行的窗口部件集设置，包括来自于 MIT 的 Athena， Motif® (模仿 Microsoft® Windows® 的窗口风格， 所有部件都具有斜边和 3 种灰色度)， OpenLook， 等等。

如今， 绝大多数比较新的 X 应用程序采用一组新式的窗口设计， 这包括 KDE 所使用的 Qt， 以及 GNOME 所使用的 GTK+。 在这样一种窗口系统下，UNIX® 桌面的一些所见即所得特性作了一些收敛， 以使初学者感到更容易一些。

## 6.3. 安装 X11

Xorg 是 FreeBSD 上的默认 X11 实现。 Xorg 是由 X.Org 基金会发行的开放源代码 X Window 系统实现中的 X 服务。 Xorg 基于 XFree86™ 4.4RC2 和 X11R6.6 的代码。 从 FreeBSD Ports 套件可以安装 Xorg 的 7.7 版本。

如果需要从 Ports Collection 编译和安装 Xorg：

```
# `cd /usr/ports/x11/xorg`
# `make install clean`
```

### 注意:

要完整地编译 Xorg 则需要至少 4 GB 的剩余磁盘空间。

另外 X11 也可以直接从 package 来安装。 我们提供了可以与 [pkg_add(1)](http://www.FreeBSD.org/cgi/man.cgi?query=pkg_add&sektion=1&manpath=freebsd-release-ports) 工具配合使用的 X11 安装包。 如果从远程下载和安装， 在使用 [pkg_add(1)](http://www.FreeBSD.org/cgi/man.cgi?query=pkg_add&sektion=1&manpath=freebsd-release-ports) 时请不要指定版本号。 [pkg_add(1)](http://www.FreeBSD.org/cgi/man.cgi?query=pkg_add&sektion=1&manpath=freebsd-release-ports) 会自动地下载最新版本的安装包。

想要从 package 安装 Xorg， 简单地输入下面的命令：

```
# `pkg_add -r xorg`
```

### 注意:

上面的例子介绍了如何安装完整的 X11 软件包， 包括服务器端，客户端，字体等等。 此外， 也有一些单独的 X11 的 ports 和 packages.

另外， 如果需要最小化的 X11 软件， 您也可以安装 [x11/xorg-minimal](http://www.freebsd.org/cgi/url.cgi?ports/x11/xorg-minimal/pkg-descr)。

这一章余下的部分将会讲解如何配置 X11, 以及如何设置一个高效的桌面环境。

## 6.4. 配置 X11

Contributed by Christopher Shumway.

### 6.4.1. 开始之前

在配置 X11 之前， 您需要了解所安装的系统的下列信息：

*   显示器规格

*   显示卡的芯片类型

*   显示卡的显存容量

显示器的规格被 X11 用来决定显示的分辨率和刷新率。 这些规格通常可以从显示器所带的文档中， 以及制造商的网站找到。 需要知道两个数字范围： 垂直刷新率和水平刷新率。

显示卡的芯片类型将决定 X11 使用什么模块来驱动图形硬件。 尽管系统能自动检测出绝大多数的硬件， 但事先了解在自动检测出错的时候还是很有用处的。

显示卡的显存大小决定了系统支持的分辨率和颜色深度。 了解这些限制非常重要。

### 6.4.2. 配置 X11

对于 Xorg 7.3 这个版本， 可以不需要任何的配置文件就能运行，在提示符下键如下命令：

```
% `startx`
```

从 Xorg 7.4 开始， 可以使用 HAL 自动检测键盘和鼠标。Ports [sysutils/hal](http://www.freebsd.org/cgi/url.cgi?ports/sysutils/hal/pkg-descr) 和 [devel/dbus](http://www.freebsd.org/cgi/url.cgi?ports/devel/dbus/pkg-descr) 将被作为 [x11/xorg](http://www.freebsd.org/cgi/url.cgi?ports/x11/xorg/pkg-descr) 所依赖的包安装进系统。 并且需要在 `/etc/rc.conf` 文件中启用：

```
hald_enable="YES"
dbus_enable="YES"
```

在更深入的配置 Xorg 以前， 需要运行这些服务 (手工启动或者重启机器)。

自动配置对于某些硬件可能不起作用或者无法做到期望的配置。 在这种情况下就有必要做一些手工配置。

### 注意:

诸如 GNOME， KDE 或 Xfce 之类的桌面环境， 大多都提供了一些允许用户非常易用的工具， 来设置像分辨率这样的显示参数。 所以如果你觉得默认的配置并不适合， 而且你打算安装一个这样的桌面环境， 那么就请继续完成桌面环境的安装， 并使用适合的显示设置工具。

配置 X11 需要一些步骤。 第一步是以超级用户的身份建立初始的配置文件：

```
# `Xorg -configure`
```

这会在 `/root` 中生成一个叫做 `xorg.conf.new` 的配置文件 (无论您使用 [su(1)](http://www.FreeBSD.org/cgi/man.cgi?query=su&sektion=1&manpath=freebsd-release-ports) 或直接登录， 都会改变默认的 `$HOME` 目录变量)。 X11 程序将尝试探测系统中的图形硬件，并将探测到的硬件信息写入配置文件， 以便加载正确的驱动程序。

下一步是测试现存的配置文件， 以确认 Xorg 能够同系统上的图形设备正常工作。 对于 Xorg 7.3 或者之前的版本， 键入：

```
# `Xorg -config xorg.conf.new`
```

从 Xorg 7.4 和更高的版本开始， 这个测试将显示出一个黑色的屏幕，对于判断 X11 是否能正常工作会造成一些困扰。 可以通过 `retro` 选项使用旧的模式：

```
# `Xorg -config xorg.conf.new -retro`
```

如果看到黑灰的格子以及 X 型鼠标指针， 就表示配置成功了。 要退出测试， 需要同时按下 **Ctrl**+**Alt**+**F*`n`*** 来切换到用于启动 X 的虚拟控制台 (**F1** 表示第一个虚拟控制台) 之后按 **Ctrl**+**C**。

### 注意:

在 Xorg 7.3 以及更早期的版本中， 应使用 **Ctrl**+**Alt**+**Backspace** 组合键来强制退出 Xorg。 如果需要在 7.4 和之后的版本中启用这个组合键， 可以在任意 X 终端模拟器中输入下面的命令：

```
% `setxkbmap -option terminate:ctrl_alt_bksp`
```

或者为 hald 创建一个叫作 `x11-input.fdi` 的键盘配置文件并保存至 `/usr/local/etc/hal/fdi/policy` 目录。 这个文件需包含以下这些：

```
<?xml version="1.0" encoding="iso-8859-1"?>
<deviceinfo version="0.2">
  <device>
    <match key="info.capabilities" contains="input.keyboard">
	  <merge key="input.x11_options.XkbOptions" type="string">terminate:ctrl_alt_bksp</merge>
    </match>
  </device>
</deviceinfo>
```

你可能需要重启你的机器来使得 hald 重新读取这个文件。

此外， 还需要在 `xorg.conf.new` 中的 `ServerLayout` 或 `ServerFlags` 小节中添加：

```
Option	"DontZap"	"off"
```

如果鼠标无法正常工作， 在继续深入之前需要先配置它。 参阅 FreeBSD 安装一章中的 第 2.10.10 节 “配置鼠标”。 另外， 从 7.4 版本开始， `xorg.conf` 中的 `InputDevice` 部分将被忽略， 这有助于自动检测硬件设备。 可以在这个文件中的 `ServerLayout` 或者 `ServerFlags` 加入以下选项使用旧的模式：

```
Option "AutoAddDevices" "false"
```

输入设备连同其他需要的选项 (比如， 键盘布局切换) 就可以像在之前的版本中的那样配置了。

### 注意:

正如前面所提到的， 自版本 7.4 开始 hald 守护进程默认自动检测你的键盘。 可能检测出你的键盘布局或型号有差异， 在桌面环境中， 比如 GNOME， KDE 或者 Xfce 提供了工具来配置键盘。 另一方面， 也可在 [setxkbmap(1)](http://www.FreeBSD.org/cgi/man.cgi?query=setxkbmap&sektion=1&manpath=freebsd-release-ports) 工具的帮助下或者通过 hald 的配置文件来直接设置键盘的属性。

举例来说， 如果某人想要使用一个 PC 102 键法语布局的键盘， 我们就需要为 hald 创建一个配置文件， 叫作 `x11-input.fdi` 并保存入 `/usr/local/etc/hal/fdi/policy` 目录。 这个文件需要包含如下这些：

```
<?xml version="1.0" encoding="iso-8859-1"?>
<deviceinfo version="0.2">
  <device>
    <match key="info.capabilities" contains="input.keyboard">
	  <merge key="input.x11_options.XkbModel" type="string">pc102</merge>
	  <merge key="input.x11_options.XkbLayout" type="string">fr</merge>
    </match>
  </device>
</deviceinfo>
```

如果这个文件已经存在， 只要把键盘配置相关的部分拷贝加入即可。

你需要重启你的机器使 hald 读入此文件。

也可以在 X 模拟终端或一个脚本中使用以下的命令达到相同的效果:

```
% `setxkbmap -model pc102 -layout fr`
```

`/usr/local/share/X11/xkb/rules/base.lst` 列出了各种不同的键盘， 布局和可用的选项。

接下来是调整 `xorg.conf.new` 配置文件并作测试。 用文本编辑器如 [emacs(1)](http://www.FreeBSD.org/cgi/man.cgi?query=emacs&sektion=1&manpath=freebsd-release-ports) 或 [ee(1)](http://www.FreeBSD.org/cgi/man.cgi?query=ee&sektion=1&manpath=freebsd-release-ports) 打开这个文件。 要做的第一件事是为当前系统的显示器设置刷新率。 这些值包括垂直和水平的同步频率。 把它们加到 `xorg.conf.new` 的 `"Monitor"` 小节中：

```
Section "Monitor"
        Identifier   "Monitor0"
        VendorName   "Monitor Vendor"
        ModelName    "Monitor Model"
        HorizSync    30-107
        VertRefresh  48-120
EndSection
```

在配置文件中也有可能没有 `HorizSync` 和 `VertRefresh`。 如果是这样的话， 就只能手动添加， 并在 `HorizSync` 和 `VertRefresh` 后面设置合适的数值了。 在上面的例子中， 给出了相应的显示器的参数。

X 能够使用显示器所支持的 DPMS (能源之星) 功能。 [xset(1)](http://www.FreeBSD.org/cgi/man.cgi?query=xset&sektion=1&manpath=freebsd-release-ports) 程序可以控制超时时间， 并强制待机、挂起或关机。 如果希望启用显示器的 DPMS 功能， 则需要把下面的设置添加到 monitor 节中：

```
        Option       "DPMS"
```

关闭 `xorg.conf.new` 之前还应该选择默认的分辨率和色深。 这是在 `"Screen"` 小节中定义的：

```
Section "Screen"
        Identifier "Screen0"
        Device     "Card0"
        Monitor    "Monitor0"
        DefaultDepth 24
        SubSection "Display"
                Viewport  0 0
                Depth     24
                Modes     "1024x768"
        EndSubSection
EndSection
```

`DefaultDepth` 关键字描述了要运行的默认色深。 这可以通过 [Xorg(1)](http://www.FreeBSD.org/cgi/man.cgi?query=Xorg&sektion=1&manpath=freebsd-release-ports) 的 `-depth` 命令行开关来替代配置文件中的设置。 `Modes` 关键字描述了给定颜色深度下屏幕的分辨率。 需要说明的是， 目标系统的图形硬件只支持由 VESA 定义的标准模式。 前面的例子中， 默认色深是使用 24 位色。 在采用这个色深时， 允许的分辨率是 1024x768。

最后就是将配置文件存盘， 并使用前面介绍的测试模式测试一下。

### 注意:

在发现并解决问题的过程中， 包含了与 X11 服务器相关的各个设备的信息的 X11 日志文件会为您发现和排除问题有所帮助。 Xorg 日志的文件名是 `/var/log/Xorg.0.log` 这样的格式。 实际的日志文件名可能是 `Xorg.0.log` 到 `Xorg.8.log` 等等。

如果一切准备妥当， 就可以把配置文件放到公共的目录中了。 您可以在 [Xorg(1)](http://www.FreeBSD.org/cgi/man.cgi?query=Xorg&sektion=1&manpath=freebsd-release-ports) 里面找到具体位置。 这个位置通常是 `/etc/X11/xorg.conf` 或 `/usr/local/etc/X11/xorg.conf`。

```
# `cp xorg.conf.new /etc/X11/xorg.conf`
```

现在已经完成了 X11 的配置全过程。 Xorg 可以通过 [startx(1)](http://www.FreeBSD.org/cgi/man.cgi?query=startx&sektion=1&manpath=freebsd-release-ports) 工具来启动。 除此之外， X11 服务器也可以用 [xdm(1)](http://www.FreeBSD.org/cgi/man.cgi?query=xdm&sektion=1&manpath=freebsd-release-ports) 来启动。

### 6.4.3. 高级配置主题

#### 6.4.3.1. 配置 Intel® i810 显示芯片组

配置 Intel i810 芯片组的显示卡需要有针对 X11 的能够用来驱动显示卡的 `agpgart` AGP 程序接口。 请参见 [agp(4)](http://www.FreeBSD.org/cgi/man.cgi?query=agp&sektion=4&manpath=freebsd-release-ports) 驱动程序的联机手册了解更多细节。

这也适用于其他的图形卡硬件配置。 注意如果系统没有将 [agp(4)](http://www.FreeBSD.org/cgi/man.cgi?query=agp&sektion=4&manpath=freebsd-release-ports) 驱动程序编译进内核，尝试用 [kldload(8)](http://www.FreeBSD.org/cgi/man.cgi?query=kldload&sektion=8&manpath=freebsd-release-ports) 加载模块是无效的。 这个驱动程序必须编译进内核或者使用 `/boot/loader.conf` 在启动时加载进入内核。

#### 6.4.3.2. 添加宽屏平板显示器

这一节假定您了解一些关于高级配置的知识。 如果使用前面的标准配置工具不能产生可用的配置， 则在日志文件中提供的信息应该足以修正配置使其正确工作。 如果需要的话， 您应使用一个文本编辑器来完成这项工作。

目前的宽屏 (WSXGA、 WSXGA+、 WUXGA、 WXGA、 WXGA+， 等等) 支持 16:10 和 10:9 或一些支持不大好的显示比例。 常见的一些 16:10 比例的分辨率包括：

*   2560x1600

*   1920x1200

*   1680x1050

*   1440x900

*   1280x800

有时， 也可以简单地把这些分辨率作为 `Section "Screen"` 中的 `Mode` 来进行配置， 类似下面这样：

```
Section "Screen"
Identifier "Screen0"
Device     "Card0"
Monitor    "Monitor0"
DefaultDepth 24
SubSection "Display"
	Viewport  0 0
	Depth     24
	Modes     "1680x1050"
EndSubSection
EndSection
```

Xorg 能够自动地通过 I2C/DDC 信息来自动获取宽屏显示器的分辨率信息， 并处理显示器支持的频率和分辨率。

如果驱动程序没有对应的 `ModeLines`， 就需要给 Xorg 一些提示了。 使用 `/var/log/Xorg.0.log` 能够提取足够的信息， 就可以写一个可用的 `ModeLine` 了。 这类信息如下所示：

```
(II) MGA(0): Supported additional Video Mode:
(II) MGA(0): clock: 146.2 MHz   Image Size:  433 x 271 mm
(II) MGA(0): h_active: 1680  h_sync: 1784  h_sync_end 1960 h_blank_end 2240 h_border: 0
(II) MGA(0): v_active: 1050  v_sync: 1053  v_sync_end 1059 v_blanking: 1089 v_border: 0
(II) MGA(0): Ranges: V min: 48  V max: 85 Hz, H min: 30  H max: 94 kHz, PixClock max 170 MHz
```

这些信息称做 EDID 信息。 从中建立 `ModeLine` 只是把这些数据重新排列顺序而已：

```
ModeLine <name> <clock> <4 horiz. timings> <4 vert. timings>
```

如此， 本例中的 `Section "Monitor"` 中的 `ModeLine` 应类似下面的形式：

```
Section "Monitor"
Identifier      "Monitor1"
VendorName      "Bigname"
ModelName       "BestModel"
ModeLine        "1680x1050" 146.2 1680 1784 1960 2240 1050 1053 1059 1089
Option          "DPMS"
EndSection
```

经过简单的编辑步骤之后， X 就可以在您的宽屏显示器上启动了。

## 6.5. 在 X11 中使用字体

供稿 Murray Stokely.

### 6.5.1. Type1 字体

X11 使用的默认字体不是很理想。 大型的字体显得参差不齐，看起来很不专业， 并且， 在 Netscape® 中， 小字体简直无法看清。 有好几种免费、 高质量的字体可以很方便地用在 X11 中。 例如，URW 字体集合 ([x11-fonts/urwfonts](http://www.freebsd.org/cgi/url.cgi?ports/x11-fonts/urwfonts/pkg-descr)) 就包括了高质量的 标准 type1 字体 (Times Roman®, Helvetica®、 Palatino® 和其他一些)。 在 Freefont 集合中 ([x11-fonts/freefonts](http://www.freebsd.org/cgi/url.cgi?ports/x11-fonts/freefonts/pkg-descr)) 也包括更多的字体， 但它们中的绝大部分使用在图形软件中，如 Gimp，在屏幕字体中使用并不完美。另外， 只要花很少的功夫，可以将 XFree86™ 配置成能使用 TrueType® 字体：请参见后面的 TrueType® 字体一节。

如果希望使用 Ports Collection 来安装上面的 Type1 字体， 只需运行下面的命令：

```
# `cd /usr/ports/x11-fonts/urwfonts`
# `make install clean`
```

freefont 或其他的字库和上面所说的大体类似。 为了让 X 服务器能够检测到这些字体， 需要在 X 服务器的配置文件 (`/etc/X11/xorg.conf`) 中增加下面的配置：

```
FontPath "/usr/local/lib/X11/fonts/URW/"
```

或者，也可以在命令行运行：

```
% `xset fp+ /usr/local/lib/X11/fonts/URW`
% `xset fp rehash`
```

这样会起作用，但是当 X 会话结束后就会丢失， 除非它被添加到启动文件 (`~/.xinitrc` 中， 针对一个寻常的 `startx` 会话，或者当您通过一个类似 XDM 的图形登录管理器登录时添加到 `~/.xsession` 中)。 第三种方法是使用新的 `/usr/local/etc/fonts/local.conf` 文件： 查看 anti-aliasing 章节。

### 6.5.2. TrueType® 字体

Xorg 已经内建了对 TrueType® 字体的支持。有两个不同的模块能够启用这个功能。 在这个例子中使用 freetype 这个模块，因为它与其他的字体描绘后端 是兼容的。要启用 freetype 模块，只需要将下面这行添加到 `/etc/X11/xorg.conf` 文件的 `"Module"` 部分。

```
Load "freetype"
```

现在，为 TrueType® 字体创建一个目录 (比如， `/usr/local/lib/X11/fonts/TrueType`) 然后把所有的 TrueType® 字体复制到这个目录。记住您不能直接从 Macintosh® 计算机中提取 TrueType® 字体； 能被 X11 使用的必须是 UNIX®/MS-DOS®/Windows® 格式的。 一旦您已经将这些文件复制到了这个目录， 就可以用 ttmkfdir 来创建 `fonts.dir` 文件， 以便让 X 字体引擎知道您已经安装了这些新文件。 `ttmkfdir` 可以在 FreeBSD Ports 套件中的 [x11-fonts/ttmkfdir](http://www.freebsd.org/cgi/url.cgi?ports/x11-fonts/ttmkfdir/pkg-descr) 中找到。

```
# `cd /usr/local/lib/X11/fonts/TrueType`
# `ttmkfdir -o fonts.dir`
```

现在把 TrueType® 字体目录添加到字体路径中。 这和上面 Type1 字体的步骤是一样的， 那就是，使用

```
% `xset fp+ /usr/local/lib/X11/fonts/TrueType`
% `xset fp rehash`
```

或者把 `FontPath` 这行加到 `xorg.conf` 文件中。

就是这样。现在 Netscape®, Gimp, StarOffice™ 和其他所有的 X 应用程序 应该可以认出安装的 TrueType® 字体。一些很小的字体(如在 Web 页面上高分辨率显示的文本) 和一些很大的字体(在 StarOffice™ 下) 现在看起来已经很好了。

### 6.5.3. Anti-Aliased 字体

Updated by Joe Marcus Clarke.

对于所有支持 Xft 的应用程序， 所有放到 X11 `/usr/local/lib/X11/fonts/` 和 `~/.fonts/` 中的字体都自动地被加入反走样支持。 绝大多数较新的程序都提供了 Xft 支持， 包括 KDE、 GNOME 以及 Firefox。

要控制哪些字体是 anti-aliased，或者配置 anti-aliased 特性， 创建(或者编辑，如果文件已经存在的话)文件 `/usr/local/etc/fonts/local.conf`。Xft 字体系统的几个 高级特性都可以使用这个文件来调节； 这一部分只描述几种最简单的情况。要了解更多的细节，请查看 [fonts-conf(5)](http://www.FreeBSD.org/cgi/man.cgi?query=fonts-conf&sektion=5&manpath=freebsd-release-ports).

这个文件一定是 XML 格式的。注意确保所有的标签都完全的关闭掉。 这个文件以一个很普通的 XML 头开始， 后跟一个 DOCTYPE 定义， 接下来是 `<fontconfig>` 标签：

```
      <?xml version="1.0"?>
      <!DOCTYPE fontconfig SYSTEM "fonts.dtd">
      <fontconfig>

```

像前面所做的那样，在 `/usr/local/lib/X11/fonts/` 和 `~/.fonts/` 目录下的所有字体已经可以被支持 Xft 的 应用程序使用了。如果您想添加这两个目录以外的其他路径， 简单的添加下面这行到 `/usr/local/etc/fonts/local.conf`文件中：

```
<dir>/path/to/my/fonts</dir>
```

添加了新的字体，尤其是添加了新的字体目录后， 您应该运行下面的命令重建字体缓存：

```
# `fc-cache -f`
```

Anti-aliasing 会让字体边缘有些模糊，这样增加了非常小的文本的可读性， 并从大文本字体中删除 “锯齿”。 但如果使用普通的文本， 则可能引起眼疲劳。 要禁止 14 磅 以下字体的反走样， 需要增加如下配置：

```
        <match target="font">
            <test name="size" compare="less">
                <double>14</double>
            </test>
            <edit name="antialias" mode="assign">
                <bool>false</bool>
            </edit>
        </match>
        <match target="font">
            <test name="pixelsize" compare="less" qual="any">
                <double>14</double>
            </test>
            <edit mode="assign" name="antialias">
                <bool>false</bool>
            </edit>
        </match>
```

用 anti-aliasing 来间隔一些等宽字体也是不适当的。 这似乎是 KDE 的一个问题。 要修复这个问题需要确保每个字体之间的间距保持在 100。 加入下面这些行：

```
       <match target="pattern" name="family">
           <test qual="any" name="family">
               <string>fixed</string>
           </test>
           <edit name="family" mode="assign">
               <string>mono</string>
           </edit>
        </match>
        <match target="pattern" name="family">
            <test qual="any" name="family">
                <string>console</string>
            </test>
            <edit name="family" mode="assign">
                <string>mono</string>
            </edit>
        </match>
```

(这里把其他普通的修复的字体作为 `"mono"`)，然后加入：

```
         <match target="pattern" name="family">
             <test qual="any" name="family">
                 <string>mono</string>
             </test>
             <edit name="spacing" mode="assign">
                 <int>100</int>
             </edit>
         </match>      
```

某些字体，比如 Helvetica，当 anti-aliased 的时候可能存在问题。 通常的表现为字体本身似乎被垂直的切成两半。 糟糕的时候，还可能导致应用程序崩溃。 为了避免这样的现象，考虑添加下面几行到 `local.conf`文件里面：

```
         <match target="pattern" name="family">
             <test qual="any" name="family">
                 <string>Helvetica</string>
             </test>
             <edit name="family" mode="assign">
                 <string>sans-serif</string>
             </edit>
         </match>        
```

一旦您完成对 `local.conf` 文件的编辑，确保您使用了 `</fontconfig>` 标签来结束文件。 不这样做将会导致您的更改被忽略。

最后，用户可以通过他们个人的 `.fonts.conf` 文件来添加自己的设定。 要完成此项工作， 用户只需简单地创建 `~/.fonts.conf` 并添加相关配置。 此文件也必须是 XML 格式的。

最后：对于 LCD 屏幕， 可能希望使用子像素的取样。 简单而言， 这是通过分别控制 (水平方向分开的) 红、绿、蓝 像素， 来改善水平分辨率； 这样做的效果一般会非常明显。 要启用它， 只需在 `local.conf` 文件的某个地方加入：

```
         <match target="font">
             <test qual="all" name="rgba">
                 <const>unknown</const>
             </test>
             <edit name="rgba" mode="assign">
                 <const>rgb</const>
             </edit>
         </match>

```

### 注意:

随您显示器的种类不同， 可能需要把 `rgb` 改为 `bgr`、 `vrgb` 或 `vbgr`： 试验一下看看那个更好。

## 6.6. X 显示管理器

Contributed by Seth Kingsley.

### 6.6.1. 概要

X 显示管理器(XDM) 是一个 X 视窗系统用于进行登录会话管理的可选项。 这个可以应用于多种情况下，包括小 “X Terminals”， 桌面，大网络显示服务器。既然 X 视窗系统不受网络和协议的限制， 那对于通过网络连接起来的运行 X 客户端和服务器端的不同机器， 就会有很多的可配置项。 XDM 提供了一个选择要连接到哪个显示服务器的图形接口， 只要键入如登录用户名和密码这样的验证信息。

您也可以把 XDM 想象成与 [getty(8)](http://www.FreeBSD.org/cgi/man.cgi?query=getty&sektion=8&manpath=freebsd-release-ports) 工具一样(see 第 27.3.2 节 “配置” for details)。为用户提供了同样功能。它可以完成系统的登录任务， 然后为用户运行一个会话管理器 (通常是一个 X 视窗管理器)。接下来 XDM 就等待这个程序退出，发出信号用户已经登录完成，应当退出屏幕。 这时， XDM 就可以为下一个登录用户显示登录和可选择屏幕。

### 6.6.2. 使用 XDM

如果希望使用 XDM 来启动， 首先需要安装 [x11/xdm](http://www.freebsd.org/cgi/url.cgi?ports/x11/xdm/pkg-descr) port (在较新版本的 Xorg 中它并不是默认安装的)。 XDM 服务程序位于 `/usr/local/bin/xdm`。 任何时候都可以 `root` 用户的身份来运行它， 以令其管理本地系统的 X 显示。 如果希望让 XDM 在系统每次启动过程中自动运行， 比较方便的做法是把它写到 `/etc/ttys` 的配置中。 有关这个文件的具体格式和使用方法请参阅 第 27.3.2.1 节 “添加一个记录到`/etc/ttys`”。 在默认的 `/etc/ttys` 文件中已经包含了在虚拟终端上运行 XDM 服务的示范配置：

```
ttyv8   "/usr/local/bin/xdm -nodaemon"  xterm   off secure
```

默认情况下，这个记录是关闭的，要启用它， 您需要把第 5 部分的 `off` 改为 `on` 然后按照 第 27.3.2.2 节 “重新读取`/etc/ttys`来强制`init` ” 的指导 重新启动 [init(8)](http://www.FreeBSD.org/cgi/man.cgi?query=init&sektion=8&manpath=freebsd-release-ports)。第一部分，这个程序将管理的终端名称是 `ttyv8`。这意味着 XDM 将运行在第 9 个虚拟终端上。

### 6.6.3. 配置 XDM

XDM 的配置目录是在 `/usr/local/lib/X11/xdm`中。在这个目录中， 您会看到几个用来改变 XDM 行为和外观的文件。您会找到这些文件：

| 文件 | 描述 |
| --- | --- |
| `Xaccess` | 客户端授权规则。 |
| `Xresources` | 默认的 X 资源值。 |
| `Xservers` | 远程和本地显示管理列表。 |
| `Xsession` | 用于登录的默认的会话脚本。 |
| `Xsetup_`* | 登录之前用于加载应用程序的脚本。 |
| `xdm-config` | 运行在这台机器上的所有显示的全局配置。 |
| `xdm-errors` | 服务器程序产生的错误。 |
| `xdm-pid` | 当前运行的 XDM 的进程 ID。 |

当 XDM 运行时， 在这个目录中有几个脚本和程序可以用来设置桌面。 这些文件中的每一个的用法都将被简要地描述。 这些文件的更详细的语法和用法在 [xdm(1)](http://www.FreeBSD.org/cgi/man.cgi?query=xdm&sektion=1&manpath=freebsd-release-ports) 中将有详细描述。

默认的配置是一个矩形的登录窗口，上面有机器的名称， “Login:” 和 “Password:”。如果您想设计您自己个性化的 XDM 屏幕，这是一个很好的起点。

#### 6.6.3.1. Xaccess

用以连接由 XDM 所控制的显示设备的协议， 叫做 X 显示管理器连接协议 (XDMCP)。 这个文件是一组用以控制来自远程计算机的 XDMCP 连接的规则。 除非您修改 `xdm-config` 使其接受远程连接， 否则其内容将被忽略。 默认情况下， 它不允许来自任何客户端的连接。

#### 6.6.3.2. Xresources

这是一个默认的用来显示选项和登录屏幕的应用程序文件。 您可以在这个文件中对登录程序的外观进行定制。 其格式与 X11 文档中描述的默认应用程序文件是一样的。

#### 6.6.3.3. Xservers

这是一个选择者应当提供的作为可选的远程显示列表。

#### 6.6.3.4. Xsession

这是一个用户登录后针对 XDM 的默认会话脚本。通常，在 `~/.xsession` 中每个用户将有一个可定制的会话脚本。

#### 6.6.3.5. Xsetup_*

在显示选择者或登录接口之前，这些将被自动运行。 这是一个每个显示都要用到的脚本，叫做 `Xsetup_`， 后面会跟一个本地显示的数字(比如 `Xsetup_0`)。典型的，这些脚本将在后台 (如 `xconsole`)运行一个或两个程序。

#### 6.6.3.6. xdm-config

此文件以应用程序默认值的形式， 提供了在安装时所使用的普适的显示设置。

#### 6.6.3.7. xdm-errors

这个文件包含了 XDM 正设法运行的的 X 服务器 的输出。 如果 XDM 正设法运行的显示由于某种原因被挂起， 那这是一个寻找错误信息的好地方。 这些信息会在每一个会话的基础上被写到用户的 `~/.xsession-errors` 文件中。

### 6.6.4. 运行一个网络显示服务器

对于其他客户端来说， 如果希望它们能连接到显示服务器，您就必须编辑访问控制规则， 并启用连接侦听。 默认情况下， 这些都预设为比较保守的值。 要让 XDM 能侦听连接， 首先要在 `xdm-config` 文件中注释掉一行：

```
! SECURITY: do not listen for XDMCP or Chooser requests ! Comment out this line if you want to manage X terminals with xdm
DisplayManager.requestPort:     0
```

然后重新启动 XDM。 记住默认应用程序文件的注释以“!” 字母开始，不是“#”。 您需要设置严格的访问控制 ── 看看在 `Xaccess` 文件中的实例， 并参考 [xdm(1)](http://www.FreeBSD.org/cgi/man.cgi?query=xdm&sektion=1&manpath=freebsd-release-ports) 的联机手册， 以了解进一步的细节。

### 6.6.5. 替换 XDM

有几个替换默认 XDM 程序的方案。 其中之一是 上一节已经描述过的 kdm (与 KDE 捆绑在一起)。 kdm 提供了许多视觉上的改进和局部的修饰， 同样能让用户在启动时能选择他们喜欢的窗口管理器。

## 6.7. 桌面环境

Contributed by Valentino Vaschetto.

这节描述了 FreeBSD 上用于 X 的不同桌面环境。 “桌面环境” 可能仅仅是一个简单的窗口管理器， 也可能是一个像 KDE 或者 GNOME 这样的完整桌面应用程序套件。

### 6.7.1. GNOME

#### 6.7.1.1. 有关 GNOME

GNOME 是一个用户界面友好的桌面环境， 能够使用户很容易地使用和配置他们的计算机。 GNOME 包括一个面板(用来启动应用程序和显示状态)， 一个桌面(存放数据和应用程序的地方)， 一套标准的桌面工具和应用程序， 和一套与其他人相互协同工作的协议集。 其他操作系统的用户在使用 GNOME 提供的强大的图形驱动环境时会觉得很好。 更多的关于 FreeBSD 上 GNOME 的信息 可以在 [FreeBSD GNOME Project](http://www.FreeBSD.org/gnome) 的网站上找到。 此外， 这个网站也提供了相当详尽的关于安装、 配置和管理 GNOME 的常见问题解答 (FAQ)。

#### 6.7.1.2. 安装 GNOME

这个软件可以很容易地通过预编译包或 Ports 套件来安装：

要从网络安装 GNOME， 只要键入：

```
# `pkg_add -r gnome2`
```

从源代码编译 GNOME，可以使用 ports 树：

```
# `cd /usr/ports/x11/gnome2`
# `make install clean`
```

GNOME 需要挂载 `/proc` 文件系统才能正常运作。添加如下

```
proc           /proc       procfs  rw  0   0
```

到 `/etc/fstab` 以便在系统启动时自动挂载 [procfs(5)](http://www.FreeBSD.org/cgi/man.cgi?query=procfs&sektion=5&manpath=freebsd-release-ports)。

一旦装好了 GNOME， 就必须告诉 X server 启动 GNOME 而不是默认的窗口管理器。

最简单的启动 GNOME 的方法是使用 GDM， GNOME 显示管理器。 随 GNOME 桌面一同安装的 GDM 尽管默认是禁用的。 可以在 `/etc/rc.conf` 中加入以下这行启用：

```
gdm_enable="YES"
```

这样在你重启机器的时候， GDM 将自动运行。

通常我们希望在 GDM 启动时， 同时启用所有的 GNOME 服务， 可以将如下这行加入 `/etc/rc.conf`：

```
gnome_enable="YES"
```

GNOME 也可以通过适当地配置名为 `.xinitrc` 的文件来启动。 如果已经有了自定义的 `.xinitrc`， 将启动当前窗口管理器的那一行改为启动 /usr/local/bin/gnome-session 就可以了。 如果还没有， 那么只需简单地：

```
% `echo "/usr/local/bin/gnome-session" > ~/.xinitrc`
```

接下来输入 `startx`， GNOME 桌面环境就启动了。

### 注意:

如果之前使用了一些旧式的显示管理器， 例如 XDM， 则这样做是没用的。 此时应建立一个可执行的 `.xsession` 文件， 其中包含同样的命令。 要完成这项工作， 需要用 /usr/local/bin/gnome-session 取代现有的窗口管理器：

```
% `echo "#!/bin/sh" > ~/.xsession`
% `echo "/usr/local/bin/gnome-session" >> ~/.xsession`
% `chmod +x ~/.xsession`
```

还有一种做法， 是配置显示管理器， 以便在登录时提示您选择窗口管理器； 在 KDE 细节 环节中介绍了关于如何为 kdm （KDE 的显示管理器）进行这样的配置。

### 6.7.2. KDE

#### 6.7.2.1. 有关 KDE

KDE 是一个容易使用的现代桌面环境。 KDE 有很多很好的特性：

*   一个美丽的现代的桌面。

*   一个集合了完美网络环境的桌面。

*   一个集成的帮助系统，能够方便、高效地帮助您使用 KDE 桌面和它的应用程序。

*   所有的 KDE 应用程序具有一致的所见即所得界面。

*   标准的菜单和工具栏，键盘布局，颜色配置等。

*   国际化：KDE 可以使用超过 40 种语言。

*   集中化、 统一的对话框驱动的桌面配置

*   许多有用的 KDE 应用程序。

KDE 附带了一个名为 Konqueror 的 web 浏览器， 它是其他运行于 UNIX® 系统上的 web 浏览器的一个强大的竞争对手。 要了解关于 KDE 的更多详情， 可以访问 [KDE 网站](http://www.kde.org/)。 与 FreeBSD 相关的 KDE 信息和资源， 可以在 [FreeBSD 上的 KDE 团队](http://freebsd.kde.org/) 的网站找到。

FreeBSD 上提供了两种版本的 KDE。 版本 3 已经推出了很长时间， 十分成熟。 而版本 4， 也就是下一代版本， 也可以通过 Ports 套件来安装。 这两种版本甚至能够并存。

#### 6.7.2.2. 安装 KDE

与 GNOME 和其他桌面环境类似， 这个软件可以很容易地通过预编译包或 Ports 套件来安装：

要从网络安装 KDE3 只需要：

```
# `pkg_add -r kde`
```

要从网络安装 KDE4 则需要：

```
# `pkg_add -r kde4`
```

[pkg_add(1)](http://www.FreeBSD.org/cgi/man.cgi?query=pkg_add&sektion=1&manpath=freebsd-release-ports) 就会自动的下载最新版本的应用程序。

要从源代码编译 KDE3， 可以使用 ports 树：

```
# `cd /usr/ports/x11/kde3`
# `make install clean`
```

而从 ports 提供的源代码编译 KDE4， 对应的操作则是：

```
# `cd /usr/ports/x11/kde4`
# `make install clean`
```

安装好 KDE 之后， 还需要告诉 X server 启动这个应用程序来代替默认的窗口管理器。 这可以通过编辑 `.xinitrc` 文件来完成：

对于 KDE3：

```
% `echo "exec startkde" > ~/.xinitrc`
```

对于 KDE4：

```
% `echo "exec /usr/local/kde4/bin/startkde" > ~/.xinitrc`
```

现在，无论您什么时候用 `startx`进入 X 视窗系统， KDE 就将成为您的桌面环境。

如果使用一个像 XDM 这样的显示管理器， 那配置文件可能有点不同。需要编辑一个 `.xsession` 文件，有关 kdm 的用法会在这章的后面介绍。

### 6.7.3. 有关 KDE 的更多细节

现在 KDE 已经被安装在系统中了。 通过帮助页面或点击多个菜单可以发现很多东西。 Windows® 或 Mac® 用户会有回到家的感觉。

有关 KDE 的最好的参考资料是 它的在线文档。KDE 拥有它自己的 web 浏览器 Konqueror， 还有很多其他的应用程序和丰富文档。 这节的余下部分将讨论一些很难用走马观花的方法来学习的技术项目。

#### 6.7.3.1. KDE 显示管理器

如果在同一系统上有多个用户， 则管理员通常会希望使用图形化的登录界面。 前面已经提到， 使用 XDM 可以完成这项工作。 不过， KDE 本身也提供了另一个选择， 即 kdm， 它的外观更富吸引力， 而且提供了更多的登录选项。 值得一提的是， 用户还能通过菜单很容易地选择希望使用的桌面环境 (KDE、 GNOME 或其它)。

要启用 kdm， 需要根据 KDE 的版本修改不同的配置文件。

对于 KDE3， `/etc/ttys` 中的 `ttyv8` 项需被改写成如下的形式：

```
ttyv8 "/usr/local/bin/kdm -nodaemon" xterm on secure
```

对于 KDE4， 你需要将如下这行加入 `/etc/rc.conf`：

```
local_startup="${local_startup} /usr/local/kde4/etc/rc.d"
kdm4_enable="YES"
```

### 6.7.4. Xfce

#### 6.7.4.1. 有关 Xfce

Xfce 是以被 GNOME 使用的 GTK+ 工具包为基础的桌面环境， 但是更加轻巧，适合于那些需要一个易于使用和配置并且简单而高效的桌面的人。 看起来，它非常像使用在商业 UNIX®系统上的 CDE 环境。 Xfce 的主要特性有下面这些：

*   一个简单，易于使用的桌面。

*   完全通过鼠标的拖动和按键来控制等。

*   与 CDE 相似的主面板，菜单，applets 和应用 launchers。

*   集成的窗口管理器，文件管理器，声音管理器， GNOME 应用模块等等。

*   可配置界面的主题。(因为它使用 GTK+)

*   快速，轻便，高效：对于比较老的/旧的机器或带有很少内存的机器仍然很理想。

更多有关 Xfce 的信息可以参考[Xfce 网站](http://www.xfce.org/)。

#### 6.7.4.2. 安装 Xfce

有一个二进制的 Xfce 软件包存在(在写作的时候)。要安装的话，执行下面的命令：

```
# `pkg_add -r xfce4`
```

另外， 也可以使用 Ports Collection 从源代码联编：

```
# `cd /usr/ports/x11-wm/xfce4`
# `make install clean`
```

现在，要告诉 X 服务器在下次 X 启动时执行 Xfce。 只要执行下面的命令：

```
% `echo "/usr/local/bin/startxfce4" > ~/.xinitrc`
```

接下来就是启动 X， Xfce 将成为您的桌面。 与以前一样，如果使用像 `XDM` 这样的显示管理器，需要创建一个 `.xsession`文件，就像有关 GNOME 的那节描述的， 使用`/usr/local/bin/startxfce4` 命令，或者，配置显示管理器允许在启动时选择一个桌面， 就像有关 kdm 的那节描述的。`