# 第七章、Linux 磁盘与文件系统管理

最近更新日期：20//

系统管理员很重要的任务之一就是管理好自己的磁盘文件系统，每个分区不可太大也不能太小， 太大会造成磁盘容量的浪费，太小则会产生文件无法储存的困扰。此外，我们在前面几章谈到的文件权限与属性中， 这些权限与属性分别记录在文件系统的哪个区块内？这就得要谈到 filesystem 中的 inode 与 block 了。同时，为了虚拟化与大容量磁盘， 现在的 CentOS 7 默认使用大容量性能较佳的 xfs 当默认文件系统了！这也得了解一下。 在本章我们的重点在于如何制作文件系统，包括分区、格式化与挂载等，是很重要的一个章节喔！

# 7.1 认识 Linux 文件系统

## 7.1 认识 Linux 文件系统

Linux 最传统的磁盘文件系统 （filesystem） 使用的是 EXT2 这个啦！所以要了解 Linux 的文件系统就得要由认识 EXT2 开始！ 而文件系统是创建在磁盘上面的，因此我们得了解磁盘的物理组成才行。磁盘物理组成的部分我们在第零章谈过了，至于磁盘分区则在第二章谈过了，所以下面只会很快的复习这两部份。 重点在于 inode, block 还有 superblock 等文件系统的基本部分喔！

### 7.1.1 磁盘组成与分区的复习

由于各项磁盘的物理组成我们在第零章里面就介绍过， 同时第二章也谈过分区的概念了，所以这个小节我们就拿之前的重点出来介绍就好了！ 详细的信息请您回去那两章自行复习喔！^_^。好了，首先说明一下磁盘的物理组成，整颗磁盘的组成主要有：

*   圆形的盘片（主要记录数据的部分）；
*   机械手臂，与在机械手臂上的磁头（可读写盘片上的数据）；
*   主轴马达，可以转动盘片，让机械手臂的磁头在盘片上读写数据。

从上面我们知道数据储存与读取的重点在于盘片，而盘片上的物理组成则为（假设此磁盘为单碟片， 盘片图示请参考第二章图 2.2.1 的示意）：

*   扇区（Sector）为最小的物理储存单位，且依据磁盘设计的不同，目前主要有 512Bytes 与 4K 两种格式；
*   将扇区组成一个圆，那就是柱面（Cylinder）；
*   早期的分区主要以柱面为最小分区单位，现在的分区通常使用扇区为最小分区单位（每个扇区都有其号码喔，就好像座位一样）；
*   磁盘分区表主要有两种格式，一种是限制较多的 MBR 分区表，一种是较新且限制较少的 GPT 分区表。
*   MBR 分区表中，第一个扇区最重要，里面有：（1）主要开机区（Master boot record, MBR）及分区表（partition table）， 其中 MBR 占有 446 Bytes，而 partition table 则占有 64 Bytes。
*   GPT 分区表除了分区数量扩充较多之外，支持的磁盘容量也可以超过 2TB。

至于磁盘的文件名部份，基本上，所有实体磁盘的文件名都已经被仿真成 /dev/sd[a-p] 的格式，第一颗磁盘文件名为 /dev/sda。 而分区的文件名若以第一颗磁盘为例，则为 /dev/sda[1-128] 。除了实体磁盘之外，虚拟机的磁盘通常为 /dev/vd[a-p] 的格式。 若有使用到软件磁盘阵列的话，那还有 /dev/md[0-128] 的磁盘文件名。使用的是 LVM 时，文件名则为 /dev/VGNAME/LVNAME 等格式。 关于软件磁盘阵列与 LVM 我们会在后面继续介绍，这里主要介绍的以实体磁盘及虚拟磁盘为主喔！

*   /dev/sd[a-p][1-128]：为实体磁盘的磁盘文件名；
*   /dev/vd[a-d][1-128]：为虚拟磁盘的磁盘文件名

复习完物理组成后，来复习一下磁盘分区吧！如前所述，以前磁盘分区最小单位经常是柱面，但 CentOS 7 的分区软件， 已经将最小单位改成扇区了，所以容量大小的分区可以切的更细～此外，由于新的大容量磁盘大多得要使用 GPT 分区表才能够使用全部的容量， 因此过去那个 MBR 的传统磁盘分区表限制就不会存在了。不过，由于还是有小磁盘啊！因此， 你在处理分区的时候，还是得要先查询一下，你的分区是 MBR 的分区？还是 GPT 的分区？在第三章的 CentOS 7 安装中， 鸟哥建议过强制使用 GPT 分区喔！所以本章后续的动作，大多还是以 GPT 为主来介绍喔！旧的 MBR 相关限制回去看看第二章吧！

### 7.1.2 文件系统特性

我们都知道磁盘分区完毕后还需要进行格式化（format），之后操作系统才能够使用这个文件系统。 为什么需要进行“格式化”呢？这是因为每种操作系统所设置的文件属性/权限并不相同， 为了存放这些文件所需的数据，因此就需要将分区进行格式化，以成为操作系统能够利用的“文件系统格式（filesystem）”。

由此我们也能够知道，每种操作系统能够使用的文件系统并不相同。 举例来说，windows 98 以前的微软操作系统主要利用的文件系统是 FAT （或 FAT16），windows 2000 以后的版本有所谓的 NTFS 文件系统，至于 Linux 的正统文件系统则为 Ext2 （Linux second extended file system, ext2fs）这一个。此外，在默认的情况下，windows 操作系统是不会认识 Linux 的 Ext2 的。

传统的磁盘与文件系统之应用中，一个分区就是只能够被格式化成为一个文件系统，所以我们可以说一个 filesystem 就是一个 partition。但是由于新技术的利用，例如我们常听到的 LVM 与软件磁盘阵列（software raid）， 这些技术可以将一个分区格式化为多个文件系统（例如 LVM），也能够将多个分区合成一个文件系统（LVM, RAID）！ 所以说，目前我们在格式化时已经不再说成针对 partition 来格式化了， 通常我们可以称呼一个可被挂载的数据为一个文件系统而不是一个分区喔！

那么文件系统是如何运行的呢？这与操作系统的文件数据有关。较新的操作系统的文件数据除了文件实际内容外， 通常含有非常多的属性，例如 Linux 操作系统的文件权限（rwx）与文件属性（拥有者、群组、时间参数等）。 文件系统通常会将这两部份的数据分别存放在不同的区块，权限与属性放置到 inode 中，至于实际数据则放置到 data block 区块中。 另外，还有一个超级区块 （superblock） 会记录整个文件系统的整体信息，包括 inode 与 block 的总量、使用量、剩余量等。

每个 inode 与 block 都有编号，至于这三个数据的意义可以简略说明如下：

*   superblock：记录此 filesystem 的整体信息，包括 inode/block 的总量、使用量、剩余量， 以及文件系统的格式与相关信息等；
*   inode：记录文件的属性，一个文件占用一个 inode，同时记录此文件的数据所在的 block 号码；
*   block：实际记录文件的内容，若文件太大时，会占用多个 block 。

由于每个 inode 与 block 都有编号，而每个文件都会占用一个 inode ，inode 内则有文件数据放置的 block 号码。 因此，我们可以知道的是，如果能够找到文件的 inode 的话，那么自然就会知道这个文件所放置数据的 block 号码， 当然也就能够读出该文件的实际数据了。这是个比较有效率的作法，因为如此一来我们的磁盘就能够在短时间内读取出全部的数据， 读写的性能比较好啰。

我们将 inode 与 block 区块用图解来说明一下，如下图所示，文件系统先格式化出 inode 与 block 的区块，假设某一个文件的属性与权限数据是放置到 inode 4 号（下图较小方格内），而这个 inode 记录了文件数据的实际放置点为 2, 7, 13, 15 这四个 block 号码，此时我们的操作系统就能够据此来排列磁盘的读取顺序，可以一口气将四个 block 内容读出来！ 那么数据的读取就如同下图中的箭头所指定的模样了。

![inode/block 数据存取示意图](img/filesystem-1.jpg)图 7.1.1、inode/block 数据存取示意图

这种数据存取的方法我们称为索引式文件系统（indexed allocation）。那有没有其他的惯用文件系统可以比较一下啊？ 有的，那就是我们惯用的 U 盘（闪存），U 盘使用的文件系统一般为 FAT 格式。FAT 这种格式的文件系统并没有 inode 存在，所以 FAT 没有办法将这个文件的所有 block 在一开始就读取出来。每个 block 号码都记录在前一个 block 当中， 他的读取方式有点像下面这样：

![FAT 文件系统数据存取示意图](img/filesystem-2.jpg)图 7.1.2、FAT 文件系统数据存取示意图

上图中我们假设文件的数据依序写入 1->7->4->15 号这四个 block 号码中， 但这个文件系统没有办法一口气就知道四个 block 的号码，他得要一个一个的将 block 读出后，才会知道下一个 block 在何处。 如果同一个文件数据写入的 block 分散的太过厉害时，则我们的磁头将无法在磁盘转一圈就读到所有的数据， 因此磁盘就会多转好几圈才能完整的读取到这个文件的内容！

常常会听到所谓的“磁盘重组”吧？ 需要磁盘重组的原因就是文件写入的 block 太过于离散了，此时文件读取的性能将会变的很差所致。 这个时候可以通过磁盘重组将同一个文件所属的 blocks 汇整在一起，这样数据的读取会比较容易啊！ 想当然尔，FAT 的文件系统需要三不五时的磁盘重组一下，那么 Ext2 是否需要磁盘重整呢？

由于 Ext2 是索引式文件系统，基本上不太需要常常进行磁盘重组的。但是如果文件系统使用太久， 常常删除/编辑/新增文件时，那么还是可能会造成文件数据太过于离散的问题，此时或许会需要进行重整一下的。 不过，老实说，鸟哥倒是没有在 Linux 操作系统上面进行过 Ext2/Ext3 文件系统的磁盘重组说！似乎不太需要啦！^_^

### 7.1.3 Linux 的 EXT2 文件系统（inode）

在第五章当中我们介绍过 Linux 的文件除了原有的数据内容外，还含有非常多的权限与属性，这些权限与属性是为了保护每个使用者所拥有数据的隐密性。 而前一小节我们知道 filesystem 里面可能含有的 inode/block/superblock 等。为什么要谈这个呢？因为标准的 Linux 文件系统 Ext2 就是使用这种 inode 为基础的文件系统啦！

而如同前一小节所说的，inode 的内容在记录文件的权限与相关属性，至于 block 区块则是在记录文件的实际内容。 而且文件系统一开始就将 inode 与 block 规划好了，除非重新格式化（或者利用 resize2fs 等指令变更文件系统大小），否则 inode 与 block 固定后就不再变动。但是如果仔细考虑一下，如果我的文件系统高达数百 GB 时， 那么将所有的 inode 与 block 通通放置在一起将是很不智的决定，因为 inode 与 block 的数量太庞大，不容易管理。

为此之故，因此 Ext2 文件系统在格式化的时候基本上是区分为多个区块群组 （block group） 的，每个区块群组都有独立的 inode/block/superblock 系统。感觉上就好像我们在当兵时，一个营里面有分成数个连，每个连有自己的联络系统， 但最终都向营部回报连上最正确的信息一般！这样分成一群群的比较好管理啦！整个来说，Ext2 格式化后有点像下面这样：

