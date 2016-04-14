---
title: BananaPro的GPIO库
categories:
  - config
tags:
  - BananaPro
  - 毕设
  - 嵌入式
date: 2016-04-13 10:52:06
---
## 我们知道像raspberry、BananaPro之类的开发板与普通linux电脑的很大的区别就是它可以将GPIO口引出方便大家连接各种模块，因此，对GPIO口的了解就显得尤为重要。对于Lemaker的BananaPro官方的[wiki](http://wiki.lemaker.org/How_to_control_the_IO_on_the_SBC_boards/zh-hans)其实已经写得很详细，之所以拿出来单独写一是想在自己的博客上面留个笔记，二是遇到一些很奇葩的问题需要特别引起注意。
### GPIO口的操作根据使用的语言和方式的不同也可以使用不同的库，一个是使用C语言的[WiringLMK](https://github.com/LeMaker/WiringLMK),官方的说明在[这里](http://wiki.lemaker.org/WiringLMK/zh-hans)，还有就是使用Python的[LMK.GPIO](https://github.com/LeMaker/LMK.GPIO)官方的说明在[这里](http://wiki.lemaker.org/LMK.GPIO/zh-hans)，再有就是使用web进行控制的[webio-lemaker](https://github.com/LeMaker/webio-lemaker)。本人了解的目前就只有这三种方式。由于在实际使用中只用过wiringPi的库所以接下来只会介绍这个库，后面如果用到其他的库会在本文一并加上。
<!-- more -->
1. wiringPi库。这个库起初是用来作为BCM2835芯片的GPIO库，而现在已经支持诸如SPI、I2C、UART、PWM等协议的库了。而他所支持的芯片的产商也是更多，比如BananaPro的全志的芯片。wiring的命名的由来是因为其与arduino上的wiring类似所以以此命名。wiringPi提供库函数以便给C/C++语言提供调用，并且wiringPi还提供一个命令行工具gpio用来设置GPIO管脚，所以你甚至可以在shell脚本里面控制GPIO（你可以使用gpio -h来查看帮助信息），比如你使用openkeeper联网成功之后就拉低一个管脚，然后就点亮一个LED。DevLib是wiringPi 的一部分，它可以使用一些比如12864和1302之类的芯片。
2. 安装wiringPi。
  * `git clone https://github.com/LeMaker/WiringLMK.git `
  * `cd WiringLMK`
  * `chmod +x ./build`
  * `sudo ./build`
  测试安装是否成功：`gpio -v`或者`gpio readall`看看输出是否正确。
