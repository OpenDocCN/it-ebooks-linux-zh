# 第十六章、文件服务器之二： SAMBA 服务器

最近更新日期：2011/07/29

如果想要共享档案，在 Linux 对 Linux 的环境下，最简单的方法就是透过 NIS 这玩意儿了！至于 Windows 对 Windows 的环境下，最简单的方法则是『网络上的芳邻』啊。那如果你的区网中有 Windows 也有 Linux 而且想要共享文件系统的话，那该怎办？ 那就使用 Samba 服务器吧！Samba 可以让 Linux 加入 Windows 的网芳支持，让异质平台可以共享文件系统！ 非常好用的呦！不仅如此， Samba 也可以让 Linux 上面的打印机成为打印机服务器 (Printer Server)。鸟哥个人觉得， Samba 对于整个区网的贡献真的是很大啦！

*   16.1 什么是 SAMBA
    *   16.1.1 SAMBA 的发展历史与名称的由来
    *   16.1.2 SAMBA 常见的应用
    *   16.1.3 SAMBA 使用的 NetBIOS 通讯协议
    *   16.1.4 SAMBA 使用的 daemons
    *   16.1.5 联机模式的介绍 (peer/peer, domain model)
*   16.2 SAMBA 服务器的基础设定
    *   16.2.1 Samba 所需软件及其软件结构
    *   16.2.2 基础的网芳分享流程与 smb.conf 的常用设定项目：
    *   *   服务器整体参数, 分享资源参数, 变数特性
    *   16.2.3 不需密码的分享 (security = share, 纯测试) (testparm, smbclient)
    *   16.2.4 需账号密码才可登入的分享 (security = user) (pdbedit, smbpasswd)
    *   16.2.5 设定成为打印机服务器 (CUPS 系统) (cupsaddsmb)
    *   16.2.6 安全性的议题与管理： SELinux, iptables, Samba 内建, Quota
    *   16.2.7 主机安装时的规划与中文扇区挂载
*   16.3 Samba 客户端软件功能
    *   16.3.1 Windows 系统的使用： WinXP 防火墙, port 445
    *   16.3.2 Linux 系统的使用： smbclient, mount.cifs, nmblookup, smbtree, smbstatus
*   16.4 以 PDC 服务器提供账号管理
    *   16.4.1 让 Samba 管理网域使用者的一个实作案例
    *   16.4.2 PDC 服务器的建置
    *   16.4.3 Wimdows XP pro. 的客户端
    *   16.4.4 Wimdows 7 的客户端
    *   16.4.5 PDC 之问题克服
*   16.5 服务器简单维护与管理
    *   16.5.1 服务器相关问题克服
    *   16.5.2 让用户修改 samba 密码同时同步更新 /etc/shadow 密码
    *   16.5.3 利用 ACL 配合单一使用者时的控管
