# 第八章 - 系统任务管理

这里有关于磁盘管理的, 用户管理的, 远程登陆的, 最后还介绍了 Iptables...

可以理解为, 乱七八糟...

希望会对新手和老司机都有帮助 :)

~~翻译进展到一半了, 进度还是蛮快的, 其实现在想想, 当初的动机也不再那么纯洁了, 我也是想以后找工作的时候在简历上画一笔...毕竟都快毕不了业了...~~

也希望会对我有帮助 :P

# Hack-55 Fdisk 命令

## Fdisk 命令

讲真, 这种东西挺危险的, 如果你不知道你在做什么, 请不要随意使用这个工具, 如果出了差错, 那你近几天的搜索关键词将会变成"数据恢复 Linux", 这是一个悲伤的故事...

所以个人建议新手还是不要实际操作, 知道个大概意思就可以, 当然, 你完全可以新划分一块分区进行操作, 或者边看鸟哥的书边操作, 毕竟这里说的都很肤浅, 三思而后行, 特别是对重要数据进行操作.

#### 基本命令

`fdisk`的操作键:

*   `n` 新建一个分区
*   `d` 删除一个分钱
*   `p` 打印当前的分区表
*   `w` 把当前所有操作写入分区表, 也就是保存. (三思啊主公!)
*   `q` 退出

#### 新建一个分区

下面的例子新建了一个`/dev/sda1`分区:

```
# fdisk /dev/sda
Command (m for help): p
Disk /dev/sda: 287.0 GB, 287005343744 bytes
255 heads, 63 sectors/track, 34893 cylinders
Units = cylinders of 16065 * 512 = 8225280 bytes

Device   Boot   Start   End   Blocks   Id   System

Command (m for help): n
Command action
    e   extended
    p   primary partition (1-4)
p

Partition number (1-4): 1
First cylinder (1-34893, default 1):
Using default value 1
Last cylinder or +size or +sizeM or +sizeK (1-34893,
default 34893):
Using default value 34893

Command (m for help): w
The partition table has been altered!

Calling ioctl() to re-read partition table.
Syncing disks. 
```

不得不说, 这个作者越来越水了,这种分区的东西一年半载根本用不上一次, 介绍这么点东西还不如不说, 这里面水多深, 又是分区表格式, 又是磁盘格式, 这里面的区别, 道道, 一天半会儿根本学不来...

建议大家先不要触及这方面的东西, 要不就先看看操作系统, 了解一下磁盘分区, 了解一下储存方式以及 Linux 是怎么组织文件的, 要是什么都不懂, 还是离分区远远的吧, 数据弄丢了可真不是一件小事儿.(用虚拟机的就不要管我了...)

另外, 还有一些工具是用来恢复数据的, 不懂的千万要查文档, 否则你怎么死的都不知道,

# Hack-56 Mke2fsk 命令

## Mke2fsk 命令

光分区了还不算, 还要给分的区建立一种格式, 让磁盘有组织有纪律的存放文件.

`mke2fsk`就是这样一个格式化分区的工具.

回到之前创建的新分区(并不是让你回去创建分区啊! 很危险的! 看看就好了, 不要真把硬盘搞坏了), 查看一下分区的信息:

```
# tune2fs -l /dev/sda1
tune2fs 1.35 (28-Feb-2004)
tune2fs: Bad magic number in super-block while trying to open /dev/sda1
Couldn't find valid filesystem superblock. 
```

看吧, 找不到`superblock`, 话说这个`superblock`是个什么?

> A superblock is a record of the characteristics of a filesystem, including its size, the block size, the empty and the filled blocks and their respective counts, the size and location of the inode tables, the disk block map and usage information, and the size of the block groups.

可以简单地理解为一张大~~饼~~表, 上面记录了各种文件的信息, 系统的信息, 谁放在哪儿了, 哪里还有空位子, 等等等等...

然后我们开始格式化这个分区:

```
# mke2fs /dev/sda1 
```

说道格式化分区, `Gparted`实在是一个不可多得的好工具.

```
# mke2fs -m 0 -b 4096 /dev/sda1
mke2fs 1.35 (28-Feb-2004)
Filesystem label=
OS type: Linux
Block size=4096 (log=2)
Fragment size=4096 (log=2)
205344 inodes, 70069497 blocks
0 blocks (0.00%) reserved for the super user
First data block=0
Maximum filesystem blocks=71303168
2139 block groups
32768 blocks per group, 32768 fragments per group
96 inodes per group
Superblock backups stored on blocks:
32768, 98304, 163840, 229376, 294912, 819200, 884736,
1605632, 2654208, 4096000, 7962624, 11239424, 20480000,
23887872
Writing inode tables: done
Writing superblocks and filesystem accounting information:
done
This filesystem will be automatically checked every 32
mounts or 180 days, whichever comes first. Use tune2fs -c
or -i to override. 
```

这里来一点注释:

*   `-m 0` 设置保留块的百分比为 0, 默认是 5%. 保留的地方是给 root 的.
*   `-b 4096` 设置每一块多少比特, 可用的有 1024, 2048 和 4096.

上面的过程, 创建了一个`ext2`的分区.

你可以用下面的两条命令来创建`ext3`的分区:

```
# mkfs.ext3 /dev/sda1
# mke2fs –j /dev/sda1 
```

