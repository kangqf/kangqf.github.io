---
title: Altium Designer 使用自定义原理图模板
categories:
  - basis
tags:
  - Altium Designer
date: 2016-02-14 21:15:11
---

## 这篇文章将介绍在Altium Designer中建立并使用自己的模板（Template）文件。
1. 在Altium Designer中官方自带了一些模板，例如A4的横向模板如下：
{% qnimg ADST01.png title:test alt:来自七牛云的图片 'class:class1 class2' %}但是上面Altium 的大LOGO实在是令人不爽，而且上面显示的信息与我们期望看到的还是有偏差，所以我们就需要自定义我们自己的模板。
<!--more-->
2. 首先 **在不使用模板的情况下** `File -> New -> Schematic`新建一个原理图文件，然后保存到我们的自定义模板的文件夹下`C:\FILE\AltiumDesigner\Templates`注意要保存成为模板文件，也就是`xxx.SchDot`文件。
3. 然后在`Design -> Document Options -> Sheet Options`中去掉`Title Block`的复选框。这是会发现右下角的信息不见了，然后按照我们自己设计需要用放置Line的方法画出我们需要显示信息的格子。
4. 用适当大小的黑色的字体放置我们的提示信息。并放置我们对应的变量，我们可以在`Design -> Document Options -> Parameters` 里面设置或是新建自己的变量。比如我们的变量里面没有Support，我们就自己新建一个Support变量。
5. 在我们的图标处`Place -> Drawing Tools -> Graphic..`放置我们的图片，放置的图片最好也放到模板文件的目录中，否则一旦该图片文件移动或是删除我们的模板中的图标便会丢失。
6. 设置模板文件的搜索路径。`Dxp -> Preferences -> System -> Default Locations`里面可以设置默认的库文件路径以及工程文件路径。{% qnimg ADST03.png title:test alt:来自七牛云的图片 'class:class1 class2' %}`Dxp -> Preferences -> Data Management -> Templates`里面设置模板文件的搜索路径。{% qnimg ADST04.png title:test alt:来自七牛云的图片 'class:class1 class2' %}
7. 设置默认模板文件。`Dxp -> Preferences -> Schematic ->General -> Defaults -> Template`在下拉框中选择我们自己定义的模板。
8. 使用模板。新建一个原理图文件并保存，会发现右下角的部分信息已经自动填充了，如果需要修改显示的信息的内容，只需要在`Design -> Document Options -> Parameters`中修改文档的参数就可以了。下面就是我们使用自定义模板的效果图：{% qnimg ADST02.png title:test alt:来自七牛云的图片 'class:class1 class2' %}
