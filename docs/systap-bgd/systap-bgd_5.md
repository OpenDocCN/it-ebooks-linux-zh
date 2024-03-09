# SystemTap 脚本集锦

# 5\. SystemTap 脚本集锦

本章列举了若干可用于监控和调查内核子系统的 SystemTap 脚本。如果你安装了`systemtap-testsuite`这个 RPM 包，所有这些示例都能在`/usr/share/systemtap/testsuite/systemtap.examples/`下找到。

# 网络

# 5.1\. 网络

以下各节的脚本展示了如何跟踪网络相关的函数和剖析（profile）网络活动。

## 剖析网络活动

本节展示 SystemTap 中剖析网络活动的方式。下面的`nettop.stp`允许我们一窥每个进程的网络流量使用情况。

nettop.stp

```
#! /usr/bin/env stap

global ifxmit, ifrecv
global ifmerged

probe netdev.transmit
{
  ifxmit[pid(), dev_name, execname(), uid()] <<< length
}

probe netdev.receive
{
  ifrecv[pid(), dev_name, execname(), uid()] <<< length
}

function print_activity()
{
  printf("%5s %5s %-7s %7s %7s %7s %7s %-15s\n",
         "PID", "UID", "DEV", "XMIT_PK", "RECV_PK",
         "XMIT_KB", "RECV_KB", "COMMAND")

  foreach ([pid, dev, exec, uid] in ifrecv) {
      ifmerged[pid, dev, exec, uid] += @count(ifrecv[pid,dev,exec,uid]);
  }
  foreach ([pid, dev, exec, uid] in ifxmit) {
      ifmerged[pid, dev, exec, uid] += @count(ifxmit[pid,dev,exec,uid]);
  }
  foreach ([pid, dev, exec, uid] in ifmerged-) {
    n_xmit = @count(ifxmit[pid, dev, exec, uid])
    n_recv = @count(ifrecv[pid, dev, exec, uid])
    printf("%5d %5d %-7s %7d %7d %7d %7d %-15s\n",
           pid, uid, dev, n_xmit, n_recv,
           n_xmit ? @sum(ifxmit[pid, dev, exec, uid])/1024 : 0,
           n_recv ? @sum(ifrecv[pid, dev, exec, uid])/1024 : 0,
           exec)
  }

  print("\n")

  delete ifxmit
  delete ifrecv
  delete ifmerged
}

probe timer.ms(5000), end, error
{
  print_activity()
} 
```

注意看`print_activity()`的这几个表达式：

```
n_xmit ? @sum(ifxmit[pid, dev, exec, uid])/1024 : 0
n_recv ? @sum(ifrecv[pid, dev, exec, uid])/1024 : 0 
```

它们也是`if/else`语句，等价于如下的伪代码：

```
if n_recv != 0 then
  @sum(ifrecv[pid, dev, exec, uid])/1024
else
  0 
```

`nettop.stp`跟踪用了网络流量的进程，并逐个进程输出如下的信息：

*   PID — 进程的 PID.
*   UID — 进程所有者的 UID。
*   DEV — 进程使用的端口，如`eth0`、`eth1`。
*   XMIT_PK — 发送的包的数量
*   RECV_PK — 接收的包的数量
*   XMIT_KB — 发送的 KB 数
*   RECV_KB — 接收的 KB 数

`nettop.stp`每隔 5 秒就会取样一次。你可以修改`probe timer.ms(5000)`来调整取样间隔。`nettop.stp`在 20 秒内的输出如下：

```
[...]
  PID   UID DEV     XMIT_PK RECV_PK XMIT_KB RECV_KB COMMAND
    0     0 eth0          0       5       0       0 swapper
11178     0 eth0          2       0       0       0 synergyc

  PID   UID DEV     XMIT_PK RECV_PK XMIT_KB RECV_KB COMMAND
 2886     4 eth0         79       0       5       0 cups-polld
11362     0 eth0          0      61       0       5 firefox
    0     0 eth0          3      32       0       3 swapper
 2886     4 lo            4       4       0       0 cups-polld
11178     0 eth0          3       0       0       0 synergyc

  PID   UID DEV     XMIT_PK RECV_PK XMIT_KB RECV_KB COMMAND
    0     0 eth0          0       6       0       0 swapper
 2886     4 lo            2       2       0       0 cups-polld
11178     0 eth0          3       0       0       0 synergyc
 3611     0 eth0          0       1       0       0 Xorg

  PID   UID DEV     XMIT_PK RECV_PK XMIT_KB RECV_KB COMMAND
    0     0 eth0          3      42       0       2 swapper
11178     0 eth0         43       1       3       0 synergyc
11362     0 eth0          0       7       0       0 firefox
 3897     0 eth0          0       1       0       0 multiload-apple
[...] 
```

## 跟踪网络连接中的内核函数调用

本节展示如何跟踪内核的`net/socket.c`中的函数的调用情况。这将帮助你从细节上看清各进程是怎么跟内核的网络功能打交道的。

socket-trace.stp

```
#! /usr/bin/env stap

probe kernel.function("*@net/socket.c").call {
  printf ("%s -> %s\n", thread_indent(1), ppfunc())
}

probe kernel.function("*@net/socket.c").return {
  printf ("%s <- %s\n", thread_indent(-1), ppfunc())
} 
```

`socket-trace.stp`这个脚本其实在我们之前在第三章介绍`thread_indent()`的时候已经见过了。下面是它在 3 秒内的输出：

```
[...]
0 Xorg(3611): -> sock_poll
3 Xorg(3611): <- sock_poll
0 Xorg(3611): -> sock_poll
3 Xorg(3611): <- sock_poll
0 gnome-terminal(11106): -> sock_poll
5 gnome-terminal(11106): <- sock_poll
0 scim-bridge(3883): -> sock_poll
3 scim-bridge(3883): <- sock_poll
0 scim-bridge(3883): -> sys_socketcall
4 scim-bridge(3883):  -> sys_recv
8 scim-bridge(3883):   -> sys_recvfrom
12 scim-bridge(3883):-> sock_from_file
16 scim-bridge(3883):<- sock_from_file
20 scim-bridge(3883):-> sock_recvmsg
24 scim-bridge(3883):<- sock_recvmsg
28 scim-bridge(3883):   <- sys_recvfrom
31 scim-bridge(3883):  <- sys_recv
35 scim-bridge(3883): <- sys_socketcall
[...] 
```

