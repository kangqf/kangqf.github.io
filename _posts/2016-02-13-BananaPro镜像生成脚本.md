---
title: BananaPro镜像生成脚本
categories:
  - basis
tags:
  - BananaPro
  - 嵌入式
  - ARM
  - 毕设
date: 2016-02-13 22:49:21
---
### 这篇文章主要是讲解一个开源硬件`BananaPro`的系统镜像的组成，以及编写一个脚本来生成一个系统镜像。

1. 镜像，就是我们常见的.img文件，这样的文件通常是通过块读写来备份一个操作系统或者是硬盘而生成的。在`BananaPro`中默认是从SD卡启动所以也就是备份SD卡中的文件得到的（`dd if=/dev/mmcblock0 of=~/BananaPro.img`），或是通过其他的方式生成的（后面会讲到）。还原镜像在linux下仍然可以使用`dd`命令，而在windows下可以使用`Win32DiskImager.exe`来将镜像还原到SD卡。
<!--more-->
2. 镜像的加载和启动过程（[参考这里](http://www.eeboard.com/bbs/thread-38335-1-1.html)）。A20的启动过程大概可分为5步：
  * Boot ROM。
    * 系统上电后，ARM处理器在复位时从地址0x00000000开始执行指令，这里的内容为Boot ROM，也就是CPU本身自带的启动代码，这个启动代码由全志在芯片出厂前就下载到CPU中了，普通用户不需要接触这部分代码。启动代码主要用来引导下面的u-boot。Boot ROM默认的启动时序为：SD Card0 > NAND FLASH > SD Card2 > SPI NOR FLASH。另外通过外部的一个启动选择引脚可以使其跳转到USB启动模式。（猜测：在我们`BananaPro`上可能就是那个UBOOT按键）。
    * 通常情况下，启动选择引脚状态连接50K内部上拉电阻。在上电后，执行存储在Boot ROM中的启动代码，将自动检测启动选择引脚状态。只有当该引脚状态为低电平时才选择USB启动模式。
  * SPL。
    * 一般来说在确定启动设备后，CPU将加载并执行`bootloader`程序，CPU通过拷贝或映射`bootloader`程序到内存，然后执行`bootloader`的第一条指令。
    * 但是我们发现A20通过`uboot`引导系统之前先载入了`uboot SPL`。SPL是一个迷你版的`uboot`，全拼为`Second Program Loader`。适用于SOC的内部`SRAM`<64K的情况（*也就是说SDRAM不够用来存放uboot*），用它来加载完整的`uboot`程序到`SDRAM`，并通过完整`uboot`加载内核来启动系统。其中`SRAM`一般指CPU内部的L1/L2或外部的L2高速缓存，这里即为`Boot ROM`，而`SDRAM`指内存。
    * SPL程序流程如下：
      * 初始化ARM处理器
      * 初始化串口控制台
      * 配置时钟和最基础的分频
      * 初始化SDRAM
      * 配置引脚多路复用功能
      * 启动设备初始化
      * 加载完整的uboot程序并转交控制权
  * Uboot。Uboot用于载入硬件配置文件`Script.bin`到指定内存地址，然后从指定位置载入内核到指定的内存空间，然后根据配置文件初始化处理器的硬件资源.
  * Kernel。内核载入到指定SRAM（0x48000000）之后，校验内核成功并跳转到内核，然后内核开始启动。
  * rootfs。内核启动后就会根据配置去调用对应的rootfs初始化程序或脚本，然后启动rootfs的各个进程。
3. BananaPro的镜像结构。
  * 0-8kb -> 不可用，用于存放分区表等内容
  * 8kb-32kb -> SPL
  * 32kb-544kb -> uboot
  * 544kb-672kb -> 存放环境变量
  * 672kb-1024kb -> 保留
  * 1024kb-... -> 自由分区（分别是FAT用于存放配置文件，以及EXT4用于存放rootfs）
4. root-fs。你需要自己找到一个 arm 架构的 rootfs放在root-fs文件夹里面，比如使用[Volumio](https://github.com/volumio/RootFS.git)。或者参考后面的文章自己定制一个Ubuntu的rootfs。然后我们会`rm -rf build` `mkdir -p build`创建一个空的新文件夹用来存放我们将会生成的东西。
5. u-boot-spl.bin。
  * `git clone https://github.com/LeMaker/u-boot-sunxi.git`获取uboot的源码
  * `cd u-boot-sunxi && make CROSS_COMPILE=arm-linux-gnueabihf- BananaPro_config && make CROSS_COMPILE=arm-linux-gnueabihf-`编译生成带有SPL头的uboot
  * `cp u-boot-sunxi-with-spl.bin ../build/u-boot-spl.bin`拷贝生成的带SPL的uboot
6. script.bin。这是BananaPro的硬件配置文件，用于配置各个GPIO口等信息，这是由一个.fex文件通过转换工具转成的二进制文件。
  * `git clone https://github.com/LeMaker/sunxi-tools.git` 获取转换工具源码
  * `cd sunxi-tools && make clean && make`编译生成转换工具
  * `git clone https://github.com/LeMaker/sunxi-boards.git`获取配置文件源码
  * `cp sunxi-boards/sys_config/a20/BananaPro.fex build/BananaPro.fex` 拷贝配置文件到指定位置。
  * `sunxi-tools/fex2bin build/BananaPro.fex build/script.bin`将配置文件转换为二进制文件
7. 编译生成内核。
  * `git clone https://github.com/LeMaker/linux-sunxi.git --depth=1`获取内核源码
  * ```
    cd linux-sunxi &&
  	make ARCH=arm CROSS_COMPILE=arm-linux-gnueabihf- sun7i_defconfig &&
  	make ARCH=arm CROSS_COMPILE=arm-linux-gnueabihf- menuconfig &&
  	make ARCH=arm CROSS_COMPILE=arm-linux-gnueabihf- uImage modules &&
  	cp arch/arm/boot/uImage ../build/uImage &&
  	cp .config ../build/.config &&
  	make ARCH=arm CROSS_COMPILE=arm-linux-gnueabihf- INSTALL_MOD_PATH=../build/modules modules_install
    ```
    生成内核及驱动
8. 然后就是自己生一个.img文件，对其分区。
  * `sudo dd if=/dev/zero of=${LOOP_DEV} bs=1k count=1023 seek=1`清空前面的空间，但不清空分区表。
  * `sudo dd if=build/u-boot-spl.bin of=${LOOP_DEV} bs=1024 seek=8` 烧写到有SPL的uboot。
9. 配置uboot的参数。可以有两种方式来配置，分别是`boot.scr`或`uEnv.txt`
  * `boot.scr`。u-boot在启动的时候会在第一个分区（FAT/extX格式）寻找`/boot.scr`或者`/boot/boot.scr`文件。boot.scr中可以包含用于载入script.bin，kernel，initrd（可选）以及设置内核启动参数的uboot命令。`mkimage -C none -A arm -T script -d boot.cmd build/boot.scr`是将`boot.cmd`转换为`/boot.scr`的，boot.cmd内容如下：
  ```
  setenv bootargs console=ttyS0,115200 noinitrd disp.screen0_output_mode=EDID:1280x1024p60 init=/init root=/dev/mmcblk0p2 rootfstype=ext4 rootwait panic=10 ${extra}
  fatload mmc 0 0x43000000 boot/script.bin
  fatload mmc 0 0x48000000 boot/uImage
  bootm 0x48000000

  ```
  * `uEnv.txt`。u-boot在启动的时候会在第一个分区（FAT/extX格式）寻找`/uEnv.txt` 或者 `extX /boot/uEnv.txt`来设置uboot启动的一些参数。uEnv.txt内容如下：
  ```
  bootargs=console=ttyS0,115200 console=tty0 disp.screen0_output_mode=EDID:1280x720p50 hdmi.audio=EDID:0 root=/dev/mmcblk0p2 rootfstype=ext4 elevator=deadline rootwait
  aload_script=fatload mmc 0 0x43000000 script.bin;
  aload_kernel=fatload mmc 0 0x48000000 uImage; bootm 0x48000000;
  uenvcmd=run aload_script aload_kernel

  ```
10. 拷贝script.bin、kernel、boot.scr或uEvn.txt文件到fat分区。
  ```
  sudo mount -t vfat "${LOOP_PART_BOOT}" /mnt
  sudo cp build/uImage /mnt
  sudo cp build/script.bin /mnt
  sudo cp uEnv.txt /mnt
  ```
11. 拷贝rootfs
  ```
  sudo mount -t ext4 "${LOOP_PART_SYS}" /mnt
  sudo rm -rf /mnt/*
  sudo cp -r root-fs/* /mnt
  if [ -f /mnt/README.md ]; then
  	sudo rm -rf /mnt/README.md
  fi
  sync
  ```
12. 拷贝驱动
  ```
  sudo mkdir -p /mnt/lib/modules
  sudo rm -rf /mnt/lib/modules/
  sudo cp -r build/modules/lib /mnt/
  sync
  ```
13. 创建一些必要的文件夹
  ```
  sudo mkdir -p /mnt/proc
  sudo mkdir -p /mnt/sys
  sudo mkdir -p /mnt/mnt
  sudo mkdir -p /mnt/tmp
  sudo mkdir -p /mnt/boot
  ```
## 最后附上shell文件，[bootsrc版](http://kangqingfei.qiniudn.com/blog%2Fcodes%2Fconfigs%2Fbootscr.sh)、[uEvn版](http://kangqingfei.qiniudn.com/blog%2Fcodes%2Fconfigs%2FuEvn.sh)