*   16.6 重点回顾
*   16.7 本章习题
*   16.8 参考数据与延伸阅读
*   16.9 [针对本文的建议：http://phorum.vbird.org/viewtopic.php?p=118976](http://phorum.vbird.org/viewtopic.php?p=118976)

* * *

# 16.1 什么是 SAMBA

## 16.1 什么是 SAMBA

在这个章节中，我们要教大家跳的是热情有劲的巴西 SAMBA 舞蹈... 喔不～搞错了～是要向大家介绍 SAMBA 这个好用的服务器啦！咦！怪了！ 怎么服务器的名称会使用 SAMBA 呢？还真是怪怪的呢！那么这个 SAMBA 服务器的功能是什么呢？另外， 它最早是经由什么样的想法而开发出来的呢？底下就让我们慢慢的谈一谈吧！

* * *

### 16.1.1 SAMBA 的发展历史与名称的由来

在早期的网络世界当中，档案数据在不同主机之间的传输大多是使用 [FTP](http://linux.vbird.org/linux_server/0410vsftpd.php) 这个好用的服务器软件来进行传送。不过使用 FTP 传输档案却有个小小的问题， 那就是你无法直接修改主机上面的档案数据！也就是说，你想要更改 Linux 主机上面的某个档案时，你必须要将该档案自服务器下载后才能修改。 也因此该档案在服务器与客户端都会存在。这个时候，万一如果有一天你修改了某个档案， 却忘记将数据上传回主机，那么等过了一阵子之后，呵呵，你如何知道那个档案才是最新的？

*   让档案在两部主机之间直接修改： NFS 与 CIFS

既然有这样的问题，那么好吧，我可不可以在客户端的机器上面直接使用服务器上面的档案， 如果可以在客户端直接进行服务器端档案的存取，那么我在客户端就不需要存在该档案数据啰，也就是说， 我只要有 Server 上面的档案资料存在就可以啦！有没有这样的文件系统啊！ 很高兴的是，[第十三章的 NFS](http://linux.vbird.org/linux_server/0330nfs.php) 就是这样的文件系统之一啦！我只要在客户端将 Server 所提供分享的目录挂载进来， 那么在客户端的机器上面就可以直接取用 Server 上的档案资料啰，而且， 该数据就像是我客户端上面的 partition 一般，真是好用！

而除了可以让 Unix Like 的机器互相分享档案的 NFS 服务器之外，在微软 (Microsoft) 操作系统上面也有类似的文件系统，那就是 Common Internet File System, CIFS 这个咚咚啦！CIFS 最简单的想法就是目前常见的『网络上的芳邻』咯！ Windows 系统的计算机可以透过桌面上『网络上的芳邻』来分享别人所提供的档案数据哩！真是方便。 不过，NFS 仅能让 Unix 机器沟通， CIFS 只能让 Windows 机器沟通。伤脑筋，那么有没有让 Windows 与 Unix-Like 这两个不同的平台相互分享档案数据的文件系统呢？

*   利用封包侦测逆向工程发展的 SMB Server

在 1991 年一个名叫 Andrew Tridgell 博士班研究生就有这样的困扰，他手上有三部机器，分别是跑 DOS 的个人计算机、DEC 公司的 Digital Unix 系统以及 Sun 的 Unix 系统。在当时， DEC 公司有发展出一套称为 PATHWORKS 的软件，这套软件可以用来分享 DEC 的 Unix 与个人计算机的 DOS 这两个操作系统的档案数据，可惜让 Tridgell 觉得较困扰的是，Sun 的 Unix 无法藉由这个软件来达到数据分享的目的 (注 1)。

这个时候 Tridgell 就想说：『咦！既然这两部系统可以相互沟通，没道理 Sun 就必需这么苦命吧？可不可以将这两部系统的运作原理找出来，然后让 Sun 这部机器也能够分享档案数据呢？』， 为了解决这样的的问题，他老兄就自行写了个 program 去侦测当 DOS 与 DEC 的 Unix 系统在进行数据分享传送时所使用到的通讯协议信息，然后将这些重要的信息撷取下来， 并且基于上述所找到的通讯协议而开发出 Server Message Block (SMB) 这个文件系统，而就是这套 SMB 软件就能够让 Unix 与 DOS 互相的分享数据啰！

**Tips:** 再次的给他强调一次，在 Unix Like 上面可以分享档案数据的 file system 是 NFS，那么在 Windows 上面使用的『网络上的芳邻』所使用的文件系统则称为 Common Internet File System, CIFS

![](img/vbird_face.gif)

*   取名 SAMBA 的主因 ^_^

既然写成了软件，想一想，总是需要注册一下商标吧！因此 Tridgell 就去申请了 SMBServer (Server Message Block 的简写) 这个名字来做为他撰写的这个软件的商标，可惜的是，因为 SMB 是没有意义的文字，因此没有办法达成注册。既然如此的话，那么能不能在字典里面找到相关的字词可以做为商标来注册呢？ 翻了老半天，呵呵！这个 **S**A**MB**A 刚好含有 SMB ， 又是热情有劲的拉丁舞蹈的名称，不然就用这个名字来做为商标好了！这成为我们今天所使用的 SAMBA 的名称由来啦！ ^_^

* * *

### 16.1.2 SAMBA 常见的应用

由上面说明的 SAMBA 发展缘由，你就应该不难知道， SAMBA 最初发展的主要目就是要用来沟通 Windows 与 Unix Like 这两个不同的作业平台，那么 SAMBA 可以进行哪些动作呢？想一想网芳能做的吧！

*   分享档案与打印机服务；
*   可以提供用户登入 SAMBA 主机时的身份认证，以提供不同身份者的个别数据；
*   可以进行 Windows 网络上的主机名解析 (NetBIOS name)
*   可以进行装置的分享 (例如 Zip, CDROM...)

底下我们来谈几个 SAMBA 服务器的应用实例吧！

*   利用软件直接编修 WWW 主机上面的网页数据

相信很多人都是利用个人计算机将网页制作完毕之后，再以类似 FTP 之类的服务将网页上传到 WWW 主机的， 但这样有个困扰，那就是同时在客户端与 WWW 主机上头都有一份网页数据，常常会忘记哪一份是最新的， 最麻烦的是，有时候下载下来的档案已经经过好多修改了，却在下次的 FTP 作业，不小心又下载一次旧数据， 结果将已经修改过的数据覆盖过去～天吶！又要重写一遍.....真是讨厌！

如果你有安装 SAMBA 服务器的设定的话，那么透过『网芳』的功能，直接联机远程服务器所提供的目录， 如此一来你可以直接在你的个人计算机上面修改主机的档案数据，只有一份正确的数据而已喔！ 这就有点像是『在线编修』呢，一修改完成，在 Internet 上面可以立刻检验，方便的很吶！

*   做成可直接联机的文件服务器

在鸟哥过去待过的实验室中，由于计算机数量不多，研究生常常会使用到不同的计算机 (因为大家都得抢没有人用的计算机啊！) ，此外，也常常有研究生拿自己的 NoteBook 来工作，因此，有些团队的数据就分散在各个计算机当中，使用上相当的不方便。 这个时候，鸟哥就使用 SAMBA 将硬盘空间分享出来，由于使用者要登入 SAMBA 这个服务器主机时需要输入用户数据 (账号与密码)，而不同的登入者会取得不一样的目录资源，所以可以避免自己的数据在公用计算机上面被窥视， 此外，在不同的公用计算机上面都可以登入 SAMBA 主机，数据的使用上面真是相当的棒啊！

*   打印机服务器

SAMBA 除了分享文件系统外，也可以分享打印机喔，鸟哥的研究室好几部计算机就是直接以 Linux 分享的打印机来印制报告的。你会说『啊 Windows 也可以办的到啊！没有什么了不起的！』是啊。 但是鸟哥认为，用 Linux 做为服务器主机时毕竟还是比较稳定一点，可以 24 小时且全年无休的努力工作吶。此外，因为目前透过『网络上的芳邻』来攻击局域网络的 Windows 操作系统的计算机病毒实在是太多了，防不胜防， Linux 对于这样的攻击并没有很大的影响 (因为常见的攻击手法均针对 Windows 而来～)，所以也比较安全一些说～

* * *

SAMBA 的应用挺广泛的，尤其对于局域网络内的计算机来说，更是一项不可多得的好用的服务器， 虽然或许你会说，SAMBA 的功能不过是模仿 Windows 的网芳以及 AD 相关的软件， 那我直接使用 Windows 不就 OK 了？可惜的是， Windows XP 对于网芳的联机限制依版本而有所不同， 以企业常见的专业版 (Professional) 来说，他仅能提供最多同时十个联机到网芳的联机能力，这...不太够用吧！ 所以啰，SAMBA 稳定、可靠又没有限制联机数，值得学习吧！^_^！更多的应用你可以自行发掘吶！

* * *

### 16.1.3 SAMBA 使用的 NetBIOS 通讯协议

事实上，[就像 NFS 是架构在 RPC Server 上面](http://linux.vbird.org/linux_server/0330nfs.php)一样， SAMBA 这个文件系统是架构在 NetBIOS (Network Basic Input/Output System, NetBIOS) 这个通讯协议上面所开发出来的。既然如此，我们当然就要了解一下 NetBIOS 啰！

最早 IBM 发展出 NetBIOS 的目的仅是要让局域网络内少数计算机进行网络链接的一个通讯协议而已， 所以考虑的角度并不是针对大型网络，因此，这个 NetBIOS 是无法跨路由的 (Router / Gateway)。这个 NetBIOS 在局域网络内实在是很好用， 所以微软的网络架构就使用了这个咚咚来进行沟通的吶！而 SAMBA 最早发展的时候，其实是想要让 Linux 系统可以加入 Windows 的系统当中来分享使用彼此的档案数据的，所以当然 SAMBA 就架构在 NetBIOS 发展出来啰。

不过 NetBIOS 是无法跨路由的，因此使用 NetBIOS 发展起来的服务器理论上也是无法跨越路由的呢！ 那么该服务器的使用范围不就受限相当的多了？好在，我们还有所谓的 NetBIOS over TCP/IP 的技术呢！这是什么样的技术啊？

举个例子来说好了，我们知道 TCP/IP 是目前网络连接的基本协议，现在我们将 NetBIOS 想成是一封明信片， 这个明信片只能让你自己欣赏而已，如果今天我们要将这个明信片送到远方的朋友那边时！就需要透过邮件系统 (例如邮局啦、国际快递啦等等的) 来传送了！这个 TCP/IP 就可以视为邮件传递系统啦！透过这个 NetBIOS over TCP/IP 的技术，我们就可以跨路由的使用 SAMBA 服务器所提供的功能咯！ 当然啦，目前 SAMBA 还是比较广泛的使用在 LAN 里面说。

**Tips:** 或许你会发现在 Windows 网络设定里面常常看到 NetBEUI 这个咚咚，那是什么呢？那个是 NetBIOS Extened User Interface 的简写，也是 IBM 在 NetBIOS 发展出来之后的改良版本。虽然这两者的技术不太相同， 不过，我们只要知道一些简单的概念就可以了！所以，在这里我们不针对 NetBEUI 来介绍。

![](img/vbird_face.gif)

* * *

### 16.1.4 SAMBA 使用的 daemons

NetBIOS 当初发展时就着眼在局域网络内的快速数据交流，而因为是定义在局域网络内，因此他并没有使用类似 TCP/IP 之类的传输协议，也就不需要 IP 的设定。如此一来数据如何在两部主机之间交流呢？ 其实主机在 NetBIOS 协议当中的定义为使用『NetBIOS Name』，每一部主机必须要有不同的 NetBIOS Name 才行， 而档案数据就是在不同的 NetBIOS name 之间沟通啰！我们以一个网芳的设定来作简单的说明好了：

1.  取得对方主机的 NetBIOS name 定位该主机所在：

    当我们想要登入某部 Windows 主机使用他所提供的档案数据时，必需要加入该 Windows 主机的群组 (Workgroup)，并且我们的机器也必需要设定一个主机名，注意喔，这个主机名跟 Hostname 是不一样的，因为这个主机名是架构在 NetBIOS 协议上的，我们可以简单的称呼他为 NetBIOS Name。在同一个群组当中，NetBIOS Name 必需要是独一无二的喔！

2.  利用对方给予权限存取可用资源：

    在我们找到该主机名后，是否能登入该对方主机或者是取用对方主机所提供的资源， 还要看对方 Windows 主机有没有提供我们使用的权限吶！所以，并不是登入该 Windows 主机之后我们就可以无限制的取用该主机的档案资源了。也就是说，如果对方主机允许你登入， 但是却没有开放任何资源让你取用，呵呵，登入主机也无法查看对方的硬盘里面的数据的啦！

我们的 SAMBA 则是透过两支服务来控制这两个步骤，分别是：

*   nmbd ：这个 daemon 是用来管理工作组啦、NetBIOS name 啦等等的解析。主要利用 UDP 协议开启 port 137, 138 来负责名称解析的任务；

*   smbd ：这个 daemon 的主要功能就是用来管理 SAMBA 主机分享的目录、档案与打印机等等。 主要利用可靠的 TCP 协议来传输数据，开放的端口为 139 及 445(不一定存在) 。

所以啰， SAMBA 每次启动至少都需要有这两个 daemons 喔！这可不要忘记啰！而当我们启动了 SAMBA 之后，主机系统就会启动 137, 138 这两个 UDP 及 139 这一个 TCP 埠口，这也不要忘记了！ 因为后面设定防火墙的时候，还会使用到这三个 port 的呢！

* * *

### 16.1.5 联机模式的介绍 (peer/peer, domain model)

SAMBA 服务器的应用相当的广泛，而且可以依照不同的网域联机方式，与不同的用户账号密码的控管方式来进行分类。 例如最常见的 Workgroup 及 Domain 两种方式的联机模式呢！底下我们就是要来谈一谈这两种最常见的局域网络的联机模式： peer/peer (对等模式) 及 domain model (主控模式)。

*   peer/peer (Workgroup model, 对等模式)：

peer 有同等、同辈的意思存在，所以由字面上来看，peer/peer 当然就是指两部主机的地位相等啰！ 这是什么意思呢？简单的说，假如在局域网络里面的所有 PC 均可以在自己的计算机上面管理自己的账号与密码， 同时每一部计算机也都具有独力执行各项软件的能力，只是藉由网络将各个 PC 链接在一起而已的一个架构， 所以，每一部机器都是可以独立运作的喔！

这样的架构在目前小型办公室里面是最常见的。例如办公室里面有十个人，每个人桌上可能都安装有一套 Windows 操作系统的个人计算机，而这十部计算机都可以独立进行办公室软件的执行啊、独立上网啊、独立玩游戏啊等等的， 因为这十部计算机都可以独立运作，所以不会有一部计算机关掉，其他的计算机就无法工作的情况发生，这就是 peer/peer 的典型架构。

那在这样的架构底下，要如何透过网络联机来取得对方的数据呢？举例来说，以下图的架构为例，在这样的架构下，假设 vbird (PC A) 写了一个报告书，而 dmtsai (PC B) 想要以网络直接取用这个报告书时，那 dmtsai 就必须要知道 vbird 使用的密码，并且 vbird 必须要在 PC A 上面启用 Windows 的『资源共享(或者是共享)』之后，才能够让 dmtsai 联机进入喔 (此时 PC A 为 Server) ！而且， vbird 可以随时依照自己的喜好来更改自己的账号与密码，而不受 dmtsai 的影响。不过，dmtsai 就得要取得 vbird 同意取得新的账号与密码后，才能够登入 PC A 喔！反过来说，同样的， vbird 要取得 dmtsai 的数据时，同样需要取得 PC B 的账号与密码后，才能够顺利登入啊 (此时 PC A 为 Client 喔)！因为 PC A, PC B 的角色与地位都同时可以为 Client 与 Server ，所以就是 peer/peer 的架构了！

![](img/model_peer.gif) 图 16.1-1、peer/peer 联机的示意图

使用 peer/peer 的架构的好处是每部计算机均可以独立运作，而不受他人的影响！不过， 缺点就是当整个网域内的所有人员都要进行数据分享时，光是知道所有计算机里面的账号与密码，就会很伤脑筋了！ 所以， Peer/Peer 的架构是比较适合 (1)小型的网域，或者是 (2)没有需要常常进行档案数据分享的网络环境，或者是 (3)每个使用者都独自拥有该计算机的拥有权(就是说，该计算机是用户的，而不是公用的啦！) 而，如果该单位的所有 PC 均是公有的 (例如学校的计算机教室环境)，而且你需要统一控管整个网域里面的账号与密码的话， 那就得使用底下的 domain models 了！

*   domain model (主控模式)

假设今天你服务的单位有 10 部计算机，但是你的单位有 20 个员工，这也就是说，这 20 个员工轮流抢着用这 10 部计算机。如果每部计算机都如同 peer/peer 的架构时，那么每部计算机都需要输入这 20 个员工的账号与密码来提供他们登入喔。而且，今天假如有个员工想要变更自己的密码时，就需要到 10 台计算机上面进行密码变更的作业！否则他就必须要记得这 10 部计算机里面，那一部计算机是记忆那一个密码...好烦那～

如果上述是这样的情况，使用 peer/peer 架构就不是一个好方法了！这个时候就需要藉由 domain model 来达成你的需求啦！所谓的 domain model 概念其实也很简单，既然使用计算机资源需要账号与密码， 那么我将所有的账号与密码都放置在一部主控计算机 (Primary Domain Controller, PDC) 上面，在我的网域里面，任何人想要使用任何计算机时，都需要在屏幕前方输入账号与密码，然后通通藉由 PDC 服务器的辨识后，才给予适当的权限。也就是说，不同的身份还具有不一样的计算机资源权限就是了！ 例如底下的图示：

![](img/model_domain.gif) 图 16.1-2、domain model 联机的示意图

PDC 服务器控管整个网域里面的各个机器 (PC A ~ PC D) 的账号与密码的信息，假如今天有个使用者账号名称为 vbird ，且密码为 12345 时，他不论使用哪一部计算机 (PC A ~ PC D) 只要在屏幕前方输入 vbird 与他的密码，则该机器会先到 PDC 上面查验是否有 vbird 以及 vbird 的密码，并且 PDC 主机会给予 vbird 这个用户相关的计算机资源权限。当 vbird 在任何一部主机上面登入成功后，他就可以使用相关的计算机资源了！

这样的架构比较适合人来人往的企业架构，当系统管理员要控管新进人员的计算机资源使用权时，可以直接针对 PDC 来修改就好了，不需要每一部主机都去修修改改的，对于系统管理员来说，这样的架构在控管账号资源上，当然是比较简单的啦！

各种架构适用的环境与适用的人都不相同，并没有那个是最好啦！请依照你的工作环境来选择联机的模式啰！当然， SAMBA 可以达到上述两种模式的啦！底下我们会分别来介绍喔！

* * *

# 16.2 SAMBA 服务器的基础设定

## 16.2 SAMBA 服务器的基础设定

SAMBA 这个软件几乎在所有的 Linux distributions 上面都有提供，因为即使你的 Linux 仅做为个人桌面计算机使用时， 你依旧可能会需要联机到远程的 Windows 网芳，那个时候就得要 samba 提供的客户端软件功能啰！ 因此你只要直接安装系统上面提供的默认 samba 版本即可。底下我们会先介绍 samba 服务器，然后再介绍客户端功能喔！

* * *

### 16.2.1 Samba 所需软件及其软件结构

目前常见的 samba 版本为 3.x 版，旧版的 2.x 版在设定上有点不一样，因此在进入设定前请先确认你的 samba 版本。 咱们的 CentOS 6.x 主要提供的是 Samba 3.x 的版本，不过也有释出 4.x 的版本 (samba4)，我们这里主要介绍的是预设的 3.x 版本的。那么你需要什么软件呢？基本上有这些：

*   samba： 这个软件主要提供了 SMB 服务器所需的各项服务程序 (smbd 及 nmbd)、 的文件档、以及其他与 SAMBA 相关的 logrotate 配置文件及开机默认选项档案等；

*   samba-client： 这个软件则提供了当 Linux 做为 SAMBA Client 端时，所需要的工具指令，例如挂载 SAMBA 文件格式的 mount.cifs、 取得类似网芳相关树形图的 smbtree 等等；

*   samba-common： 这个软件提供的则是服务器与客户端都会使用到的数据，包括 SAMBA 的主要配置文件 (smb.conf)、语法检验指令 (testparm) 等等；

这三个软件你都得要安装才行喔！如果尚未安装的话，使用 yum 去装好它吧！安装完毕之后，你可以依序察看一下 Samba 的软件结构喔！与它相关的配置文件基本上有这些：

*   /etc/samba/smb.conf： 这是 Samba 的主要配置文件，基本上，咱们的 Samba 就仅有这个配置文件而已，且这个配置文件本身就是很详细的说明文件了，请用 vim 去查阅它吧！主要的设定项目分为服务器的相关设定 (global)，如工作组、NetBIOS 名称与密码等级等， 以及分享的目录等相关设定，如实际目录、分享资源名称与权限等等两大部分。

*   /etc/samba/lmhosts： 早期的 NetBIOS name 需额外设定，因此需要这个 lmhosts 的 NetBIOS name 对应的 IP 檔。 事实上它有点像是 /etc/hosts 的功能！只不过这个 lmhosts 对应的主机名是 NetBIOS name 喔！不要跟 /etc/hosts 搞混了！目前 Samba 预设会去使用你的本机名称 (hostname) 作为你的 NetBIOS name，因此这个档案不设定也无所谓。

*   /etc/sysconfig/samba： 提供启动 smbd, nmbd 时，你还想要加入的相关服务参数。

*   /etc/samba/smbusers： 由于 Windows 与 Linux 在管理员与访客的账号名称不一致，例如： administrator (windows) 及 root(linux)， 为了对应这两者之间的账号关系，可使用这个档案来设定

*   /var/lib/samba/private/{passdb.tdb,secrets.tdb}： 管理 Samba 的用户账号/密码时，会用到的数据库档案；

*   /usr/share/doc/samba-<版本>： 这个目录包含了 SAMBA 的所有相关的技术手册喔！也就是说，当你安装好了 SAMBA 之后，你的系统里面就已经含有相当丰富而完整的 SAMBA 使用手册了！值得高兴吧！ ^_^，所以，赶紧自行参考喔！

至于常用的脚本文件案方面，若分为服务器与客户端功能，则主要有底下这几个数据：

*   /usr/sbin/{smbd,nmbd}：服务器功能，就是最重要的权限管理 (smbd) 以及 NetBIOS name 查询 (nmbd) 两个重要的服务程序；

*   /usr/bin/{tdbdump,tdbtool}：服务器功能，在 Samba 3.0 以后的版本中，用户的账号与密码参数已经转为使用数据库了！Samba 使用的数据库名称为 TDB (Trivial DataBase)。 既然是使用数据库，当然要使用数据库的控制指令来处理啰。tdbdump 可以察看数据库的内容，tdbtool 则可以进入数据库操作接口直接手动修改帐密参数。不过，你得要安装 tdb-tools 这个软件才行；

*   /usr/bin/smbstatus：服务器功能，可以列出目前 Samba 的联机状况， 包括每一条 Samba 联机的 PID, 分享的资源，使用的用户来源等等，让你轻松管理 Samba 啦；

*   /usr/bin/{smbpasswd,pdbedit}：服务器功能，在管理 Samba 的用户账号密码时， 早期是使用 smbpasswd 这个指令，不过因为后来使用 TDB 数据库了，因此建议使用新的 pdbedit 指令来管理用户数据；

*   /usr/bin/testparm：服务器功能，这个指令主要在检验配置文件 smb.conf 的语法正确与否，当你编辑过 smb.conf 时，请务必使用这个指令来检查一次，避免因为打字错误引起的困扰啊！

*   /sbin/mount.cifs：客户端功能，在 Windows 上面我们可以设定『网络驱动器机』来连接到自己的主机上面。在 Linux 上面，我们则是透过 mount (mount.cifs) 来将远程主机分享的档案与目录挂载到自己的 Linux 主机上面哪！

*   /usr/bin/smbclient：客户端功能，当你的 Linux 主机想要藉由『网络上的芳邻』的功能来查看别台计算机所分享出来的目录与装置时，就可以使用 smbclient 来查看啦！这个指令也可以使用在自己的 SAMBA 主机上面，用来查看是否设定成功哩！

*   /usr/bin/nmblookup：客户端功能，有点类似 nslookup 啦！重点在查出 NetBIOS name 就是了。

*   /usr/bin/smbtree：客户端功能，这玩意就有点像 Windows 系统的网络上的芳邻显示的结果，可以显示类似『靠近我的计算机』之类的数据， 能够查到工作组与计算机名称的树状目录分布图！

大致的软件结构就是这样，底下就准备来讲一个简单的案例吧！这样比较好介绍配置文件项目啦！

* * *

### 16.2.2 基础的网芳分享流程与 smb.conf 的常用设定项目

既然 Samba 是要加入 Windows 的网芳服务当中，所以它的设定方式应该是要与网芳差不多才是。所以我们先来聊一聊 Windows 的一些网芳设定方法再说。在早期 Windows 的网芳设定真是很简单，不过也因为太简单， 所以产生的安全问题可是相当的麻烦的。后来在 Windows XP 的 SP2 (服务包第二版) 之后加入了很多的预设防火墙机制， 因此使用网芳的预设限制常常会是这样的：

*   服务器与客户端之间必须要在同一个网域当中 (否则需要修改 Windows 预设防火墙)；
*   最好设定为同一工作组；
*   主机的名称不可相同 (NetBIOS name)；
*   专业版 Windows XP 最多仅能提供同时 10 个用户联机到同一台网芳服务器上。

工作组与主机名的设定，你可以在『我的计算机』右键单击，选择内容后去修订相关的设定值。当你的 Windows 主机群符合上述的条件后，就很容易处理网芳分享的工作啦！分享的步骤一般是这样的：

1.  叫出档案总管，然后在要分享的目录、磁盘或装置 (如打印机) 上面按下右键，选择『共享』，然后就能够设定好分享的数据了；
2.  最好建立一组给用户使用的账号与密码，让其他主机的用户可以透过该账号密码联机进入使用网芳分享的资源；

例题：假设你打开 Windows XP 的档案总管，在 D:\VBird\Data 这个目录下，你按下右键选『共享与安全性』，之后，在出现的窗口中， 你选择：『你了解这个安全风险，但仍不要执行精灵而共享档案，请按这里』，然后勾选：『在网络上共享这个文件夹』， 最后共享的名称你输入了：『VBGame』，请问，假设你的 IP 是 192.168.100.20 ，那么你的用户会看到什么网址列？答：网芳的资源名称通常的写法是：『 \IP\分享资源名称』，我们的分享资源名称为 VBGame，因此最终这个分享的资源名称应该是：『 \192.168.100.20\VBGame 』才对！很多朋友都会写成：『 \192.168.100.20\VBird\Game 』那错得很离谱喔！

真是有够简单的！那么 Samba 怎么设定啊？也是很简单，依据上述的限制以及流程你可以这样想象：

1.  服务器整体设定方面：在 smb.conf 当中设定好工作组、NetBIOS 主机名、密码使用状态 (无密码分享或本机密码) 等等；
2.  规划准备分享的目录参数：在 smb.conf 内设定好预计要分享的目录或装置以及可供使用的账号数据；
3.  建立所需要的文件系统：根据步骤 2 的设定，在 Linux 文件系统当中建立好分享出去的档案或装置，以及相关的权限参数；
4.  建立可用 Samba 的账号：根据步骤 2 的设定，建立所需的 Linux 实体账号，再以 pdbedit 建立使用 Samba 的密码；
5.  启动服务：启动 Samba 的 smbd, nmbd 服务，开始运转哩！

根据上面的流程，其实我们最需要知道的就是 smb.conf 这个配置文件的信息就是了。 所以首先我们就要来介绍一下这个档案的设定方式啰！这个档案其实可以分为两部份来看， 一个是主机信息部分，在 smb.conf 当中以 [global] (全领域) 作为设定的依据；另一个则是分享的信息， 以个别的目录名称为依据。另外，由于 Samba 主要是想加入网芳功能，因此在 smb.conf 内的很多设定都与 Windows 类似喔：

*   在 smb.conf 当中，井字号与分号 (# 跟 ;) 都是批注符号；
*   在这个配置文件中，大小写是没关系的！因为 Windows 没分大小写！

*   smb.conf 的服务器整体参数： [global] 项目

在 smb.conf 这个配置文件当中的设定项目有点像底下这样：

```
# 会有很多加上 # 或 ; 的批注说明，你也可以自行加上来提醒自己相关设定
[global]
   参数项目 = 设定内容
   ....

[分享资源名称]
   参数项目 = 设定内容
   .... 
```

在 [global] 当中的就是一些服务器的整体参数了，包括工作组、主机的 NetBIOS 名称、字符编码的显示、登录文件的设定、 是否使用密码以及使用密码验证的机制等等，都是在这个 [global] 项目中设定的。至于 [分享资源名称] 则是针对你开放的目录来进权限方面的设定，包括谁可以浏览该目录、是否可以读写等等参数。 在 [global] 部分关于主机名信息方面的参数主要有：

*   workgroup = 工作组的名称：注意，主机群要相同；
*   netbios name = 主机的 NetBIOS 名称啊，每部主机均不同；
*   server string = 主机的简易说明，这个随便写即可。

另外，过去常常让使用者心生不满的语系显示问题方面，你务必要清楚的知道的是，SAMBA 服务器上面的数据 (例如 mount 磁盘分区槽的参数以及原本的数据编码), SAMBA 服务器显示的语系, Windows 客户端显示的语系, Windows 客户端连上 SAMBA 的软件 都需要符合设定值才行！在新版的 3.x 上面有数个提供这些语系转换的设定喔，如下所示：

*   display charset = 自己服务器上面的显示编码， 例如你在终端机时所查阅的编码信息。一般来说，与底下的 unix charset 会相同。
*   unix charset = 在 Linux 服务器上面所使用的编码，一般来说就是 i18n 的编码啰！ 所以你必须要参考 /etc/sysconfig/i18n 内的『默认』编码。
*   dos charset = 就是 Windows 客户端的编码了！ 一般来说我们的繁体中文 Windows 使用的是 big5 编码，这个编码在 Samba 内的格式被称为『 cp950 』喔！

关于语系编码，建议你参考一下讨论区的这一篇：

*   [`phorum.vbird.org/viewtopic.php?t=22001`](http://phorum.vbird.org/viewtopic.php?t=22001)

我们的网友 eyesblue 写得太好了！所以建议大家直接前往查阅即可！在这里鸟哥将该文章内容作个例题来玩玩。

例题：假设你的 Samba 使用的语系 /etc/sysconfig/i18n 显示的是『 LANG="zh_TW.big5" 』， 而预计要分享的目标 Windows 系统是 XP，那么你的语系数据应该如何设定？答：由于 Linux, Windows XP 都使用 big5 编码，因此设定值应该是：

```
 unix charset    = cp950
        display charset = cp950
        dos charset     = cp950 
```

除此之外，还有登录文件方面的信息，包括这些参数：

*   log file = 登录档放置的档案，文件名可能会使用变量处理；
*   max log size = 登录档最大仅能到多少 Kbytes ，若大于该数字，则会被 rotate 掉。

还有网芳开放分享时，安全性程度有关的密码参数，包括这几个：

*   security = share, user, domain：三选一，这三个设定值分别代表：

    *   share：分享的数据不需要密码，大家均可使用 (没有安全性)；
    *   user ：使用 SAMBA 服务器本身的密码数据库，密码数据库与底下的 passdb backend 有关；
    *   domain：使用外部服务器的密码，亦即 SAMBA 是客户端之意，如果设定这个项目， 你还得要提供『password server = IP』的设定值才行；
*   encrypt passwords = Yes 代表密码要加密，注意那个 passwords 要有 s 才对！

*   passdb backend = 数据库格式，如前所述，为了加快速度， 目前密码文件已经转为使用数据库了！默认的数据库格式微 tdbsam ，而预设的档案则放置到 /var/lib/samba/private/passwd.tdb。

事实上 Samba 的密码方面设定值很多喔，包括你还可以利用 samba 来修改 /etc/passwd 里头的人物的密码呢！ 不过这个时候就得需要『 unix password sync 』以及『 passwd program 』这两个参数值的帮忙了。 我们这里先谈比较简单的，其他进阶的部分可以 man smb.conf 去进行搜寻查阅喔！ ^_^

*   分享资源的相关参数设定 [分享的名称]

这部分就是我们在前面的小范例当中说明的，要将 (1)哪个实际的目录 (2)分享成什么名称？中刮号里面放的是『分享名称』！ 那在这个分享名称内常见的参数有：

*   [分享名称] ：这个分享名称很重要，它是一个『代号』而已。记得回去看看 16.2.2 里面提到的那个范例；

*   comment ：只是这个目录的说明而已！

*   path ：这个分享名称实际会进入的 Linux 文件系统 (目录)。 也就是说，在网芳当中看到的是 [分享] 的名称，而实际操作的文件系统则是在 path 里头所设定的。

*   browseable ：是否让所有的用户看到这个项目？

*   writable ：是否可以写入？这里需要注意一下喔！那个 read only 与 writable 不是两个蛮相似的设定值吗？如果 writable 在这里设定为 yes ，亦即可以写入，如果 read only 同时设定为 yes ， 那不就互相抵触了！那个才是正确的设定？答案是：最后出现的那个设定值为主要的设定！

*   create mode 与 directory mode 都与权限有关的咯！

*   writelist = 使用者, @群组，这个项目可以指定能够进入到此资源的特定使用者。 如果是 @group 的格式，则加入该群组的使用者均可取得使用的权限，设定上会比较简单！

因为分享的资源主要与 Linux 系统的档案权限有关，因此里头的设定参数多与权限有关。

*   smb.conf 内的可用变量功能

为了简化设定值，Samba 提供很多不同的变量给我们来使用，主要有底下这几个变量喔：

*   %S：取代目前的设定项目值，所谓的『设定项目值』就是在 [分享] 里面的内容！举例来说，例如底下的设定范例：

    ```
    [homes]
       valid users = %S
       .... 
    ```

    因为 valid users 是允许的登入者，设定为 %S 表示任何可登入的使用者都能够登入的意思～今天如果 dmtsai 这个使用者登入之后，那个 [homes] 就会自动的变成了 [dmtsai] 了！这样可以明白了吗？ %S 的用意就是在替换掉目前 [ ] 里面的内容啦！

*   %m：代表 Client 端的 NetBIOS 主机名喔！
*   %M：代表 Client 端的 Internet 主机名喔！就是 HOSTNAME。
*   %L：代表 SAMBA 主机的 NetBIOS 主机名。
*   %H：代表用户的家目录。
*   %U：代表目前登入的使用者的使用者名称
*   %g：代表登入的使用者的组名。
*   %h：代表目前这部 SAMBA 主机的 HOSTNAME 喔！注意是 hostname 不是 NetBIOS name 喔！
*   %I：代表 Client 的 IP 咯。
*   %T：代表目前的日期与时间

以上就是在 smb.conf 上头常看到的几种设定项目，相信初次接触 Samba 的朋友，看到上头写的资料肯定是一头雾水的！ 我们底下用几个小范例来实际的介绍 smb.conf 的设定后，你就会知道这些参数如何应用了！ 记得，看完底下的下范例后，要回来再将这些参数的意义瞧一瞧， 而且若有其他额外的参数须知，务必自行 man smb.conf 喔！重要的很！

**Tips:** 时代变动太快，版本变动太多～要讲完所有的参数实在是很难的一件事～所以在这里鸟哥只讲一些常用的设定项目， 很多细项就得要靠各位看官自己努力了～文末也有列出很多 Samba 的在线资源，记得要查察看！

![](img/vbird_face.gif)

* * *

### 16.2.3 不需密码的分享 (security = share, 纯测试)

瞎密？不需要密码就能够使用 SAMBA 主机所提供的目录资源？真假？没错啦，可以达到的。不过，因为不需要密码就能够登入， 虽然你可以设定权限成为只读，让使用者可以『瞧瞧而已』，但是毕竟比较危险。 因为如果你不小心将重要数据放置到该分享的目录当中，岂不危险？所以尽量不要这样设定，所以标题才会讲：『纯测试』嘛！

*   0\. 假设条件

在底下的案例中，服务器 (192.168.100.254) 预计设定的参数状况为：

*   在 LAN 内所有的网芳主机工作组 (workgroup) 为： vbirdhouse
*   这部 Samba 服务器的 NetBIOS 名称 (netbios name) 为： vbirdserver
*   使用者认证层级设定 (security) 为： share
*   取消原本有放行的 [homes] 目录；
*   仅分享 /tmp 这个目录而已，且取名为： temp
*   Linux 服务器的编码格式假设为万国码 (Unicode, 亦即 utf8)
*   客户端为中文 Windows ，在客户端的软件也使用 big5 的编码

老实说， netbios name 几乎可以不用设定了，因为现在我们都用 IP 进行网芳联机，不一定会使用主机名嘛！ 所以这一版当中，鸟哥取消了 lmhosts 的设定值喔！好了，底下就开始依序来进行 samba 的设定吧！

*   1\. 设定 smb.conf 配置文件

由于我们有设定语系相关的数据，因此得要先查查看，到底我们 Linux 服务器的语系是否为 utf8 呢？检查方法如下：

```
[root@www ~]# cat /etc/sysconfig/i18n
LANG="zh_TW.UTF-8"  &lt;==确实是出现了 utf8 喔！ 
```

如上所示，确实是 utf8 啊！而在这个例子当中我们仅分享 /tmp 这个目录而已，而且假设这个分享出来的目录是可擦写的， 另外，我们并没有分享打印机喔！而在 smb.conf 当中的批注符号可以是『 # 』也可以是『 ; 』喔！要注意！

```
[root@www ~]# cd /etc/samba
[root@www samba]# cp smb.conf smb.conf.raw  &lt;==先备份再说！
[root@www samba]# vim smb.conf
# 1\. 先设定好服务器整体环境方面的参数
[global]
        # 与主机名有关的设定信息
        workgroup     = vbirdhouse
        netbios name  = vbirdserver
        server string = This is vbird's samba server

        # 与语系方面有关的设定项目喔，为何如此设定请参考前面的说明
        unix charset    = utf8
        display charset = utf8
        dos charset     = cp950

        # 与登录文件有关的设定项目，注意变量 (%m)
        log file = /var/log/samba/log.%m
        max log size = 50

        # 这里才是与密码有关的设定项目哩！
        **security = share**

        # 修改一下打印机的加载方式，不要加载啦！
        load printers    = no

# 2\. 分享的资源设定方面：主要得将旧的批注，新的加入！
#    先取消 [homes], [printers] 的项目，然后针对 /tmp 的设定，可浏览且可写入喔
[temp]                                     &lt;==分享资源名称
        comment    = Temporary file space  &lt;==简单的解释此资源
        path       = /tmp                  &lt;==实际 Linux 分享的目录
        writable   = yes                   &lt;==是否可写入？在此例为是的
        browseable = yes                   &lt;==能不能被浏览到资源名称
        &lt;u&gt;guest ok   = yes&lt;/u&gt;                   &lt;==单纯分享时，让用户随意登入的设定值 
```

请你特别留意，在原本的 smb.conf 上面就已经有很多默认值了，这些默认值如果你不知道他的用途， 尽量保留默认值，也可以使用 man smb.conf 去查询该默认值的意义。上述的设定是完全控制使用者的认证层级的呦！

*   2\. 用 testparm 查阅 smb.conf 的语法设定正确性

在启动 samba 之前，我们务必要了解到 smb.conf 里面语法是否正确，检验的方式使用 testparm 这个指令即可。 测试方式如下：

```
[root@www ~]# testparm
选项与参数：
-v ：查阅完整的参数设定，连同默认值也会显示出来喔！

[root@www ~]# testparm
Load smb config files from /etc/samba/smb.conf
Processing section "[temp]"   &lt;==看有几个中括号，若中刮号前出现讯息，则有错误
Loaded services file OK.
Server role: ROLE_STANDALONE
Press enter to see a dump of your service definitions &lt;==按 Enter 继续

[global]   &lt;==底下就是刚刚在 smb.conf 里头设定的数据！
        dos charset = cp950
        unix charset = utf8
        display charset = utf8
        workgroup = VBIRDHOUSE
        netbios name = VBIRDSERVER
        server string = This is vbird's samba server
        security = SHARE
        log file = /var/log/samba/log.%m
        max log size = 50
        load printers = No

[temp]
        comment = Temporary file space
        path = /tmp
        read only = No
        guest ok = Yes 
```

上头是语法验证与各个项目的列出，如果你下达 testparm 却出现如下画面那就是有问题：

```
[root@www ~]# testparm
Load smb config files from /etc/samba/smb.conf
Unknown parameter encountered: "linux charset" &lt;==中括号前为错误讯息！
Ignoring unknown parameter "linux charset"
Processing section "[temp]"
Loaded services file OK.
Server role: ROLE_STANDALONE
Press enter to see a dump of your service definitions 
```

如果发现上述的错误，这表示你的 smb.conf 有个『 linux charset 』的设定参数，不过 smb.conf 其实是不支持这个参数的。 可能的问题是 samba 2.x 与 samba 3.x 有一些项目的支持已经不存在了，所以你使用旧版的 2.x 配置文件来 3.x 上头执行时，就会出现问题。此外，『打字错误』也是很常见的一个问题吶！赶紧测试一下语法先， 然后根据 smb.conf 存在的项目去进行修改吧。

如果你想要了解 samba 的所有设定 (包括没有在 smb.conf 里头设定的默认值)，可以使用 testparm -v 来作详细的输出， 资料相当的丰富，透过这个你也可以知道你的主机环境设定为何呢！ ^_^

*   3\. 服务器端的服务启动与埠口观察

启动实在太简单了，利用预设的 CentOS 启动方式来处理即可。

```
[root@www ~]# /etc/init.d/smb start  &lt;==这一版开始要启动两个 daemon
[root@www ~]# /etc/init.d/nmb start
[root@www ~]# chkconfig smb on
[root@www ~]# chkconfig nmb on
[root@www ~]# netstat -tlunp &#124; grep mbd
Active Internet connections (only servers)
Proto Recv-Q Send-Q Local Address        Foreign Address State   PID/Program name
tcp        0      0 :::139               :::*            LISTEN  1772/smbd
tcp        0      0 :::445               :::*            LISTEN  1772/smbd
udp        0      0 192.168.1.100:137    0.0.0.0:*               1780/nmbd
udp        0      0 192.168.100.254:137  0.0.0.0:*               1780/nmbd
udp        0      0 0.0.0.0:137          0.0.0.0:*               1780/nmbd
udp        0      0 192.168.1.100:138    0.0.0.0:*               1780/nmbd
udp        0      0 192.168.100.254:138  0.0.0.0:*               1780/nmbd
udp        0      0 0.0.0.0:138          0.0.0.0:*               1780/nmbd 
```

特别注意，在 Samba 当中预设会启动多个端口，这包括数据传输的 TCP 端口 (139, 445)，以及进行 NetBIOS 名称解析之类工作的 UDP 埠口 (137, 138)，所以你才会看到很多数据的。那么能否仅支持 139 这个必要的埠口，关闭 445 呢？可以啊～透过 testparm -v 的观察，可以发现『 smb ports = 445 139 』这个设定值指定两个埠口的，因此你可以在 smb.conf 增加这个设定值，并改为 smb ports = 139 即可。不过，建议先保留默认值啦！

*   4\. 假设自我为客户端的检验 (默认用 lo 接口)

关于客户端的观察我们会在后续进行介绍。在这里仅是说明如何确定我们的 Samba 设定与服务有顺利的在运作。 我们可以在本机上透过 smbclient 这支程序来处理，它的基本查询语法是这样的：

```
[root@www ~]# smbclient -L [//主机或 IP] [-U 使用者账号]
选项与参数：
-L ：仅查阅后面接的主机所提供分享的目录资源；
-U ：以后面接的这个账号来尝试取得该主机的可使用资源 
```

由于在这个范例当中我们并没有规范用户的安全等级 (share)，所以不必使用 -U 这个选项，因此你可以这样看看：

```
[root@www ~]# smbclient -L //127.0.0.1 
Enter root's password: &lt;==因为不需要密码，因此这里单击 [Enter] 吧！
Domain=[VBIRDHOUSE] OS=[Unix] Server=[Samba 3.5.4-68.el6_0.2]

        Sharename       Type      Comment
        ---------       ----      -------
        temp            Disk      Temporary file space
        IPC$            IPC       IPC Service (This is vbird's samba server)
Domain=[VBIRDHOUSE] OS=[Unix] Server=[Samba 3.5.4-68.el6_0.2]

        Server               Comment
        ---------            -------
        VBIRDSERVER          This is vbird's samba server

        Workgroup            Master
        ---------            -------
        VBIRDHOUSE           VBIRDSERVER 
```

上表输出的信息当中，分享的目录资源 (Sharename) 就是在 smb.conf 当中设定的 [temp] 名称啰！ 因此在这里的意思是：任何人都可以进入 //127.0.0.1/temp 这个目录当中， 而这个目录在 Linux 系统其实是 /tmp 目录。至于那个 IPC$ 则是为了要应付 Windows 环境所必须要存在的项目就是了。那么该如何使用这个资源呢？接下来我们可以利用 mount 这个指令来测试看看啰：

```
[root@www ~]# mount -t cifs //127.0.0.1/temp /mnt
Password: &lt;==因为没有密码，所以这里还是按 Enter 即可
[root@www ~]# df
Filesystem           1K-blocks      Used Available Use% Mounted on
....(前面省略)....
//127.0.0.1/temp/      1007896     53688    903008   6% /mnt

[root@www ~]# cd /mnt
[root@www mnt]# ll  &lt;==以上这两个动作要进行！才会知道有没有权限的问题！
[root@www mnt]# touch zzz
[root@www mnt]# ll zzz /tmp/zzz
-rw-r--r--. 1 nobody nobody 0 Jul 29 13:08 /tmp/zzz
-rw-r--r--. 1 nobody nobody 0 Jul 29 13:08 zzz
# 注意喔！你进入 /mnt 身份会被压缩成为 nobody 呢！不再是 root 啊！

[root@www mnt]# cd ; umount /mnt 
```

确实可以挂载的起来，所以，测试完毕后，就将这个挂载的资料卸除吧。关于 mount 的用法，我们会在后面的小节继续介绍。

* * *

基本上，到此为止咱们就设定好一个简单的不需要密码即可登入的 Samba 服务器了！ 你可以先行到客户端软件功能的部分进行更细部的挂载测试。 接下来，让我们以简易的需要密码才能够登入 Samba 的方式来设计一个范例吧！

* * *

### 16.2.4 需账号密码才可登入的分享 (security = user)

设定一部不需密码即可登入的 Samba server 是非常简单的，不过， 你总不希望某些有机密性质的资料放在不设防的网芳中让大家查阅吧？ 举例来说，你总不希望你的家目录被人家随意浏览吧？家目录内可能有你自己的情书呢！^_^

那怎么办？没关系，我们可以透过 Samba 服务器提供的认证方式来进行用户权力的给予， 也就是说，你在客户端联机到服务器时，必须要输入正确的账号与密码后，才能够登入 Samba 查阅到你自己的数据！ 那会不会很难啊？不会啦！ Samba 本身就提供一个小程序来帮助我们处理密码的建立了，整个流程还不太难。

比较重要的是 Samba 使用者账号必须要存在于 Linux 系统当中 (/etc/passwd)， 但是 Samba 的密码与 Unix 的密码档案并不相同 (这是因为 Linux 与网芳的密码验证方式及编码格式不同所致)。 这就比较有点小麻烦～没关系，就让我们依样画葫芦来处理一下这个部分的设定吧！

*   0\. 假设条件

由于使用者层级会改变成 user 的阶段，因此 [temp] 已经没有必要存在！请将该设定删除或批注。 而服务器方面的整体数据则请保留，包括工作组等等的数据，并新增底下的资料：

*   使用者认证层级设定 (security) 为： user
*   用户密码档案使用 TDB 数据库格式，默认档案在 /var/lib/samba/private/ 内；
*   密码必须要加密；
*   每个可使用 samba 的使用者均拥有自己的家目录；
*   设定三个用户，名称为 smb1, smb2, smb3 ，且均加入 users 为次要群组。此三个用户 Linux 密码为 1234， Samba 密码则为 4321；
*   分享 /home/project 这个目录，且资源名称取名为： project；
*   加入 users 这个群组的使用者可以使用 //IP/project 资源，且在该目录下 users 这个群组的使用者具有写入的权限。

好了，开始一步步的处理吧！

*   1\. 设定 smb.conf 配置文件与目录权限相关之设定

在这个范例的配置文件当中，我们会新增几个参数，新增的参数部分会用特殊字体圈起来， 引用之前参数的部分则为一般字体。请交互参考看看啰：

```
# 1\. 开始设定重要的 smb.conf 档案呦！
[root@www ~]# vim /etc/samba/smb.conf
[global]
        workgroup       = vbirdhouse
        netbios name    = vbirdserver
        server string   = This is vbird's samba server
        unix charset    = utf8
        display charset = utf8
        dos charset     = cp950
        log file        = /var/log/samba/log.%m
        max log size    = 50
        load printers    = no

 # 与密码有关的设定项目，包括密码档案所在格式喔！
 security = user          &lt;==这行就是重点啦！改成 user 层级
        passdb backend = tdbsam  &lt;==使用的是 TDB 数据库格式！

# 2\. 分享的资源设定方面：删除 temp  加入 homes 与 project
[homes]                                   &lt;==分享的资源名称
        comment        = Home Directories
        browseable     = no               &lt;==除了使用者自己外，不可被其他人浏览
        writable       = yes              &lt;==挂载后可擦写此分享
        create mode    = 0664             &lt;==建立档案的权限为 664
        directory mode = 0775             &lt;==建立目录的权限为 775

[project]                                 &lt;==就是那三位使用者的共享资源
        comment    = smbuser's project
        path       = /home/project        &lt;==实际的 Linux 上面的目录位置
        browseable = yes                  &lt;==可被其他人所浏览到资源名称(非内容)
        writable   = yes                  &lt;==可以被写入
        write list = @users               &lt;==写入者有哪些人的意思

# 2\. 每次改完 smb.conf 你都需要重新检查一下语法正确否！
[root@www ~]# testparm  &lt;==详细的 debug 请自行处理啰！ 
```

在上表当中比较有趣的设定项目主要有：

*   [global] 修改与新增的部分：security 设定为 user 层级，且使用『passdb backend = tdbsam』这个数据库格式，因此密码文件会放置于 /var/lib/samba/private/ 内。 此外，默认密码就是加密的，因此不需要额外使用其他的设定参数来规范；

*   [homes] 这个使用者资源共享部分： homes 是最特殊的资源共享名称，因为 Linux 上面的每位用户均有家目录，例如 smb1 的家目录位于 /home/smb1/ ，那当 smb1 用户使用 samba 时，她就会发现多了个 //127.0.0.1/smb1/ 的资源可用，而 smb2 就在 //127.0.0.1/smb2/ 这个资源。由于不可浏览 (browseable)，所以除了使用者可以看到自己的家目录资源外，其他人是无法浏览的。此外，为了规范权限，而多了 create mode 与 directory mode 两个设定值 (此值可设定也可不理会)；

*   [project] 这个使用者资源共享部分：当我们新增一个共享资源时， 最重要的就是规范资源名称。在此例中我们使用 project 这个资源名称来指向 /home/project ，也就是说， //127.0.0.1/project 代表的是 /home/project 的意思。此外，能够使用这个资源的账号，为加入 users 这个群组的用户喔！ 透过 write list 这个项目比较单纯，如果是早期的设定，可能会使用 valid users ，但近来鸟哥比较偏好 write list 设定项目。 不过能否顺利的存取档案还与 Linux 最底层的档案权限有关。

千万不要忘记了，除了配置文件之外，详细的目录权限与账号设定等规范也要设定好！底下我们用范例来进行此项工作！

例题：我们预计要分享 /home/project 目录，这个目录的权限该如何设定？答：因为是要开放给 users 群组，而共享群组的权限通常是『 2770 』这个含有 SGID 的特殊旗标功能。因此这个目录应该如此设定才好：

```
[root@www ~]# mkdir /home/project
[root@www ~]# chgrp users /home/project
[root@www ~]# chmod 2770 /home/project
[root@www ~]# ll -d /home/project
drwxrws---. 2 root users 4096 Jul 29 13:17 /home/project 
```

*   2\. 设定可使用 Samba 的用户账号与密码

设定使用者账号是很重要的一环，因为设定错误的话，当然也就任何人都没有办法登入的！在这里我们必须先要说明一下 Linux 的文件系统与 SAMBA 设定的使用者登入权限的相关性！

*   在 Linux 这个系统下，任何程序都需要取得 UID 与 GID (User ID 与 Group ID) 的身份之后，才能够拥有该身份的权限，也才能够适当的进行存取档案等动作！

*   关于 Linux 这个系统的 UID 与 GID 与账号的相对关系，一般记录在 /etc/passwd 当中，当然也能透过 NIS, ldap 等方式来取对应；

*   SAMBA 仅只是 Linux 底下的一套软件，使用 SAMBA 来进行 Linux 文件系统时，还是需要以 Linux 系统下的 UID 与 GID 为准则！

如果上面这几点说明你没有问题了，现在就来看一下当我们在 Windows 计算机上面以网络上的芳邻来连接 Linux 并且进行数据的存取时，会是怎样的一个情况呢？

我们需要透过 SAMBA 所提供的功能来进行 Linux 的存取，而 Linux 的存取是需要取得 Linux 系统上面的 UID 与 GID 的，因此，我们登入 SAMBA 服务器时，所利用 SAMBA 取得的其实是 Linux 系统里面的相关账号！这也就是说，**在 SAMBA 上面的使用者账号，必须要是 Linux 账号中的一个！**

所以说，在不考虑 NIS 或 LDAP 等其他账号的验证方式，单纯以 Linux 本机账号 (/etc/passwd) 作为身份验证时， 在 Samba 服务器所提供可登入的账号名称，必须要存在于 /etc/passwd 当中！ 这是一个很重要的概念！例如你要先有 dmtsai 在 /etc/passwd 当中后，才能将 dmtsai 加入 Samba 的使用者当中。这都是很基本的账号权限概念，如果你觉得这里阅读方面有问题， 若不考虑鸟哥的解释不良，表示你必须要回去读读基础篇了～ ^_^

现在我们知道需要新增 smb1, smb2, smb3 三个用户，且这三个用户需要加入 users 群组。此外，我们之前还建立过 student 这个用户，假设这四个人都需要能用 Samba 服务，那么除了新增用户之外，我们还需要利用 pdbedit 这个指令来处理 Samba 用户功能喔！

```
# 1\. 先来建立所需要的各个账号，但假设 student 已经存在了喔！
[root@www ~]# useradd -G users smb1
[root@www ~]# useradd -G users smb2
[root@www ~]# useradd -G users smb3
[root@www ~]# echo 1234 &#124; passwd --stdin smb1
[root@www ~]# echo 1234 &#124; passwd --stdin smb2
[root@www ~]# echo 1234 &#124; passwd --stdin smb3

# 2\. 使用 pdbedit 指令功能
[root@www ~]# pdbedit -L [-vw]            &lt;==单纯的察看帐户信息
[root@www ~]# pdbedit -a&#124;-r&#124;-x -u 账号    &lt;==新增/修改/删除账号
[root@www ~]# pdbedit -a -m -u 机器账号   &lt;==与 PDC 有关的机器码
选项与参数：
-L ：列出目前在数据库当中的账号与 UID 等相关信息；
-v ：需要搭配 -L 来执行，可列出更多的讯息，包括家目录等数据；
-w ：需要搭配 -L 来执行，使用旧版的 smbpasswd 格式来显示数据；
-a ：新增一个可使用 Samba 的账号，后面的账号需要在 /etc/passwd 内存在者；
-r ：修改一个账号的相关信息，需搭配很多特殊参数，请 man pdbedit；
-x ：删除一个可使用 Samba 的账号，可先用 -L 找到账号后再删除；
-m ：后面接的是机器的代码 (machine account)，与 domain model 有关！

# 2.1 开始新增使用者吧！
[root@www ~]# pdbedit -a -u smb1
new password: &lt;==输入 4321 这个密码瞧瞧
retype new password: &lt;==再输入一次吧！
Unix username:        smb1   &lt;==底下为输入正确后的显示结果！
NT username:
Account Flags:        [U          ]
User SID:             S-1-5-21-4073076488-3046109240-798551845-1000
Primary Group SID:    S-1-5-21-4073076488-3046109240-798551845-513
Full Name:
Home Directory:       \\vbirdserver\smb1
HomeDir Drive:
Logon Script:
Profile Path:         \\vbirdserver\smb1\profile
Domain:               VBIRDSERVER
Account desc:
Workstations:
Munged dial:
Logon time:           0
Logoff time:          9223372036854775807 seconds since the Epoch
Kickoff time:         9223372036854775807 seconds since the Epoch
Password last set:    Fri, 29 Jul 2011 13:19:56 CST
Password can change:  Fri, 29 Jul 2011 13:19:56 CST
Password must change: never
Last bad password   : 0
Bad password count  : 0
Logon hours         : FFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFF
# 你可以发现其实讯息非常的多！若需修改细部设定，请 man pdbedit 吧！
[root@www ~]# pdbedit -a -u smb2
[root@www ~]# pdbedit -a -u smb3
[root@www ~]# pdbedit -a -u student

# 2.2 查询目前已经存在的 Samba 账号
[root@www ~]# pdbedit -L
smb1:2004:
smb3:2006:
smb2:2005:
student:505:
# 仅会列出账号与 UID 而已呦！

# 2.3 尝试修改与删除 smb3 这个账号看看
[root@www ~]# smbpasswd smb3
New SMB password: 
Retype new SMB password:
# 修改密码比较特殊，管理密码参数是使用 pdbedit，修改密码得要用 smbpasswd 呦！

[root@www ~]# pdbedit -x -u smb3
[root@www ~]# pdbedit -Lw
# 此时你就看不到 smb3 这个用户啰！所以测试完请立即将它加回来！ 
```

以后如果有需要新增额外的使用者账号，若该账号原本不存在，则使用 useradd 再以 pdbedit -a 去新增。 若已经存在于 Linux 的实体账号，直接用 pdbedit -a 新增即可。同时要注意，管理 TDB 数据库格式建议使用 pdbedit 这个新的玩意儿来处理，smbpasswd 仅剩下修改密码的功能需记忆即可！

*   3\. 重新启动 Samba 并进行自我测试

在经过重新启动后，我们所进行的修订才会生效。然后使用 smbclient 来检查看看，是否不同身份会有不一样的浏览结果呢？ 赶紧看看：

```
[root@www ~]# /etc/init.d/smb restart
[root@www ~]# /etc/init.d/nmb restart

# 1\. 先用匿名登录试看看！
[root@www ~]# smbclient -L //127.0.0.1
Enter root's password:      &lt;==直接按下 [Enter] 即可。
Anonymous login successful  &lt;==有看到匿名的字样了！
Domain=[VBIRDHOUSE] OS=[Unix] Server=[Samba 3.5.4-68.el6_0.2]

        Sharename       Type      Comment
        ---------       ----      -------
        project         Disk      smbuser's project
        IPC$            IPC       IPC Service (This is vbird's samba server)
....(底下省略)....

# 2\. 再使用 smb1 这个账号登入试看看！
[root@www ~]# smbclient -L //127.0.0.1 -U smb1
Enter smb1's password:  &lt;==输入 smb1 在 pdbedit 所建立的密码！
Domain=[VBIRDHOUSE] OS=[Unix] Server=[Samba 3.5.4-68.el6_0.2]

        Sharename       Type      Comment
        ---------       ----      -------
        project         Disk      smbuser's project
        IPC$            IPC       IPC Service (This is vbird's samba server)
        smb1            Disk      Home Directories &lt;==多了这玩意儿！
....(底下省略).... 
```

由上表我们可以发现，经由不同的身份登入可以取得不一样的浏览数据， 所以在使用上面需要特别留意喔！接下来，让我们开始来自我挂载测试看看！

```
[root@www ~]# mount -t cifs //127.0.0.1/smb1 /mnt -o username=smb1
Password: &lt;==确定是输入正确的密码喔！
# 此时 /home/smb1/ 与 /mnt 应该拥有相同的档名才对！因为挂载嘛！

[root@www ~]# ll /home/smb1/.bashrc
-rw-r--r--. 1 smb1 smb1 124 May 30 23:46 /home/smb1/.bashrc &lt;==确定有档案
[root@www ~]# ls -a /mnt
# 却看不到任何东西！应该是 SELinux 的问题吧！根据 /var/log/messages 的讯息，
# 进行如下的动作就能够处理好这个程序！

[root@www ~]# setsebool -P samba_enable_home_dirs=1
[root@www ~]# ls -a /mnt
.  ..  .bash_logout  .bash_profile  .bashrc  .gnome2  .mozilla
# 档名出现啦！OKOK！这个使用者挂载处理完毕！

[root@www ~]# umount /mnt 
```

自我测试是非常重要的！因为 Samba 是会对外提供服务的，因此 SELinux 会特别『关照』一下这个服务！包括默认用户家目录不会有开放的权限、预设的 SELinux type 不对就无法使用 (你可以自己尝试挂载 //127.0.0.1/project 就知道啥原因啰！)，所以，自行测试完毕就能够理解哪个地方的 SELinux 没有设定妥当！详细的设定请到 16.2.6 安全性设定去查阅。

**Tips:** 根据网友回报，因为之前我们设定的 security 是 share，而且已经使用 Windows 系统测试过，在同一部 Windows 系统上面重复测试时，会发生无法登入的情况。建议直接将 windows 系统重新启动清除前一次登入的信息即可！ ^_^

![](img/vbird_face.gif)

*   4\. 关于权限的再说明与累加其他分享资源的方式：

有的时候你会发现，明明在 smb.conf 当中已经设定了 writable 可写入，使用者登入的身份也没有问题， 为啥就是无法挂载或写入呢？是否是服务器设定哪里还有问题啊？非也非也！主要的问题常常是来自于 Linux 文件系统的权限啦！

举上面的例子来说，当你无法挂载却发现 Linux 传统权限是对的，那么肯定是 SELinux 出问题～这部份得要用 setsebool 与 chcon 或 restorecon 等指令来克服。另外就是，我们在 smb.conf 当中设定 [project] 为可写入，亦即 /home/project 是可写入的。假设 smb1 属于 users 这个群组，因此以 smb1 登入 SAMBA 服务器后，对于 /home/project 应该是具有可以读写的能力的！但是，如果你以 root 的身份建立 /home/project 却又忘记修改权限的话， 此时 /home/project 是无法让 users 这个群组写入的，因此 smb1 这个使用者当然不具有写入的能力。 这样说，了解鸟哥想要说啥了吗？注意注意喔！ ^_^

那如果你还要扩充分享的目录与能够登入的使用者时，可以这样做：

*   利用编辑 smb.conf 来开放其他的目录资源，并且特别注意 Linux 在该目录下的权限喔！请使用 chown 与 chmod 吧！
*   利用 pdbedit 来新增其他可用 Samba 的账号，如果该账号并没有出现在 /etc/passwd 里面，请先以 useradd 新增该账号；
*   不论进行完任何的设定，请先以 testparm 进行确认，之后以 /etc/init.d/{smb,nmb} restart 来重新启动！

事实上，SAMBA 的一般用途就是在这个联机的模式中！多使用 SAMBA 来分享你的资源吧！鸟哥都是使用 SAMBA 来做为远程服务器与我的工作机互通有无的重要媒介说～

* * *

### 16.2.5 设定成为打印机服务器 (CUPS 系统)

时至今日，打印机的网络功能已经很强悍了！甚至也有支持无线网络的打印机，因此每台打印机都可以独立作为各个 PC 的独自的打印机，老实说也没有必要进行 Samba 的网络打印机服务器啦！但毕竟还是有些比较旧型的机种， 或者买不起有内建网络的打印机时，那么 Samba 的打印机服务器还是有存在的价值啰。

在 Linux 底下进行打印的服务很多，不过我们这里要介绍的仅有目前较广为流行的 CUPS (Common Unix Printing System) 这一个。 详细的 CUPS 安装设定方法我们已经在[基础篇第三版第二十一章 CUPS](http://linux.vbird.org/linux_basic/0610hardware.php#cups) 当中提过，所以这里我们不再详细说明，仅介绍大致的处理流程就是了。如果你需要较早期的 LPRng 打印系统的话， 建议可以参考底下的数据喔：

*   依玛猫的打印文件：[`www.imacat.idv.tw/tech/lnxprint.html`](http://www.imacat.idv.tw/tech/lnxprint.html)
*   鸟哥的 LPRng 简介：[`linux.vbird.org/linux_server/0370samba/0370samba.php`](http://linux.vbird.org/linux_server/0370samba/0370samba.php#server_printer_lprng)

**Tips:** 在这个小节中，鸟哥假设你的打印机并不是网络打印机，而是使用 USB 接口连接的打印机格式。如果你的打印机真的有支持网络， 那建议直接参考打印机手册来设定即可，不需要安装 Samba 打印机。因为某些厂牌的打印机网络卡有特殊的功能， 例如 HP 的网卡通常还支持某些特殊的打印功能 (双面、多页打印等)，这些功能透过伺服器重新分享时，可能会遗失！

![](img/vbird_face.gif)

*   0\. 假设条件

既然要分享打印机，就得要有打印机啊！鸟哥使用对 Linux 支持度较高的 HP LaserJet P2015dn 这部打印机为例， 不使用网络功能，单纯使用 USB 连接到 Samba 服务器上。

*   CUPS 连接到 USB 打印机，并且开放非本机的 IP 来源使用此打印机；
*   使用 CUPS 内建的打印机驱动程序；
*   前往 HP 打印机官网取得 Windows 操作系统的驱动程序；

*   1\. 安装打印机与确定打印机的联机正常

再次说明，并不是所有的打印机都被 Linux 所支持的，所以当你想要链接一部打印机到 Linux 系统上头时， 请务必到 [`www.openprinting.org/printers`](http://www.openprinting.org/printers) 上头去看看是否有被支持喔！ 如果没有被支持，那就换一部打印机吧！不要进行垂死的挣扎了...

如果你的打印机端口为使用 USB 或者是平行串行端口的话，那么当你连接上打印机后， 可以利用底下的方式测试看看是否成功的连接上了：

```
[root@www ~]# lsusb
Bus 001 Device 002: ID 03f0:3817 Hewlett-Packard LaserJet P2015 series
Bus 001 Device 001: ID 1d6b:0002 Linux Foundation 2.0 root hub
[root@www ~]# ll /dev/usb/lp0
crw-rw----. 1 root lp 180, 0 Jul 29 13:55 /dev/usb/lp0
# 看得出来，已经有个 lp0 的打印机啰！测试打印一下吧！

[root@www ~]# echo "Hello printer" &gt; /dev/usb/lp0 
```

如果打印机有响应，这表示 OK 的啦！你可以进行底下的工作了。

*   2\. 设定 CUPS 与打印机的联机

预设 CUPS 都会开启，不过，因为我们安装的是『 basic server 』的模式，所以 CUPS 默认并没有被安装起来。 所以这里要安装且重新设定与启动才行。本章节 CUPS 的设定原则是这样的：

*   我需要让 192.168.100.0/24 这个网域可以使用打印机
*   我需要让 192.168.100.0/24 及 127.0.0.0/8 可以管理 CUPS 系统

然后开始这样做：

```
[root@www ~]# yum groupinstall "Print Server"
[root@www ~]# vim /etc/cups/cupsd.conf
# 1\. 让监听的接口开放在所有接口！
# Listen localhost:631  &lt;==约在第 18 行左右，改成如下：
Listen 0.0.0.0:631

# 2\. 让内部网域能够进行 CUPS 的浏览与管控
&lt;Location /&gt;        &lt;==约在 32 行左右，新增能够让内网其他 IP 浏览者
  Order allow,deny
 Allow From 127.0.0.0/8
  Allow From 192.168.100.0/24
&lt;/Location&gt;

&lt;Location /admin&gt;       &lt;==约在 39 行左右，新增能够管理 CUPS 者
  Encryption Required   &lt;==因为这里的关系，所以可能会用 https://IP 喔！
  Order allow,deny
 Allow From 127.0.0.0/8
  Allow From 192.168.100.0/24
&lt;/Location&gt; 
```

设定完毕后就可以开始来启动 cups 系统，可以这样做：

```
[root@www ~]# /etc/init.d/cups start
[root@www ~]# chkconfig cups on
[root@www ~]# netstat -tunlp &#124; grep 'cups'
tcp     0  0  0.0.0.0:631         0.0.0.0:*      LISTEN      1851/cupsd
udp     0  0  0.0.0.0:631         0.0.0.0:*                  1851/cupsd 
```

那个 631 的埠口就是 CUPS 所启动的啦！要注意的是，开放界面得要给 0.0.0.0 才对呦！然后我们可以开始设定打印机了！ 由于 CUPS 支持很多不同的打印机端口，每种端口都不一样，常见的有：

*   USB 端口： usb:/dev/usb/lp0
*   网络打印机： ipp://ip/打印机型号
*   网络芳邻打印机： smb://user:password@host/printer

之所以要加上 192.168.100.0/24 可以控制服务器 CUPS 的原因在于...鸟哥的服务器没有 X 窗口啦！ 所以需要透过平时的工作机连上服务器才行啊！此时，将 CUPS 开放在区网内可以控制的功能就很重要啦！ 此外，因为鸟哥的主机所在环境问题，这部 192.168.100.254 还有一个界面为 192.168.1.100，鸟哥在 cupsd.conf 里面也加入这个网段了 (上面的范例中并没有特别强调)，所以底下的图示你会看到很多 192.168.1.100 的 IP ，不要害怕！那是正常的！ ^_^！好了，请打开浏览器，在网址列输入： [`192.168.100.254:631`](https://192.168.100.254:631) (底下则是 192.168.1.100)

![](img/centos6_cups_usb_01.gif) 图 16.2-1、用 CUPS 设定 USB 打印机

如上图所示，由于我们使用的是 https 这个需要凭证的联机模式，因此就会出现这个不受信任的网站讯息。 没关系，你直接按下『我了解安全风险』后，再选择『新增例外网站』即可出现如下图示：

![](img/centos6_cups_usb_02.gif) 图 16.2-2、用 CUPS 设定 USB 打印机

如果这部主机真的是你的，那么就选择箭头 2 所指的那个『永久储存』吧！最后按下箭头 3 所指的『确认安全例外』即可！如果一切顺利，就会出现如下的 CUPS 设定图示：

![](img/centos6_cups_usb_03.gif) 图 16.2-3、用 CUPS 设定 USB 打印机

在上头的欢迎图示当中，由于我们是想要建立打印机，因此点选箭头(1)所指的那个按钮进入打印机功能，然后点选 (2)来建立打印机吧！

![](img/centos6_cups_usb_04.gif) 图 16.2-4、用 CUPS 设定 USB 打印机

这一版比较有趣的地方，是会先让你输入账号与密码才进行后续的动作哩！所以这里请输入 root 的帐密吧！

![](img/centos6_cups_usb_05.gif) 图 16.2-5、用 CUPS 设定 USB 打印机

在上面的图示中，你应该要选择的是我们这部本机的 USB 打印机装置才对。该装置是由 HAL 服务所自动侦测到的， 如果你没有看到任何 USB 的打印机，那可能就得要查询一下打印机电源是否正确的开启了！ 点选他吧！

![](img/centos6_cups_usb_06.gif) 图 16.2-6、用 CUPS 设定 USB 打印机

建立打印机时，最重要的是那个打印队列 (上面方框中的第一个，名称的那个玩意儿)，在这里鸟哥使用 CUPS 预设帮我捉到的档名。 这个名称很重要，是未来分享出的打印机名字啰！至于位置与描述就随便你填啰。由于我们是想要做成打印服务器，所以『share this printer 』当然要勾选！当你按下『继续』后，就会出现如下图示：

![](img/centos6_cups_usb_07.gif) 图 16.2-7、用 CUPS 设定 USB 打印机

接下来 CUPS 会帮你选择一个相对较佳的驱动程序，基本上，使用 CUPS 帮你捉到的预设驱动程序应该就 OK 了！ 选完后请按下『加入打印机』按钮吧！

![](img/centos6_cups_usb_08.gif) 图 16.2-8、用 CUPS 设定 USB 打印机

看你还有没有要修改其他的预设参数，如果没有的话，就按下图 16.2-8 的『Set Default Options』按钮吧！ 如果一切没有问题，你的打印机就设定妥当了。如果想要查阅打印机的详细信息，那可以点选 Printer 的项目！如下图所示：

![](img/centos6_cups_usb_09.gif) 图 16.2-9、用 CUPS 设定 USB 打印机

如果都正常没问题，那么你的系统已经有一部打印机被 CUPS 所管理，且这部打印机在网络的网址为：

*   [`服务器 IP:631/printers/打印机队列名称`](http://服务器 IP:631/printers/打印机队列名称)
*   [`192.168.100.254:631/printers/HP_LaserJet_P2015_Series`](http://192.168.100.254:631/printers/HP_LaserJet_P2015_Series)

接下来看看如何将它连结到咱们的 Samba 服务器中吧！

*   3\. 在 smb.conf 当中加入打印机的支持 (Optional)

开始告诉 Samba 将这部打印机给他分享出去吧！你需要这样处理：

```
[root@www ~]# vim /etc/samba/smb.conf
[global]
        # 得要修改 load printers 的设定，然后新增几个数据
        load printers = yes
        cups options  = raw       &lt;==可支持来自 Windows 用户的打印作业
        printcap name = cups
        printing      = cups      &lt;==与上面这两个在告知使用 CUPS 打印系统

[printers]                        &lt;==打印机一定要写 printers 喔！
        comment = All Printers
        path    = /var/spool/samba&lt;==预设把来自 samba 的打印作业暂时放置的队列
        browseable = no           &lt;==不被外人所浏览啦！有权限才可浏览
        guest ok   = no           &lt;==与底下两个都不许访客来源与写入(非文件系统)
        writable   = no
        printable  = yes          &lt;==允许打印很重要的一项工作！

[root@www ~]# testparm  &lt;==若有错误，请自行处理一下
[root@www ~]# /etc/init.d/smb restart
[root@www ~]# /etc/init.d/nmb restart 
```

基本上透过这样的设定你的 Samba 就能够顺利的提供打印机的服务了！不过可惜的是，Windows 客户端依旧得要安装打印机的驱动程序才能够使用 Samba 所提供的打印机，此时真是麻烦兼讨厌啊～有没有可能让 Samba 主动的提供驱动程序给使用者，这样一来客户端就不需要额外去找驱动程序啰！是可以的，透过 Samba 3.x 即可处理！就这么巧， CentOS 的 Samba 就是 3.x 呢！所以我们可以透过底下的方式来处理。

*   4\. 让 Samba 主动提供驱动程序给 Windows 用户使用

另外，或许你会想，打印机的型号这么多，那么 Linux 该如何提供这些打印机的驱动程序啊？岂不麻烦？ 还好啦， CUPS 主要是透过利用 Postscript 的打印语言与打印机沟通的，因此客户端只要取得 postscript 的驱动程序他们就能够使用咱们的 Samba 服务器所提供的打印机了！如此一来， 不论打印机的型号为何，只要他们能够支持 Postscript 的打印格式，OK 搞定！而且 CUPS 官网本身就有提供 CUPS 的 Postscript 驱动程序啰！可以到底下的连结去下载：

*   支持 CUPS 的软件：[`www.cups.org/software.php`](http://www.cups.org/software.php)

很棒的是，因为我们是 CentOS 6.x 有支持 rpm 软件封装的系统，因此可以直接下载 cups-windows-6.0-1.i386.rpm 这个档案即可，直接安装这个 rpm 档案就能够取得 cups 对 Windows 的打印机驱动程序了。 这个档案安装完毕之后，会将驱动程序放置于 /usr/share/cups/drivers/ 里头呦！ 不过你得要注意的是，除了这个驱动程序外，要支持 Windows 2000 以后出产的 Windows 版本， 你还得到 Windows XP 底下的目录去下载几个 32 位支持的档案：

*   Win XP 32 位：C:\WINDOWS\system32\spool\drivers\w32x86\3

将该目录下里面的 PS 开头的档案通通下载下来，应该有四个档案的，请将他复制成为档名小写的档案，并且放置到你 Samba 服务器上的 /usr/share/cups/drivers/ 目录下，该目录内放置的这就是基本的驱动程序说！ 在鸟哥的这个目录底下至少含有这几个档案就是了：

```
[root@www ~]# ll /usr/share/cups/drivers
-rw-r--r-- 1 root root    803  4 月 20  2006 cups6.inf
-rw-r--r-- 1 root root     72  4 月 20  2006 cups6.ini
-rw-r--r-- 1 root root  12568  4 月 20  2006 cupsps6.dll
-rw-r--r-- 1 root root  13672  4 月 20  2006 cupsui6.dll  &lt;==上面为 cups 提供
-rw-r--r-- 1 root root 129024  3 月 24 13:29 ps5ui.dll    &lt;==底下为 Win XP 提供
-rw-r--r-- 1 root root 455168  3 月 24 13:29 pscript5.dll
-rw-r--r-- 1 root root  27568  3 月 24 13:29 pscript.hlp
-rw-r--r-- 1 root root 792644  3 月 24 13:29 pscript.ntf 
```

上述的档案鸟哥将他打包成为一个档案了，你可以在底下的连结下载：

*   [`linux.vbird.org/linux_server/0370samba/cups-samba-windowsxp.tgz`](http://linux.vbird.org/linux_server/0370samba/cups-samba-windowsxp.tgz)

不过你得注意，这个档案内的 Windows 数据是由 32 位的 Windows XP 上面捉来的，所以对于 Windows 98/ME 是没有作用的。同时，对于 64 位的其他较晚期的 Windows 7 等系统可能就得要重新处理了！ 你得自行上网查阅相关的数据下载方式喔。接下来我们必须要在 smb.conf 里面增加一笔新的分享数据，这个分享数据必须是 [print$] 名称才行！有点类似这样啦：

```
[root@www ~]# vim /etc/samba/smb.conf
[global]
....(设定保留原本数据)....
[homes]
....(设定保留原本数据)....
[printers]
....(设定保留原本数据)....
[print$]
        comment    = Printer drivers
        path       = /etc/samba/drivers  &lt;==存放打印机驱动程序的目录
        browseable = yes
        guest ok   = no
        read only  = yes
        write list = root                &lt;==这个驱动程序的管理员
[project]
....(设定保留原本数据)....

[root@www ~]# mkdir /etc/samba/drivers
[root@www ~]# chcon -t samba_share_t /etc/samba/drivers
# 由于预设的 CUPS 仅有 root 能管理，因此我们以 root 作为打印机管理员；
# 同时 SELinux 的类型也要修订如上的方式！那 root 就得要加入 samba 的支持才行：
[root@www ~]# pdbedit -a -u root

[root@www ~]# testparm                 &lt;==测试语法
[root@www ~]# /etc/init.d/smb restart  &lt;==重新启动

[root@www ~]# smbclient -L //127.0.0.1 -U root
Enter root's password:  &lt;==输入 root 在 samba 的密码先
Domain=[VBIRDHOUSE] OS=[Unix] Server=[Samba 3.5.4-68.el6_0.2]

        Sharename       Type      Comment
        ---------       ----      -------
        print$          Disk      Printer drivers
        project         Disk      smbuser's project
        HP_LaserJet_P2015_Series Printer   HP LaserJet P2015 Series
        IPC$            IPC       IPC Service (This is vbird's samba server)
        root            Disk      Home Directories
# 瞧！有看到一部打印机以及驱动程序所在的分享数据啰！ 
```

现在我们要告知 Samba 说，我们的 CUPS 可提供 Windows 客户端的驱动程序，所以用户不需要自行设定他们的驱动程序哩！ 要由 cups 告知 Samba 是由 cupsaddsmb 这个指令来搞定的，整个指令的执行很简单的：

```
[root@www ~]# cupsaddsmb [-H SAMBA 服务器名] [-h CUPS 服务器名] \
&gt;   -a -v [-U 使用者账号]
选项与参数：
-H ：后续接的是 Samba 服务器名，本机的话可以直接用 localhost 即可；
-h ：后续接的为 CUPS 的服务器名，同样的可使用 localhost 即可；
-a ：自动搜寻出所有可用的 CUPS 打印机；
-v ：列出更多的信息；
-U ：打印机管理员

# 利用前面的说明将打印机驱动程序挂上 SAMBA (注意 CUPS 管理员预设是 root)
[root@www ~]# cupsaddsmb -H localhost -U root -a -v
Password for root required to access localhost via SAMBA: &lt;==root 在 SAMBA 密码
# 这里会闪过很多的讯息，说明已经安装了某些信息，底下鸟哥仅列出简单的讯息而已。
Running command: smbclient //localhost/print$ -N -A /tmp/cupsbrdBaE -c 'mkdir 
W32X86;put /tmp/cupsu13OSU W32X86/HP_LaserJet_P2015_Series.ppd;...

[root@www ~]# ll /etc/samba/drivers
drwxr-xr-x. 3 root root 4096 Jul 29 15:15 W32X86  &lt;==这就是驱动程序目录 
```

最后在驱动程序的存放目录会多出一个 W32X86 的目录，你可以查询一下该目录的内容， 那就是预计要给客户端使用的驱动程序啦！这样就搞定了！不过，为了将所有的数据通通驱动， 建议你将 CUPS 及 SAMBA 通通重新启动吧！

```
[root@www ~]# /etc/init.d/cups restart
[root@www ~]# /etc/init.d/smb restart
[root@www ~]# /etc/init.d/nmb restart 
```

*   5\. 一些问题的克服：

如果一切顺利的话，你在 Windows 客户端应该可以顺利的连接到打印机啰！ 开心吧！不过，如果你曾经印错数据，那么该如何进入 Linux 的 Samba 主机将该数据移除呢？ 你最好知道底下的几个指令，关于这些指令的进阶用法则请自行给他 man 看看了：

```
# 1\. 列出所有可用的打印机状态
[root@www ~]# lpstat -a
HP_LaserJet_P2015_Series accepting requests since Fri 29 Jul 2011 02:55:28 PM CST

# 2\. 查询目前默认打印机的的工作情况
[root@www ~]# lpq
hpljp2015dn 已就绪
没有项目
# 列出打印机的工作，若有打印作业存在时 (例如关掉打印机再印测试页)，会如下所示：
hpljp2015dn 已就绪并正在打印
等级    拥有人  工作    档案                            总计  大小
active  root    2       Test Page                       17408 byte

# 3\. 删除所有的工作项目喔！
[root@www ~]# lprm -
# 加上那个减号 (-) 代表移除所有等待中的打印作业！ 
```

打印作业就是这样进行的啦！赶紧试看看吧！接下来探讨一下相关的防火墙与安全性的讨论！

* * *

### 16.2.6 安全性的议题与管理

使用 SAMBA 其实是有一定程度的危险性的，这是因为很多网络攻击的蠕虫、病毒、木马就是透过网芳来攻击的！ 为了抵挡不必要的联机，所以 CentOS 5.x 预设的 SELinux 已经关闭了很多 Samba 联机的功能， 因此预设情况下，很多客户端的挂载可能会有问题。此外，仅开放有权限的网域来源，以及透过 smb.conf 来管理特定的权限，也是很重要的！同时，Linux 文件系统的 r, w, x 权限也是需要注意的喔！ 我们底下就简单的介绍一下一些基本的安全性管理吧！

*   SELinux 的相关议题：

其实就如同[第七章 (7.4.5)](http://linux.vbird.org/linux_server/0210network-secure.php#selinux_log) 里面提到的，我们透过登录档的内容就能够知道如何解决 SELinux 对各个服务所造成的问题了。不过，既然我们知道服务是 Samba 了，能不能找出与 Samba 有关的 SELinux 规则呢？当然可以！基本的 Samba 规则主要有：

```
[root@www ~]# getsebool -a &#124; grep samba
samba_domain_controller --&gt; off  &lt;==PDC 时可能会用到
samba_enable_home_dirs --&gt; off   &lt;==开放用户使用家目录
samba_export_all_ro --&gt; off      &lt;==允许只读文件系统的功能
samba_export_all_rw --&gt; off      &lt;==允许读写文件系统的功能
samba_share_fusefs --&gt; off
samba_share_nfs --&gt; off
use_samba_home_dirs --&gt; off      &lt;==类似用户家目录的开放！
virt_use_samba --&gt; off 
```

看吧！几乎所有的规则默认都是关闭的！所以我们需要慢慢的打开啊！目前我们仅会用到用户的家目录以及分享成为可擦写， 不过似乎仅要 samba_enable_home_dirs 那个项目设定妥当即可喔！因此我们可以这样做：

```
[root@www ~]# setsebool -P samba_enable_home_dirs=1
[root@www ~]# getsebool -a &#124; grep samba_enable_home
samba_enable_home_dirs --&gt; on 
```

这样用户挂载他们的家目录时 (例如 smb1 使用 //127.0.0.1/smb1/) 就不会出现无法挂载的怪问题了！此外， 由于分享成为 Samba 的目录还需要有 samba_share_t 的类型。那我们还有分享 /home/project 还记得吗？那个目录也需要修订喔！ 这样做看看：

```
[root@www ~]# ll -Zd /home/project
drwxrws---. root users unconfined_u:object_r:home_root_t:s0 /home/project

[root@www ~]# chcon -t samba_share_t /home/project
[root@www ~]# ll -Zd /home/project
drwxrws---. root users unconfined_u:object_r:samba_share_t:s0 /home/project 
```

如果你分享的目录不只是 Samba ，还包括 FTP 或者是其他的服务时，那可能就得要使用 public_content_t 这个大家都能够读取的类型才行！若你还有发现任何 SELinux 的问题，请依照 /var/log/messages 里面的信息去修订吧！

*   防火墙议题：利用 iptables 来管理

最简单的管理登入 SAMBA 的方法就是透过 iptables 啦！详细的说明我们已经在[第九章防火墙](http://linux.vbird.org/linux_server/0250simple_firewall.php)中提过了，所以这里不再详加说明。 要知道的是，如果你仅要针对底下的范围开放 Samba 时，可以这样想：

*   仅针对 192.168.100.0/24, 192.168.1.0/24 这两个网域开放 SAMBA 使用权
*   SAMBA 启用的 port UDP: 137, 138 及 TCP: 139, 445；

所以 iptables.allow 规则当中应该要加入这几项：

```
[root@www ~]# vim /usr/local/virus/iptables/iptables.allow
# 加入底下这几行！
iptables -A INPUT -i $EXTIF -p tcp -s 192.168.100.0/24 -m multiport \
         --dport 139,445 -j ACCEPT
iptables -A INPUT -i $EXTIF -p tcp -s 192.168.1.0/24 -m multiport \
         --dport 139,445 -j ACCEPT
iptables -A INPUT -i $EXTIF -p udp -s 192.168.100.0/24 -m multiport \
         --dport 137,138 -j ACCEPT
iptables -A INPUT -i $EXTIF -p udp -s 192.168.1.0/24 -m multiport \
         --dport 137,138 -j ACCEPT
[root@www ~]# /usr/local/virus/iptables/iptables.rule 
```

这是很简单很简单的防火墙规则，你必须要依据你的环境自行修改 (通常修改那个 192.168.1.0/24 网段即可！)。 由于 smbd 及 nmbd 并不支持 TCP Wrappers ，所以你也只能透过 iptables 来控制了～

*   防火墙议题： 透过内建的 Samba 设定 (smb.conf)

事实上 Samba 已经有许多防火墙机制啦！那就是在 smb.conf 内的 hosts allow 及 hosts deny 这两个参数。 通常我们只要使用 hosts allow 即可，那么没有写入这个设定项目的其他来源就会被拒绝联机的！这是比较严格的设定。 举例来说，如果你只想要让本机、192.168.100.254, 192.168.100.10, 192.168.1.0/24 使用 SAMBA 而已，那么可以这样写：

```
[root@www ~]# vim /etc/samba/smb.conf
[global]
        # 跟防火墙的议题有关的设定
        hosts allow = 127\. 192.168.100.254 192.168.100.10 192.168.1.
[homes]
....保留原始设定....
[root@www ~]# testparm
[root@www ~]# /etc/init.d/smb restart 
```

这个设定值的内容支持部分比对，因此 192.168.1.0/24 只要写出前面三个 IP 段即可 (192.168.1.)。 如此一来不但只有数部主机可以登入我们的 SAMBA 服务器，而且设定值又简单！不像 iptables 写的落落长～ 鸟哥建议在防火墙议题方面，只要使用 iptables 或 hosts allow 其中一项即可，当中又以 hosts allow 较为建议唷！ 当然啦，如果你是针对区网开放的，那么设定 iptables 防火墙反而是比较好的呦！因为不需要更动到 smb.conf 配置文件嘛！让服务的设定变的比较单纯些～

*   文件系统议题：利用 Quota 限制用户磁盘使用

既然网芳是要分享文件系统给用户的，那么想当然尔，各个 Samba 用户们确实会将数据放置到你的 Samba 服务器上嘛！ 那万一单个用户随便上传个数百 GB 的容量到你的 Samba 服务器，而且常常给你随意存取一番， 会不会造成文件系统分配不公或者是带宽方面的问题呢？想想就觉得是『会嘛！』那怎办？就透过 Quota 磁盘配额啊！ 磁盘配额我们在[基础篇第三版第十五章](http://linux.vbird.org/linux_basic/0420quota.php)已经谈过，在本书[第一章 (1.2.2-3)](http://linux.vbird.org/linux_server/0105beforeserver.php#server_os_3) 里面也已经有实作过， 在底下请你依据第一章的后续动作来处理吧！

例题：我们预计分配 smb1, smb2, smb3 在他们自己的家目录下，各拥有 300MB/400MB (soft/hard) 的磁盘配额限量，那该如何做？答：请先依据第一章的 Quota (1.2.2-3) 相关数据处理完：

*   /etc/fstab 加入 /home 挂载点的 usrquota,grpquota 等设定值；
*   重新挂载 /home ，让 Quota 实际被支持；
*   以 quotacheck -avug 建立 Quota 的数据库档案；
*   启动 Quota ；

假若你已经于第一章就处理完毕了，那么这一题就非常简单喔！透过 edquota -u smb1 来处理即可！

```
[root@www ~]# edquota -u smb1
Disk quotas for user smb1 (uid 2004):
  Filesystem                blocks    soft    hard inodes  soft  hard
  /dev/mapper/server-myhome       0 300000  400000      0     0     0

[root@www ~]# edquota -p smb1 smb2
[root@www ~]# edquota -p smb1 smb3
[root@www ~]# repquota -ua
*** Report for user quotas on device /dev/mapper/server-myhome
Block grace time: 7days; Inode grace time: 7days
                        Block limits                File limits
User            used    soft    hard  grace    used  soft  hard  grace
----------------------------------------------------------------------
smb1      --      32  300000  400000              9     0     0
smb2      --      32  300000  400000              8     0     0
smb3      --      32  300000  400000              8     0     0 
```

* * *

### 16.2.7 主机安装时的规划与中文扇区挂载

现在你知道 Samba 服务器的功能是用来作为文件服务器的，每个使用者都可以拥有家目录，并透过网芳的功能来链接到 Samba 服务器中。这就有个问题啦，那就是你的使用者如果太多，并且将他们的重要数据都放到这部 Samba 服务器上头的话，那肯定 /home 未来会有点不足啊！所以 /home 所在的磁盘或许可以使用大一点的硬盘， 或者使用磁盘阵列，使用 [LVM (基础学习篇第三版十五章)](http://linux.vbird.org/linux_basic/0420quota.php#lvm) 也是个不错的方案。 底下为简单的思考方向：

*   在安装 Linux 的时候，建议不需要安装 X Window ；
*   在规划 Linux 时，/home 最好独立出一个 partition ，而且硬盘空间最好能够大一些；
*   /home 独立出来的 partition 可以单独进行 quota 的作业，以规范用户的最大硬盘用量；
*   无网卡的打印机 (USB) 可直接链接到 Linux 主机再透过 Samba 分享；
*   由于 SAMBA 一般来说都仅针对内部 (LAN) 主机进行开放，所以，可能的话 SAMBA 主机直接使用 private IP 来设定即可，当然啦， SAMBA 是否使用 private IP 还得视你的整个网域的 IP 网段的特性来规划。 以鸟哥研究室来说，因为实验室所有计算机的 IP 都是 Public IP，那么 SAMBA 如果使用 Private IP 反而会让大家都无法连接上啊！ ^_^
*   如果你的 SAMBA 主机使用 Public IP 时，请特别留意规范好防火墙的设定，尽量仅让 LAN 内的计算机可以联机进来即可，不要对 Internet 开放喔！

另外，如果你的 Samba 服务器需要挂载含有中文的 partition 时，譬如说你将原本 Windows XP 的 FAT32 文件系统挪到 Linux 系统下，此时如果用一般模式来挂载该分割槽时，一些中文档名可能会无法被顺利的显示出来。 这个时候你就得需要这样做了：

```
mount -t vfat -o iocharset=big5,codepage=950 /dev/sd[a-p][1-15] /mount/point 
```

其中 iocharset 指的是本机的语系编码方式， codepage 则与远程软件有关。因为我们是在本机进行挂载， 所以实际上使用 iocharset 这个参数即可啦！更多说明则请看下节的客户端设定部分啰！

* * *

# 16.3 Samba 客户端软件功能

## 16.3 Samba 客户端软件功能

现在你已经架设好了 Samba 服务器啦！有服务器当然要有客户端来使用才是好的服务器嘛！不然要这个服务器干嘛？ 而我们假设局域网络内有 Windows/Linux 系统，这两种系统都是透过 NetBIOS over TCP/IP 来连上 Samba 服务器的， 在设定之前你必须要知道的有几件事：

*   在区网内的主机最好具有相同的工作组，且具有不同的主机名；
*   Windows XP pro. 最多仅能允许十个用户同时连接到自己的网芳；
*   你可以在网芳当中看到的通常是相同群组的主机；
*   可以使用『搜寻』-->『计算机』-->『输入 IP』来查到 Samba 主机；
*   Windows 的网芳预设仅有同一 IP 网段的主机才能登入 (Windows 防火墙设定)！

接下来咱们就分别依照 Windows 及 Linux 系统来做说明吧！

* * *

### 16.3.1 Windows 系统的使用

在 Windows 上面的搜寻网络上的网芳主机实在挺简单的，你有好几种方法可以处理：

*   打开『档案总管』，『网络上的芳邻』、『整个网络』、『Microsoft Windows Network』 就能看到属于你群组的所有计算机主机了！

*   『开始』、『搜寻』、『档案或文件夹』、『计算机或人员』、『网络上的计算机』，然后在出现的方框当中填写正确的 IP ，按下『搜寻』即可！这个方法可以适用于不在同一个群组当中的网络主机喔！

*   如果是 Windows 7 的话，只要点选文件夹即可。

举例来说，如果想要连接到我们的 Samba 主机的话，而又不知到这部 Samba 主机的 NetBIOS name ， 那利用搜寻的结果会有点类似如下的图示：

![](img/centos6_win7_1.gif) 图 16.3-1、Windows 7 客户端搜寻示意图

上图左侧先点选『网络』，然后到右上方的框框中，输入 NetBIOS name，若不知道的话，就留白让 Windows 7 自己找。 如上图所示，就有找到三部网络主机啊！我们来点选一下 VBIRDSERVER 吧！ 因为要登入人家服务器，所以就被要求要输入密码。 如下图所示，请填写好你所拥有的账号与密码吧！

![](img/centos6_win7_2.gif) 图 16.3-2、Windows 7 客户端登入 SAMBA 服务器示意图

若顺利登入系统了，那么就能够看到如下的图示，就是取得该服务器的可用资源啦！因为我们并没有针对 Windows 7 提供打印机的驱动程序，那部份先略过。我们现在来将 project 挂载成本机磁盘试看看：

![](img/centos6_win7_3.gif) 图 16.3-3、Windows 7 客户端登入 SAMBA 服务器示意图

如上图所示，在 project 上面右键单击，选择『联机网络驱动器机』，就会出现如下的画面让你去选择挂载磁盘驱动器的参数喔：

![](img/centos6_win7_4.gif) 图 16.3-4、Windows 7 客户端挂载网络驱动器机的示意图

你可以自己调整想要的驱动器号，例如预设的 Z 槽， 那么以后你的档案总管中就会生出一个 Z 槽，该磁盘槽就代表 \192.168.100.254\project 那个分享的目录啰！

*   让 Windows 系统的网芳支持不同网域的 IP 联机

由于网芳的资安问题越来越严重，因此 Windows XP 之后的版本都预设仅开放本机 IP 网域的网芳联机而已。 如果你的 Windows 想要让别人可以在 Internet 或不同的 IP 网段对你联机时，你就得修改一下防火墙的设定啊！ 请叫出控制台，然后点选『Windows 防火墙』就会出现如下的图示了：

![](img/centos6_win7_5.gif) 图 16.3-5、Windows 7 服务器防火墙示意图

因为我们得要细部设定防火墙，因此点选上图中左侧的『进阶设定』来取得如下图示吧！

![](img/centos6_win7_6.gif) 图 16.3-6、Windows 7 服务器防火墙示意图

还记得网络是双向的吧？所以，我们得先要针对输入 (从外部连到本机) 的规则来处置。如上图所示，按下 (1)输入规则，然后点选 (2)档案及打印机共享，之后到 (3)选择详细的规则内容，会出现另外一个窗口，在 (4)点选『领域』的部分来设定不同网段， 最终在 (5)的地方『新增』可进入本机的远程 IP 网段喔！按下新增会出现如下图示喔：

![](img/centos6_win7_7.gif) 图 16.3-7、Windows 7 服务器防火墙示意图

如上图所示，在 (1)填写正确的 IP 或网段，然后按下 (2)确定后，就能够在 (3)的框框当中出现可联机的远程服务器啰！

*   透过 port 445 的特殊登入方式

如果你知道 Samba 服务器有启用 port 445 ，并且他已经分享了某个目录时，举例来说，我们的 192.168.100.254 有分享出 project 这个分享资源名称时，那么这个目录的完整写法为：『 \192.168.100.254\project 』， 我们可以透过『开始』出现的那个方框来处理这个玩意儿！如下所示：

![](img/centos6_win7_8.gif) 图 16.3-8、Windows 7 透过 port 445 联机

如果可以登入的话就会顺利登入，否则就会弹出一个要你输入账号密码的窗口，输入正确的数据即可！ 呼呼！真过瘾～除此之外，我们还可以登入别人 Windows 主机的 C 或 D 槽喔！写法则变成这样：

*   \192.168.100.20\c$

所以说，怕了吧！俺还真害怕～所以啊，Samba 没必要时，那个 port 445 应该是可以关闭的吧！

* * *

### 16.3.2 Linux 系统的使用

*   smbclient：查询网芳分享的资源，以及使用类似 FTP 的方式上传/下载网芳

咱们的 Samba 有提供 Linux 网芳的客户端功能喔！也就是说 Linux 可以挂载 Samba 服务器也能挂载 Windows 提供的网芳啦！主要是透过 smbclient 来观察，再以 mount 来挂载文件系统哩。先来介绍一下 smbclient 这个指令吧：

```
# 1\. 关于查询的功能，例如查出 192.168.100.254 的网芳数据
[root@clientlinux ~]# smbclient -L //[IP&#124;hostname] [-U username]
[root@clientlinux ~]# smbclient -L //192.168.100.254 -U smb1
Enter smb1's password:
Domain=[VBIRDHOUSE] OS=[Unix] Server=[Samba 3.5.4-68.el6_0.2]

        Sharename       Type      Comment
        ---------       ----      -------
        project         Disk      smbuser's project
        print$          Disk      Printer drivers
        IPC$            IPC       IPC Service (This is vbird's samba server)
        HP_LaserJet_P2015_Series Printer   HP LaserJet P2015 Series
        smb1            Disk      Home Directories &lt;==等一下用这个当范例
Domain=[VBIRDHOUSE] OS=[Unix] Server=[Samba 3.5.4-68.el6_0.2]

        Server               Comment
        ---------            -------
        VBIRDSERVER          This is vbird's samba server

        Workgroup            Master
        ---------            -------
        VBIRDHOUSE           VBIRDSERVER
# 从这里可以知道在目前网域当中有多少个工作组与主要的名称解析主机 
```

除了这个先前用过的查询功能之外，我们可以这样简易使用网芳的：

```
# 2\. 利用类似 FTP 的方式登入远程主机
[root@clientlinux ~]# smbclient '//[IP&#124;hostname]/资源名称' [-U username]
# 意思是使用某个账号来直接登入某部主机的某个分享资源，举例如下：
[root@clientlinux ~]# smbclient '//192.168.100.254/smb1' -U smb1
Enter smb1's password:
Domain=[VBIRDHOUSE] OS=[Unix] Server=[Samba 3.5.4-68.el6_0.2]
smb: \&gt; dir
# 在 smb: \&gt; 底下其实就是在 //192.168.100.254/dmtsai 这个目录底下啦！所以，
# 我们可以使用 dir, get, put 等常用的 ftp 指令来进行数据传输了！
?   :列出所有可以用的指令，常用！
cd  :变换到远程主机的目录
del :杀掉某个档案
lcd :变换本机端的目录
ls  :察看目前所在目录的档案
dir :与 ls 相同
get :下载单一档案
mget:下载大量档案
mput:上传大量档案
put :上传单一档案
rm  :删除档案
exit:离开 smbclient 的软件功能
# 其他的指令用法请参考 man smbclient 喔！ 
```

*   mount.cifs：直接挂载网芳成为网络驱动器机

事实上，使用 smbclient 一点也不方便，因为使用的是 ftp 的功能语法，有点怪怪的～能不能像 Windows 那样，可以直接联机网络驱动器机啊？这当然没有问题！不过就需要藉由 mount.cifs 来协助了！

早期的 Samba 主要是提供 smbmount 或 mount.smbfs 这个指令来挂载 (smbfs 是 SMB filesystem 的缩写)， 不过这个指令已经被可以进行比较好的编码判断的 mount.cifs 所取代啦！mount.cifs 可以将远程服务器分享出来的目录整个给他挂载到本机的挂载点，如此一来， 远程服务器的目录就好像在我们本机的一个分割槽一样喔！可以直接执行复制、编辑等动作！这可就好用的多了！ 底下我们来谈一谈怎么用这个 mount.cifs 吧！

```
[root@clientlinux ~]# mount -t cifs //IP/分享资源 /挂载点 [-o options]
选项与参数：
-o 后面接的参数 (options) 常用的有底下这些：
   username=你的登入账号：例如 username=smb1
   password=你的登入密码：需要与上面 username 相对应啊！
   iocharset=本机的语系编码方式，如 big5 或 utf8 等等；
   codepage=远程主机的语系编码方式，例如繁体中文为 cp950

# 范例一：以 smb1 的身份将其家目录挂载至 /mnt/samba 中
[root@clientlinux ~]# mkdir /mnt/samba
[root@clientlinux ~]# mount -t cifs //192.168.100.254/smb1 /mnt/samba \
&gt; -o username=smb1,password=4321,codepage=cp950
[root@clientlinux ~]# df
文件系统               1K-区段      已用     可用 已用% 挂载点
//192.168.100.254/smb1/
                       7104632    143368   6606784   3% /mnt/samba 
```

经由 mount 的动作，我们就可以轻易的将远程分享出来的咚咚给他挂载到自己 Linux 本机上面！好用的很～更详细的 mount 用法，请 man mount！

*   nmblookup：查询 NetBIOS name 与 IP 及其他相关信息：

现在我们可以透过一些 NetBIOS 相关的功能来取得 NetBIOS name ，不过，如果你还想要知道这个 NetBIOS name 的其他信息时， 例如 IP、分享的资源等等，那可以使用 nmblookup 这个指令来搞定即可。他是这么使用的：

```
[root@clientlinux ~]# nmblookup [-S] [-U wins IP] [-A IP] name
选项与参数：
-S ：除了查询 name 的 IP 之外，亦会找出该主机的分享资源与 MAC 等；
-U ：后面一般可接 Windows 的主要名称管理服务器的 IP ，可与 -R 互用；
-R ：与 -U 互用，以 Wins 服务器来查询某个 Netbios name；
-A ：相对于其他的参数， -A 后面可接 IP ，藉 IP 来找出相对的 NetBIOS 数据；

# 范例一：藉由 192.168.100.254 找出 vbirdserver 这部主机的 IP 地址
[root@clientlinux ~]# nmblookup -U 192.168.100.254 vbirdserver
querying vbirdserver on 192.168.100.254
192.168.100.254 vbirdserver&lt;00&gt;
192.168.1.100 vbirdserver&lt;00&gt;    &lt;==之前鸟哥就说有两个 IP 嘛！俺的主机！

# 范例二：找出 vbirdserver 的 MAC 与 IP 等信息：
[root@clientlinux ~]# nmblookup -S vbirdserver
querying vbirdserver on 192.168.100.255  &lt;==在区网内广播开始找！
192.168.100.254 vbirdserver&lt;00&gt;          &lt;==找到 IP 啰！
Looking up status of 192.168.100.254
        VBIRDSERVER     &lt;00&gt; -         B &lt;ACTIVE&gt;
        ..__MSBROWSE__. &lt;01&gt; - &lt;GROUP&gt; B &lt;ACTIVE&gt;
        VBIRDHOUSE      &lt;00&gt; - &lt;GROUP&gt; B &lt;ACTIVE&gt; 
```

*   smbtree：网络上的芳邻浏览器显示模式！

如果你想要使用类似 Windows 上面，可以一看就明了各个网芳所分享的资源时，你能使用 smbtree 来直接查询喔！ 这个指令更简单！直接输入就能用：

```
[root@clientlinux ~]# smbtree [-bDS]
选项与参数：
-b ：以广播的方式取代主要浏览器的查询
-D ：仅列出工作组，不包括分享的资源
-S ：列出工作组与该工作组下的计算机名称 (NetBIOS) 不包括各项资源目录

# 范例一：列出目前的网芳树状相关图
[root@clientlinux ~]# smbtree
Enter root's password:  &lt;==直接按 [Enter] 即可！
WORKGROUP
        \\WIN7-PC
VBIRDHOUSE
        \\WINXP
cli_start_connection: failed to connect to WINXP&lt;20&gt; (0.0.0.0). 
        \\VBIRDSERVER                   This is vbird's samba server
             \\VBIRDSERVER\HP_LaserJet_P2015_Series  HP LaserJet P2015 Series
             \\VBIRDSERVER\IPC$     IPC Service (This is vbird's samba server)
             \\VBIRDSERVER\print$   Printer drivers
             \\VBIRDSERVER\project  smbuser's project

[root@clientlinux ~]# smbtree -S
Enter root's password:
WORKGROUP
        \\WIN7-PC
VBIRDHOUSE
        \\WINXP
        \\VBIRDSERVER                   This is vbird's samba server
# 此时仅有工作组与计算机名称而已呢！ 
```

*   smbstatus：观察 SAMBA 的状态

其实这个指令算是服务器的相关功能啦！因为它主要的目的是查阅目前 SAMBA 有多少人来联机， 且哪些资源共享已经被使用等等的信息。所以如果你想要使用这个软件，请先安装 samba 喔！简单用法如下：

```
[root@www ~]# smbstatus [-pS] [-u username]
选项与参数：
-p ：列出已经使用 SAMBA 联机的程序 PID ；
-S ：列出已经被使用的资源共享状态；
-u ：只列出某个用户相关的分享数据

# 范例一：列出目前主机完整的 Samba 状态
[root@www ~]# smbstatus
Samba version 3.5.4-68.el6_0.2
PID     Username      Group         Machine
-------------------------------------------------------------------
5993      smb1          smb1          __ffff_192.168.100.10 (::ffff:192.168.100.10)
5930      smb1          smb1          win7-pc      (::ffff:192.168.100.30)
# 上半部主要在列出目前联机的状态中，主要来自那个客户端机器与登入的用户名

Service      pid     machine       Connected at
-------------------------------------------------------
IPC$         5930   win7-pc       Fri Jul 29 15:56:03 2011
project      5930   win7-pc       Fri Jul 29 15:59:25 2011
smb1         5993   __ffff_192.168.100.10  Fri Jul 29 16:32:45 2011
# 这部分则显示出，目前有几个目录被使用了？那个 smb1 代表 //IP/smb1/ 喔！ 
```

你可以透过这个小程序来了解到目前有多少人使用你的 SAMBA 的啦！

* * *

# 16.4 以 PDC 服务器提供账号管理

## 16.4 以 PDC 服务器提供账号管理

我们在 16.1.5 约略谈过 PDC 这个玩意儿，他可以让用户在计算机教室的任何一个地方，都用同一组账号密码登入， 并可取得相同的家目录等数据，这与我们之前谈到的，在 Linux 底下使用 NIS 搭配 NFS 是很类似的作法！ 只是它是用在 Windows 上头就是了。那如何完成呢？我们底下就来谈谈这个玩意儿！^_^

* * *

### 16.4.1 让 Samba 管理网域使用者的一个实作案例

前面介绍的内容都是属于 Peer/Peer 的联机状况，也就是 Samba 服务器与 Windows 客户端其实是平等地位的啦！ 所以 Windows 客户端需要知道 Samba 服务器内的账号密码数据后，才能够顺利的使用 Samba 的资源。 不过，这样的方式在较大型一些的局域网络环境可能就会有点困扰，例如学校的环境。

举例来说，如果你有一个计算机教室里面有 50 部 Windows XP Pro. 的个人计算机，由于计算机教室大家都会使用， 因此里面这 50 部个人计算机有使用还原精灵，也就是每次计算机重新启动后整个操作系统就会还原成原本的样子。 但我们知道使用者总是需要有个人家目录吧？他们总不希望这次的工作在重新启动后就失去了～ 所以我们可以利用一部主机来让他们储存数据啊！那就是 Primary Domain Controller (PDC) 服务器。

其实 Samba PDC 的作用很简单，就是让 Samba PDC 成为整个局域网络的领域管理员 (domain controller)， 然后让 Windows 主机加入这个领域，未来使用者利用 Windows 登入时，(1)Windows 会前往 PDC 服务器取得用户的账号密码， 同时 (2)PDC 还会传送用户的重要数据到那部 Windows 个人计算机上，而 Windows 计算机上的用户注销时， (3)该用户修改过的数据也会回传给 PDC 。如此一来不管这个使用者在哪一部个人计算机上面登入， 他都能够取得正确的个人资料！很棒的作用吧！

PDC 是个很复杂的环境，他可以达到的功能相当的多，而且密码的验证也不必在同一部 PDC 主机上面， 不过这里我们不谈那么复杂的东西，只是做一个简单的练习，因此底下的这部 PDC 使用 Linux 自己的密码来进行验证， 并且也只管理自己所分享出去的资源啰！至于假设网络环境与相关工作组参数如下：

![](img/pdc_01.gif) 图 16.4-1、一个简易的 PDC 实作案例相关参数示意图

整个基本的设定流程应该是这样的：

*   区网计算机环境设定：整体网域设定好，尤其 Windows 的工作组与计算机名称及 IP 等参数；
*   PDC 设定：因为 PDC 管理自己的密码，所以 security = user；
*   PDC 最好拥有整个网域的名称解析权力，亦即成为主要的名称解析器；
*   需有 netlogon 资源共享，提供 windows 2000/XP pro. 客户端的登入之用；
*   由于 Windows 需读入个人配置文件，默认目录为 profile，Linux 系统需预先设定此目录；
*   增加 PDC 上的使用者账号以及机器代码 (machine account) 等等
*   在 Windows 2000/XP pro. 个人计算机上设定成为 PDC 的客户端。

底下咱们就来依序处理处理先！

* * *

### 16.4.2 PDC 服务器的建置

PDC 服务器的建立非常的麻烦，需要一步一步的实作进行，挺讨厌的。而且，由于建置 PDC 的环境主要在管理整个区网内的 Windows 计算机，因此每部 Windows 计算机的主机名与相关参数要先确定下来，如同上一小节的图示内， 每部计算机的角色定位都需要清楚才行。清楚了各个计算机的角色后，接下来就能够慢慢的实作进行啰！

*   1\. 建置 NetBIOS 与 IP 对应的数据：设定 lmhosts 与 /etc/hosts

由于我们的 Samba 即将成为整个网域的名称解析者，因此你最好将整个网域的 NetBIOS name 与 IP 的对应写入 lmhosts 档案当中。如果你的区网是以 DHCP 发放 IP 的，那么你最好搭配 DNS 系统去建置你的主机名对应信息， 否则主机名对应不起来，总是有点困扰。在这个案例中，由于鸟哥使用的 NetBIOS name (如 vbirdserver) 与主机名 (如 www.centos.vbird) 并不相同，因此这里建议需要修改 lmhosts 才好。

```
[root@www ~]# vim /etc/samba/lmhosts
127.0.0.1       localhost      &lt;==这行是预设存在的，不要动他，底下的请自行新增
192.168.100.254  vbirdserver
192.168.100.10   vbirdlinux
192.168.100.20   vbirdwinxp
192.168.100.30   vbirdwin7

[root@www ~]# vim /etc/hosts
192.168.100.254 www.centos.vbird        vbirdserver
192.168.100.10  clientlinux.centos.vbird     vbirdlinux
192.168.100.20  vbirdwinxp
192.168.100.30  vbirdwin7 
```

由于 Linux 上的 Samba 很多数据还是与 TCP/IP 的主机名有关，所以除了 lmhosts 之外，建议还是处理一下 /etc/hosts 比较妥当！这样就行啦！

*   2\. 建置 PDC 主设定：处理 smb.conf

假设我们要让 PDC 客户端登入时可以取得他自己的家目录，那么需要这样处理：

```
[root@www ~]# vim /etc/samba/smb.conf
[global]
        workgroup       = vbirdhouse   &lt;==请务必确认一下工作组与主机名
        netbios name    = vbirdserver
        server string   = This is vbird's samba server
        unix charset    = utf8
        display charset = utf8
        dos charset     = cp950
        log file        = /var/log/samba/log.%m
        max log size    = 50
        security        = user
        passdb backend  = tdbsam
        load printers   = yes
        cups options    = raw
        printcap name   = cups
        printing        = cups

        # 与 PDC 有关的一些设定值：
        # 底下几个设定值处理成为本局域网络内的主要名称解析器
        preferred master = yes
        domain master    = yes
        local master     = yes
        wins support     = yes
        # 操作系统 (OS) 等级越高才能成为主网域的控制者，一般 NT 为 32,
        # Windows 2000 为 64 ，所以这里我们设定高一点，但不可超过 255
        os level      = 100
        # 底下则是设定能否利用 PDC 登入，且登入需要进行哪些动作：
        domain logons = yes
        logon drive   = K:              &lt;==登入后家目录挂载成 Windows 哪一槽
        logon script  = startup.bat     &lt;==每个使用者登入后会自动执行的程序
        time server   = yes             &lt;==自动调整 Windows 时间与 Samba 同步
        admin users   = root            &lt;==预设的管理员账号！预设为 root 
        logon path    = \\%N\%U\profile &lt;==使用者的个人化设定
        logon home    = \\%N\%U         &lt;==用户的家目录位置！

# 这个在指定登入者能够进行的工作，里面主要是具有许多执行程序：
[netlogon]  &lt;==与前面的 logon script 有关，该程序放置在这里
   comment         = Network Logon Service
   path            = /winhome/netlogon  &lt;==重要的目录，要自己建立才行！
   writable        = no
   write list      = root
   follow symlinks = yes
   guest ok        = yes

[homes]
....(底下保留原本设定)....

[root@www ~]# testparm
[root@www ~]# /etc/init.d/smb restart
[root@www ~]# /etc/init.d/nmb restart 
```

上面的设定有几个地方比较有趣一点：

*   time server：要使 Samba 与 Windows 主机的时间同步，使用这个项目；
*   logon script：当使用者以 Windows 客户端登入后，Samba 可以提供一支批处理文件，让使用者去设定好他们自己的目录配置。整个配置的内容记录在 startup.bat 当中。 你要注意的是，这个 startup.bat 档名可以随意更改，不过他必须要放置到 [netlogon] 所指定的目录内；
*   logon drive：那么这个家目录要挂载到那个分割槽？ 在 Windows 底下大多以 C, D, E... 做为磁盘的代号，你这里可以指定一下家目录要放置成为那个磁盘代号；
*   admin users：指定这个 Samba PDC 的管理员身份。
*   [netlogon]：指定利用网络登录时首先去查询的目录资源。
*   logon path：用户登入后，会取得的环境设定数据在哪？ 我们知道用户会有一堆环境数据，例如桌面等，这些东西都放置到这里来。使用的变量中， %N 代表 PDC 服务器的位置， %U 则代表用户的 Linux 家目录。因此最终你得要有 ~someone/profile 的目录才可以。
*   logon home：用户的家目录，默认与 Linux 的家目录相同位置。

*   3\. 建立 Windows 客户端登入时所需的设定数据 netlogon 目录

先来建立 [netlogon] 内所需要的数据好了，那就是一个目录。由于鸟哥预计将所有的 PDC 数据通通放置到 /winhome 当中，包括用户家目录，因此很多东西都需要修订喔！包括后来的 SELinux 肯定会出问题的～

```
[root@www ~]# mkdir -p /winhome/netlogon 
```

接下来我们还得要建立允许使用者执行的档案，就是那个 startup.bat 才行！ 注意一下，我们这里假设用户家目录为 K 槽，那你可以这样做：

```
[root@www ~]# vim /winhome/netlogon/startup.bat
net time \\vbirdserver /set /yes
net use K: /home
# 这个档案的格式为：net use [device:] [directory]

# 再将该档案转成 DOS 的断行格式才行！因为是提供给 Windows  系统嘛！
[root@www ~]# yum install unix2dos
[root@www ~]# unix2dos /winhome/netlogon/startup.bat
[root@www ~]# cat -A /winhome/netlogon/startup.bat
net time \\vbirdserver /set /yes^M$
net use K: /home^M$
# 瞧见吗？会多出个奇怪的 ^M 符号，那就是 Windows 断行字符。 
```

*   4\. 建立 Windows 专用的使用者

因为鸟哥预计将使用者全部挪到 /winhome 底下，而且每个用户家目录应该还要有 profile 目录存在才行， 为了避免麻烦，所以我们先到 /etc/skel 去处理一下，然后才建立账号，最后才产生 samba 用户吧！ 产生 samba 用户可以使用 pdbedit 也能够直接使用 smbpasswd -a ，因为没有要用特殊的参数， 所以，Samba 用户就用旧的 smbpasswd 来处理即可。

```
[root@www ~]# mkdir /etc/skel/profile
[root@www ~]# useradd -d /winhome/dmtsai dmtsai
[root@www ~]# useradd -d /winhome/nikky  nikky
[root@www ~]# smbpasswd -a root
[root@www ~]# smbpasswd -a dmtsai
[root@www ~]# smbpasswd -a nikky
[root@www ~]# pdbedit -L
smb1:2004:
smb3:2006:
smb2:2005:
student:505:
root:0:root
dmtsai:2007:
nikky:2008:
# 重点是需要有画底线的那几个人物出现才行呦！

[root@www ~]# ll /winhome
drwx------. 5 dmtsai dmtsai 4096 Jul 29 16:49 dmtsai
drwxr-xr-x. 2 root   root   4096 Jul 29 16:48 netlogon
drwx------. 5 nikky  nikky  4096 Jul 29 16:49 nikky
# 用户的家目录不是在 /home 而是在 /winhome 里头才是对的呦！ 
```

那以后新增的使用者都有可以存放来自 Windows 的特殊配置文件目录喔！比较好管理啰～ 当然啦，使用 useradd 新增使用者后，记得也要使用 smbpasswd -a username 来让该使用者可以使用 Samba 喔！

*   5\. 建立机器码账号

由于 PDC 会针对 Windows 客户端的主机名 (NetBIOS name) 进行主机账号检查， 所以我们也要为客户端的主机名进行账号的设定。咦！啥是主机账号？ 一般用户账号是英文或数字，主机账号则在该账号最后面加上一个钱字号『$』即可！ 举例来说， vbirdwinxp 这部主机可设定的账号名称为 vbirdwinxp$。

而我们知道要使用 smbpasswd 增加的使用者必须要在 /etc/passwd 当中，因此要建立这个账号你就得要这样做：

```
[root@www ~]# useradd -M -s /sbin/nologin -d /dev/null vbirdwinxp$
[root@www ~]# useradd -M -s /sbin/nologin -d /dev/null vbirdwin7$ 
```

会增加 -M -s -d 等参数的原因是因为不想要让这个账号具有可以登入的权限， 因此将这个主机账号设定的比较怪一点～ ^_^～接下来让 Samba 知道这个账号是主机账号，所以你应该要这样做：

```
[root@www ~]# smbpasswd -a -m vbirdwinxp$
[root@www ~]# smbpasswd -a -m vbirdwin7$ 
```

这样便加入主机账号啰！而我们的 Samba PDC 也就可以透过『主机账号』来判断 Windows 客户端能否连上来， 若连接上 PDC 与 Windows 客户端后，接下来一般使用者账号就可以在 windows 客户端登入了！

*   6\. 修改安全性相关数据

由于我们建立的账号目录在 /winhome 底下，并非正规的 CentOS 目录，所以最重要的 SELinux 可能会跑掉～ 所以，我们还得要修订 SELinux 才行！方法很简单，将 SELinux type 转为 samba_share_t 即可！

```
[root@www ~]# chcon -R -t samba_share_t /winhome 
```

由于 SELinux 的数据是会继承上层目录的，因此未来新增的用户，理论上，就不需要重新修订 SELinux 的文件类型了。 但是，如果你老是发现登入 PDC 的账号却无法取得家目录，那么就观察 /var/log/messages 内的资料来修订吧！

* * *

### 16.4.3 Wimdows XP pro. 的客户端

请注意，底下的方法仅适用于 Windows 2000, Windows XP 专业版 (Pro.)，一般的 Windows XP home 版本是不支持的！ 如果你客户端的主机是随机版的 Windows XP ，通常是 Windows XP home ，那底下的方法可能就无法适用啰！ 要连接上 Samba PDC 的过程也是挺简单的，你可以这样做： (至于 Windows 7 对于 Samba 的版本要求较高，官方网站是说得高于 3.3.x 以上版本才有支持)

*   1\. 确认 windows 客户端的网域与主机名

首先我们必须要确认 Windows 客户端的工作组与主机名跟咱们的 Samba PDC 相同， 确认的方式在局域网络里面已经提过了，这里在强调一次。将鼠标移动到『我的计算机』上面，按下右键， 选择『内容』，然后点选『计算机名称』，会出现如下图示：

![](img/pdc_winxp_01.gif) 图 16.4-2、Windows 客户端连上 PDC 的方式流程示意图

如上图所示，你要先确认箭头 1 处指的主机名与工作组，在我们这个案例当中的工作组为 vbirdhouse， 这部 Windows 主机的 NetBIOS 名称则为 vbirdwinxp 喔！如果不对的话，请按下『变更』来设定， 并且重新启动。重新启动完毕后再到上图的画面当中，按下箭头 2 所指的网络识别处。

*   2\. 设定主机名与域名

接下来我们要设定这部 Windows XP pro. 要链接到局域网络上的哪部 PDC 上面，亦即是处理主机账号以及 Samba PDC 负责的网域 (domain) 啦！在图 16.4-2 按下『网络识别』后，分别在出现的窗口当中选择：

1.  下一步；
2.  这台计算机是公司网络的一部份，而且我在工作时用来联机到其他计算机(T)
3.  我的公司使用一或多个网域的网络(C)
4.  下一步

然后就会出现如下的窗口：

![](img/pdc_winxp_02.gif) 图 16.4-3、Windows 客户端连上 PDC 的方式流程示意图

请依序填写 Samba 主机上面的管理员账号与密码，要注意这个密码是记录于 Samba 中的那个，可不是 /etc/shadow 喔！别搞混了～这是 Samba 服务器的设定呢。输入之后按下一步吧，通常都会出现找不到正确主机的画面，如下所示：

![](img/pdc_winxp_03.gif) 图 16.4-4、Windows 客户端连上 PDC 的方式流程示意图

鸟哥也觉得很奇怪，老是告诉我找不到！不过没有关系，这里我们依旧再填一次主机的 NetBIOS name 以及组名， 如上图所示，然后继续按下一步，就会出现如下的画面啦：

![](img/pdc_winxp_04.gif) 图 16.4-5、Windows 客户端连上 PDC 的方式流程示意图

这次就给他输入正确的管理员账号与密码，记得最后面的网域就是工作组名称，别写错了。 处理完毕后给他按下确定吧！然后就会出现如下画面：

![](img/pdc_winxp_05.gif) 图 16.4-6、Windows 客户端连上 PDC 的方式流程示意图

恭喜你，这就表示已经连接上 Samba PDC 啰！我们希望所有的使用者都直接由 Samba PDC 控管，所以这里请填写 『此时不新增使用者』吧！按下一步去。

*   3\. 重新启动并以新的域名登入

在图 16.4-6 之后请重新启动，开机后整个画面会有点类似这样：

![](img/pdc_winxp_06.gif) 图 16.4-7、Windows 客户端连上 PDC 的方式流程示意图

为了保护我们的系统，因此得要按下 [ctrl]+[alt]+[del] 三个组合按键后，才会出现如下的登入画面：

![](img/pdc_winxp_07.gif) 图 16.4-8、Windows 客户端连上 PDC 的方式流程示意图

目前系统上面就会有两个可选择的账号管理模式，一个是本机账号一个是 PDC 提供的账号，那我怎知登入者是哪个管理模式？ 所以你就得要按下上述画面的『选项』，才会出现『登入到』的那一行数据。出现的两个数据分别是：

*   VBIRDWINXP(此计算机)：这就是你的计算机名称，亦即是以本机账号登入；
*   VBIRDHOUSE：就是 PDC 的 workgroup 项目，透过 PDC 的账号来尝试登入。

现在请输入你在 Samba PDC 上面拥有的账号与密码来尝试登入吧！那如果你输入的账号密码是对的，却发现如下的画面时， 肯定是某些档案权限或者是 SELinux 设定错误！请参考 /var/log/messages 或 /var/log/samba/* 里面的登录档来修改！

![](img/pdc_winxp_08.gif) 图 16.4-9、使用 PDC 账号登入却发现权限错误的图示

*   4\. 观察用户的家目录与配置文件

如果你可以顺利登入的话，打开档案总管后应该可以看到类似下方的画面：

![](img/pdc_winxp_09.gif) 图 16.4-10、登入 PDC 后，取得的家目录状态

呵呵！该连上的通通连结上来啰！你也可以在自己的家目录 (K 槽) 新增移除数据的！是否很不错啊！ ^_^！ 而当你注销之后，你在 Windows 桌面上头所进行的各项个人化设定通通会被移动到 /winhome/dmtsai/profile 当中喔！ 如果不相信的话，请自行前往 Samba 服务器上头瞧一瞧就知道了。

* * *

### 16.4.4 Wimdows 7 的客户端

根据 SAMBA 官网的说明，支持 Windows 7 的 Samba 版本必须要高于 3.3.x 才行，还好，我们的 CentOS 6.x Samba 版本真的是高于 3.3.x 的 3.5.x，因此理论上是支持 Windows 7 的！只不过 Windows 7 要加入 Samba PDC 还得要修改注册码才行！这部份真的是给它很困扰！在 Windows 7 机码的修改方面，主要是修改底下的机码：

```
# 1\. 这个部分是进行『新增』机码！
[HKEY_LOCAL_MACHINE\SYSTEM\CurrentControlSet\services\LanmanWorkstation\Parameters]
“DomainCompatibilityMode”=dword:00000001
“DNSNameResolutionRequired”=dword:00000000 
```

修改的方式为，在 Windows7 的执行里面输入『 regedit 』，会出现如下的画面：

![](img/centos6_win7_9.gif) 图 16.4-11、Windows 7 注册机码的动作

先由 (1)左侧窗口一层一层点选到我们所需要的目标去，然后 (2)观察最底下的机码顺序看对不对。之后 (3)在右侧窗口点选我们所需要的机码，如果是要新增，那就是在右侧空白处右键单击选新增即可增加一组机码名称。最后 (4)双击机码会出现可供修改的窗口，那就改成上面表格中的要求即可。更多关于 Windows 7 加入 PDC 的相关资料， 请查阅文末的参考数据部分喔。

等到将机码修订完毕，你就可以使用与 Windows XP 相同的方式来加入 PDC 啰！

* * *

### 16.4.5 PDC 之问题克服

如果老是发生错误讯息为『使用的帐户是计算机帐户。请使用你的通用用户帐户或本机用户帐户来存取这台服务器』时， 你可以这样做的：

*   先察看一下 /var/log/samba 里面的登录文件信息，尤其是 log.vbirdwinxp 关于这部主机的信息吶；

*   如果还是无法解决，可以在 lmhosts 里面增加 vbirdwinxp 的 IP 与主机名的对应，然后将 samba 整个关掉『/etc/init.d/smb stop』，等待一段时间让 NetBIOS 的名称解析时间逾时，再重新启动 samba 『/etc/init.d/smb start』，然后再重新做一次输入 root 的密码那个动作

在鸟哥尝试过的案例中，上面第二个步骤挺有效的！不过，还是得要察看 /var/log/samba 里面的登录信息才行喔！

*   一些 Windows 账号在 Windows 系统上面的使用技巧

虽然 PDC 很好用，不过你要注意的是，每次你使用 PDC 上头的账号登入 Windows 客户端主机时， Windows 主机会由 /winhome/username/profile/ 当中加载所需要的数据， 并暂时启动一个文件夹在 Windows 系统的 C:\Documents and Settings\username 当中，如果你的家目录下的 profile 数据太多时， 光是传输就会花去很多时间的！

所以，你应该将一些档案数据放置到你的家目录下，亦即 K 槽当中，尽量不要使用 Windows 预设的『我的文档夹』， 因为『我的文档夹』会将数据移动到『 /winhome/username/profile/My Documents/ 』目录下，同样的， 储存到桌面的数据会被放置到『 /winhome/username/profile/桌面/ 』目录中，那样在登入与注销时会花去很多时间喔！ 这个小地方也要注意的呢！ ^_^

好了，关于 SAMBA 的 PDC 作法我们就谈到这里，还有更多的信息你可以前往这个章节最后面的参考数据所列出的网址去查阅， 因为还有很多的作法吶！事实上，鸟哥觉得在一个网域当中，如果有多部的 Windows NT 主机，例如 Windows 2000/XP pro. 这一类的比较稳定的个人使用桌面版本时，使用 PDC 就很有用了！因为 Windows 2000/XP pro. 也是一个多人的操作系统，不像 Windows 98 是单人的操作系统。所以，当使用 Windows 2000/XP pro. 而无法登入 PDC 时，你是无法使用 Windows 2000/XP pro. 上面的任何的信息的。 但是在 Windows 98 上面若无法正确的登入，你仍然具有该计算机的主控权喔！

另外，设定 Windows 客户端之前，请先确认你的 Windows 是什么版本？ 上述的动作对于 Windows XP 家用版 (Home), Windows 7 是没有作用的！请先确认才行喔！

* * *

# 16.5 服务器简单维护与管理

## 16.5 服务器简单维护与管理

除了上述的正规作法之外，其实还有一些稍微重要的事情要跟大家分享的！

* * *

### 16.5.1 服务器相关问题克服

通常我们在设定 SAMBA 的时候，如果是以单一主机的工作组 (Workgroup) 的方式来进行 smb.conf 的设定时，几乎很容易就可以设定成功了！并没有什么很困难的步骤。不过，万一还是无法成功的设定起来， 请务必察看登录档，也就是在 /var/log/samba/ 里面的数据！在这里面的资料当中，你会发现：咦！ 怎么这么多档案啊！因为我们在 smb.conf 里面设定了：

*   log file = /var/log/samba/log.%m

那个 %m 是指客户端计算机的 NetBIOS Name 的意思，所以，当有个 vbirdwinxp 的主机来登入我们的 vbirdserver 主机时，那么登入的信息就会被纪录在 /var/log/samba/log.vbirdwinxp 档案喔！而如果万一来源 IP 并没有 Netbios name 的时候，那么很可能是一些错误讯息，这些错误讯息就会被纪录到 log.smbd, log.nmbd 里面去了！所以，如果你要察看某部计算机连上你的 SAMBA 主机发生了什么问题时，特别要留意这个登录档的形式喔！

另外，如果你的 SAMBA 明明已经启动完成了，却偏偏老是无法成功，又无法查出问题时，建议先关闭 Samba 一阵子，再重新启动：

*   /etc/init.d/smb stop

在鸟哥过去的案例当中，确实有几次是因为 PID 与 NetBIOS 的问题，导致整个 SAMBA 怪怪的～所以完整的关闭之后， 经过一阵子的短暂时间，再重新启动，应该就可以恢复正常了！

还有，万一你在进行写入的动作时，老是发现『你没有相关写入的权限！』，不要怀疑，几乎可以确定是 Permission 的问题，也就是 Linux 的权限与 SAMBA 开放的权限并不相符合，或者是 SELinux 在搞鬼！无论如何， 你必须要了解能不能写入 Linux 磁盘，看的是 PID 的权限与 Linxu 文件系统是否吻合，而那个 smb.conf 里面设定的相关权限只是在 SAMBA 运作过程当中『预计』要给使用者的权限而已，并不能取代真正的 Linux 权限喔！所以，万一真的发现该问题存在， 请登入 Linux 系统，查验一下该对应的目录的 permission 吧！^_^

另外，通常造成明明已经查到分享 (smbclient -L 的结果)，却老是无法顺利挂载的情况，主要有底下几个可能的原因：

*   虽然 smb.conf 设定正确，但是设定值『 path 』所指定的目录却忘记建立了 (最常见的呆样！)；
*   虽然 smb.conf 设定为可擦写，但是目录针对该用户的权限却是只读或者是无权限；
*   虽然权限全部都正确，但是 SELinux 的类型却错误了！
*   虽然全部的数据都是正确的，但是 SELinux 的规则 (getsebool -a) 却没有顺利启动。

上述都是一些常见的问题，更多问题的解决方案，请参考最正确的登录文件信息吧！ ^_^

* * *

### 16.5.2 让使用者修改 samba 密码同时同步更新 /etc/shadow 密码

有个问题是，我们知道使用者可以透过 passwd 修改 /etc/shadow 内的密码，而且用户也能够自行以 smbpasswd 修改 Samba 的密码。如果用户是类似 PDC 的用户，那么这些用户理论上就很少使用 Linux 啦！那么想一想， 能否让用户在修改 Windows 密码 (就是 Samba) 时，同步更新 Linux 上面的 /etc/shadow 密码呢？ 答案是可行的啦！而且动作并不困难～因为 smb.conf 里头已经提供了相对应的参数设定值！你可以参考底下的网站数据：

*   [`moto.debian.org.tw/viewtopic.php?t=7732&`](http://moto.debian.org.tw/viewtopic.php?t=7732&)
*   [`de.samba.org/samba/docs/using_samba/ch09.html`](http://de.samba.org/samba/docs/using_samba/ch09.html)

鸟哥做个总结，基本上你需要的是 smb.conf 里面 [global] 的几个设定值：

```
[root@www ~]# vim /etc/samba/smb.conf
[global]
# 保留前面的各项设定值，并新增底下三行即可：
 unix password sync  = yes                &lt;==让 Samba 与 Linux 密码同步
        passwd program      = /usr/bin/passwd %u &lt;==以 root 呼叫修改密码的指令
        pam password change = yes                &lt;==并且支持 pam 模块！

[root@www ~]# testparm
[root@www ~]# /etc/init.d/smb restart 
```

接下来，当你以一般用户 (例如 dmtsai) 修改 samba 的密码时，就会像这样：

```
[dmtsai@www ~]$ smbpasswd
Old SMB password:  &lt;==得先输入旧密码，才能输入新密码
New SMB password:
Retype new SMB password:
Password changed for user dmtsai &lt;==这就是成功的字样！

# 若出现底下的字样，应该就是你的密码输入被限制了！例如输入的密码字符少于 6 个！
machine 127.0.0.1 rejected the password change: Error was : Password restriction.
Failed to change password for dmtsai 
```

* * *

### 16.5.3 利用 ACL 配合单一使用者时的控管

想象一个案例，如果你是学校的网管人员，有个兼任老师向你申请账号，主要是要在很多班级内取得同学的专题资料。 因为该老师是兼任的，你或许担心一不小心该教师就将同学的辛苦资料给销毁，倒不是教师们故意的， 而是很多时候...不熟嘛！这个时候如果你将该老师加入同学的群组，然后偏偏同学们所在的目录是群组可写入的话， 那么该教师就能够拥有可擦写的权限了，也就容易造成一些莫名的灾难～

那该怎么办？其实可以透过 ACL 来管理某个目录的单一用户权力啦！所以说， 权限的管理不必透过 smb.conf 的设定，只要透过 ACL 来管理就能够达到你所需要的目的了。 关于 ACL 的说明我们在[基础学习篇第三版第十四章](http://linux.vbird.org/linux_basic/0410accountmanager.php#acl_talk)已经提过了， 这里不再啰唆，请自行前往查阅吶！ ^_^

* * *

# 16.6 重点回顾

## 16.6 重点回顾

*   由 Tridgell 利用逆向工程分析网芳得到 Server Message Block 协议的产生；
*   Samba 名称的由来是因为需包含没有意义的 SMB server 之故；
*   SAMBA 可以让 Linux 与 Windows 直接进行文件系统的使用；
*   SAMBA 主要架构在 NetBIOS 上发展的，且以 NetBIOS over TCP/IP 克服 NetBIOS 无法跨路由的问题；
*   Samba 使用的 daemon 主要有管理分享权限的 smbd 以及 NetBIOS 解析的 nmbd
*   Samba 使用的模式主要有单机的 workgroup 方式，以及网域控管的 PDC 模式；
*   Samba 的主配置文件之档名为 smb.conf
*   smb.conf 内，主要区分为 [global] 服务器整体设定与 [share] 分享的资源两大部分
*   Samba 使用者账号控管主要的设定值为 security = {share,user,domain} 等
*   Samba 客户端可使用 smbclient 以及 mount.cifs 进行网芳的挂载
*   新版的 Samba 默认使用数据库记录帐户信息，新增账号用 pdbedit ，修改密码则用 smbpasswd
*   Samba 主要支持 CUPS 的打印机服务器
*   在权限控管方面，最容易出错的为 SELinux 的规则与类型 (SELinux type)
*   在 PDC 的设定方面，由于与主机名相关性很高，建议设定 lmhosts 档案内容为宜

* * *

# 16.7 本章习题

## 16.7 本章习题

*   一般来说， SAMBA 使用的配置文件放置在哪里？档名为何？使用的档名为 smb.conf ，通常会放置在 /etc/samba/smb.conf 里面，不过，最好可以使用 rpm -qc packagename 来查询！
*   哪一个指令可以用来判断 smb.conf 这个配置文件的正确性？当我们修改完 smb.conf 之后，记得要以 testparm 来进行 samba 的确认！
*   哪一个指令可以用来察看 SAMBA 主机分享出什么目录？利用 smbclient 即可：『smbclient -L NetBiosName -U username 』！
*   在 Linux 客户端挂载网芳的文件系统主要是依据哪个指令来达成的？就是透过 mount.cifs 或 mount -t cifs 来达成的！
*   我今天使用 smbpasswd 去新增一位使用者 badbird，让他可以登入我的 Linux SAMBA 主机，但是无论如何就是无法新增。你认为原因可能是什么？由于 Samba 用户的信息必须要存在于 /etc/passwd 里面，既然无法新增，应该先确认 badbird 这个用户已经存在于 Linux 系统当中了！

* * *

# 16.8 参考数据与延伸阅读

## 16.8 参考数据与延伸阅读

*   注 1：维基百科对 Samba 的来源与作者的介绍： [`en.wikipedia.org/wiki/Samba_software`](http://en.wikipedia.org/wiki/Samba_software) [`en.wikipedia.org/wiki/Andrew_Tridgell`](http://en.wikipedia.org/wiki/Andrew_Tridgell)
*   man 5 smb.conf
*   Study Area ：[`www.study-area.org/linux/servers/linux_samba.htm`](http://www.study-area.org/linux/servers/linux_samba.htm)
*   电子书 Using Samba：[`de.samba.org/samba/docs/using_samba/ch00.html`](http://de.samba.org/samba/docs/using_samba/ch00.html)
*   Samba PDC HOWTO: [`us5.samba.org/samba/docs/man/Samba-HOWTO-Collection/samba-pdc.html`](http://us5.samba.org/samba/docs/man/Samba-HOWTO-Collection/samba-pdc.html)
*   SAMBA 官方网站：[`www.samba.org/`](http://www.samba.org/)
*   杨锦昌老师的 SAMBA 密技：[`apt.nc.hcc.edu.tw/web/student_server_FC1.htm#samba`](http://apt.nc.hcc.edu.tw/web/student_server_FC1.htm#samba)
*   依玛猫的打印文件：[`www.imacat.idv.tw/tech/lnxprint.html`](http://www.imacat.idv.tw/tech/lnxprint.html)
*   Gentoo Linux 的 Samba 文件：[`www.gentoo.org/doc/zh_tw/quick-samba-howto.xml`](http://www.gentoo.org/doc/zh_tw/quick-samba-howto.xml)
*   cupsaddsmb 用法：[`www.enterprisenetworkingplanet.com/netsysm/article.php/3621876`](http://www.enterprisenetworkingplanet.com/netsysm/article.php/3621876)
*   下载 CUPS-windows 的网站：[`ftp.easysw.com/pub/cups/windows/`](http://ftp.easysw.com/pub/cups/windows/)
*   eyesblue 在讨论区针对语系的说明：[`phorum.vbird.org/viewtopic.php?t=22001`](http://phorum.vbird.org/viewtopic.php?t=22001)
*   testparm -v
*   关于 Windows 7 加入 PDC 的机码相关问题： [`wiki.samba.org/index.php/Windows7`](https://wiki.samba.org/index.php/Windows7) [`www.linuxquestions.org/questions/linux-server-73/joining-a-windows-7-client-to-samba-pdc-v-3-4-3-a-815174/`](http://www.linuxquestions.org/questions/linux-server-73/joining-a-windows-7-client-to-samba-pdc-v-3-4-3-a-815174/) [`www.1stbyte.com/2009/05/31/join-windows-7-to-samba-pdc/`](http://www.1stbyte.com/2009/05/31/join-windows-7-to-samba-pdc/)

    ```
    # 2\. 这个部分是进行『修改』机码！不过，如果你作了，会让你可加入 PDC，
    # 但却无法顺利的登入！所以这里得要特别注意！
    [HKEY_LOCAL_MACHINE\SYSTEM\CurrentControlSet\services\Netlogon\Parameters]
    “RequireSignOnSeal”=dword:00000000
    “RequireStrongKey”=dword:00000000 
    ```

* * *

2001/09/17：好久以前曾经完成的一项任务 ^_^ 2003/07/26：将 2001/09/17 所写的内容做了大幅度的修订，增加原理以及更多的设定项目！ 2003/09/10：将 PDC 部分补充的更完整，因为加入了个人化的 Profiles 在 /home/samba/profiles 当中了！同时加入课后练习喔 2003/09/30：加入了 CUPS 打印机的支持！ 2005/10/17：Samba 2.2 在中文编码上面与最新的 samba 3.0.x 版本不同。请参考： [中文编码](http://phorum.vbird.org/viewtopic.php?t=22001) 网友的详细说明喔！ 2006/12/20：将旧的文章移动到 [此处](http://linux.vbird.org/linux_server/0370samba/0370samba.php) 2006/12/29：终于写完了 Samba 了！将 PDC 改写，很多乱乱的地方都改掉了～ 2007/04/12：原本对 homes 的说明中，那个 umask 应该是 002 ，原先的 022 是错的！ 2010/06/11：一直误会了作者的名字，名称为 Andrew Tridgell 而不是 Tridgwell ！抱歉了！ 2011/03/18：将旧的基于 CentOS 4.x 的文章移动到 [此处](http://linux.vbird.org/linux_server/0370samba/0370samba-centos4.php) 2011/03/31：终于搞定了 Samba ，光是实作就花去鸟哥大部分的时间了～累毙！ 2011/07/29：将基于 CentOS 5.x 的版本移动到[此处](http://linux.vbird.org/linux_server/0370samba/0370samba-centos5.php) 2011/07/29：PDC 的部分，终于可以加入 windows 7 啰！开心！

* * *