## 监控 TCP 连接的创建

本节展示如何监控 TCP 连接的创建。这可以帮助你第一时间识别出任何未授权的、可疑的或其它不请自来的网络连接。

tcp_connections.stp

```
#! /usr/bin/env stap

probe begin {
  printf("%6s %16s %6s %6s %16s\n",
         "UID", "CMD", "PID", "PORT", "IP_SOURCE")
}

probe kernel.function("tcp_accept").return?,
      kernel.function("inet_csk_accept").return? {
  sock = $return
  if (sock != 0)
    printf("%6d %16s %6d %6d %16s\n", uid(), execname(), pid(),
           inet_get_local_port(sock), inet_get_ip_source(sock))
} 
```

当`tcp_connections.stp`运行时，它会实时输出新创建的 TCP 连接的如下信息：

*   当前 UID
*   接受连接的程序名
*   接受连接的进程 PID
*   创建连接的远程 IP 地址

```
UID            CMD    PID   PORT        IP_SOURCE
0             sshd   3165     22      10.64.0.227
0             sshd   3165     22      10.64.0.227 
```

## 监控 TCP 包

本节展示如何监控收到的 TCP 包。这可以帮助你分析应用的流量使用情况。

tcpdumplike.stp

```
#! /usr/bin/env stap

// A TCP dump like example

probe begin, timer.s(1) {
  printf("-----------------------------------------------------------------\n")
  printf("       Source IP         Dest IP  SPort  DPort  U  A  P  R  S  F \n")
  printf("-----------------------------------------------------------------\n")
}

probe udp.recvmsg /* ,udp.sendmsg */ {
  printf(" %15s %15s  %5d  %5d  UDP\n",
         saddr, daddr, sport, dport)
}

probe tcp.receive {
  printf(" %15s %15s  %5d  %5d  %d  %d  %d  %d  %d  %d\n",
         saddr, daddr, sport, dport, urg, ack, psh, rst, syn, fin)
} 
```

当`tcpdumplike.stp`运行时，它会实时输出收到的 TCP 包的如下信息：

*   源 IP 地址和目标 IP 地址（saddr 和 daddr）
*   源端口和目标端口（sport 和 dport）
*   包标识

`tcpdumplike.stp`使用了以下函数来获取包的标识信息：

*   urg - urgent
*   ack - acknowledgement
*   psh - push
*   rst - reset
*   syn - synchronize
*   fin - finished

上述函数返回 1 或 0 来表示包中是否存在对应的标识。 ⁠

```
-----------------------------------------------------------------
       Source IP         Dest IP  SPort  DPort  U  A  P  R  S  F
-----------------------------------------------------------------
  209.85.229.147       10.0.2.15     80  20373  0  1  1  0  0  0
  92.122.126.240       10.0.2.15     80  53214  0  1  0  0  1  0
  92.122.126.240       10.0.2.15     80  53214  0  1  0  0  0  0
  209.85.229.118       10.0.2.15     80  63433  0  1  0  0  1  0
  209.85.229.118       10.0.2.15     80  63433  0  1  0  0  0  0
  209.85.229.147       10.0.2.15     80  21141  0  1  1  0  0  0
  209.85.229.147       10.0.2.15     80  21141  0  1  1  0  0  0
  209.85.229.147       10.0.2.15     80  21141  0  1  1  0  0  0
  209.85.229.147       10.0.2.15     80  21141  0  1  1  0  0  0
  209.85.229.147       10.0.2.15     80  21141  0  1  1  0  0  0
  209.85.229.118       10.0.2.15     80  63433  0  1  1  0  0  0
[...] 
```

## 监控内核中的网络丢包情况

某些情况下 Linux 网络栈会丢包。有些版本的 Linux 内核包含静态内核探测点`kernel.trace("kfree_skb")`，它可以帮助你跟踪包丢掉的原因。`dropwatch.stp`就使用了它来跟踪丢包；这个脚本每五秒统计一次丢包的位置。

dropwatch.stp

```
#! /usr/bin/env stap

############################################################
# Dropwatch.stp
# Author: Neil Horman <nhorman@redhat.com>
# An example script to mimic the behavior of the dropwatch utility
# http://fedorahosted.org/dropwatch
############################################################

# Array to hold the list of drop points we find
global locations

# Note when we turn the monitor on and off
probe begin { printf("Monitoring for dropped packets\n") }
probe end { printf("Stopping dropped packet monitor\n") }

# increment a drop counter for every location we drop at
probe kernel.trace("kfree_skb") { locations[$location] <<< 1 }

# Every 5 seconds report our drop locations
probe timer.sec(5)
{
  printf("\n")
  foreach (l in locations-) {
    printf("%d packets dropped at %s\n",
           @count(locations[l]), symname(l))
  }
  delete locations
} 
```

`kernel.trace("kfree_skb")`跟踪内核中网络包被丢弃的位置。它有两个参数：一个指向将被释放的缓冲区的指针`$skb`，和释放缓冲区时的内核位置`$location`。如果可以获取`$location`所存储的内核地址上对应的函数名，`dropwatch.stp`脚本可以把它的值映射成对应的函数。这个映射默认不会启用。对于 1.4 及以上的 SystemTap，你可以指定`--all-modules`选项来启用该映射：

```
stap --all-modules dropwatch.stp 
```

在低版本的 SystemTap，你可以使用下面的命令模拟`--all-modules`选项：

```
stap -dkernel \
`cat /proc/modules | awk 'BEGIN { ORS = " " } {print "-d"$1}'` \
dropwatch.stp 
```

运行`dropwatch.stp`15 秒会输出类似下面的结果。输出的结果会按函数名或地址聚合丢包的次数。

```
Monitoring for dropped packets

1762 packets dropped at unix_stream_recvmsg
4 packets dropped at tun_do_read
2 packets dropped at nf_hook_slow

467 packets dropped at unix_stream_recvmsg
20 packets dropped at nf_hook_slow
6 packets dropped at tun_do_read

446 packets dropped at unix_stream_recvmsg
4 packets dropped at tun_do_read
4 packets dropped at nf_hook_slow
Stopping dropped packet monitor 
```

