---
layout: post
title: "Install ArchLinux to USB media or Virtual Machine"
date: "2015-12-29"
category: config
tags: [archlinux,media]
---


*****
# 是的，考完了，考完了，考完了！
*****

*****
### 那就来装个系统吧。
1. 打开VMware，新建一个虚拟机，使用`archlinux-2015.12.01-dual.iso`(连上教育网去北交的镜像站下载)，作为启动安装盘，新建一个10G的硬盘
<!--more-->
2. `fdisk -l`  `fdisk /dev/sda` 新建一个100M分区，剩下再建一个分区。（这里也不一定是要boot分区，当然你也可以新建/home分区）
3. 格式化  `mkfs.ext4 /dev/sda1`   `mkfs.ext4 /dev/sda2` 然后可以用`lsblk`查看一下分区结构
4. 挂载  `mount /dev/sda1 /boot`   `mount /dev/sda2 /mnt`
5. 编辑源 `vi /etc/pacman.d/mirrorlist`   `467 dd` 删除前面的把最后一行改成北京交通大学的源 `Server = http://mirror.bjtu.edu.cn/archlinux/$repo/os/$arch`
6. 安装基本的系统 `pacstrap -i /mnt base base-devel vim`
7. 生成挂载信息 `genfstab -U -p /mnt >> /mnt/etc/fstab`
8. chroot 到新系统 `arch-chroot /mnt`
9. 写入主机名 `echo xxx > /etc/hostname`
10. 生成文字编码 `vim /etc/locale.gen` 将`zh_CN`打头的 以及 `en_US.UTF8 UTF8` 取消注释 运行`locale-gen`
11. 如果要安装到***usb***移动介质 就将`/etc/mkinitcpio.conf`里面的`HOOKS`加上 `block` 然后 `mkinitcpio -p linux`，这是用来生成内核的img文件的。
12. 然后设置密码 `passwd`
13. 安装引导
  * 安装syslinux：`pacman -S syslinux` 生成引导：`syslinux-install_update -iam`,如果是usb介质 就还要将 `/boot/syslinux/syslinux.cfg` 里面的`arch archfallback` 的`APPEND` 中的`sdx`改成 `root=UUID=xxxx rw` 因为这样才能找到U盘 xxxx是U盘的***/mnt所在分区的UUID***。UUID 查看:`ls -l /dev/disk/by-uuid`
  * 安装grub：`pacman -S grub`，安装引导:`grub-install /dev/sda`，生成引导`grub-mkconfig  -o  /boot/grub/grub.cfg`。如果想要引导windows的话安装os-probe(先安装ntfs-3g):`pacman -S os-prober`,生成配置文件:`os-prober`,生成引导文件：`grub-mkconfig -o /boot/grub/grub.cfg`

14. 安装一些必要的工具 `net-tools git zsh i3 xorg-server xorg-server-utils xorg-xinit xterm`，后面几个是xwindow跑起来的必要条件，安装完之后你就可以startx来启动x界面。这时显示的是三个xterm的白色背景的终端，要想使用i3：直接 `vim .xinitrc` 写入 `exec i3`，这样startx出来的就是i3的配置向导了。

15. 添加用户:`useradd -m -G wheel -s /bin/zsh kqf`，设置密码：`passwd kqf`，添加sudo权限`visudo`，在 root ALL=(ALL) ALL 下面加一行 kqf ALL=(ALL) ALL

16. 联网，使用netctl通过wifi联网要借助wifi-menu方便一些，先安装wifi-menu的依赖 `pacman -S dialog wpa_supplicant`，生成配置文件`sudo wifi-menu wlp3s0`，查看配置文件`netctl list`，联网前要先把无线接口关闭`sudo ifconfig wlp3s0 down`，然后连接`sudo netctl start wifi-kkk`，设置开机自动连接wifi`sudo enable netctl.service`。

17. 安装驱动，`pacman -S alsa-utils xf86-input-synaptics nvidia`分别是声卡，触摸板，显卡驱动（视自己情况而定）。

18. 安装yaourt，添加Yaourt源到 /etc/pacman.conf，`vim /etc/pacman.conf` 然后添加`[archlinuxfr]
Server = http://repo.archlinux.fr/(i686|x86_64)`，根据自己电脑是32位还是64位选择i386和x86_64，然后同步后安裝：`pacman -Syu yaourt`。添加archlinucn的源`Server = http://mirrors.tuna.tsinghua.edu.cn/archlinuxcn/$arch`，记得设置`SigLevel = Optional TrustAll`否则可能会有签名错误。

