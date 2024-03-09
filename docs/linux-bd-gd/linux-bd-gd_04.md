# 2\. 准备一个新分区

# 2.1\. 简介

本章，我们将为 LFS 系统准备一个分区。步骤是创建一个新的磁盘分区并在这个分区上创建文件系统，然后挂载它。

［译者注］很多 LFS 新手第一次都搞不清路径问题，加上指导书中又刻意省略了解包、切换目录、清理目录的命令，着实迷糊了不少人，如果你愿意，那么可以参考一下 youbest 兄的一篇大作：[手把手教你如何建立自己的 Linux 系统（LFS 速成手册）](http://www.linuxsir.org/bbs/thread322894.html)，其中的每个命令和步骤都写的一清二楚。不过，这篇文章是基于 LFS 6.3 版本的，但是它是真正的**手把手**教程，可以**无痛**的完成整个 LFS 全部过程，强烈推荐！

# 2.2\. 创建一个新分区

像大多数其他操作系统一样，LFS 通常安装在一个新的专用分区上。如果你有充足的磁盘空间，推荐将 LFS 系统构建在一个新的空白磁盘分区上。当然，LFS 系统(甚至是多个 LFS 系统)也可以安装在现存的某个操作系统所在的分区上，它们完全可以和平共处。这个文档：[*http://www.linuxfromscratch.org/hints/downloads/files/lfs_next_to_existing_systems.txt*](http://www.linuxfromscratch.org/hints/downloads/files/lfs_next_to_existing_systems.txt) 解释了怎样实现上面的目标。但是本书只讨论如何在一个新的空白分区上构建 LFS 系统。

建立一个最小的系统需要 1.3GB 左右的分区，这样才能有足够的空间存储并编译所有的源码包。当然，如果您打算把 LFS 作为您的主 Linux 系统，您可能会在上面安装其它软件，那么您就需要更大的空间(2~3GB)。LFS 系统本身并不占用这么多空间，所需的空间大部分用来为软件编译提供足够的临时空间，编译软件包的时候需要使用大量的临时空间，软件包装好之后这些临时空间可以回收。

因为编译过程中内存(RAM)并不总是够用的，所以最好使用一个小的硬盘分区作为交换空间。内核使用交换空间来存放不常用到的数据，以便为正在运行的进程腾出内存空间。LFS 系统使用的交换分区与宿主系统使用的交换分区可以是同一个，因此当宿主系统已经有交换分区的时候就不必为 LFS 系统再创建一个了。

启动一个磁盘分区程序，例如 `cfdisk` 或者 `fdisk` ，用即将在上面创建新分区的硬盘名字作为命令行选项，比如主 IDE 硬盘名字就是 `/dev/hda` 。创建一个 Linux 本地分区，需要的话，您还要创建一个交换分区。如果您还不知道如何使用这两个工具的话，请参考 `cfdisk(8)` 或者 `fdisk(8)` 手册页。

请记住新分区的名称(比如 `hda5`)，本书称其为"LFS 分区"，交换分区的名称也要记住，这些分区的名称以后将在 `/etc/fstab` 文件中用到。

# 2.3\. 在新分区上创建文件系统

空白分区建立之后，现在可以在上面创建文件系统了。在 Linux 世界使用最广泛的是 `ext2` 文件系统，但是随着新的大容量硬盘的出现，日志文件系统开始逐渐流行。`ext3` 是一种被广泛使用的基于 `ext2` 的日志文件系统，并且与 E2fsprogs 工具兼容。我们将创建一个 `ext3` 文件系统。您还可以在 [*http://www.linuxfromscratch.org/blfs/view/svn/postlfs/filesystems.html*](http://www.linuxfromscratch.org/blfs/view/svn/postlfs/filesystems.html)找到创建其它文件系统的指导。

要在 LFS 分区上创建 `ext3` 文件系统，请运行下面的命令：

```
mke2fs -jv /dev/<xxx> 
```

用您创建的 LFS 分区的名称替换 *`<xxx>`* (我们上面的例子里是 `hda5`)。

### 注意

有些宿主系统在文件系统创建工具(E2fsprogs)中使用了自定义的增强特性。这可能会导致你在第九章重启进入新的 LFS 系统时出现问题。因为这些特性并不被 LFS 安装的 E2fsprogs 支持，你将会得到一个类似于"unsupported filesystem features, upgrade your e2fsprogs"的错误。你可以使用下面的命令来检查你的宿主系统是否使用了自定义的增强特性：

```
debugfs -R feature /dev/<xxx> 
```

如果输出的特性不同于：`has_joural`, `dir_index`, `filetype`, `large_file`, `resize_inode`, `sparse_super` 或 `needs_recovery` ， 那么就说明你的宿主系统使用了自定义的增强特性。在这种情况下，为了避免后面的问题，请重新编译 E2fsprogs 包，然后用这个重新编译过的工具来创建你将要用来安装 LFS 系统的文件系统：

```
cd /tmp
tar -xjvf /path/to/sources/e2fsprogs-1.39.tar.bz2
cd e2fsprogs-1.39
mkdir -v build
cd build
../configure
make #note that we intentionally don't 'make install' here!
./misc/mke2fs -jv /dev/<xxx>
cd /tmp
rm -rfv e2fsprogs-1.39 
```

如果创建了交换分区，那么还需要用下面的命令进行格式化，如果您使用已有的交换分区，那么就不需要格式化了。

```
mkswap /dev/<yyy> 
```

用您创建的交换分区的名称替换 *`<yyy>`* 。

# 2.4\. 挂载新分区

创建文件系统之后，要让分区可以访问，需要把分区挂载到一个选定的挂载点上。考虑在本书的目的，我们假定文件系统挂载到 `/mnt/lfs` ，但是您也可以选择别的目录。

选定一个挂载点，并指定给 `LFS` 环境变量，请运行命令：

```
export LFS=/mnt/lfs 
```

下一步，创建这个挂载点，并挂载 LFS 文件系统，请运行命令：

```
mkdir -pv $LFS
mount -v -t ext3 /dev/<xxx> $LFS 
```

用您创建的 LFS 分区名称替换 *`<xxx>`* 。

如果 LFS 装在多个分区上(比如一个分区用于 `/` 目录，另一个分区用于 `/usr` 目录)，用下面的命令挂载它们：

```
mkdir -pv $LFS
mount -v -t ext3 /dev/<xxx> $LFS
mkdir -v $LFS/usr
mount -v -t ext3 /dev/<yyy> $LFS/usr 
```

用相应的分区名称替换 *`<xxx>`* 和 *`<yyy>`* 。

请确认挂载新分区的时候没有使用太多的限制选项(如 `nosuid`, `nodev`, `noatime` 选项)。运行不带参数的 `mount` 命令看看挂载的 LFS 分区设置了什么选项，如果出现了 *`nosuid`*, *`nodev`*, *`noatime`* 选项之一，您就需要重新挂载这个分区。

如果你使用了交换分区，可以使用下述 `swapon` 命令确保它被启用了：

```
/sbin/swapon -v /dev/<zzz> 
```

将 *`<zzz>`* 替换为正确的交换分区名。

现在工作的空间已经建立好了，接下来要下载所需的软件包。