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
2. ubuntu 中一些软件的源
  * nginx： `sudo add-apt-repository ppa:nginx/stable`
  * nodejs
  * atom
  * lll
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
    * DigitalOcean 的主机，装有两个版本的Python，如果命令行Python版本错误，应该修改 `/usr/local/bin` 里面的Python，而如果因为Python版本不对导致dpkg报错的话应该修改`/usr/bin`里面的Python。
    * kk
