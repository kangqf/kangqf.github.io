---
title: 为BananaPro编译GPU驱动
categories:
  - config
tags:
  - BananaPro
  - 毕设
date: 2016-04-02 21:43:01
---

上承我们刚刚构建最小文件系统，下承即将要编译Qt，在这里我们编译一下BananaPro的GPU驱动。在官方的linux中并没有将GPU驱动编译进内核，不过可以在官方wiki->[BananaPro/Pi:GPU]( http://wiki.lemaker.org/BananaPro/Pi:GPU)中编译用户空间的驱动，然后在开机的时候加载就好了，编译过程也很简单，基本上就是按照wiki的步骤一步步来就ok。不过在此之前，我们很有必要搞清楚一些概念性的东西(大部分来自各个百科)。

<!-- more -->


* FrameBuffer：是一种驱动视频显示设备的方式，即映射一块内存到用户空间，然后通过操作这块内存来操作显示设备。Framebuffer机制模仿显卡的功能，将显卡硬件结构抽象掉，可以通过 Framebuffer的读写直接对显存进行操作。用户可以将Framebuffer看成是显示内存的一个映像，将其映射到进程地址空间之后，就可以直接进行读写操作，而写操作可以立即反应在屏幕上。这种操作是抽象的，统一的。用户不必关心物理显存的位置、换页机制等等具体细节。这些都是由 Framebuffer设备驱动来完成的。对于低性能的嵌入式设备一般不可能跑起Xwindow，所以就通过直接操作fb的方式驱动显示设备，而在一些X协议的底层实现，也是使用的fb,比如对于Xorg，可以根据xorg.conf里的配置判断是否使用fbdev。

* X-window(Xwindow、X11或X))：一套在UNIX机器上运行优良的视窗系统，这个系统包含三个部分：
  * X服务器（X Server）是一个管理显示的进程，必须运行在一个有图形显示能力的电脑上。
  * X客户程序（X Client）是一个使用X服务器显示其资料的程序，它可以运行在与X服务器不同的电脑上。
  * X协议（X protocol）是X客户程序和服务器进行通信的一套协定，X协议支持网络。
  X不是内置于操作系统，它只是比用户层次稍高一些。在系统中也是一个相对独立的元件。
* XFree86：XWindow系统最被广泛使用的基建平台。XFree86是XWindow系统的一个实现，1992年至2004年，它一直循著自由发放的开放源代码模式发展着。由2004年开始，它再不是以GPL软件许可证的形式出现。
* X.Org：XFree86的开发因软件许可证出现分岐，而衍生出另一个发展自XFree86-4.4-RC2 版本源代码，名叫X.Org 服务器的XWindow系统。它被包括ubuntu，fedora在内的发行版本采用以代替XFree86。
* xlib 是 一个用c语言编写的 X Window System 协议的客户端库，
它包含有与x服务器进行通信的函数，编程者可以在不了解x底层协议的情况下直接使用它进行编程。
* xcb:是xlib的代替者。
* GLX: GLX 库解决了 X Window 中的 OpenGL 图形渲染问题。同样的，WGL 和 AGL 库分别解决了 MS Windows 和 Mac OS 窗口系统中的 OpenGL 图形渲染问题。GLX 像胶水一样，建立了 X Window、OpenGL 以及 3D 硬件加速驱动之间的联系。在 Linux 中，如果未有安装相应的支持 3D 加速的显卡驱动，那么 GLX 可以调用 Mesa (Unix-like 系统中应用最为广泛的 OpenGL 实现) 的 3D 软件加速来取得 X Window 与 OpenGL 的集成；当然，这种做法所实现的 3D 渲染效率就要大打折扣，并且也会累的 CPU 气喘。

* DRI (Direct Rendering Infrastructure) - OpenGL Context 的库集合或是一系列3D驱动程序的集合，DRI的一个主要目的就是提供高效能的OpenGL支持。
* DRM (Direct Rendering Manager)：本質是kernel module (menuconfig 里面character device -> drm)管理对硬件的操控要求


