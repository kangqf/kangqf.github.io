---
title: VMware config network
categories:
  - tips
tags:
  - VMware
  - network
date: 2016-01-05 21:19:25
---

### 今天折腾虚拟机各种网络的问题，恼火死了，暑假的时候参加嵌入式培训的时候由于要发东西到宿主机，大家也遇到了虚拟机ping不通宿主机的问题，当时很多同学是碰运气，我也不记得当时是怎么解决的，现在就把VMware里面的一些网络配置仔细写一下。

安装完虚拟机后，我们还可以在windows的`控制面板\网络和 Internet\网络连接`里面看到这样两个连接
{% qnimg  VM-network-link.png title:test alt:来自七牛云的图片 'class:class1 class2' %}
其中VMnet1是虚拟机Host-only模式的网络接口，VMnet8是NAT模式的网络接口，这些后面会介绍，查看他们两个的ip，可以发现VMnet1的ip是192.168.178.1，VMnet2的ip是192.168.254.1,。
打开一台虚拟机后，点击 `虚拟机 > 设置 > 网络适配器` 我们可以看到像下面这样的情况。
{% qnimg VM-setting-network.png title:test alt:来自七牛云的图片 'class:class1 class2' %}
然而当我们在VMware里面 编辑 > 虚拟网络编辑器 的时候却看到下面这个情况
{% qnimg VM-virtual-network-editor.png title:test alt:来自七牛云的图片 'class:class1 class2' %}
其实这才是真实的情况，有三种连接的方式分别是 桥接模式，仅主机模式，NAT模式。接下来我们就一一解析这三种模式（PS：以下实验请在成年人的监护下进行，而且极有可能造成宽带断网之类的不良影响，并且确保你的电脑内存够大磁盘读写够快，否则容易造成电脑死机，由此引发一切后果概不承担。还有，在桥接模式下，别用putty之类的试图通过ssh连接虚拟机，会很卡卡卡卡，原因后面你会懂的）

1. 桥接模式。这是默认设置的模式，为了了解它的拓扑结构我们先进行一系列实验。为了排除不同的操作系统底层实现对实验带来的影响，我们开了3个虚拟机，分别是archlinux最新版，xp，ubuntu14.04，宿主机是win8.1，关闭所有机器的防火墙，尤其是xp。由于我们处在校园网内，所以可以再增加一个我们学校的bt down铺的主机。所以我们现在能够确定的（简化了核心交换机）拓扑及机器信息如下：
{% qnimg VM-net-tuop.png title:test alt:来自七牛云的图片 'class:class1 class2' %}

  然后用 ping 命令进行实验得到下面这样的结果：
      1. win8 ping 虚拟机，时而出现超时,一般时延小于1ms,有时时延极大。wireshark直接死掉，里面出现大量的重复request数据，request的ttl从1到128不等且多次重复出现，reply的ttl稳定128 而数量和request几乎相当，ping了4次出现了500万+条数据报。tracert要在本机的几点上重复跳不定次。
      2. 虚拟机 ping win8 ，对于xp时而出现超时,一般时延小于1ms,有时时延极大。对于linux 出现大量DUP! 提示，虚拟机卡死。wireshark由于中间按了停止所以，发现reply的ttl从128到90不等各种重复出现，request出现3条ttl都为64，同样在半路停止的情况下出现了50万+条的数据报。tracert只需一条。tracepath 无法到达。
      3. linux ping btdown铺发现出现大量重定向和大量来自win8的数据报（这已经很明显了），wireshark里面显示数据报依然有300万+大部分是重定向和来自bt down铺的reply，当时同学说bt down铺并没有异常。网络指示灯狂闪。。。
      4. 虚拟机互ping无异常，wireshark抓不到任何包，tracexx一跳就到。但是一旦关闭目标主机，出现大量重定向。。。
      5. win8 ping bt down铺正常而且能抓到报文。

{% qnimg VM-net0-result.png title:test alt:来自七牛云的图片 'class:class1 class2' %}

  所以结论是。。。。待我想想
