# 技术观察清单

> From: [eLinux.org](http://eLinux.org/Technology_Watch_List "http://eLinux.org/Technology_Watch_List")

# Technology Watch List

This page lists technologies and projects that CELF members are interested in the status of. This includes kernel patches, new technology research, and middleware and user-space projects of key interest for consumer electronics products. The projects may be the topics of discussion at CELF meetings, and we plan to watch and report the status of these technologies.

Please add any information you have about the technology items listed below!!

## Contents

*   1 Latest Watchlist
*   2 Kernel Stuff
    *   2.1 Size Stuff
    *   2.2 File Systems
    *   2.3 Tracing and instrumentation
    *   2.4 Realtime
    *   2.5 Security
    *   2.6 Power Management
    *   2.7 Bootup Time
    *   2.8 Miscellaneous
*   3 Middleware

## Latest Watchlist

The **Status** field in the table below indicates whether this feature is on track for being mainlined. The **When was last activity** field indicates the kernel version number or date when the last activity was noted for this feature. This could be the last kernel version where bits from this patch were mainlined, or the last date of visible feature development activity outside the main tree.

See also: Embedded linux status

## Kernel Stuff

### Size Stuff

| Technology, Feature or Patch | Status | When was last activity | Notes |
| --- | --- | --- | --- |
| [Linux-tiny](http://elinux.org/Linux_Tiny) | In active maintenance. Path set published for 2.6.24 | Latest full patchset was published Oct. 13, 2007, Latest patch (CONSOLE_TRANSLATIONS) was mainlined in June 2008 | Maintainer is Michael Opdenacker (with help from Thomas Petazzoni). See [Linux Tiny Patch Details](http://elinux.org/Linux_Tiny_Patch_Details "Linux Tiny Patch Details") for details about the patch status. |
| kpagemap - memory instrumentation | mainlined in Feb, 2008 (for 2.6.25) | Feb 2008 |  
*   Can show details about every allocated and virtual page on the system.
*   Introduces PSS and USS size metrics
    *   PSS - Proportional Set Size
    *   USS - Unique Set Size
*   Resources:
    *   ELC 2007 presentation: [`selenic.com/repo/pagemap/raw-file/tip/memory-profiling.html`](http://selenic.com/repo/pagemap/raw-file/tip/memory-profiling.html)
    *   LWN.net article: [`lwn.net/Articles/230975/`](http://lwn.net/Articles/230975/)
    *   Visualization tools: [`selenic.com/repo/pagemap`](http://selenic.com/repo/pagemap)

 |
| [Bloatwatch](http://www.selenic.com/bloatwatch/) (2.0) | Now actively reporting kernel sizes | April 2008 | See [Matt's presentation from ELC 2008](http://www.celinux.org/elc08_presentations/elc2008.odp) |
| gcc -ffunction-sections -fdata-sections | Patches submitted to LKML | July 2008 | See [Function sections](http://elinux.org/Function_sections "Function sections") |

### File Systems

| Technology, Feature or Patch | Status | When was last activity | Notes |
| --- | --- | --- | --- |
| [Squash Fs](http://elinux.org/Squash_Fs "Squash Fs") | Mainlined in January 2009 (for 2.6.29) See [`lwn.net/Articles/314326/`](http://lwn.net/Articles/314326/). | As of Feb, 2009, Phillip was working on some user-space tools issues, to support the new filesystem format (version 4.0) | Good job Phillip! |
| [AXFS](http://elinux.org/AXFS "AXFS") | Not mainlined. | Some required (predecessor) bits were mainlined in March, 2008, A version was submitted to LKML in August 2008 | Flash filesystem that allows for tuning the amount of [XIP](http://elinux.org/Application_XIP "Application XIP") 
*   See [AXFS: Architecture and Results](http://www.celinux.org/elc08_presentations/AXFS_at_ELC_2008.ppt) - Jared Hulbert's presentation for ELC 2008

 |
| LogFS | Not mainlined. | Last mainline attempt was May, 2008. |  
*   Home page is at: [`logfs.org/logfs/`](http://logfs.org/logfs/)
*   Jörn Engel is working on a re-write to address several major issues.
*   CELF is funding this work.

 |
| UBIFS | mainlined in 2.6.27 |  | Resources: 
*   [home page?](http://www.linux-mtd.infradead.org/doc/ubifs.html)
*   [white paper](http://www.linux-mtd.infradead.org/doc/ubifs_whitepaper.pdf)
*   [LWN.net article](http://lwn.net/Articles/276025/)

 |
| [Pram Fs](http://elinux.org/Pram_Fs "Pram Fs") | submitted for review for 2.6.31 | June 2009 | See [`lkml.org/lkml/2009/6/13/86`](http://lkml.org/lkml/2009/6/13/86) |

### Tracing and instrumentation

| Technology, Feature or Patch | Status | When was last activity | Notes |
| --- | --- | --- | --- |
| LTTng | core not mainlined. Markers were mainlined in 2.6.24 | ? | LTTng instrumentation was changed to use markers, in early 2007 |
| [SystemTap](http://elinux.org/System_Tap) (and Kprobes) for non-i386 arches | ARM support merged for 2.6.25 | ? | KProbes ports for ARM, MIPS and PPC32 were reported on at ELC 2007, SystemTap for SH was demo'ed at ELC 2007 |
| [Kernel Function Trace](http://elinux.org/Kernel_Function_Trace) (KFT) | not mainlined | Last published patches for 2.6.22 | Nicholas McGuire was taking over maintainership from Tim Bird, with funding from CELF. Haven't heard much recently (as of June 2008) |
| ftrace | mainlined in 2.6.27 |  | This was formerly the latency-trace features from the RT-preempt patch set, refactored for general use |
| printk-times (arch support) | fully mainlined? | April, 2005 | Some arches had problems with accessing the clock too early in the kernel bootup sequence, but a new setup routine defers turning on the timestamping until after timekeeping is initialized |

### Realtime

| Technology, Feature or Patch | Status | When was last activity | Notes |
| --- | --- | --- | --- |
| KTimers | mainlined, but needs lots of porting to embedded architectures | 2.6.23 / 2.6.24 | Adding clock driver support for various architectures is an ongoing process |
| RT-preempt | some parts mainlined | current RT patches are still against 2.6.26 | Next target is to integrate threaded interrupts in 2.6.29 - as discussed on LinuxPlumbers conference Oregon 2008 |
| Xenomai | external project | 2.6.25 - stable release, newer in development | Xenomai is a real-time development framework cooperating with the Linux kernel, in order to provide a pervasive, interface-agnostic, hard real-time support to user-space applications, seamlessly integrated into the GNU/Linux environment. Ready to deploy. |

### Security

| Technology, Feature or Patch | Status | When was last activity | Notes |
| --- | --- | --- | --- |
| App Armour | not mainlined | May, 2007 |  
*   LSM framework was removed from kernel in 2.6.24
*   AppArmour group was let go from Novell in late 2007
    *   See [`www.news.com/8301-13580_3-9796140-39.html`](http://www.news.com/8301-13580_3-9796140-39.html)
    *   [LWN.net article](http://lwn.net/SubscriberLink/254740/f71fe8e26c906233/)

 |
| [TOMOYO Linux](http://elinux.org/TomoyoLinux) | not mainlined | [Nov 17, 2007](http://lwn.net/Articles/258905/) (4th post) [(trying now)](http://elinux.org/TomoyoLinux#Mainline) | "TOMOYO Linux has only recently surfaced on the wider mailing lists; its reception has not been entirely friendly. This project's developers have some work to do if they are (1) to get past the same obstacles which have slowed AppArmor, and (2) show that their project is sufficiently different from AppArmor to merit inclusion as yet another security framework." (from [Linux Weather Forecast](http://www.linux-foundation.org/en/Linux_Weather_Forecast/security)) |
| SMACK | mainlined in 2.6.25 | . | [LWN.net article on SMACK](http://lwn.net/Articles/244531/) |
| SELinux (usable in embedded) | SELinux is mainlined, but some issues for making SELinux usable for embedded remain | April 2008 |  
*   There has been much progress recently to support SELinux in the embedded space
*   Requires filesytem with xattrs (some flash filesystems do not support xattrs)
*   at ELC 2008, Yuichi Nakamura described an embedded configuration of SELinux in as little as 700K
    *   [Development of Embedded SELinux](http://www.celinux.org/elc08_presentations/ELC2008_nakamura.pdf) - Yuichi Nakamura presentation at ELC 2008

 |

### Power Management

| Technology, Feature or Patch | Status | When was last activity | Notes |
| --- | --- | --- | --- |
| powertop | support for powertop is mainlined (for x86 architecture) | ? |  
*   Powertop shows timers and power state durations
*   Only well-supported on x86??
    *   To support on other architectures, the platform needs to support CPUIdle interface, in order to show C-state (power state) ** There has been some activity for non-Intel processors

 |
| PM QoS | in 2.6.23-mm1 | Oct '07 | (see [`lesswatts.org`](http://lesswatts.org)) need Embedded folks to take a look and help define the interface, expand the features and raise issues from the embedded perspective. |
| Wolfson voltage regulator stuff | not mainlined?? | March 2008? | See [Every Microamp is Sacred - A Dynamic Voltage and Current Control Interface for the Linux Kernel](http://www.celinux.org/elc08_presentations/regulator-api-celf.pdf) - Liam Girdwood's ELC 2008 presentation |

### Bootup Time

| Technology, Feature or Patch | Status | When was last activity | Notes |
| --- | --- | --- | --- |
| deferred module load | not mainlined, no patches | July 2008 | Proposed, without patches, by Tim Bird on linux-embedded list |
| async initcalls | not mainlined | July 2008 | Patches submitted by Arjan van de Ven |
| snapshot boot | not mainlined | July 2006 | Sony presented paper at OLS 2006\. This technology is used in Sony products. |
| fastboot kernel configuration option | not mainlined? | July 2006 | Arjan van de Ven submitted as part of his async initcalls patches |

### Miscellaneous

| Technology, Feature or Patch | Status | When was last activity | Notes |
| --- | --- | --- | --- |
| Userspace I/O | Seems to be merged into mainline (see: [`lwn.net/Articles/242483/`](http://lwn.net/Articles/242483/) ) | July, 2007 | References: [`www.kroah.com/log/linux/uio.html`](http://www.kroah.com/log/linux/uio.html) |

## Middleware

| Project | Status | When was last activity | Notes |
| --- | --- | --- | --- |
| libdlna | Developer has added support for all profiles except MPEG-4 and WMV ( [`hg.geexbox.org/libdlna/`](http://hg.geexbox.org/libdlna/) ) | 29 Aug, 07 | Short term goal is to provide DLNA support to Ushare media server, long term goal is to provide generic DLNA reference library References: [`libdlna.geexbox.org/`](http://libdlna.geexbox.org/) |

[Category](http://eLinux.org/Special:Categories "Special:Categories"):

*   [Community](http://eLinux.org/Category:Community "Category:Community")