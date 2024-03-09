# 5\. 构建临时编译环境

# 5.1\. 简介

本章介绍如何编译和安装一个小的 Linux 系统。这个系统将仅包含必要的工具，用于构建第六章中最终的 LFS 系统。

构建这个小系统分两步进行，第一步是构建一个新的不依赖于宿主系统的工具链(编译器、汇编器、连接器、库文件以及一些有用的软件)，第二个步骤是利用这个工具链去构建其它基本的工具。

本章中编译的文件将安装在 `$LFS/tools` 目录下，这样可以与下一章将要安装的软件以及宿主系统区分开来。这些软件包编译出来是起临时作用的，我们不希望这些软件和即将建立的 LFS 系统混杂在一起。

### 重要

在运行每一个软件包的编译指令之前，都需要用 `lfs` 用户解开这个软件包，并用 **cd** 命令进入软件包解开后的目录。编译指令假定您使用的是 **bash** shell 。

［译者注］举例来说，对于第一个软件包 Binutils-2.16.1 ，在执行书上的第一个命令

```
mkdir -v ../binutils-build 
```

之前，必须先执行下列解包和切换目录的命令：

```
tar -xvjf $LFS/sources/binutils-2.16.1.tar.bz2 -C $LFS/sources/
cd $LFS/sources/binutils-2.16.1/ 
```

其他软件包以此类推。

某些软件包在编译之前需要打上补丁，但仅仅是需要补丁来解决某个问题的时候。一个补丁可能本章和下一章都会用到，也可能只在其中一章用到。因此，当某个软件包存在一个补丁，而在编译时并未让你使用这个补丁时，不要以为是我们忘记了。事实上，那个补丁只需要在另外一次编译中被用到。而应用某个补丁的时候，也可能会出现某些关于 *offset* 或 *fuzz* 的警告信息，不要理会，这个补丁仍然会被成功的应用。

大多数软件包的编译过程中，屏幕上可能会滚过一些警告信息，这些是正常且可以被安全忽略的。这些警告就像显示的那样：警告的是不标准，却不是不正确的 C 或 C++ 语法。C 标准常常会变，而某些软件包仍然使用老的标准，这不是问题，仅仅导致一些警告信息而已。

### 重要

在安装完每个软件包之后，删除其源代码和编译目录，除非另有特别说明。删除源文件可以节省磁盘空间，并且可以在下次需要安装同一个软件包的时候不会出现配置错误。

［译者注］举例来说，对于第一个软件包 Binutils-2.16.1 ，在执行完书上的最后一个命令

```
cp -v ld/ld-new /tools/bin 
```

之后，可以使用下列命令删除其源文件和编译目录：

```
rm -fr $LFS/sources/binutils-2.16.1/
rm -fr $LFS/sources/binutils-build/ 
```

其他的软件包以此类推。

最后一次检查 `LFS` 环境变量是否设置正确：

```
echo $LFS 
```

请确认输出显示的是挂载 LFS 分区的路径，在我们的例子中是 `/mnt/lfs` 。

# 5.2\. 工具链技术说明

本节阐述了整个构建方法的一些基本原理和技术细节，您不必马上就理解本节中的所有内容。在您实际操作之后，就会了解大多数的东西，您可以在任何时候回顾本节。

第五章的总体目标是提供一个临时环境，您可以 chroot 到这个环境，在里面构建一个第六章中的干净、没有问题的目标 LFS 系统。为了尽量的与宿主系统分开，我们创建了一个自包含、自依赖的工具链。要注意的是，这个创建过程被设计为尽量减少新手犯错误的可能，同时尽可能多的提供教育价值。

### 重要

在继续之前，要先知道工作平台的名称，就是所谓的"target triplet"(目标三元组)，许多时候，target triplet 可能是 *i686-pc-linux-gnu* 。要确定 target triplet 的名称，有一个简单的方法就是运行许多源码包里都有的 `config.guess` 脚本。解开 Binutils 的源码包，然后运行脚本 **`./config.guess`** 并注意输出的内容。

工作平台的动态连接器名称也需要知道，这里指的是动态加载器(不要与 Binutils 里的标准连接器 `ld` 混淆了)。动态连接器由 Glibc 提供，用来找到并加载一个程序运行时所需的共享库，在做好程序运行的准备之后，运行这个程序。动态连接器的名称通常是 `ld-linux.so.2` ，在不怎么流行的平台上则可能是 `ld.so.1` ，而在新的 64 位平台上更可能是别的完全不同的名称。查看宿主系统的 `/lib` 目录可以确定动态连接器的名称。确定这个名称还有一个必杀技，就是在宿主系统上随便找一个二进制文件，运行 **`readelf -l &lt;二进制文件名&gt; | grep interpreter`** 并查看输出的内容。涵盖所有平台的权威参考请查看 Glibc 源码根目录里的 `shlib-versions` 文件。

第五章中构建方法是如何工作的一些技术要点：

*   这个过程在原理上与交叉编译类似，通过把工具安装在同一个目录(使用相同的"prefix")中以便协同工作，还利用了一点 GNU 的"魔法"。

*   小心处理标准连接器的库文件搜索路径，确保程序仅连接到指定的库上。

*   小心处理 `gcc` 的 `specs` 文件，告诉编译器要使用哪个动态连接器。

首先安装的是 Binutils ，因为 GCC 和 Glibc 的 `configure` 脚本要在汇编器和连接器上执行各种各样的特性测试，以确定软件的哪些功能要启用，哪些功能要禁用。这样做比你想像的还要重要，配置不正确的 GCC 或者 Glibc 会导致工具链出现微妙的错误，这样的错误造成的影响可能直到整个系统快要编译完成的时候才显现出来。测试程序通常会在其它的许多工作进行之前给出错误警告(以避免其后的无效劳动)。

Binutils 的汇编器和连接器安装在两个位置：`/tools/bin` 和 `/tools/$TARGET_TRIPLET/bin` ，一个位置的程序是另外一个位置的硬链接。连接器的一个重要方面是它的库搜索顺序，将 *`--verbose`* 选项传递给 `ld` 可以获得详细的信息。例如，输入命令：**`ld --verbose | grep SEARCH`** 将显示当前搜索路径和顺序。要显示 `ld` 连接的是哪些文件，可以编译一个伪(dummy)程序并把 *`--verbose`* 选项传递给连接器。举个例子，输入 **`gcc dummy.c -Wl,--verbose 2&gt;&1 | grep succeeded`** 将显示所有连接成功的文件。

第二个安装的软件包是 GCC 。下面是运行 `configure` 脚本时，输出内容的一个示例：

```
checking what assembler to use...
        /tools/i686-pc-linux-gnu/bin/as
checking what linker to use... /tools/i686-pc-linux-gnu/bin/ld 
```

