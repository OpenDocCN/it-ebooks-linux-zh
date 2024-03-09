# 第四章 - 日期设置

这一章介绍了 5 个关于日期设置的命令.

# Hack-33 设置系统时间

## 设置系统时间

`date`这个命令是用来显示/设置时间的.

如果要设置本系统的时间, 命令如下:

```
$ date {mmddhhmiyyyy.ss} 
```

其中:

*   `mm` 几月, 两位
*   `dd` 几号, 两位
*   `hh` 几点, 两位
*   `mi` 几分, 两位
*   `yyyy` 哪一年, 四位
*   `ss` 第几秒, 两位

例如, 设置日期为 1995 年 4 月 3 日 2 点 1 分 0 秒:

```
$ date 040302011995.00 #需要 root 权限 
```

如果只设置时间(不包括年月日):

```
$ date +%T -s "22:19:53"
$ date +%T%p -s "10:19:53PM" 
```

# Hack-34 设置硬件时间

## 设置硬件时间

硬件时间和系统时间有什么区别吗?

当然有了, 硬件时间是主板上的时间, 而系统时间则是系统里面的时间.

> 系统用两个时钟保存时间：硬件时钟和系统时钟。
> 
> 硬件时钟(即实时时钟 RTC 或 CMOS 时钟)仅能保存：年、月、日、时、分、秒这些时间数值，无法保存时间标准(UTC 或 localtime)和是否使用夏令时调节。
> 
> 系统时钟(即软件时间) 与硬件时间分别维护，保存了：时间、时区和夏令时设置。Linux 内核保存为自 UTC 时间 1970 年 1 月 1 日经过的秒数。系统启动之后，系统时钟与硬件时钟独立运行，Linux 通过时钟中断计数维护系统时钟。

via: [wiki.archlinux.org](https://wiki.archlinux.org/index.php/Time_%28%E7%AE%80%E4%BD%93%E4%B8%AD%E6%96%87%29)

如果你有双系统的话, 你就会发现, Linux 上的时间和 Windows 上的时间相差 8 个小时, 这是为什么呢? 因为两种系统读时间的姿势不对, 虽然他们都是从主板里面读取时间, 但是 Windows 默认的读法是直接读, 也就是说, 主板里写的是啥, 他就读成啥, 但是 Linux 呢, Linux 就会把主板里的时间换算成 UTC, UTC 是啥? 国际时间, 格林尼治时间, 然后看一下我们在中国,+8 区, 再加 8 个小时, 所以双系统的情况下就会相差 8 个小时了.

欲知详情, 请看: [linux 系统时间和硬件时钟问题](http://blog.gesha.net/archives/221/)

#### 几个命令:

*   显示硬件时间 `hwclock`
*   设置硬件时间为系统时间 `hwclock ––systohc`

# Hack-35 格式化日期

## 格式化日期

下面的例子是用不同的格式来显示当前日期:

```
➤ date
2016 年 01 月 04 日 星期一 16:56:44 CST
➤ date --date='now'
2016 年 01 月 04 日 星期一 16:56:55 CST
➤ date --date='tomorrow'
2016 年 01 月 05 日 星期二 16:56:59 CST
➤ date --date='yestoday'
date: invalid date ‘yestoday’
➤ date --date='today'
2016 年 01 月 04 日 星期一 16:57:11 CST
➤ date --date='1970-01-01 00:00:01 UTC +5 hours' +%s
18001
➤ date '+Current Date: %m/%d/%y%nCurrent Time:%H:%M:%S'
Current Date: 01/04/16
Current Time:16:57:25
➤ date +"%d-%m-%Y"
04-01-2016
➤ date +"%d/%m/%Y"
04/01/2016
➤ date +"%A,%B %d %Y"
星期一,一月 04 2016
➤ 
```

解释相关选项:

*   `%D` 日期 (mm/dd/yy)
*   `%d` 第几号 (01..31)
*   `%m` 月份 (01..12)
*   `%y` 年份的后两位 (00..99)
*   `%a` 周几 (Sun..Sat)
*   `%A` 周几 (Sunday..Saturday)
*   `%b` 月份 (Jan..Dec)
*   `%B` 月份 (January..December)
*   `%H` 几点 (00..23)
*   `%I` 几点 (01..12)
*   `%Y` 年份 (1970...)

`date`还有一个很有用的功能就是转换时间戳, 比如, 把现在的时间转换成 Unix 时间戳:

```
➤ date +%s
1451901927 
```

这个时间戳, 就是从 1970-1-1 数过来的秒数.

# Hack-36 显示过去的时间

## 显示过去的时间

这里没什么好说的.

```
$ date --date='3 seconds ago'
Thu Jan
1 08:27:00 PST 2009
$ date --date="1 day ago"
Wed Dec 31 08:27:13 PST 2008
$ date --date="1 days ago"
Wed Dec 31 08:27:18 PST 2008
$ date --date="1 month ago"
Mon Dec
1 08:27:23 PST 2008
$ date --date="1 year ago"
Tue Jan
1 08:27:28 PST 2008
$ date --date="yesterday"
Wed Dec 31 08:27:34 PST 2008
$ date --date="10 months 2 day ago"
Thu Feb 28 08:27:41 PST 2008 
```

# Hack-37 显示未来的时间

## 显示未来的时间

同样也没什么好说的.

```
$ date
Thu Jan
1 08:30:07 PST 2009
$ date --date='3 seconds'
Thu Jan
1 08:30:12 PST 2009
$ date --date='4 hours'
Thu Jan
1 12:30:17 PST 2009
$ date --date='tomorrow'
Fri Jan
2 08:30:25 PST 2009
$ date --date="1 day"
Fri Jan
2 08:30:31 PST 2009
$ date --date="1 days"
Fri Jan
2 08:30:38 PST 2009
$ date --date="2 days"
Sat Jan
3 08:30:43 PST 2009
$ date --date='1 month'
Sun Feb
1 08:30:48 PST 2009
$ date --date='1 week'
Thu Jan
8 08:30:53 PST 2009
$ date --date="2 months"
Sun Mar
1 08:30:58 PST 2009
$ date --date="2 years"
Sat Jan
1 08:31:03 PST 2011
$ date --date="next day"
Fri Jan
2 08:31:10 PST 2009
$ date --date="-1 days ago"
Fri Jan
2 08:31:15 PST 2009
$ date --date="this Wednesday"
Wed Jan
7 00:00:00 PST 2009 
```