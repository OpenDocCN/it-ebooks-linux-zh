# 3\. 软件包和补丁

# 3.1\. 简介

本章包含了一个构建基本 Linux 系统需要下载的软件包清单，列出的版本号是已知可以正常工作的版本，本书就是建立在这些软件包之上的。我们强烈建议您不要使用新的版本，因为用于前一个版本的编译安装命令可能并不适用于新的版本。最新版本的软件包也许需要一个与旧版本不同的工作环境，如果并没有配置这样的工作环境，那么软件包就可能会出现问题。

下载位置可能并不总是有效的，如果在本书发布之后，某个软件的下载位置有了变动，Google([*http://www.google.com/*](http://www.google.com/))可以搜索到大多数的软件包。如果 Google 也搜索不到，请尝试 [*http://www.linuxfromscratch.org/lfs/packages.html*](http://www.linuxfromscratch.org/lfs/packages.html) 上的其它下载手段。

下载的软件包和补丁需要存放到一个构建过程中方便访问的地方，还需要一个工作目录来解压和编译源码包。`$LFS/sources` 既可以用来存储软件包和补丁，也可以作为工作目录。使用这个目录的好处是，所有需要的部件都在 LFS 分区上，构建过程中的所有步骤都可以访问到。

要创建这个目录，在开始下载之前用 *root* 用户登录，并运行下面的命令：

```
mkdir -v $LFS/sources 
```

把目录设置为可写和 sticky 模式，这里"Sticky"的意思是虽然某个目录对于多个用户有写入的权限，但这个目录中的文件只有其所有者才能删除。请运行下面的命令使目录可写，并设置 sticky 模式：

```
chmod -v a+wt $LFS/sources 
```

# 3.2\. 全部软件包

下载或者用别的方式获得下列软件包：

Autoconf (2.59) - 904KB:

主页：[*http://www.gnu.org/software/autoconf/*](http://www.gnu.org/software/autoconf/)

下载：[*http://ftp.gnu.org/gnu/autoconf/autoconf-2.59.tar.bz2*](http://ftp.gnu.org/gnu/autoconf/autoconf-2.59.tar.bz2)

MD5 和：`1ee40f7a676b3cfdc0e3f7cd81551b5f`

Automake (1.9.6) - 748KB:

主页：[*http://www.gnu.org/software/automake/*](http://www.gnu.org/software/automake/)

下载：[*http://ftp.gnu.org/gnu/automake/automake-1.9.6.tar.bz2*](http://ftp.gnu.org/gnu/automake/automake-1.9.6.tar.bz2)

MD5 和：`c11b8100bb311492d8220378fd8bf9e0`

Bash (3.1) - 2,475KB:

主页：[*http://www.gnu.org/software/bash/*](http://www.gnu.org/software/bash/)

下载：[*http://ftp.gnu.org/gnu/bash/bash-3.1.tar.gz*](http://ftp.gnu.org/gnu/bash/bash-3.1.tar.gz)

MD5 和：`ef5304c4b22aaa5088972c792ed45d72`

Bash Documentation (3.1) - 2,013 KB:

下载：[*http://ftp.gnu.org/gnu/bash/bash-doc-3.1.tar.gz*](http://ftp.gnu.org/gnu/bash/bash-doc-3.1.tar.gz)

MD5 和：`a8c517c6a7b21b8b855190399c5935ae`

Berkeley DB (4.4.20) - 7,767 KB:

主页：[*http://dev.sleepycat.com/*](http://dev.sleepycat.com/)

下载：[*http://downloads.sleepycat.com/db-4.4.20.tar.gz*](http://downloads.sleepycat.com/db-4.4.20.tar.gz)

MD5 和：`d84dff288a19186b136b0daf7067ade3`

Binutils (2.16.1) - 12,256 KB:

主页：[*http://sources.redhat.com/binutils/*](http://sources.redhat.com/binutils/)

下载：[*http://ftp.gnu.org/gnu/binutils/binutils-2.16.1.tar.bz2*](http://ftp.gnu.org/gnu/binutils/binutils-2.16.1.tar.bz2)

MD5 和：`6a9d529efb285071dad10e1f3d2b2967`

Bison (2.2) - 1,052KB:

主页：[*http://www.gnu.org/software/bison/*](http://www.gnu.org/software/bison/)

下载：[*http://ftp.gnu.org/gnu/bison/bison-2.2.tar.bz2*](http://ftp.gnu.org/gnu/bison/bison-2.2.tar.bz2)

MD5 和：`e345a5d021db850f06ce49eba78af027`

Bzip2 (1.0.3) - 654KB:

主页：[*http://www.bzip.org/*](http://www.bzip.org/)

下载：[*http://www.bzip.org/1.0.3/bzip2-1.0.3.tar.gz*](http://www.bzip.org/1.0.3/bzip2-1.0.3.tar.gz)

MD5 和：`8a716bebecb6e647d2e8a29ea5d8447f`

Coreutils (5.96) - 4,948KB:

主页：[*http://www.gnu.org/software/coreutils/*](http://www.gnu.org/software/coreutils/)

下载：[*http://ftp.gnu.org/gnu/coreutils/coreutils-5.96.tar.bz2*](http://ftp.gnu.org/gnu/coreutils/coreutils-5.96.tar.bz2)

MD5 和：`bf55d069d82128fd754a090ce8b5acff`

DejaGNU (1.4.4) - 1,056KB:

主页：[*http://www.gnu.org/software/dejagnu/*](http://www.gnu.org/software/dejagnu/)

下载：[*http://ftp.gnu.org/gnu/dejagnu/dejagnu-1.4.4.tar.gz*](http://ftp.gnu.org/gnu/dejagnu/dejagnu-1.4.4.tar.gz)

MD5 和：`053f18fd5d00873de365413cab17a666`

Diffutils (2.8.1) - 762KB:

主页：[*http://www.gnu.org/software/diffutils/*](http://www.gnu.org/software/diffutils/)

下载：[*http://ftp.gnu.org/gnu/diffutils/diffutils-2.8.1.tar.gz*](http://ftp.gnu.org/gnu/diffutils/diffutils-2.8.1.tar.gz)

MD5 和：`71f9c5ae19b60608f6c7f162da86a428`

E2fsprogs (1.39) - 3,616KB:

主页：[*http://e2fsprogs.sourceforge.net/*](http://e2fsprogs.sourceforge.net/)

下载：[*http://prdownloads.sourceforge.net/e2fsprogs/e2fsprogs-1.39.tar.gz?download*](http://prdownloads.sourceforge.net/e2fsprogs/e2fsprogs-1.39.tar.gz?download)

MD5 和：`06f7806782e357797fad1d34b7ced0c6`

Expect (5.43.0) - 514KB:

主页：[*http://expect.nist.gov/*](http://expect.nist.gov/)

下载：[*http://expect.nist.gov/src/expect-5.43.0.tar.gz*](http://expect.nist.gov/src/expect-5.43.0.tar.gz)

MD5 和：`43e1dc0e0bc9492cf2e1a6f59f276bc3`

File (4.17) - 544KB:

下载：*ftp://ftp.gw.com/mirrors/pub/unix/file/file-4.17.tar.gz*

MD5 和：`50919c65e0181423d66bb25d7fe7b0fd`

### 注意

4.17 版本的 File 软件包在所列的位置可能下载不到，主下载站点的管理员有时候会在新版本发布之后，删除旧的版本。替代的下载位置是 [*http://www.linuxfromscratch.org/lfs/download.html#ftp*](http://www.linuxfromscratch.org/lfs/download.html#ftp) ，这里可以下载到所需的版本。

Findutils (4.2.27) - 1,097 KB:

主页：[*http://www.gnu.org/software/findutils/*](http://www.gnu.org/software/findutils/)

下载： [*http://ftp.gnu.org/gnu/findutils/findutils-4.2.27.tar.gz*](http://ftp.gnu.org/gnu/findutils/findutils-4.2.27.tar.gz)

MD5 和：`f1e0ddf09f28f8102ff3b90f3b5bc920`

Flex (2.5.33) - 680KB:

主页：[*http://flex.sourceforge.net*](http://flex.sourceforge.net)

下载：[*http://prdownloads.sourceforge.net/flex/flex-2.5.33.tar.bz2?download*](http://prdownloads.sourceforge.net/flex/flex-2.5.33.tar.bz2?download)

MD5 和：`343374a00b38d9e39d1158b71af37150`

Gawk (3.1.5) - 1,716KB:

主页：[*http://www.gnu.org/software/gawk/*](http://www.gnu.org/software/gawk/)

下载：[*http://ftp.gnu.org/gnu/gawk/gawk-3.1.5.tar.bz2*](http://ftp.gnu.org/gnu/gawk/gawk-3.1.5.tar.bz2)

MD5 和：`5703f72d0eea1d463f735aad8222655f`

GCC (4.0.3) - 32,208KB:

主页：[*http://gcc.gnu.org/*](http://gcc.gnu.org/)

下载：[*http://ftp.gnu.org/gnu/gcc/gcc-4.0.3/gcc-4.0.3.tar.bz2*](http://ftp.gnu.org/gnu/gcc/gcc-4.0.3/gcc-4.0.3.tar.bz2)

MD5 和：`6ff1af12c53cbb3f79b27f2d6a9a3d50`

Gettext (0.14.5) - 6,940KB:

主页：[*http://www.gnu.org/software/gettext/*](http://www.gnu.org/software/gettext/)

下载：[*http://ftp.gnu.org/gnu/gettext/gettext-0.14.5.tar.gz*](http://ftp.gnu.org/gnu/gettext/gettext-0.14.5.tar.gz)

MD5 和：`e2f6581626a22a0de66dce1d81d00de3`

Glibc (2.3.6) - 13,687KB:

主页：[*http://www.gnu.org/software/libc/*](http://www.gnu.org/software/libc/)

下载：[*http://ftp.gnu.org/gnu/glibc/glibc-2.3.6.tar.bz2*](http://ftp.gnu.org/gnu/glibc/glibc-2.3.6.tar.bz2)

MD5 和：`bfdce99f82d6dbcb64b7f11c05d6bc96`

Glibc LibIDN add-on (2.3.6) - 99 KB:

下载：[*http://ftp.gnu.org/gnu/glibc/glibc-libidn-2.3.6.tar.bz2*](http://ftp.gnu.org/gnu/glibc/glibc-libidn-2.3.6.tar.bz2)

MD5 和：`49dbe06ce830fc73874d6b38bdc5b4db`

Grep (2.5.1a) - 516KB:

主页：[*http://www.gnu.org/software/grep/*](http://www.gnu.org/software/grep/)

下载：[*http://ftp.gnu.org/gnu/grep/grep-2.5.1a.tar.bz2*](http://ftp.gnu.org/gnu/grep/grep-2.5.1a.tar.bz2)

MD5 和：`52202fe462770fa6be1bb667bd6cf30c`

Groff (1.18.1.1) - 2,208KB:

主页：[*http://www.gnu.org/software/groff/*](http://www.gnu.org/software/groff/)

下载：[*http://ftp.gnu.org/gnu/groff/groff-1.18.1.1.tar.gz*](http://ftp.gnu.org/gnu/groff/groff-1.18.1.1.tar.gz)

MD5 和：`511dbd64b67548c99805f1521f82cc5e`

GRUB (0.97) - 950KB:

主页：[*http://www.gnu.org/software/grub/*](http://www.gnu.org/software/grub/)

下载：*ftp://alpha.gnu.org/gnu/grub/grub-0.97.tar.gz*

MD5 和：`cd3f3eb54446be6003156158d51f4884`

Gzip (1.3.5) - 324KB:

主页：[*http://www.gzip.org/*](http://www.gzip.org/)

下载：*ftp://alpha.gnu.org/gnu/gzip/gzip-1.3.5.tar.gz*

MD5 和：`3d6c191dfd2bf307014b421c12dc8469`

Iana-Etc (2.10) - 184KB:

主页：[*http://www.sethwklein.net/projects/iana-etc/*](http://www.sethwklein.net/projects/iana-etc/)

下载：[*http://www.sethwklein.net/projects/iana-etc/downloads/iana-etc-2.10.tar.bz2*](http://www.sethwklein.net/projects/iana-etc/downloads/iana-etc-2.10.tar.bz2)

MD5 和：`53dea53262b281322143c744ca60ffbb`

Inetutils (1.4.2) - 1,019 KB:

主页：[*http://www.gnu.org/software/inetutils/*](http://www.gnu.org/software/inetutils/)

下载：[*http://ftp.gnu.org/gnu/inetutils/inetutils-1.4.2.tar.gz*](http://ftp.gnu.org/gnu/inetutils/inetutils-1.4.2.tar.gz)

MD5 和：`df0909a586ddac2b7a0d62795eea4206`

IPRoute2 (2.6.16-060323) - 378 KB:

主页：[*http://linux-net.osdl.org/index.php/Iproute2*](http://linux-net.osdl.org/index.php/Iproute2)

下载：[*http://developer.osdl.org/dev/iproute2/download/iproute2-2.6.16-060323.tar.gz*](http://developer.osdl.org/dev/iproute2/download/iproute2-2.6.16-060323.tar.gz)

MD5 和：`f31d4516b35bbfeaa72c762f5959e97c`

Kbd (1.12) - 618KB:

下载： [*http://www.kernel.org/pub/linux/utils/kbd/kbd-1.12.tar.bz2*](http://www.kernel.org/pub/linux/utils/kbd/kbd-1.12.tar.bz2)

MD5 和：`069d1175b4891343b107a8ac2b4a39f6`

Less (394) - 286KB:

主页：[*http://www.greenwoodsoftware.com/less/*](http://www.greenwoodsoftware.com/less/)

下载：[*http://www.greenwoodsoftware.com/less/less-394.tar.gz*](http://www.greenwoodsoftware.com/less/less-394.tar.gz)

MD5 和：`a9f072ccefa0d315b325f3e9cdbd4b97`

LFS-Bootscripts (6.2) - 24 KB:

下载：[*http://www.linuxfromscratch.org/lfs/downloads/6.2/lfs-bootscripts-6.2.tar.bz2*](http://www.linuxfromscratch.org/lfs/downloads/6.2/lfs-bootscripts-6.2.tar.bz2)

MD5 和：`45f9efc6b75c26751ddb74d1ad0276c1`

Libtool (1.5.22) - 2,856KB:

主页：[*http://www.gnu.org/software/libtool/*](http://www.gnu.org/software/libtool/)

下载：[*http://ftp.gnu.org/gnu/libtool/libtool-1.5.22.tar.gz*](http://ftp.gnu.org/gnu/libtool/libtool-1.5.22.tar.gz)

MD5 和：`8e0ac9797b62ba4dcc8a2fb7936412b0`

Linux (2.6.16.27) - 39,886 KB:

主页：[*http://www.kernel.org/*](http://www.kernel.org/)

下载：[*http://www.kernel.org/pub/linux/kernel/v2.6/linux-2.6.16.27.tar.bz2*](http://www.kernel.org/pub/linux/kernel/v2.6/linux-2.6.16.27.tar.bz2)

MD5 和：`ebedfe5376efec483ce12c1629c7a5b1`

### 注意

Linux 内核更新很快，主要是由于经常发现安全漏洞。除非勘误表上允许使用其他版本的内核，否则应当使用最新版的 2.6.16.x 内核。请不要使用 2.6.17 或更新版本的内核，因为它们可能与启动脚本不兼容。

Linux-Libc-Headers (2.6.12.0) - 2,481 KB:

下载：[*http://ep09.pld-linux.org/~mmazur/linux-libc-headers/linux-libc-headers-2.6.12.0.tar.bz2*](http://ep09.pld-linux.org/~mmazur/linux-libc-headers/linux-libc-headers-2.6.12.0.tar.bz2)

MD5 和：`eae2f562afe224ad50f65a6acfb4252c`

M4 (1.4.4) - 376KB:

主页：[*http://www.gnu.org/software/m4/*](http://www.gnu.org/software/m4/)

下载：[*http://ftp.gnu.org/gnu/m4/m4-1.4.4.tar.gz*](http://ftp.gnu.org/gnu/m4/m4-1.4.4.tar.gz)

MD5 和：`8d1d64dbecf1494690a0f3ba8db4482a`

Make (3.80) - 900KB:

主页：[*http://www.gnu.org/software/make/*](http://www.gnu.org/software/make/)

下载：[*http://ftp.gnu.org/gnu/make/make-3.80.tar.bz2*](http://ftp.gnu.org/gnu/make/make-3.80.tar.bz2)

MD5 和：`0bbd1df101bc0294d440471e50feca71`

Man-DB (2.4.3) - 798KB:

主页：[*http://www.nongnu.org/man-db/*](http://www.nongnu.org/man-db/)

下载：[*http://savannah.nongnu.org/download/man-db/man-db-2.4.3.tar.gz*](http://savannah.nongnu.org/download/man-db/man-db-2.4.3.tar.gz)

MD5 和：`30814a47f209f43b152659ba51fc7937`

Man-pages (2.34) - 1,760KB:

下载：[*http://www.kernel.org/pub/linux/docs/manpages/man-pages-2.34.tar.bz2*](http://www.kernel.org/pub/linux/docs/manpages/man-pages-2.34.tar.bz2)

MD5 和：`fb8d9f55fef19ea5ab899437159c9420`

Mktemp (1.5) - 69KB:

主页：[*http://www.mktemp.org/*](http://www.mktemp.org/)

下载：*ftp://ftp.mktemp.org/pub/mktemp/mktemp-1.5.tar.gz*

MD5 和：`9a35c59502a228c6ce2be025fc6e3ff2`

Module-Init-Tools (3.2.2) - 166 KB:

主页：[*http://www.kerneltools.org/*](http://www.kerneltools.org/)

下载：[*http://www.kerneltools.org/pub/downloads/module-init-tools/module-init-tools-3.2.2.tar.bz2*](http://www.kerneltools.org/pub/downloads/module-init-tools/module-init-tools-3.2.2.tar.bz2)

MD5 和：`a1ad0a09d3231673f70d631f3f5040e9`

Ncurses (5.5) - 2,260KB:

主页：[*http://dickey.his.com/ncurses/*](http://dickey.his.com/ncurses/)

下载：*ftp://invisible-island.net/ncurses/ncurses-5.5.tar.gz*

MD5 和：`e73c1ac10b4bfc46db43b2ddfd6244ef`

Patch (2.5.4) - 183KB:

主页：[*http://www.gnu.org/software/patch/*](http://www.gnu.org/software/patch/)

下载：[*http://ftp.gnu.org/gnu/patch/patch-2.5.4.tar.gz*](http://ftp.gnu.org/gnu/patch/patch-2.5.4.tar.gz)

MD5 和：`ee5ae84d115f051d87fcaaef3b4ae782`

Perl (5.8.8) - 9,887KB:

主页：[*http://www.perl.com/*](http://www.perl.com/)

下载：[*http://ftp.funet.fi/pub/CPAN/src/perl-5.8.8.tar.bz2*](http://ftp.funet.fi/pub/CPAN/src/perl-5.8.8.tar.bz2)

MD5 和：`a377c0c67ab43fd96eeec29ce19e8382`

Procps (3.2.6) - 273KB:

主页：[*http://procps.sourceforge.net/*](http://procps.sourceforge.net/)

下载：[*http://procps.sourceforge.net/procps-3.2.6.tar.gz*](http://procps.sourceforge.net/procps-3.2.6.tar.gz)

MD5 和：`7ce39ea27d7b3da0e8ad74dd41d06783`

Psmisc (22.2) - 239KB:

主页：[*http://psmisc.sourceforge.net/*](http://psmisc.sourceforge.net/)

下载：[*http://prdownloads.sourceforge.net/psmisc/psmisc-22.2.tar.gz?download*](http://prdownloads.sourceforge.net/psmisc/psmisc-22.2.tar.gz?download)

MD5 和：`77737c817a40ef2c160a7194b5b64337`

Readline (5.1) - 1,983KB:

主页：[*http://cnswww.cns.cwru.edu/php/chet/readline/rltop.html*](http://cnswww.cns.cwru.edu/php/chet/readline/rltop.html)

下载：[*http://ftp.gnu.org/gnu/readline/readline-5.1.tar.gz*](http://ftp.gnu.org/gnu/readline/readline-5.1.tar.gz)

MD5 和：`7ee5a692db88b30ca48927a13fd60e46`

Sed (4.1.5) - 781KB:

主页：[*http://www.gnu.org/software/sed/*](http://www.gnu.org/software/sed/)

下载：[*http://ftp.gnu.org/gnu/sed/sed-4.1.5.tar.gz*](http://ftp.gnu.org/gnu/sed/sed-4.1.5.tar.gz)

MD5 和：`7a1cbbbb3341287308e140bd4834c3ba`

Shadow (4.0.15) - 1,265KB:

下载： *ftp://ftp.pld.org.pl/software/shadow/shadow-4.0.15.tar.bz2*

MD5 和：`a0452fa989f8ba45023cc5a08136568e`

### 注意

4.0.15 版本的 Shadow 可能在这个位置下载不到。主下载站点的管理员有时候会在新版本发布之后，删除旧的版本。替代的下载位置是 [*http://www.linuxfromscratch.org/lfs/download.html#ftp*](http://www.linuxfromscratch.org/lfs/download.html#ftp) ，这里可以下载到所需的版本。

Sysklogd (1.4.1) - 80KB:

主页：[*http://www.infodrom.org/projects/sysklogd/*](http://www.infodrom.org/projects/sysklogd/)

下载：[*http://www.infodrom.org/projects/sysklogd/download/sysklogd-1.4.1.tar.gz*](http://www.infodrom.org/projects/sysklogd/download/sysklogd-1.4.1.tar.gz)

MD5 和：`d214aa40beabf7bdb0c9b3c64432c774`

Sysvinit (2.86) - 97KB:

下载：*ftp://ftp.cistron.nl/pub/people/miquels/sysvinit/sysvinit-2.86.tar.gz*

MD5 和：`7d5d61c026122ab791ac04c8a84db967`

Tar (1.15.1) - 1,574KB:

主页：[*http://www.gnu.org/software/tar/*](http://www.gnu.org/software/tar/)

下载：[*http://ftp.gnu.org/gnu/tar/tar-1.15.1.tar.bz2*](http://ftp.gnu.org/gnu/tar/tar-1.15.1.tar.bz2)

MD5 和：`57da3c38f8e06589699548a34d5a5d07`

Tcl (8.4.13) - 3,432KB:

主页：[*http://tcl.sourceforge.net/*](http://tcl.sourceforge.net/)

下载：[*http://prdownloads.sourceforge.net/tcl/tcl8.4.13-src.tar.gz?download*](http://prdownloads.sourceforge.net/tcl/tcl8.4.13-src.tar.gz?download)

MD5 和：`c6b655ad5db095ee73227113220c0523`

Texinfo (4.8) - 1,487KB:

主页：[*http://www.gnu.org/software/texinfo/*](http://www.gnu.org/software/texinfo/)

下载：[*http://ftp.gnu.org/gnu/texinfo/texinfo-4.8.tar.bz2*](http://ftp.gnu.org/gnu/texinfo/texinfo-4.8.tar.bz2)

MD5 和：`6ba369bbfe4afaa56122e65b3ee3a68c`

Udev (096) - 190KB:

主页：[*http://www.kernel.org/pub/linux/utils/kernel/hotplug/udev.html*](http://www.kernel.org/pub/linux/utils/kernel/hotplug/udev.html)

下载：[*http://www.kernel.org/pub/linux/utils/kernel/hotplug/udev-096.tar.bz2*](http://www.kernel.org/pub/linux/utils/kernel/hotplug/udev-096.tar.bz2)

MD5 和：`f4effef7807ce1dc91ab581686ef197b`

Udev Configuration Tarball - 4 KB:

下载：[*http://www.linuxfromscratch.org/lfs/downloads/6.2/udev-config-6.2.tar.bz2*](http://www.linuxfromscratch.org/lfs/downloads/6.2/udev-config-6.2.tar.bz2)

MD5 和：`9ff2667ab0f7bfe8182966ef690078a0`

Util-linux (2.12r) - 1,339 KB:

下载：[*http://www.kernel.org/pub/linux/utils/util-linux/util-linux-2.12r.tar.bz2*](http://www.kernel.org/pub/linux/utils/util-linux/util-linux-2.12r.tar.bz2)

MD5 和：`af9d9e03038481fbf79ea3ac33f116f9`

Vim (7.0) - 6,152KB:

主页：[*http://www.vim.org*](http://www.vim.org)

下载：*ftp://ftp.vim.org/pub/vim/unix/vim-7.0.tar.bz2*

MD5 和：`4ca69757678272f718b1041c810d82d8`

Vim (7.0) language files (optional) - 1,228 KB:

主页：[*http://www.vim.org*](http://www.vim.org)

下载：*ftp://ftp.vim.org/pub/vim/extra/vim-7.0-lang.tar.gz*

MD5 和：`6d43efaff570b5c86e76b833ea0c6a04`

Zlib (1.2.3) - 485KB:

主页：[*http://www.zlib.net/*](http://www.zlib.net/)

下载：[*http://www.zlib.net/zlib-1.2.3.tar.gz*](http://www.zlib.net/zlib-1.2.3.tar.gz)

MD5 和：`debc62758716a169df9f62e6ab2bc634`

这些软件包总的大小为 180 MB

# 3.3\. 需要的补丁

除了下载软件包之外，还有一些补丁需要下载。这些补丁修正了本该由软件包开发者修正的错误；另外也做了一些小的修改，使得软件包之间可以更好的协同工作。构建 LFS 系统需要下列补丁：

Bash Upstream Fixes Patch - 23 KB:

下载：[*http://www.linuxfromscratch.org/patches/lfs/6.2/bash-3.1-fixes-8.patch*](http://www.linuxfromscratch.org/patches/lfs/6.2/bash-3.1-fixes-8.patch)

MD5 和：`bc337045fa4c5839babf0306cc9df6d0`

Bzip2 Bzgrep Security Fixes Patch - 1.2 KB:

下载：[*http://www.linuxfromscratch.org/patches/lfs/6.2/bzip2-1.0.3-bzgrep_security-1.patch*](http://www.linuxfromscratch.org/patches/lfs/6.2/bzip2-1.0.3-bzgrep_security-1.patch)

MD5 和：`4eae50e4fd690498f23d3057dfad7066`

Bzip2 Documentation Patch - 1.6 KB:

下载：[*http://www.linuxfromscratch.org/patches/lfs/6.2/bzip2-1.0.3-install_docs-1.patch*](http://www.linuxfromscratch.org/patches/lfs/6.2/bzip2-1.0.3-install_docs-1.patch)

MD5 和：`9e5dfbf4814b71ef986b872c9af84488`

Coreutils Internationalization Fixes Patch - 101 KB:

下载：[*http://www.linuxfromscratch.org/patches/lfs/6.2/coreutils-5.96-i18n-1.patch*](http://www.linuxfromscratch.org/patches/lfs/6.2/coreutils-5.96-i18n-1.patch)

MD5 和：`3df2e6fdb1b5a5c13afedd3d3e05600f`

Coreutils Suppress Uptime, Kill, Su Patch - 13 KB:

下载：[*http://www.linuxfromscratch.org/patches/lfs/6.2/coreutils-5.96-suppress_uptime_kill_su-1.patch*](http://www.linuxfromscratch.org/patches/lfs/6.2/coreutils-5.96-suppress_uptime_kill_su-1.patch)

MD5 和：`227d41a6d0f13c31375153eae91e913d`

Coreutils Uname Patch - 4.6 KB:

下载：[*http://www.linuxfromscratch.org/patches/lfs/6.2/coreutils-5.96-uname-1.patch*](http://www.linuxfromscratch.org/patches/lfs/6.2/coreutils-5.96-uname-1.patch)

MD5 和：`c05b735710fbd62239588c07084852a0`

Database (Berkeley) Upstream Fixes Patch - 3.8 KB:

下载：[*http://www.linuxfromscratch.org/patches/lfs/6.2/db-4.4.20-fixes-1.patch*](http://www.linuxfromscratch.org/patches/lfs/6.2/db-4.4.20-fixes-1.patch)

MD5 和：`32b28d1d1108dfcd837fe10c4eb0fbad`

Diffutils Internationalization Fixes Patch - 18 KB:

下载：[*http://www.linuxfromscratch.org/patches/lfs/6.2/diffutils-2.8.1-i18n-1.patch*](http://www.linuxfromscratch.org/patches/lfs/6.2/diffutils-2.8.1-i18n-1.patch)

MD5 和：`c8d481223db274a33b121fb8c25af9f7`

Expect Spawn Patch - 6.8 KB:

下载：[*http://www.linuxfromscratch.org/patches/lfs/6.2/expect-5.43.0-spawn-1.patch*](http://www.linuxfromscratch.org/patches/lfs/6.2/expect-5.43.0-spawn-1.patch)

MD5 和：`ef6d0d0221c571fb420afb7033b3bbba`

Gawk Segfault Patch - 1.3 KB:

下载：[*http://www.linuxfromscratch.org/patches/lfs/6.2/gawk-3.1.5-segfault_fix-1.patch*](http://www.linuxfromscratch.org/patches/lfs/6.2/gawk-3.1.5-segfault_fix-1.patch)

MD5 和：`7679530d88bf3eb56c42eb6aba342ddb`

GCC Specs Patch - 15 KB:

下载：[*http://www.linuxfromscratch.org/patches/lfs/6.2/gcc-4.0.3-specs-1.patch*](http://www.linuxfromscratch.org/patches/lfs/6.2/gcc-4.0.3-specs-1.patch)

MD5 和：`0aa7d4c6be50c3855fe812f6faabc306`

Glibc Linux Types Patch - 1.1 KB:

下载：[*http://www.linuxfromscratch.org/patches/lfs/6.2/glibc-2.3.6-linux_types-1.patch*](http://www.linuxfromscratch.org/patches/lfs/6.2/glibc-2.3.6-linux_types-1.patch)

MD5 和：`30ea59ae747478aa9315455543b5bb43`

Glibc Inotify Syscall Functions Patch - 1.4 KB:

下载：[*http://www.linuxfromscratch.org/patches/lfs/6.2/glibc-2.3.6-inotify-1.patch*](http://www.linuxfromscratch.org/patches/lfs/6.2/glibc-2.3.6-inotify-1.patch)

MD5 和：`94f6d26ae50a0fe6285530fdbae90bbf`

Grep RedHat Fixes Patch - 55 KB:

下载：[*http://www.linuxfromscratch.org/patches/lfs/6.2/grep-2.5.1a-redhat_fixes-2.patch*](http://www.linuxfromscratch.org/patches/lfs/6.2/grep-2.5.1a-redhat_fixes-2.patch)

MD5 和：`2c67910be2d0a54714f63ce350e6d8a6`

Groff Debian Patch - 360 KB:

下载：[*http://www.linuxfromscratch.org/patches/lfs/6.2/groff-1.18.1.1-debian_fixes-1.patch*](http://www.linuxfromscratch.org/patches/lfs/6.2/groff-1.18.1.1-debian_fixes-1.patch)

MD5 和：`a47c281afdda457ba4033498f973400d`

GRUB Disk Geometry Patch - 28 KB:

下载：[*http://www.linuxfromscratch.org/patches/lfs/6.2/grub-0.97-disk_geometry-1.patch*](http://www.linuxfromscratch.org/patches/lfs/6.2/grub-0.97-disk_geometry-1.patch)

MD5 和：`bf1594e82940e25d089feca74c6f1879`

Gzip Security Patch - 2 KB:

下载：[*http://www.linuxfromscratch.org/patches/lfs/6.2/gzip-1.3.5-security_fixes-1.patch*](http://www.linuxfromscratch.org/patches/lfs/6.2/gzip-1.3.5-security_fixes-1.patch)

MD5 和：`f107844f01fc49446654ae4a8f8a0728`

Inetutils GCC-4.x Fix Patch - 1.3 KB:

下载：[*http://www.linuxfromscratch.org/patches/lfs/6.2/inetutils-1.4.2-gcc4_fixes-3.patch*](http://www.linuxfromscratch.org/patches/lfs/6.2/inetutils-1.4.2-gcc4_fixes-3.patch)

MD5 和：`5204fbc503c9fb6a8e353583818db6b9`

Inetutils No-Server-Man-Pages Patch - 4.1 KB:

下载：[*http://www.linuxfromscratch.org/patches/lfs/6.2/inetutils-1.4.2-no_server_man_pages-1.patch*](http://www.linuxfromscratch.org/patches/lfs/6.2/inetutils-1.4.2-no_server_man_pages-1.patch)

MD5 和：`eb477f532bc6d26e7025fcfc4452511d`

Kbd Backspace/Delete Fix Patch - 11 KB:

下载：[*http://www.linuxfromscratch.org/patches/lfs/6.2/kbd-1.12-backspace-1.patch*](http://www.linuxfromscratch.org/patches/lfs/6.2/kbd-1.12-backspace-1.patch)

MD5 和：`692c88bb76906d99cc20446fadfb6499`

Kbd GCC-4.x Fix Patch - 1.4 KB:

下载：[*http://www.linuxfromscratch.org/patches/lfs/6.2/kbd-1.12-gcc4_fixes-1.patch*](http://www.linuxfromscratch.org/patches/lfs/6.2/kbd-1.12-gcc4_fixes-1.patch)

MD5 和：`615bc1e381ab646f04d8045751ed1f69`

Linux kernel UTF-8 Composing Patch - 11 KB:

下载：[*http://www.linuxfromscratch.org/patches/lfs/6.2/linux-2.6.16.27-utf8_input-1.patch*](http://www.linuxfromscratch.org/patches/lfs/6.2/linux-2.6.16.27-utf8_input-1.patch)

MD5 和：`d67b53e1e99c782bd28d879e11ee16c3`

Linux Libc Headers Inotify Patch - 4.7 KB:

下载：[*http://www.linuxfromscratch.org/patches/lfs/6.2/linux-libc-headers-2.6.12.0-inotify-3.patch*](http://www.linuxfromscratch.org/patches/lfs/6.2/linux-libc-headers-2.6.12.0-inotify-3.patch)

MD5 和：`8fd71a4bd3344380bd16caf2c430fa9b`

Mktemp Tempfile Patch - 3.5 KB:

下载：[*http://www.linuxfromscratch.org/patches/lfs/6.2/mktemp-1.5-add_tempfile-3.patch*](http://www.linuxfromscratch.org/patches/lfs/6.2/mktemp-1.5-add_tempfile-3.patch)

MD5 和：`65d73faabe3f637ad79853b460d30a19`

Module-init-tools Patch - 1.2 KB:

下载：[*http://www.linuxfromscratch.org/patches/lfs/6.2/module-init-tools-3.2.2-modprobe-1.patch*](http://www.linuxfromscratch.org/patches/lfs/6.2/module-init-tools-3.2.2-modprobe-1.patch)

MD5 和：`f1e452fdf3b8d7ef60148125e390c3e8`

Ncurses Fixes Patch - 8.2 KB:

下载：[*http://www.linuxfromscratch.org/patches/lfs/6.2/ncurses-5.5-fixes-1.patch*](http://www.linuxfromscratch.org/patches/lfs/6.2/ncurses-5.5-fixes-1.patch)

MD5 和：`0e033185008f21578c6e4c7249f92cbb`

Perl Libc Patch - 1.1 KB:

下载：[*http://www.linuxfromscratch.org/patches/lfs/6.2/perl-5.8.8-libc-2.patch*](http://www.linuxfromscratch.org/patches/lfs/6.2/perl-5.8.8-libc-2.patch)

MD5 和：`3bf8aef1fb6eb6110405e699e4141f99`

Readline Upstream Fixes Patch - 3.8 KB:

下载：[*http://www.linuxfromscratch.org/patches/lfs/6.2/readline-5.1-fixes-3.patch*](http://www.linuxfromscratch.org/patches/lfs/6.2/readline-5.1-fixes-3.patch)

MD5 和：`e30963cd5c6f6a11a23344af36cfa38c`

Sysklogd 8-Bit Cleanness Patch - 0.9 KB:

下载：[*http://www.linuxfromscratch.org/patches/lfs/6.2/sysklogd-1.4.1-8bit-1.patch*](http://www.linuxfromscratch.org/patches/lfs/6.2/sysklogd-1.4.1-8bit-1.patch)

MD5 和：`cc0d9c3bd67a6b6357e42807cf06073e`

Sysklogd Fixes Patch - 27 KB:

下载：[*http://www.linuxfromscratch.org/patches/lfs/6.2/sysklogd-1.4.1-fixes-1.patch*](http://www.linuxfromscratch.org/patches/lfs/6.2/sysklogd-1.4.1-fixes-1.patch)

MD5 和：`508104f058d1aef26b3bc8059821935f`

Tar GCC-4.x Fix Patch - 1.2 KB:

下载：[*http://www.linuxfromscratch.org/patches/lfs/6.2/tar-1.15.1-gcc4_fix_tests-1.patch*](http://www.linuxfromscratch.org/patches/lfs/6.2/tar-1.15.1-gcc4_fix_tests-1.patch)

MD5 和：`8e286a1394e6bcf2907f13801770a72a`

Tar Security Fixes Patch - 3.9 KB:

下载：[*http://www.linuxfromscratch.org/patches/lfs/6.2/tar-1.15.1-security_fixes-1.patch*](http://www.linuxfromscratch.org/patches/lfs/6.2/tar-1.15.1-security_fixes-1.patch)

MD5 和：`19876e726d9cec9ce1508e3af74dc22e`

Tar Sparse Fix Patch - 0.9 KB:

下载：[*http://www.linuxfromscratch.org/patches/lfs/6.2/tar-1.15.1-sparse_fix-1.patch*](http://www.linuxfromscratch.org/patches/lfs/6.2/tar-1.15.1-sparse_fix-1.patch)

MD5 和：`9e3623f7c88d8766878ecb27c980d86a`

Texinfo Multibyte Fixes Patch - 1.5 KB:

下载：[*http://www.linuxfromscratch.org/patches/lfs/6.2/texinfo-4.8-multibyte-1.patch*](http://www.linuxfromscratch.org/patches/lfs/6.2/texinfo-4.8-multibyte-1.patch)

MD5 和：`6cb5b760cfdd2dd53a0430eb572a8aaa`

Texinfo Tempfile Fix Patch - 2.2 KB:

下载：[*http://www.linuxfromscratch.org/patches/lfs/6.2/texinfo-4.8-tempfile_fix-2.patch*](http://www.linuxfromscratch.org/patches/lfs/6.2/texinfo-4.8-tempfile_fix-2.patch)

MD5 和：`559bda136a2ac7777ecb67511227af85`

Util-linux Cramfs Patch - 2.8 KB:

下载：[*http://www.linuxfromscratch.org/patches/lfs/6.2/util-linux-2.12r-cramfs-1.patch*](http://www.linuxfromscratch.org/patches/lfs/6.2/util-linux-2.12r-cramfs-1.patch)

MD5 和：`1c3f40b30e12738eb7b66a35b7374572`

Vim Upstream Fixes Patch - 42 KB:

下载：[*http://www.linuxfromscratch.org/patches/lfs/6.2/vim-7.0-fixes-7.patch*](http://www.linuxfromscratch.org/patches/lfs/6.2/vim-7.0-fixes-7.patch)

MD5 和：`d274219566702b0bafcb83ab4685bbde`

Vim Man Directories Patch - 4.2 KB:

下载：[*http://www.linuxfromscratch.org/patches/lfs/6.2/vim-7.0-mandir-1.patch*](http://www.linuxfromscratch.org/patches/lfs/6.2/vim-7.0-mandir-1.patch)

MD5 和：`b6426eb4192faba1e867ddd502323f5b`

Vim Spellfile Patch - 1.2 KB:

下载：[*http://www.linuxfromscratch.org/patches/lfs/6.2/vim-7.0-spellfile-1.patch*](http://www.linuxfromscratch.org/patches/lfs/6.2/vim-7.0-spellfile-1.patch)

MD5 和：`98e59e34cb6e16a8d4671247cebd64ee`

所有补丁总计约 773.8 KB

除了上面列出的必需补丁外，LFS 社区还创建了许多可选的补丁。这些可选补丁修正了某些不重要的问题，或者是开启了某些默认没有开启的功能。请仔细阅读位于 [*http://www.linuxfromscratch.org/patches/*](http://www.linuxfromscratch.org/patches/) 的补丁数据库，选择任何符合您需要的额外补丁。