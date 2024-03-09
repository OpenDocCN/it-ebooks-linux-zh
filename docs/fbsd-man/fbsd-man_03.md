# 部分 II. 常见的任务

前面已经介绍了必要的基础知识， 手册的这一部分将讨论 FreeBSD 的一些最常用的功能。 这些章节包括：

*   向您介绍流行和实用的桌面应用程序： 浏览器、产品工具、文档察看程序，等等。

*   向您介绍一系列可以在 FreeBSD 上使用的多媒体工具。

*   介绍联编定制的 FreeBSD 内核以启用附加功能的方法。

*   详细介绍包括桌面和网络打印机在内的打印系统设置。

*   向您展示如何在 FreeBSD 上运行 Linux 应用程序。

某些章节希望您首先阅读过其他部分， 在这些章的开头部分也会给出类似的提示。

## 第 7 章 桌面应用

Contributed by Christophe Juniet.

## 7.1. 概述

FreeBSD 可以运行种类繁多的桌面应用程序， 这包括像浏览器和字处理这样的软件。 绝大多数这样的程序都可以通过 package 来安装， 或者从 Ports Collection 自动地构建。 许多新用户希望能够在它们的系统中找到这样的应用程序。 这一章将向您展示如何轻松地使用 package 或者 Ports Collection 中安装这样的软件。

需要注意的是从 ports 安装意味着要编译源码。 根据编译的 ports 和电脑速度的不同， 这可能需要花费相当长的时间。 若是您觉得编译源码太过耗时的话， 绝大多数 ports 也有预编译的版本可供安装。