* OpenGL(Open Graphics Library)：一个跨编程语言、跨平台的编程接口规范。它用于三维图像（二维的亦可），是一个功能强大，调用方便的底层图形库。
* OpenGL ES(OpenGL for Embedded Systems)：是 OpenGL 三维图形 API 的子集，针对手机、PDA和游戏主机等嵌入式设备而设计。
* OpenVG(Open Vector Graphics):OpenVG 是针对诸如Flash和SVG的矢量图形算法库提供底层硬件支持界面的免授权费、跨平台应用程序接口API。用以在小屏幕设备上实现动人心弦的用户界面和文本显示效果，并支持硬件实现，这样可以在在极低的处理器功率级别下实现流畅的交互性能。

* Qt->KDM->KDE
  * Qt:是一个1991年由奇趣科技开发的跨平台C++图形用户界面应用程序开发框架。它既可以开发GUI程序，也可用于开发非GUI程序，比如控制台工具和服务器。Qt是面向对象的框架，使用特殊的代码生成扩展（称为元对象编译器(Meta Object Compiler, moc)）以及一些宏，易于扩展，允许组件编程。
  * KDM(KDE DISPLAY MANAGER):是KDE(Kool Desktop Environment)的X显示管理器。kdm 管理一系列本地或者远程机器的 X 服务器，提供的服务类似于字符终端中 init、getty 和 login 提供的相应服务：提示输入登录名和密码，验证用户，运行会话。
  * KDE(Kool Desktop Environment):一种著名的运行于 Linux、Unix 以及FreeBSD 等操作系统上的自由图形桌面环境，整个系统采用的都是 TrollTech 公司所开发的Qt程序库（现在属于Digia公司）。
* GTK->GDM->GNOME
  * GTK（GIMP Toolkit)：是一套源码以LGPL许可协议分发、跨平台的图形工具包。它支持创建基于 GUI 的应用程序。GTK+虽然是用C语言写的，但是你可以使用熟悉的语言来使用GTK+，因为GTK+已经被绑定到几乎所有流行的语言上。
  * GDM(The GNOME Display Manager)：GNOME显示环境的管理器，并被用来替代原来的`X Display Manager`。
  * GNOME:类Unix操作系统上最常用的图形桌面环境。GNOME属于GNU计划中的一部份。作为KDE的替代品(由于KDE依赖的Qt当时并未使用自由软件许可)，GNOME是完全公开的(免费的软件)，以GTK图形界面库为基础。

* LightDM(Light Display Manager)：一个全新的、轻量的Linux桌面的桌面显示管理器。 LightDM是一个跨桌面（KDE，GNOME）显示管理器，其目的是成为 X.org的X服务器的标准显示管理器，支持本地图形界面以获得最好的兼容性。LightDM现在已经具备了GTK、Qt甚至WebKit的界面，也就是用HTML来做登陆界面。
* Xfce：Xfce是一个自由软件，运行在类Unix操作系统 （如Linux、FreeBSD 和 Solaris）上，提供轻量级桌面环境。Xfce快速、轻量，界面美观和对用户友好。Xfce由独立的软件组件构成，可根据需要单独使用或者组合在一起提供计算机图形桌面环境的全面功能。Xfce由C语言写成，依赖GTK+。

## 接着，介绍一下BananaPro的GPU -> ARM Mali400 MP2

