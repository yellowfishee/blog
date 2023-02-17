+++
lastmod = 2023-02-17T16:59:37+08:00
draft = false
author = "yellowfishe"
+++

## Blog Ideas {#blog-ideas}


### <span class="org-todo todo TODO">TODO</span> first blog {#first-blog}


### <span class="org-todo done DONE">DONE</span> win11 emacs 安装 eaf 插件 <span class="tag"><span class="emacs">emacs</span></span> {#win11-emacs-安装-eaf-插件}


#### 简单介绍一下EAF {#简单介绍一下eaf}

\#+begin_quote
EAF 是一个可编程扩展的框架，它自带一系列丰富的应用，你也可以进入[EAF](https://github.com/emacs-eaf/emacs-application-framework)项目首页进行浏览，
\#+end_quote&gt;


#### 安装EAF {#安装eaf}

这里主要记录一下出的错误，首先根据官方给出的README进行操作：
`git clone --depth=1 -b master https://github.com/emacs-eaf/emacs-application-framework.git ~/.emacs.d/site-lisp/emacs-application-framework/`

由于windows下的cmd中使用cd ~打不开home，所以你需要在环境变量中设置一个HOME路径，我设置的路径是C:/user/username/AppData/Roaming这个路径，然后使用gitbash进行git clone
我这里没使用过powershell，后续测试一下。

克隆好之后我们进入目录，然后./intsall-eaf.py，这里我踩了一个坑，最开始我一直使用的是gitbash安装，使用python install-eaf.py的时候一直错误，这里改到powershell使用./isntall-eaf.py之后使用正常，同时这里需要有git，py3，node，npm依赖，然后需要安装wiki中给出的依赖。
\#+begin_src python
pip install epc
pip install wmctrl
pip install pygetwindow
\#+end_src&gt;

然后基本上就可以安装完毕了，后续见官方文档即可，报错都在\*eaf\*buffer下面


### <span class="org-todo done DONE">DONE</span> DJI MSDK <span class="tag"><span class="DJI">DJI</span></span> {#dji-msdk}


### 背景 {#背景}

由于工作需要所以要接触到DJI的无人机开发，这里做一点简短的笔记来记录一下遇见过的一些问题


### 开始 {#开始}


### 环境 {#环境}

简单介绍一下本次使用的环境，由于另外一台电脑没有过多的配置，所以采用的直接就是Audroid Studio自带的配置，这里说一下主要出现的问题。

1.  missing 31 d8.bat
    这个是因为31版本之后就没有这个文件了，所以我们可以在给出的SDK文件中将改变的文件进行一下名字的改动，就可以解决这个问题
2.  ndk not found