因为 FreeBSD 提供的二进制兼容 Linux 的特性， 许多原本为 Linux 开发的程序都可以直接用在您的桌面。 在安装任何的 Linux 应用程序之前， 强烈的推荐您阅读 第 11 章 *Linux® 二进制兼容模式*。 当您在寻找特定的 ports 时， 可以使用 [whereis(1)](http://www.FreeBSD.org/cgi/man.cgi?query=whereis&sektion=1&manpath=freebsd-release-ports)。 一般来说， 许多利用 Linux 二进制兼容特性的 ports 都以“linux-”开头。 在下面的介绍中，都假设安装 Linux 应用程序前已经开启了 Linux 二进制兼容功能。

本章涵盖以下种类应用程序：

*   浏览器 (例如 Firefox、 Opera、 Konqueror)

*   办公、图象处理 (例如 KOffice、 AbiWord、 GIMP、 OpenOffice.org、 LibreOffice)

*   文档查看 (例如 Acrobat Reader®、 gv、 Xpdf、 GQview)

*   财务 (例如 GnuCash、 Gnumeric、 Abacus)

阅读这章之前，您应该：

*   知道如何安装额外的第三方软件(第 5 章 *安装应用程序: Packages 和 Ports*)。

*   知道如何安装 Linux 软件(第 11 章 *Linux® 二进制兼容模式*)。

想要获得更多的有关多媒体环境的信息，请阅读 第 8 章 *多媒体*。如果您想要建立和使用电子邮件， 请参考第 29 章 *电子邮件*。

## 7.2. 浏览器

FreeBSD 并没有预先安装特定的浏览器。然而，在 ports 的目录 [www](http://www.FreeBSD.org/ports/www.html) 有许多浏览器可以安装。如果您没有时间一一编译它们 (有些时候这可能需要花费相当长的时间) 大部分都有 package 可用。

KDE 和 GNOME 已经提供 HTML 浏览器。 请参考第 6.7 节 “桌面环境”得到更多完整的有关设定这些桌面环境的信息。

如果您要找小型的浏览器， 可以试试看 [www/dillo2](http://www.freebsd.org/cgi/url.cgi?ports/www/dillo2/pkg-descr)、 [www/links](http://www.freebsd.org/cgi/url.cgi?ports/www/links/pkg-descr) 或 [www/w3m](http://www.freebsd.org/cgi/url.cgi?ports/www/w3m/pkg-descr)。

这一节涉及如下程序：

| 程序名称 | 资源需求 | 安装时间 | 主要依赖 |
| --- | --- | --- | --- |
| Firefox | 中等 | 长 | Gtk+ |
| Opera | 少 | 轻松 | 同时有可用的 FreeBSD 和 Linux 版本。 Linux 版本需要使用 Linux 二进制兼容模块和 linux-openmotif。 |
| Firefox | 中等 | 长 | Gtk+ |
| Konqueror | 中等 | 长 | 需要 KDE 库 |

### 7.2.1. Firefox

Firefox 是一个现代， 自由， 开放源代码稳定的浏览器， 并完全移植到了 FreeBSD 上： 它的特性包括有一个非常标准的 HTML 显示引擎， 标签式浏览， 弹出窗口阻止， 扩展插件， 改进的安全性， 等等。 Firefox 是基于 Mozilla 的代码。

您可以通过输入下面的命令来安装预编译的包：

```
# `pkg_add -r firefox`
```

这将会安装 Firefox 7.0， 如果希望运行 Firefox 3.6， 则应使用下面的命令：

```
# `pkg_add -r firefox36`
```

如果你希望从源代码编译的话， 可以通过 Ports Collection 安装：

```
# `cd /usr/ports/www/firefox`
# `make install clean`
```

对于 Firefox 3.6， 对应的命令中的 `firefox` 应改为 `firefox36`。

### 7.2.2. Firefox 与 Java™ 插件

### 注意:

在这一节和接下来的两节中， 我们均假定您已经安装了 Firefox。

通过 Ports 套件来安装 OpenJDK 6， 输入下面的命令：

```
# `cd /usr/ports/java/openjdk6`
# `make install clean`
```

接下来安装 [java/icedtea-web](http://www.freebsd.org/cgi/url.cgi?ports/java/icedtea-web/pkg-descr) port：

```
# `cd /usr/ports/java/icedtea-web`
# `make install clean`
```

请确认在编译上述 port 时使用的是系统预设的配置。

启动浏览器并在地址栏中输入 `about:plugins` 然后按 **Enter**。 浏览器将会呈现一个列出所有已安装插件的页面； Java™ 插件应在其中出现。

如果浏览器找不到插件， 则用户可能必须运行下面的命令， 并重启浏览器：

```
% `ln -s /usr/local/lib/IcedTeaPlugin.so \
  $HOME/.mozilla/plugins/`
```

### 7.2.3. Firefox 与 Adobe® Flash® 插件

Adobe® Flash® 插件并没有直接提供其 FreeBSD 版本。 不过， 我们有一个软件层 (wrapper) 可以用来运行 Linux 版本的插件。 这个 wrapper 也支持 Adobe® Acrobat®、 RealPlayer 和很多其他插件。

根据你 FreeBSD 版本的不同选择相应的安装步骤：

1.  **FreeBSD 7.X**

    安装 [www/nspluginwrapper](http://www.freebsd.org/cgi/url.cgi?ports/www/nspluginwrapper/pkg-descr) port。 这个 port 需要安装一个较大的[emulators/linux_base-fc4](http://www.freebsd.org/cgi/url.cgi?ports/emulators/linux_base-fc4/pkg-descr) port。

    下一步是安装 [www/linux-flashplugin9](http://www.freebsd.org/cgi/url.cgi?ports/www/linux-flashplugin9/pkg-descr) port。 这将会安装 Flash® 9.X， 此版本目前能在 FreeBSD 7.X 上正常运行。

    ### 注意:

    在比 FreeBSD 7.1-RELEASE 更旧版本的系统上， 你必须安装 [www/linux-flashplugin7](http://www.freebsd.org/cgi/url.cgi?ports/www/linux-flashplugin7/pkg-descr) 并跳过以下 [linprocfs(5)](http://www.FreeBSD.org/cgi/man.cgi?query=linprocfs&sektion=5&manpath=freebsd-release-ports) 的部份。

2.  **FreeBSD 8.X**

    安装 [www/nspluginwrapper](http://www.freebsd.org/cgi/url.cgi?ports/www/nspluginwrapper/pkg-descr) port。 这个 port 需要安装一个较大的[emulators/linux_base-f10](http://www.freebsd.org/cgi/url.cgi?ports/emulators/linux_base-f10/pkg-descr) port。

    下一步是安装 [www/linux-f10-flashplugin10](http://www.freebsd.org/cgi/url.cgi?ports/www/linux-f10-flashplugin10/pkg-descr) port。 这将会安装 Flash® 10.X， 此版本目前能在 FreeBSD 8.X 上正常运行。

    这个版本需要创建一个符号链接：

    ```
    # `ln -s /usr/local/lib/npapi/linux-f10-flashplugin/libflashplayer.so \
      /usr/local/lib/browser_plugins/`
    ```

    如果系统中没有 `/usr/local/lib/browser_plugins` 目录， 则应手工创建它。

按照 FreeBSD 版本， 在安装了正确的 Flash® port 之后， 插件必须由每个用户运行 `nspluginwrapper` 安装：

```
% `nspluginwrapper -v -a -i`
```

如果希望播放 Flash® 动画的话，Linux® 的进程文件系统， [linprocfs(5)](http://www.FreeBSD.org/cgi/man.cgi?query=linprocfs&sektion=5&manpath=freebsd-release-ports) 必须挂载于 `/usr/compat/linux/proc`。 可以通过以下的命令实现：

```
# `mount -t linprocfs linproc /usr/compat/linux/proc`
```

这也可以在机器启动时自动挂载， 把以下这行加入 `/etc/fstab`：

```
linproc	/usr/compat/linux/proc	linprocfs	rw	0	0
```

然后就可以打开浏览器， 并在地址栏中输入 `about:plugins` 然后按下 **Enter**。 这将显示目前可用的插件列表。

### 7.2.4. Firefox and Swfdec Flash® Plugin

Swfdec 是一个用以解码和渲染 Flash® 动画的库。 Swfdec-Mozilla 是一个使用了 Swfdec 库让 Firefox 能播放 SWF 文件的插件。它目前仍处于开发状态。

如果你不能或者不想编译安装，可以通过网络安装二进制包：

```
# `pkg_add -r swfdec-plugin`
```

如果二进制包还不可用，你可以通过 Ports Collection 编译安装：

```
# `cd /usr/ports/www/swfdec-plugin`
# `make install clean`
```

然后重启你的浏览器使得这个插件生效。

### 7.2.5. Opera

Opera 是一个功能齐全， 并符合标准的浏览器。 它还提供了内建的邮件和新闻阅读器、 IRC 客户端， RSS/Atom feed 阅读器以及更多功能。 除此之外， Opera 是一个比较轻量的浏览器， 其速度很快。 它提供了两种不同的版本： “native” FreeBSD 版本， 以及通过 Linux 模拟运行的版本。

要使用 Opera 的 FreeBSD 版本来浏览网页，安装以下的 package：

```
# `pkg_add -r opera`
```

有些 FTP 站点没有所有版本的 package， 但仍然可以通过 Ports 套件来安装 Opera：

```
# `cd /usr/ports/www/opera`
# `make install clean`
```

要安装 Linux 版本的 Opera， 将上面例子中的 `opera` 改为 `linux-opera` 即可。

Adobe® Flash® 插件目前并没有提供 FreeBSD 专用的版本。 不过， 可以使用其 Linux® 版本的插件。 要安装这个版本， 需要安装 [www/linux-f10-flashplugin10](http://www.freebsd.org/cgi/url.cgi?ports/www/linux-f10-flashplugin10/pkg-descr) port， 以及 [www/opera-linuxplugins](http://www.freebsd.org/cgi/url.cgi?ports/www/opera-linuxplugins/pkg-descr)：

```
# `cd /usr/ports/www/linux-f10-flashplugin10`
# `make install clean`
# `cd /usr/ports/www/opera-linuxplugins`
# `make install clean`
```

然后可以检查插件是否可用了： 在地址栏中输入 `opera:plugins` 然后按 **Enter**。 浏览器将列出可用的插件列表。

添加 Java™ 插件的方法， 与 为 Firefox 添加插件 的方法相同。

### 7.2.6. Konqueror

Konqueror 是 KDE 的一部分，不过也可以通过安装 [x11/kdebase3](http://www.freebsd.org/cgi/url.cgi?ports/x11/kdebase3/pkg-descr) 在非 KDE 环境下使用。 Konqueror 不止是一个浏览器， 也是一个文件管理器和多媒体播放器。

也有种类丰富的插件能够配合 Konqueror 一起使用， 您可以通过 [misc/konq-plugins](http://www.freebsd.org/cgi/url.cgi?ports/misc/konq-plugins/pkg-descr) 来安装它们。

Konqueror 也支持 Flash®； 关于如何获得用于 Konqueror 的 Flash® 支持的 “How To” 文档 可以在 `[`freebsd.kde.org/howtos/konqueror-flash.php`](http://freebsd.kde.org/howtos/konqueror-flash.php)` 找到。

## 7.3. 办公、图象处理

当需要进行办公或者进行图象处理时， 新用户通常都会找一些好用的办公套件或者字处理软件。 尽管目前有一些 桌面环境， 如 KDE 已经提供了办公套件， 但目前这还没有一定之规。 无论您使用那种桌面环境， FreeBSD 都能提供您需要的软件。

这节涉及如下程序：

| 软件名称 | 资源需求 | 安装时间 | 主要依赖 |
| --- | --- | --- | --- |
| KOffice | 少 | 多 | KDE |
| AbiWord | 少 | 少 | Gtk+ 或 GNOME |
| The Gimp | 少 | 长 | Gtk+ |
| OpenOffice.org | 多 | 长 | JDK™、 Mozilla |
| LibreOffice | 较重 | 巨大 | Gtk+ 或 KDE/ GNOME 或 JDK™ |

### 7.3.1. KOffice

KDE 社区提供了一套办公套件， 它能用在桌面环境。它包含四个标准的组件，这些组件可以在其它办公套件中找到。 KWord 是字处理程序、 KSpread 是电子表格程序、 KPresenter 是演示文档制作管理程序、 Kontour 是矢量绘图软件。

安装最新的 KOffice 之前，先确定您是否安装了最新版的 KDE。

使用 package 来安装 KOffice，安装细节如下：

```
# `pkg_add -r koffice`
```

如果没有可用的 package，您可以使用 Ports Collection 安装。 安装 KDE3 的 KOffice 版本，如下：

```
# `cd /usr/ports/editors/koffice-kde3`
# `make install clean`
```

### 7.3.2. AbiWord

AbiWord 是一个免费的字处理程序，它看起来和 Microsoft® Word 的感觉很相似。 它适合用来打印文件、信函、报告、备忘录等等， 它非常快且包含许多特性，并且非常容易使用。

AbiWord 可以导入或输出很多文件格式， 包括一些象 Microsoft® `.doc` 这类专有格式的文件。

AbiWord 也有 package 的安装方式。您可以用以下方法安装：

```
# `pkg_add -r abiword`
```

如果没有可用的 package，它也可以从 Ports Collection 编译。ports collection 应该是最新的。它的安装方式如下：

```
# `cd /usr/ports/editors/abiword`
# `make install clean`
```

### 7.3.3. GIMP

对图象的编辑或者加工， GIMP 是一个非常精通图象处理的软件。 它可以被用来当作简单的绘图程序或者一个专业的照片处理套件。 它支持大量的插件和具有脚本界面的特性。 GIMP 可以读写众多的文件格式， 支持扫描仪和手写板。

您可以用下列命令安装：

```
# `pkg_add -r gimp`
```

如果您在 FTP 站点没有找到这个 package，您也可以使用 Ports Collection 的方法安装。ports 的 [graphics](http://www.FreeBSD.org/ports/graphics.html) 目录也包含有 Gimp 手册。 以下是安装它们的方法：

```
# `cd /usr/ports/graphics/gimp`
# `make install clean`
# `cd /usr/ports/graphics/gimp-manual-pdf`
# `make install clean`
```

### 注意:

Ports 中的 [graphics](http://www.FreeBSD.org/ports/graphics.html) 目录也有开发中的 GIMP 版本 [graphics/gimp-devel](http://www.freebsd.org/cgi/url.cgi?ports/graphics/gimp-devel/pkg-descr)。 HTML 版本的 Gimp 手册 可以在 [graphics/gimp-manual-html](http://www.freebsd.org/cgi/url.cgi?ports/graphics/gimp-manual-html/pkg-descr) 找到。

### 7.3.4. OpenOffice.org

OpenOffice.org 包括一套完整的办公套件： 字处理程序、 电子表格程序、 演示文档管理程序和绘图程序。 它和其它的办公套件的特征非常相似，它可以导入输出不同的流行的文件格式。 它支持许多种语言 ── 国际化已经渗透到了其界面、 拼写检查和字典等各个层面。

OpenOffice.org 的字处理程序使用 XML 文件格式使它增加了可移植性和灵活性。 电子表格程序支持宏语言和使用外来的数据库界面。 OpenOffice.org 已经可以平稳的运行在 Windows®、Solaris™、Linux、FreeBSD 和 Mac OS® X 等各种操作系统下。 更多的有关 OpenOffice.org 的信息可以在 [OpenOffice.org 网页](http://www.openoffice.org/) 找到。 对于特定的 FreeBSD 版本的信息，您可以在直接在 [FreeBSD OpenOffice 移植团队](http://porting.openoffice.org/freebsd/)的页面下载。

安装 OpenOffice.org 方法如下：

```
# `pkg_add -r openoffice.org`
```

### 注意:

如果您正在使用 FreeBSD 的 -RELEASE 版本， 一般来说这样做是没问题的。 如果不是这样， 您就可能需要看一看 FreeBSD OpenOffice.org 移植小组的网站， 并使用 [pkg_add(1)](http://www.FreeBSD.org/cgi/man.cgi?query=pkg_add&sektion=1&manpath=freebsd-release-ports) 从那里下载并安装合适的软件包。 最新的发布版本和开发版本都可以在那里找到。

装好 package 之后， 您只需输入下面的命令就能运行 OpenOffice.org 了：

```
% `openoffice.org`
```

### 注意:

在第一次运行时， 将询问您一些问题， 并在您的主目录中建立一个 `.openoffice.org` 目录。

如果没有可用的 OpenOffice.org package，您仍旧可以选择编译 port。然而， 您必须记住它的要求以及大量的磁盘空间和相当长的时间编译。

```
# `cd /usr/ports/editors/openoffice.org-3`
# `make install clean`
```

### 注意:

如果希望联编一套进行过本地化的版本， 将前述命令行改为：

```
# `make LOCALIZED_LANG=your_language install clean`
```

您需要将 *`your_language`* 改为正确的 ISO-代码。 所支持的语言代码可以在 `files/Makefile.localized` 文件中找到， 这个文件位于 port 的目录。

一旦完成上述操作， 就可以通过下面的命令来运行 OpenOffice.org 了：

```
% `openoffice.org`
```

### 7.3.5. LibreOffice

LibreOffice 是由 [The Document Foundation](http://www.documentfoundation.org/) 开发的自由软件办公套件， 它与其他平台上的主流办公系统兼容。 这是 OpenOffice.org 的一个贴牌的分支版本， 包含了完整办公效率套件中必备的应用： 文字处理、 电子表格、 幻灯演示、 绘图工具、 数据库管理程序， 以及用于创建和编辑数学公式的程序。 它提供了许多不同语言的支持 ── 国际化支持除了界面之外， 还包括了拼写检查器和字典。

LibreOffice 的字处理程序使用了内建的 XML 文件格式， 以期获得更好的可移植性和灵活性。 电子表格程序提供了一种可以与外部数据库交互的宏语言支持。 LibreOffice 目前已经可以稳定运行于 Windows®、 Linux、 FreeBSD 和 Mac OS® X。 关于 LibreOffice 的更多信息可以在 [LibreOffice 网站](http://www.libreoffice.org/) 找到。

如果希望通过预编译的二进制包安装 LibreOffice， 执行：

```
# `pkg_add -r libreoffice`
```

### 注意:

如果运行的是 FreeBSD 的 -RELEASE 版本， 这个命令应该不会遇到任何问题。

装好软件包之后， 需要用下面的命令来安装 LibreOffice：

```
% `libreoffice`
```

### 注意:

在首次运行时， 系统会询问一系列问题， 并在当前用户的主目录中创建 `.libreoffice` 目录。

如果 LibreOffice 软件包不可用， 您还是可以通过 port 安装。 不过， 请注意编译它需要相当多的磁盘空间和时间。

```
# `cd /usr/ports/editors/libreoffice`
# `make install clean`
```

### 注意:

如果希望编译本地化的版本， 把前面的命令换成：

```
# `make LOCALIZED_LANG=your_language install clean`
```

您需要把 *`your_language`* 换成正确的语言 ISO 代码。 可用的代码可以在 port 的 `Makefile` 中的 `pre-fetch` target 中找到。

完成联编和安装之后， 就可以用下面的命令运行 LibreOffice 了：

```
% `libreoffice`
```

## 7.4. 文档查看器

UNIX® 系统出现以来， 一些新的文档格式开始流行起来； 它们所需要的标准查看器可能不一定在系统内。 本节中， 我们将了解如何安装它们。

这节涵盖如下应用程序:

| 软件名称 | 资源需求 | 安装时间 | 主要依赖 |
| --- | --- | --- | --- |
| Acrobat Reader® | 少 | 少 | Linux 二进制兼容 |
| gv | 少 | 少 | Xaw3d |
| Xpdf | 少 | 少 | FreeType |
| GQview | 少 | 少 | Gtk+ 或 GNOME |

### 7.4.1. Acrobat Reader®

现在许多文档都用 PDF 格式， 根据“轻便小巧文档格式”的定义。一个被建议使用的查看器是 Acrobat Reader®，由 Adobe 所发行的 Linux 版本。因为 FreeBSD 能够运行 Linux 二进制文件， 所以它也可以用在 FreeBSD 中。

要从 Ports collection 安装 Acrobat Reader® 8， 只需：

```
# `cd /usr/ports/print/acroread8`
# `make install clean`
```

由于授权的限制， 我们不提供预编译的版本。

### 7.4.2. gv

gv 是 PostScript® 和 PDF 文件格式查看器。它源自 ghostview 因为使用 Xaw3d 函数库让它看起来更美观。 它很快而且界面很干净。gv 有很多特性比如象纸张大小、刻度或者抗锯齿。 大部分操作都可以只用键盘或鼠标完成。

安装 gv package，如下：

```
# `pkg_add -r gv`
```

如果您无法获取预编译的包， 则可以使用 Ports Collection：

```
# `cd /usr/ports/print/gv`
# `make install clean`
```

### 7.4.3. Xpdf

如果您想要一个小型的 FreeBSD PDF 查看器， Xpdf 是一个小巧并且高效的查看器。 它只需要很少的资源而且非常稳定。它使用标准的 X 字体并且不需要 Motif® 或者其它的 X 工具包。

安装 Xpdf package，使用如下命令：

```
# `pkg_add -r xpdf`
```

如果 package 不可用或者您宁愿使用 Ports Collection，如下：

```
# `cd /usr/ports/graphics/xpdf`
# `make install clean`
```

一旦安装完成，您就可以启动 Xpdf 并且使用鼠标右键来使用菜单。

### 7.4.4. GQview

GQview 是一个图片管理器。 您可以单击鼠标来观看一个文件、开启一个外部编辑器、 使用预览和更多的功能。它也有幻灯片播放模式和一些基本的文件操作。 您可以管理采集的图片并且很容易找到重复的。 GQview 可以全屏幕观看并且支持国际化。

如果您想要安装 GQview package，如下：

```
# `pkg_add -r gqview`
```

如果您没有可用的 package 或者您宁愿使用 Ports Collection，如下：

```
# `cd /usr/ports/graphics/gqview`
# `make install clean`
```

## 7.5. 财务

假如，基于任何的理由，您想要在 FreeBSD Desktop 管理您个人的财政，有一些强大并且易于使用的软件可以被您选择安装。 它们中的一些与流行的文件格式兼容象 Quicken 和 Excel 文件。

本节涵盖如下程序：

| 软件名称 | 资源需求 | 安装时间 | 主要依赖 |
| --- | --- | --- | --- |
| GnuCash | 少 | 长 | GNOME |
| Gnumeric | 少 | 长 | GNOME |
| Abacus | 少 | 少 | Tcl/Tk |
| KMyMoney | 少 | 长 | KDE |

### 7.5.1. GnuCash

GnuCash 是 GNOME 的一部分，GNOME 致力于为最终用户提供用户友好且功能强大的软件。使用 GnuCash，您可以关注您的收入和开支、您的银行帐户， 或者您的股票。它的界面特性看起来非常的专业。

GnuCash 提供一个智能化的注册、帐户分级系统、 很多键盘快捷方式和自动完成方式。它能分开一个单个的处理到几个详细的部分。 GnuCash 能导入和合并 Quicken QIF 文件格式。 它也支持大部分的国际日期和流行的格式。

在您的系统中安装 GnuCash 所需的命令如下：

```
# `pkg_add -r gnucash`
```

如果 package 不可用，您可以使用 Ports Collection 安装：

```
# `cd /usr/ports/finance/gnucash`
# `make install clean`
```

### 7.5.2. Gnumeric

Gnumeric 是一个电子表格程序， GNOME 桌面环境的一部分。 它以通过元素格式和许多片断的自动填充系统来方便的自动“猜测”用户输入而著称。 它能导入一些流行的文件格式，比如象 Excel、 Lotus 1-2-3 或 Quattro Pro。 Gnumeric 凭借 [math/guppi](http://www.freebsd.org/cgi/url.cgi?ports/math/guppi/pkg-descr) 支持图表。 它有大量的嵌入函数和允许所有通常比如象、数字、货币、日期、 时间等等的一些单元格式。

以 package 方式安装 Gnumeric 的方法如下：

```
# `pkg_add -r gnumeric`
```

如果 package 不可用，您可以使用 Ports Collection 安装：

```
# `cd /usr/ports/math/gnumeric`
# `make install clean`
```

### 7.5.3. Abacus

Abacus 是一个小巧易用的电子表格程序。 它包含许多嵌入函数在一些领域如统计学、财务和数学方面很有帮助。 它能导入和输出 Excel 文件格式。 Abacus 可以产生 PostScript® 输出。

以 package 的方式安装 Abacus 的方法如下：

```
# `pkg_add -r abacus`
```

如果 package 不可用，您可以使用 Ports Collection 安装：

```
# `cd /usr/ports/deskutils/abacus`
# `make install clean`
```

### 7.5.4. KMyMoney

KMyMoney 是一个 KDE 环境下的个人财务管理软件。 KMyMoney 旨在提供并融合各种商业财务管理软件所有的重要特性。 它也同样注重易用性和特有的复式记帐功能。 KMyMoney 能从标准的 Quicken Interchange Format (QIF) 文件导入数据， 追踪投资，处理多种货币并能提供一个财务报告。 另有可用的插件支持导入 OFX 格式的数据。

以 package 的方式安装 KMyMoney 的方法如下：

```
# `pkg_add -r kmymoney2`
```

如果 package 不可用，您可以使用 Ports Collection 安装：

```
# `cd /usr/ports/finance/kmymoney2`
# `make install clean`
```

## 7.6. 总结

尽管 FreeBSD 由于其高性能和可靠性而获得了许多 ISP 的信赖， 但它也完全可以用于桌面环境。 拥有数以千计的 [packages](http://www.FreeBSD.org/applications.html) 和 [ports](http://www.FreeBSD.org/ports/index.html) 能够帮您迅速建立完美的桌面环境。

下面是本章涉及到的所有的软件的简要回顾：

| 软件名称 | Package 名称 | Ports 名称 |
| --- | --- | --- |
| Opera | `opera` | [www/opera](http://www.freebsd.org/cgi/url.cgi?ports/www/opera/pkg-descr) |
| Firefox | `firefox` | [www/firefox](http://www.freebsd.org/cgi/url.cgi?ports/www/firefox/pkg-descr) |
| KOffice | `koffice` | [editors/koffice-kde3](http://www.freebsd.org/cgi/url.cgi?ports/editors/koffice-kde3/pkg-descr) |
| AbiWord | `abiword` | [editors/abiword](http://www.freebsd.org/cgi/url.cgi?ports/editors/abiword/pkg-descr) |
| The GIMP | `gimp` | [graphics/gimp](http://www.freebsd.org/cgi/url.cgi?ports/graphics/gimp/pkg-descr) |
| OpenOffice.org | `openoffice` | [editors/openoffice.org-3](http://www.freebsd.org/cgi/url.cgi?ports/editors/openoffice.org-3/pkg-descr) |
| LibreOffice | `libreoffice` | [editors/libreoffice](http://www.freebsd.org/cgi/url.cgi?ports/editors/libreoffice/pkg-descr) |
| Acrobat Reader® | `acroread` | [print/acroread8](http://www.freebsd.org/cgi/url.cgi?ports/print/acroread8/pkg-descr) |
| gv | `gv` | [print/gv](http://www.freebsd.org/cgi/url.cgi?ports/print/gv/pkg-descr) |
| Xpdf | `xpdf` | [graphics/xpdf](http://www.freebsd.org/cgi/url.cgi?ports/graphics/xpdf/pkg-descr) |
| GQview | `gqview` | [graphics/gqview](http://www.freebsd.org/cgi/url.cgi?ports/graphics/gqview/pkg-descr) |
| GnuCash | `gnucash` | [finance/gnucash](http://www.freebsd.org/cgi/url.cgi?ports/finance/gnucash/pkg-descr) |
| Gnumeric | `gnumeric` | [math/gnumeric](http://www.freebsd.org/cgi/url.cgi?ports/math/gnumeric/pkg-descr) |
| Abacus | `abacus` | [deskutils/abacus](http://www.freebsd.org/cgi/url.cgi?ports/deskutils/abacus/pkg-descr) |
| KMyMoney | `kmymoney2` | [finance/kmymoney2](http://www.freebsd.org/cgi/url.cgi?ports/finance/kmymoney2/pkg-descr) |

## 第 8 章 多媒体

编辑： Ross Lippert.中文翻译： 张 雪平.

## 8.1. 概述

FreeBSD 广泛地支持各种声卡， 让您可以从容地享受来自您的计算机的高保真输出。 这包括了录制和播放 MPEG Audio Layer 3 (MP3)、 WAV、 以及 Ogg Vorbis 等许多种格式声音的能力。 FreeBSD 同时也包括了许多的应用程序，让您可以录音、 增加声音效果以及控制附加的 MIDI 设备。

要是乐于动手， FreeBSD 也能支持播放一般的视频文件和 DVD。 对各种视频媒体进行编码、 转换和播放的应用程序比起处理声音的应用程序略少一些。 例如， 在撰写这章时， FreeBSD Ports Collection 中还没有类似 [audio/sox](http://www.freebsd.org/cgi/url.cgi?ports/audio/sox/pkg-descr) 那样好的重编码工具能够用来在不同的格式之间转换。 不过， 这个领域的软件研发进展是很快的。

本章将介绍配置声卡的必要步骤。 X11 的安装和配置 (第 6 章 *X Window 系统*) 里已经考虑到了您显卡的问题， 但要想有更好的播放效果， 仍需要调整一些东西。

读了本章后，您将知道：

*   如何配置系统识别声卡。

*   测试声卡是否正常工作的方法。

*   如何排除声卡安装中的问题。

*   如何播放和编码 MP3 以及其它格式的音频。

*   X 服务器如何支持视频。

*   哪些好的视频播放/压缩“ports”。

*   如何播放 DVD、 `.mpg` 以及 `.avi` 文件。

*   如何从 CD 和 DVD 中提取文件。

*   怎样配置电视卡。

*   如何配置图像扫描仪。

在读本章这前，您应该：

*   知道如何配置、安装一个新的内核 (第 9 章 *配置 FreeBSD 的内核*)

### 警告:

用[mount(8)](http://www.FreeBSD.org/cgi/man.cgi?query=mount&sektion=8&manpath=freebsd-release-ports) 命令去装载 CD 光盘，至少会产生一个错误， 更糟的情况下会产生 *kernel panic*。 这种媒体所用的编码与通常的 ISO 文件系统是不同的。

## 8.2. 安装声卡

贡献者 Moses Moore.Enhanced by Marc Fonvieille.

### 8.2.1. 配置系统

在开始之前，您应该清楚声卡类型、所用的芯片以及它是 PCI 还是 ISA 卡。 FreeBSD 支持种类繁多的 PCI 和 ISA 卡。检查 [硬件兼容说明](http://www.FreeBSD.org/releases/10.3R/hardware.html) 中支持的音频设备列表看看是否支持您的声卡， 硬件兼容说明也会说明支持您声卡的是哪个驱动程序。

要使用声卡， 就应装载正确的驱动程序。完成的方式有两种： 最简单的是使用命令 [kldload(8)](http://www.FreeBSD.org/cgi/man.cgi?query=kldload&sektion=8&manpath=freebsd-release-ports) 来装载一个内核模块，在命令行输入

```
# `kldload snd_emu10k1`
```

或者在文件 `/boot/loader.conf` 里加入一行，内容如下

```
snd_emu10k1_load="YES"
```

上边实例用于 Creative SoundBlaster® Live! 声卡。 其它可装载的模块列在文件 `/boot/defaults/loader.conf` 里边。 如果不知道应该使用哪个驱动， 您可以尝试加载 `snd_driver` module:

```
# `kldload snd_driver`
```

这是个 meta 驱动，一次加载了最常见的设备驱动。 这会提高搜索正确驱动的速度。也可以通过 `/boot/loader.conf` 工具来加载所有的声卡驱动。

如果希望在加载了 `snd_driver` meta 驱动之后了解到底选择了哪种声卡， 可以通过使用 `cat /dev/sndstat` 来查询 `/dev/sndstat` 文件。

另外，您也可以把支持您声卡的代码静态地编译到内核里去。 下一节就采用这种方式支持硬件给出提示。 关于重新编译内核，请参考 第 9 章 *配置 FreeBSD 的内核*。

#### 8.2.1.1. 定制内核使其支持声卡

要做的第一件事情就是添加通用音频框架驱动 [sound(4)](http://www.FreeBSD.org/cgi/man.cgi?query=sound&sektion=4&manpath=freebsd-release-ports) 到内核中， 您需要添加下面这行到内核配置文件中：

```
device sound
```

接下来就是加入对我们所用声卡的支持了。 首先需要确定我们的声卡需要使用哪一个驱动。 您可以参考 [硬件兼容列表](http://www.FreeBSD.org/releases/10.3R/hardware.html) 所列出的音频设备， 以确定您声卡的驱动。 例如， Creative SoundBlaster® Live! 声卡由 [snd_emu10k1(4)](http://www.FreeBSD.org/cgi/man.cgi?query=snd_emu10k1&sektion=4&manpath=freebsd-release-ports) 驱动来支持。 要添加它， 需要在内核编译配置文件中加入下面一行：

```
device snd_emu10k1
```

一定要阅读驱动的联机手册了解如何使用它们。 关于内核配置文件中声卡驱动的具体写法， 也可以在 `/usr/src/sys/conf/NOTES` 文件中找到。

非即插即用的 ISA 卡可能需要您为内核提供一些关于声卡配置的信息 (IRQ、 I/O 端口， 等等)， 这一点与其他不支持即插即用的 ISA 卡类似。 这项工作可以通过 `/boot/device.hints` 文件来完成。 系统启动时， [loader(8)](http://www.FreeBSD.org/cgi/man.cgi?query=loader&sektion=8&manpath=freebsd-release-ports) 将读取这个文件， 并将其中的配置传给内核。 例如， 旧式的 Creative SoundBlaster® 16 ISA 非即插即用卡需要使用 [snd_sbc(4)](http://www.FreeBSD.org/cgi/man.cgi?query=snd_sbc&sektion=4&manpath=freebsd-release-ports) 驱动并配合 snd_sb16(4)。 您可以在内核编译配置文件中增加如下配置：

```
device snd_sbc
device snd_sb16
```

还有下面这些到 `/boot/device.hints`中：

```
hint.sbc.0.at="isa"
hint.sbc.0.port="0x220"
hint.sbc.0.irq="5"
hint.sbc.0.drq="1"
hint.sbc.0.flags="0x15"
```

这样，声卡使用 `0x220` I/O 端口和 IRQ `5`。

在 `/boot/device.hints` 文件中所使用的语法， 在 [sound(4)](http://www.FreeBSD.org/cgi/man.cgi?query=sound&sektion=4&manpath=freebsd-release-ports) 联机手册中以及所用的具体声卡驱动的联机手册中， 会进行进一步的讲解。

上面所展示的是默认的配置。 有时候， 您可能需要更改 IRQ 或其他配置， 以适应声卡的实际情况。 查看 [snd_sbc(4)](http://www.FreeBSD.org/cgi/man.cgi?query=snd_sbc&sektion=4&manpath=freebsd-release-ports) 联机手册了解更多信息。

### 8.2.2. 测试声卡

用修改过的内核重起，或者加载了需要的模块之后， 声卡将会出现在您的系统消息缓存中 ([dmesg(8)](http://www.FreeBSD.org/cgi/man.cgi?query=dmesg&sektion=8&manpath=freebsd-release-ports))，就像这样：

```
pcm0: <Intel ICH3 (82801CA)> port 0xdc80-0xdcbf,0xd800-0xd8ff irq 5 at device 31.5 on pci0
pcm0: [GIANT-LOCKED]
pcm0: <Cirrus Logic CS4205 AC97 Codec>
```

声卡的状态可以通过 `/dev/sndstat` 文件来查询：

```
# `cat /dev/sndstat`
FreeBSD Audio Driver (newpcm)
Installed devices:
pcm0: <Intel ICH3 (82801CA)> at io 0xd800, 0xdc80 irq 5 bufsz 16384
kld snd_ich (1p/2r/0v channels duplex default)
```

您系统的输出可能与此不同。如果没有看到 `pcm` 设备，回顾并检查一下前面做的。 重新检查您的内核配置文件并保证选择了正确的设备。 常见问题列在 第 8.2.2.1 节 “常见问题” 一节。

如果一切正常，您现在应该拥有一个多功能声卡了。 如果您的 CD-ROM 或者 DVD-ROM 驱动器的音频输出线已经与声卡连在一起， 您可以把 CD 放入驱动器并用 [cdcontrol(1)](http://www.FreeBSD.org/cgi/man.cgi?query=cdcontrol&sektion=1&manpath=freebsd-release-ports) 来播放：

```
% `cdcontrol -f /dev/acd0 play 1`
```

许多应用程序，比如 [audio/workman](http://www.freebsd.org/cgi/url.cgi?ports/audio/workman/pkg-descr) 可以提供一个友好的界面。 您可能想要安装一个应用程序比如 [audio/mpg123](http://www.freebsd.org/cgi/url.cgi?ports/audio/mpg123/pkg-descr) 来听 MP3 音频文件。

另一种快速测试声卡的方法， 是将数据发送到 `/dev/dsp`， 像这样做：

```
% `cat filename > /dev/dsp`
```

这里 `filename` 可以是任意文件。 这行命令会产生一些噪音，证明声卡果真在工作。

### 注意:

设备节点 `/dev/dsp*` 会在需要的时候自动产生。 如果没有使用它们， 则它们不会出现在 [ls(1)](http://www.FreeBSD.org/cgi/man.cgi?query=ls&sektion=1&manpath=freebsd-release-ports) 的输出中。

声卡混音级别可以通过 [mixer(8)](http://www.FreeBSD.org/cgi/man.cgi?query=mixer&sektion=8&manpath=freebsd-release-ports) 命令更改。 更多细节可以在 [mixer(8)](http://www.FreeBSD.org/cgi/man.cgi?query=mixer&sektion=8&manpath=freebsd-release-ports) 联机手册中找到。

#### 8.2.2.1. 常见问题

| 错误信息 | 解决方法 |
| --- | --- |
| sb_dspwr(XX) timed out | I/O 端口没有设置正确。 |
| bad irq XX | IRQ 设置不正确。确信设定的 IRQ 和声卡的 IRQ 是一样的。 |
| xxx: gus pcm not attached, out of memory | 没有足够的内存空间供设置使用。 |
| xxx: can't open /dev/dsp! | 使用命令 `fstat &#124; grep dsp` 进行检查是否有其它的程序打开了设备。 值得注意的是 esound 和 KDE 提供的声卡支持经常是造成麻烦的祸根。 |

另一个问题是许多新式的显卡本身包含它们自己的声音驱动， 用以配合 HDMI 这样的设备使用。 这个声音设备有时会在真正的声卡之前被探测到， 从而成为默认的回放设备， 而使真正的声卡无法发声。 要检查这种情况， 运行 dmesg 并观察 `pcm`。 其输出类似下面这样：

```
...
hdac0: HDA Driver Revision: 20100226_0142
hdac1: HDA Driver Revision: 20100226_0142
hdac0: HDA Codec #0: NVidia (Unknown)
hdac0: HDA Codec #1: NVidia (Unknown)
hdac0: HDA Codec #2: NVidia (Unknown)
hdac0: HDA Codec #3: NVidia (Unknown)
pcm0: <HDA NVidia (Unknown) PCM #0 DisplayPort> at cad 0 nid 1 on hdac0
pcm1: <HDA NVidia (Unknown) PCM #0 DisplayPort> at cad 1 nid 1 on hdac0
pcm2: <HDA NVidia (Unknown) PCM #0 DisplayPort> at cad 2 nid 1 on hdac0
pcm3: <HDA NVidia (Unknown) PCM #0 DisplayPort> at cad 3 nid 1 on hdac0
hdac1: HDA Codec #2: Realtek ALC889
pcm4: <HDA Realtek ALC889 PCM #0 Analog> at cad 2 nid 1 on hdac1
pcm5: <HDA Realtek ALC889 PCM #1 Analog> at cad 2 nid 1 on hdac1
pcm6: <HDA Realtek ALC889 PCM #2 Digital> at cad 2 nid 1 on hdac1
pcm7: <HDA Realtek ALC889 PCM #3 Digital> at cad 2 nid 1 on hdac1
...
```

此处显卡 (`NVidia`) 先于真正的声卡 (`Realtek ALC889`) 被探测到。 要使用声卡作为默认的回放设备， 将 `hw.snd.default_unit` 改为对应的设备编号：

```
# `sysctl hw.snd.default_unit=n`
```

这里的 `n` 是希望使用的声音设备编号， 在这个例子中是 `4`。 您可以在 `/etc/sysctl.conf` 中写上这个配置来令其永久性生效：

```
hw.snd.default_unit=*`4`*
```

### 8.2.3. 利用多个声源

贡献者 Munish Chopra.

通常而言， 会希望多个音源能够同时播放， 例如， esound 或者 artsd 就可能不支持与其它程序共享音频设备。

FreeBSD 可以通过 *虚拟声道(Virtual Sound Channels)* 来达到这样的效果， 它可以用 [sysctl(8)](http://www.FreeBSD.org/cgi/man.cgi?query=sysctl&sektion=8&manpath=freebsd-release-ports) 来启用。 虚拟的声道可以能过在内核里混合声音来混合声卡里播放的声道。

使用三条 sysctl 命令来设置虚拟声道的数目。 如果您是 `root` 用户， 执行下面的操作：

```
# `sysctl dev.pcm.0.play.vchans=4`
# `sysctl dev.pcm.0.rec.vchans=4`
# `sysctl hw.snd.maxautovchans=4`
```

上面的实例设定了 4 个虚拟声道，这也是实际上所使用的数目。 `dev.pcm.0.play.vchans=4` 和 `dev.pcm.0.rec.vchans=4` 是 `pcm0` 用来播放与录音的虚拟声道数， 一当链接上一个设备它就可配置了。 `hw.snd.maxautovchans` 是分配给新的音频设备的虚拟声道数， 此时这个设备要用 [kldload(8)](http://www.FreeBSD.org/cgi/man.cgi?query=kldload&sektion=8&manpath=freebsd-release-ports) 来链接。 因为 `pcm` 模块可以独立装载许多硬件驱动程序， 因此 `hw.snd.maxautovchans` 也就可以存储分配给以后链接到的设备的虚拟声道数。 可参阅 [pcm(4)](http://www.FreeBSD.org/cgi/man.cgi?query=pcm&sektion=4&manpath=freebsd-release-ports) 手册页义获取更多细节。

### 注意:

您不能在使用某个设备的时候改变其虚拟通道数。 首先需要关闭所有使用该设备的程序， 如音乐播放器或声音服务。

当应用程序请求 `/dev/dsp0` 时， 系统会自动为其分配正确的 `pcm` 设备。

### 8.2.4. 如何设置混音器通道值

这一节的作者是 Josef El-Rayes.

不同的混音通道的默认音量是硬编码进 [pcm(4)](http://www.FreeBSD.org/cgi/man.cgi?query=pcm&sektion=4&manpath=freebsd-release-ports) 驱动程序的。 同时， 也有很多应用或服务程序提供了允许用户直接设置并记住这些值的功能。 不过这并不是一个很好的解决方案， 您可能希望在驱动一级有一个可以设置的默认值。 这可以通过在 `/boot/device.hints` 定义适当的值来实现。 例如：

```
hint.pcm.0.vol="50"
```

这将在 [pcm(4)](http://www.FreeBSD.org/cgi/man.cgi?query=pcm&sektion=4&manpath=freebsd-release-ports) 模块加载时， 将通道音量设置为默认的 50。

## 8.3. MP3 音频

贡献者 Chern Lee.

MP3 (MPEG Layer 3 Audio)达到过 CD 音质的效果，FreeBSD 工作站没理由会缺少这样的好东东。

### 8.3.1. MP3 播放器

目前为止， 最为流行的 X11 MP3 播放器是 XMMS (X 多媒体系统)。 Winamp 的肤面可以直接用于 XMMS， 因为它的 GUI 几乎和 Nullsoft 的 Winamp 完全一样。 另外， XMMS 也提供了内建的插件支持。

XMMS 可以通过 [multimedia/xmms](http://www.freebsd.org/cgi/url.cgi?ports/multimedia/xmms/pkg-descr) port 或 package 来安装。

XMMS 的界面很直观， 它提供了播放列表、 图形化均衡器等等。 如果您熟悉 Winamp， 就会感觉 XMMS 很容易使用。

[audio/mpg123](http://www.freebsd.org/cgi/url.cgi?ports/audio/mpg123/pkg-descr) port 提供了一个命令行界面的 MP3 播放器。

mpg123 可以在执行时通过命令行指定声音设备和要播放的 MP3 文件， 假设你的声音设备是 `/dev/dsp1.0` 并且你想要播放的 MP3 文件为 *`Foobar-GreatestHits.mp3`* 你可以键入以下的命令：

```
# `mpg123 -a /dev/dsp1.0 Foobar-GreatestHits.mp3`
High Performance MPEG 1.0/2.0/2.5 Audio Player for Layer 1, 2 and 3.
Version 0.59r (1999/Jun/15). Written and copyrights by Michael Hipp.
Uses code from various people. See 'README' for more!
THIS SOFTWARE COMES WITH ABSOLUTELY NO WARRANTY! USE AT YOUR OWN RISK!

Playing MPEG stream from Foobar-GreatestHits.mp3 ...
MPEG 1.0 layer III, 128 kbit/s, 44100 Hz joint-stereo

```

### 8.3.2. 抓取 CD 音轨

在对 CD 或 CD 音轨编码成 MP3 之前， CD 上的音频数据应先抓到硬盘里。 这个可以通过复制原始的 CDDA(CD 数字音频)数据成为波形(WAV)文件。

工具 `cdda2wav` 是 [sysutils/cdrtools](http://www.freebsd.org/cgi/url.cgi?ports/sysutils/cdrtools/pkg-descr) 套件的一部份，可用来从 CD 中获取音频及其相关信息。

把 CD 放到光驱里，下面的命令可以完成 (作为 `root`用户) 把整张 CD 分割成单个 (每个音轨) 的 WAV 文件：

```
# `cdda2wav -D 0,1,0 -B`
```

cdda2wav 支持 ATAPI (IDE)光驱。 从 IDE 光驱中抓取音轨， 需要用设备名称代替 SCSI 的单元号。 例如， 想从 IDE 光驱中抓取第 7 道音轨：

```
# `cdda2wav -D /dev/acd0 -t 7`
```

参数 `-D *`0,1,0`*` 表示 SCSI 设备 `0,1,0`， 与命令 `cdrecord -scanbus` 的输出相对应。

抓取单轨，要使用选项 `-t`，如下所示：

```
# `cdda2wav -D 0,1,0 -t 7`
```

这个实例用于抓取第七个音轨。要抓取一定范围的音轨，如从 1 到 7：

```
# `cdda2wav -D 0,1,0 -t 1+7`
```

利用[dd(1)](http://www.FreeBSD.org/cgi/man.cgi?query=dd&sektion=1&manpath=freebsd-release-ports)也可以从 ATAPI 光驱中抓取音轨，从 第 19.6.5 节 “复制音频 CD” 可以了解更多。

### 8.3.3. MP3 编码

现今，可选的 MP3 编码器是 lame。 Lame 可以从 ports 树里的 [audio/lame](http://www.freebsd.org/cgi/url.cgi?ports/audio/lame/pkg-descr) 处找到。

利用抓取的 WAV 文件，下边的命令就可以把 `audio01.wav` 转换成 `audio01.mp3`：

```
# `lame -h -b 128 \
--tt "Foo Song Title" \
--ta "FooBar Artist" \
--tl "FooBar Album" \
--ty "2001" \
--tc "Ripped and encoded by Foo" \
--tg "Genre" \
audio01.wav audio01.mp3`
```

128 kbits 是标准的 MP3 位率(bitrate)。 许多人可能喜欢更高的品质例如 160 或 192。 更高的位率， 会使 MP3 占用更多的磁盘空间--但音质会更高。选项 `-h` 控制 “高品质但低速度 (higher quality but a little slower)” 模式的开关。 选项 `--t` 表示把 ID3 标签--通常包含了歌曲的信息， 植入到 MP3 文件里。 其它的编码选项可以查询 lame 的联机手册。

### 8.3.4. MP3 解码

要把 MP3 歌曲刻录成音乐 CD，就需要把它转换成非压缩的波形(WAV)格式。 XMMS 和 mpg123 都支持把 MP3 输出成非压缩格式文件。

在 XMMS 中输出到磁盘：

1.  启动 XMMS.

2.  在窗口里右击鼠标，弹出 XMMS 菜单。

3.  在 `选项(Options)` 里选择 `设定(Preference)`。

4.  改变输出插件成 “写磁盘插件(Disk Writer Plugin)”。

5.  按 `配置(Configure)`。

6.  输入或选择一个目录用于存放解压的文件。

7.  象平常一样，把 MP3 文件装入到 XMMS 里边， 把音量调节到 100%并且关掉 EQ 设定。

8.  按一下 `播放(Play)` ── XMMS 如同在播放 mp3 一样，只是听不到声音。 实际上是在播放 mp3 到一个文件里。

9.  要想再听 MP3 歌曲，记得把默认的输出插件设回原来的值。

用 mpg123 进行标准输出：

*   执行 `mpg123 -s audio01.mp3 > audio01.pcm`

XMMS 输出的文件是波形(WAV)格式， 而 mpg123 则把 MP3 转换成无压缩的 PCM 音频数据。两种格式都支持用 cdrecord 刻录成音乐 CD。 使用 [burncd(8)](http://www.FreeBSD.org/cgi/man.cgi?query=burncd&sektion=8&manpath=freebsd-release-ports) 您就必须使用无压缩的 PCM。 如果选择波形格式， 就要注意在每道开始时的一小点杂音， 这段声音是波形文件的头部份。 可以使用工具 SoX 来轻松去除。 SoX 可从 [audio/sox](http://www.freebsd.org/cgi/url.cgi?ports/audio/sox/pkg-descr) port 或包(package)中安装得到：

```
% `sox -t wav -r 44100 -s -w -c 2 track.wav track.raw`
```

阅读 第 19.6 节 “创建和使用光学介质(CD)”") 这部份可以了解到更多在 FreeBSD 里刻盘的信息。

## 8.4. 视频回放

贡献者 Ross Lippert.

视频回放是个很新并且迅速发展中的应用领域。 一定要有耐心，因为不是所有的事情都象处音频那么顺利。

在开始之前，您要了解显卡的类型以及它所用的芯片的类型。 尽管 Xorg 支持大量的显卡， 但能达到好的回放效果的却寥寥无几。 在 X11 运行时，您可以使用命令 [xdpyinfo(1)](http://www.FreeBSD.org/cgi/man.cgi?query=xdpyinfo&sektion=1&manpath=freebsd-release-ports) 获得使用您的显卡的 X 服务器所支持的扩展列表。

为了评估各种播放器和设置，您需要有一小段用作测试的 MPEG 文件。 由于一些 DVD 播放器会默认地在 `/dev/dvd` 里去找 DVD 文件， 因此， 您会发现建立符号链接到恰当的设备会很有用：

```
# `ln -sf /dev/acd0 /dev/dvd`
# `ln -sf /dev/acd0 /dev/rdvd`
```

注意：由于 [devfs(5)](http://www.FreeBSD.org/cgi/man.cgi?query=devfs&sektion=5&manpath=freebsd-release-ports) 本身的原因， 像这样手工建立的链接在重启后将不会存在。 想要无论什么时候您启动系统都能自动建立符号链接， 那就把下边这行加到 `/etc/devfs.conf` 里边：

```
link acd0 dvd
link acd0 rdvd
```

另外，DVD 解密要求调用专用的 DVD-ROM 函数，要求把许可定到 DVD 设备里。

为了改善 X11 界面使用共享内存的能力， 建议提高一些 [sysctl(8)](http://www.FreeBSD.org/cgi/man.cgi?query=sysctl&sektion=8&manpath=freebsd-release-ports) 变量的值：

```
kern.ipc.shmmax=67108864
kern.ipc.shmall=32768
```

### 8.4.1. 测定视频的性能

在 X11 下有几种可以显示图像的方式。 到底哪个能工作很大程度上依赖于硬件。 首先， 下边描述的每一种方法在不同的硬件上都会有不同的品质。 其次， 在 X11 里的图像显示近来引起普遍的关注， 随着 Xorg 的每一个版本， 都会有很大的突破。

常见图像接口列表：

1.  X11: 一般性的使用共享内存的 X11 输出。

2.  XVideo: 一种 X11 接口扩展，支持任何 X11 图像的可拖拉。

3.  SDL: 简单直接媒体层。

4.  DGA: 直接图片存取。

5.  SVGAlib: 低层次掌控图片层。

#### 8.4.1.1. XVideo

Xorg 有种扩展叫做 *XVideo* (或称 Xvideo, Xv, xv)， 它可以通过一个特殊的加速器直接把图像显示在可拖拉的对象里。 即使在低端的计算机 (例如我的 PIII 400 Mhz 膝上电脑)， 这个扩展也提供了很好的播放质量。

要了解这一扩展是否在正常工作， 使用 `xvinfo` 命令：

```
% `xvinfo`
```

如果显示结果如下，那您的显卡就支持 XVideo：

```
X-Video Extension version 2.2
screen #0
  Adaptor #0: "Savage Streams Engine"
    number of ports: 1
    port base: 43
    operations supported: PutImage
    supported visuals:
      depth 16, visualID 0x22
      depth 16, visualID 0x23
    number of attributes: 5
      "XV_COLORKEY" (range 0 to 16777215)
              client settable attribute
              client gettable attribute (current value is 2110)
      "XV_BRIGHTNESS" (range -128 to 127)
              client settable attribute
              client gettable attribute (current value is 0)
      "XV_CONTRAST" (range 0 to 255)
              client settable attribute
              client gettable attribute (current value is 128)
      "XV_SATURATION" (range 0 to 255)
              client settable attribute
              client gettable attribute (current value is 128)
      "XV_HUE" (range -180 to 180)
              client settable attribute
              client gettable attribute (current value is 0)
    maximum XvImage size: 1024 x 1024
    Number of image formats: 7
      id: 0x32595559 (YUY2)
        guid: 59555932-0000-0010-8000-00aa00389b71
        bits per pixel: 16
        number of planes: 1
        type: YUV (packed)
      id: 0x32315659 (YV12)
        guid: 59563132-0000-0010-8000-00aa00389b71
        bits per pixel: 12
        number of planes: 3
        type: YUV (planar)
      id: 0x30323449 (I420)
        guid: 49343230-0000-0010-8000-00aa00389b71
        bits per pixel: 12
        number of planes: 3
        type: YUV (planar)
      id: 0x36315652 (RV16)
        guid: 52563135-0000-0000-0000-000000000000
        bits per pixel: 16
        number of planes: 1
        type: RGB (packed)
        depth: 0
        red, green, blue masks: 0x1f, 0x3e0, 0x7c00
      id: 0x35315652 (RV15)
        guid: 52563136-0000-0000-0000-000000000000
        bits per pixel: 16
        number of planes: 1
        type: RGB (packed)
        depth: 0
        red, green, blue masks: 0x1f, 0x7e0, 0xf800
      id: 0x31313259 (Y211)
        guid: 59323131-0000-0010-8000-00aa00389b71
        bits per pixel: 6
        number of planes: 3
        type: YUV (packed)
      id: 0x0
        guid: 00000000-0000-0000-0000-000000000000
        bits per pixel: 0
        number of planes: 0
        type: RGB (packed)
        depth: 1
        red, green, blue masks: 0x0, 0x0, 0x0
```

同时注意：列出来的格式(YUV2, YUV12, 等等) 并不总是随着 XVdieo 的每一次执行而存在。没有它们可能会迷惑某些人。

如果结果看起来是这样：

```
X-Video Extension version 2.2
screen #0
no adaptors present
```

那么您的显卡可以就不支持 XVideo 功能。

如果您的卡不支持 XVideo， 则只是说明您的显示器在满足刷新图像的计算要求上存在更大的困难。 尽管显卡和处理器很重要，您仍然会有个不错的显示效果。 此外， 您也可以参考我们提供的文献， 在 第 8.4.3 节 “进一步了解” 中有所介绍。

#### 8.4.1.2. 简单直接媒体层

简单直接媒体层(SDL)，原意是做为 Microsoft® Windows®、BeOS 以及 UNIX® 之间的端口层，允许跨平台应用发展，更高效地利用声卡和图形卡。SDL 层可以在低层访问硬件， 有时这样做就比 X11 接口层更为高效。

关于 SDL， 可以参考 [devel/sdl12](http://www.freebsd.org/cgi/url.cgi?ports/devel/sdl12/pkg-descr)。

#### 8.4.1.3. 直接图形存取

直接图形存取 (Direct Graphics Access) 是一种 X11 扩展， 通过它， 应用程序能够绕过 X 服务， 并直接修改画面缓存 (framebuffer)。 由于它依赖一种底层的内存映射来实现其功能， 因此使用它的程序必须以 `root` 身份来执行。

DGA 扩展可以通过 [dga(1)](http://www.FreeBSD.org/cgi/man.cgi?query=dga&sektion=1&manpath=freebsd-release-ports) 来完成测试和性能测量。 运行 `dga` 时， 它将随按键改变现实的颜色。 按 **q** 退出这个程序。

### 8.4.2. Ports 和 包(Packages) 对视频的解决

这部份主要讨论在 FreeBSD Ports 集中提供的可用于视频回放的软件。 视频回放在软件发展中是个很活跃的领域， 并且各种不同程序的功能可能与这里的描述不尽相同。

首先要弄清楚的重要一点是在 FreeBSD 上使用的视频程序其发展与在 Linux 里使用的是一样的。 大部份程序都还处在β阶段。使用 FreeBSD 的包可能面对的问题：

1.  一个应用程序不能播放其它程序制作的文件。

2.  一个应用程序不能播放其自已制作的文件。

3.  不同机上的同样的程序，各自重新建立(rebuild)了一次， 播放同一个文件结果也会有不同。

4.  一个看起来没什么的过滤器， 如图像尺寸的调整， 也有可能因为一个调整例程的问题变得很不象样。

5.  应用程序频繁地留下垃圾(dumps core)。

6.  没有随 port 一起安装的文档可以在网上或者 port 的 `work` 目录中找到。

这些程序中许多也体现了 “Linux 主义”。即， 有些问题来自于(程序)使用的标准库存在于 Linux 的发行版中， 或者有些是 Linux 内核的功能， 而该程序的作者事先所假定了的是 Linux 内核。这些问题并不总是被 port 编护人员注意到或处理过， 这也就可能导致如下问题：

1.  使用`/proc/cpuinfo`去检测处理器的特性。

2.  滥用线程可能导致一个程序悬挂完成，而不是完全中止。

3.  软件还不属于 FreeBSD Ports 集，而又与其它程序经常地一起使用。

现在，这些程序的开发人员也已同 port 的维护人员进行了联合， 以减少制作 port 时出错。

#### 8.4.2.1. MPlayer

MPlayer 是近来开发的同时也正迅速发展着的一个视频播放器。 MPlayer 团队的目标是在 Linux 和其它 UNIX 系统中的速度和机动性能。 在团队的创始人实在受不了当时可用的播放器的性能时， 这个计划就开始了。 有人也许会说图形接口已经成为新型设计的牺牲品。 但是一旦您习惯了命令行选项和按键控制方式，它就能表现得很好。

##### 8.4.2.1.1. 创建 MPlayer

MPlayer 可以从 [multimedia/mplayer](http://www.freebsd.org/cgi/url.cgi?ports/multimedia/mplayer/pkg-descr) 找到。 MPlayer 在联编过程中会进行许多硬件检测， 而得到的可执行文件因此将无法移植到其他系统中使用。 因此， 从 ports 完成联编而不是安装预编译的包就很重要。 另外， 在 `make` 命令行还可以指定许多选项， 在 `Makefile` 中有所描述， 接下来我们开始联编：

```
# `cd /usr/ports/multimedia/mplayer`
# `make`
N - O - T - E

Take a careful look into the Makefile in order
to learn how to tune mplayer towards you personal preferences!
For example,
make WITH_GTK1
builds MPlayer with GTK1-GUI support.
If you want to use the GUI, you can either install
/usr/ports/multimedia/mplayer-skins
or download official skin collections from
http://www.mplayerhq.hu/homepage/dload.html

```

默认的 port 选项对于绝大多数用户来说是够用了。 不过， 如果您需要 XviD 编解码器， 则必须指定 `WITH_XVID` 这个命令行选项。 默认的 DVD 设备也可以用 `WITH_DVD_DEVICE` 选项来定义， 其默认值是 `/dev/acd0`。

撰写这一章的时候， MPlayer port 的联编过程包括了 HTML 文档和两个可执行文件， `mplayer` 和 `mencoder`， 后者是一个视频再编码工具。

MPlayer 的 HTML 文档提供了丰富的内容。 如果读者发现本章中缺少关于视频硬件的一些信息， 则 MPlayer 的文档将是十分详尽的补充。 如果您正在找关于 UNIX® 中的视频支持的资料， 您绝对应该花一些时间来阅读 MPlayer 的文档。

##### 8.4.2.1.2. 使用 MPlayer

任何 MPlayer 用户必须在其用户主目录下建立一个叫 `.mplayer` 的子目录。 输入下边的内容来建立这个必须的子目录：

```
% `cd /usr/ports/multimedia/mplayer`
% `make install-user`
```

在 `mplayer` 的手册里列出了它的命令选项。 HTML 文档里有更为详细的信息。 这部份里， 我们只是描述了很少的常见应用。

要播放一个文件，如 `testfile.avi`， 可以通过各种视频接口当中的某一个去设置 `-vo` 选项：

```
% `mplayer -vo xv testfile.avi`
```

```
% `mplayer -vo sdl testfile.avi`
```

```
% `mplayer -vo x11 testfile.avi`
```

```
# `mplayer -vo dga testfile.avi`
```

```
# `mplayer -vo 'sdl:dga' testfile.avi`
```

所有这些选项都是值得一试的， 因为它们的性能依赖很多因素，并且都与硬件密切相关。

要播放 DVD， 需要把 `testfile.avi` 改为 `dvd://*`N`* -dvd-device *`DEVICE`*`。 这里 *`N`* 是要播放的节目编号， 而 `DEVICE` 则是 DVD-ROM 的设备节点。 例如， 要播放 `/dev/dvd` 的第三个节目：

```
# `mplayer -vo xv dvd://3 -dvd-device /dev/dvd`
```

### 注意:

可以在编译 MPlayer 时， 通过 `WITH_DVD_DEVICE` 来指定默认的 DVD 设备。 系统内定的默认设备是 `/dev/acd0`。 更多细节， 请参考 port 的 `Makefile`。

要停止、暂停、前进等等，可以参考设定的按键---这些可以通过 `mplayer -h` 得到或查看手册。

另外，回放的重要选项是：用于全屏模式的 `-fs -zoom` 和起辅助完成作用的`-framedrop`。

为了让 mplayer 的命令行不是太长，使用者可以通过建立一个文件 `.mplayer/config` 来设定如下默认选项：

```
vo=xv
fs=yes
zoom=yes
```

最后，`mplayer` 可以把 DVD 题目(title)抓取成为 `.vob` 文件。为了从 DVD 中导出第二个题目，请输入：

```
# `mplayer -dumpstream -dumpfile out.vob dvd://2 -dvd-device /dev/dvd`
```

输出文件 `out.vob` 将是 MPEG 并且可以被这部份描述的其它 “包” 利用。

##### 8.4.2.1.3. mencoder

在使用 `mencoder` 之前， 首先熟悉其 HTML 文档中所介绍的选项是一个不错的主意。 它提供了联机手册， 但如果没有 HTML 文档则帮助不大。 有无数种方法来提高视频品质、 降低比特率、 修改格式， 而这些技巧可能会影响性能。 下面是几个例子， 第一个是简单地复制：

```
% `mencoder input.avi -oac copy -ovc copy -o output.avi`
```

不正确的命令选项组合可能使生成的文件不能被 `mplayer` 播放。因此，如果您只是想抓取文件， 一定在 `mplayer` 里使用 “`-dumpfile`”。

转换 `input.avi` 成为带有 MPEG3 音频编码 (要求 [audio/lame](http://www.freebsd.org/cgi/url.cgi?ports/audio/lame/pkg-descr) ) 的 MPEG4 编码：

```
% `mencoder input.avi -oac mp3lame -lameopts br=192 \
	 -ovc lavc -lavcopts vcodec=mpeg4:vhq -o output.avi`
```

这样就产生了可被 `mplayer` 和 `xine`播放的输出。

`input.avi` 可以换成 `dvd://1 -dvd-device /dev/dvd` 并以 `root` 的身份来执行， 以重新对 DVD 节目进行编码。 由于您第一次做这样的工作时很可能会对结果不太满意， 建议您首先把节目复制成文件， 然后对它进行操作。

#### 8.4.2.2. xine 视频播放器

xine 视频播放器是一个关注范围很广的项目， 它不仅看准多合一的视频解决， 而且出品了一个可再用的基本库和一个可扩展插件的可执行模块。 发行有 “包” 和 port 版本-- [multimedia/xine](http://www.freebsd.org/cgi/url.cgi?ports/multimedia/xine/pkg-descr)。

xine 播放器仍然很粗糙， 但这很显然与好开头无关。实际上 xine 要求你有快速的 CPU 和快速的显卡来运行，或者需要支持 XVideo 扩展。 图形界面(GUI)可以使用，但很勉强。

到写这章时，还没有可用于播放 CSS 编码的 DVD 文件的输入模块随同 xine 一起发行。 第三方的建造(builds)里内建有这样的模块， 但都不属于 FreeBSD Ports 集。

与 MPlayer 相比， xine 为用户考虑得更多， 但同时，对用户来说也少了很多有条理的控制方式。 xine 播放器在 XVideo 接口上做得不错。

默认情况下，播放器 xine 启动的时候会使用图形界面。那么就可以使用菜单打开指定的文件：

```
% `xine`
```

另外，没有图形界面也可以使用如下命令立即打开播放文件：

```
% `xine -g -p mymovie.avi`
```

#### 8.4.2.3. 使用 transcode

transcode 这个软件并不是播放器， 而是一系列用于对视频和音频文件进行重新编码的工具。 通过使用 transcode， 就可以拥有使用带 `stdin/stdout` 接口的命令行工具来合并视频文件， 以及修复坏损文件的能力。

在联编 [multimedia/transcode](http://www.freebsd.org/cgi/url.cgi?ports/multimedia/transcode/pkg-descr) port 时可以指定大量选项， 我们建议使用下面的命令行来构建 transcode：

```
# `make WITH_OPTIMIZED_CFLAGS=yes WITH_LIBA52=yes WITH_LAME=yes WITH_OGG=yes \
WITH_MJPEG=yes -DWITH_XVID=yes`
```

对于多数用户而言， 前述配置已经足够了。

为了说明 `transcode` 的功能， 下面的例子展示了如何将 DivX 转换为 PAL MPEG-1 文件 (PAL VCD)：

```
% `transcode -i input.avi -V --export_prof vcd-pal -o output_vcd`
% `mplex -f 1 -o output_vcd.mpg output_vcd.m1v output_vcd.mpa`
```

生成的 MPEG 文件， `output_vcd.mpg`， 可以通过 MPlayer 来播放。 您甚至可以直接将这个文件刻录到 CD-R 介质上来创建 Video CD， 如果希望这样做的话， 需要安装 [multimedia/vcdimager](http://www.freebsd.org/cgi/url.cgi?ports/multimedia/vcdimager/pkg-descr) 和 [sysutils/cdrdao](http://www.freebsd.org/cgi/url.cgi?ports/sysutils/cdrdao/pkg-descr) 这两个程序。

`transcode` 提供了联机手册， 但您仍应参考 [transcode wiki](http://www.transcoding.org/cgi-bin/transcode) 以了解更多信息和例子。

### 8.4.3. 进一步了解

FreeBSD 里不同的视频软件包正迅速发展中。 很可能在不久的将来，这里所谈到的问题都将得到解决。 同时，有些人想超越 FreeBSD 的音/像(A/V)能力， 那他们就不得不从一些 FAQ 和指南里学知识， 并使用一些不同的应用程序。 这里就给这些读者指出一些补充信息。

[MPlayer 文档](http://www.mplayerhq.hu/DOCS/) 是很技术性的。 这些文档可以给那些希望获得关于 UNIX®视频高级技术的人们提供参考。 MPlayer 邮件列表很不喜欢没耐心阅读文档的人， 如果您发现什么问题想报告给他们，请首先 RTFM。

[xine HOWTO](http://dvd.sourceforge.net/xine-howto/en_GB/html/howto.html) 里边有一章是关于提高性能的，对所有的播放器都很适应。

最后是一些很有前途的程序，读者可以试一下：

*   [Avifile](http://avifile.sourceforge.net/)，它就是 [multimedia/avifile](http://www.freebsd.org/cgi/url.cgi?ports/multimedia/avifile/pkg-descr) port。

*   [Ogle](http://www.dtek.chalmers.se/groups/dvd/) 它就是 [multimedia/ogle](http://www.freebsd.org/cgi/url.cgi?ports/multimedia/ogle/pkg-descr) port。

*   [Xtheater](http://xtheater.sourceforge.net/)

*   [multimedia/dvdauthor](http://www.freebsd.org/cgi/url.cgi?ports/multimedia/dvdauthor/pkg-descr)， 一个制作 DVD 节目的源码开放包。

## 8.5. 安装电视卡

原创： Josef El-Rayes.改编：Marc Fonvieille.

### 8.5.1. 介绍

电视卡可以让您在您的计算机里观看到无线或有线电视。 许多卡是通过 RCA 或 S-video 输入接收复合视频， 而且有些卡还带有调频广播接收器。

FreeBSD 通过[bktr(4)](http://www.FreeBSD.org/cgi/man.cgi?query=bktr&sektion=4&manpath=freebsd-release-ports)驱动程序，提供了对基于 PCI 的电视卡的支持， 要求这些卡使用的是 Brooktree Bt848/849/878/879 或 Conexant CN-878/Fusion 878a 视频采集芯片。 您还要确保这个板上带的有被支持的调谐器， 参考[bktr(4)](http://www.FreeBSD.org/cgi/man.cgi?query=bktr&sektion=4&manpath=freebsd-release-ports)手册查看所支持的调谐器列表。

### 8.5.2. 增加驱动程序

要使用您的卡，您就要装载[bktr(4)](http://www.FreeBSD.org/cgi/man.cgi?query=bktr&sektion=4&manpath=freebsd-release-ports)驱动程序。 这个可以通过往 `/boot/loader.conf` 里边添加下边一行来实现。象这样：

```
bktr_load="YES"
```

另外，您也可以把这个驱动编译进内核， 要是这样的话，就把下边几行加到内核配置里去：

```
device	 bktr
device	iicbus
device	iicbb
device	smbus
```

这些附加的设备驱动程序是必须的， 因为卡的各组成部分是能过一根 I2C 总线相互连接在一起的。 然后建立安装新的内核。

一旦这个支持被加到了您的系统里，您须要重启系统。 在启动过程中，您的电视卡应该显示为 up(启动)，象这样：

```
bktr0: <BrookTree 848A> mem 0xd7000000-0xd7000fff irq 10 at device 10.0 on pci0
iicbb0: <I2C bit-banging driver> on bti2c0
iicbus0: <Philips I2C bus> on iicbb0 master-only
iicbus1: <Philips I2C bus> on iicbb0 master-only
smbus0: <System Management Bus> on bti2c0
bktr0: Pinnacle/Miro TV, Philips SECAM tuner.
```

当然，这些信息可能因您的硬件不同而有所区别。 但是您应该能检查那个调制器是否被正确检测到了， 可能要忽略一些检测到的同[sysctl(8)](http://www.FreeBSD.org/cgi/man.cgi?query=sysctl&sektion=8&manpath=freebsd-release-ports) MIB（管理系统库）和内核配置文件选项一起的参数。 例如，如果您想强制使用 Philips(飞利浦) SECAM 制式的调谐器 ， 您就应把下列行加到内核配置文件里：

```
options OVERRIDE_TUNER=6
```

或者，您直接使用[sysctl(8)](http://www.FreeBSD.org/cgi/man.cgi?query=sysctl&sektion=8&manpath=freebsd-release-ports)：

```
# `sysctl hw.bt848.tuner=6`
```

请参见 [bktr(4)](http://www.FreeBSD.org/cgi/man.cgi?query=bktr&sektion=4&manpath=freebsd-release-ports) 手册和 `/usr/src/sys/conf/NOTES` 文件， 以了解更多详细关于可用选项的资料。

### 8.5.3. 有用的应用程序

要使用您的电视卡，您需要安装下列应用程序之一：

*   [multimedia/fxtv](http://www.freebsd.org/cgi/url.cgi?ports/multimedia/fxtv/pkg-descr) 提供 “窗口电视(TV-in-a-window)” 功能和图像/声音/图像采集功能。

*   [multimedia/xawtv](http://www.freebsd.org/cgi/url.cgi?ports/multimedia/xawtv/pkg-descr) 也是一款电视应用程序，功能同 fxtv 一样。

*   [misc/alevt](http://www.freebsd.org/cgi/url.cgi?ports/misc/alevt/pkg-descr) 解码和显示 Videotext/Teletext。

*   [audio/xmradio](http://www.freebsd.org/cgi/url.cgi?ports/audio/xmradio/pkg-descr)， 一款用于一些电视卡的调频电台调谐器的程序。

*   [audio/wmtune](http://www.freebsd.org/cgi/url.cgi?ports/audio/wmtune/pkg-descr)， 一款用于电台调谐器的便捷的桌面程序。

更多的程序在 FreeBSD Ports Collection(Ports 集)里。

### 8.5.4. 问题解决

如果您的电视卡遇到了什么问题， 您应该首先检查一下您的视频采集芯片和调谐器是不是真正的被[bktr(4)](http://www.FreeBSD.org/cgi/man.cgi?query=bktr&sektion=4&manpath=freebsd-release-ports) 驱动程序支持，并且是不是使用了正确的配置选项。 想得到更多支持和关于您的电视卡的各种问题， 您可以接触和使用[freebsd-multimedia](http://lists.FreeBSD.org/mailman/listinfo/freebsd-multimedia) 邮件列表的压缩包。

## 8.6. 图象扫描仪

撰写人 Marc Fonvieille.

### 8.6.1. 介绍

在 FreeBSD 中， 访问扫描仪的能力， 是通过 SANE (Scanner Access Now Easy) API 提供的。 SANE 也会使用一些 FreeBSD 设备驱动来访问扫描仪硬件。

FreeBSD 支持 SCSI 和 USB 扫描仪。 在做任何配置之前请确保您的扫描仪被 SANE 支持。 SANE 有一个 [支持的设备](http://www.sane-project.org/sane-supported-devices.html) 列表， 可以为您提供有关扫描仪的支持情况和状态的信息。 在 FreeBSD 8.X 之前版本的系统中， [uscanner(4)](http://www.FreeBSD.org/cgi/man.cgi?query=uscanner&sektion=4&manpath=freebsd-release-ports) 手册页也提供了系统支持的 USB 扫描仪列表。

### 8.6.2. 内核配置

上面提到 SCSI 和 USB 接口都是支持的。 取决于您的扫描仪接口， 需要不同的设备驱动程序。

#### 8.6.2.1. USB 接口

默认的 `GENERIC` 内核包含了支持 USB 扫描仪需要的设备驱动。 如果您决定使用一个定制的内核， 确保下面在您的内核配置文件中存在下面这些行：

```
device usb
device uhci
device ohci
device ehci
```

在 FreeBSD 8.X 之前的版本中， 还需要下面这行配置：

```
device uscanner
```

在这些 FreeBSD 版本中， 是通过设备驱动程序 [uscanner(4)](http://www.FreeBSD.org/cgi/man.cgi?query=uscanner&sektion=4&manpath=freebsd-release-ports) 来提供对 USB 扫描仪的支持的。 从 FreeBSD 8.0 开始， 这些支持则直接由 [libusb(3)](http://www.FreeBSD.org/cgi/man.cgi?query=libusb&sektion=3&manpath=freebsd-release-ports) 函数库提供。

使用正确的内核重新引导系统之后， 插入 USB 扫描仪。 系统消息缓冲区 (使用 [dmesg(8)](http://www.FreeBSD.org/cgi/man.cgi?query=dmesg&sektion=8&manpath=freebsd-release-ports) 查看) 中会出现下面的信息， 表示检测到了扫描仪：

```
ugen0.2: <EPSON> at usbus0
```

或者， 对于 FreeBSD 7.X 系统而言：

```
uscanner0: EPSON EPSON Scanner, rev 1.10/3.02, addr 2
```

随 FreeBSD 版本不同， 这些信息表示扫描仪设备位于设备节点 `/dev/ugen0.2` 或 `/dev/uscanner0`。 在这个例子中， 我们使用的是 EPSON Perfection® 1650 USB 扫描仪。

#### 8.6.2.2. SCSI 接口

如果您的扫描仪是 SCSI 接口的， 重要的是要知道您使用哪种 SCSI 控制器。 取决于所使用的 SCSI 芯片， 您需要调整内核配置文件。 `GENERIC` 的内核支持最常用的 SCSI 控制器。 请阅读 `NOTES` 文件并在您的内核配置文件中添加正确的行。 除了 SCSI 适配器驱动之外， 您还需要在内核配置文件中增加下述配置：

```
device scbus
device pass
```

在正确地联编并安装了内核之后， 就应该可以在系统启动时， 从系统消息缓冲中看到这些设备：

```
pass2 at aic0 bus 0 target 2 lun 0
pass2: <AGFA SNAPSCAN 600 1.10> Fixed Scanner SCSI-2 device
pass2: 3.300MB/s transfers
```

如果您的扫描仪没有在系统启动的时候加电， 很可能还需要强制手动检测一下，用 [camcontrol(8)](http://www.FreeBSD.org/cgi/man.cgi?query=camcontrol&sektion=8&manpath=freebsd-release-ports) 命令执行一次 SCSI 总线扫描：

```
# `camcontrol rescan all`
Re-scan of bus 0 was successful
Re-scan of bus 1 was successful
Re-scan of bus 2 was successful
Re-scan of bus 3 was successful
```

然后扫描仪就会出现在 SCSI 设备列表里：

```
# `camcontrol devlist`
<IBM DDRS-34560 S97B>              at scbus0 target 5 lun 0 (pass0,da0)
<IBM DDRS-34560 S97B>              at scbus0 target 6 lun 0 (pass1,da1)
<AGFA SNAPSCAN 600 1.10>           at scbus1 target 2 lun 0 (pass3)
<PHILIPS CDD3610 CD-R/RW 1.00>     at scbus2 target 0 lun 0 (pass2,cd0)
```

有关 SCSI 设备的更多细节， 可查看 [scsi(4)](http://www.FreeBSD.org/cgi/man.cgi?query=scsi&sektion=4&manpath=freebsd-release-ports) 和 [camcontrol(8)](http://www.FreeBSD.org/cgi/man.cgi?query=camcontrol&sektion=8&manpath=freebsd-release-ports) 手册页。

### 8.6.3. SANE 配置

SANE 系统分为两部分： 后端 ([graphics/sane-backends](http://www.freebsd.org/cgi/url.cgi?ports/graphics/sane-backends/pkg-descr)) 和前端 ([graphics/sane-frontends](http://www.freebsd.org/cgi/url.cgi?ports/graphics/sane-frontends/pkg-descr))。 后端部分提供到扫描仪自身的访问。 SANE 的 [支持设备](http://www.sane-project.org/sane-supported-devices.html)列表详细说明了哪一个后端可以支持您的图象扫描仪。 如果您想使用您的设备，就必须为您的扫描仪选定正确的后端。 前端部分提供图形化的扫描界面 (xscanimage)。

要做的第一步就是安装 [graphics/sane-backends](http://www.freebsd.org/cgi/url.cgi?ports/graphics/sane-backends/pkg-descr) port 或者 package。然后，使用 `sane-find-scanner` 命令来检查 SANE 系统做的扫描仪检测：

```
# `sane-find-scanner -q`
found SCSI scanner "AGFA SNAPSCAN 600 1.10" at /dev/pass3
```

输出显示了扫描仪的接口类型和扫描仪连接到系统上的设备节点。 生产厂家和产品型号可能没有显示，不过不重要。

### 注意:

一些 USB 扫描仪需要您加载固件，后端的手册页中有这方面的解释。 您也应该阅读 [sane-find-scanner(1)](http://www.FreeBSD.org/cgi/man.cgi?query=sane-find-scanner&sektion=1&manpath=freebsd-release-ports) 和 [sane(7)](http://www.FreeBSD.org/cgi/man.cgi?query=sane&sektion=7&manpath=freebsd-release-ports) 手册页。

现在我们需要检查扫描仪是否可以被扫描前端识别。 默认情况下， SANE 后端自带一个叫做 [scanimage(1)](http://www.FreeBSD.org/cgi/man.cgi?query=scanimage&sektion=1&manpath=freebsd-release-ports) 的命令行工具。 这个命令允许您列出设备以及从命令行执行图片扫描。 `-L` 选项用来列出扫描仪设备：

```
# `scanimage -L`
device `snapscan:/dev/pass3' is a AGFA SNAPSCAN 600 flatbed scanner
```

或者， 如果使用的是 第 8.6.2.1 节 “USB 接口” 中的 USB 扫描仪：

```
# `scanimage -L`
device 'epson2:libusb:/dev/usb:/dev/ugen0.2' is a Epson GT-8200 flatbed scanner
```

上述输出来自于 FreeBSD 8.X 系统。 `'epson2:libusb:/dev/usb:/dev/ugen0.2'` 给出了扫描仪所使用的后台名字 (`epson2`) 和设备节点 (`/dev/ugen0.2`)。

### 注意:

如果没有输出任何信息， 或提示没有识别到扫描仪， 则说明 [scanimage(1)](http://www.FreeBSD.org/cgi/man.cgi?query=scanimage&sektion=1&manpath=freebsd-release-ports) 无法识别它。 如果发生这种情况， 您就需要修改扫描仪支持后端的配置文件， 并定义所使用的扫描设备。 `/usr/local/etc/sane.d/` 目录中包含了所有的后端配置文件。 这类识别问题经常会在某些 USB 扫描仪上发生。

linkend="scanners-kernel-usb"> 中所使用的 USB 扫描仪， `sane-find-scanner` 会给出下面的信息：

例如， 对于在 第 8.6.2.1 节 “USB 接口”， 在 FreeBSD 8.X 中， 扫描仪已经被很好地识别并能够正常工作了； 而对于更早版本的 FreeBSD 而言 (使用 [uscanner(4)](http://www.FreeBSD.org/cgi/man.cgi?query=uscanner&sektion=4&manpath=freebsd-release-ports) 驱动程序) `sane-find-scanner` 则会给出这样的信息：

```
# `sane-find-scanner -q`
found USB scanner (UNKNOWN vendor and product) at device /dev/uscanner0
```

扫描仪被正确的探测到了，它使用 USB 接口，连接在 `/dev/uscanner0` 设备节点上。 我们现在可以检查看看扫描仪是否被正确的识别：

```
# `scanimage -L`

No scanners were identified. If you were expecting something different,
check that the scanner is plugged in, turned on and detected by the
sane-find-scanner tool (if appropriate). Please read the documentation
which came with this software (README, FAQ, manpages).
```

由于扫描仪没有识别成功， 我们就需要编辑 `/usr/local/etc/sane.d/epson2.conf` 文件。 所用的扫描仪型号是 EPSON Perfection® 1650， 这样我们知道扫描仪应使用 `epson` 后端。确保阅读后端配置文件中的帮助注释。 改动非常简单：注释掉导致您的扫描仪使用错误接口的所有行 (在我们这种情况下，我们将注释掉从 `scsi` 开始的所有行，因为我们的扫描仪使用 USB 接口)，然后在文件的结尾添加指定的接口和所用的设备节点。 这种情况下， 添加下面这行：

```
usb /dev/uscanner0
```

请确保阅读后端配置文件提供的注释以及后端手册页了解更多细节， 并使用正确的语法。我们现在可以检验扫描仪是否被识别到了：

```
# `scanimage -L`
device `epson:/dev/uscanner0' is a Epson GT-8200 flatbed scanner
```

我们的 USB 扫描仪被识别到了。 此时如果商标和型号与扫描仪的实际情况不符， 并不会带来太大的麻烦。 您需要关注的是 ``epson:/dev/uscanner0'` 字段， 这个给了我们正确地后端名称和正确的设备节点。

一旦 `scanimage -L` 命令可以看到扫描仪， 配置就完成了。设备现在准备好等待扫描了。

[scanimage(1)](http://www.FreeBSD.org/cgi/man.cgi?query=scanimage&sektion=1&manpath=freebsd-release-ports) 允许我们从命令行执行图片扫描， 相比之下使用图形用户界面来执行图片扫描会更好。 SANE 提供了一个简单但实用的图形界面： xscanimage ([graphics/sane-frontends](http://www.freebsd.org/cgi/url.cgi?ports/graphics/sane-frontends/pkg-descr))。

Xsane ([graphics/xsane](http://www.freebsd.org/cgi/url.cgi?ports/graphics/xsane/pkg-descr))是另一个流行的图形扫描前端。 这个前端提供了一些高级特性， 比如多样的扫描模式(photocopy，fax，等。)， 色彩校正，批量扫描，等等。这两个程序都可以作为 GIMP 的插件使用。

### 8.6.4. 授权其他用户访问扫描仪

前面所有的操作都是用 `root` 权限来完成的。 然而您可能需要让其他的用户也可以访问扫描仪。 用户需要有扫描仪所用的设备节点的读和写权限。 比如，我们的 USB 扫描仪使用设备节点 `/dev/ugen0.2` 实际上只是到实际设备节点 `/dev/usb/0.2.0` 的符号连接 (可以通过查看 `/dev` 目录的内容来确认这一点)。 设备节点本身和这个符号连接分别属于 `wheel` 和 `operator` 组。 将用户 `*`joe`*` 添加到 这些组中， 就可以允许他使用扫描仪了， 不过， 出于显而易见的安全方面的原因， 在将用户加到特定的用户组， 特别是 `wheel` 组时， 无疑需三思而后行。 更好的解决方法是创建一个专门用于访问 USB 设备的组， 并让这个组的成员能够访问 USB 设备。

这里作为示例， 我们将会使用名为 `*`usb`*` 的组。 第一步是借助 [pw(8)](http://www.FreeBSD.org/cgi/man.cgi?query=pw&sektion=8&manpath=freebsd-release-ports) 命令来创建它：

```
# `pw groupadd usb`
```

接下来， 令 `/dev/ugen0.2` 符号连接和 `/dev/usb/0.2.0` 设备节点能够以 `usb` 组的身份来访问， 具体而言是配置正确的写权限 (`0660` 或 `0664`)， 因为默认情况下只有属主 (`root`) 才能写这些设备。 这些配置是通过在 `/etc/devfs.rules` 文件中添加如下的设置来实现的：

```
[system=5]
add path ugen0.2 mode 0660 group usb
add path usb/0.2.0 mode 0666 group usb
```

FreeBSD 7.X 用户需要将上面的配置改为使用与之对应的 `/dev/uscanner0`：

```
[system=5]
add path uscanner0 mode 660 group usb
```

随后您还需要在 `/etc/rc.conf` 中添加下面的内容并重新启动：

```
devfs_system_ruleset="system"
```

关于这些配置的进一步细节请参考联机手册 [devfs(8)](http://www.FreeBSD.org/cgi/man.cgi?query=devfs&sektion=8&manpath=freebsd-release-ports)。

现在， 只需将用户添加到 `*`usb`*` 组， 就可以使用扫描仪了：

```
# `pw groupmod usb -m joe`
```

更多详情， 请参见联机手册 [pw(8)](http://www.FreeBSD.org/cgi/man.cgi?query=pw&sektion=8&manpath=freebsd-release-ports)。

## 第 9 章 配置 FreeBSD 的内核

Updated and restructured by Jim Mock.Originally contributed by Jake Hamby.

## 9.1. 概述

内核是 FreeBSD 操作系统的核心。 它负责管理内存、 执行安全控制、 网络、 磁盘访问等等。 尽管 FreeBSD 可以动态修改的现在已经越来越多， 但有时您还是需要重新配置和编译您的内核。

读完这章，您将了解：

*   为什么需要建立定制的内核。

*   如何编写内核配置文件，或修改已存在的配置文件。

*   如何使用内核配置文件创建和联编新的内核。

*   如何安装新内核。

*   如何处理出现的问题。

这一章给出的命令应该以 `root` 身份执行， 否则可能会不成功。

## 9.2. 为什么需要建立定制的内核?

过去， FreeBSD 采用的是被人们称作 “单片式” 的内核。 这种内核本身是一个大的程序， 它支持的设备不能够动态地加以改变， 而当希望改变内核的行为时， 就必须编译一个新的内核， 并重新启动计算机才可以使用它。

如今， FreeBSD 正在迅速地迁移到一种新的模型， 其特点是将大量内核功能放进可以动态加载和卸载的内核模块来提供。 这使得内核能够适应硬件的调整 (例如笔记本计算机中的 PCMCIA 卡)， 以及为内核引入新的功能， 而无需在编译内核时就将其添加进去。 这种做法称为模块化内核。

尽管如此， 仍然有一些功能需要静态地联编进内核。 有时， 这是由于这些功能与内核的结合非常紧密而无法实现动态加载， 还有一些情况是暂时没有人将这些功能改写为可动态加载的模块。

联编定制的内核是成为高级 BSD 用户所必须经历的一关。 尽管这一过程需要花费一些时间， 但它能够为您的 FreeBSD 系统带来一些好处。 与必须支持大量硬件的 `GENERIC` 内核不同， 定制的内核可以只包含对于 *您* PC 硬件的支持。 这样做有很多好处， 例如：

*   更快地启动。 因为内核只需要检测您系统上的硬件， 启动时所花费的时间将大大缩短。

*   使用更少的内存。 由于可以删去不需要的功能和设备驱动， 通常定制的内核会比 `GENERIC` 使用的内存更少。 节省内核使用的内存之所以重要是因为内核必须常驻于物理内存中， 从而使应用程序能够用到更多的内存。 正因为这样， 对 RAM 较小的系统来说定制内核就更为重要了。

*   支持更多的硬件。 定制的内核允许您增加类似声卡这样的 `GENERIC` 内核没有提供内建支持的硬件。

## 9.3. 发现系统硬件

作者 Tom Rhodes.

在尝试配置内核以前，比较明智的做法是先获得一份机器硬件的清单。 当 FreeBSD 并不是主操作系统时，通过查看当前操作系统的配置可以很容易的 创建一份机器硬件的配置清单。举例来说， Microsoft® 的 设备管理器 里通常含有关于已安装硬件的重要信息。 设备管理器 位于控制面板。

### 注意:

某些版本的 Microsoft® Windows® 有一个 系统 图标会指明 设备管理器 的位置。

如果机器上并不存在其他的操作系统， 系统管理员只能手动寻找这些信息了。其中的一个方法是使用 [dmesg(8)](http://www.FreeBSD.org/cgi/man.cgi?query=dmesg&sektion=8&manpath=freebsd-release-ports) 工具以及 [man(1)](http://www.FreeBSD.org/cgi/man.cgi?query=man&sektion=1&manpath=freebsd-release-ports) 命令。FreeBSD 上大多数的驱动程序都有一份手册页（manual page）列出了所支持的硬件， 在系统启动的时候，被发现的硬件也会被列出。举例来说， 下面的这几行表示 `psm` 驱动找到了一个鼠标：

```
psm0: <PS/2 Mouse> irq 12 on atkbdc0
psm0: [GIANT-LOCKED]
psm0: [ITHREAD]
psm0: model Generic PS/2 mouse, device ID 0
```

这个驱动需要被包含在客户制定的内核配置文件里， 或着使用 [loader.conf(5)](http://www.FreeBSD.org/cgi/man.cgi?query=loader.conf&sektion=5&manpath=freebsd-release-ports) 加载。

有时，`dmesg` 里只会显示来自系统消息的数据， 而不是系统启动时的检测信息。在这样的情况下，你可以查看文件 `/var/run/dmesg.boot`。

另一个查找硬件信息的方法是使用 [pciconf(8)](http://www.FreeBSD.org/cgi/man.cgi?query=pciconf&sektion=8&manpath=freebsd-release-ports) 工具， 它能提供更详细的输出，比如：

```
ath0@pci0:3:0:0:        class=0x020000 card=0x058a1014 chip=0x1014168c rev=0x01 hdr=0x00
    vendor     = 'Atheros Communications Inc.'
    device     = 'AR5212 Atheros AR5212 802.11abg wireless'
    class      = network
    subclass   = ethernet
```

这个片断取自于 `pciconf -lv` 命令的输出，显示 `ath` 驱动找到了一个无线以太网设备。输入命令 `man ath` 就能查阅有关 [ath(4)](http://www.FreeBSD.org/cgi/man.cgi?query=ath&sektion=4&manpath=freebsd-release-ports) 的手册页（manual page）了。

还可以传给 [man(1)](http://www.FreeBSD.org/cgi/man.cgi?query=man&sektion=1&manpath=freebsd-release-ports) 命令 `-k` 选项， 同样能获得有用的信息。例如：

```
# man -k *`Atheros`*
```

能得到一份包含特定词语的手册页（manual page）:

```
ath(4)                   - Atheros IEEE 802.11 wireless network driver
ath_hal(4)               - Atheros Hardware Access Layer (HAL)
```

手头备有一份硬件的配置清单， 那么编译制定内核的过程就显得不那么困难了。

## 9.4. 内核驱动，子系统和模块

在编译一个制定的内核之前请三思一下这么做的理由， 如果仅是需要某个特定的硬件支持的话， 那么很可能已经存在一个现成的模块了。

内核模块存放在目录 `/boot/kernel` 中，并能由 [kldload(8)](http://www.FreeBSD.org/cgi/man.cgi?query=kldload&sektion=8&manpath=freebsd-release-ports) 命令加载入正在运行的内核。 基本上所有的内核驱动都有特定的模块和手册页。比如， 下面提到的 `ath` 无线以太网驱动。 在这个设备的联机手册中有以下信息：

```
Alternatively, to load the driver as a module at boot time, place the
following line in loader.conf(5):

    if_ath_load="YES"
```

遵照示例，在 `/boot/loader.conf` 中加入 `if_ath_load="YES"` 则能在机器启动的时候动态加载这个模块。

某些情况下，则没有相关的模块。通常是一些子系统和非常重要的驱动， 比如，快速文件系统 (FFS) 就是一个内核必需的选项。 同样的还有网络支持 (INET)。不幸的是， 分辨一个驱动是否必需的唯一方法就是检查测试以下那个模块本身。

### 警告:

去除某个驱动的支持或某个选项会非常容易得到一个坏掉的内核。 举例来说，如果把 [ata(4)](http://www.FreeBSD.org/cgi/man.cgi?query=ata&sektion=4&manpath=freebsd-release-ports) 驱动从内核配置文件中去掉， 那么一个使用 ATA 磁盘设备的系统可能就变得无法引导，除非有在 `loader.conf` 中加载。当你无法确定的时候， 请检查一下那个模块并把它留在你的内核配置中。

## 9.5. 建立并安装一个定制的内核

首先对内核构建目录做一个快速的浏览。 这里所提到的所有目录都在 `/usr/src/sys` 目录中； 也可以通过 `/sys` 来访问它。 这里的众多子目录包含了内核的不同部分， 但对我们所要完成的任务最重要的目录是 `arch/conf`， 您将在这里编辑定制的内核配置； 以及 `compile`， 编译过程中的文件将放置在这里。 *`arch`* 表示 `i386`、 `amd64`、 `ia64`、 `powerpc`、 `sparc64`， 或 `pc98` (在日本比较流行的另一种 PC 硬件开发分支)。 在特定硬件架构目录中的文件只和特定的硬件有关； 而其余代码则是与机器无关的， 则所有已经或将要移植并运行 FreeBSD 的平台上都共享这些代码。 文件目录是按照逻辑组织的， 所支持的硬件设备、 文件系统， 以及可选的组件通常都在它们自己的目录中。

这一章提供的例子假定您使用 i386 架构的计算机。 如果您的情况不是这样， 只需对目录名作相应的调整即可。

### 注意:

如果您的系统中 *没有* `/usr/src/sys` 这样一个目录， 则说明没有安装内核源代码。 安装它最简单的方法是通过以 root 身份运行 `sysinstall`， 选择 Configure， 然后是 Distributions、 src， 选中其中的 base 和 sys。 如果您不喜欢 sysinstall 并且有一张 “官方的” FreeBSD CDROM， 也可以使用下列命令， 从命令行来安装源代码：

```
# `mount /cdrom`
# `mkdir -p /usr/src/sys`
# `ln -s /usr/src/sys /sys`
# `cat /cdrom/src/ssys.[a-d]* | tar -xzvf -`
# `cat /cdrom/src/sbase.[a-d]* | tar -xzvf -`
```

接下来， 进入 `arch/conf` 目录下面， 复制 `GENERIC` 配置文件， 并给这个文件起一个容易辨认的名称， 它就是您的内核名称。例如：

```
# `cd /usr/src/sys/i386/conf`
# `cp GENERIC MYKERNEL`
```

通常，这个名称是大写的，如果您正维护着多台不同硬件的 FreeBSD 机器， 以您机器的域名来命名是非常好的主意。我们把它命名为 `MYKERNEL`就是这个原因。

### 提示:

将您的内核配置文件直接保存在 `/usr/src` 可能不是一个好主意。 如果您遇到问题， 删掉 `/usr/src` 并重新开始很可能是一个诱人的选择。 一旦开始做这件事， 您可能几秒钟之后才会意识到您同时会删除定制的内核配置文件。 另外， 也不要直接编辑 `GENERIC`， 因为下次您 更新代码 时它会被覆盖， 而您的修改也就随之丢失了。

您也可以考虑把内核配置文件放到别的地方， 然后再到 `i386` 目录中创建一个指向它的符号链接。

例如：

```
# `cd /usr/src/sys/i386/conf`
# `mkdir /root/kernels`
# `cp GENERIC /root/kernels/MYKERNEL`
# `ln -s /root/kernels/MYKERNEL`
```

### 注意:

必须以 `root` 身份执行这些和接下来命令， 否则就会得到 permission denied 的错误提示。

现在就可以用您喜欢的文本编辑器来编辑 `MYKERNEL` 了。 如果您刚刚开始使用 FreeBSD， 唯一可用的编辑器很可能是 vi， 它的使用比较复杂， 限于篇幅， 这里不予介绍， 您可以在 参考书目 一章中找到很多相关书籍。 不过， FreeBSD 也提供了一个更好用的编辑器， 它叫做 ee， 对于新手来说， 这很可能是一个不错的选择。 您可以修改配置文件中的注释以反映您的配置， 或其他与 `GENERIC` 不同的地方。

如果您在 SunOS™或者其他 BSD 系统下定制过内核，那这个文件中的绝大部分将对您非常熟悉。 如果您使用的是诸如 DOS 这样的系统，那`GENERIC`配置文件看起来就非常困难， 所以在下面的 配置文件章节将慢慢地、仔细地进行介绍。

### 注意:

如果您和 FreeBSD project 进行了 代码同步， 则一定要在进行任何更新之前查看 `/usr/src/UPDATING`。 这个文件中描述了更新过的代码中出现的重大问题或需要注意的地方。 `/usr/src/UPDATING` 总是和您的 FreeBSD 源代码对应， 因此能够提供比手册更具时效性的新内容。

现在应该编译内核的源代码了。

过程 9.1. 联编内核

1.  进入 `/usr/src` 目录：

    ```
    # `cd /usr/src`
    ```

2.  编译内核：

    ```
    # `make buildkernel KERNCONF=MYKERNEL`
    ```

3.  安装新内核：

    ```
    # `make installkernel KERNCONF=MYKERNEL`
    ```

### 注意:

使用这种方法联编内核时， 需要安装完整的 FreeBSD 源代码。

### 提示:

默认情况下， 在联编您所定制的内核时， *全部* 内核模块也会同时参与构建。 如果您希望更快地升级内核， 或者只希望联编您所需要的模块， 则应在联编之前编辑 `/etc/make.conf`：

```
MODULES_OVERRIDE = linux acpi sound/sound sound/driver/ds1 ntfs
```

这个变量的内容是所希望构建的模块列表。

```
WITHOUT_MODULES = linux acpi sound ntfs
```

这个变量的内容是将不在联编过程中编译的顶级模块列表。 如果希望了解更多与构建内核有关的变量， 请参见 [make.conf(5)](http://www.FreeBSD.org/cgi/man.cgi?query=make.conf&sektion=5&manpath=freebsd-release-ports) 联机手册。

新内核将会被复制到 `/boot/kernel` 目录中成为 `/boot/kernel/kernel` 而旧的则被移到 `/boot/kernel.old/kernel`。 现在关闭系统， 然后用新的内核启动计算机。 如果出现问题， 后面的一些 故障排除方法 将帮您摆脱困境。 如果您的内核 无法启动， 请参考那一节。

### 注意:

其他与启动过程相关的文件， 如 [loader(8)](http://www.FreeBSD.org/cgi/man.cgi?query=loader&sektion=8&manpath=freebsd-release-ports) 及其配置， 则放在 `/boot`。 第三方或定制的模块也可以放在 `/boot/kernel`， 不过应该注意保持模块和内核的同步时很重要的， 否则会导致不稳定和错误。

## 9.6. 配置文件

Updated by Joel Dahl.

配置文件的格式是非常简单的。 每一行都包括一个关键词， 以及一个或多个参数。 实际上， 绝大多数行都只包括一个参数。 在 `#` 之后的内容会被认为是注释而忽略掉。 接下来几节, 将以 `GENERIC` 中的顺序介绍所有关键字。 如果需要与平台有关的选项和设备的详细列表， 请参考与 `GENERIC` 文件在同一个目录中的那个 `NOTES`， 而平台无关的选项， 则可以在 `/usr/src/sys/conf/NOTES` 找到。

配置文件中还可以使用 `include` 语句。 这个语句能够在内核配置文件中直接引用其他配置文件的内容， 使得您能够使用较小的、 仅包含相对于现存配置的变动而减少维护所需的工作。 例如， 如果您只需对 `GENERIC` 内核进行少量定制， 在其中添加几个驱动程序和附加选项， 则只要维护相对于 GENERIC 的变化就可以了：

```
include GENERIC
ident MYKERNEL

options         IPFIREWALL
options         DUMMYNET
options         IPFIREWALL_DEFAULT_TO_ACCEPT
options         IPDIVERT

```

许多系统管理员会发现， 这种方法与先前从头开始写配置文件的方法相比， 可以带开相当多的好处： 本地采用的配置文件只表达与 `GENERIC` 内核的差异， 这样， 在升级的时候往往就不需要做任何改动， 而新加入 `GENERIC` 的功能就会自动加入到本地的内核， 除非使用 `nooptions` 或 `nodevice` 语句将其排除。 这一章余下的部分将着重介绍典型的配置文件， 以及内核选项和设备的作用。

### 注意:

如果您需要一份包含所有选项的文件， 例如用于测试目的， 则应以 `root` 身份执行下列命令：

```
# `cd /usr/src/sys/i386/conf && make LINT`
```

下面是一个 `GENERIC` 内核配置文件的例子， 它包括了一些需要解释的注释。 这个例子应该和您复制的 `/usr/src/sys/i386/conf/GENERIC` 非常接近。

```
machine		i386
```

这是机器的架构， 它只能是 `amd64`, `i386`, `ia64`, `pc98`, `powerpc`, 或 `sparc64` 中的一种。

```
cpu          I486_CPU
cpu          I586_CPU
cpu          I686_CPU
```

上面的选项指定了您系统中所使用的 CPU 类型。 您可以使用多个 CPU 类型 (例如， 您不确定是应该指定 `I586_CPU` 或 `I686_CPU`)。 然而对于定制的内核， 最好能够只指定您使用的那种 CPU。 如果您对于自己使用的 CPU 类型没有把握， 可以通过查看 `/var/run/dmesg.boot` 中的启动信息来了解。

```
ident          GENERIC
```

这是内核的名字。 您应该取一个自己的名字， 例如取名叫 `MYKERNEL`， 如果您一直在按照前面的说明做的话。 您放在 `ident` 后面的字符串在启动内核时会显示出来， 因此如果希望能够容易区分常用的内核和刚刚定制的内核， 就应该采取不同的名字 (例如， 您想定制一个试验性的内核)。

```
#To statically compile in device wiring instead of /boot/device.hints
#hints          "GENERIC.hints"         # Default places to look for devices.
```

[device.hints(5)](http://www.FreeBSD.org/cgi/man.cgi?query=device.hints&sektion=5&manpath=freebsd-release-ports) 可以用来配置设备驱动选项。 在启动的时候 [loader(8)](http://www.FreeBSD.org/cgi/man.cgi?query=loader&sektion=8&manpath=freebsd-release-ports) 将会检查缺省位置 `/boot/devicehints`。 使用 `hints` 选项您就可以把这些 hints 静态编译进内核。 这样就没有必要在 `/boot`下创建`devicehints`。

```
makeoptions     DEBUG=-g          # Build kernel with gdb(1) debug symbols
```

一般的 FreeBSD 联编过程， 在所联编的内核指定了 `-g` 选项时， 由于此选项将传递给 [gcc(1)](http://www.FreeBSD.org/cgi/man.cgi?query=gcc&sektion=1&manpath=freebsd-release-ports) 表示加入调试信息， 因此会将调试符号也包含进来。

```
options          SCHED_ULE         # ULE scheduler
```

这是 FreeBSD 上使用的默认系统调度器。 请保留此选项。

```
options          PREEMPTION         # Enable kernel thread preemption
```

允许内核线程根据优先级的抢占调度。 这有助于改善交互性， 并可以让中断线程更早地执行， 而无须等待。

```
options          INET              # InterNETworking
```

网络支持，即使您不打算连网，也请保留它，大部分的程序至少需要回环网络（就是和本机进行网络连接），所以强烈要求保留它。

```
options          INET6             # IPv6 communications protocols
```

这将打开 IPv6 连接协议。

```
options          FFS               # Berkeley Fast Filesystem
```

这是最基本的硬盘文件系统，如果打算从本地硬盘启动，请保留它。

```
options          SOFTUPDATES       # Enable FFS Soft Updates support
```

这个选项会启用内核中的 Soft Updates 支持， 它会显著地提高磁盘的写入速度。 尽管这项功能是由内核直接提供的， 但仍然需要在每个磁盘上启用它。 请检查 [mount(8)](http://www.FreeBSD.org/cgi/man.cgi?query=mount&sektion=8&manpath=freebsd-release-ports) 的输出， 以了解您系统中的磁盘上是否已经启用了 Soft Updates。 如果没有看到 `soft-updates` 选项， 则需要使用 [tunefs(8)](http://www.FreeBSD.org/cgi/man.cgi?query=tunefs&sektion=8&manpath=freebsd-release-ports) (对于暨存系统) 或 [newfs(8)](http://www.FreeBSD.org/cgi/man.cgi?query=newfs&sektion=8&manpath=freebsd-release-ports) (对于新系统) 命令来激活它。

```
options          UFS_ACL           # Support for access control lists
```

这个选项将启用内核中的访问控制表的支持。 这依赖于扩展属性以及 UFS2， 以及在 第 15.11 节 “文件系统访问控制表” 中所介绍的那些特性。 ACL 默认是启用的， 并且如果已经在文件系统上使用了这一特性， 就不应再关掉它， 因为这会去掉文件的访问控制表， 并以不可预期的方式改变受保护的文件的访问方式。

```
options          UFS_DIRHASH       # Improve performance on big directories
```

通过使用额外的内存，这个选项可以加速在大目录上的磁盘操作。 您应该在大型服务器和频繁使用的工作站上打开这个选项，而在磁盘操作不是很重要的 小型系统上关闭它，比如防火墙。

```
options          MD_ROOT           # MD is a potential root device
```

这个选项将打开以基于内存的虚拟磁盘作为根设备的支持。

```
options          NFSCLIENT         # Network Filesystem Client
options          NFSSERVER         # Network Filesystem Server
options          NFS_ROOT          # NFS usable as /, requires NFSCLIENT
```

网络文件系统。 如果您不打算通过 TCP/IP 挂接 UNIX® 文件服务器的分区， 就可以注释掉它。

```
options          MSDOSFS           # MSDOS Filesystem
```

MS-DOS® 文件系统。 只要您不打算在启动时挂接由 DOS 格式化的硬盘分区， 就可以把它注释掉。 如前面所介绍的那样， 在您第一次挂接 DOS 分区时， 内核会自动加载需要的模块。 此外， [emulators/mtools](http://www.freebsd.org/cgi/url.cgi?ports/emulators/mtools/pkg-descr) 软件提供了一个很方便的功能， 通过它您可以直接访问 DOS 软盘而无需挂接或卸下它们 (而且也完全不需要 `MSDOSFS`)。

```
options          CD9660            # ISO 9660 Filesystem
```

用于 CDROM 的 ISO 9660 文件系统。 如果没有 CDROM 驱动器或很少挂接光盘数据 (因为在首次使用数据 CD 时会自动加载)， 就可以把它注释掉。 音乐 CD 并不需要这个选项。

```
options          PROCFS            # Process filesystem (requires PSEUDOFS)
```

进程文件系统。 这是一个挂接在 `/proc` 的一个 “假扮的” 文件系统， 其作用是允许类似 [ps(1)](http://www.FreeBSD.org/cgi/man.cgi?query=ps&sektion=1&manpath=freebsd-release-ports) 这样的程序给出正在运行的进程的进一步信息。 多数情况下， 并不需要使用 `PROCFS`， 因为绝大多数调试和监控工具， 已经进行了一系列修改， 使之不再依赖 `PROCFS`： 默认安装的系统中并不会挂接这一文件系统。

```
options          PSEUDOFS          # Pseudo-filesystem framework
```

如果希望使用 `PROCFS`， 就必须加入 `PSEUDOFS` 的支持。

```
options          GEOM_GPT          # GUID Partition Tables.
```

这个选项提供了在磁盘上使用大量的分区的能力。

```
options          COMPAT_43         # Compatible with BSD 4.3 [KEEP THIS!]
```

使系统兼容 4.3BSD。不要去掉这一行，不然有些程序将无法正常运行。

```
options          COMPAT_FREEBSD4   # Compatible with FreeBSD4
```

如果希望支持在旧版 FreeBSD 上编译的使用旧式接口的应用程序， 就需要加入这一选项。 一般来说， 推荐在所有的 i386™ 系统上启用这个选项， 因为难免可能会用到一些旧的应用； 到 5.X 才开始支持的平台， 如 ia64 和 SPARC64®， 则不需要这个选项。

```
options          COMPAT_FREEBSD5   # Compatible with FreeBSD5
```

如果希望支持在 FreeBSD 5.X 版本上编译， 且使用 FreeBSD 5.X 系统调用接口的应用程序， 则应加上这个选项。

```
options          COMPAT_FREEBSD6   # Compatible with FreeBSD6
```

如果希望支持在 FreeBSD 6.X 版本上编译， 且使用 FreeBSD 6.X 系统调用接口的应用程序， 则应加上这个选项。

```
options          COMPAT_FREEBSD7   # Compatible with FreeBSD7
```

如果希望支持在 FreeBSD 8 以上版本的操作系统中运行在 FreeBSD 7.X 版本上编译， 且使用 FreeBSD 7.X 系统调用接口的应用程序， 则应加上这个选项。

```
options          SCSI_DELAY=5000  # Delay (in ms) before probing SCSI
```

这将让内核在探测每个 SCSI 设备之前等待 5 秒。 如果您只有 IDE 硬盘驱动器， 就可以不管它， 反之您可能会希望尝试降低这个数值以加速启动过程。 当然， 如果您这么做之后 FreeBSD 在识别您的 SCSI 设备时遇到问题， 则您还需要再把它改回去。

```
options          KTRACE            # ktrace(1) support
```

这个选项打开内核进程跟踪，在调试时很有用。

```
options          SYSVSHM           # SYSV-style shared memory
```

提供 System V 共享内存(SHM)的支持，最常用到 SHM 的应该是 X Window 的 XSHM 延伸， 不少绘图相关程序会自动使用 SHM 来提供额外的速度。如果您要使用 X Window，您最好加入这个选项。

```
options          SYSVMSG           # SYSV-style message queues
```

支持 System V 消息。 这只会在内核中增加数百字节的空间占用。

```
options          SYSVSEM           # SYSV-style semaphores
```

支持 System V 信号量， 不常用到， 但只在 kernel 中占用几百个字节的空间。

### 注意:

[ipcs(1)](http://www.FreeBSD.org/cgi/man.cgi?query=ipcs&sektion=1&manpath=freebsd-release-ports) 命令的 `-p` 选项可以显示出任何用到这些 System V 机制的进程。

```
options 	     _KPOSIX_PRIORITY_SCHEDULING # POSIX P1003_1B real-time extensions
```

在 1993 年 POSIX® 添加的实时扩展。 在 Ports Collection 中某些应用程序会用到这些 （比如 StarOffice™）。

```
options          KBD_INSTALL_CDEV  # install a CDEV entry in /dev
```

这个选项是在 `/dev`下建立键盘设备节点必需的。

```
options          ADAPTIVE_GIANT    # Giant mutex is adaptive.
```

内核全局锁 (Giant) 是一种互斥机制 (休眠互斥体) 的名字， 它用于保护许多内核资源。 现在， 这已经成为了一种无法接受的性能瓶颈， 它已经被越来越多地使用保护单个资源的锁代替。 `ADAPTIVE_GIANT` 选项将使得内核全局锁作为一种自适应自旋锁。 这意味着， 当有线程希望锁住内核全局锁互斥体， 但互斥体已经被另一个 CPU 上的线程锁住的时候， 它将继续运行， 直到那个线程释放锁为止。 一般情况下， 另一个线程将进入休眠状态并等待下一次调度。 如果您不确定是否应该这样做的话， 一般应该打开它。

### 注意:

请注意在 FreeBSD 8.0-RELEASE 及以后的版本，所有的互斥体默认都是自适应的， 除非在编译时使用 `NO_ADAPTIVE_MUTEXES` 选项， 明确的指定为非自适应。因此，内核全局锁（Giant）目前默认也是自适应的, 而且 `ADAPTIVE_GIANT` 选项已经从内核配置文件中移出。

```
device          apic               # I/O APIC
```

apic 设备将启用使用 I/O APIC 作为中断发送设备的能力。 apic 设备可以被 UP 和 SMP 内核使用， 但 SMP 内核必须使用它。 要支持多处理器， 还需要加上 `options SMP`。

### 注意:

只有在 i386 和 amd64 平台上才存在 apic 设备， 在其他硬件平台上不应使用它。

```
device          eisa
```

如果您的主机板上有 EISA 总线，加入这个设置。使用这个选项可以自动扫描并设置所有连接在 EISA 总线上的设备。

```
device          pci
```

如果您的主板有 PCI 总线，就加入这个选项。使用这个选项可以自动扫描 PCI 卡，并在 PCI 到 ISA 之间建立通路。

```
# Floppy drives
device          fdc
```

这是软驱控制器。

```
# ATA and ATAPI devices
device          ata
```

这个驱动器支持所有 ATA 和 ATAPI 设备。您只要在内核中加入`device ata`选项， 就可以让内核支持现代计算机上的所有 PCI ATA/ATAPI 设备。

```
device          atadisk                 # ATA disk drives
```

这个是使用 ATAPI 硬盘驱动器时必须加入的选项。

```
device          ataraid                 # ATA RAID drives
```

这个选项需要 `device ata`， 它用于 ATA RAID 驱动。

```
 device          atapicd                 # ATAPI CDROM drives
```

这个是 ATAPI CDROM 驱动器所必须的。

```
device          atapifd                 # ATAPI floppy drives
```

这个是 ATAPI 软盘驱动器所必须的。

```
device          atapist                 # ATAPI tape drives
```

这个是 ATAPI 磁带机驱动器所必须的.

```
options         ATA_STATIC_ID           # Static device numbering
```

这指定对控制器使用其静态的编号； 如果没有这个选项， 则会动态地分配设备的编号。

```
# SCSI Controllers
device          ahb        # EISA AHA1742 family
device          ahc        # AHA2940 and onboard AIC7xxx devices
options         AHC_REG_PRETTY_PRINT    # Print register bitfields in debug
                                        # output.  Adds ~128k to driver.
device          ahd        # AHA39320/29320 and onboard AIC79xx devices
options         AHD_REG_PRETTY_PRINT    # Print register bitfields in debug
                                        # output.  Adds ~215k to driver.
device          amd        # AMD 53C974 (Teckram DC-390(T))
device          isp        # Qlogic family
#device         ispfw      # Firmware for QLogic HBAs- normally a module
device          mpt        # LSI-Logic MPT-Fusion
#device         ncr        # NCR/Symbios Logic
device          sym        # NCR/Symbios Logic (newer chipsets + those of `ncr')
device          trm        # Tekram DC395U/UW/F DC315U adapters

device          adv        # Advansys SCSI adapters
device          adw        # Advansys wide SCSI adapters
device          aha        # Adaptec 154x SCSI adapters
device          aic        # Adaptec 15[012]x SCSI adapters, AIC-6[23]60.
device          bt         # Buslogic/Mylex MultiMaster SCSI adapters

device          ncv        # NCR 53C500
device          nsp        # Workbit Ninja SCSI-3
device          stg        # TMC 18C30/18C50
```

SCSI 控制器。可以注释掉您系统中没有的设备。 如果您只有 IDE 设备，您可以把这些一起删掉。 `*_REG_PRETTY_PRINT` 这样的配置， 则是对应驱动程序的调试选项。

```
# SCSI peripherals
device          scbus      # SCSI bus (required for SCSI)
device          ch         # SCSI media changers
device          da         # Direct Access (disks)
device          sa         # Sequential Access (tape etc)
device          cd         # CD
device          pass       # Passthrough device (direct SCSI access)
device          ses        # SCSI Environmental Services (and SAF-TE)
```

SSCSI 外围设备。也可以像上面一样操作。

### 注意:

目前系统提供的 USB [umass(4)](http://www.FreeBSD.org/cgi/man.cgi?query=umass&sektion=4&manpath=freebsd-release-ports) 以及少量其它驱动使用了 SCSI 子系统， 尽管它们并不是真的 SCSI 设备。 因此， 如果在内核配置使用了这类驱动程序， 请务必不要删除 SCSI 支持。

```
# RAID controllers interfaced to the SCSI subsystem
device          amr        # AMI MegaRAID
device          arcmsr     # Areca SATA II RAID
device          asr        # DPT SmartRAID V, VI and Adaptec SCSI RAID
device          ciss       # Compaq Smart RAID 5*
device          dpt        # DPT Smartcache III, IV - See NOTES for options
device          hptmv      # Highpoint RocketRAID 182x
device          rr232x     # Highpoint RocketRAID 232x
device          iir        # Intel Integrated RAID
device          ips        # IBM (Adaptec) ServeRAID
device          mly        # Mylex AcceleRAID/eXtremeRAID
device          twa        # 3ware 9000 series PATA/SATA RAID

# RAID controllers
device          aac        # Adaptec FSA RAID
device          aacp       # SCSI passthrough for aac (requires CAM)
device          ida        # Compaq Smart RAID
device          mfi        # LSI MegaRAID SAS
device          mlx        # Mylex DAC960 family
device          pst        # Promise Supertrak SX6000
device          twe        # 3ware ATA RAID
```

支持 RAID 控制器。如果您没有这些，可以把它们注释掉或是删掉。

```
# atkbdc0 controls both the keyboard and the PS/2 mouse
device          atkbdc     # AT keyboard controller
```

键盘控制器（`atkbdc`）提供 AT 键盘输入以及 PS/2 指针设备的 I/O 服务。 键盘驱动程序（`atkbd`）与 PS/2 鼠标驱动程序（`psm`）需要这个控制器，所以不要删除它。

```
device          atkbd      # AT keyboard
```

`atkbd`驱动程序，与`atkbdc`控制器一起使用， 提供连接到 AT 键盘控制器的 AT 84 键盘与 AT 加强型键盘的访问服务。

```
device          psm        # PS/2 mouse
```

如果您的鼠标连接到 PS/2 鼠标端口，就使用这个设备驱动程序。

```
device          kbdmux        # keyboard multiplexer
```

针对键盘多路选择器的基本支持。 如果您不打算使用多个键盘， 则可以放心地删除这一行。

```
device          vga        # VGA video card driver
```

显卡驱动。

```
device          splash     # Splash screen and screen saver support
```

启动时的 splash 画面！ 屏幕保护程序也需要这一选项。

```
# syscons is the default console driver, resembling an SCO console
device          sc
```

`sc` 是默认的控制台驱动程序， 类似 SCO 控制台。 由于绝大部分全屏幕程序都通过类似 `termcap` 这样的终端数据库函数库赖访问控制台， 因此无论您使用这个或与 `VT220` 兼容的 `vt` 都没有什么关系。 如果您在运行这种控制台时使用全屏幕程序时发生问题， 请在登录之后将 `TERM` 变量设置为 `scoansi`。

```
# Enable this for the pcvt (VT220 compatible) console driver
#device          vt
#options         XSERVER          # support for X server on a vt console
#options         FAT_CURSOR       # start with block cursor
```

这是一个兼容 VT220 的控制台驱动， 它同时能够向下兼容 VT100/102。 在同 `sc` 硬件不兼容的一些笔记本上它能够运行的很好。 当然， 登录系统时请把 `TERM` 变量设置为 `vt100` 或 `vt220`。 此驱动在连接网络上大量不同的机器时也被证明非常有用， 因为此时 `termcap` 或 `terminfo` 通常没有可用的 `sc` 设备 ── 而 `vt100` 则几乎每种平台都支持。

```
device          agp
```

如果您的机器使用 AGP 卡， 请把上面一行加入配置。 这将启用 AGP， 以及某些卡上的 AGP GART 支持。

```
# 电源管理支持 (参见 NOTES 了解更多选项)
#device          apm
```

高级电源管理支持。对笔记本有用，不过在 `GENERIC` 里默认禁用。

```
# 增加 i8254 的 挂起/恢复 支持。
device           pmtimer
```

用于电源管理事件， 例如 APM 和 ACPI 的时钟设备驱动。

```
# PCCARD (PCMCIA) support
# PCMCIA and cardbus bridge support
device          cbb               # cardbus (yenta) bridge
device          pccard            # PC Card (16-bit) bus
device          cardbus           # CardBus (32-bit) bus
```

PCMCIA 支持。如果您使用膝上型计算机，您需要这个。

```
# Serial (COM) ports
device          sio               # 8250, 16[45]50 based serial ports
```

这些串口在 MS-DOS®/Windows® 的世界中称为 `COM` 口。

### 注意:

如果使用内置式的调制解调器， 并占用 `COM4` 而您另有一个串口在 `COM2`， 则必须把调制解调器的 IRQ 改为 2 (由于晦涩的技术原因， IRQ2 = IRQ 9) 才能够在 FreeBSD 中访问它。 如果有多口的串口卡， 请参考 [sio(4)](http://www.FreeBSD.org/cgi/man.cgi?query=sio&sektion=4&manpath=freebsd-release-ports) 以了解需要在 `/boot/device.hints` 中进行的设置。 某些显卡 (特别是基于 S3 芯片的卡) 使用形如 `0x*2e8` 的 IO 地址， 而许多廉价的串口卡不能够正确地对 16-位 IO 地址空间进行解码， 因此它们会产生冲突， 并造成 `COM4` 实际上无法使用。

每一个串口都需要有一个唯一的 IRQ (除非您使用支持中断分享的串口卡)， 因此默认的 `COM3` 和 `COM4` IRQ 是不能使用的。

```
# Parallel port
device          ppc
```

ISA-bus 并行接口。

```
device          ppbus      # Parallel port bus (required)
```

提供并行总线的支持。

```
device          lpt        # Printer
```

提供并口打印机的支持。

### 注意:

要使用并口打印机，就必须同时加入上面三行设置。

```
device          plip       # TCP/IP over parallel
```

这是针对并行网络接口的驱动器。

```
device          ppi        # Parallel port interface device
```

普通用途的 I/O (“geek port”) + IEEE1284 I/O.

```
#device         vpo        # Requires scbus and da
```

这是针对 Iomega Zip 驱动器的。它要求`scbus`和`da`的支持。 最好的执行效果是工作在 EPP 1.9 模式。

```
#device         puc
```

如果您有由 [puc(4)](http://www.FreeBSD.org/cgi/man.cgi?query=puc&sektion=4&manpath=freebsd-release-ports) 支持的 “哑” 串行或并行 PCI 卡， 则应去掉这一行的注释。

```
# PCI Ethernet NICs.
device          de         # DEC/Intel DC21x4x (“Tulip”)
device          em         # Intel PRO/1000 adapter Gigabit Ethernet Card
device          ixgb       # Intel PRO/10GbE Ethernet Card
device          txp        # 3Com 3cR990 (“Typhoon”)
device          vx         # 3Com 3c590, 3c595 (“Vortex”)
```

多种 PCI 网卡驱动器。注释或删除您系统中没有的设备.

```
# PCI Ethernet NICs that use the common MII bus controller code.
# NOTE: Be sure to keep the 'device miibus' line in order to use these NICs!
device          miibus     # MII bus support
```

MII 总线支持对于一些 PCI 10/100 Ethernet NIC 来说是必需的。

```
device          bce        # Broadcom BCM5706/BCM5708 Gigabit Ethernet
device          bfe        # Broadcom BCM440x 10/100 Ethernet
device          bge        # Broadcom BCM570xx Gigabit Ethernet
device          dc         # DEC/Intel 21143 and various workalikes
device          fxp        # Intel EtherExpress PRO/100B (82557, 82558)
device          lge        # Level 1 LXT1001 gigabit ethernet
device          msk        # Marvell/SysKonnect Yukon II Gigabit Ethernet
device          nge        # NatSemi DP83820 gigabit ethernet
device          nve        # nVidia nForce MCP on-board Ethernet Networking
device          pcn        # AMD Am79C97x PCI 10/100 (precedence over 'lnc')
device          re         # RealTek 8139C+/8169/8169S/8110S
device          rl         # RealTek 8129/8139
device          sf         # Adaptec AIC-6915 (“Starfire”)
device          sis        # Silicon Integrated Systems SiS 900/SiS 7016
device          sk         # SysKonnect SK-984x & SK-982x gigabit Ethernet
device          ste        # Sundance ST201 (D-Link DFE-550TX)
device          stge       # Sundance/Tamarack TC9021 gigabit Ethernet
device          ti         # Alteon Networks Tigon I/II gigabit Ethernet
device          tl         # Texas Instruments ThunderLAN
device          tx         # SMC EtherPower II (83c170 “EPIC”)
device          vge        # VIA VT612x gigabit ethernet
device          vr         # VIA Rhine, Rhine II
device          wb         # Winbond W89C840F
device          xl         # 3Com 3c90x (“Boomerang”, “Cyclone”)
```

使用 MII 总线控制器代码的驱动器。

```
# ISA Ethernet NICs.  pccard NICs included.
device          cs         # Crystal Semiconductor CS89x0 NIC
# 'device ed' requires 'device miibus'
device          ed         # NE[12]000, SMC Ultra, 3c503, DS8390 cards
device          ex         # Intel EtherExpress Pro/10 and Pro/10+
device          ep         # Etherlink III based cards
device          fe         # Fujitsu MB8696x based cards
device          ie         # EtherExpress 8/16, 3C507, StarLAN 10 etc.
device          lnc        # NE2100, NE32-VL Lance Ethernet cards
device          sn         # SMC's 9000 series of Ethernet chips
device          xe         # Xircom pccard Ethernet

# ISA devices that use the old ISA shims
#device         le
```

ISA 以太网卡驱动。 参见 `/usr/src/sys/i386/conf/NOTES` 以了解关于哪个驱动程序能够驱动您的网卡的细节。

```
# Wireless NIC cards
device          wlan            # 802.11 support
```

通用 802.11 支持。 这行配置是无线网络所必需的。

```
device          wlan_wep        # 802.11 WEP support
device          wlan_ccmp       # 802.11 CCMP support
device          wlan_tkip       # 802.11 TKIP support
```

针对 802.11 设备的加密支持。 如果希望使用加密和 802.11i 安全协议， 就需要这些配置行。

```
device          an         # Aironet 4500/4800 802.11 wireless NICs.
device          ath             # Atheros pci/cardbus NIC's
device          ath_hal         # Atheros HAL (Hardware Access Layer)
device          ath_rate_sample # SampleRate tx rate control for ath
device          awi        # BayStack 660 and others
device          ral        # Ralink Technology RT2500 wireless NICs.
device          wi         # WaveLAN/Intersil/Symbol 802.11 wireless NICs.
#device         wl         # Older non 802.11 Wavelan wireless NIC.
```

用以支持多种无线网卡。

```
# Pseudo devices
device   loop          # Network loopback
```

这是 TCP/IP 的通用回环设备。 如果您 telnet 或 FTP 到 `localhost` (也就是 `127.0.0.1`) 则将通过这个设备回到本机。 这个设备是 *必需的*。

```
device   random        # Entropy device
```

Cryptographically secure random number generator.

```
device   ether         # Ethernet support
```

`ether` 只有在使用以太网卡时才需要。 它包含了通用的以太网协议代码。

```
device   sl            # Kernel SLIP
```

`sl` 用以提供 SLIP 支持。 目前它几乎已经完全被 PPP 取代了， 因为后者更容易配置， 而且更适合调制解调器之间的连接， 并提供了更强大的功能。

```
device   ppp           # Kernel PPP
```

这一选项用以提供内核级的 PPP 支持， 用于拨号连接。 也有以用户模式运行的 PPP 实现， 使用 `tun` 并提供包括按需拨号在内的更为灵活的功能。

```
device   tun           # Packet tunnel.
```

它会被用户模式的 PPP 软件用到。 参考本书的 PPP 以了解更多的细节。

```
 device   pty           # Pseudo-ttys (telnet etc)
```

这是一个 “pseudo-terminal” 或模拟登入端口。 它用来接收连入的 `telnet` 以及 `rlogin` 会话、 xterm， 以及一些其它程序如 Emacs 等。

```
device   md            # Memory “disks”
```

内存盘伪设备。

```
device   gif           # IPv6 and IPv4 tunneling
```

它实现了在 IPv4 上的 IPv6 隧道、 IPv6 上的 IPv4 隧道、 IPv4 上的 IPv4 隧道、 以及 IPv6 上的 IPv6 隧道。 `gif` 设备是 “自动克隆” 的， 它会根据需要自动创建设备节点。

```
device   faith         # IPv6-to-IPv4 relaying (translation)
```

这个伪设备能捕捉发给它的数据包，并把它们转发给 IPv4/IPv6 翻译服务程序。

```
# The `bpf' device enables the Berkeley Packet Filter.
# Be aware of the administrative consequences of enabling this!
# Note that 'bpf' is required for DHCP.
device   bpf           # Berkeley packet filter
```

这是 Berkeley 包过滤器。这个伪设备允许网络接口被置于混杂模式， 从而，截获广播网 (例如，以太网) 上的每一个数据包。 截获的数据报可以保存到磁盘上，也可以使用 [tcpdump(1)](http://www.FreeBSD.org/cgi/man.cgi?query=tcpdump&sektion=1&manpath=freebsd-release-ports) 程序来分析。

### 注意:

[bpf(4)](http://www.FreeBSD.org/cgi/man.cgi?query=bpf&sektion=4&manpath=freebsd-release-ports) 设备也被用于 [dhclient(8)](http://www.FreeBSD.org/cgi/man.cgi?query=dhclient&sektion=8&manpath=freebsd-release-ports) 来获取默认路由器(网关)的 IP 地址。如果使用 DHCP，就不要注释掉这行。

```
# USB support
device          uhci          # UHCI PCI->USB interface
device          ohci          # OHCI PCI->USB interface
device          ehci          # EHCI PCI->USB interface (USB 2.0)
device          usb           # USB Bus (required)
#device         udbp          # USB Double Bulk Pipe devices
device          ugen          # Generic
device          uhid          # “Human Interface Devices”
device          ukbd          # Keyboard
device          ulpt          # Printer
device          umass         # Disks/Mass storage - Requires scbus and da
device          ums           # Mouse
device          ural          # Ralink Technology RT2500USB wireless NICs
device          urio          # Diamond Rio 500 MP3 player
device          uscanner      # Scanners
# USB Ethernet, requires mii
device          aue           # ADMtek USB Ethernet
device          axe           # ASIX Electronics USB Ethernet
device          cdce          # Generic USB over Ethernet
device          cue           # CATC USB Ethernet
device          kue           # Kawasaki LSI USB Ethernet
device          rue           # RealTek RTL8150 USB Ethernet
```

支持各类 USB 设备。

```
# FireWire support
device          firewire      # FireWire bus code
device          sbp           # SCSI over FireWire (Requires scbus and da)
device          fwe           # Ethernet over FireWire (non-standard!)
```

支持各类火线设备。

要了解 FreeBSD 所支持的设备的其他情况， 请参考 `/usr/src/sys/i386/conf/NOTES`。

### 9.6.1. 大内存支持(PAE)

大内存配置的机器需要超过４GB 的虚拟地址。 因为 4GB 的限制，Intel 在 Pentium®及后续的 CPUs 上增加了 36 位物理地址的支持。

物理地址扩展 (PAE) 是 Intel® Pentium® Pro 和后续的 CPU 提供的一种允许将内存地址扩展到 64GB 的功能， FreeBSD 的所有最新版本均支持此功能， 并通过 `PAE` 选项来启用这个能力。 因为 Intel 架构的限制， 高于或低于 4GB 都没有什么区别， 超过 4GB 的内存分配只是简单地添加到可用内存池中。

为了让内核支持 PAE，只要增加下面这一行到配置文件：

```
options		    PAE
```

### 注意:

PAE 在 FreeBSD 里面现在只能支持 Intel® IA-32 处理器。 同时，还应该注意，FreeBSD 的 PAE 支持没有经过广泛的测试， 和其他稳定的特性相比只能当作是 beta 版。

PAE 在 FreeBSD 下有如下的一些限制：

*   进程不能接触大于 4GB 的 VM 空间。

*   没有使用 [bus_dma(9)](http://www.FreeBSD.org/cgi/man.cgi?query=bus_dma&sektion=9&manpath=freebsd-release-ports) 接口的设备驱动程序在打开了 PAE 支持的内核中会导致数据损坏。 因为这个原因， `PAE` 内核配置文件 会把所有在打开了 PAE 的内核上不能工作的驱动程序排除在外。

*   一些系统打开了探测系统内存资源使用能力的功能，因为打开了 PAE 支持，这些功能可能会被覆盖掉。 其中一个例子就是内核参数`kern.maxvnodes`，它是控制 内核能使用的最大 vnodes 数目的，建议重新调整它及其他类似参数到合适的值。

*   为了避免 KVA 的消耗，很有必要增加系统的内核虚拟地址， 或者减少很耗系统资源的内核选项的总量（看上面）。`KVA_PAGES`选项 可以用来增加 KVA 空间。

为了稳定和高性能，建议查看[tuning(7)](http://www.FreeBSD.org/cgi/man.cgi?query=tuning&sektion=7&manpath=freebsd-release-ports)手册页。[pae(4)](http://www.FreeBSD.org/cgi/man.cgi?query=pae&sektion=4&manpath=freebsd-release-ports)手册页包含 FreeBSD'sPAE 支持的最新信息。

## 9.7. 如果出现问题怎么办

在定制一个内核时，可能会出现四种问题。它们是：

`config`失败：

如果 [config(8)](http://www.FreeBSD.org/cgi/man.cgi?query=config&sektion=8&manpath=freebsd-release-ports) 在给出您的内核描述时失败， 则可能在某些地方引入了一处小的错误。 幸运的是， [config(8)](http://www.FreeBSD.org/cgi/man.cgi?query=config&sektion=8&manpath=freebsd-release-ports) 会显示出它遇到问题的行号， 这样您就能够迅速地定位错误。 例如， 如果您看到：

```
config: line 17: syntax error
```

可以通过与 `GENERIC` 或其他参考资料对比， 来确定这里的关键词是否拼写正确。

`make`失败：

如果 `make` 命令失败， 它通常表示内核描述中发生了 [config(8)](http://www.FreeBSD.org/cgi/man.cgi?query=config&sektion=8&manpath=freebsd-release-ports) 无法找出的的错误。 同样地， 仔细检查您的配置， 如果仍然不能解决问题， 发一封邮件到 [FreeBSD 一般问题邮件列表](http://lists.FreeBSD.org/mailman/listinfo/freebsd-questions) 并附上您的内核配置， 则问题应该很快就能解决。

内核无法启动：

如果您的内核无法启动， 或不识别您的设备， 千万别慌！ 非常幸运的是， FreeBSD 有一个很好的机制帮助您从不兼容的内核恢复。 在 FreeBSD 启动加载器那里简单地选择一下要启动的内核就可以了。 当系统在引导菜单的 10 秒倒计时时进入它， 方法是选择 “Escape to a loader prompt” 选项， 其编号为 6。 输入 `unload kernel`， 然后输入 `boot /boot/kernel.old/kernel`， 或者其他任何一个可以正确引导的内核即可。 当重新配置内核时， 保持一个已经证明能够正常启动的内核永远是一个好习惯。

当使用好的内核启动之后您可以检查配置文件并重新尝试编译它。 比较有用的资源是 `/var/log/messages` 文件， 它会记录每次成功启动所产生的所有内核消息。 此外， [dmesg(8)](http://www.FreeBSD.org/cgi/man.cgi?query=dmesg&sektion=8&manpath=freebsd-release-ports) 命令也会显示这次启动时产生的内核消息。

### 注意:

如果在编译内核时遇到麻烦， 请务必保留一个 `GENERIC` 或已知可用的其他内核， 并命名为别的名字以免在下次启动时被覆盖。 不要依赖 `kernel.old` 因为在安装新内核时， `kernel.old` 会被上次安装的那个可能不正常的内核覆盖掉。 另外， 尽快把可用的内核挪到 `/boot/kernel` 否则类似 [ps(1)](http://www.FreeBSD.org/cgi/man.cgi?query=ps&sektion=1&manpath=freebsd-release-ports) 这样的命令可能无法正常工作。 为了完成这一点， 需要修改目录的名字：

```
# `mv /boot/kernel /boot/kernel.bad`
# `mv /boot/kernel.good /boot/kernel`
```

内核工作，但是[ps(1)](http://www.FreeBSD.org/cgi/man.cgi?query=ps&sektion=1&manpath=freebsd-release-ports)根本不工作:

如果您安装了一个与系统中内建工具版本不同的内核， 例如在 -STABLE 系统上安装了 -CURRENT 的内核， 许多用于检查系统状态的工具如 [ps(1)](http://www.FreeBSD.org/cgi/man.cgi?query=ps&sektion=1&manpath=freebsd-release-ports) 和 [vmstat(8)](http://www.FreeBSD.org/cgi/man.cgi?query=vmstat&sektion=8&manpath=freebsd-release-ports) 都将无法正常使用。 您应该 重新编译一个和内核版本一致的系统。 这也是为什么一般不鼓励使用与系统其他部分版本不同的内核的一个主要原因。

## 第 10 章 打印

Contributed by Sean Kelly.Restructured and updated by Jim Mock.

## 10.1. 概述

FreeBSD 可以支持众多种类的打印机， 从最古老的针式打印机到最新的激光打印机以及它们之间所有类型的打印机， 令您运行的应用程序产生高质量的打印输出。

FreeBSD 也可以配置成网络打印服务器。 它可以从包括 FreeBSD、 Windows® 及 Mac OS® 在内的多种其他计算机上接收打印任务。 FreeBSD 将保证打印任务之间不会相互干扰并一次性完成， 而且能够对机器或用户提交打印任务的情况进行统计并找到其中用量最多的人， 以及生成用于标识打印任务属于哪位用户的 “标签” 页等等。

在读完这章后，您将知道：

*   怎样配置 FreeBSD 后台打印。

*   怎样安装打印过滤器来对特殊的打印任务做特殊的处理， 包括把传来的文档转换成打印机能理解的格式。

*   怎样在打印输出上开启报头或者横幅页功能。

*   怎样打印到连接在其他计算机上的打印机。

*   怎样打印到直接连接在网络上的打印机。

*   怎样控制打印机的限制， 包括限制打印任务的大小和阻止某些用户打印。

*   怎样记录打印机统计表和使用情况。

*   怎样解决打印故障。

在读这章之前， 您应该：

*   知道怎样配置并安装新内核 (第 9 章 *配置 FreeBSD 的内核*)。

## 10.2. 介绍

为了在 FreeBSD 中使用打印机， 需要首先配置好伯克利行式打印机后台打印系统即 LPD。 它是 FreeBSD 的标准打印控制系统。 这章介绍 LPD 后台打印系统， 在接下来将简称为 LPD， 并且将指导您完成其配置。

如果您已经熟悉了 LPD 或者其他后台打印系统， 则可以跳到 设置后台打印系统 这部分。

LPD 完全控制一台计算机上的打印机。 它负责许多的事情：

*   它控制本地和连接在网络上其他计算机上打印机的访问。

*   它允许用户提交要打印的文件; 这些通常被认为是*任务*。

*   它为每个打印机维护一个 *队列* 来防止多个用户在同一时刻访问一台打印机。

*   它可以打印*报头*(也叫做*banner*或者 *burst*页使用户可以轻松的从一堆打印输出中找到它们打印的任务。

*   它来设置连接在串口上的打印机的通讯参数。

*   它能通过网络将任务发送到另外一台计算机的 LPD 后台打印队列中。

*   它可以根据不同种类的打印机语言和打印机的性能运行特殊的过滤器来格式化任务。

*   它记录打印机的使用情况。

通过配置文件 (`/etc/printcap`)和提供的特殊过滤程序， 您可以使 LPD 系统在众多种类的打印机硬件上完成上面全部的或者一些子集的功能。

### 10.2.1. 为什么要用后台打印

如果您是系统唯一的用户， 您可能会奇怪为什么要在您不需要访问控制， 报头页或者打印机使用统计时为后台打印费心。 它可以设置成允许直接访问打印机， 但您还是应该使用后台打印， 因为：

*   LPD 在后台打印任务； 您不用被迫等待数据被完全副本到打印机的时间。

*   LPD 可以可以方便的通过过滤器给任务加上日期/ 时间的页眉或者把一种特殊的文件格式 (比如 TeX DVI 文件) 转换成一种打印机可以理解的格式。 您不必去手动做这些步骤。

*   许多提供打印功能的免费和商业程序想要和您计算机上的的后台打印系统通讯。 通过设置后台打印系统， 您将更轻松的支持其他以后要添加的或者现有的软件。

## 10.3. 基本设置

### 警告:

从 FreeBSD 8.0 起， 串口对应的设备名由 `/dev/ttydN` 变为 `/dev/ttyuN`。 FreeBSD 7.X 用户应将这篇文档的示例中的设备名改为原先的样子。

要想在 LPD 后台打印系统上使用打印机， 您需要设置打印机硬件和 LPD 软件。 这个 文档描述了这两级设置：

*   参见简单打印机 设置来了解怎样连接一个打印机， 告诉 LPD 怎样与 它通讯， 并且打印纯文本到 打印机。

*   参见 高级打印机设置 来了解怎样打印多种 特殊格式的文件， 怎样打印报头页， 怎样通过网络 打印， 怎样控制打印机的访问权限， 并且学会为打印 作业记帐统计。

### 10.3.1. 简单打印机设置

这部分讲解怎样配置打印机硬件和 LPD 使之与打印机配合。 讲解的基础知识有：

*   硬件 设置部分将讲解怎样把一台打印机连接到 您计算机的一个端口上。

*   软件 设置部分将讲解怎样配置 LPD 后台打印的配置 文件 (`/etc/printcap`)。

如果您正在设置一台通过网络协议 接收数据来打印而不是通过串口或者并口的打印机， 参见 使用网络数据流界面的打印机。

尽管这部分叫“简单打印机 设置”， 但还是相当复杂的。 使打印机 配合 LPD 后台打印系统在计算机上正常运转是最难的 部分。 一旦您的打印机可以正常工作后，那些高级选项， 比如报文页和记帐， 是相当简单的。

#### 10.3.1.1. 硬件设置

这部分讲述了打印机连接到计算机的多种 途径。 主要讨论了多种接口和 连接线， 还有允许 FreeBSD 与打印机通讯所需的 内核配置。

如果您已经连接好了您的打印机而且已经 用它在另外一个操作系统下成功的打印， 您 或许可以跳到这个部分软件设置。

##### 10.3.1.1.1. 端口和连接电缆

现在所出售的在 PC 上使用的打印机通常至少有 以下三种接口中的一个：

*   *串口*， 也叫 RS-232 或者 COM 口， 使用您计算机上的串口来发送数据到打印机。 串口在计算机上已经非常普遍， 而且电缆也非常容易买到且容易制作。 串口有时需要特殊的电缆， 而且可能需要您去配置稍微有点儿复杂的通讯选项。 大多数 PC 的串口的最高传输速度只有 115200 bps， 这使得打印很大的图像需要的时间很长。

*   *并口* 使用计算机上的并口来发送数据到打印机。 并口在计算机上也已经非常普遍， 而且速度高于 RS-232 串口。 电缆非常容易买到， 但很难手工制作。 并口通常没有通讯选项， 这使得配置它相当简单。

    并口按打印机上的接头来命名也叫做 “Centronics”接口。

*   USB 接口， 即通用串行总线， 可以达到比并口和串口高很多的速度。 其电缆既简单又便宜。 USB 用来打印比串口和并口更有优势， 但 UNIX® 系统不能很好的支持它。 避免这个问题的方法就是购买一台 像大多数打印机一样的既有 USB 接口又有并口的 打印机。

一般来说并口只提供单向通讯 (计算机到打印机)， 而串口和 USB 则可以提供双向通讯。 新的并口 (EPP 和 ECP) 及打印机在使用了 IEEE-1284 标准的电缆之后， 可以在 FreeBSD 下双向通讯。

与打印机通过并口双向通讯通常由这两种方法中的一种来完成。 第一个方法是使用为 FreeBSD 编写的可以通过打印机使用的语言与打印机通讯的驱动程序。 这通常用在喷墨打印机上， 且可以用来报告剩余墨水多少和其他状态信息。 第二种方法使用在支持 PostScript® 的打印机上。

PostScript® 任务事实上由程序发送给打印机； 但它并不进行打印而是直接将结果返回给计算机。 PostScript® 也采取双向通讯来将打印中的问题报告给计算机， 比如 PostScript® 程序中的错误或者打印机卡纸。 这些信息对于用户来说也许是非常有价值的。 此外， 最好的在支持 PostScript® 的打印机上记帐的方法需要双向通讯： 询问打印机打印总页数 (打印机从出厂一共打印过多少页)， 然后发送用户的任务， 之后再次查询总打印页数。 将打印前后得到的两个值相减就可以得到该用户要付多少纸钱。

##### 10.3.1.1.2. 并口

用并口连接打印机需要用 Centronics 电缆把打印机与计算机连接起来。 具体说明指导在打印机， 计算机的说明书上应该有， 或者干脆两个上面都有。

记住您用的计算机上的哪个并口。 第一个并口在 FreeBSD 上叫 `/dev/ppc0`； 第二个叫 `/dev/ppc1`， 依此类推。打印机设备也用同样的方法命名： `/dev/lpt0` 是接在第一个并口上的打印机， 依此类推。

##### 10.3.1.1.3. 串口

用串口连接打印机需要用 合适的串口电缆把打印机与计算机连接起来。 具体 说明指导应该在打印机， 计算机的说明书上有， 或者 同样干脆两个上面都有。

如果您不确定什么样儿的电缆才是 “ 合适的串口 电缆 ” ， 您可以尝试以下几种不同的 电缆：

*   *调制解调器* 电缆每一端的 每一根引脚都直接连接到另一端 相应的引脚 上。 这种电缆也叫做 “DTE-to-DCE” 电缆。

*   *非调制解调器*电缆上每一端的有些引脚 是与另一端相应引脚直接连接的， 而有一些则是交叉连接的 (比如， 发送数据引脚连接到 接收数据引脚 )， 还有一些引脚直接在电缆连接头儿内 短接。 这种电缆也叫做 “DTE-to-DTE” 电缆。

*   一些特殊的打印机需要的*串口打印机* 电缆， 是一种和非调制解调器电缆类似的电缆， 只是一些信号还是送到了另一端， 而 不是直接在连接头儿内短路。

当然， 您还得为打印机设置通讯参数。 一般是通过打印机面板上的按钮或者 DIP 开关进行设置。 在计算机和打印机上都选择它们所支持的最高 `波特` (每秒多少比特， 有时也叫 *波特率*) 的传输速率。 选择 7 或者 8 个数据位； 选择不校验， 偶校验或者奇校验； 选择 1 个或 2 个停止位。 还要选择流量控制协议： 无， XON/XOFF (也叫做 “in-band” 或 “软件”) 流量控制。 记住您的软件配置中的参数也要设成上面的数值。

#### 10.3.1.2. 软件设置

这部分描述了要使用 FreeBSD 系统中的 LPD 后台打印系统进行打印所需的软件设置。

包括这几个步骤：

1.  在需要的时候配置内核来允许您连接 打印机的端口； 配置内核 部分会告诉您 需要做什么。

2.  如果您使用并口， 则需要设置一下 并口的通讯模式; 设置 并口通讯模式 部分会告诉您具体的 细节。

3.  测试操作系统是否能够发送数据到打印机。 检测打印机 联机状况 部分会告诉您要怎样 做。

4.  为 LPD 设置与打印机匹配的参数则 通过修改 `/etc/printcap` 这个文件来完成。 这章后面 的部分将讲解如何来完成设置。

##### 10.3.1.2.1. 配置内核

操作系统的内核为了使某些特殊设备工作需要重新 编译。 打印机所用的串口、 并口就属于那些特殊设备。 因此， 可能需要 添加对串口或并口的支持， 如果内核并没有配置它们的话。

要想知道您现在使用的内核是否支持串口， 输入：

```
# `grep sioN /var/run/dmesg.boot`
```

其中 *`N`* 是串口的 编号， 从 0 开始。 如果您看到 类似下面的输出：

```
sio2 at port 0x3e8-0x3ef irq 5 on isa
sio2: type 16550A
```

则说明您现在使用的内核支持串口。

要想知道您现在使用的内核是否支持并口， 输入：

```
# `grep ppcN /var/run/dmesg.boot`
```

其中 *`N`* 是并口的 编号， 同样从 0 开始。 如果得到类似 下面的输出：

```
ppc0: <Parallel port> at port 0x378-0x37f irq 7 on isa0
ppc0: SMC-like chipset (ECP/EPP/PS2/NIBBLE) in COMPATIBLE mode
ppc0: FIFO with 16/16/8 bytes threshold
```

那么您现在使用的内核支持并口。

您可能必须为了使操作系统支持您打印机需要的串口或 并口而 重新配置内核。

要增加对串口的支持， 参见 内核配置这部分。 要增加对并口的支持， 除了参见 上面提到的那部分之外， *还要* 参见下面的 部分。

#### 10.3.1.3. 设置并口的通讯模式

在使用并口时， 您可以选择 让 FreeBSD 用中断方式还是轮询方式来 与打印机通讯。 在 FreeBSD 上， 通用的打印机驱动 ([lpt(4)](http://www.FreeBSD.org/cgi/man.cgi?query=lpt&sektion=4&manpath=freebsd-release-ports)) 使用 [ppbus(4)](http://www.FreeBSD.org/cgi/man.cgi?query=ppbus&sektion=4&manpath=freebsd-release-ports) 系统， 它利用 [ppc(4)](http://www.FreeBSD.org/cgi/man.cgi?query=ppc&sektion=4&manpath=freebsd-release-ports) 驱动来控制端口芯片。

*   *中断* 方式是 GENERIC 核心的默认方式。 在这种方式下， 操作系统占用一条中断请求线来检测打印机是否已经做好接收数据的准备。

*   *轮询* 方式是操作系统反复不断的询问打印机是否做好接收数据的准备。 当它返回就绪时， 核心开始发送下面要发送的数据。

中断方式速度通常会快一些， 但却占用了一条宝贵的中断请求线。 一些新出的 HP 打印机 不能正常的工作在中断模式下， 是由于一些定时问题 (还没正确的理解) 造成的。 这些打印机需要使用轮询方式。 您应该使用 任何一种方式， 只要它能正常工作就行。 一些打印机虽然在两种模式下都可以 工作， 但在中断模式下会慢的要命。

您可以用以下两种方法设定通讯模式： 通过 配置内核或者使用 [lptcontrol(8)](http://www.FreeBSD.org/cgi/man.cgi?query=lptcontrol&sektion=8&manpath=freebsd-release-ports) 这个程序。

*要通过配置内核的方法设置 通讯模式：*

1.  修改内核配置文件。 找到 一个叫 `ppc0` 的记录。 如果您想要设置的是 第二个并口， 那么用 `ppc1` 代替。 使用第三个并口的时候用 `ppc2` 代替， 依此类推。

    *   如果您希望使用中断驱动模式， 则应编辑下面的配置：

        ```
        hint.ppc.0.irq="*`N`*"
        ```

        它在 `/boot/device.hints` 这个文件中， 其中 *`N`* 用正确的中断 编号代替。 同时， 核心配置文件也必须 包括 [ppc(4)](http://www.FreeBSD.org/cgi/man.cgi?query=ppc&sektion=4&manpath=freebsd-release-ports) 的驱动：

        ```
        device ppc
        ```

    *   如果您想要使用轮询方式， 只需要把 `/boot/device.hints` 这个文件中的下面这行删除掉：

        ```
        hint.ppc.0.irq="*`N`*"
        ```

        在 FreeBSD 下， 有时上面的方法并不能使并口工作在轮询方式。 大多数情况是由于 [acpi(4)](http://www.FreeBSD.org/cgi/man.cgi?query=acpi&sektion=4&manpath=freebsd-release-ports) 驱动造成的， 它可以自动侦测到设备并将其挂载到系统上， 但也因此， 它控制着打印机端口的访问模式. 您需要检查 [acpi(4)](http://www.FreeBSD.org/cgi/man.cgi?query=acpi&sektion=4&manpath=freebsd-release-ports) 的配置来解决这个问题。

2.  保存文件。 然后配置， 建立， 并安装刚配置的内核， 最后重新启动。 参见 内核配置 这章来获得更多细节。

*使用* [lptcontrol(8)](http://www.FreeBSD.org/cgi/man.cgi?query=lptcontrol&sektion=8&manpath=freebsd-release-ports) *设置通讯模式*：

1.  输入：

    ```
    # `lptcontrol -i -d /dev/lptN`
    ```

    将 `lptN` 设置成中断方式。

2.  输入：

    ```
    # `lptcontrol -p -d /dev/lptN`
    ```

    将 `lptN` 设置成轮询方式。

您可以把这些命令加入到 `/etc/rc.local` 这个文件中， 这样每次启动系统 时都会设置成您想要的方式。 参见 [lptcontrol(8)](http://www.FreeBSD.org/cgi/man.cgi?query=lptcontrol&sektion=8&manpath=freebsd-release-ports) 来获得 更多信息。

#### 10.3.1.4. 检测打印机的通讯

在设置后台打印系统之前， 您应该确保您的计算机可以把数据 发送到打印机上。 分别独立调试打印机的通讯和后台打印系统会更简单。

我们为了测试打印机，将发送一些文本给它。 一个叫 [lptest(1)](http://www.FreeBSD.org/cgi/man.cgi?query=lptest&sektion=1&manpath=freebsd-release-ports) 的程序能胜任这项工作， 它可以让打印机立即打印出程序发给它的 字符： 它在每行打出 可以打印的 96 个 ASCII 字符。

当我们使用的是一台 PostScript® ( 或者以其他语言为基础的 ) 打印机， 那么 需要更仔细的检测。 一段小小的 PostScript® 程序足以完成检测的任务， 比如下面这段程序：

```
%!PS
100 100 moveto 300 300 lineto stroke
310 310 moveto /Helvetica findfont 12 scalefont setfont
(Is this thing working?) show
showpage
```

可以把上面这段 PostScript® 代码写进一个文件里， 并且像下面部分的例子里那样 使用。

### 注意:

上面的小程序是针对 PostScript® 而不是惠普的 PCL 写的。 由于 PCL 拥有许多其他打印机没有的强大功能， 比如它支持在打印纯文本的同时夹带特殊的命令， 而 PostScript® 则不能直接打印纯文本， 所以需要对这类打印机语言进行特殊的处理。

##### 10.3.1.4.1. 检测并口打印机

这部分内容将指导您怎样检测 FreeBSD 是否可以与一台已经连接在并口上的打印机通讯。

*要测试并口上的打印机：*

1.  用 [su(1)](http://www.FreeBSD.org/cgi/man.cgi?query=su&sektion=1&manpath=freebsd-release-ports) 命令转换到 `root` 用户。

2.  发送数据到打印机。

    *   如果打印机可以直接打印纯文本， 可以用 [lptest(1)](http://www.FreeBSD.org/cgi/man.cgi?query=lptest&sektion=1&manpath=freebsd-release-ports)。 输入：

        ```
        # `lptest > /dev/lptN`
        ```

        其中 *`N`* 是并口的编号， 从 0 开始。

    *   如果打印机支持 PostScript® 或其他打印机语言， 可以发送一段小程序到打印机。 输入：

        ```
        # `cat > /dev/lptN`
        ```

        然后， 一行一行地 *输入* 输入这段程序。 因为在按下 `换行` 或者 `回车` 之后， 这一行就不能再修改了。 当您输入完这段程序之后， 按 `CONTROL+D`， 或者其他表示文件结束的键。

        另外一种办法， 您可以把这段程序写在一个文件里， 并输入：

        ```
        # `cat file > /dev/lptN`
        ```

        其中 *`file`* 是包含这您要发给打印机程序的文件名。

之后， 您应该看到打印出了一些东西。 如果打印出的东西看起来并不正确， 请不要着急； 我们将在后面指导您如何解决这类问题。

##### 10.3.1.4.2. 检测串口打印机

这部分将告诉您如何检测 FreeBSD 是否可以与连接在串口上的打印机通讯。

*要测试连接在串口上的打印机：*

1.  通过 [su(1)](http://www.FreeBSD.org/cgi/man.cgi?query=su&sektion=1&manpath=freebsd-release-ports) 命令转为 `root` 用户。

2.  修改 `/etc/remote` 这个文件。 增加下面这些内容：

    ```
    printer:dv=/dev/port:br#*`bps-rate`*:pa=*`parity`*
    ```

    其中 *`port`* 是串口的设备节点 (`ttyu0`、 `ttyu1`， 等等)， *`bps-rate`* 是与打印机通讯时使用的速率， 而 *`parity`* 是通讯时打印机要求的校验方法 (应该是 `even`、 `odd`、 `none`， 或 `zero` 之一)。

    这儿有一个串口打印机的例子， 它连接在第三个串口上， 速度为 19200   波特， 不进行校验：

    ```
    printer:dv=/dev/ttyu2:br#19200:pa=none
    ```

3.  用 [tip(1)](http://www.FreeBSD.org/cgi/man.cgi?query=tip&sektion=1&manpath=freebsd-release-ports) 连接打印机。 输入：

    ```
    # `tip printer`
    ```

    如果没能成功， 则要再次修改 `/etc/remote` 这个文件， 并且试试用 `/dev/cuaaN` 代替 `/dev/ttydN`。

4.  发送数据到打印机。

    *   如果打印机可以直接打印纯文本， 则用 [lptest(1)](http://www.FreeBSD.org/cgi/man.cgi?query=lptest&sektion=1&manpath=freebsd-release-ports)。 输入：

        ```
        % `$lptest`
        ```

    *   如果打印机支持 PostScript® 或者其他 打印机语言， 则发送一段小程序到 打印机。 一行一行的输入程序， 必须 *非常仔细* 因为像退格 或者其他编辑键也许对打印机来说有它的 意义。 您同样也需要按一个特殊的 文件结束键， 让打印机知道它已经 接收了整个程序。 对于 PostScript® 打印机， 按 `CONTROL+D`。

        或者， 您同样也可以把程序存储在一个文件里 并输入：

        ```
        % `>file`
        ```

        其中 *`file`* 是 包含要发送程序的文件名。 在 [tip(1)](http://www.FreeBSD.org/cgi/man.cgi?query=tip&sektion=1&manpath=freebsd-release-ports) 发送这个文件之后， 按代表 文件结束的键。

您应该看到打印出了一些东西。 如果它们看起来并不正确也不要着急； 我们将在稍后的章节中介绍如何解决这类问题。

#### 10.3.1.5. 启用后台打印： 文件 `/etc/printcap`

目前， 您的打印机应该已经连好了线， 系统内核也为与打印机联机而重新配置好 (如果需要的话)， 而且您也已经可以发送一些简单的数据到打印机。 现在， 我们要配置 LPD 来使其控制您的打印机。

配置 LPD 要修改 `/etc/printcap` 这个文件。 由于 LPD 后台打印系统在每次使用后台打印的时候， 都会读取这个文件， 因此对这个文件的修改会立即生效。

[printcap(5)](http://www.FreeBSD.org/cgi/man.cgi?query=printcap&sektion=5&manpath=freebsd-release-ports) 这个文件的格式很简单。 您可以用您最喜欢的文本编辑器来修改 `/etc/printcap` 这个文件。 这种格式和其他的像 `/usr/share/misc/termcap` 和 `/etc/remote` 这类文件是一样的。 要得到关于这种格式的详尽信息， 请参阅联机手册 [cgetent(3)](http://www.FreeBSD.org/cgi/man.cgi?query=cgetent&sektion=3&manpath=freebsd-release-ports)。

简单的后台打印配置包括下面的几步：

1.  给打印机起一个名字 (记忆和使用的别名)， 然后把它们写进文件 `/etc/printcap`； 参见 如何为打印机命名 这章来得到更多的关于起名的帮助。

2.  通过增加 `sh` 项关掉报头页 (它默认是启用的)； 参见 如何禁用报头页 部分来得到更多信息。

3.  建立一个后台打印队列的目录， 并且通过 `sd` 项目指定它的位置； 您可参见 创建后台打印队列目录 一节了解更多信息。

4.  在 `/dev` 下设置打印机设备节点， 并且在写在 `/etc/printcap` 文件中 `lp` 项目里； 参见 识别打印机设备 这部分可以得到更多信息。 此外， 如果打印机连接在串口上， 通讯参数的设置需要写在 `ms#` 项中。 这些参数在 配置后台打印通讯参数 这在前面已经讨论过。

5.  安装纯文本过滤器； 详情请参见 安装文本过滤器 小节。

6.  用 [lpr(1)](http://www.FreeBSD.org/cgi/man.cgi?query=lpr&sektion=1&manpath=freebsd-release-ports) 命令来测试设置。 想得到更多信息可以参见 测试 和 故障排除 部分。

### 注意:

使用打印机语言的打印机， 如 PostScript® 打印机， 通常是不能直接打印纯文本的。 前面提到， 并且将在后面继续进行介绍的简单的设置方法， 均假定您正在安装这种只能打印它能识别的文件格式的打印机。

用户通常会希望直接在系统提供的打印机上打印纯文本。 采用 LPD 接口的程序也通常是这样设计的。 如果您正在安装这样一台打印机， 并且希望它不仅能打印使用它支持的打印机语言的任务 *而且* 还能打印纯文本的任务的话， 那么强烈建议您在上面提到的简单设置的步骤上增加一步： 安装从自动纯文本到 PostScript® (或者其他打印机语言) 的转换程序。 更多的细节， 请参见 在 PostScript® 打印机上打印纯文本。

##### 10.3.1.5.1. 打印机的命名

第一步 (简单) 就是给打印机起一个名字。 您是按功能起名字还是干脆起个古怪的名字都没有关系， 因为您可以给打印机设置许多的别名。

在 `/etc/printcap` 里至少有一个打印机必须指定， 别名是 `lp`。 这是默认的打印机名。 如果用户既没有 `PRINTER` 环境变量， 也没有在任何 LPD 命令的命令行中指定打印机名， 则 `lp` 将是默认要使用的打印机。

还有， 我们通常把最后一个别名设置成能完全描述打印机的名字， 包括厂家和型号。

一旦您选好了名字或者一些别名， 把它们放进文件 `/etc/printcap` 里。 打印机的名字应该从最左边的一列写起。 用竖杠来隔开每个别名， 并且在最后一个别名后面加上一个冒号。

在下面的例子中， 我们从一个基本的 `/etc/printcap` 开始， 它只定义了两台打印机 (一台 Diablo 630 行式打印机和一台 Panasonic KX-P4455 PostScript® 激光打印机 ):

```
#
#  /etc/printcap for host rose
#
rattan|line|diablo|lp|Diablo 630 Line Printer:

bamboo|ps|PS|S|panasonic|Panasonic KX-P4455 PostScript v51.4:
```

在这个例子中， 第一台打印机被命名为 `rattan` 并且设置了 `line`， `diablo`, `lp`， 和 `Diablo 630 Line Printer` 这几个别名。 因为它被设置了 `lp` 这个别名， 所以它是默认打印机。 第二台 被命名为 `bamboo`， 并且设置了 `ps`， `PS`, `S`， `panasonic`， 和 `Panasonic KX-P4455 PostScript v51.4` 这几个别名。

##### 10.3.1.5.2. 不打印报头页

LPD 后台打印系统默认 会为每个任务打印 *报头页*。 报头页 包含了发送这个任务的用户， 发送这个任务的计算机， 任务的名字， 并用大字母打出。 但不幸的是， 所有这些额外的文本， 都会给在对打印机进行最初的配置时排除故障带来困难， 所以我们将先不打印报头页。

要暂停打印报头页， 为打印机的记录增加 `sh` 标记， 在 `/etc/printcap` 文件中。 这儿有一个 `/etc/printcap` 文件中使用 `sh` 的例子:

```
#
#  /etc/printcap for host rose - no header pages anywhere
#
rattan|line|diablo|lp|Diablo 630 Line Printer:\
        :sh:

bamboo|ps|PS|S|panasonic|Panasonic KX-P4455 PostScript v51.4:\
        :sh:
```

注意我们的正确格式: 第一行从最左边一列开始， 而后的每一行用 TAB 缩进一次。 一行写不下需要换行时， 在换行前打一个反斜杠。

##### 10.3.1.5.3. 建立后台打印队列目录

下一步设置就是要建立一个 *后台打印队列目录*， 也就是在打印任务最终完成之前用于存放这些任务的目录， 这个目录中也会存放后台打印系统用到的其他一些文件。

由于后台打印队列目录的变量本质， 通常 把这些目录安排在 `/var/spool` 下。 您也没有必要去 备份后台打印队列目录里的内容。 重新建立它们只要简单的使用 [mkdir(1)](http://www.FreeBSD.org/cgi/man.cgi?query=mkdir&sektion=1&manpath=freebsd-release-ports) 命令。

通常， 我们习惯将目录名起成和 打印机一样的名字， 像下面 这样：

```
# `mkdir /var/spool/printer-name`
```

然而， 如果您有很多网络打印机， 您可能想要把这些后台打印的队列目录目录放在一个单独的专为使用 LPD 打印而准备的目录里。 我们将用我们的两台打印机 `rattan` 和 `bamboo` 作为例子：

```
# `mkdir /var/spool/lpd`
# `mkdir /var/spool/lpd/rattan`
# `mkdir /var/spool/lpd/bamboo`
```

### 注意:

如果担心用户任务的保密性， 可能会希望保护相应的后台打印队列目录， 使之不能被其他用户访问。 后台打印的队列目录的属主应该是 daemon 用户， 而 `daemon` 用户和 `daemon` 组拥有读写和搜索的权限，但其他用户没有。 接下来用我们的两台打印机作为例子:

```
# `chown daemon:daemon /var/spool/lpd/rattan`
# `chown daemon:daemon /var/spool/lpd/bamboo`
# `chmod 770 /var/spool/lpd/rattan`
# `chmod 770 /var/spool/lpd/bamboo`
```

最后， 您需要通过`/etc/printcap` 文件告诉 LPD 这些目录。 您可以用 `sd` 标记来指定后台打印队列目录的路径:

```
#
#  /etc/printcap for host rose - added spooling directories
#
rattan|line|diablo|lp|Diablo 630 Line Printer:\
        :sh:sd=/var/spool/lpd/rattan:

bamboo|ps|PS|S|panasonic|Panasonic KX-P4455 PostScript v51.4:\
        :sh:sd=/var/spool/lpd/bamboo:
```

注意打印机的名字要从第 1 列开始， 其他记录每行都要用 TAB 键缩进一次， 写不开需要换行在最后加上反斜杠。

如果您没用 `sd` 标记指定后台打印队列目录， 后台打印系统会将 `/var/spool/lpd` 目录作为默认目录。

##### 10.3.1.5.4. 识别打印机设备

在 Hardware Setup 一节中，我们说明了 FreeBSD 与打印机通讯将使用哪个端口和 `/dev` 目录下的节点。 我们要告诉 LPD 这些信息。 当后台打印系统有任务需要打印，它将为过滤程序 （负责传送数据到打印机） 打开指定的设备。

用 `lp` 标记在 `/etc/printcap` 里列出 `/dev` 下的设备节点。

在我们的例子中， 假设打印机 `rattan` 在第一个并口上， 打印机 `bamboo` 在第六个串口上; 下面是 要对 `/etc/printcap` 文件里增加的内容 :

```
#
#  /etc/printcap for host rose - identified what devices to use
#
rattan|line|diablo|lp|Diablo 630 Line Printer:\
        :sh:sd=/var/spool/lpd/rattan:\
        :lp=/dev/lpt0:

bamboo|ps|PS|S|panasonic|Panasonic KX-P4455 PostScript v51.4:\
        :sh:sd=/var/spool/lpd/bamboo:\
        :lp=/dev/ttyu5:
```

如果您没在您的 `/etc/printcap` 文件中 用 `lp` 标记指定设备节点， LPD 将默认使用 `/dev/lp` 。 `/dev/lp` 目前在 FreeBSD 中不存在。

如果您正在安装的打印机是连接在 并口上的， 请跳到 安装文本 过滤器 这章。 如果不是的话， 还是最好按下面介绍的 步骤做。

##### 10.3.1.5.5. 配置后台打印通讯参数

对于连在串口上的打印机， LPD 可以为发送数据到打印机的过滤程序设置好波特率， 校验， 和其他串口通讯参数 。 这是有利的， 因为:

*   它可以让您只需简单的修改 `/etc/printcap` 就能尝试不同的通讯 参数; 您并不需要去重新编译过滤器 程序。

*   它使得后台打印系统可以在 多台有不同串口通讯设置的打印机上使用 相同的过滤器程序。

下面这个 `/etc/printcap` 中 用 `lp` 标记来控制列出设备的 串口通讯参数 :

`br#bps-rate`

设置设备的通讯速度为 *`bps-rate`*， 这里 *`bps-rate`* 可以为 50， 75， 110, 134， 150， 200， 300， 600， 1200， 1800， 2400， 4800， 9600, 19200， 38400， 57600， or 115200 比特每秒。

`ms#stty-mode`

设置已打开的中端设备的选项 。 [stty(1)](http://www.FreeBSD.org/cgi/man.cgi?query=stty&sektion=1&manpath=freebsd-release-ports) 将详细 讲述可用的选项。

当 LPD 打开 用 `lp` 指定的设备时， 它会 将设备的特性设置成在 `ms#` 标记后指定的那样。 特别是 `parenb`, `parodd`， `cs5`, `cs6`， `cs7`, `cs8`， `cstopb`, `crtscts`， 和 `ixon` 这些模式， 它们在 [stty(1)](http://www.FreeBSD.org/cgi/man.cgi?query=stty&sektion=1&manpath=freebsd-release-ports) 手册中有详细说明。

我们举个例子来添加我们连在第 6 个串口上的 打印机。 我们将设波特为 38400。 至于模式， 我们将用 `-parenb` 设置成不校验， 用 `cs8` 设置成 8 位字符， 用 `clocal` 设置成不要调制解调器控制， 用 `crtscts` 设置成硬件流量控制：

```
bamboo|ps|PS|S|panasonic|Panasonic KX-P4455 PostScript v51.4:\
        :sh:sd=/var/spool/lpd/bamboo:\
        :lp=/dev/ttyu5:ms#-parenb cs8 clocal crtscts:
```

##### 10.3.1.5.6. 安装文本过滤器

我们现在准备告诉 LPD 使用什么文本过滤器 给打印机发送任务。 *文本过滤器*， 也叫 *输入过滤器*， 是一个 在 LPD 有一个任务要发给 打印机时运行的程序。 当 LPD 为打印机运行文本过滤器时， 它设置过滤器的 标准输入为要发给打印机的任务， 而标准输出为 用 `lp` 标记指定的打印机 。 过滤器先从标准输入读取 任务， 为打印机进行一些转换 ， 并将结果写到标准输出， 这些结果 将被打印。 想得到更多关于文本过滤器的信息， 见 过滤器 这节。

对于简单的打印机设置， 文本过滤器可以仅仅是一段 执行 `/bin/cat` 的 shell 脚本来 发送任务到打印机。 FreeBSD 还提供了一个叫做 `lpf` 的过滤器， 它可以处理退格和下划线来 使那些可能不能很好处理这类字符流的打印机正常工作。 而且， 当然， 您可以用任何其他的 您想用的过滤程序。 `lpf` 过滤器在 lpf: 一个文本 过滤器 这节将有详细描述。

首先， 我们来写一段叫做 `/usr/local/libexec/if-simple` 的简单 shell 脚本作为文本过滤器。 用您熟悉的文本编辑器将下面的内容放进 这个文件：

```
#!/bin/sh
#
# if-simple - Simple text input filter for lpd
# Installed in /usr/local/libexec/if-simple
#
# Simply copies stdin to stdout.  Ignores all filter arguments.

/bin/cat && exit 0
exit 2
```

使这个文件可以被执行：

```
# `chmod 555 /usr/local/libexec/if-simple`
```

然后用 `if` 标记在 `/etc/printcap` 里告诉 LPD 使用这个脚本。 我们将仍然为 一直作为例子的这两台打印机在 `/etc/printcap` 里增加这个标记：

```
#
#  /etc/printcap for host rose - added text filter
#
rattan|line|diablo|lp|Diablo 630 Line Printer:\
        :sh:sd=/var/spool/lpd/rattan:\ :lp=/dev/lpt0:\
        :if=/usr/local/libexec/if-simple:

bamboo|ps|PS|S|panasonic|Panasonic KX-P4455 PostScript v51.4:\
        :sh:sd=/var/spool/lpd/bamboo:\
        :lp=/dev/ttyu5:ms#-parenb cs8 clocal crtscts:\
        :if=/usr/local/libexec/if-simple:
```

### 注意:

`if-simple` 脚本的副本可以在 `/usr/share/examples/printing` 目录中找到。

##### 10.3.1.5.7. 开启 LPD

[lpd(8)](http://www.FreeBSD.org/cgi/man.cgi?query=lpd&sektion=8&manpath=freebsd-release-ports) 在 `/etc/rc` 中被运行， 它是否被运行由 `lpd_enable` 这个变量控制。 这个 变量默认是 `NO`。 如果您还没有修改 ， 那么增加这行：

```
lpd_enable="YES"
```

到 `/etc/rc.conf` 文件当中， 然后既可以重启您的 机器， 也可以直接运行 [lpd(8)](http://www.FreeBSD.org/cgi/man.cgi?query=lpd&sektion=8&manpath=freebsd-release-ports)。

```
# `lpd`
```

##### 10.3.1.5.8. 测试

现在已经基本完成了 LPD 的基本设置。 但不幸的是， 还不是庆祝的时候， 因为我们还需要测试设置并且修正所有的 问题。 要测试设置， 尝试打印一些东西。 要 用 LPD 系统打印， 您可以 使用 [lpr(1)](http://www.FreeBSD.org/cgi/man.cgi?query=lpr&sektion=1&manpath=freebsd-release-ports) 命令， 它可以提交一个任务来打印。

您可以联合使用 [lpr(1)](http://www.FreeBSD.org/cgi/man.cgi?query=lpr&sektion=1&manpath=freebsd-release-ports) 和 [lptest(1)](http://www.FreeBSD.org/cgi/man.cgi?query=lptest&sektion=1&manpath=freebsd-release-ports) 程序， 在 检查打印机 通讯 这节介绍怎样生成一些测试文本。

*要测试简单 LPD 设置：*

输入：

```
# `lptest 20 5 | lpr -Pprinter-name`
```

其中 *`printer-name`* 是 在 `/etc/printcap` 中指定的打印机的一个名字 ( 或者一个别名) 。 要测试默认 打印机， 输入 [lpr(1)](http://www.FreeBSD.org/cgi/man.cgi?query=lpr&sektion=1&manpath=freebsd-release-ports) 不带任何 `-P` 选项。 同样， 如果您正在测试一台使用 PostScript® 的打印机， 发送一个 PostScript® 程序到打印机而不是 使用 [lptest(1)](http://www.FreeBSD.org/cgi/man.cgi?query=lptest&sektion=1&manpath=freebsd-release-ports)。 您可以把程序放在一个 文件里， 然后输入： `lpr file`。

对于一台 PostScript® 打印机， 您应该得到那段程序的 结果。 而如果您使用的 [lptest(1)](http://www.FreeBSD.org/cgi/man.cgi?query=lptest&sektion=1&manpath=freebsd-release-ports)， 则您得到的 结果应该看起来像下面这样：

```
!"#$%&'()*+,-./01234
"#$%&'()*+,-./012345
#$%&'()*+,-./0123456
$%&'()*+,-./01234567
%&'()*+,-./012345678
```

要更进一步的测试打印机， 尝试下载一些大的 程序 (为基于特定语言的打印机 ) 或者运行 [lptest(1)](http://www.FreeBSD.org/cgi/man.cgi?query=lptest&sektion=1&manpath=freebsd-release-ports) 并使用不同的参数。 比如， `lptest 80 60` 将生成 60 行 每行 80 个字符。

如果打印机不能工作， 参考 故障排除 这节。

## 10.4. 高级设置

### 警告:

从 FreeBSD 8.0 起， 串口对应的设备名由 `/dev/ttydN` 变为 `/dev/ttyuN`。 FreeBSD 7.X 用户应将这篇文档的示例中的设备名改为原先的样子。

这部分将描述用来打印特别格式文件， 页眉， 通过网络打印， 以及对打印机使用限制和 记帐。

### 10.4.1. 过滤器

尽管 LPD 处理网络协议， 任务排队， 访问控制， 和打印的其他方面， 但大部分 *实际* 工作还是由 *过滤器*。 过滤器是 一种与打印机通讯并且处理设备依赖和特殊需要的 程序。 在简单打印机设置这节里， 我们安装了一个纯文本过滤器 ── 一个应该可以用在大多数打印机上的极简单的过滤器 ( 安装文本过滤器)。

然而，为了进行格式转换， 打印记帐， 适应特殊的打印机， 等等， 您需要明白过滤器是怎样工作的。 在根本上过滤器负责处理这些方面。 但坏消息是大多数时候 *您* 必须自己提供过滤器。 好消息是很多过滤器通常都已经有了; 当没有的时候， 它们通常也是很好写的。

FreeBSD 也提供了一个过滤器， `/usr/libexec/lpr/lpf`， 可以让大多数可以打印纯文本的打印机工作。 ( 它处理文件里的退格和跳格，并且进行记帐， 但这基本就是它所有能做的了。) 这里还有几个过滤器和过滤器组件在 FreeBSD Ports Collection 里。

这是在这节里您将找到的内容：

*   在 过滤器是如何工作的 小节中将介绍在打印过程中过滤器的作用。 如果希望了解在 LPD 使用过滤器时， 在 “幕后” 发生的事情， 便应阅读这一小节。 了解这些知识能够帮助您在为打印机安装过滤器时更快地排查可能会遇到的各种问题。

*   LPD 假定任何打印机在默认状态下均能打印纯文本内容。 对于不能直接打印纯文本的 PostScript® 打印机 (以及其他基于打印语言的打印机) 而言这会带来问题。 在 在 PostScript® 打印机上使用纯文本任务 这节中将会介绍如何解决这个问题的方法。 如果您使用 PostScript® 打印机， 就应阅读这节内容。

*   PostScript® 对于许多程序来说都是一个非常受欢迎的输出格式。 一些人甚至直接写 PostScript® 代码。 但不幸的是， PostScript® 打印机非常昂贵。 模拟 PostScript® 在 非 PostScript® 打印机上 这节将告诉您怎样进一步修改 打印机的文本过滤器， 使得一台 *非 PostScript®* 打印机接受 并打印 PostScript® 数据。 如果 您没有 PostScript® 打印机， 那么您应该阅读这个小节。

*   转换过滤器 这节讲述了一个自动把指定格式文件， 比如图像或排版数据， 转换成您打印机可以理解的格式的方法。 在阅读了这节之后， 您就应该可以配置打印机， 让用户可以用 `lpr -t` 来打印 troff 数据、 用 `lpr -d` 来打印 TeX DVI 数据， 或用 `lpr -v` 来打印光栅图像数据等工作了。 建议您阅读这节。

*   输出 过滤器 这节讲述了这个不是经常使用的 LPD: 的功能－输出过滤器。 除非您要打印页眉 (见 页眉 这节 )， 您或许可以完全跳过这节。

*   lpf: 一个文本过滤器 描述了 `lpf`， 一个 FreeBSD 自带的相当完整而又简单的文本过滤器， 可以使用在行式打印机(和那些担当行式打印机功能的激光打印机 )上。 如果您需要一个快速的方法来让打印机统计打印纯文本的工作量， 或者您有一台遇到退格字符就冒烟的打印机， 您应该考虑 `lpf`。

### 注意:

您可以在 `/usr/share/examples/printing` 目录中找到下面将提到的那些脚本的副本。

#### 10.4.1.1. 过滤器是怎样工作的

前面说过， 过滤器是一个被 LPD 启动， 用来处理与打印机通讯过程中设备依赖的部分 的可执行程序。

当 LPD 想要打印 一个任务中的文件， 它启动一个过滤器 程序。 它把要打印的文件设置成过滤器的标准输入， 标准输出设置成打印机， 并且把错误信息定向到 错误日志文件 (在 `lf` 标识里指定， 默认在 `/etc/printcap`， 或者 `/dev/console` 文件里 )。

过滤器被 LPD 启动， 并且过滤器的参数依赖于 `/etc/printcap` 文件中所列出的和用户为任务用 [lpr(1)](http://www.FreeBSD.org/cgi/man.cgi?query=lpr&sektion=1&manpath=freebsd-release-ports) 命令所指定的。 例如， 如果用户输入 `lpr -t`， LPD 会启动 troff 过滤器， 即在目标打印机的 `tf` 标签里所列出的过滤器。 如果用户想要打印纯文本， 它将会启动 `if` 过滤器 (这是通常的情况： 参见 输出过滤器 来得到 细节)。

在 `/etc/printcap` 文件中， 您可以指定三种过滤器：

*   The *文本过滤器* ， 在 LPD 文档中也叫做 *输入过滤器* ， 处理 常规的文本打印。 可以把它想象成默认过滤器。 LPD 假定每台打印机默认情况下都可以打印纯文本， 而文本过滤器的任务就是来搞定退格、 跳格， 或者其他在某种打印机上容易错误的特殊字符。 如果您所在的环境对打印机的使用情况进行记帐， 那么文本过滤器必须也对打印的页数进行统计， 通常是根据打印的行数和打印机在每页上能打印的行数进行计算得出。 文本过滤器的启动命令为：

    `filter-name` [-c] -w *`width`* -l *`length`* -i *`indent`* -n *`login`* -h *`host`* *`acct-file`*

    这里

    `-c`

    当任务用 `lpr -l` 这个命令提交时出现

    *`width`*

    这里取您在 `/etc/printcap` 文件中指定的 `pw` (页 宽) 标签的值， 默认为 132。

    *`length`*

    这里取您的 `pl` (页 长) 标签的值， 默认为 66

    *`indent`*

    这里是来自 `lpr -i` 命令的总缩进量， 默认为 0

    *`login`*

    这里是正在打印文件的用户名

    *`host`*

    这里是提交打印任务的主机名

    *`acct-file`*

    这里是来自 `af` 变量中指定的用于记帐的文件名。

*   *转换过滤器* 的功能是， 将特定格式的文件转换成打印机能够识别并打印的格式。 例如， ditroff 格式的排版数据就是无法直接打印的， 但您可以安装一个转换过滤器来将 ditroff 文件转换成一种打印机可以识别和打印的形式。 请参见 转换过滤器 这一节来了解更多细节。 如果您需要对打印进行记帐， 那么转换过滤器也必须完成记帐工作。 转换过虑器的启动命令为：

    `filter-name` -x *`pixel-width`* -y *`pixel-height`* -n *`login`* -h *`host`* *`acct-file`*

    这其中 *`pixel-width`* 的值来自 `px` 标签 (默认为 0)， 而 *`pixel-height`* 的值来自 `py` 标签 (默认为 0)。

*   *输出过滤器* 仅在没有文本过滤器时， 或者报头页被打开时使用。 就我们的经验而言， 输出过滤器是很少用到的. 在 输出过滤器 这节中会介绍它们。 启动输出过滤器的命令行只有两个参数：

    `filter-name` -w *`width`* -l *`length`*

    它们的作用与文本过滤器的 `-w` 和 `-l` 参数是一样的。

过滤器也应该在 *退出* 时给出下面的几种退出状态:

exit 0

过滤器已经成功的打印了文件.

exit 1

过滤器打印失败了， 但希望 LPD 试着再打印一次。 如果过滤器返回了这个状态， LPD 将重新启动过滤器。

exit 2

过滤器打印失败并且不希望 LPD 重试。 这种情况下 LPD 会放弃这个文件。

文本过滤器随 FreeBSD 一起发布， 文件名为 `/usr/libexec/lpr/lpf`， 它利用页宽和页长参数来决定何时发送送纸指令， 并提供位打印记帐的方法。 它使用登录名、 主机名， 和记帐文件参数来生成记帐记录。

如果您想购买过滤器， 要注意它是否是与 LPD 兼容。 如果兼容的话， 则它们必须支持前面提到的那些参数。 如果您打算编写普通的过滤器程序， 则同样需要使之支持前面那些参数和退出状态码。

#### 10.4.1.2. 在 PostScript® 打印机上打印纯文本任务

如果您是您的计算机和 PostScript® (或其他语言的) 打印机的唯一用户， 而且您不打算发送纯文本到打印机， 并因此不打算从应用程序程序直接将纯文本发到打印机的话， 就完全不需要再关心这节的内容了。

但是， 如果打印机同时需要接收 PostScript® 和纯文本的任务， 就需要对打印机进行设置了。 要完成这项工作， 我们需要一个文本过滤器来检测到达的任务是纯文本的还是 PostScript® 格式的。 所有 PostScript® 的任务必须以 `%!` (其他打印机语言请参见打印机的文档) 开头。 如果任务的头两个字符是这两个， 就代表这是 PostScript® 格式的， 并且可以直接略过任务剩余的部分。 如果任务开头的两个字符不是这两个， 那么过滤器将把文本转换成 PostScript® 并打印结果。

我们怎样去做？

如果你有一台串口打印机， 一个好办法就是安装 `lprps`。 `lprps` 是一个可以与打印机进行双向通信 PostScript® 打印机过滤器。 它用打印机传来的详细信息来更新打印机的状态文件， 所以用户和管理员可以准确的看到打印机处在什么样的状态 (比如 缺墨 或者 卡纸)。 但更重要的是， 它包含了一个叫做 `psif` 的程序， 它可以检测接收到的文件是否是纯文本的， 并且将使用 `textps` 命令 ( 也是由 `lprps` 提供的程序) 转换文本到 PostScript®。 然后它会用 `lprps` 将任务发送到打印机。

`lprps` 可以在 FreeBSD Ports Collection (详见 The Ports Collection) 中找到。 你可以根据页面的尺寸选择安装 [print/lprps-a4](http://www.freebsd.org/cgi/url.cgi?ports/print/lprps-a4/pkg-descr) 和 [print/lprps-letter](http://www.freebsd.org/cgi/url.cgi?ports/print/lprps-letter/pkg-descr)。 在安装了 `lprps` 之后， 只需指定 `psif` 这个程序的路径， 这也是包含在 `lprps` 中的一个程序。 如果您已经用 ports 安装好了 `lprps`， 将下面的内容添加到 `/etc/printcap` 文件中 PostScript® 打印机的记录部分中：

```
:if=/usr/local/libexec/psif:
```

同时还需要指定 `rw` 标签来告诉 LPD 使用读-写模式打开打印机。

如果您有一台并口的 PostScript® 打印机 (因此不能与打印机进行 `lprps` 需要的双向通信)， 可以使用下面这段 shell 脚本来充当文本过滤器:

```
#!/bin/sh
#
#  psif - Print PostScript or plain text on a PostScript printer
#  Script version; NOT the version that comes with lprps
#  Installed in /usr/local/libexec/psif
#

IFS="" read -r first_line
first_two_chars=`expr "$first_line" : '\(..\)'`

if [ "$first_two_chars" = "%!" ]; then
    #
    #  PostScript job， print it.
    #
    echo "$first_line" && cat && printf "\004" && exit 0
    exit 2
else
    #
    #  Plain text， convert it， then print it.
    #
    ( echo "$first_line"; cat ) | /usr/local/bin/textps && printf "\004" && exit 0
    exit 2
fi
```

在上面的脚本中， `textps` 命令是一个独立安装的程序用来将纯文本转换成 PostScript®。 您可以使用任何您喜欢的文本到 PostScript® 转换程序。 FreeBSD Ports Collection (详见 Ports Collection) 中包含了一个功能非常完整的文本到 PostScript® 的转换程序， 它叫做 `a2ps`。

#### 10.4.1.3. 模拟 PostScript® 在非 PostScript® 打印机上

PostScript® 是高质量排版和打印 *事实上的* 标准。 而 PostScript® 也是一个 *昂贵* 的标准。 幸好， Aladdin 开发了一个和 PostScript® 类似的叫做 Ghostscript 的程序可以用在 FreeBSD 上。 Ghostscript 可以读取大多数 PostScript® 的文件并处理其中的页面交给多种设备，包括许多品牌的非 PostScript® 打印机。 通过安装 Ghostscript 并使用一个特殊的文本过滤器，则可以使一台非 PostScript® 打印机用起来就像真的 PostScript® 打印机一样。

Ghostscript 被收录在 FreeBSD Ports Collection 中，有许多可用的版本， 比较常用的版本是 [print/ghostscript-gpl](http://www.freebsd.org/cgi/url.cgi?ports/print/ghostscript-gpl/pkg-descr)。

要模拟 PostScript®， 文本过滤器要检测是否要打印一个 PostScript® 文件。 如果不是， 那么过滤器将直接将文件发送到打印机； 否则， 它会用 Ghostscript 先将文件转换成打印机可以理解的格式。

这里有一个例子: 下面的脚本是一个针对 Hewlett Packard DeskJet 500 打印机的文本过滤器。 对于其他打印机， 替换 `gs` (Ghostscript) 命令中的 `-sDEVICE` 参数就可以了。 (输入 `gs -h` 来获得当前安装的 Ghostscript 所支持的设备列表。)

```
#!/bin/sh
#
#  ifhp - Print Ghostscript-simulated PostScript on a DeskJet 500
#  Installed in /usr/local/libexec/ifhp

#
#  Treat LF as CR+LF (to avoid the "staircase effect" on HP/PCL
#  printers):
#
printf "\033&k2G" || exit 2

#
#  Read first two characters of the file
#
IFS="" read -r first_line
first_two_chars=`expr "$first_line" : '\(..\)'`

if [ "$first_two_chars" = "%!" ]; then
    #
    #  It is PostScript; use Ghostscript to scan-convert and print it.
    #
    /usr/local/bin/gs -dSAFER -dNOPAUSE -q -sDEVICE=djet500 \
      -sOutputFile=- - && exit 0
else
    #
    #  Plain text or HP/PCL， so just print it directly; print a form feed
    #  at the end to eject the last page.
    #
    echo "$first_line" && cat && printf "\033&l0H" &&
exit 0
fi

exit 2
```

最后， 需要告知 LPD 所使用的过滤器， 通过 `if` 标签完成:

```
:if=/usr/local/libexec/ifhp:
```

您可以输入 `lpr plain.text` 和 `lpr whatever.ps`， 它们都应该可以成功打印。

#### 10.4.1.4. 转换过滤器

在完成了 打印机简单设置 这节中所描述的内容之后， 头一件事 恐怕就是为你喜爱的格式的文件安装转换过滤器了 (除了纯 ASCII 文本)。

##### 10.4.1.4.1. 为什么安装转换过滤器?

转换过滤器使打印众多格式的文件变得很容易。 比如， 假设我们大量使用 TeX 排版系统， 并且有一台 PostScript® 打印机。 每次从 TeX 生成一个 DVI 文件， 我们都不能直接打印它直到我们将 DVI 文件转换成 PostScript®。 转换的命令应该是下面的样子:

```
% `dvips seaweed-analysis.dvi`
% `lpr seaweed-analysis.ps`
```

通过安装 DVI 文件的转换过滤器， 我们可以跳过每次手动转换这一步， 而让 LPD 来完成这个步骤。 现在， 每次要打印 DVI 文件， 我们只需要一步就可以打印它：

```
% `lpr -d seaweed-analysis.dvi`
```

我们要 LPD 转换 DVI 文件是通过指定 `-d` 选项完成的。 格式和转换 选项 这一节列出了所有的转换选项。

对于每种想要打印机支持的转换， 首先要安装 *转换过滤器* 然后在 `/etc/printcap` 中指定它的路径。 在简单打印设置中， 转换过滤器类似于文本过滤器 (详见 安装文本过滤器 ) 不同的是它不是用来打印纯文本， 而是将一个文件转换成打印机能够理解的格式。

##### 10.4.1.4.2. 我应该安装哪个转换过滤器？

您应该安装您希望使用的转换过滤器。 如果要打印很多 DVI 数据， 就需要 DVI 转换过滤器； 如果有大量的 troff 数据， 就应该安装 troff 过滤器。

下面的表格总结了可以与 LPD 配合 工作的过滤器， 以及它们在 `/etc/printcap`文件中的变量名， 还有如何在 `lpr`命令中调用它们：

| 文件类型 | 在`/etc/printcap`文件中的变量名 | 在`lpr`命令中调用使用的参数 |
| --- | --- | --- |
| cifplot | `cf` | `-c` |
| DVI | `df` | `-d` |
| plot | `gf` | `-g` |
| ditroff | `nf` | `-n` |
| FORTRAN text | `rf` | `-f` |
| troff | `tf` | `-f` |
| raster | `vf` | `-v` |
| plain text | `if` | none, `-p`, or `-l` |

在例子中， `lpr -d`就是指 打印机需要在`/etc/printcap`文件中 `df`变量所指的过滤器。

不管别人怎么说， 像 FORTRAN 的文本 和 plot 这些格式已经基本不用了。 所以在您的机器上， 就可以安装其他的过滤器来替换这些参数原有的意义。 例如， 假设想要能直接打印 Printerleaf 文件 (由 Interleaf desktop publishing 程序生成)， 而且不打算打印 plot 文件， 就可以安装一个 Printerleaf 转换过滤器并且用 `gf` 变量指定它。 然后就可以告诉您的用户使用 `lpr -g` 就可以 “打印 Printerleaf 文件。”

##### 10.4.1.4.3. 安装转换过滤器

以为安装的转换过滤器不是 FreeBSD 基本系统的一部分， 所以它们可能是在 `/usr/local` 目录下。 通常目录 `/usr/local/libexec` 是保存它们的地方， 因为它们通常是通过 LPD 运行的； 普通用户应该并不需要直接运行它们。

要启用一个转换过滤器， 只需要在 `/etc/printcap` 文件中为目标打印机中合适的变量赋上过滤器所在的路径。

在接下来的例子当中， 我们将为 一台叫做 `bamboo` 的打印机添加一个转换过滤器。 下面是这个例子的 `/etc/printcap` 文件， 其中使用新变量 `df` 来为打印机 `bamboo` 设置转换过滤器：

```
#
#  /etc/printcap for host rose - added df filter for bamboo
#
rattan|line|diablo|lp|Diablo 630 Line Printer:\
        :sh:sd=/var/spool/lpd/rattan:\
        :lp=/dev/lpt0:\
        :if=/usr/local/libexec/if-simple:

bamboo|ps|PS|S|panasonic|Panasonic KX-P4455 PostScript v51.4:\
        :sh:sd=/var/spool/lpd/bamboo:\
        :lp=/dev/ttyu5:ms#-parenb cs8 clocal crtscts:rw:\
        :if=/usr/local/libexec/psif:\
        :df=/usr/local/libexec/psdf:
```

这里的 DVI 过滤器是一段 shell 脚本， 名字叫做 `/usr/local/libexec/psdf`。 下面是它的代码：

```
#!/bin/sh
#
#  psdf - DVI to PostScript printer filter
#  Installed in /usr/local/libexec/psdf
#
# Invoked by lpd when user runs lpr -d
#
exec /usr/local/bin/dvips -f | /usr/local/libexec/lprps "$@"
```

这段脚本以过滤器模式运行 `dvips` (参数 `-f` ) 并从标准输入读取要打印的任务。 然后运行 PostScript® 文本过滤器 `lprps` (详见 在 PostScript® 打印机上打印纯文本任务 这一节)， 并且带着 LPD 传给脚本的全部参数。 `lprps` 工具将利用这些参数来为打印进行记帐。

##### 10.4.1.4.4. 更多转换过滤器应用实例

因为安装转换过滤器的步骤并不是固定的， 所以这节介绍了一些可行的例子。 在以后的安装配置过程中可以以这些例子为参考。 甚至如果合适的话， 可以完全照搬过去。

这段例子中的脚本是一个 Hewlett Packard LaserJet III-Si 打印机的光栅格式数据 (实际上也就是 GIF 文件)：

```
#!/bin/sh
#
#  hpvf - Convert GIF files into HP/PCL, then print
#  Installed in /usr/local/libexec/hpvf

PATH=/usr/X11R6/bin:$PATH; export PATH
giftopnm | ppmtopgm | pgmtopbm | pbmtolj -resolution 300 \
    && exit 0 \
    || exit 2
```

它的工作原理就是将 GIF 文件转换成 portable anymap， 再转换成 portable graymap， 然后再转换成 portable bitmap， 最后再转换成 LaserJet/PCL- 兼容的数据。

下面是为打印机配置上上述过滤器的 `/etc/printcap` 文件：

```
#
#  /etc/printcap for host orchid
#
teak|hp|laserjet|Hewlett Packard LaserJet 3Si:\
        :lp=/dev/lpt0:sh:sd=/var/spool/lpd/teak:mx#0:\
        :if=/usr/local/libexec/hpif:\
        :vf=/usr/local/libexec/hpvf:
```

下面的脚本是一个在名叫 `bamboo` 的这台 PostScript® 打印机上打印用 groff 排版软件生成的 troff 数据的打印过滤器：

```
#!/bin/sh
#
#  pstf - Convert groff's troff data into PS, then print.
#  Installed in /usr/local/libexec/pstf
#
exec grops | /usr/local/libexec/lprps "$@"
```

上面这段脚本还是用 `lprps` 来与打印机进行通讯。 如果打印机是接在并口上的， 那么就应该使用下面的这段脚本：

```
#!/bin/sh
#
#  pstf - Convert groff's troff data into PS, then print.
#  Installed in /usr/local/libexec/pstf
#
exec grops
```

这里是我们要启用过滤器需要在 `/etc/printcap` 里增加的内容：

```
:tf=/usr/local/libexec/pstf:
```

下面的例子也许会让许多 FORTRAN 老手羞愧。 它是一个 FORTRAN- 文本 的过滤器， 能在任意一台 可以打印纯文本的打印机上使用。 我们将为打印机 `teak` 安装这个过滤器：

```
#!/bin/sh
#
# hprf - FORTRAN text filter for LaserJet 3si:
# Installed in /usr/local/libexec/hprf
#

printf "\033&k2G" && fpr && printf "\033&l0H" &&
 exit 0
exit 2
```

然后我们要在 `/etc/printcap` 中为打印机能够 `teak` 启用这个过滤器添加下面的内容：

```
:rf=/usr/local/libexec/hprf:
```

最后， 再给出一个有些复杂的例子。 我们将给以前介绍过的 `teak` 这台激光打印机添加一个 DVI 过滤器。 首先， 最容易的部分： 更新 `/etc/printcap` 加入 DVI 过滤器的路径：

```
:df=/usr/local/libexec/hpdf:
```

现在， 该困难的部分了： 编写过滤器。 为了实现过滤器， 我们需要一个 DVI-到-LaserJet/PCL 转换程序。 FreeBSD Ports Collection (详见 Ports Collection 这一节) 中有一个： [print/dvi2xx](http://www.freebsd.org/cgi/url.cgi?ports/print/dvi2xx/pkg-descr)。 安装这个 port 就会得到我们需要的程序， `dvilj2p` ， 它可以将 DVI 数据转换成 LaserJet IIp， LaserJet III， 和 LaserJet 2000 兼容的数据。

`dvilj2p` 工具使得过滤器 `hpdf` 变得十分复杂， 因为 `dvilj2p` 不能读取标准输入。 它需要从文件中读取数据。 更糟糕的是， 这个文件的名字必须以 `.dvi` 结尾。 所以使用 `/dev/fd/0` 作为标准输入是有问题的。 我们可以通过连接 (符号连接) 来解决这个问题。 连接一个临时的文件名 (一个以 `.dvi` 结尾的文件名) 到 `/dev/fd/0`， 从而强制 `dvilj2p` 从标准输入读取。

现在迎面而来的是另外一个问题， 我们不能使用 `/tmp` 存放临时连接。 符号连接是被用户和组 `bin` 拥有的。 而过滤器则是以 `daemon` 用户运行的。 并且 `/tmp` 目录设置了 sticky 位。 所以过滤器只能建立符号连接， 但它不能在用完之后清除掉这些连接。 因为它们属于不同的用户。

所以过滤器将在当前工作目录下建立符号连接， 即后台打印队列目录 (用变量 `sd` 在 `/etc/printcap` 中指定)。 这是一个非常好的让过滤器完成它工作的地方， 特别还是因为 (有时) 这个目录比起 `/tmp` 来有更多的可用磁盘空间。

最后， 给出过滤器的代码：

```
#!/bin/sh
#
#  hpdf - Print DVI data on HP/PCL printer
#  Installed in /usr/local/libexec/hpdf

PATH=/usr/local/bin:$PATH; export PATH

#
#  Define a function to clean up our temporary files.  These exist
#  in the current directory, which will be the spooling directory
#  for the printer.
#
cleanup() {
   rm -f hpdf$$.dvi
}

#
#  Define a function to handle fatal errors: print the given message
#  and exit 2\.  Exiting with 2 tells LPD to do not try to reprint the
#  job.
#
fatal() {
    echo "$@" 1>&2
    cleanup
    exit 2
}

#
#  If user removes the job, LPD will send SIGINT, so trap SIGINT
#  (and a few other signals) to clean up after ourselves.
#
trap cleanup 1 2 15

#
#  Make sure we are not colliding with any existing files.
#
cleanup

#
#  Link the DVI input file to standard input (the file to print).
#
ln -s /dev/fd/0 hpdf$$.dvi || fatal "Cannot symlink /dev/fd/0"

#
#  Make LF = CR+LF
#
printf "\033&k2G" || fatal "Cannot initialize printer"

#
#  Convert and print.  Return value from dvilj2p does not seem to be
#  reliable, so we ignore it.
#
dvilj2p -M1 -q -e- dfhp$$.dvi

#
#  Clean up and exit
#
cleanup
exit 0
```

##### 10.4.1.4.5. 自动转换： 一种替代转换过滤器的方法

以上这些转换过滤器基本上建成了您的打印环境， 但也有不足就是必须由用户来指定 (在 [lpr(1)](http://www.FreeBSD.org/cgi/man.cgi?query=lpr&sektion=1&manpath=freebsd-release-ports) 命令行中) 要使用哪一个过滤器。 如果您的用户不是对计算机很在行， 那么选用过滤器将是一件麻烦的事情。 更糟的是， 当过滤器设定的不正确时， 过滤器被用在了不它对应类型的文件上， 打印机也许会喷出上百张纸。

比只安装转换过滤器更好的方法， 就是让文本过滤器 (因为它是默认的过滤器) 来检测要打印文件的类型， 然后自动运行正确的转换过滤器。 像 `file` 这样的工具可以给我们一定的帮助。 当然， 要区分开 *有些* 文件的类型还是有困难的 ── 但是， 当然， 您可以仅为它们提供转换过滤器。

FreeBSD 的 Ports 套件提供了一个可以自动进行转换的文本过滤器， 名字叫做 `apsfilter` ([print/apsfilter](http://www.freebsd.org/cgi/url.cgi?ports/print/apsfilter/pkg-descr))。 它可以检测纯文本、 PostScript®、 DVI 以及几乎任何格式的文件， 并在执行相应的转换之后完成打印工作。

#### 10.4.1.5. 输出过滤器

LPD 后台打印系统还支持一种我们还没有讨论过的过滤器： 输出过滤器。 输出过滤器只是用来打印纯文本的， 类似于文本过滤器， 但简化了许多地方。 如果您正在使用输出过滤器而不是文本过滤器， 那么：

*   LPD 为整个任务启动一个输出过滤器， 而不是为任务中的每个文件都启动一次。

*   LPD 不会提供任务中文件开始和结束的信息给输出过滤器。

*   LPD 不会提供用户名或者主机名给过滤器， 所以它是无法做打印记帐的。 事实上它只有两个参数：

    `过滤器-名字` -w*`宽度`* -l*`长度`*

    *`宽度`* 来自于 `pw` 变量， 而 *`length`* 来自于 `pl` 变量， 这些值都是实际问题中给打印机设置的。

不要让输出过滤器的简化所耽误。 如果想要输出过滤器完成让任务中的每个文件都重新开始一页打印是 *不可能* 的。 请使用文本过滤器 (也叫输入过滤器)； 详见 安装文本过滤器。 此外， 实际上， 输出过滤器 *更复杂* ， 它要检查发给它的字节流中是否有特殊的标志字符， 并且给自己发送信号来代替 LPD 的。

可是， 如果打算要报头页或者需要发送控制字符或者其他的初始化字符串来完成打印报头页， 那么输出过滤器则是 *必需的*。 (但是它也是 *无用的* 如果打算对打印的用户计费， 因为 LPD 不会给输出过滤器任何用户或者主机的信息。)

在一台单个的打印机上， LPD 同时允许输出过滤器、 文本过滤器和其他的过滤器。 在某些情况下， LPD 将仅会启动输出过滤器来打印报头页 (详见 报头页)。 然后 LPD 会要求输出过滤器 *自己停止运行* ， 它发送给过滤器两个字节： ASCII 031 跟着一个 ASCII 001。 当输出过滤器看见这两个字节 (031, 001)， 它应该通过发送 `SIGSTOP` 信号来停止自己的运行。 当 LPD 已经运行好了其他的过滤器， 它会通过给输出过滤器发送 `SIGCONT` 信号来让输出过滤器重新运行。

如果仅有一个输出过滤器而 *没有* 文本过滤器， 并且 LPD 正在处理一个纯文本任务， LPD 会使用输出过滤器来完成这个任务。 像以前运行一样， 输出过滤器会按顺序打印任务中的文件， 而不会插入送纸或其他进纸的命令， 但这也许并 *不是* 您想要的结果。 在大多数情况下， 您还是需要一个文本过滤器。

`lpf` 这个我们前面介绍过的文本过滤器程序， 也可以用来做输出过滤器。 如果需要使用快速且混乱的输出过滤器， 但又不想写字节检测和信号发送代码， 那么试试 `lpf`。 `lpf` 也可以包含在一个 shell 脚本中来处理任何打印机可能需要的初始化代码。

#### 10.4.1.6. `lpf`： 一个文本过滤器

`/usr/libexec/lpr/lpf` 这个程序包含在 FreeBSD 的二进制程序中， 它是一个文本过滤器 (输入过滤器)。 它可以缩排输出 (用 `lpr -i` 命令提交的任务)， 可以打印控制字符禁止断页用 `lpr -l` 提交的任务)， 可以调整任务中退格和制表符打印的位置， 还可以对打印进行记帐。 它同样可以像输出过滤器一样工作。

`lpf` 适用于很多打印环境。 尽管它本身没有向打印机发送初始化代码的功能， 但写一个 shell 脚本来完成所需的初始化并执行 `lpf` 是很容易的。

为了让 `lpf` 可以正确的进行打印记帐， 那么需要 `/etc/printcap` 中的 `pw` 和 `pl` 变量都填入正确的值。 它用这些值来测定一页能打印多少文本， 并计算出任务有多少页。 想得到更多关于打印记帐的信息， 请参见 对打印机使用进行记帐。

### 10.4.2. 报头页

如果您有 *很多* 用户， 他们正在使用各式各样的打印机， 那么您或许要考虑一下把 *报头页* 当作无可避免之灾祸了。

报头页， 也叫 *banner* 或者 *burst 页*， 可以用来辨别打印出的文件是谁打印的。 它们通常用大号的粗体字母打印出来， 也可能用装饰线围绕四周， 所以在一堆打印出的文件中， 突出的显示了这个文件属于哪个用户的哪个任务。 这可以让用户快速的找到他们的任务。 而报头页一个明显的缺点就是， 在每个任务中都要有一张或者几张纸作为报头页印出来， 可是它们的有用的地方只发挥几分钟的作用， 最后它们会被放进回收站或者扔进垃圾堆。 (注意报头页只是一个任务一个， 而不是任务中的每个文件都有一个， 所以可能对纸张还不算很浪费。)

LPD 系统可以自动为您的打印提供报头页， *如果* 您的打印机可以直接打印纯文本。 如果您的打印机是一台 PostScript® 打印机， 您将需要一个外部的程序来生成报头页； 详见 在 PostScript® 打印机上打印报头页。

#### 10.4.2.1. 打开报头页

在 简单打印设置 这节， 我们通过在 `/etc/printcap` 文件中指定 `sh` (“禁止报头页”) 来把报头页功能关掉了。 要重新为打印机开启报头页功能， 只需要删除掉 `sh`。

听起来很容易， 不是么？

是的。 您 *可能* 不得不让输出过滤器来给打印机发送初始化字符串。 下面是一个用在 Hewlett Packard PCL-兼容打印机上的输出过滤器的例子：

```
#!/bin/sh
#
#  hpof - Output filter for Hewlett Packard PCL-compatible printers
#  Installed in /usr/local/libexec/hpof

printf "\033&k2G" || exit 2
exec /usr/libexec/lpr/lpf
```

用 `of` 变量指定输出过滤器的路径。 参见 输出过滤器 这一节来得到更多信息。

下面是一个为我们以前介绍的叫做 `teak` 的打印机配置的 `/etc/printcap` 文件； 在配置当中我们开启了报头页并且加入了上述的打印过滤器：

```
#
#  /etc/printcap for host orchid
#
teak|hp|laserjet|Hewlett Packard LaserJet 3Si:\
        :lp=/dev/lpt0:sd=/var/spool/lpd/teak:mx#0:\
        :if=/usr/local/libexec/hpif:\
        :vf=/usr/local/libexec/hpvf:\
        :of=/usr/local/libexec/hpof:
```

现在， 当用户再发任务给打印机 `teak` 的时候， 每个任务都会有一个报头页。 如果用户想要花时间来寻找他们自己打印的文件， 那么他们可以通过 `lpr -h` 命令来提交任务； 参考 报头页选项 这一节来得到更多关于 [lpr(1)](http://www.FreeBSD.org/cgi/man.cgi?query=lpr&sektion=1&manpath=freebsd-release-ports) 的选项。

### 注意:

LPD 在报头页之后发出一个换纸字符。 如果您的打印机使用一个不同的字符或者字符串当作退纸指令， 在 `/etc/printcap` 中用 `ff` 变量指定即可。

#### 10.4.2.2. 控制报头页

通过启用报头页， LPD 将生成出一个 *长报头*， 一整页的大字母， 标着用户， 主机和任务名。 下面是一个例子 (`kelly` 从主机 `rose` 打印了一个叫做 “outline” 的任务)：

```
      k                   ll       ll
      k                    l        l
      k                    l        l
      k   k     eeee       l        l     y    y
      k  k     e    e      l        l     y    y
      k k      eeeeee      l        l     y    y
      kk k     e           l        l     y    y
      k   k    e    e      l        l     y   yy
      k    k    eeee      lll      lll     yyy y
                                               y
                                          y    y
                                           yyyy

                                   ll
                          t         l        i
                          t         l
       oooo    u    u   ttttt       l       ii     n nnn     eeee
      o    o   u    u     t         l        i     nn   n   e    e
      o    o   u    u     t         l        i     n    n   eeeeee
      o    o   u    u     t         l        i     n    n   e
      o    o   u   uu     t  t      l        i     n    n   e    e
       oooo     uuu u      tt      lll      iii    n    n    eeee

      r rrr     oooo     ssss     eeee
      rr   r   o    o   s    s   e    e
      r        o    o    ss      eeeeee
      r        o    o      ss    e
      r        o    o   s    s   e    e
      r         oooo     ssss     eeee

                                              Job:  outline
                                              Date: Sun Sep 17 11:04:58 1995
```

LPD 会附加一个换页符在这段文本之后， 所以任务会在新的一页上开始 (除非设置了 `sf` (禁止换纸) 在 `/etc/printcap` 文件里目标打印机的记录中)。

如果您喜欢， LPD 可以生成一个 *短报头*； 指定 `sb` (短 banner) 在文件 `/etc/printcap` 中。 报头页就会看起来像下面这样：

```
rose:kelly  Job: outline  Date: Sun Sep 17 11:07:51 1995
```

同样是默认的， LPD 也是先打印报头页， 然后才是任务。 要想反过来， 在 `/etc/printcap` 中指定 `hl` (最后报头)。

#### 10.4.2.3. 为带报头页的任务记帐

使用 LPD 内置的报头页会在进行打印记帐的时候产生一种特殊情况： 报头页肯定是 *免费* 的。

为什么？

因为输出过滤器是仅有的一个在打印报头页时能进行记帐的外部程序， 但却没有提供给它任何 *用户或者主机* 的信息或者记帐文件， 所以它无法知道谁应该为打印机的使用付费。 如果仅仅是 “增加一页计数” 给文本过滤器或者其他过滤器 (它们有用户和主机的信息) 是不够的， 因为用户可以用 `lpr -h` 命令跳过报头页。 他还是需要为自己并没有打印的报头页付钱。 基本上， `lpr -h` 是明知用户的首选， 但也不能强制让别人使用它。

让每个过滤器生成自己的报头页 (因此可以为它们计费) 是 *仍然不够的*。 如果用户想要用 `lpr -h` 命令禁止报头页， 它们将仍然印出报头页并且为它们付费。 因为 LPD 不会把 `-h` 这个参数传给任何过滤器。

这样， 您该怎么办呢？

您可以：

*   认可 LPD 的这个问题， 并且免费提供报头页打印。

*   安装一个替代 LPD 的软件， 比如 LPRng。 参考 替换标准的后台打印软件 来得到更多关于可以替代 LPD 的软件的信息。

*   写一个 *聪明的* 输出过滤器。 通常， 输出过滤器不应该去完成除了初始化打印机或者进行一些简单字符转换以外的任何事情。 它适合完成报头页和纯文本任务 (当没有文本 (输入) 过滤器时)。 但是， 如果有文本过滤器为纯文本任务服务， 那么 LPD 将仅为打印报头页启动输出过滤器。 而且， 这个输出过滤器可以理解报头页里 LPD 生成的信息， 然后决定哪位用户和主机应该为报头页付费。 这种方法仅有的问题是输出过滤器仍然不知道应该使用什么记帐文件 (`af` 变量的内容并没有被传递过来)， 但是如果您有一个众所周知的记帐文件， 就可以直接把文件名写进输出过滤器。 为了简化解释报头的步骤， 我们定义 `sh` (短报头) 变量在 `/etc/printcap` 文件中。 但这些还是太麻烦了， 而且用户也更喜欢让他们免费打印报头页的慷慨的系统管理员。

#### 10.4.2.4. 在 PostScript® 打印机上打印报头页

像上面描述的那样，LPD 可以生成一个纯文本的报头页来适应多种打印机。 当然， PostScript® 不能直接打印纯文本， 所以 LPD 没什么用──或者说大多时候是这样。

一个显而易见的方法来得到报头页就是让每个转换过滤器和文本过滤器都来生成报头页。 这些过滤器应该用用户名和主机的参数来生成一个相对应的报头页。 这种方法的缺点就是用户总是打印出报头页， 无论他们是否用 `lpr -h` 命令来提交的任务。

让我们来深入深入的研究一下这个方法。 下面的脚本输入三个参数 (用户登录名， 主机名， 和任务名) 然后生成一个简单的 PostScript® 报头页：

```
#!/bin/sh
#
#  make-ps-header - make a PostScript header page on stdout
#  Installed in /usr/local/libexec/make-ps-header
#

#
#  These are PostScript units (72 to the inch).  Modify for A4 or
#  whatever size paper you are using:
#
page_width=612
page_height=792
border=72

#
#  Check arguments
#
if [ $# -ne 3 ]; then
    echo "Usage: `basename $0` <user> <host> <job>" 1>&2
    exit 1
fi

#
#  Save these, mostly for readability in the PostScript, below.
#
user=$1
host=$2
job=$3
date=`date`

#
#  Send the PostScript code to stdout.
#
exec cat <<EOF
%!PS

%
%  Make sure we do not interfere with user's job that will follow
%
save

%
%  Make a thick, unpleasant border around the edge of the paper.
%
$border $border moveto
$page_width $border 2 mul sub 0 rlineto
0 $page_height $border 2 mul sub rlineto
currentscreen 3 -1 roll pop 100 3 1 roll setscreen
$border 2 mul $page_width sub 0 rlineto closepath
0.8 setgray 10 setlinewidth stroke 0 setgray

%
%  Display user's login name, nice and large and prominent
%
/Helvetica-Bold findfont 64 scalefont setfont
$page_width ($user) stringwidth pop sub 2 div $page_height 200 sub moveto
($user) show

%
%  Now show the boring particulars
%
/Helvetica findfont 14 scalefont setfont
/y 200 def
[ (Job:) (Host:) (Date:) ] {
200 y moveto show /y y 18 sub def }
forall

/Helvetica-Bold findfont 14 scalefont setfont
/y 200 def
[ ($job) ($host) ($date) ] {
        270 y moveto show /y y 18 sub def
} forall

%
% That is it
%
restore
showpage
EOF
```

现在， 每个转换过滤器和文本过滤器都能调用这段脚本来生成报头页， 然后打印用户的任务。 下面是我们早些时候在这个文档中提到的 DVI 转换过滤器， 被修改之后来生成一个报头页：

```
#!/bin/sh
#
#  psdf - DVI to PostScript printer filter
#  Installed in /usr/local/libexec/psdf
#
#  Invoked by lpd when user runs lpr -d
#

orig_args="$@"

fail() {
    echo "$@" 1>&2
    exit 2
}

while getopts "x:y:n:h:" option; do
    case $option in
        x|y)  ;; # Ignore
        n)    login=$OPTARG ;;
        h)    host=$OPTARG ;;
        *)    echo "LPD started `basename $0` wrong." 1>&2
              exit 2
              ;;
    esac
done

[ "$login" ] || fail "No login name"
[ "$host" ] || fail "No host name"

( /usr/local/libexec/make-ps-header $login $host "DVI File"
  /usr/local/bin/dvips -f ) | eval /usr/local/libexec/lprps $orig_args
```

过滤器是怎样解释参数列表来决定用户名和主机名的。 解释的方法对于其他转换过滤器来说也是一样的。 尽管文本过滤器需要输入的参数有些小的不同， (参见 过滤器是怎样工作的)。

像我们以前提到的那样， 上面的配置， 尽管相当简单， 关掉了 “禁止报头页” 的选项 (`-h` 选项) 在 `lpr` 中。 如果用户想要保护树木 (或者是几便士， 如果你对打印报头页收费的话)， 它还不能完成这件事情， 因为每个过滤器都要为每个任务打印一个报头页。

要允许用户对于每个任务都可以关闭报头页， 您需要使用在 为报头页记帐 这节中介绍的那种技巧： 写一个输出过滤器来解释 LPD- 生成的报头页并且生成一个 PostScript® 的版本。 如果用户用 `lpr -h` 命令提交任务， 那么 LPD 将不会生成报头页， 并且输出过滤器也不会生成报头页。 否则， 输出过滤器将从 LPD 读取文本， 然后发送适当的报头页的 PostScript® 编码给打印机。

如果您有的是一台连在串口上的 PostScript® 打印机， 您可以使用 `lprps` 里的一个输出过滤器， `psof` ， 它可以完成上述任务。 但注意 `psof` 不对报头页计费。

### 10.4.3. 网络打印

FreeBSD 支持网络打印： 发送任务给远程打印机。 网络打印通常指两种不同的方式：

*   访问一台连接在远程主机上的打印机。 在一台主机上安装一台常规的串口或并口打印机。 然后， 设置 LPD 来通过网络访问其他主机上的打印机。 具体见 安装在远程主机上的打印机 这节。

*   访问一台直接连接在网络上的打印机。 打印机另有一个网络接口 (或者替代常规的串口或者并口)。 这样的打印机可能像下面这样工作：

    *   它或许可以理解 LPD 的协议， 并且甚至可以接收远程主机发来的任务排进队列。 这样， 它就像一个普通的主机运行着 LPD 一样。 做在 安装在远程主机上的打印机 里介绍的步骤， 可以设置好这样的打印机。

    *   它或许支持网络数据流。 这样， 把打印机 “接” 在一台网络上的主机上， 由这台主机负责安排任务并发送任务到打印机。 参见 带网络数据流接口的打印机 这节来得到更多安装这类打印机的建议。

#### 10.4.3.1. 安装在远程主机上的打印机

LPD 后台打印系统内建了对给其他也运行着 LPD (或者是与 LPD 兼容的) 的主机发送任务的功能。 这个功能使您可以在一台主机上安装打印机， 并让它可以在其他主机上访问。 这个功能同样适用在那些有网络接口并且可以理解 LPD 协议的打印机上。

要开启这种远程打印的功能， 首先在一台主机上安装打印机， 就是 *打印服务器*， 可以使用在 简单打印机设置 这节中简单设置的方法。 高级的设置可以参考 高级打印机设置 这节中你需要的部分。 一定要测试一下打印机， 看看它是不是所有您开启的 LPD 的功能都正常工作。 此外还需要确认 *本地主机* 允许使用 *远程主机* 上的 LPD 服务 (参见 限制远程主打印任务)。

如果您正在使用一台带网络接口并与 LPD 兼容的打印机， 那么我们那下面讨论中的 *打印服务器* 就是打印机本身， 而 *打印机名* 就是您为打印机配置的名字。 参考随打印机和/或者打印机-网络接口供给的文档。

### 提示:

如果您正使用惠普的 Laserjet， 则打印机名 `text` 将自动地为您完成 LF 到 CRLF 的转换， 因而也就不需要 `hpif` 脚本了。

然后， 在另外一台你想要访问打印机的主机上的 `/etc/printcap` 文件中加入它们的记录， 像下面这样：

1.  可以随意给这个记录起名字。 简单起见， 您可以给打印服务器使用相同的名字或者别名。

2.  保留 `lp` 变量为空， (`:lp=:`)。

3.  建立一个后台打印队列目录， 并用 `sd` 变量指明其位置。 LPD 将把任务提交给打印服务器之前， 会把这些任务保存在这里。

4.  在 `rm` 变量中放入打印服务器的名字。

5.  在 `rp` 中放入打印服务器上打印机的名字。

就是这样。 不需要列出转换过滤器， 页面大小， 或者其他的一些东西在 `/etc/printcap` 文件中。

这有一个例子。 主机 `rose` 有两台打印机， `bamboo` 和 `rattan`。 我们要让主机 `orchid` 的用户可以使用这两台打印机。 下面是 `/etc/printcap` 文件， 用在主机 `orchid` (详见 开启报头页) 上的。 文件中已经有了打印机 `teak` 的记录； 我们在主机 `rose` 上增加了两台打印机：

```
#
#  /etc/printcap for host orchid - added (remote) printers on rose
#

#
#  teak is local; it is connected directly to orchid:
#
teak|hp|laserjet|Hewlett Packard LaserJet 3Si:\
        :lp=/dev/lpt0:sd=/var/spool/lpd/teak:mx#0:\
        :if=/usr/local/libexec/ifhp:\
        :vf=/usr/local/libexec/vfhp:\
        :of=/usr/local/libexec/ofhp:

#
#  rattan is connected to rose; send jobs for rattan to rose:
#
rattan|line|diablo|lp|Diablo 630 Line Printer:\
        :lp=:rm=rose:rp=rattan:sd=/var/spool/lpd/rattan:

#
#  bamboo is connected to rose as well:
#
bamboo|ps|PS|S|panasonic|Panasonic KX-P4455 PostScript v51.4:\
        :lp=:rm=rose:rp=bamboo:sd=/var/spool/lpd/bamboo:
```

然后， 我们只需要在主机 `orchid` 上建立一个后台打印队列目录：

```
# `mkdir -p /var/spool/lpd/rattan /var/spool/lpd/bamboo`
# `chmod 770 /var/spool/lpd/rattan /var/spool/lpd/bamboo`
# `chown daemon:daemon /var/spool/lpd/rattan /var/spool/lpd/bamboo`
```

现在， 主机 `orchid` 上的用户可以打印到 `rattan` 和 `bamboo` 了。 如果， 比如， 一个用户在主机 `orchid` 上输入了：

```
% `lpr -P bamboo -d sushi-review.dvi`
```

LPD 系统在主机 `orchid` 上会复制这个任务到后台打印队列目录 `/var/spool/lpd/bamboo` 并且记下这是一个 DVI 任务。 当主机 `rose` 上的打印机 `bamboo` 的后台打印队列目录有空间的时， 这两个 LPD 系统将会传输这个文件到主机 `rose` 上。 文件将排在主机 `rose` 的队列中直到最终被打印出来。 它将被从 DVI 转换成 PostScript® (因为 `bamboo` 是一台 PostScript® 打印机) 在主机 `rose`。

#### 10.4.3.2. 带有网络数据流接口的打印机

通常， 当您为打印机购买了一块网卡， 可以得到两个版本： 一个是模拟后台打印 (贵一些的版本)， 或者一个只发送数据给打印机就像在使用串口或者并口一样 (便宜一些的版本)。 这节讲述如何使用这个便宜一些的版本。 要得到贵一些版本的更多信息， 参见前面章节 安装在远程主机上的打印机。

`/etc/printcap` 文件的格式让您指定使用哪个串口或并口， 并且还要指定 (如果您正在使用串口)， 使用多快的波特， 是否使用流量控制， 为制表符延迟， 转换换行， 等等。 但是没有一种方法指定一个连接到一台正在监听 TCP/IP 的或者其他网络接口的打印机。

要发送数据到网络打印机， 就需要开发一个通讯程序， 它可以被文本或者转换过滤器调用。 下面是一些例子： 脚本 `netprint` 将标准输入的所有数据发送到一个连在网络上的打印机。 我们将打印机的名字作为第一个参数， 端口号跟在后面作为第二个参数， 传给 `netprint`。 注意它只支持单向通讯 (FreeBSD 到打印机)； 很多网络打印机支持双向通讯， 并且这是您可能利用到的 (得到打印机状态， 进行打印记帐， 等等的时候。)。

```
#!/usr/bin/perl
#
#  netprint - Text filter for printer attached to network
#  Installed in /usr/local/libexec/netprint
#
$#ARGV eq 1 || die "Usage: $0 <printer-hostname> <port-number>";

$printer_host = $ARGV[0];
$printer_port = $ARGV[1];

require 'sys/socket.ph';

($ignore, $ignore, $protocol) = getprotobyname('tcp');
($ignore, $ignore, $ignore, $ignore, $address)
    = gethostbyname($printer_host);

$sockaddr = pack('S n a4 x8', &AF_INET, $printer_port, $address);

socket(PRINTER, &PF_INET, &SOCK_STREAM, $protocol)
    || die "Can't create TCP/IP stream socket: $!";
connect(PRINTER, $sockaddr) || die "Can't contact $printer_host: $!";
while (<STDIN>) { print PRINTER; }
exit 0;
```

然后我们就可以在多种过滤器里使用这个脚本了。 加入我们有一台 Diablo 750-N 行式打印机联在网络上。 打印机在 5100 端口上接收要打印的数据。 打印机的主机名是 `scrivener`。 这里是为这个打印机写的文本过滤器：

```
#!/bin/sh
#
#  diablo-if-net - Text filter for Diablo printer `scrivener' listening
#  on port 5100\.   Installed in /usr/local/libexec/diablo-if-net
#
exec /usr/libexec/lpr/lpf "$@" | /usr/local/libexec/netprint scrivener 5100
```

### 10.4.4. 限制打印机的使用

这节将讲述关于限制打印机使用的问题。 LPD 系统让您可以控制谁可以访问打印机， 无论本地或是远程的， 是否他们可以打印机多份副本， 任务可以有多大， 以及打印队列的尺寸等。

#### 10.4.4.1. 限制多份副本

LPD 系统能够简化用户在打印多份副本时的工作。 用户可以用 `lpr -#5` (举例) 来提交打印任务， 则会将任务中每个文件都打印五份副本。 这是不是一件很棒的事情呢。

如果您感觉多份副本会对打印机造成不必要的磨损和损耗， 您可以屏蔽掉 [lpr(1)](http://www.FreeBSD.org/cgi/man.cgi?query=lpr&sektion=1&manpath=freebsd-release-ports) 的 `-#` 选项， 这可以通过在 `/etc/printcap` 文件中增加 `sc` 变量来完成。 当用户用 `-#` 选项提交任务时， 他们将看到：

```
lpr: multiple copies are not allowed
```

注意当为一台远程打印机进行设置时 (参见 安装在远程主机上的打印机 这一节) 您还需要同时在远程主机的 `/etc/printcap` 文件中 增加`sc` 变量， 否则用户还是可以从其他主机上提交使用多份副本的任务。

下面是一个例子。 这个是 `/etc/printcap` 文件在主机 `rose` 上。 打印机 `rattan` 非常轻闲， 所以我们将允许多份副本， 但是激光打印机 `bamboo` 则有些忙， 所以我们禁止多份副本， 通过增加 `sc` 变量：

```
#
#  /etc/printcap for host rose - restrict multiple copies on bamboo
#
rattan|line|diablo|lp|Diablo 630 Line Printer:\
        :sh:sd=/var/spool/lpd/rattan:\
        :lp=/dev/lpt0:\
        :if=/usr/local/libexec/if-simple:

bamboo|ps|PS|S|panasonic|Panasonic KX-P4455 PostScript v51.4:\
        :sh:sd=/var/spool/lpd/bamboo:sc:\
        :lp=/dev/ttyu5:ms#-parenb cs8 clocal crtscts:rw:\
        :if=/usr/local/libexec/psif:\
        :df=/usr/local/libexec/psdf:
```

现在， 我们还需要增机 `sc` 变量在主机 `orchid` 的 `/etc/printcap` 文件中 (顺便我们也禁止打印机 `teak` 多份打印) ：

```
#
#  /etc/printcap for host orchid - no multiple copies for local
#  printer teak or remote printer bamboo
teak|hp|laserjet|Hewlett Packard LaserJet 3Si:\
        :lp=/dev/lpt0:sd=/var/spool/lpd/teak:mx#0:sc:\
        :if=/usr/local/libexec/ifhp:\
        :vf=/usr/local/libexec/vfhp:\
        :of=/usr/local/libexec/ofhp:

rattan|line|diablo|lp|Diablo 630 Line Printer:\
        :lp=:rm=rose:rp=rattan:sd=/var/spool/lpd/rattan:

bamboo|ps|PS|S|panasonic|Panasonic KX-P4455 PostScript v51.4:\
        :lp=:rm=rose:rp=bamboo:sd=/var/spool/lpd/bamboo:sc:
```

通过使用 `sc` 变量， 我们阻止了 `lpr -#` 命令的使用， 但仍然没有禁止用户多次运行 [lpr(1)](http://www.FreeBSD.org/cgi/man.cgi?query=lpr&sektion=1&manpath=freebsd-release-ports) ， 或者多次提交任务中同样的文件， 像下面这样：

```
% `lpr forsale.sign forsale.sign forsale.sign forsale.sign forsale.sign`
```

这里有很多种方法可以阻止这种行为 (包括忽略它)， 并且是免费的。

#### 10.4.4.2. 限制对打印机的访问

您可以控制谁可以打印到哪台打印机通过 UNIX® 的组机制和文件 `/etc/printcap` 中的 `rg` 变量。 只要把可以访问打印机的用户放进适当的组中， 然后在 `rg` 变量中写上组的名字。

如果这组以外的用户 (包括 `root`) 试图打印到被限制的打印机，将会得到这样的提示：

```
lpr: Not a member of the restricted group
```

像使用 `sc` (禁止多份副本) 变量一样， 您需要指定 `rg` 在远程同样对打印机有访问限制的主机上， 如果您感觉合适的话 (参考 安装在远程主机上的打印机 这一节)。

比如， 我们将让任何人都可以访问打印机 `rattan`， 但只有在 `artists` 组中的人可以使用打印机 `bamboo`。 这里是类似的主机 `rose` 上的 `/etc/printcap` 文件：

```
#
#  /etc/printcap for host rose - restricted group for bamboo
#
rattan|line|diablo|lp|Diablo 630 Line Printer:\
        :sh:sd=/var/spool/lpd/rattan:\
        :lp=/dev/lpt0:\
        :if=/usr/local/libexec/if-simple:

bamboo|ps|PS|S|panasonic|Panasonic KX-P4455 PostScript v51.4:\
        :sh:sd=/var/spool/lpd/bamboo:sc:rg=artists:\
        :lp=/dev/ttyu5:ms#-parenb cs8 clocal crtscts:rw:\
        :if=/usr/local/libexec/psif:\
        :df=/usr/local/libexec/psdf:
```

Let us leave the other example `/etc/printcap` file (for the host `orchid`) alone. Of course, anyone on `orchid` can print to `bamboo`. It might be the case that we only allow certain logins on `orchid` anyway, and want them to have access to the printer. Or not.

### 注意:

这里每台仅能有一个限制的组。

#### 10.4.4.3. 控制提交的任务大小

如果您有很多用户访问打印机， 可能需要对用户可以提交的文件尺寸设置一个上限。 毕竟， 文件系统中后台打印队列目录的空间是有限的， 您需要保证这里有空间来存放其他用户的任务。

LPD 允许通过使用 `mx` 变量来限制任务中文件的最大字节数， 方法是指定单位为块的 `BUFSIZ` 数， 每块表示 1024 字节。 如果在这个变量的值是 0， 则表示不进行限制； 不过， 如果不指定 `mx` 变量的话， 则会使用默认值 1000 块。

### 注意:

这个限制是对于任务中 *文件* 的， 而 *不是* 任务总共的大小。

LPD 不会拒绝比限制大小大的文件。 但它是将限制大小以内的部分排入队列， 并且打印出来的只有这些。 剩下的部分将被丢弃。 这个行为是否正确还需讨论。

让我们来为例子打印机 `rattan` 和 `bamboo` 增加限制。 由于那些 `artists` 的 PostScript® 文件可能会很大， 我们将限制大小为 5 兆字节。 我们将不对纯文本行式打印机做限制：

```
#
#  /etc/printcap for host rose
#

#
#  No limit on job size:
#
rattan|line|diablo|lp|Diablo 630 Line Printer:\
        :sh:mx#0:sd=/var/spool/lpd/rattan:\
        :lp=/dev/lpt0:\
        :if=/usr/local/libexec/if-simple:

#
#  Limit of five megabytes:
#
bamboo|ps|PS|S|panasonic|Panasonic KX-P4455 PostScript v51.4:\
        :sh:sd=/var/spool/lpd/bamboo:sc:rg=artists:mx#5000:\
        :lp=/dev/ttyu5:ms#-parenb cs8 clocal crtscts:rw:\
        :if=/usr/local/libexec/psif:\
        :df=/usr/local/libexec/psdf:
```

同样， 限制只对本地用户起作用。 如果设置了允许远程用户使用您的打印机， 远程用户将不会受到这些限制。 您也需要指定 `mx` 变量在远程主机的 `/etc/printcap` 文件中。 参见 安装在远程主机上的打印机 这一节来得到更多有关远程打印的信息。

除此之外， 还有另一种限制远程任务大小的方法； 参见 限制远程主机打印任务。

#### 10.4.4.4. 限制远程主机打印任务

LPD 后台打印系统提供了多种方法来限制从远程主机提交的任务：

主机限制

您可以控制本地 LPD 接收哪台远程主机发来的请求， 通过 `/etc/hosts.equiv` 文件和 `/etc/hosts.lpd` 文件。 LPD 查看是否到来的任务请求来自被这两个文件中列出的主机。 如果没有， LPD 会拒绝这个请求。

这些文件的格式非常简单： 每行一个主机名。 注意 `/etc/hosts.equiv` 文件也被 [ruserok(3)](http://www.FreeBSD.org/cgi/man.cgi?query=ruserok&sektion=3&manpath=freebsd-release-ports) 协议使用， 并影响着 [rsh(1)](http://www.FreeBSD.org/cgi/man.cgi?query=rsh&sektion=1&manpath=freebsd-release-ports) and [rcp(1)](http://www.FreeBSD.org/cgi/man.cgi?query=rcp&sektion=1&manpath=freebsd-release-ports) 等程序， 所以要小心。

举个例子， 下面是 `/etc/hosts.lpd` 文件在主机 `rose` 上：

```
orchid
violet
madrigal.fishbaum.de
```

意思是主机 `rose` 将接收来自 `orchid`， `violet`， 和 `madrigal.fishbaum.de` 的请求。 如果任何其他的主机试图访问主机 `rose` 的 LPD， 任务将被拒绝。

大小限制

您可以控制后台打印队列目录需要保留多少空间。 建立一个叫做 `minfree` 的文件在后台打印队列目录下为本地打印机。 在这个文件中插入一个数字来代表多少磁盘块数 (512 字节) 的剩余空间来接收远程任务。

这让您可以保证远程用户不会填满您的文件系统。 您也可以用它来给本地用户一个优先： 他们可以在磁盘剩余空间低于 `minfree` 文件中的指定值后仍然可以提交任务。

比如， 让我们增加一个 `minfree` 文件为打印机 `bamboo`。 我们检查 `/etc/printcap` 文件来找到这个打印机的后台打印队列目录； 这里是打印机 `bamboo` 的记录：

```
bamboo|ps|PS|S|panasonic|Panasonic KX-P4455 PostScript v51.4:\
        :sh:sd=/var/spool/lpd/bamboo:sc:rg=artists:mx#5000:\
        :lp=/dev/ttyu5:ms#-parenb cs8 clocal crtscts:rw:mx#5000:\
        :if=/usr/local/libexec/psif:\
        :df=/usr/local/libexec/psdf:
```

后台打印队列目录在 `sd` 变量中给出。 我们设置 3 兆字节 (6144 磁盘块) 为文件系统上必须存在的总共剩余空间， 让 LPD 可以接受远程任务：

```
# `echo 6144 > /var/spool/lpd/bamboo/minfree` 
```

用户限制

您可以控制哪些远程用户可以打印到本地打印机， 通过指定 `rs` 变量在 `/etc/printcap` 文件中。 当 `rs` 出现在一个本地打印机的记录中时， LPD 将接收来自远程主机 *并* 在本地有同样登录名的用户提交的任务。 否则， LPD 会拒绝这个任务。

这个功能在一个 (比如) 有许多部门共享一个网络的环境中特别有用， 并且有些用户可以越过部门的边界。 通过为他们在您的系统上建立帐号， 他们可以他们自己的部门的系统里使用您的打印机。 如果 *只* 允许他们您的打印机， 而不是您的计算机资源， 您可以给他们 “象征” 帐户， 不带主目录并且设置一个没用的 shell ， 比如 `/usr/bin/false`。

### 10.4.5. 对打印机使用记帐

当然， 你需要对打印付费。 为什么不？ 纸张和墨水都需要花钱的。 并且这里还有维护的费用 ── 打印机是由很多部件组装成的， 并且零件会坏掉。 您可以检查您的打印机， 使用形式， 和维护费用来得出每页 (或者每尺， 每米， 或者每什么) 的费用。 现在， 您怎样启动打印记帐呢？

好了， 坏消息是 LPD 后台打印系统在这个部分没有提供很多帮助。 记帐是一个对使用的打印机的种类， 打印的格式， 和 *您的* 在对打印机的使用计费的需求依赖性很高的。

要实现记帐， 您必须更改打印机的文本过滤器 (对纯文本任务记费) 和转换过滤器 (对其他格式的文件计费)， 要统计页数或者查询打印了多少页的话。 您不可以通过使用简单的输出过滤器来逃脱计费， 因为它不能进行记帐。 参见 过滤器 这节。

通常， 有两种方法来进行记帐：

*   *定期记帐* 是更常用的方法， 可能因为它更简单。 无论合适何人打印一个任务， 过滤器都将记录用户名， 主机名， 和打印的页数到一个记帐文件。 每个月， 学期， 年， 或者任何您想设定的时间段， 收集这些不同打印机上的记帐文件， 按用户对打印的页数进行结算， 并对使用进行付费。 然后删掉所有记录文件， 开始一个新的计费周期。

*   *实时记帐* 不太常用， 可能因为它比较难。 这种方法让过滤器对用户的打印进行实时的记帐。 像磁盘配额， 记帐是实时的。 您可以组织用户打印当他们的帐户超额的时候， 并且可能提供一种方法让用户检查并调整他们的 “打印配额。” 但这个方法需要一些数据库代码来跟踪用户和他们的配额。

LPD 后台打印系统对两种方法都支持且很简单： 所以您需要提供过滤器 (大多数时候)， 还要提供记帐代码。 但这好的方面是： 您可以有非常灵活的记帐方法。 比如， 您可以选择使用阶段记帐还是实时记帐。 您可以选择记录哪些信息： 用户名， 主机名， 任务类型， 打印页数， 使用了多少平方尺的纸， 任务打印了多长时间， 等等。 您可以通过修改过滤器来存储这些信息。

#### 10.4.5.1. 快速并且混乱的打印记帐

FreeBSD 包含两个可以让您立刻可以建立起简单的阶段记帐的程序。 它们是文本过滤器 `lpf`， 在 lpf： 一个文本过滤器 这节中描述， 和 [pac(8)](http://www.FreeBSD.org/cgi/man.cgi?query=pac&sektion=8&manpath=freebsd-release-ports)， 一个收集并统计打印机记帐文件中记录的程序。

像在前面章节提到的过滤器一样 (过滤器)， LPD 启动文本或者转换过滤器并在过滤器命令行里带上记帐文件的名字。 过滤器可以使用这个参数知道该往哪写记帐记录。 这个文件的名字来自于 `af` 变量在 `/etc/printcap` 文件里， 并且如果没有指定绝对路径， 则默认是相对于后台打印队列目录的。

LPD 启动 `lpf` 带着页宽和页长的参数 (通过 `pw` 和 `pl` 变量)。 `lpf` 使用这些参数来判定将使用多少张纸。 在文件发送到打印机之后， 它就会在记帐文件中写入记录。 记录像下面这个样子：

```
2.00 rose:andy
3.00 rose:kelly
3.00 orchid:mary
5.00 orchid:mary
2.00 orchid:zhang
```

您应该让每个打印机都使用一个独立的记帐文件， 像 `lpf` 就没有内建文件锁逻辑， 这样两个 `lpf` 可能会发生彼此记录混合的情况， 如果它们同时要在同一个文件写入内容的时候。 一个最简单的保证每个打印机都使用一个独立的记帐文件的方法就是将 `af=acct` 写在 `/etc/printcap` 文件中。 然后， 每个打印机的记帐文件都会在这台打印机的后台打印队列目录中， 文件的名字叫做 `acct`。

当您准备对用户的打印进行收费时， 运行 [pac(8)](http://www.FreeBSD.org/cgi/man.cgi?query=pac&sektion=8&manpath=freebsd-release-ports) 程序。 只要转换到要收集信息的这台打印机的后台打印队列目录， 然后输入 `pac`。 您将会得到一个美元计费的摘要像下面这样：

```
  Login               pages/feet   runs    price
orchid:kelly                5.00    1   $  0.10
orchid:mary                31.00    3   $  0.62
orchid:zhang                9.00    1   $  0.18
rose:andy                   2.00    1   $  0.04
rose:kelly                177.00  104   $  3.54
rose:mary                  87.00   32   $  1.74
rose:root                  26.00   12   $  0.52

total                     337.00  154   $  6.74
```

这些是 [pac(8)](http://www.FreeBSD.org/cgi/man.cgi?query=pac&sektion=8&manpath=freebsd-release-ports) 需要的参数：

`-P*`打印机`*`

哪台 *`打印机`* 要结帐。 这个选项仅在用 `af` 变量在 `/etc/printcap` 文件中指定了绝对路径的情况下起作用。

`-c`

以金额来排序输出来代替以用户名字字母排序。

`-m`

忽略记帐文件中的主机名。 带上这个选项， 用户 `smith` 在主机 `alpha` 上与同样的用户 `smith` 在主机 `gamma` 上一样。 不带这个选项的话， 他们则是不同的用户。

`-p*`单价`*`

使用 *`price`* 作为每页或每尺美元的单价来替代 `pc` 变量指定的单价在 `/etc/printcap` 文件中， 或者两分 (默认)。 *`price`* 可以用一个浮点数来指定。

`-r`

反向排序。

`-s`

建立一个记帐摘要文件， 并且截短记帐文件。

*`名字`* *`…`*

只打印指定 *`名字`* 用户的记帐信息。

在 [pac(8)](http://www.FreeBSD.org/cgi/man.cgi?query=pac&sektion=8&manpath=freebsd-release-ports) 默认产生的摘要中， 可以看到在不同主机上的每个用户打印了多少页。 如果在您这里， 主机不考虑 (因为用户可以使用任何主机)， 运行 `pac -m`， 来得到下面的摘要：

```
  Login               pages/feet   runs    price
andy                        2.00    1   $  0.04
kelly                     182.00  105   $  3.64
mary                      118.00   35   $  2.36
root                       26.00   12   $  0.52
zhang                       9.00    1   $  0.18

total                     337.00  154   $  6.74
```

要以美元计算应付钱数， [pac(8)](http://www.FreeBSD.org/cgi/man.cgi?query=pac&sektion=8&manpath=freebsd-release-ports) 指定 `pc` 变量在 `/etc/printcap` 文件中 (默认是 200， 或者 2 分每页). 这个参数的单位是百分之一分， 在这个变量中指定每页或者每尺的价格。 您可以覆盖这个值当运行 [pac(8)](http://www.FreeBSD.org/cgi/man.cgi?query=pac&sektion=8&manpath=freebsd-release-ports) 带着参数 `-p` 的时候。 参数 `-p` 的单位是美元， 而不是百分之一分。 例如，

```
# `pac -p1.50`
```

设定每页的价格是 1 美元 5 美分。 您可以通过这个选项来达到目标利润。

最终， 运行 `pac -s` 将存储这些信息在一个记帐文件里， 文件名和打印机帐户的名字相同， 但是带着 `_sum` 的后缀。 然后截短记帐文件。 当您再次运行 [pac(8)](http://www.FreeBSD.org/cgi/man.cgi?query=pac&sektion=8&manpath=freebsd-release-ports) 的时候， 它再次读取记帐文件来得到初始的总计， 然后在记帐文件中增加信息。

#### 10.4.5.2. 怎样对打印的页数进行计数？

为了进行远程的精确记帐， 需要判断一个任务将会消耗多少张纸。 这是打印记帐问题的关键。

对于纯文本任务， 这个问题不是太难解决： 对任务中的行数进行计数然后与打印机支持的每页行数进行比较。 别忘了也对添印的行， 或者很长的逻辑上的一行但在打印机上会折成两行的这类进行记帐。

文本过滤器 `lpf` (在 lpf：一个文本过滤器 这节中介绍) 会在记帐时考虑这些问题。 如果正在编写一个可以进行记帐的文本过滤器， 您可能需要查看 `lpf` 的源代码。

怎样处理其他格式的文件？

好， 对于 DVI- 到 -LaserJet 或者 DVI- 到 -PostScript® 转换， 可以让您的过滤器输出诊断信息， 关于 `dvilj` 或者 `dvips` 命令， 并且看到多少页被转换了。 您也许可以对于其他类型的文件和转换程序进行类似操作。

但是这些方法的弱点就是事实上打印机并不是打印了所有的页。 比如， 卡纸， 缺墨， 或者炸掉了 ── 但用户还是要为没有打印的部分付钱。

您该怎样做？

只有一条 *肯定* 的方法来进行 *精确* 的记帐。 购买一台可以告诉您它使用了多少纸的打印机， 并且将它连接到串口或者网络上。 几乎所有 PostScript® 打印机都支持这个小功能。 其他制造厂或其他型号也可以有这个功能 (比如 Imagen 激光网络打印机)。 为这些打印机更改过滤器使它在打印完每个任务之后接收纸张用量， 并 *仅* 基于这个值进行记帐。 不需要计算行数， 也不需要容易出错的文件检查。

当然， 您也总是可以大方的使打印免费。

## 10.5. 使用打印机

这节将讲述如何使用在 FreeBSD 下设置好的打印机。 下面是一个用户级命令的总览：

[lpr(1)](http://www.FreeBSD.org/cgi/man.cgi?query=lpr&sektion=1&manpath=freebsd-release-ports)

打印任务

[lpq(1)](http://www.FreeBSD.org/cgi/man.cgi?query=lpq&sektion=1&manpath=freebsd-release-ports)

检查打印队列

[lprm(1)](http://www.FreeBSD.org/cgi/man.cgi?query=lprm&sektion=1&manpath=freebsd-release-ports)

从打印机的队列中移除任务

还有一个管理命令， [lpc(8)](http://www.FreeBSD.org/cgi/man.cgi?query=lpc&sektion=8&manpath=freebsd-release-ports)， 在 管理打印机 一节中有所介绍， 它可以用于控制打印机及其队列。

[lpr(1)](http://www.FreeBSD.org/cgi/man.cgi?query=lpr&sektion=1&manpath=freebsd-release-ports), [lprm(1)](http://www.FreeBSD.org/cgi/man.cgi?query=lprm&sektion=1&manpath=freebsd-release-ports), and [lpq(1)](http://www.FreeBSD.org/cgi/man.cgi?query=lpq&sektion=1&manpath=freebsd-release-ports) 这三个命令都接受 `-P *`printer-name`*` 选项来指定对哪个打印机 / 队列进行操作， 在 `/etc/printcap` 文件中列出的打印机。 这允许您提交， 删除， 并检查任务在多个打印机上。 如果您不使用 `-P` 选项， 那么这些命令会使用在 环境变量 `PRINTER` 中指定的打印机。 最终， 如果您也没有 `PRINTER` 这个环境变量， 这些命令的默认值是叫做 `lp` 的这台打印机。

从此以后， 术语 *默认打印机* 就是指 `PRINTER` 环境变量中指定的这台， 或者叫做 `lp` 的这一台当没有环境变量 `PRINTER` 的时候。

### 10.5.1. 打印任务

要打印文件， 输入：

```
% `lpr filename ...`
```

这个命令会打印所有列出的文件到默认打印机。 如果没有列出文件， [lpr(1)](http://www.FreeBSD.org/cgi/man.cgi?query=lpr&sektion=1&manpath=freebsd-release-ports) 会从标准输入读取打印数据。 比如， 这个命令打印一些重要的系统文件：

```
% `lpr /etc/host.conf /etc/hosts.equiv`
```

要选择一个指定的打印机， 输入：

```
% `lpr -P printer-name filename ...`
```

这个例子打印一个当前目录的长长的列表到叫做 `rattan` 的这台打印机：

```
% `ls -l | lpr -P rattan`
```

因为没有为 [lpr(1)](http://www.FreeBSD.org/cgi/man.cgi?query=lpr&sektion=1&manpath=freebsd-release-ports) 命令列出文件， `lpr` 从标准输入读入数据， 在这里是 `ls -l` 命令的输出。

[lpr(1)](http://www.FreeBSD.org/cgi/man.cgi?query=lpr&sektion=1&manpath=freebsd-release-ports) 命令同样可以接受多种控制格式的选项， 应用文件转换， 生成多份副本， 等等。 要得到更多信息， 参考 打印选项 这节。

### 10.5.2. 检查任务

当使用 [lpr(1)](http://www.FreeBSD.org/cgi/man.cgi?query=lpr&sektion=1&manpath=freebsd-release-ports) 进行打印时， 您希望打印的所有数据被放在一起打包成了一个 “打印任务”， 它被发送到 LPD 后台打印系统。 每台打印机都有一个任务队列， 并且您的任务在队列中等待其他用户的其他任务打印。 打印机按照先来先印的规则打印这些任务。

要显示默认打印机的队列， 输入 [lpq(1)](http://www.FreeBSD.org/cgi/man.cgi?query=lpq&sektion=1&manpath=freebsd-release-ports)。 要指定打印机， 使用 `-P` 选项。 例如， 命令

```
% `lpq -P bamboo`
```

会显示打印机 `bamboo` 的队列。 下面是命令 `lpq` 输出的一个例子：

```
bamboo is ready and printing
Rank   Owner    Job  Files                              Total Size
active kelly    9    /etc/host.conf, /etc/hosts.equiv   88 bytes
2nd    kelly    10   (standard input)                   1635 bytes
3rd    mary     11   ...                                78519 bytes
```

这里显示了队列中有三个任务在 `bamboo` 中。 第一个任务， 用户 kelly 提交的， 标识 “任务编号” 9。 每个要打印的任务都会获得一个不同的任务编号。 大多时候可以忽略这个任务编号， 但在您需要取消任务时会用到这个号码； 参考 移除任务 这节得到更多信息。

编号为 9 的任务包含了两个文件； 在 [lpr(1)](http://www.FreeBSD.org/cgi/man.cgi?query=lpr&sektion=1&manpath=freebsd-release-ports) 命令行中指定的多个文件被看作是一个单个的任务。 它是当前激活的任务 (注意这个词 `激活` 在 “Rank” 这列下面)， 意思是打印机当前正在打印那个任务。 第二个任务包含了标准输入传给 [lpr(1)](http://www.FreeBSD.org/cgi/man.cgi?query=lpr&sektion=1&manpath=freebsd-release-ports) 命令的数据。 第三个任务来自用户 `mary`; ， 它是一个比较大的任务。 她要打印的文件的路径名太长了， 所以 [lpq(1)](http://www.FreeBSD.org/cgi/man.cgi?query=lpq&sektion=1&manpath=freebsd-release-ports) 命令只显示了三个点。

[lpq(1)](http://www.FreeBSD.org/cgi/man.cgi?query=lpq&sektion=1&manpath=freebsd-release-ports) 输出的头一行也很有用： 它告诉我们打印机正在做什么 (或者至少是 LPD 认为打印机应该正在做的)。

[lpq(1)](http://www.FreeBSD.org/cgi/man.cgi?query=lpq&sektion=1&manpath=freebsd-release-ports) 命令同样支持 `-l` 选项来生成一个详细的长列表。 下面是一个 `lpq -l` 命令的例子：

```
waiting for bamboo to become ready (offline ?)
kelly: 1st				 [job 009rose]
       /etc/host.conf                    73 bytes
       /etc/hosts.equiv                  15 bytes

kelly: 2nd				 [job 010rose]
       (standard input)                  1635 bytes

mary: 3rd                                [job 011rose]
      /home/orchid/mary/research/venus/alpha-regio/mapping 78519 bytes
```

### 10.5.3. 移除任务

如果您对一个打印任务改变了主意， 可以用 [lprm(1)](http://www.FreeBSD.org/cgi/man.cgi?query=lprm&sektion=1&manpath=freebsd-release-ports) 将任务从队列中删除。 通常， 您甚至可以用 [lprm(1)](http://www.FreeBSD.org/cgi/man.cgi?query=lprm&sektion=1&manpath=freebsd-release-ports) 命令来移除一个当前激活的任务， 但是任务的一部分或者所有还是可能打印出来。

要从默认打印机中移除一个任务， 首先使用 [lpq(1)](http://www.FreeBSD.org/cgi/man.cgi?query=lpq&sektion=1&manpath=freebsd-release-ports) 找到任务编号。 然后输入：

```
% `lprm job-number`
```

要从指定打印机中删除任务， 增加 `-P` 选项。 下面的命令会删除编号为 10 的任务从 `bamboo` 这台打印机：

```
% `lprm -P bamboo 10`
```

[lprm(1)](http://www.FreeBSD.org/cgi/man.cgi?query=lprm&sektion=1&manpath=freebsd-release-ports) 命令有一些快捷方式：

lprm -

删除所有属于您的任务 (默认打印机的)。

lprm *`user`*

删除所有属于用户 *`user`* 的任务 (默认打印机的)。 超级用户可以删除用户的任务； 您只可以删除自己的任务。

lprm

命令行中不带任务编号， 任务名， 或者 `-` 选项， [lprm(1)](http://www.FreeBSD.org/cgi/man.cgi?query=lprm&sektion=1&manpath=freebsd-release-ports) 会删除默认打印机上当前激活的任务， 如果它属于你。 超级用户可以删除任务激活的任务。

使用参数 `-P` 和上面的快捷方式来用指定打印机替代默认打印机。 例如， 下面的命令会删除当前用户在打印机 `rattan` 队列中的所有任务：

```
% `lprm -P rattan -`
```

### 注意:

如果您正工作在一个网络环境中， [lprm(1)](http://www.FreeBSD.org/cgi/man.cgi?query=lprm&sektion=1&manpath=freebsd-release-ports) 将只允许在提交任务的主机上删除任务， 甚至是同一台打印机也可以在其他主机上使用时。 下面的命令证明了这个：

```
% `lpr -P rattan myfile`
% `rlogin orchid`
% `lpq -P rattan`
Rank   Owner	  Job  Files                          Total Size
active seeyan	  12	...                           49123 bytes
2nd    kelly      13   myfile                         12 bytes
% `lprm -P rattan 13`
rose: Permission denied
% `logout`
% `lprm -P rattan 13`
dfA013rose dequeued
cfA013rose dequeued

```

### 10.5.4. 超越纯文本：打印选项

[lpr(1)](http://www.FreeBSD.org/cgi/man.cgi?query=lpr&sektion=1&manpath=freebsd-release-ports) 支持许多控制文本格式的参数， 转换图形和其他格式文件， 生成多份副本， 处理任务， 等等。 这一节将描述这些选项。

#### 10.5.4.1. 格式与转换选项

下面的 [lpr(1)](http://www.FreeBSD.org/cgi/man.cgi?query=lpr&sektion=1&manpath=freebsd-release-ports) 参数控制任务中文件的格式。 使用这些参数， 如果任务不含纯文本， 或者您想让纯文本通过 [pr(1)](http://www.FreeBSD.org/cgi/man.cgi?query=pr&sektion=1&manpath=freebsd-release-ports) 格式化。

例如， 下面的命令打印一个 DVI 文件 (来自 TeX 排版系统) 文件名为 `fish-report.dvi` 到打印 `bamboo`：

```
% `lpr -P bamboo -d fish-report.dvi`
```

这些选项应用到任务中的每个文件， 所以您不能混合 (说) DVI 和 ditroff 文件在同一个任务中。 替代的方法是， 用独立的任务提交这些文件， 使用不同的转换选项给不同的任务。

### 注意:

所有这些选项除了 `-p` 和 `-T` 都需要转换过滤器安装给目标打印机。 例如， `-d` 选项需要 DVI 转换过滤器。 参考 转换过滤器 这节得到更多细节。

`-c`

打印 cifplot 文件。

`-d`

打印 DVI 文件。

`-f`

打印 FORTRAN 文本文件。

`-g`

打印 plot 数据。

`-i *`number`*`

缩进 *`number`* 列； 如果没有指定 *`number`*， 则缩进 8 列。 这个选项仅可以工作在某些过滤器上。

### 注意:

不要在选项 `-i` 和数字之间加入空格。

`-l`

打印文字数据， 包括控制字符。

`-n`

打印 ditroff (无设备依赖 troff) 数据。

-p

打印之前用 [pr(1)](http://www.FreeBSD.org/cgi/man.cgi?query=pr&sektion=1&manpath=freebsd-release-ports) 格式化纯文本。 参考 [pr(1)](http://www.FreeBSD.org/cgi/man.cgi?query=pr&sektion=1&manpath=freebsd-release-ports) 得到更多信息。

`-T *`title`*`

使用 *`title`* 在 [pr(1)](http://www.FreeBSD.org/cgi/man.cgi?query=pr&sektion=1&manpath=freebsd-release-ports) 上来替代文件名。 这个选项仅在使用 `-p` 选项时起作用。

`-t`

打印 troff 数据。

`-v`

打印 raster 数据。

下面是一个例子： 这个命令打印了一个很好的 [ls(1)](http://www.FreeBSD.org/cgi/man.cgi?query=ls&sektion=1&manpath=freebsd-release-ports) 联机手册到默认打印机：

```
% `zcat /usr/share/man/man1/ls.1.gz | troff -t -man | lpr -t`
```

[zcat(1)](http://www.FreeBSD.org/cgi/man.cgi?query=zcat&sektion=1&manpath=freebsd-release-ports) 命令解压缩 [ls(1)](http://www.FreeBSD.org/cgi/man.cgi?query=ls&sektion=1&manpath=freebsd-release-ports) 的手册并且将内容传给 [troff(1)](http://www.FreeBSD.org/cgi/man.cgi?query=troff&sektion=1&manpath=freebsd-release-ports) 命令， 它将格式化这些内容并且生成 GNU troff 输出给 [lpr(1)](http://www.FreeBSD.org/cgi/man.cgi?query=lpr&sektion=1&manpath=freebsd-release-ports) ， 它提交任务到 LPD 后台打印。 因为使用了 `-t` 选项为 [lpr(1)](http://www.FreeBSD.org/cgi/man.cgi?query=lpr&sektion=1&manpath=freebsd-release-ports) ， 后台打印将会转换 GNU troff 输出到默认打印机可以理解的格式当任务被打印时。

#### 10.5.4.2. 任务处理选项

下面的 [lpr(1)](http://www.FreeBSD.org/cgi/man.cgi?query=lpr&sektion=1&manpath=freebsd-release-ports) 选项告诉 LPD 对任务特殊处理：

-# *`copies`*

生成 *`copies`* 个副本给任务中的每个文件， 替代每个文件一份副本。 管理员可以禁止这个选项来减少打印机的浪费和鼓励复印机的使用。 参考 限制多份副本。

这个例子打印三份副本的文件 `parser.c` 跟着三份副本的文件 `parser.h` 到默认打印机：

```
% `lpr -#3 parser.c parser.h`
```

-m

打印完成后发信。 使用这个选项， LPD 系统将会发送邮件到您的帐户， 当它完成了处理您的任务后。 在信中， 它将会告诉您任务是否成功完成或者出现了错误， 并且 (通常) 指明是什么错误。

-s

不要复制文件到后台打印队列目录， 要使用符号连接。

如果您正在打印一个很大的任务， 您可能需要这个选项。 它节省后台打印队列目录的空间 (您的任务可能使后台打印队列目录所在的文件系统剩余空间超出)。 它同样也节省了时间， 因为 LPD 将不会副本任务的每个字节到后台打印队列目录。

这也有一个缺点： 因为 LPD 将直接指向源文件， 您不能修改或者删除它们直到它们被打印出来。

### 注意:

如果您打印到一台远程打印机， LPD 将最终将文件从本地主机副本到远程主机上， 所以选项 `-s` 只能节省本地后台打印队列目录的空间， 而不是远程的。 虽然如此， 但它还是很有用。

-r

移除任务中的文件在它们被复制到后台打印队列目录之后， 或者在用 `-s` 选项打印它们之后。 谨慎使用这个选项！

#### 10.5.4.3. 报头页选项

这些 [lpr(1)](http://www.FreeBSD.org/cgi/man.cgi?query=lpr&sektion=1&manpath=freebsd-release-ports) 的选项调整了通常出现在任务报头页上的文本。 如果报头页被跳过了在目标打印机上， 这些选项将不会起作用。 参考 报头页 得到更多关于设置报头页的信息。

-C *`text`*

替换报头页上的主机名为 *`text`*。 主机名通常都是提交任务的主机名称。

-J *`text`*

替换报头页上的任务名为 *`text`*。 任务名通常是任务中头一个文件的名字， 或者 `stdin` 如果您正在打印标准输入。

-h

不打印任何报头页。

### 注意:

在某些地点， 这个选项可能无效， 与报头页的产生方法有关。 参考 报头页 得到详细信息。

### 10.5.5. 管理打印机

作为一个打印机的管理者， 您必须要安装， 设置， 并且测试它们。 使用 [lpc(8)](http://www.FreeBSD.org/cgi/man.cgi?query=lpc&sektion=8&manpath=freebsd-release-ports) 命令， 您可以与打印机以更多的方式交流。 用 [lpc(8)](http://www.FreeBSD.org/cgi/man.cgi?query=lpc&sektion=8&manpath=freebsd-release-ports) ， 您可以

*   启动或停止打印机

*   启用或禁止它们的队列

*   重新安排每个队列中的任务。

首先， 一个关于术语的解释： 如果一个打印机被 *停止* 了， 它将不会打印它队列中的任何东西。 但用户还是可以提交任务， 它们会在队列中等待直到打印机被 *启动* 或者队列被清空。

如果一个队列被 *禁止*， 没有用户 (除了 `root`) 可以提交任务到打印机。 一个 *启用* 的队列允许任务被提交。 一个打印机可以被 *启动* 但它的队列被禁止， 在这种情况下打印机将打印队列中的任务， 直到队列为空。

通常， 您必须有 `root` 权限来使用 [lpc(8)](http://www.FreeBSD.org/cgi/man.cgi?query=lpc&sektion=8&manpath=freebsd-release-ports) 命令。 普通用户可以使用 [lpc(8)](http://www.FreeBSD.org/cgi/man.cgi?query=lpc&sektion=8&manpath=freebsd-release-ports) 命令来获得打印机状态并且重启一台挂了的打印机。

这里是一个关于 [lpc(8)](http://www.FreeBSD.org/cgi/man.cgi?query=lpc&sektion=8&manpath=freebsd-release-ports) 命令的摘要。 大部分命令带着一个 *`printer-name`* 参数来知道要对哪台打印机操作。 您可以用 `all` 填在 *`printer-name`* 的位置来代表所有在 `/etc/printcap` 文件中列出的打印机。

`abort printer-name`

取消当前任务并停止打印机。 用户仍然可以提交任务， 如果队列还是启用的。

`clean printer-name`

从打印机的后台打印队列目录移除旧的文件。 有时， 组成任务的文件没有被 LPD 正确的删除， 特别是在打印中出现错误或者管理活动比较多的时候。 这个命令查找不属于后台打印队列目录的文件并删除它们。

`disable printer-name`

禁止新任务入队。 如果打印机正在工作， 它将会继续打印队列中剩余的任务。 超级用户 (`root`) 总是可以提交任务， 甚至提交到一个禁止的队列。

这个命令在测试一台新打印机或者安装过滤器时非常有用： 禁止队列并提交以 `root` 提交任务。 其他用户将不能提交任务直到您完成了测试并用命令 `enable` 重新启用了队列的时候。

`down printer-name message`

打印机下线。 等于 `disable` 命令后跟一个 `stop` 命令。 *`message`* 将作为打印机状态， 当用户使用 [lpq(1)](http://www.FreeBSD.org/cgi/man.cgi?query=lpq&sektion=1&manpath=freebsd-release-ports) 或者 `lpc status` 命令查看打印机队列状态的时候显示出来。

`enable printer-name`

为打印机开启队列。 用户可以提交任务到打印机但是在打印机启动之前不会打印出任何东西。

`help command-name`

打印关于 *`command-name`* 命令的帮助。 不带 *`command-name`*， 则打印可用命令的摘要。

`restart printer-name`

启动打印机。 普通用户可以使用这个命令， 当一些特别的环境导致 LPD 锁死时， 但他们不能启用一台使用 `stop` 或者 `down` 命令停用的打印机。 `restart` 命令等同于 `abort` 后跟着一个 `start`。

`start printer-name`

启用打印机。 打印机将开始打印队列中的任务。

`stop printer-name`

停止打印机。 打印机将完成当前任务并且将不再打印队列中的任务任务。 尽管打印机被停用， 但用户仍然可以提交任务到一个开启的队列。

`topq printer-name job-or-username`

重新以 *`printer-name`* 安排队列， 通过将列出的 *`job`* 编号或者指定的所属 *`username`* 的任务放在队列的最前面。 对于这个命令， 您不可以使用 `all` 当作 *`printer-name`*。

`up printer-name`

打印机上线； 相对于 `down` 命令。 等同于 `start` 后跟着一个 `enable` 命令。

[lpc(8)](http://www.FreeBSD.org/cgi/man.cgi?query=lpc&sektion=8&manpath=freebsd-release-ports) 的命令行接受上面的命令。 如果您不输入任何命令， [lpc(8)](http://www.FreeBSD.org/cgi/man.cgi?query=lpc&sektion=8&manpath=freebsd-release-ports) 则进入一个交互模式， 在这里您可以输入命令直到输入 `exit`， `quit`， 或者文件结束符。

## 10.6. 替换标准后台打印

如果您已经通读过了这个手册， 那么到现在您应该已经了解了关于 FreeBSD 包含的后台打印系统 LPD 的一切。 您可能发现了它很多的缺点， 它们很自然的让您提出这样的问题： “这里还有什么后台打印系统吗 (并且可以工作在 FreeBSD 上) ？”

LPRng

LPRng， 它的意思是 “LPR： 下一代”， 是一个完全重写的 PLP。 Patrick Powell 和 Justin Mason (PLP 维护的主要负责人) 合作完成了 LPRng。 LPRng 的主站是 `[`www.lprng.org/`](http://www.lprng.org/)`。

CUPS

CUPS， 通用 UNIX 打印系统， 提供了一个轻便的打印层给 UNIX®-基础的操作系统。 它是由 Easy Software Products 开发的， 并且成为了 UNIX® 供应商和用户的标准打印解决方案。

CUPS 使用 Internet 打印协议 (IPP) 作为管理打印任务和队列的基础。 行式打印机守护程序 (LPD) 服务器消息块 (SMB)， 和 AppSocket (a.k.a. JetDirect) 协议的部分功能也被支持。 CUPS 增加了基于浏览网络打印机和 PostScript 打印机描述 (PPD) 的打印选项来支持 UNIX® 下的真实打印。

CUPS 的主站是 `[`www.cups.org/`](http://www.cups.org/)`。

HPLIP

HPLIP， HP Linux® 成像及打印系统 (Imaging and Printing system)， 是一套由 HP 开发的用于支持 HP 的打印、 扫描和传真设备的工具。 这套程序利用 CUPS 打印系统作为后端来提供一些打印方面的功能。

HPLIP 的主页位于 `[`hplipopensource.com/hplip-web/index.html`](http://hplipopensource.com/hplip-web/index.html)`。

## 10.7. 疑难问题

在使用 [lptest(1)](http://www.FreeBSD.org/cgi/man.cgi?query=lptest&sektion=1&manpath=freebsd-release-ports) 进行简单的测试之后， 您可能得到了下面的结果， 而不是正确的结果：

过了一会儿， 它工作了； 或者， 它没有退出一整张纸。

打印机进行了打印， 但在这之前它呆了一段而且什么都没做。 事实上， 您可能需要按一下打印机上的 打印剩余 或者 送纸 按钮来让结果出现。

如果这是问题所在， 打印机可能在等待， 看看在打印之前， 您的任务是否还有更多的数据。 要修正这个问题， 您可以让文本过滤器发送一个送纸字符 (或者其他需要的) 到打印机。 这通常足够让打印机立即打印出内部缓存内剩余的文本。 它同样可以用来确保每个任务的结尾都占用一整张纸， 这样下一个任务才不会在前一个任务最后一张纸的中间开始。

接下来的 shell 脚本 `/usr/local/libexec/if-simple` 的脚本打印了一个送纸符在它发送任务到打印机之后：

```
#!/bin/sh
#
# if-simple - Simple text input filter for lpd
# Installed in /usr/local/libexec/if-simple
#
# Simply copies stdin to stdout.  Ignores all filter arguments.
# Writes a form feed character (\f) after printing job.

/bin/cat && printf "\f" && exit 0
exit 2
```

它的输出产生了 “楼梯效果”。

您可能在纸上得到下面这些：

```
!"#$%&'()*+,-./01234
                "#$%&'()*+,-./012345
                                 #$%&'()*+,-./0123456
```

您也成为了 *楼梯效果* 的受害者， 这是由对新行的标志字符的解释不一致造成的。 UNIX® 风格的操作系统使用一个单个字符： ASCII 码 10， 即换行 (LF)。 MS-DOS®， OS/2®， 和其他的系统使用一对儿字符， ASCII 码 10 *和* ASCII 码 13 (回车 CR)。 许多打印机使用 MS-DOS® 的习惯来代表新行。

当您在 FreeBSD 上打印时， 您的文本仅用了换行字符。 打印机， 打印机看到换行字符后， 走一行纸， 但还光标位置还是在这张纸上要打印的下一个字符处。 这就是回车的作用： 将下一个要打印的字符的位置移到纸张的左边缘。

这里是 FreeBSD 想要打印机做的：

| 打印机收到 CR | 打印机打印 CR |
| 打印机收到 LF | 打印机打印 CR + LF |

下面有几种完成这个的办法：

*   使用打印机的配置开关或者控制面板来更改它对这些字符的解释。 查看打印机的手册来找到怎样更改。

    ### 注意:

    如果您引导您的系统到其他除了 FreeBSD 之外的操作系统， 您可能不得不 *重新配置* 打印机使用 这个操作系统对 CR 和 LF 字符的解释。 您可能更喜欢下面这另一种解决方案。

*   让 FreeBSD 的串口驱动自动转换 LF 到 CR+LF。 当然， 这 *仅仅* 工作在串口打印机上。 要开启这个功能， 定义 `ms#` 变量并 设置 `onlcr` 模式在 `/etc/printcap` 文件中相应打印机处。

*   发送一个 *转义码* 到打印机来让它临时对 LF 字符做不同的处理。 参考您的打印机手册来了解您的打印机支持哪些转义码。 当您找到合适的转义码， 修改文本过滤器让其先发送这个转义码， 然后再发送打印任务。

    这里是一个为懂得 Hewlett-Packard PCL 转义码的打印机编写的文本过滤器。 这个过滤器使得打印机将 LF 作为一个 LF 和一个 CR 来对待； 然后它发送任务； 最后发送一个送纸符弹出任务的最后一张纸。 它应该可以在几乎所有 Hewlett Packard 打印机上工作。

    ```
    #!/bin/sh
    #
    # hpif - Simple text input filter for lpd for HP-PCL based printers
    # Installed in /usr/local/libexec/hpif
    #
    # Simply copies stdin to stdout.  Ignores all filter arguments.
    # Tells printer to treat LF as CR+LF.  Ejects the page when done.

    printf "\033&k2G" && cat && printf "\033&l0H" && exit 0
    exit 2
    ```

    下面是一个 `/etc/printcap` 文件的例子在叫做 `orchid` 的主机上。 它只有一台打印机连接在第一个并口上， 一台 Hewlett Packard LaserJet 3Si 名字叫做 `teak`。 它使用上面那段脚本作为文本过滤器：

    ```
    #
    #  /etc/printcap for host orchid
    #
    teak|hp|laserjet|Hewlett Packard LaserJet 3Si:\
            :lp=/dev/lpt0:sh:sd=/var/spool/lpd/teak:mx#0:\
            :if=/usr/local/libexec/hpif:
    ```

行行覆盖。

打印机从来不进纸换行。 所有的文本都打印在头一行文本的上面。

这个问题是 “相反” 于楼梯效果， 像上面描述的那样， 并且更少见。 一些地方， LF 这个 FreeBSD 用来结束一行的字符被作为 CR 这个将打印位置返回到纸的左边的字符对待。 而没有向下走纸一行。

使用打印机的配置开关或者控制面板来强制对 LF 和 CR 进行下面的转换：

| 打印机收到 | 打印机打印 |
| --- | --- |
| CR | CR |
| LF | CR + LF |

打印丢掉字符。

当打印时， 每行里打印机都丢掉一些字符没有打。 这个问题可能随着打印的进行越发严重， 丢掉越来越多的字符。

这个问题是由打印机跟不上计算机通过串口发送数据的速度造成的 (这个问题应该不会发生在并口打印机上)。 有两种方法能克服这个问题：

*   如果打印机支持 XON/XOFF 流量控制， 那就让 FreeBSD 使用它， 通过加入 `ixon` 模式在 `ms#` 变量里。

*   如果打印机支持请求/清除硬件握手信号 （通常时 `RTS/CTS`）， 指定 `crtscts` 模式在 `ms#` 变量里。 并且要确定连接打印机和计算机的线是支持硬件流量控制的。

它打印出垃圾。

打印机打印出的东西看起来是一些随机的字符， 而不是想要打印的东西。

这通常意味着另一种串口打印机通讯参数设置不正确的错误。 复查 `br` 变量中设定的波特， 和 `ms#` 中的校验设置； 确定打印机也在使用和 `/etc/printcap` 文件中相同的设置。

没有反应。

如果没有反应， 问题就可能出在 FreeBSD 而不是硬件上了。 增加日志文件 (`lf`) 变量到 `/etc/printcap` 文件里出现问题的打印机的记录处。 比如， 下面是打印机 `rattan` 的记录， 使用了 `lf` 变量：

```
rattan|line|diablo|lp|Diablo 630 Line Printer:\
        :sh:sd=/var/spool/lpd/rattan:\
        :lp=/dev/lpt0:\
        :if=/usr/local/libexec/if-simple:\
        :lf=/var/log/rattan.log
```

然后， 再次打印。 检查日志文件 (在我们的例子当中， 是 `/var/log/rattan.log` 这个文件) 来看是否有错误信息出现。 根据出现的信息， 试着来修正问题。

如果您没有指定 `lf` 变量， LPD 会使用 `/dev/console` 作为默认值。

## 第 11 章 Linux® 二进制兼容模式

Restructured and parts updated by Jim Mock.Originally contributed by Brian N. Handy 和 Rich Murphey.

## 11.1. 概述

FreeBSD 提供了与 Linux® 32-bit 二进制兼容， 允许用户在 FreeBSD 系统上安装和运行大多数的 32-bit Linux® 二进制程序而无需做任何修改。 据说在某些情况下， FreeBSD 上运行的 32-bit Linux® 二进制程序能有更好的表现。

然而， 仍然有一些 Linux® 操作系统特有的功能在 FreeBSD 上并不被支持。 例如， 要是 Linux® 程序过度地使用了诸如启用虚拟 8086 模式 i386™ 特有的调用， 则无法在 FreeBSD 上运行。 另外， 目前还不支持 64-bit 的 Linux® 二进制程序。

读完这章，您将了解到：

*   如何在 FreeBSD 系统中启用 Linux® 二进制兼容模式。

*   如何安装额外的 Linux® 共享库。

*   如何在 FreeBSD 上安装 Linux® 应用程序。

*   FreeBSD 上 Linux® 兼容模式的实现细节。

在阅读这章之前，您应该知道：

*   知道如何安装 额外的第三方软件。

## 11.2. 配置 Linux® 二进制兼容模式

默认情况下， Linux® 库并没有被安装而且 Linux® 二进制兼容模式也没有被启动。 Linux® 库可以通过手动安装或者使用 FreeBSD 的 Ports Collection。

安装 [emulators/linux-base-f10](http://www.freebsd.org/cgi/url.cgi?ports/emulators/linux-base-f10/pkg-descr) 包或者 port 是最容易在 FreeBSD 系统上获得一套基本的 Linux® 库的方法。 使用如下方法安装 port：

```
# `cd /usr/ports/emulators/linux_base-f10`
# `make install distclean`
```

安装完成以后， 加载 `linux` 模块启用 Linux® 二进制兼容模式：

```
# `kldload linux`userinput>
```

查看模块是否已经被加载：

```
% `kldstat`
Id Refs Address    Size     Name
 1    2 0xc0100000 16bdb8   kernel
 7    1 0xc24db000 d000     linux.ko
```

在 `/etc/rc.conf` 中加入以下这行后 Linux® 兼容模式便会在系统启动时自动开启：

```
linux_enable="YES"
```

想要在自制内核中静态链接 Linux® 二进制兼容支持的用户可以在自定义的内核配置文件中加入 `options COMPAT_LINUX`literal>。 然后按照 第 9 章 *配置 FreeBSD 的内核* 中所描述的方法编译并安装新内核。

### 11.2.1. 手动安装额外的库

在配置了 Linux® 兼容模式之后， 如果某个 Linux® 应用程序依然提示找不到共享库， 需先找出此 Linux® 二进制程序需要的共享库再手动安装。

在 Linux® 系统上使用 `ldd` 找出应用程序所需的共享库文件。 比如， 在安装有 Doom 的 Linux® 系统上运行如下的命令列出 `linuxdoom` 所需用到的共享库文件：

```
% `ldd linuxdoom`
libXt.so.3 (DLL Jump 3.1) => /usr/X11/lib/libXt.so.3.1.0
libX11.so.3 (DLL Jump 3.1) => /usr/X11/lib/libX11.so.3.1.0
libc.so.4 (DLL Jump 4.5pl26) => /lib/libc.so.4.6.29
```

然后把上面输出中最后一列中的所有文件从 Linux® 系统复制到 FreeBSD 上的 `/compat/linux`。 复制完成之后， 建立指向第一栏中文件名的符号链接。 这样在 FreeBSD 系统上将会有如下的文件：

```
/compat/linux/usr/X11/lib/libXt.so.3.1.0
/compat/linux/usr/X11/lib/libXt.so.3 -> libXt.so.3.1.0
/compat/linux/usr/X11/lib/libX11.so.3.1.0
/compat/linux/usr/X11/lib/libX11.so.3 -> libX11.so.3.1.0
/compat/linux/lib/libc.so.4.6.29
/compat/linux/lib/libc.so.4 -> libc.so.4.6.29
```

如果已经有了一个与 `ldd` 输出中第一列的主修订号相同的 Linux® 共享库文件， 则不再需要复制最后那列文件， 现有的共享库应该可以正常使用。 如果是更新版本的共享库通常建议复制。 只要有符号链接指向新的版本， 那么就可以删除旧版的了。

比如， FreeBSD 系统中现有这些共享库文件：

```
/compat/linux/lib/libc.so.4.6.27
/compat/linux/lib/libc.so.4 -> libc.so.4.6.27
```

并且 `ldd` 指出某个二进制程序需要之后版本：

```
libc.so.4 (DLL Jump 4.5pl26) -> libc.so.4.6.29
```

既然现有文件最后的版本号只相差一到两个版本， 程序应该可以正常使用稍旧些的版本。 不管怎样， 使用新版本替换现有 `libc.so` 都是安全的。

```
/compat/linux/lib/libc.so.4.6.29
/compat/linux/lib/libc.so.4 -> libc.so.4.6.29
```

通常最初几次在 FreeBSD 上安装 Linux® 程序时需要寻找 Linux® 二进制程序所依赖的共享库文件。 在此之后， 系统里便会有足够多的 Linux® 共享库文件来运行新安装的 Linux® 二进制程序而无需额外操作。

### 11.2.2. 安装 Linux® ELF 二进制程序

ELF 二进制程序有时需要额外的步骤。 当未被标记的 ELF 二进制程序被执行的时候， 会生成如下的错误信息：

```
% `./my-linux-elf-binary`
ELF binary type not known
Abort
```

为了帮助 FreeBSD 内核分辨 FreeBSD ELF 二进制程序和 Linux® 二进制程序， 请使用 [brandelf(1)](http://www.FreeBSD.org/cgi/man.cgi?query=brandelf&sektion=1&manpath=freebsd-release-ports)：

```
% `brandelf -t Linux my-linux-elf-binary`
```

由于现在的 GNU 工具链能自动把适当的标记信息写入 ELF 二进制程序中，这个步骤通常不是必须做的。

### 11.2.3. 安装基于 Linux® RPM 的应用程序

安装基于 Linux® RPM 的应用程序， 首先需要安装 [archivers/rpm](http://www.freebsd.org/cgi/url.cgi?ports/archivers/rpm/pkg-descr) 包或者 port。 安装好之后 `root` 用户就能使用此命令安装 `.rpm` 了：

```
# `cd /compat/linux`
# `rpm2cpio < /path/to/linux.archive.rpm | cpio -id`
```

如有必要的话使用 `brandelf` 标记安装好的 ELF 二进制程序。 注意此项安装将无法干净卸载。

### 11.2.4. 配置主机名解析器

如果 DNS 不能正常工作或是出现以下的错误信息：

```
resolv+: "bind" is an invalid keyword resolv+:
"hosts" is an invalid keyword
```

请参照此方法配置 `/compat/linux/etc/host.conf`：

```
order hosts, bind
multi on
```

这里指定了先查询 `/etc/hosts` 再查询 DNS。 如果 `/compat/linux/etc/host.conf` 不存在的话， Linux® 程序便会读取 `/etc/host.conf` 并提示与 FreeBSD 的语法不兼容。 如果没有在 `/etc/resolv.conf` 文件中配置域名服务器， 可以删除 `bind`。

## 11.3. 高级主题

此章节将讲述是 Linux® 二进制兼容如何工作的， 内容基于 Terry Lambert `<tlambert@primenet.com>` (Message ID: `<199906020108.SAA07001@usr09.primenet.com>`) 发表在 [FreeBSD 闲聊邮件列表](http://lists.FreeBSD.org/mailman/listinfo/freebsd-chat) 的邮件。

FreeBSD 有一个叫 “execution class loader” 的抽象层。 它被嵌入进了 [execve(2)](http://www.FreeBSD.org/cgi/man.cgi?query=execve&sektion=2&manpath=freebsd-release-ports) 系统调用。

历史上 UNIX® 加载器会依靠查看魔数 （通常是文件的开头 4 至 8 个字节）来确认是否是系统已知的的二进制程序， 如果是的话， 就会调用二进制程序加载器。

如果它不是二进制类型的程序， [execve(2)](http://www.FreeBSD.org/cgi/man.cgi?query=execve&sektion=2&manpath=freebsd-release-ports) 调用会返回一个错误， shell 则会把它当作 shell 命令执行。 “不论当前是哪一种 shell” 都会默认做出此种假设。

随后， [sh(1)](http://www.FreeBSD.org/cgi/man.cgi?query=sh&sektion=1&manpath=freebsd-release-ports) 会检查开头的两个字符， 如果它们是 `:\n`， 那么就调用 [csh(1)](http://www.FreeBSD.org/cgi/man.cgi?query=csh&sektion=1&manpath=freebsd-release-ports)。

FreeBSD 有一份加载器列表而不是一个单一的加载器， 并能回退到 `#!` 加载器来运行 shell 解释器或者 shell 脚本。

为了支持 Linux® ABI， FreeBSD 看到了二进制 ELF 程序的魔数。 ELF 加载器会查找一个专用的 *标记*， 那是在 ELF 镜像中的一个注释部分， 此区域在 SVR4/Solaris™ ELF 二进制中并不存在。

要运行 Linux® 二进制程序， 必须先使用 [brandelf(1)](http://www.FreeBSD.org/cgi/man.cgi?query=brandelf&sektion=1&manpath=freebsd-release-ports) 命令 *标记* 为 `Linux` 类型：

```
# `brandelf -t Linux file`
```

当 ELF 加载器看到了 `Linux` 标记，便会替换 `proc` 结构中的一个指针。 所有的系统调用都通过此指针来索引。 除此以外， 进程被标记以便对 signal trampoline 代码的陷阱向量做特殊处理， 还有一些其他由 Linux® 内核模块来处理的（细微）修补。

Linux® 系统调用向量包含一个 `sysent[]` 记录的列表， 它的地址位于内核模块之中。

当一个系统调用被 Linux® 二进制程序调用时， 陷阱代码会把系统调用函数指针从 `proc` 解引用至 Linux® 而不是 FreeBSD 的系统调用入口。

Linux® 模式会动态地 *reroots* 查找。 这与 `union` 文件系统选项是等效的。 首先会试图在 `/compat/linux/*`original-path`*` 目录查找文件。 如果失败了， 就会在 `/*`original-path`*` 目录下查找。 这使得需要其它程序的程序得以运行。 例如，Linux® 工具链都可以在 Linux® ABI 的支持下运行。 也就是说 Linux® 二进制程序可以加载并执行 FreeBSD 二进制程序， 如果当前没有相应的 Linux® 二进制程序， 可以在 `/compat/linux` 目录树中放置一个 [uname(1)](http://www.FreeBSD.org/cgi/man.cgi?query=uname&sektion=1&manpath=freebsd-release-ports) 命令， 使 Linux® 程序不易察觉它们并没有运行在 Linux® 系统上。

事实上， 在 FreeBSD 内核中有一个 Linux® 内核。 所有由内核提供的服务的各种底层功能在 FreeBSD 系统调用表的记录和 Linux® 系统调用表的记录是一样的： 文件系统操作， 虚拟内存操作， 信号发送， 和 System V IPC。 唯一的不同是 FreeBSD 会得到 FreeBSD 的 *glue* 功能， 而 Linux® 程序会得到 Linux® 的 *glue* 功能。 FreeBSD 的 *glue* 功能是静态链接入内核的， 而 Linux® 的 *glue* 功能可以静态链接， 或者通过内核模块访问。

严格说来其实并没有真正的模拟， 这是一种 ABI 的实现。 有时这被称为 “Linux® 模拟” 是因为在实现的时候还没有其他适合的词用来描述。 要说 FreeBSD 运行 Linux® 二进制程序并不确切， 因为当时代码并还没有被编译进去。