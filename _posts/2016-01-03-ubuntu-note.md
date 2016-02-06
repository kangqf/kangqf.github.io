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
  * 源的了解十分重要关系到软件更新时包的依赖关系等，如果源没有设置对很有可能会导致软件无法安装或者更新，更无法升级，并且源的依赖各种错乱。
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
    * main - `Canonical`官方支持的免费和开源软件。
    * universe - 社区维护的免费和开源软件
    * restricted - 设备专有驱动
    * multiverse - 有版权和合法性问题的软件
    * deb-src - 源代码
  * Ubuntu 软件更新。Ubuntu 同样对其更新进行了分类，以便用户选择。
    * security - 重要安全更新
    * updates - 推荐更新
    * proposed - 提前释放出的更新
    * backports - 不支持的更新
  

2. ubuntu 中一些软件的源
  * nginx： `sudo add-apt-repository ppa:nginx/stable`
  * nodejs： `sudo add-apt-repository ppa:chris-lea/node.js`
  * atom： `sudo add-apt-repository ppa:webupd8team/atom`

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
