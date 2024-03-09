# 第十章 - LAMP 套装

这一章介绍了 LAMP 的安装和配置.

*   **L** Linux
*   **A** Apache2
*   **M** Mysql
*   **P** PHP

[LAMP - 维基百科](https://wikipedia.kfd.me/wiki/LAMP)

# Hack-75 安装 Apache2

## 安装 Apache2

这一篇介绍了怎样安装带有 SSL 模块的 Apache2.

(然后作者说了一大段 apache2 的优点, 其实我个人还是比较倾向于 nginx 的, 轻量级, 配置简单, 而且高并发.)

#### 下载 Apache

从 [apache 官网](http://httpd.apache.org/download.cgi) 下载 apache 的安装包, 目前的版本是`2.4.18`(2015-12-14 发行)

```
➤ wget "http://mirrors.hust.edu.cn/apache//httpd/httpd-2.4.18.tar.bz2"
--2016-01-13 14:42:33--  http://mirrors.hust.edu.cn/apache//httpd/httpd-2.4.18.tar.bz2
Resolving mirrors.hust.edu.cn (mirrors.hust.edu.cn)... 202.114.18.160
Connecting to mirrors.hust.edu.cn (mirrors.hust.edu.cn)|202.114.18.160|:80... connected.
HTTP request sent, awaiting response... 200 OK
Length: 5181291 (4.9M) [application/octet-stream]
Saving to: ‘httpd-2.4.18.tar.bz2’

httpd-2.4.18.tar.bz 100%[=====================>]   4.94M  3.97MB/s   in 1.2s   

2016-01-13 14:42:34 (3.97 MB/s) - ‘httpd-2.4.18.tar.bz2’ saved [5181291/5181291]

➤ tar -jxf httpd-2.4.18.tar.bz2 
```

#### 安装 SSL 模块

```
➤ cd httpd-2.4.18/
➤ ./configure --help
`configure' configures this package to adapt to many kinds of systems.

Usage: ./configure [OPTION]... [VAR=VALUE]...

To assign environment variables (e.g., CC, CFLAGS...), specify them as
VAR=VALUE.  See below for descriptions of some of the useful variables.

Defaults for the options are specified in brackets.

Configuration:
  -h, --help              display this help and exit
      --help=short        display options specific to this package
      --help=recursive    display the short help of all the included packages
  -V, --version           display version information and exit
...
...
... 
```

配置的时候有好多选项, 这里我们要安装 SSL 支持, 所以:

```
./configure --enable-ssl --enable-so
make
make install 
```

这样就安装好了.

#### 在`httpd.conf`中开启 SSL

Apache 的配置文件保存在`/usr/local/apache2/conf`目录中,(如果是 apt-get 安装的话, 目录则在`/etc/apache2/conf`).

把配置文件中的`#Include conf/extra/httpd-ssl.conf`前面的注释符去掉保存即可.

`/usr/local/apache2/conf/extra/httpd-ssl.conf`这个文件里面保存的就是 ssl 的配置, 包括公钥私钥的存放位置:

```
# egrep 'server.crt|server.key' httpd-ssl.conf
SSLCertificateFile "/usr/local/apache2/conf/server.crt"
SSLCertificateKeyFile "/usr/local/apache2/conf/server.key" 
```

我们还需要创建一对公钥私钥才能让 apache2 正常运行, 所以:

#### 创建公私钥

```
openssl genrsa -des3 -out server.key 2048 
```

上面的命令创建了一个 2048 位的密钥, 其中有一步是需要你输入一个 4-1023 位长的密码, 记住这个密码, 以后要用到(以后也可以去掉密码的).

下一步就是创建一个 certificate request file (创建证书所用到的文件), 用到上面创建的密钥:

```
openssl req -new -key server.key -out server.csr 
```

最后就是创建一个自己签发的证书了:

```
openssl x509 -req -days 365 -in server.csr -signkey server.key -out server.crt 
```

证书的时长是 365 天.

#### 把证书复制过去

接着上面的步骤, 把创建的证书和密钥都放到 apache 的配置目录中:

```
cp server.key /usr/local/apache2/conf/
cp server.crt /usr/local/apache2/conf/ 
```

#### 开启 Apache

```
/usr/local/apache2/bin/apachectl start 
```

过程中需要输入刚才记录的密码:

```
Apache/2.2.17 mod_ssl/2.2.17 (Pass Phrase Dialog)
Server www.example.com:443 (RSA)
Enter pass phrase:

OK: Pass Phrase Dialog successful. 
```

上面说过这个密码可以去除, 这样就不需要每次开启 apache2 的时候都输入密码了, 具体怎样做呢? 谷歌会告诉你.

#### 扩展阅读

[How To Generate SSL Key, CSR and Self Signed Certificate For Apache](http://www.thegeekstuff.com/2009/07/linux-apache-mod-ssl-generate-key-csr-crt-file/)

# Hack-76 安装 PHP

## 安装 PHP

所有的 Linux 发行版都有 php, 你可以很简单的从软件仓库安装. 但是作者还是非常建议你下载最新的 PHP 源代码, 然后手动编译和安装. 为什么呢? 因为这样可以很好的升级 PHP 版本以及打各种补丁. 这一篇介绍了如何在 Linux 上从源码安装 PHP.

#### 前提需要

作者在这里要求事先装好 Apache2 和 MySQL, 但是我觉着这里没啥必要, 你也可以装 Nginx 啊, 也可以不需要 MySQL 啊, 所以你只要有一个可以运行 PHP 的容器即可.

即使没有容器, 也可以从命令行中运行 PHP 脚本.

#### 下载安装 PHP

从[PHP 官网](http://php.net/downloads.php)下载最新的 PHP 版本.

(作者在这里举的例子是 5.2.6, 现在早已超过这个版本了, 不过我现在在图书馆, 没网... 只能贴作者的代码)

```
# bzip2 -d php-5.2.6.tar.bz2
# tar xvf php-5.2.6.tar 
```

(两种不同的解压方式, 依据你下载的格式采用不同的姿势.)

可以通过`./configure --help`来查看所有的配置选项, 最常用的选项是`--prefix={install-dir-name}`, 从名字就可以看出, 这是用来确定安装目录的, 缺省选项是`/usr/local/lib`目录.

```
# cd php-5.2.6
# ./configure --help 
```

开始编译:

```
# ./configure --with-apxs2=/usr/local/apache2/bin/apxsv --with-mysql
# make
# make install
# cp php.ini-dist /usr/local/lib/php.ini 
```

#### 配置`httpd.conf`文件

在`/usr/local/apache2/conf/httpd.conf`文件中添加如下几行:

```
<FilesMatch "\.ph(p[2-6]?|tml)$">
SetHandler application/x-httpd-php
</FilesMatch> 
```

然后确认`LoadModule php5_module modules/libphp5.so`这一行代码在 PHP 安装的过程中添加到了`httpd.conf`文件中.

#### 确认安装成功

重启 Apache2:

```
# /usr/local/bin/apache2/apachectl restart 
```

然后在`/usr/local/apache2/htdocs`目录下添加一个文件:

```
# echo '<?php phpinfo(); ?>' >> /usr/local/apache2/htdocs/test.php 
```

如果打开浏览器, 查看`http://local-host/test.php`, 出现了 phpinfo 的相关内容, 那么就是配置好了.

#### 安装过程中可能会遇到的错误:

**Error 1 :** configure: error: xml2-config not found:

如果再安装过程中遇到了一下错误:

```
# ./configure --with-apxs2=/usr/local/apache2/bin/apxs
--with-mysql
Configuring extensions
checking whether to enable LIBXML support... yes
checking libxml2 install dir... no
checking for xml2-config path...
configure: error: xml2-config not found. Please check your
libxml2 installation. 
```

那么就需要你安装`libxml2-devel`和`zlib-devel`库:

```
# rpm -ivh /home/downloads/linux-iso/libxml2-devel-2.6.26-
2.1.2.0.1.i386.rpm /home/downloads/linux-iso/zlib-devel-
1.2.3-3.i386.rpm
Preparing...##################################### [100%]
1:zlib-devel##################################### [ 50%]
2:libxml2-devel################################## [100%] 
```

下载这些库并且安装上就好了.

**Error 2 :** configure: error: Cannot find MySQL header files.

如果你遇到了以下的错误:

```
# ./configure --with-apxs2=/usr/local/apache2/bin/apxs
--with-mysql
checking for MySQL UNIX socket location...
/var/lib/mysql/mysql.sock
configure: error: Cannot find MySQL header files under
yes. Note that the MySQL client library is not bundled
anymore! 
```

则说明你没有安装 MySQL, 安上就好了:

```
# rpm -ivh /home/downloads/MySQL-devel-community-5.1.25-
0.rhel5.i386.rpm
Preparing...###################################### [100%]
1:MySQL-devel-community########################### [100%] 
```

# Hack-77 安装 MySQL

## 安装 MySQL

**Ubuntu** 用户直接`sudo apt-get install mysql-server`就好了, 其中需要输入 root 的密码, 记住它.

这一篇里面作者大部分都是在充数...

并没有什么技巧可言...

不过我知道一个技巧:

每次登陆 mysql 的时候, 是不是都要输入`mysql -u root -p`, 然后再输入那繁杂的密码, 其实有两种方法可以解决这个问题:

第一个就是利用`alias` -> `alias mysql='mysql -uroot -pmypassword123'` 缺省的 host 是本机, 而且`-u`和`root`之间可以省略空格, `-p`和你的密码之间则不允许有空格.

第二个是利用`~/.my.cnf`:

```
➤ cat ~/.my.cnf 
[client]
user='root'
password='99b460e85139819e2f'
database='test' 
```

这样当你输入`mysql`的时候就会直接登陆了.

想知道更多? 请搜索 `my.cnf`.

# Hack-78 安装 LAMP 包

## 安装 LAMP 包

上面刚讲了作者建议从源码安装这几样东西, 这里又说了怎样用软件管理器来安装了.( 不冲突, 但是真的让人感觉他是在充数啊!!! )

### Yum

```
yum install httpd #安装 apache2

yum install mysql-server

yum install php

yum install php-mysql 
```

### Apt-get

```
apt-get install nginx #安装 nginx

apt-get install mysql-server #安装 mysql

apt-get install php5-fpm #安装 php

apt-get install php5-mysql #安装 php 的 mysql 模块 
```

安装其实并不复杂, 复杂的是配置. 各种优化, 各种参数, 各种变量.

# Hack-79 安装 XAMPP

## 安装 XAMPP

**XAMPP**是一个包含了 Apache2, php, mysql, perl 的集合包.

(类似于网上的 LAMP 脚本.)

从这里下载 [`sourceforge.net/projects/xampp/`](http://sourceforge.net/projects/xampp/)

我就直接贴作者的代码了, 其实没啥意思, 看看就好了:

```
# cd /opt
# tar xvzf xampp-linux-1.7.3a.tar.gz 
```

开启:

```
# /opt/lampp/lampp start 
```

关闭:

```
# /opt/lampp/lampp stop 
```

开启特定的 XAMPP 服务:

```
# /opt/lampp/lampp startapache
XAMPP: Starting Apache with SSL (and PHP5)...
Note: Use startmysql in the argument to start only mysql 
```

关闭:

```
# /opt/lampp/lampp stopmysql
XAMPP: Stopping MySQL... 
```

这里的`startapache`和`stopmysql`都是指特定的服务.

# Hack-80 Apache 安全设置

## Apache 安全设置

安装配置完 Apache 后, 进行一些安全配置还是很必要的.

黑客无所不能.

#### 以特定的用户运行 Apache

默认情况下, Apache 会以`nobody`或者`daemon`运行, 不过最好还是让 apahce 以一个没有权限的用户运行, 比如: apache.

创建 apache 用户和用户组:

```
groupadd apache
useradd -d /usr/local/apache2/htdocs -g apache -s /bin/false apache 
```

修改`httpd.conf`文件, 将运行 apache 的用户改为 apache.

```
# vi httpd.conf
User apache
Group apache 
```

#### 限制访问根目录

在`httpd.conf`配置如下代码以限制访问根目录:

```
<Directory />
    Options None
    Order deny,allow
    Deny from all
</Directory> 
```

#### 给`conf`和`bin`目录设置适当的权限

`conf`和`bin`目录只能允许认证过的用户访问. 创建一个用户组, 把那些允许访问和修改这两个目录的用户都添加进去是一个好主意.

```
groupadd apacheadmin #创建 admin 用户组

chown -R root:apacheadmin /usr/local/apache2/bin #设置目录权限
chmod -R 770 /usr/local/apache2/bin

$ vi /etc/group #给这个用户组添加特定的用户
apacheadmin:x:1121:ramesh,john 
```

#### 禁止目录浏览

如果不禁止目录浏览的话, 用户有可能看到你主目录或子目录下的文件列表.(现在默认都是不可浏览的了, 不过了解一下并不是坏事 :))

```
<Directory />
    Options None
    Order allow,deny
    Allow from all
</Directory>

# (or) #

<Directory />
    Options -Indexes
    Order allow,deny
    Allow from all
</Directory> 
```

#### 禁止`.htaccess`文件

这个文件对于 apache 来说比较重要, 一些重写规则以及配置都写在这里面, 如果允许用户在子目录下重写这个文件, 那么原有默认的`.htaccess`文件就不管用了. 这是我们不想看到的, 所以做这个限制很有必要.

```
<Directory />
    Options None
    AllowOverride None
    Order allow,deny
    Allow from all
</Directory> 
```

#### 扩展阅读

[10 Tips to Secure Your Apache Web Server on UNIX / Linux](http://www.thegeekstuff.com/2011/03/apache-hardening/)

# Hack-81 Apachectl 和 Httpd 技巧

## Apachectl 和 Httpd 技巧

安装完 Apache2 之后, 如果你想用`apachectl`和`httpd`来发挥他们最大的潜能, 那你就不能仅仅使用`start`, `stop`, `status`了. 下面的九个例子向你介绍了如何高效的使用它们.

#### 给 apachectl 设置一个不同的`httpd.conf`文件

一般来说, 当你需要设置一个不同的 apache2 指令时, 你需要修改原来的`httpd.conf`文件, 但是利用下面的方法, 你就可以新建一个, 而不是修改原来的, 以达到测试的目的.

```
apachectl -f conf/httpd.conf.debug
# or
httpd -k start -f conf/httpd.conf.debug 
```

看一下进程:

```
ps -ef | grep http
root 25080 1 0 23:26 00:00:00 /usr/sbin/httpd -f conf/httpd.conf.debug
apache 25099 25080 0 23:28 00:00:00 /usr/sbin/httpd -f conf/httpd.conf.debug 
```

如果这个`conf/httpd.conf.debug`文件没有问题的话, 你就可以把它复制为`conf/httpd.conf`, 然后重启 apache:

```
# cp httpd.conf.debug httpd.conf
# apachectl stop
# apachectl start 
```

#### 不修改`httpd.conf`文件就更换网页根目录

用`-c`选项可以很简单的更改网页的根目录, 而不必修改配置文件.

```
# httpd -k start -c "DocumentRoot /var/www/html_debug/" 
```

#### 提升日志记录的等级

用`-e`选项可以更改 apache2 记录的日志等级

```
# httpd -k start -e debug
[Sun Aug 17 13:53:06 2008] [debug] mod_so.c(246): loaded module auth_basic_module
[Sun Aug 17 13:53:06 2008] [debug] mod_so.c(246): loaded module auth_digest_module 
```

可用的等级有:`debug, info, notice, warn, error, crit, alert, emerg`.

#### 显示 apache 已编译的模块

```
# httpd -l
Compiled in modules:
core.c
prefork.c
http_core.c
mod_so.c 
```

#### 显示 apache 加载的模块(动态/静态)

上一个`-l`选项只显示了静态编译过的模块, 这个`-M`则会显示动态模块和共享模块.

```
# httpd -M
Loaded Modules:
core_module (static)
mpm_prefork_module (static)
http_module (static)
so_module (static)
auth_basic_module (shared)
auth_digest_module (shared)
authn_file_module (shared)
authn_alias_module (shared)
Syntax OK 
```

#### 显示`httpd.conf`内所有可用的指令

这个`-L`的选项类似于 httpd 的帮助扩展, 他会显示 httpd.conf 中可用的变量及其参数,

```
# httpd -L
HostnameLookups (core.c)
"on" to enable, "off" to disable reverse DNS lookups, or
"double" to enable double-reverse DNS lookups
Allowed in *.conf anywhere
ServerLimit (prefork.c)
Maximum value of MaxClients for this run of Apache
Allowed in *.conf only outside <Directory>, <Files> or
<Location>
KeepAlive (http_core.c)
Whether persistent connections should be On or Off
Allowed in *.conf only outside <Directory>, <Files> or
<Location>
LoadModule (mod_so.c)
a module name and the name of a shared object file to load
it from
Allowed in *.conf only outside <Directory>, <Files> or
<Location> 
```

#### 检测修改过的`httpd.conf`是否有错误

当我们新更改过 apache 的配置文件后, 我们应该先检测一下里面是否有错误的语法或者配置不正确的参数, 用`-t`这个参数:

```
# httpd -t -f conf/httpd.conf.debug
httpd: Syntax error on line 148 of
/etc/httpd/conf/httpd.conf.debug:
Cannot load /etc/httpd/modules/mod_auth_basicso into
server:
/etc/httpd/modules/mod_auth_basicso: cannot open shared
object file: No such file or directory
Once you fix the issue, it will display Syntax OK.

# httpd -t -f conf/httpd.conf.debug
Syntax OK 
```

#### 显示`httpd`编译参数

```
# httpd -V
Server version: Apache/2.2.9 (Unix)
Server built:
Jul 14 2008 15:36:56
Server's Module Magic Number: 20051115:15
Server loaded:
APR 1.2.12, APR-Util 1.2.12
Compiled using: APR 1.2.12, APR-Util 1.2.12
Architecture: 32-bit
Server MPM: Prefork
threaded:
forked:
no
yes (variable process count)
Server compiled with....
-D APACHE_MPM_DIR="server/mpm/prefork"
-D APR_HAS_SENDFILE
-D HTTPD_ROOT="/etc/httpd"
-D SUEXEC_BIN="/usr/sbin/suexec"
-D DEFAULT_PIDLOG="logs/httpd.pid"
-D DEFAULT_SCOREBOARD="logs/apache_runtime_status"
-D DEFAULT_LOCKFILE="logs/accept.lock"
-D DEFAULT_ERRORLOG="logs/error_log"
-D AP_TYPES_CONFIG_FILE="conf/mime.types"
-D SERVER_CONFIG_FILE="conf/httpd.conf"
... ...
... ... 
```

或者用`-v`显示很少的信息:

```
# httpd -v
Server version: Apache/2.2.9 (Unix)
Server built:
Jul 14 2008 15:36:56 
```

#### 按需加载特定的模块

有时候你并不需要加载全部的模块, 你只需要加载某个特定的模块, 怎么做呢?

还是修改`httpd.conf`文件:

```
<IfDefine load-ldap>
    LoadModule ldap_module modules/mod_ldap.so
    LoadModule authnz_ldap_module modules/mod_authnz_ldap.so
</IfDefine> 
```

当你测试 ldap 的时候你会想加载所有与 ldap 有关的模块, 所以:

```
# httpd -k start -e debug -Dload-ldap -f
/etc/httpd/conf/httpd.conf.debug
[Sun Aug 17 14:14:58 2008] [debug] mod_so.c(246): loaded
module ldap_module
[Sun Aug 17 14:14:58 2008] [debug] mod_so.c(246): loaded
module authnz_ldap_module
[Note: Pass -Dload-ldap, to load the ldap modules into
Apache] 
```

注意`-D`参数哦~

```
# apachectl start
[Note: Start the Apache normally, if you don't want to 
load the ldap modules.] 
```

# Hack-82 Apache 虚拟主机

## Apache 虚拟主机

其实这里也没什么, 就是增加了一个主机而已...如果是用 nginx 的话, 相当简单的...

我就一步一步翻译好了, 如果觉着简单就跳过这一节吧 :)

#### 1\. 取消`httpd-vhosts.conf`的注释

```
# vi /usr/local/apache2/conf/httpd.conf
Include conf/extra/httpd-vhosts.conf 
```

#### 2\. 配置虚拟主机

```
1\. `NameVirtualHost *:80` 声明端口号
2\. `<VirtualHost *:80> </VirtualHost>` 监听 80 端口, 里面的是主机配置选项.
3\. 下面的例子写了两个虚拟主机, 照猫画虎. 
```

```
# vi /usr/local/apache2/conf/extra/httpd-vhosts.conf
NameVirtualHost *:80
<VirtualHost *:80>
    ServerAdmin ramesh@thegeekstuff.com
    DocumentRoot "/usr/local/apache2/docs/thegeekstuff"
    ServerName thegeekstuff.com
    ServerAlias www.thegeekstuff.com
    ErrorLog "logs/thegeekstuff/error_log"
    CustomLog "logs/thegeekstuff/access_log" common
</VirtualHost>

<VirtualHost *:80>
    ServerAdmin ramesh@top5freeware.com
    DocumentRoot "/usr/local/apache2/docs/top5freeware"
    ServerName top5freeware.com
    ServerAlias www.top5freeware.com
    ErrorLog "logs/top5freeware/error_log"
    CustomLog "logs/top5freeware/access_log" common
</VirtualHost> 
```

(作者在这里打了一手好广告 :D)

#### 3\. 检测配置文件是否有语法错误

```
# /usr/local/apache2/bin/httpd -S
VirtualHost configuration:
Syntax OK 
```

上面的是配置正确的, 如果出错了就会报错:

```
# /usr/local/apache2/bin/httpd -S
Warning: DocumentRoot
[/usr/local/apache2/docs/top5freeware] does not exist
Warning: ErrorLog [/usr/local/apache2/logs/thegeekstuff]
does not exist
Syntax OK 
```

#### 4\. 重启测试

```
# /usr/local/apache2/bin/apachectl restart 
```

apache2 是通过 HTTP 的 Host 来区分不通的主机的. 你可以在本地的`/etc/hosts`文件中写好要测试的网址和对应的 ip(127.0.0.1), 然后浏览器访问不通的网址, 虽然都是同一个 ip 地址(127.0.0.1), 但是却是不同的网站.

# Hack-83 循环转存日志

## 循环转存日志

其实我也不知道怎么翻译才好... 英文叫做 rotate log, 大概的意思是按照时间或大小将日志循环, 如果到了设定的日期或者日志的大小达到阀值之后, 就会自动将日志保存到别处, 然后开启一个新的日志, 这样就不会使得日志过于庞大. 如果按照时间循环的话, 就便于日志的整理和归档.

不仅是 apache, 好多服务都可以这样弄, 配置文件都在`/etc/logrotate.d/`目录下:

```
➤ ls /etc/logrotate.d/
apport       dpkg          php5-fpm  rsyslog            unattended-upgrades
apt          mysql-server  pm-utils  speech-dispatcher  upstart
cups-daemon  nginx         ppp       ufw
➤ 
```

作者在这里介绍了 apache 的配置:

```
# vi /etc/logrotate.d/apache
/usr/local/apache2/logs/access_log
/usr/local/apache2/logs/error_log {
    size 100M
    compress
    dateext
    maxage 30
    postrotate
      /usr/bin/killall -HUP httpd
      ls -ltr /usr/local/apache2/logs | mail -s
"$HOSTNAME: Apache restarted and log files rotated"
ramesh@thegeekstuff.com
    endscript
} 
```

说明:

*   `size 100M` 当日志文件到了 100M, 系统就会转存日志. 100M 可以改成 100K, 100G.
*   `compress` 压缩转存的日志文件
*   `dateext` 给转存过的日志文件名添加日期
*   `maxage` 说明转存的日志文件最多保存多久
*   `postrotate and endscript` 在这两个参数之间的命令将会在转存完毕后执行. 这里是把日志通过邮件发送了出去.

添加完了可以测试下效果:

```
# /etc/cron.daily/logrotate 
```

然后:

```
# ls /usr/local/apache2/logs
access_log
error_log
access_log-20110716.gz
error_log-20110716.gz 
```