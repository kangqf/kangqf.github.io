---
title: ubuntu note
categories:
  - config
tags:
  - note
  - ubuntu
date: 2016-01-03 16:13:54
---

### 这篇文章主要是用来记录在ubuntu使用过程中遇到的问题及其解决方法的，持续更新...
<!--more-->
1. ubuntu 的源
  * 源的了解十分重要关系到软件更新时包的依赖关系等，如果源没有设置对很有可能会导致软件无法安装或者更新，更无法升级，并且源的依赖各种错乱。举个典型的例子是，你用的是trusty版本的系统，但是你配置了vivid版本的源，然后你更新了软件索引，并且更新了一部分软件，当你安装软件的时候就会出现各种复杂的依赖错误。原因是你的安装软件的依赖的基础库的版本高于要求版本，而这又不能被自动降级，因为一些你刚刚更新的软件需要这么高级版本的依赖库。
  * ubuntu 发行版本。对与不同的发行版本会有不同的源的维护，比如像LTS的发行版会提供5年的支持，而其他的可能就是很短时间就不提供支持了。所以建议用于开发的电脑可以选择LTS的发行版。Ubuntu每半年发布一个版本，分别在4月、10月,每两年发布一个LTS长期维护版本。ubuntu为每个发行版提供一个代号便于维护,，代号也很有意思，每次都是一个形容词加一个动物类名词，且两个单词的首字母相同，在设置源的时候就是用第一个单词来代表对应的版本。下面列出近几个发行版与它的代号的对应关系（[参考这里](http://news.mydrivers.com/1/423/423871.htm)）：
    * Ubuntu 12.04 - Precise Pangolin(精准的穿山甲) LTS
    * Ubuntu 12.10 - Quantal Quetzal(量子的绿咬鹃)
    * Ubuntu 13.04 - Raring Ringtail(铆足了劲的猫熊)
    * Ubuntu 13.10 - Saucy Salamander(活泼的蝾螈)
    * Ubuntu 14.04 - Trusty Tahr (可靠的塔尔羊) LTS
    * Ubuntu 14.10 - Utopic Unicorn（乌托邦独角兽)
    * Ubuntu 15.04 - Vivid Vervet(活泼的小猴)
    * Ubuntu 15.10 - Wily Werewolf(狡诈的狼人)
    * Ubuntu 16.04 - Xenial Xerus（好客的非洲地松鼠） LTS
  * Ubuntu 软件。Ubuntu 为不同的软件分类以便用户选择是否使用这些类别的软件。
    * main         - `Canonical`官方支持的免费和开源软件。
    * universe     - 社区维护的免费和开源软件
    * restricted   - 设备专有驱动
    * multiverse   - 有版权和合法性问题的软件
    * deb-src      - 源代码
  * Ubuntu 软件更新。Ubuntu 同样对其更新进行了分类，以便用户选择。
    * security      - 重要安全更新
    * updates       - 推荐更新
    * proposed      - 提前释放出的更新
    * backports     - 不支持的更新
  * 架构，不同的架构对应的源也不同，就拿Arm的ubuntu源来说吧，找了很多的高校的镜像都没有，最后在清华大学的镜像站找到了，值得一提的是清华大学的镜像站确实挺全的，连msys2的源都有。我们常见的架构主要有 `i386`、`amd64`、`armel`、`armhf`。我们可以参考[这里](https://segmentfault.com/a/1190000000503041)来使用一些公共的源。
  * 举例来说源。我们就拿清华大学的ubuntu镜像来说，主站是[http://mirrors.tuna.tsinghua.edu.cn/](http://mirrors.tuna.tsinghua.edu.cn/) 进入我们看到有三个与ubuntu有关的目录：
    * ubuntu - 是i386及amd64的主机的源，同步自[http://archive.ubuntu.com/ubuntu](http://archive.ubuntu.com/ubuntu)
    * ubuntu-ports - 是armel以及armhf架构的源，同步自 [http://ports.ubuntu.com/ubuntu-ports/](http://ports.ubuntu.com/ubuntu-ports/)
    * ubuntu-releases - ubuntu 各个发行版的ISO镜像以及一些md5校验信息。

    然后我们进入ubuntu-ports或是ubuntu就能看到这样的结构
    >  ../
    >  dists/     - 各个版本的五种软件分类的软件更新信息存放目录
    >  indices/   - 存放软件索引信息的目录
    >  pool/      - 用于实际存储所有新的软件包
    >  project/   - 存放软件签名信息的目录
    >  ls-lR.gz   - 将命令ls -lR的输出重定向到得到的一个文本文件，相当于文件信息数
据库

    我们进入`dists`目录，可以看到各个版本的软件信息，比如拿`trusty`来看，会有五个目录（参考上面的更新分类）：
    > trusty/           - 14.04版本的主要软件信息目录
    > trusty-backports/ - 14.04版本的不支持的更新
    > trusty-proposed/  - 14.04版本的提前释放出的更新
    > trusty-security/  - 14.04版本的重要安全更新
    > trusty-updates/   - 14.04版本的推荐更新

    我们再进入其中一个目录可以看到类似下面的目录（参考上面的软件分类信息）：
    > ../
      main/
      multiverse/
      restricted/
      universe/
      Contents-arm64.gz   - 对应架构的所有软件信息
      Contents-armhf.gz   -
      Release             - md5 校验码
      Release.gpg         - 签名信息

    这样我们就得到了一个软件源的基本结构，我们的source.list写到上面这一层就够了。比如我们的一个源配置是这样的：
    ```
    deb http://archive.ubuntu.com/ubuntu/ trusty-security main restricted universe multiverse
    deb-src http://archive.ubuntu.com/ubuntu/ trusty-security main restricted universe multiverse
    ```
    按照上面的说法进行理解就是，我们配置了ubuntu 14.04 版本的源的二进制文件和源代码文件。其中只允许获取所有的更新分类中的重要安全更新分类。并且允许获取该更新分类下的所有的软件分类信息。

2. ubuntu 中一些软件的源
  * nginx： `sudo add-apt-repository ppa:nginx/stable`
  * nodejs： `sudo add-apt-repository ppa:chris-lea/node.js`
  * atom： `sudo add-apt-repository ppa:webupd8team/atom`
  * ap-hotspot: `sudo add-apt-repository ppa:nilarimogard/webupd8`

3. 禁用（启用）一些软件的升级
  * 首先软件的状态主要是有
    * unknown – 不知道
    * install – 安装或升级
    * remove – 删除软件，不删除配置文件
    * purge – 完全删除软件，包括配置文件
    * hold – 保持现状
  * `sudo echo qemu hold | dpkg --set-selections`这个命令维持软件包的版本不变。
  * `sudo echo qemu install | sudo dpkg --set-selections`这个命令会在用户upgrade的时候更新软件包。
4. 定时任务备份自己的命令历史以待以后查阅
5. 一些常见的配置文件
  * DNS 配置文件：/etc/resolv.conf
  * 主机名： /etc/hostname
  * DigitalOcean 的主机，装有两个版本的Python，如果命令行Python版本错误，应该修改 `/usr/local/bin` 里面的Python，而如果因为Python版本不对导致dpkg报错的话应该修改`/usr/bin`里面的Python。而shadowsocks用的好像是用的`/usr/bin` 里面的python。
6. 一些常见的问题的解决。
  * `watch -n 5 pkill -USR1 ^dd$` 每5秒输出dd的进度 - [参考](http://blog.csdn.net/xyz846/article/details/7367962)
  * 更新软件后导致网络管理器无法使用：`sudo apt-get install libnl-3-200=3.2.21-1 libnl-route-3-200=3.2.21-1 libnl-genl-3-200=3.2.21-1` 这是一个BUG - [参考这里](http://askubuntu.com/questions/727127/last-upgrade-crashes-network-manager-no-internet-connection-no-applet)
  * ubuntu 回滚到旧内核：
    * `dpkg --get-selections|grep linux ` 查看安装的内核版本
    * `sudo apt-get remove linux-headers-3.13.0-85-generic linux-image-3.13.0-85-generic linux-image-extra-3.13.0-85-generic `卸载旧的内核
    * `sudo find ../bits -mtime -7 -type f/d -print | xargs sudo  rm -f `查找7天之内修改的文件或文件夹然后删除之
  
