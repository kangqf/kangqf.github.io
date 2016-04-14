---
title: BananaPro 使用模数转换
categories:
  - basis
tags:
  - BananaPro
  - 毕设
  - 嵌入式

date: 2016-04-13 21:52:39
---

## 由于BananaPro在使用MQ-2传感器的时候要采集模拟电压，所以必须使用AD转换模块。这个过程也是异常艰辛，大概花了3-4天才解决这个问题故记录之。首先，由于这边的AD芯片奇缺，TI又不让申请，不给申请也就算了还给我打广告，真是醉了。后面学弟给了一块ADS1286，然后在调试过程中拿不到数据，后面发现貌似是由于ADS用的是5v供电，然后BananaPro的GPIO的拉高的电压是3.3V，相比5V的80%还是不够高。再者就是由于PCB绘制过程中使用的RGB_LED的共阴和共阳弄反了，因此干脆就直接重新换了一块ADS1118。下面就主要讲在BananaPro中使用ADS1118的一些人生的经验。
<!-- more -->
### 使用硬件SPI。在ADS1118的芯片手册中提到所有数据均通过串行外设接口 (SPI™) 进行传输，而wiringPi库又支持SPI，所以我们的第一想法当然是使用硬件SPI进行读取数据和配置寄存器了。
1. SPI协议。
  * SPI有分主机和从机，两者皆有4个接口。MISO（master in slave out）、MOSI（master out slave in）、CLK、CS。
  * SPI是全双工的工作协议，可以在一个周期完成数据的接收和发送。
  * SPI共有四种工作模式。按照上升沿还是下降沿发数据可分为两种，按照时钟不用的时候保持为高或低又可以分为两种。[参考这里](http://blog.csdn.net/haplwd/article/details/44142015)
2. BananaPro的硬件SPI。BananaPro的硬件SPI如图所示{% qnimg banana_spi.png title:test alt:来自七牛云的图片 'class:class1 class2' %}按照上面的参考博客应该是属于模式4，即上升沿发数据，下降沿接收数据，CLK不用时保持低电平。我没有发现在哪里可以配置硬件SPI的工作方式。
3. ADS1118的SPI工作模式。按照官方手册（中文23页）说的"DOUT/DRDY 中数据的 MSB (bit 15) 在第一个 SCLK上升沿随时钟移出。 在转换结果随时钟移出 DOUT/DRDY 的同时， 新配置寄存器数据在 SCLK 下降沿锁存至 DIN 中。"对于slave的ads1118来时应该是属于模式4，需要搭配工作模式为模式3的master。所以自己在之前的调试之中一直没能用起硬件SPI。

### 使用模拟SPI。
1. ads1118的工作方式。ads1118内部含有两个寄存器，一个是配置寄存器，一个是数据寄存器。配置寄存器用于保存各个配置选项。数据期存器用于保存已经转换好的数据。ads1118内部还有温度传感器，可以配置使用温度传感器。ads1118可以使用连续转换模式和单次转换模式，使用单次转换模式可以显著降低功耗。
2. ads1118 的数据传输周期可以分为32位数据周期和16位数据周期（手册24页），32位数据周期相比而言多了一个配置期存器的回读。
3. CS一直保持低电平，也就是CS可以不用接MCU的管脚直接接地。按照我对数据手册的了解，这种情况貌似只适用于32位数据周期也就是必须有个回读配置期存器，而且如果使用这种模式，因为没有CS信号，所以对时钟的上升下降要极为小心，因为时钟及其容易造成数据的错位。本人就是在这种情况下浪费了很久的实际，目前也是没有调出来。
4. CS受控。在CS受控的情况就可以使用16位数据周期格式，这种格式要注意两点。
  * 使用单次转化模式的话，在第一次配置 配置寄存器的时候DOUT是不会拉低的，因为之前没有配置过。
  * 每次都要配置配置寄存器以便开始一次转换。所以顺序应该是配置读，再配置再读，每次都是读的上一次的配置转换得到的数据。
  * 每次配置和读的顺序不要搞错了，一次读写后要久的拉高CS。
5. 自己写的示例代码。自己就调通了最后一种方式把代码贴上仅供参考。
```
//init作用是在第一次转换的时候先配置 配置寄存器
void init()
{
	int i = 0;
	uint8_t ads1118_M[16] = {1,1,0,0,0,1,0,1,1,1,1,0,1,0,1,1};
	digitalWrite(DIN,LOW);
	digitalWrite(DCLK,LOW);
	delayMicroseconds(1);
	digitalWrite(DCS,LOW);
	for(i=0;i<16;i++)
	{
		digitalWrite(DCLK,HIGH);
		delayMicroseconds(1);
		if(ads1118_M[i])
			digitalWrite(DIN,HIGH);
		else
			digitalWrite(DIN,LOW);
		delayMicroseconds(1);
		digitalWrite(DCLK,LOW);
		delayMicroseconds(2);
	}
}
void ads1118()
{
	wiringPiSetupPhys();
	pinMode(DOUT,INPUT);
	pinMode(DCLK,OUTPUT);
	pinMode(DIN,OUTPUT);
	pinMode(DCS,OUTPUT);
	digitalWrite(DCS,HIGH);

	uint8_t ads1118_M[16] = {1,1,0,0,0,1,0,1,1,1,1,0,1,0,1,1};
	uint16_t da  = 0;
	int i = 0;
	init();

	for( ; ; )
	{
		da = 0;
		//注释之前是每次浪费一次转换的数据。但是读的效果比较好就是不久前的数据。如果对于转换周期较长的建议使用
		/*digitalWrite(DCLK,LOW);
		digitalWrite(DIN,LOW);
		digitalWrite(DCS,LOW);
		delayMicroseconds(1);
		for(i=0;i<16;i++)
		{

			digitalWrite(DCLK,HIGH);
			delayMicroseconds(1);

			delayMicroseconds(1);
			digitalWrite(DCLK,LOW);
			delayMicroseconds(1);
			delayMicroseconds(1);
		}
		digitalWrite(DCS,HIGH);*/

		digitalWrite(DIN,LOW);
		digitalWrite(DCLK,LOW);
		delayMicroseconds(1);
		digitalWrite(DCS,LOW);
		while(digitalRead(DOUT))
			;
		for(i=0;i<16;i++)
		{
			digitalWrite(DCLK,HIGH);
			delayMicroseconds(1);
			if(ads1118_M[i])
				digitalWrite(DIN,HIGH);
			else
				digitalWrite(DIN,LOW);
			if(digitalRead(DOUT))
			{
				da |= 0x0001;
				printf("1");
			}
			else
			{
				da |= 0x0000;
				printf("0");
			}
			da = da<<1;
			delayMicroseconds(1);
			digitalWrite(DCLK,LOW);
			delayMicroseconds(2);
		}
		digitalWrite(DCS,HIGH);
		float vol = (2.048*da)/65536;
		printf(" da %d vol %f\n ",da,vol);
		delayMicroseconds(10000);
	}
}
```
