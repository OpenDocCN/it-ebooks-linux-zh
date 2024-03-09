# 三十四、Nginx 安装和配置

## Nginx 说明

*   Nginx 是一个很强大的高性能 Web 和反向代理服务器，常被我们用作负载均衡服务器，也可以作为邮件代理服务器
*   Nginx WIKI：[`zh.wikipedia.org/zh/Nginx`](https://zh.wikipedia.org/zh/Nginx)
*   Nginx 百科：[`baike.baidu.com/item/nginx`](http://baike.baidu.com/item/nginx)
*   Nginx 官网：[`nginx.org/en/`](http://nginx.org/en/)
*   Nginx 官网下载：[`nginx.org/en/download.html`](http://nginx.org/en/download.html)
    *   源码包方式下载：[`nginx.org/en/download.html`](http://nginx.org/en/download.html)，注意该页面的：`Stable version`，这个表示稳定版本，2016-03-22 最新版本是：`nginx-1.8.1`，这是一个 **tar.gz** 的文件链接。
    *   构建包方式下载：[`nginx.org/en/linux_packages.html#stable`](http://nginx.org/en/linux_packages.html#stable)
*   Nginx 文档：
    *   优先：[`www.nginx.com/resources/wiki/`](https://www.nginx.com/resources/wiki/)
    *   次要：[`nginx.org/en/docs/`](http://nginx.org/en/docs/)
*   Nginx 模块地址：[`www.nginx.com/resources/wiki/modules/`](https://www.nginx.com/resources/wiki/modules/)

## 来自网络上的一个好介绍

*   来源：[`help.aliyun.com/knowledge_detail/6703521.html?spm=5176.788314854.2.2.CdMGlB`](https://help.aliyun.com/knowledge_detail/6703521.html?spm=5176.788314854.2.2.CdMGlB)

> *   传统上基于进程或线程模型架构的 Web 服务通过每进程或每线程处理并发连接请求，这势必会在网络和 I/O 操作时产生阻塞，其另一个必然结果则是对内存或 CPU 的利用率低下。生成一个新的进程/线程需要事先备好其运行时环境，这包括为其分配堆内存和栈内存，以及为其创建新的执行上下文等。这些操作都需要占用 CPU，而且过多的进程/线程还会带来线程抖动或频繁的上下文切换，系统性能也会由此进一步下降。
> *   在设计的最初阶段，Nginx 的主要着眼点就是其高性能以及对物理计算资源的高密度利用，因此其采用了不同的架构模型。受启发于多种操作系统设计中基于“事件”的高级处理机制，nginx 采用了模块化、事件驱动、异步、单线程及非阻塞的架构，并大量采用了多路复用及事件通知机制。在 Nginx 中，连接请求由为数不多的几个仅包含一个线程的进程 Worker 以高效的回环(run-loop)机制进行处理，而每个 Worker 可以并行处理数千个的并发连接及请求。
> *   如果负载以 CPU 密集型应用为主，如 SSL 或压缩应用，则 Worker 数应与 CPU 数相同；如果负载以 IO 密集型为主，如响应大量内容给客户端，则 Worker 数应该为 CPU 个数的 1.5 或 2 倍。
> *   Nginx 会按需同时运行多个进程：一个主进程(Master)和几个工作进程(Worker)，配置了缓存时还会有缓存加载器进程(Cache Loader)和缓存管理器进程(Cache Manager)等。所有进程均是仅含有一个线程，并主要通过“共享内存”的机制实现进程间通信。主进程以 root 用户身份运行，而 Worker、Cache Loader 和 Cache manager 均应以非特权用户身份运行。
> *   主进程主要完成如下工作：
>     *   1.读取并验正配置信息；
>     *   2.创建、绑定及关闭套接字；
>     *   3.启动、终止及维护 worker 进程的个数；
>     *   4.无须中止服务而重新配置工作特性；
>     *   5.控制非中断式程序升级，启用新的二进制程序并在需要时回滚至老版本；
>     *   6.重新打开日志文件，实现日志滚动；
>     *   7.编译嵌入式 perl 脚本；
> *   Worker 进程主要完成的任务包括：
>     *   1.接收、传入并处理来自客户端的连接；
>     *   2.提供反向代理及过滤功能；
>     *   3.nginx 任何能完成的其它任务；
> *   Cache Loader 进程主要完成的任务包括：
>     *   1.检查缓存存储中的缓存对象；
>     *   2.使用缓存元数据建立内存数据库；
> *   Cache Manager 进程的主要任务：
>     *   1.缓存的失效及过期检验；

## Nginx 源码编译安装

*   官网下载最新稳定版本 **1.8.1**，大小：814K
*   官网安装说明：[`www.nginx.com/resources/wiki/start/topics/tutorials/install/`](https://www.nginx.com/resources/wiki/start/topics/tutorials/install/)
*   源码编译配置参数说明：
    *   [`www.nginx.com/resources/wiki/start/topics/tutorials/installoptions/`](https://www.nginx.com/resources/wiki/start/topics/tutorials/installoptions/)
    *   [`nginx.org/en/docs/configure.html`](http://nginx.org/en/docs/configure.html)
*   开始安装：

    *   安装依赖包：`yum install -y gcc gcc-c++ pcre pcre-devel zlib zlib-devel openssl openssl-devel`
    *   预设几个文件夹，方便等下安装的时候有些文件可以进行存放：
        *   `mkdir -p /usr/local/nginx /var/log/nginx /var/temp/nginx`
    *   下载源码包：``wget http://nginx.org/download/nginx-1.8.1.tar.gz`
    *   解压：`tar zxvf nginx-1.8.1.tar.gz`
    *   进入解压后目录：`cd nginx-1.8.1/`
    *   编译配置：

    `ini ./configure \ --prefix=/usr/local/nginx \ --pid-path=/var/local/nginx/nginx.pid \ --lock-path=/var/lock/nginx/nginx.lock \ --error-log-path=/var/log/nginx/error.log \ --http-log-path=/var/log/nginx/access.log \ --with-http_gzip_static_module \ --http-client-body-temp-path=/var/temp/nginx/client \ --http-proxy-temp-path=/var/temp/nginx/proxy \ --http-fastcgi-temp-path=/var/temp/nginx/fastcgi \ --http-uwsgi-temp-path=/var/temp/nginx/uwsgi \ --http-scgi-temp-path=/var/temp/nginx/scgi`

    *   编译：`make`
    *   安装：`make install`
*   启动 Nginx

    *   先检查是否在 /usr/local 目录下生成了 Nginx 等相关文件：`cd /usr/local/nginx;ll`，正常的效果应该是显示这样的：

    `nginx drwxr-xr-x. 2 root root 4096 3 月 22 16:21 conf drwxr-xr-x. 2 root root 4096 3 月 22 16:21 html drwxr-xr-x. 2 root root 4096 3 月 22 16:21 sbin`

    *   假设有生成对应的文件，那我们就删掉刚刚安装的解压包：`rm -rf /opt/setups/nginx-1.8.1`
    *   停止防火墙：`service iptables stop`
        *   或是把 80 端口加入到的排除列表：
        *   `sudo iptables -A INPUT -p tcp -m tcp --dport 80 -j ACCEPT`
        *   `sudo service iptables save`
        *   `sudo service iptables restart`
    *   启动：`/usr/local/nginx/sbin/nginx`，启动完成 shell 是不会有输出的
    *   检查 时候有 Nginx 进程：`ps aux | grep nginx`，正常是显示 3 个结果出来
    *   检查 Nginx 是否启动并监听了 80 端口：`netstat -ntulp | grep 80`
    *   访问：`192.168.1.114`，如果能看到：`Welcome to nginx!`，即可表示安装成功
    *   检查 Nginx 启用的配置文件是哪个：`/usr/local/nginx/sbin/nginx -t`
    *   刷新 Nginx 配置后重启：`/usr/local/nginx/sbin/nginx -s reload`
    *   停止 Nginx：`/usr/local/nginx/sbin/nginx -s stop`

## Nginx 配置

## Nginx 在 **1.8.1** 版本下的默认配置（去掉注释）

```
worker_processes  1;

events {
    worker_connections  1024;
}

http {
    include       mime.types;
    default_type  application/octet-stream;

    sendfile        on;
    keepalive_timeout  65;

    server {
        listen       80;
        server_name  localhost;

        location / {
            root   html;
            index  index.html index.htm;
        }

        error_page   500 502 503 504  /50x.html;
        location = /50x.html {
            root   html;
        }
    }
}
```

### HTTP 服务，虚拟主机

*   停止防火墙：`service iptables stop`，防止出现特别干扰
*   编辑默认的配置文件：`vim /usr/local/nginx/conf/nginx.conf`
*   设置两个虚拟主机（通过**端口**来区分开）

```
worker_processes  1;

events {
    worker_connections  1024;
}

http {
    include       mime.types;
    default_type  application/octet-stream;

    sendfile        on;
    keepalive_timeout  65;

    # 一个 server 代表一个虚拟主机
    server {
        listen       80;
        server_name  localhost;

        location / {
            # 虚拟机根目录是 /usr/local/nginx/html 目录
            root   html;
            # 虚拟机首页是 /usr/local/nginx/html 目录下这两个文件
            index  index.html index.htm;
        }

        error_page   500 502 503 504  /50x.html;
        location = /50x.html {
            root   html;
        }
    }

    server {
        # 第二个虚拟机的端口是 90，服务地址还是本地
        listen       90;
        server_name  localhost;

        location / {
            root   html90;
            index  index.html index.htm;
        }

        error_page   500 502 503 504  /50x.html;
        location = /50x.html {
            root   html;
        }
    }
}
```

*   设置两个虚拟主机（通过**域名**来区分开）

```
worker_processes  1;

events {
    worker_connections  1024;
}

http {
    include       mime.types;
    default_type  application/octet-stream;

    sendfile        on;
    keepalive_timeout  65;

    # 一个 server 代表一个虚拟主机
    server {
        listen       80;
        # 两个虚拟主机都使用 80 端口，设置不同域名
        server_name  code.youmeek.com;

        location / {
            # 虚拟机根目录是 /usr/local/nginx/html 目录
            root   html;
            # 虚拟机首页是 /usr/local/nginx/html 目录下这两个文件
            index  index.html index.htm;
        }

        error_page   500 502 503 504  /50x.html;
        location = /50x.html {
            root   html;
        }
    }

    server {
        listen       80;
        # 两个虚拟主机都使用 80 端口，设置不同域名
        server_name  i.youmeek.com;

        location / {
            root   html-i;
            index  index.html index.htm;
        }

        error_page   500 502 503 504  /50x.html;
        location = /50x.html {
            root   html;
        }
    }
}
```

### 反向代理和负载均衡

*   最精简的环境：一台虚拟机

    *   1 个 JDK
    *   1 个 Nginx
    *   2 个 Tomcat
*   Nginx 配置：

```
worker_processes  1;

events {
    worker_connections  1024;
}

http {
    include       mime.types;
    default_type  application/octet-stream;

    sendfile        on;
    keepalive_timeout  65;

    # 自己定义的两个 tomcat 请求地址和端口
    # 也就是当浏览器请求：tomcat.youmeek.com 的时候从下面这两个 tomcat 中去找一个进行转发
    upstream tomcatCluster {
        server 192.168.1.114:8080;
        server 192.168.1.114:8081;

        # 添加 weight 字段可以表示权重，值越高权重越大，默认值是 1，最大值官网没说，一般如果设置也就设置 3,5,7 这样的数
        # 官网：https://www.nginx.com/resources/admin-guide/load-balancer/#weight
        # server 192.168.1.114:8080 weight=2;
        # server 192.168.1.114:8081 weight=1;
    }

    server {
        listen       80;
        server_name  tomcat.youmeek.com;

        location / {
            proxy_pass   http://tomcatCluster;
            index  index.html index.htm;
        }
    }
}
```

### HTTP 服务，绑定多个域名

*   [`www.ttlsa.com/nginx/use-nginx-proxy/`](https://www.ttlsa.com/nginx/use-nginx-proxy/)

### 安装第三方模块

### 生成规格图

### 启用 Gzip 压缩

### 防盗链

*   [`help.aliyun.com/knowledge_detail/5974693.html?spm=5176.788314853.2.18.s4z1ra`](https://help.aliyun.com/knowledge_detail/5974693.html?spm=5176.788314853.2.18.s4z1ra)

### Nginx 禁止特定用户代理（User Agents）访问，静止指定 IP 访问

*   [`www.ttlsa.com/nginx/how-to-block-user-agents-using-nginx/`](https://www.ttlsa.com/nginx/how-to-block-user-agents-using-nginx/)
*   [`help.aliyun.com/knowledge_detail/5974693.html?spm=5176.788314853.2.18.s4z1ra`](https://help.aliyun.com/knowledge_detail/5974693.html?spm=5176.788314853.2.18.s4z1ra)

### Nginx 缓存

### Nginx 自动分割日志文件

### Nginx 处理跨域请求

### 安全相预防

在配置文件中设置自定义缓存以限制缓冲区溢出攻击的可能性 client_body_buffer_size 1K; client_header_buffer_size 1k; client_max_body_size 1k; large_client_header_buffers 2 1k;

1.  将 timeout 设低来防止 DOS 攻击 所有这些声明都可以放到主配置文件中。 client_body_timeout 10; client_header_timeout 10; keepalive_timeout 5 5; send_timeout 10;

2.  限制用户连接数来预防 DOS 攻击 limit_zone slimits $binary_remote_addr 5m; limit_conn slimits 5;

### 杂七杂八

*   [nginx 实现简体繁体字互转以及中文转拼音](https://www.ttlsa.com/nginx/nginx-modules-ngx_set_cconv/)
*   [nginx 记录分析网站响应慢的请求(ngx_http_log_request_speed)](https://www.ttlsa.com/nginx/nginx-modules-ngx_http_log_request_speed/)
*   [nginx 空白图片(empty_gif 模块)](https://www.ttlsa.com/nginx/nginx-modules-empty_gif/)

## 资料

*   [`help.aliyun.com/knowledge_detail/5974693.html?spm=5176.788314853.2.18.s4z1ra`](https://help.aliyun.com/knowledge_detail/5974693.html?spm=5176.788314853.2.18.s4z1ra)