---
layout: post
title: "Install ArchLinux to USB media or Virtual Machine"
date: "2015-12-29"
category: tips
tags: [archlinux,virtualmachine,media]
---


*****
# 是的，考完了，考完了，考完了！
*****

*****
### 那就来装个系统吧。
1. 打开VMware，新建一个虚拟机，使用`archlinux-2015.12.01-dual.iso`(连上教育网去北交的镜像站下载)，作为启动安装盘，新建一个10G的硬盘
2. `fdisk -l`  `fdisk /dev/sda` 新建一个100M分区，剩下再建一个分区。
3. 格式化  `mkfs.ext4 /dev/sda1`   `mkfs.ext4 /dev/sda2`
4. 挂载  `mount /dev/sda1 /boot`   `mount /dev/sda2 /mnt`
5. 编辑源 `vi /etc/pacman.d/mirrorlist`   `467 dd` 删除前面的把最后一行改成北京交通大学的源 `Server = http://mirror.bjtu.edu.cn/archlinux/$repo/os/$arch`
6. 安装基本的系统 `gacstrap -i /mnt base base-devel vim`
7. 生成挂载信息 `genfstab -U -p /mnt >> /mnt/etc/fstab`
8. chroot 到新系统 `arch-chroot /mnt`
9. 写入主机名 `echo xxx > /etc/hostname`
10. 生成文字编码 `vim /etc/locale.gen` 将`zh_CN`打头的 以及 `en_US.UTF8 UTF8` 取消注释 运行`locale-gen`
11. 如果要安装到***usb***移动介质 就将`/etc/mkinitcpio.conf`里面的`HOOKS`加上 `block` 然后 `mkinitcpio -p linux`
12. 然后设置密码 `passwd`
13. 安装 `syslinux` 生成引导 `syslinux-install_update -iam`<br/>
如果是usb介质 就还要将 `/boot/syslinux/syslinux.cfg` 里面的`arch archfallback` 的`APPEND` 中的`sdx`<br/>
改成 `root=UUID=xxxx rw` 因为这样才能找到U盘 xxxx是U盘的***/mnt所在分区的UUID***

14. 安装一些必要的工具 `net-tools git zsh i3 xorg-server xorg-server-utils xorg-xinit xterm`

### 在虚拟机里面装没改`syslinux.cfg`发现还是错了 找不到`/dev/sda3` 又是在虚拟机上安装的 怎么办
1. 开机按`F2`然后将光盘的启动优先级放到最高 然后再次进到live里面
2. 按照之前的方法挂载 然后 `arch-chroot /mnt` 然后就把`/boot/syslinux/syslinux.cfg`里面的`/sda3` 改成 `/sda2` 就ok
#### 把启动项改回来，启动 `dhcpd` 获取 ip ，然后 `startx` 竟然没起来。。。无奈 `pacman -S xorg` 然后回车全选，确定安装然后***还没起来*** **怒了** 直接 `vim .xinitrc` 写入 `exec i3` 再`startx`就把 i3 跑起来了。补充几个可能用到的东西
> `wifi-menu`(包含在`dialog`中) -> 用来配合`netctl`连WiFi
> 持续更新中..........
