# 8\. 使 LFS 系统能够启动

# 8.1\. 简介

此时可以让 LFS 启动了，本章节讨论创建 `fstab` 文件, 为新的 LFS 系统编译一个内核，并安装 Grub 引导程序，在启动菜单上选择 LFS 系统后能启动系统。

# 8.2\. 创建 /etc/fstab 文件

一些程序用 `/etc/fstab` 文件来确定哪一些文件系统是默认被加载了，加载顺序情况，哪些必须被检查的(完整性错误校验)。创建一个新的文件系统表大致如下所示：

```
cat > /etc/fstab << "EOF"
# Begin /etc/fstab

# file system  mount-point  type   options         dump  fsck
#                                                        order

/dev/<xxx>     /            <fff>  defaults        1     1
/dev/<yyy>     swap         swap   pri=1           0     0
proc           /proc        proc   defaults        0     0
sysfs          /sys         sysfs  defaults        0     0
devpts         /dev/pts     devpts gid=4,mode=620  0     0
shm            /dev/shm     tmpfs  defaults        0     0
# End /etc/fstab
EOF 
```

在你的系统上替换 *`<xxx>`*，*`<yyy>`*，和 *`<fff>`* 为适当的值，例如， `hda2`, `hda5`，and `ext3`。 关于文件中六个字段的详细信息，可通过`man 5 fstab`获取。

这个 `/dev/shm` 挂载点是为 `tmpfs` (虚拟内存文件系统)能包括启用 POSIX 共享内存。这需要内核必须在构建的时候支持这个选项才能起作用(更多相关信息在下一个章节)。请注意，目前很少软件使用 POSIX 共享内存。 所以，认为 `/dev/shm` mount 挂载点选项是可选择的，更多信息请查看内核源码树里的 `Documentation/filesystems/tmpfs.txt`。

文件系统有 MS-DOS 或 Windows 血统(i.e.: vfat，ntfs，smbfs, cifs，iso9660，udf)需要有"iocharset" 加载选项载，以使文件名称的非 ASCII(美国信息交换标准代码)特征被适当解释。这个选项和你的所处位置特征是一样的，用这个方式调整内核支持它。如果需要操作相关的特征定义(在 File systems -> Native Language Support 下可找到)，可以编译进内核里或编译成模块。 "codepage" 选项同样也是为 vfat 和 smbfs 文件系统所需要. 它应该被设置为 MS-DOS 在你的国家里使用的 codepage 数字号。举个例子，为了挂在 USB flash 设备，ru_RU.KOI8-R 的使用者需要在 `/etc/fstab` 里的以下行:

```
/dev/sda1    /media/flash vfat
    noauto,user,quiet,showexec,iocharset=koi8r,codepage=866 0 0 
```

ru_RU.UTF-8 使用者的相应行：

```
/dev/sda1    /media/flash vfat
    noauto,user,quiet,showexec,iocharset=utf8,codepage=866 0 0 
```

### 注意

在后面的例子里，内核发出如下信息：

```
FAT: utf8 is not a recommended IO charset for FAT filesystems,
    filesystem will be case sensitive! 
```

这个否定的建议应该可以被忽略，因为所有的其他"iocharset" 值选项造成 UTF-8 locales 文件名的错误显示。

它也可能是在内核构造的时候为一些文件系统指定默认的 codepage 和 iocharset。相关的参数是指定的 "默认 NLS 选项" (CONFIG_NLS_DEFAULT)，"默认远程 NLS 选项" (CONFIG_SMB_NLS_DEFAULT)，"默认的 FAT 的 codepage " (CONFIG_FAT_DEFAULT_CODEPAGE)，和 "默认 FAT 的 iocharset" (CONFIG_FAT_DEFAULT_IOCHARSET)。在这里不叙述在内核编译时为 ntfs 文件的系统设置。

# 8.3\. Linux-2.6.16.27

Linux 内核软件包包含内核源代码及其头文件。

**预计编译时间：** 1.5 - 3 SBU**所需磁盘空间：** 310 - 350 MB

## 8.3.1\. 安装 kernel

编译内核包含几个步骤——配置、编译和安装。阅读内核源码树里的 `README` 文件可选择不同于本书的其他配置内核方法。

预先设定情况下，当在 UTF-8 键盘模式里，键没反应，是 Linux 内核发生的字节顺序错误 。同样，在 UTF-8 模式起作用的情况下，有一个不能拷贝和粘贴非 ASCII 的特征。用发布的补丁可修复：

