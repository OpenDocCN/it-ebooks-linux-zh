# 二、插件管理

## 2 插件管理

既然本文主旨在于讲解如何通过插件将 vim 打造成中意的 C/C++ IDE，那么高效管理插件是首要解决的问题。

vim 自身希望通过在 .vim/ 目录中预定义子目录管理所有插件（比如，子目录 doc/ 存放插件帮助文档、plugin/ 存放通用插件脚本），vim 的各插件打包文档中通常也包含上述两个（甚至更多）子目录，用户将插件打包文档中的对应子目录拷贝至 .vim/ 目录即可完成插件的安装。一般情况下这种方式没问题，但我等重度插件用户，.vim/ 将变得混乱不堪，至少存在如下几个问题：

*   插件名字冲突。所有插件的帮助文档都在 doc/ 子目录、插件脚本都在 plugin/ 子目录，同个名字空间下必然引发名字冲突；
*   插件卸载易误。你需要先知道 doc/ 和 plugin/ 子目录下哪些文件是属于该插件的，再逐一删除，容易多删/漏删。

我希望每个插件在 .vim/ 下都有各自独立子目录，这样需要升级、卸载插件时，直接找到对应插件目录变更即可；另外，我希望所有插件清单能在某个配置文件中集中罗列，通过某种机制实现批量自动安装/更新/升级所有插件。vundle（[`github.com/VundleVim/Vundle.vim`](https://github.com/VundleVim/Vundle.vim) ）为此而生，它让管理插件变得更清晰、智能。

vundle 会接管 .vim/ 下的所有原生目录，所以先清空该目录，再通过如下命令安装 vundle：

```
git clone https://github.com/VundleVim/Vundle.vim.git ~/.vim/bundle/Vundle.vim 
```

接下来在 .vimrc 增加相关配置信息：

```
" vundle 环境设置
filetype off
set rtp+=~/.vim/bundle/Vundle.vim
" vundle 管理的插件列表必须位于 vundle#begin() 和 vundle#end() 之间
call vundle#begin()
Plugin 'VundleVim/Vundle.vim'
Plugin 'altercation/vim-colors-solarized'
Plugin 'tomasr/molokai'
Plugin 'vim-scripts/phd'
Plugin 'Lokaltog/vim-powerline'
Plugin 'octol/vim-cpp-enhanced-highlight'
Plugin 'nathanaelkane/vim-indent-guides'
Plugin 'derekwyatt/vim-fswitch'
Plugin 'kshenoy/vim-signature'
Plugin 'vim-scripts/BOOKMARKS—Mark-and-Highlight-Full-Lines'
Plugin 'majutsushi/tagbar'
Plugin 'vim-scripts/indexer.tar.gz'
Plugin 'vim-scripts/DfrankUtil'
Plugin 'vim-scripts/vimprj'
Plugin 'dyng/ctrlsf.vim'
Plugin 'terryma/vim-multiple-cursors'
Plugin 'scrooloose/nerdcommenter'
Plugin 'vim-scripts/DrawIt'
Plugin 'SirVer/ultisnips'
Plugin 'Valloric/YouCompleteMe'
Plugin 'derekwyatt/vim-protodef'
Plugin 'scrooloose/nerdtree'
Plugin 'fholgado/minibufexpl.vim'
Plugin 'gcmt/wildfire.vim'
Plugin 'sjl/gundo.vim'
Plugin 'Lokaltog/vim-easymotion'
Plugin 'suan/vim-instant-markdown'
Plugin 'lilydjwg/fcitx.vim'
" 插件列表结束
call vundle#end()
filetype plugin indent on 
```

其中，每项

```
Plugin 'dyng/ctrlsf.vim' 
```

对应一个插件（这与 go 语言管理不同代码库的机制类似），后续若有新增插件，只需追加至该列表中即可。vundle 支持源码托管在 [`github.com/`](https://github.com/) 的插件，同时 vim 官网 [`www.vim.org/`](http://www.vim.org/) 上的所有插件均在 [`github.com/vim-scripts/`](https://github.com/vim-scripts/) 有镜像，所以，基本上主流插件都可以纳入 vundle 管理。具体而言，仍以 ctrlsf.vim 为例，它在 .vimrc 中配置信息为 dyng/ctrlsf.vim，vundle 很容易构造出其真实下载地址 [`github.com/dyng/ctrlsf.vim.git`](https://github.com/dyng/ctrlsf.vim.git) ，然后借助 git 工具进行下载及安装。

此后，需要安装插件，先找到其在 github.com 的地址，再将配置信息其加入 .vimrc 中的 call vundle#begin() 和 call vundle#end() 之间，最后进入 vim 执行

```
:PluginInstall 
```

便可通知 vundle 自动安装该插件及其帮助文档。比如，我在 .vimrc 中添加了 4 个插件：

```
Plugin 'VundleVim/Vundle.vim'
Plugin 'altercation/vim-colors-solarized'
Plugin 'Lokaltog/vim-powerline'
Plugin 'octol/vim-cpp-enhanced-highlight' 
```

当前，.vim/ 为空，当我在 vim 中执行 :PluginInstall 时，整个自动安装过程便开始了：

![](img/vundle%20 批量安装插件.gif)
（vundle 批量安装插件）要卸载插件，先在 .vimrc 中注释或者删除对应插件配置信息，然后在 vim 中执行

```
:PluginClean 
```

即可删除对应插件。插件更新频率较高，差不多每隔一个月你应该看看哪些插件有推出新版本，批量更新，只需执行

```
:PluginUpdate 
```

即可。

你得注意插件的下载源。同名插件在 github.com 上可能有多个，比如，indexer 插件，至少就有 [`github.com/vim-scripts/indexer.tar.gz`](https://github.com/vim-scripts/indexer.tar.gz) 、[`github.com/everzet/vim-indexer`](https://github.com/everzet/vim-indexer) 、[`github.com/shemerey/vim-indexer`](https://github.com/shemerey/vim-indexer) 等三个，到底应该选哪个呢？以我的经验来看，对于钟意的插件，我会先找其作者的个人网站，上面通常会罗列出托管在 github.com 的具体地址；若没有，我会找该插件在 vim.org 的页面，上面也会有 github.com 托管地址；若还是没有，再以 github 和插件名作为关键字搜索，点赞数多的，通常是你想找的。为节约你找插件地址的时间，本文中出现的每个插件我都会附上其地址。非特殊情况，后文介绍到的插件不再累述如何安装。

通过 vundle 管理插件后，切勿通过发行套件自带的软件管理工具安装任何插件，不然 .vim/ 又要混乱了。