3. 初始化GPIO口。主要是有4个函数：
  * `int wiringPiSetupPhys (void) ;`这个是按照BananaPro的物理管脚顺序来初始化GPIO的映射关系
  * `int wiringPiSetup (void) ;` 该函数初始化 wiringPi，幵假定程序将使用 wiringPi的管脚定义图。具体管脚映射，可以通过 gpio readall 命令来查看。
  * `int wiringPiSetupGpio (void) ;` 在此种模式下的管脚号是 Broadcom 的 GPIO 管脚号
  * `int wiringPiSetupSys (void) ;`使用/sys/class/gpio接口，而丌是直接通过操作硬件来实现。该函数可以使用非 root 权限用户，在此种模式下的管脚号是 Broadcom 的 GPIO 管脚号
  在[这里](http://wiki.lemaker.org/WiringLMK/zh-hans#.E9.99.84.E5.BD.95.EF.BC.9AWiringLMK.E7.AE.A1.E8.84.9A.E8.AE.BE.E7.BD.AE.E8.A1.A8)查看不同的地方。
4. 管脚定义。
  * `void pinMode (int pin, int mode) ;`用于定义管脚的工作模式，主要有`INPUT`、`OUTPUT`、`PWM_OUTPUT`、`GPIO_CLOCK`四种模式，后面的两种模式只针对特定的管脚有效。
  * `void pullUpDnControl (int pin, int pud) ;`当管脚设置为输入，可以设置为上拉(PUD_UP 拉高到VCC 3.3V)或下拉(PUD_DOWN)或者是浮空(PUD_OFF)的模式。
    * 上拉：就是通过一个电阻将信号接电源，一般用于时钟信号数据信号等。防止信号被负载或者干扰拉下到某个电压下
    * 下拉：就是通过一个电阻将信号接地，一般用于保护信号。
5. 管脚操作。
  * `void digitalWrite (int pin, int value) ;`数字写，值可以使`HIGH`或是`LOW`。
  * `int digitalRead (int pin) `数字读，返回0或1
  * `analogRead (int pin) ;` 管脚能够配置附加的模拟模块（比如 Gertboard， quick2Wire 模拟板等），这个函数可读取并返回模拟信号输入值。
  * `analogWrite (int pin, int value) ;`管脚能够配置附加的模拟模块，这个函数可写入模拟信号输入值。
  * `int wiringPiISR (int pin, int edge, void (*function)(void)) ;`用于绑定中断处理函数，触发可以设置为上升沿(`INT_EDGE_RISING`)、下降沿(`INT_EDGE_FALLING`)、边沿触发(`INT_EDGE_BOTH`)。
  * `void pwmWrite (int pin, int value) ;`用于设置PWM寄存器的值，这里是硬件PWM而不是软件PWM，因此只适用于PWM输出的管脚
  * `int softPwmCreate (int pin, int initialValue, int pwmRange) ;`用于设置软件模拟的PWM的范围。pwmRange：0 表示关闭 100 表示全开，建议设置为100
  * `void softPwmWrite (int pin, int value) ;`已初始化的软PWM可以用这个函数更新管脚的值。
6. I2C模块。这个模块可以使用硬件的I2C协议进行读取数据，函数声明包含在头文件`#include <wiringPiI2C.h>`中。
  * gpio load i2c (1000) 加载I2C驱动到内核中，其中1000的值是可选的，是用来指定速率的，单位是kbps。Banana Pro会默认加载I2C驱动程序。
  * 检查I2C设备：`i2cdetect -y 2 `这会根据标号打开/dev 目录下对应的设备（例子使用的是2）返回值是标准的文件句柄。
  * 通过文件句柄可以进行简单的读写操作，不需要任何寄存器转换。而其它的一些操作需要使用`int wiringPiI2CSetup (int devID) ;`设置寄存器，其中devID就是上面返回的文件句柄。
  * `int wiringPiI2CRead (int fd) ; ` 通过文件句柄读取数据不需要发送任何寄存器的地址。
  * `int wiringPiI2CReadReg8 (int fd, int reg) ;` 可以从指定的设备寄存器读一个 8 位数值数据
  * `int wiringPiI2CReadReg16 (int fd, int reg) ;`可以从指定的设备寄存器读一个 16 位数值数据
  * `int wiringPiI2CWrite (int fd, int data) ;` 简单的设备写操作
  * `int wiringPiI2CWriteReg8 (int fd, int reg, int data) ;` 可以写一个 8 位数值到指定的设备寄存器
  * `int wiringPiI2CWriteReg16 (int fd, int reg, int data) ;`可以写一个 16 位数值到指定的设备寄存器

7. SPI模块。gpio load spi 100 用于加载spi驱动到内核中，后面是指数据缓冲区的大小单位是kb。BananaPro是默认不加载SPI设备的，如果要使用则必须手动添加或者加载`spi-sun7i`和`spidev`驱动模块到/etc/modules中。
  * `int wiringPiSPISetup (int channel, int speed) ;`用于初始化SPI的GPIO口和选择通道（BananaPro有两个通道0和1，这可以在/dev/中看到）以及设置速率。
  * `int wiringPiSPIDataRW (int channel, unsigned char *data, int len) ;` 通过SPI总线进行全双工读写数据块。
  * `int wiringPiSPIGetFd  (int channel) ;` 返回指定通道SPI设备的文件句柄
8. 串口库。wiringPi 包含了一个简单的串口处理库。使用它可以操作板上的串口或者 USB 串口设
备，返两者幵无特殊的差别。叧需要在初始化函数中指定设备名称即可。
  * `int   serialOpen      (const char *device, const int baud) ;`打开串口设备，同时设置通讯的波特率。默认将端口设置为“raw”模式（字符未经过转换），开启读超时为 10 秒。返回值是一个文件描述符，如果失败的话，返回值为-1，返种情况下，将会依据不同的失败原因来设置 errno 变量。
  * `void  serialClose     (const int fd) ;`使用指定的文件描述符关闭设备
  * `void  serialFlush     (const int fd) ;`清空指定文件描述符的数据缓冲区
  * `void  serialPutchar   (const int fd, const unsigned char c) ;`将单个字节写入指定设备的文件描述符。
  * `void  serialPuts      (const int fd, const char *s) ;` 该函数将一个以 `\0` 结尾的字符串写入指定设备的文件描述符。
  * `void  serialPrintf    (const int fd, const char *message, ...);`不系统的 printf 函数类似，区别在亍将内容写入到了串口设备。
  * `int   serialDataAvail (const int fd) ;` 返回等待读取的字符数，如果发生错误，则返回-1，此种情冴下， errno 将会被设置为错误发生原因。
  * `int   serialGetchar   (const int fd) ;` 返回串口设备的下一个待读取字符。如果没有数据，该函数将会等待 10 秒， 10 秒后若仍无数据则会返回-1.
9. 一些其他的函数。
  * `void         delay             (unsigned int howLong) ;`延时毫秒
  * `void         delayMicroseconds (unsigned int howLong) ;`延时微妙
  * `unsigned int millis            (void) ;`返回从调用 wiringPiSetup后函数后的毫秒数。返回值是一个无符号 32 位整数。在 49 天后，将会溢出。
  * `unsigned int micros            (void) ;`该函数返回从调用 wiringPiSetup 函数后的微秒数。返回值是一个无符号 32 位整数，在接近 71 分钟后，将会溢出。
9. 编译。
  * 使用WiringLMK编译一个程序例子, 你必须添加库文件链接路径-I/usr/local/include -L/usr/local/lib -lwiringPi
  * 根据不同的运行环境，你也许需要查看这些重要的参数： -lwiringPi、-lwiringPiDev或-lpthread。
  * 在使用WiringLMK GPIO库前面，必须在程序中添加头文件`#include <wiringPi.h>`。根据具体使用的模块（比如SPI，I2C，串口等），你可能也必须添加其它的#include头文件（比如`#include <wiringPiI2C.h>` `#include <wiringSerial.h>`）。