当然, 还是推荐使用`Gparted`, 图形化, 更直观.

# Hack-57 挂载一个分区

## 挂载一个分区

新建并格式化某个分区之后, 你需要把他挂载到主机上.(一般都是自动挂载的... 所以作者在这儿也是给 101 充数.)

1.挑个地儿:

新建一个文件夹, 或者选择一个已有的空文件夹都好:

```
mkdir /home/data 
```

2.把分区挂载到刚才的地方:

```
mount /dev/sda1 /home/data 
```

3.卸载:

```
umount /dev/sda1 
```

都是些很简单的命令.

如果想自动挂载这个分区, 可以写入到`/etc/fstab`中:

```
/dev/sda1 /home/database ext3 defaults 0 2 
```

# Hack-58 查看分区信息

## 查看分区信息

这里也只是一个命令, 就是输出的有点多.

`tune2fs`,这个在之前有用到过, 是用来查看分区信息的.

```
# tune2fs -l /dev/sda1
tune2fs 1.35 (28-Feb-2004)
Filesystem volume name: /home/database
Last mounted on: <not available>
Filesystem UUID: f1234556-e123-1234-abcd-bbbbaaaaae11
Filesystem magic number: 0xEF44
Filesystem revision #: 1 (dynamic)
Filesystem features:
sparse_super resize_inode filetype
Default mount options: (none)
Filesystem state: not clean
Errors behavior: Continue
Filesystem OS type: Linux
Inode count: 1094912
Block count: 140138994
Reserved block count: 0
Free blocks: 16848481
Free inodes: 1014969
First block: 0
Block size: 2048
Fragment size: 2048
Reserved GDT blocks: 512
Blocks per group: 16384
Fragments per group: 16384
Inodes per group: 128
Inode blocks per group: 8
Filesystem created: Tue Jul
Last mount time: Thu Aug 21 05:58:25 2008
Last write time: Fri Jan
Mount count: 2
Maximum mount count: 20
Last checked: Tue Jul
Check interval: 15552000 (6 months)
Next check after: Sat Dec 27 23:06:03 2008
Reserved blocks uid: 0 (user root)
Reserved blocks gid: 0 (group root)
First inode: 11
Inode size: 128
Default directory hash: tea
Directory Hash Seed: 12345829-1236-4123-9aaa-ccccc123292b 
```

完全复制粘贴的作者的磁盘信息. 感觉用处不是很大的样子... 知道有这么个东西就好.

# Hack-59 新建一个 swap 分区

## 新建一个 swap 分区

首先, 你知道什么是交换分区(swap)嘛?

不知道的话先去查查吧!

如果内存不是很紧张, 也不需要休眠的话, `Swap`分区在个人主机上存在的意义已经不是很大了.我自己是一块 120G 的 SSD, 所以硬盘很吃紧, 分出 8G 的大小给`Swap`太不划算了, 而且内存已经足够用了, 所以我就取消了`Swap`分区, 这样看起来会安心一些 :P

但是在`VPS`主机上, 交换分区还是很重要的, 因为私人买的主机, 一般内存都不会很大(因为贵啊!), 建立交换分区就会把系统暂时不用的数据存起来, 系统也就会有更多的内存来处理当前事务. 有的主机提供商会自动划分交换分区, 有的则不然(比如 DigitalOcean), 需要你自己建立.

首先新建一个够你用的文件:

```
# dd if=/dev/zero of=/home/swap-fs bs=1M count=512
512+0 records in
512+0 records out
# ls -l /home/swap-fs
-rw-r--r-- 1 root root 536870912 Jan 2 23:13 /home/swap-fs 
```

