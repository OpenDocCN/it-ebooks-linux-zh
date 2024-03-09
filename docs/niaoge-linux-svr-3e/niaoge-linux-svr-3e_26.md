# 第二十一章、文件服务器之三： FTP 服务器

最近更新日期：2011/08/08

FTP (File Transfer Protocol) 可说是最古老的协议之一了，主要是用来进行档案的传输，尤其是大型档案的传输使用 FTP 更是方便！不过，值得注意的是，使用 FTP 来传输时，其实是具有一定程度的『危险性』， 因为数据在因特网上面是完全没有受到保护的『明码』传输方式！但是单纯的 FTP 服务还是有其必要性的，例如很多学校就有 FTP 服务器的架设需求啊！

*   21.1 FTP 的数据链路原理
    *   21.1.1 FTP 功能简介
    *   21.1.2 FTP 的运作流程与使用到的端口
    *   21.1.3 客户端选择被动式联机模式
    *   21.1.4 FTP 的安全性问题与替代方案
    *   21.1.5 开放什么身份的使用者登入
*   21.2 vsftpd 服务器基础设定
    *   21.2.1 为何使用 vsftpd
    *   21.2.2 所需要的软件以及软件结构
    *   21.2.3 vsftpd.conf 设定值说明
    *   21.2.4 vsftpd 启动的模式
    *   21.2.5 CentOS 的 vsftpd 默认值： 使用本地端时间
    *   21.2.6 针对实体账号的设定： SELinux, chroot, 限制带宽, 最大上线人数, 可用账号列表
    *   21.2.7 仅有匿名登录的相关设定： 匿名的根, 可上传下载, 仅可上传, 被动式联机埠口
    *   21.2.8 防火墙设定
    *   21.2.9 常见问题与解决之道
*   21.3 客户端的图形接口 FTP 联机软件
    *   21.3.1 Filezilla
    *   21.3.2 透过浏览器取得 FTP 联机
