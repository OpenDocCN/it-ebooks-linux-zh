# FreeBSD 术语表

本术语表包含了 FreeBSD 社区和文档使用的术语和缩略语。

### A

ACL

见 Access Control List (访问控制表).

ACPI

见 Advanced Configuration and Power Interface (高级配置和电源接口).

AMD

见 Automatic Mount Daemon (自动挂载服务).

AML

见 ACPI 机器语言.

API

见 Application Programming Interface (应用程序编程接口).

APIC

见 Advanced Programmable Interrupt Controller (高级可编程中断控制器).

APM

见 Advanced Power Management (高级电源管理).

APOP

见 Authenticated Post Office Protocol (带认证的邮局协议).

ASL

见 ACPI 源语言.

ATA

见 Advanced Technology Attachment (先进技术附件).

ATM

见 Asynchronous Transfer Mode (异步传输模式).

ACPI 机器语言

一种可以由符合 ACPI 规范的操作系统提供的虚机解释执行的伪码， 在底层硬件与提供给 OS 的文档接口之间提供一个抽象层。

ACPI 源语言

用于撰写 AML 的语言。

Access Control List (访问控制表)

对于某一个对象的许可列表，通常是一个文件或网络设备。

Advanced Configuration and Power Interface (高级配置和电源接口)

一种为实现对于硬件呈现给操作系统的接口进行抽象的标准， 它使得操作系统在不需要了解底层硬件的情况下能够使用其绝大多数功能。 ACPI 发展并超越了过去由 APM、 PNPBIOS 以及其他技术所提供的能力， 并提供了用于控制电力耗用、 机器休眠、 启用和禁用设备等功能的支持。

Application Programming Interface (应用程序编程接口)

指定一个或多个程序组成部分之间规范交互的例程、协议和工具集合； 指定这些程序组成部分之间如何、何时、为何协同工作， 以及共享或操作什么数据。

Advanced Power Management (高级电源管理)

一个使得操作系统与 BIOS 协作实现电源管理的 API。 对大多数的应用程序来说 APM 已被更通用更强大的 ACPI 所取代。

Advanced Programmable Interrupt Controller (高级可编程中断控制器)

Advanced Technology Attachment (先进技术附件)

Asynchronous Transfer Mode (异步传输模式)

Authenticated Post Office Protocol (带认证的邮局协议)

Automatic Mount Daemon (自动挂载服务)

一种用于在访问文件或目录时自动挂接文件系统的服务。

### B

BAR

见 Base Address Register (基地址寄存器).

BIND

见 Berkeley Internet Name Domain (伯克利 Internet 域名服务).

BIOS

见 Basic Input/Output System (基本输入输出系统).

BSD

见 Berkeley Software Distribution (伯克利软件发行).

Base Address Register (基地址寄存器)

决定一个 PCI 设备应向什么地址范围做出反应的寄存器。

Basic Input/Output System (基本输入输出系统)

BIOS 的定义， 在一定意义上取决于其上下文。 一些人用它来表示包含了一系列用以提供软硬件间接口的基础例程的 ROM 芯片， 而其他一些人， 则用它来表示这芯片中用于帮助引导系统的那一部分例程。 此外， 还有一些人用它来表示用于在系统引导时进行配置的屏幕提示。 BIOS 是 PC 上的专有词汇， 但其他系统上也有一些类似的机制。

Berkeley Internet Name Domain (伯克利 Internet 域名服务)

DNS 协议的一种实现。

Berkeley Software Distribution (伯克利软件发行)

