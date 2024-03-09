# 序言

# i. 前言

我在 linux 上的冒险始于 1998 年，那时我下载并安装了我的第一个发行版。在用它工作了一段时间之后，我发现了很多我认为需要改进的问题。例如，我不喜欢启动脚本的排列顺序、某些程序的默认设置。我尝试过许多不同的发行版来解决这些问题，但是每个发行版都有各自的优点和缺点。最终，我意识到如果我想对我的 Linux 系统完全满意，我必须从头构建我自己的系统。

这是什么意思呢？我决心不用任何预先编译好的软件包，也不用可以安装基本系统的 CD-ROM 或启动盘。我将使用现有的 Linux 系统来开发自己定制的系统。这个"完美的" Linux 系统将拥有各种发行版的优点而没有它们的缺点。开始的时候，这个想法看起来是困难到令人感到畏惧的，但是我仍坚持这个想法，一个符合我特定需求的系统是可以构建起来的，并且不会建立一个标准却不符合我需求的系统。

在处理好诸如循环依赖和编译错误等各种问题之后，我创建了一个定制的 Linux 系统，这个系统功能完整并且适合我个人的需求。这个过程也使得我可以建立精简而紧凑的 Linux 系统，这样的系统比传统的发行版速度更快而且占用的空间更少。我称之为 Linux From Scratch 系统，或简称为 LFS 系统。

当我把我的目标和经验与 Linux 社区的其他成员分享的时候，很显然别人也有同样的想法。这样定制的 LFS 系统不仅可以满足用户的规范和需求，而且也给程序员和系统管理员们提供一了个理想的提高他们 Linux 技能的机会。由于有这样广泛的兴趣和需求，Linux From Scratch 项目诞生了。

这本 *Linux From Scratch* 指导书给读者提供了设计并构建自定义的 Linux 系统的背景知识和过程指导。本书的重点是 Linux From Scratch 这个项目以及使用 LFS 系统带来的好处。用户可以控制系统的所有特征，包括目录布局、脚本设置和安全设置等等。最终的系统将从源代码直接编译生成，用户可以指定在哪里安装、为什么安装以及怎样安装每一个程序。本书使得读者可以完全按照自己的需求定制他们的 Linux 系统，而且使用户对他们的系统有更多的控制权。

希望您在自己的 LFS 系统上工作愉快，享受真正属于*你自己*的系统所带来的各种好处。

[译者注] "From Scratch"是一个词组，它的意思是"从零做起，白手起家，从无到有"的意思，因此"Linux From Scratch"本质上不应当理解为一个发行版名称。它最贴切的含义应当是一种"方法/思想"：一切从源代码开始的方法/思想。

-- Gerard Beekmans gerard@linuxfromscratch.org

# ii. 目标读者

为什么要读这本书呢？有许多原因，最主要的原因是可以学习如何直接从源代码安装一个 linux 系统。许多人也许会问："当你可以下载和安装一个现成的 linux 系统时，为什么要如此麻烦地从源代码开始手动构建一个 linux 系统呢？"这是一个好问题，也是本书存在本节的原因。

LFS 存在的一个重要原因是可以帮助人们学习 linux 系统内部是如何工作的。构建一个 LFS 系统会帮助演示是什么使 linux 运转，各种组件如何在一起互相依赖的工作。最好的事情之一通过这种学习可以获得完全根据自己的需求定制 linux 系统的能力。

LFS 的一个关键的好处是它让用户对于系统有更多的控制，而不是依赖于他人的 linux 实现。在 LFS 的世界里，*你自己*坐在司机的位置，掌控系统的每一个细节，比如目录布局和启动脚本配置等等。你也能掌控在哪里、为何、以及怎样安装每一个程序。