正如[这里](http://blog.csdn.net/myarrow/article/details/17375483)所说的Mali GPU DDK 的 Linux 版本包含在内核中运行的以下三个组件：
1. 设备驱动程序:它是最重要的组件，提供对 Mali-400 GPU 的低级访问。其主要功能如下：
  * 对 Mali GPU 硬件的访问
  * 中断处理
  * 低级内存管理
2. 统一内存提供程序 (UMP)：它是重要的辅助组件为，可以各种不同方式使用以实现驱动程序堆栈中的零拷贝操作。
  * 通过安全 ID 访问分配的 UMP 内存。这可在不同的应用程序、驱动程序和硬件组件之间共享内存，以实现零拷贝操作
  * 设置 MMU 或 MPU 表所需的物理地址信息
  * 将 UMP 内存映射至 CPU 地址空间的方法，以实现读取和写入
3. Mali 直接渲染管理器 (DRM)：它将 Mali GPU DDK 集成到 X11 环境之中并启用直接渲染接口 (DRI2)。


## 编译安装驱动

1. 安装工具链
```
sudo apt-get update
sudo apt-get install git build-essential make gcc autoconf libtool debhelper \
dh-autoreconf pkg-config automake xutils-dev libx11-dev libxext-dev libdrm-dev \
x11proto-dri2-dev libxfixes-dev xorg-dev libltdl-dev mesa-utils
```
2. 下载源码
```
mkdir ~/hw_accleration
cd ~/hw_accleration
git clone https://github.com/robclark/libdri2.git
git clone https://github.com/linux-sunxi/libump.git
git clone https://github.com/linux-sunxi/sunxi-mali.git
git clone https://github.com/ssvb/xf86-video-fbturbo.git
```
3. 自动加载驱动模块。`sudo vim /etc/modules`然后添加
```
ump
mali
mali_drm
```
4. 安装libdri2。如果要使用mali的话就需要安装，如果只是用FrameBuffer的话就不用安装
```
cd ~/hw_accleration
cd libdri2
./autogen.sh
./configure --prefix=/usr
make
sudo make install
sudo ldconfig
```
5. 安装libump库，这个库是用来管理统一映射用户空间的内存的
```
cd ~/hw_accleration
cd libump
autoreconf -i
./configure
make
make install
sudo ldconfig
```
6. 编译mali驱动
```
cd ~/hw_accleration
cd sunxi-mali
git submodule init
git submodule update
```
7. 安装mali驱动
  * 如果使用X11
    ```
    make config
    sudo make install
    ```
  * 如果使用framebuffer
    ```
    make config ABI=armhf VERSION=r3p0 EGL_TYPE=framebuffer
    sudo make install
    ```
8. 安装fbturbo，这个好像是用来为Xserver加速的插件
```
cd ~/hw_accleration
cd xf86-video-fbturbo
autoreconf -v -i
./configure --prefix=/usr
make
sudo make install
```
  创建一个xorg配置文件来让Xserver使用fbturbo，`vim etc/X11/xorg.conf.d/99-fbturbo.conf`然后写入：
```
Section "Screen"
        Identifier        "My Screen"
        Device          "Allwinner A10/A13 FBDEV"
        Monitor         "My Monitor"
EndSection

Section "Device"
        Identifier        "Allwinner A10/A13 FBDEV"
        Driver          "fbturbo"
        Option          "fbdev" "/dev/fb0"
        Option          "SwapbuffersWait" "true"
        Option          "AccelMethod" "G2D"
EndSection

Section "Monitor"
        Identifier        "My Monitor"
        Option          "DPMS" "false"
EndSection
```
9. 默认普通用户是对一些类似于/dev/mali和/dev/ump的特殊设备文件是没有权限的，所以要添加规则以让普通用户能够使用。`vim /etc/udev/rules.d/50-mali.rules`，然后添加
```
KERNEL=="mali", MODE="0660", GROUP="video"
KERNEL=="ump", MODE="0660", GROUP="video"
KERNEL=="disp", MODE="0660", GROUP="video"
KERNEL=="g2d", MODE="0660", GROUP="video"
KERNEL=="fb*", MODE="0660", GROUP="video"
KERNEL=="cedar_dev", MODE="0660", GROUP="video"
```
  然后将用户添加到组中 `sudo usermod -aG video $USER`

10. 测试，重启开发板。`grep -i fbturbo /var/log/Xorg.0.log`看看Xserver是否已经使用了fbturbo。测试mali驱动：
* 对于使用X11的情况：
  ```
  cd test
  cc -Wall -o test test.c -lEGL -lGLESv2 -lX11
  ./test
  ```
* 对于使用FrameBuffer的情况：
  ```
  make test
  ./test/test
  ```
  如果看到
```
GL Vendor: "ARM"
GL Renderer: "Mali-400 MP"
GL Version: "OpenGL ES 2.0"
```
  说明已经安装成功了。
