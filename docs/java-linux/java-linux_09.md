# 二十五、Tomcat 8 安装和配置、优化

## Tomcat 8 安装

*   Tomcat 8 安装
    *   官网：[`tomcat.apache.org/`](http://tomcat.apache.org/)
    *   Tomcat 8 官网下载：[`tomcat.apache.org/download-80.cgi`](http://tomcat.apache.org/download-80.cgi)
    *   此时（20160207） Tomcat 8 最新版本为：`apache-tomcat-8.0.32.tar.gz`
    *   我个人习惯 `/opt` 目录下创建一个目录 `setups` 用来存放各种软件安装包；在 `/usr` 目录下创建一个 `program` 用来存放各种解压后的软件包，下面的讲解也都是基于此习惯
    *   我个人已经使用了第三方源：`EPEL、RepoForge`，如果你出现 `yum install XXXXX` 安装不成功的话，很有可能就是你没有相关源，请查看我对源设置的文章
    *   Tomcat 8 下载：`wget http://apache.fayea.com/tomcat/tomcat-8/v8.0.32/bin/apache-tomcat-8.0.32.tar.gz`
    *   压缩包解压：`tar -zxvf apache-tomcat-8.0.32.tar.gz`
    *   移到解压出来文件夹到 /usr 下：`mv apache-tomcat-8.0.32/ /usr/program/`
    *   为了方便，修改解压目录的名字：`mv /usr/program/apache-tomcat-8.0.32/ /usr/program/tomcat8/`
*   设置 Iptables 规则（这一步是必须设置的）：
    *   一种方式：先关闭 iptables，防止出现拦截问题而测试不了：`service iptables stop`
    *   一种方式：在 iptables 中添加允许规则（Tomcat 默认端口是 8080）：
        *   添加规则：`sudo iptables -I INPUT -p tcp -m tcp --dport 8080 -j ACCEPT`
        *   保存规则：`sudo /etc/rc.d/init.d/iptables save`
        *   重启 iptables：`sudo service iptables restart`
*   测试安装好后的 Tomcat：
    *   启动 Tomcat：`sh /usr/program/tomcat8/bin/startup.sh ; tail -200f /usr/program/tomcat8/logs/catalina.out`
    *   访问：`http://服务器 IP 地址:8080/`
    *   停止 Tomcat：`sh /usr/program/tomcat8/bin/shutdown.sh`

## Tomcat 8 配置

*   设置 Tomcat 环境变量：

    *   `sudo vim /etc/profile`
    *   最文件最尾巴添加下面内容：

    ``` ini CATALINA_HOME=/usr/program/tomcat8 export CATALINA_HOME

    # 第二十五章 下面参数根据自己机子内存进行配置

    # 第二十五章 CATALINA_OPTS 只配置给 Tomcat 的 VM

    # 第二十五章 JAVA_OPTS 配置给所有调用 Java 的程序的 VM

    # 第二十五章 有 CATALINA_OPTS 的时候 Tomcat 优先读这个

    # 第二十五章 其中：-server 表示启用 jdk 的 server 版，一定要作为第一个参数，在多个 CPU 时性能佳;

    CATALINA_OPTS="-server -Xms528m -Xmx528m -XX:PermSize=256m -XX:MaxPermSize=358m" export CATALINA_OPTS

    # 第二十五章 设置生成的 PID 位置

    CATALINA_PID=$CATALINA_HOME/catalina.pid export CATALINA_PID ```

    *   刷新变量配置：`source /etc/profile`
*   如果使用 `shutdown.sh` 无法停止 tomcat，可以修改其配置：`vim /usr/program/tomcat8/bin/shutdown.sh`
    *   把最尾巴这一行：`exec "$PRGDIR"/"$EXECUTABLE" stop "$@"`
    *   改为：`exec "$PRGDIR"/"$EXECUTABLE" stop 10 -force`

## Tomcat 8 优化

*   官网标准答案：[`tomcat.apache.org/tomcat-8.0-doc/config/http.html`](https://tomcat.apache.org/tomcat-8.0-doc/config/http.html)
*   官网帮助文档下载（该资料在：`/tomcat-8.0-doc/config/http.html`）：`wget http://mirror.bit.edu.cn/apache/tomcat/tomcat-8/v8.0.32/bin/apache-tomcat-8.0.32-fulldocs.tar.gz`
*   编辑配置文件：`vim /usr/program/tomcat8/conf/server.xml`
    *   修改链接参数：
        *   默认值： `xml <Connector port="8080" protocol="HTTP/1.1" connectionTimeout="20000" redirectPort="8443" />`
        *   修改为： `xml <Connector port="8080" protocol="org.apache.coyote.http11.Http11Nio2Protocol" connectionTimeout="20000" redirectPort="8443" enableLookups="false" acceptCount="100" maxThreads="200" minSpareThreads="10" compression="on" compressionMinSize="2048" compressableMimeType="text/html,text/xml,text/plain,text/css,text/javascript,application/javascript" URIEncoding="utf-8" />`
        *   重点参数解释：
            *   protocol，Tomcat 8 设置 nio2 更好：org.apache.coyote.http11.Http11Nio2Protocol
            *   protocol，Tomcat 6 设置 nio 更好：org.apache.coyote.http11.Http11NioProtocol
            *   enableLookups，禁用 DNS 查询
            *   acceptCount，指定当所有可以使用的处理请求的线程数都被使用时，可以放到处理队列中的请求数，超过这个数的请求将不予处理，默认设置 100
            *   maxThreads，最大并发数，默认设置 200
            *   minSpareThreads，Tomcat 初始化时创建的线程数，默认设置 10

## 资料

*   [`www.jikexueyuan.com/course/2064_3.html?ss=1`](http://www.jikexueyuan.com/course/2064_3.html?ss=1)
*   [`www.wellho.net/mouth/2163_CATALINA-OPTS-v-JAVA-OPTS-What-is-the-difference-.html`](http://www.wellho.net/mouth/2163_CATALINA-OPTS-v-JAVA-OPTS-What-is-the-difference-.html)
*   [`blog.csdn.net/sunlovefly2012/article/details/47395165`](http://blog.csdn.net/sunlovefly2012/article/details/47395165)
*   [`blog.csdn.net/lifetragedy/article/details/7708724`](http://blog.csdn.net/lifetragedy/article/details/7708724)
*   [`ihuangweiwei.iteye.com/blog/1233941`](http://ihuangweiwei.iteye.com/blog/1233941)
*   [`www.cnblogs.com/ggjucheng/archive/2013/04/16/3024731.html`](http://www.cnblogs.com/ggjucheng/archive/2013/04/16/3024731.html)
*   [`tomcat.apache.org/tomcat-8.0-doc/config/http.html#Connector_Comparison`](https://tomcat.apache.org/tomcat-8.0-doc/config/http.html#Connector_Comparison)

# Jenkins 安装和配置

* * *

*   Jenkins 安装和配置
    *   Jenkins 安装
    *   Jenkins 配置
    *   资料

## Jenkins 安装

*   Jenkins 安装

    *   官网：[`jenkins-ci.org/`](http://jenkins-ci.org/)
    *   官网帮助中心：[`wiki.jenkins-ci.org/display/JENKINS/Use+Jenkins`](https://wiki.jenkins-ci.org/display/JENKINS/Use+Jenkins)
    *   官网使用 Tomcat 部署方式指导：[`wiki.jenkins-ci.org/display/JENKINS/Tomcat`](https://wiki.jenkins-ci.org/display/JENKINS/Tomcat)
    *   此时（20160207） Jenkins 最新版本为：**1.647**
    *   JDK 最低要求是 JDK 7，官网推荐是 JDK 8
    *   我个人习惯 `/opt` 目录下创建一个目录 `setups` 用来存放各种软件安装包；在 `/usr` 目录下创建一个 `program` 用来存放各种解压后的软件包，下面的讲解也都是基于此习惯
    *   我个人已经使用了第三方源：`EPEL、RepoForge`，如果你出现 `yum install XXXXX` 安装不成功的话，很有可能就是你没有相关源，请查看我对源设置的文章
    *   Jenkins 下载：`wget http://mirrors.jenkins-ci.org/war/latest/jenkins.war` （大小：61 M）
        *   我们假设这个 Tomcat 就是为了 Jenkins 专用的
        *   把下载下来的 jenkins.war 移到 Tomcat 的 webapps 目录下，比如我的是：`/usr/program/tomcat8/webapps`
        *   把 Jenkins.war 改名为 ROOT.war：`mv jenkins.war ROOT.war`
        *   删除 Tomcat 下 webapps 目录下多余的一些目录
        *   首次启动 Tomcat，让 Tomcat 解压 war
        *   设置 JENKINS_HOME：
            *   寻找 jenkins home 目录地址：`find / -name .jenkins`，我这边得到的结果是：`/root/.jenkins`
            *   对在 Tomcat 文章中讲解的系统变量 `CATALINA_OPTS` 进行设置：
                *   旧值： `CATALINA_OPTS="-server -Xms528m -Xmx528m -XX:PermSize=256m -XX:MaxPermSize=358m" export CATALINA_OPTS`
                *   改为： `CATALINA_OPTS="-server -DJENKINS_HOME=/root/.jenkins -Xms528m -Xmx528m -XX:PermSize=256m -XX:MaxPermSize=358m" export CATALINA_OPTS`
*   Jenkins 各个组件配置：

    *   访问：[`192.168.0.110:8080/configure`](http://192.168.0.110:8080/configure)
*   其他问题

    *   如果访问的时候报这个异常：`java.net.UnknownHostException`，可以查看这篇文章：[`stackoverflow.com/questions/4969156/java-net-unknownhostexception`](http://stackoverflow.com/questions/4969156/java-net-unknownhostexception)

## 资料

*   [`stackoverflow.com/questions/4969156/java-net-unknownhostexception`](http://stackoverflow.com/questions/4969156/java-net-unknownhostexception)