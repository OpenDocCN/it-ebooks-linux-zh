# 第三章 - SSH 技巧

整本书进展到 28% 了, 还不算慢, 照这样下去, 再有一周就可以搞定了 :)

~~顺便提一下我毕不了业的问题, 以后看到这里也算有个念想...... 2016-1-3~~

### 这一章介绍了一些关于 SSH 命令的小技巧和冷门选项.

虽然不是很有用, 好多我在本地也没成功, 但是知道的多了也不是一件坏事.(就像我挂过的科目...

如果看不下去了就跳过吧, 实际意义不是很大. (除了第一个.)

# Hack-28 调试 ssh 客户端

## 调试 ssh 客户端

这里就讲了查看 ssh 连接的过程, 打开`-v`的开关就好了.

```
➤ ssh root@localhost -v 
OpenSSH_6.9p1 Ubuntu-2, OpenSSL 1.0.2d 9 Jul 2015
debug1: Reading configuration data /home/mr/.ssh/config
debug1: Reading configuration data /etc/ssh/ssh_config
debug1: /etc/ssh/ssh_config line 19: Applying options for *
debug1: auto-mux: Trying existing master
debug1: Control socket "/tmp/root@localhost:22" does not exist
debug1: Connecting to localhost [127.0.0.1] port 22.
debug1: connect to address 127.0.0.1 port 22: Connection refused
ssh: connect to host localhost port 22: Connection refused 
```

过程很详细.

# Hack-29 SSH 逃逸字符

## SSH 逃逸字符

说真的, 我没有操作成功, 报错信息为:

`escape not available to multiplexed sessions`

作者的意图是, 将 ssh 作为一个任务, 运行在后台, 随时切换, 这当然是好的, 可是在我们知道了`screen`以及`tmux`这些软件之后, 就不需要在本地和远程切换了.不过为了尊重原著, 还是在此翻译一下:

1.  登陆远程系统: `localhost$ ssh -l jsmith remotehost`
2.  现在我们在远程的系统上了: `remotehost$`
3.  挂起当前任务, 按下`~`之后再按`Ctrl+Z`.

```
remotehost$ ~^Z
[1]+ Stopped ssh -l jsmith remotehost
localhost$ 
```

PS: 一开始的时候要按两个`~~`, 问我原因? 看这里: [`lonesysadmin.net/2011/11/08/ssh-escape-sequences-aka-kill-dead-ssh-sessions/`](https://lonesysadmin.net/2011/11/08/ssh-escape-sequences-aka-kill-dead-ssh-sessions/)

4.现在就会到本地目录了:

```
localhost$ jobs
[1]+ Stopped ssh -l jsmith remotehost 
```

5.当你需要返回的时候,就可以这样:

```
localhost$ fg %1
ssh -l jsmith remotehost
remotehost$ 
```

# Hack-30 显示 SSH 会话状态

## 显示 SSH 会话状态

跟上一个一样, 仍旧没成功, 估计你也一样... 可能是因为版本不同吧.

`~s` 显示会话状态.

跳过.

# Hack-31 OpenSSH 安全配置

## OpenSSH 安全配置

OpenSSH 的配置文件为`/etc/ssh/sshd_config`

下面介绍了 7 个需要更改的地方, 也不能说是需要, 只能说, 这样做会让你的主机在互联网中更安全.

在上面那个配置文件中, 有好多行的开始是一个`#`字符, 这表示这个行是一个注释, 而里面的配置则是默认的, 也让就是说, 注释掉的行都是默认配置, 你可以把注释取消, 然后更改选项.

#### 禁止 root 账户登录

服务器默认是允许 root 登陆的, 但是最好不要允许 root 直接登陆, 而是用你的账户登陆, 然后`su -`来进入 root 账户.

(其实这一点我本人不敢苟同, 也有可能是因为手里的服务器都是自己掌控, 所以不分你我, 但我想大多数人也就一台自用的服务器, 这个时候就无所谓那个账户了, 而且在后面开启证书验证, 关闭密码登陆的时候, 服务器是相当安全的(就非法登陆而言,漏洞除外), 所以, 如果你只有一台机子而这台机子就你自己用的话, 还是忽略这点吧.)

之所以这么做, 是因为, 如果允许 root 登陆, 那么每一个以 root 登录的用户在进行一系列操作之后, 无法查出具体是谁做的, 也就是, 翻了错误可以丢锅... 但是当禁止 root 登陆之后, 每个用户要想行使 root 权限, 就必须使用`su -`, 这一切都是记录下来的 :) 所以犯了错就有据可查咯~

具体操作:

```
$ vi /etc/ssh/sshd_config
PermitRootLogin no #修改这个地方 
```

#### 仅**允许**特定用户/组登陆

具体操作:

```
$ vi /etc/ssh/sshd_config
AllowUsers ramesh john jason #允许登陆的用户
AllowGroups sysadmin dba #允许登陆的用户组 
```

#### 仅**禁止**特定用户/组登陆

具体操作:

```
$ vi /etc/ssh/sshd_config
DenyUsers ramesh john jason #禁止登陆的用户
DenyGroups sysadmin dba #禁止登陆的用户组 
```

#### 更改 sshd 的默认端口

SSHD 默认登陆端口为`22`, 安全起见(防止被爆破), 最好改成一个乱七八糟的端口

```
$ vi /etc/ssh/sshd_config
Port 23333 
```

#### 更改登陆时限

默认的时间限制是 2 分钟, 如果 2 分钟内没有成功登陆, 服务器就会断开连接. 2 分钟貌似有点长, 所以我们最好把他改小点:

```
$ vi /etc/ssh/sshd_config
LoginGraceTime 1m 
```

#### 更改监听的网卡

假设服务器有四个网卡, 每个网卡对应的 IP 地址分别是:

*   eth0 – 192.168.10.200
*   eth1 – 192.168.10.201
*   eth2 – 192.168.10.202
*   eth3 – 192.168.10.203

但是你只想在特定的网卡上监听服务, 那么你就可以在配置文件中写道:

```
$ vi /etc/ssh/sshd_config
ListenAddress 192.168.10.200 # 这是网卡 0
ListenAddress 192.168.10.202 # 这是网卡 2 
```

#### 不活动时断开连接

这个不活动, 指的是没有命令执行,无论命令成功获失败, 也就是说, 只要你在某一时间段内没有按下回车,且当前没有任务在运行, 那么服务器就会主动断开连接(把你踢出去).

如果是在 bash 里面,可以利用 `TMOUT`这个变量.

在 OpenSSH 中, 可以这样修改:

```
$ vi /etc/ssh/sshd_config
ClientAliveInterval 600
ClientAliveCountMax 0 #从不检查 
```

设置 600 内,如果无活动就断开连接.

# Hack-32 PuTTY

这里是介绍 windows 上的 ssh 连接软件 PuTTY, 而且还是一些关于注册表乱七八糟的, 我就不翻译了, 因为没用.

(这个作者越来越不像话了嘿!)

我说点别的吧, ssh 密钥登陆:

在这个 ssh 配置文件(`/etc/ssh/sshd_config`)中,更改如下地方:

```
PubkeyAuthentication yes
#AuthorizedKeysFile    %h/.ssh/authorized_keys # 这个是默认的,不用改
...
# Change to no to disable tunnelled clear text passwords
PasswordAuthentication no # 关闭密码登录, 只允许通过密钥登陆 
```

然后在你本机生成一个 rsa 的密钥:

用它 -> `ssh-keygen`

一步一步生成, 有提示的. # 也可以一路回车 :)

然后将生成的公钥, 以`.pub`结尾的那个, 拷贝到服务器的`~/.ssh`目录下, 然后就可以直接登陆啦~

如果不拷贝的话,可以用命令`ssh-copy-id user@host`这个时候就需要输入一次密码.

同样很方便.

第三章到此结束咯, 是不是感觉没学到什么东西...(除了`TMOUT`这个变量)