LFS 的另一个好处是可以创建一个非常小巧的 linux 系统。当安装一个常规的发行版时，人们经常要被迫安装一些可能永远不会用到的程序。这些程序浪费宝贵的磁盘空间，或更糟的是占用 CPU 资源。要构建一个少于 100 兆(MB)的 LFS 系统并不困难，这比目前大多数的发行版要小很多。这听起来是不是仍然占用太多空间？我们中的一些人专注于创建非常小的嵌入式 LFS 系统。我们成功的构建了一个只运行 Apache 服务器的系统，大约占 8MB 磁盘空间。进一步的缩减能够减至 5MB 或更少。你用一个常规的发行版试试？！这也只是设计你自己的 linux 所带来的好处之一。[译者注]关于如何构建这样的 Apache 服务器系统的详情，请参见 youbest 兄的两篇大作"[做一个功能单一，体积小巧的 LFS](http://www.linuxsir.org/bbs/showthread.php?t=233228)[5M]"和"[我们可以做的更小！《功能单一，体积小巧的 LFS》续篇](http://www.linuxsir.org/bbs/showthread.php?t=236599)[600K]"。此外，本文译者金步国也有一篇文章[《DIY 一个实用的 mini-LAPP 服务器》[x86 版]](http://www.jinbuguo.com/lfs/minilapp-x86.html)，详细讲述了如果从源代码编译一个既实用又小巧的 Linux + Apache + PHP + PostgreSQL + OpenSSH + Iptables 服务器，如果你对服务器情有独钟，也很值得参考。

我们可以拿 linux 发行版与快餐店出售的汉堡打比喻，你不能决定自己正在吃的是什么。相反，LFS 没有给您一个汉堡。而是给您一张制作汉堡的配方。用户可以查阅配方，减掉不想要的配料，增加你自己的配料以增强汉堡的口味。当你对配方满意时，开始去制作它，您可以采用任何你喜欢的方式：或烤、或烘、或炸、或焙。

另外一个比方是把 LFS 与建筑房子比较。LFS 提供房子的框架蓝图，但是需要您自己去建筑它。LFS 包含了在这过程中调整计划的自由以及定制满足用户需求的参考。

自己定制 linux 系统的另一个好处是安全性。通过从源码编译整个系统，您能够审查任何东西，打上所有的安全补丁，而不需要等待别人编译好修补安全漏洞的二进制包。除非是您发现并制作的补丁，否则您无法确保新的二进制包一定被正确编译并修正了问题。

Linux From Scratch 的目标是构建一个完整的、可以使用的基础系统。不想构建自己的 linux 系统的读者，不会从本书中获益［译者不太赞同这句话，借用 d00m3d 的一句忠告："對任何想深入了解的 Linux 愛好者，不論你現用哪個發行版，最少都應該做一次 LFS ，一定會終身受用的。"］。如果您仅仅想了解计算机启动的时候做了什么，我们推荐您查看 "From Power Up To Bash Prompt" HOWTO 文档，中文版位于 [从按下电源开关到 bash 提示符](http://users.rsise.anu.edu.au/~okeefe/p2b/chinese/power2bash.html) ，英文原版位于 [*http://axiom.anu.edu.au/~okeefe/p2b/*](http://axiom.anu.edu.au/~okeefe/p2b/) 或者 linux 文档工程(TLDP)网站 [*http://www.tldp.org/HOWTO/From-PowerUp-To-Bash-Prompt-HOWTO.html*](http://www.tldp.org/HOWTO/From-PowerUp-To-Bash-Prompt-HOWTO.html) 。那个 HOWTO 构建了一个类似本书的系统，但是它的焦点仅仅限制在创建一个能够启动并进入到 BASH 提示符的系统。 想想您的目标，如果您想构建一个实用的 linux 系统并通过这种方式进行学习，那么本书是您的最佳选择。

构建您自己的 LFS 系统的若干原因以上都列出来了。本节只是冰山一角。随着您 LFS 经验的增长，您将会发现 LFS 真正带给您的信息和知识的力量。

# iii. 先决条件

创建 LFS 系统并不是一项非常简单的任务。它需要有一定的 Linux 系统管理知识，以便能够解决问题和正确执行命令。作为最低要求，读者必须具备使用命令行(shell)来运行 cp, mv, ls, cd 等命令的能力。我们还希望读者具备使用和安装 Linux 软件的基本知识[非必须]。

因为本书假定读者*至少*具备了上述技能，各个 LFS 论坛也不太可能涉及上述基础知识，你可能会发现关于上述基础知识的问题在这些论坛中无人理睬，或者仅仅得到一个指向下述 LFS 预备知识的连接。

在构建一个 LFS 系统之前，我们推荐您先阅读下列 HOWTO [看英文吃力的读者不看也没关系]：

*   Software-Building-HOWTO [*http://www.tldp.org/HOWTO/Software-Building-HOWTO.html*](http://www.tldp.org/HOWTO/Software-Building-HOWTO.html)

    这是一份详尽的关于在 Linux 下编译安装"一般的" Unix 软件的指南。

*   The Linux Users' Guide [*http://www.linuxhq.com/guides/LUG/guide.html*](http://www.linuxhq.com/guides/LUG/guide.html)

    这份指南涵盖了各类 Linux 软件的用法。

*   The Essential Pre-Reading Hint [*http://www.linuxfromscratch.org/hints/downloads/files/essential_prereading.txt*](http://www.linuxfromscratch.org/hints/downloads/files/essential_prereading.txt)

    这是一份专为 Linux 新手而写的 LFS 提示，包括一份链接列表，这些链接的信息源包含的主题非常广泛而且内容很棒。任何想安装 LFS 的人都应该对这份提示里的许多主题有所了解。

*   LFS 初学者注意事项 [*http://www.linuxsir.org/bbs/showthread.php?t=231446*](http://www.linuxsir.org/bbs/showthread.php?t=231446)

    这是 LinuxSir.Org 上 LFS 版主"终极幻想"专为 LFS 新手而写的注意事项，中文的哦！

# iv. 对宿主系统的要求

你的宿主系统应当安装了下列软件，并且不低于指定的版本号。这些要求对于大部分现在的 Linux 发行版来说不成问题。另外要注意的是许多发行版会将软件的头文件额外单独打包存放，常见的名称为"<包名称>-devel"或"<包名称>-dev"。如果你的发行版提供了这些包请务必确保已经安装了它们。

*   **Bash-2.05a**

*   **Binutils-2.12** (不推荐使用大于 2.16.1 的版本，因为尚未经过测试)

*   **Bzip2-1.0.2**

*   **Coreutils-5.0** (或者 Sh-Utils-2.0, Textutils-2.0, 和 Fileutils-4.1)

*   **Diffutils-2.8**

*   **Findutils-4.1.20**

*   **Gawk-3.0**

*   **Gcc-2.95.3** (不推荐使用大于 4.0.3 的版本，因为尚未经过测试)

*   **Glibc-2.2.5** (不推荐使用大于 2.3.6 的版本，因为尚未经过测试)

*   **Grep-2.5**

*   **Gzip-1.2.4**

*   **Linux Kernel-2.6.x** (必须是 GCC-3.0 以上版本编译的)

    对内核版本的这两个要求是因为：如果宿主系统的内核版本低于 2.6.2 或者不是用 GCC-3.0 以上版本编译的，那么 Binutils 将不能支持线程本地存储(thread-local storage)，同时 NPTL(本地 POSIX 线程库)的测试程序也会出现段错误。

    如果宿主系统的内核版本低于 2.6.2 或者不是用 GCC-3.0 以上版本编译的，您需要安装一个符合上述条件的新内核，然后用该新内核启动宿主系统。有两种方法可以解决这个问题。第一，如果你的 Linux 供应商提供这样的内核，你可以直接安装它；第二，如果你的 Linux 供应商不提供这样的内核或者你不想安装他们提供的内核，你可以自己编译一个内核。关于编译内核和配置引导管理器(假定宿主系统使用 GRUB)的指导，请查看第八章。

*   **Make-3.79.1**

*   **Patch-2.5.4**

*   **Sed-3.0.2**

*   **Tar-1.14**

为了确定宿主系统是否满足上述要求，运行下面的命令进行查看：

```
cat > version-check.sh << "EOF"
#!/bin/bash

# Simple script to list version numbers of critical development tools

bash --version | head -n1 | cut -d" " -f2-4
echo -n "Binutils: "; ld --version | head -n1 | cut -d" " -f3-4
bzip2 --version 2>&1 < /dev/null | head -n1 | cut -d" " -f1,6-8
echo -n "Coreutils: "; chown --version | head -n1 | cut -d")" -f2
diff --version | head -n1
find --version | head -n1
gawk --version | head -n1
gcc --version | head -n1
/lib/libc.so.6 | head -n1 | cut -d" " -f1-7
grep --version | head -n1
gzip --version | head -n1
cat /proc/version | head -n1 | cut -d" " -f1-3,5-7
make --version | head -n1
patch --version | head -n1
sed --version | head -n1
tar --version | head -n1

EOF

bash version-check.sh 
```

# v. 排版约定

为了易于理解和使用，一些排版约定在本书中通篇使用，本节包含其中一些排版格式的例子。

```
./configure --prefix=/usr 
```

这种样式的文本表明应该按照所看到的内容完整无误的输入，除非有另外的说明。这种样式也用在解释部分，来表明引用了哪些命令。

```
install-info: unknown option '--dir-file=/mnt/lfs/usr/info/dir' 
```

这种样式(固定宽度的文本)的文本表明它们是屏幕上输出的内容，可能是命令运行的结果，也有可能用来显示文件名，例如：`/etc/ld.so.conf` 。

*强调*

这种样式的文本在本书中有几种用途，但主要是用来强调重点。

[*http://www.linuxfromscratch.org/*](http://www.linuxfromscratch.org/)

这种样式用来显示超链接，不管链接的是 LFS 社区内部还是外部的页面，包括 HOWTO 、下载链接和网站。

```
cat > $LFS/etc/group << "EOF"
root:x:0:
bin:x:1:
......
EOF 
```

创建配置文件的时候使用这种样式，第一个命令让系统创建 `$LFS/etc/group` 文件，内容是接下来输入的每一行直到文件结束符(EOF)为止。因此，一般就是按照看到的内容整段输入。

*`<替代文本>`*

这种样式用来显示那些需要输入替代内容的文本或者是复制粘贴的内容。

*`[可选文本]`*

这种格式用来封装可选的文本。

`passwd(5)`

这种格式用来指向一个特定的手册页(以下简称 "man")。括号中的数字指定 `man` 中特定的章节。例如，`passwd` 有两个 man 页，按照 LFS 安装指令，分别位于 `/usr/share/man/man1/passwd.1` 和 `/usr/share/man/man5/passwd.5` ，这两个手册页的内容并不相同。本书中使用 `passwd(5)` 指代 `/usr/share/man/man5/passwd.5` 。`man passwd` 将会显示第一个匹配"passwd"的 man 页，也就是 `/usr/share/man/man1/passwd.1` 。在这个例子中，你可能需要运行 `man 5 passwd` 命令来指定你想要的 man 页。不过需要提醒一下的是大多数 man 页并不存在多个页面，也就是说 `man <程序名>` 通常就足够了。

# vi. 本书的组织结构

本书分为以下几个部分：

## Part I - 简介

Part I 解释了一些安装 LFS 过程中需要注意的重要事项，这部分还提供了本书的一些要点信息。

## Part II - 构建前的准备工作

Part II 描述了安装前的准备工作：准备一个新的分区、下载软件包和建立临时编译工具链。

## Part III - 构建 LFS 系统

Part III 引导读者逐步建立整个 LFS 系统：一个一个编译安装全部的软件包，配置启动脚本并安装内核。然后，可以在这个 Linux 基本系统上，根据需要编译安装其它软件包来扩展系统。本书的结尾，是一份易用的参考，列出了所有安装了的程序、库和重要的文件，另外还有一份软件包之间的依赖关系列表。

# vii. 勘误表

用于构建 LFS 系统的软件经常会被更新和加强。在 LFS 指导书发布以后也经常发布新的安全警告和 bug 修正。要检查本书中的软件包版本或者使用的指令是否需要更新或更改以堵上安全漏洞或修正 bug ，请查看这里：[*http://www.linuxfromscratch.org/lfs/errata/6.2/*](http://www.linuxfromscratch.org/lfs/errata/6.2/) 在继续前进之前，你应当将这里的更改应用到这本书相应的地方。