基于上面提到过的原因，这是重要的步骤，它同时证明了 GCC 的配置脚本并不是搜索 PATH 里的目录来寻找要使用哪个工具的，而且，在 `gcc` 的实际操作中，相同的搜索路径不一定会被使用。要知道 `gcc` 会使用哪个标准连接器，请运行 **`gcc -print-prog-name=ld`** 命令。

在编译一个伪程序的时候，给 `gcc` 命令传递 *`-v`* 选项可以获得详细的信息。举个例子：**`gcc -v dummy.c`** 将显示在预处理、编译和汇编各个阶段的详细信息，包括 `gcc` 文件包含的搜索路径及其顺序。

接下来安装的软件包是 Glibc 。编译 Glibc 的时候，最需要注意的地方是编译器、二进制工具(Binutils)和内核头文件。编译器一般不是问题，因为 Glibc 总是使用在 `PATH` 目录里找到的 `gcc` 。二进制工具和内核头文件则有点复杂，因此，为慎重起见，明确使用配置开关(选项)来强制进行正确的选择。在运行 `configure` 之后，请检查 `config.make` 文件的内容(位于 `glibc-build` 目录下)，查看所有重要的细节。注意，*`CC="gcc -B/tools/bin/"`*的作用是控制要使用哪个二进制工具；而 *`-nostdinc`* 和 *`-isystem`* 选项则是控制编译器的文件包含搜索路径。这些选项表明了 Glibc 软件包的一个重要特征：根据其编译方法，它是非常自给自足的，而且一般不依赖于工具链的默认值。

装完 Glibc 之后，需要做一些调整使得只在 `/tools` 目录里搜索和连接。安装一个调整好的 `ld` ，将它的固化搜索路径限制在 `/tools/lib` 目录。然后修改 `gcc` 的 specs 文件以指向 `/tools/lib` 目录里新的动态连接器。最后这一步在整个过程中至关重要，像上面提到的，指向动态连接器的固化路径被嵌入到每个 ELF 可执行文件里。可以通过 **`readelf -l &lt;二进制文件名&gt; | grep interpreter`** 命令来检查。修改 gcc 的 specs 文件以确保本章后面编译的每一个程序都使用位于 `/tools/lib` 目录里新的动态连接器。

需要使用新动态连接器也是第二遍编译 GCC 需要打 Specs 补丁的原因。不这样做的结果是 GCC 会把宿主系统 `/lib` 目录下动态连接器的名字嵌入进来，这样有悖于与宿主系统隔离的目标。

第二遍编译 Binutils 的过程中，我们利用 *`--with-lib-path`* 选项来控制 `ld` 的库搜索路径。如前面所指出的，核心工具链是自包含和自依赖的，所以第五章余下的软件包的编译将依赖于 `/tools` 下新的 Glibc 。

在第六章进入虚根环境后，第一个安装的主要软件包就是 Glibc ，原因是上面所提到的 Glibc 自给自足的特性。一旦 Glibc 安装到 `/usr` 目录后，马上改变工具链的默认值，然后构建目标 LFS 系统的其它部分。

# 5.3\. Binutils-2.16.1 - 第一遍

Binutils 是一组开发工具，包括连接器、汇编器和其他用于目标文件和档案的工具。

**预计编译时间：** 1 SBU**所需磁盘空间：** 189 MB

## 5.3.1\. 安装 Binutils

首先安装的第一个软件包是 Binutils ，这非常重要，因为 Glibc 和 GCC 会针对可用的连接器和汇编器进行多种测试，以决定是否打开某些特性。

Binutils 的文档推荐用一个新建的目录来编译它，而不是在源码目录中：

```
mkdir -v ../binutils-build
cd ../binutils-build 
```

### 注意

如果你想使用本书余下部份列出的 SBU 值，那么现在就要测量一下编译本软件包的时间。你可以用类似下面这样的 `time` 命令：**`time { ./configure ... && make && make install; }`** 。

为编译 Binutils 做准备：

```
../binutils-2.16.1/configure --prefix=/tools --disable-nls 
```

**配置选项的含义：**

*`--prefix=/tools`*

这个参数告诉 configure 脚本，应该把 Binutils 软件包中的程序安装到 `/tools` 目录中。

*`--disable-nls`*

这个参数禁止了国际化(通常简称 i18n)，静态程序不需要国际化的特性。

接下来编译它：

```
make 
```

现在编译完成了。通常我们会运行测试套件，但是目前测试套件(Tcl, Expect, DejaGNU)尚未安装。而且在这里运行测试也没什么用处，因为第一遍安装的程序很快就会被第二遍的程序所覆盖。

安装软件包：

```
make install 
```

接下来为后面"调整工具链"步骤准备连接器：

```
make -C ld clean
make -C ld LIB_PATH=/tools/lib
cp -v ld/ld-new /tools/bin 
```

**make 参数的含义：**

*`-C ld clean`*

告诉 make 程序删除所有 `ld` 子目录中编译生成的文件。

*`-C ld LIB_PATH=/tools/lib`*

这个选项重新编译 `ld` 子目录中的所有文件。在命令行中指定 Makefile 的 `LIB_PATH` 变量值，使它明确指向临时工具目录，以覆盖默认值。这个变量的值指定了连接器的默认库搜索路径，它在这一章的稍后部分会用到。

关于这个软件包的详细资料位于节 6.11.2, Binutils 的内容

# 5.4\. GCC-4.0.3 - 第一遍

GCC 软件包包含 GNU 编译器集合，其中有 C 和 C++ 编译器。

**预计编译时间：** 8.2 SBU**所需磁盘空间：** 514 MB

## 5.4.1\. 安装 GCC

GCC 的安装指南推荐用一个新建的目录来编译它，而不是在源码目录中：

```
mkdir -v ../gcc-build
cd ../gcc-build 
```

为编译 GCC 做准备：

```
../gcc-4.0.3/configure --prefix=/tools \
    --with-local-prefix=/tools --disable-nls --enable-shared \
    --enable-languages=c 
```

**配置选项的含义：**

*`--with-local-prefix=/tools`*

这个参数的目的是把 `/usr/local/include` 目录从 `gcc` 的 include 搜索路径里删除。这并不是绝对必要，但我们想尽量减小宿主系统的影响，所以才这样做。

*`--enable-shared`*

这个参数咋一看有点违反直觉。但只有加上它，才能编译出 `libgcc_s.so.1` 和 `libgcc_eh.a` 。Glibc(下一个软件包)的配置脚本只有在找到 `libgcc_eh.a` 时才能确保产生正确的结果。

*`--enable-languages=c`*

只编译 GCC 软件包中的 C 编译器。我们在本章里不需要其它编译器。

接下来编译它：

```
make bootstrap 
```

**make 参数的含义：**

*`bootstrap`*