当运行脚本的机器不支持`--all-modules`和`/proc/modules`时，`symname`只会输出原始的地址。你可以通过`/boot/System.map-$(uname -r)`按地址找出对应的函数。下面的`/boot/System.map-$(uname -r)`片段中，地址`0xffffffff8149a8ed`映射到函数`unix_stream_recvmsg`：

```
[...]
ffffffff8149a420 t unix_dgram_poll
ffffffff8149a5e0 t unix_stream_recvmsg
ffffffff8149ad00 t unix_find_other
[...] 
```

# 磁盘

# 5.2\. 磁盘

以下各节的脚本展示了如何监控磁盘和 I/O 活动。

## 统计磁盘读写状况

本节展示了如何找出磁盘读写最频繁的进程。

disktop.stp

```
#!/usr/bin/env stap
#
# Copyright (C) 2007 Oracle Corp.
#
# Get the status of reading/writing disk every 5 seconds,
# output top ten entries
#
# This is free software,GNU General Public License (GPL);
# either version 2, or (at your option) any later version.
#
# Usage:
#  ./disktop.stp
#

global io_stat,device
global read_bytes,write_bytes

probe vfs.read.return {
  if ($return>0) {
    if (devname!="N/A") {/*skip read from cache*/
      io_stat[pid(),execname(),uid(),ppid(),"R"] += $return
      device[pid(),execname(),uid(),ppid(),"R"] = devname
      read_bytes += $return
    }
  }
}

probe vfs.write.return {
  if ($return>0) {
    if (devname!="N/A") { /*skip update cache*/
      io_stat[pid(),execname(),uid(),ppid(),"W"] += $return
      device[pid(),execname(),uid(),ppid(),"W"] = devname
      write_bytes += $return
    }
  }
}

probe timer.ms(5000) {
  /* skip non-read/write disk */
  if (read_bytes+write_bytes) {

    printf("\n%-25s, %-8s%4dKb/sec, %-7s%6dKb, %-7s%6dKb\n\n",
           ctime(gettimeofday_s()),
           "Average:", ((read_bytes+write_bytes)/1024)/5,
           "Read:",read_bytes/1024,
           "Write:",write_bytes/1024)

    /* print header */
    printf("%8s %8s %8s %25s %8s %4s %12s\n",
           "UID","PID","PPID","CMD","DEVICE","T","BYTES")
  }
  /* print top ten I/O */
  foreach ([process,cmd,userid,parent,action] in io_stat- limit 10)
    printf("%8d %8d %8d %25s %8s %4s %12d\n",
           userid,process,parent,cmd,
           device[process,cmd,userid,parent,action],
           action,io_stat[process,cmd,userid,parent,action])

  /* clear data */
  delete io_stat
  delete device
  read_bytes = 0
  write_bytes = 0
}

probe end{
  delete io_stat
  delete device
  delete read_bytes
  delete write_bytes
} 
```

`disktop.stp`输出磁盘读写最频繁的十个进程，包含各个进程的以下数据：

*   UID - 进程所有者的 UID
*   PID - 进程的 PID
*   PPID - 进程的父进程的 PID
*   CMD - 进程的名字
*   DEVICE - 读/写的设备名
*   T - 进程的操作；`W`是写，而`R`是读。
*   BYTES - 读/写的数据量

`disktop.stp`使用`ctime()`和`gettimeofday_s()`输出当前时间。`gettimeofday_s`返回当前时间自 epoch（1970 年 1 月 1 日）以来的秒数，`ctime`把它转化成可读的时间戳。 在这个脚本中，`$return`是一个存储着虚拟文件系统读写的字节数的本地变量。`$return`只能在函数返回事件探针中使用（比如这里的`vfs.read.return`和`vfs.write.return`）。

以下是本节脚本的输出：

```
[...]
Mon Sep 29 03:38:28 2008 , Average:  19Kb/sec, Read: 7Kb, Write: 89Kb

UID      PID     PPID                       CMD   DEVICE    T    BYTES
0    26319    26294                   firefox     sda5    W        90229
0     2758     2757           pam_timestamp_c     sda5    R         8064
0     2885        1                     cupsd     sda5    W         1678

Mon Sep 29 03:38:38 2008 , Average:   1Kb/sec, Read: 7Kb, Write: 1Kb

UID      PID     PPID                       CMD   DEVICE    T    BYTES
0     2758     2757           pam_timestamp_c     sda5    R         8064
0     2885        1                     cupsd     sda5    W         1678 
```

## 追踪对任意文件的读写

本节展示如何监控各进程读/写任意文件所花费的时间。这可以帮助你发现系统中加载时间过长的文件。

iotime.stp

```
#! /usr/bin/env stap

/*
 * Copyright (C) 2006-2007 Red Hat Inc.
 *
 * This copyrighted material is made available to anyone wishing to use,
 * modify, copy, or redistribute it subject to the terms and conditions
 * of the GNU General Public License v.2.
 *
 * You should have received a copy of the GNU General Public License
 * along with this program.  If not, see <http://www.gnu.org/licenses/>.
 *
 * Print out the amount of time spent in the read and write systemcall
 * when each file opened by the process is closed. Note that the systemtap
 * script needs to be running before the open operations occur for
 * the script to record data.
 *
 * This script could be used to to find out which files are slow to load
 * on a machine. e.g.
 *
 * stap iotime.stp -c 'firefox'
 *
 * Output format is:
 * timestamp pid (executabable) info_type path ...
 *
 * 200283135 2573 (cupsd) access /etc/printcap read: 0 write: 7063
 * 200283143 2573 (cupsd) iotime /etc/printcap time: 69
 *
 */

global start
global time_io

function timestamp:long() { return gettimeofday_us() - start }

function proc:string() { return sprintf("%d (%s)", pid(), execname()) }

probe begin { start = gettimeofday_us() }

global filehandles, fileread, filewrite

probe syscall.open.return {
  filename = user_string($filename)
  if ($return != -1) {
    filehandles[pid(), $return] = filename
  } else {
    printf("%d %s access %s fail\n", timestamp(), proc(), filename)
  }
}

probe syscall.read.return {
  p = pid()
  fd = $fd
  bytes = $return
  time = gettimeofday_us() - @entry(gettimeofday_us())
  if (bytes > 0)
    fileread[p, fd] += bytes
  time_io[p, fd] <<< time
}

probe syscall.write.return {
  p = pid()
  fd = $fd
  bytes = $return
  time = gettimeofday_us() - @entry(gettimeofday_us())
  if (bytes > 0)
    filewrite[p, fd] += bytes
  time_io[p, fd] <<< time
}

probe syscall.close {
  if ([pid(), $fd] in filehandles) {
    printf("%d %s access %s read: %d write: %d\n",
           timestamp(), proc(), filehandles[pid(), $fd],
           fileread[pid(), $fd], filewrite[pid(), $fd])
    if (@count(time_io[pid(), $fd]))
      printf("%d %s iotime %s time: %d\n",  timestamp(), proc(),
             filehandles[pid(), $fd], @sum(time_io[pid(), $fd]))
   }
  delete fileread[pid(), $fd]
  delete filewrite[pid(), $fd]
  delete filehandles[pid(), $fd]
  delete time_io[pid(),$fd]
} 
```

