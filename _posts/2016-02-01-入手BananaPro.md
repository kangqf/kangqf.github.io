---
title: 入手BananaPro
categories:
  - config
tags:
  - BananaPro
  - 嵌入式
  - 毕设
date: 2016-02-01 22:39:23
---

### 这篇应该是很久很久以前就写好的，但是由于各种原因导致直至今日才开始发。由于之前在`lemaker`公司申请了一块`BananaPro`说好要在它的论坛发帖，可是之前那人告诉我等论坛改版后再发，后面就一拖再拖，直到现在。而且为了履行自己的承诺，我选择的毕业设计也是这方面有关的，因为最害怕被人家说成骗板子的了，之后的文章会有很多与`BananaPro`有关的内容。
<!--more-->
1. 拿到板子,本来说是打错标签了，结果管理员还是挺守信的发了一块板子过来，大谢。
2. 系统，由于本人熟悉ubuntu所以首选当然是Lubuntu了，关于如何刷入系统，官网有详细的教程(建议把`Banana Pro & Pi User Manual-V1.0.pdf`详细读一遍)，注意选择tf卡，在经济允许的情况下尽量选择高速卡，经过我之后的使用发现这个`SBC`的主要短板就是tf卡。顺便介绍一下tf卡的知识（主要参考维基百科）
  * MMC、SD、miniSD、MicroSD、SDIO。首先，MMC最先出现，然后是SD，再是SDIO，所以在ubuntu里面插在`MMC/SD/SDIO插槽`都被识别成mmc。一般的笔记本都是一个读卡器接口都能插`MMC、SD、SDIO`，而有的（像我的ThinkPad e430）还能插`MS卡`和`MSPro卡`。对于miniSD和MicroSD(TF)目前也就是卡的大小不一样了（SD>miniSD>TF），都可以通过一个转换器变成SD卡，从而被电脑通过多合一接口读取。
  * SD、SDHC、SDXC。分别是SD卡不同的标准，SD卡采用FAT16文件系统，意思是管理文件所在位置的表格用16比特表示，所以最多只能管理65536个范围，再考虑每个范围能存储32KB的数据量，所以65536 × 32KB = 2GB，SD卡容量上限只能到达2GB。为解决FAT16格式可支持容量有限的问题，SDHC改用了FAT32格式；依规格定义，容量最大可达到32GB，SDXC标准规范为SD卡的下一代标准，最大容量可高达2TB（[参考这里](https://zh.wikipedia.org/wiki/SDHC)）。所以说，8M<SD<=2G,4G<=SDHC<=32G,64G<=SDXC<=2T。
  * classx/UHS。SD卡提供不同的速度，早期它是按CD-ROM的150 KB/s为1倍速（记作“1x”）的速率计算方法来计算。SDHC标准（SD 2.0），重新定义了SD卡的速度规格，分为三档：Class 2、4、6，分别表示最低的写入速度的大小，后来增加了Class 10及全新制定的UHS。UHS（Ultra High Speed）是与SDXC同时推出的SD卡总线标准。此标准适用于SDHC和SDXC。UHS-I最高传输速度（理论值）为104MB/s，UHS-II最高传输速度达312MB/s，是UHS-I的三倍，UHS-II第一排针脚与UHS-I相同，这样可兼容于UHS-I设备，而第二排针脚是新增的。而现在的UHS的速率分级有U1（10MB/s与class10一样所以看到一些商品同时标出U1和C10）、U3(30MB/s)，要注意的是要用到Ux的速率必须支持UHS协议，只标Ux不标I的都是耍流氓。
3. 扩展分区。使用一段时间发现官方的镜像不能完全使用TF卡的空间，这是因为要让发行镜像小，更好的存储，下载。但是我们肯定是想充分利用TF卡的空间的，所以就需要扩展。大概的思路是这样的：
  * 首先是要知道镜像文件写入的都是TF卡前面部分的空间，而后面的空间是留空的。而最开始是u-boot，然后是boot分区，然后是文件系统的分区，最后面是空白分区。
  * 然后我们的思路就很明显了，首先删除后面的文件系统分区，然后新建一个分区，由于新建的分区没有改变原来文件系统的内容和起始点，只是改变了终止的扇区，相当于只是扩充了空间所以不会有文件的丢失，类似于windows的扩展卷。
  * 我们用`fdisk /dev/mmcblock0`然后`d`删除后面的文件系统的主分区，然后回车。现在`p`看看是不是只剩下一个启动分区了。然后`n`新建分区，四次回车分别确认新建分区的种类，分区号，起始点终止点，然后就可以输入`w`保存了。你也可以再保存之前print一下当前的分区信息。
  * 然后重启，重启后输入`resize2fs /dev/mmcblock0p2`来扩大新的分区的大小。
4. 关掉作为心跳信号的绿灯（实在是太闪眼了）。首先要获取[sunxi-tools](https://github.com/LeMaker/sunxi-tools.git)，然后`make`编译得到`fex2bin`工具，然后拿到BananaPro引脚配置的`BananaPro.fex`这个可以再[xunxi-boards]( https://github.com/LeMaker/sunxi-boards.git)找到，找到LED配置的地方如下：
  ```
  [leds_para]
  leds_used = 1
  leds_num = 2
  leds_pin_1 = port:PH24<1><default><default><0>
  leds_pin_2 = port:PG02<1><default><default><0>
  leds_name_1 = "green:ph24:led1"
  leds_name_2 = "blue:pg02:led2"
  ;leds_default_1 = 1
  ;leds_trigger_1 = "heartbeat"
  ;leds_trigger_2 = "mmc0"

  ```
  把最后两行用分号注释，其中`heartbeat`是绿灯，`mmc0`是蓝灯我们都关掉。最后`sunxi-tools/fex2bin build/BananaPro.fex build/script.bin`把配置文件转换成二进制文件然后放到boot分区替换原来的script.bin。
5. 控制。
  * 串口。控制方法有好几种，最简单的就是串口了，就买一根串口线，连上RXD TXD GND（不接地会乱码），就ok。win下装上PL2303（或者是CH340），找个串口助手就可以了，但是有个不爽的是每次发命令就要把之前的命令给删了重输。然后是Ubuntu是装上Kermit或Minicom还有就是GUI的Cutecom。打开就能操作，就像通过ssh操作似的很方便。
    * 对于Kermit，配置 ~/.kermrc 就ok，配置文件如xxxx，`sudo kermit -c` 启动就好。~/.kermrc的内容如下：
    ```
    set line /dev/ttyUSB0
    set speed 115200
    set carrier-watch off
    set handshake none
    set flow-control none
    robust
    set file type bin
    set file name lit
    set rec pack 1000
    set send pack 1000
    set window 5
    ```
    * 对于Minicom 按下`Ctrl+a`再按下 `z` `o`进行串口的配置，然后直接 sudo minicom 即启动就ok。
    * 对于cutecom直接`sudo cutecom`就可以了，里面有配置选项。
  * 控制方法2，就是通过网线。就是用网线把笔记本和板子连接，然后ssh登陆。
    * 对于win 可以使用`arp -a`来查看究竟哪个是板子的ip，但是貌似要在网络设备里设置一个共享才能获取到Ip。否则获取到的是169打头的无效的ip。
    * 对于Ubuntu 那就是搭建DHCP服务器，安装dhcp 服务器`sudo apt-get install isc-dhcp-server`（也可以使用udhcpd），然后配置本机IP为静态Ip，然后设置`/etc/dhcp/dhcpd.conf`，然后就可以获取到IP地址了。给出一个参考的配置
    ```
    #默认租期10800秒 3小时
    default-lease-time 10800;
    max-lease-time 86400;
    ddns-update-style none;

    # 指定默认网关即路由
    option routers 192.168.123.1;
    # 指定域名服务器的地址 会修改客户端的resolv.conf
    option domain-name-servers 192.168.123.1;
    # 给个域名
    option domain-name "kangqingfei's Ubuntu dhcp-server";
    # 指定动态分配IP地址池
    subnet 192.168.123.0 netmask 255.255.255.0{
       range 192.168.123.10 192.168.123.200;
    }
    ```
    * 然后再就可以远程桌面登陆，安装`vnc4server` `xfce4` `xrdp`然后用自带的远程桌面连接，貌似用vncviewer只出现一把叉。 按照[这个方法](http://www.withonly.com/?p=453)解决了这个问题，就是`echo xfce4-session >~/.xsession`,修改好后还是可以手机登录的。
