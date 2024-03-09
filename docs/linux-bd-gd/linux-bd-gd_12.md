# 9\. 结束

# 9.1\. 结束

顺利完成了！新 LFS 系统已经安装完成！我们祝贺你在闪亮的自定义的 Linux 系统上有更多的成功。

创建一个 `/etc/lfs-release` 文件是个好主意。有了这个文件，你能方便地在系统上(和告诉我们你需要询问的帮助指向)找到 LFS 是哪个版本。运行下面语句创建文件：

```
echo 6.2 > /etc/lfs-release 
```

# 9.2\. 看看你是第几个?

现在你已经完成本书全部步骤，你想成为 LFS 计算内的用户吗？ 从 [*http://www.linuxfromscratch.org/cgi-bin/lfscounter.cgi*](http://www.linuxfromscratch.org/cgi-bin/lfscounter.cgi) 上输入你的姓名和你使用的第一个 LFS 版本注册为 LFS 用户。

现在让我们重启进入 LFS 系统。

# 9.3\. 重启系统

现在所有的软件都安装完毕，到了重启你的电脑的时候了。但是，你应该要注意一些事。你用这本书创建的系统是相当最小限度的系统， 你很可能缺少需要的很多连续前进的功能。用一些来自 BLFS 书额外的包，在最近仍然使用的 chroot 环境中安装，你能离你自己的宿主机器，在你重启一次后进入新的 LFS 系统，可以用更多更佳的形式来继续安装。安装一个文本模式的浏览器，例如象 Lynx，你能在一个虚拟终端方便地观看 BLFS 书，安装包在另一个终端上。GPM 包将同样允许你在你地虚拟终端上完成拷贝/粘贴动作 最后，假如如果静态 IP 配置不能符合你的网络需求条件，安装 Dhcpcd 或 PPP 包，这点也是同样有用处的。

现在我们说完了，离开我们闪亮的新 LFS 安装，第一次启动！首先，退出 chroot 环境：

```
logout 
```

卸载虚拟文件系统：

```
umount -v $LFS/dev/pts
umount -v $LFS/dev/shm
umount -v $LFS/dev
umount -v $LFS/proc
umount -v $LFS/sys 
```

卸载 LFS 自己的文件系统：

```
umount -v $LFS 
```

如果多个分区被创建，卸载其他分区之前先卸载主要的一个，比如这个：

```
umount -v $LFS/usr
umount -v $LFS/home
umount -v $LFS 
```

现在，重启系统：

```
shutdown -r now 
```

早先的概要说明时，假如 GRUB 引导装载程序已经设置，按照启动菜单的设置可自动启动 *LFS 6.2* 。

当重启完毕，LFS 系统已经准备使用更多你需要增加的程序。

# 9.4\. 现在做什么?

感谢你阅读 LFS 书。我们希望你能在书中找到帮助和学习更多关于系统创建过程的知识。

现在 LFS 已经安装完毕，你可能觉得比较疑惑 "下一步怎么做？" 为了回答这问题，我们编制了一个资源列表给你。

*   维护

    所有软件的 Bugs 和安全通告。既然一个 LFS 系统从源代码中构建成，跟踪报告并更新软件取决你自己。这里有 几个在线资源跟踪报告，其中一些在下面：

    *   Freshmeat.net ([*http://freshmeat.net/*](http://freshmeat.net/))

        Freshmeat 能提醒你(通过 email)，在你的系统上最新安装包的新版本*。*

    *   [*CERT*](http://www.cert.org/) (Computer Emergency Response Team)

        CERT 是一个发布关于不同操作系统和应用程序的安全警告邮件列表。 订阅有效的信息在 [*http://www.us-cert.gov/cas/signup.html*](http://www.us-cert.gov/cas/signup.html).

    *   Bugtraq

        Bugtraq 是一个完全揭露计算机安全的邮件列表。它发布最新发现的安全发布公告，并偶尔提供为其修复的方法。有效发布信息在[*http://www.securityfocus.com/archive*](http://www.securityfocus.com/archive).

*   Beyond Linux From Scratch

    Beyond Linux From Scratch 书涵盖了 LFS 书之外的大量范围的软件安装。BLFS 项目在 [*http://www.linuxfromscratch.org/blfs/*](http://www.linuxfromscratch.org/blfs/).

*   LFS Hints

    LFS Hints 收藏了 LFS 社区自愿者提交的文档，hints 可用到的在[*http://www.linuxfromscratch.org/hints/list.html*](http://www.linuxfromscratch.org/hints/list.html).

*   邮件列表

    这是几个 LFS 邮件列表，假如你需要帮助可以订阅当前的开放，或者要为这个项目贡献力量和其他，查看 第一章节 得到更多信息。

*   Linux 文档项目

    Linux 文档项目的目的 (TLDP)是收集所有 Linux 发行版文档。 TLDP 的特点是收集了大量的 HOWTOs、guides,和 man pages。这个计划在 [*http://www.tldp.org/*](http://www.tldp.org/).