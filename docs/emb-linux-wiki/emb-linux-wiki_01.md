# 翻译须知

# 嵌入式 Linux 知识库翻译小组用户手册

*   翻译计划
*   翻译过程

## 翻译计划

该翻译计划由 [@泰晓科技](http://tinylab.org) 于 2015 年 7 月 3 日 发起：

> #泰晓原译# [#eLinux 翻译计划#](http://t.cn/RLqNvGt) 现筹备 10 人小组，进行 eLinux.org 的翻译工作，持续发布为 gitbook 电子书，并在获得 Linux 基金会许可后出版为纸质书。参与翻译，您将：1，系统学习嵌入式 Linux，2，结识业界一线工程师，3，获得更多发展机会。关注@泰晓科技 微博私信报名，择优录取。

随后做了大量准备工作：

*   注册 github.com 帐号并建立 [项目 github 仓库](https://github.com/tinyclub/elinux)
*   注册 gitbook.com 帐号并建立[项目 gitbook 首页](http://tinylab.gitbooks.io/elinux)
*   建立 [eLinux.org 中文版项目](http://www.tinylab.org/elinux/)
*   召集了 26 个同学，全体注册 github 帐号并邀请加入协作团队 elinux
*   建立微信协作群，及时沟通与交流翻译过程中遇到的各类问题
*   建立 elinux.org 的 gitbook 目录架构，并让各位同学认领某个模块
*   编写自动转换脚本 `tools/mediaiwiki-2-markdown.sh` 自动转 elinux.org wiki page 为 markdown
*   自动构建辅助导入一级页面
*   编写自动构建二级页面脚本 `tools/m2m-subpage.sh` 并导入 Develop Portal 的二级页面
*   与此同时，部分同学已经翻译了一些内容并发送了 Pull Request，部分同学开始准备 Review

接下来就是这里的用户手册编写，到这里要做的工作是：

*   更详细地描述翻译和提交过程，简化大家的参与过程

*   制定文档格式规范

*   理清一些基本的翻译约定

## 翻译过程

*   注册并登录 github fork [代码仓库](https://github.com/tinyclub/elinux)

*   注册 gitbook.com，在 gitbook.com 建立书籍并绑定到 github.com 刚 fork 的仓库。

*   clone 代码仓库并切到 develop 分支

    ```
     git clone https://github.com/tinyclub/elinux.git
      cd elinux && git checkout develop 
    ```

*   找到自己认领的模块，先翻译一级链接，再翻译二级链接

*   指定自己 fork 的远程代码仓库（用于后面发 Pull Request），以 Github 帐号：`lzufalcon` 为例（请替换为自己的帐号，下同；之后 `myelinux` 就指向自己的 elinux 仓库）：

    ```
     git remote add myelinux git@github.com:lzufalcon/elinux.git 
    ```

*   基于自己计划翻译的内容创建分支， 以 `zh/dev_portals/Boot_Time/Boot_Time.md` 为例，可以基于远程 `develop` 分支创建 `boot_time` 分支：

    ```
     git fetch --all
      git checkout -b boot_time tinyclub/develop 
    ```

*   先检测英文原文是否和“From”（即 eLinux.org）原稿一致，不一致就修复并提交。

    *   因为是脚本自动导入，所以难免会存在一定的出错几率。
    *   部分 html 文本太复杂，`pandoc` 未能准确转换，可根据人工分析转为更精简的 `markdown` 语法
        *   请检查部分表格是否未能转换过来，内容错乱严重的一般都是这类问题。直接打开原文，查看源码，复制对应的表格部分即可。
        *   请检查缩进是否正确，部分原稿本身的缩进就有问题，建议修复。
        *   部分代码显示混乱，请缩进或者用两个 ``` 括起来。
        *   部分内部目录链接无法跳转到文章中或者外链失效，请检查修复。
*   把 `en/` 下的英文原稿同步到 `zh/` 下，原文提交为一笔 Git 修改记录。

    *   *注*：请不要直接翻译 `en/` 下的内容，只翻译 `zh/` 下的即可。

*   翻译前请参照如下安装 `gitbook` 环境，以 Ubuntu 为例

    ```
     $ sudo aptitude install -y retext git nodejs npm
      $ sudo ln -fs /usr/bin/nodejs /usr/bin/node
      $ sudo aptitude install -y calibre fonts-arphic-gbsn00lp
      $ npm config set registry https://registry.npm.taobao.org
      $ sudo npm install gitbook-cli -g 
    ```

    *注*：`calibre` 提供 `ebook-converter`，用于生成 pdf 等格式。

*   开始翻译

    *   翻译时可以用 gitbook.com 的在线编辑工具
    *   也可以用本地的工具，如 vim 编辑
    *   也可下载 GitBook Editor，界面类似在线编辑工具
*   翻译时

    *   请尽量遵守英文原稿的格式
    *   不要翻译目录里头的：`(#xxxx)`，`tools/build-toc.sh` 用它自动重构中文链接
    *   请规范使用 [Markdown 语法](http://help.gitbook.com/format/markdown.html)
    *   中英文混排时，英文和数字短语前后须加空格，以便获得更好的视觉感受
    *   代码片段上下须加空行，代码片段可以用 ``` 前后括起来，请参考 [Markdown](http://help.gitbook.com/format/markdown.html) `Code and Syntax Highlighting` 一节。
    *   --target 和 --host 之类的命令或者参数最好用标示符 ` 括起来。效果如：`--target` 和 `--host`
    *   全篇要统一用中文标点符号，全部用全角。
    *   碰到专业名词，特定缩写，不需要翻译。
    *   考虑到 Wiki 已经转为 GitBook，`This page` 统一翻译为`本文`。
    *   所有目录的标题中的 `Contents` 翻译为 `目录`。
    *   翻译后，只保留中文译文，英文内容可移除，也就是说不要一行英文原文，一行中文译文，只保留译文即可。
    *   每翻译完一个段落请务必通读
        *   确保用词没有歧义，整段衔接流畅，如有必要请调整/添加必要的衔接词汇
        *   并对照英文原文确保没有漏掉原文任何需要表达的含义，不要刻意漏掉部分自己感觉模糊的词汇
    *   如果有部分段落或者词汇理解模糊，请优先在协作群讨论或者借助第三方翻译工具协助，推荐 bing.com, iciba.com
    *   部分文章链接失效，请设法修复，比如说有指向 IBM Developer Works 的链接，可以 Google 链接最后的文件名找到正确的链接
    *   请在文件头注明如下信息（校订请列出所有提供了反馈并被采纳的同学，翻译和校订以 `@lzufalcon` 为例），可以在 `zh/doc/PLAN.md` 的最后找到大家的 github 地址：

        > 原文：[eLinux.org](http://elinux.org/Boot_Time.md)
        > 翻译：[@lzufalcon](https://github.com/lzufalcon)
        > 校订：[@lzufalcon](https://github.com/lzufalcon)

    *   新同学参与 Review 而且其 Feedbacks 被采纳后请把其 Github ID 以及链接追加到校订者名单，多人请用逗号分开。

    *   重构文章内目录（**注**：对管理员有效，各位译者请忽略）

        ```
         export PATH=$PATH:/path/to/elinux/tools
              build-toc.sh xxx.md 
        ```

    *   提交到 Git 仓库

        *   请统一使用如下 Subject 和 Message，全部使用英文，其中 n = 1,2,3,4...，根据 Review 次数追加

            ```
             zh: Translate xxx.md (Vn)

                  V1: Fix up ...
                  V2: ...
                  V3: ... 
            ```

*   翻译后预览和编译

    *   在本地编写可用 `Retext` 工具 预览，也可用 `pandoc` 转为 html（**注**：`pandoc` 转换结果跟 `gitbook` 略有差异）

        ```
         pandoc -f markdown -t html xxx.markdown > xxx.html 
        ```

    *   编译可选方案

        *   本地编译

            ```
             make && make pdf 或者 gitbook build && gitbook pdf 
            ```

        *   直接提交到 github，会自动触发 gitbook.com 构建，以 `boot_time` 分支为例（`myelinux` 指向自己的 `elinux` 仓库）：

            ```
             git push myelinux boot_time 
            ```

        *   添加 Travis-CI 自动构建支持

            *   登录 [`travis-ci.org/`](https://travis-ci.org/)
            *   绑定 github 帐号并添加上述 fork 过来的 elinux 项目
            *   进入 Settings，打开所有选项（本书已添加 .travis.yml）
*   编译通过后可重新整理代码仓库

    *   针对某个文件，确保一个文件一条变更，可通过 `git rebase -i commit_id^` 来合并翻译过程中针对某个文件所有未提交变更。
    *   创建 upstream 分支，更新远程仓库，`rebase` 到最新仓库，并修复所有冲突

```
 git checkout -b boot_time_upstream boot_time
    git fetch --all
    git rebase --onto tinyclub/develop --root
    git push myelinux boot_time_upstream 
```

*   之后，通过 Github 发送 Pull Request：本地选 `boot_time_upstream`，远程选 `develop`，也即是说要把自己仓库中的 `boot_time_upstream` 合并到远程的 `develop`

*   评审人员收到后会分配人员评审

*   根据评审人员反馈重新修改，并创建新分支用于进一步的评审，例如（记得追加 `Vn` 后缀，例如 V1，V2，而不是直接覆盖原来的分支，方便备份）：

    ```
     git checkout -b boot_time_upstream_v1 boot_time_upstream

      // 处理来自校订人员的各种反馈

      git fetch --all
      git rebase --onto tinyclub/develop --root
      git push myelinux boot_time_upstream_v1 
    ```

*   重复上述 Pull Request 步骤，直到被 Merge 到主线

# 如何编译

# 如何构建 GitBook

## 安装

```
$ sudo aptitude install -y retext git nodejs npm
$ sudo ln -fs /usr/bin/nodejs /usr/bin/node
$ sudo aptitude install -y calibre fonts-arphic-gbsn00lp
$ npm config set registry https://registry.npm.taobao.org
$ npm config set ca ""
$ sudo npm install gitbook-cli -g

$ sudo npm install gitbook-plugin-disqus \
                   gitbook-plugin-google_code_prettify \
                   gitbook-plugin-collapsible-menu \
                   gitbook-plugin-maxiang \
                   gitbook-plugin-livereload -g 
```

## 下载

```
$ git clone https://github.com/tinyclub/elinux.git && cd elinux/ 
```

## 构建 GitBook

```
$ gitbook build // make
$ gitbook pdf   // make pdf 
```

## 小贴士

### 错误: getaddrinfo ENOTFOUND elinux.org

```
$ gitbook pdf
info: loading book configuration....
warn: you should specify a gitbook version to use in your book.json, for example: 2.x.x
info: OK
info: >0 plugins loaded
info: Parsing multilingual book, with 1 lanuages
info: Preparing language book en
info: loading book configuration....OK
info: >0 plugins loaded
info: start generation with pdf generator
info: clean pdf generatorOK
info: start generation with pdf generator
info: clean pdf generatorOK
stream.js:94
      throw er; // Unhandled stream error in pipe.
            ^
Error: getaddrinfo ENOTFOUND elinux.org
    at errnoException (dns.js:44:10)
    at GetAddrInfoReqWrap.onlookup [as oncomplete] (dns.js:94:26) 
```

*   解决办法

    ```
     $ ping elinux.org
      PING elinux.org (140.211.15.183) 56(84) bytes of data.

      $ sudo -s
      $ echo "140.211.15.183 elinux.org" >> /etc/hosts 
    ```

# 任务分工

## 认领任务

请在下面的任务列表中认领一个任务，并在条目后面追加上自己的 Github ID 和链接，避免其他同学同时翻译，例如：

*   [Firmware](http://eLinux.org/Firmware) (@[lzufalcon](https://github.com/lzufalcon))

## 任务列表

*   Development Portals

    *   [Firmware](http://eLinux.org/Firmware) (@[lzufalcon](https://github.com/lzufalcon))
    *   [Security](http://elinux.org/Security) (@[lzz5235](https://github.com/lzz5235))
    *   [Boot Time](http://elinux.org/Boot_Time) (@[lzufalcon](https://github.com/lzufalcon))
    *   [System Size](http://elinux.org/System_Size) (@[wengpingbo](https://github.com/wengpingbo))
    *   [Networking](http://elinux.org/Networking) (@[lzufalcon](https://github.com/lzufalcon))
    *   [Resource Management](http://elinux.org/Resource_Management) (@[lzufalcon](https://github.com/lzufalcon))
    *   [Multimedia](http://www.elinux.org/Multimedia) (@[lzufalcon](https://github.com/lzufalcon))
    *   [Memory Management](http://elinux.org/Memory_Management) (@[zipper1956](https://github.com/zipper1956))
    *   [Power Management](http://www.elinux.org/Power_Management) (@[qkhhyga](https://github.com/qkhhyga))
    *   [File Systems](http://www.elinux.org/File_Systems) (@[lzufalcon](https://github.com/lzufalcon))
    *   [Real Time](http://www.elinux.org/Real_Time) (@[hduffddybz](https://github.com/hduffddybz))
    *   [Device drivers](http://www.elinux.org/Device_drivers) (@[lzufalcon](https://github.com/lzufalcon))
    *   [Device Tree](http://www.elinux.org/Device_Tree) (@[sdfd](https://github.com/sdfd))
    *   [Hardware Hacking](http://elinux.org/Hardware_Hacking) (@[lzufalcon](https://github.com/lzufalcon))
    *   [Development Platforms](http://elinux.org/Development_Platforms) (@[lzufalcon](https://github.com/lzufalcon))
    *   [Kernel Mainlining](http://elinux.org/Kernel_Mainlining) (@[DecJude](https://github.com/DecJude))
    *   [Legal Issues](http://www.elinux.org/Legal_Issues) (@[zxqhbd](https://github.com/zxqhbd))
    *   [Events](http://elinux.org/Events) (@[lzufalcon](https://github.com/lzufalcon))
    *   [Glossary](http://elinux.org/Glossary) (@[mintisan](https://github.com/mintisan))
*   [Toolbox](http://elinux.org/Toolbox) (@[bigzz](https://github.com/bigzz))

    *   Development Tools

        *   Logic_Analyzers
        *   Toolchains
        *   Build Systems
        *   Embedded Linux Distributions
        *   Debuggers
        *   Debug Assist Boards
        *   Memory Debuggers
        *   Tools
        *   Integrated Development Environments
        *   Emulators
        *   Tracers and Profilers
        *   Benchmarks
        *   Source Management Tools
        *   Test Systems
        *   Test Tools
        *   Scripting
    *   Developer Resources

        *   Linux Kernel Resources
        *   Kernel Subsystems
        *   The TTY Demystified
        *   Device Tree
        *   Online Documentation
        *   Books
        *   Reference Material
        *   Podcasts
        *   Device drivers
        *   Beginning Programming
    *   Tips and Tricks

        *   How to Identify IC Markings
        *   Code Styling Tips
        *   Debugging Tips
        *   GDB Tips
        *   GCC Tips
    *   Misc & Wishlist

        *   Setting up a Bluetooth Network
        *   Continuous Logging for Watchdog Timer Expiration
        *   Crash Diagnostics
*   [Debugging Portal](http://www.elinux.org/Debugging_Portal)

    *   Kernel Debugging (@[codingbelief](https://github.com/codingbelief))
        *   Debugging by printing / Printk
        *   Kernel Debugging Tips
        *   Kgdb
        *   KDB
        *   Kdmx
        *   Debugging The Linux Kernel Using Gdb
        *   MagicSysRq
        *   External Links
    *   Kernel Tracing and Profiling (@[Lotte-Bai](https://github.com/Lotte-Bai))
        *   System Tap
        *   Kernel Trace Systems
        *   Linux Trace Toolkit
        *   LTTng
        *   Ftrace
        *   Using Kernel Function Trace
        *   Linux Kernel State Tracer
*   [Android Portal](http://elinux.org/Android_Portal)

    *   Getting Started (@[lovelers](https://github.com/lovelers))
        *   Introduction to Android
        *   Design and Architecture
        *   Necessary tools
        *   Glossary
        *   Tutorials and Courseware
        *   Android History
        *   Versions
    *   Android Linux Kernel (@[llz383455526](https://github.com/llz383455526))
        *   Where to obtain
        *   How to build
        *   How to install (on phone, on emulator, etc.)
        *   What version to use
        *   Kernel features
        *   Board Support highlights
    *   Android System Information (@[HuangTao123](https://github.com/HuangTao123))
        *   Booting
        *   Power Management
        *   Security
        *   Memory Usage
        *   Dalvik Virtual Machine
        *   Packages, Assets and Resources
        *   Networking
        *   File Systems
        *   Android Logging System
        *   Android Source Code Description
    *   Software development (@[liuzhy520](https://github.com/liuzhy520))
        *   Software Development Kit
        *   Source Build System
        *   Development Tools
        *   Application Development Resources
        *   Scripting
        *   Debugging
        *   Testing
    *   Android-based Systems (@[willneo](https://github.com/willneo))
        *   Products (announced & shipped)
        *   Porting efforts and issues
        *   Getting Root (Jailbreaking)
        *   Miscellaneous Hardware Fixes
        *   Android x86
        *   Applications and Services
        *   Android Derivatives
        *   Linux emulators for Android
    *   Android Community (@[lovelers](https://github.com/lovelers))
        *   News
        *   Events
        *   Web/Mailing List Directory
        *   People
        *   Organizations
*   Hardware Pages

    *   BeagleBoard
    *   BeagleBone
    *   BeagleBoneBlack
    *   BeagleBone Capes
    *   MinnowBoard
    *   Raspberry Pi
    *   UDOO
    *   Improv
    *   OpenPhoenux
    *   Jetson TK1
    *   Mainline Linux on Tegra
    *   Parallella
    *   MIPS Creator CI20
    *   Banana Pi
    *   Renesas R-Car Boards
    *   DragonBoard
*   Embedded Linux Information

    *   Products
    *   Companies
    *   Vendors
    *   Processors
    *   Community
    *   Experts
    *   Jobs
    *   Board and Chip Vendors
*   eLinux.org Information and Usage tips

    *   About
    *   Help
    *   Editing Help
    *   Mailing Lists
    *   IRC