19. 启动到xwindow。
  * 使用tty登录后，输入 `startx`启动xwindow。
  * startx启动是去检查~/.xinitrc或者/etc/X11/xinit/xinitrc文件里的启动脚本，你可以在.bashrc 里面添加相应的脚本，让用户在tty登录后就能启动xwindow。
  * 使用lightdm（desktop manage）显示管理器来管理。安装lightdm`pacman -S lightdm-gtk-greeter`，使能`systemctl enable lightdm`，启动`systemctl start lightdm`，这样就能看到一个登录界面了，输入密码后就可以登录了。
  * 启动自动登录，编辑lightdm的配置文件`vim /etc/lightdm/lightdm.conf`填入
  {% codeblock  lang:config %}
    [SeatDefaults]
    pam-service=lightdm-autologin
    autologin-user=username
    autologin-user-timeout=0
    session-wrapper=/etc/lightdm/Xsession
  {% endcodeblock %}
  LightDM 能通过 PAM 即使 autologin 已启用。你必须是 autologin 组的成员来使得自己登录时不用输入密码。所以添加用户组`groupadd autologin`,`gpasswd -a username autologin`。
  * 显示管理器是用于在操作系统启动时，能自动启动对应的桌面环境

### 在虚拟机里面装没改`syslinux.cfg`发现还是错了 找不到`/dev/sda3` 又是在虚拟机上安装的 怎么办
1. 开机按`F2`然后将光盘的启动优先级放到最高 然后再次进到live里面
2. 按照之前的方法挂载 然后 `arch-chroot /mnt` 然后就把`/boot/syslinux/syslinux.cfg`里面的`/sda3` 改成 `/sda2` 就ok


#### 补充几个可能用到的东西
> 1. `pacman -S openssh` 安装ssh服务,如何配置ssh（d）可以`man sshd_config`（`man ssh_config`）
> 1. `systemctl enable/start sshd.service` (开机)启动ssh服务，
> 1. `/etc/ssh/sshd_config` ssh配置文件默认不允许root用户登录 prohibit-password -> yes
> 1. `systemctl enable dhcpcd.service` 开机自动DHCP获取地址
> 1. `sh -c "$(curl -fsSL https://raw.github.com/robbyrussell/oh-my-zsh/master/tools/install.sh)" ` 使用oh-my-zsh配置zsh
> 1. `sudo pacman -S bash-completion` 增强bash的Tab自动补全功能
> 1. `sudo pacman -S google-chrome` 安装谷歌浏览器
> 1. `sudo pacman -S shadowsocks`安装shadowsocks，默认配置文件在`/etc/shadowsocks/xxx.json`
> 1. `systemctl start shadowsocks@xxx`开启服务，`systemctl enable shadowsocks@xxx`开机自启动,`journalctl -u shadowsocks@xxx`来查询日志,具体参考[这里](https://wiki.archlinux.org/index.php/Shadowsocks_(%E7%AE%80%E4%BD%93%E4%B8%AD%E6%96%87))
> 1. 修改语言:在.zshrc或.bashrc里面添加环境变量`export LANG=zh_CN.UTF-8`
> 1. 安装字体:`pacman -S wqy-zenhei ttf-fireflysung(flash乱码) `以防止中文乱码
> 1.
> 1.
> 1. 持续更新中..........


### 问题解决
1. 遇到`a stop job is running for session c1 of user root 1 min 30 s`关机很慢的问题，这是systemd的bug，详细可以参考[这篇文章](https://bbs.archlinux.org/viewtopic.php?id=203112)按照里面22楼的说法把`/etc/systemd/system.conf`里面的`DefaultTimeoutStopSec=90s`改成了1s。因为不想降级了，所以只能这样了。

2. 安装chrome的时候出现`key xxx could not be looked up remotely`的错误。经查是key server的问题，换server：修改`vim /etc/pacman.d/gnupg/gpg.conf`中的`keyserver hkp://pool.sks-keyservers.net`为`keyserver hkp://pgp.mit.edu`。
3. 解析不了主机，修改DNS配置 `vim /etc/resolv.conf` 添加一行 `nameserver 114.114.114.114`
4. 虚拟机和主机无法共享剪切板，安装vm-tools，运行`/etc/init.d/vmware-tools start``/etc/init.d/vmware-tools-thinprint start`然后运行`vmware-user start`然后运行`vmware-user start`解决（可以添加到.zshrc）。使用官方的CDROM会找不到rc0.d文件夹（arch不靠这个）就算你自己创建也不会被自动启动，所以要手动运行。更好的解决办法等待探索。