使用这个参数的目的不仅仅是编译 GCC ，而是重复编译它几次。它用第一次编译生成的程序来第二次编译自己，然后又用第二次编译生成的程序来第三次编译自己，最后比较第二次和第三次编译的结果，以确保编译器能毫无差错的编译自身，这通常表明编译是正确的。

编译现在完成了，通常我们会在这里运行测试套件，但是正如前面说过的，测试套件目前尚未安装，而且在这里运行测试没什么用处，因为第一遍安装的程序很快就会被第二遍的程序所覆盖。

安装软件包：

```
make install 
```

最后，我们创建一个必要的符号连接。因为许多程序和脚本试图运行 `cc` 而不是 `gcc` ，这样做是为了让程序能在多种 Unix 平台上运行，并保持一致性。并不是每个人都安装 GNU C 编译器。只运行 `cc` 而不是 `gcc` 可以把选择 C 编译器的自由留给系统管理员，我们这里将指向 `gcc` ：

```
ln -vs gcc /tools/bin/cc 
```

关于这个软件包的详细资料位于节 6.12.2, GCC 的内容

# 5.5\. Linux-Libc-Headers-2.6.12.0

Linux-Libc-Headers 包含了"纯净的"内核头文件。

**预计编译时间：** 少于 0.1 SBU**所需磁盘空间：** 27 MB

## 5.5.1\. 安装 Linux-Libc-Headers

多年来的公共惯例是使用 `/usr/include` 目录下"原始的"内核头文件(直接来自于内核源码包)，但是近年来，内核开发者强烈要求不要这样做，因此诞生了 Linux-Libc-Headers 项目，其目标是维护一个 API(应用程序编程接口)版本稳定的 Linux 头文件。

安装这些头文件：

```
cp -Rv include/asm-i386 /tools/include/asm
cp -Rv include/linux /tools/include 
```

如果您的机器不是 i386 兼容架构的，请相应的调整第一条命令。

关于这个软件包的详细资料位于节 6.7.2, Linux-Libc-Headers 的内容

# 5.6\. Glibc-2.3.6

Glibc 包含了主要的 C 库。这个库提供了基本例程，用于分配内存、搜索目录、打开关闭文件、读写文件、字串处理、模式匹配、数学计算等等。

**预计编译时间：** 6 SBU**所需磁盘空间：** 325 MB

## 5.6.1\. 安装 Glibc

Glibc 文档推荐在源码目录之外的一个专门的编译目录下进行编译：

```
mkdir -v ../glibc-build
cd ../glibc-build 
```

接下来为编译 Glibc 做准备：

```
../glibc-2.3.6/configure --prefix=/tools \
    --disable-profile --enable-add-ons \
    --enable-kernel=2.6.0 --with-binutils=/tools/bin \
    --without-gd --with-headers=/tools/include \
    --without-selinux 
```

**配置选项的含义：**

*`--disable-profile`*

它关掉了 profiling 信息相关的库文件编译。如果你打算做 profiling ，就省掉这个参数。

*`--enable-add-ons`*

这个指示 Glibc 使用附加的 NPTL 包作为线程库。

*`--enable-kernel=2.6.0`*

这个告诉 Glibc 编译支持 2.6.x 内核的库。

*`--with-binutils=/tools/bin`*

这个参数并不是必需的。但它们能保证在编译 Glibc 时不会用错 Binutils 程序。

*`--without-gd`*

这个参数保证不生成 `memusagestat` 程序，这个程序会顽固地连接到宿主系统的库文件(libgd, libpng, libz 等等)。

*`--with-headers=/tools/include`*

这个参数指示 Glibc 按照前面刚刚安装到 tools 目录中的内核头文件编译自己，从而精确的知道内核的特性以根据这些特性对自己进行最佳化编译。

*`--without-selinux`*

当从一个含有 SELinux 特性的宿主系统(如 Fedora Core 3)编译时，Glibc 将会将 SELinux 支持编译进来。由于 LFS 工具链并不包含 SELinux 支持，所以一个含有 SELinux 特性的 Glibc 将会导致许多操作失败。所以这里明确禁用它。

在这个阶段你可能会看到下面的警告：

> ```
> configure: WARNING:
> *** These auxiliary programs are missing or
> *** incompatible versions: msgfmt
> *** some features will be disabled.
> *** Check the INSTALL file for required versions. 
> ```

抱怨说缺少或有不兼容的 `msgfmt` 程序，这没有什么大问题，不过有时候可能会在运行测试套件的时候出问题。`msgfmt` 程序是宿主系统 Gettext 应当提供的一部分。如果担心宿主系统的 `msgfmt` 有兼容性问题，你可以升级宿主系统的 Gettext ，也可以忽略这个问题而不去管它。

编译软件包：

```
make 
```

现在编译完成了。正如前面说过的，在这里运行测试没什么用处，但是如果你坚持要测试的话可以运行下列命令：

```
make check 
```

关于测试失败重要性的讨论请参考这里：节 6.9, "Glibc-2.3.6."

在这一章里，Glibc 的测试套件高度依赖于宿主系统的工具和环境，尤其是内核。因为这个原因，有时错误很难避免，但是无需太在意。第六章里面的 Glibc 才是我们最后所使用的，那里的 Glibc 需要通过绝大多数测试。但要注意的是，即使在第六章里，有的失败还是会出现，比如 math 测试。

当遇到一个错误时，记录下来，再用 `make check` 继续。测试套件会从出错的地方继续进行。你也可以用 `make -k check` 来一次把测试做完。但如果你这样做的话，就要把屏幕输出记录到文件里（ `make -k check &gt; ck_log` ），以便最后检查到底出了多少错，哪些测试出错了。

在安装 Glibc 的过程中，它会警告缺少 `/tools/etc/ld.so.conf` 文件。其实这没什么关系，不过下面的命令能修正它：

```
mkdir -v /tools/etc
touch /tools/etc/ld.so.conf 
```

安装软件包：

```
make install 
```

不同的国家和文化，使用不同的习俗来交流。这样的习俗很多，从比较简单的时间和日期格式，到非常复杂的语言发音。GNU 程序的"internationalization"(国际化，又称"i18n"，18 表示中间的 18 个字母)是以 locale 来实现的。

### 注意

如果刚才没有运行测试套件，那么现在就没有必要安装 locale 。在下一章里面我们将会安装。如果你一定要安装 locale ，请参考节 6.9, "Glibc-2.3.6."的内容。

关于这个软件包的详细资料位于节 6.9.4, Glibc 的内容

# 5.7\. 调整工具链

现在临时的 C 库已经装好，接下来本章中要编译的所有工具应该连接到这些库上。为了达到这个目标，需要调整连接器和编译器的 specs 文件。

在第一遍编译 Binutils 快结束时已经调整过的连接器，现在需要被重新命名以便可以被正确的找到和使用。首先备份原来的连接器，然后用调整过的连接器来替代，最后还要创建一个指向 `/tools/$(gcc -dumpmachine)/bin` 中连接器副本的连接。