`iotime.stp`跟踪每次`open`、`close`、`read`和`write`系统调用。对于访问到的每个文件，`iotime.stp`都会计算读写操作花费的时间和读写的数据量（以字节为单位）。 虽然我们可以在读写事件（`syscall.read`和`syscall.write`）中使用本地变量`$count`，但是`$count`存储的是系统调用想要读写的数据量，要获取实际读写到的数据量需要使用`$return`。

```
[...]
825946 3364 (NetworkManager) access /sys/class/net/eth0/carrier read: 8190 write: 0
825955 3364 (NetworkManager) iotime /sys/class/net/eth0/carrier time: 9
[...]
117061 2460 (pcscd) access /dev/bus/usb/003/001 read: 43 write: 0
117065 2460 (pcscd) iotime /dev/bus/usb/003/001 time: 7
[...]
3973737 2886 (sendmail) access /proc/loadavg read: 4096 write: 0
3973744 2886 (sendmail) iotime /proc/loadavg time: 11
[...] 
```

本节的脚本会输出以下数据：

*   时间戳，精确到毫秒
*   PID 和进程名
*   access 或 iotime
*   访问的文件

如果一个进程读写了数据，你会看到`access`和`iotime`成对出现。`access`那一行的时间戳表示进程访问了文件；在结尾处会输出读写的数据（以字节为单位）。`iotime`那一行会输出读写消耗的时间（以毫秒为单位）。如果一行`access`后面没有`iotime`，意味着进程没有读写到数据。

## 追踪 I/O 的累计总量

本节展示如何累计 I/O 总量。

traceio.stp

```
#! /usr/bin/env stap
# traceio.stp
# Copyright (C) 2007 Red Hat, Inc., Eugene Teo <eteo@redhat.com>
# Copyright (C) 2009 Kai Meyer <kai@unixlords.com>
#   Fixed a bug that allows this to run longer
#   And added the humanreadable function
#
# This program is free software; you can redistribute it and/or modify
# it under the terms of the GNU General Public License version 2 as
# published by the Free Software Foundation.
#

global reads, writes, total_io

probe vfs.read.return {
  if ($return > 0) {
    reads[pid(),execname()] += $return
    total_io[pid(),execname()] += $return
  }
}

probe vfs.write.return {
  if ($return > 0) {
    writes[pid(),execname()] += $return
    total_io[pid(),execname()] += $return
  }
}

function humanreadable(bytes) {
  if (bytes > 1024*1024*1024) {
    return sprintf("%d GiB", bytes/1024/1024/1024)
  } else if (bytes > 1024*1024) {
    return sprintf("%d MiB", bytes/1024/1024)
  } else if (bytes > 1024) {
    return sprintf("%d KiB", bytes/1024)
  } else {
    return sprintf("%d   B", bytes)
  }
}

probe timer.s(1) {
  foreach([p,e] in total_io- limit 10)
    printf("%8d %15s r: %12s w: %12s\n",
           p, e, humanreadable(reads[p,e]),
           humanreadable(writes[p,e]))
  printf("\n")
  # Note we don't zero out reads, writes and total_io,
  # so the values are cumulative since the script started.
} 
```

`traceio.stp`逐秒输出累计 I/O 最频繁的前十个进程。此外，它还会累计每个进程的 I/O 情况。注意该脚本跟开头找出磁盘读写最频繁的进程的脚本一样，也通过本地变量`$return`获取实际的读写数据量

```
[...]
           Xorg r:   583401 KiB w:        0 KiB
       floaters r:       96 KiB w:     7130 KiB
multiload-apple r:      538 KiB w:      537 KiB
           sshd r:       71 KiB w:       72 KiB
pam_timestamp_c r:      138 KiB w:        0 KiB
        staprun r:       51 KiB w:       51 KiB
          snmpd r:       46 KiB w:        0 KiB
          pcscd r:       28 KiB w:        0 KiB
     irqbalance r:       27 KiB w:        4 KiB
          cupsd r:        4 KiB w:       18 KiB

           Xorg r:   588140 KiB w:        0 KiB
       floaters r:       97 KiB w:     7143 KiB
multiload-apple r:      543 KiB w:      542 KiB
           sshd r:       72 KiB w:       72 KiB
pam_timestamp_c r:      138 KiB w:        0 KiB
        staprun r:       51 KiB w:       51 KiB
          snmpd r:       46 KiB w:        0 KiB
          pcscd r:       28 KiB w:        0 KiB
     irqbalance r:       27 KiB w:        4 KiB
          cupsd r:        4 KiB w:       18 KiB 
```

## 监控指定设备的 I/O

本节展示如何监控指定设备的 I/O 活动。

traceio2.stp

```
#! /usr/bin/env stap

global device_of_interest

probe begin {
  /* The following is not the most efficient way to do this.
      One could directly put the result of usrdev2kerndev()
      into device_of_interest.  However, want to test out
      the other device functions */
  dev = usrdev2kerndev($1)
  device_of_interest = MKDEV(MAJOR(dev), MINOR(dev))
}

probe vfs.write, vfs.read
{
  if (dev == device_of_interest)
    printf ("%s(%d) %s 0x%x\n",
            execname(), pid(), ppfunc(), dev)
} 
```