要不要科普一下`/dev/zero`呢? 你还是自己去查吧 :) -> [我的谷歌](https://gg.kfd.me)

哦对了,还有[我的维基](https://wikipedia.kfd.me)

然后把新建的文件转换为`swap`格式:

```
# mkswap /home/swap-fs 
```

挂载我们新建的`swap`分区:

```
swapon /home/swap-fs 
```

如果想长期使用, 那就写入到`/etc/fstab`:

```
/home/swap-fs swap swap defaults 0 0 
```

# Hack-60 新建用户

## 新建用户

#### 建立用户时只指定用户名:

```
# useradd jsmith 
```

然后别的都按照默认设置, 需要注意的是, 这样建立的用户没有默认密码的, 需要新给他设置密码.

#### 建立用户时附带信息:

```
# adduser -c "John Smith - Oracle Developer" -e 12/31/09 jsmith 
```

其中:

*   `-c` 描述用户
*   `-e` 用户过期时间(这样说有点扯, 大概就是到了这个时间, 这个用户就不能用了)

改用户的密码用:`passwd user_name`这么基础的就不必啰嗦了.

最后是查看默认添加的用户信息:

```
# useradd –D
GROUP=100
HOME=/home
INACTIVE=-1
EXPIRE=
SHELL=/bin/bash
SKEL=/etc/skel 
```

这里作者没有说太多, 不是江郎才尽, 而是这里真没什么好说的, 需要注意的一点是,`useradd`和`adduser`是不一样的. 具体哪里不一样? 你试试就知道了!

还有哦, `deluser`和`userdel`也不一样哦~ 这里倒是挺有趣的.

# Hack-61 新建用户组

## 新建用户组

新建一个用户组:

```
# groupadd developers 
```

把用户添加到用户组:

```
usermod -g developers jsmith 
```

(不忍再吐槽一句, 这也叫技巧? 明明是基本操作啊! 以后翻译东西一定要取其精华, 去其糟粕.)

大家整理东西的时候也是, 并不是别人写的都是好的, 有时候要选择性的抛弃一些东西, 选择性的吸收一些东西.

# Hack-62 SSH 密钥登录

## SSH 密钥登录

密码登录不安全, 万一被爆破了呢? 要假设黑客无所不能(除了破解 2048 位的密钥,哈哈哈)!

如果你没有密码学的相关知识也没关系, 我可以大体举个例子说明以下. 所谓公钥, 相当于一把锁, 那么私钥呢, 就是打开这把锁的钥匙了, 而且, 这把锁的钥匙只有一个, 这把钥匙也只能打开这一把锁. 我们可以理解为,钥匙的复杂度很高很高, 高到无法破解. 解密的时候呢, 公钥那边给出一个问题, 这个问题只有私钥能够解开, 拥有私钥的你在本地把题目解开之后, 把结果发送给服务器, 服务器那边一看, 哟, 答对了, 放行! 然后你就成功登陆了.

(过程大概就是这个意思, 其中涉及到密码学公钥系统的知识, 我学的不好, 只能理解个大概, 所以各位看官也就听个大概吧, 小弟哪里讲的不对, 欢迎指正!)

#### 第一步,创建私钥和公钥:

```
root@key:~# ssh-keygen 
Generating public/private rsa key pair.
Enter file in which to save the key (/root/.ssh/id_rsa): 
Created directory '/root/.ssh'.
Enter passphrase (empty for no passphrase): 
Enter same passphrase again: 
Your identification has been saved in /root/.ssh/id_rsa.
Your public key has been saved in /root/.ssh/id_rsa.pub.
The key fingerprint is:
91:27:cf:01:0c:64:90:dc:08:d0:f6:fa:be:a8:88:91 root@key
The key's randomart image is:
+--[ RSA 2048]----+
|.o.o.*+o.        |
|  o +.. .o       |
| . .    + o      |
|    .    * .     |
|   .    S o      |
| ..              |
|E  .             |
|o. ..            |
|+...o.           |
+-----------------+
root@key:~# 
```

就一个命令(`ssh-keygen`).

然后你就得到了你的公钥和私钥:

```
root@key:~# ls .ssh/
id_rsa  id_rsa.pub
root@key:~# 
```

其中公钥(`.pub`结尾的文件)可以随意分发, 但是私钥一定要保存好, 不能通过不安全的方式带到别处.(什么是不安全的方式? 看你的安全等级咯~)

#### 第二步, 把公钥拷贝到远程主机

```
jsmith@local-host$ ssh-copy-id -i ~/.ssh/id_rsa.pub remote-host
jsmith@remote-host’s password:
Now try logging into the machine, with "ssh 'remote-host'",
and check in: .ssh/authorized_keys to make sure we haven't 
added extra keys that you weren't expecting. 
```

这样就把公钥拷贝上去啦, 然后确认自己是否可以免密码登陆服务器, 如果可以, 就进入第三步, 如果不行呢, 那就再复制一遍.

#### 第三步, 关闭远程服务器的密码登陆

这一步可选, 但是我还是建议你这样做.

```
root@key:~# cat /etc/ssh/sshd_config | grep "PasswordAuthentication yes"
#PasswordAuthentication yes
root@key:~# 
```

把这里改成`no`,并取消注释, 重启 ssh 服务.

嗯... 如果你不想用`ssh-copy-id`的话, 直接把公钥复制到远程主机的`~/.ssh/authorized_keys`这个文件里也是可以的, 但是要保证一字不差哟~而且这种方法很方便, 如果你有多台主机, 就可以写一个脚本自己写入公钥, 改写端口, 以及禁止密码登录.

# Hack-63 ssh-copy-id 和 ssh- agent

## ssh-copy-id 和 ssh- agent

看了半天也不知道这是什么鬼技巧, 大概就是说了一个问题, 然后怎样解决这个问题.

翻译模式开启:

如果没有`-i`的参数或者`~/.ssh/identity.pub`不可用, `ssh-copy-id`就会报错:

```
jsmith@local-host$ ssh-copy-id -i remote-host
/usr/bin/ssh-copy-id: ERROR: No identities found 
```

如果你用`ssh-add`加载了公钥到`ssh-agent`的话, `ssh-copy-id`就会`ssh-agent`那里得到公钥并且拷贝到远程服务器.

```
jsmith@local-host$ ssh-agent $SHELL
jsmith@local-host$ ssh-add -L
The agent has no identities.
jsmith@local-host$ ssh-add
Identity added: /home/jsmith/.ssh/id_rsa
(/home/jsmith/.ssh/id_rsa)
jsmith@local-host$ ssh-add -L
ssh-rsa
AAAAB3NzaC1yc2EAAAABIwAAAQEAsJIEILxftj8aSxMa3d8t6JvM79D
aHrtPhTYpq7kIEMUNzApnyxsHpH1tQ/Ow==
/home/jsmith/.ssh/id_rsa
jsmith@local-host$ ssh-copy-id -i remote-host
jsmith@remote-host’s password:
Now try logging into the machine,
... ... ... 
```

首先我们看到, `ssh-add -L`没有回显任何已有的公钥, 添加以后, 再用`ssh-copy-id`就可以以默认的公钥拷贝到远程主机上了.

也许作者在这里是想说明这几个软件之间的关系, 尽管有别的方法拷贝, 或者补全参数就能搞定.

(但我相信作者是在这里充数啊!!! -.-)

# Hack-64 Crontab 命令

## Crontab 命令

这一小节是介绍计划任务的, 对, 没错, 就是定时爆炸的.

#### 怎样像`cron`添加一个计划任务

比较简单的写法:

```
➤ crontab -e
0 5 * * * /root/bin/backup.sh 
```

敲入`crontab -e`之后会打开一个新文件, 输入什么呢? 输入你想执行的计划任务.

解释一下这些东西:

```
{minute} {hour} {day-of-month} {month} {day-of-week} {full-path-to-shell-script} 
```

*   `minute` 0-59
*   `hour` 0-23
*   `day-of-month` 0-31
*   `month` 1-12
*   `day-of-week` 0-7

这些英文这么简单, 不用翻译了吧 :)

#### 举栗子

1.每天`00:01`运行备份脚本

```
1 0 * * * /root/bin/backup.sh 
```

2.周一到周五每天`11:59`运行备份脚本

```
59 11 * * 1,2,3,4,5 /root/bin/backup.sh 
```

等价于:

```
59 11 * * 1-5 /root/bin/backup.sh 
```

3.每 5 分钟运行某个脚本

```
*/5 * * * * /root/bin/check-status.sh 
```

4.每月 1 号`13:10`运行某个脚本

```
10 13 1 * * /root/bin/full-backup.sh 
```

#### crontab 命令参数

*   `crontab –e` 编辑任务列表
*   `crontab –l` 列出所有任务
*   `crontab -r` 移除任务文件(删除所有任务)
*   `crontab -ir` 删除文件的时候让你确认(跟 rm -i 一样)

#### 扩展阅读

*   [15 Awesome Cron Job Examples](http://www.thegeekstuff.com/2009/06/15-practical-crontab-examples/)
*   [How to Run Cron Every 5 Minutes, Seconds, Hours, Days, Months](http://www.thegeekstuff.com/2011/07/cron-every-5-minutes/)
*   [Cron Vs Anacron](http://www.thegeekstuff.com/2011/05/anacron-examples/)

# Hack-65 用 SysRq key 安全的重启

## 用 SysRq key 安全的重启

Magic SysRq key 是 Linux 内核中的一个组合键, 它允许用户执行一些低权限的命令, 无视系统当前的状态.

它经常被用来恢复死机的系统, 或者重启系统而不打断文件系统的状态. 这个组合键是`Alt+SysRq+commandkey`,在很多系统中`SysRq`键是`printscreen`键.

首先, 你要启用这个功能:

```
echo "1" > /proc/sys/kernel/sysrq 
```

#### `commandkey`列表

*   `k` 杀死所有绑定在当前虚拟终端上的进程.
*   `s` 同步所有已挂载的文件系统.
*   `b` 立即重启系统, 不同步数据也不卸载磁盘.
*   `e` 发送 SIGTERM 信号到所有进程(除了 init 主进程).
*   `m` 向终端输出当前的内存信息.
*   `i` 发送 SIGKILL 信号到所有进程(除了 init 主进程).
*   `r` 将键盘从 raw mode 转换到 XLATE mode.
*   `t` 向终端输出当前所有的任务(进程)信息.
*   `u` 以只读模式重新挂载当前已经挂载的文件系统.
*   `o` 立即关闭系统.
*   `p` 打印当前的注册信息和标志位(不知道是啥..).
*   `0-9` 设定终端日志级别, 控制发送到终端的内核信息.
*   `f` 杀死进程的(消耗更多内存).
*   `h` 显示帮助信息.

另注: **Ubuntu**上并没有成功... 所以如果你觉着上面的是在扯淡或者没有卵用, 那也是很正常的, 因为我也这样觉着...

# Hack-66 Parted 命令

## Parted 命令

知道`Gparted`吧? 这个`parted`就是它的无图形化界面.

我还是推荐大家用`Gparted`, 因为操作很直观嘛. 当然, 学了`parted`更好啦, 有些时候没有图形化界面, 就得需要命令行界面了.

还要记住一点, 每次操作的时候, 你必须清楚地明白你在做什么, 否则没有后悔药给你吃的.

你也知道数据恢复是多么的昂贵.

正餐开始(看看就好, 不必上手操作的):

#### 选择要操作的分区

当你直接输入`parted`的时候, 默认的操作对象是系统中第一个可用的分区.

下面的例子中, 它自动选择了`/dev/sda` 做为操作对象, 因为这是系统中第一个可用的分区.

```
# parted
GNU Parted 2.3
Using /dev/sda
Welcome to GNU Parted! Type 'help' to view a list of commands.
(parted)
To choose a different hard disk, use the select command as shown below.
(parted) select /dev/sdb 
```

当他找不到所选择的分区时, 就会抛出一个错误:

```
Error: Error opening /dev/sdb: No medium found
Retry/Cancel? y 
```

#### 用`print`显示所有可操作的磁盘

用这个命令可以显示出所有可操作的磁盘分区, 他还能够显示一些磁盘的属性, 比如模式, 大小, 蔟大小, 分区表信息等等.

```
(parted) print
Model: ATA WDC WD5000BPVT-7 (scsi)
Disk /dev/sda: 500GB
Sector size (logical/physical): 512B/4096B
Partition Table: msdos
Number Start End Size Type Filesystem Flags
1 1049kB 106MB 105MB primary fat16 diag
2 106MB 15.8GB 15.7GB primary ntfs boot
3 15.8GB 266GB 251GB primary ntfs
4 266GB 500GB 234GB extended
5 266GB 269GB 2682MB logical ext4
7 269GB 270GB 524MB logical ext4
8 270GB 366GB 96.8GB logical lvm
6 366GB 370GB 3999MB logical linux-swap(v1)
9 370GB 500GB 130GB logical ext4 
```

#### 用`mkpart`在硬盘中创建主分区

`mkpart`命令可以创建主分区和逻辑分区, 他所需要的参数是 `START`和`END`. 也就是标记从哪儿分, 分到哪儿. 单位是`MB`.

下面的例子分了一个 15G 的区:

```
(parted) mkpart primary 106 16179 
```

你也可以用下面的例子来开启磁盘的`boot`标志, 使之作为一个启动硬盘. Linux 保留了 1-4 或者 1-3 作为主分区, 把其他的数字所谓扩展分区.

```
(parted) set 1 boot on 
```

#### 用`mkpart`在硬盘中创建逻辑分区

在执行操作之前, 我们先看看磁盘的信息:

```
(parted) print
Model: ATA WDC WD5000BPVT-7 (scsi)
Disk /dev/sda: 500GB
Sector size (logical/physical): 512B/4096B
Partition Table: msdos
Number Start End Size Type Filesystem Flags
1 1049kB 106MB 105MB primary fat16 diag
2 106MB 15.8GB 15.7GB primary ntfs boot
3 15.8GB 266GB 251GB primary ntfs
4 266GB 500GB 234GB extended 
5 266GB 316GB 50.0GB logical ext4
6 316GB 324GB 7999MB logical linux-swap(v1)
7 324GB 344GB 20.0GB logical ext4
8 344GB 364GB 20.0GB logical ext2 
```

用下面的命令来创建一个 128G 的逻辑分区:

```
(parted) mkpart logical 372737 500000 
```

现在我们再来查看一下磁盘信息:

```
(parted) print
Model: ATA WDC WD5000BPVT-7 (scsi)
Disk /dev/sda: 500GB
Sector size (logical/physical): 512B/4096B
Partition Table: msdos
Number Start End Size Type Filesystem Flags
1 1049kB 106MB 105MB primary fat16 diag
2 106MB 15.8GB 15.7GB primary ntfs boot
3 15.8GB 266GB 251GB primary ntfs
4 266GB 500GB 234GB extended 
5 266GB 316GB 50.0GB logical ext4
6 316GB 324GB 7999MB logical linux-swap(v1)
7 324GB 344GB 20.0GB logical ext4
8 344GB 364GB 20.0GB logical ext2
9 373GB 500GB 127GB logical 
```

#### 用`mkfs`创建文件系统

有了磁盘还不算完, 还要在里面创建文件系统, 当然, 这种操作也是极其危险的, 因为格式化之后的数据将会全部丢失. `Parted`支持的文件系统有:ext2, mips, fat16, fat32, linux-swap 和 reiserfs(如果安装了 libreiserfs 的话).

下面的例子把第八块儿分区的格式从`ext4`变为了`ext2`:

```
(parted) print
Model: ATA WDC WD5000BPVT-7 (scsi)
Disk /dev/sda: 500GB
Sector size (logical/physical): 512B/4096B
Partition Table: msdos
Number Start End Size Type Filesystem Flags
1 1049kB 106MB 105MB primary fat16 diag
2 106MB 15.8GB 15.7GB primary ntfs boot
3 15.8GB 266GB 251GB primary ntfs
4 266GB 500GB 234GB extended 5 266GB 316GB 50.0GB logical ext4
6 316GB 324GB 7999MB logical linux-swap(v1)
7 324GB 344GB 20.0GB logical ext4
8 344GB 364GB 20.0GB logical ext4
9 364GB 500GB 136GB  logical ext4 
```

然后开始格式化:

```
(parted) mkfs
Warning: The existing file system will be destroyed and
all data on the partition will be lost. Do you want to
continue?
Yes/No? y
Partition number? 8
File system type?
[ext2]? ext2 
```

执行完成之后我们再来看一下:

```
(parted) print
Model: ATA WDC WD5000BPVT-7 (scsi)
Disk /dev/sda: 500GB
Sector size (logical/physical): 512B/4096B
Partition Table: msdos
Number Start End Size Type Filesystem Flags
1 1049kB 106MB 105MB primary fat16 diag
2 106MB 15.8GB 15.7GB primary ntfs boot
3 15.8GB 266GB 251GB primary ntfs
4 266GB 500GB 234GB extended 5 266GB 316GB 50.0GB logical ext4
6 316GB 324GB 7999MB logical linux-swap(v1)
7 324GB 344GB 20.0GB logical ext4
8 344GB 364GB 20.0GB logical ext2
9 364GB 500GB 136GB  logical ext4 
```

(没错我就是复制上面的内容然后再把 4 改成 2 的.)

#### 用`mkpartfs`既划分磁盘又对分区进行格式化

其实就是把上面的两条命令组合了起来, 可以这么理解.

先是看一下原分区什么样:

```
(parted) print
Model: ATA WDC WD5000BPVT-7 (scsi)
Disk /dev/sda: 500GB
Sector size (logical/physical): 512B/4096B
Partition Table: msdos
Number Start End Size Type Filesystem Flags
1 1049kB 106MB 105MB primary fat16 diag
2 106MB 15.8GB 15.7GB primary ntfs boot
3 15.8GB 266GB 251GB primary ntfs
4 266GB 500GB 234GB extended 5 266GB 316GB 50.0GB logical ext4
6 316GB 324GB 7999MB logical linux-swap(v1)
7 324GB 344GB 20.0GB logical ext4
8 344GB 364GB 20.0GB logical 
```

然后我们新建一个分区并格式化:

```
(parted) mkpartfs logical fat32 372737 500000 
```

然后再看看新的磁盘信息:

```
(parted) print
Model: ATA WDC WD5000BPVT-7 (scsi)
Disk /dev/sda: 500GB
Sector size (logical/physical): 512B/4096B
Partition Table: msdos
Number Start End Size Type Filesystem Flags
1 1049kB 106MB 105MB primary fat16 diag
2 106MB 15.8GB 15.7GB primary ntfs boot
3 15.8GB 266GB 251GB primary ntfs
4 266GB 500GB 234GB extended 5 266GB 316GB 50.0GB logical ext4
6 316GB 324GB 7999MB logical linux-swap(v1)
7 324GB 344GB 20.0GB logical ext4
8 344GB 364GB 20.0GB logical 9 373GB 500GB 127GB logical fat32 lba
(parted) 
```

#### 用`resize`来改变分区大小

```
(parted) resize 9
Start? [373GB]? 373GB
End? [500GB]? 450GB 
```

很简单的, 选中要操作的对象, 然后选择开始, 在选择结束, 就 OK 了.(其实用`Gparted`更简单, 直接拖动就好了...)

然后下面还是一堆磁盘信息, 节省篇幅, 我就不贴了.

#### 用`cp`来复制分区

这个命令有点厉害, 类似于`dd`, 他可以把整个分区都复制到别处(别的分区). 需要注意的是, 保存被复制的分区的大小一定要足够, 还有, 这是一个覆盖操作.

(作者又打印了一遍磁盘信息, 我在此略过)

建议把要操作的两块磁盘都先卸载, 以免有其他的程序对他们进行读写.

```
# mount /dev/sda8 /mnt
# cd /mnt
# ls -l
total 52
-rw-r--r-- 1 root root
-rw-r--r-- 1 root root
0 2011-09-26 22:52 part8
20 2011-09-26 22:52 test.txt

# umount /mnt
# mount /dev/sda10 /mnt
# cd /mnt
# ls -l
total 48
-rw-r--r-- 1 root root 0 2011-09-26 22:52 part10 
```

用`cp`命令把 8 复制到 10:

```
(parted) cp 8 10
growing file system... 95% (time left 00:38) 
```

下面是复制完成之后的结果:

```
# mount /dev/sda10 /mnt
# cd /mnt
# ls -l
total 52
-rw-r--r-- 1 root root 0 2011-09-26 22:52 part8
-rw-r--r-- 1 root root 20 2011-09-26 22:52 test.txt 
```

另外需要注意的是, 如果目标磁盘和被复制的磁盘的文件系统不一致, 这个复制操作将会重建目标磁盘的文件系统使其和被复制的磁盘分区一样.(也就是说, 复制操作使得两个分区一模(mu)一样.)

#### 用`rm`命令移除某个分区

```
(parted) rm
Partition number? 9
(parted) 
```

也很简单...

这样一删的话, 里面的东西就全没了 :)

# Hack-67 Rsync 命令

## Rsync 命令

`rsync` 表示 `remote sync`.

也就是远程同步的意思.

它是用来备份的.

#### `rsync`的特性

*   **速度快**: 第一次同步的时候会将全部的文件都进行备份, 以后的时候他就回对比改变过的文件, 而使得备份速度很快.
*   **安全**: `rsync`允许在同步的时候使用 ssh 协议进行加密数据.
*   **消耗带宽小**: 同步过程中使用压缩技术, 使得传输的数据更小.
*   **权限低**: 使用`rsync`不需要任何额外的权限.

语法:

```
$ rsync options source destination 
```

源地址和目的地址既可以是本地也可以时远程服务器, 如果是远程服务器的话还需要指定登录名以及远程服务器地址.

#### 在本地同步两个文件夹

```
$ rsync -zvr /var/opt/installation/inventory/ /root/temp
building file list ... done
sva.xml
svB.xml
.
sent 26385 bytes
received 1098 bytes
total size is 44867
speedup is 1.63 
```

再上面例子的参数中:

*   `-z` 开启压缩
*   `-v` 输出日志信息
*   `-r` 递归同步(在文件夹中)

还有一点需要说明, `rsync`没有保留原始文件的创建时间信息, 也就是说, 目的地的文件的创建时间与原始文件的创建时间不一致.

#### 保存文件的创建时间

刚说了他不能保留原始文件的创建时间, 这就过来打脸了:

`-a`选项可以使得原始文件与备份文件一模一样, 包括创建时间, 属性, 所属用户和所属组, 权限信息等等.

这里我就不演示了, 你可以自己试一下 :D

#### 同步文件到远程服务器

```
rsync -avz /root/temp/ thegeekstuff@192.168.200.10:/home/thegeekstuff/temp/
Password:
building file list ... done
./
rpm/
rpm/Basenames
rpm/Conflictname
sent 15810261 bytes received 412 bytes 2432411.23 bytes/sec
total size is 45305958
speedup is 2.87 
```

同步远程目录的格式为:

```
rsync -avz [local_path] [username]@[server_ip_address]:[/file_path] 
```

#### 同步远程服务器文件到本地

这个跟上一个是反方向操作.不过命令格式都差不多的:

```
rsync -avz [username]@[server_ip_address]:[/file_path] [local_path] 
```

没什么特别复杂的地方, 哦对了, 同步都是覆盖操作的, 没有像 git 那样有记录什么的, 所以还是小心一点咯

#### 扩展阅读

*   [15 rsync Command Examples](http://www.thegeekstuff.com/2010/09/rsync-command-examples/)
*   [6 rsync Examples](http://www.thegeekstuff.com/2011/01/rsync-exclude-files-and-folders/)
*   [更多介绍 - 中文](http://www.oschina.net/p/rsync)

# Hack-68 Chkconfig/Service 命令

## Chkconfig 命令 / Service 命令

**Ubuntu**上并没有这条命令...

所以我就说一下**Ubuntu**上取而代之的`service`命令吧 :)

首先说一下他是干嘛的, 字如其名, 当然是用来控制服务的.

#### 查看当前所有服务状态

在`man service`中我们可以看到有`service --status-all`这么一条命令, 没错, 他就是用来查看当前系统所运行的所有服务状态的.

输出的结果如下:

```
➤ service --status-all
 [ + ]  acct
 [ + ]  acpid
 [ + ]  alsa-utils
 [ - ]  anacron
 [ + ]  apparmor
 [ + ]  apport
 [ + ]  atd
 [ + ]  avahi-daemon
 [ + ]  binfmt-support
 [ - ]  bluetooth
 [ - ]  bootmisc.sh
 [ - ]  brltty
 [ + ]  cgmanager
 [ - ]  cgproxy
 [ - ]  cgroupfs-mount
 [ - ]  checkfs.sh
 [ - ]  checkroot-bootclean.sh
 [ - ]  checkroot.sh
... ... ...
 [ + ]  urandom
 [ - ]  uuidd
 [ + ]  virtualbox
 [ + ]  whoopsie
 [ - ]  x11-common 
```

列举的就是所有的服务, 其中,每个服务名前面的`+`代表服务正在运行,`-`则说明服务没有在运行.

#### 系统服务开启与关闭

语法:

```
Usage: service < option > | --status-all | [ service_name [ command | --full-restart ] ] 
```

具体还要看服务的脚本是怎么写的, 不过一般都会有`start`, `stop`, `status`这三个.

脚本的存放位置在`/etc/init.d/`和`/etc/systemd/system/`目录.

#### 添加服务到开机启动

这里用到的则是另一个命令了:`update-rc.d`

设置某项服务开机启动:

```
update-rc.d [service_name] defaults 
```

删除某项服务开机启动:

```
update-rc.d -f [service_name] remove 
```

当然, 这些命令都需要 root 权限.

# Hack-69 Anacron 配置

## Anacron 配置

`anacron`是`cron`在桌面系统中的软件.(其实桌面系统也有 cron.)

你可能会问, 为什么要有一个桌面系统版本啊? 因为, 桌面系统不是服务器, 不需要也不可能 24 小时运行, 所以`anacron`的意义就是在一个非 24 小时运行的机器上执行计划任务.

比如说, 当你在笔记本上运行了一个每天晚上 11 点备份文件的脚本, 但是你不可能确保每天晚上 11 点都会开机啊, 那么备份就不会运行了么? 对于`cron`来说是的, 因为过了那个点儿了, 他就不会运行了, 但是对于`anacron`来说就不是, 因为他就是专门应付这种情况的, 如果今天晚上 11 点没有运行备份脚本, 那么当你再次启动计算机的时候, 他就会立即执行这个备份脚本. 同样, 如果系统待机也会是这样, 当系统再次被唤起的时候就会执行那些本该执行却没有执行的计划任务.

#### Anacrontab 格式

正如`cron`的记录文件是`/etc/crontab`一样, `anacron`也有一个记录文件`/etc/anacrontab`.

`/etc/anacrontab`的格式为:

```
period delay job-identifier command 
```

第一个字段是**执行的周期**, 如果写 1,就表示每天执行, 如果写 7 就代表每周执行, 同理, 如果写 30,就代表每月执行, 当然, 也可以是任意的阿拉伯数字.

第二个字段是**延迟执行的时间**, 这个是用来应付那些没有被正常执行的计划任务的, 也就是说, 如果一个任务没有被正常执行(关机待机等情况), 当再次开机后, 需要延迟多长时间再次执行. 它的单位是分钟.

第三个字段是**任务的标识(zhi4)符**, 每个文件都必须又一个独一无二的标识符, 标识符文件保存在`/var/spool/anacron/`目录下, 每个文件记录着不同任务上次执行的时间, 这也就解释的通为什么它可以在开机后执行那些没有被正常执行的任务了.

第四个字段是**要执行的命令**, 比如要执行一个脚本, 就可以写"`/bin/sh /root/bala/backup.sh`".

#### 举个栗子

```
➤ cat /etc/anacrontab
7 15 test.daily /bin/sh /root/bala/backup.sh 
```

每七天执行备份脚本, 任务的标识符为`test.daily`, 如果没有正常运行, 则在开机后 15 分钟之后再次运行.

#### 两个变量

`START_HOURS_RANGE` 和 `RANDOM_DELAY`

干什么用呢? 慢慢说.

上面的这个例子是, 每天执行一个脚本, 什么时候执行? 开机十五分钟之后执行. 但是, 如果你的笔记本一周不关机怎么办? 难道备份脚本就不要执行了吗? 肯定不行啊. 所以这个时候就需要`/etc/anacrontab`里的变量(`START_HOURS_RANGE`)了.

默认的值是 3-22, 也就是凌晨 3 点到晚上 10 点, 这个时间段所谓一个开机周期, 如果过了这个周期, 那`anacron`就会知道新的一天开始了.

上面提到的第二个变量也有点意思, 刚才我们说到开机 15 分钟之后执行那个脚本, 但是真实情况还要加点东西, 加点什么呢? 加的就是这个`RANDOM_DELAY`内的分钟数, 默认的`RANDOM_DELAY`值是 45, 也就是说, 每个任务开启的时间是不确定的, 是在用户指定的时间上在加 0-45 之间的一个数.

#### Cron Vs Anacron

| **Cron** | **Anacron** |
| --- | --- |
| 最小执行周期是分钟 | 最小执行周期是天 |
| 任何用户都可以运行 | 只允许超级用户运行 |
| 要求系统 7x24 小时运行, 如果某个任务错过了执行时间, 那么它将不会被执行 | 不要求系统 7x24 小时运行. 如果某个计划任务没有被正常运行, 那么他将在下次开机后再次运行 |
| 服务器 | 桌面机或者笔记本 |

扩展阅读: [Cron Vs Anacron](http://www.thegeekstuff.com/2011/05/anacron-examples/)

# Hack-70 IPTables 规则举例

## IPTables 规则举例

#### 删除现有的规则

用下面的命令来清空当前所设置的所有规则.

```
iptables -F
(or)
iptables --flush 
```

#### 设置默认的链规则

默认的规则是允许通过, 如果你有特殊需求, 可以设置成拒绝:

```
iptables -P INPUT DROP
iptables -P FORWARD DROP
iptables -P OUTPUT DROP 
```

当你设置了默认的 INPUT 和 OUTPUT 链为 DROP 时, 在你的所有防火墙上必须要再定义两条规则, 一个是入, 另一个是出.

下面的所有例子中, 每一个方案都定义了两条规则, 一条是入规则, 另一条则是出规则.(因为默认是拒绝通过出入包的.)

当然, 如果你足够相信你的用户, 也可以将默认的出站规则设置为接受, 允许所有的出站请求.这样的话, 防火墙就只需要一条入站规则了.

#### 拒绝某个特定的 ip 地址

在列举更多的例子之前, 先来看一下怎样拒绝来自某个 ip 的全部请求:

```
iptables -A INPUT -s "ip_address_to_block" -j DROP 
```

当你在日志中发现某个 ip 异常时, 你就可以用这条规则来限制这个 ip 地址.

你也可以用下面的方式来拒绝 eth0 网卡上来自这个 ip 地址的 tcp 请求:

```
iptables -A INPUT -i eth0 -s "ip_address_to_block" -j DROP
iptables -A INPUT -i eth0 -p tcp -s "ip_address_to_block" -j DROP 
```

#### 允许所有的 SSH 入站请求

```
iptables -A INPUT -i eth0 -p tcp --dport 22 -m state --state NEW,ESTABLISHED -j ACCEPT
iptables -A OUTPUT -o eth0 -p tcp --sport 22 -m state --state ESTABLISHED -j ACCEPT 
```

上面的规则允许了所有 eth0 网卡上的 SSH 入站请求.

#### 限制特定的 ip 建立 SSH 连接

```
iptables -A INPUT -i eth0 -p tcp -s 192.168.100.0/24 --dport 22 -m state --state NEW,ESTABLISHED -j ACCEPT
iptables -A OUTPUT -o eth0 -p tcp --sport 22 -m state --state ESTABLISHED -j ACCEPT 
```

上面的例子仅允许来自 192.168.100.*的连接.

当然也可以把`192.168.100.0/24`换成`192.168.100.0/255.255.255.0`, 如果你不嫌麻烦的话...

#### 扩展阅读

*   [25 Most Frequently Used Linux IPTables Rules Examples](http://www.thegeekstuff.com/2011/06/iptables-rules-examples/)
*   [IPTables Tables, Chains, Rules Fundamentals](http://www.thegeekstuff.com/2011/01/iptables-fundamentals/)
*   [How to Add Firewall Rules](http://www.thegeekstuff.com/2011/02/iptables-add-rule/)
*   [Incoming and Outgoing Rule Examples](http://www.thegeekstuff.com/2011/03/iptables-inbound-and-outbound-rules/)

都是英文的, 一点一点啃吧 :)