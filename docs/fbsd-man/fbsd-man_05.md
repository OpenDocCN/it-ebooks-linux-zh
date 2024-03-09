# 部分 IV. 网络通讯

FreeBSD 是目前以高性能网络服务为目的而部署范围最广的操作系统之一。 讨论这些话题的章节包括：

*   串口通讯

*   PPP 和以太网上的 PPP

*   电子邮件

*   运行网络服务

*   防火墙

*   其他进阶网络话题

这些章节主要供您在需要时参考。 不必按特定的顺序来阅读它们， 此外， 您开始在网络中使用 FreeBSD 之前也不需要先把它们都读完。

## 第 27 章 串口通讯

## 27.1. 概述

UNIX® 一直都是支持串口通讯的。事实上， 早期的 UNIX® 系统就是利用串口线来输入和输出数据的。 那时常见的 “终端” 包括一个每秒 10 个字符的串口打印机和键盘， 现在这些已经发生了很大的变化。 这章将介绍一些利用 FreeBSD 进行串口通讯的方法。

读完这章，您将了解到：

*   如何通过终端连接到您的 FreeBSD 系统。

*   如何使用 modem 拨号到远程主机。

*   如何允许远程用户通过 modem 登录到您的系统。

*   如何从串口控制台引导您的系统。

阅读这章之前，您应当了解：

*   如何配置和安装一个新的内核 (第 9 章 *配置 FreeBSD 的内核*)。

*   理解 UNIX® 的权限和进程 (第 4 章 *UNIX 基础*)。

*   准备您打算在 FreeBSD 中使用的串口设备 (modem 或多插口卡)的技术参考手册。

## 27.2. 介绍

### 警告:

从 FreeBSD 8.0 开始， 用于串口的设备节点从 `/dev/cuadN` 改为了 `/dev/cuauN`； 从 `/dev/ttydN` 改为了 `/dev/ttyuN`。 FreeBSD 7.X 用户需要根据实际情况对这份文档中的例子进行必要的调整。

### 27.2.1. 术语

bps

每秒位── 数据的传输速度

DTE

数据终端设备 ── 如您的计算机

DCE

数据通讯设备 ── 如您的 modem

RS-232

用于硬件串口通讯的 EIA 标准

当讨论通讯数据速度的时候，这节不会使用术语 “baud”。Baud 指电气标准传输率，它已经使用了很长时间， 而 “bps” (bits per second) 才是正确使用的术语 (至少它不会打扰那些爱争吵的家伙)。

### 27.2.2. 线缆和端口

要将 modem 或终端与您的 FreeBSD 系统相连， 您的计算机需要一个串口， 以及用于连接串口设备所需的线缆。 如果您比较熟悉硬件及所需要的电缆， 则可以跳过这节。

#### 27.2.2.1. 线缆

串口线缆有许多不同的种类。 最常见的两种类型是 null-modem 线缆和标准 (“直联”) RS-232 线缆。 您的硬件说明书中会介绍应使用的线缆种类。

##### 27.2.2.1.1. Null-modem 线缆

null-modem 电缆会直接传送某些信号， 如 “Signal Ground” (信号地)， 但对其他信号进行交换。 例如， “Transmitted Data” (数据发送) 引脚是连到另一端 “Received Data” (数据接收) 引脚的。

也可以自行制作 null-modem 电缆给终端使用 (例如， 为了品质的要求)。 下面的表格展示了 RS-232C 信号， 以及 DB-25 连接器上的引脚。 注意， 标准也要求一根直通引脚 1 到引脚 1 的 *保护地 (Protective Ground)* 线， 但这通常都被省掉。 某些终端在只有引脚 2、 3 和 7 的时候， 就已经能够正常使用了， 而其他一些， 则需要下面例子中所展示的不同的配置。

表 27.1. DB-25 to DB-25 Null-Modem Cable

| 信号 | 引脚 # |   | 引脚 # | 信号 |
| --- | --- | --- | --- | --- |
| SG | 7 | 连接到 | 7 | SG |
| TD | 2 | 连接到 | 3 | RD |
| RD | 3 | 连接到 | 2 | TD |
| RTS | 4 | 连接到 | 5 | CTS |
| CTS | 5 | 连接到 | 4 | RTS |
| DTR | 20 | 连接到 | 6 | DSR |
| DTR | 20 | 连接到 | 8 | DCD |
| DSR | 6 | 连接到 | 20 | DTR |
| DCD | 8 | 连接到 | 20 | DTR |

这里还有两种目前比较流行的其他接线方式。

表 27.2. DB-9 到 DB-9 Null-Modem 电缆

| 信号 | 引脚 # |   | 引脚 # | 信号 |
| --- | --- | --- | --- | --- |
| RD | 2 | 接到 | 3 | TD |
| TD | 3 | 接到 | 2 | RD |
| DTR | 4 | 接到 | 6 | DSR |
| DTR | 4 | 接到 | 1 | DCD |
| SG | 5 | 接到 | 5 | SG |
| DSR | 6 | 接到 | 4 | DTR |
| DCD | 1 | 接到 | 4 | DTR |
| RTS | 7 | 接到 | 8 | CTS |
| CTS | 8 | 接到 | 7 | RTS |

表 27.3. DB-9 到 DB-25 Null-Modem 电缆

| 信号 | 引脚 # |   | 引脚 # | 信号 |
| --- | --- | --- | --- | --- |
| RD | 2 | DB-9 到 DB-25 Null-Modem 电缆 | 2 | TD |
| TD | 3 | 接到 | 3 | RD |
| DTR | 4 | 接到 | 6 | DSR |
| DTR | 4 | 接到 | 8 | DCD |
| SG | 5 | 接到 | 7 | SG |
| DSR | 6 | 接到 | 20 | DTR |
| DCD | 1 | 接到 | 20 | DTR |
| RTS | 7 | 接到 | 5 | CTS |
| CTS | 8 | 接到 | 4 | RTS |

### 注意:

当某一段连接器上的一个引脚需要连接到对端的一对引脚时， 通常是将那一对引脚使用一短线连接， 而使用长线接到另一端的那个引脚。

上面的设计似乎更为流行。 在其他变种中 (在 *RS-232 Made Easy* 这本书中进行了详细介绍) 则是 SG 接 SG， TD 接 RD、 RTS 和 CTS 接 DCD、 DTR 接 DSR， 反之亦然。

##### 27.2.2.1.2. 标准 RS-232C 线缆

标准的串口电缆会直接传送所有 RS-232C 信号。 也就是说， 一头的 “Transmitted Data” 引脚， 会直接接到另一头的 “Transmitted Data” 引脚。 这包括将调制解调器接到您的 FreeBSD 系统上的那种电缆， 同样也适用于某些型号的终端。

#### 27.2.2.2. 端口

串口是 FreeBSD 主机与终端传输数据的设备。 这节描述了端口的种类和它们在 FreeBSD 上是如何编址的。

##### 27.2.2.2.1. 端口的种类

有好几种串口。 在采购或制作线缆之前， 您应确认它能够适合您的终端以及 FreeBSD 系统。

绝大多数终端都提供 DB-25 端口。 个人计算机， 也包括运行 FreeBSD 的 PC 机， 通常会有 DB-25 或 DB-9 口。 如果您的 PC 上有多插口串口卡， 则可能有 RJ-12 或 RJ-45 口。

请参见您硬件的文档以了解所用接口的规格。 此外， 您也可以通过观察外观来了解所用的端口。

##### 27.2.2.2.2. 端口名称 Port Names

在 FreeBSD 中，您可以通过 `/dev` 目录中的一个记录来访问每个串口。有两种不同的记录：

*   呼入端口的名字是 `/dev/ttyuN`， 其中 *`N`* 是端口的编号， 从零开始计数。 一般来说， 您使用呼入端口作为终端。 呼入端口要求数据线使用载波检测 (DCD) 信号来工作。

*   呼出端口的名字是 `/dev/cuauN`。 通常并不使用呼出端口作为终端， 而只用于调制解调器。 如果串口线或终端不支持载波检测信号， 则可能必须要使用呼出端口。

如果您已经连接一个终端到第一个串口 (在 MS-DOS® 上是`COM1`)， 则可以使用 `/dev/ttyu0` 来作为终端。 如果它是在第二个串口 (`COM2`)， 那就是 `/dev/ttyu1`，等等。

### 27.2.3. 内核配置

FreeBSD 默认支持 4 个串口。 在 MS-DOS®下，这些是 `COM1`， `COM2`， `COM3`， 和 `COM4`。 FreeBSD 目前支持 “dumb” 多口串口卡，如 BocaBoard 1008 和 2016， 以及许多 Digiboard 和 Stallion Technologies 制造的智能多接口卡。 不过， 默认的内核只会寻找标准的 COM 端口。

要看看您的内核是否支持您的串口，只要在内核启动时查看一下启动信息， 或使用 `/sbin/dmesg` 命令重新检测内核启动信息。 特别的，寻找以`sio`字符启动的信息。

### 提示:

如果想只察看包含 `sio` 一词的消息， 可以使用下面的命令：

```
# `/sbin/dmesg | grep 'sio'`
```

例如，在一个带有 4 个串口的系统上，这些是串口特定的内核启动信息：

```
sio0 at 0x3f8-0x3ff irq 4 on isa
sio0: type 16550A
sio1 at 0x2f8-0x2ff irq 3 on isa
sio1: type 16550A
sio2 at 0x3e8-0x3ef irq 5 on isa
sio2: type 16550A
sio3 at 0x2e8-0x2ef irq 9 on isa
sio3: type 16550A
```

如果内核未能认出所有的串口， 可能需要通过修改 `/boot/device.hints` 文件来进行一些配置。 此外， 也可以注释或完全删除掉您没有的设备。

请参见 [sio(4)](http://www.FreeBSD.org/cgi/man.cgi?query=sio&sektion=4&manpath=freebsd-release-ports) 联机手册来了解关于串口， 以及多插口卡配置的进一步细节。 如果您正使用一个在不同版本的 FreeBSD 上的文件请务必小心， 因为设备参数和语法发生了变化。

### 注意:

这里端口 `IO_COM1` 代替了 `0x3f8`，端口 `IO_COM2` 代替了 `0x2f8`，端口 `IO_COM3` 代替了 `0x3e8`，端口 `IO_COM4` 代替了 `0x2e8`，这些都是各自端口相应的端口地址。 中断 4，3，5，9 都是经常用的中断。也要注意有些正常的串口可能 *无法* 在一些 ISA 总线的 PC 上共享中断 (多插口板卡有板载的电子设备，允许在板上所有 16550A 的设备共享一个或两个中断请求)。

### 27.2.4. 设备特殊文件

在内核中， 大多数设备都是通过 “设备特殊文件” 来访问的， 这些文件一般位于 `/dev` 目录中。 `sio` 是通过 `/dev/ttyuN` (呼入) 和 `/dev/cuauN` (呼出) 设备来访问的。 此外， FreeBSD 也提供了初始化设备 （`/dev/ttyuN.init` 和 `/dev/cuauN.init`） 以及锁设备 （`/dev/ttyuN.lock` 和 `/dev/cuauN.lock`）。 初始化设备用于在打开端口时初始化其通讯参数， 例如使用 `RTS/CTS` 信号进行流控制的调制解调器的 `crtscts`。 锁设备则用于在端口上提供一个锁标志， 防止用户或程序改变特定的参数； 请参见 [termios(4)](http://www.FreeBSD.org/cgi/man.cgi?query=termios&sektion=4&manpath=freebsd-release-ports)、 [sio(4)](http://www.FreeBSD.org/cgi/man.cgi?query=sio&sektion=4&manpath=freebsd-release-ports)， 以及 [stty(1)](http://www.FreeBSD.org/cgi/man.cgi?query=stty&sektion=1&manpath=freebsd-release-ports) 的联机手册， 以了解关于终端配置、 锁和初始化设备， 以及配置终端参数的详细信息。

### 27.2.5. 串口配置

`ttyuN` (或 `cuauN`) 设备是您将要打开的应用程序的一般设备。 当进程打开某个设备时， 它将有一个终端 I/O 设置的默认配置。 您可以在命令行看看这些设置：

```
# `stty -a -f /dev/ttyu1`
```

当您修改了这个设备的设置，这个设置会生效，除非设备被关闭。 当它被重新打开时，它将回到默认设置。 要修改默认设置，您可以打开和调整 “初始状态” 设备的设置。例如， 要为`ttyu5` 打开 `CLOCAL` 模式，8 位通讯和默认的 `XON/XOFF` 流控制， 输入：

```
# `stty -f /dev/ttyu5.init clocal cs8 ixon ixoff`
```

串口设备的系统级初始化， 是由 `/etc/rc.d/serial` 来控制的。 这个文件会影响串口设备的默认设置。

为了防止应用程序修改某些设置， 应修改 “lock state”(锁状态) 设备。 例如， 要把 `ttyu5` 的速率锁定为 57600 bps， 输入：

```
# `stty -f /dev/ttyu5.lock 57600`
```

现在，一个打开`ttyu5` 和设法改变端口速度的应用程序将被固定在 57600bit/s。很自然地， 您需要确定初始状态，然后用 root 帐户锁定状态设备的写入功能。

很显然，您应该只让 `root` 用户可以初始化或锁定设备的状态。

## 27.3. 终端

Contributed by Sean Kelly.

### 警告:

从 FreeBSD 8.0 开始， 用于串口的设备节点从 `/dev/cuadN` 改为了 `/dev/cuauN`； 从 `/dev/ttydN` 改为了 `/dev/ttyuN`。 FreeBSD 7.X 用户需要根据实际情况对这份文档中的例子进行必要的调整。

当您在计算机控制台或是在一个连接的网络上时， 终端提供了一个方便和低成本的访问 FreeBSD 系统的方法。 这节描述了如何在 FreeBSD 上使用终端。

### 27.3.1. 终端的用法和类型

早期的 UNIX® 系统没有控制台。 人们通过将终端连接到计算机的串口来登录和使用程序。 它很像用 modem 和一些终端软件来拨号进入一个远程的系统， 只能执行文本的工作。

今天的 PC 已经可以使用高质量的图形了， 但与今天的其他 UNIX®操作系统一样，建立一个登录会话的能力仍然存在。 通过使用一个终端连接到一个没有使用的串口， 您就能登录和运行任何文本程序或在 X 视窗系统中运行一个 `xterm` 窗口程序。

对于商业用户，您可以把任何终端连接到 FreeBSD 系统， 然后把它们放在员工的桌面上。 对于家庭用户，则可以使用一台比较老的 IBM PC 或 Macintosh 运行一个终端连接到一台运行 FreeBSD 的高性能机器上。

对于 FreeBSD，有三种终端：

*   哑终端

*   充当终端的 PC

*   X 终端

下面一小节将描述每一种终端。

#### 27.3.1.1. 哑终端

哑终端需要专门的好几种硬件，让您通过串口线连接到计算机。 它们被叫做 “哑” 是因为它们只能够用来显示， 发送和接收文本。 您不能在它上面运行任何程序。

有好几百种哑终端，包括 Digital Equipment Corporation 的 VT-100 和 Wyse 的 WY-75。只有几种可以在 FreeBSD 上工作。 一些高端的终端可以显示图形，但只有某些软件包可以使用这些高级特性。

哑终端被广泛用于那些不需要图形应用的工作中。

#### 27.3.1.2. 充当终端的 PC

假如 哑终端 的功能仅限于显示、 发送和接收文本的话， 那么显然任何一台闲置的个人计算机， 都完全能够胜任哑终端的工作。 因此您需要的是合适的线缆， 以及一些在这台计算机上运行的 *终端仿真* 软件。

这种配置在家庭中应用十分广泛。 例如， 如果您的爱人正忙于在您的 FreeBSD 系统的控制台上工作时， 您就可以将一台功能稍弱的计算机挂在这个 FreeBSD 系统上来同时完成一些文本界面的工作。

在 FreeBSD 的基本系统中至少有两个能用于进行串口连接的工具： [cu(1)](http://www.FreeBSD.org/cgi/man.cgi?query=cu&sektion=1&manpath=freebsd-release-ports) 和 [tip(1)](http://www.FreeBSD.org/cgi/man.cgi?query=tip&sektion=1&manpath=freebsd-release-ports)。

如果要从运行 FreeBSD 的计算机上通过串口连接到另一系统， 可以使用：

```
# `cu -l 串口设备`
```

此处 “串口设备” 表示您计算机上某个串口对应的设备名。 `/dev/cuauN`。

此处的 “N” 表示串口的编号。

### 注意:

请注意在 FreeBSD 中设备的编号是从零而非一开始的 (这一点与另一些系统， 如基于 MS-DOS® 的系统不同)。 因此， 在基于 MS-DOS® 系统中的 `COM1` 在 FreeBSD 中通常叫做 `/dev/cuau0`。

### 注意:

其他一些人可能喜欢使用另一些来自 Ports 套件的程序。 Ports 中提供了几个与 [cu(1)](http://www.FreeBSD.org/cgi/man.cgi?query=cu&sektion=1&manpath=freebsd-release-ports) 和 [tip(1)](http://www.FreeBSD.org/cgi/man.cgi?query=tip&sektion=1&manpath=freebsd-release-ports) 类似的工具， 例如 [comms/minicom](http://www.freebsd.org/cgi/url.cgi?ports/comms/minicom/pkg-descr)。

#### 27.3.1.3. X 终端

X 终端是最复杂的终端系统。它们通常需要使用以太网来连接。 它们能显示任何 X 应用程序。

我们介绍 X 终端只是为了感兴趣。然而， 这章*不会*涉及 X 终端的安装，配置或使用。

### 27.3.2. 配置

这节描述了您在一个终端上启用一个登录会话时， 需要在 FreeBSD 系统上进行的配置。 假设已经配置好了内核来支持串口， 就可以直接开始连接了。

在 第 13 章 *FreeBSD 引导过程* 中曾经提到， `init` 进程依赖于系统启动时所有的处理控制和初始化。 通过 `init` 来执行的一些任务将先读取 `/etc/ttys`文件， 然后在可用的终端上启用一个 `getty` 进程。 `getty` 进程可用来阅读一个登录名和启动 `login`程序。

然而，要为您的 FreeBSD 系统配置终端，您需要以 `root` 身份执行下面的步骤：

1.  如果它不在那里， 您需要为串口在 `/dev` 目录下添加一行记录到 `/etc/ttys`。

2.  指定 `/usr/libexec/getty` 在端口上运行， 然后从 `/etc/gettytab` 文件指定适当的 *`getty`* 类型。

3.  指定默认的终端类型。

4.  设置端口为 “on”。

5.  确定端口是否为 “secure”。

6.  迫使`init` 重新读取 `/etc/ttys`文件。

作为可选的步骤，您可以通过在 `/etc/gettytab` 中建立一个记录，在第 2 步创建一个定制的 *`getty`* 类型来使用。这章不会介绍如何做。 您可以参考 [gettytab(5)](http://www.FreeBSD.org/cgi/man.cgi?query=gettytab&sektion=5&manpath=freebsd-release-ports) 和 [getty(8)](http://www.FreeBSD.org/cgi/man.cgi?query=getty&sektion=8&manpath=freebsd-release-ports) 的联机手册了解更多信息。

#### 27.3.2.1. 添加一个记录到`/etc/ttys`

`/etc/ttys`文件列出了您 FreeBSD 系统上允许登录的所有端口。 例如， 第一个虚拟控制台 `ttyv0` 在这个文件中有一个记录。 您可以使用这个记录登录进控制台。 这个文件也包含其他虚拟控制台的记录，串口，和伪 ttys 终端。 对于一个硬连线的终端， 只要列出串口的 `/dev` 记录而不需要 `/dev` 部分 (例如， `/dev/ttyv0` 可以被列为 `ttyv0`)。

默认的 FreeBSD 安装包括一个支持最初四个串口 `ttyu0` 到 `ttyu3` 的`/etc/ttys` 文件。 如果您从那些端口中某一个使用终端，您不需要添加另一个记录。

例 27.1. 在 `/etc/ttys` 中增加终端记录

假设我们连接两个终端给系统： 一个 Wyse-50 和一个老的运行 Procomm 终端软件模拟一个 VT-100 终端的 286IBM PC。 在 `/etc/ttys` 文件中的相应的记录是这样的：

```
ttyu1![1](img/1.png)  "/usr/libexec/getty std.38400"![2](img/2.png)  wy50![3](img/3.png)  on![4](img/4.png)  insecure![5](img/5.png)
ttyu5   "/usr/libexec/getty std.19200"  vt100  on  insecure 
```

| ![1](img/#co-ttys-line1col1) | 第一部分指定了终端指定文件的名称， 它可以在 `/dev`中找到。 |
| ![2](img/#co-ttys-line1col2) | 第二部分是在这行执行的命令，通常是 [getty(8)](http://www.FreeBSD.org/cgi/man.cgi?query=getty&sektion=8&manpath=freebsd-release-ports)。 `getty` 初始化然后打开一行，设置速度， 用户名的命令和执行登录程序。`getty` 程序在它的命令行接收一个参数 (可选)， *`getty`* 类型。 一个 *`getty`* 类型会在终端行描述一个特征， 像波特率和奇偶校验。 `getty` 程序从 `/etc/gettytab` 文件读取这些特征。文件`/etc/gettytab` 包含了许多老的和新的终端行记录。 在很多例子中，启动文本 `std` 的记录将用硬连线终端来工作。 这些记录忽略了奇偶性。 这是一个从 110 到 115200 bit/s 的 `std` 记录。 当然，您可以添加您自己的记录到这个文件。 gettytab 的联机手册提供了更多的信息。当在`/etc/ttys`中设置 *`getty`* 类型的时候， 确信在终端上的通讯设置匹配。 在我们的例子中， Wyse-50 不使用奇偶性， 用 38400 bit/s 来连接。286 PC 不使用奇偶性，用 19200bit/s 来连接。 |
| ![3](img/#co-ttys-line1col3) | 第三部分是通常连接到那个 tty 行的终端类型。对于拨号端口， `unknown` 或 `dialup` 通常被用在这个地方。 对于硬连线的终端，终端类型不会改变， 所以您可以从 termcap 数据库文件中放一个真正的终端类型。在我们的例子中， Wyse-50 使用真正的终端类型， 而运行 Procomm 的 286 PC 将被设置成在 VT-100 上的模拟。 |
| ![4](img/#co-ttys-line1col4) | 如果端口被启用，可以指定第四个部分。在第二部分， 把它放在这儿将执行初始化进程来启动程序 `getty`。如果您在这部分拖延， 将没有`getty`，在端口上因此就没有登录。 |
| ![5](img/#co-ttys-line1col5) | 最后部分被用来指定端口是否安全。 标记一个安全的端口意味着您信任它允许用 `root` 帐户从那个端口登录。 不安全的端口不允许 `root`登录。 在一个不安全的端口上， 用户必须用无特权的帐户登录， 然后使用 `su` 或一个相似的机制来获得超级用户的权限。 |

#### 27.3.2.2. 重新读取`/etc/ttys`来强制`init`

对`/etc/ttys`文件做一个必要的修改后，您必须发送一个 SIGHUP 信号给初始化进程来迫使它重新读取配置文件，例如：

```
# `kill -HUP 1`
```

### 注意:

`init` 总是系统运行时的第一个进程，因此它总是 PID 1。

如果能够正确设置，所有的线缆都是适当的，终端将可以启用了， 然后一个 `getty` 进程将在每个终端运行， 您将在您的终端上看到登录命令行。

### 27.3.3. 您的连接可能出现的问题

即使您小心翼翼地注意细节，您仍然可能会在设置终端时出错。 这有一个有关问题和解决办法的列表：

#### 27.3.3.1. 没有登录命令出现：

确定终端被嵌入和打开了。如果把一台个人计算机充当一个终端， 确信终端模拟软件运行在正确的串口上。

确信线缆被稳固地连接在终端和 FreeBSD 计算机上。 确信用了正确的电缆。

确定终端和 FreeBSD 的传输速度和奇偶设置已经一致了。 如果您有一个图像显示终端，确信对比度已经调节好了。 如果它是一个可打印的终端，确信纸张和墨水已经就绪了。

确定一个 `getty` 进程正在运行和服务终端。 例如， 可以用`ps` 命令得到运行 `getty` 程序的列表，键入：

```
# `ps -axww|grep getty`
```

您将看到一个终端的记录。例如，下面的显示表明一个`getty` 正在第二个串口 `ttyu1` 运行， 正在 `/etc/gettytab` 中使用 `std.38400` 的记录：

```
22189  d1  Is+    0:00.03 /usr/libexec/getty std.38400 ttyu1
```

如果没有 `getty` 进程运行， 确信您已经在`/etc/ttys`中启用了端口。 在修改完`/etc/ttys`文件后，记得运行 `kill -HUP 1`。

如果 `getty` 进程确实在运行， 但终端上仍然没有显示出登录提示， 或者虽然显示了单缺不允许您输入， 您的终端或电缆可能不支持硬件握手。 请尝试将 `/etc/ttys` 中的 `std.38400` 改为 `3wire.38400` (注意在改完 `/etc/ttys` 之后要 `kill -HUP 1`)。 `3wire` 记录和 `std` 类似， 但忽略硬件握手。 您可能需要在使用 `3wire` 时减少波特率或启用软件流控制以避免缓冲区溢出。

#### 27.3.3.2. 出现一个 “垃圾” 而不是一个登录命令行

确信终端和 FreeBSD 使用相同的 bit/s 传输率和奇偶校验设置。 检查一下 `getty` 进程确信当前使用正确的 *`getty`* 类型。 如果没有， 编辑`/etc/ttys`然后运行`kill -HUP 1`。

#### 27.3.3.3. 当键入密码时，字符两个两个出现

将终端 (或终端模拟软件) 从 “半双工” 或 “本地回显” 换成 “全双工”。

## 27.4. 拨入服务

Contributed by Guy Helmer.Additions by Sean Kelly.

### 警告:

从 FreeBSD 8.0 开始， 用于串口的设备节点从 `/dev/cuadN` 改为了 `/dev/cuauN`； 从 `/dev/ttydN` 改为了 `/dev/ttyuN`。 FreeBSD 7.X 用户需要根据实际情况对这份文档中的例子进行必要的调整。

为拨入服务配置 FreeBSD 系统与连接到终端是非常相似的，除非您正在使用 modem 来拨号而不是终端。

### 27.4.1. 外置 vs.内置 modem

外置 modem 看起来很容易拨号。 因为，外置 modem 可以通过储存在非易失性的 RAM 中的参数来配置， 它们通常提供指示器来显示重要的 RS-232 信号的状态。 不停闪光的信号灯能给用户留下比较深刻的印象， 而且指示器也可以用来查看 modem 是否正常地工作。

内置 modem 通常缺乏非易失性的 RAM， 所以对它们的配置可能会限制在通过 DIP 开关来设置。 如果您的内置 modem 有指示灯，您也很难看得到。

#### 27.4.1.1. Modem 和线缆

如果您使用一个外置的 modem，那您将需要适当的电缆线。 一个标准的串口线应当足够长以至普通的信号能够连接上：

表 27.4. 信号名称

| 缩写 | 全名 |
| --- | --- |
| RD | 收到数据 (Received Data) |
| TD | 传出数据 (Transmitted Data) |
| DTR | 数据终端就绪 (Data Terminal Ready) |
| DSR | 数据集就绪 (Data Set Ready) |
| DCD | 数据载波检测 (Data Carrier Detect) (RS-232 的收到线路信号检测器) |
| SG | 信号地 (Signal Ground) |
| RTS | 要求发送数据 (Request to Send) |
| CTS | 允许对方发送数据 (Clear to Send) |

FreeBSD 对速度超过 2400 bps 的情形需要通过 RTS 和 CTS 信号来完成流控制， 通过 CD 信号来检测呼叫响应和挂机， 并通过 DTR 信号来在会话结束时对调制解调器进行复位。 某些电缆在连接时没有提供全部需要的信号， 这会给您带来问题， 例如在挂断时登录会话不消失， 这就有可能是电缆的问题。

与其它类 UNIX® 操作系统类似， FreeBSD 使用硬件信号来检测呼叫响应， 以及在挂断时挂断并复位调制解调器。 FreeBSD 避免发送命令给调制解调器， 或监视其状态。 如果您熟悉通过调制解调器来连接基于 PC 的 BBS 系统， 这可能看起来有点难用。

### 27.4.2. 串口的考虑

FreeBSD 支持基于 NS8250， NS16450， NS16550 和 NS16550A 的 EIA RS-232C 通讯接口。 8250 和 16450 设备有单字符缓冲。 16550 设备提供了一个 16 个字符的缓冲， 可以提高更多的系统性能。 因为单字符缓冲设备比 16 个字符的缓冲需要更多的系统资源来工作， 所以基于 16550A 的接口卡可能更好。 如果系统没有活动的串口， 或有较大的负载， 16 字符缓冲的卡对于低错误率的通讯来说更好。

### 27.4.3. 快速预览

对于终端， `init` 会在每个配置串口上为每个拨入连接产生一个 `getty` 进程。 例如， 如果一个 modem 被附带在 `/dev/ttyu0` 中，用命令`ps ax`可以显示下面这些：

```
 4850 ??  I      0:00.09 /usr/libexec/getty V19200 ttyu0
```

当用户拨上 modem， 并使用它进行连接时， CD 线就会被 modem 认出。 内核注意到载波信号已经被检测到， 需要完成 `getty` 端口的打开。 `getty` 发送一个登录：在指定的初始线速度上的命令行。 Getty 会检查合法的字符是否被接收， 在典型的配置中， 如果发现 “垃圾”， `getty` 就会设法调节线速度，直到它接收到合理的字符。

用户在键入他/她的登录名称后， `getty`执行`/usr/bin/login`， 这会要求用户输入密码来完成登录， 然后启动用户的 shell。

### 27.4.4. 配置文件

如果希望允许拨入您的 FreeBSD 系统， 在 `/etc` 目录中有三个系统配置文件需要您关注。 其一是 `/etc/gettytab`， 其中包含用于 `/usr/libexec/getty` 服务的配置信息。 其二是 `/etc/ttys`， 它的作用是告诉 `/sbin/init` 哪些 `tty` 设备上应该运行 `getty`。 最后， 关于端口的初始化命令， 应放到 `/etc/rc.d/serial` 脚本中。

关于在 UNIX® 上配置拨入调制解调器有两种主要的流派。 一种是将本地计算机到调制解调器的 RS-232 接口配置为固定速率。 这样做的好处是， 远程用户总能立即见到系统的登录提示符， 而其缺点则是， 系统并不知道用户真实的数据速率是多少， 因而， 类似 Emacs 这样的程序， 也就无法调整它们绘制屏幕的方式， 以便为慢速连接改善响应时间。

另一种流派将调制解调器的 RS-232 接口速率配置为随远程用户的连接速率变化。 例如， 对 V.32bis (14.4 Kbps) 连接， 调制解调器会让自己的 RS-232 接口以 19.2 Kbps 的速率运行， 而 2400 bps 连接， 则会使调制解调器的 RS-232 接口以 2400 bps 的速率运行。 由于 `getty` 并不能识别具体的调制解调器的连接速率反馈信息， 因此， `getty` 会以初始速度给出一个 `login:` 提示， 并检查用户的响应字符。 如果用户看到乱码， 则他们应知道此时应按下 **Enter** 键， 直到看到可以辨认的提示符为止。 如果数据速率不匹配， 则 `getty` 会将用户输入的任何信息均视为 “乱码”， 并尝试以下一种速率来再次给出 `login:` 提示符。 这一过程可能需要令人作呕地重复下去， 不过一般而言， 用户只要敲一两下键盘就能看到正确的提示符了。 显然， 这种登录过程看起来不如前面所介绍的 “锁定速率” 方法那样简单明了， 但使用低速连接的用户， 却可以在运行全屏幕程序时得到更好的交互响应。

这一节将尽可能公平地介绍关于配置的信息， 但更着力于介绍调制解调器速率随连接速率变化的配置方法。

#### 27.4.4.1. `/etc/gettytab`

`/etc/gettytab`是一个用来配置 `getty` 信息的 termcap 风格的文件。 请看看 gettytab 的联机手册了解完整的文件格式和功能列表。

##### 27.4.4.1.1. 锁定速度的配置

如果您把您的 modem 的数据通讯率锁定在一个特殊的速度上， 您不需要对 `/etc/gettytab` 文件作任何变化。

##### 27.4.4.1.2. 匹配速度的配置

您将需要在 `/etc/gettytab` 中设置一个记录来告诉 `getty` 您希望在 modem 上使用的速度。 如果您的 modem 的速率是 2400 bit/s， 则可以使用现有的 `D2400` 的记录。

```
#
# Fast dialup terminals, 2400/1200/300 rotary (can start either way)
#
D2400|d2400|Fast-Dial-2400:\
        :nx=D1200:tc=2400-baud:
3|D1200|Fast-Dial-1200:\
        :nx=D300:tc=1200-baud:
5|D300|Fast-Dial-300:\
        :nx=D2400:tc=300-baud:
```

如果您有一个更高速度的 modem， 必须在 `/etc/gettytab` 中添加一个记录。 下面是一个让您可以以最高 19.2 Kbit/s 的用在 14.4 Kbit/s 的 modem 上的接口记录：

```
#
# Additions for a V.32bis Modem
#
um|V300|High Speed Modem at 300,8-bit:\
        :nx=V19200:tc=std.300:
un|V1200|High Speed Modem at 1200,8-bit:\
        :nx=V300:tc=std.1200:
uo|V2400|High Speed Modem at 2400,8-bit:\
        :nx=V1200:tc=std.2400:
up|V9600|High Speed Modem at 9600,8-bit:\
        :nx=V2400:tc=std.9600:
uq|V19200|High Speed Modem at 19200,8-bit:\
        :nx=V9600:tc=std.19200:
```

这样做的结果是 8-数据位， 没有奇偶校验的连接。

上面使用 19.2 Kbit/s 的连接速度的例子，也可以使用 9600 bit/s (for V.32)， 2400 bit/s， 1200 bit/s，300 bit/s， 直到 19.2 Kbit/s。 通讯率的调节使用 `nx=` (“next table”) 来实现。 每条线使用一个 `tc=` (“table continuation”) 的记录来加速对于一个特殊传输率的标准设置。

如果您有 28.8 Kbit/s 的 modem，或您想使用它的 14.4Kbit/s 模式， 就需要使用一个更高的超过 19.2 Kbit/s 的通讯速度的 modem。 这是一个启动 57.6 Kbit/s 的 `gettytab` 记录的例子：

```
#
# Additions for a V.32bis or V.34 Modem
# Starting at 57.6 Kbps
#
vm|VH300|Very High Speed Modem at 300,8-bit:\
        :nx=VH57600:tc=std.300:
vn|VH1200|Very High Speed Modem at 1200,8-bit:\
        :nx=VH300:tc=std.1200:
vo|VH2400|Very High Speed Modem at 2400,8-bit:\
        :nx=VH1200:tc=std.2400:
vp|VH9600|Very High Speed Modem at 9600,8-bit:\
        :nx=VH2400:tc=std.9600:
vq|VH57600|Very High Speed Modem at 57600,8-bit:\
        :nx=VH9600:tc=std.57600:
```

如果您的 CPU 速度较低， 或系统的负荷很重， 而且没有 16550A 的串口，您可能会在 57.6 Kbit/s 上得到 sio “silo”错误。

#### 27.4.4.2. `/etc/ttys`

`/etc/ttys`文件的配置在 例 27.1 “在 `/etc/ttys` 中增加终端记录”中介绍过。 配置 modem 是相似的， 但我们必须指定一个不同的终端类型。 锁定速度和匹配速度配置的通用格式是：

```
ttyu0   "/usr/libexec/getty *`xxx`*"   dialup on
```

上面的第一条是这个记录的设备特定文件 ── `ttyu0` 表示 `/dev/ttyu0` 是这个 `getty` 将被监视的文件。 第二条 `"/usr/libexec/getty xxx"` 是将运行在设备上的进程 `init`。 第三条，dialup，是默认的终端类型。 第四个参数， `on`， 指出了线路是可操作的 `init`。 也可能会有第五个参数， `secure`， 但它将只被用作拥有物理安全的终端 (如系统终端)。

默认的终端类型可能依赖于本地参考。 拨号是传统的默认终端类型， 以至用户可以定制它们的登录脚本来注意终端什么时候拨号， 和自动调节它们的终端类型。 然而， 作者发现它很容易在它的站点上指定 `vt102` 作为默认的终端类型， 因为用户刚才在它们的远程系统上使用的是 VT102 模拟器。

您对`/etc/ttys`作修改之后，您可以发送 `init` 进程给一个 HUP 信号来重读文件。您可以使用下面的命令来发送信号：

```
# `kill -HUP 1`
```

如果这是您的第一次设置系统， 您可能要在发信号 `init` 之前等一下， 等到您的 modem 正确地配置并连接好。

##### 27.4.4.2.1. 锁定速度的配置

对于一个锁定速度的配置，您的 `ttys` 记录必须有一个为 `getty` 提供固定速度的记录。 对于一个速度被锁定在 19.2kbit/s 的 modem， `ttys` 记录是这样的：

```
ttyu0   "/usr/libexec/getty std.19200"   dialup on
```

如果您的 modem 被锁定在一个不同的数据速度， 为 std.speed 使用适当的速度来代替 std.19200。 确信您使用了一个在 `/etc/gettytab` 中列出的正确的类型。

##### 27.4.4.2.2. 匹配速度的设置

在一个匹配速度的设置中，您的 `ttys` 录需要参考在 `/etc/gettytab` 适当的起始 “auto-baud” 记录。 例如， 如果您为一个以 19.2 Kbit/s 开始的可匹配速度的 modem 添加上面建议的记录， 您的 `ttys` 记录可能是这样的：

```
ttyu0   "/usr/libexec/getty V19200"   dialup on
```

#### 27.4.4.3. `/etc/rc.d/serial`

高速调制解调器， 如使用 V.32、 V.32bis， 以及 V.34 的那些， 需要使用硬件 (`RTS/CTS`) 流控制。 您可以在 `/etc/rc.d/serial` 中增加 `stty` 命令来在 FreeBSD 内核中， 为调制解调器设置硬件流控制标志。

例如， 在 1 号串口 (`COM2`) 拨入和拨出设备上配置 `termios` 标志 `crtscts`， 可以通过在 `/etc/rc.d/serial` 增加下面的设置来实现：

```
# Serial port initial configuration
stty -f /dev/ttyu1.init crtscts
stty -f /dev/cuau1.init crtscts
```

### 27.4.5. Modem 设置

如果您有一个 modem， 它的参数能被存储在非易失性的 RAM 中， 您将必须使用一个终端程序来设置参数 （比如 MS-DOS® 下的 Telix 或者 FreeBSD 下的 `tip`）。 使用同样的通讯速度来连接 modem 作为初始速度 `getty` 将使用和配置 modem 的非易失性 RAM 来适应这些要求：

*   连接时宣告 CD

*   操作时宣告 DTR； DTR 消失时挂断线路并复位调制解调器

*   CTS 传输数据流控制

*   禁用 XON/XOFF 流控制

*   RTS 接收数据流控制

*   宁静模式 (无返回码)

*   无命令回显

请阅读您 modem 的文档找到您需要用什么命令和 DIP 接口设置。

例如，要在一个 U.S. Robotics® Sportster® 14400 的外置 modem 上设置上面的参数，可以用下面这些命令：

```
ATZ
AT&C1&D2&H1&I0&R2&W
```

您也可能想要在 modem 上寻找机会调节这个设置， 例如它是否使用 V.42bis 和 MNP5 压缩。

外置 modem 也有一些用来设置的 DIP 开关， 也许您可以使用这些设置作为一个例子：

*   Switch 1: UP ── DTR Normal

*   Switch 2: N/A (Verbal Result Codes/Numeric Result Codes)

*   Switch 3: UP ── Suppress Result Codes

*   Switch 4: DOWN ── No echo, offline commands

*   Switch 5: UP ── Auto Answer

*   Switch 6: UP ── Carrier Detect Normal

*   Switch 7: UP ── Load NVRAM Defaults

*   Switch 8: N/A (Smart Mode/Dumb Mode)

在拨号 modem 上的结果代码应该被 禁用/抑制， 以避免当 `getty` 在 modem 处于命令模式并回显输入时错误地给出 `login:` 提示时可能造成的问题。 这样可能导致 `getty` 与 modem 之间产生更长的不必要交互。

#### 27.4.5.1. 锁定速度的配置

对于锁定速度的配置， 您需要配置 modem 来获得一个不依赖于通讯率的稳定的 modem 到计算机 的传输率。 在一个 U.S. Robotics® Sportster® 14400 外置 modem 上， 这些命令将锁定 modem 到计算机 的传输率：

```
ATZ
AT&B1&W
```

#### 27.4.5.2. 匹配速度的配置

对于一个变速的配置， 您需要配置 modem 调节它的串口传输率匹配接收的传输率。 在一个 U.S. Robotics® Sportster® 14400 的外置 modem 上， 这些命令将锁定 modem 的错误修正传输率适合命令要求的速度， 但允许串口速度适应没有纠错的连接：

```
ATZ
AT&B2&W
```

#### 27.4.5.3. 检查 modem 的配置

大多数高速的 modem 提供了用来查看当前操作参数的命令。 在 USR Sportster 14400 外置 modem 上， 命令 `ATI5` 显示了存储在非易失性 RAM 中的设置。 要看看正确的 modem 操作参数， 可以使用命令 `ATZ` 然后是 `ATI4`。

如果您有一个不同牌子的 modem， 检查 modem 的使用手册看看如何双重检查您的 modem 的配置参数。

### 27.4.6. 问题解答

这儿是几个检查拨号 modem 的步骤。

#### 27.4.6.1. 检查 FreeBSD 系统

把您的 modem 连接到 FreeBSD 系统， 启动系统， 然后， 如果您的 modem 有一个指示灯， 当登录时看看 modem 的 DTR 指示灯是否亮： 会在系统控制台出现命令行――如果它亮， 意味着 FreeBSD 已经在适当的通讯端口启动了一个 `getty` 进程， 等待 modem 接收一个呼叫。

如果 DTR 指示灯不亮， 通过控制台登录到 FreeBSD 系统，然后执行一个 `ps ax` 命令来看 FreeBSD 是否正在正确的端口运行 `getty`进程。 您将在进程显示中看到像这样的一行：

```
  114 ??  I      0:00.10 /usr/libexec/getty V19200 ttyu0
  115 ??  I      0:00.10 /usr/libexec/getty V19200 ttyu1
```

如果您看到是这样的：

```
  114 d0  I      0:00.10 /usr/libexec/getty V19200 ttyu0
```

modem 不接收呼叫， 这意味着 `getty` 已经在通讯端口打开了。 这可以指出线缆有问题或 modem 错误配置， 因为 `getty` 无法打开通讯端口。

如果您没有看到任何 `getty` 进程等待打开想要的 `ttyuN` 端口， 在 `/etc/ttys` 中双击您的记录看看那儿是否有错误。 另外，检查日志文件 `/var/log/messages` 看看是否有一些来自 `init` 或 `getty` 的问题日志。 如果有任何信息， 仔细检查配置文件 `/etc/ttys` 和 `/etc/gettytab`，还有相应的设备文件 `/dev/ttyuN`， 是否有错误，丢失记录，或丢失了设备指定文件。

#### 27.4.6.2. 尝试接入 Try Dialing In

设法拨入系统。 确信使用 8 位， 没有奇偶检验， 在远程系统上的 1 阻止位。 如果您不能立刻得到一个命令行， 试试每隔一秒按一下 **Enter**。 如果您仍没有看到一个登录： 设法发送一个 `BREAK`。 如果您正使用一个高速的 modem 来拨号， 请在锁定拨号 modem 的接口速度后再试试。

如果您不能得到一个登录：prompt，再检查一下 `/etc/gettytab`，重复检查：

*   在`/etc/ttys` 中指定的初始可用的名称与 `/etc/gettytab` 的一个可用的相匹配。

*   每个 `nx=` 记录与另一个 `gettytab` 可用名称匹配。

*   每个 `tc=` 记录与另一个 `gettytab`可用名称相匹配。

如果您拨号但 FreeBSD 系统上的 modem 没有回应， 确信 modem 能回应电话。 如果 modem 看起来配置正确了， 通过检查 modem 的指示灯来确认 DTR 线连接正确。

如果您做了好几次，它仍然无法工作，打断一会，等会再试试。 如果还不能工作， 也许您应该发一封电子邮件给 [FreeBSD 一般问题邮件列表](http://lists.FreeBSD.org/mailman/listinfo/freebsd-questions) 寻求帮助。

## 27.5. 拨出设备

### 警告:

从 FreeBSD 8.0 开始， 用于串口的设备节点从 `/dev/cuadN` 改为了 `/dev/cuauN`； 从 `/dev/ttydN` 改为了 `/dev/ttyuN`。 FreeBSD 7.X 用户需要根据实际情况对这份文档中的例子进行必要的调整。

下面将让您的主机通过 modem 连接到另一台计算机上。 这只要适当地建立一个终端作为远程主机就可以。

这可以用来登录进一个 BBS。

如果您用 PPP 有问题， 那这种连接可以用来从 Internet 上下载一个文件。 如果您必须 FTP 一些东西， 而 PPP 断了， 使用终端会话来 FTP 它们。 然后使用 zmodem 来把它们传输到您的机器上。

### 27.5.1. 我的 Stock Hayes Modem 不被支持，我该怎么办?

事实上， 联机手册对于这个的描述已经过时了。 一个通用的 Hayes 拨号已经内建其中。 只要在您的 `/etc/remote` 文件中使用 `at=hayes`。

Hayes 驱动不够 “聪明” 只能认出一些比较新的 modem 的高级特性 ── 如 `BUSY`、 `NO DIALTONE`， 或 `CONNECT 115200` 的信息将被搞乱。 当您使用的时候， 您必须把这些信息关掉。(通过 `ATX0&W`)。

另外，拨号的延迟是 60 秒。 您的 modem 可能使用另外的时间或提示认为有其他的通讯问题。 试试 `ATS7=45&W`。

### 27.5.2. 我如何输入这些 AT 命令?

在 `/etc/remote` 文件中增加一个 “direct” 项。 举例而言， 如果您的调制解调器挂在第一个串口， 即 `/dev/cuau0` 上， 则应添加下面这行：

```
cuau0:dv=/dev/cuau0:br#19200:pa=none
```

此处应使用您的 modem 所支持的最高 br bps 速率。 接下来， 输入 `tip cuau0` 就可以连到 modem 上了。

此外， 也可以 `root` 身份执行 `cu` 命令：

```
# `cu -lline -sspeed`
```

*`line`* 是串口 (例如 `/dev/cuau0`) 而 *`speed`* 则是速率 (如 `57600`)。 当您输入完 AT 之后， 按 `~.` 即可退出。

### 27.5.3. 现在 pn `@`标记不能工作？

在电话号码中的 `@` 标记告诉计算机在 `/etc/phones` 文件中查找一个电话号码。 但 `@` 标记也是一个在像 `/etc/remote` 这样的可用文件中的特殊字符。 用一个反斜线符号退出：

```
pn=\@
```

### 27.5.4. 我如何在命令行拨电话号码?

在您的 `/etc/remote` 文件中通常放着一个叫做 “generic” 的记录。 例如：

```
tip115200|Dial any phone number at 115200 bps:\
        :dv=/dev/cuau0:br#115200:at=hayes:pa=none:du:
tip57600|Dial any phone number at 57600 bps:\
        :dv=/dev/cuau0:br#57600:at=hayes:pa=none:du:
```

然后， 可以执行：

```
# `tip -115200 5551234`
```

如果您更喜欢`cu`而不是`tip`，使用一个通用的`cu`记录：

```
cu115200|Use cu to dial any number at 115200bps:\
        :dv=/dev/cuau1:br#57600:at=hayes:pa=none:du:
```

然后键入：

```
# `cu 5551234 -s 115200`
```

### 27.5.5. 这么做时是否每次都需要重新输入 bps 速率?

添加一项 `tip1200` 或 `cu1200`， 并将 bps 速率换成更合适的值。 `tip` 的默认值是 1200  bps， 也就是为什么会有 `tip1200` 这条记录的原因。 虽然您并不需要使用 1200 bps。

### 27.5.6. 我通过一个终端服务器访问了很多主机。

除非每次都要等到您连接到主机然后键入 `CONNECT host`， 否则使用 `tip` 的 `cm` 功能。 例如， 在 `/etc/remote` 中的这些记录：

```
pain|pain.deep13.com|Forrester's machine:\
        :cm=CONNECT pain\n:tc=deep13:
muffin|muffin.deep13.com|Frank's machine:\
        :cm=CONNECT muffin\n:tc=deep13:
deep13:Gizmonics Institute terminal server:\
        :dv=/dev/cuau2:br#38400:at=hayes:du:pa=none:pn=5551234:
```

将让您键入 `tip pain` 或 `tip muffin` 连接到主机 `pain` 或 `muffin`， 和 `tip deep13` 连接到终端服务器。

### 27.5.7. `tip`能为每个站点试用多个线路吗？

经常有一个问题， 一个大学有几个 modem 线路， 几千个学生设法使用它们。

在 `/etc/remote` 中为您的大学添加一个记录， 然后为 `pn` 功能使用 `@` 标记：

```
big-university:\
        :pn=\@:tc=dialout
dialout:\
        :dv=/dev/cuau3:br#9600:at=courier:du:pa=none:
```

接着， 在 `/etc/phones` 中列出大学的电话号码：

```
big-university 5551111
big-university 5551112
big-university 5551113
big-university 5551114
```

`tip` 将按顺序试用每一个，然后就停止。 如果想继续测试， 隔一段时间再运行 `tip`。

### 27.5.8. 为什么我必须键入 **Ctrl**+**P** 两次才能发出 **Ctrl**+**P** 一次?

**Ctrl**+**P** 是默认的“强制”字符，被用来告诉 `tip` 下一个字符是文字的数据。您可以用 `~s` 给任何其他的字符设置强制字符，这意思是 “设置一个变量”。

在新的一行键入 `~sforce=single-char`。 *`single-char`* 是任何简单的字符。 如果您遗漏了 *`single-char`*， 那强制字符就是空字符， 这可以键入 **Ctrl**+**2** 或 **Ctrl**+**Space**来完成。 更好的 *`single-char`* 是 **Shift**+**Ctrl**+**6**， 这只用在一些终端服务器上。

通过在您的 `$HOME/.tiprc` 文件中指定下面这行， 就可以得到您想要的任何强制字符：

```
force=*`single-char`*
```

### 27.5.9. 突然我键入的每一样东西都变成了大写??

您一定是键入了 **Ctrl**+**A**， 即 `tip` 的 “raise character”， 会临时地指定成坏掉的 caps-lock 键。 使用上面的 `~s` 来合理地设置各种 `raisechar`。 事实上， 如果您不想使用这些特性的话，您可以用同样的方法设置强制字符。

这儿有一个很好的示例 `.tiprc 文件`，对 Emacs 用户来说，需要经常按 **Ctrl**+**2** 和 **Ctrl**+**A**：

```
force=^^
raisechar=^^
```

`^^` 是 **Shift**+**Ctrl**+**6**.

### 27.5.10. 如何用 `tip` 做文件传输？

如果您正在与另一台 UNIX® 系统对话， 您可以用 `~p`(put) 和 `~t` (take) 发送和接收文件。 这些命令可以在远程系统上运行 `cat` 和 `echo` 来接收和发送文件。 语法是这样的：

`~p` local-file [remote-file]

`~t` remote-file [local-file]

由于没有错误校验， 所以您需要使用其他协议， 如 zmodem。

### 27.5.11. 我如何用`tip`运行 zmodem？

要接收这些文件，可以在远程终端启动发送程序。然后，键入 `~C rz` 在本地开始接收它们。 要发送文件， 可以在远程终端启动接收程序。 然后， 键入 `~C sz files` 把它们发送到远程系统。

## 27.6. 设置串口控制台

Contributed by Kazutaka YOKOTA.Based on a document by Bill Paul.

### 警告:

从 FreeBSD 8.0 开始， 用于串口的设备节点从 `/dev/cuadN` 改为了 `/dev/cuauN`； 从 `/dev/ttydN` 改为了 `/dev/ttyuN`。 FreeBSD 7.X 用户需要根据实际情况对这份文档中的例子进行必要的调整。

### 27.6.1. 介绍

FreeBSD 可以通过一个串口只使用一个哑 (dumb) 终端就可以启动一个系统。 这样一种配置只有两种人能使用： 希望在机器上安装 FreeBSD 的系统管理员， 他没有键盘或显示器， 还有就是要调试内核或设备驱动程序的开发人员。

就象 第 13 章 *FreeBSD 引导过程* 描述的， FreeBSD 采用一个三步的启动过程。 最先两步储存在 FreeBSD 启动磁盘的启动 slice 的启动代码块中。 引导块然后就被加载， 接着运行第三步启动引导器 (`/boot/loader`)。

为了设置串口控制台， 您必须配置启动代码块， 启动引导器代码和内核。

### 27.6.2. 串口控制台的配置， 简明版

这一节假定您使用默认的配置， 只希望迅速地获得关于配置串口控制台的概览。

1.  使用串口电缆连接 `COM1` 和控制终端。

2.  要在串口控制台上显示所有的引导信息， 需要以超级用户的身份执行下面的命令：

    ```
    # echo 'console="comconsole"' >> /boot/loader.conf
    ```

3.  编辑 `/etc/ttys` 并把 `ttyu0` 的 `off` 改为 `on`， `dialup` 改为 `vt100`。 否则通过串口控制台上将不会提示输入口令， 从而导致潜在的安全漏洞。

4.  重新启动并观察是否生效。

如果需要不同的配置， 更进一步的配置讨论可以在 第 27.6.3 节 “串口控制台的设置” 找到。

### 27.6.3. 串口控制台的设置

1.  准备一根串口线缆。

    您需要使用一个 null-modem 的线缆或标准的串口线和一个 null-modem 适配器。 请参考 第 27.2.2 节 “线缆和端口” 中有关串口线的讨论。

2.  拔掉键盘。

    绝大多数的 PC 在开机检测的时候会检测到键盘， 如果没有检测到键盘， 则会出现错误。 一些机器会提示缺少键盘， 就不会继续引导系统。

    如果您的计算机出现错误， 但仍能继续启动， 您可以不必理它。

    如果您的计算机没有键盘拒绝启动， 那您需要配置 BIOS 来避免它。 请参考您的主板的使用说明了解更多细节。

    ### 提示:

    在 BIOS 中将键盘设为 “Not installed” (未安装)。 现在您仍然无法使用键盘。 这样做只是告诉 BIOS 在启动时不要探测键盘。 您的 BIOS 不应抱怨键盘不存在。 即使这一标志设置为 “Not installed” 时， 只要把键盘插上， 它就仍可使用。 如果以上的选项不存在于 BIOS 中， 可尝试寻找 “Halt on Error” 选项。 把这一项设置为 “All but Keyboard” 或者是 “No Errors”， 都能器到相同的作用。

    ### 注意:

    如果系统有 PS/2 鼠标， 如果幸运的话， 您也可以象键盘一样把它拔下来， 这是因为 PS/2 鼠标与键盘的一些硬件是共享的， 您的鼠标插上去， 系统会认为键盘仍在那儿。

3.  插一个哑 (dumb) 终端到`COM1`：（`sio0`）。

    如果您没有哑终端， 可以使用一个比较老的带有一个 modem 程序的 PC/XT 机器， 或在其他 UNIX® 机器上的串口。 如果您没有 `COM1`： (`sio0`)， 去找一个。 这时， 您就不能只能选择 `COM1`：来启动系统。 如果您已经在另一台设备上使用 `COM1`， 您必须临时删除那个设备， 然后安装一个新的系统引导块和内核。

4.  确信您的内核配置文件已经为 `COM1`： (`sio0`) 设置了适当的标记：

    有关的标记是：

    `0x10`

    启用控制台支持。 如果没有设置它， 则其他的控制台标记都会被忽略。 现在， 绝大多数的设置都有控制台的支持。 这个标记的第一个就是首选的。 这个单独选项是不能确保串口适用于控制台的， 设置下面的标记或加上下面描述的 `-h` 选项， 和这个放在一起。

    `0x20`

    无论是否使用了下面将要讨论的 `-h` 选项， 都强制这个单元作为控制台 (除非使用了更高优先级的控制台)。 标志 `0x20` 必须与 `0x10` 一起使用。

    `0x40`

    预留这个单元 (配合 `0x10`) 并让它不能用于普通的使用。 您不应在希望作为控制台的串口单元上设置这个标志。 这一标志是为内核远程调试准备的。 参见 开发者手册 以了解关于远程调试更进一步的情况。

    例如：

    ```
    device sio0 flags 0x10
    ```

    看看 [sio(4)](http://www.FreeBSD.org/cgi/man.cgi?query=sio&sektion=4&manpath=freebsd-release-ports) 的联机手册了解更多信息。

    如果标记没有被设置， 您必须运行 UserConfig 或重新编译内核。

5.  在启动磁盘的 `a` 分区的根目录创建 `boot.config` 文件。

    这个文件将指导引导块代码如何启动系统。 为了激活串口控制台， 您必须有一个或多个下面的选项――如果您要多个选项， 在同一行必须都包含它们：

    `-h`

    切换内部和串口控制台。 您使用这个来交换控制台设备。 例如， 如果您从内部控制台启动， 您可以使用 `-h` 来直接使用启动引导器和内核来使用串口作为它的控制台设备。 另外， 如果您从串口启动， 您可以使用 `-h` 来告诉启动引导器和内核使用显示设备作为控制台。

    `-D`

    切换单一和双重控制台配置。 在单一配置中， 控制台将是本机的控制台 (显示设备) 或串口。 在双重控制台配置中， 显示设备和串口将同时成为控制台， 无论 `-h` 的选项的情形。 然而， 双控制台配置只在引导块运行的过程中起作用。 一旦启动引导器获得控制， 由 `-h` 选项指定的控制台将成为唯一的控制台。

    `-P`

    在启动时，探测键盘。如果键盘找不到， `-D` 和 `-h` 选项会自动设置。

    ### 注意:

    由于当前版本引导块的空间限制， `-P` 选项只能探测扩展的键盘。 少于 101 键的键盘将无法被探测到。 如果您碰到这个情况， 您必须避免使用 `-P` 选项。 目前还没有绕过这个问题的办法。

    使用 `-P` 选项来自动选择控制台， 或使用 `-h` 选项来激活控制台。

    您也可以使用 boot 联机文档中所描述的其他选项。

    除了 `-P` 选项， 所有选项将被传给启动引导器 (`/boot/loader`)。 启动引导器将通过检查 `-h` 选项的状态来决定是显示设备成为控制台， 还是串口成为控制台。 这表示如果您指定 `-D` 选项， 但在 `/boot.config` 中没有 `-h` 选项， 您在启动代码块时使用串口作为控制台。 启动引导器将使用内部显示设备作为控制台。

6.  启动机器

    当您启动您的 FreeBSD 时，引导块将把 `/boot.config` 的内容发给控制台。例如：

    ```
    /boot.config: -P
    Keyboard: no
    ```

    如果您把 `-P` 放在 `/boot.config` 中并指出键盘存在或不存在， 那将只出现第二行。 这些信息会被定位到串口或内部控制台， 或两者同时， 这完全取决于 `/boot.config` 中的选项。

    | 选项 | 送出消息的设备 |
    | --- | --- |
    | none | 内部控制台 |
    | `-h` | 串口控制台 |
    | `-D` | 串口控制台和内部控制台 |
    | `-Dh` | 串口控制台和内部控制台 |
    | `-P`， 有键盘 | 内部控制台 |
    | `-P`， 无键盘 | 串口控制台 |

    出现上面信息后， 在引导块加载启动引导器和更多信息被映到屏幕之前将有一个小小的停顿。 在通常情况下，您不需要打断启动进程， 但为了确信设置是否正确，您也可以这样做。

    在控制台上按 **Enter** 以外的任意键就能打断启动进程。 引导块将进入命令行模式。 您将看到：

    ```
    >> FreeBSD/i386 BOOT
    Default: 0:ad(0,a)/boot/loader
    boot:
    ```

    检验上面出现的信息， 可能是串口， 或内部控制台， 或两个同时， 完全取决于您在 `/boot.config` 中的选项。 如果信息出现在正确的控制台， 按 **Enter** 继续启动进程。

    如果您要使用串口控制台， 但您没有看到命令行， 那可能设置有问题。 这时， 输入 `-h` 然后按 **Enter** 或 **Return** 来告诉引导块 (然后是启动引导器和内核) 选择串口作为控制台。 一旦系统起来了， 就可以回去检查一下是什么出了问题。

启动引导器加载完后， 您将进入启动进程的第三步， 您仍然可以在启动引导器通过设定您喜欢的环境来切换内部控制台和串口控制台。 参考 第 27.6.6 节 “从启动引导器修改控制台”。

### 27.6.4. 摘要

这是几个在这章要讨论的几个设置和选择的控制台的摘要。

#### 27.6.4.1. 例 1： 您为 `sio0` 设置标记 0x10

```
device sio0 flags 0x10
```

| 在 /boot.config 中的选项 | 引导块执行时所用的控制台 | 引导加载器执行时所用的控制台 | 内核所用的控制台 |
| --- | --- | --- | --- |
| 无 | 内部 | 内部 | 内部 |
| `-h` | 串口 | 串口 | 串口 |
| `-D` | 串口和内部 | 内部 | 内部 |
| `-Dh` | 串口和内部 | 串口 | 串口 |
| `-P`， 有键盘 | 内部 | 内部 | 内部 |
| `-P`， 没有键盘 | 串口和内部 | 串口 | 串口 |

#### 27.6.4.2. 例 2：您为 `sio0` 设置标记为 0x30

```
device sio0 flags 0x30
```

| 在 /boot.config 中的选项 | 引导块执行时所用的控制台 | 引导加载器执行时所用的控制台 | 内核所用的控制台 |
| --- | --- | --- | --- |
| 无 | 内部 | 内部 | 串口 |
| `-h` | 串口 | 串口 | 串口 |
| `-D` | 串口和内部 | 内部 | 串口 |
| `-Dh` | 串口和内部 | 串口 | 串口 |
| `-P`， 有键盘 | 内部 | 内部 | 串口 |
| `-P`， 没有键盘 | 串口和内部 | 串口 | 串口 |

### 27.6.5. 串口控制台的提示

#### 27.6.5.1. 设置更高的串口速度

在默认配置中， 串口的设置是： 速率 9600 波特、 8 数据位、 无奇偶校验位、 1 停止位。 如果您希望修改默认的控制台速率， 可以采用下列几种方法之一：

*   将 `BOOT_COMCONSOLE_SPEED` 配置为希望的速率， 并重新编译引导块。 请参见 第 27.6.5.2 节 “使用 `sio0` 以外的串口 作为控制台” 以了解如何联编和安装新的引导块。

    如果串口控制台已配置为使用 `-h` 以外的其它方式引导， 或者内核使用的速率与引导块不同， 则必需在内核配置文件中加入下述设置， 并重新联编新内核：

    ```
    options CONSPEED=19200
    ```

*   使用内核引导选项 `-S`. `-S` 这个命令行选项可以加到 `/boot.config` 中。 请参见联机手册 [boot(8)](http://www.FreeBSD.org/cgi/man.cgi?query=boot&sektion=8&manpath=freebsd-release-ports) 以获得如何在 `/boot.config` 中增加选项， 以及其它的可用选项。

*   在您的 `/boot/loader.conf` 文件中启用 `comconsole_speed` 选项。

    使用这个选项时，您还需要在 `/boot/loader.conf` 中配置 `console`、 `boot_serial`， 以及 `boot_multicons`。 下面是一个利用 `comconsole_speed` 改变串口控制台速率的例子：

    ```
    boot_multicons="YES"
    boot_serial="YES"
    comconsole_speed="115200"
    console="comconsole,vidconsole"
    ```

#### 27.6.5.2. 使用 `sio0` 以外的串口 作为控制台

使用串口而不是 `sio0` 作为控制台需要做一些重编译。 如果您无论如何都要使用另一个串口， 重新编译引导块， 启动引导器和内核。

1.  取得内核源代码 (参考 第 25 章 *更新与升级 FreeBSD*)。

2.  编辑 `/etc/make.conf` 文件， 然后设置 `BOOT_COMCONSOLE_PORT`作为您要使用 (`0x3f8`、 `0x2f8`、 0x3E8 或 0x2E8) 端口的地址。 只有 `sio0` 到 `sio3` (`COM1` 到 `COM4`) 都可以使用； 但多口串口卡将不会工作。 不需要任何中断设置。

3.  创建一个定制的内核配置文件， 在您要使用的串口添加合适的标记。 例如， 如果要将 `sio1` (`COM2`) 作为控制台：

    ```
    device sio1 flags 0x10
    ```

    或

    ```
    device sio1 flags 0x30
    ```

    其他端口的控制台标记也不要设。

4.  重新编译和安装引导块：

    ```
    # `cd /sys/boot`
    # `make clean`
    # `make`
    # `make install`
    ```

5.  重建和安装内核。

6.  用 [bsdlabel(8)](http://www.FreeBSD.org/cgi/man.cgi?query=bsdlabel&sektion=8&manpath=freebsd-release-ports) 将引导块写到启动盘上，然后从新内核启动。

#### 27.6.5.3. 通过串口线进入 DDB 调试器

```
options BREAK_TO_DEBUGGER
options DDB
```

#### 27.6.5.4. 在串口控制台上得到一个登录命令行

您可能希望通过串口线进入登录提示， 现在您可以看到启动信息， 通过串口控制台键入内核调试信息。可以这样做。

用一个编辑器打开 `/etc/ttys` 文件， 然后找到下面的行：

```
ttyu0 "/usr/libexec/getty std.9600" unknown off secure
ttyu1 "/usr/libexec/getty std.9600" unknown off secure
ttyu2 "/usr/libexec/getty std.9600" unknown off secure
ttyu3 "/usr/libexec/getty std.9600" unknown off secure
```

`ttyu0` 到 `ttyu3` 相当于 `COM1` 到 `COM4`。 可以打开或关闭某个端口。 如果您已经改变了串口的速度， 还必须改掉标准的 `9600` 与当前的例如 `19200` 相匹配。

您也可以改变终端的类型从不知名的到您串口终端的真实类型。 编辑完这个文件， 您必须 `kill -HUP 1` 来使这个修改生效。

### 27.6.6. 从启动引导器修改控制台

前面一节描述了如何通过调整引导块来设定串口控制台。 这节将讲到在启动引导器中通过键入一些命令和环境变量来指定控制台。 由于启动引导器会被启动进程的第三步所调用， 引导块以后， 在启动引导器中的设置将忽略在引导块中的设置。

#### 27.6.6.1. 配置串口控制台

您可以很容易地指定启动引导器和内核来使用串口控制台， 只需要在 `/boot/loader.ronf`中写入下面这行：

```
console="comconsole"
```

无论前一节中的引导块如何配置， 这个设置都会生效。

您最好把上面一行放在 `/boot/loader.conf` 文件的第一行，以便尽早地在启动时看到串口控制台的启动信息。

同样地，您可以指定内部控制台为：

```
console="vidconsole"
```

如果您不设置启动引导环境变量控制台， 启动引导器和内核将使用在引导块时用 `-h` 选项指定的控制台。

控制台可以在 `/boot/loader.conf.local` 或者是在 `/boot/loader.conf` 中指定。

看看 [loader.conf(5)](http://www.FreeBSD.org/cgi/man.cgi?query=loader.conf&sektion=5&manpath=freebsd-release-ports) 的联机手册了解更多信息。

### 注意:

目前， 引导块尚不提供与引导加载器的 `-P` 选项等价的选项， 另外， 它也不能根据是否有键盘存在自动决定选择使用内部控制台还是串口控制台。

#### 27.6.6.2. 使用串口而不是`sio0`作为控制台

要使用一个串口而不是 `sio0` 作为串口控制台 需要重新编译启动引导器。下面的步骤跟 第 27.6.5.2 节 “使用 `sio0` 以外的串口 作为控制台” 描述的相似。

### 27.6.7. 警告

这篇文章本意是想告诉人们如何设定没有显示设备或键盘的专用服务器。 不幸的是， 绝大多数系统没有键盘可以让您启动， 而没有显示设备就不让您启动。 使用 AMI BIOS 的机器可以通过在 CMOS 中将 “graphics adapter” 项设为 “Not installed” 来在启动时不要求显示适配器。

然而， 许多机器并不支持这个选项， 如果您的系统没有显示硬件就拒绝启动。 对于这些机器， 即使您没有显示器， 也必须在机器上插上显示适配器。 建议您试试采用 AMI BIOS 的机器。

## 第 28 章 PPP 和 SLIP

Restructured, reorganized, and updated by Jim Mock.

## 28.1. 概述

FreeBSD 有很多方法可以将计算机与计算机连接起来。 通过使用拨号 modem 来建立网络或 Internet 连接， 或允许其他人通过您的机器来连上网络， 这些都要求使用 PPP 或 SLIP。 这章将详细介绍设置这些基于 modem 的通信服务的方法。

读完这一章， 您将了解：

*   如何设置用户级 PPP。

*   如何设置内核级 PPP。 (仅限 FreeBSD 7.X)。

*   如何设置 PPPoE (PPP over Ethernet)。

*   如何设置 PPPoA (PPP over ATM)。

*   如何配置和安装 SLIP 客户端和服务器。 (仅限 FreeBSD 7.X)。

在阅读这章之前， 您应：

*   熟悉基本的网络术语。

*   理解拨号连接和 PPP、 SLIP 的基础知识。

您可能想知道用户级 PPP 与内核级 PPP 之间的不同之处。 回答很简单： 用户级 PPP 处理用户级的输入和输出数据， 而不是内核级。 在内核与用户区之间复制数据的花费要大一些， 但它能提供具有更多特性的 PPP 实现。 用户级 PPP 使用 `tun` 设备与外界通信而内核级 PPP 使用 `ppp` 设备。

### 注意:

在这章中， 如果没有特殊说明， 则 ppp 指的是用户态 PPP， 除非需要和其它 PPP 软件， 例如 pppd (仅限 FreeBSD 7.X) 加以区分。 另外， 若没有额外的注明， 本章所介绍的所有命令都需要以 `root` 身份来运行权限。

## 28.2. 使用用户级 PPP

Updated and enhanced by Tom Rhodes.Originally contributed by Brian Somers.With input from Nik Clayton, Dirk Frömberg 和 Peter Childs.

### 警告:

从 FreeBSD 8.0 开始， [uart(4)](http://www.FreeBSD.org/cgi/man.cgi?query=uart&sektion=4&manpath=freebsd-release-ports) 驱动取代了 [sio(4)](http://www.FreeBSD.org/cgi/man.cgi?query=sio&sektion=4&manpath=freebsd-release-ports) 驱动。 用以表示串口的设备节点由分别 `/dev/cuadN` 改为了 `/dev/cuauN`， 并从 `/dev/ttydN` 改为了 `/dev/ttyuN`。 FreeBSD 7.X 用户在升级时需要因应之对配置文件进行必要的更改。

### 28.2.1. 用户级 PPP

#### 28.2.1.1. 前提条件

本章假定您具备如下条件：

*   您有一个 ISP 提供的用于连接使用 PPP 的帐号。

*   您需要有连接在系统上， 并做了正确配置的 modem， 或其他能够连接您 ISP 的设备。

*   ISP 的拨号号码。

*   您的登录名称和密码 (可能是一般的 UNIX 风格的登录名和密码对， 也可能是 PAP 或 CHAP 登录名和密码对)。

*   一个或多个域名服务器 IP 地址。 通常， 您会从 ISP 处得到两个这样的 IP 地址。 如果您至少得到了一个， 就可以在文件 `ppp.conf` 中加入 `enable dns` 命令使 ppp 设置域名服务。 这个功能取决于 ISP 对支持 DNS 协商的具体实现。

下面的信息由您的 ISP 提供， 但不是必需的：

*   ISP 的网关 IP 地址。 网关是您准备连接， 并设为 *默认路由* 的主机。 如果您没有这个信息， 您可以虚构一个， 在连接时 ISP 的 PPP 服务器会自动告诉您正确的值。

    这个虚构的 IP 地址在 ppp 中记做 `HISADDR`。

*   准备使用的子网掩码。 如果 ISP 没有提供， 一般使用 `255.255.255.255` 是没有问题的。

*   如果 ISP 提供了静态的 IP 地址和主机名， 可以输入它们。 反之， 则应让对方主机指定它认为合适的 IP 地址。

如果您不知道这些信息， 请与您的 ISP 联系。

### 注意:

在这节中， 所有作为例子展示的配置文件中都有行号。 这些行号只是为了使解释和讨论变得方便， 在真实的文件中并不存在。 此外， 在必要时应使用 Tab 和空格来进行缩进。

#### 28.2.1.2. PPP 自动化配置

`ppp`和`pppd`(PPP 的内核级实现， 仅限 FreeBSD 7.X) 都使用 `/etc/ppp` 目录中的配置文件。 用户级 PPP 的例子可以在 `/usr/share/examples/ppp/` 中找到。

配置`ppp`要求根据您的需要编辑几个文件。 编辑哪几个文件取决于您的 IP 是静态分配 (每次都使用同一个地址) 还是动态分配的 (每次连接到 ISP 都会获得不同的 IP 地址)。

##### 28.2.1.2.1. PPP 和静态 IP 地址

您需要编辑配置文件`/etc/ppp/ppp.conf`， 如下所示。

### 注意:

以冒号`:`结尾的行从第一列 (行首)开始， 其它所有的行都要使用空格或制表符 (Tab) 来缩进。

```
1     default:
2       set log Phase Chat LCP IPCP CCP tun command
3       ident user-ppp VERSION (built COMPILATIONDATE)
4       set device /dev/cuau0
5       set speed 115200
6       set dial "ABORT BUSY ABORT NO\\sCARRIER TIMEOUT 5 \
7                 \"\" AT OK-AT-OK ATE1Q0 OK \\dATDT\\T TIMEOUT 40 CONNECT"
8       set timeout 180
9       enable dns
10
11    provider:
12      set phone "(123) 456 7890"
13      set authname foo
14      set authkey bar
15      set login "TIMEOUT 10 \"\" \"\" gin:--gin: \\U word: \\P col: ppp"
16      set timeout 300
17      set ifaddr *`x.x.x.x`* *`y.y.y.y`* 255.255.255.255 0.0.0.0
18      add default HISADDR
```

行 1：

指定默认的项。 当 PPP 运行时这个项中的命令将自动执行。

行 2：

启用登录参数。 工作正常后， 为避免产生过多的日志文件， 这行应该简化为：

```
set log phase tun
```

行 3：

告诉 PPP 怎样向对方标识自己。 如果在建立或使用连接时遇到任何麻烦， PPP 就会向对方主机自我标识。 对方主机管理员在处理这个问题时， 这些信息会有用。

行 4：

标明 modem 要连接的端口号。 `COM1` 对应的设备是 `/dev/cuau0` 而 `COM2` 对应的则是 `/dev/cuau1`。

行 5：

设置连接的速度。 如果 115200 有问题， 试试 38400。

行 6 & 7：

拨号字符串。 用户级 PPP 使用一种与 [chat(8)](http://www.FreeBSD.org/cgi/man.cgi?query=chat&sektion=8&manpath=freebsd-release-ports)程序相似的语法。 请参考联机手册了解这种语言的相关信息。

注意， 为了便于阅读此命令进行了换行。 任何 `ppp.conf` 里的命令都可以这样做， 前提是行的最后一个字符必须是 `\`。

行 8：

设置连接的时间间隔。 默认是 180 秒， 所以这一行是多余的。

行 9：

告诉 PPP 向对方主机确认本地域名解析设置。 如果您运行了本地的域名服务器， 要注释或删除掉这一行。

行 10：

为了可读性的需要设置一个空行。 空行会被 PPP 忽略。

行 11：

为 “provider”指定一个项。 可以改成 ISP 的名字， 这样您以后就可以使用 `load *`ISP`*` 来开启连接。

行 12：

设置提供商的电话号码。 多个电话号码可以使用冒号 (`:`) 或管道符号 (`|`) 隔开。 这两个字符的区别在[ppp(8)](http://www.FreeBSD.org/cgi/man.cgi?query=ppp&sektion=8&manpath=freebsd-release-ports)的联机手册中有介绍。 总的来讲， 如果您要循环使用这些号码， 可以使用冒号。 如果您想使用第一个号码， 当第一个号码失败了再用第二个号码， 就使用管道符号。 如所示的那样， 要给整个电话号码加上引号(")。

如果电话号码里有空格， 必须用引号(`"`)将其括起来。 否则会造成简单却难以察觉的错误。

行 13 & 14：

指定用户名和密码。 当使用 UNIX® 风格的命令提示符登录时， 这些值可以用带有 \U \P 参数的 `set login` 命令进行修改。 当使用 PAP 或 CHAP 进行连接时， 这些值在验证使用。

行 15：

如果您使用的是 PAP 或者 CHAP， 在这里就不会有登录。 要注释或删除掉这一行。 请参考 PAP 和 CHAP 认证 以了解更多细节。

登录命令是的语法是 chat 类型的。 在这个例子中是这样的：

```
J. Random Provider
login: *`foo`*
password: *`bar`*
protocol: ppp
```

您需要改变这个脚本以适合您自己的需要。 当您第一次写这个脚本时， 应当确保已经启用 “chat” 并处于登录状态， 这样您才能确认通信是否正在按计划进行。

行 16：

设置默认的超时时间。 这里， 连接若在 300 秒内无响应将被断开。如果您不想设置成超时， 将这个值设置成 0， 或在命令行使用 `-ddial` 选项。

行 17：

设置接口地址。 您需要用 ISP 提供给您的 IP 地址替换字符串 *`x.x.x.x`*， 用 ISP 的网关 IP 地址 (即您要连接的主机) 替换字符串 *`y.y.y.y`*。 如果 ISP 没有给您提供网关地址， 可以使用 `10.0.0.2/0`。 如果您需要使用一个 “猜到”的地址， 请确保在 `/etc/ppp/ppp.linkup` 中为每个 PPP 和动态 IP 地址 指令创建了这一项。 如果没有这一行， `ppp` 将无法以 `-auto` 模式运行。

第 18 行：

添加一个到 ISP 网关的默认路由。 `HISADDR`这个关键字会被第 17 行所指定的网关地址替换。 这行必须出现在第 17 行之后，以免在 `HISADDR` 初始化之前使用它的值。

如果您不想使用 `-auto` 的 PPP，则这行应挪到 `ppp.linkup` 文件中。

若您有一个静态 IP 地址， 且使用`-auto` 模式运行 ppp(因为在连接之前已经正确设置了路由表项)， 那就不需要再向`ppp.linkup` 添加项。 您可能希望在连接以后创建一个项来调用程序。 这在以后的 sendmail 的例子中会解释。

示例配置文件可以在目录 `/usr/share/examples/ppp/` 中找到。

##### 28.2.1.2.2. PPP 和动态 IP 地址

如果 ISP 没给您指定静态的 IP 地址， `ppp`要被配置成能够与对方协商确定本地和远程地址。 要完成这项工作， 先要“猜”一个 IP 地址， 然后允许 `ppp`在连接后使用 IP 配置协议(IPCP)进行正确配置。 `ppp.conf`的配置是与 PPP 和静态 IP 地址一样的， 除了以下的改变：

```
17      set ifaddr 10.0.0.1/0 10.0.0.2/0 255.255.255.255 0.0.0.0
```

再次强调， 不要包括行号， 它只是一个引用标记。 缩排一个空格是必需的。

行 17：

`/` 字符后面是 PPP 所要求的地址掩码。 您可以根据需要使用不同 IP 地址， 但以上的例子永远是可行的。

最后的参数(`0.0.0.0`)告诉 PPP 从`0.0.0.0` 而不是 `10.0.0.1` 开始协商地址， 对于有些 ISP， 这是必需的。 不要将 `0.0.0.0` 作为 `set ifaddr` 的第一个参数， 因为这使得 PPP 在 `-auto` 模式时不能设置初始路由。

如果您不运行`-auto`模式， 就需要在`/etc/ppp/ppp.linkup`中创建一个项。 连接建立之后， `ppp.linkup`被启用。 这时候， `ppp`将指派接口地址， 接着再添加路由表项：

```
1     provider:
2        add default HISADDR
```

行 1：

为了建立连接， `ppp` 会按照如下规则在 `ppp.linkup`寻找项:首先， 试图寻找相同的标签 (如同在`ppp.conf`一样）。 如果失败了， 寻找作为网关 IP 地址的项， 此项是四个八位字节的风格。 如果依旧没有找到， 就寻找 `MYADDR` 项

行 2：

这行告诉 `ppp`添加指向 `HISADDR`的默认路由。 `HISADDR`由通过 IPCP 协商得到的 IP 号替换。

参考`/usr/share/examples/ppp/ppp.conf.sample` 和`/usr/share/examples/ppp/ppp.linkup.sample` 中的`pmdemand`项以获取细节化的例子。

##### 28.2.1.2.3. 接收拨入

当要配置 ppp 接受来自 LAN 上的 拨入时， 您需要决定是否将包转给 LAN。 如果是的话， 您就必须从 LAN 子网中给对方分配一个 IP， 需要在文件 `/etc/ppp/ppp.conf` 中使用命令 `enable proxy`。 您还应该确定文件 `/etc/rc.conf` 中包含以下内容：

```
gateway_enable="YES"
```

##### 28.2.1.2.4. 使用哪个 getty？

配置 FreeBSD 的拨号服务 描述了如何用 [getty(8)](http://www.FreeBSD.org/cgi/man.cgi?query=getty&sektion=8&manpath=freebsd-release-ports) 来启动拨号服务。

除了 `getty` 之外还有 [mgetty](http://mgetty.greenie.net/) (可通过 [comms/mgetty+sendfax](http://www.freebsd.org/cgi/url.cgi?ports/comms/mgetty+sendfax/pkg-descr) port 来安装)， 它是 `getty` 的智能版本， 是按照拨号线的思想设计的。

使用 `mgetty` 的好处是它能积极地与 modem 进行 *会话*， 这就意味着如果在`/etc/ttys`中的端口被关闭， 您的 moderm 就不会回应拨入。

较新版本的 `mgetty` (从 0.99beta 起) 也支持自动检测 PPP 数据流， 这样即便客户端不使用脚本也能访问服务器了。

参考 Mgetty 和 AutoPPP 的联机手册了解更多信息。

##### 28.2.1.2.5. PPP 权限

`ppp` 命令通常必须以 `root` 用户的身份运行。 如果希望以普通用户的身份启动 `ppp` 服务 (就像下面描述的那样)， 就必须把此用户加入 `network` 组， 使其获得运行 `ppp` 的权限。

您还需要使用`allow`命令使用户能访问配置文 件的一个或多个部分：

```
allow users fred mary
```

如果这个命令被用在 `default` 部分中， 您可以让指定的用户访问任何东西。

##### 28.2.1.2.6. 动态 IP 用户的 PPP Shell

创建一个名为`/etc/ppp/ppp-shell`文件， 加入以下内容：

```
#!/bin/sh
IDENT=`echo $0 | sed -e 's/^.*-\(.*\)$/\1/'`
CALLEDAS="$IDENT"
TTY=`tty`

if [ x$IDENT = xdialup ]; then
        IDENT=`basename $TTY`
fi

echo "PPP for $CALLEDAS on $TTY"
echo "Starting PPP for $IDENT"

exec /usr/sbin/ppp -direct $IDENT
```

这个脚本要有可执行属性。 然后通过如下命令创建一个指向此脚本且名为 `ppp-dialup`的符号链接：

```
# `ln -s ppp-shell /etc/ppp/ppp-dialup`
```

您应该将这个脚本作为所有拨入用户的 *shell*。 以下是在文件 `/etc/passwd` 中关于 PPP 用户 `pchilds` 的例子 (切记， 不要直接修改这个密码文件， 用 [vipw(8)](http://www.FreeBSD.org/cgi/man.cgi?query=vipw&sektion=8&manpath=freebsd-release-ports) 来修改它)。

```
pchilds:*:1011:300:Peter Childs PPP:/home/ppp:/etc/ppp/ppp-dialup
```

创建一个名为 `/home/ppp` 的目录作为拨入用户的主目录， 其中包含以下这些空文件：

```
-r--r--r--   1 root     wheel           0 May 27 02:23 .hushlogin
-r--r--r--   1 root     wheel           0 May 27 02:22 .rhosts
```

这样就可以防止`/etc/motd`被显示出来。

##### 28.2.1.2.7. 静态 IP 用户的 Shell

像上面那样创建`ppp-shell`文件， 为每个静态分配 IP 用户创建一个到 `ppp-shell`的 符号链接。

例如， 如果您希望为三个拨号用户， `fred`， `sam`， 和 `mary` 路由 /24 CIDR 的网络， 则需要键入以下内容：

```
# `ln -s /etc/ppp/ppp-shell /etc/ppp/ppp-fred`
# `ln -s /etc/ppp/ppp-shell /etc/ppp/ppp-sam`
# `ln -s /etc/ppp/ppp-shell /etc/ppp/ppp-mary`
```

每个用户的 Shell 必须被设成一个符号链接(例如用户 `mary`的 Shell 应该是`/etc/ppp/ppp-mary`)。

##### 28.2.1.2.8. 为动态 IP 用户设置`ppp.conf`

`/etc/ppp/ppp.conf`文件应该包含下面 这些行：

```
default:
  set debug phase lcp chat
  set timeout 0

ttyu0:
  set ifaddr 203.14.100.1 203.14.100.20 255.255.255.255
  enable proxy

ttyu1:
  set ifaddr 203.14.100.1 203.14.100.21 255.255.255.255
  enable proxy
```

### 注意:

缩进得必须的。

`default:`项在每次会话时都会加载。 每个在 `/etc/ttys` 中启用的行都必须为其创建一个相似于 `ttyu0:` 的项。 每一行应该从动态 IP 地址池中取得唯一的 IP 地址。

##### 28.2.1.2.9. 为静态 IP 用户配置 `ppp.conf`

根据上面 `/usr/share/examples/ppp/ppp.conf` 文件的内容， 您必须为每个静态拨号用户添加一个项。 我们继续以 `fred`、 `sam` 以及 `mary`为例。

```
fred:
  set ifaddr 203.14.100.1 203.14.101.1 255.255.255.255

sam:
  set ifaddr 203.14.100.1 203.14.102.1 255.255.255.255

mary:
  set ifaddr 203.14.100.1 203.14.103.1 255.255.255.255
```

如果需要， `/etc/ppp/ppp.linkup` 也应该包括每个静态 IP 用户的的路由信息。 下面这一行为客户连接添加了到 `203.14.101.0/24` 网络的路由。

```
fred:
  add 203.14.101.0 netmask 255.255.255.0 HISADDR

sam:
  add 203.14.102.0 netmask 255.255.255.0 HISADDR

mary:
  add 203.14.103.0 netmask 255.255.255.0 HISADDR
```

##### 28.2.1.2.10. `mgetty`和 AutoPPP

默认情况下， [comms/mgetty+sendfax](http://www.freebsd.org/cgi/url.cgi?ports/comms/mgetty+sendfax/pkg-descr) port 在编译时启用了 `AUTO_PPP` 选项， 它使 `mgetty` 能够检测 PPP 连接的 LCP 状态， 并自动产生 PPP shell。 不过， 由于在默认配置中的 login/password 序列并不出现， 因此， 就必须使用 PAP 或 CHAP 来严重用户身份。

这节假定用户已经在系统中成功地编译并安装了 [comms/mgetty+sendfax](http://www.freebsd.org/cgi/url.cgi?ports/comms/mgetty+sendfax/pkg-descr)。

确认您的 `/usr/local/etc/mgetty+sendfax/login.config` 文件中包含以下内容：

```
/AutoPPP/ -     -		      /etc/ppp/ppp-pap-dialup
```

这行告诉`mgetty`运行 `ppp-pap-dialup`脚本来侦听 PPP 连接。

创建`/etc/ppp/ppp-pap-dialup`文件写入以下内容 (此文件应该是可执行的)：

```
#!/bin/sh
exec /usr/sbin/ppp -direct pap$IDENT
```

对应于每个在`/etc/ttys`的启用行， 都要在`/etc/ppp/ppp.conf` 中创建相应的项。 这和上面的定义是相同的。

```
pap:
  enable pap
  set ifaddr 203.14.100.1 203.14.100.20-203.14.100.40
  enable proxy
```

每个以这种方式登录的用户， 都必须在 `/etc/ppp/ppp.secret` 文件中给出用户名/口令， 或者使用以下选项， 来通过 PAP 方式以 `/etc/passwd` 文件提供的信息来完成身份验证。

```
enable passwdauth
```

如果您想为某些用户分配静态 IP， 可以在 `/etc/ppp/ppp.secret` 中将 IP 号作为第三个参数指定。 请参见 `/usr/share/examples/ppp/ppp.secret.sample` 中的例子。

##### 28.2.1.2.11. MS Extensions

可以配置 PPP 以提供 DNS 和 NetBIOS 域名服务器地址。

要在 PPP 1.x 版本中启用这些扩展， 需要在 `/etc/ppp/ppp.conf` 的对应项中加入下列配置：

```
enable msext
set ns 203.14.100.1 203.14.100.2
set nbns 203.14.100.5
```

PPP 版本 2 及以上：

```
accept dns
set dns 203.14.100.1 203.14.100.2
set nbns 203.14.100.5
```

这将告诉客户端首选域名服务器和备用域名服务器。

在版本 2 及以上版本中， 如果省略了 `set dns`， PPP 会使用 `/etc/resolv.conf`中的值。

##### 28.2.1.2.12. PAP 和 CHAP 验证

一些 ISP 将系统配置为使用 PAP 或 CHAP 机制来完成连接验证。 如果遇到这种情况， 在您连接时 ISP 就不会看到 `login:` 提示符， 而是立即开始 PPP 对话。

PAP 安全性要比 CHAP 差一些， 但在这里安全性并不是问题， 因为密码 (即使用明文传送) 只是通过串行线传送， 攻击者并没有太多机会去 “窃听” 它。

参考 PPP 与静态 IP 地址 或 PPP 与动态 IP 地址 小节， 并完成下列改动：

```
13      set authname *`MyUserName`*
14      set authkey *`MyPassword`*
15      set login
```

第 13 行：

这一行指明您的 PAP/CHAP 用户名。 您需要为*`MyUserName`*输入正确的值。

第 14 行：

这一行指明您的 PAP/CHAP password 密码。 您需要为 *`MyPassword`* 输入正确的值。 另外，您可能希望加入一些额外的选项，例如：

```
16      accept PAP
```

或

```
16      accept CHAP
```

以明确您的意图， 不过， 默认情况下 PAP 和 CHAP 都会被接受。

行 15：

如果您使用的是 PAP 或 CHAP， 一般来说 ISP 就不会要求您登录服务器了。 这时， 就必须禁用 “set login” 设置。

##### 28.2.1.2.13. 即时改变您的`ppp` 配置

与后台运行的`ppp`程序进行对话是可能的， 前提是设置了一个合适的诊断端口。 做到这一点， 需要把下面的行加入到您的配置中：

```
set server /var/run/ppp-tun*`%d`* DiagnosticPassword 0177
```

这行告诉 PPP 在指定的 UNIX®域 socket 中侦听， 当用户连接时需要给出指定的密码。 `%d`用`tun`设备号替换。

一旦启用了 socket， 就可以在脚本中调用程序[pppctl(8)](http://www.FreeBSD.org/cgi/man.cgi?query=pppctl&sektion=8&manpath=freebsd-release-ports)来处理正在运行的 的 PPP。

#### 28.2.1.3. 使用 PPP 网络地址翻译

PPP 可以使用内建的 NAT， 而无需内核支持。 您可以在 `/etc/ppp/ppp.conf` 中加入如下配置来启用它：

```
nat enable yes
```

PPP NAT 也可以使用命令行选项 `-nat`启动。 在 `/etc/rc.conf` 文件中也有 `ppp_nat` 项， 并默认启用。

如果您使用了这个特性， 您还会发现在 `/etc/ppp/ppp.conf`中以下 选项对于启用 incoming connections forwarding 是有用的：

```
nat port tcp 10.0.0.2:ftp ftp
nat port tcp 10.0.0.2:http http
```

或者完全不信任外来的请求

```
nat deny_incoming yes
```

#### 28.2.1.4. 最后的系统配置

现在您已配置了`ppp`， 但在真正工作之前还有一些事情要做。 即修改 `/etc/rc.conf`。

从上依次往下看， 确认已经正确地配置了 `hostname=`， 例如：

```
hostname="foo.example.com"
```

如果您的 ISP 提供给您一个静态的 IP 和名字， 将这个名字设为 hostname 是最合适的。

寻找 `network_interfaces` 变量。 如果要配置系统通过拨号连入 ISP， 一定要将`tun0`设备加入这个列表， 否则就删除它。

```
network_interfaces="lo0 tun0"
ifconfig_tun0=
```

### 注意:

`ifconfig_tun0`变量应该是空的， 且要创建一个名为 `/etc/start_if.tun0`的文件。 这个文件应该包含这一行：

```
ppp -auto mysystem
```

此脚本在网络配置时被执行， 开启 PPP 守护进程进入自动模式。 如果这台机子充当一个 LAN 的网关， 您可能希望使用 `-alias`。 参考相关联机手册了解更多细节。

务必在 `/etc/rc.conf` 中， 把路由程序设置为 `NO`：

```
router_enable="NO"
```

不启动 `routed` 服务程序非常重要， 因为 `routed` 总会删掉由 `ppp` 所建立的默认路由。

此外， 我们建议您确认一下 `sendmail_flags` 这一行中没有指定 `-q` 参数， 否则 `sendmail` 将会不断地尝试查找网络， 而这样做将会导致机器不断地进行拨号。 可以考虑：

```
sendmail_flags="-bd"
```

替代的做法是当每次 PPP 连接建立时您必须通过键入以下命令强制 `sendmail` 重新检查邮件队列：

```
# `/usr/sbin/sendmail -q`
```

您也可以在`ppp.linkup`使用`!bg`命令自动完成这些工作：

```
1     provider:
2       delete ALL
3       add 0 0 HISADDR
4       !bg sendmail -bd -q30m
```

如果您不喜欢这样做， 可以设立一个 “dfilter” 以阻止 SMTP 传输。 参考相关文件了解更多细节。

现在您唯一要做的事是重新启动计算机。 重启之后，可以输入：

```
# `ppp`
```

然后是`dial provider`以开启 PPP 会话。 或者如果您想让`ppp`自动建立会话， 因为您有一条广域网连接 (且没有创建 `start_if.tun0` 脚本)， 键入：

```
# `ppp -auto provider`
```

#### 28.2.1.5. 总结

当第一次设置 PPP 时， 下面几步是必须的：

客户端：

1.  确保 `tun`编译进了进核。

2.  确保 `/dev` 目录中名为 `tunN` 的设备文件是可用的。

3.  在 `/etc/ppp/ppp.conf`中创建一个项。 `pmdemand`示例应该适合于绝大多数 ISP。

4.  如果您使用动态 IP 地址， 在`/etc/ppp/ppp.linkup`创建一个项。

5.  更新`/etc/rc.conf` 文件。

6.  如果您要求按需拨号， 创建一个`start_if.tun0`脚本。

服务器端：

1.  确保`tun`设备已编译入内核。

2.  确保 `/dev` 目录中名为 `tunN` 的设备文件是可用的。

3.  在`/etc/passwd`中创建一个项 (使用[vipw(8)](http://www.FreeBSD.org/cgi/man.cgi?query=vipw&sektion=8&manpath=freebsd-release-ports)程序)。

4.  在用户的 home 目录创建一个运行 `ppp -direct direct-server`或相似命令的 profile。

5.  在`/etc/ppp/ppp.conf`中创建一个项。 `direct-server`示例应该能满足要求。

6.  在 `/etc/ppp/ppp.linkup`中创建一个项。

7.  更新 `/etc/rc.conf` 文件。

## 28.3. 使用内核级 PPP

Parts originally contributed by Gennady B. Sorokopud 和 Robert Huff.

### 警告:

这节内容只在 FreeBSD 7.X 上可用。

### 28.3.1. 设立内核级 PPP

在开始配置 PPP 之前， 请确认 `pppd` 已经存放在 `/usr/sbin` 中， 并且 `/etc/ppp` 目录是存在的。

`pppd`能在两种模式下工作：

1.  作为一个 “客户” ── 您要通过 PPP 串行线或 modem 线把您的机器连接到互联网上。

2.  作为“服务器” ──计算机已经位于网络上， 且被用于通过 PPP 与其它计算机连接。

两种情况您都需要设立一个选项文件， (`/etc/ppp/options` 或者是 `~/.ppprc` 如果您的计算机有多个用户使用 PPP)。

您还需要一些 modem/serial 软件([comms/kermit](http://www.freebsd.org/cgi/url.cgi?ports/comms/kermit/pkg-descr)就很适合)， 使您能够拨号并与远程主机建立连接。

### 28.3.2. 使用`pppd`作为客户端

Based on information provided by Trev Roydhouse.

下面这个 `/etc/ppp/options`选项文件能够被用来与 CISCO 终端服务器的 PPP 线连接。

```
crtscts         # enable hardware flow control
modem           # modem control line
noipdefault     # remote PPP server must supply your IP address
                # if the remote host does not send your IP during IPCP
                # negotiation, remove this option
passive         # wait for LCP packets
domain ppp.foo.com      # put your domain name here

:*`remote_ip`*    # put the IP of remote PPP host here
                # it will be used to route packets via PPP link
                # if you didn't specified the noipdefault option
                # change this line to *`local_ip`*:*`remote_ip`*

defaultroute    # put this if you want that PPP server will be your
                # default router
```

连接：

1.  使用 Kermit (或其他 modem 程序来拨号)， 然后输入您的用户名和口令 (或在远程主机上启用 PPP 所需的其他信息)。

2.  退出 Kermit (并不挂断连接)。

3.  键入下面这行：

    ```
    # `/usr/sbin/pppd /dev/tty01 19200`
    ```

    一定要使用正确的速度和设备名。

现在您的计算机已经用 PPP 连接。 如果连接失败， 您可在文件 `/etc/ppp/options` 中添加 `debug` 选项， 并查看控制台信息以跟踪问题。

下面这个`/etc/ppp/pppup`脚本能自动完成这三个步骤：

```
#!/bin/sh
pgrep -l pppd
pid=`pgrep pppd`
if [ "X${pid}" != "X" ] ; then
        echo 'killing pppd, PID=' ${pid}
        kill ${pid}
fi
pgrep -l kermit
pid=`pgrep kermit`
if [ "X${pid}" != "X" ] ; then
        echo 'killing kermit, PID=' ${pid}
        kill -9 ${pid}
fi

ifconfig ppp0 down
ifconfig ppp0 delete

kermit -y /etc/ppp/kermit.dial
pppd /dev/tty01 19200
```

`/etc/ppp/kermit.dial` 是一个 Kermit 脚本， 它会完成拨号， 并在远程主机上完成所有需要的身份验证过程 (这份文档的最后有一个脚本实例)。

使用下面这个脚本`/etc/ppp/pppdown`断开 PPP 连线：

```
#!/bin/sh
pid=`pgrep pppd`
if [ X${pid} != "X" ] ; then
        echo 'killing pppd, PID=' ${pid}
        kill -TERM ${pid}
fi

pgrep -l kermit
pid=`pgrep kermit`
if [ "X${pid}" != "X" ] ; then
        echo 'killing kermit, PID=' ${pid}
        kill -9 ${pid}
fi

/sbin/ifconfig ppp0 down
/sbin/ifconfig ppp0 delete
kermit -y /etc/ppp/kermit.hup
/etc/ppp/ppptest
```

通过执行`/usr/etc/ppp/ppptest`， 看看`pppd` 是否仍在运行：

```
#!/bin/sh
pid=`pgrep pppd`
if [ X${pid} != "X" ] ; then
        echo 'pppd running: PID=' ${pid-NONE}
else
        echo 'No pppd running.'
fi
set -x
netstat -n -I ppp0
ifconfig ppp0
```

执行脚本 `/etc/ppp/kermit.hup`以挂起 moderm， 这个文件包含：

```
set line /dev/tty01	; put your modem device here
set speed 19200
set file type binary
set file names literal
set win 8
set rec pack 1024
set send pack 1024
set block 3
set term bytesize 8
set command bytesize 8
set flow none

pau 1
out +++
inp 5 OK
out ATH0\13
echo \13
exit
```

也可以用`chat` 代替`kermit`：

以下两个文件用以建立`pppd`连接。

`/etc/ppp/options`：

```
/dev/cuad1 115200

crtscts		# enable hardware flow control
modem		# modem control line
connect "/usr/bin/chat -f /etc/ppp/login.chat.script"
noipdefault	# remote PPP serve must supply your IP address
	        # if the remote host doesn't send your IP during
                # IPCP negotiation, remove this option
passive         # wait for LCP packets
domain *`your.domain`*	# put your domain name here

:		# put the IP of remote PPP host here
	        # it will be used to route packets via PPP link
                # if you didn't specified the noipdefault option
                # change this line to *`local_ip`*:*`remote_ip`*

defaultroute	# put this if you want that PPP server will be
	        # your default router
```

`/etc/ppp/login.chat.script`：

### 注意:

以下的内容应该放在一行内。

```
ABORT BUSY ABORT 'NO CARRIER' "" AT OK ATDT*`phone.number`*
  CONNECT "" TIMEOUT 10 ogin:-\\r-ogin: *`login-id`*
  TIMEOUT 5 sword: *`password`*
```

一旦这些被安装且修改正确， 您所要做的就是运行`pppd`， 就像这样：

```
# `pppd`
```

### 28.3.3. 使用`pppd`作为服务器

`/etc/ppp/options`要包括下面这些内容：

```
crtscts                         # Hardware flow control
netmask 255.255.255.0           # netmask (not required)
192.114.208.20:192.114.208.165  # IP's of local and remote hosts
                                # local ip must be different from one
                                # you assigned to the Ethernet (or other)
                                # interface on your machine.
                                # remote IP is IP address that will be
                                # assigned to the remote machine
domain ppp.foo.com              # your domain
passive                         # wait for LCP
modem                           # modem line
```

下面这个脚本`/etc/ppp/pppserv` 使 pppd 以服务器方式启动：

```
#!/bin/sh
pgrep -l pppd
pid=`pgrep pppd`
if [ "X${pid}" != "X" ] ; then
        echo 'killing pppd, PID=' ${pid}
        kill ${pid}
fi
pgrep -l kermit
pid=`pgrep kermit`
if [ "X${pid}" != "X" ] ; then
        echo 'killing kermit, PID=' ${pid}
        kill -9 ${pid}
fi

# reset ppp interface
ifconfig ppp0 down
ifconfig ppp0 delete

# enable autoanswer mode
kermit -y /etc/ppp/kermit.ans

# run ppp
pppd /dev/tty01 19200
```

使用脚本`/etc/ppp/pppservdown`停止服务器：

```
#!/bin/sh
pgrep -l pppd
pid=`pgrep pppd`
if [ "X${pid}" != "X" ] ; then
        echo 'killing pppd, PID=' ${pid}
        kill ${pid}
fi
pgrep -l kermit
pid=`pgrep kermit`
if [ "X${pid}" != "X" ] ; then
        echo 'killing kermit, PID=' ${pid}
        kill -9 ${pid}
fi
ifconfig ppp0 down
ifconfig ppp0 delete

kermit -y /etc/ppp/kermit.noans
```

下面的 Kermit 脚本 (`/etc/ppp/kermit.ans`) 能够启用/禁用您 modem 的自动应答模式。 其内容类似下面这样：

```
set line /dev/tty01
set speed 19200
set file type binary
set file names literal
set win 8
set rec pack 1024
set send pack 1024
set block 3
set term bytesize 8
set command bytesize 8
set flow none

pau 1
out +++
inp 5 OK
out ATH0\13
inp 5 OK
echo \13
out ATS0=1\13   ; change this to out ATS0=0\13 if you want to disable
                ; autoanswer mode
inp 5 OK
echo \13
exit
```

一个名为`/etc/ppp/kermit.dial`的脚本用于向远程主机 进行拨号和验证。 您要根据需要定制它。 要加入您的登寻名和密码， 您还要根据 modem 和远程主机的反应修改输入语句。

```
;
; put the com line attached to the modem here:
;
set line /dev/tty01
;
; put the modem speed here:
;
set speed 19200
set file type binary            ; full 8 bit file xfer
set file names literal
set win 8
set rec pack 1024
set send pack 1024
set block 3
set term bytesize 8
set command bytesize 8
set flow none
set modem hayes
set dial hangup off
set carrier auto                ; Then SET CARRIER if necessary,
set dial display on             ; Then SET DIAL if necessary,
set input echo on
set input timeout proceed
set input case ignore
def \%x 0                       ; login prompt counter
goto slhup

:slcmd                          ; put the modem in command mode
echo Put the modem in command mode.
clear                           ; Clear unread characters from input buffer
pause 1
output +++                      ; hayes escape sequence
input 1 OK\13\10                ; wait for OK
if success goto slhup
output \13
pause 1
output at\13
input 1 OK\13\10
if fail goto slcmd              ; if modem doesn't answer OK, try again

:slhup                          ; hang up the phone
clear                           ; Clear unread characters from input buffer
pause 1
echo Hanging up the phone.
output ath0\13                  ; hayes command for on hook
input 2 OK\13\10
if fail goto slcmd              ; if no OK answer, put modem in command mode

:sldial                         ; dial the number
pause 1
echo Dialing.
output atdt9,550311\13\10               ; put phone number here
assign \%x 0                    ; zero the time counter

:look
clear                           ; Clear unread characters from input buffer
increment \%x                   ; Count the seconds
input 1 {CONNECT }
if success goto sllogin
reinput 1 {NO CARRIER\13\10}
if success goto sldial
reinput 1 {NO DIALTONE\13\10}
if success goto slnodial
reinput 1 {\255}
if success goto slhup
reinput 1 {\127}
if success goto slhup
if < \%x 60 goto look
else goto slhup

:sllogin                        ; login
assign \%x 0                    ; zero the time counter
pause 1
echo Looking for login prompt.

:slloop
increment \%x                   ; Count the seconds
clear                           ; Clear unread characters from input buffer
output \13
;
; put your expected login prompt here:
;
input 1 {Username: }
if success goto sluid
reinput 1 {\255}
if success goto slhup
reinput 1 {\127}
if success goto slhup
if < \%x 10 goto slloop         ; try 10 times to get a login prompt
else goto slhup                 ; hang up and start again if 10 failures

:sluid
;
; put your userid here:
;
output ppp-login\13
input 1 {Password: }
;
; put your password here:
;
output ppp-password\13
input 1 {Entering SLIP mode.}
echo
quit

:slnodial
echo \7No dialtone.  Check the telephone line!\7
exit 1

; local variables:
; mode: csh
; comment-start: "; "
; comment-start-skip: "; "
; end:
```

## 28.4. PPP 连接故障排除

Contributed by Tom Rhodes.

### 警告:

从 FreeBSD 8.0 开始， [uart(4)](http://www.FreeBSD.org/cgi/man.cgi?query=uart&sektion=4&manpath=freebsd-release-ports) 驱动取代了 [sio(4)](http://www.FreeBSD.org/cgi/man.cgi?query=sio&sektion=4&manpath=freebsd-release-ports) 驱动。 用以表示串口的设备节点由分别 `/dev/cuadN` 改为了 `/dev/cuauN`， 并从 `/dev/ttydN` 改为了 `/dev/ttyuN`。 FreeBSD 7.X 用户在升级时需要因应之对配置文件进行必要的更改。

本节将讲述通过 modem 连接使用 PPP 时可能出现的问题。 例如， 您可能需要确切地知道您拨入的系统会出现一个怎样的命令行提示符。 有些 ISP 会提供 `ssword`提示符， 而其它的可能会出现 `password`； 如果没有根据情况的不同相应地编写 `ppp` 脚本， 登录就会失败。 诊断 `ppp` 最常用的方法是手动进行连接。 以下的信息会一步一步地带您完成手动连接。

### 28.4.1. 检查设备节点

如果使用的是定制内核， 确认在其编译配置中包含下列配置：

```
device   uart
```

默认的 `GENERIC` 内核中包含了 `uart` 设备， 因此如果您使用的是它的话， 就不需要担心了。 只要查看 `dmesg` 输出中是否有 modem 设备：

```
# `dmesg | grep uart`
```

您应该找到与 `uart` 设备有关的输出。 这些就是我们需要的 COM 端口。 如果您的 modem 按照标准串行端口工作， 您就会在 `uart1` 或 `COM2` 上找到它。 如果 modem 设备连接在 `uart1` 接口 (在 DOS 中称为`COM2`)， 那么您的 modem 将会是 `/dev/cuau1`。

### 28.4.2. 手动连接

通过手动控制`ppp`来连接 Internet 是诊断连接及获知 ISP 处理 PPP 客户端方式的一个快速， 简单的方法。 让我们从 PPP 命令行开始， 在所有的例子中我们使用 *example* 表示运行 PPP 服务的主机名。 键入`ppp` 命令打开 `ppp`：

```
# `ppp`
```

现在我们已经打开了`ppp`。

```
ppp ON example> `set device /dev/cuau1`
```

设置 modem 设备， 在本例子中是 `cuau1`。

```
ppp ON example> `set speed 115200`
```

设置连接速度， 在本例中我们使用 15,200 kbps。

```
ppp ON example> `enable dns`
```

使`ppp`配置域名服务， 在文件`/etc/resolv.conf`中添加域名服务器行。 如果 `ppp`不能确定我们的主机名， 可以在稍后设置。

```
ppp ON example> `term`
```

切换到 “终端”样我们就能手动地控制这台 modem 的模式。

```
deflink: Entering terminal mode on /dev/cuau1
type '~h' for help
```

```
`at`
OK
`atdt123456789`
```

使用命令`at`初始化 modem， 然后使用`atdt`和 ISP 给您的号码进行拨号。

```
CONNECT
```

连接配置， 如果我们遇到了与硬件无关的连接问题， 可以在这里尝试解决。

```
ISP Login:`myusername`
```

这里提示您输入用户名， 输入 ISP 提供的用户名然后按回车。

```
ISP Pass:`mypassword`
```

这时提示我们输入密码， 输入 ISP 提供的密码。 如同登录入 FreeBSD， 密码不会显示。

```
Shell or PPP:`ppp`
```

由于 ISP 的不同， 这个提示符可能不会出现。 这里我们需要考虑： 是使用运行于提供商端的 Shell， 还是启动 `ppp`？ 这本例中， 我们选择使用 `ppp`， 因为我们希望得到 Internet 连接。

```
Ppp ON example>
```

注意在这个例子中， 第一个 `p`已经大写。 这表示我们已经成功地连接上了 ISP。

```
PPp ON example>
```

我们已经成功通过了 ISP 的验证， 正在等待分配 IP 地址。

```
PPP ON example>
```

我们得到了一个 IP 地址， 成功地完成了连接。

```
PPP ON example>`add default HISADDR`
```

这样就完成了添加默认路由所需的配置。 这是与外界通信所必需的。 因为之前我们只是与服务器端建立了连接。 如果由于已存在的路由而导致操作失败， 您可以在 `add` 前加 `!`号。 除此之外， 您也可以在真正连接之前设置这些 (指 add default HISADDR)， ppp 会根据这项设定协商取得新的路由。

如果一切顺利， 现在我们应该能得到一个活动的 Internet 连接， 可以使用 **CTRL**+**z** 使其转入后台。 如果您发现 `PPP`重新变为 `ppp`， 则表示连接被断开。 大写的 P 表明建立了到 ISP 的连接， 而小写的 p 则表示连接由于某种原因被断开， 这有助于帮助我们了解连接的状态。 `ppp` 只有这两个状态。

#### 28.4.2.1. 诊断排错

如果您有一根直连线且似乎不能建立连接， 要使用`set ctsrts off`以关闭字节流的 CTS/RTS。 这种情况一般发生在连接兼容 PPP 的终端服务器时。 当它向通信连接写入数据时， PPP 就会挂起， 一直等待一个 CTS， 或者一个不可能出现的 Clear to Send 信号。 如果使用了这个选项， 您还应使用 `set accmap`选项， 某些存在缺陷的硬件在完成端对端发送特定字符， 特别是 XON/XOFF 时可能会遇到困难。 请参见 [ppp(8)](http://www.FreeBSD.org/cgi/man.cgi?query=ppp&sektion=8&manpath=freebsd-release-ports) 联机手册以了解关于可用选项的更多细节， 以及如何使用它们。

如果您的 modem 比较旧， 就需要使用 `set parity even` 了。 奇偶校验的默认设置是 none， 但在旧式的 (当流量大量增加时) 调制解调器和某些 ISP 被用来纠错。 您需要使用这个选项才能使用 Compuserve ISP。

PPP 可能并不返回命令模式， 这通常是 ISP 等待您这一端发起协商时发生了错误。 此时， 使用 `~p` 命令将强制 ppp 开始发送配置信息。

如果您没有看到登录提示， 则很可能需要使用 PAP 或 CHAP 验证来代替前面例子中的 UNIX® 风格验证。 要使用 PAP 或 CHAP 只需在进入终端模式之前把下面的选项加入 PPP：

```
ppp ON example> `set authname myusername`
```

此处 *`myusername`* 应改为您的 ISP 分配给您的用户名。

```
ppp ON example> `set authkey mypassword`
```

此处 *`mypassword`* 应该为您的 ISP 分配给您的口令。

如果连接正常， 但无法查找域名， 请尝试 [ping(8)](http://www.FreeBSD.org/cgi/man.cgi?query=ping&sektion=8&manpath=freebsd-release-ports) 某个 IP 地址来看看是否返回了信息。 如果您发现百分之百 (100%) 丢包， 那么您很可能没有分配默认路由。 请仔细检查选项 `add default HISADDR` 是否在连接时被设置了。 如果您能连接到远程的 IP 地址则有可能域名解析服务器的地址没有被加入到 `/etc/resolv.conf`。 这个文件应该是下面的样子：

```
domain *`example.com`*
nameserver *`x.x.x.x`*
nameserver *`y.y.y.y`*
```

此处 *`x.x.x.x`* 和 *`y.y.y.y`* 应该改为您的 ISP 的 DNS 服务器的 IP 地址。 这一信息在您注册时可能会提供给您， 不过通常只需给 ISP 打个电话就能知道了。

您还可以让 [syslog(3)](http://www.FreeBSD.org/cgi/man.cgi?query=syslog&sektion=3&manpath=freebsd-release-ports) 为您的 PPP 连接提供日志。 只需增加：

```
!ppp
*.*     /var/log/ppp.log
```

到 `/etc/syslog.conf` 中。 绝大多数情况下， 这个功能默认已经打开了。

## 28.5. 使用基于以太网的 PPP(PPPoE)

Contributed (from http://node.to/freebsd/how-tos/how-to-freebsd-pppoe.html) by Jim Mock.

本节将介绍如何建立基于以太网的 PPP (PPPoE)。

### 28.5.1. 配置内核

对于 PPPOE， 并没有必须的内核配置。 如果必需的 netgraph 支持没有编译入内核， 它可以由 ppp 动态加载。

### 28.5.2. 设置`ppp.conf`

以下是一个`ppp.conf`的例子：

```
default:
  set log Phase tun command # you can add more detailed logging if you wish
  set ifaddr 10.0.0.1/0 10.0.0.2/0

name_of_service_provider:
  set device PPPoE:*`xl1`* # replace xl1 with your Ethernet device
  set authname YOURLOGINNAME
  set authkey YOURPASSWORD
  set dial
  set login
  add default HISADDR
```

### 28.5.3. 运行 ppp

以 `root` 身份执行：

```
# `ppp -ddial name_of_service_provider`
```

### 28.5.4. 启动时运行 ppp

在 `/etc/rc.conf` 中加入以下内容：

```
ppp_enable="YES"
ppp_mode="ddial"
ppp_nat="YES"	# if you want to enable nat for your local network, otherwise NO
ppp_profile="name_of_service_provider"
```

### 28.5.5. 使用 PPPoE 服务标签

在某些时候， 有必要使用一个服务标签来建立您的连接。 服务标签用于区分同一网络中的不同服务器。

您可以在 ISP 提供的文档中找到必要的服务标签信息。 若不能找到， 则应向您的 ISP 寻求技术支持。

作为最后的方法， 您可以试试 [Roaring Penguin PPPoE](http://www.roaringpenguin.com/pppoe/)， 它可以在 Ports Collection 中找到。 然而需要注意的是， 它可能会清楚 modem 的固件， 并使其无法正常工作， 因此一定要仔细考虑之后再做这个操作。 简单地安装由服务提供商随 modem 提供的程序。 随后， 选择 System 菜单。 您的配置文件应该会在这里列出。 一般来说它的名字应该是 *ISP*。

配置文件名 (service tag， 服务标签) 将被用于 PPPoE 在 `ppp.conf` 中的配置项， 作为服务商 `set device` 命令的一部分 (参见 [ppp(8)](http://www.FreeBSD.org/cgi/man.cgi?query=ppp&sektion=8&manpath=freebsd-release-ports) 联机手册以了解更多细节)。 它应该类似下面的样子：

```
set device PPPoE:*`xl1`*:*`ISP`*
```

记住将*`xl1`*换成实际的以太网设备。

记住将 *`ISP`* 换成您刚刚找到的 profile 名。

获得更多的信息， 请参考：

*   [Cheaper Broadband with FreeBSD on DSL](http://renaud.waldura.com/doc/freebsd/pppoe/) by Renaud Waldura.

*   [Nutzung von T-DSL und T-Online mit FreeBSD](http://www.ruhr.de/home/nathan/FreeBSD/tdsl-freebsd.html) by Udo Erdelhoff (in German).

### 28.5.6. 带有一个 3Com® HomeConnect® ADSL Modem 的 PPPOE 双重连接

这个 modem 不遵循 [RFC 2516](http://www.faqs.org/rfcs/rfc2516.html) (*A Method for transmitting PPP over Ethernet (PPPoE)*， 其作者为 L. Mamakos、 K. Lidl、 J. Evarts、 D. Carrel、 D. Simone 以及 R. Wheeler)。 而是使用不同的数据包格式作为以太网的框架。 请向 [3Com](http://www.3com.com/) 抱怨， 如果您认为它应该遵守 PPPoE 的规范。

为了让 FreeBSD 能够与这个设备通信， 必须设置 sysctl。 通过更改`/etc/sysctl.conf`， 这一步可以在启动时自动完成：

```
net.graph.nonstandard_pppoe=1
```

或者， 也可以直接执行下面的命令：

```
# `sysctl net.graph.nonstandard_pppoe=1`
```

很不幸，由于这是系统全局设置， 无法同时与正常的 PPP 客户端(或服务器) 和 3Com®HomeConnect® ADSL Modem 通信。

## 28.6. 使用 ATM 上的 PPP (PPPoA)

以下将介绍如何设置基于 ATM 的 PPP(PPPoA)。 PPPoA 是欧洲 DSL 提供商的普遍选择。

### 28.6.1. 使用 Alcatel SpeedTouch™USB 的 PPPoA

针对这一设备的 PPPoA 支持， 在 FreeBSD 中是作为 port 提供的， 因为其固件使用了 [阿尔卡特许可协议](http://www.speedtouchdsl.com/disclaimer_lx.htm)， 因而不能与 FreeBSD 的基本系统一起免费地再发布。

使用 Ports 套件 可以非常方便地安装 [net/pppoa](http://www.freebsd.org/cgi/url.cgi?ports/net/pppoa/pkg-descr) port， 之后按照它提供的指示操作就可以了。

和许多 USB 设备类似， 阿尔卡特的 SpeedTouch™ USB 需要从主机上下载固件才能够正常工作。 在 FreeBSD 中您可以将此操作自动化， 在有设备插到某个 USB 口的时候自动下载固件。 可以在 `/etc/usbd.conf` 文件中加入下面的信息来让它自动完成固件的传送。 注意， 必须以 `root` 用户的身份编辑它。

```
device "Alcatel SpeedTouch USB"
    devname "ugen[0-9]+"
    vendor 0x06b9
    product 0x4061
    attach "/usr/local/sbin/modem_run -f /usr/local/libdata/mgmt.o"
```

要启动 USB 守护进程 usbd， 在`/etc/rc.conf`加入以下行：

```
usbd_enable="YES"
```

也可以将 ppp 设置成启动时拨号。 向 `/etc/rc.conf`加入以下这几行。 同样地您需要以`root`用户登录。

```
ppp_enable="YES"
ppp_mode="ddial"
ppp_profile="adsl"
```

为了使其正常工作， 您需要使用[net/pppoa](http://www.freebsd.org/cgi/url.cgi?ports/net/pppoa/pkg-descr) port 提供的`ppp.conf`样例。

### 28.6.2. 使用 mpd

可以使用 mpd 来连接多种类型的服务， 特别是 PPTP 服务。 您可以在 Ports Collection 中找到 mpd， 它的位置是 [net/mpd](http://www.freebsd.org/cgi/url.cgi?ports/net/mpd/pkg-descr)。 许多 ADSL modem 需要在 modem 和计算机之间建立一条 PPTP 隧道， 而阿尔卡特 SpeedTouch™ Home 正是其中的一种。

首先需要从 port 完成安装， 然后才能配置 mpd 来满足您的需要， 并完成服务商的配置。 port 会把一系列包括了详细注解的配置文件实例放到 `PREFIX/etc/mpd/`。 注意， 这里的 *`PREFIX`* 表示 ports 安装的目录， 默认情况下， 应该是 `/usr/local/`。 关于配置 mpd 的完整说明， 会以 HTML 格式随 port 一起安装。 这些文件将放在 `PREFIX/share/doc/mpd/`。 下面是通过 mpd 连接 ADSL 服务的一个简单例子。 配置被分别放到了两个文件中， 第一个是 `mpd.conf`：

```
default:
    load adsl

adsl:
    new -i ng0 adsl adsl
    set bundle authname *`username`* ![1](img/1.png)
    set bundle password *`password`* ![2](img/2.png)
    set bundle disable multilink

    set link no pap acfcomp protocomp
    set link disable chap
    set link accept chap
    set link keep-alive 30 10

    set ipcp no vjcomp
    set ipcp ranges 0.0.0.0/0 0.0.0.0/0

    set iface route default
    set iface disable on-demand
    set iface enable proxy-arp
    set iface idle 0

    open
```

| ![1](img/#co-mpd-ex-user) | username 用来向您的 ISP 进行验证。 |
| ![2](img/#co-mpd-ex-pass) | password 用来向您的 ISP 进行验证。 |

`mpd.links`包含连接的信息：

```
adsl:
    set link type pptp
    set pptp mode active
    set pptp enable originate outcall
    set pptp self *`10.0.0.1`* ![1](img/1.png)
    set pptp peer *`10.0.0.138`* ![2](img/2.png)
```

| ![1](img/#co-mpd-ex-self) | 运行 mpd 的主机的 IP 地址。 |
| ![2](img/#co-mpd-ex-peer) | ADSL modem 的 IP 地址。 Alcatel SpeedTouch™ Home 默认的是 `10.0.0.138`。 |

初始化连接：

```
# `mpd -b adsl`
```

您可以通过以下命令查看连接状态：

```
% `ifconfig ng0`
ng0: flags=88d1<UP,POINTOPOINT,RUNNING,NOARP,SIMPLEX,MULTICAST> mtu 1500
     inet 216.136.204.117 --> 204.152.186.171 netmask 0xffffffff
```

使用 mpd 连接 ADSL 服务是推荐的方式。

### 28.6.3. 使用 pptpclient

也可以使用[net/pptpclient](http://www.freebsd.org/cgi/url.cgi?ports/net/pptpclient/pkg-descr)连接其它的 PPPoA。

要使用 [net/pptpclient](http://www.freebsd.org/cgi/url.cgi?ports/net/pptpclient/pkg-descr) 连接 DSL 服务， 需要安装 port 或 package 并编辑 `/etc/ppp/ppp.conf`。 您需要有 `root` 权限才能完成这两项操作。 以下是 `ppp.conf` 中的一个示例项。 参考 ppp 的联机手册 [ppp(8)](http://www.FreeBSD.org/cgi/man.cgi?query=ppp&sektion=8&manpath=freebsd-release-ports)， 以了解更多有关 `ppp.conf` 选项的信息。

```
adsl:
 set log phase chat lcp ipcp ccp tun command
 set timeout 0
 enable dns
 set authname *`username`* ![1](img/1.png)
 set authkey *`password`* ![2](img/2.png)
 set ifaddr 0 0
 add default HISADDR
```

| ![1](img/#co-pptp-ex-user) | 您在 DSL 服务提供商那里的用户名 |
| ![2](img/#co-pptp-ex-pass) | 您帐户的口令。 |

### 警告:

由于您必须将帐号密码以明文的方式放入`ppp.conf` 您应该确保没有任何人能看到此文件的内容。 以下一系列命令将会确保此文件只对 `root`用户可读。 请参见 [chmod(1)](http://www.FreeBSD.org/cgi/man.cgi?query=chmod&sektion=1&manpath=freebsd-release-ports) 和 [chown(8)](http://www.FreeBSD.org/cgi/man.cgi?query=chown&sektion=8&manpath=freebsd-release-ports) 的联机手册以了解有关如何操作的进一步信息。

```
# `chown root:wheel /etc/ppp/ppp.conf`
# `chmod 600 /etc/ppp/ppp.conf`
```

以下将为到 DSL 路由器的会话打开一个 tunnel。 以太网 DSL modem 有一个设置的局域网 IP 地址。 以 Alcatel SpeedTouch™ Home 为例， 这个地址是 `10.0.0.138`。 路由器的文档应该会告诉您它使用的地址。 执行以下命令以打开 tunnel 并开始会话：

```
# `pptp address adsl`
```

### 提示:

您应该在命令的最后加上(“&”)号， 否则 pptp 无法返回到命令行提示符。

要创建一个 `tun`虚拟设备用于进程 pptp 和 ppp 之间的交互。 一旦您回到了命令行， 或者 pptp 进程确认了一个连接， 您可以这样检查 tunnel 设备：

```
% `ifconfig tun0`
tun0: flags=8051<UP,POINTOPOINT,RUNNING,MULTICAST> mtu 1500
        inet 216.136.204.21 --> 204.152.186.171 netmask 0xffffff00
        Opened by PID 918
```

如果您无法连接， 一般可以通过 telnet 或者 web 浏览器检查路由器(modem)的配置。 如果依旧无法连接， 您应该检查`pptp`的输出及 ppp 的日志文件 `/var/log/ppp.log` 以获得线索。

## 28.7. 使用 SLIP

Originally contributed by Satoshi Asami.With input from Guy Helmer 和 Piero Serini.

### 警告:

这节内容只在 FreeBSD 7.X 上可用。

### 28.7.1. 设置 SLIP 客户端

下面是在静态主机网络上配置 FreeBSD 机器使用 SLIP 的方法。 对于动态主机名分配 (您的地址会随每次拨号而不同)， 您可能需要稍复杂一些的设置。

首先， 您需要确认调制解调器所连接的串口。 许多人会设置一个符号连接， 例如 `/dev/modem`， 用以指向实际的设备名， 如 `/dev/cuadN`。 这样您就可以对实际的设备名进行抽象， 以备调制解调器换到其他串口时方便调整之用。 不然， 修改 `/etc` 和遍布于系统中的 `.kermrc` 文件将是一件很麻烦的事情！

### 注意:

`/dev/cuad0` 对应 `COM1`， 而 `/dev/cuad1` 则对应 `COM2`， 等等。

确保您的内核文件包含以下内容：

```
device   sl
```

这包含在`GENERIC`内核， 所以这应该不会是个问题， 除非您 已经删除了它。

#### 28.7.1.1. 只需做一次的事情

1.  把您本地网络上的机器、 网关以及域名服务器， 都加入到 `/etc/hosts` 文件中。 我们的是下面这个样子：

    ```
    127.0.0.1               localhost loghost
    136.152.64.181          water.CS.Example.EDU water.CS water
    136.152.64.1            inr-3.CS.Example.EDU inr-3 slip-gateway
    128.32.136.9            ns1.Example.EDU ns1
    128.32.136.12           ns2.Example.EDU ns2
    ```

2.  请确保在您的 `/etc/nsswitch.conf` 中的 `hosts:` 小节里面， `files` 先于 `dns` 出现。 如果不是这样的话， 可能会产生一些不希望的现象。

3.  编辑`/etc/rc.conf`。

    1.  编辑以下这行设置主机名(hostname)：

        ```
        hostname="myname.my.domain"
        ```

        应该用您主机的 Internet 全名代替。

    2.  改变这一行以指明默认的路由：

        ```
        defaultrouter="NO"
        ```

        改为：

        ```
        defaultrouter="slip-gateway"
        ```

4.  创建文件`/etc/resolv.conf`， 写入以下内容：

    ```
    domain CS.Example.EDU
    nameserver 128.32.136.9
    nameserver 128.32.136.12
    ```

    正如您看到的， 这些行设置了域名服务器。 当然， 实际的域名和 IP 地址取决于您的环境。

5.  设置`root`和 `toor`的密码(其它任何没有密码的帐号)。

6.  重启计算机， 然后确认使用了正确的主机名。

#### 28.7.1.2. 创建一个 SLIP 连接

1.  在命令提示符之后输入 `slip` 进行拨号， 输入您的机器名和口令。 具体需要输入什么， 与您的环境密切相关。 如果使用 Kermit， 则可以使用类似下面的脚本：

    ```
    # kermit setup
    set modem hayes
    set line /dev/modem
    set speed 115200
    set parity none
    set flow rts/cts
    set terminal bytesize 8
    set file type binary
    # The next macro will dial up and login
    define slip dial 643-9600, input 10 =>, if failure stop, -
    output slip\x0d, input 10 Username:, if failure stop, -
    output silvia\x0d, input 10 Password:, if failure stop, -
    output ***\x0d, echo \x0aCONNECTED\x0a
    ```

    当然， 您还需要修改用户名和口令来满足实际需要。 完成这些操作之后， 只需在 Kermit 提示符之后输入 `slip` 就可以连接了。

    ### 注意:

    将密码以纯文本的形式存放在文件系统无论如何都是个 *坏* 主意。 请考虑这样做的风险。

2.  在这里退出 Kermit (也可以用 **Ctrl**+**z** 将其挂起)， 以 `root` 用户键入：

    ```
    # `slattach -h -c -s 115200 /dev/modem`
    ```

    如果您能`ping`通路由器另一端的主机， 就是连接好了! 如果不行， 您可以使用`-a`选项代替 `-c`作为`slattach`的参数。

#### 28.7.1.3. 关闭连接

按下面的步骤做：

```
# `kill -INT `cat /var/run/slattach.modem.pid``
```

来杀掉 `slattach`。 切记上述操作只有以 `root` 身份才能完成。 接下来回到 `kermit` (如果之前是将它挂起了， 则使用 `fg`) 并退出 (**q**)。

在 [slattach(8)](http://www.FreeBSD.org/cgi/man.cgi?query=slattach&sektion=8&manpath=freebsd-release-ports) 联机手册中提到， 必须使用 `ifconfig sl0 down` 才能将接口标记为关闭， 但和这样做似乎没有什么区别。 (`ifconfig sl0` 仍然报告同样的东西。)

有时， 您的 modem 可能会拒绝挂断。 这种情况下， 只需重新启动 `kermit` 并再次退出它就可以了。 一般来说试二次就可以了。

#### 28.7.1.4. 问题解答

如果还不行， 尽管发邮件到 [freebsd-net](http://lists.FreeBSD.org/mailman/listinfo/freebsd-net) 邮件列表来提问。 常见的问题包括：

*   执行 `slattach` 时不使用 `-c`和`-a`选项 (这应该不是关键的， 但有些用户报告这样做解决了问题)。

*   使用`s10`替换 `sl0` (在一些字体下很难看出不同)。

*   试试`ifconfig sl0`来查看您的接口状态。 例如， 您可以这样做：

    ```
    # `ifconfig sl0`
    sl0: flags=10<POINTOPOINT>
            inet 136.152.64.181 --> 136.152.64.1 netmask ffffff00
    ```

*   如果在使用 [ping(8)](http://www.FreeBSD.org/cgi/man.cgi?query=ping&sektion=8&manpath=freebsd-release-ports) 时得到了 no route to host 这样的提示， 则说明您的路由表可能有问题。 可以用 `netstat -r` 命令来显示当前的路由：

    ```
    # `netstat -r`
    Routing tables
    Destination      Gateway            Flags     Refs     Use  IfaceMTU    Rtt    Netmasks:

    (root node)
    (root node)

    Route Tree for Protocol Family inet:
    (root node) =>
    default          inr-3.Example.EDU  UG          8   224515  sl0 -      -
    localhost.Exampl localhost.Example. UH          5    42127  lo0 -       0.438
    inr-3.Example.ED water.CS.Example.E UH          1        0  sl0 -      -
    water.CS.Example localhost.Example. UGH        34 47641234  lo0 -       0.438
    (root node)
    ```

    前述的例子来自于一个非常繁忙的系统。 您系统上的这些数字会因网络活动的不同而改变。

### 28.7.2. 设置 SLIP 服务器

本文提供了在 FreeBSD 上设置 SLIP 服务， 也就是如何配置您的系统， 使其能在远程 SLIP 客户端登录时自动地开启连接的建议。

#### 28.7.2.1. 前提条件

这一节技术性很强， 所以要求您有一定的背景知识。 本节假定您熟悉 TCP/IP 网络协议， 特别是网络和节点寻址、 子网掩码、 子网划分、 路由、 路由协议 (如 RIP) 等知识。 在拨号服务器上配置 SLIP 需要这些概念性的知识。 如果您不熟悉它们， 请先阅读 Craig Hunt 的 *TCP/IP 网络管理* 由 O'Reilly & Associates, Inc. 出版 (ISBN 0-937175-82-X)， 或 Douglas Comer 有关 TCP/IP 协议的书籍。

此外还假定您已经配置好了您的调制解调器以及相应的系统文件， 以允许通过调制解调器进行登录。 如果您还没有为此配置好系统， 请参见 第 27.4 节 “拨入服务” 以了解关于如何进行拨号服务的配置。 您可能也会想看一看 [sio(4)](http://www.FreeBSD.org/cgi/man.cgi?query=sio&sektion=4&manpath=freebsd-release-ports) 的联机手册， 以了解关于串口设备驱动的进一步信息， 以及 [ttys(5)](http://www.FreeBSD.org/cgi/man.cgi?query=ttys&sektion=5&manpath=freebsd-release-ports)、 [gettytab(5)](http://www.FreeBSD.org/cgi/man.cgi?query=gettytab&sektion=5&manpath=freebsd-release-ports)、 [getty(8)](http://www.FreeBSD.org/cgi/man.cgi?query=getty&sektion=8&manpath=freebsd-release-ports) & [init(8)](http://www.FreeBSD.org/cgi/man.cgi?query=init&sektion=8&manpath=freebsd-release-ports) 上关于怎样配置系统来接受来自调制解调器的登录请求的具体情况， 还有 [stty(1)](http://www.FreeBSD.org/cgi/man.cgi?query=stty&sektion=1&manpath=freebsd-release-ports) 以了解关于设置串口参数 (例如 `clocal` 表示串口直联) 等。

#### 28.7.2.2. 快速浏览

使用 FreeBSD 作为 SLIP 服务器， 在典型配置时， 它是这样工作的： 一个 SLIP 客户拨号并以专用的 login ID 登录到 FreeBSD SLIP 服务器系统。 这个用户使用 `/usr/sbin/sliplogin` 作为 shell。 `sliplogin` 程序会在文件 `/etc/sliphome/slip.hosts` 中查找这个用户的项， 如果找到了匹配项， 就将串行线连接到一个可用的 SLIP 接口， 然后运行 shell 脚本 `/etc/sliphome/slip.login` 以配置 SLIP 接口。

##### 28.7.2.2.1. 一个 SLIP 服务器登录的例子

例如， 如果一个 SLIP 用户的 ID 是`Shelmerg`， 在`/etc/master.passwd`中`Shelmerg`的项如下的所示：

```
Shelmerg:password:1964:89::0:0:Guy Helmer - SLIP:/usr/users/Shelmerg:/usr/sbin/sliplogin
```

`Shelmerg`登录时， `sliplogin`在文件 `/etc/sliphome/slip.hosts`中搜索与用户 ID 匹配的行;如下所示：

```
Shelmerg        dc-slip sl-helmer       0xfffffc00		  autocomp
```

`sliplogin`找到这条区配行， 并将串行线与另一个可用的 SLIP 接口连起来， 然后执行`/etc/sliphome/slip.login`脚本：

```
/etc/sliphome/slip.login 0 19200 Shelmerg dc-slip sl-helmer 0xfffffc00 autocomp
```

如果一切顺利 `/etc/sliphome/slip.login` 将在 `sliplogin` 绑定的 SLIP 接口上发出 `ifconfig` (前述的例子中是 SLIP 接口 0， 这是 `slip.login` 的第一个参数)， 以设置本地 IP 地址 (`dc-slip`)、 远程 IP 地址 (`sl-helmer`)、 这一 SLIP 接口的子网掩码 (`0xfffffc00`)， 以及任何其他标志 (`autocomp`)。 如果发生错误， `sliplogin` 通常会通过 syslogd 的 daemon facility 记下有用的信息， 前者会把这些信息保存到 `/var/log/messages` (参见 [syslogd(8)](http://www.FreeBSD.org/cgi/man.cgi?query=syslogd&sektion=8&manpath=freebsd-release-ports) 和 [syslog.conf(5)](http://www.FreeBSD.org/cgi/man.cgi?query=syslog.conf&sektion=5&manpath=freebsd-release-ports) 以及 `/etc/syslog.conf` 的联机手册， 以了解 syslogd 在记录什么， 以及这些内容将被记在哪里)。

#### 28.7.2.3. 内核配置

FreeBSD 的默认内核 (`GENERIC`) 提供了 SLIP ([sl(4)](http://www.FreeBSD.org/cgi/man.cgi?query=sl&sektion=4&manpath=freebsd-release-ports)) 支持； 使用定制的内核时， 您必须把下面的设置加入到配置文件：

```
device   sl
```

默认情况下， 您的 FreeBSD 计算机不会转发包。 如果您希望将 FreeBSD SLIP 服务器作为路由器使用， 就需要修改 `/etc/rc.conf` 文件， 将 `gateway_enable` 变量设为 `YES`。 这样下次系统引导时就能够保持这一配置了。

要立即应用这些配置， 可以 `root` 的身份运行：

```
# /etc/rc.d/routing start
```

请参阅 第 9 章 *配置 FreeBSD 的内核* 以了解如何配置 FreeBSD 内核， 并获得在重新配置内核方面的指导。

#### 28.7.2.4. Sliplogin 配置

正如先前所提到的， `/etc/sliphome` 目录中有三个文件， 它们共同构成 `/usr/sbin/sliplogin` 的配置 (参考 `sliplogin` 的联机手册 [sliplogin(8)](http://www.FreeBSD.org/cgi/man.cgi?query=sliplogin&sektion=8&manpath=freebsd-release-ports))： 用于定义 SLIP 用户和相关的 IP 地址的 `slip.hosts`、 通常仅用于配置 SLIP 接口的 `slip.login`， 以及 (可选的) `slip.logout`， 用以撤销由 `slip.login` 所执行的动作。

##### 28.7.2.4.1. 配置 `slip.hosts`

`/etc/sliphome/slip.hosts`里的每行包含至少四个元素， 元素之间由空格隔开：

*   SLIP 用户的登录 ID

*   SLIP 连接的本地地址(指 SLIP 服务器)

*   SLIP 连接的远程地址

*   网络掩网

本地和远程地址可以是主机名 (通过文件`/etc/hosts`或者域名服务解析为 IP 地址， 这取决于文件`/etc/nsswitch.conf` 中的设置)， 网络掩网可以是一个 能通过文件`/etc/networks`解析的名字。 在一个样例系统中， `/etc/sliphome/slip.hosts`是这样的：

```
#
# login local-addr      remote-addr     mask            opt1    opt2
#                                               (normal,compress,noicmp)
#
Shelmerg  dc-slip       sl-helmerg      0xfffffc00      autocomp
```

在这行末尾是一或多个选项：

*   `normal` ──不压缩报头

*   `compress` ── 压缩报头

*   `autocomp` ──如果远程端允许， 压缩报头

*   `noicmp` ──禁用 ICMP 数据包 (这样就会丢弃所有的“ping”数据包， 不占用您的带宽)

对 SLIP 连接的本地及远程地址的选择取决是您是准备在 SLIP 服务器上使用 TCP/IP 子网还是使用“ARP 代理” (它并不是“真正的”ARP 代理， 而是我们在本节用于介绍的术语)。 如果您不能确定选择何种方式或者如何分配地址， 请参考"前提条件"(第 28.7.2.1 节 “前提条件”)里列出的 TCP/IP 书籍 或者向您的 IP 网络管理员请教。

如果打算为您的 SLIP 客户使用一个独立的子网， 就需要先从分配得到的网络号中取出一个子网号， 然后再在这个子网里给每个 SLIP 客户分配 IP 地址。 接下来， 您还需要通过 SLIP 服务器在最近的 IP 路由器上配置一个指向 SLIP 子网的静态路由。

如果要使用 “代理 ARP” 的方式， 您还需要从 SLIP 服务器的以太子网中为每个 SLIP 客户分配 IP 地址， 还必须修改`/etc/sliphome/slip.login` 和 `/etc/sliphome/slip.logout`脚本以使用 [arp(8)](http://www.FreeBSD.org/cgi/man.cgi?query=arp&sektion=8&manpath=freebsd-release-ports)来管理在 SLIP 服务器 ARP 表中的 “代理 ARP” 项。

##### 28.7.2.4.2. `slip.login` Configuration

典型的`/etc/sliphome/slip.login` 如下所示：

```
#!/bin/sh -
#
#       @(#)slip.login  5.1 (Berkeley) 7/1/90

#
# generic login file for a slip line.  sliplogin invokes this with
# the parameters:
#      1        2         3        4          5         6     7-n
#   slipunit ttyspeed loginname local-addr remote-addr mask opt-args
#
/sbin/ifconfig sl$1 inet $4 $5 netmask $6
```

这个`slip.login`脚本仅仅为带有相应本地及远程地址和掩码的 SLIP 接口执行 `ifconfig`。

如果您决定使用“ARP 代理” 方式(而非为您的 SLIP 客户使用独立的子网)， 您的`/etc/sliphome/slip.login` 应该是这样：

```
#!/bin/sh -
#
#       @(#)slip.login  5.1 (Berkeley) 7/1/90

#
# generic login file for a slip line.  sliplogin invokes this with
# the parameters:
#      1        2         3        4          5         6     7-n
#   slipunit ttyspeed loginname local-addr remote-addr mask opt-args
#
/sbin/ifconfig sl$1 inet $4 $5 netmask $6
# Answer ARP requests for the SLIP client with our Ethernet addr
/usr/sbin/arp -s $5 00:11:22:33:44:55 pub
```

`slip.login`新加的行`arp -s $5 00:11:22:33:44:55 pub` 在 SLIP 服务器的 ARP 表中加入了一个表项。 这个 ARP 项使得每当这个以太网上的其它 IP 节点对 SLIP 客户端 IP 地址进行 ARP 请求时， SLIP 服务器会以自已的以太网 MAC 地址作为回应。

当使用以上的例子时， 一定要将 以太网 MAC 地址 （`00:11:22:33:44:55`） 替换成您系统网卡的 MAC 地址， 否则“ARP 代理” 将完全无法工作！ 您可以查看 `netstat -i` 输出结果以取得以太网 MAC 地址; 输出的第二行应该是这样：

```
ed0   1500  <Link>0.2.c1.28.5f.4a         191923	0   129457     0   116
```

这行表明这个系统的以太网 MAC 地址是`00:02:c1:28:5f:4a` ──`netstat -i`输出的以太网 MAC 地址必须改成用冒号隔开， 并且要单个十六进数前加上。 这是[arp(8)](http://www.FreeBSD.org/cgi/man.cgi?query=arp&sektion=8&manpath=freebsd-release-ports)要求的格式; 参考[arp(8)](http://www.FreeBSD.org/cgi/man.cgi?query=arp&sektion=8&manpath=freebsd-release-ports) 的联机手册以获取完整的使用方法。

### 注意:

在编写 `/etc/sliphome/slip.login` 和 `/etc/sliphome/slip.logout` 时， 一定要设置 “可执行” (execute) 位 (换言之， `chmod 755 /etc/sliphome/slip.login /etc/sliphome/slip.logout`)， 否则 `sliplogin`将无法执行它。

##### 28.7.2.4.3. `slip.logout`配置

`/etc/sliphome/slip.logout`并不是必需的 (除非您使用了“ARP 代理”)， 如果您准备创建它， 这里有一个基本的 `slip.logout` 脚本的例子：

```
#!/bin/sh -
#
#       slip.logout

#
# logout file for a slip line.  sliplogin invokes this with
# the parameters:
#      1        2         3        4          5         6     7-n
#   slipunit ttyspeed loginname local-addr remote-addr mask opt-args
#
/sbin/ifconfig sl$1 down
```

如果使用了 “代理 ARP”， 则可能希望 `/etc/sliphome/slip.logout` 在用户注销时自动为 SLIP 客户端删除 ARP 项：

```
#!/bin/sh -
#
#       @(#)slip.logout

#
# logout file for a slip line.  sliplogin invokes this with
# the parameters:
#      1        2         3        4          5         6     7-n
#   slipunit ttyspeed loginname local-addr remote-addr mask opt-args
#
/sbin/ifconfig sl$1 down
# Quit answering ARP requests for the SLIP client
/usr/sbin/arp -d $5
```

`arp -d $5` 将删除由 “代理 ARP” `slip.login` 在 SLIP 客户程序登录时所生成的 ARP 项。

再次强调： 建立 `/etc/sliphome/slip.logout` 之后， 一定要设置可执行位 (也就是说， `chmod 755 /etc/sliphome/slip.logout`)。

#### 28.7.2.5. 路由考虑

如果没有使用 “代理 ARP” 的方法来在您的 SLIP 客户机和网络的其余部分 (也可能是 Internet) 之间路由数据包， 您可能需要增加离您最近的默认路由器的静态路由， 以便通过 SLIP 服务器来在 SLIP 客户机子网上进行路由。

##### 28.7.2.5.1. 静态路由

向您最近的默认路由添加一个静态路由可以说是很麻烦 (或者说是不可能， 如果您没有权限这么做)。 如果在您的组织中使用多路由器网络， 有些路由器 (比如 Cisco 和 Proteon 生产的) 不但要配置指向 SLIP 子网的路由， 而且还需要配置将哪些静态路由传给其它的路由器。 所以一些专家意见和问题解答对于使基于静态路由表的路由正常工作很有必要。

## 第 29 章 电子邮件

Original work by Bill Lloyd.Rewritten by Jim Mock.

## 29.1. 概述

“电子邮件”，或通常所说的 email，是现今使用最广泛的通信方式之一。 本章将对如何在 FreeBSD 上运行邮件服务，以及如何使用 FreeBSD 来收发电子邮件作基本的介绍； 然而，它并不是一份完整的参考手册，实际上，许多需要考虑的重要事项都没有提及。 我们推荐读者阅读 附录 B, *参考文献* 中的参考书籍，以获得对于这部分的全面认识。

读完这章，您将了解：

*   哪些软件与收发电子邮件有关。

*   FreeBSD 下的基本 sendmail 配置文件在哪里。

*   本地和远程邮箱之间的区别。

*   如何阻止垃圾邮件制造者非法地使用您的邮件服务器作为转发中继。

*   如何安装和配置用于替代 sendmail 的其他邮件传输代理。

*   如何处理常见的邮件服务器问题。

*   如何使用 SMTP 和 UUCP。

*   如何设置系统使其只能发送邮件。

*   如何在拨号连接时使用邮件。

*   如何配置 SMTP 验证以增加安全性。

*   如何安装并使用用户邮件代理，如 mutt 来收发邮件。

*   如何从远程的 POP 或 IMAP 服务器上下载邮件。

*   如何在进入的邮件上自动地应用过滤器和规则。

阅读本章之前，您需要：

*   正确地配置您的网络连接 (第 32 章 *高级网络*).

*   正确地为您的邮件服务器配置 DNS 信息 (第 30 章 *网络服务器*).

*   知道如何安装第三方软件 (第 5 章 *安装应用程序: Packages 和 Ports*).

## 29.2. 使用电子邮件

邮件交换可以分为 5 部分。它们是： 用户端程序、服务端守护进程、DNS、远程或本地的邮箱、 当然，还有邮件主机自己。

### 29.2.1. 用户端程序

这包括一些基于命令行的程序，例如 mutt、 alpine、elm 和 `mail`，以及类似 balsa、 xfmail 这样的 GUI 程序。 此外，还有我们更“熟悉的”WWW 浏览器这样的程序。 这些程序简单地通过调用服务守护进程把邮件事务交给本地的 “邮件主机”，或者通过 TCP 把邮件发出去。

### 29.2.2. 邮件主机上使用的服务程序

FreeBSD 默认情况下采用 sendmail， 但它也支持为数众多的其它邮件服务程序， 这其中包括：

*   exim;

*   postfix;

*   qmail.

邮件服务器后台守护程序通常有两个功能 ── 接收外面发来的邮件和把邮件传送出去。 但它 *不* 负责使用类似 POP 或 IMAP 这样的协议来帮您阅读邮件， 也不负责连接到本地的 `mbox` 或 Maildir 信箱。 您可能需要其它的 服务程序 来完成这些任务。

### 警告:

较早版本的 sendmail 有一些严重的安全问题， 他们可能导致攻击者从本地和/或远程操作您的电脑。 您应该确认自己使用的是最新版本以避免这些问题。 另外， 也可以从 FreeBSD Ports Collection 来安装其它的 MTA。

### 29.2.3. Email 和 DNS

域名系统 (DNS) 及其服务程序 `named` 在 email 的投递过程当中扮演着很重要的角色。 为了能够从您的站点向其它的站点传递邮件， 服务程序需要通过 DNS 查找接收邮件的远程站点的位置。 类似地， 在远程站点向您的主机投递邮件时也会发生这样的查找。

DNS 负责将主机名映射为 IP 地址， 同时， 也需要保存递送邮件时所需要的信息， 这些信息称作 MX 记录。 MX (Mail eXchanger，邮件交换) 记录指定了哪个， 或哪些主机能够接收特定域下的邮件。 如果您没有为主机名或域名设置 MX 记录， 则邮件将被直接递交给主机名对应 IP 所在的主机。

您可以通过 [host(1)](http://www.FreeBSD.org/cgi/man.cgi?query=host&sektion=1&manpath=freebsd-release-ports) 命令来查找任何域或主机名对应的 MX 记录， 如下面的例子所示：

```
% `host -t mx FreeBSD.org`
FreeBSD.org mail is handled (pri=10) by mx1.FreeBSD.org
```

### 29.2.4. 接收邮件

为您的域接收邮件是通过邮件服务器来完成的。 它收集发送给您的域的那些邮件，并保存到 `mbox` (存储邮件默认的方法) 或 Maildir 格式， 这取决于您采用的配置。 一旦邮件被保存下来， 就可以在本地通过类似 [mail(1)](http://www.FreeBSD.org/cgi/man.cgi?query=mail&sektion=1&manpath=freebsd-release-ports) 或 mutt 这样的程序， 或在远程通过 POP 或 IMAP 这样的协议来读取了。 简单地说， 如果您只在本地阅读邮件，那就没有必要安装 POP 或 IMAP 服务。

#### 29.2.4.1. 通过 POP 和 IMAP 访问远程的邮件

如果希望在远程访问邮箱， 就需要访问 POP 或 IMAP 服务器。 这些协议允许用户从远程方便地访问他们的信箱。 尽管 POP 和 IMAP 都允许用户从远程访问信箱， 但 IMAP 有很多优点， 这包括：

*   IMAP 既可以从远程服务器上抓取邮件， 也可以把邮件放上去。

*   IMAP 支持并发更新。

*   IMAP 对于使用低速网络的用户尤为有用， 因为它能够让这些用户把邮件的结构下载下去， 而无需立即下载整个邮件。 它还可以在服务器端执行类似查找这样的操作， 以减少客户机和服务器之间的通讯量。

您可以按照下面的步骤来安装和配置 POP 或 IMAP 服务器：

1.  选择一个最符合需要的 IMAP 或 POP 服务器。 下列 POP 和 IMAP 服务器是最著名的， 而且都有很多成功案例：

    *   qpopper;

    *   teapop;

    *   imap-uw;

    *   courier-imap;

    *   dovecot;

2.  通过 ports collection 安装 POP 或 IMAP 服务。

3.  根据需要修改 `/etc/inetd.conf` 来加载 POP 或 IMAP 服务。

### 警告:

此外还应注意的是 POP 和 IMAP 传递的信息， 包括用户名和口令等等， 通常都是明文的。 这意味着如果您希望加密传输过程中的信息， 可能需要考虑使用 [ssh(1)](http://www.FreeBSD.org/cgi/man.cgi?query=ssh&sektion=1&manpath=freebsd-release-ports) 隧道或者使用 SSL。 关于如何实施隧道在 第 15.10.8 节 “SSH 隧道” 中进行了详细阐述， SSL 部分在第 15.8 节 “OpenSSL”。

#### 29.2.4.2. 操作本地的信箱

信箱可以在邮件服务器本地直接用 MUA 来进行操作。 这通常是通过 mutt 或 [mail(1)](http://www.FreeBSD.org/cgi/man.cgi?query=mail&sektion=1&manpath=freebsd-release-ports) 这样的应用程序实现的。

### 29.2.5. 邮件服务器

邮件服务器是通过服务器给的一个名字 (译注：来识别主机)， 这也正是它能在您的主机和网络上发送和接收邮件的原因。

## 29.3. sendmail 配置

作者：Christopher Shumway.

[sendmail(8)](http://www.FreeBSD.org/cgi/man.cgi?query=sendmail&sektion=8&manpath=freebsd-release-ports) 是 FreeBSD 中的默认邮件传输代理 (MTA)。 sendmail 的任务是从邮件用户代理 (MUA) 接收邮件然后根据配置文件的定义把它们送给配置好的的寄送程序。 sendmail 也能接受网络连接， 并且发送邮件到本地邮箱或者发送它到其它程序。

sendmail 使用下列配置文件：

| 文件名 | 功能 |
| --- | --- |
| `/etc/mail/access` | sendmail 访问数据库文件 |
| `/etc/mail/aliases` | 邮箱别名 |
| `/etc/mail/local-host-names` | sendmail 接收邮件主机列表 |
| `/etc/mail/mailer.conf` | 邮寄配置程序 |
| `/etc/mail/mailertable` | 邮件分发列表 |
| `/etc/mail/sendmail.cf` | sendmail 的主配置文件 |
| `/etc/mail/virtusertable` | 虚拟用户和域列表 |

### 29.3.1. `/etc/mail/access`

访问数据库定义了什么主机或者 IP 地址可以访问本地邮件服务器和它们是哪种类型的访问。 主机可能会列出 `OK`、 `REJECT`、`RELAY` 或者简单的通过 sendmail 的出错处理程序检测一个给定的邮件错误。 主机默认列出 `OK`，允许传送邮件到主机， 只要邮件的最后目的地是本地主机。列出 `REJECT` 将拒绝所有的邮件连接。如果带有 `RELAY` 选项的主机将被允许通过这个邮件服务器发送邮件到任何地方。

例 29.1. 配置 sendmail 的访问许可数据库

```
cyberspammer.com                550 We do not accept mail from spammers
FREE.STEALTH.MAILER@            550 We do not accept mail from spammers
another.source.of.spam          REJECT
okay.cyberspammer.com           OK
128.32                          RELAY
```

在上面的例子中我们有 5 条记录。 与左边列表匹配的发件人受到右边列表动作的影响。 前边的两个例子给出了 sendmail 的出错处理程序检测到的错误代码。 当一个邮件与左边列表相匹配时，这个信息会被打印到远程主机上。 下一条记录拒绝来自 Internet 上的一个特别主机的邮件 `another.source.of.spam`。接下来的记录允许来自 `okay.cyberspammer.com` 的邮件连接， 这条记录比上面那行 `cyberspammer.com` 更准确。更多的准确匹配使不准确的匹配无效。最后一行允许电子邮件从主机和 `128.32` 开头的 IP 地址转发。 这些主机将被允许通过这台邮件服务器前往其它邮件服务器发送邮件。

当这个文件被升级的时候，您必须在 `/etc/mail/` 运行 `make` 升级数据库。

### 29.3.2. `/etc/mail/aliases`

别名数据库包含一个扩展到用户，程序或者其它别名的虚拟邮箱列表。 下面是一些在 `/etc/mail/aliases` 中使用的例子：

例 29.2. 邮件别名

```
root: localuser
ftp-bugs: joe,eric,paul
bit.bucket:  /dev/null
procmail: "|/usr/local/bin/procmail"
```

这个文件的格式很简单； 冒号左边的邮箱名， 会被展开成右边的形式。 第一个例子简单地将 `root` 邮箱扩展为 `localuser`， 之后将继续在别名数据库中进行查找。 如果没有找到匹配的记录， 则邮件会被发给本地用户 `localuser`。 第二个例子展示了一个邮件列表。 发送到 `ftp-bugs` 的邮件会被展开成 `joe`， `eric` 和 `paul` 这三个邮箱。 注意也可以通过 `<user@example.com>` 这样的形式来指定远程的邮箱。 接下来的例子展示了如何把邮件写入到文件中， 这个例子中是 `/dev/null`。 最后一个例子展示了如何将邮件发给一个程序， 具体而言是通过 UNIX® 管道发到 `/usr/local/bin/procmail` 的标准输入。

更新此文件时， 您需要在 `/etc/mail/` 中使用 `make` 来更新数据库。

### 29.3.3. `/etc/mail/local-host-names`

这是一个 [sendmail(8)](http://www.FreeBSD.org/cgi/man.cgi?query=sendmail&sektion=8&manpath=freebsd-release-ports) 被接受为一个本地主机名的主机名列表。 可以放入任何 sendmail 将从那里收发邮件的域名或主机。例如，如果这个邮件服务器从域 `example.com` 和主机 `mail.example.com` 接收邮件，它的 `local-host-names` 文件，可以看起来象如下这样：

```
example.com
mail.example.com
```

当这个文件被升级，[sendmail(8)](http://www.FreeBSD.org/cgi/man.cgi?query=sendmail&sektion=8&manpath=freebsd-release-ports) 必须重新启动，以便更新设置。

### 29.3.4. `/etc/mail/sendmail.cf`

sendmail 的主配置文件 `sendmail.cf` 控制着 sendmail 的所有行为， 包括从重写邮件地址到打印拒绝远程邮件服务器信息等所有事。 当然，作为一个不同的角色，这个配置文件是相当复杂的， 它的细节部分已经超出了本节的范围。幸运的是， 这个文件对于标准的邮件服务器来说很少需要被改动。

sendmail 主配置文件可以用 [m4(1)](http://www.FreeBSD.org/cgi/man.cgi?query=m4&sektion=1&manpath=freebsd-release-ports) 宏定义 sendmail 的特性和行为。它的细节请看 `/usr/src/contrib/sendmail/cf/README`。

当这个文件被修改时， sendmail 必须重新启动以便对新修改生效。

### 29.3.5. `/etc/mail/virtusertable`

`virtusertable` 映射虚拟域名和邮箱到真实的邮箱。 这些邮箱可以是本地的、远程的、`/etc/mail/aliases` 中定义的别名或一个文件。

例 29.3. 虚拟域邮件映射的例子

```
root@example.com                root
postmaster@example.com          postmaster@noc.example.net
@example.com                    joe
```

在上面这个例子中， 我们映射了一个域 `example.com`。 这个文件是按照从上到下， 首个匹配的方式来处理的。 第一项将 `<root@example.com>` 映射到本地邮箱 `root`。 下一项则将 `<postmaster@example.com>` 映射到位于 `noc.example.net` 的 `postmaster`。 最后， 如果没有来自 `example.com` 的匹配， 则将使用最后一条映射， 它表示将所有的其它邮件发给 `example.com` 域的某个人。 这样， 将映射到本地信箱 `joe`。

## 29.4. 改变您的邮件传输代理程序

Written by Andrew Boothman.Information taken from e-mails written by Gregory Neil Shapiro.

先前已经提到，FreeBSD 中的 sendmail 已经安装了您的 MTA (邮件传输代理程序)。因此它负责着您的收发邮件的工作。

然而，基于不同的理由，一些系统管理员想要改变他们系统的 MTA。这些理由从简单的想要尝试另一个 MTA，到需要一个特殊的特性或者 package 依赖某个邮寄程序等等。幸运的是，不管是什么理由，FreeBSD 都能容易的改变它。

### 29.4.1. 安装一个新的 MTA

对于可用的 MTA 您有很多的选择。一个好的出发点是 FreeBSD Ports Collection，在那里您能找到很多。 当然您可以从任何位置不受任何限制的使用 MTA，只要您能让它运行在 FreeBSD 下。

开始安装您的新 MTA。一旦它被安装， 它可以让您有机会判定它是否能满足您的需要， 并且在它接管 sendmail 之前让您有机会配置您的新软件。 当完成这些之后，您应该确信安装的新软件不会尝试更改系统的二进制文件例如 `/usr/bin/sendmail`。 除此以外， 您的新邮件软件启用之前要已经配置好它。

具体配置请参考您所选择的 MTA 软件的配置文档或其它相关资料。

### 29.4.2. 禁用 sendmail

### 警告:

如果您打算禁用 sendmail 的邮件发出服务， 保持系统中有一个替代它的、 可用的邮件递送系统就非常重要。 如果您不这样做的话， 类似 [periodic(8)](http://www.FreeBSD.org/cgi/man.cgi?query=periodic&sektion=8&manpath=freebsd-release-ports) 这样的系统功能就无法如预期的那样， 通过邮件来传送其执行结果。 您系统中的许多部分可能都假定有可用的 sendmail-兼容 系统。 如果这些应用程序继续使用 sendmail 的执行文件来发送邮件， 而您又禁用了它， 则邮件将进入 sendmail 的非活跃 (inactive) 队列， 而永远不会被送达。

要彻底禁用包括邮件送出服务在内的所有 sendmail 功能， 必须将

```
sendmail_enable="NO"
sendmail_submit_enable="NO"
sendmail_outbound_enable="NO"
sendmail_msp_queue_enable="NO"
```

写入 `/etc/rc.conf`。

如果只是想要停止 sendmail 的接收邮件服务， 您应该在 `/etc/rc.conf` 文件中设置

```
sendmail_enable="NO"
```

更多的有关 sendmail 可用的启动选项，参看 [rc.sendmail(8)](http://www.FreeBSD.org/cgi/man.cgi?query=rc.sendmail&sektion=8&manpath=freebsd-release-ports) 联机手册.

### 29.4.3. 机器引导时运行您的新 MTA

可以向 `/etc/rc.conf` 中加入配置项使新的 MTA 在系统启动时运行， 下面是一个 postfix 的例子：

```
# echo '*`postfix`*_enable=“YES”' >> /etc/rc.conf
```

这样 MTA 就能在系统启动是自动运行了。

### 29.4.4. 替换系统默认的邮寄程序 sendmail

因为 sendmail 程序是一个在 UNIX® 系统下普遍存在的一个标准的软件，一些软件就假定它已经被安装并且配置好。 基于这个原因，许多其它的 MTA 提供者都提供了兼容 sendmail 的命令行界面来执行。 这使它们像“混入”sendmail 一样变的很容易掌握。

因此，如果您使用其它的邮寄程序， 您必须确定这个软件是去尝试运行标准的 sendmail 二进制，就象 `/usr/bin/sendmail`，还是运行您自己选择的替换邮寄程序。 幸运的是，FreeBSD 提供了一个系统调用 [mailwrapper(8)](http://www.FreeBSD.org/cgi/man.cgi?query=mailwrapper&sektion=8&manpath=freebsd-release-ports)，它能为您做这件工作。

当 sendmail 安装后被运行，您可以在 `/etc/mail/mailer.conf` 中找到如下行：

```
sendmail	 /usr/libexec/sendmail/sendmail
send-mail	/usr/libexec/sendmail/sendmail
mailq		/usr/libexec/sendmail/sendmail
newaliases	/usr/libexec/sendmail/sendmail
hoststat	/usr/libexec/sendmail/sendmail
purgestat	/usr/libexec/sendmail/sendmail
```

这个的意思就是当这些公共命令 (例如 `sendmail` 它本身) 运行时， 系统实际上调用了一个 `sendmail` 指定的 mailwrapper 的副本，它检查 `mailer.conf` 并且运行 `/usr/libexec/sendmail/sendmail` 做为替代。当默认的 `sendmail` 功能被调用， 系统将很容易的改变实际上运行的二进制文件。

因此如果您想要 `/usr/local/supermailer/bin/sendmail-compat` 替换 sendmail 被运行，您应该改变 `/etc/mail/mailer.conf` 文件为：

```
sendmail	 /usr/local/supermailer/bin/sendmail-compat
send-mail	/usr/local/supermailer/bin/sendmail-compat
mailq		/usr/local/supermailer/bin/mailq-compat
newaliases	/usr/local/supermailer/bin/newaliases-compat
hoststat	/usr/local/supermailer/bin/hoststat-compat
purgestat	/usr/local/supermailer/bin/purgestat-compat
```

### 29.4.5. 最后

一旦做完您想要配置的每件事，您应该杀掉 sendmail 进程并且启动属于您的新软件的进程， 或者简单的重启。 重启也将给您提供了确认您的系统已经进行了正确的配置的机会。 在引导的时候自动的运行您新的 MTA。

## 29.5. 疑难解答

| **29.5.1.** | 为什么必须在我的站点的主机上使用 FQDN？ |
|  | 您可能会发现主机实际上是在另外一个域里面， 例如，如果您是在 `foo.bar.edu` 里，而您要找一台叫 `mumble` 的主机，它在 `bar.edu` 域里，您就必须用完整的域名 `mumble.bar.edu`，而不是用 `mumble`。传统上，这在 BSD BIND resolvers 中是可行的。 然而目前随 FreeBSD 附带的 BIND 已不为同一域外提供缩写服务。所以，这个不完整的主机名 `mumble` 必须以 `mumble.foo.bar.edu` 这种形式才能被找到， 或者将在根域中搜索它。这跟以前的处理是不同的，以前版本将会继续寻找 `mumble.bar.edu` 和 `mumble.edu`。 如果您想要了解这种方式是否是好，或者它有什么安全方面的漏洞， 请参阅 RFC 1535 文档。如果您想要一个好的工作环境，您可以使用如下行： 
```
search foo.bar.edu bar.edu
```

替换先前旧的版本：

```
domain foo.bar.edu
```

把这行放在您的 `/etc/resolv.conf` 文件中。然而，请一定要确定这样的搜寻顺序不会造成 RFC 1535 里提到的“boundary between local and public administration” 问题。 |
| **29.5.2.** | sendmail 提示信息 mail loops back to myself |
|  | 下面是 sendmail FAQ 中的回答： 
```
我得到了如下的信息：

553 MX list for domain.net points back to relay.domain.net
554 <user@domain.net>... Local configuration error

我如何解决这个问题？

您已经通过 MX 记录指定把发送给特定的域 (例如，domain.net)
的邮件被转寄到指定的主机 (在这个例子中，relay.domain.net)，
而这台机器并不认为它自己是 domain.net。请把 domain.net 添加到
/etc/mail/local-host-names 文件中 [在 8.10 版之前是 /etc/sendmail.cw]
(如果您使用 FEATURE(use_cw_file) 的话) 或者在 /etc/mail/sendmail.cf
中添加“Cw domain.net”。
```

sendmail 的 FAQ 可以在 `[`www.sendmail.org/faq/`](http://www.sendmail.org/faq/)` 找到， 如果您想要对您的邮件做任何的“调整”， 则推荐首先看一看它。 |
| **29.5.3.** | 我如何在一个拨号主机上运行一个邮件服务？ |
|  | 您想要把局域网上的 FreeBSD 主机连接到互连网上，而这台 FreeBSD 主机将会成为这个局域网的邮件网关， 这个拨号连接不必一直保持在连接状态。最少有两种方法可以满足您的要求。一种方法就是使用 UUCP。另一种方法是找到一个专职的服务器来为您的域提供副 MX 主机服务。 例如，如果您公司的域名是 `example.com`，您的互连网服务提供者把 `example.net` 作为您域的副 MX 服务： 
```
example.com.          MX        10      example.com.
                      MX        20      example.net.
```

只有一台主机被指定当做您的最终收信主机 (在 `example.com` 主机的 `/etc/mail/sendmail.cf` 文件中添加 `Cw example.com`)。当 `sendmail` 试图分发邮件的时候， 它会尝试通过 modem 连接到您 (`example.com`)。 因为您并不在线，所以总是会得到一个超时的错误。 sendmail 将会把邮件发送到副 MX 主机，也就是说，您的互连网服务提供者 (`example.net`)。副 MX 主机将周期性的尝试连接并发送邮件到您的主机 (`example.com`)。您也许想要使用下面的这个登录脚本：

```
#!/bin/sh
# Put me in /usr/local/bin/pppmyisp
( sleep 60 ; /usr/sbin/sendmail -q ) &
/usr/sbin/ppp -direct pppmyisp
```

如果您想要为一个用户建立一个分开登录的脚本， 您可以使用 `sendmail -qRexample.com` 替换上面的脚本。这样将使所有的邮件按照您的 `example.com` 队列立即被处理。更深入的方法可以参考下面这段：这段信息是从 [FreeBSD Internet 服务提供商的邮件列表](http://lists.FreeBSD.org/mailman/listinfo/freebsd-isp) 拿来的。

```
> 我们为用户提供副 MX 主机服务。用户每天都会上线好几次
> 并且自动把信件取回主 MX 主机
> (当有他们的邮件时我们并没有通知他们)。
> 我们的 mailqueue 程序每 30 分钟清一次邮件队列。那段时间他们
> 就必须上线 30 分钟以确保他们的信件送达他们的主 MX 主机。
>
> 有任何指令可以用 sendmail 寄出所有邮件么？
> 普通用户在我们的机器上当然没有 root 权限。

在 sendmail.cf 的“privacy flags”部分，有这样的设定
Opgoaway,restrictqrun

移除 restrictqrun 可以让非 root 用户启动队列处理的程序。
您可能也要重新安排您的 MX 设定。我们是用户的 MX 主机，
而且我们还设定了这个：

# If we are the best MX for a host, try directly instead of generating # local config error.
OwTrue

这样的话远程机器会直接把信送给您，而不会尝试连接您的用户的机器。
然后您就可以把邮件发送到您的用户。这个设定只对
“主机”有效，所以您必须要让您的用户在 DNS 中把他们的邮件主机设置为
“customer.com”或者
“hostname.customer.com”。只要为“customer.com”在 DNS
里添加一个 A 记录就可以了。
```

 |
| **29.5.4.** | 为什么当我发送邮件到其它主机总是有 Relaying Denied 出错信息？ |
|  | 默认的 FreeBSD 安装中， sendmail 会配置为只发送来自它所在主机上的邮件。 例如，如果有可用的 POP 服务器，则用户将可以从学校、 公司或其他什么别的地方检查邮件，但他们仍然无法从远程直接发送邮件。 通常，在几次尝试之后， MAILER-DAEMON 将发出一封包含 5.7 Relaying Denied 错误信息的邮件。有很多方法可以避免这种现象。 最直截了当的方法是把您的 ISP 的地址放到 `/etc/mail/relay-domains` 文件中。 完成这项工作的简单的方法是： 
```
# `echo "your.isp.example.com" > /etc/mail/relay-domains`
```

建立或编辑这个文件以后您必须重新启动 sendmail。 如果您是一个管理员并且不希望在本地发送邮件， 或者想要在其它的机器甚至其它的 ISP 上使用一个客户端系统， 这个方法是很方便的。如果您仅有一到两个邮件帐户它也非常的有用。 如果有大量的地址需要添加， 您可以很简单的使用您喜欢的文本编辑器打开这个文件添加域名， 每行一个：

```
your.isp.example.com
other.isp.example.net
users-isp.example.org
www.example.org
```

现在邮件可以通过您的系统传送， 这个列表中存在的主机 (前提是用户在您的系统上已经有一个帐户) 将可以成功的发送。这是一个允许正常的远程用户从您的系统发送邮件， 并且阻止其它非法用户通过您系统发送垃圾邮件的好方法。 |

## 29.6. 高级主题

下面这节将介绍邮件配置和为整个域安装邮件。

### 29.6.1. 基本配置

在邮箱外，只要您设置 `/etc/resolv.conf` 或者运行您自己的名字服务器，您就可以发送邮件到外部的主机。 如果您想要您的邮件发送给某个特定的 MTA(例如， sendmail) 在您的 FreeBSD 主机上，有两个方法：

*   运行您自己的域名服务器和您自己的域。例如， `FreeBSD.org`

*   获得直接分发给您主机的邮件。您可以直接使用您当前的 DNS 名称。例如，`example.FreeBSD.org`。

不管您选择上面那种方法，为了直接在您的主机上发送邮件， 必须有一个静态的 IP 地址(不是象 PPP 拨号一样的动态地址)。 如果您在防火墙后面，它必须让 SMTP 协议通过。 如果您想要在您的主机上直接的收取邮件， 您必须确定两件事：

*   确定在您 DNS 中的 MX 记录(最小编号的)指向您的 IP 地址。

*   确定在您 DNS 中的 MX 记录没有禁止您的主机。

上面的每条记录都允许您在您的主机直接接收邮件。

试试这个：

```
# `hostname`
example.FreeBSD.org
# `host example.FreeBSD.org`
example.FreeBSD.org has address 204.216.27.XX
```

如果您看到这些， 则直接发往 `<yourlogin@example.FreeBSD.org>` 应该已经可以正常工作了 (假设 sendmail 已经在 `example.FreeBSD.org` 上正确启动了)。

如果您看到这些：

```
# `host example.FreeBSD.org`
example.FreeBSD.org has address 204.216.27.XX
example.FreeBSD.org mail is handled (pri=10) by hub.FreeBSD.org
```

所有发送到主机 (`example.FreeBSD.org`) 的邮件在相同的用户名下将会被 `hub` 终止的收集，而不是直接发送到您的主机。

上面的信息是通过您的 DNS 服务器来处理的。支持邮件路由信息的 DNS 记录是 *邮件* *交换* 记录。如果 MX 记录不存在，邮件将通过它自己的 IP 地址被直接的发送到主机。

`freefall.FreeBSD.org`的 MX 记录如下所示:

```
freefall		MX	30	mail.crl.net
freefall		MX	40	agora.rdrop.com
freefall		MX	10	freefall.FreeBSD.org
freefall		MX	20	who.cdrom.com
```

正如您说看到的，`freefall` 有很多 MX 记录。 最小编号的 MX 记录是直接接收邮件的主机。如果因为一些原因它不可用，其它 (有时会访问“backup MXes”)接收信息将会暂时接替并做临时的排列。

为了有效的使用交换式 MX 站点，应当从您的机器上分离一些 Internet 连接。您的 ISP 或者其它友好的站点可以没有任何问题的为您提供这个服务。

### 29.6.2. Mail for Your Domain

为了设置一个“邮件主机”(又称邮件服务器) 您必须要把许多邮件发送到与它相连的几个工作站中。 基本上，您想要“要求”在您域的每个主机的所有邮件 (在这个例子里是 `*.FreeBSD.org`) 转向到您的邮件服务器，从而使您的用户可以在主邮件服务器里接收他们的邮件。

要使工作最简单，带有同样 *用户名* 的帐户应该同时存在于两台机器上。使用 [adduser(8)](http://www.FreeBSD.org/cgi/man.cgi?query=adduser&sektion=8&manpath=freebsd-release-ports) 来这样做。

您将使用的邮件主机必须为每个工作站指定一个邮件交换。您可以在 DNS 中这样配置：

```
example.FreeBSD.org	A	204.216.27.XX		; Workstation
			MX	10 hub.FreeBSD.org	; Mailhost
```

无论 A 记录指向哪，这将为工作站重新定位到邮件主机。邮件将被发送到 MX 主机。

您不能自己这样做除非您运行着一个 DNS 服务器。 如果不是这样，或者不能运行您自己的 DNS 服务器，告诉您的 ISP 或者给您提供 DNS 服务的人。

如果您正在使用虚拟邮件主机，下面的信息将会对您有用。 在这个例子里，我们假定您有一个客户并且他有自己的域， 这个例子中是 `customer1.org`，您要把 `customer1.org` 所有的邮件发送到您的邮件主机 `mail.myhost.com`。 您的 DNS 记录应该是这样：

```
customer1.org		MX	10	mail.myhost.com
```

您 *不* 需要有个 A 记录， 如果您只为域 `customer1.org` 处理邮件。

### 注意:

必须清楚 `customer1.org` 将不能工作，除非存在一个 A 记录。

最后一件您必须要做的事是告诉 sendmail 接受邮件的是什么域和(或)主机名。 这里有好几种方法。下面方法可以任选一种：

*   添加您的主机到 `/etc/mail/local-host-names` 文件中，如果您使用的是 `FEATURE(use_cw_file)`。如果您使用 sendmail 8.10 或者更高版本，文件是 `/etc/sendmail.cw`。

*   添加一行 `Cwyour.host.com` 到您的 `/etc/sendmail.cf` 或 `/etc/mail/sendmail.cf` 文件，如果您使用 sendmail 8.10 或者更高版本。

## 29.7. SMTP 与 UUCP

sendmail 的配置，在 FreeBSD 中已经配置好为您的站点直接的连接 Internet。 如果站点希望他们的邮件通过 UUCP 交换，则必须安装其它的 sendmail 配置文件。

手工的配置 `/etc/mail/sendmail.cf` 是一个高级主题。sendmail 8 版本通过 [m4(1)](http://www.FreeBSD.org/cgi/man.cgi?query=m4&sektion=1&manpath=freebsd-release-ports) 预处理生成一个配置文件，实际上这个配置发生在一个比较高的抽象层。 [m4(1)](http://www.FreeBSD.org/cgi/man.cgi?query=m4&sektion=1&manpath=freebsd-release-ports) 配置文件可以在 `/usr/share/sendmail/cf` 下找到。 `cf` 目录中的 `README` 文件是关于 [m4(1)](http://www.FreeBSD.org/cgi/man.cgi?query=m4&sektion=1&manpath=freebsd-release-ports) 配置的基本的介绍。

最好的支持 UUCP 传送的方法是使用 `mailertable` 的特点。建立一个资料库让 sendmail 可以使用它自己的路由决策。

首先，您必须建立您自己的 `.mc` 文件。 `/usr/share/sendmail/cf/cf` 目录包含一些例子。 假定您已经命名自己的文件叫做 `foo.mc`， 您要做的只是把它转换成一个有效的 `sendmail.cf`：

```
# `cd /etc/mail`
# `make foo.cf`
# `cp foo.cf /etc/mail/sendmail.cf`
```

一个典型的 `.mc` 文件看起来可能象这样：

```
VERSIONID(`*`Your version number`*') OSTYPE(bsd4.4)

FEATURE(accept_unresolvable_domains)
FEATURE(nocanonify)
FEATURE(mailertable, `hash -o /etc/mail/mailertable')

define(`UUCP_RELAY', *`your.uucp.relay`*)
define(`UUCP_MAX_SIZE', 200000)
define(`confDONT_PROBE_INTERFACES')

MAILER(local)
MAILER(smtp)
MAILER(uucp)

Cw    *`your.alias.host.name`*
Cw    *`youruucpnodename.UUCP`*
```

`accept_unresolvable_domains`、 `nocanonify` 和 `confDONT_PROBE_INTERFACES` 特性将避免在传送邮件时使用 DNS 的机会。`UUCP_RELAY` 项是支持 UUCP 传送所必须的。简单的放入一个 Internet 上可以处理 UUCP 虚拟域地址的主机名。通常，您在这里填入您 ISP 邮件的回复处。

一旦您做完这些，您还需要这个 `/etc/mail/mailertable` 文件。 如果您只有一个用来传递所有邮件的对外通道的话， 以下的文件就足够了：

```
#
# makemap hash /etc/mail/mailertable.db < /etc/mail/mailertable
.                             uucp-dom:*`your.uucp.relay`*
```

一个更复杂点的例子象这样：

```
#
# makemap hash /etc/mail/mailertable.db < /etc/mail/mailertable
#
horus.interface-business.de   uucp-dom:horus
.interface-business.de        uucp-dom:if-bus
interface-business.de         uucp-dom:if-bus
.heep.sax.de                  smtp8:%1
horus.UUCP                    uucp-dom:horus
if-bus.UUCP                   uucp-dom:if-bus
.                             uucp-dom:
```

头三行处理域地址邮件，不应该被传送出默认的路由， 而由某些 UUCP 邻居取代的特殊情况，这是为了走“捷径”。 下一行处理本地网的邮件让它可以使用 SMTP 来传送。 最后，UUCP 邻居提起。UUCP 虚拟域的记载， 允许一个 `uucp-neighbor !recipient` 推翻默认规则。最后一行则以一个单独的句点最为结束， 以 UUCP 传送到提供您所有的邮件网关的 UUCP 邻居。 所有在 `uucp-dom:` 关键字里的节点名称必须是有效的 UUCP 邻居，您可以用 `uuname` 去确认。

提醒您这个文件在使用前必须被转换成 DBM 数据库文件。最好在 `mailertable` 最上面用注解写出命令行来完成这个工作。 当您每次更换您的 `mailertable` 后您总是需要执行这个命令。

最后提示：如果您不确定某个特定的路径可用， 记得把 `-bt` 选项加到 sendmail。这会将 sendmail 启动在 *地址检测模式*。只要按下 `3,0`，接着输入您希望测试的邮件路径位置。 最后一行告诉您使用邮件代理程序， 代理程序会通知目的主机以及 (可能转换) 地址。 要离开此模式请按 **Ctrl**+**D**。

```
% `sendmail -bt`
ADDRESS TEST MODE (ruleset 3 NOT automatically invoked)
Enter <ruleset> <address>
> `3,0 foo@example.com`
canonify           input: foo @ example . com
...
parse            returns: $# uucp-dom $@ *`your.uucp.relay`* $: foo < @ example . com . >
> `^D`
```

## 29.8. 只发送邮件的配置

Contributed by Bill Moran.

许多时候， 可能只希望通过转发服务器来发送邮件。 典型的情况包括：

*   使用桌面机， 但希望通过类似 [send-pr(1)](http://www.FreeBSD.org/cgi/man.cgi?query=send-pr&sektion=1&manpath=freebsd-release-ports) 这样的程序发送邮件。 这样就需要使用 ISP 的邮件转发服务器。

*   不在本地处理邮件的服务器， 但它需要把邮件交给转发服务器来进行处理。

几乎任何一个 MTA 都能够胜任这样的工作。 然而不幸的是， 要把一个全功能的 MTA 正确地配置为只把邮件交给其他服务器是一件很困难的事情。 使用 sendmail 以及 postfix 这样的程序， 多少有些杀鸡用牛刀的感觉。

此外， 如果您使用典型的 Internet 访问服务， 您的协议可能会包含禁止运行 “邮件服务器” 的条款。

满足这些需要最简单的办法是安装 [mail/ssmtp](http://www.freebsd.org/cgi/url.cgi?ports/mail/ssmtp/pkg-descr) port。 以 `root` 身份执行下面的命令：

```
# `cd /usr/ports/mail/ssmtp`
# `make install replace clean`
```

一旦装好， [mail/ssmtp](http://www.freebsd.org/cgi/url.cgi?ports/mail/ssmtp/pkg-descr) 就可以用四行 `/usr/local/etc/ssmtp/ssmtp.conf` 来配置：

```
root=yourrealemail@example.com
mailhub=mail.example.com
rewriteDomain=example.com
hostname=_HOSTNAME_
```

请确认您为 `root` 使用了真实的电子邮件地址。 用您的 ISP 提供的外发邮件转发服务器名称， 替换掉 `mail.example.com` (某些 ISP 可能将其称为 “外发邮件服务器” 或 “SMTP 服务器”)。

接下来需要确认禁用了 sendmail， 包括邮件发出服务在内。 请参见 第 29.4.2 节 “禁用 sendmail” 以了解进一步的细节。

[mail/ssmtp](http://www.freebsd.org/cgi/url.cgi?ports/mail/ssmtp/pkg-descr) 也提供了一些其他选项。 请参见在 `/usr/local/etc/ssmtp` 中的示例配置， 或者 ssmtp 的联机手册来得到一些例子和更多的其他信息。

以这种方式配置 ssmtp， 能够让您计算机上的任何需要发送邮件的软件都正常运转， 而不必冒违反 ISP 的使用政策， 或使您的电脑被劫持用于发送垃圾邮件的风险。

## 29.9. 拨号连接时使用邮件传送

如果您有静态的 IP 地址， 就应该不用修改任何默认的配置。 将主机名设置为分配给您的 Internet 名称，其他的事情 sendmail 都会替您做好。

如果您的 IP 地址是动态分配的， 并使用 PPP 连接拨入 Internet， 则您可能会从 ISP 的邮件服务器上得到一个信箱。 这里我们假设您的 ISP 的域名是 `example.net`， 您的用户名是 `user`， 您把自己的机器称作 `bsd.home`， 而您的 ISP 告诉您可以使用 `relay.example.net` 来转发邮件。

为了从邮箱收取邮件， 需要安装一个收信代理。 fetchmail 是一个能够支持许多种不同协议的不错的选择。 这个程序可以通过 package 或 Ports Collection ([mail/fetchmail](http://www.freebsd.org/cgi/url.cgi?ports/mail/fetchmail/pkg-descr)) 来安装。 通常， 您的 ISP 会提供 POP。 如果您使用用户 PPP，您还可以在 Internet 连接建立时自动地抓取邮件， 这可以通过在 `/etc/ppp/ppp.linkup` 中增加如下的项来实现：

```
MYADDR:
!bg su user -c fetchmail
```

如果您正使用 sendmail (如下所示) 来为非本地用户传送邮件， 则可能需要让 sendmail 在您的 Internet 连接建立时立即传送邮件队列。 要完成这项工作， 应该把下面的命令放到 `/etc/ppp/ppp.linkup` 中的 `fetchmail` 之后

```
  !bg su user -c "sendmail -q"
```

假设您在 `bsd.home` 上有一个 `user` 用户。 在 `bsd.home` 上的 `user` 主目录中创建一个 `.fetchmailrc` 文件：

```
poll example.net protocol pop3 fetchall pass MySecret
```

因为包含了密码 `MySecret`， 这个文件应该只有 `user` 可读。

要使用正确的 `from:` 头来发送文件， 您必须告诉 sendmail 使用 `<user@example.net>` 而不是 i `<user@bsd.home>`。 另外， 您可能也需要要求 sendmail 通过 `relay.example.net` 来发送邮件， 以便更快地传送它们。

以下的 `.mc` 文件应该可以满足您的需求：

```
VERSIONID(`bsd.home.mc version 1.0')
OSTYPE(bsd4.4)dnl
FEATURE(nouucp)dnl
MAILER(local)dnl
MAILER(smtp)dnl
Cwlocalhost
Cwbsd.home
MASQUERADE_AS(`example.net')dnl
FEATURE(allmasquerade)dnl
FEATURE(masquerade_envelope)dnl
FEATURE(nocanonify)dnl
FEATURE(nodns)dnl
define(`SMART_HOST', `relay.example.net')
Dmbsd.home
define(`confDOMAIN_NAME',`bsd.home')dnl
define(`confDELIVERY_MODE',`deferred')dnl
```

如何转换这个 `.mc` 文件到 `sendmail.cf` 文件的细节，请参考前面的章节。 另外，在更新 `sendmail.cf` 文件后， 不要忘记重启 sendmail。

## 29.10. SMTP 验证

作者：James Gorham.

在您的邮件服务器上启用 SMTP 验证有很多好处。 SMTP 验证可以让 sendmail 多一重安全保障， 而且也使得使用不同机器的漫游用户能够使用同一个邮件服务器， 而不需要每次都修改它们的邮件客户端配置。

1.  从 ports 安装 [security/cyrus-sasl2](http://www.freebsd.org/cgi/url.cgi?ports/security/cyrus-sasl2/pkg-descr)。 这个 port 位于 [security/cyrus-sasl2](http://www.freebsd.org/cgi/url.cgi?ports/security/cyrus-sasl2/pkg-descr)。 [security/cyrus-sasl2](http://www.freebsd.org/cgi/url.cgi?ports/security/cyrus-sasl2/pkg-descr) port 支持很多可以在编译时指定的可选项。 由于我们要使用 SMTP 身份验证， 因此要确认没有禁用 `LOGIN` 选项。

2.  安装完 [security/cyrus-sasl2](http://www.freebsd.org/cgi/url.cgi?ports/security/cyrus-sasl2/pkg-descr) 之后， 编辑 `/usr/local/lib/sasl2/Sendmail.conf` (如果不存在则建立一个) 并在其中增加下列配置：

    ```
    pwcheck_method: saslauthd
    ```

3.  接下来， 安装 [security/cyrus-sasl2-saslauthd](http://www.freebsd.org/cgi/url.cgi?ports/security/cyrus-sasl2-saslauthd/pkg-descr)， 编辑 `/etc/rc.conf` 并加入下列配置：

    ```
    saslauthd_enable="YES"
    ```

    最后启用 saslauthd 服务：

    ```
    # `/usr/local/etc/rc.d/saslauthd start`
    ```

    这个服务将充当 sendmail 使用 FreeBSD 的 `passwd` 数据库来完成身份验证时的代理人角色。 这避免了为每个需要使用 SMTP 身份验证的用户建立对应的用户名和口令的麻烦， 也确保了登录与邮件的口令一致。

4.  现在编辑 `/etc/make.conf` 文件，添加如下行：

    ```
    SENDMAIL_CFLAGS=-I/usr/local/include/sasl -DSASL
    SENDMAIL_LDFLAGS=-L/usr/local/lib
    SENDMAIL_LDADD=-lsasl2
    ```

    这些配置将告诉系统在联编 sendmail 时使用适当的配置选项来在编译过程中连入 [cyrus-sasl2](http://www.freebsd.org/cgi/url.cgi?ports/cyrus-sasl2/pkg-descr). 在重新编译 sendmail 之前， 请确认已经安装了 [cyrus-sasl2](http://www.freebsd.org/cgi/url.cgi?ports/cyrus-sasl2/pkg-descr)。

5.  重新编译 sendmail 运行如下命令：

    ```
    # `cd /usr/src/lib/libsmutil`
    # `make cleandir && make obj && make`
    # `cd /usr/src/lib/libsm`
    # `make cleandir && make obj && make`
    # `cd /usr/src/usr.sbin/sendmail`
    # `make cleandir && make obj && make && make install`
    ```

    如果 `/usr/src` 和共享库没有大的变化并且它们都必须可用，sendmail 编译应该没有任何问题。

6.  sendmail 被重新编译和安装后， 编辑您的 `/etc/mail/freebsd.mc` 文件 (或者无论您选择使用的您的哪个 `.mc` 文件。许多管理员选择使用跟 [hostname(1)](http://www.FreeBSD.org/cgi/man.cgi?query=hostname&sektion=1&manpath=freebsd-release-ports) 一样的唯一的 `.mc` 文件输出)。添加这些行在这个文件：

    ```
    dnl set SASL options
    TRUST_AUTH_MECH(`GSSAPI DIGEST-MD5 CRAM-MD5 LOGIN')dnl
    define(`confAUTH_MECHANISMS', `GSSAPI DIGEST-MD5 CRAM-MD5 LOGIN')dnl
    ```

    这些选项配置有不同的方法，对于 sendmail 验证用户。 如果您想要使用除 pwcheck 之外的方法，请参考相关文档。

7.  最后，在 `/etc/mail` 运行 [make(1)](http://www.FreeBSD.org/cgi/man.cgi?query=make&sektion=1&manpath=freebsd-release-ports)。 它将建立您的新 `.mc` 文件并建立一个 `.cf` 文件命名为 `freebsd.cf` (或者您想使用您的其它名字的 `.mc`文件)。接着使用命令 `make install restart`，这将复制文件到 `sendmail.cf`，并且正确的重新启动 sendmail。 更多有关这个过程的信息，您可以参考 `/etc/mail/Makefile` 文件。

如果所每个步骤都做对了， 您应该可以通过您的邮件客户端进入您的登录信息并且传送一个测试信息。 更多的分析，设置 sendmail 的 `LogLevel` 到 13 并且查看 `/var/log/maillog` 中的信息。

如欲了解更多的信息， 请参看 sendmail 网站上的 [关于 SMTP 验证](http://www.sendmail.org/~ca/email/auth.html) 的介绍。

## 29.11. 邮件用户代理

Contributed by Marc Silver.

邮件用户代理 (MUA) 是一个用于收发邮件的应用程序。 更进一步， 随着电子邮件的 “演化” 并愈发复杂， MUA 在和电子邮件相结合方面变得日趋强大； 这为用户提供了更多的功能和灵活性。 FreeBSD 包含了对于众多邮件用户代理的支持， 所有这些都可以通过 FreeBSD Ports Collection 来轻松安装。 用户可以选择类似 evolution 以及 balsa 这样的图形界面程序， 也可以选择类似 mutt、 alpine 或 `mail` 这样的控制台程序， 或者某些大型机构使用的 web 界面。

### 29.11.1. mail

[mail(1)](http://www.FreeBSD.org/cgi/man.cgi?query=mail&sektion=1&manpath=freebsd-release-ports) 是 FreeBSD 中默认的邮件用户代理 (MUA)。 它是一个基于控制台的 MUA， 提供了所有用于收发文本形式的电子邮件所需的基本功能， 虽然它处理附件的能力有限， 而且只支持本地的信箱。

虽然 `mail` 没有内建的 POP 或 IMAP 服务器支持， 然而这些信箱可以通过类似 fetchmail 这样的应用程序， 来下载到本地的 `mbox` 文件中。 这一应用程序在本章的稍后部分 (第 29.12 节 “使用 fetchmail”) 进行了介绍。

要收发邮件， 只需简单地使用 `mail` 命令， 如下所示：

```
% `mail`
```

用户保存在 `/var/mail` 中的信箱的内容会被 `mail` 程序自动地读取。 如果信箱是空的， 程序会退出并给出一个消息表示没有邮件。 一旦读完了信箱， 将启动应用程序的界面， 并列出邮件。 所有的邮件会被自动编号， 类似下面的样子：

```
Mail version 8.1 6/6/93\.  Type ? for help.
"/var/mail/marcs": 3 messages 3 new
>N  1 root@localhost        Mon Mar  8 14:05  14/510   "test"
 N  2 root@localhost        Mon Mar  8 14:05  14/509   "user account"
 N  3 root@localhost        Mon Mar  8 14:05  14/509   "sample"
```

现在， 您通过使用 `mail` 的 **t** 命令， 并给出邮件的编号， 就可以看到邮件了。 在这个例子中， 我们将阅读第一封邮件：

```
& `t 1`
Message 1:
From root@localhost  Mon Mar  8 14:05:52 2004
X-Original-To: marcs@localhost
Delivered-To: marcs@localhost
To: marcs@localhost
Subject: test
Date: Mon,  8 Mar 2004 14:05:52 +0200 (SAST)
From: root@localhost (Charlie Root)

This is a test message, please reply if you receive it.
```

正如在上面的例子中所看到的， **t** 键将显示完整的邮件头。 要再次查看邮件的列表， 可以使用 **h** 键。

如果需要回复邮件， 也可以使用 `mail` 来完成， 方法是使用 **R** 或 **r** 这两个 `mail`键。 **R** 键会要求 `mail` 只回复发送邮件的人， 而 **r** 不仅回复发送邮件的人， 而且也会将回复抄送给原来邮件的其他接收者。 如果需要， 也可以在这些命令后面指定邮件的编号。 做完这些之后， 就可以输入回复了， 在邮件的最后应该有一个只有一个 **.** 的行， 例如：

```
& `R 1`
To: root@localhost
Subject: Re: test

`Thank you, I did get your email.
.`
EOT
```

要发出新邮件， 可以使用 **m**， 后面接收件人的邮件地址。 多个收件人之间， 应该使用 **,** 隔开。 接下来需要输入邮件的主题， 然后是正文。 同样的， 在邮件最后需要一个只有 **.** 的空行表示结束。

```
& `mail root@localhost`
Subject: `I mastered mail

Now I can send and receive email using mail ... :)
.`
EOT
```

在 `mail` 工具中， 可以用 **?** 来显示帮助， 而参考 [mail(1)](http://www.FreeBSD.org/cgi/man.cgi?query=mail&sektion=1&manpath=freebsd-release-ports) 联机手册则可以获得更多关于 `mail` 的帮助信息。

### 注意:

正如前面所提到的那样， [mail(1)](http://www.FreeBSD.org/cgi/man.cgi?query=mail&sektion=1&manpath=freebsd-release-ports) 命令在设计时没有考虑到要处理附件， 因而在这方面他的功能很弱。 新的 MUA， 如 mutt， 能够更好地处理附件。 但如果您仍然希望使用 `mail` 命令， 那么 [converters/mpack](http://www.freebsd.org/cgi/url.cgi?ports/converters/mpack/pkg-descr) port 则是一个值得考虑的附加工具。

### 29.11.2. mutt

mutt 是一个短小精悍的邮件用户代理， 它提供了许多卓越的功能， 包括：

*   能够按线索阅读邮件；

*   支持使用 PGP 对邮件进行数字签名和加密；

*   支持 MIME；

*   支持 Maildir；

*   高度可定制。

所有这些特性， 都使得 mutt 得以跻身于目前最先进的邮件用户代理的行列。 请参考 `[`www.mutt.org`](http://www.mutt.org)` 以了解更多关于 mutt 的资料。

稳定版本的 mutt 可以通过 [mail/mutt](http://www.freebsd.org/cgi/url.cgi?ports/mail/mutt/pkg-descr) port 来安装， 而开发版本， 则可以通过使用 [mail/mutt-devel](http://www.freebsd.org/cgi/url.cgi?ports/mail/mutt-devel/pkg-descr) port 安装。 通过 port 安装之后，可以通过下面的命令来启动 mutt：

```
% `mutt`
```

mutt 会自动读取 `/var/mail` 中的用户信箱， 并显示其内容。 如果用户信箱中没有邮件， 则 mutt 将等待来自用户的命令。 下面的例子展示了 mutt 列出邮件的情形：

![](img/mutt1.png)

要阅读邮件， 只需用光标键选择它， 然后按 **Enter** 键。 以下是 mutt 显示邮件的例子：

![](img/mutt2.png)

和 [mail(1)](http://www.FreeBSD.org/cgi/man.cgi?query=mail&sektion=1&manpath=freebsd-release-ports) 类似， mutt 允许用户只回复发件人， 或者回复所有人。 如果只想回复发信人， 使用 **r** 快捷键。 要回复所有人 (group reply)， 可以用 **g** 快捷键。

### 注意:

mutt 会使用 [vi(1)](http://www.FreeBSD.org/cgi/man.cgi?query=vi&sektion=1&manpath=freebsd-release-ports) 命令作为编辑器， 用于创建和回复邮件。 这一行为可以通过建立用户自己的 `.muttrc` 文件来订制， 方法是修改 `editor` 变量或配置 `EDITOR` 环境变量。 请参见 `[`www.mutt.org/`](http://www.mutt.org/)` 以了解配置 mutt 的进一步信息。

要撰写新邮件， 需要首先按 **m**。 在输入了有效的邮件主题之后， mutt 将启动 [vi(1)](http://www.FreeBSD.org/cgi/man.cgi?query=vi&sektion=1&manpath=freebsd-release-ports)， 您可以在其中撰写邮件。 写好邮件的内容之后， 存盘并退出 `vi`， 则 mutt 将继续， 并显示一些关于将发出的邮件的摘要信息。 要发送邮件， 只需按 **y**。 下面给出了摘要信息的一个例子：

![](img/mutt3.png)

mutt 也提供了相当详尽的帮助， 在绝大多数菜单中， 都可以使用 **?** 键将其呼出。 屏幕顶行中也会给出常用的快捷键。

### 29.11.3. alpine

alpine 主要是针对初学者设计的， 但也提供了一些高级功能。

### 警告:

过去， alpine 软件被发现有许多远程漏洞， 这些漏洞会允许远程的攻击者在用户的本地系统上， 通过发送精心炮制的邮件来执行任意的代码。 所有的 *已知* 问题都已经被修正了， 但 alpine 的代码是以很不安全的风格编写的， 并且 FreeBSD 安全官相信仍然有一些尚未被发现的安全漏洞。 您应当考虑并承担安装 alpine 可能带来的风险。

最新版本的 alpine 可以通过使用 [mail/alpine](http://www.freebsd.org/cgi/url.cgi?ports/mail/alpine/pkg-descr) port 来安装。 装好之后， alpine 可以通过下面的命令启动：

```
% `alpine`
```

第一次启动 alpine 时， 它会显示出一个欢迎页， 并给出简要的介绍， 以及 alpine 开发小组要求用户匿名发送一封邮件， 以便帮助他们了解有多少用户在使用他们开发的客户程序的请求。 要发送这封匿名的邮件， 请按 **Enter**， 您也可以按 **E** 退出， 而不发送匿名邮件。 下面是欢迎页的一个例子：

![](img/pine1.png)

接下来展现给用户的将是主菜单， 可以很容易地通过光标键在上面进行选择。 这个主菜单提供了用于撰写新邮件、 浏览邮件目录， 甚至管理地址簿等等的快捷方式。 主菜单下面是完成各种功能的快捷键说明。

由 alpine 打开的默认目录是 `inbox`。 要查看邮件索引， 应按 **I**， 或选择下面所示的 MESSAGE INDEX 选项：

![](img/pine2.png)

邮件索引展示了当前目录下的邮件， 可以使用光标键翻阅。 按 **Enter** 键阅读高亮选定的邮件。

![](img/pine3.png)

在上面的截屏中， 使用 alpine 显示了一封示例邮件。 在屏幕底部也显示了快捷键供参考。 其中的一个例子是 **r** 键， 它告诉 MUA 回复正显示的邮件。

![](img/pine4.png)

在 alpine 中回复邮件， 是通过 pico 编辑器完成的， 后者默认情况下会随 alpine 一起安装。 而 pico 工具使得浏览邮件变得更加简单， 并且要比 [vi(1)](http://www.FreeBSD.org/cgi/man.cgi?query=vi&sektion=1&manpath=freebsd-release-ports) 或 [mail(1)](http://www.FreeBSD.org/cgi/man.cgi?query=mail&sektion=1&manpath=freebsd-release-ports) 更能容忍误操作。 回复写好之后， 可以用 **Ctrl**+**X** 来发出它。 此前， alpine 程序会要求确认。

![](img/pine5.png)

alpine 程序可以通过使用主菜单中的 SETUP 选项来进行定制。 请参考 `[`www.washington.edu/alpine/`](http://www.washington.edu/alpine/)` 来了解更多信息。

## 29.12. 使用 fetchmail

Contributed by Marc Silver.

fetchmail 是一个全功能的 IMAP 和 POP 客户程序， 它允许用户自动地从远程的 IMAP 和 POP 服务器上下载邮件， 并保存到本地的信箱中； 这样， 访问这些邮件就变得更方便了。 fetchmail 可以通过 [mail/fetchmail](http://www.freebsd.org/cgi/url.cgi?ports/mail/fetchmail/pkg-descr) port 安装， 它提供了许多有用的功能， 其中包括：

*   支持 POP3、 APOP、 KPOP、 IMAP、 ETRN 以及 ODMR 协议。

*   通过 SMTP 转发邮件， 这使得过滤、 转发， 以及邮件别名能够正常工作。

*   能够以服务程序的方式运行， 并周期性地检查邮件。

*   能够从多个信箱收取邮件， 并根据配置， 将这些邮件转发给不同的本地用户。

尽管介绍全部 fetchmail 的功能超出了本书的范围， 但这里仍然介绍了其基本的功能。 fetchmail 工具需要一个名为 `.fetchmailrc` 的配置文件才能正常工作。 这个文件中包含了服务器信息， 以及登录使用的凭据。 由于这个文件包含敏感内容， 建议将其设置为只有属主所有， 使用下面的命令：

```
% `chmod 600 .fetchmailrc`
```

下面的 `.fetchmailrc` 提供了一个将某一用户的信箱通过 POP 下载到本地的例子。 它告诉 fetchmail 连接到 `example.com`， 并使用用户名 `joesoap` 和口令 `XXX`。 这个例子假定 `joesoap` 同时也是本地的系统用户。

```
poll example.com protocol pop3 username "joesoap" password "XXX"
```

下一个例子将连接多个 POP 和 IMAP 服务器， 并根据需要转到不同的本地用户：

```
poll example.com proto pop3:
user "joesoap", with password "XXX", is "jsoap" here;
user "andrea", with password "XXXX";
poll example2.net proto imap:
user "john", with password "XXXXX", is "myth" here;
```

另外， fetchmail 也可以通过指定 `-d` 参数， 并给出 fetchmail 在轮询 `.fetchmailrc` 文件中列出的服务器的时间间隔， 来以服务程序的方式运行。 下面的例子会让 fetchmail 每 600 秒轮询一次：

```
% `fetchmail -d 600`
```

更多关于 fetchmail 的资料， 可以在 `[`fetchmail.berlios.de/`](http://fetchmail.berlios.de/)` 找到。

## 29.13. 使用 procmail

Contributed by Marc Silver.

procmail 是一个强大得惊人的过滤进入邮件的应用程序。 它允许用户定义 “规则”， 并用这些规则来匹配进入的邮件， 进而执行某些特定的功能， 或将这些邮件转发到其他信箱和/或邮件地址。 procmail 可以通过 [mail/procmail](http://www.freebsd.org/cgi/url.cgi?ports/mail/procmail/pkg-descr) port 来安装。 装好之后， 可以直接把它集成到绝大多数 MTA 中； 请参考您使用的 MTA 的文档了解具体的作法。 另外， procmail 可允许通过把下面的设置加入到用户主目录中的 `.forward` 文件中， 来启用 procmail 功能：

```
"|exec /usr/local/bin/procmail || exit 75"
```

接下来我们将介绍一些基本的 procmail 规则， 以及它们都是做什么的。 各种各样的规则， 都应该写到 `.procmailrc` 文件中， 而这个文件则必须放在用户的主目录下。

主要的规则， 也可以在 [procmailex(5)](http://www.FreeBSD.org/cgi/man.cgi?query=procmailex&sektion=5&manpath=freebsd-release-ports) 联机手册中找到。

将所有来自 `<user@example.com>` 的邮件， 转发到外部地址 `<goodmail@example2.com>`：

```
:0
* ^From.*user@example.com
! goodmail@example2.com
```

转发所有不超过 1000 字节的邮件到外部地址 `<goodmail@example2.com>`：

```
:0
* < 1000
! goodmail@example2.com
```

把所有发送到 `<alternate@example.com>` 的邮件放到信箱 `alternate` 中：

```
:0
* ^TOalternate@example.com
alternate
```

将所有标题为 “Spam” 的邮件发到 `/dev/null`：

```
:0
^Subject:.*Spam
/dev/null
```

将收到的所有 `FreeBSD.org` 邮件列表的邮件， 转发到各自的信箱：

```
:0
* ^Sender:.owner-freebsd-\/[^@]+@FreeBSD.ORG
{
	LISTNAME=${MATCH}
	:0
	* LISTNAME??^\/[^@]+
	FreeBSD-${MATCH}
}
```

## 第 30 章 网络服务器

Reorganized by Murray Stokely.

## 30.1. 概要

本章将覆盖某些在 UNIX® 系统上常用的网络服务。话题将会涉及 如何安装、配置、测试和维护多种不同类型的网络服务。本章节中将提 供大量配置文件的样例，期望能够对您有所裨益。

在读完本章之后，您将会知道：

*   如何管理 inetd。

*   如何设置运行一个网络文件系统。

*   如何配置一个网络信息服务器以共享用户帐号。

*   如何通过 DHCP 自动配置网络。

*   如何配置一个域名服务器。

*   如何设置 Apache HTTP 服务器。

*   如何设置文件传输（FTP）服务器。

*   如何使用 Samba 为 Windows® 客户端设置文件和打印服务。

*   如何同步时间和日期，以及如何设置使用 NTP 协议的时间服务器。

*   如何配置标准的日志守护进程， `syslogd`， 接受远程主机的日志。

在阅读此章节之前，您应当：

*   理解有关`/etc/rc`中脚本的基本知识。

*   熟悉基本网络术语。

*   懂得如何安装额外的第三方软件（第 5 章 *安装应用程序: Packages 和 Ports*）。

## 30.2.  inetd “超级服务器”

Contributed by Chern Lee.更新 The FreeBSD Documentation Project.

### 30.2.1. 总览

[inetd(8)](http://www.FreeBSD.org/cgi/man.cgi?query=inetd&sektion=8&manpath=freebsd-release-ports) 有时也被称作 “Internet 超级服务器”， 因为它可以为多种服务管理连接。 当 inetd 收到连接时， 它能够确定连接所需的程序， 启动相应的进程， 并把 socket 交给它 (服务 socket 会作为程序的标准输入、 输出和错误输出描述符)。 使用 inetd 来运行那些负载不重的服务有助于降低系统负载， 因为它不需要为每个服务都启动独立的服务程序。

一般说来， inetd 主要用于启动其它服务程序， 但它也有能力直接处理某些简单的服务， 例如 chargen、 auth， 以及 daytime。

这一节将介绍关于如何通过命令行选项， 以及配置文件 `/etc/inetd.conf` 来对 inetd 进行配置的一些基础知识。

### 30.2.2. 设置

inetd 是通过 [rc(8)](http://www.FreeBSD.org/cgi/man.cgi?query=rc&sektion=8&manpath=freebsd-release-ports) 系统启动的。 `inetd_enable` 选项默认设为 `NO`， 但可以在安装系统时， 由用户根据需要通过 sysinstall 来打开。 将：

```
inetd_enable="YES"
```

或

```
inetd_enable="NO"
```

写入 `/etc/rc.conf` 可以启用或禁用系统启动时 inetd 的自动启动。 命令：

```
# `/etc/rc.d/inetd rcvar`
```

可以显示目前的设置。

此外， 您还可以通过 `inetd_flags` 参数来向 inetd 传递额外的其它参数。

### 30.2.3. 命令行选项

与多数服务程序类似， inetd 也提供了为数众多的用以控制其行为的参数。 完整的参数列表如下：

`inetd` `[-d] [-l] [-w] [-W] [-c maximum] [-C rate] [-a address | hostname] [-p filename] [-R rate] [-s maximum] [configuration file]`

这些参数都可以通过 `/etc/rc.conf` 的 `inetd_flags` 选项来传给 inetd。 默认情况下， `inetd_flags` 设为 `-wW -C 60`， 者表示希望为 inetd 的服务启用 TCP wrapping， 并阻止来自同一 IP 每分钟超过 60 次的请求。

虽然我们会在下面介绍关于限制连接频率的选项， 但初学的用户可能会很高兴地发现这些参数通常并不需要进行修改。 在收到超大量的连接请求时， 这些选项则有可能会发挥作用。 完整的参数列表， 可以在 [inetd(8)](http://www.FreeBSD.org/cgi/man.cgi?query=inetd&sektion=8&manpath=freebsd-release-ports) 联机手册中找到。

-c maximum

指定单个服务的最大并发访问数量，默认为不限。 也可以在此服务的具体配置里面通过`max-child`改掉。

-C rate

指定单个服务一分钟内能被单个 IP 地址调用的最大次数， 默认不限。也可以在此服务的具体配置里面通过`max-connections-per-ip-per-minute` 改掉。

-R rate

指定单个服务一分钟内能被调用的最大次数，默认为 256。 设为 0 则允许不限次数调用。

-s maximum

指定同一 IP 同时请求同一服务时允许的最大值； 默认值为不限制。 您可以通过 `max-child-per-ip` 参数来以服务为单位进行限制。

### 30.2.4. `inetd.conf`

对于 inetd 的配置， 是通过 `/etc/inetd.conf` 文件来完成的。

在修改了 `/etc/inetd.conf` 之后， 可以使用下面的命令来强制 inetd 重新读取配置文件：

例 30.1. 重新加载 inetd 配置文件

```
# `/etc/rc.d/inetd reload`
```

配置文件中的每一行都是一个独立的服务程序。 在这个文件中， 前面有 “#” 的内容被认为是注释。 `/etc/inetd.conf` 文件的格式如下：

```
service-name
socket-type
protocol
{wait|nowait}[/max-child[/max-connections-per-ip-per-minute[/max-child-per-ip]]]
user[:group][/login-class]
server-program
server-program-arguments
```

下面是针对 IPv4 的 [ftpd(8)](http://www.FreeBSD.org/cgi/man.cgi?query=ftpd&sektion=8&manpath=freebsd-release-ports) 服务的例子：

```
ftp     stream  tcp     nowait  root    /usr/libexec/ftpd       ftpd -l
```

service-name

指明各个服务的服务名。其服务名必须与`/etc/services`中列出的一致。 这将决定 inetd 会监听哪个 port。 一旦有新的服务需要添加，必须先在`/etc/services`里面添加。

socket-type

可以是`stream`、`dgram`、`raw`或者 `seqpacket`。 `stream` 用于基于连接的 TCP 服务；而 `dgram` 则用于使用 UDP 协议的服务。

protocol

下列之一：

| 协议 | 说明 |
| --- | --- |
| tcp， tcp4 | TCP IPv4 |
| udp， udp4 | UDP IPv4 |
| tcp6 | TCP IPv6 |
| udp6 | UDP IPv6 |
| tcp46 | Both TCP IPv4 and v6 |
| udp46 | Both UDP IPv4 and v6 |

{wait|nowait}[/max-child[/max-connections-per-ip-per-minute[/max-child-per-ip]]]

`wait|nowait` 指明从 inetd 里头调用的服务是否可以自己处理 socket. `dgram`socket 类型必须使用`wait`， 而 stream socket daemons， 由于通常使用多线程方式，应当使用 `nowait`. `wait` 通常把多个 socket 丢给单个服务进程， 而 `nowait` 则 会为每个新的 socket 生成一个子进程。

`max-child` 选项能够配置 inetd 能为本服务派生出的最大子进程数量。 如果某特定服务需要限定最高 10 个实例， 把`/10` 放到`nowait`后头就可以了。 指定 `/0` 表示不限制子进程的数量。

除了 `max-child` 之外， 还有两个选项可以限制来自同一位置到特定服务的最大连接数。 `max-connections-per-ip-per-minute` 可以限制特定 IP 地址每分钟的总连接数， 例如， 限制任何 IP 地址每分钟最多连接十次。 `max-child-per-ip` 则可以限制为某一 IP 地址在任何时候所启动的子进程数量。 这些选项对于防止针对服务器有意或无意的资源耗竭和拒绝服务 (DoS) 攻击十分有用。

这个字段中， 必须指定 `wait` 或 `nowait` 两者之一。 而 `max-child`、 `max-connections-per-ip-per-minute` 和 `max-child-per-ip` 则是可选项。

流式多线程服务， 并且不配置任何 `max-child`、 `max-connections-per-ip-per-minute` 或 `max-child-per-ip` 限制时， 其配置为： `nowait`。

同一个服务， 但希望将服务启动的数量限制为十个时， 则是： `nowait/10`。

同样配置， 限制每个 IP 地址每分钟最多连接二十次， 而同时启动的子进程最多十个， 应写作： `nowait/10/20`。

下面是 [fingerd(8)](http://www.FreeBSD.org/cgi/man.cgi?query=fingerd&sektion=8&manpath=freebsd-release-ports) 服务的默认配置：

```
finger stream  tcp     nowait/3/10 nobody /usr/libexec/fingerd fingerd -s
```

最后这个例子中， 将子进程数限制为 100 个， 而任意 IP 最多同时建立 5 个连接： `nowait/100/0/5`。

user

该开关指定服务将以什么用户身份运行。一般而言，服务运行身份是 `root`。基于安全目的，可以看到有些服务以 `daemon`身份，或者是最小特权的 `nobody`身份运行。

server-program

当连接到来时，执行服务程序的全路径。如果服务是由 inetd 内置提供的，以`internal`代替。

server-program-arguments

当`server-program`调用到时，该开关 的值通过`argv[0]`通过传递给服务而工作。 如果命令行为：`mydaemon -d`，则 `mydaemon -d`为`server-program-arguments` 开关的值。同样的，如果服务是由 inetd 内置提供的，这里还是 `internal`。

### 30.2.5. Security

随安装时所选的模式不同， 许多 inetd 的服务可能已经默认启用。 如果确实不需要某个特定的服务， 则应考虑禁用它。 在 `/etc/inetd.conf` 中， 将对应服务的那行前面加上 “#”， 然后 重新加载 inetd 配置 就可以了。 某些服务， 例如 fingerd， 可能是完全不需要的， 因为它们提供的信息可能对攻击者有用。

某些服务在设计时是缺少安全意识的， 或者有过长或压根没有连接请求的超时机制。 这使得攻击者能够通过缓慢地对这些服务发起连接， 并耗尽可用的资源。 对于这种情况， 设置 `max-connections-per-ip-per-minute`、 `max-child` 或 `max-child-per-ip` 限制， 来制约服务的行为是个好办法。

默认情况下，TCP wrapping 是打开的。参考 [hosts_access(5)](http://www.FreeBSD.org/cgi/man.cgi?query=hosts_access&sektion=5&manpath=freebsd-release-ports) 手册，以获得更多关于在各种 inetd 调用的服务上设置 TCP 限制的信息。

### 30.2.6. 杂项

daytime、 time、 echo、 discard、 chargen， 以及 auth 都是由 inetd 提供的内建服务。

auth 服务提供了网络身份服务， 它可以配置为提供不同级别的服务， 而其它服务则通常只能简单的打开或关闭。

参考 [inetd(8)](http://www.FreeBSD.org/cgi/man.cgi?query=inetd&sektion=8&manpath=freebsd-release-ports) 手册获得更多信息。

## 30.3. 网络文件系统（NFS）

Reorganized and enhanced by Tom Rhodes.Written by Bill Swingle.

网络文件系统是 FreeBSD 支持的文件系统中的一种， 也被称为 NFS。 NFS 允许一个系统在网络上与它人共享目录和文件。通过使用 NFS，用户和程序可以象访问本地文件 一样访问远端系统上的文件。

以下是 NFS 最显而易见的好处：

*   本地工作站使用更少的磁盘空间，因为通常的数据可以存放在一 台机器上而且可以通过网络访问到。

*   用户不必在每个网络上机器里头都有一个 home 目录。Home 目录 可以被放在 NFS 服务器上并且在网络上处处可用。

*   诸如软驱，CDROM，和 Zip® 之类的存储设备可以在网络上面被别的机器使用。 这可以减少整个网络上的可移动介质设备的数量。

### 30.3.1. NFS 是如何工作的

NFS 至少包括两个主要的部分： 一台服务器， 以及至少一台客户机， 客户机远程地访问保存在服务器上的数据。 要让这一切运转起来， 需要配置并运行几个程序。

服务器必须运行以下服务：

| 服务 | 描述 |
| --- | --- |
| nfsd | NFS，为来自 NFS 客户端的 请求服务。 |
| mountd | NFS 挂载服务，处理[nfsd(8)](http://www.FreeBSD.org/cgi/man.cgi?query=nfsd&sektion=8&manpath=freebsd-release-ports)递交过来的请求。 |
| rpcbind | 此服务允许 NFS 客户程序查询正在被 NFS 服务使用的端口。 |

客户端同样运行一些进程，比如 nfsiod。 nfsiod 处理来自 NFS 的请求。 这是可选的，而且可以提高性能，对于普通和正确的操作来说并不是必须的。 参考[nfsiod(8)](http://www.FreeBSD.org/cgi/man.cgi?query=nfsiod&sektion=8&manpath=freebsd-release-ports)手册获得更多信息。

### 30.3.2. 配置 NFS

NFS 的配置过程相对简单。这个过程只需要 对`/etc/rc.conf`文件作一些简单修改。

在 NFS 服务器这端，确认`/etc/rc.conf` 文件里头以下开关都配上了:

```
rpcbind_enable="YES"
nfs_server_enable="YES"
mountd_flags="-r"
```

只要 NFS 服务被置为 enable，mountd 就能自动运行。

在客户端一侧，确认下面这个开关出现在 `/etc/rc.conf`里头:

```
nfs_client_enable="YES"
```

`/etc/exports`文件指定了哪个文件系统 NFS 应该输出（有时被称为“共享”）。 `/etc/exports`里面每行指定一个输出的文件系统和 哪些机器可以访问该文件系统。在指定机器访问权限的同时，访问选项 开关也可以被指定。有很多开关可以被用在这个文件里头，不过不会在这 里详细谈。您可以通过阅读[exports(5)](http://www.FreeBSD.org/cgi/man.cgi?query=exports&sektion=5&manpath=freebsd-release-ports) 手册来发现这些开关。

以下是一些`/etc/exports`的例子：

下面是一个输出文件系统的例子， 不过这种配置与您所处的网络环境及其配置密切相关。 例如， 如果要把 `/cdrom` 输出给与服务器域名相同的三台计算机 (因此例子中只有机器名， 而没有给出这些计算机的域名)， 或在 `/etc/hosts` 文件中进行了这种配置。 `-ro` 标志表示把输出的文件系统置为只读。 由于使用了这个标志， 远程系统在输出的文件系统上就不能写入任何变动了。

```
/cdrom -ro host1 host2 host3
```

下面的例子可以输出`/home`给三个以 IP 地址方式表示的主机。 对于在没有配置 DNS 服务器的私有网络里头，这很有用。 此外， `/etc/hosts` 文件也可以用以配置主机名；参看 [hosts(5)](http://www.FreeBSD.org/cgi/man.cgi?query=hosts&sektion=5&manpath=freebsd-release-ports) 。 `-alldirs` 标记允许子目录被作为挂载点。 也就是说，客户端可以根据需要挂载需要的目录。

```
/home  -alldirs  10.0.0.2 10.0.0.3 10.0.0.4
```

下面几行输出 `/a` ，以便两个来自不同域的客户端可以访问文件系统。 `-maproot=root` 标记授权远端系统上的 `root` 用户在被输出的文件系统上以`root`身份进行读写。 如果没有特别指定 `-maproot=root` 标记， 则即使用户在远端系统上是 `root` 身份， 也不能修改被输出文件系统上的文件。

```
/a  -maproot=root  host.example.com box.example.org
```

为了能够访问到被输出的文件系统，客户端必须被授权。 请确认客户端在您的 `/etc/exports` 被列出。

在 `/etc/exports` 里头，每一行里面，输出信息和文件系统一一对应。 一个远程主机每次只能对应一个文件系统。而且只能有一个默认入口。比如，假设 `/usr` 是独立的文件系统。这个 `/etc/exports` 就是无效的：

```
# Invalid when /usr is one file system
/usr/src   client
/usr/ports client
```

一个文件系统，`/usr`， 有两行指定输出到同一主机， `client`. 解决这一问题的正确的格式是：

```
/usr/src /usr/ports  client
```

在同一文件系统中， 输出到指定客户机的所有目录， 都必须写到同一行上。 没有指定客户机的行会被认为是单一主机。 这限制了你可以怎样输出的文件系统， 但对绝大多数人来说这不是问题。

下面是一个有效输出列表的例子， `/usr` 和 `/exports` 是本地文件系统：

```
# Export src and ports to client01 and client02, but only
# client01 has root privileges on it
/usr/src /usr/ports -maproot=root    client01
/usr/src /usr/ports               client02
# The client machines have root and can mount anywhere
# on /exports. Anyone in the world can mount /exports/obj read-only
/exports -alldirs -maproot=root      client01 client02
/exports/obj -ro
```

在修改了 `/etc/exports` 文件之后， 就必须让 mountd 服务重新检查它， 以便使修改生效。 一种方法是通过给正在运行的服务程序发送 HUP 信号来完成：

```
# `kill -HUP `cat /var/run/mountd.pid``
```

或指定适当的参数来运行 `mountd` [rc(8)](http://www.FreeBSD.org/cgi/man.cgi?query=rc&sektion=8&manpath=freebsd-release-ports) 脚本：

```
# `/etc/rc.d/mountd onereload`
```

关于使用 rc 脚本的细节， 请参见 第 12.7 节 “在 FreeBSD 中使用 rc”。

另外， 系统重启动可以让 FreeBSD 把一切都弄好。 尽管如此， 重启不是必须的。 以 `root` 身份执行下面的命令可以搞定一切。

在 NFS 服务器端：

```
# `rpcbind`
# `nfsd -u -t -n 4`
# `mountd -r`
```

在 NFS 客户端：

```
# `nfsiod -n 4`
```

现在每件事情都应该就绪，以备挂载一个远端文件系统。 在这些例子里头， 服务器名字将是：`server` ，而客户端的名字将是： `client`。 如果您只打算临时挂载一个远端文件系统或者只是打算作测试配置正确与否， 只要在客户端以 `root` 身份执行下面的命令：

```
# `mount server:/home /mnt`
```

这条命令会把服务端的 `/home` 目录挂载到客户端的 `/mnt` 上。 如果配置正确，您应该可以进入客户端的 `/mnt` 目录并且看到所有服务端的文件。

如果您打算让系统每次在重启动的时候都自动挂载远端的文件系统，把那个文件系统加到 `/etc/fstab` 文件里头去。下面是例子：

```
server:/home	/mnt	nfs	rw	0	0
```

[fstab(5)](http://www.FreeBSD.org/cgi/man.cgi?query=fstab&sektion=5&manpath=freebsd-release-ports) 手册里有所有可用的开关。

### 30.3.3. 锁

某些应用程序 (例如 mutt) 需要文件上锁支持才能正常运行。 在使用 NFS 时， 可以用 rpc.lockd 来支持文件上锁功能。 要启用它， 需要在服务器和客户机的 `/etc/rc.conf` 中加入 (假定两端均已配好了 NFS)：

```
rpc_lockd_enable="YES"
rpc_statd_enable="YES"
```

然后使用下述命令启动该程序：

```
# `/etc/rc.d/lockd start`
# `/etc/rc.d/statd start`
```

如果并不需要真的在 NFS 客户机和 NFS 服务器间确保上锁的语义， 可以让 NFS 客户机在本地上锁， 方法是使用 [mount_nfs(8)](http://www.FreeBSD.org/cgi/man.cgi?query=mount_nfs&sektion=8&manpath=freebsd-release-ports) 时指定 `-L` 参数。 请参见 [mount_nfs(8)](http://www.FreeBSD.org/cgi/man.cgi?query=mount_nfs&sektion=8&manpath=freebsd-release-ports) 联机手册以了解更多细节。

### 30.3.4. 实际应用

NFS 有很多实际应用。下面是比较常见的一些：

*   多个机器共享一台 CDROM 或者其他设备。这对于在多台机器中安装软件来说更加便宜跟方便。

*   在大型网络中，配置一台中心 NFS 服务器用来放置所有用户的 home 目录可能会带来便利。 这些目录能被输出到网络以便用户不管在哪台工作站上登录，总能得到相同的 home 目录。

*   几台机器可以有通用的`/usr/ports/distfiles` 目录。 这样的话，当您需要在几台机器上安装 port 时，您可以无需在每台设备上下载而快速访问源码。

### 30.3.5. 通过 amd 自动地挂接

Contributed by Wylie Stilwell.Rewritten by Chern Lee.

[amd(8)](http://www.FreeBSD.org/cgi/man.cgi?query=amd&sektion=8&manpath=freebsd-release-ports) (自动挂接服务) 能够自动地在访问时挂接远程的文件系统。 如果文件系统在一段时间之内没有活动， 则会被 amd 自动卸下。 通过使用 amd， 能够提供一个持久挂接以外的选择， 而后者往往需要列入 `/etc/fstab`。

amd 通过将自己以 NFS 服务器的形式， 附加到 `/host` 和 `/net` 目录上来工作。 当访问这些目录中的文件时， amd 将查找相应的远程挂接点， 并自动地挂接。 `/net` 用于挂接远程 IP 地址上导出的文件系统， 而 `/host` 则用于挂接远程主机名上的文件系统。

访问 `/host/foobar/usr` 中的文件， 相当于告诉 amd 尝试挂接在主机 `foobar` 上导出的 `/usr`。

例 30.2. 通过 amd 来挂接导出的文件系统

您可以通过使用 `showmount` 命令来查看远程主机上导出的文件系统。 例如， 要查看 `foobar` 上导出的文件系统， 可以用：

```
% `showmount -e foobar`
Exports list on foobar:
/usr                               10.10.10.0
/a                                 10.10.10.0
% `cd /host/foobar/usr`
```

如同在前面例子中所看到的， `showmount` 显示了导出的 `/usr`。 当进入 `/host/foobar/usr` 这个目录时， amd 将尝试解析主机名 `foobar` 并自动地挂接需要的文件系统导出。

amd 可以通过启动脚本来启动， 方法是在 `/etc/rc.conf` 中加入：

```
amd_enable="YES"
```

除此之外， 还可以给 amd 通过 `amd_flags` 选项来传递额外的参数。 默认情况下， `amd_flags` 为：

```
amd_flags="-a /.amd_mnt -l syslog /host /etc/amd.map /net /etc/amd.map"
```

`/etc/amd.map` 文件定义了挂接导出文件系统时所使用的默认选项。 `/etc/amd.conf` 文件， 则定义了更多关于 amd 的高级功能选项。

请参考 [amd(8)](http://www.FreeBSD.org/cgi/man.cgi?query=amd&sektion=8&manpath=freebsd-release-ports) 和 [amd.conf(5)](http://www.FreeBSD.org/cgi/man.cgi?query=amd.conf&sektion=5&manpath=freebsd-release-ports) 联机手册， 以了解进一步的情况。

### 30.3.6. 与其他系统集成时的常见问题

Contributed by John Lind.

某些特定的 ISA PC 系统上的以太网适配器上有一些限制， 这些限制可能会导致严重的网络问题， 特别是与 NFS 配合使用时。 这些问题并非 FreeBSD 所特有的， 但 FreeBSD 系统会受到这些问题的影响。

这样的问题， 几乎总是在当 (FreeBSD) PC 系统与高性能的工作站， 例如 Silicon Graphics, Inc., 和 Sun Microsystems, Inc. 的工作站联网时发生。 NFS 挂接能够正常工作， 而且一些操作也可能成功， 但服务器会很快变得对客户机不太理会， 虽然对其他客户机的请求仍然能够正常处理。 这种情况通常发生在客户端， 无论它是一个 FreeBSD 系统或是终端。 在许多系统上， 一旦发生了这样的问题， 通常没办法正常地关闭客户机。 唯一的办法通常是让终端复位， 因为这一 NFS 状况没有办法被解决。

尽管 “正确的” 解决办法， 是为 FreeBSD 系统配备一块高性能的、 适用的以太网适配器， 然而也有办法绕过问题并得到相对满意的结果。 如果 FreeBSD 系统是 *服务器*， 则在客户机挂接时， 应该指定 `-w=1024`。 如果 FreeBSD 系统是 *客户机*， 则应加入 `-r=1024` 参数。 这些选项可以通过在对应的 `fstab` 的第四个字段加入， 以便让客户机能够自动地挂接， 或者通过 [mount(8)](http://www.FreeBSD.org/cgi/man.cgi?query=mount&sektion=8&manpath=freebsd-release-ports) 的 `-o` 参数在手工挂接时指定。

还需要注意的是另一个问题， 有时会被误认为是和上面一样的问题。 这个问题多见于 NFS 服务器和客户机在不同的网络上时。 如果是这种情况， 一定要 *确定* 您的路由器确实把必需的 UDP 信息路由到了目的地， 否则您将什么也做不了。

下面的例子中， `fastws` 是主机 (接口) 的名字， 它是一台高性能的终端， 而 `freebox` 是另一台主机 (接口) 的名字， 它是一个使用较低性能的以太网适配器的 FreeBSD 系统。 同时， `/sharedfs` 将被导出成为 NFS 文件系统 (参见 [exports(5)](http://www.FreeBSD.org/cgi/man.cgi?query=exports&sektion=5&manpath=freebsd-release-ports))， 而 `/project` 将是客户机上挂接这一导出文件系统的挂接点。 所有的应用场景中， 请注意附加选项， 例如 `hard` 或 `soft` 以及 `bg` 可能是您的应用所需要的。

关于 FreeBSD 系统 (`freebox`) 作为客户机的示范 `/etc/fstab` 文件， 见于 `freebox` 之上：

```
fastws:/sharedfs /project nfs rw,-r=1024 0 0
```

在 `freebox` 上手工挂接：

```
# `mount -t nfs -o -r=1024 fastws:/sharedfs /project`
```

以 FreeBSD 系统作为服务器的例子， 是 `fastws` 上的 `/etc/fstab`：

```
freebox:/sharedfs /project nfs rw,-w=1024 0 0
```

在 `fastws` 上手工挂接的命令是：

```
# `mount -t nfs -o -w=1024 freebox:/sharedfs /project`
```

几乎所有的 16-位 以太网控制器， 都能够在没有上述读写尺寸限制的情况下正常工作。

对于那些关心到底是什么问题的人， 下面是失败如何发生的解释， 同时这也说明了为什么这是一个无法恢复的问题。 典型情况下， NFS 会使用一个 “块” 为单位进行操作， 其尺寸是 8 K (虽然它可能会将操作分成更小尺寸的分片)。 由于最大的以太网包尺寸大约是 1500 字节， 因此 NFS “块” 会分成多个以太网包， 虽然在更高层的代码看来它仍然是一个完整的单元， 并在接收方重新组装， 作为一个整体来 *确认*。 高性能的工作站， 可以将构成 NFS 单元的包迅速发出， 其节奏会快到标准允许的最大限度。 在容量较小的卡上， 后来的包会冲掉同一单元内的较早的包， 因而整个单元无法被重建或确认。 其结果是， 工作站将超时并重试， 但仍然是完整的 8 K 单元， 这一过程将无休止地重复下去。

如果将单元尺寸限制在以太网包尺寸之下， 我们就能够确保每一个以太网包都能够被独立地接收和确认， 从而避免了上面的死锁情形。

溢出在高性能工作站将数据库投向 PC 系统时仍会发生， 但在更好的网卡上， 能够保证这类溢出不会在每一个 NFS “单元” 上都发生。 当出现溢出时， 被影响的单元被重传， 因而此时有很大的机会它将被正确接收、 重组， 并确认。

## 30.4. 网络信息服务 (NIS/YP)

Written by Bill Swingle.Enhanced by Eric Ogren 和 Udo Erdelhoff.

### 30.4.1. 它是什么？

NIS， 表示网络信息服务 (Network Information Services)， 最初由 Sun Microsystems 开发， 用于 UNIX® (最初是 SunOS™) 系统的集中管理。 目前， 它基本上已经成为了业界标准； 所有主流的类 UNIX® 系统 (Solaris™, HP-UX, AIX®, Linux, NetBSD, OpenBSD, FreeBSD, 等等) 都支持 NIS。

NIS 也就是人们所熟知的黄页(Yellow Pages)， 但由于商标的问题， Sun 将其改名为现在的名字。 旧的术语 (以及 yp)， 仍然经常可以看到， 并被广泛使用。

这是一个基于 RPC 的客户机/服务器系统， 它允许在一个 NIS 域中的一组机器共享一系列配置文件。 这样， 系统管理员就可以配置只包含最基本配置数据的 NIS 客户机系统， 并在单点上增加、 删除或修改配置数据。

尽管实现的内部细节截然不同， 这和 Windows NT® 域系统非常类似， 以至于可以将两者的基本功能相互类比。

### 30.4.2. 您应该知道的术语和进程

有一系列术语和重要的用户进程将在您在 FreeBSD 上实现 NIS 时用到， 无论是在创建 NIS 服务器， 或作为 NIS 客户机：

| 术语 | 说明 |
| --- | --- |
| NIS 域名 | NIS 主服务器和所有其客户机 (包括从服务器) 会使用同一 NIS 域名。 和 Windows NT® 域名类似， NIS 域名与 DNS 无关。 |
| rpcbind | 必须运行这个程序， 才能够启用 RPC (远程过程调用， NIS 用到的一种网络协议)。 如果没有运行 rpcbind， 则没有办法运行 NIS 服务器， 或作为 NIS 客户机。 |
| ypbind | “绑定(bind)” NIS 客户机到它的 NIS 服务器上。 这样， 它将从系统中获取 NIS 域名， 并使用 RPC 连接到服务器上。 ypbind 是 NIS 环境中， 客户机-服务器通讯的核心； 如果客户机上的 ypbind 死掉的话， 它将无法访问 NIS 服务器。 |
| ypserv | 只应在 NIS 服务器上运行它； 这是 NIS 的服务器进程。 如果 [ypserv(8)](http://www.FreeBSD.org/cgi/man.cgi?query=ypserv&sektion=8&manpath=freebsd-release-ports) 死掉的话， 则服务器将不再具有响应 NIS 请求的能力 (此时， 如果有从服务器的话， 则会接管操作)。 有一些 NIS 的实现 (但不是 FreeBSD 的这个) 的客户机上， 如果之前用过一个服务器， 而那台服务器死掉的话， 并不尝试重新连接到另一个服务器。 通常， 发生这种情况时， 唯一的办法就是重新启动服务器进程 (或者， 甚至重新启动服务器) 或客户机上的 ypbind 进程。 |
| rpc.yppasswdd | 另一个只应在 NIS 主服务器上运行的进程； 这是一个服务程序， 其作用是允许 NIS 客户机改变它们的 NIS 口令。 如果没有运行这个服务， 用户将必须登录到 NIS 主服务器上， 并在那里修改口令。 |

### 30.4.3. 它是如何工作的？

在 NIS 环境中， 有三种类型的主机： 主服务器， 从服务器， 以及客户机。 服务器的作用是充当主机配置信息的中央数据库。 主服务器上保存着这些信息的权威副本， 而从服务器则是保存这些信息的冗余副本。 客户机依赖于服务器向它们提供这些信息。

许多文件的信息可以通过这种方式来共享。 通常情况下， `master.passwd`、 `group`， 以及 `hosts` 是通过 NIS 分发的。 无论什么时候， 如果客户机上的某个进程请求这些本应在本地的文件中的资料的时候， 它都会向所绑定的 NIS 服务器发出请求， 而不使用本地的版本。

#### 30.4.3.1. 机器类型

*   一台 *NIS 主服务器*。 这台服务器， 和 Windows NT® 域控制器类似， 会维护所有 NIS 客户机所使用的文件。 `passwd`， `group`， 以及许多其他 NIS 客户机所使用的文件， 都被存放到主服务器上。

    ### 注意:

    可以将一台 NIS 主服务器用在多个 NIS 域中。 然而， 本书不打算对这种配置进行介绍， 因为这种配置， 通常只出现在小规模的 NIS 环境中。

*   *NIS 从服务器*。 这一概念， 与 Windows NT® 的备份域控制器类似。 NIS 从服务器， 用于维护 NIS 主服务器的数据文件副本。 NIS 从服务器提供了一种冗余， 这在许多重要的环境中是必需的。 此外， 它也帮助减轻了主服务器的负荷： NIS 客户机总是挂接到最先响应它们的 NIS 服务器上， 而这也包括来自从服务器的响应。

*   *NIS 客户机*。 NIS 客户机， 和多数 Windows NT® 工作站类似， 通过 NIS 服务器 (或对于 Windows NT® 工作站， 则是 Windows NT® 域控制器) 来完成登录时的身份验证过程。

### 30.4.4. 使用 NIS/YP

这一节将通过实例介绍如何配置 NIS 环境。

#### 30.4.4.1. 规划

假定您正在管理大学中的一个小型实验室。 在这个实验室中， 有 15 台 FreeBSD 机器， 目前尚没有集中的管理点； 每一台机器上有自己的 `/etc/passwd` 和 `/etc/master.passwd`。 这些文件通过人工干预的方法来保持与其他机器上版本的同步； 目前， 如果您在实验室中增加一个用户， 将不得不在所有 15 台机器上手工执行 `adduser` 命令。 毋庸置疑， 这一现状必须改变， 因此您决定将整个实验室转为使用 NIS， 并使用两台机器作为服务器。

因此， 实验室的配置应该是这样的：

| 机器名 | IP 地址 | 机器的角色 |
| --- | --- | --- |
| `ellington` | `10.0.0.2` | NIS 主服务器 |
| `coltrane` | `10.0.0.3` | NIS 从服务器 |
| `basie` | `10.0.0.4` | 教员工作站 |
| `bird` | `10.0.0.5` | 客户机 |
| `cli[1-11]` | `10.0.0.[6-17]` | 其他客户机 |

如果您是首次配置 NIS， 仔细思考如何进行规划就十分重要。 无论您的网络的大小如何， 都必须进行几个决策。

##### 30.4.4.1.1. 选择 NIS 域名

这可能不是您过去使用的 “域名(domainname)”。 它的规范的叫法， 应该是 “NIS 域名”。 当客户机广播对此信息的请求时， 它会将 NIS 域的名字作为请求的一部分发出。 这样， 统一网络上的多个服务器， 就能够知道谁应该回应请求。 您可以把 NIS 域名想象成以某种方式相关的一组主机的名字。

一些机构会选择使用它们的 Internet 域名来作为 NIS 域名。 并不推荐这样做， 因为在调试网络问题时， 这可能会导致不必要的困扰。 NIS 域名应该是在您网络上唯一的， 并且有助于了解它所描述的到底是哪一组机器。 例如对于 Acme 公司的美工部门， 可以考虑使用 “acme-art” 这样的 NIS 域名。 在这个例子中， 您使用的域名是 `test-domain`。

然而， 某些操作系统 (最著名的是 SunOS™) 会使用其 NIS 域名作为 Internet 域名。 如果您的网络上存在包含这类限制的机器， 就 *必须* 使用 Internet 域名来作为您的 NIS 域名。

##### 30.4.4.1.2. 服务器的物理要求

选择 NIS 服务器时， 需要时刻牢记一些东西。 NIS 的一个不太好的特性就是其客户机对于服务器的依赖程度。 如果客户机无法与其 NIS 域的服务器联系， 则这台机器通常会陷于不可用的状态。 缺少用户和组信息， 会使绝大多数系统进入短暂的冻结状态。 基于这样的考虑， 您需要选择一台不经常重新启动， 或用于开发的机器来承担其责任。 如果您的网络不太忙， 也可以使用运行着其他服务的机器来安放 NIS 服务， 只是需要注意， 一旦 NIS 服务器不可用， 则 *所有* 的 NIS 客户机都会受到影响。

#### 30.4.4.2. NIS 服务器

所有的 NIS 信息的正规版本， 都被保存在一台单独的称作 NIS 主服务器的机器上。 用于保存这些信息的数据库， 称为 NIS 映射(map)。 在 FreeBSD 中， 这些映射被保存在 `/var/yp/[domainname]` 里， 其中 `[domainname]` 是提供服务的 NIS 域的名字。 一台 NIS 服务器， 可以同时支持多个域， 因此可以建立很多这样的目录， 所支撑一个域对应一个。 每一个域都会有一组独立的映射。

NIS 主和从服务器， 通过 `ypserv` 服务程序来处理所有的 NIS 请求。 `ypserv` 有责任接收来自 NIS 客户机的请求， 翻译请求的域， 并将名字映射为相关的数据库文件的路径， 然后将来自数据库的数据传回客户机。

##### 30.4.4.2.1. 配置 NIS 主服务器

配置主 NIS 服务器相对而言十分的简单， 而其具体步骤则取决于您的需要。 FreeBSD 提供了一步到位的 NIS 支持。 您需要做的全部事情， 只是在 `/etc/rc.conf` 中加入一些配置， 其他工作会由 FreeBSD 完成。

1.  ```
    nisdomainname="test-domain"
    ```

    这一行将在网络启动 (例如重新启动) 时， 把 NIS 域名配置为 `test-domain`。

2.  ```
    nis_server_enable="YES"
    ```

    这将要求 FreeBSD 在网络子系统启动之后立即启动 NIS 服务进程。

3.  ```
    nis_yppasswdd_enable="YES"
    ```

    这将启用 `rpc.yppasswdd` 服务程序， 如前面提到的， 它允许用户在客户机上修改自己的 NIS 口令。

### 注意:

随 NIS 配置的不同， 可能还需要增加其他一些项目。 请参见 关于 NIS 服务器同时充当 NIS 客户机 这一节， 以了解进一步的情况。

设置好前面这些配置之后， 需要以超级用户身份运行 `/etc/netstart` 命令。 它会根据 `/etc/rc.conf` 的设置来配置系统中的其他部分。 最后， 在初始化 NIS 映射之前， 还需要手工启动 ypserv 服务程序：

```
# `/etc/rc.d/ypserv start`
```

##### 30.4.4.2.2. 初始化 NIS 映射

*NIS 映射* 是一些数据库文件， 它们位于 `/var/yp` 目录中。 这些文件基本上都是根据 NIS 主服务器的 `/etc` 目录自动生成的， 唯一的例外是： `/etc/master.passwd` 文件。 一般来说， 您会有非常充分的理由不将 `root` 以及其他管理帐号的口令发到所有 NIS 域上的服务器上。 因此， 在开始初始化 NIS 映射之前， 我们应该：

```
# `cp /etc/master.passwd /var/yp/master.passwd`
# `cd /var/yp`
# `vi master.passwd`
```

这里， 删除掉和系统有关的帐号对应的项 (`bin`、 `tty`、 `kmem`、 `games`， 等等)， 以及其他不希望被扩散到 NIS 客户机的帐号 (例如 `root` 和任何其他 UID 0 (超级用户) 的帐号)。

### 注意:

确认 `/var/yp/master.passwd` 这个文件是同组用户， 以及其他用户不可读的 (模式 600)！ 如果需要的话， 用 `chmod` 命令来改它。

完成这些工作之后， 就可以初始化 NIS 映射了！ FreeBSD 提供了一个名为 `ypinit` 的脚本来帮助您完成这项工作 (详细信息， 请见其联机手册)。 请注意， 这个脚本在绝大多数 UNIX® 操作系统上都可以找到， 但并不是所有操作系统的都提供。 在 Digital UNIX/Compaq Tru64 UNIX 上它的名字是 `ypsetup`。 由于我们正在生成的是 NIS 主服务器的映射， 因此应该使用 `ypinit` 的 `-m` 参数。 如果已经完成了上述步骤， 要生成 NIS 映射， 只需执行：

```
ellington# `ypinit -m test-domain`
Server Type: MASTER Domain: test-domain
Creating an YP server will require that you answer a few questions.
Questions will all be asked at the beginning of the procedure.
Do you want this procedure to quit on non-fatal errors? [y/n: n] `n`
Ok, please remember to go back and redo manually whatever fails.
If you don't, something might not work.
At this point, we have to construct a list of this domains YP servers.
rod.darktech.org is already known as master server.
Please continue to add any slave servers, one per line. When you are
done with the list, type a <control D>.
master server   :  ellington
next host to add:  `coltrane`
next host to add:  `^D`
The current list of NIS servers looks like this:
ellington
coltrane
Is this correct?  [y/n: y] `y`

[..output from map generation..]

NIS Map update completed.
ellington has been setup as an YP master server without any errors.
```

`ypinit` 应该会根据 `/var/yp/Makefile.dist` 来创建 `/var/yp/Makefile` 文件。 创建完之后， 这个文件会假定您正在操作只有 FreeBSD 机器的单服务器 NIS 环境。 由于 `test-domain` 还有一个从服务器， 您必须编辑 `/var/yp/Makefile`：

```
ellington# `vi /var/yp/Makefile`
```

应该能够看到这样一行， 其内容是

```
NOPUSH = "True"
```

(如果还没有注释掉的话)。

##### 30.4.4.2.3. 配置 NIS 从服务器

配置 NIS 从服务器， 甚至比配置主服务器还要简单。 登录到从服务器上， 并按照前面的方法， 编辑 `/etc/rc.conf` 文件。 唯一的区别是， 在运行 `ypinit` 时需要使用 `-s` 参数。 这里的 `-s` 选项， 同时要求提供 NIS 主服务器的名字， 因此我们的命令行应该是：

```
coltrane# `ypinit -s ellington test-domain`

Server Type: SLAVE Domain: test-domain Master: ellington

Creating an YP server will require that you answer a few questions.
Questions will all be asked at the beginning of the procedure.

Do you want this procedure to quit on non-fatal errors? [y/n: n]  `n`

Ok, please remember to go back and redo manually whatever fails.
If you don't, something might not work.
There will be no further questions. The remainder of the procedure
should take a few minutes, to copy the databases from ellington.
Transferring netgroup...
ypxfr: Exiting: Map successfully transferred
Transferring netgroup.byuser...
ypxfr: Exiting: Map successfully transferred
Transferring netgroup.byhost...
ypxfr: Exiting: Map successfully transferred
Transferring master.passwd.byuid...
ypxfr: Exiting: Map successfully transferred
Transferring passwd.byuid...
ypxfr: Exiting: Map successfully transferred
Transferring passwd.byname...
ypxfr: Exiting: Map successfully transferred
Transferring group.bygid...
ypxfr: Exiting: Map successfully transferred
Transferring group.byname...
ypxfr: Exiting: Map successfully transferred
Transferring services.byname...
ypxfr: Exiting: Map successfully transferred
Transferring rpc.bynumber...
ypxfr: Exiting: Map successfully transferred
Transferring rpc.byname...
ypxfr: Exiting: Map successfully transferred
Transferring protocols.byname...
ypxfr: Exiting: Map successfully transferred
Transferring master.passwd.byname...
ypxfr: Exiting: Map successfully transferred
Transferring networks.byname...
ypxfr: Exiting: Map successfully transferred
Transferring networks.byaddr...
ypxfr: Exiting: Map successfully transferred
Transferring netid.byname...
ypxfr: Exiting: Map successfully transferred
Transferring hosts.byaddr...
ypxfr: Exiting: Map successfully transferred
Transferring protocols.bynumber...
ypxfr: Exiting: Map successfully transferred
Transferring ypservers...
ypxfr: Exiting: Map successfully transferred
Transferring hosts.byname...
ypxfr: Exiting: Map successfully transferred

coltrane has been setup as an YP slave server without any errors.
Don't forget to update map ypservers on ellington.
```

现在应该会有一个叫做 `/var/yp/test-domain` 的目录。 在这个目录中， 应该保存 NIS 主服务器上的映射的副本。 接下来需要确定这些文件都及时地同步更新了。 在从服务器上， 下面的 `/etc/crontab` 项将帮助您确保这一点：

```
20      *       *       *       *       root   /usr/libexec/ypxfr passwd.byname
21      *       *       *       *       root   /usr/libexec/ypxfr passwd.byuid
```

这两行将强制从服务器将映射与主服务器同步。 由于主服务器会尝试确保所有其 NIS 映射的变动都知会从服务器， 因此这些项并不是绝对必需的。 不过， 由于保持其他客户端的口令信息正确性十分重要， 而这则依赖于从服务器， 强烈推荐明确指定让系统时常强制更新口令映射。 对于繁忙的网络而言， 这一点尤其重要， 因为有时可能出现映射更新不完全的情况。

现在， 在从服务器上执行 `/etc/netstart`， 就可以启动 NIS 服务了。

#### 30.4.4.3. NIS 客户机

NIS 客户机会通过 `ypbind` 服务程序来与特定的 NIS 服务器建立一种称作绑定的联系。 `ypbind` 会检查系统的默认域 (这是通过 `domainname` 命令来设置的)， 并开始在本地网络上广播 RPC 请求。 这些请求会指定 `ypbind` 尝试绑定的域名。 如果已经配置了服务器， 并且这些服务器接到了广播， 它将回应 `ypbind`， 后者则记录服务器的地址。 如果有多个可用的服务器 (例如一个主服务器， 加上多个从服务器)， `ypbind` 将使用第一个响应的地址。 从这一时刻开始， 客户机会把所有的 NIS 请求直接发给那个服务器。 `ypbind` 偶尔会 “ping” 服务器以确认其仍然在正常运行。 如果在合理的时间内没有得到响应， 则 `ypbind` 会把域标记为未绑定， 并再次发起广播， 以期找到另一台服务器。

##### 30.4.4.3.1. 设置 NIS 客户机

配置一台 FreeBSD 机器作为 NIS 客户机是非常简单的。

1.  编辑 `/etc/rc.conf` 文件， 并在其中加上下面几行， 以设置 NIS 域名， 并在网络启动时启动 `ypbind`：

    ```
    nisdomainname="test-domain"
    nis_client_enable="YES"
    ```

2.  要从 NIS 服务器导入所有的口令项， 需要从您的 `/etc/master.passwd` 文件中删除所有用户， 并使用 `vipw` 在这个文件的最后一行加入：

    ```
    +:::::::::
    ```

    ### 注意:

    这一行将让 NFS 服务器的口令映射中的帐号能够登录。 也有很多修改这一行来配置 NIS 客户机的办法。 请参见稍后的 netgroups 小节 以了解进一步的情况。 要了解更多信息， 可以参阅 O'Reilly 的 `Managing NFS and NIS` 这本书。

    ### 注意:

    需要至少保留一个本地帐号 (也就是不通过 NIS 导入) 在您的 `/etc/master.passwd` 文件中， 而这个帐号应该是 `wheel` 组的成员。 如果 NIS 发生不测， 这个帐号可以用来远程登录， 成为 `root`， 并修正问题。

3.  要从 NIS 服务器上导入组信息， 需要在 `/etc/group` 文件末尾加入：

    ```
    +:*::
    ```

想要立即启动 NIS 客户端， 需要以超级用户身份运行执行下列命令：

```
# `/etc/netstart`
# `/etc/rc.d/ypbind start`
```

完成这些步骤之后， 就应该可以通过运行 `ypcat passwd` 来看到 NIS 服务器的口令映射了。

### 30.4.5. NIS 的安全性

基本上， 任何远程用户都可以发起一个 RPC 到 [ypserv(8)](http://www.FreeBSD.org/cgi/man.cgi?query=ypserv&sektion=8&manpath=freebsd-release-ports) 并获得您的 NIS 映射的内容， 如果远程用户了解您的域名的话。 要避免这类未经授权的访问， [ypserv(8)](http://www.FreeBSD.org/cgi/man.cgi?query=ypserv&sektion=8&manpath=freebsd-release-ports) 支持一个称为 “securenets” 的特性， 用以将访问限制在一组特定的机器上。 在启动过程中， [ypserv(8)](http://www.FreeBSD.org/cgi/man.cgi?query=ypserv&sektion=8&manpath=freebsd-release-ports) 会尝试从 `/var/yp/securenets` 中加载 securenet 信息。

### 注意:

这个路径随 `-p` 参数改变。 这个文件包含了一些项， 每一项中包含了一个网络标识和子网掩码， 中间用空格分开。 以 “#” 开头的行会被认为是注释。 示范的 securenets 文件如下所示：

```
# allow connections from local host -- mandatory
127.0.0.1     255.255.255.255
# allow connections from any host
# on the 192.168.128.0 network
192.168.128.0 255.255.255.0
# allow connections from any host
# between 10.0.0.0 to 10.0.15.255
# this includes the machines in the testlab
10.0.0.0      255.255.240.0
```

如果 [ypserv(8)](http://www.FreeBSD.org/cgi/man.cgi?query=ypserv&sektion=8&manpath=freebsd-release-ports) 接到了来自匹配上述任一规则的地址的请求， 则它会正常处理请求。 反之， 则请求将被忽略， 并记录一条警告信息。 如果 `/var/yp/securenets` 文件不存在， 则 `ypserv` 会允许来自任意主机的请求。

`ypserv` 程序也支持 Wietse Venema 的 TCP Wrapper 软件包。 这样， 管理员就能够使用 TCP Wrapper 的配置文件来代替 `/var/yp/securenets` 完成访问控制。

### 注意:

尽管这两种访问控制机制都能够提供某种程度的安全， 但是， 和特权端口检查一样， 它们无法避免 “IP 伪造” 攻击。 您的防火墙应该阻止所有与 NIS 有关的访问。

使用 `/var/yp/securenets` 的服务器， 可能会无法为某些使用陈旧的 TCP/IP 实现的 NIS 客户机服务。 这些实现可能会在广播时， 将主机位都设置为 0， 或在计算广播地址时忽略子网掩码。 尽管这些问题可以通过修改客户机的配置来解决， 其他一些问题也可能导致不得不淘汰那些客户机系统， 或者不使用 `/var/yp/securenets`。

在使用陈旧的 TCP/IP 实现的系统上， 使用 `/var/yp/securenets` 是一个非常糟糕的做法， 因为这将导致您的网络上的 NIS 丧失大部分功能。

使用 TCP Wrapper 软件包， 会导致您的 NIS 服务器的响应延迟增加。 而增加的延迟， 则可能会导致客户端程序超时， 特别是在繁忙的网络或者很慢的 NIS 服务器上。 如果您的某个客户机因此而产生一些异常， 则应将这些客户机变为 NIS 从服务器， 并强制其绑定自己。

### 30.4.6. 不允许某些用户登录

在我们的实验室中， `basie` 这台机器， 是一台教员专用的工作站。 我们不希望将这台机器拿出 NIS 域， 而主 NIS 服务器上的 `passwd` 文件， 则同时包含了教员和学生的帐号。 这时应该怎么做？

有一种办法来禁止特定的用户登录机器， 即使他们身处 NIS 数据库之中。 要完成这一工作， 只需要在客户机的 `/etc/master.passwd` 文件中加入一些 `-username` 这样的项， 其中， *`username`* 是希望禁止登录的用户名。 一般推荐使用 `vipw` 来完成这个工作， 因为 `vipw` 会对您在 `/etc/master.passwd` 文件上所作的修改进行合法性检查， 并在编辑结束时重新构建口令数据库。 例如， 如果希望禁止用户 `bill` 登录 `basie`， 我们应该：

```
basie# `vipw`
`[在末尾加入 -bill， 并退出]`
vipw: rebuilding the database...
vipw: done

basie# `cat /etc/master.passwd`

root:[password]:0:0::0:0:The super-user:/root:/bin/csh
toor:[password]:0:0::0:0:The other super-user:/root:/bin/sh
daemon:*:1:1::0:0:Owner of many system processes:/root:/sbin/nologin
operator:*:2:5::0:0:System &:/:/sbin/nologin
bin:*:3:7::0:0:Binaries Commands and Source,,,:/:/sbin/nologin
tty:*:4:65533::0:0:Tty Sandbox:/:/sbin/nologin
kmem:*:5:65533::0:0:KMem Sandbox:/:/sbin/nologin
games:*:7:13::0:0:Games pseudo-user:/usr/games:/sbin/nologin
news:*:8:8::0:0:News Subsystem:/:/sbin/nologin
man:*:9:9::0:0:Mister Man Pages:/usr/share/man:/sbin/nologin
bind:*:53:53::0:0:Bind Sandbox:/:/sbin/nologin
uucp:*:66:66::0:0:UUCP pseudo-user:/var/spool/uucppublic:/usr/libexec/uucp/uucico
xten:*:67:67::0:0:X-10 daemon:/usr/local/xten:/sbin/nologin
pop:*:68:6::0:0:Post Office Owner:/nonexistent:/sbin/nologin
nobody:*:65534:65534::0:0:Unprivileged user:/nonexistent:/sbin/nologin
+:::::::::
-bill

basie#
```

### 30.4.7. 使用 Netgroups

Contributed by Udo Erdelhoff.

前一节介绍的方法， 在您需要为非常少的用户和/或机器进行特殊的规则配置时还算凑合。 在更大的网络上， 您 *一定会* 忘记禁止某些用户登录到敏感的机器上， 或者， 甚至必须单独地修改每一台机器的配置， 因而丢掉了 NIS 最重要的优越性： *集中式* 管理。

NIS 开发人员为这个问题提供的解决方案， 被称作 *netgroups*。 它们的作用和语义， 基本上可以等同于 UNIX® 文件系统上使用的组。 主要的区别是它们没有数字化的 ID， 以及可以在 netgroup 中同时包含用户和其他 netgroup。

Netgroups 被设计用来处理大的、 复杂的包含数百用户和机器的网络。 一方面， 在您不得不处理这类情形时， 这是一个很有用的东西。 而另一方面， 它的复杂性又使得通过非常简单的例子很难解释 netgroup 到底是什么。 这一节的其余部分的例子将展示这个问题。

假设您在实验室中成功地部署 NIS 引起了上司的兴趣。 您接下来的任务是将 NIS 域扩展， 以覆盖校园中的一些其他的机器。 下面两个表格中包括了新用户和新机器， 及其简要说明。

| 用户名 | 说明 |
| --- | --- |
| `alpha`, `beta` | IT 部门的普通雇员 |
| `charlie`, `delta` | IT 部门的学徒 |
| `echo`, `foxtrott`, `golf`, ... | 普通雇员 |
| `able`, `baker`, ... | 目前的实习生 |

| 机器名 | 说明 |
| --- | --- |
| `war`, `death`, `famine`, `pollution` | 最重要的服务器。 只有 IT 部门的雇员才允许登录这些机器。 |
| `pride`, `greed`, `envy`, `wrath`, `lust`, `sloth` | 不太重要的服务器， 所有 IT 部门的成员， 都可以登录这些机器。 |
| `one`, `two`, `three`, `four`, ... | 普通工作站。 只有 *真正的* 雇员才允许登录这些机器。 |
| `trashcan` | 一台不包含关键数据的旧机器。 即使是实习生， 也允许登录它。 |

如果您尝试通过一个一个地阻止用户来实现这些限制， 就需要在每一个系统的 `passwd` 文件中， 为每一个不允许登录该系统的用户添加对应的 `-user` 行。 如果忘记了任何一个， 就可能会造成问题。 在进行初始配置时， 正确地配置也许不是什么问题， 但随着日复一日地添加新用户， *总有一天* 您会忘记为新用户添加某个行。 毕竟， Murphy 是一个乐观的人。

使用 netgroups 来处理这一状况可以带来许多好处。 不需要单独地处理每一个用户； 您可以赋予用户一个或多个 netgroups 身份， 并允许或禁止某一个 netgroup 的所有成员登录。 如果添加了新的机器， 只需要定义 netgroup 的登录限制。 如果增加了新用户， 也只需要将用户加入一个或多个 netgroup。 这些变化是相互独立的： 不再需要 “对每一个用户和机器执行 ……”。 如果您的 NIS 配置经过了谨慎的规划， 就只需要修改一个中央的配置文件， 就能够允许或禁止访问某台机器的权限了。

第一步是初始化 NIS 映射 netgroup。 FreeBSD 的 [ypinit(8)](http://www.FreeBSD.org/cgi/man.cgi?query=ypinit&sektion=8&manpath=freebsd-release-ports) 默认情况下并不创建这个映射， 但它的 NIS 实现能够在创建这个映射之后立即对其提供支持。 要创建空映射， 简单地输入

```
ellington# `vi /var/yp/netgroup`
```

并开始增加内容。 在我们的例子中， 至少需要四个 nergruop： IT 雇员， IT 学徒， 普通雇员和实习生。

```
IT_EMP  (,alpha,test-domain)    (,beta,test-domain)
IT_APP  (,charlie,test-domain)  (,delta,test-domain)
USERS   (,echo,test-domain)     (,foxtrott,test-domain) \
        (,golf,test-domain)
INTERNS (,able,test-domain)     (,baker,test-domain)
```

`IT_EMP`, `IT_APP` 等等， 是 netgroup 的名字。 每一个括号中的组中， 都有一些用户帐号。 组中的三个字段是：

1.  在哪些机器上能够使用这些项。 如果不指定主机名， 则项在所有机器上都有效。 如果指定了主机， 则很容易造成混淆。

2.  属于这个 netgroup 的帐号。

3.  帐号的 NIS 域。 您可以从其他 NIS 域中把帐号导入到您的 netgroup 中， 如果您管理多个 NIS 域的话。

每一个字段都可以包括通配符。 参见 [netgroup(5)](http://www.FreeBSD.org/cgi/man.cgi?query=netgroup&sektion=5&manpath=freebsd-release-ports) 了解更多细节。

### 注意:

Netgroup 的名字一般来说不应超过 8 个字符， 特别是当您的 NIS 域中有机器打算运行其它操作系统的时候。 名字是区分大小写的； 使用大写字母作为 netgroup 的名字， 能够让您更容易地区分用户、 机器和 netgroup 的名字。

某些 NIS 客户程序 (FreeBSD 以外的那些) 可能无法处理含有大量项的 netgroup。 例如， 某些早期版本的 SunOS™ 会在 netgroup 中包含多于 15 个 *项* 时出现问题。 要绕过这个问题， 可以创建多个 子 netgroup，每一个中包含少于 15 个用户， 以及一个包含所有 子 netgroup 的真正的 netgroup：

```
BIGGRP1  (,joe1,domain)  (,joe2,domain)  (,joe3,domain) [...]
BIGGRP2  (,joe16,domain)  (,joe17,domain) [...]
BIGGRP3  (,joe31,domain)  (,joe32,domain)
BIGGROUP  BIGGRP1 BIGGRP2 BIGGRP3
```

如果需要超过 225 个用户， 可以继续重复上面的过程。

激活并分发新的 NIS 映射非常简单：

```
ellington# `cd /var/yp`
ellington# `make`
```

这个操作会生成三个 NIS 映射， 即 `netgroup`、 `netgroup.byhost` 和 `netgroup.byuser`。 用 [ypcat(1)](http://www.FreeBSD.org/cgi/man.cgi?query=ypcat&sektion=1&manpath=freebsd-release-ports) 可以检查这些 NIS 映射是否可用了：

```
ellington% `ypcat -k netgroup`
ellington% `ypcat -k netgroup.byhost`
ellington% `ypcat -k netgroup.byuser`
```

第一个命令的输出， 应该与 `/var/yp/netgroup` 的内容相近。 第二个命令， 如果没有指定本机专有的 netgroup， 则应该没有输出。 第三个命令， 则用于显示某个用户对应的 netgroup 列表。

客户机的设置也很简单。 要配置服务器 `war`， 只需进入 [vipw(8)](http://www.FreeBSD.org/cgi/man.cgi?query=vipw&sektion=8&manpath=freebsd-release-ports) 并把

```
+:::::::::
```

改为

```
+@IT_EMP:::::::::
```

现在， 只有 netgroup `IT_EMP` 中定义的用户会被导入到 `war` 的口令数据库中， 因此只有这些用户能够登录。

不过， 这个限制也会作用于 shell 的 `~`， 以及所有在用户名和数字用户 ID 之间实施转换的函数的功能。 换言之， `cd ~user` 将不会正常工作， 而 `ls -l` 也将显示数字的 ID 而不是用户名， 并且 `find . -user joe -print` 将失败， 并给出 No such user 的错误信息。 要修正这个问题， 您需要导入所有的用户项， 而 *不允许他们登录服务器*。

这可以通过在 `/etc/master.passwd` 加入另一行来完成。 这行的内容是：

`+:::::::::/sbin/nologin`， 意思是 “导入所有的项， 但导入项的 shell 则替换为 `/sbin/nologin`”。 通过在 `/etc/master.passwd` 中增加默认值， 可以替换掉 `passwd` 中的任意字段。

### 警告:

务必确认 `+:::::::::/sbin/nologin` 这一行出现在 `+@IT_EMP:::::::::` 之后。 否则， 所有从 NIS 导入的用户帐号将以 `/sbin/nologin` 作为登录 shell。

完成上面的修改之后， 在 IT 部门有了新员工时， 只需修改一个 NIS 映射就足够了。 您也可以用类似的方法， 在不太重要的服务器上， 把先前本地版本的 `/etc/master.passwd` 中的 `+:::::::::` 改为：

```
+@IT_EMP:::::::::
+@IT_APP:::::::::
+:::::::::/sbin/nologin
```

相关的用于普通工作站的配置则应是：

```
+@IT_EMP:::::::::
+@USERS:::::::::
+:::::::::/sbin/nologin
```

一切平安无事， 直到数周后， 有一天策略发生了变化： IT 部门也开始招收实习生了。 IT 实习生允许使用普通的终端， 以及不太重要的服务器； 而 IT 学徒， 则可以登录主服务器。 您增加了新的 netgroup `IT_INTERN`， 以及新的 IT 实习生到这个 netgroup 并开始修改每一台机器上的配置…… 老话说得好：“牵一发， 动全身”。

NIS 通过 netgroup 来建立 netgroup 的能力， 正可以避免这样的情形。 一种可能的方法是建立基于角色的 netgroup。 例如， 您可以创建称为 `BIGSRV` 的 netgroup， 用于定义最重要的服务器上的登录限制， 以及另一个成为 `SMALLSRV` 的 netgroup， 用以定义次重要的服务器， 以及第三个， 用于普通工作站的 netgroup `USERBOX`。 这三个 netgroup 中的每一个， 都包含了允许登录到这些机器上的所有 netgroup。 您的 NIS 映射中的新项如下所示：

```
BIGSRV    IT_EMP  IT_APP
SMALLSRV  IT_EMP  IT_APP  ITINTERN
USERBOX   IT_EMP  ITINTERN USERS
```

这种定义登录限制的方法， 在您能够将机器分组并加以限制的时候可以工作的相当好。 不幸的是， 这是种例外， 而非常规情况。 多数时候， 需要按机器去定义登录限制。

与机器相关的 netgroup 定义， 是处理上述策略改动的另一种可能的方法。 此时， 每台机器的 `/etc/master.passwd` 中， 都包含两个 “+” 开头的行。 第一个用于添加允许登录的 netgroup 帐号， 而第二个则用于增加其它帐号， 并把 shell 设置为 `/sbin/nologin`。 使用 “全大写” 的机器名作为 netgroup 名是个好主意。 换言之， 这些行应该类似于：

```
+@*`BOXNAME`*:::::::::
+:::::::::/sbin/nologin
```

一旦在所有机器上都完成了这样的修改， 就再也不需要修改本地的 `/etc/master.passwd` 了。 所有未来的修改都可以在 NIS 映射中进行。 这里是一个例子， 其中展示了在这一应用情景中所需要的 netgroup 映射， 以及其它一些常用的技巧：

```
# Define groups of users first
IT_EMP    (,alpha,test-domain)    (,beta,test-domain)
IT_APP    (,charlie,test-domain)  (,delta,test-domain)
DEPT1     (,echo,test-domain)     (,foxtrott,test-domain)
DEPT2     (,golf,test-domain)     (,hotel,test-domain)
DEPT3     (,india,test-domain)    (,juliet,test-domain)
ITINTERN  (,kilo,test-domain)     (,lima,test-domain)
D_INTERNS (,able,test-domain)     (,baker,test-domain)
#
# Now, define some groups based on roles
USERS     DEPT1   DEPT2     DEPT3
BIGSRV    IT_EMP  IT_APP
SMALLSRV  IT_EMP  IT_APP    ITINTERN
USERBOX   IT_EMP  ITINTERN  USERS
#
# And a groups for a special tasks
# Allow echo and golf to access our anti-virus-machine
SECURITY  IT_EMP  (,echo,test-domain)  (,golf,test-domain)
#
# machine-based netgroups
# Our main servers
WAR       BIGSRV
FAMINE    BIGSRV
# User india needs access to this server
POLLUTION  BIGSRV  (,india,test-domain)
#
# This one is really important and needs more access restrictions
DEATH     IT_EMP
#
# The anti-virus-machine mentioned above
ONE       SECURITY
#
# Restrict a machine to a single user
TWO       (,hotel,test-domain)
# [...more groups to follow]
```

如果您正使用某种数据库来管理帐号， 应该可以使用您的数据库的报告工具来创建映射的第一部分。 这样， 新用户就自动地可以访问这些机器了。

最后的提醒： 使用基于机器的 netgroup 并不总是适用的。 如果正在为学生实验室部署数十台甚至上百台同样的机器， 您应该使用基于角色的 netgroup， 而不是基于机器的 netgroup， 以便把 NIS 映射的尺寸保持在一个合理的范围内。

### 30.4.8. 需要牢记的事项

这里是一些其它在使用 NIS 环境时需要注意的地方。

*   每次需要在实验室中增加新用户时， 必须 *只* 在 NIS 服务器上加入用户， 而且 *一定要记得重建 NIS 映射*。 如果您忘记了这样做， 新用户将无法登录除 NIS 主服务器之外的任何其它机器。 例如， 如果要在实验室增加新用户 `jsmith`， 我们需要：

    ```
    # `pw useradd jsmith`
    # `cd /var/yp`
    # `make test-domain`
    ```

    也可以运行 `adduser jsmith` 而不是 `pw useradd jsmith`.

*   *将管理用的帐号排除在 NIS 映射之外*。 一般来说， 您不希望这些管理帐号和口令被扩散到那些包含不应使用它们的用户的机器上。

*   *确保 NIS 主和从服务器的安全， 并尽可能减少其停机时间*。 如果有人攻入或简单地关闭这些机器， 则整个实验室的任也就无法登录了。

    这是集中式管理系统中最薄弱的环节。 如果没有保护好 NIS 服务器， 您就有大批愤怒的用户需要对付了！

### 30.4.9. NIS v1 兼容性

FreeBSD 的 ypserv 提供了某些为 NIS v1 客户提供服务的支持能力。 FreeBSD 的 NIS 实现， 只使用 NIS v2 协议， 但其它实现可能会包含 v1 协议， 以提供对旧系统的向下兼容能力。 随这些系统提供的 ypbind 服务将首先尝试绑定 NIS v1 服务器， 即使它们并不真的需要它 (有些甚至可能会一直广播搜索请求， 即使已经从某台 v2 服务器得到了回应也是如此)。 注意， 尽管支持一般的客户机调用， 这个版本的 ypserv 并不能处理 v1 的映射传送请求； 因而， 它就不能与较早的支持 v1 协议的 NIS 服务器配合使用， 无论是作为主服务器还是从服务器。 幸运的是， 现今应该已经没有仍然在用的这样的服务器了。

### 30.4.10. 同时作为 NIS 客户机的 NIS 服务器

在多服务器域的环境中， 如果服务器同时作为 NIS 客户， 在运行 ypserv 时要特别小心。 一般来说， 强制服务器绑定自己要比允许它们广播绑定请求要好， 因为这种情况下它们可能会相互绑定。 某些怪异的故障， 很可能是由于某一台服务器停机， 而其它服务器都依赖其服务所导致的。 最终， 所有的客户机都会超时并绑定到其它服务器， 但这个延迟可能会相当可观， 而且恢复之后仍然存在再次发生此类问题的隐患。

您可以强制一台机器绑定到特定的服务器， 这是通过 `ypbind` 的 `-S` 参数来完成的。 如果不希望每次启动 NIS 服务器时都手工完成这项工作， 可以在 `/etc/rc.conf` 中加入：

```
nis_client_enable="YES"	# run client stuff as well
nis_client_flags="-S *`NIS domain`*,*`server`*"
```

参见 [ypbind(8)](http://www.FreeBSD.org/cgi/man.cgi?query=ypbind&sektion=8&manpath=freebsd-release-ports) 以了解更多情况。

### 30.4.11. 口令格式

在实现 NIS 时， 口令格式的兼容性问题是一种最为常见的问题。 假如您的 NIS 服务器使用 DES 加密口令， 则它只能支持使用 DES 的客户机。 例如， 如果您的网络上有 Solaris™ NIS 客户机， 则几乎肯定需要使用 DES 加密口令。

要检查您的服务器和客户机使用的口令格式， 需要查看 `/etc/login.conf`。 如果主机被配置为使用 DES 加密的口令， 则 `default` class 将包含类似这样的项：

```
default:\
	:passwd_format=des:\
	:copyright=/etc/COPYRIGHT:\
	[Further entries elided]
```

其他一些可能的 `passwd_format` 包括 `blf` 和 `md5` (分别对应于 Blowfish 和 MD5 加密口令)。

如果修改了 `/etc/login.conf`， 就必须重建登录性能数据库， 这是通过以 `root` 身份运行下面的程序来完成的：

```
# `cap_mkdb /etc/login.conf`
```

### 注意:

已经在 `/etc/master.passwd` 中的口令的格式不会被更新， 直到用户在登录性能数据库重建 *之后* 首次修改口令为止。

接下来， 为了确保所有的口令都按照您选择的格式加密了， 还需要检查 `/etc/auth.conf` 中 `crypt_default` 给出的优先选择的口令格式。 要完成此工作， 将您选择的格式放到列表的第一项。 例如， 当使用 DES 加密的口令时， 对应项应为：

```
crypt_default	=	des blf md5
```

在每一台基于 FreeBSD 的 NIS 服务器和客户机上完成上述工作之后， 就可以肯定您的网络上它们都在使用同样的口令格式了。 如果在 NIS 客户机上做身份验证时发生问题， 这也是第一个可能出现问题的地方。 注意： 如果您希望在混合的网络上部署 NIS 服务器， 可能就需要在所有系统上都使用 DES， 因为这是所有系统都能够支持的最低限度的公共标准。

## 30.5. 网络自动配置 (DHCP)

Written by Greg Sutter.

### 30.5.1. 什么是 DHCP？

DHCP， 动态主机配置协议， 是一种让系统得以连接到网络上， 并获取所需要的配置参数手段。 FreeBSD 使用来自 OpenBSD 3.7 的 OpenBSD `dhclient`。 这里提供的所有关于 `dhclient` 的信息， 都是以 ISC 或 OpenBSD DHCP 客户端程序为准的。 DHCP 服务器是 ISC 软件包的一部分。

### 30.5.2. 这一节都介绍哪些内容

这一节描述了 ISC 和 DHCP 系统中的客户端， 以及和 ISC DHCP 系统中的服务器端的组件。 客户端程序， `dhclient`， 是随 FreeBSD 作为它的一部分提供的； 而服务器部分， 则可以通过 [net/isc-dhcp31-server](http://www.freebsd.org/cgi/url.cgi?ports/net/isc-dhcp31-server/pkg-descr) port 得到。 [dhclient(8)](http://www.FreeBSD.org/cgi/man.cgi?query=dhclient&sektion=8&manpath=freebsd-release-ports)、 [dhcp-options(5)](http://www.FreeBSD.org/cgi/man.cgi?query=dhcp-options&sektion=5&manpath=freebsd-release-ports)、 以及 [dhclient.conf(5)](http://www.FreeBSD.org/cgi/man.cgi?query=dhclient.conf&sektion=5&manpath=freebsd-release-ports) 联机手册， 加上下面所介绍的参考文献， 都是非常有用的资源。

### 30.5.3. 它如何工作

当 DHCP 客户程序， `dhclient` 在客户机上运行时， 它会开始广播请求配置信息的消息。 默认情况下， 这些请求是在 UDP 端口 68 上。 服务器通过 UDP 67 给出响应， 向客户机提供一个 IP 地址， 以及其他有关的配置参数， 例如子网掩码、 路由器， 以及 DNS 服务器。 所有这些信息都会以 DHCP “lease” 的形式给出， 并且只在一段特定的时间内有效 (这是由 DHCP 服务器的维护者配置的)。 这样， 那些已经断开网络的客户机使用的陈旧的 IP 地址就能被自动地回收了。

DHCP 客户程序可以从服务器端获取大量的信息。 关于能获得的信息的详细列表， 请参考 [dhcp-options(5)](http://www.FreeBSD.org/cgi/man.cgi?query=dhcp-options&sektion=5&manpath=freebsd-release-ports)。

### 30.5.4. FreeBSD 集成

FreeBSD 完全地集成了 OpenBSD 的 DHCP 客户端， `dhclient`。 DHCP 客户端支持在安装程序和基本系统中均有提供， 这使得您不再需要去了解那些已经运行了 DHCP 服务器的网络的具体配置参数。

sysinstall 能够支持 DHCP。 在 sysinstall 中配置网络接口时， 它询问的第二个问题便是： “Do you want to try DHCP configuration of the interface? (您是否希望在此接口上尝试 DHCP 配置?)”。 如果做肯定的回答， 则将运行 `dhclient`， 一旦成功， 则将自动地填写网络配置信息。

要在系统启动时使用 DHCP， 您必须做两件事：

*   您的内核中， 必须包含 `bpf` 设备。 如果需要这样做， 需要将 `device bpf` 添加到内核的编译配置文件中， 并重新编译内核。 要了解关于编译内核的进一步信息， 请参见 第 9 章 *配置 FreeBSD 的内核*。

    `bpf` 设备已经是 FreeBSD 发行版中默认的 `GENERIC` 内核的一部分了， 因此如果您没有对内核进行定制， 则不用创建一份新的内核配置文件， DHCP 就能工作了。

    ### 注意:

    对于那些安全意识很强的人来说， 您应该知道 `bpf` 也是包侦听工具能够正确工作的条件之一 (当然， 它们还需要以 `root` 身份运行才行)。 `bpf` *是* 使用 DHCP 所必须的， 但如果您对安全非常敏感， 则很可能会有理由不把 `bpf` 加入到您的内核配置中， 直到您真的需要使用 DHCP 为止。

*   编辑您的 `/etc/rc.conf` 并加入下面的设置：

    ```
    ifconfig_fxp0="DHCP"
    ```

    ### 注意:

    务必将 `fxp0` 替换为您希望自动配置的网络接口的名字， 您可以在 第 12.8 节 “设置网卡” 找到更进一步的介绍。

    如果您希望使用另一位置的 `dhclient`， 或者需要给 `dhclient` 传递其他参数， 还可以添加下面的配置 (根据需要进行修改)：

    ```
    dhclient_program="/sbin/dhclient"
    dhclient_flags=""
    ```

DHCP 服务器， dhcpd， 是作为 [net/isc-dhcp31-server](http://www.freebsd.org/cgi/url.cgi?ports/net/isc-dhcp31-server/pkg-descr) port 的一部分提供的。 这个 port 包括了 ISC DHCP 服务器及其文档。

### 30.5.5. 文件

*   `/etc/dhclient.conf`

    `dhclient` 需要一个配置文件， `/etc/dhclient.conf`。 一般说来， 这个文件中只包括注释， 而默认值基本上都是合理的。 这个配置文件在 [dhclient.conf(5)](http://www.FreeBSD.org/cgi/man.cgi?query=dhclient.conf&sektion=5&manpath=freebsd-release-ports) 联机手册中进行了进一步的阐述。

*   `/sbin/dhclient`

    `dhclient` 是一个静态连编的， 它被安装到 `/sbin` 中。 [dhclient(8)](http://www.FreeBSD.org/cgi/man.cgi?query=dhclient&sektion=8&manpath=freebsd-release-ports) 联机手册给出了关于 `dhclient` 的进一步细节。

*   `/sbin/dhclient-script`

    `dhclient-script` 是一个 FreeBSD 专用的 DHCP 客户端配置脚本。 在 [dhclient-script(8)](http://www.FreeBSD.org/cgi/man.cgi?query=dhclient-script&sektion=8&manpath=freebsd-release-ports) 中对它进行了描述， 但一般来说， 用户不需要对其进行任何修改， 就能够让一切正常运转了。

*   `/var/db/dhclient.leases`

    DHCP 客户程序会维护一个数据库来保存有效的 lease， 它们被以日志的形式保存到这个文件中。 [dhclient.leases(5)](http://www.FreeBSD.org/cgi/man.cgi?query=dhclient.leases&sektion=5&manpath=freebsd-release-ports) 给出了更为细致的介绍。

### 30.5.6. 进阶读物

DHCP 协议的完整描述是 [RFC 2131](http://www.freesoft.org/CIE/RFC/2131/)。 关于它的其他信息资源的站点 `[`www.dhcp.org/`](http://www.dhcp.org/)` 也提供了详尽的资料。

### 30.5.7. 安装和配置 DHCP 服务器

#### 30.5.7.1. 这一章包含哪些内容

这一章提供了关于如何在 FreeBSD 系统上使用 ISC (Internet 系统协会) 的 DHCP 实现套件来架设 DHCP 服务器的信息。

DHCP 套件中的服务器部分并没有作为 FreeBSD 的一部分来提供， 因此您需要安装 [net/isc-dhcp31-server](http://www.freebsd.org/cgi/url.cgi?ports/net/isc-dhcp31-server/pkg-descr) port 才能提供这个服务。 请参见 第 5 章 *安装应用程序: Packages 和 Ports* 以了解关于如何使用 Ports Collection 的进一步详情。

#### 30.5.7.2. 安装 DHCP 服务器

为了在您的 FreeBSD 系统上进行配置以便作为 DHCP 服务器来使用， 需要把 [bpf(4)](http://www.FreeBSD.org/cgi/man.cgi?query=bpf&sektion=4&manpath=freebsd-release-ports) 设备编译进内核。 要完成这项工作， 需要将 `device bpf` 加入到您的内核配置文件中， 并重新联编内核。 要得到关于如何联编内核的进一步信息， 请参见 第 9 章 *配置 FreeBSD 的内核*。

`bpf` 设备是 FreeBSD 所附带的 `GENERIC` 内核中已经联入的组件， 因此您并不需要为了让 DHCP 正常工作而特别地定制内核。

### 注意:

如果您有较强的安全意识， 应该注意 `bpf` 同时也是让听包程序能够正确工作的设备 (尽管这类程序仍然需要以特权用户身份运行)。 `bpf` *是* 使用 DHCP 所必需的， 但如果您对安全非常敏感， 您可能会不希望将 `bpf` 放进内核， 直到您真的认为 DHCP 是必需的为止。

接下来要做的是编辑示范的 `dhcpd.conf`， 它由 [net/isc-dhcp31-server](http://www.freebsd.org/cgi/url.cgi?ports/net/isc-dhcp31-server/pkg-descr) port 安装。 默认情况下， 它的名字应该是 `/usr/local/etc/dhcpd.conf.sample`， 在开始修改之前， 您需要把它复制为 `/usr/local/etc/dhcpd.conf`。

#### 30.5.7.3. 配置 DHCP 服务器

`dhcpd.conf` 包含了一系列关于子网和主机的定义， 下面的例子可以帮助您理解它：

```
option domain-name "example.com";![1](img/1.png)
option domain-name-servers 192.168.4.100;![2](img/2.png)
option subnet-mask 255.255.255.0;![3](img/3.png)

default-lease-time 3600;![4](img/4.png)
max-lease-time 86400;![5](img/5.png)
ddns-update-style none;![6](img/6.png)

subnet 192.168.4.0 netmask 255.255.255.0 {
  range 192.168.4.129 192.168.4.254;![7](img/7.png)
  option routers 192.168.4.1;![8](img/8.png)
}

host mailhost {
  hardware ethernet 02:03:04:05:06:07;![9](img/9.png)
  fixed-address mailhost.example.com;![10](img/10.png)
}
```

| ![1](img/#domain-name) | 这个选项指定了提供给客户机作为默认搜索域的域名。 请参考 [resolv.conf(5)](http://www.FreeBSD.org/cgi/man.cgi?query=resolv.conf&sektion=5&manpath=freebsd-release-ports) 以了解关于这一概念的详情。 |
| ![2](img/#domain-name-servers) | 这个选项用于指定一组客户机使用的 DNS 服务器， 它们之间以逗号分隔。 |
| ![3](img/#subnet-mask) | 提供给客户机的子网掩码。 |
| ![4](img/#default-lease-time) | 客户机可以请求租约的有效期， 而如果没有， 则服务器将指定一个租约有效期， 也就是这个值 (单位是秒)。 |
| ![5](img/#max-lease-time) | 这是服务器允许租出地址的最大时长。 如果客户机请求了更长的租期， 则它将得到一个地址， 但其租期仅限于 `max-lease-time` 秒。 |
| ![6](img/#ddns-update-style) | 这个选项用于指定 DHCP 服务器在一个地址被接受或释放时是否应对应尝试更新 DNS。 在 ISC 实现中， 这一选项是 *必须指定的*。 |
| ![7](img/#range) | 指定地址池中可以用来分配给客户机的 IP 地址范围。 在这个范围之间， 以及其边界的 IP 地址将分配给客户机。 |
| ![8](img/#routers) | 定义客户机的默认网关。 |
| ![9](img/#hardware) | 主机的硬件 MAC 地址 (这样 DHCP 服务器就能够在接到请求时知道请求的主机身份)。 |
| ![10](img/#fixed-address) | 指定总是得到同一 IP 地址的主机。 请注意在此处使用主机名是对的， 因为 DHCP 服务器会在返回租借地址信息之前自行解析主机名。 |

在配制好 `dhcpd.conf` 之后， 应在 `/etc/rc.conf` 中启用 DHCP 服务器， 也就是增加：

```
dhcpd_enable="YES"
dhcpd_ifaces="dc0"
```

此处的 `dc0` 接口名应改为 DHCP 服务器需要监听 DHCP 客户端请求的接口 (如果有多个， 则用空格分开)。

接下来， 可以用下面的命令来启动服务：

```
# `/usr/local/etc/rc.d/isc-dhcpd start`
```

如果未来您需要修改服务器的配置， 请务必牢记发送 `SIGHUP` 信号给 dhcpd 并 *不会* 导致配置文件的重新加载， 而这在其他服务程序中则是比较普遍的约定。 您需要发送 `SIGTERM` 信号来停止进程， 然后使用上面的命令来重新启动它。

#### 30.5.7.4. 文件

*   `/usr/local/sbin/dhcpd`

    dhcpd 是静态连接的， 并安装到 `/usr/local/sbin` 中。 随 port 安装的 [dhcpd(8)](http://www.FreeBSD.org/cgi/man.cgi?query=dhcpd&sektion=8&manpath=freebsd-release-ports) 联机手册提供了关于 dhcpd 更为详尽的信息。

*   `/usr/local/etc/dhcpd.conf`

    dhcpd 需要配置文件， 即 `/usr/local/etc/dhcpd.conf` 才能够向客户机提供服务。 这个文件需要包括应提供给客户机的所有信息， 以及关于服务器运行的其他信息。 此配置文件的详细描述可以在随 port 安装的 [dhcpd.conf(5)](http://www.FreeBSD.org/cgi/man.cgi?query=dhcpd.conf&sektion=5&manpath=freebsd-release-ports) 联机手册上找到。

*   `/var/db/dhcpd.leases`

    DHCP 服务器会维护一个它签发的租用地址数据库， 并保存在这个文件中， 这个文件是以日志的形式保存的。 随 port 安装的 [dhcpd.leases(5)](http://www.FreeBSD.org/cgi/man.cgi?query=dhcpd.leases&sektion=5&manpath=freebsd-release-ports) 联机手册提供了更详细的描述。

*   `/usr/local/sbin/dhcrelay`

    dhcrelay 在更为复杂的环境中， 可以用来支持使用 DHCP 服务器转发请求给另一个独立网络上的 DHCP 服务器。 如果您需要这个功能， 需要安装 [net/isc-dhcp31-relay](http://www.freebsd.org/cgi/url.cgi?ports/net/isc-dhcp31-relay/pkg-descr) port。 [dhcrelay(8)](http://www.FreeBSD.org/cgi/man.cgi?query=dhcrelay&sektion=8&manpath=freebsd-release-ports) 联机手册提供了更为详尽的介绍。

## 30.6. 域名系统 (DNS)

Contributed by Chern Lee, Tom Rhodes 和 Daniel Gerzo.

### 30.6.1. 纵览

FreeBSD 在默认情况下使用一个版本的 BIND (Berkeley Internet Name Domain)， 这是目前最为流行的 DNS 协议实现。 DNS 是一种协议， 可以通过它将域名同 IP 地址相互对应。 例如， 查询 `www.FreeBSD.org` 将得到 FreeBSD Project 的 web 服务器的 IP 地址， 而查询 `ftp.FreeBSD.org` 则将得到响应的 FTP 机器的 IP 地址。 类似地， 也可以做相反的事情。 查询 IP 地址可以得到其主机名。 当然， 完成 DNS 查询并不需要在系统中运行域名服务器。

目前， 默认情况下 FreeBSD 使用的是 BIND9 DNS 服务软件。 我们内建于系统中的版本提供了增强的安全特性、 新的文件目录结构， 以及自动的 [chroot(8)](http://www.FreeBSD.org/cgi/man.cgi?query=chroot&sektion=8&manpath=freebsd-release-ports) 配置。

在 Internet 上的 DNS 是通过一套较为复杂的权威根域名系统， 顶级域名 (TLD)， 以及一系列小规模的， 提供少量域名解析服务并对域名信息进行缓存的域名服务器组成的。

目前， BIND 由 Internet Systems Consortium `[`www.isc.org/`](https://www.isc.org/)` 维护。

### 30.6.2. 术语

要理解这份文档， 需要首先了解一些相关的 DNS 术语。

| 术语 | 定义 |
| --- | --- |
| 正向 DNS | 将域名映射到 IP 地址 |
| 原点 (Origin) | 表示特定域文件所在的域 |
| named, BIND | 在 FreeBSD 中 BIND 域名服务器软件包的常见叫法。 |
| 解析器 (Resolver) | 计算机用以向域名服务器查询域名信息的一个系统进程 |
| 反向 DNS | 将 IP 地址映射为主机名 |
| 根域 | Internet 域层次的起点。 所有的域都在根域之下， 类似文件系统中， 文件都在根目录之下那样。 |
| 域 (Zone) | 独立的域， 子域， 或者由同一机构管理的 DNS 的一部分。 |

域的例子：

*   `.` 在本文档中通常指代根域。

*   `org.` 是根域之下的一个顶级域名 (TLD)。

*   `example.org.` 是在 `org.` TLD 之下的一个域。

*   `1.168.192.in-addr.arpa` 是一个表示所有 `192.168.1.*` IP 地址空间中 IP 地址的域。

如您所见， 域名中越细节的部分会越靠左出现。 例如， `example.org.` 就比 `org.` 范围更小， 类似地 `org.` 又比根域更小。 域名各个部分的格局与文件系统十分类似： `/dev` 目录在根目录之下， 等等。

### 30.6.3. 运行域名服务器的理由

域名服务器通常会有两种形式： 权威域名服务器， 以及缓存域名服务器。

下列情况需要有权威域名服务器：

*   想要向全世界提供 DNS 信息， 并对请求给出权威应答。

*   注册了类似 `example.org` 的域， 而需要将 IP 指定到其下的主机名上。

*   某个 IP 地址块需要反向 DNS 项 (IP 到主机名)。

*   备份服务器， 或常说的从 (slave) 服务器， 会在主服务器出现问题或无法访问时来应答查询请求。

下列情况需要有缓存域名服务器：

*   本地的 DNS 服务器能够缓存， 并比直接向外界的域名服务器请求更快地得到应答。

当有人查询 `www.FreeBSD.org` 时，解析器通常会向上级 ISP 的域名服务器发出请求， 并获得回应。 如果有本地的缓存 DNS 服务器， 查询只有在第一次被缓存 DNS 服务器发到外部世界。 其他的查询不会发向局域网外， 因为它们已经有在本地的缓存了。

### 30.6.4. DNS 如何运作

在 FreeBSD 中， BIND 服务程序被称为 named。

| 文件 | 描述 |
| --- | --- |
| [named(8)](http://www.FreeBSD.org/cgi/man.cgi?query=named&sektion=8&manpath=freebsd-release-ports) | BIND 服务程序 |
| [rndc(8)](http://www.FreeBSD.org/cgi/man.cgi?query=rndc&sektion=8&manpath=freebsd-release-ports) | 域名服务控制程序 |
| `/etc/namedb` | BIND 存放域名信息的位置。 |
| `/etc/namedb/named.conf` | 域名服务配置文件 |

随在服务器上配置的域的性质不同， 域的定义文件一般会存放到 `/etc/namedb` 目录中的 `master`、 `slave`， 或 `dynamic` 子目录中。 这些文件中提供了域名服务器在响应查询时所需要的 DNS 信息。

### 30.6.5. 启动 BIND

由于 BIND 是默认安装的， 因此配置它相对而言很简单。

默认的 named 配置， 是在 [chroot(8)](http://www.FreeBSD.org/cgi/man.cgi?query=chroot&sektion=8&manpath=freebsd-release-ports) 环境中提供基本的域名解析服务， 并且只限于监听本地 IPv4 回环地址 (127.0.0.1)。 如果希望启动这一配置， 可以使用下面的命令：

```
# `/etc/rc.d/named onestart`
```

如果希望 named 服务在每次启动的时候都能够启动， 需要在 `/etc/rc.conf` 中加入：

```
named_enable="YES"
```

当然， 除了这份文档所介绍的配置选项之外， 在 `/etc/namedb/named.conf` 中还有很多其它的选项。 不过， 如果您需要了解 FreeBSD 中用于启动 named 的那些选项的话， 则可以查看 `/etc/defaults/rc.conf` 中的 `named_*` 参数， 并参考 [rc.conf(5)](http://www.FreeBSD.org/cgi/man.cgi?query=rc.conf&sektion=5&manpath=freebsd-release-ports) 联机手册。 除此之外， 第 12.7 节 “在 FreeBSD 中使用 rc” 也是一个不错的起点。

### 30.6.6. 配置文件

目前， named 的配置文件存放于 `/etc/namedb` 目录， 在使用前应根据需要进行修改， 除非您只打算让它完成简单的域名解析服务。 这个目录同时也是您进行绝大多数配置的地方。

#### 30.6.6.1. `/etc/namedb/named.conf`

```
// $FreeBSD$
//
// Refer to the named.conf(5) and named(8) man pages, and the documentation
// in /usr/share/doc/bind9 for more details.
//
// If you are going to set up an authoritative server, make sure you
// understand the hairy details of how DNS works.  Even with
// simple mistakes, you can break connectivity for affected parties,
// or cause huge amounts of useless Internet traffic.

options {
	// Relative to the chroot directory, if any
	directory	"/etc/namedb";
	pid-file	"/var/run/named/pid";
	dump-file	"/var/dump/named_dump.db";
	statistics-file	"/var/stats/named.stats";

// If named is being used only as a local resolver, this is a safe default.
// For named to be accessible to the network, comment this option, specify
// the proper IP address, or delete this option.
	listen-on	{ 127.0.0.1; };

// If you have IPv6 enabled on this system, uncomment this option for
// use as a local resolver.  To give access to the network, specify
// an IPv6 address, or the keyword "any".
//	listen-on-v6	{ ::1; };

// These zones are already covered by the empty zones listed below.
// If you remove the related empty zones below, comment these lines out.
	disable-empty-zone "255.255.255.255.IN-ADDR.ARPA";
	disable-empty-zone "0.0.0.0.0.0.0.0.0.0.0.0.0.0.0.0.0.0.0.0.0.0.0.0.0.0.0.0.0.0.0.0.IP6.ARPA";
	disable-empty-zone "1.0.0.0.0.0.0.0.0.0.0.0.0.0.0.0.0.0.0.0.0.0.0.0.0.0.0.0.0.0.0.0.IP6.ARPA";

// If you've got a DNS server around at your upstream provider, enter
// its IP address here, and enable the line below.  This will make you
// benefit from its cache, thus reduce overall DNS traffic in the Internet.
/*
	forwarders {
		127.0.0.1;
	};
*/

// If the 'forwarders' clause is not empty the default is to 'forward first'
// which will fall back to sending a query from your local server if the name
// servers in 'forwarders' do not have the answer.  Alternatively you can
// force your name server to never initiate queries of its own by enabling the
// following line:
//	forward only;

// If you wish to have forwarding configured automatically based on
// the entries in /etc/resolv.conf, uncomment the following line and
// set named_auto_forward=yes in /etc/rc.conf.  You can also enable
// named_auto_forward_only (the effect of which is described above).
//	include "/etc/namedb/auto_forward.conf";
```

正如注释所言， 如果希望从上级缓存中受益， 可以在此处启用 `forwarders`。 正常情况下， 域名服务器会逐级地查询 Internet 来找到特定的域名服务器， 直到得到答案为止。 这个选项将让它首先查询上级域名服务器 (或另外提供的域名服务器)， 从而从它们的缓存中得到结果。 如果上级域名服务器是一个繁忙的高速域名服务器， 则启用它将有助于改善服务品质。

### 警告:

`127.0.0.1` *不会* 正常工作。 一定要把地址改为您上级服务器的 IP 地址。

```
	/*
	   Modern versions of BIND use a random UDP port for each outgoing
	   query by default in order to dramatically reduce the possibility
	   of cache poisoning.  All users are strongly encouraged to utilize
	   this feature, and to configure their firewalls to accommodate it.

	   AS A LAST RESORT in order to get around a restrictive firewall
	   policy you can try enabling the option below.  Use of this option
	   will significantly reduce your ability to withstand cache poisoning
	   attacks, and should be avoided if at all possible.

	   Replace NNNNN in the example with a number between 49160 and 65530.
	*/
	// query-source address * port NNNNN;
};

// If you enable a local name server, don't forget to enter 127.0.0.1
// first in your /etc/resolv.conf so this server will be queried.
// Also, make sure to enable it in /etc/rc.conf.

// The traditional root hints mechanism. Use this, OR the slave zones below.
zone "." { type hint; file "named.root"; };

/*	Slaving the following zones from the root name servers has some
	significant advantages:
	1\. Faster local resolution for your users
	2\. No spurious traffic will be sent from your network to the roots
	3\. Greater resilience to any potential root server failure/DDoS

	On the other hand, this method requires more monitoring than the
	hints file to be sure that an unexpected failure mode has not
	incapacitated your server.  Name servers that are serving a lot
	of clients will benefit more from this approach than individual
	hosts.  Use with caution.

	To use this mechanism, uncomment the entries below, and comment
	the hint zone above.
*/
/*
zone "." {
	type slave;
	file "slave/root.slave";
	masters {
		192.5.5.241;	// F.ROOT-SERVERS.NET.
	};
	notify no;
};
zone "arpa" {
	type slave;
	file "slave/arpa.slave";
	masters {
		192.5.5.241;	// F.ROOT-SERVERS.NET.
	};
	notify no;
};
zone "in-addr.arpa" {
	type slave;
	file "slave/in-addr.arpa.slave";
	masters {
		192.5.5.241;	// F.ROOT-SERVERS.NET.
	};
	notify no;
};
*/

/*	Serving the following zones locally will prevent any queries
	for these zones leaving your network and going to the root
	name servers.  This has two significant advantages:
	1\. Faster local resolution for your users
	2\. No spurious traffic will be sent from your network to the roots
*/
// RFC 1912
zone "localhost"	{ type master; file "master/localhost-forward.db"; };
zone "127.in-addr.arpa" { type master; file "master/localhost-reverse.db"; };
zone "255.in-addr.arpa"	{ type master; file "master/empty.db"; };

// RFC 1912-style zone for IPv6 localhost address
zone "0.ip6.arpa"	{ type master; file "master/localhost-reverse.db"; };

// "This" Network (RFCs 1912 and 3330)
zone "0.in-addr.arpa"		{ type master; file "master/empty.db"; };

// Private Use Networks (RFC 1918)
zone "10.in-addr.arpa"		{ type master; file "master/empty.db"; };
zone "16.172.in-addr.arpa"	{ type master; file "master/empty.db"; };
zone "17.172.in-addr.arpa"	{ type master; file "master/empty.db"; };
zone "18.172.in-addr.arpa"	{ type master; file "master/empty.db"; };
zone "19.172.in-addr.arpa"	{ type master; file "master/empty.db"; };
zone "20.172.in-addr.arpa"	{ type master; file "master/empty.db"; };
zone "21.172.in-addr.arpa"	{ type master; file "master/empty.db"; };
zone "22.172.in-addr.arpa"	{ type master; file "master/empty.db"; };
zone "23.172.in-addr.arpa"	{ type master; file "master/empty.db"; };
zone "24.172.in-addr.arpa"	{ type master; file "master/empty.db"; };
zone "25.172.in-addr.arpa"	{ type master; file "master/empty.db"; };
zone "26.172.in-addr.arpa"	{ type master; file "master/empty.db"; };
zone "27.172.in-addr.arpa"	{ type master; file "master/empty.db"; };
zone "28.172.in-addr.arpa"	{ type master; file "master/empty.db"; };
zone "29.172.in-addr.arpa"	{ type master; file "master/empty.db"; };
zone "30.172.in-addr.arpa"	{ type master; file "master/empty.db"; };
zone "31.172.in-addr.arpa"	{ type master; file "master/empty.db"; };
zone "168.192.in-addr.arpa"	{ type master; file "master/empty.db"; };

// Link-local/APIPA (RFCs 3330 and 3927)
zone "254.169.in-addr.arpa"	{ type master; file "master/empty.db"; };

// TEST-NET for Documentation (RFC 3330)
zone "2.0.192.in-addr.arpa"	{ type master; file "master/empty.db"; };

// Router Benchmark Testing (RFC 3330)
zone "18.198.in-addr.arpa"	{ type master; file "master/empty.db"; };
zone "19.198.in-addr.arpa"	{ type master; file "master/empty.db"; };

// IANA Reserved - Old Class E Space
zone "240.in-addr.arpa"		{ type master; file "master/empty.db"; };
zone "241.in-addr.arpa"		{ type master; file "master/empty.db"; };
zone "242.in-addr.arpa"		{ type master; file "master/empty.db"; };
zone "243.in-addr.arpa"		{ type master; file "master/empty.db"; };
zone "244.in-addr.arpa"		{ type master; file "master/empty.db"; };
zone "245.in-addr.arpa"		{ type master; file "master/empty.db"; };
zone "246.in-addr.arpa"		{ type master; file "master/empty.db"; };
zone "247.in-addr.arpa"		{ type master; file "master/empty.db"; };
zone "248.in-addr.arpa"		{ type master; file "master/empty.db"; };
zone "249.in-addr.arpa"		{ type master; file "master/empty.db"; };
zone "250.in-addr.arpa"		{ type master; file "master/empty.db"; };
zone "251.in-addr.arpa"		{ type master; file "master/empty.db"; };
zone "252.in-addr.arpa"		{ type master; file "master/empty.db"; };
zone "253.in-addr.arpa"		{ type master; file "master/empty.db"; };
zone "254.in-addr.arpa"		{ type master; file "master/empty.db"; };

// IPv6 Unassigned Addresses (RFC 4291)
zone "1.ip6.arpa"		{ type master; file "master/empty.db"; };
zone "3.ip6.arpa"		{ type master; file "master/empty.db"; };
zone "4.ip6.arpa"		{ type master; file "master/empty.db"; };
zone "5.ip6.arpa"		{ type master; file "master/empty.db"; };
zone "6.ip6.arpa"		{ type master; file "master/empty.db"; };
zone "7.ip6.arpa"		{ type master; file "master/empty.db"; };
zone "8.ip6.arpa"		{ type master; file "master/empty.db"; };
zone "9.ip6.arpa"		{ type master; file "master/empty.db"; };
zone "a.ip6.arpa"		{ type master; file "master/empty.db"; };
zone "b.ip6.arpa"		{ type master; file "master/empty.db"; };
zone "c.ip6.arpa"		{ type master; file "master/empty.db"; };
zone "d.ip6.arpa"		{ type master; file "master/empty.db"; };
zone "e.ip6.arpa"		{ type master; file "master/empty.db"; };
zone "0.f.ip6.arpa"		{ type master; file "master/empty.db"; };
zone "1.f.ip6.arpa"		{ type master; file "master/empty.db"; };
zone "2.f.ip6.arpa"		{ type master; file "master/empty.db"; };
zone "3.f.ip6.arpa"		{ type master; file "master/empty.db"; };
zone "4.f.ip6.arpa"		{ type master; file "master/empty.db"; };
zone "5.f.ip6.arpa"		{ type master; file "master/empty.db"; };
zone "6.f.ip6.arpa"		{ type master; file "master/empty.db"; };
zone "7.f.ip6.arpa"		{ type master; file "master/empty.db"; };
zone "8.f.ip6.arpa"		{ type master; file "master/empty.db"; };
zone "9.f.ip6.arpa"		{ type master; file "master/empty.db"; };
zone "a.f.ip6.arpa"		{ type master; file "master/empty.db"; };
zone "b.f.ip6.arpa"		{ type master; file "master/empty.db"; };
zone "0.e.f.ip6.arpa"		{ type master; file "master/empty.db"; };
zone "1.e.f.ip6.arpa"		{ type master; file "master/empty.db"; };
zone "2.e.f.ip6.arpa"		{ type master; file "master/empty.db"; };
zone "3.e.f.ip6.arpa"		{ type master; file "master/empty.db"; };
zone "4.e.f.ip6.arpa"		{ type master; file "master/empty.db"; };
zone "5.e.f.ip6.arpa"		{ type master; file "master/empty.db"; };
zone "6.e.f.ip6.arpa"		{ type master; file "master/empty.db"; };
zone "7.e.f.ip6.arpa"		{ type master; file "master/empty.db"; };

// IPv6 ULA (RFC 4193)
zone "c.f.ip6.arpa"		{ type master; file "master/empty.db"; };
zone "d.f.ip6.arpa"		{ type master; file "master/empty.db"; };

// IPv6 Link Local (RFC 4291)
zone "8.e.f.ip6.arpa"		{ type master; file "master/empty.db"; };
zone "9.e.f.ip6.arpa"		{ type master; file "master/empty.db"; };
zone "a.e.f.ip6.arpa"		{ type master; file "master/empty.db"; };
zone "b.e.f.ip6.arpa"		{ type master; file "master/empty.db"; };

// IPv6 Deprecated Site-Local Addresses (RFC 3879)
zone "c.e.f.ip6.arpa"		{ type master; file "master/empty.db"; };
zone "d.e.f.ip6.arpa"		{ type master; file "master/empty.db"; };
zone "e.e.f.ip6.arpa"		{ type master; file "master/empty.db"; };
zone "f.e.f.ip6.arpa"		{ type master; file "master/empty.db"; };

// IP6.INT is Deprecated (RFC 4159)
zone "ip6.int"			{ type master; file "master/empty.db"; };

// NB: Do not use the IP addresses below, they are faked, and only
// serve demonstration/documentation purposes!
//
// Example slave zone config entries.  It can be convenient to become
// a slave at least for the zone your own domain is in.  Ask
// your network administrator for the IP address of the responsible
// master name server.
//
// Do not forget to include the reverse lookup zone!
// This is named after the first bytes of the IP address, in reverse
// order, with ".IN-ADDR.ARPA" appended, or ".IP6.ARPA" for IPv6.
//
// Before starting to set up a master zone, make sure you fully
// understand how DNS and BIND work.  There are sometimes
// non-obvious pitfalls.  Setting up a slave zone is usually simpler.
//
// NB: Don't blindly enable the examples below. :-)  Use actual names
// and addresses instead.

/* An example dynamic zone
key "exampleorgkey" {
	algorithm hmac-md5;
	secret "sf87HJqjkqh8ac87a02lla==";
};
zone "example.org" {
	type master;
	allow-update {
		key "exampleorgkey";
	};
	file "dynamic/example.org";
};
*/

/* Example of a slave reverse zone
zone "1.168.192.in-addr.arpa" {
	type slave;
	file "slave/1.168.192.in-addr.arpa";
	masters {
		192.168.1.1;
	};
};
*/
```

在 `named.conf` 中， 还给出了从域、转发域和反解析域的例子。

如果新增了域， 就必需在 `named.conf` 中加入对应的项目。

例如， 用于 `example.org` 的域文件的描述类似下面这样：

```
zone "example.org" {
	type master;
	file "master/example.org";
};
```

如 `type` 语句所标示的那样， 这是一个主域， 其信息保存在 `/etc/namedb/master/example.org` 中， 如 `file` 语句所示。

```
zone "example.org" {
	type slave;
	file "slave/example.org";
};
```

在从域的情形中， 所指定的域的信息会从主域名服务器传递过来， 并保存到对应的文件中。 当主域服务器发生问题或不可达时， 从域名服务器就有一份可用的域名信息， 从而能够对外提供服务。

#### 30.6.6.2. 域文件

下面的例子展示了用于 `example.org` 的主域文件 (存放于 `/etc/namedb/master/example.org`)：

```
$TTL 3600        ; 1 hour default TTL
example.org.    IN      SOA      ns1.example.org. admin.example.org. (
                                2006051501      ; Serial
                                10800           ; Refresh
                                3600            ; Retry
                                604800          ; Expire
                                300             ; Negative Reponse TTL
                        )

; DNS Servers
                IN      NS      ns1.example.org.
                IN      NS      ns2.example.org.

; MX Records
                IN      MX 10   mx.example.org.
                IN      MX 20   mail.example.org.

                IN      A       192.168.1.1

; Machine Names
localhost       IN      A       127.0.0.1
ns1             IN      A       192.168.1.2
ns2             IN      A       192.168.1.3
mx              IN      A       192.168.1.4
mail            IN      A       192.168.1.5

; Aliases
www             IN      CNAME   example.org.
```

请注意以 “.” 结尾的主机名是全称主机名， 而结尾没有 “.” 的则是相对于原点的主机名。 例如， `ns1` 将被转换为 `ns1.example.org.`

域信息文件的格式如下：

```
记录名          IN 记录类型     值
```

最常用的 DNS 记录：

SOA

域权威开始

NS

权威域名服务器

A

主机地址

CNAME

别名对应的正规名称

MX

邮件传递服务器

PTR

域名指针 (用于反向 DNS)

```
example.org. IN SOA ns1.example.org. admin.example.org. (
                        2006051501      ; Serial
                        10800           ; Refresh after 3 hours
                        3600            ; Retry after 1 hour
                        604800          ; Expire after 1 week
                        300 )           ; Negative Reponse TTL
```

`example.org.`

域名， 同时也是这个域信息文件的原点。

`ns1.example.org.`

该域的主/权威域名服务器。

`admin.example.org.`

此域的负责人的电子邮件地址， 其中 “@” 需要换掉 (`<admin@example.org>` 对应 `admin.example.org`)

`2006051501`

文件的序号。 每次修改域文件时都必须增加这个数字。 现今， 许多管理员会考虑使用 `yyyymmddrr` 这样的格式来表示序号。 `2006051501` 通常表示上次修改于 05/15/2006， 而后面的 `01` 则表示在那天的第一次修改。 序号非常重要， 它用于通知从域服务器更新数据。

```
       IN NS           ns1.example.org.
```

这是一个 NS 项。 每个准备提供权威应答的服务器都必须有一个对应项。

```
localhost       IN      A       127.0.0.1
ns1             IN      A       192.168.1.2
ns2             IN      A       192.168.1.3
mx              IN      A       192.168.1.4
mail            IN      A       192.168.1.5
```

A 记录指明了机器名。 正如在前面所看到的， `ns1.example.org` 将解析为 `192.168.1.2`。

```
                IN      A       192.168.1.1
```

这一行把当前原点 `example.org` 指定为使用 IP 地址 `192.168.1.1`。

```
www             IN CNAME        @
```

正规名 (CNAME) 记录通常用于为某台机器指定别名。 在这个例子中， 将 `www` 指定成了 “主” 机器的一个别名， 后者的名字与域名 `example.org` (`192.168.1.1`) 相同。 CNAME 不能同与之有相同名字的任何其它记录并存。

```
               IN MX   10      mail.example.org.
```

MX 记录表示哪个邮件服务器负责接收发到这个域的邮件。 `mail.example.org` 是邮件服务器的主机名， 而 10 则是它的优先级。

可以有多台邮件服务器， 其优先级分别是 10、 20 等等。 尝试向 `example.org` 投递邮件的服务器， 会首先尝试优先级最高的 MX (优先级数值最小的记录)、 接着尝试次高的， 并重复这一过程直到邮件递达为止。

in-addr.arpa 域名信息文件 (反向 DNS)， 采用的格式是同样的， 只是 PTR 项代替了 A 或 CNAME 的位置。

```
$TTL 3600

1.168.192.in-addr.arpa. IN SOA ns1.example.org. admin.example.org. (
                        2006051501      ; Serial
                        10800           ; Refresh
                        3600            ; Retry
                        604800          ; Expire
                        300 )           ; Negative Reponse TTL

        IN      NS      ns1.example.org.
        IN      NS      ns2.example.org.

1       IN      PTR     example.org.
2       IN      PTR     ns1.example.org.
3       IN      PTR     ns2.example.org.
4       IN      PTR     mx.example.org.
5       IN      PTR     mail.example.org.
```

这个文件给出了上述假想域中 IP 地址到域名的映射关系。

需要说明的是， 在 PTR 记录右侧的名字必须是全称域名 (也就是必须以 “.” 结束)。

### 30.6.7. 缓存域名服务器

缓存域名服务器是一种主要承担解析递归查询角色的域名服务器。 它简单地自行进行查询， 并将查询结果记住以备后续使用。

### 30.6.8. 安全

尽管 BIND 是最为常用的 DNS 实现， 但它总是有一些安全问题。 时常会有人发现一些可能的甚至可以利用的安全漏洞。

尽管 FreeBSD 会自动将 named 放到 [chroot(8)](http://www.FreeBSD.org/cgi/man.cgi?query=chroot&sektion=8&manpath=freebsd-release-ports) 环境中运行， 但仍有一些其它可用的安全机制来帮助您规避潜在的针对 DNS 服务的攻击。

阅读 [CERT](http://www.cert.org/) 的安全公告， 并订阅 the [FreeBSD 安全问题通知邮件列表](http://lists.FreeBSD.org/mailman/listinfo/freebsd-security-notifications) 是一个有助于帮助您了解最新 Internet 及 FreeBSD 安全问题的好习惯。

### 提示:

如果发现了问题， 确保源代码是最新的， 并重新联编一份 named 有可能会有所帮助。

### 30.6.9. 进一步阅读

BIND/named 联机手册： [rndc(8)](http://www.FreeBSD.org/cgi/man.cgi?query=rndc&sektion=8&manpath=freebsd-release-ports) [named(8)](http://www.FreeBSD.org/cgi/man.cgi?query=named&sektion=8&manpath=freebsd-release-ports) [named.conf(5)](http://www.FreeBSD.org/cgi/man.cgi?query=named.conf&sektion=5&manpath=freebsd-release-ports)

*   [官方的 ISC BIND 页面](https://www.isc.org/software/bind)

*   [Official ISC BIND Forum](https://www.isc.org/software/guild)

*   [O'Reilly DNS 和 BIND 第 5 版](http://www.oreilly.com/catalog/dns5/)

*   [RFC1034 - 域名 - 概念和工具](http://www.rfc-editor.org/rfc/rfc1034.txt)

*   [RFC1035 - 域名 - 实现及其标准](http://www.rfc-editor.org/rfc/rfc1035.txt)

## 30.7. Apache HTTP 服务器

Contributed by Murray Stokely.

### 30.7.1. 纵览

FreeBSD 被用于运行许多全球最为繁忙的 web 站点。 大多数 Internet 上的 web 服务器， 都使用 Apache HTTP 服务器。 Apache 软件包可以在您的 FreeBSD 安装盘上找到。 如果没有在首次安装时附带安装 Apache， 则可以通过 [www/apache13](http://www.freebsd.org/cgi/url.cgi?ports/www/apache13/pkg-descr) 或 [www/apache22](http://www.freebsd.org/cgi/url.cgi?ports/www/apache22/pkg-descr) port 来安装。

一旦成功地安装了 Apache， 就必须对其进行配置。

### 注意:

这一节介绍了 1.3.X 版本的 Apache HTTP 服务器 的配置， 因为它是随 FreeBSD 一同使用的最多的版本。 Apache 2.X 引入了很多新技术， 但在此并不讨论。 要了解关于 Apache 2.X 的更多资料， 请参见 `[`httpd.apache.org/`](http://httpd.apache.org/)`。

### 30.7.2. 配置

主要的 Apache HTTP Server 配置文件， 在 FreeBSD 上会安装为 `/usr/local/etc/apache/httpd.conf`。 这是一个典型的 UNIX® 文本配置文件， 它使用 `#` 作为注释符。 关于全部配置选项的详尽介绍超出了本书的范围， 这里将只介绍最常被修改的那些。

`ServerRoot "/usr/local"`

这指定了 Apache 安装的顶级目录。 执行文件被放到服务器根目录 (server root) 的 `bin` 和 `sbin` 子目录中， 而配置文件则位于 `etc/apache`。

`ServerAdmin you@your.address`

这个地址是在服务器发生问题时应发送电子邮件的地址， 它会出现在服务器生成的页面上， 例如错误页面。

`ServerName www.example.com`

`ServerName` 允许您配置发送回客户端的主机名， 如果您的服务器被用户以别的名字访问 (例如， 使用 `www` 而不是主机本身的真实名字)。

`DocumentRoot "/usr/local/www/data"`

`DocumentRoot`： 这个目录是您的文档所在的目录。 默认情况下， 所有的请求都会从这个位置去获取， 但也可以通过符号连接和别名指定其它的位置。

在修改配置之前备份 Apache 的配置文件永远是一个好习惯。 一旦对初始配置满意了， 就可以开始运行 Apache 了。

### 30.7.3. 运行 Apache

与许多其它网络服务不同， Apache 并不依赖 inetd 超级服务器来运行。 一般情况下会把它配置为一个独立的服务器， 以期在客户的 web 浏览器连入 HTTP 请求时， 能够获得更好的性能。 它提供了一个 shell 脚本来使启动、 停止和重新启动服务器变得尽可能地简单。 首次启动 Apache， 只需执行：

```
# `/usr/local/sbin/apachectl start`
```

可以在任何时候使用下面的命令来停止服务：

```
# `/usr/local/sbin/apachectl stop`
```

当由于某种原因修改了配置文件之后， 需要重启服务器：

```
# `/usr/local/sbin/apachectl restart`
```

要在重启 Apache 服务器时不中断当前的连接， 则应运行：

```
# `/usr/local/sbin/apachectl graceful`
```

更多的信息， 可以在 [apachectl(8)](http://www.FreeBSD.org/cgi/man.cgi?query=apachectl&sektion=8&manpath=freebsd-release-ports) 联机手册中找到。

要在系统启动时启动 Apache， 则应在 `/etc/rc.conf` 中加入：

```
apache_enable="YES"
```

或者对于 Apache 2.2：

```
apache22_enable="YES"
```

如果您希望在系统引导时启动 Apache `httpd` 程序并指定其它一些选项， 则可以把下面的行加到 `rc.conf`：

```
apache_flags=""
```

现在 web 服务器就开始运行了， 您可以使用 web 浏览器打开 `http://localhost/`。 默认显示的 web 页面是 `/usr/local/www/data/index.html`。

### 30.7.4. 虚拟主机

Apache 支持两种不同类型的虚拟主机。 第一种方法是基于名字的虚拟主机。 基于名字的虚拟主机使用客户机发来的 HTTP/1.1 头来辨别主机名。 这使得不同的域得以共享同一个 IP 地址。

要配置 Apache 来使用基于名字的虚拟主机， 需要把类似下面的项加到您的 `httpd.conf` 中：

```
NameVirtualHost *
```

如果您的 web 服务器的名字是 `www.domain.tld`， 而您希望建立一个 `www.someotherdomain.tld` 的虚拟域， 则应在 `httpd.conf` 中加入：

```
<VirtualHost *>
ServerName www.domain.tld
DocumentRoot /www/domain.tld
</VirtualHost>

<VirtualHost *>
ServerName www.someotherdomain.tld
DocumentRoot /www/someotherdomain.tld
</VirtualHost>
```

您需要把上面的地址和文档路径改为所使用的那些。

要了解关于虚拟主机的更多信息， 请参考官方的 Apache 文档， 这些文档可以在 `[`httpd.apache.org/docs/vhosts/`](http://httpd.apache.org/docs/vhosts/)` 找到。

### 30.7.5. Apache 模块

有许多不同的 Apache 模块， 它们可以在基本的服务器基础上提供许多附加的功能。 FreeBSD 的 Ports Collection 为安装 Apache 和常用的附加模块提供了非常方便的方法。

#### 30.7.5.1. mod_ssl

mod_ssl 这个模块使用 OpenSSL 库， 来提供通过 安全套接字层 (SSL v2/v3) 和 传输层安全 (TLS v1) 协议的强加密能力。 这个模块提供了从某一受信的证书签署机构申请签名证书所需的所有工具， 您可以藉此在 FreeBSD 上运行安全的 web 服务器。

如果您未曾安装 Apache， 也可以直接安装一份包含了 mod_ssl 的版本的 Apache 1.3.X， 其方法是通过 [www/apache13-modssl](http://www.freebsd.org/cgi/url.cgi?ports/www/apache13-modssl/pkg-descr) port 来进行。 SSL 支持已经作为 Apache 2.X 的一部分提供， 您可以通过 [www/apache22](http://www.freebsd.org/cgi/url.cgi?ports/www/apache22/pkg-descr) port 来安装后者。

#### 30.7.5.2. 语言绑定

Apache 对于一些主要的脚本语言都有相应的模块。 这些模块使得完全使用某种脚本语言来写 Apache 模块成为可能。 他们通常也被嵌入到服务器作为一个常驻内存的解释器， 以避免启动一个外部解释器对于下一节将描述的动态网站所需时间和资源上的开销。

### 30.7.6. 动态网站

在过去的十年里，越来越多的企业为了增加收益和暴光率而转向了互联网。 这也同时增进了对于互动网页内容的需求。有些公司，比如 Microsoft® 推出了基于他们专有产品的解决方案，开源社区也做出了积极的回应。 比较时尚的选择包括 Django，Ruby on Rails， mod_perl, and mod_php.

#### 30.7.6.1. Django

Django 是一个以 BSD 许可证发布的 framework， 能让开发者快速写出高性能高品质的 web 应用程序。 它提供给一个对象关系映射组件，数据类型可以被当 Python 中的对象，和一组丰富的动态数据库访问 API， 使开发者避免了写 SQL 语句。它同时还提供了可扩展的模板系统， 让应用程序的逻辑部分与 HTML 的表现层分离。

Django 依赖与 mod_python， Apache, 和一个可选的 SQL 数据库引擎。 在设置了一些恰当的标志后，FreeBSD 的 Port 系统将会帮助你安装这些必需的依赖库。

例 30.3. 安装 Django，Apache2， mod_python3，和 PostgreSQL

```
# `cd /usr/ports/www/py-django; make all install clean -DWITH_MOD_PYTHON3 -DWITH_POSTGRESQL`
```

在安装了 Django 和那些依赖的软件之后， 你需要创建一个 Django 项目的目录，然后配置 Apache，当有对于你网站上应用程序的某些指定的 URL 时调用内嵌的 Python 解释器。

例 30.4. Django/mod_python 有关 Apache 部分的配置

你需要在 Apache 的配置文件 `httpd.conf` 加入以下这几行， 把对某些 URL 的请求传给你的 web 应用程序：

```
<Location "/">
    SetHandler python-program
    PythonPath "['/dir/to/your/django/packages/'] + sys.path"
    PythonHandler django.core.handlers.modpython
    SetEnv DJANGO_SETTINGS_MODULE mysite.settings
    PythonAutoReload On
    PythonDebug On
</Location>
```

#### 30.7.6.2. Ruby on Rails

Ruby on Rails 是另外一个开源的 web framework， 提供了一个全面的开发框架，能帮助 web 开发者工作更有成效和快速写出强大的应用。 它能非常容易的从 posts 系统安装。

```
# `cd /usr/ports/www/rubygem-rails; make all install clean`
```

#### 30.7.6.3. mod_perl

Apache/Perl 集成计划， 将 Perl 程序设计语言的强大功能， 与 Apache HTTP 服务器 紧密地结合到了一起。 通过 mod_perl 模块， 可以完全使用 Perl 来撰写 Apache 模块。 此外， 服务器中嵌入的持久性解释器， 消除了由于启动外部的解释器为 Perl 脚本的启动所造成的性能损失。

mod_perl 通过多种方式提供。 要使用 mod_perl， 应该注意 mod_perl 1.0 只能配合 Apache 1.3 而 mod_perl 2.0 只能配合 Apache 2.X 使用。 mod_perl 1.0 可以通过 [www/mod_perl](http://www.freebsd.org/cgi/url.cgi?ports/www/mod_perl/pkg-descr) 安装， 而以静态方式联编的版本， 则可以通过 [www/apache13-modperl](http://www.freebsd.org/cgi/url.cgi?ports/www/apache13-modperl/pkg-descr) 来安装。 mod_perl 2.0 则可以通过 [www/mod_perl2](http://www.freebsd.org/cgi/url.cgi?ports/www/mod_perl2/pkg-descr) 安装。

#### 30.7.6.4. mod_php

Written by Tom Rhodes.

PHP， 也称为 “PHP: Hypertext Preprocessor”， 是一种特别适合于 Web 开发的通用脚本语言。 它能够很容易地嵌入到 HTML 之中， 其语法接近于 C、 Java™， 以及 Perl， 以期让 web 开发人员的一迅速撰写动态生成的页面。

要获得用于 Apache web 服务器的 PHP5 支持， 可以从安装 [lang/php5](http://www.freebsd.org/cgi/url.cgi?ports/lang/php5/pkg-descr) port 开始。

在首次安装 [lang/php5](http://www.freebsd.org/cgi/url.cgi?ports/lang/php5/pkg-descr) port 的时候， 系统会自动显示可用的一系列 `OPTIONS` (配置选项)。 如果您没有看到菜单， 例如由于过去曾经安装过 [lang/php5](http://www.freebsd.org/cgi/url.cgi?ports/lang/php5/pkg-descr) port 等等， 可以用下面的命令再次显示配置菜单， 在 port 的目录中执行：

```
# `make config`
```

在配置选项对话框中， 选中 `APACHE` 这一项， 就可以联编出用于与 Apache web 服务器配合使用的可动态加载的 mod_php5 模块了。

### 注意:

由于各式各样的原因 (例如， 出于已经部署的 web 应用的兼容性考虑)， 许多网站仍在使用 PHP4。 如果您需要 mod_php4 而不是 mod_php5， 请使用 [lang/php4](http://www.freebsd.org/cgi/url.cgi?ports/lang/php4/pkg-descr) port。 [lang/php4](http://www.freebsd.org/cgi/url.cgi?ports/lang/php4/pkg-descr) port 也支持许多 [lang/php5](http://www.freebsd.org/cgi/url.cgi?ports/lang/php5/pkg-descr) port 提供的配置和编译时选项。

前面我们已经成功地安装并配置了用于支持动态 PHP 应用所需的模块。 请检查并确认您已将下述配置加入到了 `/usr/local/etc/apache/httpd.conf` 中：

```
LoadModule php5_module        libexec/apache/libphp5.so
```

```
AddModule mod_php5.c
    <IfModule mod_php5.c>
        DirectoryIndex index.php index.html
    </IfModule>
    <IfModule mod_php5.c>
        AddType application/x-httpd-php .php
        AddType application/x-httpd-php-source .phps
    </IfModule>
```

这些工作完成之后， 还需要使用 `apachectl` 命令来完成一次 graceful restart 以便加载 PHP 模块：

```
# `apachectl graceful`
```

在未来您升级 PHP 时， `make config` 这步操作就不再是必需的了； 您所选择的 `OPTIONS` 会由 FreeBSD 的 Ports 框架自动保存。

在 FreeBSD 中的 PHP 支持是高度模块化的， 因此基本安装的功能十分有限。 增加其他功能的支持非常简单， 只需通过 [lang/php5-extensions](http://www.freebsd.org/cgi/url.cgi?ports/lang/php5-extensions/pkg-descr) port 即可完成。 这个 port 提供了一个菜单驱动的界面来帮助完成 PHP 扩展的安装。 另外， 也可以通过对应的 port 来单独安装扩展。

例如， 要将对于 MySQL 数据库服务器的支持加入 PHP5， 只需简单地安装 `databases/php5-mysql`。

安装完扩展之后， 必须重新启动 Apache 服务器， 来令其适应新的配置变更：

```
# `apachectl graceful`
```

## 30.8. 文件传输协议 (FTP)

Contributed by Murray Stokely.

### 30.8.1. 纵览

文件传输协议 (FTP) 为用户提供了一个简单的， 与 FTP 服务器交换文件的方法。 FreeBSD 系统中包含了 FTP 服务软件， ftpd。 这使得在 FreeBSD 上建立和管理 FTP 服务器变得非常简单。

### 30.8.2. 配置

最重要的配置步骤是决定允许哪些帐号访问 FTP 服务器。 一般的 FreeBSD 系统包含了一系列系统帐号分别用于执行不同的服务程序， 但未知的用户不应被允许登录并使用这些帐号。 `/etc/ftpusers` 文件中， 列出了不允许通过 FTP 访问的用户。 默认情况下， 这包含了前述的系统帐号， 但也可以在这里加入其它不应通过 FTP 访问的用户。

您可能会希望限制通过 FTP 登录的某些用户， 而不是完全阻止他们使用 FTP。 这可以通过 `/etc/ftpchroot` 文件来完成。 这一文件列出了希望对 FTP 访问进行限制的用户和组的表。 而在 [ftpchroot(5)](http://www.FreeBSD.org/cgi/man.cgi?query=ftpchroot&sektion=5&manpath=freebsd-release-ports) 联机手册中， 已经对此进行了详尽的介绍， 故而不再赘述。

如果您想要在服务器上启用匿名的 FTP 访问， 则必须建立一个名为 `ftp` 的 FreeBSD 用户。 这样， 用户就可以使用 `ftp` 或 `anonymous` 和任意的口令 (习惯上， 应该是以那个用户的邮件地址作为口令) 来登录和访问您的 FTP 服务器。 FTP 服务器将在匿名用户登录时调用 [chroot(2)](http://www.FreeBSD.org/cgi/man.cgi?query=chroot&sektion=2&manpath=freebsd-release-ports)， 以便将其访问限制在 `ftp` 用户的主目录中。

有两个文本文件可以用来指定显示在 FTP 客户程序中的欢迎文字。 `/etc/ftpwelcome` 文件中的内容将在用户连接上之后， 在登录提示之前显示。 在成功的登录之后， 将显示 `/etc/ftpmotd` 文件中的内容。 请注意后者是相对于登录环境的， 因此对于匿名用户而言， 将显示 `~ftp/etc/ftpmotd`。

一旦正确地配置了 FTP 服务器， 就必须在 `/etc/inetd.conf` 中启用它。 这里需要做的全部工作就是将注释符 “#” 从已有的 ftpd 行之前去掉：

```
ftp	stream	tcp	nowait	root	/usr/libexec/ftpd	ftpd -l
```

如 例 30.1 “重新加载 inetd 配置文件” 所介绍的那样， 修改这个文件之后， 必须让 inetd 重新加载它， 才能使新的设置生效。请参阅 第 30.2.2 节 “设置” 以获取更多有关如何在你系统上启用 inetd 的详细信息。

ftpd 也可以作为一个独立的服务启动。 这样的话就需要在 `/etc/rc.conf` 中设置如下的变量：

```
ftpd_enable="YES"
```

在设置了上述变量之后，独立的服务将在下次系统重启的时候启动， 或者通过以 `root` 身份手动执行如下的命令启动：

```
# `/etc/rc.d/ftpd start`
```

现在可以通过输入下面的命令来登录您的 FTP 服务器了：

```
% `ftp localhost`
```

### 30.8.3. 维护

ftpd 服务程序使用 [syslog(3)](http://www.FreeBSD.org/cgi/man.cgi?query=syslog&sektion=3&manpath=freebsd-release-ports) 来记录消息。 默认情况下， 系统日志将把和 FTP 相关的消息记录到 `/var/log/xferlog` 文件中。 FTP 日志的位置， 可以通过修改 `/etc/syslog.conf` 中如下所示的行来修改：

```
ftp.info      /var/log/xferlog
```

一定要小心对待在匿名 FTP 服务器中可能遇到的潜在问题。 一般而言， 允许匿名用户上传文件应三思。 您可能发现自己的 FTP 站点成为了交易未经授权的商业软件的论坛， 或发生更糟糕的情况。 如果不需要匿名的 FTP 上传， 可以在文件上配置权限， 使得您能够在其它匿名用户能够下载这些文件之前复查它们。

## 30.9. 为 Microsoft® Windows® 客户机提供文件和打印服务 (Samba)

Contributed by Murray Stokely.

### 30.9.1. 纵览

Samba 是一个流行的开源软件包， 它提供了针对 Microsoft® Windows® 客户机的文件和打印服务。 这类客户机可以连接并使用 FreeBSD 系统上的文件空间， 就如同使用本地的磁盘一样， 或者像使用本地打印机一样使用 FreeBSD 上的打印机。

Samba 软件包可以在您的 FreeBSD 安装盘上找到。 如果您没有在初次安装 FreeBSD 时安装 Samba， 则可以通过 [net/samba34](http://www.freebsd.org/cgi/url.cgi?ports/net/samba34/pkg-descr) port 或 package 来安装。

### 30.9.2. 配置

默认的 Samba 配置文件会以 `/usr/local/share/examples/samba34/smb.conf.default` 的名字安装。这个文件必须复制为 `/usr/local/etc/smb.conf` 并进行定制， 才能开始使用 Samba。

`smb.conf` 文件中包含了 Samba 的运行时配置信息， 例如对于打印机的定义， 以及希望共享给 Windows® 客户机的 “共享文件系统”。 Samba 软件包包含了一个称为 swat 的 web 管理工具， 后者提供了配置 `smb.conf` 文件的简单方法。

#### 30.9.2.1. 使用 Samba Web 管理工具 (SWAT)

Samba Web 管理工具 (SWAT) 是一个通过 inetd 运行的服务程序。 因此， 需要把 `/etc/inetd.conf` 中下面几行的注释去掉， 才能够使用 swat 来配置 Samba：

```
swat   stream  tcp     nowait/400      root    /usr/local/sbin/swat    swat
```

如 例 30.1 “重新加载 inetd 配置文件” 中所介绍的那样， 在修改了这个配置文件之后， 必须让 inetd 重新加载配置， 才能使其生效。

一旦在 `inetd.conf` 中启用了 swat， 就可以用浏览器访问 connect to `[`localhost:901`](http://localhost:901)` 了。 您将首先使用系统的 `root` 帐号登录。

只要成功地登录进了 Samba 配置页面， 就可以浏览系统的文档， 或从 Globals(全局) 选项卡开始配置了。 Globals 小节对应于 `[global]` 小节中的变量， 前者位于 `/usr/local/etc/smb.conf` 中。

#### 30.9.2.2. 全局配置

无论是使用 swat， 还是直接编辑 `/usr/local/etc/smb.conf`， 通常首先要配置的 Samba 选项都是：

`workgroup`

NT 域名或工作组名， 其他计算机将通过这些名字来找到服务器。

`netbios name`

这个选项用于设置 Samba 服务器的 NetBIOS 名字。 默认情况下， 这是所在主机的 DNS 名字的第一部分。

`server string`

这个选项用于设置通过 `net view` 命令， 以及某些其他网络工具可以查看到的关于服务器的说明性文字。

#### 30.9.2.3. 安全配置

在 `/usr/local/etc/smb.conf` 中的两个最重要的配置， 是选定的安全模型， 以及客户机上用户的口令存放后端。 下面的语句控制这些选项：

`security`

最常见的选项形式是 `security = share` 和 `security = user`。 如果您的客户机使用用户名， 并且这些用户名与您的 FreeBSD 机器一致， 一般应选择用户级 (user) 安全。 这是默认的安全策略， 它要求客户机首先登录， 然后才能访问共享的资源。

如果采用共享级 (share) 安全， 则客户机不需要用有效的用户名和口令登录服务器， 就能够连接共享的资源。 这是较早版本的 Samba 中的默认值。

`passdb backend`

Samba 提供了若干种不同的验证后端模型。 您可以通过 LDAP、 NIS+、 SQL 数据库， 或经过修改的口令文件， 来完成客户端的身份验证。 默认的验证模式是 `smbpasswd`， 这也是本章将介绍的全部内容。

假设您使用的是默认的 `smbpasswd` 后端， 则必须首先创建一个 `/usr/local/etc/samba/smbpasswd` 文件， 来允许 Samba 对客户进行身份验证。 如果您打算让 UNIX® 用户帐号能够从 Windows® 客户机上登录， 可以使用下面的命令：

```
# `smbpasswd -a username`
```

### 注意:

目前推荐使用的后端是 `tdbsam`， 您应使用下面的命令来添加用户帐号：

```
# `pdbedit -a -u username`
```

请参考 [官方的 Samba HOWTO](http://www.samba.org/samba/docs/man/Samba-HOWTO-Collection/) 以了解关于配置选项的进一步信息。 按照前面给出的基本描述， 您应该已经可以启动 Samba 了。

### 30.9.3. 启动 Samba

[net/samba34](http://www.freebsd.org/cgi/url.cgi?ports/net/samba34/pkg-descr) port 会增加一个新的用于控制 Samba 的启动脚本。 要启用这个脚本， 以便用它来完成启动、 停止或重启 Samba 的任务， 需要在 `/etc/rc.conf` 文件中加入：

```
samba_enable="YES"
```

此外， 也可以进行更细粒度的控制：

```
nmbd_enable="YES"
```

```
smbd_enable="YES"
```

### 注意:

这也同时配置了在系统引导时启动 Samba。

配置好之后， 就可以在任何时候通过下面的命令来启动 Samba 了：

```
# `/usr/local/etc/rc.d/samba start`
Starting SAMBA: removing stale tdbs :
Starting nmbd.
Starting smbd.
```

请参见 第 12.7 节 “在 FreeBSD 中使用 rc” 以了解关于使用 rc 脚本的进一步信息。

Samba 事实上包含了三个相互独立的服务程序。 您应该能够看到 nmbd 和 smbd 两个服务程序都是通过 `samba` 脚本启动的。 如果在 `smb.conf` 中启用了 winbind 名字解析服务， 则应该可以看到 winbindd 服务被启动起来。

可以在任何时候通过下面的命令来停止运行 Samba：

```
# `/usr/local/etc/rc.d/samba stop`
```

Samba 是一个复杂的软件包， 它提供了用于与 Microsoft® Windows® 网络进行集成的各式各样的功能。 要了解关于这里所介绍的基本安装以外的其它功能， 请访问 `[`www.samba.org`](http://www.samba.org)`。

## 30.10. 通过 NTP 进行时钟同步

Contributed by Tom Hukins.

### 30.10.1. 纵览

随着时间的推移， 计算机的时钟会倾向于漂移。 网络时间协议 (NTP) 是一种确保您的时钟保持准确的方法。

许多 Internet 服务依赖、 或极大地受益于本地计算机时钟的准确性。 例如， web 服务器可能会接收到一个请求， 要求如果文件在某一时刻之后修改过才发送它。 在局域网环境中， 共享文件的计算机之间的时钟是否同步至关重要， 因为这样才能使时间戳保持一致。 类似 [cron(8)](http://www.FreeBSD.org/cgi/man.cgi?query=cron&sektion=8&manpath=freebsd-release-ports) 这样的程序， 也依赖于正确的系统时钟， 才能够准确地执行操作。

FreeBSD 附带了 [ntpd(8)](http://www.FreeBSD.org/cgi/man.cgi?query=ntpd&sektion=8&manpath=freebsd-release-ports) NTP 服务器， 它可以用于查询其它的 NTP 服务器， 并配置本地计算机的时钟， 或者为其它机器提供服务。

### 30.10.2. 选择合适的 NTP 服务器

为了同步您的系统时钟， 需要首先找到至少一个 NTP 服务器以供使用。 网络管理员， 或 ISP 都可能会提供用于这样目的的 NTP 服务器──请查看他们的文档以了解是否是这样。 另外， 也有一个在线的 [公开的 NTP 服务器列表](http://ntp.isc.org/bin/view/Servers/WebHome)， 您可以从中选一个较近的 NTP 服务器。 请确认您选择的服务器的访问策略， 如果需要的话， 申请一下所需的许可。

选择多个相互不连接的 NTP 服务器是一个好主意， 这样在某个服务器不可达， 或者时钟不可靠时就可以有别的选择。 这是因为， [ntpd(8)](http://www.FreeBSD.org/cgi/man.cgi?query=ntpd&sektion=8&manpath=freebsd-release-ports) 会智能地选择它收到的响应──它会更倾向于使用可靠的服务器。

### 30.10.3. 配置您的机器

#### 30.10.3.1. 基本配置

如果只想在系统启动时同步时钟， 则可以使用 [ntpdate(8)](http://www.FreeBSD.org/cgi/man.cgi?query=ntpdate&sektion=8&manpath=freebsd-release-ports)。 对于经常重新启动， 并且不需要经常同步的桌面系统来说这比较适合， 但绝大多数机器都应该运行 [ntpd(8)](http://www.FreeBSD.org/cgi/man.cgi?query=ntpd&sektion=8&manpath=freebsd-release-ports)。

在引导时使用 [ntpdate(8)](http://www.FreeBSD.org/cgi/man.cgi?query=ntpdate&sektion=8&manpath=freebsd-release-ports) 来配合运行 [ntpd(8)](http://www.FreeBSD.org/cgi/man.cgi?query=ntpd&sektion=8&manpath=freebsd-release-ports) 也是一个好主意。 [ntpd(8)](http://www.FreeBSD.org/cgi/man.cgi?query=ntpd&sektion=8&manpath=freebsd-release-ports) 渐进地修正时钟， 而 [ntpdate(8)](http://www.FreeBSD.org/cgi/man.cgi?query=ntpdate&sektion=8&manpath=freebsd-release-ports) 则直接设置时钟， 无论机器的当前时间和正确时间有多大的偏差。

要启用引导时的 [ntpdate(8)](http://www.FreeBSD.org/cgi/man.cgi?query=ntpdate&sektion=8&manpath=freebsd-release-ports)， 需要把 `ntpdate_enable="YES"` 加到 `/etc/rc.conf` 中。 此外， 还需要通过 `ntpdate_flags` 来设置同步的服务器和选项， 它们将传递给 [ntpdate(8)](http://www.FreeBSD.org/cgi/man.cgi?query=ntpdate&sektion=8&manpath=freebsd-release-ports)。

#### 30.10.3.2. 一般配置

NTP 是通过 `/etc/ntp.conf` 文件来进行配置的， 其格式在 [ntp.conf(5)](http://www.FreeBSD.org/cgi/man.cgi?query=ntp.conf&sektion=5&manpath=freebsd-release-ports) 中进行了描述。 下面是一个例子：

```
server ntplocal.example.com prefer
server timeserver.example.org
server ntp2a.example.net

driftfile /var/db/ntp.drift
```

这里， `server` 选项指定了使用哪一个服务器， 每一个服务器都独立一行。 如果某一台服务器上指定了 `prefer` (偏好) 参数， 如上面的 `ntplocal.example.com`， 则会优先选择这个服务器。 如果偏好的服务器和其他服务器的响应存在显著的差别， 则丢弃它的响应， 否则将使用来自它的响应， 而不理会其他服务器。 一般来说， `prefer` 参数应该标注在非常精确的 NTP 时源， 例如那些包含特殊的时间监控硬件的服务器上。

而 `driftfile` 选项， 则指定了用来保存系统时钟频率偏差的文件。 [ntpd(8)](http://www.FreeBSD.org/cgi/man.cgi?query=ntpd&sektion=8&manpath=freebsd-release-ports) 程序使用它来自动地补偿时钟的自然漂移， 从而使时钟即使在切断了外来时源的情况下， 仍能保持相当的准确度。

另外， `driftfile` 选项也保存上一次响应所使用的 NTP 服务器的信息。 这个文件包含了 NTP 的内部信息， 它不应被任何其他进程修改。

#### 30.10.3.3. 控制您的服务器的访问

默认情况下， NTP 服务器可以被整个 Internet 上的主机访问。 如果在 `/etc/ntp.conf` 中指定 `restrict` 参数， 则可以控制允许哪些机器访问您的服务器。

如果希望拒绝所有的机器访问您的 NTP 服务器， 只需在 `/etc/ntp.conf` 中加入：

```
restrict default ignore
```

### 注意:

这样做会禁止您的服务器访问在本地配置中列出的服务器。 如果您需要令 NTP 服务器与外界的 NTP 服务器同步时间， 则应允许指定服务器。 请参见联机手册 [ntp.conf(5)](http://www.FreeBSD.org/cgi/man.cgi?query=ntp.conf&sektion=5&manpath=freebsd-release-ports) 以了解进一步的细节。

如果只希望子网内的机器通过您的服务器同步时钟， 而不允许它们配置为服务器， 或作为同步时钟的节点来时用， 则加入

```
restrict 192.168.1.0 mask 255.255.255.0 nomodify notrap
```

这里， 需要把 `192.168.1.0` 改为您网络上的 IP 地址， 并把 `255.255.255.0` 改为您的子网掩码。

`/etc/ntp.conf` 可能包含多个 `restrict` 选项。 要了解进一步的细节， 请参见 [ntp.conf(5)](http://www.FreeBSD.org/cgi/man.cgi?query=ntp.conf&sektion=5&manpath=freebsd-release-ports) 的 `Access Control Support`(访问控制支持) 小节。

### 30.10.4. 运行 NTP 服务器

要让 NTP 服务器在系统启动时随之开启， 需要把 `ntpd_enable="YES"` 加入到 `/etc/rc.conf` 中。 如果希望向 [ntpd(8)](http://www.FreeBSD.org/cgi/man.cgi?query=ntpd&sektion=8&manpath=freebsd-release-ports) 传递更多参数， 需要编辑 `/etc/rc.conf` 中的 `ntpd_flags`。

要在不重新启动机器的前提下启动服务器， 需要手工运行 `ntpd`， 并带上 `/etc/rc.conf` 中的 `ntpd_flags` 所指定的参数。 例如：

```
# `ntpd -p /var/run/ntpd.pid`
```

### 30.10.5. 在临时性的 Internet 连接上使用 ntpd

[ntpd(8)](http://www.FreeBSD.org/cgi/man.cgi?query=ntpd&sektion=8&manpath=freebsd-release-ports) 程序的正常工作并不需要永久性的 Internet 连接。 然而， 如果您的临时性连接是配置为按需拨号的， 那么防止 NTP 通讯频繁触发拨号， 或保持连接就有必要了。 如果您使用用户级 PPP， 可以使用 `filter` 语句， 在 `/etc/ppp/ppp.conf` 中进行必要的设置。 例如：

```
 set filter dial 0 deny udp src eq 123
 # Prevent NTP traffic from initiating dial out
 set filter dial 1 permit 0 0
 set filter alive 0 deny udp src eq 123
 # Prevent incoming NTP traffic from keeping the connection open
 set filter alive 1 deny udp dst eq 123
 # Prevent outgoing NTP traffic from keeping the connection open
 set filter alive 2 permit 0/0 0/0
```

要了解进一步的信息， 请参考 [ppp(8)](http://www.FreeBSD.org/cgi/man.cgi?query=ppp&sektion=8&manpath=freebsd-release-ports) 的 `PACKET FILTERING`(包过滤) 小节， 以及 `/usr/share/examples/ppp/` 中的例子。

### 注意:

某些 Internet 访问提供商会阻止低编号的端口， 这会导致 NTP 无法正常工作， 因为响应无法到达您的机器。

### 30.10.6. 进一步的信息

关于 NTP 服务器的文档， 可以在 `/usr/share/doc/ntp/` 找到 HTML 格式的版本。

## 30.11. 使用 `syslogd` 记录远程主机的日志

Contributed by Tom Rhodes.

处理系统日志对于系统安全和管理是一个重要方面。 当有多台分布在中型或大型网络的机器，再或者是处于各种不同类型的网络中， 监视他们上面的日志文件则显得非常难以操作， 在这种情况下， 配置远程日志记录能使整个处理过程变得更加轻松。

集中记录日志到一台指定的机器能够减轻一些日志文件管理的负担。 日志文件的收集， 合并与循环可以在一处配置， 使用 FreeBSD 原生的工具， 比如 [syslogd(8)](http://www.FreeBSD.org/cgi/man.cgi?query=syslogd&sektion=8&manpath=freebsd-release-ports) 和 [newsyslog(8)](http://www.FreeBSD.org/cgi/man.cgi?query=newsyslog&sektion=8&manpath=freebsd-release-ports)。 在以下的配置示例中， 主机 `A`， 命名为 `logserv.example.com`， 将用来收集本地网络的日志信息。 主机 `B`， 命名为 `logclient.example.com` 将把日志信息传送给服务器。 在现实中， 这两个主机都需要配置正确的正向和反向的 DNS 或者在 `/etc/hosts` 中记录。 否则， 数据将被服务器拒收。

### 30.11.1. 日志服务器的配置

日志服务器是配置成用来接收远程主机日志信息的机器。 在大多数的情况下这是为了方便配置， 或者是为了更好的管理。 不论是何原因， 在继续深入之前需要提一些必需条件。

一个正确配置的日志服务器必须符合以下几个最基本的条件：

*   服务器和客户端的防火墙规则允许 514 端口上的 UDP 报文通过。

*   syslogd 被配置成接受从远程客户发来的消息。

*   syslogd 服务器和所有的客户端都必须有配有正确的正向和反向 DNS， 或者在 `/etc/hosts` 中有相应配置。

配置日志服务器， 客户端必须在 `/etc/syslog.conf` 中列出, 并指定日志的 facility：

```
+logclient.example.com
*.*     /var/log/logclient.log
```

### 注意:

更多关于各种被支持并可用的 *facility* 能在 [syslog.conf(5)](http://www.FreeBSD.org/cgi/man.cgi?query=syslog.conf&sektion=5&manpath=freebsd-release-ports) 手册页中找到。

一旦加入以后， 所有此类 `facility` 消息都会被记录到先前指定的文件 `/var/log/logclient.log`。

提供服务的机器还需要在其 `/etc/rc.conf` 中配置：

```
syslogd_enable="YES"
syslogd_flags="-a logclient.example.com -v -v"
```

第一个选项表示在系统启动时启用 `syslogd` 服务， 第二个选项表示允许服务器接收来自指定日志源客户端的数据。 第二行配置中最后的部分， 使用 `-v -v`， 表示增加日志消息的详细程度。 在调整 facility 配置的时候， 这个配置非常有用， 因为管理员能够看到哪些消息将作为哪个 facility 的内容来记录。

可以同时指定多个 `-a` 选项来允许多个客户机。 此外， 还可以指定 IP 地址或网段， 请参阅 [syslog(3)](http://www.FreeBSD.org/cgi/man.cgi?query=syslog&sektion=3&manpath=freebsd-release-ports) 联机手册以了解可用配置的完整列表。

最后， 日志文件应该被创建。 不论你用何种方法创建， 比如 [touch(1)](http://www.FreeBSD.org/cgi/man.cgi?query=touch&sektion=1&manpath=freebsd-release-ports) 能很好的完成此类任务：

```
# `touch /var/log/logclient.log`
```

此时， 应该重启并确认一下 `syslogd` 守护进程：

```
# `/etc/rc.d/syslogd restart`
# `pgrep syslog`
```

如果返回了一个 PIC 的话， 服务端应该被成功重启了, 并继续开始配置客户端。 如果服务端没有重启的话， 请在 `/var/log/messages` 日志中查阅相关输出。

### 30.11.2. 日志客户端配置

日志客户端是一台发送日志信息到日志服务器的机器， 并在本地保存拷贝。

与日志服务器类似， 客户端也需要满足一些最基本的条件：

*   [syslogd(8)](http://www.FreeBSD.org/cgi/man.cgi?query=syslogd&sektion=8&manpath=freebsd-release-ports) 必须被配置成发送指定类型的消息到能接收他们的日志服务器。

*   防火墙必须允许 514 端口上的 UDP 包通过；

*   必须配置正向与反向 DNS， 或者在 `/etc/hosts` 中有正确的记录。

相比服务器来说配置客户端更轻松一些。 客户端的机器在 `/etc/rc.conf` 中做如下的设置：

```
syslogd_enable="YES"
syslogd_flags="-s -v -v"
```

和前面类似， 这些选项会在系统启动过程中启用 `syslogd` 服务， 并增加日志消息的详细程度。 而 `-s` 选项则表示禁止服务接收来自其他主机的日志。

Facility 是描述某个消息由系统的哪部分生成的。 举例来说， ftp 和 ipfw 都是 facility。 当这两项服务生成日志消息时， 它们通常在日志消息中包含了这两种工具。 Facility 通常带有一个优先级或等级， 就是用来标记一个日志消息的重要程度。 最普通的为 `warning` 和 `info`。 请参阅 [syslog(3)](http://www.FreeBSD.org/cgi/man.cgi?query=syslog&sektion=3&manpath=freebsd-release-ports) 手册页以获得一个完整可用的 facility 与优先级列表。

日志服务器必须在客户端的 `/etc/syslog.conf` 中指明。 在此例中， `@` 符号被用来表示发送日志数据到远程的服务器， 看上去差不多如下这样：

```
*.*		@logserv.example.com
```

添加后， 必须重启 `syslogd` 使得上述修改生效：

```
# `/etc/rc.d/syslogd restart`
```

测试日志消息是否能通过网络发送， 在准备发出消息的客户机上用 [logger(1)](http://www.FreeBSD.org/cgi/man.cgi?query=logger&sektion=1&manpath=freebsd-release-ports) 来向 `syslogd` 发出信息：

```
# `logger "Test message from logclient"`
```

这段消息现在应该同时出现在客户机的 `/var/log/messages` 以及日志服务器的 `/var/log/logclient.log` 中。

### 30.11.3. 调试日志服务器

在某些情况下， 如果日志服务器没有收到消息的话就需要调试一番了。 有几个可能的原因， 最常见的两个是网络连接的问题和 DNS 的问题。 为了测试这些问题， 请确认两边的机器都能使用 `/etc/rc.conf` 中所设定的主机名访问到对方。 如果这个能正常工作的话， 那么就需要对 `/etc/rc.conf` 中的 `syslogd_flags` 选项做些修改了。

在以下的示例中， `/var/log/logclient.log` 是空的， `/var/log/message` 中也没有表明任何失败的原因。 为了增加调试的输出， 修改 `ayalogd_flags` 选项至类似于如下的示例， 并重启服务：

```
syslogd_flags="-d -a logclien.example.com -v -v"
```

```
# `/etc/rc.d/syslogd restart`
```

在重启服务之后， 屏幕上将立刻闪现类似这样的调试数据：

```
logmsg: pri 56, flags 4, from logserv.example.com, msg syslogd: restart
syslogd: restarted
logmsg: pri 6, flags 4, from logserv.example.com, msg syslogd: kernel boot file is /boot/kernel/kernel
Logging to FILE /var/log/messages
syslogd: kernel boot file is /boot/kernel/kernel
cvthname(192.168.1.10)
validate: dgram from IP 192.168.1.10, port 514, name logclient.example.com;
rejected in rule 0 due to name mismatch.
```

很明显，消息是由于主机名不匹配而被拒收的。 在一点一点的检查了配置文件之后， 发现了 `/etc/rc.conf` 中如下这行有输入错误：

```
syslogd_flags="-d -a logclien.example.com -v -v"
```

这行应该包涵有 `logclient`， 而不是 `logclien`。 在做了正确的修改并重启之后便能见到预期的效果了：

```
# `/etc/rc.d/syslogd restart`
logmsg: pri 56, flags 4, from logserv.example.com, msg syslogd: restart
syslogd: restarted
logmsg: pri 6, flags 4, from logserv.example.com, msg syslogd: kernel boot file is /boot/kernel/kernel
syslogd: kernel boot file is /boot/kernel/kernel
logmsg: pri 166, flags 17, from logserv.example.com,
msg Dec 10 20:55:02 <syslog.err> logserv.example.com syslogd: exiting on signal 2
cvthname(192.168.1.10)
validate: dgram from IP 192.168.1.10, port 514, name logclient.example.com;
accepted in rule 0.
logmsg: pri 15, flags 0, from logclient.example.com, msg Dec 11 02:01:28 trhodes: Test message 2
Logging to FILE /var/log/logclient.log
Logging to FILE /var/log/messages
```

此刻， 消息能够被正确接收并保存入文件了。

### 30.11.4. 安全性方面的思考

就像其他的网络服务一样， 在实现配置之前需要考虑安全性。 有时日志文件也包含了敏感信息， 比如本地主机上所启用的服务， 用户帐号和配置数据。 从客户端发出的数据经过网络到达服务器， 这期间既没有加密也没有密码保护。 如果有加密需要的话， 可以使用 [security/stunnel](http://www.freebsd.org/cgi/url.cgi?ports/security/stunnel/pkg-descr)， 它将在一个加密的隧道中传输数据。

本地安全也同样是个问题。 日志文件在使用中或循环转后都没有被加密。 本地用户可能读取这些文件以获得对系统更深入的了解。 对于这类情况， 给这些文件设置正确的权限是非常有必要的。 [newsyslog(8)](http://www.FreeBSD.org/cgi/man.cgi?query=newsyslog&sektion=8&manpath=freebsd-release-ports) 工具支持给新创建和循环的日志设置权限。 把日志文件的权限设置为 `600` 能阻止本地用户不必要的窥探。

## 第 31 章 防火墙

Contributed by Joseph J. Barbish.Converted to SGML and updated by Brad Davis.

## 31.1. 入门

防火墙的存在， 使得过滤出入系统的数据流成为可能。 防火墙可以使用一组或多组 “规则 (rules)”， 来检查出入您的网络连接的数据包， 并决定允许或阻止它们通过。 这些规则通常可以检查数据包的某个或某些特征， 这些特征包括， 但不必限于协议类型、 来源或目的主机地址， 以及来源或目的端口。

防火墙可以大幅度地改善主机或网络的安全。 它可以用来完成下面的任务：

*   保护和隔离应用程序、 服务程序， 以及您内部网络上的机器， 不受那些来自公共的 Internet 网络上您所不希望的数据流量的干扰。

*   限制或禁止从内部网访问公共的 Internet 上的服务。

*   支持网络地址转换 (NAT)， 它使得您的内部网络能够使用私有的 IP 地址， 并分享一条通往公共的 Internet 的连接 (使用一个 IP 地址， 或者一组公网地址)。

读完这章， 您将了解：

*   如何正确地定义包过滤规则。

*   FreeBSD 中内建的几种防火墙之间的差异。

*   如何使用和配置 OpenBSD 的 PF 防火墙。

*   如何使用和配置 IPFILTER。

*   如何使用和配置 IPFW。

阅读这章之前， 您需要：

*   理解基本的 FreeBSD 和 Internet 概念。

## 31.2. 防火墙的概念

建立防火墙规则集的基本方法有两种： “明示允许 (inclusive)”型 或 “明示禁止 (exclusive)”型。 明示禁止的防火墙规则， 默认允许所有数据通过防火墙， 而这种规则集中定义的， 则是不允许通过防火墙的流量， 换言之， 与这些规则不匹配的数据， 全部是允许通过防火墙的。 明示允许的防火墙正好相反， 它只允许符合规则集中定义规则的流量通过， 而其他所有的流量都被阻止。

明示允许型防火墙能够提供对于传出流量更好的控制， 这使其更适合那些直接对 Internet 公网提供服务的系统的需要。 它也能够控制来自 Internet 公网到您的私有网络的访问类型。 所有和规则不匹配的流量都会被阻止并记录在案。 一般来说明示允许防火墙要比明示禁止防火墙更安全， 因为它们显著地减少了允许不希望的流量通过可能造成的风险。

### 注意:

除非特别说明， 这一章的配置和示范的规则集都是创建明示允许防火墙的。

使用了 “带状态功能的防火墙 (stateful firewall)”， 可以进一步地收紧安全机制。 这种防火墙能够记录通过防火墙的连接， 进而只允许与现有连接匹配的连接， 或创建新的连接。 带状态功能的防火墙的缺点是， 在很短时间内有大量的连接请求时， 它们可能会受到拒绝服务 (DoS) 攻击。 绝大多数防火墙都提供了同时启用两种防火墙的能力， 以便为站点提供更好的保护。

## 31.3. 防火墙软件包

FreeBSD 的基本系统内建了三种不同的防火墙软件包。 它们是 *IPFILTER* (也被称作 IPF)、 *IPFIREWALL* (也被称作 IPFW)， 以及 *OpenBSD 的 PacketFilter* (也被称为 PF)。 FreeBSD 也提供了两个内建的、 用于流量整形 (基本上是控制带宽占用) 的软件包： [altq(4)](http://www.FreeBSD.org/cgi/man.cgi?query=altq&sektion=4&manpath=freebsd-release-ports) 和 [dummynet(4)](http://www.FreeBSD.org/cgi/man.cgi?query=dummynet&sektion=4&manpath=freebsd-release-ports)。 Dummynet 在过去一直和 IPFW 紧密集成， 而 ALTQ 则需要配合 PF 使用。 IPFILTER 的流量整形功能可以使用 IPFILTER 的 NAT 和过滤功能以及 IPFW 的 [dummynet(4)](http://www.FreeBSD.org/cgi/man.cgi?query=dummynet&sektion=4&manpath=freebsd-release-ports) 配合, *或者* 使用 PF 跟 ALTQ 的组合。 IPFW， 以及 PF 都是用规则来控制是否允许数据包出入您的系统， 虽然它们采取了不同的实现方法和规则语法。

FreeBSD 包含多个内建的防火墙软件包的原因在于， 不同的人会有不同的需求和偏好。 任何一个防火墙软件包都很难说是最好的。

作者倾向于使用 IPFILTER， 因为它提供的状态式规则， 在 NAT 的环境中要简单许多， 而且它内建了 ftp 代理， 这简化了使用外部 FTP 服务时所需的配置。

由于所有的防火墙都基于检查所选定的包控制字段来实现功能， 撰写防火墙规则集时， 就必须了解 TCP/IP 是如何工作的， 以及包的控制字段在正常会话交互中的作用。 您可以在这个网站找到一份很好的解释文档： `[`www.ipprimer.com/overview.cfm`](http://www.ipprimer.com/overview.cfm)`.

## 31.4. OpenBSD Packet Filter (PF) 和 ALTQ

Revised and updated by John Ferrell.

2003 年 7 月， OpenBSD 的防火墙， 也就是常说的 PF 被成功地移植到了 FreeBSD 上， 并可以通过 FreeBSD Ports Collection 来安装了； 第一个将 PF 集成到基本系统中的版本是 2004 年 11 月发行的 FreeBSD 5.3。 PF 是一个完整的提供了大量功能的防火墙软件， 并提供了可选的 ALTQ (交错队列， Alternate Queuing) 功能。 ALTQ 提供了服务品质 (QoS) 带宽整形功能。

OpenBSD 项目非常杰出的维护着一份 [PF FAQ](http://www.openbsd.org/faq/pf/)。 就其本身而言，这一节注重于 FreeBSD 的 PF 和提供一些关于使用方面的一般常识。更详细的使用信息请参阅 [PF FAQ](http://www.openbsd.org/faq/pf/)。

更多的详细信息， 可以在 FreeBSD 版本的 PF 网站上找到： `[`pf4freebsd.love2party.net/`](http://pf4freebsd.love2party.net/)`。

### 31.4.1. 使用 PF 可加载的内核模块

要加载 PF 内核模块， 可以在 `/etc/rc.conf` 中加入下面的设置：

```
pf_enable="YES"
```

然后使用启动脚本来加载模块：

```
# `/etc/rc.d/pf start`
```

需要说明的是， 如果系统中没有规则集配置文件， 则上述操作不会加载 PF 模块。 配置文件的默认位置是 `/etc/pf.conf`。 如果 PF 规则集在其他位置， 可以用下面的 `/etc/rc.conf` 配置来告诉 PF：

```
pf_rules="*`/path/to/pf.conf`*"
```

`pf.conf` 的例子可以在 `/usr/share/examples/pf/` 找到。

PF 模块也可以手工从命令行加载：

```
# `kldload pf.ko`
```

PF 的日志记录功能是由 `pflog.ko` 提供的， 通过在 `/etc/rc.conf` 中加入下面的设置：

```
pflog_enable="YES"
```

然后使用启动脚本来加载模块：

```
# `/etc/rc.d/pflog start`
```

如果您需要其他 PF 特性， 则需要将 PF 支持联编进内核。

### 31.4.2. PF 内核选项

虽然你不必亲自把对 PF 的支持编译进 FreeBSD 内核，但是有时你仍然需要这么做来使用到 PF 的某些没有被收录进可加载模块的高级特性，比如 [pfsync(4)](http://www.FreeBSD.org/cgi/man.cgi?query=pfsync&sektion=4&manpath=freebsd-release-ports) 伪设备用来发送某些改变到 PF 状态表。 它能配合 [carp(4)](http://www.FreeBSD.org/cgi/man.cgi?query=carp&sektion=4&manpath=freebsd-release-ports) 使用 PF 建立支持故障转移的防火墙。 更多有关 CARP 的详细信息可以参阅本手册的 第 32.14 节 “Common Address Redundancy Protocol (CARP， 共用地址冗余协议)”")。

The PF kernel options can be found in `/usr/src/sys/conf/NOTES` and are reproduced below:

有关 PF 的内核选项可以在 `/usr/src/sys/conf/NOTES` 中找到， 以下也略有阐述：

```
device pf
device pflog
device pfsync
```

`device pf` 选项用于启用 “Packet Filter” 防火墙的支持 （[pf(4)](http://www.FreeBSD.org/cgi/man.cgi?query=pf&sektion=4&manpath=freebsd-release-ports)）。

`device pflog` 启用可选的 [pflog(4)](http://www.FreeBSD.org/cgi/man.cgi?query=pflog&sektion=4&manpath=freebsd-release-ports) 伪网络设备， 用以通过 [bpf(4)](http://www.FreeBSD.org/cgi/man.cgi?query=bpf&sektion=4&manpath=freebsd-release-ports) 描述符来记录流量。 [pflogd(8)](http://www.FreeBSD.org/cgi/man.cgi?query=pflogd&sektion=8&manpath=freebsd-release-ports) 服务可以用来存储信息， 并把它们以日志形式记录到磁盘上。

`device pfsync` 选项启用可选的 [pfsync(4)](http://www.FreeBSD.org/cgi/man.cgi?query=pfsync&sektion=4&manpath=freebsd-release-ports) 支持，这是用于监视 “状态变更” 的伪网络设备。

### 31.4.3. 可用的 `rc.conf` 选项

The following [rc.conf(5)](http://www.FreeBSD.org/cgi/man.cgi?query=rc.conf&sektion=5&manpath=freebsd-release-ports) statements configure PF and [pflog(4)](http://www.FreeBSD.org/cgi/man.cgi?query=pflog&sektion=4&manpath=freebsd-release-ports) at boot:

以下 [rc.conf(5)](http://www.FreeBSD.org/cgi/man.cgi?query=rc.conf&sektion=5&manpath=freebsd-release-ports) 中的语句用于启动时配置 PF 和 [pflog(4)](http://www.FreeBSD.org/cgi/man.cgi?query=pflog&sektion=4&manpath=freebsd-release-ports)

```
pf_enable="YES"                 # 启用 PF (如果需要的话， 自动加载内核模块)
pf_rules="/etc/pf.conf"         # pf 使用的规则定义文件
pf_flags=""                     # 启动时传递给 pfctl 的其他选项
pflog_enable="YES"              # 启动 pflogd(8)
pflog_logfile="/var/log/pflog"  # pflogd 用于记录日志的文件名
pflog_flags=""                  # 启动时传递给 pflogd 的其他选项
```

如果您的防火墙后面有一个 LAN， 而且需要通过它来转发 LAN 上的包， 或进行 NAT， 还需要同时启用下述选项：

```
gateway_enable="YES"            # 启用为 LAN 网关
```

### 31.4.4. 建立过滤规则

PF 会从 [pf.conf(5)](http://www.FreeBSD.org/cgi/man.cgi?query=pf.conf&sektion=5&manpath=freebsd-release-ports) (默认为 `/etc/pf.conf`) 文件中读取配置规则， 并根据那里的规则修改、丢弃或让数据包通过。 默认安装的 FreeBSD 已经提供了一些简单的例子放在 `/usr/share/examples/pf/` 目录下。 请参阅 [PF FAQ](http://www.openbsd.org/faq/pf/) 获取完整的 PF 规则信息。

### 警告:

在浏览 [PF FAQ](http://www.openbsd.org/faq/pf/) 时， 请时刻注意不同版本的 FreeBSD 可能会使用不同版本的 PF。 目前， FreeBSD 8.*`X`* 和之前的系统使用的是与 OpenBSD 4.1 相同版本的 PF。 FreeBSD 9.*`X`* 和之后的系统使用的是与 OpenBSD 4.5 相同版本的 PF。

[FreeBSD packet filter 邮件列表](http://lists.FreeBSD.org/mailman/listinfo/freebsd-pf) 是一个提有关配置使用 PF 防火墙问题的好地方。请在提问之前查阅邮件列表的归档！

### 31.4.5. 使用 PF

使用 [pfctl(8)](http://www.FreeBSD.org/cgi/man.cgi?query=pfctl&sektion=8&manpath=freebsd-release-ports) 可以控制 PF。 以下是一些实用的命令 （请查阅 [pfctl(8)](http://www.FreeBSD.org/cgi/man.cgi?query=pfctl&sektion=8&manpath=freebsd-release-ports) 获得全部可用的选项）:

| 命令 | 作用 |
| --- | --- |
| `pfctl -e` | 启用 PF |
| `pfctl -d` | 禁用 PF |
| `pfctl -F all -f /etc/pf.conf` | 清除所有规则 (nat, filter, state, table, 等等。) 并读取 `/etc/pf.conf` |
| `pfctl -s [ rules &#124; nat &#124; state ]` | 列出 filter 规则, nat 规则, 或状态表 |
| `pfctl -vnf /etc/pf.conf` | 检查 `/etc/pf.conf` 中的错误，但不加载相关的规则 |

### 31.4.6. 启用 ALTQ

ALTQ 只有在作为编译选项加入到 FreeBSD 内核时才能使用。ALTQ 目前还不是所有的可用网卡驱动都能够支持的。 请参见 [altq(4)](http://www.FreeBSD.org/cgi/man.cgi?query=altq&sektion=4&manpath=freebsd-release-ports) 联机手册了解您正使用的 FreeBSD 版本中的驱动支持情况。

下面这些选项将启用 ALTQ 以及一些附加的功能：

```
options         ALTQ
options         ALTQ_CBQ        # 基于分类的排列 (CBQ)
options         ALTQ_RED        # 随机先期检测 (RED)
options         ALTQ_RIO        # 对进入和发出的包进行 RED
options         ALTQ_HFSC       # 带等级的包调度器 (HFSC)
options         ALTQ_PRIQ       # 按优先级的排列 (PRIQ)
options         ALTQ_NOPCC      # 在联编 SMP 内核时必须使用，禁止读时钟
```

`options ALTQ` 将启用 ALTQ 框架的支持。

`options ALTQ_CBQ` 用于启用 *基于分类的队列* (CBQ) 支持。 CBQ 允许您将连接分成不同的类别， 或者说， 队列， 以便在规则中为它们指定不同的优先级。

`options ALTQ_RED` 将启用 *随机预检测* (RED)。 RED 是一种用于防止网络拥塞的技术。 RED 度量队列的长度， 并将其与队列的最大和最小长度阈值进行比较。 如果队列过长， 则新的包将被丢弃。 如名所示， RED 从不同的连接中随机地丢弃数据包。

`options ALTQ_RIO` 将启用 *出入的随机预检测*。

`options ALTQ_HFSC` 启用 *层次式公平服务平滑包调度器*。 要了解关于 HFSC 进一步的信息， 请参见 `[`www-2.cs.cmu.edu/~hzhang/HFSC/main.html`](http://www-2.cs.cmu.edu/~hzhang/HFSC/main.html)`。

`options ALTQ_PRIQ` 启用 *优先队列* (PRIQ)。 PRIQ 首先允许高优先级队列中的包通过。

`options ALTQ_NOPCC` 启用 ALTQ 的 SMP 支持。 如果是 SMP 系统， 则必须使用它。

## 31.5. IPFILTER (IPF) 防火墙

IPFILTER 的作者是 Darren Reed。 IPFILTER 是独立于操作系统的： 它是一个开放源代码的应用， 并且已经被移植到了 FreeBSD、 NetBSD、 OpenBSD、 SunOS、 HP/UX， 以及 Solaris 操作系统上。 IPFILTER 的支持和维护都相当活跃， 并且有规律地发布更新版本。

IPFILTER 提供了内核模式的防火墙和 NAT 机制， 这些机制可以通过用户模式运行的接口程序进行监视和控制。 防火墙规则可以使用 [ipf(8)](http://www.FreeBSD.org/cgi/man.cgi?query=ipf&sektion=8&manpath=freebsd-release-ports) 工具来动态地设置和删除。 NAT 规则可以通过 [ipnat(1)](http://www.FreeBSD.org/cgi/man.cgi?query=ipnat&sektion=1&manpath=freebsd-release-ports) 工具来维护。 [ipfstat(8)](http://www.FreeBSD.org/cgi/man.cgi?query=ipfstat&sektion=8&manpath=freebsd-release-ports) 工具则可以用来显示 IPFILTER 内核部分的统计数据。 最后， 使用 [ipmon(8)](http://www.FreeBSD.org/cgi/man.cgi?query=ipmon&sektion=8&manpath=freebsd-release-ports) 程序可以把 IPFILTER 的动作记录到系统日志文件中。

IPF 最初是使用一组 “以最后匹配的规则为准” 的策略来实现的， 这种方式只能支持无状态的规则。 随着时代的进步， IPF 被逐渐增强， 并加入了 “quick” 选项， 以及支持状态的 “keep state” 选项， 这使得规则处理逻辑变得更富有现代气息。 IPF 的官方文档只介绍了传统的规则编写方法和文件处理逻辑。 新增的功能只是作为一些附加的选项出现， 如果能完全理解这些功能， 则对于建立更安全的防火墙就很有好处。

这一节中主要是针对 “quick” 选项， 以及支持状态的 “keep state” 选项的介绍。 这是明示允许防火墙规则集最基本的编写要素。

要获得关于传统规则处理方式的详细信息， 请参考： `[`www.obfuscation.org/ipf/ipf-howto.html#TOC_1`](http://www.obfuscation.org/ipf/ipf-howto.html#TOC_1)` 以及 `[`coombs.anu.edu.au/~avalon/ip-filter.html`](http://coombs.anu.edu.au/~avalon/ip-filter.html)`。

IPF FAQ 可以在 `[`www.phildev.net/ipf/index.html`](http://www.phildev.net/ipf/index.html)` 找到。

除此之外， 您还可以在 `[`marc.theaimsgroup.com/?l=ipfilter`](http://marc.theaimsgroup.com/?l=ipfilter)` 找到开放源代码的 IPFilter 的邮件列表存档， 并进行搜索。

### 31.5.1. 启用 IPF

IPF 作为 FreeBSD 基本安装的一部分， 以一个独立的内核模块的形式提供。 如果在 `rc.conf` 中配置了 `ipfilter_enable="YES"`， 系统就会自动地动态加载 IPF 内核模块。 这个内核模块在创建时启用了日志支持， 并加入了 `default pass all` 选项。 如果只是需要把默认的规则设置为 `block all` 的话， 就不需要把 IPF 编译到内核中。 简单地通过把 `block all` 这条规则加入自己的规则集来达到同样的目的。

### 31.5.2. 内核选项

下面这些 FreeBSD 内核编译选项并不是启用 IPF 所必需的。 这里只是作为背景知识来加以阐述。 如果将 IPF 编入了内核， 则对应的内核模块将不被使用。

关于 IPF 选项语句的内核编译配置的例子， 可以在内核源代码中的 `/usr/src/sys/conf/NOTES` 找到。 此处列举如下：

```
options IPFILTER
options IPFILTER_LOG
options IPFILTER_DEFAULT_BLOCK
```

`options IPFILTER` 用于启用 “IPFILTER” 防火墙的支持。

`options IPFILTER_LOG` 用于启用 IPF 的日志支持， 所有匹配了包含 `log` 的规则的包， 都会被记录到 `ipl` 这个包记录伪──设备中。

`options IPFILTER_DEFAULT_BLOCK` 将改变防火墙的默认动作， 进而， 所有不匹配防火墙的 `pass` 规则的包都会被阻止。

这些选项只有在您重新编译并安装了上述配置的内核之后才会生效。

### 31.5.3. 可用的 `rc.conf` 选项

要在启动时激活 IPF， 需要在 `/etc/rc.conf` 中增加下面的设置：

```
ipfilter_enable="YES"             # 启动 ipf 防火墙
ipfilter_rules="/etc/ipf.rules"   # 将被加载的规则定义， 这是一个文本文件
ipmon_enable="YES"                # 启动 IP 监视日志
ipmon_flags="-Ds"                 # D = 作为服务程序启动
                                  # s = 使用 syslog 记录
                                  # v = 记录 tcp 窗口大小、 ack 和顺序号(seq)
                                  # n = 将 IP 和端口映射为名字
```

如果在防火墙后面有使用了保留的私有 IP 地址范围的 LAN， 还需要增加下面的一些选项来启用 NAT 功能：

```
gateway_enable="YES"              # 启用作为 LAN 网关的功能
ipnat_enable="YES"                # 启动 ipnat 功能
ipnat_rules="/etc/ipnat.rules"    # 用于 ipnat 的规则定义文件
```

### 31.5.4. IPF

[ipf(8)](http://www.FreeBSD.org/cgi/man.cgi?query=ipf&sektion=8&manpath=freebsd-release-ports) 命令可以用来加载您自己的规则文件。 一般情况下， 您可以建立一个包括您自定义的规则的文件， 并使用这个命令来替换掉正在运行的防火墙中的内部规则：

```
# `ipf -Fa -f /etc/ipf.rules`
```

`-Fa` 表示清除所有的内部规则表。

`-f` 用于指定将要被读取的规则定义文件。

这个功能使得您能够修改自定义的规则文件， 通过运行上面的 IPF 命令， 可以将正在运行的防火墙刷新为使用全新的规则集， 而不需要重新启动系统。 这对于测试新的规则来说就很方便， 因为您可以任意执行上面的命令。

请参考 [ipf(8)](http://www.FreeBSD.org/cgi/man.cgi?query=ipf&sektion=8&manpath=freebsd-release-ports) 联机手册以了解这个命令提供的其它选项。

[ipf(8)](http://www.FreeBSD.org/cgi/man.cgi?query=ipf&sektion=8&manpath=freebsd-release-ports) 命令假定规则文件是一个标准的文本文件。 它不能处理使用符号代换的脚本。

也确实有办法利用脚本的非常强大的符号替换能力来构建 IPF 规则。 要了解进一步的细节， 请参考 第 31.5.9 节 “构建采用符号替换的规则脚本”。

### 31.5.5. IPFSTAT

默认情况下， [ipfstat(8)](http://www.FreeBSD.org/cgi/man.cgi?query=ipfstat&sektion=8&manpath=freebsd-release-ports) 会获取并显示所有的累积统计， 这些统计是防火墙启动以来用户定义的规则匹配的出入流量， 您可以通过使用 `ipf -Z` 命令来将这些计数器清零。

请参见 [ipfstat(8)](http://www.FreeBSD.org/cgi/man.cgi?query=ipfstat&sektion=8&manpath=freebsd-release-ports) 联机手册以了解进一步的细节。

默认的 [ipfstat(8)](http://www.FreeBSD.org/cgi/man.cgi?query=ipfstat&sektion=8&manpath=freebsd-release-ports) 命令输出类似于下面的样子：

```
input packets: blocked 99286 passed 1255609 nomatch 14686 counted 0
 output packets: blocked 4200 passed 1284345 nomatch 14687 counted 0
 input packets logged: blocked 99286 passed 0
 output packets logged: blocked 0 passed 0
 packets logged: input 0 output 0
 log failures: input 3898 output 0
 fragment state(in): kept 0 lost 0
 fragment state(out): kept 0 lost 0
 packet state(in): kept 169364 lost 0
 packet state(out): kept 431395 lost 0
 ICMP replies: 0 TCP RSTs sent: 0
 Result cache hits(in): 1215208 (out): 1098963
 IN Pullups succeeded: 2 failed: 0
 OUT Pullups succeeded: 0 failed: 0
 Fastroute successes: 0 failures: 0
 TCP cksum fails(in): 0 (out): 0
 Packet log flags set: (0)
```

如果使用了 `-i` (进入流量) 或者 `-o` (输出流量)， 这个命令就只获取并显示内核中所安装的对应过滤器规则的统计数据。

`ipfstat -in` 以规则号的形式显示进入的内部规则表。

`ipfstat -on` 以规则号的形式显示流出的内部规则表。

输出和下面的类似：

```
@1 pass out on xl0 from any to any
@2 block out on dc0 from any to any
@3 pass out quick on dc0 proto tcp/udp from any to any keep state
```

`ipfstat -ih` 显示内部规则表中的进入流量， 每一个匹配规则前面会同时显示匹配的次数。

`ipfstat -oh` 显示内部规则表中的流出流量， 每一个匹配规则前面会同时显示匹配的次数。

输出和下面的类似：

```
2451423 pass out on xl0 from any to any
354727 block out on dc0 from any to any
430918 pass out quick on dc0 proto tcp/udp from any to any keep state
```

`ipfstat` 命令的一个重要的功能可以通过指定 `-t` 参数来使用， 它会以类似 [top(1)](http://www.FreeBSD.org/cgi/man.cgi?query=top&sektion=1&manpath=freebsd-release-ports) 的显示 FreeBSD 正运行的进程表的方式来显示统计数据。 当您的防火墙正在受到攻击的时候， 这个功能让您得以识别、 试验， 并查看攻击的数据包。 这个选项提还提供了实时选择希望监视的目的或源 IP、 端口或协议的能力。 请参见 [ipfstat(8)](http://www.FreeBSD.org/cgi/man.cgi?query=ipfstat&sektion=8&manpath=freebsd-release-ports) 联机手册以了解详细信息。

### 31.5.6. IPMON

为了使 `ipmon` 能够正确工作， 必须打开 `IPFILTER_LOG` 这个内核选项。 这个命令提供了两种不同的使用模式。 内建模式是默认的模式， 如果您不指定 `-D` 参数， 就会采用这种模式。

服务模式是持续地通过系统日志来记录的工作模式， 这样， 您就可以通过查看日志来了解过去曾经发生过的事情。 这种模式是 FreeBSD 和 IPFILTER 配合工作的模式。 由于在 FreeBSD 中提供了一个内建的系统日志自动轮转功能， 因此， 使用 [syslogd(8)](http://www.FreeBSD.org/cgi/man.cgi?query=syslogd&sektion=8&manpath=freebsd-release-ports) 比默认的将日志信息记录到一个普通文件要好。 在默认的 `rc.conf` 文件中， `ipmon_flags` 语句会指定 `-Ds` 标志：

```
ipmon_flags="-Ds"                 # D = 作为服务程序启动
                  # s = 使用 syslog 记录
                  # v = 记录 tcp 窗口大小、 ack 和顺序号(seq)
                  # n = 将 IP 和端口映射为名字
```

记录日志的好处是很明显的。 它提供了在事后重新审查相关信息， 例如哪些包被丢弃， 以及这些包的来源地址等等。 这将为查找攻击者提供非常有用的第一手资料。

即使启用了日志机制， IPF 仍然不会对其规则进行任何日志记录工作。 防火墙管理员可以决定规则集中的哪些应记录日志， 并在这些规则上加入 log 关键字。 一般来说， 只应记录拒绝性的规则。

作为惯例， 通常会有一条默认的、拒绝所有网络流量的规则， 并指定 log 关键字， 作为您的规则集的最后一条。 这样就能够看到所有没有匹配任何规则的数据包了。

### 31.5.7. IPMON 的日志

Syslogd 使用特殊的方法对日志数据进行分类。 它使用称为 “facility” 和 “level” 的组。 以 `-Ds` 模式运行的 IPMON 采用 `local0` 作为默认的 “facility” 名。 如果需要， 可以用下列 levels 来进一步区分数据：

```
LOG_INFO - 使用 "log" 关键字指定的通过或阻止动作
LOG_NOTICE - 同时记录通过的那些数据包
LOG_WARNING - 同时记录阻止的数据包
LOG_ERR - 进一步记录含不完整的包头的数据包
```

要设置 IPFILTER 来将所有的数据记录到 `/var/log/ipfilter.log`， 需要首先建立这个文件。 下面的命令可以完成这个工作：

```
# `touch /var/log/ipfilter.log`
```

[syslogd(8)](http://www.FreeBSD.org/cgi/man.cgi?query=syslogd&sektion=8&manpath=freebsd-release-ports) 功能可以通过在 `/etc/syslog.conf` 文件中的语句来定义。 `syslog.conf` 提供了相当多的用以控制 syslog 如何处理类似 IPF 这样的用用程序所产生的系统消息的方法。

您需要将下列语句加到 `/etc/syslog.conf`：

```
local0.* /var/log/ipfilter.log
```

这里的 `local0.*` 表示把所有的相关日志信息写到指定的文件中。

要让 `/etc/syslog.conf` 中的修改立即生效， 可以重新启动计算机， 或者通过执行 `/etc/rc.d/syslogd reload` 来让它重新读取 `/etc/syslog.conf`。

不要忘了修改 `/etc/newsyslog.conf` 来让刚创建的日志进行轮转。

### 31.5.8. 记录消息的格式

由 `ipmon` 生成的消息由空格分隔的数据字段组成。 所有的消息都包含的字段是：

1.  接到数据包的日期。

2.  接到数据包的时间。 其格式为 HH:MM:SS.F， 分别是小时、 分钟、 秒， 以及分秒 (这个数字可能有许多位)。

3.  处理数据包的网络接口名字， 例如 `dc0`。

4.  组和规则的编号， 例如 `@0:17`。

可以通过 `ipfstat -in` 来查看这些信息。

1.  动作： p 表示通过， b 表示阻止， S 表示包头不全， n 表示没有匹配任何规则， L 表示 log 规则。 显示这些标志的顺序是： S, p, b, n, L。 大写的 P 或 B 表示记录包的原因是某个全局的日志配置， 而不是某个特定的规则。

2.  地址。 这实际上包括三部分： 源地址和端口 (以逗号分开)， 一个 -> 符号， 以及目的地址和端口， 例如： `209.53.17.22,80 -> 198.73.220.17,1722`。

3.  `PR`， 后跟协议名称或编号， 例如： `PR tcp`。

4.  `len`， 后跟包头的长度， 以及包的总长度， 例如： `len 20 40`。

对于 TCP 包， 则还会包括一个附加的字段， 由一个连字号开始， 之后是表示所设置的标志的一个字母。 请参见 [ipf(5)](http://www.FreeBSD.org/cgi/man.cgi?query=ipf&sektion=5&manpath=freebsd-release-ports) 联机手册， 以了解这些字母所对应的标志。

对于 ICMP 包， 则在最后会有两个字段。 前一个总是 “ICMP”， 而后一个则是 ICMP 消息和子消息的类型， 中间以斜线分靠， 例如 ICMP 3/3 表示端口不可达消息。

### 31.5.9. 构建采用符号替换的规则脚本

一些有经验的 IPF 会创建包含规则的文件， 并把它编写成能够与符号替换脚本兼容的方式。 这样做最大的好处是能够在修改时只修改符号名字所代表的值， 而在脚本执行时直接替换掉所有的名符。 作为脚本， 可以使用符号替换来把那些经常使用的值直接用于多个规则。 下面将给出一个例子。

这个脚本所使用的语法与 [sh(1)](http://www.FreeBSD.org/cgi/man.cgi?query=sh&sektion=1&manpath=freebsd-release-ports)、 [csh(1)](http://www.FreeBSD.org/cgi/man.cgi?query=csh&sektion=1&manpath=freebsd-release-ports)， 以及 [tcsh(1)](http://www.FreeBSD.org/cgi/man.cgi?query=tcsh&sektion=1&manpath=freebsd-release-ports) 脚本。

符号替换的前缀字段是美元符号： `$`。

符号字段不使用 $ 前缀。

希望替换符号字段的值， 必须使用双引号 (`"`) 括起来。

您的规则文件的开头类似这样：

```
############# IPF 规则脚本的开头 ########################
oif="dc0"            # 外网接口的名字
odns="192.0.2.11"    # ISP 的 DNS 服务器 IP 地址
myip="192.0.2.7"     # 来自 ISP 的静态 IP 地址
ks="keep state"
fks="flags S keep state"

# 可以使用这个脚本来建立 /etc/ipf.rules 文件，
# 也可以 "直接地" 运行它。
#
# 请删除两个注释号之一。
#
# 1) 保留下面一行， 则创建 /etc/ipf.rules：
#cat > /etc/ipf.rules << EOF
#
# 2) 保留下面一行， 则 "直接地" 运行脚本：
/sbin/ipf -Fa -f - << EOF

# 允许发出到我的 ISP 的域名服务器的访问
pass out quick on $oif proto tcp from any to $odns port = 53 $fks
pass out quick on $oif proto udp from any to $odns port = 53 $ks

# 允许发出未加密的 www 访问请求
pass out quick on $oif proto tcp from $myip to any port = 80 $fks

# 允许发出使用 TLS SSL 加密的 https www 访问请求
pass out quick on $oif proto tcp from $myip to any port = 443 $fks
EOF
################## IPF 规则脚本的结束 ########################
```

这就是所需的全部内容。 这个规则本身并不重要， 它们主要是用于体现如何使用符号代换字段， 以及如何完成值的替换。 如果上面的例子的名字是 `/etc/ipf.rules.script`， 就可以通过输入下面的命令来重新加载规则：

```
# `sh /etc/ipf.rules.script`
```

在规则文件中嵌入符号有一个问题： IPF 无法识别符号替换， 因此它不能直接地读取这样的脚本。

这个脚本可以使用下面两种方法之一来使用：

*   去掉 `cat` 之前的注释， 并注释掉 `/sbin/ipf` 开头的那一行。 像其他配置一样， 将 `ipfilter_enable="YES"` 放到 `/etc/rc.conf` 文件中， 并在此后立刻执行脚本， 以创建或更新 `/etc/ipf.rules`。

*   通过把 `ipfilter_enable="NO"` (这是默认值) 加到 `/etc/rc.conf` 中， 来禁止系统启动脚本开启 IPFILTER。

    在 `/usr/local/etc/rc.d/` 启动目录中增加一个类似下面的脚本。 应该给它起一个显而易见的名字， 例如 `ipf.loadrules.sh`。 请注意， `.sh` 扩展名是必需的。

    ```
    #!/bin/sh
    sh /etc/ipf.rules.script
    ```

    脚本文件必须设置为属于 `root`， 并且属主可读、 可写、 可执行。

    ```
    # `chmod 700 /usr/local/etc/rc.d/ipf.loadrules.sh`
    ```

这样， 在系统启动时， 就会自动加载您的 IPF 规则了。

### 31.5.10. IPF 规则集

规则集是指一组编写好的依据包的值决策允许通过或阻止 IPF 规则。 包的双向交换组成了一个会话交互。 防火墙规则集会作用于来自于 Internet 公网的包以及由系统发出来回应这些包的数据包。 每一个 TCP/IP 服务 (例如 telnet, www, 邮件等等) 都由协议预先定义了其特权 (监听) 端口。 发到特定服务的包会从源地址使用非特权 (高编号) 端口发出， 并发到特定服务在目的地址的对应端口。 所有这些参数 (例如： 端口和地址） 都是可以为防火墙规则所利用的， 判别是否允许服务通过的标准。

IPF 最初被写成使用一组称作 “以最后匹配的规则为准” 的处理逻辑， 且只能处理无状态的规则。 随着时代的发展， IPF 进行了改进， 并提供了 “quick” 选项， 以及一个有状态的 “keep state” 选项。 后者使处理逻辑迅速地跟上了时代的步伐。

这一节中提供的一些指导， 是基于使用包含 “quick” 选项和有状态的 “keep state” 选项来进行阐述的。 这些是编写明示允许防火墙规则集的基本要素。

### 警告:

当对防火墙规则进行操作时， 应 *谨慎行事*。 某些配置可能会 *将您反锁在* 服务器外面。 保险起见， 您可以考虑在第一次进行防火墙配置时在本地控制台上， 而不是远程， 如通过 ssh 来进行。

### 31.5.11. 规则语法

这里给出的规则语法已经简化到只处理那些新式的带状态规则， 并且都是 “第一个匹配的规则获胜” 逻辑的。 要了解完整的传统规则语法描述， 请参见 [ipf(8)](http://www.FreeBSD.org/cgi/man.cgi?query=ipf&sektion=8&manpath=freebsd-release-ports) 联机手册。

以 `#` 字符开头的内容会被认为是注释。 这些注释可以出现在一行规则的末尾， 或者独占一行。 空行会被忽略。

规则由关键字组成。 这些关键字必须以一定的顺序， 从左到右出现在一行上。 接下来的文字中关键字将使用粗体表示。 某些关键字可能提供了子选项， 这些子选项本身可能也是关键字， 而且可能会提供更多的子选项。 下面的文字中， 每种语法都使用粗体的小节标题呈现， 并介绍了其上下文。

*`ACTION IN-OUT OPTIONS SELECTION STATEFUL PROTO SRC_ADDR,DST_ADDR OBJECT PORT_NUM TCP_FLAG STATEFUL`*

*`ACTION`* = block | pass

*`IN-OUT`* = in | out

*`OPTIONS`* = log | quick | on 网络接口的名字

*`SELECTION`* = proto 协议名称 | 源/目的 IP | port = 端口号 | flags 标志值

*`PROTO`* = tcp/udp | udp | tcp | icmp

*`SRC_ADD,DST_ADDR`* = all | from 对象 to 对象

*`OBJECT`* = IP 地址 | any

*`PORT_NUM`* = port 端口号

*`TCP_FLAG`* = S

*`STATEFUL`* = keep state

#### 31.5.11.1. ACTION (动作)

动作对表示匹配规则的包应采取什么动作。 每一个规则 *必须* 包含一个动作。 可以使用下面两种动作之一：

`block` 表示如果规则与包匹配， 则丢弃包。

`pass` 表示如果规则与包匹配， 则允许包通过防火墙。

#### 31.5.11.2. IN-OUT

每个过滤器规则都必须明确地指定是流入还是流出的规则。 下一个关键字必须要么是 `in`， 要么是 `out`， 否则将无法通过语法检查。

`in` 表示规则应被应用于刚刚从 Internet 公网上收到的数据包。

`out` 表示规则应被应用于即将发出到 Internet 的数据包。

#### 31.5.11.3. OPTIONS

### 注意:

这些选项必须按下面指定的顺序出现。

`log` 表示包头应被写入到 `ipl` 日志 (如前面 LOGGING 小节所介绍的那样)， 如果它与规则匹配的话。

`quick` 表示如果给出的参数与包匹配， 则以这个规则为准， 这使得能够 "短路" 掉后面的规则。 这个选项对于使用新式的处理逻辑是必需的。

`on` 表示将网络接口的名称作为筛选参数的一部分。 接口的名字会在 [ifconfig(8)](http://www.FreeBSD.org/cgi/man.cgi?query=ifconfig&sektion=8&manpath=freebsd-release-ports) 的输出中显示。 使用这个选项， 则规则只会应用到某一个网络接口上的出入数据包上。 要配置新式的处理逻辑， 必须使用这个选项。

当记录包时， 包的头会被写入到 IPL 包日志伪设备中。 紧跟 `log` 关键字， 可以使用下面几个修饰符 (按照下列顺序)：

`body` 表示应同时记录包的前 128 字节的内容。

`first` 如果 `log` 关键字和 `keep state` 选项同时使用， 则这个选项只在第一个包上触发， 这样就不用记录每一个 “keep state” 包信息了。

#### 31.5.11.4. SELECTION

这一节所介绍的关键字可以用于所检察的包的属性。 有一个关键字主题， 以及一组子选项关键字， 您必须从他们中选择一个。 以下是一些通用的属性， 它们必须按下面的顺序使用：

#### 31.5.11.5. PROTO

`proto` 是一个主题关键字， 它必须与某个相关的子选项关键字配合使用。 这个值的作用是匹配某个特定的协议。 要使用新式的规则处理逻辑， 就必须使用这个选项。

`tcp/udp | udp | tcp | icmp` 或其他在 `/etc/protocols` 中定义的协议。 特殊的协议关键字 `tcp/udp` 可以用于匹配 TCP 或 UDP 包， 引入这个关键字的作用是是避免大量的重复规则的麻烦。

#### 31.5.11.6. SRC_ADDR/DST_ADDR

使用 `all` 关键词， 基本上相当于 “from any to any” 在没有配合其他关键字的情形。

`from src to dst`： `from` 和 `to` 关键字主要是用来匹配 IP 地址。 所有的规则都必须 *同时* 给出源和目的两个参数。 `any` 是一个可以用于匹配任意 IP 地址的特殊关键字。 例如， 您可以使用 `from any to any` 或 `from 0.0.0.0/0 to any` 或 `from any to 0.0.0.0/0` 或 `from 0.0.0.0 to any` 以及 `from any to 0.0.0.0`。

如果无法使用子网掩码来表示 IP 的话， 表达地址就会很麻烦。 使用 [net-mgmt/ipcalc](http://www.freebsd.org/cgi/url.cgi?ports/net-mgmt/ipcalc/pkg-descr) port 可以帮助进行计算。 请参见下面的网页了解如何撰写长度掩码： `[`jodies.de/ipcalc`](http://jodies.de/ipcalc)`。

#### 31.5.11.7. PORT

如果为源或目的指定了匹配端口， 规则就只能应用于 TCP 和 UDP 包了。 当编写端口比较规则时， 可以指定 `/etc/services` 中所定义的名字， 也可以直接用端口号来指定。 如果端口号出现在源对象一侧， 则被认为是源端口号； 反之， 则被认为是目的端口号。 要使用新式的规则处理逻辑， 就必须与 `to` 对象配合使用这个选项。 使用的例子： `from any to any port = 80`

对单个端口的比较可以多种方式进行， 并可使用不同的比较算符。 此外， 还可以指定端口的范围。

port "=" | "!=" | "<" | ">" | "<=" | ">=" | "eq" | "ne" | "lt" | "gt" | "le" | "ge".

要指定端口范围， 可以使用 "<>" | "><"。

### 警告:

在源和目的匹配参数之后， 需要使用下面两个参数， 才能够使用新式的规则处理逻辑。

#### 31.5.11.8. TCP_FLAG

标志只对 TCP 过滤有用。 这些字母用来表达 TCP 包头的标志。

新式的规则处理逻辑使用 `flags S` 参数来识别 tcp 会话开始的请求。

#### 31.5.11.9. STATEFUL

`keep state` 表示如果有一个包与规则匹配， 则其筛选参数应激活有状态的过滤机制。

### 注意:

如果使用新式的处理逻辑， 则这个选项是必需的。

### 31.5.12. 有状态过滤

有状态过滤将网络流量当作一种双向的包交换来处理。 如果激活它， keep-state 会动态地为每一个相关的包在双向会话交互过程中产生内部规则。 它能够确认发起者和包的目的地之间的会话是有效的双向包交换过程的一部分。 如果包与这些规则不符， 则将自动地拒绝。

状态保持也使得 ICMP 包能够与 TCP 或 UDP 会话相关。 因此， 如果您在浏览网站时收到允许的状态保持规则匹配的 ICMP 类型 3 代码 4 响应， 则这些响应会被自动地允许进入。 所有 IPF 能够处理的包， 都可以作为某种活跃会话的一部分， 即使它是另一种协议的， 也会被允许进入。

所发生的事情是：

将要通过连入 Internet 公网的网络接口发出的包， 首先会经过动态状态表的检查。 如果包与会话中预期的下一个包匹配， 防火墙就会允许包通过， 并更新状态表中的会话的交互流信息。 不属于活跃会话的包， 则简单地交给输出规则集去检查。

发到连入 Internet 公网接口的包， 也会先经过动态状态表的检查。 如果包与会话中预期的下一个包匹配， 防火墙就会允许包通过， 并更新状态表中的会话的交互流信息。 不属于活跃会话的包， 则简单地交给输入规则集去检查。

当会话结束时， 对应的项会在动态状态表中删除。

有状态过滤使得您能够集中于阻止/允许新的会话。 一旦新会话被允许通过， 则所有后续的包就都被自动地允许通过， 而伪造的包则被自动地拒绝。 如果新的会话被阻止， 则后续的包也都不会被允许通过。 有状态过滤从技术角度而言， 在阻止目前攻击者常用的洪水式攻击来说， 具有更好的抗御能力。

### 31.5.13. 明示允许规则集的例子

下面的规则集是如何编写非常安全的明示允许防火墙规则集的一个范例。 明示允许防火墙只让允许的服务 `pass` (通过)， 而所有其他的访问都会被默认地拒绝。 期望用来保护其他机器的防火墙， 通常也叫做 “网络防火墙”， 应使用至少两个网络接口， 并且通常只有一个接入到受信的一端 (LAN)， 而另一块则接入不受信的一端 (Internet 公网)。 另外， 防火墙也可以配置为只保护它所运行的那个系统 ── 这种类型称作 “主机防火墙”， 通常在接入不受信网络的服务器上使用。

包括 FreeBSD 在内的所有类 UNIX® 系统通常都会使用 `lo0` 和 IP 地址 `127.0.0.1` 用于操作系统中内部的通讯。 防火墙规则必须允许这些包无阻碍地通过。

接入 Internet 公网的网络接口， 是放置规则并允许将访问请求发到 Internet 以及接收响应的地方。 这有可能是用户模式的 PPP `tun0` 接口， 如果您的网卡同 DSL 或电缆调制解调器相联的话。

如果有网卡是直接接入私有网段的， 这些网络接口就可能需要配置允许来自这些 LAN 的包在彼此之间， 以及到外界 (Internet) 上的对应的通过规则。

一般说来， 规则应被组织为三个主要的小节： 所有允许自由通过的接口规则， 发到公网接口的规则， 以及进入公网接口的规则。

每一个公网接口规则中， 经常会匹配到的规则应该放置在尽可能靠前的位置。 而最后一个规则应该是阻止包通过， 并记录它们。

下面防火墙规则集中， Outbound 部分是一些使用 `pass` 的规则， 这些规则指定了允许访问的公网 Internet 服务， 并且指定了 `quick`、 `on`、 `proto`、 `port`， 以及 `keep state` 这些选项。 `proto tcp` 规则还指定了 `flag` 这个选项， 这样会话的第一个包将出发状态机制。

接收部分则首先阻止所有不希望的包， 这样做有两个不同的原因。 其一是恶意的包可能和某些允许的流量规则存在部分匹配， 而我们希望阻止， 而不是让这些包仅仅与 `allow` 规则部分匹配就允许它们进入。 其二是， 已经确信要阻止的包被拒绝这件事， 往往并不是我们需要关注的， 因此只要简单地予以阻止即可。 防火墙规则集中的每个部分的最后一条规则都是阻止并记录包， 这有助于为逮捕攻击者留下法律所要求的证据。

另外一个需要注意的事情是确保系统对不希望的数据包不做回应。 无效的包应被丢弃和消失。 这样， 攻击者便无法知道包是否到达了您的系统。 攻击者对系统了解的越少， 攻陷系统所需的时间也就越多。 包含 `log first` 选项的规则只会记录它们第一次被触发时的包， 在例子中这个选项被用于记录 `nmap OS 指纹探测` 规则。 [security/nmap](http://www.freebsd.org/cgi/url.cgi?ports/security/nmap/pkg-descr) 是攻击者常用的一种用于探测目标系统所用操作系统的工具。

如果您看到了 `log first` 规则的日志， 就应该用 `ipfstat -hio` 命令来看看那个规则被匹配的次数。 如果数目较大， 则表示系统正在受到洪水式攻击。

如果记录的包的端口号并不是您所知道的， 可以在 `/etc/services` 或 `[`en.wikipedia.org/wiki/List_of_TCP_and_UDP_port_numbers`](http://en.wikipedia.org/wiki/List_of_TCP_and_UDP_port_numbers)` 了解端口号通常的用途。

参考下面的网页， 了解木马使用的端口： `[`www.sans.org/security-resources/idfaq/oddports.php`](http://www.sans.org/security-resources/idfaq/oddports.php)`。

下面是我在自己的系统中使用的完整的， 非常安全的 `明示允许` 防火墙规则集。 直接使用这个规则集不会给您造成问题， 您所要做的只是注释掉那些您不需要 `pass`(允许通过) 的服务。

如果在日志中发现了希望 `阻止` 的记录， 只需在 inbound 小节中增加一条阻止规则集可。

您必须将每一个规则中的 `dc0` 替换为您系统上接入 Internet 的网络接口名称， 例如， 用户环境下的 PPP 应该是 `tun0`。

在 `/etc/ipf.rules` 中加入下面的内容：

```
#################################################################
# No restrictions on Inside LAN Interface for private network
# Not needed unless you have LAN
#################################################################

#pass out quick on xl0 all
#pass in quick on xl0 all

#################################################################
# No restrictions on Loopback Interface
#################################################################
pass in quick on lo0 all
pass out quick on lo0 all

#################################################################
# Interface facing Public Internet (Outbound Section)
# Match session start requests originating from behind the
# firewall on the private network
# or from this gateway server destined for the public Internet.
#################################################################

# Allow out access to my ISP's Domain name server.
# xxx must be the IP address of your ISP's DNS.
# Dup these lines if your ISP has more than one DNS server
# Get the IP addresses from /etc/resolv.conf file
pass out quick on dc0 proto tcp from any to xxx port = 53 flags S keep state
pass out quick on dc0 proto udp from any to xxx port = 53 keep state

# Allow out access to my ISP's DHCP server for cable or DSL networks.
# This rule is not needed for 'user ppp' type connection to the
# public Internet, so you can delete this whole group.
# Use the following rule and check log for IP address.
# Then put IP address in commented out rule & delete first rule
pass out log quick on dc0 proto udp from any to any port = 67 keep state
#pass out quick on dc0 proto udp from any to z.z.z.z port = 67 keep state

# Allow out non-secure standard www function
pass out quick on dc0 proto tcp from any to any port = 80 flags S keep state

# Allow out secure www function https over TLS SSL
pass out quick on dc0 proto tcp from any to any port = 443 flags S keep state

# Allow out send & get email function
pass out quick on dc0 proto tcp from any to any port = 110 flags S keep state
pass out quick on dc0 proto tcp from any to any port = 25 flags S keep state

# Allow out Time
pass out quick on dc0 proto tcp from any to any port = 37 flags S keep state

# Allow out nntp news
pass out quick on dc0 proto tcp from any to any port = 119 flags S keep state

# Allow out gateway & LAN users' non-secure FTP ( both passive & active modes)
# This function uses the IPNAT built in FTP proxy function coded in
# the nat rules file to make this single rule function correctly.
# If you want to use the pkg_add command to install application packages
# on your gateway system you need this rule.
pass out quick on dc0 proto tcp from any to any port = 21 flags S keep state

# Allow out ssh/sftp/scp (telnet/rlogin/FTP replacements)
# This function is using SSH (secure shell)
pass out quick on dc0 proto tcp from any to any port = 22 flags S keep state

# Allow out insecure Telnet
pass out quick on dc0 proto tcp from any to any port = 23 flags S keep state

# Allow out FreeBSD CVSup
pass out quick on dc0 proto tcp from any to any port = 5999 flags S keep state

# Allow out ping to public Internet
pass out quick on dc0 proto icmp from any to any icmp-type 8 keep state

# Allow out whois from LAN to public Internet
pass out quick on dc0 proto tcp from any to any port = 43 flags S keep state

# Block and log only the first occurrence of everything
# else that's trying to get out.
# This rule implements the default block
block out log first quick on dc0 all

#################################################################
# Interface facing Public Internet (Inbound Section)
# Match packets originating from the public Internet
# destined for this gateway server or the private network.
#################################################################

# Block all inbound traffic from non-routable or reserved address spaces
block in quick on dc0 from 192.168.0.0/16 to any    #RFC 1918 private IP
block in quick on dc0 from 172.16.0.0/12 to any     #RFC 1918 private IP
block in quick on dc0 from 10.0.0.0/8 to any        #RFC 1918 private IP
block in quick on dc0 from 127.0.0.0/8 to any       #loopback
block in quick on dc0 from 0.0.0.0/8 to any         #loopback
block in quick on dc0 from 169.254.0.0/16 to any    #DHCP auto-config
block in quick on dc0 from 192.0.2.0/24 to any      #reserved for docs
block in quick on dc0 from 204.152.64.0/23 to any   #Sun cluster interconnect
block in quick on dc0 from 224.0.0.0/3 to any       #Class D & E multicast

##### Block a bunch of different nasty things. ############
# That I do not want to see in the log

# Block frags
block in quick on dc0 all with frags

# Block short tcp packets
block in quick on dc0 proto tcp all with short

# block source routed packets
block in quick on dc0 all with opt lsrr
block in quick on dc0 all with opt ssrr

# Block nmap OS fingerprint attempts
# Log first occurrence of these so I can get their IP address
block in log first quick on dc0 proto tcp from any to any flags FUP

# Block anything with special options
block in quick on dc0 all with ipopts

# Block public pings
block in quick on dc0 proto icmp all icmp-type 8

# Block ident
block in quick on dc0 proto tcp from any to any port = 113

# Block all Netbios service. 137=name, 138=datagram, 139=session
# Netbios is MS/Windows sharing services.
# Block MS/Windows hosts2 name server requests 81
block in log first quick on dc0 proto tcp/udp from any to any port = 137
block in log first quick on dc0 proto tcp/udp from any to any port = 138
block in log first quick on dc0 proto tcp/udp from any to any port = 139
block in log first quick on dc0 proto tcp/udp from any to any port = 81

# Allow traffic in from ISP's DHCP server. This rule must contain
# the IP address of your ISP's DHCP server as it's the only
# authorized source to send this packet type. Only necessary for
# cable or DSL configurations. This rule is not needed for
# 'user ppp' type connection to the public Internet.
# This is the same IP address you captured and
# used in the outbound section.
pass in quick on dc0 proto udp from z.z.z.z to any port = 68 keep state

# Allow in standard www function because I have apache server
pass in quick on dc0 proto tcp from any to any port = 80 flags S keep state

# Allow in non-secure Telnet session from public Internet
# labeled non-secure because ID/PW passed over public Internet as clear text.
# Delete this sample group if you do not have telnet server enabled.
#pass in quick on dc0 proto tcp from any to any port = 23 flags S keep state

# Allow in secure FTP, Telnet, and SCP from public Internet
# This function is using SSH (secure shell)
pass in quick on dc0 proto tcp from any to any port = 22 flags S keep state

# Block and log only first occurrence of all remaining traffic
# coming into the firewall. The logging of only the first
# occurrence avoids filling up disk with Denial of Service logs.
# This rule implements the default block.
block in log first quick on dc0 all
################### End of rules file #####################################
```

### 31.5.14. NAT

NAT 是 网络地址转换(Network Address Translation) 的缩写。 对于那些熟悉 Linux® 的人来说， 这个概念叫做 IP 伪装 (Masquerading)； NAT 和 IP 伪装是完全一样的概念。 由 IPF 的 NAT 提供的一项功能是， 将防火墙后的本地局域网 (LAN) 共享一个 ISP 提供的 IP 地址来接入 Internet 公网。

有些人可能会问， 为什么需要这么做。 一般而言， ISP 会为非商业用户提供动态的 IP 地址。 动态地址意味着每次登录到 ISP 都有可能得到不同的 IP 地址， 无论是采用电话拨号登录， 或使用 cable 以及 DSL 调制解调器的方式。 这个 IP 是您与 Internet 公网交互时使用的身份。

现在考虑家中有五台 PC 需要访问 Internet 的情形。 您可能需要向 ISP 为每一台 PC 所使用的独立的 Internet 账号付费， 并且拥有五根电话线。

有了 NAT， 您就只需要一个 ISP 账号， 然后将另外四台 PC 的网卡通过交换机连接起来， 并通过运行 FreeBSD 系统的那台机器作为网关连接出去。 NAT 会自动地将每一台 PC 在内网的 LAN IP 地址， 在离开防火墙时转换为公网的 IP 地址。 此外， 当数据包返回时， 也将进行逆向的转换。

在 IP 地址空间中， 有一些特殊的范围是保留供经过 NAT 的内网 LAN IP 地址使用的。 根据 RFC 1918， 可以使用下面这些 IP 范围用于内网， 它们不会在 Internet 公网上路由：

| 起始 IP `10.0.0.0` | - | 结束 IP `10.255.255.255` |
| 起始 IP `172.16.0.0` | - | 结束 IP `172.31.255.255` |
| 起始 IP `192.168.0.0` | - | 结束 IP `192.168.255.255` |

### 31.5.15. IPNAT

NAT 规则是通过 `ipnat` 命令加载的。 默认情况下， NAT 规则会保存在 `/etc/ipnat.rules` 文件中。 请参见 [ipnat(1)](http://www.FreeBSD.org/cgi/man.cgi?query=ipnat&sektion=1&manpath=freebsd-release-ports) 了解更多的详情。

如果在 NAT 已经启动之后想要修改 NAT 规则， 可以修改保存 NAT 规则的那个文件， 然后在执行 `ipnat` 命令时加上 `-CF` 参数， 以删除在用的 NAT 内部规则表， 以及所有地址翻译表中已有的项。

要重新加载 NAT 规则， 可以使用类似下面的命令：

```
# `ipnat -CF -f /etc/ipnat.rules`
```

如果想要看看您系统上 NAT 的统计信息， 可以用下面的命令：

```
# `ipnat -s`
```

要列出当前的 NAT 表的映射关系， 使用下面的命令：

```
# `ipnat -l`
```

要显示详细的信息并显示与规则处理和当前的规则/表项：

```
# `ipnat -v`
```

### 31.5.16. IPNAT 规则

NAT 规则非常的灵活， 能够适应商业用户和家庭用户的各种不同的需求。

这里所介绍的规则语法已经被简化， 以适应非商用环境中的一般情况。 完整的规则语法描述， 请参考 [ipnat(5)](http://www.FreeBSD.org/cgi/man.cgi?query=ipnat&sektion=5&manpath=freebsd-release-ports) 联机手册中的介绍。

NAT 规则的写法与下面的例子类似：

```
map *`IF`* *`LAN_IP_RANGE`* -> *`PUBLIC_ADDRESS`*
```

关键词 `map` 出现在规则的最前面。

将 *`IF`* 替换为对外的网络接口名。

*`LAN_IP_RANGE`* 是内网中的客户机使用的地址范围。 通常情况下， 这应该是类似 `192.168.1.0/24` 的地址。

*`PUBLIC_ADDRESS`* 既可以是外网的 IP 地址， 也可以是 `0/32` 这个特殊的关键字， 它表示分配到 *`IF`* 上的所有地址。

### 31.5.17. NAT 的工作原理

当包从 LAN 到达防火墙， 而目的地址是公网地址时， 它首先会通过 outbound 过滤规则。 接下来， NAT 会得到包， 并按自顶向下的顺序处理规则， 而第一个匹配的规则将生效。 NAT 接下来会根据包对应的接口名字和源 IP 地址检查所有的规则。 如果包和某个 NAT 规则匹配， 则会检查包的 (源 IP 地址， 例如， 内网的 IP 地址) 是否在 NAT 规则中箭头左侧指定的 IP 地址范围匹配。 如果匹配， 则包的原地址将被根据用 `0/32` 关键字指定的 IP 地址重写。 NAT 将向它的内部 NAT 表发送此地址， 这样， 当包从 Internet 公网中返回时， 就能够把地址映射回原先的内网 IP 地址， 并在随后使用过滤器规则来处理。

### 31.5.18. 启用 IPNAT

要启用 IPNAT， 只需在 `/etc/rc.conf` 中加入下面一些语句。

使机器能够在不同的网络接口之间进行包的转发， 需要：

```
gateway_enable="YES"
```

每次开机时自动启动 IPNAT：

```
ipnat_enable="YES"
```

指定 IPNAT 规则集文件：

```
ipnat_rules="/etc/ipnat.rules"
```

### 31.5.19. 大型 LAN 中的 NAT

对于在一个 LAN 中有大量 PC， 以及包含多个 LAN 的情形， 把所有的内网 IP 地址都映射到同一个公网 IP 上会导致资源不够的问题， 因为同一个端口可能在许多做了 NAT 的 LAN PC 上被多次使用， 并导致碰撞。 有两种方法来缓解这个难题。

#### 31.5.19.1. 指定使用哪些端口

普通的 NAT 规则类似于：

```
map dc0 192.168.1.0/24 -> 0/32
```

上面的规则中， 包的源端口在包通过 IPNAT 时时不会发生变化的。 通过使用 `portmap` 关键字， 您可以要求 IPNAT 只使用指定范围内的端口地址。 比如说， 下面的规则将让 IPNAT 把源端口改为指定范围内的端口：

```
map dc0 192.168.1.0/24 -> 0/32 portmap tcp/udp 20000:60000
```

使用 `auto` 关键字可以让配置变得更简单一些， 它会要求 IPNAT 自动地检测可用的端口并使用：

```
map dc0 192.168.1.0/24 -> 0/32 portmap tcp/udp auto
```

#### 31.5.19.2. 使用公网地址池

对很大的 LAN 而言， 总有一天会达到这样一个临界值， 此时的 LAN 地址已经多到了无法只用一个公网地址表现的程度。 如果有可用的一块公网 IP 地址， 则可以将这些地址作为一个 “地址池” 来使用， 让 IPNAT 来从这些公网 IP 地址中挑选用于发包的地址， 并将其为这些包创建映射关系。

例如， 如果将下面这个把所有包都映射到同一公网 IP 地址的规则：

```
map dc0 192.168.1.0/24 -> 204.134.75.1
```

稍作修改， 就可以用子网掩码来表达 IP 地址范围：

```
map dc0 192.168.1.0/24 -> 204.134.75.0/255.255.255.0
```

或者用 CIDR 记法来指定的一组地址了：

```
map dc0 192.168.1.0/24 -> 204.134.75.0/24
```

### 31.5.20. 端口重定向

非常流行的一种做法是， 将 web 服务器、 邮件服务器、 数据库服务器以及 DNS 分别放到 LAN 上的不同的 PC 上。 这种情况下， 来自这些服务器的网络流量仍然应该被 NAT， 但必须有办法把进入的流量发到对应的局域网的 PC 上。 IPNAT 提供了 NAT 重定向机制来解决这个问题。 考虑下面的情况， 您的 web 服务器的 LAN 地址是 `10.0.10.25`， 而您的唯一的公网 IP 地址是 `20.20.20.5`， 则可以编写这样的规则：

```
rdr dc0 20.20.20.5/32 port 80 -> 10.0.10.25 port 80
```

或者：

```
rdr dc0 0.0.0.0/0 port 80 -> 10.0.10.25 port 80
```

另外， 也可以让 LAN 地址 `10.0.10.33` 上运行的 LAN DNS 服务器来处理公网上的 DNS 请求：

```
rdr dc0 20.20.20.5/32 port 53 -> 10.0.10.33 port 53 udp
```

### 31.5.21. FTP 和 NAT

FTP 是一个在 Internet 如今天这样为人所熟知之前就已经出现的恐龙， 那时， 研究机构和大学是通过租用的线路连到一起的， 而 FTP 则被用于在科研人员之间共享大文件。 那时， 数据的安全性并不是需要考虑的事情。 若干年之后， FTP 协议则被埋进了正在形成中的 Internet 骨干， 而它使用明文来交换用户名和口令的缺点， 并没有随着新出现的一些安全需求而得到改变。 FTP 提供了两种不同的风格， 即主动模式和被动模式。 两者的区别在于数据通道的建立方式。 被动模式相对而言要更加安全， 因为数据通道是由发起 ftp 会话的一方建立的。 关于 FTP 以及它所提供的不同模式， 在 `[`www.slacksite.com/other/ftp.html`](http://www.slacksite.com/other/ftp.html)` 进行了很好的阐述。

#### 31.5.21.1. IPNAT 规则

IPNAT 提供了一个内建的 FTP 代理选项， 它可以在 NAT map 规则中指定。 它能够监视所有外发的 FTP 主动或被动模式的会话开始请求， 并动态地创建临时性的过滤器规则， 只打开用于数据通道的端口号。 这样， 就消除了 FTP 一般会给防火墙带来的， 需要大范围地打开高端口所可能带来的安全隐患。

下面的规则可以处理来自内网的 FTP 访问：

```
map dc0 10.0.10.0/29 -> 0/32 proxy port 21 ftp/tcp
```

这个规则能够处理来自网关的 FTP 访问：

```
map dc0 0.0.0.0/0 -> 0/32 proxy port 21 ftp/tcp
```

这个则处理所有来自内网的非 FTP 网络流量：

```
map dc0 10.0.10.0/29 -> 0/32
```

FTP map 规则应该在普通的 map 规则之前出现。 所有的包会从最上面的第一个规则开始进行检查。 匹配的顺序是网卡名称， 内网源 IP 地址， 以及它是否是 FTP 包。 如果所有这些规则都匹配成功， 则 FTP 代理将建立一个临时的过滤规则， 以便让 FTP 会话的数据包能够正常出入， 同时对这些包进行 NAT。 所有的 LAN 数据包， 如果没有匹配第一条规则， 则会继续尝试匹配下面的规则， 并最终被 NAT。

#### 31.5.21.2. IPNAT FTP 过滤规则

如果使用了 NAT FTP 代理， 则只需要为 FTP 创建一个规则。

如果不使用 FTP 代理， 就需要下面这三个规则：

```
# Allow out LAN PC client FTP to public Internet
# Active and passive modes
pass out quick on rl0 proto tcp from any to any port = 21 flags S keep state

# Allow out passive mode data channel high order port numbers
pass out quick on rl0 proto tcp from any to any port > 1024 flags S keep state

# Active mode let data channel in from FTP server
pass in quick on rl0 proto tcp from any to any port = 20 flags S keep state
```

## 31.6. IPFW

IPFIREWALL (IPFW) 是一个由 FreeBSD 发起的防火墙应用软件， 它由 FreeBSD 的志愿者成员编写和维护。 它使用了传统的无状态规则和规则编写方式， 以期达到简单状态逻辑所期望的目标。

标准的 FreeBSD 安装中， IPFW 所给出的规则集样例 (可以在 `/etc/rc.firewall` 和 `/etc/rc.firewall6` 中找到) 非常简单， 建议不要不加修改地直接使用。 该样例中没有使用状态过滤， 而该功能在大部分的配置中都是非常有用的， 因此这一节并不以系统自带的样例作为基础。

IPFW 的无状态规则语法， 是由一种提供复杂的选择能力的技术支持的， 这种技术远远超出了一般的防火墙安装人员的知识水平。 IPFW 是为满足专业用户， 以及掌握先进技术的电脑爱好者们对于高级的包选择需求而设计的。 要完全释放 IPFW 的规则所拥有的强大能力， 需要对不同的协议的细节有深入的了解， 并根据它们独特的包头信息来编写规则。 这一级别的详细阐述超出了这本手册的范围。

IPFW 由七个部分组成， 其主要组件是内核的防火墙过滤规则处理器， 及其集成的数据包记帐工具、 日志工具、 用以触发 NAT 工具的 `divert` (转发) 规则、 高级特殊用途工具、 dummynet 流量整形机制， `fwd rule` 转发工具， 桥接工具， 以及 ipstealth 工具。 IPFW 支持 IPv4 和 IPv6。

### 31.6.1. 启用 IPFW

IPFW 是基本的 FreeBSD 安装的一部分， 以单独的可加载内核模块的形式提供。 如果在 `rc.conf` 中加入 `firewall_enable="YES"` 语句， 就会自动地加载对应的内核模块。 除非您打算使用由它提供的 NAT 功能， 一般情况下并不需要把 IPFW 编进 FreeBSD 的内核。

如果将 `firewall_enable="YES"` 加入到 `rc.conf` 中并重新启动系统， 则下列信息将在启动过程中， 以高亮的白色显示出来：

```
ipfw2 initialized, divert disabled, rule-based forwarding disabled, default to deny, logging disabled
```

可加载内核模块在编译时加入了记录日志的能力。 要启用日志功能， 并配置详细日志记录的限制， 需要在 `/etc/sysctl.conf` 中加入一些配置。 这些设置将在重新启动之后生效：

```
net.inet.ip.fw.verbose=1
net.inet.ip.fw.verbose_limit=5
```

### 31.6.2. 内核选项

把下列选项在编译 FreeBSD 内核时就加入， 并不是启用 IPFW 所必需的， 除非您需要使用 NAT 功能。 这里只是将这些选项作为背景知识来介绍。

```
options    IPFIREWALL
```

这个选项将 IPFW 作为内核的一部分来启用。

```
options    IPFIREWALL_VERBOSE
```

这个选项将启用记录通过 IPFW 的匹配了包含 `log` 关键字规则的每一个包的功能。

```
options    IPFIREWALL_VERBOSE_LIMIT=5
```

以每项的方式， 限制通过 [syslogd(8)](http://www.FreeBSD.org/cgi/man.cgi?query=syslogd&sektion=8&manpath=freebsd-release-ports) 记录的包的个数。 如果在比较恶劣的环境下记录防火墙的活动可能会需要这个选项。 它能够避免潜在的针对 syslog 的洪水式拒绝服务攻击。

```
options    IPFIREWALL_DEFAULT_TO_ACCEPT
```

这个选项默认地允许所有的包通过防火墙， 如果您是第一次配置防火墙， 使用这个选项将是一个不错的主意。

```
options    IPDIVERT
```

这一选项启用 NAT 功能。

### 注意:

如果内核选项中没有加入 `IPFIREWALL_DEFAULT_TO_ACCEPT`， 而配置使用的规则集中也没有明确地指定允许连接进入的规则， 默认情况下， 发到本机和从本机发出的所有包都会被阻止。

### 31.6.3. `/etc/rc.conf` Options

启用防火墙：

```
firewall_enable="YES"
```

要选择由 FreeBSD 提供的几种防火墙类型中的一种来作为默认配置， 您需要阅读 `/etc/rc.firewall` 文件并选出合适的类型， 然后在 `/etc/rc.conf` 中加入类似下面的配置：

```
firewall_type="open"
```

您还可以指定下列配置规则之一：

*   `open` ── 允许所有流量通过。

*   `client` ── 只保护本机。

*   `simple` ── 保护整个网络。

*   `closed` ── 完全禁止除回环设备之外的全部 IP 流量。

*   `UNKNOWN` ── 禁止加载防火墙规则。

*   `filename` ── 到防火墙规则文件的绝对路径。

有两种加载自定义 ipfw 防火墙规则的方法。 其一是将变量 `firewall_type` 设为包含不带 [ipfw(8)](http://www.FreeBSD.org/cgi/man.cgi?query=ipfw&sektion=8&manpath=freebsd-release-ports) 命令行选项的 *防火墙规则* 文件的完整路径。 下面是一个简单的规则集例子：

```
add deny in
add deny out
```

除此之外， 也可以将 `firewall_script` 变量设为包含 `ipfw` 命令的可执行脚本， 这样这个脚本会在启动时自动执行。 与前面规则集文件等价的规则脚本如下：

`ipfw` 命令是在防火墙运行时， 用于在其内部规则表中手工逐条添加或删除防火墙规则的标准工具。 这一方法的问题在于， 一旦您的关闭计算机或停机， 则所有增加或删除或修改的规则也就丢掉了。 把所有的规则都写到一个文件中， 并在启动时使用这个文件来加载规则， 或一次大批量地替换防火墙规则， 那么推荐使用这里介绍的方法。

`ipfw` 的另一个非常实用的功能是将所有正在运行的防火墙规则显示出来。 IPFW 的记账机制会为每一个规则动态地创建计数器， 用以记录与它们匹配的包的数量。 在测试规则的过程中， 列出规则及其计数器是了解它们是否工作正常的重要手段。

按顺序列出所有的规则：

```
# `ipfw list`
```

列出所有的规则， 同时给出最后一次匹配的时间戳：

```
# `ipfw -t list`
```

列出所有的记账信息、 匹配规则的包的数量， 以及规则本身。 第一列是规则的编号， 随后是发出包匹配的数量， 进入包的匹配数量， 最后是规则本身。

```
# `ipfw -a list`
```

列出所有的动态规则和静态规则：

```
# `ipfw -d list`
```

同时显示已过期的动态规则：

```
# `ipfw -d -e list`
```

将计数器清零：

```
# `ipfw zero`
```

只把规则号为 *`NUM`* 的计数器清零：

```
# `ipfw zero NUM`
```

### 31.6.4. IPFW 规则集

规则集是指一组编写好的依据包的值决策允许通过或阻止 IPFW 规则。 包的双向交换组成了一个会话交互。 防火墙规则集会作用于来自于 Internet 公网的包以及由系统发出来回应这些包的数据包。 每一个 TCP/IP 服务 (例如 telnet, www, 邮件等等) 都由协议预先定义了其特权 (监听) 端口。 发到特定服务的包会从源地址使用非特权 (高编号) 端口发出， 并发到特定服务在目的地址的对应端口。 所有这些参数 (例如： 端口和地址） 都是可以为防火墙规则所利用的， 判别是否允许服务通过的标准。

当有数据包进入防火墙时， 会从规则集里的第一个规则开始进行比较， 并自顶向下地进行匹配。 当包与某个选择规则参数相匹配时， 将会执行规则所定义的动作， 并停止规则集搜索。 这种策略， 通常也被称作 “最先匹配者获胜” 的搜索方法。 如果没有任何与包相匹配的规则， 那么它就会根据强制的 IPFW 默认规则， 也就是 65535 号规则截获。 一般情况下这个规则是阻止包， 而且不给出任何回应。

### 注意:

如果规则定义的动作是 `count`、 `skipto` 或 `tee` 规则的话， 搜索会继续。

这里所介绍的规则， 都是使用了那些包含状态功能的， 也就是 `keep state`、 `limit`、 `in`、 `out` 以及 `via` 选项的规则。 这是编写明示允许防火墙规则集所需的基本框架。

### 警告:

在操作防火墙规则时应谨慎行事， 如果操作不当， 很容易将自己反锁在外面。

#### 31.6.4.1. 规则语法

这里所介绍的规则语法已经经过了简化， 只包括了建立标准的明示允许防火墙规则集所必需的那些。 要了解完整的规则语法说明， 请参见 [ipfw(8)](http://www.FreeBSD.org/cgi/man.cgi?query=ipfw&sektion=8&manpath=freebsd-release-ports) 联机手册。

规则是由关键字组成的： 这些关键字必须以特定的顺序从左到右书写。 下面的介绍中， 关键字使用粗体表示。 某些关键字还包括了子选项， 这些子选项本身可能也是关键字， 有些还可以包含更多的子选项。

`#` 用于表示开始一段注释。 它可以出现在一个规则的后面， 也可以独占一行。 空行会被忽略。

*`CMD RULE_NUMBER ACTION LOGGING SELECTION STATEFUL`*

##### 31.6.4.1.1. CMD

每一个新的规则都应以 *`add`* 作为前缀， 它表示将规则加入内部表。

##### 31.6.4.1.2. RULE_NUMBER

每一条规则都与一个范围在 1 到 65535 之间的规则编号相关联。

##### 31.6.4.1.3. ACTION

每一个规则可以与下列的动作之一相关联， 所指定的动作将在进入的数据包与规则所指定的选择标准相匹配时执行。

*`allow | accept | pass | permit`*

这些关键字都表示允许匹配规则的包通过防火墙， 并停止继续搜索规则。

*`check-state`*

根据动态规则表检查数据包。 如果匹配， 则执行规则所指定的动作， 亦即生成动态规则； 否则， 转移到下一个规则。 check-state 规则没有选择标准。 如果规则集中没有 check-state 规则， 则会在第一个 keep-state 或 limit 规则处， 对动态规则表实施检查。

*`deny | drop`*

这两个关键字都表示丢弃匹配规则的包。 同时， 停止继续搜索规则。

##### 31.6.4.1.4. LOGGING

*`log`* or *`logamount`*

当数据包与带 `log` 关键字的规则匹配时， 将通过名为 SECURITY 的 facility 来把消息记录到 [syslogd(8)](http://www.FreeBSD.org/cgi/man.cgi?query=syslogd&sektion=8&manpath=freebsd-release-ports)。 只有在记录的次数没有超过 logamount 参数所指定的次数时， 才会记录日志。 如果没有指定 `logamount`， 则会以 sysctl 变量 `net.inet.ip.fw.verbose_limit` 所指定的限制为准。 如果将这两种限制值之一指定为零， 则表示不作限制。 如果达到了限制数， 可以通过将规则的日志计数或包计数清零来重新启用日志， 请参见 `ipfw reset log` 命令来了解细节。

### 注意:

日志是在所有其他匹配条件都验证成功之后， 在针对包实施最终动作 (accept, deny) 之前进行的。 您可以自行决定哪些规则应启用日志。

##### 31.6.4.1.5. SELECTION

这一节所介绍的关键字主要用来描述检查包的哪些属性， 用以判断包是否与规则相匹配。 下面是一些通用的用于匹配包特征的属性， 它们必须按顺序使用：

*`udp | tcp | icmp`*

也可以指定在 `/etc/protocols` 中所定义的协议。 这个值定义的是匹配的协议， 在规则中必须指定它。

*`from src to dst`*

`from` 和 `to` 关键字用于匹配 IP 地址。 规则中必须 *同时* 指定源和目的两个参数。 如果需要匹配任意 IP 地址， 可以使用特殊关键字 `any`。 还有一个特殊关键字， 即 `me`， 用于匹配您的 FreeBSD 系统上所有网络接口上所配置的 IP 地址， 它可以用于表达网络上的其他计算机到防火墙 (也就是本机)， 例如 `from me to any` 或 `from any to me` 或 `from 0.0.0.0/0 to any` 或 `from any to 0.0.0.0/0` 或 `from 0.0.0.0 to any` 或 `from any to 0.0.0.0` 以及 `from me to 0.0.0.0`。 IP 地址可以通过 带点的 IP 地址/掩码长度 (CIDR 记法)， 或者一个带点的 IP 地址的形式来指定。 这是编写规则时所必需的。 使用 [net-mgmt/ipcalc](http://www.freebsd.org/cgi/url.cgi?ports/net-mgmt/ipcalc/pkg-descr) port 可以用来简化计算。 关于这个工具的更多信息， 也可参考它的主页： `[`jodies.de/ipcalc`](http://jodies.de/ipcalc)`。

*`port number`*

这个参数主要用于那些支持端口号的协议 (例如 TCP 和 UDP)。 如果要通过端口号匹配某个协议， 就必须指定这个参数。 此外， 也可以通过服务的名字 (根据 `/etc/services`) 来指定服务， 这样会比使用数字指定端口号直观一些。

*`in | out`*

相应地， 匹配进入和发出的包。 这里的 `in` 和 `out` 都是关键字， 在编写匹配规则时， 必需作为其他条件的一部分来使用。

*`via IF`*

根据指定的网络接口的名称精确地匹配进出的包。 这里的 `via` 关键字将使得接口名称成为匹配过程的一部分。

*`setup`*

要匹配 TCP 会话的发起请求， 就必须使用它。

*`keep-state`*

这是一个必须使用的关键字。 在发生匹配时， 防火墙将创建一个动态规则， 其默认行为是， 匹配使用同一协议的、从源到目的 IP/端口 的双向网络流量。

*`limit {src-addr | src-port | dst-addr | dst-port}`*

防火墙只允许匹配规则时， 与指定的参数相同的 *`N`* 个连接。 可以指定至少一个源或目的地址及端口。 `limit` 和 `keep-state` 不能在同一规则中同时使用。 `limit` 提供了与 `keep-state` 相同的功能， 并增加了一些独有的能力。

#### 31.6.4.2. 状态规则选项

有状态过滤将网络流量当作一种双向的包交换来处理。 它提供了一种额外的检查能力， 用以检测会话中的包是否来自最初的发送者， 并在遵循双向包交换的规则进行会话。 如果包与这些规则不符， 则将自动地拒绝它们。

`check-state` 用来识别在 IPFW 规则集中的包是否符合动态规则机制的规则。 如果匹配， 则允许包通过， 此时防火墙将创建一个新的动态规则来匹配双向交换中的下一个包。 如果不匹配， 则将继续尝试规则集中的下一个规则。

动态规则机制在 SYN-flood 攻击下是脆弱的， 因为这种情况会产生大量的动态规则， 从而耗尽资源。 为了抵抗这种攻击， 从 FreeBSD 中加入了一个叫做 `limit` 的新选项。 这个选项可以用来限制符合规则的会话允许的并发连接数。 如果动态规则表中的规则数超过 `limit` 的限制数量， 则包将被丢弃。

#### 31.6.4.3. 记录防火墙消息

记录日志的好处是显而易见的： 它提供了在事后检查所发生的状况的方法， 例如哪些包被丢弃了， 这些包的来源和目的地， 从而为您提供找到攻击者所需的证据。

即使启用了日志机制， IPFW 也不会自行生成任何规则的日志。 防火墙管理员需要指定规则集中的哪些规则应该记录日志， 并在这些规则上增加 `log` 动作。 一般来说， 只有 deny 规则应记录日志， 例如对于进入的 ICMP ping 的 deny 规则。 另外， 复制 “默认的 ipfw 终极 deny 规则”， 并加入 `log` 动作来作为您的规则集的最后一条规则也是很常见的用法。 这样， 您就能看到没有匹配任何一条规则的那些数据包。

日志是一把双刃剑， 如果不谨慎地加以利用， 则可能会陷入过多的日志数据中， 并导致磁盘被日志塞满。 将磁盘填满是 DoS 攻击最为老套的手法之一。 由于 syslogd 除了会将日志写入磁盘之外， 还会输出到 root 的控制台屏幕上， 因此有过多的日志信息是很让人恼火的事情。

`IPFIREWALL_VERBOSE_LIMIT=5` 内核选项将限制同一个规则发到系统日志程序 [syslogd(8)](http://www.FreeBSD.org/cgi/man.cgi?query=syslogd&sektion=8&manpath=freebsd-release-ports) 的连续消息的数量。 当内核启用了这个选项时， 某一特定规则所产生的连续消息的数量将封顶为这个数字。 一般来说， 没有办法从连续 200 条一模一样的日志信息中获取更多有用的信息。 举例来说， 如果同一个规则产生了 5 次消息并被记录到 syslogd， 余下的相同的消息将被计数， 并像下面这样发给 syslogd：

```
last message repeated 45 times
```

所有记录的数据包包消息， 默认情况下会最终写到 `/var/log/security` 文件中， 后者在 `/etc/syslog.conf` 文件里进行了定义。

#### 31.6.4.4. 编写规则脚本

绝大多数有经验的 IPFW 用户会创建一个包含规则的文件， 并且， 按能够以脚本形式运行的方式来书写。 这样做最大的一个好处是， 可以大批量地刷新防火墙规则， 而无须重新启动系统就能够激活它们。 这种方法在测试新规则时会非常方便， 因为同一过程在需要时可以多次执行。 作为脚本， 您可以使用符号替换来撰写那些经常需要使用的值， 并用同一个符号在多个规则中反复地表达它。 下面将给出一个例子。

这个脚本使用的语法同 [sh(1)](http://www.FreeBSD.org/cgi/man.cgi?query=sh&sektion=1&manpath=freebsd-release-ports)、 [csh(1)](http://www.FreeBSD.org/cgi/man.cgi?query=csh&sektion=1&manpath=freebsd-release-ports) 以及 [tcsh(1)](http://www.FreeBSD.org/cgi/man.cgi?query=tcsh&sektion=1&manpath=freebsd-release-ports) 脚本兼容。 符号替换字段使用美元符号 $ 作为前缀。 符号字段本身并不使用 $ 前缀。 符号替换字段的值必须使用 "双引号" 括起来。

可以使用类似下面的规则文件：

```
############### start of example ipfw rules script #############
#
ipfw -q -f flush       # Delete all rules
# Set defaults
oif="tun0"             # out interface
odns="192.0.2.11"      # ISP's DNS server IP address
cmd="ipfw -q add "     # build rule prefix
ks="keep-state"        # just too lazy to key this each time
$cmd 00500 check-state
$cmd 00502 deny all from any to any frag
$cmd 00501 deny tcp from any to any established
$cmd 00600 allow tcp from any to any 80 out via $oif setup $ks
$cmd 00610 allow tcp from any to $odns 53 out via $oif setup $ks
$cmd 00611 allow udp from any to $odns 53 out via $oif $ks
################### End of example ipfw rules script ############
```

这就是所要做的全部事情了。 例子中的规则并不重要， 它们主要是用来表示如何使用符号替换。

如果把上面的例子保存到 `/etc/ipfw.rules` 文件中。 下面的命令来会重新加载规则。

```
# `sh /etc/ipfw.rules`
```

`/etc/ipfw.rules` 这个文件可以放到任何位置， 也可以命名为随便什么别的名字。

也可以手工执行下面的命令来达到类似的目的：

```
# `ipfw -q -f flush`
# `ipfw -q add check-state`
# `ipfw -q add deny all from any to any frag`
# `ipfw -q add deny tcp from any to any established`
# `ipfw -q add allow tcp from any to any 80 out via tun0 setup keep-state`
# `ipfw -q add allow tcp from any to 192.0.2.11 53 out via tun0 setup keep-state`
# `ipfw -q add 00611 allow udp from any to 192.0.2.11 53 out via tun0 keep-state`
```

#### 31.6.4.5. 带状态规则集

以下的这组非-NAT 规则集， 是如何编写非常安全的 '明示允许' 防火墙的一个例子。 明示允许防火墙只允许匹配了 pass 规则的包通过， 而默认阻止所有的其他数据包。 用来保护整个网段的防火墙， 至少需要有两个网络接口， 并且其上必须配置规则， 以便让防火墙正常工作。

所有类 UNIX® 操作系统， 也包括 FreeBSD， 都设计为允许使用网络接口 `lo0` 和 IP 地址 `127.0.0.1` 来完成操作系统内部的通讯。 防火墙必须包含一组规则， 使这些数据包能够无障碍地收发。

接入 Internet 公网的那个网络接口上， 应该配置授权和访问控制， 来限制对外的访问， 以及来自 Internet 公网的访问。 这个接口很可能是您的用户态 PPP 接口， 例如 `tun0`， 或者您接在 DSL 或电缆 modem 上的网卡。

如果有至少一个网卡接入了防火墙后的内网 LAN， 则必须为这些接口配置规则， 以便让这些接口之间的包能够顺畅地通过。

所有的规则应被组织为三个部分， 所有应无阻碍地通过的规则， 公网的发出规则， 以及公网的接收规则。

公网接口相关的规则的顺序， 应该是最经常用到的放在尽可能靠前的位置， 而最后一个规则， 则应该是阻止那个接口在那一方向上的包。

发出部分的规则只包含一些 `allow` 规则， 允许选定的那些唯一区分协议的端口号所指定的协议通过， 以允许访问 Internet 公网上的这些服务。 所有的规则中都指定了 `proto`、 `port`、 `in/out`、 `via` 以及 `keep state` 这些选项。 `proto tcp` 规则同时指定 `setup` 选项， 来区分开始协议会话的包， 以触发将包放入 keep state 规则表中的动作。

接收部分则首先阻止所有不希望的包， 这样做有两个不同的原因。 其一是恶意的包可能和某些允许的流量规则存在部分匹配， 而我们希望阻止， 而不是让这些包仅仅与 `allow` 规则部分匹配就允许它们进入。 其二是， 已经确信要阻止的包被拒绝这件事， 往往并不是我们需要关注的， 因此只要简单地予以阻止即可。 防火墙规则集中的每个部分的最后一条规则都是阻止并记录包， 这有助于为逮捕攻击者留下法律所要求的证据。

另外一个需要注意的事情是确保系统对不希望的数据包不做回应。 无效的包应被丢弃和消失。 这样， 攻击者便无法知道包是否到达了您的系统。 攻击者对系统了解的越少， 其攻击的难度也就越大。 如果不知道端口号， 可以查阅 `/etc/services/` 或到 `[`en.wikipedia.org/wiki/List_of_TCP_and_UDP_port_numbers`](http://en.wikipedia.org/wiki/List_of_TCP_and_UDP_port_numbers)` 并查找一下端口号， 以了解其用途。 另外， 您也可以在这个网页上了解常见木马所使用的端口： `[`www.sans.org/security-resources/idfaq/oddports.php`](http://www.sans.org/security-resources/idfaq/oddports.php)`。

#### 31.6.4.6. 明示允许规则集的例子

下面是一个非-NAT 的规则集， 它是一个完整的明示允许规则集。 使用它作为您的规则集不会有什么问题。 只需把那些不需要的服务对应的 pass 规则注释掉就可以了。 如果您在日志中看到消息， 而且不想再看到它们， 只需在接收部分增加一个一个 `deny` 规则。 您可能需要把 `dc0` 改为接入公网的接口的名字。 对于使用用户态 PPP 的用户而言， 应该是 `tun0`。

这些规则遵循一定的模式。

*   所有请求 Internet 公网上服务的会话开始包， 都使用了 `keep-state`。

*   所有来自 Internet 的授权服务请求， 都采用了 `limit` 选项来防止洪水式攻击。

*   所有的规则都使用了 `in` 或者 `out` 来说明方向。

*   所有的规则都使用了 `via` *`接口名`* 来指定应该匹配通过哪一个接口的包。

这些规则都应放到 `/etc/ipfw.rules`。

```
################ Start of IPFW rules file ###############################
# Flush out the list before we begin.
ipfw -q -f flush

# Set rules command prefix
cmd="ipfw -q add"
pif="dc0"     # public interface name of NIC
              # facing the public Internet

#################################################################
# No restrictions on Inside LAN Interface for private network
# Not needed unless you have LAN.
# Change xl0 to your LAN NIC interface name
#################################################################
#$cmd 00005 allow all from any to any via xl0

#################################################################
# No restrictions on Loopback Interface
#################################################################
$cmd 00010 allow all from any to any via lo0

#################################################################
# Allow the packet through if it has previous been added to the
# the "dynamic" rules table by a allow keep-state statement.
#################################################################
$cmd 00015 check-state

#################################################################
# Interface facing Public Internet (Outbound Section)
# Interrogate session start requests originating from behind the
# firewall on the private network or from this gateway server
# destined for the public Internet.
#################################################################

# Allow out access to my ISP's Domain name server.
# x.x.x.x must be the IP address of your ISP.s DNS
# Dup these lines if your ISP has more than one DNS server
# Get the IP addresses from /etc/resolv.conf file
$cmd 00110 allow tcp from any to x.x.x.x 53 out via $pif setup keep-state
$cmd 00111 allow udp from any to x.x.x.x 53 out via $pif keep-state

# Allow out access to my ISP's DHCP server for cable/DSL configurations.
# This rule is not needed for .user ppp. connection to the public Internet.
# so you can delete this whole group.
# Use the following rule and check log for IP address.
# Then put IP address in commented out rule & delete first rule
$cmd 00120 allow log udp from any to any 67 out via $pif keep-state
#$cmd 00120 allow udp from any to x.x.x.x 67 out via $pif keep-state

# Allow out non-secure standard www function
$cmd 00200 allow tcp from any to any 80 out via $pif setup keep-state

# Allow out secure www function https over TLS SSL
$cmd 00220 allow tcp from any to any 443 out via $pif setup keep-state

# Allow out send & get email function
$cmd 00230 allow tcp from any to any 25 out via $pif setup keep-state
$cmd 00231 allow tcp from any to any 110 out via $pif setup keep-state

# Allow out FBSD (make install & CVSUP) functions
# Basically give user root "GOD" privileges.
$cmd 00240 allow tcp from me to any out via $pif setup keep-state uid root

# Allow out ping
$cmd 00250 allow icmp from any to any out via $pif keep-state

# Allow out Time
$cmd 00260 allow tcp from any to any 37 out via $pif setup keep-state

# Allow out nntp news (i.e., news groups)
$cmd 00270 allow tcp from any to any 119 out via $pif setup keep-state

# Allow out secure FTP, Telnet, and SCP
# This function is using SSH (secure shell)
$cmd 00280 allow tcp from any to any 22 out via $pif setup keep-state

# Allow out whois
$cmd 00290 allow tcp from any to any 43 out via $pif setup keep-state

# deny and log everything else that.s trying to get out.
# This rule enforces the block all by default logic.
$cmd 00299 deny log all from any to any out via $pif

#################################################################
# Interface facing Public Internet (Inbound Section)
# Check packets originating from the public Internet
# destined for this gateway server or the private network.
#################################################################

# Deny all inbound traffic from non-routable reserved address spaces
$cmd 00300 deny all from 192.168.0.0/16 to any in via $pif  #RFC 1918 private IP
$cmd 00301 deny all from 172.16.0.0/12 to any in via $pif     #RFC 1918 private IP
$cmd 00302 deny all from 10.0.0.0/8 to any in via $pif          #RFC 1918 private IP
$cmd 00303 deny all from 127.0.0.0/8 to any in via $pif        #loopback
$cmd 00304 deny all from 0.0.0.0/8 to any in via $pif            #loopback
$cmd 00305 deny all from 169.254.0.0/16 to any in via $pif   #DHCP auto-config
$cmd 00306 deny all from 192.0.2.0/24 to any in via $pif       #reserved for docs
$cmd 00307 deny all from 204.152.64.0/23 to any in via $pif  #Sun cluster interconnect
$cmd 00308 deny all from 224.0.0.0/3 to any in via $pif         #Class D & E multicast

# Deny public pings
$cmd 00310 deny icmp from any to any in via $pif

# Deny ident
$cmd 00315 deny tcp from any to any 113 in via $pif

# Deny all Netbios service. 137=name, 138=datagram, 139=session
# Netbios is MS/Windows sharing services.
# Block MS/Windows hosts2 name server requests 81
$cmd 00320 deny tcp from any to any 137 in via $pif
$cmd 00321 deny tcp from any to any 138 in via $pif
$cmd 00322 deny tcp from any to any 139 in via $pif
$cmd 00323 deny tcp from any to any 81 in via $pif

# Deny any late arriving packets
$cmd 00330 deny all from any to any frag in via $pif

# Deny ACK packets that did not match the dynamic rule table
$cmd 00332 deny tcp from any to any established in via $pif

# Allow traffic in from ISP's DHCP server. This rule must contain
# the IP address of your ISP.s DHCP server as it.s the only
# authorized source to send this packet type.
# Only necessary for cable or DSL configurations.
# This rule is not needed for .user ppp. type connection to
# the public Internet. This is the same IP address you captured
# and used in the outbound section.
#$cmd 00360 allow udp from any to x.x.x.x 67 in via $pif keep-state

# Allow in standard www function because I have apache server
$cmd 00400 allow tcp from any to me 80 in via $pif setup limit src-addr 2

# Allow in secure FTP, Telnet, and SCP from public Internet
$cmd 00410 allow tcp from any to me 22 in via $pif setup limit src-addr 2

# Allow in non-secure Telnet session from public Internet
# labeled non-secure because ID & PW are passed over public
# Internet as clear text.
# Delete this sample group if you do not have telnet server enabled.
$cmd 00420 allow tcp from any to me 23 in via $pif setup limit src-addr 2

# Reject & Log all incoming connections from the outside
$cmd 00499 deny log all from any to any in via $pif

# Everything else is denied by default
# deny and log all packets that fell through to see what they are
$cmd 00999 deny log all from any to any
################ End of IPFW rules file ###############################
```

#### 31.6.4.7. 一个 NAT 和带状态规则集的例子

要使用 IPFW 的 NAT 功能， 还需要进行一些额外的配置。 除了其他 IPFIREWALL 语句之外， 还需要在内核编译配置中加上 `option IPDIVERT` 语句。

在 `/etc/rc.conf` 中， 除了普通的 IPFW 配置之外， 还需要加入：

```
natd_enable="YES"                   # Enable NATD function
natd_interface="rl0"                # interface name of public Internet NIC
natd_flags="-dynamic -m"            # -m = preserve port numbers if possible
```

将带状态规则与 `divert natd` 规则 (网络地址转换) 会使规则集的编写变得非常复杂。 `check-state` 的位置， 以及 `divert natd` 规则将变得非常关键。 这样一来， 就不再有简单的顺序处理逻辑流程了。 提供了一种新的动作类型， 称为 `skipto`。 要使用 `skipto` 命令， 就必须给每一个规则进行编号， 以确定 `skipto` 规则号是您希望跳转到的位置。

下面给出了一些未加注释的例子来说明如何编写这样的规则， 用以帮助您理解包处理规则集的处理顺序。

处理流程从规则文件最上边的第一个规则开始处理， 并自顶向下地尝试每一个规则， 直到找到匹配的规则， 且数据包从防火墙中放出为止。 请注意规则号 100 101， 450， 500， 以及 510 的位置非常重要。 这些规则控制发出和接收的包的地址转换过程， 这样它们在 keep-state 动态表中的对应项中就能够与内网的 LAN IP 地址关联。 另一个需要注意的是， 所有的 allow 和 deny 规则都指定了包的方向 (也就是 outbound 或 inbound) 以及网络接口。 最后， 请注意所有发出的会话请求都会请求 `skipto rule 500` 以完成网络地址转换。

下面以 LAN 用户使用 web 浏览器访问一个 web 页面为例。 Web 页面使用 80 来完成通讯。 当包进入防火墙时， 规则 100 并不匹配， 因为它是发出而不是收到的包。 它能够通过规则 101， 因为这是第一个包， 因而它还没有进入动态状态保持表。 包最终到达规则 125， 并匹配该规则。 最终， 它会通过接入 Internet 公网的网卡发出。 这之前， 包的源地址仍然是内网 IP 地址。 一旦匹配这个规则， 就会触发两个动作。 `keep-state` 选项会把这个规则发到 keep-state 动态规则表中， 并执行所指定的动作。 动作是发到规则表中的信息的一部分。 在这个例子中， 这个动作是 `skipto rule 500`。 规则 500 NAT 包的 IP 地址， 并将其发出。 请务必牢记， 这一步非常重要。 接下来， 数据包将到达目的地， 之后返回并从规则集的第一条规则开始处理。 这一次， 它将与规则 100 匹配， 其目的 IP 地址将被映射回对应的内网 LAN IP 地址。 其后， 它会被 `check-state` 规则处理， 进而在暨存会话表中找到对应项， 并发到 LAN。 数据包接下来发到了内网 LAN PC 上， 而后者则会发送从远程服务器请求下一段数据的新数据包。 这个包会再次由 `check-state` 规则检查， 并找到发出的表项， 并执行其关联的动作， 即 `skipto 500`。 包跳转到规则 500 并被 NAT 后发出。

在接收一侧， 已经存在的会话的数据包会被 `check-state` 规则自动地处理， 并转到 `divert nat` 规则。 我们需要解决的问题是， 阻止所有的坏数据包， 而只允许授权的服务。 例如在防火墙上运行了 Apache 服务， 而我们希望人们在访问 Internet 公网的同时， 也能够访问本地的 web 站点。 新的接入开始请求包将匹配规则 100， 而 IP 地址则为防火墙所在的服务器而映射到了 LAN IP。 此后， 包会匹配所有我们希望检查的那些令人生厌的东西， 并最终匹配规则 425。 一旦发生匹配， 会发生两件事。 数据包会被发到 keep-state 动态表， 但此时， 所有来自那个源 IP 的会话请求的数量会被限制为 2。 这一做法能够挫败针对指定端口上服务的 DoS 攻击。 动作同时指定了 `allow` 包应被发到 LAN 上。 包返回时， `check-state` 规则会识别出包属于某一已经存在的会话交互， 并直接把它发到规则 500 做 NAT， 并发到发出接口。

示范规则集 #1:

```
#!/bin/sh
cmd="ipfw -q add"
skip="skipto 500"
pif=rl0
ks="keep-state"
good_tcpo="22,25,37,43,53,80,443,110,119"

ipfw -q -f flush

$cmd 002 allow all from any to any via xl0  # exclude LAN traffic
$cmd 003 allow all from any to any via lo0  # exclude loopback traffic

$cmd 100 divert natd ip from any to any in via $pif
$cmd 101 check-state

# Authorized outbound packets
$cmd 120 $skip udp from any to xx.168.240.2 53 out via $pif $ks
$cmd 121 $skip udp from any to xx.168.240.5 53 out via $pif $ks
$cmd 125 $skip tcp from any to any $good_tcpo out via $pif setup $ks
$cmd 130 $skip icmp from any to any out via $pif $ks
$cmd 135 $skip udp from any to any 123 out via $pif $ks

# Deny all inbound traffic from non-routable reserved address spaces
$cmd 300 deny all from 192.168.0.0/16  to any in via $pif  #RFC 1918 private IP
$cmd 301 deny all from 172.16.0.0/12   to any in via $pif  #RFC 1918 private IP
$cmd 302 deny all from 10.0.0.0/8      to any in via $pif  #RFC 1918 private IP
$cmd 303 deny all from 127.0.0.0/8     to any in via $pif  #loopback
$cmd 304 deny all from 0.0.0.0/8       to any in via $pif  #loopback
$cmd 305 deny all from 169.254.0.0/16  to any in via $pif  #DHCP auto-config
$cmd 306 deny all from 192.0.2.0/24    to any in via $pif  #reserved for docs
$cmd 307 deny all from 204.152.64.0/23 to any in via $pif  #Sun cluster
$cmd 308 deny all from 224.0.0.0/3     to any in via $pif  #Class D & E multicast

# Authorized inbound packets
$cmd 400 allow udp from xx.70.207.54 to any 68 in $ks
$cmd 420 allow tcp from any to me 80 in via $pif setup limit src-addr 1

$cmd 450 deny log ip from any to any

# This is skipto location for outbound stateful rules
$cmd 500 divert natd ip from any to any out via $pif
$cmd 510 allow ip from any to any

######################## end of rules  ##################
```

下面的这个规则集基本上和上面一样， 但使用了易于读懂的编写方式， 并给出了相当多的注解， 以帮助经验较少的 IPFW 规则编写者更好地理解这些规则到底在做什么。

示范规则集 #2：

```
#!/bin/sh
################ Start of IPFW rules file ###############################
# Flush out the list before we begin.
ipfw -q -f flush

# Set rules command prefix
cmd="ipfw -q add"
skip="skipto 800"
pif="rl0"     # public interface name of NIC
              # facing the public Internet

#################################################################
# No restrictions on Inside LAN Interface for private network
# Change xl0 to your LAN NIC interface name
#################################################################
$cmd 005 allow all from any to any via xl0

#################################################################
# No restrictions on Loopback Interface
#################################################################
$cmd 010 allow all from any to any via lo0

#################################################################
# check if packet is inbound and nat address if it is
#################################################################
$cmd 014 divert natd ip from any to any in via $pif

#################################################################
# Allow the packet through if it has previous been added to the
# the "dynamic" rules table by a allow keep-state statement.
#################################################################
$cmd 015 check-state

#################################################################
# Interface facing Public Internet (Outbound Section)
# Check session start requests originating from behind the
# firewall on the private network or from this gateway server
# destined for the public Internet.
#################################################################

# Allow out access to my ISP's Domain name server.
# x.x.x.x must be the IP address of your ISP's DNS
# Dup these lines if your ISP has more than one DNS server
# Get the IP addresses from /etc/resolv.conf file
$cmd 020 $skip tcp from any to x.x.x.x 53 out via $pif setup keep-state

# Allow out access to my ISP's DHCP server for cable/DSL configurations.
$cmd 030 $skip udp from any to x.x.x.x 67 out via $pif keep-state

# Allow out non-secure standard www function
$cmd 040 $skip tcp from any to any 80 out via $pif setup keep-state

# Allow out secure www function https over TLS SSL
$cmd 050 $skip tcp from any to any 443 out via $pif setup keep-state

# Allow out send & get email function
$cmd 060 $skip tcp from any to any 25 out via $pif setup keep-state
$cmd 061 $skip tcp from any to any 110 out via $pif setup keep-state

# Allow out FreeBSD (make install & CVSUP) functions
# Basically give user root "GOD" privileges.
$cmd 070 $skip tcp from me to any out via $pif setup keep-state uid root

# Allow out ping
$cmd 080 $skip icmp from any to any out via $pif keep-state

# Allow out Time
$cmd 090 $skip tcp from any to any 37 out via $pif setup keep-state

# Allow out nntp news (i.e., news groups)
$cmd 100 $skip tcp from any to any 119 out via $pif setup keep-state

# Allow out secure FTP, Telnet, and SCP
# This function is using SSH (secure shell)
$cmd 110 $skip tcp from any to any 22 out via $pif setup keep-state

# Allow out whois
$cmd 120 $skip tcp from any to any 43 out via $pif setup keep-state

# Allow ntp time server
$cmd 130 $skip udp from any to any 123 out via $pif keep-state

#################################################################
# Interface facing Public Internet (Inbound Section)
# Check packets originating from the public Internet
# destined for this gateway server or the private network.
#################################################################

# Deny all inbound traffic from non-routable reserved address spaces
$cmd 300 deny all from 192.168.0.0/16  to any in via $pif  #RFC 1918 private IP
$cmd 301 deny all from 172.16.0.0/12   to any in via $pif  #RFC 1918 private IP
$cmd 302 deny all from 10.0.0.0/8      to any in via $pif  #RFC 1918 private IP
$cmd 303 deny all from 127.0.0.0/8     to any in via $pif  #loopback
$cmd 304 deny all from 0.0.0.0/8       to any in via $pif  #loopback
$cmd 305 deny all from 169.254.0.0/16  to any in via $pif  #DHCP auto-config
$cmd 306 deny all from 192.0.2.0/24    to any in via $pif  #reserved for docs
$cmd 307 deny all from 204.152.64.0/23 to any in via $pif  #Sun cluster
$cmd 308 deny all from 224.0.0.0/3     to any in via $pif  #Class D & E multicast

# Deny ident
$cmd 315 deny tcp from any to any 113 in via $pif

# Deny all Netbios service. 137=name, 138=datagram, 139=session
# Netbios is MS/Windows sharing services.
# Block MS/Windows hosts2 name server requests 81
$cmd 320 deny tcp from any to any 137 in via $pif
$cmd 321 deny tcp from any to any 138 in via $pif
$cmd 322 deny tcp from any to any 139 in via $pif
$cmd 323 deny tcp from any to any 81  in via $pif

# Deny any late arriving packets
$cmd 330 deny all from any to any frag in via $pif

# Deny ACK packets that did not match the dynamic rule table
$cmd 332 deny tcp from any to any established in via $pif

# Allow traffic in from ISP's DHCP server. This rule must contain
# the IP address of your ISP's DHCP server as it's the only
# authorized source to send this packet type.
# Only necessary for cable or DSL configurations.
# This rule is not needed for 'user ppp' type connection to
# the public Internet. This is the same IP address you captured
# and used in the outbound section.
$cmd 360 allow udp from x.x.x.x to any 68 in via $pif keep-state

# Allow in standard www function because I have Apache server
$cmd 370 allow tcp from any to me 80 in via $pif setup limit src-addr 2

# Allow in secure FTP, Telnet, and SCP from public Internet
$cmd 380 allow tcp from any to me 22 in via $pif setup limit src-addr 2

# Allow in non-secure Telnet session from public Internet
# labeled non-secure because ID & PW are passed over public
# Internet as clear text.
# Delete this sample group if you do not have telnet server enabled.
$cmd 390 allow tcp from any to me 23 in via $pif setup limit src-addr 2

# Reject & Log all unauthorized incoming connections from the public Internet
$cmd 400 deny log all from any to any in via $pif

# Reject & Log all unauthorized out going connections to the public Internet
$cmd 450 deny log all from any to any out via $pif

# This is skipto location for outbound stateful rules
$cmd 800 divert natd ip from any to any out via $pif
$cmd 801 allow ip from any to any

# Everything else is denied by default
# deny and log all packets that fell through to see what they are
$cmd 999 deny log all from any to any
################ End of IPFW rules file ###############################
```

## 第 32 章 高级网络

## 32.1. 概述

本章将就一系列与网络有关的高级话题进行讨论。

读完这章，您将了解：

*   关于网关和路由的基础知识。

*   如何配置 IEEE® 802.11 和 Bluetooth® 设备。

*   如何用 FreeBSD 做网桥。

*   如何为无盘机上配置网络启动。

*   如何配置从网络 PXE 启动一个 NFS 根文件系统。

*   如何配置网络地址转换 (NAT)。

*   如何使用 PLIP 连接两台计算机。

*   如何在运行 FreeBSD 的计算机上配置 IPv6。

*   如何配置 ATM。

*   如何利用 CARP， FreeBSD 支持的 Common Address Redundancy Protocol (共用地址冗余协议)

在读这章之前， 您应：

*   理解 `/etc/rc` 脚本的基本知识。

*   熟悉基本的网络术语。

*   了解如何配置和安装新的 FreeBSD 内核 (第 9 章 *配置 FreeBSD 的内核*)。

*   了解如何安装第三方软件 (第 5 章 *安装应用程序: Packages 和 Ports*)。

## 32.2. 网关和路由

贡献者：Coranth Gryphon.中文翻译：张 雪平 和 袁 苏义.

要让网络上的两台计算机能够相互通讯， 就必须有一种能够描述如何从一台计算机到另一台计算机的机制， 这一机制称作 *路由选择(routing)*。 “路由项” 是一对预先定义的地址： “目的地(destination)” 和 “网关(gateway)”。 这个地址对所表达的意义是， 通过 *网关* 能够完成与 *目的地* 的通信。 有三种类型的目的地址： 单个主机、 子网、 以及 “默认”。 如果没有可用的其它路由， 就会使用 “默认路由”， 有关默认路由的内容， 将在稍后的章节中进行讨论。 网关也有三种类型： 单个主机， 网络接口 (也叫 “链路 (links)”) 和以太网硬件地址 (MAC 地址)。

### 32.2.1. 实例

为了说明路由选择的各个部分， 首先来看看下面的例子。 这是 `netstat` 命令的输出：

```
% `netstat -r`
Routing tables

Destination      Gateway            Flags     Refs     Use     Netif Expire

default          outside-gw         UGSc       37      418      ppp0
localhost        localhost          UH          0      181       lo0
test0            0:e0:b5:36:cf:4f   UHLW        5    63288       ed0     77
10.20.30.255     link#1             UHLW        1     2421
example.com      link#1             UC          0        0
host1            0:e0:a8:37:8:1e    UHLW        3     4601       lo0
host2            0:e0:a8:37:8:1e    UHLW        0        5       lo0 =>
host2.example.com link#1             UC          0        0
224              link#1             UC          0        0
```

头两行给出了当前配置中的默认路由 (将在 下一节 中进行介绍) 和 `localhost (本机)` 路由。

这里的路由表中给出的用于 `localhost` 的接口 (`Netif` 列) 是 `lo0`， 也就是大家熟知的 “回环设备”。 它表示所有以此为 “目的地” 的通信都留在本机， 而不通过 LAN 发出， 因为这些流量最终会回到起点。

接着出现的是以 `0:e0:` 开头的地址。这些是以太网硬件地址，也称为 MAC 地址。 FreeBSD 会自动识别在同一个以太网中的任何主机 (如 `test0`)， 并为其新增一个路由， 并通过那个以太网接口 ── `ed0` 直接与它通讯 (译者注：那台主机)。与这类路由表相关的也有一个超时项 (`Expire`列)，当我们在指定时间内没有收到从那个主机发来的信息， 这项就派上用场了。这种情况下，到这个主机的路由就会被自动删除。 这些主机被使用一种叫做 RIP(路由信息协议--Routing Information Protocol)的机制所识别，这种机制利用基于“最短路径选择 (shortest path determination)”的办法计算出到本地主机的路由。

FreeBSD 也会为本地子网添加子网路由(`10.20.30.255` 是子网 `10.20.30` 的广播地址，而 `example.com` 是这个子网相联的域名)。 名称 `link#1` 代表主机上的第一块以太网卡。 您会发现，对于它们没有指定另外的接口。

这两个组(本地网络主机和本地子网)的路由是由守护进程 routed 自动配置的。如果它没有运行， 那就只有被静态定义 (例如，明确输入的) 的路由才存在了。

`host1` 行代表我们的主机，它通过以太网地址来识别。 因为我们是发送端，FreeBSD 知道使用回环接口 (`lo0`) 而不是通过以太网接口来进行发送。

两个 `host2` 行是我们使用 [ifconfig(8)](http://www.FreeBSD.org/cgi/man.cgi?query=ifconfig&sektion=8&manpath=freebsd-release-ports) 别名 (请看关于以太网的那部分就会知道我们为什么这么做) 时产生的一个实例。在 `lo0` 接口之后的 `=>` 符号表明我们不仅使用了回环 (因为这个地址也涉及了本地主机)，而且明确指出它是个别名。 这类路由只有在支持别名的主机上才能显现出来。 所有本地网上的其它的主机对于这类路由只会简单拥有 `link#1`。

最后一行 (目标子网`224`) 用于处理多播――它会覆盖到其它的区域。

最后，每个路由的不同属性可以在 `Flags` 列中看到。下边是个关于这些标志和它们的含义的一个简表：

| U | Up: 路由处于活动状态。 |
| H | Host: 路由目标是单个主机。 |
| G | Gateway: 所有发到目的地的网络传到这一远程系统上， 并由它决定最后发到哪里。 |
| S | Static: 这个路由是手工配置的，不是由系统自动生成的。 |
| C | Clone: 生成一个新的路由， 通过这个路由我们可以连接上这些机子。 这种类型的路由通常用于本地网络。 |
| W | WasCloned: 指明一个路由――它是基于本地区域网络 (克隆) 路由自动配置的。 |
| L | Link: 路由涉及到了以太网硬件。 |

### 32.2.2. 默认路由

当本地系统需要与远程主机建立连接时， 它会检查路由表以决定是否有已知的路径存在。 如果远程主机属于一个我们已知如何到达 (克隆的路由) 的子网内，那么系统会检查看沿着那个接口是否能够连接。

如果所有已知路径都失败，系统还有最后一个选择： “默认”路由。这个路由是特殊类型的网关路由 (通常只有一个存在于系统里)，并且总是在标志栏使用一个 `c`来进行标识。对于本地区域网络里的主机， 这个网关被设置到任何与外界有直接连接的机子里 (无论是通过 PPP、DSL、cable modem、T1 或其它的网络接口连接)。

如果您正为某台本身就做为网关连接外界的机子配置默认路由的话， 那么该默认路由应该是您的“互联网服务商 (ISP)”那方的网关机子。

让我们来看一个关于默认路由的例子。这是个很普遍的配置：

![](img/net-routing.png)

主机 `Local1` 和 `Local2` 在您那边。`Local1` 通过 PPP 拨号连接到了 ISP。这个 PPP 服务器通过一个局域网连接到另一台网关机子――它又通过一个外部接口连接到 ISP 提供的互联网上。

您的每一台机子的默认路由应该是：

| Host | Default Gateway | Interface |
| --- | --- | --- |
| Local2 | Local1 | Ethernet |
| Local1 | T1-GW | PPP |

一个常见的问题是“我们为什么 (或怎样) 能将 `T1-GW` 设置成为 `Local1` 默认网关，而不是它所连接 ISP 服务器？”

记住，因为 PPP 接口使用的一个地址是在 ISP 的局域网里的，用于您那边的连接，对于 ISP 的局域网里的其它机子，其路由会自动产生。 因此，您就已经知道了如何到达机子 `T1-GW`， 那么也就没必要中间那一步了――发送通信给 ISP 服务器。

通常使用地址 `X.X.X.1` 做为一个局域网的网关。 因此 (使用相同的例子)，如果您本地的 C 类地址空间是 `10.20.30`，而您的 ISP 使用的是 `10.9.9`， 那么默认路由表将是：

| Host | Default Route |
| --- | --- |
| Local2 (10.20.30.2) | Local1 (10.20.30.1) |
| Local1 (10.20.30.1, 10.9.9.30) | T1-GW (10.9.9.1) |

您可以很轻易地通过 `/etc/rc.conf` 文件设定默认路由。在我们的实例里，在主机 `Local2` 里，我们在文件 `/etc/rc.conf` 里增加了下边内容：

```
defaultrouter="10.20.30.1"
```

也可以直接在命令行使用 [route(8)](http://www.FreeBSD.org/cgi/man.cgi?query=route&sektion=8&manpath=freebsd-release-ports) 命令:

```
# `route add default 10.20.30.1`
```

要了解关于如何手工维护网络路由表的进一步细节， 请参考 [route(8)](http://www.FreeBSD.org/cgi/man.cgi?query=route&sektion=8&manpath=freebsd-release-ports) 联机手册。

### 32.2.3. 重宿主机(Dual Homed Hosts)

还有一种其它的类型的配置是我们要提及的， 这就是一个主机处于两个不同的网络。技术上，任何作为网关 (上边的实例中，使用了 PPP 连接) 的机子就算作是重宿主机。 但这个词实际上仅用来指那种处于两个局域网之中的机子。

有一种情形，一台机子有两个网卡， 对于各个子网都有各自的一个地址。另一种情况， 这台机子仅有一张网卡，但使用 [ifconfig(8)](http://www.FreeBSD.org/cgi/man.cgi?query=ifconfig&sektion=8&manpath=freebsd-release-ports) 做了别名。如果有两个独立的以太网在使用的情形就使用前者， 如果只有一个物理网段，但逻辑上分成了两个独立的子网， 就使用后者。

每种情况都要设置路由表以便两子网都知道这台主机是到其它子网的网关――入站路由 (inbound route)。将一台主机配置成两个子网间的路由器， 这种配置经常在我们需要实现单向或双向的包过滤或防火墙时被用到。

如果想让主机在两个接口间转发数据包，您需要激活 FreBSD 的这项功能。至于怎么做，请看下一部分了解更多。

### 32.2.4. 建立路由器

网络路由器只是一个将数据包从一个接口转发到另一个接口的系统。 互联网标准和良好的工程实践阻止了 FreeBSD 计划在 FreeBSD 中把它置成默认值。您在可以在 [rc.conf(5)](http://www.FreeBSD.org/cgi/man.cgi?query=rc.conf&sektion=5&manpath=freebsd-release-ports) 中改变下列变量的值为 `YES`，使这个功能生效：

```
gateway_enable="YES"          # Set to YES if this host will be a gateway
```

这个选项会把[sysctl(8)](http://www.FreeBSD.org/cgi/man.cgi?query=sysctl&sektion=8&manpath=freebsd-release-ports) 变量――`net.inet.ip.forwarding` 设置成 `1`。如果您要临时地停止路由， 您可以把它重设为 `0`。

新的路由器需要有路由才知道将数据传向何处。 如果网络够简单，您可以使用静态路由。FreeBSD 也自带一个标准的 BSD 路由选择守护进程 [routed(8)](http://www.FreeBSD.org/cgi/man.cgi?query=routed&sektion=8&manpath=freebsd-release-ports)， 称之为 RIP ( version 1 和 version 2) 和 IRDP。对 BGP v4，OSPF v2 和其它复杂路由选择协议的支持可以从 [net/zebra](http://www.freebsd.org/cgi/url.cgi?ports/net/zebra/pkg-descr) 包中得到。 像 GateD® 一样的商业产品也提供了更复杂的网络路由解决方案。

### 32.2.5. 设置静态路由

贡献者：Coranth Gryphon.中文翻译：张 雪平 和 袁 苏义.

#### 32.2.5.1. 手动配置

假设如下这样一个网络：

![](img/static-routes.png)

在这里，`RouterA` 是我们的 FreeBSD 机子，它充当连接到互联网其它部分的路由器的角色。 默认路由设置为`10.0.0.1`， 它就允许与外界连接。我们假定已经正确配置了 `RouterB`，并且知道如何连接到想去的任何地方。 (在这个图里很简单。只须在 `RouterB` 上增加默认路由，使用 `192.168.1.1` 做为网关。)

如果我们查看一下`RouterA`的路由表， 我们就会看到如下一些内容：

```
% `netstat -nr`
Routing tables

Internet:
Destination        Gateway            Flags    Refs      Use  Netif  Expire
default            10.0.0.1           UGS         0    49378    xl0
127.0.0.1          127.0.0.1          UH          0        6    lo0
10.0.0.0/24        link#1             UC          0        0    xl0
192.168.1.0/24     link#2             UC          0        0    xl1
```

使用当前的路由表，`RouterA` 是不能到达我们的内网――Internal Net 2 的。它没有到 `192.168.2.0/24` 的路由。 一种可以接受的方法是手工增加这条路由。以下的命令会把 Internal Net 2 网络加入到 `RouterA` 的路由表中，使用`192.168.1.2` 做为下一个跳跃：

```
# `route add -net 192.168.2.0/24 192.168.1.2`
```

现在 `RouterA` 就可以到达 `192.168.2.0/24` 网络上的任何主机了。

#### 32.2.5.2. 永久配置

上面的实例对于运行着的系统来说配置静态路由是相当不错了。 只是，有一个问题――如果您重启您的 FreeBSD 机子，路由信息就会消失。 处理附加的静态路由的方法是把它放到您的 `/etc/rc.conf` 文件里去。

```
# Add Internal Net 2 as a static route
static_routes="internalnet2"
route_internalnet2="-net 192.168.2.0/24 192.168.1.2"
```

配置变量 `static_routes` 是一串以空格隔开的字符串。每一串表示一个路由名字。 在上面的例子中我们中有一个串在 `static_routes` 里。这个字符串中 *`internalnet2`*。 然后我们新增一个配置变量 `route_internalnet2`， 这里我们把所有传给 [route(8)](http://www.FreeBSD.org/cgi/man.cgi?query=route&sektion=8&manpath=freebsd-release-ports)命令的参数拿了过来。 在上面的实例中的我使用的命令是：

```
# `route add -net 192.168.2.0/24 192.168.1.2`
```

因此，我们需要的是 `"-net 192.168.2.0/24 192.168.1.2"`。

前边已经提到， 可以把多个静态路由的名称， 放到 `static_routes` 里边。 接着我们就来建立多个静态路由。 下面几行所展示的， 是在一个假想的路由器上增加 `192.168.0.0/24` 和 `192.168.1.0/24` 之间静态路由的例子：

```
static_routes="net1 net2"
route_net1="-net 192.168.0.0/24 192.168.0.1"
route_net2="-net 192.168.1.0/24 192.168.1.1"
```

### 32.2.6. 路由传播

我们已经讨论了如何定义通向外界的路由， 但未谈及外界是如何找到我们的。

我们已经知道可以设置路由表， 这样任何指向特定地址空间 (在我们的例子中是一个 C 类子网) 的数据都会被送往网络上特定的主机， 然后由这台主机向地址空间内部转发数据。

当您得到一个分配给您的网络的地址空间时， ISP(网络服务商)会设置它们的路由表， 这样指向您子网的数据就会通过 PPP 连接下传到您的网络。 但是其它跨越国界的网络是如何知道将数据传给您的 ISP 的呢？

有一个系统(很像分布式 DNS 信息系统)， 它一直跟踪被分配的地址空间， 并说明它们连接到互联网骨干(Internet backbone)的点。 “骨干(Backbone)” 指的是负责全世界和跨国的传输的主要干线。 每一台骨干主机(backbone machine)有一份主要表集的副本， 它将发送给特定网络的数据导向相应的骨干载体上(backbone carrier)， 从结点往下遍历服务提供商链，直到数据到达您的网络。

服务提供商的任务是向骨干网络广播，以声明它们就是通向您的网点的连接结点 (以及进入的路径)。这就是路由传播。

### 32.2.7. 问题解答

有时候，路由传播会有一个问题，一些网络无法与您连接。 或许能帮您找出路由是在哪里中断的最有用的命令就是 [traceroute(8)](http://www.FreeBSD.org/cgi/man.cgi?query=traceroute&sektion=8&manpath=freebsd-release-ports)了。当您无法与远程主机连接时， 这个命令一样有用(例如 [ping(8)](http://www.FreeBSD.org/cgi/man.cgi?query=ping&sektion=8&manpath=freebsd-release-ports) 失败)。

[traceroute(8)](http://www.FreeBSD.org/cgi/man.cgi?query=traceroute&sektion=8&manpath=freebsd-release-ports) 命令将以您想连接的主机的名字作为参数执行。 不管是到达了目标，还是因为没有连接而终止， 它都会显示所经过的所有网关主机。

想了解更多的信息，查看 [traceroute(8)](http://www.FreeBSD.org/cgi/man.cgi?query=traceroute&sektion=8&manpath=freebsd-release-ports) 的手册。

### 32.2.8. 多播路由

FreeBSD 一开始就支持多播应用软件和多播路由选择。 多播程序并不要求 FreeBSD 的任何特殊的配置， 就可以工作得很好。多播路由需要支持被编译入内核：

```
options MROUTING
```

另外，多播路由守护进程――[mrouted(8)](http://www.FreeBSD.org/cgi/man.cgi?query=mrouted&sektion=8&manpath=freebsd-release-ports) 必须通过 `/etc/mrouted.conf` 配置来开启通道和 DVMRP。 更多关于多播路由配置的信息可以在 [mrouted(8)](http://www.FreeBSD.org/cgi/man.cgi?query=mrouted&sektion=8&manpath=freebsd-release-ports) 的手册里找到。

### 注意:

多播路由服务 [mrouted(8)](http://www.FreeBSD.org/cgi/man.cgi?query=mrouted&sektion=8&manpath=freebsd-release-ports) 实现了 DVMRP 多播路由协议， 在许多采用多播的场合， 它已被 [pim(4)](http://www.FreeBSD.org/cgi/man.cgi?query=pim&sektion=4&manpath=freebsd-release-ports) 取代。 [mrouted(8)](http://www.FreeBSD.org/cgi/man.cgi?query=mrouted&sektion=8&manpath=freebsd-release-ports) 以及相关的 [map-mbone(8)](http://www.FreeBSD.org/cgi/man.cgi?query=map-mbone&sektion=8&manpath=freebsd-release-ports) 和 [mrinfo(8)](http://www.FreeBSD.org/cgi/man.cgi?query=mrinfo&sektion=8&manpath=freebsd-release-ports) 工具可以在 FreeBSD 的 Ports Collection [net/mrouted](http://www.freebsd.org/cgi/url.cgi?ports/net/mrouted/pkg-descr) 中找到。

## 32.3. 无线网络

陈福康, Marc Fonvieille 和 Murray Stokely.

### 32.3.1. 无线网络基础

绝大多数无线网络都采用了 IEEE® 802.11 标准。 基本的无线网络中， 都包含多个以 2.4GHz 或 5GHz 频段的无线电波广播的站点 (不过， 随所处地域的不同， 或者为了能够更好地进行通讯， 具体的频率会在 2.3GHz 和 4.9GHz 的范围内变化)。

802.11 网络有两种组织方式： 在 *infrastructure 模式* 中， 一个通讯站作为主站， 其他通讯站都与其关联； 这种网络称为 BSS， 而主站则成为无线访问点 (AP)。 在 BSS 中， 所有的通讯都是通过 AP 来完成的； 即使通讯站之间要相互通讯， 也必须将消息发给 AP。 在第二种形式的网络中， 并不存在主站， 通讯站之间是直接通讯的。 这种网络形式称作 IBSS， 通常也叫做 *ad-hoc 网络*。

802.11 网络最初在 2.4GHz 频段上部署， 并采用了由 IEEE® 802.11 和 802.11b 标准所定义的协议。 这些标准定义了采用的操作频率、 包括分帧和传输速率 (通讯过程中可以使用不同的速率) 在内的 MAC 层特性等。 稍后的 802.11a 标准定义了使用 5GHz 频段进行操作， 以及不同的信号机制和更高的传输速率。 其后定义的 802.11g 标准启用了在 2.4GHz 上如何使用 802.11a 信号和传输机制， 以提供对较早的 802.11b 网络的向前兼容。

802.11 网络中采用的各类底层传输机制提供了不同类型的安全机制。 最初的 802.11 标准定义了一种称为 WEP 的简单安全协议。 这个协议采用固定的预发布密钥， 并使用 RC4 加密算法来对在网络上传输的数据进行编码。 全部通讯站都必须采用同样的固定密钥才能通讯。 这一格局已经被证明很容易被攻破， 因此目前已经很少使用了， 采用这种方法只能让那些接入网络的用户迅速断开。 最新的安全实践是由 IEEE® 802.11i 标准给出的， 它定义了新的加密算法， 并通过一种附加的协议来让通讯站向无线访问点验证身份， 并交换用于进行数据通讯的密钥。 更进一步， 用于加密的密钥会定期地刷新， 而且有机制能够监测入侵的尝试 (并阻止这种尝试)。 无线网络中另一种常用的安全协议标准是 WPA。 这是在 802.11i 之前由业界组织定义的一种过渡性标准。 WPA 定义了在 802.11i 中所规定的要求的子集， 并被设计用来在旧式硬件上实施。 特别地， WPA 要求只使用由最初 WEP 所采用的算法派生的 TKIP 加密算法。 802.11i 则不但允许使用 TKIP， 而且还要求支持更强的加密算法 AES-CCM 来用于加密数据。 (在 WPA 中并没有要求使用 AES 加密算法， 因为在旧式硬件上实施这种算法时所需的计算复杂性太高。)

除了前面介绍的那些协议标准之外， 还有一种需要介绍的标准是 802.11e。 它定义了用于在 802.11 网络上运行多媒体应用， 如视频流和使用 IP 传送的语音 (VoIP) 的协议。 与 802.11i 类似， 802.11e 也有一个前身标准， 通常称作 WME (后改名为 WMM)， 它也是由业界组织定义的 802.11e 的子集， 以便能够在旧式硬件中使用多媒体应用。 关于 802.11e 与 WME/WMM 之间的另一项重要区别是， 前者允许对流量通过服务品质 (QoS) 协议和增强媒体访问协议来安排优先级。 对于这些协议的正确实现， 能够实现高速突发数据和流量分级。

FreeBSD 支持采用 802.11a, 802.11b 和 802.11g 的网络。 类似地， 它也支持 WPA 和 802.11i 安全协议 (与 11a、 11b 和 11g 配合)， 而 WME/WMM 所需要的 QoS 和流量分级， 则在部分无线设备上提供了支持。

### 32.3.2. 基本安装

#### 32.3.2.1. 内核配置

要使用无线网络， 您需要一块无线网卡， 并适当地配置内核令其提供无线网络支持。 后者被分成了多个模块， 因此您只需配置使用您所需要的软件就可以了。

首先您需要的是一个无线设备。 最为常用的一种无线配件是 Atheros 生产的。 这些设备由 [ath(4)](http://www.FreeBSD.org/cgi/man.cgi?query=ath&sektion=4&manpath=freebsd-release-ports) 驱动程序提供支持， 您需要把下面的配置加入到 `/boot/loader.conf` 文件中：

```
if_ath_load="YES"
```

Atheros 驱动分为三个部分： 驱动部分 ([ath(4)](http://www.FreeBSD.org/cgi/man.cgi?query=ath&sektion=4&manpath=freebsd-release-ports))、 用于处理芯片专有功能的支持层 ([ath_hal(4)](http://www.FreeBSD.org/cgi/man.cgi?query=ath_hal&sektion=4&manpath=freebsd-release-ports))， 以及一组用以选择传输帧速率的算法 (ath_rate_sample here)。 当以模块方式加载这一支持时， 所需的其它模块会自动加载。 如果您使用的不是 Atheros 设备， 则应选择对应的模块； 例如：

```
if_wi_load="YES"
```

表示使用基于 Intersil Prism 产品的无线设备 ([wi(4)](http://www.FreeBSD.org/cgi/man.cgi?query=wi&sektion=4&manpath=freebsd-release-ports) 驱动)。

### 注意:

在这篇文档余下的部分中， 我们将以 [ath(4)](http://www.FreeBSD.org/cgi/man.cgi?query=ath&sektion=4&manpath=freebsd-release-ports) 卡来进行示范， 如果要套用这些配置的话， 可能需要根据您实际的配置情况来修改示例中的设备名称。 在 FreeBSD 兼容硬件说明中提供了目前可用的无线网络驱动， 以及兼容硬件的列表。 针对不同版本和硬件平台的说明可以在 FreeBSD 网站的 [Release Information](http://www.FreeBSD.org/releases/index.html) 页面找到。 如果您的无线设备没有与之对应的 FreeBSD 专用驱动程序， 也可以尝试使用 NDIS 驱动封装机制来直接使用 Windows® 驱动。

对于 FreeBSD 7.X， 在配置好设备驱动之后， 您还需要引入驱动程序所需要的 802.11 网络支持。 对于 [ath(4)](http://www.FreeBSD.org/cgi/man.cgi?query=ath&sektion=4&manpath=freebsd-release-ports) 驱动而言， 至少需要 [wlan(4)](http://www.FreeBSD.org/cgi/man.cgi?query=wlan&sektion=4&manpath=freebsd-release-ports) `wlan_scan_ap` 和 `wlan_scan_sta` 模块； [wlan(4)](http://www.FreeBSD.org/cgi/man.cgi?query=wlan&sektion=4&manpath=freebsd-release-ports) 模块会自动随无线设备驱动一同加载， 剩下的模块必须要在系统引导时加载， 就需要在 `/boot/loader.conf` 中加入下面的配置：

```
wlan_scan_ap_load="YES"
wlan_scan_sta_load="YES"
```

从 FreeBSD 8.0 起， 这些模块成为了 [wlan(4)](http://www.FreeBSD.org/cgi/man.cgi?query=wlan&sektion=4&manpath=freebsd-release-ports) 驱动的基础组件， 并会随适配器驱动一起动态加载。

除此之外， 您还需要提供您希望使用的安全协议所需的加密支持模块。 这些模块是设计来让 [wlan(4)](http://www.FreeBSD.org/cgi/man.cgi?query=wlan&sektion=4&manpath=freebsd-release-ports) 模块根据需要自动加载的， 但目前还必须手工进行配置。 您可以使用下面这些模块： [wlan_wep(4)](http://www.FreeBSD.org/cgi/man.cgi?query=wlan_wep&sektion=4&manpath=freebsd-release-ports)、 [wlan_ccmp(4)](http://www.FreeBSD.org/cgi/man.cgi?query=wlan_ccmp&sektion=4&manpath=freebsd-release-ports) 和 [wlan_tkip(4)](http://www.FreeBSD.org/cgi/man.cgi?query=wlan_tkip&sektion=4&manpath=freebsd-release-ports)。 [wlan_ccmp(4)](http://www.FreeBSD.org/cgi/man.cgi?query=wlan_ccmp&sektion=4&manpath=freebsd-release-ports) 和 [wlan_tkip(4)](http://www.FreeBSD.org/cgi/man.cgi?query=wlan_tkip&sektion=4&manpath=freebsd-release-ports) 这两个驱动都只有在您希望采用 WPA 和/或 802.11i 安全协议时才需要。 如果您的网络不采用加密， 就不需要 [wlan_wep(4)](http://www.FreeBSD.org/cgi/man.cgi?query=wlan_wep&sektion=4&manpath=freebsd-release-ports) 支持了。 要在系统引导时加载这些模块， 需要在 `/boot/loader.conf` 中加入下面的配置：

```
wlan_wep_load="YES"
wlan_ccmp_load="YES"
wlan_tkip_load="YES"
```

通过系统引导配置文件 (也就是 `/boot/loader.conf`) 中的这些信息生效， 您必须重新启动运行 FreeBSD 的计算机。 如果不想立刻重新启动， 也可以使用 [kldload(8)](http://www.FreeBSD.org/cgi/man.cgi?query=kldload&sektion=8&manpath=freebsd-release-ports) 来手工加载。

### 注意:

如果不想加载模块， 也可以将这些驱动编译到内核中， 方法是在内核的编译配置文件中加入下面的配置：

```
device wlan              # 802.11 support
device wlan_wep          # 802.11 WEP support
device wlan_ccmp         # 802.11 CCMP support
device wlan_tkip         # 802.11 TKIP support
device wlan_amrr         # AMRR transmit rate control algorithm
device ath               # Atheros pci/cardbus NIC's
device ath_hal           # pci/cardbus chip support
options AH_SUPPORT_AR5416 # enable AR5416 tx/rx descriptors
device ath_rate_sample   # SampleRate tx rate control for ath
```

使用 FreeBSD 7.X 时， 还需要配置下面这两行； FreeBSD 的其他版本不需要它们。

```
device wlan_scan_ap      # 802.11 AP mode scanning
device wlan_scan_sta     # 802.11 STA mode scanning
```

将这些信息写到内核编译配置文件中之后， 您需要重新编译内核， 并重新启动运行 FreeBSD 的计算机。

在系统启动之后， 您会在引导时给出的信息中， 找到类似下面这样的关于无线设备的信息：

```
ath0: <Atheros 5212> mem 0x88000000-0x8800ffff irq 11 at device 0.0 on cardbus1
ath0: [ITHREAD]
ath0: AR2413 mac 7.9 RF2413 phy 4.5
```

### 32.3.3. Infrastructure 模式

通常的情形中使用的是 infrastructure 模式或称 BSS 模式。 在这种模式中， 有一系列无线访问点接入了有线网络。 每个无线网都会有自己的名字， 这个名字称作网络的 SSID。 无线客户端都通过无线访问点来完成接入。

#### 32.3.3.1. FreeBSD 客户机

##### 32.3.3.1.1. 如何查找无线访问点

您可以通过使用 `ifconfig` 命令来扫描网络。 由于系统需要在操作过程中切换不同的无线频率并探测可用的无线访问点， 这种请求可能需要数分钟才能完成。 只有超级用户才能启动这种扫描：

```
# `ifconfig wlan0 create wlandev ath0`
# `ifconfig wlan0 up scan`
SSID/MESH ID    BSSID              CHAN RATE   S:N     INT CAPS
dlinkap         00:13:46:49:41:76   11   54M -90:96   100 EPS  WPA WME
freebsdap       00:11:95:c3:0d:ac    1   54M -83:96   100 EPS  WPA
```

### 注意:

在开始扫描之前， 必须将网络接口设为 `up`。 后续的扫描请求就不需要再将网络接口设为 up 了。

### 注意:

在 FreeBSD 7.X 中， 会直接适配器设备， 例如 `ath0`， 而不是 `wlan0` 设备。 因此您需要把前面的命令行改为：

```
# `ifconfig ath0 up scan`
```

在这份文档余下的部分中， 您也需要注意 FreeBSD 7.X 上的这些差异， 并对命令行示例进行类似的改动。

扫描会列出所请求到的所有 BSS/IBSS 网络列表。 除了网络的名字 `SSID` 之外， 我们还会看到 `BSSID` 即无线访问点的 MAC 地址。 而 `CAPS` 字段则给出了网络类型及其提供的功能， 其中包括：

表 32.1. 通讯站功能代码

| 功能代码 | 含义 |
| --- | --- |
| `E` | Extended Service Set (ESS)。 表示通讯站是 infrastructure 网络 (相对于 IBSS/ad-hoc 网络) 的成员。 |
| `I` | IBSS/ad-hoc 网络。 表示通讯站是 ad-hoc 网络 (相对于 ESS 网络) 的成员。 |
| `P` | 私密。 在 BSS 中交换的全部数据帧均需保证数据保密性。 这表示 BSS 需要通讯站使用加密算法， 例如 WEP、 TKIP 或 AES-CCMP 来加密/解密与其他通讯站交换的数据帧。 |
| `S` | 短前导码 (Short Preamble)。 表示网络采用的是短前导码 (由 802.11b High Rate/DSSS PHY 定义， 短前导码采用 56-位 同步字段， 而不是在长前导码模式中所采用的 128-位 字段)。 |
| `s` | 短碰撞槽时间 (Short slot time)。 表示由于不存在旧式 (802.11b) 通讯站， 802.11g 网络正使用短碰撞槽时间。 |

要显示目前已知的网络， 可以使用下面的命令：

```
# `ifconfig wlan0 list scan`
```

这些信息可能会由无线适配器自动更新， 也可使用 `scan` 手动更新。 快取缓存中的旧数据会自动删除， 因此除非进行更多扫描， 这个列表会逐渐缩小。

##### 32.3.3.1.2. 基本配置

在这一节中我们将展示一个简单的例子来介绍如何让无线网络适配器在 FreeBSD 中以不加密的方式工作。 在您熟悉了这些概念之后， 我们强烈建议您在实际的使用中采用 WPA 来配置网络。

配置无线网络的过程可分为三个基本步骤： 选择无线访问点、 验证您的通讯站身份， 以及配置 IP 地址。 下面的几节中将分步骤地介绍它们。

###### 32.3.3.1.2.1. 选择无线访问点

多数时候让系统以内建的探测方式选择无线访问点就可以了。 这是在您将网络接口置为 up 或在 `/etc/rc.conf` 中配置 IP 地址时的默认方式， 例如：

```
wlans_ath0="wlan0"
ifconfig_wlan0="DHCP"
```

### 注意:

如前面提到的那样， FreeBSD 7.X 只需要一行配置：

```
ifconfig_ath0="DHCP"
```

如果存在多个无线访问点， 而您希望从中选择具体的一个， 则可以通过指定 SSID 来实现：

```
wlans_ath0="wlan0"
ifconfig_wlan0="ssid *`your_ssid_here`* DHCP"
```

在某些环境中， 多个访问点可能会使用同样的 SSID (通常， 这样做的目的是简化漫游)， 这时可能就需要与某个具体的设备关联了。 这种情况下， 您还应指定无线访问点的 BSSID (这时可以不指定 SSID)：

```
wlans_ath0="wlan0"
ifconfig_wlan0="ssid *`your_ssid_here`* bssid *`xx:xx:xx:xx:xx:xx`* DHCP"
```

除此之外， 还有一些其它的方法能够约束查找无线访问点的范围， 例如限制系统扫描的频段， 等等。 如果您的无线网卡支持多个频段， 这样做可能会非常有用， 因为扫描全部可用频段是一个十分耗时的过程。 要将操作限制在某个具体的频段， 可以使用 `mode` 参数； 例如：

```
wlans_ath0="wlan0"
ifconfig_wlan0="mode *`11g`* ssid *`your_ssid_here`* DHCP"
```

就会强制卡使用采用 2.4GHz 的 802.11g， 这样在扫描的时候， 就不会考虑那些 5GHz 的频段了。 除此之外， 还可以通过 `channel` 参数来将操作锁定在特定频率， 以及通过 `chanlist` 参数来指定扫描的频段列表。 关于这些参数的进一步信息， 可以在联机手册 [ifconfig(8)](http://www.FreeBSD.org/cgi/man.cgi?query=ifconfig&sektion=8&manpath=freebsd-release-ports) 中找到。

###### 32.3.3.1.2.2. 验证身份

一旦您选定了无线访问点， 您的通讯站就需要完成身份验证， 以便开始发送和接收数据。 身份验证可以通过许多方式进行， 最常用的一种方式称为开放式验证， 它允许任意通讯站加入网络并相互通信。 这种验证方式只应在您第一次配置无线网络进行测试时使用。 其它的验证方式则需要在进行数据通讯之前， 首先进行密钥协商握手； 这些方式要么使用预先分发的密钥或密码， 要么是用更复杂一些的后台服务， 如 RADIUS。 绝大多数用户会使用默认的开放式验证， 而第二多的则是 WPA-PSK， 它也称为个人 WPA， 在 下面 的章节中将进行介绍。

### 注意:

如果您使用 Apple® AirPort® Extreme 基站作为无线访问点， 则可能需要同时在两端配置 WEP 共享密钥验证。 这可以通过在 `/etc/rc.conf` 文件中进行设置， 或使用 [wpa_supplicant(8)](http://www.FreeBSD.org/cgi/man.cgi?query=wpa_supplicant&sektion=8&manpath=freebsd-release-ports) 程序来手工完成。 如果您只有一个 AirPort® 基站， 则可以用类似下面的方法来配置：

```
wlans_ath0="wlan0"
ifconfig_wlan0="authmode shared wepmode on weptxkey *`1`* wepkey *`01234567`* DHCP"
```

一般而言， 应尽量避免使用共享密钥这种验证方法， 因为它以非常受限的方式使用 WEP 密钥， 使得攻击者能够很容易地破解密钥。 如果必须使用 WEP (例如， 为了兼容旧式的设备) 最好使用 WEP 配合 `open` 验证方式。 关于 WEP 的更多资料请参见 第 32.3.3.1.4 节 “WEP”。

###### 32.3.3.1.2.3. 通过 DHCP 获取 IP 地址

在您选定了无线访问点， 并配置了验证参数之后， 还必须获得 IP 地址才能真正开始通讯。 多数时候， 您会通过 DHCP 来获得无线 IP 地址。 要达到这个目的， 需要编辑 `/etc/rc.conf` 并在配置中加入 `DHCP`：

```
wlans_ath0="wlan0"
ifconfig_wlan0="DHCP"
```

现在您已经完成了启用无线网络接口的全部准备工作了， 下面的操作将启用它：

```
# `/etc/rc.d/netif start`
```

一旦网络接口开始运行， 就可以使用 `ifconfig` 来查看网络接口 `ath0` 的状态了：

```
# `ifconfig wlan0`
wlan0: flags=8843<UP,BROADCAST,RUNNING,SIMPLEX,MULTICAST> mtu 1500
        ether 00:11:95:d5:43:62
        inet 192.168.1.100 netmask 0xffffff00 broadcast 192.168.1.255
        media: IEEE 802.11 Wireless Ethernet OFDM/54Mbps mode 11g
        status: associated
        ssid dlinkap channel 11 (2462 Mhz 11g) bssid 00:13:46:49:41:76
        country US ecm authmode OPEN privacy OFF txpower 21.5 bmiss 7
        scanvalid 60 bgscan bgscanintvl 300 bgscanidle 250 roam:rssi 7
        roam:rate 5 protmode CTS wme burst
```

这里的 `status: associated` 表示您已经连接到了无线网络 (在这个例子中， 这个网络的名字是 `dlinkap`)。 `bssid 00:13:46:49:41:76` 是指您所用无线访问点的 MAC 地址； `authmode OPEN` 表示您通讯的内容将将不加密。

###### 32.3.3.1.2.4. 静态 IP 地址

如果无法从某个 DHCP 服务器获得 IP 地址， 则可以配置一个静态 IP 地址， 方法是将前面的 `DHCP` 关键字替换为地址信息。 请务必保持其他用于连接无线访问点的参数：

```
wlans_ath0="wlan0"
ifconfig_wlan0="inet *`192.168.1.100`* netmask *`255.255.255.0`* ssid *`your_ssid_here`*"
```

##### 32.3.3.1.3. WPA

WPA (Wi-Fi 保护访问) 是一种与 802.11 网络配合使用的安全协议， 其目的是消除 WEP 中缺少身份验证能力的问题， 以及一些其它的安全弱点。 WPA 采用了 802.1X 认证协议， 并采用从多种与 WEP 不同的加密算法中选择一种来保证数据保密性。 WPA 支持的唯一一种加密算法是 TKIP (临时密钥完整性协议)， TKIP 是一种对 WEP 所采用的基本 RC4 加密算法的扩展， 除此之外还提供了对检测到的入侵的响应机制。 TKIP 被设计用来与旧式硬件一同工作， 只需要进行部分软件修改； 它提供了一种改善安全性的折衷方案， 但仍有可能受到攻击。 WPA 也指定了 AES-CCMP 加密作为 TKIP 的替代品， 在可能时倾向于使用这种加密； 表达这一规范的常用术语是 WPA2 (或 RSN)。

WPA 定义了验证和加密协议。 验证通常是使用两种方法之一来完成的： 通过 802.1X 或类似 RADIUS 这样的后端验证服务， 或通过在通讯站和无线访问点之间通过事先分发的密码来进行最小握手。 前一种通常称作企业 WPA， 而后者通常也叫做个人 WPA。 因为多数人不会为无线网络配置 RADIUS 后端服务器， 因此 WPA-PSK 是在 WPA 中最为常见的一种。

对无线连接的控制和身份验证工作 (密钥协商或通过服务器验证) 是通过 [wpa_supplicant(8)](http://www.FreeBSD.org/cgi/man.cgi?query=wpa_supplicant&sektion=8&manpath=freebsd-release-ports) 工具来完成的。 这个程序运行时需要一个配置文件， `/etc/wpa_supplicant.conf`。 关于这个文件的更多信息， 请参考联机手册 [wpa_supplicant.conf(5)](http://www.FreeBSD.org/cgi/man.cgi?query=wpa_supplicant.conf&sektion=5&manpath=freebsd-release-ports)。

###### 32.3.3.1.3.1. WPA-PSK

WPA-PSK 也称作 个人-WPA， 它基于预先分发的密钥 (PSK)， 这个密钥是根据作为无线网络上使用的主密钥的密码生成的。 这表示每个无线用户都会使用同样的密钥。 WPA-PSK 主要用于小型网络， 在这种网络中， 通常不需要或没有办法架设验证服务器。

### 警告:

无论何时， 都应使用足够长， 且包括尽可能多字母和数字的强口令， 以免被猜出和/或攻击。

第一步是修改配置文件 `/etc/wpa_supplicant.conf`， 并在其中加入在您网络上使用的 SSID 和事先分发的密钥：

```
network={
  ssid="freebsdap"
  psk="freebsdmall"
}
```

接下来， 在 `/etc/rc.conf` 中， 我们将指定无线设备的配置， 令其采用 WPA， 并通过 DHCP 来获取 IP 地址：

```
wlans_ath0="wlan0"
ifconfig_wlan0="WPA DHCP"
```

下面启用无线网络接口：

```
# `/etc/rc.d/netif start`
Starting wpa_supplicant.
DHCPDISCOVER on wlan0 to 255.255.255.255 port 67 interval 5
DHCPDISCOVER on wlan0 to 255.255.255.255 port 67 interval 6
DHCPOFFER from 192.168.0.1
DHCPREQUEST on wlan0 to 255.255.255.255 port 67
DHCPACK from 192.168.0.1
bound to 192.168.0.254 -- renewal in 300 seconds.
wlan0: flags=8843<UP,BROADCAST,RUNNING,SIMPLEX,MULTICAST> mtu 1500
      ether 00:11:95:d5:43:62
      inet 192.168.0.254 netmask 0xffffff00 broadcast 192.168.0.255
      media: IEEE 802.11 Wireless Ethernet OFDM/36Mbps mode 11g
      status: associated
      ssid freebsdap channel 1 (2412 Mhz 11g) bssid 00:11:95:c3:0d:ac
      country US ecm authmode WPA2/802.11i privacy ON deftxkey UNDEF
      AES-CCM 3:128-bit txpower 21.5 bmiss 7 scanvalid 450 bgscan
      bgscanintvl 300 bgscanidle 250 roam:rssi 7 roam:rate 5 protmode CTS
      wme burst roaming MANUAL
```

除此之外， 您也可以手动地使用 above 中那份 `/etc/wpa_supplicant.conf` 来配置， 方法是执行：

```
# `wpa_supplicant -i wlan0 -c /etc/wpa_supplicant.conf`
Trying to associate with 00:11:95:c3:0d:ac (SSID='freebsdap' freq=2412 MHz)
Associated with 00:11:95:c3:0d:ac
WPA: Key negotiation completed with 00:11:95:c3:0d:ac [PTK=CCMP GTK=CCMP]
CTRL-EVENT-CONNECTED - Connection to 00:11:95:c3:0d:ac completed (auth) [id=0 id_str=]
```

接下来的操作， 是运行 `dhclient` 命令来从 DHCP 服务器获取 IP：

```
# `dhclient wlan0`
DHCPREQUEST on wlan0 to 255.255.255.255 port 67
DHCPACK from 192.168.0.1
bound to 192.168.0.254 -- renewal in 300 seconds.
# `ifconfig wlan0`
wlan0: flags=8843<UP,BROADCAST,RUNNING,SIMPLEX,MULTICAST> mtu 1500
      ether 00:11:95:d5:43:62
      inet 192.168.0.254 netmask 0xffffff00 broadcast 192.168.0.255
      media: IEEE 802.11 Wireless Ethernet OFDM/36Mbps mode 11g
      status: associated
      ssid freebsdap channel 1 (2412 Mhz 11g) bssid 00:11:95:c3:0d:ac
      country US ecm authmode WPA2/802.11i privacy ON deftxkey UNDEF
      AES-CCM 3:128-bit txpower 21.5 bmiss 7 scanvalid 450 bgscan
      bgscanintvl 300 bgscanidle 250 roam:rssi 7 roam:rate 5 protmode CTS
      wme burst roaming MANUAL
```

### 注意:

如果在 `/etc/rc.conf` 中把 `ifconfig_wlan0` 设置成了 `DHCP` (像 `ifconfig_wlan0="DHCP"` 这样)， 那么在 `wpa_supplicant` 连上了无线接入点 (AP) 之后，则会自动运行 `dhclient`。

如果不打算使用 DHCP 或者 DHCP 不可用， 您可以在 `wpa_supplicant` 为通讯站完成了身份认证之后， 指定静态 IP 地址：

```
# `ifconfig wlan0 inet 192.168.0.100 netmask 255.255.255.0`
# `ifconfig wlan0`
wlan0: flags=8843<UP,BROADCAST,RUNNING,SIMPLEX,MULTICAST> mtu 1500
      ether 00:11:95:d5:43:62
      inet 192.168.0.100 netmask 0xffffff00 broadcast 192.168.0.255
      media: IEEE 802.11 Wireless Ethernet OFDM/36Mbps mode 11g
      status: associated
      ssid freebsdap channel 1 (2412 Mhz 11g) bssid 00:11:95:c3:0d:ac
      country US ecm authmode WPA2/802.11i privacy ON deftxkey UNDEF
      AES-CCM 3:128-bit txpower 21.5 bmiss 7 scanvalid 450 bgscan
      bgscanintvl 300 bgscanidle 250 roam:rssi 7 roam:rate 5 protmode CTS
      wme burst roaming MANUAL
```

如果没有使用 DHCP， 还需要手工配置默认网关， 以及域名服务器：

```
# `route add default your_default_router`
# `echo "nameserver your_DNS_server" >> /etc/resolv.conf`
```

###### 32.3.3.1.3.2. 使用 EAP-TLS 的 WPA

使用 WPA 的第二种方式是使用 802.1X 后端验证服务器。 在这个例子中， WPA 也称作 企业-WPA， 以便与安全性较差、 采用事先分发密钥的 个人-WPA 区分开来。 在 企业-WPA 中， 验证操作是采用 EAP 完成的 (可扩展认证协议)。

EAP 并未附带加密方法。 因此设计者决定将 EAP 放在加密信道中进行传送。 目前有许多 EAP 验证方法， 最常用的方法是 EAP-TLS、 EAP-TTLS 和 EAP-PEAP。

EAP-TLS (带 传输层安全 的 EAP) 是一种在无线世界中得到了广泛支持的验证协议， 因为它是 [Wi-Fi 联盟](http://www.wi-fi.org/) 核准的第一个 EAP 方法。 EAP-TLS 需要使用三个证书： CA 证书 (在所有计算机上安装)、 用以向您证明服务器身份的服务器证书， 以及每个无线客户端用于证明身份的客户机证书。 在这种 EAP 方式中， 验证服务器和无线客户端均通过自己的证书向对方证明身份， 它们均验证对方的证书是本机构的证书发证机构 (CA) 签发的。

与之前介绍的方法类似， 配置也是通过 `/etc/wpa_supplicant.conf` 来完成的：

```
network={
  ssid="freebsdap" ![1](img/1.png)
  proto=RSN  ![2](img/2.png)
  key_mgmt=WPA-EAP ![3](img/3.png)
  eap=TLS ![4](img/4.png)
  identity="loader" ![5](img/5.png)
  ca_cert="/etc/certs/cacert.pem" ![6](img/6.png)
  client_cert="/etc/certs/clientcert.pem" ![7](img/7.png)
  private_key="/etc/certs/clientkey.pem" ![8](img/8.png)
  private_key_passwd="freebsdmallclient" ![9](img/9.png)
}
```

| ![1](img/#co-tls-ssid) | 这个字段表示网络名 (SSID)。 |
| ![2](img/#co-tls-proto) | 这里， 我们使用 RSN (IEEE® 802.11i) 协议， 也就是 WPA2。 |
| ![3](img/#co-tls-kmgmt) | `key_mgmt` 这行表示所用的密钥管理协议。 在我们的例子中， 它是使用 EAP 验证的 WPA： `WPA-EAP`。 |
| ![4](img/#co-tls-eap) | 这个字段中， 提到了我们的连接采用 EAP 方式。 |
| ![5](img/#co-tls-id) | `identity` 字段包含了 EAP 的实体串。 |
| ![6](img/#co-tls-cacert) | `ca_cert` 字段给出了 CA 证书文件的路径名。 在验证服务器证书时， 这个文件是必需的。 |
| ![7](img/#co-tls-clientcert) | `client_cert` 这行给出了客户机证书的路径名。 对每个无线客户端而言， 这个证书都是在全网范围内唯一的。 |
| ![8](img/#co-tls-pkey) | `private_key` 字段是客户机证书私钥文件的路径名。 |
| ![9](img/#co-tls-pwd) | `private_key_passwd` 字段是私钥的口令字。 |

接着， 把下面的配置写入 `/etc/rc.conf`：

```
wlans_ath0="wlan0"
ifconfig_wlan0="WPA DHCP"
```

下一步是使用 `rc.d` 机制来启用网络接口：

```
# `/etc/rc.d/netif start`
Starting wpa_supplicant.
DHCPREQUEST on wlan0 to 255.255.255.255 port 67 interval 7
DHCPREQUEST on wlan0 to 255.255.255.255 port 67 interval 15
DHCPACK from 192.168.0.20
bound to 192.168.0.254 -- renewal in 300 seconds.
wlan0: flags=8843<UP,BROADCAST,RUNNING,SIMPLEX,MULTICAST> mtu 1500
      ether 00:11:95:d5:43:62
      inet 192.168.0.254 netmask 0xffffff00 broadcast 192.168.0.255
      media: IEEE 802.11 Wireless Ethernet DS/11Mbps mode 11g
      status: associated
      ssid freebsdap channel 1 (2412 Mhz 11g) bssid 00:11:95:c3:0d:ac
      country US ecm authmode WPA2/802.11i privacy ON deftxkey UNDEF
      AES-CCM 3:128-bit txpower 21.5 bmiss 7 scanvalid 450 bgscan
      bgscanintvl 300 bgscanidle 250 roam:rssi 7 roam:rate 5 protmode CTS
      wme burst roaming MANUAL
```

如前面提到的那样， 也可以手工通过 `wpa_supplicant` 和 `ifconfig` 命令达到类似的目的。

###### 32.3.3.1.3.3. 使用 EAP-TTLS 的 WPA

在使用 EAP-TLS 时， 参与验证过程的服务器和客户机都需要证书， 而在使用 EAP-TTLS (带传输层安全隧道的 EAP) 时， 客户机证书则是可选的。 这种方式与某些安全 web 站点更为接近， 即使访问者没有客户端证书， 这些 web 服务器也能建立安全的 SSL 隧道。 EAP-TTLS 会使用加密的 TLS 隧道来传送验证信息。

对于它的配置， 同样是通过 `/etc/wpa_supplicant.conf` 文件来进行的：

```
network={
  ssid="freebsdap"
  proto=RSN
  key_mgmt=WPA-EAP
  eap=TTLS ![1](img/1.png)
  identity="test" ![2](img/2.png)
  password="test" ![3](img/3.png)
  ca_cert="/etc/certs/cacert.pem" ![4](img/4.png)
  phase2="auth=MD5" ![5](img/5.png)
}
```

| ![1](img/#co-ttls-eap) | 这个字段是我们的连接所采用的 EAP 方式。 |
| ![2](img/#co-ttls-id) | `identity` 字段中是在加密 TLS 隧道中用于 EAP 验证的身份串。 |
| ![3](img/#co-ttls-passwd) | `password` 字段中是用于 EAP 验证的口令字。 |
| ![4](img/#co-ttls-cacert) | `ca_cert` 字段给出了 CA 证书文件的路径名。 在验证服务器证书时， 这个文件是必需的。 |
| ![5](img/#co-ttls-pha2) | 这个字段中给出了加密 TLS 隧道中使用的验证方式。 在这个例子中， 我们使用的是带 MD5-加密口令 的 EAP。 “inner authentication” (译注：内部鉴定) 通常也叫 “phase2”。 |

您还必须把下面的配置写入 `/etc/rc.conf`：

```
wlans_ath0="wlan0"
ifconfig_wlan0="WPA DHCP"
```

下一步是启用网络接口：

```
# `/etc/rc.d/netif start`
Starting wpa_supplicant.
DHCPREQUEST on wlan0 to 255.255.255.255 port 67 interval 7
DHCPREQUEST on wlan0 to 255.255.255.255 port 67 interval 15
DHCPREQUEST on wlan0 to 255.255.255.255 port 67 interval 21
DHCPACK from 192.168.0.20
bound to 192.168.0.254 -- renewal in 300 seconds.
wlan0: flags=8843<UP,BROADCAST,RUNNING,SIMPLEX,MULTICAST> mtu 1500
      ether 00:11:95:d5:43:62
      inet 192.168.0.254 netmask 0xffffff00 broadcast 192.168.0.255
      media: IEEE 802.11 Wireless Ethernet DS/11Mbps mode 11g
      status: associated
      ssid freebsdap channel 1 (2412 Mhz 11g) bssid 00:11:95:c3:0d:ac
      country US ecm authmode WPA2/802.11i privacy ON deftxkey UNDEF
      AES-CCM 3:128-bit txpower 21.5 bmiss 7 scanvalid 450 bgscan
      bgscanintvl 300 bgscanidle 250 roam:rssi 7 roam:rate 5 protmode CTS
      wme burst roaming MANUAL
```

###### 32.3.3.1.3.4. 使用 EAP-PEAP 的 WPA

### 注意:

PEAPv0/EAP-MSCHAPv2 是最常见的 PEAP 方法。 此文档的以下部分将使用 PEAP 指代这些方法。

PEAP (受保护的 EAP) 被设计用以替代 EAP-TTLS， 并且是在 EAP-TLS 之后最为常用的 EAP 标准。 换言之， 如果您的网络中有多种不同的操作系统， PEAP 将是仅次于 EAP-TLS 的支持最广的标准。

PEAP 与 EAP-TTLS 很像： 它使用服务器端证书， 通过在客户端与验证服务器之间建立加密的 TLS 隧道来向用户验证身份， 这保护了验证信息的交换过程。 在安全方面， EAP-TTLS 与 PEAP 的区别是 PEAP 会以明文广播用户名， 只有口令是通过加密 TLS 隧道传送的。 而 EAP-TTLS 在传送用户名和口令时， 都使用 TLS 隧道。

我们需要编辑 `/etc/wpa_supplicant.conf` 文件， 并加入与 EAP-PEAP 有关的配置：

```
network={
  ssid="freebsdap"
  proto=RSN
  key_mgmt=WPA-EAP
  eap=PEAP ![1](img/1.png)
  identity="test" ![2](img/2.png)
  password="test" ![3](img/3.png)
  ca_cert="/etc/certs/cacert.pem" ![4](img/4.png)
  phase1="peaplabel=0" ![5](img/5.png)
  phase2="auth=MSCHAPV2" ![6](img/6.png)
}
```

| ![1](img/#co-peap-eap) | 这个字段的内容是用于连接的 EAP 方式。 |
| ![2](img/#co-peap-id) | `identity` 字段中是在加密 TLS 隧道中用于 EAP 验证的身份串。 |
| ![3](img/#co-peap-passwd) | `password` 字段中是用于 EAP 验证的口令字。 |
| ![4](img/#co-peap-cacert) | `ca_cert` 字段给出了 CA 证书文件的路径名。 在验证服务器证书时， 这个文件是必需的。 |
| ![5](img/#co-peap-pha1) | 这个字段包含了第一阶段验证 (TLS 隧道) 的参数。 随您使用的验证服务器的不同， 您需要指定验证的标签。 多数时候， 标签应该是 “客户端 EAP 加密”， 这可以通过使用 `peaplabel=0` 来指定。 更多信息可以在联机手册 [wpa_supplicant.conf(5)](http://www.FreeBSD.org/cgi/man.cgi?query=wpa_supplicant.conf&sektion=5&manpath=freebsd-release-ports) 中找到。 |
| ![6](img/#co-peap-pha2) | 这个字段的内容是验证协议在加密的 TLS 隧道中使用的信息。 对 PEAP 而言， 这是 `auth=MSCHAPV2`。 |

您还必须把下面的配置加入到 `/etc/rc.conf`：

```
wlans_ath0="wlan0"
ifconfig_wlan0="WPA DHCP"
```

下一步是启用网络接口：

```
# `/etc/rc.d/netif start`
Starting wpa_supplicant.
DHCPREQUEST on wlan0 to 255.255.255.255 port 67 interval 7
DHCPREQUEST on wlan0 to 255.255.255.255 port 67 interval 15
DHCPREQUEST on wlan0 to 255.255.255.255 port 67 interval 21
DHCPACK from 192.168.0.20
bound to 192.168.0.254 -- renewal in 300 seconds.
wlan0: flags=8843<UP,BROADCAST,RUNNING,SIMPLEX,MULTICAST> mtu 1500
      ether 00:11:95:d5:43:62
      inet 192.168.0.254 netmask 0xffffff00 broadcast 192.168.0.255
      media: IEEE 802.11 Wireless Ethernet DS/11Mbps mode 11g
      status: associated
      ssid freebsdap channel 1 (2412 Mhz 11g) bssid 00:11:95:c3:0d:ac
      country US ecm authmode WPA2/802.11i privacy ON deftxkey UNDEF
      AES-CCM 3:128-bit txpower 21.5 bmiss 7 scanvalid 450 bgscan
      bgscanintvl 300 bgscanidle 250 roam:rssi 7 roam:rate 5 protmode CTS
      wme burst roaming MANUAL
```

##### 32.3.3.1.4. WEP

WEP (有线等效协议) 是最初 802.11 标准的一部分。 其中没有提供身份验证机制， 只提供了弱访问控制， 而且很容易破解。

WEP 可以通过 `ifconfig` 配置：

```
# `ifconfig wlan0 create wlandev ath0`
# `ifconfig wlan0 inet 192.168.1.100 netmask 255.255.255.0 \
	    ssid my_net wepmode on weptxkey 3 wepkey 3:0x3456789012`
```

*   `weptxkey` 指明了使用哪个 WEP 密钥来进行数据传输。 这里我们使用第三个密钥。 它必须与无线接入点的配置一致。 如果你不清楚你的无线接入点， 尝试用 `1` （就是说第一个密钥）来设置这个变量。

*   `wepkey` 用于选择 WEP 密钥。 其格式应为 *`index:key`*， key 默认为 `1`; 如果需要设置的密钥不是第一个， 就必需指定 index 了。

    ### 注意:

    您需要将 `0x3456789012` 改为在无线接入点上配置的那个。

我们建议您阅读联机手册 [ifconfig(8)](http://www.FreeBSD.org/cgi/man.cgi?query=ifconfig&sektion=8&manpath=freebsd-release-ports) 来了解进一步的信息。

`wpa_supplicant` 机制也可以用来配置您的无线网卡使用 WEP。 前面的例子也可以通过在 `/etc/wpa_supplicant.conf` 中加入下述设置来实现：

```
network={
  ssid="my_net"
  key_mgmt=NONE
  wep_key3=3456789012
  wep_tx_keyidx=3
}
```

接着：

```
# `wpa_supplicant -i wlan0 -c /etc/wpa_supplicant.conf`
Trying to associate with 00:13:46:49:41:76 (SSID='dlinkap' freq=2437 MHz)
Associated with 00:13:46:49:41:76
```

### 32.3.4. Ad-hoc 模式

IBSS 模式， 也称为 ad-hoc 模式， 是为点对点连接设计的。 例如， 如果希望在计算机 `A` 和 `B` 之间建立 ad-hoc 网络， 我们只需选择两个 IP 地址和一个 SSID 就可以了。

在计算机 `A` 上：

```
# `ifconfig wlan0 create wlandev ath0 wlanmode adhoc`
# `ifconfig wlan0 inet 192.168.0.1 netmask 255.255.255.0 ssid freebsdap`
# `ifconfig wlan0`
  wlan0: flags=8843<UP,BROADCAST,RUNNING,SIMPLEX,MULTICAST> metric 0 mtu 1500
	  ether 00:11:95:c3:0d:ac
	  inet 192.168.0.1 netmask 0xffffff00 broadcast 192.168.0.255
	  media: IEEE 802.11 Wireless Ethernet autoselect mode 11g <adhoc>
	  status: running
	  ssid freebsdap channel 2 (2417 Mhz 11g) bssid 02:11:95:c3:0d:ac
	  country US ecm authmode OPEN privacy OFF txpower 21.5 scanvalid 60
	  protmode CTS wme burst
```

此处的 `adhoc` 参数表示无线网络接口应以 IBSS 模式运转。

此时， 在 `B` 上应该能够检测到 `A` 的存在了：

```
# `ifconfig wlan0 create wlandev ath0 wlanmode adhoc`
# `ifconfig wlan0 up scan`
  SSID/MESH ID    BSSID              CHAN RATE   S:N     INT CAPS
  freebsdap       02:11:95:c3:0d:ac    2   54M -64:-96  100 IS   WME
```

在输出中的 `I` 再次确认了 `A` 机是以 ad-hoc 模式运行的。 我们只需给 `B` 配置一不同的 IP 地址：

```
# `ifconfig wlan0 inet 192.168.0.2 netmask 255.255.255.0 ssid freebsdap`
# `ifconfig wlan0`
  wlan0: flags=8843<UP,BROADCAST,RUNNING,SIMPLEX,MULTICAST> metric 0 mtu 1500
	  ether 00:11:95:d5:43:62
	  inet 192.168.0.2 netmask 0xffffff00 broadcast 192.168.0.255
	  media: IEEE 802.11 Wireless Ethernet autoselect mode 11g <adhoc>
	  status: running
	  ssid freebsdap channel 2 (2417 Mhz 11g) bssid 02:11:95:c3:0d:ac
	  country US ecm authmode OPEN privacy OFF txpower 21.5 scanvalid 60
	  protmode CTS wme burst
```

这样， `A` 和 `B` 就可以交换信息了。

### 32.3.5. FreeBSD 基于主机的（无线）访问接入点

FreeBSD 可以作为一个（无线）访问接入点（AP）， 这样可以不必再去买一个硬件 AP 或者使用 ad-hoc 模式的网络。 当你的 FreeBSD 机器作为网关连接到另外一个网络的时候将非常有用。

#### 32.3.5.1. 基本配置

在把你的 FreeBSD 机器配置成一个 AP 以前， 你首先需要先在内核配置好对你的无线网卡的无线网络支持。 当然你还需要加上你想用的安全协议。想获得更详细的信息， 请参阅 第 32.3.2 节 “基本安装”。

### 注意:

目前还不支持使用 Windows® 驱动和 NDIS 驱动包装的网卡做为 AP 使用。只有 FreeBSD 原生的无线驱动能够支持 AP 模式。

一旦装载了无线网络的支持， 你就可以检查一下看看你的无线设备是否支持基于主机的无线访问接入模式 （通常也被称为 hostap 模式）：

```
# `ifconfig wlan0 create wlandev ath0`
# `ifconfig wlan0 list caps`
drivercaps=6f85edc1<STA,FF,TURBOP,IBSS,HOSTAP,AHDEMO,TXPMGT,SHSLOT,SHPREAMBLE,MONITOR,MBSS,WPA1,WPA2,BURST,WME,WDS,BGSCAN,TXFRAG>
cryptocaps=1f<WEP,TKIP,AES,AES_CCM,TKIPMIC>
```

这段输出显示了网卡所支持的各种功能； 其中的关键字 `HOSTAP` 表示这块网卡可以作为无线网络接入点来使用。 此外， 这里还会给出所支持的加密算法： WEP、 TKIP、 AES， 等等。 这些信息对于知道在访问接入点上使用何种安全协议非常重要。

只有创建网络伪设备时能够配置无线设备是否以 hostap 模式运行， 如果之前已经存在了相应的设备， 则需要首先将其销毁：

```
# `ifconfig wlan0 destroy`
```

接着， 在配置其它参数前， 以正确的选项重新生成设备：

```
# `ifconfig wlan0 create wlandev ath0 wlanmode hostap`
# `ifconfig wlan0 inet 192.168.0.1 netmask 255.255.255.0 ssid freebsdap mode 11g channel 1`
```

再次使用 `ifconfig` 检查 `wlan0` 网络接口的状态：

```
# `ifconfig wlan0`
  wlan0: flags=8843<UP,BROADCAST,RUNNING,SIMPLEX,MULTICAST> metric 0 mtu 1500
	  ether 00:11:95:c3:0d:ac
	  inet 192.168.0.1 netmask 0xffffff00 broadcast 192.168.0.255
	  media: IEEE 802.11 Wireless Ethernet autoselect mode 11g <hostap>
	  status: running
	  ssid freebsdap channel 1 (2412 Mhz 11g) bssid 00:11:95:c3:0d:ac
	  country US ecm authmode OPEN privacy OFF txpower 21.5 scanvalid 60
	  protmode CTS wme burst dtimperiod 1 -dfs
```

`hostap` 参数指定了接口以主机接入点的方式运行。

通过在 `/etc/rc.conf` 中加入下面的配置， 也可以在系统引导的过程中自动完成对于网络接口的配置：

```
wlans_ath0="wlan0"
create_args_wlan0="wlanmode hostap"
ifconfig_wlan0="inet *`192.168.0.1`* netmask *`255.255.255.0`* ssid *`freebsdap`* mode 11g channel *`1`*"
```

#### 32.3.5.2. 不使用认证或加密的（无线）访问接入点

尽管我们不推荐运行一个不使用任何认证或加密的 AP， 但这是一个非常简单的检测 AP 是否正常工作的方法。 这样配置对于调试客户端问题也非常重要。

一旦 AP 被配置成了我们前面所展示的那样， 就可以在另外一台无线机器上初始化一次扫描来找到这个 AP：

```
# `ifconfig wlan0 create wlandev ath0`
# `ifconfig wlan0 up scan`
SSID/MESH ID    BSSID              CHAN RATE   S:N     INT CAPS
freebsdap       00:11:95:c3:0d:ac    1   54M -66:-96  100 ES   WME
```

在客户机上能看到已经连接上了（无线）访问接入点：

```
# `ifconfig wlan0 inet 192.168.0.2 netmask 255.255.255.0 ssid freebsdap`
# `ifconfig wlan0`
  wlan0: flags=8843<UP,BROADCAST,RUNNING,SIMPLEX,MULTICAST> metric 0 mtu 1500
	  ether 00:11:95:d5:43:62
	  inet 192.168.0.2 netmask 0xffffff00 broadcast 192.168.0.255
	  media: IEEE 802.11 Wireless Ethernet OFDM/54Mbps mode 11g
	  status: associated
	  ssid freebsdap channel 1 (2412 Mhz 11g) bssid 00:11:95:c3:0d:ac
	  country US ecm authmode OPEN privacy OFF txpower 21.5 bmiss 7
	  scanvalid 60 bgscan bgscanintvl 300 bgscanidle 250 roam:rssi 7
	  roam:rate 5 protmode CTS wme burst
```

#### 32.3.5.3. 使用 WPA 的（无线）访问接入点

这一段将注重介绍在 FreeBSD （无线）访问接入点上配置使用 WPA 安全协议。 更多有关 WPA 和配置基于 WPA 无线客户端的细节 请参阅 第 32.3.3.1.3 节 “WPA”。

hostapd 守护进程将被用于处理与客户端的认证和在启用 WPA （无线）访问接入点上的密钥管理。

接下来，所有的配置操作都将在作为 AP 的 FreeBSD 机器上完成。 一旦 AP 能够正确的工作了，便把如下这行加入 `/etc/rc.conf` 使得 hostapd 能在机器启动的时候自动运行：

```
hostapd_enable="YES"
```

在配置 hostapd 以前， 请确保你已经完成了基本配置中所介绍的步骤 第 32.3.5.1 节 “基本配置”。

##### 32.3.5.3.1. WPA-PSK

WPA-PSK 旨在为没有认证服务器的小型网络而设计的。

配置文件为 `/etc/hostapd.conf` file：

```
interface=wlan0 ![1](img/1.png)
debug=1 ![2](img/2.png)
ctrl_interface=/var/run/hostapd ![3](img/3.png)
ctrl_interface_group=wheel ![4](img/4.png)
ssid=freebsdap ![5](img/5.png)
wpa=1 ![6](img/6.png)
wpa_passphrase=freebsdmall ![7](img/7.png)
wpa_key_mgmt=WPA-PSK ![8](img/8.png)
wpa_pairwise=CCMP TKIP ![9](img/9.png)
```

| ![1](img/#co-ap-wpapsk-iface) | 这一项标明了访问接入点所使用的无线接口。 |
| ![2](img/#co-ap-wpapsk-dbug) | 这一项设置了执行 hostapd 时候显示相关信息的详细程度。 `1` 表示最小的级别。 |
| ![3](img/#co-ap-wpapsk-ciface) | `ctrl_interface` 这项给出了 hostapd 存储与其他外部程序（比如 [hostapd_cli(8)](http://www.FreeBSD.org/cgi/man.cgi?query=hostapd_cli&sektion=8&manpath=freebsd-release-ports)) 通信的域套接口文件路径。这里使用了默认值。 |
| ![4](img/#co-ap-wpapsk-cifacegrp) | `ctrl_interface_group` 这行设置了允许访问控制界面文件的组属性 （这里我们使用了 `wheel` 组）。 |
| ![5](img/#co-ap-wpapsk-ssid) | 这一项是设置网络的名称。 |
| ![6](img/#co-ap-wpapsk-wpa) | `wpa` 这项表示启用了 WPA 而且指明要使用何种 WPA 认证协议。 值 `1` 表示 AP 将使用 WPA-PSK。 |
| ![7](img/#co-ap-wpapsk-pass) | `wpa_passphrase` 这项包含用于 WPA 认证的 ASCII 密码。 
### 警告:

通常使用从丰富的字母表生成足够长度的强壮密码， 以不至于被轻易的猜测或攻击到。

 |
| ![8](img/#co-ap-wpapsk-kmgmt) | `wpa_key_mgmt` 这行表明了我们所使用的密钥管理协议。 在这个例子中是 WPA-PSK。 |
| ![9](img/#co-ap-wpapsk-pwise) | `wpa_pairwise` 这项表示（无线）访问接入点所接受的加密算法。 在这个例子中，TKIP(WPA) 和 CCMP(WPA2) 密码都会被接受。 CCMP 密码是除 TKIP 外的另一种选择， CCMP 一般作为首选密码； 仅有在 CCMP 不能被使用的环境中选择 TKIP。 |

接下来的一步就是运行 hostapd：

```
# `/etc/rc.d/hostapd forcestart`
```

```
# `ifconfig wlan0`
  wlan0: flags=8843<UP,BROADCAST,RUNNING,SIMPLEX,MULTICAST> mtu 2290
	  inet 192.168.0.1 netmask 0xffffff00 broadcast 192.168.0.255
	  inet6 fe80::211:95ff:fec3:dac%ath0 prefixlen 64 scopeid 0x4
	  ether 00:11:95:c3:0d:ac
	  media: IEEE 802.11 Wireless Ethernet autoselect mode 11g <hostap>
	  status: associated
	  ssid freebsdap channel 1 bssid 00:11:95:c3:0d:ac
	  authmode WPA2/802.11i privacy MIXED deftxkey 2 TKIP 2:128-bit txpowmax 36 protmode CTS dtimperiod 1 bintval 100
```

现在客户端能够连接上运行的（无线）访问接入点了， 更多细节可以参阅 第 32.3.3.1.3 节 “WPA”。 查看有哪些客户连接上了 AP 可以运行命令 `ifconfig wlan0 list sta`。

#### 32.3.5.4. 使用 WEP 的（无线）访问接入点

我们不推荐使用 WEP 来设置一个（无线）访问接入点， 因为没有认证的机制并容易被破解。 一些历史遗留下的无线网卡仅支持 WEP 作为安全协议， 这些网卡仅允许搭建不含认证或 WEP 协议的 AP。

在设置了正确的 SSID 和 IP 地址后，无线设备就可以进入 hostap 模式了：

```
# `ifconfig wlan0 create wlandev ath0 wlanmode hostap`
# `ifconfig wlan0 inet 192.168.0.1 netmask 255.255.255.0 \
	ssid freebsdap wepmode on weptxkey 3 wepkey 3:0x3456789012 mode 11g`
```

*   `weptxkey` 表示传输中使用哪一个 WEP 密钥。 这个例子中用了第 3 把密钥（请注意密钥的编号从 `1`开始）。 这个参数必须设置以用来加密数据。

*   `wepkey` 表示设置所使用的 WEP 密钥。 它应该符合 *`index:key`* 这样的格式。 如果没有指定 index，那么默认值为 `1`。 这就是说如果我们使用了除第一把以外的密钥， 那么就需要指定 index。

再使用一次 `ifconfig` 命令查看 `wlan0` 接口的状态：

```
# `ifconfig wlan0`
  wlan0: flags=8843<UP,BROADCAST,RUNNING,SIMPLEX,MULTICAST> metric 0 mtu 1500
	  ether 00:11:95:c3:0d:ac
	  inet 192.168.0.1 netmask 0xffffff00 broadcast 192.168.0.255
	  media: IEEE 802.11 Wireless Ethernet autoselect mode 11g <hostap>
	  status: running
	  ssid freebsdap channel 4 (2427 Mhz 11g) bssid 00:11:95:c3:0d:ac
	  country US ecm authmode OPEN privacy ON deftxkey 3 wepkey 3:40-bit
	  txpower 21.5 scanvalid 60 protmode CTS wme burst dtimperiod 1 -dfs
```

现在可以从另外一台无线机器上初始化一次扫描来找到这个 AP 了：

```
# `ifconfig wlan0 create wlandev ath0`
# `ifconfig wlan0 up scan`
SSID            BSSID              CHAN RATE  S:N   INT CAPS
freebsdap       00:11:95:c3:0d:ac    1   54M 22:1   100 EPS
```

现在客户机能够使用正确的参数（密钥等） 找到并连上（无线）访问接入点了， 更多细节请参阅第 32.3.3.1.4 节 “WEP”。

### 32.3.6. 同时使用有线和无线连接

一般而言， 有线网络的速度更快而且更可靠， 而无线网络则提供更好的灵活及机动性， 使用笔记本的用户， 往往会希望结合两者的优点， 并能够在两种连接之间无缝切换。

在 FreeBSD 上可以将多个网络接口合并到一起， 并以 “故障转移” 的方式自动切换， 也就是说， 这一组网络接口有一定的优先顺序， 而操作系统在链路状态发生变化时则自动进行切换， 例如当同时存在有线和无线连接的时候优先使用有线网络， 而当有线网络断开时， 则自动切换到无线网络。

我们将在稍后的 第 32.6 节 “链路聚合与故障转移” 中介绍链路聚合和故障转移， 并在 例 32.3 “有线网络和无线网络接口间的自动切换” 中对这种配置方式进行示范。

### 32.3.7. 故障排除

如果您在使用无线网络时遇到了麻烦， 此处提供了一系列用以帮助排除故障的步骤。

*   如果您在列表中找不到无线访问点， 请确认您没有将无线设备配置为使用有限的一组频段。

*   如果您无法关联到无线访问点， 请确认您的通讯站配置与无线访问点的配置一致。 这包括认证模式以及安全协议。 尽可能简化您的配置。 如果您正使用类似 WPA 或 WEP 这样的安全协议， 请将无线访问点配置为开放验证和不采用安全措施， 并检查是否数据能够通过。

*   一旦您能够关联到无线访问点之后， 就可以使用简单的工具如 [ping(8)](http://www.FreeBSD.org/cgi/man.cgi?query=ping&sektion=8&manpath=freebsd-release-ports) 来诊断安全配置了。

    `wpa_supplicant` 提供了许多调试支持； 尝试手工运行它， 在启动时指定 `-dd` 选项， 并察看输出结果。

*   除此之外还有许多其它的底层调试工具。 您可以使用 `/usr/src/tools/tools/net80211` 中的 `wlandebug` 命令来启用 802.11 协议支持层的调试功能。 例如：

    ```
    # `wlandebug -i ath0 +scan+auth+debug+assoc`
      net.wlan.0.debug: 0 => 0xc80000<assoc,auth,scan>
    ```

    可以用来启用与扫描无线访问点和 802.11 协议在安排通讯时与握手有关的控制台信息。

    还有许多有用的统计信息是由 802.11 层维护的； `wlanstats` 工具可以显示这些信息。 这些统计数据能够指出由 802.11 层识别出来的错误。 请注意某些错误可能是由设备驱动在 802.11 层之下识别出来的， 因此这些错误可能并不显示。 要诊断与设备有关的问题， 您需要参考设备驱动程序的文档。

如果上述信息没能帮助您找到具体的问题所在， 请提交问题报告， 并在其中附上这些工具的输出。

## 32.4. 蓝牙

作者：Pav Lucistnik.中文翻译：张 雪平 和 袁 苏义.

### 32.4.1. 简介

Bluetooth (蓝牙) 是一项无线技术， 用于建立带宽为 2.4GHZ，波长为 10 米的私有网络。 网络一般是由便携式设备，比加手机 (cellular phone)， 掌上电脑 (handhelds) 和膝上电脑 (laptops)) 以 ad-hoc 形式组成。不象其它流行的无线技术――Wi-Fi，Bluetooth 提供了更高级的服务层面，像类 FTP 的文件服务、文件推送 (file pushing)、语音传送、串行线模拟等等。

在 FreeBSD 里，蓝牙栈 (Bluetooth stack) 通过使用 Netgraph 框架 (请看 [netgraph(4)](http://www.FreeBSD.org/cgi/man.cgi?query=netgraph&sektion=4&manpath=freebsd-release-ports)) 来的实现。 大量的"Bluetooth USB dongle"由 [ng_ubt(4)](http://www.FreeBSD.org/cgi/man.cgi?query=ng_ubt&sektion=4&manpath=freebsd-release-ports) 驱动程序支持。 基于 Broadcom BCM2033 芯片组的 Bluetooth 设备可以通过 [ubtbcmfw(4)](http://www.FreeBSD.org/cgi/man.cgi?query=ubtbcmfw&sektion=4&manpath=freebsd-release-ports) 和 [ng_ubt(4)](http://www.FreeBSD.org/cgi/man.cgi?query=ng_ubt&sektion=4&manpath=freebsd-release-ports) 驱动程序支持。 3Com Bluetooth PC 卡 3CRWB60-A 由 [ng_bt3c(4)](http://www.FreeBSD.org/cgi/man.cgi?query=ng_bt3c&sektion=4&manpath=freebsd-release-ports) 驱动程序支持。 基于 Serial 和 UART 的蓝牙设备由 [sio(4)](http://www.FreeBSD.org/cgi/man.cgi?query=sio&sektion=4&manpath=freebsd-release-ports)、[ng_h4(4)](http://www.FreeBSD.org/cgi/man.cgi?query=ng_h4&sektion=4&manpath=freebsd-release-ports) 和 [hcseriald(8)](http://www.FreeBSD.org/cgi/man.cgi?query=hcseriald&sektion=8&manpath=freebsd-release-ports)。本节介绍 USB Bluetooth dongle 的使用。

### 32.4.2. 插入设备

默认的 Bluetooth 设备驱动程序已存在于内核模块里。 接入设备前，您需要将驱动程序加载入内核：

```
# `kldload ng_ubt`
```

如果系统启动时 Bluetooth 设备已经存在于系统里， 那么从 `/boot/loader.conf` 里加载这个模块：

```
ng_ubt_load="YES"
```

插入 USB dongle。控制台(console)(或 syslog 中)会出现类似如下的信息：

```
ubt0: vendor 0x0a12 product 0x0001, rev 1.10/5.25, addr 2
ubt0: Interface 0 endpoints: interrupt=0x81, bulk-in=0x82, bulk-out=0x2
ubt0: Interface 1 (alt.config 5) endpoints: isoc-in=0x83, isoc-out=0x3,
      wMaxPacketSize=49, nframes=6, buffer size=294
```

脚本 `/etc/rc.d/bluetooth` 是用来启动和停止 Bluetooth stack (蓝牙栈)的。 最好在拔出设备前停止 stack(stack)，当然也不是非做不可。 启动 stack (栈) 时，会得到如下的输出：

```
# `/etc/rc.d/bluetooth start ubt0`
BD_ADDR: 00:02:72:00:d4:1a
Features: 0xff 0xff 0xf 00 00 00 00 00
<3-Slot> <5-Slot> <Encryption> <Slot offset>
<Timing accuracy> <Switch> <Hold mode> <Sniff mode>
<Park mode> <RSSI> <Channel quality> <SCO link>
<HV2 packets> <HV3 packets> <u-law log> <A-law log> <CVSD>
<Paging scheme> <Power control> <Transparent SCO data>
Max. ACL packet size: 192 bytes
Number of ACL packets: 8
Max. SCO packet size: 64 bytes
Number of SCO packets: 8
```

### 32.4.3. 主控制器接口 (HCI)

主控制器接口 (HCI) 提供了通向基带控制器和连接管理器的命令接口及访问硬件状态字和控制寄存器的通道。 这个接口提供了访问蓝牙基带 (Bluetooth baseband) 功能的统一方式。 主机上的 HCI 层与蓝牙硬件上的 HCI 固件交换数据和命令。 主控制器的传输层 (如物理总线) 驱动程序提供两个 HCI 层交换信息的能力。

为每个蓝牙 (Bluetooth) 设备创建一个 *hci* 类型的 Netgraph 结点。 HCI 结点一般连接蓝牙设备的驱动结点 (下行流) 和 L2CAP 结点 (上行流)。 所有的 HCI 操作必须在 HCI 结点上进行而不是设备驱动结点。HCI 结点的默认名是 “devicehci”。更多细节请参考 [ng_hci(4)](http://www.FreeBSD.org/cgi/man.cgi?query=ng_hci&sektion=4&manpath=freebsd-release-ports) 的联机手册。

最常见的任务是发现在 RF proximity 中的蓝牙 (Bluetooth) 设备。这个就叫做 *质询(inquiry)*。质询及 HCI 相关的操作可以由 [hccontrol(8)](http://www.FreeBSD.org/cgi/man.cgi?query=hccontrol&sektion=8&manpath=freebsd-release-ports) 工具来完成。 以下的例子展示如何找出范围内的蓝牙设备。 在几秒钟内您应该得到一张设备列表。 注意远程主机只有被置于 *discoverable(可发现)* 模式才能答应质询。

```
% `hccontrol -n ubt0hci inquiry`
Inquiry result, num_responses=1
Inquiry result #0
       BD_ADDR: 00:80:37:29:19:a4
       Page Scan Rep. Mode: 0x1
       Page Scan Period Mode: 00
       Page Scan Mode: 00
       Class: 52:02:04
       Clock offset: 0x78ef
Inquiry complete. Status: No error [00]
```

`BD_ADDR` 是蓝牙设备的特定地址， 类似于网卡的 MAC 地址。需要用此地址与某个设备进一步地通信。 可以为 BD_ADDR 分配由人可读的名字 (human readable name)。 文件 `/etc/bluetooth/hosts` 包含已知蓝牙主机的信息。 下面的例子展示如何获得分配给远程设备的可读名。

```
% `hccontrol -n ubt0hci remote_name_request 00:80:37:29:19:a4`
BD_ADDR: 00:80:37:29:19:a4
Name: Pav's T39
```

如果在远程蓝牙上运行质询，您会发现您的计算机是 “your.host.name (ubt0)”。 分配给本地设备的名字可随时改变。

蓝牙系统提供点对点连接 (只有两个蓝牙设备参与) 和点对多点连接。在点对多点连接中，连接由多个蓝牙设备共享。 以下的例子展示如何取得本地设备的活动基带 (baseband) 连接列表。

```
% `hccontrol -n ubt0hci read_connection_list`
Remote BD_ADDR    Handle Type Mode Role Encrypt Pending Queue State
00:80:37:29:19:a4     41  ACL    0 MAST    NONE       0     0 OPEN
```

*connection handle(连接柄)* 在需要终止基带连接时有用。注意：一般不需要手动完成。 栈 (stack) 会自动终止不活动的基带连接。

```
# `hccontrol -n ubt0hci disconnect 41`
Connection handle: 41
Reason: Connection terminated by local host [0x16]
```

参考 `hccontrol help` 获取完整的 HCI 命令列表。大部分 HCI 命令不需要超级用户权限。

### 32.4.4. 逻辑连接控制和适配协议(L2CAP)

逻辑连接控制和适配协议 (L2CAP) 为上层协议提供面向连接和无连接的数据服务， 并提供多协议功能和分割重组操作。L2CAP 充许上层协议和应用软件传输和接收最大长度为 64K 的 L2CAP 数据包。

L2CAP 基于 *通道(channel)* 的概念。 通道 (Channel) 是位于基带 (baseband) 连接之上的逻辑连接。 每个通道以多对一的方式绑定一个单一协议 (single protocol)。 多个通道可以绑定同一个协议，但一个通道不可以绑定多个协议。 每个在通道里接收到的 L2CAP 数据包被传到相应的上层协议。 多个通道可共享同一个基带连接。

为每个蓝牙 (Bluetooth) 设备创建一个 *l2cap* 类型的 Netgraph 结点。 L2CAP 结点一般连接 HCI 结点(下行流)和蓝牙设备的驱动结点(上行流)。 L2CAP 结点的默认名是 “devicel2cap”。 更多细节请参考 [ng_l2cap(4)](http://www.FreeBSD.org/cgi/man.cgi?query=ng_l2cap&sektion=4&manpath=freebsd-release-ports) 的联机手册。

一个有用的命令是 [l2ping(8)](http://www.FreeBSD.org/cgi/man.cgi?query=l2ping&sektion=8&manpath=freebsd-release-ports)， 它可以用来 ping 其它设备。 一些蓝牙实现可能不会返回所有发送给它们的数据， 所以下例中的 `0 bytes` 是正常的。

```
# `l2ping -a 00:80:37:29:19:a4`
0 bytes from 0:80:37:29:19:a4 seq_no=0 time=48.633 ms result=0
0 bytes from 0:80:37:29:19:a4 seq_no=1 time=37.551 ms result=0
0 bytes from 0:80:37:29:19:a4 seq_no=2 time=28.324 ms result=0
0 bytes from 0:80:37:29:19:a4 seq_no=3 time=46.150 ms result=0
```

[l2control(8)](http://www.FreeBSD.org/cgi/man.cgi?query=l2control&sektion=8&manpath=freebsd-release-ports) 工具用于在 L2CAP 上进行多种操作。 以下这个例子展示如何取得本地设备的逻辑连接 (通道) 和基带连接的列表：

```
% `l2control -a 00:02:72:00:d4:1a read_channel_list`
L2CAP channels:
Remote BD_ADDR     SCID/ DCID   PSM  IMTU/ OMTU State
00:07:e0:00:0b:ca    66/   64     3   132/  672 OPEN
% `l2control -a 00:02:72:00:d4:1a read_connection_list`
L2CAP connections:
Remote BD_ADDR    Handle Flags Pending State
00:07:e0:00:0b:ca     41 O           0 OPEN
```

另一个诊断工具是 [btsockstat(1)](http://www.FreeBSD.org/cgi/man.cgi?query=btsockstat&sektion=1&manpath=freebsd-release-ports)。 它完成与 [netstat(1)](http://www.FreeBSD.org/cgi/man.cgi?query=netstat&sektion=1&manpath=freebsd-release-ports) 类似的操作， 只是用了蓝牙网络相关的数据结构。 以下这个例子显示与 [l2control(8)](http://www.FreeBSD.org/cgi/man.cgi?query=l2control&sektion=8&manpath=freebsd-release-ports) 相同的逻辑连接。

```
% `btsockstat`
Active L2CAP sockets
PCB      Recv-Q Send-Q Local address/PSM       Foreign address   CID   State
c2afe900      0      0 00:02:72:00:d4:1a/3     00:07:e0:00:0b:ca 66    OPEN
Active RFCOMM sessions
L2PCB    PCB      Flag MTU   Out-Q DLCs State
c2afe900 c2b53380 1    127   0     Yes  OPEN
Active RFCOMM sockets
PCB      Recv-Q Send-Q Local address     Foreign address   Chan DLCI State
c2e8bc80      0    250 00:02:72:00:d4:1a 00:07:e0:00:0b:ca 3    6    OPEN
```

### 32.4.5. RFCOMM 协议

RFCOMM 协议提供基于 L2CAP 协议的串行端口模拟。 该协议基于 ETSI TS 07.10 标准。RFCOMM 是一个简单的传输协议， 附加了摸拟 9 针 RS-232(EIATIA-232-E) 串行端口的定义。 RFCOMM 协议最多支持 60 个并发连接 (RFCOMM 通道)。

为了实现 RFCOMM， 运行于不同设备上的应用程序建立起一条关于它们之间通信段的通信路径。 RFCOMM 实际上适用于使用串行端口的应用软件。 通信段是一个设备到另一个设备的蓝牙连接 (直接连接)。

RFCOMM 关心的只是直接连接设备之间的连接， 或在网络里一个设备与 modem 之间的连接。RFCOMM 能支持其它的配置， 比如在一端通过蓝牙无线技术通讯而在另一端使用有线接口。

在 FreeBSD，RFCOMM 协议在蓝牙套接字层 (Bluetooth sockets layer) 实现。

### 32.4.6. 设备的结对(Pairing of Devices)

默认情况下，蓝牙通信是不需要验证的， 任何设备可与其它任何设备对话。一个蓝牙设备 (比如手机) 可以选择通过验证以提供某种特殊服务 (比如拨号服务)。 蓝牙验证一般使用 *PIN 码(PIN codes)*。 一个 PIN 码是最长为 16 个字符的 ASCII 字符串。 用户需要在两个设备中输入相同的 PIN 码。用户输入了 PIN 码后， 两个设备会生成一个 *连接密匙(link key)*。 接着连接密钥可以存储在设备或存储器中。 连接时两个设备会使用先前生成的连接密钥。 以上介绍的过程被称为 *结对(pairing)*。 注意如果任何一方丢失了连接密钥，必须重新进行结对。

守护进程 [hcsecd(8)](http://www.FreeBSD.org/cgi/man.cgi?query=hcsecd&sektion=8&manpath=freebsd-release-ports) 负责处理所有蓝牙验证请求。 默认的配置文件是 `/etc/bluetooth/hcsecd.conf`。 下面的例子显示一个手机的 PIN 码被预设为“1234”：

```
device {
        bdaddr  00:80:37:29:19:a4;
        name    "Pav's T39";
        key     nokey;
        pin     "1234";
      }
```

PIN 码没有限制(除了长度)。有些设备 (例如蓝牙耳机) 会有一个预置的 PIN 码。`-d` 开关强制 [hcsecd(8)](http://www.FreeBSD.org/cgi/man.cgi?query=hcsecd&sektion=8&manpath=freebsd-release-ports) 守护进程处于前台，因此很容易看清发生了什么。 设置远端设备准备接收结对 (pairing)，然后启动蓝牙连接到远端设备。 远端设备应该回应接收了结对并请求 PIN 码。输入与 `hcsecd.conf` 中一样的 PIN 码。 现在您的个人计算机已经与远程设备结对了。 另外您也可以在远程设备上初始结点。

可以通过在 `/etc/rc.conf` 文件中增加下面的行， 以便让 hcsecd 在系统启动时自动运行：

```
hcsecd_enable="YES"
```

以下是简单的 hcsecd 服务输出样本：

```
hcsecd[16484]: Got Link_Key_Request event from 'ubt0hci', remote bdaddr 0:80:37:29:19:a4
hcsecd[16484]: Found matching entry, remote bdaddr 0:80:37:29:19:a4, name 'Pav's T39', link key doesn't exist
hcsecd[16484]: Sending Link_Key_Negative_Reply to 'ubt0hci' for remote bdaddr 0:80:37:29:19:a4
hcsecd[16484]: Got PIN_Code_Request event from 'ubt0hci', remote bdaddr 0:80:37:29:19:a4
hcsecd[16484]: Found matching entry, remote bdaddr 0:80:37:29:19:a4, name 'Pav's T39', PIN code exists
hcsecd[16484]: Sending PIN_Code_Reply to 'ubt0hci' for remote bdaddr 0:80:37:29:19:a4
```

### 32.4.7. 服务发现协议 (SDP)

服务发现协议 (SDP) 提供给客户端软件一种方法， 它能发现由服务器软件提供的服务及属性。 服务的属性包括所提供服务的类型或类别， 使用该服务所需要的机制或协议。

SDP 包括 SDP 服务器和 SDP 客户端之间的通信。 服务器维护一张服务记录列表，它介绍服务器上服务的特性。 每个服务记录包含关于单个服务的信息。通过发出 SDP 请求， 客户端会得到服务记录列表的信息。如果客户端 (或者客户端上的应用软件) 决定使用一个服务，为了使用这个服务它必须与服务提供都建立一个独立的连接。 SDP 提供了发现服务及其属性的机制，但它并不提供使用这些服务的机制。

一般地，SDP 客户端按照服务的某种期望特征来搜索服务。 但是，即使没有任何关于由 SDP 服务端提供的服务的预设信息， 有时也能令人满意地发现它的服务记录里所描述的是哪种服务类型。 这种发现所提供服务的过程称为 *浏览(browsing)*。

蓝牙 SDP 服务端 [sdpd(8)](http://www.FreeBSD.org/cgi/man.cgi?query=sdpd&sektion=8&manpath=freebsd-release-ports) 和命令行客户端 [sdpcontrol(8)](http://www.FreeBSD.org/cgi/man.cgi?query=sdpcontrol&sektion=8&manpath=freebsd-release-ports) 都包括在了标准的 FreeBSD 安装里。 下面的例子展示如何进行 SDP 浏览查询。

```
% `sdpcontrol -a 00:01:03:fc:6e:ec browse`
Record Handle: 00000000
Service Class ID List:
        Service Discovery Server (0x1000)
Protocol Descriptor List:
        L2CAP (0x0100)
                Protocol specific parameter #1: u/int/uuid16 1
                Protocol specific parameter #2: u/int/uuid16 1

Record Handle: 0x00000001
Service Class ID List:
        Browse Group Descriptor (0x1001)

Record Handle: 0x00000002
Service Class ID List:
        LAN Access Using PPP (0x1102)
Protocol Descriptor List:
        L2CAP (0x0100)
        RFCOMM (0x0003)
                Protocol specific parameter #1: u/int8/bool 1
Bluetooth Profile Descriptor List:
        LAN Access Using PPP (0x1102) ver. 1.0

```

...等等。注意每个服务有一个属性 (比如 RFCOMM 通道)列表。 根据服务您可能需要为一些属性做个注释。 有些“蓝牙实现 (Bluetooth implementation)”不支持服务浏览， 可能会返回一个空列表。这种情况，可以搜索指定的服务。 下面的例子展示如何搜索 OBEX Object Push (OPUSH) 服务：

```
% `sdpcontrol -a 00:01:03:fc:6e:ec search OPUSH`
```

要在 FreeBSD 里为蓝牙客户端提供服务，可以使用 [sdpd(8)](http://www.FreeBSD.org/cgi/man.cgi?query=sdpd&sektion=8&manpath=freebsd-release-ports) 服务。 您可以通过在 `/etc/rc.conf` 中加入下面的行：

```
sdpd_enable="YES"
```

然后用下面的命令来启动 sdpd 服务：

```
# `/etc/rc.d/sdpd start`
```

需要为远端提供蓝牙服务的本地的服务程序会使用本地 SDP 进程注册服务。像这样的程序就有 [rfcomm_pppd(8)](http://www.FreeBSD.org/cgi/man.cgi?query=rfcomm_pppd&sektion=8&manpath=freebsd-release-ports)。 一旦启动它，就会使用本地 SDP 进程注册蓝牙 LAN 服务。

使用本地 SDP 进程注册的服务列表，可以通过本地控制通道发出 SDP 浏览查询获得：

```
# `sdpcontrol -l browse`
```

### 32.4.8. 拨号网络 (DUN) 和使用 PPP(LAN) 层面的网络接入

拨号网络 (DUN) 配置通常与 modem 和手机一起使用。 如下是这一配置所涉及的内容：

*   计算机使用手机或 modem 作为无线 modem 来连接拨号因特网连入服务器， 或者使用其它的拨号服务；

*   计算机使用手机或 modem 接收数据请求。

使用 PPP(LAN) 层面的网络接入常使用在如下情形：

*   单个蓝牙设备的局域网连入；

*   多个蓝牙设备的局域网接入；

*   PC 到 PC (使用基于串行线模拟的 PPP 网络)。

在 FreeBSD 中，两个层面使用 [ppp(8)](http://www.FreeBSD.org/cgi/man.cgi?query=ppp&sektion=8&manpath=freebsd-release-ports) 和 [rfcomm_pppd(8)](http://www.FreeBSD.org/cgi/man.cgi?query=rfcomm_pppd&sektion=8&manpath=freebsd-release-ports) (一种封装器，可以将 RFCOMM 蓝牙连接转换为 PPP 可操作的东西) 来实现。 在使用任何层面之前，一个新的 PPP 标识必须在 `/etc/ppp/ppp.conf` 中建立。 想要实例请参考 [rfcomm_pppd(8)](http://www.FreeBSD.org/cgi/man.cgi?query=rfcomm_pppd&sektion=8&manpath=freebsd-release-ports)。

在下面的例子中，[rfcomm_pppd(8)](http://www.FreeBSD.org/cgi/man.cgi?query=rfcomm_pppd&sektion=8&manpath=freebsd-release-ports) 用来在 NUN RFCOMM 通道上打开一个到 BD_ADDR 为 00:80:37:29:19:a4 的设备的 RFCOMM 连接。具体的 RFCOMM 通道号要通过 SDP 从远端设备获得。也可以手动指定通 RFCOMM，这种情况下 [rfcomm_pppd(8)](http://www.FreeBSD.org/cgi/man.cgi?query=rfcomm_pppd&sektion=8&manpath=freebsd-release-ports) 将不能执行 SDP 查询。使用 [sdpcontrol(8)](http://www.FreeBSD.org/cgi/man.cgi?query=sdpcontrol&sektion=8&manpath=freebsd-release-ports) 来查找远端设备上的 RFCOMM 通道。

```
# `rfcomm_pppd -a 00:80:37:29:19:a4 -c -C dun -l rfcomm-dialup`
```

为了提供 PPP(LAN) 网络接入服务，必须运行 [sdpd(8)](http://www.FreeBSD.org/cgi/man.cgi?query=sdpd&sektion=8&manpath=freebsd-release-ports) 服务。一个新的 LAN 客户端条目必须在 `/etc/ppp/ppp.conf` 文件中建立。 想要实例请参考 [rfcomm_pppd(8)](http://www.FreeBSD.org/cgi/man.cgi?query=rfcomm_pppd&sektion=8&manpath=freebsd-release-ports)。 最后，在有效地通道号上开始 RFCOMM PPP 服务。 RFCOMM PPP 服务会使用本地 SDP 进程自动注册蓝牙 LAN 服务。下面的例子展示如何启动 RFCOMM PPP 服务。

```
# `rfcomm_pppd -s -C 7 -l rfcomm-server`
```

### 32.4.9. OBEX 对象推送 (OBEX Object Push - OPUSH) 层面

OBEX 协议被广泛地用于移动设备之间简单的文件传输。 它的主要用处是在红外线通信领域， 被用于笔记本或手持设备之间的一般文件传输。

OBEX 服务器和客户端由第三方软件包 obexapp 实现，它可以从 [comms/obexapp](http://www.freebsd.org/cgi/url.cgi?ports/comms/obexapp/pkg-descr) port 安装。

OBEX 客户端用于向 OBEX 服务器推入或接出对象。 一个对像可以是(举个例子)商业卡片或约会。 OBEX 客户能通过 SDP 从远程设备取得 RFCOMM 通道号。这可以通过指定服务名代替 RFCOMM 通道号来完成。支持的服务名是有：IrMC、FTRN 和 OPUSH。 也可以用数字来指定 RFCOMM 通道号。下面是一个 OBEX 会话的例子，一个设备信息对像从手机中被拉出， 一个新的对像被推入手机的目录。

```
% `obexapp -a 00:80:37:29:19:a4 -C IrMC`
obex> get telecom/devinfo.txt devinfo-t39.txt
Success, response: OK, Success (0x20)
obex> put new.vcf
Success, response: OK, Success (0x20)
obex> di
Success, response: OK, Success (0x20)
```

为了提供 OBEX 推入服务，[sdpd(8)](http://www.FreeBSD.org/cgi/man.cgi?query=sdpd&sektion=8&manpath=freebsd-release-ports) 必须处于运行状态。必须创建一个根目录用于存放所有进入的对象。 根文件夹的默认路径是 `/var/spool/obex`。 最后，在有效的 RFCOMM 通道号上开始 OBEX 服务。OBEX 服务会使用 SDP 进程自动注册 OBEX 对象推送 (OBEX Object Push) 服务。 下面的例子展示如何启动 OBEX 服务。

```
# `obexapp -s -C 10`
```

### 32.4.10. 串口(SP)层面

串口(SP)层面允许蓝牙设备完成 RS232 (或类似) 串口线的仿真。 这个层面所涉及到情形是， 通过虚拟串口使用蓝牙代替线缆来处理以前的程序。

工具 [rfcomm_sppd(1)](http://www.FreeBSD.org/cgi/man.cgi?query=rfcomm_sppd&sektion=1&manpath=freebsd-release-ports) 来实现串口层。 “Pseudo tty” 用来作为虚拟的串口。 下面的例子展示如何连接远程设备的串口服务。 注意您不必指定 RFCOMM 通道――[rfcomm_sppd(1)](http://www.FreeBSD.org/cgi/man.cgi?query=rfcomm_sppd&sektion=1&manpath=freebsd-release-ports) 能够通过 SDP 从远端设备那里获得。 如果您想代替它的话，可以在命令行里指定 RFCOMM 通道来实现：

```
# `rfcomm_sppd -a 00:07:E0:00:0B:CA -t /dev/ttyp6`
rfcomm_sppd[94692]: Starting on /dev/ttyp6...
```

一旦连接上，“pseudo tty”就可以充当串口了：

```
# `cu -l ttyp6`
```

### 32.4.11. 问题解答

#### 32.4.11.1. 不能连接远端设备

一些较老的蓝牙设备并不支持角色转换 (role switching)。默认情况下，FreeBSD 接受一个新的连接时， 它会尝试进行角色转换并成为主控端 (master)。 不支持角色转换的设备将无法连接。 注意角色转换是在新连接建立时运行的， 因此如果远程设备不支持角色转换，就不可能向它发出请求。 一个 HCI 选项用来在本地端禁用角色转换。

```
# `hccontrol -n ubt0hci write_node_role_switch 0`
```

#### 32.4.11.2. 如果有错， 能否知道到底正在发生什么？

可以。 需要借助第三方软件包 hcidump， 它可以通过 [comms/hcidump](http://www.freebsd.org/cgi/url.cgi?ports/comms/hcidump/pkg-descr) port 来安装。 hcidump 工具和 [tcpdump(1)](http://www.FreeBSD.org/cgi/man.cgi?query=tcpdump&sektion=1&manpath=freebsd-release-ports) 非常相像。 它可以用来显示蓝牙数据包的内容， 并将其记录到文件中。

## 32.5. 桥接

原作 Andrew Thompson.

### 32.5.1. 简介

有时， 会有需要将一个物理网络分成两个独立的网段， 而不是创建新的 IP 子网， 并将其通过路由器相连。 以这种方式连接两个网络的设备称为 “网桥 (bridge)”。 有两个网络接口的 FreeBSD 系统可以作为网桥来使用。

网桥通过学习每个网络接口上的 MAC 层地址 (以太网地址) 工作。 只当数据包的源地址和目标地址处于不同的网络时， 网桥才进行转发。

在很多方面，网桥就像一个带有很少端口的以太网交换机。

### 32.5.2. 适合桥接的情况

适合使用网桥的， 有许多种不同的情况。

#### 32.5.2.1. 使多个网络相互联通

网桥的基本操作是将两个或多个网段连接在一起。 由于各式各样的原因， 人们会希望使用一台真正的计算机， 而不是网络设备来充任网桥的角色， 常见的原因包括线缆的限制、 需要进行防火墙， 或为虚拟机网络接口连接虚拟网络。 网桥也可以将无线网卡以 hostap 模式接入有线网络。

#### 32.5.2.2. 过滤/数据整形防火墙

使用防火墙的常见情形是无需进行路由或网络地址转换的情况 (NAT)。

举例来说， 一家通过 DSL 或 ISDN 连接到 ISP 的小公司， 拥有 13 个 ISP 分配的全局 IP 地址和 10 台 PC。 在这种情况下， 由于划分子网的问题， 采用路由来实现防火墙会比较困难。

基于网桥的防火墙可以串接在 DSL/ISDN 路由器的后面， 而无需考虑 IP 编制的问题。

#### 32.5.2.3. 网络监视

网桥可以用于连接两个不同的网段， 并用于监视往返的以太网帧。 这可以通过在网桥接口上使用 [bpf(4)](http://www.FreeBSD.org/cgi/man.cgi?query=bpf&sektion=4&manpath=freebsd-release-ports)/[tcpdump(1)](http://www.FreeBSD.org/cgi/man.cgi?query=tcpdump&sektion=1&manpath=freebsd-release-ports)， 或通过将全部以太网帧复制到另一个网络接口 (span 口) 来实现。

#### 32.5.2.4. 2 层 VPN

通过 IP 连接的网桥， 可以利用 EtherIP 隧道或基于 [tap(4)](http://www.FreeBSD.org/cgi/man.cgi?query=tap&sektion=4&manpath=freebsd-release-ports) 的解决方案， 如 OpenVPN 可以将两个以太网连接到一起。

#### 32.5.2.5. 2 层 冗余

网络可以通过多条链路连接在一起， 并使用生成树协议 (Spanning Tree Protocol) 来阻止多余的通路。 为使以太网能够正确工作， 两个设备之间应该只有一条激活通路， 而生成树能够检测环路， 并将多余的链路置为阻断状态。 当激活通路断开时， 协议能够计算另外一棵树， 并重新激活阻断的通路， 以恢复到网络各点的连通性。

### 32.5.3. 内核配置

这一节主要介绍 [if_bridge(4)](http://www.FreeBSD.org/cgi/man.cgi?query=if_bridge&sektion=4&manpath=freebsd-release-ports) 网桥实现。 除此之外， 还有一个基于 netgraph 的网桥实现， 如欲了解进一步细节， 请参见联机手册 [ng_bridge(4)](http://www.FreeBSD.org/cgi/man.cgi?query=ng_bridge&sektion=4&manpath=freebsd-release-ports)。

网桥驱动是一个内核模块， 并会随使用 [ifconfig(8)](http://www.FreeBSD.org/cgi/man.cgi?query=ifconfig&sektion=8&manpath=freebsd-release-ports) 创建网桥接口时自动加载。 您也可以将 `device if_bridge` 加入到内核配置文件中， 以便将其静态联编进内核。

包过滤可以通过使用了 [pfil(9)](http://www.FreeBSD.org/cgi/man.cgi?query=pfil&sektion=9&manpath=freebsd-release-ports) 框架的任意一种防火墙软件包来完成。 这些防火墙可以以模块形式加载， 也可以静态联编进内核。

通过配合 [altq(4)](http://www.FreeBSD.org/cgi/man.cgi?query=altq&sektion=4&manpath=freebsd-release-ports) 和 [dummynet(4)](http://www.FreeBSD.org/cgi/man.cgi?query=dummynet&sektion=4&manpath=freebsd-release-ports)， 网桥也可以用于流量控制。

### 32.5.4. 启用网桥

网桥是通过接口复制来创建的。 您可以使用 [ifconfig(8)](http://www.FreeBSD.org/cgi/man.cgi?query=ifconfig&sektion=8&manpath=freebsd-release-ports) 来创建网桥接口， 如果内核不包括网桥驱动， 则它会自动将其载入。

```
# `ifconfig bridge create`
bridge0
# `ifconfig bridge0`
bridge0: flags=8802<BROADCAST,SIMPLEX,MULTICAST> metric 0 mtu 1500
        ether 96:3d:4b:f1:79:7a
        id 00:00:00:00:00:00 priority 32768 hellotime 2 fwddelay 15
        maxage 20 holdcnt 6 proto rstp maxaddr 100 timeout 1200
        root id 00:00:00:00:00:00 priority 0 ifcost 0 port 0
```

如此就建立了一个网桥接口， 并为其随机分配了以太网地址。 `maxaddr` 和 `timeout` 参数能够控制网桥在转发表中保存多少个 MAC 地址， 以及表项中主机的过期时间。 其他参数控制生成树的运转方式。

将成员网络接口加入网桥。 为了让网桥能够为所有网桥成员接口转发包， 网桥接口和所有成员接口都需要处于启用状态：

```
# `ifconfig bridge0 addm fxp0 addm fxp1 up`
# `ifconfig fxp0 up`
# `ifconfig fxp1 up`
```

网桥现在会在 `fxp0` 和 `fxp1` 之间转发以太网帧。 等效的 `/etc/rc.conf` 配置如下， 如此配置将在系统启动时创建同样的网桥。

```
cloned_interfaces="bridge0"
ifconfig_bridge0="addm fxp0 addm fxp1 up"
ifconfig_fxp0="up"
ifconfig_fxp1="up"
```

如果网桥主机需要 IP 地址， 则应将其绑在网桥设备本身， 而不是某个成员设备上。 这可以通过静态设置或 DHCP 来完成：

```
# `ifconfig bridge0 inet 192.168.0.1/24`
```

除此之外， 也可以为网桥接口指定 IPv6 地址。

### 32.5.5. 防火墙

当启用包过滤时， 通过网桥的包可以分别在进入的网络接口、 网桥接口和发出的网络接口上进行过滤。 这些阶段均可禁用。 当包的流向很重要时， 最好在成员接口而非网桥接口上配置防火墙。

网桥上可以进行许多配置以决定非 IP 及 ARP 包能否通过， 以及通过 IPFW 实现二层防火墙。 请参见 [if_bridge(4)](http://www.FreeBSD.org/cgi/man.cgi?query=if_bridge&sektion=4&manpath=freebsd-release-ports) 联机手册以了解进一步的细节。

### 32.5.6. 生成树

网桥驱动实现了快速生成树协议 (RSTP 或 802.1w)， 并与较早的生成树协议 (STP) 兼容。 生成树可以用来在网络拓扑中检测并消除环路。 RSTP 提供了比传统 STP 更快的生成树覆盖速度， 这种协议会在相邻的交换机之间交换信息， 以迅速进入转发状态， 并避免产生环路。 FreeBSD 支持以 RSTP 和 STP 模式运行， 而 RSTP 是默认模式。

使用 `stp` 命令可以在成员接口上启用生成树。 对包含 `fxp0` 和 `fxp1` 的网桥， 可以用下列命令启用 STP：

```
# `ifconfig bridge0 stp fxp0 stp fxp1`
bridge0: flags=8843<UP,BROADCAST,RUNNING,SIMPLEX,MULTICAST> metric 0 mtu 1500
        ether d6:cf:d5:a0:94:6d
        id 00:01:02:4b:d4:50 priority 32768 hellotime 2 fwddelay 15
        maxage 20 holdcnt 6 proto rstp maxaddr 100 timeout 1200
        root id 00:01:02:4b:d4:50 priority 32768 ifcost 0 port 0
        member: fxp0 flags=1c7<LEARNING,DISCOVER,STP,AUTOEDGE,PTP,AUTOPTP>
                port 3 priority 128 path cost 200000 proto rstp
                role designated state forwarding
        member: fxp1 flags=1c7<LEARNING,DISCOVER,STP,AUTOEDGE,PTP,AUTOPTP>
                port 4 priority 128 path cost 200000 proto rstp
                role designated state forwarding
```

网桥的生成树 ID 为 `00:01:02:4b:d4:50` 而优先级为 `32768`。 其中 `root id` 与生成树相同， 表示这是作为生成树根的网桥。

另一个网桥也启用了生成树：

```
bridge0: flags=8843<UP,BROADCAST,RUNNING,SIMPLEX,MULTICAST> metric 0 mtu 1500
        ether 96:3d:4b:f1:79:7a
        id 00:13:d4:9a:06:7a priority 32768 hellotime 2 fwddelay 15
        maxage 20 holdcnt 6 proto rstp maxaddr 100 timeout 1200
        root id 00:01:02:4b:d4:50 priority 32768 ifcost 400000 port 4
        member: fxp0 flags=1c7<LEARNING,DISCOVER,STP,AUTOEDGE,PTP,AUTOPTP>
                port 4 priority 128 path cost 200000 proto rstp
                role root state forwarding
        member: fxp1 flags=1c7<LEARNING,DISCOVER,STP,AUTOEDGE,PTP,AUTOPTP>
                port 5 priority 128 path cost 200000 proto rstp
                role designated state forwarding
```

这里的 `root id 00:01:02:4b:d4:50 priority 32768 ifcost 400000 port 4` 表示根网桥是前面的 `00:01:02:4b:d4:50`， 而从此网桥出发的通路代价为 `400000`, 此通路到根网桥是通过 `port 4` 即 `fxp0` 连接的。

### 32.5.7. 网桥的高级用法

#### 32.5.7.1. 重建流量流

网桥支持监视模式， 在 [bpf(4)](http://www.FreeBSD.org/cgi/man.cgi?query=bpf&sektion=4&manpath=freebsd-release-ports) 处理之后会将包丢弃， 而不是继续处理或转发。 这可以用于将两个或多个接口上的输入转化为一个 [bpf(4)](http://www.FreeBSD.org/cgi/man.cgi?query=bpf&sektion=4&manpath=freebsd-release-ports) 流。 在将两个独立的接口上的传输的 RX/TX 信号重整为一个时， 这会非常有用。

如果希望将四个网络接口上的输入转成一个流：

```
# `ifconfig bridge0 addm fxp0 addm fxp1 addm fxp2 addm fxp3 monitor up`
# `tcpdump -i bridge0`
```

#### 32.5.7.2. 镜像口 (Span port)

网桥收到的每个以太网帧都可以发到镜像口上。 网桥上的镜像口数量没有限制， 如果一个接口已经被配置为镜像口， 则它就不能再作为网桥的成员口来使用。 这种用法主要是为与网桥镜像口相连的监听机配合使用。

如果希望将所有帧发到名为 `fxp4` 的接口上：

```
# `ifconfig bridge0 span fxp4`
```

#### 32.5.7.3. 专用接口 (Private interface)

专用接口不会转发流量到除专用接口之外的其他端口。 这些流量会无条件地阻断， 因此包括 ARP 在内的以太网帧均不会被转发。 如果需要选择性地阻断流量， 则应使用防火墙。

#### 32.5.7.4. 自学习接口 (Sticky Interfaces)

如果网桥的成员接口标记为自学习， 则动态学习的地址项一旦进入转发快取缓存， 即被认为是静态项。 自学习项不会从快取缓存中过期或替换掉， 即使地址在另一接口上出现也是如此。 这使得不必事先发布转发表， 也能根据学习结果得到静态项的有点， 但在这些网段被网桥看到的客户机， 就不能漫游至另一网段了。

另一种用法是将网桥与 VLAN 功能连用， 这样客户网络会被隔离在一边， 而不会浪费 IP 地址空间。 考虑 `CustomerA` 在 `vlan100` 上， 而 `CustomerB` 则在 `vlan101` 上。 网桥地址为 `192.168.0.1`， 同时作为 internet 路由器使用。

```
# `ifconfig bridge0 addm vlan100 sticky vlan100 addm vlan101 sticky vlan101`
# `ifconfig bridge0 inet 192.168.0.1/24`
```

两台客户机均将 `192.168.0.1` 作为默认网关， 由于网桥快取缓存是自学习的， 因而它们无法伪造 MAC 地址来截取其他客户机的网络流量。

在 VLAN 之间的通讯可以通过专用接口 (或防火墙) 来阻断：

```
# `ifconfig bridge0 private vlan100 private vlan101`
```

这样这些客户机就完全相互隔离了。 可以使用整个的 `/24` 地址空间， 而无需划分子网。

#### 32.5.7.5. 地址限制

接口后的源 MAC 地址数量是可以控制的。 一旦到达了限制未知源地址的包将会被丢弃， 直至现有缓存中的一项过期或被移除。

下面的例子是设置 `CustomerA` 在 `vlan100` 上可连接的以太网设备最大值为 10。

```
# `ifconfig bridge0 ifmaxaddr vlan100 10`
```

#### 32.5.7.6. SNMP 管理

网桥接口和 STP 参数能够由 FreeBSD 基本系统的 SNMP 守护进程进行管理。导出的网桥 MIB 符和 IETF 标准， 所以任何 SNMP 客户端或管理包都可以被用来接收数据。

在网桥机器上从`/etc/snmp.config` 文件中去掉以下这行的注释 `begemotSnmpdModulePath."bridge" = "/usr/lib/snmp_bridge.so"` 并启动 bsnmpd 守护进程。 其他的配置选项诸如 community names 和 access lists 可能也许也需要修改。 参阅 [bsnmpd(1)](http://www.FreeBSD.org/cgi/man.cgi?query=bsnmpd&sektion=1&manpath=freebsd-release-ports) 和 [snmp_bridge(3)](http://www.FreeBSD.org/cgi/man.cgi?query=snmp_bridge&sektion=3&manpath=freebsd-release-ports) 获取更多信息。

以下的例子中使用了 Net-SNMP 软件 ([net-mgmt/net-snmp](http://www.freebsd.org/cgi/url.cgi?ports/net-mgmt/net-snmp/pkg-descr)) 来查询一个网桥，当然同样也能够使用 port [net-mgmt/bsnmptools](http://www.freebsd.org/cgi/url.cgi?ports/net-mgmt/bsnmptools/pkg-descr)。 在 SNMP 客户端 Net-SNMP 的配置文件 `$HOME/.snmp/snmp.conf` 中 加入以下几行来导入网桥的 MIB 定义：

```
mibdirs +/usr/share/snmp/mibs
mibs +BRIDGE-MIB:RSTP-MIB:BEGEMOT-MIB:BEGEMOT-BRIDGE-MIB
```

通过 IETF BRIDGE-MIB(RFC4188) 监测一个单独的网桥

```
% `snmpwalk -v 2c -c public bridge1.example.com mib-2.dot1dBridge`
BRIDGE-MIB::dot1dBaseBridgeAddress.0 = STRING: 66:fb:9b:6e:5c:44
BRIDGE-MIB::dot1dBaseNumPorts.0 = INTEGER: 1 ports
BRIDGE-MIB::dot1dStpTimeSinceTopologyChange.0 = Timeticks: (189959) 0:31:39.59 centi-seconds
BRIDGE-MIB::dot1dStpTopChanges.0 = Counter32: 2
BRIDGE-MIB::dot1dStpDesignatedRoot.0 = Hex-STRING: 80 00 00 01 02 4B D4 50
...
BRIDGE-MIB::dot1dStpPortState.3 = INTEGER: forwarding(5)
BRIDGE-MIB::dot1dStpPortEnable.3 = INTEGER: enabled(1)
BRIDGE-MIB::dot1dStpPortPathCost.3 = INTEGER: 200000
BRIDGE-MIB::dot1dStpPortDesignatedRoot.3 = Hex-STRING: 80 00 00 01 02 4B D4 50
BRIDGE-MIB::dot1dStpPortDesignatedCost.3 = INTEGER: 0
BRIDGE-MIB::dot1dStpPortDesignatedBridge.3 = Hex-STRING: 80 00 00 01 02 4B D4 50
BRIDGE-MIB::dot1dStpPortDesignatedPort.3 = Hex-STRING: 03 80
BRIDGE-MIB::dot1dStpPortForwardTransitions.3 = Counter32: 1
RSTP-MIB::dot1dStpVersion.0 = INTEGER: rstp(2)
```

`dot1dStpTopChanges.0`的值为 2 意味着 STP 网桥拓扑改变了 2 次，拓扑的改变表示 1 个或多个 网络中的连接改变或失效并且有一个新树生成。 `dot1dStpTimeSinceTopologyChange.0` 的值则能够显示这是何时改变的。

监测多个网桥接口可以使用 private BEGEMOT-BRIDGE-MIB：

```
% `snmpwalk -v 2c -c public bridge1.example.com`
enterprises.fokus.begemot.begemotBridge
BEGEMOT-BRIDGE-MIB::begemotBridgeBaseName."bridge0" = STRING: bridge0
BEGEMOT-BRIDGE-MIB::begemotBridgeBaseName."bridge2" = STRING: bridge2
BEGEMOT-BRIDGE-MIB::begemotBridgeBaseAddress."bridge0" = STRING: e:ce:3b:5a:9e:13
BEGEMOT-BRIDGE-MIB::begemotBridgeBaseAddress."bridge2" = STRING: 12:5e:4d:74:d:fc
BEGEMOT-BRIDGE-MIB::begemotBridgeBaseNumPorts."bridge0" = INTEGER: 1
BEGEMOT-BRIDGE-MIB::begemotBridgeBaseNumPorts."bridge2" = INTEGER: 1
...
BEGEMOT-BRIDGE-MIB::begemotBridgeStpTimeSinceTopologyChange."bridge0" = Timeticks: (116927) 0:19:29.27 centi-seconds
BEGEMOT-BRIDGE-MIB::begemotBridgeStpTimeSinceTopologyChange."bridge2" = Timeticks: (82773) 0:13:47.73 centi-seconds
BEGEMOT-BRIDGE-MIB::begemotBridgeStpTopChanges."bridge0" = Counter32: 1
BEGEMOT-BRIDGE-MIB::begemotBridgeStpTopChanges."bridge2" = Counter32: 1
BEGEMOT-BRIDGE-MIB::begemotBridgeStpDesignatedRoot."bridge0" = Hex-STRING: 80 00 00 40 95 30 5E 31
BEGEMOT-BRIDGE-MIB::begemotBridgeStpDesignatedRoot."bridge2" = Hex-STRING: 80 00 00 50 8B B8 C6 A9
```

通过 `mib-2.dot1dBridge` 子树改变正在被监测的网桥接口：

```
% `snmpset -v 2c -c private bridge1.example.com`
BEGEMOT-BRIDGE-MIB::begemotBridgeDefaultBridgeIf.0 s bridge2
```

## 32.6. 链路聚合与故障转移

Written by Andrew Thompson.

### 32.6.1. 介绍

使用 [lagg(4)](http://www.FreeBSD.org/cgi/man.cgi?query=lagg&sektion=4&manpath=freebsd-release-ports) 接口， 能够将多个网络接口聚合为一个虚拟接口， 以提供容灾和高速连接的能力。

### 32.6.2. 运行模式

Failover (故障转移)

只通过主网口收发数据。 如果主网口不可用， 则使用下一个激活的网口。 您在这里加入的第一个网口便会被视为主网口； 此后加入的其他网口， 则会被视为故障转移的备用网口。 如果发生故障转移之后， 原先的网口又恢复了可用状态， 则它仍会作为主网口使用。

Cisco® Fast EtherChannel®

Cisco® Fast EtherChannel® (FEC) 是一种静态配置， 并不进行节点间协商或交换以太网帧来监控链路情况。 如果交换机支持 LACP， 则应使用后者而非这种配置。

FEC 将输出流量在激活的网口之间以协议头散列信息为依据分拆， 并接收来自任意激活网口的入流量。 散列信息包含以太网源地址、 目的地址， 以及 (如果有的话) VLAN tag 和 IPv4/IPv6 源地址及目的地址信息。

LACP

支持 IEEE® 802.3ad 链路聚合控制协议 (LACP) 和标记协议。 LACP 能够在节点与若干链路聚合组之间协商链路。 每一个链路聚合组 (LAG) 由一组相同速度、 以全双工模式运行的网口组成。 流量在 LAG 中的网口之间， 会以总速度最大的原则进行分摊。 当物理链路发生变化时， 链路聚合会迅速适应变动形成新的配置。

LACP 也是将输出流量在激活的网口之间以协议头散列信息为依据分拆， 并接收来自任意激活网口的入流量。 散列信息包含以太网源地址、 目的地址， 以及 (如果有的话) VLAN tag 和 IPv4/IPv6 源地址及目的地址信息。

Loadbalance (负载均衡)

这是 *FEC* 模式的别名。

Round-robin (轮转)

将输出流量以轮转方式在所有激活端口之间调度， 并从任意激活端口接收进入流量。 这种模式违反了以太网帧排序规则， 因此应小心使用。

### 32.6.3. 例子

例 32.1. 与 Cisco® 交换机配合完成 LACP 链路聚合

在这个例子中， 我们将 FreeBSD 的两个网口作为一个负载均衡和故障转移链路聚合组接到交换机上。 在此基础上， 还可以增加更多的网口， 以提高吞吐量和故障容灾能力。 由于以太网链路上两节点间的帧序是强制性的， 因此两个节点之间的连接速度， 会取决于一块网卡的最大速度。 传输算法会尽量采用更多的信息， 以便将不同的网络流量分摊到不同的网络接口上， 并平衡不同网口的负载。

在 Cisco® 交换机上将 *`FastEthernet0/1`* 和 *`FastEthernet0/2`* 这两个网口添加到 channel-group *`1`*：

```
`interface FastEthernet0/1
 channel-group 1 mode active
 channel-protocol lacp`
!
`interface FastEthernet0/2
 channel-group 1 mode active
 channel-protocol lacp`
```

使用 *`fxp0`* 和 *`fxp1`* 创建 [lagg(4)](http://www.FreeBSD.org/cgi/man.cgi?query=lagg&sektion=4&manpath=freebsd-release-ports) 接口， 启用这个接口并配置 IP 地址 *`10.0.0.3/24`*：

```
# `ifconfig fxp0 up`
# `ifconfig fxp1 up`
# `ifconfig lagg0 create` 
# `ifconfig lagg0 up laggproto lacp laggport fxp0 laggport fxp1 10.0.0.3/24`
```

用下面的命令查看接口状态：

```
# `ifconfig lagg0`
```

标记为 *ACTIVE* 的接口是激活据合组的部分， 这表示它们已经完成了与远程交换机的协商， 同时， 流量将通过这些接口来收发。 在 [ifconfig(8)](http://www.FreeBSD.org/cgi/man.cgi?query=ifconfig&sektion=8&manpath=freebsd-release-ports) 的详细输出中会给出 LAG 的标识。

```
lagg0: flags=8843<UP,BROADCAST,RUNNING,SIMPLEX,MULTICAST> metric 0 mtu 1500
        options=8<VLAN_MTU>
        ether 00:05:5d:71:8d:b8
        media: Ethernet autoselect
        status: active
        laggproto lacp
        laggport: fxp1 flags=1c<ACTIVE,COLLECTING,DISTRIBUTING>
        laggport: fxp0 flags=1c<ACTIVE,COLLECTING,DISTRIBUTING>
```

如果需要查看交换机上的端口状态， 则应使用 **`show lacp neighbor`** 命令：

```
switch# show lacp neighbor
Flags:  S - Device is requesting Slow LACPDUs
        F - Device is requesting Fast LACPDUs
        A - Device is in Active mode       P - Device is in Passive mode

Channel group 1 neighbors

Partner's information:

                  LACP port                        Oper    Port     Port
Port      Flags   Priority  Dev ID         Age     Key     Number   State
Fa0/1     SA      32768     0005.5d71.8db8  29s    0x146   0x3      0x3D
Fa0/2     SA      32768     0005.5d71.8db8  29s    0x146   0x4      0x3D
```

如欲查看进一步的详情， 则需要使用 **`show lacp neighbor detail`** 命令。

如果希望在系统重启时保持这些设置， 应在 `/etc/rc.conf` 中增加如下配置：

```
ifconfig_*`fxp0`*="up"
ifconfig_*`fxp1`*="up"
cloned_interfaces="lagg0"
ifconfig_lagg0="laggproto lacp laggport *`fxp0`* laggport *`fxp1`* *`10.0.0.3/24`*"

```

例 32.2. 故障转移模式

故障转移模式中， 当首选链路发生问题时， 会自动切换到备用端口。 首先启用成员接口， 接着是配置 [lagg(4)](http://www.FreeBSD.org/cgi/man.cgi?query=lagg&sektion=4&manpath=freebsd-release-ports) 接口， 其中， 使用 *`fxp0`* 作为首选接口， *`fxp1`* 作为备用接口， 并在整个接口上配置 IP 地址 *`10.0.0.15/24`*：

```
# `ifconfig fxp0 up`
# `ifconfig fxp1 up`
# `ifconfig lagg0 create`
# `ifconfig lagg0 up laggproto failover laggport fxp0 laggport fxp1 10.0.0.15/24`
```

创建成功之后， 接口状态会是类似下面这样， 主要的区别是 MAC 地址和设备名：

```
# `ifconfig lagg0`
lagg0: flags=8843<UP,BROADCAST,RUNNING,SIMPLEX,MULTICAST> metric 0 mtu 1500
        options=8<VLAN_MTU>
        ether 00:05:5d:71:8d:b8
        inet 10.0.0.15 netmask 0xffffff00 broadcast 10.0.0.255
        media: Ethernet autoselect
        status: active
        laggproto failover
        laggport: fxp1 flags=0<>
        laggport: fxp0 flags=5<MASTER,ACTIVE>
```

系统将在 *`fxp0`* 上进行流量的收发。 如果 *`fxp0`* 的连接中断， 则 *`fxp1`* 会自动成为激活连接。 如果主端口的连接恢复， 则它又会成为激活连接。

如果希望在系统重启时保持这些设置， 应在 `/etc/rc.conf` 中增加如下配置：

```
ifconfig_*`fxp0`*="up"
ifconfig_*`fxp1`*="up"
cloned_interfaces="lagg0"
ifconfig_lagg0="laggproto failover laggport *`fxp0`* laggport *`fxp1`* *`10.0.0.15/24`*"

```

例 32.3. 有线网络和无线网络接口间的自动切换

对于使用笔记本的用户来说， 通常会希望使用无线网络接口作为备用接口， 以便在有线网络不可用时继续保持网络连接。 通过使用 [lagg(4)](http://www.FreeBSD.org/cgi/man.cgi?query=lagg&sektion=4&manpath=freebsd-release-ports)， 我们可以只使用一个 IP 地址的情况下， 优先使用性能和安全性都更好的有线网络， 同时保持通过无线网络连接来传输数据的能力。

要实现这样的目的， 就需要将用于连接无线网络的物理接口的 MAC 地址修改为与所配置的 [lagg(4)](http://www.FreeBSD.org/cgi/man.cgi?query=lagg&sektion=4&manpath=freebsd-release-ports) 一致， 后者是从主网络接口， 也就是有线网络接口， 继承而来。

在这个配置中， 我们将优先使用有线网络接口 *`bge0`* 作为主网络接口， 而将无线网络接口 *`wlan0`* 作为备用网络接口。 这里的 *`wlan0`* 使用的物理设备是 *`iwn0`*， 我们需要将它的 MAC 地址修改为与有线网络接口一致。 为了达到这个目的首先要得到有线网络接口上的 MAC 地址：

```
# `ifconfig bge0`
bge0: flags=8843<UP,BROADCAST,RUNNING,SIMPLEX,MULTICAST> metric 0 mtu 1500
	options=19b<RXCSUM,TXCSUM,VLAN_MTU,VLAN_HWTAGGING,VLAN_HWCSUM,TSO4>
	ether 00:21:70:da:ae:37
	inet6 fe80::221:70ff:feda:ae37%bge0 prefixlen 64 scopeid 0x2
	nd6 options=29<PERFORMNUD,IFDISABLED,AUTO_LINKLOCAL>
	media: Ethernet autoselect (1000baseT <full-duplex>)
	status: active
```

您可能需要将 *`bge0`* 改为您系统上实际使用的接口， 并从输出结果中的 `ether` 这行找出有线网络的 MAC 地址。 接着是修改物理的无线网络接口， *`iwn0`*：

```
# `ifconfig iwn0 ether 00:21:70:da:ae:37`
```

启用无线网络接口， 但不在其上配置 IP 地址：

```
# `ifconfig wlan0 create wlandev iwn0 ssid my_router up`
```

启用 *`bge0`* 接口。 创建 [lagg(4)](http://www.FreeBSD.org/cgi/man.cgi?query=lagg&sektion=4&manpath=freebsd-release-ports) 接口， 其中 *`bge0`* 作为主网络接口， 而以 *`wlan0`* 作为备选接口：

```
# `ifconfig bge0 up`
# `ifconfig lagg0 create`
# `ifconfig lagg0 up laggproto failover laggport bge0 laggport wlan0`
```

新创建的接口的状态如下， 您系统上的 MAC 地址和设备名等可能会有所不同：

```
# `ifconfig lagg0`
lagg0: flags=8843<UP,BROADCAST,RUNNING,SIMPLEX,MULTICAST> metric 0 mtu 1500
        options=8<VLAN_MTU>
        ether 00:21:70:da:ae:37
        media: Ethernet autoselect
        status: active
        laggproto failover
        laggport: wlan0 flags=0<>
        laggport: bge0 flags=5<MASTER,ACTIVE>
```

接着用 DHCP 客户端来获取 IP 地址：

```
# `dhclient lagg0`
```

如果希望在系统重启时保持这些设置， 应在 `/etc/rc.conf` 中增加如下配置：

```
ifconfig_bge0="up"
ifconfig_iwn0="ether 00:21:70:da:ae:37"
wlans_iwn0="wlan0"
ifconfig_wlan0="WPA"
cloned_interfaces="lagg0"
ifconfig_lagg0="laggproto failover laggport bge0 laggport wlan0 DHCP"

```

## 32.7. 无盘操作

更新： Jean-François Dockès.重新组织及增强：Alex Dupre.中文翻译：张 雪平 和 袁 苏义.

FreeBSD 主机可以从网络启动而无需本地磁盘就可操作， 使用的是从 NFS 服务器装载的文件系统。 除了标准的配置文件，无需任何的系统修改。 很容易设置这样的系统因为所有必要的元素都很容易得到：

*   至少有两种可能的方法从网络加载内核：

    *   PXE：Intel® 的先启动执行环境 (Preboot eXecution Environment) 系统是一种灵活的引导 ROM 模式，这个 ROM 内建在一些网卡或主板的中。查看 [pxeboot(8)](http://www.FreeBSD.org/cgi/man.cgi?query=pxeboot&sektion=8&manpath=freebsd-release-ports) 以获取更多细节。

    *   Etherboot port ([net/etherboot](http://www.freebsd.org/cgi/url.cgi?ports/net/etherboot/pkg-descr)) 产生通过网络加载内核的可 ROM 代码。这些代码可以烧入网卡上的 PROM 上，或从本地软盘 (或硬盘) 驱动器加载，或从运行着的 MS-DOS® 系统加载。它支持多种网卡。

*   一个样板脚本 (`/usr/share/examples/diskless/clone_root`) 简化了对服务器上的工作站根文件系统的创建和维护。 这个脚本需要少量的自定义，但您能很快的熟悉它。

*   `/etc` 存在标准的系统启动文件用于侦测和支持无盘的系统启动。

*   可以向 NFS 文件或本地磁盘进行交换(如果需要的话)。

设置无盘工作站有许多方法。 有很多相关的元素大部分可以自定义以适合本地情况。 以下将介绍一个完整系统的安装，强调的是简单性和与标准 FreeBSD 启动脚本的兼容。介绍的系统有以下特性：

*   无盘工作站使用一个共享的只读 `/` 文件系统和一个共享的只读`/usr`。

    root 文件系统是一份标准的 FreeBSD 根文件系统 (一般是服务器的)，只是一些配置文件被特定于无盘操作的配置文件覆盖。

    root 文件系统必须可写的部分被 [md(4)](http://www.FreeBSD.org/cgi/man.cgi?query=md&sektion=4&manpath=freebsd-release-ports) 文件系统覆盖。 任何的改写在重启后都会丢失。

*   内核由 etherboot 或 PXE 传送和加载， 有些情况可能会指定使用其中之一。

### 小心:

如上所述，这个系统是不安全的。 它应该处于网络的受保护区域并不被其它主机信任。

这部分所有的信息均在 5.2.1-RELEASE 上测试过。

### 32.7.1. 背景信息

设置无盘工作站相对要简单而又易出错。 有时分析一些原因是很难的。例如：

*   编译时选项在运行时可能产生不同的行为。

*   出错信息经常是加密了的或根本就没有。

在这里， 涉及到的一些背景知识对于可能出现的问题的解决是很有帮助的。

要成功地引导系统还有些操作需要做。

*   机子需要获取初始的参数，如它的 IP 地址、执行文件、服务器名、根路径。这个可以使用 或 BOOTP 协议来完成。 DHCP 是 BOOTP 的兼容扩展， 并使用相同的端口和基本包格式。

    只使用 BOOTP 来配置系统也是可行的。 [bootpd(8)](http://www.FreeBSD.org/cgi/man.cgi?query=bootpd&sektion=8&manpath=freebsd-release-ports) 服务程序被包含在基本的 FreeBSD 系统里。

    不过，DHCP 相比 BOOTP 有几个好处 (更好的配置文件，使用 PXE 的可能性，以及许多其它并不直接相关的无盘操作)， 接着我们会要描述一个 DHCP 配置， 可能的话会利用与使用 [bootpd(8)](http://www.FreeBSD.org/cgi/man.cgi?query=bootpd&sektion=8&manpath=freebsd-release-ports) 相同的例子。这个样板配置会使用 ISC DHCP 软件包 (3.0.1.r12 发行版安装在测试服务器上)。

*   机子需要传送一个或多个程序到本地内存。 TFTP 或 NFS 会被使用。选择 TFTP 还是 NFS 需要在几个地方的“编译时间”选项里设置。 通常的错误源是为文件名指定了错误的协议：TFTP 通常从服务器里的一个单一目录传送所有文件，并需要相对这个目录的文件名。 NFS 需要的是绝对文件路径。

*   介于启动程序和内核之间的可能的部分需要被初始化并执行。 在这部分有几个重要的变量：

    *   PXE 会装入 [pxeboot(8)](http://www.FreeBSD.org/cgi/man.cgi?query=pxeboot&sektion=8&manpath=freebsd-release-ports)――它是 FreeBSD 第三阶段装载器的修改版。 [loader(8)](http://www.FreeBSD.org/cgi/man.cgi?query=loader&sektion=8&manpath=freebsd-release-ports) 会获得许多参数用于系统启动， 并在传送控制之前把它们留在内核环境里。 在这种情况下，使用 `GENERIC` 内核就可能了。

    *   Etherboot 会做很少的准备直接装载内核。 您要使用指定的选项建立 (build) 内核。

    PXE 和 Etherboot 工作得一样的好。 不过， 因为一般情况下内核希望 [loader(8)](http://www.FreeBSD.org/cgi/man.cgi?query=loader&sektion=8&manpath=freebsd-release-ports) 做了更多的事情， PXE 是推荐的方法。

    如果您的 BIOS 和网卡都支持 PXE， 就应该使用它。

*   最后，机子需要访问它的文件系统。 NFS 使用在所有的情况下。

查看 [diskless(8)](http://www.FreeBSD.org/cgi/man.cgi?query=diskless&sektion=8&manpath=freebsd-release-ports) 手册页。

### 32.7.2. 安装说明

#### 32.7.2.1. 配置使用 ISC DHCP

ISC DHCP 服务器可以回应 BOOTP 和 DHCP 的请求。

ISC DHCP 4.2 并不属于基本系统。首先您需要安装 [net/isc-dhcp42-server](http://www.freebsd.org/cgi/url.cgi?ports/net/isc-dhcp42-server/pkg-descr) port 或相应的“包”。

一旦安装了 ISC DHCP， 还需要一个配置文件才能运行 (通常名叫 `/usr/local/etc/dhcpd.conf`)。 这里有个注释过的例子，里边主机 `margaux` 使用 Etherboot， 而主机`corbieres` 使用 PXE：

```
default-lease-time 600;
max-lease-time 7200;
authoritative;

option domain-name "example.com";
option domain-name-servers 192.168.4.1;
option routers 192.168.4.1;

subnet 192.168.4.0 netmask 255.255.255.0 {
  use-host-decl-names on; ![1](img/1.png)
  option subnet-mask 255.255.255.0;
  option broadcast-address 192.168.4.255;

  host margaux {
    hardware ethernet 01:23:45:67:89:ab;
    fixed-address margaux.example.com;
    next-server 192.168.4.4; ![2](img/2.png)
    filename "/data/misc/kernel.diskless"; ![3](img/3.png)
    option root-path "192.168.4.4:/data/misc/diskless"; ![4](img/4.png)
  }
  host corbieres {
    hardware ethernet 00:02:b3:27:62:df;
    fixed-address corbieres.example.com;
    next-server 192.168.4.4;
    filename "pxeboot";
    option root-path "192.168.4.4:/data/misc/diskless";
  }
} 
```

| ![1](img/#co-dhcp-host-name) | 这个选项告诉 dhcpd 发送`host` 里声明的用于无盘主机的主机名的值。 另外可能会增加一个 `option host-name margaux` 到 `host` 声明里。 |
| ![2](img/#co-dhcp-next-server) | `next-server` 正式指定 TFTP 或 NFS 服务用于载入装载器或内核文件 (默认使用的是相同的主机作为 DHCP 服务器)。 |
| ![3](img/#co-dhcp-filename) | `filename` 正式定义这样的文件――etherboot 或 PXE 为执行下一步将装载它。 根据使用的传输方式，它必须要指定。 Etherboot 可以被编译来使用 NFS 或 TFTP。 FreeBSD port 默认配置了 NFS。 PXE 使用 TFTP， 这就是为什么在这里使用相对文件名 (这可能依赖于 TFTP 服务器配置，不过会相当典型)。 同样，PXE 会装载 `pxeboot`， 而不是内核。另外有几个很有意思的可能，如从 FreeBSD CD-ROM 的 `/boot` 目录装载 `pxeboot` (因为 [pxeboot(8)](http://www.FreeBSD.org/cgi/man.cgi?query=pxeboot&sektion=8&manpath=freebsd-release-ports) 能够装载 `GENERIC` 内核，这就使得可以使用 PXE 从远程的 CD-ROM 里启动)。 |
| ![4](img/#co-dhcp-root-path) | `root-path` 选项定义到根 (root) 文件系统的路径，通常是 NFS 符号。当使用 PXE 时，只要您不启用内核里的 BOOTP 选项，可以不管主机的 IP。NFS 服务器然后就如同 TFTP 一样。 |

#### 32.7.2.2. 配置使用 BOOTP

这里紧跟的是一个等效的 bootpd 配置 (减少到一个客户端)。这个可以在 `/etc/bootptab` 里找到。

请注意：为了使用 BOOTP，etherboot 必须使用非默认选项 `NO_DHCP_SUPPORT` 来进行编译，而且 PXE *需要* DHCP。bootpd 的唯一可见的好处是它存在于基本系统中。

```
.def100:\
  :hn:ht=1:sa=192.168.4.4:vm=rfc1048:\
  :sm=255.255.255.0:\
  :ds=192.168.4.1:\
  :gw=192.168.4.1:\
  :hd="/tftpboot":\
  :bf="/kernel.diskless":\
  :rp="192.168.4.4:/data/misc/diskless":

margaux:ha=0123456789ab:tc=.def100
```

#### 32.7.2.3. 使用 Etherboot 准备启动程序

[Etherboot 的网站](http://etherboot.sourceforge.net) 包含有[更多的文档](http://etherboot.sourceforge.net/doc/html/userman/t1.html) ――主要瞄准的是 Linux 系统，但无疑包含有有用的信息。 如下列出的是关于在 FreeBSD 系统里使用 Etherboot。

首先您必须安装[net/etherboot](http://www.freebsd.org/cgi/url.cgi?ports/net/etherboot/pkg-descr) 包或 port。

您可以改变 Etherboot 的配置 (如使用 TFTP 来代替 NFS)， 方法是修改 `Config` 文件――在 Etherboot 源目录里。

对于我们的设置，我们要使用一张启动软盘。 对于其它的方法(PROM，或 MS-DOS®程序)， 请参考 Etherboot 文档。

想要使用启动软盘，先插入一张软盘到安装有 Etherboot 的机器的驱动器里， 然后把当前路径改到 `src` 目录――在 Etherboot 树下， 接着输入：

```
# `gmake bin32/devicetype.fd0`

```

*`devicetype`* 依赖于无盘工作站上的以太网卡的类型。 参考在同一个目录下的 `NIC` 文件确认正确的 *`devicetype`*。

#### 32.7.2.4. 使用 PXE 启动

默认地，[pxeboot(8)](http://www.FreeBSD.org/cgi/man.cgi?query=pxeboot&sektion=8&manpath=freebsd-release-ports) 装载器通过 NFS 装载内核。它可以编译来使用 TFTP――通过在文件 `/etc/make.conf` 里指定 `LOADER_TFTP_SUPPORT` 选项来代替。 请参见 `/usr/share/examples/etc/make.conf` 里的注释 了解如何配置。

除此之外还有两个未说明的 `make.conf` 选项――它可能对于设置一系列控制台无盘机器会有用： `BOOT_PXELDR_PROBE_KEYBOARD`和 `BOOT_PXELDR_ALWAYS_SERIAL`。

当机器启动里，要使用 PXE， 通常需要选择 `Boot from network` 选项――在 BIOS 设置里， 或者在 PC 初始化的时候输入一个功能键 (function key)。

#### 32.7.2.5. 配置 TFTP 和 NFS 服务器

如果您正在使用 PXE 或 Etherboot――配置使用了 TFTP，那么您需要在文件服务器上启用 tftpd：

1.  建立一个目录――从那里 tftpd 可以提供文件服务，如 `/tftpboot`。

2.  把这一行加入到 `/etc/inetd.conf`里：

    ```
    tftp	dgram	udp	wait	root	/usr/libexec/tftpd	tftpd -l -s /tftpboot
    ```

    ### 注意:

    好像有一些版本的 PXE 需要 TCP 版本的 TFTP。 在这种情况下，加入第二行，使用 `stream tcp` 来代替 `dgram udp`。

3.  让 inetd 重读其配置文件。 要正确执行这个命令， 在 `/etc/rc.conf` 文件中必须加入 `inetd_enable="YES"`：

    ```
    # `/etc/rc.d/inetd restart`
    ```

您可把 `tftpboot` 目录放到服务器上的什何地方。 确定这个位置设置在 `inetd.conf` 和 `dhcpd.conf` 里。

在所有的情况下，您都需要启用 NFS， 并且 NFS 服务器上导出相应的文件系统。

1.  把这一行加入到`/etc/rc.conf`里：

    ```
    nfs_server_enable="YES"
    ```

2.  通过往 `/etc/exports` 里加入下面几行(调整“载入点”列， 并且使用无盘工作站的名字替换 *`margaux corbieres`*)， 导出文件系统――无盘根目录存在于此：

    ```
    *`/data/misc`* -alldirs -ro *`margaux corbieres`*
    ```

3.  让 mountd 重读它的配置文件。如果您真的需要启用第一步的 `/etc/rc.conf` 里 NFS， 您可能就要重启系统了。

    ```
    # `/etc/rc.d/mountd restart`
    ```

#### 32.7.2.6. 建立无盘内核

如果您在使用 Etherboot， 您需要为无盘客户端建立内核配置文件， 使用如下选项(除了常使用的外)：

```
options     BOOTP          # Use BOOTP to obtain IP address/hostname
options     BOOTP_NFSROOT  # NFS mount root filesystem using BOOTP info

```

您可能也想使用 `BOOTP_NFSV3`， `BOOT_COMPAT` 和 `BOOTP_WIRED_TO` (参考 `NOTES` 文件)。

这些名字具有历史性，并且有些有些误导， 因为它们实际上启用了内核里 (它可能强制限制 BOOTP 或 DHCP 的使用)，与 DHCP 和 BOOTP 的无关的应用。

编译内核(参考第 9 章 *配置 FreeBSD 的内核*)， 然后将它复制到 `dhcpd.conf` 里指定的地方。

### 注意:

当使用 PXE 里， 使用以上选项建立内核并不做严格要求(尽管建议这样做)。 启用它们会在内核启动时引起更多的 DHCP 提及过的请求，带来的小小的风险是在有些特殊情况下新值和由 [pxeboot(8)](http://www.FreeBSD.org/cgi/man.cgi?query=pxeboot&sektion=8&manpath=freebsd-release-ports) 取回的值之间的不一致性。 使用它们的好处是主机名会被附带设置。否则， 您就需要使用其它的方法来设置主机名，如在客户端指定的 `rc.conf` 文件里。

### 注意:

为了使带有 Etherboot 的内核可引导，就需要把设备提示 (device hint) 编译进去。通常要在配置文件(查看 `NOTES` 配置注释文件) 里设置下列选项：

```
hints		"GENERIC.hints"
```

#### 32.7.2.7. 准备根(root)文件系统

您需要为无盘工作站建立根文件系统， 它就是 `dhcpd.conf` 里的 `root-path` 所指定的目录。

##### 32.7.2.7.1. 使用 `make world` 来复制根文件系统

这种方法可以迅速安装一个彻底干净的系统 (不仅仅是根文件系统) 到 `DESTDIR`。 您要做的就是简单地执行下面的脚本：

```
#!/bin/sh
export DESTDIR=/data/misc/diskless
mkdir -p ${DESTDIR}
cd /usr/src; make buildworld && make buildkernel
make installworld && make installkernel
cd /usr/src/etc; make distribution
```

一旦完成，您可能需要定制 `/etc/rc.conf` 和 `/etc/fstab`――根据您的需要放到 `DESTDIR`里。

#### 32.7.2.8. 配置 swap(交换)

如果需要，位于服务器上的交换文件可以通过 NFS 来访问。

##### 32.7.2.8.1. NFS 交换区

内核并不支持在引导时启用 NFS 交换区。 交换区必须通过启动脚本启用， 其过程是挂接一个可写的文件系统， 并在其上创建并启用交换文件。 要建立尺寸合适的交换文件， 可以这样做：

```
# `dd if=/dev/zero of=/path/to/swapfile bs=1k count=1 oseek=100000`
```

要启用它，您须要把下面几行加到 `rc.conf`里：

```
swapfile=*`/path/to/swapfile`*
```

#### 32.7.2.9. 杂项问题

##### 32.7.2.9.1. 运行时 `/usr` 是只读在

如果无盘工作站是配置来支持 X， 那么您就必须调整 XDM 配置文件，因为它默认把错误信息写到 `/usr`。

##### 32.7.2.9.2. 使用非 FreeBSD 服务器

当用作根文件系统的服务器运行的是不 FreeBSD，您须要在 FreeBSD 机器上建立根文件系统， 然后把它复制到它的目的地，使用的命令可以是 `tar` 或 `cpio`。

在这种情况下，有时对于 `/dev` 里的一些特殊的文件会有问题，原因就是不同的 “最大/最小”整数大小。 一种解决的方法就是从非 FreeBSD 服务里导出一个目录， 并把它载入 FreeBSD 到机子上， 并使用 [devfs(5)](http://www.FreeBSD.org/cgi/man.cgi?query=devfs&sektion=5&manpath=freebsd-release-ports) 来为用户透明地分派设备节点。

## 32.8. 从 PXE 启动一个 NFS 根文件系统

原作者 Craig Rodrigues.

Intel® 预启动执行环境 （PXE） 能让操作系统从网络启动。 通常由近代主板的 BIOS 提供 PXE 支持，它可以通过在 BIOS 设置里选择从网络启动开启。 一个功能完整的 PXE 配置还需要正确地设置 DHCP 和 TFTP 服务。

当计算机启动的时候， 通过 DHCP 获取关于 从 TFTP 得到引导加载器（boot loader）的信息。 在计算机接受此信息以后， 便通过 TFTP 下载并执行引导加载器。 这些记载于 [预启动执行环境 (PXE) 规范](http://download.intel.com/design/archives/wfm/downloads/pxespec.pdf) 的 2.2.1 章节中。 在 FreeBSD 中， 在 PXE 过程中获取的引导加载器为 `/boot/pxeboot`。 在 `/boot/pxeboot` 执行之后， FreeBSD 的内核被加载， 接着是其他的 FreeBSD 相关引导部分依次被执行。 更多关于 FreeBSD 启动过程的详细信息请参阅 第 13 章 *FreeBSD 引导过程*。

### 32.8.1. 配置用于 NFS 根文件系统的 `chroot` 环境

1.  Choose a directory which will have a FreeBSD installation which will be NFS mountable. For example, a directory such as `/b/tftpboot/FreeBSD/install` can be used.

    选择一个可被用户 NFS 挂载并安装有 FreeBSD 的目录。 比如可以使用像 `/b/tftpboot/FreeBSD/install` 这样的一个目录。

    ```
    # `export NFSROOTDIR=/b/tftpboot/FreeBSD/install`
    # `mkdir -p ${NFSROOTDIR}`
    ```

2.  使用如下的命令开启 NFS 服务 第 30.3.2 节 “配置 NFS”.

3.  将下面这行加入 `/etc/exports` 用以通过 NFS 导出此目录：

    ```
    /b -ro -alldirs
    ```

4.  重起 NFS 服务：

    ```
    # `/etc/rc.d/nfsd restart`
    ```

5.  按照 第 30.2.2 节 “设置” 中标明的步骤启用 [inetd(8)](http://www.FreeBSD.org/cgi/man.cgi?query=inetd&sektion=8&manpath=freebsd-release-ports)。

6.  将如下这行加入到 `/etc/inetd.conf`：

    ```
    tftp dgram udp wait root /usr/libexec/tftpd tftpd -l -s /b/tftpboot
    ```

7.  重启 inetd：

    ```
    # `/etc/rc.d/inetd restart`
    ```

8.  重新编译 FreeBSD 内核和用户态：

    ```
    # `cd /usr/src`
    # `make buildworld`
    # `make buildkernel`
    ```

9.  把 FreeBSD 安装到 NFS 挂载目录：

    ```
    # `make installworld DESTDIR=${NFSROOTDIR}`
    # `make installkernel DESTDIR=${NFSROOTDIR}`
    # `make distribution DESTDIR=${NFSROOTDIR}`

    ```

10.  测试 TFTP 服务是否能下载将从 PXE 获取的引导加载器：

    ```
    # `tftp localhost`
    tftp> `get FreeBSD/install/boot/pxeboot`
    Received 264951 bytes in 0.1 seconds

    ```

11.  编辑 `${NFSROOTDIR}/etc/fstab` 并加入以下这行挂载 NFS 根文件系统：

    ```
    # Device                                         Mountpoint    FSType   Options  Dump Pass
    myhost.example.com:/b/tftpboot/FreeBSD/install       /         nfs      ro        0    0

    ```

    用你的 NFS 服务器主机名或者 IP 地址替换 *`myhost.example.com`*。 在此例中， 根文件系统是以“只读”的方式挂载用来防止 NFS 客户端可能意外删除根文件系统上的文件。

12.  设置 [chroot(8)](http://www.FreeBSD.org/cgi/man.cgi?query=chroot&sektion=8&manpath=freebsd-release-ports) 环境中的 root 密码。

    ```
    # `chroot ${NFSROOTDIR}`
    # `passwd`
    ```

    此为设置从 PXE 启动的客户机的 root 密码。

13.  允许 ssh root 登录从 PXE 启动的客户机， 编辑 `${NFSROOTDIR}/etc/ssh/sshd_config` 并开启 `PermitRootLogin` 选项。 关于此选项的说明请参阅 [sshd_config(5)](http://www.FreeBSD.org/cgi/man.cgi?query=sshd_config&sektion=5&manpath=freebsd-release-ports)。

14.  对 ${NFSROOTDIR} 的 [chroot(8)](http://www.FreeBSD.org/cgi/man.cgi?query=chroot&sektion=8&manpath=freebsd-release-ports) 环境做些其他的定制。 这可以是像使用 [pkg_add(1)](http://www.FreeBSD.org/cgi/man.cgi?query=pkg_add&sektion=1&manpath=freebsd-release-ports) 安装二进制包， 使用 [vipw(8)](http://www.FreeBSD.org/cgi/man.cgi?query=vipw&sektion=8&manpath=freebsd-release-ports) 修改密码， 或者编辑 [amd.conf(5)](http://www.FreeBSD.org/cgi/man.cgi?query=amd.conf&sektion=5&manpath=freebsd-release-ports) 映射自动挂载等。例如：

    ```
    # `chroot ${NFSROOTDIR}`
    # `pkg_add -r bash`
    ```

### 32.8.2. 配置 `/etc/rc.initdiskless` 中用到的内存文件系统

如果你从一个 NFS 根卷启动， `/etc/rc` 如果检测到是从 NFS 启动便会运行 `/etc/rc.initdiskless` 脚本。 请阅读此脚本中的注释部分以便了解到底发生了什么。 我们需要把 `/etc` 和 `/var` 做成内存文件系统的原因是这些目录需要能被写入， 但 NFS 根文件系统是只读的。

```
# `chroot ${NFSROOTDIR}`
# `mkdir -p conf/base`
# `tar -c -v -f conf/base/etc.cpio.gz --format cpio --gzip etc`
# `tar -c -v -f conf/base/var.cpio.gz --format cpio --gzip var`
```

当系统启动的时候， `/etc` 和 `/var` 内存文件系统就会被创建并挂载， `cpio.gz` 就会被复制进去。

### 32.8.3. 配置 DHCP 服务

PXE 需要配置一个 TFTP 服务器和一个 DHCP 服务器。 DHCP 服务并不要求与 TFTP 服务在同一台机器上， 但是必须能够从你的网络访问到它。

1.  按照此文档处 第 30.5.7 节 “安装和配置 DHCP 服务器” 方法安装 DHCP 服务。 确保 `/etc/rc.conf` 和 `/usr/local/etc/dhcpd.conf` 都配置正确。

2.  在 `/usr/local/etc/dhcpd.conf` 中配置 `next-server`， `filename`， `option root-path` 选项指向你的 TFTP 服务器的 IP 地址， 以及 TFTP 上 `/boot/pxeboot` 文件的路径， 和 NFS 根文件系统的路径。 这里一份 `dhcpd.conf` 实例：

    ```
    subnet 192.168.0.0 netmask 255.255.255.0 {
       range 192.168.0.2 192.168.0.3 ;
       option subnet-mask 255.255.255.0 ;
       option routers 192.168.0.1 ;
       option broadcast-address 192.168.0.255 ;
       option domain-name-server 192.168.35.35, 192.168.35.36 ;
       option domain-name "example.com";

       # IP address of TFTP server
       next-server 192.168.0.1 ;

       # path of boot loader obtained
       # via tftp
       filename "FreeBSD/install/boot/pxeboot" ;

       # pxeboot boot loader will try to NFS mount this directory for root FS
       option root-path "192.168.0.1:/b/tftpboot/FreeBSD/install/" ;

    }

    ```

### 32.8.4. 配置 PXE 客户端与调试连接问题

1.  当客户端启动的时候， 进入 BIOS 配置菜单。 设置 BIOS 从网络启动。 如果之前你所有的配置步骤都正确的话， 那么所有部分应该能 "正常工作"。

2.  使用 [net/wireshark](http://www.freebsd.org/cgi/url.cgi?ports/net/wireshark/pkg-descr) port 查看 DHCP 和 TFTP 的网络流量来调试各种问题。

3.  确保 `pxeboot` 能从 TFTP 获取。 在你的 TFTP 服务器上检查 `/var/log/xferlog` 日志确保 `pxeboot` 被从正确的位置获取。 可以这样测试上面例子 `dhcpd.conf` 中所设置的：

    ```
    # `tftp 192.168.0.1`
    tftp> `get FreeBSD/install/boot/pxeboot`
    Received 264951 bytes in 0.1 seconds
    ```

    请阅读 [tftpd(8)](http://www.FreeBSD.org/cgi/man.cgi?query=tftpd&sektion=8&manpath=freebsd-release-ports) 和 [tftp(1)](http://www.FreeBSD.org/cgi/man.cgi?query=tftp&sektion=1&manpath=freebsd-release-ports)。 其中的 `BUGS` 列出了 TFTP 的一些限制。

4.  确保根文件系统能够从 NFS 挂载。 可以这样测试上面例子 `dhcpd.conf` 中所设置的：

    ```
    # `mount -t nfs 192.168.0.1:/b/tftpboot/FreeBSD/install /mnt`
    ```

5.  阅读 `src/sys/boot/i386/libi386/pxe.c` 中的代码以了解 `pxeboot` 加载器如何设置诸如 `boot.nfsroot.server` 和 `boot.nfsroot.path` 之类的变量。 这些变量被用在了 `src/sys/nfsclient/nfs_diskless.c` 的 NFS 无盘根挂载代码中。

6.  Read [pxeboot(8)](http://www.FreeBSD.org/cgi/man.cgi?query=pxeboot&sektion=8&manpath=freebsd-release-ports) and [loader(8)](http://www.FreeBSD.org/cgi/man.cgi?query=loader&sektion=8&manpath=freebsd-release-ports).

## 32.9. ISDN

关于 ISDN 技术和硬件的一个好的资源是[Dan Kegel 的 ISDN 主页](http://www.alumni.caltech.edu/~dank/isdn/)。

一个快速简单的到 ISDN 的路线图如下：

*   如果您住在欧洲，您可能要查看一下 ISDN 卡部分。

*   如果您正计划首要地使用 ISDN 基于拨号非专用线路连接到带有提供商的互联网， 您可能要了解一下终端适配器。如果您更改提供商的话， 这会给您带来最大的灵活性、最小的麻烦。

*   如果您连接了两个局域网 (LAN)，或使用了专用的 ISDN 连线连接到互联网，您可能要考虑选择单独的路由器/网桥。

在决定选择哪一种方案的时候，价格是个很关键的因素。 下面列有从不算贵到最贵的选择：

### 32.9.1. ISDN 卡

贡献者：Hellmuth Michaelis.中文翻译：张 雪平.

FreeBSD 的 ISDN 工具通过被动卡 (passive card) 仅支持 DSS1/Q.931(或 Euro-ISDN) 标准。 此外也支持一些 active card， 它们的固件也支持其它信号协议， 这其中包括最先得到支持的 “Primary Rate (PRI) ISDN”卡。

isdn4bsd 软件允许连接到其它 ISDN 路由器，使用的是原始的 HDLC 上的 IP 或利用同步 PPP：使用带有 `isppp` (一个修改过的 [sppp(4)](http://www.FreeBSD.org/cgi/man.cgi?query=sppp&sektion=4&manpath=freebsd-release-ports) 驱动程序)的 PPP 内核，或使用用户区 (userland) [ppp(8)](http://www.FreeBSD.org/cgi/man.cgi?query=ppp&sektion=8&manpath=freebsd-release-ports)。通过使用 userland [ppp(8)](http://www.FreeBSD.org/cgi/man.cgi?query=ppp&sektion=8&manpath=freebsd-release-ports)，两个或更多 ISDN 的 B 通道联结变得可能。 除了许多如 300 波特 (Baud) 的软 modem 一样的工具外， 还可以实现电话应答机应用。

在 FreeBSD 里，正有更多的 PC ISDN 卡被支持； 报告显示在整个欧洲及世界的其它许多地区可以成功使用。

被支持的主动型 ISDN 卡主要是带有 Infineon (以前的 Siemens) ISAC/HSCX/IPAC ISDN 芯片组，另外还有带有 Cologne (只有 ISA 总线) 芯片的 ISDN 卡、带有 Winbond W6692 芯片的 PCI 卡、一部分带有 Tiger300/320/ISAC 芯片组的卡以及带有一些商家专有的芯片组的卡 (如 AVM Fritz!Card PCI V.1.0 和 the AVM Fritz!Card PnP)。

当前积极的支持的 ISDN 卡有 AVM B1 (ISA 和 PCI) BRI 卡和 AVM T1 PCI PRI 卡。

关于 isdn4bsd 的文档，请查看 [isdn4bsd 的主页](http://www.freebsd-support.de/i4b/)， 那里也有提示、勘误表以及更多的文档 (如 [isdn4bsd 手册](http://people.FreeBSD.org/~hm/))。

要是您有兴趣增加对不同 ISDN 协议的支持，对当前还不支持的 ISDN PC 卡的支持或想增强 isdn4bsd 的性能，请联系 Hellmuth Michaelis。

对于安装、配置以及 isdn4bsd 故障排除的问题，可以利用 [freebsd-isdn](http://lists.FreeBSD.org/mailman/listinfo/freebsd-isdn) 邮件列表。

### 32.9.2. ISDN 终端适配器

终端适配器 (TA) 对于 ISDN 就好比 modem 对于常规电话线。

许多 TA 使用标准的 Hayes modem AT 命令集，并且可以降级来代替 modem。

TA 基本的运作同 modem 一样，不同之处是连接和整个速度更比老 modem 更快。同 modem 的安装一样，您也需要配置 PPP。确认您的串口速度已足够高。

使用 TA 连接互联网提供商的主要好处是您可以做动态的 PPP。 由于 IP 地址空间变得越来越紧张，许多提供商都不愿再提供静态 IP。许多的独立的路由器是不支持动态 IP 分配的。

TA 完全依赖于您在运行的 PPP 进程， 以完成它们的功能和稳定的连接。这可以让您在 FreeBSD 机子里轻易地从使用 modem 升级到 ISDN，要是您已经安装了 PPP 的话。只是，在您使用 PPP 程序时所体验到任何问题同时也存在。

如果您想要最大的稳定性，请使用 PPP 内核选项，而不要使用 userland PPP。

下面的 TA 就可以同 FreeBSD 一起工作：

*   Motorola BitSurfer 和 Bitsurfer Pro

*   Adtran

大部分其它的 TA 也可能工作，TA 提供商试图让他们的产品可以接受大部分的标准 modem AT 命令集。

对于外置 TA 的实际问题是：象 modem 要一样，您机子需要有一个好的串行卡。

想要更深入地理解串行设备以及异步和同步串口这间的不同点， 您就要读读 FreeBSD 串行硬件教程了。

TA 将标准的 PC 串口 (同步的) 限制到了 115.2 Kbs，即使您有 128 Kbs 的连接。 想要完全利用 ISDN 有能力达到的 128 Kbs，您就需要把 TA 移到同步串行卡上。

当心被骗去买一个内置的 TA 以及自认为可以避免同步/异步问题。内置的 TA 只是简单地将一张标准 PC 串口芯片内建在里边。 所做的这些只是让您省去买另一根串行线以及省去寻找另一个空的插孔。

带有 TA 的同步卡至少和一个独立的路由器同一样快地， 而且仅使用一个简单的 386 FreeBSD 盒驱动它。

选择同步卡/TA 还是独立的路由器，是个要高度谨慎的问题。 在邮件列表里有些相关的讨论。我们建议您去搜索一下关于完整讨论的记录。

### 32.9.3. 单独的 ISDN 桥/路由器

ISDN 桥或路由器根本就没有指定要 FreeBSD 或其它任何的操作系统。更多完整的关于路由和桥接技术的描述， 请参考网络指南的书籍。

这部分的内容里，路由器和桥接这两个词汇将会交替地使用。

随着 ISDN 路由器/桥的价格下滑，对它们的选择也会变得越来越流行。 ISDN 路由器是一个小盒子，可以直接地接入您的本地以太网， 并且自我管理到其它桥/路由器的连接。它有个内建的软件用于与通信――通过 PPP 和其它流行的协议。

路由器有比标准 TA 更快的吞吐量，因为它会使用完全同步的 ISDN 连接。

使用 ISDN 路由器和桥的主要问题是两个生产商之间的协同性仍存在问题。 如果您计划连接到互联网提供商，您应该跟他们进行交涉。

如果您计划连接两个局域网网段，如您的家庭网和办公网， 这将是最简单最低维护的解决方案。因为您买的设备是用于连接两边的， 可以保证这种连接一定会成功。

例如连接到家里的计算机，或者是办公网里的一个分支连接到办公主网， 那么下面的设置就可能用到：

例 32.4. 办公室局部或家庭网

网络使用基于总线拓扑的 10 base 2 以太网 (“瘦网(thinnet)”)。如果有必要，用网线连接路由器和 AUI/10BT 收发器。

![10 Base 2 Ethernet](img/isdn-bus.png)

如果您的家里或办公室支部里只有一台计算机， 您可以使用一根交叉的双绞线直接连接那台独立路由器。

例 32.5. 主办公室或其它网络

网络使用的是星形拓扑的 10 base T 以太网(“双绞线”)。

![ISDN Network Diagram](img/isdn-twisted-pair.png)

大部分路由器/网桥有一大好处就是，它们允许您在 *同一* 时间，有两个 *分开独立的* PPP 连接到两个分开的点上。这点在许多的 TA 上是不支持的， 除非带有两个串口的特定模式(通常都很贵)。请不要把它与通道连接、MPP 等相混淆。

这是个非常有用的功能，例如，如果在您的办公室里您有个专有的 ISDN 连接，而且您想接入到里边，但休想让另一根 ISDN 线也能工作。 办公室里的路由器能够管理专有的 B 通道连接到互联网 (64 Kbps) 以及使用另一个通道 B 来完成单独的数据连接。 第二个 B 通道可以用于拨进、拨出或动态与第一个 B 通道进行连接 (MPP 等)，以获取更大宽带。

以太网桥也允许您传输的不仅仅是 IP 通信。您也可以发送 IPX/SPX 或其它任何您所使用的协议。

## 32.10. 网络地址转换

作者：Chern Lee.译者：李 鑫.

### 32.10.1. 概要

FreeBSD 的网络地址转换服务， 通常也被叫做 [natd(8)](http://www.FreeBSD.org/cgi/man.cgi?query=natd&sektion=8&manpath=freebsd-release-ports)， 是一个能够接收连入的未处理 IP 包， 将源地址修改为本级地址然后重新将这些包注入到发出 IP 包流中。 [natd(8)](http://www.FreeBSD.org/cgi/man.cgi?query=natd&sektion=8&manpath=freebsd-release-ports) 同时修改源地址和端口， 当接收到响应数据时，它作逆向转换以便把数据发回原先的请求者。

NAT 最常见的用途是为人们所熟知的 Internet 连接共享。

### 32.10.2. 安装

随着 IPv4 的 IP 地址空间的日益枯竭， 以及使用如 DSL 和电缆等高速连接的用户的逐渐增多， 越来越多的人开始需要 Internet 连接共享这样的解决方案。 由于能够将许多计算机通过一个对外的 IP 地址进行接入， [natd(8)](http://www.FreeBSD.org/cgi/man.cgi?query=natd&sektion=8&manpath=freebsd-release-ports) 成为了一个理想的选择。

更为常见的情况， 一个用户通过电缆或者 DSL 线路 接入，并拥有一个 IP 地址，同时，希望通过这台接入 Internet 的计算机来为 LAN 上更多的计算机提供接入服务。

为了完成这一任务， 接入 Internet 的 FreeBSD 机器必须扮演网关的角色。 这台网关必须有两块网卡 ── 一块用于连接 Internet 路由器， 另一块用来连接 LAN。 所有 LAN 上的机器通过 Hub 或交换机进行连接。

### 注意:

有多种方法能够通过 FreeBSD 网关将 LAN 接入 Internet。 这个例子只介绍了有至少两块网卡的网关。

![Network Layout](img/natd.png)

上述配置被广泛地用于共享 Internet 连接。 LAN 中的一台机器连接到 Internet 中。 其余的计算机则通过那台 “网关” 机来连接 Internet。

### 32.10.3. 引导加载器配置

在默认的 `GENERIC` 内核中， 并没有启用通过 [natd(8)](http://www.FreeBSD.org/cgi/man.cgi?query=natd&sektion=8&manpath=freebsd-release-ports) 进行网址翻译的功能， 不过， 这一功能可以通过在 `/boot/loader.conf` 中添加两项配置来在引导时自动予以加载：

```
ipfw_load="YES"
ipdivert_load="YES"
```

此外， 还可以将引导加载器变量 `net.inet.ip.fw.default_to_accept` 设为 `1`：

```
net.inet.ip.fw.default_to_accept="1"
```

### 注意:

在刚开始配置防火墙和 NAT 网关时， 增加这个配置是个好主意。 默认的 [ipfw(8)](http://www.FreeBSD.org/cgi/man.cgi?query=ipfw&sektion=8&manpath=freebsd-release-ports) 规则将是 `allow ip from any to any` 而不是默认的 `deny ip from any to any`， 这样， 在系统重启时， 也就不太容易被反锁在外面。

### 32.10.4. 内核配置

当不能使用内核模块， 或更希望将全部需要的功能联编进内核时， 可以在内核配置中添加下面的设置来实现：

```
options IPFIREWALL
options IPDIVERT
```

此外，下列是一些可选的选项：

```
options IPFIREWALL_DEFAULT_TO_ACCEPT
options IPFIREWALL_VERBOSE
```

### 32.10.5. 系统引导时的配置

如果希望在系统引导过程中启用防火墙和 NAT 支持， 应在 `/etc/rc.conf`中添加下列配置：

```
gateway_enable="YES" ![1](img/1.png)
firewall_enable="YES" ![2](img/2.png)
firewall_type="OPEN" ![3](img/3.png)
natd_enable="YES"
natd_interface="*`fxp0`*" ![4](img/4.png)
natd_flags="" ![5](img/5.png)
```

| ![1](img/#co-natd-gateway-enable) | 将机器配置为网关。 执行 `sysctl net.inet.ip.forwarding=1` 效果相同。 |
| ![2](img/#co-natd-firewall-enable) | 在启动时启用 `/etc/rc.firewall` 中的防火墙规则。 |
| ![3](img/#co-natd-firewall-type) | 指定一个预定义的允许所有包进入的防火墙规则集。 参见 `/etc/rc.firewall` 以了解其他类型的规则集。 |
| ![4](img/#co-natd-natd-interface) | 指定通过哪个网络接口转发包 (接入 Internet 的那一个)。 |
| ![5](img/#co-natd-natd-flags) | 其他希望在启动时传递给 [natd(8)](http://www.FreeBSD.org/cgi/man.cgi?query=natd&sektion=8&manpath=freebsd-release-ports) 的参数。 |

在 `/etc/rc.conf` 中加入上述选项将在系统启动时运行 `natd -interface fxp0`。 这一工作也可以手工完成。

### 注意:

当有太多选项要传递时，也可以使用一个 [natd(8)](http://www.FreeBSD.org/cgi/man.cgi?query=natd&sektion=8&manpath=freebsd-release-ports) 的配置文件来完成。这种情况下，这个配置文件必须通过在 `/etc/rc.conf` 里增加下面内容来定义：

```
natd_flags="-f /etc/natd.conf"
```

`/etc/natd.conf` 文件会包含一个配置选项列表， 每行一个。在紧跟部分的例子里将使用下面的文件：

```
redirect_port tcp 192.168.0.2:6667 6667
redirect_port tcp 192.168.0.3:80 80
```

关于配置文件的更多信息，参考 [natd(8)](http://www.FreeBSD.org/cgi/man.cgi?query=natd&sektion=8&manpath=freebsd-release-ports) 手册页中关于 `-f` 选项那一部分。

在 LAN 后面的每一台机子和接口应该被分配私有地址空间(由 RFC 1918 定义) 里的 IP 地址，并且默认网关设成 natd 机子的内连 IP 地址。

例如：客户端 `A` 和 `B` 在 LAN 后面，IP 地址是 `192.168.0.2` 和 `192.168.0.3`，同时 natd 机子的 LAN 接口上的 IP 地址是 `192.168.0.1`。客户端 `A` 和 `B` 的默认网关必须要设成 natd 机子的 IP――`192.168.0.1`。natd 机子外连，或互联网接口不需要为了 [natd(8)](http://www.FreeBSD.org/cgi/man.cgi?query=natd&sektion=8&manpath=freebsd-release-ports) 而做任何特别的修改就可工作。

### 32.10.6. 端口重定向

使用 [natd(8)](http://www.FreeBSD.org/cgi/man.cgi?query=natd&sektion=8&manpath=freebsd-release-ports) 的缺点就是 LAN 客户不能从互联网访问。LAN 上的客户可以进行到外面的连接，而不能接收进来的连接。如果想在 LAN 的客户端机子上运行互联网服务，这就会有问题。 对此的一种简单方法是在 natd 机子上重定向选定的互联网端口到 LAN 客户端。

例如：在客户端 `A` 上运行 IRC 服务，而在客户端 `B` 上运行 web 服务。 想要正确的工作，在端口 6667 (IRC) 和 80 (web) 上接收到的连接就必须重定向到相应的机子上。

`-redirect_port` 需要使用适当的选项传送给 [natd(8)](http://www.FreeBSD.org/cgi/man.cgi?query=natd&sektion=8&manpath=freebsd-release-ports)。语法如下：

```
     -redirect_port proto targetIP:targetPORT[-targetPORT]
                 [aliasIP:]aliasPORT[-aliasPORT]
                 [remoteIP[:remotePORT[-remotePORT]]]
```

在上面的例子中，参数应该是：

```
    -redirect_port tcp 192.168.0.2:6667 6667
    -redirect_port tcp 192.168.0.3:80 80
```

这就会重定向适当的 *tcp* 端口到 LAN 上的客户端机子。

`-redirect_port` 参数可以用来指出端口范围来代替单个端口。例如， *`tcp 192.168.0.2:2000-3000 2000-3000`* 就会把所有在端口 2000 到 3000 上接收到的连接重定向到主机 `A` 上的端口 2000 到 3000。

当直接运行 [natd(8)](http://www.FreeBSD.org/cgi/man.cgi?query=natd&sektion=8&manpath=freebsd-release-ports) 时，就可以使用这些选项， 把它们放到 `/etc/rc.conf` 里的 `natd_flags=""` 选项上， 或通过一个配置文件进行传送。

想要更多配置选项，请参考 [natd(8)](http://www.FreeBSD.org/cgi/man.cgi?query=natd&sektion=8&manpath=freebsd-release-ports)。

### 32.10.7. 地址重定向

如果有几个 IP 地址提供，那么地址重定向就会很有用， 然而他们必须在一个机子上。使用它，[natd(8)](http://www.FreeBSD.org/cgi/man.cgi?query=natd&sektion=8&manpath=freebsd-release-ports) 就可以分配给每一个 LAN 客户端它们自己的外部 IP 地址。[natd(8)](http://www.FreeBSD.org/cgi/man.cgi?query=natd&sektion=8&manpath=freebsd-release-ports) 然后会使用适当的处部 IP 地址重写从 LAN 客户端外出的数据包， 以及重定向所有进来的数据包――一定的 IP 地址回到特定的 LAN 客户端。这也叫做静态 NAT。例如，IP 地址 `128.1.1.1`、 `128.1.1.2` 和 `128.1.1.3` 属于 natd 网关机子。 `128.1.1.1` 可以用来作 natd 网关机子的外连 IP 地址，而 `128.1.1.2` 和 `128.1.1.3` 用来转发回 LAN 客户端 `A` 和 `B`。

`-redirect_address` 语法如下：

```
-redirect_address localIP publicIP
```

| localIP | LAN 客户端的内部 IP 地址。 |
| publicIP | 相应 LAN 客户端的外部 IP 地址。 |

在这个例子里，参数是：

```
-redirect_address 192.168.0.2 128.1.1.2 -redirect_address 192.168.0.3 128.1.1.3
```

象 `-redirect_port` 一样，这些参数也是放在 `/etc/rc.conf` 里的 `natd_flags=""` 选项上， 或通过一个配置文件传送给它。使用地址重定向， 就没有必要用端口重定向了，因为所有在某个 IP 地址上收到的数据都被重定向了。

在 natd 机子上的外部 IP 地址必须激活并且别名到 (aliased) 外连接口。要这做就看看 [rc.conf(5)](http://www.FreeBSD.org/cgi/man.cgi?query=rc.conf&sektion=5&manpath=freebsd-release-ports)。

## 32.11. 并口电缆 IP (PLIP)

PLIP 允许我们在两个并口间运行 TCP/IP。 在使用笔记本电脑， 或没有网卡的计算机时， 这会非常有用。 这一节中， 我们将讨论：

*   制作用于并口的 (laplink) 线缆。

*   使用 PLIP 连接两台计算机。

### 32.11.1. 制作并口电缆。

您可以在许多计算机供应店里买到并口电缆。 如果买不到， 或者希望自行制作， 则可以参阅下面的表格， 它介绍了如何利用普通的打印机并口电缆来改制：

表 32.2. 用于网络连接的并口电缆接线方式

| A-name | A 端 | B 端 | 描述 | Post/Bit |
| --- | --- | --- | --- | --- |
|  
DATA0 -ERROR

 |  
2 15

 |  
15 2

 | 数据 |  
0/0x01 1/0x08

 |
|  
DATA1 +SLCT

 |  
3 13

 |  
13 3

 | 数据 |  
0/0x02 1/0x10

 |
|  
DATA2 +PE

 |  
4 12

 |  
12 4

 | 数据 |  
0/0x04 1/0x20

 |
|  
DATA3 -ACK

 |  
5 10

 |  
10 5

 | 脉冲 (Strobe) |  
0/0x08 1/0x40

 |
|  
DATA4 BUSY

 |  
6 11

 |  
11 6

 | 数据 |  
0/0x10 1/0x80

 |
| GND | 18-25 | 18-25 | GND | - |

### 32.11.2. 设置 PLIP

首先，您需要一根 laplink 线。然后， 确认两台计算机的内核都有对 [lpt(4)](http://www.FreeBSD.org/cgi/man.cgi?query=lpt&sektion=4&manpath=freebsd-release-ports) 驱动程序的支持：

```
# `grep lp /var/run/dmesg.boot`
lpt0: <Printer> on ppbus0
lpt0: Interrupt-driven port
```

并口必须是一个中断驱动的端口， 您应在 `/boot/device.hints` 文件中配置：

```
hint.ppc.0.at="isa"
hint.ppc.0.irq="7"
```

然后检查内核配置文件中是否有一行 `device plip` 或加载了 `plip.ko` 内核模块。 这两种情况下， 在使用 [ifconfig(8)](http://www.FreeBSD.org/cgi/man.cgi?query=ifconfig&sektion=8&manpath=freebsd-release-ports) 命令时都会显示并口对应的网络接口， 类似这样：

```
# `ifconfig plip0`
plip0: flags=8810<POINTOPOINT,SIMPLEX,MULTICAST> mtu 1500
```

用 laplink 线接通两台计算机的并口。

在两边以 `root` 身份配置通讯参数。 例如， 如果你希望将 `host1` 通过另一台机器 `host2` 连接：

```
                 host1 <-----> host2
IP Address    10.0.0.1      10.0.0.2
```

配置 `host1` 上的网络接口，照此做：

```
# `ifconfig plip0 10.0.0.1 10.0.0.2`
```

配置 `host2` 上的网络接口，照此做：

```
# `ifconfig plip0 10.0.0.2 10.0.0.1`
```

您现在应该有个工作的连接了。想要更详细的信息， 请阅读 [lp(4)](http://www.FreeBSD.org/cgi/man.cgi?query=lp&sektion=4&manpath=freebsd-release-ports) 和 [lpt(4)](http://www.FreeBSD.org/cgi/man.cgi?query=lpt&sektion=4&manpath=freebsd-release-ports) 手册页。

您还应该增加两个主机到 `/etc/hosts`：

```
127.0.0.1               localhost.my.domain localhost
10.0.0.1                host1.my.domain host1
10.0.0.2                host2.my.domain host2
```

要确认连接是否工作，可以到每一台机子上，然后 ping 另外一台。例如，在 `host1` 上：

```
# `ifconfig plip0`
plip0: flags=8851<UP,POINTOPOINT,RUNNING,SIMPLEX,MULTICAST> mtu 1500
        inet 10.0.0.1 --> 10.0.0.2 netmask 0xff000000
# `netstat -r`
Routing tables

Internet:
Destination        Gateway          Flags     Refs     Use      Netif Expire
host2              host1            UH          0       0       plip0
# `ping -c 4 host2`
PING host2 (10.0.0.2): 56 data bytes
64 bytes from 10.0.0.2: icmp_seq=0 ttl=255 time=2.774 ms
64 bytes from 10.0.0.2: icmp_seq=1 ttl=255 time=2.530 ms
64 bytes from 10.0.0.2: icmp_seq=2 ttl=255 time=2.556 ms
64 bytes from 10.0.0.2: icmp_seq=3 ttl=255 time=2.714 ms

--- host2 ping statistics ---
4 packets transmitted, 4 packets received, 0% packet loss
round-trip min/avg/max/stddev = 2.530/2.643/2.774/0.103 ms
```

## 32.12. IPv6

原始作者：Aaron Kaplan.重新组织和增加：Tom Rhodes.中文翻译：张 雪平.Extended by Brad Davis.

IPv6 (也被称作 IPng “下一代 IP”) 是众所周知的 IP 协议 (也叫 IPv4) 的新版本。 和其他现代的 *BSD 系统一样， FreeBSD 包含了 KAME 的 IPv6 参考实现。 因此， 您的 FreeBSD 系统包含了尝试 IPv6 所需要的所有工具。 这一节主要集中讨论如何配置和使用 IPv6。

在 1990 年代早期， 人们开始担心可用的 IPv4 地址空间在不断地缩小。 随着 Internet 的爆炸式发展， 主要的两个担心是：

*   用尽所有的地址。 当然现在这个问题已经不再那样尖锐， 因为 RFC1918 私有地址空间 (`10.0.0.0/8`、 `172.16.0.0/12`， 以及 `192.168.0.0/16`) 和网络地址转换 (NAT) 技术已经被广泛采用。

*   路由表条目变得太大。这点今天仍然是焦点。

IPv6 解决这些和其它许多的问题：

*   128 位地址空间。换句话，理论上有 340,282,366,920,938,463,463,374,607,431,768,211,456 个地址可以使用。这意味着在我们的星球上每平方米大约有 6.67 * 10²⁷ 个 IPv6 地址。

*   路由器仅在它们的路由表里存放网络地址集， 这就减少路由表的平均空间到 8192 个条目。

IPv6 还有其它许多有用的功能，如：

*   地址自动配置 ([RFC2462](http://www.ietf.org/rfc/rfc2462.txt))

*   Anycast (任意播) 地址(“一对多”)

*   强制的多播地址

*   IPsec (IP 安全)

*   简单的头结构

*   移动的 (Mobile) IP

*   IPv6 到 IPv4 的转换机制

要更多信息，请查看：

*   IPv6 概观，在 [playground.sun.com](http://playground.sun.com/pub/ipng/html/ipng-main.html)

*   [KAME.net](http://www.kame.net)

### 32.12.1. 关于 IPv6 地址的背景知识

有几种不同类型的 IPv6 地址：Unicast，Anycast 和 Multicast。

Unicast 地址是为人们所熟知的地址。一个被发送到 unicast 地址的包实际上会到达属于这个地址的接口。

Anycast 地址语义上与 unicast 地址没有差别， 只是它们强调一组接口。指定为 anycast 地址的包会到达最近的 (以路由为单位) 接口。Anycast 地址可能只被路由器使用。

Multicast 地址标识一组接口。指定为 multicast 地址的包会到达属于 multicast 组的所有的接口。

### 注意:

IPv4 广播地址 (通常为 `xxx.xxx.xxx.255`) 由 IPv6 的 multicast 地址来表示。

表 32.3. 保留的 IPv6 地址

| IPv6 地址 | 预定长度 (bits) | 描述 | 备注 |
| --- | --- | --- | --- |
| `::` | 128 bits | 未指定 | 类似 IPv4 中的 `0.0.0.0` |
| `::1` | 128 bits | 环回地址 | 类似 IPv4 中的 `127.0.0.1` |
| `::00:xx:xx:xx:xx` | 96 bits | 嵌入的 IPv4 | 低 32 bits 是 IPv4 地址。这也称作 “IPv4 兼容 IPv6 地址” |
| `::ff:xx:xx:xx:xx` | 96 bits | IPv4 影射的 IPv6 地址 | 低的 32 bits 是 IPv4 地址。 用于那些不支持 IPv6 的主机。 |
| `fe80::` - `feb::` | 10 bits | 链路环回 | 类似 IPv4 的环回地址。 |
| `fec0::` - `fef::` | 10 bits | 站点环回 |   |
| `ff::` | 8 bits | 多播 |   |
| `001` (base 2) | 3 bits | 全球多播 | 所有的全球多播地址都指定到这个地址池中。前三个二进制位是 “001”。 |

### 32.12.2. IPv6 地址的读法

规范形式被描述为：`x:x:x:x:x:x:x:x`， 每一个“x”就是一个 16 位的 16 进制值。当然， 每个十六进制块以三个“0”开始头的也可以省略。如 `FEBC:A574:382B:23C1:AA49:4592:4EFE:9982`

通常一个地址会有很长的子串全部为零， 因此每个地址的这种子串常被简写为“::”。 例如：`fe80::1` 对应的规范形式是 `fe80:0000:0000:0000:0000:0000:0000:0001`。

第三种形式是以众所周知的用点“.”作为分隔符的十进制 IPv4 形式，写出最后 32 Bit 的部分。例如 `2002::10.0.0.1` 对应的十进制正规表达方式是 `2002:0000:0000:0000:0000:0000:0a00:0001` 它也相当于写成 `2002::a00:1`.

到现在，读者应该能理解下面的内容了：

```
# `ifconfig`
```

```
rl0: flags=8943<UP,BROADCAST,RUNNING,PROMISC,SIMPLEX,MULTICAST> mtu 1500
         inet 10.0.0.10 netmask 0xffffff00 broadcast 10.0.0.255
         inet6 fe80::200:21ff:fe03:8e1%rl0 prefixlen 64 scopeid 0x1
         ether 00:00:21:03:08:e1
         media: Ethernet autoselect (100baseTX )
         status: active
```

`fe80::200:21ff:fe03:8e1%rl0` 是一个自动配置的链路环回地址。它作为自动配置的一部分由 MAC 生成。

关于 IPv6 地址的结构的更多信息，请参看 [RFC3513](http://www.ietf.org/rfc/rfc3513.txt)。

### 32.12.3. 进行连接

目前，有四种方式可以连接到其它 IPv6 主机和网络：

*   咨询你的互联网服务提供商是否提供 IPv6。

*   [SixXS](http://www.sixxs.net) 向全球范围提供通道。

*   使用 6-to-4 通道 ([RFC3068](http://www.ietf.org/rfc/rfc3068.txt))

*   如果您使用的是拨号连接， 则可以使用 [net/freenet6](http://www.freebsd.org/cgi/url.cgi?ports/net/freenet6/pkg-descr) port。

### 32.12.4. IPv6 世界里的 DNS

对于 IPv6 有两种类型的 DNS 记录：IETF 已经宣布 A6 是过时标准；现行的标准是 AAAA 记录。

使用 AAAA 记录是很简单的。通过增加下面内容， 给您的主机分配置您刚才接收到的新的 IPv6 地址：

```
MYHOSTNAME           AAAA    MYIPv6ADDR
```

到您的主域 DNS 文件里，就可以完成。要是您自已没有 DNS 域服务，您可以询问您的 DNS 提供商。目前的 bind 版本 (version 8.3 与 9) 和 [dns/djbdns](http://www.freebsd.org/cgi/url.cgi?ports/dns/djbdns/pkg-descr)(含 IPv6 补丁) 支持 AAAA 记录。

### 32.12.5. 在 `/etc/rc.conf` 中进行所需的修改

#### 32.12.5.1. IPv6 客户机设置

这些设置将帮助您把一台您 LAN 上的机器配置为一台客户机， 而不是路由器。 要让 [rtsol(8)](http://www.FreeBSD.org/cgi/man.cgi?query=rtsol&sektion=8&manpath=freebsd-release-ports) 在启动时自动配置您的网卡， 只需添加：

```
ipv6_enable="YES"
```

要自动地静态指定 IP 地址， 例如 `2001:471:1f11:251:290:27ff:fee0:2093`， 到 `fxp0` 上， 则写上：

```
ipv6_ifconfig_fxp0="2001:471:1f11:251:290:27ff:fee0:2093"
```

要指定 `2001:471:1f11:251::1` 作为默认路由， 需要在 `/etc/rc.conf` 中加入：

```
ipv6_defaultrouter="2001:471:1f11:251::1"
```

#### 32.12.5.2. IPv6 路由器/网关配置

这将帮助您从隧道提供商那里取得必要的资料， 并将这些资料转化为在重启时能够保持住的设置。 要在启动时恢复您的隧道， 需要在 `/etc/rc.conf` 中增加：

列出要配置的通用隧道接口， 例如 `gif0`：

```
gif_interfaces="gif0"
```

配置该接口使用本地端地址 *`MY_IPv4_ADDR`* 和远程端地址 *`REMOTE_IPv4_ADDR`*：

```
gifconfig_gif0="*`MY_IPv4_ADDR REMOTE_IPv4_ADDR`*"
```

应用分配给您用于 IPv6 隧道远端的 IPv6 地址， 需要增加：

```
ipv6_ifconfig_gif0="*`MY_ASSIGNED_IPv6_TUNNEL_ENDPOINT_ADDR`*"
```

此后十设置 IPv6 的默认路由。 这是 IPv6 隧道的另一端：

```
ipv6_defaultrouter="*`MY_IPv6_REMOTE_TUNNEL_ENDPOINT_ADDR`*"
```

#### 32.12.5.3. IPv6 隧道配置

如果服务器将您的网络通过 IPv6 路由到世界的其他角落， 您需要在 `/etc/rc.conf` 中添加下面的配置：

```
ipv6_gateway_enable="YES"
```

### 32.12.6. 路由宣告和主机自动配置

这节将帮助您配置 [rtadvd(8)](http://www.FreeBSD.org/cgi/man.cgi?query=rtadvd&sektion=8&manpath=freebsd-release-ports) 来宣示默认的 IPv6 路由。

要启用 [rtadvd(8)](http://www.FreeBSD.org/cgi/man.cgi?query=rtadvd&sektion=8&manpath=freebsd-release-ports) 您需要在 `/etc/rc.conf` 中添加：

```
rtadvd_enable="YES"
```

指定由哪个网络接口来完成 IPv6 路由请求非常重要。 举例来说， 让 [rtadvd(8)](http://www.FreeBSD.org/cgi/man.cgi?query=rtadvd&sektion=8&manpath=freebsd-release-ports) 使用 `fxp0`：

```
rtadvd_interfaces="fxp0"
```

接下来我们需要创建配置文件， `/etc/rtadvd.conf`。 示例如下：

```
fxp0:\
	:addrs#1:addr="2001:471:1f11:246::":prefixlen#64:tc=ether:
```

将 `fxp0` 改为您打算使用的接口名。

接下来， 将 `2001:471:1f11:246::` 改为分配给您的地址前缀。

如果您拥有专用的 `/64` 子网， 则不需要修改其他设置。 反之， 您需要把 `prefixlen#` 改为正确的值。

## 32.13. 异步传输模式 (ATM)

贡献者：Harti Brandt.中文翻译：张 雪平.

### 32.13.1. 配置 classical IP over ATM (PVCs)

Classical IP over ATM (CLIP) 是一种最简单的使用带 IP 的 ATM 的方法。 这种方法可以用在交换式连接 (SVC) 和永久连接 (PVC) 上。这部分描述的就是配置基于 PVC 的网络。

#### 32.13.1.1. 完全互连的配置

第一种使用 PVC 来设置 CLIP 的方式就是通过专用的 PVC 让网络里的每一台机子都互连在一起。 尽管这样配置起来很简单，但对于数量更多一点的机子来说就有些不切实际了。 例如我们有四台机子在网络里，每一台都使用一张 ATM 适配器卡连接到 ATM 网络。第一步就是规划 IP 地址和机子间的 ATM 连接。我们使用下面的：

| 主机 | IP 地址 |
| --- | --- |
| `hostA` | `192.168.173.1` |
| `hostB` | `192.168.173.2` |
| `hostC` | `192.168.173.3` |
| `hostD` | `192.168.173.4` |

为了建造完全交错的网络，我们需要在第一对机子间有一个 ATM 连接：

| 机器 | VPI.VCI 对 |
| --- | --- |
| `hostA` - `hostB` | 0.100 |
| `hostA` - `hostC` | 0.101 |
| `hostA` - `hostD` | 0.102 |
| `hostB` - `hostC` | 0.103 |
| `hostB` - `hostD` | 0.104 |
| `hostC` - `hostD` | 0.105 |

在每一个连接端 VPI 和 VCI 的值都可能会不同， 只是为了简单起见，我们假定它们是一样的。 下一步我们需要配置每一个主机上的 ATM 接口：

```
hostA# `ifconfig hatm0 192.168.173.1 up`
hostB# `ifconfig hatm0 192.168.173.2 up`
hostC# `ifconfig hatm0 192.168.173.3 up`
hostD# `ifconfig hatm0 192.168.173.4 up`
```

假定所有主机上的 ATM 接口都是 `hatm0`。 现在 PVC 需要配置到 `hostA` 上 (我们假定它们都已经配置在了 ATM 交换机上，至于怎么做的， 您就需要参考一下该交换机的手册了)。

```
hostA# `atmconfig natm add 192.168.173.2 hatm0 0 100 llc/snap ubr`
hostA# `atmconfig natm add 192.168.173.3 hatm0 0 101 llc/snap ubr`
hostA# `atmconfig natm add 192.168.173.4 hatm0 0 102 llc/snap ubr`

hostB# `atmconfig natm add 192.168.173.1 hatm0 0 100 llc/snap ubr`
hostB# `atmconfig natm add 192.168.173.3 hatm0 0 103 llc/snap ubr`
hostB# `atmconfig natm add 192.168.173.4 hatm0 0 104 llc/snap ubr`

hostC# `atmconfig natm add 192.168.173.1 hatm0 0 101 llc/snap ubr`
hostC# `atmconfig natm add 192.168.173.2 hatm0 0 103 llc/snap ubr`
hostC# `atmconfig natm add 192.168.173.4 hatm0 0 105 llc/snap ubr`

hostD# `atmconfig natm add 192.168.173.1 hatm0 0 102 llc/snap ubr`
hostD# `atmconfig natm add 192.168.173.2 hatm0 0 104 llc/snap ubr`
hostD# `atmconfig natm add 192.168.173.3 hatm0 0 105 llc/snap ubr`
```

当然，除 UBR 外其它的通信协定也可让 ATM 适配器支持这些。 此种情况下，通信协定的名字要跟人通信参数后边。工具 [atmconfig(8)](http://www.FreeBSD.org/cgi/man.cgi?query=atmconfig&sektion=8&manpath=freebsd-release-ports) 的帮助可以这样得到：

```
# `atmconfig help natm add`
```

或者在 [atmconfig(8)](http://www.FreeBSD.org/cgi/man.cgi?query=atmconfig&sektion=8&manpath=freebsd-release-ports) 手册页里得到。

相同的配置也可以通过 `/etc/rc.conf` 来完成。对于 `hostA`，看起来就象这样：

```
network_interfaces="lo0 hatm0"
ifconfig_hatm0="inet 192.168.173.1 up"
natm_static_routes="hostB hostC hostD"
route_hostB="192.168.173.2 hatm0 0 100 llc/snap ubr"
route_hostC="192.168.173.3 hatm0 0 101 llc/snap ubr"
route_hostD="192.168.173.4 hatm0 0 102 llc/snap ubr"
```

所有 CLIP 路由的当前状态可以使用如下命令获得：

```
hostA# `atmconfig natm show`
```

## 32.14. Common Address Redundancy Protocol (CARP， 共用地址冗余协议)

原作 Tom Rhodes.

Common Address Redundancy Protocol， 或简称 CARP 能够使多台主机共享同一 IP 地址。 在某些配置中， 这样做可以提高可用性， 或实现负载均衡。 下面的例子中， 这些主机也可以同时使用其他的不同的 IP 地址。

要启用 CARP 支持， 必须在 FreeBSD 内核配置中增加下列选项， 并按照 第 9 章 *配置 FreeBSD 的内核* 章节介绍的方法重新联编内核：

```
device	carp
```

另外的一个方法是在启动时加载 `if_carp.ko` 模块。 把如下的这行加入到 `/boot/loader.conf`：

```
if_carp_load="YES"
```

这样就可以使用 CARP 功能了， 一些具体的参数， 可以通过一系列 `sysctl` OID 来调整。

| OID | 描述 |
| --- | --- |
| `net.inet.carp.allow` | 接受进来的 CARP 包。 默认启用。 |
| `net.inet.carp.preempt` | 当主机中有一个 CARP 网络接口失去响应时， 这个选项将停止这台主机上所有的 CARP 接口。 默认禁用。 |
| `net.inet.carp.log` | 当值为 `0` 表示禁止记录所有日志。 值为 `1` 表示记录损坏的 CARP 包。任何大于 `1` 表示记录 CARP 网络接口的状态变化。默认值为 `1`。 |
| `net.inet.carp.arpbalance` | 使用 ARP 均衡本地网络流量。 默认禁用。 |
| `net.inet.carp.suppress_preempt` | 此只读 OID 显示抑制抢占的状态。 如果一个接口上的连接失去响应, 则抢占会被抑制。 当这个变量的值为 `0` 时，表示抢占未被抑制。 任何问题都会使 OID 递增。 |

CARP 设备可以通过 `ifconfig` 命令来创建。

```
# `ifconfig carp0 create`
```

在真实环境中， 这些接口需要一个称作 VHID 的标识编号。 这个 VHID 或 Virtual Host Identification (虚拟主机标识) 用于在网络上区分主机。

### 32.14.1. 使用 CARP 来改善服务的可用性 (CARP)

如前面提到的那样， CARP 的作用之一是改善服务的可用性。 这个例子中， 将为三台主机提供故障转移服务， 这三台服务器各自有独立的 IP 地址， 并提供完全一样的 web 内容。 三台机器以 DNS 轮询的方式提供服务。 用于故障转移的机器有两个 CARP 接口， 分别配置另外两台服务器的 IP 地址。 当有服务器发生故障时， 这台机器会自动得到故障机的 IP 地址。 这样以来， 用户就完全感觉不到发生了故障。 故障转移的服务器提供的内容和服务， 应与其为之提供热备份的服务器一致。

两台机器的配置， 除了主机名和 VHID 之外应完全一致。 在我们的例子中， 这两台机器的主机名分别是 `hosta.example.org` 和 `hostb.example.org`。 首先， 需要将 CARP 配置加入到 `rc.conf`。 对于 `hosta.example.org` 而言， `rc.conf` 文件中应包含下列配置：

```
hostname="hosta.example.org"
ifconfig_fxp0="inet 192.168.1.3 netmask 255.255.255.0"
cloned_interfaces="carp0"
ifconfig_carp0="vhid 1 pass testpass 192.168.1.50/24"
```

在 `hostb.example.org` 上， 对应的 `rc.conf` 配置则是：

```
hostname="hostb.example.org"
ifconfig_fxp0="inet 192.168.1.4 netmask 255.255.255.0"
cloned_interfaces="carp0"
ifconfig_carp0="vhid 2 pass testpass 192.168.1.51/24"
```

### 注意:

在两台机器上由 `ifconfig` 的 `pass` 选项指定的密码必须是一致的， 这一点非常重要。 `carp` 设备只会监听和接受来自持有正确密码的机器的公告。 此外， 不同虚拟主机的 VHID 必须不同。

第三台机器， `provider.example.org` 需要进行配置， 以便在另外两台机器出现问题时接管。 这台机器需要两个 `carp` 设备， 分别处理两个机器。 对应的 `rc.conf` 配置类似下面这样：

```
hostname="provider.example.org"
ifconfig_fxp0="inet 192.168.1.5 netmask 255.255.255.0"
cloned_interfaces="carp0 carp1"
ifconfig_carp0="vhid 1 advskew 100 pass testpass 192.168.1.50/24"
ifconfig_carp1="vhid 2 advskew 100 pass testpass 192.168.1.51/24"
```

配置两个 `carp` 设备， 能够让 `provider.example.org` 在两台机器中的任何一个停止响应时， 立即接管其 IP 地址。

### 注意:

默认的 FreeBSD 内核 *可能* 启用了主机间抢占。 如果是这样的话， `provider.example.org` 可能在正式的内容服务器恢复时不释放 IP 地址。 此时， 管理员必须手工强制 IP 回到原来内容服务器。 具体做法是在 `provider.example.org` 上使用下面的命令：

```
# `ifconfig carp0 down && ifconfig carp0 up`
```

这个操作需要在与出现问题的主机对应的那个 `carp` 接口上进行。

现在您已经完成了 CARP 的配置， 并可以开始测试了。 测试过程中， 可以随时重启或切断两台机器的网络。

如欲了解更多细节， 请参见 [carp(4)](http://www.FreeBSD.org/cgi/man.cgi?query=carp&sektion=4&manpath=freebsd-release-ports) 联机手册。