`traceio2.stp`接受一个参数：设备号，要想获取名为`directory`的文件夹所在设备的设备号，使用`stat -c "0x%D" directory`。 `usrdev2kerndev()`把设备号转化成内核理解的格式。`usrdev2kerndev()`的输出经过`MAJOR()`和`MINOR()`处理，分别得到主设备号和次设备号，再经过`MKDEV()`处理，得到内核里对应的设备号。 `traceio2.stp`的输出包括了读/写进程的名字和 PID，所调用的函数（`vfs_read`或`vfs_write`）和内核里对应的设备号。

下面是`stap traceio2.stp 0x805`的输出，其中`0x805`是`/home`的设备号。`/home`位于`/dev/sda5`，正是我们想要监控的设备。

```
[...]
synergyc(3722) vfs_read 0x800005
synergyc(3722) vfs_read 0x800005
cupsd(2889) vfs_write 0x800005
cupsd(2889) vfs_write 0x800005
cupsd(2889) vfs_write 0x800005
[...] 
```

## 监控对指定文件的读写

本节展示如何实时监控对指定文件的读写。

inodewatch.stp

```
#! /usr/bin/env stap

probe vfs.write, vfs.read
{
  # dev and ino are defined by vfs.write and vfs.read
  if (dev == MKDEV($1,$2) # major/minor device
      && ino == $3)
    printf ("%s(%d) %s 0x%x/%u\n",
      execname(), pid(), ppfunc(), dev, ino)
} 
```

`inodewatch.stp`从命令行中依次获取如下参数：

1.  文件的主设备号
2.  文件的次设备号
3.  文件的 inode 号

要获取上述信息，使用`stat -c '%D %i' filename`，注意`filename`取绝对路径。 比如：要监控`/etc/crontab`，先运行`stat -c '%D %i' /etc/crontab`。应该会有如下输出：

```
805 1078319 
```

805 是十六进制的设备号。最小的两位是次设备号，其余是主设备号。1078319 是 inode 号。要监控`/etc/crontab`，运行`stap inodewatch.stp 0x8 0x05 1078319`.（加`0x`以表示这是十六进制的数）

该命令的输出包括进程名和进程 PID，以及调用的函数（`vfs_read`或`vfs_write`），设备号（以十六进制的格式输出）和 inode 号。下面就是`stap inodewatch.stp 0x8 0x05 1078319`的输出（当脚本运行时，`/etc/crontab`也正在执行中）：

```
cat(16437) vfs_read 0x800005/1078319
cat(16437) vfs_read 0x800005/1078319 
```

## 监控对指定文件的属性的修改

本节展示如何实时监控对指定文件的属性的修改。

inodewatch2.stp

```
#! /usr/bin/env stap

global ATTR_MODE = 1

probe kernel.function("setattr_copy")!,
      kernel.function("generic_setattr")!,
      kernel.function("inode_setattr") {
  dev_nr = $inode->i_sb->s_dev
  inode_nr = $inode->i_ino

  if (dev_nr == MKDEV($1,$2) # major/minor device
      && inode_nr == $3
      && $attr->ia_valid & ATTR_MODE)
    printf ("%s(%d) %s 0x%x/%u %o %d\n",
      execname(), pid(), ppfunc(), dev_nr, inode_nr, $attr->ia_mode, uid())
} 
```

跟上一节的脚本类似，`inodewatch2.stp`也需要提供目标文件的设备号和 inode 号作为参数。用上一节的方法可以获取这些数据。 `inodewatch.stp`的输出也类似于上一节的脚本，不过`inodewatch.stp`还包括文件属性的变化，和对应用户的 UID。下面就是监控`/home/joe/bigfile`时，用户 job 执行`chmod 777 /home/joe/bigfile`和`chmod 666 /home/joe/bigfile`后的输出：

```
chmod(17448) inode_setattr 0x800005/6011835 100777 500
chmod(17449) inode_setattr 0x800005/6011835 100666 500 
```

## 定期输出块 I/O 等待时间

本节展示如何跟踪每个块 I/O 的等待时间。这可以帮助你发现给定时间内块 I/O 操作是否排起了长队。

ioblktime.stp

```
#! /usr/bin/env stap

global req_time%[25000], etimes

probe ioblock.request
{
  req_time[$bio] = gettimeofday_us()
}

probe ioblock.end
{
  t = gettimeofday_us()
  s =  req_time[$bio]
  delete req_time[$bio]
  if (s) {
    etimes[devname, bio_rw_str(rw)] <<< t - s
  }
}

/* for time being delete things that get merged with others */
probe kernel.trace("block_bio_frontmerge"),
      kernel.trace("block_bio_backmerge")
{
  delete req_time[$bio]
}

probe timer.s(10), end {
  ansi_clear_screen()
  printf("%10s %3s %10s %10s %10s\n",
         "device", "rw", "total (us)", "count", "avg (us)")
  foreach ([dev,rw] in etimes - limit 20) {
    printf("%10s %3s %10d %10d %10d\n", dev, rw,
           @sum(etimes[dev,rw]), @count(etimes[dev,rw]), @avg(etimes[dev,rw]))
  }
  delete etimes
} 
```

`ioblktime.stp`计算每个设备上块 I/O 平均等待时间，每 10 秒更新一次。你可以修改`probe timer.s(10), end {`来更改刷新频率。 有时候，在设备上的块 I/O 操作实在太多，以致于超过了默认的`MAXMAPENTRIES`值。如果你在定义数组时没有指定大小，SystemTap 会以`MAXMAPENTRIES`作为数组的最大长度。它的默认值是 2048,不过你可以使用 stap 命令的选项`-DMAXMAPENTRIES=10000`来指定该变量的值。

```
 device  rw total (us)      count   avg (us)
       sda   W       9659          6       1609
      dm-0   W      20278          6       3379
      dm-0   R      20524          5       4104
       sda   R      19277          5       3855 
```

上面的输出展示了设备名，操作类型（`rw`），总等待时间（`total(us)`），操作数（`count`），和平均等待时间（`avg(us)`）。这里面的时间都是以毫秒为单位。

# 剖析

# 5.3\. 剖析

