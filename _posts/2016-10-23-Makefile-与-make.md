---
title: Makefile 与 make
categories:
  - tools
tags:
  - Makefile
  - make
date: 2016-10-23 19:26:03
---

## Makefile介绍
Makefile 是一种类似于脚本的，能够自动化执行程序的编译链接工作(甚至命令的执行 py latex 等命令)，从而省去了人工在命令行里输入指令的繁琐过程(并且在vim下对Makefile有着极好的支持)。而make指令(在win是cmake，linux下是gmake)可以自动搜索路径下的Makefile文件，并根据文件内容进行指令的执行，所以Makefile可以看做是一个工程的配置文件，只不过这个工程是用make打开编译。虽然Makefile给我们的程序的编译链接工作省去了很多繁琐的工作，但是偷懒的程序员依然觉得编写Makefile也是一件很是繁琐的工作，因此就诞生了诸多款的make工具用来根据程序员的一些设定自动生成能够执行的Makefile文件，从未又一定程度上减少了编写Makefile文件的重复工作量。这些工具有：
<!--more-->
* cmake cmake是跨平台项目管理工具，它用更抽象的语法来组织项目。虽然，仍然是目标，依赖之类的东西，但更为抽象和友好，比如你可用math表示数学库，而不需要再具体指定到底是math.dll还是libmath.so，在windows下它会支持生成visual studio的工程，在linux下它会生成Makefile，甚至它还能生成eclipse工程文件。也就是说，从同一个抽象规则出发，它为各个IDE定制工程文件。cmake命令执行的CMakeLists.txt文件 参考 [来源：知乎](https://www.zhihu.com/question/27455963/answer/36722992)

* qmake qmake工具就是Qt公司制造出来，用来生成Qt 专用makefile文件，这种makefile文件就能自动智能调用moc和uic对源程序进行预处理和编译。qmake当然必须也是跨平台的，跟cmake一样能对应各种平台生成对应makefile文件。qmake是根据Qt 工程文件（.pro）来生成对应的makefile的。工程文件（.pro）相对来说比较简单，一般工程你都可以自己手写，但是一般都是由Qt的开发环境 Qt Creator自动生成的，你还是只需要按下那个邪恶三角形就完事了。还没有完，由于qmake很简单很好用又支持跨平台，而且是可以独立于它的IDE，所以你也可以用在非Qt工程上面，照样可以生成普通的makefile，只要在pro文件中加入CONFIG -= qt 就可以了。 参考[辉常哥](https://www.zhihu.com/question/27455963/answer/89770919)

## Makefile 语法

## cmake 语法

## qmake 语法

 {% asciinema 50301 %}


## 使用make进行程序的编译
