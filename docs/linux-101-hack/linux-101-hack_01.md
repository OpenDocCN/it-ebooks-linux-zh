# 第一章 - 关于 CD

# 第一章 关于 cd

```
➤ man cd
No manual entry for cd 
```

这么常用的 cd 竟然没有 man！是因为太简单了么？

如果你认为它太简单而轻视它，那就是你的不对了，不信？下面的六种技巧就会让你大吃一惊！

# Hack-1 用 CDPATH 来重新定义目录

## 用 CDPATH 来重新定义目录

如果你经常`/etc`下的目录，每次都要输入`/etc/xxx`真的有点烦，但是当你设定`CDPATH`后，你就可以直接在`CDPATH`目录和当前目录下游走了～

比如：

```
➤ pwd
/home/mr
➤ cd nginx
bash: cd: nginx: No such file or directory #当然进不去这个目录
➤ echo $CDPATH #默认的 CDPATH 是空的

➤ CDPATH=/etc #把 CDPATH 设为/etc，就可以进入/etc 下的目录了
➤ cd nginx
/etc/nginx
➤ pwd
/etc/nginx #进去了～
➤ 
```

如果你想长期使用这个方法，比如，经常游走于`/etc`,`/var`,`/etc/nginx`,那么就可以把`CDPATH`export 一下，像这样：

`export CDPATH=.:~:/etc:/var:/etc/nginx`

把这句话写入`.bashrc`就可以永久生效了。

# Hack-2 CD 的别名

## CD 的别名

这也是一个小技巧，用途很广泛，用`alias`设置别名：

比如，如果要想返回上一层目录，你可能会这样做：`cd ..`，是不是很烦？

那么这种方法可能会让你有点吃惊：

```
➤ pwd
/home/mr
➤ cd .. #返回上一层目录
➤ pwd
/home
➤ cd /home/mr
➤ alias ..='cd ..' #利用 alias
➤ ..
➤ pwd #同样返回了上一层目录
/home 
```

然后你可以把好多有趣的命令都用`alias`缩短，我在 githu 上整理了一下我常用的`alias`命令，感兴趣的话可以去看看 :P