以下各节的脚本展示了如何通过监控函数调用来剖析（profile）内核活动。

## 统计函数调用次数

本节展示如何统计 30 秒内某个内核函数调用次数。通过使用通配符，你可以用这个脚本同时统计多个内核函数。

functioncallcount.stp

```
#! /usr/bin/env stap
# The following line command will probe all the functions
# in kernel's memory management code:
#
# stap  functioncallcount.stp "*@mm/*.c"

probe kernel.function(@1).call {  # probe functions listed on commandline
  called[ppfunc()] <<< 1  # add a count efficiently
}

global called

probe end {
  foreach (fn in called-)  # Sort by call count (in decreasing order)
  #       (fn+ in called)  # Sort by function name
    printf("%s %d\n", fn, @count(called[fn]))
  exit()
} 
```

`functioncallcount.stp`接受内核函数名作为参数。你可以使用通配符，这样就能同时监控多个内核函数。 它的输出包括调用者的名字和取样时间内调用次数。下面是`stap functioncallcount.stp "*@mm/*.c"`的输出片段：

```
[...]
__vma_link 97
__vma_link_file 66
__vma_link_list 97
__vma_link_rb 97
__xchg 103
add_page_to_active_list 102
add_page_to_inactive_list 19
add_to_page_cache 19
add_to_page_cache_lru 7
all_vm_events 6
alloc_pages_node 4630
alloc_slabmgmt 67
anon_vma_alloc 62
anon_vma_free 62
anon_vma_lock 66
anon_vma_prepare 98
anon_vma_unlink 97
anon_vma_unlock 66
arch_get_unmapped_area_topdown 94
arch_get_unmapped_exec_area 3
arch_unmap_area_topdown 97
atomic_add 2
atomic_add_negative 97
atomic_dec_and_test 5153
atomic_inc 470
atomic_inc_and_test 1
[...] 
```

## 追踪函数调用链

本节展示如何追踪函数调用链。

para-callgraph.stp

```
#! /usr/bin/env stap

function trace(entry_p, extra) {
  %( $# > 1 %? if (tid() in trace) %)
  printf("%s%s%s %s\n",
         thread_indent (entry_p),
         (entry_p>0?"->":"<-"),
         ppfunc (),
         extra)
}

%( $# > 1 %?
global trace
probe $2.call {
  trace[tid()] = 1
}
probe $2.return {
  delete trace[tid()]
}
%)

probe $1.call   { trace(1, $$parms) }
probe $1.return { trace(-1, $$return) } 
```

`para-callgraph.stp`接受两个命令行参数：

