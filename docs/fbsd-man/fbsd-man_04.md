# 部分 III. 系统管理

FreeBSD 手册中其余章节的内容都是关于系统管理。 每一章节都从描述这章将要介绍的内容开始， 由浅入深对相关内容进行介绍。

这些章节在撰写时， 已经设计成了许多相互独立的部分， 如果您需要了解某部分内容， 直接阅读这部分内容即可， 而无需按照顺序， 也不必在您开始使用 FreeBSD 之前完整地阅读它们。

## 第 12 章 设置和调整

原作： Chern Lee.这份文档基于一份教程， 其作者是 Mike Smith.此外， 也参考了 tuning(7)， 其作者是 Matt Dillon.

## 12.1. 概述

使用 FreeBSD 的一个重要问题是系统配置。 正确地配置系统能充分地减少以后维护和升级系统所需的工作量。 这章将解释一些 FreeBSD 的配置过程，包括一些可以调整的 FreeBSD 系统的一些参数。

读完本章， 您将了解：

*   如何有效地利用文件系统和交换分区。

*   `rc.conf` 的基本设置以及 `/usr/local/etc/rc.d` 启动体系。

*   如何设置和测试网卡。

*   如何在您的网络设备上配置虚拟主机。

*   如何使用 `/etc` 下的各配置文件。

*   如何通过 `sysctl` 变量来对 FreeBSD 系统进行调优。

*   怎样调整磁盘性能和修改内核限制。

在阅读本章之前，您应该了解：

*   了解 UNIX® 和 FreeBSD 的基础知识 (第 4 章 *UNIX 基础*)。

*   熟悉内核配置编译的基础知识 (第 9 章 *配置 FreeBSD 的内核*)。

## 12.2. 初步配置

### 12.2.1. 分区规划

#### 12.2.1.1. 基本分区

当使用 [bsdlabel(8)](http://www.FreeBSD.org/cgi/man.cgi?query=bsdlabel&sektion=8&manpath=freebsd-release-ports) 或者 [sysinstall(8)](http://www.FreeBSD.org/cgi/man.cgi?query=sysinstall&sektion=8&manpath=freebsd-release-ports) 来分割您的文件系统的时候， 要记住硬盘驱动器外磁道传输数据要比从内磁道传输数据快。 因此应该将小的和经常访问的文件系统放在驱动器靠外的位置， 一些大的分区比如 `/usr` 应该放在磁盘比较靠里的位置。 以类似这样的顺序建立分区是一个不错的主意：root，swap， `/var`， `/usr`。

`/var` 分区的大小能反映您的机器使用情况。 `/var` 文件系统用来存储邮件， 日志文件和打印队列缓存， 特别是邮箱和日志文件可能会达到无法预料的大小， 这主要取决于在您的系统上有多少用户和您的日志文件可以保存多长时间。 大多数用户很少需要 `/var` 有 1GB 以上的闲置空间。

### 注意:

有时候 `/var/tmp` 需要很多的磁盘空间。 在使用 [pkg_add(1)](http://www.FreeBSD.org/cgi/man.cgi?query=pkg_add&sektion=1&manpath=freebsd-release-ports) 安装新的软件时，包管理工具会在 `/var/tmp` 中解压出一份临时拷贝。 大的软件包，像 Firefox， OpenOffice 或者 LibreOffice 在安装时如果 `/var/tmp` 中没有足够的空间就可能需要一些技巧了。

`/usr` 分区存储很多用来系统运行所需要的文件例如 [ports(7)](http://www.FreeBSD.org/cgi/man.cgi?query=ports&sektion=7&manpath=freebsd-release-ports) (建议这样做) 和源代码 (可选的)。 ports 和基本系统的源代码在安装时都是可选的， 但我们建议给这个分区至少保留 2GB 的可用空间。

当选择分区大小的时候，记住保留一些空间。 用完了一个分区的空间而在另一个分区上还有很多， 可能会导致出现一些错误。

### 注意:

一些用户会发现 [sysinstall(8)](http://www.FreeBSD.org/cgi/man.cgi?query=sysinstall&sektion=8&manpath=freebsd-release-ports) 的 `Auto-defaults` 自动分区有时会分配给 `/var` 和 `/` 较小的分区空间。 分区应该精确一些并且大一些。

#### 12.2.1.2. 交换分区

一般来讲，交换分区应该大约是系统内存 (RAM) 的两倍。 例如，如果机器有 128M 内存，交换文件应该是 256M。 较小内存的系统可以通过多一点地交换分区来提升性能。 不建议小于 256 兆的交换分区，并且扩充您的内存应该被考虑一下。 当交换分区最少是主内存的两倍的时候，内核的 VM (虚拟内存) 页面调度算法可以将性能调整到最好。如果您给机器添加更多内存， 配置太小的交换分区会导致 VM 页面扫描的代码效率低下。

在使用多块 SCSI 磁盘(或者不同控制器上的 IDE 磁盘)的大系统上， 建议在每个驱动器上建立交换分区(直到四个驱动器)。 交换分区应该大约一样大小。内核可以使用任意大小， 但内部数据结构则是最大交换分区的 4 倍。保持交换分区同样的大小， 可以允许内核最佳地调度交换空间来访问磁盘。 即使不太使用，分配大的交换分区也是好的， 在被迫重启之前它可以让您更容易的从一个失败的程序中恢复过来。

#### 12.2.1.3. 为什么要分区？

一些用户认为一个单独的大分区将会很好， 但是有很多原因会证明为什么这是个坏主意。首先， 每个分区有不同的分区特性，因此分开可以让文件系统调整它们。 例如，根系统和 `/usr` 一般只是读取，写入很少。 很多读写频繁的被放在 `/var` 和 `/var/tmp`中。

适当的划分一个系统， 在其中使用较小的分区， 这样， 那些以写为主的分区将不会比以读为主的分区付出更高的代价。 将以写为主的分区放在靠近磁盘的边缘， 例如放在实际的大硬盘的前面代替放在分区表的后面，将会提高您需要的分区的 I/O 性能。现在可能也需要在比较大的分区上有很好的 I/O 性能， 把他们移动到磁盘外围不会带来多大的性能提升，反而把 `/var` 移到外面会有很好的效果。最后涉及到安全问题。 一个主要是只读的小的、整洁的根分区可以提高从一个严重的系统崩溃中恢复过来的机会。

## 12.3. 核心配置

系统的配置信息主要位于 `/etc/rc.conf`。 这个文件包含了配置信息很大的一部分，主要在系统启动的时候来配置系统， 这个名字直接说明了这点；它也是 `rc*` 文件的配置信息。

系统管理员应该在 `rc.conf` 文件中建立记录来覆盖 `/etc/defaults/rc.conf` 中的默认设置。 这个默认文件不应该被逐字的复制到 `/etc` ―― 它包含的是默认值而不是一个例子。 所有特定的改变应该在 `rc.conf` 中。

在集群应用中，为了降低管理成本， 可以采用多种策略把涉及全站范围的设置从特定于系统的设置中分离出来。 推荐的方法是把系统范围的配置放到 `/etc/rc.conf.local` 文件中。 例如：

*   `/etc/rc.conf`:

    ```
    sshd_enable="YES"
    keyrate="fast"
    defaultrouter="10.1.1.254"
    ```

*   `/etc/rc.conf.local`:

    ```
    hostname="node1.example.org"
    ifconfig_fxp0="inet 10.1.1.1/8"
    ```

`rc.conf` 文件可以通过 `rsync` 或类似的程序来分发到所有的机器上， 而各自的 `rc.conf.local` 文件则保持不变。

使用 [sysinstall(8)](http://www.FreeBSD.org/cgi/man.cgi?query=sysinstall&sektion=8&manpath=freebsd-release-ports) 或者 `make world` 来升级系统不会覆盖 `rc.conf` 文件， 所以系统配置信息不会丢失。

### 提示:

配置文件 `/etc/rc.conf` 是通过 [sh(1)](http://www.FreeBSD.org/cgi/man.cgi?query=sh&sektion=1&manpath=freebsd-release-ports) 解析的。 这使得系统管理员可以在其中添加一些逻辑， 从而创建能够适应非常复杂的场景的配置。 请参阅联机手册 [rc.conf(5)](http://www.FreeBSD.org/cgi/man.cgi?query=rc.conf&sektion=5&manpath=freebsd-release-ports) 来了解关于这一话题的进一步信息。

## 12.4. 应用程序配置

典型的，被安装的应用程序有他自己的配置文件、语法等等。 从基本系统中分开他们是很重要的以至于他们可以容易的被 package 管理工具定位和管理

一般来说，这些文件被安装在 `/usr/local/etc`。这个例子中， 一个应用程序有很多配置文件并且创建了一个子目录来存放他们。

通常，当一个 port 或者 package 被安装的时候， 配置文件示例也同样被安装了。它们通常用 `.default` 的后缀来标识。如果不存在这个应用程序的配置文件， 它们会通过复制 `.default` 文件来创建。

例如，看一下这个目下的内容 `/usr/local/etc/apache`：

```
-rw-r--r--  1 root  wheel   2184 May 20  1998 access.conf
-rw-r--r--  1 root  wheel   2184 May 20  1998 access.conf.default
-rw-r--r--  1 root  wheel   9555 May 20  1998 httpd.conf
-rw-r--r--  1 root  wheel   9555 May 20  1998 httpd.conf.default
-rw-r--r--  1 root  wheel  12205 May 20  1998 magic
-rw-r--r--  1 root  wheel  12205 May 20  1998 magic.default
-rw-r--r--  1 root  wheel   2700 May 20  1998 mime.types
-rw-r--r--  1 root  wheel   2700 May 20  1998 mime.types.default
-rw-r--r--  1 root  wheel   7980 May 20  1998 srm.conf
-rw-r--r--  1 root  wheel   7933 May 20  1998 srm.conf.default
```

文件大小显示了只有 `srm.conf` 改变了。以后 Apache 的升级就不会改变这个文件。

## 12.5. 启动服务

Contributed by Tom Rhodes.

许多用户会选择使用 Ports Collection 来在 FreeBSD 上安装第三方软件。 很多情况下这可能需要进行一些配置以便让这些软件能够在系统初始化的过程中启动。 服务， 例如 [mail/postfix](http://www.freebsd.org/cgi/url.cgi?ports/mail/postfix/pkg-descr) 或 [www/apache13](http://www.freebsd.org/cgi/url.cgi?ports/www/apache13/pkg-descr) 就是这些需要在系统初始化时启动的软件包中的两个典型代表。 这一节解释了启动第三方软件所需要的步骤。

FreeBSD 包含的大多数服务，例如 [cron(8)](http://www.FreeBSD.org/cgi/man.cgi?query=cron&sektion=8&manpath=freebsd-release-ports)， 就是通过系统启动脚本启动的。 这些脚本也许会有些不同， 这取决于 FreeBSD 版本。 但是不管怎样， 需要考虑的一个重要方面是他们的启动配置文件要能被基本启动脚本识别捕获。

### 12.5.1. 扩展应用程序配置

现在 FreeBSD 提供了 `rc.d`， 这使得对应用软件的启动进行配置变得更加方便， 并提供了更多的其他功能。 例如， 使用在 rc.d 一节中所介绍的关键字， 应用程序就可以设置在某些其他服务， 例如 DNS 之后启动； 除此之外， 还可以通过 `rc.conf` 来指定一些额外的启动参数， 而不再需要将它们硬编码到启动脚本中。 基本的启动脚本如下所示：

```
#!/bin/sh
#
# PROVIDE: utility
# REQUIRE: DAEMON
# KEYWORD: shutdown

. /etc/rc.subr

name=utility
rcvar=utility_enable

command="/usr/local/sbin/utility"

load_rc_config $name

#
# DO NOT CHANGE THESE DEFAULT VALUES HERE
# SET THEM IN THE /etc/rc.conf FILE
#
utility_enable=${utility_enable-"NO"}
pidfile=${utility_pidfile-"/var/run/utility.pid"}

run_rc_command "$1"
```

这个脚本将保证 utility 能够在 `DAEMON` 服务之后启动。 它同时也提供了设置和跟踪 PID， 也就是进程 ID 文件的方法。

可以在 `/etc/rc.conf` 中加入：

```
utility_enable="YES"
```

这个方法也使得命令行参数、包含 `/etc/rc.subr` 中所提供的功能， 兼容 [rcorder(8)](http://www.FreeBSD.org/cgi/man.cgi?query=rcorder&sektion=8&manpath=freebsd-release-ports) 工具并提供更简单的通过 `rc.conf` 文件来配置的方法。

### 12.5.2. 用服务来启动服务

其他服务， 例如 POP3 服务器， IMAP， 等等， 也可以通过 [inetd(8)](http://www.FreeBSD.org/cgi/man.cgi?query=inetd&sektion=8&manpath=freebsd-release-ports) 来启动。 这一过程包括从 Ports Collection 安装相应的应用程序， 并把配置加入到 `/etc/inetd.conf` 文件， 或去掉当前配置中的某些注释。 如何使用和配置 inetd 在 inetd 一节中进行了更为深入的阐述。

一些情况下， 通过 [cron(8)](http://www.FreeBSD.org/cgi/man.cgi?query=cron&sektion=8&manpath=freebsd-release-ports) 来启动系统服务也是一种可行的选择。 这种方法有很多好处， 因为 `cron` 会以 `crontab` 的文件属主身份执行那些进程。 这使得普通用户也能够执行他们的应用。

`cron` 工具提供了一个独有的功能， 以 `@reboot` 来指定时间。 这样的设置将在 [cron(8)](http://www.FreeBSD.org/cgi/man.cgi?query=cron&sektion=8&manpath=freebsd-release-ports) 启动时运行， 通常这也是系统初始化的时候。

## 12.6. 配置 `cron`

Contributed by Tom Rhodes.

FreeBSD 最有用的软件包(utilities)中的一个是 [cron(8)](http://www.FreeBSD.org/cgi/man.cgi?query=cron&sektion=8&manpath=freebsd-release-ports)。 `cron` 软件在后台运行并且经常检查 `/etc/crontab` 文件。`cron` 软件也检查 `/var/cron/tabs` 目录，搜索新的 `crontab` 文件。这些 `crontab` 文件存储一些 `cron` 在特定时间执行任务的信息。

`cron` 程序使用两种不同类型的配置文件， 即系统 crontab 和用户 crontabs。 两种格式的唯一区别是第六个字段。 在系统 crontab 中，第六个字段是用于执行命令的用户名。 这给予了系统 crontab 以任意用户身份执行命令的能力。 在用户 crontab 中， 第六个字段是要执行的命令， 所有的命令都会以这个用户自己的身份执行； 这是一项重要的安全功能。

### 注意:

同其他用户一样， `root` 用户也可以有自己的 crontab。 它不同于 `/etc/crontab` (也就是系统 crontab)。 由于有系统 crontab 的存在， 通常并不需要给 `root` 建立单独的用户 crontab。

让我们来看一下 `/etc/crontab` 文件：

```
# /etc/crontab - root's crontab for FreeBSD
#
# $FreeBSD: src/etc/crontab,v 1.32 2002/11/22 16:13:39 tom Exp $
# ![1](img/1.png)
#
SHELL=/bin/sh
PATH=/etc:/bin:/sbin:/usr/bin:/usr/sbin ![2](img/2.png)
HOME=/var/log
#
#
#minute	hour	mday	month	wday	who	command ![3](img/3.png)
#
#
*/5	*	*	*	*	root	/usr/libexec/atrun ![4](img/4.png) 
```

| ![1](img/#co-comments) | 像大多数 FreeBSD 配置文件一样，`#` 字符是注释。 这样， 就可以编写注释来说明要执行什么操作， 以及这样做的原因。 需要注意的是， 注释应该另起一行， 而不能跟命令放在同一行上， 否则它们会被看成命令的一部分。 这个文件中的空行会被忽略。 |
| ![2](img/#co-env) | 首先应该定义环境变量。等号 (`=`) 字符用来定义任何环境变量，像这个例子用到了 `SHELL`，`PATH` 和 `HOME` 变量。如果 shell 行被忽略掉，`cron` 将会用默认值 `sh`。如果 `PATH` 变量被忽略， 那么就没有默认值并且需要指定文件绝对位置。如果 `HOME` 被忽略，`cron` 将用用执行者的 home 目录。 |
| ![3](img/#co-field-descr) | 这一行定义了七个字段。它们是 `minute`、 `hour`、`mday`、 `month`、`wday`、 `who` 和 `command`。 它们差不多已经说明了各自的用处。Minute 是命令要运行时的分钟，Hour 跟 minute 差不多，只是用小时来表示。Mday 是每个月的天。Month 跟 hour 还有 minute 都差不多，用月份来表示。wday 字段表示星期几。 所有这些字段的值必须是数字并且用 24 小时制来表示。“who” 字段是特别的，并且只在 `/etc/crontab` 文件中存在。 这个字段指定了命令应该以哪个用户的身份来运行。当一个用户添加了他(她)的 `crontab` 文件的时候，他们就会没有这个字段选项。最后，是 `command` 字段。这是最后的一个字段， 所以自然就是它指定要运行的程序。 |
| ![4](img/#co-main) | 最后一行定义了上面所说的值。注意这里我们有一个 `*/5` 列表，紧跟着是一些 `*` 字符。`*` 字符代表“开始到最后”， 也可以被解释成 *每次*。所以，根据这行， 显然表明了无论在何时每隔 5 分钟以 `root` 身份来运行 `atrun` 命令。查看 [atrun(8)](http://www.FreeBSD.org/cgi/man.cgi?query=atrun&sektion=8&manpath=freebsd-release-ports) 手册页以获得 `atrun` 的更多信息。命令可以有任意多个传递给它们的标志。无论怎样， 扩展到多行的命令应该用反斜线(“\”)来续行。 |

这是每个 `crontab` 文件的基本设置， 虽然它们有一个不同。第六行我们指定的用户名只存在于系统 `/etc/crontab` 文件。这个字段在普通用户的 `crontab` 文件中应该被忽略。

### 12.6.1. 安装 Crontab

### 重要:

绝对不要用这种方法来编辑/安装系统 crontab。 您需要做的只是使用自己喜欢的编辑器： `cron` 程序会注意到文件发生了变化， 并立即开始使用新的版本。参见 这个 FAQ 项目 以了解进一步的情况。

要安装刚写好的用户 `crontab`， 首先使用最习惯的编辑器来创建一个符合要求格式的文件，然后用 `crontab` 程序来完成。最常见的用法是：

```
% `crontab crontab-file`
```

在前面的例子中， `crontab-file` 是一个事先写好的 `crontab`。

还有一个选项用来列出安装的 `crontab` 文件： 只要传递 `-l` 选项给 `crontab` 然后看一下输出。

用户想不用模板(已经存在的文件)而直接安装他的 crontab 文件，用 `crontab -e` 选项也是可以的。 它将会启动一个编辑器并且创建一个新文件，当这个文件被保存的时候， 它会自动的用 `crontab` 来安装这个文件。

如果您稍后想要彻底删除自己的用户 `crontab` 可以使用 `crontab` 的 `-r` 选项。

## 12.7. 在 FreeBSD 中使用 rc

Contributed by Tom Rhodes.

在 2002 年， FreeBSD 整合了来自 NetBSD 的 `rc.d` 系统， 并通过它来完成系统的初始化工作。 用户要注意在 `/etc/rc.d` 目录下的文件。 这里面的许多文件是用来管理基础服务的， 它们可以通过 `start`、 `stop`， 以及 `restart` 选项来控制。 举例来说， [sshd(8)](http://www.FreeBSD.org/cgi/man.cgi?query=sshd&sektion=8&manpath=freebsd-release-ports) 可以通过下面的命令来重启：

```
# `/etc/rc.d/sshd restart`
```

对其它服务的操作与此类似。 当然， 这些服务通常是在启动时根据 [rc.conf(5)](http://www.FreeBSD.org/cgi/man.cgi?query=rc.conf&sektion=5&manpath=freebsd-release-ports) 自动启动的。 例如， 要配置使系统启动时启动网络地址转换服务， 可以简单地通过在 `/etc/rc.conf` 中加入如下设置来完成：

```
natd_enable="YES"
```

如果 `natd_enable="NO"` 行已经存在， 只要简单的把 `NO` 改成 `YES` 即可。 rc 脚本在下次重新启动的时候会自动的装载所需要的服务， 像下面所描述的那样。

由于 `rc.d` 系统在系统启动/关闭时首先启动/停止服务，如果设置了适当的 `/etc/rc.conf` 变量，标准的 `start`、`stop` 和 `restart` 选项将会执行他们的动作。例如 `sshd restart` 命令只在 `/etc/rc.conf` 中的 `sshd_enable` 设置成 `YES` 的时候工作。不管是否在 `/etc/rc.conf` 中设置了，要 `start`、`stop` 或者 `restart` 一个服务，命令前可以加上一个“one”前缀。例如要不顾当前 `/etc/rc.conf` 的设置重新启动 `sshd`，执行下面的命令：

```
# `/etc/rc.d/sshd onerestart`
```

用选项 `rcvar` 可以简单来的检查 `/etc/rc.conf` 中用适当的 `rc.d` 脚本启动的服务是否被启用。从而管理员可以运行这样的程序来检查 `sshd` 是否真的在 `/etc/rc.conf` 中被启动了：

```
# `/etc/rc.d/sshd rcvar`
# sshd
$sshd_enable=YES
```

### 注意:

第二行 (`# sshd`) 是从 `sshd` 命令中输出的，而不是 `root` 控制台。

为了确定一个服务是否真的在运行，可以用 `status` 选项。例如验证 `sshd` 是否真的启动了：

```
# `/etc/rc.d/sshd status`
sshd is running as pid 433.
```

有些时候也可以 `reload` 服务。 这一操作实际上是向服务发送一个信号， 来强制其重新加载配置。 多数情况下， 发给服务的会是 `SIGHUP` 信号。 并非所有服务都支持这一功能。

`rc.d` 系统不仅用于网络服务， 它也为系统初始化中的多数过程提供支持。 比如 `bgfsck` 文件， 当它被执行时， 将会给出下述信息：

```
Starting background file system checks in 60 seconds.
```

这个文件用做后台文件系统检查，系统初始化的时候完成。

很多系统服务依赖其他服务提供的相应功能。例如，NIS 和其他基于 RPC 的服务启动可能在 `rpcbind` 服务启动之前失败。 要解决这个问题，依赖关系信息和其他头信息当作注释被包含在每个启动脚本文件的前面。 程序在系统初始化时分析这些注释以决定调用其他系统服务来满足依赖关系。

下面的字句必须被包含在所有的启动脚本文件里， （他们都是 [rc.subr(8)](http://www.FreeBSD.org/cgi/man.cgi?query=rc.subr&sektion=8&manpath=freebsd-release-ports) 用来 “enable” 启动脚本必需的）:

*   `PROVIDE`: 指定此文件所提供的服务的名字。

以下的字句可以被包含在启动文件的顶部。严格来说他们不是必需的， 但作为对于 [rcorder(8)](http://www.FreeBSD.org/cgi/man.cgi?query=rcorder&sektion=8&manpath=freebsd-release-ports) 有一定的提示作用：

*   `REQUIRE`: 列出此服务启动之前所需要的其他服务。 此脚本提供的服务会在指定的那些服务 *之后* 启动。

*   `BEFORE`: 列出依赖此服务的其他服务。 此脚本提供的服务将在指定的那些服务 *之前* 启动。

通过在启动脚本中仔细设定这些关键字， 系统管理员可以很有条理的控制脚本的启动顺序， 进而避免使用像其他 UNIX® 操作系统那样混乱的 “runlevels”。

更多关于 `rc.d` 系统的信息， 可以在 [rc(8)](http://www.FreeBSD.org/cgi/man.cgi?query=rc&sektion=8&manpath=freebsd-release-ports) 和 [rc.subr(8)](http://www.FreeBSD.org/cgi/man.cgi?query=rc.subr&sektion=8&manpath=freebsd-release-ports) 联机手册中找到。 如果您有意撰写自己的 `rc.d` 脚本， 或对现有的脚本进行一些改进， 也可以参考 这篇文章。

## 12.8. 设置网卡

Contributed by Marc Fonvieille.

现在我们不可想象一台计算机没有网络连接的情况。 添加和配置一块网卡是任何 FreeBSD 系统管理员的一项基本任务。

### 12.8.1. 查找正确的驱动程序

在开始之前，您应该知道您的网卡类型，它用的芯片和它是 PCI 还是 ISA 网卡。FreeBSD 支持很多种 PCI 和 ISA 网卡。 可以查看您的版本硬件兼容性列表以确定您的网卡被支持。

确认系统能够支持您的网卡之后， 您还需要为它选择合适的驱动程序。 `/usr/src/sys/conf/NOTES` 和 `/usr/src/sys/arch/conf/NOTES` 将为您提供所支持的一些网卡和芯片组的信息。 如果您怀疑驱动程序是否使所要找的那一个， 请参考驱动程序的联机手册。 联机手册将提供关于所支持的硬件更详细的信息， 甚至还包括可能发生的问题。

如果您的网卡很常见的话， 大多数时候您不需要为驱动浪费精力。 常用的网卡在 `GENERIC` 内核中已经支持了， 所以您的网卡在启动时就会显示出来，像是：

```
dc0: <82c169 PNIC 10/100BaseTX> port 0xa000-0xa0ff mem 0xd3800000-0xd38
000ff irq 15 at device 11.0 on pci0
miibus0: <MII bus> on dc0
bmtphy0: <BCM5201 10/100baseTX PHY> PHY 1 on miibus0
bmtphy0:  10baseT, 10baseT-FDX, 100baseTX, 100baseTX-FDX, auto
dc0: Ethernet address: 00:a0:cc:da:da:da
dc0: [ITHREAD]
dc1: <82c169 PNIC 10/100BaseTX> port 0x9800-0x98ff mem 0xd3000000-0xd30
000ff irq 11 at device 12.0 on pci0
miibus1: <MII bus> on dc1
bmtphy1: <BCM5201 10/100baseTX PHY> PHY 1 on miibus1
bmtphy1:  10baseT, 10baseT-FDX, 100baseTX, 100baseTX-FDX, auto
dc1: Ethernet address: 00:a0:cc:da:da:db
dc1: [ITHREAD]
```

在这个例子中，我们看到有两块使用 [dc(4)](http://www.FreeBSD.org/cgi/man.cgi?query=dc&sektion=4&manpath=freebsd-release-ports) 驱动的网卡在系统中。

如果您的网卡没有出现在 `GENERIC` 中， 则需要手工加载合适的驱动程序。 要完成这项工作可以使用下面两种方法之一：

*   最简单的办法是用 [kldload(8)](http://www.FreeBSD.org/cgi/man.cgi?query=kldload&sektion=8&manpath=freebsd-release-ports) 加载网卡对应的内核模块。 除此之外， 通过在 `/boot/loader.conf` 文件中加入适当的设置， 也可以让系统在引导时自动加载这些模块。 不过， 并不是所有的网卡都能够通过这种方法提供支持； ISA 网卡是比较典型的例子。

*   另外， 您也可以将网卡的支持静态联编进内核。 察看 `/usr/src/sys/conf/NOTES`， `/usr/src/sys/arch/conf/NOTES` 以及驱动程序的联机手册以了解需要在您的内核配置文件中加一些什么。 要了解关于重新编译内核的进一步细节， 请参见 第 9 章 *配置 FreeBSD 的内核*。 如果您的卡在引导时可以被内核 (`GENERIC`) 识别， 您应该不需要编译新的内核。

#### 12.8.1.1. 使用 Windows® NDIS 驱动程序

不幸的是， 许多厂商由于认为驱动程序会涉及许多敏感的商业机密， 至今仍不愿意将把驱动程序作为开放源代码形式发布列入他们的时间表。 因此， FreeBSD 和其他操作系统的开发者就只剩下了两种选择： 要么经历长时间的痛苦过程来对驱动进行逆向工程， 要么使用现存的为 Microsoft® Windows® 平台提供的预编译版本的驱动程序。 包括参与 FreeBSD 开发的绝大多数开发人员， 都选择了后一种方法。

得益于 Bill Paul (wpaul) 的工作， 已经可以 “直接地” 支持 网络驱动接口标准 (NDIS, Network Driver Interface Specification) 了。 FreeBSD NDISulator (也被称为 Project Evil) 可以支持二进制形式的 Windows® 驱动程序， 并让它相信正在运行的是 Windows®。 由于 [ndis(4)](http://www.FreeBSD.org/cgi/man.cgi?query=ndis&sektion=4&manpath=freebsd-release-ports) 驱动使用的是用于 Windows® 的二进制形式的驱动， 因此它只能在 i386™ 和 amd64 系统上使用。

### 注意:

[ndis(4)](http://www.FreeBSD.org/cgi/man.cgi?query=ndis&sektion=4&manpath=freebsd-release-ports) 驱动在设计时主要提供了 PCI、 CardBus 和 PCMCIA 设备的支持， 而 USB 设备目前则没有提供支持。

要使用 NDISulator， 您需要三件东西：

1.  内核的源代码

2.  二进制形式的 Windows® XP 驱动程序 (扩展名为 `.SYS`)

3.  Windows® XP 驱动程序配置文件 (扩展名为 `.INF`)

您需要找到用于您的卡的这些文件。 一般而言， 这些文件可以在随卡附送的 CD 或制造商的网站上找到。 在下面的例子中， 我们用 `W32DRIVER.SYS` 和 `W32DRIVER.INF` 来表示这些文件。

### 注意:

不能在 FreeBSD/amd64 上使用 Windows®/i386 驱动程序。 必须使用 Windows®/amd64 驱动才能在其上正常工作。

接下来的步骤是将二进制形式的驱动程序组装成内核模块。 要完成这一任务， 需要以 `root` 用户的身份执行 [ndisgen(8)](http://www.FreeBSD.org/cgi/man.cgi?query=ndisgen&sektion=8&manpath=freebsd-release-ports)：

```
# `ndisgen /path/to/W32DRIVER.INF /path/to/W32DRIVER.SYS`
```

[ndisgen(8)](http://www.FreeBSD.org/cgi/man.cgi?query=ndisgen&sektion=8&manpath=freebsd-release-ports) 是一个交互式的程序， 它会提示您输入所需的一些其他的额外信息； 这些工作完成之后， 它会在当前目录生成一个内核模块文件， 这个文件可以通过下述命令来加载：

```
# `kldload ./W32DRIVER_SYS.ko`
```

除了刚刚生成的内核模块之外， 还必须加载 `ndis.ko` 和 `if_ndis.ko` 这两个内核模块， 在您加载需要 [ndis(4)](http://www.FreeBSD.org/cgi/man.cgi?query=ndis&sektion=4&manpath=freebsd-release-ports) 的模块时， 通常系统会自动完成这一操作。 如果希望手工加载它们， 则可以使用下列命令：

```
# `kldload ndis`
# `kldload if_ndis`
```

第一个命令会加载 NDIS 袖珍端口驱动封装模块， 而第二条命令则加载实际的网络接口。

现在请查看 [dmesg(8)](http://www.FreeBSD.org/cgi/man.cgi?query=dmesg&sektion=8&manpath=freebsd-release-ports) 来了解是否发生了错误。 如果一切正常， 您会看到类似下面的输出：

```
ndis0: <Wireless-G PCI Adapter> mem 0xf4100000-0xf4101fff irq 3 at device 8.0 on pci1
ndis0: NDIS API version: 5.0
ndis0: Ethernet address: 0a:b1:2c:d3:4e:f5
ndis0: 11b rates: 1Mbps 2Mbps 5.5Mbps 11Mbps
ndis0: 11g rates: 6Mbps 9Mbps 12Mbps 18Mbps 36Mbps 48Mbps 54Mbps
```

这之后， 就可以像使用其它网络接口 (例如 `dc0`) 一样来使用 `ndis0` 设备了。

与任何其它模块一样， 您也可以配置系统， 令其在启动时自动加载 NDIS 模块。 首先， 将生成的模块 `W32DRIVER_SYS.ko` 复制到 `/boot/modules` 目录中。 接下来， 在 `/boot/loader.conf` 中加入：

```
W32DRIVER_SYS_load="YES"
```

### 12.8.2. 配置网卡

现在正确的网卡驱动程序已经装载，那么就应该配置它了。 跟其他配置一样，网卡可以在安装时用 sysinstall 来配置。

要显示您系统上的网络接口的配置，输入下列命令：

```
% `ifconfig`
dc0: flags=8843<UP,BROADCAST,RUNNING,SIMPLEX,MULTICAST> metric 0 mtu 1500
        options=80008<VLAN_MTU,LINKSTATE>
        ether 00:a0:cc:da:da:da
        inet 192.168.1.3 netmask 0xffffff00 broadcast 192.168.1.255
        media: Ethernet autoselect (100baseTX <full-duplex>)
        status: active
dc1: flags=8802<UP,BROADCAST,RUNNING,SIMPLEX,MULTICAST> metric 0 mtu 1500
        options=80008<VLAN_MTU,LINKSTATE>
        ether 00:a0:cc:da:da:db
        inet 10.0.0.1 netmask 0xffffff00 broadcast 10.0.0.255
        media: Ethernet 10baseT/UTP
        status: no carrier
plip0: flags=8810<POINTOPOINT,SIMPLEX,MULTICAST> metric 0 mtu 1500
lo0: flags=8049<UP,LOOPBACK,RUNNING,MULTICAST> metric 0 mtu 16384
        options=3<RXCSUM,TXCSUM>
        inet6 fe80::1%lo0 prefixlen 64 scopeid 0x4
        inet6 ::1 prefixlen 128
        inet 127.0.0.1 netmask 0xff000000
        nd6 options=3<PERFORMNUD,ACCEPT_RTADV>
```

在这个例子中，显示出了下列设备：

*   `dc0`: 第一个以太网接口

*   `dc1`: 第二个以太网接口

*   `plip0`： 并口 (如果系统中有并口的话)

*   `lo0`: 回环设备

FreeBSD 使用内核引导时检测到的网卡驱动顺序来命名网卡。例如 `sis2` 是系统中使用 [sis(4)](http://www.FreeBSD.org/cgi/man.cgi?query=sis&sektion=4&manpath=freebsd-release-ports) 驱动的第三块网卡。

在这个例子中，`dc0` 设备启用了。主要表现在：

1.  `UP` 表示这块网卡已经配置完成准备工作。

2.  这块网卡有一个 Internet (`inet`) 地址 (这个例子中是 `192.168.1.3`)。

3.  它有一个有效的子网掩码 (`netmask`； `0xffffff00` 等同于 `255.255.255.0`)。

4.  它有一个有效的广播地址 (这个例子中是 `192.168.1.255`)。

5.  网卡的 MAC (`ether`) 地址是 `00:a0:cc:da:da:da`

6.  物理传输媒介模式处于自动选择状态 (`media: Ethernet autoselect (100baseTX <full-duplex>)`)。我们看到 `dc1` 被配置成运行在 `10baseT/UTP` 模式下。 要了解驱动媒介类型的更多信息， 请查阅它们的使用手册。

7.  连接状态 (`status`)是 `active`，也就是说连接信号被检测到了。对于 `dc1`，我们看到 `status: no carrier`。 这通常是网线没有插好。

如果 [ifconfig(8)](http://www.FreeBSD.org/cgi/man.cgi?query=ifconfig&sektion=8&manpath=freebsd-release-ports) 的输出显示了类似于：

```
dc0: flags=8843<BROADCAST,SIMPLEX,MULTICAST> metric 0 mtu 1500
        options=80008<VLAN_MTU,LINKSTATE>
        ether 00:a0:cc:da:da:da
        media: Ethernet autoselect (100baseTX <full-duplex>)
        status: active
```

的信息，那么就是还没有配置网卡。

要配置网卡，您需要 `root` 权限。 网卡配置可以通过使用 [ifconfig(8)](http://www.FreeBSD.org/cgi/man.cgi?query=ifconfig&sektion=8&manpath=freebsd-release-ports) 命令行方式来完成， 但是这样每次启动都要做一遍。放置网卡配置信息的文件是 `/etc/rc.conf`。

用您自己喜欢的编辑器打开 `/etc/rc.conf`。 并且您需要为每一块系统中存在的网卡添加一行， 在我们的例子中，添加如下几行：

```
ifconfig_dc0="inet 192.168.1.3 netmask 255.255.255.0"
ifconfig_dc1="inet 10.0.0.1 netmask 255.255.255.0 media 10baseT/UTP"
```

用自己正确的设备名和地址来替换例子中的 `dc0`，`dc1` 等内容。您应该应该查阅网卡驱动和 [ifconfig(8)](http://www.FreeBSD.org/cgi/man.cgi?query=ifconfig&sektion=8&manpath=freebsd-release-ports) 的手册页来了解各选项，也要查看一下 [rc.conf(5)](http://www.FreeBSD.org/cgi/man.cgi?query=rc.conf&sektion=5&manpath=freebsd-release-ports) 帮助页来了解 `/etc/rc.conf` 的语法。

如果在安装的时候配置了网络，关于网卡的一些行可能已经存在了。 所以在添加新行前仔细检查一下 `/etc/rc.conf`。

您也可能需要编辑 `/etc/hosts` 来添加局域网中不同的机器名称和 IP 地址， 如果它们不在那里的话。 请查看联机手册 [hosts(5)](http://www.FreeBSD.org/cgi/man.cgi?query=hosts&sektion=5&manpath=freebsd-release-ports) 和 `/usr/share/examples/etc/hosts` 以了解更多信息。

### 注意:

如果计划通过这台机器访问 Internet， 您还需要手工配置默认网关和域名解析服务器：

```
# `echo 'defaultrouter="your_default_router"' >> /etc/rc.conf`
# `echo 'nameserver your_DNS_server' >> /etc/resolv.conf`
```

### 12.8.3. 测试和调试

对 `/etc/rc.conf` 做了必要的修改之后应该重启系统以应用对接口的修改， 并且确认系统重启后没有任何配置错误。 另外您也可以重启网络系统：

```
# `/etc/rc.d/netif restart`
```

### 注意:

如果在 `/etc/rc.conf` 中配置了默认网关， 还需要运行下面的命令：

```
# `/etc/rc.d/routing restart`
```

网络系统重启之后， 应测试网络接口。

#### 12.8.3.1. 测试以太网卡

为了确认网卡被正确的配置了，在这里我们要做两件事情。首先， ping 自己的网络接口，接着 ping 局域网内的其他机器。

首先测试本地接口：

```
% `ping -c5 192.168.1.3`
PING 192.168.1.3 (192.168.1.3): 56 data bytes
64 bytes from 192.168.1.3: icmp_seq=0 ttl=64 time=0.082 ms
64 bytes from 192.168.1.3: icmp_seq=1 ttl=64 time=0.074 ms
64 bytes from 192.168.1.3: icmp_seq=2 ttl=64 time=0.076 ms
64 bytes from 192.168.1.3: icmp_seq=3 ttl=64 time=0.108 ms
64 bytes from 192.168.1.3: icmp_seq=4 ttl=64 time=0.076 ms

--- 192.168.1.3 ping statistics ---
5 packets transmitted, 5 packets received, 0% packet loss
round-trip min/avg/max/stddev = 0.074/0.083/0.108/0.013 ms
```

现在我们应该 ping 局域网内的其他机器：

```
% `ping -c5 192.168.1.2`
PING 192.168.1.2 (192.168.1.2): 56 data bytes
64 bytes from 192.168.1.2: icmp_seq=0 ttl=64 time=0.726 ms
64 bytes from 192.168.1.2: icmp_seq=1 ttl=64 time=0.766 ms
64 bytes from 192.168.1.2: icmp_seq=2 ttl=64 time=0.700 ms
64 bytes from 192.168.1.2: icmp_seq=3 ttl=64 time=0.747 ms
64 bytes from 192.168.1.2: icmp_seq=4 ttl=64 time=0.704 ms

--- 192.168.1.2 ping statistics ---
5 packets transmitted, 5 packets received, 0% packet loss
round-trip min/avg/max/stddev = 0.700/0.729/0.766/0.025 ms
```

您如果您设置了 `/etc/hosts` 文件，也可以用机器名来替换 `192.168.1.2`。

#### 12.8.3.2. 调试

调试硬件和软件配置一直是一件头痛的事情， 从最简单的开始可以减轻一些痛苦。 例如网线是否插好了？是否配置好了网络服务？防火墙配置正确吗？ 是否使用了被 FreeBSD 支持的网卡？ 在发送错误报告之前您应该查看一下硬件说明， 升级 FreeBSD 到最新的 STABLE 版本， 看一下邮件列表或者在 Internet 上搜索一下。

如果网卡工作了， 但性能低下，应该好好阅读一下 [tuning(7)](http://www.FreeBSD.org/cgi/man.cgi?query=tuning&sektion=7&manpath=freebsd-release-ports) 联机手册。 您也可以检查一下网络配置， 不正确的设置会导致慢速的网络连接。

一些用户可能会在一些网卡上经历一到两次 device timeouts， 这通常是正常现象。 如果经常这样甚至引起麻烦， 则应确定一下它跟其他设备没有冲突。 仔细检查网线连接， 或者换一块网卡。

有时用户会看到少量 watchdog timeout 错误。 这种情况要做的第一件事就是检查线缆连接。 一些网卡需要支持总线控制的 PCI 插槽。 在一些老的主板上，只有一个 PCI 插槽支持 (一般是 slot 0)。 检查网卡和主板说明书来确定是不是这个问题。

No route to host 通常发生在如果系统不能发送一个路由到目的主机的包的时候。 这在没有指定默认路由或者网线没有插上时会发生。 检查 `netstat -rn` 的输出并确认有一个有效的路由能到达相应的主机。 如果没有，请查阅 第 32 章 *高级网络*。

ping: sendto: Permission denied 错误信息经常由防火墙的配置错误引起。 如果 `ipfw` 在内核中启用了但是没有定义规则， 那么默认的规则就是拒绝所有通讯，甚至 ping 请求！ 查阅 第 31 章 *防火墙* 以了解更多信息。

有时网卡性能低下或者低于平均水平， 这种情况最好把传输媒介模式从 `autoselect` 改变为正确的传输介质模式。 这通常对大多数硬件有用， 但可能不会解决所有人的问题。 接着，检查所有网络设置，并且阅读 [tuning(7)](http://www.FreeBSD.org/cgi/man.cgi?query=tuning&sektion=7&manpath=freebsd-release-ports) 手册页。

## 12.9. 虚拟主机

FreeBSD 的一个很普通的用途是虚拟主机站点， 一个服务器虚拟成很多服务器一样提供网络服务。 这通过在一个接口上绑定多个网络地址来实现。

一个特定的网络接口有一个“真实”的地址， 也可能有一些“别名”地址。这些别名通常用 `/etc/rc.conf` 中的记录来添加。

一个 `fxp0` 的别名记录类似于：

```
ifconfig_fxp0_alias0="inet xxx.xxx.xxx.xxx netmask xxx.xxx.xxx.xxx"
```

记住别名记录必须从 `alias0` 开始并且按顺序递增(例如 `_alias1`、 `_alias2`)。 配置程序将会停止在第一个缺少的数字的地方。

计算别名的子网掩码是很重要的，幸运的是它很简单。 对于一个接口来说，必须有一个描述子网掩码的地址。 任何在这个网段下的地址必须有一个全是 `1` 的子网掩码(通常表示为 `255.255.255.255` 或 `0xffffffff`。

举例来说， 假设使用 `fxp0` 连接到两个网络， 分别是 `10.1.1.0`， 其子网掩码为 `255.255.255.0`， 以及 `202.0.75.16`， 其子网掩码为 `255.255.255.240`。 我们希望从 `10.1.1.1` 到 `10.1.1.5` 以及从 `202.0.75.17` 到 `202.0.75.20` 的地址能够互相访问。 如前所述， 只有两个网段中的第一个地址 (本例中， `10.0.1.1` 和 `202.0.75.17`) 应使用真实的子网掩码； 其余的 (`10.1.1.2` 到 `10.1.1.5` 以及 `202.0.75.18` 到 `202.0.75.20`) 则必须配置为使用 `255.255.255.255` 作为子网掩码。

下面是根据上述描述所进行的 `/etc/rc.conf` 配置：

```
ifconfig_fxp0="inet 10.1.1.1 netmask 255.255.255.0"
ifconfig_fxp0_alias0="inet 10.1.1.2 netmask 255.255.255.255"
ifconfig_fxp0_alias1="inet 10.1.1.3 netmask 255.255.255.255"
ifconfig_fxp0_alias2="inet 10.1.1.4 netmask 255.255.255.255"
ifconfig_fxp0_alias3="inet 10.1.1.5 netmask 255.255.255.255"
ifconfig_fxp0_alias4="inet 202.0.75.17 netmask 255.255.255.240"
ifconfig_fxp0_alias5="inet 202.0.75.18 netmask 255.255.255.255"
ifconfig_fxp0_alias6="inet 202.0.75.19 netmask 255.255.255.255"
ifconfig_fxp0_alias7="inet 202.0.75.20 netmask 255.255.255.255"
```

## 12.10. 配置文件

### 12.10.1. `/etc` 布局

在配置信息中有很多的目录，这些包括：

| `/etc` | 一般的系统配置信息。这儿的数据是与特定系统相关的。 |
| `/etc/defaults` | 系统配置文件的默认版本。 |
| `/etc/mail` | 额外的 [sendmail(8)](http://www.FreeBSD.org/cgi/man.cgi?query=sendmail&sektion=8&manpath=freebsd-release-ports) 配置信息，其他 MTA 配置文件。 |
| `/etc/ppp` | 用于用户级和内核级 ppp 程序的配置。 |
| `/etc/namedb` | [named(8)](http://www.FreeBSD.org/cgi/man.cgi?query=named&sektion=8&manpath=freebsd-release-ports) 数据的默认位置。通常 `named.conf` 和区域文件存放在这里。 |
| `/usr/local/etc` | 被安装的应用程序配置文件。可以参考每个应用程序的子目录。 |
| `/usr/local/etc/rc.d` | 被安装程序的 启动/停止 脚本。 |
| `/var/db` | 特定系统自动产生的数据库文件，像 package 数据库，位置数据库等等。 |

### 12.10.2. 主机名

#### 12.10.2.1. `/etc/resolv.conf`

`/etc/resolv.conf` 指示了 FreeBSD 如何访问域名系统(DNS)。

`resolv.conf` 中最常见的记录是：

| `nameserver` | 按顺序要查询的名字服务器的 IP 地址，最多三个。 |
| `search` | 搜索机器名的列表。这通常由本地机器名的域决定。 |
| `domain` | 本地域名。 |

一个典型的 `resolv.conf` 文件：

```
search example.com
nameserver 147.11.1.11
nameserver 147.11.100.30
```

### 注意:

只能使用一个 `search` 和 `domain` 选项。

如果您在使用 DHCP，[dhclient(8)](http://www.FreeBSD.org/cgi/man.cgi?query=dhclient&sektion=8&manpath=freebsd-release-ports) 经常使用从 DHCP 服务器接受来的信息重写 `resolv.conf`。

#### 12.10.2.2. `/etc/hosts`

`/etc/hosts` 是 Internet 早期使用的一个简单文本数据库。 它结合 DNS 和 NIS 提供名字到 IP 地址的映射。 通过局域网连接的机器可以用这个简单的命名方案来替代设置一个 [named(8)](http://www.FreeBSD.org/cgi/man.cgi?query=named&sektion=8&manpath=freebsd-release-ports) 服务器。另外，`/etc/hosts` 也可以提供一个 Internet 名称的本地纪录以减轻需要从外部查询带来的负担。

```
# $FreeBSD$
#
#
# Host Database
#
# This file should contain the addresses and aliases for local hosts that
# share this file.  Replace 'my.domain' below with the domainname of your
# machine.
#
# In the presence of the domain name service or NIS, this file may
# not be consulted at all; see /etc/nsswitch.conf for the resolution order.
#
#
::1			localhost localhost.my.domain
127.0.0.1		localhost localhost.my.domain
#
# Imaginary network.
#10.0.0.2		myname.my.domain myname
#10.0.0.3		myfriend.my.domain myfriend
#
# According to RFC 1918, you can use the following IP networks for
# private nets which will never be connected to the Internet:
#
#	10.0.0.0	-   10.255.255.255
#	172.16.0.0	-   172.31.255.255
#	192.168.0.0	-   192.168.255.255
#
# In case you want to be able to connect to the Internet, you need
# real official assigned numbers.  Do not try to invent your own network
# numbers but instead get one from your network provider (if any) or
# from your regional registry (ARIN, APNIC, LACNIC, RIPE NCC, or AfriNIC.)
#
```

`/etc/hosts` 用简单的格式：

```
[Internet address] [official hostname] [alias1] [alias2] ...
```

例如：

```
10.0.0.1 myRealHostname.example.com myRealHostname foobar1 foobar2
```

参考 [hosts(5)](http://www.FreeBSD.org/cgi/man.cgi?query=hosts&sektion=5&manpath=freebsd-release-ports) 以获得更多信息。

### 12.10.3. 日志文件配置

#### 12.10.3.1. `syslog.conf`

`syslog.conf` 是 [syslogd(8)](http://www.FreeBSD.org/cgi/man.cgi?query=syslogd&sektion=8&manpath=freebsd-release-ports) 程序的配置文件。 它指出了的 `syslog` 哪种信息类型被存储在特定的日志文件中。

```
# $FreeBSD$
#
#       Spaces ARE valid field separators in this file. However,
#       other *nix-like systems still insist on using tabs as field
#       separators. If you are sharing this file between systems, you
#       may want to use only tabs as field separators here.
#       Consult the syslog.conf(5) manual page.
*.err;kern.debug;auth.notice;mail.crit          /dev/console
*.notice;kern.debug;lpr.info;mail.crit;news.err /var/log/messages
security.*                                      /var/log/security
mail.info                                       /var/log/maillog
lpr.info                                        /var/log/lpd-errs
cron.*                                          /var/log/cron
*.err                                           root
*.notice;news.err                               root
*.alert                                         root
*.emerg                                         *
# uncomment this to log all writes to /dev/console to /var/log/console.log
#console.info                                   /var/log/console.log
# uncomment this to enable logging of all log messages to /var/log/all.log
#*.*                                            /var/log/all.log
# uncomment this to enable logging to a remote log host named loghost
#*.*                                            @loghost
# uncomment these if you're running inn
# news.crit                                     /var/log/news/news.crit
# news.err                                      /var/log/news/news.err
# news.notice                                   /var/log/news/news.notice
!startslip
*.*                                             /var/log/slip.log
!ppp
*.*                                             /var/log/ppp.log
```

参考 [syslog.conf(5)](http://www.FreeBSD.org/cgi/man.cgi?query=syslog.conf&sektion=5&manpath=freebsd-release-ports) 手册页以获得更多信息

#### 12.10.3.2. `newsyslog.conf`

`newsyslog.conf` 是一个通常用 [cron(8)](http://www.FreeBSD.org/cgi/man.cgi?query=cron&sektion=8&manpath=freebsd-release-ports) 计划运行的 [newsyslog(8)](http://www.FreeBSD.org/cgi/man.cgi?query=newsyslog&sektion=8&manpath=freebsd-release-ports) 程序的配置文件。 [newsyslog(8)](http://www.FreeBSD.org/cgi/man.cgi?query=newsyslog&sektion=8&manpath=freebsd-release-ports) 指出了什么时候日志文件需要打包或者重新整理。 比如 `logfile` 被移动到 `logfile.0`，`logfile.0` 被移动到 `logfile.1` 等等。另外，日志文件可以用 [gzip(1)](http://www.FreeBSD.org/cgi/man.cgi?query=gzip&sektion=1&manpath=freebsd-release-ports) 来压缩，它们是这样的命名格式： `logfile.0.gz`，`logfile.1.gz` 等等。

`newsyslog.conf` 指出了哪个日志文件要被管理，要保留多少和它们什么时候被创建。 日志文件可以在它们达到一定大小或者在特定的日期被重新整理。

```
# configuration file for newsyslog
# $FreeBSD$
#
# filename          [owner:group]    mode count size when [ZB] [/pid_file] [sig_num]
/var/log/cron                           600  3     100  *     Z
/var/log/amd.log                        644  7     100  *     Z
/var/log/kerberos.log                   644  7     100  *     Z
/var/log/lpd-errs                       644  7     100  *     Z
/var/log/maillog                        644  7     *    @T00  Z
/var/log/sendmail.st                    644  10    *    168   B
/var/log/messages                       644  5     100  *     Z
/var/log/all.log                        600  7     *    @T00  Z
/var/log/slip.log                       600  3     100  *     Z
/var/log/ppp.log                        600  3     100  *     Z
/var/log/security                       600  10    100  *     Z
/var/log/wtmp                           644  3     *    @01T05 B
/var/log/daily.log                      640  7     *    @T00  Z
/var/log/weekly.log                     640  5     1    $W6D0 Z
/var/log/monthly.log                    640  12    *    $M1D0 Z
/var/log/console.log                    640  5     100  *     Z
```

参考 [newsyslog(8)](http://www.FreeBSD.org/cgi/man.cgi?query=newsyslog&sektion=8&manpath=freebsd-release-ports) 手册页以获得更多信息。

### 12.10.4. `sysctl.conf`

`sysctl.conf` 和 `rc.conf` 这两个文件的风格很接近。 其中的配置均为 `变量=值` 这样的形式。 在这个文件中配置的值， 均会在系统进入多用户模式之后进行实际的修改操作。 需要注意的是， 并不是所有的变量都能够在多用户模式下修改。

如果希望关闭对收到致命的信号退出的进程进行记录， 并阻止普通用户看到其他用户的进程， 可以在 `sysctl.conf` 中进行下列配置：

```
# 不记录由于致命信号导致的进程退出 (例如信号 11，访问越界)
kern.logsigexit=0

# 阻止用户看到以其他用户 UID 身份执行的进程。
security.bsd.see_other_uids=0
```

## 12.11. 用 sysctl 进行调整

[sysctl(8)](http://www.FreeBSD.org/cgi/man.cgi?query=sysctl&sektion=8&manpath=freebsd-release-ports) 是一个允许您改变正在运行中的 FreeBSD 系统的接口。它包含一些 TCP/IP 堆栈和虚拟内存系统的高级选项， 这可以让有经验的管理员提高引人注目的系统性能。用 [sysctl(8)](http://www.FreeBSD.org/cgi/man.cgi?query=sysctl&sektion=8&manpath=freebsd-release-ports) 可以读取设置超过五百个系统变量。

基于这点，[sysctl(8)](http://www.FreeBSD.org/cgi/man.cgi?query=sysctl&sektion=8&manpath=freebsd-release-ports) 提供两个功能：读取和修改系统设置。

查看所有可读变量：

```
% `sysctl -a`
```

读一个指定的变量，例如 `kern.maxproc`：

```
% `sysctl kern.maxproc`
kern.maxproc: 1044
```

要设置一个指定的变量，直接用 *`variable`*=*`value`* 这样的语法：

```
# `sysctl kern.maxfiles=5000`
kern.maxfiles: 2088 -> 5000
```

sysctl 变量的设置通常是字符串、数字或者布尔型。 (布尔型用 `1` 来表示'yes'，用 `0` 来表示'no')。

如果你想在每次机器启动时自动设置某些变量， 可将它们加入到文件 `/etc/sysctl.conf` 之中。更多信息，请参阅手册页 [sysctl.conf(5)](http://www.FreeBSD.org/cgi/man.cgi?query=sysctl.conf&sektion=5&manpath=freebsd-release-ports) 及 第 12.10.4 节 “`sysctl.conf`”。

### 12.11.1. 只读的 [sysctl(8)](http://www.FreeBSD.org/cgi/man.cgi?query=sysctl&sektion=8&manpath=freebsd-release-ports)

Contributed by Tom Rhodes.

有时可能会需要修改某些只读的 [sysctl(8)](http://www.FreeBSD.org/cgi/man.cgi?query=sysctl&sektion=8&manpath=freebsd-release-ports) 的值。 尽管有时不得不这样做， 但只有通过(重新)启动才能达到这样的目的。

例如一些膝上型电脑的 [cardbus(4)](http://www.FreeBSD.org/cgi/man.cgi?query=cardbus&sektion=4&manpath=freebsd-release-ports) 设备不会探测内存范围，并且产生看似于这样的错误：

```
cbb0: Could not map register memory
device_probe_and_attach: cbb0 attach returned 12
```

像上面的错误通常需要修改一些只读的 [sysctl(8)](http://www.FreeBSD.org/cgi/man.cgi?query=sysctl&sektion=8&manpath=freebsd-release-ports) 默认设置。要实现这点，用户可以在本地的 `/boot/loader.conf.local` 里面放一个 [sysctl(8)](http://www.FreeBSD.org/cgi/man.cgi?query=sysctl&sektion=8&manpath=freebsd-release-ports) “OIDs”。那些设置定位在 `/boot/defaults/loader.conf` 文件中。

修复上面的问题用户需要在刚才所说的文件中设置 `hw.pci.allow_unsupported_io_range=1`。现在 [cardbus(4)](http://www.FreeBSD.org/cgi/man.cgi?query=cardbus&sektion=4&manpath=freebsd-release-ports) 就会正常的工作了。

## 12.12. 调整磁盘

### 12.12.1. Sysctl 变量

#### 12.12.1.1. `vfs.vmiodirenable`

`vfs.vmiodirenable` sysctl 变量可以设置成 0(关)或者 1(开)；默认是 1。 这个变量控制目录是否被系统缓存。大多数目录是小的， 在系统中只使用单个片断(典型的是 1K)并且在缓存中使用的更小 (典型的是 512 字节)。当这个变量设置为关闭 (`0`) 时， 缓存器仅仅缓存固定数量的目录，即使您有很大的内存。 而将其开启 (设置为 1) 时， 则允许缓存器用 VM 页面缓存来缓存这些目录，让所有可用内存来缓存目录。 不利的是最小的用来缓存目录的核心内存是大于 512 字节的物理页面大小(通常是 4k)。 我们建议如果您在运行任何操作大量文件的程序时保持这个选项打开的默认值。 这些服务包括 web 缓存，大容量邮件系统和新闻系统。 尽管可能会浪费一些内存，但打开这个选项通常不会降低性能。 但还是应该检验一下。

#### 12.12.1.2. `vfs.write_behind`

`vfs.write_behind` sysctl 变量默认是 `1` (打开)。 它告诉文件系统簇被收集满的时候把内容写进介质， 典型的是在写入大的连续的文件时。 主要的想法是， 如果可能对 I/O 性能会产生负面影响时， 应尽量避免让缓冲缓存被未同步缓冲区充满。 然而它可能降低处理速度并且在某些情况下您可能想要关闭它。

#### 12.12.1.3. `vfs.hirunningspace`

`vfs.hirunningspace` sysctl 变量决定了在任何给定情况下， 有多少写 I/O 被排进队列以给系统的磁盘控制器。 默认值一般是足够的，但是对有很多磁盘的机器来说您可能需要把它设置成 4M 或 5M。注意这个设置成很高的值(超过缓存器的写极限)会导致坏的性能。 不要盲目的把它设置太高！高的数值会导致同时发生的读操作的迟延。

sysctl 中还有许多与 buffer cache 和 VM 页面 cache 有关的值， 一般不推荐修改它们。 虚拟内存系统已经能够很好地进行自动调整了。

#### 12.12.1.4. `vm.swap_idle_enabled`

`vm.swap_idle_enabled` sysctl 变量在有很多用户进入、离开系统和有很多空闲进程的大的多用户系统中很有用。 这些系统注重在空闲的内存中间产生连续压力的处理。通过 `vm.swap_idle_threshold1` 和 `vm.swap_idle_threshold2` 打开这个特性并且调整交换滞后 (在空闲时)允许您降低内存页中空闲进程的优先权，从而比正常的出页 (pageout)算法更快。这给出页守护进程带来了帮助。 除非您需要否则不要把这个选项打开，因为您所权衡的是更快地进入内存， 因而它会吃掉更多的交换和磁盘带宽。在小的系统上它会有决定性的效果， 但是在大的系统上它已经做了合适的页面调度这个选项允许 VM 系统容易的让全部的进程进出内存。

#### 12.12.1.5. `hw.ata.wc`

FreeBSD 4.3 中默认将 IDE 的写缓存关掉了。 这会降低到 IDE 磁盘用于写入操作的带宽， 但我们认为这有助于避免硬盘厂商所引入的， 可能引致严重的数据不一致问题。 这类问题实际上是由于 IDE 硬盘就写操作完成这件事的不诚实导致的。 当启用了 IDE 写入缓存时， IDE 硬盘驱动器不但不会按顺序将数据写到盘上， 而且当磁盘承受重载时， 它甚至会自作主张地对推迟某些块的实际写操作。 这样一来， 在系统发生崩溃或掉电时， 就会导致严重的文件系统损坏。 基于这些考虑， 我们将 FreeBSD 的默认配置改成了更为安全的禁用 IDE 写入缓存。 然而不幸的是， 这样做导致了性能的大幅降低， 因此在后来的发行版中这个配置又改为默认启用了。 您可以通过观察 `hw.ata.wc` sysctl 变量， 来确认您的系统中所采用的默认值。 如果 IDE 写缓存被禁用， 您可以通过将内核变量设置为 1 来启用它。 这一操作必须在启动时通过 boot loader 来完成。 在内核启动之后尝试这么做是没有任何作用的。

要了解更多的信息，请查阅 [ata(4)](http://www.FreeBSD.org/cgi/man.cgi?query=ata&sektion=4&manpath=freebsd-release-ports)。

#### 12.12.1.6. `SCSI_DELAY` (`kern.cam.scsi_delay`)

`SCSI_DELAY` 内核配置会缩短系统启动时间。 默认值在系统启动过程中有 `15` 秒的迟延时间， 这是一个足够多且可靠的值。把它减少到 `5` 通常也能工作(特别是现代的驱动器)。 您可以在系统引导时调整引导加载器变量 `kern.cam.scsi_delay` 来改变它。 需要注意的是， 此处使用的单位是 *毫秒* 而 *不* 是 *秒* 。

### 12.12.2. Soft Updates

[tunefs(8)](http://www.FreeBSD.org/cgi/man.cgi?query=tunefs&sektion=8&manpath=freebsd-release-ports) 程序能够用来很好的调整文件系统。 这个程序有很多不同的选项，但是现在只介绍 Soft Updates 的打开和关闭，这样做：

```
# `tunefs -n enable /filesystem`
# `tunefs -n disable /filesystem`
```

在文件系统被挂载之后不能用 [tunefs(8)](http://www.FreeBSD.org/cgi/man.cgi?query=tunefs&sektion=8&manpath=freebsd-release-ports) 来修改。打开 Soft Updates 的最佳时机是在单用户模式下任何分区被挂载前。

Soft Updates 极大地改善了元数据修改的性能， 主要是文件创建和删除，通过内存缓存。我们建议您在所有的文件系统上使用 Soft Updates。应该知道 Soft Updates 的两点：首先， Soft Updates 保证了崩溃后的文件系统完整性，但是很可能有几秒钟 (甚至一分钟！) 之前的数据没有写到物理磁盘。如果您的系统崩溃了您可能会丢失很多工作。 第二，SoftUpdates 推迟文件系统块的释放时间。如果在文件系统 (例如根文件系统)快满了的情况下对系统进行大规模的升级比如 `make installworld`， 可能会引起磁盘空间不足从而造成升级失败。

#### 12.12.2.1. Soft Updates 的详细资料

有两种传统的方法来把文件系统的元数据 (meta-data) 写入磁盘。 (Meta-data 更新是更新类似 inodes 或者目录这些没有内容的数据)

从前，默认方法是同步更新这些元数据(meta-data)。 如果一个目录改变了，系统在真正写到磁盘之前一直等待。 文件数据缓存(文件内容)在这之后以非同步形式写入。 这么做有利的一点是操作安全。如果更新时发生错误，元数据(meta-data) 一直处于完整状态。文件要不就被完整的创建要不根本就不创建。 如果崩溃时找不到文件的数据块，[fsck(8)](http://www.FreeBSD.org/cgi/man.cgi?query=fsck&sektion=8&manpath=freebsd-release-ports) 可以找到并且依靠把文件大小设置为 0 来修复文件系统。 另外，这么做既清楚又简单。缺点是元数据(meta-data)更新很慢。例如 `rm -r` 命令，依次触及目录下的所有文件， 但是每个目录的改变(删除一个文件)都要同步写入磁盘。 这包含它自己更新目录，inode 表和可能对文件分散的块的更新。 同样问题出现大的文件操作上(比如 `tar -x`)。

第二种方法是非同步元数据更新。这是 Linux/ext2fs 和 *BSD ufs 的 `mount -o async` 默认的方法。所有元数据更新也是通过缓存。 也就是它们会混合在文件内容数据更新中。 这个方法的优点是不需要等待每个元数据更新都写到磁盘上， 所以所有引起元数据更新大的操作比同步方式更快。同样， 这个方法也是清楚且简单的，所以代码中的漏洞风险很小。 缺点是不能保证文件系统的状态一致性。如果更新大量元数据时失败 (例如掉电或者按了重启按钮)，文件系统会处在不可预知的状态。 系统再启动时没有机会检查文件系统的状态；inode 表更新的时候可能文件的数据块已经写入磁盘了但是相关联的目录没有，却不能用 `fsck` 命令来清理(因为磁盘上没有所需要的信息)。 如果文件系统修复后损坏了，唯一的选择是使用 [newfs(8)](http://www.FreeBSD.org/cgi/man.cgi?query=newfs&sektion=8&manpath=freebsd-release-ports) 并且从备份中恢复它。

这个问题通常的解决办法是使用 *dirty region logging* 或者 *journaling* 尽管它不是一贯的被使用并且有时候应用到其他的事务纪录中更好。 这种方法元数据更新依然同步写入，但是只写到磁盘的一个小区域。 过后他们将会被移动到正确的位置。因为纪录区很小， 磁盘上接近的区域磁头不需要移动很长的距离，所以这些比写同步快一些。 另外这个方法的复杂性有限，所以出现错误的机会也很少。缺点是元数据要写两次 (一次写到纪录区域，一次写到正确的区域)。正常情况下， 悲观的性能可能会发生。从另一方面来讲， 崩溃的时候所有未发生的元数据操作可以很快的在系统启动之后从记录中恢复过来。

Kirk McKusick，伯克利 FFS 的开发者，用 Soft Updates 解决了这个问题：元数据更新保存在内存中并且按照排列的顺序写入到磁盘 (“有序的元数据更新”)。这样的结果是，在繁重的元数据操作中， 如果先前的更新还在内存中没有被写进磁盘，后来的更新就会捕捉到。 所以所有的目录操作在写进磁盘的时候首先在内存中执行 (数据块按照它们的位置来排列，所以它们不会在元数据前被写入)。 如果系统崩溃了这将导致一个固定的 “日志回朔”： 所有不知如何写入磁盘的操作都像没有发生过一样。文件系统的一致性保持在 30 到 60 秒之前。它保证了所有正在使用的资源被标记例如块和 inodes。崩溃之后， 唯一的资源分配错误是一个实际是“空闲”的资源的资源被标记为“使用”。 [fsck(8)](http://www.FreeBSD.org/cgi/man.cgi?query=fsck&sektion=8&manpath=freebsd-release-ports) 可以认出这种情况并且释放不再使用的资源。它对于忽略崩溃后用 `mount -f` 强制挂上的文件系统的错误状态是安全的。 为了释放可能没有使用的资源，[fsck(8)](http://www.FreeBSD.org/cgi/man.cgi?query=fsck&sektion=8&manpath=freebsd-release-ports) 需要在过后的时间运行。一个主意是用 *后台 fsck*：系统启动的时候只有一个文件系统的 *快照* 被记录下来。`fsck` 可以在过后运行。所有文件系统可以在“有错误”的时候被挂接， 所以系统可以在多用户模式下启动。接着，后台 `fsck` 可以在所有文件系统需要的时候启动来释放可能没有使用的资源。 (尽管这样，不用 Soft Updates 的文件系统依然需要通常的 `fsck`。)

它的优点是元数据操作几乎跟非同步一样快 (也就是比需要两次元数据写操作的 *logging* 更快)。缺点是代码的复杂性(意味着对于丢失用户敏感数据有更多的风险) 和高的内存使用量。另外它有些特点需要知道。崩溃之后， 文件系统状态会“落后”一些。同步的方法用 `fsck` 后在一些地方可能产生一些零字节的文件， 这些文件在用 Soft Updates 文件系统之后不会存在， 因为元数据和文件内容根本没有写进磁盘(可能发生在运行 `rm` 之后)。这可能在文件系统上安装大量数据时候引发问题， 没有足够的剩余空间来两次存储所有文件。

## 12.13. 调整内核限制

### 12.13.1. 文件/进程限制

#### 12.13.1.1. `kern.maxfiles`

`kern.maxfiles` 可以根据系统的需要适当增减。 这个变量用于指定在系统中允许的文件描述符的最大数量。 当文件描述符表满的时候， file: table is full 会在系统消息缓冲区中反复出现， 您可以使用 `dmesg` 命令来观察这一现象。

每个打开的文件、 套接字和管道， 都会占用一个文件描述符。 在大型生产服务器上， 可能会轻易地用掉数千个文件描述符， 具体用量取决于服务的类型和并行启动的服务数量。

在早期版本的 FreeBSD 中， `kern.maxfiles` 的默认值， 是根据您内核配置文件中的 `maxusers` 选项计算的。 `kern.maxfiles` 这个数值， 会随 `maxusers` 成比例地增减。 当编译定制的内核时， 按照您系统的用途来修改这个值是个好主意。 这个数字同时还决定内核的许多预设的限制值。 有时， 尽管并不会真的有 256 个用户同时连接一台生产服务器， 但对于高负载的 web 服务器而言， 却可能需要与之类似的资源。

变量 `kern.maxusers` 会在系统启动时， 根据可用内存的尺寸进行计算， 在内核开始运行之后， 可以通过只读的 `kern.maxusers` sysctl 变量值来进行观察。 有些情况下， 可能会希望使用更大或更小一些的 `kern.maxusers`， 它可以以加载器变量的形式进行配置； 类似 64、 128 和 256 这样的值都并不罕见。 我们不推荐使用超过 256 的值， 除非您需要巨量的文件描述符； 根据 `kern.maxusers` 推算默认值的那些变量， 一般都可以在引导甚至运行时通过 `/boot/loader.conf` (请参见 [loader.conf(5)](http://www.FreeBSD.org/cgi/man.cgi?query=loader.conf&sektion=5&manpath=freebsd-release-ports) 联机手册或 `/boot/defaults/loader.conf` 文件来获得相关的指导) 或这篇文档的其余部分所介绍的方式来调整。

在较早的版本中， 如果您明确地将 `maxusers` 设置为 `0`， 则系统会自动地根据硬件配置来确定这个值。[^([5])](#ftn.idp77311856)。 在 FreeBSD 5.X 和更高版本中， `maxusers` 如果不指定的话， 就会取默认值 `0`。 如果希望自行管理 `maxusers`， 则应配置一个不低于 4 的值， 特别是使用 X Window System 或编译软件的时候。 这样做的原因是， `maxusers` 所决定的一个最为重要的表的尺寸会影响最大进程数， 这个数值将是 `20 + 16 * maxusers`。 因此如果将 `maxusers` 设置为 1， 您就只能同时运行 36 个进程， 这还包括了 18 个左右的系统引导时启动的进程， 以及 15 个左右的， 在您启动 X Window System 时所引发的进程。 即使是简单的任务， 如阅读联机手册， 也需要启动多至九个的进程， 用以过滤、 解压缩， 并显示它。 将 `maxusers` 设为 64 将允许您同时执行最多 1044 个进程， 这几乎足以满足任何需要了。 不过， 如果您看在启动其它程序， 或运行用以支持大量用户的服务 (例如 `ftp.FreeBSD.org`) 时， 看到令人担忧的 proc table full 错误， 就应该提高这一数值， 并重新联编内核。

### 注意:

`maxusers` 并 *不能* 限制实际能够登录到您系统上来的用户的数量。 它的主要作用是根据您可能支持的用户数量来为一系列系统数据表设置合理的尺寸， 以便提供支持他们所需运行的进程资源。

#### 12.13.1.2. `kern.ipc.somaxconn`

`kern.ipc.somaxconn` sysctl 变量 限制了接收新 TCP 连接侦听队列的大小。对于一个经常处理新连接的高负载 web 服务环境来说，默认的 `128` 太小了。 大多数环境这个值建议增加到 `1024` 或者更多。 服务进程会自己限制侦听队列的大小(例如 [sendmail(8)](http://www.FreeBSD.org/cgi/man.cgi?query=sendmail&sektion=8&manpath=freebsd-release-ports) 或者 Apache)， 常常在它们的配置文件中有设置队列大小的选项。 大的侦听队列对防止拒绝服务 DoS 攻击也会有所帮助。

### 12.13.2. 网络限制

`NMBCLUSTERS` 内核配置选项指出了系统可用的网络 Mbuf 的数量。 一个高流量的服务器使用一个小数目的网络缓存会影响 FreeBSD 的性能。 每个 cluster 可能需要 2K 内存，所以一个 1024 的值需要在内核中给网络缓存保留 2M 内存。 可以用简单的方法计算出来需要多少网络缓存。 如果您有一个同时发生 1000 个以上连接的 web 服务器， 并且每个连接用掉 16K 接收和发送缓存， 就需要大概 32M 网络缓存来确保 web 服务器的工作。 一个好的简单计算方法是乘以 2，所以 2x32Mb/2Kb=64MB/2kb=32768。 我们建议在有大量内存的机器上把这个值设置在 4096 到 32768 之间。 没有必要把它设置成任意太高的值，它会在启动时引起崩溃。 [netstat(1)](http://www.FreeBSD.org/cgi/man.cgi?query=netstat&sektion=1&manpath=freebsd-release-ports) 的 `-m` 选项可以用来观察网络 cluster 使用情况。

`kern.ipc.nmbclusters` 可以用来在启动时刻调节这个。 仅仅在旧版本的 FreeBSD 需要使用 `NMBCLUSTERS` [config(8)](http://www.FreeBSD.org/cgi/man.cgi?query=config&sektion=8&manpath=freebsd-release-ports) 选项。

经常使用 [sendfile(2)](http://www.FreeBSD.org/cgi/man.cgi?query=sendfile&sektion=2&manpath=freebsd-release-ports) 系统调用的繁忙的服务器， 有必要通过 `NSFBUFS` 内核选项或者在 `/boot/loader.conf` (查看 [loader(8)](http://www.FreeBSD.org/cgi/man.cgi?query=loader&sektion=8&manpath=freebsd-release-ports) 以获得更多细节) 中设置它的值来调节 [sendfile(2)](http://www.FreeBSD.org/cgi/man.cgi?query=sendfile&sektion=2&manpath=freebsd-release-ports) 缓存数量。 这个参数需要调节的普通原因是在进程中看到 `sfbufa` 状态。sysctl `kern.ipc.nsfbufs` 变量在内核配置变量中是只读的。 这个参数是由 `kern.maxusers` 决定的，然而它可能有必要因此而调整。

### 重要:

即使一个套接字被标记成非阻塞，在这个非阻塞的套接字上呼叫 [sendfile(2)](http://www.FreeBSD.org/cgi/man.cgi?query=sendfile&sektion=2&manpath=freebsd-release-ports) 可能导致 [sendfile(2)](http://www.FreeBSD.org/cgi/man.cgi?query=sendfile&sektion=2&manpath=freebsd-release-ports) 呼叫阻塞直到有足够的 `struct sf_buf` 可用。

#### 12.13.2.1. `net.inet.ip.portrange.*`

`net.inet.ip.portrange.*` sysctl 变量自动的控制绑定在 TCP 和 UDP 套接字上的端口范围。 这里有三个范围：一个低端范围，一个默认范围和一个高端范围。 大多数网络程序分别使用由 `net.inet.ip.portrange.first` 和 `net.inet.ip.portrange.last` 控制的从 1024 到 5000 的默认范围。端口范围用作对外连接，并且某些情况可能用完系统的端口， 这经常发生在运行一个高负荷 web 代理服务器的时候。 这个端口范围不是用来限制主要的例如 web 服务器进入连接或者有固定端口例如邮件传递对外连接的。 有时您可能用完了端口，那就建议适当的增加 `net.inet.ip.portrange.last`。 `10000`、`20000` 或者 `30000` 可能是适当的值。 更改端口范围的时候也要考虑到防火墙。 一些防火墙会阻止端口的大部分范围 (通常是低范围的端口)并且用高端口进行对外连接(──)。 基于这个问题建议不要把 `net.inet.ip.portrange.first` 设的太小。

#### 12.13.2.2. TCP 带宽迟延(Bandwidth Delay Product)

限制 TCP 带宽延迟积和 NetBSD 的 TCP/Vegas 类似。 它可以通过将 sysctl 变量 `net.inet.tcp.inflight.enable` 设置成 `1` 来启用。 系统将尝试计算每一个连接的带宽延迟积， 并将排队的数据量限制在恰好能保持最优吞吐量的水平上。

这一特性在您的服务器同时向使用普通调制解调器， 千兆以太网， 乃至更高速度的光与网络连接 (或其他带宽延迟积很大的连接) 的时候尤为重要， 特别是当您同时使用滑动窗缩放， 或使用了大的发送窗口的时候。 如果启用了这个选项， 您还应该把 `net.inet.tcp.inflight.debug` 设置为 `0` (禁用调试)， 对于生产环境而言， 将 `net.inet.tcp.inflight.min` 设置成至少 `6144` 会很有好处。 然而， 需要注意的是， 这个值设置过大事实上相当于禁用了连接带宽延迟积限制功能。 这个限制特性减少了在路由和交换包队列的堵塞数据数量， 也减少了在本地主机接口队列阻塞的数据的数量。在少数的等候队列中、 交互式连接，尤其是通过慢速的调制解调器，也能用低的 *往返时间*操作。但是，注意这只影响到数据发送 (上载/服务端)。对数据接收(下载)没有效果。

调整 `net.inet.tcp.inflight.stab` 是 *不* 推荐的。 这个参数的默认值是 20， 表示把 2 个最大包加入到带宽延迟积窗口的计算中。 额外的窗口似的算法更为稳定， 并改善对于多变网络环境的相应能力， 但也会导致慢速连接下的 ping 时间增长 (尽管还是会比没有使用 inflight 算法低许多)。 对于这些情形， 您可能会希望把这个参数减少到 15， 10， 或 5； 并可能因此而不得不减少 `net.inet.tcp.inflight.min` (比如说， 3500) 来得到希望的效果。 减少这些参数的值， 只应作为最后不得已时的手段来使用。

### 12.13.3. 虚拟内存

#### 12.13.3.1. `kern.maxvnodes`

vnode 是对文件或目录的一种内部表达。 因此， 增加可以被操作系统利用的 vnode 数量将降低磁盘的 I/O。 一般而言， 这是由操作系统自行完成的， 也不需要加以修改。 但在某些时候磁盘 I/O 会成为瓶颈， 而系统的 vnode 不足， 则这一配置应被增加。 此时需要考虑是非活跃和空闲内存的数量。

要查看当前在用的 vnode 数量：

```
# `sysctl vfs.numvnodes`
vfs.numvnodes: 91349
```

要查看最大可用的 vnode 数量：

```
# `sysctl kern.maxvnodes`
kern.maxvnodes: 100000
```

如果当前的 vnode 用量接近最大值， 则将 `kern.maxvnodes` 值增大 1,000 可能是个好主意。 您应继续查看 `vfs.numvnodes` 的数值， 如果它再次攀升到接近最大值的程度， 仍需继续提高 `kern.maxvnodes`。 在 [top(1)](http://www.FreeBSD.org/cgi/man.cgi?query=top&sektion=1&manpath=freebsd-release-ports) 中显示的内存用量应有显著变化， 更多内存会处于活跃 (active) 状态。

* * *

[^([5])](#idp77311856) 自动调整算法会将 `maxusers` 设置为与主存的数量一样， 或者取其下限 32 或上限 384。

## 12.14. 添加交换空间

不管您计划得如何好，有时候系统并不像您所期待的那样运行。 如果您发现需要更多的交换空间，添加它很简单。 有三种方法增加交换空间：添加一块新的硬盘驱动器、通过 NFS 使用交换空间和在一个现有的分区上创建一个交换文件。

要了解关于如何加密交换区， 相关配置， 以及为什么要这样做， 请参阅手册的 第 19.17 节 “对交换区进行加密”。

### 12.14.1. 在新的硬盘驱动器上使用交换空间

这是添加交换空间最好的方法， 当然为了达到这个目的需要添加一块硬盘。 毕竟您总是可以使用另一块磁盘。如果能这么做， 重新阅读一下手册中关于交换空间的 第 12.2 节 “初步配置” 来了解如何最优地安排交换空间。

### 12.14.2. 通过 NFS 交换

除非没有可以用作交换空间的本地硬盘时， 否则不推荐您使用 NFS 来作为交换空间使用。 NFS 交换会受到可用网络带宽限制并且增加 NFS 服务器的负担。

### 12.14.3. 交换文件

您可以创建一个指定大小的文件用来当作交换文件。 在我们的例子中我们将会使用叫做 `/usr/swap0` 的 64MB 大小的文件。当然您也可以使用任何您所希望的名字。

例 12.1. 在 FreeBSD 中创建交换文件

1.  确认您的内核配置包含虚拟磁盘(Memory disk)驱动 ([md(4)](http://www.FreeBSD.org/cgi/man.cgi?query=md&sektion=4&manpath=freebsd-release-ports))。它在 `GENERIC` 内核中是默认的。

    ```
    device   md   # Memory "disks"
    ```

2.  创建一个交换文件(`/usr/swap0`)：

    ```
    # `dd if=/dev/zero of=/usr/swap0 bs=1024k count=64`
    ```

3.  赋予它(`/usr/swap0`)一个适当的权限：

    ```
    # `chmod 0600 /usr/swap0`
    ```

4.  在 `/etc/rc.conf` 中启用交换文件：

    ```
    swapfile="/usr/swap0"   # Set to name of swapfile if aux swapfile desired.
    ```

5.  通过重新启动机器或下面的命令使交换文件立刻生效：

    ```
    # `mdconfig -a -t vnode -f /usr/swap0 -u 0 && swapon /dev/md0`
    ```

## 12.15. 电源和资源管理

Written by Hiten Pandya 和 Tom Rhodes.

BIOS 接口管理，例如*可插拔 BIOS (PNPBIOS)*或者*高级电源管理(APM)* 等等。电源和资源管理是现代操作系统的关键组成部分。 例如您可能当系统温度过高的时候让您的操作系统能监视到 (并且可能提醒您)。

以有效的方式利用硬件资源是非常重要的。 在引入 ACPI 之前， 管理电源使用和系统散热对操作系统是很困难的。 硬件由 BIOS 进行管理， 因而用户对电源管理配置的控制和查看都比较困难。 一些系统通过 *高级电源管理 (APM)* 提供了有限的配置能力。 电源和资源管理是现代操作系统的一个关键组件。 例如， 您可能希望操作系统监视系统的一些限制， 例如系统的温度是否超出了预期的增长速度 (并在需要时发出警告)。

在 FreeBSD 使用手册的这一章节，我们将提供 ACPI 全面的信息。 参考资料会在末尾给出。

### 12.15.1. 什么是 ACPI？

高级配置和电源接口 (ACPI) 是一个业界标准的硬件资源和电源管理接口 (因此而得名) 。它是 *操作系统控制的配置和电源管理(Operating System-directed configuration and Power Management)*，也就是说， 它给操作系统(OS)提供了更多的控制和弹性。 在引入 ACPI 之前， 现代操作系统使得目前即插即用接口的局限性更加 “凸现” 出来。 ACPI 是 APM(高级电源管理) 的直接继承者。

### 12.15.2. 高级电源管理 (APM) 的缺点

*高级电源管理 (APM)* 是一种基于系统目前的活动控制其电源使用的机制。 APM BIOS 由 (系统的) 制造商提供， 并且是硬件平台专属的。 在 OS 中的 APM 驱动作为中介来访问 *APM 软件接口*， 从而实现对电源使用的管理。 在 2000 年或更早的时期生产的计算机系统， 仍需要使用 APM。

APM 有四个主要的问题。 首先， 电源管理是通过 (制造商专属的) BIOS 实现的， 而 OS 则完全不了解其细节。 例如， 用户在 APM BIOS 中设置了硬盘驱动器的空闲等待数值， 当超过这一空闲时间的限制时， 它 (BIOS) 将会减慢硬盘驱动器的速度， 而不会征求 OS 的同意。 第二， APM 逻辑是嵌入 BIOS 的， 因此它是在 OS 的控制之外运转的。 这意味着用户只能通过通过刷新他们 ROM 中的 APM BIOS 才能够解决某些问题； 而这是一个很危险的操作， 因为它可能使系统进入一个无法恢复的状态。 第三， APM 是一种制造商专属的技术， 也就是说有很多第三方的 (重复的工作) 以及 bugs， 如果在一个制造商的 BIOS 中有， 也未必会在其他的产品中解决。 最后但绝不是最小的问题， APM BIOS 没有为实现复杂的电源策略提供足够的余地， 也无法实现能够非常适合具体机器的策略。

*即插即用 BIOS (PNPBIOS)* 在很多时候都是不可靠的。 PNPBIOS 是 16-位 的技术， 因此 OS 不得不使用 16-位 模拟才能够与 PNPBIOS 的方法 “接口”。

FreeBSD APM 驱动在 [apm(4)](http://www.FreeBSD.org/cgi/man.cgi?query=apm&sektion=4&manpath=freebsd-release-ports) 手册页中有描述。

### 12.15.3. 配置 ACPI

默认情况下， `acpi.ko` 驱动， 会在系统引导时由 [loader(8)](http://www.FreeBSD.org/cgi/man.cgi?query=loader&sektion=8&manpath=freebsd-release-ports) 加载， 而 *不应* 直接联编进内核。 这样做的原因是模块操作起来更方便， 例如， 无需重新联编内核就可以切换到另一个 `acpi.ko` 版本。 这样可以让测试变得更简单一些。 另一个原因是， 许多时候在启动已经启动之后再启动 ACPI 可能会有些问题。 如果您遇到了问题， 可以全面禁用 ACPI。 这个驱动不应， 目前也无法卸载， 因为系统总线通过它与许多不同的硬件进行交互。 ACPI 可以通过在 `/boot/loader.conf` 中配置或在 [loader(8)](http://www.FreeBSD.org/cgi/man.cgi?query=loader&sektion=8&manpath=freebsd-release-ports) 提示符处配置 `hint.acpi.0.disabled="1"` 来禁用。

### 注意:

ACPI 和 APM 不能共存， 相反， 它们应分开使用。 后加载的驱动如果发现系统中已经执行了其中的一个， 便会停止执行。

ACPI 可以用来让系统进入休眠模式， 方法是使用 [acpiconf(8)](http://www.FreeBSD.org/cgi/man.cgi?query=acpiconf&sektion=8&manpath=freebsd-release-ports) 的 `-s` 参数， 加上一个 `1-5` 的数字。 多数用户会希望使用 `1` 或 `3` (挂起到 RAM)。 而 `5` 则会让系统执行与下列命令效果类似的软关机：

```
# `halt -p`
```

除此之外， 还有一些通过 [sysctl(8)](http://www.FreeBSD.org/cgi/man.cgi?query=sysctl&sektion=8&manpath=freebsd-release-ports) 提供的选项。 请参见联机手册 [acpi(4)](http://www.FreeBSD.org/cgi/man.cgi?query=acpi&sektion=4&manpath=freebsd-release-ports) 和 [acpiconf(8)](http://www.FreeBSD.org/cgi/man.cgi?query=acpiconf&sektion=8&manpath=freebsd-release-ports) 以获得更多信息。

## 12.16. 使用和调试 FreeBSD ACPI

撰写人：Nate Lawson.协力：Peter Schultz 和 Tom Rhodes.

ACPI 是一种全新的发现设备、 管理电源使用、 以及提供过去由 BIOS 管理的访问不同硬件的标准化方法。 让 ACPI 在各种系统上都能正确使用的工作一直在进行， 但许多主板的 *ACPI 机器语言* (AML) 字节代码中的 bug， FreeBSD 的内核中子系统设计的不完善， 以及 Intel® ACPI-CA 解释器中的 bug 仍然不时会出现。

这份文档期望能够帮助您协助 FreeBSD ACPI 的维护人员来找到您所观察到的问题的根源， 并通过调试找到其解决方法。 感谢您阅读这份文档， 我们也希望能够解决您的系统上的问题。

### 12.16.1. 提交调试信息

### 注意:

在提交问题之前， 请确认您已经在运行最新的 BIOS 版本， 此外， 也包括嵌入式控制器的固件版本。

如果您希望提交一个问题， 请确保将下述信息发到 freebsd-acpi@FreeBSD.org:

*   问题行为的描述， 包括系统类型、型号，以及任何触发问题的相关信息。 另外， 请注意尽可能准确地描述这一问题是否对您是陌生的。

*   在 “boot -v” 之后得到的 [dmesg(8)](http://www.FreeBSD.org/cgi/man.cgi?query=dmesg&sektion=8&manpath=freebsd-release-ports) 输出， 以及任何在重现 bug 时出现的错误信息。

*   在禁用了 ACPI 之后的 “boot -v” 的 [dmesg(8)](http://www.FreeBSD.org/cgi/man.cgi?query=dmesg&sektion=8&manpath=freebsd-release-ports) 输出， 如果您发现禁用 ACPI 能够帮助消除问题。

*   来自 `sysctl hw.acpi`的输出。 这也是找到您的系统所提供的功能的一种好办法。

*   能够得到您的 *ACPI Source Language* (ASL) 的 URL。 *不要* 把 ASL 直接发到邮件列表中， 因为它们可能非常大。 为了得到 ASL 您可以运行这个命令：

    ```
    # `acpidump -dt > name-system.asl`
    ```

    (把 *`name`* 改为您的登录名， 并把 *`system`* 改为您的硬件制造商及其型号。 例如： `njl-FooCo6000.asl`)

许多开发者也会订阅 [FreeBSD-CURRENT 邮件列表](http://lists.FreeBSD.org/mailman/listinfo/freebsd-current) 但还是请发到 [freebsd-acpi](http://lists.FreeBSD.org/mailman/listinfo/freebsd-acpi) 这样它会被更多人看到。 请耐心等待， 因为我们都有全职的其他工作。 如果您的 bug 不是显而易见的， 我们可能会要求您通过 [send-pr(1)](http://www.FreeBSD.org/cgi/man.cgi?query=send-pr&sektion=1&manpath=freebsd-release-ports) 来提交一个 PR。 在输入 PR 时，请将同样的信息包含进去。 这将帮助我们来追踪和解决问题。 不要在给 [freebsd-acpi](http://lists.FreeBSD.org/mailman/listinfo/freebsd-acpi) 写信之前发送 PR 因为我们把它当作已知文体的备忘录而不是报告机制。 您的问题很可能已经被其他人报告过了。

### 12.16.2. 背景

ACPI 存在于采用 ia32 (x86)、 ia64 (安腾)、 以及 amd64 (AMD) 架构的所有现代计算机上。 完整的标准具有大量的各式功能， 包括 CPU 性能管理、 电源控制、 温度监控、 电池系统、 嵌入式控制器以及总线枚举。 绝大多数系统实现比完整标准的功能要少一些。 例如， 桌面系统通常只实现总线枚举部分， 而笔记本则通常支持降温和电源管理功能。 笔记本通常还提供休眠和唤醒支持， 并提供与此适应的复杂功能。

符合 ACPI 的系统中有许多组件。 BIOS 和芯片组制造商提供一些固定的表 (例如， FADT) 在存储器中， 以提供类似 APIC 映射 (用于 SMP)、 配置寄存器、 以及简单的配置值等等。 另外， 一个字节代码 (bytecode) 表 (*系统区别描述表* DSDT) 则提供了通过树状命名空间来指定设备及其功能的方法。

ACPI 驱动必须要处理固定表， 实现字节码解释器， 并修改驱动程序和内核， 以接受来自 ACPI 子系统的信息。 对于 FreeBSD， Intel® 提供了一个解释器 (ACPI-CA)， 它在 Linux 和 NetBSD 也可以使用。 ACPI-CA 源代码可以在 `src/sys/contrib/dev/acpica` 找到。 用于在 FreeBSD 中允许 ACPI-CA 正确运转的代码则在 `src/sys/dev/acpica/Osd`。 最后， 用于实现 ACPI 设备的驱动可以在 `src/sys/dev/acpica` 找到。

### 12.16.3. 常见问题

要让 ACPI 正常工作， 它的每一部分都必须工作正常。 下面是一些常见的问题， 按照出新的频繁程度排序， 并给出了一些绕过或修正它们的方法。

#### 12.16.3.1. 鼠标问题

某些时候， 唤醒操作会导致鼠标不再正常工作。 已知的绕过这一问题的方法， 是在 `/boot/loader.conf` 文件中添加 `hint.psm.0.flags="0x3000"` 设置。 如果这样做不能解决问题， 请考虑按前面介绍的方法提交问题报告。

#### 12.16.3.2. 休眠/唤醒

ACPI 提供了三种休眠到 RAM (STR) 的状态， `S1`-`S3`， 以及一个休眠到磁盘的状态 (`STD`)， 称作 `S4`。 `S5` 是 “软关机” 同时也是系统接好电源但没有开机时的正常状态。 `S4` 实际上可以用两种不同的方法来实现。 `S4`BIOS 是一种由 BIOS 辅助的挂起到磁盘方法， 而 `S4`OS 则是完全由操作系统实现的。

可以使用 `sysctl hw.acpi` 来查看与休眠有关的项目。 这里是我的 Thinkpad 上得到的结果。

```
hw.acpi.supported_sleep_state: S3 S4 S5
hw.acpi.s4bios: 0
```

这表示我可以使用 `acpiconf -s` 来测试 `S3`， `S4`OS， 以及 `S5`。 如果 `s4bios` 是一 (`1`)， 则可以使用 `S4`BIOS 来代替 `S4` OS。

当测试休眠/唤醒时， 从 `S1` 开始， 如果它被支持的话。 这个状态是最可能正常工作的状态， 因为它不需要太多的驱动支持。 没有人实现 `S2` 但如果您有它的支持， 则应该和 `S1` 类似。 下一件值得尝试的是 `S3`。 这是最深的 STR 状态， 并需要一系列驱动的支持才能够正常地重新初始化您的硬件。 如果您在唤醒系统时遇到问题， 请不要吝惜发邮件给 [freebsd-acpi](http://lists.FreeBSD.org/mailman/listinfo/freebsd-acpi) 邮件列表， 尽管不要指望问题一定会很快解决， 因为有许多驱动程序/硬件需要进行更多的测试和改进。

休眠和唤醒操作最常见的问题是某些设备驱动程序不会保存、 恢复或正确地重新初始化其固件、 寄存器或设备内存。 尝试调试这些问题时， 首先可以尝试：

```
# `sysctl debug.bootverbose=1`
# `sysctl debug.acpi.suspend_bounce=1`
# `acpiconf -s 3`
```

这个测试会模拟休眠和恢复过程而不真的进入 `S3` 状态。 有时， 您会用这种方式很容易地抓住问题 (例如， 丢失固件状态、 设备 watchdog 超时， 以及一直重试等)。 注意系统不会真的进入 `S3` 状态， 这意味着这些设备可能不会掉电， 而许多设备在完全不提供休眠和恢复方法时仍可正常工作， 而不像使用真的 `S3` 状态那样。

较难的情况则需要更多的硬件， 例如用于串口控制台的串口/线， 以及用于 [dcons(4)](http://www.FreeBSD.org/cgi/man.cgi?query=dcons&sektion=4&manpath=freebsd-release-ports) 的火线口/线和内核调试技能。

为了帮助隔离问题， 请在内核中删去尽可能多的驱动。 如果这样做能够解决问题， 请尝试逐个加载驱动直到问题再次出现。 通常预编译的驱动程序如 `nvidia.ko`、 X11 显示驱动， 以及 USB 的问题最多， 而以太网卡的驱动则通常工作的很好。 如果您能够通过加载和卸载驱动使系统正常工作， 您可以通过将适当的命令放到 `/etc/rc.suspend` 和 `/etc/rc.resume` 来将这个过程自动化。 在这两个文件中有一个注释掉的卸载和加载驱动程序的例子供您参考。 另外您还可以将 `hw.acpi.reset_video` 设置为零 (`0`)， 如果您的显示在唤醒之后显得很混乱。 此外您还可以尝试更长或更短的 `hw.acpi.sleep_delay` 值看看是否有所助益。

另一件值得一试的事情是使用一个比较新的包含 ACPI 支持的 Linux 发行版来试试看他们的 休眠/唤醒 功能是否在同样的硬件上能够正常工作。 如果在 Linux 下正常， 则很可能是 FreeBSD 驱动程序的问题， 而隔离问题并找到存在问题的驱动有助于解决它。 需要注意的是 ACPI 的维护人员通常并不维护其他驱动 (例如 声音、 ATA， 等等) 因此如果最终发现是驱动的问题最好还是发到 [freebsd-current](http://lists.FreeBSD.org/mailman/listinfo/freebsd-current) 邮件列表并发给驱动程序的维护者。 如果您喜欢冒险， 则可以加一些 [printf(3)](http://www.FreeBSD.org/cgi/man.cgi?query=printf&sektion=3&manpath=freebsd-release-ports) 到有问题的驱动中， 以找到它的恢复功能发生问题的位置。

最后， 试试看禁用 ACPI 并代之以启用 APM。 如果 休眠/唤醒 能够在 APM 下正常工作， 使用 APM 可能会更好， 特别是对于较老的硬件 (2000 年以前)。 硬件制造商需要一些时间来让老硬件的 ACPI 工作正常， 而 ACPI 的问题十之八九是 BIOS 中的毛病引发的。

#### 12.16.3.3. 系统停止响应 (暂时或永久性地)

绝大多数系统停止响应是由于未能及时响应中断或发生了中断风暴导致的。 芯片组有很多问题最终会溯源到 BIOS 如何在引导系统之前配置中断， APIC (MADT) 表的正确性， 以及 *系统控制中断* (SCI) 如何路由。

通过察看 `vmstat -i` 的输出中包括 `acpi0` 的那一行可以区分中断风暴和未能及时响应中断。 如果每秒计数器增长的速度多于一两个， 则您是遇到了中断风暴。 如果系统停止了响应， 您可以尝试停止内核并进入 DDB (在控制台上按 **CTRL**+**ALT**+**ESC**) 并输入 `show interrupts`。

处理中断问题的救命稻草是尝试禁用 APIC 支持， 这是通过在 `loader.conf` 中加入 `hint.apic.0.disabled="1"` 完成的。

#### 12.16.3.4. 崩溃

崩溃对于 ACPI 是比较罕见的情况， 如果发现， 我们将会非常重视并很快修复它。 您要做的第一件事是设法隔离出能够重现崩溃 (如果可能的话) 的操作并获取一份调用堆栈。 请启用 `options DDB` 并设置串行控制台 (参见 第 27.6.5.3 节 “通过串口线进入 DDB 调试器”) 或配置一个 [dump(8)](http://www.FreeBSD.org/cgi/man.cgi?query=dump&sektion=8&manpath=freebsd-release-ports) 分区。 您将在 DDB 中通过 `tr` 得到调用堆栈。 如果您只能用手抄的方法记录它， 一定要记下头五 (5) 行和最后五 (5) 行。

然后， 尝试通过在启动时禁用 ACPI 来隔离故障。 如果这样做能够正常工作， 请通过设置 `debug.acpi.disable` 的那组数值来隔离具体是哪个 ACPI 子系统的问题。 请参见 [acpi(4)](http://www.FreeBSD.org/cgi/man.cgi?query=acpi&sektion=4&manpath=freebsd-release-ports) 联机手册中给出的那些例子。

#### 12.16.3.5. 系统在休眠或关机之后又启动了

首先请尝试在 [loader.conf(5)](http://www.FreeBSD.org/cgi/man.cgi?query=loader.conf&sektion=5&manpath=freebsd-release-ports) 中设置 `hw.acpi.disable_on_poweroff=`“0”。 这将让 ACPI 不再在关机过程中禁用一些事件。 基于同样的原因， 一些系统需要把这个值设置为 “1” (这是默认值)。 这通常能够修复在休眠或关机时立即再次启动的问题。

#### 12.16.3.6. 其他问题

如果您有 ACPI 的其他问题 (同 docking station 协同工作、 无法检测设备， 等等)， 请把描述发给邮件列表； 不过， 这些问题也有可能和 ACPI 中尚未完成的部分有关， 它们可能需要时间才能被实现。 请给点耐心， 并准备测试我们可能会发给您的补丁。

### 12.16.4. ASL、`acpidump`， 以及 IASL

最常见的问题是 BIOS 制造商提供的不正确 (甚至完全错误的!) 字节代码。 这通常会以类似下面这样的内核消息显示在控制台上：

```
ACPI-1287: *** Error: Method execution failed [\\_SB_.PCI0.LPC0.FIGD._STA] \\
(Node 0xc3f6d160), AE_NOT_FOUND
```

许多时候， 您可以通过将 BIOS 升级到最新版本来解决此类问题。 绝大多数控制台消息是无害的， 但如果您有其他问题例如电池工作不正常， 则从 AML 开始查找问题将是一条捷径。 字节代码， 或常说的 AML， 是从一种叫做 ASL 的语言写成的源代码进行编译得到的结果。 AML 一般存放在 DSDT 表中。 要得到您系统的 ASL， 需要使用 [acpidump(8)](http://www.FreeBSD.org/cgi/man.cgi?query=acpidump&sektion=8&manpath=freebsd-release-ports)。 需要同时指定 `-t` (显示固定标的内容) 和 `-d` (将 AML 反编译成 ASL) 两个选项。 请参见 如何提交调试信息 一节了解如何使用它。

最方便的初步检查是尝试重新编译 ASL 来看看是否有错误。 通常可以忽略这一过程中产生的警告， 但错误一般就都是 bug， 它们通常就是导致 ACPI 无法正常工作的原因。 要重新编译您的 ASL， 可以使用下面的命令：

```
# `iasl your.asl`
```

### 12.16.5. 修复 ASL

我们的长期目标是让每一个人都能够在不需要任何用户干预的情况下使用 ACPI。 然而， 目前我们仍然在开发绕过 BIOS 制造商常见错误的方法。 Microsoft® 解释器 (`acpi.sys` 和 `acpiec.sys`) 并不会严格地检查是否遵守了标准， 因此许多只在 Windows® 中测试 ACPI 的 BIOS 制造商很可能永远不会修正他们的 ASL。 我们希望不断地找出并用文档说明 Microsoft® 的解释器到底允许那些不标准的行为， 并在 FreeBSD 进行对应的修改使它能够正常工作而不需要用户修正 ASL。 作为一项临时缓解问题的方法， 并帮助我们确认其行为， 您可以手工修正 ASL。 如果这样能够解决问题， 请把新旧 ASL 的 [diff(1)](http://www.FreeBSD.org/cgi/man.cgi?query=diff&sektion=1&manpath=freebsd-release-ports) 发给我们， 这样我们就有可能绕过 ACPI-CA 中的错误行为， 从而不再需要您来手工修正。

下面是一些常见的错误信息， 它们的原因， 以及如何修正。

#### 12.16.5.1. _OS dependencies (_OS 依赖)

某些 AML 假定世界是由不同版本的 Windows® 组成的。 您可以让 FreeBSD 声称自己是任意 OS 来看一看是否能够修正问题。 比较简单的办法是设置 `hw.acpi.osname`="Windows 2001" 到 `/boot/loader.conf` 中， 或使用您在 ASL 中找到的其他字符串。

#### 12.16.5.2. Missing Return statements (缺少返回语句)

一些方法可能没按照标准要求的那样显式地返回值。 尽管 ACPI-CA 无法处理它， 但 FreeBSD 提供了一个绕过它并允许其暗含地返回值的方法。 您也可以增加一个显式的 Return 语句， 如果您知道那里需要返回一个值的话。 要强制 `iasl` 编译 ASL， 需要使用 `-f` 标志。

#### 12.16.5.3. 替换默认的 AML

在定制 `your.asl` 之后， 您可以通过下面的命令编译它：

```
# `iasl your.asl`
```

可以使用 `-f` 标志来强制创建 AML， 即使在编译过程中发生了错误。 请注意某些错误 (例如， 缺少 Return 语句) 会自动被解释器忽略掉。

`DSDT.aml` 是 `iasl` 命令的默认输出文件名。 可以加载它来取代您 BIOS 中存在问题的副本 (它仍然存在于闪存中)， 其方法是按下面的说明编辑 `/boot/loader.conf`：

```
acpi_dsdt_load="YES"
acpi_dsdt_name="/boot/DSDT.aml"
```

一定要把您的 `DSDT.aml` 复制到 `/boot` 目录中。

### 12.16.6. 从 ACPI 中获取调试输出信息

ACPI 驱动程序提供了非常灵活的调试机制。 这允许您指定一组子系统， 以及所需要的详细信息。 需要调试的子系统可以按 “layers(层)” 来指定， 并分为 ACPI-CA 组件 (ACPI_ALL_COMPONENTS) 和 ACPI 硬件支持 (ACPI_ALL_DRIVERS)。 调试输出的详细程度可以通过 “level(详细度)” 来指定， 其范围是 ACPI_LV_ERROR (只报告错误) 到 ACPI_LV_VERBOSE (显示所有)。 “level” 是一个位掩码因此可以一次设置多个选项， 中间用空格分开。 实际使用中您应该考虑使用串行控制台来记录输出， 如果它太长以至于冲掉了控制台消息缓冲的话。 不同的层和输出详细度的完整列表可以在 [acpi(4)](http://www.FreeBSD.org/cgi/man.cgi?query=acpi&sektion=4&manpath=freebsd-release-ports) 联机手册中找到。

调试输出默认并不开启。 要起用它， 您需要在内核设置中添加 `options ACPI_DEBUG`， 如果您的内核中编入了 ACPI 的话。 您还可以在 `/etc/make.conf` 中加入 `ACPI_DEBUG=1` 来在全局起用它。 如果它只是模块， 您可以用下面的方法来重新编译 `acpi.ko`：

```
# `cd /sys/modules/acpi/acpi
&& make clean &&
make ACPI_DEBUG=1`
```

安装 `acpi.ko` 到 `/boot/kernel` and add your 并把所需的详细度和层在 `loader.conf` 中指定。 这个例子将启用所有 ACPI-CA 组件以及所有 ACPI 硬件驱动 (CPU、 LID， 等等) 的消息。 只输出错误信息， 也就是最低的详细度。

```
debug.acpi.layer="ACPI_ALL_COMPONENTS ACPI_ALL_DRIVERS"
debug.acpi.level="ACPI_LV_ERROR"
```

如果您需要的信息是由某个特定的事件触发的 (比如说， 休眠之后的唤醒)， 您可以不修改 `loader.conf` 而转而使用 `sysctl` 来在启动和为那个事件准备系统之后再指定层和详细度。 这些 `sysctl` 的名字和 `loader.conf` 中的一致。

### 12.16.7. 参考文献

关于 ACPI 的更多信息可以从下面这些地方找到：

*   The [FreeBSD ACPI 邮件列表](http://lists.FreeBSD.org/mailman/listinfo/freebsd-acpi)

*   ACPI 邮件列表存档 `[`lists.freebsd.org/pipermail/freebsd-acpi/`](http://lists.freebsd.org/pipermail/freebsd-acpi/)`

*   旧的 ACPI 邮件列表存档 `[`home.jp.FreeBSD.org/mail-list/acpi-jp/`](http://home.jp.FreeBSD.org/mail-list/acpi-jp/)`

*   The ACPI 2.0 标准 `[`acpi.info/spec.htm`](http://acpi.info/spec.htm)`

*   FreeBSD 手册页: [acpi(4)](http://www.FreeBSD.org/cgi/man.cgi?query=acpi&sektion=4&manpath=freebsd-release-ports), [acpi_thermal(4)](http://www.FreeBSD.org/cgi/man.cgi?query=acpi_thermal&sektion=4&manpath=freebsd-release-ports), [acpidump(8)](http://www.FreeBSD.org/cgi/man.cgi?query=acpidump&sektion=8&manpath=freebsd-release-ports), [iasl(8)](http://www.FreeBSD.org/cgi/man.cgi?query=iasl&sektion=8&manpath=freebsd-release-ports), [acpidb(8)](http://www.FreeBSD.org/cgi/man.cgi?query=acpidb&sektion=8&manpath=freebsd-release-ports)

*   [DSDT 调试资源](http://www.cpqlinux.com/acpi-howto.html#fix_broken_dsdt). (使用 Compaq 作为例子但通常情况下都很有用。)

## 第 13 章 FreeBSD 引导过程

## 13.1. 概述

启动电脑以及加载操作系统的过程被称为“引导过程”， 或者简称为“引导”。 FreeBSD 的引导过程给用户自定义启动提供了很大的伸缩性， 您可以选择启动不同的操作系统，或者是同一系统的不同版本及内核。

本章将详细介绍您能在 FreeBSD 引导过程中设置的配置选项。 这包括了引导内核、探测设备并启动 [init(8)](http://www.FreeBSD.org/cgi/man.cgi?query=init&sektion=8&manpath=freebsd-release-ports) 等等之前所发生的所有事情。 这些事项一般发生在文本由白变灰时。

读完这章您将会知道：

*   FreeBSD 引导系统里的各项组件， 以及它们之间的交互方式.

*   在 FreeBSD 引导时给各组件配置选项以控制引导过程。

*   [device.hints(5)](http://www.FreeBSD.org/cgi/man.cgi?query=device.hints&sektion=5&manpath=freebsd-release-ports)的基本知识。

### 只适用于 x86 :

本章只描述了运行于 Intel x86 体系之上的 FreeBSD 的引导过程。

## 13.2. 引导问题

启动电脑及启动和引导操作系统构成了一个有趣的两难境地。 按照定义在操作系统被启动之前计算机是无法完成任何任务的，包括运行磁盘上的程序。 如果计算机在没有操作系统的情况下不能运行来自于磁盘上的程序而操作系统又是放在磁盘上的， 那操作系统是如何启动的呢？

在 *Munchausen 男爵历险记 (The Adventures of Baron Munchausen)* 这本书中有一个和这个过程类似的故事， 一个人掉到了下水管道里， 然后靠着拉自己的靴襻 (bootstrap) 克服重重困难爬了出来。 在早期文献中， 多以术语 *bootstrap* 来指代操作系统的加载机制， 如今它逐渐被简写为 “booting”。

在 x86 硬件体系中，基本输入/输出系统 (BIOS) 负责加载操作系统， 为了做到这一点，BIOS 在磁盘上寻找主引导记录 (MBR)，而 MBR 必须在放置的磁盘的特定位置。BIOS 有足够的能力来读入和运行 MBR， 且假使地认为 MBR 能完成加载操作系统的剩余任务， MBR 可能需要 BIOS 的帮助。

在 MBR 中的代码通常被提为*引导管理器*， 尤其是与用户交互的那类。这一类引导器通常有更多代码位于磁盘第一 *轨道*或在操作系统的文件系统中。 (引导管理器有时也被称为*boot loader*， 但是 FreeBSD 对后面的引导阶段才使用这个术语。) 流行的引导管理器包括 boot0(亦称 Boot Easy，标准的 FreeBSD 引导管理器)、 Grub、GAG，以及 LILO。 (只有 boot0 能装得进 MBR。)

如果您只安装了一个操作系统，那么一个标准的 MBR 就足够了。 这个 MBR 先在磁盘上搜索可引导的(亦称“活动的”)分区， 然后运行分区上的代码以加载操作系统的其它部分。 MBR 由[fdisk(8)](http://www.FreeBSD.org/cgi/man.cgi?query=fdisk&sektion=8&manpath=freebsd-release-ports)安装，是一个缺省的 MBR。相关文件为 `/boot/mbr`。

如果您在磁盘上安装了多个操作系统那么您可以安装一个不同的 引导管理器，它能显示一张操作系统的列表，您能从中选择启动哪个。 这样的两种引导器将在下一小节中讨论。

启动系统的剩余部分被分为三个阶段。第一阶段由 MBR 执行,它只是使计算机进入特定的状态然后执行第二阶段。 第二阶段稍微干得多一些。第三阶段完成加载操作系统的任务。 工作被分为三个阶段是因为 PC 标准对第一第二阶段执行的程序的大小有所限制。 把这些任务连在一起使得 FreeBSD 可以提供更大伸缩性的加载器 (loader)。

然后内核启动，它开始探测设备并初始化它们。 一旦内核引导进程完成任务，内核将控制权交给用户进程 [init(8)](http://www.FreeBSD.org/cgi/man.cgi?query=init&sektion=8&manpath=freebsd-release-ports)， 它确认磁盘是否处于可用状态。[init(8)](http://www.FreeBSD.org/cgi/man.cgi?query=init&sektion=8&manpath=freebsd-release-ports) 然后开始用户级资源配置： 加载文件系统启动网卡，及粗略地启动所有 FreeBSD 系统加载时经常运行的进程。

## 13.3. 引导管理器和各引导阶段

### 13.3.1. The Boot Manager

在 MBR 或引导管理器中的代码有时被提为引导过程的 *阶段 0*。这一小节便是前面提到引导器中的两种： boot0 和 LILO。

boot0 引导管理器：. 由 FreeBSD 的安装程序以及 boot0cfg(8) 所安装的 MBR， 默认基于 `/boot/boot0`。 (程序 boot0 非常简单， 由于在 MBR 中的程序只能有 446 字节长， 分区表和 MBR 末端的`0x55AA`标识也要挤占一些空间。) 如果你已经安装 boot0 并且有多个操作系统在你的硬盘上， 那么你如果您安装了 FreeBSD MBR 而且安装了多个操作系统， 则会在系统启动时看到类似下面的提示：

例 13.1. `boot0` 截屏

```
F1 DOS
F2 FreeBSD
F3 Linux
F4 ??
F5 Drive 1

Default: F2
```

目前已经知道一些其它操作系统，特别是 Windows® ， 会以自己的 MBR 覆盖现有 MBR。 如果发生了这种事情， 或者您想用 FreeBSD 的 MBR 覆盖现有的 MBR，您可以使用以下的命令：

```
# `fdisk -B -b /boot/boot0 device`
```

*`device`* 是要写入 MBR 的设备名，比如 `ad0` 代表第一个 IDE 磁盘，`ad2` 代表第二个 IDE 控制器上的第一个 IDE 磁盘， `da0` 代表第一个 SCSI 磁盘，等等。 抑或，如果你需要一个自行配置的 MBR，请使用[boot0cfg(8)](http://www.FreeBSD.org/cgi/man.cgi?query=boot0cfg&sektion=8&manpath=freebsd-release-ports)。

The LILO Boot Manager: 要想安装这个引导管理器并也用来引导 FreeBSD， 首先启动 Linux，并将以下选项加入到已有的配置文件 `/etc/lilo.conf`：

```
other=/dev/hdXY
table=/dev/hdX
loader=/boot/chain.b
label=FreeBSD
```

在上面的内容里，使用 Linux 的标示符指定了 FreeBSD 的主分区和驱动器， 将*`X`*替换为 Linux 驱动器字母， 将*`Y`*替换为 Linux 主分区号。 如果您使用的是 SCSI 驱动器，您需要将 *`/dev/hd`* 改成 *`/dev/sd`*， 这里再次使用了 *`XY`* 的语法。 如果您安装的两个系统在同一驱动器上，`loader=/boot/chain.b` 选项可以去掉。现在您可以执行 `/sbin/lilo -v` 使修改生效；应检查屏幕上的消息确认修改。

### 13.3.2. 第一阶段，`/boot/boot1`，和第二阶段， `/boot/boot2`

概念上，第一，第二阶段同属于一个程序，处于磁盘的相同区域。但由于空间限制， 它们被分为两部分。可是您总是会一起安装它们。它们由安装器或 bsdlabel(见下文)复制自被组合而成的 `/boot/boot`。

它们位于文件系统外，引导分区的第一轨道，从第一扇区开始。在这里 boot0，或者任何其它引导管理器， 期望找到一个程序运行，继续引导进程。 所使用的扇区数可由`/boot/boot`的大小确定。

`boot1` 非常简单，因为它再多也只能有 512 字节， 只能识别储存着分区信息的 *bsdlabel*， 及寻找执行 `boot2`。

`boot2` 稍微有点加强，能够理解 FreeBSD 的文件系统以便于寻找里面的文件， 能提供选择内核和加载器的简单界面。

因为 loader 有着更强的功能， 提供了一套易于使用的引导配置，`boot2` 一般都执行 loader， 但以前它的任务是直接运行内核。

例 13.2. `boot2` 的屏幕输出

```
>> FreeBSD/i386 BOOT
Default: 0:ad(0,a)/boot/loader
boot:
```

如果您要更改已安装的 `boot1` 和 `boot2`，请使用命令 [bsdlabel(8)](http://www.FreeBSD.org/cgi/man.cgi?query=bsdlabel&sektion=8&manpath=freebsd-release-ports)。

```
# `bsdlabel -B diskslice`
```

*`diskslice`* 是用于引导的磁盘和分区， 比如 `ad0s1` 代表第一个 IDE 磁盘上的第一个分区。

### dangerously dedicated:

如果您在 [bsdlabel(8)](http://www.FreeBSD.org/cgi/man.cgi?query=bsdlabel&sektion=8&manpath=freebsd-release-ports) 命令中只使用了磁盘名，比如 `ad0`，就会破坏磁盘上的所有分区。 这当然不是您所希望的，所以在按下 **回车** 之前 一定要对命令进行多次确认。

### 13.3.3. 第三阶段，`/boot/loader`

加载器 (loader) 是三个阶段中的最后阶段， 且是放置在文件系统之中的，一般是文件 `/boot/loader`。

loader 被作为一种友好的配置方式，使用了一组内建且易用的命令集。 这些命令由一个强大的多的解释器支持构建，其本身带有复杂得多的命令集。

#### 13.3.3.1. Loader 程序流程

初始时，loader 会探测控制台和磁盘，识别是从哪块盘引导的。 它会根据这些信息设置变量， 启动解释器以接受通过脚本或交互方式传来的用户命令。

loader 然后会读取并运行 `/boot/loader.rc`， 默认地读取 `/boot/defaults/loader.conf` 以设置可靠的默认变量，读取 `/boot/loader.conf` 对这些变量作本地修改。`loader.rc` 依据这些变量进行动作，加载任何被选择的模块和内核。

最后，默认地，loader 会停留 10 秒等待按键， 若没有发生中断，就开始引导内核。如果被中断，用户会得到一个命令行提示符， 在这里用户得更改变量、卸载所有模块、加载模块、最后引导 或重新引导。

#### 13.3.3.2. Loader 内建的命令

这些是最常用的 loader 命令.对所有可用命令的解释请参见 [loader(8)](http://www.FreeBSD.org/cgi/man.cgi?query=loader&sektion=8&manpath=freebsd-release-ports)。

autoboot*`seconds`*

在给定的时间内如果没有中断发生就引导内核。它显示一个倒数计时， 默认的时间范围是 10 秒。

boot [-options] [kernelname]

立即按指定的选项启动指定名字的内核 (如果有指定的话)。 只有首先执行过 *unload* 命令之后指定的内核名字才会生效， 否则， 启动的将是先前已经加载的内核。

boot-conf

基于变量对各种模块进行自动配置 (和引导内核时发生的一样)。 您只须记住要先使用 `unload` 命令， 然后修改一些变量，比如 `kernel`。

help [topic]

显示从文件 `/boot/loader.help` 读取的帮助信息。如果给定的主题是 `index`， 那么列出来的是所有可用的主题。

include *`filename`* …

通过给定的文件名处理文件。文件被读入，然后被一行一行地解释。 任何错误都会立即中止 include 命令。

load [-t type] *`filename`*

加载内核、内核模块，或者是给定类型的文件 (通过给定的文件名)。 任何在文件名后面的参数都会被传给文件。

ls [-l] [path]

显示给定路径或者是根目录 (如果路径没有指定) 下面的文件列表。 如果指定了 `-l` 选项，文件大小也会显示。

lsdev [-v]

列出所有可以加载模块的设备。 如果指定了`-v` 选项，会显示出更多的细节。

lsmod [-v]

显示已被加载的模块。如果指明了 `-v` 选项， 会显示更多的细节。

more *`filename`*

显示指定的文件，每隔 `LINES` 停顿一次。

reboot

立即重启系统。

set *`variable`* set *`variable`*=*`value`*

设置 loader 的环境变量。

unload

移除所有已被加载的模块。

#### 13.3.3.3. Loader 示例

这里有一些实际中 loader 用法的示例

*   只是简单的引导默认内核，不同的是进入单用户模式：

    ```
    `boot -s`
    ```

*   卸载默认内核和模块，然后加载旧的 (或者其它) 的内核：

    ```
    `unload`
    `load kernel.old`
    ```

    您可以使用被称为通用内核的 `kernel.GENERIC`， 或者您以前安装的内核 `kernel.old` (当您升级或配置了您自己的内核等时候)。

    ### 注意:

    使用以下命令加载常用的模块和另一个内核：

    ```
    `unload`
    `set kernel="kernel.old"`
    `boot-conf`
    ```

*   加载内核配置脚本：

    ```
    `load -t userconfig_script /boot/kernel.conf`
    ```

#### 13.3.3.4. 启动时的 Splash 图像

Contributed by Joseph J. Barbish.

在启动时出现的 splash 图像比起原本的启动信息更加可视话。 这个图像将被始终显示在屏幕上直到出现控制台的登录提示或者 X 显示管理器提供了登录画面。

在 FreeBSD 系统中有两个基本的环境。 第一个是默认传统的控制台命令行环境。 在系统启动之后， 会在控制台上出现一个登录提示。 第二个环境是 X11 桌面图形环境。 在安装了 X11 和一种图形 桌面环境， 比如 GNOME， KDE， 或者 XFce， X11 桌面可以用 `startx` 命令运行。

比起传统基于字符的登录提示，有些用户可能更喜欢 X11 图形化的登录界面。 图形化的登录管理器像 Xorg 的 XDM， GNOME 的 gdm， KDE 的 kdm (还有其他 Port Collection 中的) 基本上都提供了一个图形化的登录界面代替控制台上的登录提示符。 在成功登录之后， 它们展现给用户一个图形化的桌面。

在命令行环境， splash 图像将在显示登录提示符之前隐藏所有启动时的监测与任务启动的消息。 在 X11 环境， 用户将会获得一个视觉上更加清爽启动体验， 类似于某些像 (Microsoft® Windows® 或者非 UNIX® 类型的系统) 用户所希望体验到的。

##### 13.3.3.4.1. Splash 图像功能

目前的 splash 图像的功能仅限于支持 256 色的位图 (`.bmp`) 或者 ZSoft PCX (`.pcx`) 文件。 此外， splash 图像文件的分辨率必须是 320x200 像素或者更少， 才够能在标准 VGA 适配器上使用。

要使用尺寸更大的图像， 达到最大分辨率 1024x768 像素， 则需开启 FreeBSD 的 VESA 支持。 这可以通过在系统启动时加载 VESA 模块完成， 或者在内核配置文件中加入 `VESA` 选项并编译 (参阅 第 9 章 *配置 FreeBSD 的内核*)。 VESA 支持给予了用户显示覆盖整个显示器的启动画面能力。

在启动的时候 splash 图像就会被显示在屏幕上， 它可以在任何时候都按任意键关闭。

Splash 图像同样也会是 X11 之外默认的屏幕保护。 在一段时间的闲置后，屏幕便会转为周期性的变换显示 splash 图像， 从明亮至暗淡， 周而复始。 默认的 splash 图像 (屏幕保护) 可由 `/etc/rc.conf` 中的 `saver=` 选项控制。 `saver=` 选项有一些内置的屏幕保护可供选择， 完整的列表可以再 [splash(4)](http://www.FreeBSD.org/cgi/man.cgi?query=splash&sektion=4&manpath=freebsd-release-ports) 手册页中找到。 默认的屏幕保护被称为 “warp”。 请注意在 `/etc/rc.conf` 中所指定 `saver=` 选项仅限应用于虚拟控制台。 对于 X11 图形化的登录管理器无效。

一些有关启动引导器的信息， 包括启动选项菜单和一个定时倒数提示符都会在启动时显示， 即是开启了 splash 图像功能。

splash 图像文件样本可以从 [`artwork.freebsdgr.org`](http://artwork.freebsdgr.org/node/3/) 下载。 安装了 [sysutils/bsd-splash-changer](http://www.freebsd.org/cgi/url.cgi?ports/sysutils/bsd-splash-changer/pkg-descr) port 之后， 每次启动的时候便能从集合中随机选择 splash 图像。

##### 13.3.3.4.2. 开启 Splash 图像功能

Splash 图像 (`.bmp`) 或者 (`.pcx`) 文件必须放置在 root 分区上， 比如 `/boot` 目录。

对于默认的显示分辨率 (256 色，320x200 像素或更少) 编辑 `/boot/lodaer.conf`， 添加如下的设置：

```
splash_bmp_load="YES"
bitmap_load="YES"
bitmap_name="*`/boot/splash.bmp`*"
```

对于更高的分辨率，最大至 1024x768 像素， 编辑 `/boot/lodaer.conf`， 添加如下的设置：

```
vesa_load="YES"
splash_bmp_load="YES"
bitmap_load="YES"
bitmap_name="*`/boot/splash.bmp`*"
```

以上这些设置假设 `/boot/splash.bmp` 为需要被使用的 splash 图像。 当需要使用 PCX 文件的时候， 添加入下列设置， 根据分辨率的高低添加 `vesa_load="YES"`。

```
splash_pcx_load="YES"
bitmap_load="YES"
bitmap_name="*`/boot/splash.pcx`*"
```

文件名并不限于以上例子中的 “splash”。 它可以是任何名称，只要是 BMP 或者 PCX 类型的文件， 比如 `splash_640x400.bmp` 或者 `blue_wave.pcx`.

一些有趣的 `loader.conf` 选项：

`beastie_disable="YES"`

这将关闭显示启动选项菜单， 但是倒数记时仍然会出现。 即是在启动菜单选项被禁用的时候， 在倒数记时段键入相应的启动选项仍然有效。

`loader_logo="beastie"`

这将替换启动选项菜单右侧默认显示的 “FreeBSD” 为彩色的小魔鬼标志， 就像以往的发行版那样。

请参阅 [splash(4)](http://www.FreeBSD.org/cgi/man.cgi?query=splash&sektion=4&manpath=freebsd-release-ports)， [loader.conf(5)](http://www.FreeBSD.org/cgi/man.cgi?query=loader.conf&sektion=5&manpath=freebsd-release-ports) 和 [vga(4)](http://www.FreeBSD.org/cgi/man.cgi?query=vga&sektion=4&manpath=freebsd-release-ports) 手册页获取更多详细信息。

## 13.4. 内核在引导时的交互

一旦内核被 loader (一般情况下) 或者 boot2 (越过 loader) 加载， 它将检查引导标志，如果有的话，就会进行必要的动作调整。

### 13.4.1. 内核引导标志

这里是一些常用的引导标志：

`-a`

在内核初始化时，询问作为根加载的设备。

`-C`

从 CDROM 引导。

`-c`

运行 UserConfig (引导时的内核配置器)

`-s`

引导进入单用户模式

`-v`

在内核引导过程中显示更有的信息

### 注意:

还有更多的引导标志，阅读 [boot(8)](http://www.FreeBSD.org/cgi/man.cgi?query=boot&sektion=8&manpath=freebsd-release-ports) 以获取有关它们的信息。

## 13.5. Device Hints

Contributed by Tom Rhodes.

在初始化系统启动时，[loader(8)](http://www.FreeBSD.org/cgi/man.cgi?query=loader&sektion=8&manpath=freebsd-release-ports) 会读取 [device.hints(5)](http://www.FreeBSD.org/cgi/man.cgi?query=device.hints&sektion=5&manpath=freebsd-release-ports) 文件。这个文件以变量的形式储存着内核引导信息， 有时被称为 “device hints”。 设备驱动程序用“device hints” 对设备进行配置。

Device hints 也可以在 第三阶段的 boot loader 的命令行提示符中指定。变量可以用 `set` 命令添加，`unset` 命令删除， `show` 命令查看。在文件 `/boot/device.hints` 设置的变量亦可以在这里被覆盖。键入 boot loader 中的变量不是永久性的，在下次启动时就会被忘记。

一旦系统引导成功，[kenv(1)](http://www.FreeBSD.org/cgi/man.cgi?query=kenv&sektion=1&manpath=freebsd-release-ports) 命令可以用来清楚所有的变量。

文件 `/boot/device.hints` 的语法是一行一个变量， 使用“#”作为注释标记。 每行是按照如下方式组织的：

```
`hint.driver.unit.keyword="value"`
```

第三阶段 boot loader 的语法是：

```
`set hint.driver.unit.keyword=value`
```

`driver` 是设备驱动程序名，`unit` 是设备驱动程序单位名，`keyword` 是 hint 关键字。 关键字可以由以下选项组成：

*   `at`：指明设备所绑定的总线

*   `port`：指明所使用 I/O 的起始地址。

*   `irq`：指明所使用的中断请求号。

*   `drq`：指明 DMA channel 号。

*   `maddr`：指明设备占用的物理内存地址。

*   `flags`：给设备设置各种标志位。

*   `disabled`：如果设成 `1`， 设备被禁用。

设备驱动程序能够接受更多的 hints，推荐您参看它们的联机手册。参看 [device.hints(5)](http://www.FreeBSD.org/cgi/man.cgi?query=device.hints&sektion=5&manpath=freebsd-release-ports)、[kenv(1)](http://www.FreeBSD.org/cgi/man.cgi?query=kenv&sektion=1&manpath=freebsd-release-ports)、[loader.conf(5)](http://www.FreeBSD.org/cgi/man.cgi?query=loader.conf&sektion=5&manpath=freebsd-release-ports) 和 [loader(8)](http://www.FreeBSD.org/cgi/man.cgi?query=loader&sektion=8&manpath=freebsd-release-ports) 联机手册以获取更多的信息。

## 13.6. Init：进程控制及初始化

一旦内核完成引导，它就把控制权交给了用户进程 [init(8)](http://www.FreeBSD.org/cgi/man.cgi?query=init&sektion=8&manpath=freebsd-release-ports)，它放置在 `/sbin/init`， 或者 `init_path` 变量指定的程序路径中。 这个变量是在 `loader` 里面设置的。

### 13.6.1. 自动重启过程

自动重启过程会确认系统中可用的文件系统处于健康的状态。 如果不是， 而且使用 [fsck(8)](http://www.FreeBSD.org/cgi/man.cgi?query=fsck&sektion=8&manpath=freebsd-release-ports) 也无法修复这些问题， [init(8)](http://www.FreeBSD.org/cgi/man.cgi?query=init&sektion=8&manpath=freebsd-release-ports) 会进入 单用户模式 以便系统管理员直接修正这些问题。

### 13.6.2. 单用户模式

此模式可以通过 自动重启过程 或者通过带有 `-s` 选项的用户引导或通过在 `loader` 里设置 `boot_single` 变量等多种方式来达到。

也可以在多用户模式下调动无重启 (`-r`) 选项和停机 (`-h`) 选项的 [shutdown(8)](http://www.FreeBSD.org/cgi/man.cgi?query=shutdown&sektion=8&manpath=freebsd-release-ports) 命令来进入单用户模式。

如果系统 `控制台` 在文件 `/etc/ttys` 中被设置为 `不安全(insecure)`， 在初始化单用户模式前会出现要求输入 `root` 密码的命令行提示符。

例 13.3. 在 `/etc/ttys` 文件中的不安全控制台

```
# name  getty                           type    status          comments
#
# If console is marked "insecure", then init will ask for the root password # when going to single-user mode.
console none                            unknown off insecure
```

### 注意:

把控制台设置成 `不安全 (insecure)` 使只知道 `root` 密码的人才能进入单用户模式， 因为您认为控制台在物理上是不安全的。因此如果您考虑到安全性， 请选择 `不安全 (insecure)`，而非 `安全 (secure)`。

### 13.6.3. 多用户模式

如果 [init(8)](http://www.FreeBSD.org/cgi/man.cgi?query=init&sektion=8&manpath=freebsd-release-ports) 发现您的文件系统一切正常，又或者用户在单用户模式完成了工作， 系统就会进入多用户模式，开始系统的资源配置。

#### 13.6.3.1. 资源配置 (rc)

资源配置分别从文件 `/etc/defaults/rc.conf`、 `/etc/rc.conf` 中读取默认配置和细节配置， 然后加载在文件 `/etc/fstab` 中提及的文件系统、 启动网络服务、启动各种系统守护进程，最后启动本地安装包的启动脚本。

[rc(8)](http://www.FreeBSD.org/cgi/man.cgi?query=rc&sektion=8&manpath=freebsd-release-ports) 联机手册是关于资源配置的很好的参考。

## 13.7. 关机 (shutdown) 过程

由命令 [shutdown(8)](http://www.FreeBSD.org/cgi/man.cgi?query=shutdown&sektion=8&manpath=freebsd-release-ports) 的发起的关机过程中， [init(8)](http://www.FreeBSD.org/cgi/man.cgi?query=init&sektion=8&manpath=freebsd-release-ports) 会试着运行 `/etc/rc.shutdown` 脚本， 给所有进程发送 `TERM` 信号， 最后给不按时停止的进程发送 `KILL` 信号。

在支持电源管理的平台上关闭 FreeBSD 系统的电源， 只要简单地使用命令 `shutdown -p now` 即可。 此外， 可以用命令 `shutdown -r now` 来重启 FreeBSD。 要执行 [shutdown(8)](http://www.FreeBSD.org/cgi/man.cgi?query=shutdown&sektion=8&manpath=freebsd-release-ports) 您必须是 `root` 用户或 `operator` 组的成员。 也可以使用 [halt(8)](http://www.FreeBSD.org/cgi/man.cgi?query=halt&sektion=8&manpath=freebsd-release-ports) 和 [reboot(8)](http://www.FreeBSD.org/cgi/man.cgi?query=reboot&sektion=8&manpath=freebsd-release-ports) 命令来关闭系统， 请参看它们的联机手册以获得更多的信息。

### 注意:

电源管理需要支持， 这要求内核支持 [acpi(4)](http://www.FreeBSD.org/cgi/man.cgi?query=acpi&sektion=4&manpath=freebsd-release-ports) 或以模块形式加载它。

## 第 14 章 用户和基本的帐户管理

Contributed by Neil Blakey-Milner.

## 14.1. 概述

FreeBSD 允许多个用户同时使用计算机. 当然,这些用户中不是很多人同时坐在同一台计算机前. [^([6])](#ftn.idp78039024),而是其他用户可以通过网络来使用同一台计算机以完成他们的工作.要使用系统,每个人都应该有一个帐户.

读完这章，您将了解到:

*   在一个 FreeBSD 系统上不同用户帐户之间的区别.

*   如何添加用户帐户.

*   如何删除用户帐户.

*   如何改变帐户细节，如用户的全名，或首选的 shell.

*   如何在每个帐户基础上设置限制，来控制像内存，CPU 时钟这样的资源.

*   如何使用组来使帐户管理更容易.

在阅读这章之前，您应当了解:

*   了解 UNIX®和 FreeBSD 的基础知识 (第 4 章 *UNIX 基础*).

* * *

[^([6])](#idp78039024) Well, 除非您连接多个终端设备,这种情况我们在第 27 章 *串口通讯*讨论.

## 14.2. 介绍

所有访问系统的用户都是通过帐户完成的，所以用户和帐户管理是 FreeBSD 系统不可或缺的重要部分.

每个 FreeBSD 系统的帐户都有一些和它相对应的信息去验证它.

用户名

用户名在`login:` 提示符的后面键入。 用户名对于一台计算机来讲是唯一的； 您不可以使用两个相同的用户名来登录。 有很多用来创建正确用户名的规则， 具体请参考 [passwd(5)](http://www.FreeBSD.org/cgi/man.cgi?query=passwd&sektion=5&manpath=freebsd-release-ports)； 您使用的用户名通常需要 8 个或更少的小写字母。

口令

每个帐户都有一个口令与它对应。 口令可以是空的， 这样不需要口令就可以访问系统。 这通常不是一个好主意； 每个帐户都应该有口令。

用户 ID (UID)

UID 是系统用来识别用户的数字，传统上它的范围是 0 到 65536 之间[^([7])](#ftn.users-largeuidgid)，用以唯一地标识用户。 FreeBSD 在内部使用 UID 来识别用户 ── 在工作以前。 任何允许您指定一个用户名的 FreeBSD 命令都会把它转换成 UID。 这意味着您可以用不同的用户名使用多个帐户， 但它们的 UID 是一样的。 FreeBSD 会把这些帐户认定是同一个用户。

组 ID (GID)

GID 是用来识别用户所在的组的， 传统上范围在 0 到 65536 之间[^([7])](users-introduction.xhtml#ftn.users-largeuidgid)的数字。 组是一种基于用户 GID 而不是它们的 UID 的用来控制用户访问资源的机制。 这可以减少一些配置文件的大小。 一个用户也可以属于多个组。

登录类

登录类是对组机制的扩展,当把系统分配给不同用户时,它提供了额外的灵活性.

口令的定期更改

默认情况下， FreeBSD 并不强制用户去改变他们的口令。 您可以以用户为单位强制要求一些或所有的用户定期改变他们的口令。

帐户的到期时间

默认情况下 FreeBSD 不会自动完成帐户过期操作。 如果您正在创建帐户， 您应该知道一个帐户的有效使用期限。 例如， 在学校里您会为每个学生建立一个帐户， 您可以指定它们何时过期。 帐户过期后， 虽然帐户的目录和文件仍然存在， 但帐户已经不能继续使用了。

用户的全名

用户名可以唯一地识别 FreeBSD 的帐户， 但它不会反映用户的全名。 这些信息可能与帐户是相关的。

主目录

主目录是用户用来启动的目录的完全路径。 一个通常的规则是把所有用户的主目录都放在 `/home/username` 下，或者 `/usr/home/username` 下。 用户将把他们的个人文件放在自己的主目录下， 他们可以在那里创建任何目录.

用户 shell

Shell 提供了用户用来操作系统的默认环境。 有很多不同的 shell， 有经验的用户会根据他们的经验来选择自己喜好的 shell。

有三种类型的帐户: 超级用户， 系统用户， 以及 普通用户。 超级用户帐户通常叫做 `root`， 可以没有限制地管理系统。 系统用户运行服务。 最后， 普通用户给那些登录系统以及阅读邮件的人使用。

* * *

[^([7])](#users-largeuidgid) 可以使用的 UID/GID 的最大值是 4294967295， 但这可能会给采用上述假定的软件造成严重的问题。

## 14.3. 超级用户帐户

超级用户帐户， 通常叫做 `root`， 可以重新配置和管理系统， 在收发邮件， 系统检查或编程这样的日常工作中， 尽量不要使用 root 权限。

这是因为不象普通用户帐户， 超级用户能够无限制地操作系统， 超级用户帐户的滥用可能会引起无法想象的灾难。 普通的用户帐户不会由于出错而破坏系统， 所以要尽可能的使用普通帐户， 除非您需要额外的特权。

在使用超级用户命令时要再三检查， 因为一个额外的空格或缺少某个字符的命令都可能会引起数据丢失。

所以， 在阅读完这章后您第一件要做的事就是， 在平时使用的时候， 创建一个没有特权的用户帐户。 无论您使用的是单用户还是多用户系统这样的申请都是相同的。 在这章的后面， 我们将讨论如何创建一个额外的帐户和如何在普通用户和超级用户之间进行切换。

## 14.4. 系统帐户

系统用户是那些要使用诸如 DNS、 邮件， web 等服务的用户。 使用帐户的原因就是安全； 如果所有的用户都由超级用户来运行， 那它们就可以不受约束地做任何事情。

典型的系统帐户包括 `daemon`、 `operator`、 `bind` (供 域名服务 使用)、 `news`， 以及 `www`。

`nobody` 是普通的没有特权的系统用户。 然而， 大多数与用户联系很密切的服务是使用 `nobody`的， 记的这点非常重要， 这样可能使用户变的非常有特权。

## 14.5. 用户帐户

用户帐户是让真实的用户访问系统的主要方式， 这些帐户把用户和环境隔离， 能阻止用户损坏系统和其他用户， 在不影响其他用户的情况之下定制自己的环境。

任何人访问您的系统必须要有他们自己唯一的帐户。 这可以让您找到谁做了什么事， 并且阻止人们破坏其他用户的设置和阅读其他人的邮件等等。

每个用户能够设置他们自己的环境， 以利于他们通过改变 shell， 编辑器， 键盘绑定和语言等适应并且更好的使用这个系统。

## 14.6. 修改帐户

在 UNIX® 的处理用户帐户的环境中有很多不同的命令可用. 最普通的命令如下， 接下来是详细使用它们的例子。

| 命令 | 摘要 |
| --- | --- |
| [adduser(8)](http://www.FreeBSD.org/cgi/man.cgi?query=adduser&sektion=8&manpath=freebsd-release-ports) | 在命令行添加新用户. |
| [rmuser(8)](http://www.FreeBSD.org/cgi/man.cgi?query=rmuser&sektion=8&manpath=freebsd-release-ports) | 在命令行删除用户. |
| [chpass(1)](http://www.FreeBSD.org/cgi/man.cgi?query=chpass&sektion=1&manpath=freebsd-release-ports) | 一个灵活的用于修改用户数据库信息的工具. |
| [passwd(1)](http://www.FreeBSD.org/cgi/man.cgi?query=passwd&sektion=1&manpath=freebsd-release-ports) | 一个用于修改用户口令的简单的命令行工具. |
| [pw(8)](http://www.FreeBSD.org/cgi/man.cgi?query=pw&sektion=8&manpath=freebsd-release-ports) | 一个强大灵活修改用户帐户的工具. |

### 14.6.1. `添加用户`

[adduser(8)](http://www.FreeBSD.org/cgi/man.cgi?query=adduser&sektion=8&manpath=freebsd-release-ports) 是一个简单的添加新用户的命令. 它为用户创建 `passwd` 和 `group` 文件。 它也为新用户创建一个主目录， 之后， 它会复制一组默认的配置文件 (“dotfiles”) 从 `/usr/share/skel` 这个目录， 然后给新用户发送一封带欢迎信息的邮件。

例 14.1. 在 FreeBSD 中添加一个新用户

```
# `adduser`
Username: `jru`
Full name: `J. Random User` Uid (Leave empty for default):
Login group [jru]:
Login group is jru. Invite jru into other groups? []: `wheel`
Login class [default]:
Shell (sh csh tcsh zsh nologin) [sh]: `zsh`
Home directory [/home/jru]:
Home directory permissions (Leave empty for default):
Use password-based authentication? [yes]:
Use an empty password? (yes/no) [no]:
Use a random password? (yes/no) [no]:
Enter password:
Enter password again:
Lock out the account after creation? [no]:
Username   : jru
Password   : ****
Full Name  : J. Random User
Uid        : 1001
Class      :
Groups     : jru wheel
Home       : /home/jru
Shell      : /usr/local/bin/zsh
Locked     : no
OK? (yes/no): `yes`
adduser: INFO: Successfully added (jru) to the user database.
Add another user? (yes/no): `no` Goodbye!
#
```

### 注意:

您输入的口令并不会回显到屏幕上， 此外系统也不会显示星号。 请务必确保没有输错口令。

### 14.6.2. `删除用户`

您可以使用[rmuser(8)](http://www.FreeBSD.org/cgi/man.cgi?query=rmuser&sektion=8&manpath=freebsd-release-ports) 从系统中完全删除一个用户. [rmuser(8)](http://www.FreeBSD.org/cgi/man.cgi?query=rmuser&sektion=8&manpath=freebsd-release-ports) 执行如下步骤:

1.  删除用户的 [crontab(1)](http://www.FreeBSD.org/cgi/man.cgi?query=crontab&sektion=1&manpath=freebsd-release-ports) 记录 (如果有的话).

2.  删除属于用户的[at(1)](http://www.FreeBSD.org/cgi/man.cgi?query=at&sektion=1&manpath=freebsd-release-ports) 工作.

3.  杀掉属于用户的所有进程.

4.  删除本地口令文件中的用户.

5.  删除用户的主目录 (如果他有自己的主目录).

6.  删除来自 `/var/mail`属于用户的邮件.

7.  删除所有诸如 `/tmp`的临时文件存储区中的文件.

8.  最后, 删除 `/etc/group`中所有属于组的该用户名.

    ### 注意:

    如果一个组变成空，而组名和用户名一样,组将被删除. [adduser(8)](http://www.FreeBSD.org/cgi/man.cgi?query=adduser&sektion=8&manpath=freebsd-release-ports)命令建立每个用户唯一的组.

[rmuser(8)](http://www.FreeBSD.org/cgi/man.cgi?query=rmuser&sektion=8&manpath=freebsd-release-ports) 不能用来删除超级用户的帐户, 因为那样做是对系统极大的破坏.

默认情况下, 使用交互模式, 这样能够让您清楚的知道您在做什么.

例 14.2. `删除用户` 交互模式下的帐户删除

```
# `rmuser jru` Matching password entry:
jru:*:1001:1001::0:0:J. Random User:/home/jru:/usr/local/bin/zsh Is this the entry you wish to remove? `y` Remove user's home directory (/home/jru)? `y` Updating password file, updating databases, done.
Updating group file: trusted (removing group jru -- personal group is empty) done.
Removing user's incoming mail file /var/mail/jru: done.
Removing files belonging to jru from /tmp: done.
Removing files belonging to jru from /var/tmp: done.
Removing files belonging to jru from /var/tmp/vi.recover: done.
#
```

### 14.6.3. `chpass`

[chpass(1)](http://www.FreeBSD.org/cgi/man.cgi?query=chpass&sektion=1&manpath=freebsd-release-ports) 可以改变用户的口令, shells, 和包括个人信息在内的数据库信息.

只有系统管理员， 即超级用户， 才可以用 [chpass(1)](http://www.FreeBSD.org/cgi/man.cgi?query=chpass&sektion=1&manpath=freebsd-release-ports) 改变其他用户口令和信息。

除了可选择的用户名， 不需要任何选项， [chpass(1)](http://www.FreeBSD.org/cgi/man.cgi?query=chpass&sektion=1&manpath=freebsd-release-ports) 将显示一个包含用户信息的编辑器. 可以试图改变用户在数据库中的信息.

### 注意:

如果您不是超级用户的话， 在退出编辑状态之后， 系统会询问您口令。

例 14.3. 以超级用户交互执行 `chpass` 命令

```
#Changing user database information for jru.
Login: jru
Password: *
Uid [#]: 1001
Gid [# or name]: 1001
Change [month day year]:
Expire [month day year]:
Class:
Home directory: /home/jru
Shell: /usr/local/bin/zsh
Full Name: J. Random User
Office Location:
Office Phone:
Home Phone:
Other information:
```

普通用户只能改变他们自己很少的一部分信息.

例 14.4. 以普通用户交互执行 `chpass` 命令

```
#Changing user database information for jru.
Shell: /usr/local/bin/zsh
Full Name: J. Random User
Office Location:
Office Phone:
Home Phone:
Other information:
```

### 注意:

[chfn(1)](http://www.FreeBSD.org/cgi/man.cgi?query=chfn&sektion=1&manpath=freebsd-release-ports) 和 [chsh(1)](http://www.FreeBSD.org/cgi/man.cgi?query=chsh&sektion=1&manpath=freebsd-release-ports) 只是到 [chpass(1)](http://www.FreeBSD.org/cgi/man.cgi?query=chpass&sektion=1&manpath=freebsd-release-ports) 的符号连接， 类似地， [ypchpass(1)](http://www.FreeBSD.org/cgi/man.cgi?query=ypchpass&sektion=1&manpath=freebsd-release-ports), [ypchfn(1)](http://www.FreeBSD.org/cgi/man.cgi?query=ypchfn&sektion=1&manpath=freebsd-release-ports) 以及 [ypchsh(1)](http://www.FreeBSD.org/cgi/man.cgi?query=ypchsh&sektion=1&manpath=freebsd-release-ports) 也是这样。 NIS 是自动支持的， 不一定要在命令前指定 `yp`。 如果这让您有点不太明白， 不必担心， NIS 将在 第 30 章 *网络服务器* 介绍。

### 14.6.4. `passwd 命令`

[passwd(1)](http://www.FreeBSD.org/cgi/man.cgi?query=passwd&sektion=1&manpath=freebsd-release-ports) 是改变您自己作为一个普通用户口令或者作为超级用户口令常用的方法.

### 注意:

用户改变口令前必须键入原来的口令, 防止用户离开终端时非授权的用户进入改变合法用户的口令。

例 14.5. 改变您的口令

```
% `passwd` Changing local password for jru.
Old password:
New password:
Retype new password:
passwd: updating the database...
passwd: done
```

例 14.6. 改变其他用户的口令同超级用户的一样

```
# `passwd jru` Changing local password for jru.
New password:
Retype new password:
passwd: updating the database...
passwd: done
```

### 注意:

就象 [chpass(1)](http://www.FreeBSD.org/cgi/man.cgi?query=chpass&sektion=1&manpath=freebsd-release-ports)一样, [yppasswd(1)](http://www.FreeBSD.org/cgi/man.cgi?query=yppasswd&sektion=1&manpath=freebsd-release-ports) 只是一个到 [passwd(1)](http://www.FreeBSD.org/cgi/man.cgi?query=passwd&sektion=1&manpath=freebsd-release-ports)的连接, 所以 NIS 用任何一个命令都可以正常工作.

### 14.6.5. `pw 命令`

[pw(8)](http://www.FreeBSD.org/cgi/man.cgi?query=pw&sektion=8&manpath=freebsd-release-ports) 是一个用来创建、删除、修改、显示用户和组的命令行工具。 它还有系统用户和组文件编辑器的功能。 [pw(8)](http://www.FreeBSD.org/cgi/man.cgi?query=pw&sektion=8&manpath=freebsd-release-ports) 有一个非常强大的命令行选项设置， 但新用户可能会觉得它比这里讲的其它命令要复杂很多。

## 14.7. 限制用户使用系统资源

如果您有一些用户， 并想要对他们所使用的系统资源加以限制， FreeBSD 提供了一些系统管理员限制用户访问系统资源的方法。 这些限制通常被分为两种: 磁盘配额， 以及其它资源限制。

磁盘配额限制用户对磁盘的使用， 而且它还提供一种快速检查用户使用磁盘数量而不需要时刻计算的方法。 配额将在 第 19.15 节 “文件系统配额”讨论.

其它资源限制包括 CPU、 内存以及用户可能会使用的其它资源。 这些是通过对登录进行分类完成的， 下面将做讨论。

登录的类由 `/etc/login.conf` 文件定义。 比较精确的描述超出了本章的范围， 但 [login.conf(5)](http://www.FreeBSD.org/cgi/man.cgi?query=login.conf&sektion=5&manpath=freebsd-release-ports) 联机手册会有比较详细的描述。 可以说每个用户都分配到一个登录类 (默认是 `defalut`)， 每个登录类都有一套和它相对应的功能。 登录功能是 `名字=值` 这样的一对值， 其中*`名字`* 是一个众所周知的标识符， *`值`* 是一个根据名字经过处理得到的任意字符串。 设置登录类和功能相当简单， 在 [login.conf(5)](http://www.FreeBSD.org/cgi/man.cgi?query=login.conf&sektion=5&manpath=freebsd-release-ports) 联机手册会有比较详细的描述。

### 注意:

系统并不直接读取 `/etc/login.conf` 中的配置， 而是采用数据库文件 `/etc/login.conf.db` 以提供更快的查找能力。 要从 `/etc/login.conf` 文件生成 `/etc/login.conf.db`， 应使用下面的命令：

```
# `cap_mkdb /etc/login.conf`
```

资源限制与普通登录限制是有区别的。 首先， 对于每种限制， 有软限制 (比较常见) 和硬限制之分。 一个软限制可能被用户调整过， 但不会超过硬限制。 越往后可能越低， 但不会升高。 其次， 绝大多数资源限制会分配特定用户的每个进程， 而不是该用户的全部进程。 注意， 这些区别是资源限制的特殊操作所规定的， 不是登录功能框架的完成 (也就是说, 他们*实际上* 不是一个登录功能的特例)。

不再罗嗦了， 下面是绝大多数资源限制的例子 (您可以在 [login.conf(5)](http://www.FreeBSD.org/cgi/man.cgi?query=login.conf&sektion=5&manpath=freebsd-release-ports) 找到其它与登录功能相关的内容)。

`coredumpsize`

很明显， 由程序产生的核心文件大小的限制在磁盘使用上是属于其它限制的 (例如， `文件大小`， 磁盘配额)。 不过， 由于用户自己无法产生核心文件， 而且通常并不删除它们， 设置这个可以尽量避免由于一个大型应用程序的崩溃所造成的大量磁盘空间的浪费。 (例如, emacs) 崩溃。

`cputime`

这是一个用户进程所能消耗的最长 CPU 时间。 违反限制的进程， 将被内核杀掉。

### 注意:

这是一个有关 CPU 消耗的*时钟* 限制, 不是[top(1)](http://www.FreeBSD.org/cgi/man.cgi?query=top&sektion=1&manpath=freebsd-release-ports) 和 [ps(1)](http://www.FreeBSD.org/cgi/man.cgi?query=ps&sektion=1&manpath=freebsd-release-ports) 命令时屏幕上显示的 CPU 消耗的百分比。 在写此说明时， 后者的限制是不太可能和没有价值的： 编译器 ── 编译一个可能是合法的工作 ── 可以在某一时刻轻易的用掉 100% 的 CPU。

`filesize`

这是用户可以处理一个文件的最大值。 不象 磁盘配额， 这个限制是对单个文件强制执行的， 不是用户自己的所有文件。

`maxproc`

这是一个用户可以运行的最大进程数。 这包括前台和后台进程。 很明显， 这不可能比系统指定 `kern.maxproc` [sysctl(8)](http://www.FreeBSD.org/cgi/man.cgi?query=sysctl&sektion=8&manpath=freebsd-release-ports) 的限制要大。 同时也要注意， 设置的过小会妨碍用户的处理能力： 可能需要多次登录或执行多个管道。 一些任务， 例如编译一些大的程序， 也可能会产生很多进程 (例如， [make(1)](http://www.FreeBSD.org/cgi/man.cgi?query=make&sektion=1&manpath=freebsd-release-ports)， [cc(1)](http://www.FreeBSD.org/cgi/man.cgi?query=cc&sektion=1&manpath=freebsd-release-ports) 以及其它一些预处理程序)。

`memorylocked`

这是一个进程允许锁到主存中的最大内存容量 (参见 [mlock(2)](http://www.FreeBSD.org/cgi/man.cgi?query=mlock&sektion=2&manpath=freebsd-release-ports))。 大型程序， 例如像 [amd(8)](http://www.FreeBSD.org/cgi/man.cgi?query=amd&sektion=8&manpath=freebsd-release-ports) 在遇到问题时， 它们得到的巨大交换量无法传递给系统进行处理。

`memoryuse`

这是在给定时间内一个进程可能消耗的最大内存数量。 它包括核心内存和交换内存。 在限制内存消耗方面， 这不是一个完全的限制，但它是一个好的开始。

`openfiles`

这是一个进程可以打开的最大文件数。 在 FreeBSD 中， 文件可以被表现为套接字和 IPC 通道； 注意不要把这个数设置的太小。 系统级的限制是由 `kern.maxfiles` 定义的， 详情参见 [sysctl(8)](http://www.FreeBSD.org/cgi/man.cgi?query=sysctl&sektion=8&manpath=freebsd-release-ports)。

`sbsize`

这是网络内存数量的限制， 这主要是针对通过创建许多套接字的老式 DoS 攻击的， 但也可以用来限制网络通信。

`stacksize`

这是一个进程堆栈可能达到的最大值。 它不能单独的限制一个程序可能使用的内存数量； 所以， 需要与其它的限制手段配合使用。

在设置资源限制时， 有一些其他的事需要注意。 下面是一些通常的技巧、 建议和注意事项。

*   系统启动的进程`/etc/rc`会被指派给 `守护程序` 的登录类.

*   虽然 `/etc/login.conf` 文件是一个对绝大多数限制做合理配置的资源文件， 但只有您也就是系统管理员，才知道什么最适合您的系统。 设置的太高可能会因为过于开放而导致系统被滥用， 而设置过低， 则可能降低效率。

*   使用 X Window 的用户可能要比其他用户使用更多的资源。 因为 X11 本身就使用很多资源， 而且它鼓励用户同时运行更多的程序。

*   务必注意， 许多限制措施是针对单个进程来实施的， 它们并不限制某一用户所能用到的总量。 例如， 将 `openfiles` 设置为 50 表示以该用户身份运行的进程最多只能打开 50 个文件。 因而， 用户实际可以打开的文件总数就应该是 `maxproc` 和 `openfiles` 值的乘积。 对内存用量的限额与此类似。

有关资源限制,登录类的更深入信息可以查看相关联机手册: [cap_mkdb(1)](http://www.FreeBSD.org/cgi/man.cgi?query=cap_mkdb&sektion=1&manpath=freebsd-release-ports), [getrlimit(2)](http://www.FreeBSD.org/cgi/man.cgi?query=getrlimit&sektion=2&manpath=freebsd-release-ports), [login.conf(5)](http://www.FreeBSD.org/cgi/man.cgi?query=login.conf&sektion=5&manpath=freebsd-release-ports).

## 14.8. 组

组简单的讲就是一个用户列表. 组通过组名和 GID (组 ID) 来识别。 在 FreeBSD (以及绝大多数其他 UNIX® 系统) 中， 内核用以决定一个进程是能够完成一项动作的两个因素是它所属的用户 ID 和组 ID。 与用户 ID 不同， 每个进程都有一个和它相关联的组的列表。 您可能听说过用户或进程的 “组 ID”； 大多数情况下， 这表示列表中的第一个组。

与组 ID 对应的组名在`/etc/group`中。 这是一个由冒号来界定的文本文件。 第一部分是组名， 第二部分是加密后的口令， 第三部分是组 ID， 第四部分是以逗号相隔的成员列表。 它可以用手工方式进行编辑 (当然， 如果您能保证不出语法错误的话！)。 对于更完整的语法描述， 参见 [group(5)](http://www.FreeBSD.org/cgi/man.cgi?query=group&sektion=5&manpath=freebsd-release-ports) 联机手册.

如果不想手工编辑 `/etc/group`， 也可以使用 [pw(8)](http://www.FreeBSD.org/cgi/man.cgi?query=pw&sektion=8&manpath=freebsd-release-ports) 添加和编辑组。 例如， 要添加一个叫 `teamtwo` 的组， 确定它存在：

例 14.7. 使用[pw(8)](http://www.FreeBSD.org/cgi/man.cgi?query=pw&sektion=8&manpath=freebsd-release-ports)添加一个组

```
# `pw groupadd teamtwo`
# `pw groupshow teamtwo`
teamtwo:*:1100:
```

上面的数字 `1100` 是组 `teamtwo` 的组 ID。 目前， `teamtwo` 还没有成员， 因此也就没有多大用处。 接下来， 把 `jru` 加入到 `teamtwo` 组。

例 14.8. 使用 [pw(8)](http://www.FreeBSD.org/cgi/man.cgi?query=pw&sektion=8&manpath=freebsd-release-ports) 设置组的成员列表

```
# `pw groupmod teamtwo -M jru`
# `pw groupshow teamtwo`
teamtwo:*:1100:jru
```

`-M` 所需的参数是一个用逗号分隔的组中将要成为成员的用户列表。 前面我们已经知道， 口令文件中， 每个用户已经指定了一个所属组。 之后用户被自动地添加到组列表里； 当我们使用 `groupshow` 命令时 [pw(8)](http://www.FreeBSD.org/cgi/man.cgi?query=pw&sektion=8&manpath=freebsd-release-ports) 用户列表不被显示出来。 但当通过 [id(1)](http://www.FreeBSD.org/cgi/man.cgi?query=id&sektion=1&manpath=freebsd-release-ports) 或者类似工具查看时， 就会看到用户列表。 换言之， [pw(8)](http://www.FreeBSD.org/cgi/man.cgi?query=pw&sektion=8&manpath=freebsd-release-ports) 命令只能读取 `/etc/group` 文件； 它从不尝试从 `/etc/passwd` 文件读取更多信息。

例 14.9. 使用 [pw(8)](http://www.FreeBSD.org/cgi/man.cgi?query=pw&sektion=8&manpath=freebsd-release-ports) 为组添加新的成员

```
# `pw groupmod teamtwo -m db`
# `pw groupshow teamtwo`
teamtwo:*:1100:jru,db
```

`-m` 选项的参数是一个由逗号分隔的即将被添加进组的用户列表。 与先前那个例子的不同之处在于， 这个列表中的用户将被添加进组而非取代组中的现有用户。

例 14.10. 使用[id(1)](http://www.FreeBSD.org/cgi/man.cgi?query=id&sektion=1&manpath=freebsd-release-ports)来决定组成员

```
% `id jru`
uid=1001(jru) gid=1001(jru) groups=1001(jru), 1100(teamtwo)
```

正如您所看到的， `jru` 是组 `jru` 和组 `teamtwo`的成员.

有关[pw(8)](http://www.FreeBSD.org/cgi/man.cgi?query=pw&sektion=8&manpath=freebsd-release-ports)的更多信息， 请参看其它联机手册。 更多的关于 `/etc/group` 文件格式的信息， 请参考 [group(5)](http://www.FreeBSD.org/cgi/man.cgi?query=group&sektion=5&manpath=freebsd-release-ports) 联机手册。

## 第 15 章 安全

这一章的许多内容来自 security(7) 联机手册，其作者是 Matthew Dillon.

## 15.1. 概述

这一章将对系统安全的基本概念进行介绍， 除此之外， 还将介绍一些好的习惯， 以及 FreeBSD 下的一些更深入的话题。 这章的许多内容对于一般的系统和 Internet 安全也适用。 如今， Internet 已经不再像以前那样是一个人人都愿意与您作好邻居的 “友善” 的地方。 让系统更加安全， 将保护您的数据、 智力财产、 时间， 以及其他很多东西不至于被入侵者或心存恶意的人所窃取。

FreeBSD 提供了一系列工具和机制来保证您的系统和网络的完整及安全。

读完这章，您将了解：

*   基本的 FreeBSD 系统安全概念。

*   FreeBSD 中众多可用的密码学设施，例如 DES 和 MD5。

*   如何设置一次性口令验证机制。

*   如何配置 TCP Wrappers 以便与 inetd 配合使用。

*   如何在 FreeBSD 上设置 Kerberos5。

*   如何配置 IPsec 并在 FreeBSD/Windows® 机器之间建构 VPN。

*   如何配置并使用 OpenSSH，以及 FreeBSD 的 SSH 执行方式。

*   系统 ACL 的概念，以及如何使用它们。

*   如何使用 Portaudit 工具来审核从 Ports Collection 安装的第三方软件包的安全性。

*   如何从 FreeBSD 的安全公告中获得有用信息并采取相应措施。

*   对于进程记帐功能的感性认识， 并了解如何在 FreeBSD 中启用它。

在开始阅读这章之前，您需要：

*   理解基本的 FreeBSD 和 Internet 概念。

其他安全方面的话题， 则贯穿本书的始终。 例如， 强制性访问控制 (MAC) 在 第 17 章 *强制访问控制* 中进行了介绍， 而 Internet 防火墙则在 第 31 章 *防火墙* 中进行了讨论。

## 15.2. 介绍

安全是系统管理员自始至终的基本要求。 由于所有的 BSD UNIX® 多用户系统都提供了与生俱来的安全性， 因此建立和维护额外的安全机制， 确保用户的 “诚实” 可能也就是最需要系统管理员考虑的艰巨的工作了。 机器的安全性取决于您设置的安全设施， 而许多安全方面的考虑， 则会与人们使用计算机时的便利性相矛盾。 一般来说， UNIX® 系统能够胜任数目众多进程并发地处理各类任务， 这其中的许多进程是以服务身份运行的 ── 这意味着， 外部实体能够与它们互联并产生会话交互。 如今的桌面系统， 已经能够达到许多昔日的小型机甚至主机的性能， 而随着这些计算机的联网和在更大范围内完成互联， 安全也成为了一个日益严峻的课题。

系统的安全也应能够应付各种形式的攻击， 这也包括那些使系统崩溃， 或阻止其正常运转， 并不仅限于试图窃取 `root` 帐号 (“破译 root”) 的攻击形式。 安全问题大体可分为以下几类：

1.  拒绝服务攻击。

2.  窃取其他用户的帐户。

3.  通过可访问服务窃取 root 帐户。

4.  通过用户帐户窃取 root 帐户。

5.  建立后门。

拒绝式服务攻击是侵占机器所需资源的一种行为。 通常， DoS 攻击采用暴力(brute-force)手段通过压倒性的流量来破坏服务器和网络栈， 以使机器崩溃或无法使用。 某些 DoS 攻击则利用在网络栈中的错误， 仅用一个简单的信息包就可以让机器崩溃， 这类情况通常只能通过给内核打补丁来修复。 在一些不利的条件下， 对服务器的攻击能够被修复， 只要适当地修改一下系统的选项来限制系统对服务器的负荷。 顽强的网络攻击是很难对付的。 例如，一个欺骗性信息包的攻击， 无法阻止入侵者切断您的系统与 Internet 的连接。 它不会使您的机器死掉，但它会把 Internet 连接占满。

窃取用户帐户要比 D.o.S.攻击更加普遍。 许多系统管理员仍然在他们的服务器上运行着基本的 telnetd，rlogind， rshd 和 ftpd 服务。 这些服务在默认情况下不会以加密连接来操作。 结果是如果您的系统有中等规模大小的用户群， 在通过远程登录的方式登录到您系统的用户中， 一些人的口令会被人窃取。 仔细的系统管理员会从那些成功登录系统的远程访问日志中寻找可疑的源地址。

通常必须假定，如果一个入侵者已经访问到了一个用户的帐户， 那么它就可能使自己成为 `root`。 然而， 事实是在一个安全和维护做得很好的系统中， 访问用户的帐户不一定会让入侵者成为 `root`。 这个差别是很重要的，因为没有成为 `root` 则入侵者通常是无法隐藏它的轨迹的， 而且， 如果走运的话， 除了让用户的文件乱掉和系统崩溃之外， 它不能做什么别的事情。 窃取用户帐户是很普遍的事情， 因为用户往往不会对系统管理员的警告采取措施。

系统管理员必须牢牢记住，可能有许多潜在的方法会使他们机器上的 `root` 用户受到威胁。入侵者可能知道 `root` 的口令，而如果在以 `root` 权限运行的服务器上找到一个缺陷 (bug)， 就可以通过网络连接到那台服务器上达到目的；另外， 一旦入侵者已经侵入了一个用户的帐户， 可以在自己的机器上运行一个 suid-root 程序来发现服务器的漏洞， 从而让他侵入到服务器并获取 `root`。 攻击者找到了入侵一台机器上 `root` 的途径之后， 他们就不再需要安装后门了。许多 `root` 漏洞被发现并修正之后， 入侵者会想尽办法去删除日志来消除自己的访问痕迹， 所以他们会安装后门。 后门能给入侵者提供一个简单的方法来重新获取访问系统的 `root` 权限， 但它也会给聪明的系统管理员一个检测入侵的简便方法。 让入侵者无法安装后门事实上对您的系统安全是有害的， 因为这样并不会修复那些侵入系统的入侵者所发现的新漏洞。

安全的管理方法应当使用像 “洋葱皮” 一样多层次的方法来实现， 这些措施可以按下面的方式进行分类：

1.  确保 `root` 和维护人员帐户的安全。

2.  确保 `root` – 以 root 用户权限运行的服务器和 suid/sgid 可执行程序的安全。

3.  确保用户帐户的安全。

4.  确保口令文件的安全。

5.  确保内核中核心组件、直接访问设备和文件系统的安全。

6.  快速检测系统中发生的不适当的变化。

7.  做个偏执狂。

这一章的下一节将比较深入地讲述上面提到的每一个条目。

## 15.3. 确保 FreeBSD 的安全

### 命令与协议:

在这份文档中，我们使用 粗体 来表示应用程序， 并使用 `单倍距` 字体来表示命令。 这样的排版区分能够有效地区分类似 ssh 这样的概念， 因为它既可以表示命令，又可以表示协议。

接下来的几节中， 将介绍在这一章中 前一节 中所介绍的那些加强 FreeBSD 系统安全性的手段。

### 15.3.1. 确保 `root` 和维护人员帐户的安全

首先，如果您没有确保 `root` 帐户的安全， 就没必要先劳神确保用户帐户的安全了。绝大多数系统都会指派一个口令给 `root` 帐户。 我们的第一个假定是，口令 *总是* 不安全的。 这并不意味着您要把口令删掉。 口令通常对访问机器的控制台来说是必须的。 也就是说， 您应该避免允许在控制台以外的地方使用口令， 甚至包括使用 [su(1)](http://www.FreeBSD.org/cgi/man.cgi?query=su&sektion=1&manpath=freebsd-release-ports) 命令的情形。 例如，确信您的 pty 终端在 `/etc/ttys` 文件中被指定为 insecure (不安全)，这将使直接通过 `telnet` 或 `rlogin` 登录 `root` 会不被接受。 如果使用如 sshd 这样的其他登录服务， 也要确认直接登录 root 是关闭的。您可以通过编辑 `/etc/ssh/sshd_config` 文件来做到这一点，确信 `PermitRootLogin` 被设置成 `no`。 考虑到每一种访问方法 ── 如 FTP 这样的服务， 以免因为它们而导致安全性的损失。 直接登录 `root` 只有通过系统控制台才被允许。

当然， 作为一个系统管理员， 您应当获得 `root`身份， 因此， 我们开了一些后门来允许自己进入。 但这些后门只有在经过了额外的口令确认之后才能使用。 一种让 `root` 可访问的方法是增加适当的用户帐户到 `wheel` 组 (在 `/etc/group` 中)。`wheel` 组中的用户成员可以使用 `su` 命令来成为 `root`。 绝对不应该通过在口令项中进行设置来赋予维护人员天然的 `wheel` 组成员身份。 维护人员应被放置在 `staff` 组中，然后通过 `/etc/group` 文件加入到 `wheel` 组。事实上，只有那些需要以 `root` 身份进行操作的用户才需要放进 `wheel` 组中。 当然，也可以通过 某种其它的验证手段，例如 Kerberos，可以通过 `root` 帐户中的 `.k5login` 文件来允许执行 [ksu(1)](http://www.FreeBSD.org/cgi/man.cgi?query=ksu&sektion=1&manpath=freebsd-release-ports) 成为 `root` ，而不必把它们放进 `wheel` 组。 这可能是一种更好的解决方案， 因为 `wheel` 机制仍然可能导致入侵者获得 `root` ，如果他拿到了口令文件，并能够进入职员的帐户。 尽管有 `wheel` 比什么都没有要强一些， 但它并不是一种绝对安全的办法。

可以使用 [pw(8)](http://www.FreeBSD.org/cgi/man.cgi?query=pw&sektion=8&manpath=freebsd-release-ports) 命令来完全禁止某一个帐号：

```
#`pw lock staff`
```

这将阻止用户使用任何方法登录，包括 [ssh(1)](http://www.FreeBSD.org/cgi/man.cgi?query=ssh&sektion=1&manpath=freebsd-release-ports)。

另一个阻止某个帐户访问的方法是使用一个 “`*`” 字符替换掉加密后的口令。 这将不会与任何加密后的口令匹配，从而阻止了用户的访问。 举例说明：

```
foobar:R9DT/Fa1/LV9U:1000:1000::0:0:Foo Bar:/home/foobar:/usr/local/bin/tcsh
```

应被改为：

```
foobar:*:1000:1000::0:0:Foo Bar:/home/foobar:/usr/local/bin/tcsh
```

这会阻止用户 `foobar` 使用传统的方式登录。 但是对于使用了 Kerberos 或者配置了 [ssh(1)](http://www.FreeBSD.org/cgi/man.cgi?query=ssh&sektion=1&manpath=freebsd-release-ports) 公钥/密钥对的情况下，用户依然可以访问。

这些安全机制同样假定， 从严格受限的机器向限制更宽松的机器上登录。 例如， 如果您的服务器运行了所有的服务，那么，工作站应该什么都不运行。 为了让工作站尽可能地安全，应该避免运行任何没有必要的服务， 甚至不运行任何服务。 另外， 也应该考虑使用带口令保护功能的屏幕保护程序。 毋庸置疑， 如果攻击者能够物理地接触您的工作站， 那么他就有能力破坏任何安全设施，这确实是我们需要考虑的一个问题，但同样地， 真正能够物理接触您的工作站或服务器并实施攻击的人在现实生活中并不常见， 绝大多数攻击来自于网络， 而攻击者往往无法物理地接触服务器或工作站。

使用类似 Kerberos 这样的工具，也为我们提供了使用一个工具来禁用某个用户， 或修改他们的口令， 并在所有机器上立即生效的方法。 如果员工的帐号被窃取， 能够在所有的其他机器上生效的口令变更将很有意义。如果口令分散地保存在多个机器上， 一次修改 N 台机器上的口令很可能是一件痛苦的事情。 此外， Kerberos 还能够提供更多的限制，除了 Kerberos 令牌有很好的过期机制之外， 它还能够强制用户在某个特定的期限内修改口令(比如说，每月一次).

### 15.3.2. 确保以 root 用户权限运行的服务器和 suid/sgid 可执行程序的安全

谨慎的管理员只运行他们需要的服务， 不多， 不少。 要当心第三方的服务程序很可能有更多的问题。 例如， 运行旧版的 imapd 或 popper 无异于将 `root` 令牌拱手送给全世界的攻击者。 永远不要运行那些您没有仔细检查过的服务程序， 另外也要知道， 许多服务程序并不需要以 `root` 的身份运行。 例如， ntalk、 comsat， 以及 finger 这些服务， 都能够以一种被称作 *沙盒* 的特殊用户的身份运行。 除非您已经解决掉了许多麻烦的问题， 否则沙盒就不是完美的， 但洋葱式安全规则仍然成立： 如果有人设法攻破了在沙盒中运行的程序， 那么在做更多坏事之前， 他们还必须想办法攻破沙盒本身的限制。 攻击者需要攻破的层次越多， 他们成功的可能性就越小。 过去， 破解 root 的漏洞几乎在所有以 `root` 身份运行的服务上都发现过， 包括那些基本的系统服务。 如果您的机器只打算向外界提供 sshd 登录， 而用户不会使用 telnetd 或 rshd 甚至 rlogind 登录， 就应该毫不犹豫地关闭它们！

FreeBSD 现在默认在沙盒中运行 ntalkd, comsat, 以及 finger。此外， [named(8)](http://www.FreeBSD.org/cgi/man.cgi?query=named&sektion=8&manpath=freebsd-release-ports) 也可以这样运行。 `/etc/defaults/rc.conf` 中包括了如何如此运行 named 的方法，只是这些内容被注释掉了。 如何升级或安装系统将决定这些沙盒所使用的特殊用户是否被自动安装。 谨慎的系统管理员将根据需要研究并实现沙盒。

此外，还有一些服务通常并不在沙盒中运行： sendmail, popper, imapd, ftpd, 以及一些其他的服务。当然，它们有一些替代品，但安装那些服务可能需要做更多额外的工作。 可能必须以 `root` 身份运行这些程序， 并通过其他机制来检测入侵。

系统中另一个比较大的 `root` 漏洞 是安装在其中的 suid-root 和 sgid 的可执行文件。 绝大多数这类程序， 例如 rlogin 会放在 `/bin`、 `/sbin`、 `/usr/bin`， 或 `/usr/sbin` 目录中。 尽管并没有 100% 的安全保证，但系统默认的 suid 和 sgid 可执行文件通常是相对安全的。 当然，偶尔也会发现一些存在于这些可执行文件中的 `root` 漏洞。1998 年，`Xlib` 中发现了一处 `root` 漏洞，这使得 xterm (通常是做了 suid 的) 变得可以入侵。 做得安全些， 总比出现问题再后悔要强。 因此，谨慎的管理员通常会限制 suid 可执行文件， 并保证只有员工帐号能够执行它们，或只开放给特定的用户组，甚至彻底干掉 (`chmod 000`) 任何 suid 可执行文件， 以至于没有人能够执行它们。没有显示设备的服务器通常不会需要 xterm 可执行文件。 sgid 可执行文件通常同样地危险。 一旦入侵者攻克了 sgid-kmem，那么他就能够读取 `/dev/kmem` 并进而读取经过加密的口令文件， 从而窃取任何包含口令的帐号。另外，攻破了 `kmem` 的入侵者能够监视通过 pty 传送的按键序列，即使用户使用的是安全的登录方式。 攻破了 `tty` 组的用户则能够向几乎所有用户的 tty 写入数据。如果用户正在运行一个终端程序，或包含了键盘模拟功能的终端仿真程序， 那么，入侵者能够以那个用户的身份执行任何命令。

### 15.3.3. 确保用户帐户的安全

用户帐号的安全通常是最难保证的。虽然您可以为您的员工设置严苛的登录限制， 并用 “星号” 替换掉他们的口令， 但您可能无法对普通的用户这么做。 如果有足够的决策权， 那么在保证用户帐号安全的斗争中或许会处于优势， 但如果不是这样， 您能做的只是警惕地监控这些帐号的异动。 让用户使用 ssh 或 Kerberos 可能会有更多的问题， 因为需要更多的管理和技术支持， 尽管如此， 与使用加密的口令文件相比， 这仍不失为一个好办法。

### 15.3.4. 确保口令文件的安全

能够确保起作用的唯一一种方法， 是将口令文件中尽可能多的口令用星号代替， 并通过 ssh 或 Kerberos 来使用这些账号。 即使只有 `root` 用户能够读取加密过的口令文件 (`/etc/spwd.db`)， 入侵者仍然可能设法读到它的内容， 即使他暂时还无法写入这个文件。

您的安全脚本应该经常检查并报告口令文件的异动 (参见后面的 检查文件完整性 一节)。

### 15.3.5. 确保内核中内核设备、直接访问设备和文件系统的安全

如果攻击者已经拿到了 `root` 那么他就有能力作任何事情， 当然， 有一些事情是他们比较喜欢干的。 例如， 绝大多数现代的内核都包括一个内建的听包设备。 在 FreeBSD 中，这个设备被称作 `bpf` 。攻击者通常会尝试在攻克的系统上运行它。 如果您不需要 `bpf` 设备提供的功能，那么，就不要把它编入内核。

但是， 即使您关闭了 `bpf` 设备， 仍需要关注 `/dev/mem` 和 `/dev/kmem`。 就事论事地说， 入侵者仍然能通过直接访问的方式写入磁盘设备。 另外， 还有一个称作模块加载器的内核机制， [kldload(8)](http://www.FreeBSD.org/cgi/man.cgi?query=kldload&sektion=8&manpath=freebsd-release-ports)。 有进取心的入侵者， 可以经由这一机制， 在正在运行的内核中通过 KLD 模块来安装自己的 `bpf`， 或其它听包设备。 为了避免这些问题， 您必须将内核的安全级别提高到至少 1。

内核的安全级别可以通过多种方式来设置。 最简单的设置正在运行的内核安全级的方法， 是使用 `sysctl` 来设置内核变量 `kern.securelevel`：

```
# `sysctl kern.securelevel=1`
```

默认情况下， FreeBSD 内核启动时的安全级别是 -1。 除非管理员或 [init(8)](http://www.FreeBSD.org/cgi/man.cgi?query=init&sektion=8&manpath=freebsd-release-ports) 由于启动脚本加以改变， 安全级别会继续保持为 -1。 在系统启动过程中， 可以在 `/etc/rc.conf` 文件中， 将变量 `kern_securelevel_enable` 变量设置为 `YES` 并将 `kern_securelevel` 变量设置为希望的安全级别来提高它。

默认情况下， 在启动脚本执行完之后， FreeBSD 的安全级别设置是 -1。 这称作 “不安全模式”， 因为文件的不可修改标记 (immutable flag) 可以改为关闭， 而且全部设备可以直接进行读写， 等等。

一旦将安全级别设置为 1 或更高， 则只允许追加 (append-only) 和不可修改标记会被执行， 而且不可以关闭。 直接访问裸设备则会被拒绝。 更高的安全级别会施加进一步的访问限制。 关于安全级别的完整介绍， 请参阅联机手册 [security(7)](http://www.FreeBSD.org/cgi/man.cgi?query=security&sektion=7&manpath=freebsd-release-ports) (对于 FreeBSD 7.0 之前的版本， 则是联机手册 [init(8)](http://www.FreeBSD.org/cgi/man.cgi?query=init&sektion=8&manpath=freebsd-release-ports))。

### 注意:

将安全级别调整到 1 或更高可能会导致 X11 (访问 `/dev/io` 会被阻止)， 或从源代码联编 FreeBSD (这一过程中的 `installworld` 部分需要临时取消一些文件上的只允许追加和不可修改标记) 出现一些问题， 并导致一些其他小问题。 有些时候， 例如 X11 的情况， 可以通过在引导过程中较早的阶段启动 [xdm(1)](http://www.FreeBSD.org/cgi/man.cgi?query=xdm&sektion=1&manpath=freebsd-release-ports) 来绕过， 因为这时安全级别还很低。 类似这样的方法， 对于某些安全级别或限制有可能不可用。 提前做好计划可能会是个好主意。 理解不同的安全级别所施加的限制非常重要， 因为一些限制可能让系统变得很难使用。 另外， 了解它们也有助于理性地配置默认设定。

如果内核的安全级别设为 1 或更高， 在重要的启动程序、 目录和脚本文件上设置 `schg` 标记 (也就是在系统启动到设置安全级别之前运行的程序和它们的配置) 就有意义了。 然而， 这样做也可能有些过火， 而由于系统运行于较高的安全级别， 升级系统也会变得困难的多。 作为妥协， 可以让系统以较高的安全级别运行， 但并不将所有的启动文件都配置 `schg` 标记。 另一种方法是将 `/` 和 `/usr` 以只读模式挂载。 请注意， 过分严苛的安全配置很可能限制您检测入侵的能力。

### 15.3.6. 检查文件完整性: 可执行文件，配置文件和其他文件

当实施严格的限制时，往往会在使用的方便性上付出代价。例如，使用 `chflags` 来把 `schg` 标记 应用到 `/` 和 `/usr` 中的绝大多数文件上可能会起到反作用， 因为尽管它能够保护那些文件， 但同时也使入侵检测无法进行。 层次化安全的最后一层可能也是最重要的 ── 检测。 如果无法检测出潜在的入侵行为， 那么安全的其他部分可能相对来讲意义可能就不那么大了 (或者，更糟糕的事情是， 那些措施会给您安全的假象)。 层次化安全最重要的功能是减缓入侵者， 而不是彻底不让他们入侵， 这样才可能当场抓住入侵者。

检测入侵的一种好办法是查找那些被修改、 删除或添加的文件。 检测文件修改的最佳方法是与某个 (通常是中央的) 受限访问的系统上的文件进行比对。 在一台严格限制访问的系统上撰写您的安全脚本通常不能够被入侵者察觉， 因此，这非常重要。为了最大限度地发挥这一策略的优势，通常会使用只读的 NFS， 或者设置 ssh 钥匙对以便为其他机器提供访问。除了网络交互之外， NFS 可能是一种很难被察觉的方法 ── 它允许您监控每一台客户机上的文件系统， 而这种监控几乎是无法察觉的。如果一台严格受限的服务器和客户机是通过交换机连接的， 那么 NFS 将是一种非常好的方式。 不过，如果那台监控服务器和客户机之间通过集线器 (Hub)，或经过许多层的路由来连接，则这种方式就很不安全了， 此时，应考虑使用 ssh ，即使这可以在审计记录中查到。

一旦为这个受限的机器赋予了至少读取它应监控的客户系统的权限， 就应该为实际的监控撰写脚本。以 NFS 挂接为例，可以用类似 [find(1)](http://www.FreeBSD.org/cgi/man.cgi?query=find&sektion=1&manpath=freebsd-release-ports) 和 [md5(1)](http://www.FreeBSD.org/cgi/man.cgi?query=md5&sektion=1&manpath=freebsd-release-ports) 这样的命令为基础来完成我们所需的工作。 最好能够每天对被控机的所有执行文件计算一遍 md5，同时，还应以更高的频率测试那些 `/etc` 和 `/usr/local/etc` 中的控制文件。一旦发现了不匹配的情形，监控机应立即通知系统管理员。 好的安全脚本也应该检查在系统分区，如 `/` 和 `/usr` 中是否有新增或删除的可执行文件，以及不适宜的 suid 。

如果打算使用 ssh 来代替 NFS，那么撰写安全脚本将变得困难许多。 本质上，需要在脚本中使用 `scp` 在客户端复制文件， 另一方面，用于检查的执行文件 (例如 find) 也需要使用 `scp` 传到客户端，因为 ssh 客户程序很可能已经被攻陷。 总之，在一条不够安全的链路上 ssh 可能是必须的， 但也必须应付它所带来的难题。

安全脚本还应该检查用户以及职员成员的权限设置文件： `.rhosts`、 `.shosts`、 `.ssh/authorized_keys` 等等。 这些文件可能并非通过 `MD5` 来进行检查。

如果您的用户磁盘空间很大， 检查这种分区上面的文件可能非常耗时。 这种情况下， 采用标志来禁止使用 suid 可执行文件将是一个好主意。 您可能会想看看 `nosuid` 选项 （参见 [mount(8)](http://www.FreeBSD.org/cgi/man.cgi?query=mount&sektion=8&manpath=freebsd-release-ports)）。 尽管如此， 这些扫描仍然应该至少每周进行一次， 这样做的意义并不是检测有效的攻击， 而是检查攻击企图。

进程记帐 (参见 [accton(8)](http://www.FreeBSD.org/cgi/man.cgi?query=accton&sektion=8&manpath=freebsd-release-ports)) 是一种相对成本较低的， 可以帮助您在被入侵后评估损失的机制。 对于找出入侵者是如何进入系统的这件事情来说， 它会非常的有所助益，特别是当入侵者什么文件都没有修改的情况下。

最后， 安全脚本应该处理日志文件， 而日志文件本身应该通过尽可能安全的方法生成 ── 远程 syslog 可能非常有用。 入侵者会试图掩盖他们的踪迹， 而日志文件对于希望了解入侵发生时间的系统管理员来说则显得尤为重要。 保持日志文件的永久性记录的一种方法是在串口上运行系统控制台， 并在一台安全的机器上收集这些信息。

### 15.3.7. 偏执

带点偏执不会带来伤害。作为一种惯例， 系统管理员在不影响使用的便利的前提下可以启用任何安全特性，此外， 在经过深思熟虑之后，也可以增加一些 *确实会* 让使用变得不那么方便的安全特性。 更重要的是，有安全意识的管理员应该学会混合不同的安全策略 ── 如果您逐字逐句地按照这份文档来配置您的机器， 那无异于向那些同样能得到这份文档的攻击者透露了更多的信息。

### 15.3.8. 拒绝服务攻击

这一节将介绍拒绝服务攻击。 DoS 攻击通常是基于数据包的攻击， 尽管几乎没有任何办法来阻止大量的伪造数据包耗尽网络资源， 但通常可以通过一些手段来限制这类攻击的损害，使它们无法击垮服务器：

1.  限制服务进程 fork。

2.  限制 springboard 攻击 (ICMP 响应攻击， ping 广播，等等)。

3.  使内核路由缓存过载。

一种比较常见的 DoS 攻击情形， 是通过攻击复制进程 (fork) 的服务， 使其产生大量子进程， 从而是其运行的机器耗尽内存、 文件描述符等资源， 直到服务器彻底死掉。 inetd (参见 [inetd(8)](http://www.FreeBSD.org/cgi/man.cgi?query=inetd&sektion=8&manpath=freebsd-release-ports)) 提供了许多选项来限制这类攻击。 需要注意的是， 尽管能够阻止一台机器彻底垮掉， 但通常无法防止服务本身被击垮。 请仔细阅读 inetd 的联机手册， 特别是它的 `-c`、 `-C` 以及 `-R` 这三个选项。 伪造 IP 攻击能够绕过 inetd 的 `-C` 选项， 因此， 这些选项需要配合使用。 某些独立的服务器也有类似的限制参数。

例如， Sendmail 就提供了自己的 `-OMaxDaemonChildren` 选项， 它通常比 Sendmail 的负载限制选项更为有效， 因为服务器负载的计算有滞后性。 您可以在启动 sendmail 时指定一个 `MaxDaemonChildren` 参数； 把它设的足够高以便承载您所需要的负荷， 当然， 不要高到足以让运行 Sendmail 的机器死掉。 此外， 以队列模式 (`-ODeliveryMode=queued`) 运行 Sendmail 并把服务程序 (`sendmail -bd`) 和队列执行程序分别执行 (`sendmail -q15m`) 也是一个好主意。 如果您希望保证队列的实时性， 可以考虑使用更短的间隔， 例如 `-q1m`， 但同时也需要指定一个合理的子进程数， 也就是通过 `MaxDaemonChildren` 选项以免 *那个* Sendmail 造成重叠的故障。

Syslogd 可以被直接地攻击，因此， 强烈建议只要可行，就在启动它的时候加上 `-s` 参数， 其他情况下，则至少应该加上 `-a`。

对于基于连接的服务，例如 TCP Wrapper 的 reverse-identd， 都应该格外的小心， 因为它们都可能直接遭受攻击。 一般情况下， 基于安全考虑， 不应使用 TCP Wrapper 所提供的 reverse-ident 这样的功能。

此外， 将内部服务保护起来， 阻止来自其他主机的访问也十分重要， 这些工作可以通过设置边界路由器来完成。 主要的想法， 是阻止来自您的 LAN 以外的访问， 这有助于避免 `root` 受到攻击。 尽可能配置排他式的防火墙， 例如， “用防火墙阻止所有的网络流量 *除了* 端口 A、B、 C、D，以及 M-Z”。 通过采用这种方法， 您可以很容易地将低端口的访问阻止在外， 而又不难配置使防火墙放过那些明确需要开放的服务， 例如 named (如果您的机器准备作为域的主要解析服务器)， ntalkd， sendmail，以及其他可以从 Internet 访问的服务。 如果您尝试以其他方式配置防火墙 ── 采用比较宽松的策略， 那么您将很有可能忘记 “关掉” 一两个服务， 或者在增加了一些服务之后忘记更新防火墙策略。 尽管如此， 仍然可以考虑允许让数据进入编号较高的那一部分端口， 这将保证那些需要这样特性的服务能够正常工作， 而又不影响低端口服务的安全性。 此外， 还应注意到 FreeBSD 允许您来控制动态绑定的端口的范围， 即一系列 `net.inet.ip.portrange` 变量，通过 `sysctl` 来完成设置。 (`sysctl -a | fgrep portrange`)。 这使得您完成较复杂的防火墙策略变得易如反掌。 例如， 您可能希望普通的高段端口的起止范围是 4000 到 5000， 而更高范围则是 49152 到 65535， 随后在防火墙中阻止低于 4000 的所有端口 (当然， 除了那些特地为 Internet 访问而开设的端口)。

另一种常被称作 springboard 的攻击也是非常常见的 DoS 攻击 ── 它通过使服务器产生其无法处理的响应来达到目的。 最常见的攻击就是 *ICMP ping 广播攻击*。 攻击者通过伪造 ping 包， 将其源 IP 设置为希望攻击的机器的 IP。 如果您的边界路由器没有进行禁止 ping 广播地址的设置， 则您的网络将最终陷于响应伪造的 ping 包之中， 特别是当攻击者同时使用了多个不同的网络时。 广播攻击能够产生超过 120 兆位的瞬时流量。 另一种常见的针对 ICMP 错误报告系统的 springboard 攻击， 通过建立可以生成 ICMP 出错响应的包， 攻击者能够攻击服务器的网络下行资源， 并导致其上行资源耗尽。 这种类型的攻击也可以通过耗尽内存来使得使得被攻击的服务器崩溃， 特别是当这些服务器无法足够快地完成 ICMP 响应的时候。 较新的内核可以通过调整 sysctl 变量 `net.inet.icmp.icmplim` 来限制这种攻击。 最后一类主要的 springboard 是针对某些 inetd 的内部服务， 例如 udp echo 服务进行的。 攻击者简单地伪造一个来自服务器 A 的 echo 口的 UDP 包， 然后将这个包发到 B 的 echo 口。 于是， 两台服务器将不停地将包弹给对方。 攻击者能够将两台服务器的这种服务都耗竭， 并且通过这种方式， 只需要很少的包就可以让 LAN 超载。 类似的问题对 chargen 口也是存在的。 好的系统管理员应该关闭这些 inetd 的测试服务。

伪造的包攻击也可以用来使内核的路由缓存过载。 请参考 `net.inet.ip.rtexpire`， `rtminexpire`， 以及 `rtmaxcache` `sysctl` 参数。 伪造的包可以用随机的源 IP 攻击， 使得内核在路由表中产生一个临时的缓存项， 它可以通过 `netstat -rna | fgrep W3` 看到。 这些路由通常需要 1600 秒才会过期。 如果内核发现路由表变得太大， 它会动态地降低 `rtexpire` 但以 `rtminexpire` 为限。 这引发了两个问题：

1.  在访问量不大的服务器上， 内核对于突然袭击的反应不够快。

2.  `rtminexpire` 的值没有低到让内核在此类攻击时活下去的程度。

如果您的服务器通过 T3 或更快的线路接入 Internet， 那么通过 [sysctl(8)](http://www.FreeBSD.org/cgi/man.cgi?query=sysctl&sektion=8&manpath=freebsd-release-ports) 来手动地降低 `rtexpire` 和 `rtminexpire` 就非常必要。 当然，绝不要把它们设置为零 (除非您想让机器崩溃) 将这两个参数设置为 2 通常已经足以抵御这类攻击了。

### 15.3.9. Kerberos 和 SSH 的访问问题

如果您打算使用， 那么 Kerberos 和 ssh 都有一些需要解决的问题。 Kerberos 5 是一个很棒的验证协议， 但使用了它的 telnet 和 rlogin 应用程序有一些 bug， 使得它们不适合处理二进制流。 而且， 除非使用了 `-x` 选项， 否则默认情况下 Kerberos 并不加密会话。 ssh 在默认时加密所有的会话内容。

除了默认转发加密密钥之外， ssh 在所有的其他方面都做得很好。 这意味着如果您持有供您访问系统其他部分密钥的工作站作了很好的安全防护， 而您连到了一台不安全的机器上， 则您的密钥可能被别人获得。 尽管实际的密钥并没有被泄漏， 但由于 ssh 会在您登录的过程中启用一个转发端口， 如果攻击者拿到那台不安全的机器上的 `root` 那么他将能够利用那个端口来使用您的密钥， 从而访问您能够访问的那些机器。

我们建议您在使用 ssh 时配合 Kerberos 来完成工作人员的登录过程。 Ssh 在编译时可以加入 Kerberos 支持。 在减少了潜在地暴露 ssh 密钥的机会的同时， 它还能够通过 Kerberos 来保护口令。 Ssh 密钥只有在做过安全防护的机器上执行自动操作时才应使用 (这是 Kerberos 不适合的情形)。 此外，我们还建议您要么在 ssh 配置中关闭密钥转发， 要么在 `authorized_keys` 中增加 `from=IP/DOMAIN` 选项， 来限制这些密钥能够登录的来源机器。

## 15.4. DES、 Blowfish、 MD5， 以及 Crypt

部分重写、更新来自 Bill Swingle.

UNIX® 系统上的每个用户都有一个与其帐户关联的口令。 很显然， 密码只需要被这个用户和操作系统知道。 为了保证口令的私密性， 采用了一种称为 “单向散列” 的方法来处理口令， 简单地说， 很容易从口令推算出散列值， 反之却很难。 其实， 刚才那句话可能并不十分确切： 因为操作系统本身并不 *真的* 知道您的口令。 它只知道口令 *经过加密的形式*。 获取口令对应 “明文” 的唯一办法是采用暴力在口令可能的区间内穷举。

不幸的是，当 UNIX® 刚刚出现时，安全地加密口令的唯一方法基于 DES， 数据加密标准 ( the Data Encryption Standard )。 于是这给那些非美国居民带来了问题， 因为 DES 的源代码在当时不能被出口到美国以外的地方， FreeBSD 必须找到符合美国法律，但又要与其他那些使用 DES 的 UNIX® 版本兼容的办法。

解决方案是把加密函数库分割为两个， 于是美国的用户可以安装并使用 DES 函数库， 而国际用户则使用另外一套库提供的一种可以出口的加密算法。 这就是 FreeBSD 为什么使用 MD5 作为它的默认加密算法的原因。 MD5 据信要比 DES 更安全，因此，安装 DES 更多地是出于兼容目的。

### 15.4.1. 识别您采用的加密算法

现在这个库支持 DES、 MD5 和 Blowfish 散列函数。默认情况下， FreeBSD 使用 MD5 来加密口令。

可以很容易地识别 FreeBSD 使用哪种加密方法。 检查 `/etc/master.passwd` 文件中的加密密码是一种方法。 用 MD5 散列加密的密码通常要比用 DES 散列得到的长一些， 并且以 `$1$` 字符开始。 以 `$2a$` 开始的口令是通过 Blowfish 散列函数加密的。 DES 密码字符没有任何可以用于鉴别的特征， 但他们要比 MD5 短， 并且以不包括 `$` 在内的 64 个可显示字符来表示， 因此相对比较短的、没有以美元符号开头的字符串很可能是一个 DES 口令。

新口令所使用的密码格式是由 `/etc/login.conf` 中的 `passwd_format` 来控制的， 可供选择的算法包括 `des`, `md5` 和 `blf`。 请参考 [login.conf(5)](http://www.FreeBSD.org/cgi/man.cgi?query=login.conf&sektion=5&manpath=freebsd-release-ports) 联机帮助以获得更进一步的详情。

## 15.5. 一次性口令

默认情况下， FreeBSD 提供了 OPIE (One-time Passwords In Everything) 支持， 它默认使用 MD5 散列。

下面将介绍三种不同的口令。 第一种是您常用的 UNIX® 风格或 Kerberos 口令； 我们在后面的章节中将称其为 “UNIX® 口令”。 第二种是使用 OPIE 的 [opiekey(1)](http://www.FreeBSD.org/cgi/man.cgi?query=opiekey&sektion=1&manpath=freebsd-release-ports) 程序生成， 并为 [opiepasswd(1)](http://www.FreeBSD.org/cgi/man.cgi?query=opiepasswd&sektion=1&manpath=freebsd-release-ports) 以及登录提示所接受的一次性口令，我们称其为 “一次性口令”。 最后一类口令是您输入给 `opiekey` 程序 (有些时候是 `opiepasswd` 程序) 用以产生一次性口令的秘密口令，我们称其为 “秘密口令” 或通俗地简称为 “口令”。

秘密口令和您的 UNIX® 口令毫无关系， 尽管可以设置为相同的， 但不推荐这么做。 OPIE 秘密口令并不像旧式的 UNIX® 口令那样只能限于 8 位以内[^([8])](#ftn.idp78724848)。 您想要用多长的口令都可以。 有六、七个词的短句是很常见的选择。 在绝大多数时候， OPIE 系统和 UNIX® 口令系统完全相互独立地工作。

除了口令之外， 对于 OPIE 还有两组至关重要的数据。 其一被称作 “种子” 或 “key”， 它包括两个字符和五个数字。 另一个被称作 “迭代轮数”， 这是一个 1 到 100 之间的数字。 OPIE 通过将种子加到秘密口令后面， 并执行迭代轮数那么多次的 MD4/MD5 散列运算来得到结果， 并将结果表示为 6 个短的英文单词。 这 6 个英文单词就是您的一次性口令。 验证系统 (主要是 PAM) 会记录上次使用的一次性口令， 如果用户提供的口令的散列值与上次一致， 则可以通过身份验证。 由于使用了单向的散列函数， 因此即使截获了上次使用的口令， 也没有办法恢复出下次将要使用的口令； 每次成功登录都将导致迭代轮数递减， 这样用户和登录程序将保持同步。 每当迭代轮数减少到 1 时， 都必须重新初始化 OPIE。

接下来将讨论和每个系统有关的三个程序。 `opiekey` 程序能够接收带迭代计数， 种子和秘密口令， 并生成一个一次性口令， 或一张包含连续的一组一次性口令的表格。 `opiepasswd` 程序用于初始化 OPIE， 并修改口令、 迭代次数、种子和一次性口令。 和 `opieinfo` 程序可以用于检查相应的验证数据文件 (`/etc/opiekeys`) 并显示执行命令的用户当前的迭代轮数和种子。

我们将介绍四种不同的操作。 在安全的连接上通过 `opiepasswd` 来第一次设置一次性口令， 或修改口令及种子。 第二类操作是在不安全的连接上使用 `opiepasswd` 辅以在安全连接上执行的 `opiekey` 来完成同样的工作。 第三类操作是在不安全的连接上使用 `opiekey` 来登录。 最后一类操作是采用 `opiekey` 来生成大批的密码， 以便抄下来或打印出来，在没有安全连接的地方使用。

### 15.5.1. 安全连接的初始化

第一次初始化 OPIE 时， 可以使用 `opiepasswd` 命令：

```
% `opiepasswd -c`
[grimreaper] ~ $ opiepasswd -f -c
Adding unfurl:
Only use this method from the console; NEVER from remote. If you are using
telnet, xterm, or a dial-in, type ^C now or exit with no password.
Then run opiepasswd without the -c parameter.
Using MD5 to compute responses.
Enter new secret pass phrase:
Again new secret pass phrase:
ID unfurl OTP key is 499 to4268
MOS MALL GOAT ARM AVID COED

```

在 `Enter new secret pass phrase:` 或 `Enter secret password:` 提示之后， 应输入一个密码或口令字。 请留意， 这并不是您用于登录的口令， 它用于生成一次性的登录密钥。 “ID” 这一行给出了所需的参数： 您的登录名， 迭代轮数， 以及种子。 登录系统时， 它能够记住这些参数并呈现给您， 因此无需记忆它们。 最后一行给出了与您的秘密口令对应的、用于登录的一个一次性口令； 如果您立即重新登录， 则它将是您需要使用的那个口令。

### 15.5.2. 不安全连接初始化

如果您需要通过一个不安全的连接来初始化， 则应首先在安全连接上执行过一次 `opiekey`； 您可能希望在可信的机器的 shell 提示符下完成。 此外还需要指定一个迭代轮数 (100 也许是一个较好的选择) 也可以选择一个自己的种子， 或让计算机随机生成一个。 在不安全的连接上 (当然是连到您希望初始化的机器上)，使用 `opiepasswd` 命令：

```
% `opiepasswd`

Updating unfurl:
You need the response from an OTP generator.
Old secret pass phrase:
        otp-md5 498 to4268 ext
        Response: GAME GAG WELT OUT DOWN CHAT
New secret pass phrase:
        otp-md5 499 to4269
        Response: LINE PAP MILK NELL BUOY TROY

ID mark OTP key is 499 gr4269
LINE PAP MILK NELL BUOY TROY

```

为了接受默认的种子， 按下 **Return** （回车）。 在输入访问口令之前， 到一个有安全连接的机器上， 并给它同样的参数：

```
% `opiekey 498 to4268`
Using the MD5 algorithm to compute response.
Reminder: Don't use opiekey from telnet or dial-in sessions.
Enter secret pass phrase:
GAME GAG WELT OUT DOWN CHAT

```

现在回到不安全的连接， 并将生成的一次性口令粘贴到相应的应用程序中。

### 15.5.3. 生成一个一次性密码

一旦初始化过 OPIE， 当您登录时将看到类似这样的提示：

```
% `telnet example.com`
Trying 10.0.0.1...
Connected to example.com
Escape character is '^]'.

FreeBSD/i386 (example.com) (ttypa)

login: `<username>`
otp-md5 498 gr4269 ext
Password: 
```

另外， OPIE 提示有一个很有用的特性 (这里没有表现出来)： 如果您在口令提示处按下 **Return** (回车) 系统将回显刚键入的口令， 您可以藉此看到自己所键入的内容。 如果试图手工键入一个一次性密码， 这会非常有用。

此时您需要生成一个一次性密码来回答这一提示。 这项工作必须在一个可信的系统上执行 `opiekey` 来完成。 (也可以找到 DOS、 Windows® 以及 Mac OS® 等操作系统上运行的版本)。 这个程序需要将迭代轮数和种子提供给它。 您可以从登录提示那里复制和粘贴它们。

在可信的系统上：

```
% `opiekey 498 to4268`
Using the MD5 algorithm to compute response.
Reminder: Don't use opiekey from telnet or dial-in sessions.
Enter secret pass phrase:
GAME GAG WELT OUT DOWN CHAT
```

现在就可以用刚刚获得的一次性口令登录了。

### 15.5.4. 产生多个一次性口令

有时，会需要到不能访问可信的机器或安全连接的地方。 这种情形下， 可以使用 `opiekey` 命令来一次生成许多一次性口令。 例如：

```
% `opiekey -n 5 30 zz99999`
Using the MD5 algorithm to compute response.
Reminder: Don't use opiekey from telnet or dial-in sessions.
Enter secret pass phrase: `<secret password>`
26: JOAN BORE FOSS DES NAY QUIT
27: LATE BIAS SLAY FOLK MUCH TRIG
28: SALT TIN ANTI LOON NEAL USE
29: RIO ODIN GO BYE FURY TIC
30: GREW JIVE SAN GIRD BOIL PHI
```

`-n 5` 按顺序请求 5 个口令， `30` 则指定了最后一个迭代轮数应该是多少。 注意这些口令将按与使用顺序相反的顺序来显示。 如果您比较偏执， 可以手工写下这些结果； 一般来说把它粘贴到 `lpr` 就可以了。 注意，每一行都显示迭代轮数及其对应的一次性的密码； 一些人建议用完一个就划掉一个。

### 15.5.5. 限制使用 UNIX® 口令

OPIE 可以对 UNIX® 口令的使用进行基于 IP 的登录限制。 对应的文件是 `/etc/opieaccess`， 这个文件默认情况下就是存在的。 请参阅 [opieaccess(5)](http://www.FreeBSD.org/cgi/man.cgi?query=opieaccess&sektion=5&manpath=freebsd-release-ports) 以了解关于这个文件进一步的情况， 以及安全方面需要进行的一些考虑。

下面是一个示范的 `opieaccess` 文件：

```
permit 192.168.0.0 255.255.0.0
```

这行允许指定 IP 地址的用户 (再次强调这种地址容易被伪造) 在任何时候使用 UNIX® 口令登录。

如果 `opieaccess` 中没有匹配的规则， 则将默认拒绝任何非 OPIE 登录。

* * *

[^([8])](#idp78724848) 在 FreeBSD 中标准的登录口令最长不能超过 128 个字符。

## 15.6. TCP Wrappers

作者 Tom Rhodes.

每一个熟悉 [inetd(8)](http://www.FreeBSD.org/cgi/man.cgi?query=inetd&sektion=8&manpath=freebsd-release-ports) 都应该听说过 TCP Wrappers， 但几乎没有人对它在网络环境中的作用有全面的理解。 几乎每个人都会安装防火墙来处理网络连接， 然而虽然防火墙有非常广泛的用途， 它却不是万能的， 例如它无法处理类似向连接发起者发送一些文本这样的任务。 而 TCP Wrappers 软件能够完成它以及更多的其他事情。 接下来的几段中将讨论许多 TCP Wrappers 提供的功能， 并且， 还给出了一些配置实例。

TCP Wrappers 软件扩展了 inetd 为受其控制的服务程序实施控制的能力。 通过使用这种方法， 它能够提供日志支持、 返回消息给联入的连接、 使得服务程序只接受内部连接， 等等。 尽管防火墙也能够完成其中的某些功能， 但这不仅增加了一层额外的保护， 也提供了防火墙无法提供的功能。

然而， 由 TCP Wrappers 提供的一些额外的安全功能， 不应被视为好的防火墙的替代品。 TCP Wrappers 应结合防火墙或其他安全加强设施一并使用， 为系统多提供一层安全防护。

由于这些配置是对于 inetd 的扩展， 因此， 读者应首先阅读 配置 inetd 这节。

### 注意:

尽管由 [inetd(8)](http://www.FreeBSD.org/cgi/man.cgi?query=inetd&sektion=8&manpath=freebsd-release-ports) 运行的程序并不是真正的 “服务程序”， 但传统上也把它们称为服务程序。 下面仍将使用这一术语。

### 15.6.1. 初始配置

在 FreeBSD 中使用 TCP Wrappers 的唯一要求是确保 inetd 在从 `rc.conf` 中启动时包含了 `-Ww` 选项； 这是默认的设置。 当然， 还需要对 `/etc/hosts.allow` 进行适当的配置， 但 [syslogd(8)](http://www.FreeBSD.org/cgi/man.cgi?query=syslogd&sektion=8&manpath=freebsd-release-ports) 在配置不当时会在系统日志中记录相关消息。

### 注意:

与其它的 TCP Wrappers 实现不同， 使用 `hosts.deny` 在这里被认为是不推荐和过时的做法。 所有的配置选项应放到 `/etc/hosts.allow` 中。

在最简单的配置中， 服务程序的连接策略是根据 `/etc/hosts.allow` 允许或阻止。 FreeBSD 中的默认配置是允许一切发到由 inetd 所启动的服务的连接请求。 在基本配置之后将讨论更复杂的情况。

基本配置的形式通常是 `服务 : 地址 : 动作`。 这里 `服务` 是从 `inetd` 启动的服务程序的名字。 而 `地址` 可以是任何有效的主机名、 一个 IP 或由方括号 ([ ]) 括起来的 IPv6 地址。 `动作` 字段可以使 `allow` 或 `deny`， 分别用于允许和禁止相应的访问。 在配置时您需要注意所有的配置都是按照第一个匹配的规则运转的， 这表示配置文件将按照顺序查找匹配规则， 而一旦找到匹配， 则搜索也就停止了。

另外也有许多其他选项， 这些将在后面介绍。 简单的配置行从上面这些描述之中可以很容易得出。 例如， 允许 POP3 连接通过 [mail/qpopper](http://www.freebsd.org/cgi/url.cgi?ports/mail/qpopper/pkg-descr) 服务， 应把下面的行添加到 `hosts.allow`：

```
# This line is required for POP3 connections:
qpopper : ALL : allow
```

增加这样之后， 需要重新启动 inetd。 可以通过使用 [kill(1)](http://www.FreeBSD.org/cgi/man.cgi?query=kill&sektion=1&manpath=freebsd-release-ports) 命令来完成这项工作， 或使用 `/etc/rc.d/inetd` 的 *`restart`* parameter 参数。

### 15.6.2. 高级配置

TCP Wrappers 也有一些高级的配置选项； 它们能够用来对如何处理连接实施更多的控制。 一些时候， 返回一个说明到特定的主机或请求服务的连接可能是更好的办法。 其他情况下， 记录日志或者发送邮件给管理员可能更为适合。 另外， 一些服务可能只希望为本机提供。 这些需求都可以通过使用 `通配符`， 扩展字符以及外部命令来实现。 接下来的两节将介绍这些。

#### 15.6.2.1. 外部命令

假设由于发生了某种状况， 而导致连接应该被拒绝掉， 而将其原因发送给发起连接的人。 如何完成这样的任务呢？ 这样的动作可以通过使用 `twist` 选项来实现。 当发起了连接请求时， `twist` 将调用一个命令或脚本。 在 `hosts.allow` 文件中已经给出了一个例子：

```
# The rest of the daemons are protected.
ALL : ALL \
        : severity auth.info \
        : twist /bin/echo "You are not welcome to use %d from %h."
```

这个例子将把消息 “You are not allowed to use `daemon` from `hostname`.” 返回给访问先前没有配置过允许访问的服务客户。 对于希望把消息反馈给连接发起者， 然后立即切断这样的需求来说， 这样的配置非常有用。 请注意所有反馈信息 *必须* 被引号 `"` 包围； 这一规则是没有例外的。

### 警告:

如果攻击者向服务程序发送大量的连接请求， 则可能发动一次成功的拒绝服务攻击。

另一种可能是针对这种情况使用 `spawn`。 类似 `twist`， `spawn` 选项也暗含拒绝连接， 并可以用来执行外部命令或服务。 与 `twist` 不同的是， `spawn` 不会向连接发起者发送回应。 考虑下面的配置：

```
# We do not allow connections from example.com:
ALL : .example.com \
	: spawn (/bin/echo %a from %h attempted to access %d >> \
	  /var/log/connections.log) \
	: deny
```

这将拒绝来自 `*.example.com` 域的所有连接； 同时还将记录主机名， IP 地址， 以及对方所尝试连接的服务名字到 `/var/log/connections.log` 文件中。

除了前面已经介绍过的转义字符， 例如 `%a` 之外， 还有一些其它的转义符。 参考 [hosts_access(5)](http://www.FreeBSD.org/cgi/man.cgi?query=hosts_access&sektion=5&manpath=freebsd-release-ports) 联机手册可以获得完整的列表。

#### 15.6.2.2. 通配符选项

前面的例子都使用了 `ALL`。 其它选项能够将功能扩展到更远。 例如， `ALL` 可以被用来匹配每一个服务、 域，或 IP 地址。 另一些可用的通配符包括 `PARANOID`， 它可以用来匹配任何来自可能被伪造的 IP 地址的主机。 换言之， `paranoid` 可以被用来定义来自 IP 与其主机名不符的客户。 下面的例子将给您更多的感性认识：

```
# Block possibly spoofed requests to sendmail:
sendmail : PARANOID : deny
```

在这个例子中， 所有连接 `sendmail` 的 IP 地址与其主机名不符的主机都将被拒绝。

### 小心:

如果服务器和客户机有一方的 DNS 配置不正确， 使用 `PARANOID` 可能会严重地削弱服务。 在设置之前， 管理员应该谨慎地考虑。

要了解关于通配符和他们的功能， 请参考 [hosts_access(5)](http://www.FreeBSD.org/cgi/man.cgi?query=hosts_access&sektion=5&manpath=freebsd-release-ports) 联机手册。

为了使设置能够生效， 应该首先把 `hosts.allow` 的第一行配置注释掉。 这节的开始部分已经说明了这一点。

## 15.7. Kerberos5

撰写者 Tillman Hodgson.原文来自 Mark Murray.

Kerberos 是一组附加的网络系统/协议， 用以让用户通过一台安全服务器提供的服务来验证身份。 包括远程登录、远程复制、在系统间安全地复制文件， 以及其它高危险性的操作， 由于其存在而显著地提高了安全型并且更加可控。

Kerberos 可以理解为一种身份验证代理系统。 它也被描述为一种以受信第三方为主导的身份验证系统。 Kerberos 只提供一种功能 ── 在网络上安全地完成用户的身份验证。 它并不提供授权功能 (也就是说用户能够做什么操作) 或审计功能 (记录用户作了什么操作)。 一旦客户和服务器都使用了 Kerberos 来证明各自的身份之后， 他们还可以加密全部的通讯以保证业务数据的私密性和完整性。

因此， 强烈建议将 Kerberos 同其它提供授权和审计服务的安全手段联用。

接下来的说明可以用来指导如何安装 FreeBSD 所附带的 Kerberos。 不过， 您仍然需要参考相应的联机手册以获得完整的描述。

为了展示 Kerberos 的安装过程， 我们约定：

*   DNS 域 (“zone”) 为 example.org。

*   Kerberos 领域是 EXAMPLE.ORG。

### 注意:

在安装 Kerberos 时请使用实际的域名即使您只是想在内部网上用一用。 这可以避免 DNS 问题并保证了同其它 Kerberos 之间的互操作性。

### 15.7.1. 历史

Kerberos 最早由 MIT 作为解决网络安全问题的一个方案提出。 Kerberos 协议采用了强加密， 因此客户能够在不安全的网络上向服务器 (以及相反地) 验证自己的身份。

Kerberos 是网络验证协议名字， 同时也是用以表达实现了它的程序的形容词。 (例如 Kerberos telnet)。 目前最新的协议版本是 5，在 RFC 1510 中有所描述。

该协议有许多免费的实现， 这些实现涵盖了许多种不同的操作系统。 最初研制 Kerberos 的麻省理工学院 (MIT) 也仍然在继续开发他们的 Kerberos 软件包。 在 US 它被作为一种加密产品使用， 因而历史上曾经受到 US 出口管制。 MIT Kerberos 可以通过 port ([security/krb5](http://www.freebsd.org/cgi/url.cgi?ports/security/krb5/pkg-descr)) 来安装和使用。 Heimdal Kerberos 是另一种第 5 版实现， 并且明确地在 US 之外的地区开发， 以避免出口管制 (因此在许多非商业的类 UNIX® 系统中非常常用。 Heimdal Kerberos 软件包可以通过 port ([security/heimdal](http://www.freebsd.org/cgi/url.cgi?ports/security/heimdal/pkg-descr)) 安装， 最新的 FreeBSD 的最小安装也会包含它。

为使尽可能多的读者从中受益， 这份说明以 FreeBSD 附带的 Heimdal 软件包为准。

### 15.7.2. 配置 Heimdal KDC

密钥分发中心 (KDC) 是 Kerberos 提供的集中式验证服务 ── 它是签发 Kerberos tickets 的那台计算机。 KDC 在 Kerberos 领域中的其它机器看来是 “受信的”， 因此必须格外注意其安全性。

需要说明 Kerberos 服务器只需要非常少的计算资源， 尽管如此， 基于安全理由仍然推荐使用独占的机器来扮演 KDC 的角色。

要开始配置 KDC， 首先请确认您的 `/etc/rc.conf` 文件包含了作为一个 KDC 所需的设置 (您可能需要适当地调整路径以适应自己系统的情况)：

```
kerberos5_server_enable="YES"
kadmind5_server_enable="YES"
```

接下来需要修改 Kerberos 的配置文件， `/etc/krb5.conf`：

```
[libdefaults]
    default_realm = EXAMPLE.ORG
[realms]
    EXAMPLE.ORG = {
        kdc = kerberos.example.org
        admin_server = kerberos.example.org
    }
[domain_realm]
    .example.org = EXAMPLE.ORG
```

请注意这个 `/etc/krb5.conf` 文件假定您的 KDC 有一个完整的主机名， 即 `kerberos.example.org`。 如果您的 KDC 主机名与它不同， 则应添加一条 CNAME (别名) 项到 zone 中去。

### 注意:

对于有正确地配置过的 BIND DNS 服务器的大型网络， 上述例子可以精简为：

```
[libdefaults]
      default_realm = EXAMPLE.ORG
```

将下面的内容加入到 `example.org` zone 数据文件中：

```
_kerberos._udp      IN  SRV     01 00 88 kerberos.example.org.
_kerberos._tcp      IN  SRV     01 00 88 kerberos.example.org.
_kpasswd._udp       IN  SRV     01 00 464 kerberos.example.org.
_kerberos-adm._tcp  IN  SRV     01 00 749 kerberos.example.org.
_kerberos           IN  TXT     EXAMPLE.ORG
```

### 注意:

要让客户机能够找到 Kerberos 服务， 就 *必须* 首先配置完整或最小配置的 `/etc/krb5.conf` *并且* 正确地配置 DNS 服务器。

接下来需要创建 Kerberos 数据库。 这个数据库包括了使用主密码加密的所有实体的密钥。 您并不需要记住这个密码， 它会保存在一个文件 (`/var/heimdal/m-key`) 中。 要创建主密钥， 需要执行 `kstash` 并输入一个口令。

主密钥一旦建立， 您就可以用 `kadmin` 程序的 `-l` 参数 (表示 “local”) 来初始化数据库了。 这个选项让 `kadmin` 直接地修改数据库文件而不是通过 `kadmind` 的网络服务。 这解决了在数据库创建之前连接它的鸡生蛋的问题。 进入 `kadmin` 提示符之后， 用 `init` 命令来创建领域的初始数据库。

最后， 仍然在 `kadmin` 中， 使用 `add` 命令来创建第一个 principal。 暂时使用全部的默认设置， 随后可以在任何时候使用 `modify` 命令来修改这些设置。 另外， 也可以用 `?` 命令来了解可用的选项。

典型的数据库创建过程如下：

```
# `kstash`
Master key: `xxxxxxxx`
Verifying password - Master key: `xxxxxxxx`

# `kadmin -l`
kadmin> `init EXAMPLE.ORG`
Realm max ticket life [unlimited]:
kadmin> `add tillman`
Max ticket life [unlimited]:
Max renewable life [unlimited]:
Attributes []:
Password: `xxxxxxxx`
Verifying password - Password: `xxxxxxxx`
```

现在是启动 KDC 服务的时候了。 运行 `/etc/rc.d/kerberos start` 以及 `/etc/rc.d/kadmind start` 来启动这些服务。 尽管此时还没有任何正在运行的 Kerberos 服务， 但您仍然可以通过获取并列出您刚刚创建的那个 principal (用户) 的 ticket 来验证 KDC 确实在正常工作， 使用 KDC 本身的功能：

```
% `kinit tillman`
tillman@EXAMPLE.ORG's Password:

% `klist`
Credentials cache: FILE:/tmp/krb5cc_500
	Principal: tillman@EXAMPLE.ORG

  Issued           Expires          Principal
Aug 27 15:37:58  Aug 28 01:37:58  krbtgt/EXAMPLE.ORG@EXAMPLE.ORG
```

完成所需的操作之后， 可以撤消这一 ticket：

```
% `kdestroy`
```

### 15.7.3. 为 Kerberos 启用 Heimdal 服务

首先我们需要一份 Kerberos 配置文件 `/etc/krb5.conf` 的副本。 只需简单地用安全的方式 (使用类似 [scp(1)](http://www.FreeBSD.org/cgi/man.cgi?query=scp&sektion=1&manpath=freebsd-release-ports) 的网络工具， 或通过软盘) 复制 KDC 上的版本， 并覆盖掉客户机上的对应文件就可以了。

接下来需要一个 `/etc/krb5.keytab` 文件。 这是提供 Kerberos 服务的服务器和工作站的一个主要区别 ── 服务器必须有 `keytab` 文件。 这个文件包括了服务器的主机密钥， 这使得 KDC 得以验证它们的身份。 此文件必须以安全的方式传到服务器上， 因为如果密钥被公之于众， 则安全也就毁于一旦。 也就是说， 通过明文的通道， 例如 FTP 是非常糟糕的想法。

一般来说， 您会希望使用 `kadmin` 程序来把 `keytab` 传到服务器上。 由于也需要使用 `kadmin` 来为主机建立 principal (KDC 一端的 `krb5.keytab`)， 因此这并不复杂。

注意您必须已经获得了一个 ticket 而且这个 ticket 必须许可使用 `kadmind.acl` 中的 `kadmin` 接口。 请参考 Heimdal info 中的 “Remote administration(远程管理)” 一节 (`info heimdal`) 以了解如何设计访问控制表。 如果不希望启用远程的 `kadmin` 操作， 则可以简单地采用安全的方式连接 KDC (通过本机控制台， [ssh(1)](http://www.FreeBSD.org/cgi/man.cgi?query=ssh&sektion=1&manpath=freebsd-release-ports) 或 Kerberos [telnet(1)](http://www.FreeBSD.org/cgi/man.cgi?query=telnet&sektion=1&manpath=freebsd-release-ports)) 并使用 `kadmin -l` 在本地执行管理操作。

安装了 `/etc/krb5.conf` 文件之后， 您就可以使用 Kerberos 上的 `kadmin` 了。 `add --random-key` 命令可以用于添加主机 principal， 而 `ext` 命令则允许导出服务器的主机 principal 到它的 keytab 中。 例如：

```
# `kadmin`
kadmin> `add --random-key host/myserver.example.org`
Max ticket life [unlimited]:
Max renewable life [unlimited]:
Attributes []:
kadmin> `ext host/myserver.example.org`
kadmin> `exit`
```

注意 `ext` 命令 (这是 “extract” 的简写) 默认会把导出的密钥放到 `/etc/krb5.keytab` 中。

如果您由于没有在 KDC 上运行 `kadmind` (例如基于安全理由) 因而无法远程地使用 `kadmin` 您可以直接在 KDC 上添加主机 principal (`host/myserver.EXAMPLE.ORG`) 随后将其导出到一个临时文件中 (以免覆盖 KDC 上的 `/etc/krb5.keytab`)， 方法是使用下面的命令：

```
# `kadmin`
kadmin> `ext --keytab=/tmp/example.keytab host/myserver.example.org`
kadmin> `exit`
```

随后需要把 keytab 复制到服务器上 (例如使用 `scp` 或软盘)。 一定要指定一个不同于默认的 keytab 名字以免覆盖 KDC 上的 keytab。

到现在您的服务器已经可以同 KDC 通讯了 (因为已经配置了 `krb5.conf` 文件)， 而且它还能够证明自己的身份 (由于配置了 `krb5.keytab` 文件)。 现在可以启用一些 Kerberos 服务。 在这个例子中， 我们将在 `/etc/inetd.conf` 中添加下面的行来启用 `telnet` 服务， 随后用 `/etc/rc.d/inetd restart` 重启 [inetd(8)](http://www.FreeBSD.org/cgi/man.cgi?query=inetd&sektion=8&manpath=freebsd-release-ports) 服务来使设置生效：

```
telnet    stream  tcp     nowait  root    /usr/libexec/telnetd  telnetd -a user
```

关键的部分是 `-a` (表示验证) 类型设置为用户 (user)。 请参考 [telnetd(8)](http://www.FreeBSD.org/cgi/man.cgi?query=telnetd&sektion=8&manpath=freebsd-release-ports) 联机手册以了解细节。

### 15.7.4. 使用 Heimdal 来启用客户端 Kerberos

设置客户机是非常简单的。 在正确配置了 Kerberos 的网络中， 只需要将位于 `/etc/krb5.conf` 的配置文件进行一下设置就可以了。 这一步骤可以简单地通过安全的方式将文件从 KDC 复制到客户机上来完成。

尝试在客户机上执行 `kinit`、 `klist`， 以及 `kdestroy` 来测试获取、 显示并删除 刚刚为 principal 建立的 ticket 是否能够正常进行， 如果能， 则用其它的 Kerberos 应用程序来连接启用了 Kerberos 的服务。 如果应用程序不能正常工作而获取 ticket 正常， 则通常是服务本身， 而非客户机或 KDC 有问题。

在测试类似 `telnet` 的应用程序时， 应考虑使用抓包程序 (例如 [tcpdump(1)](http://www.FreeBSD.org/cgi/man.cgi?query=tcpdump&sektion=1&manpath=freebsd-release-ports)) 来确认您的口令没有以明文方式传输。 尝试使用 `telnet` 的 `-x` 参数， 它将加密整个数据流 (类似 `ssh`)。

许多非核心的 Kerberos 客户应用程序也是默认安装的。 在 Hemidal 的 “最小” 安装理念下， `telnet` 是唯一一个采用了 Kerberos 的服务。

Heimdal port 则提供了一些默认不安装的客户应用程序， 例如启用了 Kerberos 版本的 `ftp`、 `rsh`、 `rcp`、 `rlogin` 以及一些更不常用的程序。 MIT port 也包括了一整套 Kerberos 客户应用程序。

### 15.7.5. 用户配置文件： `.k5login` 和 `.k5users`

在某个领域中的用户往往都有自己的 Kerberos principal (例如 `tillman@EXAMPLE.ORG`) 并映射到本机用户帐户 (例如本机上名为 `tillman` 的帐户)。 客户端应用程序， 如 `telnet` 通常并不需要用户名或 principal。

不过, 有时您可能会需要赋予某些没有匹配 Kerberos principal 的人使用本地用户帐户的权限。 例如 `tillman@EXAMPLE.ORG` 可能需要访问本地的 `webdevelopers` 用户帐号。 其它 principal 可能也会需要访问这个本地帐号。

用户 home 目录中的 `.k5login` 和 `.k5users` 这两个文件可以配合 `.hosts` 和 `.rhosts` 来有效地解决这个问题。 例如， 如果 `.k5login` 中有如下内容：

```
tillman@example.org
jdoe@example.org
```

并放到了本地用户 `webdevelopers` 的 home 目录中， 则列出的两个 principals 都可以使用那个帐号， 而无须共享口令。

建议您在开始实施之前首先阅读这些命令的联机帮助。 特别地， `ksu` 的联机手册包括了 `.k5users` 的相关内容。

### 15.7.6. Kerberos 提示、技巧和故障排除

*   当使用 Heimdal 或 MIT Kerberos ports 时， 需要确认 `PATH` 环境变量把 Kerberos 客户应用列在系统自带的版本之前。

*   同一领域内的所有计算机的时间设置是否同步？ 如果不是的话， 则身份验证可能会失败。 第 30.10 节 “通过 NTP 进行时钟同步” 描述了如何使用 NTP 来同步时钟。

*   MIT 和 Heimdal 能够很好地互操作。 一个例外是 `kadmin`， 因为这个协议没有被标准化。

*   如果您改变了主机名， 您还需要修改您的 `host/` principal 并更新 keytab。 这一规律也适用于类似 Apache 的 [www/mod_auth_kerb](http://www.freebsd.org/cgi/url.cgi?ports/www/mod_auth_kerb/pkg-descr) 所使用的 `www/` principal 这样的特殊 keytab 项。

*   您的领域中的每一台主机必须在 DNS (或至少在 `/etc/hosts` 中) 可以解析 (同时包括正向和反向)。 CNAME 能够正常使用， 但必须有正确的对应 A 和 PTR 记录。 此时给出的错误信息可能很让人困惑： Kerberos5 refuses authentication because Read req failed: Key table entry not found。

*   某些作为客户使用您的 KDC 的操作系统可能没有将 `ksu` 设置为 setuid `root` 的权限。 这意味着 `ksu` 将不能够正常工作， 从安全角度说这是一个不错的主意， 但可能令人烦恼。 这类问题并不是 KDC 的错误。

*   使用 MIT Kerberos 时， 如果希望允许一个 principal 拥有超过默认的十小时有效期的 ticket 则必须使用 `kadmin` 中的 `modify_principal` 来修改 principal 本身以及 `krbtgt` 的 maxlife(最大有效期)。 此后， principal 可以使用 `kinit` 的 `-l` 参数来请求一个有更长有效期的 ticket。

*   ### 注意:

    如果在 KDC 上运行了听包程序， 并在工作站上执行 `kinit`， 您可能会注意到 TGT 是在 `kinit` 一开始执行的时候就发出了的 ── 甚至在您输入口令之前！ 关于这个现象的解释是 Kerberos 服务器可以无限制地收发 TGT (Ticket Granting Ticket) 给任何未经授权的请求； 但是， 每一个 TGT 都是使用用户的口令派生出来的密钥进行加密的。 因此， 当用户输入口令时它并不会发送给 KDC， 而是直接用于解密 `kinit` 所拿到的 TGT。 如果解密过程得到了一个包含合法的时间戳的有效 ticket， 则说明用户的 Kerberos 凭据有效。 这些凭据包含了一个会话密钥用以在随后建立 Kerberos 服务器的加密通讯， 传递由服务器自己的私钥加密的实际的 ticket-granting ticket。 这个第二层加密对于用户来说是看不到的， 但它使得 Kerberos 服务器能够验证每一个 TGT 的真实性。

*   如果需要有效期更长的 ticket (例如一周) 而且您使用 OpenSSH 连接保存您的 ticket 的机器， 请确认 `sshd_config` 中的 Kerberos `TicketCleanup` 被设置为 `no` 否则在注销时会自动删除所有的 ticket。

*   切记主机的 principals 的 ticket 有效期一定要比用户的长。 如果您的用户 principal 的有效期是一周， 而所连接的主机的有效期是九个小时， 则缓存的主机 principal 将先行过期， 结果是 ticket 缓存无法正常工作。

*   当配置 `krb5.dict` 文件来防止使用特定的简单口令 (`kadmind` 的联机手册中简要介绍了它)， 请切记只有指定了口令策略的 principals 才会使用它们。 `krb5.dict` 文件的格式很简单： 每个串占一行。 创建一个到 `/usr/share/dict/words` 的符号连接会很有用。

### 15.7.7. 与 MIT port 的区别

MIT 和 Heimdal 主要的区别在于 `kadmin` 程序使用不同 (尽管等价) 的命令和协议。 如果您的 KDC 是 MIT 的， 则其影响是不能使用 Heimdal 的 `kadmin` 程序来远程管理 KDC (或相反)。

完成同样工作的命令可能会有些许的不同。 推荐按照 MIT Kerberos 的网站 (`[`web.mit.edu/Kerberos/www/`](http://web.mit.edu/Kerberos/www/)`) 上的说明来操作。 请小心关于路径的问题， MIT port 会默认安装到 `/usr/local/`， 您因此可能会执行 “普通的” 系统应用程序而非 MIT, 如果您的 `PATH` 环境变量把 把系统目录放在前面的话。

### 注意:

如果使用 FreeBSD 提供的 MIT [security/krb5](http://www.freebsd.org/cgi/url.cgi?ports/security/krb5/pkg-descr) port， 一定要仔细阅读 port 所安装的 `/usr/local/share/doc/krb5/README.FreeBSD`， 如果您想知道为什么通过 `telnetd` 和 `klogind` 登录时会出现一些诡异的现象的话。 最重要地， “incorrect permissions on cache file(缓存文件权限不正确)” 行为需要使用 `login.krb5` 来进行验证， 才能够正确地修改转发凭据的属主。

除此之外， 还应修改 `rc.conf` 并加入下列配置：

```
kerberos5_server="/usr/local/sbin/krb5kdc"
kadmind5_server="/usr/local/sbin/kadmind"
kerberos5_server_enable="YES"
kadmind5_server_enable="YES"
```

这样做的原因是， MIT kerberos 会将可执行文件装到 `/usr/local` 之下。

### 15.7.8. 缓解 Kerberos 的限制

#### 15.7.8.1. Kerberos 是一种 all-or-nothing 方式

在网络上启用的每个服务都必须进行修改以便让其能够配合 Kerberos 工作 (否则就只能使用其它方法来保护它们不受网络攻击的侵害)， 如果不是这样， 则用户的凭据就有可能被窃取并再次使用。 一个例子是对所有的远程 shell (例如通过 `rsh` 和 `telnet`) 启用了 Kerberos 但没有将使用明文验证的 POP3 邮件服务器 Kerberos 化。

#### 15.7.8.2. Kerberos 是为单用户工作站设计的

在多用户环境中 Kerberos 的安全性会被削弱。 这是因为它把 ticket 保存到 `/tmp` 目录中， 而这个目录可以被任何用户读取。 如果有用户与其它人同时共享一台计算机 (也就是 multi-user)， 则这个用户的 ticket 就可能被其它用户窃取 (复制)。

可以通过使用 `-c` 文件名 这样的命令行选项， 或者(推荐的)改变 `KRB5CCNAME` 环境变量来避免这个问题， 但很少有人这么做。原则上， 将 ticket 保存到用户的 home 目录并简单地设置权限就能够缓解这个问题。

#### 15.7.8.3. KDC 会成为单点崩溃故障点

根据设计， KDC 必须是安全的， 因为主密码数据库保存在它上面。 决不应该在 KDC 上面运行其它服务， 而且还应确保它的物理安全。 由于 Kerberos 使用同一个密钥 (传说中的那个 “主” 密钥) 来加密所有的密码， 而将这个文件保存在 KDC， 因此其安全尤为重要

不过， 主密钥的泄露并没有想象中的那么可怕。 主密钥只用来加密 Kerberos 数据库以及产生随机数发生器的种子。 只要 KDC 是安全的， 即使攻击者拿到了主密钥也做不了什么。

另外， 如果 KDC 不可用 (例如由于拒绝服务攻击或网络故障) 则网络服务将由于验证服务无法进行而不能使用， 从而导致更大范围的拒绝服务攻击。 通过部署多个 KDC (一个主服务器， 配合一个或多个从服务器) 并采用经过仔细设计和实现的备用验证方式可以避免这种问题 (PAM 是一个不错的选择)。

#### 15.7.8.4. Kerberos 的不足

Kerberos 允许用户、主机和服务之间进行相互认证。 但它并没有提供机制来向用户、主机或服务验证 KDC。 这意味着种过木马的程序，例如 `kinit` 有可能记录用户所有的用户名和密码。 尽管如此， 可以用类似 [security/tripwire](http://www.freebsd.org/cgi/url.cgi?ports/security/tripwire/pkg-descr) 这样的文件系统完整性检查工具来避免此类情况的发生。

### 15.7.9. 相关资源和其它资料

*   [The Kerberos FAQ](http://www.faqs.org/faqs/Kerberos-faq/general/preamble.html)

*   [Designing an Authentication System: a Dialog in Four Scenes](http://web.mit.edu/Kerberos/www/dialogue.html)

*   [RFC 1510, The Kerberos Network Authentication Service (V5)](http://www.ietf.org/rfc/rfc1510.txt?number=1510)

*   [MIT Kerberos home page](http://web.mit.edu/Kerberos/www/)

*   [Heimdal Kerberos home page](http://www.pdc.kth.se/heimdal/)

## 15.8. OpenSSL

作者 Tom Rhodes.

许多用户可能并没有注意到 FreeBSD 所附带的 OpenSSL 工具包的功能。 OpenSSL 提供了建立在普通的通讯层基础上的加密传输层； 这些功能为许多网络应用和服务程序所广泛使用。

对 OpenSSL 的一些常见用法包括加密邮件客户的身份验证过程， 基于 Web 的交易如信用卡等等。 许多 ports 如 [www/apache13-ssl](http://www.freebsd.org/cgi/url.cgi?ports/www/apache13-ssl/pkg-descr)， 以及 [mail/claws-mail](http://www.freebsd.org/cgi/url.cgi?ports/mail/claws-mail/pkg-descr) 等等都提供了编译进 OpenSSL 支持的方法。

### 注意:

绝大多数情况下 Ports Collection 会试图使用 [security/openssl](http://www.freebsd.org/cgi/url.cgi?ports/security/openssl/pkg-descr) 除非明确地将 `WITH_OPENSSL_BASE` make 变量设置为 “yes”。

FreeBSD 中附带的 OpenSSL 版本能够支持 安全套接字层 v2/v3 (SSLv2/SSLv3) 和 安全传输层 v1 (TLSv1) 三种网络协议， 并可作为通用的密码学函数库使用。

### 注意:

尽管 OpenSSL 支持 IDEA 算法， 但由于美国专利， 它在默认情况下是不编译的。 如果想使用它， 请查阅相应的授权， 如果认为授权可以接受， 则可以在 `make.conf` 中设置 `MAKE_IDEA`。

为应用软件提供证书是 OpenSSL 最为常用的功能之一。 证书是一种能够确保公司或个人有效身份不被伪造的凭据。 如果证书没有被众多 “权威发证机构”， 或 CA 中的某一个确认， 则会产生一个警告。 权威发证机构通常是一家公司， 例如 [VeriSign](http://www.verisign.com)， 它能够通过签署来证明个人或公司证书的有效性。 这个过程是需要付费的， 当然， 这不是使用证书的必要条件； 然而， 这样做会让那些比较偏执的用户感到轻松。

### 15.8.1. 生成证书

为了生成证书， 需要使用下面的命令：

```
# `openssl req -new -nodes -out req.pem -keyout cert.pem`
Generating a 1024 bit RSA private key
................++++++
.......................................++++++
writing new private key to 'cert.pem'
-----
You are about to be asked to enter information that will be incorporated
into your certificate request.
What you are about to enter is what is called a Distinguished Name or a DN.
There are quite a few fields but you can leave some blank
For some fields there will be a default value,
If you enter '.', the field will be left blank.
-----
Country Name (2 letter code) [AU]:`US`
State or Province Name (full name) [Some-State]:`PA`
Locality Name (eg, city) []:`Pittsburgh`
Organization Name (eg, company) [Internet Widgits Pty Ltd]:`My Company`
Organizational Unit Name (eg, section) []:`Systems Administrator`
Common Name (eg, YOUR name) []:`localhost.example.org`
Email Address []:`trhodes@FreeBSD.org`

Please enter the following 'extra' attributes
to be sent with your certificate request
A challenge password []:`SOME PASSWORD`
An optional company name []:`Another Name`
```

请注意， 在 “Common Name” 提示后面我们输入的是一个域名。 这个提示要求输入服务器的名字， 这个名字今后将用于完成验证过程； 如果在这里输入域名以外的内容， 那么证书也就失去其意义了。 您还可以指定一些其他的选项， 比如证书的有效期， 以及使用的加密算法等等。 这些选项的完整列表， 可以在 [openssl(1)](http://www.FreeBSD.org/cgi/man.cgi?query=openssl&sektion=1&manpath=freebsd-release-ports) 联机手册中找到。

在您执行前述命令的目录中将生成两个文件。 证书申请， 即 `req.pem`， 可以发给一家发证机构， 它将验证您输入的凭据的真实性， 并对申请进行签名， 再把证书返还给您。 第二个文件的名字将是 `cert.pem`， 它包含了证书的私钥， 应被全力保护； 如果它落入别人手中， 则可以被用来伪造您 (或您的服务器)。

如果不需要来自 CA 的签名， 也可以创建自行签名的证书。 首先， 需要生成 RSA 密钥：

```
# `openssl dsaparam -rand -genkey -out myRSA.key 1024`
```

接下来， 生成 CA 密钥：

```
# `openssl gendsa -des3 -out myca.key myRSA.key`
```

然后用这个密钥来创建证书：

```
# `openssl req -new -x509 -days 365 -key myca.key -out new.crt`
```

上述步骤将在当前目录中生成两个新文件： 一个是权威发证机构的签名文件， `myca.key`； 另一个是证书本身， `new.crt`。 这些文件应该放到同一个目录中， 一般而言， 推荐放到 `/etc`， 并且只允许 `root` 读取。 建议把权限设置为 0700， 这可以通过 `chmod` 工具来完成。

### 15.8.2. 使用证书的一个例子

那么有了这些文件可以做些什么呢？ 一个比较典型的用法是用来加密 Sendmail MTA 的通讯连接。 这可以解决用户通过本地 MTA 发送邮件时使用明文进行身份验证的问题。

### 注意:

这个用法可能并不完美， 因为某些 MUA 会由于没有在本地安装证书而向用户发出警告。 请参考那些软件的说明了解关于安装证书的信息。

下面的设置应添加到本地的 `.mc` 文件

```
dnl SSL Options
define(`confCACERT_PATH',`/etc/certs')dnl
define(`confCACERT',`/etc/certs/new.crt')dnl
define(`confSERVER_CERT',`/etc/certs/new.crt')dnl
define(`confSERVER_KEY',`/etc/certs/myca.key')dnl
define(`confTLS_SRV_OPTIONS', `V')dnl
```

这里， `/etc/certs/` 是准备用来在本地保存证书和密钥的位置。 最后， 需要重新生成本地的 `.cf` 文件。 这一工作可以简单地通过在 目录中执行 `make` `install` 来完成。 接下来， 可以使用 `make` `restart` 来重新启动 Sendmail 服务程序。

如果一切正常的话， 在 `/var/log/maillog` 中就不会出现错误提示， Sendmail 也应该出现在进程列表中。

做一个简单的测试， 使用 [telnet(1)](http://www.FreeBSD.org/cgi/man.cgi?query=telnet&sektion=1&manpath=freebsd-release-ports) 来连接邮件服务器：

```
# `telnet example.com 25`
Trying 192.0.34.166...
Connected to example.com.
Escape character is '^]'.
220 example.com ESMTP Sendmail 8.12.10/8.12.10; Tue, 31 Aug 2004 03:41:22 -0400 (EDT)
`ehlo example.com`
250-example.com Hello example.com [192.0.34.166], pleased to meet you
250-ENHANCEDSTATUSCODES
250-PIPELINING
250-8BITMIME
250-SIZE
250-DSN
250-ETRN
250-AUTH LOGIN PLAIN
250-STARTTLS
250-DELIVERBY
250 HELP
`quit`
221 2.0.0 example.com closing connection
Connection closed by foreign host.
```

如果输出中出现了 “STARTTLS” 则说明一切正常。

## 15.9. IPsec 上的 VPN

撰写者 Nik Clayton.

使用 FreeBSD 网关在两个被 Internet 分开的网络之间架设 VPN。

### 15.9.1. 理解 IPsec

撰写者 Hiten M. Pandya.

这一节将指导您完成架设 IPsec。为了配置 IPsec， 您应当熟悉如何编译一个定制的内核的一些概念 (参见 第 9 章 *配置 FreeBSD 的内核*)。

*IPsec* 是一种建立在 Internet 协议 (IP) 层之上的协议。 它能够让两个或更多主机以安全的方式来通讯 (并因此而得名)。 FreeBSD IPsec “网络协议栈” 基于 [KAME](http://www.kame.net/) 的实现， 它支持两种协议族， IPv4 和 IPv6。

IPsec 包括了两个子协议：

*   *Encapsulated Security Payload (ESP)*, 保护 IP 包数据不被第三方介入， 通过使用对称加密算法 (例如 Blowfish、 3DES)。

*   *Authentication Header (AH)*, 保护 IP 包头不被第三方介入和伪造， 通过计算校验和以及对 IP 包头的字段进行安全散列来实现。 随后是一个包含了散列值的附加头， 以便能够验证包。

ESP 和 AH 可以根据环境的不同， 分别或者一同使用。

IPsec 既可以用来直接加密主机之间的网络通讯 (也就是 *传输模式*)； 也可以用来在两个子网之间建造 “虚拟隧道” 用于两个网络之间的安全通讯 (也就是 *隧道模式*)。 后一种更多的被称为是 *虚拟专用网 (VPN)*。 [ipsec(4)](http://www.FreeBSD.org/cgi/man.cgi?query=ipsec&sektion=4&manpath=freebsd-release-ports) 联机手册提供了关于 FreeBSD 中 IPsec 子系统的详细信息。

要把 IPsec 支持放进内核， 应该在配置文件中加入下面的选项：

```
options   IPSEC        #IP security
device    crypto

```

如果需要 IPsec 的调试支持， 还应增加：

```
options   IPSEC_DEBUG  #debug for IP security

```

### 15.9.2. 问题

由于对如何建立 VPN 并不存在标准， 因此 VPN 可以采用许多种不同的技术来实现， 每种技术都有其强项和弱点。 这篇文章将展现一个具体的应用情景， 并为它设计了适合的 VPN。

### 15.9.3. 情景： 两个网络，一个家庭的网络和一个公司的网络。 都接入了 Internet，并且通过这条 VPN 就像在同一个网络一样。

现有条件如下：

*   至少有两个不同的站点

*   每个站点都使用内部的 IP

*   两个站点都通过运行 FreeBSD 的网关接入 Internet。

*   每个网络上的网关至少有一个公网的 IP 地址。

*   网络的内部地址可以是公网或私有的 IP 地址， 这并不是问题。它们并不冲突，比如它们不同时使用 `192.168.1.x` 这样的地址。

### 15.9.4. 在 FreeBSD 上配置 IPsec

作者 Tom Rhodes.

开始需先从 Ports Collection 安装 [security/ipsec-tools](http://www.freebsd.org/cgi/url.cgi?ports/security/ipsec-tools/pkg-descr)。 这个第三方软件提供了一些能够帮助配置的应用程序。

下一步是创建两个 [gif(4)](http://www.FreeBSD.org/cgi/man.cgi?query=gif&sektion=4&manpath=freebsd-release-ports) 伪设备用来在两个网络间传输数据包的 “隧道”。 使用 `root` 身份运行以下命令， 并用真实的内部外部网关替换命令中的 *`internal`* 和 *`external`* 项：

```
# `ifconfig gif0 create`
```

```
# `ifconfig gif0 internal1 internal2`
```

```
# `ifconfig gif0 tunnel external1 external2`
```

比如，公司 LAN 对外的 IP 地址是 `172.16.5.4`， 内部的 IP 地址为 `10.246.38.1`。 家庭 LAN 对外的 IP 地址是 `192.168.1.12`， 内部的 IP 地址为 `10.0.0.5`。

这看起来可能有些混乱，所以我们通过 [ifconfig(8)](http://www.FreeBSD.org/cgi/man.cgi?query=ifconfig&sektion=8&manpath=freebsd-release-ports) 命令输出再回顾一下：

```
Gateway 1:

gif0: flags=8051 mtu 1280
tunnel inet 172.16.5.4 --> 192.168.1.12
inet6 fe80::2e0:81ff:fe02:5881%gif0 prefixlen 64 scopeid 0x6
inet 10.246.38.1 --> 10.0.0.5 netmask 0xffffff00

Gateway 2:

gif0: flags=8051 mtu 1280
tunnel inet 192.168.1.12 --> 172.16.5.4
inet 10.0.0.5 --> 10.246.38.1 netmask 0xffffff00
inet6 fe80::250:bfff:fe3a:c1f%gif0 prefixlen 64 scopeid 0x4
```

一旦完成以后，两个私有的 IP 地址都应该能像下面 [ping(8)](http://www.FreeBSD.org/cgi/man.cgi?query=ping&sektion=8&manpath=freebsd-release-ports) 命令输出那样互相访问。

```
priv-net# ping 10.0.0.5
PING 10.0.0.5 (10.0.0.5): 56 data bytes
64 bytes from 10.0.0.5: icmp_seq=0 ttl=64 time=42.786 ms
64 bytes from 10.0.0.5: icmp_seq=1 ttl=64 time=19.255 ms
64 bytes from 10.0.0.5: icmp_seq=2 ttl=64 time=20.440 ms
64 bytes from 10.0.0.5: icmp_seq=3 ttl=64 time=21.036 ms
--- 10.0.0.5 ping statistics ---
4 packets transmitted, 4 packets received, 0% packet loss
round-trip min/avg/max/stddev = 19.255/25.879/42.786/9.782 ms

corp-net# ping 10.246.38.1
PING 10.246.38.1 (10.246.38.1): 56 data bytes
64 bytes from 10.246.38.1: icmp_seq=0 ttl=64 time=28.106 ms
64 bytes from 10.246.38.1: icmp_seq=1 ttl=64 time=42.917 ms
64 bytes from 10.246.38.1: icmp_seq=2 ttl=64 time=127.525 ms
64 bytes from 10.246.38.1: icmp_seq=3 ttl=64 time=119.896 ms
64 bytes from 10.246.38.1: icmp_seq=4 ttl=64 time=154.524 ms
--- 10.246.38.1 ping statistics ---
5 packets transmitted, 5 packets received, 0% packet loss
round-trip min/avg/max/stddev = 28.106/94.594/154.524/49.814 ms
```

正如预期的那样，两边都有从私有地址发送和接受 ICMP 数据包的能力。下面， 两个网关都必须配置路由规则以正确传输两边的网络流量。 下面的命令可以实现这个：

```
# `corp-net# route add 10.0.0.0 10.0.0.5 255.255.255.0`
```

```
# `corp-net# route add net 10.0.0.0: gateway 10.0.0.5`
```

```
# `priv-net# route add 10.246.38.0 10.246.38.1 255.255.255.0`
```

```
# `priv-net# route add host 10.246.38.0: gateway 10.246.38.1`
```

此刻，不论从网关还是网关后的机器都能访问内部的网络。 这很容易通过以下的例子确认：

```
corp-net# ping 10.0.0.8
PING 10.0.0.8 (10.0.0.8): 56 data bytes
64 bytes from 10.0.0.8: icmp_seq=0 ttl=63 time=92.391 ms
64 bytes from 10.0.0.8: icmp_seq=1 ttl=63 time=21.870 ms
64 bytes from 10.0.0.8: icmp_seq=2 ttl=63 time=198.022 ms
64 bytes from 10.0.0.8: icmp_seq=3 ttl=63 time=22.241 ms
64 bytes from 10.0.0.8: icmp_seq=4 ttl=63 time=174.705 ms
--- 10.0.0.8 ping statistics ---
5 packets transmitted, 5 packets received, 0% packet loss
round-trip min/avg/max/stddev = 21.870/101.846/198.022/74.001 ms

priv-net# ping 10.246.38.107
PING 10.246.38.1 (10.246.38.107): 56 data bytes
64 bytes from 10.246.38.107: icmp_seq=0 ttl=64 time=53.491 ms
64 bytes from 10.246.38.107: icmp_seq=1 ttl=64 time=23.395 ms
64 bytes from 10.246.38.107: icmp_seq=2 ttl=64 time=23.865 ms
64 bytes from 10.246.38.107: icmp_seq=3 ttl=64 time=21.145 ms
64 bytes from 10.246.38.107: icmp_seq=4 ttl=64 time=36.708 ms
--- 10.246.38.107 ping statistics ---
5 packets transmitted, 5 packets received, 0% packet loss
round-trip min/avg/max/stddev = 21.145/31.721/53.491/12.179 ms
```

配置 “隧道” 是比较容易的部分。 配置一条安全链接则是个更加深入的过程。 下面的配置是使用 pre-shared （PSK） RSA 密钥。除了 IP 地址外，两边的 `/usr/local/etc/racoon/racoon.conf` 也几乎相同。

```
path    pre_shared_key  "/usr/local/etc/racoon/psk.txt"; #location of pre-shared key file
log     debug;	#log verbosity setting: set to 'notify' when testing and debugging is complete

padding	# options are not to be changed
{
        maximum_length  20;
        randomize       off;
        strict_check    off;
        exclusive_tail  off;
}

timer	# timing options. change as needed
{
        counter         5;
        interval        20 sec;
        persend         1;
#       natt_keepalive  15 sec;
        phase1          30 sec;
        phase2          15 sec;
}

listen	# address [port] that racoon will listening on
{
        isakmp          172.16.5.4 [500];
        isakmp_natt     172.16.5.4 [4500];
}

remote  192.168.1.12 [500]
{
        exchange_mode   main,aggressive;
        doi             ipsec_doi;
        situation       identity_only;
        my_identifier   address 172.16.5.4;
        peers_identifier        address 192.168.1.12;
        lifetime        time 8 hour;
        passive         off;
        proposal_check  obey;
#       nat_traversal   off;
        generate_policy off;

                        proposal {
                                encryption_algorithm    blowfish;
                                hash_algorithm          md5;
                                authentication_method   pre_shared_key;
                                lifetime time           30 sec;
                                dh_group                1;
                        }
}

sainfo  (address 10.246.38.0/24 any address 10.0.0.0/24 any)	# address $network/$netmask $type address $network/$netmask $type ( $type being any or esp)
{								# $network must be the two internal networks you are joining.
        pfs_group       1;
        lifetime        time    36000 sec;
        encryption_algorithm    blowfish,3des,des;
        authentication_algorithm        hmac_md5,hmac_sha1;
        compression_algorithm   deflate;
}
```

解释所有可用的选项， 连同这些例子里列出的都超越了这份文档的范围。 在 racoon 配置手册页中有着丰富的相关信息。

SPD 策略也需要配置一下， 这样 FreeBSD 和 racoon 就能够加密和解密主机间的网络流量了。

这可以通过在公司的网关上运行一个类似下面简单的 shell 脚本实现。保存到 `/usr/local/etc/racoon/setkey.conf`， 这个文件会被在系统初始化的时候用到。

```
flush;
spdflush;
# To the home network
spdadd 10.246.38.0/24 10.0.0.0/24 any -P out ipsec esp/tunnel/172.16.5.4-192.168.1.12/use;
spdadd 10.0.0.0/24 10.246.38.0/24 any -P in ipsec esp/tunnel/192.168.1.12-172.16.5.4/use;
```

一旦完成后，便使用下面的命令在两边的网关上都启动 racoon：

```
# `/usr/local/sbin/racoon -F -f /usr/local/etc/racoon/racoon.conf -l /var/log/racoon.log`
```

输出将会类似这样的：

```
corp-net# /usr/local/sbin/racoon -F -f /usr/local/etc/racoon/racoon.conf
Foreground mode.
2006-01-30 01:35:47: INFO: begin Identity Protection mode.
2006-01-30 01:35:48: INFO: received Vendor ID: KAME/racoon
2006-01-30 01:35:55: INFO: received Vendor ID: KAME/racoon
2006-01-30 01:36:04: INFO: ISAKMP-SA established 172.16.5.4[500]-192.168.1.12[500] spi:623b9b3bd2492452:7deab82d54ff704a
2006-01-30 01:36:05: INFO: initiate new phase 2 negotiation: 172.16.5.4[0]192.168.1.12[0]
2006-01-30 01:36:09: INFO: IPsec-SA established: ESP/Tunnel 192.168.1.12[0]->172.16.5.4[0] spi=28496098(0x1b2d0e2)
2006-01-30 01:36:09: INFO: IPsec-SA established: ESP/Tunnel 172.16.5.4[0]->192.168.1.12[0] spi=47784998(0x2d92426)
2006-01-30 01:36:13: INFO: respond new phase 2 negotiation: 172.16.5.4[0]192.168.1.12[0]
2006-01-30 01:36:18: INFO: IPsec-SA established: ESP/Tunnel 192.168.1.12[0]->172.16.5.4[0] spi=124397467(0x76a279b)
2006-01-30 01:36:18: INFO: IPsec-SA established: ESP/Tunnel 172.16.5.4[0]->192.168.1.12[0] spi=175852902(0xa7b4d66)
```

确认一下 “隧道” 能正常工作， 切换到另外一个控制台用如下的 [tcpdump(1)](http://www.FreeBSD.org/cgi/man.cgi?query=tcpdump&sektion=1&manpath=freebsd-release-ports) 命令查看网络流量。根据需要替换掉下面的 `em0` 网卡界面。

```
# `tcpdump -i em0 host 172.16.5.4 and dst 192.168.1.12`
```

控制台上能看到如下类似的输出。如果不是这样的话， 可能就有些问题了，调试的话需要用到返回的数据。

```
01:47:32.021683 IP corporatenetwork.com > 192.168.1.12.privatenetwork.com: ESP(spi=0x02acbf9f,seq=0xa)
01:47:33.022442 IP corporatenetwork.com > 192.168.1.12.privatenetwork.com: ESP(spi=0x02acbf9f,seq=0xb)
01:47:34.024218 IP corporatenetwork.com > 192.168.1.12.privatenetwork.com: ESP(spi=0x02acbf9f,seq=0xc)
```

此刻，两个网络就好像是同一个网络的一部分一样。 而且这两个网络很可能也应该有防火墙的保护。 要使得这两个网络能互相访问，就需要添加一些进出包的规则。 就 [ipfw(8)](http://www.FreeBSD.org/cgi/man.cgi?query=ipfw&sektion=8&manpath=freebsd-release-ports) 来说，加入下面的几行进配置文件：

```
ipfw add 00201 allow log esp from any to any
ipfw add 00202 allow log ah from any to any
ipfw add 00203 allow log ipencap from any to any
ipfw add 00204 allow log udp from any 500 to any
```

### 注意:

规则号可能需要根据现有机器上的配置做相应的修改。

对于 [pf(4)](http://www.FreeBSD.org/cgi/man.cgi?query=pf&sektion=4&manpath=freebsd-release-ports) 或者 [ipf(8)](http://www.FreeBSD.org/cgi/man.cgi?query=ipf&sektion=8&manpath=freebsd-release-ports) 的用户， 下面的几行规则应该可行：

```
pass in quick proto esp from any to any
pass in quick proto ah from any to any
pass in quick proto ipencap from any to any
pass in quick proto udp from any port = 500 to any port = 500
pass in quick on gif0 from any to any
pass out quick proto esp from any to any
pass out quick proto ah from any to any
pass out quick proto ipencap from any to any
pass out quick proto udp from any port = 500 to any port = 500
pass out quick on gif0 from any to any
```

最后，要允许机器初始化的时候开始 VPN 支持，在 `/etc/rc.conf` 中加入以下的几行：

```
ipsec_enable="YES"
ipsec_program="/usr/local/sbin/setkey"
ipsec_file="/usr/local/etc/racoon/setkey.conf" # allows setting up spd policies on boot
racoon_enable="yes"
```

## 15.10. OpenSSH

原著 Chern Lee.

OpenSSH 是一组用于安全地访问远程计算机的连接工具。 它可以作为 `rlogin`、 `rsh` `rcp` 以及 `telnet` 的直接替代品使用。 更进一步， 其他任何 TCP/IP 连接都可以通过 SSH 安全地进行隧道/转发。 OpenSSH 对所有的传输进行加密， 从而有效地阻止了窃听、 连接劫持， 以及其他网络级的攻击。

OpenSSH 由 OpenBSD project 维护， 它基于 SSH v1.2.12 并包含了最新的错误修复和更新。 它同时兼容 SSH 协议的 1 和 2 两个版本。

### 15.10.1. 使用 OpenSSH 的好处

一般说来， 在使用 [telnet(1)](http://www.FreeBSD.org/cgi/man.cgi?query=telnet&sektion=1&manpath=freebsd-release-ports) 或 [rlogin(1)](http://www.FreeBSD.org/cgi/man.cgi?query=rlogin&sektion=1&manpath=freebsd-release-ports) 时， 数据是以未经加密的明文的形式发送的。 这样一来， 在客户机和服务器之间的网络上运行的听包程序， 便可以在会话中窃取到传输的用户名/密码和数据。 OpenSSH 提供了多种的身份验证和加密方法来防止这种情况的发生。

### 15.10.2. 启用 sshd

sshd 的启用是作为 FreeBSD 安装中 `Standard` 安装过程中的一步来进行的。 要查看 sshd 是否已被启用， 请检查 `rc.conf` 文件中的：

```
sshd_enable="YES"
```

这表示在下次系统启动时加载 OpenSSH 的服务程序 [sshd(8)](http://www.FreeBSD.org/cgi/man.cgi?query=sshd&sektion=8&manpath=freebsd-release-ports)。 此外， 也可以手动使用 [rc(8)](http://www.FreeBSD.org/cgi/man.cgi?query=rc&sektion=8&manpath=freebsd-release-ports) 脚本 `/etc/rc.d/sshd` 来启动 OpenSSH：

```
/etc/rc.d/sshd start
```

### 15.10.3. SSH 客户

[ssh(1)](http://www.FreeBSD.org/cgi/man.cgi?query=ssh&sektion=1&manpath=freebsd-release-ports) 的工作方式和 [rlogin(1)](http://www.FreeBSD.org/cgi/man.cgi?query=rlogin&sektion=1&manpath=freebsd-release-ports) 非常类似。

```
# `ssh user@example.com`
Host key not found from the list of known hosts.
Are you sure you want to continue connecting (yes/no)? `yes`
Host 'example.com' added to the list of known hosts.
user@example.com's password: `*******`
```

登录过程和使用 `rlogin` 或 `telnet` 建立的会话非常类似。 在连接时， SSH 会利用一个密钥指纹系统来验证服务器的真实性。 只有在第一次连接时， 用户会被要求输入 `yes`。 之后的连接将会验证预先保存下来的密钥指纹。 如果保存的指纹与登录时接收到的不符， 则将会给出警告。 指纹保存在 `~/.ssh/known_hosts` 中， 对于 SSH v2 指纹， 则是 `~/.ssh/known_hosts2`。

默认情况下， 较新版本的 OpenSSH 只接受 SSH v2 连接。 如果能用版本 2 则客户程序会自动使用， 否则它会返回使用版本 1 的模式。 此外， 也可以通过命令行参数 `-1` 或 `-2` 来相应地强制使用版本 1 或 2。 保持客户端的版本 1 能力是为了考虑较早版本的兼容性。

### 15.10.4. 安全复制

[scp(1)](http://www.FreeBSD.org/cgi/man.cgi?query=scp&sektion=1&manpath=freebsd-release-ports) 命令和 [rcp(1)](http://www.FreeBSD.org/cgi/man.cgi?query=rcp&sektion=1&manpath=freebsd-release-ports); 的用法类似， 它用于将文件复制到远程的机器上， 或复制过来， 区别是它是安全的。

```
#  `scp user@example.com:/COPYRIGHT COPYRIGHT`
user@example.com's password: `*******`
COPYRIGHT            100% |*****************************|  4735
00:00
#
```

由于先前的例子中已经保存了指纹， 使用 [scp(1)](http://www.FreeBSD.org/cgi/man.cgi?query=scp&sektion=1&manpath=freebsd-release-ports) 时会自动地加以验证。

[scp(1)](http://www.FreeBSD.org/cgi/man.cgi?query=scp&sektion=1&manpath=freebsd-release-ports) 使用的参数同 [cp(1)](http://www.FreeBSD.org/cgi/man.cgi?query=cp&sektion=1&manpath=freebsd-release-ports) 类似。 第一个参数是一个或一组文件， 然后是复制的目标。 由于文件是通过 SSH 在网上传递的， 因此某些文件的名字需要写成 `用户名@主机名:<远程文件路径>`。

### 15.10.5. 配置

针对 OpenSSH 服务程序和客户端的系统级配置文件在 `/etc/ssh` 目录中。

`ssh_config` 用于配置客户端的设定， 而 `sshd_config` 则用于配置服务器端。

另外 `sshd_program` (默认是 `/usr/sbin/sshd`)， 以及 `sshd_flags` 这两个 `rc.conf` 选项提供了更多的配置选择。

### 15.10.6. ssh-keygen

用于取代口令的一种方法是使用 [ssh-keygen(1)](http://www.FreeBSD.org/cgi/man.cgi?query=ssh-keygen&sektion=1&manpath=freebsd-release-ports) 来生成 DSA 或 RSA 密钥对用于验证用户的身份：

```
% `ssh-keygen -t dsa`
Generating public/private dsa key pair.
Enter file in which to save the key (/home/user/.ssh/id_dsa):
Created directory '/home/user/.ssh'.
Enter passphrase (empty for no passphrase):
Enter same passphrase again:
Your identification has been saved in /home/user/.ssh/id_dsa.
Your public key has been saved in /home/user/.ssh/id_dsa.pub.
The key fingerprint is:
bb:48:db:f2:93:57:80:b6:aa:bc:f5:d5:ba:8f:79:17 user@host.example.com

```

[ssh-keygen(1)](http://www.FreeBSD.org/cgi/man.cgi?query=ssh-keygen&sektion=1&manpath=freebsd-release-ports) 会生成一个包含公私钥对用于验证身份。 私钥将保存到 `~/.ssh/id_dsa` 或 `~/.ssh/id_rsa`， 而公钥则被存放到 `~/.ssh/id_dsa.pub` 或 `~/.ssh/id_rsa.pub`， 文件名取决于您选择的 DSA 和 RSA 密钥类型。 RSA 或者 DSA 公钥必须被存放到远程机器上的 `~/.ssh/authorized_keys` 才能够使系统正确运转。

这将允许从远程连接时以基于 SSH 密钥的验证来代替口令验证。

如果在 [ssh-keygen(1)](http://www.FreeBSD.org/cgi/man.cgi?query=ssh-keygen&sektion=1&manpath=freebsd-release-ports) 中使用了通行字， 则每次使用私钥时都需要输入它。 [ssh-agent(1)](http://www.FreeBSD.org/cgi/man.cgi?query=ssh-agent&sektion=1&manpath=freebsd-release-ports) 能够缓解多次输入长通行字的压力， 并将在接下来的 第 15.10.7 节 “ssh-agent 和 ssh-add” 予以详述。

### 警告:

选项和配置文件可能随 OpenSSH 的版本不同而不同； 为了避免出现问题， 您应参考 [ssh-keygen(1)](http://www.FreeBSD.org/cgi/man.cgi?query=ssh-keygen&sektion=1&manpath=freebsd-release-ports) 联机手册。

这将使到远程机器的连接基于 SSH 密钥而不是口令。

如果在运行 [ssh-keygen(1)](http://www.FreeBSD.org/cgi/man.cgi?query=ssh-keygen&sektion=1&manpath=freebsd-release-ports) 时使用了通行字， 每次使用私钥的时候用户都将被要求输入通行字。 [ssh-agent(1)](http://www.FreeBSD.org/cgi/man.cgi?query=ssh-agent&sektion=1&manpath=freebsd-release-ports) 能够减缓重复输入较长通行字的负担， 有关更详细的探究在 第 15.10.7 节 “ssh-agent 和 ssh-add” 下一节 .

### 警告:

随着你系统上的 OpenSSH 版本的不同，各种选项和配置文件也会不同； 为了避免此类问题， 你需要参阅 [ssh-keygen(1)](http://www.FreeBSD.org/cgi/man.cgi?query=ssh-keygen&sektion=1&manpath=freebsd-release-ports) 联机手册。

### 15.10.7. ssh-agent 和 ssh-add

[ssh-agent(1)](http://www.FreeBSD.org/cgi/man.cgi?query=ssh-agent&sektion=1&manpath=freebsd-release-ports) 和 [ssh-add(1)](http://www.FreeBSD.org/cgi/man.cgi?query=ssh-add&sektion=1&manpath=freebsd-release-ports) 这两个工具， 提供了一种将 SSH 秘钥加载到内存中以便使用， 而不必每次都输入通行字的方法。

[ssh-agent(1)](http://www.FreeBSD.org/cgi/man.cgi?query=ssh-agent&sektion=1&manpath=freebsd-release-ports) 工具能够使用加载到其中的私钥来处理验证过程。 [ssh-agent(1)](http://www.FreeBSD.org/cgi/man.cgi?query=ssh-agent&sektion=1&manpath=freebsd-release-ports) 应被用于启动另一个应用程序。 最基本的用法是， 使用它来启动 shell， 而高级一些的用法则是用它来启动窗口管理器。

要在 shell 中使用 [ssh-agent(1)](http://www.FreeBSD.org/cgi/man.cgi?query=ssh-agent&sektion=1&manpath=freebsd-release-ports)， 首先应把 shell 作为参数来启动它。 随后， 应通过 [ssh-add(1)](http://www.FreeBSD.org/cgi/man.cgi?query=ssh-add&sektion=1&manpath=freebsd-release-ports) 并输入通行字， 来向它提供身份验证信息。 一旦这些步骤都做完了， 用户就应该能够 [ssh(1)](http://www.FreeBSD.org/cgi/man.cgi?query=ssh&sektion=1&manpath=freebsd-release-ports) 到任何一个安装了对应公钥的机器了。 例如：

```
% ssh-agent *`csh`*
% ssh-add
Enter passphrase for /home/user/.ssh/id_dsa:
Identity added: /home/user/.ssh/id_dsa (/home/user/.ssh/id_dsa)
%
```

要在 X11 中使用 [ssh-agent(1)](http://www.FreeBSD.org/cgi/man.cgi?query=ssh-agent&sektion=1&manpath=freebsd-release-ports)， 调用 [ssh-agent(1)](http://www.FreeBSD.org/cgi/man.cgi?query=ssh-agent&sektion=1&manpath=freebsd-release-ports) 的过程应置于 `~/.xinitrc` 之中。 这将把 [ssh-agent(1)](http://www.FreeBSD.org/cgi/man.cgi?query=ssh-agent&sektion=1&manpath=freebsd-release-ports) 服务提供给所有在 X11 中运行的程序。 下面是一个 `~/.xinitrc` 文件的实例：

```
exec ssh-agent *`startxfce4`*
```

这将启动 [ssh-agent(1)](http://www.FreeBSD.org/cgi/man.cgi?query=ssh-agent&sektion=1&manpath=freebsd-release-ports)， 而后者将在每次 X11 启动时运行 XFCE。 作完这些之后就可以重启 X11 以便使修改生效。 随后您就可以运行 [ssh-add(1)](http://www.FreeBSD.org/cgi/man.cgi?query=ssh-add&sektion=1&manpath=freebsd-release-ports) 来加载全部 SSH 密钥了。

### 15.10.8. SSH 隧道

OpenSSH 能够创建隧道以便用加密的会话来封装其他协议。

下面的命令告诉 [ssh(1)](http://www.FreeBSD.org/cgi/man.cgi?query=ssh&sektion=1&manpath=freebsd-release-ports) 为 telnet 创建一个隧道：

```
% `ssh -2 -N -f -L 5023:localhost:23 user@foo.example.com`
%
```

上述 `ssh` 命令使用了下面这些选项：

`-2`

强制 `ssh` 使用第 2 版的协议 (如果需要和较老的 SSH 一同工作请不要使用这个选项)。

`-N`

表示不使用命令行， 或者说只使用隧道。 如果省略， `ssh` 将同时初始化会话。

`-f`

强制 `ssh` 在后台执行。

`-L`

表示产生一条 *`本地端口:远程主机:远程端口`* 形式的隧道。

`user@foo.example.com`

远程 SSH 服务器。

SSH 隧道通过监听 `localhost` 上面指定端口来完成工作。 它将把本机主机/端口上接收到的连接通过 SSH 连接转发到远程主机/端口。

本例中， 位于 `localhost` 的 *`5023`* 端口 被用于转发 `localhost` 的连接到远程主机的 *`23`* 端口。 由于 *`23`* 是 telnet 使用的， 因此它将通过 SSH 隧道完成 telnet 会话。

这可以用来封装任意不安全的 TCP 协议， 例如 SMTP、 POP3、 FTP 等等。

例 15.1. 使用 SSH 为 SMTP 创建安全隧道

```
% `ssh -2 -N -f -L 5025:localhost:25 user@mailserver.example.com`
user@mailserver.example.com's password: `*****`
% `telnet localhost 5025`
Trying 127.0.0.1...
Connected to localhost.
Escape character is '^]'.
220 mailserver.example.com ESMTP
```

这可以与 [ssh-keygen(1)](http://www.FreeBSD.org/cgi/man.cgi?query=ssh-keygen&sektion=1&manpath=freebsd-release-ports) 以及额外的用户帐号配合来建立一个更透明的 SSH 隧道环境。 密钥可以被用在需要输入口令的地方， 而且可以为不同的用户配置不同的隧道。

#### 15.10.8.1. 实用的 SSH 通道例子

##### 15.10.8.1.1. 加强 POP3 服务的安全

工作时， 有一个允许外来连接的 SSH 服务器。 同一个办公网络中有一个邮件服务器提供 POP3 服务。 这个网络， 或从您家到办公室的网络可能不， 或不完全可信。 基于这样的原因， 您需要以安全的方式来查看邮件。 解决方法是创建一个到办公室 SSH 服务器的连接， 并通过这个连接来访问 POP3 服务：

```
% `ssh -2 -N -f -L 2110:mail.example.com:110 user@ssh-server.example.com`
user@ssh-server.example.com's password: `******`
```

当这个通道连上时， 您可以把 POP3 请求发到 `localhost` 端口 2110。 这个连接将通过通道安全地转发到 `mail.example.com`。

##### 15.10.8.1.2. 绕过严厉的防火墙

一些大脑长包的网络管理员会使用一些极端的防火墙策略， 不仅过滤进入的连接， 而且也过滤连出的连接。 一些时候您可能只能连接远程机器 22 端口，以及 80 端口用来进行 SSH 和网页浏览。

您可能希望访问一些其它的 (也许与工作无关的) 服务， 例如提供音乐的 Ogg Vorbis 流媒体服务器。 如果 Ogg Vorbis server 在 22 或 80 端口以外的端口播放音乐， 则您将无法访问它。

解决方法是建立一个到您的网络的防火墙之外的网络上的 SSH 服务器， 并通过它提供的通道连接到 Ogg Vorbis 服务器上。

```
% `ssh -2 -N -f -L 8888:music.example.com:8000 user@unfirewalled-system.example.org`
user@unfirewalled-system.example.org's password: `*******`
```

现在您可以把客户程序指定到 `localhost` 的 8888 端口， 它将把请求转发给 `music.example.com` 的 8000 端口， 从而绕过防火墙。

### 15.10.9. 允许用户登录 `AllowUsers` 选项

通常限制哪些用户能够登录， 以及从何处登录会是好主意。 采用 `AllowUsers` 选项能够方便地达到这一目的。 例如， 想要只允许 `root` 用户从 `192.168.1.32` 登录， 就可以在 `/etc/ssh/sshd_config` 文件中加入下述设置：

```
AllowUsers root@192.168.1.32
```

要允许用户 `admin` 从任何地方登录， 则只需列出用户名：

```
AllowUsers admin
```

可以在同一行指定多个用户， 例如：

```
AllowUsers root@192.168.1.32 admin
```

### 注意:

列出需要登录机器的用户很重要； 否则他们将被锁在外面。

在完成对 `/etc/ssh/sshd_config` 的修改之后您必须告诉 [sshd(8)](http://www.FreeBSD.org/cgi/man.cgi?query=sshd&sektion=8&manpath=freebsd-release-ports) 重新加载其配置文件， 方法是执行：

```
# `/etc/rc.d/sshd reload`
```

### 15.10.10. 进一步的资料

[OpenSSH](http://www.openssh.com/)

[ssh(1)](http://www.FreeBSD.org/cgi/man.cgi?query=ssh&sektion=1&manpath=freebsd-release-ports) [scp(1)](http://www.FreeBSD.org/cgi/man.cgi?query=scp&sektion=1&manpath=freebsd-release-ports) [ssh-keygen(1)](http://www.FreeBSD.org/cgi/man.cgi?query=ssh-keygen&sektion=1&manpath=freebsd-release-ports) [ssh-agent(1)](http://www.FreeBSD.org/cgi/man.cgi?query=ssh-agent&sektion=1&manpath=freebsd-release-ports) [ssh-add(1)](http://www.FreeBSD.org/cgi/man.cgi?query=ssh-add&sektion=1&manpath=freebsd-release-ports) [ssh_config(5)](http://www.FreeBSD.org/cgi/man.cgi?query=ssh_config&sektion=5&manpath=freebsd-release-ports)

[sshd(8)](http://www.FreeBSD.org/cgi/man.cgi?query=sshd&sektion=8&manpath=freebsd-release-ports) [sftp-server(8)](http://www.FreeBSD.org/cgi/man.cgi?query=sftp-server&sektion=8&manpath=freebsd-release-ports) [sshd_config(5)](http://www.FreeBSD.org/cgi/man.cgi?query=sshd_config&sektion=5&manpath=freebsd-release-ports)

## 15.11. 文件系统访问控制表

作者 Tom Rhodes.

与文件系统在其他方面的加强， 如快照等一道， FreeBSD 提供了通过文件系统访问控制表 (ACL) 实现的安全机制。

访问控制表以高度兼容 (POSIX®.1e) 的方式扩展了标准的 UNIX® 权限模型。 这一特性使得管理员能够利用其优势设计更为复杂的安全模型。

如果想为 UFS 文件系统启用 ACL 支持， 则需要添加下列选项：

```
options UFS_ACL
```

并重新编译内核。 如果没有将这个选项编译进内核， 则在挂接支持 ACL 的文件系统时将会收到警告。 这个选项在 `GENERIC` 内核中已经包含了。 ACL 依赖于在文件系统上启用扩展属性。 在新一代的 UNIX® 文件系统， UFS2 中内建了这种支持。

### 注意:

在 UFS1 上配置扩展属性需要比 UFS2 更多的管理开销。 而且， 在 UFS2 上的扩展属性的性能也有极大的提高。 因此， 如果想要使用访问控制表， 推荐使用 UFS2 而不是 UFS1。

ACL 可以在挂接时通过选项 `acls` 来启动， 它可以加入 `/etc/fstab`。 另外， 也可以通过使用 [tunefs(8)](http://www.FreeBSD.org/cgi/man.cgi?query=tunefs&sektion=8&manpath=freebsd-release-ports) 修改超级块中的 ACL 标记来持久性地设置自动的挂接属性。 一般而言， 后一种方法是推荐的做法， 其原因是：

*   挂接时的 ACL 标记无法被重挂接 ([mount(8)](http://www.FreeBSD.org/cgi/man.cgi?query=mount&sektion=8&manpath=freebsd-release-ports) `-u`) 改变， 只有完整地 [umount(8)](http://www.FreeBSD.org/cgi/man.cgi?query=umount&sektion=8&manpath=freebsd-release-ports) 并做一次新的 [mount(8)](http://www.FreeBSD.org/cgi/man.cgi?query=mount&sektion=8&manpath=freebsd-release-ports) 才能改变它。 这意味着 ACL 状态在系统启动之后就不可能在 root 文件系统上发生变化了。 另外也没有办法改变正在使用的文件系统的这个状态。

*   在超级块中的设置将使得文件系统总被以启用 ACL 的方式挂接， 即使在 `fstab` 中的对应项目没有作设置， 或设备顺序发生变化时也是如此。 这避免了不慎将文件系统以没有启用 ACL 的状态挂接， 从而避免没有强制 ACL 这样的安全问题。

### 注意:

可以修改 ACL 行为， 以允许在没有执行一次全新的 [mount(8)](http://www.FreeBSD.org/cgi/man.cgi?query=mount&sektion=8&manpath=freebsd-release-ports) 的情况下启用它， 但我们认为， 不鼓励在未启用 ACL 时这么做是有必要的， 因为如果启用了 ACL， 然后关掉它， 然后在没有刷新扩展属性的情况下重新启用它是很容易造成问题的。 一般而言， 一旦启用了文件系统的 ACL 就不应该再关掉它， 因为此时的文件系统的保护措施可能和用户所期待的样子不再兼容， 而重新启用 ACL 将重新把先前的 ACL 附着到文件上， 而由于它们的权限发生了变化， 就很可能造成无法预期的行为。

在查看目录时， 启用了 ACL 的文件将在通常的属性后面显示 `+` (加号)。 例如：

```
drwx------  2 robert  robert  512 Dec 27 11:54 private
drwxrwx---+ 2 robert  robert  512 Dec 23 10:57 directory1
drwxrwx---+ 2 robert  robert  512 Dec 22 10:20 directory2
drwxrwx---+ 2 robert  robert  512 Dec 27 11:57 directory3
drwxr-xr-x  2 robert  robert  512 Nov 10 11:54 public_html
```

这里我们看到了 `directory1`、 `directory2`， 以及 `directory3` 目录使用了 ACL。 而 `public_html` 则没有。

### 15.11.1. 使用 ACL

文件系统 ACL 可以使用 [getfacl(1)](http://www.FreeBSD.org/cgi/man.cgi?query=getfacl&sektion=1&manpath=freebsd-release-ports) 工具来查看。 例如， 如果想查看 `test` 的 ACL 设置， 所用的命令是：

```
% `getfacl test`
	#file:
	#owner:1001
	#group:1001
	user::rw-
	group::r--
	other::r--
```

要修改这个文件上的 ACL 设置， 则需要使用 [setfacl(1)](http://www.FreeBSD.org/cgi/man.cgi?query=setfacl&sektion=1&manpath=freebsd-release-ports) 工具。 例如：

```
% `setfacl -k test`
```

`-k` 参数将把所有当前定义的 ACL 从文件或文件系统中删除。 一般来说应该使用 `-b` 因为它会保持让 ACL 正常工作的那些项不变。

```
% `setfacl -m u:trhodes:rwx,group:web:r--,o::--- test`
```

在前面的命令中， `-m` 选项被用来修改默认的 ACL 项。由于已经被先前的命令 删除，因此没有预先定义的项，于是默认的选项被恢复，并附加上指定的选项。 请小心地检查，如果您加入了一个不存在的用户或组，那么将会在 `stdout` 得到一条 Invalid argument 的错误提示。

## 15.12. 监视第三方安全问题

Contributed by Tom Rhodes.

过去几年中， 安全领域在如何处理漏洞的评估方面取得了长足的进步。 几乎每一个操作系统都越来越多地安装和配置了第三方工具， 而系统被入侵的威胁也随之增加。

漏洞的评估是安全的一个关键因素， 尽管 FreeBSD 会发布基本系统的安全公告， 然而为每一个第三方工具都发布安全公告则超出了 FreeBSD Project 的能力。 在这一前提下， 一种减轻第三方漏洞的威胁， 并警告管理员存在已知的安全问题的方法也就应运而生。 名为 Portaudit 的 FreeBSD 附加工具能够帮助您达成这一目的。

[ports-mgmt/portaudit](http://www.freebsd.org/cgi/url.cgi?ports/ports-mgmt/portaudit/pkg-descr) port 会下载一个数据库， 这一数据库是由 FreeBSD Security Team 和 ports 开发人员维护的， 其中包含了已知的安全问题。

要开始使用 Portaudit， 需要首先从 Ports Collection 安装它：

```
# `cd /usr/ports/ports-mgmt/portaudit && make install clean`
```

在安装过程中， [periodic(8)](http://www.FreeBSD.org/cgi/man.cgi?query=periodic&sektion=8&manpath=freebsd-release-ports) 的配置文件将被修改， 以便让 Portaudit 能够在每天的安全审计过程中运行。 一定要保证发到 `root` 帐号的每日安全审计邮件确实有人在读。 除此之外不需要进行更多的配置了。

安装完成之后， 管理员可以通过下面的命令来更新数据库， 并查看目前安装的软件包中所存在的已知安全漏洞：

```
# `portaudit -Fda`
```

### 注意:

由于每天执行 [periodic(8)](http://www.FreeBSD.org/cgi/man.cgi?query=periodic&sektion=8&manpath=freebsd-release-ports) 时都会自动更新数据库， 因此， 运行这条命令是可选的。 在这里只是作为例子给出。

在任何时候， 如果希望对通过 Ports Collection 安装的第三方软件工具进行审计， 管理员都可以使用下面的命令：

```
# `portaudit -a`
```

针对存在漏洞的软件包， Portaudit 将生成类似下面的输出：

```
Affected package: cups-base-1.1.22.0_1
Type of problem: cups-base -- HPGL buffer overflow vulnerability.
Reference: <http://www.FreeBSD.org/ports/portaudit/40a3bca2-6809-11d9-a9e7-0001020eed82.html>

1 problem(s) in your installed packages found.

You are advised to update or deinstall the affected package(s) immediately.
```

通过访问上面给出的 URL， 管理员能够了解关于那个漏洞的进一步信息。 这些信息通常包括受到影响的 FreeBSD Port 版本， 以及其他可能包含安全公告的网站。

简而言之， Portaudit 是一个强大的工具， 并能够配合 Portupgrade port 来非常有效地工作。

## 15.13. FreeBSD 安全公告

作者 Tom Rhodes.

像其它具有产品级品质的操作系统一样， FreeBSD 会发布 “安全公告”。 通常这类公告会只有在相应的发行版本已经正确地打过补丁之后发到安全邮件列表并在勘误中说明。 本节将介绍什么是安全公告， 如何理解它， 以及为系统打补丁的具体步骤。

### 15.13.1. 安全公告看上去是什么样子？

FreeBSD 安全公告的样式类似下面的范例， 这一例子来自 [freebsd-security-notifications](http://lists.FreeBSD.org/mailman/listinfo/freebsd-security-notifications) 邮件列表。

```
=============================================================================
FreeBSD-SA-XX:XX.UTIL                                       Security Advisory
                                                          The FreeBSD Project

Topic:          denial of service due to some problem![1](img/1.png)

Category:       core![2](img/2.png)
Module:         sys![3](img/3.png)
Announced:      2003-09-23![4](img/4.png)
Credits:        Person![5](img/5.png)
Affects:        All releases of FreeBSD![6](img/6.png)
                FreeBSD 4-STABLE prior to the correction date
Corrected:      2003-09-23 16:42:59 UTC (RELENG_4, 4.9-PRERELEASE)
                2003-09-23 20:08:42 UTC (RELENG_5_1, 5.1-RELEASE-p6)
                2003-09-23 20:07:06 UTC (RELENG_5_0, 5.0-RELEASE-p15)
                2003-09-23 16:44:58 UTC (RELENG_4_8, 4.8-RELEASE-p8)
                2003-09-23 16:47:34 UTC (RELENG_4_7, 4.7-RELEASE-p18)
                2003-09-23 16:49:46 UTC (RELENG_4_6, 4.6-RELEASE-p21)
                2003-09-23 16:51:24 UTC (RELENG_4_5, 4.5-RELEASE-p33)
                2003-09-23 16:52:45 UTC (RELENG_4_4, 4.4-RELEASE-p43)
                2003-09-23 16:54:39 UTC (RELENG_4_3, 4.3-RELEASE-p39)![7](img/7.png)
CVE Name:       CVE-XXXX-XXXX![8](img/8.png)

For general information regarding FreeBSD Security Advisories,
including descriptions of the fields above, security branches, and the
following sections, please visit
http://www.FreeBSD.org/security/.

I.   Background![9](img/9.png)

II.  Problem Description![10](img/10.png)

III. Impact![11](img/11.png)

IV.  Workaround![12](img/12.png)

V.   Solution![13](img/13.png)

VI.  Correction details![14](img/14.png)

VII. References![15](img/15.png)
```

| ![1](img/#co-topic) | `Topic`(标题) 一栏说明了问题到底是什么。 它基本上是对所发现的安全问题及其所涉及的工具的描述。 |
| ![2](img/#co-category) | `Category` (分类) 是指系统中受到影响的组件， 这一栏可能是 `core`、 `contrib`， 或者 `ports` 之一。 `core` 分类表示安全弱点影响到了 FreeBSD 操作系统的某个核心组件。 `contrib` 分类表示弱点存在于某个捐赠给 FreeBSD Project 的软件， 例如 sendmail。 最后是 `ports`， 它表示该弱点影响了 Ports Collection 中的某个第三方软件。 |
| ![3](img/#co-module) | `Module`(模块) 一栏给出了组件的具体位置， 例如 `sys`。 在这个例子中， 可以看到 `sys` 模块是存在问题的； 因此， 这个漏洞会影响某个在内核中的组件。 |
| ![4](img/#co-announce) | `Announced`(发布时间) 一栏反映了与安全公告有关的数据是什么时候公之于众的。 这说明安全团队已经证实问题确实存在， 而补丁已经写入了 FreeBSD 的代码库。 |
| ![5](img/#co-credit) | `Credits`(作者) 一栏给出了注意到问题存在并报告它的个人或团体。 |
| ![6](img/#co-affects) | The `Affects`(影响范围) 一栏给出了 FreeBSD 的哪些版本存在这个漏洞。 对于内核来说， 检视受影响的文件上执行的 `ident` 输出可以帮助确认文件版本。 对于 ports， 版本号在 `/var/db/pkg` 里面的 port 的名字后面列出。 如果系统没有与 FreeBSD CVS 代码库同步并每日构建， 它很可能是有问题的。 |
| ![7](img/#co-corrected) | `Corrected`(修正时间) 一栏给出了发行版本中修正问题的具体日期、时间和时差。 |
| ![8](img/#co-cve) | 在公共漏洞数据库 (Common Vulnerabilities Database) 系统中预留的， 用于查看漏洞的标识信息。 |
| ![9](img/#co-backround) | `Background`(技术背景) 一栏提供了受影响的组件的作用。 多数时候这一部分会说明为什么 FreeBSD 中包含了它， 它的作用， 以及它的一些原理。 |
| ![10](img/#co-descript) | `Problem Description`(问题描述) 一栏深入阐述安全漏洞的技术细节。 这部分有时会包括有问题的代码相关的详细情况， 甚至是这个部件如何能够被恶意利用并打开漏洞的细节。 |
| ![11](img/#co-impact) | `Impact`(影响) 一栏描述了问题能够造成的影响类型。 例如， 可能导致拒绝服务攻击， 权限提升， 甚至导致得到超级用户的权限。 |
| ![12](img/#co-workaround) | `Workaround`(应急方案) 一栏给出了系统管理员在暂时无法升级系统时可以采取的临时性对策。 这些原因可能包括时间限制， 网络资源的限制， 或其它因素。 不过无论如何， 安全不能够被轻视， 有问题的系统要么应该打补丁， 要么应该实施这种应急方案。 |
| ![13](img/#co-solution) | `Solution`(解决方案) 一栏提供了如何给有问题的系统打补丁的方法。 这是经过逐步测试和验证过的给系统打补丁并让其安全地工作的方法。 |
| ![14](img/#co-details) | =`Correction Details`(修正细节) 一栏展示了针对 CVS 分支或某个发行版的修正特征。 同时也提供了每个分支上相关文件的版本号。 |
| ![15](img/#co-ref) | `References`(文献) 一栏通常会给出其它信息的来源。 这可能包括 URL， 书籍、 邮件列表以及新闻组。 |

## 15.14. 进程记帐

Contributed by Tom Rhodes.

进程记帐是一种管理员可以使用的跟踪系统资源使用情况的手段， 包括它们分配给了哪些用户、 提供系统监视手段， 并且可以精细到用户执行的每一个命令。

当然， 这种做法是兼有利弊的。 它的好处是， 查找入侵时可以迅速把范围缩小到攻击者进入的时刻； 而这样做的缺点， 则是记帐会产生大量的日志， 因而需要很多磁盘空间来存储它们。 这一节将带领管理员一步一步地配置基本的进程记帐。

### 15.14.1. 启用并利用进程记帐

在使用进程记帐之前， 必须先启用它。 要完成这项工作， 需要运行下面的命令：

```
# `touch /var/account/acct`

# `accton /var/account/acct`

# `echo 'accounting_enable="YES"' >> /etc/rc.conf`
```

一旦启用之后， 记帐就会开始跟踪 CPU 统计数据、 命令， 等等。 所有的记帐日志不是以可读的方式记录的， 要查看它们， 需要使用 [sa(8)](http://www.FreeBSD.org/cgi/man.cgi?query=sa&sektion=8&manpath=freebsd-release-ports) 这个工具。 如果没有给出其他参数， 则 `sa` 将按用户， 以分钟为单位显示他们所使用的时间、 总共的 CPU 和用户时间， 以及平均的 I/O 操作数目， 等等。

要显示关于刚刚发出的命令的相关信息， 则应使用 [lastcomm(1)](http://www.FreeBSD.org/cgi/man.cgi?query=lastcomm&sektion=1&manpath=freebsd-release-ports) 工具。 `lastcomm` 命令可以用来显示在某一 [ttys(5)](http://www.FreeBSD.org/cgi/man.cgi?query=ttys&sektion=5&manpath=freebsd-release-ports) 上的用户信息， 例如：

```
# `lastcomm ls
	trhodes ttyp1`
```

将会显示出所有已知的 `trhodes` 在 `ttyp1` 终端上执行 `ls` 的情况。

更多的可用选项在联机手册 [lastcomm(1)](http://www.FreeBSD.org/cgi/man.cgi?query=lastcomm&sektion=1&manpath=freebsd-release-ports)、 [acct(5)](http://www.FreeBSD.org/cgi/man.cgi?query=acct&sektion=5&manpath=freebsd-release-ports) 和 [sa(8)](http://www.FreeBSD.org/cgi/man.cgi?query=sa&sektion=8&manpath=freebsd-release-ports) 中有所介绍。

## 第 16 章 Jails

原作 Matteo Riondato.

## 16.1. 概述

这一章将为您介绍 FreeBSD jail 是什么， 以及如何使用它们。 Jail， 有时也被认为是对 *chroot 环境* 的一种增强型替代品， 对于管理员而言是非常强大的工具， 同时， 它的一些基本用法， 对高级用户而言也相当有用。

读完这章， 您将了解：

*   jail 是什么， 以及它在您安装的 FreeBSD 中所能发挥的作用。

*   如何联编、 启动和停止 jail。

*   如何从 jail 内部或主机上进行管理的一些基础知识。

其他一些能够为您提供关于 jail 的有用信息的地方还有：

*   [jail(8)](http://www.FreeBSD.org/cgi/man.cgi?query=jail&sektion=8&manpath=freebsd-release-ports) 联机手册。 这是关于 `jail` ── 用于在 FreeBSD 中启动、 停止和控制 FreeBSD jails ── 工具的完整说明书。

*   邮件列表及其存档。 由 [FreeBSD 邮件列表服务器](http://lists.FreeBSD.org/mailman/listinfo) 提供的 [FreeBSD 一般问题邮件列表](http://lists.FreeBSD.org/mailman/listinfo/freebsd-questions) 和其他邮件列表的存档， 已经包含了一系列关于 jails 的有价值的信息。 通常搜索存档或询问 [freebsd-questions](http://lists.FreeBSD.org/mailman/listinfo/freebsd-questions) 邮件列表能够给您带来很多有用的信息。

## 16.2. 与 Jail 相关的一些术语

为了帮助您更好地理解与 jail 有关的 FreeBSD 系统知识， 以及它们如何与 FreeBSD 的其它部分相互作用， 您应理解下列术语：

[chroot(8)](http://www.FreeBSD.org/cgi/man.cgi?query=chroot&sektion=8&manpath=freebsd-release-ports) (命令)

这个工具使用 FreeBSD 的系统调用 [chroot(2)](http://www.FreeBSD.org/cgi/man.cgi?query=chroot&sektion=2&manpath=freebsd-release-ports) FreeBSD 来改变进程， 以及进程的所有衍生进程所能看到的根目录。

[chroot(2)](http://www.FreeBSD.org/cgi/man.cgi?query=chroot&sektion=2&manpath=freebsd-release-ports) (环境)

在 “chroot” 中运行的进程环境。 这包括类似文件系统中的可见部分、 可用的用户及用户组 ID、 网络接口以及其他 IPC 机制等资源。

[jail(8)](http://www.FreeBSD.org/cgi/man.cgi?query=jail&sektion=8&manpath=freebsd-release-ports) (命令)

用以在 jail 环境中运行进程的系统管理工具。

宿主 (系统、 进程、 用户等等)

能够控制 jail 环境的系统。 宿主系统能够访问全部可用的硬件资源， 并能够控制 jail 环境内外的进程。 宿主系统与 jail 的一项重要区别是， 在宿主系统中的超级用户进程， 并不像在 jail 中那样受到一系列限制。

hosted (系统、 进程、 用户等等)

可访问资源受 FreeBSD jail 限制的进程、 用户或其他实体。

## 16.3. 介绍

由于系统管理是一项困难而又令人费解的任务， 因此人们开发了一系列强大的工具， 来让管理员的工作变得更加简单。 这些改进通常是让系统能够以更简单的方式安装、 配置， 并毫无问题地持续运转。 这其中， 许多管理员希望能够为系统正确地进行安全方面的配置， 使其能够用于真正的用途， 而阻止安全方面的风险。

FreeBSD 系统提供的一项用于改善安全的工具就是 *jail*。 jail 是在 FreeBSD 4.X 中由 Poul-Henning Kamp 引入的， 它在 FreeBSD 5.X 中又进行了一系列改进， 使得它成为了一个强大而灵活的系统。 目前仍然在对其进行持续的开发， 以提高其可用性、 性能和安全性。

### 16.3.1. Jail 是什么

BSD-类的操作系统从 4.2BSD 开始即提供了 [chroot(8)](http://www.FreeBSD.org/cgi/man.cgi?query=chroot&sektion=8&manpath=freebsd-release-ports)。 [chroot(2)](http://www.FreeBSD.org/cgi/man.cgi?query=chroot&sektion=2&manpath=freebsd-release-ports) 工具能够改变一组进程的根目录的位置， 从而建立一个与系统中其他部分相隔离的安全环境： 在 chroot 环境中的进程， 将无法访问其外的文件或其他资源。 正是由于这种能力， 即使攻击者攻破了某一个运行于 chroot 环境的服务， 也不能攻破整个系统。 [chroot(8)](http://www.FreeBSD.org/cgi/man.cgi?query=chroot&sektion=8&manpath=freebsd-release-ports) 对于那些不需要很多灵活性或复杂的高级功能的简单应用而言相当好用。 另外， 在引入 chroot 概念的过程中， 曾经发现过许多跳出 chroot 环境的方法， 尽管这些问题在较新的 FreeBSD 版本中已经修正， 但很明显地， [chroot(8)](http://www.FreeBSD.org/cgi/man.cgi?query=chroot&sektion=8&manpath=freebsd-release-ports) 并不是一项用于加固服务安全的理想解决方案。 因此， 必须实现一个新的子系统来解决这些问题。

这就是为什么要开发 *jail* 最主要的原因。

Jail 以多种方式改进了传统的 [chroot(2)](http://www.FreeBSD.org/cgi/man.cgi?query=chroot&sektion=2&manpath=freebsd-release-ports) 环境概念。 在传统的 [chroot(2)](http://www.FreeBSD.org/cgi/man.cgi?query=chroot&sektion=2&manpath=freebsd-release-ports) 环境中， 只限制了进程能够访问文件系统的哪些部分。 其他部分的系统资源 (例如系统用户、 正在运行的进程， 以及网络子系统) 是由 chroot 进程与宿主系统中的其他进程共享的。 jail 扩展了这个模型， 它不仅将文件系统的访问虚拟化， 而且还将用户、 FreeBSD 的网络子系统， 以及一些其他系统资源虚拟化。 关于这些精细控制以及调整 jail 环境访问能力的更具体的介绍， 可参见 第 16.5 节 “微调和管理”。

jail 具有以下四项特点：

*   目录子树 ── 进入 jail 的起点。 一旦进入了 jail， 进程就不再被允许访问这棵子树以外的对象。 传统上影响到最初 [chroot(2)](http://www.FreeBSD.org/cgi/man.cgi?query=chroot&sektion=2&manpath=freebsd-release-ports) 设计的安全问题不会影响 FreeBSD jail。

*   主机名 ── 将用于 jail 的主机名。 jail 主要用于存放网络服务， 因此在每个 mail 上能够标注一个有意义的主机名， 能够在很大程度上简化系统管理员的工作。

*   IP 地址 ── 这个地址是指定给 jail 的， 在 jail 的生命周期内都无法改变。 通常 jail 的 IP 地址是某一个网络接口上的别名地址， 但这并不是必需的。

*   命令 ── 准备在 jail 中执行的可执行文件的完整路径名。 这个命令是相对于 jail 环境的根目录的， 随 jail 环境的类型不同， 可能会有很多不同之处。

除了这些之外， jail 也可以拥有自己的用户和自己的 `root` 用户。 自然， 这里的 `root` 用户的权力会受限于 jail 环境， 并且， 从宿主系统的观点看来， jail `root` 用户并不是一个无所不能的用户。 此外， jail 中的 `root` 用户不能执行除了其对应 [jail(8)](http://www.FreeBSD.org/cgi/man.cgi?query=jail&sektion=8&manpath=freebsd-release-ports) 环境之外的系统中的一些关键操作。 关于 `root` 用户的能力和限制， 在后面的 第 16.5 节 “微调和管理” 中将加以介绍。

## 16.4. 建立和控制 jail

一些系统管理员喜欢将 jail 分为两类： “完整的” jail， 通常包含真正的 FreeBSD 系统， 以及 “服务” jail， 专用于执行一个可能使用特权的应用或服务。 这只是一种概念上的区分， 并不影响如何建立 jail 的过程。 在联机手册 [jail(8)](http://www.FreeBSD.org/cgi/man.cgi?query=jail&sektion=8&manpath=freebsd-release-ports) 中对如何创建 jail 进行了清晰的阐述：

```
# `setenv D /here/is/the/jail`
# `mkdir -p $D` ![1](img/1.png)
`#` **`cd /usr/src`**
`#` **`make buildworld`** ![2](img/2.png)
`#` **`make installworld DESTDIR=$D`** ![3](img/3.png)
`#` **`make distribution DESTDIR=$D`** ![4](img/4.png)
`#` **`mount -t devfs devfs $D/dev`** ![5](img/5.png)
```

| ![1](img/#jailpath) | 第一步就是为 jail 选择一个位置。 这个路径是在宿主系统中 jail 的物理位置。 一种常用的选择是 `/usr/jail/jailname`， 此处 *`jailname`* 是 jail 的主机名。 `/usr/` 文件系统通常会有足够的空间来保存 jail 文件系统， 对于 “完整” 的 jail 而言， 它通常包含了 FreeBSD 默认安装的基本系统中每个文件的副本。 |
| ![2](img/#jailbuildworld) | 如果你已经通过使用 `make world` 或者 `make buildworld` 重新编译过了你的 userland， 则可以跳过这一步骤并把现有的 userland 安装进新的 jail。 |
| ![3](img/#jailinstallworld) | 这个命令将在 jail 目录中安装所需的可执行文件、 函数库以及联机手册等。 |
| ![4](img/#jaildistrib) | `distribution` 这个 make target 将安装全部配置文件， 或者换句话说， 就是将 `/usr/src/etc/` 复制到 jail 环境中的 `/etc`： `$D/etc/`。 |
| ![5](img/#jaildevfs) | 在 jail 中不是必须要挂接 [devfs(8)](http://www.FreeBSD.org/cgi/man.cgi?query=devfs&sektion=8&manpath=freebsd-release-ports) 文件系统。 而另一方面， 几乎所有的应用程序都会需要访问至少一个设备， 这主要取决于应用程序的性质和目的。 控制 jail 中能够访问的设备非常重要， 因为不正确的配置， 很可能允许攻击者在 jail 中进行一些恶意的操作。 通过 [devfs(8)](http://www.FreeBSD.org/cgi/man.cgi?query=devfs&sektion=8&manpath=freebsd-release-ports) 实施的控制， 可以通过由联机手册 [devfs(8)](http://www.FreeBSD.org/cgi/man.cgi?query=devfs&sektion=8&manpath=freebsd-release-ports) 和 [devfs.conf(5)](http://www.FreeBSD.org/cgi/man.cgi?query=devfs.conf&sektion=5&manpath=freebsd-release-ports) 介绍的规则集配置来实现。 |

一旦装好了 jail， 就可以使用 [jail(8)](http://www.FreeBSD.org/cgi/man.cgi?query=jail&sektion=8&manpath=freebsd-release-ports) 工具来安装它了。 [jail(8)](http://www.FreeBSD.org/cgi/man.cgi?query=jail&sektion=8&manpath=freebsd-release-ports) 工具需要四个必填参数， 这些参数在 第 16.3.1 节 “Jail 是什么” 中进行了介绍。 除了这四个参数之外， 您还可以指定一些其他参数， 例如， 以特定用户身份来在 jail 中运行程序等等。 这里， `*`command`*` 参数取决于您希望建立的 jail 的类型； 对于 *虚拟系统*， 可以选择 `/etc/rc`， 因为它会完成真正的 FreeBSD 系统启动所需的操作。 对于 *服务* jail， 执行的命令取决于将在 jail 中运行的应用程序。

Jail 通常应在系统启动时启动， 因此， FreeBSD `rc` 机制提供了一些很方便的机制来简化这些工作。

1.  在引导时需要启动的 jail 列表应写入 [rc.conf(5)](http://www.FreeBSD.org/cgi/man.cgi?query=rc.conf&sektion=5&manpath=freebsd-release-ports) 文件：

    ```
    jail_enable="YES"   # 如果设为 NO 则表示不自动启动 jail
    jail_list="*`www`*"     # 以空格分隔的 jail 名字列表
    ```

    ### 注意:

    在 `jail_list` 中的名字中， 可以使用字母和数字， 而不应使用其他字符。

2.  对于 `jail_list` 中列出的 jail， 还应指定一系列对应的 [rc.conf(5)](http://www.FreeBSD.org/cgi/man.cgi?query=rc.conf&sektion=5&manpath=freebsd-release-ports) 设置， 用以描述具体的 jail：

    ```
    jail_*`www`*_rootdir="/usr/jail/www"     # jail 的根目录
    jail_*`www`*_hostname="*`www`*.example.org"   # jail 的主机名
    jail_*`www`*_ip="192.168.0.10"          # jail 的 IP 地址
    jail_*`www`*_devfs_enable="YES"          # 在 jail 中挂接 devfs
    jail_*`www`*_devfs_ruleset="*`www_ruleset`*" # 在 jail 中应用的 devfs 规则集
    ```

    默认情况下， 在 [rc.conf(5)](http://www.FreeBSD.org/cgi/man.cgi?query=rc.conf&sektion=5&manpath=freebsd-release-ports) 中配置启动的 jail 会执行其中的 `/etc/rc` 脚本， 也就是说， 默认情况下将 jail 作为虚拟系统方式来启动。 对于服务 jail， 您应另外指定启动命令， 方法是设置对应的 `jail_*`jailname`*_exec_start` 配置。

    ### 注意:

    如欲了解全部可用的选项， 请参阅联机手册 [rc.conf(5)](http://www.FreeBSD.org/cgi/man.cgi?query=rc.conf&sektion=5&manpath=freebsd-release-ports)。

`/etc/rc.d/jail` 脚本也可以用于手工启动或停止 `rc.conf` 中配置的 jail：

```
# `/etc/rc.d/jail start www`
# `/etc/rc.d/jail stop www`
```

目前， 尚没有一种方法来很干净地关闭 [jail(8)](http://www.FreeBSD.org/cgi/man.cgi?query=jail&sektion=8&manpath=freebsd-release-ports)。 这是因为通常用于正常关闭系统的命令， 目前尚不能在 jail 中使用。 目前， 关闭 jail 最好的方式， 是在 jail 外通过 [jexec(8)](http://www.FreeBSD.org/cgi/man.cgi?query=jexec&sektion=8&manpath=freebsd-release-ports) 工具， 在 jail 中执行下列命令：

```
# `sh /etc/rc.shutdown`
```

更进一步的详细说明， 请参见联机手册 [jail(8)](http://www.FreeBSD.org/cgi/man.cgi?query=jail&sektion=8&manpath=freebsd-release-ports)。

## 16.5. 微调和管理

您可以为 jail 设置许多不同的选项， 并让 FreeBSD 宿主系统以不同的方式与 jail 交互， 以支持更高级别的应用。 这一节将介绍：

*   一些用于微调 jail 行为和安全限制的选项。

*   一些可以通过 FreeBSD Ports 套件安装的高级 jail 管理应用程序， 这些程序可以用于实现一般的基于 jail 的解决方案。

### 16.5.1. FreeBSD 提供的用于微调 jail 的系统工具

对于 jail 的配置微调， 基本上都是通过设置 [sysctl(8)](http://www.FreeBSD.org/cgi/man.cgi?query=sysctl&sektion=8&manpath=freebsd-release-ports) 变量来完成的。 系统提供了一个特殊的 sysctl 子树， 全部相关的选项均在这棵子树中； 这就是 FreeBSD 内核的 `security.jail.*` 选项子树。 下面是与 jail 有关的主要 sysctl， 以及这些变量的默认值。 这些名字都比较容易理解， 如欲了解进一步的详情， 请参阅联机手册 [jail(8)](http://www.FreeBSD.org/cgi/man.cgi?query=jail&sektion=8&manpath=freebsd-release-ports) 和 [sysctl(8)](http://www.FreeBSD.org/cgi/man.cgi?query=sysctl&sektion=8&manpath=freebsd-release-ports)。

*   `security.jail.set_hostname_allowed: 1`

*   `security.jail.socket_unixiproute_only: 1`

*   `security.jail.sysvipc_allowed: 0`

*   `security.jail.enforce_statfs: 2`

*   `security.jail.allow_raw_sockets: 0`

*   `security.jail.chflags_allowed: 0`

*   `security.jail.jailed: 0`

系统管理员可以在 *宿主系统* 中， 透过设置这些变量的值来默认为 `root` 用户增加或取消限制。 需要注意的是， 某些限制是不能够取消的。 在 [jail(8)](http://www.FreeBSD.org/cgi/man.cgi?query=jail&sektion=8&manpath=freebsd-release-ports) 中的 `root` 用户， 无法挂载或卸下文件系统， 此外在 jail 中的 `root` 用户也不能加载或卸载 [devfs(8)](http://www.FreeBSD.org/cgi/man.cgi?query=devfs&sektion=8&manpath=freebsd-release-ports) 规则集、 配置防火墙规则， 或执行其他需要修改内核数据的管理操作， 例如设置内核的 `securelevel` 等等。

FreeBSD 的基本系统包含一系列用于查看目前在使用的 jail 信息， 以及接入 jail 并执行管理命令所需的基本工具。 [jls(8)](http://www.FreeBSD.org/cgi/man.cgi?query=jls&sektion=8&manpath=freebsd-release-ports) 和 [jexec(8)](http://www.FreeBSD.org/cgi/man.cgi?query=jexec&sektion=8&manpath=freebsd-release-ports) 命令都是 FreeBSD 基本系统的一部分， 并可用于执行简单的任务：

*   列出在用的 jail 以及对应的 jail 标识 (JID)、 IP 地址、 主机名和路径。

*   从宿主系统中接入正在运行的 jail， 并在其中执行命令， 以完成一系列 jail 管理任务。 这在 `root` 希望干净地关闭 jail 时非常有用。 [jexec(8)](http://www.FreeBSD.org/cgi/man.cgi?query=jexec&sektion=8&manpath=freebsd-release-ports) 工具也可以用于在 jail 中启动 shell 以便对其进行管理； 例如：

    ```
    # `jexec 1 tcsh`
    ```

### 16.5.2. 由 FreeBSD Ports 套件提供的高级管理工具

在众多第三方 jail 管理工具中， [sysutils/jailutils](http://www.freebsd.org/cgi/url.cgi?ports/sysutils/jailutils/pkg-descr) 是最完整和好用的。 它是一系列方便 [jail(8)](http://www.FreeBSD.org/cgi/man.cgi?query=jail&sektion=8&manpath=freebsd-release-ports) 管理的小工具。 请参见其网站以了解进一步的详情。

## 16.6. Jail 的应用

### 16.6.1. 服务 Jail

原作 Daniel Gerzo.

这一节主要基于 Simon L. B. Nielsen 的 `[`simon.nitro.dk/service-jails.html`](http://simon.nitro.dk/service-jails.html)` 中的思路， 以及由 Ken Tom `<locals@gmail.com>` 更新的文档。 这一节中描述了如何配置 FreeBSD 系统的 [jail(8)](http://www.FreeBSD.org/cgi/man.cgi?query=jail&sektion=8&manpath=freebsd-release-ports) 功能为其增加一个安全层次。 这部分假定您运行 RELENG_6_0 或更新版本， 并理解本章之前部分的内容。

#### 16.6.1.1. 设计

jail 的一个主要问题是如何对它们进行升级和管理。 由于每个 jail 都是从头联编的， 对于单个 jail 而言升级也许还不是个很严重的问题， 因为升级不会太过麻烦， 而对于多个 jail 而言， 升级不仅会耗费大量时间， 并且是十分乏味的过程。

### 警告:

这个配置过程需要您对 FreeBSD 有较多的配置和使用经验。 如果这些过程显得太过复杂， 您应考虑使用较简单的系统， 例如 [sysutils/ezjail](http://www.freebsd.org/cgi/url.cgi?ports/sysutils/ezjail/pkg-descr)， 它提供了更简单的管理 FreeBSD jail 的方法。

基本的想法是， 在不同的 jail 中尽可能多地以安全的方式使用共享的资源 ── 使用只读的 [mount_nullfs(8)](http://www.FreeBSD.org/cgi/man.cgi?query=mount_nullfs&sektion=8&manpath=freebsd-release-ports) 挂接， 这会让升级简单许多， 从而使为每个服务建立不同的 jail 这种方案变得更加可行。 另外， 它也为增加、删除以及升级 jail 提供了更为便捷的方法。

### 注意:

在这里服务的常见例子包括： HTTP 服务、 DNS 服务、 SMTP 服务等等， 诸如此类。

这节介绍的配置的目的包括：

*   建立简单并易于理解的 jail 结构。 也就是说 *不必* 为每个 jail 执行完整的 installworld 操作。

*   使增删 jail 更容易。

*   使更新或升级 jail 更容易。

*   使运行自订的 FreeBSD 分支成为可能。

*   对安全的更偏执的追求， 尽可能减少被攻陷的可能。

*   尽可能节省空间和 inode。

如前面提到的那样， 这个设计极大程度上依赖于将一份只读的主模板 (known as nullfs) 挂接到每一个 jail 中， 并为每个 jail 配置一个可读写的设备。 这种设备可以是物理磁盘、 分区， 或以 vnode 为后端的 [md(4)](http://www.FreeBSD.org/cgi/man.cgi?query=md&sektion=4&manpath=freebsd-release-ports) 设备。 在这个例子中， 我们将使用可读写的 nullfs 挂接。

下面的表中描述了文件系统格局：

*   每个 jail 挂接到 `/home/j` 目录下的一个目录。

*   `/home/j/mroot` 是每个 jail 共用的模板， 对于所有的 jail 而言都是只读的。

*   在 `/home/j` 目录中， 每个 jail 有一个对应的空目录。

*   每个 jail 中都有一个 `/s` 目录， 这个目录将连接到系统中的可读写部分。

*   每个 jail 应基于 `/home/j/skel` 建立其可读写空间。

*   每个 jailspace (jail 中的可读写部分) 应创建到 `/home/js`。

### 注意:

这假定所有的 jail 都放置于 `/home` 分区中。 当然， 您可以根据需要将这个配置改为需要的任何样子， 但在接下来的例子中， 也应相应地加以变动。

#### 16.6.1.2. 建立模板

这一节将介绍创建 jail 所需的只读主模板所需的步骤。

一般来说， 您应将系统升级到最新的 FreeBSD -RELEASE 分支， 具体做法请参见本手册的相关 章节。 当更新不可行时， 则需要完成 buildworld 过程， 另外， 您还需要 [sysutils/cpdup](http://www.freebsd.org/cgi/url.cgi?ports/sysutils/cpdup/pkg-descr) 软件包。 我们将使用 [portsnap(8)](http://www.FreeBSD.org/cgi/man.cgi?query=portsnap&sektion=8&manpath=freebsd-release-ports) 工具来下载 FreeBSD Ports 套件。 在使用手册的 Portsnap 章节 中， 提供了针对初学者的介绍。

1.  首先， 需要为将要存放只读的 FreeBSD 执行文件的文件系统建立一个目录， 接着进入 FreeBSD 源代码的目录， 并在其中安装 jail 模板：

    ```
    # `mkdir /home/j /home/j/mroot`
    # `cd /usr/src`
    # `make installworld DESTDIR=/home/j/mroot`
    ```

2.  接着， 准备一份 FreeBSD Ports 套件， 以及用于执行 mergemaster 的 FreeBSD 源代码：

    ```
    # `cd /home/j/mroot`
    # `mkdir usr/ports`
    # `portsnap -p /home/j/mroot/usr/ports fetch extract`
    # `cpdup /usr/src /home/j/mroot/usr/src`
    ```

3.  创建系统中可读写部分的骨架：

    ```
    # `mkdir /home/j/skel /home/j/skel/home /home/j/skel/usr-X11R6 /home/j/skel/distfiles`
    # `mv etc /home/j/skel`
    # `mv usr/local /home/j/skel/usr-local`
    # `mv tmp /home/j/skel`
    # `mv var /home/j/skel`
    # `mv root /home/j/skel`
    ```

4.  使用 mergemaster 安装缺失的配置文件。 接下来， 删除 mergemaster 创建的多余目录：

    ```
    # `mergemaster -t /home/j/skel/var/tmp/temproot -D /home/j/skel -i`
    # `cd /home/j/skel`
    # `rm -R bin boot lib libexec mnt proc rescue sbin sys usr dev`
    ```

5.  现在， 将可读写文件系统连接到只读文件系统中。 请确保您在 `s/` 目录中建立了适当的符号连接。 如果没有建立目录或建立的位置不正确， 可能会导致安装失败。

    ```
    # `cd /home/j/mroot`
    # `mkdir s`
    # `ln -s s/etc etc`
    # `ln -s s/home home`
    # `ln -s s/root root`
    # `ln -s ../s/usr-local usr/local`
    # `ln -s ../s/usr-X11R6 usr/X11R6`
    # `ln -s ../../s/distfiles usr/ports/distfiles`
    # `ln -s s/tmp tmp`
    # `ln -s s/var var`
    ```

6.  最后， 创建一个默认的包含下列配置的 `/home/j/skel/etc/make.conf`：

    ```
    WRKDIRPREFIX?=  /s/portbuild
    ```

    配置 `WRKDIRPREFIX` 使得在每个 jail 中分别编译 FreeBSD 成为可能。 请注意 ports 目录是只读系统的一部分。 而自订的 `WRKDIRPREFIX` 则使得联编过程得以在 jail 中的可读写部分完成。

#### 16.6.1.3. 建立 Jail

现在我们已经有了完整的 FreeBSD jail 模板， 可以在 `/etc/rc.conf` 中安装并配置它们了。 这个例子中演示了建立 3 个 jail： “NS”、 “MAIL” 和 “WWW”。

1.  在 `/etc/fstab` 文件中加入下列配置， 以便让系统自动挂接 jail 的只读模板和读写空间：

    ```
    /home/j/mroot   /home/j/ns     nullfs  ro  0   0
    /home/j/mroot   /home/j/mail   nullfs  ro  0   0
    /home/j/mroot   /home/j/www    nullfs  ro  0   0
    /home/js/ns     /home/j/ns/s   nullfs  rw  0   0
    /home/js/mail   /home/j/mail/s nullfs  rw  0   0
    /home/js/www    /home/j/www/s  nullfs  rw  0   0
    ```

    ### 注意:

    扫描批次号 (pass number) 为 0 的分区不会在启动时使用 [fsck(8)](http://www.FreeBSD.org/cgi/man.cgi?query=fsck&sektion=8&manpath=freebsd-release-ports) 进行检查， 而转存批次号 (dump number) 为 0 的分区则不会在 [dump(8)](http://www.FreeBSD.org/cgi/man.cgi?query=dump&sektion=8&manpath=freebsd-release-ports) 时备份。 我们不希望 fsck 检查 nullfs 挂接， 或让 dump 备份 jail 中的只读 nullfs 挂接。 这就是为什么在每个 `fstab` 条目的最后两列是 “0 0” 的原因。

2.  在 `/etc/rc.conf` 中配置 jail：

    ```
    jail_enable="YES"
    jail_set_hostname_allow="NO"
    jail_list="ns mail www"
    jail_ns_hostname="ns.example.org"
    jail_ns_ip="192.168.3.17"
    jail_ns_rootdir="/usr/home/j/ns"
    jail_ns_devfs_enable="YES"
    jail_mail_hostname="mail.example.org"
    jail_mail_ip="192.168.3.18"
    jail_mail_rootdir="/usr/home/j/mail"
    jail_mail_devfs_enable="YES"
    jail_www_hostname="www.example.org"
    jail_www_ip="62.123.43.14"
    jail_www_rootdir="/usr/home/j/www"
    jail_www_devfs_enable="YES"
    ```

    ### 警告:

    应把 `jail_*`name`*_rootdir` 变量设置成 `/usr/home` 而不是 `/home` 的原因是 `/home` 目录在默认安装的 FreeBSD 上是指向 `/usr/home` 的一个符号连接。 而 `jail_*`name`*_rootdir` 变量必须是一个 *不* 包含符号连接的路径， 否则 jail 将拒绝启动。 可以使用 [realpath(1)](http://www.FreeBSD.org/cgi/man.cgi?query=realpath&sektion=1&manpath=freebsd-release-ports) 工具来决定这一变量应被赋予一个什么样的值。 更详细的信息请参阅安全公告 FreeBSD-SA-07:01.jail

3.  为每个 jail 创建所需的只读文件系统挂接点：

    ```
    # `mkdir /home/j/ns /home/j/mail /home/j/www`
    ```

4.  在 jail 中安装可读写的模板。 注意您需要使用 [sysutils/cpdup](http://www.freebsd.org/cgi/url.cgi?ports/sysutils/cpdup/pkg-descr)， 它能够帮助您确保每个目录都是正确地复制的：

    ```
    # `mkdir /home/js`
    # `cpdup /home/j/skel /home/js/ns`
    # `cpdup /home/j/skel /home/js/mail`
    # `cpdup /home/j/skel /home/js/www`
    ```

5.  这样， 就完成了 jail 的制作， 可以运行了。 首先为 jail 挂接文件系统， 然后使用 `/etc/rc.d/jail` 脚本来启动它们：

    ```
    # `mount -a`
    # `/etc/rc.d/jail start`
    ```

现在 jail 应该就启动起来了。 要检查它们是否运行正常， 可以使用 [jls(8)](http://www.FreeBSD.org/cgi/man.cgi?query=jls&sektion=8&manpath=freebsd-release-ports) 命令。 它的输出应该类似这样：

```
# `jls`
   JID  IP Address      Hostname                      Path
     3  192.168.3.17    ns.example.org                /home/j/ns
     2  192.168.3.18    mail.example.org              /home/j/mail
     1  62.123.43.14    www.example.org               /home/j/www
```

这时， 就可以登入 jail 并增加用户和配置服务了。 `JID` 列给出了正在运行的 jail 的标识编号。 您可以使用下面的命令来在 `JID` 编号为 3 的 jail 中执行管理任务：

```
# `jexec 3 tcsh`
```

#### 16.6.1.4. 升级

有时， 由于安全问题， 或新增功能有用， 会希望将系统升级到一个新版本的 FreeBSD。 这种安装方式的设计使得升级现有 jail 变得很容易。 另外， 它也能最大限度地减小停机时间， 因为 jail 只在最后时刻才需要关闭。 另外， 它也提供了简单的回退到先前版本的方法。

1.  第一步是按通常的方法升级主机的系统。 接着， 在 `/home/j/mroot2` 中建立一个新的临时模板：

    ```
    # `mkdir /home/j/mroot2`
    # `cd /usr/src`
    # `make installworld DESTDIR=/home/j/mroot2`
    # `cd /home/j/mroot2`
    # `cpdup /usr/src usr/src`
    # `mkdir s`
    ```

    在运行 `installworld` 时会创建一些不需要的目录， 应将它们删除：

    ```
    # `chflags -R 0 var`
    # `rm -R etc var root usr/local tmp`
    ```

2.  重建到主系统中的可读写符号连接：

    ```
    # `ln -s s/etc etc`
    # `ln -s s/root root`
    # `ln -s s/home home`
    # `ln -s ../s/usr-local usr/local`
    # `ln -s ../s/usr-X11R6 usr/X11R6`
    # `ln -s s/tmp tmp`
    # `ln -s s/var var`
    ```

3.  现在是时候关闭 jail 了：

    ```
    # `/etc/rc.d/jail stop`
    ```

4.  卸下原先的文件系统：

    ```
    # `umount /home/j/ns/s`
    # `umount /home/j/ns`
    # `umount /home/j/mail/s`
    # `umount /home/j/mail`
    # `umount /home/j/www/s`
    # `umount /home/j/www`
    ```

    ### 注意:

    可读写的文件系统 (`/s`) 会在只读系统之后挂接， 因此应首先卸载。

5.  将先前的只读文件系统挪走， 换成新的系统。 这样做也同时保留了先前系统的备份， 从而可以在出现问题时从中恢复。 这里我们根据新系统的创建时间来命名。 此外我们把先前的 FreeBSD Ports 套件直接移动到新的文件系统中， 以节省磁盘空间和 inode：

    ```
    # `cd /home/j`
    # `mv mroot mroot.20060601`
    # `mv mroot2 mroot`
    # `mv mroot.20060601/usr/ports mroot/usr`
    ```

6.  现在新的只读模板就可以用了， 剩下的事情是重新挂接文件系统并启动 jails：

    ```
    # `mount -a`
    # `/etc/rc.d/jail start`
    ```

最后用 [jls(8)](http://www.FreeBSD.org/cgi/man.cgi?query=jls&sektion=8&manpath=freebsd-release-ports) 检查 jail 启动是否正常。 不要忘记在 jail 中运行 mergemaster。 配置文件和 rc.d 脚本在升级时应进行更新。

## 第 17 章 强制访问控制

原作 Tom Rhodes.

## 17.1. 概要

FreeBSD 5.X 在 POSIX®.1e 草案的基础上引入了 TrustedBSD 项目提供的新的安全性扩展。 新安全机制中最重要的两个， 是文件系统访问控制列表 (ACL) 和强制访问控制 (MAC) 机制。 强制访问控制允许加载新的访问控制模块， 并借此实施新的安全策略， 其中一部分为一个很小的系统子集提供保护并加强特定的服务， 其他的则对所有的主体和客体提供全面的标签式安全保护。 定义中有关强制的部分源于如下事实， 控制的实现由管理员和系统作出， 而不像自主访问控制 (DAC, FreeBSD 中的标准文件以及 System V IPC 权限) 那样是按照用户意愿进行的。

本章将集中讲述强制访问控制框架 (MAC 框架) 以及一套用以实施多种安全策略的插件式的安全策略模块。

阅读本章之后， 您将了解：

*   目前 FreeBSD 中具有哪些 MAC 安全策略模块， 以及与之相关的机制。

*   MAC 安全策略模块将实施何种策略， 以及标签式与非标签式策略之间的差异。

*   如何高效地配置系统令使其使用 MAC 框架。

*   如何配置 MAC 框架所提供的不同的安全策略模块。

*   如何用 MAC 框架构建更为安全的环境， 并举例说明。

*   如何测试 MAC 配置以确保正确构建了框架。

阅读本章之前， 您应该：

*   了解 UNIX® 和 FreeBSD 的基础 (第 4 章 *UNIX 基础*)。

*   熟悉内核配置/编译 (第 9 章 *配置 FreeBSD 的内核*) 的基础。

*   对安全及其如何与 FreeBSD 相配合有些了解； (第 15 章 *安全*)。

### 警告:

对本章信息的不当使用可能导致丧失系统访问权， 激怒用户， 或者无法访问 X11 提供的特性。 更重要的是， MAC 不能用于彻底保护一个系统。 MAC 框架仅用于增强现有安全策略； 如果没有健全的安全条例以及定期的安全检查， 系统将永远不会绝对安全。

此外还需要注意的是， 本章中所包含的例子仅仅是例子。 我们并不建议在一个生产用系统上进行这些特别的设置。 实施各种安全策略模块需要谨慎的考虑与测试， 因为那些并不完全理解所有机制如何工作的人， 可能会发现需要对整个系统中很多的文件或目录进行重新配置。

### 17.1.1. 未涉及的内容

本章涵盖了与 MAC 框架有关的诸多方面的安全问题； 而新的 MAC 安全策略模块的开发成果则不会涉及。 MAC 框架中所包含的一部分安全策略模块， 具有一些用于测试及新模块开发的特定属性， 其中包括 [mac_test(4)](http://www.FreeBSD.org/cgi/man.cgi?query=mac_test&sektion=4&manpath=freebsd-release-ports)、 [mac_stub(4)](http://www.FreeBSD.org/cgi/man.cgi?query=mac_stub&sektion=4&manpath=freebsd-release-ports) 以及 [mac_none(4)](http://www.FreeBSD.org/cgi/man.cgi?query=mac_none&sektion=4&manpath=freebsd-release-ports)。 关于这些安全策略模块及其提供的众多机制的详细信息，请参阅联机手册中的内容。

## 17.2. 本章出现的重要术语

在阅读本章之前， 有些关键术语需要解释， 希望能藉此扫清可能出现的疑惑， 并避免在文中对新术语、 新信息进行生硬的介绍。

*   *区间*(compartment)： (译注： *区间* 这一术语， 在一些文献中也称做类别 (category)。 此外， 在其它一些翻译文献中， 该术语也翻译为 “象限”。) 指一组被划分或隔离的程序和数据， 其中， 用户被明确地赋予了访问特定系统组件的权限。 同时， 区间也能够表达分组， 例如工作组、 部门、 项目， 或话题。 可以通过使用区间来实施 need-to-know 安全策略。

*   *高水位线*(high water mark)： 高水位线策略是一种允许提高安全级别， 以期访问更高级别的信息的安全策略。 在多数情况下， 当进程结束时， 又会回到原先的安全级别。 目前， FreeBSD MAC 框架尚未提供这样的策略， 在这里介绍其定义主要是希望给您一个完整的概念。

*   *完整性*(integrity)： 作为一个关键概念， 完整性是数据可信性的一种程度。 若数据的完整性提高， 则数据的可信性相应提高。

*   *标签*(label)： 标签是一种可应用于文件、 目录或系统其他客体的安全属性， 它也可以被认为是一种机密性印鉴。 当一个文件被施以标签时， 其标签会描述这一文件的安全参数， 并只允许拥有相似安全性设置的文件、 用户、 资源等访问该文件。 标签值的涵义及解释取决于相应的策略配置： 某些策略会将标签当作对某一客体的完整性和保密性的表述， 而其它一些策略则会用标签保存访问规则。

*   *程度*(level)： 对某种安全属性加强或削弱的设定。 若程度增加， 其安全性也相应增加。

*   *低水位线*(low water mark)： 低水位线策略允许降低安全级别， 以访问安全性较差的信息。 多数情况下， 在进程结束时， 又会回到原先的安全级别。 目前在 FreeBSD 中唯一实现这一安全策略的是 [mac_lomac(4)](http://www.FreeBSD.org/cgi/man.cgi?query=mac_lomac&sektion=4&manpath=freebsd-release-ports)。

*   *多重标签*(multilabel)： `multilabel` 属性是一个文件系统选项。 该选项可在单用户模式下通过 [tunefs(8)](http://www.FreeBSD.org/cgi/man.cgi?query=tunefs&sektion=8&manpath=freebsd-release-ports) 程序进行设置。 可以在引导时使用的 [fstab(5)](http://www.FreeBSD.org/cgi/man.cgi?query=fstab&sektion=5&manpath=freebsd-release-ports) 文件中， 也可在创建新文件系统时进行配置。 该选项将允许管理员对不同客体施以不同的 MAC 标签。 该选项仅适用于支持标签的安全策略模块。

*   *客体*(object)： 客体或系统客体是一种实体， 信息随 *主体* 的导向在客体内部流动。 客体包括目录、 文件、 区段、 显示器、 键盘、 存储器、 磁存储器、 打印机及其它数据存储/转移设备。 基本上， 客体就是指数据容器或系统资源。 对 *客体* 的访问实际上意味着对数据的访问。

*   *策略*(policy)： 一套用以规定如何达成目标的规则。 *策略* 一般用以描述如何对特定客体进行操作。 本章将在*安全策略*的范畴内讨论*策略*， 一套用以控制数据和信息流并规定其访问者的规则，就是其中一例。

*   *敏感性*(sensitivity)： 通常在讨论 MLS 时使用。 敏感性程度曾被用来描述数据应该有何等的重要或机密。 若敏感性程度增加， 则保密的重要性或数据的机密性相应增强。

*   *单一标签*(single label)： 整个文件系统使用一个标签对数据流实施访问控制， 叫做单一标签。 当文件系统使用此设置时， 即无论何时当 `多重标签` 选项未被设定时， 所有文件都将遵守相同标签设定。

*   *主体*(subject)： 主体就是引起信息在两个 *客体* 间流动的任意活动实体， 比如用户， 用户进程(译注：原文为 processor)， 系统进程等。 在 FreeBSD 中， 主体几乎总是代表用户活跃在某一进程中的一个线程。

## 17.3. 关于 MAC 的说明

在掌握了所有新术语之后， 我们从整体上来考虑 MAC 是如何加强系统安全性的。 MAC 框架提供的众多安全策略模块可以用来保护网络及文件系统， 也可以禁止用户访问某些特定的端口、 套接字及其它客体。 将策略模块组合在一起以构建一个拥有多层次安全性的环境， 也许是其最佳的使用方式， 这可以通过一次性加载多个安全策略模块来实现。 在多层次安全环境中， 多重策略模块可以有效地控制安全性， 这一点与强化型 (hardening) 策略， 即那种通常只强化系统中用于特定目的的元素的策略是不同的。 相比之下， 多重策略的唯一不足是需要系统管理员先期设置好参数， 如多重文件系统安全标志、 每一位用户的网络访问权限等等。

与采用框架方式实现的长期效果相比， 这些不足之处是微不足道的。 例如， 让系统具有为特定配置挑选必需的策略的能力， 有助于降低性能开销。 而减少对无用策略的支持， 不仅可以提高系统的整体性能， 而且提供了更灵活的选择空间。 好的实施方案中应该考虑到整体的安全性要求， 并有效地利用框架所提供的众多安全策略模块。

这样一个使用 MAC 特性的系统， 至少要保证不允许用户任意更改安全属性； 所有的用户实用工具、 程序以及脚本， 必须在所选安全策略模块提供的访问规则的约束下工作； 并且系统管理员应掌握 MAC 访问规则的一切控制权。

细心选择正确的安全策略模块是系统管理员专有的职责。 某些环境也许需要限制网络的访问控制权， 在这种情况下， 使用 [mac_portacl(4)](http://www.FreeBSD.org/cgi/man.cgi?query=mac_portacl&sektion=4&manpath=freebsd-release-ports)、 [mac_ifoff(4)](http://www.FreeBSD.org/cgi/man.cgi?query=mac_ifoff&sektion=4&manpath=freebsd-release-ports) 乃至 [mac_biba(4)](http://www.FreeBSD.org/cgi/man.cgi?query=mac_biba&sektion=4&manpath=freebsd-release-ports) 安全策略模块都会是不错的开始； 在其他情况下， 系统客体也许需要严格的机密性， 像 [mac_bsdextended(4)](http://www.FreeBSD.org/cgi/man.cgi?query=mac_bsdextended&sektion=4&manpath=freebsd-release-ports) 和 [mac_mls(4)](http://www.FreeBSD.org/cgi/man.cgi?query=mac_mls&sektion=4&manpath=freebsd-release-ports) 这样的安全策略模块就是为此而设。

对安全策略模块的决定可依据网络配置进行， 也许只有特定的用户才应该被允许使用由 [ssh(1)](http://www.FreeBSD.org/cgi/man.cgi?query=ssh&sektion=1&manpath=freebsd-release-ports) 提供的程序以访问网络或互联网， [mac_portacl(4)](http://www.FreeBSD.org/cgi/man.cgi?query=mac_portacl&sektion=4&manpath=freebsd-release-ports) 安全策略模块应该成为这种情况下的选择。 但对文件系统又该作些什么呢？ 是由特定的用户或群组来确定某些目录的访问权限， 抑或是将特定客体设为保密以限制用户或组件访问特定文件？

在文件系统的例子中， 也许访问客体的权限对某些用户是保密的， 但对其他则不是。 比如， 一个庞大的开发团队， 也许会被分成许多由几人组成的小组， A 项目中的开发人员可能不被允许访问 B 项目开发人员创作的客体， 但同时他们还需要访问由 C 项目开发人员创作的客体， 这正符合上述情形。 使用由 MAC 框架提供的不同策略， 用户就可以被分成这种小组， 然后被赋予适当区域的访问权， 由此， 我们就不用担心信息泄漏的问题了。

因此， 每一种安全策略模块都有其处理系统整体安全问题的独特方法。 对安全策略模块的选择应在对安全策略深思熟虑的基础之上进行。 很多情况下， 整体安全策略需要重新修正并在系统上实施。 理解 MAC 框架提供的不同安全策略模块会帮助管理员就其面临的情形选择最佳的策略模块。

FreeBSD 的默认内核并不包含 MAC 框架选项， 因此， 在尝试使用本章中的例子或信息之前， 您应该添加以下内核选项：

```
options	MAC
```

此外， 内核还需要重新编译并且重新安装。

### 小心:

尽管有关 MAC 的许多联机手册中都声明它们可以被编译到内核中， 但对这些策略模块的使用仍可能导致锁死系统的网络及其他功能。 使用 MAC 就像使用防火墙一样， 因此必须要小心防止将系统完全锁死。 在使用 MAC 时， 应该考虑是否能够回退到之前的配置， 在远程进行配置更应加倍小心。

## 17.4. 理解 MAC 标签

MAC 标签是一种安全属性， 它可以被应用于整个系统中的主体和客体。

配置标签时， 用户必须能够确切理解其所进行的操作。 客体所具有的属性取决于被加载的策略模块， 不同策略模块解释其属性的方式也差别很大。 由于缺乏理解或无法了解其间联系而导致的配置不当， 会引起意想不到的， 也许是不愿看到的系统异常。

客体上的安全标签是由安全策略模块决定的安全访问控制的一部分。 在某些策略模块中， 标签本身所包含的所有信息足以使其作出决策， 而在其它一些安全策略模块中， 标签则可能被作为一个庞大规则体系的一部分进行处理。

举例来说， 在文件上设定 `biba/low` 标签， 意味着此标签隶属 Biba 策略模块， 其值为 “low”。

某些在 FreeBSD 中支持标签特性的策略会提供三个预定义的标签， 分别是 low、 high 及 equal 标签。 尽管这些标签在不同安全策略模块中会对访问控制采取不同措施， 但有一点是可以肯定的， 那就是 low 标签表示最低限度的设定， equal 标签会将主体或客体设定为被禁用的或不受影响的， high 标签则会应用 Biba 及 MLS 安全策略模块中允许的最高级别的设定。

在单一标签文件系统的环境中， 同一客体上只会应用一个标签， 于是， 一套访问权限将被应用于整个系统， 这也是很多环境所全部需要的。 另一些应用场景中， 我们需要将多重标签应用于文件系统的客体或主体， 如此一来， 就需要使用 [tunefs(8)](http://www.FreeBSD.org/cgi/man.cgi?query=tunefs&sektion=8&manpath=freebsd-release-ports) 的 `multilabel` 选项。

在使用 Biba 和 MLS 时可以配置数值标签， 以标示分级控制中的层级程度。 数值的程度可以用来划分或将信息按组分类， 从而只允许同程度或更高程度的组对其进行访问。

多数情况下， 管理员将仅对整个文件系统设定单一标签。

*等一下， 这看起来很像 DAC！ 但我认为 MAC 确实只将控制权赋予了管理员。* 此句话依然是正确的。 在某种程度上， `root` 是实施控制的用户， 他配置安全策略模块以使用户们被分配到适当的类别/访问 levels 中。 唉， 很多安全策略模块同样可以限制 `root` 用户。 对于客体的基本控制可能会下放给群组， 但 `root` 用户随时可以废除或更改这些设定。 这就是如 Biba 及 MLS 这样一些安全策略模块所包含的 hierarchal/clearance 模型。

### 17.4.1. 配置标签

实际上， 有关标签式安全策略模块配置的各种问题都是用基础系统组件实现的。 这些命令为客体和主体配置以及配置的实施和验证提供了一个简便的接口。

所有的配置都应该通过 [setfmac(8)](http://www.FreeBSD.org/cgi/man.cgi?query=setfmac&sektion=8&manpath=freebsd-release-ports) 及 [setpmac(8)](http://www.FreeBSD.org/cgi/man.cgi?query=setpmac&sektion=8&manpath=freebsd-release-ports) 组件实施。 `setfmac` 命令是用来对系统客体设置 MAC 标签的， 而 `setpmac` 则是用来对系统主体设置标签的。 例如：

```
# `setfmac biba/high test`
```

若以上命令不发生错误则会直接返回命令提示符， 只有当发生错误时， 这些命令才会给出提示， 这和 [chmod(1)](http://www.FreeBSD.org/cgi/man.cgi?query=chmod&sektion=1&manpath=freebsd-release-ports) 和 [chown(8)](http://www.FreeBSD.org/cgi/man.cgi?query=chown&sektion=8&manpath=freebsd-release-ports) 命令类似。 某些情况下， 以上命令产生的错误可能是 Permission denied， 一般在受限客体上设置或修改设置时会产生此错误。 [^([9])](#ftn.idp80134256) 系统管理员可使用以下命令解决此问题：

```
# `setfmac biba/high test`
Permission denied
# `setpmac biba/low setfmac biba/high test`
# `getfmac test`
test: biba/high
```

如上所示， 通过 `setpmac` 对被调用的进程赋予不同的标签， 以覆盖安全策略模块的设置。 `getpmac` 组件通常用于当前运行的进程， 如 sendmail： 尽管其使用进程编号来替代命令， 其逻辑是相同的。 如果用户试图对其无法访问的文件进行操作， 根据所加载的安全策略模块的规则， 函数 `mac_set_link` 将会给出 Operation not permitted 的错误提示。

#### 17.4.1.1. 一般标签类型

[mac_biba(4)](http://www.FreeBSD.org/cgi/man.cgi?query=mac_biba&sektion=4&manpath=freebsd-release-ports)、 [mac_mls(4)](http://www.FreeBSD.org/cgi/man.cgi?query=mac_mls&sektion=4&manpath=freebsd-release-ports) 及 [mac_lomac(4)](http://www.FreeBSD.org/cgi/man.cgi?query=mac_lomac&sektion=4&manpath=freebsd-release-ports) 策略模块提供了设定简单标签的功能， 其值应该是 high、 equal 及 low 之一。 以下是对这些标签功能的简单描述：

*   `low` 标签被认为是主体或客体所具有的最低层次的标签设定。 对主体或客体采用此设定， 将阻止其访问标签为 high 的客体或主体。

*   `equal` 标签只能被用于不希望受策略控制的客体上。

*   `high` 标签对客体或主体采用可能的最高设定。

至于每个策略模块， 每种设定都会产生不同的信息流指令。 阅读联机手册中相关的章节将进一步阐明这些一般标签配置的特点。

##### 17.4.1.1.1. 标签高级配置

如下所示， 用于 `比较方式:区间+区间` (`comparison:compartment+compartment`) 的标签等级数：

```
biba/10:2+3+6(5:2+3-20:2+3+4+5+6)
```

其含义为：

“Biba 策略标签”/“等级 10” ：“区间 2、 3 及 6”： (“等级 5 ...”)

本例中， 第一个等级将被认为是 “有效区间” 的 “有效等级”， 第二个等级是低级等级， 最后一个则是高级等级。 大多数配置中并不使用这些设置， 实际上， 它们是为更高级的配置准备的。

当把它们应用在系统客体上时， 则只有当前的等级/区间， 因为它们反映可以实施访问控制的系统中可用的范围， 以及网络接口。

等级和区间， 可以用来在一对主体和客体之间建立一种称为 “支配 (dominance)” 的关系， 这种关系可能是主体支配客体， 客体支配主体， 互不支配或互相支配。 “互相支配” 这种情况会在两个标签相等时发生。 由于 Biba 的信息流特性， 您可以设置一系列区间， “need to know”， 这可能发生于项目之间， 而客体也由其对应的区间。 用户可以使用 `su` 和 `setpmac` 来将他们的权限进一步细分， 以便在没有限制的区间里访问客体。

#### 17.4.1.2. 用户和标签设置

用户本身也需要设置标签， 以使其文件和进程能够正确地与系统上定义的安全策略互动， 这是通过使用登录分级在文件 `login.conf` 中配置的。 每个使用标签的策略模块都会进行用户分级设定。

以下是一个使用所有策略模块的例子：

```
default:\
	:copyright=/etc/COPYRIGHT:\
	:welcome=/etc/motd:\
	:setenv=MAIL=/var/mail/$,BLOCKSIZE=K:\
	:path=~/bin:/sbin:/bin:/usr/sbin:/usr/bin:/usr/local/sbin:/usr/local/bin:\
	:manpath=/usr/share/man /usr/local/man:\
	:nologin=/usr/sbin/nologin:\
	:cputime=1h30m:\
	:datasize=8M:\
	:vmemoryuse=100M:\
	:stacksize=2M:\
	:memorylocked=4M:\
	:memoryuse=8M:\
	:filesize=8M:\
	:coredumpsize=8M:\
	:openfiles=24:\
	:maxproc=32:\
	:priority=0:\
	:requirehome:\
	:passwordtime=91d:\
	:umask=022:\
	:ignoretime@:\
	:label=partition/13,mls/5,biba/10(5-15),lomac/10[2]:
```

`label` 选项用以设定用户分级默认标签， 该标签将由 MAC 执行。 用户绝不会被允许更改该值， 因此其从用户的观点看不是可选的。 当然， 在真实情况的配置中， 管理员不会希望启用所有策略模块。 我们建议您在实施以上配置之前阅读本章的其余部分。

### 注意:

用户也许会在首次登录后更改其标签， 尽管如此， 这仅仅是策略的主观局限性。 上面的例子告诉 Biba 策略， 进程的最小完整性是为 5， 最大完整性为 15， 默认且有效的标签为 10。 进程将以 10 的完整性运行直至其决定更改标签， 这可能是由于用户使用了 setpmac 命令 (该操作将在登录时被 Biba 限制在一定用户范围之内)。

在所有情况下， 修改 `login.conf` 之后， 都必须使用 `cap_mkdb` 重编译登录分级 capability 数据库， 这在接下来的例子和讨论中就会有所体现。

很多站点可能拥有数目可观的用户需要不同的用户分级， 注意到这点是大有裨益的。 深入来说就是需要事先做好计划， 因为管理起来可能十分困难。

在 FreeBSD 以后的版本中， 将包含一种将用户映射到标签的新方式， 尽管如此， 这也要到 FreeBSD 5.3 之后的某个时间才能实现。

#### 17.4.1.3. 网络接口和标签设定

也可以在网络接口上配置标签， 以控制进出网络的数据流。 在所有情况下， 策略都会以适应客体的方式运作。 例如， 在 `biba` 中设置为高的用户， 就不能访问标记为低的网络接口。

`maclabel` 可以作为 `ifconfig` 的参数用于设置网络接口的 MAC 标签。 例如：

```
# `ifconfig bge0 maclabel biba/equal`
```

将在 [bge(4)](http://www.FreeBSD.org/cgi/man.cgi?query=bge&sektion=4&manpath=freebsd-release-ports) 接口上设置 `biba/equal` 的 MAC 标签。 当使用类似 `biba/high(low-high)` 这样的标签时， 整个标签应使用引号括起来； 否则将发生错误。

每一个支持标签的策略模块都提供了用于在网络接口上禁用该 MAC 标签的系统控制变量。 将标签设置为 `equal` 的效果与此类似。 请参见 `sysctl` 的输出、 策略模块的联机手册， 或本章接下来的内容， 以了解更进一步的详情。

### 17.4.2. 用单一标签还是多重标签？

默认情况下， 系统采用的是 `singlelabel` 选项。 但这对管理员意味着什么呢？ 两种策略之间存在很多的不同之处， 它们在系统安全模型的灵活性方面， 提供了不同的选择。

`singlelabel` 只允许在每个主体或客体上使用一个标签， 如 `biba/high`。 这降低了管理的开销， 但也同时降低了支持标签的策略的灵活性。 许多管理员可能更希望在安全策略中使用 `multilabel`。

`multilabel` 选项允许每一个主体或客体拥有各自独立的 MAC 标签， 起作用与标准的、 只允许整个分区上使用一个的 `singlelabel` 选项类似。 `multilabel` 和 `single` 标签选项只有对实现了标签功能的那些策略， 如 Biba、 Lomac、 MLS 以及 SEBSD 才有意义。

很多情况下是不需要设置 `multilabel` 的。 考虑下列情形和安全模型：

*   使用了 MAC 以及许多混合策略的 FreeBSD web-服务器。

*   这台机器上的整个系统中只需要一个标签， 即 `biba/high`。 此处的文件系统并不需要 `multilabel` 选项， 因为有效的 label 只有一个。

*   因为这台机器将作为 Web 服务器使用， 因此应该以 `biba/low` 运行 Web 服务， 以杜绝向上写。 Biba 策略以及它如何运作将在稍后予以讨论， 因此， 如果您感觉前面的说明难以理解的话， 请继续阅读下面的内容， 再回来阅读这些内容就会有较为清晰的认识了。 服务器可以使用设置为 `biba/low` 的单独的分区， 用于保持其运行环境的状态。 这个例子中还省略了许多内容， 例如， 如何为数据配置访问限制、 参数配置和用户的设置； 它只是为前述的内容提供一个简单的例子。

如果打算使用非标签式策略， 就不需要 `multilabel` 选项了。 这些策略包括 `seeotheruids`、 `portacl` 和 `partition`。

另一个需要注意的事情是， 在分区上使用 `multilabel` 并建立基于 `multilabel` 可能会提高系统管理的开销， 因为文件系统中的所有客体都需要指定标签。 这包括对目录、文件， 甚至设备节点。

接下来的命令将在需要使用多个标签的文件系统上设置 `multilabel`。 这一操作只能在单用户模式下完成：

```
# `tunefs -l enable /`
```

交换区不需要如此配置。

### 注意:

某些用户可能会在根分区上配置 `multilabel` 标志时遇到困难。 如果发生这样的情况， 请复查本章的 第 17.17 节 “MAC 框架的故障排除”。

* * *

[^([9])](#idp80134256) 其它情况也能导致不同的执行失败。 例如， 文件可能并不隶属于尝试重标签该文件的用户， 客体可能不存在或着是只读的。 文件的某一属性、 进程的某一属性或新的自定义标签值的某一属性， 将使强制式策略不允许进程重标签文件。 例如： 低完整性的用户试图修改高完整性文件的标签， 或者低完整性的用户试图将低完整性文件的标签改为高完整性标签。

## 17.5. 规划安全配置

在实施新技术时， 首先进行规划都是非常好的习惯。 在这段时间， 管理员一般都应 “进行全面的考察”， 这至少应包括下列因素：

*   方案实施的必要条件；

*   方案实施的目标；

就实施 MAC 而言， 这包括：

*   如何在目标系统上对信息和资源进行分类。

*   需要限制哪类信息或资源的访问， 以及应采用何种限制。

*   需要使用哪些 MAC 模块来完成这些目标。

尽管重新配置并修改系统资源和安全配置是可行的， 但查找整个系统并修复暨存的文件和用户帐号并不是一件轻而易举的事情。 规划有助于完成无问题且有效的可信系统实施。 *事先* 对采用 MAC 的可信系统， 以及其配置做试运行十分有益， 因为这对实施的成败至关重要。 草率散漫地配置 MAC 通常是导致失败的祸根。

不同的环境可能会有不同的需求。 建立多层次而完备的安全配置， 可以减少系统正式运转之后所需要的微调。 同样地， 接下来的章节将介绍管理员能够使用的各种不同的模块； 描述它们的使用和配置； 除此之外还有一些关于它们最适合的情景的介绍。 例如， web 服务器可能希望使用 [mac_biba(4)](http://www.FreeBSD.org/cgi/man.cgi?query=mac_biba&sektion=4&manpath=freebsd-release-ports) 和 [mac_bsdextended(4)](http://www.FreeBSD.org/cgi/man.cgi?query=mac_bsdextended&sektion=4&manpath=freebsd-release-ports) 策略， 而其他情况下， 例如一台机器上只有少量的本地用户时， [mac_partition(4)](http://www.FreeBSD.org/cgi/man.cgi?query=mac_partition&sektion=4&manpath=freebsd-release-ports) 则是不错的选择。

## 17.6. 模块配置

在 MAC 框架中的每个模块， 都可以像前述那样连编入内核， 或作为运行时内核模块加载。 推荐的用法， 是通过在 `/boot/loader.conf` 加入适当的设置， 以便在系统启动时的初始化操作过程中加载这些模块。

接下来的一些小节， 将讨论许多 MAC 模块， 并简单介绍它们的功能。 此外， 这一章还将介绍一些具体环境中的用例。 某些模块支持一种称为标签 (labeling) 的用法， 它可以通过使用类似 “允许做这个而不允许做那个” 的标签来实现访问控制。 标签配置文件可以控制允许的文件访问方式、 网络通讯， 以及许多其他权限。 在前一节中， 我们已经展示了文件系统中如何通过 `multilabel` 标志来启用基于文件或分区的访问控制的方法。

单标签配置在整个系统中只强制一个标签的限制， 这也是 `tunefs` 选项为什么是 `multilabel` 的原因。

## 17.7. MAC seeotheruids 模块

模块名： `mac_seeotheruids.ko`

对应的内核配置： `options MAC_SEEOTHERUIDS`

引导选项： `mac_seeotheruids_load="YES"`

[mac_seeotheruids(4)](http://www.FreeBSD.org/cgi/man.cgi?query=mac_seeotheruids&sektion=4&manpath=freebsd-release-ports) 模块模仿并扩展了 `security.bsd.see_other_uids` 和 `security.bsd.see_other_gids` `sysctl` 变量。 这一模块并不需要预先配置标签， 它能够透明地与其他模块协同工作。

加载模块之后， 下列 `sysctl` 变量可以用来控制其功能：

*   `security.mac.seeotheruids.enabled` 将启用模块的功能， 并使用默认的配置。 这些默认设置将阻止用户看到其他用户的进程和 socket。

*   `security.mac.seeotheruids.specificgid_enabled` 将允许特定的组从这一策略中和面。 要将某些组排除在这一策略之外， 可以用 `security.mac.seeotheruids.specificgid=XXX` `sysctl` 变量。 前述例子中， *`XXX`* 应替换为希望不受限的组 ID 的数值形式。

*   `security.mac.seeotheruids.primarygroup_enabled` 可以用来将特定的主要组排除在策略之外。 使用这一变量时， 不能同时设置 `security.mac.seeotheruids.specificgid_enabled`。

## 17.8. MAC bsdextended 模块

模块名： `mac_bsdextended.ko`

对应的内核配置： `options MAC_BSDEXTENDED`

引导选项： `mac_bsdextended_load="YES"`

[mac_bsdextended(4)](http://www.FreeBSD.org/cgi/man.cgi?query=mac_bsdextended&sektion=4&manpath=freebsd-release-ports) 模块能够强制文件系统防火墙策略。 这一模块的策略提供了标准文件系统权限模型的一种扩展， 使得管理员能够建立一种类似防火墙的规则集， 以文件系统层次结构中的保护文件、 实用程序，以及目录。 在尝试访问文件系统客体时， 会遍历规则表， 直至找到匹配的规则， 或到达表尾。 这一行为可以通过修改 [sysctl(8)](http://www.FreeBSD.org/cgi/man.cgi?query=sysctl&sektion=8&manpath=freebsd-release-ports) 参数， security.mac.bsdextended.firstmatch_enabled 来进行设置。 与 FreeBSD 中的其他防火墙设置类似， 也可以建一个文件来配置访问控制策略， 并通过 [rc.conf(5)](http://www.FreeBSD.org/cgi/man.cgi?query=rc.conf&sektion=5&manpath=freebsd-release-ports) 变量的配置在系统引导时加载它。

规则表可以通过工具 [ugidfw(8)](http://www.FreeBSD.org/cgi/man.cgi?query=ugidfw&sektion=8&manpath=freebsd-release-ports) 工具来输入， 其语法类似 [ipfw(8)](http://www.FreeBSD.org/cgi/man.cgi?query=ipfw&sektion=8&manpath=freebsd-release-ports)。 此外还可以通过使用 [libugidfw(3)](http://www.FreeBSD.org/cgi/man.cgi?query=libugidfw&sektion=3&manpath=freebsd-release-ports) 库来开发其他的工具。

当使用这一模块模块时应极其小心； 不正确的使用将导致文件系统的某些部分无法访问。

### 17.8.1. 例子

在加载了 [mac_bsdextended(4)](http://www.FreeBSD.org/cgi/man.cgi?query=mac_bsdextended&sektion=4&manpath=freebsd-release-ports) 模块之后， 下列命令可以用来列出当前的规则配置：

```
# `ugidfw list`
0 slots, 0 rules
```

如希望的那样， 目前还没有定义任何规则。 这意味着一切都还可以访问。 要创建一个阻止所有用户， 而保持 `root` 不受影响的规则， 只需运行下面的命令：

```
# `ugidfw add subject not uid root new object not uid root mode n`
```

这本身可能是一个很糟糕的主意， 因为它会阻止所有用户执行哪怕最简单的命令， 例如 `ls`。 更富于爱心的规则可能是：

```
# `ugidfw set 2 subject uid user1 object uid user2 mode n`
# `ugidfw set 3 subject uid user1 object gid user2 mode n`
```

这将阻止任何 `user1` 对 `*`user2`*` 的主目录的全部访问， 包括目录列表。

`user1` 可以用 `not uid *`user2`*` 代替。 这将同样的强制访问控制实施在所有用户， 而不是单个用户上。

### 注意:

`root` 用户不会受到这些变动的影响。

我们已经给出了 [mac_bsdextended(4)](http://www.FreeBSD.org/cgi/man.cgi?query=mac_bsdextended&sektion=4&manpath=freebsd-release-ports) 模块如何帮助加强文件系统的大致介绍。 要了解更进一步的信息， 请参见 [mac_bsdextended(4)](http://www.FreeBSD.org/cgi/man.cgi?query=mac_bsdextended&sektion=4&manpath=freebsd-release-ports) 和 [ugidfw(8)](http://www.FreeBSD.org/cgi/man.cgi?query=ugidfw&sektion=8&manpath=freebsd-release-ports) 联机手册。

## 17.9. MAC ifoff 模块

模块名： `mac_ifoff.ko`

对应的内核配置： `options MAC_IFOFF`

引导选项： `mac_ifoff_load="YES"`

[mac_ifoff(4)](http://www.FreeBSD.org/cgi/man.cgi?query=mac_ifoff&sektion=4&manpath=freebsd-release-ports) 模块完全是为了立即禁止网络接口， 以及阻止在系统初启时启用网络接口而设计的。 它不需要再系统中配置任何标签， 也不依赖于其他 MAC 模块。

绝大多数特性都可以通过调整下面的 `sysctl` 来加以控制。

*   `security.mac.ifoff.lo_enabled` 表示 启用/禁用 环回接口 ([lo(4)](http://www.FreeBSD.org/cgi/man.cgi?query=lo&sektion=4&manpath=freebsd-release-ports)) 上的全部流量。

*   `security.mac.ifoff.bpfrecv_enabled` 表示 启用/禁用 伯克利包过滤器 ([bpf(4)](http://www.FreeBSD.org/cgi/man.cgi?query=bpf&sektion=4&manpath=freebsd-release-ports)) 接口上的全部流量。

*   `security.mac.ifoff.other_enabled` 将在所有其他接口 启用/禁用 网络。

最为常用的 [mac_ifoff(4)](http://www.FreeBSD.org/cgi/man.cgi?query=mac_ifoff&sektion=4&manpath=freebsd-release-ports) 用法之一是在不允许引导过程中出现网络流量的环境中监视网络。 另一个建议的用法是撰写一个使用 [security/aide](http://www.freebsd.org/cgi/url.cgi?ports/security/aide/pkg-descr) 的脚本， 以便自动地在受保护的目录中发现新的或修改过的文件时切断网络。

## 17.10. MAC portacl 模块

模块名： `mac_portacl.ko`

对应的内核配置： `MAC_PORTACL`

引导选项： `mac_portacl_load="YES"`

[mac_portacl(4)](http://www.FreeBSD.org/cgi/man.cgi?query=mac_portacl&sektion=4&manpath=freebsd-release-ports) 模块可以用来通过一系列 `sysctl` 变量来限制绑定本地的 TCP 和 UDP 端口。 本质上 [mac_portacl(4)](http://www.FreeBSD.org/cgi/man.cgi?query=mac_portacl&sektion=4&manpath=freebsd-release-ports) 使得 非-`root` 用户能够绑定到它所指定的特权端口， 也就是那些编号小于 1024 的端口。

在加载之后， 这个模块将在所有的 socket 上启用 MAC 策略。 可以调整下列一些配置：

*   `security.mac.portacl.enabled` 将完全 启用/禁用 策略。

*   `security.mac.portacl.port_high` 将设置为 [mac_portacl(4)](http://www.FreeBSD.org/cgi/man.cgi?query=mac_portacl&sektion=4&manpath=freebsd-release-ports) 所保护的最高端口号。

*   `security.mac.portacl.suser_exempt` 如果设置为非零值， 表示将 `root` 用户排除在策略之外。

*   `security.mac.portacl.rules` 将指定实际的 mac_portacl 策略； 请参见下文。

实际的 `mac_portacl` 策略， 是在 `security.mac.portacl.rules` sysctl 所指定的一个下列形式的字符串： `rule[,rule,...]` 其中可以给出任意多个规则。 每一个规则的形式都是： `idtype:id:protocol:port`。 这里的 *`idtype`* 参数可以是 `uid` 或 `gid`， 分别表示将 *`id`* 参数解释为用户 id 或组 id。 *`protocol`* 参数可以用来确定希望应用到 TCP 或 UDP 协议上， 方法是把这一参数设置为 `tcp` 或 `udp`。 最后的 *`port`* 参数则给出了所指定的用户或组能够绑定的端口号。

### 注意:

由于规则集会直接由内核加以解释， 因此只能以数字形式表示用户 ID、 组 ID， 以及端口等参数。 换言之， 您不能使用用户、 组， 或端口服务的名字来指定它们。

默认情况下， 在 类-UNIX® 系统中， 编号小于 1024 的端口只能为特权进程使用或绑定， 也就是那些以 `root` 身份运行的进程。 为了让 [mac_portacl(4)](http://www.FreeBSD.org/cgi/man.cgi?query=mac_portacl&sektion=4&manpath=freebsd-release-ports) 能够允许非特权进程绑定低于 1024 的端口， 就必须首先禁用标准的 UNIX® 限制。 这可以通过把 [sysctl(8)](http://www.FreeBSD.org/cgi/man.cgi?query=sysctl&sektion=8&manpath=freebsd-release-ports) 变量 `net.inet.ip.portrange.reservedlow` 和 `net.inet.ip.portrange.reservedhigh` 设置为 0 来实现。

请参见下面的例子， 或 [mac_portacl(4)](http://www.FreeBSD.org/cgi/man.cgi?query=mac_portacl&sektion=4&manpath=freebsd-release-ports) 联机手册中的说明， 以了解进一步的信息。

### 17.10.1. 例子

下面的例子更好地展示了前面讨论的内容：

```
# `sysctl security.mac.portacl.port_high=1023`
# `sysctl net.inet.ip.portrange.reservedlow=0 net.inet.ip.portrange.reservedhigh=0`
```

首先我们需要设置使 [mac_portacl(4)](http://www.FreeBSD.org/cgi/man.cgi?query=mac_portacl&sektion=4&manpath=freebsd-release-ports) 管理标准的特权端口， 并禁用普通的 UNIX® 绑定限制。

```
# `sysctl security.mac.portacl.suser_exempt=1`
```

您的 `root` 用户不应因此策略而失去特权，　因此请把 `security.mac.portacl.suser_exempt` 设置为一个非零的值。 现在您已经成功地配置了　[mac_portacl(4)](http://www.FreeBSD.org/cgi/man.cgi?query=mac_portacl&sektion=4&manpath=freebsd-release-ports) 模块， 并使其默认与 类-UNIX® 系统一样运行了。

```
# `sysctl security.mac.portacl.rules=uid:80:tcp:80`
```

允许 UID 为 80 的用户 (正常情况下， 应该是 `www` 用户) 绑定到 80 端口。 这样 `www` 用户就能够运行 web 服务器， 而不需要使用 `root` 权限了。

```
# `sysctl security.mac.portacl.rules=uid:1001:tcp:110,uid:1001:tcp:995`
```

允许 UID 为 1001 的用户绑定 TCP 端口 110 (“pop3”) 和 995 (“pop3s”)。 这样用户就能够启动接受来发到 110 和 995 的连接请求的服务了。

## 17.11. MAC partition (分区) 模块

模块名： `mac_partition.ko`

对应的内核配置： `options MAC_PARTITION`

引导选项： `mac_partition_load="YES"`

[mac_partition(4)](http://www.FreeBSD.org/cgi/man.cgi?query=mac_partition&sektion=4&manpath=freebsd-release-ports) 策略将把进程基于其 MAC 标签放到特定的 “partitions” (分区) 中。 这是一种特殊类型的 [jail(8)](http://www.FreeBSD.org/cgi/man.cgi?query=jail&sektion=8&manpath=freebsd-release-ports)， 但对两者进行比较意义不大。

这个模块应加到 [loader.conf(5)](http://www.FreeBSD.org/cgi/man.cgi?query=loader.conf&sektion=5&manpath=freebsd-release-ports) 文件中， 以便在启动过程中启用这些规则。

绝大多数这一策略的配置是通过 [setpmac(8)](http://www.FreeBSD.org/cgi/man.cgi?query=setpmac&sektion=8&manpath=freebsd-release-ports) 工具来完成的， 它将在后面介绍。 这个策略可以使用下面的 `sysctl`：

*   `security.mac.partition.enabled` 将启用强制的 MAC 进程 partitions。

当启用了这个规则时， 用户将只能看到他们自己的， 以及其他与他们同处一个 partition 的进程， 而不能使用能够越过 partition 的工具。 例如， `insecure` class 中的用户， 就无法使用 `top` 命令， 以及其他需要产生新进程的工具。

要设置或删除 partition 标签中的工具， 需要使用 `setpmac`：

```
# `setpmac partition/13 top`
```

这将把 `top` 命令加入到 `insecure` class 中的用户的标签集。 注意， 所有由 `insecure` class 中的用户产生的进程， 仍然会留在 `partition/13` 标签中。

### 17.11.1. 例子

下面的命令将显示 partition 标签以及进程列表：

```
# `ps Zax`
```

接下来的这个命令将允许察看其他用户的进程 partition 标签， 以及那个用户正在运行的进程：

```
# `ps -ZU trhodes`
```

### 注意:

除非加载了 [mac_seeotheruids(4)](http://www.FreeBSD.org/cgi/man.cgi?query=mac_seeotheruids&sektion=4&manpath=freebsd-release-ports) 策略， 否则用户就看不到 `root` 的标签。

非常手工化的实现， 可能会在 `/etc/rc.conf` 中禁用所有的服务， 并用脚本来按不同的标签来启动它们。

### 注意:

下面的几个策略支持基于所给出的三种标签的完整性设定。 这些选项， 连同它们的限制， 在模块的联机手册中进行了进一步介绍。

## 17.12. MAC 多级 (Multi-Level) 安全模块

模块名： `mac_mls.ko`

对应的内核配置： `options MAC_MLS`

引导选项： `mac_mls_load="YES"`

[mac_mls(4)](http://www.FreeBSD.org/cgi/man.cgi?query=mac_mls&sektion=4&manpath=freebsd-release-ports) 策略， 通过严格控制信息流向来控制系统中主体和客体的访问。

在 MLS 环境中， “许可 (clearance)” 级别会在每一个主体或客体标签上进行设置， 连同对应的区间。 由于这些透明度或敏感度可以有六千多个层次， 因此为每一个主体或客体进行配置将是一件让任何系统管理员都感到头疼的任务。 所幸的是， 这个策略中已经包含了三个 “立即可用的” 标签。

这些标签是 `mls/low`、 `mls/equal` 以及 `mls/high`。 由于这些标签已经在联机手册中进行了介绍， 这里只给出简要的说明：

*   `mls/low` 标签包含了最低配置， 从而允许其他客体支配它。 任何标记为 `mls/low` 的客体将是地透明度的， 从而不允许访问更高级别的信息。 此外， 这个标签也阻止拥有较高透明度的客体向其写入或传递信息。

*   `mls/equal` 标签应放到不希望使用这一策略的客体上。

*   `mls/high` 标签是允许的最高级别透明度。 指定了这个标签的客体将支配系统中的其他客体； 但是， 它们将不允许向较低级别的客体泄露信息。

MLS 提供了：

*   提供了一些非层次分类的层次安全模型；

*   固定规则： 不允许向上读， 不允许向下写 (主体可以读取同级或较低级别的客体， 但不能读取高级别的。 类似地， 主体可以向同级或较高级写， 而不能向下写)；

*   保密 (防止不适当的数据透露)；

*   系统设计的基础要点， 是在多个敏感级别之间并行地处理数据 (而不泄露秘密的和机密的信息)。

下列 `sysctl` 可以用来配置特殊服务和接口：

*   `security.mac.mls.enabled` 用来启用/禁用 MLS 策略。

*   `security.mac.mls.ptys_equal` 将所有的 [pty(4)](http://www.FreeBSD.org/cgi/man.cgi?query=pty&sektion=4&manpath=freebsd-release-ports) 设备标记为 `mls/equal`。

*   `security.mac.mls.revocation_enabled` 可以用来在标签转为较低 grade 时撤销客体访问权。

*   `security.mac.mls.max_compartments` 可以用来设置客体的最大区间层次； 基本上， 这也就是系统中所允许的最大区间数。

要管理 MLS 标签， 可以使用 [setfmac(8)](http://www.FreeBSD.org/cgi/man.cgi?query=setfmac&sektion=8&manpath=freebsd-release-ports) 命令。 要在客体上指定标签， 需要使用下面的命令：

```
# `setfmac mls/5 test`
```

下述命令用于取得文件 `test` 上的 MLS 标签：

```
# `getfmac test`
```

以上是对于 MLS 策略提供功能的概要。 另一种做法是在 `/etc` 中建立一个主策略文件， 并在其中指定 MLS 策略信息， 作为 `setfmac` 命令的输入。 这种方法， 将在其他策略之后进行介绍。

### 17.12.1. 规划托管敏感性

通过使用多级安全策略模块， 管理员可以规划如何控制敏感信息的流向。 默认情况下， 由于其默认的禁止向上读以及向下写的性质， 系统会默认将所有客体置于较低的状态。 这样， 所有的客体都可以访问， 而管理员则可以在配置阶段慢慢地进行提高信息的敏感度这样的修改。

除了前面介绍的三种基本标签选项之外， 管理员还可以根据需要将用户和用户组进行分组， 以阻止它们之间的信息流。 一些人们比较熟悉的信息限界词汇， 如 `机密`、 `秘密`， 以及 `绝密` 可以方便您理解这一概念。 管理员也可以简单地根据项目级别建不同的分组。 无论采用何种分类方法， 在实施限制性的策略之前， 都必须首先想好如何进行规划。

这个安全策略模块最典型的用例是电子商务的 web 服务器， 其上的文件服务保存公司的重要信息以及金融机构的情况。 对于只有两三个用户的个人工作站而言， 则可能不甚适用。

## 17.13. MAC Biba 模块

模块名： `mac_biba.ko`

对应的内核配置： `options MAC_BIBA`

引导选项： `mac_biba_load="YES"`

[mac_biba(4)](http://www.FreeBSD.org/cgi/man.cgi?query=mac_biba&sektion=4&manpath=freebsd-release-ports) 模块将加载 MAC Biba 策略。 这个策略与 MLS 策略非常类似， 只是信息流的规则有些相反的地方。 通俗地说， 这就是防止敏感信息向下传播， 而 MLS 策略则是防止敏感信息的向上传播； 因而， 这一节的许多内容都可以同时应用于两种策略。

在 Biba 环境中， “integrity” (完整性) 标签， 将设置在每一个主体或客体上。 这些标签是按照层次级别建立的。 如果客体或主体的级别被提升， 其完整性也随之提升。

被支持的标签是 `biba/low`， `biba/equal` 以及 `biba/high`； 解释如下：

*   `biba/low` 标签是客体或主体所能拥有的最低完整性级别。 在客体或主体上设置它， 将阻止其在更高级别客体或主体对其进行的写操作， 虽然读仍被允许。

*   `biba/equal` 标签只应在那些希望排除在策略之外的客体上设置。

*   `biba/high` 允许向较低标签的客体上写， 但不允许读那些客体。 推荐在那些可能影响整个系统完整性的客体上设置这个标签。

Biba 提供了：

*   层次式的完整性级别， 并提供了一组非层次式的完整性分类；

*   固定规则： 不允许向上写， 不允许向下读 (与 MLS 相反)。 主体可以在它自己和较低的级别写， 但不能向更高级别实施写操作。 类似地， 主体也可以读在其自己的， 或更高级别的客体， 但不能读取较低级别的客体；

*   完整性 (防止对数据进行不正确的修改)；

*   完整性级别 (而不是 MLS 的敏感度级别)。

下列 `sysctl` 可以用于维护 Biba 策略。

*   `security.mac.biba.enabled` 可以用来在机器上启用/禁用是否实施 Biba 策略。

*   `security.mac.biba.ptys_equal` 可以用来在 [pty(4)](http://www.FreeBSD.org/cgi/man.cgi?query=pty&sektion=4&manpath=freebsd-release-ports) 设备上禁用 Biba 策略。

*   `security.mac.biba.revocation_enabled` 将在支配主体发生变化时强制撤销对客体的访问权。

要操作系统客体上的 Biba 策略， 需要使用 `setfmac` 和 `getfmac` 命令：

```
# `setfmac biba/low test`
# `getfmac test`
test: biba/low
```

### 17.13.1. 规划托管完整性

与敏感性不同， 完整性是要确保不受信方不能对信息进行篡改。 这包括了在主体和客体之间传递的信息。 这能够确保用户只能修改甚至访问需要他们的信息。

[mac_biba(4)](http://www.FreeBSD.org/cgi/man.cgi?query=mac_biba&sektion=4&manpath=freebsd-release-ports) 安全策略模块允许管理员指定用户能够看到和执行的文件和程序， 并确保这些文件能够为系统及用户或用户组所信任， 而免受其他威胁。

在最初的规划阶段， 管理员必须做好将用户分成不同的等级、 级别和区域的准备。 在启动前后， 包括数据以及程序和使用工具在内的客体， 用户都会无法访问。 一旦启用了这个策略模块， 系统将默认使用高级别的标签， 而划分用户级别和等级的工作则交由管理员来进行配置。 与前面介绍的级别限界不同， 好的规划方法可能还包括 topic。 例如， 只允许开发人员修改代码库、 使用源代码编译器， 以及其他开发工具， 而其他用户则分入其他类别， 如测试人员、 设计人员， 以及普通用户， 这些用户可能只拥有读这些资料的权限。

通过其自然的安全控制， 完整性级别较低的主体， 就会无法向完整性级别高的主体进行写操作； 而完整性级别较高的主体， 也不能观察或读较低完整性级别的客体。 通过将客体的标签设为最低级， 可以阻止所有主体对其进行的访问操作。 这一安全策略模块预期的应用场合包括受限的 web 服务器、 开发和测试机， 以及源代码库。 而对于个人终端、 作为路由器的计算机， 以及网络防火墙而言， 它的用处就不大了。

## 17.14. MAC LOMAC 模块

模块名： `mac_lomac.ko`

对应的内核配置： `options MAC_LOMAC`

引导选项： `mac_lomac_load="YES"`

和 MAC Biba 策略不同， [mac_lomac(4)](http://www.FreeBSD.org/cgi/man.cgi?query=mac_lomac&sektion=4&manpath=freebsd-release-ports) 策略只允许在降低了完整性级别之后， 才允许在不破坏完整性规则的前提下访问较低完整性级别的客体。

MAC 版本的 Low-watermark 完整性策略不应与较早的 [lomac(4)](http://www.FreeBSD.org/cgi/man.cgi?query=lomac&sektion=4&manpath=freebsd-release-ports) 实现相混淆， 除了使用浮动的标签来支持主体通过辅助级别区间降级之外， 其工作方式与 Biba 大体相似。 这一次要的区间以 `[auxgrade]` 的形式出现。 当指定包含辅助级别的 lomac 策略时， 其形式应类似于： `lomac/10[2]` 这里数字二 (2) 就是辅助级别。

MAC LOMAC 策略依赖于系统客体上存在普适的标签， 这样就允许主体从较低完整性级别的客体读取， 并对主体的标签降级， 以防止其在之后写高完整性级别的客体。 这就是前面讨论的 `[auxgrade]` 选项， 因此这个策略能够提供更大的兼容性， 而所需要的初始配置也要比 Biba 少。

### 17.14.1. 例子

与 Biba 和 MLS 策略类似； `setfmac` 和 `setpmac` 工具可以用来在系统客体上放置标签：

```
# `setfmac /usr/home/trhodes lomac/high[low]`
# `getfmac /usr/home/trhodes` lomac/high[low]
```

注意， 这里的辅助级别是 `low`， 这一特性只由 MAC LOMAC 策略提供。

## 17.15. MAC Jail 中的 Nagios

下面给出了通过多种 MAC 模块， 并正确地配置策略来实现安全环境的例子。 这只是一个测试， 因此不应被看作四海一家的解决之道。 仅仅实现一个策略， 而忽略它不能解决任何问题， 并可能在生产环境中产生灾难性的后果。

在开始这些操作之前， 必须在每一个文件系统上设置 `multilabel` 选项， 这些操作在这一章开始的部分进行了介绍。 不完成这些操作， 将导致错误的结果。 首先， 请确认已经安装了 [net-mngt/nagios-plugins](http://www.freebsd.org/cgi/url.cgi?ports/net-mngt/nagios-plugins/pkg-descr)、 [net-mngt/nagios](http://www.freebsd.org/cgi/url.cgi?ports/net-mngt/nagios/pkg-descr)， 和 [www/apache13](http://www.freebsd.org/cgi/url.cgi?ports/www/apache13/pkg-descr) 这些 ports， 并对其进行了配置， 且运转正常。

### 17.15.1. 创建一个 insecure (不安全) 用户 Class

首先是在 `/etc/login.conf` 文件中加入一个新的用户 class：

```
insecure:\
:copyright=/etc/COPYRIGHT:\
:welcome=/etc/motd:\
:setenv=MAIL=/var/mail/$,BLOCKSIZE=K:\
:path=~/bin:/sbin:/bin:/usr/sbin:/usr/bin:/usr/local/sbin:/usr/local/bin
:manpath=/usr/share/man /usr/local/man:\
:nologin=/usr/sbin/nologin:\
:cputime=1h30m:\
:datasize=8M:\
:vmemoryuse=100M:\
:stacksize=2M:\
:memorylocked=4M:\
:memoryuse=8M:\
:filesize=8M:\
:coredumpsize=8M:\
:openfiles=24:\
:maxproc=32:\
:priority=0:\
:requirehome:\
:passwordtime=91d:\
:umask=022:\
:ignoretime@:\
:label=biba/10(10-10):
```

并在 default 用户 class 中加入：

```
:label=biba/high:
```

一旦完成上述操作， 就需要运行下面的命令来重建数据库：

```
# `cap_mkdb /etc/login.conf`
```

### 17.15.2. 引导配置

现在暂时还不要重新启动， 我们还需要在 `/boot/loader.conf` 中增加下面几行， 以便让模块随系统初始化一同加载：

```
mac_biba_load="YES"
mac_seeotheruids_load="YES"
```

### 17.15.3. 配置用户

使用下面的命令将 `root` 设为属于默认的 class：

```
# `pw usermod root -L default`
```

所有非 `root` 或系统的用户， 现在需要一个登录 class。 登录 class 是必须的， 否则这些用户将被禁止使用类似 [vi(1)](http://www.FreeBSD.org/cgi/man.cgi?query=vi&sektion=1&manpath=freebsd-release-ports) 这样的命令。 下面的 `sh` 脚本应能完成这个工作：

```
# `for x in `awk -F: '($3 >= 1001) && ($3 != 65534) { print $1 }' \`
	`/etc/passwd`; do pw usermod $x -L default; done;`
```

将 `nagios` 和 `www` 这两个用户归入不安全 class：

```
# `pw usermod nagios -L insecure`
```

```
# `pw usermod www -L insecure`
```

### 17.15.4. 创建上下文文件

接下来需要创建一个上下文文件； 您可以把下面的实例放到 `/etc/policy.contexts` 中。

```
# This is the default BIBA policy for this system.

# System:
/var/run                        biba/equal
/var/run/*                      biba/equal

/dev                            biba/equal
/dev/*                          biba/equal

/var				biba/equal
/var/spool                      biba/equal
/var/spool/*                    biba/equal

/var/log                        biba/equal
/var/log/*                      biba/equal

/tmp				biba/equal
/tmp/*				biba/equal
/var/tmp			biba/equal
/var/tmp/*			biba/equal

/var/spool/mqueue		biba/equal
/var/spool/clientmqueue		biba/equal

# For Nagios:
/usr/local/etc/nagios
/usr/local/etc/nagios/*         biba/10

/var/spool/nagios               biba/10
/var/spool/nagios/*             biba/10

# For apache
/usr/local/etc/apache           biba/10
/usr/local/etc/apache/*         biba/10
```

这个策略通过在信息流上设置限制来强化安全。 在这个配置中， 包括 `root` 和其他用户在内的用户， 都不允许访问 Nagios。 作为 Nagios 一部分的配置文件和进程， 都是完全独立的， 也称为 jailed。

接下来可以用下面的命令将其读入系统：

```
# `setfsmac -ef /etc/policy.contexts /`
# `setfsmac -ef /etc/policy.contexts /`
```

### 注意:

随环境不同前述的文件系统布局可能会有所不同； 不过无论如何， 都只能在一个文件系统上运行它。

在 `/etc/mac.conf` 文件中的 main 小节需要进行下面的修改：

```
default_labels file ?biba
default_labels ifnet ?biba
default_labels process ?biba
default_labels socket ?biba
```

### 17.15.5. 启用网络

在 `/boot/loader.conf` 中增加下列内容：

```
security.mac.biba.trust_all_interfaces=1
```

将下述内容加入 `rc.conf` 中的网络接口配置。 如果主 Internet 配置是通过 DHCP 完成的， 则需要在每次系统启动之后手工执行类似的配置：

```
maclabel biba/equal
```

### 17.15.6. 测试配置

首先要确认 web 服务以及 Nagios 不会随系统的初始化和重启过程而自动启动。 在此之前， 请在此确认 `root` 用户不能访问 Nagios 配置目录中的任何文件 如果 `root` 能够在 `/var/spool/nagios` 中运行 [ls(1)](http://www.FreeBSD.org/cgi/man.cgi?query=ls&sektion=1&manpath=freebsd-release-ports)， 则表示配置有误。 如果配置正确的话， 您会收到一条 “permission denied” 错误信息。

如果一切正常， Nagios、 Apache， 以及 Sendmail 就可以按照适应安全策略的方式启动了。 下面的命令将完成此工作：

```
# `cd /etc/mail && make stop && \
setpmac biba/equal make start && setpmac biba/10\(10-10\) apachectl start && \
setpmac biba/10\(10-10\) /usr/local/etc/rc.d/nagios.sh forcestart`
```

再次检查是否一切正常。 如果不是的话， 请检查日志文件和错误信息。 此外， 还可以用 [sysctl(8)](http://www.FreeBSD.org/cgi/man.cgi?query=sysctl&sektion=8&manpath=freebsd-release-ports) 来临时禁用 [mac_biba(4)](http://www.FreeBSD.org/cgi/man.cgi?query=mac_biba&sektion=4&manpath=freebsd-release-ports) 安全策略模块的强制措施， 并象之前那样进行配置和启动服务。

### 注意:

`root` 用户可以放心大胆地修改安全强制措施， 并编辑配置文件。 下面的命令可以对安全策略进行降级， 并启动一个新的 shell：

```
# `setpmac biba/10 csh`
```

要阻止这种情况发生， 就需要配置 [login.conf(5)](http://www.FreeBSD.org/cgi/man.cgi?query=login.conf&sektion=5&manpath=freebsd-release-ports) 中许可的命令范围了。 如果 [setpmac(8)](http://www.FreeBSD.org/cgi/man.cgi?query=setpmac&sektion=8&manpath=freebsd-release-ports) 尝试执行超越许可范围的命令， 则会返回一个错误， 而不是执行命令。 在这个例子中， 可以把 root 设为 `biba/high(high-high)`。

## 17.16. User Lock Down

这个例子针对的是一个相对较小的存储系统， 其用户数少于五十。 用户能够在其上登录， 除了存储数据之外， 还可以访问一些其他资源。

在这个场景中， [mac_bsdextended(4)](http://www.FreeBSD.org/cgi/man.cgi?query=mac_bsdextended&sektion=4&manpath=freebsd-release-ports) 可以与 [mac_seeotheruids(4)](http://www.FreeBSD.org/cgi/man.cgi?query=mac_seeotheruids&sektion=4&manpath=freebsd-release-ports) 并存， 以达到禁止访问非授权资源， 同时隐藏其他用户的进程的目的。

首先， 在 `/boot/loader.conf` 中加入：

```
mac_seeotheruids_load="YES"
```

随后， 可以通过下述 rc.conf 变量来启用 [mac_bsdextended(4)](http://www.FreeBSD.org/cgi/man.cgi?query=mac_bsdextended&sektion=4&manpath=freebsd-release-ports) 安全策略模块：

```
ugidfw_enable="YES"
```

默认规则保存在 `/etc/rc.bsdextended` 中， 并在系统初始化时加载； 但是， 其中的默认项可能需要进行一些改动。 因为这台机器只为获得了授权的用户提供服务， 因此除了最后两项之外， 其它内容都应保持注释的状态。 这两项规则将默认强制加载属于用户的系统客体。

在这台机器上添加需要的用户并重新启动。 出于测试的目的， 请在两个控制台上分别以不同的用户身份登录。 运行 `ps aux` 命令来看看是否能看到其他用户的进程。 此外， 在其他用户的主目录中运行 [ls(1)](http://www.FreeBSD.org/cgi/man.cgi?query=ls&sektion=1&manpath=freebsd-release-ports) 命令， 如果配置正确， 则这个命令会失败。

不要尝试以 `root` 用户的身份进行测试， 除非您已经修改了特定的 `sysctl` 来阻止超级用户的访问。

### 注意:

在添加新用户时， 他们的 [mac_bsdextended(4)](http://www.FreeBSD.org/cgi/man.cgi?query=mac_bsdextended&sektion=4&manpath=freebsd-release-ports) 规则不会自动出现在规则集表中。 要迅速更新规则集， 只需简单地使用 [kldunload(8)](http://www.FreeBSD.org/cgi/man.cgi?query=kldunload&sektion=8&manpath=freebsd-release-ports) 和 [kldload(8)](http://www.FreeBSD.org/cgi/man.cgi?query=kldload&sektion=8&manpath=freebsd-release-ports) 工具来卸载并重新加载安全策略模块。

## 17.17. MAC 框架的故障排除

在开发过程中， 有一些用户报告了正常配置下出现的问题。 其中的一些问题如下所示：

### 17.17.1. 无法在 `/` 上启用 `multilabel` 选项

`multilabel` 标志在根 (`/`) 分区上没有保持启用状态！

看起来每五十个用户中就有一个遇到这样的问题， 当然， 在我们的初始配置过程中也出现过这样的问题。 更进一步的观察使得我相信这个所谓的 “bug” 是由于文档中不确切的描述， 或对其产生的误解造成的。 无论它是因为什么引发的， 下面的步骤应该能够解决此问题：

1.  编辑 `/etc/fstab` 并将根分区设置为 `ro`， 表示只读。

2.  重新启动并进入单用户模式。

3.  在 `/` 上运行 `tunefs` `-l enable`

4.  重新启动并进入正常的模式。

5.  运行 `mount` `-urw` `/` 并把 `/etc/fstab` 中的 `ro` 改回 `rw`， 然后再次重新启动。

6.  再次检查来自 `mount` 的输出， 已确认根文件系统上正确地设置了 `multilabel`。

### 17.17.2. 在 MAC 之后无法启动 X11 了

在使用 MAC 建立安全的环境之后， 就无法启动 X 了！

这可能是由于 MAC `partition` 策略， 或者对某个 MAC 标签策略进行了错误的配置导致的。 要调试这个问题， 请尝试：

1.  检查错误信息； 如果用户是在 `insecure` class 中， 则 `partition` 策略就可能导致问题。 尝试将用户的 class 重新改为 `default` class， 并使用 `cap_mkdb` 命令重建数据库。 如果这无法解决问题， 则进入第二步。

2.  仔细检查标签策略。 确认针对有问题的用户的策略是正确的， 特别是 X11 应用， 以及 `/dev` 项。

3.  如果这些都无法解决问题， 将出错消息和对您的环境的描述， 发送到 [TrustedBSD](http://www.TrustedBSD.org) 网站上的 TrustedBSD 讨论邮件列表， 或者 [FreeBSD 一般问题邮件列表](http://lists.FreeBSD.org/mailman/listinfo/freebsd-questions) 邮件列表。

### 17.17.3. Error: [_secure_path(3)](http://www.FreeBSD.org/cgi/man.cgi?query=_secure_path&sektion=3&manpath=freebsd-release-ports) cannot stat `.login_conf`

当我试图从 `root` 用户切换到其同中的其他用户时， 出现了错误提示 _secure_path: unable to state .login_conf。

这个提示通常在用户拥有高于它将要成为的那个用户的 标签设定时出现。 例如， 如果系统上的一个用户 `joe` 拥有默认的 `biba/low` 标签， 而 `root` 用户拥有 `biba/high`， 它也就不能查看 `joe` 的主目录， 无论 `root` 是否使用了 `su` 来成为 `joe`。 这种情况下， Biba 完整性模型， 就不会允许 `root` 查看在较低完整性级别中的客体。

### 17.17.4. `root` 用户名被破坏了！

在普通模式， 甚至是单用户模式中， `root` 不被识别。 `whoami` 命令返回了 0 (零) 而 `su` 则提示 who are you?。 到底发生了什么？

标签策略被禁用可能会导致这样的问题， 无论是通过 [sysctl(8)](http://www.FreeBSD.org/cgi/man.cgi?query=sysctl&sektion=8&manpath=freebsd-release-ports) 或是卸载了策略模块。 如果打算禁用策略， 或者临时禁用它， 则登录性能数据库需要重新配置， 在其中删除 `label` 选项。 仔细检查 `login.conf` 以确保所有的 `label` 选项都已经删除， 然后使用 `cap_mkdb` 命令来重建数据库。

这种情况也可能在通过策略来限制访问 `master.passwd` 文件或对应的那个数据库时发生。 这主要是由于管理员修改受某一 label 限制的文件， 而与系统级的通用策略发生了冲突。 这时， 用户信息将由系统直接读取， 而在文件继承了新的 label 之后则会拒绝访问。 此时， 只需使用 [sysctl(8)](http://www.FreeBSD.org/cgi/man.cgi?query=sysctl&sektion=8&manpath=freebsd-release-ports) 禁用这一策略， 一切就会恢复正常了。

## 第 18 章 安全事件审计

原作 Tom Rhodes 和 Robert Watson.

## 18.1. 概述

FreeBSD 中包含了对于细粒度安全事件审计的支持。 事件审计能够支持可靠的、 细粒度且可配置的， 对于各类与安全有关的系统事件， 包括登录、 配置变更， 以及文件和网络访问等的日志记录。 这些日志记录对于在正在运行的系统上实施监控、 入侵检测和事后分析都十分重要。 FreeBSD 实现了 Sun 所发布的 BSM API 和文件格式， 并且与 Sun™ 的 Solaris™ 和 Apple® 的 Mac OS® X 审计实现兼容。

这一章的重点是安装和配置事件审计。 它介绍了事件策略， 并提供了一个审计的配置例子。

读完这章， 您将了解：

*   事件审计是什么， 以及它如何工作。

*   如何在 FreeBSD 上为用户和进程配置事件审计。

*   如何使用审计记录摘要和复审工具来对审计记录进行复审。

阅读这章之前， 您应该：

*   理解 UNIX® 和 FreeBSD 的基础知识 (第 4 章 *UNIX 基础*)。

*   熟悉关于内核配置和编译的基本方法 (第 9 章 *配置 FreeBSD 的内核*)。

*   熟悉安全知识以及如何在 FreeBSD 运用它们 (第 15 章 *安全*)。

### 警告:

审计机制中存在一些已知的限制， 例如并不是所有与安全有关的系统事件都可以审计， 另外某些登录机制， 例如基于 X11 显示管理器， 以及第三方服务的登录机制， 都不会在用户的登录会话中正确配置审计。

安全审计机制能够对系统活动生成非常详细的记录信息： 在繁忙的系统中， 记帐数据如果配置不当会非常的大， 并在一周内迅速超过几个 GB 的尺寸。 管理员应考虑审计配置中的导致磁盘空间需求的这些问题。 例如， 可能需要为 `/var/audit` 目录单独分配一个文件系统， 以防止在审计日志所用的文件系统被填满时影响其它文件系统。

## 18.2. 本章中的一些关键术语

在开始阅读这章之前， 我们需要解释一下与审计有关的一些关键的术语：

*   *事件 (event)*： 可审计事件是指能够被审计子系统记录的任何事件。 举例说来， 与安全有关的事件包括创建文件、 建立网络连接， 以及以某一用户身份登录， 等等。 任何事件必要么是 “有主 (attributable)” 的， 即可以最终归于某一已通过验证的用户的身份， 反之， 则称该事件是 “无主 (non-attributable)” 的。 无主事件可以是发生在登录过程成功之前的任何事件， 例如尝试一次无效密码等。

*   *类 (class)*： 事件类是指相关事件的一个命名集合， 通常在筛选表达式中使用。 常用的事件类包括 “创建文件” (fc)、 “执行” (ex) 和 “登入和注销” (lo)。

*   *记录 (record)*： 记录是指描述一个安全事件的日志项。 记录包括记录事件类型、 执行操作的主体 (用户) 信息、 日期和事件信息， 以及与之相关的对象或参数信息， 最后是操作成功或失败。

*   *账目 (trail)*： 审计账目， 或日志文件， 包含了一系列描述安全事件的审计记录。 典型情况下， 审计账目基本上是以事件发生的时间顺序记录的。 只有获得授权的进程， 才能够向审计账目中提交记录。

*   *筛选表达式 (selection expression)*： 筛选表达式是包含一系列前缀和审计事件类名字， 用以匹配事件的字符串。

*   *预选 (preselection)*： 系统通过这一过程来识别事件是否是管理员所感兴趣的， 从而避免为他们不感兴趣的事件生成记录。 预选配置使用一系列选择表达式， 用以识别事件类别、 要审计的用户， 以及适用于验证过用户身份， 以及未验证用户身份的进程的全局配置。

*   *浓缩 (reduction)*： 从现有的审计记帐中筛选出用于保留、 打印或分析的过程。 除此之外， 它也表示从审计记帐中删去不需要的审计记录的过程。 通过使用浓缩操作， 管理员可以实现预留审计数据的策略。 例如， 详细的审计记帐信息， 可能会保留一个月之久， 但在这之后， 则对这些记帐信息执行浓缩操作， 只保留登录信息用于存档。

## 18.3. 安装审计支持

对于事件审计的支持， 已经随标准的 `installworld` 过程完成。 管理员可以通过查看 `/etc/security` 的内容来确认这一点。 您应能看到一些名字以 *audit* 开头的文件， 例如 `audit_event`。

对于审计功能的用户态支持目前是作为 FreeBSD 基本系统的一部分来安装的。 默认内核中也包含了对于事件审计的内核支持， 但如果您使用的是定制内核， 就必须在内核配置文件中明确指定希望添加这一支持：

```
options	AUDIT
```

接下来， 您应按照 第 9 章 *配置 FreeBSD 的内核* 中所介绍的步骤来完成一次内核的编译和安装。

在编译好并安装了内核， 并重新启动了系统之后， 就可以在 [rc.conf(5)](http://www.FreeBSD.org/cgi/man.cgi?query=rc.conf&sektion=5&manpath=freebsd-release-ports) 中增加下列配置来启用审计服务了：

在编译、安装了开启审计功能的内核，并重新启动计算机之后， 就可以在 [rc.conf(5)](http://www.FreeBSD.org/cgi/man.cgi?query=rc.conf&sektion=5&manpath=freebsd-release-ports) 中增加下列配置来启用审计服务了：

```
auditd_enable="YES"
```

此后， 必须重新启动系统， 或通过下面的命令手工启动审计服务来启动审计支持：

```
/etc/rc.d/auditd start
```

## 18.4. 对审计进行配置

所有用于安全审计的配置文件， 都可以在 `/etc/security` 找到。 要启动审计服务， 下面这些文件必须存在：

*   `audit_class` - 包含对于审计类的定义。

*   `audit_control` - 控制审计子系统的特性， 例如默认审计类、 在审计日志所在的卷上保留的最小空间、 审计日志的最大尺寸， 等等。

*   `audit_event` - 文字化的系统审计事件名称和描述， 以及每个事件属于哪个类别。

*   `audit_user` - 针对特定用户的审计需求， 这些配置在登录时会与全局的默认值合并。

*   `audit_warn` - 由 auditd 调用的一个可定制的 shell 脚本， 用于在意外情况， 如用于审计日志的空间过少， 或审计日志文件被翻转时， 生成警告信息。

### 警告:

在编辑和维护审计配置文件时一定要小心， 因为配置文件中的错误会导致记录事件不正确。

### 18.4.1. 事件筛选表达式

在审计配置文件中的许多地方会用到筛选表达式来确定哪些事件是需要审计的。 表达式中需要指定要匹配的事件类型， 并使用前缀指定是否应接受或忽略匹配的事件， 此外， 还可以指定一个可选项指明匹配成功或失败的操作。 选择表达式是按从左到右的顺序计算的， 而对于两个表达式的情形， 则是通过将后一个追加到前一个之后来实现的。

下面列出了在 `audit_class` 中的默认事件类型：

*   `all` - *all (全部)* - 表示匹配全部事件类。

*   `ad` - *administrative (管理)* - 所有在系统上所进行的管理性操作。

*   `ap` - *application (应用)* - 应用程序定义的动作。

*   `cl` - *file close (文件关闭)* - 审计对 `close` 系统调用的操作。

*   `ex` - *exec (执行)* - 审计程序的执行。 对于命令行参数和环境变量的审计是通过在 [audit_control(5)](http://www.FreeBSD.org/cgi/man.cgi?query=audit_control&sektion=5&manpath=freebsd-release-ports) 中 `policy` 的 `argv` 和 `envv` 参数来控制的。

*   `fa` - *file attribute access (造访文件属性)* - 审计访问对象属性， 例如 [stat(1)](http://www.FreeBSD.org/cgi/man.cgi?query=stat&sektion=1&manpath=freebsd-release-ports)、 [pathconf(2)](http://www.FreeBSD.org/cgi/man.cgi?query=pathconf&sektion=2&manpath=freebsd-release-ports) 以及类似事件。

*   `fc` - *file create (创建文件)* - 审计创建了文件的事件。

*   `fd` - *file delete (删除文件)* - 审计所发生的文件删除事件。

*   `fm` - *file attribute modify (修改文件属性)* - 审计文件属性发生变化的事件， 例如 [chown(8)](http://www.FreeBSD.org/cgi/man.cgi?query=chown&sektion=8&manpath=freebsd-release-ports)、 [chflags(1)](http://www.FreeBSD.org/cgi/man.cgi?query=chflags&sektion=1&manpath=freebsd-release-ports)、 [flock(2)](http://www.FreeBSD.org/cgi/man.cgi?query=flock&sektion=2&manpath=freebsd-release-ports)， 等等。

*   `fr` - *file read (读文件数据)* - 审计读取数据、 文件以读方式打开等事件。

*   `fw` - *file write (写文件数据)* - 审计写入数据、 文件以写方式打开等事件。

*   `io` - *ioctl* - 审计对 [ioctl(2)](http://www.FreeBSD.org/cgi/man.cgi?query=ioctl&sektion=2&manpath=freebsd-release-ports) 系统调用的使用。

*   `ip` - *ipc* - 审计各种形式的进程间通信 (IPC)， 包括 POSIX 管道和 System V IPC 操作。

*   `lo` - *login_logout* - 审计系统中发生的 [login(1)](http://www.FreeBSD.org/cgi/man.cgi?query=login&sektion=1&manpath=freebsd-release-ports) 和 [logout(1)](http://www.FreeBSD.org/cgi/man.cgi?query=logout&sektion=1&manpath=freebsd-release-ports) 事件。

*   `na` - *non attributable (无主)* - 审计无法归类的事件。

*   `no` - *invalid class (无效类)* - 表示不匹配任何事件。

*   `nt` - *network (网络)* - 与网络操作有关的事件， 例如 [connect(2)](http://www.FreeBSD.org/cgi/man.cgi?query=connect&sektion=2&manpath=freebsd-release-ports) 和 [accept(2)](http://www.FreeBSD.org/cgi/man.cgi?query=accept&sektion=2&manpath=freebsd-release-ports)。

*   `ot` - *other (其它)* - 审计各类杂项事件。

*   `pc` - *process (进程)* - 审计进程操作， 例如 [exec(3)](http://www.FreeBSD.org/cgi/man.cgi?query=exec&sektion=3&manpath=freebsd-release-ports) 和 [exit(3)](http://www.FreeBSD.org/cgi/man.cgi?query=exit&sektion=3&manpath=freebsd-release-ports)。

这些审计事件， 可以通过修改 `audit_class` 和 `audit_event` 这两个配置文件来进行定制。

这个列表中， 每个审计类均包含一个表示匹配成功/失败操作的前缀， 以及这一项是否是增加或删去对事件类或类型的匹配。

*   (none) 审计事件的成功和失败实例。

*   `+` 审计这一类的成功事件。

*   `-` 审计这一类的失败事件。

*   `^` 不审计本类中的成功或失败事件。

*   `^+` 不审计本类中的成功事件。

*   `^-` 不审计本类中的失败事件。

下面例子中的筛选字符串表示筛选成功和失败的登录/注销事件， 而对执行事件， 则只审计成功的：

```
lo,+ex
```

### 18.4.2. 配置文件

多数情况下， 在配置审计系统时， 管理员只需修改两个文件： `audit_control` 和 `audit_user`。 前者控制系统级的审计属性和策略， 而后者则用于针对具体的用户来微调。

#### 18.4.2.1. `audit_control` 文件

`audit_control` 文件指定了一系列用于审计子系统的默认设置。 通过查看这个文件， 我们可以看到下面的内容：

```
dir:/var/audit
flags:lo
minfree:20
naflags:lo
policy:cnt
filesz:0
```

这里的 `dir` 选项可以用来设置用于保存审计日志的一个或多个目录。 如果指定了多个目录， 则将在填满一个之后换用下一个。 一般而言， 审计通常都会配置为保存在一个专用的文件系统之下， 以避免审计系统与其它子系统在文件系统满的时候所产生的冲突。

`flags` 字段用于为有主事件配置系统级的预选条件。 在前面的例子中， 所有用户成功和失败的登录和注销都会被审计。

`minfree` 参数用于定义保存审计日志的文件系统上剩余空间的最小百分比。 当超过这一阈值时， 将产生一个警告。 前面的例子中， 最小剩余空间比例设置成了两成。

`naflags` 选项表示审计类审计无主事件， 例如作为登录进程和系统服务的那些进程的事件。

`policy` 选项用于指定一个以逗号分隔的策略标志表， 以控制一系列审计行为。 默认的 `cnt` 标志表示系统应在审计失败时继续运行 (强烈建议使用这个标志)。 另一个常用的标志是 `argv`， 它表示在审计命令执行操作时， 同时审计传给 [execve(2)](http://www.FreeBSD.org/cgi/man.cgi?query=execve&sektion=2&manpath=freebsd-release-ports) 系统调用的命令行参数。

`filesz` 选项指明了审计日志在自动停止记录和翻转之前允许的最大尺寸。 默认值 0 表示禁用自动日志翻转。 如果配置的值不是零， 但小于最小值 512k， 则这个配置会被忽略， 并在日志中记录这一消息。

#### 18.4.2.2. `audit_user` 文件

`audit_user` 文件允许管理员为特定用户指定进一步的审计需求。 每一行使用两个字段来配置用户的审计： 第一个是 `alwaysaudit` 字段， 它指明了一组对该用户总会进行审计的事件； 而第二个则是 `neveraudit` 字段， 它指明了一系列对该用户不审计的事件。

在下述 `audit_user` 示例文件中， 审计了 `root` 用户的 登录/注销 事件， 以及成功的命令执行事件， 此外， 还审计了 `www` 用户的文件创建和成功的命令执行事件。 如果与前面的示范 `audit_control` 文件配合使用， 则 `root` 的 `lo` 项就是多余的， 而对 `www` 用户而言， 其登录/注销事件也会被审计：

```
root:lo,+ex:no
www:fc,+ex:no
```

## 18.5. 管理审计子系统

### 18.5.1. 查看审计日志

审计记帐是以 BSM 二进制格式保存的， 因此必须使用工具来对其进行修改， 或将其转换为文本。 [praudit(1)](http://www.FreeBSD.org/cgi/man.cgi?query=praudit&sektion=1&manpath=freebsd-release-ports) 命令能够将记帐文件转换为简单的文本格式； 而 [auditreduce(1)](http://www.FreeBSD.org/cgi/man.cgi?query=auditreduce&sektion=1&manpath=freebsd-release-ports) 命令则可以为分析、 存档或打印目的来浓缩审计日志文件。 `auditreduce` 支持一系列筛选参数， 包括事件类型、 事件类、 用户、 事件的日期和时间， 以及文件路径或操作对象。

例如， `praudit` 工具会将指定的审计记帐转存为简单文本格式的审计日志：

```
# `praudit /var/audit/AUDITFILE`
```

此处 `AUDITFILE` 是要转存的审计日志文件。

审计记帐中包括一系列审计记录， 这些记录由一系列短语 (token) 组成， 而 `praudit` 能把它们顺序显示为一行。 每个短语都属于某个特定的类型， 例如 `header` 表示审计记录头， 而 `path` 则表示在一次名字查找中的文件路径。 下面是一个 `execve` 事件的例子：

```
header,133,10,execve(2),0,Mon Sep 25 15:58:03 2006, + 384 msec
exec arg,finger,doug
path,/usr/bin/finger
attribute,555,root,wheel,90,24918,104944
subject,robert,root,wheel,root,wheel,38439,38032,42086,128.232.9.100
return,success,0
trailer,133
```

这个审计记录表示一次成功的 `execve` 调用， 执行了 `finger doug`。 在参数短语中是由 shell 提交给内核的命令行。 `path` 短语包含了由内核查找得到的可执行文件路径。 `attribute` 短语中包含了对可执行文件的描述， 特别地， 它包括了文件的权限模式， 用以确定应用程序是否是 setuid 的。 `subject`(主体) 短语描述了主体进程， 并顺序记录了审计用户 ID、 生效用户 ID 和组 ID、 实际用户 ID 和组 ID、 进程 ID、 会话 ID、 端口 ID， 以及登录地址。 注意审计用户 ID 和实际用户 ID 是不同的： 用户 `robert` 在执行这个命令之前已经切换为 `root` 帐户， 但它会以最初进行身份验证的用户身份进行审计。 最后， `return` 短语表示执行成功， 而 `trailer` 表示终结这一记录。

`praudit` 可以选择使用 `-x` 参数来支持 XML 格式的输出。

### 18.5.2. 浓缩审计记帐

由于审计日志可能会很大， 管理员可能会希望选择记录的一个子集来使用， 例如与特定用户相关的记录：

```
# `auditreduce -u trhodes /var/audit/AUDITFILE | praudit`
```

这将选择保存在 `AUDITFILE` 中的所有由 `trhodes` 产生的审计日志。

### 18.5.3. 委派审计复审权限

在 `audit` 组中的用户， 拥有读取 `/var/audit` 下的审计记帐的权限； 默认情况下， 这个组是空的， 因此只有 `root` 用户可以读取审计记帐。 如果希望给某个用户指定审计复审权， 则可以将其加入 `audit`。 由于查看审计日志的内容可以提供关于用户和进程行为的大量深度信息， 在您委派这些权力时， 请务必谨慎行事。

### 18.5.4. 通过审计管道来实时监控

审计管道是位于设备文件系统中的自动复制 (cloning) 的虚拟设备， 用于让应用程序控制正在运行的审计记录流， 这主要是为了满足入侵检测和系统监控软件作者的需要。 不过， 对管理员而言， 审计管道设备也提供了一种无需冒审计记帐文件属主出现问题的麻烦， 或由于日志翻转而打断事件流的麻烦， 而实现实时监控的方便途径。 要跟踪实时事件流， 使用下面的命令行：

```
# `praudit /dev/auditpipe`
```

默认情况下， 审计管道设备节点只有 `root` 用户才能访问。 如果希望 `audit` 组的成员能够访问它， 应在 `devfs.rules` 中加入下述 `devfs` 规则：

```
add path 'auditpipe*' mode 0440 group audit
```

请参见 [devfs.rules(5)](http://www.FreeBSD.org/cgi/man.cgi?query=devfs.rules&sektion=5&manpath=freebsd-release-ports) 以了解关于配置 devfs 文件系统的进一步信息。

### 警告:

很容易配置出审计事件反馈循环， 也就是查看事件的操作本身会产生更多的事件。 例如， 如果所有的网络 I/O 均被审计， 又在 SSH 会话中执行 [praudit(1)](http://www.FreeBSD.org/cgi/man.cgi?query=praudit&sektion=1&manpath=freebsd-release-ports)， 就会以很高的速率产生持续的审计事件流， 因为每显示一个事件都会产生新的事件。 建议您在需要在审计管道设备上执行 `praudit` 时， 选择一个没有进行细粒度 I/O 审计的会话来运行。

### 18.5.5. 审计记帐文件的轮转

审计计账只由内核写入， 且只能由 auditd 管理。 管理员不应尝试使用 [newsyslog.conf(5)](http://www.FreeBSD.org/cgi/man.cgi?query=newsyslog.conf&sektion=5&manpath=freebsd-release-ports) 或其它工具来完成审计日志的轮转工作。 您可以使用 `audit` 管理工具来关闭审计、 重新配置审计系统， 并完成日志轮转。 下面的命令将让审计服务创建新的审计日志， 并发信号给内核要求其使用新的日志。 旧日志将终止并被改名， 此时， 管理员就可以操作它了。

```
# `audit -n`
```

### 警告:

如果 auditd 服务程序没有在运行， 则这个命令将失败并给出错误提示。

在 `/etc/crontab` 加入如下设置， 将使 [cron(8)](http://www.FreeBSD.org/cgi/man.cgi?query=cron&sektion=8&manpath=freebsd-release-ports) 每十二小时将日志轮转一次。

```
0     */12       *       *       *       root    /usr/sbin/audit -n
```

这些修改会在您保存 `/etc/crontab` 后生效。

对于审计记帐文件基于尺寸的自动翻转， 可以通过 [audit_control(5)](http://www.FreeBSD.org/cgi/man.cgi?query=audit_control&sektion=5&manpath=freebsd-release-ports) 中的 `filesz` 选项来配置， 这个选项在这一章的配置文件一节中已经介绍过。

### 18.5.6. 压缩审计记帐

由于审计记帐文件会变得很大， 通常会希望在审计服务关闭它时， 对其进行压缩或归档。 `audit_warn` 脚本可以用来在一系列与审计有关的事件发生时， 执行一些用户定义的操作， 这也包括在审计记帐翻转时进行清理操作。 举例而言， 可以在 `audit_warn` 脚本中加入下列内容来在审计记帐关闭时压缩它：

```
#
# Compress audit trail files on close.
#
if [ "$1" = closefile ]; then
        gzip -9 $2
fi
```

其它存档操作也包括将审计记帐复制到一个中央的服务器， 删除旧的记帐文件， 或浓缩审计记帐并删除不需要的记录等。 这个脚本会在审计记帐文件正常关闭时执行一次， 因此在非正常关闭系统时， 就不会执行它了。

## 第 19 章 存储

## 19.1. 概述

这章介绍了 FreeBSD 中磁盘的使用方法。包括内存盘， 网络附属磁盘和标准的 SCSI/IDE 存储设备，以及使用 USB 的设备。

读完这章，您将了解到：

*   FreeBSD 中用来描述硬盘上数据组织的术语 (partitions and slices)。

*   如何在您的系统上增加硬盘。

*   如何配置 FreeBSD 来使用 USB 存储设备。

*   如何设置虚拟文件系统，例如内存磁盘。

*   如何使用配额来限制磁盘空间的使用。

*   如何增加磁盘安全来预防功击。

*   如何刻录 CD 和 DVD 。

*   用于备份的多种存储媒介。

*   如何在 FreeBSD 上使用备份程序。

*   如何备份到软磁盘。

*   文件系统快照是什么， 以及如何有效地使用它们。

在读这章之前，您应该：

*   知道怎样去配置和安装新的 FreeBSD 内核 (第 9 章 *配置 FreeBSD 的内核*).

## 19.2. 设备命名

下面是在 FreeBSD 上被支持的物理存储设备和它们被分配的设备名。

表 19.1. 物理磁盘命名规则

| 驱动器类型 | 驱动设备命名 |
| --- | --- |
| IDE 硬盘驱动器 | `ad` |
| IDE CDROM 驱动器 | `acd` |
| SCSI 硬盘以及 USB 大容量存储设备 | `da` |
| SCSI CDROM 驱动器 | `cd` |
| 各类非标准 CDROM 驱动器 | 用于 Mitsumi CD-ROM 的 `mcd` 以及用于 Sony CD-ROM 驱动器的 `scd` |
| Floppy drives | `fd` |
| SCSI tape drives | `sa` |
| IDE tape drives | `ast` |
| Flash drives | `fla` for DiskOnChip® Flash device |
| RAID drives | `aacd` for Adaptec® AdvancedRAID, `mlxd` and `mlyd` for Mylex®, `amrd` for AMI MegaRAID®, `idad` for Compaq Smart RAID, `twed` for 3ware® RAID. |

## 19.3. 添加磁盘

Originally contributed by David O'Brien.

下面这节将会介绍如何在一台只有一块磁盘的机器上新增一块 SCSI 磁盘。 首先 需要关掉计算机，然后按操作规程来安装驱动器，控制器和驱动程序。由于 各厂家生产的产品各不相同，具体的安装细节不在此文档介绍之内。

以 `root` 用户登录。安装完驱动后，检查一下 `/var/run/dmesg.boot` 有没有找到新的磁盘。在我们 的例子中新增加的磁盘就是 `da1`，我们从 `/1` 挂上它。 (如果您正添加 IDE 驱动器， 则设备名应该是 `ad1`)。

因为 FreeBSD 运行在 IBM-PC 兼容机上，它必须遵循 PC BIOS 分区规范。 这与传统的 BSD 分区是不同的。一个 PC 的磁盘最高只能有四个 BIOS 主分区。如果磁盘只安装 FreeBSD 您可以使用 *dedicated* 模式。另外， FreeBSD 必须安装在 PC BIOS 支持的分区内。FreeBSD 把分区叫作 *slices* 这可能会把人搞糊涂。您也可以在只安装 FreeBSD 的磁盘上使用 slices，也可以在安装有其它操作系统的磁盘上使用 slices。这不会影响其它操作系统的 `fdisk` 分区工具。

在 slice 方式表示下，驱动器被添加到 `/dev/da1s1e`。 可以读作：SCSI 磁盘，编号为 1 (第二个 SCSI 磁盘)， slice 1 (PC BIOS 分区 1)， 的 BSD 分区 `e` 。在有些例子中，也可以简化为 `/dev/da1e`。

由于 [bsdlabel(8)](http://www.FreeBSD.org/cgi/man.cgi?query=bsdlabel&sektion=8&manpath=freebsd-release-ports) 使用 32-位 的整数来表示扇区号， 因此在多数情况下它的表现力限于每个磁盘 2³²-1 个扇区， 或 2TB。 [fdisk(8)](http://www.FreeBSD.org/cgi/man.cgi?query=fdisk&sektion=8&manpath=freebsd-release-ports) 格式允许的起始扇区号不能高于 2³²-1， 而分区尺寸也不能超过 2³²-1， 这样一来通常情况下分区尺寸不能超过 2TB， 而磁盘尺寸则不能超过 4TB。 [sunlabel(8)](http://www.FreeBSD.org/cgi/man.cgi?query=sunlabel&sektion=8&manpath=freebsd-release-ports) 格式的限制是每个分区 2³²-1 个扇区， 但可以有 8 个分区， 因而可以支持最大 16TB 的磁盘。 对于更大的磁盘， 可以使用 [gpart(8)](http://www.FreeBSD.org/cgi/man.cgi?query=gpart&sektion=8&manpath=freebsd-release-ports) 来创建 GPT 分区。 GPT 除了支持大磁盘之外， 还不受 4 个 slice 的限制。

### 19.3.1. 使用 [sysinstall(8)](http://www.FreeBSD.org/cgi/man.cgi?query=sysinstall&sektion=8&manpath=freebsd-release-ports)

1.  **使用 Sysinstall**

    您可以使用 `sysinstall` 命令的菜单来分区和标记一个新的磁盘。 这一操作需要有 root 权限， 您可以直接使用 `root` 账户登录或者使用 `su` 命令来切换到 root 用户。运行 `sysinstall` ，然后选择 `Configure` 菜单。在 `FreeBSD Configuration Menu` 下，上下滚动， 选择 `Fdisk` 条目。

2.  **fdisk 分区编辑器**

    进入 fdisk 分区编辑器后，选择 **`A`** ，FreeBSD 将使用全部的磁盘。当被告知 “remain cooperative with any future possible operating systems”时，回答 `YES`。使用 **W** 保存刚才的修改。现在使用 **Q** 退出 FDISK 编辑器。下面会看到有关 “主引导区” 的信息。 现在您已经在运行的系统上添加了一个磁盘， 因此应该选择 `None`。

3.  **Disk Label 编辑器**

    接下来，您应该退出 sysinstall 并且再次启动它，并按照上面的步骤直接进入 `Label` 选项。进入 `磁盘标签编辑器`。 这就是您要创建的 BSD 分区。一个磁盘最多可以有 8 个分区，标记为 `a-h`。有几个分区标签有特殊的用途。 `a` 分区被用来作为根分区(`/`)。 系统磁盘（例如：从那儿启动的分区）必须有一个 `a` 分区。`b` 分区被用作交换分区，可以用很多磁盘用作交 换分区。 `c` 分区代表整个硬盘，或在 FreeBSD slice 模式下代表整个 slice。其它分区作为一般分区来使用。

    sysinstall 的标签编辑器用 `e` 表示非 root 和非 swap 分区。在标签编辑器中，可以使用键入 **C** 创建一个文件系统。当提示这是否是一个 FS（文件系统）或 swap 时，选择 `FS`，然后给出一个加载点（如： `/mnt`）。 当在 post-install 模式时添加一个磁盘， sysinstall 不会在 `/etc/fstab` 中创建记录，所以是否指定加载点并不重要。

    现在已经准备把新标签写到磁盘上，然后创建一个文件系统，可以按下 **W**。出现任何错误都会不能创建新的分区。可以退出标签编辑 器然后重新执行 sysinstall 。

4.  **完成**

    下面一步就是编辑 `/etc/fstab`，为您的磁盘添加一个新 记录。

### 19.3.2. 使用命令行工具

#### 19.3.2.1. 使用 Slices

这步安装将允许磁盘与可能安装在您计算机上的其它操作系统一起 正确工作，而不会搞乱其它操作系统的分区。推荐使用这种方法来安装 新磁盘，除非您有更好的理由再使用 `dedicated` 模式！

```
# `dd if=/dev/zero of=/dev/da1 bs=1k count=1`
# `fdisk -BI da1` #初始化新磁盘
# `bsdlabel -B -w da1s1 auto` #加上标签
# `bsdlabel -e da1s1` # 现在编辑您刚才创建的磁盘分区
# `mkdir -p /1`
# `newfs /dev/da1s1e` # 为您创建的每个分区重复这个操作
# `mount /dev/da1s1e /1` # 挂上分区
# `vi /etc/fstab` # 完成之后，添加合适的记录到您的 /etc/fstab 文件。
```

如果有一个 IDE 磁盘，记得要用 `ad` 替换前面的 `da`。

#### 19.3.2.2. 专用模式

如果您并没有安装其它的操作系统，可以使用 `dedicated` 模式。记住这种模式可能会弄乱 Microsoft 的操作系统，但不会对它进行破坏。 它不识别找到的 IBM OS/2® 的 “appropriate” 分区。

```
# `dd if=/dev/zero of=/dev/da1 bs=1k count=1`
# `bsdlabel -Bw da1 auto`
# `bsdlabel -e da1`				# 创建 `e' 分区
# `newfs /dev/da1e`
# `mkdir -p /1`
# `vi /etc/fstab`				# 为 /dev/da1e 添加一个记录
# `mount /1`
```

另一种方法：

```
# `dd if=/dev/zero of=/dev/da1 count=2`
# `bsdlabel /dev/da1 | bsdlabel -BR da1 /dev/stdin`
# `newfs /dev/da1e`
# `mkdir -p /1`
# `vi /etc/fstab`					# 为 /dev/da1e 添加一个记录
# `mount /1`
```

## 19.4. RAID

### 19.4.1. 软件 RAID

#### 19.4.1.1. 连接磁盘驱动器配置 (CCD)

Original work by Christopher Shumway.Revised by Jim Brown.

选择一个大容量存储比较好的解决方案，最重要的因素是产品的速度、 性能和成本。 通常这三者不可能都满足;要获得比较快和可靠的大容量存储设备， 就比较昂贵。但如果将成本降下来，那它的速度或可靠性就会打折扣。

在设计下面描述的系统时， 价格被选为最重要的因素， 接下来是速度和性能。 这个系统的数据传输速度基本上受限于网络。 性能也非常重要， CCD 驱动器上的所有数据都被备份到了 CD-R 盘， 可以很容易地对数据进行恢复。

在选择一个大容量的存储解决方案时，第一步是要设计您自己的需求。 如果您的需求更偏重于速度和性能，那么您的解决方案将就不同于上面的设计。

##### 19.4.1.1.1. 安装硬件

除了 IDE 系统磁盘外，还有三个 Western Digital 30GB、5400 RPM 的 IDE 磁盘构成了大约 90G 的连接磁盘驱动存储空间。 理想情况是每个 IDE 硬盘都独占 IDE 控制器和数据线， 但为了尽可能降低成本， 通常并不会安装更多的控制器， 而是通过配置跳线，使每个 IDE 控制器都管理一个主盘和一个从盘。

重启动后，系统 BIOS 被配置成自动检测硬盘。FreeBSD 检测到它们：

```
ad0: 19574MB <WDC WD205BA> [39770/16/63] at ata0-master UDMA33
ad1: 29333MB <WDC WD307AA> [59598/16/63] at ata0-slave UDMA33
ad2: 29333MB <WDC WD307AA> [59598/16/63] at ata1-master UDMA33
ad3: 29333MB <WDC WD307AA> [59598/16/63] at ata1-slave UDMA33
```

### 注意:

如果 FreeBSD 没有检测到它们，请确定它们的跳线是否设置 正确。大多数 IDE 磁盘有一个 “Cable Select” 跳线。这个 *不是* 设置 master/slave 硬盘的跳线。查阅文档 信息来确定正确的跳线设置。

接下来考虑的是，如何创建文件系统。应该好好研究一下 [vinum(4)](http://www.FreeBSD.org/cgi/man.cgi?query=vinum&sektion=4&manpath=freebsd-release-ports) (第 22 章 *Vinum 卷管理程序*)和 [ccd(4)](http://www.FreeBSD.org/cgi/man.cgi?query=ccd&sektion=4&manpath=freebsd-release-ports) 两种方式，在这里我们选择 [ccd(4)](http://www.FreeBSD.org/cgi/man.cgi?query=ccd&sektion=4&manpath=freebsd-release-ports)

##### 19.4.1.1.2. 安装 CCD

[ccd(4)](http://www.FreeBSD.org/cgi/man.cgi?query=ccd&sektion=4&manpath=freebsd-release-ports) 允许用户将几个相同的的磁盘通过一个逻辑文件系统 连接起来。要使用 [ccd(4)](http://www.FreeBSD.org/cgi/man.cgi?query=ccd&sektion=4&manpath=freebsd-release-ports)，您需要在内核中配置 [ccd(4)](http://www.FreeBSD.org/cgi/man.cgi?query=ccd&sektion=4&manpath=freebsd-release-ports) 支持选项。把这行加入到内核配置文件中，然后重建内核：

```
device   ccd
```

对 [ccd(4)](http://www.FreeBSD.org/cgi/man.cgi?query=ccd&sektion=4&manpath=freebsd-release-ports) 的支持也可以内核模块的形式载入。

要安装 [ccd(4)](http://www.FreeBSD.org/cgi/man.cgi?query=ccd&sektion=4&manpath=freebsd-release-ports), 首先需要使用 [bsdlabel(8)](http://www.FreeBSD.org/cgi/man.cgi?query=bsdlabel&sektion=8&manpath=freebsd-release-ports) 来编辑硬盘：

```
bsdlabel -w ad1 auto
bsdlabel -w ad2 auto
bsdlabel -w ad3 auto
```

此处将整个硬盘创建为 `ad1c`, `ad2c` 和 `ad3c`。

下一步是改变 disklable 的类型。也可以使用 [bsdlabel(8)](http://www.FreeBSD.org/cgi/man.cgi?query=bsdlabel&sektion=8&manpath=freebsd-release-ports) 来编辑：

```
bsdlabel -e ad1
bsdlabel -e ad2
bsdlabel -e ad3
```

这儿在每个已经设置了 `EDITOR` 环境变量的磁盘上打开了 disklable，在我我例子中使用的是 [vi(1)](http://www.FreeBSD.org/cgi/man.cgi?query=vi&sektion=1&manpath=freebsd-release-ports)。

可以看到：

```
8 partitions:
#        size   offset    fstype   [fsize bsize bps/cpg]
  c: 60074784        0    unused        0     0     0   # (Cyl.    0 - 59597)
```

添加一个新的 `e` 分区给 [ccd(4)](http://www.FreeBSD.org/cgi/man.cgi?query=ccd&sektion=4&manpath=freebsd-release-ports) 用。这可以是 `c` 分区的一个副本， 但 `fstype` *必须* 是 **`4.2BSD`**。做完之后，您会看到一面这些：

```
8 partitions:
#        size   offset    fstype   [fsize bsize bps/cpg]
  c: 60074784        0    unused        0     0     0   # (Cyl.    0 - 59597)
  e: 60074784        0    4.2BSD        0     0     0   # (Cyl.    0 - 59597)
```

##### 19.4.1.1.3. 建立文件系统

现在已给每个磁盘都加上了标签，下面需要建立 [ccd(4)](http://www.FreeBSD.org/cgi/man.cgi?query=ccd&sektion=4&manpath=freebsd-release-ports)。要这样做， 需要使用 [ccdconfig(8)](http://www.FreeBSD.org/cgi/man.cgi?query=ccdconfig&sektion=8&manpath=freebsd-release-ports) 工具，同时要提供类似下面的选项：

```
ccdconfig ccd0![1](img/1.png) 32![2](img/2.png) 0![3](img/3.png) /dev/ad1e![4](img/4.png) /dev/ad2e /dev/ad3e
```

每个选项的意义和用法如下所示：

| ![1](img/#co-ccd-dev) | 配置设备的第一个参数，在这是 `/dev/ccd0c`。 `/dev/` 部分是任选项。 |
| ![2](img/#co-ccd-interleave) | 下一个参数是文件系统的插入页(interleave)。插入页定义了一个 磁盘块中一个分段或条带(stripe)的大小，通常是 512 个字节。所以一个为 32 的插入页将是 16,384 字节。 |
| ![3](img/#co-ccd-flags) | 插入页为 [ccdconfig(8)](http://www.FreeBSD.org/cgi/man.cgi?query=ccdconfig&sektion=8&manpath=freebsd-release-ports) 附带了标记。如果您要启用驱动器镜像， 需要在这儿指定它。在这个配置中没有做 [ccd(4)](http://www.FreeBSD.org/cgi/man.cgi?query=ccd&sektion=4&manpath=freebsd-release-ports) 的镜像，所以把它 设为 0 (zero)。 |
| ![4](img/#co-ccd-devs) | [ccdconfig(8)](http://www.FreeBSD.org/cgi/man.cgi?query=ccdconfig&sektion=8&manpath=freebsd-release-ports) 的最后配置是设备的排列问题。使用完整的设备 路径名。 |

运行 [ccdconfig(8)](http://www.FreeBSD.org/cgi/man.cgi?query=ccdconfig&sektion=8&manpath=freebsd-release-ports) 后 [ccd(4)](http://www.FreeBSD.org/cgi/man.cgi?query=ccd&sektion=4&manpath=freebsd-release-ports) 就配置好了。现在要创建文件 系统了，参考 [newfs(8)](http://www.FreeBSD.org/cgi/man.cgi?query=newfs&sektion=8&manpath=freebsd-release-ports) 选项，执行下同的命令：

```
newfs /dev/ccd0c
```

##### 19.4.1.1.4. 自动创建

最后，要挂上 [ccd(4)](http://www.FreeBSD.org/cgi/man.cgi?query=ccd&sektion=4&manpath=freebsd-release-ports) ，需要先配置它。把当前的配置文件写入 `/etc/ccd.conf` 中，使用下面的命令：

```
ccdconfig -g > /etc/ccd.conf
```

当重新启动系统时，如果 `/etc/ccd.conf` 存在， 脚本 `/etc/rc` 就运行 `ccdconfig -C`。 这样就能自动配置 [ccd(4)](http://www.FreeBSD.org/cgi/man.cgi?query=ccd&sektion=4&manpath=freebsd-release-ports) 以到它能被挂上。

### 注意:

如果启动进入了单用户模式，在 [mount(8)](http://www.FreeBSD.org/cgi/man.cgi?query=mount&sektion=8&manpath=freebsd-release-ports) 上 [ccd(4)](http://www.FreeBSD.org/cgi/man.cgi?query=ccd&sektion=4&manpath=freebsd-release-ports) 之前，需要执行下面的命令来配置队列：

```
ccdconfig -C
```

要自动挂接 [ccd(4)](http://www.FreeBSD.org/cgi/man.cgi?query=ccd&sektion=4&manpath=freebsd-release-ports),需要为 [ccd(4)](http://www.FreeBSD.org/cgi/man.cgi?query=ccd&sektion=4&manpath=freebsd-release-ports) 在 `/etc/fstab` 中配置一个记录，以便在启动时它能被挂上。 如下所示：

```
/dev/ccd0c              /media       ufs     rw      2       2
```

#### 19.4.1.2.  Vinum 卷管理

Vinum 卷管理是一个实现虚拟磁盘的块驱动设备工具。它使磁盘从 块设备的接口和数据映射中独立出来。与传统的存储设备相比，增加了 灵活性、性能和可靠性。 [vinum(4)](http://www.FreeBSD.org/cgi/man.cgi?query=vinum&sektion=4&manpath=freebsd-release-ports) 实现了 RAID-0、RAID-1 和 RAID-5 三种模式，它们既可以独立使用，也可组合使用。

参考 第 22 章 *Vinum 卷管理程序* 得到更多 [vinum(4)](http://www.FreeBSD.org/cgi/man.cgi?query=vinum&sektion=4&manpath=freebsd-release-ports) 的信息。

### 19.4.2. 硬件 RAID

FreeBSD 支持很多硬件 RAID 控制器。 这些硬件不需要 FreeBSD 指定软件来管理 RAID 系统。

使用 BIOS 支持的硬件，一般情况下这些硬件可以自行操作。 下面是一个简明的描述设置一个 Promise IDE RAID 控制器。 当硬件设备装好且系统重启后，屏幕上显示一个询问信息。接着进入硬件设置屏幕。在这里， 您可以把所有的磁盘联合在一起使用。这样 FreeBSD 将磁盘看作一个驱动器。其它 级别的 RAID 也可以相应的进行设置。

### 19.4.3. 重建 ATA RAID1 阵列

FreeBSD 允许您热插拔阵列中损坏的磁盘。 在您重新启动系统之前请注意这一点。

您可能会在 `/var/log/messages` 或者在 [dmesg(8)](http://www.FreeBSD.org/cgi/man.cgi?query=dmesg&sektion=8&manpath=freebsd-release-ports) 的输出中看到类似下面这些的内容：

```
ad6 on monster1 suffered a hard error.
ad6: READ command timeout tag=0 serv=0 - resetting
ad6: trying fallback to PIO mode
ata3: resetting devices .. done
ad6: hard error reading fsbn 1116119 of 0-7 (ad6 bn 1116119; cn 1107 tn 4 sn 11)\\
status=59 error=40
ar0: WARNING - mirror lost
```

使用 [atacontrol(8)](http://www.FreeBSD.org/cgi/man.cgi?query=atacontrol&sektion=8&manpath=freebsd-release-ports)，查看更多的信息：

```
# `atacontrol list`
ATA channel 0:
	Master:      no device present
	Slave:   acd0 <HL-DT-ST CD-ROM GCR-8520B/1.00> ATA/ATAPI rev 0

ATA channel 1:
	Master:      no device present
	Slave:       no device present

ATA channel 2:
	Master:  ad4 <MAXTOR 6L080J4/A93.0500> ATA/ATAPI rev 5
	Slave:       no device present

ATA channel 3:
	Master:  ad6 <MAXTOR 6L080J4/A93.0500> ATA/ATAPI rev 5
	Slave:       no device present

# `atacontrol status ar0`
ar0: ATA RAID1 subdisks: ad4 ad6 status: DEGRADED
```

1.  首先您应将包含故障盘的 ata 通道卸下， 以便安全地将其拆除：

    ```
    # `atacontrol detach ata3`
    ```

2.  换上磁盘

3.  重新挂接 ata 通道：

    ```
    # `atacontrol attach ata3`
    Master:  ad6 <MAXTOR 6L080J4/A93.0500> ATA/ATAPI rev 5
    Slave:   no device present
    ```

4.  将新盘作为热备盘加入阵列：

    ```
    # `atacontrol addspare ar0 ad6`
    ```

5.  重建阵列：

    ```
    # `atacontrol rebuild ar0`
    ```

6.  可以通过下面的命令来查看进度：

    ```
    # `dmesg | tail -10`
    [output removed]
    ad6: removed from configuration
    ad6: deleted from ar0 disk1
    ad6: inserted into ar0 disk1 as spare

    # `atacontrol status ar0`
    ar0: ATA RAID1 subdisks: ad4 ad6 status: REBUILDING 0% completed
    ```

7.  等待操作完成。

## 19.5. USB 存储设备

Contributed by Marc Fonvieille.

到目前为止，有许多外部外部存储解决方案， 例如：通用串行总线 (USB)：硬盘、USB thumbdrives、CD-R burners 等等。 FreeBSD 为这些设备提供了支持。

### 19.5.1. 配置

USB 大容量存储设备驱动，在 [umass(4)](http://www.FreeBSD.org/cgi/man.cgi?query=umass&sektion=4&manpath=freebsd-release-ports), 中提供了对 USB 存储设备的支持。如果您使用 `GENERIC` 内核，您不必要改变配置文件里的任何内容。 如果您使用了定制的内核，就要确定下面的行出现在您的内核配置文件里：

```
device scbus
device da
device pass
device uhci
device ohci
device ehci
device usb
device umass
```

[umass(4)](http://www.FreeBSD.org/cgi/man.cgi?query=umass&sektion=4&manpath=freebsd-release-ports) 驱动程序使用 SCSI 子系统来访问 USB 存储设备， 您的 USB 设备将被系统看成为一个 SCSI 设备。依靠您主板上的 USB 芯片， 您只须选择 `device uhci` 或用于 USB 1.X 支持的 `device ohci` 二者之一即可， 但是两者都加入内核配置文件当中也是无害的。 对于 USB 2.X 控制器的支持由 [ehci(4)](http://www.FreeBSD.org/cgi/man.cgi?query=ehci&sektion=4&manpath=freebsd-release-ports) 提供 (`device ehci` 这一行)。 不要忘了如果您加入了上面的几行要重新编译和安装内核。

### 注意:

如果您的 USB 设备是一个 CD-R 或 DVD 刻录机， SCSI CD-ROM 驱动程序， [cd(4)](http://www.FreeBSD.org/cgi/man.cgi?query=cd&sektion=4&manpath=freebsd-release-ports), 就必须加入内核中通过下面这行：

```
device cd
```

由于刻录机被视为 SCSI 设备， 因此， 不应该在内核配置文件中使用 [atapicam(4)](http://www.FreeBSD.org/cgi/man.cgi?query=atapicam&sektion=4&manpath=freebsd-release-ports) 驱动程序。

### 19.5.2. 测试配置

配置好后准备进行测试：插入您的 USB 设备， 在系统信息中 ([dmesg(8)](http://www.FreeBSD.org/cgi/man.cgi?query=dmesg&sektion=8&manpath=freebsd-release-ports)), 应该会出现像下面的设备：

```
umass0: USB Solid state disk, rev 1.10/1.00, addr 2
GEOM: create disk da0 dp=0xc2d74850
da0 at umass-sim0 bus 0 target 0 lun 0
da0: <Generic Traveling Disk 1.11> Removable Direct Access SCSI-2 device
da0: 1.000MB/s transfers
da0: 126MB (258048 512 byte sectors: 64H 32S/T 126C)
```

当然啦，商标，设备标识 (`da0`) 和其它的细节信息会根据您的配置不同 而有所不同。

因为 USB 设备被看作 SCSI 设备中的一个， `camcontrol` 命令也能够用来列出 USB 存储设备和系统的关联：

```
# `camcontrol devlist`
<Generic Traveling Disk 1.11>      at scbus0 target 0 lun 0 (da0,pass0)
```

如果设备上已经包含了文件系统， 现在应该就可以挂接它了。 如果需要， 请参阅 第 19.3 节 “添加磁盘” 来了解如何在 USB 驱动器上格式化和创建分区。

### 警告:

允许非可信用户挂载任意介质， 例如通过使用前面介绍的 `vfs.usermount` 来启用的功能， 从安全角度来看是很不保险的。 FreeBSD 中的绝大多数文件系统并不提供针对恶意设备的内建防护能力。

如果希望设备能够被普通用户挂接， 还需要做一些其它操作。 首先， 在 USB 存储设备连接到计算机上时， 系统自动生成的设备文件， 必须是该用户能够读写的。 一种做法是让所有属于 `operator` 组的用户都可以访问该设备。 要完成这项工作， 首先需要用 [pw(8)](http://www.FreeBSD.org/cgi/man.cgi?query=pw&sektion=8&manpath=freebsd-release-ports) 来给用户指定组。 其次， 在生成设备文件时， `operator` 组应能读写它们。 这可以通过在 `/etc/devfs.rules` 中增加一些相应的设置来实现：

```
[localrules=5]
add path 'da*' mode 0660 group operator
```

### 注意:

如果系统中已经有其它 SCSI 磁盘， 则上述操作必须做一些变化。 例如， 如果系统中已经存在了设备名为 `da0` 到 `da2` 的磁盘， 则第二行应改为：

```
add path 'da[3-9]*' mode 0660 group operator
```

这会将系统中已经存在的磁盘， 排除在属于 `operator` 组的设备之外。

另外， 您还需要在 `/etc/rc.conf` 文件中， 启用 [devfs.rules(5)](http://www.FreeBSD.org/cgi/man.cgi?query=devfs.rules&sektion=5&manpath=freebsd-release-ports) 规则集：

```
devfs_system_ruleset="localrules"
```

接下来， 需要配置内核， 令普通用户能够挂接文件系统。 最简单的方法是将下面的配置加入到 `/etc/sysctl.conf`：

```
vfs.usermount=1
```

注意， 这个设置只有在下次重启系统时才会生效。 另外， 您也可以使用 [sysctl(8)](http://www.FreeBSD.org/cgi/man.cgi?query=sysctl&sektion=8&manpath=freebsd-release-ports) 来设置这个变量。

最后一步是创建将要挂接文件系统的目录。 这个目录必须是属于将要挂接文件系统的用户的。 以 `root` 身份为用户建立属于该用户的 `/mnt/username` (此处 *`username`* 应替换成用户的登录名， 并把 *`usergroup`* 替换成用户所属的组)：

```
# `mkdir /mnt/username`
# `chown username:usergroup /mnt/username`
```

假设已经插入了一个 USB 读卡设备， 并且系统将其识别为 `/dev/da0s1`， 由于这些设备通常是 FAT 文件系统， 用户可以这样挂接它们：

```
% `mount -t msdosfs -o -m=644,-M=755 /dev/da0s1 /mnt/username`
```

如果拔出设备 (必须首先将其对应的磁盘卷卸下)， 则您会在系统消息缓冲区中看到类似下面的信息：

```
umass0: at uhub0 port 1 (addr 2) disconnected
(da0:umass-sim0:0:0:0): lost device
(da0:umass-sim0:0:0:0): removing device entry
GEOM: destroy disk da0 dp=0xc2d74850
umass0: detached
```

### 19.5.3. 深入阅读

除了 Adding Disks 和 Mounting and Unmounting File Systems 章之外， 阅读各种手册页也是有益的： [umass(4)](http://www.FreeBSD.org/cgi/man.cgi?query=umass&sektion=4&manpath=freebsd-release-ports), [camcontrol(8)](http://www.FreeBSD.org/cgi/man.cgi?query=camcontrol&sektion=8&manpath=freebsd-release-ports), 和 FreeBSD  8.X 的 [usbconfig(8)](http://www.FreeBSD.org/cgi/man.cgi?query=usbconfig&sektion=8&manpath=freebsd-release-ports) 或者对于更早期 FreeBSD 版本的 [usbdevs(8)](http://www.FreeBSD.org/cgi/man.cgi?query=usbdevs&sektion=8&manpath=freebsd-release-ports)。

## 19.6. 创建和使用光学介质(CD)

Contributed by Mike Meyer.

### 19.6.1. 介绍

CD 与普通的磁盘相比有很多不同的特性。最初它们是不能被用户写入的。 由于没有磁头和磁道移动时的延迟，所以它们可以连续的进行读取。 方便的在两个系统之间进行数据的传输，比起相同大小的存储介质来说。

CD 有磁道，这关系到数据读取时的连续性而不是物理磁盘的性能。 要在 FreeBSD 中制作一个 CD，您要准备好要写到 CD 上的数据文件， 然后根据每个 tracks 写入到 CD。

ISO 9660 文件系统被设计用来处理这些差异。 但令人遗憾的是， 它也有一些其他文件系统所没有的限制， 不过幸运的是， 它提供了一项扩展机制， 使得正确写入的 CD 能够超越这些限制， 而又能在不支持这些扩展的系统上正常使用。

[sysutils/](http://www.freebsd.org/cgi/url.cgi?ports/sysutils//pkg-descr) port 包括了 [mkisofs(8)](http://www.FreeBSD.org/cgi/man.cgi?query=mkisofs&sektion=8&manpath=freebsd-release-ports)， 这是一个可以用来生成包含 ISO 9660 文件系统的数据文件的程序。 他也提供了对于一些扩展的支持选项，下面将详细介绍。

使用哪个工具来刻录 CD 取决于您的 CD 刻录机是 ATAPI 的， 还是其他类型的。 对于 ATAPI CD 刻录机， 可以使用基本系统附带的 `burncd` 程序。 SCSI 和 USB CD 刻录机， 则需要配合 `cdrecord` 程序使用， 它可以通过 [sysutils/cdrtools](http://www.freebsd.org/cgi/url.cgi?ports/sysutils/cdrtools/pkg-descr) port 安装。 除此之外， 在 ATAPI 接口的刻录机上， 也可以配合 ATAPI/CAM 模块 来使用 `cdrecord` 以及其它为 SCSI 刻录机撰写的工具。

如果您想使用带图形界面的 CD 刻录软件， 可以考虑一下 X-CD-Roast 或 K3b。 这些工具可以通过使用预编译安装包， 或通过 [sysutils/xcdroast](http://www.freebsd.org/cgi/url.cgi?ports/sysutils/xcdroast/pkg-descr) 和 [sysutils/k3b](http://www.freebsd.org/cgi/url.cgi?ports/sysutils/k3b/pkg-descr) ports 来安装。 X-CD-Roast 和 K3b 需要 ATAPI/CAM 模块 配合 ATAPI 硬件。

### 19.6.2. mkisofs

[mkisofs(8)](http://www.FreeBSD.org/cgi/man.cgi?query=mkisofs&sektion=8&manpath=freebsd-release-ports) 程序作为 [sysutils/cdrtools](http://www.freebsd.org/cgi/url.cgi?ports/sysutils/cdrtools/pkg-descr) port 的一部分， 将生成 ISO 9660 文件系统，其中包含 UNIX® 命名空间中的文件名。 最简单的用法是：

```
# `mkisofs -o imagefile.iso /path/to/tree`
```

这个命令将创建一个包含 ISO9660 文件系统的 *`imagefile.iso`* 文件，它是目录树 *`/path/to/tree`* 的一个副本。 在处理过程中， 它将文件名称映射为标准的 ISO9660 文件系统的文件名，将排除那些不典型的 ISO 文件系统的文件。

有很多选项能够用来克服那些限制。特别的，`-R` 选项能够启用 Rock Ridge 扩展一般的 UNIX® 系统，`-J` 选项能启用用于 Microsoft 系统的 Joliet 扩展，`-hfs` 选项能用来创建用于 Mac OS® 系统的 HFS 文件系统。

对于那些即将要在 FreeBSD 系统中使用 CD 的人来说，`-U` 选项能用来消除所有文件名的限制。当使用 `-R` 选项时，它会产生一个 文件系统映像，它与您从那儿启动 FreeBSD 树是一样的，虽然它在许多方面也违反了 ISO 9660 的标准。

最后一个常用的选项是 `-b`。 它用来指定启动映像的位置， 用以生成 “El Torito” 启动 CD。 这个选项使用一个参数， 用以指定将写入 CD 的目录的根。 默认情况下， [mkisofs(8)](http://www.FreeBSD.org/cgi/man.cgi?query=mkisofs&sektion=8&manpath=freebsd-release-ports) 会以常说的 “软盘模拟” 方式来创建 ISO， 因此它希望引导映像文件的尺寸恰好是 1200， 1440 或 2880 KB。 某些引导加载器， 例如 FreeBSD 发行版磁盘， 并不使用模拟模式； 这种情况下， 需要使用 `-no-emul-boot` 选项。 因此， 如果 `/tmp/myboot` 是一个包含了启动映像文件 `/tmp/myboot/boot/cdboot` 的可引导的 FreeBSD 系统， 您就可以使用下面的命令生成 ISO 9660 文件系统映像 `/tmp/bootable.iso`：

```
# `mkisofs -R -no-emul-boot -b boot/cdboot -o /tmp/bootable.iso /tmp/myboot`
```

完成这些工作之后， 如果您的内核中配置了 `md`， 就可以用下列命令来挂接文件系统了：

```
# `mdconfig -a -t vnode -f /tmp/bootable.iso -u 0`
# `mount -t cd9660 /dev/md0 /mnt`
```

可以发现 `/mnt` 和 `/tmp/myboot` 是一样的。

还可以使用 [mkisofs(8)](http://www.FreeBSD.org/cgi/man.cgi?query=mkisofs&sektion=8&manpath=freebsd-release-ports) 的其它选项来调整它的行为。特别是修改 ISO 9660 的划分格式，创建 Joliet 和 HFS 格式的磁盘。查看 [mkisofs(8)](http://www.FreeBSD.org/cgi/man.cgi?query=mkisofs&sektion=8&manpath=freebsd-release-ports) 联机手册得到更多的帮助。

### 19.6.3. burncd

如果用的是 ATAPI 的 CD 刻录机，可以使用 `burncd` 　命令来刻录您的 CD ISO 映像文件。 `burncd` 命令是基本 　系统的一部分，中以使用 `/usr/sbin/burncd` 来安装。 　用法如下：

```
# `burncd -f cddevice data imagefile.iso fixate`
```

在 *`cddevice`* 上刻录一份 *`imagefile.iso`* 的副本。 默认的设备是 `/dev/acd0`。 请参考 [burncd(8)](http://www.FreeBSD.org/cgi/man.cgi?query=burncd&sektion=8&manpath=freebsd-release-ports) 以了解设置写入速度的参数，如何在刻录完成之后自动弹出 CD，以及刻录音频数据。

### 19.6.4. cdrecord

如果没有一个 ATAPI CD 刻录机，必须使用 `cdrecord` 来刻录您的 CD 。 `cdrecord` 不是基本系统的一部分;必须 从 [sysutils/cdrtools](http://www.freebsd.org/cgi/url.cgi?ports/sysutils/cdrtools/pkg-descr) 或适当的 package 安装它。基本系统的变化可能会引起这个程序的错误。可能是由 “coaster” 引起的。当升级系统时，同时需要升级 port， 或者如果您 使用 -STABLE， 那么在升级到新版本时也要升级 port。

`cdrecord` 有许多选项，基本用法与 `burncd` 相似。刻录一个 ISO 9660 映像文件只需这样做：

```
# `cdrecord dev=device imagefile.iso`
```

使用 `cdrecord` 的比较巧妙的方法是找到使用的 `dev` 。要找到正确的设置，可以使用 `cdrecord` 的 `-scanbus` 标记，这会产生这样的结果：

```
# `cdrecord -scanbus`
Cdrecord-Clone 2.01 (i386-unknown-freebsd7.0) Copyright (C) 1995-2004 Jörg Schilling
Using libscg version 'schily-0.1'
scsibus0:
        0,0,0     0) 'SEAGATE ' 'ST39236LW       ' '0004' Disk
        0,1,0     1) 'SEAGATE ' 'ST39173W        ' '5958' Disk
        0,2,0     2) *
        0,3,0     3) 'iomega  ' 'jaz 1GB         ' 'J.86' Removable Disk
        0,4,0     4) 'NEC     ' 'CD-ROM DRIVE:466' '1.26' Removable CD-ROM
        0,5,0     5) *
        0,6,0     6) *
        0,7,0     7) *
scsibus1:
        1,0,0   100) *
        1,1,0   101) *
        1,2,0   102) *
        1,3,0   103) *
        1,4,0   104) *
        1,5,0   105) 'YAMAHA  ' 'CRW4260         ' '1.0q' Removable CD-ROM
        1,6,0   106) 'ARTEC   ' 'AM12S           ' '1.06' Scanner
        1,7,0   107) *
```

这个列表列出了设备的的适当的 `dev` 值。找到您的 CD burner ,使用三个用逗号分隔的数值来表示 `dev`.在 这个例子中，CRW 是 `dev=1,5,0`，所以正确的输入应是 dev=1,5,0 。有一个很容易的方法可以指定这个值;看看 [cdrecord(1)](http://www.FreeBSD.org/cgi/man.cgi?query=cdrecord&sektion=1&manpath=freebsd-release-ports) 的介绍了解有关音轨，控制速度和其他的东西。

### 19.6.5. 复制音频 CD

您可以这样复制 CD，把 CD 上面的音频数据解压缩出一系列的文件， 再把这些文件写到一张空白 CD 上。 这个过程对于 ATAPI 和 SCSI 驱动器来说有些微的不同。

过程 19.1. SCSI 驱动器

1.  使用 `cdda2wav` 来解压缩音频。

    ```
    % `cdda2wav -vall -D2,0 -B -Owav`
    ```

2.  使用 `cdrecord` 来写 `.wav` 文件。

    ```
    % `cdrecord -v dev=2,0 -dao -useinfo  *.wav`
    ```

    确保 *`2,0`* 被适当地设置了， 具体方法在 第 19.6.4 节 “cdrecord” 中有所描述。

过程 19.2. ATAPI 驱动器

### 注意:

借助于 ATAPI/CAM 模块， `cdda2wav` 同样也能在 ATAPI 设备上使用。 此工具比起下面推荐的方法通常是个更好的选择(抖动修正， 字节序问题， 等等)。

1.  ATAPI CD 驱动用 `/dev/acddtnn`表示每个轨道， 这里 *`d`* 是驱动器号， *`nn`* 是轨道号，由两位小数位组成，省略前缀零。 所以第一个盘片上的第一个轨道就是 `/dev/acd0t01`，第二个就是 `/dev/acd0t02`，第三个就是 `/dev/acd0t03`，等等。

    请务必确认在 `/dev` 中出现了对应的文件。 如果您发现有某些项目缺失， 则应强制系统重新识别介质：

    ```
    # `dd if=/dev/acd0 of=/dev/null count=1`
    ```

2.  使用 [dd(1)](http://www.FreeBSD.org/cgi/man.cgi?query=dd&sektion=1&manpath=freebsd-release-ports) 解压缩每个轨道。当解压缩文件的时候您也必须使用 一个特殊的块大小。

    ```
    # `dd if=/dev/acd0t01 of=track1.cdr bs=2352`
    # `dd if=/dev/acd0t02 of=track2.cdr bs=2352`
    ...

    ```

3.  使用 `burncd` 把解压缩的文件刻录到光盘上。您必须指定 这些文件是音频文件，这样 `burncd` 会在刻录完成时 结束光盘。

    ```
    # `burncd -f /dev/acd0 audio track1.cdr track2.cdr ... fixate`
    ```

### 19.6.6. 复制数据 CD

您可以把数据 CD 复制成一个与之等价的映像文件， 可以使用 [mkisofs(8)](http://www.FreeBSD.org/cgi/man.cgi?query=mkisofs&sektion=8&manpath=freebsd-release-ports) 创建这种文件， 或使用它来复制任何数据 CD。 这里给出的例子假定您的 CDROM 设备是 `acd0`， 您应将其替换为您实际使用的 CDROM 设备。

```
# `dd if=/dev/acd0 of=file.iso bs=2048`
```

现在您有一个映像文件了，您可以像上面描述的那样把它刻录成 CD。

### 19.6.7. 使用数据 CD

现在您已经创建了一张标准的数据 CDROM，您或许想要 挂载来读取上面的设备。 默认情况下，[mount(8)](http://www.FreeBSD.org/cgi/man.cgi?query=mount&sektion=8&manpath=freebsd-release-ports) 假定文件系统是 `ufs` 类型的。如果您尝试下面的命令：

```
# `mount /dev/cd0 /mnt`
```

您会得到一条 Incorrect super block 的错误信息，没有挂载成功。CDROM 不是 `UFS` 文件系统，所以试图这样挂载它是 是不行的。您需要告诉 [mount(8)](http://www.FreeBSD.org/cgi/man.cgi?query=mount&sektion=8&manpath=freebsd-release-ports) 文件系统是 `ISO9660` 类型的，这样 就可以了。只需要指定 [mount(8)](http://www.FreeBSD.org/cgi/man.cgi?query=mount&sektion=8&manpath=freebsd-release-ports) 的 `-t cd9660` 选项。例如， 如果您想要挂载 CDROM 设备， `/dev/cd0` 到 `/mnt` 目录，您需要执行：

```
# `mount -t cd9660 /dev/cd0 /mnt`
```

注意您的设备名 (在这个例子中是 `/dev/cd0`)可能 有所不同，取决于您的 CDROM 使用的接口。另外， `-t cd9660` 选项等同于执行 [mount_cd9660(8)](http://www.FreeBSD.org/cgi/man.cgi?query=mount_cd9660&sektion=8&manpath=freebsd-release-ports)。上面的例子可以缩短 为：

```
# `mount_cd9660 /dev/cd0 /mnt`
```

用这种方法您基本可以使用任何买到的数据 CDROM。 然而某些有 ISO 9660 扩展的光盘可能会行为古怪。 例如，joliet 光盘用两个字节的 unicode 字符存储所有的文件名。 FreeBSD 内核并不使用 Unicode， 但 FreeBSD CD9660 驱动可以将 Unicode 字符自动转换为内核可以识别的形式。 如果您发现有些非英文字符显示为问号， 就绪要使用 `-C` 选项来指定字符集了。 欲了解进一步的详情， 请参见联机手册 [mount_cd9660(8)](http://www.FreeBSD.org/cgi/man.cgi?query=mount_cd9660&sektion=8&manpath=freebsd-release-ports)。

### 注意:

如果希望通过 `-C` 选项来进行字符集转换， 则内核会需要加载 `cd9660_iconv.ko` 模块。 这项工作可以通过在 `loader.conf` 中加入下列配置：

```
cd9660_iconv_load="YES"
```

并重新启动计算机来完成， 除此之外， 也可以通过 [kldload(8)](http://www.FreeBSD.org/cgi/man.cgi?query=kldload&sektion=8&manpath=freebsd-release-ports) 来手动加载。

有时候，当您试图挂载 CDROM 的时候，会得到一条 Device not configured 的错误信息。这通常 表明 CDROM 驱动认为托盘里没有光盘， 或者驱动器在总线上不可见。 需要几秒钟时间等待 CDROM 驱动器辨别已经接到反馈的信息， 请耐心等待。

有时候，SCSI CDROM 可能会找不到，因为没有足够的 时间来应答总线的 reset 信号。如果您有一个 SCSI CDROM 请将下面的选项添加到您的内核 配置文件并重建您的内核。

```
options SCSI_DELAY=15000
```

这个告诉您的 SCSI 总线启动时暂停 15 秒钟， 给您的 CDROM 驱动器足够的机会来应答 总线 reset 信号。

### 19.6.8. 刻录原始数据 CD

您可以选择把一个文件目录刻录到 CD 上而不用 创建 ISO 9660 文件系统。有些人这么做是为了备份的 目的。这个运行的比刻录一个标准 CD 速度要快得多：

```
# `burncd -f /dev/acd1 -s 12 data archive.tar.gz fixate`
```

要重新找回这样刻录到 CD 上的数据， 您必须从原始设备节点读取数据：

```
# `tar xzvf /dev/acd1`
```

您不能像挂载一个通常的 CDROM 一样挂载这张光盘。 这样的 CDROM 也不能在除了 FreeBSD 之外的任何操作系统上读出。 如果您想要可以挂载 CD，或者 和另一种操作系统共享数据，您必须像上面描述的那样使用 [mkisofs(8)](http://www.FreeBSD.org/cgi/man.cgi?query=mkisofs&sektion=8&manpath=freebsd-release-ports)。

### 19.6.9. 使用 ATAPI/CAM 驱动

Contributed by Marc Fonvieille.

这个驱动允许 ATAPI 设备(CD-ROM, CD-RW, DVD 驱动器等...)通过 SCSI 子系统访问， 这样允许使用像 [sysutils/cdrdao](http://www.freebsd.org/cgi/url.cgi?ports/sysutils/cdrdao/pkg-descr) 或者 [cdrecord(1)](http://www.FreeBSD.org/cgi/man.cgi?query=cdrecord&sektion=1&manpath=freebsd-release-ports) 这样的程序。

要使用这个驱动， 您需要把下面这行添加到 `/boot/loader.conf` 文件中：

```
atapicam_load="YES"
```

接下来， 重新启动计算机。

### 注意:

如果您希望将 [atapicam(4)](http://www.FreeBSD.org/cgi/man.cgi?query=atapicam&sektion=4&manpath=freebsd-release-ports) 以静态联编的形式加入内核， 则需要在内核配置文件中加入这行：

```
device atapicam
```

此外还需要在内核配置文件中加入：

```
device ata
device scbus
device cd
device pass
```

这些应该已经有了。 然后， 重新联编并安装新内核， 并重新启动计算机。

在引导过程中， 您的刻录机将会出现在内核的提示信息中， 就像这样：

```
acd0: CD-RW <MATSHITA CD-RW/DVD-ROM UJDA740> at ata1-master PIO4
cd0 at ata1 bus 0 target 0 lun 0
cd0: <MATSHITA CDRW/DVD UJDA740 1.00> Removable CD-ROM SCSI-0 device
cd0: 16.000MB/s transfers
cd0: Attempt to query device size failed: NOT READY, Medium not present - tray closed
```

驱动器现在可以通过 `/dev/cd0` 设备名访问了，例如要 挂载 CD-ROM 到 `/mnt`，只需要键入下面的 命令：

```
# `mount -t cd9660 /dev/cd0 /mnt`
```

作为 `root`，您可以运行下面的 命令来得到刻录机的 SCSI 地址：

```
# `camcontrol devlist`
<MATSHITA CDRW/DVD UJDA740 1.00>   at scbus1 target 0 lun 0 (pass0,cd0)
```

这样 `1,0,0` 就是 SCSI 地址了，可以被 [cdrecord(1)](http://www.FreeBSD.org/cgi/man.cgi?query=cdrecord&sektion=1&manpath=freebsd-release-ports) 和其他的 SCSI 程序使用。

有关 ATAPI/CAM 和 SCSI 系统的更多信息， 可以参阅 [atapicam(4)](http://www.FreeBSD.org/cgi/man.cgi?query=atapicam&sektion=4&manpath=freebsd-release-ports) 和 [cam(4)](http://www.FreeBSD.org/cgi/man.cgi?query=cam&sektion=4&manpath=freebsd-release-ports) 手册 页。

## 19.7. 创建和使用光学介质(DVD)

Contributed by Marc Fonvieille.With inputs from Andy Polyakov.

### 19.7.1. 介绍

和 CD 相比，DVD 是下一代光学存储介质技术。 DVD 可以容纳比任何 CD 更多的数据，已经成为现今视频出版业的标准。

我们称作可记录 DVD 的有五种物理记录格式：

*   DVD-R：这是第一种可用的 DVD 可记录格式。 DVD-R 标准由 [DVD Forum](http://www.dvdforum.com/forum.shtml) 定义。 这种格式是一次可写的。

*   DVD-RW：这是 DVD-R 标准的可覆写版本。 一张 DVD-RW 可以被覆写大约 1000 次。

*   DVD-RAM：这也是一种被 DVD Forum 所支持的可覆写格式。 DVD-RAM 可以被看作一种可移动硬盘。 然而，这种介质和大部分 DVD-ROM 驱动器以及 DVD-Video 播放器不兼容； 只有少数 DVD 刻录机支持 DVD-RAM。 请参阅 第 19.7.9 节 “使用 DVD-RAM” 以了解关于如何使用 DVD-RAM 的进一步详情。

*   DVD+RW：这是一种由 [DVD+RW Alliance](http://www.dvdrw.com/) 定义的可覆写格式。一张 DVD+RW 可以被覆写大约 1000 次。

*   DVD+R：这种格式是 DVD+RW 格式的一次可写变种。

一张单层的可记录 DVD 可以存储 4,700,000,000  字节，相当于 4.38 GB 或者说 4485 MB (1 千字节等于 1024 字节)。

### 注意:

必须说明一下物理介质与应用程序的分歧。 例如 DVD-Video 是一种特殊的文件系统， 可以被覆写到任何可记录的 DVD 物理介质上： DVD-R、DVD+R、DVD-RW 等等。在选择介质类型之前， 您一定要确认刻录机和 DVD-Video 播放器 (一种单独的播放器或者计算机上的 DVD-ROM 驱动器) 是和这种介质兼容的。

### 19.7.2. 配置

[growisofs(1)](http://www.FreeBSD.org/cgi/man.cgi?query=growisofs&sektion=1&manpath=freebsd-release-ports) 将被用来实施 DVD 刻录。 这个命令是 dvd+rw-tools 工具集 ([sysutils/dvd+rw-tools](http://www.freebsd.org/cgi/url.cgi?ports/sysutils/dvd+rw-tools/pkg-descr)) 的一部分。 dvd+rw-tools 支持所有的 DVD 介质类型。

这些工具将使用 SCSI 子系统来访问设备，因此 ATAPI/CAM 支持 必须加入内核。 如果您的刻录机采用 USB 接口则不需要这么做，请参考 第 19.5 节 “USB 存储设备” 来了解 USB 设备配置的进一步详情。

此外，还需要启用 ATAPI 设备的 DMA 支持。 这一工作可以通过在 `/boot/loader.conf` 文件中加入下面的行来完成：

```
hw.ata.atapi_dma="1"
```

试图使用 dvd+rw-tools 之前您应该参考 [dvd+rw-tools 硬件兼容性列表](http://fy.chalmers.se/~appro/linux/DVD+RW/hcn.html) 是否有与您的 DVD 刻录机有关的信息。

### 注意:

如果您想要一个图形化的用户界面，您应该看一看 K3b ([sysutils/k3b](http://www.freebsd.org/cgi/url.cgi?ports/sysutils/k3b/pkg-descr))，它提供了 [growisofs(1)](http://www.FreeBSD.org/cgi/man.cgi?query=growisofs&sektion=1&manpath=freebsd-release-ports) 的一个友好界面和许多其他刻录工具。

### 19.7.3. 刻录数据 DVD

[growisofs(1)](http://www.FreeBSD.org/cgi/man.cgi?query=growisofs&sektion=1&manpath=freebsd-release-ports) 命令是 mkisofs 的前端，它会调用 [mkisofs(8)](http://www.FreeBSD.org/cgi/man.cgi?query=mkisofs&sektion=8&manpath=freebsd-release-ports) 来创建文件系统布局，完成到 DVD 上的刻录。 这意味着您不需要在刻录之前创建数据映像。

要把 `/path/to/data` 目录的数据刻录到 DVD+R 或者 DVD-R 上面，使用下面的命令：

```
# `growisofs -dvd-compat -Z /dev/cd0 -J -R /path/to/data`
```

`-J -R` 选项传递给 [mkisofs(8)](http://www.FreeBSD.org/cgi/man.cgi?query=mkisofs&sektion=8&manpath=freebsd-release-ports) 用于文件系统创建 (这表示创建带有带有 joliet 和 Rock Ridge 扩展的 ISO 9660 文件系统)， 参考 [mkisofs(8)](http://www.FreeBSD.org/cgi/man.cgi?query=mkisofs&sektion=8&manpath=freebsd-release-ports) 联机手册了解更多细节。

选项 `-Z` 用来在任何情况下初始刻录会话： 不管多会话与否。 DVD 设备，*`/dev/cd0`*， 必须依照您的配置做出改变。 `-dvd-compat` 参数会结束光盘， 光盘成为不可附加的。这会提供更多的和 DVD-ROM 驱动器的介质兼容性。

也可以刻录成一个 pre-mastered 映像, 例如记录一个映像文件 *`imagefile.iso`*, 我们可以运行：

```
# `growisofs -dvd-compat -Z /dev/cd0=imagefile.iso`
```

刻录的速度可以被检测到并自动进行调整， 根据介质和驱动器的使用情况。如果您想强制改变速度， 可以使用 `-speed=` 参数。更多的信息，请看 [growisofs(1)](http://www.FreeBSD.org/cgi/man.cgi?query=growisofs&sektion=1&manpath=freebsd-release-ports) 联机手册。

### 注意:

如果需要在刻录的编录中添加超过 4.38GB 的单个文件， 就必须使用 [mkisofs(8)](http://www.FreeBSD.org/cgi/man.cgi?query=mkisofs&sektion=8&manpath=freebsd-release-ports) 或其他相关工具 (例如 [growisofs(1)](http://www.FreeBSD.org/cgi/man.cgi?query=growisofs&sektion=1&manpath=freebsd-release-ports)) 的 `-udf -iso-level 3` 参数来创建 UDF/ISO-9660 混合文件系统。 只有在创建 ISO 映像文件或直接在盘上写数据时才需要这样做。 以这种方式创建的光盘必须通过 [mount_udf(8)](http://www.FreeBSD.org/cgi/man.cgi?query=mount_udf&sektion=8&manpath=freebsd-release-ports) 工具以 UDF 文件系统挂载， 因此只有操作系统支持 UDF 时才可以这样做， 否则盘上的文件数据可能会无法正确读出。

要创建这样的 ISO 文件：

```
% `mkisofs -R -J -udf -iso-level 3 -o imagefile.iso /path/to/data`
```

直接将文件刻录到光盘上：

```
# `growisofs -dvd-compat -udf -iso-level 3 -Z /dev/cd0 -J -R /path/to/data`
```

假如只是使用包含巨型文件的 ISO 映像文件时， 就不需要在运行 [growisofs(1)](http://www.FreeBSD.org/cgi/man.cgi?query=growisofs&sektion=1&manpath=freebsd-release-ports) 来将映像文件刻录成光盘时指定任何额外的选项了。

另外， 在映像文件中增加或直接刻录巨型文件时， 还需要注意使用最新的 [sysutils/cdrtools](http://www.freebsd.org/cgi/url.cgi?ports/sysutils/cdrtools/pkg-descr) (包含了 [mkisofs(8)](http://www.FreeBSD.org/cgi/man.cgi?query=mkisofs&sektion=8&manpath=freebsd-release-ports))， 因为旧版并不提供巨型文件支持。 如果您遇到问题， 也可以尝试一下开发版本的软件包， 例如 [sysutils/cdrtools-devel](http://www.freebsd.org/cgi/url.cgi?ports/sysutils/cdrtools-devel/pkg-descr) 并参阅 [mkisofs(8)](http://www.FreeBSD.org/cgi/man.cgi?query=mkisofs&sektion=8&manpath=freebsd-release-ports) 联机手册。

### 19.7.4. 刻录 DVD-Video

DVD-Video 是一种特殊的基于 ISO 9660 和 micro-UDF (M-UDF) 规范的文件系统。 DVD-Video 也呈现了一个特殊的数据格式， 这就是为什么您需要一个特殊的程序像 [multimedia/dvdauthor](http://www.freebsd.org/cgi/url.cgi?ports/multimedia/dvdauthor/pkg-descr) 来制作 DVD 的原因。

如果您已经有了 DVD-Video 文件系统的映像， 就可以以同样的方式制作另一个映像，可以参看前面章节的例子。 如果您想制作 DVD 并想放在特定的目录中，如在目录 `/path/to/video` 中， 可以使用下面的命令来刻录 DVD-Video：

```
# `growisofs -Z /dev/cd0 -dvd-video /path/to/video`
```

`-dvd-video` 选项将传递给 [mkisofs(8)](http://www.FreeBSD.org/cgi/man.cgi?query=mkisofs&sektion=8&manpath=freebsd-release-ports) 并指示它创建一个 DVD-Video 文件系统布局。 除此之外。 `-dvd-video` 选项也包含了 `-dvd-compat` [growisofs(1)](http://www.FreeBSD.org/cgi/man.cgi?query=growisofs&sektion=1&manpath=freebsd-release-ports) 选项。

### 19.7.5. 使用 DVD+RW

不像 CD-RW, 一个空白的 DVD+RW 在每一次使用前必须先格式化。 [growisofs(1)](http://www.FreeBSD.org/cgi/man.cgi?query=growisofs&sektion=1&manpath=freebsd-release-ports) 程序将会适时的自动对其进行适当的处理， 这是 *recommended* 的方式。您也可以使用 `dvd+rw-format` 来对 DVD+RW 进行格式化：

```
# `dvd+rw-format /dev/cd0`
```

您只需要执行这样的操作一次，牢记只有空白的 DVD+RW 介质才需要格式化。您可以以前面章节同样的方式来刻录 DVD+RW。

如果您想刻录新的数据 (刻录一个新的完整的文件系统 而不仅仅是追加一些数据) 到 DVD+RW，您不必再将其格式化成空白盘， 您只须要直接覆盖掉以前的记录即可。 (执行一个新的初始化对话), 像这样：

```
# `growisofs -Z /dev/cd0 -J -R /path/to/newdata`
```

DVD+RW 格式化程序为简单的向以前的记录追加数据提供了可能性。 这个操作有一个新的会话和一个已经存在的会话合并而成。 它不需要多个写会话过程， [growisofs(1)](http://www.FreeBSD.org/cgi/man.cgi?query=growisofs&sektion=1&manpath=freebsd-release-ports) 将在介质上 *增加* ISO 9660 文件系统。

例如，我们想追加一些数据到到我们以前的 DVD+RW 上，我们可以使用下面的命令：

```
# `growisofs -M /dev/cd0 -J -R /path/to/nextdata`
```

在以后的写操作时， 应使用与最初的刻录会话时相同的 [mkisofs(8)](http://www.FreeBSD.org/cgi/man.cgi?query=mkisofs&sektion=8&manpath=freebsd-release-ports) 选项。

### 注意:

如果您想获得与 DVD-ROM 驱动更好的兼容性，可以使用 `-dvd-compat` 选项。 在 DVD+RW 这种情况下， 这样做并不妨碍您添加数据。

如果出于某种原因您真的想要空白介质盘， 可以执行下面的命令：

```
# `growisofs -Z /dev/cd0=/dev/zero`
```

### 19.7.6. 使用 DVD-RW

DVD-RW 接受两种光盘格式：增补顺序写入和受限式覆写。默认的 DVD-RW 盘是顺序写入格式。

空白的 DVD-RW 能够直接进行刻录而不需要格式化操作， 然而非空的顺序写入格式的 DVD-RW 需要格式化才能写入新的初始区段。

要格式化一张 DVD-RW 为顺序写入模式，运行：

```
# `dvd+rw-format -blank=full /dev/cd0`
```

### 注意:

一次完全的格式化 (`-blank=full`) 在 1x 倍速的介质上将会花费大约 1 个小时。快速格式化可以使用 `-blank` 选项来进行，如果 DVD-RW 要以 Disk-At-Once (DAO) 模式刻录的话。要以 DAO 模式刻录 DVD-RW，使用命令：

```
# `growisofs -use-the-force-luke=dao -Z /dev/cd0=imagefile.iso`
```

`-use-the-force-luke=dao` 选项不是必需的， 因为 [growisofs(1)](http://www.FreeBSD.org/cgi/man.cgi?query=growisofs&sektion=1&manpath=freebsd-release-ports) 试图最低限度的检测 (快速格式化) 介质并进行 DAO 写入。

事实上对于任何 DVD-RW 都应该使用受限式覆写模式， 这种格式比默认的增补顺序写入更加灵活。

在一张顺序 DVD-RW 上写入数据，使用和其他 DVD 格式相同的指令：

```
# `growisofs -Z /dev/cd0 -J -R /path/to/data`
```

如果您想在您以前的刻录上附加数据，您必须使用 [growisofs(1)](http://www.FreeBSD.org/cgi/man.cgi?query=growisofs&sektion=1&manpath=freebsd-release-ports) 的 `-M` 选项。然而， 如果您在一张增补顺序写入模式的 DVD-RW 上附加数据， 将会在盘上创建一个新的区段，结果就是一张多区段光盘。

受限式覆写格式的 DVD-RW 在新的初始化区段前不需要格式化， 您只是要用 `-Z` 选项覆写光盘，这和 DVD+RW 的情形是相似的。也可以用和 DVD+RW 同样方式的 `-M` 选项把现存的 ISO 9660 文件系统写入光盘。 结果会是一张单区段 DVD。

要把 DVD-RW 置于受限式覆写格式， 必须使用下面的命令：

```
# `dvd+rw-format /dev/cd0`
```

更改回顺序写入模式使用：

```
# `dvd+rw-format -blank=full /dev/cd0`
```

### 19.7.7. 多区段

几乎没有哪个 DVD-ROM 驱动器支持多区段 DVD，它们大多数时候都只读取第一个区段。 顺序写入格式的 DVD+R、DVD-R 和 DVD-RW 可以支持多区段， DVD+RW 和 DVD-RW 受限式覆写格式不存在多区段的概念。

在 DVD+R、DVD-R 或者 DVD-RW 的顺序写入格式下， 一次初始化 (未关闭) 区段之后使用下面的命令， 将会在光盘上添加一个新的区段：

```
# `growisofs -M /dev/cd0 -J -R /path/to/nextdata`
```

对 DVD+RW 或者 DVD-RW 在受限式覆写模式下使用这条命令， 会合并新区段到存在的区段中来附加数据。 结果就是一张单区段光盘。 这是在这些介质上用于在最初的写操作之后添加数据的方式。

### 注意:

介质上的一些空间用于区段之间区段的开始与结束。 因此，应该用大量的数据添加区段来优化介质空间。 对于 DVD+R 来说区段的数量限制为 154， 对于 DVD-R 来说大约是 2000，对于双层 DVD+R 来说是 127。

### 19.7.8. 更多的信息

要获得更多的关于 DVD 的信息 `dvd+rw-mediainfo /dev/cd0` 命令可以运行来获得 更多的信息。

更多的关于 dvd+rw-tools 的信息可以在 [growisofs(1)](http://www.FreeBSD.org/cgi/man.cgi?query=growisofs&sektion=1&manpath=freebsd-release-ports) 联机手册找到，在 [dvd+rw-tools web site](http://fy.chalmers.se/~appro/linux/DVD+RW/) 和 [cdwrite mailing list](http://lists.debian.org/cdwrite/) 联接中也可找到。

### 注意:

`dvd+rw-mediainfo` 命令的输出结果记录， 以及介质的问题会被用来做问题报告。 如果没有这些输出， 就很难帮您解决问题。

### 19.7.9. 使用 DVD-RAM

#### 19.7.9.1. 配置

DVD-RAM 刻录机通常使用 SCSI 或 ATAPI 两种接口之一。 对于 ATAPI 设备， DMA 传输模式必须手工启用。 这一工作可以通过在 `/boot/loader.conf` 文件中增加下述配置来完成：

```
hw.ata.atapi_dma="1"
```

#### 19.7.9.2. 初始化介质

如本章前面的介绍所言， DVD-RAM 可以视为一移动硬盘。 与任何其它型号的移动硬盘类似， 首次使用它之前， 应首先 “初始化” DVD-RAM。 在下面的例子中， 我们将在全部空间上使用标准的 UFS2 文件系统：

```
# `dd if=/dev/zero of=/dev/acd0 bs=2k count=1`
# `bsdlabel -Bw acd0`
# `newfs /dev/acd0`
```

您应根据实际情况将 `acd0` 改为您所使用的设备名。

#### 19.7.9.3. 使用介质

一旦您在 DVD-RAM 上完成了前面的操作， 就可以像普通的硬盘一样挂接它了：

```
# `mount /dev/acd0 /mnt`
```

然后就可以正常地对 DVD-RAM 进行读写了。

## 19.8. 创建和使用软盘

原作 Julio Merino.重写 Martin Karlsson.

把数据存储在软盘上有时也是十分有用的。 例如， 在没有其它可靠的存储介质， 或只需将少量数据传到其他计算机时。

这一章将介绍怎样在 FreeBSD 上使用软盘。 在使用 DOS 3.5 英寸软盘时首要要涉及的就是格式化， 但其概念与其它的软盘格式化极为类似。

### 19.8.1. 格式化软盘

#### 19.8.1.1. 设备

软盘的访问像其它设备一样是通过在 `/dev` 中的条目来实现的。 直接访问软盘时， 只需简单地使用 `/dev/fdN` 来表示。

#### 19.8.1.2. 格式化

一张软盘在使用这前必须先被低级格式化。 通常卖主已经做过了，但格式化是检测介质完整性的一种好方法。 尽管这有可能会强取大量（或少量）的硬盘大小，但 大部分磁盘都能被格式化设计为 1440kB 。

低级格式化软盘你需要使用 [fdformat(1)](http://www.FreeBSD.org/cgi/man.cgi?query=fdformat&sektion=1&manpath=freebsd-release-ports) 命令。这个程序需要设备名作为参数。

要留意一切错误信息，这些信息能够帮助你确定 磁盘的好与坏。

##### 19.8.1.2.1. 软盘的格式化

使用 `/dev/fdN` 设备来格式化软盘。插入一张新的 3.5 英寸的软盘在你的设备中：

```
# `/usr/sbin/fdformat -f 1440 /dev/fd0`
```

### 19.8.2. 磁盘标签

经过低级格式化后， 你需要给它分配一个标签。 这个磁盘标签以后会被删去， 但系统需要使用它来确定磁盘的尺寸。

新的磁盘标签将会接管整个磁盘，会包括所有合适的关于软盘的 geometry 信息。 磁盘标签的 geometry 值列在 `/etc/disktab`中。

现在可以用下面的方法来使用 [bsdlabel(8)](http://www.FreeBSD.org/cgi/man.cgi?query=bsdlabel&sektion=8&manpath=freebsd-release-ports) 了：

```
# `/sbin/bsdlabel -B -w /dev/fd0 fd1440`
```

### 19.8.3. 文件系统

现在对软盘进行高级格式化。 这会在它上面安置一个新的文件系统，可使 FreeBSD 来对它进行读写。 在创建完新的文件系统后，磁盘标签将被消毁，所以如果你想重新格式化磁盘， 你必须重新创建磁盘标签。

软盘的文件系统可以选择 UFS 或 FAT 。 FAT 是通常情况下软盘比较好的选择。

要制作新的文件系统在软盘上，可以使用下面的命令：

```
# `/sbin/newfs_msdos /dev/fd0`
```

现在磁盘已经可以进行读取和使用。

### 19.8.4. 使用软盘

要使用软盘，需要先使用 [mount_msdosfs(8)](http://www.FreeBSD.org/cgi/man.cgi?query=mount_msdosfs&sektion=8&manpath=freebsd-release-ports) 挂接它。 除此之外， 也可以使用在 ports 套件中的 [emulators/mtools](http://www.freebsd.org/cgi/url.cgi?ports/emulators/mtools/pkg-descr) 程序。

## 19.9. 用磁带机备份

主流的磁带机有 4mm, 8mm, QIC, mini-cartridge 和 DLT。

### 19.9.1. 4mm (DDS: Digital Data Storage)

4mm 磁带机正在逐步取代 QIC 成为工作站备份数据的首选设备。 在 Conner 收购了 QIC 磁带机领域领先的制造商 Archive 之后不久， 即不再生产这种磁带机， 这使得这一趋势变得愈加明显。 4mm 的驱动器更加小和安静，但对于数据保存的可靠性仍不及 8mm 驱动器。它要比 8mm 的便宜和小得多 (3 x 2 x 0.5 inches, 76 x 51 x 12 mm) 。和 8mm 的一样，读写关的寿命都不长，因为它们同样使用螺旋式 的方式来读写。

这些设备的数据传输的速度约在 ~150 kB/s 到 ~500 kB/s 之间， 存储空间从 1.3 GB 到 2.0 GB 之间，硬件压缩可使空间加倍。磁带库 单元可以有 6 台磁带机，120 个磁带匣，以自动切换的方式使用同一个磁带柜， 磁带库的容量可达 240 GB 。

DDS-3 标准现在支持的磁带机容量最高可达到 12 GB (或压缩的 24 GB )。

4mm 和 8mm 同样都使用螺旋式读写的方式，所有螺旋式读写的优点及缺点， 都可以在 4mm 和 8mm 磁带机上看到。

磁带在经过 2,000 次的使用或 100 次的全部备份后，就该退休了。

### 19.9.2. 8mm (Exabyte)

8mm 磁带机是最常见的 SCSI 磁带机，也是磁带交换的最佳选择。几乎每个 工作站都有一台 2 GB 8mm 磁带机。8mm 磁带机可信度高、方便、安静。 卡匣小 (4.8 x 3.3 x 0.6 inches; 122 x 84 x 15 mm)而且不贵。8mm 磁带机 的下边是一个短短的读写头，而读写头的寿命取决于磁带经过读写头时，相对高 速运动情况。

数据传输速度约在 250 kB/s 到 500 kB/s 之间，可存储的空间从 300 MB 到 7 GB，硬件压缩可使空间加倍。磁带库单元可以有 6 台磁 带机，120 个磁带匣，以自动切换的方式使用同一个磁带柜，磁带库的容量可达 840+ GB。

Exabyte “Mammoth” 模型支持 12 GB 的容量在一个磁带 上(压缩后可达 24 GB )相当于普通磁带的二倍。

数据是使用螺旋式读写的方式记录在磁带上的，读写头和磁带约相差 6 度， 磁带以 270 度缠绕着轴，并抵住读写头，轴适时地旋转，使得磁带具有高密度， 从一端到另一端并可使磁道紧密地分布。

### 19.9.3. QIC

QIC-150 磁带和磁带机可能是最常见的磁带机和介质了。 QIC 磁带机是最便宜的 “正规” 备份设备。 它的缺点在于介质的价格较高。 QIC 磁带要比 8mm 或 4mm 磁带贵， 每 GB 的数据存储价格可能最高高出 5 倍。 但是， 如果您的需求能够为半打磁带所满足的话， 那么 QIC 可能是明智之选。 QIC 是 *最* 常见的磁带机。 每个站点都会有某种密度的 QIC。 这有时是一种麻烦， QIC 有很多在外观上相似（有时一样），但是密度不同的磁带。 QIC 磁带机噪音很大。 它们在寻址以及读写时都会发出声音。 QIC 磁带的规格是 6 x 4 x 0.7 英寸 (152 x 102 x 17 毫米)。

数据传输的速度介于 150 kB/s 到 500 kB/s 之间，可存储的空间 从 40 MB 到 15 GB。较新的 QIC 磁带机具有硬件压缩的功能。 QIC 的使用率愈来愈低，渐渐被 DAT 所取代。

数据以磁道的方式记录在磁带上，磁道数及磁道的宽度会根据容量而有所不同。 通常新的磁带机具有的向后兼容的读取功能（通常也具备写入的功能）。对于数据 的安全性，QIC 具有不错的评价。

磁带机在经过 5,000 次的使用后，就该退休了。

### 19.9.4. DLT

在这一章列出的磁带机中 DLT 具有最快的数据传输率。 1/2" (12.5mm) 的 磁带包含在单轴的磁带匣 (4 x 4 x 1 inches; 100 x 100 x 25 mm)中。磁带匣 的一边是一个旋转匣道，通过匣道的开合，可以让磁带卷动。磁带匣内只有一个 轴，而本章中所提到的其他磁带匣都是有两个轴的（9 磁道磁带机例外）。

数据传输的速度约 1.5 MB/s，是 4mm, 8mm, 或 QIC 磁带机的三倍。 可存储的空间从 10 GB 到 20 GB，具有磁带机数据库。磁带机数据库 单元可以有 1 to 20 台磁带机，5 到 900 个磁带匣，磁带机数据库的容量可达 50 GB 到 9 TB 。

如果要压缩的话，DLT 型 IV 格式的磁带机最高可支持 70 GB 的存储 容量。

数据存储在平行于磁带运行方向的磁道上（就像 QIC 磁带），一次写入两个 磁道。读写头的寿命相当长，每当磁带停止前进，磁带与读写头之间没有相对运动。

### 19.9.5. AIT

AIT 是 Sony 开发的一种新格式，每个磁带最高可以存储 50 GB。磁带 机使用内存芯片来保存磁带上的索引内容。这个索引能够被磁带机驱动器快速阅读 来搜索磁带机上文件所处的位置，而不像其他的磁带机需要花几分钟的时间才能找 到文件。像 SAMS:Alexandria 这样的软件：能够操 作四十或者更多的 AIT 磁带库，直接使用内存芯片来进行通信把内容显示在屏幕上， 以决定把什么文件备份到哪个磁带上，加载和恢复数据。

像这样的库成本大概在 $20,000 美元左右，零售市场可能还要贵一点。

### 19.9.6. 第一次使用新的磁带机

当在一块完全空白的磁带上尝试定入数据时，会得到类似下面这样的错误信息：

```
sa0(ncr1:4:0): NOT READY asc:4,1
sa0(ncr1:4:0):  Logical unit is in process of becoming ready
```

信息指出这块磁带没有块编号 (block 编号为 0)。在 QIC-525 之后的所有 QIC 磁带，都采用 QIC-525 标准，必须写入一个 Identifier Block 。对于这种问题， 有以下两种解决的办法：

*   用`mt fsf 1` 可以让磁带机对磁带写入 Identifier Block 。

*   使用面板上的按钮磁带。

    再插入一次，并存储 `dump` 数据到磁带上。

    这时`dump` 将传回 DUMP: End of tape detected ，然后您会得到这样的错误信息： HARDWARE FAILURE info:280 asc:80,96。

    这时用 `mt rewind` 来倒转磁带。

    磁带操作的后续操作就完成了。

## 19.10. 用软盘备份

### 19.10.1. 能够使用软盘来备份数据吗

软磁盘通常是用来备份的设备中不太合适的设备：

*   这种设备不太可靠，特别是长期使用。

*   备份和恢复都很慢

*   它们只有非常有限的存储容量。

然而，如果没有其它的备份数据的方法，那软盘备份总比没有备份要好。

如果必须使用软盘的话，必须确保盘片的质量。软盘在办公室中使用已经有许多 年了。最好使用一些名牌厂商的产品以确保质量。

### 19.10.2. 如何备份数据到软盘

最好的备份数据到软盘的方法是使用 [tar(1)](http://www.FreeBSD.org/cgi/man.cgi?query=tar&sektion=1&manpath=freebsd-release-ports) 程序加上 `-M` 选项， 它可以允许数据备份到多张软盘上。

要备份当前目录中所有的文件可以使用这个命令 (需要有 `root`权限)：

```
# `tar Mcvf /dev/fd0 *`
```

当第一张盘满的时候， [tar(1)](http://www.FreeBSD.org/cgi/man.cgi?query=tar&sektion=1&manpath=freebsd-release-ports) 会指示您插入下一张盘，插入第二张盘之后就按回车。

```
Prepare volume #2 for /dev/fd0 and hit return:
```

这个步骤可能需要重复很多次，直到这些文件备份完成为止。

### 19.10.3. 可以压缩备份吗

不幸的是，[tar(1)](http://www.FreeBSD.org/cgi/man.cgi?query=tar&sektion=1&manpath=freebsd-release-ports) 在为多卷文件作备份时是不允许使用 `-z` 选项的。当然，可以用 [gzip(1)](http://www.FreeBSD.org/cgi/man.cgi?query=gzip&sektion=1&manpath=freebsd-release-ports) 压缩所有的文件，把它们打包到磁盘，以后在用 [gunzip(1)](http://www.FreeBSD.org/cgi/man.cgi?query=gunzip&sektion=1&manpath=freebsd-release-ports) 解开。

### 19.10.4. 如何恢复备份

要恢复所有文件：

```
# `tar Mxvf /dev/fd0`
```

有两种方法来恢复软盘中的个别文件。首先，就要用第一张软盘启动：

```
# `tar Mxvf /dev/fd0 filename`
```

[tar(1)](http://www.FreeBSD.org/cgi/man.cgi?query=tar&sektion=1&manpath=freebsd-release-ports) 程序会提示您插入后面的软盘，直到它找到所需要的文件。

如果您知道哪个文件在哪个盘上，您就可以插入那张盘，然后使用上同同样的命令。 如果软盘上的第一个文件与前面的文件是连续的，那 [tar(1)](http://www.FreeBSD.org/cgi/man.cgi?query=tar&sektion=1&manpath=freebsd-release-ports) 命令会警告您它无法 恢复，即使您不要求它这样做。

## 19.11. 备份策略

原作 Lowell Gilbert.

设计备份计划的第一要务是确认以下问题皆已考虑到：

*   磁盘故障

*   文件的意外删除

*   随机的文件损毁

*   机器完全损毁 (例如火灾)， 包括破坏全部在线备份。

针对上述的每个问题采用完全不同的技术来解决是完全可行的。 除了只包含少量几乎没有价值数据的个人系统之外， 一般来说很少有一种技术能够同时兼顾前面所有的需要。

可以采用的技术包括：

*   对整个系统的数据进行存档， 备份到永久性的离线介质上。 这种方法实际上能够提供针对前面所有问题的保护， 但这样做通常很慢， 而且恢复时会比较麻烦。 您可以将备份置于近线或在线的状态， 然而恢复文件仍然是一个难题， 特别是对没有特权的那些用户而言。

*   文件系统快照。 这种技术实际上只对无意中删除文件这一种情况有用， 但在这种情况下它会提供 *非常大* 的帮助， 而且访问迅速， 操作容易。

*   直接复制整个文件系统和/或磁盘 (例如周期性地对整个机器做 [rsync(1)](http://www.FreeBSD.org/cgi/man.cgi?query=rsync&sektion=1&manpath=freebsd-release-ports))。 通常这对于在网络上的单一需求最为适用。 要为磁盘故障提供更为通用的保护， 通常这种方法要逊于 RAID。 对于恢复无意中删除的文件来说， 这种方法基本上与 UFS 快照属于同一层次， 使用哪一个取决于您的喜好。

*   RAID。 它能够最大限度地减少磁盘故障导致的停机时间。 其代价是需要处理更为频繁的磁盘故障 (因为磁盘的数量增加了)， 尽管这类故障不再需要作为非常紧急的事项来处理。

*   检查文件的指纹。 [mtree(8)](http://www.FreeBSD.org/cgi/man.cgi?query=mtree&sektion=8&manpath=freebsd-release-ports) 工具对于这种操作非常有用。 尽管这并不是一种备份的技术， 但它能够确保您有机会注意到那些您需要求助于离线备份的事情。 这对于离线备份非常重要， 而且应有计划地加以检查。

很容易列举更多的技术， 它们中有许多实际上是前面所列出的方法的变种。 特别的需求通常会需要采用特别的技术 (例如， 备份在线运行的数据库， 往往需要数据库软件提供某种方法来完成中间步骤) 来满足。 最重要的事情是， 一定要了解需要将数据保护起来免受何种风险， 以及发生问题时应该如何处理。

## 19.12. 备份程序

有三个主要的备份程序 [dump(8)](http://www.FreeBSD.org/cgi/man.cgi?query=dump&sektion=8&manpath=freebsd-release-ports)、[tar(1)](http://www.FreeBSD.org/cgi/man.cgi?query=tar&sektion=1&manpath=freebsd-release-ports) 和 [cpio(1)](http://www.FreeBSD.org/cgi/man.cgi?query=cpio&sektion=1&manpath=freebsd-release-ports)。

### 19.12.1. Dump 和 Restore

`dump` 和 `restore` 是 UNIX® 传统的备份程序。 它以 block 而不是以文件为单位来备份数据、链接或目录。 `dump` 备份的是设备上的整个文件系统， 不能只备份一个文件系统的部分或是用到两个以上文件系统的目录树。 与其他备份软件不同的是， `dump` 不会写文件和目录到磁带机， 而是写入包含文件 和目录的原始数据块。 当需要恢复数据的时候，`restore` 默认在 `/tmp/` 下保存临时数据 ── 如果你正在操作的恢复盘只有比较小的 `/tmp` 的话， 你可能需要把环境变量 `TMPDIR` 设置到一个有更多空间的目录， 使得此过程更容易成功。

### 注意:

如果在您的 root 目录使用 `dump`， 将不需要备份 `/home`、`/usr` 或其他目录， 因为这些是典型的其他文件系统或符号连接到那些文件系统的加载点。

`dump` 是最早出现于 AT&T UNIX 的 Version 6 (约 1975)。 默认的参数适用于 9-track 磁带(6250 bpi)， 所以如果要用高密度的磁带（最高可达 62,182 ftpi）， 就不能用默认的参数， 而要另外指定参数。 这些默认值必须在命令行被修改以更好地利用当前磁带机的功能。

`rdump` 和 `rrestore` 可以通过网络在另一台计算机的磁带机上备份数据。 这两个程序都是依靠 [rcmd(3)](http://www.FreeBSD.org/cgi/man.cgi?query=rcmd&sektion=3&manpath=freebsd-release-ports) 和 [ruserok(3)](http://www.FreeBSD.org/cgi/man.cgi?query=ruserok&sektion=3&manpath=freebsd-release-ports) 来访问远程的磁带机。 因此，运行备份的用户必须要有远程主机的 `.rhosts` 访问权。 `rdump` 和 `rrestore` 的参数必须适用于远程主机 例如，当您从 FreeBSD 连到一台 SUN 工作站 knomodo 去使用磁带机时，使用：

```
# `/sbin/rdump 0dsbfu 54000 13000 126 komodo:/dev/nsa8 /dev/da0a 2>&1`
```

要注意的是：必须检查您在使用 `.rhosts` 时的安全情况。

也可以通过使用 `ssh` 用一个更安全的方式来使用 `dump` 和 `restore` 。

例 19.1. 通过 ssh 使用 `dump`

```
# `/sbin/dump -0uan -f - /usr | gzip -2 | ssh -c blowfish \
          targetuser@targetmachine.example.com dd of=/mybigfiles/dump-usr-l0.gz`
```

或使用 `dump`　的 built-in 方法， 设置环境变量 `RSH`：

例 19.2. 通过设置 ssh 环境变量 `RSH` 使用 `dump`

```
# `RSH=/usr/bin/ssh /sbin/dump -0uan -f targetuser@targetmachine.example.com:/dev/sa0 /usr`
```

### 19.12.2. `tar`

[tar(1)](http://www.FreeBSD.org/cgi/man.cgi?query=tar&sektion=1&manpath=freebsd-release-ports) 也同样是在第 6 版 AT&T UNIX (大约是 1975 前后) 出现的。 `tar` 对文件系统直接操作； 其作用是把文件和目录写入磁带。 `tar` 并不支持 [cpio(1)](http://www.FreeBSD.org/cgi/man.cgi?query=cpio&sektion=1&manpath=freebsd-release-ports) 所提供的全部功能， 但也不需要 `cpio` 所需要使用的诡异的命令行管道。

要 `tar` 到连接在名为 `komodo` 的 Sun 机器上的 Exabyte 磁带机， 可以使用：

```
# `tar cf - . | rsh komodo dd of=tape-device obs=20b`
```

如果您担心通过网络备份会有安全问题，应当使用 `ssh` ， 而不是 `rsh`。

### 19.12.3. `cpio`

[cpio(1)](http://www.FreeBSD.org/cgi/man.cgi?query=cpio&sektion=1&manpath=freebsd-release-ports) 是 UNIX® 最早用来作文件交换的磁带机程序。它有执行字节 交换的选项，可以用几种不同的格式写入，并且可以将数据用管道传给其他程序。 `cpio` 没办法自动查找目录树内的文件列表，必须通过标准 输入 `stdin` 来指定。

`cpio` 不支持通过网络的备份方式。可以使用 pipeline 和 `rsh` 来传送数据给远程的磁带机。

```
# `for f in directory_list; do`
`find $f >> backup.list`
`done`
# `cpio -v -o --format=newc < backup.list | ssh user@host "cat > backup_device"`
```

这里的 *`directory_list`* 是要备份的目录列表， *`user`*@*`host`* 结合了将 要执行备份的用户名和主机名，*`backup_device`* 是写 入备份的设备（如 `/dev/nsa0`）。

### 19.12.4. `pax`

[pax(1)](http://www.FreeBSD.org/cgi/man.cgi?query=pax&sektion=1&manpath=freebsd-release-ports) 是符合 IEEE/POSIX® 标准的程序。多年来各种不同版本 的 `tar` 和 `cpio` 间有些不兼容。 为了防止这种情况，并使其标准化，POSIX® 出了这套新的工具程序。 `pax` 尝试可以读写各种 `cpio` 和 `tar` 的格式，并可以自己增加新的格式。它的命令 集比 `tar` 更接近 `cpio`。

### 19.12.5. Amanda

Amanda (Advanced Maryland Network Disk Archiver) 并非单一的程序，而是一个客户机/服务器模式的备份系统 。一台 Amanda 服务器可以备份任意数量执行 Amanda 的客户机或是将连上 Amanda 服务器的计算机上的数据备份到一台磁带机上。一个常见的问题是，数据写入磁带机的时间将超 过取行数据的时间，而 Amanda 解决了这个问题。它使用一个 “holding disk” 来同时备份几个文件系统。 Amanda 建立 “archive sets” 的一组磁带，用来备份在 Amanda 的配置文件中所列出的完整的文件系统。

Amanda 配置文件提供完整的备份控制及 Amanda 产生的网络传输。 Amanda 可以使用上述任何一个设备程序来向磁带写入数据。Amanda 可以从 port 或 package 取得，它并非系统默认安装的。

### 19.12.6. Do Nothing 备份策略

“Do nothing” 不是一个程序，而是被广泛使用的备份策略。 不需要预算，不需要备份的计划表，全部都不用。如果您的数据发生了什么问题， 忽略它！

如果您的时间和数据不值得您做这些事，那么 “Do nothing” 将是最好的备份程序。要注意的是，UNIX® 是相当好用的工具，您可能在几个月 内，就发现您已经收集了不少对您来说相当具有价值的文件和程序。

“Do nothing” 对于像 `/usr/obj` 和其他 可由您的计算机产生的文件来说，是最好的方法。例如这本手册包含有 HTML 或 PostScript® 格式的文件。这些文档格式是从 SGML 输入文件创建的。创建 HTML 或 PostScript® 格式的文件的备份就没有必要了。只要经常备份 SGML 文件就够了。

### 19.12.7. 哪个备份程序最好？

在[dump(8)](http://www.FreeBSD.org/cgi/man.cgi?query=dump&sektion=8&manpath=freebsd-release-ports) *时期* Elizabeth D. Zwicky 测试了所有以上列出的备份程序。在各种各样怪异的文件系统中， `dump` 是您明智的选择。Elizabeth 建立起各种各样、 奇怪或常见的文件系统，并用各种备份程序，测试在各种文件系统上备份 及恢复数据。这些怪异之处包括：具有 holes 和一个 nulls block 的文件， 文件名具有有趣字符，无法读写的文件及设备，在备份时改变文件大小，在 备份时建立或删除的文件。她将结果写在： LISA V in Oct. 1991. 参阅 [torture-testing Backup and Archive Programs](http://www.coredumps.de/doc/dump/zwicky/testdump.doc.html ).

### 19.12.8. 应急恢复程序

#### 19.12.8.1. 在出现灾难前

在遇到灾难前，只需要执行以下四个步骤：

第一，打出您的每个磁盘驱动器的磁盘标签 (例如： `bsdlabel da0 | lpr`)，文件系统表， (`/etc/fstab`) ，以及所有启动信息， 并将其复制两份。

第二， 刻录一张 “livefs” CDROM。 这个 CDROM 包含了用于引导进入 FreeBSD “livefs” 修复模式的支持， 这种模式允许用户执行许多任务， 例如执行 [dump(8)](http://www.FreeBSD.org/cgi/man.cgi?query=dump&sektion=8&manpath=freebsd-release-ports)、 [restore(8)](http://www.FreeBSD.org/cgi/man.cgi?query=restore&sektion=8&manpath=freebsd-release-ports)、 [fdisk(8)](http://www.FreeBSD.org/cgi/man.cgi?query=fdisk&sektion=8&manpath=freebsd-release-ports)、 [bsdlabel(8)](http://www.FreeBSD.org/cgi/man.cgi?query=bsdlabel&sektion=8&manpath=freebsd-release-ports)、 [newfs(8)](http://www.FreeBSD.org/cgi/man.cgi?query=newfs&sektion=8&manpath=freebsd-release-ports)、 [mount(8)](http://www.FreeBSD.org/cgi/man.cgi?query=mount&sektion=8&manpath=freebsd-release-ports)， 等等。 Livefs CD 映像文件随 FreeBSD/i386 10.3-RELEASE 提供， 可以从 `ftp://ftp.FreeBSD.org/pub/FreeBSD/releases/i386/ISO-IMAGES/10.3/FreeBSD-10.3-RELEASE-i386-livefs.iso` 获得。

第三，定期将数据备份到磁带。任何在上次备份后的改变都无法恢复。记得将 磁盘写保护。

第四， 测试在第二步所建立的 “livefs” CDROM 及备份的磁带。 写下笔记， 并和这张 CDROM、 打印副本以及磁带放在一起。 您在需要恢复数据时可能正心慌意乱， 而这些记录可能会帮助您避免毁掉备份磁带 （怎么会发生这种情况呢？ 举例来说， 本应执行 `tar xvf /dev/sa0` 命令时， 您可能会不小心输入 `tar cvf /dev/sa0`， 从而覆盖备份磁带）。

保险起见， 您可以制作两份 “livefs” CDROM 和备份磁带。 其中一份应放到其它地方， 这里说的其他地方当然不是指同一栋办公楼的地下室， 世贸中心的一大批公司已经学到了血的教训。 保存这份备份的位置应该与您的计算机和磁盘驱动器越远越好。

#### 19.12.8.2. 出现灾难后

关键问题是： 您的硬件是否幸免于难？ 由于已经做好了定期的备份工作， 因此并不需要担心软件的问题。

如果硬件已经损坏， 这些部分应该在尝试使用计算机之前换掉。

如果硬件还能用， 将 “livefs” CDROM 插入 CDROM 驱动器并引导系统。 您将看到最初安装系统时的菜单。 选择正确的国家之后， 选择 Fixit -- Repair mode with CDROM/DVD/floppy or start a shell 选项， 然后再选择 CDROM/DVD -- Use the live filesystem CDROM/DVD 这项。 您可以使用 `restore` 以及其他位于 `/mnt2/rescue` 的工具。

分别恢复每一个文件系统

试着 `mount` （例如： `mount /dev/da0a /mnt`） 第一个磁盘上的 root 分区。 如果 bsdlabel 已经毁坏， 则需要使用 `bsdlabel` 根据您先前打印存档的记录来重新分区并分配磁盘标签。 接着使用 `newfs` 重建文件系统。 以读写方式重新挂载磁盘的根分区 (`mount -u -o rw /mnt`)。 使用您的备份程序以及备份磁带恢复文件系统数据 （例如 `restore vrf /dev/sa0`）。 最后卸下文件系统 （例如 `umount /mnt`）。 对于毁掉的其他文件系统， 重复执行前面这些操作。

当您的系统正常启动后， 将您的数据备份到新的磁带。 任何造成数据丢失的的灾难都可能再次发生。 现在花一些时间， 也许可以在下次发生灾难时救您一把。

## 19.13. 网络、内存和 和以及映像文件为介质的虚拟文件系统

Reorganized and enhanced by Marc Fonvieille.

除了插在您计算机上的物理磁盘： 软盘、 CD、 硬盘驱动器， 等等之外， FreeBSD 还能识别一些其他的磁盘形式 - *虚拟磁盘*。

这还包括， 如 网络文件系统 (Network File System) 和 Coda 一类的网络文件系统、 内存以及映像文件为介质的虚拟文件系统。

随运行的 FreeBSD 版本不同， 用来创建和使用以映像文件介质文件系统和内存文件系统的工具也不尽相同。

### 注意:

系统会使用 [devfs(5)](http://www.FreeBSD.org/cgi/man.cgi?query=devfs&sektion=5&manpath=freebsd-release-ports) 来创建设备节点， 这对用户来说是透明的。

### 19.13.1. 以映像文件为介质的文件系统

在 FreeBSD 系统中， 可以用 [mdconfig(8)](http://www.FreeBSD.org/cgi/man.cgi?query=mdconfig&sektion=8&manpath=freebsd-release-ports) 程序来配置和启用内存磁盘， [md(4)](http://www.FreeBSD.org/cgi/man.cgi?query=md&sektion=4&manpath=freebsd-release-ports)。 要使用 [mdconfig(8)](http://www.FreeBSD.org/cgi/man.cgi?query=mdconfig&sektion=8&manpath=freebsd-release-ports)， 就需要在内核配置文件中添加 [md(4)](http://www.FreeBSD.org/cgi/man.cgi?query=md&sektion=4&manpath=freebsd-release-ports) 模块来支持它：

```
device md
```

[mdconfig(8)](http://www.FreeBSD.org/cgi/man.cgi?query=mdconfig&sektion=8&manpath=freebsd-release-ports) 命令支持三种类型的虚拟文件系统： 使用 [malloc(9)](http://www.FreeBSD.org/cgi/man.cgi?query=malloc&sektion=9&manpath=freebsd-release-ports)，来分配内存文件系统，内存文件系统作为文件或作为 备用的交换分区。一种使用方式是在文件中来挂载一个软盘和 CD 镜像。

将一个暨存的映像文件作为文件系统挂载：

例 19.3. 使用 `mdconfig` 挂载已经存在的映像文件

```
# `mdconfig -a -t vnode -f diskimage -u 0`
# `mount /dev/md0 /mnt`
```

使用 [mdconfig(8)](http://www.FreeBSD.org/cgi/man.cgi?query=mdconfig&sektion=8&manpath=freebsd-release-ports) 来创建新的映像文件:

例 19.4. 使用 `mdconfig` 将映像文件作为文件系统挂载

```
# `dd if=/dev/zero of=newimage bs=1k count=5k`
5120+0 records in
5120+0 records out
# `mdconfig -a -t vnode -f newimage -u 0`
# `bsdlabel -w md0 auto`
# `newfs md0a`
/dev/md0a: 5.0MB (10224 sectors) block size 16384, fragment size 2048
        using 4 cylinder groups of 1.25MB, 80 blks, 192 inodes.
super-block backups (for fsck -b #) at:
 160, 2720, 5280, 7840
# `mount /dev/md0a /mnt`
# `df /mnt`
Filesystem 1K-blocks Used Avail Capacity  Mounted on
/dev/md0a       4710    4  4330     0%    /mnt
```

如果没有通过 `-u` 选项指定一个标识号 [mdconfig(8)](http://www.FreeBSD.org/cgi/man.cgi?query=mdconfig&sektion=8&manpath=freebsd-release-ports) 将使用 [md(4)](http://www.FreeBSD.org/cgi/man.cgi?query=md&sektion=4&manpath=freebsd-release-ports) 为它自动选择一个未用的设备标识号。 分配给它的标识名将被输出到标准输出设备， 其形式是与 `md4` 类似。 如果希望了解更多相关信息， 请参见联机手册 [mdconfig(8)](http://www.FreeBSD.org/cgi/man.cgi?query=mdconfig&sektion=8&manpath=freebsd-release-ports)。

[mdconfig(8)](http://www.FreeBSD.org/cgi/man.cgi?query=mdconfig&sektion=8&manpath=freebsd-release-ports) 功能很强大， 但在将映像文件作为文件系统挂载时， 仍需使用许多行的命令。 为此 FreeBSD 也提供了一个名为 [mdmfs(8)](http://www.FreeBSD.org/cgi/man.cgi?query=mdmfs&sektion=8&manpath=freebsd-release-ports) 的工具， 该程序使用 [mdconfig(8)](http://www.FreeBSD.org/cgi/man.cgi?query=mdconfig&sektion=8&manpath=freebsd-release-ports) 来配置 [md(4)](http://www.FreeBSD.org/cgi/man.cgi?query=md&sektion=4&manpath=freebsd-release-ports) 设备， 并用 [newfs(8)](http://www.FreeBSD.org/cgi/man.cgi?query=newfs&sektion=8&manpath=freebsd-release-ports) 在其上创建 UFS 文件系统， 然后用 [mount(8)](http://www.FreeBSD.org/cgi/man.cgi?query=mount&sektion=8&manpath=freebsd-release-ports) 来完成挂载操作。 例如， 如果想创建和挂接像上面那样的文件系统映像， 只需简单地执行下面的步骤：

例 19.5. 使用 `mdmfs` 命令配置和挂载一个映像文件为文件系统

```
# `dd if=/dev/zero of=newimage bs=1k count=5k`
5120+0 records in
5120+0 records out
# `mdmfs -F newimage -s 5m md0 /mnt`
# `df /mnt`
Filesystem 1K-blocks Used Avail Capacity  Mounted on
/dev/md0        4718    4  4338     0%    /mnt
```

如果你使用没有加标识号的 `md` 选项， [mdmfs(8)](http://www.FreeBSD.org/cgi/man.cgi?query=mdmfs&sektion=8&manpath=freebsd-release-ports) 将使用 [md(4)](http://www.FreeBSD.org/cgi/man.cgi?query=md&sektion=4&manpath=freebsd-release-ports) 的自动标示号特性来自动为其 选择一个未使用的设备。更详细的 [mdmfs(8)](http://www.FreeBSD.org/cgi/man.cgi?query=mdmfs&sektion=8&manpath=freebsd-release-ports)，请参考联机手册。

### 19.13.2. 以内存为介质的文件系统

一般来说， 在建立以内存为介质的文件系统时， 应使用 “交换区作为介质 (swap backing)”。 使用交换区作为介质， 并不意味着内存盘将被无条件地换出到交换区， 它只是表示将根据需要从可换出的内存池中分配内存。 此外， 也可以使用 [malloc(9)](http://www.FreeBSD.org/cgi/man.cgi?query=malloc&sektion=9&manpath=freebsd-release-ports) 创建以内存作为介质的文件系统。 不过在内存不足时， 这种方式可能引致系统崩溃。

例 19.6. 用 `mdconfig` 创建新的内存盘设备

```
# `mdconfig -a -t swap -s 5m -u 1`
# `newfs -U md1`
/dev/md1: 5.0MB (10240 sectors) block size 16384, fragment size 2048
        using 4 cylinder groups of 1.27MB, 81 blks, 192 inodes.
        with soft updates
super-block backups (for fsck -b #) at:
 160, 2752, 5344, 7936
# `mount /dev/md1 /mnt`
# `df /mnt`
Filesystem 1K-blocks Used Avail Capacity  Mounted on
/dev/md1        4718    4  4338     0%    /mnt
```

例 19.7. 使用 `mdmfs` 来新建内存介质文件系统

```
# `mdmfs -s 5m md2 /mnt`
# `df /mnt`
Filesystem 1K-blocks Used Avail Capacity  Mounted on
/dev/md2        4846    2  4458     0%    /mnt
```

### 19.13.3. 从系统中移除内存盘设备

当不再使用内存盘设备时， 应将其资源释放回系统。 第一步操作是卸下文件系统， 然后使用 [mdconfig(8)](http://www.FreeBSD.org/cgi/man.cgi?query=mdconfig&sektion=8&manpath=freebsd-release-ports) 把虚拟磁盘从系统中分离， 以释放资源。

例如， 要分离并释放所有 `/dev/md4` 使用的资源， 应使用命令：

```
# `mdconfig -d -u 4`
```

`mdconfig -l` 命令可以列出关于配置 [md(4)](http://www.FreeBSD.org/cgi/man.cgi?query=md&sektion=4&manpath=freebsd-release-ports) 设备的信息。

## 19.14. 文件系统快照

Contributed by Tom Rhodes.

FreeBSD 提供了一个和 Soft Updates 关联的新功能: 文件系统快照

快照允许用户创建指定文件系统的映像，并把它们当做一个文件来对待。 快照文件必须在文件系统正在使用时创建，一个用户对每个文件系统创建的 快照不能大于 20 个。活动的快照文件被记录在超级块中，所以它们可以在系统 启动的时候一块进行挂接后摘掉。当一个快照不再需要时，可以使用标准的 [rm(1)](http://www.FreeBSD.org/cgi/man.cgi?query=rm&sektion=1&manpath=freebsd-release-ports) 使用来使其删除。快照可以以任何顺序进行移除，但所有使用 的快照不可能同时进行移除，因为其它的快照将有可能互相引用一些块。

不可改的 `snapshot` 文件标志， 是由 [mksnap_ffs(8)](http://www.FreeBSD.org/cgi/man.cgi?query=mksnap_ffs&sektion=8&manpath=freebsd-release-ports) 在完成创建快照文件时设置的。 [unlink(1)](http://www.FreeBSD.org/cgi/man.cgi?query=unlink&sektion=1&manpath=freebsd-release-ports) 命令是一个特例， 以允许删除快照文件。

快照可以通过 [mount(8)](http://www.FreeBSD.org/cgi/man.cgi?query=mount&sektion=8&manpath=freebsd-release-ports) 命令创建。 将文件系统 `/var` 的快照放到 `/var/snapshot/snap` 可以使用下面的命令：

```
# `mount -u -o snapshot /var/snapshot/snap /var`
```

作为选择，你也可以使用 [mksnap_ffs(8)](http://www.FreeBSD.org/cgi/man.cgi?query=mksnap_ffs&sektion=8&manpath=freebsd-release-ports) 来创建一个快照：

```
# `mksnap_ffs /var /var/snapshot/snap`
```

可以查找文件系统中的快照文件 (例如 `/var`)， 方法是使用 [find(1)](http://www.FreeBSD.org/cgi/man.cgi?query=find&sektion=1&manpath=freebsd-release-ports) 命令：

```
# `find /var -flags snapshot`
```

当快照文件被创建好后，可以用于下面一些目的：

*   有些管理员用文件快照来进行备份， 因为快照可以被转移到 CD 或磁带上。

*   文件系统一致性检查程序 [fsck(8)](http://www.FreeBSD.org/cgi/man.cgi?query=fsck&sektion=8&manpath=freebsd-release-ports) 可以用来检查快照文件。 如果文件系统在挂接前是一致的， 则检查结果也一定是一致的 (也就是不会做任何修改)。 实际上这也正是后台 [fsck(8)](http://www.FreeBSD.org/cgi/man.cgi?query=fsck&sektion=8&manpath=freebsd-release-ports) 的操作过程。

*   在快照上运行 [dump(8)](http://www.FreeBSD.org/cgi/man.cgi?query=dump&sektion=8&manpath=freebsd-release-ports) 程序。 dump 将返回包含文件系统和快照的时间戳。[dump(8)](http://www.FreeBSD.org/cgi/man.cgi?query=dump&sektion=8&manpath=freebsd-release-ports) 也能够抓取快照，使用 `-L` 标志可以首先创建快照， 完成 dump 映像之后再自动删除它。

*   用 [mount(8)](http://www.FreeBSD.org/cgi/man.cgi?query=mount&sektion=8&manpath=freebsd-release-ports) 来挂接快照作为文件系统的一个冻结的镜像。 要 [mount(8)](http://www.FreeBSD.org/cgi/man.cgi?query=mount&sektion=8&manpath=freebsd-release-ports) 快照 `/var/snapshot/snap` 运行：

    ```
    # `mdconfig -a -t vnode -f /var/snapshot/snap -u 4`
    # `mount -r /dev/md4 /mnt`
    ```

现在你就可以看到挂接在 `/mnt` 目录下的 `/var` 文件系统的快照。 每一样东西都保存的像它创建时的状态一样。 唯一例外的是更早的快照文件将表现为长度为 0 的文件。 用完快照文件之后可以把它卸下，使用：

```
# `umount /mnt`
# `mdconfig -d -u 4`
```

想了解更多关于 `softupdates` 和 文件系统快照的信息， 包括技术说明， 可以访问 Marshall Kirk McKusick 的 WWW 站点 `[`www.mckusick.com/`](http://www.mckusick.com/)`。

## 19.15. 文件系统配额

配额是操作系统的一个可选的功能， 它允许管理员以文件系统为单元， 限制分派给用户或组成员所使用的磁盘空间大小或是使用的总文件数量。 这经常被用于那些分时操作的系统上， 对于这些系统而言， 通常希望限制分派到每一个用户或组的资源总量， 从而可以防止某个用户占用所有可用的磁盘空间。

### 19.15.1. 配置系统来启用磁盘配额

在决定使用磁盘配额前，确信磁盘配额已经在内核中配置好了。只要在在内核 中配置文件中添加下面一行就行了：

```
options QUOTA
```

在默认情况下 `GENERIC` 内核是不会启用这个功能的， 所以必须配置、重建和安装一个定制的内核。请参考 FreeBSD 内核配置 第 9 章 *配置 FreeBSD 的内核* 这章了解更多有关内核配置的信息。

接下来，需要在 `/etc/rc.conf` 中启用磁盘配额。可以 通过添加下面这行来完成：

```
enable_quotas="YES"
```

为了更好的控制配额时的启动，还有另外一个可配置的变量。通常 启动时，集成在每个文件系统上的配额会被配额检查程序 [quotacheck(8)](http://www.FreeBSD.org/cgi/man.cgi?query=quotacheck&sektion=8&manpath=freebsd-release-ports) 自动检查。配额检查功能能够确保在配额数据库中 的数据正确地反映了文件系统的数 据情况。这是一个很耗时间的处理进程，它会影响系统的启动时间。如果 想跳过这一步，可以在文件 `/etc/rc.conf` 加入 下面这一行来达到目的：

```
check_quotas="NO"
```

最后，要编辑 `/etc/fstab` 文件，以在每一个 文件系统基础上启用磁盘配额。这是启用用户和组配额，或同时启用用户 和组配额的地方。

要在一个文件系统上启用每个用户的配额，可以在 `/etc/fstab` 里添加 `userquota` 选项在要雇用配额文件的系统上。例如：

```
/dev/da1s2g   /home    ufs rw,userquota 1 2
```

同样的，要启用组配额，使用 `groupquota` 选项来代替 `userquota` 选项。要同时启用用户和组配额，可以这样做：

```
/dev/da1s2g    /home    ufs rw,userquota,groupquota 1 2
```

默认情况下，配额文件是存放在文件系统的以 `quota.user` 和 `quota.group` 命名的根目录下。可以查看 [fstab(5)](http://www.FreeBSD.org/cgi/man.cgi?query=fstab&sektion=5&manpath=freebsd-release-ports) 联机手册了解更多信息。 尽管联机手册 [fstab(5)](http://www.FreeBSD.org/cgi/man.cgi?query=fstab&sektion=5&manpath=freebsd-release-ports) 提到， 可以为配额文件指定其他的位置， 但并不推荐这样做， 因为不同的配额工具并不一定遵循此规则。

到这儿，可以用新内核重新启动系统。 `/etc/rc` 将自动 运行适当的命令来创建最初的配额文件，所以并不需要手动来创建任何零长度的配额 文件。

在通常的操作过程中，并不要求手动运行 [quotacheck(8)](http://www.FreeBSD.org/cgi/man.cgi?query=quotacheck&sektion=8&manpath=freebsd-release-ports)、 [quotaon(8)](http://www.FreeBSD.org/cgi/man.cgi?query=quotaon&sektion=8&manpath=freebsd-release-ports), 或 [quotaoff(8)](http://www.FreeBSD.org/cgi/man.cgi?query=quotaoff&sektion=8&manpath=freebsd-release-ports) 命令，然而可能需要阅读与他们的操作 相似的联机手册。

### 19.15.2. 设置配额限制

一旦您配置好了启用配额的系统，可以检查一下它们是真的有用。 可以这样做：

```
# `quota -v`
```

您应该能够看到一行当前正在使用的每个文件系统启用的磁盘配额 使用情况的摘要信息。

现在可以使用 [edquota(8)](http://www.FreeBSD.org/cgi/man.cgi?query=edquota&sektion=8&manpath=freebsd-release-ports) 命令准备启用配额限制。

有几个有关如何强制限制用户或组可以分配到的磁盘空间大小的选项。 您可以限制磁盘存储块的配额， 或文件的数量， 甚至同时限制两者。 这些限制最终可分为两类： 硬限制和软限制。

硬性限制是一种不能越过的限制。 一旦用户达到了系统指定的硬性限制， 他就无法在对应的文件系统分配到更多的资源。 例如， 如果文件系统上分给用户的硬性限制是 500 KB， 而现在已经用掉了 490 KB， 那么这个用户最多还能再分配 10 KB 的空间。 换言之， 如果这时试图再分配 11 KB， 则会失败。

而与此相反， 软性限制在一段时间内是允许越过的。 这段时间也称为宽限期， 其默认值是一周。 如果一个用户延缓时间太长的话，软限制将会变成硬限制， 而继续分配磁盘空间的操作将被拒绝。 当用户占用的空间回到软性限制值以下时， 宽限期将重新开始计算。

下面是一个运行 [edquota(8)](http://www.FreeBSD.org/cgi/man.cgi?query=edquota&sektion=8&manpath=freebsd-release-ports) 时看到的例子。当 [edquota(8)](http://www.FreeBSD.org/cgi/man.cgi?query=edquota&sektion=8&manpath=freebsd-release-ports) 命令被调用时，会被转移进 `EDITOR` 环境变量指派的编辑 器中，允许编辑配额限制。如果环境变量没有设置，默认在 vi 编辑器上进行。

```
# `edquota -u test`
```

```
Quotas for user test:
/usr: kbytes in use: 65, limits (soft = 50, hard = 75)
        inodes in use: 7, limits (soft = 50, hard = 60)
/usr/var: kbytes in use: 0, limits (soft = 50, hard = 75)
        inodes in use: 0, limits (soft = 50, hard = 60)
```

在每一个启用了磁盘配额的文件系统上，通常会看到两行。一行是 block 限制，另一行是 inode 限制。简单地改变要修改的配额限制的值。 例如，提高这个用户软限制的数值到 500 ，硬限制到 600 ：

```
/usr: kbytes in use: 65, limits (soft = 50, hard = 75)
```

to:

```
/usr: kbytes in use: 65, limits (soft = 500, hard = 600)
```

当离开编辑器的时候，新的配额限制设置将会被保存。

有时，在 UIDs 的范围上设置配额限制是非常必要的。这可以通过在 [edquota(8)](http://www.FreeBSD.org/cgi/man.cgi?query=edquota&sektion=8&manpath=freebsd-release-ports) 命令后面加上 `-p` 选项来完成。首先， 给用户分配所需要的配额限制，然后运行命令 `edquota -p protouser startuid-enduid`。例如，如果 用户 `test` 已经有了所需要的配额限制，下面的命令 可以被用来复制那些 UIDs 为 10,000 到 19,999 的配额限制：

```
# `edquota -p test 10000-19999`
```

更多细节请参考 [edquota(8)](http://www.FreeBSD.org/cgi/man.cgi?query=edquota&sektion=8&manpath=freebsd-release-ports) 联机手册。

### 19.15.3. 检查配额限制和磁盘使用

既可以使用 [quota(1)](http://www.FreeBSD.org/cgi/man.cgi?query=quota&sektion=1&manpath=freebsd-release-ports) 也可以使用 [repquota(8)](http://www.FreeBSD.org/cgi/man.cgi?query=repquota&sektion=8&manpath=freebsd-release-ports) 命令来检查 配额限制和磁盘使用情况。 [quota(1)](http://www.FreeBSD.org/cgi/man.cgi?query=quota&sektion=1&manpath=freebsd-release-ports) 命令能够检查单个用户和组的配置 使用情况。只有超级用户才可以检查其它用户的配额和磁盘使用情况。 [repquota(8)](http://www.FreeBSD.org/cgi/man.cgi?query=repquota&sektion=8&manpath=freebsd-release-ports) 命令可以用来了解所有配额和磁盘的使用情况。

下面是一个使用 `quota -v` 命令后的输出情况：

```
Disk quotas for user test (uid 1002):
     Filesystem  usage    quota   limit   grace   files   quota   limit   grace
           /usr      65*     50      75   5days       7      50      60
       /usr/var       0      50      75               0      50      60
```

前面以 `/usr` 作为例子。 此用户目前已经比软限制 50 KB 超出了 15 KB， 还剩下 5 天的宽限期。 请注意， 星号 `*` 说明用户已经超出了其配额限制。

通常， 如果用户没有使用文件系统上的磁盘空间， 就不会在 [quota(1)](http://www.FreeBSD.org/cgi/man.cgi?query=quota&sektion=1&manpath=freebsd-release-ports) 命令的输出中显示， 即使已经为那个用户指定了配额。 而使用 `-v` 选项则会显示它们， 例如前面例子中的 `/usr/var`。

### 19.15.4. 通过 NFS 使用磁盘配额

配额能够在 NFS 服务器上被配额子系统强迫使用。在 NFS 客户端， [rpc.rquotad(8)](http://www.FreeBSD.org/cgi/man.cgi?query=rpc.rquotad&sektion=8&manpath=freebsd-release-ports) 命令可以使用 quota 信息用于 [quota(1)](http://www.FreeBSD.org/cgi/man.cgi?query=quota&sektion=1&manpath=freebsd-release-ports) 命令， 可以允许用户查看它们的 quota 统计信息。

可以这样在 `/etc/inetd.conf` 中启用 `rpc.rquotad`：

```
rquotad/1      dgram rpc/udp wait root /usr/libexec/rpc.rquotad rpc.rquotad
```

现在重启 `inetd`：

```
# `/etc/rc.d/inetd restart`
```

## 19.16. 加密磁盘分区

Contributed by Lucky Green.

FreeBSD 提供了极好的数据保护措施，防止未受权的数据访问。 文件权限和强制访问控制(MAC)(看 第 17 章 *强制访问控制*) 可以帮助预防在操作系统处于运行状态和计算机加电时未受权的第三方访问数据。 但是，和操作系统强制受权不相关的是，如果黑客有物理上访问计算机的可能， 那他就可以简单的把计算机的硬件安装到另一个系统上复制出敏感的数据。

无论攻击者如何取得停机后的硬件或硬盘驱动器本身， FreeBSD GEOM Based Disk Encryption (基于 GEOM 的磁盘加密， gbde) 和 `geli` 加密子系统都能够保护计算机上的文件系统数据， 使它们免受哪怕是训练有素的攻击者获得有用的资源。 与那些只能加密单个文件的笨重的加密方法不同， `gbde` 和 `geli` 能够透明地加密整个文件系统。 明文数据不会出现在硬盘的任何地方。

### 19.16.1. 使用 gbde 对磁盘进行加密

1.  **成为 `root`**

    配置 gbde 需要超级用户的权力。

    ```
    % `su -`
    Password:
    ```

2.  **在内核配置文件中添加对 [gbde(4)](http://www.FreeBSD.org/cgi/man.cgi?query=gbde&sektion=4&manpath=freebsd-release-ports) 的支持**

    在您的内核配置中加入下面一行：

    `options GEOM_BDE`

    按照 第 9 章 *配置 FreeBSD 的内核* 所进行的介绍重新编译并安装内核。

    重新引导进入新的内核。

3.  另一种无需重新编译内核的方法， 是使用 `kldload` 来加载 [gbde(4)](http://www.FreeBSD.org/cgi/man.cgi?query=gbde&sektion=4&manpath=freebsd-release-ports)：

    ```
    # `kldload geom_bde`
    ```

#### 19.16.1.1. 准备加密盘

下面这个例子假设您添加了一个新的硬盘在您的系统并将拥有一个单独的加密分区。 这个分区将挂接在 `/private`目录下。 gbde 也可以用来加密 `/home` 和 `/var/mail`， 但是这需要更多的复杂命令来执行。

1.  **添加新的硬盘**

    添加新的硬盘到系统中可以查看在 第 19.3 节 “添加磁盘” 中的说明。 这个例子的目的是说明一个新的硬盘分区已经添加到系统中如： `/dev/ad4s1c`。在例子中 `/dev/ad0s1*` 设备代表系统中存在的标准 FreeBSD 分区。

    ```
    # `ls /dev/ad*`
    /dev/ad0        /dev/ad0s1b     /dev/ad0s1e     /dev/ad4s1
    /dev/ad0s1      /dev/ad0s1c     /dev/ad0s1f     /dev/ad4s1c
    /dev/ad0s1a     /dev/ad0s1d     /dev/ad4
    ```

2.  **创建一个目录来保存 gbde Lock 文件**

    ```
    # `mkdir /etc/gbde`
    ```

    gbde lock 文件包含了 gbde 需要访问的加密分区的信息。 没有 lock 文件， gbde 将不能解密包含在加密分区上的数据。 每个加密分区使用一个独立的 lock 文件。

3.  **初始化 gbde 分区**

    一个 gbde 分区在使用前必须被初始化， 这个初始化过程只需要执行一次：

    ```
    # `gbde init /dev/ad4s1c -i -L /etc/gbde/ad4s1c.lock`
    ```

    [gbde(8)](http://www.FreeBSD.org/cgi/man.cgi?query=gbde&sektion=8&manpath=freebsd-release-ports) 将打开您的编辑器， 提示您去设置在一个模板文件中的配置变量。 使用 UFS1 或 UFS2，设置扇区大小为 2048：

    ```
    $FreeBSD: src/sbin/gbde/template.txt,v 1.1 2002/10/20 11:16:13 phk Exp $
    #
    # Sector size is the smallest unit of data which can be read or written.
    # Making it too small decreases performance and decreases available space.
    # Making it too large may prevent filesystems from working.  512 is the
    # minimum and always safe.  For UFS, use the fragment size
    #
    sector_size     =       2048
    [...]

    ```

    [gbde(8)](http://www.FreeBSD.org/cgi/man.cgi?query=gbde&sektion=8&manpath=freebsd-release-ports) 将让您输入两次用来加密数据的密钥短语。 两次输入的密钥必须相同。 gbde 保护您数据的能力依靠您选择输入的密钥的质量。 [^([10])](#ftn.idp82164976)

    `gbde init` 命令为您的 gbde 分区创建了一个 lock 文件， 在这个例子中存储在 `/etc/gbde/ad4s1c.lock`中。 gbde lock 文件必须使用 “.lock” 扩展名才能够被 `/etc/rc.d/gbde` 启动脚本正确识别。

    ### 小心:

    gbde lock 文件 *必须* 和加密分区上的内容同时备份。 如果发生只有 lock 文件遭到删除的情况时， 就没有办法确定 gbde 分区上的数据是否是解密过的。 另外， 如果没有 lock 文件， 即使磁盘的合法主人， 不经过大量细致的工作也无法访问加密分区上的数据， 而这是在设计 [gbde(8)](http://www.FreeBSD.org/cgi/man.cgi?query=gbde&sektion=8&manpath=freebsd-release-ports) 时完全没有考虑过的。

4.  **把加密分区和内核进行关联**

    ```
    # `gbde attach /dev/ad4s1c -l /etc/gbde/ad4s1c.lock`
    ```

    在加密分区的初始化过程中您将被要求提供一个密码短语。 新的加密设备将在 `/dev` 中显示为 `/dev/device_name.bde`：

    ```
    # `ls /dev/ad*`
    /dev/ad0        /dev/ad0s1b     /dev/ad0s1e     /dev/ad4s1
    /dev/ad0s1      /dev/ad0s1c     /dev/ad0s1f     /dev/ad4s1c
    /dev/ad0s1a     /dev/ad0s1d     /dev/ad4        /dev/ad4s1c.bde
    ```

5.  **在加密设备上创建文件系统**

    当加密设备和内核进行关联后， 您就可以使用 [newfs(8)](http://www.FreeBSD.org/cgi/man.cgi?query=newfs&sektion=8&manpath=freebsd-release-ports) 在此设备上创建文件系统， 使用 [newfs(8)](http://www.FreeBSD.org/cgi/man.cgi?query=newfs&sektion=8&manpath=freebsd-release-ports) 来初始化一个 UFS2 文件系统比初始化一个 UFS1 文件系统还要快，摧荐使用 `-O2` 选项。

    ```
    # `newfs -U -O2 /dev/ad4s1c.bde`
    ```

    ### 注意:

    [newfs(8)](http://www.FreeBSD.org/cgi/man.cgi?query=newfs&sektion=8&manpath=freebsd-release-ports) 命令必须在一个 gbde 分区上执行， 这个分区通过一个存在的 `*.bde` 设备名进行标识。

6.  **挂接加密分区**

    为加密文件系统创建一个挂接点。

    ```
    # `mkdir /private`
    ```

    挂接加密文件系统。

    ```
    # `mount /dev/ad4s1c.bde /private`
    ```

7.  **校验加密文件系统是否有效**

    加密的文件系统现在对于 [df(1)](http://www.FreeBSD.org/cgi/man.cgi?query=df&sektion=1&manpath=freebsd-release-ports) 应该可见并可以使用。

    ```
    % `df -H`
    Filesystem        Size   Used  Avail Capacity  Mounted on
    /dev/ad0s1a      1037M    72M   883M     8%    /
    /devfs            1.0K   1.0K     0B   100%    /dev
    /dev/ad0s1f       8.1G    55K   7.5G     0%    /home
    /dev/ad0s1e      1037M   1.1M   953M     0%    /tmp
    /dev/ad0s1d       6.1G   1.9G   3.7G    35%    /usr
    /dev/ad4s1c.bde   150G   4.1K   138G     0%    /private
    ```

#### 19.16.1.2. 挂接已有的加密文件系统

每次系统启动后， 在使用加密文件系统前必须和内核重新进行关联， 校验错误和再次挂接。使用的命令必须由 `root`用户来执行。

1.  **关联 gbde 分区到内核**

    ```
    # `gbde attach /dev/ad4s1c -l /etc/gbde/ad4s1c.lock`
    ```

    接下来系统将提示您输入在初始化加密的 gbde 分区时所用的密码短语。

2.  **校验文件系统错误**

    加密文件系统不能列在 `/etc/fstab` 文件中进行自动加载， 在加载前必须手动运行 [fsck(8)](http://www.FreeBSD.org/cgi/man.cgi?query=fsck&sektion=8&manpath=freebsd-release-ports) 命令对文件系统进行错误检测。

    ```
    # `fsck -p -t ffs /dev/ad4s1c.bde`
    ```

3.  **挂接加密文件系统**

    ```
    # `mount /dev/ad4s1c.bde /private`
    ```

    加密后的文件系统现在可以有效使用。

##### 19.16.1.2.1. 自动挂接加密分区

可以创建脚本来自动地附加、 检测， 并挂接加密分区， 然而， 处于安全考虑， 这个脚本不应包含 [gbde(8)](http://www.FreeBSD.org/cgi/man.cgi?query=gbde&sektion=8&manpath=freebsd-release-ports) 密码。 因而， 我们建议这类脚本在控制台或通过 [ssh(1)](http://www.FreeBSD.org/cgi/man.cgi?query=ssh&sektion=1&manpath=freebsd-release-ports) 执行并要求用户输入口令。

除此之外， 系统还提供了一个 `rc.d` 脚本。 这个脚本的参数可以通过 [rc.conf(5)](http://www.FreeBSD.org/cgi/man.cgi?query=rc.conf&sektion=5&manpath=freebsd-release-ports) 来指定， 例如：

```
gbde_autoattach_all="YES"
gbde_devices="ad4s1c"
gbde_lockdir="/etc/gbde"
```

在启动时将要求输入 gbde 的口令。 在输入正确的口令之后， gbde 加密分区将被自动挂接。 对于将 gbde 用在笔记本电脑上时， 这就很有用了。

#### 19.16.1.3. gbde 提供的密码学保护

[gbde(8)](http://www.FreeBSD.org/cgi/man.cgi?query=gbde&sektion=8&manpath=freebsd-release-ports) 采用 CBC 模式的 128-位 AES 来加密扇区数据。 磁盘上的每个扇区都采用不同的 AES 密钥来加密。 要了解关于 gbde 的密码学设计， 包括扇区密钥如何从用户提供的口令字中生成等细节， 请参考 [gbde(4)](http://www.FreeBSD.org/cgi/man.cgi?query=gbde&sektion=4&manpath=freebsd-release-ports)。

#### 19.16.1.4. 兼容性问题

[sysinstall(8)](http://www.FreeBSD.org/cgi/man.cgi?query=sysinstall&sektion=8&manpath=freebsd-release-ports) 是和 gbde 加密设备不兼容的。 在启动 [sysinstall(8)](http://www.FreeBSD.org/cgi/man.cgi?query=sysinstall&sektion=8&manpath=freebsd-release-ports) 时必须将 `*.bde` 设备和内核进行分离，否则在初始化探测设备时将引起冲突。 与加密设备进行分离在我们的例子中使用如下的命令：

```
# `gbde detach /dev/ad4s1c`
```

还需要注意的是， 由于 [vinum(4)](http://www.FreeBSD.org/cgi/man.cgi?query=vinum&sektion=4&manpath=freebsd-release-ports) 没有使用 [geom(4)](http://www.FreeBSD.org/cgi/man.cgi?query=geom&sektion=4&manpath=freebsd-release-ports) 子系统， 因此不能同时使用 gbde 与 vinum 卷。

### 19.16.2. 使用 `geli` 对磁盘进行加密

撰写者 Daniel Gerzo.

还有另一个可用于加密的 GEOM class ── `geli`。 它目前由 Paweł Jakub Dawidek 开发。 `Geli` 工具与 `gbde` 不同； 它提供了一些不同的功能， 并采用了不同的方式来进行密码学运算。

[geli(8)](http://www.FreeBSD.org/cgi/man.cgi?query=geli&sektion=8&manpath=freebsd-release-ports) 最重要的功能包括：

*   使用了 [crypto(9)](http://www.FreeBSD.org/cgi/man.cgi?query=crypto&sektion=9&manpath=freebsd-release-ports) 框架 ── 如果系统中有加解密硬件加速设备， 则 `geli` 会自动加以利用。

*   支持多种加密算法 (目前支持 AES、 Blowfish， 以及 3DES)。

*   允许对根分区进行加密。 在系统启动时， 将要求输入用于加密根分区的口令。

*   允许使用两个不同的密钥 (例如， 一个 “个人密钥” 和一个 “公司密钥”)。

*   `geli` 速度很快 ── 它只进行简单的扇区到扇区的加密。

*   允许备份和恢复主密钥。 当用户必须销毁其密钥时， 仍然可以通过从备份中恢复密钥来存取数据。

*   允许使用随机的一次性密钥来挂接磁盘 ── 这对于交换区和临时文件系统非常有用。

更多 `geli` 功能介绍可以在 [geli(8)](http://www.FreeBSD.org/cgi/man.cgi?query=geli&sektion=8&manpath=freebsd-release-ports) 联机手册中找到。

下面的步骤介绍了如何启用 FreeBSD 内核中的 `geli` 支持， 并解释了如何创建新和使用 `geli` 加密 provider。

由于需要修改内核， 您需要拥有超级用户权限。

1.  **在内核中加入 `geli` 支持**

    在内核配置文件中加入下面两行：

    ```
    options GEOM_ELI
    device crypto
    ```

    按照 第 9 章 *配置 FreeBSD 的内核* 介绍的步骤重新编译并安装内核。

    另外， `geli` 也可以在系统引导时加载。 这是通过在 `/boot/loader.conf` 中增加下面的配置来实现的：

    ```
    geom_eli_load="YES"
    ```

    [geli(8)](http://www.FreeBSD.org/cgi/man.cgi?query=geli&sektion=8&manpath=freebsd-release-ports) 现在应该已经为内核所支持了。

2.  **生成主密钥**

    下面的例子讲描述如何生成密钥文件， 它将作为主密钥 (Master Key) 的一部分， 用于挂接到 `/private` 的加密 provider。 这个密钥文件将提供一些随机数据来加密主密钥。 同时， 主密钥也会使用一个口令字来保护。 Provider 的扇区尺寸为 4kB。 此外， 这里的讨论将介绍如何挂载 `geli` provider， 在其上创建文件系统， 如何挂接并在其上工作， 最后将其卸下。

    建议您使用较大的扇区尺寸 (例如 4kB)， 以获得更好的性能。

    主密钥将由口令字保护， 而密钥文件的数据来源则将是 `/dev/random`。 我们称之为 provider 的 `/dev/da2.eli` 的扇区尺寸将是 4kB。

    ```
    # `dd if=/dev/random of=/root/da2.key bs=64 count=1`
    # `geli init -s 4096 -K /root/da2.key /dev/da2`
    Enter new passphrase:
    Reenter new passphrase:
    ```

    同时使用口令字和密钥文件并不是必须的； 您也可以只使用其中的一种来加密主密钥。

    如果密钥文件写作 “-”， 则表示使用标准输入。 下面是关于如何使用多个密钥文件的例子：

    ```
    # `cat keyfile1 keyfile2 keyfile3 | geli init -K - /dev/da2`
    ```

3.  **将 provider 与所生成的密钥关联**

    ```
    # `geli attach -k /root/da2.key /dev/da2`
    Enter passphrase:
    ```

    新的明文设备将被命名为 `/dev/da2.eli`。

    ```
    # `ls /dev/da2*`
    /dev/da2  /dev/da2.eli
    ```

4.  **创建新的文件系统**

    ```
    # `dd if=/dev/random of=/dev/da2.eli bs=1m`
    # `newfs /dev/da2.eli`
    # `mount /dev/da2.eli /private`
    ```

    现在加密的文件系统应该已经可以被 [df(1)](http://www.FreeBSD.org/cgi/man.cgi?query=df&sektion=1&manpath=freebsd-release-ports) 看到， 并处于可用状态了：

    ```
    # `df -H`
    Filesystem     Size   Used  Avail Capacity  Mounted on
    /dev/ad0s1a    248M    89M   139M    38%    /
    /devfs         1.0K   1.0K     0B   100%    /dev
    /dev/ad0s1f    7.7G   2.3G   4.9G    32%    /usr
    /dev/ad0s1d    989M   1.5M   909M     0%    /tmp
    /dev/ad0s1e    3.9G   1.3G   2.3G    35%    /var
    /dev/da2.eli   150G   4.1K   138G     0%    /private
    ```

5.  **卸下卷并断开 provider**

    一旦在加密分区上的工作完成， 并且不再需要 `/private` 分区， 就应考虑将其卸下并将 `geli` 加密分区从内核上断开。

    ```
    # `umount /private`
    # `geli detach da2.eli`
    ```

关于如何使用 [geli(8)](http://www.FreeBSD.org/cgi/man.cgi?query=geli&sektion=8&manpath=freebsd-release-ports) 的更多信息， 可以在其联机手册中找到。

#### 19.16.2.1. 使用 `geli` `rc.d` 脚本

`geli` 提供了一个 `rc.d` 脚本， 它可以用于简化 `geli` 的使用。 通过 [rc.conf(5)](http://www.FreeBSD.org/cgi/man.cgi?query=rc.conf&sektion=5&manpath=freebsd-release-ports) 配置 `geli` 的方法如下：

```
geli_devices="da2"
geli_da2_flags="-p -k /root/da2.key"
```

这将把 `/dev/da2` 配置为一个 `geli` provider， 其主密钥文件位于 `/root/da2.key`， 而 `geli` 在连接 provider 时将不使用口令字 (注意只有在 `geli` init 阶段使用了 `-P` 才可以这样做)。 系统将在关闭之前将 `geli` provider 断开。

关于如何配置 `rc.d` 的详细信息可以在使用手册的 rc.d 一节中找到。

* * *

[^([10])](#idp82164976) 这个提示教您怎样选择一个安全易记的密钥短语， 请看 [Diceware Passphrase](http://world.std.com/~reinhold/diceware.html) 网站。

## 19.17. 对交换区进行加密

原作 Christian Brüffer.

FreeBSD 提供了易于配置的交换区加密机制。 随所用的 FreeBSD 版本， 可用的配置选项会有所不同， 而配置方法也会有一些差异。 可以使用 [gbde(8)](http://www.FreeBSD.org/cgi/man.cgi?query=gbde&sektion=8&manpath=freebsd-release-ports) 和 [geli(8)](http://www.FreeBSD.org/cgi/man.cgi?query=geli&sektion=8&manpath=freebsd-release-ports) 两种加密系统来进行交换区的加密操作。 前面所说的这两种加密系统， 都用到了 `encswap` 这个 rc.d 脚本。

在前面的小节 如何加密磁盘分区 中， 已经就不同的加密系统之间的区别进行了简单的讨论。

### 19.17.1. 为什么需要对交换区进行加密？

与加密磁盘分区类似， 加密交换区有助于保护敏感信息。 为此， 我们不妨考虑一个需要处理敏感信息的程序， 例如， 它需要处理口令。 如果这些口令一直保持在物理内存中， 则一切相安无事。 然而， 如果操作系统开始将内存页换出到交换区， 以便为其他应用程序腾出内存时， 这些口令就可能以未加密的形式写到磁盘上， 并为攻击者所轻易获得。 加密交换区能够有效地解决这类问题。

### 19.17.2. 准备

### 注意:

在本节余下的部分中， 我们约定使用 `ad0s1b` 作为交换区。

到目前为止， 交换区仍是未加密的。 很可能其中已经存有明文形式的口令或其他敏感数据。 要纠正这一问题， 首先应使用随机数来覆盖交换分区的数据：

```
# `dd if=/dev/random of=/dev/ad0s1b bs=1m`
```

### 19.17.3. 使用 [gbde(8)](http://www.FreeBSD.org/cgi/man.cgi?query=gbde&sektion=8&manpath=freebsd-release-ports) 来加密交换区

`/etc/fstab` 中与交换区对应的行中， 设备名应追加 `.bde` 后缀：

```
# Device                Mountpoint      FStype  Options         Dump    Pass#
/dev/ad0s1b.bde         none            swap    sw              0       0

```

### 19.17.4. 使用 [geli(8)](http://www.FreeBSD.org/cgi/man.cgi?query=geli&sektion=8&manpath=freebsd-release-ports) 来加密分区

另一种方法是使用 [geli(8)](http://www.FreeBSD.org/cgi/man.cgi?query=geli&sektion=8&manpath=freebsd-release-ports) 来达到加密交换区的目的， 其过程与使用 [gbde(8)](http://www.FreeBSD.org/cgi/man.cgi?query=gbde&sektion=8&manpath=freebsd-release-ports) 大体相似。 此时， 在 `/etc/fstab` 中交换区对应的行中， 设备名应追加 `.eli` 后缀：

```
# Device                Mountpoint      FStype  Options         Dump    Pass#
/dev/ad0s1b.eli         none            swap    sw              0       0

```

[geli(8)](http://www.FreeBSD.org/cgi/man.cgi?query=geli&sektion=8&manpath=freebsd-release-ports) 默认情况下使用密钥长度为 256-位的 AES 加密算法。

当然， 这些默认值是可以通过 `/etc/rc.conf` 中的 `geli_swap_flags` 选项来修改的。 下面的配置表示让 rc.d 脚本 `encswap` 创建一个 [geli(8)](http://www.FreeBSD.org/cgi/man.cgi?query=geli&sektion=8&manpath=freebsd-release-ports) 交换区， 在其上使用密钥长度为 128-位 的 Blowfish 加密算法， 4 kilobytes 的扇区尺寸， 并采用 “最后一次关闭时卸下” 的策略：

```
geli_swap_flags="-e blowfish -l 128 -s 4096 -d"
```

请参见 [geli(8)](http://www.FreeBSD.org/cgi/man.cgi?query=geli&sektion=8&manpath=freebsd-release-ports) 联机手册中关于 `onetime` 命令的说明， 以了解其他可用的选项。

### 19.17.5. 验证所作的配置能够发挥作用

在重启系统之后， 就可以使用 `swapinfo` 命令来验证加密交换区是否已经在正常运转了。

如果使用了 [gbde(8)](http://www.FreeBSD.org/cgi/man.cgi?query=gbde&sektion=8&manpath=freebsd-release-ports)， 则：

```
% `swapinfo`
Device          1K-blocks     Used    Avail Capacity
/dev/ad0s1b.bde    542720        0   542720     0%

```

如果使用了 [geli(8)](http://www.FreeBSD.org/cgi/man.cgi?query=geli&sektion=8&manpath=freebsd-release-ports)， 则：

```
% `swapinfo`
Device          1K-blocks     Used    Avail Capacity
/dev/ad0s1b.eli    542720        0   542720     0%

```

## 19.18. 高可用性存储 (HAST)

Contributed by Daniel Gerzo.With inputs from Freddie Cash, Pawel Jakub Dawidek, Michael W. Lucas 和 Viktor Petersson.

### 19.18.1. 概述

高可用性是担负关键业务的应用的一项主要需求， 而高可用存储则是这类环境中的一项关键组件。 高可用存储 Highly Available STorage， 或 HAST， 是由 Paweł Jakub Dawidek 开发的一种用于提供在两台物理上隔离的系统之间以透明的方式， 通过 TCP/IP 网络传输数据的高可用性框架。 HAST 可以看作通过网络进行的 RAID1 (镜像)， 类似于 GNU/Linux® 平台上的 DRBD® 存储系统。 配合 FreeBSD 提供的其他高可用性基础设施， 如 CARP， HAST 可以用来构建可以抗御硬件故障的高可用存储集群。

读完这节， 您将了解：

*   何为 HAST， 它如何工作以及提供哪些功能。

*   如何在 FreeBSD 上配置和使用 HAST。

*   如何与 CARP 及 [devd(8)](http://www.FreeBSD.org/cgi/man.cgi?query=devd&sektion=8&manpath=freebsd-release-ports) 配合构建可靠的存储系统。

在阅读这节之前， 您应：

*   了解 UNIX® 和 FreeBSD 的基础知识 (第 4 章 *UNIX 基础*)。

*   知道如何配置网络接口以及其他核心 FreeBSD 子系统 (第 12 章 *设置和调整*)。

*   理解 FreeBSD 的网络功能 (第 IV 部分 “网络通讯”)。

*   使用 FreeBSD 8.1-RELEASE 或更新版本。

HAST 项目是由 FreeBSD 基金会资助完成的， 并得到了来自 [OMCnet Internet Service GmbH](http://www.omc.net/) 和 [TransIP BV](http://www.transip.nl/) 的支持。

### 19.18.2. HAST 的功能

HAST 系统提供的功能主要包括：

*   可以掩盖本地硬盘的 I/O 错误。

*   文件系统无关， 因而可以配合 FreeBSD 支持的任何文件系统使用。

*   高效率的快速重新同步机制， 令系统只同步在另一节点停机时修改过的块。

*   可以在已经部署好的环境中添加冗余。

*   配合 CARP、 Heartbeat 或其他类似的工具， 可以实现健壮的可靠存储系统。

### 19.18.3. HAST 的运行机制

由于 HAST 本质上是在多个机器间同步地进行块级复制， 因此它需要至少两个节点 (物理的机器) ── 其一作为 `主` (也称作 `master`) 节点， 另一个作为 `从` (`slave`) 节点。 这两台机器会共同构成一个集群。

### 注意:

目前 HAST 只能使用最多两个集群节点。

由于 HAST 是配置成以主从节点的方式运行， 在任何时刻都只能有唯一的一个节点是主节点。 `主` 节点， 也称作 `活跃` 节点， 负责处理由 HAST 管理的设备的全部 I/O 请求。 而 `从` 节点则会自动从 `主` 节点同步数据的变更操作。

在 HAST 系统中的物理设备包括：

*   本地磁盘 (在主节点上)

*   远程磁盘 (在从节点上)

HAST 在块的级别上同步运行， 这使其对文件系统和应用程序透明。 HAST 在 `/dev/hast/` 目录中提供标准的 GEOM 设备供其他工具或应用程序使用， 因此， 在使用上， 对应用程序或文件系统而言， HAST 提供的设备与普通的裸盘或分区等没有任何区别。

发到本地磁盘的每次写、 删除或缓存刷写操作， 都会同时通过 TCP/IP 发到远程磁盘上。 读操作是由本地磁盘完成， 除非本地磁盘上的数据不是最新的， 或发生了 I/O 错误。 在这种情况下， 读操作会在从节点上完成。

#### 19.18.3.1. 同步及复制模式

HAST 希望提供快速的故障恢复能力。 基于这一考量， 减少在某个节点停机后需要的同步时间就十分重要。 为了提供快速的同步能力， HAST 会维护一份保存在磁盘上的脏区段位映射表 (bitmap of dirty extents)， 在普通的同步模式中， 它只同步这些部分的数据 (初始的同步除外)。

处理同步有多种不同的方式， HAST 计划实现以下几种同步方式：

*   *memsync*： 当本地的写操作已经完成， 并且远程节点汇报已经收到数据时， 便认为数据的写操作已经完成， 而不是等待远程节点完成数据的写操作。 远程节点在发出回应之后， 会立即开始执行写操作。 这种模式的目标是减少响应时间， 但在同时仍然保持很好的可靠性。 目前 *memsync* 复制模式尚未实现。

*   *fullsync*： 只有在本地写操作完成， 并且远程的写操作也已经完成的情况下， 才认为数据的写操作已经完成。 这种模式是最保险， 同时也是最慢的一种复制模式。 这是目前系统预设的复制模式。

*   *async*： 在本地写操作完成时， 即认为数据已经写完。 这是最快， 同时也是风险最大的复制模式， 一般而言只有在另一节点的延迟较大时才应考虑使用。 目前 *async* 复制模式尚未实现。

### 警告:

目前， 只支持 *fullsync* 复制模式。

### 19.18.4. HAST 的配置

HAST 需要 `GEOM_GATE` 支持才能正常工作。 系统自带的预设 `GENERIC` 内核 *并不* 包含 `GEOM_GATE`， 但默认的 FreeBSD 安装包含了 `geom_gate.ko` 内核模块。 如果对系统进行了裁剪， 则应确认这个模块是否可用。 此外， `GEOM_GATE` 也可以静态联编进内核， 方法是在内核的编译配置中添加下面的设置：

```
options	GEOM_GATE
```

从操作系统的角度， HAST 框架包含了下面这些部件：

*   负责进行数据同步的 [hastd(8)](http://www.FreeBSD.org/cgi/man.cgi?query=hastd&sektion=8&manpath=freebsd-release-ports) 服务程序，

*   用于执行管理操作的 [hastctl(8)](http://www.FreeBSD.org/cgi/man.cgi?query=hastctl&sektion=8&manpath=freebsd-release-ports) 用户态管理工具，

*   配置文件 [hast.conf(5)](http://www.FreeBSD.org/cgi/man.cgi?query=hast.conf&sektion=5&manpath=freebsd-release-ports)。

下面的例子将介绍使用 HAST 在两个节点之间以 `主`-`从` 模式复制数据的方法。 两个节点的名字分别是 `hasta` 其 IP， 地址为 *`172.16.0.1`*， 以及 `hastb`， 其 IP 地址为 *`172.16.0.2`*。 这两台机器都使用尺寸相同的磁盘 `/dev/ad6` 来专用于 HAST 的运行。 HAST 存储池 (有时也称为资源， 例如位于 `/dev/hast/` 的设备文件) 将命名为 `test`。

HAST 的配置文件是 `/etc/hast.conf`。 在两个节点上， 这个文件的内容应该是完全一样的。 最简配置如下：

```
resource test {
	on hasta {
		local /dev/ad6
		remote 172.16.0.2
	}
	on hastb {
		local /dev/ad6
		remote 172.16.0.1
	}
}
```

如果需要更高级的配置， 请参阅联机手册 [hast.conf(5)](http://www.FreeBSD.org/cgi/man.cgi?query=hast.conf&sektion=5&manpath=freebsd-release-ports)。

### 提示:

在 `remote` 语句中也可以使用主机名。 这种情况下需要确保这些主机名是可以解析的， 例如在 `/etc/hosts` 文件中， 或在本地 DNS 中进行了定义。

现在在两个节点上都有同样的配置了， 接下来我们需要创建 HAST 存储池。 在两个节点上分别运行下面的命令来初始化本地此怕， 并启动 [hastd(8)](http://www.FreeBSD.org/cgi/man.cgi?query=hastd&sektion=8&manpath=freebsd-release-ports) 服务：

```
# `hastctl create test`
# `/etc/rc.d/hastd onestart`
```

### 注意:

*没有* 办法使用已经包含文件系统的 GEOM 设备来创建存储池 (换言之， 已经存在的文件系统无法转换为 HAST 管理的存储池)， 这是因为创建存储池的过程需要保存一些元数据， 而已经写入文件系统的设备不再能提供保存这些元数据所需的空间。

HAST 并不负责选择节点的角色 (`主` 或 `从`)。 节点的角色是由管理员手工， 或由类似 Heartbeat 这样的软件通过 [hastctl(8)](http://www.FreeBSD.org/cgi/man.cgi?query=hastctl&sektion=8&manpath=freebsd-release-ports) 来完成配置的。 在希望成为主节点的系统 (`hasta`) 上运行下面的命令令其成为主节点：

```
# `hastctl role primary test`
```

类似地， 用下面的命令来指明从节点 (`hastb`)：

```
# `hastctl role secondary test`
```

### 小心:

有可能会出现两个节点之间无法正常通讯， 但又都配置为主节点这样的情况； 这种称作 `脑分裂` 的状态是十分危险的。 在 第 19.18.5.2 节 “从脑分裂状态恢复” 中介绍了如何从这种状态中恢复的方法。

接下来， 可以在两个节点上分别用 [hastctl(8)](http://www.FreeBSD.org/cgi/man.cgi?query=hastctl&sektion=8&manpath=freebsd-release-ports) 工具来验证节点身份是否正确：

```
# `hastctl status test`
```

这其中比较重要的是 `status`(状态) 这行， 在两个节点上， 其输出均应为 `complete`(完好)。 如果系统给出的输出是 `degraded` (降级)， 则表示出现了问题。 正常情况下， 节点间的同步已经开始。 当 `hastctl status` 命令报告的 `dirty` 数据块数量为 0 字节时， 表示两个节点的数据已经完全同步。

最后一步是在 GEOM 设备 `/dev/hast/test` 上创建文件系统。 这项工作必须在 `主` 节点上进行 (因为 `/dev/hast/test` 只在 `主` 节点上出现)， 随硬盘尺寸的不同， 这可能需要花费数分钟的时间：

```
# `newfs -U /dev/hast/test`
# `mkdir /hast/test`
# `mount /dev/hast/test /hast/test`
```

一旦完成了 HAST 框架的配置， 最后一步就是确保 HAST 在系统引导过程中会自动启动了。 为了达到这个目的， 应在 `/etc/rc.conf` 文件中添加这行配置：

```
hastd_enable="YES"
```

#### 19.18.4.1. 故障转移配置

这个例子的目的在于建立一套健壮的存储系统， 令其能够抵御在任何一个节点上发生的故障。 这其中的关键任务是对集群中的 `主` 节点发生故障的情形进行及时的补救处理。 当发生这种情况时， `从` 节点可以无缝地接手主节点的工作， 对文件系统进行检查并挂接， 从而继续运行， 而不损失任何数据。

为了达成这一任务， 需要使用 FreeBSD 提供的另一项功能 ── CARP 所提供的 IP 层自动故障转移能力。 CARP 是共用地址冗余协议 Common Address Redundancy Protocol 的缩写， 它允许多个同网段的主机共享同一 IP 地址。 请根据 第 32.14 节 “Common Address Redundancy Protocol (CARP， 共用地址冗余协议)”") 的介绍在两个节点上都配置 CARP。 完成这些配置之后， 两个节点都会有自己的 `carp0` 网络接口， 共用 IP 地址 *`172.16.0.254`*。 显然， 集群中的 HAST 主节点也必须是 CARP 主节点。

前面一节中创建的 HAST 存储池现在可以提供给网络上的其他主机使用了。 其上的文件系统可以通过 NFS、 Samba 等等， 以共用 IP 地址 *`172.16.0.254`* 来访问。 现在余下的唯一问题是自动化对主节点故障的处理。

当 CARP 网络接口的链路状态发生变化时， FreeBSD 操作系统会产生一个 [devd(8)](http://www.FreeBSD.org/cgi/man.cgi?query=devd&sektion=8&manpath=freebsd-release-ports) 消息， 这样就可以监视 CARP 网络接口的状态了。 CARP 接口的状态变化表示节点发生故障， 或重新回到了网络中。 这些情况下需要运行特定的脚本来完成对应的处理。

为了截获 CARP 网络接口的状态变化， 需要在两个节点的 `/etc/devd.conf` 文件中添加如下的设置：

```
notify 30 {
	match "system" "IFNET";
	match "subsystem" "carp0";
	match "type" "LINK_UP";
	action "/usr/local/sbin/carp-hast-switch master";
};

notify 30 {
	match "system" "IFNET";
	match "subsystem" "carp0";
	match "type" "LINK_DOWN";
	action "/usr/local/sbin/carp-hast-switch slave";
};
```

为使编辑的配置生效， 需要在两个节点上执行下面的命令：

```
# `/etc/rc.d/devd restart`
```

当网络接口 `carp0` 的状态发生变化时， 系统会产生一个通知消息， 这允许 [devd(8)](http://www.FreeBSD.org/cgi/man.cgi?query=devd&sektion=8&manpath=freebsd-release-ports) 子系统运行管理员指定的任意脚本， 在这个例子中是 `/usr/local/sbin/carp-hast-switch`。 这个脚本的作用是自动化故障转移。 关于前面 [devd(8)](http://www.FreeBSD.org/cgi/man.cgi?query=devd&sektion=8&manpath=freebsd-release-ports) 配置的具体含义， 请参阅联机手册 [devd.conf(5)](http://www.FreeBSD.org/cgi/man.cgi?query=devd.conf&sektion=5&manpath=freebsd-release-ports)。

下面是一个这种脚本的示例：

```
#!/bin/sh

# Original script by Freddie Cash <fjwcash@gmail.com>
# Modified by Michael W. Lucas <mwlucas@BlackHelicopters.org>
# and Viktor Petersson <vpetersson@wireload.net>

# The names of the HAST resources, as listed in /etc/hast.conf
resources="test"

# delay in mounting HAST resource after becoming master
# make your best guess
delay=3

# logging
log="local0.debug"
name="carp-hast"

# end of user configurable stuff

case "$1" in
	master)
		logger -p $log -t $name "Switching to primary provider for ${resources}."
		sleep ${delay}

		# Wait for any "hastd secondary" processes to stop
		for disk in ${resources}; do
			while $( pgrep -lf "hastd: ${disk} \(secondary\)" > /dev/null 2>&1 ); do
				sleep 1
			done

			# Switch role for each disk
			hastctl role primary ${disk}
			if [ $? -ne 0 ]; then
				logger -p $log -t $name "Unable to change role to primary for resource ${disk}."
				exit 1
			fi
		done

		# Wait for the /dev/hast/* devices to appear
		for disk in ${resources}; do
			for I in $( jot 60 ); do
				[ -c "/dev/hast/${disk}" ] && break
				sleep 0.5
			done

			if [ ! -c "/dev/hast/${disk}" ]; then
				logger -p $log -t $name "GEOM provider /dev/hast/${disk} did not appear."
				exit 1
			fi
		done

		logger -p $log -t $name "Role for HAST resources ${resources} switched to primary."

		logger -p $log -t $name "Mounting disks."
		for disk in ${resources}; do
			mkdir -p /hast/${disk}
			fsck -p -y -t ufs /dev/hast/${disk}
			mount /dev/hast/${disk} /hast/${disk}
		done

	;;

	slave)
		logger -p $log -t $name "Switching to secondary provider for ${resources}."

		# Switch roles for the HAST resources
		for disk in ${resources}; do
			if ! mount | grep -q "^/dev/hast/${disk} on "
			then
			else
				umount -f /hast/${disk}
			fi
			sleep $delay
			hastctl role secondary ${disk} 2>&1
			if [ $? -ne 0 ]; then
				logger -p $log -t $name "Unable to switch role to secondary for resource ${disk}."
				exit 1
			fi
			logger -p $log -t $name "Role switched to secondary for resource ${disk}."
		done
	;;
esac
```

简而言之， 在节点成为网络的 `master` / `primary` 节点时， 脚本会进行下面的操作：

*   在本节点升格为 HAST 存储池的主节点。

*   检查 HAST 存储池上的文件系统。

*   挂接存储池中的文件系统到适当的位置。

当节点成为 `backup` / `secondary` 节点时：

*   卸下 HAST 存储池。

*   将本节点降格为 HAST 存储池的从节点。

### 小心:

务必注意， 上面的脚本只是概念性的介绍。 它并不能处理所有可能发生的情况， 因此应根据实际情况进行修改， 例如启动/停止必要的服务， 等等。

### 提示:

在前面的例子中， 出于示范的目的我们使用的是标准的 UFS 文件系统。 为了减少恢复所需的时间， 可以使用带日志的 UFS 文件系统， 或者使用 ZFS 文件系统。

更具体的信息和例子请参阅 [HAST Wiki](http://wiki.FreeBSD.org/HAST) 页面。

### 19.18.5. 故障排除

#### 19.18.5.1. 一般故障排除提示

HAST 通常都能够无故障地运行， 不过， 和任何其他软件产品一样， 有时它也可能会无法以希望的方式运转。 导致问题的可能性有很多， 但一般来说， 首先要确保集群中所有节点的时间是同步的。

当尝试排除 HAST 故障时， 应提高 [hastd(8)](http://www.FreeBSD.org/cgi/man.cgi?query=hastd&sektion=8&manpath=freebsd-release-ports) 的调试级别。 这可以通过在启动 [hastd(8)](http://www.FreeBSD.org/cgi/man.cgi?query=hastd&sektion=8&manpath=freebsd-release-ports) 服务时指定 `-d` 参数来实现。 需要说明的是， 可以多次指定这一参数来进一步提高调试级别。 此外， 还可以考虑使用 `-F` 参数来启动服务， 它会令 [hastd(8)](http://www.FreeBSD.org/cgi/man.cgi?query=hastd&sektion=8&manpath=freebsd-release-ports) 服务在前台运行。

#### 19.18.5.2. 从脑分裂状态恢复

当集群中的两个节点之间无法相互通讯时， 两个节点都会认为自己是主节点， 从而导致 `脑分裂` 的状态。 这种情形十分危险， 因为两个节点会产生互相无法合并的数据。 这种情形需要系统管理员实施手工干预。

从这种状态中恢复时， 管理员必须决定哪一个节点包含最重要的数据变动 (或者手工合并这些改动) 并让 HAST 进行一次完整的同步操作， 覆盖有问题的那个节点的数据。 要完成这个工作，在有问题的节点上执行下面的命令：

```
# `hastctl role init <resource>`
# `hastctl create <resource>`
# `hastctl role secondary <resource>`
```

## 第 20 章 GEOM： 模块化磁盘变换框架

原作 Tom Rhodes.

## 20.1. 概述

本章将介绍以 FreeBSD GEOM 框架来使用磁盘。 这包括了使用这一框架来配置的主要的 RAID 控制工具。 这一章不会深入讨论 GEOM 如何处理或控制 I/O、 其下层的子系统或代码。 您可以从 [geom(4)](http://www.FreeBSD.org/cgi/man.cgi?query=geom&sektion=4&manpath=freebsd-release-ports) 联机手册及其众多 SEE ALSO 参考文献中得到这些信息。 这一章也不是对 RAID 配置的权威介绍， 它只介绍由 支持 GEOM 的 RAID 级别。

读完这章， 您将了解：

*   通过 GEOM 支持的 RAID 类型。

*   如何使用基本工具来配置和管理不同的 RAID 级别。

*   如何通过 GEOM 使用镜像、 条带、 加密和挂接在远程的磁盘设备。

*   如何排除挂接在 GEOM 框架上的磁盘设备的问题。

阅读这章之前， 您应：

*   理解 FreeBSD 如何处理磁盘设备 (第 19 章 *存储*)。

*   了解如何配置和安装新的 FreeBSD 内核 (第 9 章 *配置 FreeBSD 的内核*)。

## 20.2. GEOM 介绍

GEOM 允许访问和控制类 (classes) ── 主引导记录、 BSD 标签 (label)， 等等 ── 通过使用 provider， 或在 `/dev` 中的特殊文件。 它支持许多软件 RAID 配置， GEOM 能够向操作系统， 以及在其上运行的工具提供透明的访问方式。

## 20.3. RAID0 - 条带

原作 Tom Rhodes 和 Murray Stokely.

条带是一种将多个磁盘驱动器合并为一个卷的方法。 许多情况下， 这是通过硬件控制器来完成的。 GEOM 磁盘子系统提供了 RAID0 的软件支持， 它也成为磁盘条带。

在 RAID0 系统中， 数据被分为多个块， 这些块将分别写入阵列的所有磁盘。 与先前需要等待系统将 256k 数据写到一块磁盘上不同， RAID0 系统， 能够同时分别将打碎的 64k 写到四块磁盘上， 从而提供更好的 I/O 性能。 这一性能提升还能够通过使用多个磁盘控制器来进一步改进。

在 RAID0 条带中的每一个盘的尺寸必须一样， 因为 I/O 请求是分散到多个盘上的， 以便让这些盘上的读写并行完成。

![磁盘条带图](img/striping.png)过程 20.1. 在未格式化的 ATA 磁盘上建立条带

1.  加载 `geom_stripe.ko` 模块：

    ```
    # `kldload geom_stripe`
    ```

2.  确信存在合适的挂接点 (mount point)。 如果这个卷将成为根分区， 那么暂时把它挂接到其他位置 i， 如 `/mnt`：

    ```
    # `mkdir /mnt`
    ```

3.  确定将被做成条带卷的磁盘的设备名， 并创建新的条带设备。 举例而言， 要将两个未用的、 尚未分区的 ATA 磁盘 `/dev/ad2` 和 `/dev/ad3` 做成一个条带设备：

    ```
    # `gstripe label -v st0 /dev/ad2 /dev/ad3`
    Metadata value stored on /dev/ad2.
    Metadata value stored on /dev/ad3.
    Done.
    ```

4.  接着需要写标准的 label， 也就是通常所说的分区表到新卷上， 并安装标准的引导代码：

    ```
    # `bsdlabel -wB /dev/stripe/st0`
    ```

5.  上述过程将在 `/dev/stripe` 目录中的 `st0` 设备基础上建立两个新设备。 这包括 `st0a` 和 `st0c`。 这时， 就可以在 `st0a` 设备上用下述 `newfs` 命令来建立文件系统了：

    ```
    # `newfs -U /dev/stripe/st0a`
    ```

    在屏幕上将滚过一些数字， 整个操作应该能在数秒内完成。 现在可以挂接刚刚做好的卷了。

要挂接刚创建的条带盘：

```
# `mount /dev/stripe/st0a /mnt`
```

要在启动过程中自动挂接这个条带上的文件系统， 需要把关于卷的信息放到 `/etc/fstab` 文件中。为达到此目的， 需要创建一个叫 `stripe` 的永久的挂载点：

```
# `mkdir /stripe`
# `echo "/dev/stripe/st0a /stripe ufs rw 2 2" \`
    `>> /etc/fstab`
```

此外， `geom_stripe.ko` 模块也必须通过在 `/boot/loader.conf` 中增加下述设置， 以便在系统初始化过程中自动加载：

```
# `echo 'geom_stripe_load="YES"' >> /boot/loader.conf`
```

## 20.4. RAID1 - 镜像

镜像是许多公司和家庭用户使用的一种无须中断的备份技术。 简单地说， 镜像的概念就是 磁盘 B 是同步复制 (replicate) 的 磁盘 A 的副本， 或者 磁盘 C+D 是 diskA+B 的同步复制副本， 等等。 无论磁盘配置如何， 这种技术的共同特点都是一块磁盘或分区的内容会同步复制到另外的地方。 这样， 除了能够很容易地恢复信息之外， 还能够在无须中断服务或访问的情况下进行备份， 甚至直接将副本送到数据保安公司异地储存。

在开始做这件事之前， 首先请准备两个容量相同的磁盘驱动器， 下面的例子假定它们都是使用直接访问方式 (Direct Access， [da(4)](http://www.FreeBSD.org/cgi/man.cgi?query=da&sektion=4&manpath=freebsd-release-ports)) 的 SCSI 磁盘。

### 20.4.1. 对主磁盘进行镜像

假定您现有系统中的 FreeBSD 安装到了第一个， 也就是 `da0` 盘上， 则应告诉 [gmirror(8)](http://www.FreeBSD.org/cgi/man.cgi?query=gmirror&sektion=8&manpath=freebsd-release-ports) 将主要数据保存在这里。

在开始构建镜像卷之前， 可以启用更多的调试信息， 并应开放对设备的完全访问。 这可以通过将 [sysctl(8)](http://www.FreeBSD.org/cgi/man.cgi?query=sysctl&sektion=8&manpath=freebsd-release-ports) 变量 `kern.geom.debugflags` 设置为下面的值来实现：

```
# `sysctl kern.geom.debugflags=17`
```

接下来需要创建镜像。 这个过程的第一步是在主磁盘上保存元数据信息， 也就是用下面的命令来创建 `/dev/mirror/gm` 设备：

### 警告:

在引导用的设备基础上新建镜像时， 有可能会导致保存在磁盘上最后一个扇区的数据丢失。 在新安装 FreeBSD 之后立即创建镜像可以减低此风险。 下面的操作与默认的 FreeBSD 9.*`X`* 安装过程不兼容， 因为它采用了新的 GPT 分区格式。 GEOM 会覆盖 GPT 元数据， 这会导致数据丢失， 并有可能导致系统无法引导。

```
# `gmirror label -vb round-robin gm0 /dev/da0`
```

系统应给出下面的回应：

```
Metadata value stored on /dev/da0.
Done.
```

初始化 GEOM， 这步操作会加载内核模块 `/boot/kernel/geom_mirror.ko`：

```
# `gmirror load`
```

### 注意:

当这个命令运行完之后， 系统会在 `/dev/mirror` 目录中创建设备节点 `gm0`。

配置在系统初始化过程中自动加载 `geom_mirror.ko`：

```
# `echo 'geom_mirror_load="YES"' >> /boot/loader.conf`
```

编辑 `/etc/fstab` 文件， 将其中先前的 `da0` 改为新的镜像设备 `gm0`。

### 注意:

如果 [vi(1)](http://www.FreeBSD.org/cgi/man.cgi?query=vi&sektion=1&manpath=freebsd-release-ports) 是你喜欢的编辑器， 以下则是完成此项任务的一个简便方法：

```
# `vi /etc/fstab`
```

在 [vi(1)](http://www.FreeBSD.org/cgi/man.cgi?query=vi&sektion=1&manpath=freebsd-release-ports) 中备份现有的 `fstab` 内容， 具体操作是 **`:w /etc/fstab.bak`**。 接着， 把所有旧的 `da0` 替换成 `gm0`， 也就是输入命令 **`:%s/da/mirror\/gm/g`**。

修改完后的 `fstab` 文件应该是下面的样子。 磁盘驱动器是 SCSI 或 ATA 甚至 RAID 都没有关系， 最终的结果都是 `gm`。

```
# Device		Mountpoint	FStype	Options		Dump	Pass#
/dev/mirror/gm0s1b	none		swap	sw		0	0
/dev/mirror/gm0s1a	/		ufs	rw		1	1
/dev/mirror/gm0s1d	/usr		ufs	rw		0	0
/dev/mirror/gm0s1f	/home		ufs	rw		2	2
#/dev/mirror/gm0s2d	/store		ufs	rw		2	2
/dev/mirror/gm0s1e	/var		ufs	rw		2       2
/dev/acd0		/cdrom		cd9660	ro,noauto	0	0
```

重启系统：

```
# `shutdown -r now`
```

在系统初始化过程中， 新建的 `gm0` 会代替 `da0` 设备工作。 系统完成初始化之后， 可以通过检查 `mount` 命令的输出来查看效果：

```
# `mount`
Filesystem         1K-blocks    Used    Avail Capacity  Mounted on
/dev/mirror/gm0s1a   1012974  224604   707334    24%    /
devfs                      1       1        0   100%    /dev
/dev/mirror/gm0s1f  45970182   28596 42263972     0%    /home
/dev/mirror/gm0s1d   6090094 1348356  4254532    24%    /usr
/dev/mirror/gm0s1e   3045006 2241420   559986    80%    /var
devfs                      1       1        0   100%    /var/named/dev
```

这个输出是正常的。 最后， 使用下面的命令将 `da1` 磁盘加到镜像卷中， 以开始同步过程：

```
# `gmirror insert gm0 /dev/da1`
```

在构建镜像卷的过程中， 可以用下面的命令查看状态：

```
# `gmirror status`
```

一旦镜像卷的构建操作完成， 这个命令的输出就会变成这样：

```
      Name    Status  Components
mirror/gm0  COMPLETE  da0
                      da1
```

如果有问题或者构建仍在进行， 输出中的 `COMPLETE` 就会是 `DEGRADED`。

### 20.4.2. 故障排除

#### 20.4.2.1. 系统拒绝引导

如果系统引导时出现类似下面的提示：

```
ffs_mountroot: can't find rootvp
Root mount failed: 6
mountroot>
```

这种情况应使用电源或复位按钮重启机器。 在引导菜单中， 选择第六 (6) 个选项。 这将让系统进入 [loader(8)](http://www.FreeBSD.org/cgi/man.cgi?query=loader&sektion=8&manpath=freebsd-release-ports) 提示符。 在此处手工加载内核模块：

```
OK? `load geom_mirror`
OK? `boot`
```

如果这样做能解决问题， 则说明由于某种原因模块没有被正确加载。 检查 `/boot/loader.conf` 中相关条目是否正确。 如果问题仍然存在，可以在内核配置文件中加入：

```
options	GEOM_MIRROR
```

然后重新编译和安装内核来解决这个问题。

### 20.4.3. 从磁盘故障中恢复

磁盘镜像的一大好处是在当其中一个磁盘出现故障时， 可以很容易地将其替换掉， 并且通常不会丢失数据。

考虑前面的 RAID1 配置， 假设 `da1` 出现了故障并需要替换， 要替换它， 首先确定哪个磁盘出现了故障， 并关闭系统。 此时， 可以用换上新的磁盘， 并重新启动系统。 这之后可以用下面的命令来完成磁盘的替换操作：

```
# `gmirror forget gm0`
```

```
# `gmirror insert gm0 /dev/da1`
```

在重建过程中可以用 `gmirror` `status` 命令来监看进度。 就是这样简单。

## 20.5. RAID3 - 使用专用校验设备的字节级条带

Written by Mark Gladman 和 Daniel Gerzo.Based on documentation by Tom Rhodes 和 Murray Stokely.

RAID3 是一种将多个磁盘组成一个卷的技术， 在这个配置中包含一个专用于校验的盘。 在 RAID3 系统中， 数据会以字节为单位拆分并写入除校验盘之外的全部驱动器中。 这意味着从 RAID3 中读取数据时将会访问所有的驱动器。 采用多个磁盘控制器可以进一步改善性能。 RAID3 阵列最多可以容忍其中的 1 个驱动器出现故障， 它可以提供全部驱动器总容量的 1 - 1/n， 此处 n 是阵列中的磁盘数量。 这类配置比较适合保存大容量的数据， 例如多媒体文件。

在建立 RAID3 阵列时， 至少需要 3 块磁盘。 所有的盘的尺寸必须一致， 因为 I/O 请求会并发分派到不同的盘上。 另外， 由于 RAID3 本身的设计， 盘的数量必须恰好是 3, 5, 9, 17, 等等 (2^n + 1)。

### 20.5.1. 建立专用的 RAID3 阵列

在 FreeBSD 中， RAID3 是通过 [graid3(8)](http://www.FreeBSD.org/cgi/man.cgi?query=graid3&sektion=8&manpath=freebsd-release-ports) GEOM class 实现的。 在 FreeBSD 中建立专用的 RAID3 阵列需要下述步骤。

### 注意:

虽然理论上从 RAID3 阵列启动 FreeBSD 是可行的， 但这并不常见， 也不推荐您这样做。

1.  首先， 在引导加载器中用下面的命令加载 `geom_raid3.ko` 内核模块：

    ```
    # `graid3 load`
    ```

    此外， 也可以通过命令行手工加载 `geom_raid3.ko` 模块：

    ```
    # `kldload geom_raid3.ko`
    ```

2.  创建用于挂载卷的挂点目录：

    ```
    # `mkdir /multimedia/`
    ```

3.  确定将要加入阵列的磁盘设备名， 并创建新的 RAID3 设备。 最终， 这个设备将代表整个阵列。 下面的例子使用三个未经分区的 ATA 磁盘： `ada1` 和 `ada2` 保存数据， 而 `ada3` 用于校验。

    ```
    # `graid3 label -v gr0 /dev/ada1 /dev/ada2 /dev/ada3`
    Metadata value stored on /dev/ada1.
    Metadata value stored on /dev/ada2.
    Metadata value stored on /dev/ada3.
    Done.
    ```

4.  为新建的 `gr0` 设备分区， 并在其上创建 UFS 文件系统：

    ```
    # `gpart create -s GPT /dev/raid3/gr0`
    # `gpart add -t freebsd-ufs /dev/raid3/gr0`
    # `newfs -j /dev/raid3/gr0p1`
    ```

    屏幕上会滚过许多数字， 这个过程需要一段时间才能完成。 此后， 您就完成了创建卷的全部操作， 可以挂载它了。

5.  最后一步是挂载文件系统：

    ```
    # `mount /dev/raid3/gr0p1 /multimedia/`
    ```

    现在可以使用 RAID3 阵列了。

为了让上述配置在系统重启后继续可用， 还需要进行一些额外的配置操作。

1.  在挂载卷之前必须首先加载 `geom_raid3.ko` 模块。 将下面的配置添加到 `/boot/loader.conf` 文件中， 可以让系统在引导过程中自动加载这个模块：

    ```
    geom_raid3_load="YES"
    ```

2.  您需要在 `/etc/fstab` 文件中加入下列配置， 以便让系统引导时自动挂载阵列上的文件系统：

    ```
    /dev/raid3/gr0p1	/multimedia	ufs	rw	2	2
    ```

## 20.6. GEOM Gate 网络设备

通过 gate 工具， GEOM 支持以远程方式使用设备， 例如磁盘、 CD-ROM、 文件等等。 这和 NFS 类似。

在开始工作之前， 首先要创建一个导出文件。 这个文件的作用是指定谁可以访问导出的资源， 以及提供何种级别的访问授权。 例如， 要把第一块 SCSI 盘的第四个 slice 导出， 对应的 `/etc/gg.exports` 会是类似下面的样子：

```
192.168.1.0/24 RW /dev/da0s4d
```

这表示允许同属私有子网的所有机器访问 `da0s4d` 分区上的文件系统。

要导出这个设备， 首先请确认它没有被挂接， 然后是启动 [ggated(8)](http://www.FreeBSD.org/cgi/man.cgi?query=ggated&sektion=8&manpath=freebsd-release-ports) 服务：

```
# `ggated`
```

现在我们将在客户机上 `mount` 该设备， 使用下面的命令：

```
# `ggatec create -o rw 192.168.1.1 /dev/da0s4d`
ggate0
# `mount /dev/ggate0 /mnt`
```

到此为止， 设备应该已经可以通过挂接点 `/mnt` 访问了。

### 注意:

请注意， 如果设备已经被服务器或网络上的任何其他机器挂接， 则前述操作将会失败。

如果不再需要使用这个设备， 就可以使用 [umount(8)](http://www.FreeBSD.org/cgi/man.cgi?query=umount&sektion=8&manpath=freebsd-release-ports) 命令来安全地将其卸下了， 这一点和其他磁盘设备类似。

## 20.7. 为磁盘设备添加卷标

在系统初始化的过程中， FreeBSD 内核会为检测到的设备创建设备节点。 这种检测方式存在一些问题， 例如， 在通过 USB 添加设备时应如何处理？ 很可能有闪存盘设备最初被识别为 `da0` 而在这之后， 则由 `da0` 变成了 `da1`。 而这则会在挂接 `/etc/fstab` 中的文件系统时造成问题， 这些问题， 还可能在系统引导时导致无法正常启动。

解决这个问题的一个方法是以连接拓扑方式链式地进行 SCSI 设备命名， 这样， 当在 SCSI 卡上增加新设备时， 这些设备将使用一个未用的编号。 但如果 USB 设备取代了主 SCSI 磁盘的位置呢？ 由于 USB 通常会在 SCSI 卡之前检测到， 因此很可能出现这种现象。 当然， 可以通过在系统引导之后再插入这些设备来绕过这个问题。 另一种绕过这个问题的方法， 则是只使用 ATA 驱动器， 并避免在 `/etc/fstab` 中列出 SCSI 设备。

还有一种更好的解决方法。 通过使用 `glabel` 工具， 管理员或用户可以为磁盘设备打上标签， 并在 `/etc/fstab` 中使用这些标签。 由于 `glabel` 会将标签保存在对应 provider 的最后一个扇区， 在系统重启之后， 它仍会持续存在。 因此， 通过将具体的设备替换为使用标签表示， 无论设备节点变成什么， 文件系统都能够顺利地完成挂接。

### 注意:

这并不是说标签一定是永久性的。 `glabel` 工具既可以创建永久性标签， 也可以创建临时性标签。 在重启时， 只有永久性标签会保持。 请参见联机手册 [glabel(8)](http://www.FreeBSD.org/cgi/man.cgi?query=glabel&sektion=8&manpath=freebsd-release-ports) 以了解两者之间的差异。

### 20.7.1. 标签类型和使用示范

有两种类型的标签， 一种是普通标签， 另一种是文件系统标签。 标签可以是永久性的或暂时性的。永久性的标签可以通过 [tunefs(8)](http://www.FreeBSD.org/cgi/man.cgi?query=tunefs&sektion=8&manpath=freebsd-release-ports) 或 [newfs(8)](http://www.FreeBSD.org/cgi/man.cgi?query=newfs&sektion=8&manpath=freebsd-release-ports) 命令创键。根据文件系统的类型， 它们将在 `/dev` 下的一个子目录中被创建。例如， UFS2 文件系统的标签会创建到 `/dev/ufs` 目录中。永久性的标签还可以使用 `glabel label` 创建。它们不再是文件系统特定的，而是会在 `/dev/label` 目录中被创建。

暂时性的标签在系统下次重启时会消失， 这些标签会创建到 `/dev/label` 目录中， 很适合测试之用。可以使用 `glabel create` 创建暂时性的标签。请参阅 [glabel(8)](http://www.FreeBSD.org/cgi/man.cgi?query=glabel&sektion=8&manpath=freebsd-release-ports) 手册页以获取更多详细信息。

要为一个 UFS2 文件系统创建永久性标签， 而不破坏其上的数据，可以使用下面的命令：

```
# `tunefs -L home /dev/da3`
```

### 警告:

如果文件系统满了， 这可能会导致数据损坏； 不过， 如果文件系统快满了， 此时应首先删除一些无用的文件， 而不是增加标签。

现在， 您应可以在 `/dev/ufs` 目录中看到标签， 并将其加入 `/etc/fstab`：

```
/dev/ufs/home		/home            ufs     rw              2      2
```

### 注意:

当运行 `tunefs` 时， 应首先卸下文件系统。

现在可以像平时一样挂接文件系统了：

```
# `mount /home`
```

现在， 只要在系统引导时通过 `/boot/loader.conf` 配置加载了内核模块 `geom_label.ko`， 或在联编内核时指定了 `GEOM_LABEL` 选项， 设备节点由于增删设备而顺序发生变化时， 就不会影响文件系统的挂接了。

通过使用 `newfs` 命令的 `-L` 参数， 可以在创建文件系统时为其添加默认的标签。 请参见联机手册 [newfs(8)](http://www.FreeBSD.org/cgi/man.cgi?query=newfs&sektion=8&manpath=freebsd-release-ports) 以了解进一步的详情。

下列命令可以清除标签：

```
# `glabel destroy home`
```

以下的例子展示了如何为一个启动磁盘打上标签。

例 20.1. 为启动磁盘打上标签

为启动磁盘打上永久性标签， 系统应该能够正常启动， 即使磁盘被移动到了另外一个控制器或者转移到了一个不同的系统上。 此例中我们假设使用了一个 ATA 磁盘， 当前这个设备被系统识别为 `ad0`。 还假设使用了标准的 FreeBSD 分区划分方案， `/`, `/var`, `/usr` 和 `/tmp` 文件系统， 还有一个 swap 分区。

重启系统，在 [loader(8)](http://www.FreeBSD.org/cgi/man.cgi?query=loader&sektion=8&manpath=freebsd-release-ports) 提示符下键入 **4** 启动到单用户模式。 然后输入以下的命令：

```
# `glabel label rootfs /dev/ad0s1a`
GEOM_LABEL: Label for provider /dev/ad0s1a is label/rootfs
# `glabel label var /dev/ad0s1d`
GEOM_LABEL: Label for provider /dev/ad0s1d is label/var
# `glabel label usr /dev/ad0s1f`
GEOM_LABEL: Label for provider /dev/ad0s1f is label/usr
# `glabel label tmp /dev/ad0s1e`
GEOM_LABEL: Label for provider /dev/ad0s1e is label/tmp
# `glabel label swap /dev/ad0s1b`
GEOM_LABEL: Label for provider /dev/ad0s1b is label/swap
# `exit`
```

系统加继续启动进入多用户模式。 在启动完毕后， 编辑 `/etc/fstab` 用各自的标签替换下常规的设备名。 最终 `/etc/fstab` 看起来差不多是这样的：

```
# Device                Mountpoint      FStype  Options         Dump    Pass#
/dev/label/swap         none            swap    sw              0       0
/dev/label/rootfs       /               ufs     rw              1       1
/dev/label/tmp          /tmp            ufs     rw              2       2
/dev/label/usr          /usr            ufs     rw              2       2
/dev/label/var          /var            ufs     rw              2       2
```

现在可以重启系统了。 如果一切顺利的话， 系统可以正常启动并且 `mount` 命令显示：

```
# `mount`
/dev/label/rootfs on / (ufs, local)
devfs on /dev (devfs, local)
/dev/label/tmp on /tmp (ufs, local, soft-updates)
/dev/label/usr on /usr (ufs, local, soft-updates)
/dev/label/var on /var (ufs, local, soft-updates)
```

从 FreeBSD 7.2 开始， [glabel(8)](http://www.FreeBSD.org/cgi/man.cgi?query=glabel&sektion=8&manpath=freebsd-release-ports) class 新增了一种用于 UFS 文件系统唯一标识符， `ufsid` 的标签支持。 这些标签可以在 `/dev/ufsid` 目录中找到， 它们会在系统引导时自动创建。 在 `/etc/fstab` 机制中， 也可以使用 `ufsid` 标签。 您可以使用 `glabel status` 命令来获得与文件系统对应的 `ufsid` 标签列表：

```
% `glabel status`
                  Name  Status  Components
ufsid/486b6fc38d330916     N/A  ad4s1d
ufsid/486b6fc16926168e     N/A  ad4s1f
```

在上面的例子中 `ad4s1d` 代表了 `/var` 文件系统， 而 `ad4s1f` 则代表了 `/usr` 文件系统。 您可以使用这些 `ufsid` 值来挂载它们， 在 `/etc/fstab` 中配置类似这样：

```
/dev/ufsid/486b6fc38d330916        /var        ufs        rw        2      2
/dev/ufsid/486b6fc16926168e        /usr        ufs        rw        2      2
```

所有包含了 `ufsid` 的标签都可以用这种方式挂载， 从而消除了需要手工创建永久性标签的麻烦， 而又能够提供提供与设备名无关的挂载方式的便利。

## 20.8. 通过 GEOM 实现 UFS 日志

随着 FreeBSD 7.0 的发布， 提供了长期为人们所期待的日志功能的实现。 这个实现采用了 GEOM 子系统， 可以很容易地使用 [gjournal(8)](http://www.FreeBSD.org/cgi/man.cgi?query=gjournal&sektion=8&manpath=freebsd-release-ports) 工具来进行配置。

日志是什么？ 日志的作用是保存文件系统事务的记录， 换言之， 完成一次完整的磁盘写入操作所需的变动， 这些记录会在元数据以及文件数据写盘之前， 写入到磁盘中。 这种事务日志可以在随后用于重放并完成文件系统事务， 以避免文件系统出现不一致的问题。

这种方法是另一种阻止文件系统丢失数据并发生不一致的方法。 与 Soft Updates 追踪并确保元数据更新顺序这种方法不同， 它会实际地将日志保存到指定为此项任务保留的磁盘空间上， 在某些情况下可全部存放到另外一块磁盘上。

与其他文件系统的日志实现不同， `gjournal` 采用的是基于块， 而不是作为文件系统的一部分的方式 - 它只是作为一种 GEOM 扩展实现。

如果希望启用 `gjournal`， FreeBSD 内核需要下列选项 - 这是 FreeBSD 7.0 以及更高版本系统上的默认配置：

```
options	UFS_GJOURNAL
```

如果使用日志的卷需要在启动的时候被挂载， 还需加载 `geom_journal.ko` 内核模块， 将以下这行加入 `/boot/loader.conf`：

```
geom_journal_load="YES"
```

这个功能也可被编译进一个定制的内核， 需在内核配置文件中加入以下这行：

```
options	GEOM_JOURNAL
```

现在， 可以为空闲的文件系统创建日志了。 对于新增的 SCSI 磁盘 `da4`， 具体的操作步骤为：

```
# `gjournal load`
# `gjournal label /dev/da4`
```

这样， 就会出现一个与 `/dev/da4` 设备节点对应的 `/dev/da4.journal` 设备节点。 接下来， 可以在这个设备上建立文件系统：

```
# `newfs -O 2 -J /dev/da4.journal`
```

这个命令将建立一个包含日志设备的 UFS2 文件系统。

然后就可以用 `mount` 命令来挂接设备了：

```
# `mount /dev/da4.journal /mnt`
```

### 注意:

当磁盘包含多个 slice 时， 每个 slice 上都会建立日志。 例如， 如果有 `ad4s1` 和 `ad4s2` 这两个 slice， 则 `gjournal` 会建立 `ad4s1.journal` 和 `ad4s2.journal`。

出于性能考虑， 可能会希望在其他磁盘上保存日志。 对于这类情形， 应该在启用日志的设备后面，给出日志提供者或存储设备。 在暨存的文件系统上， 可以用 `tunefs` 来启用日志； 不过， 在尝试修改文件系统之前， 您应对其进行备份。 多数情况下， 如果无法创建实际的日志， `gjournal` 就会失败， 并且不会防止由于不当使用 `tunefs` 而造成的数据丢失。

对于 FreeBSD 系统的启动磁盘使用日志也是可能的。 请参阅 Implementing UFS Journaling on a Desktop PC 以获得更多详细信息。

## 第 21 章 文件系统 Support

Written by Tom Rhodes.

## 21.1. 概述

文件系统对于任何操作系统来说都是一个不可缺的部分。 它们允许用户上载和存储文件，提供对数据的访问，当然， 是使硬盘能具有实际的用途。不同的操作系统通常都有一个共同的主要方面， 那就是它们原生的文件系统。在 FreeBSD 上这个文件系统通常被称为快速文件系统或者 FFS， 这是基于原来的 Unix™ 文件系统，通常也被称为 UFS。 这是 FreeBSD 用于在磁盘上访问数据的原生的文件系统。

FreeBSD 也支持数量繁多的不同的文件系统， 用于提供本地从其他操作系统上访问数据的支持， 那些就是指存放在本地挂载的 USB 存储设备，闪存设备和硬盘上的数据。还支持一些非原生的文件系统。 这些文件系统是在其他的操作系统上开发的，像 Linux® 的扩展文件系统 （EXT），和 Sun™ 的 Z 文件系统 （ZFS）。

FreeBSD 上对于各种文件系统的支持分成不同的层次。 一些要求加载内核模块，另外的可能要求安装一系列的工具。 这一章节旨在帮助 FreeBSD 用户在他们的系统上访问其他的文件系统， 由 Sun™ 的 Z 文件系统开始。

在阅读了这一章节之后，你将了解：

*   原生与被支持的文件系统之间的区别。

*   FreeBSD 支持哪些文件系统。

*   如何起用，配置，访问和使用非原生的文件系统。

在阅读这章以前，你应该：

*   了解 UNIX® 和 FreeBSD 基本知识 (第 4 章 *UNIX 基础*)。

*   熟悉基本的内核配置/编译方法 (第 9 章 *配置 FreeBSD 的内核*)。

*   熟悉在 FreeBSD 上安装第三方软件 (第 5 章 *安装应用程序: Packages 和 Ports*)。

*   熟悉 FreeBSD 上的磁盘，存储和设备名 (第 19 章 *存储*)。

## 21.2. Z 文件系统 (ZFS)

Z 文件系统是由 Sun™ 开发使用存储池方法的新技术。 这就是说只有在需要存储数据的时候空间才会被使用。 它也为保护数据最大完整性而设计的，支持数据快照， 多份拷贝和数据校验。增加了被称为 RAID-Z 的新的数据复制类型。RAID-Z 是种类似于 RAID5 类型, 但被设计成防止写入漏洞。

### 21.2.1. 调整 ZFS

ZFS 子系统需利用到大量的系统资源， 所以可能需要一些调校来为日常应用提供最大化的效能。 作为 FreeBSD 的一项试验性的特性，这可能在不久的将来有所变化； 无论如何，下面的这些步骤是我们推荐的：

#### 21.2.1.1. 内存

总共的系统内存至少应有 1GB，推荐 2GB 或者更多。 在此处所有的例子中，我们使用了 1GB 内存的系统并配合了一些恰当的调校。

有些人在少于 1GB 内存的环境有幸正常使用， 但是在这样有限的物理内存的条件下，当系统的负载很高时， FreeBSD 极有可能因于内存耗尽而崩溃。

#### 21.2.1.2. 内核配置

我们建议把未使用的驱动和选项从内核配置文件中去除。 既然大部份的驱动都有以模块的形式存在，它们就可以很容易的通过 `/boot/loader.conf` 加载。

i386™ 构架的用户应在内核配置文件中加入以下的选项， 重新编译内核并重启机器：

```
options 	KVA_PAGES=512
```

这个选项将扩展内核的地址空间， 因而允许 `vm.kvm_size` 能够超越 1 GB 的限制(PAE 为 2 GB)。 为了找出这个选项最合适的值， 把以兆(MB)为单位所需的地址空间除以 4 得到。 在这个例子中，`512` 则为 2 GB。

#### 21.2.1.3. Loader 可调参数

所有构架上 FreeBSD 都应该加大 `kmem` 地址空间。在有 1GB 物理内存的测试系统上，在 `/boot/loader.conf` 中加入如下的参数并且重启后通过了测试。

```
vm.kmem_size="330M"
vm.kmem_size_max="330M"
vfs.zfs.arc_max="40M"
vfs.zfs.vdev.cache.size="5M"
```

更多 ZFS 相关推荐调校的细节请参阅 `[`wiki.freebsd.org/ZFSTuningGuide`](http://wiki.freebsd.org/ZFSTuningGuide)`.

### 21.2.2. 使用 ZFS

FreeBSD 有一种启动机制能在系统初始化时挂载 ZFS 存储池。 可以通过以下的命令设置：

```
# `echo 'zfs_enable="YES"' >> /etc/rc.conf`
# `/etc/rc.d/zfs start`
```

这份文档剩余的部分假定系统中有 3 块 SCSI 磁盘可用， 它们的设备名分别为 `da0`， `da1` 和 `da2`。 IDE 硬件的用户可以使用 `ad` 代替 SCSI。

#### 21.2.2.1. 单个磁盘存储池

在单个磁盘上创建一个简单， 非冗余的 ZFS， 使用 `zpool` 命令：

```
# `zpool create example /dev/da0`
```

可以通过 `df` 的输出查看新的存储池：

```
# `df`
Filesystem  1K-blocks    Used    Avail Capacity  Mounted on
/dev/ad0s1a   2026030  235230  1628718    13%    /
devfs               1       1        0   100%    /dev
/dev/ad0s1d  54098308 1032846 48737598     2%    /usr
example      17547136       0 17547136     0%    /example
```

这份输出清楚的表明了 `example` 存储池不仅创建成功而且被 *挂载* 了。 我们能像访问普通的文件系统那样访问它， 就像以下例子中演示的那样，用户能够在上面创建文件并浏览：

```
# `cd /example`
# `ls`
# `touch testfile`
# `ls -al`
total 4
drwxr-xr-x   2 root  wheel    3 Aug 29 23:15 .
drwxr-xr-x  21 root  wheel  512 Aug 29 23:12 ..
-rw-r--r--   1 root  wheel    0 Aug 29 23:15 testfile
```

遗憾的是这个存储池并没有利用到 ZFS 的任何特性。 在这个存储池上创建一个文件系统，并启用压缩：

```
# `zfs create example/compressed`
# `zfs set compression=gzip example/compressed`
```

现在 `example/compressed` 是一个启用了压缩的 ZFS 文件系统了。 可以尝试复制一些大的文件到 `/example/compressed`。

使用这个命令可以禁用压缩：

```
# `zfs set compression=off example/compressed`
```

使用如下的命令卸载这个文件系统，并用 `df` 工具确认：

```
# `zfs umount example/compressed`
# `df`
Filesystem  1K-blocks    Used    Avail Capacity  Mounted on
/dev/ad0s1a   2026030  235232  1628716    13%    /
devfs               1       1        0   100%    /dev
/dev/ad0s1d  54098308 1032864 48737580     2%    /usr
example      17547008       0 17547008     0%    /example
```

重新挂在这个文件系统使之能被访问， 并用 `df` 确认：

```
# `zfs mount example/compressed`
# `df`
Filesystem         1K-blocks    Used    Avail Capacity  Mounted on
/dev/ad0s1a          2026030  235234  1628714    13%    /
devfs                      1       1        0   100%    /dev
/dev/ad0s1d         54098308 1032864 48737580     2%    /usr
example             17547008       0 17547008     0%    /example
example/compressed  17547008       0 17547008     0%    /example/compressed
```

存储池与文件系统也可通过 `mount` 的输出查看：

```
# `mount`
/dev/ad0s1a on / (ufs, local)
devfs on /dev (devfs, local)
/dev/ad0s1d on /usr (ufs, local, soft-updates)
example on /example (zfs, local)
example/data on /example/data (zfs, local)
example/compressed on /example/compressed (zfs, local)
```

正如前面所提到的，ZFS 文件系统， 在创建之后就能像普通的文件系统那样使用。然而， 还有很多其他的特性是可用的。在下面的例子中， 我们将创建一个新的文件系统，`data`。 并要在上面存储些重要的文件， 所以文件系统需要被设置成把每一个数据块都保存两份拷贝：

```
# `zfs create example/data`
# `zfs set copies=2 example/data`
```

现在可以再次使用 `df` 查看数据和空间的使用状况：

```
# `df`
Filesystem         1K-blocks    Used    Avail Capacity  Mounted on
/dev/ad0s1a          2026030  235234  1628714    13%    /
devfs                      1       1        0   100%    /dev
/dev/ad0s1d         54098308 1032864 48737580     2%    /usr
example             17547008       0 17547008     0%    /example
example/compressed  17547008       0 17547008     0%    /example/compressed
example/data        17547008       0 17547008     0%    /example/data
```

请注意存储池上的每一个文件系统都有着相同数量的可用空间。 这就是我们在这些例子中使用 `df` 的原因， 是为了文件系统都是从相同的存储池取得它们所需的空间。 ZFS 去掉了诸如卷和分区此类的概念， 并允许多个文件系统占用同一个存储池。 不再需要文件系统与存储池的时候能像这样销毁它们：

```
# `zfs destroy example/compressed`
# `zfs destroy example/data`
# `zpool destroy example`
```

磁盘无法避免的会坏掉和停止运转。 当这块磁盘坏掉的时候，上面的数据都将丢失。 一个避免因磁盘损坏而丢失数据的方法是使用 RAID。ZFS 在它的存储池设计中支持这样的特性， 这便是下一节将探讨的。

#### 21.2.2.2. ZFS RAID-Z

正如前文中所提到的，这一章节将假设存在 3 个 SCSI 设备， `da0`， `da1` 和 `da2` (或者 `ad0` 和超出此例使用了 IDE 磁盘)。 使用如下的命令创建一个 RAID-Z 存储池:

```
# `zpool create storage raidz da0 da1 da2`
```

### 注意:

Sun™ 推荐在一个 RAID-Z 配置中使用的磁盘数量为 3 至 9 块。 如果你要求在单独的一个存储池中使用 10 块或更多的磁盘， 请考虑分拆成更小 RAID-z 组。 如果你只有 2 块磁盘， 并仍然需要冗余， 请考虑使用 ZFS 的 mirror 特性。 更多细节请参考 [zpool(8)](http://www.FreeBSD.org/cgi/man.cgi?query=zpool&sektion=8&manpath=freebsd-release-ports) 手册页。

zpool `storage` 至此就创建好了。 可以如前文提到的那样使用 [mount(8)](http://www.FreeBSD.org/cgi/man.cgi?query=mount&sektion=8&manpath=freebsd-release-ports) 和 [df(1)](http://www.FreeBSD.org/cgi/man.cgi?query=df&sektion=1&manpath=freebsd-release-ports) 确认。 如需配给更多的磁盘设备则把它们加这个列表的后面。 在存储池上创建一个叫 `home` 的文件系统， 用户的文件最终都将被保存在上面：

```
# `zfs create storage/home`
```

像前文中提到的那样，用户的目录与文件也可启用压缩并保存多份拷贝， 可通过如下的命令完成：

```
# `zfs set copies=2 storage/home`
# `zfs set compression=gzip storage/home`
```

把用户的数据都拷贝过来并创建一个符号链接， 让他们开始使用这个新的目录：

```
# `cp -rp /home/* /storage/home`
# `rm -rf /home /usr/home`
# `ln -s /storage/home /home`
# `ln -s /storage/home /usr/home`
```

现在用户的数据应该都保存在新创建的 `/storage/home` 上了。 测试添加一个新用户并以这个身份登录。

尝试创建一个可日后用来回退的快照：

```
# `zfs snapshot storage/home@08-30-08`
```

请注意快照选项将只会抓取一个真实的文件系统， 而不是某个用户目录或文件。`@` 字符为文件系统名或卷名的分隔符。 当用户目录被损坏时，可用如下命令恢复：

```
# `zfs rollback storage/home@08-30-08`
```

获得所有可用快照的列表，可使用 `ls` 命令查看文件系统的 `.zfs/snapshot` 目录。例如，执行如下命令来查看之前抓取的快照：

```
# `ls /storage/home/.zfs/snapshot`
```

可以编写一个脚本来每月定期抓取用户数据的快照，久而久之， 快照可能消耗掉大量的磁盘空间。 之前创建的快照可用以下命令删除：

```
# `zfs destroy storage/home@08-30-08`
```

在所有这些测试之后，我们没有理由再把 `/store/home` 这样放置了。让它称为真正的 `/home` 文件系统：

```
# `zfs set mountpoint=/home storage/home`
```

使用 `df` 和 `mount` 命令将显示现在系统把我们的文件系统真正当作了 `/home`：

```
# `mount`
/dev/ad0s1a on / (ufs, local)
devfs on /dev (devfs, local)
/dev/ad0s1d on /usr (ufs, local, soft-updates)
storage on /storage (zfs, local)
storage/home on /home (zfs, local)
# `df`
Filesystem   1K-blocks    Used    Avail Capacity  Mounted on
/dev/ad0s1a    2026030  235240  1628708    13%    /
devfs                1       1        0   100%    /dev
/dev/ad0s1d   54098308 1032826 48737618     2%    /usr
storage       26320512       0 26320512     0%    /storage
storage/home  26320512       0 26320512     0%    /home
```

这样就基本完成了 RAID-Z 的配置了。使用夜间 [periodic(8)](http://www.FreeBSD.org/cgi/man.cgi?query=periodic&sektion=8&manpath=freebsd-release-ports) 获取有关文件系统创建之类的状态更新， 执行如下的命令：

```
# `echo 'daily_status_zfs_enable="YES"' >> /etc/periodic.conf`
```

#### 21.2.2.3. 修复 RAID-Z

每一种软 RAID 都有监测它们 `状态` 的方法。 ZFS 也不例外。 可以使用如下的命令查看 RAID-Z 设备：

```
# `zpool status -x`
```

如果所有的存储池处于健康状态并且一切正常的话， 将返回如下信息：

```
all pools are healthy
```

如果存在问题，可能是一个磁盘设备下线了， 那么返回的存储池的状态将看上去是类似这个样子的：

```
  pool: storage
 state: DEGRADED
status: One or more devices has been taken offline by the administrator.
	Sufficient replicas exist for the pool to continue functioning in a
	degraded state.
action: Online the device using 'zpool online' or replace the device with
	'zpool replace'.
 scrub: none requested
config:

	NAME        STATE     READ WRITE CKSUM
	storage     DEGRADED     0     0     0
	  raidz1    DEGRADED     0     0     0
	    da0     ONLINE       0     0     0
	    da1     OFFLINE      0     0     0
	    da2     ONLINE       0     0     0

errors: No known data errors
```

在这个例子中，这是由管理员把此设备下线后的状态。 可以使用如下的命令将磁盘下线：

```
# `zpool offline storage da1`
```

现在切断系统电源之后就可以替换下 `da1` 了。 当系统再次上线时，使用如下的命令替换磁盘：

```
# `zpool replace storage da1`
```

至此可用不带 `-x` 标志的命令再次检查状态：

```
# `zpool status storage`
 pool: storage
 state: ONLINE
 scrub: resilver completed with 0 errors on Sat Aug 30 19:44:11 2008
config:

	NAME        STATE     READ WRITE CKSUM
	storage     ONLINE       0     0     0
	  raidz1    ONLINE       0     0     0
	    da0     ONLINE       0     0     0
	    da1     ONLINE       0     0     0
	    da2     ONLINE       0     0     0

errors: No known data errors
```

在这个例子中，一切都显示正常。

#### 21.2.2.4. 数据校验

正如前面所提到的，ZFS 使用 `校验和`(checksum) 来检查存储数据的完整性。 这时在文件系统创建时自动启用的，可使用以下的命令禁用：

```
# `zfs set checksum=off storage/home`
```

这不是个明智的选择，因为校验和 不仅非常有用而且只需占用少量的存储空间。 并且启用它们也不会明显的消耗过多资源。 启用后就可以让 ZFS 使用校验和校验来检查数据的完整。 这个过程通常称为 “scrubbing”。 可以使用以下的命令检查 `storage` 存储池里数据的完整性：

```
# `zpool scrub storage`
```

这个过程需花费相当长的时间，取决于存储的数据量。 而且 I/O 非常密集， 所以在任何时间只能执行一个这样的操作。 在 scrub 完成之后，状态就会被更新， 可使用如下的命令查看：

```
# `zpool status storage`
 pool: storage
 state: ONLINE
 scrub: scrub completed with 0 errors on Sat Aug 30 19:57:37 2008
config:

	NAME        STATE     READ WRITE CKSUM
	storage     ONLINE       0     0     0
	  raidz1    ONLINE       0     0     0
	    da0     ONLINE       0     0     0
	    da1     ONLINE       0     0     0
	    da2     ONLINE       0     0     0

errors: No known data errors
```

这个例子中完成时间非常的清楚。 这个特性可以帮助你在很长的一段时间内确保数据的完整。

Z 文件系统有更多的选项，请参阅 [zfs(8)](http://www.FreeBSD.org/cgi/man.cgi?query=zfs&sektion=8&manpath=freebsd-release-ports) 和 [zpool(8)](http://www.FreeBSD.org/cgi/man.cgi?query=zpool&sektion=8&manpath=freebsd-release-ports) 手册页。

## 第 22 章 Vinum 卷管理程序

原作 Greg Lehey.

## 22.1. 概述

无论您有什么样的磁盘，总会有一些潜在问题：

*   它们可能容量太小。

*   它们可能速度太慢。

*   它们可能也太不可靠。

针对这些问题， 人们提出并实现了许多不同的解决方案。 为了应对这些问题， 一些用户采用了多个， 有时甚至是冗余的磁盘这类方法。 除了支持许多种不同的硬件 RAID 控制器之外， FreeBSD 的基本系统中包括了 Vinum 卷管理器， 它是一个用以实现虚拟磁盘驱动器的块设备。 *Vinum* 是一种称为 *卷管理器*， 或者说用于解决前面这三种问题的虚拟磁盘驱动程序。 Vinum 能够提供比传统磁盘系统更好的灵活性、 性能和可靠性， 并实现了能够单独或配合使用 RAID-0、 RAID-1 和 RAID-5 模型。

这一章对传统磁盘存储的潜在问题进行了简要说明，并介绍了 Vinum 卷管理器。

### 注意:

从 FreeBSD 5 开始， 对 Vinum 进行了重写， 以便使其符合 GEOM 架构 (第 20 章 *GEOM： 模块化磁盘变换框架*)， 同时保留其原有的设计创意、 术语， 以及保存在磁盘上的元数据格式。 这一重写的版本称为 *gvinum* (表示 *GEOM vinum*)。 接下来的文字中 *Vinum* 是一个抽象的名字， 通常并不具体指某一特定的实现。 新版本中所有的指令都应通过 `gvinum` 命令来操作， 而对应的内核模块的名字， 也由 `vinum.ko` 改为了 `geom_vinum.ko`， 而在 `/dev/vinum` 中的所有设备节点， 也改为放到了 `/dev/gvinum`。 从 FreeBSD 6 开始， 旧版的 Vinum 实现已不再提供。

## 22.2. 磁盘容量太小

磁盘越大，存储的数据也就越多。您经常会发现您需要 一个比您可使用的磁盘大得多的文件系统。 无可否认，这个问题 已经没有十年前那样严峻了，但它仍然存在。通过创建一个在许多 磁盘上存储数据的抽象设备，一些系统可以解决这个问题。

## 22.3. 访问瓶颈

现代系统经常需要用一个高度并发的方式来访问数据。 例如，巨大的 FTP 或 HTTP 服务器可以支持数以千计的并发会话， 可以有多个连到外部世界的 100 Mbit/s , 这远远地超过了绝大多数磁盘的数据传输速率。

当前的磁盘驱动器最高可以以 70 MB/s 的速度传输数据, 但这个值在一个有许多不受约束的进程访问一个驱动器的环境中变得并不重要， 它们可能只完成了这些值的一小部分。这样一种情况下，从磁盘子 系统的角度来看问题就更加有趣：重要的参数是在子系统上的负 荷，换句话说是传输占用了驱动器多少时间。

在任何磁盘传输中, 驱动器必须先寻道, 等待磁头访问第一个扇区, 然后执行传输. 这些动作看起来可能很细小: 我们不会感有任何中断。

假设传输 10 kB 数据， : 现在的高性能磁盘平均寻道时间是 3.5ms。 最快的驱动器可以旋转在 15,000 rpm，, 所以平均寻址时间为 2ms. 在 70 MB/s 的速度传输时, 数据的传输时间大约 150 μs, 几乎无法和寻址时间相比. 在这样一种情况下, 高效的传输也会降低到 1 MB/s 显然传输的快慢依赖与所传输数据的大小。

对于这个瓶颈的一般和明显的解决方法是采用 “多个磁盘”:而不是只使用一个大磁盘, 它使用几个比较小的磁盘联合起来形成一个大的磁盘. 每个磁盘都可以独立地进行传输数据，所以通过使用多个磁盘 大大提高了数据吞吐量。

当然，所要求的吞吐量的提高要比磁盘的数量小得多。 尽管每个驱动器并行传输数据，但没有办法确保请求能够平均 分配到每个驱动器上。不可避免一个驱动器的负载可能比另一个要高得多。

磁盘的负载平衡很大程度依赖于驱动器上数据的共享方式. 在下面的讨论中, 将磁盘存储想象成一个巨大的数据扇区，像一本书的页 那样用编号来设定地址. 最明显的方法是把虚拟磁盘分成许多连续的扇区组， 每个扇区大小就是独立的磁盘大小，用这种方法来存储数据， 就像把一本厚厚的书分成很多小的章节。 这个方法叫做 *串联* 它有一个优点就是磁盘不需要有任何特定的大小关系。 当访问到的虚拟磁盘根据它的地址空间来分布的时候， 它能工作得很好。 当访问集中在一个比较小的区域的时候，性能的提高没有显著的改进。 图 22.1 “串联组织” 举例说明了用串联组织的方式来分配存储单元的顺序。

![串联组织](img/vinum-concat.png)图 22.1. 串联组织

另外一种影射方法是把地址空间分布在比较小的容量相同的磁盘上， 从而能够在不同的设备上存储它们。例如，前 256 个扇区可能存储在第一 个磁盘上，接着的 256 个扇区存储在另一个磁盘上等等。 写满最后一个磁 盘后，进程会重复以前的工作，直到所有的磁盘被写满。这个影射叫做 *分段(striping)* 或者 RAID-0 [^([11])](#ftn.idp83151344). 分段要求很精确地寻址，通过多个磁盘进行数据传输的时候，它 可能会引起额外的 I/O 负载，但它也可能提供更多的连续负载。 图 22.2 “分段组织” 显示了用分段形式分配的存储单元的顺序。

![分段组织](img/vinum-striped.png)图 22.2. 分段组织

* * *

[^([11])](#idp83151344) RAID 代表*廉价冗余磁盘阵列 (Redundant Array of Inexpensive Disks)* 提供各种容错机制， 但后面这个术语可能会有些让人误解：它不提供冗余功能。

## 22.4. 数据的完整性

现时磁盘的最后一个问题是它们不太可靠。 虽然磁盘驱动器的可靠性在过去几年有了很大的提高， 但它们仍然是服务器中最容易损坏的核心组件。 当它们发生故障的时候， 结果可能是灾难性的： 替换坏的磁盘驱动器并恢复数据可能要花费几天时间。

解决这个问题的传统方法是建立 *镜象*， 在不同的物理硬件上对数据做两个副本。 根据 RAID 级别出现的时间顺序， 这个技术也被叫做 RAID 级别 1 或者 RAID-1。 任何写到卷的数据也会被写到镜象上， 所以可以从任何一个副本读取数据， 如果其中有一个出现故障， 数据也还可以从其他驱动器上访问到。

镜象有两个问题：

*   价格. 它需要两倍的存储容量。

*   性能影响。 写入操作必须在两个驱动器上执行，所以它们 花费两倍的带宽。读取数据并不会影响性能： 它们甚至看起来会更快。

一个可选的方案采用 *奇偶校验* 的方式， 用以实现 RAID 2、 3、 4 和 5。 这其中， RAID-5 是我们最感兴趣的。 在 Vinum 的实现中， 这是一个条带组织结构的变体， 其中， 每一个条带中都以一个专用的块， 来保存其它块的奇偶校验值。 这样， RAID-5 plex 除了在每个块中都包含了一个奇偶校验块之外， 实现 RAID-5 时也就和普通的条带 plex 一样了。 作为 RAID-5 的一项要求， 奇偶校验块在每一个条带中的顺序都是不同的。 数据块的编号， 决定了它的相对块号。

![RAID-5 的组织](img/vinum-raid5-org.png)图 22.3. RAID-5 的组织

与镜像相比， RAID-5 最显著的优势在于只需使用少得多的存储空间。 读取类似于条带式存储的组织， 但写入会慢得多， 大约仅相当于读性能的 25%。 如果一个驱动器失效， 则阵列仍然可以在降级的模式运行： 读取来自正常的驱动器数据的操作照常进行， 但读取失效的驱动器的数据， 则来自于余下驱动器上相关的计算结果。

## 22.5. Vinum 目标

为了解决这些问题，Vinum 提出了一个四层的目标结构：

*   最显著的目标是虚拟磁盘, 叫做 *卷(volume)*. 卷本质上与一个 UNIX 磁盘 驱动器有同样的属性，虽然它们是有些不太一样。它们没有大小的限制。

*   卷下面是 *plexes*, 每一个表示卷的所有地址空间。在层次结构中的这个水平能够提供 冗余功能。可以把 plex 想象成用一个镜象排列的方式组织起来的 独立磁盘，每个都包含同样的数据。

*   由于 Vinum 存在于 UNIX 磁盘存储框架中,所以它也可能 使用 UNIX 分区作为多个磁盘 plex 的组成部分, 但事实上这并不可靠:UNIX 磁盘只能有有限数量的分区。 取而代之，Vinum 把一个简单的 UNIX 分区 (the *drive*) 分解成叫做*subdisks*的相邻区域, 它可以使用这个 来为 plex 建立块。

*   Subdisks 位于 Vinum *驱动器上*, 当前的 UNIX 分区。Vinum 驱动器可以包含很多的 subdisks。 除了驱动器开始的一小块区域用来存储配置和描述信息以外，整个 驱动器都可以用于存储数据。

下面的章节描述了这些目标提供了 Vinum 所要求的功能的方法。

### 22.5.1. 卷的大小要求

在 Vinum 的配置中，Plex 可以把多个 subdisk 分布在所有的驱动上。 结果, 每个独立的驱动器的大小都不会限制 plex 的大小，从而不会限制卷的大小

### 22.5.2. 多余的数据存储

Vinum 通过给一个卷连上多个 plex 来完成镜象的功能。 每个 plex 是一个在一个卷中的数据的描述。一个卷可以包含一个 到八个 plex。

虽然一个 plex 描述了一个卷的所有数据，, 但可能描述的部分被物理地丢失了。可能是设计的问题 （没有为 plex 部分定义一个 subdisk）也可能是意外的故障 （由于驱动器的故障导致）。只要至少有一个 plex 能够为 卷的完全地址范围提供数据，卷就能够正常工作。

### 22.5.3. 性能问题

Vinum 在 plex 水平既执行串联也执行分段：

*   一个*串连的 plex*轮流使用 每个 subdisk 的地址空间。

*   一个 *分段的 plex* 在每个 subdisk 上 划分数据. Subdisk 必须是大小一样的，为了从一个连接的 plex 中 区分开它，必须至少有两个 subdisk。

### 22.5.4. 哪种 plex 组织更有效？

FreeBSD 10.3 提供的 Vinum 版本能实现两种 plex:

*   串联的 plex 更加灵活：它们可以包含任何数量的 subdisk， subdisk 也可能有不同的长度。Plex 可以通过添加额外的 subdisk 来得到扩展。 与分段 plex 不同， 它们需要的 CPU 时钟更少， 尽管 CPU 上的负载差异是不可测量的。 另一方面，它们的负载可能不平衡，一个磁盘可能负载很重， 而其他的可能很空闲。

*   分段(RAID-0) plexes 的最大优点是 它们减少了负载不平衡的情况: 通过选择一个最合适大小的分段 (大约是 256 kB), 您甚至可以在各个组成的驱动器上降低负载 . 这种方法的缺点是在 subdisk 上受到非常复杂的编码限制 : 它们必须是同样大小, 通过添加新的 subdisk 来扩展一个 plex 是非常复杂的,以至 Vinum 当前没有实现它. Vinum 利用一个额外 的，代价不高的限制：一个分段的 plex 必须有至少两个 subdisk， 否则， 它就无法区分连接的 plex 了。

表 22.1 “Vinum Plex 组织图” 总结一下每个 plex 组织 的优点和缺点.

表 22.1. Vinum Plex 组织图

| Plex 类型 | 最少 subdisks | 可否添加 subdisks | 尺寸相同 | 应用 |
| --- | --- | --- | --- | --- |
| 串联 | 1 | 可以 | 不必须 | 带有很大弹性和适中性能的大数据量存储。 |
| 分段 | 2 | 不可以 | 必须 | 大量并发访问时,具有较高性能。 |

## 22.6. 一些例子

Vinum 维护着一个描述本系统中对象的 *配置数据库*。 开始时， 用户可以在 [gvinum(8)](http://www.FreeBSD.org/cgi/man.cgi?query=gvinum&sektion=8&manpath=freebsd-release-ports) 工具来从若干配置文件生成配置数据库。 Vinum 在其控制的每个磁盘分区 (在 Vinum 中称为 *device*) 上都保存配置数据库的副本。 这一数据库在每次状态变化时均会更新， 因而重启每个 Vinum 对象时， 都能够恢复其状态。

### 22.6.1. 配置文件

配置文件描述了独立的 Vinum.一个简单卷的定义可能是这样的:

```
    drive a device /dev/da3h
    volume myvol
      plex org concat
        sd length 512m drive a
```

这个文件描述了四个 Vinum 目标:

*   *drive* 行描述了一个磁盘分区（驱动器） 和与下面的硬件相关的它的位置。它给出了一个符号名 *a*. 这个与设备名称分开的符号名允许 磁盘从一个位置移动到另一个位置而不会搞混。

*   *volume* 行描述了一个卷。 唯一的必须属性是名称，在这个例子中是 *myvol*.

*   *plex* 行定义了一个 plex。 唯一需要的参数是组织,在这个例子中是 *concat*. 没有名称是必然的: 系统自动通过添加 suffix *.p**x* 来从卷名称产生一个名字,这里的*x* 是在卷中的 plex 的编号。而这个 plex 将被 叫做*myvol.p0*。

*   *sd* 行描述了一个 subdisk。 最小的说明是存储 subdisk 的驱动器名称，和 subdisk 的长度。 对于 plex，没有名称也是必然的：系统自动通过添加 suffix *.s**x* 来分配源自 plex 的名称，这里 *x*是 plex 中 subdisk 的编号。 Vinum 给这个 subdisk 命名为*myvol.p0.s0*。

处理完这个文件后， [gvinum(8)](http://www.FreeBSD.org/cgi/man.cgi?query=gvinum&sektion=8&manpath=freebsd-release-ports) 会产生下面的输出：

```
      # gvinum -> `create config1`
      Configuration summary
      Drives:         1 (4 configured)
      Volumes:        1 (4 configured)
      Plexes:         1 (8 configured)
      Subdisks:       1 (16 configured)

	D a                     State: up       Device /dev/da3h        Avail: 2061/2573 MB (80%)

	V myvol                 State: up       Plexes:       1 Size:        512 MB

	P myvol.p0            C State: up       Subdisks:     1 Size:        512 MB

	S myvol.p0.s0           State: up       PO:        0  B Size:        512 MB
```

这些输出内容展示了 [gvinum(8)](http://www.FreeBSD.org/cgi/man.cgi?query=gvinum&sektion=8&manpath=freebsd-release-ports) 的简要列表格式。 在 图 22.4 “一个简单的 Vinum 卷” 中用图形展示了这个配置。

![一个简单的 Vinum 卷](img/vinum-simple-vol.png)图 22.4. 一个简单的 Vinum 卷

下面这个图显示了一个由按顺序排列的 subdisk 组成的 plex。 在这个小小的例子中，卷包含一个 plex，plex 包含一个 subdisk。

这个卷本身和普通的磁盘分区相比并没有什么特别的优越性， 它包含了一个 plex， 因此不是冗余的。 这个 plex 中包括了一个子磁盘， 因此这和从磁盘分区分配存储没什么两样。 接下来的几节， 将介绍一些更有用的配置方法。

### 22.6.2. 提高容错性： 镜像

卷的容错性可以通过镜像来提高。 在配置镜像卷时， 确保 plex 分布在不同的驱动器上十分重要， 这样一个驱动器坏掉时， 就不会同时影响两个 plex。 下面的配置将映射卷：

```
	drive b device /dev/da4h
	volume mirror
      plex org concat
        sd length 512m drive a
	  plex org concat
	    sd length 512m drive b
```

上面的例子中， 并不需要再次指定驱动器 *a*， 因为 Vinum 监控所有其配置数据库的对象。 完成定义之后， 配置如下所示：

```
	Drives:         2 (4 configured)
	Volumes:        2 (4 configured)
	Plexes:         3 (8 configured)
	Subdisks:       3 (16 configured)

	D a                     State: up       Device /dev/da3h        Avail: 1549/2573 MB (60%)
	D b                     State: up       Device /dev/da4h        Avail: 2061/2573 MB (80%)

    V myvol                 State: up       Plexes:       1 Size:        512 MB
    V mirror                State: up       Plexes:       2 Size:        512 MB

    P myvol.p0            C State: up       Subdisks:     1 Size:        512 MB
    P mirror.p0           C State: up       Subdisks:     1 Size:        512 MB
    P mirror.p1           C State: initializing     Subdisks:     1 Size:        512 MB

    S myvol.p0.s0           State: up       PO:        0  B Size:        512 MB
	S mirror.p0.s0          State: up       PO:        0  B Size:        512 MB
	S mirror.p1.s0          State: empty    PO:        0  B Size:        512 MB
```

图 22.5 “镜像 Vinum 卷” 以图形方式展示了其结构。

![镜像 Vinum 卷](img/vinum-mirrored-vol.png)图 22.5. 镜像 Vinum 卷

这个例子中， 每一个 plex 包含了完整的 512 MB 地址空间。 在前面的例子中， plex 则只包括一个子盘。

### 22.6.3. 优化性能

前面例子中的镜像卷要比没有镜像的卷具有更好的容灾能力， 但它的性能要差一些： 每一次写入卷时， 需要同时写到两个驱动器上， 因而也就需要更大的磁盘访问带宽。 如果希望非常好的性能， 则需要另外一种方式： 不做镜像， 而将数据分成条带放到尽可能多的、不同的磁盘上。 下面给出了一个跨越四个磁盘驱动器的 plex 卷：

```
	drive c device /dev/da5h
	drive d device /dev/da6h
	volume stripe
	plex org striped 512k
	  sd length 128m drive a
	  sd length 128m drive b
	  sd length 128m drive c
	  sd length 128m drive d
```

和之前类似， 并不需要定义 Vinum 已经知道的驱动器。 在完成定义之后， 将得到如下配置：

```
	Drives:         4 (4 configured)
	Volumes:        3 (4 configured)
	Plexes:         4 (8 configured)
	Subdisks:       7 (16 configured)

    D a                     State: up       Device /dev/da3h        Avail: 1421/2573 MB (55%)
    D b                     State: up       Device /dev/da4h        Avail: 1933/2573 MB (75%)
    D c                     State: up       Device /dev/da5h        Avail: 2445/2573 MB (95%)
    D d                     State: up       Device /dev/da6h        Avail: 2445/2573 MB (95%)

    V myvol                 State: up       Plexes:       1 Size:        512 MB
    V mirror                State: up       Plexes:       2 Size:        512 MB
    V striped               State: up       Plexes:       1 Size:        512 MB

    P myvol.p0            C State: up       Subdisks:     1 Size:        512 MB
    P mirror.p0           C State: up       Subdisks:     1 Size:        512 MB
    P mirror.p1           C State: initializing     Subdisks:     1 Size:        512 MB
    P striped.p1            State: up       Subdisks:     1 Size:        512 MB

    S myvol.p0.s0           State: up       PO:        0  B Size:        512 MB
    S mirror.p0.s0          State: up       PO:        0  B Size:        512 MB
    S mirror.p1.s0          State: empty    PO:        0  B Size:        512 MB
    S striped.p0.s0         State: up       PO:        0  B Size:        128 MB
    S striped.p0.s1         State: up       PO:      512 kB Size:        128 MB
    S striped.p0.s2         State: up       PO:     1024 kB Size:        128 MB
    S striped.p0.s3         State: up       PO:     1536 kB Size:        128 MB
```

![条带化的 Vinum 卷](img/vinum-striped-vol.png)图 22.6. 条带化的 Vinum 卷

这个卷在 图 22.6 “条带化的 Vinum 卷” 中给出。 条带的阴影部分， 表示在 plex 地址空间中的位置： 颜色最浅的在最前面， 而最深的在最后。

### 22.6.4. 高性能容在

如果硬件足够多， 也能够构建比标准 UNIX® 分区同时提高了容灾性和性能的卷。 典型的配置文件类似：

```
	volume raid10
      plex org striped 512k
        sd length 102480k drive a
        sd length 102480k drive b
        sd length 102480k drive c
        sd length 102480k drive d
        sd length 102480k drive e
      plex org striped 512k
        sd length 102480k drive c
        sd length 102480k drive d
        sd length 102480k drive e
        sd length 102480k drive a
        sd length 102480k drive b
```

第二个 plex 中的子盘和第一个 plex 中的错开了两个驱动器： 这能够帮助确保即使同时访问两个驱动器， 写操作也不会同时发生在同一个盘上。

图 22.7 “镜像并条带化的 Vinum 卷” 给出了该卷的结构。

![镜像并条带化的 Vinum 卷](img/vinum-raid10-vol.png)图 22.7. 镜像并条带化的 Vinum 卷

## 22.7. 对象命名

如前面所描述的那样， Vinum 会给 plex 和子盘指定默认的名字， 而这些名字也是可以定制的。 不推荐修改默认的名字： 使用允许给对象任意命名的 VERITAS 卷管理器的经验证明， 这一灵活性并没有带来太多的好处， 相反， 它很容易导致对象的混淆。

名字中可以包括任何非空白的字符， 但一般来说， 建议只使用字母、 数字和下划线。 卷、 plex， 以及子盘的名字， 可以包含最多 64 个字符， 而驱动器的名字， 则最长可以使用 32 个字符。

Vinum 对象会在 `/dev/gvinum` 之下生成设备节点。 前述的配置将使 Vinum 创建以下设备节点：

*   每个卷对应的设备项。 这些是 Vinum 使用的主要设备。 因此， 前述配置包括下列设备： `/dev/gvinum/myvol`、 `/dev/gvinum/mirror`、 `/dev/gvinum/striped`、 `/dev/gvinum/raid5` 以及 `/dev/gvinum/raid10`。

*   所有卷的直接项都存放在 `/dev/gvinum/` 中。

*   目录 `/dev/gvinum/plex`， 以及 `/dev/gvinum/sd` 中相应地存放了每个 plex 以及子盘的设备节点。

例如， 考虑下面的配置文件：

```
	drive drive1 device /dev/sd1h
	drive drive2 device /dev/sd2h
	drive drive3 device /dev/sd3h
	drive drive4 device /dev/sd4h
    volume s64 setupstate
      plex org striped 64k
        sd length 100m drive drive1
        sd length 100m drive drive2
        sd length 100m drive drive3
        sd length 100m drive drive4
```

处理这个文件之后， [gvinum(8)](http://www.FreeBSD.org/cgi/man.cgi?query=gvinum&sektion=8&manpath=freebsd-release-ports) 将在 `/dev/gvinum` 中建立下面的结构：

```
	drwxr-xr-x  2 root  wheel       512 Apr 13 16:46 plex
	crwxr-xr--  1 root  wheel   91,   2 Apr 13 16:46 s64
	drwxr-xr-x  2 root  wheel       512 Apr 13 16:46 sd

    /dev/vinum/plex:
    total 0
    crwxr-xr--  1 root  wheel   25, 0x10000002 Apr 13 16:46 s64.p0

    /dev/vinum/sd:
    total 0
    crwxr-xr--  1 root  wheel   91, 0x20000002 Apr 13 16:46 s64.p0.s0
    crwxr-xr--  1 root  wheel   91, 0x20100002 Apr 13 16:46 s64.p0.s1
    crwxr-xr--  1 root  wheel   91, 0x20200002 Apr 13 16:46 s64.p0.s2
    crwxr-xr--  1 root  wheel   91, 0x20300002 Apr 13 16:46 s64.p0.s3
```

虽然 plex 和子盘一般并不推荐指定名字， 但还是必须给 Vinum 驱动器命名。 这样， 当把驱动器转移到不同的地方时， 它仍然能够被自动地识别出来。 驱动器名最长可以包含 32 个字符。

### 22.7.1. 创建文件系统

对于系统而言， 卷和磁盘是一样的。 唯一的例外是， 与 UNIX® 驱动器不同， Vinum 并不对卷进行分区， 因而它也就不包含分区表。 这要求修改某些磁盘工具， 特别是 [newfs(8)](http://www.FreeBSD.org/cgi/man.cgi?query=newfs&sektion=8&manpath=freebsd-release-ports)， 它会试图将 Vinum 卷名当作分区标识。 例如， 磁盘驱动器的名字可能是 `/dev/ad0a` 或 `/dev/da2h`。 这些名字分别表达在第一个 (0) IDE (`ad`) 磁盘上的第一个分区 (`a`)， 以及第三个 (2) SCSI 磁盘 (`da`) 上的第八个分区 (`h`)。 而相比而言， Vinum 卷可能叫做 `/dev/gvinum/concat`， 这个名字和分区名没有什么关系。

要在这个卷上创建文件系统， 则需要使用 [newfs(8)](http://www.FreeBSD.org/cgi/man.cgi?query=newfs&sektion=8&manpath=freebsd-release-ports)：

```
# `newfs /dev/gvinum/concat`
```

## 22.8. 配置 Vinum

在 `GENERIC` 内核中， 并不包含 Vinum。 可以编译一个定制的包含 Vinum 的内核， 然而并不推荐这样做。 启动 Vinum 的标准方法， 是使用内核模块 (kld)。 甚至不需要使用 [kldload(8)](http://www.FreeBSD.org/cgi/man.cgi?query=kldload&sektion=8&manpath=freebsd-release-ports) 来启动 Vinum： 在启动 [gvinum(8)](http://www.FreeBSD.org/cgi/man.cgi?query=gvinum&sektion=8&manpath=freebsd-release-ports) 时， 它会检查这一模块是否已经加载， 如果没有， 则会自动地加载它。

### 22.8.1. 启动

Vinum 将配置信息， 采用与配置文件一样的形式来存放到磁盘分区上。 当从配置数据库中读取时， Vinum 会识别一系列在配置文件中不可用的关键字。 例如， 磁盘配置文件可能包含下面的文字：

```
volume myvol state up
volume bigraid state down
plex name myvol.p0 state up org concat vol myvol
plex name myvol.p1 state up org concat vol myvol
plex name myvol.p2 state init org striped 512b vol myvol
plex name bigraid.p0 state initializing org raid5 512b vol bigraid
sd name myvol.p0.s0 drive a plex myvol.p0 state up len 1048576b driveoffset 265b plexoffset 0b
sd name myvol.p0.s1 drive b plex myvol.p0 state up len 1048576b driveoffset 265b plexoffset 1048576b
sd name myvol.p1.s0 drive c plex myvol.p1 state up len 1048576b driveoffset 265b plexoffset 0b
sd name myvol.p1.s1 drive d plex myvol.p1 state up len 1048576b driveoffset 265b plexoffset 1048576b
sd name myvol.p2.s0 drive a plex myvol.p2 state init len 524288b driveoffset 1048841b plexoffset 0b
sd name myvol.p2.s1 drive b plex myvol.p2 state init len 524288b driveoffset 1048841b plexoffset 524288b
sd name myvol.p2.s2 drive c plex myvol.p2 state init len 524288b driveoffset 1048841b plexoffset 1048576b
sd name myvol.p2.s3 drive d plex myvol.p2 state init len 524288b driveoffset 1048841b plexoffset 1572864b
sd name bigraid.p0.s0 drive a plex bigraid.p0 state initializing len 4194304b driveoff set 1573129b plexoffset 0b
sd name bigraid.p0.s1 drive b plex bigraid.p0 state initializing len 4194304b driveoff set 1573129b plexoffset 4194304b
sd name bigraid.p0.s2 drive c plex bigraid.p0 state initializing len 4194304b driveoff set 1573129b plexoffset 8388608b
sd name bigraid.p0.s3 drive d plex bigraid.p0 state initializing len 4194304b driveoff set 1573129b plexoffset 12582912b
sd name bigraid.p0.s4 drive e plex bigraid.p0 state initializing len 4194304b driveoff set 1573129b plexoffset 16777216b
```

这里最明显的区别是， 指定了配置的位置信息、名称 (这些在配置文件中还是可用的， 但不鼓励用户自行指定) 以及状态信息 (这是用户不能指定的)。 Vinum 并不在配置信息中保存关于驱动器的信息： 它会扫描已经配置的磁盘驱动器上包含 Vinum 标识的分区。 这使得 Vinum 能够在 UNIX® 驱动器被指定了不同的 ID 时也能够正确识别它们。

#### 22.8.1.1. 自动启动

*Gvinum* 在通过 [loader.conf(5)](http://www.FreeBSD.org/cgi/man.cgi?query=loader.conf&sektion=5&manpath=freebsd-release-ports) 加载了内核模块之后就能自动启动。 在启动时加载 *Gvinum* 模块， 需在 `/boot/loader.conf` 中加入 `geom_vinum_load="YES"`。

当使用 `gvinum start` 命令来启动 Vinum 时， Vinum 会从某一个 Vinum 驱动器中读取配置数据库。 正常情况下， 每个驱动器上都包含了同样的配置数据库副本， 因此从哪个驱动器上读取是无所谓的。 但是， 在系统崩溃之后， Vinum 就必须检测哪一个驱动器上的配置数据库是最新的， 并从上面读取配置。 如果需要， 它会更新其它驱动器上的配置。

## 22.9. 使用 Vinum 作为根文件系统

如果文件系统使用完全镜像的 Vinum 配置， 有时也会希望根文件系统也作了镜像。 这种配置要比镜像其它文件系统麻烦一些， 因为：

*   根文件系统在引导过程中很早的时候就必须处于可用状态， 因此 Vinum 的基础设施在这一时刻就应该可用了。

*   包含根文件系统的卷， 同时也保存了系统的引导程序和内核， 因此它们必须能够被宿主系统的内建工具 (例如 PC 机的 BIOS) 识别， 而通常是没办法让它们了解 Vinum 的细节的。

下面几节中， 术语 “根卷” 标识包含根文件系统的 Vinum 卷。 把这个卷命名为 `"root"` 可能是个不错的主意， 不过从技术上说， 并不严格地要求这样做。 不过， 接下来的命令例子都使用这个名字。

### 22.9.1. 及早启动 Vinum 以适应对根文件系统的要求

有许多关于它的尺度：

*   Vinum 必须在启动时可以被内核使用。 因此， 在 第 22.8.1.1 节 “自动启动” 中所介绍的方法， 也就无法适应这一任务的需要了。 在接下来的配置中， 也 *不能* 设置 `start_vinum` 参数。 第一种方法是通过将 Vinum 静态联编到内核中来实现， 这样， 它就在任何时候都可用了， 虽然一般并不需要这样。 另一种方法是通过 `/boot/loader` (第 13.3.3 节 “第三阶段，`/boot/loader`”) 来尽早加载 vinum 内核模块， 这一操作发生在内核加载之前。 这可以通过将下面的配置：

    ```
    geom_vinum_load="YES"
    ```

    加入到 `/boot/loader.conf` 文件中来实现。

*   对 *Gvinum* 而言， 所有的启动过程都是在内核模块加载时自动进行的， 因此上面的操作， 也就是所要进行的全部工作了。

### 22.9.2. 让基于 Vinum 的卷在引导时可以访问

因为目前的 FreeBSD 引导程序只有 7.5 KB 的代码， 并且已经承担了从 UFS 文件系统中读取文件 (例如 `/boot/loader`) 的重任， 因此完全没有办法再让它去分析 Vinum 配置数据中的 Vinum 结构， 并找到引导卷本身的信息。 因此， 需要一些技巧来为引导代码提供标准的 `"a"` 分区， 而它则包含了根文件系统。

要让这些得以实现， 根卷需要满足下面的条件：

*   根卷不能是条带卷或 RAID-5 卷。

*   根卷 plex 不能包含连接的子盘。

需要说明的是， 使用多个 plex， 每个 plex 都复制一份根文件系统的副本， 是需要而且是可行的。 然而， 引导过程只能使用这些副本中的一个来引导系统， 直到内核最终自行挂接根文件系统为止。 这些 plex 中的每个子盘， 在这之后会有它们自己的 `"a"` 分区， 以表达每一个可以引导的设备。 每一个 `"a"` 分区， 尽管并不需要和其它包含根卷的 plex 处于各自驱动器的同一位置。 但是， 这样创建 Vinum 卷使得镜像卷相互对称， 从而能够避免了混淆。

为了创建每一个根卷的 `"a"` 分区， 需要完成下面的操作：

1.  使用下面的命令来了解根卷成员子盘的位置 (从设备开始的偏移量) 和尺寸：

    ```
    # `gvinum l -rv root`
    ```

    需要注意的是， Vinum 偏移量和尺寸的单位是字节。 它们必须是 512 的整数倍， 才能得到 `bsdlabel` 命令所需的块号。

2.  在每一个根卷成员设备上， 执行命令：

    ```
    # `bsdlabel -e devname`
    ```

    这其中， 对于没有 slice (也就是 fdisk) 表的磁盘， *`devname`* 必须是磁盘的名字 (例如 `da0`)， 或者是 slice 的名字 (例如 `ad0s1`)。

    如果设备上已经有了 `"a"` 分区 (比如说， 包含 Vinum 之前的根文件系统)， 则应改为其它的名字， 以便继续访问 (如果需要的话)， 但它并不会继续用于启动系统。 注意， 活动的分区 (类似正挂接的根文件系统) 不能被改名， 因此， 要完成这项工作， 必须从 “Fixit” 盘启动， 或者分两步操作， 并 (在镜像情形中) 首先操作那些非引导盘。

    然后， 设备上 Vinum 分区的偏移 (如果有的话) 必须加到这个设备上根卷对应的子盘上。 其结果值， 将成为新的 `"a"` 分区的 `"offset"` 值。 这个分区的 `"size"` 值， 可以根据前面的配置计算得出。 `"fstype"` 应该是 `4.2BSD`。 `"fsize"`、 `"bsize"`， 以及 `"cpg"` 值， 则应与文件系统的实际情况匹配， 尽管在配置 Vinum 时并不重要。

    这样， 新的 `"a"` 分区， 将创建并覆盖这一设备上的 Vinum 分区的范围。 注意， `bsdlabel` 只有在 Vinum 分区的 fstype 被标记为 `"vinum"` 时， 才允许这样做。

3.  这就成了！ 所有的 `"a"` 分区现在都已存在， 而且是根卷的一份副本。 强烈建议您再次验证其结果， 方法是：

    ```
    # `fsck -n /dev/devnamea`
    ```

务必注意， 所有包含控制信息的文件， 都必须放到 Vinum 卷上的根文件系统。 在启动新的 Vinum 根卷时， 它们可能和实际在用的根文件系统不匹配。 因此， `/etc/fstab` 和 `/boot/loader.conf` 这两个文件需要特别地注意。

在下次重启时， 引导程序需要从新的基于 Vinum 的根文件系统中获取适当的控制信息， 并据此工作。 在内核初始化过程的结尾部分， 在所有的设备都被宣示之后， 如果显示了下面的信息， 则表示配置成功：

```
Mounting root from ufs:/dev/gvinum/root
```

### 22.9.3. 基于 Vinum 的根文件系统的配置范例

在 Vinum 根卷配置好之后， `gvinum l -rv root` 的输出可能类似下面的样子：

```
...
Subdisk root.p0.s0:
		Size:        125829120 bytes (120 MB)
		State: up
		Plex root.p0 at offset 0 (0  B)
		Drive disk0 (/dev/da0h) at offset 135680 (132 kB)

Subdisk root.p1.s0:
		Size:        125829120 bytes (120 MB)
		State: up
		Plex root.p1 at offset 0 (0  B)
		Drive disk1 (/dev/da1h) at offset 135680 (132 kB)

```

需要注意的值是 `135680`， 也就是偏移量 (相对于 `/dev/da0h` 分区)。 这相当于 `bsdlabel` 记法中的 265 个 512-字节的磁盘块。 类似地， 根卷的尺寸是 245760 个 512-字节的磁盘块。 `/dev/da1h` 中， 包含了根卷的第二个副本， 采用了同样的配置。

这些设备的 bsdlabel 类似下面的样子：

```
...
8 partitions:
#        size   offset    fstype   [fsize bsize bps/cpg]
  a:   245760      281    4.2BSD     2048 16384     0   # (Cyl.    0*- 15*)
  c: 71771688        0    unused        0     0         # (Cyl.    0 - 4467*)
  h: 71771672       16     vinum                        # (Cyl.    0*- 4467*)

```

可以看到， 伪装的 `"a"` 分区的 `"size"` 参数和前面的一样， 而 `"offset"` 参数则是 Vinum 分区 `"h"`， 以及设备中这一分区 (或 slice) 的偏移量之和。 这是一种典型的配置， 它能够避免在 第 22.9.4.3 节 “无法启动， 引导程序发生 panic” 中介绍的问题。 此外， 我们也看到整个 `"a"` 分区完全处于设备上包含了 Vinum 数据的 `"h"` 分区之中。

注意， 在上面的配置中， 整个设备都是 Vinum 专用的， 而且没有留下 Vinum 之前的根分区， 因为它永久性地成为了新建的 Vinum 配置中的一个子盘。

### 22.9.4. 故障排除

如果遇到了问题， 则需要从中恢复的办法。 下面列出了一些常见的缺陷， 及其解决方法。

#### 22.9.4.1. 系统的引导程序加载了， 但无法启动

如果由于某种原因系统不再继续启动， 引导程序可以在 10-秒 倒计时的时候， 按 **space** 键来停止。 加载器变量 (例如 `vinum.autostart`) 可以通过使用 `show` 命令来查看， 并使用 `set` 和 `unset` 命令来设置。

如果遇到的问题是由于 Vinum 的内核模块没有列入预加载的列表， 而没有正确加载， 则简单使用 `load geom_vinum` 会有所帮助。

此后， 可以使用 `boot -as` 来继续启动过程。 选项 `-as` 会要求内核询问所挂接的根文件系统 (`-a`)， 并使引导过程在单用户模式停止 (`-s`)， 此时根文件系统是以只读方式挂接的。 这样， 即使只挂接了多 plex 卷中的一个 plex， 也不会引致 plex 之间数据不一致的问题。

当提示输入要挂接的根文件系统时， 可以输入任何一个包含根文件系统的设备。 如果正确地配置了 `/etc/fstab`， 则默认的应该是类似 `ufs:/dev/gvinum/root`。 一般可以使用类似 `ufs:da0d` 这样的设备来代替它， 因为它通常包括了 Vinum 之前的根文件系统。 需要注意的是， 如果在这里输入了 `"a"` 分区， 则它可能表达的实际上是 Vinum 根设备的一个子盘， 而在镜像式配置中， 这只会挂接镜像的根设备中的一个。 如果之后将这个文件系统以读写方式挂接， 则需要从 Vinum 根卷中删去其他的 plex， 否则这些卷中可能会包含不一致的数据。

#### 22.9.4.2. 只加载了主引导程序

如果 `/boot/loader` 加载失败， 而主引导程序加载正常 (在启动时， 屏幕最左边一列有一个旋转的线)， 则可以尝试在此时中断主引导程序的过程， 方法是按 **space** 键。 这将在引导的第二阶段暂停， 具体可以参见 第 13.3.2 节 “第一阶段，`/boot/boot1`，和第二阶段， `/boot/boot2`”。 此时， 可以尝试从另一个分区， 例如原先包含根文件系统， 并不再叫作 `"a"` 的那个分区， 启动。

#### 22.9.4.3. 无法启动， 引导程序发生 panic

这种情况一般是由于 Vinum 安装过程中破坏了引导程序造成的。 不幸的是， Vinum 目前只在分区开始的地方保留了 4 KB 的空间， 之后就开始写 Vinum 头信息了。 然而， 目前第一阶段和第二阶段的引导程序， 加上 bsdlabel 嵌入的内容则需要 8 KB。 因此， 如果 Vinum 分区从偏移量 0 开始， 而这个 slice 或磁盘能够启动， 则 Vinum 的安装将毁掉引导程序。

类似地， 如果从上述情形中恢复， 例如， 从 “Fixit” 盘启动， 并通过 `bsdlabel -B` 按照 第 13.3.2 节 “第一阶段，`/boot/boot1`，和第二阶段， `/boot/boot2`” 中介绍的方法来恢复引导程序， 则引导程序会覆盖掉 Vinum 头， 这样 Vinum 也就找不到它的磁盘了。 尽管这并不会真的毁掉 Vinum 的配置数据， 或者 Vinum 卷上的数据， 并且可以通过输入一模一样的 Vinum 配置数据来恢复， 但从这种状况中完全恢复是非常困难的。 要真正解决问题， 必须将整个 Vinum 分区向后移动至少 4 KB， 以便使 Vinum 头和系统的引导程序不再冲突。

## 第 23 章 虚拟化

原作 Murray Stokely.

## 23.1. 概述

虚拟化软件能够让同一台机器上同时运行多个操作系统。 在 PC 上， 这种系统通常由一个运行虚拟化软件的宿主操作系统， 以及一系列客户操作系统组成。

读完这章， 您将了解：

*   宿主操作系统与客户操作系统的区别。

*   如何在采用 Intel® 处理器的 Apple® Macintosh® 计算机上安装 FreeBSD。

*   如何在 Microsoft® Windows® 以 Virtual PC 安装 FreeBSD。

*   如何针对虚拟化环境对 FreeBSD 系统进行性能调优。

在阅读这章之前， 您应：

*   理解 UNIX® 和 FreeBSD 的基础知识 (第 4 章 *UNIX 基础*)。

*   了解如何安装 FreeBSD (第 2 章 *安装 FreeBSD*)。

*   了解如何配置网络连接 (第 32 章 *高级网络*)。

*   了解如何安装第三方软件 (第 5 章 *安装应用程序: Packages 和 Ports*).

## 23.2. 作为客户 OS 的 FreeBSD

### 23.2.1. MacOS 上的 Parallels

为 Mac® 设计的 Parallels Desktop 是一种可用于采用 Intel® 处理器， 并运行 Mac OS® 10.4.6 或更高版本的 Apple® Mac® 计算机的商业软件。 它为 FreeBSD 系统提供了完整的支持。 在 Mac OS® X 上安装了这个软件之后， 用户需要配置虚拟机并安装所需的客户操作系统。

#### 23.2.1.1. 在 Parallels/Mac OS® X 上安装 FreeBSD

在 Mac OS® X/Parallels 上安装 FreeBSD 的第一步是创建一个新的虚拟机。 在系统提示选择客户 OS 类型 (Guest OS Type) 时选择 FreeBSD， 并根据您使用 FreeBSD 虚拟实例的需要分配磁盘和内存：

![](img/parallels-freebsd1.png)

对多数在 Parallels 上使用 FreeBSD 的情形而言， 4GB 磁盘空间和 512MB 的 RAM 就够用了：

![](img/parallels-freebsd2.png)![](img/parallels-freebsd3.png)![](img/parallels-freebsd4.png)![](img/parallels-freebsd5.png)

选择使用的网络和网卡类型：

![](img/parallels-freebsd6.png)![](img/parallels-freebsd7.png)

保存并完成配置：

![](img/parallels-freebsd8.png)![](img/parallels-freebsd9.png)

在创建了 FreeBSD 虚拟机之后， 还需要在其中安装 FreeBSD。 最好的做法是使用官方的 FreeBSD CDROM 或从官方 FTP 站点下载的 ISO 镜像来完成这个任务。 如果您的本地 Mac® 文件系统中有 ISO 映像文件， 或您的 Mac® 的 CD 驱动器中有 CDROM， 就可以在 FreeBSD Parallels 窗口的右下角点击光盘图标。 之后， 系统将给出一个窗口， 供您完成将虚拟机中的 CDROM 驱动器连接到本地的 ISO 文件或真正的 CDROM 驱动器上。

![](img/parallels-freebsd11.png)

在完成了将 CDROM 与您的安装源完成关联之后， 就可以按重启 (reboot) 图标来重启 FreeBSD 虚拟机了。 Parallels 将配合一个特殊的 BIOS 启动， 后者能够像普通的 BIOS 一样检查系统中是否有 CDROM 驱动器。

![](img/parallels-freebsd10.png)

此时， 它就能够找到 FreeBSD 安装介质并开始 第 2 章 *安装 FreeBSD* 中所介绍的标准的基于 sysinstall 安装的过程。

![](img/parallels-freebsd12.png)

此时您可以安装 X11， 但暂时不要对它进行配置。 在完成安装之后， 重启并进入新安装的 FreeBSD 虚拟机。

![](img/parallels-freebsd13.png)

#### 23.2.1.2. 在 Mac OS® X/Parallels 上配置 FreeBSD

在您将 FreeBSD 安装到 Mac OS® X 的 Parallels 上之后， 还需要进行一系列的配置， 以便为系统的虚拟化操作进行优化。

1.  **配置引导加载器变量**

    最重要的一步是通过调低 `kern.hz` 变量来降低 Parallels 环境中的 FreeBSD 对 CPU 的使用。 这可以通过在 `/boot/loader.conf` 中增加下述配置来完成：

    ```
    kern.hz=100
    ```

    如果不使用这个配置， 闲置的 FreeBSD Parallels 客户 OS 会在单处理器的 iMac® 上使用大约 15% 的 CPU。 如此修改之后， 空闲时的使用量就减少到大约 5% 了。

2.  **创建新的内核配置文件**

    您可以删去全部 SCSI、 FireWire， 以及 USB 设备驱动程序。 Parallels 提供了一个由 [ed(4)](http://www.FreeBSD.org/cgi/man.cgi?query=ed&sektion=4&manpath=freebsd-release-ports) 驱动的虚拟网卡， 因此， 除了 [ed(4)](http://www.FreeBSD.org/cgi/man.cgi?query=ed&sektion=4&manpath=freebsd-release-ports) 和 [miibus(4)](http://www.FreeBSD.org/cgi/man.cgi?query=miibus&sektion=4&manpath=freebsd-release-ports) 之外的其他网络接口驱动都可以从内核中删去。

3.  **配置网络**

    最基本的网络配置， 是通过使用 DHCP 来将您的虚拟机与宿主 Mac® 接入同一个局域网。 这可以通过在 `/etc/rc.conf` 中加入 `ifconfig_ed0="DHCP"` 来完成。 更高级一些的网络配置方法， 请参见 第 32 章 *高级网络* 中的介绍。

### 23.2.2. Windows® 上的 Virtual PC

Virtual PC 是 Microsoft® 上的 Windows® 软件产品， 可以免费下载使用。 相关系统要求，请参阅 [system requirements](http://www.microsoft.com/windows/downloads/virtualpc/sysreq.mspx) 说明。 在 Microsoft® Windows® 装完 Virtual PC 之后， 必须针对所安装的虚拟机器来做相应设定。

#### 23.2.2.1. 在 Virtual PC/Microsoft® Windows® 上安装 FreeBSD

在 Microsoft® Windows®/Virtual PC 上安装 FreeBSD 的第一步是新增虚拟器。 如下所示，在提示向导中请选择 Create a virtual machine：

![](img/virtualpc-freebsd1.png)![](img/virtualpc-freebsd2.png)

然后在 Operating system 处选 Other：

![](img/virtualpc-freebsd3.png)

并依据自身需求来规划硬盘容量和内存的分配。对大多数在 Virtual PC 使用 FreeBSD 的情况而言， 大约 4GB 的硬盘空间以及 512MB 的内存就够用了。

![](img/virtualpc-freebsd4.png)![](img/virtualpc-freebsd5.png)

保存并完成配置：

![](img/virtualpc-freebsd6.png)

接下来选择新建的 FreeBSD 虚拟机器，并单击 Settings， 以设定网络种类以及网卡：

![](img/virtualpc-freebsd7.png)![](img/virtualpc-freebsd8.png)

在新建 FreeBSD 虚拟机器以后， 就可以继续以其安装 FreeBSD。 安装方面， 比较好的作法是使用官方的 FreeBSD 光盘或从官方 FTP 站下载 ISO 镜像。 若您的 Windows® 系统 内已有该 ISO 镜像， 那么就可以在 FreeBSD 虚拟机器上双击， 以开始启动。 接着在 Virtual PC 窗口内按 CD 再按 Capture ISO Image...。 接着出现一个对话框， 可以把虚拟机器内的光驱设定到该 ISO 镜像， 或者是真实的光驱。

![](img/virtualpc-freebsd9.png)![](img/virtualpc-freebsd10.png)

设好光盘来源之后，就可以重新开机， 也就是先按 Action 再按 Reset 即可。 Virtual PC 会以特殊 BIOS 开机， 并与普通 BIOS 一样会先检查是否有光盘驱动器。

![](img/virtualpc-freebsd11.png)

此时， 它会找到 FreeBSD 安装光盘， 并开始在 第 2 章 *安装 FreeBSD* 内所介绍的 sysinstall 安装过程。 这时候也可以顺便安装 X11， 但不要进行相关设定。

![](img/virtualpc-freebsd12.png)

完成安装之后， 记得把安装光盘或者 ISO 镜像退出。 最后， 把装好的 FreeBSD 虚拟机器重新开机即可。

![](img/virtualpc-freebsd13.png)

#### 23.2.2.2. 调整 Microsoft® Windows®/Virtual PC 上的 FreeBSD

在 Microsoft® Windows® 上以 Virtual PC 装好 FreeBSD 后， 还需要做一些设定步骤， 以便将虚拟机内的 FreeBSD 最佳化。

1.  **设定 boot loader 参数**

    最重要的步骤乃是藉由调降 `kern.hz` 来降低 Virtual PC 环境内 FreeBSD 的 CPU 占用率。 在 `/boot/loader.conf` 内加上下列设定即可：

    ```
    kern.hz=100
    ```

    若不作这设定， 那么光是 idle 状态的 FreeBSD Virtual PC guest OS 就会在单一处理器的电脑上大约有 40% 的 CPU 占用率。 作了上述修改之后, 占用率大约会降至 3%。

2.  **建立一个新的内核配置文件**

    可以放心把所有的 SCSI， FireWire 和 USB 设备驱动都移除。 Virtual PC 有提供 [de(4)](http://www.FreeBSD.org/cgi/man.cgi?query=de&sektion=4&manpath=freebsd-release-ports) 的虚拟网卡， 因此除了 [de(4)](http://www.FreeBSD.org/cgi/man.cgi?query=de&sektion=4&manpath=freebsd-release-ports) 以及 [miibus(4)](http://www.FreeBSD.org/cgi/man.cgi?query=miibus&sektion=4&manpath=freebsd-release-ports) 以外其他的网卡也都可以从内核的配置文件中移除。

3.  **设定网络**

    可以给虚拟机器简单得使用 DHCP 来设定与 host (Microsoft® Windows®) 相同的本地网络环境， 只要在 `/etc/rc.conf` 加上 `ifconfig_de0="DHCP"` 即可完成。 其他的高级网络设置， 可参阅 第 32 章 *高级网络*.

### 23.2.3. 运行于 MacOS 的 VMware

Mac® 版本的 VMware Fusion 是一个商业软件，运行在基于 Intel® 的 Apple® Mac® 计算机的 Mac OS® 10.4.9 或更版本的操作系统上。 FreeBSD 是一个完全被支持的客户操作系统。 在 Mac OS® X 上安装了 VMware Fusion 之后， 用户就可以着手配置一个虚拟机器并安装客户操作系统。

#### 23.2.3.1. 在 VMware/Mac OS® X 上安装 FreeBSD

第一步是运行 VMware Fusion， 虚拟机器库将被装载。 单击 "New" 创建 VM：

![](img/vmware-freebsd01.png)

New Virtual Machine Assistant 将被运行来帮助你创建 VM， 单击 Continue 继续：

![](img/vmware-freebsd02.png)

在 Operatiing System 项选择 Other，Version 项可选 FreeBSD 或 FreeBSD 64-bit。

![](img/vmware-freebsd03.png)

选一个你想要的 VM 镜像名字和存储的目录位置。

![](img/vmware-freebsd04.png)

选择 VM 虚拟硬盘的大小：

![](img/vmware-freebsd05.png)

选择安装 VM 的方式， 从一个 ISO 镜像或一张 CD 安装：

![](img/vmware-freebsd06.png)

一旦你点击了 Finish， VM 就会启动了：

![](img/vmware-freebsd07.png)

以你通常的方式安装 FreeBSD 或者参照 第 2 章 *安装 FreeBSD* 中的步骤：

![](img/vmware-freebsd08.png)

安装完成之后，你就可以修改一些 VM 的设定，比如内存大小：

### 注意:

在 VM 运行的时候，VM 系统硬件的设置是无法修改的。

![](img/vmware-freebsd09.png)

配置 VM 的 CPU 数量：

![](img/vmware-freebsd10.png)

CD-ROM 设备的状态。通常当你不在需要 CDROM/ISO 的时候可以切断他们跟 VM 的连接。

![](img/vmware-freebsd11.png)

最后一项需要修改的是 VM 与网络连接的方式。 如果你希望除了宿主以外的机器也能连接到 VM， 请选择 Connect directly to the physical network (Bridged)。选择 Share the host's internet connection (NAT) 的话， VM 可以连接上网络，但是不能从外面访问。

![](img/vmware-freebsd12.png)

在你修改完设定之后，就可以从新安装的 FreeBSD 虚拟机器启动了。

#### 23.2.3.2. 配置运行于 Mac OS® X/VMware 上的 FreeBSD

在 Mac OS® X 上的 VMware 上安装完 FreeBSD 之后，有些配置的步骤可用来优化虚拟系统。

1.  **设置 boot loader 变量**

    最重要的步骤是降低 `kern.hz` 来减少 VMware 上 FreeBSD 的 CPU 使用率。这需要在 `/boot/loader.conf` 里加入以下这行设定：

    ```
    kern.hz=100
    ```

    如果没有这项设定，VMware 上的 FreeBSD 客户 OS 空闲时将占用 iMac® 上一个 CPU 大约 15% 的资源。在修改此项设定之后仅为 5%。

2.  **创建一个新的内核配置文件**

    你可以去掉所有的 FireWire, USB 设备的驱动程序。 VMware 提供了一个 [em(4)](http://www.FreeBSD.org/cgi/man.cgi?query=em&sektion=4&manpath=freebsd-release-ports) 支持的虚拟网络适配器，所以除了 [em(4)](http://www.FreeBSD.org/cgi/man.cgi?query=em&sektion=4&manpath=freebsd-release-ports) 之外的网卡驱动都可以被剔除。

3.  **设置网络**

    最基本的网络设定包括简单的使用 DHCP 把你的虚拟机器连接到宿主 Mac® 相同的本地网络上。 在 `/etc/rc.conf` 中加入： `ifconfig_em0="DHCP"`。 更多有关网络的设置可以参阅 第 32 章 *高级网络*。

## 23.3. 作为宿主 OS 的 FreeBSD

在过去的几年中 FreeBSD 并没有任何可用的并被官方支持的虚拟化解决方案。 一些用户曾时使用过利用 Linux® 二进制兼容层运行的 VMware 陈旧并多半已过时的版本 (比如 [emulators/vmware3](http://www.freebsd.org/cgi/url.cgi?ports/emulators/vmware3/pkg-descr))。 在 FreeBSD 7.2 发布不久， Sun 开源版本 (Open Source Edition OSE) 的 VirtualBox™ 作为一个 FreeBSD 原生的程序出现在了 Ports Collection 中。

VirtualBox™ 是一个开发非常活跃， 完全虚拟化的软件， 并且可在大部份的操作系统上使用， 包括 Windows®， Mac OS®， Linux® 和 FreeBSD。同样也能把 Windows® 或 UNIX® 作为客户系统运行。 它有一个开源和一个私有两种版本。 从用户的角度来看， OSE 版本最主要的限制也许是缺乏 USB 的支持。 其他更多的差异可以通过链接 `[`www.virtualbox.org/wiki/Editions`](http://www.virtualbox.org/wiki/Editions)` 查看 “Editions” 页面。 目前， FreeBSD 上只有 OSE 版本可用。

### 23.3.1. 安装 VirtualBox™

VirtualBox™ 已作为一个 FreeBSD port 提供， 位于 [emulators/virtualbox-ose](http://www.freebsd.org/cgi/url.cgi?ports/emulators/virtualbox-ose/pkg-descr)， 可使用如下的命令安装：

```
# `cd /usr/ports/emulators/virtualbox-ose`
# `make install clean`
```

在配置对话框中的一个有用的选项是 `GusetAdditions` 程序套件。 这些在客户操作系统中提供了一些有用的特性， 比如集成鼠标指针 (允许在宿主和客户系统间使用鼠标， 而不用事先按下某个特定的快捷键来切换) 和更快的视频渲染， 特别是在 Windows® 客户系统中。 在安装了客户操作系统之后， 客户附加软件可在 Devices 菜单中找到。

在第一次运行 VirtualBox™ 之前还需要做一些配置上的修改。port 会安装一个内核模块至 `/boot/modules` 目录， 此模块需要事先加载：

```
# `kldload vboxdrv`
```

可以在 `/boot/loader.conf` 中加入以下的配置使此模块在机器重启之后能自动加载：

```
vboxdrv_load="YES"
```

在 3.1.2 之前版本的 VirtualBox™ 需要挂接 `proc` 文件系统。 在新版本中不再有此要求， 因为它们使用了由 [sysctl(3)](http://www.FreeBSD.org/cgi/man.cgi?query=sysctl&sektion=3&manpath=freebsd-release-ports) 库提供的功能。

当使用旧版本的 port 时， 需要使用下面的步骤来挂载 `proc`：

```
# `mount -t procfs proc /proc`
```

为了使配置能在重启后始终生效， 需要在 `/etc/fstab` 中加入以下这行：

```
proc	/proc	procfs	rw	0	0
```

### 注意:

如果在运行 VirtualBox™ 的终端中发现了类似如下的错误消息：

```
VirtualBox: supR3HardenedExecDir: couldn't read "", errno=2 cchLink=-1
```

此故障可能是由 `proc` 文件系统导致的。 请使用 `mount` 命令检查文件系统是否正确挂载。

在安装 VirtualBox™ 时会自动创建 `vboxusers` 组。 所有需要使用 VirtualBox™ 的用户必须被添加为此组中的成员。 可以使用 `pw` 命令添加新的成员：

```
# `pw groupmod vboxusers -m yourusername`
```

运行 VirtualBox™， 可以通过选择你当前图形环境中的 Sun VirtualBox， 也可以在虚拟终端中键入以下的命令:

```
% `VirtualBox`
```

获得更多有关配置和使用 VirtualBox™ 的信息， 请访问官方网站 `[`www.virtualbox.org`](http://www.virtualbox.org)`。 鉴于 FreeBSD port 非常新， 并仍处于开发状态。请查看 FreeBSD wiki 上的相关页面 `[`wiki.FreeBSD.org/VirtualBox`](http://wiki.FreeBSD.org/VirtualBox)` 以获取最新的信息和故障排查细则。

## 第 24 章 本地化－I18N/L10N 使用和设置

Contributed by Andrey Chernov.Rewritten by Michael C. Wu.

## 24.1. 概述

FreeBSD 是一个由分布于全世界的用户和贡献者支持的项目。 这章将讨论 FreeBSD 的国际化和本地化的问题,允许非英语用户也能使用 FreeBSD 很好地工作。 在系统和应用水平上，主要是通过执行 i18N 标准来实现的，所以这里我们将为读者提供详细的介绍。

读完这一章，您将了解：

*   不同的语言和地域是如何在现代操作系统上进行编码的。

*   如何为您的登入 shell 设置本地化。

*   如何配置您的控制台为非英语语言。 languages.

*   如何使用不同的语言来有效地使用 X Windows。

*   在哪里可以找到更多有关开发符合 i18N 标准的应用程序的信息。

阅读这章之前，您应当了解：

*   怎样安装额外的第三方程序（第 5 章 *安装应用程序: Packages 和 Ports*）。

## 24.2. 基础知识

### 24.2.1. I18N/L10N 是什么？

开发人员把 internationalization 简写成 I18N,中间的数字是前后两个字母间的字母个数。 L10N 依据“localization” 使用同样的命名规则。 I18N/L10N 方法、协议和应用结合在一起，允许用户使用他们自己所选择的语言。

I18N 应用程序使用 I18N 工具来编程。它允许开发人员写一个简单的文件， 就可以将显示的菜单和文本翻译成本地语言。我们非常鼓励程序员遵循这种规则。

### 24.2.2. 为什么要使用 I18N/L10N?

I18N/L10N 标准能够很好地支持您查看、输入或处理非英语语言。

### 24.2.3. I18N 支持哪些语言？

I18N 和 L10N 不是 FreeBSD 特有的。当前，它能支持世界上绝大部分主力语言， 包括但不限于：中文，德文，日文，朝鲜文，法文，俄文，越南文等等。

## 24.3. 使用本地化语言

I18N 不是 FreeBSD 特有的，它是一个规则。我们鼓励您帮助 FreeBSD 完善这一规则。

本地化设置需要具备三个条件：语言代码 (Language Code)、 国家代码 (Country Code) 和编码(Encoding)。 本地名字可以用下面这些部分来构造：

```
*`语言代码`*_*`国家代码`*.*`编码`*
```

### 24.3.1. 语言和国家代码

为了用特殊的语言来对 FreeBSD 系统进行本地化（或其他类 UNIX®系统）， 用户必须要知道相应的国家和语言代码（国家代码告诉应用程序使用哪一种语言规范）。 此外，WEB 浏览器，SMTP/POP 服务器，web 服务器等都是以这个为基础的。下面就是一个国家和语言代码的例子:

| 语言/国家代码 | 描述 |
| --- | --- |
| en_US | 美国英语 |
| ru_RU | 俄语 |
| zh_CN | 简体中文 |

### 24.3.2. 编码

一些语言不使用 ASCII 编码，它们使用 8-位， 宽或多字节的字符， 更多的信息请参考 [multibyte(3)](http://www.FreeBSD.org/cgi/man.cgi?query=multibyte&sektion=3&manpath=freebsd-release-ports)。 比较老的应用程序可能会无法识别它们， 并误认为是控制字符。 比较新的应用程序通常会认出 8-位字符。 随实现的不同， 用户可能不得不将宽或多字节字符支持编入应用程序， 或进行一些额外的配置， 才能够正常使用它们。 要输入和处理宽或多字节字符， FreeBSD Ports Collection 已经为每种语言提供了不同的程序。 请参考各个 FreeBSD Port 中的 I18N 文档。

特别需要指出的是， 用户可能需要查看应用程序的文档， 以确定如何正确地配置它， 或需要为 configure/Makefile/编译器 指定什么样的参数。

记住下面这些:

*   特定语言的简单 C 字符集 (参见 [multibyte(3)](http://www.FreeBSD.org/cgi/man.cgi?query=multibyte&sektion=3&manpath=freebsd-release-ports))，例如 ISO8859-1, ISO8859-15, KOI8-R, CP437。

*   宽字节或多字节编码，如 EUC, Big5。

您可以在[IANA Registry](http://www.iana.org/assignments/character-sets)检查一下现行的字符集列表。

### 注意:

与此不同的是， FreeBSD 使用与 X11-兼容的本地编码模式。

### 24.3.3. I18N 应用程序

在 FreeBSD Ports 和 Package 系统里面，I18N 应用程序已经使用`I18N` 来命名。然而它们不是总支持需要的语言。

### 24.3.4. 本地化设置

通常只要在登入 shell 里面设置`LANG`为本地化， 一般通过设置用户的 `~/.login_conf` 或用户 shell 的启动文件（`~/.profile`，`~/.bashrc`, `~/.cshrc`）。没有必要设置 `LC_CTYPE`，`LC_CTIME`。 更多的信息请参考特定语言的 FreeBSD 文档。

您应当在您的配置文件中设置下面两个变量：

*   `LANG` 为 POSIX®设置本地化语言功能。

*   `MM_CHARSET`应用程序的 MIME 字符集。

这包括用户的 shell 配置，特定的应用配置和 X11 配置。

#### 24.3.4.1. 设置本地化的方法

有两种方法来设置本地化，接下来都会描述。 第一种 (推荐) 就是在 登入分类里面指定环境变量。 第二种方法是把环境变量加到 shell 的启动文件里面。

##### 24.3.4.1.1. 登入分类方法

这种方法允许把本地化名称和 MIME 字符集的环境变量赋给可能的 shell， 而不是加到每个特定 shell 的启动文件里面。 用户级设置 Level Setup 允许普通用户自己完成这个设置，而管理员级设置需要超级用户权限。

###### 24.3.4.1.1.1. 用户级设置

这有一个设置用户根目录文件`.login_conf`的小例子， 它为上述两个变量设置了 Latin-1 编码。

```
me:\
	:charset=ISO-8859-1:\
	:lang=de_DE.ISO8859-1:
```

这是一个为`.login_conf`设置繁体中文的 BIG-5 编码的例子。应该设置下面的大部分变量， 因为很多软件都没有为中文，日文和韩文设置正确的本地化变量。

```
#Users who do not wish to use monetary units or time formats
#of Taiwan can manually change each variable
me:\
	:lang=zh_TW.Big5:\
	:setenv=LC_ALL=zh_TW.Big5:\
	:setenv=LC_COLLATE=zh_TW.Big5:\
	:setenv=LC_CTYPE=zh_TW.Big5:\
	:setenv=LC_MESSAGES=zh_TW.Big5:\
	:setenv=LC_MONETARY=zh_TW.Big5:\
	:setenv=LC_NUMERIC=zh_TW.Big5:\
	:setenv=LC_TIME=zh_TW.Big5:\
	:charset=big5:\
	:xmodifiers="@im=gcin": #Set gcin as the XIM Input Server
```

更多的信息参考管理员级设置和[login.conf(5)](http://www.FreeBSD.org/cgi/man.cgi?query=login.conf&sektion=5&manpath=freebsd-release-ports)

###### 24.3.4.1.1.2. 管理员级设置

检查用户的登入分类在 `/etc/login.conf`里面是否设置了正确的语言。主要确定下面的几个设置：

```
*`language_name`*|*`Account Type Description`*:\
	:charset=*`MIME_charset`*:\
	:lang=*`locale_name`*:\
	:tc=default:
```

再次使用前面的 Latin-1 编码的例子：

```
german|German Users Accounts:\
	:charset=ISO-8859-1:\
	:lang=de_DE.ISO8859-1:\
	:tc=default:
```

在修改用户的登入类型之前， 应首先执行下面的命令：

```
# `cap_mkdb /etc/login.conf`
```

以便使在 `/etc/login.conf` 中新增的配置生效。

##### 使用 [vipw(8)](http://www.FreeBSD.org/cgi/man.cgi?query=vipw&sektion=8&manpath=freebsd-release-ports) 改变登入类型。

使用`vipw`添加新用户，看起来像下面这样：

```
user:password:1111:11:*`language`*:0:0:User Name:/home/user:/bin/sh
```

##### 用[adduser(8)](http://www.FreeBSD.org/cgi/man.cgi?query=adduser&sektion=8&manpath=freebsd-release-ports)改变登入类型。

用`adduser`添加新用户看起来像下面这样：

*   在`/etc/adduser.conf`里面设置`defaultclass = 语言`。应该记住，您必须为使用其它语言的所有用户设置 `缺省`类别。

*   每一次使用[adduser(8)](http://www.FreeBSD.org/cgi/man.cgi?query=adduser&sektion=8&manpath=freebsd-release-ports)的时候，一个特定语言的可选择性回答会像下面这样给出：

    ```
    Enter login class: default []: 
    ```

*   如果您打算给每一个用户使用另外一种语言，您应该这样：

    ```
    # `adduser -class language`
    ```

##### 使用[pw(8)](http://www.FreeBSD.org/cgi/man.cgi?query=pw&sektion=8&manpath=freebsd-release-ports)改变登入类型。

如果您使用[pw(8)](http://www.FreeBSD.org/cgi/man.cgi?query=pw&sektion=8&manpath=freebsd-release-ports)来添加新用户，应该这样使用：

```
# `pw useradd user_name -L language`
```

##### 24.3.4.1.2. Shell 启动文件方法

### 注意:

不推荐使用这种方法，因为它需要给每一个可能的 shell 程序一个不同的启动文件。 应该用登入分类方法来代替这种方法。

为了设置本地化名称和 MIME 字符集，只要在`/etc/profile`或 `/etc/csh.login`启动文件里面设置这两个变量。下面我们使用德语做例子：

在`/etc/profile`里面：

```
LANG=de_DE.ISO8859-1; export LANG
MM_CHARSET=ISO-8859-1; export MM_CHARSET
```

或在`/etc/csh.login`里面：

```
setenv LANG de_DE.ISO8859-1
setenv MM_CHARSET ISO-8859-1
```

另外，您可以把上面的设置添加到`/usr/share/skel/dot.profile` （和前面的`/etc/profile`一样），或者`/usr/share/skel/dot.login` （和前面的`/etc/csh.login`一样）。

对于 X11：

在`$HOME/.xinitrc`里面：

```
LANG=de_DE.ISO8859-1; export LANG
```

或者：

```
setenv LANG de_DE.ISO8859-1
```

依赖您的 shell(看上面）。

### 24.3.5. 控制台设置

对于所有的简单 C 字符集，在`/etc/rc.conf`中用正在讨论的语言设置正确的控制台字符：

```
font8x16=*`font_name`*
font8x14=*`font_name`*
font8x8=*`font_name`*
```

这儿的*`font_name`*来自于`/usr/share/syscons/fonts`目录， 不带`.fnt`后缀。

如果需要的话， 还应通过 `sysinstall` 来配置与单字节 C 字符集对应的 keymap 和 screenmap。 在 sysinstall 中， 选择 Configure 之后选择 Console 即可进行配置。 除此之外， 您也可以在 `/etc/rc.conf` 中加入类似下面的配置：

```
scrnmap=*`screenmap_name`*
keymap=*`keymap_name`*
keychange="*`fkey_number sequence`*"
```

这儿的*`screenmap_name`*是来自`/usr/share/syscons/scrnmaps`目录， 不带`.scm`后缀。 一个带影射字体的屏幕布局通常被作为一个工作区， 用来在 VGA 适配器字体矩阵上扩展 8 位到 9 位。 如果屏幕字体是使用一个 8 位的排列，要移动这些字母离开这些区域。

如果您在`/etc/rc.conf`里面启用了 moused daemon：

```
moused_enable="YES"
```

那么需要在下一段检查鼠标指针信息。

默认情况下， [syscons(4)](http://www.FreeBSD.org/cgi/man.cgi?query=syscons&sektion=4&manpath=freebsd-release-ports)驱动程序的鼠标指针在字符集中占用 0xd0-0xd3 的范围。 如果您的语言使用这个范围，您必须把指针范围移出这个范围。 要绕过这个问题， 需要在 `/etc/rc.conf` 中加入：

```
mousechar_start=3
```

这里， *`keymap_name`* 来自于 `/usr/share/syscons/keymaps` 目录， 但去掉了 `.kbd` 后缀。 如果不确定应该使用哪一个键盘布局， 则可以使用 [kbdmap(1)](http://www.FreeBSD.org/cgi/man.cgi?query=kbdmap&sektion=1&manpath=freebsd-release-ports) 来测试， 而无需反复重启。

通常， `keychange` 是设定功能键时， 匹配选定的终端类型来说是必需的， 因为功能键序列无法在键盘布局中定义。

此外您还应该检查并确认在 `/etc/ttys` 中已经为所有的 `ttyv*` 项配置了正确的终端类型。 目前， 相关的默认定义是：

| 字符集设置 | 终端类型 |
| --- | --- |
| ISO8859-1 or ISO8859-15 | `cons25l1` |
| ISO8859-2 | `cons25l2` |
| ISO8859-7 | `cons25l7` |
| KOI8-R | `cons25r` |
| KOI8-U | `cons25u` |
| CP437 (VGA default) | `cons25` |
| US-ASCII | `cons25w` |

对于多字节字符语言，可以您的在 `/usr/ports/language` 目录中使用正确的 FreeBSD port。一些 port 以控制台出现， 而系统把它作为串行 vtty 终端，因此， 必须为 X11 和伪串行控制台准备足够的 vtty 终端。 下面是在控制台中使用其他语言的应用程序的部分列表：

| 语言 | 特定区域 |
| --- | --- |
| Traditional Chinese (BIG-5) | [chinese/big5con](http://www.freebsd.org/cgi/url.cgi?ports/chinese/big5con/pkg-descr) |
| Japanese | [japanese/kon2-16dot](http://www.freebsd.org/cgi/url.cgi?ports/japanese/kon2-16dot/pkg-descr) or [japanese/mule-freewnn](http://www.freebsd.org/cgi/url.cgi?ports/japanese/mule-freewnn/pkg-descr) |
| Korean | [korean/han](http://www.freebsd.org/cgi/url.cgi?ports/korean/han/pkg-descr) |

### 24.3.6. X11 设置

虽然 X11 不是 FreeBSD 计划的一部分， 但我们已经为 FreeBSD 用户包含了一些信息。 具体细节可以参考[Xorg Web 站点](http://www.x.org/) 或是您使用的 X11 Server 的网站。

在`~/.Xresources`里面，您可以适当调整特定应用程序的 I18N 设置（如字体，菜单等）。

#### 24.3.6.1. 显示字体

安装 Xorg 服务器 ([x11-servers/xorg-server](http://www.freebsd.org/cgi/url.cgi?ports/x11-servers/xorg-server/pkg-descr))， 然后安装对应语言的 TrueType® 字体。 请设置正确的地区信息， 这将让您能够在菜单和其它地方看到所选择的语言。

#### 24.3.6.2. 输入非英语字符

X11 输入方法（XIM）协议是所有 X11 客户端的一个新标准。 所有将作为 XIM 客户端来写的 X11 应用程序从 XIM 输入服务器输入。 不同的语言有几种 XIM 服务器可用。

### 24.3.7. 打印机设置

一些简单的 C 字符集通常是用硬编码来编码进打印机的。更宽或多位的字符集需要特定的设置， 我们推荐使用 apsfilter。您也可以使用特定语言转换器把文档转换为 PostScript®或 PDF 格式。

### 24.3.8. 内核和文件系统

FreeBSD 的快速文件系统 (FFS) 是完全支持 8-位 字符的， 因此它可以被用于任何简单的 C 字符集 (参见 [multibyte(3)](http://www.FreeBSD.org/cgi/man.cgi?query=multibyte&sektion=3&manpath=freebsd-release-ports))， 但在文件系统中不会保存字符集的名字； 也就是说， 它不加修改地保存 8-位信息， 而并不知道如何编码。 正式说来， FFS 目前还不支持任何形式的宽或多字节字符集。 不过， 某些宽或多字符集提供了独立的针对 FFS 的补丁来帮助启用关于它们的支持。 目前这些要么是无法移植的， 要么过于粗糙， 因此我们不打算把它们加入到源代码中。 请参考相关语言的 Web 站点， 以了解关于这些补丁的进一步情况。

FreeBSD MS-DOS®已经能够配置成用在 MS-DOS®上，Unicode 字符集和可选的 FreeBSD 文件系统字符集的更多信息， 请参考 [mount_msdosfs(8)](http://www.FreeBSD.org/cgi/man.cgi?query=mount_msdosfs&sektion=8&manpath=freebsd-release-ports) 联机手册。

## 24.4. 编译 I18N 程序

许多 FreeBSD Ports 已经支持 I18N 了。他们中的一些都用-I18N 作标记。 这些和其他很多程序已经内建 I18N 的支持，不需要考虑其他的事项了。

然而一些像 MySQL 这样的应用程序需要重新配置字符集，可在 `Makefile`里面设置，或者直接把参数传递给 configure。

## 24.5. 本地化 FreeBSD

### 24.5.1. 俄语（KOI8-R 编码）

Originally contributed by Andrey Chernov.

关于 KOI8-R 编码的更多信息请查阅[KOI8-R 参考（Russian Net Character Set）](http://koi8.pp.ru/)。

#### 24.5.1.1. 本地设置

把下面的行加入到您的`~/.login_conf`文件：

```
me:My Account:\
	:charset=KOI8-R:\
	:lang=ru_RU.KOI8-R:
```

参看前面的设置本地化的例子。

#### 24.5.1.2. 控制台设置

*   把下面一行加到 `/etc/rc.conf`：

    ```
    mousechar_start=3
    ```

*   并在 `/etc/rc.conf` 里面增加如下设置：

    ```
    keymap="ru.koi8-r"
    scrnmap="koi8-r2cp866"
    font8x16="cp866b-8x16"
    font8x14="cp866-8x14"
    font8x8="cp866-8x8"
    ```

*   对于`/etc/ttys`里面的`ttyv*`记录，要使用 `cons25r`作为终端类型。

参看前面的设置控制台的例子。

#### 24.5.1.3. 打印机设置

既然绝大多数带俄语字符的打印机遵循 CP866 的标准， 那么需要一个针对 KOI8-R 到 CP866 转换的特定输出过滤器。这样的一个过滤器默认的安装在 `/usr/libexec/lpr/ru/koi2alt`。 一个支持俄语的打印机的`/etc/printcap`记录看起来是这样的：

```
lp|Russian local line printer:\
	:sh:of=/usr/libexec/lpr/ru/koi2alt:\
	:lp=/dev/lpt0:sd=/var/spool/output/lpd:lf=/var/log/lpd-errs:
```

更多信息参考[printcap(5)](http://www.FreeBSD.org/cgi/man.cgi?query=printcap&sektion=5&manpath=freebsd-release-ports)手册页。

#### 24.5.1.4. MS-DOS®文件系统和俄语文件名

下面的例子是在挂上 MS-DOS® 文件系统后，启用对俄语文件名支持的[fstab(5)](http://www.FreeBSD.org/cgi/man.cgi?query=fstab&sektion=5&manpath=freebsd-release-ports)记录：

```
/dev/ad0s2      /dos/c  msdos   rw,-Wkoi2dos,-Lru_RU.KOI8-R 0 0
```

选项 `-L` 用于选择地区名称， 而 `-W` 则用于设置字符转换表。 要使用 `-W` 选项， 则一定要首先挂接 `/usr`， 然后再挂接 MS-DOS® 分区， 因为转换表是放在 `/usr/libdata/msdosfs` 的。 要了解进一步的细节， 请参考 [mount_msdosfs(8)](http://www.FreeBSD.org/cgi/man.cgi?query=mount_msdosfs&sektion=8&manpath=freebsd-release-ports) 联机手册。

#### 24.5.1.5. X11 设置

1.  首先请进行前面介绍的 非-X 的本地化设置。

2.  如果您正使用 Xorg， 请安装 [x11-fonts/xorg-fonts-cyrillic](http://www.freebsd.org/cgi/url.cgi?ports/x11-fonts/xorg-fonts-cyrillic/pkg-descr) package。

    检查您 `/etc/X11/xorg.conf` 文件中的 `"Files"` 小节。 下面的行， 应加到任何其它 `FontPath` 项之前：

    ```
    FontPath   "/usr/local/lib/X11/fonts/cyrillic"
    ```

    ### 注意:

    请查看 ports 中的其它西里尔字体。

3.  要激活俄语键盘， 需要在 `xorg.conf` 文件的 `"Keyboard"` 小节中加入下列内容：

    ```
    Option "XkbLayout"   "us,ru"
    Option "XkbOptions"  "grp:toggle"
    ```

    要确信`XkbDisable` 已经关闭 (注释掉) 了。

    RUS/LAT 的切换用**CapsLock**。老的**CapsLock**功能可以通过 **Shift**+**CapsLock** 来模拟（只有在 LAT 模式的时候）。

    使用 `grp:toggle` 时， RUS/LAT 切换键将是 **右 Alt**， 而使用 `grp:ctrl_shift_toggle` 则表示切换键是 **Ctrl**+**Shift**。 使用 `grp:caps_toggle` 时， RUS/LAT 切换键则是 **CapsLock**。 旧的 **CapsLock** 功能仍可通过 **Shift**+**CapsLock** (只对 LAT 模式有效)。 由于不明原因， `grp:caps_toggle` 在 Xorg 中无法使用。

    如果您的键盘上有 “Windows®” 键， 但发现 RUS 模式下， 某些非字母键映射不正常， 则应在您的 `xorg.conf` 文件中加入下面这行：

    ```
    Option "XkbVariant" ",winkeys"
    ```

    ### 注意:

    俄语的 XKB 键盘可能并不为某些不具备本地化功能的应用程序所支持。

### 注意:

本地化程序最低限度应在程序启动时调用 `XtSetLanguageProc (NULL, NULL, NULL);` 函数。

参见 [KOI8-R for X Window](http://koi8.pp.ru/xwin.html) 以获得关于对 X11 应用进行本地化的指导。

### 24.5.2. 设置繁体中文

FreeBSD-Taiwan 计划有一个使用很多中文 ports 的中文化指南在 `[`netlab.cse.yzu.edu.tw/~statue/freebsd/zh-tut/`](http://netlab.cse.yzu.edu.tw/~statue/freebsd/zh-tut/)`。 目前， `FreeBSD 中文化指南` 的维护人员是 沈俊兴 `<statue@freebsd.sinica.edu.tw>`。

沈俊兴 `<statue@freebsd.sinica.edu.tw>` 利用 FreeBSD-Taiwan 的 `zh-L10N-tut`建立了 [Chinese FreeBSD Collection (CFC)](http://netlab.cse.yzu.edu.tw/~statue/cfc/)。 相关的 packages 和脚本等可以在 `ftp://freebsd.csie.nctu.edu.tw/pub/taiwan/CFC/` 找到。

### 24.5.3. 德语本地化（适合所有的 ISO 8859-1 语言）

Slaven Rezic `<eserte@cs.tu-berlin.de>` 写了一个在 FreeBSD 机器下如何使用日尔曼语言的德语指南。 这份德语教程可以在 `[`user.cs.tu-berlin.de/~eserte/FreeBSD/doc/umlaute/umlaute.html`](http://user.cs.tu-berlin.de/~eserte/FreeBSD/doc/umlaute/umlaute.html)` 找到。

### 24.5.4. 希腊语本地化

Nikos Kokkalis `<nickkokkalis@gmail.com>` 撰写了关于在 FreeBSD 上支持希腊语的完整文章， 在 `www.freebsd.org/doc/el_GR.ISO8859-7/articles/greek-language-support/index.html`。 请注意这篇文章 *只有* 希腊语的版本。

### 24.5.5. 日语和韩语本地化

日语本地化请参考`[`www.jp.FreeBSD.org/`](http://www.jp.FreeBSD.org/)`，韩语参考 `[`www.kr.FreeBSD.org/`](http://www.kr.FreeBSD.org/)`。

### 24.5.6. 非英语的 FreeBSD 文档

一些 FreeBSD 的贡献者已经将部分 FreeBSD 文档翻译成了其他语言。 您可在 主站 以及 `/usr/share/doc` 找到。

## 第 25 章 更新与升级 FreeBSD

重新组织和部分更新，由 Jim Mock.原创：Jordan Hubbard, Poul-Henning Kamp, John Polstra 和 Nik Clayton.中文翻译：张 雪平.

## 25.1. 概述

FreeBSD 在发行版之间始终是持续开发的。 一些人喜欢使用官方发行的版本， 另一些喜欢与最新的开发保持同步。 然而， 即使是官方的发行版本也常常需要安全补丁和重大修正方面的更新。 不论你使用了何种版本， FreeBSD 都提供了所有更新系统所需的工具， 让你轻松的在不同版本间升级。 这一章节将帮助你决定是跟踪开发系统还是坚持使用某个发行的版本。 同时还列出了一些保持系统更新所需的基本工具。

读了本章后，您将了解到：

*   使用哪些工具来更新系统与 Ports Collection。

*   如何使用 freebsd-update, CVSup, CVS, or CTM 让你的系统保持更新。

*   如何比较已安装的系统与原来已知拷贝的状态。

*   如何使用 CVSup 或者文档 ports 来更新本地的文档。

*   两个开发分支 FreeBSD-STABLE 和 FreeBSD-CURRENT 的区别。

*   如何通过 `make buildworld` 重新编译安装整个基本系统(等等)。

在读本章这前，您应该了解的：

*   正确设置网络连接 (第 32 章 *高级网络*)。

*   知道怎样安装附加的第三方软件(第 5 章 *安装应用程序: Packages 和 Ports*)。

### 注意:

整个这一章中，`cvsup` 命令都被用来获取 FreeBSD 源代码的更新。 你需要安装 [net/cvsup](http://www.freebsd.org/cgi/url.cgi?ports/net/cvsup/pkg-descr) port 或者二进制包(如果你不想要安装图形界面的 `cvsup` 客户端的话， 则可以安装 [net/cvsup-without-gui](http://www.freebsd.org/cgi/url.cgi?ports/net/cvsup-without-gui/pkg-descr) port)。 你也可以使用 [csup(1)](http://www.FreeBSD.org/cgi/man.cgi?query=csup&sektion=1&manpath=freebsd-release-ports) 代替， 它现在已经是基本系统的一部分了。

## 25.2. FreeBSD 更新

Written by Tom Rhodes.Based on notes provided by Colin Percival.

打安全补丁是对于维护计算机软件的一个重要部分， 特别是对于操作系统。对于 FreeBSD 来说， 很长的一段时间以来这都不是一件容易的事情。 补丁打在源代码上，代码需要被重新编译为二进制， 然后再重新安装编译后的程序。

FreeBSD 引入了 `freebsd-update` 工具之后这便不再是问题了。这个工具提供了 2 种功能。 第一，它可以把二进制的安全和勘误更新直接应用于 FreeBSD 的基本系统，而不需要重新编译和安装。第二， 这个工具还支持主要跟次要的发行版的升级。

### 注意:

由安全小组支持的各种体系结构和发行版都可使用二进制更新。 在升级到一个新的发行版本之前， 应先阅读一下当前发行版的声明， 因为它们可能包含有关于你期望升级版本的重要消息。 这些发行声明可以通过以下链接查阅： `[`www.FreeBSD.org/releases/`](http://www.FreeBSD.org/releases/)`。

如果 `crontab` 中存在有用到 `freebsd-update` 特性的部分， 那么这些在开始以下操作前必须先被禁止。

### 25.2.1. 配置文件

有些用户可能希望通过调整配置文件 `/etc/freebsd-update.conf` 中的默认配置来更好地控制升级的过程。 可用的参数在文档中介绍的很详细， 但下面的这些可能需要进一步的解释：

```
# Components of the base system which should be kept updated.
Components src world kernel
```

这个参数是控制 FreeBSD 的哪一部分将被保持更新。 默认的是更新源代码，整个基本系统还有内核。 这些部件跟安装时的那些相同，举例来说， 在这里加入 `world/games` 就会允许打入游戏相关的补丁。 使用 `src/bin` 则是允许更新 `src/bin` 目录中的源代码。

最好的选择是把这个选项保留为默认值， 因为如果要修改它去包含一些指定的选项， 就需要用户列出每一个想要更新的项目。 这可能会引起可怕的后果， 因为部分的源代码和二进制程序得不到同步。

```
# Paths which start with anything matching an entry in an IgnorePaths
# statement will be ignored.
IgnorePaths
```

添加路径，比如 `/bin` 或者 `/sbin` 让这些指定的目录在更新过程中不被修改。 这个选项能够防止本地的修改被 `freebsd-update` 覆盖。

```
# Paths which start with anything matching an entry in an UpdateIfUnmodified
# statement will only be updated if the contents of the file have not been
# modified by the user (unless changes are merged; see below).
UpdateIfUnmodified /etc/ /var/ /root/ /.cshrc /.profile
```

更新指定目录中的未被修改的配置文件。 用户的任何修改都会使这些文件的自动更新失效。 还有另外一个选项， `KeepModifiedMetadata`， 这个能让 `freebsd-update` 在合并时保存修改。

```
# When upgrading to a new FreeBSD release, files which match MergeChanges
# will have any local changes merged into the version from the new release.
MergeChanges /etc/ /var/named/etc/
```

一个 `freebsd-update` 应该尝试合并的配置文件的列表。文件合并的过程是 一系列的 [diff(1)](http://www.FreeBSD.org/cgi/man.cgi?query=diff&sektion=1&manpath=freebsd-release-ports) 补丁类似于更少选项的 [mergemaster(8)](http://www.FreeBSD.org/cgi/man.cgi?query=mergemaster&sektion=8&manpath=freebsd-release-ports) 合并的选项是接受，打开一个文本编辑器，或者 `freebsd-update` 会被中止。 在不能确定的时候，请先备份 `/etc` 然后接受合并。更多关于 `mergemaster` 的信息请参阅 第 25.7.11.1 节 “`mergemaster`”。

```
# Directory in which to store downloaded updates and temporary
# files used by FreeBSD Update.
# WorkDir /var/db/freebsd-update
```

这个目录是放置所有补丁和临时文件的。 用户做一个版本升级的话，请确认此处至少有 1 GB 的可用磁盘空间。

```
# When upgrading between releases, should the list of Components be
# read strictly (StrictComponents yes) or merely as a list of components
# which *might* be installed of which FreeBSD Update should figure out
# which actually are installed and upgrade those (StrictComponents no)?
# StrictComponents no
```

当设置成 `yes` 时， `freebsd-update` 将假设这个 `Components` 列表时完整的， 并且对此列表以外的项目不会修改。实际上就是 `freebsd-update` 会尝试更新 `Componets` 列表里的每一个文件。

### 25.2.2. 安全补丁

安全补丁存储在远程的机器上， 可以使用如下的命令下载并安装：

```
# `freebsd-update fetch`
# `freebsd-update install`
```

如果给内核打了补丁，那么系统需要重新启动。 如果一切都进展顺利，系统就应该被打好了补丁而且 `freebsd-update` 可由夜间 [cron(8)](http://www.FreeBSD.org/cgi/man.cgi?query=cron&sektion=8&manpath=freebsd-release-ports) 执行。在 `/etc/crontab` 中加入以下条目足以完成这项任务：

```
@daily                                  root    freebsd-update cron
```

这条记录是说明每天运行一次 `freebsd-update` 工具。 用这种方法， 使用了 `cron` 参数， `freebsd-update` 仅检查是否存在更新。 如果有了新的补丁，就会自动下载到本地的磁盘， 但不会自动给系统打上。`root` 会收到一封电子邮件告知需手动安装补丁。

如果出现了错误，可以使用下面的 `freebsd-update` 命令回退到上一次的修改：

```
# `freebsd-update rollback`
```

完成以后如果内核或任何的内核模块被修改的话， 就需要重新启动系统。这将使 FreeBSD 装载新的二进制程序进内存。

`freebsd-update` 工具只能自动更新 `GENERIC` 内核。 如果您使用自行联编的内核， 则在 `freebsd-update` 安装完更新的其余部分之后需要手工重新联编和安装内核。 不过， `freebsd-update` 会检测并更新位于 `/boot/GENERIC` (如果存在) 中的 `GENERIC` 内核， 即使它不是当前 (正在运行的) 系统的内核。

### 注意:

保存一份 `GENERIC` 内核的副本到 `/boot/GENERIC` 是一个明智的主意。 在诊断许多问题， 以及在 第 25.2.3 节 “重大和次要的更新” 中介绍的使用 `freebsd-update` 更新系统时会很有用。

除非修改位于 `/etc/freebsd-update.conf` 中的配置， `freebsd-update` 会随其他安装一起对内核的源代码进行更新。 重新联编并安装定制的内核可以以通常的方式来进行。

### 注意:

通过 `freebsd-update` 发布的更新有时并不会涉及内核。 如果在执行 `freebsd-update install` 的过程中内核代码没有进行变动， 就没有必要重新联编内核了。 不过， 由于 `freebsd-update` 每次都会更新 `/usr/src/sys/conf/newvers.sh` 文件， 而修订版本 (`uname -r` 报告的 `-p` 数字) 来自这个文件， 因此， 即使内核没有发生变化， 重新联编内核也可以让 [uname(1)](http://www.FreeBSD.org/cgi/man.cgi?query=uname&sektion=1&manpath=freebsd-release-ports) 报告准确的修订版本。 在维护许多系统时这样做会比较有帮助， 因为这一信息可以迅速反映机器上安装的软件更新情况。

### 25.2.3. 重大和次要的更新

这个过程会删除旧的目标文件和库， 这将使大部分的第三方应用程序无法删除。 建议将所有安装的 ports 先删除然后重新安装，或者稍后使用 [ports-mgmt/portupgrade](http://www.freebsd.org/cgi/url.cgi?ports/ports-mgmt/portupgrade/pkg-descr) 工具升级。 大多数用户将会使用如下命令尝试编译：

```
# `portupgrade -af`
```

这将确保所有的东西都会被正确的重新安装。 请注意环境变量 `BATCH` 设置成 `yes` 的话将在整个过程中对所有询问回答 `yes`，这会帮助在编译过程中免去人工的介入。

如果正在使用的是定制的内核， 则升级操作会复杂一些。 您会需要将一份 `GENERIC` 内核的副本放到 `/boot/GENERIC`。 如果系统中没有 `GENERIC` 内核， 可以用以下两种方法之一来安装：

*   如果只联编过一次内核， 则位于 `/boot/kernel.old` 中的内核， 就是 `GENERIC` 的那一个。 只需将这个目录改名为 `/boot/GENERIC` 即可。

*   假如能够直接接触机器， 则可以通过 CD-ROM 介质来安装 `GENERIC` 内核。 将安装盘插入光驱， 并执行下列命令：

    ```
    # `mount /cdrom`
    # `cd /cdrom/X.Y-RELEASE/kernels`
    # `./install.sh GENERIC`
    ```

    您需要将 `X.Y-RELEASE` 替换为您正在使用的版本。 `GENERIC` 内核默认情况下会安装到 `/boot/GENERIC`。

*   如果前面的方法都不可用， 还可以使用源代码来重新联编和安装 `GENERIC` 内核：

    ```
    # `cd /usr/src`
    # `env DESTDIR=/boot/GENERIC make kernel`
    # `mv /boot/GENERIC/boot/kernel/* /boot/GENERIC`
    # `rm -rf /boot/GENERIC/boot`
    ```

    如果希望 `freebsd-update` 能够正确地将内核识别为 `GENERIC`， 您必须确保没有对 `GENERIC` 配置文件进行过任何变动。 此外， 建议您取消任何其他特殊的编译选项 (例如使用空的 `/etc/make.conf`)。

上述步骤并不需要使用这个 `GENERIC` 内核来引导系统。

重大和次要的更新可以由 `freebsd-update` 命令后指定一个发行版本来执行， 举例来说，下面的命令将帮助你升级到 FreeBSD 8.1：

```
# `freebsd-update -r 8.1-RELEASE upgrade`
```

在执行这个命令之后，`freebsd-update` 将会先解析配置文件和评估当前的系统以获得更新系统所需的必要信息。 然后便会显示出一个包含了已检测到与未检测到的组件列表。 例如：

```
Looking up update.FreeBSD.org mirrors... 1 mirrors found.
Fetching metadata signature for 8.0-RELEASE from update1.FreeBSD.org... done.
Fetching metadata index... done.
Inspecting system... done.

The following components of FreeBSD seem to be installed:
kernel/smp src/base src/bin src/contrib src/crypto src/etc src/games
src/gnu src/include src/krb5 src/lib src/libexec src/release src/rescue
src/sbin src/secure src/share src/sys src/tools src/ubin src/usbin
world/base world/info world/lib32 world/manpages

The following components of FreeBSD do not seem to be installed:
kernel/generic world/catpages world/dict world/doc world/games
world/proflibs

Does this look reasonable (y/n)? y
```

此时，`freebsd-update` 将会尝试下载所有升级所需的文件。在某些情况下， 用户可能被问及需安装些什么和如何进行之类的问题。

当使用定制内核时， 前面的步骤会产生类似下面的警告：

```
WARNING: This system is running a "*`MYKERNEL`*" kernel, which is not a
kernel configuration distributed as part of FreeBSD 8.0-RELEASE.
This kernel will not be updated: you MUST update the kernel manually
before running "/usr/sbin/freebsd-update install"
```

此时您可以暂时安全地无视这个警告。 更新的 `GENERIC` 内核将在升级过程的中间步骤中使用。

在下载完针对本地系统的补丁之后， 这些补丁会被应用到系统上。 这个过程需要消耗的时间取决于机器的速度和其负载。 这个过程中将会对配置文件所做的变动进行合并 ── 这一部分需要用户的参与， 文件可能会自动合并， 屏幕上也可能会给出一个编辑器， 用于手工完成合并操作。 在处理过程中， 合并成功的结果会显示给用户。 失败或被忽略的合并， 则会导致这一过程的终止。 用户可能会希望备份一份 `/etc` 并在这之后手工合并重要的文件， 例如 `master.passwd` 和 `group`。

### 注意:

系统至此还没有被修改，所有的补丁和合并都在另外一个目录中进行。 当所有的补丁都被成功的打上了以后，所有的配置文件都被合并后， 我们就已经完成了整个升级过程中最困难的部分， 下面就需要用户来安装这些变更了。

一旦这个步骤完成后，使用如下的命令将升级后的文件安装到磁盘上。

```
# `freebsd-update install`
```

内核和内核模块会首先被打上补丁。 此时必须重新启动计算机。 如果您使用的是定制的内核， 请使用 [nextboot(8)](http://www.FreeBSD.org/cgi/man.cgi?query=nextboot&sektion=8&manpath=freebsd-release-ports) 命令来将下一次用于引导系统的内核 `/boot/GENERIC` (它会被更新)：

```
# `nextboot -k GENERIC`
```

### 警告:

在使用 `GENERIC` 内核启动之前， 请确信它包含了用于引导系统所需的全部驱动程序 (如果您是在远程进行升级操作， 还应确信网卡驱动也是存在的)。 特别要注意的情形是， 如果之前的内核中静态联编了通常以内核模块形式存在的驱动程序， 一定要通过 `/boot/loader.conf` 机制来将这些模块加载到 `GENERIC` 内核的基础上。 此外， 您可能也希望临时取消不重要的服务、 磁盘和网络挂载等等， 直到升级过程完成为止。

现在可以用更新后的内核引导系统了：

```
# `shutdown -r now`
```

在系统重新上线后，需要再次运行 `freebsd-update`。 升级的状态被保存着，这样 `freebsd-update` 就无需重头开始，但是会删除所有旧的共享库和目标文件。 执行如下命令继续这个阶段的升级：

```
# `freebsd-update install`
```

### 注意:

取决与是否有库的版本更新，通常只有 2 个而不是 3 个安装阶段。

现在需要重新编译和安装第三方软件。 这么做的原因是某些已安装的软件可能依赖于在升级过程中已删除的库。 可使用 [ports-mgmt/portupgrade](http://www.freebsd.org/cgi/url.cgi?ports/ports-mgmt/portupgrade/pkg-descr) 自动化这个步骤，以如下的命令开始：

```
# `portupgrade -f ruby`
# `rm /var/db/pkg/pkgdb.db`
# `portupgrade -f ruby18-bdb`
# `rm /var/db/pkg/pkgdb.db /usr/ports/INDEX-*.db`
# `portupgrade -af`
```

一旦这个完成了以后，再最后一次运行 `freebsd-update` 来结束升级过程。 执行如下命令处理升级中的所有细节：

```
# `freebsd-update install`
```

如果您临时用过 `GENERIC` 内核来引导系统， 现在是按照通常的方法重新联编并安装新的定制内核的时候了。

重新启动机器进入新版本的 FreeBSD 升级过程至此就完成了。

### 25.2.4. 系统状态对照

`freebsd-update` 工具也可被用来对着一个已知完好的 FreeBSD 拷贝测试当前的版本。 这个选项评估当前的系统工具，库和配置文件。 使用以下的命令开始对照：

```
# `freebsd-update IDS >> outfile.ids`
```

### 警告:

这个命令的名称是 IDS， 它并不是一个像 [security/snort](http://www.freebsd.org/cgi/url.cgi?ports/security/snort/pkg-descr) 这样的入侵检测系统的替代品。因为 `freebsd-update` 在磁盘上存储数据， 很显然它们有被篡改的可能。 当然也可以使用一些方法来降低被篡改的可能性，比如设置 `kern.securelevel` 和不使用时把 `freebsd-update` 数据放在只读文件系统上，例如 DVD 或 安全存放的外置 USB 磁盘上。

现在系统将会被检查，生成一份包含了文件和它们的 [sha256(1)](http://www.FreeBSD.org/cgi/man.cgi?query=sha256&sektion=1&manpath=freebsd-release-ports) 哈希值的清单，已知发行版中的值与当前系统中安装的值将会被打印到屏幕上。 这就是为什么输出被送到了 `outfile.ids` 文件。 它滚动的太块无法用肉眼对照，而且会很快填满控制台的缓冲区。

这个文件中有非常长的行，但输出的格式很容易分析。 举例来说，要获得一份与发行版中不同哈希值的文件列表， 已可使用如下的命令：

```
# `cat outfile.ids | awk '{ print $1 }' | more`
/etc/master.passwd
/etc/motd
/etc/passwd
/etc/pf.conf
```

这份输出时删节缩短后的，其实是有更多的文件。 其中有些文件并非人为修改，比如 `/etc/passwd` 被修改是因为添加了用户进系统。在某些情况下， 还有另外的一些文件，诸如内核模块与 `freebsd-update` 的不同是因为它们被更新过了。 为了指定的文件或目录排除在外，把它们加到 `/etc/freebsd-update.conf` 的 `IDSIgnorePaths` 选项中。

除了前面讨论过的部分之外， 这也能被当作是对升级方法的详细补充。

## 25.3. Portsnap： 一个 Ports Collection 更新工具

Written by Tom Rhodes.Based on notes provided by Colin Percival.

FreeBSD 基本系统也包括了一个更新 Ports Collection 的工具： [portsnap(8)](http://www.FreeBSD.org/cgi/man.cgi?query=portsnap&sektion=8&manpath=freebsd-release-ports)。在运行之后，它会连上一个远程网站， 校验安全密钥，然后下载一份 Ports Collection 的拷贝。 密钥是用来校验所有下载文件的完整性，确保它们在传输是未被修改。 使用以下的命令下载最新的 Ports Collection：

```
# `portsnap fetch`
Looking up portsnap.FreeBSD.org mirrors... 3 mirrors found.
Fetching snapshot tag from portsnap1.FreeBSD.org... done.
Fetching snapshot metadata... done.
Updating from Wed Aug  6 18:00:22 EDT 2008 to Sat Aug 30 20:24:11 EDT 2008.
Fetching 3 metadata patches.. done.
Applying metadata patches... done.
Fetching 3 metadata files... done.
Fetching 90 patches.....10....20....30....40....50....60....70....80....90\. done.
Applying patches... done.
Fetching 133 new ports or files... done.
```

这个例子展示的是 [portsnap(8)](http://www.FreeBSD.org/cgi/man.cgi?query=portsnap&sektion=8&manpath=freebsd-release-ports) 发现并校验了几个用于当前 ports 的补丁。这还表明以前运行过， 如果是第一次运行的话，那么仅仅只会下载 Ports Collection。

在 [portsnap(8)](http://www.FreeBSD.org/cgi/man.cgi?query=portsnap&sektion=8&manpath=freebsd-release-ports) 成功地完成一次 `fetch` 操作之后， 会将校验过的 Ports 套件和后续的补丁保存在本地。 首次执行 `portsnap` 之后， 你必须使用 `extract` 安装下载的文件：

```
# `portsnap extract`
/usr/ports/.cvsignore
/usr/ports/CHANGES
/usr/ports/COPYRIGHT
/usr/ports/GIDs
/usr/ports/KNOBS
/usr/ports/LEGAL
/usr/ports/MOVED
/usr/ports/Makefile
/usr/ports/Mk/bsd.apache.mk
/usr/ports/Mk/bsd.autotools.mk
/usr/ports/Mk/bsd.cmake.mk
*`...`*
```

使用 `portsnap update` 命令更新已安装的 Ports：

```
# `portsnap update`
```

至此更新就完成了，然后便可以使用更新后的 Ports Collection 来安装或升级应用程序。

`fetch` 和 `extract` 或 `update` 可以作为连续的动作执行， 如下例所示：

```
# `portsnap fetch update`
```

这个命令将会下载最新版本的 Ports 并更新本地位于 `/usr/ports` 的拷贝。

## 25.4. 更新系统附带的文档

除了基本系统和 Ports 套件之外， 文档也是 FreeBSD 操作系统的一个组成部分。 尽管您总是可以通过 [FreeBSD 网站](http://www.freebsd.org/doc/) 来访问最新的 FreeBSD 文档， 一些用户的网络连接可能很慢， 甚至完全没有网络连接。 幸运的是， 有很多方法可以用来更新随发行版本附带的 FreeBSD 文档的本地副本。

### 25.4.1. 使用 CVSup 来更新文档

FreeBSD 文档的源代码和安装版本都可以通过 CVSup 来以与基本系统 (参考 第 25.7 节 “重新编译 “world””) 类似的方法来升级。 这一节中将会介绍：

*   如何安装联编文档所需的工具集， 用于从源代码来联编 FreeBSD 文档所需的那些工具。

*   如何使用 CVSup 将文档下载到 `/usr/doc`。

*   如何从源代码联编 FreeBSD 文档， 并将其安装到 `/usr/share/doc`。

*   联编文档的过程中支持的一些编译选项， 例如只联编某些语言的版本， 或只联编特定的输出格式。

### 25.4.2. 安装 CVSup 和文档工具集

从源代码联编 FreeBSD 文档需要大量的工具。 这些工具并不是 FreeBSD 基本系统的一部分， 因为这些工具需要占用大量的磁盘空间， 而且并不是对所有 FreeBSD 用户都有用； 只有活跃地撰写 FreeBSD 新文档， 或经常从源代码更新文档的用户才需要这些工具。

全部所需的工具， 均可通过 Ports 套件来安装。 [textproc/docproj](http://www.freebsd.org/cgi/url.cgi?ports/textproc/docproj/pkg-descr) port 是由 FreeBSD 文档计划开发的方便安装和更新这些工具的主 port。

### 注意:

如果不需要 PostScript® 或 PDF 文档的话， 也可以考虑安装 [textproc/docproj-nojadetex](http://www.freebsd.org/cgi/url.cgi?ports/textproc/docproj-nojadetex/pkg-descr) port。 这套文档工具集包含除了 teTeX typesetting 引擎之外的其他全部工具。 teTeX 是一个很大的工具集， 因此如果不需要 PDF 输出的话， 排除它会节省很多时间和磁盘空间。

如欲了解关于安装和使用 CVSup 的进一步信息， 请参阅 使用 CVSup。

### 25.4.3. 更新文档源代码

CVSup 工具能够下载文档源代码的原始副本， 您可使用 `/usr/share/examples/cvsup/doc-supfile` 文件作为配置模板来修改。 在 `doc-supfile` 中的默认主机名是一个无效的占位主机名， 但 [cvsup(1)](http://www.FreeBSD.org/cgi/man.cgi?query=cvsup&sektion=1&manpath=freebsd-release-ports) 能够通过命令行来指定主机名， 因此文档源代码可以使用下面的命令从 CVSup 服务器获得：

```
# `cvsup -h cvsup.FreeBSD.org -g -L 2 /usr/share/examples/cvsup/doc-supfile`
```

您应将 *`cvsup.FreeBSD.org`* 改为最近的 CVSup 服务器。 参见 第 A.6.7 节 “CVSup 站点” 关于镜像站点的完整列表。

初始的文档源代码下载需要一些时间， 您需要耐心等待它完成。

后续的更新可以用同样的命令来进行。 由于 CVSup 工具只下载上次运行之后所发生过的更新， 因此在首次运行之后再运行 CVSup 应该是很快的。

在签出源代码之后， 还可以使用另一种由 `/usr/doc` 目录中的 `Makefile` 支持的方法来更新它。 通过在 `/etc/make.conf` 中配置 `SUP_UPDATE`、 `SUPHOST` 和 `DOCSUPFILE`， 可以通过运行：

```
# `cd /usr/doc`
# `make update`
```

来完成更新。 典型的 `/etc/make.conf` 中的 [make(1)](http://www.FreeBSD.org/cgi/man.cgi?query=make&sektion=1&manpath=freebsd-release-ports) 选项是：

```
SUP_UPDATE= yes
SUPHOST?= cvsup.freebsd.org
DOCSUPFILE?= /usr/share/examples/cvsup/doc-supfile
```

### 注意:

将 `SUPHOST` 和 `DOCSUPFILE` 的值使用 `?=` 来指定的好处是使 make 命令行能够覆盖这些选项。 在向 `make.conf` 中增加选项时推荐这样做， 以避免在测试时反复修改这个文件。

### 25.4.4. 文档源代码中可调的选项

FreeBSD 文档的更新和联编系统支持一些方便只更新一部分文档， 或只联编特定格式及译文的选项。 这些选项可以在 `/etc/make.conf` 文件中配置， 也可以通过 [make(1)](http://www.FreeBSD.org/cgi/man.cgi?query=make&sektion=1&manpath=freebsd-release-ports) 工具来指定。

这些选项包括：

`DOC_LANG`

准备联编和安装的语言列表。 例如， 指定为 `en_US.ISO8859-1` 表示只联编英文版的文档。

`FORMATS`

准备输出的格式列表。 目前， 系统支持 `html`、 `html-split`、 `txt`、 `ps`、 `pdf`、 和 `rtf`。

`SUPHOST`

用于用来更新的 CVSup 服务器的主机名。

`DOCDIR`

用于安装文档的目录。 默认为 `/usr/share/doc`。

如欲了解 FreeBSD 中其他可供配置的全局 make 变量， 请参阅 [make.conf(5)](http://www.FreeBSD.org/cgi/man.cgi?query=make.conf&sektion=5&manpath=freebsd-release-ports)。

关于 FreeBSD 文档联编系统的其他详情， 请参阅 FreeBSD 文档计划入门之新手必读部分。

### 25.4.5. 从源代码安装 FreeBSD 文档

在 `/usr/doc` 中下载了最新的文档源代码快照之后， 就可以开始动手联编文档了。

要更新全部 `DOC_LANG` 中定义的语言的文档， 需要执行下面的命令：

```
# `cd /usr/doc`
# `make install clean`
```

如果在 `make.conf` 中配置了正确的 `DOCSUPFILE`、 `SUPHOST` 和 `SUP_UPDATE` 选项， 则可以将更新源代码和安装一步完成：

```
# `cd /usr/doc`
# `make update install clean`
```

如果只需要更新某个特定语言的文档， 可以在 `/usr/doc` 中与之对应的目录中运行 [make(1)](http://www.FreeBSD.org/cgi/man.cgi?query=make&sektion=1&manpath=freebsd-release-ports)：

```
# `cd /usr/doc/en_US.ISO8859-1`
# `make update install clean`
```

此外， 还可以透过 make 变量 `FORMATS` 来控制输出格式， 例如：

```
# `cd /usr/doc`
# `make FORMATS='html html-split' install clean`
```

### 25.4.6. 使用文档 Ports

原作： Marc Fonvieille.

在之前的章节中， 我们已展示了从源代码更新 FreeBSD 文档的方法。 基于源代码的更新的方法可能并不是对于所有的 FreeBSD 系统都可行有效。 编译文档源代码需要一大堆的工具， *文档工具链*， 对于 CVS 的一定了解和从仓库中检出源代码， 还有一些编译已检出代码的手工步骤。 这一章节我们将介绍一种使用 Ports 来更新已安装的 FreeBSD 文档：

*   下载并安装预编译好的文档快照， 而不用在本地编译任何部份 (这样便不再需要安装整个文档工具链了)。

*   下载文档的源代码并使用 ports 框架编译 (使得检出和编译的步骤更容易些)。

这两种更新 FreeBSD 文档的方法都由一组 文档工程组 `<doceng@FreeBSD.org>` 每月更新的 *文档 ports* 提供支持。 这些都列在了 FreeBSD Ports [docs](http://www.freshports.org/docs/) 虚拟分类下面。

#### 25.4.6.1. 编译和安装文档 Ports

文档 ports 使用 ports 的构建框架使得文档的编译变得更加容易。 自动化了检出文档源代码， 配以适合的环境设置和命令行参数运行 [make(1)](http://www.FreeBSD.org/cgi/man.cgi?query=make&sektion=1&manpath=freebsd-release-ports)， 它们使得安装或卸载文档变得就像安装 FreeBSD 其他 port 或二进制包那样容易。

### 注意:

另一个特性便是当在本地编译文档 ports 时， *文档工具链* ports 会被列入依赖关系， 并自动安装。

文档 ports 按以下的方式组织：

*   一个 “主 port”， 在 [misc/freebsd-doc-en](http://www.freebsd.org/cgi/url.cgi?ports/misc/freebsd-doc-en/pkg-descr) 下可以找到这个文档 port。 它是所有文档 ports 的基础。 在默认的情况下， 它只安装英文版文档。

*   一个 “合集 port”， [misc/freebsd-doc-all](http://www.freebsd.org/cgi/url.cgi?ports/misc/freebsd-doc-all/pkg-descr)， 它将构建并安装所有语言版本的所有文档。

*   最后是各种翻译的 “从属 port”， 比如： [misc/freebsd-doc-hu](http://www.freebsd.org/cgi/url.cgi?ports/misc/freebsd-doc-hu/pkg-descr) 是匈牙利文版的文档。 所有这些都基于主 port 并会安装上对应语言的翻译文档。

以 `root` 用户身份运行如下的命令安装文档：

```
# `cd /usr/ports/misc/freebsd-doc-en`
# `make install clean`
```

这将会安装分章节的英文版本 HTML 格式文档 (与`[`www.FreeBSD.org`](http://www.FreeBSD.org)` 上的相同) 到 `/usr/local/share/doc/freebsd` 目录。

##### 25.4.6.1.1. 常见的调节选项

文档 ports 有许多用来修改默认行为的选项。 以下是一段简要列表：

`WITH_HTML`

允许构建 HTML 格式： 每份文档为一个单一的 HTML 文件。 此种文档的文件名视情况而定通常是 `article.html`， 或 `book.html`， 另外附加一些图片。

`WITH_PDF`

允许构建 Adobe® Portable Document Format， 可使用 Adobe® Acrobat Reader®， Ghostscript 或者其他的 PDF 阅读器查阅。 此种文档的文件名视情况而定通常是 `article.pdf` 或 `book.pdf`。

`DOCBASE`

文档将被安装到的目录。默认值 `/usr/local/share/doc/freebsd`。

### 注意:

请注意默认的目录与 CVSup 方法种所使用的目录不同。 这是因为我们正在安装的是一个 port， 而 ports 通常会被安装到 `/usr/local` 目录。 这可以指定 `PREFIX` 变量覆盖默认值。

这是一份简短的关于如何使用以上提到变量来安装 PDF 格式的匈牙利文档：

```
# cd /usr/ports/misc/freebsd-doc-hu
# make -DWITH_PDF DOCBASE=share/doc/freebsd/hu install clean
```

#### 25.4.6.2. 使用文档 Packages

正如上文所述， 从 ports 构建文档需要在本地安装一份文档工具链和一些编译所需的磁盘空间。 当不够资源安装文档工具链， 或者从源代码编译需要太多的磁盘空间时， 我们仍然可以安装预编译好的文档快照的 ports。

文档工程组 `<doceng@FreeBSD.org>` 每个月都会制作 FreeBSD 文档快照的包。 这些二进制包可以通过包工具来操作， 比如 [pkg_add(1)](http://www.FreeBSD.org/cgi/man.cgi?query=pkg_add&sektion=1&manpath=freebsd-release-ports)， [pkg_delete(1)](http://www.FreeBSD.org/cgi/man.cgi?query=pkg_delete&sektion=1&manpath=freebsd-release-ports)， 等等。

### 注意:

当使用二进制包时， 将安装所指定语言相关的 FreeBSD 文档的 *所有* 可用格式。

举例来说， 以下的命令将安装最新预编译的匈牙利语文档：

```
# `pkg_add -r hu-freebsd-doc`
```

### 注意:

二进制包使用了以下与对应 ports 名称不同的命名格式: `lang-freebsd-doc`。 这里的 *`lang`* 是语言代码的简短形式， 比如 `hu` 表示匈牙利语， 或者 `zh_cn` 表示简体中文。

#### 25.4.6.3. 更新文档 Ports

任何用于更新 ports 的工具都可以被用来更新已安装的文档 port。 举例来说， 下面的命令通过 [ports-mgmt/portupgrade](http://www.freebsd.org/cgi/url.cgi?ports/ports-mgmt/portupgrade/pkg-descr) 工具来更新已安装的匈牙利语文档二进制包。

```
# `portupgrade -PP hu-freebsd-doc`
```

## 25.5. 追踪开发分支

FreeBSD 有两个开发分支： FreeBSD-CURRENT 和 FreeBSD-STABLE。 这一章节将对每个分支作相应介绍与如何保持你的系统更新。 我们将先介绍 FreeBSD-CURRENT 然后是 FreeBSD-STABLE。

### 25.5.1. 使用最新的 FreeBSD CURRENT

这里再次强调， FreeBSD-CURRENT 是 FreeBSD 开发的 “最前沿”。 FreeBSD-CURRENT 用户要有较高的技术能力， 并且应该有能力自已解决困难的系统问题。 如果您是个 FreeBSD 新手， 那么在安装之前最好三思。

#### 25.5.1.1. FreeBSD-CURRENT 是什么？

FreeBSD-CURRENT 是 FreeBSD 的发展前沿。 包括了在下一个官方发行的软件中可能存在， 也可能不存在的发展、 试验性改动、 以及过渡性的机制。 尽管许多 FreeBSD 开发者每天都会编译 FreeBSD-CURRENT 源代码， 但有时这些代码仍然会是不能编译的。 虽然这些问题会很快解决， 但 FreeBSD-CURRENT 是带来破坏还是您正希望的功能性改善， 很可能完全取决于您获取源代码的的时机！

#### 25.5.1.2. 谁需要 FreeBSD-CURRENT？

FreeBSD-CURRENT 适合下边三种主要兴趣团体：

1.  FreeBSD 社区的成员： 积极工作在源码树的某部分的人和为保持 “最新” 为绝对需求的人。

2.  FreeBSD 社区的成员： 为促使 FreeBSD-CURRENT 保持尽可能的健全而愿花时间去解决问题的积极的测试者； 以及那些愿意提出关于 FreeBSD 变化和总体方向的建设性建议并且提供补丁实现它们的人们。

3.  那些只是想关注或为了参考目的使用当前 (current) 源码的人们 (如，为了*阅读*，而不是执行)。 这些人也偶尔做做注释或贡献代码。

#### 25.5.1.3. FreeBSD-CURRENT *不是*什么？

1.  追求最新功能， 您听说里面有一些很酷的新功能， 并希望成为您周围的人中第一个尝试它们的人。 尽管您能够因此首先了解到最新的功能， 但这也意味着在出现新的 bug 时您也首当其冲。

2.  修复错漏的快捷方式。任何 FreeBSD-CURRENT 的既定版本在修复已知错漏的同时又可能会产生新的错漏。

3.  无所不在的“官方支持”。 我们尽最大努力在 3 个“合法的” FreeBSD-CURRENT 组之一真诚给人们提供帮助，但是我们 *没有时间*提供技术支持。 这并不是因为我们是那种不喜欢帮助人解困的无耻之徒 (如果我们是的话，就不会制作 FreeBSD 了)。 我们不能每天简单地回复上百的消息，*而且* 我们继续发展 FreeBSD！ 在改善 FreeBSD 和回复大量关于实验代码的问题之间如果要做个选择的话， 开发人员会选择前者。

#### 25.5.1.4. 使用 FreeBSD-CURRENT

1.  加入 [freebsd-current](http://lists.FreeBSD.org/mailman/listinfo/freebsd-current) 和 [svn-src-head](http://lists.FreeBSD.org/mailman/listinfo/svn-src-head) 列表。 这个不仅仅是个好主意，而且很 *重要*。如果您不去 *[freebsd-current](http://lists.FreeBSD.org/mailman/listinfo/freebsd-current)*， 您就不会看到人们所做的关于系统当前状态的说明， 这样您就有可能在别人已经发现并解决了的一大堆问题面前难倒。 更重要的是您会错过一些重要的公告---对于您的系统安全可能是至关重要的。

    [svn-src-head](http://lists.FreeBSD.org/mailman/listinfo/svn-src-head) 列表允许您看到每个变化的提交记录， 因为这些记录与其它相关信息是同步的。

    要加入这些列表，或其它可能的列表，请访问 [`lists.FreeBSD.org/mailman/listinfo`](http://lists.FreeBSD.org/mailman/listinfo) ，并且点击您想订阅的列项。 关于其它步骤的说明那里有提供。 如果你有兴趣追踪整个原代码树的变更记录， 我们建议你订阅 [svn-src-all](http://lists.FreeBSD.org/mailman/listinfo/svn-src-all) 邮件列表。

2.  从 FreeBSD 镜像站点 获取源码。 您有两种方式选择：

    1.  与称作 `standard-supfile` 的 `supfile` 一起使用 cvsup，这个可以从 `/usr/share/examples/cvsup`得到。 这是最被推荐的方式，因为它允许您一次获取整个集合， 以后就只取更改过的部分。许多人从 `cron` 运行 `cvsup`，以保持他们的源码自动更新。 您须要定制上边的 `supfile` 样本，并且配置 cvsup 以适应您的环境。

        ### 注意:

        `standard-supfile` 例子是为追踪指定的 FreeBSD 安全分支而指定的， 而不是 FreeBSD-CURRENT。 你需要编辑这个文件并把如下这行：

        ```
        *default release=cvs tag=RELENG_*`X`*_*`Y`*
        ```

        替换为：

        ```
        *default release=cvs tag=.
        ```

        可以参阅手册中的 CVS Tags 章节获得更多可用 tag 的详细说明。

    2.  使用工具 CTM。 如果您的连接性能不太好(高价连接或只能通过电子邮件存取)， CTM 是个选择。 但这也颇有争议并且常常得到到坏文件。因此很少使用它， 这也注定了不能长期用它来工作。对于使用 9600 bps 或更快连接的人，我们推荐使用 CVSup。

3.  如果您获取源码是用于运行，而不只是看看，那么就获取 *整个* FreeBSD-CURRENT，不要选部分。 这样做的原因是源码的大部分都依赖于其他部分， 要是您试着只编译其中一部分的话，保证您会陷入麻烦。

    在编译 FreeBSD-CURRENT 之前，请仔细阅读 `/usr/src` 里的 `Makefile` 文件。 尽管是部分的升级过程，您至少也要首先安装新的内核和重建系统。阅读 [FreeBSD-CURRENT 邮件列表](http://lists.FreeBSD.org/mailman/listinfo/freebsd-current) 邮件列表和 `/usr/src/UPDATING`， 会让您在其它循序渐进的过程中保持最新， 这对于我们向下一个发行版转移是很有必要的。

4.  热心一点！如果您正运行 FreeBSD-CURRENT， 我们很想知道您关于它的一些想法， 尤其是关于错漏修复或增进的建议。 非常欢迎带有代码的建议！

### 25.5.2. 使用最新的 FreeBSD STABLE

#### 25.5.2.1. FreeBSD-STABLE 是什么?

FreeBSD-STABLE 是我们的发展分支，我们的主要发行版就由此而来。 这个分支会以不同速度变化，并且假定这些是第一次进入 FreeBSD-CURRENT 进行测试。然而，这 *仍然* 是个发展中的分支，这意味着在一定的时候，FreeBSD-STABLE 源码可能或不可能满足一些特殊的要求。 它只不过是另一个工程发展途径，并不是终端用户的资源。

#### 25.5.2.2. 谁需要 FreeBSD-STABLE？

如果您有兴趣追随 FreeBSD 的开发过程或为其做点贡献， 尤其是和下一个 “非计划” 的 FreeBSD 发行版有关时， 您应该考虑采用 FreeBSD-STABLE。

尽管安全更新也会进入 FreeBSD-STABLE 分支，但您并不 *必须* 使用 FreeBSD-STABLE 来达到这样的目的。 每一个 FreeBSD 的安全公告都会解释如何修复受到影响的发行版中的问题 [^([12])](#ftn.idp84468976)，而因为安全原因而去采用一个开发分支显然可能会同时引入一些不希望的修改。

尽管我们尽力确保 FreeBSD-STABLE 分支在任何时候都能够正确编译和运行， 但没有人能够担保它在任何时候都总可以。 此外， 尽管代码在进入 FreeBSD-STABLE 之前都是在 FreeBSD-CURRENT 上完成开发， 但使用 FreeBSD-STABLE 的人要比使用 FreeBSD-CURRENT 的更多。 有证据显示， 犄角旮旯里的各种问题有些时候仍然会由于在 FreeBSD-CURRENT 不那么明显 而在 FreeBSD-STABLE 暴露出来。

基于这些原因， *不* 推荐您盲目地追随 FreeBSD-STABLE， 并且， 在粗略地测试过代码之前不要更新任何生产服务器到 FreeBSD-STABLE 也非常重要。

如果您没有用于完成这些工作的资源， 我们推荐您使用最新的 FreeBSD 发行版， 并使用发行版提供的二进制更新机制来在发行版之间完成迁移。

#### 25.5.2.3. 使用 FreeBSD-STABLE

1.  加入 [freebsd-stable](http://lists.FreeBSD.org/mailman/listinfo/freebsd-stable) 列表。让您随时了解可能出现在 FreeBSD-STABLE 里的“build 依赖性”或其它需要特别注意的问题。 当开发员正在考虑某些有争议的修复或更新时， 他们就会在这个邮件列表里发表声明，给用户机会回应， 看他们对于提出的变化是否还有什么问题。

    加入相关的 SVN 列表来追踪你所关心的分支。比如，如果你在追踪 7-STABLE 分支，加入 [svn-src-stable-7](http://lists.FreeBSD.org/mailman/listinfo/svn-src-stable-7) 列表。 这样每次这个分支上有改动的时候就能让你看到提交记录, 还包括了修改可能引起的副作用之类的相关信息。

    要加入这些列表或其他可用的，访问 [`lists.FreeBSD.org/mailman/listinfo`](http://lists.FreeBSD.org/mailman/listinfo) 并点击您希望订阅的列表。关于其它步骤的说明可以在那里看到。 如果你有兴趣追踪整个原代码树的变更记录， 我们建议你订阅 [svn-src-all](http://lists.FreeBSD.org/mailman/listinfo/svn-src-all) 邮件列表。

2.  如果您正安装一个新系统， 并希望它运行每月从 FreeBSD-STABLE 编译的快照， 请察看 Snapshots 网页以了解更多信息。 另外， 也可以从 镜像站点 安装最新的 FreeBSD-STABLE 发行版， 并按照其中的说明将系统更新到最新的 FreeBSD-STABLE 源代码。

    如果您已经在运行较早的 FreeBSD 版本， 并希望通过源代码方式升级， 则可以通过 FreeBSD 镜像站点 来完成。 这可以通过两种方式来进行：

    1.  与称作 `stable-supfile` 的 `supfile` 一起使用 cvsup，这个可以从 `/usr/share/examples/cvsup` 得到。 这是最被推荐的方式，因为它允许您一次获取整个集合， 以后就只取更改过的部分。许多人从 `cron` 运行 `cvsup`，以保持他们的源码自动更新。 您须要定制上边的 `supfile` 样本，并且配置 cvsup 以适应您的环境。

    2.  使用工具 CTM。 如果您的连接性能不太好(高价连接或只能通过电子邮件存取)， CTM 是个选择。 但这也颇有争议并且常常得到到坏文件。因此很少使用它， 这也注定了不能长期用它来工作。对于使用 9600 bps 或更快连接的人，我们推荐使用 CVSup。

3.  本质上说，如果您需要快速存取源码并且不计较通信宽带的话，可以使用 `cvsup` 或 `ftp`。否则，就使用 CTM。

4.  在编译 FreeBSD-STABLE 之前，请仔细阅读 `/usr/src` 里的 `Makefile`。 您至少应该安装一个新的内核并重建系统， 首先做为升级过程的一部分。阅读 [FreeBSD-STABLE 邮件列表](http://lists.FreeBSD.org/mailman/listinfo/freebsd-stable) 邮件列表和 `/usr/src/UPDATING`， 可能让您在其它循序渐进的过程中保持更新， 这在我们向下一发行版转移时是很有必要的。

* * *

[^([12])](#idp84468976) 这也不总是正确。我们不可能永远支持 FreeBSD 的旧发行版， 尽管我们会在发布之后支持他们数年之久。 关于 FreeBSD 目前对于旧发行版的支持政策的完整描述， 请参见 `www.FreeBSD.org/security/`。

## 25.6. 同步您的源码

有许多方式通过互联网(或电子邮件)与 FreeBSD 项目源码特定领域或所有领域保持更新，主要依赖于您的兴趣。 我们提供的主要服务是匿名 CVS、 CVSup，和 CTM。

### 警告:

虽然只更新源码树中的部分是可能的， 唯一被支持的更新过程是更新整个树、并且重编译用户区 (如：在用户空间运行的所有程序，像 `/bin` 和 `/sbin`下边的)和内核源码。 只更新源码树中的部分，或只有内核，或只有用户区 (userland) 通常会出现错误。这些问题包括有编译错误、内核崩溃 (kernel panics)、数据出错。

匿名 CVS 和 CVSup 使用 *下拉(pull)* 模式来更新源代码。 在 CVSup 中， 用户 (或者 `cron` 脚本) 会调用 `cvsup` 程序， 后者会同某一个 `cvsupd` 服务进行交互， 以更新您的文件。 您接到的更新是更新时刻最新的， 并且您只会收到那些需要的更新。 您可以很容易地限制更新的范围， 只更新那些您需要的文件。 服务器端会根据您手头已经有的文件即时地生成更新内容。 匿名 CVS 相对于 CVSup 而言要简单一些， 因为它只是对 CVS 的一种扩展， 让您可以从远程的 CVS 代码库得到更新。 CVSup 相对而言， 要比 匿名 CVS 更有效率， 然而后者却更容易使用。

另一种方法是 CTM。 这种方法并不能将您手头的代码与中央代码库中的版本进行比较， 也不能下载它们。 在主 CTM 服务器上运行的脚本会每天执行多次， 每次运行都能够自动地识别所有文件自上次运行以来所发生的变化， 如果发现有文件发生了变动， 就会压缩、 标上一个序列号， 并进行便于使用电子邮件进行传送的编码操作 (其中只包括可打印的 ASCII 字符)。 一旦接收到， 这些“CTM deltas”就会被传送给 [ctm_rmail(1)](http://www.FreeBSD.org/cgi/man.cgi?query=ctm_rmail&sektion=1&manpath=freebsd-release-ports) 工具---可以自动进行解码、校验和应用这些变化到用户的复制的源码里。 这个过程比 CVSup 更为有效， 而且更少占用我们的服务器资源，因为它不仅仅采用 *下拉(pull)* 模式，还采用 *上推(push)* 模式。

当然， 这样做也会带来一些不便。 如果您不经意删除了您的压缩包的部分内容， CVSup 会检测到并为您重建破坏的部分。 CTM 是不会这样做的， 如果您删除了您的源码树中的某部分(并已不能恢复)， 那么您就必须从破坏处 (从最新的 CVS “base delta”) 开始，使用 CTM 或 匿名 CVS 进行重建，仅仅删除坏的数据并再同步。

## 25.7. 重新编译 “world”

只要您根据一定版本的 FreeBSD (FreeBSD-STABLE、FreeBSD-CURRENT 等等)， 已经同步了您本地的源码树，那么您就可以使用这些源码树来重建系统。

### 做好备份:

无需强调在行动 *之前* 备份整个系统是多么的重要。 尽管重新编译系统是 (如果您按照文档的指示做的话) 一件很容易完成的工作， 但出错也是在所难免的， 另外， 别人在源码里面引入的错误也可能造成系统无法引导。

请确信自己已经做过备份， 并且在手边有恢复软盘或可以引导的光盘。 您可能永远也不会用到它， 但安全第一嘛！

### 订阅恰当的邮件列表:

FreeBSD-STABLE 和 FreeBSD-CURRENT 分支自然是 *发展中的*。为 FreeBSD 做贡献的都是人，偶尔也会犯错误。

有时这些错误没什么危害，只是引起您的系统生成新的诊断警告。 有时是灾难性的，并导致您的系统不能启动或破坏您的文件系统 (甚至更糟)。

如果出现了类似的问题， 贴一封“小心(heads up)”帖到相关的邮件列表里， 讲清问题的本质以及受影响的系统。在问题解决后，再贴封“解除(all clear)”声明。

如果使用 FreeBSD-STABLE 或 FreeBSD-CURRENT 而又不阅读 [FreeBSD-STABLE 邮件列表](http://lists.FreeBSD.org/mailman/listinfo/freebsd-stable) 和 [FreeBSD-CURRENT 邮件列表](http://lists.FreeBSD.org/mailman/listinfo/freebsd-current) 各自的邮件列表， 那么您是自找麻烦。

### 不要使用 `make world`:

许多较早的文档推荐使用 `make world` 来完成这项工作。 这样做会跳过一些必要的步骤， 因此只有在您知道自己在做什么的时候才可以这样做。 几乎所有的情况下 `make world` 都是不应该做的事情， 您应该使用这里描述的方法。

### 25.7.1. 更新系统的规范方法

在更新系统时， 一定要首先查看 `/usr/src/UPDATING` 文件， 以便了解在 buildworld 之前需要进行的操作， 然后按照下面列出的步骤进行操作：

这些更新步骤假定您使用的是包含旧编译器、 内核以及用户态工具及配置的旧版 FreeBSD。 我们使用 “world” 来表示系统中的核心执行文件、 函数库和程序文件。 编译器是 “world” 的一部分， 但有其特殊性。

此外， 我们还假定您已经获得了较新版本操作系统的源代码。 如果您正更新的系统中的源代码也是旧版系统所附带的， 您还需要参阅 第 25.6 节 “同步您的源码” 来把代码同步到较新的版本。

从源代码更新系统， 有时会比初看上去的时候更麻烦一些， 另一方面， FreeBSD 的开发人员有时会不得不修改推荐的更新步骤， 特别是当出现了一些无法避免的依赖关系的时候。 这一节余下的部分， 将介绍目前推荐的更新步骤背后的原理。

成功的更新操作必须解决下面的这些问题：

*   旧的编译器可能无法编译新的内核。 (另一方面， 旧的编译器很可能有 bug。) 因此， 新的内核应该以新的编译器编译。 更具体地说， 新的编译器应在新内核开始联编之前已经完成了联编步骤。 请注意， 新的编译器并不一定需要在联编新内核之前 *安装* 到系统中。

*   新的 world 有可能依赖一些新的内核特性。 因此， 新内核必须在新的 world 之前安装。

这两个问题就是为什么我们将在后面的章节中介绍的， 需要按照 `buildworld`、 `buildkernel`、 `installkernel`、 `installworld` 的顺序来更新系统的原因。 这并不是您需要遵守推荐的更新操作的全部原因， 除了这两个最重要的理由之外， 还有一些并不那么显而易见的原因：

*   旧的 world 可能无法配合新的内核正常工作， 因此， 您在安装完新内核之后， 应尽快将 world 也随之更新。

*   有些配置文件的变动必须在安装新的 world 之前完成， 而另一些配置文件的变动则有可能导致旧 world 工作不正常。 因此， 通常而言会需要两次不同的配置文件更新步骤。

*   多数情况下， 更新步骤只会替换或增加文件； 换言之， 现有的旧文件并不会被删除。 有时， 这可能会导致一些其他问题。 因此， 有时安装操作会指明， 必须在某些操作之前手工删除一些文件。 这些在未来可能会被自动化， 也可能不会自动化。

由于有这些考虑， 因此一般情况下我们建议使用下列更新步骤。 请注意， 具体的更新操作中可能会需要一些附加的步骤， 但核心的过程应该是不会轻易发生变化的：

1.  `make buildworld`

    这步操作会联编新的编译器， 以及少量相关工具， 并在随后使用新的编译器来联编 world。 联编的结果会存放在 `/usr/obj`。

2.  `make buildkernel`

    与旧式的、 使用 [config(8)](http://www.FreeBSD.org/cgi/man.cgi?query=config&sektion=8&manpath=freebsd-release-ports) 和 [make(1)](http://www.FreeBSD.org/cgi/man.cgi?query=make&sektion=1&manpath=freebsd-release-ports) 的方法不同， 这种做法会使用存放于 `/usr/obj` 中的 *新的* 编译器。 这种做法使得您免去了由于编译器与内核源代码不一致导致的问题。

3.  `make installkernel`

    安装新的内核及其模块， 使系统能够以更新后的内核启动。

4.  重启系统并进入单用户模式。

    单用户模式使得更新正在运行的软件可能导致的问题减到最少。 此外， 它也使配合新内核运行旧 world 可能出现的问题减到最少。

5.  `mergemaster -p`

    这步操作会进行完成安装新的 world 所需的配置文件更新操作。 例如， 它可能会在系统的密码数据库中添加新的用户组或用户。 这些操作通常在上次更新之后增加了新的用户组或特殊系统用户之后是需要的， 因为 `installworld` 这步操作会需要这些用户或组才能顺利完成。

6.  `make installworld`

    从 `/usr/obj` 中复制 world。 这步操作之后， 您在盘上的系统， 包括内核和 world 就都是新的了。

7.  `mergemaster`

    更新余下的配置文件， 因为您的 world 已经更新完成了。

8.  重启系统。

    这步操作将加在新的内核， 以及新的 world 和更新过的配置文件。

注意， 如果您正从同一 FreeBSD 版本分支升级， 例如， 从 7.0 到 7.1， 则上述过程可能没有那么必要， 因为您不太可能遇到严重的编译器、 内核源代码、 用户态程序源代码或配置文件不匹配的情形。 旧式的 `make world` 然后再联编新内核的升级方法， 很可能有机会能够正常运作而完成升级工作。

但是， 在大版本升级的过程中， 不按照前面所介绍的操作来进行升级时， 便很可能遇到一些问题。

此外， 还需要注意的是， 有些时候升级的过程中 (例如从 4.*`X`* 到 5.0) 可能会需要一些额外的步骤 (例如在 installworld 之前更名或删除一些文件)。 请仔细阅读 `/usr/src/UPDATING` 这个文件， 特别是它的结尾部分所介绍的推荐的升级操作顺序。

由于开发人员发现不可能完全避免一些不匹配方面的问题， 这个过程一直在演化过程中。 不过幸运的是， 目前推荐的这个升级步骤， 应该能够在很长一段时间内不需要做任何调整。

总结一下， 目前推荐的从源代码升级 FreeBSD 的方法是：

```
# `cd /usr/src`
# `make buildworld`
# `make buildkernel`
# `make installkernel`
# `shutdown -r now`
```

### 注意:

有时， 可能需要额外地执行一次 `mergemaster -p` 才能够完成 `buildworld` 步骤。 这些要求， 会在 `UPDATING` 中进行描述。 一般而言， 您可以简单地跳过这一步， 只要进行的不是大跨度的 FreeBSD 版本升级。

在 `installkernel` 成功完成之后， 您需要引导到单用户模式 (举例而言， 可以在加载器提示后输入 `boot -s`)。 接下来执行：

```
# `adjkerntz -i`
# `mount -a -t ufs`
# `mergemaster -p`
# `cd /usr/src`
# `make installworld`
# `mergemaster`
# `reboot`
```

### 阅读进一步的说明:

前面所给出的， 只是帮助您开始工作的简要说明。 要清楚地理解每一步， 特别是如果打算自行定制内核配置， 就应阅读下面的内容。

### 25.7.2. 阅读 `/usr/src/UPDATING`

在您做其它事之前，请阅读 `/usr/src/UPDATING` (或在您的源码里的等效的文件)。 这个文件要包含有关于您可能遇到的问题的重要信息， 或指定了您可能使用到的命令的执行顺序。如果 `UPDATING` 与您这里读到相矛盾，那就先依据 `UPDATING`。

### 重要:

正如先前所述，阅读 `UPDATING` 并不能替代订阅正确的邮件列表。两都是互补的，并不彼此排斥。

### 25.7.3. 检查 `/etc/make.conf`

检查 `/usr/share/examples/etc/make.conf` 以及 `/etc/make.conf`。 第一个文件包含了一些默认的定义 – 它们中的绝大多数都注释掉了。 为了在重新编译系统时能够使用它们， 请把这些选项加入到 `/etc/make.conf`。 请注意在 `/etc/make.conf` 中的任何设置同时也会影响每次运行 `make` 的结果， 因此设置一些适合自己系统的选项是一个好习惯。

一般的用户通常会从 `/usr/share/examples/etc/make.conf` 复制 `CFLAGS` 和 `NO_PROFILE` 这样的设置到 `/etc/make.conf` 中并令它们生效。

请考虑其他的一些选项 (例如 `COPTFLAGS`、 `NOPORTDOCS` 等等)， 看看是否合用。

### 25.7.4. 更新 `/etc` 里的文件

`/etc` 目录包含有除了您的系统启动时执行的脚本外大部分的系统配置信息。 有些脚本随 FreeBSD 的版本而不同。

有些配置文件在天天运行的系统里也是要使用到的。尤其是 `/etc/group`。

偶尔， 作为安装过程的一部分， `make installworld` 会要求事先创建某些特定的用户或组。 在进行升级时， 它们可能并不存在。 这会给升级造成问题。 有时， `make buildworld` 会检查它们是否已经存在。

最近就有个这样的例子， 当时新增了 `smmsp` 用户。 当用户尝试完成安装操作时， 在 [mtree(8)](http://www.FreeBSD.org/cgi/man.cgi?query=mtree&sektion=8&manpath=freebsd-release-ports) 尝试建立 `/var/spool/clientmqueue` 时失败了。

解决办法是通过使用 `-p` 选项以构建前 (pre-buildworld) 模式运行 [mergemaster(8)](http://www.FreeBSD.org/cgi/man.cgi?query=mergemaster&sektion=8&manpath=freebsd-release-ports)。 这表示只对比那些对于成功执行 `buildworld` 或 `installworld` 起关键作用的文件。 在第一次这样做时， 如果使用的是早期的不支持 `-p` 的 `mergemaster` 版本的话， 使用源码中的新版本即可。

```
# `cd /usr/src/usr.sbin/mergemaster`
# `./mergemaster.sh -p`
```

### 提示:

如果您是个偏执狂 (paranoid)， 您可以检查您的系统看看哪个文件属于您已更名或删除了的那个组。

```
# `find / -group GID -print`
```

将显示所有 *`GID`* 组 (可以是组名也可以是数字地组 ID)所有的文件。

### 25.7.5. 改为单用户模式

您可能想在单用户模式下编译系统。 除了对更快处理事情显然有好处外， 重装系统将触及许多重要的系统文件， 包括所有标准系统二进制文件、库文件、包含 (include) 文件等等。 在正运行的系统 (尤其是在有活跃的用户的时候) 中更改这些文件是自寻烦恼。

另一种模式是在多用户模式下编译系统，然后转换到单用户模式下安装。 如果您喜欢这种方式，只需在建立 (build) 完成后才执行下边的步骤。 您推迟转换到单用户模式下直到您必须 `installkernel` 或 `installworld`。

从运行的系统里，以超级用户方式执行：

```
# `shutdown now`
```

这样就会转换到单用户模式。

除此之外， 也可以重启系统， 并在启动菜单处选择 “single user”(单用户) 选项。 这样系统将以单用户模式启动。 接着， 在 shell 提示符处执行：

```
# `fsck -p`
# `mount -u /`
# `mount -a -t ufs`
# `swapon -a`
```

这会检查文件系统，重新将 `/` 以读/写模式挂接， 参考 `/etc/fstab` 挂接其它所有的 UFS 文件系统，然后启用交换区。

### 注意:

如果您的 CMOS 时钟是设置为本地时间，而不是 GMT (如果 [date(1)](http://www.FreeBSD.org/cgi/man.cgi?query=date&sektion=1&manpath=freebsd-release-ports) 命令输出不能显示正确的时间和地区也确有其事)， 您可能也需要执行下边的命令：

```
# `adjkerntz -i`
```

这样可以确定您正确的本地时区设置──不这样做， 您以后可能会碰到一些问题。

### 25.7.6. 删除 `/usr/obj`

随着重新构建系统的进行， 编译结果会放到 (默认情况下) `/usr/obj` 下。 这些目录会映射到 `/usr/src`。

通过删除这个目录， 可以加速 `make buildworld` 的过程， 并避免相互依赖关系等复杂的问题。

`/usr/obj` 中的某些文件可能设置了不可改标记 (详情参见 [chflags(1)](http://www.FreeBSD.org/cgi/man.cgi?query=chflags&sektion=1&manpath=freebsd-release-ports))， 需要首先去掉这些标志。

```
# `cd /usr/obj`
# `chflags -R noschg *`
# `rm -rf *`
```

### 25.7.7. 重新编译基本系统

#### 25.7.7.1. 保存输出

建议把执行 [make(1)](http://www.FreeBSD.org/cgi/man.cgi?query=make&sektion=1&manpath=freebsd-release-ports) 后得到的输出存成一个文件。 如果什么地方出了错，您就会有个错误信息的备份。 尽管这样不能帮您分析哪里出了错， 但如果您把您的问题贴到某个邮件列表里就能帮助其他的人。

这样做最简单的办法是使用 [script(1)](http://www.FreeBSD.org/cgi/man.cgi?query=script&sektion=1&manpath=freebsd-release-ports) 命令，同是带上参数指定存放输出的文件名。 您应在重建系统之前立即这样做，然后在过程完成时输入 **`exit`**。

```
# `script /var/tmp/mw.out`
Script started, output file is /var/tmp/mw.out
# `make TARGET`
*… compile, compile, compile …*
# `exit`
Script done, …
```

如果您这样做，就 *不要* 把文件存到 `/tmp` 里边。下次启动时，这个目录就会被清除掉。 存放的最好地方是 `/var/tmp` (如上个实例)或 `root` 的主目录。

#### 25.7.7.2. 编译基本系统

您必须在`/usr/src`目录里边：

```
# `cd /usr/src`
```

(当然，除非您的源码是在其它地方，真是这样的话更换成那个目录就行了)。

使用 [make(1)](http://www.FreeBSD.org/cgi/man.cgi?query=make&sektion=1&manpath=freebsd-release-ports) 命令重建系统。这个命令会从 `Makefile` (描述组成 FreeBSD 的程序应该怎样被重建， 以什么样的顺序建立等等) 里读取指令。

输入的一般命令格式如下：

```
# `make -x -DVARIABLE target`
```

这个例子里，`-*`x`*` 是会传递给 [make(1)](http://www.FreeBSD.org/cgi/man.cgi?query=make&sektion=1&manpath=freebsd-release-ports) 的一个选项。查看 [make(1)](http://www.FreeBSD.org/cgi/man.cgi?query=make&sektion=1&manpath=freebsd-release-ports) 手册有您可用的选项例子。

`-D*`VARIABLE`*` 传递一个变量给 `Makefile`。这些变量控制了 `Makefile` 的行为。这些同 `/etc/make.conf` 设置的变量一样， 只是提供了另一种设置它们的方法。

```
# `make -DNO_PROFILE target`
```

是另一种指定不被建立 (built) 的先定库 (profiled libraries) 的方式，协同 `/etc/make.conf` 里的

```
NO_PROFILE=    true 	#    避免编译性能分析库
```

一起使用。

*`目标 (target)`* 告诉 [make(1)](http://www.FreeBSD.org/cgi/man.cgi?query=make&sektion=1&manpath=freebsd-release-ports) 什么该做。每个 `Makefile` 定义了一定数量不同的“目标 (targets)”， 然后您选择的目标就决定了什么会发生。

有些目标列在 `Makefile` 里的，但并不意味着您要执行。相反，建立过程 (build process) 利用它们把重建系统的一些必要的步骤分割成几个子步骤。

大部分的时间不需要向 [make(1)](http://www.FreeBSD.org/cgi/man.cgi?query=make&sektion=1&manpath=freebsd-release-ports) 传递参数，因此您的命令看起来可能象这样：

```
# `make target`
```

此处 *`target`* 表示的是若干编译选项。 多数情况下， 第一个 target 都应该是 `buildworld`。

正如名字所暗示的，`buildworld` 在 `/usr/obj` 下边建立了一个全新的树， 然后使用另一个 target， `installworld` 在当前的机器里安装它。

将这些选项分开有两个优点。 首先， 它允许您安全地完成建立 (build)， 而不对正在运行的系统的组件产生影响。 构建过程是 “自主的 (self hosted)”。 因为这样， 您可以安全地在以多用户模式运行的机器里执行 `buildworld` ，而不用当心不良影响。 但是依然推荐您在单用户模式时运行 `installworld`。

第二，允许您使用 NFS 挂接 (NFS mounts) 升级您网络里的多台计算机。如果您有三台 `A`、`B` 和 `C` 想进行升级，在`A` 执行 `make buildworld` 和 `make installworld`。 然后将 `A` 上的 `/usr/src` 和 `/usr/obj` 通过 NFS 挂接到 `B` 和 `C` 上， 接下来， 只需在 `B` 和 `C` 上使用 `make installworld` 来安装构建的结果就可以了。

尽管 `world` target 仍然存在，强烈建议您不要用它。

运行

```
# `make buildworld`
```

我们提供了一个试验性的功能， 可以在构建过程中为 `make` 指定 `-j` 参数， 令其在构建过程中同时启动多个并发的进程。 对于多 CPU 的机器而言， 这样做有助于发挥其性能。 不过， 由于编译过程中的瓶颈主要是在 IO 而不是 CPU 上， 因此它也会对单 CPU 的机器带来好处。

对典型的单 CPU 机器， 可以使用：

```
# `make -j4 buildworld`
```

这样， [make(1)](http://www.FreeBSD.org/cgi/man.cgi?query=make&sektion=1&manpath=freebsd-release-ports) 会最多同时启动 4 个进程。 从发到邮件列表中的经验看， 这样做能带来最佳的性能。

如果您使用的机器有多颗 CPU， 并且配置了 SMP 的内核， 也可以试试看 6 到 10 的数值， 并观察是否能带来构建性能上的改善。

#### 25.7.7.3. 耗时

联编基本系统所需的时间会受到很多因素的影响， 不过， 较新的机器应该都能在一两个小时之内完成 FreeBSD-STABLE 源代码的构建， 而无须任何技巧或捷径。 完成 FreeBSD-CURRENT 源代码的联编， 则通常需要更长一些的时间。

### 25.7.8. 编译和安装新内核

要充分利用您的新系统，您应该重新编译内核。 这是很有必要的，因为特定的内存结构已经发生了改变，像 [ps(1)](http://www.FreeBSD.org/cgi/man.cgi?query=ps&sektion=1&manpath=freebsd-release-ports) 和 [top(1)](http://www.FreeBSD.org/cgi/man.cgi?query=top&sektion=1&manpath=freebsd-release-ports) 这样的程序会不能工作， 除非内核同源码树的版本是一样的。

最简单、最安全的方式是 build 并安装一个基于 `GENERIC` 的内核。虽然 `GENERIC` 可能没有适合您的系统的所有必要的设备， 但它包括了启动您的系统到单用户模式所必需的内容。 这是个不错的检测新系统是否工作正常的测试。在从 `GENERIC` 启动、核实系统可以工作后， 您就可以建立 (build) 一个基于您的正常内核配置文件的新的内核了。

在 FreeBSD 中， 首先完成 build world 然后再编译新内核非常重要。

### 注意:

如果您想建立一个定制内核，而且已经有了配置文件， 只需象这样使用 `KERNCONF=MYKERNEL：`

```
# `cd /usr/src`
# `make buildkernel KERNCONF=MYKERNEL`
# `make installkernel KERNCONF=MYKERNEL`
```

注意，如果您已把 `内核安全级别(kern.securelevel)` 调高到了 1 以上，而且还设置了 `noschg` 或相似的标识到了您的内核二进制里边，您可能会发现转换到单用户模式里使用 `installkernel` 是很有必要的。 如果您没有设置它， 则应该也能毫无问题地在多用户模式执行这两个命令。 请参考 [init(8)](http://www.FreeBSD.org/cgi/man.cgi?query=init&sektion=8&manpath=freebsd-release-ports) 以了解更多关于 `内核安全级(kern.securelevel)` 的信息；查看 [chflags(1)](http://www.FreeBSD.org/cgi/man.cgi?query=chflags&sektion=1&manpath=freebsd-release-ports) 了解更多关于不同文件标识的信息。

### 25.7.9. 重启到单用户模式

您应该单用户模式测试新内核。照第 25.7.5 节 “改为单用户模式”处的说明去做。

### 25.7.10. 安装编译好的新系统

您现在应使用 `installworld` 来安装新的系统二进制。

执行

```
# `cd /usr/src`
# `make installworld`
```

### 注意:

如果在 `make buildworld` 的命令行指定了变量，您就必须在 `make installworld` 命令行里指定同样的变量。 对于其它的选项并不是必需的，如，`-j` 就不能同 `installworld` 一起使用。

举例，您执行了：

```
# `make -DNO_PROFILE buildworld`
```

您就必须使用：

```
# `make -DNO_PROFILE installworld`
```

来安装结果，否则就要试着安装先定 (profiled) 的在 `make buildworld` 阶段没有建立 (built) 的二进制文件。

### 25.7.11. 不是由 `make installworld` 更新的更新文件

重新编译整个系统不会使用新的或改过的配置文件更新某些目录 (尤其像 `/etc`、`/var` 和 `/usr`)

更新这些文件最简单的方式就是使用 [mergemaster(8)](http://www.FreeBSD.org/cgi/man.cgi?query=mergemaster&sektion=8&manpath=freebsd-release-ports)，手工去做也是可以的，只要您愿意。 不管您选择哪一种，一定记得备份 `/etc` 以防出错。

#### 25.7.11.1. `mergemaster`

贡献者：Tom Rhodes.

[mergemaster(8)](http://www.FreeBSD.org/cgi/man.cgi?query=mergemaster&sektion=8&manpath=freebsd-release-ports) 工具是个 Bourne 脚本，用于检测 `/etc` 和 `/usr/src/etc` 源码树里边的配置文件的不同点。 这是保持系统配置文件同源码树里的一起更新的推荐方式。

在提示符里简单地输入 `mergemaster` 就可以开始，并观看它的开始过程。`mergemaster` 会建立一个临时的根(root)环境，在 `/` 下， 放置各种系统配置文件。这些文件然后同当前安装到您系统里的进行比较。 此时，不同的文件会以 [diff(1)](http://www.FreeBSD.org/cgi/man.cgi?query=diff&sektion=1&manpath=freebsd-release-ports) 格式进行显示，使用 `+` 符号标识增加或修改的行，`-` 标识将完全删除的行或将被替换成新行。查看 [diff(1)](http://www.FreeBSD.org/cgi/man.cgi?query=diff&sektion=1&manpath=freebsd-release-ports) 手册可以得到更多关于 [diff(1)](http://www.FreeBSD.org/cgi/man.cgi?query=diff&sektion=1&manpath=freebsd-release-ports) 语法和文件不同点怎样显示的信息。

[mergemaster(8)](http://www.FreeBSD.org/cgi/man.cgi?query=mergemaster&sektion=8&manpath=freebsd-release-ports) 会给您显示每个文件的不同处， 这样您就可以选择是删除新文件 (相对临时文件)， 是以未改状态安装临时文件，是以当前安装的文件合并临时文件， 还是再看一次 [diff(1)](http://www.FreeBSD.org/cgi/man.cgi?query=diff&sektion=1&manpath=freebsd-release-ports) 结果。

“选择删除临时文件”将使 [mergemaster(8)](http://www.FreeBSD.org/cgi/man.cgi?query=mergemaster&sektion=8&manpath=freebsd-release-ports) 知道我们希望保留我们当前的文件不改，并删除新的。 并不推荐这个选择，除非您没有更改当前文件的理由。任何时候在 [mergemaster(8)](http://www.FreeBSD.org/cgi/man.cgi?query=mergemaster&sektion=8&manpath=freebsd-release-ports) 提示符里输入 **?**，您就会得到帮助。 如果选择跳过文件，将在其它文件处理完后再次进行。

“选择安装未修改临时文件”将会使新文件替换当前的。 对大部分未改的文件，这是个最好的选择。

“选择合并文件”将为您打开一个文本编辑器， 里边是两个文件的内容。您现在就可以一边合并它们， 一边在屏幕里查看，同时从两者中选取部分生成最终文件。 当两个文件一起比较时，**l** 键会选择左边的内容， **r** 会选择右边的。最终的输出是由两个部分组成的一个文件， 用它就可以安装了。这个选项通常用于用户修改了设置的文件。

“选择再次查看 [diff(1)](http://www.FreeBSD.org/cgi/man.cgi?query=diff&sektion=1&manpath=freebsd-release-ports) 结果”将会在提供给选择之前， 显示文件的不同处，就象 [mergemaster(8)](http://www.FreeBSD.org/cgi/man.cgi?query=mergemaster&sektion=8&manpath=freebsd-release-ports) 所做的一样。

在 [mergemaster(8)](http://www.FreeBSD.org/cgi/man.cgi?query=mergemaster&sektion=8&manpath=freebsd-release-ports) 完成了对系统文件的处理后， 您会得到其它的选项。[mergemaster(8)](http://www.FreeBSD.org/cgi/man.cgi?query=mergemaster&sektion=8&manpath=freebsd-release-ports) 可能会问您是否要重建密码文件， 并在最后提示您是否要删除余下的临时文件。

#### 25.7.11.2. 手动更新

如果想要手工更新，但不要只是从 `/usr/src/etc` 把文件复制到 `/etc` 就了事。有些文件是必须先“安装”的。 这是因为 `/usr/src/etc` 目录并 *不是* 想像的那样是 `/etc` 目录的一个复制。事实上，有些是文件是 `/etc` 有的，而 `/usr/src/etc` 里边没有。

如果您使用 [mergemaster(8)](http://www.FreeBSD.org/cgi/man.cgi?query=mergemaster&sektion=8&manpath=freebsd-release-ports) (作为推荐)，您可以向前跳到 下一节。

手工做最简单的方式是安装这些文件到一个新的目录，完成后再来查找不同处。

### 备份您已有的 `/etc`:

虽然，理论上，没有什么会自动访问这个目录， 事情还是做稳操胜当一点。复制已有 `/etc` 到一个安全的地方，如：

```
# `cp -Rp /etc /etc.old`
```

`-R` 完成递归复制 (译者注：即可以复制目录以下的所有内容)，`-p` 保留文件的时间、所属等等。

您需要建立一个虚目录 (a dummy set of directories) 来安装新的 `/etc` 和其它文件。 `/var/tmp/root` 是个不错的选择， 除此之外，还有一些子目录是需要的。

```
# `mkdir /var/tmp/root`
# `cd /usr/src/etc`
# `make DESTDIR=/var/tmp/root distrib-dirs distribution`
```

这样就建好了需要的目录结构，然后安装文件。在 `/var/tmp/root` 下建立的大部分子目录是空的， 而且要删除掉。最简单的方式是：

```
# `cd /var/tmp/root`
# `find -d . -type d | xargs rmdir 2>/dev/null`
```

这样会删除所有的空目录。(标准的错误信息被重定向到了 `/dev/null`，以防止关于非空目录的警告。)

`/var/tmp/root` 现在包含了应放在 `/` 下某个位置的所有文件。 您现在必须仔细检查每一个文件，检测它们与您已有的文件有多大不同。

注意，有些已经安装在 `/var/tmp/root` 下的文件有个“.”在开头。在写的时候，像这样唯一的文件是 `/var/tmp/root/` 和 `/var/tmp/root/root/` 里 shell 启动文件，尽管可能有其它的(依赖于您什么时候读取这个)。 确信使用 `ls -a` 可以看到它们。

最简单的方式是使用 [diff(1)](http://www.FreeBSD.org/cgi/man.cgi?query=diff&sektion=1&manpath=freebsd-release-ports) 去比较两个文件：

```
# `diff /etc/shells /var/tmp/root/etc/shells`
```

这会显示出 `/etc/shells` 文件和新的 `/var/tmp/root/etc/shells` 文件的不同处。 用这些来决定是合并您已做的变化还是复制您的旧文件过来。

### 使用日戳 (Time Stamp) 命名新的 Root(根)目录(`/var/tmp/root`)，这样您可以轻松地比较两个版本的不同:

频繁重建系统意味着必须频繁更新 `/etc`，而这可能会有点烦琐。

在合并到 `/etc` 的文件里， 最新更改的您可以做个复制，由此加快这个(指更新)过程。 下边就给出了一个怎样做的主意。

1.  像平常一样建立系统 (Make the world)。当您想更新 `/etc` 和其它目录里， 给目标目录一个含有当前日期的名字。假如您是 1998 年 2 月 14 日做的，您可以执行下边的：

    ```
    # `mkdir /var/tmp/root-19980214`
    # `cd /usr/src/etc`
    # `make DESTDIR=/var/tmp/root-19980214 \
        distrib-dirs distribution`
    ```

2.  如上边列出的，从这个目录合并变化。

    在您完成后，*不要* 删除 `/var/tmp/root-19980214` 目录。

3.  在您下载了最新版的源码并改过后，执行第一步。 这样将得到一个新的目录，可能叫做 `/var/tmp/root-19980221` (如果等了一周做的升级)。

4.  您现在能看到两个目录间的不同了---在隔周的时间里使用 [diff(1)](http://www.FreeBSD.org/cgi/man.cgi?query=diff&sektion=1&manpath=freebsd-release-ports) 建立递归 diff 产生的不同：

    ```
    # `cd /var/tmp`
    # `diff -r root-19980214 root-19980221`
    ```

    一般情况下，这两种间的不同处比 `/var/tmp/root-19980221/etc` 和 `/etc` 之间的不同要小很多。 因为不同点更小，也就更容易把这些变化移到您的 `/etc` 目录里边。

5.  您现在可以删除早先的两个 `/var/tmp/root-*` 目录：

    ```
    # `rm -rf /var/tmp/root-19980214`
    ```

6.  每次您需要合并这些变化到 `/etc` 里，就重复这个流程。

您可以使用 [date(1)](http://www.FreeBSD.org/cgi/man.cgi?query=date&sektion=1&manpath=freebsd-release-ports) 自动产生目录的名称：

```
# `mkdir /var/tmp/root-`date "+%Y%m%d"``
```

### 25.7.12. 重启

现在完成了。在您检查所有内容都放置正确后， 您可以重启系统了。只是简单的 [shutdown(8)](http://www.FreeBSD.org/cgi/man.cgi?query=shutdown&sektion=8&manpath=freebsd-release-ports) 可以这样做：

```
# `shutdown -r now`
```

### 25.7.13. 结束

恭喜！您现在成功升级了您的 FreeBSD 系统。

如果还有轻微的错误，可以轻易地重建系统的选定部分。 例如，在部分升级或合并 `/etc` 时，您不小心删除了 `/etc/magic`，[file(1)](http://www.FreeBSD.org/cgi/man.cgi?query=file&sektion=1&manpath=freebsd-release-ports) 命令就会停止工作。这种情况下，执行下边进行修复：

```
# `cd /usr/src/usr.bin/file`
# `make all install`
```

### 25.7.14. 问题

| **25.7.14.1.** | 每个变化您都须要重建系统吗？ |
|  | 这个不好说，因为要看变化的情况。如，如果您刚运行了 CVSup，并得到下边更新的文件： 
```
src/games/cribbage/instr.c
src/games/sail/pl_main.c
src/release/sysinstall/config.c
src/release/sysinstall/media.c
src/share/mk/bsd.port.mk
```

这就不必重建整个系统。您只需到相关的子目录里执行 `make all install`，仅此而已。 但是，如果有重大变化，如 `src/lib/libc/stdlib`， 那么您就要重建系统或至少静态连接的那些部分 (除了您增加的部分都是静态连接的)。在这天后，就是您的事了。要是说每两个星期重建一下系统的话， 您可能会高兴。或者您可能只想重做改变过的部分， 确信您能找出所有依赖关系。当然，所有这些依赖于您想升级的频率，和您是否想跟踪 FreeBSD-STABLE 或 FreeBSD-CURRENT。 |
| **25.7.14.2.** | 我的编译失败，并伴随有许多 11 (或其它的数字信息) 号错误。是怎么回事呀？ |
|  | 这个通常表示硬件错误。 (重)建系统是个强压测试系统硬件的有效地方式， 并且常常产生内存错误。 这些正好表示它们自已做为编译器离奇地死于收到的奇怪信息。一个确信的指示器是如果重新开始 make，并且整个过程中会死在不同的点上。对于这种情况，您没有什么可做的，除了更换机器里的部件，看是哪一个坏了。 |
| **25.7.14.3.** | 我完成后可以删除 `/usr/obj` 吗？ |
|  | 简短地说，可以。`/usr/obj` 包含了所有在编译阶段生成的目标文件。通常， 在 `make buildworld` 过程中第一步之一就是删除这个目录重新开始。 这种情况下，在您完成后，保留 `/usr/obj` 没有多大意义，还可释放一大堆磁盘空间(目前是 2 GB 左右)。不过， 如果您很了解整个过程， 也可以让 `make buildworld` 跳过这一步。 这会让后续的联编过程执行得更快， 因为大部分的源码都不必再进行编译了。 这样做的负面效果是它可能会触发一些由于敏感的依赖关系导致的问题， 这些问题会导致联编以奇怪的方式出错并失败。 这在 FreeBSD 邮件列表里经常引起沸腾， 当有人抱怨他们 build 失败时，并没意识到这是因为自已是想抄近路。 |
| **25.7.14.4.** | 中断的 build 可以被恢复吗？ |
|  | 依赖于您在您找到问题之前整个过程进行了多远。*一般而言* (当然这并不是硬性规定)， `make buildworld` 的过程中将会首先构建新版的基本构建工具 (例如 [gcc(1)](http://www.FreeBSD.org/cgi/man.cgi?query=gcc&sektion=1&manpath=freebsd-release-ports)， 以及 [make(1)](http://www.FreeBSD.org/cgi/man.cgi?query=make&sektion=1&manpath=freebsd-release-ports)) 和系统库。 随后会安装这些工具和库。 这些新版本的工具和库在随后将被用于重新编译和连接它们本身。 整个系统 (现在包括了常规的用户程序， 例如 [ls(1)](http://www.FreeBSD.org/cgi/man.cgi?query=ls&sektion=1&manpath=freebsd-release-ports) 或 [grep(1)](http://www.FreeBSD.org/cgi/man.cgi?query=grep&sektion=1&manpath=freebsd-release-ports)) 会同新版的系统文件一起被重新构建。如果您正处于最后一个阶段， 并且了解它 (因为您已经看过了所保存的输出) 则可以 (相当安全地) 做： 
```
*… 问题修复 …*
# `cd /usr/src`
# `make -DNO_CLEAN all`
```

这样就不会取消先前的 `make buildworld` 所做的工作了。在“make buildworld”的输出中如果看到如下信息：

```
--------------------------------------------------------------
Building everything..
--------------------------------------------------------------
```

出现在 `make buildworld` 的输出中， 则这样做应该不会有什么问题。如果没有看到这样的信息， 或者您不确定， 则从头开始构建将是万无一失的做法。 |
| **25.7.14.5.** | 我怎样加快建立系统的速度？ |
|  |  
*   以单用户模式运行

*   把 `/usr/src` 和 `/usr/obj` 目录放到不同磁盘里的独立文件系统里。如果可能，这些磁盘在不同的磁盘控制器里。

*   更好的，是把这些文件系统放置到多个使用 [ccd(4)](http://www.FreeBSD.org/cgi/man.cgi?query=ccd&sektion=4&manpath=freebsd-release-ports) (连接磁盘驱动器--concatenated disk driver)设备的磁盘里。

*   关掉 profiling (在 `/etc/make.conf` 里设置 “NO_PROFILE=true”)。您差不多用不了它。

*   在 `/etc/make.conf` 里也为 `CFLAGS` 设置上 `-O -pipe`。 最佳优化 `-O2` 会更慢，而且 `-O` 和 `-O2` 之间的优化差别基本上可以忽略。 `-pipe` 让编译器使用管道而不用临时文件进行通信， 这样可以减少磁盘存取 (以内存作为代价)。

*   传递 `-j*`n`*` 选项给 [make(1)](http://www.FreeBSD.org/cgi/man.cgi?query=make&sektion=1&manpath=freebsd-release-ports) 以便并发运行多个进程。 这样就不会考虑您的是否是单个或多个处理器机器。

*   存放 `/usr/src` 的文件系统可以使用 `noatime` 选项来挂接 (或重新挂接)。 这样会防止文件系统记录文件的存取时间。 您可能并不需要这些信息。

    ```
    # `mount -u -o noatime /usr/src`
    ```

    ### 警告:

    这个例子里假定 `/usr/src` 是在它自已的文件系统里。如果不是 (例如假设它是 `/usr` 的部分)，那么您就需要那个文件系统挂接点， 而不是 `/usr/src`。

*   存放 `/usr/obj` 的文件系统可以使用 `async` 选项被挂接 (或重新挂接)。 这样做将启用异步写盘。 换句话说， 对应用程序而言写会立即完成， 而数据则延迟几秒才会写到盘里。 这样做能够成批地写下数据， 从而极大地改善性能。

    ### 警告:

    注意， 这个选项会使您的文件系统变得脆弱。 使用这个选项会提高在电源断掉或机器非正常重启时， 文件系统进入不可恢复状态的概率。

    如果在这个文件系统里 `/usr/obj` 是很关键的，这不是问题。如果您有其它有价值的数据在同一个文件系统， 那么在您使用这个选项这前，确认备份一下。

    ```
    # `mount -u -o async /usr/obj`
    ```

    ### 警告:

    同上，如果 `/usr/obj` 不在自已的文件系统里，使用相关挂接点的名字把它从例子里边替换掉。

 |
| **25.7.14.6.** | 如果出现了错误我该怎么办？ |
|  | 绝对确信您的环境没有先前 build 留下的残余。这点够简单。 
```
# `chflags -R noschg /usr/obj/usr`
# `rm -rf /usr/obj/usr`
# `cd /usr/src`
# `make cleandir`
# `make cleandir`
```

不错，`make cleandir` 真的要执行两次。然后重新开始整个过程，使用 `make buildworld` 开始。如果您还有问题，就把错误和 `uname -a` 的输出发送到 [FreeBSD 一般问题邮件列表](http://lists.FreeBSD.org/mailman/listinfo/freebsd-questions) 邮件列表。准备回答其它关于您的设置的问题！ |

## 25.8. 删除过时的文件、 目录和函数库

Based on notes provided by Anton Shterenlikht.

在 FreeBSD 的开发过程中， 随时可能会出现一些文件或其内容过时的情况。 这种情况有可能是由于其功能在其它地方实现了， 函数库的版本号增加， 或完全从基本系统中删去， 等等。 一般的联编和更新过程并不会删去这些旧的文件、 函数库或目录， 在更新系统之后， 应及时予以清理。 清理的好处是这些文件不会再继续占用存储 (以及备份) 空间， 另外， 如果旧的函数库或文件中存在安全或可靠性问题， 您也应更新到新的函数库， 以避免安全隐患或崩溃情形的发生。 过时的文件、 目录和函数库会列在 `/usr/src/ObsoleteFiles.inc` 中。 接下来将介绍在系统更新过程中如何删去这些过时的文件。

我们假定您已经按照 第 25.7.1 节 “更新系统的规范方法” 介绍的步骤完成了更新操作。 在 `make installworld` 和 `mergemaster` 命令完成之后， 您应使用下面的命令检查系统中是否存在过时的文件或库：

```
# `cd /usr/src`
# `make check-old`
```

如果有过时的文件， 则可以用下面的命令来删除：

```
# `make delete-old`
```

### 提示:

参阅 `/usr/src/Makefile` 可以了解其他 target 的功用。

在删除文件时， 系统会针对每个文件都给出提示。 您可以跳过这些提示， 并让系统自动完成删除操作， 方法是使用 make 变量 `BATCH_DELETE_OLD_FILES`， 具体做法如下：

```
# `make -DBATCH_DELETE_OLD_FILES delete-old`
```

您也可以用 `yes` 命令和管道来达到类似的目的：

```
# `yes|make delete-old`
```

### 警告:

删去过时的文件， 有可能会破坏现有的依赖这些文件的应用程序。 对于旧的函数库来说， 这种问题出现的可能性更大。 绝大多数情况下， 您应重新联编使用旧库的所有的程序、 port 或函数库之后再执行 `make delete-old-libs`。

在 Ports Collection 中提供了一些检测动态连接库依赖关系的工具， 例如 [sysutils/libchk](http://www.freebsd.org/cgi/url.cgi?ports/sysutils/libchk/pkg-descr) 和 [sysutils/bsdadminscripts](http://www.freebsd.org/cgi/url.cgi?ports/sysutils/bsdadminscripts/pkg-descr)。

过时的动态连接库可能会与新库冲突， 导致类似这样的警告消息：

```
/usr/bin/ld: warning: libz.so.4, needed by /usr/local/lib/libtiff.so, may conflict with libz.so.5
/usr/bin/ld: warning: librpcsvc.so.4, needed by /usr/local/lib/libXext.so, may conflict with librpcsvc.so.5
```

要解决这样的问题， 需要确认安装这个库的 port：

```
# `pkg_info -W  /usr/local/lib/libtiff.so`
/usr/local/lib/libtiff.so was installed by package tiff-3.9.4
# `pkg_info -W /usr/local/lib/libXext.so`
/usr/local/lib/libXext.so was installed by package libXext-1.1.1,1
```

接着卸载、 重新联编并安装 port。 您可以使用 [ports-mgmt/portmaster](http://www.freebsd.org/cgi/url.cgi?ports/ports-mgmt/portmaster/pkg-descr) 或 [ports-mgmt/portupgrade](http://www.freebsd.org/cgi/url.cgi?ports/ports-mgmt/portupgrade/pkg-descr) 工具来自动完成这些操作。 在确认所有的 port 都重新联编， 并且不再使用旧库以后， 您就可以用下面的命令来删除它们了：

```
# `make delete-old-libs`
```

## 25.9. 跟踪多台机器

贡献者 Mike Meyer.

如果您有多台机器想跟踪同样的源码树， 那么让它们都下载源码并重建所有东西，看起有点浪费资源： 磁盘空间、网络带宽以及 CPU 周期。 解决的办法是让一台机器处理大部分的工作，而其它的机器通过 NFS 挂接 (mount) 这些工作。这部分列举了一种这样做的方法。

### 25.9.1. 准备

首先，确定一批机器，运行的二进制代码是同一套---我们称作 *构建集群 (build set)*。 每台机器可以使用不同的定制内核， 但它们运行的是相同的用户区二进制文件(userland binaries)。 从这批机器中选择一台机器做为 *构建机器(build machine)*。 这将是用于构建(build)系统和内核的机器。想像一下，它应该是一台快速的机器， 有足够的空余的 CPU 来执行`make buildworld`。 您也想要选一台机器做为 *测试机器(test machine)*， 这个将用于软件的更新生成产品之前对他们进行测试。这个 *必须* 是一台您能提供的平时也可使用的机器。 它可以是“构建机器”，但没这个必要。

在这个“构建集群”里的所有机器需要从同一台机器、 同一个点上挂接 `/usr/obj` 和 `/usr/src`。理想地， 它们在“构建机器”上的两个不同的驱动器里， 但是在那台机器上可以进行 NFS 挂接。如果您有多个“构建集群”， `/usr/src` 应该在某个“构建机器”上， 而在其它机器上进行 NFS 挂接。

最后，确认“构建集群”里所有机器上的 `/etc/make.conf` 和 `/etc/src.conf` 与“构建机器”里的相同。 这意味着“构建机器”必须构建部分基本系统用于 “构建集群”里所有机器的安装。同样， 每台“构建机器”要有它自已的内核名字，使用 `/etc/make.conf` 里的 `KERNCONF` 进行设置，并且每台“构建机器”应该把它们列在 `KERNCONF` 里，同时把自已的内核列在最前。 “构建机器”的 `/usr/src/sys/arch/conf` 里一定要有每台机器的内核配置文件，如果它想构建它们的内核的话。

### 25.9.2. 基本系统

既然所有的妥当了，就准备构建所有的东西。如第 25.7.7.2 节 “编译基本系统”中描述的一样在“构建机器”上构建内核和系统， 但是什么也不安装。在构建结束后，转到“测试机器”上， 安装您刚构建的内核。如果这台机器通过 NFS 挂接了 `/usr/src` 和 `/usr/obj`， 在您重启到单用户模式里，您需要启动网络然后挂接他们。 最简单的方式是启动到多用户模式下，然后执行 `shutdown now` 转到单用户模式。一旦进入，您就可以安装新的内核和系统，并执行 `mergemaster`，就像平常一样。完成后， 重启返回到一般多用户模式操作这台机器。

在您确信所有在 “测试机”里都工作正常后， 就使用相同的过程在 “构建集群”里的其它机器里安装新的软件。

### 25.9.3. Ports

类似的想法是使用 ports 树。 第一个关键的步骤是从同一台计算机上挂接 `/usr/ports` 到 “构建集群” 里的全部计算机。 然后正确设置 `/etc/make.conf` 共享 distfiles。您应把 `DISTDIR` 设置到一个共享的目录里， 那里可以被任何一个 `root` 用户写入， 并且是由您的 NFS 挂接映射的。 设置每一台机器的 `WRKDIRPREFIX` 到一个本地构建 (build) 目录。最后，如果您要构建和发布包 (packages)，那么您应该设置 `PACKAGES` 到一个类似于 `DISTDIR` 的目录。

## 第 26 章 DTrace

Written by Tom Rhodes.

## 26.1. 概述

DTrace，也称为动态跟踪，是由 Sun™ 开发的一个用来在生产和试验性生产系统上找出系统瓶颈的工具。 在任何情况下它都不是一个调试工具， 而是一个实时系统分析寻找出性能及其他问题的工具。

DTrace 是个特别好的分析工具，带有大量的帮助诊断系统问题的特性。 还可以使用预先写好的脚本利用它的功能。 用户也可以通过使用 DTrace D 语言创建他们自己定制的分析工具， 以满足特定的需求。

在阅读了这一章节之后，你将了解：

*   DTrace 是什么，它提供了些哪些特性。

*   DTrace 在 Solaris™ 与 FreeBSD 上的实现的差别。

*   如何在 FreeBSD 上开启和使用 DTrace。

在阅读这一章节之前，你应该了解：

*   了解 UNIX® 和 FreeBSD 的基本知识 (第 4 章 *UNIX 基础*)。

*   熟悉基本的内核配置/编译 (第 9 章 *配置 FreeBSD 的内核*).

*   熟悉 FreeBSD 有关的安全知识 (第 15 章 *安全*)。

*   了解如何获取和重新编译 FreeBSD 源代码 (第 25 章 *更新与升级 FreeBSD*)。

### 警告:

这项特性目前仍被认为是试验性的。 有些选项功能性缺失，另有一些可能还无法运行。最终， 这个特性会适合用于生产，届时这篇文档也会做些适当的修改。

## 26.2. 实现上的差异

虽然 FreeBSD 上的 DTrace 与 Solaris™ 上的非常相似， 在继续深入之前我们需要说明一下存在的差异。 用户首先会注意到的便是 FreeBSD 上的 DTrace 需要明确地被启用。 DTrace 相关的内核选项和模块必须开启后才能正常工作。 稍后我们会作详细介绍。

有一个 `DDB_CTF` 内核选项用来开启从内核与内核模块加载 `CTF` 数据。 CTF 是 Solaris™ Compact C Type Format 封装了类似于 DWARF 和 venerable stabs 简化的调试信息。CTF 数据是由 `ctfconvert` 和 `ctfmerge` 工具加入二进制文件的。`ctfconvert` 工具分析由编译器生成的 DWARF ELF 调试 section， `ctfmerge` 合并目标文件的 CTF ELF section 到可执行文件或共享库。更多关于在启用 FreeBSD 内核上启用此项的详细内容即将完成。

比起 Solaris™， FreeBSD 有几个不同提供器。 最值得注意的是 `dtmalloc` 提供器， 可以让你根据类型追踪 FreeBSD 内核中的 `malloc()`。

只有 `root` 可以使用 FreeBSD 上的 DTrace。 这是由系统安全上的差异造成的，Solaris™ 提供了一些 FreeBSD 上还未实现的低层的安全检查。 同样， `/dev/dtrace/dtrace` 也被严格的限制为仅供 `root` 用户访问。

最后，DTrace 为 Sun™ CDDL 许可下发布的软件。随 FreeBSD 发行的 `Common Development and Distribution License` 可以在查阅 `/usr/src/cddl/contrib/opensolaris/OPENSOLARIS.LICENSE` 或者通过 `[`www.opensolaris.org/os/licensing`](http://www.opensolaris.org/os/licensing)` 查看在线版本。

这个许可表示带有 DTrace 选项的 FreeBSD 内核仍为 BSD 许可； 然而， 以二进制发布模块， 或者加载二进制模块则需遵守 CDDL。

## 26.3. 启用 DTrace 支持

在内核配置文件中加入以下几行来开启对 DTrace 的支持：

```
options         KDTRACE_HOOKS
options         DDB_CTF
```

### 注意:

使用 AMD64 架构的需要在内核配置文件中加入如下这行：

```
options         KDTRACE_FRAME
```

此选项提供了对 FBT 特性的支持。 DTrace 可以在没有此选项的情况下正常工作， 但是函数边界跟踪便会有所限制。

所有的源代码都必须重新使用 CTF 选项编译安装。重新编译 FreeBSD 源代码可以通过以下的命令完成：

```
# `cd /usr/src`

# `make WITH_CTF=1 kernel`
```

系统需要重新启动。

在重新启动和新内核载入内存之后，需要添加 Korn shell 的支持。因为 DTrace 工具包有一些工具是由 `ksh` 写的。安装 [shells/ksh93](http://www.freebsd.org/cgi/url.cgi?ports/shells/ksh93/pkg-descr)。 同样也可以通过 [shells/pdksh](http://www.freebsd.org/cgi/url.cgi?ports/shells/pdksh/pkg-descr) 或者 [shells/mksh](http://www.freebsd.org/cgi/url.cgi?ports/shells/mksh/pkg-descr) 使用这些工具。

最后是获得最新的 DTrace 工具包。 当前版本可以通过下面的链接找到 `[`www.opensolaris.org/os/community/dtrace/dtracetoolkit/`](http://www.opensolaris.org/os/community/dtrace/dtracetoolkit/)`。 这个工具包含有一个安装机制，尽管如此，并不需要安装便可使用它们。

## 26.4. 使用 DTrace

在使用 DTrace 的功能之前，DTrace 设备必须存在。 使用如下的命令装载此设备：

```
# `kldload dtraceall`
```

DTrace 支持现在应该可以使用了。 管理员现在可以使用如下的命令查看所有的探测器：

```
# `dtrace -l | more`
```

所有的输出都传递给 `more` 工具， 因为它们会很快超出屏幕的显示区域。此时，DTrace 应该被认为是能够正常工作的了。现在是该考察工具包的时候了。

工具包是实现写好的一堆脚本，与 DTrace 一起运行来收集系统信息。 有脚本用来检查已打开的文件，内存，CPU 使用率和许多东西。使用如下的命令解开脚本：

```
# `gunzip -c DTraceToolkit* | tar xvf -`
```

使用 `cd` 命令切换到那个目录， 并修改所有文件的可执行权限，把那些名字小写的文件权限改为 `755`。

所有这些脚本都需要修改它们的内容。那些指向 `/usr/bin/ksh` 需要修改成 `/usr/local/bin/ksh`，另外使用 `/usr/bin/sh` 需要变更为 `/bin/sh`，最后还有使用 `/usr/bin/perl` 的需要变更为 `/usr/local/bin/perl`。

### 重要:

此刻还需谨慎提醒一下读者 FreeBSD 的 DTrace 支持仍是 *不完整的* 和 *试验性* 的。 这些脚本中的大多数都无法运行，因为它们过于针对 Solaris™ 或者使用了目前还不支持的探测器。

在撰写这篇文章的时候，DTrace 工具包中只有两个脚本在 FreeBSD 上是完全支持的： `hotkernel` 和 `procsystime` 脚本。这两个脚本便是我们下一部分将要探讨的：

`hotkernel` 被设计成验明哪个函数占用了内核时间。 正常运行的话，它将生成类似以下的输出：

```
# `./hotkernel`
Sampling... Hit Ctrl-C to end.
```

系统管理员必须使用 **Ctrl**+**C** 组合键停止这个进程。 紧接着中止之后，脚本便会一张内核函数与测定时间的列表， 使用增量排序输出：

```
kernel`_thread_lock_flags                                   2   0.0%
0xc1097063                                                  2   0.0%
kernel`sched_userret                                        2   0.0%
kernel`kern_select                                          2   0.0%
kernel`generic_copyin                                       3   0.0%
kernel`_mtx_assert                                          3   0.0%
kernel`vm_fault                                             3   0.0%
kernel`sopoll_generic                                       3   0.0%
kernel`fixup_filename                                       4   0.0%
kernel`_isitmyx                                             4   0.0%
kernel`find_instance                                        4   0.0%
kernel`_mtx_unlock_flags                                    5   0.0%
kernel`syscall                                              5   0.0%
kernel`DELAY                                                5   0.0%
0xc108a253                                                  6   0.0%
kernel`witness_lock                                         7   0.0%
kernel`read_aux_data_no_wait                                7   0.0%
kernel`Xint0x80_syscall                                     7   0.0%
kernel`witness_checkorder                                   7   0.0%
kernel`sse2_pagezero                                        8   0.0%
kernel`strncmp                                              9   0.0%
kernel`spinlock_exit                                       10   0.0%
kernel`_mtx_lock_flags                                     11   0.0%
kernel`witness_unlock                                      15   0.0%
kernel`sched_idletd                                       137   0.3%
0xc10981a5                                              42139  99.3%
```

这个脚本也能与内核模块一起工作。要使用此特性， 用 `-m` 标志运行脚本：

```
# `./hotkernel -m`
Sampling... Hit Ctrl-C to end.
^C
MODULE                                                  COUNT   PCNT
0xc107882e                                                  1   0.0%
0xc10e6aa4                                                  1   0.0%
0xc1076983                                                  1   0.0%
0xc109708a                                                  1   0.0%
0xc1075a5d                                                  1   0.0%
0xc1077325                                                  1   0.0%
0xc108a245                                                  1   0.0%
0xc107730d                                                  1   0.0%
0xc1097063                                                  2   0.0%
0xc108a253                                                 73   0.0%
kernel                                                    874   0.4%
0xc10981a5                                             213781  99.6%
```

`procsystime` 脚本捕捉并打印给定 PID 的系统调用时间。 在下面的例子中，新生成了一个 `/bin/csh` 实例。`procsystime` 执行后则等待在新运行的 `csh` 上键入一些命令。 这是测试的结果：

```
# `./procsystime -n csh`
Tracing... Hit Ctrl-C to end...
^C

Elapsed Times for processes csh,

         SYSCALL          TIME (ns)
          getpid               6131
       sigreturn               8121
           close              19127
           fcntl              19959
             dup              26955
         setpgid              28070
            stat              31899
       setitimer              40938
           wait4              62717
       sigaction              67372
     sigprocmask             119091
    gettimeofday             183710
           write             263242
          execve             492547
           ioctl             770073
           vfork            3258923
      sigsuspend            6985124
            read         3988049784
```

正如显示的那样，`read` 系统调用似乎使用了最多的纳秒单位时间， `getpid()` 系统调用使用了最少的时间。

## 26.5. D 语言

DTrace 工具包包括了很多由 DTrace 特殊语言写成的脚本。 在 Sun™ 的文档中称这类语言为 “D 语言”， 它与 C++ 非常类似。对此语言更深入的讨论则超出了这篇文章的范围。 更多相关的讨论可以在 `[`wikis.sun.com/display/DTrace/Documentation`](http://wikis.sun.com/display/DTrace/Documentation)` 找到。