![ext2 文件系统示意图](img/ext2_filesystem.jpg)图 7.1.3、ext2 文件系统示意图 [^([1])](#ps1)

在整体的规划当中，文件系统最前面有一个开机扇区（boot sector），这个开机扇区可以安装开机管理程序， 这是个非常重要的设计，因为如此一来我们就能够将不同的开机管理程序安装到个别的文件系统最前端，而不用覆盖整颗磁盘唯一的 MBR， 这样也才能够制作出多重开机的环境啊！至于每一个区块群组（block group）的六个主要内容说明如后：

*   data block （数据区块）

data block 是用来放置文件内容数据地方，在 Ext2 文件系统中所支持的 block 大小有 1K, 2K 及 4K 三种而已。在格式化时 block 的大小就固定了，且每个 block 都有编号，以方便 inode 的记录啦。 不过要注意的是，由于 block 大小的差异，会导致该文件系统能够支持的最大磁盘容量与最大单一文件大小并不相同。 因为 block 大小而产生的 Ext2 文件系统限制如下：[[2]](#ps2)

| Block 大小 | 1KB | 2KB | 4KB |
| --- | --- | --- | --- |
| 最大单一文件限制 | 16GB | 256GB | 2TB |
| 最大文件系统总容量 | 2TB | 8TB | 16TB |

你需要注意的是，虽然 Ext2 已经能够支持大于 2GB 以上的单一文件大小，不过某些应用程序依然使用旧的限制， 也就是说，某些程序只能够捉到小于 2GB 以下的文件而已，这就跟文件系统无关了！ 举例来说，鸟哥在环工方面的应用中有一套秀图软件称为 PAVE[[3]](#ps3)， 这套软件就无法捉到鸟哥在数值模式仿真后产生的大于 2GB 以上的文件！所以后来只能找更新的软件来取代它了！

除此之外 Ext2 文件系统的 block 还有什么限制呢？有的！基本限制如下：

*   原则上，block 的大小与数量在格式化完就不能够再改变了（除非重新格式化）；
*   每个 block 内最多只能够放置一个文件的数据；
*   承上，如果文件大于 block 的大小，则一个文件会占用多个 block 数量；
*   承上，若文件小于 block ，则该 block 的剩余容量就不能够再被使用了（磁盘空间会浪费）。

如上第四点所说，由于每个 block 仅能容纳一个文件的数据而已，因此如果你的文件都非常小，但是你的 block 在格式化时却选用最大的 4K 时，可能会产生一些容量的浪费喔！我们以下面的一个简单例题来算一下空间的浪费吧！

例题：假设你的 Ext2 文件系统使用 4K block ，而该文件系统中有 10000 个小文件，每个文件大小均为 50Bytes， 请问此时你的磁盘浪费多少容量？答：由于 Ext2 文件系统中一个 block 仅能容纳一个文件，因此每个 block 会浪费“ 4096 - 50 = 4046 （Byte）”， 系统中总共有一万个小文件，所有文件大小为：50 （Bytes） x 10000 = 488.3KBytes，但此时浪费的容量为：“ 4046 （Bytes） x 10000 = 38.6MBytes ”。想一想，不到 1MB 的总文件大小却浪费将近 40MB 的容量，且文件越多将造成越多的磁盘容量浪费。

什么情况会产生上述的状况呢？例如 BBS 网站的数据啦！如果 BBS 上面的数据使用的是纯文本来记载每篇留言， 而留言内容如果都写上“如题”时，想一想，是否就会产生很多小文件了呢？

好，既然大的 block 可能会产生较严重的磁盘容量浪费，那么我们是否就将 block 大小订为 1K 即可？ 这也不妥，因为如果 block 较小的话，那么大型文件将会占用数量更多的 block ，而 inode 也要记录更多的 block 号码，此时将可能导致文件系统不良的读写性能。

所以我们可以说，在您进行文件系统的格式化之前，请先想好该文件系统预计使用的情况。 以鸟哥来说，我的数值模式仿真平台随便一个文件都好几百 MB，那么 block 容量当然选择较大的！至少文件系统就不必记录太多的 block 号码，读写起来也比较方便啊！

![鸟哥的图示](img/vbird_face.gif "鸟哥的图示")

**Tips** 事实上，现在的磁盘容量都太大了！所以，大概大家都只会选择 4K 的 block 大小吧！呵呵！

*   inode table （inode 表格）

再来讨论一下 inode 这个玩意儿吧！如前所述 inode 的内容在记录文件的属性以及该文件实际数据是放置在哪几号 block 内！ 基本上，inode 记录的文件数据至少有下面这些：[[4]](#ps4)

*   该文件的存取模式（read/write/excute）；
*   该文件的拥有者与群组（owner/group）；
*   该文件的容量；
*   该文件创建或状态改变的时间（ctime）；
*   最近一次的读取时间（atime）；
*   最近修改的时间（mtime）；
*   定义文件特性的旗标（flag），如 SetUID...；
*   该文件真正内容的指向 （pointer）；

inode 的数量与大小也是在格式化时就已经固定了，除此之外 inode 还有些什么特色呢？

*   每个 inode 大小均固定为 128 Bytes （新的 ext4 与 xfs 可设置到 256 Bytes）；
*   每个文件都仅会占用一个 inode 而已；
*   承上，因此文件系统能够创建的文件数量与 inode 的数量有关；
*   系统读取文件时需要先找到 inode，并分析 inode 所记录的权限与使用者是否符合，若符合才能够开始实际读取 block 的内容。

我们约略来分析一下 EXT2 的 inode / block 与文件大小的关系好了。inode 要记录的数据非常多，但偏偏又只有 128Bytes 而已， 而 inode 记录一个 block 号码要花掉 4Byte ，假设我一个文件有 400MB 且每个 block 为 4K 时， 那么至少也要十万笔 block 号码的记录呢！inode 哪有这么多可记录的信息？为此我们的系统很聪明的将 inode 记录 block 号码的区域定义为 12 个直接，一个间接, 一个双间接与一个三间接记录区。这是啥？我们将 inode 的结构画一下好了。

![inode 结构示意图](img/inode.jpg)图 7.1.4、inode 结构示意图

上图最左边为 inode 本身 （128 Bytes），里面有 12 个直接指向 block 号码的对照，这 12 笔记录就能够直接取得 block 号码啦！ 至于所谓的间接就是再拿一个 block 来当作记录 block 号码的记录区，如果文件太大时， 就会使用间接的 block 来记录号码。如上图 7.1.4 当中间接只是拿一个 block 来记录额外的号码而已。 同理，如果文件持续长大，那么就会利用所谓的双间接，第一个 block 仅再指出下一个记录号码的 block 在哪里， 实际记录的在第二个 block 当中。依此类推，三间接就是利用第三层 block 来记录号码啦！

这样子 inode 能够指定多少个 block 呢？我们以较小的 1K block 来说明好了，可以指定的情况如下：

*   12 个直接指向： 12*1K=12K 由于是直接指向，所以总共可记录 12 笔记录，因此总额大小为如上所示；

*   间接： 256*1K=256K 每笔 block 号码的记录会花去 4Bytes，因此 1K 的大小能够记录 256 笔记录，因此一个间接可以记录的文件大小如上；

*   双间接： 256*256*1K=2562K 第一层 block 会指定 256 个第二层，每个第二层可以指定 256 个号码，因此总额大小如上；

*   三间接： 256*256*256*1K=2563K 第一层 block 会指定 256 个第二层，每个第二层可以指定 256 个第三层，每个第三层可以指定 256 个号码，因此总额大小如上；

*   总额：将直接、间接、双间接、三间接加总，得到 12 + 256 + 256*256 + 256*256*256 （K） = 16GB

此时我们知道当文件系统将 block 格式化为 1K 大小时，能够容纳的最大文件为 16GB，比较一下文件系统限制表的结果可发现是一致的！但这个方法不能用在 2K 及 4K block 大小的计算中， 因为大于 2K 的 block 将会受到 Ext2 文件系统本身的限制，所以计算的结果会不太符合之故。

![鸟哥的图示](img/vbird_face.gif "鸟哥的图示")

**Tips** 如果你的 Linux 依旧使用 Ext2/Ext3/Ext4 文件系统的话，例如鸟哥之前的 CentOS 6.x 系统，那么默认还是使用 Ext4 的文件系统喔！ Ext4 文件系统的 inode 容量已经可以扩大到 256Bytes 了，更大的 inode 容量，可以纪录更多的文件系统信息，包括新的 ACL 以及 SELinux 类型等， 当然，可以纪录的单一文件大小达 16TB 且单一文件系统总容量可达 1EB 哩！

*   Superblock （超级区块）

Superblock 是记录整个 filesystem 相关信息的地方， 没有 Superblock ，就没有这个 filesystem 了。他记录的信息主要有：

*   block 与 inode 的总量；
*   未使用与已使用的 inode / block 数量；
*   block 与 inode 的大小 （block 为 1, 2, 4K，inode 为 128Bytes 或 256Bytes）；
*   filesystem 的挂载时间、最近一次写入数据的时间、最近一次检验磁盘 （fsck） 的时间等文件系统的相关信息；
*   一个 valid bit 数值，若此文件系统已被挂载，则 valid bit 为 0 ，若未被挂载，则 valid bit 为 1 。

Superblock 是非常重要的，因为我们这个文件系统的基本信息都写在这里，因此，如果 superblock 死掉了， 你的文件系统可能就需要花费很多时间去挽救啦！一般来说， superblock 的大小为 1024Bytes。相关的 superblock 讯息我们等一下会以 dumpe2fs 指令来调用出来观察喔！

此外，每个 block group 都可能含有 superblock 喔！但是我们也说一个文件系统应该仅有一个 superblock 而已，那是怎么回事啊？ 事实上除了第一个 block group 内会含有 superblock 之外，后续的 block group 不一定含有 superblock ， 而若含有 superblock 则该 superblock 主要是做为第一个 block group 内 superblock 的备份咯，这样可以进行 superblock 的救援呢！

*   Filesystem Description （文件系统描述说明）

这个区段可以描述每个 block group 的开始与结束的 block 号码，以及说明每个区段 （superblock, bitmap, inodemap, data block） 分别介于哪一个 block 号码之间。这部份也能够用 dumpe2fs 来观察的。

*   block bitmap （区块对照表）

如果你想要新增文件时总会用到 block 吧！那你要使用哪个 block 来记录呢？当然是选择“空的 block ”来记录新文件的数据啰。 那你怎么知道哪个 block 是空的？这就得要通过 block bitmap 的辅助了。从 block bitmap 当中可以知道哪些 block 是空的，因此我们的系统就能够很快速的找到可使用的空间来处置文件啰。

同样的，如果你删除某些文件时，那么那些文件原本占用的 block 号码就得要释放出来， 此时在 block bitmap 当中相对应到该 block 号码的标志就得要修改成为“未使用中”啰！这就是 bitmap 的功能。

*   inode bitmap （inode 对照表）

这个其实与 block bitmap 是类似的功能，只是 block bitmap 记录的是使用与未使用的 block 号码， 至于 inode bitmap 则是记录使用与未使用的 inode 号码啰！

*   dumpe2fs： 查询 Ext 家族 superblock 信息的指令

了解了文件系统的概念之后，再来当然是观察这个文件系统啰！刚刚谈到的各部分数据都与 block 号码有关！ 每个区段与 superblock 的信息都可以使用 dumpe2fs 这个指令来查询的！不过很可惜的是，我们的 CentOS 7 现在是以 xfs 为默认文件系统， 所以目前你的系统应该无法使用 dumpe2fs 去查询任何文件系统的。没关系，鸟哥先找自己的一部机器来跟大家介绍， 你可以在后续的格式化内容讲完之后，自己切出一个 ext4 的文件系统去查询看看即可。鸟哥这块文件系统是 1GB 的容量，使用默认方式来进行格式化的， 观察的内容如下：

```
[root@study ~]# dumpe2fs [-bh] 设备文件名
选项与参数：
-b ：列出保留为坏轨的部分（一般用不到吧！？）
-h ：仅列出 superblock 的数据，不会列出其他的区段内容！

范例：鸟哥的一块 1GB ext4 文件系统内容
[root@study ~]# blkid   &lt;==这个指令可以叫出目前系统有被格式化的设备
/dev/vda1: LABEL="myboot" UUID="ce4dbf1b-2b3d-4973-8234-73768e8fd659" TYPE="xfs"
/dev/vda2: LABEL="myroot" UUID="21ad8b9a-aaad-443c-b732-4e2522e95e23" TYPE="xfs"
/dev/vda3: UUID="12y99K-bv2A-y7RY-jhEW-rIWf-PcH5-SaiApN" TYPE="LVM2_member"
/dev/vda5: UUID="e20d65d9-20d4-472f-9f91-cdcfb30219d6" TYPE="ext4"  &lt;==看到 ext4 了！

[root@study ~]# dumpe2fs /dev/vda5
dumpe2fs 1.42.9 （28-Dec-2013）
Filesystem volume name:   &lt;none&gt;           # 文件系统的名称（不一定会有）
Last mounted on:          &lt;not available&gt;  # 上一次挂载的目录位置
Filesystem UUID:          e20d65d9-20d4-472f-9f91-cdcfb30219d6
Filesystem magic number:  0xEF53           # 上方的 UUID 为 Linux 对设备的定义码
Filesystem revision #:    1 （dynamic）      # 下方的 features 为文件系统的特征数据
Filesystem features:      has_journal ext_attr resize_inode dir_index filetype extent 64bit 
 flex_bg sparse_super large_file huge_file uninit_bg dir_nlink extra_isize
Filesystem flags:         signed_directory_hash
Default mount options:    user_xattr acl   # 默认在挂载时会主动加上的挂载参数
Filesystem state:         clean            # 这块文件系统的状态为何，clean 是没问题
Errors behavior:          Continue
Filesystem OS type:       Linux
Inode count:              65536            # inode 的总数
Block count:              262144           # block 的总数
Reserved block count:     13107            # 保留的 block 总数
Free blocks:              249189           # 还有多少的 block 可用数量
Free inodes:              65525            # 还有多少的 inode 可用数量
First block:              0
Block size:               4096             # 单个 block 的容量大小
Fragment size:            4096
Group descriptor size:    64
....（中间省略）....
Inode size:               256              # inode 的容量大小！已经是 256 了喔！
....（中间省略）....
Journal inode:            8
Default directory hash:   half_md4
Directory Hash Seed:      3c2568b4-1a7e-44cf-95a2-c8867fb19fbc
Journal backup:           inode blocks
Journal features:         （none）
Journal size:             32M              # Journal 日志式数据的可供纪录总容量
Journal length:           8192
Journal sequence:         0x00000001
Journal start:            0

Group 0: （Blocks 0-32767）                  # 第一块 block group 位置
  Checksum 0x13be, unused inodes 8181
  Primary superblock at 0, Group descriptors at 1-1   # 主要 superblock 的所在喔！
  Reserved GDT blocks at 2-128
  Block bitmap at 129 （+129）, Inode bitmap at 145 （+145）
  Inode table at 161-672 （+161）                       # inode table 的所在喔！
  28521 free blocks, 8181 free inodes, 2 directories, 8181 unused inodes
  Free blocks: 142-144, 153-160, 4258-32767           # 下面两行说明剩余的容量有多少
  Free inodes: 12-8192
Group 1: （Blocks 32768-65535） [INODE_UNINIT]          # 后续为更多其他的 block group 喔！
....（下面省略）....
# 由于数据量非常的庞大，因此鸟哥将一些信息省略输出了！上表与你的屏幕会有点差异。
# 前半部在秀出 supberblock 的内容，包括标头名称（Label）以及 inode/block 的相关信息
# 后面则是每个 block group 的个别信息了！您可以看到各区段数据所在的号码！
# 也就是说，基本上所有的数据还是与 block 的号码有关就是了！很重要！ 
```

如上所示，利用 dumpe2fs 可以查询到非常多的信息，不过依内容主要可以区分为上半部是 superblock 内容， 下半部则是每个 block group 的信息了。从上面的表格中我们可以观察到鸟哥这个 /dev/vda5 规划的 block 为 4K， 第一个 block 号码为 0 号，且 block group 内的所有信息都以 block 的号码来表示的。 然后在 superblock 中还有谈到目前这个文件系统的可用 block 与 inode 数量喔！

至于 block group 的内容我们单纯看 Group0 信息好了。从上表中我们可以发现：

*   Group0 所占用的 block 号码由 0 到 32767 号，superblock 则在第 0 号的 block 区块内！
*   文件系统描述说明在第 1 号 block 中；
*   block bitmap 与 inode bitmap 则在 129 及 145 的 block 号码上。
*   至于 inode table 分布于 161-672 的 block 号码中！
*   由于 （1）一个 inode 占用 256 Bytes ，（2）总共有 672 - 161 + 1（161 本身） = 512 个 block 花在 inode table 上， （3）每个 block 的大小为 4096 Bytes（4K）。由这些数据可以算出 inode 的数量共有 512 * 4096 / 256 = 8192 个 inode 啦！
*   这个 Group0 目前可用的 block 有 28521 个，可用的 inode 有 8181 个；
*   剩余的 inode 号码为 12 号到 8192 号。

如果你对文件系统的详细信息还有更多想要了解的话，那么请参考本章最后一小节的介绍喔！ 否则文件系统看到这里对于基础认知您应该是已经相当足够啦！下面则是要探讨一下， 那么这个文件系统概念与实际的目录树应用有啥关连啊？

### 7.1.4 与目录树的关系

由前一小节的介绍我们知道在 Linux 系统下，每个文件（不管是一般文件还是目录文件）都会占用一个 inode ， 且可依据文件内容的大小来分配多个 block 给该文件使用。而由第五章的权限说明中我们知道目录的内容在记录文件名， 一般文件才是实际记录数据内容的地方。那么目录与文件在文件系统当中是如何记录数据的呢？基本上可以这样说：

*   目录

当我们在 Linux 下的文件系统创建一个目录时，文件系统会分配一个 inode 与至少一块 block 给该目录。其中，inode 记录该目录的相关权限与属性，并可记录分配到的那块 block 号码； 而 block 则是记录在这个目录下的文件名与该文件名占用的 inode 号码数据。也就是说目录所占用的 block 内容在记录如下的信息：

![目录占用的 block 记录的数据示意图](img/centos7_dir_block.jpg)图 7.1.5、记载于目录所属的 block 内的文件名与 inode 号码对应示意图

如果想要实际观察 root 主文件夹内的文件所占用的 inode 号码时，可以使用 ls -i 这个选项来处理：

```
[root@study ~]# ls -li
total 8
53735697 -rw-------. 1 root root 1816 May  4 17:57 anaconda-ks.cfg
53745858 -rw-r--r--. 1 root root 1864 May  4 18:01 initial-setup-ks.cfg 
```

由于每个人所使用的计算机并不相同，系统安装时选择的项目与 partition 都不一样，因此你的环境不可能与我的 inode 号码一模一样！上表的左边所列出的 inode 仅是鸟哥的系统所显示的结果而已！而由这个目录的 block 结果我们现在就能够知道， 当你使用“ ll / ”时，出现的目录几乎都是 1024 的倍数，为什么呢？因为每个 block 的数量都是 1K, 2K, 4K 嘛！ 看一下鸟哥的环境：

```
[root@study ~]# ll -d / /boot /usr/sbin /proc /sys
dr-xr-xr-x.  17 root root  4096 May  4 17:56 /         &lt;== 1 个 4K block
dr-xr-xr-x.   4 root root  4096 May  4 17:59 /boot     &lt;== 1 个 4K block
dr-xr-xr-x. 155 root root     0 Jun 15 15:43 /proc     &lt;== 这两个为内存内数据，不占磁盘容量
dr-xr-xr-x.  13 root root     0 Jun 15 23:43 /sys
dr-xr-xr-x.   2 root root 16384 May  4 17:55 /usr/sbin &lt;== 4 个 4K block 
```

由于鸟哥的根目录使用的 block 大小为 4K ，因此每个目录几乎都是 4K 的倍数。 其中由于 /usr/sbin 的内容比较复杂因此占用了 4 个 block ！至于奇怪的 /proc 我们在第五章就讲过该目录不占磁盘容量， 所以当然耗用的 block 就是 0 啰！

![鸟哥的图示](img/vbird_face.gif "鸟哥的图示")

**Tips** 由上面的结果我们知道目录并不只会占用一个 block 而已，也就是说： 在目录下面的文件数如果太多而导致一个 block 无法容纳的下所有的文件名与 inode 对照表时，Linux 会给予该目录多一个 block 来继续记录相关的数据；

*   文件：

当我们在 Linux 下的 ext2 创建一个一般文件时， ext2 会分配一个 inode 与相对于该文件大小的 block 数量给该文件。例如：假设我的一个 block 为 4 KBytes ，而我要创建一个 100 KBytes 的文件，那么 linux 将分配一个 inode 与 25 个 block 来储存该文件！ 但同时请注意，由于 inode 仅有 12 个直接指向，因此还要多一个 block 来作为区块号码的记录喔！

*   目录树读取：

好了，经过上面的说明你也应该要很清楚的知道 inode 本身并不记录文件名，文件名的记录是在目录的 block 当中。 因此在第五章文件与目录的权限说明中， 我们才会提到“新增/删除/更名文件名与目录的 w 权限有关”的特色！那么因为文件名是记录在目录的 block 当中， 因此当我们要读取某个文件时，就务必会经过目录的 inode 与 block ，然后才能够找到那个待读取文件的 inode 号码， 最终才会读到正确的文件的 block 内的数据。

由于目录树是由根目录开始读起，因此系统通过挂载的信息可以找到挂载点的 inode 号码，此时就能够得到根目录的 inode 内容，并依据该 inode 读取根目录的 block 内的文件名数据，再一层一层的往下读到正确的文件名。举例来说，如果我想要读取 /etc/passwd 这个文件时，系统是如何读取的呢？

```
[root@study ~]# ll -di / /etc /etc/passwd
 128 dr-xr-xr-x.  17 root root 4096 May  4 17:56 /
33595521 drwxr-xr-x. 131 root root 8192 Jun 17 00:20 /etc
36628004 -rw-r--r--.   1 root root 2092 Jun 17 00:20 /etc/passwd 
```

在鸟哥的系统上面与 /etc/passwd 有关的目录与文件数据如上表所示，该文件的读取流程为（假设读取者身份为 dmtsai 这个一般身份使用者）：

1.  / 的 inode： 通过挂载点的信息找到 inode 号码为 128 的根目录 inode，且 inode 规范的权限让我们可以读取该 block 的内容（有 r 与 x） ；

2.  / 的 block： 经过上个步骤取得 block 的号码，并找到该内容有 etc/ 目录的 inode 号码 （33595521）；

3.  etc/ 的 inode： 读取 33595521 号 inode 得知 dmtsai 具有 r 与 x 的权限，因此可以读取 etc/ 的 block 内容；

4.  etc/ 的 block： 经过上个步骤取得 block 号码，并找到该内容有 passwd 文件的 inode 号码 （36628004）；

5.  passwd 的 inode： 读取 36628004 号 inode 得知 dmtsai 具有 r 的权限，因此可以读取 passwd 的 block 内容；

6.  passwd 的 block： 最后将该 block 内容的数据读出来。

7.  filesystem 大小与磁盘读取性能：

另外，关于文件系统的使用效率上，当你的一个文件系统规划的很大时，例如 100GB 这么大时， 由于磁盘上面的数据总是来来去去的，所以，整个文件系统上面的文件通常无法连续写在一起（block 号码不会连续的意思）， 而是填入式的将数据填入没有被使用的 block 当中。如果文件写入的 block 真的分的很散， 此时就会有所谓的文件数据离散的问题发生了。

如前所述，虽然我们的 ext2 在 inode 处已经将该文件所记录的 block 号码都记上了， 所以数据可以一次性读取，但是如果文件真的太过离散，确实还是会发生读取效率低落的问题。 因为磁头还是得要在整个文件系统中来来去去的频繁读取！ 果真如此，那么可以将整个 filesystme 内的数据全部复制出来，将该 filesystem 重新格式化， 再将数据给他复制回去即可解决这个问题。

此外，如果 filesystem 真的太大了，那么当一个文件分别记录在这个文件系统的最前面与最后面的 block 号码中， 此时会造成磁盘的机械手臂移动幅度过大，也会造成数据读取性能的低落。而且磁头在搜寻整个 filesystem 时， 也会花费比较多的时间去搜寻！因此， partition 的规划并不是越大越好， 而是真的要针对您的主机用途来进行规划才行！^_^

### 7.1.5 EXT2/EXT3/EXT4 文件的存取与日志式文件系统的功能

上一小节谈到的仅是读取而已，那么如果是新建一个文件或目录时，我们的文件系统是如何处理的呢？ 这个时候就得要 block bitmap 及 inode bitmap 的帮忙了！假设我们想要新增一个文件，此时文件系统的行为是：

1.  先确定使用者对于欲新增文件的目录是否具有 w 与 x 的权限，若有的话才能新增；
2.  根据 inode bitmap 找到没有使用的 inode 号码，并将新文件的权限/属性写入；
3.  根据 block bitmap 找到没有使用中的 block 号码，并将实际的数据写入 block 中，且更新 inode 的 block 指向数据；
4.  将刚刚写入的 inode 与 block 数据同步更新 inode bitmap 与 block bitmap，并更新 superblock 的内容。

一般来说，我们将 inode table 与 data block 称为数据存放区域，至于其他例如 superblock、 block bitmap 与 inode bitmap 等区段就被称为 metadata （中介数据） 啰，因为 superblock, inode bitmap 及 block bitmap 的数据是经常变动的，每次新增、移除、编辑时都可能会影响到这三个部分的数据，因此才被称为中介数据的啦。

*   数据的不一致 （Inconsistent） 状态

在一般正常的情况下，上述的新增动作当然可以顺利的完成。但是如果有个万一怎么办？ 例如你的文件在写入文件系统时，因为不知名原因导致系统中断（例如突然的停电啊、 系统核心发生错误啊～等等的怪事发生时），所以写入的数据仅有 inode table 及 data block 而已， 最后一个同步更新中介数据的步骤并没有做完，此时就会发生 metadata 的内容与实际数据存放区产生不一致 （Inconsistent） 的情况了。

既然有不一致当然就得要克服！在早期的 Ext2 文件系统中，如果发生这个问题， 那么系统在重新开机的时候，就会借由 Superblock 当中记录的 valid bit （是否有挂载） 与 filesystem state （clean 与否） 等状态来判断是否强制进行数据一致性的检查！若有需要检查时则以 e2fsck 这支程序来进行的。

不过，这样的检查真的是很费时～因为要针对 metadata 区域与实际数据存放区来进行比对， 呵呵～得要搜寻整个 filesystem 呢～如果你的文件系统有 100GB 以上，而且里面的文件数量又多时， 哇！系统真忙碌～而且在对 Internet 提供服务的服务器主机上面， 这样的检查真的会造成主机复原时间的拉长～真是麻烦～这也就造成后来所谓日志式文件系统的兴起了。

*   日志式文件系统 （Journaling filesystem）

为了避免上述提到的文件系统不一致的情况发生，因此我们的前辈们想到一个方式， 如果在我们的 filesystem 当中规划出一个区块，该区块专门在记录写入或修订文件时的步骤， 那不就可以简化一致性检查的步骤了？也就是说：

1.  预备：当系统要写入一个文件时，会先在日志记录区块中纪录某个文件准备要写入的信息；
2.  实际写入：开始写入文件的权限与数据；开始更新 metadata 的数据；
3.  结束：完成数据与 metadata 的更新后，在日志记录区块当中完成该文件的纪录。

在这样的程序当中，万一数据的纪录过程当中发生了问题，那么我们的系统只要去检查日志记录区块， 就可以知道哪个文件发生了问题，针对该问题来做一致性的检查即可，而不必针对整块 filesystem 去检查， 这样就可以达到快速修复 filesystem 的能力了！这就是日志式文件最基础的功能啰～

那么我们的 ext2 可达到这样的功能吗？当然可以啊！ 就通过 ext3/ext4 即可！ ext3/ext4 是 ext2 的升级版本，并且可向下相容 ext2 版本呢！ 所以啰，目前我们才建议大家，可以直接使用 ext4 这个 filesystem 啊！ 如果你还记得 dumpe2fs 输出的讯息，可以发现 superblock 里面含有下面这样的信息：

```
Journal inode:            8
Journal backup:           inode blocks
Journal features:         （none）
Journal size:             32M
Journal length:           8192
Journal sequence:         0x00000001 
```

看到了吧！通过 inode 8 号记录 journal 区块的 block 指向，而且具有 32MB 的容量在处理日志呢！ 这样对于所谓的日志式文件系统有没有比较有概念一点呢？^_^。

### 7.1.6 Linux 文件系统的运行

我们现在知道了目录树与文件系统的关系了，但是由第零章的内容我们也知道， 所有的数据都得要载入到内存后 CPU 才能够对该数据进行处理。想一想，如果你常常编辑一个好大的文件， 在编辑的过程中又频繁的要系统来写入到磁盘中，由于磁盘写入的速度要比内存慢很多， 因此你会常常耗在等待磁盘的写入/读取上。真没效率！

为了解决这个效率的问题，因此我们的 Linux 使用的方式是通过一个称为非同步处理 （asynchronously） 的方式。所谓的非同步处理是这样的：

当系统载入一个文件到内存后，如果该文件没有被更动过，则在内存区段的文件数据会被设置为干净（clean）的。 但如果内存中的文件数据被更改过了（例如你用 nano 去编辑过这个文件），此时该内存中的数据会被设置为脏的 （Dirty）。此时所有的动作都还在内存中执行，并没有写入到磁盘中！ 系统会不定时的将内存中设置为“Dirty”的数据写回磁盘，以保持磁盘与内存数据的一致性。 你也可以利用第四章谈到的 sync 指令来手动强迫写入磁盘。

我们知道内存的速度要比磁盘快的多，因此如果能够将常用的文件放置到内存当中，这不就会增加系统性能吗？ 没错！是有这样的想法！因此我们 Linux 系统上面文件系统与内存有非常大的关系喔：

*   系统会将常用的文件数据放置到内存的缓冲区，以加速文件系统的读/写；
*   承上，因此 Linux 的实体内存最后都会被用光！这是正常的情况！可加速系统性能；
*   你可以手动使用 sync 来强迫内存中设置为 Dirty 的文件回写到磁盘中；
*   若正常关机时，关机指令会主动调用 sync 来将内存的数据回写入磁盘内；
*   但若不正常关机（如跳电、死机或其他不明原因），由于数据尚未回写到磁盘内， 因此重新开机后可能会花很多时间在进行磁盘检验，甚至可能导致文件系统的损毁（非磁盘损毁）。

### 7.1.7 挂载点的意义 （mount point）

每个 filesystem 都有独立的 inode / block / superblock 等信息，这个文件系统要能够链接到目录树才能被我们使用。 将文件系统与目录树结合的动作我们称为“挂载”。 关于挂载的一些特性我们在第二章稍微提过， 重点是：挂载点一定是目录，该目录为进入该文件系统的入口。 因此并不是你有任何文件系统都能使用，必须要“挂载”到目录树的某个目录后，才能够使用该文件系统的。

举例来说，如果你是依据鸟哥的方法安装你的 CentOS 7.x 的话， 那么应该会有三个挂载点才是，分别是 /, /boot, /home 三个 （鸟哥的系统上对应的设备文件名为 LVM, LVM, /dev/vda2）。 那如果观察这三个目录的 inode 号码时，我们可以发现如下的情况：

```
[root@study ~]# ls -lid / /boot /home
128 dr-xr-xr-x. 17 root root 4096 May  4 17:56 /
128 dr-xr-xr-x.  4 root root 4096 May  4 17:59 /boot
128 drwxr-xr-x.  5 root root   41 Jun 17 00:20 /home 
```

看到了吧！由于 XFS filesystem 最顶层的目录之 inode 一般为 128 号，因此可以发现 /, /boot, /home 为三个不同的 filesystem 啰！ （因为每一行的文件属性并不相同，且三个目录的挂载点也均不相同之故。） 我们在第六章一开始的路径中曾经提到根目录下的 . 与 .. 是相同的东西， 因为权限是一模一样嘛！如果使用文件系统的观点来看，同一个 filesystem 的某个 inode 只会对应到一个文件内容而已（因为一个文件占用一个 inode 之故）， 因此我们可以通过判断 inode 号码来确认不同文件名是否为相同的文件喔！所以可以这样看：

```
[root@study ~]# ls -ild /  /.  /..
128 dr-xr-xr-x. 17 root root 4096 May  4 17:56 /
128 dr-xr-xr-x. 17 root root 4096 May  4 17:56 /.
128 dr-xr-xr-x. 17 root root 4096 May  4 17:56 /.. 
```

上面的信息中由于挂载点均为 / ，因此三个文件 （/, /., /..） 均在同一个 filesystem 内，而这三个文件的 inode 号码均为 128 号，因此这三个文件名都指向同一个 inode 号码，当然这三个文件的内容也就完全一模一样了！ 也就是说，根目录的上层 （/..） 就是他自己！这么说，看的懂了吗？ ^_^

### 7.1.8 其他 Linux 支持的文件系统与 VFS

虽然 Linux 的标准文件系统是 ext2 ，且还有增加了日志功能的 ext3/ext4 ，事实上，Linux 还有支持很多文件系统格式的， 尤其是最近这几年推出了好几种速度很快的日志式文件系统，包括 SGI 的 XFS 文件系统， 可以适用更小型文件的 Reiserfs 文件系统，以及 Windows 的 FAT 文件系统等等， 都能够被 Linux 所支持喔！常见的支持文件系统有：

*   传统文件系统：ext2 / minix / MS-DOS / FAT （用 vfat 模块） / iso9660 （光盘）等等；
*   日志式文件系统： ext3 /ext4 / ReiserFS / Windows' NTFS / IBM's JFS / SGI's XFS / ZFS
*   网络文件系统： NFS / SMBFS

想要知道你的 Linux 支持的文件系统有哪些，可以察看下面这个目录：

```
[root@study ~]# ls -l /lib/modules/$（uname -r）/kernel/fs 
```

系统目前已载入到内存中支持的文件系统则有：

```
[root@study ~]# cat /proc/filesystems 
```

*   Linux VFS （Virtual Filesystem Switch）

了解了我们使用的文件系统之后，再来则是要提到，那么 Linux 的核心又是如何管理这些认识的文件系统呢？ 其实，整个 Linux 的系统都是通过一个名为 Virtual Filesystem Switch 的核心功能去读取 filesystem 的。 也就是说，整个 Linux 认识的 filesystem 其实都是 VFS 在进行管理，我们使用者并不需要知道每个 partition 上头的 filesystem 是什么～ VFS 会主动的帮我们做好读取的动作呢～

假设你的 / 使用的是 /dev/hda1 ，用 ext3 ，而 /home 使用 /dev/hda2 ，用 reiserfs ， 那么你取用 /home/dmtsai/.bashrc 时，有特别指定要用的什么文件系统的模块来读取吗？ 应该是没有吧！这个就是 VFS 的功能啦！通过这个 VFS 的功能来管理所有的 filesystem， 省去我们需要自行设置读取文件系统的定义啊～方便很多！整个 VFS 可以约略用下图来说明：

![VFS 文件系统的示意图](img/centos7_vfs.gif)图 7.1.6、VFS 文件系统的示意图

老实说，文件系统真的不好懂！ 如果你想要对文件系统有更深入的了解，文末的相关链接[[5]](#ps5)务必要参考参考才好喔！

### 7.1.9 XFS 文件系统简介

CentOS 7 开始，默认的文件系统已经由原本的 EXT4 变成了 XFS 文件系统了！为啥 CentOS 要舍弃对 Linux 支持度最完整的 EXT 家族而改用 XFS 呢？ 这是有一些原因存在的。

*   EXT 家族当前较伤脑筋的地方：支持度最广，但格式化超慢！

Ext 文件系统家族对于文件格式化的处理方面，采用的是预先规划出所有的 inode/block/meta data 等数据，未来系统可以直接取用， 不需要再进行动态配置的作法。这个作法在早期磁盘容量还不大的时候还算 OK 没啥问题，但时至今日，磁盘容量越来越大，连传统的 MBR 都已经被 GPT 所取代，连我们这些老人家以前听到的超大 TB 容量也已经不够看了！现在都已经说到 PB 或 EB 以上容量了呢！那你可以想像得到，当你的 TB 以上等级的传统 ext 家族文件系统在格式化的时候，光是系统要预先分配 inode 与 block 就消耗你好多好多的人类时间了...

![鸟哥的图示](img/vbird_face.gif "鸟哥的图示")

**Tips** 之前格式化过一个 70 TB 以上的磁盘阵列成为 ext4 文件系统，按下格式化，去喝了咖啡、吃了便当才回来看做完了没有... 所以，后来立刻改成 xfs 文件系统了。

另外，由于虚拟化的应用越来越广泛，而作为虚拟化磁盘来源的巨型文件 （单一文件好几个 GB 以上！） 也就越来越常见了。 这种巨型文件在处理上需要考虑到性能问题，否则虚拟磁盘的效率就会不太好看。因此，从 CentOS 7.x 开始， 文件系统已经由默认的 Ext4 变成了 xfs 这一个较适合大容量磁盘与巨型文件性能较佳的文件系统了。

![鸟哥的图示](img/vbird_face.gif "鸟哥的图示")

**Tips** 其实鸟哥有几组虚拟计算机教室服务器系统，里面跑的确实是 EXT4 文件系统，老实说，并不觉得比 xfs 慢！所以，对鸟哥来说， 性能并不是主要改变文件系统的考虑！对于文件系统的复原速度、创建速度，可能才是鸟哥改换成 xfs 的思考点。

*   XFS 文件系统的配置 [[6]](#ps6)

基本上 xfs 就是一个日志式文件系统，而 CentOS 7.x 拿它当默认的文件系统，自然就是因为最早之前，这个 xfs 就是被开发来用于大容量磁盘以及高性能文件系统之用， 因此，相当适合现在的系统环境。此外，几乎所有 Ext4 文件系统有的功能， xfs 都可以具备！也因此在本小节前几部份谈到文件系统时， 其实大部份的操作依旧是在 xfs 文件系统环境下介绍给各位的哩！

xfs 文件系统在数据的分佈上，主要规划为三个部份，一个数据区 （data section）、一个文件系统活动登录区 （log section）以及一个实时运行区 （realtime section）。 这三个区域的数据内容如下：

*   数据区 （data section）

基本上，数据区就跟我们之前谈到的 ext 家族一样，包括 inode/data block/superblock 等数据，都放置在这个区块。 这个数据区与 ext 家族的 block group 类似，也是分为多个储存区群组 （allocation groups） 来分别放置文件系统所需要的数据。 每个储存区群组都包含了 （1）整个文件系统的 superblock、 （2）剩余空间的管理机制、 （3）inode 的分配与追踪。此外，inode 与 block 都是系统需要用到时， 这才动态配置产生，所以格式化动作超级快！

另外，与 ext 家族不同的是， xfs 的 block 与 inode 有多种不同的容量可供设置，block 容量可由 512Bytes ~ 64K 调配，不过，Linux 的环境下， 由于内存控制的关系 （分页档 pagesize 的容量之故），因此最高可以使用的 block 大小为 4K 而已！（鸟哥尝试格式化 block 成为 16K 是没问题的，不过，Linux 核心不给挂载！ 所以格式化完成后也无法使用啦！） 至于 inode 容量可由 256Bytes 到 2M 这么大！不过，大概还是保留 256Bytes 的默认值就很够用了！

![鸟哥的图示](img/vbird_face.gif "鸟哥的图示")

**Tips** 总之， xfs 的这个数据区的储存区群组 （allocation groups, AG），你就将它想成是 ext 家族的 block 群组 （block groups） 就对了！本小节之前讲的都可以在这个区块内使用。 只是 inode 与 block 是动态产生，并非一开始于格式化就完成配置的。

*   文件系统活动登录区 （log section）

在登录区这个区域主要被用来纪录文件系统的变化，其实有点像是日志区啦！文件的变化会在这里纪录下来，直到该变化完整的写入到数据区后， 该笔纪录才会被终结。如果文件系统因为某些缘故 （例如最常见的停电） 而损毁时，系统会拿这个登录区块来进行检验，看看系统挂掉之前， 文件系统正在运行些啥动作，借以快速的修复文件系统。

因为系统所有动作的时候都会在这个区块做个纪录，因此这个区块的磁盘活动是相当频繁的！xfs 设计有点有趣，在这个区域中， 你可以指定外部的磁盘来作为 xfs 文件系统的日志区块喔！例如，你可以将 SSD 磁盘作为 xfs 的登录区，这样当系统需要进行任何活动时， 就可以更快速的进行工作！相当有趣！

*   实时运行区 （realtime section）

当有文件要被创建时，xfs 会在这个区段里面找一个到数个的 extent 区块，将文件放置在这个区块内，等到分配完毕后，再写入到 data section 的 inode 与 block 去！ 这个 extent 区块的大小得要在格式化的时候就先指定，最小值是 4K 最大可到 1G。一般非磁盘阵列的磁盘默认为 64K 容量，而具有类似磁盘阵列的 stripe 情况下，则建议 extent 设置为与 stripe 一样大较佳。这个 extent 最好不要乱动，因为可能会影响到实体磁盘的性能喔。

*   XFS 文件系统的描述数据观察

刚刚讲了这么多，完全无法理会耶～有没有像 EXT 家族的 dumpe2fs 去观察 superblock 内容的相关指令可以查阅呢？有啦！可以使用 xfs_info 去观察的！ 详细的指令作法可以参考如下：

```
[root@study ~]# xfs_info 挂载点&#124;设备文件名

范例一：找出系统 /boot 这个挂载点下面的文件系统的 superblock 纪录
[root@study ~]# df -T /boot
Filesystem     Type 1K-blocks   Used Available Use% Mounted on
/dev/vda2      xfs    1038336 133704    904632  13% /boot
# 没错！可以看得出来是 xfs 文件系统的！来观察一下内容吧！

[root@study ~]# xfs_info /dev/vda2
1  meta-data=/dev/vda2         isize=256    agcount=4, agsize=65536 blks
2           =                  sectsz=512   attr=2, projid32bit=1
3           =                  crc=0        finobt=0
4  data     =                  bsize=4096   blocks=262144, imaxpct=25
5           =                  sunit=0      swidth=0 blks
6  naming   =version 2         bsize=4096   ascii-ci=0 ftype=0
7  log      =internal          bsize=4096   blocks=2560, version=2
8           =                  sectsz=512   sunit=0 blks, lazy-count=1
9  realtime =none              extsz=4096   blocks=0, rtextents=0 
```

上面的输出讯息可以这样解释：

*   第 1 行里面的 isize 指的是 inode 的容量，每个有 256Bytes 这么大。至于 agcount 则是前面谈到的储存区群组 （allocation group） 的个数，共有 4 个， agsize 则是指每个储存区群组具有 65536 个 block 。配合第 4 行的 block 设置为 4K，因此整个文件系统的容量应该就是 4*65536*4K 这么大！
*   第 2 行里面 sectsz 指的是逻辑扇区 （sector） 的容量设置为 512Bytes 这么大的意思。
*   第 4 行里面的 bsize 指的是 block 的容量，每个 block 为 4K 的意思，共有 262144 个 block 在这个文件系统内。
*   第 5 行里面的 sunit 与 swidth 与磁盘阵列的 stripe 相关性较高。这部份我们下面格式化的时候会举一个例子来说明。
*   第 7 行里面的 internal 指的是这个登录区的位置在文件系统内，而不是外部设备的意思。且占用了 4K * 2560 个 block，总共约 10M 的容量。
*   第 9 行里面的 realtime 区域，里面的 extent 容量为 4K。不过目前没有使用。

由于我们并没有使用磁盘阵列，因此上头这个设备里头的 sunit 与 extent 就没有额外的指定特别的值。根据 xfs（5） 的说明，这两个值会影响到你的文件系统性能， 所以格式化的时候要特别留意喔！上面的说明大致上看看即可，比较重要的部份已经用特殊字体圈起来，你可以瞧一瞧先！

# 7.2 文件系统的简单操作

## 7.2 文件系统的简单操作

稍微了解了文件系统后，再来我们得要知道如何查询整体文件系统的总容量与每个目录所占用的容量啰！ 此外，前两章谈到的文件类型中尚未讲的很清楚的链接文件 （Link file） 也会在这一小节当中介绍的。

### 7.2.1 磁盘与目录的容量

现在我们知道磁盘的整体数据是在 superblock 区块中，但是每个各别文件的容量则在 inode 当中记载的。 那在命令行下面该如何叫出这几个数据呢？下面就让我们来谈一谈这两个指令：

*   df：列出文件系统的整体磁盘使用量；
*   du：评估文件系统的磁盘使用量（常用在推估目录所占容量）

*   df

```
[root@study ~]# df [-ahikHTm] [目录或文件名]
选项与参数：
-a  ：列出所有的文件系统，包括系统特有的 /proc 等文件系统；
-k  ：以 KBytes 的容量显示各文件系统；
-m  ：以 MBytes 的容量显示各文件系统；
-h  ：以人们较易阅读的 GBytes, MBytes, KBytes 等格式自行显示；
-H  ：以 M=1000K 取代 M=1024K 的进位方式；
-T  ：连同该 partition 的 filesystem 名称 （例如 xfs） 也列出；
-i  ：不用磁盘容量，而以 inode 的数量来显示

范例一：将系统内所有的 filesystem 列出来！
[root@study ~]# df
Filesystem              1K-blocks    Used Available Use% Mounted on
/dev/mapper/centos-root  10475520 3409408   7066112  33% /
devtmpfs                   627700       0    627700   0% /dev
tmpfs                      637568      80    637488   1% /dev/shm
tmpfs                      637568   24684    612884   4% /run
tmpfs                      637568       0    637568   0% /sys/fs/cgroup
/dev/mapper/centos-home   5232640   67720   5164920   2% /home
/dev/vda2                 1038336  133704    904632  13% /boot
# 在 Linux 下面如果 df 没有加任何选项，那么默认会将系统内所有的 
# （不含特殊内存内的文件系统与 swap） 都以 1 KBytes 的容量来列出来！
# 至于那个 /dev/shm 是与内存有关的挂载，先不要理他！ 
```

先来说明一下范例一所输出的结果讯息为：

*   Filesystem：代表该文件系统是在哪个 partition ，所以列出设备名称；
*   1k-blocks：说明下面的数字单位是 1KB 呦！可利用 -h 或 -m 来改变容量；
*   Used：顾名思义，就是使用掉的磁盘空间啦！
*   Available：也就是剩下的磁盘空间大小；
*   Use%：就是磁盘的使用率啦！如果使用率高达 90% 以上时， 最好需要注意一下了，免得容量不足造成系统问题喔！（例如最容易被灌爆的 /var/spool/mail 这个放置邮件的磁盘）
*   Mounted on：就是磁盘挂载的目录所在啦！（挂载点啦！）

```
范例二：将容量结果以易读的容量格式显示出来
[root@study ~]# df -h
Filesystem               Size  Used Avail Use% Mounted on
/dev/mapper/centos-root   10G  3.3G  6.8G  33% /
devtmpfs                 613M     0  613M   0% /dev
tmpfs                    623M   80K  623M   1% /dev/shm
tmpfs                    623M   25M  599M   4% /run
tmpfs                    623M     0  623M   0% /sys/fs/cgroup
/dev/mapper/centos-home  5.0G   67M  5.0G   2% /home
/dev/vda2               1014M  131M  884M  13% /boot
# 不同于范例一，这里会以 G/M 等容量格式显示出来，比较容易看啦！

范例三：将系统内的所有特殊文件格式及名称都列出来
[root@study ~]# df -aT
Filesystem              Type        1K-blocks    Used Available Use% Mounted on
rootfs                  rootfs       10475520 3409368   7066152  33% /
proc                    proc                0       0         0    - /proc
sysfs                   sysfs               0       0         0    - /sys
devtmpfs                devtmpfs       627700       0    627700   0% /dev
securityfs              securityfs          0       0         0    - /sys/kernel/security
tmpfs                   tmpfs          637568      80    637488   1% /dev/shm
devpts                  devpts              0       0         0    - /dev/pts
tmpfs                   tmpfs          637568   24684    612884   4% /run
tmpfs                   tmpfs          637568       0    637568   0% /sys/fs/cgroup
.....（中间省略）.....
/dev/mapper/centos-root xfs          10475520 3409368   7066152  33% /
selinuxfs               selinuxfs           0       0         0    - /sys/fs/selinux
.....（中间省略）.....
/dev/mapper/centos-home xfs           5232640   67720   5164920   2% /home
/dev/vda2               xfs           1038336  133704    904632  13% /boot
binfmt_misc             binfmt_misc         0       0         0    - /proc/sys/fs/binfmt_misc
# 系统里面其实还有很多特殊的文件系统存在的。那些比较特殊的文件系统几乎
# 都是在内存当中，例如 /proc 这个挂载点。因此，这些特殊的文件系统
# 都不会占据磁盘空间喔！ ^_^

范例四：将 /etc 下面的可用的磁盘容量以易读的容量格式显示
[root@study ~]# df -h /etc
Filesystem               Size  Used Avail Use% Mounted on
/dev/mapper/centos-root   10G  3.3G  6.8G  33% /
# 这个范例比较有趣一点啦，在 df 后面加上目录或者是文件时， df
# 会自动的分析该目录或文件所在的 partition ，并将该 partition 的容量显示出来，
# 所以，您就可以知道某个目录下面还有多少容量可以使用了！ ^_^

范例五：将目前各个 partition 当中可用的 inode 数量列出
[root@study ~]# df -ih 
Filesystem              Inodes IUsed IFree IUse% Mounted on
/dev/mapper/centos-root    10M  108K  9.9M    2% /
devtmpfs                  154K   397  153K    1% /dev
tmpfs                     156K     5  156K    1% /dev/shm
tmpfs                     156K   497  156K    1% /run
tmpfs                     156K    13  156K    1% /sys/fs/cgroup
# 这个范例则主要列出可用的 inode 剩余量与总容量。分析一下与范例一的关系，
# 你可以清楚的发现到，通常 inode 的数量剩余都比 block 还要多呢 
```

由于 df 主要读取的数据几乎都是针对一整个文件系统，因此读取的范围主要是在 Superblock 内的信息， 所以这个指令显示结果的速度非常的快速！在显示的结果中你需要特别留意的是那个根目录的剩余容量！ 因为我们所有的数据都是由根目录衍生出来的，因此当根目录的剩余容量剩下 0 时，那你的 Linux 可能就问题很大了。

![鸟哥的图示](img/vbird_face.gif "鸟哥的图示")

**Tips** 说个陈年老笑话！鸟哥还在念书时，别的研究室有个管理 Sun 工作站的研究生发现， 他的磁盘明明还有好几 GB ，但是就是没有办法将光盘内几 MB 的数据 copy 进去， 他就去跟老板讲说机器坏了！嘿！明明才来维护过几天而已为何会坏了！ 结果他老板就将维护商叫来骂了 2 小时左右吧！

后来，维护商发现原来磁盘的“总空间”还有很多， 只是某个分区填满了，偏偏该研究生就是要将数据 copy 去那个分区！呵呵！ 后来那个研究生就被命令“再也不许碰 Sun 主机”了～～

另外需要注意的是，如果使用 -a 这个参数时，系统会出现 /proc 这个挂载点，但是里面的东西都是 0 ，不要紧张！ /proc 的东西都是 Linux 系统所需要载入的系统数据，而且是挂载在“内存当中”的， 所以当然没有占任何的磁盘空间啰！

至于那个 /dev/shm/ 目录，其实是利用内存虚拟出来的磁盘空间，通常是总实体内存的一半！ 由于是通过内存仿真出来的磁盘，因此你在这个目录下面创建任何数据文件时，存取速度是非常快速的！（在内存内工作） 不过，也由于他是内存仿真出来的，因此这个文件系统的大小在每部主机上都不一样，而且创建的东西在下次开机时就消失了！ 因为是在内存中嘛！

*   du

```
[root@study ~]# du [-ahskm] 文件或目录名称
选项与参数：
-a  ：列出所有的文件与目录容量，因为默认仅统计目录下面的文件量而已。
-h  ：以人们较易读的容量格式 （G/M） 显示；
-s  ：列出总量而已，而不列出每个各别的目录占用容量；
-S  ：不包括子目录下的总计，与 -s 有点差别。
-k  ：以 KBytes 列出容量显示；
-m  ：以 MBytes 列出容量显示；

范例一：列出目前目录下的所有文件大小
[root@study ~]# du
4       ./.cache/dconf  &lt;==每个目录都会列出来
4       ./.cache/abrt
8       ./.cache
....（中间省略）....
0       ./test4
4       ./.ssh          &lt;==包括隐藏文件的目录
76      .               &lt;==这个目录（.）所占用的总量
# 直接输入 du 没有加任何选项时，则 du 会分析“目前所在目录”
# 的文件与目录所占用的磁盘空间。但是，实际显示时，仅会显示目录容量（不含文件），
# 因此 . 目录有很多文件没有被列出来，所以全部的目录相加不会等于 . 的容量喔！
# 此外，输出的数值数据为 1K 大小的容量单位。

范例二：同范例一，但是将文件的容量也列出来
[root@study ~]# du -a
4       ./.bash_logout         &lt;==有文件的列表了
4       ./.bash_profile
4       ./.bashrc
....（中间省略）....
4       ./.ssh/known_hosts
4       ./.ssh
76      .

范例三：检查根目录下面每个目录所占用的容量
[root@study ~]# du -sm /*
0       /bin
99      /boot
....（中间省略）....
du: cannot access ‘/proc/17772/task/17772/fd/4’: No such file or directory
du: cannot access ‘/proc/17772/fdinfo/4’: No such file or directory
0       /proc      &lt;==不会占用硬盘空间！
1       /root
25      /run
....（中间省略）....
3126    /usr       &lt;==系统初期最大就是他了啦！
117     /var
# 这是个很常被使用的功能～利用万用字符 * 来代表每个目录，如果想要检查某个目录下，
# 哪个次目录占用最大的容量，可以用这个方法找出来。值得注意的是，如果刚刚安装好 Linux 时，
# 那么整个系统容量最大的应该是 /usr 。而 /proc 虽然有列出容量，但是那个容量是在内存中，
# 不占磁盘空间。至于 /proc 里头会列出一堆“No such file or directory” 的错误，
# 别担心！因为是内存内的程序，程序执行结束就会消失，因此会有些目录找不到，是正确的！ 
```

与 df 不一样的是，du 这个指令其实会直接到文件系统内去搜寻所有的文件数据， 所以上述第三个范例指令的运行会执行一小段时间！此外，在默认的情况下，容量的输出是以 KB 来设计的， 如果你想要知道目录占了多少 MB ，那么就使用 -m 这个参数即可啰！而， 如果你只想要知道该目录占了多少容量的话，使用 -s 就可以啦！

至于 -S 这个选项部分，由于 du 默认会将所有文件的大小均列出，因此假设你在 /etc 下面使用 du 时， 所有的文件大小，包括 /etc 下面的次目录容量也会被计算一次。然后最终的容量 （/etc） 也会加总一次， 因此很多朋友都会误会 du 分析的结果不太对劲。所以啰，如果想要列出某目录下的全部数据， 或许也可以加上 -S 的选项，减少次目录的加总喔！

### 7.2.2 实体链接与符号链接： ln

关于链接（link）数据我们第五章的 Linux 文件属性及 Linux 文件种类与扩展名当中提过一些信息， 不过当时由于尚未讲到文件系统，因此无法较完整的介绍链接文件啦。不过在上一小节谈完了文件系统后， 我们可以来了解一下链接文件这玩意儿了。

在 Linux 下面的链接文件有两种，一种是类似 Windows 的捷径功能的文件，可以让你快速的链接到目标文件（或目录）； 另一种则是通过文件系统的 inode 链接来产生新文件名，而不是产生新文件！这种称为实体链接 （hard link）。 这两种玩意儿是完全不一样的东西呢！现在就分别来谈谈。

*   Hard Link （实体链接, 硬式链接或实际链接）

在前一小节当中，我们知道几件重要的信息，包括：

*   每个文件都会占用一个 inode ，文件内容由 inode 的记录来指向；
*   想要读取该文件，必须要经过目录记录的文件名来指向到正确的 inode 号码才能读取。

也就是说，其实文件名只与目录有关，但是文件内容则与 inode 有关。那么想一想， 有没有可能有多个文件名对应到同一个 inode 号码呢？有的！那就是 hard link 的由来。 所以简单的说：hard link 只是在某个目录下新增一笔文件名链接到某 inode 号码的关连记录而已。

举个例子来说，假设我系统有个 /root/crontab 他是 /etc/crontab 的实体链接，也就是说这两个文件名链接到同一个 inode ， 自然这两个文件名的所有相关信息都会一模一样（除了文件名之外）。实际的情况可以如下所示：

```
[root@study ~]# ll -i /etc/crontab
34474855 -rw-r--r--. 1 root root 451 Jun 10  2014 /etc/crontab

[root@study ~]# ln /etc/crontab .   &lt;==创建实体链接的指令
[root@study ~]# ll -i /etc/crontab crontab
34474855 -rw-r--r--. 2 root root 451 Jun 10  2014 crontab
34474855 -rw-r--r--. 2 root root 451 Jun 10  2014 /etc/crontab 
```

你可以发现两个文件名都链接到 34474855 这个 inode 号码，所以您瞧瞧，是否文件的权限/属性完全一样呢？ 因为这两个“文件名”其实是一模一样的“文件”啦！而且你也会发现第二个字段由原本的 1 变成 2 了！ 那个字段称为“链接”，这个字段的意义为：“有多少个文件名链接到这个 inode 号码”的意思。 如果将读取到正确数据的方式画成示意图，就类似如下画面：

![实体链接的文件读取示意图](img/hard_link1.gif)图 7.2.1、实体链接的文件读取示意图

上图的意思是，你可以通过 1 或 2 的目录之 inode 指定的 block 找到两个不同的文件名，而不管使用哪个文件名均可以指到 real 那个 inode 去读取到最终数据！那这样有什么好处呢？最大的好处就是“安全”！如同上图中， 如果你将任何一个“文件名”删除，其实 inode 与 block 都还是存在的！ 此时你可以通过另一个“文件名”来读取到正确的文件数据喔！此外，不论你使用哪个“文件名”来编辑， 最终的结果都会写入到相同的 inode 与 block 中，因此均能进行数据的修改哩！

一般来说，使用 hard link 设置链接文件时，磁盘的空间与 inode 的数目都不会改变！ 我们还是由图 7.2.1 来看，由图中可以知道， hard link 只是在某个目录下的 block 多写入一个关连数据而已，既不会增加 inode 也不会耗用 block 数量哩！

![鸟哥的图示](img/vbird_face.gif "鸟哥的图示")

**Tips** hard link 的制作中，其实还是可能会改变系统的 block 的，那就是当你新增这笔数据却刚好将目录的 block 填满时，就可能会新加一个 block 来记录文件名关连性，而导致磁盘空间的变化！不过，一般 hard link 所用掉的关连数据量很小，所以通常不会改变 inode 与磁盘空间的大小喔！

由图 7.2.1 其实我们也能够知道，事实上 hard link 应该仅能在单一文件系统中进行的，应该是不能够跨文件系统才对！ 因为图 7.2.1 就是在同一个 filesystem 上嘛！所以 hard link 是有限制的：

*   不能跨 Filesystem；
*   不能 link 目录。

不能跨 Filesystem 还好理解，那不能 hard link 到目录又是怎么回事呢？这是因为如果使用 hard link 链接到目录时， 链接的数据需要连同被链接目录下面的所有数据都创建链接，举例来说，如果你要将 /etc 使用实体链接创建一个 /etc_hd 的目录时，那么在 /etc_hd 下面的所有文件名同时都与 /etc 下面的文件名要创建 hard link 的，而不是仅链接到 /etc_hd 与 /etc 而已。 并且，未来如果需要在 /etc_hd 下面创建新文件时，连带的， /etc 下面的数据又得要创建一次 hard link ，因此造成环境相当大的复杂度。 所以啰，目前 hard link 对于目录暂时还是不支持的啊！

*   Symbolic Link （符号链接，亦即是捷径）

相对于 hard link ， Symbolic link 可就好理解多了，基本上， Symbolic link 就是在创建一个独立的文件，而这个文件会让数据的读取指向他 link 的那个文件的文件名！由于只是利用文件来做为指向的动作， 所以，当来源文件被删除之后，symbolic link 的文件会“开不了”， 会一直说“无法打开某文件！”。实际上就是找不到原始“文件名”而已啦！

举例来说，我们先创建一个符号链接文件链接到 /etc/crontab 去看看：

```
[root@study ~]# ln -s /etc/crontab crontab2
[root@study ~]# ll -i /etc/crontab /root/crontab2
34474855 -rw-r--r--. 2 root root 451 Jun 10  2014 /etc/crontab
53745909 lrwxrwxrwx. 1 root root  12 Jun 23 22:31 /root/crontab2 -&gt; /etc/crontab 
```

由上表的结果我们可以知道两个文件指向不同的 inode 号码，当然就是两个独立的文件存在！ 而且链接文件的重要内容就是他会写上目标文件的“文件名”， 你可以发现为什么上表中链接文件的大小为 12 Bytes 呢？ 因为箭头（-->）右边的文件名“/etc/crontab”总共有 12 个英文，每个英文占用 1 个 Bytes ，所以文件大小就是 12Bytes 了！

关于上述的说明，我们以如下图示来解释：

![符号链接的文件读取示意图](img/symbolic_link1.gif)图 7.2.2、符号链接的文件读取示意图

由 1 号 inode 读取到链接文件的内容仅有文件名，根据文件名链接到正确的目录去取得目标文件的 inode ， 最终就能够读取到正确的数据了。你可以发现的是，如果目标文件（/etc/crontab）被删除了，那么整个环节就会无法继续进行下去， 所以就会发生无法通过链接文件读取的问题了！

这里还是得特别留意，这个 Symbolic Link 与 Windows 的捷径可以给他划上等号，由 Symbolic link 所创建的文件为一个独立的新的文件，所以会占用掉 inode 与 block 喔！

* * *

由上面的说明来看，似乎 hard link 比较安全，因为即使某一个目录下的关连数据被杀掉了， 也没有关系，只要有任何一个目录下存在着关连数据，那么该文件就不会不见！举上面的例子来说，我的 /etc/crontab 与 /root/crontab 指向同一个文件，如果我删除了 /etc/crontab 这个文件，该删除的动作其实只是将 /etc 目录下关于 crontab 的关连数据拿掉而已， crontab 所在的 inode 与 block 其实都没有被变动喔！

不过由于 Hard Link 的限制太多了，包括无法做“目录”的 link ， 所以在用途上面是比较受限的！反而是 Symbolic Link 的使用方面较广喔！好了， 说的天花乱坠，看你也差不多快要昏倒了！没关系，实作一下就知道怎么回事了！要制作链接文件就必须要使用 ln 这个指令呢！

```
[root@study ~]# ln [-sf] 来源文件 目标文件
选项与参数：
-s  ：如果不加任何参数就进行链接，那就是 hard link，至于 -s 就是 symbolic link
-f  ：如果 目标文件 存在时，就主动的将目标文件直接移除后再创建！

范例一：将 /etc/passwd 复制到 /tmp 下面，并且观察 inode 与 block
[root@study ~]# cd /tmp
[root@study tmp]# cp -a /etc/passwd .
[root@study tmp]# du -sb ; df -i .
6602    .   &lt;==先注意一下这里的容量是多少！
Filesystem                Inodes  IUsed    IFree IUse% Mounted on
/dev/mapper/centos-root 10485760 109748 10376012    2% /
# 利用 du 与 df 来检查一下目前的参数～那个 du -sb 是计算整个 /tmp 下面有多少 Bytes 的容量啦！

范例二：将 /tmp/passwd 制作 hard link 成为 passwd-hd 文件，并观察文件与容量
[root@study tmp]# ln passwd passwd-hd
[root@study tmp]# du -sb ; df -i .
6602    .
Filesystem                Inodes  IUsed    IFree IUse% Mounted on
/dev/mapper/centos-root 10485760 109748 10376012    2% /
# 仔细看，即使多了一个文件在 /tmp 下面，整个 inode 与 block 的容量并没有改变！

[root@study tmp]# ls -il passwd*
2668897 -rw-r--r--. 2 root root 2092 Jun 17 00:20 passwd
2668897 -rw-r--r--. 2 root root 2092 Jun 17 00:20 passwd-hd
# 原来是指向同一个 inode 啊！这是个重点啊！另外，那个第二栏的链接数也会增加！

范例三：将 /tmp/passwd 创建一个符号链接
[root@study tmp]# ln -s passwd passwd-so
[root@study tmp]# ls -li passwd*
2668897 -rw-r--r--. 2 root root 2092 Jun 17 00:20 passwd
2668897 -rw-r--r--. 2 root root 2092 Jun 17 00:20 passwd-hd
2668898 lrwxrwxrwx. 1 root root    6 Jun 23 22:40 passwd-so -&gt; passwd
# passwd-so 指向的 inode number 不同了！这是一个新的文件～这个文件的内容是指向 
# passwd 的。passwd-so 的大小是 6Bytes ，因为 “passwd” 这个单字共有六个字符之故

[root@study tmp]# du -sb ; df -i .
6608    .
Filesystem                Inodes  IUsed    IFree IUse% Mounted on
/dev/mapper/centos-root 10485760 109749 10376011    2% /
# 呼呼！整个容量与 inode 使用数都改变啰～确实如此啊！

范例四：删除原始文件 passwd ，其他两个文件是否能够打开？
[root@study tmp]# rm passwd
[root@study tmp]# cat passwd-hd
.....（正常显示完毕！）
[root@study tmp]# cat passwd-so
cat: passwd-so: No such file or directory
[root@study tmp]# ll passwd*
-rw-r--r--. 1 root root 2092 Jun 17 00:20 passwd-hd
lrwxrwxrwx. 1 root root    6 Jun 23 22:40 passwd-so -&gt; passwd
# 怕了吧！符号链接果然无法打开！另外，如果符号链接的目标文件不存在，
# 其实文件名的部分就会有特殊的颜色显示喔！ 
```

![鸟哥的图示](img/vbird_face.gif "鸟哥的图示")

**Tips** 还记得第五章当中，我们提到的 /tmp 这个目录是干嘛用的吗？是给大家作为暂存盘用的啊！ 所以，您会发现，过去我们在进行测试时，都会将数据移动到 /tmp 下面去练习～ 嘿嘿！因此，有事没事，记得将 /tmp 下面的一些怪异的数据清一清先！

要注意啰！使用 ln 如果不加任何参数的话，那么就是 Hard Link 啰！如同范例二的情况，增加了 hard link 之后，可以发现使用 ls -l 时，显示的 link 那一栏属性增加了！而如果这个时候砍掉 passwd 会发生什么事情呢？passwd-hd 的内容还是会跟原来 passwd 相同，但是 passwd-so 就会找不到该文件啦！

而如果 ln 使用 -s 的参数时，就做成差不多是 Windows 下面的“捷径”的意思。当你修改 Linux 下的 symbolic link 文件时，则更动的其实是“原始文件”， 所以不论你的这个原始文件被链接到哪里去，只要你修改了链接文件，原始文件就跟着变啰！ 以上面为例，由于你使用 -s 的参数创建一个名为 passwd-so 的文件，则你修改 passwd-so 时，其内容与 passwd 完全相同，并且，当你按下储存之后，被改变的将是 passwd 这个文件！

此外，如果你做了下面这样的链接：

> ln -s /bin /root/bin

那么如果你进入 /root/bin 这个目录下，“请注意呦！该目录其实是 /bin 这个目录，因为你做了链接文件了！”所以，如果你进入 /root/bin 这个刚刚创建的链接目录， 并且将其中的数据杀掉时，嗯！ /bin 里面的数据就通通不见了！这点请千万注意！所以赶紧利用“rm /root/bin ” 将这个链接文件删除吧！

基本上， Symbolic link 的用途比较广，所以您要特别留意 symbolic link 的用法呢！未来一定还会常常用到的啦！

*   关于目录的 link 数量：

或许您已经发现了，那就是，当我们以 hard link 进行“文件的链接”时，可以发现，在 ls -l 所显示的第二字段会增加一才对，那么请教，如果创建目录时，他默认的 link 数量会是多少？ 让我们来想一想，一个“空目录”里面至少会存在些什么？呵呵！就是存在 . 与 .. 这两个目录啊！ 那么，当我们创建一个新目录名称为 /tmp/testing 时，基本上会有三个东西，那就是：

*   /tmp/testing
*   /tmp/testing/.
*   /tmp/testing/..

而其中 /tmp/testing 与 /tmp/testing/. 其实是一样的！都代表该目录啊～而 /tmp/testing/.. 则代表 /tmp 这个目录，所以说，当我们创建一个新的目录时， “新的目录的 link 数为 2 ，而上层目录的 link 数则会增加 1 ” 不信的话，我们来作个测试看看：

```
[root@study ~]# ls -ld /tmp
drwxrwxrwt. 14 root root 4096 Jun 23 22:42 /tmp
[root@study ~]# mkdir /tmp/testing1
[root@study ~]# ls -ld /tmp
drwxrwxrwt. 15 root root 4096 Jun 23 22:45 /tmp  # 这里的 link 数量加 1 了！
[root@study ~]# ls -ld /tmp/testing1
drwxr-xr-x. 2 root root 6 Jun 23 22:45 /tmp/testing1/ 
```

瞧！原本的所谓上层目录 /tmp 的 link 数量由 14 增加为 15 ，至于新目录 /tmp/testing 则为 2 ，这样可以理解目录的 link 数量的意义了吗？ ^_^

# 7.3 磁盘的分区、格式化、检验与挂载

## 7.3 磁盘的分区、格式化、检验与挂载

对于一个系统管理者（ root ）而言，磁盘的的管理是相当重要的一环，尤其近来磁盘已经渐渐的被当成是消耗品了 ..... 如果我们想要在系统里面新增一颗磁盘时，应该有哪些动作需要做的呢：

1.  对磁盘进行分区，以创建可用的 partition ；
2.  对该 partition 进行格式化 （format），以创建系统可用的 filesystem；
3.  若想要仔细一点，则可对刚刚创建好的 filesystem 进行检验；
4.  在 Linux 系统上，需要创建挂载点 （亦即是目录），并将他挂载上来；

当然啰，在上述的过程当中，还有很多需要考虑的，例如磁盘分区 （partition） 需要定多大？ 是否需要加入 journal 的功能？inode 与 block 的数量应该如何规划等等的问题。但是这些问题的决定， 都需要与你的主机用途来加以考虑的～所以，在这个小节里面，鸟哥仅会介绍几个动作而已， 更详细的设置值，则需要以你未来的经验来参考啰！

### 7.3.1 观察磁盘分区状态

由于目前磁盘分区主要有 MBR 以及 GPT 两种格式，这两种格式所使用的分区工具不太一样！你当然可以使用本章预计最后才介绍的 parted 这个通通有支持的工具来处理，不过，我们还是比较习惯使用 fdisk 或者是 gdisk 来处理分区啊！因此，我们自然就得要去找一下目前系统有的磁盘有哪些？ 这些磁盘是 MBR 还是 GPT 等等的！这样才能处理啦！

*   lsblk 列出系统上的所有磁盘列表

lsblk 可以看成“ list block device ”的缩写，就是列出所有储存设备的意思！这个工具软件真的很好用喔！来瞧一瞧！

```
[root@study ~]# lsblk [-dfimpt] [device]
选项与参数：
-d  ：仅列出磁盘本身，并不会列出该磁盘的分区数据
-f  ：同时列出该磁盘内的文件系统名称
-i  ：使用 ASCII 的线段输出，不要使用复杂的编码 （再某些环境下很有用）
-m  ：同时输出该设备在 /dev 下面的权限数据 （rwx 的数据）
-p  ：列出该设备的完整文件名！而不是仅列出最后的名字而已。
-t  ：列出该磁盘设备的详细数据，包括磁盘伫列机制、预读写的数据量大小等

范例一：列出本系统下的所有磁盘与磁盘内的分区信息
[root@study ~]# lsblk
NAME            MAJ:MIN RM  SIZE RO TYPE MOUNTPOINT
sr0              11:0    1 1024M  0 rom
vda             252:0    0   40G  0 disk             # 一整颗磁盘
&#124;-vda1          252:1    0    2M  0 part
&#124;-vda2          252:2    0    1G  0 part /boot
`-vda3          252:3    0   30G  0 part
  &#124;-centos-root 253:0    0   10G  0 lvm  /           # 在 vda3 内的其他文件系统
  &#124;-centos-swap 253:1    0    1G  0 lvm  [SWAP]
  `-centos-home 253:2    0    5G  0 lvm  /home 
```

从上面的输出我们可以很清楚的看到，目前的系统主要有个 sr0 以及一个 vda 的设备，而 vda 的设备下面又有三个分区， 其中 vda3 甚至还有因为 LVM 产生的文件系统！相当的完整吧！从范例一我们来谈谈默认输出的信息有哪些。

*   NAME：就是设备的文件名啰！会省略 /dev 等前导目录！
*   MAJ:MIN：其实核心认识的设备都是通过这两个代码来熟悉的！分别是主要：次要设备代码！
*   RM：是否为可卸载设备 （removable device），如光盘、USB 磁盘等等
*   SIZE：当然就是容量啰！
*   RO：是否为只读设备的意思
*   TYPE：是磁盘 （disk）、分区 （partition） 还是只读存储器 （rom） 等输出
*   MOUTPOINT：就是前一章谈到的挂载点！

```
范例二：仅列出 /dev/vda 设备内的所有数据的完整文件名
[root@study ~]# lsblk -ip /dev/vda
NAME                        MAJ:MIN RM  SIZE RO TYPE MOUNTPOINT
/dev/vda                    252:0    0   40G  0 disk
&#124;-/dev/vda1                 252:1    0    2M  0 part
&#124;-/dev/vda2                 252:2    0    1G  0 part /boot
`-/dev/vda3                 252:3    0   30G  0 part
  &#124;-/dev/mapper/centos-root 253:0    0   10G  0 lvm  /
  &#124;-/dev/mapper/centos-swap 253:1    0    1G  0 lvm  [SWAP]
  `-/dev/mapper/centos-home 253:2    0    5G  0 lvm  /home        # 完整的文件名，由 / 开始写 
```

*   blkid 列出设备的 UUID 等参数

虽然 lsblk 已经可以使用 -f 来列出文件系统与设备的 UUID 数据，不过，鸟哥还是比较习惯直接使用 blkid 来找出设备的 UUID 喔！ 什么是 UUID 呢？UUID 是全域单一识别码 （universally unique identifier），Linux 会将系统内所有的设备都给予一个独一无二的识别码， 这个识别码就可以拿来作为挂载或者是使用这个设备/文件系统之用了。

```
[root@study ~]# blkid
/dev/vda2: UUID="94ac5f77-cb8a-495e-a65b-2ef7442b837c" TYPE="xfs" 
/dev/vda3: UUID="WStYq1-P93d-oShM-JNe3-KeDl-bBf6-RSmfae" TYPE="LVM2_member"
/dev/sda1: UUID="35BC-6D6B" TYPE="vfat"
/dev/mapper/centos-root: UUID="299bdc5b-de6d-486a-a0d2-375402aaab27" TYPE="xfs"
/dev/mapper/centos-swap: UUID="905dc471-6c10-4108-b376-a802edbd862d" TYPE="swap"
/dev/mapper/centos-home: UUID="29979bf1-4a28-48e0-be4a-66329bf727d9" TYPE="xfs" 
```

如上所示，每一行代表一个文件系统，主要列出设备名称、UUID 名称以及文件系统的类型 （TYPE）！这对于管理员来说，相当有帮助！ 对于系统上面的文件系统观察来说，真是一目了然！

*   parted 列出磁盘的分区表类型与分区信息

虽然我们已经知道了系统上面的所有设备，并且通过 blkid 也知道了所有的文件系统！不过，还是不清楚磁盘的分区类型。 这时我们可以通过简单的 parted 来输出喔！我们这里仅简单的利用他的输出而已～本章最后才会详细介绍这个指令的用法的！

```
[root@study ~]# parted device_name print

范例一：列出 /dev/vda 磁盘的相关数据
[root@study ~]# parted /dev/vda print
Model: Virtio Block Device （virtblk）        # 磁盘的模块名称（厂商）
Disk /dev/vda: 42.9GB                       # 磁盘的总容量
Sector size （logical/physical）: 512B/512B   # 磁盘的每个逻辑/物理扇区容量
Partition Table: gpt                        # 分区表的格式 （MBR/GPT）
Disk Flags: pmbr_boot

Number  Start   End     Size    File system  Name  Flags      # 下面才是分区数据
 1      1049kB  3146kB  2097kB                     bios_grub
 2      3146kB  1077MB  1074MB  xfs
 3      1077MB  33.3GB  32.2GB                     lvm 
```

看到上表的说明，你就知道啦！我们用的就是 GPT 的分区格式喔！这样会观察磁盘分区了吗？接下来要来操作磁盘分区了喔！

### 7.3.2 磁盘分区： gdisk/fdisk

接下来我们想要进行磁盘分区啰！要注意的是：“MBR 分区表请使用 fdisk 分区， GPT 分区表请使用 gdisk 分区！” 这个不要搞错～否则会分区失败的！另外，这两个工具软件的操作很类似，执行了该软件后，可以通过该软件内部的说明数据来操作， 因此不需要硬背！只要知道方法即可。刚刚从上面 parted 的输出结果，我们也知道鸟哥这个测试机使用的是 GPT 分区， 因此下面通通得要使用 gdisk 来分区才行！

*   gdisk

```
[root@study ~]# gdisk 设备名称

范例：由前一小节的 lsblk 输出，我们知道系统有个 /dev/vda，请观察该磁盘的分区与相关数据
[root@study ~]# gdisk /dev/vda  &lt;==仔细看，不要加上数字喔！
GPT fdisk （gdisk） version 0.8.6

Partition table scan:
  MBR: protective
  BSD: not present
  APM: not present
  GPT: present

Found valid GPT with protective MBR; using GPT.  &lt;==找到了 GPT 的分区表！

Command （? for help）:     &lt;==这里可以让你输入指令动作，可以按问号 （?） 来查看可用指令
Command （? for help）: ?
b       back up GPT data to a file
c       change a partition's name
d       delete a partition           # 删除一个分区
i       show detailed information on a partition
l       list known partition types
n       add a new partition          # 增加一个分区
o       create a new empty GUID partition table （GPT）
p       print the partition table    # 印出分区表 （常用）
q       quit without saving changes  # 不储存分区就直接离开 gdisk
r       recovery and transformation options （experts only）
s       sort partitions
t       change a partition's type code
v       verify disk
w       write table to disk and exit # 储存分区操作后离开 gdisk
x       extra functionality （experts only）
?       print this menu
Command （? for help）: 
```

你应该要通过 lsblk 或 blkid 先找到磁盘，再用 parted /dev/xxx print 来找出内部的分区表类型，之后才用 gdisk 或 fdisk 来操作系统。 上表中可以发现 gdisk 会扫描 MBR 与 GPT 分区表，不过这个软件还是单纯使用在 GPT 分区表比较好啦！

老实说，使用 gdisk 这支程序是完全不需要背指令的！如同上面的表格中，你只要按下 ? 就能够看到所有的动作！ 比较重要的动作在上面已经用底线画出来了，你可以参考看看。其中比较不一样的是“q 与 w”这两个玩意儿！ 不管你进行了什么动作，只要离开 gdisk 时按下“q”，那么所有的动作“都不会生效！”相反的， 按下“w”就是动作生效的意思。所以，你可以随便玩 gdisk ，只要离开时按下的是“q”即可。 ^_^！ 好了，先来看看分区表信息吧！

```
Command （? for help）: p  &lt;== 这里可以输出目前磁盘的状态
Disk /dev/vda: 83886080 sectors, 40.0 GiB                     # 磁盘文件名/扇区数与总容量
Logical sector size: 512 Bytes                                # 单一扇区大小为 512 Bytes 
Disk identifier （GUID）: A4C3C813-62AF-4BFE-BAC9-112EBD87A483  # 磁盘的 GPT 识别码
Partition table holds up to 128 entries
First usable sector is 34, last usable sector is 83886046
Partitions will be aligned on 2048-sector boundaries
Total free space is 18862013 sectors （9.0 GiB）

Number  Start （sector）    End （sector）  Size       Code  Name # 下面为完整的分区信息了！
   1            2048            6143   2.0 MiB     EF02       # 第一个分区数据
   2            6144         2103295   1024.0 MiB  0700
   3         2103296        65026047   30.0 GiB    8E00
# 分区编号 开始扇区号码  结束扇区号码  容量大小
Command （? for help）: q
# 想要不储存离开吗？按下 q 就对了！不要随便按 w 啊！ 
```

使用“ p ”可以列出目前这颗磁盘的分区表信息，这个信息的上半部在显示整体磁盘的状态。 以鸟哥这颗磁盘为例，这个磁盘共有 40GB 左右的容量，共有 83886080 个扇区，每个扇区的容量为 512Bytes。 要注意的是，现在的分区主要是以扇区为最小的单位喔！

下半部的分区表信息主要在列出每个分区的个别信息项目。每个项目的意义为：

*   Number：分区编号，1 号指的是 /dev/vda1 这样计算。
*   Start （sector）：每一个分区的开始扇区号码位置
*   End （sector）：每一个分区的结束扇区号码位置，与 start 之间可以算出分区的总容量
*   Size：就是分区的容量了
*   Code：在分区内的可能的文件系统类型。Linux 为 8300，swap 为 8200。不过这个项目只是一个提示而已，不见得真的代表此分区内的文件系统喔！
*   Name：文件系统的名称等等。

从上表我们可以发现几件事情：

*   整部磁盘还可以进行额外的分区，因为最大扇区为 83886080，但只使用到 65026047 号而已；
*   分区的设计中，新分区通常选用上一个分区的结束扇区号码数加 1 作为起始扇区号码！

这个 gdisk 只有 root 才能执行，此外，请注意，使用的“设备文件名”请不要加上数字，因为 partition 是针对“整个磁盘设备”而不是某个 partition 呢！所以执行“ gdisk /dev/vda1 ” 就会发生错误啦！要使用 gdisk /dev/vda 才对！

![鸟哥的图示](img/vbird_face.gif "鸟哥的图示")

**Tips** 再次强调，你可以使用 gdisk 在您的磁盘上面胡搞瞎搞的进行实际操作，都不打紧，但是请“千万记住，不要按下 w 即可！”离开的时候按下 q 就万事无妨啰！ 此外，不要在 MBR 分区上面使用 gdisk，因为如果指令按错，恐怕你的分区纪录会全部死光光！也不要在 GPT 上面使用 fdisk 啦！切记切记！

*   用 gdisk 新增分区

如果你是按照鸟哥建议的方式去安装你的 CentOS 7，那么你的磁盘应该会预留一块容量来做练习的。如果没有的话， 那么你可能需要找另外一颗磁盘来让你练习才行呦！而经过上面的观察，我们也确认系统还有剩下的容量可以来操作练习分区！ 假设我需要有如下的分区需求：

*   1GB 的 xfs 文件系统 （Linux）
*   1GB 的 vfat 文件系统 （Windows）
*   0.5GB 的 swap （Linux swap）（这个分区等一下会被删除喔！）

那就来处理处理！

```
[root@study ~]# gdisk /dev/vda
Command （? for help）: p
Number  Start （sector）    End （sector）  Size       Code  Name
   1            2048            6143   2.0 MiB     EF02
   2            6144         2103295   1024.0 MiB  0700
   3         2103296        65026047   30.0 GiB    8E00
# 找出最后一个 sector 的号码是很重要的！

Command （? for help）: ?  # 查一下增加分区的指令为何
Command （? for help）: n  # 就是这个！所以开始新增的行为！
Partition number （4-128, default 4）: 4  # 默认就是 4 号，所以也能 enter 即可！
First sector （34-83886046, default = 65026048） or {+-}size{KMGTP}: 65026048  # 也能 enter
Last sector （65026048-83886046, default = 83886046） or {+-}size{KMGTP}: +1G  # 决不要 enter
# 这个地方可有趣了！我们不需要自己去计算扇区号码，通过 +容量 的这个方式，
# 就可以让 gdisk 主动去帮你算出最接近你需要的容量的扇区号码喔！

Current type is 'Linux filesystem'
Hex code or GUID （L to show codes, Enter = 8300）: # 使用默认值即可～直接 enter 下去！
# 这里在让你选择未来这个分区预计使用的文件系统！默认都是 Linux 文件系统的 8300 啰！

Command （? for help）: p
Number  Start （sector）    End （sector）  Size       Code  Name
   1            2048            6143   2.0 MiB     EF02
   2            6144         2103295   1024.0 MiB  0700
   3         2103296        65026047   30.0 GiB    8E00
 4        65026048        67123199   1024.0 MiB  8300  Linux filesystem 
```

重点在“ Last sector ”那一行，那行绝对不要使用默认值！因为默认值会将所有的容量用光！因此它默认选择最大的扇区号码！ 因为我们仅要 1GB 而已，所以你得要加上 +1G 这样即可！不需要计算 sector 的数量，gdisk 会根据你填写的数值， 直接计算出最接近该容量的扇区数！每次新增完毕后，请立即“ p ”查看一下结果喔！请继续处理后续的两个分区！ 最终出现的画面会有点像下面这样才对！

```
Command （? for help）: p
Number  Start （sector）    End （sector）  Size       Code  Name
   1            2048            6143   2.0 MiB     EF02
   2            6144         2103295   1024.0 MiB  0700
   3         2103296        65026047   30.0 GiB    8E00
   4        65026048        67123199   1024.0 MiB  8300  Linux filesystem
   5        67123200        69220351   1024.0 MiB  0700  Microsoft basic data
   6        69220352        70244351   500.0 MiB   8200  Linux swap 
```

基本上，几乎都用默认值，然后通过 +1G, +500M 来创建所需要的另外两个分区！比较有趣的是文件系统的 ID 啦！一般来说， Linux 大概都是 8200/8300/8e00 等三种格式， Windows 几乎都用 0700 这样，如果忘记这些数字，可以在 gdisk 内按下：“ L ”来显示喔！ 如果一切的分区状态都正常的话，那么就直接写入磁盘分区表吧！

```
Command （? for help）: w

Final checks complete. About to write GPT data. THIS WILL OVERWRITE EXISTING
PARTITIONS!!

Do you want to proceed? （Y/N）: y
OK; writing new GUID partition table （GPT） to /dev/vda.
Warning: The kernel is still using the old partition table.
The new table will be used at the next reboot.
The operation has completed successfully.
# gdisk 会先警告你可能的问题，我们确定分区是对的，这时才按下 y ！不过怎么还有警告？
# 这是因为这颗磁盘目前正在使用当中，因此系统无法立即载入新的分区表～

[root@study ~]# cat /proc/partitions
major minor  #blocks  name

 252        0   41943040 vda
 252        1       2048 vda1
 252        2    1048576 vda2
 252        3   31461376 vda3
 253        0   10485760 dm-0
 253        1    1048576 dm-1
 253        2    5242880 dm-2
# 你可以发现，并没有 vda4, vda5, vda6 喔！因为核心还没有更新！ 
```

因为 Linux 此时还在使用这颗磁盘，为了担心系统出问题，所以分区表并没有被更新喔！这个时候我们有两个方式可以来处理！ 其中一个是重新开机，不过很讨厌！另外一个则是通过 partprobe 这个指令来处理即可！

*   partprobe 更新 Linux 核心的分区表信息

```
[root@study ~]# partprobe [-s]  # 你可以不要加 -s ！那么屏幕不会出现讯息！
[root@study ~]# partprobe -s    # 不过还是建议加上 -s 比较清晰！
/dev/vda: gpt partitions 1 2 3 4 5 6

[root@study ~]# lsblk /dev/vda  # 实际的磁盘分区状态
NAME            MAJ:MIN RM  SIZE RO TYPE MOUNTPOINT
vda             252:0    0   40G  0 disk
&#124;-vda1          252:1    0    2M  0 part
&#124;-vda2          252:2    0    1G  0 part /boot
&#124;-vda3          252:3    0   30G  0 part
&#124; &#124;-centos-root 253:0    0   10G  0 lvm  /
&#124; &#124;-centos-swap 253:1    0    1G  0 lvm  [SWAP]
&#124; `-centos-home 253:2    0    5G  0 lvm  /home
&#124;-vda4          252:4    0    1G  0 part
&#124;-vda5          252:5    0    1G  0 part
`-vda6          252:6    0  500M  0 part

[root@study ~]# cat /proc/partitions  # 核心的分区纪录
major minor  #blocks  name

 252        0   41943040 vda
 252        1       2048 vda1
 252        2    1048576 vda2
 252        3   31461376 vda3
 252        4    1048576 vda4
 252        5    1048576 vda5
 252        6     512000 vda6
# 现在核心也正确的抓到了分区参数了！ 
```

*   用 gdisk 删除一个分区

已经学会了新增分区，那么删除分区呢？好！现在让我们将刚刚创建的 /dev/vda6 删除！你该如何进行呢？鸟哥下面很快的处理一遍， 大家赶紧来瞧一瞧先！

```
[root@study ~]# gdisk /dev/vda
Command （? for help）: p
Number  Start （sector）    End （sector）  Size       Code  Name
   1            2048            6143   2.0 MiB     EF02
   2            6144         2103295   1024.0 MiB  0700
   3         2103296        65026047   30.0 GiB    8E00
   4        65026048        67123199   1024.0 MiB  8300  Linux filesystem
   5        67123200        69220351   1024.0 MiB  0700  Microsoft basic data
   6        69220352        70244351   500.0 MiB   8200  Linux swap

Command （? for help）: d
Partition number （1-6）: 6

Command （? for help）: p
# 你会发现 /dev/vda6 不见了！非常棒！没问题就写入吧！

Command （? for help）: w
# 同样会有一堆讯息！鸟哥就不重复输出了！自己选择 y 来处理吧！

[root@study ~]# lsblk
# 你会发现！怪了！怎么还是有 /dev/vda6 呢？没办法！还没有更新核心的分区表啊！所以当然有错！

[root@study ~]# partprobe -s
[root@study ~]# lsblk
# 这个时候，那个 /dev/vda6 才真的消失不见了！了解吧！ 
```

![鸟哥的图示](img/vbird_face.gif "鸟哥的图示")

**Tips** 万分注意！不要去处理一个正在使用中的分区！例如，我们的系统现在已经使用了 /dev/vda2 ，那如果你要删除 /dev/vda2 的话， 必须要先将 /dev/vda2 卸载，否则直接删除该分区的话，虽然磁盘还是慧写入正确的分区信息，但是核心会无法更新分区表的信息的！ 另外，文件系统与 Linux 系统的稳定性，恐怕也会变得怪怪的！反正！千万不要处理正在使用中的文件系统就对了！

*   fdisk

虽然 MBR 分区表在未来应该会慢慢的被淘汰，毕竟现在磁盘容量随便都大于 2T 以上了。而对于在 CentOS 7.x 中还无法完整支持 GPT 的 fdisk 来说， 这家伙真的英雄无用武之地了啦！不过依旧有些旧的系统，以及虚拟机的使用上面，还是有小磁盘存在的空间！这时处理 MBR 分区表， 就得要使用 fdisk 啰！

因为 fdisk 跟 gdisk 使用的方式几乎一样！只是一个使用 ? 作为指令提示数据，一个使用 m 作为提示这样而已。 此外，fdisk 有时会使用柱面 （cylinder） 作为分区的最小单位，与 gdisk 默认使用 sector 不太一样！大致上只是这点差别！ 另外， MBR 分区是有限制的 （Primary, Extended, Logical...）！不要忘记了！鸟哥这里不使用范例了，毕竟示范机上面也没有 MBR 分区表... 这里仅列出相关的指令给大家对照参考啰！

```
[root@study ~]# fdisk /dev/sda
Command （m for help）: m   &lt;== 输入 m 后，就会看到下面这些指令介绍
Command action
   a   toggle a bootable flag
   b   edit bsd disklabel
   c   toggle the dos compatibility flag
   d   delete a partition            &lt;==删除一个 partition
   l   list known partition types
   m   print this menu
   n   add a new partition           &lt;==新增一个 partition
   o   create a new empty DOS partition table
   p   print the partition table     &lt;==在屏幕上显示分区表
   q   quit without saving changes   &lt;==不储存离开 fdisk 程序
   s   create a new empty Sun disklabel
   t   change a partition's system id
   u   change display/entry units
   v   verify the partition table
   w   write table to disk and exit  &lt;==将刚刚的动作写入分区表
   x   extra functionality （experts only） 
```

### 7.3.3 磁盘格式化（创建文件系统）

分区完毕后自然就是要进行文件系统的格式化啰！格式化的指令非常的简单，那就是“make filesystem, mkfs” 这个指令啦！这个指令其实是个综合的指令，他会去调用正确的文件系统格式化工具软件！因为 CentOS 7 使用 xfs 作为默认文件系统， 下面我们会先介绍 mkfs.xfs ，之后介绍新一代的 EXT 家族成员 mkfs.ext4，最后再聊一聊 mkfs 这个综合指令吧！

*   XFS 文件系统 mkfs.xfs

我们常听到的“格式化”其实应该称为“创建文件系统 （make filesystem）”才对啦！所以使用的指令是 mkfs 喔！那我们要创建的其实是 xfs 文件系统， 因此使用的是 mkfs.xfs 这个指令才对。这个指令是这样使用的：

```
[root@study ~]# mkfs.xfs [-b bsize] [-d parms] [-i parms] [-l parms] [-L label] [-f] \
                         [-r parms] 设备名称
选项与参数：
关於单位：下面只要谈到“数值”时，没有加单位则为 Bytes 值，可以用 k,m,g,t,p （小写）等来解释
          比较特殊的是 s 这个单位，它指的是 sector 的“个数”喔！
-b  ：后面接的是 block 容量，可由 512 到 64k，不过最大容量限制为 Linux 的 4k 喔！
-d  ：后面接的是重要的 data section 的相关参数值，主要的值有：
      agcount=数值  ：设置需要几个储存群组的意思（AG），通常与 CPU 有关
      agsize=数值   ：每个 AG 设置为多少容量的意思，通常 agcount/agsize 只选一个设置即可
      file          ：指的是“格式化的设备是个文件而不是个设备”的意思！（例如虚拟磁盘）
      size=数值     ：data section 的容量，亦即你可以不将全部的设备容量用完的意思
      su=数值       ：当有 RAID 时，那个 stripe 数值的意思，与下面的 sw 搭配使用
      sw=数值       ：当有 RAID 时，用于储存数据的磁盘数量（须扣除备份碟与备用碟）
      sunit=数值    ：与 su 相当，不过单位使用的是“几个 sector（512Bytes 大小）”的意思
      swidth=数值   ：就是 su*sw 的数值，但是以“几个 sector（512Bytes 大小）”来设置
-f  ：如果设备内已经有文件系统，则需要使用这个 -f 来强制格式化才行！
-i  ：与 inode 有较相关的设置，主要的设置值有：
      size=数值     ：最小是 256Bytes 最大是 2k，一般保留 256 就足够使用了！
      internal=[0&#124;1]：log 设备是否为内置？默认为 1 内置，如果要用外部设备，使用下面设置
      logdev=device ：log 设备为后面接的那个设备上头的意思，需设置 internal=0 才可！
      size=数值     ：指定这块登录区的容量，通常最小得要有 512 个 block，大约 2M 以上才行！
-L  ：后面接这个文件系统的标头名称 Label name 的意思！
-r  ：指定 realtime section 的相关设置值，常见的有：
      extsize=数值  ：就是那个重要的 extent 数值，一般不须设置，但有 RAID 时，
                      最好设置与 swidth 的数值相同较佳！最小为 4K 最大为 1G 。

范例：将前一小节分区出来的 /dev/vda4 格式化为 xfs 文件系统
[root@study ~]# mkfs.xfs /dev/vda4
meta-data=/dev/vda4       isize=256    agcount=4, agsize=65536 blks
         =                sectsz=512   attr=2, projid32bit=1
         =                crc=0        finobt=0
data     =                bsize=4096   blocks=262144, imaxpct=25
         =                sunit=0      swidth=0 blks
naming   =version 2       bsize=4096   ascii-ci=0 ftype=0
log      =internal log    bsize=4096   blocks=2560, version=2
         =                sectsz=512   sunit=0 blks, lazy-count=1
realtime =none            extsz=4096   blocks=0, rtextents=0
# 很快格是化完毕！都用默认值！较重要的是 inode 与 block 的数值

[root@study ~]# blkid /dev/vda4
/dev/vda4: UUID="39293f4f-627b-4dfd-a015-08340537709c" TYPE="xfs"
# 确定创建好 xfs 文件系统了！ 
```

使用默认的 xfs 文件系统参数来创建系统即可！速度非常快！如果我们有其他额外想要处理的项目，才需要加上一堆设置值！举例来说，因为 xfs 可以使用多个数据流来读写系统，以增加速度，因此那个 agcount 可以跟 CPU 的核心数来做搭配！举例来说，如果我的服务器仅有一颗 4 核心，但是有启动 Intel 超线程功能，则系统会仿真出 8 颗 CPU 时，那个 agcount 就可以设置为 8 喔！举个例子来瞧瞧：

```
范例：找出你系统的 CPU 数，并据以设置你的 agcount 数值
[root@study ~]# grep 'processor' /proc/cpuinfo
processor       : 0
processor       : 1
# 所以就是有两颗 CPU 的意思，那就来设置设置我们的 xfs 文件系统格式化参数吧！！

[root@study ~]# mkfs.xfs -f -d agcount=2 /dev/vda4
meta-data=/dev/vda4       isize=256    agcount=2, agsize=131072 blks
         =                sectsz=512   attr=2, projid32bit=1
         =                crc=0        finobt=0
.....（下面省略）.....
# 可以跟前一个范例对照看看，可以发现 agcount 变成 2 了喔！
# 此外，因为已经格式化过一次，因此 mkfs.xfs 可能会出现不给你格式化的警告！因此需要使用 -f 
```

*   XFS 文件系统 for RAID 性能优化 （Optional）

我们在第十四章会持续谈到进阶文件系统的设置，其中就有磁盘阵列这个东西！磁盘阵列是多颗磁盘组成一颗大磁盘的意思， 利用同步写入到这些磁盘的技术，不但可以加快读写速度，还可以让某一颗磁盘坏掉时，整个文件系统还是可以持续运行的状态！那就是所谓的容错。

基本上，磁盘阵列 （RAID） 就是通过将文件先细分为数个小型的分区区块 （stripe） 之后，然后将众多的 stripes 分别放到磁盘阵列里面的所有磁盘， 所以一个文件是被同时写入到多个磁盘去，当然性能会好一些。为了文件的保全性，所以在这些磁盘里面，会保留数个 （与磁盘阵列的规划有关） 备份磁盘 （parity disk）， 以及可能会保留一个以上的备用磁盘 （spare disk），这些区块基本上会占用掉磁盘阵列的总容量，不过对于数据的保全会比较有保障！

那个分区区块 stripe 的数值大多介于 4K 到 1M 之间，这与你的磁盘阵列卡支持的项目有关。stripe 与你的文件数据容量以及性能相关性较高。 当你的系统大多是大型文件时，一般建议 stripe 可以设置大一些，这样磁盘阵列读/写的频率会降低，性能会提升。如果是用于系统， 那么小文件比较多的情况下， stripe 建议大约在 64K 左右可能会有较佳的性能。不过，还是都须要经过测试啦！完全是 case by case 的情况。 更多详细的磁盘阵列我们在第十四章再来谈，这里先有个大概的认识即可。14 章看完之后，再回来这个小节瞧瞧啰！

文件系统的读写要能够有最优化，最好能够搭配磁盘阵列的参数来设计，这样性能才能够起来！也就是说，你可以先在文件系统就将 stripe 规划好， 那交给 RAID 去存取时，它就无须重复进行文件的 stripe 过程，性能当然会更好！那格式化时，最优化性能与什么咚咚有关呢？我们来假设个环境好了：

*   我有两个线程的 CPU 数量，所以 agcount 最好指定为 2
*   当初设置 RAID 的 stripe 指定为 256K 这么大，因此 su 最好设置为 256k
*   设置的磁盘阵列有 8 颗，因为是 RAID5 的设置，所以有一个 parity （备份碟），因此指定 sw 为 7
*   由上述的数据中，我们可以发现数据宽度 （swidth） 应该就是 256K*7 得到 1792K，可以指定 extsize 为 1792k

相关数据的来源可以参考文末[[7]](#ps7)的说明，这里仅快速的使用 mkfs.xfs 的参数来处理格式化的动作喔！

```
[root@study ~]# mkfs.xfs -f -d agcount=2,su=256k,sw=7 -r extsize=1792k /dev/vda4
meta-data=/dev/vda4              isize=256    agcount=2, agsize=131072 blks
         =                       sectsz=512   attr=2, projid32bit=1
         =                       crc=0        finobt=0
data     =                       bsize=4096   blocks=262144, imaxpct=25
         =                       sunit=64     swidth=448 blks
naming   =version 2              bsize=4096   ascii-ci=0 ftype=0
log      =internal log           bsize=4096   blocks=2560, version=2
         =                       sectsz=512   sunit=64 blks, lazy-count=1
realtime =none                   extsz=1835008 blocks=0, rtextents=0 
```

从输出的结果来看， agcount 没啥问题， sunit 结果是 64 个 block，因为每个 block 为 4K，所以算出来容量就是 256K 也没错！ 那个 swidth 也相同！使用 448 * 4K 得到 1792K！那个 extsz 则是算成 Bytes 的单位，换算结果也没错啦！上面是个方式，那如果使用 sunit 与 swidth 直接套用在 mkfs.xfs 当中呢？那你得小心了！因为指令中的这两个参数用的是“几个 512Bytes 的 sector 数量”的意思！ 是“数量”单位而不是“容量”单位！因此先计算为：

*   sunit = 256K/512Byte*1024（Bytes/K） = 512 个 sector
*   swidth = 7 个磁盘 *sunit = 7* 512 = 3584 个 sector

所以指令就得要变成如下模样：

```
[root@study ~]# mkfs.xfs -f -d agcount=2,sunit=512,swidth=3584 -r extsize=1792k /dev/vda4 
```

再说一次，这边你大概先有个概念即可，看不懂也没关系！等到 14 章看完后，未来回到这里，应该就能够看得懂了！ 多看几次！多做几次～操作系统的练习就是这样才能学的会！看得懂！ ^_^

*   EXT4 文件系统 mkfs.ext4

如果想要格式化为 ext4 的传统 Linux 文件系统的话，可以使用 mkfs.ext4 这个指令即可！这个指令的参数快速的介绍一下！

```
[root@study ~]# mkfs.ext4 [-b size] [-L label] 设备名称
选项与参数：
-b  ：设置 block 的大小，有 1K, 2K, 4K 的容量，
-L  ：后面接这个设备的标头名称。

范例：将 /dev/vda5 格式化为 ext4 文件系统
[root@study ~]# mkfs.ext4 /dev/vda5
mke2fs 1.42.9 （28-Dec-2013）
Filesystem label=                                  # 显示 Label name
OS type: Linux
Block size=4096 （log=2）                            # 每一个 block 的大小
Fragment size=4096 （log=2）
Stride=0 blocks, Stripe width=0 blocks             # 跟 RAID 相关性较高
65536 inodes, 262144 blocks                        # 总计 inode/block 的数量
13107 blocks （5.00%） reserved for the super user
First data block=0
Maximum filesystem blocks=268435456
8 block groups                                     # 共有 8 个 block groups 喔！
32768 blocks per group, 32768 fragments per group
8192 inodes per group
Superblock backups stored on blocks:
        32768, 98304, 163840, 229376

Allocating group tables: done
Writing inode tables: done
Creating journal （8192 blocks）: done
Writing superblocks and filesystem accounting information: done

[root@study ~]# dumpe2fs -h /dev/vda5
dumpe2fs 1.42.9 （28-Dec-2013）
Filesystem volume name:   &lt;none&gt;
Last mounted on:          &lt;not available&gt;
Filesystem UUID:          3fd5cc6f-a47d-46c0-98c0-d43b072e0e12
....（中间省略）....
Inode count:              65536
Block count:              262144
Block size:               4096
Blocks per group:         32768
Inode size:               256
Journal size:             32M 
```

由于数据量较大，因此鸟哥仅列出比较重要的项目而已，提供给你参考。另外，本章稍早之前介绍的 dumpe2fs 现在也可以测试练习了！查阅一下相关的数据吧！ 因为 ext4 的默认值已经相当适合我们系统使用，大部分的默认值写入于我们系统的 /etc/mke2fs.conf 这个文件中，有兴趣可以自行前往查阅。 也因此，我们无须额外指定 inode 的容量，系统都帮我们做好默认值啰！只需要得到 uuid 这个咚咚即可啦！

*   其他文件系统 mkfs

mkfs 其实是个综合指令而已，当我们使用 mkfs -t xfs 时，它就会跑去找 mkfs.xfs 相关的参数给我们使用！ 如果想要知道系统还支持哪种文件系统的格式化功能，直接按 [tabl] 就很清楚了！

```
[root@study ~]# mkfs[tab][tab]
mkfs         mkfs.btrfs   mkfs.cramfs  mkfs.ext2    mkfs.ext3    mkfs.ext4    
mkfs.fat     mkfs.minix   mkfs.msdos   mkfs.vfat    mkfs.xfs 
```

所以系统还有支持 ext2/ext3/vfat 等等多种常用的文件系统喔！那如果要将刚刚的 /dev/vda5 重新格式化为 VFAT 文件系统呢？

```
[root@study ~]# mkfs -t vfat /dev/vda5
[root@study ~]# blkid /dev/vda5
/dev/vda5: UUID="7130-6012" TYPE="vfat" PARTLABEL="Microsoft basic data"

[root@study ~]# mkfs.ext4 /dev/vda5
[root@study ~]# blkid /dev/vda4 /dev/vda5
/dev/vda4: UUID="e0a6af55-26e7-4cb7-a515-826a8bd29e90" TYPE="xfs" 
/dev/vda5: UUID="899b755b-1da4-4d1d-9b1c-f762adb798e1" TYPE="ext4" 
```

上面就是我们这个章节最后的结果了！ /dev/vda4 是 xfs 文件系统，而 /dev/vda5 是 ext4 文件系统喔！都有练习妥当了嘛？

![鸟哥的图示](img/vbird_face.gif "鸟哥的图示")

**Tips** 越来越多同学上课都不听讲，只是很单纯的将鸟哥在屏幕操作的过程“拍照”下来而已～当鸟哥说“开始操作！等一下要检查喔！” 大家就拼命的从手机里面将刚刚的照片抓出来，一个一个指令照着打～

不过，屏幕并不能告诉你“ [tab] 按钮其实不是按下 enter”的结果， 如上所示，同学拼命的按下 mkfs 之后，却没有办法得到下面出现的众多指令，就开始举手...老师！我没办法作到你讲的画面...

拜托读者们，请注意：“我们是要练习 Linux 系统，不是要练习 "英文打字"”啦！英文打字回家练就好了！ @_@

### 7.3.4 文件系统检验

由于系统在运行时谁也说不准啥时硬件或者是电源会有问题，所以“死机”可能是难免的情况（不管是硬件还是软件）。 现在我们知道文件系统运行时会有磁盘与内存数据非同步的状况发生，因此莫名其妙的死机非常可能导致文件系统的错乱。 问题来啦，如果文件系统真的发生错乱的话，那该如何是好？就...挽救啊！不同的文件系统救援的指令不太一样，我们主要针对 xfs 及 ext4 这两个主流来说明而已喔！

*   xfs_repair 处理 XFS 文件系统

当有 xfs 文件系统错乱才需要使用这个指令！所以，这个指令最好是不要用到啦！但有问题发生时，这个指令却又很重要...

```
[root@study ~]# xfs_repair [-fnd] 设备名称
选项与参数：
-f  ：后面的设备其实是个文件而不是实体设备
-n  ：单纯检查并不修改文件系统的任何数据 （检查而已）
-d  ：通常用在单人维护模式下面，针对根目录 （/） 进行检查与修复的动作！很危险！不要随便使用

范例：检查一下刚刚创建的 /dev/vda4 文件系统
[root@study ~]# xfs_repair /dev/vda4
Phase 1 - find and verify superblock...
Phase 2 - using internal log
Phase 3 - for each AG...
Phase 4 - check for duplicate blocks...
Phase 5 - rebuild AG headers and trees...
Phase 6 - check inode connectivity...
Phase 7 - verify and correct link counts...
done
# 共有 7 个重要的检查流程！详细的流程介绍可以 man xfs_repair 即可！

范例：检查一下系统原本就有的 /dev/centos/home 文件系统
[root@study ~]# xfs_repair /dev/centos/home
xfs_repair: /dev/centos/home contains a mounted filesystem
xfs_repair: /dev/centos/home contains a mounted and writable filesystem

fatal error -- couldn't initialize XFS library 
```

xfs_repair 可以检查/修复文件系统，不过，因为修复文件系统是个很庞大的任务！因此，修复时该文件系统不能被挂载！ 所以，检查与修复 /dev/vda4 没啥问题，但是修复 /dev/centos/home 这个已经挂载的文件系统时，嘿嘿！就出现上述的问题了！ 没关系，若可以卸载，卸载后再处理即可。

Linux 系统有个设备无法被卸载，那就是根目录啊！如果你的根目录有问题怎办？这时得要进入单人维护或救援模式，然后通过 -d 这个选项来处理！ 加入 -d 这个选项后，系统会强制检验该设备，检验完毕后就会自动重新开机啰！不过，鸟哥完全不打算要进行这个指令的实做... 永远都不希望实做这东西...

*   fsck.ext4 处理 EXT4 文件系统

fsck 是个综合指令，如果是针对 ext4 的话，建议直接使用 fsck.ext4 来检测比较妥当！那 fsck.ext4 的选项有下面几个常见的项目：

```
[root@study ~]# fsck.ext4 [-pf] [-b superblock] 设备名称
选项与参数：
-p  ：当文件系统在修复时，若有需要回复 y 的动作时，自动回复 y 来继续进行修复动作。
-f  ：强制检查！一般来说，如果 fsck 没有发现任何 unclean 的旗标，不会主动进入
      细部检查的，如果您想要强制 fsck 进入细部检查，就得加上 -f 旗标啰！
-D  ：针对文件系统下的目录进行最优化配置。
-b  ：后面接 superblock 的位置！一般来说这个选项用不到。但是如果你的 superblock 因故损毁时，
      通过这个参数即可利用文件系统内备份的 superblock 来尝试救援。一般来说，superblock 备份在：
      1K block 放在 8193, 2K block 放在 16384, 4K block 放在 32768

范例：找出刚刚创建的 /dev/vda5 的另一块 superblock，并据以检测系统
[root@study ~]# dumpe2fs -h /dev/vda5 &#124; grep 'Blocks per group'
Blocks per group:         32768
# 看起来每个 block 群组会有 32768 个 block，因此第二个 superblock 应该就在 32768 上！
# 因为 block 号码为 0 号开始编的！

[root@study ~]# fsck.ext4 -b 32768 /dev/vda5
e2fsck 1.42.9 （28-Dec-2013）
/dev/vda5 was not cleanly unmounted, check forced.
Pass 1: Checking inodes, blocks, and sizes
Deleted inode 1577 has zero dtime.  Fix&lt;y&gt;? yes
Pass 2: Checking directory structure
Pass 3: Checking directory connectivity
Pass 4: Checking reference counts
Pass 5: Checking group summary information

/dev/vda5: ***** FILE SYSTEM WAS MODIFIED *****  # 文件系统被改过，所以这里会有警告！
/dev/vda5: 11/65536 files （0.0% non-contiguous）, 12955/262144 blocks
# 好巧合！鸟哥使用这个方式来检验系统，恰好遇到文件系统出问题！于是可以有比较多的解释方向！
# 当文件系统出问题，它就会要你选择是否修复～如果修复如上所示，按下 y 即可！
# 最终系统会告诉你，文件系统已经被更改过，要注意该项目的意思！

范例：已默认设置强制检查一次 /dev/vda5
[root@study ~]# fsck.ext4 /dev/vda5
e2fsck 1.42.9 （28-Dec-2013）
/dev/vda5: clean, 11/65536 files, 12955/262144 blocks
# 文件系统状态正常，它并不会进入强制检查！会告诉你文件系统没问题 （clean）

[root@study ~]# fsck.ext4 -f /dev/vda5
e2fsck 1.42.9 （28-Dec-2013）
Pass 1: Checking inodes, blocks, and sizes
....（下面省略）.... 
```

无论是 xfs_repair 或 fsck.ext4，这都是用来检查与修正文件系统错误的指令。注意：通常只有身为 root 且你的文件系统有问题的时候才使用这个指令，否则在正常状况下使用此一指令， 可能会造成对系统的危害！通常使用这个指令的场合都是在系统出现极大的问题，导致你在 Linux 开机的时候得进入单人单机模式下进行维护的行为时，才必须使用此一指令！

另外，如果你怀疑刚刚格式化成功的磁盘有问题的时后，也可以使用 xfs_repair/fsck.ext4 来检查一磁盘呦！其实就有点像是 Windows 的 scandisk 啦！此外，由于 xfs_repair/fsck.ext4 在扫瞄磁盘的时候，可能会造成部分 filesystem 的修订，所以“执行 xfs_repair/fsck.ext4 时， 被检查的 partition 务必不可挂载到系统上！亦即是需要在卸载的状态喔！”

### 7.3.5 文件系统挂载与卸载

我们在本章一开始时的挂载点的意义当中提过挂载点是目录， 而这个目录是进入磁盘分区（其实是文件系统啦！）的入口就是了。不过要进行挂载前，你最好先确定几件事：

*   单一文件系统不应该被重复挂载在不同的挂载点（目录）中；
*   单一目录不应该重复挂载多个文件系统；
*   要作为挂载点的目录，理论上应该都是空目录才是。

尤其是上述的后两点！如果你要用来挂载的目录里面并不是空的，那么挂载了文件系统之后，原目录下的东西就会暂时的消失。 举个例子来说，假设你的 /home 原本与根目录 （/） 在同一个文件系统中，下面原本就有 /home/test 与 /home/vbird 两个目录。然后你想要加入新的磁盘，并且直接挂载 /home 下面，那么当你挂载上新的分区时，则 /home 目录显示的是新分区内的数据，至于原先的 test 与 vbird 这两个目录就会暂时的被隐藏掉了！注意喔！并不是被覆盖掉， 而是暂时的隐藏了起来，等到新分区被卸载之后，则 /home 原本的内容就会再次的跑出来啦！

而要将文件系统挂载到我们的 Linux 系统上，就要使用 mount 这个指令啦！ 不过，这个指令真的是博大精深～粉难啦！我们学简单一点啊～ ^_^

```
[root@study ~]# mount -a
[root@study ~]# mount [-l]
[root@study ~]# mount [-t 文件系统] LABEL=''  挂载点
[root@study ~]# mount [-t 文件系统] UUID=''   挂载点  # 鸟哥近期建议用这种方式喔！
[root@study ~]# mount [-t 文件系统] 设备文件名  挂载点
选项与参数：
-a  ：依照配置文件 /etc/fstab 的数据将所有未挂载的磁盘都挂载上来
-l  ：单纯的输入 mount 会显示目前挂载的信息。加上 -l 可增列 Label 名称！
-t  ：可以加上文件系统种类来指定欲挂载的类型。常见的 Linux 支持类型有：xfs, ext3, ext4,
      reiserfs, vfat, iso9660（光盘格式）, nfs, cifs, smbfs （后三种为网络文件系统类型）
-n  ：在默认的情况下，系统会将实际挂载的情况实时写入 /etc/mtab 中，以利其他程序的运行。
      但在某些情况下（例如单人维护模式）为了避免问题会刻意不写入。此时就得要使用 -n 选项。
-o  ：后面可以接一些挂载时额外加上的参数！比方说帐号、密码、读写权限等：
      async, sync:   此文件系统是否使用同步写入 （sync） 或非同步 （async） 的
                     内存机制，请参考文件系统运行方式。默认为 async。
      atime,noatime: 是否修订文件的读取时间（atime）。为了性能，某些时刻可使用 noatime
      ro, rw:        挂载文件系统成为只读（ro） 或可读写（rw）
      auto, noauto:  允许此 filesystem 被以 mount -a 自动挂载（auto）
      dev, nodev:    是否允许此 filesystem 上，可创建设备文件？ dev 为可允许
      suid, nosuid:  是否允许此 filesystem 含有 suid/sgid 的文件格式？
      exec, noexec:  是否允许此 filesystem 上拥有可执行 binary 文件？
      user, nouser:  是否允许此 filesystem 让任何使用者执行 mount ？一般来说，
                     mount 仅有 root 可以进行，但下达 user 参数，则可让
                     一般 user 也能够对此 partition 进行 mount 。
      defaults:      默认值为：rw, suid, dev, exec, auto, nouser, and async
      remount:       重新挂载，这在系统出错，或重新更新参数时，很有用！ 
```

基本上，CentOS 7 已经太聪明了，因此你不需要加上 -t 这个选项，系统会自动的分析最恰当的文件系统来尝试挂载你需要的设备！ 这也是使用 blkid 就能够显示正确的文件系统的缘故！那 CentOS 是怎么找出文件系统类型的呢？ 由于文件系统几乎都有 superblock ，我们的 Linux 可以通过分析 superblock 搭配 Linux 自己的驱动程序去测试挂载， 如果成功的套和了，就立刻自动的使用该类型的文件系统挂载起来啊！那么系统有没有指定哪些类型的 filesystem 才需要进行上述的挂载测试呢？ 主要是参考下面这两个文件：

*   /etc/filesystems：系统指定的测试挂载文件系统类型的优先顺序；
*   /proc/filesystems：Linux 系统已经载入的文件系统类型。

那我怎么知道我的 Linux 有没有相关文件系统类型的驱动程序呢？我们 Linux 支持的文件系统之驱动程序都写在如下的目录中：

*   /lib/modules/$（uname -r）/kernel/fs/

例如 ext4 的驱动程序就写在“/lib/modules/$（uname -r）/kernel/fs/ext4/”这个目录下啦！

另外，过去我们都习惯使用设备文件名然后直接用该文件名挂载， 不过近期以来鸟哥比较建议使用 UUID 来识别文件系统，会比设备名称与标头名称还要更可靠！因为是独一无二的啊！

*   挂载 xfs/ext4/vfat 等文件系统

```
范例：找出 /dev/vda4 的 UUID 后，用该 UUID 来挂载文件系统到 /data/xfs 内
[root@study ~]# blkid /dev/vda4
/dev/vda4: UUID="e0a6af55-26e7-4cb7-a515-826a8bd29e90" TYPE="xfs"

[root@study ~]# mount UUID="e0a6af55-26e7-4cb7-a515-826a8bd29e90" /data/xfs
mount: mount point /data/xfs does not exist  # 非正规目录！所以手动创建它！

[root@study ~]# mkdir -p /data/xfs
[root@study ~]# mount UUID="e0a6af55-26e7-4cb7-a515-826a8bd29e90" /data/xfs
[root@study ~]# df /data/xfs
Filesystem     1K-blocks  Used Available Use% Mounted on
/dev/vda4        1038336 32864   1005472   4% /data/xfs
# 顺利挂载，且容量约为 1G 左右没问题！

范例：使用相同的方式，将 /dev/vda5 挂载于 /data/ext4
[root@study ~]# blkid /dev/vda5
/dev/vda5: UUID="899b755b-1da4-4d1d-9b1c-f762adb798e1" TYPE="ext4"

[root@study ~]# mkdir /data/ext4
[root@study ~]# mount UUID="899b755b-1da4-4d1d-9b1c-f762adb798e1" /data/ext4
[root@study ~]# df /data/ext4
Filesystem     1K-blocks  Used Available Use% Mounted on
/dev/vda5         999320  2564    927944   1% /data/ext4 
```

*   挂载 CD 或 DVD 光盘

请拿出你的 CentOS 7 原版光盘出来，然后放入到光驱当中，我们来测试一下这个玩意儿啰！

```
范例：将你用来安装 Linux 的 CentOS 原版光盘拿出来挂载到 /data/cdrom！
[root@study ~]# blkid
.....（前面省略）.....
/dev/sr0: UUID="2015-04-01-00-21-36-00" LABEL="CentOS 7 x86_64" TYPE="iso9660" PTTYPE="dos"

[root@study ~]# mkdir /data/cdrom
[root@study ~]# mount /dev/sr0 /data/cdrom
mount: /dev/sr0 is write-protected, mounting read-only

[root@study ~]# df /data/cdrom
Filesystem     1K-blocks    Used Available Use% Mounted on
/dev/sr0         7413478 7413478         0 100% /data/cdrom
# 怎么会使用掉 100% 呢？是啊！因为是 DVD 啊！所以无法再写入了啊！ 
```

光驱一挂载之后就无法退出光盘片了！除非你将他卸载才能够退出！ 从上面的数据你也可以发现，因为是光盘嘛！所以磁盘使用率达到 100% ，因为你无法直接写入任何数据到光盘当中！ 此外，如果你使用的是图形界面，那么系统会自动的帮你挂载这个光盘到 /media/ 里面去喔！也可以不卸载就直接退出！ 但是文字界面没有这个福利就是了！ ^_^

![鸟哥的图示](img/vbird_face.gif "鸟哥的图示")

**Tips** 话说当时年纪小 （其实是刚接触 Linux 的那一年, 1999 年前后），摸 Linux 到处碰壁！连将 CDROM 挂载后， 光驱竟然都不让我退片！那个时候难过的要死！还用回纹针插入光驱让光盘退片耶！不过如此一来光盘就无法被使用了！ 若要再次使用光驱，当时的解决的方法竟然是“重新开机！”囧的可以啊！

*   挂载 vfat 中文 U 盘 （USB 磁盘）

请拿出你的 U 盘并插入 Linux 主机的 USB 接口中！注意，你的这个 U 盘不能够是 NTFS 的文件系统喔！接下来让我们测试测试吧！

```
范例：找出你的 U 盘设备的 UUID，并挂载到 /data/usb 目录中
[root@study ~]# blkid
/dev/sda1: UUID="35BC-6D6B" TYPE="vfat"

[root@study ~]# mkdir /data/usb
[root@study ~]#   mount -o codepage=950,iocharset=utf8 UUID="35BC-6D6B" /data/usb
[root@study ~]# # mount -o codepage=950,iocharset=big5 UUID="35BC-6D6B" /data/usb
[root@study ~]# df /data/usb
Filesystem     1K-blocks  Used Available Use% Mounted on
/dev/sda1        2092344     4   2092340   1% /data/usb 
```

如果带有中文文件名的数据，那么可以在挂载时指定一下挂载文件系统所使用的语系数据。 在 man mount 找到 vfat 文件格式当中可以使用 codepage 来处理！中文语系的代码为 950 喔！另外，如果想要指定中文是万国码还是大五码， 就得要使用 iocharset 为 utf8 还是 big5 两者择一了！因为鸟哥的 U 盘使用 utf8 编码，因此将上述的 big5 前面加上 # 符号， 代表注解该行的意思啰！

万一你使用的 USB 磁盘被格式化为 NTFS 时，那可能就得要动点手脚，因为默认的 CentOS 7 并没有支持 NTFS 文件系统格式！ 所以你得要安装 NTFS 文件系统的驱动程序后，才有办法处理的！这部份我们留待 22 章讲到 yum 服务器时再来谈吧！ 因为目前我们也还没有网络、也没有讲软件安装啊！ ^_^

*   重新挂载根目录与挂载不特定目录

整个目录树最重要的地方就是根目录了，所以根目录根本就不能够被卸载的！问题是，如果你的挂载参数要改变， 或者是根目录出现“只读”状态时，如何重新挂载呢？最可能的处理方式就是重新开机 （reboot）！ 不过你也可以这样做：

```
范例：将 / 重新挂载，并加入参数为 rw 与 auto
[root@study ~]# mount -o remount,rw,auto / 
```

重点是那个“ -o remount,xx ”的选项与参数！请注意，要重新挂载 （remount） 时， 这是个非常重要的机制！尤其是当你进入单人维护模式时，你的根目录常会被系统挂载为只读，这个时候这个指令就太重要了！

另外，我们也可以利用 mount 来将某个目录挂载到另外一个目录去喔！这并不是挂载文件系统，而是额外挂载某个目录的方法！ 虽然下面的方法也可以使用 symbolic link 来链接，不过在某些不支持符号链接的程序运行中，还是得要通过这样的方法才行。

```
范例：将 /var 这个目录暂时挂载到 /data/var 下面：
[root@study ~]# mkdir /data/var
[root@study ~]# mount --bind /var /data/var
[root@study ~]# ls -lid /var /data/var
16777346 drwxr-xr-x. 22 root root 4096 Jun 15 23:43 /data/var
16777346 drwxr-xr-x. 22 root root 4096 Jun 15 23:43 /var
# 内容完全一模一样啊！因为挂载目录的缘故！

[root@study ~]# mount &#124; grep var
/dev/mapper/centos-root on /data/var type xfs （rw,relatime,seclabel,attr2,inode64,noquota） 
```

看起来，其实两者链接到同一个 inode 嘛！ ^_^ 没错啦！通过这个 mount --bind 的功能， 您可以将某个目录挂载到其他目录去喔！而并不是整块 filesystem 的啦！所以从此进入 /data/var 就是进入 /var 的意思喔！

*   umount （将设备文件卸载）

```
[root@study ~]# umount [-fn] 设备文件名或挂载点
选项与参数：
-f  ：强制卸载！可用在类似网络文件系统 （NFS） 无法读取到的情况下；
-l  ：立刻卸载文件系统，比 -f 还强！
-n  ：不更新 /etc/mtab 情况下卸载。 
```

就是直接将已挂载的文件系统给他卸载即是！卸载之后，可以使用 df 或 mount 看看是否还存在目录树中？ 卸载的方式，可以下达设备文件名或挂载点，均可接受啦！下面的范例做看看吧！

```
范例：将本章之前自行挂载的文件系统全部卸载：
[root@study ~]# mount
.....（前面省略）.....
/dev/vda4 on /data/xfs type xfs （rw,relatime,seclabel,attr2,inode64,logbsize=256k,sunit=512,..）
/dev/vda5 on /data/ext4 type ext4 （rw,relatime,seclabel,data=ordered）
/dev/sr0 on /data/cdrom type iso9660 （ro,relatime）
/dev/sda1 on /data/usb type vfat （rw,relatime,fmask=0022,dmask=0022,codepage=950,iocharset=...）
/dev/mapper/centos-root on /data/var type xfs （rw,relatime,seclabel,attr2,inode64,noquota）
# 先找一下已经挂载的文件系统，如上所示，特殊字体即为刚刚挂载的设备啰！
# 基本上，卸载后面接设备或挂载点都可以！不过最后一个 centos-root 由于有其他挂载，
# 因此，该项目一定要使用挂载点来卸载才行！

[root@study ~]# umount /dev/vda4      &lt;==用设备文件名来卸载
[root@study ~]# umount /data/ext4     &lt;==用挂载点来卸载
[root@study ~]# umount /data/cdrom    &lt;==因为挂载点比较好记忆！
[root@study ~]# umount /data/usb     
[root@study ~]# umount /data/var      &lt;==一定要用挂载点！因为设备有被其他方式挂载 
```

由于通通卸载了，此时你才可以退出光盘片、软盘片、U 盘等设备喔！如果你遇到这样的情况：

```
[root@study ~]# mount /dev/sr0 /data/cdrom
[root@study ~]# cd /data/cdrom
[root@study cdrom]# umount /data/cdrom
umount: /data/cdrom: target is busy.
        （In some cases useful info about processes that use
         the device is found by lsof（8） or fuser（1））

[root@study cdrom]# cd /
[root@study /]# umount /data/cdrom 
```

由于你目前正在 /data/cdrom/ 的目录内，也就是说其实“你正在使用该文件系统”的意思！所以自然无法卸载这个设备！那该如何是好？就“离开该文件系统的挂载点”即可。以上述的案例来说， 你可以使用“ cd / ”回到根目录，就能够卸载 /data/cdrom 啰！简单吧！

### 7.3.6 磁盘/文件系统参数修订

某些时刻，你可能会希望修改一下目前文件系统的一些相关信息，举例来说，你可能要修改 Label name ， 或者是 journal 的参数，或者是其他磁盘/文件系统运行时的相关参数 （例如 DMA 启动与否～）。 这个时候，就得需要下面这些相关的指令功能啰～

*   mknod

还记得我们说过，在 Linux 下面所有的设备都以文件来代表吧！但是那个文件如何代表该设备呢？ 很简单！就是通过文件的 major 与 minor 数值来替代的～所以，那个 major 与 minor 数值是有特殊意义的，不是随意设置的喔！我们在 lsblk 指令的用法里面也谈过这两个数值呢！举例来说，在鸟哥的这个测试机当中， 那个用到的磁盘 /dev/vda 的相关设备代码如下：

```
[root@study ~]# ll /dev/vda*
brw-rw----. 1 root disk 252, 0 Jun 24 02:30 /dev/vda
brw-rw----. 1 root disk 252, 1 Jun 24 02:30 /dev/vda1
brw-rw----. 1 root disk 252, 2 Jun 15 23:43 /dev/vda2
brw-rw----. 1 root disk 252, 3 Jun 15 23:43 /dev/vda3
brw-rw----. 1 root disk 252, 4 Jun 24 20:00 /dev/vda4
brw-rw----. 1 root disk 252, 5 Jun 24 21:15 /dev/vda5 
```

上表当中 252 为主要设备代码 （Major） 而 0~5 则为次要设备代码 （Minor）。 我们的 Linux 核心认识的设备数据就是通过这两个数值来决定的！举例来说，常见的磁盘文件名 /dev/sda 与 /dev/loop0 设备代码如下所示：

| 磁盘文件名 | Major | Minor |
| --- | --- | --- |
| /dev/sda | 8 | 0-15 |
| /dev/sdb | 8 | 16-31 |
| /dev/loop0 | 7 | 0 |
| /dev/loop1 | 7 | 1 |

如果你想要知道更多核心支持的硬件设备代码 （major, minor） 请参考核心官网的链接[[8]](#ps8)。 基本上，Linux 核心 2.6 版以后，硬件文件名已经都可以被系统自动的实时产生了，我们根本不需要手动创建设备文件。 不过某些情况下面我们可能还是得要手动处理设备文件的，例如在某些服务被关到特定目录下时（chroot）， 就需要这样做了。此时这个 mknod 就得要知道如何操作才行！

```
[root@study ~]# mknod 设备文件名 [bcp] [Major] [Minor]
选项与参数：
设备种类：
   b  ：设置设备名称成为一个周边储存设备文件，例如磁盘等；
   c  ：设置设备名称成为一个周边输入设备文件，例如鼠标/键盘等；
   p  ：设置设备名称成为一个 FIFO 文件；
Major ：主要设备代码；
Minor ：次要设备代码；

范例：由上述的介绍我们知道 /dev/vda10 设备代码 252, 10，请创建并查阅此设备
[root@study ~]# mknod /dev/vda10 b 252 10
[root@study ~]# ll /dev/vda10
brw-r--r--. 1 root root 252, 10 Jun 24 23:40 /dev/vda10
# 上面那个 252 与 10 是有意义的，不要随意设置啊！

范例：创建一个 FIFO 文件，文件名为 /tmp/testpipe
[root@study ~]# mknod /tmp/testpipe p
[root@study ~]# ll /tmp/testpipe
prw-r--r--. 1 root root 0 Jun 24 23:44 /tmp/testpipe
# 注意啊！这个文件可不是一般文件，不可以随便就放在这里！
# 测试完毕之后请删除这个文件吧！看一下这个文件的类型！是 p 喔！^_^

[root@study ~]# rm /dev/vda10 /tmp/testpipe
rm: remove block special file '/dev/vda10' ? y
rm: remove fifo '/tmp/testpipe' ? y 
```

*   xfs_admin 修改 XFS 文件系统的 UUID 与 Label name

如果你当初格式化的时候忘记加上标头名称，后来想要再次加入时，不需要重复格式化！直接使用这个 xfs_admin 即可。 这个指令直接拿来处理 LABEL name 以及 UUID 即可啰！

```
[root@study ~]# xfs_admin [-lu] [-L label] [-U uuid] 设备文件名
选项与参数：
-l  ：列出这个设备的 label name
-u  ：列出这个设备的 UUID
-L  ：设置这个设备的 Label name
-U  ：设置这个设备的 UUID 喔！

范例：设置 /dev/vda4 的 label name 为 vbird_xfs，并测试挂载
[root@study ~]# xfs_admin -L vbird_xfs /dev/vda4
writing all SBs
new label = "vbird_xfs"                 # 产生新的 LABEL 名称啰！
[root@study ~]# xfs_admin -l /dev/vda4
label = "vbird_xfs"
[root@study ~]# mount LABEL=vbird_xfs /data/xfs/

范例：利用 uuidgen 产生新 UUID 来设置 /dev/vda4，并测试挂载
[root@study ~]# umount /dev/vda4       # 使用前，请先卸载！
[root@study ~]# uuidgen
e0fa7252-b374-4a06-987a-3cb14f415488    # 很有趣的指令！可以产生新的 UUID 喔！
[root@study ~]# xfs_admin -u /dev/vda4
UUID = e0a6af55-26e7-4cb7-a515-826a8bd29e90
[root@study ~]# xfs_admin -U e0fa7252-b374-4a06-987a-3cb14f415488 /dev/vda4
Clearing log and setting UUID
writing all SBs
new UUID = e0fa7252-b374-4a06-987a-3cb14f415488
[root@study ~]# mount UUID=e0fa7252-b374-4a06-987a-3cb14f415488 /data/xfs 
```

不知道你会不会有这样的疑问：“鸟哥啊，既然 mount 后面使用设备文件名 （/dev/vda4） 也可以挂载成功，那你为什么要用很讨厌的很长一串的 UUID 来作为你的挂载时写入的设备名称啊？”问的好！原因是这样的：“因为你没有办法指定这个磁盘在所有的 Linux 系统中，文件名一定都会是 /dev/vda ！”

举例来说，我们刚刚使用的 U 盘在鸟哥这个测试系统当中查询到的文件名是 /dev/sda，但是当这个 U 盘放到其他的已经有 /dev/sda 文件名的 Linux 系统下，它的文件名就会被指定成为 /dev/sdb 或 /dev/sdc 等等。反正，不会是 /dev/sda 了！那我怎么用同一个指令去挂载这只 U 盘呢？ 当然有问题吧！但是 UUID 可是很难重复的！看看上面 uuidgen 产生的结果你就知道了！所以你可以确定该名称不会被重复！ 这对系统管理上可是相当有帮助的！它也比 LABEL name 要更精准的多呢！ ^_^

*   tune2fs 修改 ext4 的 label name 与 UUID

```
[root@study ~]# tune2fs [-l] [-L Label] [-U uuid] 设备文件名
选项与参数：
-l  ：类似 dumpe2fs -h 的功能～将 superblock 内的数据读出来～
-L  ：修改 LABEL name
-U  ：修改 UUID 啰！

范例：列出 /dev/vda5 的 label name 之后，将它改成 vbird_ext4
[root@study ~]# dumpe2fs -h /dev/vda5 &#124; grep name
dumpe2fs 1.42.9 （28-Dec-2013）
Filesystem volume name:   &lt;none&gt;   # 果然是没有设置的！

[root@study ~]# tune2fs -L vbird_ext4 /dev/vda5
[root@study ~]# dumpe2fs -h /dev/vda5 &#124; grep name
Filesystem volume name:   vbird_ext4
[root@study ~]# mount LABEL=vbird_ext4 /data/ext4 
```

这个指令的功能其实很广泛啦～上面鸟哥仅列出很简单的一些参数而已，更多的用法请自行参考 man tune2fs 。

# 7.4 设置开机挂载

## 7.4 设置开机挂载

手动处理 mount 不是很人性化，我们总是需要让系统“自动”在开机时进行挂载的！本小节就是在谈这玩意儿！ 另外，从 FTP 服务器捉下来的镜像文件能否不用烧录就可以读取内容？我们也需要谈谈先！

### 7.4.1 开机挂载 /etc/fstab 及 /etc/mtab

刚刚上面说了许多，那么可不可以在开机的时候就将我要的文件系统都挂好呢？这样我就不需要每次进入 Linux 系统都还要在挂载一次呀！当然可以啰！那就直接到 /etc/fstab 里面去修修就行啰！不过，在开始说明前，这里要先跟大家说一说系统挂载的一些限制：

*   根目录 / 是必须挂载的﹐而且一定要先于其它 mount point 被挂载进来。
*   其它 mount point 必须为已创建的目录﹐可任意指定﹐但一定要遵守必须的系统目录架构原则 （FHS）
*   所有 mount point 在同一时间之内﹐只能挂载一次。
*   所有 partition 在同一时间之内﹐只能挂载一次。
*   如若进行卸载﹐您必须先将工作目录移到 mount point（及其子目录） 之外。

让我们直接查阅一下 /etc/fstab 这个文件的内容吧！

```
[root@study ~]# cat /etc/fstab
# Device                              Mount point  filesystem parameters    dump fsck
/dev/mapper/centos-root                   /       xfs     defaults            0 0
UUID=94ac5f77-cb8a-495e-a65b-2ef7442b837c /boot   xfs     defaults            0 0
/dev/mapper/centos-home                   /home   xfs     defaults            0 0
/dev/mapper/centos-swap                   swap    swap    defaults            0 0 
```

其实 /etc/fstab （filesystem table） 就是将我们利用 mount 指令进行挂载时， 将所有的选项与参数写入到这个文件中就是了。除此之外， /etc/fstab 还加入了 dump 这个备份用指令的支持！ 与开机时是否进行文件系统检验 fsck 等指令有关。 这个文件的内容共有六个字段，这六个字段非常的重要！你“一定要背起来”才好！ 各个字段的总结数据与详细数据如下：

![鸟哥的图示](img/vbird_face.gif "鸟哥的图示")

**Tips** 鸟哥比较龟毛一点，因为某些 distributions 的 /etc/fstab 文件排列方式蛮丑的， 虽然每一栏之间只要以空白字符分开即可，但就是觉得丑，所以通常鸟哥就会自己排列整齐， 并加上注解符号（就是 # ），来帮我记忆这些信息！

```
[设备/UUID 等]  [挂载点]  [文件系统]  [文件系统参数]  [dump]  [fsck] 
```

*   第一栏：磁盘设备文件名/UUID/LABEL name：

这个字段可以填写的数据主要有三个项目：

*   文件系统或磁盘的设备文件名，如 /dev/vda2 等
*   文件系统的 UUID 名称，如 UUID=xxx
*   文件系统的 LABEL 名称，例如 LABEL=xxx

因为每个文件系统都可以有上面三个项目，所以你喜欢哪个项目就填哪个项目！无所谓的！只是从鸟哥测试机的 /etc/fstab 里面看到的，在挂载点 /boot 使用的已经是 UUID 了喔！那你会说不是还有多个写 /dev/mapper/xxx 的吗？怎么回事啊？ 因为那个是 LVM 啊！LVM 的文件名在你的系统中也算是独一无二的，这部份我们在后续章节再来谈。 不过，如果为了一致性，你还是可以将他改成 UUID 也没问题喔！（鸟哥还是比较建议使用 UUID 喔！） 要记得使用 blkid 或 xfs_admin 来查询 UUID 喔！

*   第二栏：挂载点 （mount point）：：

就是挂载点啊！挂载点是什么？一定是目录啊～要知道啊！忘记的话，请回本章稍早之前的数据瞧瞧喔！

*   第三栏：磁盘分区的文件系统：

在手动挂载时可以让系统自动测试挂载，但在这个文件当中我们必须要手动写入文件系统才行！ 包括 xfs, ext4, vfat, reiserfs, nfs 等等。

*   第四栏：文件系统参数：

记不记得我们在 mount 这个指令中谈到很多特殊的文件系统参数？ 还有我们使用过的“-o codepage=950”？这些特殊的参数就是写入在这个字段啦！ 虽然之前在 mount 已经提过一次，这里我们利用表格的方式再汇整一下：

| 参数 | 内容意义 |
| --- | --- |
| async/sync 非同步/同步 | 设置磁盘是否以非同步方式运行！默认为 async（性能较佳） |
| auto/noauto 自动/非自动 | 当下达 mount -a 时，此文件系统是否会被主动测试挂载。默认为 auto。 |
| rw/ro 可读写/只读 | 让该分区以可读写或者是只读的型态挂载上来，如果你想要分享的数据是不给使用者随意变更的， 这里也能够设置为只读。则不论在此文件系统的文件是否设置 w 权限，都无法写入喔！ |
| exec/noexec 可执行/不可执行 | 限制在此文件系统内是否可以进行“执行”的工作？如果是纯粹用来储存数据的目录， 那么可以设置为 noexec 会比较安全。不过，这个参数也不能随便使用，因为你不知道该目录下是否默认会有可执行文件。举例来说，如果你将 noexec 设置在 /var ，当某些软件将一些可执行文件放置于 /var 下时，那就会产生很大的问题喔！ 因此，建议这个 noexec 最多仅设置于你自订或分享的一般数据目录。 |
| user/nouser 允许/不允许使用者挂载 | 是否允许使用者使用 mount 指令来挂载呢？一般而言，我们当然不希望一般身份的 user 能使用 mount 啰，因为太不安全了，因此这里应该要设置为 nouser 啰！ |
| suid/nosuid 具有/不具有 suid 权限 | 该文件系统是否允许 SUID 的存在？如果不是可执行文件放置目录，也可以设置为 nosuid 来取消这个功能！ |
| defaults | 同时具有 **rw, suid, dev, exec, auto, nouser, async** 等参数。 基本上，默认情况使用 defaults 设置即可！ |

*   第五栏：能否被 dump 备份指令作用：

dump 是一个用来做为备份的指令，不过现在有太多的备份方案了，所以这个项目可以不要理会啦！直接输入 0 就好了！

*   第六栏：是否以 fsck 检验扇区：

早期开机的流程中，会有一段时间去检验本机的文件系统，看看文件系统是否完整 （clean）。 不过这个方式使用的主要是通过 fsck 去做的，我们现在用的 xfs 文件系统就没有办法适用，因为 xfs 会自己进行检验，不需要额外进行这个动作！所以直接填 0 就好了。

好了，那么让我们来处理一下我们的新建的文件系统，看看能不能开机就挂载呢？

例题：假设我们要将 /dev/vda4 每次开机都自动挂载到 /data/xfs ，该如何进行？答：首先，请用 nano 将下面这一行写入 /etc/fstab 最后面中；

```
[root@study ~]# nano /etc/fstab
UUID="e0fa7252-b374-4a06-987a-3cb14f415488"  /data/xfs  xfs  defaults  0 0 
```

再来看看 /dev/vda4 是否已经挂载，如果挂载了，请务必卸载再说！

```
[root@study ~]# df
Filesystem              1K-blocks    Used Available Use% Mounted on
/dev/vda4                 1038336   32864   1005472   4% /data/xfs
# 竟然不知道何时被挂载了？赶紧给他卸载先！
# **因为，如果要被挂载的文件系统已经被挂载了（无论挂载在哪个目录），那测试就不会进行喔！**

[root@study ~]# umount /dev/vda4 
```

最后测试一下刚刚我们写入 /etc/fstab 的语法有没有错误！这点很重要！因为这个文件如果写错了， 则你的 Linux 很可能将无法顺利开机完成！所以请务必要测试测试喔！

```
[root@study ~]# mount -a
[root@study ~]# df /data/xfs 
```

最终有看到 /dev/vda4 被挂载起来的信息才是成功的挂载了！而且以后每次开机都会顺利的将此文件系统挂载起来的！ 现在，你可以下达 reboot 重新开机，然后看一下默认有没有多一个 /dev/vda4 呢？

/etc/fstab 是开机时的配置文件，不过，实际 filesystem 的挂载是记录到 /etc/mtab 与 /proc/mounts 这两个文件当中的。每次我们在更动 filesystem 的挂载时，也会同时更动这两个文件喔！但是，万一发生你在 /etc/fstab 输入的数据错误，导致无法顺利开机成功，而进入单人维护模式当中，那时候的 / 可是 read only 的状态，当然你就无法修改 /etc/fstab ，也无法更新 /etc/mtab 啰～那怎么办？没关系，可以利用下面这一招：

```
[root@study ~]# mount -n -o remount,rw / 
```

### 7.4.2 特殊设备 loop 挂载 （镜像文件不烧录就挂载使用）

如果有光盘镜像文件，或者是使用文件作为磁盘的方式时，那就得要使用特别的方法来将他挂载起来，不需要烧录啦！

*   挂载光盘/DVD 镜像文件

想像一下如果今天我们从国家高速网络中心（[`ftp.twaren.net`](http://ftp.twaren.net/)）或者是昆山科大（[`ftp.ksu.edu.tw`](http://ftp.ksu.edu.tw/)）下载了 Linux 或者是其他所需光盘/DVD 的镜像文件后， 难道一定需要烧录成为光盘才能够使用该文件里面的数据吗？当然不是啦！我们可以通过 loop 设备来挂载的！

那要如何挂载呢？鸟哥将整个 CentOS 7.x 的 DVD 镜像文件捉到测试机上面，然后利用这个文件来挂载给大家参考看看啰！

```
[root@study ~]# ll -h /tmp/CentOS-7.0-1406-x86_64-DVD.iso
-rw-r--r--. 1 root root 3.9G Jul  7  2014 /tmp/CentOS-7.0-1406-x86_64-DVD.iso
# 看到上面的结果吧！这个文件就是镜像文件，文件非常的大吧！

[root@study ~]# mkdir /data/centos_dvd
[root@study ~]# mount -o loop /tmp/CentOS-7.0-1406-x86_64-DVD.iso /data/centos_dvd
[root@study ~]# df /data/centos_dvd
Filesystem     1K-blocks    Used Available Use% Mounted on
/dev/loop0       4050860 4050860         0 100% /data/centos_dvd
# 就是这个项目！ .iso 镜像文件内的所有数据可以在 /data/centos_dvd 看到！

[root@study ~]# ll /data/centos_dvd
total 607
-rw-r--r--. 1  500  502     14 Jul  5  2014 CentOS_BuildTag &lt;==瞧！就是 DVD 的内容啊！
drwxr-xr-x. 3  500  502   2048 Jul  4  2014 EFI
-rw-r--r--. 1  500  502    611 Jul  5  2014 EULA
-rw-r--r--. 1  500  502  18009 Jul  5  2014 GPL
drwxr-xr-x. 3  500  502   2048 Jul  4  2014 images
.....（下面省略）.....

[root@study ~]# umount /data/centos_dvd/
# 测试完成！记得将数据给他卸载！同时这个镜像文件也被鸟哥删除了...测试机容量不够大！ 
```

非常方便吧！如此一来我们不需要将这个文件烧录成为光盘或者是 DVD 就能够读取内部的数据了！ 换句话说，你也可以在这个文件内“动手脚”去修改文件的！这也是为什么很多镜像文件提供后，还得要提供验证码 （MD5） 给使用者确认该镜像文件没有问题！

*   创建大文件以制作 loop 设备文件！

想一想，既然能够挂载 DVD 的镜像文件，那么我能不能制作出一个大文件，然后将这个文件格式化后挂载呢？ 好问题！这是个有趣的动作！而且还能够帮助我们解决很多系统的分区不良的情况呢！举例来说，如果当初在分区时， 你只有分区出一个根目录，假设你已经没有多余的容量可以进行额外的分区的！偏偏根目录的容量还很大！ 此时你就能够制作出一个大文件，然后将这个文件挂载！如此一来感觉上你就多了一个分区啰！用途非常的广泛啦！

下面我们在 /srv 下创建一个 512MB 左右的大文件，然后将这个大文件格式化并且实际挂载来玩一玩！ 这样你会比较清楚鸟哥在讲啥！

*   创建大型文件

首先，我们得先有一个大的文件吧！怎么创建这个大文件呢？在 Linux 下面我们有一支很好用的程序 dd ！他可以用来创建空的文件喔！详细的说明请先翻到下一章 压缩指令的运用 来查阅，这里鸟哥仅作一个简单的范例而已。 假设我要创建一个空的文件在 /srv/loopdev ，那可以这样做：

```
[root@study ~]# dd if=/dev/zero of=/srv/loopdev bs=1M count=512
512+0 records in   &lt;==读入 512 笔数据
512+0 records out  &lt;==输出 512 笔数据
536870912 Bytes （537 MB） copied, 12.3484 seconds, 43.5 MB/s
# 这个指令的简单意义如下：
# if    是 input file ，输入文件。那个 /dev/zero 是会一直输出 0 的设备！
# of    是 output file ，将一堆零写入到后面接的文件中。
# bs    是每个 block 大小，就像文件系统那样的 block 意义；
# count 则是总共几个 bs 的意思。所以 bs*count 就是这个文件的容量了！

[root@study ~]# ll -h /srv/loopdev
-rw-r--r--. 1 root root 512M Jun 25 19:46 /srv/loopdev 
```

dd 就好像在叠砖块一样，将 512 块，每块 1MB 的砖块堆叠成为一个大文件 （/srv/loopdev） ！ 最终就会出现一个 512MB 的文件！粉简单吧！

*   大型文件的格式化

默认 xfs 不能够格式化文件的，所以要格式化文件得要加入特别的参数才行喔！让我们来瞧瞧！

```
[root@study ~]# mkfs.xfs -f /srv/loopdev
[root@study ~]# blkid /srv/loopdev
/srv/loopdev: UUID="7dd97bd2-4446-48fd-9d23-a8b03ffdd5ee" TYPE="xfs" 
```

其实很简单啦！所以鸟哥就不输出格式化的结果了！要注意 UUID 的数值，未来会用到！

*   挂载

那要如何挂载啊？利用 mount 的特殊参数，那个 -o loop 的参数来处理！

```
[root@study ~]# mount -o loop UUID="7dd97bd2-4446-48fd-9d23-a8b03ffdd5ee" /mnt
[root@study ~]# df /mnt
Filesystem     1K-blocks  Used Available Use% Mounted on
/dev/loop0        520876 26372    494504   6% /mnt 
```

通过这个简单的方法，感觉上你就可以在原本的分区在不更动原有的环境下制作出你想要的分区就是了！ 这东西很好用的！尤其是想要玩 Linux 上面的“虚拟机”的话， 也就是以一部 Linux 主机再切割成为数个独立的主机系统时，类似 VMware 这类的软件， 在 Linux 上使用 xen 这个软件，他就可以配合这种 loop device 的文件类型来进行根目录的挂载，真的非常有用的喔！ ^_^

比较特别的是，CentOS 7.x 越来越聪明了，现在你不需要下达 -o loop 这个选项与参数，它同样可以被系统挂上来！ 连直接输入 blkid 都会列出这个文件内部的文件系统耶！相当有趣！不过，为了考虑向下兼容性，鸟哥还是建议你加上 loop 比较妥当喔！ 现在，请将这个文件系统永远的自动挂载起来吧！

```
[root@study ~]# nano /etc/fstab
/srv/loopdev  /data/file  xfs  defaults**,loop**   0 0
# 毕竟系统大多仅查询 block device 去找出 UUID 而已，因此使用文件创建的 filesystem，
# 最好还是使用原本的文件名来处理，应该比较不容易出现错误讯息的！

[root@study ~]# umount /mnt
[root@study ~]# mkdir /data/file
[root@study ~]# mount -a
[root@study ~]# df /data/file
Filesystem     1K-blocks  Used Available Use% Mounted on
/dev/loop0        520876 26372    494504   6% /data/file 
```

# 7.5 内存交换空间（swap）之创建

## 7.5 内存交换空间（swap）之创建

以前的年代因为内存不足，因此那个可以暂时将内存的程序拿到硬盘中暂放的内存交换空间 （swap） 就显的非常的重要！ 否则，如果突然间某支程序用掉你大部分的内存，那你的系统恐怕有损毁的情况发生喔！所以，早期在安装 Linux 之前，大家常常会告诉你： 安装时一定需要的两个 partition ，一个是根目录，另外一个就是 swap（内存交换空间）。关于内存交换空间的解释在第三章安装 Linux 内的磁盘分区时有约略提过，请你自行回头瞧瞧吧！

一般来说，如果硬件的配备资源足够的话，那么 swap 应该不会被我们的系统所使用到， swap 会被利用到的时刻通常就是实体内存不足的情况了。从第零章的计算机概论当中，我们知道 CPU 所读取的数据都来自于内存， 那当内存不足的时候，为了让后续的程序可以顺利的运行，因此在内存中暂不使用的程序与数据就会被挪到 swap 中了。 此时内存就会空出来给需要执行的程序载入。由于 swap 是用磁盘来暂时放置内存中的信息，所以用到 swap 时，你的主机磁盘灯就会开始闪个不停啊！

虽然目前（2015）主机的内存都很大，至少都有 4GB 以上啰！因此在个人使用上，你不要设置 swap 在你的 Linux 应该也没有什么太大的问题。 不过服务器可就不这么想了～由于你不会知道何时会有大量来自网络的要求，因此最好还是能够预留一些 swap 来缓冲一下系统的内存用量！ 至少达到“备而不用”的地步啊！

现在想像一个情况，你已经将系统创建起来了，此时却才发现你没有创建 swap ～那该如何是好呢？ 通过本章上面谈到的方法，你可以使用如下的方式来创建你的 swap 啰！

*   设置一个 swap partition
*   创建一个虚拟内存的文件

不啰唆，就立刻来处理处理吧！

### 7.5.1 使用实体分区创建 swap

创建 swap 分区的方式也是非常的简单的！通过下面几个步骤就搞定啰：

1.  分区：先使用 gdisk 在你的磁盘中分区出一个分区给系统作为 swap 。由于 Linux 的 gdisk 默认会将分区的 ID 设置为 Linux 的文件系统，所以你可能还得要设置一下 system ID 就是了。
2.  格式化：利用创建 swap 格式的“mkswap 设备文件名”就能够格式化该分区成为 swap 格式啰
3.  使用：最后将该 swap 设备启动，方法为：“swapon 设备文件名”。
4.  观察：最终通过 free 与 swapon -s 这个指令来观察一下内存的用量吧！

不啰唆，立刻来实作看看！既然我们还有多余的磁盘容量可以分区，那么让我们继续分区出 512MB 的磁盘分区吧！ 然后将这个磁盘分区做成 swap 吧！

*   1\. 先进行分区的行为啰！

```
[root@study ~]# gdisk /dev/vda
Command （? for help）: n
Partition number （6-128, default 6）:
First sector （34-83886046, default = 69220352） or {+-}size{KMGTP}:
Last sector （69220352-83886046, default = 83886046） or {+-}size{KMGTP}: +512M
Current type is 'Linux filesystem'
Hex code or GUID （L to show codes, Enter = 8300）: 8200
Changed type of partition to 'Linux swap'

Command （? for help）: p
Number  Start （sector）    End （sector）  Size       Code  Name
   6        69220352        70268927   512.0 MiB   8200  Linux swap  # 重点就是产生这东西！

Command （? for help）: w

Do you want to proceed? （Y/N）: y

[root@study ~]# partprobe
[root@study ~]# lsblk
NAME            MAJ:MIN RM  SIZE RO TYPE MOUNTPOINT
vda             252:0    0   40G  0 disk
.....（中间省略）.....
`-vda6          252:6    0  512M  0 part   # 确定这里是存在的才行！
# 鸟哥有简化输出喔！结果可以看到我们多了一个 /dev/vda6 可以使用于 swap 喔！ 
```

*   2\. 开始创建 swap 格式

```
[root@study ~]# mkswap /dev/vda6
Setting up swapspace version 1, size = 524284 KiB
no label, UUID=6b17e4ab-9bf9-43d6-88a0-73ab47855f9d
[root@study ~]# blkid /dev/vda6
/dev/vda6: UUID="6b17e4ab-9bf9-43d6-88a0-73ab47855f9d" TYPE="swap"
# 确定格式化成功！且使用 blkid 确实可以抓到这个设备了喔！ 
```

*   3\. 开始观察与载入看看吧！

```
[root@study ~]# free
              total        used        free      shared  buff/cache   available
Mem:        1275140      227244      330124        7804      717772      875536  # 实体内存
Swap:       1048572      101340      947232                                      # swap 相关
# 我有 1275140K 的实体内存，使用 227244K 剩余 330124K ，使用掉的内存有
# 717772K 用在缓冲/高速缓存的用途中。至于 swap 已经有 1048572K 啰！这样会看了吧？！

[root@study ~]# swapon /dev/vda6
[root@study ~]# free
              total        used        free      shared  buff/cache   available
Mem:        1275140      227940      329256        7804      717944      874752
Swap:       1572856      101260     1471596   &lt;==有看到增加了没？

[root@study ~]# swapon -s
Filename                 Type            Size    Used    Priority
/dev/dm-1                partition       1048572 101260  -1
/dev/vda6                partition       524284  0       -2
# 上面列出目前使用的 swap 设备有哪些的意思！

[root@study ~]# nano /etc/fstab
UUID="6b17e4ab-9bf9-43d6-88a0-73ab47855f9d"  swap  swap  defaults  0  0
# 当然要写入配置文件，只不过不是文件系统，所以没有挂载点！第二个字段写入 swap 即可。 
```

### 7.5.2 使用文件创建 swap

如果是在实体分区无法支持的环境下，此时前一小节提到的 loop 设备创建方法就派的上用场啦！ 与实体分区不一样的，这个方法只是利用 dd 去创建一个大文件而已。多说无益，我们就再通过文件创建的方法创建一个 128 MB 的内存交换空间吧！

*   1\. 使用 dd 这个指令来新增一个 128MB 的文件在 /tmp 下面：

```
[root@study ~]# dd if=/dev/zero of=/tmp/swap bs=1M count=128
128+0 records in
128+0 records out
134217728 Bytes （134 MB） copied, 1.7066 seconds, 78.6 MB/s

[root@study ~]# ll -h /tmp/swap
-rw-r--r--. 1 root root 128M Jun 26 17:47 /tmp/swap 
```

这样一个 128MB 的文件就创建妥当。若忘记上述的各项参数的意义，请回前一小节查阅一下啰！

*   2\. 使用 mkswap 将 /tmp/swap 这个文件格式化为 swap 的文件格式：

```
[root@study ~]# mkswap /tmp/swap
Setting up swapspace version 1, size = 131068 KiB
no label, UUID=4746c8ce-3f73-4f83-b883-33b12fa7337c
# 这个指令下达时请“特别小心”，因为下错字符控制，将可能使您的文件系统挂掉！ 
```

*   3\. 使用 swapon 来将 /tmp/swap 启动啰！

```
[root@study ~]# swapon /tmp/swap
[root@study ~]# swapon -s
Filename            Type            Size    Used    Priority
/dev/dm-1           partition       1048572 100380  -1
/dev/vda6           partition       524284  0       -2
/tmp/swap           file            131068  0       -3 
```

*   4\. 使用 swapoff 关掉 swap file，并设置自动启用

```
[root@study ~]# nano /etc/fstab
/tmp/swap  swap  swap  defaults  0  0
# 为何这里不要使用 UUID 呢？这是因为系统仅会查询区块设备 （block device） 不会查询文件！
# 所以，这里千万不要使用 UUID，不然系统会查不到喔！

[root@study ~]# swapoff /tmp/swap /dev/vda6
[root@study ~]# swapon -s
Filename                                Type            Size    Used    Priority
/dev/dm-1                               partition       1048572 100380  -1
# 确定已经回复到原本的状态了！然后准备来测试！！

[root@study ~]# swapon -a
[root@study ~]# swapon -s
# 最终你又会看正确的三个 swap 出现啰！这也才确定你的 /etc/fstab 设置无误！ 
```

说实话，swap 在目前的桌面电脑来讲，存在的意义已经不大了！这是因为目前的 x86 主机所含的内存实在都太大了 （一般入门级至少也都有 4GB 了），所以，我们的 Linux 系统大概都用不到 swap 这个玩意儿的。不过， 如果是针对服务器或者是工作站这些常年上线的系统来说的话，那么，无论如何，swap 还是需要创建的。

因为 swap 主要的功能是当实体内存不够时，则某些在内存当中所占的程序会暂时被移动到 swap 当中，让实体内存可以被需要的程序来使用。另外，如果你的主机支持电源管理模式， 也就是说，你的 Linux 主机系统可以进入“休眠”模式的话，那么， 运行当中的程序状态则会被纪录到 swap 去，以作为“唤醒”主机的状态依据！ 另外，有某些程序在运行时，本来就会利用 swap 的特性来存放一些数据段， 所以， swap 来是需要创建的！只是不需要太大！

# 7.6 文件系统的特殊观察与操作

## 7.6 文件系统的特殊观察与操作

文件系统实在是非常有趣的东西，鸟哥学了好几年还是很多东西不很懂呢！在学习的过程中很多朋友在讨论区都有提供一些想法！ 这些想法将他归纳起来有下面几点可以参考的数据呢！

### 7.6.1 磁盘空间之浪费问题

我们在前面的 EXT2 data block 介绍中谈到了一个 block 只能放置一个文件， 因此太多小文件将会浪费非常多的磁盘容量。但你有没有注意到，整个文件系统中包括 superblock, inode table 与其他中介数据等其实都会浪费磁盘容量喔！所以当我们在 /dev/vda4, /dev/vda5 创建起 xfs/ext4 文件系统时， 一挂载就立刻有很多容量被用掉了！

另外，不知道你有没有发现到，当你使用 ls -l 去查询某个目录下的数据时，第一行都会出现一个“total”的字样！ 那是啥东西？其实那就是该目录下的所有数据所耗用的实际 block 数量 * block 大小的值。 我们可以通过 ll -s 来观察看看上述的意义：

```
[root@study ~]# ll -sh
total 12K
4.0K -rw-------. 1 root root 1.8K May  4 17:57 anaconda-ks.cfg
4.0K -rw-r--r--. 2 root root  451 Jun 10  2014 crontab
 0 lrwxrwxrwx. 1 root root   12 Jun 23 22:31 crontab2 -&gt; /etc/crontab
4.0K -rw-r--r--. 1 root root 1.9K May  4 18:01 initial-setup-ks.cfg
 0 -rw-r--r--. 1 root root    0 Jun 16 01:11 test1
 0 drwxr-xr-x. 2 root root    6 Jun 16 01:11 test2
 0 -rw-rw-r--. 1 root root    0 Jun 16 01:12 test3
 0 drwxrwxr-x. 2 root root    6 Jun 16 01:12 test4 
```

从上面的特殊字体部分，那就是每个文件所使用掉 block 的容量！举例来说，那个 crontab 虽然仅有 451Bytes ， 不过他却占用了整个 block （每个 block 为 4K），所以将所有的文件的所有的 block 加总就得到 12KBytes 那个数值了。 如果计算每个文件实际容量的加总结果，其实只有不到 5K 而已～所以啰，这样就耗费掉好多容量了！未来大家在讨论小磁盘、 大磁盘，文件大小的损耗时，要回想到这个区块喔！ ^_^

### 7.6.2 利用 GNU 的 parted 进行分区行为（Optional）

虽然你可以使用 gdisk/fdisk 很快速的将你的分区切割妥当，不过 gdisk 主要针对 GPT 而 fdisk 主要支持 MBR ，对 GPT 的支持还不够！ 所以使用不同的分区时，得要先查询到正确的分区表才能用适合的指令，好麻烦！有没有同时支持的指令呢？有的！那就是 parted 啰！

![鸟哥的图示](img/vbird_face.gif "鸟哥的图示")

**Tips** 老实说，若不是后来有推出支持 GPT 的 gdisk，鸟哥其实已经爱用 parted 来进行分区行为了！虽然很多指令都需要同时开一个终端机去查 man page， 不过至少所有的分区表都能够支持哩！ ^_^

parted 可以直接在一行命令行就完成分区，是一个非常好用的指令！它常用的语法如下：

```
[root@study ~]# parted [设备] [指令 [参数]]
选项与参数：
指令功能：
          新增分区：mkpart [primary&#124;logical&#124;extended] [ext4&#124;vfat&#124;xfs] 开始 结束
          显示分区：print
          删除分区：rm [partition]

范例一：以 parted 列出目前本机的分区表数据
[root@study ~]# parted /dev/vda print
Model: Virtio Block Device （virtblk）         &lt;==磁盘接口与型号
Disk /dev/vda: 42.9GB                        &lt;==磁盘文件名与容量
Sector size （logical/physical）: 512B/512B    &lt;==每个扇区的大小
Partition Table: gpt                         &lt;==是 GPT 还是 MBR 分区
Disk Flags: pmbr_boot

Number  Start   End     Size    File system     Name                  Flags
 1      1049kB  3146kB  2097kB                                        bios_grub
 2      3146kB  1077MB  1074MB  xfs
 3      1077MB  33.3GB  32.2GB                                        lvm
 4      33.3GB  34.4GB  1074MB  xfs             Linux filesystem
 5      34.4GB  35.4GB  1074MB  ext4            Microsoft basic data
 6      35.4GB  36.0GB  537MB   linux-swap（v1）  Linux swap
[  1 ]  [  2 ]  [  3  ] [  4  ] [  5  ]         [  6  ] 
```

上面是最简单的 parted 指令功能简介，你可以使用“ man parted ”，或者是“ parted /dev/vda help mkpart ”去查询更详细的数据。比较有趣的地方在于分区表的输出。我们将上述的分区表示意拆成六部分来说明：

1.  Number：这个就是分区的号码啦！举例来说，1 号代表的是 /dev/vda1 的意思；
2.  Start：分区的起始位置在这颗磁盘的多少 MB 处？有趣吧！他以容量作为单位喔！
3.  End：此分区的结束位置在这颗磁盘的多少 MB 处？
4.  Size：由上述两者的分析，得到这个分区有多少容量；
5.  File system：分析可能的文件系统类型为何的意思！
6.  Name：就如同 gdisk 的 System ID 之意。

不过 start 与 end 的单位竟然不一致！好烦～如果你想要固定单位，例如都用 MB 显示的话，可以这样做：

```
[root@study ~]# parted /dev/vda unit mb print 
```

如果你想要将原本的 MBR 改成 GPT 分区表，或原本的 GPT 分区表改成 MBR 分区表，也能使用 parted ！ 但是请不要使用 vda 来测试！因为分区表格式不能转换！因此进行下面的测试后，在该磁盘的系统应该是会损毁的！ 所以鸟哥拿一颗没有使用的 U 盘来测试，所以文件名会变成 /dev/sda 喔！再讲一次！不要恶搞喔！

```
范例二：将 /dev/sda 这个原本的 MBR 分区表变成 GPT 分区表！（危险！危险！勿乱搞！无法复原！）
[root@study ~]# parted /dev/sda print
Model: ATA QEMU HARDDISK （scsi）
Disk /dev/sda: 2148MB
Sector size （logical/physical）: 512B/512B
Partition Table: msdos    # 确实显示的是 MBR 的 msdos 格式喔！

[root@study ~]# parted /dev/sda mklabel gpt
Warning: The existing disk label on /dev/sda will be destroyed and all data on 
this disk will be lost. Do you want to continue?
Yes/No? y

[root@study ~]# parted /dev/sda print
# 你应该就会看到变成 gpt 的模样！只是...后续的分区就全部都死掉了！ 
```

接下来我们尝试来创建一个全新的分区吧！再次的创建一个 512MB 的分区来格式化为 vfat，且挂载于 /data/win 喔！

```
范例三：创建一个约为 512MB 容量的分区
[root@study ~]# parted /dev/vda print
.....（前面省略）.....
Number  Start   End     Size    File system     Name                  Flags
.....（中间省略）.....
 6      35.4GB  36.0GB  537MB   linux-swap（v1）  Linux swap  # 要先找出来下一个分区的起始点！

[root@study ~]# parted /dev/vda mkpart primary fat32 36.0GB 36.5GB
# 由于新的分区的起始点在前一个分区的后面，所以当然要先找出前面那个分区的 End 位置！
# 然后再请参考 mkpart 的指令功能，就能够处理好相关的动作！
[root@study ~]# parted /dev/vda print
.....（前面省略）.....
Number  Start   End     Size    File system     Name                  Flags
 7      36.0GB  36.5GB  522MB                   primary

[root@study ~]# partprobe
[root@study ~]# lsblk /dev/vda7
NAME MAJ:MIN RM  SIZE RO TYPE MOUNTPOINT
vda7 252:7    0  498M  0 part      # 要确定它是真的存在才行！

[root@study ~]# mkfs -t vfat /dev/vda7
[root@study ~]# blkid /dev/vda7
/dev/vda7: SEC_TYPE="msdos" UUID="6032-BF38" TYPE="vfat"

[root@study ~]# nano /etc/fstab
UUID="6032-BF38"  /data/win  vfat  defaults   0  0

[root@study ~]# mkdir /data/win
[root@study ~]# mount -a
[root@study ~]# df /data/win
Filesystem     1K-blocks  Used Available Use% Mounted on
/dev/vda7         509672     0    509672   0% /data/win 
```

事实上，你应该使用 gdisk 来处理 GPT 分区就好了！不过，某些特殊时刻，例如你要自己写一只脚本，让你的分区全部一口气创建， 不需要 gdisk 一条一条指令去进行时，那么 parted 就非常有效果了！因为他可以直接进行 partition 而不需要跟用户互动！这就是它的最大好处！ 鸟哥还是建议，至少你要操作过几次 parted ，知道这家伙的用途！未来有需要再回来查！或使用 man parted 去处理喔！

# 7.7 重点回顾

## 7.7 重点回顾

*   一个可以被挂载的数据通常称为“文件系统, filesystem”而不是分区 （partition） 喔！
*   基本上 Linux 的传统文件系统为 Ext2 ，该文件系统内的信息主要有：

    *   superblock：记录此 filesystem 的整体信息，包括 inode/block 的总量、使用量、剩余量， 以及文件系统的格式与相关信息等；
    *   inode：记录文件的属性，一个文件占用一个 inode，同时记录此文件的数据所在的 block 号码；
    *   block：实际记录文件的内容，若文件太大时，会占用多个 block 。
*   Ext2 文件系统的数据存取为索引式文件系统（indexed allocation）
*   需要磁盘重组的原因就是文件写入的 block 太过于离散了，此时文件读取的性能将会变的很差所致。 这个时候可以通过磁盘重组将同一个文件所属的 blocks 汇整在一起。
*   Ext2 文件系统主要有：boot sector, superblock, inode bitmap, block bitmap, inode table, data block 等六大部分。
*   data block 是用来放置文件内容数据地方，在 Ext2 文件系统中所支持的 block 大小有 1K, 2K 及 4K 三种而已
*   inode 记录文件的属性/权限等数据，其他重要项目为： 每个 inode 大小均为固定，有 128/256Bytes 两种基本容量。每个文件都仅会占用一个 inode 而已； 因此文件系统能够创建的文件数量与 inode 的数量有关；
*   文件的 block 在记录文件的实际数据，目录的 block 则在记录该目录下面文件名与其 inode 号码的对照表；
*   日志式文件系统 （journal） 会多出一块记录区，随时记载文件系统的主要活动，可加快系统复原时间；
*   Linux 文件系统为增加性能，会让内存作为大量的磁盘高速缓存；
*   实体链接只是多了一个文件名对该 inode 号码的链接而已；
*   符号链接就类似 Windows 的捷径功能。
*   磁盘的使用必需要经过：分区、格式化与挂载，分别惯用的指令为：gdisk, mkfs, mount 三个指令
*   分区时，应使用 parted 检查分区表格式，再判断使用 fdisk/gdisk 来分区，或直接使用 parted 分区
*   为了考虑性能，XFS 文件系统格式化时，可以考虑加上 agcount/su/sw/extsize 等参数较佳
*   如果磁盘已无未分区的容量，可以考虑使用大型文件取代磁盘设备的处理方式，通过 dd 与格式化功能。
*   开机自动挂载可参考/etc/fstab 之设置，设置完毕务必使用 mount -a 测试语法正确否；

# 7.8 本章习题 - 第一题一定要做

## 7.8 本章习题 - 第一题一定要做

（ 要看答案请将鼠标移动到“答：”下面的空白处，按下左键圈选空白处即可察看 ）

*   情境仿真题一：复原本章的各例题练习，本章新增非常多 partition ，请将这些 partition 删除，恢复到原本刚安装好时的状态。

    *   目标：了解到删除分区需要注意的各项信息；
    *   前提：本章的各项范例练习你都必须要做过，才会拥有 /dev/vda4 ~ /dev/vda7 出现；
    *   需求：熟悉 gdisk, parated, umount, swapoff 等指令。 由于本章处理完毕后，将会有许多新增的 partition ，所以请删除掉这两个 partition 。删除的过程需要注意的是：

    *   需先以 free / swapon -s / mount 等指令查阅，要被处理的文件系统不可以被使用！ 如果有被使用，则你必须要使用 umount 卸载文件系统。如果是内存交换空间，则需使用 swapon -s 找出被使用的分区， 再以 swapoff 去卸载他！

        ```
        [root@study ~]# umount /data/ext4 /data/xfs /data/file /data/win
        [root@study ~]# swapoff /dev/vda6 /tmp/swap 
        ```

    *   观察 /etc/fstab ，该文件新增的行全部删除或注解！

        ```
        [root@study ~]# nano /etc/fstab
        .....（前面省略）.....
        /dev/mapper/centos-swap swap                 swap    defaults        0 0  # 从这行之后全删除
        UUID="e0fa7252-b374-4a06-987a-3cb14f415488"  /data/xfs  xfs   defaults      0 0
        /srv/loopdev                                 /data/file xfs   defaults,loop 0 0
        UUID="6b17e4ab-9bf9-43d6-88a0-73ab47855f9d"  swap       swap  defaults      0 0
        /tmp/swap                                    swap       swap  defaults      0 0
        UUID="6032-BF38"                             /data/win  vfat  defaults      0 0 
        ```

    *   使用“ gdisk /dev/vda ”删除，也可以使用“ parted /dev/vda rm 号码”删除喔！

        ```
        [root@study ~]# parted /dev/vda rm 7
        [root@study ~]# parted /dev/vda rm 6
        [root@study ~]# parted /dev/vda rm 5
        [root@study ~]# parted /dev/vda rm 4
        [root@study ~]# partprobe
        [root@study ~]# rm /tmp/swap /srv/loopdev 
        ```

*   情境仿真题二：由于我的系统原本分区的不够好，我的用户希望能够独立一个 filesystem 附挂在 /srv/myproject 目录下。 那你该如何创建新的 filesystem ，并且让这个 filesystem 每次开机都能够自动的挂载到 /srv/myproject ， 且该目录是给 project 这个群组共享的，其他人不可具有任何权限。且该 filesystem 具有 1GB 的容量。

    *   目标：理解文件系统的创建、自动挂载文件系统与专案开发必须要的权限；
    *   前提：你需要进行过第六章的情境仿真才可以继续本章；
    *   需求：本章的所有概念必须要清楚！ 那就让我们开始来处理这个流程吧！

    *   首先，我们必须要使用 gdisk /dev/vda 来创建新的 partition。 然后按下“ n ”，按下“Enter”选择默认的分区号码，再按“Enter”选择默认的启始柱面， 按下“+1G”创建 1GB 的磁盘分区，再按下“Enter”选择默认的文件系统 ID。 可以多按一次“p ”看看是否正确，若无问题则按下“w”写入分区表；

    *   避免重新开机，因此使用“ partprobe ”强制核心更新分区表；

    *   创建完毕后，开始进行格式化的动作如下：“mkfs.xfs -f /dev/vda4”，这样就 OK 了！

    *   开始创建挂载点，利用：“ mkdir /srv/myproject ”来创建即可；

    *   编写自动挂载的配置文件：“ nano /etc/fstab ”，这个文件最下面新增一行，内容如下： /dev/vda4 /srv/myproject xfs defaults 0 0

    *   测试自动挂载：“ mount -a ”，然后使用“ df /srv/myproject ”观察看看有无挂载即可！

    *   设置最后的权限，使用：“ chgrp project /srv/myproject ”以及“ chmod 2770 /srv/myproject ”即可。

* * *

简答题部分：

*   我们常常说，开机的时候，“发现磁盘有问题”，请问，这个问题的产生是“filesystem 的损毁”，还是“磁盘的损毁”？ 特别需要注意的是，如果您某个 filesystem 里面，由于操作不当，可能会造成 Superblock 数据的损毁， 或者是 inode 的架构损毁，或者是 block area 的记录遗失等等，这些问题当中，其实您的“磁盘”还是好好的， 不过，在磁盘上面的“文件系统”则已经无法再利用！一般来说，我们的 Linux 很少会造成 filesystem 的损毁， 所以，发生问题时，很可能整个磁盘都损毁了。但是，如果您的主机常常不正常断电，那么， 很可能磁盘是没问题的，但是，文件系统则有损毁之虞。此时，重建文件系统 （reinstall） 即可！ 不需要换掉磁盘啦！ ^_^
*   当我有两个文件，分别是 file1 与 file2 ，这两个文件互为 hard link 的文件，请问， 若我将 file1 删除，然后再以类似 vi 的方式重新创建一个名为 file1 的文件， 则 file2 的内容是否会被更动？ 这是来自网友的疑问。当我删除 file1 之后， file2 则为一个正规文件，并不会与他人共同分享同一个 inode 与 block ，因此，当我重新创建一个文件名为 file1 时，他所利用的 inode 与 block 都是由我们的 filesystem 主动去搜寻 meta data ，找到空的 inode 与 block 来创建的， 与原本的 file1 并没有任何关连性喔！所以，新建的 file1 并不会影响 file2 呢！

# 7.9 参考资料与延伸阅读

## 7.9 参考资料与延伸阅读

*   [[1]](#ac1)根据 The Linux Document Project 的文件所绘制的图示，详细的参考文献可以参考如下链接： Filesystem How-To: [`tldp.org/HOWTO/Filesystems-HOWTO-6.html`](http://tldp.org/HOWTO/Filesystems-HOWTO-6.html)
*   [[2]](#ac2)参考维基百科所得数据，链接网址如下： 条目： Ext2 介绍 [`en.wikipedia.org/wiki/Ext2`](http://en.wikipedia.org/wiki/Ext2)
*   [[3]](#ac3)PAVE 为一套秀图软件，常应用于数值模式的输出文件之再处理： PAVE 使用手册： [`www.ie.unc.edu/cempd/EDSS/pave_doc/index.shtml`](http://www.ie.unc.edu/cempd/EDSS/pave_doc/index.shtml)
*   [[4]](#ac4)详细的 inode 表格所定义的旗标可以参考如下链接： John's spec of the second extended filesystem: [`uranus.it.swin.edu.au/~jn/explore2fs/es2fs.htm`](http://uranus.it.swin.edu.au/~jn/explore2fs/es2fs.htm)
*   [[5]](#ac5)其他值得参考的 Ext2 相关文件系统文章之链接如下：
    *   “Design and Implementation of the Second Extended Filesystem ”[`e2fsprogs.sourceforge.net/ext2intro.html`](http://e2fsprogs.sourceforge.net/ext2intro.html)
    *   Whitepaper: Red Hat's New Journaling File System: ext3: [`www.redhat.com/support/wpapers/redhat/ext3/`](http://www.redhat.com/support/wpapers/redhat/ext3/)
    *   The Second Extended File System - An introduction: [`www.freeos.com/articles/3912/`](http://www.freeos.com/articles/3912/)
    *   ext3 or ReiserFS? Hans Reiser Says Red Hat's Move Is Understandable [`www.linuxplanet.com/linuxplanet/reports/3726/1/`](http://www.linuxplanet.com/linuxplanet/reports/3726/1/)
    *   文件系统的比较：维基百科：[`en.wikipedia.org/wiki/Comparison_of_file_systems`](http://en.wikipedia.org/wiki/Comparison_of_file_systems)
    *   [Ext2/Ext3 文件系统：http://linux.vbird.org/linux_basic/1010appendix_B.php](http://linux.vbird.org/linux_basic/1010appendix_B.php)
*   [[6]](#ac6)参考数据为：
    *   man xfs 详细内容
    *   xfs 官网：[`xfs.org/docs/xfsdocs-xml-dev/XFS_User_Guide/tmp/en-US/html/index.html`](http://xfs.org/docs/xfsdocs-xml-dev/XFS_User_Guide/tmp/en-US/html/index.html)
*   [[7]](#ac7)计算 RAID 的 sunit 与 swidth 的方式：
    *   计算 sunit 与 swidth 的方法： [`xfs.org/index.php/XFS_FAQ`](http://xfs.org/index.php/XFS_FAQ)
    *   计算 raid 与 sunit/swidth 部落客： [`blog.tsunanet.net/2011/08/mkfsxfs-raid10-optimal-performance.html`](http://blog.tsunanet.net/2011/08/mkfsxfs-raid10-optimal-performance.html)
*   [[8]](#ac8) Linux 核心所支持的硬件之设备代号（Major, Minor）查询： [`www.kernel.org/doc/Documentation/devices.txt`](https://www.kernel.org/doc/Documentation/devices.txt)
*   [9]与 Boot sector 及 Superblock 的探讨有关的讨论文章： The Second Extended File System: [`www.nongnu.org/ext2-doc/ext2.html`](http://www.nongnu.org/ext2-doc/ext2.html) Rob's ext2 documentation: [`www.landley.net/code/toybox/ext2.html`](http://www.landley.net/code/toybox/ext2.html)

2002/07/15：第一次完成 2003/02/07：重新编排与加入 FAQ 2004/03/15：修改 inode 的说明，并且将链接文件的说明移动至这个章节当中！ 2005/07/20：将旧的文章移动到 [这里](http://linux.vbird.org/linux_basic/0230filesystem/0230filesystem.php) 。 2005/07/22：将原本的附录一与附录二移动成为[附录 B](http://linux.vbird.org/linux_basic/1010appendix_B.php) 啦！ 2005/07/26：做了一个比较完整的修订，加入较完整的 ext3 的说明～ 2005/09/08：看到了一篇讨论，说明 FC4 在默认的环境中，使用 mkswap 会有问题。 2005/10/11：新增加了一个目录的 link 数量说明！ 2005/11/11：增加了一个 fsck 的 -f 参数在里头！ 2006/03/02：参考：[这里](http://www.tldp.org/LDP/sag/html/sag.html#FILESYSTEMS)的说明，将 ext2/ext3 最大文件系统由 16TB 改为 32TB。 2006/03/31：增加了虚拟内存的相关说明. 2006/05/01：将磁盘扇区的图做个修正，感谢网友 LiaoLiang 兄提供的信息！并加入参考文献！ 2006/06/09：增加 hard link 不能链接到目录的原因，详情参考：[`phorum.study-area.org/viewtopic.php?t=12235`](http://phorum.study-area.org/viewtopic.php?t=12235) 2006/06/28：增加关于 loop device 的相关说明呐！ 2006/09/08：加入 mknod 内的设备代号说明 ，以及列出 Linux 核心网站的设备代号查询。 2008/09/29：原本的 FC4 系列文章移动到[此处](http://linux.vbird.org/linux_basic/0230filesystem/0230filesystem-fc4.php) 2008/10/24：由于软盘的使用已经越来越少了，所以将 fdformat 及 mkbootdisk 拿掉了！ 2008/10/31：这个月事情好多～花了一个月才将数据整理完毕！修改幅度非常的大喔！ 2008/11/01：最后一节的利用 GNU 的 parted 进行分区行为误植为 GUN ！感谢网友阿贤的来信告知！ 2008/12/05：感谢网友 ian_chen 的告知，之前将 flash 当成 flush 了！真抱歉！已更新！ 2009/04/01：感谢讨论区网友[提供的说明](http://phorum.vbird.org/viewtopic.php?t=32583)， 鸟哥之前 superblock 这里写得不够好，有订正说明，请帮忙看看。 2009/08/19：加入两题情境仿真，重新修订一题简答题。 2009/08/30：加入 du 的 -S 说明中。 2015/06/17：将旧的基于 CentOS5 的版本移动到[这里](http://linux.vbird.org/linux_basic/0230filesystem/0230filesystem-centos5.php)。 2015/10/26：加上 noexec 的额外说明！感谢网友在讨论区的建议！