1.  想要跟踪的函数（`$1`）
2.  可选的触发函数。该函数可以在线程范围内启动/停止追踪。只要触发函数不退出，追踪就不会结束。 `para-callgraph.stp`使用了`thread_indent()`；此外它的输出包括了时间戳、进程名，和`$1`所在的线程 ID。关于`thread_indent()`的更多信息，请参考。。。。 （译注：这个脚本的编码风格小朋友们可不要学。前两个探针里的`trace`是数组，后两个探针里的`trace`是函数。另外`$#`表示参数的个数，写过 shell 的都明白。`%( $# > 1 %? if (tid() in trace) %)`是一个预处理三元表达式，见[langref](https://sourceware.org/systemtap/langref/Language_elements.html)中的“5.8.1 Conditions”）

下面是`stap para-callgraph.stp 'kernel.function("*@fs/*.c")' 'kernel.function("sys_read")'`的输出片段：

```
[...]
   267 gnome-terminal(2921): <-do_sync_read return=0xfffffffffffffff5
   269 gnome-terminal(2921):<-vfs_read return=0xfffffffffffffff5
     0 gnome-terminal(2921):->fput file=0xffff880111eebbc0
     2 gnome-terminal(2921):<-fput
     0 gnome-terminal(2921):->fget_light fd=0x3 fput_needed=0xffff88010544df54
     3 gnome-terminal(2921):<-fget_light return=0xffff8801116ce980
     0 gnome-terminal(2921):->vfs_read file=0xffff8801116ce980 buf=0xc86504 count=0x1000 pos=0xffff88010544df48
     4 gnome-terminal(2921): ->rw_verify_area read_write=0x0 file=0xffff8801116ce980 ppos=0xffff88010544df48 count=0x1000
     7 gnome-terminal(2921): <-rw_verify_area return=0x1000
    12 gnome-terminal(2921): ->do_sync_read filp=0xffff8801116ce980 buf=0xc86504 len=0x1000 ppos=0xffff88010544df48
    15 gnome-terminal(2921): <-do_sync_read return=0xfffffffffffffff5
    18 gnome-terminal(2921):<-vfs_read return=0xfffffffffffffff5
     0 gnome-terminal(2921):->fput file=0xffff8801116ce980 
```

## 统计给定线程在内核空间和用户空间上的耗时

本节展示如何统计给定线程花费在内核空间或用户空间上的运行时间。

thread-times.stp

```
#! /usr/bin/env stap

probe perf.sw.cpu_clock!, timer.profile {
  // NB: To avoid contention on SMP machines, no global scalars/arrays used,
  // only contention-free statistics aggregates.
  tid=tid(); e=execname()
  if (!user_mode())
    kticks[e,tid] <<< 1
  else
    uticks[e,tid] <<< 1
  ticks <<< 1
  tids[e,tid] <<< 1
}

global uticks%, kticks%, ticks

global tids%

probe timer.s(5), end {
  allticks = @count(ticks)
  printf ("%16s %5s %7s %7s (of %d ticks)\n",
          "comm", "tid", "%user", "%kernel", allticks)
  foreach ([e,tid] in tids- limit 20) {
    uscaled = @count(uticks[e,tid])*10000/allticks
    kscaled = @count(kticks[e,tid])*10000/allticks
    printf ("%16s %5d %3d.%02d%% %3d.%02d%%\n",
      e, tid, uscaled/100, uscaled%100, kscaled/100, kscaled%100)
  }
  printf("\n")

  delete uticks
  delete kticks
  delete ticks
  delete tids
} 
```

`thread-time.stp`列出 5 秒内花费 CPU 时间最多的 20 个进程，和这段时间 CPU 滴答（ticks）的总数。脚本的输出还包括每个进程 CPU 占用百分比，分别按内核空间和用户空间列出。 下面就是它的输出:

```
 tid   %user %kernel (of 20002 ticks)
    0   0.00%  87.88%
32169   5.24%   0.03%
 9815   3.33%   0.36%
 9859   0.95%   0.00%
 3611   0.56%   0.12%
 9861   0.62%   0.01%
11106   0.37%   0.02%
32167   0.08%   0.08%
 3897   0.01%   0.08%
 3800   0.03%   0.00%
 2886   0.02%   0.00%
 3243   0.00%   0.01%
 3862   0.01%   0.00%
 3782   0.00%   0.00%
21767   0.00%   0.00%
 2522   0.00%   0.00%
 3883   0.00%   0.00%
 3775   0.00%   0.00%
 3943   0.00%   0.00%
 3873   0.00%   0.00% 
```

## 监控应用轮询情况

本节展示如何监控应用的轮询（polling）情况。这将允许你跟踪多余的或过度的轮询，帮助锁定 CPU 使用或能源消耗需要改善的地方。

timeout.stp

```
#! /usr/bin/env stap
# Copyright (C) 2009 Red Hat, Inc.
# Written by Ulrich Drepper <drepper@redhat.com>
# Modified by William Cohen <wcohen@redhat.com>

global process, timeout_count, to
global poll_timeout, epoll_timeout, select_timeout, itimer_timeout
global nanosleep_timeout, futex_timeout, signal_timeout

probe syscall.poll, syscall.epoll_wait {
  if (timeout) to[pid()]=timeout
}

probe syscall.poll.return {
  if ($return == 0 && to[pid()] > 0 ) {
    poll_timeout[pid()]++
    timeout_count[pid()]++
    process[pid()] = execname()
    delete to[pid()]
  }
}

probe syscall.epoll_wait.return {
  if ($return == 0 && to[pid()] > 0 ) {
    epoll_timeout[pid()]++
    timeout_count[pid()]++
    process[p] = execname()
    delete to[pid()]
  }
}

probe syscall.select.return {
  if ($return == 0) {
    select_timeout[pid()]++
    timeout_count[pid()]++
    process[pid()] = execname()
  }
}

probe syscall.futex.return {
  if (errno_str($return) == "ETIMEDOUT") {
    futex_timeout[pid()]++
    timeout_count[pid()]++
    process[pid()] = execname()
  }
}

probe syscall.nanosleep.return {
  if ($return == 0) {
    nanosleep_timeout[pid()]++
    timeout_count[pid()]++
    process[pid()] = execname()
  }
}

probe kernel.function("it_real_fn") {
  itimer_timeout[pid()]++
  timeout_count[pid()]++
  process[pid()] = execname()
}

probe syscall.rt_sigtimedwait.return {
  if (errno_str($return) == "EAGAIN") {
    signal_timeout[pid()]++
    timeout_count[pid()]++
    process[pid()] = execname()
  }
}

probe syscall.exit {
  if (pid() in process) {
    delete process[pid()]
    delete timeout_count[pid()]
    delete poll_timeout[pid()]
    delete epoll_timeout[pid()]
    delete select_timeout[pid()]
    delete itimer_timeout[pid()]
    delete futex_timeout[pid()]
    delete nanosleep_timeout[pid()]
    delete signal_timeout[pid()]
  }
}

probe timer.s(1) {
  ansi_clear_screen()
  printf ("  pid |   poll  select   epoll  itimer   futex nanosle  signal| process\n")
  foreach (p in timeout_count- limit 20) {
     printf ("%5d |%7d %7d %7d %7d %7d %7d %7d| %-.38s\n", p,
              poll_timeout[p], select_timeout[p],
              epoll_timeout[p], itimer_timeout[p],
              futex_timeout[p], nanosleep_timeout[p],
              signal_timeout[p], process[p])
  }
} 
```

`timeout.stp`跟踪下列系统调用，并仅当因为超时而退出该调用时记录次数：

*   poll
*   select
*   epoll
*   itimer
*   futex
*   nanosleep
*   signal

```
 uid |   poll  select   epoll  itimer   futex nanosle  signal| process
28937 | 148793       0       0    4727   37288       0       0| firefox
22945 |      0   56949       0       1       0       0       0| scim-bridge
    0 |      0       0       0   36414       0       0       0| swapper
 4275 |  23140       0       0       1       0       0       0| mixer_applet2
 4191 |      0   14405       0       0       0       0       0| scim-launcher
22941 |   7908       1       0      62       0       0       0| gnome-terminal
 4261 |      0       0       0       2       0    7622       0| escd
 3695 |      0       0       0       0       0    7622       0| gdm-binary
 3483 |      0    7206       0       0       0       0       0| dhcdbd
 4189 |   6916       0       0       2       0       0       0| scim-panel-gtk
 1863 |   5767       0       0       0       0       0       0| iscsid
 2562 |      0    2881       0       1       0    1438       0| pcscd
 4257 |   4255       0       0       1       0       0       0| gnome-power-man
 4278 |   3876       0       0      60       0       0       0| multiload-apple
 4083 |      0    1331       0    1728       0       0       0| Xorg
 3921 |   1603       0       0       0       0       0       0| gam_server
 4248 |   1591       0       0       0       0       0       0| nm-applet
 3165 |      0    1441       0       0       0       0       0| xterm
29548 |      0    1440       0       0       0       0       0| httpd
 1862 |      0       0       0       0       0    1438       0| iscsid 
```

你可以通过修改最后一个探针（`timer.s(1)`）来增大取样时间。`timeout.stp`的输出包括前 20 个轮询应用的名字和 UID，连带每个应用调用每种轮询系统调用的累计次数。在上面的输出片段中，由于某个插件模块，firefox 进行了过度的轮询。

## 监控最常调用的系统调用

上一节的`timeout.stp`通过监控系统调用的某个子集，帮助你找到过度轮询的应用。同样，如果你怀疑某些系统调用被过度地调用了，通过类似的监控，你也能把它们找出来。下面就通过`topsys.stp`实现这一点：

topsys.stp

```
#! /usr/bin/env stap
#
# This script continuously lists the top 20 systemcalls in the interval 
# 5 seconds
#

global syscalls_count

probe syscall.* {
  syscalls_count[name] <<< 1
}

function print_systop () {
  printf ("%25s %10s\n", "SYSCALL", "COUNT")
  foreach (syscall in syscalls_count- limit 20) {
    printf("%25s %10d\n", syscall, @count(syscalls_count[syscall]))
  }
  delete syscalls_count
}

probe timer.s(5) {
  print_systop ()
  printf("--------------------------------------------------------------\n")
} 
```

`topsys.stp`每 5 秒列出调用最多的 20 个系统调用。它也列出了这段时间内每个系统调用被调用的次数。下面是它的一个输出：

```
--------------------------------------------------------------
                  SYSCALL      COUNT
             gettimeofday       1857
                     read       1821
                    ioctl       1568
                     poll       1033
                    close        638
                     open        503
                   select        455
                    write        391
                   writev        335
                    futex        303
                  recvmsg        251
                   socket        137
            clock_gettime        124
           rt_sigprocmask        121
                   sendto        120
                setitimer        106
                     stat         90
                     time         81
                sigreturn         72
                    fstat         66
-------------------------------------------------------------- 
```

## 找出每个进程的系统调用量

本节展示如何找出调用系统调用最多的进程。在上一节，我们谈到了如何找出调用最多的系统调用。而在上上节，我们也谈到了如何找出轮询最多的进程。通过监控每个进程的调用系统调用的次数，可以在调查轮询进程和其他滥用资源者时提供更多的数据。

`syscalls_by_proc.stp`

```
#! /usr/bin/env stap

# Copyright (C) 2006 IBM Corp.
#
# This file is part of systemtap, and is free software.  You can
# redistribute it and/or modify it under the terms of the GNU General
# Public License (GPL); either version 2, or (at your option) any
# later version.

#
# Print the system call count by process name in descending order.
#

global syscalls

probe begin {
  print ("Collecting data... Type Ctrl-C to exit and display results\n")
}

probe nd_syscall.* {
  syscalls[execname()]++
}

probe end {
  printf ("%-10s %-s\n", "#SysCalls", "Process Name")
  foreach (proc in syscalls-)
    printf("%-10d %-s\n", syscalls[proc], proc)
} 
```

`syscalls_by_proc.stp`列出调用系统调用最多的 20 个进程。它也列出了这段时间内每个进程调用系统调用的数量。下面是它的输出：

```
Collecting data... Type Ctrl-C to exit and display results
#SysCalls  Process Name
1577       multiload-apple
692        synergyc
408        pcscd
376        mixer_applet2
299        gnome-terminal
293        Xorg
206        scim-panel-gtk
95         gnome-power-man
90         artsd
85         dhcdbd
84         scim-bridge
78         gnome-screensav
66         scim-launcher
[...] 
```

要想输出进程 PID 而非进程名，改用下面的脚本：

syscalls_by_pid.stp

```
#! /usr/bin/env stap

# Copyright (C) 2006 IBM Corp.
#
# This file is part of systemtap, and is free software.  You can
# redistribute it and/or modify it under the terms of the GNU General
# Public License (GPL); either version 2, or (at your option) any
# later version.

#
# Print the system call count by process ID in descending order.
#

global syscalls

probe begin {
  print ("Collecting data... Type Ctrl-C to exit and display results\n")
}

probe nd_syscall.* {
  syscalls[pid()]++
}

probe end {
  printf ("%-10s %-s\n", "#SysCalls", "PID")
  foreach (pid in syscalls-)
    printf("%-10d %-d\n", syscalls[pid], pid)
} 
```

正如在输出中提到的，你需要手动 Ctrl-C 退出程序来显示结果。你可以简单地添加一个`timer.s()`探针，让脚本在给定时间后自动退出。举个例子，要想让脚本 5 秒后退出，往里面添加下面的探针：

```
probe timer.s(5)
{
    exit()
} 
```

# 标识用户空间锁竞争

# 5.4\. 标识用户空间锁竞争

本节展示如何显示特定时间内用户空间锁竞争的情况。通过展示锁竞争的图景，你可以判断当前的性能问题是否由对`futex`的竞争所造成的。 简单地说，如果在同一时间内多个进程试图获取同一把锁，就会产生对`futex`的竞争。由于仅有一个进程可以持有锁，其他的进程都只能等待锁重新可用，锁竞争会导致性能的下降。 下面的`futexes.stp`脚本通过探测`futex`系统调用来显示锁竞争的情况：

futexes.stp

```
#! /usr/bin/env stap

# This script tries to identify contended user-space locks by hooking
# into the futex system call.

global FUTEX_WAIT = 0 /*, FUTEX_WAKE = 1 */
global FUTEX_PRIVATE_FLAG = 128 /* linux 2.6.22+ */
global FUTEX_CLOCK_REALTIME = 256 /* linux 2.6.29+ */

global lock_waits # long-lived stats on (tid,lock) blockage elapsed time
global process_names # long-lived pid-to-execname mapping

probe syscall.futex.return {  
  if (($op & ~(FUTEX_PRIVATE_FLAG|FUTEX_CLOCK_REALTIME)) != FUTEX_WAIT) next
  process_names[pid()] = execname()
  elapsed = gettimeofday_us() - @entry(gettimeofday_us())
  lock_waits[pid(), $uaddr] <<< elapsed
}

probe end {
  foreach ([pid+, lock] in lock_waits) 
    printf ("%s[%d] lock %p contended %d times, %d avg us\n",
            process_names[pid], pid, lock, @count(lock_waits[pid,lock]),
            @avg(lock_waits[pid,lock]))
} 
```

`futexes.stp`需要手动 Ctrl+C 退出。一旦退出后，它会输出下面信息：

*   参与锁竞争的进程的名字和 ID
*   被竞争的锁变量的地址
*   锁被竞争的次数
*   竞争锁的平均耗时

⁠下面是`futexes.stp`在运行约 20 秒 退出时，大致的输出情况：

```
[...]
automount[2825] lock 0x00bc7784 contended 18 times, 999931 avg us
synergyc[3686] lock 0x0861e96c contended 192 times, 101991 avg us
synergyc[3758] lock 0x08d98744 contended 192 times, 101990 avg us
synergyc[3938] lock 0x0982a8b4 contended 192 times, 101997 avg us
[...] 
```