```
patch -Np1 -i ../linux-2.6.16.27-utf8_input-1.patch 
```

运行下面的命令准备编译：

```
make mrproper 
```

这能保证内核树绝对干净。内核开放团队推荐每次编译内核之前都运行这个命令。不要相信解压后的源码树就是干净的。

用菜单形式界面的配置内核。BLFS 有一些关于配置内核必备条件的细节，不在 LFS 包里，在[*http://www.linuxfromscratch.org/blfs/view/svn/longindex.html#kernel-config-index*](http://www.linuxfromscratch.org/blfs/view/svn/longindex.html#kernel-config-index):

```
make menuconfig 
```

本文译者也有一篇文章，[《Linux-3.10-x86_64 内核配置选项简介》](http://www.jinbuguo.com/kernel/longterm-3_10-options.html)，介绍了几乎每一个内核选项。对于使用 VMware 虚拟机的读者，这里还有一份基于 2.6.24 内核的 [.config](http://lamp.linux.gov.cn/miniLAPP/patchs/linux-2.6.24-config.txt) 文件可供参考(切忌照搬照抄,因为这样是行不通的)。

选择 **make oldconfig** 在同样情况下，可能会更适合。阅读 `README` 可获取更多信息。

如果愿意，你可以跳过内核配置，直接复制内核配置文件`.config`,从主机系统(如果可用)解压到`linux-2.6.16.27`目录。当然，我们不推荐这个方式。通常探究所有配置菜单项，再根据需要创建内核是最佳的。

编译内核镜像和模块：

```
make 
```

如果使用内核模块，`/etc/modprobe.conf` 文件是必须的。有关模块和内核配置的信息在 7.4, "LFS 系统的设备和模块处理" ，还有`linux-2.6.16.27/Documentation`目录中的内核文档。同样，引起注意的可能还有 `modprobe.conf(5)` 。

安装模块，如果内核配置使用它们：

```
make modules_install 
```

内核编译完成后，增加步骤完成安装是必须的。一些文件需要拷贝副本到 `/boot` 目录。

内核镜像的路径，根据不同的平台可能会改变，下面的命令假定在 x86 架构上：

```
cp -v arch/i386/boot/bzImage /boot/lfskernel-2.6.16.27 
```

`System.map` 是一个内核的符号文件 。它映射每个内核 API 函数的入口，以及内核在运行中内核数据结构的地址。 运行下面这个命令安装这个文件：

```
cp -v System.map /boot/System.map-2.6.16.27 
```

内核配置文件 `.config` 产生于**make menuconfig** 这个步骤，包含所有的内核配置选择被编译。一个好主意是保留这个文件以备将来参考：

```
cp -v .config /boot/config-2.6.16.27 
```

安装 Linux 内核文档：

```
install -d /usr/share/doc/linux-2.6.16.27 &&
cp -r Documentation/* /usr/share/doc/linux-2.6.16.27 
```

有一点重要提示，内核源码目录的所有者不是 *root*。只要是用 *root* (类似我们在 chroot 环境里)，解压出来的文件不是计算机上的用户和组 ID，对于其他包这不是问题，在安装后被删除源码树。但是，Linux 源码通常保留很长时间。因为 碰巧有一个用户 ID 和这个软件打包者的用户组 ID 相同，那么他可以拥有对内核源码的写权限。

如果这个内核源码准备保留，在`linux-2.6.16.27` 目录运行 `chown -R 0:0` ，确保所有文件的属主是 *root*.

### 警告

一些内核文档推荐建立一个`/usr/src/linux` 的链接指向内核源码目录。这个是对 2.6 版本内核的要求，而且在 LFS 系统上 *不允许* ，它会导致你可能在完成 LFS 系统构建后，再安装其他软件包出现错误。

同样，系统的 `include`目录下的头文件应该 *永远* 保持 Glibc 编译后的版本， 和 Linux-Libc-Headers 包相同，所以应该要 *决不* 替换内核的头文件。

## 8.3.2\. Linux 的内容

**安装的文件：** config-2.6.16.27, lfskernel-2.6.16.27, System.map-2.6.16.27

### 简要描述

|  |  |
| --- | --- |
| `config-2.6.16.27` | 包含所有内核配置选择后的项 |
| `lfskernel-2.6.16.27` | Linux 系统的引擎。当启动计算机时，内核是操作系统装载的第一个部分，它检测并初始化所有的电脑硬件的组件，然后将这些设备以文件树的形式存放使得其它软件可以访问，并且能让单个 CPU 成为多任务处理机器能力，可以同时运行许多程序。 |
| `System.map-2.6.16.27` | 显示地址和符号的文件；它映射内核里所有函数和数据结构的入口和地址。 |

# 8.4\. 使 LFS 系统能够启动

你的全新 LFS 系统差不多要完成了。 最后要做的事是确保系统可以正常启动。下面的指令仅适用于 IA-32 架构的计算机，就是主流的 PC 机。 关于其它架构计算机 "boot loading（引导装载）"的信息可以在相应的资源里找到。

引导装载是一个很复杂的问题，因此接下来会有一些警告的话。所以需要熟悉当前的引导装置和硬盘上其他操作系统需要能被启动。确定紧急启动盘已经准备了，假如电脑变成不能用了（不能启动）能够 "援救"。

先前，我们为这个步骤编译和安装了 GRUB 引导装载程序做了准备。这个程序包括了在硬盘的指定位置上写的一些特殊 GRUB 文件，我们强烈推荐你创建一张 GRUB 引导软盘作为备份，插入一张空白软盘并输入下面的命令：

```
dd if=/boot/grub/stage1 of=/dev/fd0 bs=512 count=1
dd if=/boot/grub/stage2 of=/dev/fd0 bs=512 seek=1 
```

取出软盘并在安全的地方存放，现在，运行 `grub` shell：

```
grub 
```

GRUB 使用它自己的驱动器和分区命名结构，形式是 *(hdn,m)*，这里的 *n* 是硬盘驱动号， *m* 是分区号， 两个都是从零开始。例如，分区 `hda1` 是 GRUB 的 *(hd0,0)* ， `hdb3`是 *(hd1,2)*. 与 Linux 不同的是， GRUB 不把光驱作为硬盘驱动器。 例如，假如 `hdb` 是光盘驱动器，第二个硬盘驱动器是 `hdc`，第二个硬盘驱动器仍然是 *(hd1)*。

用上面的信息为 root 分区（或 boot 分区，假如是单独使用了分区的情况下）。 下面的例子里假定 root 分区（或单独的 boot 分区）是 `hda4`.

告诉 GRUB 在哪里搜索它的 `stage{1,2}` 文件。用 Tab 键能在各处让 GRUB 显示可选择项：

```
root (hd0,3) 
```

### 警告

下一个命令会覆盖当前的引导装载程序，如果不需要的话就不要运行这个命令，例如，使用第三方启动管理器来管理主引导记录 (MBR)。当然，现在的情况是安装 GRUB 到 LFS 分区的“boot sector”更有意义。在这个例子里，下一个命令将变成 **`setup (hd0,3)`** 。

告诉 GRUB 安装它自己到 `hda`的 MBR：

```
setup (hd0) 
```

如果一切顺利，GRUB 会报告在 `/boot/grub`找到它的文件。现在可以退出 `grub` shell：

```
quit 
```

创建一个 "显示菜单"文件定义 GRUB 的启动菜单：

```
cat > /boot/grub/menu.lst << "EOF"
# Begin /boot/grub/menu.lst

# By default boot the first menu entry.
default 0

# Allow 30 seconds before booting the default.
timeout 30

# Use prettier colors.
color green/black light-green/black

# The first entry is for LFS.
title LFS 6.2
root (hd0,3)
kernel /boot/lfskernel-2.6.16.27 root=/dev/hda4
EOF 
```

如果需要可以为宿主系统增加一项，看起来如下：

```
cat >> /boot/grub/menu.lst << "EOF"
title Red Hat
root (hd0,2)
kernel /boot/kernel-2.6.5 root=/dev/hda3
initrd /boot/initrd-2.6.5
EOF 
```

如果是 Windows 的双启动系统，下面的项能够启动它：

```
cat >> /boot/grub/menu.lst << "EOF"
title Windows
rootnoverify (hd0,0)
chainloader +1
EOF 
```

如果用 `info grub` 不能获取足够的信息，更多 GRUB 资料可以在它的网站找到 [*http://www.gnu.org/software/grub/*](http://www.gnu.org/software/grub/).

FHS 规定 GRUB 的 `menu.lst` 文件必须链接到 `/etc/grub/menu.lst`。为了符合这个规定，可以用下面的命令：

```
mkdir -v /etc/grub &&
ln -sv /boot/grub/menu.lst /etc/grub 
```