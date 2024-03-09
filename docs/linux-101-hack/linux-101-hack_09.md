# 第九章 - 安装软件

这一章介绍了在不同的发行版上用不同的方法安装软件.

RedHat 系列(rpm),Debian 系列(apt-*).还有 CentOS 的 yum.

具体的区别可以看 [What is the difference between yum, apt-get, rpm, ./configure && make install?](http://superuser.com/questions/125933/what-is-the-difference-between-yum-apt-get-rpm-configure-make-install)

| 发行版 | 低级管理 | 高级管理 |
| --- | --- | --- |
| Debian and derivatives | dpkg | apt-get / aptitude |
| CentOS | rpm | yum |
| openSUSE | rpm | zypper |

最后一节介绍了怎样通过源码进行安装.

这里介绍的可能没有以前那么好玩了, 不过都很实用(对每个发行版来说).

~~还有 80 页就翻译完啦~~~

# Hack-71 Yum 命令

## Yum 命令

Yum 的全称是 "Yellowdog Updater Modified".

[wikipedia 链接](https://wikipedia.kfd.me/zh-cn/Yellowdog_Updater,_Modified)

#### 用`yum install`安装软件包

如果想安装某个软件, 只需要这样做:

```
yum install packagename 
```

下面的例子安装了 postgresql:

```
# yum install postgresql.x86_64
Resolving Dependencies
Install 2 Package(s)
Is this ok [y/N]: y
Running Transaction
Installing : postgresql-libs-9.0.4-5.fc15.x86_64 1/2
Installing : postgresql-9.0.4-5.fc15.x86_64 2/2 
```

默认情况下, `yum`会向你确认要安装的包, 如不想显示这个确认, 可以加一个`-y`的参数:

```
# yum -y install postgresql.x86_64 
```

#### 用`yum remove`卸载软件包

卸载软件包也很简单:

```
# yum remove
postgresql.x86_64
Package postgresql.x86_64 0:9.0.4-5.fc15 will be erased
Is this ok [y/N]: y
Running Transaction
Erasing : postgresql-9.0.4-5.fc15.x86_64 1/1 
```

这个例子是卸载 postgresql 的.

#### 用`yum update`升级某个软件包

```
# yum update postgresql.x86_64 
```

这个例子升级了 postgresql.

#### 用`yum search`搜索某个软件包

如果你不确定具体要安装的软件包的名字, 可以用`yum search`来搜索.

下面的例子搜索了那些名字中带有 firefox 的包:

```
# yum search firefox
Loaded plugins: langpacks, presto, refresh-packagekit
============== N/S Matched: firefox ======================
firefox.x86_64 : Mozilla Firefox Web browser
gnome-do-plugins-firefox.x86_64
mozilla-firetray-firefox.x86_64
mozilla-adblockplus.noarch : Mozilla Firefox extension
mozilla-noscript.noarch : Mozilla Firefox extension 
```

如果想显示全部的信息, 可以用`yum search all`.

#### 用`yum info`来显示包的所有信息

当你通过`yum search`搜索到某个包时, 可以用`yum info`来查看包的信息:

```
# yum info samba-common.i686
Loaded plugins: langpacks, presto, refresh-packagekit Available Packages
Name        : samba-common
Arch        : i686
Epoch       : 1
Version     : 3.5.11
Release     : 71.fc15.1
Size        : 9.9 M
Repo        : updates
Summary     : Files used by both Samba servers and clients
URL         : http://www.samba.org/
License     : GPLv3+ and LGPLv3+
Description : Samba-common provides files necessary for both the server and client 
```

上面的例子查看了`samba-common`的包信息.

#### 扩展阅读

[15 Linux Yum Command Examples](http://www.thegeekstuff.com/2011/08/yum-command-examples/)

# Hack-72 RPM 命令

## RPM 命令

`RPM`的全称是`Red Hat Package Manager`.

#### 用`rpm -ivh`来安装 RPM 包

RPM 的文件名包含了软件的名字, 版本号, 发行号,以及软件架构.

比如在`MySQL-client-3.23.57-1.i386.rpm`这个文件名中,

*   MySQL-client 是包的名字
*   3.23.57 是版本号
*   1 是发行号
*   i386 是软件架构(32 位)

当你安装一个 RPM 包时, RPM 会检查你的系统是否能够安装这个包, 看一下这个包文件要安装在哪儿, 安装完成之后还会更新 RPM 的软件数据库.

```
# rpm -ivh
MySQL-client-3.23.57-1.i386.rpm
Preparing...################################### [100%]
1:MySQL-client############################## [100%] 
```

上面的例子中:

*   `-i` 代表安装(install)
*   `-v` 代表显示详细信息(verbose)
*   `-h` 打印 hash marks(我也不知道这是什么鬼...)

#### 用`rpm -qa`列出全部已经安装的包

```
# rpm -qa
cdrecord-2.01-10.7.el5
bluez-libs-3.7-1.1
setarch-2.0-1.1
...
...
... 
```

其中:

*   `-q` 列举, 查询(query)
*   `-a` 全部(all)

#### 列举包的时候规定显示格式

```
# rpm -qa --queryformat '%{name-%{version}-%{release} %{size}\n'
cdrecord-2.01-10.7 12324
bluez-libs-3.7-1.1 5634
setarch-2.0-1.1 235563
...
...
... 
```

#### 用`rpm -qf`查看某个文件所属的包

```
# rpm -qf /usr/bin/mysqlaccess
MySQL-client-3.23.57-1 
```

可以看到这个文件属于`MySQL-client-3.23.57-1`这个包.

#### 用`rpm -qip`查看某个已安装包的具体信息

```
# rpm -qip MySQL-client-3.23.57-1.i386.rpm
Name        : MySQL-client Relocations: (not relocatable)
Version     : 3.23.57
Vendor      : MySQL AB
Release     : 1
Build Date  : Mon 09 Jun 2003
Install Date:
Build Host  : build.mysql.com
Group       : Applications/Databases
Size        : 5305109
Signature   : (none)
Packager    : Lenz Grimmer
URL         : http://www.mysql.com/
Summary     : MySQL - Client
License     : GPL / LGPL
Description : This package is a standard MySQL client. 
```

其中:

*   `-i` 查看 rpm 包的信息
*   `-p` 指定包的名字

#### 查看包内的文件

```
$ rpm -qlp ovpc-2.1.10.rpm
/usr/bin/mysqlaccess
/usr/bin/mysqldata
/usr/bin/mysqlperm
...
...
/usr/bin/mysqladmin 
```

其中`-l`的意思就是列出包内的文件.

#### 用`rpm -qRP`查看某个包依赖的其他包

```
# rpm -qRp MySQL-client-3.23.57-1.i386.rpm
/bin/sh
/usr/bin/perl 
```

这个`mysql-client`就依赖`sh`和`perl`.

#### 扩展阅读

[RPM Command: 15 Examples to Install, Uninstall, Upgrade, Query RPM Packages](http://www.thegeekstuff.com/2010/07/rpm-command-examples/)

# Hack-73 Apt-* 命令

## Apt-* 命令

啊哈, 终于到了 Debian 系列啦~

#### `apt-cache search` 搜索软件包

```
➤ apt-cache search ^apache2$
apache2 - Apache HTTP Server 
```

这个例子搜索了 apache2 这个软件, 可以看到, 支持正则表达式哦~

#### `dpkg -l`列出当前安装的软件包

其实也不仅列出了安装的软件包, 还列出了曾经安装过, 现在没有完全卸载的软件包, 前面有几个标识符, 比如`i`,`r`:

```
apache2 - Apache HTTP Server
➤ dpkg -l
Desired=Unknown/Install/Remove/Purge/Hold
| Status=Not/Inst/Conf-files/Unpacked/halF-conf/Half-inst/trig-aWait/Trig-pend
|/ Err?=(none)/Reinst-required (Status,Err: uppercase=bad)
||/ Name                                 Version                 Architecture            Description
+++-====================================-=======================-=======================-=============================================================================
ii  account-plugin-aim                   3.12.10-0ubuntu2        amd64                   Messaging account plugin for AIM
ii  account-plugin-facebook              0.12+15.10.20150723-0ub all                     GNOME Control Center account plugin for single signon - facebook
ii  account-plugin-flickr                0.12+15.10.20150723-0ub all                     GNOME Control Center account plugin for single signon - flickr
ii  account-plugin-google                0.12+15.10.20150723-0ub all                     GNOME Control Center account plugin for single signon
ii  account-plugin-jabber                3.12.10-0ubuntu2        amd64                   Messaging account plugin for Jabber/XMPP
ii  account-plugin-salut                 3.12.10-0ubuntu2        amd64                   Messaging account plugin for Local XMPP (Salut)
ii  account-plugin-yahoo                 3.12.10-0ubuntu2        amd64                   Messaging account plugin for Yahoo!
ii  accountsservice                      0.6.40-2ubuntu5         amd64                   query and manipulate user account information
ii  acct                                 6.5.5-2.1ubuntu1        amd64                   The GNU Accounting utilities for process and login accounting
ii  ack-grep                             2.14-4                  all                     grep-like program specifically for large source trees
ii  acl                                  2.2.52-2                amd64                   Access control list utilities
ii  acpi-support                         0.142                   amd64                   scripts for handling many ACPI events
...
...
... 
```

可以看到, 乱七八糟一大堆...

这个命令也能安装软件.`dpkg -i packge_name.deb`

#### `apt-get install` 安装软件

也很简单啊, 只要把你需要安装的软件告诉他, 然后他就会按照依赖关系把需要的包一个一个都给装好:

```
$ sudo apt-get install apache2
[sudo] password for ramesh:
The following NEW packages will be installed:
apache2 apache2-mpm-worker apache2-utils
apache2.2-common libapr1 libaprutil1 libpq5
0 upgraded, 7 newly installed, 0 to remove and 26 not upgraded. 
```

#### `apt-get remove` 卸载软件

```
$ sudo apt-get purge apache2
(or)
$ sudo apt-get remove apache2 
```

两者都可以卸载, 区别是什么? 第一个是彻底卸载, 包括各种配置文件,可以理解为`apt-get remove apache2*` 第二个只是移除二进制文件, 单纯的把 apache2 卸载.

除此之外, 还有`apt-get update`, `apt-get upgrade`, `apt-get dist-upgrade`等一系列命令... 想知道他们都是什么吗? 自己去搜吧 :)

相当的 easy~

# Hack-74 从源码安装

## 从源码安装

有时候软件仓库里并没有我们需要的软件, 或者软件仓库里的软件太老了, 而你的强迫症则迫使你安装最新的软件, 怎么办? 从源码编译安装呗!

你也许会问, 为什么是源码从新编译呢? 因为不同的系统所拥有的文件是不一样的, 在我的电脑上可以正常安装的软件, 在你那里就不行, 为什么? 缺文件啊! 所以通过源码安装的时候就会检查这些依赖库, 如果没有, 还需要你再把那些用到的依赖库给安装上. 而且, 源码比起生成的二进制文件小多了~ 还节省带宽呢!

你有会问, 为什么 Windows 直接点开 exe 就能安装了? 因为 exe 都是自成一派的, 程序所运行的文件都在这个 exe 文件当中了, 把 exe 里面的东西提取出来, 再到注册表里一注册, 程序就能跑, 每个软件跟其他的软件都没有半毛钱的关系, 也就不需要检查什么依赖库了(当然, 你得把*.Net*那些东西给装上).所以每个 exe 都比较大...而且是越来越大...

#### 接下来就步入正题了, 怎样从源码安装?

当我们下载好程序的源码时, 第一步, 当然是把他解压了:

```
tar xvfj application.tar.bz2 
```

还记得`bz2`吗?

然后进入这个文件:

```
cd application/ 
```

然后:

```
./configure 
```

这是做什么? 慢慢看!

(这里没有贴输出, 因为每个软件编译的时候都不一样, 不过都是检查依赖包之类的.)

最后呢:

```
make 
```

如果 make 成功了, 你会在当前目录下看到生成的二进制文件, 如果可以运行呢,那就可以安装到本系统上了:

```
make install 
```

(也就是把那些二进制文件复制到系统中, 这个操作需要 root 权限).

在每个下载的规范的源代码中, 都会有一个 README 文件, 这个是干什么呢? 是告诉你这源代码怎么用的 :P