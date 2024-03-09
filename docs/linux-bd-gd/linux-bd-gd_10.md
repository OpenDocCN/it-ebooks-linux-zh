# 7\. 配置系统启动脚本

# 7.1\. 简介

本章详细描述了如何安装并恰当配置启动脚本(LFS-Bootscripts)，大多数脚本无需修改就可运行，但少数需要附加配置文件，因为它们与硬件相关。

因为 System-V 风格的初始化脚本被广泛使用，因此本书使用这种风格的脚本，作为补充选项，在 [*http://www.linuxfromscratch.org/hints/downloads/files/bsd-init.txt*](http://www.linuxfromscratch.org/hints/downloads/files/bsd-init.txt) 可以找到一个详细设计 BSD 风格启动初始化的提示。在 LFS 邮件列表里搜索"depinit"还可以找到更多选择。

如果您使用其它风格的初始化脚本，请跳过本章继续进行 Chapter 8 。

# 7.2\. LFS-Bootscripts-6.2

LFS-Bootscripts 软件包包含一套在 LFS 系统启动和关闭时的启动和停止脚本。

**预计编译时间：** 0.1 SBU**所需磁盘空间：** 0.3 MB**安装依赖于：** Bash, Coreutils

## 7.2.1\. 安装 LFS-Bootscripts

安装软件包：

```
make install 
```

## 7.2.2\. Contents of LFS-Bootscripts

**Installed scripts:** checkfs, cleanfs, console, functions, halt, hotplug, ifdown, ifup, localnet, mountfs, mountkernfs, network, rc, reboot, sendsignals, setclock, static, swap, sysklogd, template, udev

### 简要描述

|  |  |
| --- | --- |
| `checkfs` | 在挂载之前检查文件系统完整性(日志文件系统和基于网络的文件系统除外) |
| `cleanfs` | 删除系统重启后就不需要保存了的文件，例如在 `/var/run/` 和 `/var/lock/` 目录下的文件；重新创建 `/var/run/utmp` 文件并删除可能存在的 `/etc/nologin`, `/fastboot`, `/forcefsck` 文件。 |
| `console` | 为指定的键盘布局读入正确的键盘映射表，并设置屏幕字体。 |
| `functions` | 包含在不同脚本中共用的一些函数，例如错误和状态检查函数。 |
| `halt` | 关闭系统 |
| `hotplug` | 为系统设备加载模块 |
| `ifdown` | 协助 network 脚本停止网络设备 |
| `ifup` | 协助 network 脚本启动网络设备 |
| `localnet` | 设置系统主机名和本地回环(loopback)设备 |
| `mountfs` | 挂载所有文件系统，有 *noauto* 标记或者基于网络的文件系统除外。 |
| `mountkernfs` | 用来挂载内核提供的文件系统，例如 `proc` |
| `network` | 设置网络连接，例如网卡等；并设置默认网关(如果可用) 。 |
| `rc` | 主要的运行级控制脚本，负责让所有其它脚本按符号链接名确定的顺序一个接一个的运行。 |
| `reboot` | 重新启动系统 |
| `sendsignals` | 在系统重启或关闭系统之前，确保每一个进程都已经终止了。 |
| `setclock` | 如果硬件时钟没有设置为 UTC 时间，将内核时钟重置为本地时间。 |
| `static` | 提供为网络接口指派静态 IP 地址的功能 |
| `swap` | 启用或禁用交换文件和交换分区 |
| `sysklogd` | 启动或停止系统和内核日志守护进程 |
| `template` | 为其它守护进程创建自定义启动脚本的模板 |
| `udev` | 启动 udev 并在 `/dev` 目录创建设备节点 |

# 7.3\. 启动脚本是如何工作的?

Linux 使用的是基于 *运行级(run-levels)* 概念的称为 SysVinit 的专用启动工具。它在不同的系统上可能是完全不一样的，所以不能认为一个脚本在某个 Linux 发行版上工作正常，于是在 LFS 中也会正常工作。LFS 有自己的一套规则，当然，LFS 也遵守一些公认的标准。

SysVinit(从现在开始我们称之为"init")以运行级的模式来工作，一般有 7 个运行级(从 0 到 6，实际上可以有更多的运行级，但都是用于特殊情况而且一般使用不到。 参见 `init(8)` 以获得更多信息)，每个运行级对应于一套设定好的任务，当启动一个运行级的时候，计算机就需要执行相应的任务。默认的运行级是 3，下面是对不同运行级的描述：

0: 停止计算机 1: 单用户模式 2: 无网络多用户模式 3: 有网络多用户模式 4: 保留作自定义，否则同运行级 3 5: 同运行级 4，一般用于图形界面(GUI)登录(如 X 的 `xdm` 或者 KDE 的 `kdm`) 6: 重新启动计算机

用来改变运行级的命令是 `init _`[runlevel]`_` ，这里的 *`[runlevel]`* 是目标运行级。例如，要重启计算机，用户可以运行 `init 6` 命令，`reboot` 其实只是这个命令的别名，同样，`halt` 命令也只是 `init 0` 的别名。

在 `/etc/rc.d` 目录下有一些类似于 `rc?.d` 的目录(这里 ? 是运行级的数字)以及 `rcsysinit.d` ，里面都包含许多符号链接，其中一些以 *K* 字母开头，另外一些以 *S* 字母开头，这些链接名在首字母后面都跟着两个数字。K 字母的含义是停止(杀死)一个服务，S 字母的含义是启动一个服务。而数字则确定这些脚本的启动顺序，从 00 到 99(数字越小执行的越早)。当 `init` 转换到其它运行级时，一些相应的服务会停止，而另一些服务则会启动。

真正的脚本则在 `/etc/rc.d/init.d` 目录下，它们完成实际工作，符号链接都是指向它们的。停止脚本的链接和启动脚本的链接都指向 `/etc/rc.d/init.d` 目录下同一个脚本，这是因为调用这些脚本时可以使用不同的参数，例如 *`start`*, *`stop`*, *`restart`*, *`reload`*, *`status`* 当调用 K 链接时，相应的脚本用 *`stop`* 参数运行；当调用 S 链接时，相应的脚本用 *`start`* 参数运行。

上面的说明有一个例外，在 `rc0.d` 和 `rc6.d` 目录下以 *S* 开头的链接不会启动任何东西，而是用 *`stop`* 参数调用，来停止某些服务。这背后的逻辑是，当用户要重启或关闭系统的时候，不会要启动什么服务，只会要系统停止。

以下是脚本参数的描述：

*`start`*

启动服务

*`stop`*

停止服务

*`restart`*

停止服务，然后再启动

*`reload`*

该服务的配置已更新。如果修改了某个服务的配置文件，又不必重启这个服务的时候，可以使用这个参数。

*`status`*

显示服务的状态，如果服务正在运行，会显示该服务进程的 PID 。

您可以自由修改启动进程工作的方式(毕竟这是您自己的 LFS 系统)，我们这里给出的文件只是它们怎样工作的一个示例而已。

# 7.4\. LFS 系统的设备和模块处理

在 Chapter 6 里，我们安装了 Udev 软件包，在开始深入讨论它如何工作之前，我们先简要回顾一下以前处理设备的方法。

传统上一般 Linux 系统使用创建静态设备的方法，因此在 `/dev` 目录下创建了大量的设备节点(有时会有数千个节点)，而不管对应的硬件设备实际上是否存在。这通常是由 `MAKEDEV` 脚本完成的，这个脚本包含许多调用 `mknod` 程序的命令，为这个世界上可能存在的每个设备创建相应的主设备号和次设备号。而使用 udev 方式的时候，只有被内核检测到的设备才为其创建设备节点。因为每次系统启动的时候都要重新创建这些设备节点，所以它们被存储在 `tmpfs` 文件系统(一种完全存在于内存里，不占用任何磁盘空间的文件系统)上，设备节点不需要很多磁盘空间，所占用的内存可以忽略不计。

## 7.4.1\. 历史

2000 年 2 月的时候，2.3.46 版本的内核引入了一种称为 `devfs` 的文件系统，在 2.4 系列稳定版本的内核中都是可用的。尽管它存在于内核源代码中，但这种动态创建设备的方法却从未得到核心内核开发者们的全力支持。

`devfs` 存在的主要的问题是它处理设备检测、创建和命名的方式，其中设备节点的命名可能是最严重的问题。一般可接受的方式是，如果设备名是可配置的，那么设备命名 策略应该由系统管理员决定，而不是由某些开发者强制规定。`devfs` 文件系统还存在竞争条件(race conditions)的问题，这是它天生的设计缺陷，不对内核做彻底的修改就无法修正这个问题。因为近来缺乏维护，它已经被标记为 deprecated(反对的)。

随着非稳定的 2.5 内核树的开发，即后来发布的 2.6 系列稳定版本内核，一种被称为 `sysfs` 的新虚拟文件系统诞生了。`sysfs` 的工作是把系统的硬件配置视图导出给用户空间的进程。由于有了这个用户空间可见的表示，代替 `devfs` 方案的时机就成熟了。

## 7.4.2\. Udev 实现

#### 7.4.2.1\. Sysfs 文件系统

上面简单的提到了 `sysfs` 文件系统，您可能想知道 `sysfs` 是怎么认出系统中存在的设备以及应该使用什么设备号。对于已经编入内核的驱动程序，当被内核检测到的时候，会直接在 `sysfs` 中注册其对象；对于编译成模块的驱动程序，当模块载入的时候才会这样做。一旦挂载了 `sysfs` 文件系统(挂载到 `/sys`)， 内建的驱动程序在 `sysfs` 注册的数据就可以被用户空间的进程使用，并提供给 `udev` 以创建设备节点。

#### 7.4.2.2\. Udev 启动脚本

`S10udev` 初始化脚本负责在 Linux 启动的时候创建设备节点，该脚本首先将 `/sbin/udevsend` 注册为热插拔事件处理程序。热插拔事件(随后将讨论)本不应该在这个阶段发生，注册 `udev` 只是为了以防万一。然后 `udevstart` 遍历 `/sys` 文件系统，并在 `/dev` 目录下创建符合描述的设备。例如，`/sys/class/tty/vcs/dev` 里含有"7:0"字符串，`udevstart` 就根据这个字符串创建主设备号为 *7* 、次设备号为 *0* 的 `/dev/vcs` 设备。`udevstart` 创建的每个设备的名字和权限由 `/etc/udev/rules.d/` 目录下的文件指定的规则来设置，这些文件以类似于 LFS 启动脚本风格的编号。如果 `udev` 找不到所创建设备的权限文件，就将其权限设置为缺省的 *660* ，所有者为 *root:root* 。

上面的步骤完成后，那些已经存在并且已经内建驱动的设备就可以使用了，那么以模块驱动的设备呢？

前面我们提到了"热插拔事件处理程序"的概念，当内核检测到一个新设备连接时，内核会产生 一个热插拔事件，并在 `/proc/sys/kernel/hotplug` 文件里查找处理设备连接的用户空间程序。`udev` 初始化脚本将 `udevsend` 注册为该处理程序。当产生热插拔事件的时候，内核让 `udev` 在 `/sys` 文件系统里检测与新设备的有关信息，并为新设备在 `/dev` 里创建项目。

这样带来了 `udev` 存在的一个问题，之前 `devfs` 也存在同样的问题。这通常是个"先有鸡 还是先有蛋"问题。大多数 Linux 发行版通过 `/etc/modules.conf` 配置文件来处理模块加载，对某个设备节点的访问导致相应的内核模块被加载。对 `udev` 这个方法就行不通了，因为在模块加载前，设备节点根本不存在。为了解决这个问题，在 LFS-Bootscripts 软件包里加入了 `S05modules` 启动脚本，以及 `/etc/sysconfig/modules` 文件。通过在 `modules` 文件里添加模块名，就可以在系统启动的时候加载这些模块，这样 `udev` 就可以检测到设备，并创建相应的设备节点了。

注意，在慢速的机器上，或者对于需要创建大量设备节点的驱动程序，创建设备的过程可能需要好几秒钟，这意味着某些设备节点不能立即访问到。

#### 7.4.2.3\. 设备节点创建

为了得到正确的主次设备号，Udev 依赖于 `/sys 目录下的` `sysfs 提供的信息。`例 如， `/sys/class/tty/vcs/dev` 包含字符串 "7:0"，被 `udevd` 用来创建主设备号是 7、次设备号是 0 的设备节点。在 `/dev` 目录下创建的设备节点的名字和权限由`/etc/udev/rules.d/ 目录下相应的规则决定。`这 些以类似的形式包含在 LFS-Bootscripts 包中。 如果 `udevd` 不能为它现在创建的设备发现一个规则，它会默认把权限设置为 660 ，属主设置为 _root: root。_Udev 规则语法的文档可以查看 `/usr/share/doc/udev -096/index.html。`

#### 7.4.2.4\. 模块加载

被编译成模块的设备驱动可能有编译进去的别名。别名可以通过 `modinfo` 程序的输出来查看，通常与设备总线特 有的标识有关（模块要支持）。例如，*snd-fm801* 驱动支持 PCI 设备，通过生产厂商 ID 0x1319 和设备 ID 0x0801，有一个别名"pci:v00001319d00000801sv*sd*bc04sc01i*"。 对于大多数的设备，总线驱动通过 `sysfs` 导出可能会处理的设备驱动的别名。例如，`/sys/bus/pci/devices/0000:00:0d.0/modalias` 文件可能包含字符串"pci:v00001319d00000801sv00001319sd00001319bc04sc01i00"。 LFS 安装的规则会导致 `udevd` 调用 `/sbin/modprobe` 处理热插拔事件环境变量 `MODALIAS` 的内容（应该与 sysfs 中的 modalias 文件的内容一样）。 因此在通配符扩展之后，加载所有的别名匹配这个字符串的模块。

这个例子中，除了 *snd-fm801*，荒废的（不想要的） *forte* 驱动会被加载（如果它是有效的）。 查看下面的方法来避免加载不想要的模块。

内核能在后台加载有关网络协议、文件系统和 NLS（国际语言支持） 支持的模块。

#### 7.4.2.5\. 处理可热插拔/动态设备

当您插入一个设备，例如一个 USB 接口的 MP3 播放器，内核会检测到设备连接，并产生一个热插拔事件，如果驱动程序已经加载(要么是因为驱动已经编入内核，要么是已经通过 `S05modules` 启动脚本加载了)，`udev` 将被调用，并根据 `/sys` 目录下的 `sysfs` 数据来创建相应的设备节点。如果该设备的驱动是一个未加载的模块，将设备连接到系统上只会让内核的总线驱动产生一个热插拔事件，通知用户空间有新设备连 接，但并不加载驱动。事实上，什么都没有做，设备仍然不能使用。

如果刚才插入的设备有一个驱动程序模块但是尚未加载，Hotplug 软件包就有用了，它就会响应上述的内核总线驱动热插拔事件并加载相应的模块，为其创建设备节点，这样设备就可以使用了。

## 7.4.3\. 创建设备的问题

自动创建设备节点的时候，存在一些已知的问题：

#### 7.4.3.1\. 内核模块没有自动加载

如果有一个总线特有的别名，并且总线驱动器导出必需的别名到 `sysfs，`那么 Udev 将只会加载一个模块。在其他情况下，需要安排其他的模块加载方式。在 Linux-2.6.16.27 中，Udev 为 INPUT, IDE, PCI, USB, SCSI, SERIO 和 FireWire 设备加载适当的驱动。

为了确定你请求的设备驱动 Udev 是否支持，可以以模块的名字为参数运行 `modinfo`。 现在把设置设备目录到 `/sys/bus` ，检测那里是否有一个 `modalias` 文件。

如果 `modalias` 文件存在于 `sysfs` 中，驱动程序支持设备可以直接通信，但是没有别名，这是驱动中的一个 bug。不利用 Udev 的帮助加载驱动，希望在以后这个问题会被修正。

如果在 `/sys/bus 下的`相应目录内没有 `modalias` 文件，这就意味着内核开发者没有添加对这种总线类型的模块别名支持。在 Linux-2.6.16.27 中，ISA 总线就是这种情况。希望在下个内核版本中会修正这个问题。

Udev 根本不会加载 "封装" 驱动像 *snd-pcm-oss*，和 非硬件驱动器像 *loop*。

#### 7.4.3.2\. 内核模块没有自动加载，并且 Udev 也不加载

如果 “封装” 模块增强其他模块提供的功能（例如，*snd-pcm-oss* 增强 *snd-pcm* 的功能，使声卡对于 OSS 程序可用），配置 `modprobe` 使其在加载了相应模块后加载其封装模块。在 `/etc/modprobe.conf 中`添加一个 "install" 行来实现，例如：

```
install snd-pcm /sbin/modprobe -i snd-pcm ; \
    /sbin/modprobe snd-pcm-oss ; true 
```

如果模块不是一个封装，而是对自己本身有用，配置 `S05modules` 启动脚本使其在系统启动的时候加载。 要实现这个，在 `/etc/sysconfig/modules 文件的相应行上添加模块的名字。`这对于封装模块也有用，但不是最优的。

#### 7.4.3.3\. Udev 加载了不需要的模块

在下面例子中，对于 *forte 模块，可以*不编译它或者是在 `/etc/modprobe.conf 文件中`列入黑名单：

```
blacklist forte 
```

列入黑名单的模块仍然能够通过 `modprobe` 命令手工加载。

#### 7.4.3.4\. Udev 错误的创建了设备或创建了错误的符号链接

如果规则匹配不是预想的设备，那么这种情况就会经常发生。例如，一个写的很糟糕的规则通过计算机提供商匹配到一个 SCSI 硬盘（期望的）和一个一般的 SCSI 设备（错误的）。找出这些不合格的规则，更正他们。

#### 7.4.3.5\. Udev 规则工作不可靠

这或许是前面问题的一个表现。如果不是，你的规则使用 `sysfs` 属性，这可能是一个内核计时问题，在下个版本的内核中会被修正 。 现在，你可以通过创建一个等待使用 `sysfs 属性`的规则，把它添加到 `/etc/udev/rules.d/10-wait_for_sysfs.rules` 文件中。如果你做了，请通知 LFS 开发邮件列表 ，对他们有所帮助。

#### 7.4.3.6\. Udev 没有创建设备

后面的文本假设驱动已经被静态的编译进了内核或者是作为模块已经被加载，你已经检查了 Udev 没有创建错误的设备。

某个内核驱动可能没有将其数据导出到 `sysfs`。这个问题在内核源代码树之外的第三 方驱动程序上尤其常见，结果是这些驱动无法创建其设备节点。用 `/etc/sysconfig/createfiles` 配置文件手动创建这些设备，参考内核文档里的 `devices.txt` 文件或者该驱动的文档以获得正确的主/次设备号。 静态的设备将会被 **S10udev 启动脚本**拷贝到 `/dev`。

#### 7.4.3.7\. 重启后设备名字顺序变化

这是由于 Udev 设计的问题，它以并行方式处理热插拔事件和加载模块。因此名字的顺序就会不可预测。 这将不会被修正。你不应当依赖内核设备名字会稳定。相应的，根据设备稳定的属性（比如，序列号或者 Udev 安装的各种 *_id 工具的输出）写自己 的规则来创建符号链接。可以参见 节 7.12, "为设备创建惯用符号连接" 和 节 7.13, "配置网络脚本" 。

# 7.5\. 配置 setclock 脚本

The `setclock`脚本从硬件时钟，也就是 BIOS 或 CMOS 时钟读取时间。如果硬件时钟设置为 UTC ，这个脚本会使用 `/etc/localtime` 文件(这个文件把用户所在的时区告诉 `hwclock` 程序)将硬件时钟的时间转换为本地时间。没有办法自动检测硬件时钟是否设置为 UTC 时间，因此需要手动设置。

如果您忘了硬件时钟是不是设置为 UTC 时间了，可以运行 **`hwclock --localtime --show`** 命令，这将显示硬件时钟当前的时间。如果显示的时间符合您的手表的时间，那么硬件时钟设置的是本地时间；如果 `hwclock` 显示的不是本地时间，就有可能设置的是 UTC 时间，可以通过在所显示的 `hwclock` 时间加上或减去您所在时区的小时数来验证。例如，如果您所在的时区是 MST(美国山区时区)，已知是 GMT -0700，在本地时间上加 7 小时。

如果你的硬件使用的*不是* UTC 时间，就必须将下面的 `UTC` 变量值设为 *`0`* (零)，而"UTC=1"表示使用的是 UTC 时间。

运行下面的命令新建一个 `/etc/sysconfig/clock` 文件：

```
cat > /etc/sysconfig/clock << "EOF"
# Begin /etc/sysconfig/clock

UTC=1

# End /etc/sysconfig/clock
EOF 
```

在 [*http://www.linuxfromscratch.org/hints/downloads/files/time.txt*](http://www.linuxfromscratch.org/hints/downloads/files/time.txt) 有一个很好的关于如何处理 LFS 时间的提示，说明了例如时区、UTC、`TZ` 环境变量等等问题。

# 7.6\. 配置 Linux 控制台

本节讨论如何配置 `console` 初始化脚本来设置键盘映射表和控制台字体。如果您不使用非 ASCII 字符(英镑和欧元符号就是非 ASCII 字符的例子)，并且是美式键盘，可以跳过这一节，没有配置文件的话，`console` 初始化脚本不会做任何事情。

`console` 使用 `/etc/sysconfig/console` 作为配置文件以决定使用什么键盘映射表和屏幕字体，各种特定语言的 HOWTO(参见 [*http://www.tldp.org/HOWTO/HOWTO-INDEX/other-lang.html*](http://www.tldp.org/HOWTO/HOWTO-INDEX/other-lang.html)) 能帮助您完成配置。LFS-Bootscripts 软件包安装了一个已为一些国家配置好了的预制的 `/etc/sysconfig/console` 文件，如果您所在的国家已经被支持了，您可以去掉相应部分的注释。如果您还有疑问，请在 `/usr/share/kbd` 目录下查找正确的键盘映射表和屏幕字体。 阅读 `loadkeys(1)` 和 `setfont(8)` 的手册，来确定这些程序的正确参数。

`/etc/sysconfig/console 文件中可能包含这样格式的行：`VARIABLE ="value"。变量如下：

KEYMAP

这个变量指定 `loadkeys` 程序的参数。典型的像键盘映射的名字 "es"。 如果不设定参数，bootscript 就不会运行 `loadkeys` 程序, 而是使用默认的内核键盘映射。

KEYMAP_CORRECTIONS

这个参数很少用到，它可被用来指定再次调用`loadkeys` 程序的参数。对于提供的键盘映射不是很领人满意时，做些调整。 例如，我们要把一些正常情况下不会出现的欧洲字符包含到在键盘映射中, 那我们就需要把这个参数设为 “euro2”。

FONT

这个变量是为 `setfont` 程序设定的. 通常情况下它要包括 font 的名字, “-m”, 以及需要载入的 application character map 名. 比方., 要调用 “lat1-16” font 以及 “8859-1” application character map , 就把这个参数设为 “lat1-16 -m 8859-1”. 如果这个变量没有被设定，bootscript 会加载默认的 `setfont` 程序, 以及默认的一大堆

UNICODE

此参数设成“1”, “yes” 或 “true” 会把控制台改为 UTF-8 模式。在 UTF-8 的 locale 下比较有用，其他情况都是有害的。

LEGACY_CHARSET

对于许多的键盘布局，在 Kbd 包中没有提供 Unicode 键盘映射。如果这个变量被设置为一个有效的非 UTF-8 编码的键盘映射，`console` bootscript 会把它转换成 UTF-8 编码。注意，无效键（例如，键位本身不能产生字符，而是对下一个键产生的字符限制 ;在标准美国键盘中没有无效键）和组合键（例如，为了产生字符按下 Ctrl+. A E）没有相应的内核 patch 在 UTF-8 模式下是不能工作的。这个变量只用在 UTF-8 模式下。

BROKEN_COMPOSE

如果你应用第八章中的内核 patch ，把它设为 "0"。你不得不在 "-m" 开关之后，添加需要的字符集到 FONT 变量中。这个变量只用在 UTF-8 模式下。

对于把键盘映射直接编译进内核的支持，在内核中已经去掉了。因为这样会导致有一些错误的结果。

一些例子：

*   对于一个非 Unicode 的安装，只有 KEYMAP 和 FONT 变量是必需的。例如，在波兰语安装中，应当使用：

    ```
    cat &gt; /etc/sysconfig/console &lt;&lt; "EOF"# Begin /etc/sysconfig/console

    KEYMAP="pl2"
    FONT="lat2a-16 -m 8859-2"

    # End /etc/sysconfig/console
    EOF 
    ```

*   正如上面的方法，有时需要对对提供的键盘映射进行稍微的调整。下面的例子在德国键盘映射中添加欧洲符号：

    ```
    cat &gt; /etc/sysconfig/console &lt;&lt; "EOF"# Begin /etc/sysconfig/console

    KEYMAP="de-latin1"
    KEYMAP_CORRECTIONS="euro2"
    FONT="lat0-16 -m 8859-15"

    # End /etc/sysconfig/console
    EOF 
    ```

*   下面是一个保加利亚的 Unicode 例子，存在一个 UTF-8 键盘映射，并且没有定义无效键和组合键规则：

    ```
    cat &gt; /etc/sysconfig/console &lt;&lt; "EOF"# Begin /etc/sysconfig/console

    UNICODE="1"
    KEYMAP="bg_bds-utf8"
    FONT="LatArCyrHeb-16"

    # End /etc/sysconfig/console
    EOF 
    ```

*   由于在前面的例子中使用了 512-字符 的 LatArCyrHeb-16 字体，在控制台上不能再显示明亮的颜色，除非使用 framebuffer。如果你想不利用 framebuffer 来显示明亮的颜色，并使用本语种字符，像下面的说明，通过使用相应语言的 256-字符 字体，这仍然是可能实现的。

    ```
    cat &gt; /etc/sysconfig/console &lt;&lt; "EOF"# Begin /etc/sysconfig/console

    UNICODE="1"
    KEYMAP="bg_bds-utf8"
    FONT="cyr-sun16"

    # End /etc/sysconfig/console
    EOF 
    ```

*   下面的例子解释了键盘映射从 ISO-8859-15 到 UTF-8 的自动转变和在 Unicode 模式下打开无效键：

    ```
    cat &gt; /etc/sysconfig/console &lt;&lt; "EOF"# Begin /etc/sysconfig/console

    UNICODE="1"
    KEYMAP="de-latin1"
    KEYMAP_CORRECTIONS="euro2"
    LEGACY_CHARSET="iso-8859-15"
    BROKEN_COMPOSE="0"
    FONT="LatArCyrHeb-16 -m 8859-15"

    # End /etc/sysconfig/console
    EOF 
    ```

*   对于中文、日文、韩文等一些语言，Linux 的控制台不能显示需要的字符。这些语种的用户应当安装 X Window 系统，包含所需字符集的字体，以及合适的输入法（例如，SCIM 支持很多语言）。

### 注意

`/etc/sysconfig/console` 文件只能控制 Linux 文本控制台。在 X Windows、SSH 会话以及串口控制台中，设置键盘布局和终端字体是没有用的。

# 7.7\. 配置 sysklogd 脚本

`sysklogd` 脚本用 *`-m 0`* 选项调用 `syslogd` 程序，该选项关闭了周期时间戳标记，这个标记默认让 `syslogd` 每 20 分钟写入一次 log 文件。要打开周期时间戳标记，请编辑 `sysklogd` 脚本做相应修改。请运行 **`man syslogd`** 以获得更多信息。

# 7.8\. 创建 /etc/inputrc 文件

`inputrc` 文件为特定的情况处理键盘映射，这个文件被 Readline 用作启动文件，Readline 是 Bash 和其它大多数 shell 使用的与输入相关的库。

大多数人并不需要自定义键盘映射，所以下面的命令将创建一个适用于所有登陆用户的全局 `/etc/inputrc` 文件。如果你需要为某个用户覆盖默认的设置，你可以在该用户的主目录中创建一个包含自定义键盘映射的 `.inputrc` 文件。

要想了解更多关于如何编辑 `inputrc` 文件的信息，运行 `info bash` 以参考 bash 的 info 页的 *Readline Init File* 这一节，运行 `info readline` 以参考 readline 自己的 info 页也不错。

下面是一个基本的全局 `inputrc` 文件，那些选项的注释也一起包括在文件里。请注意，注释不能和命令放在同一行里。

```
cat > /etc/inputrc << "EOF"
# Begin /etc/inputrc
# Modified by Chris Lynn <roryo@roryo.dynup.net>

# Allow the command prompt to wrap to the next line
set horizontal-scroll-mode Off

# Enable 8bit input
set meta-flag On
set input-meta On

# Turns off 8th bit stripping
set convert-meta Off

# Keep the 8th bit for display
set output-meta On

# none, visible or audible
set bell-style none

# All of the following map the escape sequence of the
# value contained inside the 1st argument to the
# readline specific functions

"\eOd": backward-word
"\eOc": forward-word

# for linux console
"\e[1~": beginning-of-line
"\e[4~": end-of-line
"\e[5~": beginning-of-history
"\e[6~": end-of-history
"\e[3~": delete-char
"\e[2~": quoted-insert

# for xterm
"\eOH": beginning-of-line
"\eOF": end-of-line

# for Konsole
"\e[H": beginning-of-line
"\e[F": end-of-line

# End /etc/inputrc
EOF 
```

# 7.9\. Bash Shell 启动文件

shell 程序 `/bin/bash` (在此之后以“shell”称呼)使用了一个启动文件集全，来帮助创造一个运行的环境。每一个文件都有特定的用处，有的文件还能使登入与交互环境有所不同。放在 `/etc` 目录下的一些文件提供了全局设置。如果相类似的设置文件出现在某个用户起始文件夹下(~/)，那么在登入该用户时，它将替代该全局设置。

使用 `/bin/login` 读取 `/etc/passwd` 文件成功登录后，启动了一个交互登录 shell 。用命令行可以启动一个交互非登录 shell(例如 `[prompt]$``/bin/bash`)。非交互 shell 通常出现在 shell 脚本运行的时候，之所以称为非交互的，因为它正在运行一个脚本，而且命令与命令之间并不等待用户的输入。

要获得更多信息，请运行 `info bash` 以参考 *Bash Startup Files and Interactive Shells* 小节。

当以交互登录方式运行 shell 的时候，会读取 `/etc/profile` 和 `~/.bash_profile` 文件。

下面是一个基本的 `/etc/profile` 文件，设置了本地语言支持所必需的环境变量，适当设置这些变量会导致：

*   程序将产生翻译成本地语言的输出

*   正确的将字符分类为字母、数字和其它类，这样做是必要的，可以让 `bash` 在非英语 locale 下正确接收命令行输入的非 ASCII 字符。

*   为指定的国家设置正确的字母排序

*   适当的默认页面大小

*   为货币、时间和日期值设置正确的格式

这个脚本还设置了 `INPUTRC` 环境变量，让 Bash 和 Readline 使用我们先前创建的 `/etc/inputrc` 文件。

把下面的 *`[ll]`* 换成您想要设置的两个字母的语言代码(例如"en")，把 *`[CC]`* 换成适当的两个字母的国家代码(例如"GB")，把 *`[charmap]`* 换成规范的字符映射表。

Glibc 支持的所有 locales 列表可以使用下列命令获得：

```
locale -a 
```

很多 locale 有许多别名，比如"ISO-8859-1"也被称为"iso8859-1"和"iso88591"。一些应用程序不能正确的处理这些别名，所以安全的做法是使用 locale 的规范名称。要确定正确的规范名称，运行下面的命令，并把其中的 *`[locale name]`* 替换成 `locale -a` 命令的输出中你感兴趣的 locale (在这个例子中是"en_GB.iso88591")。

```
LC_ALL=`[locale name]` locale charmap 
```

对于"en_GB.iso88591" locale ，上面的命令将会打印：

```
ISO-8859-1 
```

这样你就得到了正确的 locale 设置是"en_GB.ISO-8859-1"。将使用上述试探方法得到的 locale 在添加进 Bash 启动文件前做充分的测试是很重要的。

```
LC_ALL=[locale name] locale country
LC_ALL=[locale name] locale language
LC_ALL=[locale name] locale charmap
LC_ALL=[locale name] locale int_curr_symbol
LC_ALL=[locale name] locale int_prefix 
```

上述命令将会打印与 *`[locale name]`* 对应的国家和语言名称、使用的字符编码、货币符号、国际长途电话号码前缀。如果上述命令中的任意一个出现了类似下面的错误信息，则表明你该 locale 要么在 Chapter 6 中没有安装，要么不被 Glibc 的默认安装支持。

```
locale: Cannot set LC_* to default locale: No such file or directory 
```

在这种情况下，你应当要么使用 `localedef` 命令安装相应的 locale ，要么选择一个其他的 locale 。接下来的指令都将假定 Glibc 没有出现上述错误信息。

一些 LFS 基本系统之外的软件包可能并不支持你选择的 locale 。一个例子就是 X 库(X Window System 的一部分)，它会输出如下信息：

```
Warning: locale not supported by Xlib, locale set to C 
```

有时可以通过移除该 locale 的字符映射(charmap)定义部分来解决这个问题——只要这样做不会改变 Glibc 中与该 locale 关联的字符映射关系(这个可以通过对两个 locale 运行 `locale charmap` 命令来检查)。例如将"de_DE.ISO-8859-15@euro"修改为"de_DE@euro"即可让这个 locale 被 Xlib 正确识别。

其他一些软件包在你选择的 locale 出乎其意料的情况下可能即使运行异常也不会显示错误信息。在这种情况下，研究一下其他 Linux 发行版是如何支持你选择的 locale 可能会得到更加有用的信息。

一旦确定了正确的 locale 设置，创建 `/etc/profile` 文件：

```
cat > /etc/profile << "EOF"
# Begin /etc/profile

export LANG=<ll>_<CC>.<charmap><@modifiers>
export INPUTRC=/etc/inputrc

# End /etc/profile
EOF 
```

"C"(默认)和"en_US"(美国英语用户的推荐值)这两个 locale 是不同的。

# 7.10\. 配置 localnet 脚本

`localnet`脚本的一部分工作是设置系统的主机名，这需要在 `/etc/sysconfig/network` 文件里配置。

运行下面的命令创建 `/etc/sysconfig/network` 文件并设置主机名：

```
echo "HOSTNAME=<lfs>" > /etc/sysconfig/network 
```

*`<lfs>`* 请用您的计算机名替换 *`[lfs]`* ，不要在这里输入全限定域名(Fully Qualified Domain Name)，FQDN 的信息稍后将放在 `/etc/hosts` 文件里。

# 7.11\. 定制 /etc/hosts 文件

要在 `/etc/hosts` 文件里配置网卡的 IP 地址、FQDN 和可能会用的别名，语法如下：

```
<IP address> myhost.example.org aliases 
```

除非您的计算机在 Internet 上是可访问的(例如，有一个注册的域名并分配到了一个合法的 IP 地址(块)(大多数用户没有))，请确保 IP 地址在私有网络 IP 地址范围内，正确的范围是：

```
类别     网络
        A     10.0.0.0
        B     172.16.0.0 到 172.31.0.255
        C     192.168.0.0 到 192.168.255.255 
```

一个合法的 IP 地址可能是 192.168.1.1 ，这个 IP 的合法 FQDN 可能是 www.linuxfromscratch.org(不推荐，因为这是个已合法注册的域名，这样做可能会造成域名服务器的问题)。

即使您没有网卡，仍然需要一个 FQDN，某些程序需要这个才能正常工作。

运行下面的命令创建 `/etc/hosts` 文件：

```
cat > /etc/hosts << "EOF"
# Begin /etc/hosts (network card version)

127.0.0.1 localhost
<192.168.1.1> <HOSTNAME.example.org> [alias1] [alias2 ...]

# End /etc/hosts (network card version)
EOF 
```

把 *`[192.168.1.1]`* 和 *`[&lt;HOSTNAME&gt;.example.org]`* 更改为特定用户或特别要求所需要的值(如果这台机器要连入一个已存在的网络，并且网络/系统管理员已经给您分配了一个 IP 地址)。

如果您不打算配置网卡，运行下面的命令创建 `/etc/hosts` 文件：

```
cat > /etc/hosts << "EOF"
# Begin /etc/hosts (no network card version)

127.0.0.1 <HOSTNAME.example.org> <HOSTNAME> localhost

# End /etc/hosts (no network card version)
EOF 
```

# 7.12\. 为设备创建惯用符号连接

## 7.12.1\. CD-ROM symlinks

我们可能装一些软件用到 cdrom dvd 等，因此我们会需要把 /dev/cdrom /dev/dvd 的符号链接加在`/etc/fstab`中。对于每一个 CD-ROM 设备，在 `/sys 下找到相应的目录`(例如, `/sys/block/hdd`) ，然后运行如下命令：

```
udevtest /block/hdd 
```

观察一下包含很多 *_id 输出的程序的行。

有两种方法可以创建 symlinks，可以用 model 名及序号，或是用设备在总线上的位置。 以第一种方法，可以创建如下文件：

```
cat >/etc/udev/rules.d/82-cdrom.rules << EOF # Custom CD-ROM symlinks
SUBSYSTEM=="block", ENV{ID_MODEL}=="SAMSUNG_CD-ROM_SC-148F", \
    ENV{ID_REVISION}=="PS05", SYMLINK+="cdrom"
SUBSYSTEM=="block", ENV{ID_MODEL}=="PHILIPS_CDD5301", \
    ENV{ID_SERIAL}=="5VO1306DM00190", SYMLINK+="cdrom1 dvd" 
EOF 
```

### 注意

这个例子能正常工作，但 udev 不能识别 \ 的继续上一行功能，所以若要用编辑器来编辑 udev 的规则时，一定要保证每行只有一个命令。

做完这些 symlinks 就会保持正常工作状态，即使把 cdrom 移到 IDE 总线的其他位置上也能正常工作。但是如果使用新的驱动器来替换原来的 SAMSUNG CD-ROM，`/dev/cdrom 符号链接将不会被创建。`

SUBSYSTEM=="block" 关键字是为了避免匹配一般的 SCSI 设备。 在没有这个关键字的情况下，若同时存在两个 SCSI CD-ROM， 这个符号链接有时会指向 `/dev/srX` 设备 ，但有时会错误的指向 `/dev/sgX`。

第二种方法的步骤：

```
cat >/etc/udev/rules.d/82-cdrom.rules << EOF # Custom CD-ROM symlinks
SUBSYSTEM=="block", ENV{ID_TYPE}=="cd", \
    ENV{ID_PATH}=="pci-0000:00:07.1-ide-0:1", SYMLINK+="cdrom"
SUBSYSTEM=="block", ENV{ID_TYPE}=="cd", \
    ENV{ID_PATH}=="pci-0000:00:07.1-ide-1:1", SYMLINK+="cdrom1 dvd" 
EOF 
```

这样，即使你使用不同的 model 来替换原来的设备，符号链接仍然是正确的，它指向在 IDE 总线上旧的位置。 ENV{ID_TYPE}=="cd" 关键字是为了确保符号链接在总线上的那个位置放的不是 CD-ROM 时，能够消失。

当然把两种方法混合使用也是可以的。

## 7.12.2\. Dealing with duplicate devices

在 节 7.4, "LFS 系统的设备和模块处理"提到过, `/dev` 下相同功能设备的顺序是随机的。例如，你有一个 USB 的网络摄像头和一个 TV 的调谐器，有时 `/dev/video0 指向网络摄像头，``/dev/video1 指向调谐器，但是在重启之后可能就会改变。除了网卡和声卡之外的其他设备，都可以通过创建 udev 的规则来定制固定的符号链接。`网卡的解决 方法请见 节 7.13, "配置网络脚本", 声卡解决方法请见 [*BLFS*](http://www.linuxfromscratch.org/blfs/)。

每一个设备都可能有这个问题（即使这个问题在你现在的发行版中不存在），在 `/sys/class` 或 `/sys/block 下找到相应的目录。` 对于视频设备，可能是 `/sys/class/video4linux/video_`X`_`。 找出标记设备唯一性的属性（通常是 设备提供商、产品 ID 以及序列号）：

```
udevinfo -a -p /sys/class/video4linux/video0 
```

接下来，写一个创建符号链接的规则，例如：

```
cat >/etc/udev/rules.d/83-duplicate_devs.rules << EOF # Persistent symlinks for webcam and tuner
KERNEL=="video*", SYSFS{idProduct}=="1910", SYSFS{idVendor}=="0d81", \
    SYMLINK+="webcam"
KERNEL=="video*", SYSFS{device}=="0x036f", SYSFS{vendor}=="0x109e", \
    SYMLINK+="tvtuner" 
EOF 
```

结果 `/dev/video0` 和 `/dev/video1` 设备仍然随机指向调谐器和网络摄像头（因此不应当直接使用），但是符号链接 `/dev/tvtuner` 和 `/dev/webcam` 总是指向正确的设备。

关于书写 Udev 规则的更多信息，可以查看 `/usr/share/doc/udev-096/index.html`。

# 7.13\. 配置网络脚本

本节仅适用于需要配置网卡的情况。

如果不使用网卡，就不需要创建关联网卡的配置文件，这样的话，在所有运行级目录(`/etc/rc.d/rc*.d`) 下删除 `network` 符号链接。

## 7.13.1\. 创建网络接口的稳定名称

这一段的说明对于单网卡是可选的。

由于 Udev 和 网络驱动的模块化，网络设备的接口的加载顺序在每次 reboot 后可能会不同，因为驱动是并行加载的，所以顺序会变成随机。例如，在一台计算机上有两块网 卡 Intel 和 Realtek。Intel 制造的网卡可能是 eth0，Realtelk 的网卡是 eth1;但是重启后网卡的顺序可能反过来。为避免这种情况，我们应该 根据网卡的 MAC 地址或总线位置来为他们命名。

如果想要根据 MAC 地址来识别网卡，可以使用如下命令：

```
grep -H . /sys/class/net/*/address 
```

为每个网卡（除 loopback），设计一个描述性的名字，比方 “realtek”, 然后参照如下建立 Udev 规则：

```
cat > /etc/udev/rules.d/26-network.rules << EOFACTION=="add", SUBSYSTEM=="net", SYSFS{address}=="_`00:e0:4c:12:34:56`_", \
    NAME="_`realtek`_"
ACTION=="add", SUBSYSTEM=="net", SYSFS{address}=="_`00:a0:c9:78:9a:bc`_", \
    NAME="_`intel`_"
EOF 
```

### 注意

虽然这个例子可以正常工作，但要注意 udev 不能识别 \ 的继续上一行的功能。所以如果用文本编辑器来编辑就一定要保证每个规则占一行。

如果要以总线位置作为标准，可以创建如下的 Udev 规则：

```
cat > /etc/udev/rules.d/26-network.rules << EOFACTION=="add", SUBSYSTEM=="net", BUS=="_`pci`_", ID=="_`0000:00:0c.0`_", \
    NAME="_`realtek`_"
ACTION=="add", SUBSYSTEM=="net", BUS=="_`pci`_", ID=="_`0000:00:0d.0`_", \
    NAME="_`intel`_"
EOF 
```

这个规则会把网卡名字每次都定为 “realtek” 和 “intel”, 以替代 eth0 和 eth1 （即：原来的 eth0 和 eth1 接口不存在了 除非把他们加到 NAME 下）。在下面的文件中我们使用描述性的名字来替代 eth0 等。

需注明以上规则并不永远适用。例如, 当 VLAN 和 网桥 使用时，以 MAC 为基准的命名就会出问题。由于 VLAN 和 网桥 在网卡上有相同的 MAC 地址。 如果只想重新命名网卡接口，而不是 brige 和 VLAN 接口，但是规则都会匹配它们。如果使用虚拟接口，我们会有两种解决办法。其一是把 DRIVER=="?*" 关键字加在 SUBSYSTEM=="net" 后。但这种方法对于一些老型号的网卡不起作用，因为这些网卡的 uevent 中没有 DRIVER 变量，因此 按规则就不可能匹配到这些卡。 另外一种就是以总线位置命名。

另外一种已知的不能工作的情况存在于无线网络中，当应用 MadWifi 或 HostAP 驱动时, 他们会以相同的 MAC 地址和总线位置创建至少两个接口。例如，Madwifi 驱动会创建一个 athX 和 wifiX 接口（X 是一个数字）。为区别 这些接口，可以把 KERNEL 参数，比如 KERNEL=="ath*" 加到 SUBSYSTEM=="net"后。

可能还有很多情况会导致不能正常工作，现在这方面的 bug 仍然不断的报告给 Linux 的各个发布版，没有一个解决方法可以解决所有的情况。

## 7.13.2\. 创建网络接口配置文件

network 脚本启用或关闭哪个接口由 `/etc/sysconfig/network-devices` 目录下的文件决定，这个目录下的文件应该是类似于 `ifconfig.xyz` 的形式，这里"xyz"是网络接口名(例如 eth0 或者 eth0:1)。这个目录中的文件将定义接口的属性，比如 IP 地址、子网掩码等等。

在这个目录下新建文件，下面是一个为 *eth0* 设备创建 `ipv4` 文件的示例：

```
cd /etc/sysconfig/network-devices &&mkdir -v ifconfig.eth0 &&cat > ifconfig.eth0/ipv4 << "EOF"ONBOOT=yes
SERVICE=ipv4-static
IP=192.168.1.1
GATEWAY=192.168.1.2
PREFIX=24
BROADCAST=192.168.1.255
EOF 
```

每个文件中的这些变量的值都要改成您的设置，如果 `ONBOOT` 变量设置为"yes"，network 脚本会在系统启动的时候启动 NIC(Network Interface Card 网络接口卡，简称网卡)，如果设置为"yes"以外的值，网卡会被 network 脚本忽略而没有启动。

`SERVICE` 变量定义获取 IP 地址的方式，LFS-Bootscripts 有一套模块化的 IP 地址分配格式，并在 `/etc/sysconfig/network-devices/services` 目录下为其它的 IP 分配方式创建了附加的文件，这通常用作 DHCP(Dynamic Host Configuration Protocol 动态主机配置协议)方式，在 BLFS 里有详细介绍。

`GATEWAY` 变量应该设置为默认网关的 IP 地址，如果没有默认网关，就把这个变量完全注释掉。

`PREFIX` 变量设置为子网使用的位数，IP 地址的每个字节是 8 bit ，如果子网掩码是 255.255.255.0 ，那么它使用前三个字节(24 bit)指定网络号；如果网络掩码是 255.255.255.240 ，它用前 28 bit 来指定网络号。长于 24 bit 的前缀一般由 DSL 和 cable 的 ISP(Internet Service Providers 因特网服务提供商)使用，我们的例子里(PREFIX=24)，子网掩码是 255.255.255.0 ，请根据您的网络情况调整 `PREFIX` 变量。

## 7.13.3\. 创建 /etc/resolv.conf 文件

如果系统要连接到 Internet 上，就需要 DNS(Domain Name Service 域名服务)名称解析的手段，来把 Internet 域名解析为 IP 地址，反之亦然。在 `/etc/resolv.conf` 文件里设置 ISP 或网络管理员提供的域名服务器的 IP 地址就可以达到这个目的了，运行下面的命令创建这个文件：

```
cat > /etc/resolv.conf << "EOF"# Begin /etc/resolv.conf

domain {<域名>}
nameserver <主域名服务器 IP 地址>
nameserver <副域名服务器 IP 地址>

# End /etc/resolv.conf
EOF 
```

把 *`[域名服务器 IP 地址]`* 替换为您的域名服务器的 IP 地址。域名服务器常常不止一项(作为备份用途)，如果您只需要一个域名服务器，把文件里的第二行 *nameserver* 删除就可以了。在局域网里这个 IP 地址还可能是路由器。