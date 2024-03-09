# 1\. 简介

# 1.1\. 如何构建一个 LFS 系统?

我们将用一个已安装好的 Linux 发行版(例如 Debian、Mandrake、Red Hat、SuSE)来构建 LFS 系统。这个已存在的 Linux 系统(宿主系统)将作为建立新系统的起点，提供包括编译器、连接器和 Shell 等创建新系统的必要工具。您安装这个发行版的时候，需要选择"development(开发/编程)"选项，以便可以使用这些工具。

另一个选择是使用 Linux From Scratch 的 LiveCD 。这个 CD 是一个非常好的宿主系统，它包含了构建一个完整 LFS 系统所需要的一切工具，另外还包含了所有的软件包源代码、补丁和一个本书的拷贝。使用这个 CD ，你可以不需要任何网络连接或者下载任何额外的东西。要了解更多关于 LFS LiveCD 的信息或者想下载它，请查看：[*http://www.linuxfromscratch.org/livecd/*](http://www.linuxfromscratch.org/livecd/) 。

第二章描述了怎样创建一个新的 Linux 本地分区和文件系统，新的 LFS 系统将在该分区上编译和安装。第三章解释了构建一个 LFS 系统需要那些软件包和补丁，以及怎样把它们存放到新文件系统上。第四章讨论了建立一个适当的工作环境。请仔细阅读第四章，因为它讨论了在开始第五章及其后面的步骤之前，开发者需要知道的几个重要问题。

第五章解释了形成一个基本开发套件(或称为工具链)所需的许多软件的安装，这个工具链将被用来构建第六章中的实际系统。其中一些软件包需要解决循环依赖关系：例如，要编译一个编译器，您首先就需要一个编译器。

第五章告诉用户如何第一遍编译工具链，包括 Binutils 和 GCC(第一遍主要的意思就是指这两个核心软件包后面还将第二次安装)。这些软件包里的程序将静态连接以便在使用时独立于宿主系统。接下来的步骤是编译 Glibc ，也就是 C 运行时库。Glibc 将由第一遍建立的工具链程序编译。然后将第二遍编译的工具链动态连接到刚刚编译好的 Glibc 库上。第五章余下的软件包将使用第二遍建立的工具链来编译。完成这些步骤之后，LFS 的安装过程除了正在运行的内核外，将不再依赖于宿主系统。

为了不依赖宿主系统，初看起来我们需要做很多工作。节 5.2,"工具链技术说明"提供了一个完整的技术说明，包括静态连接的程序和动态连接的程序之间的差异。

第六章将构建完整的 LFS 系统。**chroot**(改变 root)程序用来进入一个虚拟的环境并开始一个新的 shell，其根目录是 LFS 分区。这非常类似于重启并让内核将 LFS 分区挂载为根分区。系统实际上并没有重启，而是由 **chroot** 代替了，因为建立一个可启动的系统需要做一些现在还不需要做的额外工作。主要的好处在于"虚根环境(chrooting)"允许您在构建 LFS 的同时可以继续使用宿主系统。在等待软件包编译完成的时候，用户可以切换到不同的虚拟控制台(VC)或者 X 桌面，就像平常一样继续使用计算机。

为了完成安装，第七章设置启动脚本，第八章安装内核和启动引导程序。第九章包含在本书之外获得进一步 LFS 体验的信息。在本书中所有步骤都完成之后，计算机就已经准备好重启进入新的 LFS 系统了。

以上就是概略的过程，在接下来的章节和软件包描述中会讨论每一步的细节。看似复杂的项目将详细阐明，随着读者(就是你啦)踏上 LFS 冒险之路，每一件事情都将依序出现。

## 1.2\. 如何提高 LFS 制作的成功率?

新手第一次做 LFS 的时候通常会遇见各种各样的问题，有许多人就此止步了，这不能不说是一个遗憾。热心的 youbest 兄有一篇大作专门针对新手经常遇见的问题，给出了[如何提高 LFS 的成功率以及部分问题的解决方法](http://www.linuxsir.org/bbs/showthread.php?t=252928)，建议大家在制作过程中参考。

## 1.3\. 在制作中途关闭计算机以后怎么办?

LFS 的制作过程是相当漫长的，特别是对机器不太好的朋友，有时候不得不中途关机，但对一些不太清楚 LFS 的工作原理的朋友，可能重新开机以后无法正确的恢复到先前的工作状态，因此为了能成功的完成 LFS 全过程，不得不持续开机几十个小时。幸好 youbest 兄有一篇大作解决了这个头疼的问题，给出了[制作 LFS 过程中各个阶段恢复工作状态的详细方法(适合 LFS6.3)](http://www.linuxsir.org/bbs/thread322895.html)，虽然是基于 LFS 6.1 版本的，但仍然具有很实用的参考价值。

# 1.2\. 与上一版本有何不同?

下面是本书自上一个版本到当前版本的更新列表。

**升级到：**

*   Automake 1.9.6

*   Bash 3.1

*   Binutils 2.16.1

*   Bison 2.2

*   Coreutils 5.96

*   E2fsprogs 1.39

*   File 4.17

*   Findutils 4.2.27

*   Flex 2.5.33

*   Gawk 3.1.5

*   GCC 4.0.3

*   Gettext 0.14.5

*   Glibc 2.3.6

*   GRUB 0.97

*   IANA-Etc 2.10

*   IPRoute2 2.6.16-060323

*   Less 394

*   LFS-Bootscripts 6.2

*   Libtool 1.5.22

*   Linux 2.6.16.27

*   Linux-Libc-Headers 2.6.12.0

*   M4 1.4.4

*   Man-pages 2.34

*   Ncurses 5.5

*   Perl 5.8.8

*   Procps 3.2.6

*   Psmisc 22.2

*   Readline 5.1

*   Sed 4.1.5

*   Shadow 4.0.15

*   TCL 8.4.13

*   Udev 096

*   Vim 7.0

*   Zlib 1.2.3

**降级到：**

*   Groff 1.18.1.1

**新增加：**

*   bash-3.1-fixes-8.patch

*   Berkeley DB-4.4.20

*   bzip2-1.0.3-bzgrep_security-1.patch

*   bzip2-1.0.3-install_docs-1.patch

*   db-4.4.20-trap-1.patch

*   gawk-3.1.5-segfault_fix-1.patch

*   gcc-4.0.3-specs-1.patch

*   glibc-2.3.6-inotify-1.patch

*   glibc-2.3.6-linux_types-1.patch

*   groff-1.18.1.1-debian_fixes-1.patch

*   inetutils-1.4.2-gcc4_fixes-3.patch

*   kbd-1.12-gcc4_fixes-1.patch

*   linux-libc-headers-2.6.12.0-inotify-3.patch

*   MAN-DB-2.4.3

*   mktemp-1.5-add_tempfile-3.patch

*   module-init-tools-3.2.2-modprobe-1.patch

*   perl-5.8.8-libc-2.patch

*   readline-5.1-fixes-3.patch

*   tar-1.15.1-gcc4_fix_tests-1.patch

*   texinfo-4.8-tempfile_fix-2.patch

*   udev-config-6.2

*   vim-7.0-fixes-7.patch

*   vim-7.0-mandir-1.patch

*   vim-7.0-spellfile-1.patch

**删除了：**

*   flex-2.5.31-debian_fixes-3.patch

*   gcc-3.4.3-linkonce-1.patch

*   gcc-3.4.3-no_fixincludes-1.patch

*   gcc-3.4.3-specs-2.patch

*   glibc-2.3.4-fix_test-1.patch

*   hotplug-2004-09-23

*   inetutils-1.4.2-kernel_headers-1.patch

*   iproute2-2.6.11-050330-remove_db-1.patch

*   Man-1.6b

*   mktemp-1.5-add_tempfile-2.patch

*   perl-5.8.6-libc-1.patch

*   udev-config-4.rules

*   vim-6.3-security_fix-1.patch

*   zlib-1.2.2-security_fix-1.patch

# 1.3\. 更新日志

这是 6.2 版本的 Linux From Scratch 指导书，2006 年 8 月 1 日发布。如果本书的版本过期了六个月以上，可能已经有更新更好的版本可供下载了。要获得更新的版本，请访问 [*http://www.linuxfromscratch.org/mirrors.html*](http://www.linuxfromscratch.org/mirrors.html)。

下面是本书自上一个版本到当前版本的详细更新日志。

**Changelog Entries:**

*   August 2, 2006

    *   [dnicholson] - Added to the list of acceptable features in ext3 file systems. Thanks to George Gowers.
*   August 1, 2006

    *   [dnicholson] - Added text describing a potential failure in the E2fsprogs testsuite when there is not enough memory available and suggest enabling swap space to address this. Also added an explicit `swapon` to the Chapter 2 mounting instructions to ensure that the user has enabled their swap space if desired. Thanks to Nathan Coulson and Alexander Patrakov.

    *   [dnicholson] - Added text warning that the Udev testsuite will produce messages in the host's logs. Fixes #1846\. Thanks to Archaic.

    *   [dnicholson] - Finished adding system inotify support. Split the patch so that the syscall functions are part of the Glibc installation. Thanks to Alexander Patrakov for supplying the proper syscall bits.

*   July 31, 2006

    *   [bdubbs] - Added a patch vim to fix a spellfile download problem. Thanks to Alexander Patrakov.
*   July 30, 2006

    *   [bdubbs] - Added notes that udev does not recognize a backslash for line continuation.

    *   [bdubbs] - Expanded the note in vim to better explain spell files.

*   July 29, 2006

    *   [bdubbs] - Added a patch to the linux-libc-headers to add the inotify header.

    *   [bdubbs] - Added a patch to Berkeley DB to avoid potential program traps.

*   July 21, 2006

    *   [bdubbs] - Added the existing bash patch to 第五章 to avoid potential custom scripting problems.

    *   [bdubbs] - Added grub-0.97-disk_geometry-1.patch.

    *   [bdubbs] - Updated to linux-2.6.16.27\. Added a note to use the latest kernel version available in the 2.6.16 series.

    *   [bdubbs] - Updated vim patch set to level 7.

    *   [bdubbs] - Updated the discussion concerning zimezones.

    *   [dnicholson] - Added a reminder to check that the virtual kernel file systems are mounted after the description of the revised chroot command.

    *   [dnicholson] - Fixed dead link to Linux Driver Model paper on the Device and Module Handling page. Replaced with sysfs paper by the same author. Thanks to Chris Staub and Bryan Kadzban.

*   July 18, 2006

    *   [bdubbs] - Several textual corrections. Thanks to Chris Staub.
*   July 15, 2006

    *   [bdubbs] - Added a patch to module-init-tools to correct a possible problem when aliases are specified with regular expressions.

    *   [bdubbs] - Updated the kernel to version 2.6.16.26.

    *   [bdubbs] - Added sed to correct path to the find program in updatedb after moving find to /bin.

    *   [bdubbs] - Updated text concerning test failures in glibc to describe the most recent results.

*   July 13, 2006

    *   [bdubbs] - Moved the executables: nice, find, kbd_mode, openvt, and setfont to /bin to support boot scripts. Added --datadir=/lib/kbd to kbd's configure so that keyboard data will always be on the root partition.

    *   [bdubbs] - Updated text in section 7.9 (Bash Shell 启动文件) to better explain the Xlib example.

*   July 12, 20006

    *   [bdubbs] - Updated to man-pages-2.34.

    *   [bdubbs] - Updated to e2fsprogs-1.39.

    *   [dnicholson] - Changed text explaining the installation of the udev-config rules. Thanks to Matthew Burgess.

    *   [dnicholson] - Various fixes and additions for examples of custom rules in Udev courtesy of Alexander Patrakov. Added the "Creating custom symlinks" page which includes examples of creating persistent device symlinks, including CD-ROMs. Added a second set of guidelines for creating persistent symlinks for network cards. Other text touch ups on the configuration pages involving Udev. Closes ticket #1818.

    *   [bdubbs] - Updated udev-config and bootscripts download location.

    *   [dnicholson] - Added commands to create the vi to vim man page symlink in all available languages. Closes ticket #1811\. Thanks to Alexander Patrakov.

    *   [dnicholson] - Updated to udev-096 and udev-config-20060712\. Removed the bug.c program and the cd symlinks script. The cd symlinks will be covered in 第七章 Closes ticket #1804\. Thanks to Alexander Patrakov for making the appropriate changes in the Udev rules.

*   July 11, 2006

    *   [bdubbs] - Changed url for the SBU pages to a generic location.

    *   [bdubbs] - Added clarifying text to section 7.9 concerning charmap specifications. Thanks to Dan Nicholson. Closes ticket #1813.

    *   [bdubbs] - Moved text in section 5.7 "调整工具链" referencing TCL out of the caution and into its own note so it does not get included later in gcc-pass2\. Closes ticket #1822.

    *   [bdubbs] - Updated the kernel to version 2.6.16.24\. Closes ticket #1808.

*   July 10, 2006

    *   [dnicholson] - Specified the full path to `modprobe` in the example modprobe rule. Closes ticket #1812.

    *   [dnicholson] - Remove the `locale country` command from the heuristic to determine the locale in Bash Shell 启动文件 since it doesn't produce results in all locales.

*   July 7, 2006

    *   [matt] - Updated module-init-tools download information as it has a new maintainer.
*   June 10, 2006

    *   [ken] - Added `gettext.sh` to list of programs installed by gettext, similarly `nologin` for shadow, `grub-set-default` for grub, `enc2xs` and `instmodsh` for perl, `slabtop` for procps, `flock` and `tailf` for util-linux, `bootlogd` for sysvinit, `manpath` for man-db, `filefrag` for e2fsprogs. Thanks to Chris Staub for the patch.
*   May 31, 2006

    *   [matthew] - Upgrade to Linux-2.6.16.19.

    *   [matthew] - Upgrade to Man-pages-2.33.

    *   [matthew] - Upgrade to Bison-2.2.

    *   [matthew] - Upgrade to Coreutils-5.96.

    *   [gerard] - Added `tee` to chapter 6's Glibc `make check` so the output can be seen on screen as well as captured in the log file.

*   May 30, 2006

    *   [matthew] - Removed an out of date comment regarding having to run `pwconv` to reset passwords after enabling password shadowing. Thanks to Chris Staub for the report.

    *   [matthew] - Removed `getunimap`, `setlogons`, and `setvesablank` from the list of programs installed by kbd. Thanks to Chris Staub for the patch.

*   May 30, 2006

    *   [matthew] - Removed `swapdev` from the list of files installed by util-linux. Thanks to Chris Staub for the patch.
*   May 27, 2006

    *   [jhuntwork] - Remove the 'refer back's in the gcc-pass2 and chapter06/gcc pages. Better organizes the commands and data so that the flow of the book is not lost.

    *   [jhuntwork] - Add a note about installing spell files for Vim in a language other than English.

    *   [jhuntwork] - Correct Vim's installation of man pages to work well with Man-DB. Patch from Alexander Patrakov and Ag Hatzim.

*   May 26, 2006

    *   [jhuntwork] - Some version corrections in the vim page.
*   May 25, 2006

    *   [jhuntwork] - Updated to Vim-7.0\. Fixes #1793.

    *   [jhuntwork] - Fixed generation of diff's man page. Thanks Randy McMurchy for the report and Ken Moffat for the fix. Fixes #1800.

*   May 22, 2006

    *   [jim] - Fixed a constant support question asked in #IRC and the mailing lists about shadow's additional sed command for cracklib. Using a complete sed command instead.
*   May 15, 2006

    *   [archaic] - Updated to udev-config-20060515\. This adds the rule to create /dev/usb nodes as well as making the rules files slightly more modular by reorganizing which rules go to which files. This is a very minor update.

    *   [archaic] - Updated to man-pages-2.32.

    *   [archaic] - Updated to udev-092.

*   May 14, 2006

    *   [manuel] - Updated SBU and disk usage values.

    *   [manuel] - Created packages.ent. Moved data about packages to packages.ent as entities.

*   May 12, 2006

    *   [archaic] - Updated to linux-2.6.16.16.
*   May 9, 2006

    *   [manuel] - Updated packages and patches sizes.
*   May 8, 2006

    *   [archaic] - Made the directory tree creation more concise and removed the extraneous /opt/* hierarchy (it is not required by FHS). Closes ticket #1656.
*   May 7, 2006

    *   [archaic] - Updated to linux-2.6.16.14.

    *   [ken] - Use ext3 filesystem instead of ext2\. Resolves ticket #1792.

*   May 6, 2006

    *   [jhuntwork] - Added MD5 sums for packages and patches. Resolves ticket #1788.
*   May 3, 2006

    *   [archaic] - Upgraded to linux-2.6.16.13.

    *   [jhuntwork] - Updated stripping notes to reflect current findings. Resolves ticket #1657.

    *   [archaic] - Updated the bug.c code to avoid USB-related uevent leakage reports.

*   May 2, 2006

    *   [jhuntwork] - Fixed sanity checks to work after final GCC and changed their format. Resolves ticket #1768.

    *   [archaic] - Removed mention of usbfs from the fstab page since it is already covered in BLFS.

    *   [archaic] - Updated to man-pages-2.31.

    *   [archaic] - Updated to iana-etc-2.10.

    *   [archaic] - Updated to tcl8.4.13.

*   May 1, 2006

    *   [archaic] - Added two seds to avoid symlink problems with Readline during reinstallation. Thanks to Dan and Manuel for the fix and for testing. Fixes ticket #1770.

    *   [archaic] - Fixed issue where module-init-tools would not re-install its binaries.

    *   [archaic] - Updated to linux-2.6.16.11.

    *   [archaic] - Updated to udev-091\. Moved to a tarball-based set of udev rules. Updated the bootscripts to support the new udevsettle program.

*   April 27, 2006

    *   [manuel] - Added SEO Company Canada to donators acknowledgements.
*   April 23, 2006

    *   [manuel] - Fixed command to change $LFS/tools ownership. Resolves ticket #1780.
*   April 22, 2006

    *   [manuel] - Revised again the 对宿主系统的要求 page wording and look. Thanks to Bruce Dubbs for the patch. Resolves ticket #1779.
*   April 21, 2006

    *   [manuel] - Added commands to determine the version of the required packages installed on the host. Thanks to Bruce Dubbs for the commands list and Randy McMurchy for reviewing the wording.

    *   [manuel] - Alphabetized patches list. Thanks to Justin R. Knierim for the patch.

*   April 20, 2006

    *   [jhuntwork] - Updated bash to 3.1.17 via an updated patch. Resolves Ticket 1775.

    *   [manuel] - Reworded why a 2.6 kernel compiled with GCC-3 is required on the host system.

    *   [manuel] - Revised dependencies info. Thanks to Chris Staub for the patch.

*   April 19, 2006

    *   [jhuntwork] - Added a more detailed list of minimum software requirements. Thanks to Chris Staub for researching these and Alexander Patrakov for suggesting the enhancement. Resolves Ticket 1598.
*   April 18, 2006

    *   [jhuntwork] - Moved all dependency information to a new page, Appendix C. Appendix C also contains information concerning the build order. While there might need to be a few tweaks yet, this information is complete enough at this point to close out the long-standing ticket #684\. Many thanks to Chris Staub, Dan Nicholson and Manuel Canales Esparcia for helping get this finished.
*   April 15, 2006

    *   [archaic] - Updated to lfs-bootscripts-20060415.

    *   [archaic] - Added patch to glibc to fix build errors in packages that include linux/types.h after sys/kd.h.

*   April 14, 2006

    *   [ken] - Add security patch for tar to address CVE-2006-0300.

    *   [archaic] - Upgraded to man-pages-2.29 and linux-2.6.16.5\. No command changes.

    *   [manuel] - Changed typography conventions. From now, replaceable text is encapsulated inside < >, optional text inside [ ], and library extensions inside { }. Thanks to Bruce Dubbs for the patch.

*   April 13, 2006

    *   [archaic] - Removed boot logging rule from /etc/syslog.conf and removed the command to move logger to /bin.

    *   [archaic] - Added symlink from vim.1 to vi.1.

    *   [archaic] - Added chgpasswd to the list of installed files for Shadow.

    *   [archaic] - Merged the udev_update branch to trunk.

*   April 12, 2006

    *   [jhuntwork] - Rewrote section explaining IP Addresses. Thanks Bryan Kadzban and Bruce Dubbs. Resolves Ticket 1663.

    *   [jhuntwork] - Added a pointer to GDBM in Berkeley DB page. Also added explanatory text concerning why LFS chose Debian's convention for storing man pages. Thanks to Tushar Teredesai and Alexander Patrakov. Resolves Ticket 1694.

    *   [jhuntwork] - Remove symlink of zsoelim to groff's soelim in chapter 6\. Man-DB produces a sufficient zsoelim which overwrites the symlink we used to create.

*   April 11, 2006

    *   [jhuntwork] - Updated bash-3.1 patch. (Ticket 1758)
*   April 8, 2006

    *   [jhuntwork] - Added a command to create an empty /etc/mtab file early in chapter 6\. This avoids testsuite failures in e2fsprogs and possibly other programs that expect /etc/mtab to be present. Explanation from Dan Nicholson, slightly modified. Also merged the 'Creating Essential Symlinks' section with 'Creating passwd, group and log Files'.
*   April 6, 2006

    *   [manuel] - Placed home page (when available) and full download links for all packages in chapter03/packages.xml.

    *   [jhuntwork] - Merged alphabetical branch to trunk.

*   April 2, 2006

    *   [archaic] - Moved the chowning of /tools to the end of chapter 5 and rewrote note about backing up or re-using /tools. Moved the mounting of kernel filesystems before the package management page and rewrote the page to mount --bind /dev and mount all other kernel filesystems while outside chroot. Rewrote note about re-entering chroot and remounting kernel filesystems. Removed /dev from the list of dirs created in chroot and added it before chroot.
*   March 30, 2006

    *   [ken] - Correct my erroneous comment about UTF-8 locales in Man-DB. Thanks to Alexander for explaining it.

    *   [ken] - upgraded to Linux-2.6.16.1, Iproute2-2.6.16-060323, and Udev-088.

*   March 29, 2006

    *   [ken] - Upgrade to shadow-4.0.15 and add convert-mans script to convert its UTF-8 man pages. Thanks to Alexander and Archaic for the script and commands. Fixes tickets #1748 and #1750.
*   March 22, 2006

    *   [archaic] - Updated to lfs-bootscripts-udev_update-20060321.
*   March 21, 2006

    *   [archaic] - Updated the bootscripts. Removed references to hotplug and the bootscripts udev patch. Removed reference to udevstart. Added text and commands for generating Udev bug reports.
*   March 18, 2006

    *   [matthew] - Do not run configure manually for iproute2 as it is run automatically by the Makefile. Thanks to Chris Staub for the patch. Fixes ticket #1734.

    *   [matthew] - Make bzdiff use mktemp instead of the deprecated tempfile command. Thanks to Chris Staub for the patch. Fixes ticket #1713.

    *   [matthew] - Upgrade to flex-2.5.33.

    *   [matthew] - Upgrade to readline-5.1.004.

    *   [matthew] - Upgrade to bash-3.1.014.

    *   [matthew] - Upgrade to psmisc-22.2.

    *   [matthew] - Upgrade to file-4.17.

    *   [matthew] - Use updated version of the coreutils suppression patch to prevent coreutils version of the su man page from being installed. Fixes #1690.

*   March 11, 2006

    *   [matthew] - Upgrade to GCC 4.0.3.
*   March 8, 2006

    *   [matthew] - Upgrade to Man-pages 2.25.

    *   [matthew] - Remove an example of poor Udev support as it does not apply to the kernel used in the book. Thanks to Alexander Patrakov.

    *   [matthew] - Upgrade to Linux 2.6.15.6.

    *   [matthew] - Upgrade to udev-087.

    *   [matthew] - Udev's run_program rules require a null device to be present at an early stage, so create one in /lib/udev/devices.

*   March 7, 2006

    *   [matthew] - Update Udev rules file to load SCSI modules and upload firmware to devices that need it. Improve explanations of device and module handling. Thanks to Alexander Patrakov.

    *   [archaic] - Replaced the debian-specific groff patch with an LFS-style patch.

*   March 3, 2006

    *   [gerard] - Remove -D_GNU_SOURCE from chapter 5 - Patch. Thanks to Dan Nicholson for the patch.
*   March 1, 2006

    *   [archaic] - Create the Udev directories before creating the symlinks.

    *   [jhuntwork] - Added a description of perl configure flags that help perl deal with a lack of groff. Thanks Dan Nicholson.

*   February 27, 2006

    *   [archaic] - New bash fixes patch adds patch 011 from Bash upstream. Bash patch 010 broke quoting in certain situations.
*   February 20, 2006

    *   [matthew] - Use non-deprecated format for accessing MODALIAS keys in the Udev rules file, and prevent the "$" from being expanded by the shell.

    *   [matthew] - Add patches 009 and 010 from Bash upstream.

    *   [matthew] - Upgrade to Man-pages 2.24.

*   February 19, 2006

    *   [matthew] - Upgrade Perl libc patch to prevent Perl from trying to find headers on the host system. Fixes bug 1695.

    *   [matthew] - Expand the Udev module handling rule to run for every subsystem, not just USB.

    *   [matthew] - Upgrade to Linux 2.6.15.4.

    *   [matthew] - Upgrade to Udev 085.

    *   [matthew] - Install Sed's HTML documentation by using --enable-html instead of editing the Makefile. Thanks to Greg Schafer for the report and the fix.

    *   [matthew] - Add upstream fixes 001-002 for Readline.

    *   [matthew] - Add upstream fixes 001-008 for Bash.

    *   [matthew] - Upgrade to Sed 4.1.5.

    *   [matthew] - Upgrade to Man-pages 2.23.

    *   [matthew] - Upgrade to Coreutils-5.94.

    *   [matthew] - Upgrade to DB-4.4.20.

    *   [matthew] - Upgrade to Perl-5.8.8, removing the now unneeded vulnerability and DB module patches.

    *   [matthew] - Add the verbose parameter to a couple of commands in Linux-Libc-Headers and DB.

    *   [matthew] - Create udev specific directories in udev's instructions instead of the more generic creatingdirs.xml. Add "pts" and "shm" directories to `/lib/udev/devices` so that they can be mounted successfully at boot time.

*   February 10, 2006

    *   [manuel] - Finished the XML indentation plus few tags changes.
*   February 8, 2006

    *   [matthew] - Rewrite the majority of chapter07/udev.xml to reflect the new configuration for handling dynamic device naming and module loading.
*   February 3, 2006

    *   [matthew] - Create the `/lib/firmware` directory that can be used by Udev's `firmware_helper` utility.

    *   [matthew] - Add descriptions of Udev's helper binaries.

    *   [manuel] - Add udev bootscript patch to whatsnew. Removed hotplug from list of packages to download.

    *   [ken] - Add udev bootscript patch to list of patches to download.

    *   [ken] - Correct the size of the udev tarball.

*   February 2, 2006

    *   [matthew] - Upgrade to Udev-084 and build all its extras to enable custom rules to be written more easily. Also, change the rules file to handle kernel module loading and patch the udev bootscript to work with this version of udev.

    *   [matthew] - Remove the hotplug package and related bootscript. Udev will now handle device creation and module loading.

    *   [matthew] - Upgrade to Linux-2.6.15.2.

*   January 30, 2006

    *   [jhuntwork] - Adjust binutils-pass1 so we don't need to hang on to its source directories. Also use 'gcc -dumpmachine' instead of the MACHTYPE var.

    *   [jhuntwork] - Various textual corrections. Thanks Chris Staub.

    *   [jhuntwork] - Remove unnecessary LDFLAGS variables in binutils pass 1 and 2\. Thanks Dan Nicholson.

*   January 29, 2006

    *   [jhuntwork] - Restore the use of *startfile_prefix_spec.

    *   [jhuntwork] - Remove a spurious -i from the perl command when 再次调整工具链. Thanks Dan Nicholson.

*   January 26, 2006

    *   [jhuntwork] - Modify chapter 6 Glibc's make install command to allow test-installation.pl to run.

    *   [jhuntwork] - Adjust chapter 5 binutils to build a static ld-new for use in the chapter 6 readjusting section. Also add some extended sanity checks. These fixes are adapted from DIY-Linux and Greg Schafer. Thanks to Dan Nicholson for the report, as well.

    *   [jhuntwork] - Added 'nodump' to commands in the 包管理 section.

*   January 25, 2006

    *   [jhuntwork] - Remove ppc specific instructions from chapter 6 patch. Cross-LFS can handle non-x86 arch specifics at this point.

    *   [jhuntwork] - Fix chapter 6 Glibc's test-installation.pl to test the correct Glibc. Fixes bug 1675\. Thanks to Dan Nicholson for the report and Greg Schafer for the fix.

    *   [jhuntwork] - Fixed the re-adjusting of the toolchain in chapter 6 so that chapter 6 GCC and Binutils links against the proper Glibc and so that we don't have to keep the binutils directories from chapter 5\. Also moved a note about saving the /tools directory to the beginning of chapter 6\. Fixes bug 1677\. Thanks to Chris Staub, Alexander Patrakov, Greg Schafer and Tushar Teredesai for reporting and resolving this issue.

    *   [matthew] - Upgrade coreutils i18n patch to version 2 to fix `sort -n` and add the en_US.UTF-8 locale to improve coreutils' test coverage. Fixes bugs 1688 and 1689\. Thanks to Alexander Patrakov.

    *   [matthew] - Add information about package management. Thanks to the BLFS project for the text.

*   January 24, 2006

    *   [matthew] - Upgrade to Groff-1.18.1.1-11.
*   January 23, 2006

    *   [matthew] - Upgrade to Man-pages 2.21.

    *   [matthew] - Upgrade to Psmisc 22.1.

    *   [matthew] - Upgrade to Shadow 4.0.14.

    *   [matthew] - Install documentation for the Linux kernel. Thanks to Tushar for the report. Fixes bug 1683.

    *   [matthew] - Added a patch to enable Perl's DB_File module to compile with the latest version of Berkeley DB. Thanks to Alexander Patrakov for the patch.

*   January 20, 2006

    *   [jhuntwork] - Added a patch to fix the sprintf security vulnerability in Perl. Thanks to Tim van der Molen for pointing it out.
*   January 17, 2006

    *   [jhuntwork] - Fixed locale generation for French UTF-8\. Thanks to Dan McGhee for the report and Alexander Patrakov for the fix.
*   January 10, 2006

    *   [ken]: Define YYENABLE_NLS in bison, to resolve a code difference shown up by Iterative Comparison Analysis. Thanks to Greg Schafer.

    *   [ken]: Revert my move of mktemp and add a sed to correct gccbug.

*   January 7, 2006

    *   [ken]: Alter the Perl instructions to always create an /etc/hosts file. This fixes a potential difference in the 'hostcat' recorded in Config_heavy.pl. Thanks to Bryan Kadzban for explaining this.

    *   [ken]: Move grep ahead of libtool, so that the latter will correctly reference /bin/grep in references to EGREP.

    *   [ken]: Move mktemp ahead of gcc so that gccbug will use mktemp.

    *   [ken]: Give Berkeley DB its full name, and remove the '-lpthread' overrides. Also add pointer to BLFS, thanks to Randy McMurchy.

*   January 5, 2006

    *   [jhuntwork]: Remove mention of news server until we actually have one. Thanks Randy.

    *   [jhuntwork]: Initial addition of UTF-8 support. Thanks to Alexander Patrakov.

*   January 3, 2006

    *   [matt]: Clarify the description of mktemp's --with-libc configure parameter (fixes bug 1667).

    *   [matt]: Upgrade to libtool 1.5.22.

    *   [matt]: Upgrade to man-pages 2.18.

    *   [matt]: Remove the -v flag from the example mkswap command in chapter 2 as it does not affect verbosity (fixes bug 1674).

*   December 31, 2005

    *   [ken]: Alter installation of Linux Libc asm Headers in chroot, to be repeatable.
*   December 23, 2005

    *   [jim]: Corrected version on Vim symlink
*   December 21, 2005

    *   [matt]: Correctly symlink Vim's documentation to /usr/share/doc. Thanks to Jeremy for the report and the fix.
*   December 17, 2005

    *   [matt]: Pass a valid path to module-init-tools' --prefix configure switch and remove the now unnecessary --mandir switch

    *   [matt]: Symlink Vim's documentation to /usr/share/doc. Fixes bug 1610\. Thanks to Randy McMurchy for the original report and to Ken and Jeremy for their investigations into the fix.

    *   [matt]: Upgrade to psmisc-21.9

    *   [matt]: Upgrade to man-pages-2.17

*   December 16, 2005

    *   [jhuntwork]: Move Procps to before Perl in chapter 6\. Perl's testsuite uses 'ps'.
*   December 13, 2005

    *   [jhuntwork]: Install Tcl's internal headers to /tools/include, allowing us to drop its source directory right away. Origin is Greg Schafer, and thanks to Dan Nicholson for the report (fixes bug 1670).
*   December 12, 2005

    *   [jhuntwork]: Updated texinfo patch. Fixes segfault issues with texindex. Thanks to Randy McMurchy for the report and Bruce Dubbs and Joe Ciccone for the fix.
*   December 11, 2005

    *   [jhuntwork]: Upgrade to tcl-8.4.12

    *   [jhuntwork]: Upgrade to less-394.

    *   [jhuntwork]: Upgrade to readline-5.1\. Also removed bash-3.0 and readline-5.0 specific patches.

    *   [jhuntwork]: Upgrade to bash-3.1\. Also fixed Tcl to work with the new bash version. Thanks to Alexander Patrakov and ultimately, Greg Schafer for the fix.

    *   [jhuntwork]: Changed variable used in readline for linking in ncurses. Thanks to Alexander Patrakov for the fix.

*   December 9, 2005

    *   [matt]: Upgrade to man-pages-2.16

    *   [matt]: Upgrade to module-init-tools-3.2.2

    *   [matt]: Upgrade to findutils-4.2.27

*   December 7, 2005

    *   [matt]: Mention the testsuites (or lack of them) for all packages (fixes bug 1664). Thanks to Chris Staub for the report and analysis of affected packages.
*   November 26, 2005

    *   [matt]: Don't install the Linuxthreads man pages, the POSIX threading API is documented in the man3p section provided by the man-pages package (fixes bug 1660).

    *   [matt]: Remove the incorrect note about not having to dump/check a journalled filesystem (fixes bug 1662).

    *   [matt]: Upgrade to module-init-tools 3.2.1.

    *   [matt]: Prevent installing the internationalized man pages for Shadow's `groups` binary (thanks to Randy McMurchy for the report).

    *   [matt]: Upgrade to man-pages 2.14.

    *   [matt]: Upgrade to findutils-4.2.26

    *   [manuel]: Changed --strip-path to --strip-components in the unpack of module-init-tools-testsuite package.

*   November 23, 2005

    *   [gerard]: Corrected reference to 'man page' to 'HTML documentation' in chapter 6/sec
*   November 18, 2005

    *   [manuel]: Fixed the unpack of the module-init-tools-testsuite package.
*   November 16, 2005

    *   [jhuntwork]: Textual correction concerning gettext in chapter 5 and the use of --disable-shared
*   November 15, 2005

    *   [archaic]: Changed the chapter 6 Perl Dpager configure option to reflect the new location of the less binary.
*   November 14, 2005

    *   [jhuntwork]: Only install `msgfmt` from gettext in chapter 5\. This is all that is necessary and prevents gettext from trying to pull in unnecessary elements from the host. Thanks to Greg Schafer for pointing this out.
*   November 12, 2005

    *   [matt]: Improve the heuristic for determining a locale that is supported by both Glibc and packages outside LFS (bug 1642). Many thanks to Alexander Patrakov for highlighting the numerous issues and for reviewing the various suggested fixes.

    *   [jhuntwork]: Move sed to earlier in the build.

    *   [jhuntwork]: Move m4 to earlier in the build. Thanks Chris Staub.

*   November 11, 2005

    *   [matt]: Omit running Bzip2's testsuite as a separate step, as `make` runs it automatically (bug 1652).
*   November 10, 2005

    *   [jhuntwork]: Initial re-ordering of packages. Thanks to Chris Staub (bug 684).
*   November 7, 2005

    *   [matt]: Install the binaries from Less to /usr/bin instead of /bin (fixes bug 1643).

    *   [matt]: Remove the --libexecdir option from both passes of GCC in chapter 5 (fixes bug 1646). Also change the --libexecdir option for Findutils to conform with the /usr/lib/packagename convention already prevalent in the book (fixes bug 1644).

*   November 6, 2005

    *   [matt]: Remove the optimization related warnings from the toolchain packages (bug 1650).

    *   [matt]: Install Vim's documentation to /usr/share/doc/vim-7.0 instead of /usr/share/vim/vim64/doc (bug 1610). Thanks to Randy McMurchy for the report, and Jeremy Huntwork for the fix.

    *   [matt]: Stop Udev from killing udevd processes on the host system (fixes bug 1651). Thanks to Alexander Patrakov for the report and the fix.

    *   [matt]: Upgrade to Coreutils 5.93.

    *   [matt]: Upgrade to Psmisc 21.8.

    *   [matt]: Upgrade to Glibc 2.3.6.

*   November 5, 2005

    *   [matt]: Add a note to the toolchain sanity check in chapter 5 to explain that if TCL fails to build, it's an indication of a broken toolchain (bug 1581).
*   November 3, 2005

    *   [matt]: Upgrade to man-pages 2.13.

    *   [matt]: Correct the instructions for running Module-Init-Tools' testsuite (fixes bug 1597). Thanks to Greg Schafer, Tushar Teredesai and to Randy McMurchy for providing the patch.

*   October 31, 2005

    *   [matt]: Upgrade to shadow-4.0.13.

    *   [matt]: Upgrade to vim-6.4.

    *   [matt]: Upgrade to procps-3.2.6.

    *   [matt]: Build udev_run_devd and udev_run_hotplugd and alter the udev rules file so that udev once again executes programs in the /etc/dev.d and /etc/hotplug.d directories (fixes bug 1635). Also change the udev rules to prevent udev from handling the "card" and "dm" devices as these are managed entirely by programs outside of LFS.

*   October 29, 2005

    *   [matt]: Upgrade to udev-071

    *   [matt]: Upgrade to man-pages 2.11.

    *   [matt]: Upgrade to coreutils-5.92\. This involved removing the DEFAULT_POSIX2_VERSION environment variable as it is no longer required. The testsuite also requires the Data::Dumper module from Perl, so it is now built in chapter05/perl.xml.

*   October 22, 2005

    *   [archaic]: Upgrade to m4-1.4.4.
*   October 21, 2005

    *   [matt]: Upgrade to file-4.16.

    *   [matt]: Upgrade to man-pages 2.10.

    *   [matt]: Upgrade to ncurses 5.5.

*   October 15, 2005

    *   [matt]: Upgrade to man-pages 2.09.

    *   [matt]: Use an updated version of the Udev rules file (fixes bug 1639).

    *   [matt]: Add a cdrom group as required by the Udev rules. file

*   October 9, 2005

    *   [matt]: Emphasise the fact that one must delete the source directory after each package has been installed. Fixes bug 1638\. Thanks to Chris Staub.
*   October 8, 2005

    *   [archaic]: Added patch to fix poor tempfile creation in Texinfo-4.8 that can lead to a symlink attack.

    *   [matt]: Upgrade to iproute2-051007.

*   October 7, 2005

    *   [matt]: Upgrade to gcc-4.0.2.
*   October 4, 2005

    *   [matt]: Prevent GCC from running the `fixincludes` script in chapter5 pass2 and chapter 6 (fixes bug 1636). Thanks to Tushar and Greg for their contributions on this issue.
*   September 29, 2005

    *   [matt]: Add more explicit reader prerequisites (fixes bug 1629).

    *   [matt]: Add `-v` to commands that accept it (fixes bug 1612).

*   September 26, 2005

    *   [matt]: Upgrade to man-pages-2.08.
*   September 24, 2005

    *   [matt]: Upgrade to gawk-3.1.5.

    *   [matt]: Upgrade to man-1.6b.

    *   [matt]: Upgrade to util-linux-2.12r.

*   September 20, 2005

    *   [matt]: Upgrade to bison-2.1.
*   September 17, 2005

    *   [matt]: Upgrade to udev-070 and remove the unnecessary "udevdir=/dev" parameter.

    *   [matt]: Added patch for coreutils to improve echo's POSIX and bash compatibility and to recognise "\xhh" syntax as required by the test suite in udev-069 and later.

*   September 15, 2005

    *   [archaic]: Added patch for util-linux to prevent a umount vulnerability.
*   September 8, 2005

    *   [jhuntwork]: Upgrade to groff-1.19.2
*   September 6, 2005

    *   [ken]: Reworded the glibc text to expect test failures.
*   September 5, 2005

    *   [ken]: Add patch to fix some of the math tests in glibc.
*   September 4, 2005

    *   [matt]: Add patch to stop `cfdisk` segfaulting when invoked on devices with partitions that don't contain an ext2, ext3, xfs or jfs filesystem (see bug 1604).

    *   [matt]: Upgrade to libtool-1.5.20.

    *   [matt]: Upgrade to findutils-4.2.25.

*   September 2, 2005

    *   [matt]: The optimization flag for util-linux comes from `configure` rather than `MCONFIG`, so adjust the `sed` in order for the segfault fix to actually work.

    *   [matt]: Avoid the potential race condition when invoking `find` to remove GCC's fixed headers.

*   August 30th, 2005

    *   [matt]: Work around a segfault in cfdisk by compiling with -O instead of the default -O2 optimization setting (fixes bug 1604).

    *   [matt]: Update the inetutils patch to use the upstream fix for GCC-4.x compilation problems (fixes bug 1602).

    *   [matt]: Upgrade to shadow-2.0.12

    *   [ken]: Remove **sed -i** commands from gcc-pass2.

*   August 28th, 2005

    *   [jhuntwork]: Adjusted tar commands in Bash and Glibc chapter 6 builds for consistency
*   August 23rd, 2005

    *   [matt]: `find` may fail due to a race condition when deleting files. Remove the && construct in chapter05/adjusting.xml so that the rest of the commands for removing fixed headers will be executed (fixes bug 1621).

    *   [matt]: Install Udev's documentation relating to configuring rules (fixes bug 1622).

    *   [matt]: Upgrade to Man-1.6a.

*   August 20th, 2005

    *   [matt]: Stop moving some of coreutils' binaries to /bin as they aren't required to be there (fixes bug 1620).
*   August 19th, 2005

    *   [matt]: Upgrade to Udev-068.

    *   [matt]: Upgrade to IANA-etc-2.00.

    *   [matt]: Upgrade to file-4.15.

*   August 18th, 2005

    *   [matt]: Simplify the method for finding where GCC's default specs file and private include directory live. Additionally, don't assume the host's sed supports the -i switch.

    *   [ken]: Add a patch to sanitise bzgrep's handling of filenames.

*   August 16th, 2005

    *   [matt]: Install sed's man page to /usr/share/doc/sed-4.1.4 instead of /usr/share/doc (fixes bug 1600).

    *   [matt]: Upgraded to linux-2.6.12.5.

*   August 15th, 2005

    *   [matt]: Alter the GCC -fomit-frame-pointer sed to protect from multiple invocations (Greg Schafer).
*   August 14th, 2005

    *   [ken]: Upgrade shadow to 4.0.11.1 with --enable-shadowgrp as advised by Greg Schafer.

    *   [matt]: Mention the common libmudflap test failures in GCC (fixes bug 1615).

    *   [matt]: Added patch to install documentation for bzip2 (fixes bug 1603).

    *   [matt]: Upgrade to linux-2.6.12.4.

    *   [matt]: Add sed to chapter05/gcc-pass2 and chapter06/gcc to ensure they get built with -fomit-frame-pointer so it matches the bootstrap build in chapter05/gcc-pass1 (fixes bug 1609).

    *   [matt]: Upgrade to udev-067 including a fix for the failing test (bug 1611).

*   August 12th, 2005

    *   [matt]: Explain that libiconv isn't required on an LFS system (fixes bug 1614).

    *   [matt]: Fix ownership of libtool's libltdl data files (fixes bug 1601).

    *   [matt]: Change findutils and vim's configure switch explanations to the convention used in the rest of the book (Bug 1613).

    *   [matt]: Expand explanation of device node creation at the start of chapter 6.

    *   [matt]: Fix incorrect version number for expect's installed library (Bug 1608).

*   August 7th, 2005

    *   [archaic]: Added note in Shadow regarding building Cracklib from BLFS first.
*   August 6th, 2005

    *   [matt]: Add texi2pdf to list of Texinfo's installed files.

    *   [matt]: Updated Vim's security patch to address the latest modeline vulnerability.

*   July 30th, 2005

    *   [matt]: Added instructions for installing Bash documentation (Randy McMurchy).

    *   [matt]: Remove GCC linkonce patch from chapter03/patches.xml as it's no longer used in the book

*   July 29th, 2005

    *   [manuel]: Removed the text about defining gvimrc.
*   July 28th, 2005

    *   [matt]: Add GCC-4 related patch for kbd.

    *   [matt]: Add GCC-4 related patch for inetutils.

    *   [matt]: Remove the note regarding a known test failure in GRUB. The test no longer fails under GCC-4.

    *   [matt]: Add GCC-4 related patch to chapter06 tar.

*   July 27th, 2005

    *   [matt]: Don't define gvim's configuration file as we don't compile gvim in LFS (Bruce Dubbs).
*   July 26th, 2005

    *   [matt]: Remove "groups" from the list of programs installed by shadow, as we use the version provided by coreutils instead (Randy McMurchy).

    *   [matt]: Updated to mktemp-1.5-add_tempfile-3.patch, which adds license and copyright information to the previous version.

*   July 23rd, 2005

    *   [matt]: Moved FORMER*CONTRIBUTORS information into the book, so as people can actually see it. The space constraint argument in that file was weak - it only added another 10 lines to a 255 page document (PDF). Now at least we _publically* acknowledge the efforts of previous contributors.

    *   [matt]: Updated to man-pages-2.07.

    *   [matt]: Updated to zlib-1.2.3.

*   July 22nd, 2005

    *   [manuel]: Added obfuscate.sh and modified the Makefile to obfuscate e-mail addresses in XHTML output.
*   July 21st, 2005

    *   [matt]: Add GCC-4 related patches to chapter06 glibc.

    *   [matt]: Unset the GCC_INCLUDEDIR variable once it's no longer needed.

*   July 19th, 2005

    *   [matt]: Removed flex++ from the list of installed files, as it is no longer present (Randy McMurchy)
*   July 18th, 2005

    *   [matt]: Re-added the explanation of the fixincludes process and rewording where necessary (Chris Staub), and reworded description of the specs patch.

    *   [matt]: Remove all host headers brought in via the fixincludes process, not just pthread.h and sigaction.h

*   July 17th, 2005

    *   [matt]: Slightly adjusted the specs file seds, to prevent multiple seds from adversely affecting them.

    *   [matt]: Removed the fixincludes sed from gcc-pass1 as we may need to fix up host's headers. Also reinstate the associated removal of pthread.h and sigthread.h.

*   July 16th, 2005

    *   [jhuntwork]: Added sed to chapter 5 gcc builds to force the fixincludes to use the headers in /tools and not the host.

    *   [jhuntwork]: Removed no_fixincludes and linkonce patches for gcc4\. Also removed the command to remove the fixed pthread.h.

    *   [jhuntwork]: Fixed adjusting toolchain sed for both chapters 5 and 6.

*   July 15th, 2005

    *   [matt]: Updated to Linux-2.6.12.3.

    *   [matt]: Added a patch to enable tar to build with gcc-4.0.1

    *   [matt]: GCC-4.x no longer installs its specs file by default. Alter the toolchain adjustment stage to first dump the specs file where GCC will find it, then alter it.

    *   [matt]: Added patches for chapter 5's Glibc to build with gcc-4.0.1

    *   [matt]: Updated to gcc-4.0.1.

    *   [matt]: Updated to udev-063.

*   July 13th, 2005

    *   [matt]: Updated to automake-1.9.6.
*   July 8th, 2005

    *   [matt]: Updated to udev-062.

    *   [matt]: Updated to linux-libc-headers-2.6.12.0.

    *   [matt]: Updated to linux-2.6.12.2.

    *   [matt]: Updated to shadow-4.0.10.

    *   [matt]: Updated to iana-etc-1.10.

*   July 6th, 2005

    *   [archaic]: Pulled the inetutils kernel header patch out again as it is not needed.

    *   [matt]: Updated to e2fsprogs-1.38.

    *   [matt]: Updated to binutils-2.16.1.

*   July 5th, 2005

    *   [matt]: Updated to tcl-8.4.11.

    *   [matt]: Updated to man-1.6.

    *   [matt]: Updated to file 4.14.

    *   [matt]: Updated to man-pages 2.05.

*   June 12th, 2005

    *   [matt]: Upgraded to gettext-0.14.5.

    *   [matt]: Upgraded to perl-5.8.7.

    *   [matt]: Upgraded to tcl-8.4.10.

    *   [matt]: Upgraded to man-pages-2.03.

*   May 24th, 2005

    *   [jim]: Changed gcc-specs patch to -2.
*   May 23nd, 2005

    *   [jim]: Changed changelog to use version entities.
*   May 22nd, 2005

    *   [matt]: Updated to Udev-058.

    *   [matt]: Updated to Libtool-1.5.18.

    *   [matt]: Updated to Gcc-3.4.4.

    *   [matt]: Updated to Binutils-2.16.

*   May 15th, 2005

    *   [matt]: Updated to Grub 0.97.

    *   [matt]: Updated to Libtool 1.5.16.

    *   [jim]: Updated to udev 057.

*   April 14, 2005

    *   [jim]: Updated to man-pages 2.02.
*   April 13, 2005

    *   [jim]: Updated to glibc 2.3.5.

    *   [jim]: Updated to gettext 0.14.4.

*   April 12, 2005

    *   [manuel]: Small redaction changes.
*   April 11, 2005

    *   [manuel]: Several tags and text corrections.
*   April 6, 2005

    *   [jim]: Removed IPRoute2 patch for a sed (Ryan Oliver).

Branch frozen for LFS 6.1 as of April 5, 2005\. Some packages and patches updates related with security up to July 9, 2005.

# 1.4\. 资源

## 1.4.1\. FAQ

在构建 LFS 系统的过程中，如果您遇到错误、有什么问题、或者认为书中有错别字，请首先查看在[*http://www.linuxfromscratch.org/faq/*](http://www.linuxfromscratch.org/faq/) 上的常见问答(FAQ) 。

## 1.4.2\. 邮件列表

`linuxfromscratch.org` 服务器为 LFS 项目的开发提供了许多邮件列表，包括主要的开发和支持列表，以及其它内容。如果 FAQ 没有解决你的问题，你可以在 [*http://www.linuxfromscratch.org/search.html*](http://www.linuxfromscratch.org/search.html) 搜索邮件列表。

要了解这些邮件列表的不同、如何订阅邮件列表、邮件列表的存档位置以及其它的相关信息，请访问：[*http://www.linuxfromscratch.org/mail.html*](http://www.linuxfromscratch.org/mail.html) 。

## 1.4.3\. IRC

LFS 社区的许多成员在我们社区的 Internet 多线交谈(IRC)网络上为大家提供帮助。在您使用这种方式获取帮助之前，请确定您的问题没有在 LFS FAQ 和邮件列表存档里出现过。您可以在 `irc.linuxfromscratch.org` 找到 IRC 网络，支持频道是 #LFS-support 。 咱们中国人也有自己的 IRC，具体细节请查看[LFS 中文 IRC 在线聊天室，内含 IRC 简明使用帮助。](http://www.linuxsir.org/bbs/thread322197.html)

## 1.4.4\. 参考

关于软件包的附加信息，可以从 [*http://www.linuxfromscratch.org/~matthew/LFS-references.html*](http://www.linuxfromscratch.org/~matthew/LFS-references.html) 获得许多有用的提示。

## 1.4.5\. 镜像站点

LFS 项目在全世界范围内有许多镜像，可以让访问 Web 站点和下载所需的软件包更加快捷便利。请访问 [*http://www.linuxfromscratch.org/mirrors.html*](http://www.linuxfromscratch.org/mirrors.html) 以获得当前可用的镜像站点列表。

## 1.4.6\. 联系信息

请直接将您的问题和意见发送到上面所列的邮件列表。

# 1.5\. 帮助

如果您在本书中遇到困难或者问题，请查看 FAQ 页：[*http://www.linuxfromscratch.org/faq/#generalfaq*](http://www.linuxfromscratch.org/faq/#generalfaq) ，您所遇到的问题往往这里已经有解答了。如果 FAQ 没有解决您的问题，请尝试找到问题的根源，[*http://www.linuxfromscratch.org/hints/downloads/files/errors.txt*](http://www.linuxfromscratch.org/hints/downloads/files/errors.txt) 上的提示将给您提供一些故障诊断的指导。您还可以到咱们中国人的 LFS 大本营：[LinuxSir.Org 的 LFS 论坛](http://www.linuxsir.org/bbs/forumdisplay.php?f=58)寻求帮助。

如果 FAQ 没有解决你的问题，你可以在 [*http://www.linuxfromscratch.org/search.html*](http://www.linuxfromscratch.org/search.html) 搜索邮件列表。

我们也有一个出色的 LFS 社区通过 IRC 和邮件列表为您提供帮助(请参见 节 1.4, "资源" )。然而，我们每天都会收到一些只要简单地察看 FAQ 或者搜索邮件列表就可以解决的问题。为了让我们能够为真正不寻常的问题提供帮助，请您自己先搜索一下！仅在你的搜索得不到问题的解决办法的时候，再向我们寻求帮助。为了帮助诊断和解决问题，您需要在帮助请求中包含所有相关信息。

## 1.5.1\. 需要提及的内容

除了一份您所遇到的问题的简短描述外，在任何帮助请求中您还需要附加一些必需的信息：

*   您所使用的 LFS Book 的版本(本书的版本是 6.2)

*   创建 LFS 所用的宿主 Linux 发行版的名称及其版本

*   遇到问题的软件包以及所在的章节

*   确切的错误信息或故障现象描述

*   您是否完全是按本书所说的在做［强烈建议新手完全按照本书操作］

### 注意

不按本书说的做*并不*意味着我们就不会给您提供帮助，毕竟 LFS 是个性化的选择。提供您在建立过程中所做的任何更改，将有助于我们估计和确定导致您所遇到的问题的可能原因。

## 1.5.2\. Configure 脚本的问题

如果执行 `configure` 脚本的时候，某个地方出错了，请检查 `config.log` 文件，这个文件可能包含 `configure` 过程中没有输出到屏幕的错误，如果您需要请求帮助，请把这里面*相关*信息也包含进去。

## 1.5.3\. 编译问题

屏幕输出和各种生成的文件内容，对确定产生编译问题的原因都是有用的。`configure` 脚本和运行 `make` 时所产生的屏幕输出也是有帮助的。不需要把所有的输出都包含进来，只需要包含足够的相关信息就行了。下面是一个从 `make` 的屏幕输出中包含所需信息的例子：

```
gcc -DALIASPATH=\"/mnt/lfs/usr/share/locale:.\"
-DLOCALEDIR=\"/mnt/lfs/usr/share/locale\"
-DLIBDIR=\"/mnt/lfs/usr/lib\"
-DINCLUDEDIR=\"/mnt/lfs/usr/include\" -DHAVE_CONFIG_H -I. -I.
-g -O2 -c getopt1.c
gcc -g -O2 -static -o make ar.o arscan.o commands.o dir.o
expand.o file.o function.o getopt.o implicit.o job.o main.o
misc.o read.o remake.o rule.o signame.o variable.o vpath.o
default.o remote-stub.o version.o opt1.o
-lutil job.o: In function `load_too_high':
/lfs/tmp/make-3.79.1/job.c:1565: undefined reference
to `getloadavg'
collect2: ld returned 1 exit status
make[2]: *** [make] Error 1
make[2]: Leaving directory `/lfs/tmp/make-3.79.1'
make[1]: *** [all-recursive] Error 1
make[1]: Leaving directory `/lfs/tmp/make-3.79.1'
make: *** [all-recursive-am] Error 2 
```

这种情形下，许多人会仅仅只包含最后一段：

```
make [2]: *** [make] Error 1 
```

这对于正确的分析问题是不够的，因为它只是提示出错了，而没有说是*什么*出错了。在上面例子中，全部内容都应该保存下来，因为它包括了所运行的命令和相关的错误信息。

[*http://catb.org/~esr/faqs/smart-questions.html*](http://catb.org/~esr/faqs/smart-questions.html) 上有一篇很好的文章［[《提问的智慧》](http://community.csdn.net/IndexPage/SmartQuestion.aspx)(中文版)］，讲述如何在 Internet 上寻求帮助，请阅读并遵照文章中的提示，将有助于您得到您所需要的答案。