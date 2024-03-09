# 第五章、 Linux 常用网络指令

最近更新日期：2011/07/18

Linux 的网络功能相当的强悍，一时之间我们也无法完全的介绍所有的网络指令，这个章节主要的目的在介绍一些常见的网络指令而已。 至于每个指令的详细用途将在后续服务器架设时，依照指令的相关性来进行说明。当然，在这个章节的主要目的是在于将所有的指令汇整在一起， 比较容易了解啦！这一章还有个相当重要的重点，那就是封包撷取的指令。若不熟悉也没关系，先放着，全部读完后再回来这一章仔细练习啊！

*   5.1 网络参数设定使用的指令
    *   5.1.1 手动/自动设定与启动/关闭 IP 参数：ifconfig, ifup, ifdown
    *   5.1.2 路由修改： route
    *   5.1.3 网络参数综合指令： ip
    *   5.1.4 无线网络： iwlist, iwconfig
    *   5.1.5 手动使用 DHCP 自动取得 IP 参数：dhclient
*   5.2 网络侦错与观察指令
    *   5.2.1 两部主机两点沟通： ping, 用 ping 追踪路径中的最大 MTU 数值
    *   5.2.2 两主机间各节点分析： traceroute
    *   5.2.3 察看本机的网络联机与后门： netstat
    *   5.2.4 侦测主机名与 IP 对应： host, nslookup
*   5.3 远程联机指令与实时通讯软件
    *   5.3.1 终端机与 BBS 联机： telnet
    *   5.3.2 FTP 联机软件： ftp, lftp (自动化脚本)
    *   5.3.3 图形接口的实时通讯软件： pidgin (gaim 的延伸)
*   5.4 文字接口网页浏览
    *   5.4.1 文字浏览器： links
    *   5.4.2 文字接口下载器： wget
*   5.5 封包撷取功能
    *   5.5.1 文字接口封包撷取器： tcpdump
    *   5.5.2 图形接口封包撷取器： wireshark
    *   5.5.3 任意启动 TCP/UDP 封包的埠口联机： nc, netcat