```
mv -v /tools/bin/{ld,ld-old}
mv -v /tools/$(gcc -dumpmachine)/bin/{ld,ld-old}
mv -v /tools/bin/{ld-new,ld}
ln -sv /tools/bin/ld /tools/$(gcc -dumpmachine)/bin/ld 
```

从现在开始，所有程序都将连接到 `/tools/lib` 中的库文件。

下面要做的是修正 GCC 的"specs"文件，使它指向新的动态连接器。一个简单的 `sed` 命令就能做到：

```
SPECFILE=`dirname $(gcc -print-libgcc-file-name)`/specs &&
gcc -dumpspecs > $SPECFILE &&
sed 's@^/lib/ld-linux.so.2@/tools&@g' $SPECFILE > tempspecfile &&
mv -vf tempspecfile $SPECFILE &&
unset SPECFILE 
```

推荐你拷贝和粘贴上面的命令，而不是手动输入。当然你也可以手动编辑 specs 文件，只要把所有的"/lib/ld-linux.so.2"都替换成"/tools/lib/ld-linux.so.2"就行了。

请用你的眼睛亲自仔细检查一下 specs 文件，以确保上述修改的的确确生效了。

### 重要

如果你的系统平台上，动态连接器的名字不是 `ld-linux.so.2` ，你必须把上面命令里的"ld-linux.so.2"换成你的系统平台上动态连接器的名字。参见节 5.2, "工具链技术说明,"。

在编译过程中，GCC 会运行 `fixincludes` 脚本来扫描系统头文件目录，并找出需要修正的头文件(比如包含语法错误)，然后把修正后的文件放到 GCC 专属头文件目录里。因此，它可能会找出宿主系统中需要修正的头文件，并将修正后的结果放到 GCC 专属头文件目录里。由于本章的剩余部分仅需要使用当前已经安装好的 GCC 和 Glibc 的头文件，所以任何"修正后的"头文件都可以被安全的删除。并且这样做也有助于避免宿主系统中的头文件"污染"编译环境。运行下面的命令删除 GCC 专属头文件目录中的头文件(由于命令较长，推荐你拷贝和粘贴命令，而不是手动输入)：

