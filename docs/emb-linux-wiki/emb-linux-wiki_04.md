# 工具箱

演示嵌入式 Linux 开发相关的各类工具，包括工具链、调试器和其他开发工具等信息，也有介绍到一些调试心得和技巧。

# 开发工具

介绍开发需要的各类工具，包括硬件逻辑分析仪、构建工具、集成开发环境、模拟器、基准测试程序、测试系统、源码管理、软件调试器、跟踪器和分析器等。

# Logic_Analyzers

> From: [eLinux.org](http://eLinux.org/Logic_Analyzers "http://eLinux.org/Logic_Analyzers")

# Logic Analyzers

A logic analyzer is an electronic instrument that displays signals in a digital circuit that are too fast to be observed and presents it to a user so that the user can more easily check correct operation of the digital system. They are typically used for capturing data in systems that have too many channels to be examined with an oscilloscope. Software running on the logic analyzer can convert the captured data into timing diagrams, protocol decodes, state machine traces, assembly language, or correlate assembly with source-level software.

When selecting a logic analyzer, make sure that the software package includes bus analyzers (I2C/SPI/UART are a given). Any tool worth purchasing will include support for these.

## Contents

*   1 Tools
    *   1.1 Linux
    *   1.2 Everyone Else
*   2 Information

# Tools

Since logic analyzers typically need software running on your development system, this list is split up to give preference to those tools which are fully supported on both Windows and Linux.

## Linux

*   [BitScope](http://www.bitscope.com/):
    *   100Mhz
    *   Cheapest one looks to be around 475 USD
*   [miniLA](http://minila.sourceforge.net/) - open source hardware!
*   [Saleae](http://www.saleae.com/):
    *   8 Inputs
    *   USB 2.0
    *   24MHz max
    *   Logic analyzer with RS232, SPI, I2C and 1-Wire Protocol Analyzer
    *   Can handle 1.8V logic ([Spec](http://www.saleae.com/logic/specs/) mentions *Input High Voltage: 2 to 5.25V*, but users report that 1.8V work, too)
    *   ~149 USD
*   [Sigma2](http://www.asix.net/tools/dbg_sigma.htm)
    *   16 inputs
    *   200MHz
    *   €198

## Everyone Else

*   [Acute](http://www.acute.com.tw/)
*   [ASIX](http://tools.asix.net/dbg_sigma.htm)
*   [TechTools](http://www.tech-tools.com/dv_main.htm)
*   [LogicPort](http://www.pctestinstruments.com/):
    *   Windows only SW
    *   up to 500Mhz?
    *   ~389 USD
*   [Zeroplus](http://www.zeroplus.com.tw/logic-analyzer_en/products.php) (e.g. LAP-C):
    *   Can handle 1.8V logic
    *   Can do decodes of the data
    *   Around 100USD depending of the device.
    *   Some devices seems to be available with Linux software (?)

# Information

*   [`en.wikipedia.org/wiki/Bus_analyzer`](http://en.wikipedia.org/wiki/Bus_analyzer)
*   [`en.wikipedia.org/wiki/Logic_analyzer`](http://en.wikipedia.org/wiki/Logic_analyzer)

[Category](http://eLinux.org/Special:Categories "Special:Categories"):

*   [Development Tools](http://eLinux.org/Category:Development_Tools "Category:Development Tools")

# Toolchains

> From: [eLinux.org](http://eLinux.org/Toolchains "http://eLinux.org/Toolchains")

# Toolchains

A [toolchain](http://en.wikipedia.org/wiki/Toolchain) is a set of distinct software development tools that are linked (or chained) together by specific stages such as GCC, binutils and glibc (a portion of the [GNU Toolchain](http://en.wikipedia.org/wiki/GNU_Toolchain)). Optionally, a toolchain may contain other tools such as a [Debugger](http://en.wikipedia.org/wiki/Debugger) or a [Compiler](http://en.wikipedia.org/wiki/Compiler) for a specific programming language, such as ,[C++](http://en.wikipedia.org/wiki/C%2B%2B). Quite often, the toolchain used for embedded development is a cross toolchain, or more commonly known as a [cross compiler](http://en.wikipedia.org/wiki/Cross_compiler). All the programs (like GCC) run on a host system of a specific architecture (such as x86) but produce binary code (executables) to run on a different architecture (e.g. ARM). This is called cross compilation and is the typical way of building embedded software. It is possible to compile natively, running gcc on your target. Before searching for a prebuilt toolchain or building your own, it's worth checking to see if one is included with your target hardware's [Board Support Package (BSP)](http://en.wikipedia.org/wiki/Board_support_package) if you have one.

## Contents

*   1 Introduction
*   2 Toolchain components
    *   2.1 Binutils
    *   2.2 C, C++, Java, Ada, Fortran, Objective-C compiler
    *   2.3 C library
    *   2.4 Debugger
    *   2.5 Lazarus and Free Pascal
*   3 Getting a toolchain
    *   3.1 Prebuilt toolchains
        *   3.1.1 CodeSourcery
        *   3.1.2 Linaro (ARM)
        *   3.1.3 DENX ELDK
        *   3.1.4 Scratchbox
        *   3.1.5 Fedora ARM
        *   3.1.6 Debian cross-tools packages
        *   3.1.7 Free Pascal
    *   3.2 Toolchain building systems
        *   3.2.1 Buildroot
        *   3.2.2 OpenADK
        *   3.2.3 Crossdev (Gentoo)
        *   3.2.4 Crosstool-NG
        *   3.2.5 Crossdev/tsrpm (Timesys)
        *   3.2.6 EmbToolkit
        *   3.2.7 OSELAS.Toolchain()
        *   3.2.8 Bitbake
*   4 By Platform
    *   4.1 ARM

## Introduction

When talking about toolchains, one must distinguish three different machines :

*   the build machine, on which the toolchain is built
*   the host machine, on which the toolchain is executed
*   the target machine, for which the toolchain generates code

From these three different machines, we distinguish four different types of toolchain building processes :

*   A native toolchain, as can be found in normal Linux distributions, has usually been compiled on x86, runs on x86 and generates code for x86.
*   A cross-compilation toolchain, which is the most interesting toolchain type for embedded development, is typically compiled on x86, runs on x86 and generates code for the target architecture (be it ARM, MIPS, PowerPC or any other architecture supported by the different toolchain components)
*   A cross-native toolchain, is a toolchain that has been built on x86, but runs on your target architecture and generates code for your target architecture. It's typically needed when you want a native gcc on your target platform, without building it on your target platform.
*   A canadian build is the process of building a toolchain on machine A, so that it runs on machine B and generates code for machine C. It's usually not really necessary.

## Toolchain components

### Binutils

The [GNU Binutils](http://www.gnu.org/software/binutils/) are the first component of a toolchain. The GNU Binutils contains two very important tools :

*   *as*, the assembler, that turns assembly code (generated by gcc) to binary
*   *ld*, the linker, that links several object code into a library, or an executable

Binutils also contains a couple of other binary file manipulation or analysis tools, such as objcopy, objdump, nm, readelf, strip, and so on. The Binutils website has some [documentation](http://sourceware.org/binutils/docs-2.19/) on all these tools.

### C, C++, Java, Ada, Fortran, Objective-C compiler

The second major component of a toolchain is the compiler. In the embedded Linux, the only realistic solution today is [GCC](http://gcc.gnu.org/), the GNU Compiler Collection. Nowadays, as input, it not only supports C, but also C++, Java, Fortran, Objective-C and Ada. As output, it supports a [very wide range](http://en.wikipedia.org/wiki/GNU_Compiler_Collection#Architectures) of architectures.

### C library

The C library implements the traditional POSIX API that can be used to develop userspace applications. It interfaces with the kernel through system calls, and provides higher-level services.

Realistically, there are nowadays two options for the C Library:

*   [glibc](http://en.wikipedia.org/wiki/Glibc) is the C library from the GNU project. It's the C library used by virtually all desktop and server GNU/Linux systems. It's feature-full, portable, complies to standards, but a bit bloated.
*   [Embedded GLIBC](http://www.eglibc.org/home) (EGLIBC) is a variant of the [GNU C Library](http://www.gnu.org/software/libc/) (GLIBC) optimized for embedded systems. Its goals include reduced footprint, support for cross-compiling and cross-testing, while maintaining source and binary compatibility with GLIBC. The project is discontinued.
*   [uClibc](http://en.wikipedia.org/wiki/Uclibc) is an alternate C library, which features a much smaller footprint. This library can be an interesting alternative if flash space and/or memory footprint is an issue. However, the space advantages gained using uClibc are becoming less important as the price of memory & flash continues to drop. It is still useful C library for embedded systems without MMU.
*   [uClibc-ng](http://www.uclibc-ng.org) is a spin-off of uClibc C library. Main goal of the spin-off is to do regular releases and do a lot of automatic runtime testing.
*   [musl](http://www.musl-libc.org) New standard C library. musl is lightweight, fast, simple, free, and strives to be correct in the sense of standards-conformance and safety.

The C library has a special relation with the C compiler, so the choice of the C library *must* be done when the toolchain is generated. Once the toolchain has been built, it is no longer possible to switch to another library.

### Debugger

The debugger is also usually part of the toolchain, as a cross-debugger is needed to debug applications running on your target machine. In the embedded Linux world, the typical debugger is [GDB](http://elinux.org/GDB).

### Lazarus and Free Pascal

[Free Pascal](http://eLinux.org/Free_Pascal "Free Pascal") is a professional but free 32 bit / 64 bit compiler for [Pascal](http://eLinux.org/Pascal "Pascal") and [ObjectPascal](http://eLinux.org/ObjectPascal "ObjectPascal"). It supports a wide variety of processors and [Linux](http://eLinux.org/index.php?title=Linux&action=edit&redlink=1 "Linux (page does not exist)") distributions including the [Raspberry Pi](http://eLinux.org/Raspberry_Pi "Raspberry Pi").

The Free Pascal toolchain is widely independent from GCC and other external tools. Major components are the Free Pascal compiler (FPC), a command-line tool, a text-mode IDE and, as an optional component, [Lazarus](http://eLinux.org/Lazarus "Lazarus"), a full-featured GUI-based IDE. FPCUnit is a framework allowing for unit-testing.

On most platforms Free Pascal makes use of the [GDB](http://eLinux.org/GDB "GDB") debugger.

[`elinux.org/Tiny6410`](http://elinux.org/Tiny6410)

[`elinux.org/Micro2440`](http://elinux.org/Micro2440)

[`elinux.org/Mini210`](http://elinux.org/Mini210)

[`elinux.org/Tiny210`](http://elinux.org/Tiny210)

## Getting a toolchain

There are several ways to get a toolchain :

*   Get a prebuilt toolchain, either from a vendor such as [CodeSourcery](http://www.codesourcery.com/), or probably inside the [Board Support Package](http://en.wikipedia.org/wiki/Board_support_package) shipped with your hardware platform by the vendor. This is the easiest solution, as the toolchain is already built, and has supposedly been tested by the vendor. The drawback is that you don't have flexibility on your toolchain features (which C library ? hard-float or soft-float ? which ABI ?)
*   Build a toolchain on your own. However, this can be a real pain. There are version dependency issues, patches required to make something work etc. etc. Check out this (obsolete) [build matrix](http://kegel.com/crosstool/crosstool-0.43/buildlogs/) for crosstool and look at all the red "failed" entries.
*   Build a toolchain using an automated tool. The community has built several scripts or more elaborate systems to ease the process of building a toolchain. This way, the recipes and patches needed to build a toolchain made of particular versions of the various components are shared and easily available.

### Prebuilt toolchains

#### CodeSourcery

[CodeSourcery](http://www.codesourcery.com/) develops [Sourcery G++](http://www.codesourcery.com/gnu_toolchains/sgpp/), an [Eclipse](http://en.wikipedia.org/wiki/Eclipse_IDE) based [Integrated Development Environment (IDE)](http://en.wikipedia.org/wiki/Integrated_development_environment) that incorporates the [GNU Toolchain](http://en.wikipedia.org/wiki/GNU_toolchain) (gcc, gdb, etc.) for cross development for numerous target architectures. [CodeSourcery](http://www.codesourcery.com/) provides a "lite" version for [ARM](http://www.codesourcery.com/gnu_toolchains/arm), [Coldfire](http://www.codesourcery.com/gnu_toolchains/coldfire), [MIPS](http://www.codesourcery.com/gnu_toolchains/mips), [SuperH](http://www.codesourcery.com/gnu_toolchains/sgpp/lite/superh) and [Power](http://www.codesourcery.com/gnu_toolchains/power) architectures. The toolchains are always very up to date. [CodeSourcery](http://www.codesourcery.com/) contributes enhancements it makes to the [GNU Toolchain](http://en.wikipedia.org/wiki/GNU_toolchain) upstream continually, making it the single largest (by patch count) corporate contributor.

#### Linaro (ARM)

[Linaro](http://linaro.org/) releases [optimized toolchains](https://wiki.linaro.org/WorkingGroups/ToolChain) for recent ARM CPUs (Cortex A8, A9...). These include Linaro's latest contributions to mainline gcc, but backported to stable gcc versions for immediate use by product developers. Linaro actually hires CodeSourcery people to improve ARM toolchains, so the ARM toolchains that you get with Linaro should be at least as good as the CodeSourcery ones.

Native toolchains are available through the standard gcc toolchain in Ubuntu. Cross toolchains are available to Ubuntu users through special packages:

```
sudo add-apt-repository ppa:linaro-maintainers/toolchain
sudo apt-get install gcc-arm-linux-gnueabi 
```

Now find out the path and name of the cross-compiler executable by looking at the contents of the package:

```
dpkg -L gcc-arm-linux-gnueabi 
```

Arch Linux users can install:

```
yaourt -S gcc-linaro-arm-linux-gnueabihf 
```

Linaro also makes source releases which can then be used by any build system (see below).

#### DENX ELDK

The DENX Embedded Linux Development Kit (ELDK) provides a complete and powerful software development environment for embedded and real-time systems. It is available for ARM, PowerPC and MIPS processors and consists of:

*   Cross Development Tools (Compiler, Assembler, Linker etc.) to develop software for the target system.
*   Native Tools (Shell, commands and libraries) which provide a standard Linux development environment that runs on the target system.
*   Firmware (U-Boot) that can be easily ported to new boards and processors.
*   Linux kernel including the complete source-code with all device drivers, board-support functions etc.
*   Xenomai - RTOS Emulation framework for systems requiring hard real-time responses.
*   SELF (Simple Embedded Linux Framework) as fundament to build your embedded systems on.

All components of the ELDK are available for free with complete source code under GPL and other Free Software Licenses. Also, detailed instructions to rebuild all the tools and packages from scratch are included.

The ELDK can be downloaded for free from several mirror sites or ordered on CD-ROM for a nominal charge (99 Euro). To order the CD please contact office@denx.de

Detailed information about the ELDK is available [here](http://www.denx.de/wiki/DULG/ELDK).

#### Scratchbox

[Scratchbox](http://www.scratchbox.org/) provides toolchains for ARM and x86 target architectures (with PowerPC, MIPS and CRIS in experimental stages but aren't making real progress since years, so Scratchbox should probably be considered ARM and x86 only). Both uClibc and glibc are supported.

Scratchbox simplifies cross compiling software which is built using GNU autotools - Code tests performed by configure are run in an emulator or even on the actual target. The toolchains scratchbox ships with are based on gcc 3.3 and as such are quite old, but stable and well tested. It should be pointed out that scripts to build custom toolchains are also provided with scratchbox allowing more recent gcc versions to be used.

#### Fedora ARM

Fedora ARM is a try to port Fedora to ARM. It provides some tools as an ARM toolchain packaged in RPM format. Link: [Fedora ARM](http://fedoraproject.org/wiki/Architectures/ARM)

#### Debian cross-tools packages

For Debian users, the toolchains problem is fairly reliably solved.

For a debian-based box just install pre-built cross toolchains from Debian experimental.

Targets include nearly all debian-supported architectures As of this writing supported compiler is gcc 4.9\. You can get older unsupported compilers from emdebian.

You will need to add the target architecture to your list of installable architectures. eg.

```
 dpkg --add-architecture armhf
 apt-get update
 apt-get install gcc-arm-linux-gnueabihf 
```

#### Free Pascal

Free Pascal is available for several processor architectures from [`www.freepascal.org`](http://www.freepascal.org/download.var), the Lazarus IDE from [`www.lazarus.freepascal.org`](http://www.lazarus.freepascal.org).

### Toolchain building systems

#### Buildroot

[Buildroot](http://www.buildroot.net) is a complete build system based on the Linux Kernel configuration system and supports a wide range of target architectures. It generates root file system images ready to be written to flash. In addition to having a huge number of packages which can be compiled into the image, it also generates a cross toolchain to build those packages from source. Even if you don't want to use buildroot for your root filesystem, it is a useful tool for generating a toolchain. Buildroot supports [uClibc](http://uclibc.org), [glibc](http://www.gnu.org/software/libc/), [eglibc](http://eglibc.org), and [musl](http://www.musl-libc.org/).

#### OpenADK

[OpenADK](http://www.openadk.org) is a complete build system based on the Linux Kernel configuration system and supports a wide range of target architectures. It is similar to buildroot. It generates root file system images ready to be written to flash. In addition to having a huge number of packages which can be compiled into the image, it also generates a cross toolchain to build those packages from source. Even if you don't want to use OpenADK for your root filesystem, it is a useful tool for generating a toolchain. OpenADK supports [uClibc](http://uclibc.org), [glibc](http://www.gnu.org/software/libc/), [uClibc-ng](http://uclibc-ng.org), and [musl](http://www.musl-libc.org/).

#### Crossdev (Gentoo)

Crossdev is specific to developers using Gentoo for their development PCs. It is a script which generates a cross toolchain using the portage build scripts for gcc etc. There are numerous architectures which are supported and both uClibc and glibc toolchains can be built. Link: [Gentoo Crossdev info](http://en.gentoo-wiki.com/wiki/Crossdev)

#### Crosstool-NG

[Crosstool-NG](http://crosstool-ng.org/) is a well-maintained fork of crosstool, targeted at easier configuration, re-factored code, and a learning base on how toolchains are built, with support for both uClibc and glibc, for debug tools (gdb, strace, dmalloc...), and a wide range of versions for each tools. Different target architectures are supported as well. It offers a kernel-like configuration system to select the different configuration options of the toolchain (component versions, component configuration, etc.). Crosstool-NG has an active and responsive user and developer community.

#### Crossdev/tsrpm (Timesys)

Crossdev is a project sponsored by Timesys, completely unrelated to the Gentoo cross toolchain generation system. The projects main focus is on a tool called tsrpm which is used to build cross development toolchains and generate cross-compiled software packages. Currently only x86 and select PowerPC architectures are supported. Link: [Crossdev](https://crossdev.timesys.com/)

#### EmbToolkit

[EmbToolkit](http://www.embtoolkit.org/) is the first toolchain building system giving the choice to generate GCC or [llvm/clang](http://www.llvm.org) based toolchain. *EmbToolkit* supports use of eglibc, glibc or uClibc as C Library and [musl C library](http://www.musl-libc.org) is also planned at time of writing. *EmbToolkit* can be used to generate only a toolchain (usable in a external project), but it is also possible to generate various root filesystems.

#### OSELAS.Toolchain()

The OSELAS.Toolchain() project aims at supplying a complete build system for recent GNU toolchains. It uses the PTXdist build system, a userland build system based on Kconfig. The current version 1.99.3.1 of OSELAS.Toolchain() contains support for arm, x86, avr, mips and PowerPC. In addition, there are toolchains for bare metal platforms like Cortex-M3 and AVR-8-Bit.

OSELAS.Toolchain() Feature matrix (v1.99.1, build with PTXdist v1.99.7)

Architeture

CPUtype

gcc

glibc

binutils

kernel header

ARM (eabi)

1136jfs,xscale(-hf),iwmmx,v4t(-hf),v5te,xscale

4.1.2, 4.3.2

2.5, 2.8

2.17, 2.18

2.6.18, 2.6.27

AVR

n/a

3.4.6, 4.1.2, 4.3.2

1.0.5, 1.4.8, 1.6.2

2.17

n/a

X86

i586, i686

4.1.2, 4.3.2

2.5, 2.8

2.17, 2.18

2.6.18, 2.6.27

PowerPC

603e

4.1.2, 4.3.2

2.5, 2.8

2.17, 2.18

2.6.18, 2.6.27

MIPS

mipsel (softfloat)

4.2.3

2.8

2.18

2.6.27

OSELAS.Toolchain() contains some further goodies like gcj support for ARM and mingw Support for x86\. Link: [OSELAS.Toolchain()](http://www.pengutronix.de/oselas/toolchain/index_en.html) Link: [PTXdist](http://www.pengutronix.de/software/ptxdist/index_en.html)

#### Bitbake

Bitbake is the tool used by [OpenEmbedded](http://wiki.openembedded.net/index.php/Main_Page). The best way to get started is probably by just building an existing distribution that uses openembedded (e.g. Ångström, see [`www.angstrom-distribution.org/building-ångström`](http://www.angstrom-distribution.org/building-ångström) for details).

## By Platform

### ARM

See [ARMCompilers](http://eLinux.org/ARMCompilers "ARMCompilers")

[Category](http://eLinux.org/Special:Categories "Special:Categories"):

*   [Development Tools](http://eLinux.org/Category:Development_Tools "Category:Development Tools")

# Build Systems

> From: [eLinux.org](http://eLinux.org/Build_Systems "http://eLinux.org/Build_Systems")

# Build Systems

*   [Open Embedded](http://eLinux.org/Open_Embedded "Open Embedded") - System for building full embedded images from scratch. Note that this is used by the [Yocto Project](http://eLinux.org/Yocto_Project "Yocto Project") as it's build system (but see note below for more detail).
*   [Buildroot](http://eLinux.org/Buildroot "Buildroot") - Easy-to-use embedded Linux build system
*   [OpenADK](http://www.openadk.org) - Open Source Appliance Development Kit
*   [PTXdist](http://www.pengutronix.de/software/ptxdist/index_en.html)
    *   Kconfig based build system developed by [Pengutronix](http://www.pengutronix.de/index_en.html)
    *   GPL licensed
    *   [Video](http://free-electrons.com/pub/video/2009/fosdem/fosdem2009-schwebel-ptxdist.ogv) of a talk given by PTXdist maintainer Robert Schwebel at [FOSDEM 2009](http://www.fosdem.org)
*   [Linux From Scratch](http://www.linuxfromscratch.org/)
*   [Nard SDK](http://www.arbetsmyra.dyndns.org/nard/) For industrial embedded systems
*   LTIB - Linux Target Image Builder (by Stuart Hughes of FreeScale) - see [`savannah.nongnu.org/projects/ltib`](http://savannah.nongnu.org/projects/ltib)
    *   [Slides](http://www.bitshrine.org/celf_ltib_bof_v1.2.pdf) and [video](http://free-electrons.com/pub/video/2008/ols/ols2008-stuart-hughes-ltib.ogg) of a talk on LTIB at the Ottawa Linux Symposium 2008
*   [OpenBricks](http://eLinux.org/index.php?title=OpenBricks&action=edit&redlink=1 "OpenBricks (page does not exist)")
    *   Embedded Linux Framework
    *   OpenBricks provides a set of packages, patches and shell-based rules that creates a toolchain and a rootfs with customized packages and features selection.
    *   Currently supports x86_32, x86_64, PowerPC, PowerPC64 and ARM architectures with either uClibc, Glibc or eGlibc C library.
*   [Building Embedded Userlands](http://www.mvista.com/download/fetchdoc.php?docid=342) - Presentation by Ned Miljevic & Klaas van Gend at the ELC 2008 which compares different configuration and build systems. [Video](http://free-electrons.com/pub/video/2008/elce/elce2008-miljevic-van-gend-embedded-userlands.ogv) of the conference available.
*   [Scratchbox](http://eLinux.org/Scratchbox "Scratchbox") Cross-Compilation Toolkit, with support for x86 and arm.
*   [OpenWRT](http://eLinux.org/Open_Wrt "Open Wrt") Cross-Compilation Toolkit mainly geared towards wireless routers but can be extended to other platforms, with support for x86, MIPS and ARM.
*   [Yocto Project](http://eLinux.org/Yocto_Project "Yocto Project") - The Yocto Project is an umbrella project which uses [Open Embedded](http://eLinux.org/Open_Embedded "Open Embedded") as it's build system.
    *   I have heard it argued that the Poky meta-data for Open Embedded actually constitute the primary build system for the Yocto Project. Since Open Embedded somewhat conflates the package data and the build scripts in the recipe files, there is some truth to this.

See also Toolchains

[Category](http://eLinux.org/Special:Categories "Special:Categories"):

*   [Development Tools](http://eLinux.org/Category:Development_Tools "Category:Development Tools")

# Embedded Linux Distributions

> From: [eLinux.org](http://eLinux.org/Embedded_Linux_Distributions "http://eLinux.org/Embedded_Linux_Distributions")

# Embedded Linux Distributions

## Contents

*   1 Introduction
*   2 Vendor distros
*   3 Platforms
*   4 Other distros
    *   4.1 Special purpose embedded Linux distributions
*   5 Configuration and Build systems
*   6 Obsolete things
*   7 Further reading

## Introduction

Besides the Linux kernel, one of the advantage of embedded Linux is the ability to leverage hundreds if not thousands of existing free and open source packages to easily and quickly add new features to devices. These packages range from graphical libraries, multimedia libraries, network libraries, cryptographic libraries, network servers, infrastructure software and more. However, integrating all these components together requires a relatively deep knowledge of the components. Hence, embedded-specific distributions and build systems have been created to ease this process.

## Vendor distros

*   The [Blackfin uClinux Distribution](http://blackfin.uclinux.org/gf/project/uclinux-dist) by [Analog Devices](http://www.analog.com/blackfin) - a fork of the uClinux distribution for Blackfin processors
*   Embedded Alley - see [`www.embeddedalley.com/`](http://www.embeddedalley.com/)
*   [KaeilOS embedded linux](http://www.kaeilos.com)
*   Lineo Solutions [uLinux](http://www.lineo.co.jp/eng/products-services/products/ulinux.html)
*   [MontaVista](http://eLinux.org/MontaVista "MontaVista") Linux - see [`www.mvista.com/products_services.php`](http://www.mvista.com/products_services.php)
*   [Pengutronix](http://www.pengutronix.de) OSELAS.BSP() - see [`www.pengutronix.de/oselas/bsp/index_en.html`](http://www.pengutronix.de/oselas/bsp/index_en.html)
*   [RidgeRun](http://eLinux.org/RidgeRun "RidgeRun") Linux - see [`www.ridgerun.com/sdk.shtml`](http://www.ridgerun.com/sdk.shtml)
*   [TimeSys](http://eLinux.org/TimeSys "TimeSys") LinuxLink - see [`www.timesys.com/embedded-linux/linuxlink`](http://www.timesys.com/embedded-linux/linuxlink)
*   [Ubuntu Mobile](http://wiki.ubuntu.com/MobileAndEmbedded)
*   Wind River - see [`www.windriver.com/products/linux/`](http://www.windriver.com/products/linux/)
*   [Little Blue Linux](http://www.littlebluelinux.com/) - MPC Data
*   [Digi Embedded Linux](http://www.digi.com/products/embeddedsolutions/softwareservices/digiembeddedlinux.jsp) for Digi's ARM based modules

## Platforms

*   [Android](http://eLinux.org/Android_Portal "Android Portal")
*   Maemo (deprecated - see Meego)
*   [Meego](http://eLinux.org/Meego "Meego")
*   Moblin (deprecated - see Meego)
*   OpenMoko
*   Access Linux Platform
*   LIMO

## Other distros

*   Snapgear Embedded Linux Distribution - [`www.snapgear.org/`](http://www.snapgear.org/)
*   [Open Wrt](http://eLinux.org/Open_Wrt "Open Wrt") - [`openwrt.org/`](http://openwrt.org/)
*   Embedded Debian - [`www.emdebian.org/`](http://www.emdebian.org/)
    *   Emdebian has made some significant progress the last few years, and has now reached an usable state. [Two versions of Emdebian](http://www.emdebian.org/emdebian/flavours.html) are available : Emdebian Grip and Emdebian Crush.
    *   Neil Williams gave a talk *Emdebian 1.0 release, small and super small Debian* at the FOSDEM 2009\. A [video](http://free-electrons.com/pub/video/2009/fosdem/fosdem2009-williams-emdebian-1.0-release.ogv) is available.
*   Embedded Gentoo - [`embedded.gentoo.org/`](http://embedded.gentoo.org/)
*   Arch Linux for ARM - [`archlinuxarm.org/`](http://archlinuxarm.org/)
*   [GeeXboX](http://eLinux.org/GeeXboX "GeeXboX") - [`www.geexbox.org/`](http://www.geexbox.org/)
    *   GeeXboX is an embedded multimedia distribution that turns your device into a full-featured MediaCenter.
*   Aboriginal Linux - [`landley.net/aboriginal/`](http://landley.net/aboriginal/)

### Special purpose embedded Linux distributions

*   [Flash Linux](http://flashlinux.org.uk/) - a distribution specifically for USB keys and Live CDs
*   Eagle Linux - [`www.safedesksolutions.com/eaglelinux/`](http://www.safedesksolutions.com/eaglelinux/)
    *   An embedded Linux distribution aimed at helping users learn Linux by creating bootable Linux images "virtually from scratch". Eagle Linux 2.3 is currently distributed as a concise, 26-page PDF documenting the creation of a minimalist, network-ready Linux image for bootable CDs, floppies, or flash drives. See description at: [`ct.enews.deviceforge.com/rd/cts?d=207-106-2-28-5560-8662-0-0-0-1`](http://ct.enews.deviceforge.com/rd/cts?d=207-106-2-28-5560-8662-0-0-0-1)
*   [uClinux](http://www.uclinux.org/) A distribution targeting (but not only) systems without Memory Management Unit. See also [UClinux Shared Library](http://eLinux.org/UClinux_Shared_Library "UClinux Shared Library").

## Configuration and Build systems

See Build Systems

See also Toolchains

## Obsolete things

*   [Qplus Target Builder](http://eLinux.org/Qplus_Target_Builder "Qplus Target Builder")
    *   Target image builder from ETRI

## Further reading

*   [Embedded OS](http://eLinux.org/Embedded_OS "Embedded OS") mentions a variety of embedded operating systems, including embedded Linux.
*   [Tools and distributions for embedded Linux development](http://lwn.net/Articles/384713/) - LWN.net 2010/04/27 by Tom Parkin
    *   This is an excellent roundup of current (as of 2010) tools and distributions available for embedded Linux development (that's redundant).

[Category](http://eLinux.org/Special:Categories "Special:Categories"):

*   [Linux](http://eLinux.org/Category:Linux "Category:Linux")

# Debuggers

> From: [eLinux.org](http://eLinux.org/Debuggers "http://eLinux.org/Debuggers")

# Debuggers

Debugging is one of the most common activities of an embedded developer. Here are some debuggers howto and links:

*   [Open On-Chip Debugger](http://openocd.sourceforge.net/)
    *   [OpenOCD](http://eLinux.org/OpenOCD "OpenOCD") local pages.
*   [GDB - The GNU debugger](http://eLinux.org/GDB "GDB")
*   [DDD - Data Display Debugger](http://www.gnu.org/software/ddd/)
*   kgdb - kernel source level debugger
*   KDB - In-kernel debugger
*   [valgrind - memory, cache and other debuggers and profilers](http://eLinux.org/Valgrind "Valgrind")

[Category](http://eLinux.org/Special:Categories "Special:Categories"):

*   [Development Tools](http://eLinux.org/Category:Development_Tools "Category:Development Tools")

# Debug Assist Boards

> From: [eLinux.org](http://eLinux.org/Debug_Assist_Boards "http://eLinux.org/Debug_Assist_Boards")

# Debug Assist Boards

Here are some debug assist boards that you might find useful.

## Contents

*   1 List of debug-assist boards for embedded Linux
    *   1.1 Jtag boards
    *   1.2 Control boards
    *   1.3 Power Measurement

## List of debug-assist boards for embedded Linux

### Jtag boards

*   Olimex JTAG USB OCD device - see [`www.sparkfun.com/products/7834`](https://www.sparkfun.com/products/7834)
    *   has USB JTAG, RS232 (full modem signals supported) port; and power supply all in one compact device
*   Abatron BDI3000 - see [`www.abatron.ch/products/bdi-family/bdi3000.html`](http://www.abatron.ch/products/bdi-family/bdi3000.html)

### Control boards

*   [Sony Debug Assist board](http://eLinux.org/Sony_Debug_Assist_board "Sony Debug Assist board") (also known as CDB Assist)
    *   provides power supply, USB pass-through to host, and control of 3 "buttons" via USB connection on host
*   [Target Switch Control From Parallel Port](http://eLinux.org/Target_Switch_Control_From_Parallel_Port "Target Switch Control From Parallel Port")
    *   an old design for controlling switches using a computer's parallel port lines

### Power Measurement

*   BayLibre ACME cape (for BeagleBone Black) - see [`baylibre.com/acme/`](http://baylibre.com/acme/)
    *   supports different power measurement probes via a standard connector
    *   presentation on issues at: [`events.linuxfoundation.org/sites/events/files/slides/Leveraging_Open-Source_Power_Measurement_Standard_Solution_0.pdf`](http://events.linuxfoundation.org/sites/events/files/slides/Leveraging_Open-Source_Power_Measurement_Standard_Solution_0.pdf)

# Memory Debuggers

> From: [eLinux.org](http://eLinux.org/Memory_Debuggers "http://eLinux.org/Memory_Debuggers")

# Memory Debuggers

Several tools exist for finding memory leaks or for reporting individual memory allocations of a program. These tools help analyze memory usage patterns, detect unbalanced allocations and frees, report buffer over- and under-runs, etc.

## Contents

*   1 mtrace
*   2 memwatch
*   3 mpatrol
*   4 dmalloc
*   5 dbgmem
*   6 valgrind
*   7 Electric Fence
*   8 Tutorials or Overviews

### mtrace

mtrace is a builtin part of glibc which allows detection of memory leaks caused by unbalanced malloc/free calls. To use it, the program is modified to call mtrace() and muntrace() to start and stop tracing of allocations. A log file is created, which can then be scanned by the 'mtrace' Perl script. The 'mtrace' program lists only unbalanced allocations. If source is available it can show the source line where the problem occurred. mtrace can be used on both C and C++ programs.

See the [mtrace wikipedia article](http://wikipedia.org/wiki/Mtrace) for more information.

### memwatch

memwatch is a program that not only detects malloc and free errors but also reads and writes beyond the allocated space (buffer over and under-runs). To use it, you modify the source to include the memwatch code, which provides replacements for malloc and free.

Some things that memwatch does not catch are writing to an address that has been freed and reading data from outside the allocated memory.

### mpatrol

mpatrol appears to be like memwatch.

See [`mpatrol.sourceforge.net/`](http://mpatrol.sourceforge.net/)

### dmalloc

"The debug memory allocation or dmalloc library has been designed as a drop in replacement for the system's malloc, realloc, calloc, free and other memory management routines while providing powerful debugging facilities configurable at runtime. These facilities include such things as memory-leak tracking, fence-post write detection, file/line number reporting, and general logging of statistics."

This library can be used without modifying the existing program, and uses environment variables to control it's operation and set of issues to log.

It's home page is at: [`dmalloc.com/`](http://dmalloc.com/)

See Cal Erickson's article (link below, page 2) for information about using this system.

### dbgmem

dbgmem looks like another dynamic library replacement tool, similar to dmalloc (but possibly having less features)

See [`dbgmem.sourceforge.net/`](http://dbgmem.sourceforge.net/)

### valgrind

valgrind does dynamic binary instrumentation to analyze the program, and provides a number of memory problem detection tools and profiling tools. Unfortunately, as of July 2010 it is only available for x86 and ppc64 architecture platforms.

See [Valgrind](http://eLinux.org/Valgrind "Valgrind")

### Electric Fence

See [Electric Fence](http://eLinux.org/Electric_Fence "Electric Fence")

## Tutorials or Overviews

*   [Memory Leak Detection in Embedded Systems](http://www.linuxjournal.com/article/6059) by Cal Erickson, Linux Journal, September 2002
    *   This article mentions mtrace, memwatch and dmalloc

[Category](http://eLinux.org/Special:Categories "Special:Categories"):

*   [Development Tools](http://eLinux.org/Category:Development_Tools "Category:Development Tools")

# Tools

> From: [eLinux.org](http://eLinux.org/Tools "http://eLinux.org/Tools")

# Tools

*   [addr2line for kernel debugging](http://eLinux.org/Addr2line_for_kernel_debugging "Addr2line for kernel debugging")
*   [decode an ioctl](http://eLinux.org/Ioctl "Ioctl")
*   [Exception Analysis tools](http://eLinux.org/Exception_Analysis_tools "Exception Analysis tools")
    *   This includes things like oops emitters, exception monitors, coredump analysis, etc.
*   [PgpKey](http://eLinux.org/PgpKey "PgpKey")
*   [Ttc](http://eLinux.org/Ttc "Ttc") - target control tool

# Integrated Development Environments

> From: [eLinux.org](http://eLinux.org/Integrated_Development_Environments "http://eLinux.org/Integrated_Development_Environments")

# Integrated Development Environments

*   [Eclipse](http://eLinux.org/Eclipse "Eclipse") - Powerful IDE written in JAVA.
*   [Free Pascal](http://eLinux.org/Free_Pascal "Free Pascal") supports both a text-mode IDE and Lazarus (see below)
*   [jEdit](http://www.jedit.org) - Editor written in JAVA which can be expanded to a full IDE with plug-ins.
*   [KDevelop](http://www.kdevelop.org) - Standard IDE for KDE.
*   [Lazarus](http://eLinux.org/Lazarus "Lazarus"), a cross-platform IDE for Free Pascal, a 32bit / 64bit professional ObjectPascal compiler.
*   [Emacs](http://www.gnu.org/software/emacs) - Powerful IDE, extensible in LISP, ships with modes to integrates with SCM (GIT, SVN, CVS...), build systems, debugger and even fancy multi-window with [ECB](http://ecb.sourceforge.net/).
*   [VIm](http://www.vim.org/) - Powerful IDE, extensible with scripting, can use various modules for completion and more.
*   [KScope](http://kscope.sourceforge.net/) - Cscope based source editing environment with KDE.
*   [Anjuta](http://www.anjuta.org) - IDE with nice plugin support
*   [QtCreator](http://qt.nokia.com/products/developer-tools/) is an nice IDE which has code completion, remote deployment (with version 2.3) and Outline view. It also has an VIm mode. Its menus are much cleaner than these from Eclipse and its easier to get started with this ide than Eclipse for that very reason.
*   [Embest IDE for ARM](http://www.armkits.com/Product/idemain.asp)

[Category](http://eLinux.org/Special:Categories "Special:Categories"):

*   [Development Tools](http://eLinux.org/Category:Development_Tools "Category:Development Tools")

# Emulators

> From: [eLinux.org](http://eLinux.org/Emulators "http://eLinux.org/Emulators")

# Emulators

*   [Qemu - hardware emulator](http://eLinux.org/Qemu "Qemu") - for everything (try this first)
*   [Skyeye](http://www.skyeye.org/) - for ARM
*   [Aranym](http://aranym.org) - for M68K
*   [Hercules](http://www.hercules-390.org) - For S390
*   [UNetICE](http://www.armkits.com/Product/unetice.asp) for ARM7 and ARM9 processors from [Embest](http://www.armkits.com)
*   [XDS100v2](http://www.armkits.com/Product/xds100.asp) for TI processor available from [Embest](http://www.armkits.com)

# Tracers and Profilers

> From: [eLinux.org](http://eLinux.org/Tracers_and_Profilers "http://eLinux.org/Tracers_and_Profilers")

# Tracers and Profilers

*   [Strace](http://eLinux.org/Strace "Strace") - trace system calls by a program (or set of programs) (very handy)
*   [ltrace](http://eLinux.org/index.php?title=Ltrace&action=edit&redlink=1 "Ltrace (page does not exist)")
    *   trace library calls
*   see Kernel Trace Systems
*   see [Profilers](http://eLinux.org/Profilers "Profilers")

[Category](http://eLinux.org/Special:Categories "Special:Categories"):

*   [Development Tools](http://eLinux.org/Category:Development_Tools "Category:Development Tools")

# Benchmarks

> From: [eLinux.org](http://eLinux.org/Benchmark_Programs "http://eLinux.org/Benchmark_Programs")

# Benchmark Programs

Here are some different programs for performing benchmarking.

*Note: It is important to recognize that benchmarks between systems may be misleading.* Benchmarks should primarily be used to determine differences in performance for different software configurations on the **same** hardware system.

## Contents

*   1 Unix Bench
*   2 lmbench
    *   2.1 Instructions for lmbench-3.0-a9
*   3 Wishlist

## Unix Bench

FYI, the URL to the UnixBench is as follows;

OLD site: [`www.tux.org/pub/tux/benchmarks/System/unixbench/`](http://www.tux.org/pub/tux/benchmarks/System/unixbench/)

NEW site: [`code.google.com/p/byte-unixbench/`](http://code.google.com/p/byte-unixbench/)

UnixBench contains 9 kinds of tests:

1.  Dhrystone 2 using register variables
2.  Double-Precision Whetstone
3.  Execl Throughput
4.  File Copy
5.  Pipe Throughput
6.  Pipe-based Context Switching
7.  Process Creation
8.  Shell Script
9.  System Call Overhead

## lmbench

The LMBench home page is at: [`www.bitmover.com/lmbench/`](http://www.bitmover.com/lmbench/) and/or [`lmbench.sourceforge.net/`](http://lmbench.sourceforge.net/) The sourceforge project page is at: [`sourceforge.net/projects/lmbench`](http://sourceforge.net/projects/lmbench)

### Instructions for lmbench-3.0-a9

(Adjust CC and OS according to your needs.)

```
cd lmbench-3.0-a9/src
make CC=arm-linux-gcc OS=arm-linux TARGET=linux 
```

Make the whole lmbench-3.0-a9 directory accessible on the target, e.g. by copying or NFS mount. Make sure the benchmark scripts can write the configuration file and results, and also unpack a tarball used during the benchmark (in case tar is not available on target):

```
chmod a+w ../bin/arm-linux ../results
tar xf webpage-lm.tar 
```

To run the benchmark on the target:

```
cd lmbench-3.0-a9/src
hostname foo    # make sure hostname is set, the scripts use it to name config and result files
OS=arm-linux ../scripts/config-run
OS=arm-linux ../scripts/results 
```

This worked for me on a target using BusyBox v1.10.2 ash.

The results are written into lmbench-3.0-a9/results/, for each run of the ../scripts/results a new file is created. You can copy the results back to your PC and run various kinds of summary postprocessing scripts from lmbench, e.g.

```
../scripts/getsummary ../results/arm-linux/* 
```

## Wishlist

A list of benchmark results would be useful:

*   Comparing performance of different FFT implementations on Beagleboard-XM: [`pmeerw.dyndns.org/blog/programming/arm_fft.html`](http://pmeerw.dyndns.org/blog/programming/arm_fft.html)

[Category](http://eLinux.org/Special:Categories "Special:Categories"):

*   [Development Tools](http://eLinux.org/Category:Development_Tools "Category:Development Tools")

# Source Management Tools

> From: [eLinux.org](http://eLinux.org/Source_Management_Tools "http://eLinux.org/Source_Management_Tools")

# Source Management Tools

Here are some different source management tools commonly used with Linux:

## Contents

*   1 Overview
*   2 Patch Management Tools
*   3 Version Control Systems
*   4 Identity Verification, text validation

## Overview

*   David Wheeler has an excellent breakdown of various SCM tools at: [`www.dwheeler.com/essays/scm.html`](http://www.dwheeler.com/essays/scm.html)
*   IBM has an good overview of available tools at: [`www-128.ibm.com/developerworks/linux/library/l-vercon/`](http://www-128.ibm.com/developerworks/linux/library/l-vercon/)
*   There is a comparison of several different tools at: [`better-scm.berlios.de/comparison/comparison.html`](http://better-scm.berlios.de/comparison/comparison.html)

## Patch Management Tools

*   diff - to create patches
    *   use 'man diff' on your local system for information
*   [patch](http://wikipedia.org/wiki/Patch_%28Unix%29) - to apply patches
    *   use 'man patch' on your local system for information
*   [Quilt](http://eLinux.org/Quilt "Quilt") is good for managing a group of patches relative to a single source base.
*   diffstat reads a patch file (or standard input) and displays a histogram of the insertions, deletions, and modifications per-file. It is useful for reviewing large, complex patch files. It reads from one or more input files or from standard input. If an input filename ends with .bz2, .Z or .gz, diffstat will read the uncompressed data via a pipe from the corresponding program.
    *   diffstat is included in most Linux distributions
    *   [diffstat home page](http://invisible-island.net/diffstat/diffstat.html)
    *   [diffstat man page](http://www.die.net/doc/linux/man/man1/diffstat.1.html)
*   [Tim's patch management tools](http://eLinux.org/Tim%27s_patch_management_tools "Tim's patch management tools")
    *   diffinfo and friends - a more verbose diffstat, with splitting, joining and comparing of patches
*   See also [Diff And Patch Tricks](http://eLinux.org/Diff_And_Patch_Tricks "Diff And Patch Tricks")
*   [Splash](http://eLinux.org/Splash "Splash") - Sony tool for managing patches (kind of like quilt or stacked git)

## Version Control Systems

*   [Git](http://eLinux.org/Git "Git")
*   [Subversion](http://eLinux.org/Subversion "Subversion")
*   [BitKeeper](http://eLinux.org/BitKeeper "BitKeeper")

## Identity Verification, text validation

*   [PgpKey](http://eLinux.org/PgpKey "PgpKey")

[Category](http://eLinux.org/Special:Categories "Special:Categories"):

*   [Development Tools](http://eLinux.org/Category:Development_Tools "Category:Development Tools")

# Test Systems

> From: [eLinux.org](http://eLinux.org/Test_Systems "http://eLinux.org/Test_Systems")

# Test Systems

Here is a quick list of different test systems (and test projects) for Linux:

## Contents

*   1 Test Projects
    *   1.1 Test Automation Tools
    *   1.2 Papers on testing
    *   1.3 Test Suites
    *   1.4 Static Analysis
*   2 Automated kernel compilation results
    *   2.1 L4X
    *   2.2 Kautobuild
    *   2.3 ABAT
*   3 Bug tracking system
    *   3.1 Components
    *   3.2 Usage
    *   3.3 External links

## Test Projects

Clearly, a number of open source projects in this realm exist. This page intends to collect descriptions and links to these projects

### Test Automation Tools

*   [Autotest](http://test.kernel.org/autotest)
*   [LAVA](https://launchpad.net/lava) - Linaro's test framework
*   [Opentest](http://arago-project.org/wiki/index.php/Opentest)
*   [Red Hat Test Project](https://testing.108.redhat.com/)
*   [Ktest](http://eLinux.org/Ktest "Ktest") Automate kernel testing
*   [Jenkins-based Test Automation](http://eLinux.org/index.php?title=Jenkins-based_Test_Automation&action=edit&redlink=1 "Jenkins-based Test Automation (page does not exist)")
    *   the official test framework for the LTSI project

### Papers on testing

*   Paper on finding bugs in Unix programs using random input: [`ftp.cs.wisc.edu/paradyn/technical_papers/fuzz.pdf`](http://ftp.cs.wisc.edu/paradyn/technical_papers/fuzz.pdf)

### Test Suites

*   [Linux Test Project](http://ltp.sourceforge.net/)
*   [LTP-DDT](http://processors.wiki.ti.com/index.php/LTP-DDT)

### Static Analysis

*   [Sparse](http://tree.celinuxforum.org/CelfPubWiki/Sparse)
*   [Smatch](http://smatch.sourceforge.net/)
*   [clang](http://clang.llvm.org/StaticAnalysis.html)

## Automated kernel compilation results

Here are some locations where automated tests of kernel compilation can be viewed:

### L4X

*   [`l4x.org/k/`](http://l4x.org/k/) - Jan Dittmer's page showing the build status and kernel size of the defconfigs of many architectures. Running since 2004 or 2005

### Kautobuild

Kautobuild is Simtec's automated system to build and store results for ARM and MIPS platforms, for every kernel version. It uses defconfigs for multiple boards, and reports compile errors/warnings, module size, kernel size etc.

*   Kautobuild for ARM - [`armlinux.simtec.co.uk/kautobuild/`](http://armlinux.simtec.co.uk/kautobuild/)
*   mail archive version of results is at: [`lists.simtec.co.uk/pipermail/kautobuild/`](http://lists.simtec.co.uk/pipermail/kautobuild/)
*   Article about the Simtel site is at: [`www.linuxdevices.com/news/NS4646877354.html`](http://www.linuxdevices.com/news/NS4646877354.html) (2006)

### ABAT

ABAT - [`sourceforge.net/projects/abat/`](https://sourceforge.net/projects/abat/)

```
- does a download/build/boot regression test for several mainline kernel trees, as soon as new versions are available
- test results matrix is available here:
   - http://ftp.kernel.org/pub/linux/kernel/people/mbligh/abat/regression_matrix.html 
```

## Bug tracking system

A **bug tracking system** is a software application that is designed to help quality assurance and computer programmers keep track of reported software bugs in their work.

Many bug-tracking systems, such as those used by most open source software projects, allow users to enter bug reports directly. Other systems are used only internally in a company or organization doing software development. Typically bug tracking systems are integrated with other software project management applications.

Having a bug tracking system is extremely valuable in software development, and they are used extensively by companies developing software products.

### Components

A major component of a bug tracking system is a database that records facts about known bugs. Facts may include the time a bug was reported, its severity, the erroneous program behavior, and details on how to reproduce the bug; as well as the identity of the person who reported it and any programmers who may be working on fixing it.

Typical bug tracking systems support the concept of the life cycle for a bug which is tracked through status assigned to the bug. A bug tracking system should allow administrators to configure permissions based on status, move the bug to another status, or delete the bug. The system should also allow administrators to configure the bug statuses and to what status a bug in a particular status can be moved to.

### Usage

In a corporate environment, a bug-tracking system may be used to generate reports on the productivity of programmers at fixing bugs. However, this may sometimes yield inaccurate results because different bugs may have different levels of severity and complexity. The severity of a bug may not be directly related to the complexity of fixing the bug. There may be different opinions among the managers and architects.

A *local bug tracker (LBT)* is usually a computer program used by a team of application support professionals to keep track of issues communicated to software developers. Using an LBT allows support professionals to track bugs in their "own language" and not the "language of the developers." In addition, LBT use allows a team of support professionals to track specific information about users who have called to complain that may not always be needed in the actual development queue (thus, there are two tracking systems when an LBT is in place.)

### External links

*   [Trac](http://trac.edgewall.org/) - Web-based software project management and bug/issue tracking system.
*   [Bugzilla](http://www.bugzilla.org/) - Bug tracking used by the Mozilla projects. Inherently web-based, written in Perl , and uses MySQL as its database back-end. Open-Source.
*   [Bugreport is a place to publish the security advisories](http://www.bugreport.ir/?)
*   [How to Report Bugs Effectively](http://www.chiark.greenend.org.uk/~sgtatham/bugs.html)

# Test Tools

> From: [eLinux.org](http://eLinux.org/Test_Tools "http://eLinux.org/Test_Tools")

# Test Tools

## Test Tools

Tools to aid with Testing

### SPI

*   [spidev_test.c](http://www.kernel.org/doc/Documentation/spi/spidev_test.c)
*   [SpiSlaveZero](http://eLinux.org/SpiSlaveZero "SpiSlaveZero") SPI Slave Zero universal SPI peripheral

### USB

*   [Gadget Zero](http://www.linux-usb.org/usbtest/)

# Scripting

> From: [eLinux.org](http://eLinux.org/Scripting "http://eLinux.org/Scripting")

# Scripting

Scripting is powerful technology especially valuable in embbedded Linux. It is used for building complex projects, building root file systems and distributions, system management, tests automation.

Most commons shells are [bash](http://www.gnu.org/software/bash/) on PC and busybox's [ash](http://en.wikipedia.org/wiki/Almquist_shell) on embedded Linux.

## Contents

*   1 Shell scripting
    *   1.1 Shell scripting libraries
        *   1.1.1 Specialized frameworks and libraries
        *   1.1.2 Samples from books
        *   1.1.3 Historical
        *   1.1.4 References

## Shell scripting

*   [Bash Reference Manual](http://www.gnu.org/software/bash/manual/bash.html)
*   [Bash Guide for Beginners](http://www.makelinux.net/books/Bash-Beginners-Guide/)
*   [Advanced Bash-Scripting Guide](http://www.makelinux.net/books/abs-guide/)
*   [Command-Line Tools Summary](http://www.makelinux.net/books/GNU-Linux-Tools-Summary/GNU/Linux)
*   [`wiki.bash-hackers.org/`](http://wiki.bash-hackers.org/)
*   [`bash.cyberciti.biz/`](http://bash.cyberciti.biz/)
*   [Top 10 Best Cheat Sheets and Tutorials for Linux / UNIX Commands](http://www.cyberciti.biz/tips/linux-unix-commands-cheat-sheets.html)
*   [`wiki.bash-hackers.org/`](http://wiki.bash-hackers.org/)
*   [`wiki.archlinux.org/index.php/bash`](https://wiki.archlinux.org/index.php/bash)
*   [`www.techbar.me/linux-shell-tips/`](http://www.techbar.me/linux-shell-tips/)

### Shell scripting libraries

*   [shtool](http://www.gnu.org/software/shtool/) The GNU Portable Shell Tool
    *   [man shtool](http://www.makelinux.net/man/1/S/shtool)
    *   Portable wrappers to standard operations
    *   3000 SLOC, 19 functions, bloated
*   [Wicked Cool Shell Scripts, 2004, samples](http://intuitive.com/wicked/wicked-cool-shell-script-library.shtml)
    *   Indeed cool shell scripts, worth to read
    *   Most in interesting functions: 015-newrm.sh, 016-unrm.sh, 021-findman.sh, 029-loancalc.sh, 037-zcat.sh, 038-bestcompress.sh, 040-diskhogs.sh, 084-webaccess.sh, 100-hangman.sh
    *   4000 SLOC, 100 files-functions
    *   Easy to use
*   [mbfl - Marco's Bash Functions Library](http://marcomaggi.github.io/docs/mbfl.html)
    *   5000 SLOC, 500 functions, bloated
    *   The philosophy of MBFL is to do the work as much as possible without external commands.
    *   Complicated to use
*   [@ aka monkey-tail](https://github.com/lmartinking/monkey-tail)
    *   300 SLOC, 20 functions, simple wrapper functions
    *   Easy to use
*   [lib.sh](https://github.com/makelinux/lib)
    *   Single script, 300 SLOC, 40 functions and aliases
    *   Easy to use
    *   Most useful functions: make-debug, trap_err, readline-bindings, duplicates, fs_usage, system_status_short, git_fixup, tcpdump-text, git_ign_add, for_each, mem_avail_kb

#### Specialized frameworks and libraries

*   [Bashinator](http://www.bashinator.org/)
    *   Logging framework
    *   700 SLOC, 18 functions
    *   Complicated to use
*   [libbash - tool for managing bash scripts](http://sourceforge.net/projects/libbash/)
    *   loads and unloads functions from scripts with commands source and unset
*   [bsfl - Bash Shell Function Library](http://code.google.com/p/bsfl/)
    *   600 SLOC, 50 functions, logging functions, trivial wrappers
    *   Easy to use
*   [shesfw - Shell Script Framework tool](http://code.google.com/p/shesfw/)
    *   200 SLOC, 20 functions
    *   unified interface to kdialog, Xdialog, zenity
*   [shUnit2 - xUnit based unit testing for Unix shell scripts](http://code.google.com/p/shunit2/)
*   [log4sh - logging framework](https://sites.google.com/a/forestent.com/projects/log4sh)
    *   logging framework for shell scripts that works similar to [`logging.apache.org/`](http://logging.apache.org/)
*   [bashworks](https://github.com/jpic/bashworks) - something messy
*   [rerun - a modular shell automation framework to organize your keeper scripts](https://github.com/rerun/rerun/blob/master/README.md)
    *   700 SLOC, 30 functions

#### Samples from books

*   [Learning the bash shell, 2005, samples](http://examples.oreilly.com/9781565923478/), 62 files
*   [Bash Cookbook, 2007](http://examples.oreilly.com/9780596526788/), 99 files
*   [Classic Shell Scripting, 2005](http://examples.oreilly.com/9780596005955/). 82 files

#### Historical

*   [UNIX Power Tools, 1997, samples](http://examples.oreilly.com/9780596003302/)
*   [Portable Shell Programming, 1995, samples](http://www.cs.uleth.ca/~holzmann/C/shells/shell_book_blinn/)
    *   1000 SLOC, 33 files-functions
    *   Regular operations implelented in shell. Usedfull on out of memory (OOM) when there is no memory to run external programms.
    *   easy to use

#### References

*   [List of Bash shell-scripting libraries](http://dberkholz.com/2011/04/07/bash-shell-scripting-libraries/)

See also

*   [Android Scripting](http://eLinux.org/Android_Scripting "Android Scripting")

# 开发者资源

Linux 内核相关的各类资源，包括线上文档、书籍、影音等等。

# Linux Kernel Resources

> From: [eLinux.org](http://eLinux.org/Linux_Kernel_Resources "http://eLinux.org/Linux_Kernel_Resources")

# Linux Kernel Resources

This page has references to various kernel resources (web sites and mailing lists) for developers. Most of this information was gathered over a year ago, and may not be accurate.

/\ *Note: You should always look at the kernel MAINTAINERS file for up-to-date information*

## Contents

*   1 Vanilla Linux kernel
    *   1.1 Mailing List (lkml)
    *   1.2 LKML summaries
    *   1.3 Repository access
    *   1.4 News
    *   1.5 Changelog
*   2 Architecture Sites
    *   2.1 MIPS
    *   2.2 ARM
    *   2.3 PowerPC
    *   2.4 SuperH (SH)
*   3 Documentation
    *   3.1 Online
    *   3.2 Books
*   4 Cross-reference / code online

## Vanilla Linux kernel

*   [www.kernel.org](http://www.kernel.org/)
*   [Linux Kernel Source Tarballs](http://kernel.org/pub/linux/kernel/)
*   [Linus' Git Repository](http://git.kernel.org/?p=linux/kernel/git/torvalds/linux.git;a=summary)

### Mailing List (lkml)

*   [The Big List of Linux Kernel mailing lists, and where to find their archives](http://vger.kernel.org/vger-lists.html)
    *   [LKML](http://vger.kernel.org/vger-lists.html#linux-kernel) - The Linux Kernel Mailing List (where the big boys hang out)
    *   [linux-embedded](http://vger.kernel.org/vger-lists.html#linux-embedded)
        *   Embedded Linux Kernel List
*   [How to subscribe to these lists](http://vger.kernel.org/majordomo-info.html)

### LKML summaries

*   [LWN Kernel page](http://lwn.net/Kernel/) - Linux Weekly News kernel coverage

### Repository access

*   [Kernel Git repositories](http://git.kernel.org)
*   [Vanilla Linux Git Tree](http://git.kernel.org/?p=linux/kernel/git/torvalds/linux.git;a=summary)
    *   This is "upstream". Get your code into here, please.
    *   But [this one](http://padator.org/linux/full-history-linux.git.tar) has all the going back to 0.0.1, and updates itself from Linus's tree when you do a "git pull". (This is really cool. You want this.)

### News

*   [Linux Weekly News, Kernel page](http://lwn.net/Kernel/)

### Changelog

*   Comprehensible changelog of the linux kernel

    *   [`wiki.kernelnewbies.org/LinuxChanges`](http://wiki.kernelnewbies.org/LinuxChanges)
*   LWN atricles for spcific releases

    *   2.6.3 [`lwn.net/Articles/71669/`](http://lwn.net/Articles/71669/)
    *   2.6.10 [`lwn.net/Articles/117187/`](http://lwn.net/Articles/117187/)
    *   2.6.12 [`lwn.net/Articles/140165/`](http://lwn.net/Articles/140165/)
    *   2.6.15 [`lwn.net/Articles/166130/`](http://lwn.net/Articles/166130/)
*   LWN aricles on 2.6 API changes

    *   2.6 API changes [`lwn.net/Articles/2.6-kernel-api/`](http://lwn.net/Articles/2.6-kernel-api/)
    *   2.6.12 API changes [`lwn.net/Articles/140164/`](http://lwn.net/Articles/140164/)

## Architecture Sites

### MIPS

*   web site = [`www.linux-mips.org/wiki/Main_Page`](http://www.linux-mips.org/wiki/Main_Page)
*   mailing list = [`www.linux-mips.org/wiki/Net_Resources#Mailing_lists`](http://www.linux-mips.org/wiki/Net_Resources#Mailing_lists)
*   Maintainer = Ralph Baechle

*   there's an alternate site on [Source Forge](http://eLinux.org/Source_Forge "Source Forge")

*   the site is: [`sourceforge.net/projects/linux-mips`](http://sourceforge.net/projects/linux-mips)
*   Note that this is used for experimental stuff that hasn't been merged into the official mips tree by Ralph Baechle

### ARM

*   web site = [`www.arm.linux.org.uk/`](http://www.arm.linux.org.uk/)
*   cvs access = [`cvs.arm.linux.org.uk/`](http://cvs.arm.linux.org.uk/)
*   mailing list = [`www.arm.linux.org.uk/armlinux/mailinglists.php`](http://www.arm.linux.org.uk/armlinux/mailinglists.php)
*   wiki = [`www.linux-arm.org/`](http://www.linux-arm.org/)
*   Maintainer = Russell King

### [PowerPC](http://eLinux.org/PowerPC "PowerPC")

*   web site = [`penguinppc.org/`](http://penguinppc.org/)
*   mailing lists = [`penguinppc.org/about/community.php#lists`](http://penguinppc.org/about/community.php#lists)
*   Git repository = kernel.org:/pub/scm/linux/kernel/git/paulus/powerpc.git
*   Maintainer = Paul Mackerras
*   Power Macintosh Maintainer = Benjamin Herrenschmidt

*   cross-compiler mini-howto: [`penguinppc.org/embedded/cross-compiling/`](http://penguinppc.org/embedded/cross-compiling/)

See the following for information on different linuxppc source trees available: [`www.penguinppc.org/dev/kernel.shtml`](http://www.penguinppc.org/dev/kernel.shtml)

### SuperH (SH)

*   [www.linux-sh.org](http://www.linux-sh.org/)
*   [oss.renesas.com/](http://oss.renesas.com/)
*   Git repository = kernel.org:/pub/scm/linux/kernel/git/lethal/sh-2.6.git
*   mailing list address = linux-sh@vger.kernel.org
*   mailing list page = [`vger.kernel.org/vger-lists.html#linux-sh`](http://vger.kernel.org/vger-lists.html#linux-sh)
*   mailing list archives = [`news.gmane.org/gmane.linux.ports.sh.devel`](http://news.gmane.org/gmane.linux.ports.sh.devel)
*   wiki = [`linux-sh.org/shwiki/FrontPage`](http://linux-sh.org/shwiki/FrontPage)
*   Maintainer = Paul Mundt

## Documentation

### Online

*   Rusty Russell's [Unreliable Guide to Locking](http://kernelbook.sourceforge.net/kernel-locking.html)
*   Embedded Linux kernel and driver development - [Free Tutorials at Free Electrons](http://free-electrons.com/training/drivers)
*   Linux USB drivers - [USB Driver Tutorial at Free Electrons](http://free-electrons.com/articles/linux-usb)

### Books

*   *Linux Kernel Developmen*t by Robert Love
    *   Good introduction to Linux kernel development
*   *Linux Device Drivers* by Jonathan Corbet, Alessandro Rubini, and Greg Kroah-Hartman
    *   Great book for getting started with Linux device drivers
    *   Free online pdf edition: [`lwn.net/images/pdf/LDD3/`](http://lwn.net/images/pdf/LDD3/)
    *   online html [`www.makelinux.net/ldd3/`](http://www.makelinux.net/ldd3/)
*   *Essential Linux Device Drivers* by Sreekrishnan Venkateswaran
    *   Introduction to driver development for major subsystems
*   *Professional Linux Kernel Architecture* by Wolfgang Mauerer
    *   Introduction to the architecture, concepts and algorithms of the Linux kernel
*   *Understanding the Linux Kernel* by Daniel Bovet and Marco Cesati
    *   Guided tour of the code that forms the core of all Linux operating systems
*   *Linux Kernel in a Nutshell* by Greg Kroah-Hartman
    *   Overview of kernel configuration and building
    *   Free online edition: [`www.kroah.com/lkn/`](http://www.kroah.com/lkn/)

## Cross-reference / code online

*   [`www.makelinux.net/kernel_map`](http://www.makelinux.net/kernel_map)
*   [`lxr.free-electrons.com/`](http://lxr.free-electrons.com/)
*   [`sosdg.org/~coywolf/lxr/source/`](http://sosdg.org/~coywolf/lxr/source/)
*   [`lxr.linux.no/source/`](http://lxr.linux.no/source/)
*   [`lxr.devzen.net/source/`](http://lxr.devzen.net/source/)
*   [Find a kernel function line](http://eLinux.org/Find_a_kernel_function_line "Find a kernel function line")

[Category](http://eLinux.org/Special:Categories "Special:Categories"):

*   [Development Tools](http://eLinux.org/Category:Development_Tools "Category:Development Tools")

# Kernel Subsystems

> From: [eLinux.org](http://eLinux.org/Toolbox "http://eLinux.org/Toolbox")

# Kernel Subsystems

*   [The TTY Demystified](http://www.linusakesson.net/programming/tty/index.php) - excellent explanation of kernel tty system
*   [Device Tree](http://elinux.org/Device_Tree) - a structure used to describe system hardware at startup - can be passed or modified by firmware, or built into kernel

# Online Documentation

> From: [eLinux.org](http://eLinux.org/Toolbox "http://eLinux.org/Toolbox")

# Online Documentation

*   [Papers from the Ottawa Linux Symposium](http://kernel.org/doc/ols/)
*   [Free Software tools for embedded systems](http://free-electrons.com/training/devtools)
*   [Real time in embedded Linux systems](http://free-electrons.com/articles/realtime/)
*   [Embedded Linux optimizations](http://free-electrons.com/articles/optimizations)
*   [Audio in embedded Linux systems](http://free-electrons.com/training/audio)
*   [Multimedia in embedded Linux systems](http://free-electrons.com/training/multimedia)
*   [Embedded Linux From Scratch... in 40 minutes!](http://free-electrons.com/articles/elfs/)
*   [Linux technology reference](http://www.makelinux.net/reference)

# Books

> From: [eLinux.org](http://eLinux.org/Category:Books "http://eLinux.org/Category:Books")

# Category:Books

There are many books on Embedded Linux. This page lists several that received their own page on this wiki.

*   [Embedded Linux System Design and Development](http://eLinux.org/Embedded_Linux_System_Design_and_Development "Embedded Linux System Design and Development")

    *   by P. Raghavan, Amol Lad, and Sriram Neelakandan (Hardcover - Dec 21, 2005)
    *   This one looks quite good.
*   [Embedded Linux Primer: A Practical Real-World Approach](http://eLinux.org/Embedded_linux_primer "Embedded linux primer") - by Christopher Hallinan

    *   So does this one.
*   [Building Embedded Linux Systems](http://eLinux.org/Building_Embedded_Linux_Systems "Building Embedded Linux Systems")

    *   by Karim Yaghmour
    *   Be sure to get the second edition (from 2008). The first edition (from 2003) is outdated.
*   [Advanced Programming in the UNIX Environment, Second Edition](http://www.amazon.com/Programming-Environment-Addison-Wesley-Professional-Computing/dp/0321525949/ref=sr_1_1?ie=UTF8&s=books&qid=1259788186&sr=8-1) by the late W. Richard Stevens and Stephen A. Rago

    *   Not embedded specific, but THE reference for Linux/Unix programming
*   [Linux System Programming](http://www.amazon.com/Linux-System-Programming-Robert-Love/dp/0596009585/ref=sr_1_1?ie=UTF8&s=books&qid=1259788281&sr=1-1)

    *   by Robert Love
    *   Good introduction to Linux system programming
*   [PCI Express System Architecture](http://www.amazon.com/gp/product/0321156307/ref=oh_details_o00_s00_i00?ie=UTF8&psc=1)

    *   Mindshare Inc. (Author), Ravi Budruk (Author), Don Anderson (Author), Tom Shanley (Author)
*   [Linux Debugging and Performance Tuning](http://eLinux.org/Linux_Debugging_and_Performance_Tuning "Linux Debugging and Performance Tuning")

    *   by Steve Best
*   [OMAP and DaVinci Software for Dummies](http://eLinux.org/OMAP_and_DaVinci_Software_for_Dummies "OMAP and DaVinci Software for Dummies")
    *   by Steve Blonstein, Alan Campbell, Texas Instruments
*   [Essential Linux Device Drivers](http://eLinux.org/Essential_Linux_Device_Drivers "Essential Linux Device Drivers")
*   [Linux Device Drivers](http://eLinux.org/Linux_Device_Drivers "Linux Device Drivers")
*   [The_Linux_Programming_Interface_-_by_Michael_Kerrisk](http://eLinux.org/The_Linux_Programming_Interface_-_by_Michael_Kerrisk "The Linux Programming Interface - by Michael Kerrisk")
    *   The Linux Programming Interface by Michael Kerrisk

## Pages in category "Books"

The following 11 pages are in this category, out of 11 total.

|  
### B

*   Building Embedded Linux Systems

### E

*   Embedded Linux Primer
*   Embedded Linux System Design and Development
*   Essential Linux Device Drivers

 |  
### F

*   Flameman/books

### L

*   Linux Debugging and Performance Tuning
*   Linux Device Drivers
*   Linux Kernel Development - by Robert Love

 |  
### O

*   OMAP and DaVinci Software for Dummies

### T

*   The Art of Debugging with GDB, DDD, and Eclipse
*   The Linux Programming Interface - by Michael Kerrisk

 |

[Category](http://eLinux.org/Special:Categories "Special:Categories"):

*   [Categories](http://eLinux.org/Category:Categories "Category:Categories")

# Reference Material

> From: [eLinux.org](http://eLinux.org/Reference_Material "http://eLinux.org/Reference_Material")

# Reference Material

*   ARM Processor Reference Manuals - Registration required, but it's free.
    *   go to [`infocenter.arm.com/`](http://infocenter.arm.com/) => ARM architecture => Reference Manuals => ... => registration link (only name, e-mail address and company name are strictly required).
*   The [UHAPI](http://eLinux.org/UHAPI "UHAPI") Forum standardizes hardware-independent application programming interfaces (APIs) for analog and digital televisions, set top boxes, DVD players and recorders, personal video recorders (PVRs), home servers and other consumer audio/video (A/V) devices.
*   Hello_World_in_C
*   [How traceroute works!](http://eLinux.org/Traceroute_-_Tracing_Route "Traceroute - Tracing Route")
*   [How DNS works!](http://eLinux.org/DNS_-_Domain_Name_Server "DNS - Domain Name Server")
*   [Order_One_Study](http://eLinux.org/Order_One_Study "Order One Study")
*   [RTWG-discussion-points](http://eLinux.org/RTWG-discussion-points "RTWG-discussion-points")
*   [Subset_Libc_Specification](http://eLinux.org/Subset_Libc_Specification "Subset Libc Specification")
*   [Extern_Vs_Static_Inline](http://eLinux.org/Extern_Vs_Static_Inline "Extern Vs Static Inline")
*   [Slab_allocator](http://eLinux.org/Slab_allocator "Slab allocator")
*   Size_Tunables

# Podcasts

> From: [eLinux.org](http://eLinux.org/Podcasts "http://eLinux.org/Podcasts")

# Podcasts

*   [[1]](http://tllts.org) - The (Original) Linux Link Tech Show, weekly Linux podcast with archive going back to 2003.
*   [[2]](http://www.timesys.com/resources/podcast) - Timesys LinuxLink Radio. (Despite the name, it's has nothing to do with the older Linux Link podcast, and it's not on the radio. No longer updates on a regular schedule, but the archives are available.)

# Beginning Programming

> From: [eLinux.org](http://eLinux.org/Beginning_Programming "http://eLinux.org/Beginning_Programming")

# Beginning Programming

This page is intended to help beginners get started with learning programming. Eventually, I'd like to provide a whole series of steps, exercises and tutorials about programming, to help anyone who would like to get involved with software development or game development.

## Contents

*   1 Programming toolkits
*   2 Online resources
*   3 Programming checklist
    *   3.1 The very basics
*   4 Languages
    *   4.1 Web Programming
        *   4.1.1 HTML
    *   4.2 Scratch
    *   4.3 Javascript
        *   4.3.1 Javascript resources
    *   4.4 Python (with PyGame)
    *   4.5 C

## Programming toolkits

*   scratch - This is a beginning programming toolkit produced by MIT. This has numerous examples and tutorials, and is highly recommended as an excellent starting place for beginning programmers.

    *   Free download available from: [`scratch.mit.edu/`](http://scratch.mit.edu/)
    *   [`www.youtube.com/watch?v=pkhjX792yVI`](http://www.youtube.com/watch?v=pkhjX792yVI)
        *   a good starter tutorial on scratch
    *   [Scratch Getting Started Guide](http://info.scratch.mit.edu/sites/infoscratch.media.mit.edu/files/file/ScratchGettingStartedv14.pdf)
    *   SNAP is scratch implemented in javascript - [`snap.berkeley.edu/`](http://snap.berkeley.edu/)
*   Kahn Academy - Excellent online introductory course for computer science and programming, using interactive javascript

    *   [Computer Science tutorials and videos](https://www.khanacademy.org/cs) - Start by clicking on the Introductory Video.
    *   Note that the bottom area of the screen is a tabbed dialog with different areas:

"Questions Tips & Feedback Spin-Offs Documentation". "Spin-Offs" has sample programs related to the tutorial, video or program you're looking at, and "Documentation" has description of different functions or statements you can use in your programs.

## Online resources

*   [`www.codeacademy.com/`](http://www.codeacademy.com/) - this site has good starting tutorials for javascript, html, php, python and ruby.

*   [`www.w3schools.com/`](http://www.w3schools.com/)

## Programming checklist

Follow these steps to learn how to program:

### The very basics

*   learn what a program statement is (and what a "computer language" is)
*   learn what a variable is
*   learn what a conditional (if statement) is
*   learn what a loop is
*   learn what input is
    *   different kinds of input (key press, mouse move, mouse button)
*   learn what output is
    *   different kinds of output (text, image, sound)

Simple program 1: Write a program that counts how many times someone presses the space bar.

Simple program 2: Write a program that counts how many times someone presses each arrow key.

program 3: Write a program that starts counting when someone presses the space bar, and stops counting when they hit it a second time (like a stop-watch).

Program 4: Write a program that moves a ball to the right on the screen, when the right arrow is pressed.

Program 5: Write a program that starts a ball moving when key is pressed. The ball should keep moving until it hits a wall.

Note: For any of these programs, you can start with the program you used previously, and expand or modify it to do the next task. You should save it under a new name.

Program 6: Write a program that starts a ball moving in a different direction, depending on which arrow key (up, down, left, right) is pressed.

Program 7: Write a program that starts with a moving ball. When the ball hits a wall, it moves in the opposite direction.

Program 8: Combine programs 6 and 7\. Write a program that starts a ball in a direction based on the key pressed. The ball should keep moving after the key is released. It should bounce off the wall and go the other direction when it hits a wall.

## Languages

Programming can be done in many different computer languages.

### Web Programming

Programming can be done in a web browser. This is a computer program, like Internet Explorer, Firefox or Chrome, that reads web pages from the Internet and shows them on your screen. Embedded devices, like phones, tablets and TVs also have web browsers.

In order to do web programming, you first need to learn how the browser presents information from a web page on your computer screen. It does this by processing the words on the web page (called "parsing" the page), and then drawing things like text, lines and images on your computer screen (usually, inside the browser window).

#### HTML

The words on a web page are part of a language called HTML (HyperText Markup Language). You can learn about this language here: [`www.w3schools.com/html/default.asp`](http://www.w3schools.com/html/default.asp)

Basically, the words and symbols are put into a file (or returned from a program running on the web server), and these words tell the web browser what to draw on the screen. The process of drawing things on the screen is called "rendering" the page.

### Scratch

Here is a tutorial for a bouncing ball demo in scratch: [`scratch-time.blogspot.com/2008/12/how-to-make-bouncing-ball.html`](http://scratch-time.blogspot.com/2008/12/how-to-make-bouncing-ball.html)

### Javascript

Here is a tutorial for a bouncing ball demo in Javascript: [`sixrevisions.com/html/bouncing-a-ball-around-with-html5-and-javascript/`](http://sixrevisions.com/html/bouncing-a-ball-around-with-html5-and-javascript/)

Before you understand this language, and how it works, you first have to understand HTML.

#### Javascript resources

*   [`www.webmonkey.com/2010/02/javascript_tutorial/`](http://www.webmonkey.com/2010/02/javascript_tutorial/)
    *   5-part introductory tutorial
*   [`www.w3schools.com/js/`](http://www.w3schools.com/js/) - nice series of exercises on javascript

### Python (with PyGame)

Here is an introduction to pygame, which has an bouncing ball demo as part of the intro.

[`www.pygame.org/docs/tut/intro/intro.html`](http://www.pygame.org/docs/tut/intro/intro.html)

### C

One of the primitive programming languages it was developed by Dennis Ritchie, it has set the base for the languages that we see now. It is extensively used in system programming (Operating Systems, Device Drivers etc.). C is a powerful language with a primitive syntax, it is a compiled language unlike python. It is the lingua franca for a system developer. The linux kernel is written in 'C'.

Book: The C Programming language by Brian Kernighan and Dennis Ritchie

Online Resources: [`www.learn-c.org/`](http://www.learn-c.org/) (Interactive)

# 要诀和技巧

介绍各类要诀和技巧，内容涵盖芯片鉴别、编码风格、编译和调试技巧，以及 GDB/GCC 的使用等。

# How to Identify IC Markings

> From: [eLinux.org](http://eLinux.org/Chip_Identification "http://eLinux.org/Chip_Identification")

# Chip Identification

Chip Detective: TV show helps with tech issue

One of the common problems while debugging a new board, as well as reverse engineering designs, is the inability to read part numbers on IC packages. During assembly the parts are baked at high temperatures and often washed with chemicals which can cause the package markings to become very difficult to read.

About 8 years ago i was watching one of the generic "Detective" TV shows where they had a hand gun with the serial number ground off. On the TV show, they used a colored die and different camera lighting to bring out the serial number as the imprint what still on the metal of the gun. I thought to myself... Can i use this method to read the markings on IC packages?

I purchased a small usb based microscope and did some testing. Sure enough it worked.

Today one of my most valued tools is the Celestron 44302-A Deluxe Handheld Digital Microscope(about $50USD). It works with most linux utilities including guvcview which I use to capture images. I've uploaded a picture of an IC taken after the IC had been installed on a pcb. You will notice that the markings are pretty hard to read. Using a Sharpie you can paint the top of the package to bring out the markings. I've tried all the basic colors and found that the yellow is the best. It is dark enough to bring out the markings, but not too opaque to obscure the markings. The second photo is a capture after painting the IC with the yellow Sharpie. You can see a pretty dramatic difference. The third capture is done with the usb microscope at a 45 degree angle with the part, which makes the markings really jump out.

EDIT: this also works on ceramic packages where the markings have intentionally removed. Since most of the markings are done with laser etching, the ceramics usually retain the markings and the yellow ink will get absorbed into the ceramic portions "damaged" by the laser etching.

*   [Celestron Microscope](http://www.amazon.com/Celestron-44302-Handheld-Digital-Microscope/dp/B001UQ6E4E/ref=sr_1_1?ie=UTF8&qid=1302633075&sr=8-1)

![Celestron.jpg](http://eLinux.org/File:Celestron.jpg)

![Chip-id-procedure-1.jpg](http://eLinux.org/File:Chip-id-procedure-1.jpg)

![Chip-id-procedure-2.jpg](http://eLinux.org/File:Chip-id-procedure-2.jpg)

![Chip-id-procedure-3.jpg](http://eLinux.org/File:Chip-id-procedure-3.jpg)

![Chip-id-procedure-pen.jpg](http://eLinux.org/File:Chip-id-procedure-pen.jpg)

# Code Styling Tips

> From: [eLinux.org](http://eLinux.org/Code_Styling_Tips "http://eLinux.org/Code_Styling_Tips")

# Code Styling Tips

Here are some miscellaneous tips for good code styling:

## Proper Linux Kernel Coding Style

See the kernel coding style guide in any kernel source tree at: Documentation/CodingStyle (Online [here](http://git.kernel.org/?p=linux/kernel/git/torvalds/linux-2.6.git;a=blob_plain;f=Documentation/CodingStyle;hb=HEAD))

Greg Kroah-Hartman wrote some additional tips in his article: [Proper Linux Kernel Coding Style](http://www.linuxjournal.com/article/5780)

Michael S. Tsirkin made a [kernel guide to space](http://www.openfabrics.org/~mst/boring.txt) (*a boring list of rules*) which got polished on a worth reading [thread](http://thread.gmane.org/gmane.linux.kernel/317744) in LKML in 2005.

### use of #ifdefs

Rob Landley writes:

Read: [`doc.cat-v.org/henry_spencer/ifdef_considered_harmful.pdf`](http://doc.cat-v.org/henry_spencer/ifdef_considered_harmful.pdf)

Personally, I tend to have symbols #defined to a constant 0 or 1 depending on whether or not a function is enabled, and then just use if(SYMBOL) as a guard and let the compiler's dead code eliminator take it out for me at compile time (because if(0) {blah;} shouldn't put any code in the resulting .o file with any optimizer worth its salt. Borland C for DOS managed simple dead code elimination 20 years ago...)

## See also

[Sparse](http://eLinux.org/Sparse "Sparse")

[Category](http://eLinux.org/Special:Categories "Special:Categories"):

*   [Development Tools](http://eLinux.org/Category:Development_Tools "Category:Development Tools")

# Debugging Tips

> From: [eLinux.org](http://eLinux.org/Debugging_Tips "http://eLinux.org/Debugging_Tips")

# Debugging Tips

*   See the [Debugging_Portal](http://eLinux.org/Debugging_Portal "Debugging Portal") page
*   See the Kernel Debugging Tips page
*   See also [Debugging Makefiles](http://eLinux.org/Debugging_Makefiles "Debugging Makefiles")
*   Debugging by printing
*   Debug user-space initialization:
    *   If you get a panic - "not syncing: Attempted to kill init!" it can be for many different reasons. Try setting CONFIG_DEBUG_USER=y in your .config and pass 'user_debug=255' in the kernel command line. That will give you a more verbose output about why user space programs crash. Thanks to Daniel Mack on the linux-arm-kernel mailing list for this tip.

[Category](http://eLinux.org/Special:Categories "Special:Categories"):

*   [Tips and Tricks](http://eLinux.org/Category:Tips_and_Tricks "Category:Tips and Tricks")

# GDB Tips

> From: [eLinux.org](http://eLinux.org/GDB_Tips "http://eLinux.org/GDB_Tips")

# GDB Tips

## get element size

Sometimes, with complex structures (arrays of structs containing nested structs or arrays), it is hard to determine the actual size of a particular element.

You can use gdb with a program image to get the size of structures, by looking at the offset of an element of the structure relative to an address of zero:

Here are some examples:

```
 ${CROSS_COMPILE}gdb vmlinux
GNU gdb (GDB) 7.2
Copyright (C) 2010 Free Software Foundation, Inc.
License GPLv3+: GNU GPL version 3 or later <http://gnu.org/licenses/gpl.html>
This is free software: you are free to change and redistribute it.
There is NO WARRANTY, to the extent permitted by law.  Type "show copying"
and "show warranty" for details.
This GDB was configured as "--host=i686-pc-linux-gnu --target=arm-sony-linux-gnueabi".
For bug reporting instructions, please see:
<http://www.gnu.org/software/gdb/bugs/>.
(gdb) p &((struct poll_wqueues *)0)->polling_task
$6 = (struct task_struct **) 0xc
(gdb) p/d &((struct poll_wqueues *)0)->error
$4 = 20 
```

the second example could be read as: "print, in decimal, the address (offset) of the element error using address 0 cast as a pointer to struct poll_wqueues"

'pt' is the first element of struct poll_wqueues. Here is an example using array offsets, showing that struct poll_wqueues is 604 bytes long.

```
(gdb) p/d &((struct poll_wqueues *)0)[0]->pt
$2 = 0
(gdb) p/d &((struct poll_wqueues *)0)[1]->pt
$3 = 604 
```

[Category](http://eLinux.org/Special:Categories "Special:Categories"):

*   [Tips and Tricks](http://eLinux.org/Category:Tips_and_Tricks "Category:Tips and Tricks")

# GCC Tips

> From: [eLinux.org](http://eLinux.org/GCC_Tips "http://eLinux.org/GCC_Tips")

# GCC Tips

## Contents

*   1 What's Here, Why You Should Care
*   2 Tips
    *   2.1 View Compilation Plan
    *   2.2 See default include search paths
    *   2.3 Timing sub-commands
    *   2.4 Pre-Process, Retain Comments
    *   2.5 Pre-Process, Retain Defines/Macros
    *   2.6 See what Files the Linker is Using
    *   2.7 Print Pre-defined Macros
    *   2.8 Mixed Assembler and Source Output
    *   2.9 Specify Language
    *   2.10 List Include File Dependencies
    *   2.11 Symbol Trace
    *   2.12 Saving temporary files

## What's Here, Why You Should Care

A collection of tips useful to those doing embedded development. Accumulated over several years of doing project work, helping other engineers, untangling projects for customers and feedback from several CELF presentations related to this topic.

## Tips

### View Compilation Plan

```
gcc -### <the rest of your command line goes here> 
```

The GCC you run is a driver program for a bunch of other programs. With this parameter, gcc will produce (but not actually execute) the commands it would have used to accomplish the task you asked it to do. This way, you can see the gory details of what's going on behind the scenes. What library is being used? What is -mcpu set to? It's all there.

You can pipe this output to a file and execute that to compile a program, making it easy to experiment with tweaks to the linker or assembler.

```
Reading specs from /opt/timesys/toolchains/ppc7xx-linux/lib/gcc/powerpc-linux/3.4.1/specs
Configured with: ../configure --prefix=/opt/timesys/toolchains/ppc7xx-linux --mandir=/opt/timesys/toolchains/ppc7xx-linux/share/man --infodir=/opt/timesys/toolchains/ppc7xx-linux/share/info --enable-shared --enable-threads=posix --disable-checking --with-system-zlib --enable-__cxa_atexit --disable-libunwind-exceptions --enable-languages=c,c++ --with-sysroot=/here/workdir/i386-x-ppc7xx/deleteme --disable-libgcj --build=i686-timesys-linux --host=i686-timesys-linux --target=powerpc-linux --program-prefix=ppc7xx-linux-
Thread model: posix
gcc version 3.4.1 20040714 (TimeSys 3.4.1-7)
 /opt/timesys/toolchains/ppc7xx-linux/libexec/gcc/powerpc-linux/3.4.1/cc1 -quiet -v -D__unix__ -D__gnu_linux__ -D__linux__ -Dunix -D__unix -Dlinux -D__linux -Asystem=linux -Asystem=unix -Asystem=posix -I/opt/timesys/toolchains/ppc7xx-linux/powerpc-linux/include/nptl file.c -quiet -dumpbase file.c -auxbase file -version -o /tmp/ccShiHn4.s
ignoring nonexistent directory "/here/workdir/i386-x-ppc7xx/deleteme/usr/local/include"
ignoring nonexistent directory "/here/workdir/i386-x-ppc7xx/deleteme/usr/include"
#include "..." search starts here:
#include <...> search starts here:
 /opt/timesys/toolchains/ppc7xx-linux/powerpc-linux/include/nptl
 /opt/timesys/toolchains/ppc7xx-linux/lib/gcc/powerpc-linux/3.4.1/include
 /opt/timesys/toolchains/ppc7xx-linux/lib/gcc/powerpc-linux/3.4.1/../../../../powerpc-linux/include
End of search list.
GNU C version 3.4.1 20040714 (TimeSys 3.4.1-7) (powerpc-linux)
 compiled by GNU C version 3.2.2 20030222 (Red Hat Linux 3.2.2-5).
GGC heuristics: --param ggc-min-expand=47 --param ggc-min-heapsize=32138
 /opt/timesys/toolchains/ppc7xx-linux/lib/gcc/powerpc-linux/3.4.1/../../../../powerpc-linux/bin/as -mppc -many -V -Qy -o /tmp/ccWeV3a3.o /tmp/ccShiHn4.s
GNU assembler version 2.15.90.0.3 (powerpc-linux) using BFD version 2.15.90.0.3 20040415
 /opt/timesys/toolchains/ppc7xx-linux/libexec/gcc/powerpc-linux/3.4.1/collect2 --eh-frame-hdr -V -Qy -L/opt/timesys/toolchains/ppc7xx-linux/powerpc-linux/lib/nptl --rpath-link /opt/timesys/toolchains/ppc7xx-linux/powerpc-linux/lib/tls -m elf32ppclinux -dynamic-linker /lib/ld.so.1 -o file /opt/timesys/toolchains/ppc7xx-linux/lib/gcc/powerpc-linux/3.4.1/../../../../powerpc-linux/lib/crt1.o /opt/timesys/toolchains/ppc7xx-linux/lib/gcc/powerpc-linux/3.4.1/../../../../powerpc-linux/lib/crti.o /opt/timesys/toolchains/ppc7xx-linux/lib/gcc/powerpc-linux/3.4.1/crtbegin.o -L/opt/timesys/toolchains/ppc7xx-linux/lib/gcc/powerpc-linux/3.4.1 -L/opt/timesys/toolchains/ppc7xx-linux/lib/gcc/powerpc-linux/3.4.1/../../../../powerpc-linux/lib /tmp/ccWeV3a3.o -lgcc --as-needed -lgcc_s --no-as-needed -lc -lgcc --as-needed -lgcc_s --no-as-needed /opt/timesys/toolchains/ppc7xx-linux/lib/gcc/powerpc-linux/3.4.1/crtsavres.o /opt/timesys/toolchains/ppc7xx-linux/lib/gcc/powerpc-linux/3.4.1/crtend.o /opt/timesys/toolchains/ppc7xx-linux/lib/gcc/powerpc-linux/3.4.1/../../../../powerpc-linux/lib/crtn.o
GNU ld version 2.15.90.0.3 20040415
  Supported emulations:
   elf32ppclinux
   elf32ppc
   elf32ppcsim 
```

Note: on my (Tim Bird's) Ubuntu 12.04 system, using gcc 4.6.3, it shows quite different output. Specifically, it is missing anything about the include directories.

### See default include search paths

You can get a report about the include search paths with the following command:

```
$ gcc -xc -E -v -
Using built-in specs.
COLLECT_GCC=gcc
COLLECT_LTO_WRAPPER=/usr/lib/gcc/x86_64-linux-gnu/4.6/lto-wrapper
Target: x86_64-linux-gnu
Configured with: ../src/configure -v --with-pkgversion='Ubuntu/Linaro 4.6.3-1ubuntu5' --with-bugurl=file:///usr/share/doc/gcc-4.6/README.Bugs --enable-languages=c,c++,fortran,objc,obj-c++ --prefix=/usr --program-suffix=-4.6 --enable-shared --enable-linker-build-id --with-system-zlib --libexecdir=/usr/lib --without-included-gettext --enable-threads=posix --with-gxx-include-dir=/usr/include/c++/4.6 --libdir=/usr/lib --enable-nls --with-sysroot=/ --enable-clocale=gnu --enable-libstdcxx-debug --enable-libstdcxx-time=yes --enable-gnu-unique-object --enable-plugin --enable-objc-gc --disable-werror --with-arch-32=i686 --with-tune=generic --enable-checking=release --build=x86_64-linux-gnu --host=x86_64-linux-gnu --target=x86_64-linux-gnu
Thread model: posix
gcc version 4.6.3 (Ubuntu/Linaro 4.6.3-1ubuntu5)
COLLECT_GCC_OPTIONS='-E' '-v' '-mtune=generic' '-march=x86-64'
 /usr/lib/gcc/x86_64-linux-gnu/4.6/cc1 -E -quiet -v -imultilib . -imultiarch x86_64-linux-gnu - -mtune=generic -march=x86-64 -fstack-protector
ignoring nonexistent directory "/usr/local/include/x86_64-linux-gnu"
ignoring nonexistent directory "/usr/lib/gcc/x86_64-linux-gnu/4.6/../../../../x86_64-linux-gnu/include"
#include "..." search starts here:
#include <...> search starts here:
 /usr/lib/gcc/x86_64-linux-gnu/4.6/include
 /usr/local/include
 /usr/lib/gcc/x86_64-linux-gnu/4.6/include-fixed
 /usr/include/x86_64-linux-gnu
 /usr/include
End of search list. 
```

### Timing sub-commands

You can get a report for the amount of time taken for each sub-command that gcc uses, using the '-time' command.

Example:

```
$ gcc -time -static hello.c -o hello
# cc1 0.01 0.01
# as 0.00 0.00
# collect2 0.03 0.02
$ 
```

This was on a fast machine, with a very small sample program. It shows that the sub-programs 'cc1', 'as', and 'collect2' (the compiler, the assembler, and the linker (collect2 is a front-end for 'ld'), respectively), took less than a few hundredths of a second each to run.

### Pre-Process, Retain Comments

```
gcc -C -E <file-name.c> -o file 
```

Some engineers love to do coding in macros. The rest of us would like to break their fingers. This command will run the file through the pre-processor, expanding all macros, but retaining all comments. Stick a comment like "LOOK HERE" and search for that so you reduce the amount of time you spend looking for the offending code.

### Pre-Process, Retain Defines/Macros

```
gcc -dD -E <file-name.c> -o file 
```

Just like the previous example, but perhaps you're trying to trace the macro define mess.

### See what Files the Linker is Using

```
gcc -Wl,-t <parameters> 
```

Displays what files the linker opens in what order. When looking in archive files, the archive file is displayed in para theses, followed by the file in the archive. Very handy when working through a legacy project that depends on files linking in a certain order that suddenly breaks because of a small (probably viewed as not noteworthy) change in a makefile somewhere.

```
/usr/bin/ld: mode elf_i386
/usr/lib/gcc-lib/i386-redhat-linux/3.3.3/../../../crt1.o
/usr/lib/gcc-lib/i386-redhat-linux/3.3.3/../../../crti.o
/usr/lib/gcc-lib/i386-redhat-linux/3.3.3/crtbegin.o
/tmp/cc37FxnS.o
-lgcc_s (http://eLinux.org/usr/lib/gcc-lib/i386-redhat-linux/3.3.3/libgcc_s.so)
/lib/libc.so.6
(http://eLinux.org/usr/lib/libc_nonshared.a)elf-init.oS
-lgcc_s (http://eLinux.org/usr/lib/gcc-lib/i386-redhat-linux/3.3.3/libgcc_s.so)
/usr/lib/gcc-lib/i386-redhat-linux/3.3.3/crtend.o
/usr/lib/gcc-lib/i386-redhat-linux/3.3.3/../../../crtn.o 
```

### Print Pre-defined Macros

```
gcc -E -dM - < /dev/null | cut -c 9- | sort 
```

Very handy when porting code. Lets you know if your target processor has some missing defines or if something is different (like __INT_MAX__) that can have interesting effects on your project. Diff the output from the old to the new compiler so you can easily see the differences, makes it easy to spot problems before getting started.

Sample output, from a compiler targeting an ARM processor.

```
__APCS_32__ 1
__arm__ 1
__ARM_ARCH_4T__ 1
__ARMEL__ 1
__CHAR_BIT__ 8
__CHAR_UNSIGNED__ 1
__DBL_DENORM_MIN__ 4.9406564584124654e-324
__DBL_DIG__ 15
__DBL_EPSILON__ 2.2204460492503131e-16
__DBL_HAS_DENORM__ 1
__DBL_HAS_INFINITY__ 1
__DBL_HAS_QUIET_NAN__ 1
__DBL_MANT_DIG__ 53
__DBL_MAX_10_EXP__ 308
__DBL_MAX__ 1.7976931348623157e+308
__DBL_MAX_EXP__ 1024 
```

### Mixed Assembler and Source Output

```
gcc -g somefile.c -o somefile
objdump -S somefile 
```

Prints out each line in the program and the corresponding assembly code. Very handy when you're trying to see that the processor is generating the correct code, with the instructions you're expecting. You can also see the effects of optimization, but would recommend doing this for a small amount of code because when the optimization level is high, there's a much lower relationship between line of code and generated assembler.

Here's an example of what objdump produces for a few lines of code:

```
 gpvSharedMemory = shmat(hSharedMemory, NULL, 0);
10000958:       80 7f 00 10     lwz     r3,16(r31)
1000095c:       38 80 00 00     li      r4,0
10000960:       38 a0 00 00     li      r5,0
10000964:       48 01 09 31     bl      10011294 <shmat@plt>
10000968:       7c 60 1b 78     mr      r0,r3
1000096c:       3d 20 10 01     lis     r9,4097
10000970:       90 09 11 d0     stw     r0,4560(r9)
  if (errno != 0) {
10000974:       48 01 08 d1     bl      10011244 <__errno_location@plt>
10000978:       7c 60 1b 78     mr      r0,r3
1000097c:       7c 09 03 78     mr      r9,r0
10000980:       80 09 00 00     lwz     r0,0(r9)
10000984:       2f 80 00 00     cmpwi   cr7,r0,0
10000988:       41 9e 00 50     beq-    cr7,100009d8 <main+0x10c> 
```

### Specify Language

```
gcc -x c a-c-source-file.with-a-non-standard-extension -o test.out 
```

Great for legacy projects where where the file extensions don't match with GCC's expectations, while less of a problem since many projects got their start with GCC, this still is an issue with long-running projects that years back, used some other compiler. This stays in effect for the following file on the command line.

### List Include File Dependencies

There's a whole family of things around -M. These produce a rule that could be used in a make file, with the included files as dependencies.

```
gcc -M <file name> 
```

This shows you all includes, even those on the system path. Useful if you're doing porting work or validating if your compiler is working as expected and getting the files from the right place. You'll see something like this for a basic hello world program

```
hello.o: hello.c /usr/include/stdio.h /usr/include/features.h \
  /usr/include/sys/cdefs.h /usr/include/gnu/stubs.h \
  /usr/lib/gcc-lib/i386-redhat-linux/3.3.3/include/stddef.h \
  /usr/include/bits/types.h /usr/include/bits/wordsize.h \
  /usr/include/bits/typesizes.h /usr/include/libio.h \
  /usr/include/_G_config.h /usr/include/wchar.h /usr/include/bits/wchar.h \
  /usr/include/gconv.h \
  /usr/lib/gcc-lib/i386-redhat-linux/3.3.3/include/stdarg.h \
  /usr/include/bits/stdio_lim.h /usr/include/bits/sys_errlist.h

gcc -MM <file name> 
```

Like -M, but no system files. Great to see if your project is configured and working as expected.

```
gcc -M -MG <file name> 
```

The prior -M and -MM commands will stop if header files can be located. The parameter -MG will just produce the dependency list with the missing file. Engineers that have projects that generate header files as part of the build find -MM very handy.

```
gcc -M -MT '<target>' <file name> 
```

By default, the target will be the \<file nameu0005c="" class="calibre16">.o This command will make the default the value of \<targetu0005c class="calibre16">.</targetu0005c></file>

If the command was

```
gcc -M -MT '$(target)' hello.c 
```

You would see

```
$(target): hello.c 
```

### Symbol Trace

```
gcc -Wl,-y,printf hello.c 
```

This is very handy when you want to understand where the linker is finding a definition of a symbol. Some projects have name collisions or link order dependencies. This lets you see precisely what the linker is doing.

Given a hello world program, you would see output like

```
/tmp/ccwZx5UV.o: reference to printf
/lib/libc.so.6: definition of printf 
```

The reference is in a temporary file created during the compilation process. If you were linking several object files together explicitly, you would see the name of the object file where printf was referenced.

### Saving temporary files

```
gcc -save-temps hello.c 
```

The compilers temporary files are saved. This is often invaluable dealing with complicated makefiles to peek at the preprocessed output without having to figure out how to do a -E option by hand.

[Categories](http://eLinux.org/Special:Categories "Special:Categories"):

*   [Development Tools](http://eLinux.org/Category:Development_Tools "Category:Development Tools")
*   [Tips and Tricks](http://eLinux.org/Category:Tips_and_Tricks "Category:Tips and Tricks")
*   [Compiler](http://eLinux.org/Category:Compiler "Category:Compiler")

# 杂项与收藏

介绍到蓝牙网络、不间断的日志记录系统和系统崩溃诊断等。

# Setting up a Bluetooth Network

> From: [eLinux.org](http://eLinux.org/Bluetooth_Network "http://eLinux.org/Bluetooth_Network")

# Bluetooth Network

This page has information about setting up a Bluetooth Personal Area Network (PAN) with BlueZ. Having a Bluetooth network is helpful for providing network access to low power embedded devices.

## Contents

*   1 Background
*   2 Limitations
*   3 Requirements
*   4 BlueZ Configuration
    *   4.1 Setup /etc/bluetooth/hcid.conf
    *   4.2 Daemon Configuration
    *   4.3 End Result (When host is connected)
*   5 Bridge Configuration
    *   5.1 Setup /etc/network/interfaces
    *   5.2 Setup /etc/bluetooth/pan/dev-{up|down}
    *   5.3 End Result (When host is connected)
*   6 Setup DHCP
    *   6.1 Setup /etc/dhcpd.conf
    *   6.2 Daemon Configuration
*   7 Setup Network
    *   7.1 Shorewall
        *   7.1.1 params
        *   7.1.2 interfaces
        *   7.1.3 zones
        *   7.1.4 policy
        *   7.1.5 masq
        *   7.1.6 rules
    *   7.2 Netfilter
    *   7.3 Network Manager 0.7
*   8 Client Device
    *   8.1 /etc/network/interfaces file
    *   8.2 End result

## Background

Information on piconets can be found on [Wikipedia](http://en.wikipedia.org/wiki/Piconet) Basic information on BlueZ PAN support can be found here: [[1]](http://bluez.sourceforge.net/contrib/HOWTO-PAN)

## Limitations

A PAN network is limited to 7 clients and provides substantially less bandwidth (~700Kbit/s) than other WiFi networks.

## Requirements

To setup a home piconet, you'll need:

*   A Bluetooth device, such as a USB dongle, preferably Class 1 for range purposes.
*   A kernel that supports the Bluez stack including BNEP.
*   [bluez-utils](http://www.bluez.org/) (testing with 3.36).
*   Kernel ethernet bridging support.
*   [bridge-utils](http://www.linuxfoundation.org/en/Net:Bridge) (tested with 1.4).

These instructions are based on a Debian/Sid system, but the setup should be similar for other distributions.

## BlueZ Configuration

### Setup /etc/bluetooth/hcid.conf

[hcid.conf(5)](http://linux.die.net/man/5/hcid.conf) Your piconet server should advertise itself appropriately. Modify the class parameter within the device section so that the host presents itself as a network access point device offering network service:

```
# Local device class
class 0x020300; 
```

Change your piconet server to prefer master role on incoming connections:

```
lm master; 
```

Make your piconet server permanently discoverable:

```
discovto 0; 
```

### Daemon Configuration

[pand(1)](http://linux.die.net/man/1/pand) Setup the command line options for the pand daemon. Within Debian, this is done through the file /etc/default/bluetooth. The command lines for the pand daemon should be:

```
--listen --role NAP -u /etc/bluetooth/pan/dev-up -o /etc/bluetooth/pan/dev-down 
```

### End Result (When host is connected)

ifconfig bnep0

```
bnep0     Link encap:Ethernet  HWaddr 00:11:f6:05:79:95
          inet6 addr: fe80::211:f6ff:fe05:7995/64 Scope:Link
          UP BROADCAST RUNNING MULTICAST  MTU:1500  Metric:1
          RX packets:23661 errors:0 dropped:0 overruns:0 frame:0
          TX packets:29381 errors:0 dropped:0 overruns:0 carrier:0
          collisions:0 txqueuelen:1000
          RX bytes:2976646 (2.8 MiB)  TX bytes:27249215 (25.9 MiB) 
```

hcitool con

```
Connections:
    > ACL 00:1B:DC:0F:A8:AE handle 8 state 1 lm MASTER 
```

## Bridge Configuration

The kernel only provides an Ethernet device when at least one PAN client has connected. This means that there will be no associated device when no devices are connected. This can be very inconvenient when providing services such as DHCP. By utilizing Ethernet Bridging, a permanent pan0 device can be created.

### Setup /etc/network/interfaces

[interfaces(5)](http://web.iesrodeira.com/cgi-bin/man/man2html?5+interfaces)

[bridge-utils-interfaces(5)](http://web.iesrodeira.com/cgi-bin/man/man2html?bridge-utils-interfaces+5) On Debian systems, network interfaces are configured through this file. An example configuration would be:

```
auto pan0
iface pan0 inet static
        address 10.1.0.1
        netmask 255.255.255.0
        broadcast 10.1.0.255
        bridge_ports none
        bridge_fd 0
        bridge_stp off 
```

Alternatively, the pan0 interface can be configured manually:

```
brctl addbr pan0
brctl setfd pan0 0
brctl stp pan0 off
ifconfig pan0 10.1.0.1 netmask 255.255.255.0 
```

### Setup /etc/bluetooth/pan/dev-{up|down}

The dev up/down files add and remove the bnep0 device from the pan0 bridge interface as the first device enters the network, and as the last device leaves the network.

/etc/bluetooth/pan/dev-up

```
#!/bin/sh
ifconfig $1 up
brctl addif pan0 $1 
```

/etc/bluetooth/pan/dev-down

```
#!/bin/sh
brctl delif pan0 $1
ifconfig $1 down 
```

### End Result (When host is connected)

brctl show

```
bridge name bridge id       STP enabled interfaces
pan0        8000.0011f6057995   no      bnep0 
```

ifconfig pan0

```
pan0      Link encap:Ethernet  HWaddr 00:11:f6:05:79:95
          inet addr:10.1.0.1  Bcast:10.1.0.255  Mask:255.255.255.0
          inet6 addr: fe80::200:ff:fe00:0/64 Scope:Link
          UP BROADCAST RUNNING MULTICAST  MTU:1500  Metric:1
          RX packets:30706 errors:0 dropped:0 overruns:0 frame:0
          TX packets:40037 errors:0 dropped:0 overruns:0 carrier:0
          collisions:0 txqueuelen:0
          RX bytes:3681538 (3.5 MiB)  TX bytes:34573855 (32.9 MiB) 
```

## Setup DHCP

Unless avahi zeroconf will be used to assign address, a DHCP server will be required.

### Setup /etc/dhcpd.conf

Basic configuration:

```
option domain-name-servers <dns1>,<dns2>,<dns3>;

default-lease-time 864000;
max-lease-time 864000;

subnet 10.1.0.0 netmask 255.255.255.0 {
  option domain-name "blue";
  range 10.1.0.100 10.1.0.200;
  option routers 10.1.0.1;
} 
```

### Daemon Configuration

Setup the command line options for the dhcpd daemon. Within Debian, this is done through the file /etc/default/dhcp. Tho command lines for the dhcpd daemon should be:

```
pan0 
```

## Setup Network

If your piconet server is not the machine you intend to access your piconet devices from and/or your piconet devices need to access hosts other than your piconet server, routing and/or NAT will need to be configured

### Shorewall

Adding your piconet to an existing [Shorewall](http://www.shorewall.net) configuration is by far the easiest method.

#### params

```
BLUE_IF=pan0 
```

#### interfaces

```
#ZONE   INTERFACE       BROADCAST       OPTIONS
blue    $BLUE_IF    detect      tcpflags,dhcp,detectnets,nosmurfs 
```

#### zones

```
#ZONE   TYPE        OPTIONS     IN          OUT
#                   OPTIONS         OPTIONS
blue    ipv4 
```

#### policy

Allow piconet to access Internet:

```
#SOURCE         DEST            POLICY          LOG             LIMIT:BURST
#                                               LEVEL
blue            net             ACCEPT 
```

A rule like the following would allow the local network to access the piconet:

```
#SOURCE         DEST            POLICY          LOG             LIMIT:BURST
#
loc             all             ACCEPT 
```

The last line of the policy file should of course contain an all/all DROP rule.

#### masq

Allow local network to access piconet masquerading as piconet server:

```
#INTERFACE              SUBNET          ADDRESS         PROTO   PORT(S) IPSEC
$BLUE_IF        $LOC_IF 
```

Masquerade piconet network access to Internet

```
#INTERFACE              SUBNET          ADDRESS         PROTO   PORT(S) IPSEC
$NET_IF         $BLUE_IF 
```

#### rules

Not allowing your open piconet to do things like Spam and/or access your Cable modem is probably a good thing.

```
#ACTION         SOURCE  DEST            PROTO   DEST    SOURCE          ORIGINAL        RATE            USER/
#                                               PORT    PORT(S)         DEST            LIMIT           GROUP
SMTP/REJECT     blue    net
DROP   blue    net:10.0.0.0/8,192.168.0.0/16,172.16.0.0/12 
```

### Netfilter

A very basic [Netfilter setup](http://www.netfilter.org/documentation/HOWTO/NAT-HOWTO-4.html), assuming that eth1 connects to the Internet, and eth0 connects to the local network.

```
# Enable masquerading access to the Internet (rule may already exists)
iptables -t nat -A POSTROUTING -o eth1 -j MASQUERADE
# Enable masquerading access to the piconet from the local net
iptables -t nat -A POSTROUTING -i eth0 -o pan0 -j MASQUERADE
# Enable routing (may already exist)
echo 1 > /proc/sys/net/ipv4/ip_forward 
```

### Network Manager 0.7

[Network Manager](http://projects.gnome.org/NetworkManager/) provides connection sharing functionality. From the "Edit Connections" dialog, select "Add". Name the connection bnep0 and enter the Bluetooth device's MAC address into the Wired tab. Select "Shared to other computers" on the "IPv4 Settings" tab.

## Client Device

Embedded devices should execute the command:

```
pand --connect <bdaddr of piconet server> --persist -u ifup -o ifdown 
```

Upon boot, alternatively, the following command can be used:

```
pand --search --persist -u ifup -o ifdown 
```

### /etc/network/interfaces file

This step applies to Debian and Debian like (Angstrom/OE) distributions. Modification will be required for other distributions:

```
# Bluetooth networking
allow-hotplug bnep0
iface bnep0 inet dhcp 
```

### End result

ifconfig bnep0

```
bnep0     Link encap:Ethernet  HWaddr 00:1B:DC:0F:A8:AE
          inet addr:10.1.0.100  Bcast:10.1.0.255  Mask:255.255.255.0
          UP BROADCAST RUNNING MULTICAST  MTU:1500  Metric:1
          RX packets:29272 errors:0 dropped:0 overruns:0 frame:0
          TX packets:23598 errors:0 dropped:0 overruns:0 carrier:0
          collisions:0 txqueuelen:1000
          RX bytes:27242050 (25.9 MiB)  TX bytes:2964918 (2.8 MiB) 
```

route -n

```
Kernel IP routing table
Destination     Gateway         Genmask         Flags Metric Ref    Use Iface
10.1.0.0        0.0.0.0         255.255.255.0   U     0      0        0 bnep0
0.0.0.0         10.1.0.1        0.0.0.0         UG    0      0        0 bnep0 
```

hcitool con

```
Connections:
    < ACL 00:11:F6:05:79:95 handle 42 state 1 lm SLAVE 
```

[Category](http://eLinux.org/Special:Categories "Special:Categories"):

*   [Networking](http://eLinux.org/Category:Networking "Category:Networking")

# Continuous Logging for Watchdog Timer Expiration

> From: [eLinux.org](http://eLinux.org/Continuous_Logging_for_Watchdog_Timer_Expiration "http://eLinux.org/Continuous_Logging_for_Watchdog_Timer_Expiration")

# Continuous Logging for Watchdog Timer Expiration

Implementation Idea

I think this can be achieved by modifying the driver that services the hardware watchdog. This driver is aware of the hardware timeout (say this is N) and the application can make it a rule to always service the hardware watchdog device ([`eLinux.org/dev/wtd`](http://eLinux.org/dev/wtd)) every N/2 seconds. The driver can now warn when it has not been serviced during the first N/2 and has N/2 left to dump this event somehow.

--Leon Woestenberg

[Category](http://eLinux.org/Special:Categories "Special:Categories"):

*   [Kernel](http://eLinux.org/Category:Kernel "Category:Kernel")

# Crash Diagnostics

> From: [eLinux.org](http://eLinux.org/Crash_Diagnostics "http://eLinux.org/Crash_Diagnostics")

# Crash Diagnostics

kernel crash debugging technique using "crash" utility.

## Introduction

Most frequent issue in debugging kernel programs is lack of information; lack of information in terms of stack trace, kernel logs and crash screen-shots. Crash utility provides solution for this. It provides gdb like interface for debugging.

## Prerequisites

```
 vmlinux object file. 
```

This is kernel image with debugging information. By default, we get compressed kernel image with no debug information. For getting, vmlinux object file, you need to install "kernel-debuginfo" rpm. If you want to debug any module, make sure it is compiled with gcc flag '-g'.

## Installation

We receive crash utility as rpm.

```
 rpm name is of format: crash-$version.$arch.rpm 
```

Here, $version shows version number and $arch shows system architecture.

For getting crash dump: For getting crash dump, we need to add "crashkernel" option to grub command line. i.e. we can have:

```
 ro root=LABEL=/1 rhgb quiet crashkernel=128M@16M 
```

instead of,

```
 ro root=LABEL=/1 rhgb quiet 
```

Here, parameter "crashkernel=128M@16M" reserves 128MB of physical memory starting at 16MB. This reserved memory is used to preload and run the capture kernel (i.e. to capture crash dump). This command line option ensures that, whenever crash occurs, it stores crash dump at "/var/crash" and they are stored according to date and time.

There are other options to get crash dump like diskdump, netdump, etc.

Running crash Utility: 1\. Debugging last kernel panic:

```
 #crash <patht to vmlinux> /var/crash/crash_dump_name 
```

1.  Watching current running kernel:

    ```
    #crash 
    ```

This will prompt to crash shell. Just as example, following are commands:

```
 crash> help
   crash> bt                    ---> for backtrace
   crash> log                  ---> for dumping current system buffer 
```

For more information on this, you can visit at: [`www.redhatmagazine.com/2007/08/15/a-quick-overview-of-linux-kernel-crash-dump-analysis/`](http://www.redhatmagazine.com/2007/08/15/a-quick-overview-of-linux-kernel-crash-dump-analysis/) [`people.redhat.com/anderson/crash_whitepaper/`](http://people.redhat.com/anderson/crash_whitepaper/)

[Category](http://eLinux.org/Special:Categories "Special:Categories"):

*   [Development Tools](http://eLinux.org/Category:Development_Tools "Category:Development Tools")