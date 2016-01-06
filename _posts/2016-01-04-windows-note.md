---
title: windows note
categories:
  - tips
tags:
  - note
  - windows
date: 2016-01-04 19:11:09
---

#### 这篇文章主要是用来记录在windows使用过程中遇到的问题及其解决方法的，持续更新...
##### 今天和小伙伴一起打算做个东西，结果发现以前搭过的环境，即使在当时熟到透现在很多都忘了，所以发现写写文章还是必要的。所以从今天起会在这篇文章中记录windows平台的一些使用。由于今天小伙伴说为了连VPS下了个putty结果中了个全家桶，，，，，真是悲催，所以就从命令行开始吧。

1. ### 在windows下使用命令行。
  1.  #### ***babun***。今天在小伙伴的电脑上使用一个不知名的终端简直难用死了，连Backspace都不能使（我肯定在vim里面配了的，估计是开发者偷懒没有定义一部分宏），vim提示也没有，还一卡一卡的，简直不能忍，对比我的babun，各种兼容，所以第一个推荐大家使用的就是它了。我是做薪火杯的时候装的了都忘记了，所以参考了网上的一些文章。刚好把它卸载（就是把.babun 文件夹全删了:) ）了，重新配一下。
    1. 关于babun。 介绍就自己去[babun的官网](http://babun.github.io/)看了,反正很好用，之前我装的时候谷歌搜不到几篇文章现在一搜就是一把。
    2. 安装。官网下载安装包`babun-1.2.0-dist`，然后找个地方解压了，你会发现里面有个`install.txt`,里面写着`In order to install babun just run the install.bat script`意思是你直接点install.bat 就是了，他会默认安装到`C:/Users/xxx/.babun`下面就是我之前直接删除的那个目录，然后里面还有一句是这样的`To install in a different directory, just use the /t switch (or /target) e.g install.bat /t "D:\target_folder" `意思是你可以自己指定安装目录（安装目录不要有空格），这应该是新版本的特性，对于我这种对电脑每个文件的作用都想了如指掌的人来说当然是极好的。所以我在解压的文件夹下面打开命令行，`install.bat /t "C:\ProgramFiles\babun"`然后就开始安装了，会输出一系列的信息，最后会在.babun文件夹生成在我们刚刚指定的目录下面，里面就是我们的babun了，进去.babun,里面有个`babun.bat`双击它就可以运行了（安装完也会自动弹出并生成快捷方式）然后把它固定到开始菜单吧 :)，新版本里面还有`uninstall.bat`再也不用手动删目录了，另外新版本还增加了`Now you can open babun directly in any folder by right clicking your mouse and choosing the Open Babun here option.`就是你可以在任何文件夹上右击打开babun 。
    3. 个性化。首先修改颜色，你在标题栏右击，然后options，里面可以设置，初始的背景色是`E:160 S:0 L:26 R:28 G:28 B:28 `我把它改成`E:239 S:74 L:60 R:83 G:44 B:45 `，同样的前景色初始是`E:160 S:0 L:196 R:208 G:208 B:208 `把它改成`E:68 S:232 L:120 R:79 G:251 B:4 `，把透明度改成`low`，把光标改成`underscope`，把窗口改成120x50（Alt+F11可以全屏），把字体改成Courier new。接下来就是中文乱码的问题，这个问题困扰了我很久，网上大多数的解决方法是把编码改成gbk，然后终端会出现一个禄字替换就是了，这样的话使用cmd的一些命令（ipconfig、nslookup）就不会出现中文乱码，但这样的话如果有中文名的文件ls的时候就会乱码了（不改前是不会的），可能是鱼和熊掌不可得兼吧。我还是最终选用了前一种，毕竟我要在命令行里处理中文名的文件的概率极小。然后是去除那个禄字见[参考的方法](http://0x0d.im/archives/windows-build-linux-like-environment.html)，另外附上一个[修改ZSH主题的方法](https://prinzeugen.net/customize-zsh-prompt/#more-1884)。
    4. 安装软件。接下来就可以自行安装软件了，在这之前你可以先修改一下源`vim ~/.pact/pact.repo`，把`#PACT_REPO=http://mirrors.kernel.org/sourceware/cygwin/` 修改成`PACT_REPO=http://mirrors.163.com/cygwin/` 我修改后貌似会出现MD5校验错误，这个自己看着办，其实官方的源也挺快的。babun的包管理和ubuntu的很像，就是一个`pact`命令，你输入这个命令后他就会告诉你怎么用了。下面是网上给出的一些常用软件，另外像`autojump colored-man zsh_reload zsh-syntax-highlighting git git-flow ruby gem python pip node npm bower`这些插件参见后面的 **Babun 配置** 这篇文章里面写的很详细。
      > pact install tmux        #安装tmux
      pact install screen      #安装screen 有了这两个不用conEmu也可以了  
      pact install zip         #安装zip
      pact install lftp        #lftp命令
      pact install p7zip       # p7zip命令
      pact install connect-proxy # 基于openssh的socks https代理
      pact install util-linux    #安装linux基础命令行工具 more/col/whereis等等命令
      pact install bind-utils    #安装dig命令
      pact install inetutils     #安装Telnet等常用网络命令
      pact install python        #python环境
      pact install python-crypto #python 环境
    5. 使用Conemu。自己在[官网](https://conemu.github.io/)下载然后安装，安装好后参考 **windows下搭建类linux环境** 进行配置并进行一下个性化设置即可。
    6. 使用tmux/screen 这两货略微复杂，很有必要拿出一篇单独的文章来讲，这里可以先参考那两篇关于tmux的文章。

    参考文章：
    [Windows 系统下的开发环境搭建](http://blog.yuanbin.me/posts/2015/07/Dev-Environment-in-Windows.html)
    [Babun 配置](http://www.rxna.cn/post/wiki/babun-pei-zhi)
    [windows下搭建类linux环境](https://www.52os.net/articles/windows-install-unix-like-command-tools.html)
    [终端环境之tmux](http://foocoder.com/blog/zhong-duan-huan-jing-zhi-tmux.html/)
    [Tmux 入门介绍](http://blog.jobbole.com/87278/)

  2. #### ***msys2***。