```
GCC_INCLUDEDIR=`dirname $(gcc -print-libgcc-file-name)`/include &&
find ${GCC_INCLUDEDIR}/* -maxdepth 0 -xtype d -exec rm -rvf '{}' \; &&
rm -vf `grep -l "DO NOT EDIT THIS FILE" ${GCC_INCLUDEDIR}/*` &&
unset GCC_INCLUDEDIR 
```

### 小心

现在，需要停下来确认新工具链的基本功能(编译和连接)是否按预期工作，运行下面的命令做一个简单的合理性检查：

```
echo 'main(){}' > dummy.c
cc dummy.c
readelf -l a.out | grep ': /tools' 
```

如果一切正常，应该不会出错，而且最后一个命令的结果应当是：

```
[Requesting program interpreter: /tools/lib/ld-linux.so.2] 
```

注意，`/tools/lib` 应该是动态连接器的前缀。

如果输出不是像上面那样或者根本没有输出，那么就有大问题了。返回并检查前面的操作，找出问题，并改正过来。在改正之前，不要继续后面的部份，因为这样做没有意义。首先，再次上述合理性检查，用 `gcc` 代替 `cc` ，如果工作正常，那么是因为 `/tools/bin/cc` 这个符号链接丢失了。回头看看节 5.4, "GCC-4.0.3 - 第一遍,"，并建立符号链接。接下来，确保 `PATH` 正确。检查时，运行 `echo $PATH` 并检查 `/tools/bin` 是否在列表的最前面。如果 `PATH` 错误，可能是因为你没有以 `lfs` 用户登录，或者在节 4.4, "设置工作环境."部分出错了。另外一个原因可能是上面修正 specs 文件时出错，如果这样，重新修改 specs 文件，复制粘贴时要小心仔细。

在确定一切正常后，删除测试文件：

```
rm -v dummy.c a.out 
```

### 注意

下一小节中编译 TCL 时也将有助于检查工具连是否正确。如果 TCL 编译失败则表示之前安装的 Binutils 、GCC 或 Glibc 有问题，而不是 TCL 自身有问题。

# 5.8\. Tcl-8.4.13

Tcl 软件包包含工具命令语言(Tool Command Language)。

**预计编译时间：** 0.3 SBU**所需磁盘空间：** 24 MB

## 5.8.1\. 安装 Tcl

这个软件包和接下来安装的两个软件包(Expect 和 DejaGNU)是为了给运行 GCC 和 Binutils 的测试程序提供支持。仅为了测试而安装三个软件包，看起来似乎有点多余，但是看到那些最重要的工具正常工作，心理上会比较踏实。即使没有运行本章中测试程序(不是必需的)，运行第六章中的测试时也需要这些软件包。

为编译 Tcl 做准备：

```
cd unix
./configure --prefix=/tools 
```

编译软件包：

```
make 
```

要测试结果，请运行：**`TZ=UTC make test`** 。已知 Tcl 的测试程序会在某些还未完全了解的宿主系统下出现测试失败的情况，因此，如果这里的测试失败了，不要紧，因为这并不关键。*`TZ=UTC`* 参数将时区设置为协调世界时(UTC)，也就是格林尼治时间(GMT)，但只是在运行测试程序的时候才这样设置，这将确保时钟测试正确。关于 `TZ` 环境变量的详细资料位于第七章。

安装软件包：

```
make install 
```

安装 Tcl 头文件，下一个包(Expect)要使用 Tcl 的头文件。

```
make install-private-headers 
```

现在创建一个必需的符号链接：

```
ln -sv tclsh8.4 /tools/bin/tclsh 
```

## 5.8.2\. Tcl 的内容

**安装的程序：** tclsh(→tclsh8.4), tclsh8.4**安装的库：** libtcl8.4.so

### 简要描述

|  |  |
| --- | --- |
| `tclsh8.4` | Tcl 命令 shell |
| `tclsh` | 指向 tclsh8.4 的链接 |
| `libtcl8.4.so` | Tcl 库文件 |

# 5.9\. Expect-5.43.0

Expect 软件包包含一个通过执行脚本对话框与其它交互式程序通信的工具。

**预计编译时间：** 0.1 SBU**所需磁盘空间：** 4 MB

## 5.9.1\. 安装 Expect

先修正一个可能导致 GCC 测试程序假失败的 bug ：

```
patch -Np1 -i ../expect-5.43.0-spawn-1.patch 
```

为编译 Expect 做准备：

```
./configure --prefix=/tools --with-tcl=/tools/lib \
  --with-tclinclude=/tools/include --with-x=no 
```

**配置选项的含义：**

*`--with-tcl=/tools/lib`*

这个选项确保配置脚本找到的是安装在临时工具目录下的 Tcl ，而不是宿主系统里的。

*`--with-tclinclude=/tools/include`*

这个选项告诉 Expect 到哪里寻找 Tcl 的源代码目录和头文件。使用这个选项可以避免 `configure` 脚本因为找不到 Tcl 的源代码目录而导致的失败。

*`--with-x=no`*

这个选项告诉 `configure` 脚本不要搜索 Tk(Tcl 的图形界面组件)或者 X Window 系统的库，这两者都可能位于宿主系统上。

编译软件包：

```
make 
```

要测试结果，请运行：**`make test`** 。请注意，已知 Expect 的测试程序会在某些不在我们控制范围内的宿主系统下出现测试失败。因此，如果您运行这里的测试程序失败了也没关系，因为这并不关键。

安装软件包：

```
make SCRIPTS="" install 
```

**make 参数的含义：**

*`SCRIPTS=""`*

这个选项防止安装 Expect 所补充的一些并不需要的脚本。

## 5.9.2\. Expect 的内容

**安装的程序：** expect**安装的库：** libexpect-5.43.a

### 简要描述

|  |  |
| --- | --- |
| `expect` | 按照一个脚本与其它交互式程序通信 |
| `libexpect-5.43.a` | 包含的函数可以让 Expect 作为 Tcl 的扩展来使用，或者直接被 C 或 C++ 使用(不需要 Tcl) |

# 5.10\. DejaGNU-1.4.4

DejaGNU 软件包包含了一个测试其它程序的框架。

**预计编译时间：** 少于 0.1 SBU**所需磁盘空间：** 6.2 MB

## 5.10.1\. 安装 DejaGNU

为编译 DejaGNU 做准备：

```
./configure --prefix=/tools 
```

编译并安装软件包：

```
make install 
```

要测试结果，请运行：**`make check`** 。

## 5.10.2\. DejaGNU 的内容

**安装的程序：** runtest

### 简要描述

|  |  |
| --- | --- |
| `runtest` | 一个包装脚本，用于定位正确的 `expect` 解释程序(shell)并运行 DejaGNU |

# 5.11\. GCC-4.0.3 - 第二遍

GCC 软件包包含 GNU 编译器集合，其中有 C 和 C++编译器。

**预计编译时间：** 4.2 SBU**所需磁盘空间：** 443 MB

## 5.11.1\. 重新安装 GCC

测试 GCC 和 Binutils 所需的工具(Tcl, Expect, DejaGNU)已经安装好。现在 GCC 和 Binutils 将被重新编译，连接到新的 Glibc 并作适当测试(如果运行这章中的测试的话)。注意，这些测试套件受伪终端(PTY)的影响很大，这些伪终端通常是由宿主系统通过 `devpts` 文件系统实现的。你可以用下面的方法，来测试宿主系统中 PTY 是否设置正常：

```
expect -c "spawn ls" 
```

如果你得到下面的回答：

```
The system has no more ptys.
Ask your system administrator to create more. 
```

说明主系统的 PTY 没设置好。这种情况下，运行 GCC 和 Binutils 的测试套件就没什么意义了。你需要先解决主系统中的 PTY 设置问题。具体请参见 LFS 的 FAQ ：[*http://www.linuxfromscratch.org/lfs/faq.html#no-ptys*](http://www.linuxfromscratch.org/lfs/faq.html#no-ptys) 。

在之前的节 5.7, "调整工具链"中我们提到过在 GCC 编译过程中会运行 `fixincludes` 脚本来扫描系统头文件目录，并找出需要修正的头文件，然后把修正后的头文件放到 GCC 专属头文件目录里。因为现在 GCC 和 Glibc 已经安装完毕，而且它们的头文件已知无需修正，所以这里并不需要 `fixincludes` 脚本。另外，由于 GCC 专属头文件目录会被优先搜索，结果就是 GCC 使用的头文件是宿主系统的头文件，而不是新安装的那个，从而导致编译环境被"污染"。因此必须通过下面的命令来禁止 `fixincludes` 脚本运行：

```
cp -v gcc/Makefile.in{,.orig} &&
sed 's@\./fixinc\.sh@-c true@' gcc/Makefile.in.orig > gcc/Makefile.in 
```

在节 5.4, "GCC-4.0.3 - 第一遍"中进行的 bootstrap 编译使用了 `-fomit-frame-pointer` 选项，而非 bootstrap 编译则默认忽略了该选项，所以需要使用下面的 `sed` 命令来确保在非 bootstrap 编译时也同样使用 `-fomit-frame-pointer` 选项，以保持一致性：

```
cp -v gcc/Makefile.in{,.tmp} &&
sed 's/^XCFLAGS =$/& -fomit-frame-pointer/' gcc/Makefile.in.tmp \
  > gcc/Makefile.in 
```

使用下面的补丁修改 GCC 的缺省动态连接器(通常是 `ld-linux.so.2`)的位置：

```
patch -Np1 -i ../gcc-4.0.3-specs-1.patch 
```

上述补丁还把 `/usr/include` 从 GCC 的头文件搜索路径里删掉。现在预先打补丁而不是在安装 GCC 之后调整 specs 文件可以保证新的动态连接器在编译 GCC 的时候就用上。也就是说，随后的所有临时程序都会连接到新的 Glibc 上。

### 重要

上述补丁非常重要，为了成功编译，千万别忘了使用它们。

再次为编译创建一个单独目录：

```
mkdir -v ../gcc-build
cd ../gcc-build 
```

在开始编译前，别忘了 unset 任何优化相关的环境变量。[是不是应当省略这句?]

为编译 GCC 做准备：

```
../gcc-4.0.3/configure --prefix=/tools \
    --with-local-prefix=/tools --enable-clocale=gnu \
    --enable-shared --enable-threads=posix \
    --enable-__cxa_atexit --enable-languages=c,c++ \
    --disable-libstdcxx-pch 
```

**新配置选项的含义：**

*`--enable-clocale=gnu`*

本参数确保 C++ 库在任何情况下都使用正确的 locale 模块。如果配置脚本查找到 *de_DE* 这个 locale ，它就会使用正确的 gnu locale 模块。然而，如果没有安装 *de_DE* ，就有可能创建出应用程序二进制接口(ABI)不兼容的 C++ 库文件，这是因为选择了错误的通用(generic) locale 模块。

*`--enable-threads=posix`*

使 C++ 异常能处理多线程代码。

*`--enable-__cxa_atexit`*

用 *__cxa_atexit* 代替 *atexit* 来登记 C++ 对象的本地静态和全局析构函数，这是为了完全符合标准对析构函数的处理规定。它还会影响到 C++ ABI ，因此生成的 C++ 共享库在其他的 Linux 发行版上也能使用。

*`--enable-languages=c,c++`*

本参数编译 C 和 C++ 语言的编译器。

*`--disable-libstdcxx-pch`*

不为 `libstdc++` 编译预编译头(PCH)，它占用了很大空间，但是我们用不到它。

编译软件包：

```
make 
```

现在没必要用 *`bootstrap`* 作为 make 的目标，因为这里 GCC 是用相同版本的 GCC 来编译的，其实连源码都一模一样，就是在第一遍的时候安装的那个。

现在编译完成了，早先我们谈到过，本章中的临时工具的测试程序并不是必须运行的，如果您要运行 GCC 的测试程序，请输入下面的命令：

```
make -k check 
```

*`-k`* 参数是让测试套件即使遇到错误，也继续运行，直到完成。GCC 的测试套件非常全面，所以基本上总是会出一些错的。

对于测试错误的重要说明位于节 6.12, "GCC-4.0.3."。

安装软件包：

```
make install 
```

### 小心

现在，需要停下来再一次确认新工具链的基本功能(编译和连接)是否按预期工作，运行下面的命令做一个简单的合理性检查：

```
echo 'main(){}' > dummy.c
cc dummy.c
readelf -l a.out | grep ': /tools' 
```

如果一切正常，应该不会出错，而且最后一个命令的结果应当是：

```
[Requesting program interpreter: /tools/lib/ld-linux.so.2] 
```

注意，`/tools/lib` 应该是动态连接器的前缀。

如果输出不是像上面那样或者根本没有输出，那么就有大问题了。返回并检查前面的操作，找出问题，并改正过来。在改正之前，不要继续后面的部份，因为这样做没有意义。首先，再次上述合理性检查，用 `gcc` 代替 `cc` ，如果工作正常，那么是因为 `/tools/bin/cc` 这个符号链接丢失了。回头看看节 5.4, "GCC-4.0.3 - 第一遍," 并建立符号链接。接下来，确保 `PATH` 正确。检查时，运行 `echo $PATH` 并检查 `/tools/bin` 是否在列表的最前面。如果 `PATH` 错误，可能是因为你没有以 `lfs` 用户登录，或者在 节 4.4, "设置工作环境."部分出错了。另外一个原因可能是上面修正 specs 文件时出错，如果这样，重新修改 specs 文件，复制粘贴时小心。

在确定一切正常后，删除测试文件：

```
rm -v dummy.c a.out 
```

关于这个软件包的详细资料位于节 6.12.2, GCC 的内容

# 5.12\. Binutils-2.16.1 - 第二遍

Binutils 是一组开发工具，包括连接器、汇编器和其他用于目标文件和档案的工具。

**预计编译时间：** 1.1 SBU**所需磁盘空间：** 154 MB

## 5.12.1\. 重新安装 Binutils

再次为编译创建一个单独目录：

```
mkdir -v ../binutils-build
cd ../binutils-build 
```

为编译 Binutils 做准备：

```
../binutils-2.16.1/configure --prefix=/tools \
    --disable-nls --with-lib-path=/tools/lib 
```

**新配置选项的含义：**

*`--with-lib-path=/tools/lib`*

这个选项指示 configure 脚本在 Binutils 编译过程中将传递给连接器的库搜索路径设为 `/tools/lib` ，以防止连接器搜索宿主系统的库目录。

编译软件包：

```
make 
```

现在编译完成了，早先我们谈到过，本章中的临时工具的测试程序并不是必须运行的，如果您要运行 Binutils 的测试程序，请输入下面的命令：

```
make check 
```

安装软件包：

```
make install 
```

现在，为下一章的"再次调整工具链"阶段配置连接器：

```
make -C ld clean
make -C ld LIB_PATH=/usr/lib:/lib
cp -v ld/ld-new /tools/bin 
```

关于这个软件包的详细资料位于节 6.11.2, Binutils 的内容

# 5.13\. Ncurses-5.5

Ncurses 提供独立于终端的字符终端处理库，含有功能键定义(快捷键)、屏幕绘制以及基于文本终端的图形互动功能。

**预计编译时间：** 0.7 SBU**所需磁盘空间：** 30 MB

## 5.13.1\. 安装 Ncurses

为编译 Ncurses 做准备：

```
./configure --prefix=/tools --with-shared \
    --without-debug --without-ada --enable-overwrite 
```

**配置选项的含义：**

*`--without-ada`*

这个选项让 Ncurses 在即使宿主系统上安装了 Ada 编译器的情况下也不要编译其 Ada 绑定。需要这样做的原因是一旦我们进入 `chroot` 环境，Ada 就不能使用了。

*`--enable-overwrite`*

这个选项让 Ncurses 把它的头文件安装到 `/tools/include` 目录，而不是 `/tools/include/ncurses` 目录，以确保其它软件包可以顺利找到 Ncurses 的头文件。

编译软件包：

```
make 
```

这个软件包没有附带测试程序。

安装软件包：

```
make install 
```

关于这个软件包的详细资料位于节 6.18.2, Ncurses 的内容

# 5.14\. Bash-3.1

Bash 是 Bourne-Again Shell 的缩写，它在 UNIX 系统中作为 shell 被广泛使用。

**预计编译时间：** 0.4 SBU**所需磁盘空间：** 22 MB

## 5.14.1\. 安装 Bash

Bash-3.1 正式发布以后开发者们又修正了许多缺陷，下面的补丁包含了这些修正：

```
patch -Np1 -i ../bash-3.1-fixes-8.patch 
```

为编译 Bash 做准备：

```
./configure --prefix=/tools --without-bash-malloc 
```

**配置选项的含义：**

*`--without-bash-malloc`*

这个选项禁用了 Bash 的内存分配函数(`malloc`)，这个函数已知会造成段错误，通过设置这个选项，Bash 将使用更为稳定的 Glibc 里的 `malloc` 函数。

编译软件包：

```
make 
```

要测试结果，请运行：**`make tests`** 。

安装软件包：

```
make install 
```

为那些用 `sh` 作为 shell 的程序创建符号链接：

```
ln -vs bash /tools/bin/sh 
```

关于这个软件包的详细资料位于节 6.27.2, Bash 的内容

# 5.15\. Bzip2-1.0.3

Bzip2 包含了对文件进行压缩和解压缩的工具，对于文本文件，`bzip2` 比传统的 `gzip` 拥有更高压缩比。

**预计编译时间：** 少于 0.1 SBU**所需磁盘空间：** 4.2 MB

## 5.15.1\. 安装 Bzip2

Bzip2 软件包没有 `configure` 脚本，用下面的命令直接编译：

```
make 
```

安装软件包：

```
make PREFIX=/tools install 
```

关于这个软件包的详细资料位于节 6.28.2, Bzip2 的内容

# 5.16\. Coreutils-5.96

Coreutils 软件包包括一整套用于显示和设置基本系统特征的工具。

**预计编译时间：** 0.6 SBU**所需磁盘空间：** 56.1 MB

## 5.16.1\. 安装 Coreutils

为编译 Coreutils 做准备：

```
./configure --prefix=/tools 
```

编译软件包：

```
make 
```

要测试结果，请运行：**`make RUN_EXPENSIVE_TESTS=yes check`** ，*`RUN_EXPENSIVE_TESTS=yes`* 参数让测试程序运行几个附加的测试，在某些平台上这些测试会耗费更多的 CPU 和内存，不过一般在 Linux 上不是什么问题。

安装软件包：

```
make install 
```

关于这个软件包的详细资料位于节 6.14.2, Coreutils 的内容

# 5.17\. Diffutils-2.8.1

Diffutils 软件包里的程序可以向你显示两个文件或目录的差异，常用来生成软件的补丁。

**预计编译时间：** 0.1 SBU**所需磁盘空间：** 6.2 MB

## 5.17.1\. 安装 Diffutils

为编译 Diffutils 做准备：

```
./configure --prefix=/tools 
```

编译软件包：

```
make 
```

这个软件包没有附带测试程序。

安装软件包：

```
make install 
```

关于这个软件包的详细资料位于节 6.29.2, Diffutils 的内容

# 5.18\. Findutils-4.2.27

Findutils 软件包包含查找文件的工具，既能即时查找(递归的搜索目录，并可以显示、创建和维护文件)，也能在数据库里查找(通常比递归查找快但是在数据库没有及时更新的情况下，结果并不可靠)。

**预计编译时间：** 0.2 SBU**所需磁盘空间：** 12 MB

## 5.18.1\. 安装 Findutils

为编译 Findutils 做准备：

```
./configure --prefix=/tools 
```

编译软件包：

```
make 
```

要测试结果，请运行：**`make check`** 。

安装软件包：

```
make install 
```

关于这个软件包的详细资料位于节 6.32.2, Findutils 的内容

# 5.19\. Gawk-3.1.5

Gawk 是一个处理文本文件的工具包。

**预计编译时间：** 0.2 SBU**所需磁盘空间：** 18.2 MB

## 5.19.1\. 安装 Gawk

为编译 Gawk 做准备：

```
./configure --prefix=/tools 
```

由于 `configure` 脚本的一个 bug ，Gawk 不能正确检测某些 Glibc 支持的 locale ，这将会导致一些问题，比如，Gettext 的测试程序会失败。修复这个 bug 的办法是在 `config.h` 文件结尾追加丢失的宏定义：

```
cat >>config.h <<"EOF"
#define HAVE_LANGINFO_CODESET 1
#define HAVE_LC_MESSAGES 1
EOF 
```

编译软件包：

```
make 
```

要测试结果，请运行：**`make check`** 。

安装软件包：

```
make install 
```

关于这个软件包的详细资料位于节 6.35.2, Gawk 的内容

# 5.20\. Gettext-0.14.5

Gettext 包含用于系统的国际化和本地化的工具，可以在编译程序的时候使用本国语言支持(NLS)，可以使程序的输出使用用户设置的语言而不是英文。

**预计编译时间：** 0.4 SBU**所需磁盘空间：** 43 MB

## 5.20.1\. 安装 Gettext

对于临时工具链来说，我们只需要编译和安装 Gettext 中的一个二进制文件即可。

为编译 Gettext 做准备：

```
cd gettext-tools
./configure --prefix=/tools --disable-shared 
```

**配置选项的含义：**

*`--disable-shared`*

当前我们不需要安装任何 Gettext 共享库，因此也就不需要编译它们。

编译软件包：

```
make -C lib
make -C src msgfmt 
```

因为只编译了一个二进制文件，所以无法运行测试套件。并且我们也不推荐在此时运行测试。

安装编译好的二进制文件 `msgfmt` ：

```
cp -v src/msgfmt /tools/bin 
```

关于这个软件包的详细资料位于节 6.36.2, Gettext 的内容

# 5.21\. Grep-2.5.1a

Grep 可以按指定的匹配模式搜索文件中的内容。

**预计编译时间：** 0.1 SBU**所需磁盘空间：** 4.8 MB

## 5.21.1\. 安装 Grep

为编译 Grep 做准备：

```
./configure --prefix=/tools \
    --disable-perl-regexp 
```

**配置选项的含义：**

*`--disable-perl-regexp`*

这个选项确保 `grep` 程序不连接可能在宿主系统上存在的 PCRE(Perl 兼容正则表达式)库，因为进入 `chroot` 环境后，它就不能使用了。

编译软件包：

```
make 
```

要测试结果，请运行：**`make check`** 。

安装软件包：

```
make install 
```

关于这个软件包的详细资料位于节 6.37.2, Grep 的内容

# 5.22\. Gzip-1.3.5

Gzip 软件包包含了压缩和解压文件的程序。

**预计编译时间：** 少于 0.1 SBU**所需磁盘空间：** 2.2 MB

## 5.22.1\. 安装 Gzip

为编译 Gzip 做准备：

```
./configure --prefix=/tools 
```

编译软件包：

```
make 
```

这个软件包没有附带测试程序。

安装软件包：

```
make install 
```

关于这个软件包的详细资料位于节 6.39.2, Gzip 的内容

# 5.23\. M4-1.4.4

M4 包含一个宏处理器。

**预计编译时间：** 少于 0.1 SBU**所需磁盘空间：** 3 MB

## 5.23.1\. 安装 M4

为编译 M4 做准备：

```
./configure --prefix=/tools 
```

编译软件包：

```
make 
```

要测试结果，请运行：**`make check`** 。

安装软件包：

```
make install 
```

关于这个软件包的详细资料位于节 6.16.2, M4 的内容

# 5.24\. Make-3.80

Make 软件包包含一个编译软件包的程序。

**预计编译时间：** 0.1 SBU**所需磁盘空间：** 7.8 MB

## 5.24.1\. 安装 Make

为编译 Make 做准备：

```
./configure --prefix=/tools 
```

编译软件包：

```
make 
```

要测试结果，请运行：**`make check`** 。

安装软件包：

```
make install 
```

关于这个软件包的详细资料位于节 6.44.2, Make 的内容

# 5.25\. Patch-2.5.4

Patch 软件包包含一个根据补丁文件来修改原文件的程序。补丁文件通常是用 `diff` 程序创建的。

**预计编译时间：** 少于 0.1 SBU**所需磁盘空间：** 1.6 MB

## 5.25.1\. 安装 Patch

为编译 Patch 做准备：

```
./configure --prefix=/tools 
```

编译软件包：

```
make 
```

这个软件包没有附带测试程序。

安装软件包：

```
make install 
```

关于这个软件包的详细资料位于节 6.48.2, Patch 的内容

# 5.26\. Perl-5.8.8

Perl 是 Practical Extraction and Report Language 的缩写。Perl 将 C, sed, awk 和 sh 的最佳特性集于一身，是一种强大的编程语言。

**预计编译时间：** 0.7 SBU**所需磁盘空间：** 84 MB

## 5.26.1\. 安装 Perl

首先应用下面的补丁，调整指向 C 库的硬连接路径：

```
patch -Np1 -i ../perl-5.8.8-libc-2.patch 
```

准备编译 Perl(请正确输入下面命令中的'Data/Dumper Fcntl IO POSIX'——全是字母)：

```
./configure.gnu --prefix=/tools -Dstatic_ext='Data/Dumper Fcntl IO POSIX' 
```

**配置选项的含义：**

*`-Dstatic_ext='Data/Dumper Fcntl IO POSIX'`*

这个选项让 Perl 编译静态扩展的最小集，下一章安装和测试 Coreutils 软件包的时候需要用到。

仅需要编译这个软件包中的一小部分必要工具：

```
make perl utilities 
```

尽管 Perl 附带测试程序，但我们不推荐在这里运行。由于只编译了一部分 Perl，现在运行 `make test` 会编译 Perl 的其余部分，而这里我们并不需要它们。如果想测试的话，可以到下一章再运行测试程序。

安装这些工具和库：

```
cp -v perl pod/pod2man /tools/bin
mkdir -pv /tools/lib/perl5/5.8.8
cp -Rv lib/* /tools/lib/perl5/5.8.8 
```

关于这个软件包的详细资料位于节 6.22.2, Perl 的内容

# 5.27\. Sed-4.1.5

sed 是一个流编辑程序，在一个输入流(从一个文件或者一个管道的输入)上进行基本的文本编辑操作。

**预计编译时间：** 0.1 SBU**所需磁盘空间：** 6.1 MB

## 5.27.1\. 安装 Sed

为编译 Sed 做准备：

```
./configure --prefix=/tools 
```

编译软件包：

```
make 
```

要测试结果，请运行：**`make check`** 。

安装软件包：

```
make install 
```

关于这个软件包的详细资料位于节 6.20.2, Sed 的内容

# 5.28\. Tar-1.15.1

Tar 软件包含有一个归档程序，用来保存文件到归档文件或者从给定的 tar 归档文件中释放文件。

**预计编译时间：** 0.2 SBU**所需磁盘空间：** 13.7 MB

## 5.28.1\. 安装 Tar

如果想运行测试套件则需要使用下面的补丁修正一些与 GCC-4.0.3 相关的问题：

```
patch -Np1 -i ../tar-1.15.1-gcc4_fix_tests-1.patch 
```

为编译 Tar 做准备：

```
./configure --prefix=/tools 
```

编译软件包：

```
make 
```

要测试结果，请运行：**`make check`** 。

安装软件包：

```
make install 
```

关于这个软件包的详细资料位于节 6.53.2, Tar 的内容

# 5.29\. Texinfo-4.8

Texinfo 软件包包含读取、写入和转换 Info 文档的程序，以提供系统文档。

**预计编译时间：** 0.2 SBU**所需磁盘空间：** 16.3 MB

## 5.29.1\. 安装 Texinfo

为编译 Texinfo 做准备：

```
./configure --prefix=/tools 
```

编译软件包：

```
make 
```

要测试结果，请运行：**`make check`** 。

安装软件包：

```
make install 
```

关于这个软件包的详细资料位于节 6.54.2, Texinfo 的内容

# 5.30\. Util-linux-2.12r

Util-linux 软件包包含许多工具。其中比较重要的是加载、卸载、格式化、分区和管理驱动器，以及打开 tty 端口和处理消息。

**预计编译时间：** 少于 0.1 SBU**所需磁盘空间：** 8.9 MB

## 5.30.1\. 安装 Util-linux

Util-linux 默认不使用刚才安装在 `/tools` 目录下的头文件和库文件，我们更改配置脚本来修正这个问题：

```
sed -i 's@/usr/include@/tools/include@g' configure 
```

为编译 Util-linux 做准备：

```
./configure 
```

编译一些支持例程：

```
make -C lib 
```

我们只需要这个软件包中的少数几个工具，因此只需要编译这几个工具就可以了：

```
make -C mount mount umount
make -C text-utils more 
```

这个软件包没有附带测试程序。

把这些程序复制到临时工具目录：

```
cp mount/{,u}mount text-utils/more /tools/bin 
```

关于这个软件包的详细资料位于节 6.56.3, Util-linux 的内容

# 5.31\. 清理系统

本节的步骤是可选的，但如果 LFS 分区实在很小则除外；同时了解哪些东西是不必要的、可以删除的也是有好处的。到目前为止已经安装的可执行程序和库文件包含大约 70 MB 不必要的调试符号，运行下面的命令删除这些符号：

```
strip --strip-debug /tools/lib/*
strip --strip-unneeded /tools/{,s}bin/* 
```

上面的命令会跳过大约 20 个文件，报告不能识别这些文件格式，其中大多数是脚本而不是二进制文件。

*千万不要*在库文件上使用 *`--strip-unneeded`* ，否则会破坏其静态版本，这样你不得不又从头开始编译全部的工具链软件包。

删除文档还可以节省 20 MB 空间：

```
rm -rf /tools/{info,man} 
```

现在 `$LFS` 上就有至少 850 MB 剩余空间，可以在下一章编译安装 Glibc 。如果有足够空间编译安装 Glibc ，那编译安装其它的软件包也就没有问题了。

# 5.32\. 改变所有者

### 注意

本书剩余部分的命令必须以 `root` 用户登陆后执行而不再使用 `lfs` 用户了。同样，有必要再一次检查 `root` 用户环境下的 `$LFS` 环境变量是否被正确的设置了。

目前，`$LFS/tools` 目录的所有者是仅存在于宿主环境中的 `lfs` 用户。如果保留该目录，那么该目录内文件的所有者的 user ID 就没有对应的账号。这会带来安全上的问题，在以后创建一个用户帐号的时候，如果该用户帐号的 user ID 刚好与目录 `$LFS/tools` 所有者的 user ID 相同，那么该目录下的文件就会面临被恶意操作的危险。

为了避免这个问题，在后面建立 LFS 系统的时候，在创建 `/etc/passwd` 文件时添加与宿主系统的 user ID 和 group ID 相同的 `lfs` 用户。另外一个更好的办法是通过下面的指令把 `$LFS/tools` 目录以及其中文件的所有者改为 `root` 用户：

```
chown -R root:root $LFS/tools 
```

虽然在 LFS 系统完成的时候，可以把 `$LFS/tools` 目录删除，但是它还可以再用来建立多个*相同版本*的 LFS 系统，所以很多人会选择保留该目录。如何以最好的方法备份 `$LFS/tools` 目录取决于个人喜好，我们把这个任务留给读者作为练习。