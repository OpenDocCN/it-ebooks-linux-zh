# 第六章 安装系统基础软件

# 6.1\. 简介

在这一章，我们进入"建筑工地"，开始精心构建 LFS 系统。也就是指我们通过 chroot 命令进入一个临时的微型 Linux 系统，并作一些最后的准备，然后开始安装软件包。

软件的安装非常直截了当。尽管许多情况下对安装过程的说明可以更加简洁，但为了消除可能出现的错误，我们为每一个包都提供了全面的安装说明。理解 Linux 系统是如何工作的关键在于明白每个包的用途以及为什么用户(或系统)需要它。对每一个安装的软件包，我们都给出了对其内容的简要说明，另外，对该软件包所安装的每一个程序文件和库文件也作了简要的描述。

如果在本章中进行编译器优化，那么请看看编译器优化提示：[*http://www.linuxfromscratch.org/hints/downloads/files/optimization.txt*](http://www.linuxfromscratch.org/hints/downloads/files/optimization.txt)，也可以看看 LinuxSir 上的一篇帖子：[GCC 编译选项及优化提示](http://www.linuxsir.org/bbs/showthread.php?t=222670)。编译器优化可以使程序运行的稍快一些，但也会出现某些编译问题。如果某个包在使用优化的情况下无法通过编译，试试不用优化编译能不能解决问题。即使使用优化编译成功，由于源码与编译工具之间复杂的相互作用，程序仍有可能被错误的编译了。要注意 `-march` 和 `-mtune` 选项或许会导致一些工具链软件包(Binutils, GCC, Glibc)的问题。使用编译器优化得到的小幅度性能提升，与它带来的风险相比微不足道。所以初次编译 LFS 的用户最好不要使用任何优化，你的系统依然会又快又稳定。

本章中软件包的安装顺序应当严格遵守，以确保没有一个程序会把 /tools 作为路径硬连接到代码中。同样不要并行编译包。并行编译可能会节省时间(特别是在双 CPU 的机器上)，但也可能造成程序包含 /tools 硬连接路径，以致在 /tools 目录被删除之后，程序无法运行。

在每个软件包安装说明页的首部都提供了与该包相关的一些信息，包括：包内容的简要说明、编译大约所需时间、编译过程所需磁盘空间、编译所依赖的软件包。在安装说明之后还有该包所安装的程序和库的列表以及对它们的简要说明。

# 6.2\. 挂载虚拟内核文件系统

虚拟内核文件系统(Virtual Kernel File Systems)，是指那些是由内核产生但并不存在于硬盘上(存 在于内存中)的文件系统，他们被用来与内核进行通信。

首先让我们为虚拟内核文件系统建立挂载目录：

```
mkdir -pv $LFS/{dev,proc,sys} 
```

## 6.2.1\. 创建初始设备节点

内核在引导时要求某些设备节点必须存在(特别是 console 和 null )，这些设备节点必须创建在硬盘 上才能使得内核在 udev 尚未启动之前就可以使用它们，此外还有当 Linux 以*`init=/bin/bash`* 启动。使用下面的命令来创建这些节点：

```
mknod -m 600 $LFS/dev/console c 5 1
mknod -m 666 $LFS/dev/null c 1 3 
```

## 6.2.2.挂载并填充 /dev 目录

推荐的向 /dev 目录填充设备的方法是在 /dev 上挂载一个虚拟文件系统(比如 tmpfs)，然后在设备 被检测到或被访问到的时候(通常是在系统引导的过程中)动态创建设备节点。既然现在新的系统尚未被引导， 那么就有必要通过手工挂载和填充 /dev 目录。这可以通过绑定挂载宿主系统的 /dev 目录。绑定挂载是一种 特殊的挂载方式，允许你创建一个目录或者是挂载点的镜像到其他的地方。可以使用下面的命令：

```
mount --bind /dev $LFS/dev 
```

## 6.2.3\. 挂载虚拟内核文件系统

现在挂载虚拟内核文件系统：

```
mount -vt devpts devpts $LFS/dev/pts
mount -vt tmpfs shm $LFS/dev/shm
mount -vt proc proc $LFS/proc
mount -vt sysfs sysfs $LFS/sys 
```

# 6.3\. 包管理

对于 LFS BOOK，包管理通常被请求加进去。一个包管理器允许跟踪文件的安装，使删除或升级软件包变得简单。在这一部分里，我们不会讨论或者是推荐任何一个包管理器。我们讲述的是一些流行的技术，以及他 们是怎么工作的。对于你来说，一个完美的包管理器可能在这些技术之中，也可能是一些技术的结合。这一部分 简明的描述了当升级软件包的时候会出现的几个问题。

LFS 和 BLFS 中没有涉及包管理器的几个原因有：

*   把精力拿来做包管理器，就偏离了我们的 LFS 的初衷：一个 Linux 系统是如何构建的。

*   包管理有很多解决方案，每一个都有优点和缺点。任何一个都不会令其他种类的 fans 满意的。

有一些 hints 是关于包管理。可以查看 [*Hints subproject*](http://www.linuxfromscratch.org/hints/downloads/files/) 来选择一个适合你的。

## 6.3.1\. 升级问题

一个包管理器会使升级一个软件包到新的版本变得很简单。通常 LFS 和 BLFS 手册中的说明可以用来升级软件包。 这里有几点，你在升级软件包的时候应该注意的，尤其是在一个运行着的系统上。

*   如果工具链中的一个软件包 (Glibc, GCC 或 Binutils)需要升级到一个新的次版本，重新构建 LFS 是比较安全的。 尽管你可以按照依赖关系，只是重新构建一部分的软件包。但我们并不推荐这样做。例如，如果 glibc-2.2.x 需要 升 级到 glibc-2.3.x,重新构建是比较安全的。对于小版本的升级，简单的重新安装通常可以正常的工作，但是不能够保证。 例如，从 glibc-2.3.4 升级到 glibc-2.3.5 通常不会有问题。

*   如果包含一个共享库的软件包升级，并且共享库的名字发生了变化。动态链接到这个库的所有的 包都需要重新编译链接到新的库（注意包的版本和库的名字没有关系）。例如，一个软件包 foo-1.2.3 安装了一个名为 `libfoo.so.1` 的共享库。你升级这个包到新版本 foo-1.2.4，这个新版本安装名为 `libfoo.so.2` 的共享库。这种情况下，所有链接到 `libfoo.so.1` 的包都需要重新编译链接到 `libfoo.so.2`。注意在依赖的软件包没有编译完之前，不要删除原来的库。

## 6.3.2\. 包管理技术

下面是一些常用的包管理的技术。在决定安装包管理器之前，对多种包管理技术进行一下研究，找出某一种的缺点。

#### 6.3.2.1\. 全部记在心里！

是的，这是一种包管理的技术。一些人不需要包管理器，因为他们对包都非常熟悉，知道每一个包所安装的文件。 一些人也不需要包管理器，是因为当一个软件包改变时，他们重新构建整个系统。

#### 6.3.2.2\. 分别安装到不同目录

这是一种最简单的包管理方式，不需要安装额外的软件包来管理安装过程。每一个软件包安装到不同的目录下。 例如，包 foo-1.1 安装到 `/usr/pkg/foo-1.1` ，创建一个从 `/usr/pkg/foo` 指向 `/usr/pkg/foo-1.1` 的符号链接。当安装一个新版本 foo-1.2 时，它会安装在 `/usr/pkg/foo-1.2` ，前面的符号链接也指向新版本。

一些环境变量，像 `PATH`, `LD_LIBRARY_PATH`, `MANPATH`, `INFOPATH` 和 `CPPFLAGS` 都需要增加 `/usr/pkg/foo`。当软件包数量大了之后，就难于管理了。

#### 6.3.2.3\. 符号连接风格的包管理

这是前面包管理技术的一个变种。每一个包也是按照类似于前面方法进行安装。但是不是创建符号链接， 而是每一个都被链接到 `/usr` 目录。这样就不需要添加环境变量了。尽 管有时在安装的时候符号链接自动创建，还是有很多的是采用这种方法的。一些比较流行的有： Stow，Epkg，Graft，和 Depot。

安装过程需要伪造，这样包就会认为自己被安装到了 `/usr` 目录，尽管实际上 它们安装到 `/usr/pkg` 目录下。以这种风格安装并不是很麻烦的事情。比如，你要 安装一个包 libfoo-1.1 。用下面的指令安装就不合适：

```
./configure --prefix=/usr/pkg/libfoo/1.1
make
make install 
```

安装是没有问题，但是依赖的包不能够像你想像的那样链接到 libfoo。如果你编译一个链接到 libfoo 的包， 你就要注意，要链接到 `/usr/pkg/libfoo/1.1/lib/libfoo.so.1` ，而不是 像你想像的那样链接到 `/usr/lib/libfoo.so.1`。正确的方法是使用 `DESTDIR` 来伪造包的安装。可以这样来使用这种方法：

```
./configure --prefix=/usr
make
make DESTDIR=/usr/pkg/libfoo/1.1 install 
```

大多数的包都支持这种方法，但还是有一些不支持。对于这些不兼容的包，你可以手工安装，或许把一些有问题的包 安装到 `/opt` 可能更简单。

#### 6.3.2.4\. 基于时间戳

这种技术里，在安装包之前，会创建一个时间戳标记文件。安装之后，简单的使用的 `find` 命令的某些选项就能生成一个时间戳标记文件创建之后安装的文件的日志。install-log 就是利用这种技术写的包管理器。

尽管这种方法简单，但是它有两个缺点。如果在安装的过程中，安装的文件的时间戳不是当前的时间，这些文件将不会被包管理器记录。 另外，这种方案只能用在一次只有一个包安装时。如果有两个包在两个终端下同时安装，那么这时的日志是不可靠的。

#### 6.3.2.5\. 基于 LD_PRELOAD

这种方法下，在安装之前会有一个库被提前加载。在安装的过程中，这个库就会跟踪正在安装的包，通过给自己附加上 各种可执行性的动作，像 `cp`, `install`, `mv` 。来跟踪那些修改文件系统系统调用。为了让这种方法正 常的工作，所有的可执行文件需要都是动态链接的且没有设置 suid 和 sgid 位。预先加载库可能会产生一些讨厌的 副作用。因此，建议做一些测试，来确保包管理器不会破坏任何东西，并且能够记录所有适当的文件。

#### 6.3.2.6\. 创建包的归档

在这种方案中，包被分别安装到不同的目录树下，就像符号连接风格的包管理描述的。安装之后， 系统就会使用安装的文件创建一个包的归档。这个档案可以在本机上安装包，甚至可以在其他机器上 安装包。

这种方法被大多数商业发行版的包管理器采用。例如，RPM（自然是[*Linux Standard Base Specification*](http://lsbbook.gforge.freestandards.org/package.html#RPM)所必需），pkg-utils，Debian 的 apt 和 Gentoo 的 Portage 系统。 有一个描述在 LFS 系统中如何应用这样的包管理器的 hint，参见 [*http://www.linuxfromscratch.org/hints/downloads/files/fakeroot.txt*](http://www.linuxfromscratch.org/hints/downloads/files/fakeroot.txt).

#### 6.3.2.7\. 基于用户的管理

这种方案是 LFS 特有的，是由 Matthias Benkmann 提出的，可以参考 [*Hints Project*](http://www.linuxfromscratch.org/hints/downloads/files/)。在这种方案里，每一个包都是以不同的用户安装到标准的目录里。通过检验 user ID 可以很容易的标识一个软件包。这种方法的特点和缺点非常复杂，这里就不描述了。详细内容参见 [*http://www.linuxfromscratch.org/hints/downloads/files/more_control_and_pkg_man.txt*](http://www.linuxfromscratch.org/hints/downloads/files/more_control_and_pkg_man.txt)。

# 6.4\. 进入 Chroot 环境

现在将要进入 chroot 环境开始编译安装最终的 LFS 系统了，注意：在这里我们只使用临时构建的工具。以 root 身份运行以下命令进入构建环境：

```
chroot "$LFS" /tools/bin/env -i \
    HOME=/root TERM="$TERM" PS1='\u:\w\$ ' \
    PATH=/bin:/usr/bin:/sbin:/usr/sbin:/tools/bin \
    /tools/bin/bash --login +h 
```

env 命令的参数 -i 的作用是清除所有 chroot 环境变量。后面是重新设定 HOME, TERM, PS1, PATH 等变量的值。TERM=$TERM 设定虚拟根环境中的 TERM 的值与 chroot 外面的一样。这个值是让像 vim 和 less 之类的程序可以正确操作。如果还需要重新设置其它的值，如 CFLAGS 或 CXXFLAGS ，这里是个不错的位置。

从这里开始，不再需要 LFS 环境变量了，因为所有的工作都被限制在 LFS 文件系统里面。这是由于已经告诉了 Bash shell $LFS 是现在的根目录(/)。

注意，这里 /tools/bin 位于 PATH 的最后面。也就是说当软件包的最终版本安装之后就不再使用临时工具了。为了使 shell 无法"记住"可执行二进制代码的位置，需要通过使用 +h 参数关闭 bash 的散列功能。

注意此时 bash 提示符会显示：I have no name! 这是正常的，因为 /etc/passwd 还没有创建。

### 注意

这一章剩下的命令以及后面几章的命令都是在 chroot 环境下进行的。如果你离开了这个环境（比如重启）， 要确保内核虚拟文件系统挂载，像在 节 6.2.2, "挂载并填充 /dev 目录" 和 节 6.2.3, "挂载虚拟内核文件系统" 描述的。在继续安装之前，再次进入 chroot 环境。

# 6.5\. 创建系统目录结构

现在我们在 LFS 分区中创建目录树结构，用下列命令能创建一个标准的目录树：

```
mkdir -pv /{bin,boot,etc/opt,home,lib,mnt,opt}
mkdir -pv /{media/{floppy,cdrom},sbin,srv,var}
install -dv -m 0750 /root
install -dv -m 1777 /tmp /var/tmp
mkdir -pv /usr/{,local/}{bin,include,lib,sbin,src}
mkdir -pv /usr/{,local/}share/{doc,info,locale,man}
mkdir -v  /usr/{,local/}share/{misc,terminfo,zoneinfo}
mkdir -pv /usr/{,local/}share/man/man{1..8}
for dir in /usr /usr/local; do
  ln -sv share/{man,doc,info} $dir
done
mkdir -v /var/{lock,log,mail,run,spool}
mkdir -pv /var/{opt,cache,lib/{misc,locate},local} 
```

缺省的目录的权限模式为 755，但也并非所有的目录都如此。以上的命令有两处有所不一样：一个是 root 用户的目录，另外两个是临时文件目录。

第一个权限模式的不同之处是确保禁止任何人进入到 /root 目录中——同样的，这个模式也适用于让其它的普通用户可以工作在自己的目录中。第二个权限模式的不同之处是确保所有用户都可以写 /tmp 和 /var/tmp 目录，但不能从中删除其它用户的文件，这是由"sticky 位"，也就是"1777"中的"1"来设定的。

## 6.5.1\. FHS 兼容性说明

我们的目录树是按照 FHS(Filesystem Hierarchy Standard) 标准([*http://www.pathname.com/fhs/*](http://www.pathname.com/fhs/))。 除了上面创建的目录外，该标准还规定了必须有 /usr/local/games 和 /usr/share/games 两个目录，但是作为一个基本系统，我们并不需要这些。如果你要完全的遵守 FHS 标准的话，就自己建立这两个目录。至于 /usr/local/share 目录下的子目录，FHS 标准规定得并不严格，所以我们就创建了(在我们看来)需要的子目录。

# 6.6\. 创建必需的文件与符号连接

一些程序使用固化的路径(hard-wired paths)指向一些目前还不存在的程序上。为了兼容这些程序，可以创建一些符号链接，然后在软件安装之后用实际文件进行替代。

```
ln -sv /tools/bin/{bash,cat,grep,pwd,stty} /bin
ln -sv /tools/bin/perl /usr/bin
ln -sv /tools/lib/libgcc_s.so{,.1} /usr/lib
ln -sv bash /bin/sh 
```

一个常规的 Linux 系统在`/etc/mtab`中有一个已挂载文件系统的列表。 正常情况下，这个文件在我们挂载一个新的文件系统的时候会被创建。因为我们在 chroot 环境下不会再挂载任何文件系统 ，所以我们需要为那些用到`/etc/mtab`的程序创建一个空文件：

```
touch /etc/mtab 
```

为了让 root 用户可以登录而且用户名"root"可以被识别，在这里需要创建相应的 /etc/passwd 和 /etc/group 文件。

使用下面的命令创建 /etc/passwd 文件：

```
cat > /etc/passwd << "EOF"
root:x:0:0:root:/root:/bin/bash
EOF 
```

root 的真正密码将在后面设置("x"在这里只是一个占位符)。

下面的命令创建 /etc/group 文件：

```
cat > /etc/group << "EOF"
root:x:0:
bin:x:1:
sys:x:2:
kmem:x:3:
tty:x:4:
tape:x:5:
daemon:x:6:
floppy:x:7:
disk:x:8:
lp:x:9:
dialout:x:10:
audio:x:11:
video:x:12:
utmp:x:13:
usb:x:14:
cdrom:x:15:
EOF 
```

这里创建的用户组并不是某个标准所要求的部分，只是因为在随后 Udev 配置将要用到而以。Linux 标准基础(LSB, Linux Standard Base, [*http://www.linuxbase.org*](http://www.linuxbase.org)) 只是推荐"root"组的 GID 为 0，另一个组"bin"的 GID 为 1 。其它所有的组名和 GID 均由系统管理员自由设定，因为比较好的软件包一般都不依赖于 GID ，而只是使用组名。

因为完整的 Glibc 在 Chapter 5 中已经安装，而且 /etc/passwd 和 /etc/group 文件也已创建，用户名和组名现在可以开始使用了。现在启动新的 shell，驱除"I have no name!"提示符：

```
exec /tools/bin/bash --login +h 
```

注意这里使用了 +h 参数。这是告诉 bash 不能使用其内部哈希表查找路径。如果没有使用这个参数，则 bash 将会记住已经执行的二进制代码的路径。为了让新编译安装的二进制代码可以马上投入使用，在这一章中，我们使用 +h 关闭了此功能。

程序 login, agetty, init (还有其它一些程序) 使用一些日志文件来记录信息，比如谁在什么时候登录了系统等等。然而如果这些日志文件不存在，这些程序则无法写入。下面初始化这些日志文件，并设置适当的权限：

```
touch /var/run/utmp /var/log/{btmp,lastlog,wtmp}
chgrp -v utmp /var/run/utmp /var/log/lastlog
chmod -v 664 /var/run/utmp /var/log/lastlog 
```

/var/run/utmp 记录着现在登录的用户。/var/log/wtmp 记录所有的登录和退出。/var/log/lastlog 记录每个用户最后的登录信息。/var/log/btmp 记录错误的登录尝试。

# 6.7\. Linux-Libc-Headers-2.6.12.0

Linux-Libc-Headers 包含"纯净的"内核头文件。

**预计编译时间：** 少于 0.1 SBU**所需磁盘空间：** 27 MB

## 6.7.1\. 安装 Linux-Libc-Headers

多年来，通常的做法是直接从内核源代码中复制出"原始"内核头文件，放在 /usr/include 中使用。但是最近几年，内核开发人员强烈要求停止这种做法。因此诞生了 Linux-Libc-Headers 项目，其目的就是维护一个 API 版本稳定的内核头文件。

添加一个用户空间头文件和新内核对于 inotify 特性的系统调用支持:

```
patch -Np1 -i ../linux-libc-headers-2.6.12.0-inotify-3.patch 
```

安装内核头文件：

```
install -dv /usr/include/asm
cp -Rv include/asm-i386/* /usr/include/asm
cp -Rv include/linux /usr/include 
```

确保这些头文件的所有者是 root ：

```
chown -Rv root:root /usr/include/{asm,linux} 
```

确保用户可以读取这些头文件：

```
find /usr/include/{asm,linux} -type d -exec chmod -v 755 {} \;
find /usr/include/{asm,linux} -type f -exec chmod -v 644 {} \; 
```

## 6.7.2\. Linux-Libc-Headers 的内容

**安装的头文件：** /usr/include/{asm,linux}/*.h

### 简要描述

|  |  |
| --- | --- |
| `/usr/include/{asm,linux}/*.h` | 内核头文件 API |

# 6.8\. Man-pages-2.34

Man-pages 包含 1200 页的用户手册。

**预计编译时间：** 少于 0.1 SBU**所需磁盘空间：** 18.4 MB

## 6.8.1\. 安装 Man-pages

使用下述命令安装 Man-pages ：

```
make install 
```

## 6.8.2\. Man-pages 的内容

**安装的文件：** 一些用户手册文件

### 简要描述

|  |  |
| --- | --- |
| `man pages` | 描述了 C 和 C++ 的函数、重要的设备文件、以及一些重要的配置文件。 |

# 6.9\. Glibc-2.3.6

Glibc 包含了主要的 C 库。这个库提供了基本例程，用于分配内存、搜索目录、打开关闭文件、读写文件、字串处理、模式匹配、数学计算等等。

**预计编译时间：** 13.5 SBU (含测试套件)**所需磁盘空间：** 510 MB (含测试套件)

## 6.9.1\. 安装 Glibc

### 注意

一些 LFS 基本系统之外的软件包建议安装 GNU libiconv 以使得数据够能在不同编码之间进行转换。GNU libiconv 项目的主页([*http://www.gnu.org/software/libiconv/*](http://www.gnu.org/software/libiconv/)) 说："这个库为那些没有 iconv() 的系统或者虽然有 iconv() 却不能与 Unicode 相互转换的系统提供了一个能够与 Unicode 相互转换的实现 "。Glibc 库中有一个 iconv() ，并且能够与 Unicode 相互转换，因此，LFS 系统不需要 GNU libiconv 。

Glibc 的编译系统是高度自给自足的，即使当前编译器 specs 文件和连接器还指向 /tools 目录，也能正确安装。我们在安装 Glibc 前不能调整 specs 文件和连接器，否则 Glibc 的 autoconf 测试会失败，从而妨碍我们创建一个干净系统的目标。

glibc-libidn 这个包加上了对国际化域名(IDN)的支持到 Glibc 中。许多程序支持 IDN 需要全的`libidn` 库，而不是这个附加库。(参见 [*http://www.linuxfromscratch.org/blfs/view/svn/general/libidn.html*](http://www.linuxfromscratch.org/blfs/view/svn/general/libidn.html)).

解压缩包到 Glibc 的源码目录：

```
tar -xf ../glibc-libidn-2.3.6.tar.bz2 
```

应用下面这个 patch 来修正软件包在`sys/kd.h`之后包含`linux/types.h`导致编译错误：

```
patch -Np1 -i ../glibc-2.3.6-linux_types-1.patch 
```

添加一个头文件来定义为新内核对于 inotify 特性的系统调用函数：

```
patch -Np1 -i ../glibc-2.3.6-inotify-1.patch 
```

在 vi_VN.TCVN locale 中, `bash` 一启动就进入一个无限循环之中。不知道这是一个 `bash` 的 bug，还是一个 Glibc 的问题。为了避免这个问题，抑制这个 locale 的安装：

```
sed -i '/vi_VN.TCVN/d' localedata/SUPPORTED 
```

当运行 **make install**，一个叫`test-installation.pl`的脚本会在我们新安装的 Glibc 上做一个小的完整性测试。然而，由于我们的 toolchain 仍然指向`/tools`目录，完整性测试会导致使用错误的 Glibc。我们可以强制脚本测试我们刚安装的脚本：

```
sed -i \
's|libs -o|libs -L/usr/lib -Wl,-dynamic-linker=/lib/ld-linux.so.2 -o|' \
        scripts/test-installation.pl 
```

Glibc 文档推荐在源码目录之外的一个专门的编译目录下进行编译：

```
mkdir -v ../glibc-build
cd ../glibc-build 
```

为编译 Glibc 做准备：

```
../glibc-2.3.6/configure --prefix=/usr \
    --disable-profile --enable-add-ons \
    --enable-kernel=2.6.0 --libexecdir=/usr/lib/glibc 
```

**新配置选项的含义：**

*`--libexecdir=/usr/lib/glibc`*

把`pt_chown`程序的位置从默认的 `/usr/libexec` 改为 `/usr/lib/glibc` 。

编译软件包：

```
make 
```

### 重要

本节的 Glibc 测试很重要。在任何情况下都不要省略这一步。

对结果进行测试：

```
make -k check 2>&1 | tee glibc-check-log
grep Error glibc-check-log 
```

在*posix/annexc* 中，你可能会看到一个预料的错误（可以忽略）。另外，Glibc 测试单元，多少依赖于宿主系统。下面是一些常见的错误：

*   *nptl/tst-clock2* 和*tst-attr3* 测试有时会出错。 原因现在还不是很明白，可能是系统负载过重导致的。

*   *math* 测试在一些使用较老的 Intel 或 AMD 的系统上会失败，某些优化设置也会导致该测试失败。

*   *atime* 会在使用*`noatime`* 选项挂载 LFS 分区时失败（参见节 2.4, "挂载新分区"），在编译 LFS 过程中不要使用*`noatime`* 选项。

*   在一些很老很慢的机器上，一些测试会由于超时而失败。

在安装 Glibc 的过程中，它会警告缺少 `/etc/ld.so.conf` 文件。其实这没什么关系，不过下面的命令能修正它：

```
touch /etc/ld.so.conf 
```

安装软件包：

```
make install 
```

安装 inotify 头文件到系统头文件的地方：

```
cp -v ../glibc-2.3.6/sysdeps/unix/sysv/linux/inotify.h \
    /usr/include/sys 
```

locales 能使系统以一种上面命令没有安装的语言处理。要注意 locales 是必须的，如果他们中的一些丢失了，后面的测试单元会跳过重要测试。

单个的 locale 可以通过使用`localedef` 程序来安装。例如，下面的第一个 `localedef` 命令将`/usr/share/i18n/locales/de_DE`跟`/usr/share/i18n/charmaps/ISO-8859-1.gz`结合，并添加到 `/usr/lib/locale/locale-archive` 文件中。下面的说明将会安装一个所需 locale 的最小集合：

```
mkdir -pv /usr/lib/locale
localedef -i de_DE -f ISO-8859-1 de_DE
localedef -i de_DE@euro -f ISO-8859-15 de_DE@euro
localedef -i en_HK -f ISO-8859-1 en_HK
localedef -i en_PH -f ISO-8859-1 en_PH
localedef -i en_US -f ISO-8859-1 en_US
localedef -i en_US -f UTF-8 en_US.UTF-8
localedef -i es_MX -f ISO-8859-1 es_MX
localedef -i fa_IR -f UTF-8 fa_IR
localedef -i fr_FR -f ISO-8859-1 fr_FR
localedef -i fr_FR@euro -f ISO-8859-15 fr_FR@euro
localedef -i fr_FR -f UTF-8 fr_FR.UTF-8
localedef -i it_IT -f ISO-8859-1 it_IT
localedef -i ja_JP -f EUC-JP ja_JP 
```

另外，你可以安装你的国家、语言和字符集所对应的 locale。

当然，可以一次安装所有列在`glibc-2.3.6/localedata/SUPPORTED` 中的 locales，利用下面的命令：

```
make localedata/install-locales 
```

接着使用`localedef`命令来创建和安装 locales 没有列在`glibc-2.3.6/localedata/SUPPORTED`中的（这种情况不太可能），如果你需要他们的话。

## 6.9.2\. 配置 Glibc

我们需要建立 /etc/nsswitch.conf 文件。因为在这个文件丢失或不正确的情况下，Glibc 会使用默认配置，而 Glibc 的默认配置无法很好地在网络环境下工作。并且我们也需要设置自己的时区。

使用如下命令建立一个新的 /etc/nsswitch.conf 文件：

```
cat > /etc/nsswitch.conf << "EOF"
# Begin /etc/nsswitch.conf

passwd: files
group: files
shadow: files

hosts: files dns
networks: files

protocols: files
services: files
ethers: files
rpc: files

# End /etc/nsswitch.conf
EOF 
```

要想确定本地时区，可以使用下面的脚本：

```
tzselect 
```

按照顺序回答脚本运行过程中提出的几个问题后，脚本就会给出所需时区文件的位置(比如 *America/Edmonton*)。还有其他的一些时区列在`/usr/share/zoneinfo`中，比如*Canada/Eastern* 或 *EST5EDT*，这些虽然没有被脚本识别，但是都可以使用。

再用下列命令来创建 /etc/localtime 文件：

```
cp -v --remove-destination /usr/share/zoneinfo/<xxx> \
    /etc/localtime 
```

将 *`<xxx>`* 替换成选择的时区的名称(比如，Canada/Eastern)。

**cp 命令选项的含义：**

*`--remove-destination`*

强制删除已存在的符号链接。我们采用拷贝文件而不是创建符号链接的原因是：有可能 /usr 在单独的分区上，如果启动进入单用户模式，就会出问题。

## 6.9.3\. 配置动态链接库加载程序

默认情况下，动态链接库加载程序(/lib/ld-linux.so.2)搜索 /lib 和 /usr/lib 目录来寻找程序需要使用的动态连接库。但是，如果某些库在这两个目录之外，你就需要把它们的路径加到 /etc/ld.so.conf 文件里，以便动态链接库加载程序能够找到它们。 /usr/local/lib 和 /opt/lib 是两个经常包含动态连接库但又不在默认目录中的目录，我们要把它们添加到动态链接库加载程序的搜索路径中。

使用如下命令创建新的 /etc/ld.so.conf 文件：

```
cat > /etc/ld.so.conf << "EOF"
# Begin /etc/ld.so.conf

/usr/local/lib
/opt/lib

# End /etc/ld.so.conf
EOF 
```

## 6.9.4\. Glibc 的内容

**安装的程序：** catchsegv, gencat, getconf, getent, iconv, iconvconfig, ldconfig, ldd, lddlibc4, locale, localedef, mtrace, nscd, nscd_nischeck, pcprofiledump, pt_chown, rpcgen, rpcinfo, sln, sprof, tzselect, xtrace, zdump, zic**安装的库：** ld.so, libBrokenLocale.{a,so}, libSegFault.so, libanl.{a,so}, libbsd-compat.a, libc.{a,so}, libcidn.so, libcrypt.{a,so}, libdl.{a,so}, libg.a, libieee.a, libm.{a,so}, libmcheck.a, libmemusage.so, libnsl.a, libnss_compat.so, libnss_dns.so, libnss_files.so, libnss_hesiod.so, libnss_nis.so, libnss_nisplus.so, libpcprofile.so, libpthread.{a,so}, libresolv.{a,so}, librpcsvc.a, librt.{a,so}, libthread_db.so, libutil.{a,so}

### 简要描述

|  |  |
| --- | --- |
| `catchsegv` | 当程序发生段故障的时候，用来建立一个堆栈跟踪 |
| `gencat` | 建立消息列表 |
| `getconf` | 针对文件系统的指定变量显示其系统设置值 |
| `getent` | 从系统管理数据库获取一个条目 |
| `iconv` | 字符集转换 |
| `iconvconfig` | 建立快速加载的 iconv 模块所使用的配置文件 |
| `ldconfig` | 配置动态链接库的实时绑定 |
| `ldd` | 列出每个程序或者命令需要的共享库 |
| `lddlibc4` | 帮助 ldd 操作目标文件 |
| `locale` | 打印当前 locale 的详细信息 |
| `localedef` | 编译 locale 标准 |
| `mtrace` | 读取并解释一个内存跟踪文件然后以人类可读的格式显示一个摘要 |
| `nscd` | 为最常用的名称服务请求提供缓存的守护进程 |
| `nscd_nischeck` | 检查在进行 NIS+ 查找时是否需要安全模式 |
| `pcprofiledump` | 转储 PC profiling 产生的信息 |
| `pt_chown` | 一个辅助程序，帮助 grantpt 设置子虚拟终端的属主、用户组、读写权限 |
| `rpcgen` | 产生实现远程过程调用(RPC)协议的 C 代码 |
| `rpcinfo` | 对 RPC 服务器产生一个 RPC 呼叫 |
| `sln` | ln 程序使用静态连接编译的版本，在 ln 不起作用的时候，sln 仍然可以建立符号链接 |
| `sprof` | 读取并显示共享目标的特征描述数据 |
| `tzselect` | 对用户提出关于当前位置的问题并输出时区信息到标准输出 |
| `xtrace` | 通过打印当前执行的函数跟踪程序执行情况 |
| `zdump` | 显示时区 |
| `zic` | 时区编译器 |
| `ld.so` | 帮助动态链接库执行的辅助程序 |
| `libBrokenLocale` | 帮助应用程序(如 Mozilla)处理破损的 locale |
| `libSegFault` | 段故障信号处理器 |
| `libanl` | 异步名称查询库 |
| `libbsd-compat` | 为了在 linux 下执行一些 BSD 程序，libbsd-compat 提供了必要的可移植性 |
| `libc` | 主 C 库，集成了最常用函数 |
| `libcidn` | 被 Glibc 使用，在`getaddrinfo()`函数中来处理国际域名 |
| `libcrypt` | 用于的加密库 |
| `libdl` | 动态连接接口库 |
| `libg` | g++ 运行时库 |
| `libieee` | IEEE 浮点运算库 |
| `libm` | 数学函数库 |
| `libmcheck` | 包括了启动(boot)时需要的代码 |
| `libmemusage` | 帮助 memusage 搜集程序运行时的内存占用信息 |
| `libnsl` | 提供网络服务的库 |
| `libnss` | 名称服务切换库，包含了解释主机名、用户名、组名、别名、服务、协议等等的函数 |
| `libpcprofile` | 包含用于跟踪某些特定源代码的 CPU 使用时间的 profiling 函数 |
| `libpthread` | POSIX 线程库 |
| `libresolv` | 创建、发送、解释到互联网域名服务器的数据包 |
| `librpcsvc` | 提供 RPC 的其他杂项服务 |
| `librt` | 提供了大部分的 POSIX.1b 运行时扩展接口 |
| `libthread_db` | 对多线程程序的调试很有用 |
| `libutil` | 包含了在很多不同的 Unix 程序中使用的"标准"函数 |

# 6.10\. 再次调整工具链

现在，最终的 C 库已经安装好了，我们需要再次调整工具链，让本章随后编译的那些工具都连接到这个库上。基本上，就是把 Chapter 5 中"调整工具链"那里做的调整给取消掉。在 Chapter 5 中，工具链使用的库是从宿主系统的 `/{,usr/}lib` 转向新安装的 `/tools/lib` 目录。同样的，现在工具链使用的库将从临时的 `/tools/lib` 转向 LFS 系统最终的 `/{,usr/}lib` 目录。

首先，备份 `/tools` 下的链接，用我们在第五章中编译的链接器来替换，再创建一个链接到在 `/tools/$(gcc -dumpmachine)/bin` 中的复本。

```
mv -v /tools/bin/{ld,ld-old}
mv -v /tools/$(gcc -dumpmachine)/bin/{ld,ld-old}
mv -v /tools/bin/{ld-new,ld}
ln -sv /tools/bin/ld /tools/$(gcc -dumpmachine)/bin/ld 
```

接下来，修正 GCC 的 specs 文件，使它指向新的动态链接器，这样 GCC 才能知道在哪能发现开始文件。应用一个 `perl` 命令：

```
gcc -dumpspecs | \
perl -p -e 's@/tools/lib/ld-linux.so.2@/lib/ld-linux.so.2@g;' \
    -e 's@\*startfile_prefix_spec:\n@$_/usr/lib/ @g;' > \
    `dirname $(gcc --print-libgcc-file-name)`/specs 
```

修改之后，用你的眼睛亲自检查一下 specs 文件，确保已经改正确了。

### 重要

如果你的系统平台上，动态连接器的名字不是 `ld-linux.so.2` ，你必须把上面命令里的"ld-linux.so.2"换成你的系统平台上动态连接器的名字。若有必要，请参见 Section 5.2 "工具链技术说明"。

现在有必要停下来，检查一下新工具链的基本功能(编译和连接)是否正常，我们进行一个简单的合理性检查：

```
echo 'main(){}' > dummy.c
cc dummy.c -Wl,--verbose &> dummy.log
readelf -l a.out | grep ': /lib' 
```

如果一切正常，应该不会出错，而且最后一个命令的结果应该是(某些特殊平台上动态连接器的名称可能与此处不同)：

```
[Requesting program interpreter: /lib/ld-linux.so.2] 
```

注意，`/lib` 应该是动态连接器的前缀。

现在确保我们设置使用正确的开始文件：

```
grep -o '/usr/lib.*/crt[1in].* .*' dummy.log 
```

如果一切正常，应该不会出错，而且最后一个命令的结果应该是：

```
/usr/lib/crt1.o succeeded
/usr/lib/crti.o succeeded
/usr/lib/crtn.o succeeded 
```

接下来要做的是验证新的链接器是否在正确的搜索路径内：

```
grep 'SEARCH.*/usr/lib' dummy.log |sed 's|; |\n|g' 
```

如果一切正常，应该不会出错，而且最后一个命令的结果应该是：

```
SEARCH_DIR("/tools/i686-pc-linux-gnu/lib")
SEARCH_DIR("/usr/lib")
SEARCH_DIR("/lib"); 
```

下面，确保我们是否正在使用正确的 libc：

```
grep "/lib/libc.so.6 " dummy.log 
```

如果一切正常，应该不会出错，而且最后一个命令的结果应该是：

```
attempt to open /lib/libc.so.6 succeeded 
```

最后，确保 GCC 正在使用正确的动态链接器：

```
grep found dummy.log 
```

如果一切正常，应该不会出错，而且最后一个命令的结果应该是(某些特殊平台上动态连接器的名称可能与此处不同)：

```
found ld-linux.so.2 at /lib/ld-linux.so.2 
```

如果输出与上面不同或者没有输出，那么就有大问题了。你需要检查一下前面的操作，看看问题出在哪里，并改正过来。在改正之前，不要继续后面的部份，因为没什么意义。大多数情况下，出错都是因为上面的 specs 文件没改对。当然，如果你的平台上动态连接器的名字不是 ld-linux.so.2 ，上面的结果也会不同。在继续之前要解决所有的问题。

在确定一切正常后，删除测试文件：

```
rm -v dummy.c a.out dummy.log 
```

# 6.11\. Binutils-2.16.1

Binutils 是一组开发工具，包括连接器、汇编器和其他用于目标文件和档案的工具。

**预计编译时间：** 1.5 SBU (含测试套件)**所需磁盘空间：** 172 MB (含测试套件)

## 6.11.1\. 安装 Binutils

现在我们测试一下在 chroot 环境中，你的伪终端(PTY)是否正常工作。运行下面的命令：

```
expect -c "spawn ls" 
```

如果看到这样的输出：

```
The system has no more ptys.
Ask your system administrator to create more. 
```

说明你的 chroot 环境还没有设置好 PTY，这时运行 Binutils 和 GCC 的测试套件就没有意义了，你必须先解决 PTY 设置。

Binutils 的文档推荐用一个新建的目录来编译它，而不是在源码目录中：

```
mkdir -v ../binutils-build
cd ../binutils-build 
```

为编译 Binutils 做准备：

```
../binutils-2.16.1/configure --prefix=/usr \
    --enable-shared 
```

编译软件包：

```
make tooldir=/usr 
```

**make 参数的含义：**

*`tooldir=/usr`*

通常情况下，tooldir(可执行文件的安装目录) 是 `$(exec_prefix)/$(target_alias)`。例如在 i686 机器上，将是 tt class="filename">/usr/i686-pc-linux-gnu 。因为我们只为自己的系统进行编译，就并不需要在 `/usr` 目录后面再存在特殊的后缀。`$(exec_prefix)/$(target_alias)` 只是在交叉编译时(比如在 Intel 机器上编译将要在 PowerPC 上执行的程序)才用到。

### 重要

本节的 Binutils 测试套件很重要。在任何情况下都不要省略这一步。

对结果进行测试：

```
make check 
```

安装软件包：

```
make tooldir=/usr install 
```

安装某些软件包需要的 `libiberty` 头文件：

```
cp -v ../binutils-2.16.1/include/libiberty.h /usr/include 
```

## 6.11.2\. Binutils 的内容

**安装的程序：** addr2line, ar, as, c++filt, gprof, ld, nm, objcopy, objdump, ranlib, readelf, size, strings, strip**安装的库：** libiberty.a, libbfd.{a,so}, libopcodes.{a,so}

### 简要描述

|  |  |
| --- | --- |
| `addr2line` | 把程序地址转换为文件名和行号。在命令行中给它一个地址和一个可执行文件名，它就会使用这个可执行文件的调试信息指出在给出的地址上是哪个文件以及行号。 |
| `ar` | 建立、修改、提取归档文件。归档文件是包含多个文件内容的一个大文件，其结构保证了可以恢复原始文件内容。 |
| `as` | 一个汇编器，用来汇编 `gcc` 的输出，产生的目标文件然后由接器 ld 连接。 |
| `c++filt` | 连接器使用它来过滤 C++ 和 Java 符号，防止重载函数冲突。 |
| `gprof` | 显示程序调用段的各种数据。 |
| `ld` | 连接器，它把一些目标和归档文件结合为一个文件，重定位数据，并链接符号引用。通常，建立一个新编译程序的最后一步就是调用 ld 。 |
| `nm` | 列出出现在目标文件中的符号 |
| `objcopy` | 把一种目标文件中的内容复制到另一种类型的目标文件中 |
| `objdump` | 显示所给目标文件的信息。使用选项来控制其显示的信息。它所显示的信息通常只有编写编译工具的人才感兴趣。 |
| `ranlib` | 产生归档文件索引，并将其保存到这个归档文件中。在索引中列出了归档文件各成员所定义的可重分配目标文件。 |
| `readelf` | 显示 ELF 格式可执行文件的信息 |
| `size` | 列出目标文件每一段的大小以及总体的大小。默认情况下，对于每个目标文件或者一个归档文件中的每个模块只产生一行输出。 |
| `strings` | 打印某个文件的可打印字符串，这些字符串最少 4 个字符长，也可以使用选项"-n"设置字符串的最小长度。默认情况下，它只打印目标文件初始化和可加载段中的可打印字符；对于其它类型的文件它打印整个文件的可打印字符，这个程序对于了解非文本文件的内容很有帮助。 |
| `strip` | 删除目标文件中的全部或者特定符号 |
| `libiberty` | 包含许多 GNU 程序都会用到的函数，这些程序有： `getopt`， `obstack`， `strerror`， `strtol`， 和 `strtoul` |
| `libbfd` | 二进制文件描述库 |
| `libopcodes` | 用来处理 opcodes（"可读文本格式的"）处理器操作指令)的库，在生成一些应用程序的时候也会用到它，比如 `objdump` 。 |

# 6.12\. GCC-4.0.3

GCC 软件包包含 GNU 编译器，其中有 C 和 C++ 编译器。

**预计编译时间：** 22 SBU (含测试套件)**所需磁盘空间：** 566 MB (含测试套件)

## 6.12.1\. 安装 GCC

使用一个 `sed` 命令来禁止 GCC 安装它自己的 `libiberty.a` 。我们将使用 Binutils 附带的 `libiberty.a` 来代替：

```
sed -i 's/install_to_$(INSTALL_DEST) //' libiberty/Makefile.in 
```

在节 5.4, "GCC-4.0.3 - 第一遍" 中应用的 bootstrap 编译中，编译器会有 `-fomit-frame-pointer` 的标志。非 bootstrap 编译默认是忽略这个标志的，可以应用下面的`sed` 命令来确保编译的可靠性。

```
sed -i 's/^XCFLAGS =$/& -fomit-frame-pointer/' gcc/Makefile.in 
```

`fixincludes` 脚本偶尔会因为修改系统的头文件而出错。因为 GCC-4.0.3 和 Glibc-2.3.6 是不需要修改的，运行下面的命令可以避免 `fixincludes` 脚本运行：

```
sed -i 's@\./fixinc\.sh@-c true@' gcc/Makefile.in 
```

GCC 中提供了一个 `gccbug` 脚本，会在编译时侦测 mktemp 是否存在，并且在测试中加强代码。这将会导致脚本使用一些不算很随机的名字来命名临时文件。因为我们后面会安装 mktemp ，这里我们就模仿它的存在：

```
sed -i 's/@have_mktemp_command@/yes/' gcc/gccbug.in 
```

GCC 的安装指南推荐用一个新建的目录来编译它，而不是在源码目录中：

```
mkdir -v ../gcc-build
cd ../gcc-build 
```

为编译 GCC 做准备：

```
../gcc-4.0.3/configure --prefix=/usr \
    --libexecdir=/usr/lib --enable-shared \
    --enable-threads=posix --enable-__cxa_atexit \
    --enable-clocale=gnu --enable-languages=c,c++ 
```

编译软件包：

```
make 
```

### 重要

本节的 GCC 测试套件很重要。在任何情况下都不要省略这一步。

运行测试套件，但遇到错误不停止(你还记得那些老是出错的测试吧)：

```
make -k check 
```

要查看测试单元的测试结果，可以运行：

```
../gcc-4.0.3/contrib/test_summary 
```

如果只想看概要，可以把输出通过管道传递给 **`grep -A7 Summ`** 。

结果可以跟 [*http://www.linuxfromscratch.org/lfs/build-logs/6.2/*](http://www.linuxfromscratch.org/lfs/build-logs/6.2/) 的进行比较。

一些预想不到的错误总是无法避免的。虽然 GCC 的开发者经常留意这些问题，但是有些还是没有得到解决。通常，`libmudflap` 测试尤其被认为是有问题的（这是一个 bug，参见[*http://gcc.gnu.org/bugzilla/show_bug.cgi?id=20003*](http://gcc.gnu.org/bugzilla/show_bug.cgi?id=20003)）。 除非测试结果给上面 URL 的有很多不同，一般是可以安全继续的。

安装软件包：

```
make install 
```

有的软件包希望 C PreProcessor(预处理器)安装在 `/lib` 目录下，为了满足它们的要求，我们创建如下符号链接：

```
ln -sv ../usr/bin/cpp /lib 
```

许多软件包使用 `cc` 作为 C 编译器的名字，为了满足它们的要求，创建如下符号链接：

```
ln -sv gcc /usr/bin/cc 
```

现在，我们的最终工具链已经形成了，我们需要做的就是确保编译、链接按照我们希望的完成。我们可以通过本章前面的简册方法来实现：

```
echo 'main(){}' > dummy.c
cc dummy.c -Wl,--verbose &> dummy.log
readelf -l a.out | grep ': /lib' 
```

如果所有的都工作正常，就不会有错误，并且命令的输出应该是（允许不同平台的动态链接器的名称不同）：

```
[Requesting program interpreter: /lib/ld-linux.so.2] 
```

现在确保我们使用正确的 startfiles：

```
grep -o '/usr/lib.*/crt[1in].* .*' dummy.log 
```

如果所有的都工作正常，就不会有错误，并且命令的输出应该是：

```
/usr/lib/gcc/i686-pc-linux-gnu/4.0.3/../../../crt1.o succeeded
/usr/lib/gcc/i686-pc-linux-gnu/4.0.3/../../../crti.o succeeded
/usr/lib/gcc/i686-pc-linux-gnu/4.0.3/../../../crtn.o succeeded 
```

接下来，确认新的链接器被应用到了正确的搜索路径中：

```
grep 'SEARCH.*/usr/lib' dummy.log |sed 's|; |\n|g' 
```

如果所有的都工作正常，就不会有错误，并且命令的输出应该是：

```
SEARCH_DIR("/usr/i686-pc-linux-gnu/lib")
SEARCH_DIR("/usr/local/lib")
SEARCH_DIR("/lib")
SEARCH_DIR("/usr/lib"); 
```

现在，确保我们正在使用正确的 libc ：

```
grep "/lib/libc.so.6 " dummy.log 
```

如果所有的都工作正常，就不会有错误，并且命令的输出应该是

```
attempt to open /lib/libc.so.6 succeeded 
```

最后，确保 GCC 正在使用正确的动态链接器：

```
grep found dummy.log 
```

如果所有的都工作正常，就不会有错误，并且命令的输出应该是（允许不同平台的动态链接器的名称不同）：

```
found ld-linux.so.2 at /lib/ld-linux.so.2 
```

如果输出不是像上面那样或者根本没有输出，那么就有大问题了。返回并检查前面的操作，找出问题，并改正过来。最有可能的原因是上面修正 specs 文件时出错。任何一个问题都需要在继续之前解决掉。

一旦工具都工作正常，清理测试文件：

```
rm -v dummy.c a.out dummy.log 
```

## 6.12.2\. GCC 的内容

**安装的程序：** c++, cc(→gcc), cpp, g++, gcc, gccbug, gcov**安装的库：** libgcc.a, libgcc_eh.a, libgcc_s.so, libstdc++.{a,so}, libsupc++.a

### 简要描述

|  |  |
| --- | --- |
| `cc` | C 编译器 |
| `cpp` | C 预处理器。编译器用它来将 #include 和 #define 这类声明在源文件中展开。 |
| `c++` | C++ 编译器 |
| `g++` | C++ 编译器 |
| `gcc` | C 编译器 |
| `gccbug` | 一个 shell 脚本，帮助创建有价值的 bug 报告。 |
| `gcov` | 覆盖测试工具，用来分析在程序的哪里做优化的效果最好。 |
| `libgcc` | `gcc` 的运行时库 |
| `libstdc++` | 准 C++ 库，包含许多常用的函数。 |
| `libsupc++` | 为 C++ 语言提供支持的库函数。 |

# 6.13\. Berkeley DB-4.4.20

Berkeley DB 包含一些程序和工具，供其他的一些程序来在做数据库相关函数时调用。

**预计编译时间：** 1.2 SBU**所需磁盘空间：** 77 MB

### Other Installation Possibilities

如果你需要建立一个 RPC 服务器或者是附加语言绑定编译，在 BLFS 手册中有一些编译这个软件包的说明。附加语言的绑定编译还需要一些额外的软件包。参见 [*http://www.linuxfromscratch.org/blfs/view/svn/server/databases.html#db*](http://www.linuxfromscratch.org/blfs/view/svn/server/databases.html#db)的安装说明

另外，GDBM *可以* 被用来代替 Berkeley DB 来满足数据库需求。但是，因为在 LFS 构建过程中，Berkeley DB 被认为是一个核心部分，无法列出在 BLFS 手册中把它作为依赖的软件（太多了）。同样，很多时候我们测试的是安装了 Berkeley DB 的 LFS 系统，而不是 GDBM。如果你清楚的了解了使用 GDBM 的风险和好处，仍然想要采用它，可以参考 BLFS 手册中的说明 [*http://www.linuxfromscratch.org/blfs/view/svn/general/gdbm.html*](http://www.linuxfromscratch.org/blfs/view/svn/general/gdbm.html)

## 6.13.1\. 安装 Berkeley DB

修补软件包来防止一些潜在的陷井时间：

```
patch -Np1 -i ../db-4.4.20-fixes-1.patch 
```

为编译 Berkeley DB 做准备：

```
cd build_unix &&
../dist/configure --prefix=/usr --enable-compat185 --enable-cxx 
```

**配置选项的含义：**

*`--enable-compat185`*

这个选项指定编译 Berkeley DB 1.85 向上兼容性 API。

*`--enable-cxx`*

这个选项指定编译 C++ API 库。

编译软件包：

```
make 
```

现在测试软件包是没有意义的，因为这将会导致 TCL 捆绑编译。TCL 不能被准确的编译，因为 TCL 还是链接到 `/tools` 下的 Glibc，而不是 `/usr` 目录下的 Glibc。

安装软件包：

```
make docdir=/usr/share/doc/db-4.4.20 install 
```

**make 参数的含义：**

*`docdir=...`*

这条安装命令将 db 的文档安装到正确的位置。/p>

修改安装文件的属主：

```
chown -v root:root /usr/bin/db_* \
    /usr/lib/libdb* /usr/include/db* &&
chown -Rv root:root /usr/share/doc/db-4.4.20 
```

## 6.13.2\. Berkeley DB 的内容

**安装的程序：** db_archive, db_checkpoint, db_deadlock, db_dump, db_hotbackup, db_load, db_printlog, db_recover, db_stat, db_upgrade, db_verify**安装的库：** libdb.{so,ar}and libdb_cxx.r{o,ar}

### 简要描述

|  |  |
| --- | --- |
| `db_archive` | 打印出不再使用的日志文件路径名 |
| `db_checkpoint` | 监视和检查数据库日志的守护进程 |
| `db_deadlock` | 当死锁发生时，退出锁定要求 |
| `db_dump` | 把数据库文件转换成 `db_load` 能认出的文本文件 |
| `db_hotbackup` | 创建 "hot backup" 或者是 "hot failover" 的 Berkeley DB 数据库镜像。 |
| `db_load` | 从 db_dump 产生的文本文件中创建出数据库文件 |
| `db_printlog` | 把数据库日志文件转换成人能读懂的文本 |
| `db_recover` | 在发生错误后，把数据库恢复到一致的状态 |
| `db_stat` | 显示数据库环境统计 |
| `db_upgrade` | 把数据库文件转换成新版本的 Berkley DB 格式 |
| `db_verify` | 对数据库文件进行一致性检查 |
| `libdb.{so,a}` | 包含 db 处理相关函数的 C 库 |
| `libdb_cxx.{so,a}` | 包含 db 处理相关函数的 C++库 |

# 6.14\. Coreutils-5.96

Coreutils 软件包包括一套显示、设置基本系统属性的工具。

**预计编译时间：** 1.1 SBU**所需磁盘空间：** 58.3 MB

## 6.14.1\. 安装 Coreutils

通常 `uname` 程序总是有点毛病的，比如 _`-p`_unknown 的结果。下面的补丁对 Intel 平台的机器能修正这个问题：

```
patch -Np1 -i ../coreutils-5.96-uname-1.patch 
```

阻止 Coreutils 安装后面将由别的包安装的程序：

```
patch -Np1 -i ../coreutils-5.96-suppress_uptime_kill_su-1.patch 
```

POSIX 要求 Coreutils 的程序即使在多字节环境下也能够识别出字符的边界。下面的这个 patch 能够解决这个问题以及其他的一些国际化相关的问题：

```
patch -Np1 -i ../coreutils-5.96-i18n-1.patch 
```

为了测试应用的 patch 能够运行，修改文件的权限：

```
chmod +x tests/sort/sort-mb-tests 
```

### 注意

过去，在这个 patch 里面发现了很多 bug。当你向 Coreutils 的维护者发送错误报告的时候，首先确认不应用这个 patch 错误会不会出现。

现在已经发现在使用`who -Hu`时，转换的信息有时会导致缓冲区溢出。增大缓冲区大小：

```
sed -i 's/_LEN 6/_LEN 20/' src/who.c 
```

为编译 Coreutils 做准备：

```
./configure --prefix=/usr 
```

编译软件包：

```
make 
```

Coreutils 软件包的测试套件对系统进行了某些假设，比如要求有非 root 用户和组，但是我们目前的系统中尚不存在。如果你不想运行测试套件，就直接跳过下面将要进行的测试，直接从"安装软件包"那里继续。

下面的命令为我们做测试前的准备，创建两个 dummy(伪) 组和一个 dummy(伪) 用户：

```
echo "dummy1:x:1000:" >> /etc/group
echo "dummy2:x:1001:dummy" >> /etc/group
echo "dummy:x:1000:1000::/root:/bin/bash" >> /etc/passwd 
```

现在已经准备好可以运行测试套件了，首先运行那些需要以 `root` 运行的测试：

```
make NON_ROOT_USERNAME=dummy check-root 
```

然后以 `dummy` 用户运行剩余的测试：

```
src/su dummy -c "make RUN_EXPENSIVE_TESTS=yes check" 
```

测试结束后，删除 dummy 组和用户：

```
sed -i '/dummy/d' /etc/passwd /etc/group 
```

安装软件包：

```
make install 
```

把一些程序移动到合适的位置以符合 FHS 标准：

```
mv -v /usr/bin/{cat,chgrp,chmod,chown,cp,date,dd,df,echo} /bin
mv -v /usr/bin/{false,hostname,ln,ls,mkdir,mknod,mv,pwd,rm} /bin
mv -v /usr/bin/{rmdir,stty,sync,true,uname} /bin
mv -v /usr/bin/chroot /usr/sbin 
```

一些 LFS-Bootscripts 包中的脚本依赖于 `head`， `sleep`，和 `nice` 。由于 /usr 目录有可能在系统启动过程的早期不可用(比如尚未挂载)，所以这些二进制程序需要放置在根分区上：

```
mv -v /usr/bin/{head,sleep,nice} /bin 
```

## 6.14.2\. Coreutils 的内容

**安装的程序：** basename, cat, chgrp, chmod, chown, chroot, cksum, comm, cp, csplit, cut, date, dd, df, dir, dircolors, dirname, du, echo, env, expand, expr, factor, false, fmt, fold, groups, head, hostid, hostname, id, install, join, link, ln, logname, ls, md5sum, mkdir, mkfifo, mknod, mv, nice, nl, nohup, od, paste, pathchk, pinky, pr, printenv, printf, ptx, pwd, readlink, rm, rmdir, seq, sha1sum, shred, sleep, sort, split, stat, stty, sum, sync, tac, tail, tee, test, touch, tr, true, tsort, tty, uname, unexpand, uniq, unlink, users, vdir, wc, who, whoami, yes

### 简要描述

|  |  |
| --- | --- |
| `basename` | 去掉文件名中的目录和后缀 |
| `cat` | 把文本文件的内容发送到标准输出 |
| `chgrp` | 改变文件和目录属组，属组可以使用组名或者组识别号表示 |
| `chmod` | 改变文件和目录的权限，权限可以使用符号或者八进制两种表达方式 |
| `chown` | 改变文件和目录的所有权(包括用户和/或组) |
| `chroot` | 使用特定的目录作为执行某个命令或者交互 shell 的根目录(/)。在多数系统中，只有 root 用户能运行这个命令 |
| `cksum` | 输出指定的每个文件的 CRC(循环冗余校验)校验和与字节数 |
| `comm` | 一行一行对两个已经排序的文件进行比较，在第三列中显示同一行是否相同 |
| `cp` | 复制文件 |
| `csplit` | 把一个文件按照给定的模式或者行号分成几块 |
| `cut` | 从指定的文件中提取特定的列送到标准输出 |
| `date` | 以特定的格式显示当前时间，或者设置系统日期 |
| `dd` | 以可选块长度复制文件，默认情况下从标准输入设备输出到标准输出设备。复制过程中，还可以对文件进行一些转换 |
| `df` | 显示参数中的文件所在分区磁盘空间的使用情况，如果没有给出文件参数就显示所有已经安装的文件系统的可用空间数量。 |
| `dir` | 列出给定目录的内容 (同 `ls` 命令) |
| `dircolors` | 设置 LS_COLOR 环境变量(用来改变 `ls` 及相关工具默认颜色组合) |
| `dirname` | 显示从文件名去掉非目录后缀之后的内容 |
| `du` | 显示参数使用的磁盘空间的数量，对于参数为目录还会列出每个子目录磁盘空间占用情况。 |
| `echo` | 显示给定字符串或变量值 |
| `env` | 在一个被修改的环境中运行一个程序 |
| `expand` | 把 tab 转换为空格符 |
| `expr` | 执行表达式计算 |
| `factor` | 输出所有指定整数的质因数 |
| `false` | 返回一个不成功或者逻辑假的结果 |
| `fmt` | 重新格式化指定文件的段落 |
| `fold` | 断开指定文件(默认是标准输入)较长的行，在屏幕上显示 |
| `groups` | 显示一个用户所在的组 |
| `head` | 显示每个指定文件的前几行(默认是 10) |
| `hostid` | 以 16 进制方式，显示当前主机的数字标志符 |
| `hostname` | 显示或设置主机名 |
| `id` | 显示某个用户或者当前用户的真实和有效的 UID、GID 。 |
| `install` | 复制文件，设置它们的权限，如果可能还设置拥有它们的用户和组 |
| `join` | 合并两个文件的行 |
| `link` | 创建从指定文件到指定名称的硬链接 |
| `ln` | 创建文件之间的硬/软(符号)连接 |
| `logname` | 显示当前用户的登录名 |
| `ls` | 列出指定目录的所有内容。缺省是将文件和子目录按字母顺序排列。 |
| `md5sum` | 显示或者校验 MD5 校验码。 |
| `mkdir` | 建立目录，使用给定的参数作为目录名。 |
| `mkfifo` | 以给定的参数作为名字建立 FIFO(又叫"命名管道")文件。 |
| `mknod` | 使用给出的文件名，建立一个设备节点，也就是：FIFO、字符特殊文件(special file)或者块特殊文件(special file)。 |
| `mv` | 根据所给参数的不同，把文件或者目录移动到另外的目录或者将其改名 |
| `nice` | 修改某个进程的调度优先级 |
| `nl` | 把每个指定文件的内容写到标准输出，在每行加上行号 |
| `nohup` | 使某个命令不被挂起，并将输出重定向到一个日志文件。 |
| `od` | 以数字方式显示指定文件的内容，默认为八进制。 |
| `paste` | 将字段连接在一起，在字段之间自动插入分割符，默认的分割符是 Tab 。 |
| `pathchk` | 检查文件名是否是有效的或者是可移植的 |
| `pinky` | 一个轻量级的 finger 客户端，用来得到某个用户的信息。 |
| `pr` | 将文件分成适当大小的页送到打印机 |
| `printenv` | 显示环境变量 |
| `printf` | 根据给定的参数格式化输出数据，与 C 语言中的该函数相似。 |
| `ptx` | 为指定的文件提供一个排序索引 |
| `pwd` | 显示当前工作目录 |
| `readlink` | 显示指定符号链接的值 |
| `rm` | 删除文件或者目录 |
| `rmdir` | 删除目录(目录必需为空) |
| `seq` | 以指定的步长输出一个数列 |
| `sha1sum` | 显示或校验 160 位的 SHA1 校验码 |
| `shred` | 安全删除一个文件，重写其占用的磁盘空间，使其无法恢复。 |
| `sleep` | 延迟一段时间 |
| `sort` | 对文件进行排序 |
| `split` | 把文件分成固定大小(字节或行数)的片断 |
| `stat` | 显示文件或者文件系统的状态 |
| `stty` | 改变和显示终端行的设置 |
| `sum` | 显示指定文件的校验和及块数 |
| `sync` | 刷新文件系统缓冲区，使磁盘和内存的数据同步。 |
| `tac` | 逆向显示指定的文件，最后一行在最前。 |
| `tail` | 显示每个指定文件的最后几行(默认是 10)。 |
| `tee` | 从标准输入读取数据，输出到标准输出和指定的文件。 |
| `test` | 检查文件类型，以及进行变量的比较。 |
| `touch` | 把参数指定的文件的访问和修改时间改为当前的时间。如果文件不存在，它就建立一个空文件。 |
| `tr` | 从标准输入读入正文，对字符进行转换、压缩或者删除，然后写到标准输出 |
| `true` | 返回一个成功或者逻辑真的结果 |
| `tsort` | 对给定的文件进行拓扑排序 |
| `tty` | 显示标准输出设备连接终端的文件名 |
| `uname` | 打印系统信息 |
| `unexpand` | 把空格符转换成 tab |
| `uniq` | 抛弃指定文件或者标准输入中内容重复的行 |
| `unlink` | 删除指定文件 |
| `users` | 显示在当前主机登录的用户名 |
| `vdir` | 同 **ls -l** |
| `wc` | 统计文件中包含的字节数、单词数、行数 |
| `who` | 显示有哪些用户登录 |
| `whoami` | 打印当前用户的有效用户标志符 |
| `yes` | 重复输出"y"字符，直到被杀死。 |

# 6.15\. Iana-Etc-2.10

Iana-Etc 软件包提供了网络服务和协议的数据。

**预计编译时间：** 少于 0.1 SBU**所需磁盘空间：** 2.1 MB

## 6.15.1\. 安装 Iana-Etc

下面的命令将 IANA 的原始数据转换为 `/etc/protocols` 和 `/etc/services` 数据文件能够识别的格式：

```
make 
```

这个软件包没有附带测试程序。

安装软件包：

```
make install 
```

## 6.15.2\. Iana-Etc 的内容

**安装的文件：** /etc/protocols, /etc/services

### 简要描述

|  |  |
| --- | --- |
| `/etc/protocols` | 描述 TCP/IP 子系统可用的各种 Internet 协议。 |
| `/etc/services` | 将 internet 服务映射到一个包含端口号和所使用协议的文本名称。 |

# 6.16\. M4-1.4.4

M4 软件包包含一个宏处理器。

**预计编译时间：** 少于 0.1 SBU**所需磁盘空间：** 3 MB

## 6.16.1\. 安装 M4

为编译 M4 做准备：

```
./configure --prefix=/usr 
```

编译软件包：

```
make 
```

要测试结果，请运行：**`make check`** 。

安装软件包：

```
make install 
```

## 6.16.2\. M4 的内容

**安装的程序：** m4

### 简要描述

|  |  |
| --- | --- |
| `m4` | M4 能够将宏展开并将输入拷贝到输出。宏可以是内嵌的也可以是用户定义的，并且可以接受很多参数。除了展开宏，`m4` 还有其它内置的功能，比如包含引用文件、运行 Unix 命令、进行整数运算、文本操作、循环等等。`m4` 可以被用作一个编译器的前端或作为自身的一个宏处理程序。 |

# 6.17\. Bison-2.2

Bison 软件包包括一个语法分析程序生成器。

**预计编译时间：** 0.6 SBU**所需磁盘空间：** 11.9 MB

## 6.17.1\. 安装 Bison

为编译 Bison 做准备：

```
./configure --prefix=/usr 
```

如果 `bison` 程序不在 $PATH 中的话，编译时将会出现缺乏国际化支持的错误信息。下面处理可以解决这个问题：

```
echo '#define YYENABLE_NLS 1' >> config.h 
```

编译软件包：

```
make 
```

要测试结果，请运行：**`make check`** 。

安装软件包：

```
make install 
```

## 6.17.2\. Bison 的内容

**安装的程序：** bison, yacc**安装的库：** liby.a

### 简要描述

|  |  |
| --- | --- |
| `bison` | 根据一系列规则来生成一个可以分析文本文件的结构的程序的程序，Bison 是一个替代 Yacc (Yet Another Compiler Compiler) 的语法分析程序生成器。 |
| `yacc` | 一个 `bison` 的包装，意思是程序仍然调用 `yacc` 而不是 `bison` ，它用 *`-y`* 选项调用 `bison` 。 |
| `liby.a` | acc 库包含与 Yacc 兼容的 `yyerror` 和 `main` 函数，这个库通常不是很有用，但是 POSIX 需要它。 |

# 6.18\. Ncurses-5.5

Ncurses 程序包提供字符终端处理库，包括面板和菜单。

**预计编译时间：** 0.7 SBU**所需磁盘空间：** 31 MB

## 6.18.1\. 安装 Ncurses

Ncurses-5.5 有一个内存泄漏和一些显示的 bug 被发现，并修正了。应用这些修正：

```
patch -Np1 -i ../ncurses-5.5-fixes-1.patch 
```

为编译 Ncurses 做准备：

```
./configure --prefix=/usr --with-shared --without-debug --enable-widec 
```

**配置选项的含义：**

*`--enable-widec`*

这个选项导致宽字符库（例如，`libncursesw.so.5.5`）将会替换正常的（例如，`libncurses.so.5.5`）。这些宽字符库可以在 多字节和传统的 8 位 locale 下使用，然而正常的库一般只能在 8 位 的 locale 环境下工作。宽字符和正常的库是源码兼容的，但不是二进制兼容的。

编译软件包：

```
make 
```

这个软件包没有附带测试程序。

安装软件包：

```
make install 
```

赋予 ncurses 库文件可执行权限：

```
chmod -v 755 /usr/lib/*.5.5 
```

修正一个不应该有可执行权限的库文件：

```
chmod -v 644 /usr/lib/libncurses++w.a 
```

把库文件移到更合理的 `/lib` 目录里：

```
mv -v /usr/lib/libncursesw.so.5* /lib 
```

由于库文件移动了，所以有的符号链接就指向了不存在的文件。需要重新创建这些符号链接：

```
ln -sfv ../../lib/libncursesw.so.5 /usr/lib/libncursesw.so 
```

许多的程序依然希望链接器能够发现非宽字符的 Ncurses 库。通过符号链接和链接器脚本来欺骗它使其链接宽字符的库：

```
for lib in curses ncurses form panel menu ; do \
    rm -vf /usr/lib/lib${lib}.so ; \
    echo "INPUT(-l${lib}w)" >/usr/lib/lib${lib}.so ; \
    ln -sfv lib${lib}w.a /usr/lib/lib${lib}.a ; \
done &&
ln -sfv libncurses++w.a /usr/lib/libncurses++.a 
```

最后，确保一些在编译的时候寻找 `-lcurses` 的老程序仍然可以编译：

```
echo "INPUT(-lncursesw)" >/usr/lib/libcursesw.so &&
ln -sfv libncurses.so /usr/lib/libcurses.so &&
ln -sfv libncursesw.a /usr/lib/libcursesw.a &&
ln -sfv libncurses.a /usr/lib/libcurses.a 
```

### 注意

上面的说明并没有创建非宽字符的 Ncurses 库，因为没有软件包编译后在运行时需要链接到它们的。如果你因为一些只有二进制文件的程序，必须安装这样的库的话，按照下面的命令进行编译：

```
make distclean &&
./configure --prefix=/usr --with-shared --without-normal \
  --without-debug --without-cxx-binding &&
make sources libs &&
cp -av lib/lib*.so.5* /usr/lib 
```

## 6.18.2\. Ncurses 的内容

**安装的程序：** captoinfo(→tic), clear, infocmp, infotocap(→tic), reset(→tset), tack, tic, toe, tput, tset**安装的库：** libcursesw.{a,so} (symlink and linker script to libncursesw.{a,so}), libformw.{a,so}, libmenuw.{a,so}, libncurses++w.a, libncursesw.{a,so}, libpanelw.{a,so} and their non-wide-character counterparts without "w" in the library names.

### 简要描述

|  |  |
| --- | --- |
| `captoinfo` | 将 termcap 描述转化成 terminfo 描述 |
| `clear` | 如果可能，就进行清屏操作 |
| `infocmp` | 比较或显示 terminfo 描述 |
| `infotocap` | 将 terminfo 描述转化成 termcat 描述 |
| `reset` | 重新初始化终端到默认值 |
| `tack` | terminfo 动作检测器。主要用来测试 terminfo 数据库中某一条目的正确性。 |
| `tic` | Tic 是 terminfo 项说明的编译器。这个程序通过 ncurses 库将源代码格式的 terminfo 文件转换成编译后格式(二进制)的文件。 Terminfo 文件包含终端能力的信息。 |
| `toe` | 列出所有可用的终端类型，分别列出名称和描述。 |
| `tput` | 利用 terminfo 数据库使与终端相关的能力和信息值对 shell 可用，初始化和重新设置终端，或返回所要求终端为类型的长名。 |
| `tset` | 可以用来初始化终端 |
| `libcurses` | 链接到 `libncurses` |
| `libncurses` | 用来在显示器上显示文本的库。一个例子就是在内核的 `make menuconfig` 进程中。 |
| `libform` | 在 ncurses 中使用表格 |
| `libmenu` | 在 ncurses 中使用菜单 |
| `libpanel` | 在 ncurses 中使用面板 |

# 6.19\. Procps-3.2.6

Procps 包含有用于监视系统进程的程序。

**预计编译时间：** 0.1 SBU**所需磁盘空间：** 2.3 MB

## 6.19.1\. 安装 Procps

编译软件包：

```
make 
```

这个软件包没有附带测试程序。

安装软件包：

```
make install 
```

## 6.19.2\. Procps 的内容

**安装的程序：** free, kill, pgrep, pkill, pmap, ps, skill, slabtop, snice, sysctl, tload, top, uptime, vmstat, w, watch**安装的库：** libproc.so

### 简要描述

|  |  |
| --- | --- |
| `free` | 报告系统中的空闲和已用内存数量(同时包括物理内存和交换内存) |
| `kill` | 向进程发送消息 |
| `pgrep` | 基于进程名及其属性来查找进程 |
| `pkill` | 依据进程名及其属性向进程发送消息 |
| `pmap` | 报告所告所给定进程的内存映射 |
| `ps` | 显示当前正运行的进程列表 |
| `skill` | 向符合所给标准的进程发送消息 |
| `slabtop` | 实时显示内核 slap 缓冲的详细信息 |
| `snice` | 改变符合所给标准的进程的调度优先权 |
| `sysctl` | 在运行期间修改内核参数 |
| `tload` | 打印当前系统平均负荷曲线图 |
| `top` | 显示使用 CPU 最密集的进程列表，它提供了对实时的处理器行为的实时察看。 |
| `uptime` | 报告系统运行了多久，有多少用户登录，以及系统平均负荷。 |
| `vmstat` | 报告虚拟内存统计，并给出有关处进程、内存、块输入/输出(IO)、陷阱、CPU 使用率。 |
| `w` | 显示哪个用户登录，在哪里以及何时登录的。 |
| `watch` | 重复运行所给的命令，以显示输出的第一次满屏，这将允许用户察看随时间变化的输出。 |
| `libproc` | 含有该软件包中大多数程序所需使用的函数 |

# 6.20\. Sed-4.1.5

Sed 是一个流编辑器。

**预计编译时间：** 0.1 SBU**所需磁盘空间：** 6.4 MB

## 6.20.1\. 安装 Sed

为编译 Sed 做准备：

```
./configure --prefix=/usr --bindir=/bin --enable-html 
```

**新配置选项的含义：**

*`--enable-html`*

This builds the HTML documentation.

编译软件包：

```
make 
```

要测试结果，请运行：**`make check`** 。

安装软件包：

```
make install 
```

## 6.20.2\. Sed 的内容

**安装的程序：** sed

### 简要描述

|  |  |
| --- | --- |
| `sed` | 流式(单向)过滤和改变文本文件 |

# 6.21\. Libtool-1.5.22

GNU libtool 是一个通用库支持脚本，将使用动态库的复杂性隐藏在统一的、可移植的接口中。

**预计编译时间：** 0.1 SBU**所需磁盘空间：** 16.6 MB

## 6.21.1\. 安装 Libtool

为编译 Libtool 做准备：

```
./configure --prefix=/usr 
```

编译软件包：

```
make 
```

要测试结果，请运行：**`make check`** 。

安装软件包：

```
make install 
```

## 6.21.2\. Libtool 的内容

**安装的程序：** libtool, libtoolize**安装的库：** libltdl.{a,so}

### 简要描述

|  |  |
| --- | --- |
| `libtool` | 提供通用的库编译支持。 |
| `libtoolize` | 提供了一种标准方式来将 `libtool` 支持加入到一个软件包中 |
| `libltdl` | 隐藏 dlopening 库的复杂细节 |

# 6.22\. Perl-5.8.8

Perl 将 C, sed, awk 和 sh 的最佳特性集于一身，是一种强大的编程语言。

**预计编译时间：** 1.5 SBU**所需磁盘空间：** 143 MB

## 6.22.1\. 安装 Perl

为了运行测试套件，要先创建一个基本的 `/etc/hosts` 文件，好几个测试都需要它来解析 localhost 的名称：

```
echo "127.0.0.1 localhost $(hostname)" > /etc/hosts 
```

对 Perl 的设置进行更多的控制，你可以运行交互的 `Configure` 脚本，精心选择编译配置。如果你能接受 Perl 的自动配置(这是很明智的)，就用下面的命令：

```
./configure.gnu --prefix=/usr \
    -Dman1dir=/usr/share/man/man1 \
    -Dman3dir=/usr/share/man/man3 \
    -Dpager="/usr/bin/less -isR" 
```

**配置选项的含义：**

*`-Dpager="/usr/bin/less -isR"`*

纠正 `perldoc` 代码调用 `less` 程序时的一个错误。

*`-Dman1dir=/usr/share/man/man1 -Dman3dir=/usr/share/man/man3`*

因为 Groff 还没有安装，`Configure` 会认为我们不想安装 Perl 的 man 手册。应用这个参数来改变这种情况：

编译软件包：

```
make 
```

要测试结果，请运行：**`make test`** 。

安装软件包：

```
make install 
```

## 6.22.2\. Perl 的内容

**安装的程序：** a2p, c2ph, dprofpp, enc2xs, find2perl, h2ph, h2xs, instmodsh, libnetcfg, perl, perl5.8.8(→perl), perlbug, perlcc, perldoc, perlivp, piconv, pl2pm, pod2html, pod2latex, pod2man, pod2text, pod2usage, podchecker, podselect, psed(→s2p), pstruct(→c2ph), s2p, splain, xsubpp**安装的库：太多了，有好几百个，无法在这里全部列出！**

### 简要描述

|  |  |
| --- | --- |
| `a2p` | 把 awk 翻译成 Perl |
| `c2ph` | 显示 `cc -g -S` 产生的 C 语言结构。 |
| `dprofpp` | 显示 Perl 的 profile 数据。 |
| `enc2xs` | 为 Encode 模块编译 Perl 扩展，用于 Unicode 字符映射或 Tcl 编码文件。 |
| `find2perl` | 将 `find` 命令翻译成 Perl 代码。 |
| `h2ph` | 将 `.h` 的 C 头文件转成 `.ph` 的 perl 头文件 |
| `h2xs` | 将 `.h` 的 C 头文件转成 perl 程序扩展 |
| `instmodsh` | 一个监测安装 Perl 模块的 Shell 脚本，甚至可以从已安装模块中创建压缩包。 |
| `libnetcfg` | 可以用来配置 `libnet` |
| `perl` | 综合了 C, `sed`, `awk`, `sh` 特性和能力于一体的强大的编程语言 |
| `perl5.8.8` | `perl` 的硬连接 |
| `perlbug` | 生成关于 perl 和相关模块的 bug 报告，并且 mail 给他们。 |
| `perlcc` | 从 perl 程序生成可执行文件 |
| `perldoc` | 显示嵌于 perl 安装目录或者一个 perl 脚本的 .pod 格式的小文档。 |
| `perlivp` | Perl 安装验证过程，可以用它来验证 Perl 及其库是否安装正常。 |
| `piconv` | A 是 Perl 版本的字符编码转换程序，类似于 `iconv` |
| `pl2pm` | 将 Perl4 样式的 `.pl` 库文件转化为 Perl5 样式的 `.pm` 库模块的工具 |
| `pod2html` | 将 pod 格式的文件转为 html 格式 |
| `pod2latex` | 将 pod 格式的文件转为 LaTeX 格式 |
| `pod2man` | 将 pod 数据转为格式化的 *roff 输入 |
| `pod2text` | 将 pod 数据转为格式化的 ASCII 文本 |
| `pod2usage` | 打印文件内嵌的 pod 文档的使用信息 |
| `podchecker` | 检查 pod 格式的文档的语法 |
| `podselect` | 有选择的打印 pod 文档内容到标准输出 |
| `psed` | 是 Perl 版本的流式编辑器，类似于 `sed` |
| `pstruct` | 显示 `cc -g -S` 产生的 C 语言结构 |
| `s2p` | 把 `sed` 脚本翻译成 Perl 脚本 |
| `splain` | 强制 Perl 输出冗余警告信息 |
| `xsubpp` | 把 Perl XS 代码转换成 C 代码 |

# 6.23\. Readline-5.1

Readline 软件包是一个提供命令行编辑和历史纪录功能的库集合。

**预计编译时间：** 0.1 SBU**所需磁盘空间：** 10.2 MB

## 6.23.1\. 安装 Readline

上游开发者已经修正了自从 Readline-5.1 之后版本的一些问题。应用这些修正：

```
patch -Np1 -i ../readline-5.1-fixes-3.patch 
```

重新安装 Readline 会将老的库 libraryname 重命名为<libraryname>.old。然而着并不是一个问题。在某些情况下它会引发`ldconfig` 的一个链接 bug。应用下面的两个 sed 命令可以避免这种情况：

```
sed -i '/MV.*old/d' Makefile.in
sed -i '/{OLDSUFF}/c:' support/shlib-install 
```

为编译 Readline 做准备：

```
./configure --prefix=/usr --libdir=/lib 
```

编译软件包：

```
make SHLIB_LIBS=-lncurses 
```

**make 选项的含义：**

*`SHLIB_LIBS=-lncurses`*

这个选项强制 Readline 链接到 `libncurses` 库。

这个软件包没有附带测试程序。

安装软件包：

```
make install 
```

给 Readline 动态库更多恰当的权限：

```
chmod -v 755 /lib/lib{readline,history}.so* 
```

将静态库移动到一个更合理的位置：

```
mv -v /lib/lib{readline,history}.a /usr/lib 
```

删除 `/lib` 中的 `.so` 文件，并将它们重新连接到 `/usr/lib` 中：

```
rm -v /lib/lib{readline,history}.so
ln -sfv ../../lib/libreadline.so.5 /usr/lib/libreadline.so
ln -sfv ../../lib/libhistory.so.5 /usr/lib/libhistory.so 
```

## 6.23.2\. Readline 的内容

**安装的库：** libhistory.{a,so}, libreadline.{a,so}

### 简要描述

|  |  |
| --- | --- |
| `libhistory` | 提供一个统一的调用历史行的用户接口 |
| `libreadline` | 应用于各种需要命令行接口的应用程序的统一的用户接口的辅助程序 |

# 6.24\. Zlib-1.2.3

Zlib 软件包包含 zlib 库，很多程序中的压缩或者解压缩程序都会用到这个库。

**预计编译时间：** 少于 0.1 SBU**所需磁盘空间：** 3.1 MB

## 6.24.1\. 安装 Zlib

### 注意

如果在环境变量中指定了 `CFLAGS` 的话，Zlib 就不能正常编译共享库。如果你想使用自定义的 `CFLAGS` 环境变量，请在下述整个 configure 命令的过程中始终把 *`-fPIC`* 指令加在 `CFLAGS` 的最前面，结束后还必须再撤销它。

为编译 Zlib 做准备：

```
./configure --prefix=/usr --shared --libdir=/lib 
```

编译软件包：

```
make 
```

要测试结果，请运行：**`make check`** 。

安装共享库：

```
make install 
```

上面的命令将会在 `/lib` 目录下安装一个 `.so` 文件。我们将要移除它并重新连接到 `/usr/lib` 目录下：

```
rm -v /lib/libz.so
ln -sfv ../../lib/libz.so.1.2.3 /usr/lib/libz.so 
```

编译静态库(非共享库)：

```
make clean
./configure --prefix=/usr
make 
```

要测试静态库可以用这个命令：**`make check`** 。

安装静态库：

```
make install 
```

修正静态库的权限：

```
chmod -v 644 /usr/lib/libz.a 
```

## 6.24.2\. Zlib 的内容

**安装的库：** libz.{a,so}

### 简要描述

|  |  |
| --- | --- |
| `libz` | 包含很多程序都用到的压缩和解压函数 |

# 6.25\. Autoconf-2.59

Autoconf 能生成用于自动配置源代码的 shell 脚本

**预计编译时间：** 少于 0.1 SBU**所需磁盘空间：** 7.2 MB

## 6.25.1\. 安装 Autoconf

为编译 Autoconf 做准备：

```
./configure --prefix=/usr 
```

编译软件包：

```
make 
```

要测试结果，请运行：**`make check`** 。这可能要花费比较长的时间，大约 3 SUB。另外，因为要用到 Automake 的原因，跳过测试二。为了全面测试，可以在 Auotomake 安装完后重新测试。

安装软件包：

```
make install 
```

## 6.25.2\. Autoconf 的内容

**安装的程序：** autoconf, autoheader, autom4te, autoreconf, autoscan, autoupdate, ifnames

### 简要描述

|  |  |
| --- | --- |
| `autoconf` | 一个产生可以自动配置源代码包，生成 shell 脚本的工具，以适应各种类 UNIX 系统的需要。`autoconf` 产生的配置脚本在运行时独立于 `autoconf` ，因此使用这些脚本的用户不需要安装 `autoconf` 。 |
| `autoheader` | 能够创建供 configure 脚本使用的 C *#define* 语句模板文件。 |
| `autom4te` | 一个 M4 宏处理器的包装 |
| `autoreconf` | 当 `autoconf` 和 `automake` 的模版文件被改变的时候，以正确的顺序自动运行 `autoconf`, `autoheader`, `aclocal`, span>`automake`, `gettextize`, `libtoolize` 以节约时间。 |
| `autoscan` | 为软件包创建 `configure.in` 文件。它以命令行参数中指定的目录为根(如果未给定参数则以当前目录为根)的目录树中检查源文件，搜索其中的可移植性问题，为那个软件包创建一个 `configure.scan` 文件以充当一个预备性的 `configure.in` 文件。 |
| `autoupdate` | 将 `configure.in` 文件中 `autoconf` 宏的旧名称更新为当前名称 |
| `ifnames` | 为一个软件包写 `configure.in` 文件提供帮助，它打印软件包中那些在 C 预处理器中已经使用了的标识符。如果一个包已经设置成具有某些可移植属性，这个程序能够帮助指出它的 `configure` 脚本应该如何检查。它可以用来填补由 `configure.in` 产生的 `autoscan` 中的隔阂。 |

# 6.26\. Automake-1.9.6

Automake 与 Autoconf 配合使用，产生 Makefile 文件。

**预计编译时间：** 少于 0.1 SBU**所需磁盘空间：** 7.9 MB

## 6.26.1\. 安装 Automake

为编译 Automake 做准备：

```
./configure --prefix=/usr 
```

编译软件包：

```
make 
```

要测试结果，请运行：**`make check`** 。 This takes a long time, about 10 SUB.

安装软件包：

```
make install 
```

## 6.26.2\. Automake 的内容

**安装的程序：** acinstall, aclocal, aclocal-1.9.6, automake, automake-1.9.6, compile, config.guess, config.sub, depcomp, elisp-comp, install-sh, mdate-sh, missing, mkinstalldirs, py-compile, symlink-tree, ylwrap

### 简要描述

|  |  |
| --- | --- |
| `acinstall` | 用来安装 aclocal 风格的 M4 文件的脚本 |
| `aclocal` | 根据 `configure.in` 文件的内容，自动生成 `aclocal.m4` 文件 |
| `aclocal-1.9.6` | `aclocal`的硬链接 |
| `automake` | 根据 `Makefile.am` 文件的内容，自动生成 `Makefile.in` 文件。在目录的顶层运行该命令，可以为一个包建立所有的 `Makefile.in` 文件。通过扫描 `configure.in` 文件，它可以自动找到每一个合适的 `Makefile.am` 文件并且产生相应的 `Makefile.in` 文件。 |
| `automake-1.9.6` | `automake`的一个硬链接 |
| `compile` | 包装了编译器的脚本 |
| `config.guess` | 用来为特定的 build, host, target 尝试猜测标准的系统名称的脚本 |
| `config.sub` | 配置验证子脚本 |
| `depcomp` | 在编译程序的同时产生其依赖信息的脚本 |
| `elisp-comp` | 按字节编译 Emacs Lisp 代码 |
| `install-sh` | 能安装程序、脚本、数据文件的脚本 |
| `mdate-sh` | 打印程序和目录更改时间的脚本 |
| `missing` | 一个用来填充在安装过程检查出的缺失的 GNU 程序空位的脚本 |
| `mkinstalldirs` | 产生目录树结构的脚本 |
| `py-compile` | 编译 Python 程序 |
| `symlink-tree` | 为整个目录创建符号链接的脚本 |
| `ylwrap` | 包装了 `lex` 和 `yacc` 的脚本 |

# 6.27\. Bash-3.1

Bash 是 Bourne-Again Shell 的缩写，它在 UNIX 系统中作为 shell 被广泛使用。

**预计编译时间：** 0.4 SBU**所需磁盘空间：** 25.8 MB

## 6.27.1\. 安装 Bash

如果你下载了 Bash 的文档包，可以通过下面的命令安装：

```
tar -xvf ../bash-doc-3.1.tar.gz &&
sed -i "s|htmldir = @htmldir@|htmldir = /usr/share/doc/bash-3.1|" \
    Makefile.in 
```

上游开发者解决了从 Bash-3.1 后的一些问题：

```
patch -Np1 -i ../bash-3.1-fixes-8.patch 
```

为编译 Bash 做准备：

```
./configure --prefix=/usr --bindir=/bin \
    --without-bash-malloc --with-installed-readline 
```

**配置选项的含义：**

*`--with-installed-readline`*

这个选项是告诉 Bash 使用已经安装的系统的 `readline` 库，而不是它自己的 readline 版本。

编译软件包：

```
make 
```

要测试结果，请运行：**`make tests`** 。

安装软件包：

```
make install 
```

运行新编译的 `bash` 程序来替换正在执行的这一个：

```
exec /bin/bash --login +h 
```

### 注意

上面命令里使用的参数指示 `bash` 产生一个交互式的登陆 shell 并且继续禁用哈希功能，以便新的程序一旦被安装就立即投入使用。

## 6.27.2\. Bash 的内容

**安装的程序：** bash, bashbug, sh(→bash)

### 简要描述

|  |  |
| --- | --- |
| `bash` | 作为命令行解释器被广泛使用。它能在执行命令前解释非常复杂的命令行参数，这使它成为一个强大的工具。 |
| `bashbug` | 帮助用户用标准格式编写和提交有关 `bash` 的 bug 报告的脚本。 |
| `sh` | 指向 `bash` 的符号连接。当运行 `sh` 的时候，`bash` 会尽量模仿老的 `sh` 历史环境来运行，同时遵循 POSIX 标准。 |

# 6.28\. Bzip2-1.0.3

Bzip2 包含了对文件进行压缩和解压缩的工具，对于文本文件，`bzip2` 比传统的 `gzip` 拥有更高压缩比。

**预计编译时间：** 少于 0.1 SBU**所需磁盘空间：** 5.3 MB

## 6.28.1\. 安装 Bzip2

下面的补丁可以为这个软件包安装相应的文档：

```
patch -Np1 -i ../bzip2-1.0.3-install_docs-1.patch 
```

`bzgrep` 命令并不将传递给它的文件名中的 '|' 和 '&' 进行转义，这就会允许别有用心的用户执行任意命令。下面的补丁可以解决这个问题：

```
patch -Np1 -i ../bzip2-1.0.3-bzgrep_security-1.patch 
```

`bzdiff` 脚本仍然会使用原来的 `tempfile` 程序。可以使用 `mktemp` 来替换：

```
sed -i 's@tempfile -d /tmp -p bz@mktemp -p /tmp@' bzdiff 
```

为编译 Bzip2 做准备：

```
make -f Makefile-libbz2_so
make clean 
```

**make 参数的含义：**

*`-f Makefile-libbz2_so`*

这会采用一个另外一个 `Makefile` 来编译 Bzip2，也就是这里的 Makefile-libbz2_so 文件，它创建一个动态链接库 `libbz2.so` ，然后把 Bzip2 的工具都链接到这个库上。

编译并测试软件包：

```
make 
```

如果重新安装 Bzip2，必须首先执行 **`rm -vf /usr/bin/bz*`** ，否则下面的 `make install` 会出错。

安装 Bzip2：

```
make install 
```

把 `bzip2` 二进制共享库拷贝到 `/bin` 目录，创建必要的符号链接，再做一些清理工作：

```
cp -v bzip2-shared /bin/bzip2
cp -av libbz2.so* /lib
ln -sv ../../lib/libbz2.so.1.0 /usr/lib/libbz2.so
rm -v /usr/bin/{bunzip2,bzcat,bzip2}
ln -sv bzip2 /bin/bunzip2
ln -sv bzip2 /bin/bzcat 
```

## 6.28.2\. Bzip2 的内容

**安装的程序：** bunzip2(→bzip2), bzcat(→bzip2), bzcmp, bzdiff, bzegrep, bzfgrep, bzgrep, bzip2, bzip2recover, bzless, bzmore**安装的库：** libbz2.{a,so}

### 简要描述

|  |  |
| --- | --- |
| `bunzip2` | 解压使用 bzip2 压缩的文件 |
| `bzcat` | 解压缩指定的文件到标准输出 |
| `bzcmp` | 对 bzip2 压缩的文件运行 `cmp` 命令 |
| `bzdiff` | 对 bzip2 压缩的文件运行 `diff` 命令 |
| `bzgrep` | 对 bzip2 压缩的文件运行 `grep` 命令 |
| `bzegrep` | 对 bzip2 压缩的文件运行 `egrep` 命令 |
| `bzfgrep` | 对 bzip2 压缩的文件运行 `fgrep` 命令 |
| `bzip2` | 使用 Burrows-Wheeler 块排列文本压缩算法和霍夫曼编码来压缩文件。压缩比要大于 gzip 工具使用的基于"Lempel-Ziv"的压缩算法(如 `gzip` 格式)，接近 PPM 统计压缩算法族的压缩比。 |
| `bzip2recover` | 试图从被破坏的 bzip2 文件中恢复数据 |
| `bzless` | 对 bzip2 压缩的文件运行 `less` 命令 |
| `bzmore` | 对 bzip2 压缩的文件运行 `more` 命令 |
| `libbz2*` | 利用 Burrows-Wheeler 算法，实现无损块顺序数据压缩的库文件。 |

# 6.29\. Diffutils-2.8.1

Diffutils 软件包里的程序向你显示两个文件或目录的差异，常用来生成软件的补丁。

**预计编译时间：** 0.1 SBU**所需磁盘空间：** 6.3 MB

## 6.29.1\. 安装 Diffutils

POSIX 要求 `diff` 命令能够根据当前的 locale 处理 whitespace（空白符）。 下面的 patch 可以解决这个问题：

```
patch -Np1 -i ../diffutils-2.8.1-i18n-1.patch 
```

上面的这个 patch 将会导致用一个无效的程序`help2man`来重新编译 `diff.1` man 帮助。结果导致 `diff` 的 man 不可读。我们可以通过改变 `man/diff.1` 的时间戳来避免这个问题：

```
touch man/diff.1 
```

为编译 Diffutils 做准备：

```
./configure --prefix=/usr 
```

编译软件包：

```
make 
```

这个软件包没有附带测试程序。

安装软件包：

```
make install 
```

## 6.29.2\. Diffutils 的内容

**安装的程序：** cmp, diff, diff3, sdiff

### 简要描述

|  |  |
| --- | --- |
| `cmp` | 比较两个文件，并指出它们是否不同及不同的字节。 |
| `diff` | 比较两个文件或目录，并指出哪些文件的哪些行不同。 |
| `diff3` | 逐行比较三个文件 |
| `sdiff` | 合并两个文件，并以交互方式输出结果 |

# 6.30\. E2fsprogs-1.39

E2fsprogs 提供用于 `ext2` 文件系统的工具。它还支持 `ext3` 日志文件系统。

**预计编译时间：** 0.4 SBU**所需磁盘空间：** 31.2 MB

## 6.30.1\. 安装 E2fsprogs

推荐在 E2fsprogs 的源码目录外面来编译它：

```
mkdir -v build
cd build 
```

为编译 E2fsprogs 做准备：

```
../configure --prefix=/usr --with-root-prefix="" \
    --enable-elf-shlibs --disable-evms 
```

**配置选项的含义：**

*`--with-root-prefix=""`*

有的程序(如 `e2fsck` )对系统来说是非常重要的，例如，在 `/usr` 没有挂载的情况下。这些程序和库就应放在像 `/lib` 和 `/sbin` 这些目录中。如果没有把上面的参数传递给 E2fsprogs 的 configure 脚本，它就会把程序放在 `/usr` 目录下。

*`--enable-elf-shlibs`*

这会创建共享的库，供 E2fsprogs 包中的一些程序使用。

*`--disable-evms`*

这个选项禁止了企业卷管理系统(EVMS)插件的支持。因为这个插件并没有更新到适合最新的 EVMS 接口并且 EVMS 并不是基本 LFS 系统的一部分，所以我们并不需要这个插件。请参考 EVMS 网站 [*http://evms.sourceforge.net/*](http://evms.sourceforge.net/) 以获得更多信息。

编译软件包：

```
make 
```

要测试结果，请运行：**`make check`** 。

E2fsprogs 的一个测试会尝试分配 256 MB 内存。如果你没有充足的 RAM 空间，推荐打开足够的交换分区。参见节 2.3, "在新分区上创建文件系统" 和 节 2.4, "挂载新分区" 获取关于创建和激活交换分区的细节。

安装二进制文件和文档：

```
make install 
```

安装共享库：

```
make install-libs 
```

## 6.30.2\. E2fsprogs 的内容

**安装的程序：** badblocks, blkid, chattr, compile_et, debugfs, dumpe2fs, e2fsck, e2image, e2label, filefrag, findfs, fsck, fsck.ext2, fsck.ext3, logsave, lsattr, mk_cmds, mke2fs, mkfs.ext2, mkfs.ext3, mklost+found, resize2fs, tune2fs, uuidgen.**安装的库：** libblkid.{a,so}, libcom_err.{a,so}, libe2p.{a,so}, libext2fs.{a,so}, libss.{a,so}, libuuid.{a,so}

### 简要描述

|  |  |
| --- | --- |
| `badblocks` | 用来检查设备(通常是硬盘分区)上的坏块 |
| `blkid` | 定位并打印出块设备属性的命令行工具 |
| `chattr` | 在 `ext2` 和 `ext3` 文件系统上改变文件属性 |
| `compile_et` | 用来将错误代码(error-code)和相关出错信息的列表 转化为适用于 `com_err` 库的 C 语言文件 |
| `debugfs` | 文件系统调试器。能用来检查和改变 `ext2` 文件系统的状态 |
| `dumpe2fs` | 打印特定设备上现存的文件系统的超级块(super block)和块群(blocks group)的信息 |
| `e2fsck` | 用来检查和修复 `ext2` 和 `ext3` 文件系统 |
| `e2image` | 将关键的 `ext2` 文件系统数据保存到一个文件中 |
| `e2label` | 显示或者改变指定设备上的 `ext2` 文件系统标识 |
| `filefrag` | 报告一个文件的碎片情况 |
| `findfs` | 通过卷标或通用唯一标识符(UUID)寻找文件系统 |
| `fsck` | 用来检查或者修理文件系统 |
| `fsck.ext2` | 默认检查 `ext2` 文件系统 |
| `fsck.ext3` | 默认检查 `ext3` 文件系统 |
| `logsave` | 把一个命令的输出保存在日志文件中 |
| `lsattr` | 列出 ext2 文件系统上的文件属性 |
| `mk_cmds` | 将一个包含命令列表的文件转化为适用于子系统库 `libss` 的 C 源文件 |
| `mke2fs` | 用来创建 ext2 或 ext3 文件系统 |
| `mkfs.ext2` | 默认创建 `ext2` 文件系统 |
| `mkfs.ext3` | 默认创建 `ext3` 文件系统 |
| `mklost+found` | 在 `ext2` 文件系统上创建一个 `lost+found` 目录，并给该目录预分配磁盘数据块，以减轻 `e2fsck` 命令的负担。 |
| `resize2fs` | 可以用来增大或缩小 `ext2` 文件系统 |
| `tune2fs` | 调整 `ext2` 文件系统的可调参数 |
| `uuidgen` | 创建一个新的通用唯一标识符(UUID)。这个新 UUID 可以被认为是在所有已创建的 UUID 中独一无二的，不论是在本地的系统或者别的系统，过去还是将来。 |
| `libblkid` | 包含设备识别和节点释放的库函数 |
| `libcom_err` | 通用错误显示库 |
| `libe2p` | 用于 `dumpe2fs`, `chattr`, `lsattr` |
| `libext2fs` | 允许用户级的程序操作 `ext2` 文件系统 |
| `libss` | 用于 `debugfs` |
| `libuuid` | 用来给对象产生通用唯一标识符(UUID)使之可以在本地系统之外引用 |

*   后退

    Diffutils-2.8.1

*   前进

    File-4.17

*   上一级

*   首页.

# 6.31\. File-4.17

File 是用来判断文件类型的工具。

**预计编译时间：** 0.1 SBU**所需磁盘空间：** 7.5 MB

## 6.31.1\. 安装 File

为编译 File 做准备：

```
./configure --prefix=/usr 
```

编译软件包：

```
make 
```

这个软件包没有附带测试程序。

安装软件包：

```
make install 
```

## 6.31.2\. File 的内容

**安装的程序：** file**安装的库：** libmagic.{a,so}

### 简要描述

|  |  |
| --- | --- |
| `file` | 测试每一个指定的文件并且试图对它们进行分类。有三个测试集，按照下面的顺序执行：文件系统测试、幻数(magic number)测试、语言测试。第一个测试成功后会打印文件文件类型。 |
| `libmagic` | 包含产生幻数(magic number)的函数，供`file`程序使用。 |

# 6.32\. Findutils-4.2.27

Findutils 包含查找文件的工具，既能即时查找(递归的搜索目录，并可以显示、创建和维护文件)，也能在数据库里查找(通常比递归查找快但是在数据库没有及时更新的情况下，结果并不可靠)。

**预计编译时间：** 0.2 SBU**所需磁盘空间：** 12 MB

## 6.32.1\. 安装 Findutils

为编译 Findutils 做准备：

```
./configure --prefix=/usr --libexecdir=/usr/lib/findutils \
    --localstatedir=/var/lib/locate 
```

**配置选项的含义：**

*`--localstatedir`*

将 `locate` 数据库的位置指定为 `/var/lib/locate` ，以符合 FHS 标准。

编译软件包：

```
make 
```

要测试结果，请运行：**`make check`** 。

安装软件包：

```
make install 
```

LFS-Bootscripts 包中的一些脚本依赖于 `find`。因为在系统启动的前期，`/usr` 目录还是无法访问的（比如还没有挂载上），因此这个程序需要放在根分区上。 `updatedb` 脚本也需要用完全路径来修正：

```
mv -v /usr/bin/find /bin
sed -i -e 's/find:=${BINDIR}/find:=\/bin/' /usr/bin/updatedb 
```

## 6.32.2\. Findutils 的内容

**安装的程序：** bigram, code, find, frcode, locate, updatedb, xargs

### 简要描述

|  |  |
| --- | --- |
| `bigram` | 以前用来创建 `locate` 数据库。 |
| `code` | 以前用来创建 `locate` 数据库。它是 `frcode` 的前身。 |
| `find` | 在一个目录和其子目录里面找符合条件的文件 |
| `frcode` | 被 `updatedb` 调用来压缩文件名列表，它使用的是前端压缩(front-compression)，可以减小数据库 4 到 5 倍。 |
| `locate` | 扫描一个文件名称数据库，可以列出在数据库中符合条件的文件或者目录。 |
| `updatedb` | 更新 `locate` 数据库。它会扫描整个文件系统，包括所有挂载的文件系统(除非设定参数禁止)，并且把每一个找到的文件和目录放到 `locate` 数据库里面。 |
| `xargs` | 可以在一系列文件上运行同一个命令 |

# 6.33\. Flex-2.5.33

Flex 软件包包含一个能生成识别文本模式的程序的工具。

**预计编译时间：** 0.1 SBU**所需磁盘空间：** 8.4 MB

## 6.33.1\. 安装 Flex

为编译 Flex 做准备：

```
./configure --prefix=/usr 
```

编译软件包：

```
make 
```

要测试结果，请运行：**`make check`** 。

安装软件包：

```
make install 
```

一些程序试图在 `/usr/lib` 下面寻找 `lex` 库，可以创建一个符号链接来满足要求：

```
ln -sv libfl.a /usr/lib/libl.a 
```

一些程序并不知道 `flex` 而是试图寻找 `lex` 程序(flex 是实现 lex 功能的另一种也是更好的选择)。为了满足少数一些程序的需要，我们将创建一个 `lex` 脚本，这个脚本调用 `flex` 并通过它来模仿 `lex` 的输出文件命名惯例：

```
cat > /usr/bin/lex << "EOF"
#!/bin/sh
# Begin /usr/bin/lex

exec /usr/bin/flex -l "$@"

# End /usr/bin/lex
EOF
chmod -v 755 /usr/bin/lex 
```

## 6.33.2\. Flex 的内容

**安装的程序：** flex, lex**安装的库：** libfl.a

### 简要描述

|  |  |
| --- | --- |
| `flex` | 是一个用来生成识别文本的模式程序的工具。模式识别在很多程序中是非常有用，用户设置一些查询规则，然后 flex 可以生成一个查询程序。人们使用 flex 可以比亲自编写查询程序更便捷。 |
| `lex` | 在 `lex` 仿真模式下运行 `flex` 的一个脚本 |
| `libfl.a` | `flex` 库 |

# 6.34\. GRUB-0.97

GRUB 程序包包含 GRand 统一引导装载程序。

**预计编译时间：** 0.2 SBU**所需磁盘空间：** 10.2 MB

## 6.34.1\. 安装 GRUB

如果你把这个包缺省的优化参数(包括 *`-march`* 和 *`-mcpu`* 参数)改变的话，它会有些不正常的表现。因此，如果你定义了任何优化参数的话，比如 CFLAGS 和 CXXFLAGS ，我们劝你在编译时 unset 或修改它们。

开始先打一个补丁来达到更好的硬件侦测、修复 GCC 4.x 的一些问题以及为一些磁盘控制器提供更好的 SATA 支持：

```
patch -Np1 -i ../grub-0.97-disk_geometry-1.patch 
```

为编译 GRUB 做准备：

```
./configure --prefix=/usr 
```

编译软件包：

```
make 
```

要测试结果，请运行：**`make check`** 。

安装软件包：

```
make install
mkdir -v /boot/grub
cp -v /usr/lib/grub/i386-pc/stage{1,2} /boot/grub 
```

把 i386-pc 换成适合你的平台的路径。

i386-pc 目录还包含一些 *stage1_5 文件，是为不同的文件系统准备的。看看有哪些文件，并把你所需要的拷贝到 /boot/grub 目录下。多数人需要 e2fs_stage1_5 和/或 reiserfs_stage1_5 文件。

## 6.34.2\. GRUB 的内容

**安装的程序：** grub, grub-install, grub-md5-crypt, grub-set-default, grub-terminfo, mbchk

### 简要描述

|  |  |
| --- | --- |
| `grub` | GRUB 的命令解释 shell |
| `grub-install` | 在指定设备上安装 GRUB |
| `grub-md5-crypt` | 以 MD5 加密一个密码 |
| `grub-set-default` | 为 Grub 设置默认启动入口 |
| `grub-terminfo` | 从 terminfo 名称产生 terminfo 命令。如果你在一个不常见的终端时，可以使用这个命令。 |
| `mbchk` | 检查多重启动内核的格式 |

# 6.35\. Gawk-3.1.5

Gawk 软件包包含用于管理文本文件的程序。

**预计编译时间：** 0.2 SBU**所需磁盘空间：** 18.2 MB

## 6.35.1\. 安装 Gawk

在某些情况下，Gawk-3.1.5 会释放一块没有分配的内存。应用下面的 patch 可以解决问题：

```
patch -Np1 -i ../gawk-3.1.5-segfault_fix-1.patch 
```

为编译 Gawk 做准备：

```
./configure --prefix=/usr --libexecdir=/usr/lib 
```

由于在 `configure` 脚本中的一个 bug ，Gawk 就不会发现 Glibc 中的某些方面的 locale 支持。 这个 bug 会导致很多问题。例如，Gettext 测试单元会失败。解决这个问题的方法就是在 `config.h` 中添加丢失的宏定义：

```
cat >>config.h <<"EOF"
#define HAVE_LANGINFO_CODESET 1
#define HAVE_LC_MESSAGES 1
EOF 
```

编译软件包：

```
make 
```

要测试结果，请运行：**`make check`** 。

安装软件包：

```
make install 
```

## 6.35.2\. Gawk 的内容

**安装的程序：** awk(→gawk), gawk, gawk-3.1.5, grcat, igawk, pgawk, pgawk-3.1.5, pwcat

### 简要描述

|  |  |
| --- | --- |
| `awk` | 指向 `gawk` 的链接 |
| `gawk` | `awk` 的 GNU 版本，用来管理文本文件的程序。 |
| `gawk-3.1.5` | `gawk` 的硬链接 |
| `grcat` | 读取组数据库 `/etc/group` |
| `igawk` | 赋予 `gawk` 包含文件的能力 |
| `pgawk` | `gawk` 的概要分析(profiling)版本 |
| `pgawk-3.1.5` | `pgawk` 的硬链接 |
| `pwcat` | `/etc/passwd` 读取密码数据库 |

# 6.36\. Gettext-0.14.5

Gettext 用于系统的国际化和本地化，可以在编译程序的时候使用本国语言支持(NLS)，可以使程序的输出使用用户设置的语言而不是英文。

**预计编译时间：** 1 SBU**所需磁盘空间：** 65 MB

## 6.36.1\. 安装 Gettext

为编译 Gettext 做准备：

```
./configure --prefix=/usr 
```

编译软件包：

```
make 
```

要测试结果，请运行：**`make check`** 。这需要大约 5 SBU 。

安装软件包：

```
make install 
```

## 6.36.2\. Gettext 的内容

**安装的程序：** autopoint, config.charset, config.rpath, envsubst, gettext, gettext.sh, gettextize, hostname, msgattrib, msgcat, msgcmp, msgcomm, msgconv, msgen, msgexec, msgfilter, msgfmt, msggrep, msginit, msgmerge, msgunfmt, msguniq, ngettext, xgettext**安装的库：** libasprintf.{a,so}, libgettextlib.so, libgettextpo.{a,so}, libgettextsrc.so

### 简要描述

|  |  |
| --- | --- |
| `autopoint` | 将标准的 gettext infrastructure 文件拷贝到源码包中 |
| `config.charset` | 产生一个依赖于系统的字符编码表 |
| `config.rpath` | 输出依赖于系统的变量，描述如何在可执行程序中设置库文件的实时查找路径。 |
| `envsubst` | 替换 shell 格式化字符串中的环境变量 |
| `gettext` | 通过在消息列表中查找，来将一种自然语言的消息翻译成用户的本地语言 |
| `gettext.sh` | 主要作为一个 shell 的函数库来为 gettext 服务 |
| `gettextize` | 拷贝所有的标准 Gettext 文件到软件包的顶层目录下，以进行国际化。 |
| `hostname` | 用不同的格式显示一个网络主机名 |
| `msgattrib` | 根据翻译目录中消息的属性过滤它们，并且操作这些属性。 |
| `msgcat` | 将给定的 `.po` 文件合并到一起 |
| `msgcmp` | 比较两个 `.po` 文件，看它们是否包含相同的 msgid 字符串 |
| `msgcomm` | 找出不同 `.po` 文件中的相同信息 |
| `msgconv` | 把一个翻译列表转化成另一种字符编码 |
| `msgen` | 建立一个英文翻译目录 |
| `msgexec` | 对一个翻译列表中的所有翻译执行同一个命令 |
| `msgfilter` | 对一个翻译列表中的所有翻译使用同一个过滤器 |
| `msgfmt` | 从翻译列表生成一个二进制的消息列表 |
| `msggrep` | 将一个翻译列表中的所有符合给定格式或者属于给定源文件的消息展开 |
| `msginit` | 生成一个新的 `.po` 文件，并使用用户环境中的消息来对这个文件进行初始化。 |
| `msgmerge` | 将两个翻译合并成一个 |
| `msgunfmt` | 将二进制翻译文件反编译成源文件 |
| `msguniq` | 将翻译目录中重复的翻译合并成一个 |
| `ngettext` | 显示依赖于数字格式的文本文件的本国语言翻译 |
| `xgettext` | 从源文件中展开消息行, 用来创建起始翻译模板 |
| `libasprintf` | 定义 *autosprintf* 类，使 C 的函数以 C++ 程序可使用的结构输出，与 *<string>* 字符串和 *<iostream>* 流一起使用。 |
| `libgettextlib` | 包含多个 gettext 程序使用的函数，是私有库 |
| `libgettextpo` | 用来写处理 `.po` 文件的程序。当 Gettext 自带的标准程序(如 `msgcomm`, `msgcmp`, `msgattrib`, `msgen`)不能满足要求时，可以使用这个库。 |
| `libgettextsrc` | 包含多个 gettext 程序使用的函数，是私有库。 |

# 6.37\. Grep-2.5.1a

Grep 可以搜索文件中符合指定匹配模式的行。

**预计编译时间：** 0.1 SBU**所需磁盘空间：** 4.8 MB

## 6.37.1\. 安装 Grep

当前的 Grep 包有很多 bug,尤其是对多字节的 loacles 的支持。RedHat 采用下面的这个 patch 来解决部分问题：

```
patch -Np1 -i ../grep-2.5.1a-redhat_fixes-2.patch 
```

需要修改测试文件的权限，才能使打过 patch 后在测试中通过：

```
chmod +x tests/fmbtest.sh 
```

为编译 Grep 做准备：

```
./configure --prefix=/usr --bindir=/bin 
```

编译软件包：

```
make 
```

要测试结果，请运行：**`make check`** 。

安装软件包：

```
make install 
```

## 6.37.2\. Grep 的内容

**安装的程序：** egrep(→grep), fgrep(→grep), grep

### 简要描述

|  |  |
| --- | --- |
| `egrep` | 打印出匹配扩展正则表达式模式的行 |
| `fgrep` | 对固定字符串列表进行匹配 |
| `grep` | 对基本正则表达式进行匹配 |

# 6.38\. Groff-1.18.1.1

Groff 软件包包含几个处理和格式化文本的程序。Groff 把标准的文本和特殊的命令翻译成格式化的输出，就像你在 man 手册页里看到的那样。

**预计编译时间：** 0.4 SBU**所需磁盘空间：** 39.2 MB

## 6.38.1\. 安装 Groff

应用下面的 patch 来添加 "ascii8" 和 "nippon" 设备到 Groff：

```
patch -Np1 -i ../groff-1.18.1.1-debian_fixes-1.patch 
```

### 注意

这些设备在转换一些非 ISO-8859-1 编码的非英语的 man 手册页时，会用到。现在对于 Groff-1.19.x 没有同样功能的 patch：

许多屏幕字体没有 Unicode 的单引号和破折号。告诉 Groff 使用等价的 ASCII 字符：

```
sed -i -e 's/2010/002D/' -e 's/2212/002D/' \
    -e 's/2018/0060/' -e 's/2019/0027/' font/devutf8/R.proto 
```

Groff 希望环境变量 `PAGE` 包含缺省的纸张尺寸。对于在美国的人来说，应当使用 *`PAGE=letter`* ，如果你住在其他地方，可能需要把 *`PAGE=letter`* 改成 *`PAGE=A4`* 。

为编译 Groff 做准备：

```
PAGE=<paper_size> ./configure --prefix=/usr --enable-multibyte 
```

编译软件包：

```
make 
```

这个软件包没有附带测试程序。

安装软件包：

```
make install 
```

有些文档程序，比如 `xman` ，没有下面的符号链接会不能正常工作：

```
ln -sv eqn /usr/bin/geqn
ln -sv tbl /usr/bin/gtbl 
```

## 6.38.2\. Groff 的内容

**安装的程序：** addftinfo, afmtodit, eqn, eqn2graph, geqn(→eqn), grn, grodvi, groff, groffer, grog, grolbp, grolj4, grops, grotty, gtbl(→tbl), hpftodit, indxbib, lkbib, lookbib, mmroff, neqn, nroff, pfbtops, pic, pic2graph, post-grohtml, pre-grohtml, refer, soelim, tbl, tfmtodit, troff

### 简要描述

|  |  |
| --- | --- |
| `addftinfo` | 读取一个 troff 字体文件并增加一些 `groff` 系统使用的附加点阵字体。 |
| `afmtodit` | 建立同 `groff` 和 `grops` 一起使用的字体文件。 |
| `eqn` | 将嵌于 troff 输入文件中的方程描述翻译成 `troff` 能够理解的命令 |
| `eqn2graph` | 把 EQN 等式转换成反图像 |
| `geqn` | 指向 `eqn` 的连接 |
| `grn` | `groff` 的 gremlin 文件预处理器 |
| `grodvi` | `groff` 的产生 TeX dvi 格式的驱动 |
| `groff` | groff 文档格式系统的前端。通常它调用 `troff` 程序和对指定设备适用的后处理器 |
| `groffer` | 把 groff 文件和 man 文档显示在 X 和 tty 上 |
| `grog` | 读取文件然后猜测使用 *`-e`*, *`-man`*, *`-me`*, *`-mm`*, *`-ms`*, *`-p`*, *`-s`*, *`-t`* 中的哪个 `groff` 参数来打印文件。并把带有这个参数的 `groff` 命令输出到标准输出。 |
| `grolbp` | LBP-4 和 LBP-8 激光打印机系列的 `groff` 驱动 |
| `grolj4` | 产生适用于 HP Laserjet4 打印机的 PCL5 格式的 `groff` 驱动 |
| `grops` | 将 GNU `troff` 的输出翻译成 PostScript |
| `grotty` | 将 GNU `troff` 的标准输出翻译成适合类打字机设备的格式 |
| `gtbl` | 指向 `tbl` 的连接 |
| `hpftodit` | 使用 `groff -Tlj4` 从一个 HP-tagged 字体文件中创建 groff 使用的字体文件。 |
| `indxbib` | 建立同 `refer`, `lookbib`, `lkbib` 一起使用的文件的文献数据库的反向列表 |
| `lkbib` | 在文献数据库中搜索包括指定关键字的条目，并将其输出到标准输出 |
| `lookbib` | 打印一个标准错误的提示，除非标准输入不是终端。从标准输入读入关键字搜索在指定文件中的文献数据库中的含有这些关键字的条目，并将结果输出到标准输出。 |
| `mmroff` | 一个简易的 `groff` 预处理器 |
| `neqn` | 将方程格式化，使其成为适应 ASCII 输出的脚本 |
| `nroff` | 这个脚本用 `nroff` 命令仿真 `groff` 命令 |
| `pfbtops` | 将 `.pfb` 格式的 Postscript 字体翻译成 ASCII 码 |
| `pic` | 将内嵌于 troff 或 TeX 输入文件中的图像编译成 `troff` 或 TeX 理解的指令 |
| `pic2graph` | 把 PIC 图表转换成反图像 |
| `post-grohtml` | 将 GNU `troff` 的输出翻译成 HTML |
| `pre-grohtml` | 将 GNU `troff` 的输出翻译成 HTML |
| `refer` | 将一个文件拷贝到标准输出并丢弃 *.[* 和 *.]* 之间作为引用的内容和在 *.R1* 和 *.R2* 之间解释如何处理这些引用的命令 |
| `soelim` | 读取文件将其中的 *.so 文件* 表格替换为 *文件* 的内容 |
| `tbl` | 将内嵌于 troff 或者 TeX 输入文件中的表格编译成 `troff` 或者 TeX 理解的指令 |
| `tfmtodit` | 建立 `groff -Tdvi` 使用的字体文件 |
| `troff` | 和 Unix 的 `troff` 高度兼容。一般运行 `groff` 来调用它，`groff` 依照合适的顺序并使用合适的参数来执行预处理程序和后处理程序。 |

# 6.39\. Gzip-1.3.5

gzip 包含用 Lempel-Ziv 编码(LZ77)来压缩和解压文件的程序。

**预计编译时间：** 少于 0.1 SBU**所需磁盘空间：** 2.2 MB

## 6.39.1\. 安装 Gzip

Gzip 有两个安全漏洞，下面的补丁可以修正它：

```
patch -Np1 -i ../gzip-1.3.5-security_fixes-1.patch 
```

为编译 Gzip 做准备：

```
./configure --prefix=/usr 
```

`gzexe` 脚本里包含对 `gzip` 程序的硬路径引用。由于我们后面要改变 `gzip` 程序的位置，就需要用下面的命令改变脚本中的硬路径：

```
sed -i 's@"BINDIR"@/bin@g' gzexe.in 
```

编译软件包：

```
make 
```

这个软件包没有附带测试程序。

安装软件包：

```
make install 
```

把 `gzip` 程序移动到 `/bin` 目录并创建一些常用的符号连接：

```
mv -v /usr/bin/gzip /bin
rm -v /usr/bin/{gunzip,zcat}
ln -sv gzip /bin/gunzip
ln -sv gzip /bin/zcat
ln -sv gzip /bin/compress
ln -sv gunzip /bin/uncompress 
```

## 6.39.2\. Gzip 的内容

**安装的程序：** compress(→gzip), gunzip(→gzip), gzexe, gzip, uncompress(→gunzip), zcat(→gzip), zcmp, zdiff, zegrep, zfgrep, zforce, zgrep, zless, zmore, znew

### 简要描述

|  |  |
| --- | --- |
| `compress` | 压缩和解压缩文件 |
| `gunzip` | 解压由 gzip 压缩过的文件 |
| `gzexe` | 将文件压缩成可以自解压的可执行文件 |
| `gzip` | 通过 Lempel-Ziv 编码(LZ77)压缩指定的文件 |
| `uncompress` | 解压由 gzip 压缩过的文件 |
| `zcat` | 将解压缩的数据写到标准输出上 |
| `zcmp` | 在压缩文件上调用 `cmp` 命令 |
| `zdiff` | 在压缩文件上调用 `diff` 命令 |
| `zegrep` | 在压缩文件上调用 `egrep` 命令 |
| `zfgrep` | 在压缩文件上调用 `fgrep` 命令 |
| `zforce` | 强制性地为每一个 gzip 文件加上 `.gz` 扩展名，这样`gzip` 就不会对它们再次进行压缩。这个程序可能在一个文件经过传输后名字被截短的情况下能够派上用场。 |
| `zgrep` | 在压缩文件上调用 `grep` 命令 |
| `zless` | 在压缩文件上调用 `less` 命令 |
| `zmore` | 在压缩文件上调用 `more` 命令 |
| `znew` | 将`.Z`格式的文件(使用 compress 压缩)转压缩成`.gz`格式(使用 gzip 压缩) |

# 6.40\. Inetutils-1.4.2

Inetutils 包含基本的网络程序。

**预计编译时间：** 0.2 SBU**所需磁盘空间：** 8.9 MB

## 6.40.1\. 安装 Inetutils

应用一个 patch，使其能够在 GCC-4.0.3 下编译：

```
patch -Np1 -i ../inetutils-1.4.2-gcc4_fixes-3.patch 
```

我们并不安装 Inetutils 的全部程序，然而，它默认会把所有程序的 man 文档都装上。下面的补丁能解决这个问题：

```
patch -Np1 -i ../inetutils-1.4.2-no_server_man_pages-1.patch 
```

为编译 Inetutils 做准备：

```
./configure --prefix=/usr --libexecdir=/usr/sbin \
    --sysconfdir=/etc --localstatedir=/var \
    --disable-logger --disable-syslogd \
    --disable-whois --disable-servers 
```

**配置选项的含义：**

*`--disable-logger`*

阻止 inetutils 安装 `logger` 程序，脚本利用这个程序向系统日志守护进程传递消息。我们不安装它是因为 Util-linux 包含一个更好的版本。

*`--disable-syslogd`*

这个参数阻止 inetutils 安装 System Log Daemon(系统日志守护进程)，我们将在后面的 Sysklogd 软件包中安装它。

*`--disable-whois`*

阻止 inetutils 编译 `whois` 客户端，因为它已经很陈旧了。在 BLFS book 里面有安装更好的 `whois` 客户端的指导。

*`--disable-servers`*

阻止安装几种网络服务器。这些服务器对于基本的 LFS 系统是不合适的，有的还不安全，很多服务器都有更好的替代者。参见 [*http://www.linuxfromscratch.org/blfs/view/svn/basicnet/inetutils.html*](http://www.linuxfromscratch.org/blfs/view/svn/basicnet/inetutils.html) 。

编译软件包：

```
make 
```

这个软件包没有附带测试程序。

安装软件包：

```
make install 
```

把 `ping` 程序移动到符合 FHS 标准的位置：

```
mv -v /usr/bin/ping /bin 
```

## 6.40.2\. Inetutils 的内容

**安装的程序：** ftp, ping, rcp, rlogin, rsh, talk, telnet, tftp

### 简要描述

|  |  |
| --- | --- |
| `ftp` | 文件传输协议程序 |
| `ping` | 向网络主机发送请求应答包，并报告回复所需的时间。 |
| `rcp` | 远程文件拷贝 |
| `rlogin` | 远程登陆 |
| `rsh` | 运行远程 shell |
| `talk` | 与另一个用户交谈 |
| `telnet` | TELNET 协议接口 |
| `tftp` | 小文件传输程序 |

# 6.41\. IPRoute2-2.6.16-060323

IPRoute2 包含了基本的和高级的基于 IPv4 网络的程序。

**预计编译时间：** 0.2 SBU**所需磁盘空间：** 4.8 MB

## 6.41.1\. 安装 IPRoute2

编译软件包：

```
make SBINDIR=/sbin 
```

**make 选项的含义：**

*`SBINDIR=/sbin`*

确保将 IPRoute2 包中的二进制文件安装到 `/sbin` 目录中以符合 FHS 标准，因为一些 IPRoute2 二进制文件将会被 LFS-Bootscripts 使用。

这个软件包没有附带测试程序。

安装软件包：

```
make SBINDIR=/sbin install 
```

`arpd` 二进制文件链接到在 `/usr` 目录中的 Berkeley DB 库，并且使用数据库 `/var/lib/arpd/arpd.db`。因此，按照 FHS，它必须存在于 `/usr/sbin`目录中。移动它到那里：

```
mv -v /sbin/arpd /usr/sbin 
```

## 6.41.2\. IPRoute2 的内容

**安装的程序：** arpd, ctstat(→lnstat), ifcfg, ifstat, ip, lnstat, nstat, routef, routel, rtacct, rtmon, rtpr, rtstat(→lnstat), ss, tc.

### 简要描述

|  |  |
| --- | --- |
| `arpd` | 用户空间的 ARP 守护进程。用在大型网络中，那里内核空间的 ARP 实现不是很合适;或者是用在设置一个蜜罐。 |
| `ctstat` | 连接状态工具 |
| `ifcfg` | `ip`命令的 shell 脚本包装 |
| `ifstat` | 显示网络接口的统计信息，包括接口发送和接收到的包数量。 |
| `ip` | 主可执行程序，它包含以下几个功能：`ip link _`[device]`_` 查看和修改设备状态`ip addr` 查看地址的特性，添加新地址、删除旧地址。`ip neighbor` 查看邻居的特性，添加新邻居、删除旧邻居。`ip rule` 查看和修改路由规则`ip route` 查看路由表和修改路由表规则`ip tunnel` 查看和修改 IP 隧道及其特性`ip maddr` 查看和修改多播地址及其特性`ip mroute` 设置、修改、删除多播路由`ip monitor` 不间断的监视设备状态、地址、路由 |
| `lnstat` | 提供 Linux 网络统计信息，用于替代旧的 `rtstat` 程序。 |
| `nstat` | 显示网络统计信息 |
| `routef` | `ip route` 的一个组件，用于刷新路由表。 |
| `routel` | `ip route` 的一个组件，用于列出路由表。 |
| `rtacct` | 显示 `/proc/net/rt_acct` 文件的内容 |
| `rtmon` | 路由监视工具 |
| `rtpr` | 将 `ip -o` 的输出转换为可读的格式 |
| `rtstat` | 路由状态工具 |
| `ss` | 类似于 `netstat` 命令，显示活动的连接。 |
| `tc` | 流量控制，用于实现服务质量(QOS)和服务级别(COS)：`tc qdisc` 建立排队规则`tc class` 建立基于级别的队列调度`tc estimator` 估算网络流量`tc filter` 设置 QOS/COS 包过滤器`tc policy` 设置 QOS/COS 规则 |

# 6.42\. Kbd-1.12

Kbd 包含键盘映射表和键盘工具。

**预计编译时间：** 少于 0.1 SBU**所需磁盘空间：** 12.3 MB

## 6.42.1\. 安装 Kbd

Backspace 键和 Delete 键的功能在 kbd 包的键盘映射中是不一样的。下面的 patch 修正了 i386 的键盘映射中的这个问题：

```
patch -Np1 -i ../kbd-1.12-backspace-1.patch 
```

打完 patch 之后，Backspace 键会产生字符编码 127，Delete 键会产生一个著名的逃脱序列。

应用 patch 来修正 Kbd 中的 `setfont` 在 GCC-4.0.3 下编译出错的问题：

```
patch -Np1 -i ../kbd-1.12-gcc4_fixes-1.patch 
```

为编译 Kbd 做准备：

```
./configure --datadir=/lib/kbd 
```

**配置选项的含义：**

*`--datadir=/lib/kbd`*

这个选项把键盘布局信息存放到根分区内，而不是存放在默认的 `/usr/share/kbd`。

编译软件包：

```
make 
```

这个软件包没有附带测试程序。

安装软件包：

```
make install 
```

### 注意

对于一些语言（例如，白俄罗斯），在 Kbd 包中没有提供相应的键盘映射。系统假定使用 ISO-8859-5 编码，通常使用 CP1251 键盘映射。 这些语种的用户需要单独下载相应的键盘映射：

LFS-Bootscripts 包中的一些脚本依赖于 `kbd_mode`， `openvt`，和 `setfont`。因为 `/usr` 在启动的早些时候是无法访问的（没有挂载）。那些二进制文件需要放在根分区上：

```
mv -v /usr/bin/{kbd_mode,openvt,setfont} /bin 
```

## 6.42.2\. Kbd 的内容

**安装的程序：** chvt, deallocvt, dumpkeys, fgconsole, getkeycodes, kbd_mode, kbdrate, loadkeys, loadunimap, mapscrn, openvt, psfaddtable(→psfxtable), psfgettable(→psfxtable), psfstriptable(→psfxtable), psfxtable, resizecons, setfont, setkeycodes, setleds, setmetamode, showconsolefont, showkey, unicode_start, unicode_stop

### 简要描述

|  |  |
| --- | --- |
| `chvt` | 改变前台虚拟终端 |
| `deallocvt` | 重新分配不用的虚拟终端 |
| `dumpkeys` | 显示键盘转换表 |
| `fgconsole` | 显示活动虚拟控制台的数量 |
| `getkeycodes` | 显示内核中扫描码与键盘码的转换表 |
| `kbd_mode` | 设置或显示键盘模式 |
| `kbdrate` | 设置或显示键盘重复和延迟的速度 |
| `loadkeys` | 加载键盘转换表 |
| `loadunimap` | 加载内核的 Unicode 到字体(unicode-to-font)之间的影射表 |
| `mapscrn` | 把用户定义的输出字符影射表加载到控制台驱动器中。注意这个程序已经过时，它实现的功能已经并入 `setfont` 程序。 |
| `openvt` | 在一个新虚拟终端启动一个程序 |
| `psfaddtable` | 链接到 `psfxtable` |
| `psfgettable` | 链接到 `psfxtable` |
| `psfstriptable` | 链接到 `psfxtable` |
| `psfxtable` | 一套处理控制台字体的 Unicode 字符表的工具 |
| `resizecons` | 让内核改变控制台的大小 |
| `setfont` | 改变控制台的 EGA 或 VGA 字体 |
| `setkeycodes` | 告诉内核的键盘驱动程序在扫描码/键码(scancode-to-keycode)影射表中加入新的影射，当你的键盘上有某些特殊建的时候这个就很有用了。 |
| `setleds` | 设置当前终端键盘的发光二极管(LED)标志 |
| `setmetamode` | 设置键盘的元键(meta key) |
| `showconsolefont` | 显示当前 EGA / VGA 终端的屏幕字体 |
| `showkey` | 测试键盘发出的扫描码和键码 |
| `unicode_start` | 使控制台进入 UNICODE 模式。在 LFS 系统中从不使用，因为应用程序并未配置为支持 UNICODE 。 |
| `unicode_stop` | 终止控制台的 UNICODE 模式 |

# 6.43\. Less-394

Less 软件包包含一个文本文件查看器。

**预计编译时间：** 0.1 SBU**所需磁盘空间：** 2.6 MB

## 6.43.1\. 安装 Less

为编译 Less 做准备：

```
./configure --prefix=/usr --sysconfdir=/etc 
```

**配置选项的含义：**

*`--sysconfdir=/etc`*

这个选项告诉程序建立软件包时在 `/etc` 目录里查找配制文件。

编译软件包：

```
make 
```

这个软件包没有附带测试程序。

安装软件包：

```
make install 
```

## 6.43.2\. Less 的内容

**安装的程序：** less, lessecho, lesskey

### 简要描述

|  |  |
| --- | --- |
| `less` | 一个文件显示或分页程序。它显示某文件的内容，并可以分卷、寻找字符串、跳转到某一标记。 |
| `lessecho` | 在 Unix 系统中，可以用来扩展元字符，比如 *** 和 *?* |
| `lesskey` | 为 `less` 定义按健 |

# 6.44\. Make-3.80

Make 自动地确定一个大型程序的哪些片段需要重新编译，并且发出命令去重新编译它们。

**预计编译时间：** 0.1 SBU**所需磁盘空间：** 7.8 MB

## 6.44.1\. 安装 Make

为编译 Make 做准备：

```
./configure --prefix=/usr 
```

编译软件包：

```
make 
```

要测试结果，请运行：**`make check`** 。

安装软件包：

```
make install 
```

## 6.44.2\. Make 的内容

**安装的程序：** make

### 简要描述

|  |  |
| --- | --- |
| `make` | 自动地确定一个大型程序的哪些片段需要重新编译，并且发出命令去重新编译它们。 |

# 6.45\. Man-DB-2.4.3

Man-DB 包含查找和显示 man 手册页的程序。

**预计编译时间：** 0.2 SBU**所需磁盘空间：** 9 MB

## 6.45.1\. 安装 Man-DB

对 Man-DB 的源码需要做三个调整。

第一，改变已翻译的 Man-DB 带的 manual 页的位置，使其在传统和 UTF-8 的 locale 环境下均能使用：

```
mv man/de{_DE.88591,} &&
mv man/es{_ES.88591,} &&
mv man/it{_IT.88591,} &&
mv man/ja{_JP.eucJP,} &&
sed -i 's,\*_\*,??,' man/Makefile.in 
```

第二，使用 `sed` 的替换功能来删除 `man_db.conf` 文件中的 "/usr/man" 行， 来避免在使用像 `whatis` 这样的命令时的冗余结果：

```
sed -i '/\t\/usr\/man/d' src/man_db.conf.in 
```

第三，改变 Man-DB 在运行时应该能够发现的程序的记录，但它们还没有安装：

```
cat >>include/manconfig.h.in <<"EOF"
#define WEB_BROWSER "exec /usr/bin/lynx"
#define COL "/usr/bin/col"
#define VGRIND "/usr/bin/vgrind"
#define GRAP "/usr/bin/grap"
EOF 
```

`col` 是 Util-linux 的一部分， `lynx` 基于文本的浏览器（参见 BLFS 的安装说明）, `vgrind` 把程序源码转换为 Groff 的输入，`grap` 在 Groff 文档中对图表排版非常有用。 `vgrind` 和 `grap` 不是查看 manual 页所必须的。它们不是 LFS 的一部分，也不是 BLFS 的一部分，但是在完成 LFS 之后，你应该能够自己安装。

为编译 Man-DB 做准备：

```
./configure --prefix=/usr --enable-mb-groff --disable-setuid 
```

**配置选项的含义：**

*`--enable-mb-groff`*

通知 `man` 在格式化非 ISO-8859-1 格式的 manual 页时，使用 "ascii8" 和 "nippon" Groff 设备。

*`--disable-setuid`*

使 `man` 不能给用户 `man` 设置 uid 位。

编译软件包：

```
make 
```

这个软件包没有附带测试程序。

安装软件包：

```
make install 
```

一些软件包提供这个版本 `man` 无法显示的 UTF-8 编码的 man 手册页。 下面的脚本将会允许它们中的一些转换成为下面表中的编码格式。Man-DB 期望 manual 页的编码格式是下面表中的一项。在显示它们的时 候，可以根据需要转换成实际的 locale 编码，因此它们能够在传统的和 UTF-8 的 locale 下显示。因为脚本是在系统构建和对公共数据 中限制使用的，所以，我们不必担心错误检测和使用非预期的临时文件名。

```
cat >>convert-mans <<"EOF"
#!/bin/sh -e
FROM="$1"
TO="$2"
shift ; shift
while [ $# -gt 0 ]
do
        FILE="$1"
        shift
        iconv -f "$FROM" -t "$TO" "$FILE" >.tmp.iconv
        mv .tmp.iconv "$FILE"
done
EOF
install -m755 convert-mans  /usr/bin 
```

一些关于 man 和 info 页的压缩可以在 BLFS 手册中找到（[*http://www.linuxfromscratch.org/blfs/view/cvs/postlfs/compressdoc.html*](http://www.linuxfromscratch.org/blfs/view/cvs/postlfs/compressdoc.html)）。

## 6.45.2\. Non-English Manual Pages in LFS

每一种 Linux 的发行版在存储 manual 页的字符编码方面都有自己的方法。比如，RedHat 以 UTF-8 编码存储所有的 manual 页;然而 Debian 使用不同的语言编码(通常是 8 位的)。这 就导致包在为不同发行版设置 manual 页时不兼容。

LFS 采用跟 Debian 一样的方法。这是因为 Man-DB 不能够理解 UTF-8 存储的 man 手册页。Man-DB 比 Man 好在在任意的 locale 下不需要额外的配置就可以使用。最后，我们没有选用 RedHat 的方法，因为它的`groff`文本格式组织的不好。

语言跟字符编码的关联已经列在下面的表中。Man-DB 会在查看的时候自动的将它们转变为 locale 的编码。

**Table 6.1\. manual 页的字符编码表 pages**

| Language (code) | Encoding |
| --- | --- |
| Danish (da) | ISO-8859-1 |
| German (de) | ISO-8859-1 |
| English (en) | ISO-8859-1 |
| Spanish (es) | ISO-8859-1 |
| Finnish (fi) | ISO-8859-1 |
| French (fr) | ISO-8859-1 |
| Irish (ga) | ISO-8859-1 |
| Galician (gl) | ISO-8859-1 |
| Indonesian (id) | ISO-8859-1 |
| Icelandic (is) | ISO-8859-1 |
| Italian (it) | ISO-8859-1 |
| Dutch (nl) | ISO-8859-1 |
| Norwegian (no) | ISO-8859-1 |
| Portuguese (pt) | ISO-8859-1 |
| Swedish (sv) | ISO-8859-1 |
| Czech (cs) | ISO-8859-2 |
| Croatian (hr) | ISO-8859-2 |
| Hungarian (hu) | ISO-8859-2 |
| Japanese (ja) | EUC-JP |
| Korean (ko) | EUC-KR |
| Polish (pl) | ISO-8859-2 |
| Russian (ru) | KOI8-R |
| Slovak (sk) | ISO-8859-2 |
| Turkish (tr) | ISO-8859-9 |

### 注意

不在上面列表中的语言的 Manual 页是不支持的。挪威语无法使用，因为从 no_NO 到 nb_NO 的 locale 转变。韩语因为不完整的 patch 也是功能不全的。 of the incomplete Groff patch.

如果 manual 页的编码是 Man-DB 所预期的，manual 页就会被拷贝到 `/usr/share/man/<language code>`。例如，法语的 manual 页([*http://ccb.club.fr/man/man-fr-1.58.0.tar.bz2*](http://ccb.club.fr/man/man-fr-1.58.0.tar.bz2)) 可以使用下面的命令安装：

```
mkdir -p /usr/share/man/fr &&
cp -rv man? /usr/share/man/fr 
```

如果 manual 页是 UTF-8 编码的（例如，针对 "RedHat" 的） ，而不是上面列表中的。它们不得不在安装之前从 UTF-8 转变为列表中的编码。这可以通过 `convert-mans` 来实现。比如，西班牙的 manual 页 ([*http://ditec.um.es/~piernas/manpages-es/man-pages-es-1.55.tar.bz2*](http://ditec.um.es/~piernas/manpages-es/man-pages-es-1.55.tar.bz2)) 可以通过下面的命令来安装：

```
mv man7/iso_8859-7.7{,X}
convert-mans UTF-8 ISO-8859-1 man?/*.?
mv man7/iso_8859-7.7{X,}
make install 
```

### 注意

在转换的过程中需要除去 `man7/iso_8859-7.7` 文件，因为它已经存在于 ISO-8859-1 中，这是 man-pages-es-1.55 的一个 bug。下一个版本可能就不会需要了。

## 6.45.3\. Man-DB 的内容

**安装的程序：** accessdb, apropos, catman, convert-mans,lexgrog, man, mandb, manpath, whatis, zsoelim

### 简要描述

|  |  |
| --- | --- |
| `accessdb` | 将 `whatis` 数据库的内容转储为人类可读形式 |
| `apropos` | 搜索 `whatis` 数据库，显示包含给定字符串的系统命令的简短描述。 |
| `catman` | 创建或更新预格式化的 manual 页 |
| `convert-mans` | 重新格式化 man 手册页，以使 Man-DB 能够显示它们 |
| `lexgrog` | 显示一行给定 manual 页的摘要信息。 |
| `man` | 格式化并显示请求的 manual 页 |
| `mandb` | 创建或更新 `whatis` 数据库 |
| `manpath` | 显示 $MANPATH 的内容或在 man.conf 中设置的搜索路径（如果 $MANPATH 没有设置），以及用户的环境变量。 |
| `whatis` | 搜索 `whatis` 数据库，显示包含给定关键字的系统命令的简短描述。 |
| `zsoelim` | 读取文件并用提到的 *file* 的内容来替换 *.so file* 格式的行。 |

# 6.46\. Mktemp-1.5

Mktemp 软件包包含用于在 shell 脚本中创建安全临时文件的程序。

**预计编译时间：** 少于 0.1 SBU**所需磁盘空间：** 0.4 MB

## 6.46.1\. 安装 Mktemp

许多脚本目前仍然使用被反对使用的类似于 `mktemp` 的 `tempfile` 程序，我们现在要给 Mktemp 打一个补丁，以使它包含 `tempfile` 包装：

```
patch -Np1 -i ../mktemp-1.5-add_tempfile-3.patch 
```

为编译 Mktemp 做准备：

```
./configure --prefix=/usr --with-libc 
```

**配置选项的含义：**

*`--with-libc`*

这个使得 `mktemp` 程序从系统的 C 库中使用 *mkstemp* 和 *mkdtemp* 的功能。

编译软件包：

```
make 
```

这个软件包没有附带测试程序。

安装软件包：

```
make install
make install-tempfile 
```

## 6.46.2\. Mktemp 的内容

**安装的程序：** mktemp, tempfile

### 简要描述

|  |  |
| --- | --- |
| `mktemp` | 使用安全性较强的方式创建临时文件，用于脚本中。 |
| `tempfile` | 使用比 `mktemp` 安全性较弱的方式创建临时文件，但是能够满足向后的兼容性。 |

# 6.47\. Module-Init-Tools-3.2.2

Module-Init-Tools 包含处理 2.5.47 及以上版本的内核模块时使用的工具。

**预计编译时间：** 少于 0.1 SBU**所需磁盘空间：** 7 MB

## 6.47.1\. 安装 Module-Init-Tools

首先更正一个当模块被指定使用正则表达式时会出现的潜在问题：

```
patch -Np1 -i ../module-init-tools-3.2.2-modprobe-1.patch 
```

执行下面的命令进行测试（注意 `make distclean` 命令需要清理源码树，因为作为测试过程的一部分，源码会重新编译：

```
./configure &&
make check &&
make distclean 
```

为编译 Module-Init-Tools 做准备：

```
./configure --prefix=/ --enable-zlib 
```

编译软件包：

```
make 
```

安装软件包：

```
make INSTALL=install install 
```

**make 参数的含义：**

*`INSTALL=install`*

正常情况下，如果二进制文件已经存在了，`make install` 就不会安装它们。 这个选项是调用 `install` 而不是使用默认封装的脚本。

## 6.47.2\. Module-Init-Tools 的内容

**安装的程序：** depmod, generate-modprobe.conf, insmod, insmod.static, lsmod, modinfo, modprobe, rmmod

### 简要描述

|  |  |
| --- | --- |
| `depmod` | 创建一个可加载内核模块的依赖关系文件，`modprobe` 用它来自动加载模块。 |
| `generate-modprobe.conf` | 从一个现存的 2.2 或者 2.4 版本内核的模块设置中创建一个 modprobe.conf 文件 |
| `insmod` | 向正在运行的内核加载模块 |
| `insmod.static` | `insmod` 的静态编译版本 |
| `lsmod` | 显示当前已加载的内核模块信息 |
| `modinfo` | 检查与内核模块相关联的目标文件，并打印出所有能得到的信息。 |
| `modprobe` | 利用 `depmod` 创建的依赖关系文件来自动加载相关的模块 |
| `rmmod` | 从当前运行的内核中卸载模块 |

# 6.48\. Patch-2.5.4

Patch 根据"补丁"文件的内容来修改原来的文件。补丁文件通常是用 `diff`程序创建的，包含如何修改文件的指导。

**预计编译时间：** 少于 0.1 SBU**所需磁盘空间：** 1.6 MB

## 6.48.1\. 安装 Patch

为编译 Patch 做准备：

```
./configure --prefix=/usr 
```

编译软件包：

```
make 
```

这个软件包没有附带测试程序。

安装软件包：

```
make install 
```

## 6.48.2\. Patch 的内容

**安装的程序：** patch

### 简要描述

|  |  |
| --- | --- |
| `patch` | 根据一个`patch` 文件来对文件进行修改。通常情况下，一个 patch 文件是一个差别清单。这个清单用`diff`程序创建。通过将这些差别应用到原始文件，patch 创建出修订版本。 |

# 6.49\. Psmisc-22.2

Psmisc 包含有用于显示进程信息的程序。

**预计编译时间：** 少于 0.1 SBU**所需磁盘空间：** 2.2 MB

## 6.49.1\. 安装 Psmisc

为编译 Psmisc 做准备：

```
./configure --prefix=/usr --exec-prefix="" 
```

**配置选项的含义：**

*`--exec-prefix=""`*

这个确保 Psmisc 二进制文件按照 FHS 标准被安装在 `/bin` 而不是 `/usr/bin` ，因为一些 Psmisc 二进制文件将被 LFS-Bootscripts 使用。

编译软件包：

```
make 
```

这个软件包没有附带测试程序。

安装软件包：

```
make install 
```

没有理由把 `pstree` 和 `pstree.x11` 程序安装在 `/bin` 中，所以将他们移动到 `/usr/bin` 中：

```
mv -v /bin/pstree* /usr/bin 
```

默认情况下， Psmisc 的 `pidof` 程序未被安装。 这通常情况下不是问题。因为它将在这之后的 Sysvinit 包中被安装，而且这个包提供了一个更好的 `pidof` 程序。如果你打算不使用 Sysvinit ，则可通过创建下面的符号连接来安装完整的 Psmisc ：

```
ln -sv killall /bin/pidof 
```

## 6.49.2\. Psmisc 的内容

**安装的程序：** fuser, killall, pstree, pstree.x11(→pstree)

### 简要描述

|  |  |
| --- | --- |
| `fuser` | 报告使用所给文件或文件系统的进程的进程 ID(PID)。 |
| `killall` | 通过进程名来终止进程，它发送消息到所有正在运行任意所给指令的进程。 |
| `oldfuser` | 报告使用所给文件或文件系统的进程的进程 ID(PID)。 |
| `pstree` | 以目录树的形式显示所有正在运行的进程 |
| `pstree.x11` | 同 `pstree` ，只是它在退出前要求确认 |

# 6.50\. Shadow-4.0.15

Shadow 包含用于在安全方式下处理密码的程序。

**预计编译时间：** 0.3 SBU**所需磁盘空间：** 18.6 MB

## 6.50.1\. 安装 Shadow

### 注意

如果你打算强制使用高强度密码，请参考 [*http://www.linuxfromscratch.org/blfs/view/svn/postlfs/cracklib.html*](http://www.linuxfromscratch.org/blfs/view/svn/postlfs/cracklib.html) 以获得如何在安装 Shadow 之前先安装 Cracklib 的指导。然后在下面的 `configure` 命令中加入 *`--with-libcrack`* 项。

为编译 Shadow 做准备：

```
./configure --libdir=/lib --enable-shared --without-selinux 
```

**配置选项的含义：**

*`--without-selinux`*

selinux 的支持默认是打开的，但是 selinux 不包含在 LFS 基本系统中。如果这个选项不设置， `configure` 脚本会报错。

禁止安装 `groups` 程序和它的手册，Coreutils 软件包提供了一个更好的版本：

```
sed -i 's/groups$(EXEEXT) //' src/Makefile
find man -name Makefile -exec sed -i '/groups/d' {} \; 
```

禁止安装中文和韩文的手册页，因为 Man-DB 不能很好的格式化它们：

```
sed -i -e 's/ ko//' -e 's/ zh_CN zh_TW//' man/Makefile 
```

Shadow 支持在 UTF-8 编码环境下的其他编码的手册。Man-DB 可以通过我们安装的 `convert-mans` 脚本来转换并显示它们。

```
 for i in de es fi fr id it pt_BR; do
    convert-mans UTF-8 ISO-8859-1 man/${i}/*.?
done

for i in cs hu pl; do
    convert-mans UTF-8 ISO-8859-2 man/${i}/*.?
done

convert-mans UTF-8 EUC-JP man/ja/*.?
convert-mans UTF-8 KOI8-R man/ru/*.?
convert-mans UTF-8 ISO-8859-9 man/tr/*.? 
```

编译软件包：

```
make 
```

这个软件包没有附带测试程序。

安装软件包：

```
make install 
```

Shadow 使用两个文件来设置系统的身份认证。下面安装这两个设置文件：

```
cp -v etc/{limits,login.access} /etc 
```

不使用默认的 *crypt* 加密方法， 而使用更为安全的 *MD5* 对密码进行加密， 并且它同样允许密码长于 8 个字符。同时将用户邮箱位置从过时的 `/var/spool/mail` 修改到 `/var/mail` 是也有必要的(Shadow 普遍默认使用这个位置)。这两件事都可以通过在将相应的配置文件复制至目标位置之前，对其进行更改来达到：

```
sed -e's@#MD5_CRYPT_ENAB.no@MD5_CRYPT_ENAB yes@' \
    -e 's@/var/spool/mail@/var/mail@' \
    etc/login.defs > /etc/login.defs 
```

### 注意

如果你在编译 Shadow 时启用了 Cracklib 支持，请在下面的 `sed` 命令中插入：

```
sed -i 's@DICTPATH.*@DICTPATH\t/lib/cracklib/pw_dict@' \
    /etc/login.defs 
```

移动一些放错位置的符号连结或程序到正确位置：

```
mv -v /usr/bin/passwd /bin 
```

移动 Shadow 的动态库到一个更为合适的地方：

```
mv -v /lib/libshadow.*a /usr/lib
rm -v /lib/libshadow.so
ln -sfv ../../lib/libshadow.so.0 /usr/lib/libshadow.so 
```

`useradd` 程序的 *`-D`* 选项要求存在 `/etc/default` 目录以便程序能够正常工作：

```
mkdir -v /etc/default 
```

## 6.50.2\. 配置 Shadow

这个软件包中含有用来增加、修改和删除用户或组、设置和更改他们的密码、和执行其他管理任务的工具。为了获得对 *password shadowing (影子密码)* 的完全解释，请参见 `doc/HOWTO` 文件，它在解包后的源码目录树中。假如要使用 Shadow 支持，请注意那些需要对密码进行校验的程序(如显示管理器、FTP 程序、pop3 进程等)必须兼容 Shadow 。也就是说，他们需要能够与影子密码一起工作。

为了使用影子密码，运行以下指令：

```
pwconv 
```

为使用组影子密码，运行：

```
grpconv 
```

## 6.50.3\. Setting the root password

通过运行以下命令来设置 *root* 用户的密码：

```
passwd root 
```

## 6.50.4\. Shadow 的内容

**安装的程序：** chage, chfn, chgpasswd, chpasswd, chsh, expiry, faillog, gpasswd, groupadd, groupdel, groupmod, grpck, grpconv, grpunconv, lastlog, login, logoutd, newgrp, newusers, nologin, passwd, pwck, pwconv, pwunconv, sg(→newgrp), su, useradd, userdel, usermod, vigr(→vipw), vipw**安装的库：** libshadow.{a,so}

### 简要描述

|  |  |
| --- | --- |
| `chage` | 用于设置必须对密码进行更改的最大间隔天数 |
| `chfn` | 用于对用户的全名及其他信息进行修改 |
| `chgpasswd` | 用于对一整个系列的组账号密码进行更新 |
| `chpasswd` | 用于对一整个系列的用户账号密码进行更新 |
| `chsh` | 用于更改一个用户的默认的登录 shell |
| `expiry` | 检查并加强当前的密码过期策略 |
| `faillog` | 用于检查记录登录失败的日志，或是设置账户在被锁定前最大的登录失败次数，亦可用于重置登录失败的次数。 |
| `gpasswd` | 用来增加和删除组中的成员和管理员 |
| `groupadd` | 用指定的名称建一个组 |
| `groupdel` | 删除指定名称的组 |
| `groupmod` | 用来修改所指定组的名称或 GID |
| `grpck` | 校验组文件 `/etc/group` 和 `/etc/gshadow` 的完整性 |
| `grpconv` | 从正常组文件中创建或更新一影子组文件 |
| `grpunconv` | 从 `/etc/gshadow` 更新 `/etc/group` 并将前者删除 |
| `lastlog` | 报告最近的所有用户的登录或是所指定用户的登录 |
| `login` | 被系统用来允许用户进行登录 |
| `logoutd` | 一个后台程序，用来加强对登录时间和端口进行限制 |
| `newgrp` | 用来在登录会话期间对当前的 GID 进行修改 |
| `newusers` | 用来对一整个系列的用户账户进行创建或更新 |
| `nologin` | 显示一个账户不可用的信息，被设计用来作为那些不准登录的账户的默认 shell。 |
| `passwd` | 用来对一个用户或组账户进行密码修改 |
| `pwck` | 校验密码文件 `/etc/passwd` 和 `/etc/shadow` 的完整性 |
| `pwconv` | 从一个正常的密码文件中创建或更新一影子密码文件 |
| `pwunconv` | 从 `/etc/shadow` 更新 `/etc/passwd` 并删除前者 |
| `sg` | 当一个用户的 GID 被设置到所给的组时执行所指定的命令 |
| `su` | 用另一个用户和组 ID 来运行一个 shell |
| `useradd` | 用所给名称建立一个新的用户或更新默认新用户的信息 |
| `userdel` | 删除所指定的用户账户 |
| `usermod` | 用来更改所给用户的登录名、用户标识(UID)、shell、最初组、主目录等 |
| `vigr` | 编辑 `/etc/group` 或 `/etc/gshadow` 文件 |
| `vipw` | 编辑 `/etc/passwd` 或 `/etc/shadow` 文件 |
| `libshadow` | 包含了本包中大部分程序所用到的函数 |

# 6.51\. Sysklogd-1.4.1

Sysklogd 包含记录系统日志信息的程序，比如内核处理意外事务的日志。

**预计编译时间：** 少于 0.1 SBU**所需磁盘空间：** 0.6 MB

## 6.51.1\. 安装 Sysklogd

下面的补丁修正了许多问题，包括在 2.6 系列的内核上编译 Sysklogd 会遇到的问题：

```
patch -Np1 -i ../sysklogd-1.4.1-fixes-1.patch 
```

下面的 patch 使 sysklogd 逐字的对待日志信息中 0x80--0x9f 段的字符，而不是采用八进制进行替换。未打 patch 的 sysklogd 在 UTF-8 编码下会损害日志信息：

```
patch -Np1 -i ../sysklogd-1.4.1-8bit-1.patch 
```

编译软件包：

```
make 
```

这个软件包没有附带测试程序。

安装软件包：

```
make install 
```

## 6.51.2\. 配置 Sysklogd

创建一个新的 `/etc/syslog.conf` 文件：

```
cat > /etc/syslog.conf << "EOF"
# Begin /etc/syslog.conf

auth,authpriv.* -/var/log/auth.log
*.*;auth,authpriv.none -/var/log/sys.log
daemon.* -/var/log/daemon.log
kern.* -/var/log/kern.log
mail.* -/var/log/mail.log
user.* -/var/log/user.log
*.emerg *

# End /etc/syslog.conf
EOF 
```

## 6.51.3\. Sysklogd 的内容

**安装的程序：** klogd, syslogd

### 简要描述

|  |  |
| --- | --- |
| `klogd` | 一个系统守护进程，截获并且记录下 LINUX 内核日志信息。 |
| `syslogd` | 记录下系统里所有提供日志记录的程序给出的日志和信息内容。每一个被记录的消息至少包含时间戳和主机名(通常还包括程序名)。 |

# 6.52\. Sysvinit-2.86

Sysvinit 软件包包含一些控制系统启动、运行、关闭的程序。

**预计编译时间：** 少于 0.1 SBU**所需磁盘空间：** 1 MB

## 6.52.1\. 安装 Sysvinit

当运行级别被改变(比如，正在关闭系统)，`init` 向那些由 `init` 自身开启的，并且将不会在新的运行级别里运行的线程发送终端信号。当 `init` 做上面这些事情时，会输出像"Sending processes the TERM signal"这样的信息，这看起来就像它正在向那些系统正在运行的程序发送上面这些信息一样。要避免错误地理解这个信息，可以修改源码以便可以代替为读起来像"Sending processes started by init the TERM signal"的信息，可以用下面命令：

```
sed -i 's@Sending processes@& started by init@g' \
    src/init.c 
```

编译软件包：

```
make -C src 
```

这个软件包没有附带测试程序。

安装软件包：

```
make -C src install 
```

## 6.52.2\. 配置 Sysvinit

运行下面命令，创建一个新的 `/etc/inittab` 文件：

```
cat > /etc/inittab << "EOF"
# Begin /etc/inittab

id:3:initdefault:

si::sysinit:/etc/rc.d/init.d/rc sysinit

l0:0:wait:/etc/rc.d/init.d/rc 0
l1:S1:wait:/etc/rc.d/init.d/rc 1
l2:2:wait:/etc/rc.d/init.d/rc 2
l3:3:wait:/etc/rc.d/init.d/rc 3
l4:4:wait:/etc/rc.d/init.d/rc 4
l5:5:wait:/etc/rc.d/init.d/rc 5
l6:6:wait:/etc/rc.d/init.d/rc 6

ca:12345:ctrlaltdel:/sbin/shutdown -t1 -a -r now

su:S016:once:/sbin/sulogin

1:2345:respawn:/sbin/agetty tty1 9600
2:2345:respawn:/sbin/agetty tty2 9600
3:2345:respawn:/sbin/agetty tty3 9600
4:2345:respawn:/sbin/agetty tty4 9600
5:2345:respawn:/sbin/agetty tty5 9600
6:2345:respawn:/sbin/agetty tty6 9600

# End /etc/inittab
EOF 
```

## 6.52.3\. Sysvinit 的内容

**安装的程序：** bootlogd, halt, init, killall5, last, lastb(→last), mesg, mountpoint, pidof(→killall5), poweroff(→halt), reboot(→halt), runlevel, shutdown, sulogin, telinit(→init), utmpdump, wall

### 简要描述

|  |  |
| --- | --- |
| `bootlogd` | 把启动信息记录到一个日志文件 |
| `halt` | 正常情况下等效于 `shutdown` 加上 *`-h`* 参数(当前系统运行级别是 0 时除外)。它将告诉内核去中止系统，并在系统正在关闭的过程中将日志记录到 `/var/log/wtmp` 文件里。 |
| `init` | 当内核已经初始化硬件，接管引导程序，开启指令线程时，init 会被第一个启动。 |
| `killall5` | 发送一个信号到所有进程，但那些在它自己设定级别的进程将不会被这个运行的脚本所中断。 |
| `last` | 给出哪一个用户最后一次登录(或退出登录)，它搜索 `/var/log/wtmp` 文件，出给出系统引导、关闭、运行级别改变等信息。 |
| `lastb` | 给出登失败的尝试，并写入日志 `/var/log/btmp` |
| `mesg` | 控制是否允许其他用户也有向系统所有用户发送信息的权限 |
| `mountpoint` | 检查给定的目录是否是一个挂载点 |
| `pidof` | 报告给定程序的 PID 号 |
| `poweroff` | 告诉内核中止系统并且关闭系统(参见 `halt`) |
| `reboot` | 告诉内核重启系统(参见 `halt`) |
| `runlevel` | 告前一个和当前的系统运行级别，并且将最后一些运行级别写入 `/var/run/utmp` |
| `shutdown` | 使系统安全关闭，向所有线程发送关闭信号并且通知所有已经登录的系统用户系统即将关闭。 |
| `sulogin` | 允许 *root* 登录，它通常情况下是在系统在单用户模式下运行时，由 `init` 所派生。 |
| `telinit` | 告诉 `init` 将切换到那一个运行级 |
| `utmpdump` | 以一个多用户友好的方式列出已经给出的登录文件的目录 |
| `wall` | 向所有已经登录的用户写入一个信息 |

# 6.53\. Tar-1.15.1

Tar 包含一个归档程序。

**预计编译时间：** 0.2 SBU**所需磁盘空间：** 13.7 MB

## 6.53.1\. 安装 Tar

应用一个 patch 来解决一些使用 GCC-4.0.3 时测试单元的问题：

```
patch -Np1 -i ../tar-1.15.1-gcc4_fix_tests-1.patch 
```

当文件大于 4 GB 并且使用*`-S`*选项时，tar 有一个 bug 。下面的补丁修正了这个问题：

```
patch -Np1 -i ../tar-1.15.1-sparse_fix-1.patch 
```

Tar 最近版本都存在一个缓冲区溢出漏洞，应用下面的补丁修正这个问题：

```
patch -Np1 -i ../tar-1.15.1-security_fixes-1.patch 
```

为编译 Tar 做准备：

```
./configure --prefix=/usr --bindir=/bin --libexecdir=/usr/sbin 
```

编译软件包：

```
make 
```

要测试结果，请运行：**`make check`** 。

安装软件包：

```
make install 
```

## 6.53.2\. Tar 的内容

**安装的程序：** rmt, tar

### 简要描述

|  |  |
| --- | --- |
| `rmt` | 通过一个 Internet 连接线程实施远程操作一个磁带驱动器 |
| `tar` | 从压缩档案里创建和解压文件，也可理解为压缩包(tarball)。 |

# 6.54\. Texinfo-4.8

Texinfo 软件包包含读取、写入、转换 Info 文档的程序。

**预计编译时间：** 0.2 SBU**所需磁盘空间：** 16.6 MB

## 6.54.1\. 安装 Texinfo

`info` 程序假定一个字符串在屏幕上占据的字符单元树和在内存中的字节数是一样的。 在 UTF-8 的 locale 环境下，字符串就被拆散了。下面的 patch 可以在多字节的 locale 环境下将它们转回到英文信息：

```
patch -Np1 -i ../texinfo-4.8-multibyte-1.patch 
```

Texinfo 允许本地用户通过位于临时文件中的符号连接改写任意文件，下面的补丁可以修正这个问题：

```
patch -Np1 -i ../texinfo-4.8-tempfile_fix-2.patch 
```

为编译 Texinfo 做准备：

```
./configure --prefix=/usr 
```

编译软件包：

```
make 
```

要测试结果，请运行：**`make check`** 。

安装软件包：

```
make install 
```

安装 texinfo 组件中本应由 TeX 来安装的部份(可选操作)：

```
make TEXMF=/usr/share/texmf install-tex 
```

**make 参数的含义：**

*`TEXMF=/usr/share/texmf`*

如果你以后打算安装 TeX 的话，makefile 中的 `TEXMF` 变量保存着你的 TeX 树的位置。

Info 文档系统使用一个纯文本文件来存放菜单条目的列表。这个文件位于 `/usr/share/info/dir` 。不幸的是，由于不同软件包中 Makefile 的偶然问题，有时候这个文件会与实际安装在系统里的 Info 文档不一致。如果你要再次创建 `/usr/share/info/dir` 文件，可以使用下面的命令：

```
cd /usr/share/info
rm dir
for f in *
do install-info $f dir 2>/dev/null
done 
```

## 6.54.2\. Texinfo 的内容

**安装的程序：** info, infokey, install-info, makeinfo, texi2dvi, texi2pdf, texindex

### 简要描述

|  |  |
| --- | --- |
| `info` | 用于读取 Info 文档，它类似于 man 手册页，但是他们会给出更深入的解释而不是停留在解释程序参数上。例如你可以看看 `man bison` 和 `info bison` 的区别。 |
| `infokey` | 把包括 Info 设置的源文件编译成二进制格式 |
| `install-info` | 安装 info 文档，它会更新 `info` 的索引文件 |
| `makeinfo` | 将 Texinfo 源文档翻译成不同的格式，包括 html、info 文档、文本文档。 |
| `texi2dvi` | 把给定的 Texinfo 文档格式化成可打印的设备无关的文件 |
| `texi2pdf` | 将 Texinfo 文档转化成 PDF 文件 |
| `texindex` | 对 Texinfo 索引文件进行排序 |

# 6.55\. Udev-096

Udev 软件包包含动态地创建设备节点的程序。

**预计编译时间：** 0.1 SBU**所需磁盘空间：** 6.8 MB

## 6.55.1\. 安装 Udev

udev-config 压缩包里面包含用配置 Udev 的 LFS-specific 文件。把它解压到 Udev 的源码目录：

```
tar xf ../udev-config-6.2.tar.bz2 
```

创建一些 Udev 无法创建的设备和目录，因为这些会在系统启动的早些时候被用到：

```
install -dv /lib/{firmware,udev/devices/{pts,shm}}
mknod -m0666 /lib/udev/devices/null c 1 3
ln -sv /proc/self/fd /lib/udev/devices/fd
ln -sv /proc/self/fd/0 /lib/udev/devices/stdin
ln -sv /proc/self/fd/1 /lib/udev/devices/stdout
ln -sv /proc/self/fd/2 /lib/udev/devices/stderr
ln -sv /proc/kcore /lib/udev/devices/core 
```

编译软件包：

```
make EXTRAS="extras/ata_id extras/cdrom_id extras/edd_id \
            extras/firmware extras/floppy extras/path_id \
            extras/scsi_id extras/usb_id extras/volume_id" 
```

**make 选项的含义：**

*`EXTRAS=...`*

这将会编译一些帮助程序，对定制 Udev 的规则很有帮助。

要测试结果，请运行：**`make test`** 。

注意，Udev 的测试单元会在宿主系统的日志中产生很多信息。这些都是无害的，可以被忽略掉。

安装软件包：

```
make DESTDIR=/ \
    EXTRAS="extras/ata_id extras/cdrom_id extras/edd_id \
            extras/firmware extras/floppy extras/path_id \
            extras/scsi_id extras/usb_id extras/volume_id" install 
```

**make 参数的含义：**

*`DESTDIR=/`*

防止编译 Udev 的进程杀死可能存在于宿主系统中的 `udevd` 进程。

Udev 要工作，需要配置才可以。因为默认是不安装任何配置文件的。安装 LFS-specific 配置文件：

```
cp -v udev-config-6.2/[0-9]* /etc/udev/rules.d/ 
```

安装解释如何创建 Udev 规则的文档：

```
install -m644 -D -v docs/writing_udev_rules/index.html \
    /usr/share/doc/udev-096/index.html 
```

## 6.55.2\. Udev 的内容

**安装的程序：** ata_id, cdrom_id, create_floppy_devices, edd_id, firmware_helper, path_id, scsi_id, udevcontrol, udevd, udevinfo, udevmonitor, udevsettle, udevtest, udevtrigger, usb_id, vol_id, write_cd_aliases**安装的目录：** /etc/udev

### 简要描述

|  |  |
| --- | --- |
| `ata_id` | 为 Udev 提供关于 ATA 驱动器的一个唯一的字符串和一些附加信息（uuid，label 等） |
| `cdrom_id` | 为 Udev 提供 CD-ROM 或 DVD-ROM 驱动器的性能 |
| `create_floppy_devices` | 创建所有可能的 CMOS 类型的 floppy 设备 |
| `edd_id` | 为 Udev 提供关于 BIOS 磁盘驱动器的 EDD ID |
| `firmware_helper` | 为设备加载 firmware |
| `path_id` | 提供设备的最短的唯一的硬件路径 |
| `scsi_id` | 根据向特定设备发送 SCSI INQUIRY 命令的返回信息，为 Udev 提供一个唯一的 SCSI 标识符 |
| `udevcontrol` | 为运行 `udevd` 守护进程，配置一些选项。比如，log level。 |
| `udevd` | 一个守护进程，侦听热插拔事件，并针对事件，创建设备，运行配置好的外部程序。 |
| `udevinfo` | 允许用户查询 `udev` 数据库以得到当前这个系统上所有设备的信息，它也提供一种方式去查询任何设备在 `sysfs` 树里去帮助创建 Udev 规则。 |
| `udevmonitor` | 打印出从 Udev 的规则运行之后，收到的内核事件和 Udev 发出的环境变量。 |
| `udevsettle` | 监视 Udev 的事件队列，如果当前热插拔事件被处理完就立即退出。 |
| `udevtest` | 模拟一个 `udev` 为那些给定的设备,并且打印出真实节点的名称 `udev` 可能已经被创建或者(不在 LFS 中)被重命名的网络接口。 |
| `udevtrigger` | 重新切换到内核空间的热插拔事件处理 |
| `usb_id` | 为 Udev 提供关于 USB 设备的信息 |
| `vol_id` | 为 Udev 提供一个文件系统的 label 和 uuid |
| `/etc/udev` | 包含 `udev` 配置文件、设备许可、设备命名规则。 |

# 6.56\. Util-linux-2.12r

Util-linux 软件包包含许多工具。其中比较重要的是加载、卸载、格式化、分区和管理硬盘驱动器，打开 tty 端口和得到内核消息。

**预计编译时间：** 0.2 SBU**所需磁盘空间：** 17.2 MB

## 6.56.1\. FHS 兼容性说明

FHS 推荐使用 `/var/lib/hwclock` 目录代替常用的 `/etc` 目录以定位 `adjtime` 文件。要将 `hwclock` 编译成与 FHS 兼容的程序，运行下面的命令：

```
sed -i 's@etc/adjtime@var/lib/hwclock/adjtime@g' \
    hwclock/hwclock.c
mkdir -p /var/lib/hwclock 
```

## 6.56.2\. 安装 Util-linux

Util-linux 在基于新版本的 Linux-Libc-Headers 编译时会出错，下面的补丁修正了这个问题：

```
patch -Np1 -i ../util-linux-2.12r-cramfs-1.patch 
```

为编译 Util-linux 做准备：

```
./configure 
```

编译软件包：

```
make HAVE_KILL=yes HAVE_SLN=yes 
```

**make 参数的含义：**

*`HAVE_KILL=yes`*

防止编译和安装 `kill` 程序(已经由 Procps 安装了)。

*`HAVE_SLN=yes`*

防止编译 `sln` 程序(这是静态连接的 `ln` ，已经由 Glibc 安装了)。

这个软件包没有附带测试程序。

安装软件包：

```
make HAVE_KILL=yes HAVE_SLN=yes install 
```

## 6.56.3\. Util-linux 的内容

**安装的程序：** agetty, arch, blockdev, cal, cfdisk, chkdupexe, col, colcrt, colrm, column, ctrlaltdel, cytune, ddate, dmesg, elvtune, fdformat, fdisk, flock, fsck.cramfs, fsck.minix, getopt, hexdump, hwclock, ipcrm, ipcs, isosize, line, logger, look, losetup, mcookie, mkfs, mkfs.bfs, mkfs.cramfs, mkfs.minix, mkswap, more, mount, namei, pg, pivot_root, ramsize(→rdev), raw, rdev, readprofile, rename, renice, rev, rootflags(→rdev), script, setfdprm, setsid, setterm, sfdisk, swapoff(→swapon), swapon, tailf, tunelp, ul, umount, vidmode(→rdev), whereis, write

### 简要描述

|  |  |
| --- | --- |
| `agetty` | 打开 tty 端口，为登录名称建立命令控制符，并引出 `login` 程序 |
| `arch` | 报告机器的体系结构 |
| `blockdev` | 在命令行中调用块设备的 ioctl |
| `cal` | 显示一个简单的日历 |
| `cfdisk` | 处理指定设备的分区表 |
| `chkdupexe` | 找出重复的可执行文件 |
| `col` | 过滤回显反馈线 |
| `colcrt` | 过滤那些 `nroff` 终端不具备输出的能力，比如高分点距、半线距 |
| `colrm` | 过滤掉给出的列 |
| `column` | 把输出格式化为几列 |
| `ctrlaltdel` | 设置 CTRL+ALT+DEL 组合键的功能为硬重启或软重启 |
| `cytune` | 查询和修改 Cyclade 驱动器的中断入口 |
| `ddate` | 把阳历日期转换为 Discordian 日期 |
| `dmesg` | 显示内核的启动信息 |
| `elvtune` | 调整块设备的相互作用和性能 |
| `fdformat` | 低级格式化一张软盘 |
| `fdisk` | 磁盘分区管理程序 |
| `flock` | 得到一个文件锁，并根据状态执行一个命令 |
| `fsck.cramfs` | 对 Cramfs 文件系统的一致性进行检查 |
| `fsck.minix` | 对 Minix 文件系统的一致性进行检查 |
| `getopt` | 在给出的命令行进行选项和参数解析 |
| `hexdump` | 用用户指定的方式(包括 ASCII, 十进制, 十六进制, 八进制)显示一个文件或者标准输入的数据 |
| `hwclock` | 查询和设置硬件时钟(也被称为 RTC 或 BIOS 时钟) |
| `ipcrm` | 删除给定的进程间通信(IPC)资源 |
| `ipcs` | 提供 IPC 状态信息 |
| `isosize` | 报告 iso9660 文件系统的大小 |
| `line` | 单行拷贝 |
| `logger` | 设置系统日志的入口 |
| `look` | 显示以某个给定字符串开头的行 |
| `losetup` | 启动和控制回环(loop)设备 |
| `mcookie` | 为 `xauth` 生成 magic cookies (128 位的随机 16 进制数) |
| `mkfs` | 在一个设备(通常是一个硬盘分区)设备上建立文件系统 |
| `mkfs.bfs` | 创建一个 Santa Cruz Operations (SCO) bfs 文件系统 |
| `mkfs.cramfs` | 创建 cramfs 文件系统 |
| `mkfs.minix` | 创建 Minix 文件系统 |
| `mkswap` | 初始化指定设备或文件，以用做交换分区 |
| `more` | 分屏显示文件，但没有 less 好用 |
| `mount` | 把一个文件系统从一个设备挂载到一个目录 |
| `namei` | 显示指定路径的符号链接 |
| `pg` | 显示文本文件内容，一次显示一屏 |
| `pivot_root` | 使某个文件系统成为当前进程的根文件系统 |
| `ramsize` | 显示或者改变 RAM disk 的大小 |
| `raw` | 将一个原始的 Linux 字符设备绑定到一个块设备 |
| `rdev` | 查询和设置内核的根设备和其他信息 |
| `readprofile` | 显示内核侧写文件 /proc/profile 的信息 |
| `rename` | 对文件进行重命名 |
| `renice` | 修改正在运行进程的优先级 |
| `rev` | 颠倒一个文件每行字符的顺序 |
| `rootflags` | 在挂载根设备时查询和设置额外的信息 |
| `script` | 为终端会话过程建立一个 typescipt 文件，记录会话过程中终端的输出。 |
| `setfdprm` | 设置用户定义的软盘参数 |
| `setsid` | 在一个新的会话中运行程序 |
| `setterm` | 设置终端属性 |
| `sfdisk` | 磁盘分区表管理工具 |
| `swapoff` | 取消对指定交换设备和交换文件的使用 |
| `swapon` | 使指定的交换设备和交换文件生效 |
| `tailf` | 跟踪一个日志文件，显示日志的最后 10 行，并将日志中新的记录也显示出来。 |
| `tunelp` | 设置打印设备的参数 |
| `ul` | 用来将指定文件中出现的下划线使用指定终端画下横线的序列 |
| `umount` | 卸载一个被挂载的文件系统 |
| `vidmode` | 查询和设置视频模式 |
| `whereis` | 确定某命令二进制文件、源文件、手册文档的位置 |
| `write` | 发一个消息给另一个用户，*如果*他开启了 writting 的话。 |

# 6.57\. Vim-7.0

Vim 软件包包含一个强大的文本编辑器。

**预计编译时间：** 0.4 SBU**所需磁盘空间：** 47.4 MB

### Alternatives to Vim

如果你更喜欢使用其他的编辑器，比如 Emacs, Joe, Nano，请参考这里：[*http://www.linuxfromscratch.org/blfs/view/svn/postlfs/editors.html*](http://www.linuxfromscratch.org/blfs/view/svn/postlfs/editors.html) 获取安装方法。

## 6.57.1\. 安装 Vim

首先，将 `vim-7.0.tar.bz2` 和 `vim-7.0-lang.tar.gz`(可选) 解包到同一个目录下。然后应用几个 patch 来修正一些 Vim-7.0 的 bug：

```
patch -Np1 -i ../vim-7.0-fixes-7.patch 
```

这个版本的 Vim 会安装翻译过的 man 手册，并把它们放到 Man-DB 搜索不到的目录下。给 vim 打补丁来使其安装到可搜索目录里面，并允许 Man-DB 在运行的时候转换成希望的格式：

```
patch -Np1 -i ../vim-7.0-mandir-1.patch 
```

因为上面的 patch 中的一个，导致通过 Http 来下载拼写文件时候会出现问题。开发者解决了这个问题，应用下面的 patch 就可以解决：

```
patch -Np1 -i ../vim-7.0-spellfile-1.patch 
```

最后把配置文件 `vimrc` 的默认位置修改到 `/etc` 目录中：

```
echo '#define SYS_VIMRC_FILE "/etc/vimrc"' >> src/feature.h 
```

为编译 Vim 做准备：

```
./configure --prefix=/usr --enable-multibyte 
```

**配置选项的含义：**

*`--enable-multibyte`*

我们强烈推荐你启用该选项(虽然它是可选的)，因为它使得 Vim 可以支持使用多字节字符编码的文件，在一个使用多字节字符集的 locale 上，这是必需的。另外该选项还有助于编辑在默认使用 UTF-8 字符集的其它 Linux 发行版(如 Fedora Core)上创建的文本文件。

编译软件包：

```
make 
```

要测试结果，请运行：**`make test`** 。 注意，Vim 的测试套件会输出一大堆混乱的二进制字符到屏幕上，有时会把当前的终端设置搞乱，所以可以考虑将其输出重定向到一个日志文件。

安装软件包：

```
make install 
```

在 UTF-8 的 locale 下，`vimtutor` 程序会将教程从 ISO-8859-1 转化为 UTF-8。如果一些教程不是 ISO-8859-1 编码的，将会导致不可读。如果你解压了 `vim-7.0-lang.tar.gz` 包，并打算使用 UTF-8 的 locale，删除非 ISO-8859-1 编码的教程。英文版本的教程将会被安装：

```
rm -f /usr/share/vim/vim70/tutor/tutor.{gr,pl,ru,sk}
rm -f /usr/share/vim/vim70/tutor/tutor.??.* 
```

许多用户习惯于使用 `vi` 而不是 `vim` ，下面的命令为适应这种习惯创建一个二进制文件以及 man 文档的符号链接：

```
ln -sv vim /usr/bin/vi
for L in "" fr it pl ru; do
    ln -sv vim.1 /usr/share/man/$L/man1/vi.1
done 
```

默认情况下，Vim 的文档被安装在 `/usr/share/vim` 目录下。下面的符号链接允许通过 `/usr/share/doc/vim-7.0` 来访问，使其与其他软件包的文档保持一致：

```
ln -sv ../vim/vim70/doc /usr/share/doc/vim-7.0 
```

如果您计划在您的 LFS 系统上安装 X Window 系统，那么您可能必须在安装 X 之后重新编译 Vim 。Vim 有带有图形用户接口的版本，但需要安装 X 和其它一些库。要了解更多关于这方面的信息请参考 Vim 的安装手册和 BLFS 中有关 Vim 安装指导的页面： [*http://www.linuxfromscratch.org/blfs/view/svn/postlfs/editors.html#postlfs-editors-vim*](http://www.linuxfromscratch.org/blfs/view/svn/postlfs/editors.html#postlfs-editors-vim).

## 6.57.2\. 配置 Vim

在默认情况下，`vim` 是以与 vi 兼容的模式运行，这可能对使用过其它文本编辑器的人来说感到陌生。有些人可能喜欢这种模式，但是我们强烈建议使用 vim 模式运行 vim 。下面的配置文件明确的设置了"nocompatible"模式(也可以改用"compatible"模式)，这是必要的，因为如果要改变或覆盖其它设置，必须要在这个设置之后。使用下面的命令创建一个默认的 `vim` 配置文件：

```
cat > /etc/vimrc << "EOF"
" Begin /etc/vimrc

set nocompatible
set backspace=2
syntax on
if (&term =="iterm") || (&term =="putty")
  set background=dark
endif

" End /etc/vimrc
EOF 
```

*`set nocompatible`* 将使 `vim` 以比默认的 vi 兼容模式功能更强的方式运行。你可以去掉"no"来保持默认的 `vi` 模式。*`set backspace=2`* 让退格键能跨行、自动缩进、插入。*`syntax on`* 打开了 vim 的语法高亮功能。最后，带有 *`set background=dark`* 的 *if* 语句纠正了 `vim` 对于终端背景颜色的猜测。对于那些使用黑色背景的用户这是一个较好的配色方案。

使用下面的命令可以得到其它可以使用的选项的说明文档：

```
vim -c ':options' 
```

### 注意

默认情况下，Vim 仅会安装英文的拼写检查文件。要安装你熟悉的语言的拼写检查文件，从 *ftp://ftp.vim.org/pub/vim/runtime/spell/* 下载与你的语言相关的 `*.spl` 文件和 `*.sug` 文件（可选），以及字符解码器。把它们保存到 `/usr/share/vim/vim70/spell/` 目录下。

要使用这些拼写检查文件，在 `/etc/vimrc` 里面需要配置一些选项，例如：

```
set spelllang=en,ru
set spell 
```

要获取更多的信息，可以查看上面 URL 上的 README 文件。

## 6.57.3\. Vim 的内容

**安装的程序：** efm_filter.pl, efm_perl.pl, ex(→vim), less.sh, mve.awk, pltags.pl, ref, rview(→vim), rvim(→vim), shtags.pl, tcltags, vi(→vim), view(→vim), vim, vim132, vim2html.pl, vimdiff(→vim), vimm, vimspell.sh, vimtutor, xxd

### 简要描述

|  |  |
| --- | --- |
| `efm_filter.pl` | 能读取 `vim` 产生的错误的一个过滤器 |
| `efm_perl.pl` | 将 perl 解释器的错误信息重新格式化以用于 `vim` 的"quickfix"模式。 |
| `ex` | 以 ex 模式启动 `vim` |
| `less.sh` | 用 less.vim 起动 `vim` 的脚本 |
| `mve.awk` | 处理 `vim` 错误信息 |
| `pltags.pl` | 为 Perl 代码建立一个标记文件，以用于 `vim` |
| `ref` | 检查参数的拼写错误 |
| `rview` | 一个 `view` 的限制版，它不能启动任何 shell 命令并且 `view` 不能被挂起。 |
| `rvim` | 一个 `vim` 的限制版。它不能启动任何 shell 命令并且 `vim` 不能被挂起。 |
| `shtags.pl` | 为 perl 代码产生一个标志文件 |
| `tcltags` | 为 TCL 代码产生一个标志文件 |
| `view` | 以只读模式启动 `vim` |
| `vi` | `vim` 的一个链接 |
| `vim` | 编辑器 |
| `vim132` | 在 132 列模式下的终端中起动 `vim` |
| `vim2html.pl` | 将 vim 文档转化为 HTML 格式 |
| `vimdiff` | 使用 `vim` 同时编辑一个文档的 2 或 3 个版本并显示他们的区别使用 `vim` 同时编辑一个文档的 2 或 3 个版本并显示他们的区别 |
| `vimm` | 在一个远端终端上开启 DEC 定位输入模式 |
| `vimspell.sh` | 检查文件拼写并产生一个需要在 `vim` 中强调的语法报告。这个脚本需要 LFS 和 BLFS 都不提供的旧的 Unix `spell` 命令 |
| `vimtutor` | 教你使用 `vim` 的基本操作和命令 |
| `xxd` | 生成十六进制转储或者做相反的工作，因此它能给二进制文件打补丁。 |

# 6.58\. 关于调试符号

在缺省情况下，大多数程序和库都是带调试符号(使用 `gcc` 的 *`-g`* 选项)编译的。 当调试一个带调试符号的程序时，调试器不仅能给出内存地址，还能给出函数和变量的名字。

但是，这些调试符号明显地增大了程序和库。想知道这些调试符能带来多大的差异，请看下面的统计资料：

*   带调试符号的动态 `bash` 二进制文件：1200 KB

*   不带调试符号的动态 `bash` 二进制文件：480 KB

*   带调试符号的 Glibc 和 GCC 文件 (位于 `/lib` 和 `/usr/lib` 目录)：87 MB

*   不带调试符号的 Glibc 和 GCC 文件：16 MB

根据使用的编译器和连接动态程序的 C 库的版本的不同，文件的大小可能会有所不同，但是比较带调试符号与不带调试符号的程序的比较结果应该不会改变，大概是 2~5 倍大小。

由于大多数人都不会在系统软件上使用调试器，把这些符号去掉就能节省大量的空间。下一节将给您展示如何从程序和库文件中去除所有调试符号链接。附加的信息在系统优化信息里可以找到 [*http://www.linuxfromscratch.org/hints/downloads/files/optimization.txt*](http://www.linuxfromscratch.org/hints/downloads/files/optimization.txt)。

# 6.59\. 再次清理系统

如果编译此系统的用户不是一个程序员并且不打算在系统软件上做任何调试工作，系统大小可以通过从二进制包和库文件中删除调试链接削减大约 90 MB 的空间，这样做的代价是您以后将不能随时调试系统。

大多数人可以毫无困难地凭经验使用命令提示。但是，很容易因为一个打字错误就被报告新系统不能使用，因此在运行 `strip` 命令前，对系统当前数据做一个备份是一个不错的主意。

在执行清理命令之前，请特别注意确保正在运行的二进制文件不被清理。如果您不确定是否用户是进入在虚根环境（节 6.4, "进入 Chroot 环境"）下操作，请首先退出虚根环境：

```
logout 
```

接着用下面的命令再次进入：

```
chroot $LFS /tools/bin/env -i \
    HOME=/root TERM=$TERM PS1='\u:\w\$ ' \
    PATH=/bin:/usr/bin:/sbin:/usr/sbin \
    /tools/bin/bash --login 
```

现在二进制文件和库文件能安全地被清理了：

```
/tools/bin/find /{,usr/}{bin,lib,sbin} -type f \
  -exec /tools/bin/strip --strip-debug '{}' ';' 
```

很多文件将被报告说不能识别它们的文件格式，这些警告可以安全地忽略。这些警告只是表明那些文件是脚本而不是二进制文件。

如果硬盘空间非常紧张，这个 *`--strip-all`* 选项可以被用在二进制文件目录 `/{,usr/}{bin,sbin}` 中以获得更多的空间。不要在库文件里使用这个参数，因为这个参数将会破坏库文件。

# 6.60\. 最终的清理

从现在起，在退出后，每当重新进入 Chroot 环境，请使用下面修改过的 chroot 命令:

```
chroot "$LFS" /usr/bin/env -i \
    HOME=/root TERM="$TERM" PS1='\u:\w\$ ' \
    PATH=/bin:/usr/bin:/sbin:/usr/sbin \
    /bin/bash --login 
```

这样做的理由是起初在 `/tools` 目录下的程序已经不再需要了，这个目录可以删除以重新获得更多硬盘空间。在实际动手删除这个目录之前，请退出虚根环境并且使用上面修改过的命令重新进入虚根环境。还有一点，在删除 `/tools` 目录前，请将此目录打包压缩并且存储在一个安全的地方，以备以后编译另一个 LFS 系统。

### 注意

删除 `/tools` 目录将也会删除临时文件夹中已拷贝的 Tcl, Expect, DejaGNU 等文件,而这些文件是那些正在运行的工具链接测试程序使用的。如果要在以后使用这些程序，它们需要重新编译和重新安装，BLFS 手册介绍了如何重新安装(参见 [*http://www.linuxfromscratch.org/blfs/*](http://www.linuxfromscratch.org/blfs/))。

如果虚拟内核文件系统被卸载了，或者是重启系统了。确保虚拟内核文件系统在重新进入 chroot 环境时已经挂载了（参见 节 6.2.2, "挂载并填充 /dev 目录" 和 节 6.2.3, "挂载虚拟内核文件系统" ）。