Here: [`github.com/wrfly/bash_aliases`](https://github.com/wrfly/bash_aliases)

# Hack-3 Functions

## 与 CD 有关的 Functions()

下面说一种与`alias`差不多的`function`：

```
➤ mkdir -p /tmp/subdir1/subdir2/subdir3
mkdir: created directory ‘/tmp/subdir1’
mkdir: created directory ‘/tmp/subdir1/subdir2’
mkdir: created directory ‘/tmp/subdir1/subdir2/subdir3’
➤ cd /tmp/subdir1/subdir2/subdir3
➤ pwd
/tmp/subdir1/subdir2/subdir3
➤ 
```

是不是又有点烦？

下面就让`function`来解救你吧～

```
function mcd () {
    mkdir -p "$@" && eval cd "\"\$$#\"";
} 
```

然后，瞧好了：

```
➤ mcd /tmp/1/2/3/4/5/6/7
mkdir: created directory ‘/tmp/1’
mkdir: created directory ‘/tmp/1/2’
mkdir: created directory ‘/tmp/1/2/3’
mkdir: created directory ‘/tmp/1/2/3/4’
mkdir: created directory ‘/tmp/1/2/3/4/5’
mkdir: created directory ‘/tmp/1/2/3/4/5/6’
mkdir: created directory ‘/tmp/1/2/3/4/5/6/7’
➤ pwd
/tmp/1/2/3/4/5/6/7
➤ 
```

为什么会这样呢？这是因为我们在当前环境下新建了一个`function`，这个`function`的功能就是新建目录，然后进入我们新建的目录。

当然，这应该是最简单的`function`了吧，把一堆常用的`function`写入你的`.bashrc`里面，会让你很舒服的。

同样，我的一些常用`function`也在`github`上面，跟`alias`是放一块儿的哦。

# Hack-4 后退后退！

## 后退后退！

接着上面的目录，我们目前在这里：

```
➤ pwd
/tmp/1/2/3/4/5/6/7
➤ 
```

然后我去`/tmp`下写了`wrfly 到此一游`之后，又想返回刚才的目录了，该怎么办？

Terminal 里可没有后退键给我按！

```
➤ pwd
/tmp/1/2/3/4/5/6/7
➤ cd /tmp
➤ echo ""wrfly 到此一游""
wrfly 到此一游
➤ echo "wrfly 到此一游" > ttttest
➤ pwd
/tmp
➤ cd - ##看清了吗？我可用了两个井号键呢！
/tmp/1/2/3/4/5/6/7
➤ pwd
/tmp/1/2/3/4/5/6/7
➤ 
```

的确，Terminal 不给我们后退键，因为里面就没有键可以按啊，哈哈哈哈，不过嘛，这么多命令总有一个可以达到我们的目的，就比如刚才的 **`cd -`**，通过这个命令我们就后退到了之前的目录了。

其实这里面还有一些道道，比如：

```
➤ pwd
/tmp/1/2/3/4/5/6/7
➤ echo $OLDPWD
/tmp
➤ cd -
/tmp
➤ echo $OLDPWD
/tmp/1/2/3/4/5/6/7
➤ 
```

这个`$OLDPWD`就是上一层目录的意思，当然还有`$PWD`这个变量，表面上看来跟`pwd`是一样的（因为`pwd`还有一个`-P`的参数可以用，可以显示`soft link`的真实路径，所以他们并不是完全相同）

再插句题外话，说一下`pwd`：

```
➤ ln -s /tmp/1/2/3/4/5/6/7 7
➤ ll 7
lrwxrwxrwx 1 mr mr 18 12 月 23 15:51 7 -> /tmp/1/2/3/4/5/6/7/ 
#为什么是 18 这么大呢？因为'/tmp/1/2/3/4/5/6/7'一共有 18 个字节啦
➤ cd 7
➤ pwd
/tmp/7
➤ pwd
/tmp/7
➤ pwd -P
/tmp/1/2/3/4/5/6/7
➤ echo $PWD
/tmp/7
➤ 
```

是不是很好玩呢？

# Hack-5 操纵目录栈

## 操纵目录栈

刚才我还特地请教了下我们宿舍打 ACM 的杰神，讲了一下堆、栈、队列之间的差别和联系，然后，这一小节的题目还算不错，栈，先进后出。

*   `dirs` 显示当前目录栈的内容
*   `pushd` 入目录栈
*   `popd` 出目录栈

那么这三个命令都有什么用处呢？

不罗嗦，看操作：

```
➤ for i in {1..4};do mkdir dir_$i;done
mkdir: created directory ‘dir_1’
mkdir: created directory ‘dir_2’
mkdir: created directory ‘dir_3’
mkdir: created directory ‘dir_4’
➤ cd dir_1
➤ pushd . #pushd 完成之后总会进入这一个目录
/tmp/dir_1 /tmp/dir_1
➤ cd /tmp/dir_2
➤ pushd . #而且还会显示当前的目录栈内容
/tmp/dir_2 /tmp/dir_2 /tmp/dir_1
➤ cd /tmp/dir_3
➤ pushd .
/tmp/dir_3 /tmp/dir_3 /tmp/dir_2 /tmp/dir_1
➤ cd /tmp/dir_4
➤ pushd .
/tmp/dir_4 /tmp/dir_4 /tmp/dir_3 /tmp/dir_2 /tmp/dir_1
➤ dirs #第一个是 dir_4，因为栈的顶总是当前目录
/tmp/dir_4 /tmp/dir_4 /tmp/dir_3 /tmp/dir_2 /tmp/dir_1 
```

那么到现在为止我们的目录栈看起来是这样的：

```
1 - /tmp/dir_1
2 - /tmp/dir_2
3 - /tmp/dir_3
4 - /tmp/dir_4 
```

接下来就轮到`popd`啦：

```
➤ pwd
/tmp/dir_4
➤ dirs #dirs 显示目录栈内容
/tmp/dir_4 /tmp/dir_4 /tmp/dir_3 /tmp/dir_2 /tmp/dir_1
➤ popd #每次目录出栈之后都会显示目录栈的内容
/tmp/dir_4 /tmp/dir_3 /tmp/dir_2 /tmp/dir_1
➤ pwd #并且进入那个目录
/tmp/dir_4
➤ popd
/tmp/dir_3 /tmp/dir_2 /tmp/dir_1
➤ pwd
/tmp/dir_3
➤ popd
/tmp/dir_2 /tmp/dir_1
➤ pwd
/tmp/dir_2
➤ popd
/tmp/dir_1
➤ pwd
/tmp/dir_1
➤ popd #栈空了，没有了
bash: popd: directory stack empty
➤ 
```

看了这么多，这些组合到底有什么用？其实吧，如果你手速够快记忆力够好的话，这些对你没啥用，不过呢，像我这样比较懒的人，这东西就有用了，比如，我要在几个目录之间切换，有不想每次都输入那些命令，怎么办？就用这种方法，因为 pushd 不仅进入了这个目录，而且还给了我一个后退的途径，比`cd -`更高级一点。甚至我可以用`function cd() { pushd $@ &> /dev/null; }`来将`pushd`变成`cd`,反正我也不亏，是不是 :P

# Hack-6 更正目录名

## 自动更正目录名

其实感觉这个功能不是很有用，因为大多数情况下我们都是用`tab`直接补全目录名的，不过作者既然在这里说(chong)了(shu)，那本着尊重作者的原则我也应该啰嗦下：

`shopt -s cdspell`

就是这货，开启更正目录开关。

比如：

```
➤ cd /tmp/dir_1
➤ ls
hello
➤ cd hhllo
bash: cd: hhllo: No such file or directory
➤ 
```

没错，进不去。

但是当你开启那个神奇的开关之后：

`shopt -s cdspell`

```
➤ shopt -s cdspell
➤ cd hhllo
hello
➤ pwd
/tmp/dir_1/hello
➤ 
```

进去了～这就是自动更正目录名的效果。

其实不仅如此，`shopt`还有好多可以玩的地方，有兴趣的同学可以探索下哦～

`shopt -s xxxx`是开启 xxxx

`shopt -u xxxx`是关闭 xxxx

```
➤ shopt
autocd             off
cdable_vars        off
cdspell            on
checkhash          off
checkjobs          off
checkwinsize       on
cmdhist            on
compat31           off
compat32           off
compat40           off
compat41           off
compat42           off
complete_fullquote    on
direxpand          off
dirspell           off
dotglob            off
execfail           off
expand_aliases     on
extdebug           off
extglob            on
extquote           on
failglob           off
force_fignore      on
globstar           off
globasciiranges    off
gnu_errfmt         off
histappend         on
histreedit         off
histverify         off
hostcomplete       off
huponexit          off
interactive_comments    on
lastpipe           off
lithist            off
login_shell        off
mailwarn           off
no_empty_cmd_completion    off
nocaseglob         off
nocasematch        off
nullglob           off
progcomp           on
promptvars         on
restricted_shell    off
shift_verbose      off
sourcepath         on
xpg_echo           off 
```