*   21.4 让 vsftpd 增加 SSL 的加密功能
*   21.5 重点回顾
*   21.6 本章习题
*   21.7 参考数据与延伸阅读
*   21.8 [针对本文的建议：http://phorum.vbird.org/viewtopic.php?p=118520](http://phorum.vbird.org/viewtopic.php?p=118520)

* * *

# 21.1 FTP 的数据链路原理

## 21.1 FTP 的数据链路原理

FTP (File transfer protocol) 是相当古老的传输协议之一，他最主要的功能是在服务器与客户端之间进行档案的传输。 这个古老的协议使用的是明码传输方式，且过去有相当多的安全危机历史。为了更安全的使用 FTP 协议，我们主要介绍较为安全但功能较少的 vsftpd 这个软件吶。

* * *

### 2.1.1 FTP 功能简介

FTP 服务器的功能除了单纯的进行档案的传输与管理之外，依据服务器软件的设定架构，它还可以提供几个主要的功能。 底下我们约略的来谈一谈：

*   不同等级的用户身份：user, guest, anonymous

FTP 服务器在预设的情况下，依据使用者登入的情况而分为三种不同的身份，分别是： (1)实体账号,real user；(2)访客, guest；(3)匿名登录者, anonymous 这三种。这三种身份的用户在系统上面的权限差异很大喔！例如实体用户取得系统的权限比较完整， 所以可以进行比较多的动作；至于匿名登录者，大概我们就仅提供他下载资源的能力而已，并不许匿名者使用太多主机的资源啊！ 当然，这三种人物能够使用的『在线指令』自然也就不相同啰！ ^_^

*   命令记录与登录文件记录：

FTP 可以利用系统的 [syslogd](http://linux.vbird.org/linux_basic/0570syslog.php) 来进行数据的纪录， 而记录的数据报括了用户曾经下达过的命令与用户传输数据(传输时间、档案大小等等)的纪录呢！ 所以你可以很轻松的在 /var/log/ 里面找到各项登录信息喔！

*   限制用户活动的目录： (change root, 简称 chroot)

为了避免用户在你的 Linux 系统当中随意逛大街 (意指离开用户自己的家目录而进入到 Linux 系统的其他目录去)， 所以将使用者的工作范围『局限』在用户的家目录底下，嗯！实在是个不错的好主意！FTP 可以限制用户仅能在自己的家目录当中活动喔！如此一来，由于使用者无法离开自己的家目录，而且登入 FTP 后，显示的『根目录』就是自己家目录的内容，这种环境称之为 change root ，简称 chroot ，改变根目录的意思啦！

这有什么好处呢？当一个恶意的使用者以 FTP 登入你的系统当中，如果没有 chroot 的环境下，他可以到 /etc, /usr/local, /home 等其他重要目录底下去察看档案数据，尤其是很重要的 /etc/ 底下的配置文件，如 /etc/passwd 等等。如果你没有做好一些档案权限的管理与保护，那他就有办法取得系统的某些重要信息， 用来『入侵』你的系统呢！所以在 chroot 的环境下，当然就比较安全一些咯！

* * *

### 21.1.2 FTP 的运作流程与使用到的端口

FTP 的传输使用的是 TCP 封包协议，在[第二章网络基础](http://linux.vbird.org/linux_server/0110network_basic.php)中我们谈过， TCP 在建立联机前会先进行三向交握。不过 FTP 服务器是比较麻烦一些，因为 FTP 服务器使用了两个联机，分别是命令信道与数据流通道 (ftp-data) 。这两个联机都需要经过三向交握， 因为是 TCP 封包嘛！那么这两个联机通道的关系是如何呢？底下我们先以 FTP 预设的主动式 (active) 联机来作个简略的说明啰：

![](img/connect_active.gif) 图 21.1-1、FTP 服务器的主动式联机示意图

简单的联机流程就如上图所示，至于联机的步骤是这样的：

1.  建立命令通道的联机 如上图所示，客户端会随机取一个大于 1024 以上的埠口 (port AA) 来与 FTP 服务器端的 port 21 达成联机， 这个过程当然需要三向交握了！达成联机后客户端便可以透过这个联机来对 FTP 服务器下达指令， 包括查询档名、下载、上传等等指令都是利用这个通道来下达的；

2.  通知 FTP 服务器端使用 active 且告知连接的埠号 FTP 服务器的 21 埠号主要用在命令的下达，但是当牵涉到数据流时，就不是使用这个联机了。 客户端在需要数据的情况下，会告知服务器端要用什么方式来联机，如果是主动式 (active) 联机时， 客户端会先随机启用一个埠口 (图 21.1-1 当中的 port BB) ，且透过命令通道告知 FTP 服务器这两个信息，并等待 FTP 服务器的联机；

3.  FTP 服务器『主动』向客户端联机 FTP 服务器由命令通道了解客户端的需求后，会主动的由 20 这个埠号向客户端的 port BB 联机， 这个联机当然也会经过三向交握啦！此时 FTP 的客户端与服务器端共会建立两条联机，分别用在命令的下达与数据的传递。 而预设 FTP 服务器端使用的主动联机埠号就是 port 20 啰！

如此一来则成功的建立起『命令』与『数据传输』两个信道！不过，要注意的是， 『数据传输信道』是在有数据传输的行为时才会建立的通道喔！并不是一开始连接到 FTP 服务器就立刻建立的通道呢！留意一下啰！

*   主动式联机使用到的埠号

利用上述的说明来整理一下 FTP 服务器端会使用到的埠号主要有：

*   命令通道的 ftp (默认为 port 21) 与
*   数据传输的 ftp-data (默认为 port 20)。

再强调一次，这两个埠口的工作是不一样的，而且，重要的是两者的联机发起端是不一样的！首先 port 21 主要接受来自客户端的主动联机，至于 port 20 则为 FTP 服务器主动联机至客户端呢！这样的情况在服务器与客户端两者同时为公共 IP (Public IP) 的因特网上面通常没有太大的问题，不过，万一你的客户端是在防火墙后端，或者是 NAT 服务器后端呢？会有什么问题发生呢？底下我们来谈一谈这个严重的问题！

*   在主动联机的 FTP 服务器与客户端之间具有防火墙的联机问题

回想一下我们的[第九章防火墙](http://linux.vbird.org/linux_server/0250simple_firewall.php)！ 一般来说，很多的局域网络都会使用防火墙 (iptables) 的 NAT 功能，那么在 NAT 后端的 FTP 用户如何连接到 FTP 服务器呢？ 我们可以简单的以下图来说明：

![](img/connect_active_nat.gif) 图 21.1-2、 FTP 客户端与服务器端联机中间具有防火墙的联机状态

1.  用户与服务器间命令信道的建立： 因为 NAT 会主动的记录由内部送往外部的联机信息，而由于命令信道的建立是由客户端向服务器端联机的， 因此这一条联机可以顺利的建立起来的；

2.  用户与服务器间数据信道建立时的通知： 同样的，客户端主机会先启用 port BB ，并透过命令通道告知 FTP 服务器，且等待服务器端的主动联机；

3.  服务器主动连到 NAT 等待转递至客户端的联机问题： 但是由于透过 NAT 的转换后，FTP 服务器只能得知 NAT 的 IP 而不是客户端的 IP ， 因此 FTP 服务器会以 port 20 主动的向 NAT 的 port BB 发送主动联机的要求。 但你的 NAT 并没有启动 port BB 来监听 FTP 服务器的联机啊！

了解问题的所在了吗？在 FTP 的主动式联机当中，NAT 将会被视为客户端，但 NAT 其实并非客户端啊， 这就造成问题了。如果你曾经在 IP 分享器后面连接某些 FTP 服务器时，可能偶尔会发现明明就连接上 FTP 服务器了 (命令通道已建立)，但是就是无法取得文件名的列表，而是在超过一段时间后显示『 Can't build data connection: Connection refused，无法进行数据传输』之类的讯息， 那肯定就是这个原因所造成的困扰了。

那有没有办法可以克服这个问题呢？难道真的在 Linux NAT 后面就一定无法使用 FTP 吗？当然不是！ 目前有两个简易的方法可以克服这个问题：

*   使用 iptables 所提供的 FTP 侦测模块：

    其实 iptables 早就提供了许多好用的模块了，这个 FTP 当然不会被错过！ 你可以使用 [modprobe](http://linux.vbird.org/linux_basic/0510osloader.php#kernel_load) 这个指令来加载 ip*conntrack_ftp 及 ip_nat_ftp 等模块，这几个模块会主动的分析『目标是 port 21 的联机』信息， 所以可以得到 port BB 的资料，此时若接受到 FTP 服务器的主动联机，就能够将该封包导向正确的后端主机了！ ^*^

    不过，如果你链接的目标 FTP 服务器他的命令通道默认端口号并非标准的 21 埠号时 (例如某些地下 FTP 服务器)， 那么这两个模块就无法顺利解析出来了，这样说，理解吗？

*   客户端选择被动式 (Passive) 联机模式：

    除了主动式联机之外，FTP 还提供一种称为被动式联机的模式，什么是被动式呢？ 既然主动式是由服务器向客户端联机，反过来讲，被动式就是由客户端向服务器端发起联机的啰！ 既然是由客户端发起联机的，那自然就不需要考虑来自 port 20 的联机啦！关于被动式联机模式将在下一小节介绍喔！

* * *

### 21.1.3 客户端选择被动式联机模式

那么什么是被动式联机呢？我们可以使用底下的图示来作个简略的介绍喔：

![](img/connect_passive.gif) 图 21.1-3、FTP 的被动式数据流联机流程

1.  用户与服务器建立命令信道： 同样的需要建立命令通道，透过三向交握就可以建立起这个通道了。

2.  客户端发出 PASV 的联机要求： 当有使用数据信道的指令时，客户端可透过命令通道发出 PASV 的被动式联机要求 (Passive 的缩写)， 并等待服务器的回应；

3.  FTP 服务器启动数据端口，并通知客户端联机： 如果你的 FTP 服务器是能够处理被动式联机的，此时 FTP 服务器会先启动一个埠口在监听。 这个端口号码可能是随机的，也可以自定义某一范围的埠口，端看你的 FTP 服务器软件而定。 然后你的 FTP 服务器会透过命令通道告知客户端该已经启动的埠口 (图中的 port PASV)， 并等待客户端的联机。

4.  客户端随机取用大于 1024 的埠口进行连接： 然后你的客户端会随机取用一个大于 1024 的端口号来对主机的 port PASV 联机。 如果一切都顺利的话，那么你的 FTP 数据就可以透过 port BB 及 port PASV 来传送了。

发现上面的不同点了吗？被动式 FTP 数据信道的联机方向是由客户端向服务器端联机的喔！ 如此一来，在 NAT 内部的客户端主机就可以顺利的连接上 FTP Server 了！但是，万一 FTP 主机也是在 NAT 后端那怎么办...呵呵！那可就糗了吧～ @_@这里就牵涉到更深入的 DMZ 技巧了，我们这里暂不介绍这些深入的技巧，先理解一下这些特殊的联机方向， 这将有助于你未来服务器架设时候的考虑因素喔！

此外，不晓得你有无发现，透过 PASV 模式，服务器在没有特别设定的情况下，会随机选取大于 1024 的埠口来提供客户端连接之用。那么万一服务器启用的埠口被搞鬼怎么办？而且， 如此一来也很难追踪来自入侵者攻击的登录信息啊！所以，这个时候我们可以透过 passive ports 的功能来『限定』服务器启用的 port number 喔！

* * *

### 21.1.4 FTP 的安全性问题与替代方案

其实，在 FTP 上面传送的数据很可能被窃取，因为 FTP 是明码传输的嘛！而且某些 FTP 服务器软件的资安历史问题也是很严重的。 因此，一般来说，除非是学校或者是一些社团单位要开放没有机密或授权问题的资料之外，FTP 是少用为妙的。

拜 [SSH](http://linux.vbird.org/linux_server/0310telnetssh.php) 所赐，目前我们已经有较为安全的 FTP 了，那就是 ssh 提供的 sftp 这个 server 啊！这个 sftp-server 最大的优点就是：『在上面传输的数据是经过加密的』！所以在因特网上面流窜的时候， 嘿嘿！毕竟是比较安全一些啦！所以建议你，除非必要，否则的话使用 SSH 提供的 sftp-server 功能即可～

然而这个功能对于一些习惯了图形接口，或者是有中文档名的使用者来说，实在是不怎么方便， 虽说目前有个图形接口的 filezilla 客户端软件，不过很多时候还是会发生一些莫名的问题说！ 所以，有的时候 FTP 网站还是有其存在的需要的。如果真的要架设 FTP 网站，那么还是得需要注意几个事项喔：

1.  随时更新到最新版本的 FTP 软件，并随时注意漏洞讯息；
2.  善用 iptables 来规定可以使用 FTP 的网域；
3.  善用 TCP_Wrappers 来规范可以登入的网域；
4.  善用 FTP 软件的设定来限制使用你 FTP 服务器的使用者的不同权限啊；
5.  使用 Super daemon 来进阶管理你的 FTP 服务器；
6.  随时注意用户的家目录、以及匿名用户登入的目录的『档案权限』；
7.  若不对外公开的话，或许也可以修改 FTP 的 port 。
8.  也可以使用 FTPs 这种加密的 FTP 功能！

无论如何，在网络上听过太多人都是由于开放 FTP 这个服务器而导致整个主机被入侵的事件，所以， 这里真的要给他一直不断的强调，要注意安全啊！

* * *

### 21.1.5 开放什么身份的使用者登入

既然 FTP 是以明码传输，并且某些早期的 FTP 服务器软件也有不少的安全漏洞，那又为何需要架设 FTP 服务器啊？ 没办法啊，总是有人有需要这个玩意儿的，譬如说各大专院校不就有提供 FTP 网站的服务吗？ 这样可以让校内的同学共同分享校内的网络资源嘛！不过，由于 FTP 登入者的身份可以分为三种， 你到底要开放哪一种身份登入呢？这个时候你可以这样简单的思考一下啰：

*   开放实体用户的情况 (Real user)：

很多的 FTP 服务器默认就已经允许实体用户的登入了。不过，需要了解的是，以实体用户做为 FTP 登入者身份时， 系统默认并没有针对实体用户来进行『限制』的，所以他可以针对整个文件系统进行任何他所具有权限的工作。 因此，如果你的 FTP 使用者没能好好的保护自己的密码而导致被入侵，那么你的整个 Linux 系统数据将很有可能被窃取啊！ 开放实体用户时的建议如下：

*   使用替代的 FTP 方案较佳： 由于实体用户本来就可以透过网络连接到主机来进行工作 (例如 SSH)，因此实在没有需要特别的开放 FTP 的服务啊！因为例如 sftp 本来就能达到传输档案的功能啰！

*   限制用户能力，如 chroot 与 /sbin/nologin 等： 如果确定要让实体用户利用 FTP 服务器的话，那么你可能需要让某些系统账号无法登入 FTP 才行，例如 bin, apache 等等。 最简单常用的作法是透过 PAM 模块来处理，譬如 vsftpd 这个软件默认可以透过 /etc/vsftpd/ftpusers 这个档案来设定不想让他具有登入 FTP 的账号。另外，将使用者身份 chroot 是相当需要的！

*   访客身份 (Guest)

通常会建立 guest 身份的案例当中，多半是由于服务器提供了类似『个人 Web 首页』的功能给一般身份用户， 那么这些使用者总是需要管理自己的网页空间吧？这个时候将使用者的身份压缩成为 guest ，并且将他的可用目录设定好，即可提供使用者一个方便的使用环境了！且不需要提供他 real user 的权限喔！ 常见的建议如下：

*   仅提供需要登入的账号即可，不需要提供系统上面所有人均可登入的环境啊！

*   当然，我们在服务器的设定当中，需要针对不同的访客给他们不一样的『家目录』， 而这个家目录与用户的权限设定需要相符合喔！例如要提供 dmtsai 这个人管理他的网页空间，而他的网页空间放置在 /home/dmtsai/www 底下，那我就将 dmtsai 在 FTP 提供的目录仅有 /home/dmtsai/www 而已，比较安全啦！而且也方便使用者啊！

*   针对这样的身份者，需要设定较多的限制，包括：上下传档案数目与硬盘容量的限制、 联机登入的时间限制、许可使用的指令要减少很多很多，例如 chmod 就不要允许他使用等等！

*   匿名登录使用者 (anonymous)

虽然提供匿名登录给因特网的使用者进入实在不是个好主意，因为每个人都可以去下载你的数据， 万一带宽被吃光光怎么办？但如同前面讲过的，学校单位需要分享全校同学一些软件资源时， FTP 服务器也是一个很不错的解决方案啊！你说是吧。如果要开放匿名用户的话，要注意：

*   无论如何，提供匿名登录都是一件相当危险的事情，因为只要你一不小心， 将重要的资料放置到匿名者可以读取的目录中时，那么就很有可能会泄密！与其战战兢兢，不如就不要设定啊～

*   果真要开放匿名登录时，很多限制都要进行的，这包括：(1)允许的工作指令要减低很多， 几乎就不许匿名者使用指令啦、(2)限制文件传输的数量，尽量不要允许『上传』数据的设定、 (3)限制匿名者同时登入的最大联机数量，可以控制盗连喔！

一般来说，如果你是要放置一些公开的、没有版权纠纷的数据在网络上供人下载的话， 那么一个仅提供匿名登录的 FTP 服务器，并且对整个因特网开放是 OK 的啦！ 不过，如果你预计要提供的的软件或数据是具有版权的，但是该版权允许你在贵单位内传输的情况下， 那么架设一个『仅针对内部开放的匿名 FTP 服务器 (利用防火墙处理) 』也是 OK 的啦！

如果你还想要让使用者反馈的话，那是否要架设一个匿名者可上传的区域呢？鸟哥对这件事情的看法是.... 『万万不可』啊！如果要让使用者反馈的话，除非该使用者是你信任的，否则不要允许对方上传！ 所以此时一个文件系统权限管理严格的 FTP 服务器，并提供实体用户的登入就有点需求啦！ 总之，要依照你的需求来思考是否有需要喔！

* * *

# 21.2 vsftpd 服务器基础设定

## 21.2 vsftpd 服务器基础设定

终于要来聊一聊这个简单的 vsftpd 啰！vsftpd 的全名是『Very Secure FTP Daemon 』的意思， 换句话说，vsftpd 最初发展的理念就是在建构一个以安全为重的 FTP 服务器呢！我们先来聊一聊为什么 vsftpd 号称『非常安全』呢？然后再来谈设定吧！

* * *

### 21.2.1 为何使用 vsftpd

为了建构一个安全为主的 FTP 服务器， vsftpd 针对操作系统的『程序的权限 (privilege)』概念来设计， 如果你读过基础篇的[十七章程序与资源管理](http://linux.vbird.org/linux_basic/0440processcontrol.php)的话， 应该会晓得系统上面所执行的程序都会引发一个程序，我们称他为 PID (Process ID)， 这个 PID 在系统上面能进行的任务与他拥有的权限有关。也就是说， PID 拥有的权限等级越高， 他能够进行的任务就越多。举例来说，使用 root 身份所触发的 PID 通常拥有可以进行任何工作的权限等级。

不过，万一触发这个 PID 的程序 (program) 有漏洞而导致被网络怪客 (cracker) 所攻击而取得此 PID 使用权时， 那么网络怪客将会取得这个 PID 拥有的权限吶！所以，近来发展的软件都会尽量的将服务取得的 PID 权限降低，使得该服务即使不小心被入侵了，入侵者也无法得到有效的系统管理权限，这样会让我们的系统较为安全的啦。 vsftpd 就是基于这种想法而设计的。

除了 PID 方面的权限之外， vsftpd 也支持 chroot 这个函式的功能，chroot 顾名思义就是『 change root directory 』的意思，那个 root 指的是『根目录』而非系统管理员。 他可以将某个特定的目录变成根目录，所以与该目录没有关系的其他目录就不会被误用了。

举例来说，如果你以匿名身份登入我们的 ftp 服务的话，通常你会被限定在 /var/ftp 目录下工作， 而你看到的根目录其实就只是 /var/ftp ，至于系统其他如 /etc, /home, /usr... 等其他目录你就看不到了！ 这样一来即使这个 ftp 服务被攻破了，没有关系，入侵者还是仅能在 /var/ftp 里面跑来跑去而已，而无法使用 Linux 的完整功能。自然我们的系统也就会比较安全啦！

vsftpd 是基于上面的说明来设计的一个较为安全的 FTP 服务器软件，他具有底下的特点喔：

*   vsftpd 这个服务的启动者身份为一般用户，所以对于 Linux 系统的权限较低，对于 Linux 系统的危害就相对的减低了。此外， vsftpd 亦利用 chroot() 这个函式进行改换根目录的动作，使得系统工具不会被 vsftpd 这支服务所误用；

*   任何需要具有较高执行权限的 vsftpd 指令均以一支特殊的上层程序所控制， 该上层程序享有的较高执行权限功能已经被限制的相当的低，并以不影响 Linux 本身的系统为准；

*   绝大部分 ftp 会使用到的额外指令功能 (dir, ls, cd ...) 都已经被整合到 vsftpd 主程序当中了，因此理论上 vsftpd 不需要使用到额外的系统提供的指令，所以在 chroot 的情况下，vsftpd 不但可以顺利运作，且不需要额外功能对于系统来说也比较安全。

*   所有来自客户端且想要使用这支上层程序所提供的较高执行权限之 vsftpd 指令的需求， 均被视为『不可信任的要求』来处理，必需要经过相当程度的身份确认后，方可利用该上层程序的功能。 例如 chown(), Login 的要求等等动作；

*   此外，上面提到的上层程序中，依然使用 chroot() 的功能来限制用户的执行权限。

由于具有这样的特点，所以 vsftpd 会变的比较安全一些咯！底下就开始来谈如何设定吧！

* * *

### 21.2.2 所需要的软件以及软件结构

vsftpd 所需要的软件只有一个，那就是 vsftpd 啊！^_^！如果你的 CentOS 没有安装，请利用 yum install vsftpd 来安装他吧！软件很小，下载连同安装不需要几秒钟就搞定了！而事实上整个软件提供的配置文件也少的令人高兴！简单易用就是 vsftpd 的特色啊！这些设定数据比较重要的有：

*   /etc/vsftpd/vsftpd.conf 严格来说，整个 vsftpd 的配置文件就只有这个档案！这个档案的设定是以 [bash 的变量设定](http://linux.vbird.org/linux_basic/0320bash.php#variable)相同的方式来处理的， 也就是『参数=设定值』来设定的，注意， 等号两边不能有空白喔！至于详细的 vsftpd.conf 可以使用 『 man 5 vsftpd.conf 』来详查。

*   /etc/pam.d/vsftpd 这个是 vsftpd 使用 PAM 模块时的相关配置文件。主要用来作为身份认证之用，还有一些用户身份的抵挡功能， 也是透过这个档案来达成的。你可以察看一下该档案：

    ```
    [root@www ~]# cat /etc/pam.d/vsftpd
    #%PAM-1.0
    session optional pam_keyinit.so    force revoke
    &lt;u&gt;auth    required pam_listfile.so item=user sense=deny file=/etc/vsftpd/ftpusers onerr=succeed&lt;/u&gt;
    auth    required pam_shells.so
    auth    include  password-auth
    account include  password-auth
    session required pam_loginuid.so
    session include  password-auth 
    ```

    上面那个 file 后面接的档案是『限制使用者无法使用 vsftpd 』之意， 也就是说，其实你的限制档案不见得要使用系统默认值，也可以在这个档案里面进行修改啦！ ^_^

*   /etc/vsftpd/ftpusers 与上一个档案有关系，也就是 PAM 模块 (/etc/pam.d/vsftpd) 所指定的那个无法登入的用户配置文件啊！ 这个档案的设定很简单，你只要将『不想让他登入 FTP 的账号』写入这个档案即可。一行一个账号，看起来像这样：

    ```
    [root@www ~]# cat /etc/vsftpd/ftpusers
    # Users that are not allowed to login via ftp
    root
    bin
    daemon
    ....(底下省略).... 
    ```

    瞧见没有？绝大部分的系统账号都在这个档案内喔，也就是说，系统账号默认是没有办法使用 vsftpd 的啦！ 如果你还想要让某些使用者无法登入，写在这里是最快的！

*   /etc/vsftpd/user_list 这个档案是否能够生效与 vsftpd.conf 内的两个参数有关，分别是『 userlist_enable, userlist_deny 』。 如果说 /etc/vsftpd/ftpusers 是 PAM 模块的抵挡设定项目，那么这个 /etc/vsftpd/user_list 则是 vsftpd 自定义的抵挡项目。事实上这个档案与 /etc/vsftpd/ftpusers 几乎一模一样， 在预设的情况下，你可以将不希望可登入 vsftpd 的账号写入这里。不过这个档案的功能会依据 vsftpd.conf 配置文件内的 userlist_deny={YES/NO} 而不同，这得要特别留意喔！

*   /etc/vsftpd/chroot_list 这个档案预设是不存在的，所以你必须要手动自行建立。这个档案的主要功能是可以将某些账号的使用者 chroot 在他们的家目录下！但这个档案要生效与 vsftpd.conf 内的『 chroot_list_enable, chroot_list_file 』两个参数有关。 如果你想要将某些实体用户限制在他们的家目录下而不许到其他目录去，可以启动这个设定项目喔！

*   /usr/sbin/vsftpd 这就是 vsftpd 的主要执行档咯！不要怀疑， vsftpd 只有这一个执行档而已啊！

*   /var/ftp/ 这个是 vsftpd 的预设匿名者登入的根目录喔！其实与 ftp 这个账号的家目录有关啦！

大致上就只有这几个档案需要注意而已，而且每个档案的设定又都很简单！真是不错啊！

* * *

### 21.2.3 vsftpd.conf 设定值说明

事实上，/etc/vsftpd/vsftpd.conf 本身就是一个挺详细的配置文件，且使用『 man 5 vsftpd.conf 』则可以得到完整的参数说明。 不过我们这里依旧先将 vsftpd.conf 内的常用参数给他写出来，希望对你有帮助：

*   与服务器环境较相关的设定值

*   connect_from_port_20=YES (NO) 记得在前一小节提到的主动式联机使用的 FTP 服务器的 port 吗？这就是 ftp-data 的埠号；

*   listen_port=21 vsftpd 使用的命令通道 port，如果你想要使用非正规的埠号，在这个设定项目修改吧！ 不过你必须要知道，这个设定值仅适合以 stand alone 的方式来启动喔！(对于 super daemon 无效)

*   dirmessage_enable=YES (NO) 当用户进入某个目录时，会显示该目录需要注意的内容，显示的档案默认是 .message ，你可以使用底下的设定项目来修订！

*   message_file=.message 当 dirmessage_enable=YES 时，可以设定这个项目来让 vsftpd 寻找该档案来显示讯息！

*   listen=YES (NO) 若设定为 YES 表示 vsftpd 是以 standalone 的方式来启动的！预设是 NO 呦！所以我们的 CentOS 将它改为 YES 哩！这样才能使用 stand alone 的方式来唤醒。

*   pasv_enable=YES (NO) 支持数据流的被动式联机模式(passive mode)，一定要设定为 YES 的啦！

*   use_localtime=YES (NO) 是否使用本地时间？vsftpd 预设使用 GMT 时间(格林威治)，所以预设的 FTP 内的档案日期会比台湾晚 8 小时，建议修改设定为 YES 吧！

*   write_enable=YES (NO) 如果你允许用户上传数据时，就要启动这个设定值；

*   connect_timeout=60 单位是秒，在数据连接的主动式联机模式下，我们发出的连接讯号在 60 秒内得不到客户端的响应，则不等待并强制断线咯。

*   accept_timeout=60 当用户以被动式 PASV 来进行数据传输时，如果服务器启用 passive port 并等待 client 超过 60 秒而无回应， 那么就给他强制断线！这个设定值与 connect_timeout 类似，不过一个是管理主动联机，一个管理被动联机。

*   data_connection_timeout=300 如果服务器与客户端的数据联机已经成功建立 (不论主动还是被动联机)，但是可能由于线路问题导致 300 秒内还是无法顺利的完成数据的传送，那客户端的联机就会被我们的 vsftpd 强制剔除！

*   idle_session_timeout=300 如果使用者在 300 秒内都没有命令动作，强制脱机！避免占着茅坑不拉屎～

*   max_clients=0 如果 vsftpd 是以 stand alone 方式启动的，那么这个设定项目可以设定同一时间，最多有多少 client 可以同时连上 vsftpd 哩！限制使用 FTP 的用量！

*   max_per_ip=0 与上面 max_clients 类似，这里是同一个 IP 同一时间可允许多少联机？

*   pasv_min_port=0, pasv_max_port=0 上面两个是与 passive mode 使用的 port number 有关，如果你想要使用 65400 到 65410 这 11 个 port 来进行被动式联机模式的连接，可以这样设定 pasv_max_port=65410 以及 pasv_min_port=65400。 如果是 0 的话，表示随机取用而不限制。

*   ftpd_banner=一些文字说明 当使用者联机进入到 vsftpd 时，在 FTP 客户端软件上头会显示的说明文字。不过，这个设定值数据比较少啦！ 建议你可以使用底下的 banner_file 设定值来取代这个项目；

*   banner_file=/path/file 这个项目可以指定某个纯文本档作为使用者登入 vsftpd 服务器时所显示的欢迎字眼。同时，也能够放置一些让使用者知道本 FTP 服务器的目录架构！

*   与实体用户较相关的设定值

*   guest_enable=YES (NO) 若这个值设定为 YES 时，那么任何实体账号，均会被假设成为 guest 喔 (所以预设是不开放的)！ 至于访客在 vsftpd 当中，预设会取得 ftp 这个使用者的相关权限。但可以透过 guest_username 来修改。

*   guest_username=ftp 在 guest_enable=YES 时才会生效，指定访客的身份而已。

*   local_enable=YES (NO) 这个设定值必须要为 YES 时，在 /etc/passwd 内的账号才能以实体用户的方式登入我们的 vsftpd 服务器喔！

*   local_max_rate=0 实体用户的传输速度限制，单位为 bytes/second， 0 为不限制。

*   chroot_local_user=YES (NO) 在预设的情况下，是否要将使用者限制在自己的家目录之内(chroot)？如果是 YES 代表用户默认就会被 chroot，如果是 NO， 则预设是没有 chroot。不过，实际还是需要底下的两个参数互相参考才行。为了安全性，这里应该要设定成 YES 才好。

*   chroot_list_enable=YES (NO) 是否启用 chroot 写入列表的功能？与底下的 chroot_list_flie 有关！这个项目得要开启，否则底下的列表档案会无效。

*   chroot_list_file=/etc/vsftpd.chroot_list 如果 chroot_list_enable=YES 那么就可以设定这个项目了！这个项目与 chroot_local_user 有关，详细的设定状态请参考 21.2.6 chroot 的说明。

*   userlist_enable=YES (NO) 是否藉助 vsftpd 的抵挡机制来处理某些不受欢迎的账号，与底下的参数设定有关；

*   userlist_deny=YES (NO) 当 userlist_enable=YES 时才会生效的设定，若此设定值为 YES 时，则当使用者账号被列入到某个档案时， 在该档案内的使用者将无法登入 vsftpd 服务器！该档案文件名与下列设定项目有关。

*   userlist_file=/etc/vsftpd/user_list 若上面 userlist_deny=YES 时，则这个档案就有用处了！在这个档案内的账号都无法使用 vsftpd 喔！

*   匿名者登入的设定值

*   anonymous_enable=YES (NO) 设定为允许 anonymous 登入我们的 vsftpd 主机！预设是 YES ，底下的所有相关设定都需要将这个设定为 anonymous_enable=YES 之后才会生效！

*   anon_world_readable_only=YES (NO) 仅允许 anonymous 具有下载可读档案的权限，预设是 YES。

*   anon_other_write_enable=YES (NO) 是否允许 anonymous 具有除了写入之外的权限？包括删除与改写服务器上的档案及档名等权限。预设当然是 NO！如果要设定为 YES，那么开放给 anonymous 写入的目录亦需要调整权限，让 vsftpd 的 PID 拥有者可以写入才行！

*   anon_mkdir_write_enable=YES (NO) 是否让 anonymous 具有建立目录的权限？默认值是 NO！如果要设定为 YES， 那么 anony_other_write_enable 必须设定为 YES ！

*   anon_upload_enable=YES (NO) 是否让 anonymous 具有上传数据的功能，默认是 NO，如果要设定为 YES ，则 anon_other_write_enable=YES 必须设定。

*   deny_email_enable=YES (NO) 将某些特殊的 email address 抵挡住，不让那些 anonymous 登入！如果以 anonymous 登入服务器时，不是会要求输入密码吗？密码不是要你输入你的 email address 吗？如果你很讨厌某些 email address， 就可以使用这个设定来将他取消登入的权限！需与下个设定项目配合：

*   banned_email_file=/etc/vsftpd/banned_emails 如果 deny_email_enable=YES 时，可以利用这个设定项目来规定哪个 email address 不可登入我们的 vsftpd 喔！在上面设定的档案内，一行输入一个 email address 即可！

*   no_anon_password=YES (NO) 当设定为 YES 时，表示 anonymous 将会略过密码检验步骤，而直接进入 vsftpd 服务器内喔！所以一般预设都是 NO 的！(登入时会检查输入的 emai)

*   anon_max_rate=0 这个设定值后面接的数值单位为 bytes/秒 ，限制 anonymous 的传输速度，如果是 0 则不限制(由最大带宽所限制)，如果你想让 anonymous 仅有 30 KB/s 的速度，可以设定『anon_max_rate=30000』

*   anon_umask=077 限制 anonymous 上传档案的权限！如果是 077 则 anonymous 传送过来的档案权限会是 -rw------- 喔！

*   关于系统安全方面的一些设定值

*   ascii_download_enable=YES (NO) 如果设定为 YES ，那么 client 就优先 (预设) 使用 ASCII 格式下载文件。

*   ascii_upload_enable=YES (NO) 与上一个设定类似的，只是这个设定针对上传而言！预设是 NO

*   one_process_model=YES (NO) 这个设定项目比较危险一点～当设定为 YES 时，表示每个建立的联机都会拥有一支 process 在负责，可以增加 vsftpd 的效能。不过， 除非你的系统比较安全，而且硬件配备比较高，否则容易耗尽系统资源喔！一般建议设定为 NO 的啦！

*   tcp_wrappers=YES (NO) 当然我们都习惯支持 [TCP Wrappers](http://linux.vbird.org/linux_server/0250simple_firewall.php#tcp_wrappers) 的啦！所以设定为 YES 吧！

*   xferlog_enable=YES (NO) 当设定为 YES 时，使用者上传与下载文件都会被纪录起来。记录的档案与下一个设定项目有关：

*   xferlog_file=/var/log/xferlog 如果上一个 xferlog_enable=YES 的话，这里就可以设定了！这个是登录档的档名啦！

*   xferlog_std_format=YES (NO) 是否设定为 wu ftp 相同的登录档格式？预设为 NO ，因为登录档会比较容易读！ 不过，如果你有使用 wu ftp 登录文件的分析软件，这里才需要设定为 YES

*   dual_log_enable=YES, vsftpd_log_file=/var/log/vsftpd.log 除了 /var/log/xferlog 的 wu-ftp 格式登录档之外，还可以具有 vsftpd 的独特登录档格式喔！如果你的 FTP 服务器并不是很忙碌， 或许订出两个登录档的撰写 (/var/log/{vsftpd.log,xferlog) 是不错的。

*   nopriv_user=nobody 我们的 vsftpd 预设以 nobody 作为此一服务执行者的权限。因为 nobody 的权限相当的低，因此即使被入侵，入侵者仅能取得 nobody 的权限喔！

*   pam_service_name=vsftpd 这个是 pam 模块的名称，我们放置在 /etc/pam.d/vsftpd 即是这个咚咚！

上面这些是常见的 vsftpd 的设定参数，还有很多参数我没有列出来，你可以使用 man 5 vsftpd.conf 查阅喔！不过，基本上上面这些参数已经够我们设定 vsftpd 啰。

* * *

### 21.2.4 vsftpd 启动的模式

vsftpd 可以使用 stand alone 或 super daemon 的方式来启动，我们 CentOS 预设是以 stand alone 来启动的。 那什么时候应该选择 stand alone 或者是 super daemon 呢？如果你的 ftp 服务器是提供给整个因特网来进行大量下载的任务，例如各大专院校的 FTP 服务器，那建议你使用 stand alone 的方式， 服务的速度上会比较好。如果仅是提供给内部人员使用的 FTP 服务器，那使用 super daemon 来管理即可啊。

*   利用 CentOS 提供的 script 来启动 vsftpd (stand alone)

其实 CentOS 不用作任何设定就能够启动 vsftpd 啰！是这样启动的啦：

```
[root@www ~]# /etc/init.d/vsftpd start
[root@www ~]# netstat -tulnp&#124; grep 21
tcp  0  0 0.0.0.0:21  0.0.0.0:*   LISTEN   11689/vsftpd
# 看到啰，是由 vsftpd 所启动的呢！ 
```

*   自行设定以 super daemon 来启动 (有必要再进行，不用实作)

如果你的 FTP 是很少被使用的，那么利用 super daemon 来管理不失为一个好主意。 不过若你想要使用 super daemon 管理的话，那就得要自行修改一下配置文件了。其实也不难啦，你应该要这样处理的：

```
[root@www ~]# vim /etc/vsftpd/vsftpd.conf
# 找到 listen=YES 这一行：大约在 109 行左右啦，并将它改成：
listen=NO 
```

接下来修改一下 super daemon 的配置文件，底下这个档案你必须要自行建立的，原本是不存在的喔：

```
[root@www ~]# yum install xinetd   &lt;==假设 xinetd 没有安装时
[root@www ~]# vim /etc/xinetd.d/vsftpd
service ftp
{
        socket_type             = stream
        wait                    = no
        user                    = root
        server                  = /usr/sbin/vsftpd
        log_on_success          += DURATION USERID
        log_on_failure          += USERID
        nice                    = 10
        disable                 = no
} 
```

然后尝试启动看看呢：

```
[root@www ~]# /etc/init.d/vsftpd stop
[root@www ~]# /etc/init.d/xinetd restart
[root@www ~]# netstat -tulnp&#124; grep 21
tcp  0  0 0.0.0.0:21  0.0.0.0:*   LISTEN   32274/xinetd 
```

有趣吧！两者启动的方式可不一样啊！管理的方式就会差很多的呦！不管你要使用哪种启动的方式，切记不要两者同时启动，否则会发生错误的！你应该使用 chkconfig --list 检查一下这两种启动的方式，然后依据你的需求来决定用哪一种方式启动。鸟哥底下的设定都会以 stand alone 这个 CentOS 默认的启动模式来处理，所以赶紧将刚刚的动作给他改回来喔！

* * *

### 21.2.5 CentOS 的 vsftpd 默认值

在 CentOS 的默认值当中，vsftpd 是同时开放实体用户与匿名用户的，CentOS 的默认值如下：

```
[root@www ~]# vim /etc/vsftpd/vsftpd.conf
# 1\. 与匿名者有关的信息：
anonymous_enable=YES        &lt;==支持匿名者的登入使用 FTP 功能

# 2\. 与实体用户有关的设定
local_enable=YES            &lt;==支持本地端的实体用户登入
write_enable=YES            &lt;==允许用户上传数据 (包括档案与目录)
local_umask=022             &lt;==建立新目录 (755) 与档案 (644) 的权限

# 3\. 与服务器环境有关的设定
dirmessage_enable=YES       &lt;==若目录下有 .message 则会显示该档案的内容
xferlog_enable=YES          &lt;==启动登录文件记录，记录于 /var/log/xferlog
connect_from_port_20=YES    &lt;==支持主动式联机功能
xferlog_std_format=YES      &lt;==支持 WuFTP 的登录档格式
listen=YES                  &lt;==使用 stand alone 方式启动 vsftpd
pam_service_name=vsftpd     &lt;==支持 PAM 模块的管理
userlist_enable=YES         &lt;==支持 /etc/vsftpd/user_list 档案内的账号登入管控！
tcp_wrappers=YES            &lt;==支持 TCP Wrappers 的防火墙机制 
```

上面各项设定值请自行参考 21.2.3 的详细说明吧。而通过这样的设定值咱们的 vsftpd 可以达到如下的功能：

*   你可以使用 anonymous 这个匿名账号或其他实体账号 (/etc/passwd) 登入；
*   anonymous 的家目录在 /var/ftp ，且无上传权限，亦已经被 chroot 了；
*   实体用户的家目录参考 /etc/passwd，并没有被 chroot，可前往任何有权限可进入的目录中；
*   任何于 /etc/vsftpd/ftpusers 内存在的账号均无法使用 vsftpd (PAM)；
*   可利用 /etc/hosts.{allow|deny} 来作为基础防火墙；
*   当客户端有任何上传/下载信息时，该信息会被纪录到 /var/log/xferlog 中；
*   主动式联机的埠口为 port 20；
*   <u>使用格林威治时间 (GMT)。</u>

所以当你启动 vsftpd 后，你的实体用户就能够直接利用 vsftpd 这个服务来传输他自己的数据了。 不过比较大的问题是，因为 vsftpd 预设使用 GMT 时间，因为你在客户端使用 ftp 软件连接到 FTP 服务器时，会发现每个档案的时间都慢了八小时了！真是讨厌啊！ 所以建议你加设一个参数值，就是『 use_localtime=YES 』啰！

```
[root@www ~]# vim /etc/vsftpd/vsftpd.conf
# 在这个档案当中的最后一行加入这一句即可
use_localtime=YES

[root@www ~]# /etc/init.d/vsftpd restart
[root@www ~]# chkconfig vsftpd on 
```

如此一来你的 FTP 服务器不但可以提供匿名账号来下载 /var/ftp 的数据，如果使用实体账号来登入的话， 就能够进入到该用户的家目录底下去了！真是很简单方便的一个设定啊！且使用本地端时间呢！ ^_^

另外，如果你预计要将 FTP 开放给 Internet 使用时，请注意得要开放防火墙喔！关于防火墙的建置情况， 由于牵涉到数据流的主动、被动联机方式，因此，还得要加入防火墙模块。这部份我们在后续的 21.2.8 小节再加以介绍，反正，最终记得要开放 FTP 的联机要求就对了！

* * *

### 21.2.6 针对实体账号的设定

虽然在 CentOS 的默认情况当中实体用户已经可以使用 FTP 的服务了，不过我们可能还需要一些额外的功能来限制实体用户。 举例来说，限制用户无法离开家目录 (chroot)、限制下载速率、限制用户上传档案时的权限 (mask) 等等。 底下我们先列出一些希望达到的功能，然后再继续进行额外功能的处理：

*   希望使用台湾本地时间取代 GMT 时间；
*   用户登入时显示一些欢迎讯息的信息；
*   系统账号不可登入主机 (亦即 UID 小于 500 以下的账号)；
*   一般实体用户可以进行上传、下载、建立目录及修改档案等动作；
*   用户新增的档案、目录之 umask 希望设定为 002；
*   其他主机设定值保留默认值即可。

你可以自行处理 vsftpd.conf 这个档案，以下则是一个范例。注意，如果你的 vsftpd.conf 没有相关设定值， 请自行补上吧！OK！让我们开始一步一步来依序处理先：

1.  先建立主配置文件 vsftpd.conf，这个配置文件已经包含了主要设定值：

    ```
    [root@www ~]# vim /etc/vsftpd/vsftpd.conf
    # 1\. 与匿名者相关的信息，在这个案例中将匿名登录取消：
    anonymous_enable=NO

    # 2\. 与实体用户相关的信息：可写入，且 umask 为 002 喔！
    local_enable=YES
    write_enable=YES
    local_umask=002
    userlist_enable=YES
    userlist_deny=YES
    userlist_file=/etc/vsftpd/user_list  &lt;==这个档案必须存在！还好，预设有此档案！

    # 3\. 与服务器环境有关的设定
    use_localtime=YES
    dirmessage_enable=YES
    xferlog_enable=YES
    connect_from_port_20=YES
    xferlog_std_format=YES
    listen=YES
    pam_service_name=vsftpd
    tcp_wrappers=YES
    banner_file=/etc/vsftpd/welcome.txt &lt;==&lt;u&gt;这个档案必须存在！需手动建立！&lt;/u&gt;

    [root@www ~]# /etc/init.d/xinetd restart  &lt;==取消 super dameon
    [root@www ~]# /etc/init.d/vsftpd restart 
    ```

2.  建立欢迎讯息：

    当我们想让登入者可查阅咱们系统管理员所下达的『公告』事项时，可以使用这个设定！那就是 banner_file=/etc/vsftpd/welcome.txt 这个参数的用途了！我们可以编辑这个档案即可。 好了，开始来建立欢迎画面吧！

    ```
    [root@www ~]# vim /etc/vsftpd/welcome.txt
    欢迎光临本小站，本站提供 FTP 的相关服务！
    主要的服务是针对本机实体用户提供的，
    若有任何问题，请与鸟哥联络！ 
    ```

3.  建立限制系统账号登入的档案

    再来是针对系统账号来给予抵挡的机制，其实有两个档案啦，一个是 PAM 模块管的，一个是 vsftpd 主动提供的， 在预设的情况下这两个档案分别是：

    *   /etc/vsftpd/ftpusers：就是 /etc/pam.d/vsftpd 这个档案的设定所影响的；
    *   /etc/vsftpd/user_list：由 vsftpd.conf 的 userlist_file 所设定。 这两个档案的内容是一样的哩～并且这两个档案必须要存在才行。请你参考你的 /etc/passwd 配置文件， 然后将 UID 小于 500 的账号名称给他同时写到这两个档案内吧！一行一个账号！

    ```
    [root@www ~]# vim /etc/vsftpd/user_list
    root
    bin
    ....(底下省略).... 
    ```

4.  测试结果：

    你可以使用图形接口的 FTP 客户端软件来处理，也可以透过 Linux 本身提供的 ftp 客户端功能哩！ 关于 [ftp 指令](http://linux.vbird.org/linux_server/0140networkcommand.php#ftp)我们已经在[第五章](http://linux.vbird.org/linux_server/0140networkcommand.php)谈过了，你可以自行前往参考。这里直接测试一下吧：

    ```
    # 测试使用已知使用者登入，例如 dmtsai 这个实体用户：
    [root@www ~]# ftp localhost
    Trying 127.0.0.1...
    Connected to localhost (127.0.0.1).
    220-欢迎光临本小站，本站提供 FTP 的相关服务！   &lt;==刚刚建立的欢迎讯息
    220-主要的服务是针对本机实体用户提供的，
    220-若有任何问题，请与鸟哥联络！
    220
    Name (localhost:root): student
    331 Please specify the password.
    Password:  &lt;==输入密码啰在这里！
    500 OOPS: cannot change directory:/home/student  &lt;==有讲登入失败的原因喔！
    Login failed.
    ftp&gt; bye
    221 Goodbye. 
    ```

    由于默认一般用户无法登入 FTP 的！因为 SELinux 的问题啦！请参考下个小节的方式来处理。 然后以上面的方式测试完毕后，你可以在登入者账号处分别填写 (1)root (2)anonymous 来尝试登入看看！ 如果不能登入的话，那就是设定 OK 的啦！(root 不能登入是因为 PAM 模块以及 user_list 设定值的关系， 而匿名无法登入，是因为我们 vsftpd.conf 里头就是设定不能用匿名登录嘛！)

上面是最简单的实体账号相关设定。那如果你还想要限制用户家目录的 chroot 或其他如速限等数据，就得要看看底下的特殊设定项目啰。

*   实体账号的 SELinux 议题

在预设的情况下，CentOS 的 FTP 是不允许实体账号登入取得家目录数据的，这是因为 SELinux 的问题啦！ 如果你在刚刚的 ftp localhost 步骤中，在 bye 离开 FTP 之前下达过『 dir 』的话，那你会发现没有任何资料跑出来～ 这并不是你错了，而是 SELinux 不太对劲的缘故。那如何解决呢？这样处理即可：

```
[root@www ~]# getsebool -a &#124; grep ftp
allow_ftpd_anon_write --&gt; off
allow_ftpd_full_access --&gt; off
allow_ftpd_use_cifs --&gt; off
allow_ftpd_use_nfs --&gt; off
ftp_home_dir --&gt; off            &lt;==就是这玩意儿！要设定 on 才行！
....(底下省略)....

[root@www ~]# setsebool -P ftp_home_dir=1 
```

这样就搞定啰！如果还有其他可能发生错误的原因，包括档案数据使用 mv 而非使用 cp 导致 SELinux 文件类型无法继承原有目录的类型时，那就请自行查阅 /var/log/messages 的内容吧！通常 SELinux 没有这么难处理的啦！^_^

*   对使用者 (包括未来新增用户) 进行 chroot

在鸟哥接触的一般 FTP 使用环境中，大多数都是要开放给厂商联机来使用的，给自己人使用的机会虽然也有， 不过使用者数量通常比较少一些。所以啰，鸟哥现在都是建议默认让实体用户通通被 chroot， 而允许不必 chroot 的账号才需要额外设定。这样的好处是，新建的账号如果忘记进行 chroot，反正原本就是 chroot， 比较不用担心如果该账号是开给厂商时该怎办的问题。

现在假设我系统里面仅有 vbird 与 dmtsai 两个账号不要被 chroot，其他如 student, smb1... 等账号通通预设是 chroot 的啦，包括未来新增账号也全部预设 chroot！那该如何设定？很简单，三个设定值加上一个额外配置文件就搞定了！步骤如下：

```
# 1\. 修改 vsftpd.conf 的参数值：
[root@www ~]# vim /etc/vsftpd/vsftpd.conf
# 增加是否设定针对某些使用者来 chroot 的相关设定呦！
chroot_local_user=YES
chroot_list_enable=YES
chroot_list_file=/etc/vsftpd/chroot_list

# 2\. 建立不被 chroot 的使用者账号列表，即使没有任何账号，此档案也是要存在！
[root@www ~]# vim /etc/vsftpd/chroot_list
vbird
dmtsai

[root@www ~]# /etc/init.d/vsftpd restart 
```

如此一来，除了 dmtsai 与 vbird 之外的其他可用 FTP 的账号者，通通会被 chroot 在他们的家目录下， 这样对系统比较好啦！接下来，请你自己分别使用有与没有被 chroot 的账号来联机测试看看。

*   限制实体用户的总下载流量 (带宽)

你可不希望带宽被使用者上传/下载所耗尽，而影响咱们服务器的其他正常服务吧？所以限制使用者的传输带宽有时也是需要的！ 假设『我要限制所有使用者的总传输带宽最大可达 1 MBytes/秒 』时，你可以这样做即可：

```
[root@www ~]# vim /etc/vsftpd/vsftpd.conf
# 增加底下这一个参数即可：
local_max_rate=1000000  &lt;==记住喔，单位是 bytes/second

[root@www ~]# /etc/init.d/vsftpd restart 
```

上述的单位是 Bytes/秒，所以你可以依据你自己的网络环境来限制你的带宽！这样就给他限制好啰！有够容易吧！ 那怎么测试啊？很简单，用本机测试最准！你可以用 dd 做出一个 10MB 的档案放在 student 的家目录下，然后用 root 下达 ftp localhost，并输入 student 的帐密，接下来给他 get 这个新的档案，就能够在最终知道下载的速度啦！

*   限制最大同时上线人数与同一 IP 的 FTP 联机数

如果你有限制最大使用带宽的话，那么你可能还需要限制最大在线人数才行！举例来说，你希望最多只有 10 个人同时使用你的 FTP 的话，并且每个 IP 来源最多只能建立一条 FTP 的联机时，那你可以这样做：

```
[root@www ~]# vim /etc/vsftpd/vsftpd.conf
# 增加底下的这两个参数：
max_clients=10
max_per_ip=1

[root@www ~]# /etc/init.d/vsftpd restart 
```

这样就搞定了！让你的 FTP 不会人满为患吶！

*   建立严格的可使用 FTP 的账号列表

在预设的环境当中，我们是将『不许使用 FTP 的账号写入 /etc/vsftpd/user_list 档案』，所以没有写入 /etc/vsftpd/user_list 当中的使用者就能够使用 FTP 了！如此一来，未来新增的使用者预设都能够使用 FTP 的服务。 如果换个角度来思考，若我想只让某些人可以使用 FTP 而已，亦即是新增的使用者预设不可使用 FTP 这个服务的话那么应该如何作呢？你需要修改配置文件成为这样：

```
[root@www ~]# vim /etc/vsftpd/vsftpd.conf
# 这几个参数必须要修改成这样：
userlist_enable=YES
userlist_deny=NO
userlist_file=/etc/vsftpd/user_list

[root@www ~]# /etc/init.d/vsftpd restart 
```

则此时『写入 /etc/vsftpd/user_list 变成可以使用 FTP 的账号』了！ 所以未来新增的使用者如果要能够使用 FTP 的话，就必须要写入 /etc/vsftpd/user_list 才行！ 使用这个机制请特别小心，否则容易搞混掉～

透过这几个简单的设定值，相信 vsftpd 已经可以符合大部分合法 FTP 网站的需求啰！ 更多详细的用法则请参考 man 5 vsftpd.conf 吧！

例题：假设你因为某些特殊需求，所以必须要开放 root 使用 FTP 传输档案，那么你应该要如何处理？答：由于系统账号无法使用 FTP 是因为 PAM 模块与 vsftpd 的内建功能所致，亦即是 /etc/vsftpd/ftpusers 及 /etc/vsftpd/user_list 这两个档案的影响。所以你只要进入这两个档案，并且将 root 那一行批注掉，那 root 就可以使用 vsftpd 这个 FTP 服务了。 不过，不建议如此作喔！

* * *

### 21.2.7 仅有匿名登录的相关设定

虽然你可以同时开启实体用户与匿名用户，不过建议你，服务器还是依据需求，针对单一种身份来设定吧！ 底下我们将针对匿名用户来设定，且不开放实体用户。一般来说，这种设定是给类似大专院校的 FTP 服务器来使用的哩！

*   使用台湾本地的时间，而非 GMT 时间；
*   提供欢迎讯息，说明可提供下载的信息；
*   仅开放 anonymous 的登入，且不需要输入密码；
*   文件传输的速限为 1 Mbytes/second；
*   数据连接的过程 (不是命令通道！) 只要超过 60 秒没有响应，就强制 Client 断线！
*   只要 anonymous 超过十分钟没有动作，就予以断线；
*   最大同时上线人数限制为 50 人，且同一 IP 来源最大联机数量为 5 人；

*   预设的 FTP 匿名者的根目录所在： ftp 账号的家目录

OK！那如何设定呢？首先我们必须要知道的是匿名用户的目录在哪里？ 事实上匿名者默认登入的根目录是以 ftp 这个用户的家目录为主，所以你可以使用『 finger ftp 』来查阅。 咱们的 CentOS 默认的匿名者根目录在 /var/ftp/ 中。且匿名登录者在使用 FTP 服务时，他预设可以使用『 ftp 』 这个使用者身份的权限喔，只是被 chroot 到 /var/ftp/ 目录中就是了。

因为匿名者只会在 /var/ftp/ 当中浏览，所以你必须将要提供给用户下载的数据通通给放置到 /var/ftp/ 去。 假设你已经放置了 linux 的相关目录以及 gnu 的相关软件到该目录中了，那我们可以这样做个假设：

```
[root@www ~]# mkdir /var/ftp/linux
[root@www ~]# mkdir /var/ftp/gnu 
```

然后将 vsftpd.conf 的数据清空，重新这样处理他吧：

1.  建立 vsftpd.conf 的设定数据

    ```
    [root@www ~]# vim /etc/vsftpd/vsftpd.conf
    # 将这个档案的全部内容改成这样：
    # 1\. 与匿名者相关的信息：
    anonymous_enable=YES
    no_anon_password=YES        &lt;==匿名登录时，系统不会检验密码 (通常是 email)
    anon_max_rate=1000000       &lt;==最大带宽使用为 1MB/s 左右
    data_connection_timeout=60  &lt;==数据流联机的 timeout 为 60 秒
    idle_session_timeout=600    &lt;==若匿名者发呆超过 10 分钟就断线
    max_clients=50              &lt;==最大联机与每个 IP 的可用联机
    max_per_ip=5

    # 2\. 与实体用户相关的信息，本案例中为关闭他的情况！
    local_enable=NO

    # 3\. 与服务器环境有关的设定
    use_localtime=YES
    dirmessage_enable=YES
    xferlog_enable=YES
    connect_from_port_20=YES
    xferlog_std_format=YES
    listen=YES
    pam_service_name=vsftpd
    tcp_wrappers=YES
    banner_file=/etc/vsftpd/anon_welcome.txt &lt;==檔名有改喔！

    [root@www ~]# /etc/init.d/vsftpd restart 
    ```

2.  建立欢迎画面与下载提示讯息

    各位亲爱的观众朋友！要注意～在这个案例当中，我们将欢迎讯息设定在 /etc/vsftpd/anon_welcome.txt 这个档案中， 至于这个档案的内容你可以这样写 (这个档案一定要存在！否则会造成客户端无法联机成功喔！)：

    ```
    [root@www ~]# vim /etc/vsftpd/anon_welcome.txt
    欢迎光临本站所提供的 FTP 服务！
    本站主要提供 Linux 操作系统相关档案以及 GNU 自由软件喔！
    有问题请与站长联络！谢谢大家！
    主要的目录为：

    linux   提供 Linux 操作系统相关软件
    gnu     提供 GNU 的自由软件
    uploads 提供匿名的您上传数据 
    ```

    看到啰！主要写的数据都是针对一些公告事项就是了！

3.  客户端的测试：密码与欢迎讯息是重点！

    同样的，我们使用 ftp 这个软件来给他测试一下吧！

    ```
    [root@www ~]# ftp localhost
    Connected to localhost (127.0.0.1).
    220-欢迎光临本站所提供的 FTP 服务！   &lt;==底下这几行中文就是欢迎与提示讯息！
    220-本站主要提供 Linux 操作系统相关档案以及 GNU 自由软件喔！
    220-有问题请与站长联络！谢谢大家！
    220-主要的目录为：
    220-
    220-linux   提供 Linux 操作系统相关软件
    220-gnu     提供 GNU 的自由软件
    220-uploads 提供匿名的您上传数据
    220
    Name (localhost:root): anonymous  &lt;==匿名账号名称是要背的！
    230 Login successful.               &lt;==没有输入密码即可登入呢！
    Remote system type is UNIX.
    Using binary mode to transfer files.
    ftp&gt; dir
    227 Entering Passive Mode (127,0,0,1,196,17).
    150 Here comes the directory listing.
    drwxr-xr-x    2 0        0            4096 Aug 08 16:37 gnu
    -rw-r--r--    1 0        0              17 Aug 08 14:18 index.html
    drwxr-xr-x    2 0        0            4096 Aug 08 16:37 linux
    drwxr-xr-x    2 0        0            4096 Jun 25 17:44 pub
    226 Directory send OK.
    ftp&gt; bye
    221 Goodbye. 
    ```

    看到否？这次可就不需要输入任何密码了，因为是匿名登录嘛！而且，如果你以其他的账号来尝试登入时， 那么 vsftpd 会立刻响应仅开放匿名的讯息喔！(530 This FTP server is anonymous only.)

4.  让匿名者可上传/下载自己的资料 (权限开放最大)

在上列的数据当中，实际上匿名用户仅可进行下载的动作而已。如果你还想让匿名者可以上传档案或者是建立目录的话， 那你还需要额外增加一些设定才行：

```
[root@www ~]# vim /etc/vsftpd/vsftpd.conf
# 新增底下这几行啊！
write_enable=YES
anon_other_write_enable=YES
anon_mkdir_write_enable=YES
anon_upload_enable=YES

[root@www ~]# /etc/init.d/vsftpd restart 
```

如果你设定上面四项参数，则会允许匿名者拥有完整的建立、删除、修改档案与目录的权限。 不过，实际要生效还需要 Linux 的文件系统权限正确才行！ 我们知道匿名者取得的身份是 ftp ，所以如果想让匿名者上传数据到 /var/ftp/uploads/ 中，则需要这样做：

```
[root@www ~]# mkdir /var/ftp/uploads
[root@www ~]# chown ftp /var/ftp/uploads 
```

然后你以匿名者身份登入后，就会发现匿名者的根目录多了一个 /upload 的目录存在了，并且你可以在该目录中上传档案/目录喔！ 如此一来系统的权限大开！很要命喔！所以，请仔细的控制好你的上传目录才行！

不过，在实际测试当中，却发现还是没办法上传呢！怎么回事啊？如果你有去看一下 /var/log/messages 的话，那就会发现啦！ 又是 SELinux 这家伙呢！怎么办？就透过『 sealert -l ... 』在 /var/log/messages 里面观察到的指令丢进去， 立刻就知道解决方案啦！解决方案就是放行 SELinux 的匿名 FTP 规则如下：

```
[root@www ~]# setsebool -P allow_ftpd_anon_write=1
[root@www ~]# setsebool -P allow_ftpd_full_access=1 
```

然后你再测试一下用 anonymous 登入，到 /uploads 去上传个档案吧！就会知道能不能成功哩！

*   让匿名者仅具有上传权限，不可下载匿名者上传的东西

一般来说，用户上传的数据在管理员尚未查阅过是否合乎版权等相关事宜前，是不应该让其他人下载的！ 然而前一小节的设定当中，用户上传的资料是可以被其他人所浏览与下载的！如此一来实在是很危险！所以如果你要设定 /var/ftp/uploads/ 内透过匿名者上传的数据中，仅能上传不能被下载时，那么被上传的数据的权限就得要修改一下才行！ 请将前一小节所设定的四个参数简化成为：

```
[root@www ~]# vim /etc/vsftpd/vsftpd.conf
# 将这几行给他改一改先！记得要拿掉 anon_other_write_enable=YES
write_enable=YES
anon_mkdir_write_enable=YES
anon_upload_enable=YES
chown_uploads=YES        &lt;==新增的设定值在此！
chown_username=daemon

[root@www ~]# /etc/init.d/vsftpd restart 
```

当然啦，那个 /var/ftp/uploads/ 还是需要可以被 ftp 这个使用者写入才行！如此一来被上传的档案将会被修改档案拥有者成为 daemon 这个使用者，而 ftp (匿名者取得的身份) 是无法读取 daemon 的数据的，所以也就无法被下载啰！ ^_^

例题：在上述的设定后，我尝试以 anonymous 登入并且上传一个大档案到 /uploads/ 目录下。由于网络的问题，这个档案传到一半就断线。 下在我重新上传时，却告知这个档案无法覆写！该如何是好？答：为什么会无法覆写呢？因为这个档案在你脱机后，档案的拥有者就被改为 daemon 了！因为这个档案不属于 ftp 这个用户了， 因此我们无法进行覆写或删除的动作。此时，你只能更改本地端档案的档名再次的上传，重新从头一直上传啰！

*   被动式联机埠口的限制

FTP 的联机分为主动式与被动式，主动式联机比较好处理，因为都是透过服务器的 port 20 对外主动联机， 所以防火墙的处理比较简单。被动式联机就比较麻烦～因为预设 FTP 服务器会随机取几个没有在使用当中的埠口来建立被动式联机，那防火墙的设定就麻烦啦！

没关系，我们可以透过指定几个固定范围内的埠口来作为 FTP 的被动式数据连接之用即可， 这样我们就能够预先知道 FTP 数据链路的埠口啦！举例来说，我们假设被动式连接的埠口为 65400 到 65410 这几个埠口时，可以这样设定：

```
[root@www ~]# vim /etc/vsftpd/vsftpd.conf
# 增加底下这几行即可啊！
pasv_min_port=65400
pasv_max_port=65410

[root@www ~]# /etc/init.d/vsftpd restart 
```

匿名用户的设定大致上这样就能符合你的需求啰！其他的设定就自己看着办吧！ ^_^

* * *

### 21.2.8 防火墙设定

防火墙设定有什么难的？将[第九章](http://linux.vbird.org/linux_server/0250simple_firewall.php)里面的 script 拿出来修改即可啊！不过，如同前言谈到的，FTP 使用两个埠口，加上常有随机启用的数据流埠口，以及被动式联机的服务器埠口等， 所以，你可能得要进行：

*   加入 iptables 的 ip_nat_ftp, ip_conntrack_ftp 两个模块
*   开放 port 21 给因特网使用
*   开放前一小节提到的 port 65400~65410 埠口给 Internet 联机用

要修改的地方不少，那就让我们来一步一脚印吧！

```
# 1\. 加入模块：虽然 iptables.rule 已加入模块，不过系统档案还是修改一下好了：
[root@www ~]# vim /etc/sysconfig/iptables-config
IPTABLES_MODULES="ip_nat_ftp ip_conntrack_ftp"
# 加入模块即可！两个模块中间有空格键隔开！然后重新启动 iptables 服务啰！

[root@www ~]# /etc/init.d/iptables restart

# 2\. 修改 iptables.rule 的脚本如下：
[root@www ~]# vim /usr/local/virus/iptables/iptables.rule
iptables -A INPUT -p TCP -i $EXTIF --dport  21  --sport 1024:65534 -j ACCEPT
# 找到上面这一行，并将前面的批注拿掉即可！并且新增底下这一行喔！
iptables -A INPUT -p TCP -i $EXTIF --dport 65400:65410 --sport 1024:65534 -j ACCEPT

[root@www ~]# /usr/local/virus/iptables/iptables.rule 
```

这样就好了！同时兼顾主动式与被动式的联机！并且加入所需要的 FTP 模块啰！

* * *

### 21.2.9 常见问题与解决之道

底下说明几个常见的问题与解决之道吧！

*   如果在 Client 端上面发现无法联机成功，请检查：

    1.  iptables 防火墙的规则当中，是否开放了 client 端的 port 21 登入？
    2.  在 /etc/hosts.deny 当中，是否将 client 的登入权限挡住了？
    3.  在 /etc/xinetd.d/vsftpd 当中，是否设定错误，导致 client 的登入权限被取消了？
*   如果 Client 已经连上 vsftpd 服务器，但是却显示『 XXX file can't be opend 』的字样，请检查：

    1.  最主要的原因还是在于在 vsftpd.conf 当中设定了检查某个档案，但是你却没有将该档案设定起来， 所以，请检查 vsftpd.conf 里面所有设定的档案档名，使用 touch 这个指令将该档案建立起来即可！
*   如果 Client 已经连上 vsftpd 服务器，却无法使用某个账号登入，请检查：

    1.  在 vsftpd.conf 里面是否设定了使用 pam 模块来检验账号，以及利用 userlist_file 来管理账号？
    2.  请检查 /etc/vsftpd/ftpusers 以及 /etc/vsftpd/user_list 档案内是否将该账号写入了？
*   如果 Client 无法上传档案，该如何是好？

    1.  最可能发生的原因就是在 vsftpd.conf 里面忘记加上这个设定『write_enable=YES』这个设定，请加入；
    2.  是否所要上传的目录『权限』不对，请以 chmod 或 chown 来修订；
    3.  是否 anonymous 的设定里面忘记加上了底下三个参数：
        *   anon_other_write_enable=YES
        *   anon_mkdir_write_enable=YES
        *   anon_upload_enable=YES
    4.  是否因为设定了 email 抵挡机制，又将 email address 写入该档案中了！？请检查！
    5.  是否设定了不许 ASCII 格式传送，但 Client 端却以 ASCII 传送呢？请在 client 端以 binary 格式来传送档案！
    6.  检查一下 /var/log/messages ，是否被 SELinux 所抵挡住了呢？

上面是蛮常发现的错误，如果还是无法解决你的问题，请你务必分析一下这两个档案：/var/log/vsftpd.log 与 /var/log/messages ，里面有相当多的重要资料，可以提供给你进行除错喔！不过 /var/log/vsftpd.log 却预设不会出现！ 只有 /var/log/xferlog 而已。如果你想要加入 /var/log/vsftpd.log 的支持，可以这样做：

```
[root@www ~]# vim /etc/vsftpd/vsftpd.conf
dual_log_enable=YES
vsftpd_log_file=/var/log/vsftpd.log
# 加入这两个设定值即可呦！

[root@www ~]# /etc/init.d/vsftpd restart 
```

这样未来有新联机或者是错误时，就会额外写一份 /var/log/vsftpd.log 去喔！

* * *

# 21.3 客户端的图形接口 FTP 联机软件

## 21.3 客户端的图形接口 FTP 联机软件

客户端的联机软件主要有文字接口的 [ftp](http://linux.vbird.org/linux_server/0140networkcommand.php#ftp) 及 [lftp](http://linux.vbird.org/linux_server/0140networkcommand.php#lftp) 这两支指令，详细的使用方式请参考[第五章常用网络指令](http://linux.vbird.org/linux_server/0140networkcommand.php)的说明。至于 Linux 底下的图形接口软件，可以参考 gftp 这支程序喔！图形接口的啦！很简单啊！那 Windows 底下有没有相对应的 FTP 客户端软件？

* * *

### 21.3.1 Filezilla

上述的软件都是自由软件啊，那么 Windows 操作系统有没有自由软件啊？有的，你可以使用 filezilla 这个好东西！这个玩意儿的详细说明与下载点可以在底下的连结找到：

*   说明网站：[`filezilla.sourceforge.net/`](http://filezilla.sourceforge.net/)
*   下载网站：[`sourceforge.net/project/showfiles.php?group_id=21558`](http://sourceforge.net/project/showfiles.php?group_id=21558)

目前 (2011/06) 最新的稳定版本是 3.5.x 版，所以底下鸟哥就以这个版本来跟大家说明。为什么要选择 Filezilla 呢？除了他是自由软件之外，这家伙竟然可以连结到 SSH 的 sftp 呢！真是很不错的一个家伙啊！^*^！另外要注意的是，底下鸟哥是以 Windows 版本来说明的，不要拿来在 X window 上面安装喔！^*^ (请下载 Filezilla client 不是 server 喔！)

因为这个程序是给 Windows 安装用的，所以安装的过程就是...(下一步)^n 就好了！并且这个程序支持多国语系， 所以你可以选择繁体中文呢！实在是很棒！安装完毕之后，请你执行他，就会出现如下的画面了：

![](img/filezilla_3_002.gif) 图 21.3-1、Filezilla 的操作接口示意图

上图的 第一、二到五区的内容所代表的资料是：

1.  第一区：代表 FTP 服务器的输出信息，例如欢迎讯息等信息；
2.  第二区：代表本机的文件系统目录，与第三区有关；
3.  第三区：代表第二区所选择的磁盘内容为何；
4.  第四区：代表远程 FTP 服务器的目录与档案；
5.  第五区：代表传输时的队列信息 (等待传送的数据)

而另外图中的 a, b, c 则代表的是：

1.  站台管理员，你可以将一些常用的 FTP 服务器的 IP 与用户信息记录在此；
2.  更新，如果你的资料有更新，可使用这个按钮来同步 filezilla 的屏幕显示；
3.  主机地址、用户、密码与端口这四个玩意儿可以实时联机，不记录信息。

好，接下来我们连接到 FTP 服务器上面去，所以你可按下图 21.3-1 的 a 部分，会出现如下画面：

![](img/filezilla_3_003.gif) 图 21.3-2、Filezilla 的 FTP 站台管理员使用示意图

上图的箭头与相关的内容是这样的：

1.  先按下『新增站台』的按钮，然后在箭头 2 的地方就会出现可输入名称的方框；
2.  在该方框当中随便填写一个你容易记录的名字，只要与真正的网站有点关连即可；
3.  接下来看到右边有一般设定，在一般设定里面几个项目很重要的：
    *   主机：在这个方框中填写主机的 IP，端口如果不是标准的 port 21 才填写其他埠口。
    *   协定：主要有 (1)FTP 及 (2)SFTP (SSHD 所提供)，我们这里选 FTP
    *   加密：是否有网络加密，新的协议中，FTP 可以加上 TLS 的 FTPS 喔！预设为明码
    *   登入型式：因为需要账号密码，选择『一般』即可，然后底下就是输入使用者、账号即可。

基本上这样设定完就能够连上主机了，不过，如果你还想要更详细的规范数据连接的方式 (主动式与被动式) 以及其他数据时， 可以按下的『传输设定』按钮，就会出现如下画面了：

![](img/filezilla_3.gif) 图 21.3-3、Filezilla 站台管理员内的传输设定

在这个画面当中你可以选择是否使用被动式传输机制，还可以调整最大联机数呢！为什么要自我限制呢？ 因为 Filezilla 会主动的重复建立多条联机来快速下载，但如果 vsftpd.conf 有限制 max_per_ip 的话， 某些下载会被拒绝的！因此，这个时候在此设定为 1 就显的很重要～随时只有一支联机建立，就不会有重复登入的问题！ 最后请按下图 21.3-2 画面中的『联机』吧！

![](img/filezilla_3_004.gif) 图 21.3-4、Filezilla 联机成功示意图

更多的用法就请你自行研究啰！

* * *

### 21.3.2 透过浏览器取得 FTP 联机

我们在 [第二十章 WWW 服务器](http://linux.vbird.org/linux_server/0360apache.php)当中曾经谈过浏览器所支持的协议，其中一个就是 ftp 这个协定啰！这个协议的处理方式可以在网址列的地方这样输入的：

*   ftp://username@your_ip

要记得，如果你没有输入那个 username@ 的字样时，系统默认会以匿名登录来处理这次的联机。因此如果你想要使用实体用户联机时， 就在在 IP 或主机名之前填写你的账号。举例来说，鸟哥的 FTP 服务器 (192.168.100.254) 若有 dmtsai 这个使用者， 那我启动浏览器后，可以这样做：

*   ftp://dmtsai@192.168.100.254

然后在出现的对话窗口当中输入 dmtsai 的密码，就能够使用浏览器来管理我在 FTP 服务器内的文件系统啰！是否很容易啊 甚至，你连密码都想要写上网址列，那就更厉害啦！

*   ftp://dmtsai:yourpassword@192.168.100.254

* * *

# 21.4 让 vsftpd 增加 SSL 的加密功能

## 21.4 让 vsftpd 增加 SSL 的加密功能

既然 http 都有 https 了，那么使用明码传输的 ftp 有没有加密的 ftps 呢？嘿嘿！说的好！有的啦～既然都有 openssl 这个加密函式库， 我们当然能够使用类似的机制来处理 FTP 啰！但前提之下是你的 vsftpd 有支持 SSL 函式库才行！此外，我们也必须要建立 SSL 的凭证档给 vsftpd 使用，这样才能够进行加密嘛！了解乎！接下来，就让我们一步一步的进行 ftps 的服务器建置吧！

*   1\. 检查 vsftpd 有无支持 ssl 模块：

如果你的 vsftpd 当初编译的时候没有支持 SSL 模块，那么你就得只好自己重新编译一个 vsftpd 的软件了！我们的 CentOS 有支持吗？ 赶紧来瞧瞧：

```
[root@www ~]# ldd $(which vsftpd) &#124; grep ssl
        libssl.so.10 =&gt; /usr/lib64/libssl.so.10 (0x00007f0587879000) 
```

如果有出现 libssl.so 的字样，就是有支持！这样才能够继续下一步呦！

*   2\. 建立专门给 vsftpd 使用的凭证数据：

CentOS 给我们一个建立凭证的地方，那就是 /etc/pki/tls/certs/ 这个目录！详细的说明我们在 [20.5.2](http://linux.vbird.org/linux_server/0360apache.php#www_ssl_own) 里面谈过咯，所以这里只介绍怎么做：

```
[root@www ~]# cd /etc/pki/tls/certs
[root@www certs]# make vsftpd.pem
----- ....(前面省略)....
Country Name (2 letter code) [XX]:TW
State or Province Name (full name) []:Taiwan
Locality Name (eg, city) [Default City]:Tainan
Organization Name (eg, company) [Default Company Ltd]:KSU
Organizational Unit Name (eg, section) []:DIC
Common Name (eg, your name or your server's hostname) []:www.centos.vbird
Email Address []:root@www.centos.vbird

[root@www certs]# cp -a vsftpd.pem /etc/vsftpd/
[root@www certs]# ll /etc/vsftpd/vsftpd.pem
-rw-------. 1 root root 3116 2011-08-08 16:52 /etc/vsftpd/vsftpd.pem
# 要注意一下权限喔！ 
```

*   3\. 修改 vsftpd.conf 的配置文件，假定有实体、匿名账号：

在前面 21.2 里面大多是单纯匿名或单纯实体帐户，这里我们将实体账号透过 SSL 联机，但匿名者使用明码传输！ 两者同时提供给客户端使用啦！FTP 的设定项目主要是这样：

*   提供实体账号登入，实体账号可上传数据，且 umask 为 002
*   实体账号默认为 chroot 的情况，且全部实体账号可用带宽为 1Mbytes/second
*   实体账号的登入与数据传输均需透过 SSL 加密功能传送；
*   提供匿名登录，匿名者仅能下载，不能上传，且使用明码传输 (不透过 SSL)

此时，整体的设定值会有点像这样：

```
[root@www ~]# vim /etc/vsftpd/vsftpd.conf
# 实体账号的一般设定项目：
local_enable=YES
write_enable=YES
local_umask=002
chroot_local_user=YES
chroot_list_enable=YES
chroot_list_file=/etc/vsftpd/chroot_list
local_max_rate=10000000

# 匿名者的一般设定：
anonymous_enable=YES
no_anon_password=YES
anon_max_rate=1000000
data_connection_timeout=60
idle_session_timeout=600

# 针对 SSL 所加入的特别参数！每个项目都很重要！
ssl_enable=YES              &lt;==启动 SSL 的支持
allow_anon_ssl=NO           &lt;==但是不允许匿名者使用 SSL 喔！
force_local_data_ssl=YES    &lt;==强制实体用户数据传输加密
force_local_logins_ssl=YES  &lt;==同上，但连登入时的帐密也加密
ssl_tlsv1=YES               &lt;==支持 TLS 方式即可，底下不用启动
ssl_sslv2=NO
ssl_sslv3=NO
rsa_cert_file=/etc/vsftpd/vsftpd.pem &lt;==预设 RSA 加密的凭证档案所在

# 一般服务器系统设定的项目：
max_clients=50
max_per_ip=5
use_localtime=YES
dirmessage_enable=YES
xferlog_enable=YES
connect_from_port_20=YES
xferlog_std_format=YES
listen=YES
pam_service_name=vsftpd
tcp_wrappers=YES
banner_file=/etc/vsftpd/welcome.txt
dual_log_enable=YES
vsftpd_log_file=/var/log/vsftpd.log
pasv_min_port=65400
pasv_max_port=65410

[root@www ~]# /etc/init.d/vsftpd restart 
```

*   4\. 联机测试看看！使用 Filezilla 联机测试：

接下来我们利用 filezilla 来说明一下，如何透过 SSL/TLS 功能来进行联机加密。很简单，只要在站台管理员的地方选择：

![](img/server_ssl_1.gif) 图 21.4-1、透过 Filezilla 联机到 SSL/TLS 支持的 FTP 方式

如上图所示，重点在箭头所指的地方，需要透过 TLS 的加密方式才行！然后，鸟哥尝试使用 student 这个一般账号登入系统， 联机的时候，应该会出现如下的图示才对：

![](img/server_ssl_2.gif) 图 21.4-2、透过 Filezilla 是否接受凭证呢？

如果一切都没有问题，那么你可以点选上图那个『总是信任』的项目，如此一来，未来联机到这个地方就不会再次要你确认凭证啦！ 很简单的解决了 FTP 联机加密的问题啰！^_^

例题：想一想，既然有了 SFTP 可以进行加密的 FTP 传输，那为何需要 ftps 呢？答：因为既然要开放 SFTP 的话，就得要同时放行 sshd 亦即是 ssh 的联机，如此一来，你的 port 22 很可能会常常被侦测～若是 openssl, openssh 出问题，恐怕你的系统就会被绑架。如果你的 FTP 真的有必要存在，那么透过 ftps 以及利用 vsftpd 这个较为安全的服务器软件来架设， 理论上，是要比 sftp 来的安全些～至少对 Internet 放行 ftps 还不会觉得很可怕...

* * *

# 21.5 重点回顾

## 21.5 重点回顾

*   FTP 是文件传输协议 (File Transfer Protocol) 的简写，主要的功能是进行服务器与客户端的档案管理、传输等事项；
*   FTP 的服务器软件非常多，例如 Wu FTP, Proftpd, vsftpd 等等，各种 FTP 服务器软件的发展理念并不相同， 所以选择时请依照你的需求来决定所需要的软件；
*   FTP 使用的是明码传输，而过去一些 FTP 服务器软件也曾被发现安全漏洞，因此设定前请确定该软件已是最新版本，避免安全议题的衍生；
*   由于 FTP 是明码传输，其实可以使用 SSH 提供的 sftp 来取代 FTP ；
*   大多数的 FTP 服务器软件都提供 chroot 的功能，将实体用户限制在他的家目录内；
*   FTP 这个 daemon 所开启的正规埠口为 20 与 21 ，其中 21 为命令通道， 20 为主动联机的数据传输信道；
*   FTP 的数据传输方式主要分为主动与被动(Passive, PASV)，如果是主动的话，则 ftp-data 在服务器端主动以 port 20 连接到客户端，否则需开放被动式监听的埠口等待客户端来连接；
*   在 NAT 主机内的客户端 FTP 软件联机时可能发生困扰，这可以透过 iptables 的 nat 模块或利用被动式联机来克服；
*   一般来说， FTP 上面共有三个群组，分别是实体用户、访客与匿名登录者(real, guest, anonymous)；
*   可以藉由修改 /etc/passwd 里面的 Shell 字段，来让使用者仅能使用 FTP 而无法登入主机；
*   FTP 的指令、与用户活动所造成的登录档是放置在 /var/log/xferlog 里面；
*   vsftpd 为专注在安全议题上而发展的一套 FTP 服务器软件，他的配置文件在 /etc/vsftpd/vsftpd.conf

* * *

# 21.6 本章习题

## 21.6 本章习题

*   FTP 在建立联机以及数据传输时，会建立哪些联机？需建立两种联机，分别是命令信道与数据传输信道。在主动式联机上为 port 21(ftp) 与 port 20(ftp-data)。
*   FTP 主动式与被动式联机有何不同？主动式联机的时候，命令联机是由 client 端主动连接到服务器端，但是 ftp-data 则是由服务器端主动的联机到 client 端。至于被动式联机的时候，则不论 command 还是 ftp-data 的联机，服务器端都是监听客户端的要求的！
*   有哪些动作可以让你的 FTP 主机更为安全 (secure) ？

    *   随时更新服务器软件到最新版本；
    *   让 guest 与 anonymous 的家目录限制在固定的目录中(chroot 或是 restricted)；
    *   拒绝 root 的登入或者其他系统账号的登入；
    *   拒绝大部分的 upload 行为！
*   我们知道 ftp 会启用两个 ports ，请问这两个 port 在哪里规范的 (以 vsftpd 为例)？而且，一般正规的 port 是几号？若为 stand alone 时，都是由 vsftpd.conf 规范，命令通道为 listen_port=21 规范，数据连接为 connect_from_port_20=YES 及 pasv_max_port=0, pasv_max_port=0 所规范。 若是 super daemon 所管理时，命令信道则由 /etc/services 所规范了。

*   那几个档案可以用来抵挡类似 root 这种系统账号的登入 FTP？/etc/vsftpd/ftpusers /etc/vsftpd/user_list
*   在 FTP 的 server 与 client 端进行数据传输时，有哪两种模式？为何这两种模式影响数据的传输很重要？数据的传输有 ASCII 与 Binary 两种方式，在进行 ascii 传送方式时，被传送的档案将会以文本模式来进行传送的行为， 因此，档案的属性会被修改过，可能造成执行档最后却无法执行等的问题！一般来说，ASCII 通常仅用在文本文件与一些原始码档案的传送。
*   我的主机明明时区设定没有问题，但为何登入 vsftpd 这个 FTP 服务时，时间就是少八小时？该如何解决？肯定是时区方面出了问题，应该就是 vsftpd.conf 里面少了『 use_localtime=YES 』这个参数了。

* * *

# 21.7 参考数据与延伸阅读

## 21.7 参考数据与延伸阅读

*   vsftpd 官方网站：[`vsftpd.beasts.org/`](http://vsftpd.beasts.org/)
*   man 5 vsftpd.conf
*   Filezilla 官方网站：[`filezilla.sourceforge.net/`](http://filezilla.sourceforge.net/)
*   vsftpd + ssl 功能：[`wiki.vpslink.com/Configuring*vsftpd_for_secure_connections*%28TLS/SSL/SFTP%29`](http://wiki.vpslink.com/Configuring_vsftpd_for_secure_connections_%28TLS/SSL/SFTP%29)
*   [`beginlinux.com/blog/2009/01/secure-ftp-with-ssl-on-centos/`](http://beginlinux.com/blog/2009/01/secure-ftp-with-ssl-on-centos/)

* * *

2003/09/03：首次完成 2003/09/04：加入 FTP 服务器软件的选择建议 2006/12/19：将旧的文章移动到[此处](http://linux.vbird.org/linux_server/0410vsftpd/0410vsftpd.php)，并请自行参考 [wu-ftp](http://linux.vbird.org/linux_server/0400wuftp.php), [proftpd](http://linux.vbird.org/linux_server/0410proftpd.php) 等服务！ 2006/12/20：将分散在各处的 FTP 原则说明完毕，也更新完毕啰～疲劳～ 2011/05/28：将旧的基于 CentOS 4.x 的文章移动到[此处](http://linux.vbird.org/linux_server/0410vsftpd/0410vsftpd-centos4.php) 2011/06/04：加入了 ftps 的 SSL 联机加密机制！ 2011/08/08：将基于 CentOS 5.x 的版本移动到 [此处](http://linux.vbird.org/linux_server/0410vsftpd/0410vsftpd-centos5.php)

* * *