由 [加州大学伯克利分校](http://www.berkeley.edu) 的 计算机系统研究小组 (CSRG) 对其所发布的对于 AT&T 的 32V UNIX® 所做改进和修正软件包所起的名字。 FreeBSD 源自 CSRG 的成果。

Bikeshed Building (打口水仗)

一种许多人在简单的话题上发表大量意见， 而忽略那些复杂的问题的现象。 参见 FAQ 以了解这一术语的来历。

### C

CD

见 Carrier Detect (载波侦测).

CHAP

见 Challenge Handshake Authentication Protocol (挑战握手认证协议).

CLIP

见 Classical IP over ATM (传统的 ATM 承载 IP).

COFF

见 Common Object File Format (通用对象文件格式).

CPU

见 Central Processing Unit (中央处理器).

CTS

见 Clear To Send (允许发送).

CVS

见 Concurrent Versions System (并发版本系统).

Carrier Detect (载波侦测)

一种表示检测到载波的 RS232C 信号。

Central Processing Unit (中央处理器)

也称作处理器。 这是计算机的大脑， 所有的计算工作均在此处发生。 在不同的硬件架构之上， 采用的指令集也不尽相同。 除了最为人们熟知的 Intel-x86 及派生的硬件架构之外， 还有 Sun SPARC、 PowerPC 以及 Alpha 等硬件架构。

Challenge Handshake Authentication Protocol (挑战握手认证协议)

一种用户认证的方法，基于客户端与服务器之间的共享密钥 （secret shared）。

Classical IP over ATM (传统的 ATM 承载 IP)

Clear To Send (允许发送)

表示允许远程系统发送数据的 RS232C 信号。

参见 Request To Send (请求发送).

Common Object File Format (通用对象文件格式)

Concurrent Versions System (并发版本系统)

A version control system, providing a method of working with and keeping track of many different revisions of files. CVS provides the ability to extract, merge and revert individual changes or sets of changes, and offers the ability to keep track of which changes were made, by who and for what reason.

### D

DAC

见 Discretionary Access Control (分立式访问控制).

DDB

见 Debugger (调试器).

DES

见 Data Encryption Standard (数据加密标准).

DHCP

见 Dynamic Host Configuration Protocol (动态主机配置协议).

DNS

见 Domain Name System (域名系统).

DSDT

见 Differentiated System Description Table (系统差异描述表).

DSR

见 Data Set Ready (数据设备就绪).

DTR

见 Data Terminal Ready (数据终端就绪).

DVMRP

见 Distance-Vector Multicast Routing Protocol (距离-矢量 组播路由协议).

Discretionary Access Control (分立式访问控制)

Data Encryption Standard (数据加密标准)

A method of encrypting information, traditionally used as the method of encryption for UNIX® passwords and the [crypt(3)](http://www.FreeBSD.org/cgi/man.cgi?query=crypt&sektion=3&manpath=freebsd-release-ports) function.

Data Set Ready (数据设备就绪)

An RS232C signal sent from the modem to the computer or terminal indicating a readiness to send and receive data.

参见 Data Terminal Ready (数据终端就绪).

Data Terminal Ready (数据终端就绪)

An RS232C signal sent from the computer or terminal to the modem indicating a readiness to send and receive data.

Debugger (调试器)

An interactive in-kernel facility for examining the status of a system, often used after a system has crashed to establish the events surrounding the failure.

Differentiated System Description Table (系统差异描述表)

一个 ACPI 表， 提供系统的基本配置信息。

Distance-Vector Multicast Routing Protocol (距离-矢量 组播路由协议)

Domain Name System (域名系统)

用以将便于人类辨识的主机名 (例如， mail.example.net) 与 Internet 地址相互转换的系统。

Dynamic Host Configuration Protocol (动态主机配置协议)

一种能够在收到请求时， 动态分配 IP 地址给计算机 (主机) 的协议。 分配出去的地址， 也称为 “租期”。

### E

ECOFF

见 Extended COFF (扩展的 COFF).

ELF

见 Executable and Linking Format (可执行与连接格式).

ESP

见 Encapsulated Security Payload (安全载荷封装).

Encapsulated Security Payload (安全载荷封装)

Executable and Linking Format (可执行与连接格式)

Extended COFF (扩展的 COFF)

### F

FADT

见 Fixed ACPI Description Table (固定 ACPI 描述表).

FAT

见 File Allocation Table (文件分配表).

FAT16

见 File Allocation Table (16-bit) (16-位文件分配表).

FTP

见 File Transfer Protocol (文件传输协议).

File Allocation Table (文件分配表)

File Allocation Table (16-bit) (16-位文件分配表)

File Transfer Protocol (文件传输协议)

一种在 TCP 上实现的高级协议， 可以用于在 TCP/IP 网络上传送文件。

Fixed ACPI Description Table (固定 ACPI 描述表)

### G

GUI

见 Graphical User Interface (图形用户界面).

Giant (全局锁)

用以保护大量内核资源的一种互斥排他机制 (一种 `休眠互斥体`, sleep mutex) 的名字。 尽管这种简单的上锁机制在计算机上运行几十个进程、 使用一块网卡， 且只有一个处理器的哪个时代表现良好， 但在现时它已经成为无法容忍的性能瓶颈。 FreeBSD 的开发人员目前正在积极地将它拆解为保护更细粒度的资源的锁， 这使得在单处理器和多处理器的机器上， 都能够提供更大的并发处理能力。

Graphical User Interface (图形用户界面)

一种能够让用户与计算机之间以图形方式交互的系统。

### H

HTML

见 HyperText Markup Language (超文本标记语言).

HUP

见 HangUp (挂断).

HangUp (挂断)

HyperText Markup Language (超文本标记语言)

用以创建 web 页面的标记语言。

### I

I/O

见 Input/Output (输入/输出).

IASL

见 Intel 的 ASL 编译器.

IMAP

见 Internet Message Access Protocol (Internet 邮件访问协议).

IP

见 Internet Protocol (互联网协议).

IPFW

见 IP Firewall (IP 防火墙).

IPP

见 Internet Printing Protocol (Internet 打印协议).

IPv4

见 IP Version 4 (IP 第 4 版).

IPv6

见 IP Version 6 (IP 第 6 版).

ISP

见 Internet Service Provider (互联网服务提供者).

IP Firewall (IP 防火墙)

IP Version 4 (IP 第 4 版)

IP 协议第 4 版，使用 32 位编址。 这个版本目前仍是使用范围最广的网络协议， 但正慢慢的被 IPv6 取代。

参见 IP Version 6 (IP 第 6 版).

IP Version 6 (IP 第 6 版)

新的 IP 协议。 因为 IPv4 地址空间将被耗尽而被发明， 它使用 128 位编址。

Input/Output (输入/输出)

Intel 的 ASL 编译器

Intel 的编译器， 能够将 ASL 编译为 AML。

Internet Message Access Protocol (Internet 邮件访问协议)

A protocol for accessing email messages on a mail server, characterised by the messages usually being kept on the server as opposed to being downloaded to the mail reader client.

参见 Post Office Protocol Version 3 (邮局协议第 3 版).

Internet Printing Protocol (Internet 打印协议)

Internet Protocol (互联网协议)

一种包传输协议， 是 Internet 上的基本协议。 最初由美国国防部开发， 在 TCP/IP 协议栈中有着非常重要的地位。 假如没有互联网协议， Internet 将不会成为今天这样。 欲知更多信息， 参见 RFC 791。

Internet Service Provider (互联网服务提供者)

提供 Internet 访问服务的公司。

### K

KAME

在日本语中表示 “海龟”。 术语 KAME 在计算机领域内， 通常用来指 [KAME 计划](http://www.kame.net/)， 该计划致力于完成一个 IPv6 实现。

KDC

见 Key Distribution Center (密钥分发中心).

KLD

见 Kernel ld(1).

KSE

见 Kernel Scheduler Entities (内核调度器实体).

KVA

见 Kernel Virtual Address (内核虚拟地址).

Kbps

见 Kilo Bits Per Second (Kb 每秒、千二进制位每秒).

Kernel [ld(1)](http://www.FreeBSD.org/cgi/man.cgi?query=ld&sektion=1&manpath=freebsd-release-ports)

A method of dynamically loading functionality into a FreeBSD kernel without rebooting the system.

Kernel Scheduler Entities (内核调度器实体)

一个由内核支持的线程系统。 参见 [该项目主页](http://www.FreeBSD.org/kse) 以获得更多详细信息。

Kernel Virtual Address (内核虚拟地址)

Key Distribution Center (密钥分发中心)

Kilo Bits Per Second (Kb 每秒、千二进制位每秒)

带宽 (一段指定时间内能够通过一个给定点的数据量) 单位。 除了前缀 Kilo (1024、 千)， 还有前缀 Mega (兆)、 Giga (吉)、 Tera 等。

### L

LAN

见 Local Area Network (局域网).

LOR

见 Lock Order Reversal (锁逆序).

LPD

见 Line Printer Daemon (行式打印机服务).

Line Printer Daemon (行式打印机服务)

Local Area Network (局域网)

用于局部范围内， 如办公室、 家庭等的网络。

Lock Order Reversal (锁逆序)

FreeBSD 内核使用一系列资源锁来对资源的竞争使用进行仲裁。 在 FreeBSD-CURRENT 内核中的运行时锁诊断系统 (在发行版本中会去掉) 称为 [witness(4)](http://www.FreeBSD.org/cgi/man.cgi?query=witness&sektion=4&manpath=freebsd-release-ports)， 会检测由于锁的问题可能导致的潜在死锁。 ([witness(4)](http://www.FreeBSD.org/cgi/man.cgi?query=witness&sektion=4&manpath=freebsd-release-ports) 实际上会比较保守， 因此可能存在误报现象。) 由它产生的问题报告表示 “如果您运气不好的话， 死锁一定会在此处发生”。

真正的 LOR 通常会很快修正， 因此在您到邮件列表中发言之前， 请首先阅读 http://lists.FreeBSD.org/mailman/listinfo/freebsd-current 和 [已知的 LOR](http://sources.zabbadoz.net/freebsd/lor.html) 网页。

### M

MAC

见 Mandatory Access Control (集权式访问控制).

MADT

见 Multiple APIC Description Table (多 APIC 描述表).

MFC

见 Merge From Current (从当前版本合并).

MFP4

见 Merge From Perforce (从 Perforce 合并).

MFS

见 Merge From Stable (从稳定版本合并).

MIT

见 Massachusetts Institute of Technology (马萨诸塞理工学院).

MLS

见 Multi-Level Security (多级别安全).

MOTD

见 Message Of The Day (当天消息).

MTA

见 Mail Transfer Agent (邮件传送工具).

MUA

见 Mail User Agent (邮件用户工具).

Mail Transfer Agent (邮件传送工具)

一种用于传送电子邮件的应用程序。 传统上， MTA 是 BSD 基本系统的一部分。 目前， 基本系统中仍然包含 Sendmail， 但也有许多其他可选的 MTA， 例如 postfix、 qmail 和 Exim。

Mail User Agent (邮件用户工具)

用于让用户能够显示和撰写电子邮件的应用程序。

Mandatory Access Control (集权式访问控制)

Massachusetts Institute of Technology (马萨诸塞理工学院)

Merge From Current (从当前版本合并)

表示从 -CURRENT 分支合并功能或补丁到另一个分支， 通常是 -STABLE 的操作。

Merge From Perforce (从 Perforce 合并)

将功能或补丁从 Perforce 仓库合并到 -CURRENT 分支的操作。

参见 Perforce.

Merge From Stable (从稳定版本合并)

在正常的 FreeBSD 开发过程中， 变更会首先提交到 -CURRENT 分支进行测试，之后才会被合并到 -STABLE 分支。在很偶然的情形中，更改会先进入 -STABLE 分支，再被合并到 -CURRENT 分支。

这一术语在从 -STABLE 向安全分支合并补丁时也适用。

参见 Merge From Current (从当前版本合并).

Message Of The Day (当天消息)

一种通常在登录时显示的消息， 主要用于向用户发布关于系统的消息。

Multi-Level Security (多级别安全)

Multiple APIC Description Table (多 APIC 描述表)

### N

NAT

见 Network Address Translation (网络地址翻译).

NDISulator

见 Project Evil (邪恶计划).

NFS

见 Network File System (网络文件系统).

NTFS

见 New Technology File System (新技术文件系统).

NTP

见 Network Time Protocol (网络时间协议).

Network Address Translation (网络地址翻译)

一种通过重写 IP 数据包来穿过网关， 使得网关后面很多机器能有效的共享一个 IP 地址。

Network File System (网络文件系统)

New Technology File System (新技术文件系统)

一种由 Microsoft® 开发的并在他们的 “新技术” (NT, New Technology) 操作系统，如 Windows® 2000, Windows NT® 和 Windows® XP 中应用的文件系统。

Network Time Protocol (网络时间协议)

一种通过网络同步时钟的方法。

### O

OBE

见 Overtaken By Events (不再适用，汉语意即：计划赶不上变化).

ODMR

见 On-Demand Mail Relay (邮件按需中转).

OS

见 Operating System (操作系统).

On-Demand Mail Relay (邮件按需中转)

Operating System (操作系统)

一组提供访问计算机硬件资源能力的程序、 函数库和工具。 现今的操作系统从最简单的一次只运行一个程序、 访问一种设备， 到能够支持数千用户同时使用、 每个用户执行数十个不同的应用程序的、 完全支持多用户、 多任务和多道处理系统都有。

Overtaken By Events (不再适用，汉语意即：计划赶不上变化)

表示所建议的变更 (例如问题报告或需求) 由于 FreeBSD 后来所做的变动、 网络标准、 硬件过时等原因而而失去意义或不再适用。

### P

p4

见 Perforce.

PAE

见 Physical Address Extensions (物理地址扩展).

PAM

见 Pluggable Authentication Modules (可插入认证模块).

PAP

见 Password Authentication Protocol (密码认证协议).

PC

见 Personal Computer (个人计算机).

PCNSFD

见 Personal Computer Network File System Daemon (个人计算机网络文件系统服务).

PDF

见 Portable Document Format (可移植文档格式).

PID

见 Process ID (进程标识).

POLA

见 Principle Of Least Astonishment (最少惊动原则).

POP

见 Post Office Protocol (邮局协议).

POP3

见 Post Office Protocol Version 3 (邮局协议第 3 版).

PPD

见 PostScript Printer Description (PostScript 打印机描述).

PPP

见 Point-to-Point Protocol (点对点协议).

PPPoA

见 PPP over ATM (ATM 上的 PPP).

PPPoE

见 PPP over Ethernet (以太网上的 PPP).

PPP over ATM (ATM 上的 PPP)

PPP over Ethernet (以太网上的 PPP)

PR

见 Problem Report (问题报告).

PXE

见 Preboot eXecution Environment (引导前执行环境).

Password Authentication Protocol (密码认证协议)

Perforce

一种由 [Perforce 软件](http://www.perforce.com/) 编写的比 CVS 更先进的版本控制软件。 尽管它本身并不开放源代码， 但它对类似 FreeBSD 这样的开源项目是免费的。

一些 FreeBSD 开发人员将 Perforce 代码库作为保存那些对 -CURRENT 而言， 试验性质也太强的代码的阶段性成果。

Personal Computer (个人计算机)

Personal Computer Network File System Daemon (个人计算机网络文件系统服务)

Physical Address Extensions (物理地址扩展)

一种使物理寻址能力只有 32 位地址 (因而在没有 PAE 时， 只能访问 4 GB 虚拟地址空间) 的系统能够访问 64 GB RAM 的方法。

Pluggable Authentication Modules (可插入认证模块)

Point-to-Point Protocol (点对点协议)

Pointy Hat (尖帽子)

一件虚构的头饰，很像一件 `傻瓜帽`，奖励给使联编过程出现问题、 版本号发生倒退， 或给源代码库引入其他大问题的 FreeBSD committer。 许多活跃的 committer 很快就能积攒起一大堆。 这种用法是 (几乎总是?) 一种幽默的方式。

Portable Document Format (可移植文档格式)

Post Office Protocol (邮局协议)

参见 Post Office Protocol Version 3 (邮局协议第 3 版).

Post Office Protocol Version 3 (邮局协议第 3 版)

A protocol for accessing email messages on a mail server, characterised by the messages usually being downloaded from the server to the client, as opposed to remaining on the server.

参见 Internet Message Access Protocol (Internet 邮件访问协议).

PostScript Printer Description (PostScript 打印机描述)

Preboot eXecution Environment (引导前执行环境)

Principle Of Least Astonishment (最少惊动原则)

当 FreeBSD 发展时， 用户可见的更改应尽可能不引起用户的惊奇。 例如， 武断的重新安排 `/etc/defaults/rc.conf` 中的系统启动变量就违反了 POLA。 当开发者要做用户可见的系统更改时， 就应考虑 POLA。

Problem Report (问题报告)

对于在 FreeBSD 源代码或文档中找到某种问题的描述。 参见 如何书写 FreeBSD 问题报告。

Process ID (进程标识)

一个用于唯一标识系统中进程的数字， 当对进程进行各种操作时， 也使用它来指定具体的进程。

Project Evil (邪恶计划)

NDISulator 的工作代号， 由于 Bill Paul 编写，他 (从一个哲学观点) 根据完成这样一个工程所首先需要付出努力的可怕程度做了如此命名。 NDISulator 是一个特别的兼容模块， 使得 Microsoft Windows™ NDIS miniport 网络驱动程序可用于 FreeBSD/i386。 通常， 在网卡厂商封锁了驱动程序的源代码时， 这是能够使用这些网卡的唯一方式。 参见 `src/sys/compat/ndis/subr_ndis.c`。

### R

RA

见 Router Advertisement (路由器通告).

RAID

见 Redundant Array of Inexpensive Disks (廉价磁盘冗余阵列).

RAM

见 Random Access Memory (随机存储器).

RD

见 Received Data (数据已收到).

RFC

见 Request For Comments (意见征求书).

RISC

见 Reduced Instruction Set Computer (精减指令系统计算机； 又译：精简指令集计算机).

RPC

见 Remote Procedure Call (远程过程调用).

RS232C

见 Recommended Standard 232C (推荐标准 232C).

RTS

见 Request To Send (请求发送).

Random Access Memory (随机存储器)

Revision Control System

*Revision Control System* (RCS) 是最早实现普通文件 “版本控制” 的软件包之一。 它提供了存储、 获取、 保存归档、 记录、 命名和合并文件中多个版本的能力。 RCS 包含了许多可以配合使用的小工具。 它并不具备更现代化的版本控制系统， 例如 CVS 或 Subversion 所提供的某些功能， 但对于少量文件的管理而言， 其易于安装、 配置和使用则可看作优势。 RCS 的实现可以在几乎每一种主流 类-UNIX OS 上找到。

参见 Concurrent Versions System (并发版本系统), Subversion.

Received Data (数据已收到)

An RS232C pin or wire that data is recieved on.

参见 Transmitted Data (数据已送出).

Recommended Standard 232C (推荐标准 232C)

在串口设备之间通信的一个标准。

Reduced Instruction Set Computer (精减指令系统计算机； 又译：精简指令集计算机)

An approach to processor design where the operations the hardware can perform are simplified but made as general purpose as possible. This can lead to lower power consumption, fewer transistors and in some cases, better performance and increased code density. Examples of RISC processors include the Alpha, SPARC®, ARM® and PowerPC®.

Redundant Array of Inexpensive Disks (廉价磁盘冗余阵列)

Remote Procedure Call (远程过程调用)

repocopy

见 Repository Copy (仓库复制).

Repository Copy (仓库复制)

在 CVS 仓库内对于文件的直接复制。

如果不用仓库复制， 在需要将一个文件复制或移动到仓库中的另一位置时， CVS committer 会使用 `cvs add` 将文件放到新位置， 而如果旧的副本需要删除， 则对旧文件执行 `cvs rm`。

这种方法的缺点是无法将该文件的历史记录 (那是指 CVS 记录的一个个项目) 复制到新位置。 由于 FreeBSD 计划认为这些历史记录很有用， 因此， 我们通常采用的方法是进行一次仓库复制操作。 在这个过程中， 仓库管理员会在仓库内部直接复制文件， 而不是使用 [cvs(1)](http://www.FreeBSD.org/cgi/man.cgi?query=cvs&sektion=1&manpath=freebsd-release-ports) 程序。

Request For Comments (意见征求书)

一组定义 Internet 标准、 协议等的文档。 参见 [www.rfc-editor.org](http://www.rfc-editor.org/)。

同时， 这也是在修改提议征求意见时的一个通用术语。

Request To Send (请求发送)

An RS232C signal requesting that the remote system commences transmission of data.

参见 Clear To Send (允许发送).

Router Advertisement (路由器通告)

### S

SCI

见 System Control Interrupt (系统控制中断).

SCSI

见 Small Computer System Interface (小型机系统接口).

SG

见 Signal Ground (信号地).

SMB

见 Server Message Block (服务器消息块).

SMP

见 Symmetric MultiProcessor (对称多处理).

SMTP

见 Simple Mail Transfer Protocol (简单邮件传送协议).

SMTP AUTH

见 SMTP Authentication (SMTP 认证).

SSH

见 Secure Shell (安全 Shell).

STR

见 Suspend To RAM (挂起至 RAM).

SVN

见 Subversion.

SMTP Authentication (SMTP 认证)

Server Message Block (服务器消息块)

Signal Ground (信号地)

一个 RS232 插针或导线，是信号的参考地电平。

Simple Mail Transfer Protocol (简单邮件传送协议)

Secure Shell (安全 Shell)

Small Computer System Interface (小型机系统接口)

Subversion

Subversion 是一个版本控制系统, 类似于 CVS， 但是有一系列扩展的功能。

参见 Concurrent Versions System (并发版本系统).

Suspend To RAM (挂起至 RAM)

Symmetric MultiProcessor (对称多处理)

System Control Interrupt (系统控制中断)

### T

TCP

见 Transmission Control Protocol (传输控制协议).

TCP/IP

见 Transmission Control Protocol/Internet Protocol (传输控制协议/互联网协议).

TD

见 Transmitted Data (数据已送出).

TFTP

见 Trivial FTP (简单 FTP).

TGT

见 Ticket-Granting Ticket (票据授予票据).

TSC

见 Time Stamp Counter (时间戳计数器).

Ticket-Granting Ticket (票据授予票据)

Time Stamp Counter (时间戳计数器)

一种在现代 Pentium® 处理器内部的性能计数器， 用于提供处理器核心的频率时钟脉冲计数。

Transmission Control Protocol (传输控制协议)

一种运行于诸如 IP 等协议之上的协议， 确保包以一种可靠、有序的方式传送。

Transmission Control Protocol/Internet Protocol (传输控制协议/互联网协议)

表示在 IP 协议之上运行 TCP 这种组合的术语。 Internet 的大部分运行于 TCP/IP 之上。

Transmitted Data (数据已送出)

An RS232C pin or wire that data is transmitted on.

参见 Received Data (数据已收到).

Trivial FTP (简单 FTP)

### U

UDP

见 User Datagram Protocol (用户数据报文协议).

UFS1

见 Unix File System Version 1 (Unix 文件系统第 1 版).

UFS2

见 Unix File System Version 2 (Unix 文件系统第 2 版).

UID

见 User ID (用户标识).

URL

见 Uniform Resource Locator (统一资源定位符).

USB

见 Universal Serial Bus (通用串行总线).

Uniform Resource Locator (统一资源定位符)

一种定位资源的方法， 比如标识 Internet 上的某一份文档。

Unix File System Version 1 (Unix 文件系统第 1 版)

最初的 UNIX® 文件系统， 有时也称作伯克利快速文件系统。

Unix File System Version 2 (Unix 文件系统第 2 版)

对 USF1 的扩展， 由 FreeBSD 5-CURRENT 时引入。 UFS2 增加了 64 位块指针 (破除了 1T 的限制)， 支持扩展文件存储和其他特性。

Universal Serial Bus (通用串行总线)

一种硬件标准， 用来连接各种计算机外围设备的通用接口。

User ID (用户标识)

指派给某一计算机上每个用户的唯一号码。 通过该号码将资源和权限分派给可被标识的用户。

User Datagram Protocol (用户数据报文协议)

一种简单， 不可靠的数据报协议， 用来在 TCP/IP 网络种交换数据。 UDP 不提供类似 TCP 的错误校验与修正。

### V

VPN

见 Virtual Private Network (虚拟专用网络).

Virtual Private Network (虚拟专用网络)

一种使用公共通讯比如 Internet， 提供远程访问一个本地网络， 比如某个公司的 LAN 的方法。