*   5.6 重点回顾
*   5.7 本章习题
*   5.8 参考数据与延伸阅读
*   5.9 [针对本文的建议：http://phorum.vbird.org/viewtopic.php?t=26123](http://phorum.vbird.org/viewtopic.php?t=26123)

* * *

# 5.1 网络参数设定使用的指令

## 5.1 网络参数设定使用的指令

任何时刻如果你想要做好你的网络参数设定，包括 IP 参数、路由参数与无线网络等等，就得要了解底下这些相关的指令才行！其中以 ifconfig 及 route 这两支指令算是较重要的喔！ ^_^！当然，比较新鲜的作法，可以使用 ip 这个汇整的指令来设定 IP 参数啦！

*   ifconfig ：查询、设定网络卡与 IP 网域等相关参数；
*   ifup, ifdown：这两个档案是 script，透过更简单的方式来启动网络接口；
*   route ：查询、设定路由表 (route table)
*   ip ：复合式的指令， 可以直接修改上述提到的功能；

* * *

### 5.1.1 手动/自动设定与启动/关闭 IP 参数： ifconfig, ifup, ifdown

这三个指令的用途都是在启动网络接口，不过， ifup 与 ifdown 仅能就 /etc/sysconfig/network-scripts 内的 ifcfg-ethX (X 为数字) 进行启动或关闭的动作，并不能直接修改网络参数，除非手动调整 ifcfg-ethX 档案才行。至于 ifconfig 则可以直接手动给予某个接口 IP 或调整其网络参数！底下我们就分别来谈一谈！

*   ifconfig

ifconfig 主要是可以手动的启动、观察与修改网络接口的相关参数，可以修改的参数很多啊，包括 IP 参数以及 MTU 等等都可以修改，他的语法如下：

```
[root@www ~]# ifconfig {interface} {up&#124;down}  &lt;== 观察与启动接口
[root@www ~]# ifconfig interface {options}    &lt;== 设定与修改接口
选项与参数：
interface：网络卡接口代号，包括 eth0, eth1, ppp0 等等
options  ：可以接的参数，包括如下：
    up, down ：启动 (up) 或关闭 (down) 该网络接口(不涉及任何参数)
    mtu      ：可以设定不同的 MTU 数值，例如 mtu 1500 (单位为 byte)
    netmask  ：就是子屏蔽网络；
    broadcast：就是广播地址啊！

# 范例一：观察所有的网络接口(直接输入 ifconfig)
[root@www ~]# ifconfig
eth0      Link encap:Ethernet  HWaddr 08:00:27:71:85:BD
          inet addr:192.168.1.100  Bcast:192.168.1.255  Mask:255.255.255.0
          inet6 addr: fe80::a00:27ff:fe71:85bd/64 Scope:Link
          UP BROADCAST RUNNING MULTICAST  MTU:1500  Metric:1
          RX packets:2555 errors:0 dropped:0 overruns:0 frame:0
          TX packets:70 errors:0 dropped:0 overruns:0 carrier:0
          collisions:0 txqueuelen:1000
          RX bytes:239892 (234.2 KiB)  TX bytes:11153 (10.8 KiB) 
```

一般来说，直接输入 ifconfig 就会列出目前已经被启动的卡，不论这个卡是否有给予 IP，都会被显示出来。而如果是输入 ifconfig eth0，则仅会秀出这张接口的相关数据， 而不管该接口是否有启动。所以如果你想要知道某张网络卡的 Hardware Address，直接输入『 ifconfig "网络接口代号" 』即可喔！ ^_^！至于上表出现的各项数据是这样的(数据排列由上而下、由左而右)：

*   eth0：就是网络卡的代号，也有 lo 这个 loopback ；
*   HWaddr：就是网络卡的硬件地址，俗称的 MAC 是也；
*   inet addr：IPv4 的 IP 地址，后续的 Bcast, Mask 分别代表的是 Broadcast 与 netmask 喔！
*   inet6 addr：是 IPv6 的版本的 IP ，我们没有使用，所以略过；
*   MTU：就是第二章谈到的 [MTU](http://linux.vbird.org/linux_server/0110network_basic.php#tcpip_link_mtu) 啊！
*   RX：那一行代表的是网络由启动到目前为止的封包接收情况， packets 代表封包数、errors 代表封包发生错误的数量、 dropped 代表封包由于有问题而遭丢弃的数量等等
*   TX：与 RX 相反，为网络由启动到目前为止的传送情况；
*   collisions：代表封包碰撞的情况，如果发生太多次， 表示你的网络状况不太好；
*   txqueuelen：代表用来传输数据的缓冲区的储存长度；
*   RX bytes, TX bytes：总接收、发送字节总量

透过观察上述的资料，大致上可以了解到你的网络情况，尤其是那个 RX, TX 内的 error 数量， 以及是否发生严重的 collision 情况，都是需要注意的喔！ ^_^

```
# 范例二：暂时修改网络接口，给予 eth0 一个 192.168.100.100/24 的参数
[root@www ~]# ifconfig eth0 192.168.100.100
# 如果不加任何其他参数，则系统会依照该 IP 所在的 class 范围，自动的计算出
# netmask 以及 network, broadcast 等 IP 参数，若想改其他参数则：

[root@www ~]# ifconfig eth0 192.168.100.100 \
&gt; netmask 255.255.255.128 mtu 8000 
# 设定不同参数的网络接口，同时设定 MTU 的数值！

[root@www ~]# ifconfig eth0 mtu 9000
# 仅修改该接口的 MTU 数值，其他的保持不动！

[root@www ~]# ifconfig eth0:0 192.168.50.50
# 仔细看那个界面是 eth0:0 喔！那就是在该实体网卡上，再仿真一个网络接口，
# 亦即是在一张网络卡上面设定多个 IP 的意思啦！

[root@www ~]# ifconfig
eth0      Link encap:Ethernet  HWaddr 08:00:27:71:85:BD
          inet addr:192.168.100.100  Bcast:192.168.100.127  Mask:255.255.255.128
          inet6 addr: fe80::a00:27ff:fe71:85bd/64 Scope:Link
          UP BROADCAST RUNNING MULTICAST  MTU:9000  Metric:1
          RX packets:2555 errors:0 dropped:0 overruns:0 frame:0
          TX packets:70 errors:0 dropped:0 overruns:0 carrier:0
          collisions:0 txqueuelen:1000
          RX bytes:239892 (234.2 KiB)  TX bytes:11153 (10.8 KiB)

eth0:0    Link encap:Ethernet  HWaddr 08:00:27:71:85:BD
          inet addr:192.168.50.50  Bcast:192.168.50.255  Mask:255.255.255.0
          UP BROADCAST RUNNING MULTICAST  MTU:9000  Metric:1
# 仔细看，是否与硬件有关的信息都相同啊！没错！因为是同一张网卡嘛！
# 那如果想要将刚刚建立的那张 eth0:0 关闭就好，不影响原有的 eth0 呢？

[root@www ~]# ifconfig eth0:0 down
# 关掉 eth0:0 这个界面。那如果想用默认值启动 eth1：『ifconfig eth1 up』即可达成

# 范例三：将手动的处理全部取消，使用原有的设定值重建网络参数：
[root@www ~]# /etc/init.d/network restart
# 刚刚设定的数据全部失效，会以 ifcfg-ethX 的设定为主！ 
```

使用 ifconfig 可以暂时手动来设定或修改某个适配卡的相关功能，并且也可以透过 eth0:0 这种虚拟的网络接口来设定好一张网络卡上面的多个 IP 喔！手动的方式真是简单啊！并且设定错误也不打紧，因为我们可以利用 /etc/init.d/network restart 来重新启动整个网络接口，那么之前手动的设定数据会全部都失效喔！另外， 要启动某个网络接口，但又不让他具有 IP 参数时，直接给他 ifconfig eth0 up 即可！ 这个动作经常在无线网卡当中会进行，因为我们必须要启动无线网卡让他去侦测 AP 存在与否啊！

*   ifup, ifdown

实时的手动修改一些网络接口参数，可以利用 ifconfig 来达成，如果是要直接以配置文件， 亦即是在 /etc/sysconfig/network-scripts 里面的 ifcfg-ethx 等档案的设定参数来启动的话， 那就得要透过 ifdown 或 ifup 来达成了。

```
[root@www ~]# ifup   {interface}
[root@www ~]# ifdown {interface}

[root@www ~]# ifup eth0 
```

ifup 与 ifdown 真是太简单了！这两支程序其实是 script 而已，他会直接到 /etc/sysconfig/network-scripts 目录下搜寻对应的配置文件，例如 ifup eth0 时，他会找出 ifcfg-eth0 这个档案的内容，然后来加以设定。 关于 ifcfg-eth0 的设定则请参考[第四章](http://linux.vbird.org/linux_server/0130internet_connect.php)的说明。

不过，由于这两支程序主要是搜寻配置文件 (ifcfg-ethx) 来进行启动与关闭的， 所以在使用前请确定 ifcfg-ethx 是否真的存在于正确的目录内，否则会启动失败喔！ 另外，如果以 ifconfig eth0 .... 来设定或者是修改了网络接口后， 那就无法再以 ifdown eth0 的方式来关闭了！因为 ifdown 会分析比对目前的网络参数与 ifcfg-eth0 是否相符，不符的话，就会放弃该次动作。因此，使用 ifconfig 修改完毕后，应该要以 ifconfig eth0 down 才能够关闭该界面喔！

* * *

### 5.1.2 路由修改： route

我们在[第二章网络基础](http://linux.vbird.org/linux_server/0110network_basic.php)的时候谈过关于路由的问题， 两部主机之间一定要有路由才能够互通 TCP/IP 的协议，否则就无法进行联机啊！一般来说，只要有网络接口， 该接口就会产生一个路由，所以我们安装的主机有一个 eth0 的接口，看起来就会是这样：

```
[root@www ~]# route [-nee]
[root@www ~]# route add [-net&#124;-host] [网域或主机] netmask [mask] [gw&#124;dev]
[root@www ~]# route del [-net&#124;-host] [网域或主机] netmask [mask] [gw&#124;dev]
观察的参数：
   -n  ：不要使用通讯协议或主机名，直接使用 IP 或 port number；
   -ee ：使用更详细的信息来显示
增加 (add) 与删除 (del) 路由的相关参数：
   -net    ：表示后面接的路由为一个网域；
   -host   ：表示后面接的为连接到单部主机的路由；
   netmask ：与网域有关，可以设定 netmask 决定网域的大小；
   gw      ：gateway 的简写，后续接的是 IP 的数值喔，与 dev 不同；
   dev     ：如果只是要指定由那一块网络卡联机出去，则使用这个设定，后面接 eth0 等 
# 范例一：单纯的观察路由状态
[root@www ~]# route -n
Kernel IP routing table
Destination     Gateway         Genmask         Flags Metric Ref    Use Iface
192.168.1.0     0.0.0.0         255.255.255.0   U     0      0        0 eth0
169.254.0.0     0.0.0.0         255.255.0.0     U     1002   0        0 eth0
0.0.0.0         192.168.1.254   0.0.0.0         UG    0      0        0 eth0

[root@www ~]# route
Kernel IP routing table
Destination     Gateway         Genmask         Flags Metric Ref    Use Iface
192.168.1.0     *               255.255.255.0   U     0      0        0 eth0
link-local      *               255.255.0.0     U     1002   0        0 eth0
default         192.168.1.254   0.0.0.0         UG    0      0        0 eth0 
```

由上面的例子当中仔细观察 route 与 route -n 的输出结果，你可以发现有加 -n 参数的主要是显示出 IP ，至于使用 route 而已的话，显示的则是『主机名』喔！也就是说，在预设的情况下， route 会去找出该 IP 的主机名，如果找不到呢？ 就会显示的钝钝的(有点小慢)，所以说，鸟哥通常都直接使用 route -n 啦！ 由上面看起来，我们也知道 default = 0.0.0.0/0.0.0.0 ， 而上面的信息有哪些你必须要知道的呢？

*   Destination, Genmask：这两个玩意儿就是分别是 network 与 netmask 啦！所以这两个咚咚就组合成为一个完整的网域啰！

*   Gateway：该网域是通过哪个 gateway 连接出去的？如果显示 0.0.0.0 表示该路由是直接由本机传送，亦即可以透过局域网络的 MAC 直接传讯；如果有显示 IP 的话，表示该路由需要经过路由器 (通讯闸) 的帮忙才能够传送出去。

*   Flags：总共有多个旗标，代表的意义如下：

    *   U (route is up)：该路由是启动的；
    *   H (target is a host)：目标是一部主机 (IP) 而非网域；
    *   G (use gateway)：需要透过外部的主机 (gateway) 来转递封包；
    *   R (reinstate route for dynamic routing)：使用动态路由时，恢复路由信息的旗标；
    *   D (dynamically installed by daemon or redirect)：已经由服务或转 port 功能设定为动态路由
    *   M (modified from routing daemon or redirect)：路由已经被修改了；
    *   ! (reject route)：这个路由将不会被接受(用来抵挡不安全的网域！)
*   Iface：这个路由传递封包的接口。

此外，观察一下上面的路由排列顺序喔，依序是由小网域 (192.168.1.0/24 是 Class C)，逐渐到大网域 (169.254.0.0/16 Class B) 最后则是预设路由 (0.0.0.0/0.0.0.0)。 然后当我们要判断某个网络封包应该如何传送的时候，该封包会经由这个路由的过程来判断喔！ 举例来说，我上头仅有三个路由，若我有一个传往 192.168.1.20 的封包要传递，那首先会找 192.168.1.0/24 这个网域的路由，找到了！所以直接由 eth0 传送出去；

如果是传送到 Yahoo 的主机呢？ Yahoo 的主机 IP 是 119.160.246.241，我们通过判断 1)不是 192.168.1.0/24， 2)不是 169.254.0.0/16 结果到达 3)0/0 时，OK！传出去了，透过 eth0 将封包传给 192.168.1.254 那部 gateway 主机啊！所以说，路由是有顺序的。

因此当你重复设定多个同样的路由时， 例如在你的主机上的两张网络卡设定为相同网域的 IP 时，会出现什么情况？会出现如下的情况：

```
Kernel IP routing table
Destination    Gateway         Genmask         Flags Metric Ref    Use Iface
192.168.1.0    0.0.0.0         255.255.255.0   U     0      0        0 eth0
192.168.1.0    0.0.0.0         255.255.255.0   U     0      0        0 eth1 
```

也就是说，由于路由是依照顺序来排列与传送的， 所以不论封包是由那个界面 (eth0, eth1) 所接收，都会由上述的 eth0 传送出去， 所以，在一部主机上面设定两个相同网域的 IP 本身没有什么意义！有点多此一举就是了。 除非是类似虚拟机 (Xen, VMware 等软件) 所架设的多主机时，才会有这个必要～

```
# 范例二：路由的增加与删除
[root@www ~]# route del -net 169.254.0.0 netmask 255.255.0.0 dev eth0
# 上面这个动作可以删除掉 169.254.0.0/16 这个网域！
# 请注意，在删除的时候，需要将路由表上面出现的信息都写入
# 包括 netmask , dev 等等参数喔！注意注意

[root@www ~]# route add -net 192.168.100.0 \
&gt; netmask 255.255.255.0 dev eth0
# 透过 route add 来增加一个路由！请注意，这个路由的设定必须要能够与你的网络互通。
# 举例来说，如果我下达底下的指令就会显示错误：
# route add -net 192.168.200.0 netmask 255.255.255.0 &lt;u&gt;gw 192.168.200.254&lt;/u&gt;
# 因为我的主机内仅有 192.168.1.11 这个 IP ，所以不能直接与 192.168.200.254
# 这个网段直接使用 MAC 互通！这样说，可以理解吗？

[root@www ~]# route add default gw 192.168.1.250
# 增加预设路由的方法！请注意，只要有一个预设路由就够了喔！
# 同样的，那个 192.168.1.250 的 IP 也需要能与你的 LAN 沟通才行！
# 在这个地方如果你随便设定后，记得使用底下的指令重新设定你的网络
# &lt;u&gt;/etc/init.d/network restart&lt;/u&gt; 
```

如果是要进行路由的删除与增加，那就得要参考上面的例子了，其实，使用 man route 里面的数据就很丰富了！仔细查阅一下啰！ 你只要记得，当出现『SIOCADDRT: Network is unreachable』 这个错误时，肯定是由于 gw 后面接的 IP 无法直接与你的网域沟通 (Gateway 并不在你的网域内)， 所以，赶紧检查一下是否输入错误啊！

**Tips:** 一般来说，鸟哥如果接触到一个新的环境内的主机，在不想要更动原系统的配置文件情况下，然后预计使用本书的网络环境设定时， 手动的处理就变成：『ifconfig eth0 192.168.1.100; route add default gw 192.168.1.254』这样就搞定了！ 直接联网与测试。等到完成测试后，再给她 /etc/init.d/network restart 恢复原系统的网络即可。

![](img/vbird_face.gif)

* * *

### 5.1.3 网络参数综合指令： ip

ip 是个指令喔！并不是那个 TCP/IP 的 IP 啦！这个 ip 指令的功能可多了！基本上，他就是整合了 ifconfig 与 route 这两个指令啰～不过， ip 可以达成的功能却又多更多！真是个相当厉害的指令。如果你有兴趣的话，请自行 vi /sbin/ifup ，就知道整个 ifup 就是利用 ip 这个指令来达成的。好了，如何使用呢？让我们来瞧一瞧先！

```
[root@www ~]# ip [option] [动作] [指令]
选项与参数：
option ：设定的参数，主要有：
    -s ：显示出该装置的统计数据(statistics)，例如总接受封包数等；
动作：亦即是可以针对哪些网络参数进行动作，包括有：
    link  ：关于装置 (device) 的相关设定，包括 MTU, MAC 地址等等
    addr/address ：关于额外的 IP 协议，例如多 IP 的达成等等；
    route ：与路由有关的相关设定 
```

由上面的语法我们可以知道， ip 除了可以设定一些基本的网络参数之外，还能够进行额外的 IP 协议，包括多 IP 的达成，真是太完美了！底下我们就分三个部分 (link, addr, route) 来介绍这个 ip 指令吧！

*   关于装置接口 (device) 的相关设定： ip link

ip link 可以设定与装置 (device) 有关的相关参数，包括 MTU 以及该网络接口的 MAC 等等，当然也可以启动 (up) 或关闭 (down) 某个网络接口啦！整个语法是这样的：

```
[root@www ~]# ip [-s] link show  &lt;== 单纯的查阅该装置相关的信息
[root@www ~]# ip link set [device] [动作与参数]
选项与参数：
show：仅显示出这个装置的相关内容，如果加上 -s 会显示更多统计数据；
set ：可以开始设定项目， device 指的是 eth0, eth1 等等界面代号；
动作与参数：包括有底下的这些动作：
   up&#124;down  ：启动 (up) 或关闭 (down) 某个接口，其他参数使用默认的以太网络；
   address  ：如果这个装置可以更改 MAC 的话，用这个参数修改！
   name     ：给予这个装置一个特殊的名字；
   mtu      ：就是最大传输单元啊！

# 范例一：显示出所有的接口信息
[root@www ~]# ip link show
1: lo: &lt;LOOPBACK,UP,LOWER_UP&gt; mtu 16436 qdisc noqueue state UNKNOWN
    link/loopback 00:00:00:00:00:00 brd 00:00:00:00:00:00
2: eth0: &lt;BROADCAST,MULTICAST,UP,LOWER_UP&gt; mtu 1500 qdisc pfifo_fast state UP qlen 1000
    link/ether 08:00:27:71:85:bd brd ff:ff:ff:ff:ff:ff
3: eth1: &lt;BROADCAST,MULTICAST&gt; mtu 1500 qdisc noop state DOWN qlen 1000
    link/ether 08:00:27:2a:30:14 brd ff:ff:ff:ff:ff:ff
4: sit0: &lt;NOARP&gt; mtu 1480 qdisc noop state DOWN
    link/sit 0.0.0.0 brd 0.0.0.0

[root@www ~]# ip -s link show eth0
2: eth0: &lt;BROADCAST,MULTICAST,UP,LOWER_UP&gt; mtu 1500 qdisc pfifo_fast state UP qlen 1000
    link/ether 08:00:27:71:85:bd brd ff:ff:ff:ff:ff:ff
    RX: bytes  packets  errors  dropped overrun mcast
    314685     3354     0       0       0       0
    TX: bytes  packets  errors  dropped carrier collsns
    27200      199      0       0       0       0 
```

使用 ip link show 可以显示出整个装置接口的硬件相关信息，如上所示，包括网卡地址(MAC)、MTU 等等， 比较有趣的应该是那个 sit0 的接口了，那个 sit0 的界面是用在 IPv4 及 IPv6 的封包转换上的， 对于我们仅使用 IPv4 的网络是没有作用的。 lo 及 sit0 都是主机内部所自行设定的。 而如果加上 -s 的参数后，则这个网络卡的相关统计信息就会被列出来， 包括接收 (RX) 及传送 (TX) 的封包数量等等，详细的内容与 ifconfig 所输出的结果相同的。

```
# 范例二：启动、关闭与设定装置的相关信息
[root@www ~]# ip link set eth0 up
# 启动 eth0 这个装置接口；

[root@www ~]# ip link set eth0 down
# 阿就关闭啊！简单的要命～

[root@www ~]# ip link set eth0 mtu 1000
# 更改 MTU 的值，达到 1000 bytes，单位就是 bytes 啊！ 
```

更新网络卡的 MTU 使用 ifconfig 也可以达成啊！没啥了不起，不过，如果是要更改『网络卡代号、 MAC 地址的信息』的话，那可就得使用 ip 啰～不过，设定前可能得要先关闭该网络卡，否则会不成功。 如下所示：

```
# 范例三：修改网络卡代号、MAC 等参数
[root@www ~]# ip link set eth0 name vbird
SIOCSIFNAME: Device or resource busy
# 因为该装置目前是启动的，所以不能这样做设定。你应该要这样做：

[root@www ~]# ip link set eth0 down       &lt;==关闭界面
[root@www ~]# ip link set eth0 name vbird &lt;==重新设定
[root@www ~]# ip link show                &lt;==观察一下
2: vbird: &lt;BROADCAST,MULTICAST,UP,LOWER_UP&gt; mtu 1500 qdisc pfifo_fast state UP qlen 1000
    link/ether 08:00:27:71:85:bd brd ff:ff:ff:ff:ff:ff
# 怕了吧！连网络卡代号都可以改变！不过，玩玩后记得改回来啊！
# 因为我们的 ifcfg-eth0 还是使用原本的装置代号！避免有问题，要改回来

[root@www ~]# ip link set vbird name eth0 &lt;==界面改回来

[root@www ~]# ip link set eth0 address aa:aa:aa:aa:aa:aa
[root@www ~]# ip link show eth0
# 如果你的网络卡支持硬件地址(MAC)可以更改的话，上面这个动作就可以更改
# 你的网络卡地址了！厉害吧！不过，还是那句老话，测试完之后请立刻改回来啊！ 
```

在这个装置的硬件相关信息设定上面，包括 MTU, MAC 以及传输的模式等等，都可以在这里设定。 有趣的是那个 address 的项目，那个项目后面接的可是硬件地址 (MAC) 而不是 IP 喔！ 很容易搞错啊！切记切记！更多的硬件参数可以使用 man ip 查阅一下与 ip link 有关的设定。

*   关于额外的 IP 相关设定： ip address

如果说 ip link 是与 [OSI 七层协定](http://linux.vbird.org/linux_server/0110network_basic.php#whatisnetwork_osi) 的第二层资料连阶层有关的话，那么 ip address (ip addr) 就是与第三层网络层有关的参数啦！ 主要是在设定与 IP 有关的各项参数，包括 netmask, broadcast 等等。

```
[root@www ~]# ip address show   &lt;==就是查阅 IP 参数啊！
[root@www ~]# ip address [add&#124;del] [IP 参数] [dev 装置名] [相关参数]
选项与参数：
show    ：单纯的显示出接口的 IP 信息啊；
add&#124;del ：进行相关参数的增加 (add) 或删除 (del) 设定，主要有：
    IP 参数：主要就是网域的设定，例如 192.168.100.100/24 之类的设定喔；
    dev    ：这个 IP 参数所要设定的接口，例如 eth0, eth1 等等；
    相关参数：主要有底下这些：
        broadcast：设定广播地址，如果设定值是 + 表示『让系统自动计算』
        label    ：亦即是这个装置的别名，例如 eth0:0 就是了！
        scope    ：这个界面的领域，通常是这几个大类：
                   global ：允许来自所有来源的联机；
                   site   ：仅支持 IPv6 ，仅允许本主机的联机；
                   link   ：仅允许本装置自我联机；
                   host   ：仅允许本主机内部的联机；
                   所以当然是使用 global 啰！预设也是 global 啦！

# 范例一：显示出所有的接口之 IP 参数：
[root@www ~]# ip address show
1: lo: &lt;LOOPBACK,UP,LOWER_UP&gt; mtu 16436 qdisc noqueue state UNKNOWN
    link/loopback 00:00:00:00:00:00 brd 00:00:00:00:00:00
    inet 127.0.0.1/8 scope host lo
    inet6 ::1/128 scope host
       valid_lft forever preferred_lft forever
2: eth0: &lt;BROADCAST,MULTICAST,UP,LOWER_UP&gt; mtu 1500 qdisc pfifo_fast state UP qlen 1000
    link/ether 08:00:27:71:85:bd brd ff:ff:ff:ff:ff:ff
    inet 192.168.1.100/24 brd 192.168.1.255 scope global eth0
    inet6 fe80::a00:27ff:fe71:85bd/64 scope link
       valid_lft forever preferred_lft forever
3: eth1: &lt;BROADCAST,MULTICAST&gt; mtu 1500 qdisc noop state DOWN qlen 1000
    link/ether 08:00:27:2a:30:14 brd ff:ff:ff:ff:ff:ff
4: sit0: &lt;NOARP&gt; mtu 1480 qdisc noop state DOWN
    link/sit 0.0.0.0 brd 0.0.0.0 
```

看到上面那个特殊的字体吗？没错！那就是 IP 参数啦！也是 ip address 最主要的功能。 底下我们进一步来新增虚拟的网络介面试看看：

```
# 范例二：新增一个接口，名称假设为 eth0:vbird 
[root@www ~]# ip address add 192.168.50.50/24 broadcast + \
&gt; dev eth0 label eth0:vbird
[root@www ~]# ip address show eth0
2: eth0: &lt;BROADCAST,MULTICAST,UP,LOWER_UP&gt; mtu 1500 qdisc pfifo_fast state UP qlen 1000
    link/ether 08:00:27:71:85:bd brd ff:ff:ff:ff:ff:ff
    inet 192.168.1.100/24 brd 192.168.1.255 scope global eth0
    inet 192.168.50.50/24 brd 192.168.50.255 scope global eth0:vbird
    inet6 fe80::a00:27ff:fe71:85bd/64 scope link
       valid_lft forever preferred_lft forever
# 看到上面的特殊字体了吧？多出了一行新的接口，且名称是 eth0:vbird
# 至于那个 broadcast + 也可以写成 broadcast 192.168.50.255 啦！

[root@www ~]# ifconfig
eth0:vbird Link encap:Ethernet  HWaddr 08:00:27:71:85:BD
          inet addr:192.168.50.50  Bcast:192.168.50.255  Mask:255.255.255.0
          UP BROADCAST RUNNING MULTICAST  MTU:1500  Metric:1
# 如果使用 ifconfig 就能够看到这个怪东西了！可爱吧！ ^_^

# 范例三：将刚刚的界面删除 
[root@www ~]# ip address del 192.168.50.50/24 dev eth0
# 删除就比较简单啊！ ^_^ 
```

*   关于路由的相关设定： ip route

这个项目当然就是路由的观察与设定啰！事实上， ip route 的功能几乎与 route 这个指令差不多，但是，他还可以进行额外的参数设计，例如 MTU 的规划等等，相当的强悍啊！

```
[root@www ~]# ip route show  &lt;==单纯的显示出路由的设定而已
[root@www ~]# ip route [add&#124;del] [IP 或网域] [via gateway] [dev 装置]
选项与参数：
show ：单纯的显示出路由表，也可以使用 list ；
add&#124;del ：增加 (add) 或删除 (del) 路由的意思。
    IP 或网域：可使用 192.168.50.0/24 之类的网域或者是单纯的 IP ；
    via     ：从那个 gateway 出去，不一定需要；
    dev     ：由那个装置连出去，这就需要了！
    mtu     ：可以额外的设定 MTU 的数值喔！

# 范例一：显示出目前的路由资料
[root@www ~]# ip route show
192.168.1.0/24 dev eth0  proto kernel  scope link  src 192.168.1.100
169.254.0.0/16 dev eth0  scope link  metric 1002
default via 192.168.1.254 dev eth0 
```

如上表所示，最简单的功能就是显示出目前的路由信息，其实跟 route 这个指令相同啦！ 指示必须要注意几个小东西：

*   proto：此路由的路由协议，主要有 redirect, kernel, boot, static, ra 等， 其中 kernel 指的是直接由核心判断自动设定。
*   scope：路由的范围，主要是 link ，亦即是与本装置有关的直接联机。

再来看一下如何进行路由的增加与删除吧！

```
# 范例二：增加路由，主要是本机直接可沟通的网域
[root@www ~]# ip route add 192.168.5.0/24 dev eth0
# 针对本机直接沟通的网域设定好路由，不需要透过外部的路由器
[root@www ~]# ip route show
192.168.5.0/24 dev eth0  scope link
....(以下省略)....

# 范例三：增加可以通往外部的路由，需透过 router 喔！
[root@www ~]# ip route add 192.168.10.0/24 via 192.168.5.100 dev eth0
[root@www ~]# ip route show
192.168.5.0/24 dev eth0  scope link
....(其他省略)....
192.168.10.0/24 via 192.168.5.100 dev eth0
# 仔细看喔，因为我有 192.168.5.0/24 的路由存在 (我的网卡直接联系)，
# 所以才可以将 192.168.10.0/24 的路由丢给 192.168.5.100 
# 那部主机来帮忙传递喔！与之前提到的 route 指令是一样的限制！

# 范例四：增加预设路由
[root@www ~]# ip route add default via 192.168.1.254 dev eth0
# 那个 192.168.1.254 就是我的预设路由器 (gateway) 的意思啊！ ^_^
# 真的记得，只要一个预设路由就 OK ！

# 范例五：删除路由
[root@www ~]# ip route del 192.168.10.0/24
[root@www ~]# ip route del 192.168.5.0/24 
```

事实上，这个 ip 的指令实在是太博大精深了！刚接触 Linux 网络的朋友，可能会看到有点晕～ 不要紧啦！你先会使用 ifconfig, ifup , ifdown 与 route 即可， 等以后有经验了之后，再继续回来玩 ip 这个好玩的指令吧！ ^_^ 有兴趣的话，也可以自行参考 ethtool 这个指令喔！ (man ethtool)。

* * *

### 5.1.4 无线网络： iwlist, iwconfig

这两个指令你必须要有无线网卡才能够进行喔！这两个指令的用途是这样的：

*   iwlist：利用无线网卡进行无线 AP 的侦测与取得相关的数据；
*   iwconfig：设定无线网卡的相关参数。

这两个指令的应用我们在第四章里面的[无线网卡设定](http://linux.vbird.org/linux_server/0130internet_connect.php#wireless_connect)谈了很多了， 所以这里我们不再详谈，有兴趣的朋友应该先使用 man iwlist 与 man iwconfig 了解一下语法， 然后再到前一章的无线网络小节查一查相关的用法，就了解了啦！ ^_^

* * *

### 5.1.5 手动使用 DHCP 自动取得 IP 参数： dhclient

如果你是使用 DHCP 协议在局域网络内取得 IP 的话，那么是否一定要去编辑 ifcfg-eth0 内的 BOOTPROTO 呢？ 嘿嘿！有个更快速的作法，那就是利用 dhclient 这个指令～因为这个指令才是真正发送 dhcp 要求工作的程序啊！那要如何使用呢？很简单！如果不考虑其他的参数，使用底下的方法即可：

```
[root@www ~]# dhclient eth0 
```

够简单吧！这样就可以立刻叫我们的网络卡以 dhcp 协议去尝试取得 IP 喔！

* * *

# 5.2 网络侦错与观察指令

## 5.2 网络侦错与观察指令

在网络的互助论坛中，最常听到的一句话就是：『高手求救！我的 Linux 不能连上网络了！』我的天吶！不能上网络的原因多的很！而要完全搞懂也不是一件简单的事情呢！ 不过，事实上我们可以自己使用测试软件来追踪可能的错误原因，而很多的网络侦测指令其实在 Linux 里头已经都预设存在了，只要你好好的学一学基本的侦测指令，那么一些朋友在告诉你如何侦错的时候， 你应该就立刻可以知道如何来搞定他啰！

其实我们在[第四章谈到的五个检查步骤](http://linux.vbird.org/linux_server/0130internet_connect.php#connect_fix_IP)已经是相当详细的网络侦错流程了！ 只是还有些重要的侦测指令也得要来了解一下才好！

* * *

### 5.2.1 两部主机两点沟通： ping

这个 ping 是很重要的指令，ping 主要透过 [ICMP 封包](http://linux.vbird.org/linux_server/0110network_basic.php#tcpip_network_icmp) 来进行整个网络的状况报告，当然啦，最重要的就是那个 ICMP type 0, 8 这两个类型， 分别是要求回报与主动回报网络状态是否存在的特性。要特别注意的是， ping 还是需要透过 [IP 封包](http://linux.vbird.org/linux_server/0110network_basic.php#tcpip_network_head)来传送 ICMP 封包的， 而 IP 封包里面有个相当重要的 TTL 属性，这是很重要的一个路由特性， 详细的 IP 与 ICMP 表头资料请参考[第二章网络基础](http://linux.vbird.org/linux_server/0110network_basic.php)的详细介绍。

```
[root@www ~]# ping [选项与参数] IP
选项与参数：
-c 数值：后面接的是执行 ping 的次数，例如 -c 5 ；
-n     ：在输出数据时不进行 IP 与主机名的反查，直接使用 IP 输出(速度较快)；
-s 数值：发送出去的 ICMP 封包大小，预设为 56bytes，不过你可以放大此一数值；
-t 数值：TTL 的数值，预设是 255，每经过一个节点就会少一；
-W 数值：等待响应对方主机的秒数。
-M [do&#124;dont] ：主要在侦测网络的 MTU 数值大小，两个常见的项目是：
   do  ：代表传送一个 DF (Don't Fragment) 旗标，让封包不能重新拆包与打包；
   dont：代表不要传送 DF 旗标，表示封包可以在其他主机上拆包与打包

# 范例一：侦测一下 168.95.1.1 这部 DNS 主机是否存在？
[root@www ~]# ping -c 3 168.95.1.1
PING 168.95.1.1 (168.95.1.1) 56(84) bytes of data.
64 bytes from 168.95.1.1: icmp_seq=1 ttl=245 time=15.4 ms
64 bytes from 168.95.1.1: icmp_seq=2 ttl=245 time=10.0 ms
64 bytes from 168.95.1.1: icmp_seq=3 ttl=245 time=10.2 ms

--- 168.95.1.1 ping statistics ---
3 packets transmitted, 3 received, 0% packet loss, time 2047ms
rtt min/avg/max/mdev = 10.056/11.910/15.453/2.506 ms 
```

ping 最简单的功能就是传送 ICMP 封包去要求对方主机回应是否存在于网络环境中，上面的响应消息当中，几个重要的项目是这样的：

*   64 bytes：表示这次传送的 ICMP 封包大小为 64 bytes 这么大，这是默认值， 在某些特殊场合中，例如要搜索整个网络内最大的 MTU 时，可以使用 -s 2000 之类的数值来取代；

*   icmp_seq=1：ICMP 所侦测进行的次数，第一次编号为 1 ；

*   ttl=243：TTL 与 IP 封包内的 TTL 是相同的，每经过一个带有 MAC 的节点 (node) 时，例如 router, bridge 时， TTL 就会减少一，预设的 TTL 为 255 ， 你可以透过 -t 150 之类的方法来重新设定预设 TTL 数值；

*   time=15.4 ms：响应时间，单位有 ms(0.001 秒)及 us(0.000001 秒)， 一般来说，越小的响应时间，表示两部主机之间的网络联机越良好！

如果你忘记加上 -c 3 这样的规定侦测次数，那就得要使用 [ctrl]-c 将他结束掉了！

例题：写一支脚本程序 ping.sh ，透过这支脚本程序，你可以用 ping 侦测整个网域的主机是否有响应。此外，每部主机的侦测仅等待一秒钟，也仅侦测一次。答：由于仅侦测一次且等待一秒，因此 ping 的选项为： -W1 -c1 ，而位于本机所在的区网为 192.168.1.0/24 ，所以可以这样写 (vim /root/bin/ping.sh)：

```
#!/bin/bash
for siteip in $(seq 1 254)
do
    site="192.168.1.${siteip}"
    ping -c1 -W1 ${site} &&gt; /dev/null
    if [ "$?" == "0" ]; then
        echo "$site is UP"
    else
        echo "$site is DOWN"
    fi
done 
```

特别注意一下，如果你的主机与待侦测主机并不在同一个网域内， 那么 TTL 预设使用 255 ，如果是同一个网域内，那么 TTL 预设则使用 64 喔！

*   用 ping 追踪路径中的最大 MTU 数值

    我们由第二章的[网络基础](http://linux.vbird.org/linux_server/0110network_basic.php)里面谈到加大讯框 (frame) 时， 对于网络效能是有帮助的，因为封包打包的次数会减少，加上如果整个传输的媒体都能够接受这个 frame 而不需要重新进行封包的拆解与重组的话，那么效能当然会更好，那个修改 frame 大小的参数就是 [MTU](http://linux.vbird.org/linux_server/0110network_basic.php#tcpip_link_mtu) 啦！

    好了，现在我们知道网络卡的 MTU 修改可以透过 ifconfig 或者是 ip 等指令来达成，那么追踪整个网络传输的最大 MTU 时，又该如何查询？呵呵！最简单的方法当然是透过 ping 传送一个大封包， 并且不许中继的路由器或 switch 将该封包重组，那就能够处理啦！没错！可以这样的：

    ```
    # 范例二：找出最大的 MTU 数值
    [root@www ~]# ping -c 2 -s 1000 -M do 192.168.1.254
    PING 192.168.1.254 (192.168.1.254) 1000(1028) bytes of data.
    1008 bytes from 192.168.1.254: icmp_seq=1 ttl=64 time=0.311 ms
    # 如果有响应，那就是可以接受这个封包，如果无响应，那就表示这个 MTU 太大了。

    [root@www ~]# ping -c 2 -s 8000 -M do 192.168.1.254
    PING 192.168.1.254 (192.168.1.254) 8000(8028) bytes of data.
    From 192.168.1.100 icmp_seq=1 Frag needed and DF set (mtu = 1500)
    # 这个错误讯息是说，本地端的 MTU 才到 1500 而已，你要侦测 8000 的 MTU
    # 根本就是无法达成的！那要如何是好？用前一小节介绍的 ip link 来进行 MTU 设定吧！ 
    ```

    不过，你需要知道的是，由于 [IP 封包表头 (不含 options) 就已经占用了 20 bytes](http://linux.vbird.org/linux_server/0110network_basic.php#tcpip_network_head) ，再加上 ICMP 的表头有 8 bytes ，所以当然你在使用 -s size 的时候，那个封包的大小就得要先扣除 (20+8=28) 的大小了。 因此如果要使用 MTU 为 1500 时，就得要下达『 ping -s 1472 -M do xx.yy.zz.ip 』才行啊！

    另外，由于本地端的网络卡 MTU 也会影响到侦测，所以如果想要侦测整个传输媒体的 MTU 数值， 那么每个可以调整的主机就得要先使用 ifcofig 或 ip 先将 MTU 调大，然后再去进行侦测， 否则就会出现像上面提供的案例一样，可能会出现错误讯息的！

    不过这个 MTU 不要随便调整啊！除非真的有问题。通常调整 MTU 的时间是在这个时候：

    *   因为全部的主机群都是在内部的区网，例如丛集架构 (cluster) 的环境下， 由于内部的网络节点都是我们可以控制的，因此可以透过修改 MTU 来增进网络效能；
    *   因为操作系统默认的 MTU 与你的网域不符，导致某些网站可以顺利联机，某些网站则无法联机。 以 Windows 操作系统作为联机分享的主机时，在 Client 端挺容易发生这个问题； 如果是要连上 Internet 的主机，注意不要随便调整 MTU ，因为我们无法知道 Internet 上面的每部机器能够支持的 MTU 到多大，因为......不是我们能够管的到的嘛 ^_^！ 另外，其实每种联机方式都有不同的 MTU 值，常见的各种接口的 MTU 值分别为︰

    ```
    | 网络接口 | MTU |
    | --- | --- |
    | Ethernet | 1500 |
    | PPPoE | 1492 |
    | Dial-up(Modem) | 576 | 
    ```

* * *

### 5.2.2 两主机间各节点分析： traceroute

我们前面谈到的指令大多数都是针对主机的网络参数设定所需要的，而 ping 是两部主机之间的回声与否判断， 那么有没有指令可以追踪两部主机之间通过的各个节点 (node) 通讯状况的好坏呢？举例来说，如果我们联机到 yahoo 的速度比平常慢，你觉得是 (1)自己的网络环境有问题？ (2)还是外部的 Internet 有问题？如果是 (1) 的话，我们当然需要检查自己的网络环境啊，看看是否又有谁中毒了？但如果是 Internet 的问题呢？那只有『等等等』啊！ 判断是 (1) 还是 (2) 就得要使用 traceroute 这个指令啦！

```
[root@www ~]# traceroute [选项与参数] IP
选项与参数：
-n ：可以不必进行主机的名称解析，单纯用 IP ，速度较快！
-U ：使用 UDP 的 port 33434 来进行侦测，这是预设的侦测协议；
-I ：使用 ICMP 的方式来进行侦测；
-T ：使用 TCP 来进行侦测，一般使用 port 80 测试
-w ：若对方主机在几秒钟内没有回声就宣告不治...预设是 5 秒
-p 埠号：若不想使用 UDP 与 TCP 的预设埠号来侦测，可在此改变埠号。
-i 装置：用在比较复杂的环境，如果你的网络接口很多很复杂时，才会用到这个参数；
         举例来说，你有两条 ADSL 可以连接到外部，那你的主机会有两个 ppp，
         你可以使用 -i 来选择是 ppp0 还是 ppp1 啦！
-g 路由：与 -i 的参数相仿，只是 -g 后面接的是 gateway 的 IP 就是了。

# 范例一：侦测本机到 yahoo 去的各节点联机状态
[root@www ~]# traceroute -n tw.yahoo.com
traceroute to tw.yahoo.com (119.160.246.241), 30 hops max, 40 byte packets
 1  192.168.1.254  0.279 ms  0.156 ms  0.169 ms
 2  172.20.168.254  0.430 ms  0.513 ms  0.409 ms
 3  10.40.1.1  0.996 ms  0.890 ms  1.042 ms
 4  203.72.191.85  0.942 ms  0.969 ms  0.951 ms
 5  211.20.206.58  1.360 ms  1.379 ms  1.355 ms
 6  203.75.72.90  1.123 ms  0.988 ms  1.086 ms
 7  220.128.24.22  11.238 ms  11.179 ms  11.128 ms
 8  220.128.1.82  12.456 ms  12.327 ms  12.221 ms
 9  220.128.3.149  8.062 ms  8.058 ms  7.990 ms
10  * * *
11  119.160.240.1  10.688 ms  10.590 ms 119.160.240.3  10.047 ms
12  * * * &lt;==可能有防火墙装置等情况发生所致 
```

这个 traceroute 挺有意思的，这个指令会针对欲连接的目的地之所有 node 进行 UDP 的逾时等待， 例如上面的例子当中，由鸟哥的主机连接到 Yahoo 时，他会经过 12 个节点以上，traceroute 会主动的对这 12 个节点做 UDP 的回声等待，并侦测回复的时间，每节点侦测三次，最终回传像上头显示的结果。 你可以发现每个节点其实回复的时间大约在 50 ms 以内，算是还可以的 Internet 环境了。

比较特殊的算是第 10/12 个，会回传星号的，代表该 node 可能设有某些防护措施，让我们发送的封包信息被丢弃所致。 因为我们是直接透过路由器转递封包，并没有进入路由器去取得路由器的使用资源，所以某些路由器仅支持封包转递， 并不会接受来自客户端的各项侦测啦！此时就会出现上述的问题。因为 traceroute 预设使用 UDP 封包，如果你想尝试使用其他封包， 那么 -I 或 -T 可以试看看啰！

由于目前 UDP/ICMP 的攻击层出不穷，因此很多路由器可能就此取消这两个封包的响应功能。所以我们可以使用 TCP 来侦测呦！ 例如使用同样的方法，透过等待时间 1 秒，以及 TCP 80 埠口的情况下，可以这样做：

```
[root@www ~]# traceroute -w 1 -n -T tw.yahoo.com 
```

* * *

### 5.2.3 察看本机的网络联机与后门： netstat

如果你觉得你的某个网络服务明明就启动了，但是就是无法造成联机的话，那么应该怎么办？ 首先你应该要查询一下自己的网络接口所监听的端口 (port) 来看看是否真的有启动，因为有时候屏幕上面显示的 [OK] 并不一定是 OK 啊！ ^_^

```
[root@www ~]# netstat -[rn]       &lt;==与路由有关的参数
[root@www ~]# netstat -[antulpc]  &lt;==与网络接口有关的参数
选项与参数：
与路由 (route) 有关的参数说明：
-r  ：列出路由表(route table)，功能如同 route 这个指令；
-n  ：不使用主机名与服务名称，使用 IP 与 port number ，如同 route -n
与网络接口有关的参数：
-a  ：列出所有的联机状态，包括 tcp/udp/unix socket 等；
-t  ：仅列出 TCP 封包的联机；
-u  ：仅列出 UDP 封包的联机；
-l  ：仅列出有在 Listen (监听) 的服务之网络状态；
-p  ：列出 PID 与 Program 的檔名；
-c  ：可以设定几秒钟后自动更新一次，例如 -c 5 每五秒更新一次网络状态的显示；

# 范例一：列出目前的路由表状态，且以 IP 及 port number 显示：
[root@www ~]# netstat -rn
Kernel IP routing table
Destination     Gateway         Genmask         Flags   MSS Window  irtt Iface
192.168.1.0     0.0.0.0         255.255.255.0   U         0 0          0 eth0
169.254.0.0     0.0.0.0         255.255.0.0     U         0 0          0 eth0
0.0.0.0         192.168.1.254   0.0.0.0         UG        0 0          0 eth0
# 其实这个参数就跟 route -n 一模一样，对吧！这不是 netstat 的主要功能啦！

# 范例二：列出目前的所有网络联机状态，使用 IP 与 port number
[root@www ~]# netstat -an
Active Internet connections (servers and established)
Proto Recv-Q Send-Q Local Address     Foreign Address     State
....(中间省略)....
tcp        0      0 127.0.0.1:25      0.0.0.0:*           LISTEN
tcp        0     52 192.168.1.100:22  192.168.1.101:1937  ESTABLISHED
tcp        0      0 :::22             :::*                LISTEN
....(中间省略)....
Active UNIX domain sockets (servers and established)
Proto RefCnt Flags    Type    State      I-Node Path
unix  2      [ ACC ]  STREAM  LISTENING  11075  @/var/run/hald/dbus-uukdg1qMPh
unix  2      [ ACC ]  STREAM  LISTENING  10952  /var/run/dbus/system_bus_socket
unix  2      [ ACC ]  STREAM  LISTENING  11032  /var/run/acpid.socket
....(底下省略).... 
```

netstat 的输出主要分为两大部分，分别是 TCP/IP 的网络接口部分，以及传统的 Unix socket 部分。 还记得我们在基础篇里面曾经谈到档案的类型吗？那个 socket 与 FIFO 档案还记得吧？ 那就是在 Unix 接口用来做为程序数据交流的接口了，也就是上头表格内看到的 Active Unix domain sockets 的内容啰～

通常鸟哥都是建议加上『 -n 』这个参数的，因为可以避过主机名与服务名称的反查，直接以 IP 及端口号码 (port number) 来显示，显示的速度上会快很多！至于在输出的讯息当中， 我们先来谈一谈关于网络联机状态的输出部分，他主要是分为底下几个大项：

*   Proto：该联机的封包协议，主要为 TCP/UDP 等封包；
*   Recv-Q：非由用户程序连接所复制而来的总 bytes 数；
*   Send-Q：由远程主机所传送而来，但不具有 ACK 标志的总 bytes 数， 意指主动联机 SYN 或其他标志的封包所占的 bytes 数；
*   Local Address：本地端的地址，可以是 IP (-n 参数存在时)， 也可以是完整的主机名。使用的格是就是『 IP:port 』只是 IP 的格式有 IPv4 及 IPv6 的差异。 如上所示，在 port 22 的接口中，使用的 :::22 就是针对 IPv6 的显示，事实上他就相同于 0.0.0.0:22 的意思。 至于 port 25 仅针对 lo 接口开放，意指 Internet 基本上是无法连接到我本机的 25 埠口啦！
*   Foreign Address：远程的主机 IP 与 port number
*   stat：状态栏，主要的状态含有：

    *   ESTABLISED：已建立联机的状态；
    *   SYN_SENT：发出主动联机 (SYN 标志) 的联机封包；
    *   SYN_RECV：接收到一个要求联机的主动联机封包；
    *   FIN_WAIT1：该插槽服务(socket)已中断，该联机正在断线当中；
    *   FIN_WAIT2：该联机已挂断，但正在等待对方主机响应断线确认的封包；
    *   TIME_WAIT：该联机已挂断，但 socket 还在网络上等待结束；
    *   LISTEN：通常用在服务的监听 port ！可使用『 -l 』参数查阅。

基本上，我们常常谈到的 netstat 的功能，就是在观察网络的联机状态了，而网络联机状态中， 又以观察『我目前开了多少的 port 在等待客户端的联机』以及 『目前我的网络联机状态中，有多少联机已建立或产生问题』最常见。 那你如何了解与观察呢？通常鸟哥是这样处理的：

```
# 范例三：秀出目前已经启动的网络服务
[root@www ~]# netstat -tulnp
Active Internet connections (only servers)
Proto Recv-Q Send-Q Local Address  Foreign Address   State   PID/Program name
tcp        0      0 0.0.0.0:34796  0.0.0.0:*         LISTEN 987/rpc.statd
tcp        0      0 0.0.0.0:111    0.0.0.0:*         LISTEN 969/rpcbind
tcp        0      0 127.0.0.1:25   0.0.0.0:*         LISTEN 1231/master
tcp        0      0 :::22          :::*              LISTEN 1155/sshd
udp        0      0 0.0.0.0:111    0.0.0.0:*                969/rpcbind
....(底下省略)....
# 上面最重要的其实是那个 -l 的参数，因为可以仅列出有在 Listen 的 port 
```

你可以发现很多的网络服务其实仅针对本机的 lo 开放而已，因特网是连接不到该埠口与服务的。 而由上述的数据我们也可以看到，启动 port 111 的，其实就是 rpcbind 那只程序，那如果想要关闭这个埠口， 你可以使用 kill 删除 PID 969，也可以使用 killall 删除 rpcbind 这个程序即可。如此一来， 很轻松的你就能知道哪个程序启动了哪些端口啰！

```
# 范例四：观察本机上头所有的网络联机状态
[root@www ~]# netstat -atunp
Active Internet connections (servers and established)
Proto Recv-Q Send-Q Local Address     Foreign Address     State       PID/Program
tcp        0      0 0.0.0.0:111       0.0.0.0:*           LISTEN      969/rpcbind
tcp        0      0 0.0.0.0:22        0.0.0.0:*           LISTEN      1155/sshd
tcp        0      0 127.0.0.1:25      0.0.0.0:*           LISTEN      1231/master
tcp        0     52 192.168.1.100:22  192.168.1.101:1937  ESTABLISHED 4716/0
....(底下省略).... 
```

看到上头的特殊字体吧？那代表目前已经建立联机的一条网络联机，他是由远程主机 192.168.1.101 启动一个大于 1024 的埠口向本地端主机 192.168.1.100 的 port 22 进行的一条联机， 你必须要想起来的是：『Client 端是随机取一个大于 1024 以上的 port 进行联机』，此外『只有 root 可以启动小于 1024 以下的 port 』，那就看的懂上头那条联机啰！如果这条联机你想要砍掉他的话， 看到最右边的 4716 了没？ kill 会用吧！ ^_^

至于传统的 Unix socket 的数据，记得使用 man netstat 查阅一下吧！ 这个 Unix socket 通常是用在一些仅在本机上运作的程序所开启的插槽接口文件， 例如 X Window 不都是在本机上运作而已吗？那何必启动网络的 port 呢？当然可以使用 Unix socket 啰，另外，例如 Postfix 这一类的网络服务器，由于很多动作都是在本机上头来完成的， 所以以会占用很多的 Unix socket 喔！

例题：请说明服务名称与 port number 的对应在 Linux 当中，是用那个档案来设定对应的？答：/etc/services

* * *

### 5.2.4 侦测主机名与 IP 对应： host, nslookup

关于主机名与 IP 的对应中，我们主要介绍的是 DNS 客户端功能的 dig 这个指令。不过除了这个指令之外， 其实还有两个更简单的指令，那就是 host 与 nslookup 啦！底下让我们来聊聊这两个指令吧！

*   host

这个指令可以用来查出某个主机名的 IP 喔！举例来说，我们想要知道 tw.yahoo.com 的 IP 时，可以这样做：

```
[root@www ~]# host [-a] hostname [server]
选项与参数：
-a ：列出该主机详细的各项主机名设定数据
[server] ：可以使用非为 /etc/resolv.conf 的 DNS 服务器 IP 来查询。

# 范例一：列出 tw.yahoo.com 的 IP
[root@www ~]# host tw.yahoo.com
tw.yahoo.com is an alias for tw-cidr.fyap.b.yahoo.com.
tw-cidr.fyap.b.yahoo.com is an alias for tw-tpe-fo.fyap.b.yahoo.com.
tw-tpe-fo.fyap.b.yahoo.com has address 119.160.246.241 
```

瞧！IP 是 119.160.246.241 啊！很简单就可以查询到 IP 了！那么这个 IP 是向谁查询的呢？其实就是写在 [/etc/resolv.conf](http://linux.vbird.org/linux_server/0130internet_connect.php#resolv) 那个档案内的 DNS 服务器 IP 啦！如果不想要使用该档案内的主机来查询，也可以这样做：

```
[root@www ~]# host tw.yahoo.com 168.95.1.1
Using domain server:
Name: 168.95.1.1
Address: 168.95.1.1#53
Aliases:

tw.yahoo.com is an alias for tw-cidr.fyap.b.yahoo.com.
tw-cidr.fyap.b.yahoo.com is an alias for tw-tpe-fo.fyap.b.yahoo.com.
tw-tpe-fo.fyap.b.yahoo.com has address 119.160.246.241 
```

会告诉我们所使用来查询的主机是哪一部吶！这样就够清楚了吧！不过，再怎么清楚也比不过 dig 这个指令的，所以这个指令仅是参考参考啦！

*   nslookup

这玩意儿的用途与 host 基本上是一样的，就是用来作为 IP 与主机名对应的检查， 同样是使用 [/etc/resolv.conf](http://linux.vbird.org/linux_server/0130internet_connect.php#resolv) 这个档案来作为 DNS 服务器的来源选择。

```
[root@www ~]# nslookup [-query=[type]] [hostname&#124;IP]
选项与参数：
-query=type：查询的类型，除了传统的 IP 与主机名对应外，DNS 还有很多信息，
             所以我们可以查询很多不同的信息，包括 mx, cname 等等，
             例如： -query=mx 的查询方法！

# 范例一：找出 www.google.com 的 IP
[root@www ~]# nslookup www.google.com
Server:         168.95.1.1
Address:        168.95.1.1#53

Non-authoritative answer:
www.google.com  canonical name = www.l.google.com.
Name:   www.l.google.com
Address: 74.125.71.106
....(底下省略)....

# 范例二：找出 168.95.1.1 的主机名
[root@www ~]# nslookup 168.95.1.1
Server:         168.95.1.1
Address:        168.95.1.1#53

1.1.95.168.in-addr.arpa name = dns.hinet.net. 
```

如何，看起来与 host 差不多吧！不过，这个 nslookup 还可以由 IP 找出主机名喔！ 例如那个范例二，他的主机名是： dns.hinet.net 哩！目前大家都建议使用 dig 这个指令来取代 nslookup ，我们会在[第十九章 DNS 服务器](http://linux.vbird.org/linux_server/0350dns.php)那时再来好好谈一谈吧！

* * *

# 5.3 远程联机指令与实时通讯软件

## 5.3 远程联机指令与实时通讯软件

啥是远程联机呢？其实就是在不同的计算机之间进行登入的情况啦！我们可以透过 telnet, ssh 或者是 ftp 等协议来进行远程主机的登入。底下我们就分别来介绍一下这些基本的指令吧！这里仅是谈到客户端功能喔， 相关的服务器我们则会在后续进行说明的。

* * *

### 5.3.1 终端机与 BBS 联机： telnet

telnet 是早期我们在个人计算机上面要链接到服务器工作时，最重要的一个软件了！他不但可以直接连接到服务器上头， 还可以用来连结 BBS 呢！非常棒！不过， telnet 本身的数据在传送的时候是使用明码 (原始的数据，没有加密) ， 所以数据在 Internet 上面跑的时候，会比较危险一点 (就怕被别人监听啊)。 更详细的资料我们会在[第十一章远程联机服务器](http://linux.vbird.org/linux_server/0310telnetssh.php)内做介绍的。

```
[root@www ~]# telnet [host&#124;IP [port]]

# 范例一：连结到台湾相当热门的 PTT BBS 站 ptt.cc
[root@www ~]# yum install telnet  &lt;==默认没有安装这软件
[root@www ~]# telnet ptt.cc
    欢迎来到 批踢踢实业坊 目前有【100118】名使用者与您一同对抗炎炎夏日。

请输入代号，或以 guest 参观，或以 new 注册:  
[高手召集令] 台湾黑客年会 暑假与你骇翻南港 http://reg.hitcon.org/hit2011
要学计算机，首选台湾大学信息训练班!  http://tinyurl.com/3z42apw 
```

如上所示，我们可以透过 telnet 轻易的连结到 BBS 上面，而如果你的主机有开启 telnet 服务器服务的话，同样的利用『 telnet IP 』并且输入账号与密码之后，就能够登入主机了。 另外，在 Linux 上的 telnet 软件还提供了 Kerberos 的认证方式，有兴趣的话请自行参阅 man telnet 的说明。

除了连结到服务器以及连结到 BBS 站之外， telnet 还可以用来连结到某个 port (服务) 上头吶！ 举例来说，我们可以用 telnet 连接到 port 110 ，看看这个 port 是否有正确的启动呢？

```
# 范例二：侦测本机端的 110 这个 port 是否正确启动？
[root@www ~]# telnet localhost 110
Trying 127.0.0.1...
telnet: connect to address 127.0.0.1: Connection refused
# 如果出现这样的讯息，代表这个 port 没有启动或者是这个联机有问题，
# 因为你看到那个 refused 嘛！

[root@www ~]# telnet localhost 25
Trying ::1...
Connected to localhost.
Escape character is '^]'.
220 www.centos.vbird ESMTP Postfix
ehlo localhost
250-www.centos.vbird
250-PIPELINING
250-SIZE 10240000
....(中间省略)....
250 DSN
quit
221 2.0.0 Bye
Connection closed by foreign host. 
```

瞧！根据输出的结果，我们就能够知道这个通讯协议 (port number 提供的通讯协议功能) 是否有成功的启动吶！ 而在每个 port 所监听的服务都有其特殊的指令，例如上述的 port 25 就是在本机接口所提供的电子邮件服务， 那个服务所支持的指令就如同上面使用的数据一样，但是其他的 port 就不见得支持这个『 ehlo 』的命令， 因为不同的 port 有不同的程序嘛！所以当然支持的命令就不同啰！

* * *

### 5.3.2 FTP 联机软件： ftp, lftp

现在的人们由于有高容量的 email 可以用，因此传送档案可以很轻松的透过 email 。不过 email 还是有单封信件容量限制， 如果想要一口气传送个几百 MB 的档案，恐怕还是得要透过 FTP 这个通讯协议才行啊！文字接口的 FTP 软件主要有 ftp, lftp 两个，图形接口的呢？在 CentOS 上面预设有 gftp 这个好用的东东。在这里我们仅介绍文字接口的两个指令而已。

*   ftp

ftp 这个指令很简单，用在处理 FTP 服务器的下载数据啦。由于鸟哥所在的位置在昆山科大，因此这里使用昆山科大的 FTP 服务器为例：

```
[root@www ~]# ftp [host&#124;IP] [port]

# 范例一：联机到昆山科大去看看
[root@www ~]# yum install ftp
[root@www ~]# ftp ftp.ksu.edu.tw
Connected to ftp.ksu.edu.tw (120.114.150.21).
220---------- Welcome to Pure-FTPd [privsep] ----------
220-You are user number 1 of 50 allowed.
220-Local time is now 16:25\. Server port: 21.
220-Only anonymous FTP is allowed here  &lt;==讯息要看啊！这个 FTP 仅支援匿名
220-IPv6 connections are also welcome on this server.
220 You will be disconnected after 5 minutes of inactivity.
Name (ftp.ksu.edu.tw:root): anonymous  &lt;==鸟哥这里用匿名登录！
230 Anonymous user logged in            &lt;==嗯！确实是匿名登录了！
Remote system type is UNIX.
Using binary mode to transfer files.
ftp&gt;                &lt;==最终登入的结果看起来是这样！
ftp&gt; help           &lt;==提供需要的指令说明，可以常参考！
ftp&gt; dir            &lt;==显示远程服务器的目录内容 (文件名列表)
ftp&gt; cd /pub        &lt;==变换目录到 /pub 当中
ftp&gt; get filename   &lt;==下载单一档案，档名为 filename 
ftp&gt; mget filename* &lt;==下载多个档案，可使用通配符 *
ftp&gt; put filename   &lt;==上传 filename 这个档案到服务器上
ftp&gt; delete file    &lt;==删除主机上的 file 这个档案
ftp&gt; mkdir dir      &lt;==建立 dir 这个目录
ftp&gt; lcd /home      &lt;==切换『本地端主机』的工作目录
ftp&gt; passive        &lt;==启动或关闭 passive 模式
ftp&gt; binary         &lt;==数据传输模式设定为 binary 格式
ftp&gt; bye            &lt;==结束 ftp 软件的使用 
```

FTP 其实算是一个很麻烦的协议，因为他使用两个 port 分别进行命令与数据的交流，详细的数据我们会在第二十一章的 FTP 服务器内详谈，这里我们先单纯的介绍一下如何使用 ftp 这个软件。首先我们当然是需要登入啰， 所以在上头的表格当中我们当然需要填入账号与密码了。不过由于昆山科大仅提供匿名登录，而匿名登录者的账号就是『 anonymous 』所以直接填写那个账号即可。如果是私人的 FTP 时，才需要提供一组完整的账号与密码啦！

登入 FTP 主机后，就能够使用 ftp 软件的功能进行上传与下载的动作，几个常用的 ftp 内指令如上表，不过，鸟哥建议你可以连到大学的 FTP 网站后，使用 help (或问号 ?) 来参考可用的指令，然后尝试下载以测试使用一下这个指令吧！这样以后没有浏览器的时候，你也可以到 ftp 下载了呢！不错吧！另外你要注意的是，离开 ftp 软件时，得要输入『 bye 』喔！不是『 exit 』啦！

如果由于某些理由，让你的 FTP 主机的 port 开在非正规的埠口时，那你就可以利用底下的方式来连接到该部主机喔！

```
[root@www ~]# ftp hostname 318
# 假设对方主机的 ftp 服务开启在 318 这个 port 啊！ 
```

*   lftp (自动化脚本)

单纯使用 ftp 总是觉得很麻烦，有没有更快速的 ftp 用户软件呢？让我们可以使用类似网址列的方式来登入 FTP 服务器啊？有的，那就是 lftp 的功能了！ lftp 预设使用匿名登录 FTP 服务器，可以使用类似网址列的方式取得数据， 使用上比单纯的 ftp 要好用些。此外，由于可在指令列输入账号/密码，可以辅助进行程序脚本的设计喔！

```
[root@www ~]# lftp [-p port] [-u user[,pass]] [host&#124;IP]
[root@www ~]# lftp -f filename
[root@www ~]# lftp -c "commands"
选项与参数：
-p  ：后面可以直接接上远程 FTP 主机提供的 port
-u  ：后面则是接上账号与密码，就能够连接上远程主机了
      如果没有加账号密码， lftp 默认会使用 anonymous 尝试匿名登录
-f  ：可以将指令写入脚本中，这样可以帮助进行 shell script 的自动处理喔！
-c  ：后面直接加上所需要的指令。

# 范例一：利用 lftp 登入昆山科大的 FTP 服务器
[root@www ~]# yum install lftp
[root@www ~]# lftp ftp.ksu.edu.tw
lftp ftp.ksu.edu.tw:~&gt;  
# 瞧！一下子就登入了！很快乐吧！ ^_^！你同样可使用 help 去查阅相关内部指令 
```

至于登入 FTP 主机后，一样可以使用『help』来显示出可以执行的指令，与 ftp 很类似啦！不过多了书签的功能，而且也非常的类似 bash 吶！很不错呦！除了这个好用的文字接口的 FTP 软件之外，事实上还有很多图形接口的好用软件呢！ 最常见的就是 gftp 了，非常的容易上手喔！ CentOS 本身就有提供 gftp 了，你可以拿出原版的光盘来安装，然后进入 X Window 后， 启动一个 shell ，输入『 gftp 』就能够发现他的好用啦！

如果你想要定时的去捉下昆山科大 FTP 网站下的 /pub/CentOS/RPM-GPG* 的档案时，那么那个脚本应该要怎么写呢？ 我们尝试来写写看吧！

```
# 使用档案配合 lftp 去处理时：
[root@www ~]# mkdir lftp; cd lftp
[root@www lftp]# vim lftp.ksu.sh
open ftp.ksu.edu.tw
cd /pub/CentOS/
mget -c -d RPM-GPG*
bye
[root@www lftp]# lftp -f lftp.ksu.sh
[root@www lftp]# ls
lftp.ksu.sh      RPM-GPG-KEY-CentOS-3 RPM-GPG-KEY-CentOS-4 RPM-GPG-KEY-CentOS-6
RPM-GPG-KEY-beta RPM-GPG-KEY-centos4  RPM-GPG-KEY-CentOS-5 

# 直接将要处理的动作加入 lftp 指令中
[root@www lftp]# vim lftp.ksu.sh
lftp -c "open ftp.ksu.edu.tw
cd /pub/CentOS/
mget -c -d RPM-GPG*
bye"
[root@www lftp]# sh lftp.ksu.sh 
```

若为非匿名登录时，则可以使用『 open -u username,password hostname 』修改 lftp.ksu.sh 的第一行！ 如果再将这个脚本写入 crontab 当中，你就可以定时的以 FTP 进行上传/下载的功能啰！这就是文字指令的好处！

* * *

### 5.3.3 图形接口的实时通讯软件： pidgin (gaim 的延伸)

现在应该大家都知道什么是 MSN, 雅虎实时通以及其他的通讯软件吧？那么要连上这些服务器时，该怎么处理哪？很简单，在 X Window 底下使用 pidgin 就好了！简直简单到不行～请先进入 X Window 系统，然后经过『应用程序』--> 『因特网』-->『Pidgin 网络实时通』启动他即可 (请注意你必须已经安装了 pidgin 了，可用 yum install pidgin 处理)。

不过，伤脑筋的是，我们所安装的 basic server 类型的 CentOS 6.x 主要做为服务器之用，所以连图形接口也没有给我们。 所以，鸟哥又用另外一部主机安装成 Desktop 的模式，利用该部主机来测试 pidgin 这玩意儿的！因此， 底下的练习你也可以先略过，等到你安装另一部 Desktop linux 时再来玩玩！

![](img/pidgin-1.gif) 图 5.3-1、pidgin 的欢迎画面

在上图中按下『新增』，然后你会看到如下的画面：

![](img/pidgin-2.gif) 图 5.3-2、pidgin 支持的实时通讯数据

很神奇的是， pidgin 支持的通讯有够多的！我们使用 MSN 来作个解释好了：

![](img/pidgin-3.gif) 图 5.3-3、设定 MSN 的账号示意图

如上图，在画面中输入你的账号与密码，如果是在公用的计算机上，千万不要按下『记住密码』项目喔！按下新增后， pidgin 预设就会尝试登入了！登入后的画面如下所示：

![](img/pidgin-4.gif) 图 5.3-4、使用 pidgin 的 MSN 方式进行连天啰

如果想要注销了，那么就按下图 5.3-4 最右边那个窗口，将『启动』的那个方框勾选取消，你就直接注销啰！

* * *

# 5.4 文字接口网页浏览

## 5.4 文字接口网页浏览

什么？文字界面竟然有浏览器！别逗了好不好？呵呵！谁有那个时间在逗你呦！真的啦！有这个东西， 是在文字界面下上网浏览的好工具！分别是 links 及 wget 这两个宝贝蛋，但是，你必需要确定你已经安装了这两个套件才行。 好佳在的是，CentOS 预设这两个玩意儿都有安装喔！底下就让我们来聊一聊这两个好用的家伙吧！

* * *

### 5.4.1 文字浏览器：links

其实早期鸟哥最常使用的是 lynx 这个文字浏览器，不过 CentOS 从 5.x 以后默认使用的文字浏览器是 links 这一支，这两支的使用方式又非常的类似，因此，在这一版当中，我们就仅介绍 links 啰！若对 lynx 有兴趣的话， 自己 man 一下吧！

这个指令可以让我们来浏览网页，但鸟哥认为，这个档案最大的功能是在『 查阅 Linux 本机上面以 HTML 语法写成的文件数据 (document)』 怎么说呢？如果你曾经到 Linux 本机底下的 /usr/share/doc 这个目录看过文件数据的话， 就会常常发现一些网页档案，使用 vi 去查阅时，老是看到一堆 HTML 的语法！有碍阅读啊～ 这时候使用 links 就是个好方法啦！可以看的清清楚楚啊！ ^_^

```
[root@www ~]# links [options] [URL]
选项与参数：
-anonymous [0&#124;1]：是否使用匿名登录的意思；
-dump [0&#124;1]     ：是否将网页的数据直接输出到 standard out 而非 links 软件功能
-dump_charset   ：后面接想要透过 dump 输出到屏幕的语系编码，big5 使用 cp950 喔

# 范例一：浏览 Linux kernel 网站
[root@www ~]# links http://www.kernel.org 
```

当我直接输入 links 网站网址后，就会出现如下的图示：

![](img/centos6-links-1.gif) 图 5.4-1、使用 links 查询网页数据的显示结果

上面这个画面的基本说明如下：

*   进入画面之后，由于是文字型态，所以编排可能会有点位移！不过不打紧！不会影响我们看咚咚！
*   这个时候可以使用『上下键』来让光标在上面的选项当中(如信箱、书签等等的)，按下 Enter 就进入该页面
*   可以使用『左右键』来移动『上一页或下一页』
*   一些常见功能按键：
    *   h：history ，曾经浏览过的 URL 就显示到画面中
    *   g：Goto URL，按 g 后输入网页地址(URL) 如 :[`www.abc.edu/等`](http://www.abc.edu/等)
    *   d：download，将该链接数据下载到本机成为档案；
    *   q：Quit，离开 links 这个软件；
    *   o：Option，进入功能参数的设定值修改中，最终可写入 ~/.elinks/elinks.conf 中
    *   Ctrl+C ：强迫切断 links 的执行。
    *   箭头键:
        *   上 ：移动光标至本页中 "上一个可连结点" .
        *   下 ：移动光标至本页中 "下一个可连结点" .
        *   左 ：back. 跳回上一页.
        *   右 ：进入反白光标所链接之网页.
        *   ENTER 同鼠标 "右" 键.

至于如果是浏览 Linux 本机上面的网页档案，那就可以使用如下的方式：

```
[root@www ~]# links /usr/share/doc/HTML/index.html 
```

在鸟哥的 CentOS 6.x 当中，有这么一个档案，我就可以利用 links 来取出察看吶！显示的结果有点像底下这样：

![](img/centos6-links-2.gif) 图 5.4-2、使用 links 查询本机的 HTML 文件档案

当然啦！因为你的环境可能是在 Linux 本机的 tty1~tty6 ，所以无法显示出中文，这个时候你就得要设定为： 『LANG=en_US』之类的语系设定才行喔！另外，如果某些时刻你必须上网点选某个网站以自动取得更新时。 举例来说，早期的自动在线更新主机名系统，仅支持网页更新，那你如何进行更新呢？嘿嘿！可以使用 links 喔！利用 -dump 这个参数处理先：

```
# 透过 links 将 tw.yahoo.com 的网页内容整个抓下来储存
[root@www ~]# links -dump http://tw.yahoo.com &gt; yahoo.html

# 某个网站透过 GET 功能可以上传账号为 user 密码为 pw ，用文字接口处理为：
[root@www ~]# links -dump \
&gt; http://some.site.name/web.php?name=user&password=pw &gt; testfile 
```

上面的网站后面有加个问号 (?) 对吧？后面接的则是利用网页的『 GET 』功能取得的各项变量数据， 利用这个功能，我们就可以直接点选到该网站上啰！非常的方便吧！而且会将执行的结果输出到 testfile 档案中，不过如果网站提供的数据是以『 POST 』为主的话，那鸟哥就不知道如何搞定了。 GET 与 POST 是 WWW 通讯协议中，用来将数据透过浏览器上传到服务器端的一种方式， 一般来说，目前讨论区或部落格等，大多使用可以支持较多数据的 POST 方式上传啦！ 关于 GET 与 POST 的相关信息我们会在第二十章 WWW 服务器当中再次的提及！

* * *

### 5.4.2 文字接口下载器： wget

如果说 links 是在进行网页的『浏览』，那么 wget 就是在进行『网页数据的取得』。举例来说，我们的 Linux 核心是放置在 www.kernel.org 内，主要同时提供 ftp 与 http 来下载。我们知道可以使用 lftp 来下载数据，但如果想要用浏览器来下载呢？那就利用 wget 吧！

```
[root@www ~]# wget [option] [网址]
选项与参数：
若想要联机的网站有提供账号与密码的保护时，可以利用这两个参数来输入喔！
--http-user=usrname
--http-password=password
--quiet ：不要显示 wget 在抓取数据时候的显示讯息
更多的参数请自行参考 man wget 吧！ ^_^

# 范例一：请下载 2.6.39 版的核心
[root@www ~]# wget  \
&gt; http://www.kernel.org/pub/linux/kernel/v2.6/linux-2.6.39.tar.bz2
--2011-07-18 16:58:26--  http://www.kernel.org/pub/linux/kernel/v2.6/..
Resolving www.kernel.org... 130.239.17.5, 149.20.4.69, 149.20.20.133, ...
Connecting to www.kernel.org&#124;130.239.17.5&#124;:80... connected.
HTTP request sent, awaiting response... 200 OK
Length: 76096559 (73M) [application/x-bzip2]
Saving to: `linux-2.6.39.tar.bz2'

88% [================================&gt;        ] 67,520,536  1.85M/s  eta 7s 
```

你瞧瞧～很可爱吧！不必透过浏览器，只要知道网址后，立即可以进行档案的下载， 又快速又方便，还可以透过 proxy 的帮助来下载呢！透过修改 /etc/wgetrc 来设定你的代理服务器：

```
[root@www ~]# vim /etc/wgetrc
#http_proxy = http://proxy.yoyodyne.com:18023/  &lt;==找到底下这几行，大约在 78 行
#ftp_proxy = http://proxy.yoyodyne.com:18023/
#use_proxy = on

# 将他改成类似底下的模样，记得，你必须要有可接受的 proxy 主机才行！
http_proxy = http://proxy.ksu.edu.tw:3128/
use_proxy = on 
```

* * *

# 5.5 封包撷取功能

## 5.5 封包撷取功能

很多时候由于我们的网络联机出现问题，使用类似 ping 的软件功能却又无法找出问题点，最常见的是因为路由与 IP 转递后所产生的一些困扰 (请参考防火墙与 NAT 主机部分)，这个时候要怎么办？最简单的方法就是『分析封包的流向』啰！透过分析封包的流向，我们可以了解一条联机应该是如何进行双向的联机的动作， 也就会清楚的了解到可能发生的问题所在了！底下我们就来谈一谈这个 tcpdump 与图形接口的封包分析软件吧！

* * *

### 5.5.1 文字接口封包撷取器： tcpdump

说实在的，对于 tcpdump 这个软件来说，你甚至可以说这个软件其实就是个黑客软件， 因为他不但可以分析封包的流向，连封包的内容也可以进行『监听』， 如果你使用的传输数据是明码的话，不得了，在 router 或 hub 上面就可能被人家监听走了！ 我们在[第二章谈到的 CSMA/CD](http://linux.vbird.org/linux_server/0110network_basic.php#tcpip_link_csmacd) 流程中，不是说过有所谓的『监听软件』吗？这个 tcpdump 就是啦！ 很可怕吶！所以，我们也要来了解一下这个软件啊！(注：这个 tcpdump 必须使用 root 的身份执行)

```
[root@www ~]# tcpdump [-AennqX] [-i 接口] [-w 储存档名] [-c 次数] \
                      [-r 档案] [所欲撷取的封包数据格式]
选项与参数：
-A ：封包的内容以 ASCII 显示，通常用来捉取 WWW 的网页封包资料。
-e ：使用资料连接层 (OSI 第二层) 的 MAC 封包数据来显示；
-nn：直接以 IP 及 port number 显示，而非主机名与服务名称
-q ：仅列出较为简短的封包信息，每一行的内容比较精简
-X ：可以列出十六进制 (hex) 以及 ASCII 的封包内容，对于监听封包内容很有用
-i ：后面接要『监听』的网络接口，例如 eth0, lo, ppp0 等等的界面；
-w ：如果你要将监听所得的封包数据储存下来，用这个参数就对了！后面接档名
-r ：从后面接的档案将封包数据读出来。那个『档案』是已经存在的档案，
     并且这个『档案』是由 -w 所制作出来的。
-c ：监听的封包数，如果没有这个参数， tcpdump 会持续不断的监听，
     直到使用者输入 [ctrl]-c 为止。
所欲撷取的封包数据格式：我们可以专门针对某些通讯协议或者是 IP 来源进行封包撷取，
     那就可以简化输出的结果，并取得最有用的信息。常见的表示方法有：
     'host foo', 'host 127.0.0.1' ：针对单部主机来进行封包撷取
     'net 192.168' ：针对某个网域来进行封包的撷取；
     'src host 127.0.0.1' 'dst net 192.168'：同时加上来源(src)或目标(dst)限制
     'tcp port 21'：还可以针对通讯协议侦测，如 tcp, udp, arp, ether 等
     还可以利用 and 与 or 来进行封包数据的整合显示呢！

# 范例一：以 IP 与 port number 捉下 eth0 这个网络卡上的封包，持续 3 秒
[root@www ~]# tcpdump -i eth0 -nn
tcpdump: verbose output suppressed, use -v or -vv for full protocol decode
listening on eth0, link-type EN10MB (Ethernet), capture size 65535 bytes
17:01:47.360523 IP 192.168.1.101.1937 &gt; 192.168.1.100.22: Flags [.], ack 196, win 65219, 
&lt;u&gt;17:01:47.362139 IP 192.168.1.100.22 &gt; 192.168.1.101.1937: Flags [P.], seq 196:472, ack 1,&lt;/u&gt;
17:01:47.363201 IP 192.168.1.100.22 &gt; 192.168.1.101.1937: Flags [P.], seq 472:636, ack 1,
17:01:47.363328 IP 192.168.1.101.1937 &gt; 192.168.1.100.22: Flags [.], ack 636, win 64779,
&lt;==按下 [ctrl]-c 之后结束
6680 packets captured              &lt;==捉下来的封包数量
14250 packets received by filter   &lt;==由过滤所得的总封包数量
7512 packets dropped by kernel     &lt;==被核心所丢弃的封包 
```

如果你是第一次看 tcpdump 的 man page 时，肯定一个头两个大，因为 tcpdump 几乎都是分析封包的表头数据，用户如果没有简易的网络封包基础，要看懂粉难吶！ 所以，至少你得要回到[网络基础](http://linux.vbird.org/linux_server/0110network_basic.php)里面去将 TCP 封包的表头资料理解理解才好啊！ ^_^！至于那个范例一所产生的输出范例中，我们可以约略区分为数个字段， 我们以范例一当中那个特殊字体行来说明一下：

*   17:01:47.362139：这个是此封包被撷取的时间，『时:分:秒』的单位；
*   IP：透过的通讯协议是 IP ；
*   192.168.1.100.22 > ：传送端是 192.168.1.100 这个 IP，而传送的 port number 为 22，你必须要了解的是，那个大于 (>) 的符号指的是封包的传输方向喔！
*   192.168.1.101.1937：接收端的 IP 是 192.168.1.101， 且该主机开启 port 1937 来接收；
*   [P.], seq 196:472：这个封包带有 PUSH 的数据传输标志， 且传输的数据为整体数据的 196~472 byte；
*   ack 1：ACK 的相关资料。

最简单的说法，就是该封包是由 192.168.1.100 传到 192.168.1.101，透过的 port 是由 22 到 1937 ， 使用的是 PUSH 的旗标，而不是 SYN 之类的主动联机标志。呵呵！不容易看的懂吧！所以说，上头才讲请务必到 [TCP 表头数据](http://linux.vbird.org/linux_server/0110network_basic.php#tcpip_transfer_tcp)的部分去瞧一瞧的啊！

再来，一个网络状态很忙的主机上面，你想要取得某部主机对你联机的封包数据而已时， 使用 tcpdump 配合管线命令与正规表示法也可以，不过，毕竟不好捉取！ 我们可以透过 tcpdump 的表示法功能，就能够轻易的将所需要的数据独立的取出来。 在上面的范例一当中，我们仅针对 eth0 做监听，所以整个 eth0 接口上面的数据都会被显示到屏幕上， 不好分析啊！那么我们可以简化吗？例如只取出 port 21 的联机封包，可以这样做：

```
[root@www ~]# tcpdump -i eth0 -nn port 21
tcpdump: verbose output suppressed, use -v or -vv for full protocol decode
listening on eth0, link-type EN10MB (Ethernet), capture size 96 bytes
01:54:37.96 IP 192.168.1.101.1240 &gt; 192.168.1.100.21: . ack 1 win 65535
01:54:37.96 IP 192.168.1.100.21 &gt; 192.168.1.101.1240: P 1:21(20) ack 1 win 5840
01:54:38.12 IP 192.168.1.101.1240 &gt; 192.168.1.100.21: . ack 21 win 65515
01:54:42.79 IP 192.168.1.101.1240 &gt; 192.168.1.100.21: P 1:17(16) ack 21 win 65515
01:54:42.79 IP 192.168.1.100.21 &gt; 192.168.1.101.1240: . ack 17 win 5840
01:54:42.79 IP 192.168.1.100.21 &gt; 192.168.1.101.1240: P 21:55(34) ack 17 win 5840 
```

瞧！这样就仅提出 port 21 的信息而已，且仔细看的话，你会发现封包的传递都是双向的， client 端发出『要求』而 server 端则予以『响应』，所以，当然是有去有回啊！ 而我们也就可以经过这个封包的流向来了解到封包运作的过程。举例来说：

1.  我们先在一个终端机窗口输入『 tcpdump -i lo -nn 』 的监听，
2.  再另开一个终端机窗口来对本机 (127.0.0.1) 登入『ssh localhost』

那么输出的结果会是如何？

```
[root@www ~]# tcpdump -i lo -nn
 1 tcpdump: verbose output suppressed, use -v or -vv for full protocol decode
 2 listening on lo, link-type EN10MB (Ethernet), capture size 96 bytes
 3 11:02:54.253777 IP 127.0.0.1.32936 &gt; 127.0.0.1.22: S 933696132:933696132(0) 
   win 32767 &lt;mss 16396,sackOK,timestamp 236681316 0,nop,wscale 2&gt;
 4 11:02:54.253831 IP 127.0.0.1.22 &gt; 127.0.0.1.32936: S 920046702:920046702(0) 
   ack 933696133 win 32767 &lt;mss 16396,sackOK,timestamp 236681316 236681316,nop,
   wscale 2&gt;
 5 11:02:54.253871 IP 127.0.0.1.32936 &gt; 127.0.0.1.22: . ack 1 win 8192 &lt;nop,
   nop,timestamp 236681316 236681316&gt;
 6 11:02:54.272124 IP 127.0.0.1.22 &gt; 127.0.0.1.32936: P 1:23(22) ack 1 win 8192 
   &lt;nop,nop,timestamp 236681334 236681316&gt;
 7 11:02:54.272375 IP 127.0.0.1.32936 &gt; 127.0.0.1.22: . ack 23 win 8192 &lt;nop,
   nop,timestamp 236681334 236681334&gt; 
```

上表显示的头两行是 tcpdump 的基本说明，然后：

*   第 3 行显示的是『来自 client 端，带有 SYN 主动联机的封包』，
*   第 4 行显示的是『来自 server 端，除了响应 client 端之外(ACK)，还带有 SYN 主动联机的标志；
*   第 5 行则显示 client 端响应 server 确定联机建立 (ACK)
*   第 6 行以后则开始进入数据传输的步骤。

从第 3-5 行的流程来看，熟不熟悉啊？没错！那就是[三向交握](http://linux.vbird.org/linux_server/0110network_basic.php#tcpip_transfer_tcphand)的基础流程啦！够有趣吧！ 不过 tcpdump 之所以被称为黑客软件之一可不止上头介绍的功能吶！ 上面介绍的功能可以用来作为我们主机的封包联机与传输的流程分析， 这将有助于我们了解到封包的运作，同时了解到主机的防火墙设定规则是否有需要修订的地方。

更神奇的使用要来啦！如果我们使用 tcpdump 在 router 上面监听『明码』的传输数据时， 例如 FTP 传输协议，你觉得会发生什么问题呢？ 我们先在主机端下达『 tcpdump -i lo port 21 -nn -X 』然后再以 ftp 登入本机，并输入账号与密码， 结果你就可以发现如下的状况：

```
[root@www ~]# tcpdump -i lo -nn -X 'port 21'
    0x0000:  4500 0048 2a28 4000 4006 1286 7f00 0001  E..H*(@.@.......
    0x0010:  7f00 0001 0015 80ab 8355 2149 835c d825  .........U!I.\.%
    0x0020:  8018 2000 fe3c 0000 0101 080a 0e2e 0b67  .....&lt;.........g
    0x0030:  0e2e 0b61 3232 3020 2876 7346 5450 6420  ...a220.(vsFTPd.
    0x0040:  322e 302e 3129 0d0a                      2.0.1)..

    0x0000:  4510 0041 d34b 4000 4006 6959 7f00 0001  E..A.K@.@.iY....
    0x0010:  7f00 0001 80ab 0015 835c d825 8355 215d  .........\.%.U!]
    0x0020:  8018 2000 fe35 0000 0101 080a 0e2e 1b37  .....5.........7
    0x0030:  0e2e 0b67 5553 4552 2064 6d74 7361 690d  ...gUSER.dmtsai.
    0x0040:  0a                                       .

    0x0000:  4510 004a d34f 4000 4006 694c 7f00 0001  E..J.O@.@.iL....
    0x0010:  7f00 0001 80ab 0015 835c d832 8355 217f  .........\.2.U!.
    0x0020:  8018 2000 fe3e 0000 0101 080a 0e2e 3227  .....&gt;........2'
    0x0030:  0e2e 1b38 5041 5353 206d 7970 6173 7377  ...8PASS.mypassw
    0x0040:  6f72 6469 7379 6f75 0d0a                 ordisyou.. 
```

上面的输出结果已经被简化过了，你必须要自行在你的输出结果当中搜寻相关的字符串才行。 从上面输出结果的特殊字体中，我们可以发现『该 FTP 软件使用的是 vsftpd ，并且使用者输入 dmtsai 这个账号名称，且密码是 mypasswordisyou』 嘿嘿！你说可不可怕啊！如果使用的是明码的方式来传输你的网络数据？ 所以我们才常常在讲啊，网络是很不安全滴！

另外你得了解，为了让网络接口可以让 tcpdump 监听，所以执行 tcpdump 时网络接口会启动在 『错乱模式 (promiscuous)』，所以你会在 /var/log/messages 里面看到很多的警告讯息， 通知你说你的网络卡被设定成为错乱模式！别担心，那是正常的。至于更多的应用，请参考 man tcpdump 啰！

例题：如何使用 tcpdump 监听 (1)来自 eth0 适配卡且 (2)通讯协议为 port 22 ，(3)封包来源为 192.168.1.101 的封包资料？答：tcpdump -i eth0 -nn 'port 22 and src host 192.168.1.101'

* * *

### 5.5.2 图形接口封包撷取器： wireshark

tcpdump 是文字接口的封包撷取器，那么有没有图形接口的？有啊！那就是 wireshark (注 1) 这套软件。这套软件早期称为 ethereal ，目前同时提供文字接口的 tethereal 以及图形接口的 wireshark 两个咚咚。由于我们当初安装时预设并没有装这套，因此妳必须要先使用 yum 去网络安装喔！也可以拿出光盘来安装啦！有两套需要安装，分别是文字接口的 wireshark 以及图形接口的 wireshark-gnome 软件。安装方式如下：

```
[root@www ~]# yum install wireshark wireshark-gnome 
```

启动这套软件的方法很简单，你必须要在 X Window 底下，透过『应用程序』-->『因特网』-->『wireshark network analyzer』就可以启动啦！启动的画面如下所示：

![](img/centos6-wireshark-1.gif) 图 5.5-1、wireshark 的使用示意图

其实这一套软件功能非常强大！鸟哥这里仅讲简单的用法，若有特殊需求，就得要自己找找数据啰。 想要开始撷取封包前，得要设定一下监听的接口之类的，因此点选图 5.5-1 画面中的网络卡小图标吧！ 就会出现如下的画面给你选择了。

![](img/centos6-wireshark-2.gif) 图 5.5-2、wireshark 的使用示意图

在上图中，你得先选择想要监听的接口，鸟哥这里因为担心外部的封包太多导致画面很乱，因此这里使用内部的 lo 接口来作为范例。你得要注意， lo 平时是很安静的！所以，鸟哥在点选了『start』之后，还有打开终端机， 之后使用『 ssh localhost 』来尝试登入自己，这样才能够获得封包喔！如下图所示：

![](img/centos6-wireshark-3.gif) 图 5.5-3、wireshark 的使用示意图

若没有问题，等到你撷取了足够的封包想要进行分析之后，按下图 5.5-3 画面中的停止小图示，那么封包撷取的动作就会终止， 接下来，就让我们来开始分析一下封包吧！

![](img/centos6-wireshark-4.gif) 图 5.5-4、wireshark 的使用示意图

整个分析的画面如上所示，画面总共分为三大区块，你可以将鼠标光标移动到每个区块中间的移动棒， 就可以调整每个区块的范围大小了。第一区块主要显示的是封包的标头资料，内容就有点类似 tcpdump 的显示结果，第二区块则是详细的表头资料，包括讯框的内容、通讯协议的内容以及 socket pair 等等信息。 第三区块则是 16 进位与 ASCII 码的显示结果 (详细的封包内容)。

如果你觉得某个封包有问题，在画面 1 的地方点选该封包 (图例中是第 6 个封包)，那么画面 2 与 3 就会跟着变动！由于鸟哥测试的封包是加密数据的封包，因此画面 2 显示出封包表头，但画面 3 的封包内容就是乱码啦！ 透过这个 wireshark 你就可以一口气得到所需要的所有封包内容啦！而且还是图形接口的，很方便吧！

* * *

### 5.5.3 任意启动 TCP/UDP 封包的埠口联机： nc, netcat

这个 nc 指令可以用来作为某些服务的检测，因为他可以连接到某个 port 来进行沟通，此外，还可以自行启动一个 port 来倾听其他用户的联机吶！非常的不错用！如果在编译 nc 软件的时候给予『GAPING_SECURITY_HOLE』参数的话，嘿嘿！ 这个软件还可以用来取得客户端的 bash 哩！可怕吧！我们的 CentOS 预设并没有给予上面的参数， 所以我们不能够用来作为黑客软件～但是 nc 用来取代 telnet 也是个很棒的功能了！(有的系统将执行文件 nc 改名为 netcat 啦！)

```
[root@www ~]# nc [-u] [IP&#124;host] [port]
[root@www ~]# nc -l [IP&#124;host] [port]
选项与参数：
-l ：作为监听之用，亦即开启一个 port 来监听用户的联机；
-u ：不使用 TCP 而是使用 UDP 作为联机的封包状态

# 范例一：与 telnet 类似，连接本地端的 port 25 查阅相关讯息
[root@www ~]# yum install nc
[root@www ~]# nc localhost 25 
```

这个最简单的功能与 telnet 几乎一样吧！可以去检查某个服务啦！不过，更神奇的在后面， 我们可以建立两个联机来传讯喔！举个例子来说，我们先在服务器端启动一个 port 来进行倾听：

```
# 范例二：激活一个 port 20000 来监听使用者的联机要求
[root@www ~]# nc -l localhost 20000 &
[root@www ~]# netstat -tlunp &#124; grep nc
tcp        0      0 ::1:20000        :::*     LISTEN      5433/nc
# 启动一个 port 20000  在本机上！ 
```

接下来你再开另外一个终端机来看看，也利用 nc 来联机服务器，并且输入一些指令看看喔！

```
[root@www ~]# nc localhost 20000
   &lt;==这里可以开始输入字符串了！ 
```

此时，在客户端我们可以打入一些字，你会发现在服务器端会同时出现你输入的字眼吶！ 如果你同时给予一些额外的参数，例如利用标准输入与输出 (stdout, stdin) 的话，那么就可以透过这个联机来作很多事情了！ 当然 nc 的功能不只如此，你还可以发现很多的用途喔！ 请自行到你主机内的 /usr/share/doc/nc-1.84/scripts/ 目录下看看这些 script ，有帮助的吶！ 不过，如果你需要额外的编译出含有 GAPING_SECURITY_HOLE 功能， 以使两端联机可以进行额外指令的执行时，就得要自行下载原始码来编译了！

* * *

# 5.6 重点回顾

## 5.6 重点回顾

*   修改网络接口的硬件相关参数，可以使用 ifconfig 这个指令，包括 MTU 等等；
*   ifup 与 ifdown 其实只是 script ，在使用时，会主动去 /etc/sysconfig/network-scripts 下找到相对应的装置配置文件，才能够正确的启动与关闭；
*   路由的修改与查阅可以使用 route 来查询，此外， route 亦可进行新增、删除路由的工作；
*   ip 指令可以用来作为整个网络环境的设定，利用 ip link 可以修改『网络装置的硬件相关功能』， 包括 MTU 与 MAC 等等，可以使用 ip address 修改 TCP/IP 方面的参数，包括 IP 以及网域参数等等， ip route 则可以修改路由！
*   ping 主要是透过 ICMP 封包来进行网络环境的检测工作，并且可以使用 ping 来查询整体网域可接受最大的 MTU 值；
*   侦察每个节点的联机状况，可以使用 traceroute 这个指令来追踪！
*   netstat 除了可以观察本机的启动接口外，还可以观察 Unix socket 的传统插槽接口数据；
*   host 与 nslookup 预设都是透过 /etc/resolv.conf 内设定的 DNS 主机来进行主机名与 IP 的查询；
*   lftp 可以用来匿名登录远程的 FTP 主机；
*   links 主要的功能是『浏览』，包括本机上 HTML 语法的档案， wget 则主要在用来下载 WWW 的资料；
*   撷取封包以分析封包的流向，可使用 tcpdump ，至于图形接口的 wireshark 则可以进行更为详细的解析。
*   透过 tcpdump 分析三向交握，以及分析明码传输的数据，可发现网络加密的重要性。
*   nc 可用来取代 telnet 进行某些服务埠口的检测工作。

* * *

# 5.7 本章习题

## 5.7 本章习题

*   暂时将你的 eth0 这张网络卡的 IP 设定为 192.168.1.100 ，如何进行？ifconfig eth0 192.168.1.100
*   我要增加一个路由规则，以 eth0 连接 192.168.100.100/24 这个网域，应该如何下达指令？route add -net 192.l68.100.0 netmask 255.255.255.0 dev eth0
*   我的网络停顿的很厉害，尤其是连接到 tw.yahoo.com 的时候，那么我应该如何检查那个环节出了问题？traceroute tw.yahoo.com
*   我发现我的 Linux 主机上面有个联机很怪异，想要将他断线，应该如何进行？以 root 的身份进行『netstat -anp |more』查出该联机的 PID，然后以『 kill -9 PID 』踢掉该联机。
*   你如何知道 green.ev.ncku.edu.tw 这部主机的 IP ？方法很多，可以利用 host green.ev.ncku.edu.tw 或 dig green.ev.ncku.edu.tw 或 nslookup green.ev.ncku.edu.tw 等方法找出
*   请找出你的机器上面最适当的 MTU 应该是多少？请利用『ping -c 3 -M do -s MTU yourIP 』找出你的 IP 的 MTU 数值。 事实上，你还可以先以 ip 设定网络卡较大的 MTU 后，在进行上述的动作，才能够找出网域内适合的 MTU。
*   如何在终端机接口上面进行 WWW 浏览？又该如何下载 WWW 上面提供的档案？要浏览可以使用 links 或 lynx ，至于要下载则使用 wget 这个软件。
*   在终端机接口中，如何连接 bbs.sayya.org 这个 BBS ？利用 telnet bbs.sayya.org 即可连接上
*   请自行以 tcpdump 观察本机端的 ssh 联机时，三向交握的内容
*   请自行回答：为何使用明码传输的网络联机数据较为危险？并自行以软件将封包取出，并与同学讨论封包的信息
*   请自行至 Internet 下载 nc(netcat) 的原始码，并且编译成为具有 GAPING_SECURITY_HOLE 的参数， 然后建立一条联机使用 -e /bin/bash 尝试将本地端的 bash 丢给目的端执行 (特殊功能，可让 client 取得来自主机的 bash)。

* * *

# 5.8 参考数据与延伸阅读

## 5.8 参考数据与延伸阅读

*   注 1：wireshark 的官网网址：[`www.wireshark.org/`](http://www.wireshark.org/)

* * *

2002/07/31：第一次完成日期！ 2003/08/19：重新编排版面，加入 jmcce 的安装以及 MTU 的相关说明 2003/08/20：加入课后练习去 2003/09/19：加入参考用解答咯！ 2005/03/24：route 的指令参数写错了！已经订正！ 2006/07/24：将旧的文章移动到 [此处](http://linux.vbird.org/linux_server/0140networkcommand/0140networkcommand.php) 2006/07/24：拿掉相关性不高的 [JMCCE 中文终端机](http://linux.vbird.org/linux_server/0140networkcommand/0140networkcommand.php#jmcce)； 将 [Windows 系统的 MTU 检测修改方法移除](http://linux.vbird.org/linux_server/0140networkcommand/0140networkcommand.php#MTU)。 也拿掉 [ncftp](http://linux.vbird.org/linux_server/0140networkcommand/0140networkcommand.php#ncftp) 的说明 2006/08/02：修改了很多部分，加入一些封包侦测的功能程序，tcpdump, nc 等指令！ 2010/08/28：将旧的，基于 CentOS 4.x 所撰写的文章放置于[此处](http://linux.vbird.org/linux_server/0140networkcommand/0140networkcommand-centos4.php) 2010/09/03：加入 links 取消 lynx，ethereal 改成 wireshark，gaim 改成 pidgin 了，nc 指令的用法跟前几版有点不同。 2011/07/18：将基于 CentOS 5.x 的文章移动到[此处](http://linux.vbird.org/linux_server/0140networkcommand//0140networkcommand-centos5.php) 2011/07/18：将资料修订为 CentOS 6.x 的模样！不过 tcpdump 的变化不大，部分数据为 CentOS 5.x 的撷取示意！

* * *