---
title: Qt Quick Scene Graph
categories:
  - basis
tags:
  - 翻译
  - 毕设
  - Qt
  - QML
  - 绘图
date: 2016-04-26 11:03:15
---
## 这是一篇翻译的文章，原文[链接](http://developer.ubuntu.com/api/qml/sdk-14.10/QtQuick.qtquick-visualcanvas-scenegraph/),当然这个链接的内容也是来自[这里](http://doc.qt.io/qt-5/qtquick-visualcanvas-scenegraph.html)。在此之前你可能需要了解一些`Scene Graph`的知识，你可以参照：
* [Scene graph](https://en.wikipedia.org/wiki/Scene_graph)
* [Scene Management - Scene Graph](http://www.cnblogs.com/soroman/archive/2009/12/31/1637176.html)
* [ 不朽的SceneGraph](http://blog.csdn.net/nightmare/article/details/1165113)
* [既方便又强大的QML Scene Graph](http://www.tuicool.com/articles/7jaYzq)
* [场景渲染(Scene Graph)](http://blog.csdn.net/lainegates/article/details/50890551)
* [Qt新渲染底层Scene Graph研究](http://blog.csdn.net/gamesdev/article/details/43063219)

### and then 开始翻译

Qt 中的场景图

在 Qt Quick 2 中使用了基于` OpenGL ES 2.0 `或` OpenGL 2.0 `的场景图来进行渲染。使用场景图进行绘图而不是传统的绘图机制（QPainter类似的）意味着渲染的场景可以在帧间保留，并且将要渲染的完整的一组基本元素在渲染开始之前就已经知道了。这就允许一系列的优化，比如分批渲染来尽可能的减小状态的改变和丢弃被覆盖的元素。
<!-- more -->
例如，假设一个用户界面包含一个10条目的清单，每个条目包含有一个背景颜色，一个图标，一个文本。使用传统的绘图的技术，这将需要绘制一共30个单元，和保存同等数量的状态。另一方面，对于场景图，可以识别将要渲染的一个元素，这样所有的背景都可以在一个单元里面被绘制，图片和文本也是一样，从而减少需要会是在的单元数量到只要3个。像这样的批量处理和减少状态变化可以在某些硬件上面极大地提高绘图的性能。


场景图和`Qt Quick 2.0`密切相关，并且不能单独使用。场景图是由`QQuickWindow`类管理和渲染的，并且用户自定义的类型也可以添加到场景图绘制的绘图单元通过调用`QQuickItem::updatePaintNode()`。

一个独立的结构包含足够的信息来渲染所有的条目的场景图是条目绘制的典型代表。一旦它被设置完成，它将会独立于条目的状态进行操作和渲染。在很多的平台场景图将被一个专门的渲染进程进行渲染，同时GUI线程在准备下一个帧的状态。

## `Qt Quick`场景图的结构

场景图是有很多的预定义的节点类型，每一个节点都有专门的用途。尽管我们称之为场景图，但一个更精确的定义是一个节点树。这颗节点树是由QML场景中的`QQuickItem`类型构成，并且场景是有一个用于绘制场景的渲染器内部处理的。节点自己没有包含任何有关绘制的代码和与绘制有关的虚函数。


尽管节点树大部分情况是由已经存在的Qt Quick QML的类型内部构建的，但是也可以由用户自己添加一颗包含自己内容的子树，包括3D模型的子树。

## 节点

对于用户来说最重要的节点在`QSGGeometryNode`中，这是用来定义用户自定义的绘图，通过定义它的几何图形（geometry）和素材（material）。几何图形使用`QSGGeometry`来定义，这也描述了图形初始化时的的形状和网格。它可能是一条线，一个矩形，一个多边形，多个不连接的矩形，或是复杂的3D图形。构成部件定义了像素点在形状中的填充方式。

一个节点可以有很多的子节点，子节点的的节点将在自己父节点之后渲染，这样它才会出现在子节点。

*注意：这不意味着所有的东西都是按照实际的顺序来渲染的，只有那些可视化的输出时可以保证会以这种顺序渲染*

可用的节点有：
* QSGClipNode：在场景图中实现剪切功能
* QSGGeometryNode：适用于在场景图中渲染的所有的内容
* QSGNode：场景图中所有节点的基类
* QSGOpacityNode：用来改变节点的透明度
* QSGTransformNode：实现在场景图中进行旋转

自定义的节点可以以通过将其归类到`QQuickItem::updatePaintNode()`的子类中来添加到场景图中。然后设置` QQuickItem::ItemHasContents`标志位。

*警告：了解到OpenGL操作和场景图的交互是发生在专门的渲染线程中是非常重要的，这首先发生在`updatePaintNode()`的调用中。经验是在`QQuickItem::updatePaintNode()`函数中只使用那些包含`QSG`前缀的类*

更多的细节可以查看这个例子：[Scene Graph - Custom Geometry](http://doc.qt.io/qt-5/qtquick-scenegraph-customgeometry-example.html).

## 预处理

节点有一个虚函数`QSGNode::preprocess()` 可以在场景图被渲染之前调用。节点子类可以设置标志`QSGNode::UsePreprocess`然后重载函数` QSGNode::preprocess()` 来为他们的节点做最后的准备。比如将贝塞尔曲线划分正确的等级以适应当前的缩放比例或是更新一个结构。

## 节点的所有权

节点的所有权可以是创建者也可以是场景图通过设置标识`QSGNode::OwnedByParent`。当场景图生存在GUI线程之外的时候，将所有权给场景图会是更好的方式，因为这简化了垃圾清理。

## 素材

素材描述了一个`QSGGeometryNode`的几何图形的内部该如何被填充。它封装了一个OpenGL的着色程序并且提供能够实现的灵活性，尽管大部分的Qt Quick 条目自己只使用一些很基础的素材，比如使用纯色填充或是文本填充。

对于那些只是想在QML类型中使用自定义的底纹的的用户，可以直接在QML里面使用`ShaderEffect`这种类型。

下面是这个素材类的完整列表：
* QSGFlatColorMaterial 一种在场景图中渲染纯色的几何图形的简便方法
* QSGMaterial 封装的一个着色器的渲染状态
* QSGMaterialShader 代表一个在渲染器中的OpenGL的着色器
* QSGMaterialType 作为一个独特的类型标记结合 QSGMaterial
* QSGOpaqueTextureMaterial 一种在场景图中渲染文本几何图形的简便方法
* QSGSimpleMaterial 模板生成的类用于存储状态和`QSGSimpleMateralShader` 一起使用
* QSGSimpleMaterialShader 用户在场景图中自定义素材的一种简便的方法
* QSGTextureMaterial 一种在场景图中渲染文本填充的集合图形的简便的方法
* QSGVertexColorMaterial 一种在场景图中渲染per-vertex 的有色集合图形的简便方法

更多细节参考例子[Simple Material](http://doc.qt.io/qt-5/qtquick-scenegraph-simplematerial-example.html)

## 方便的节点

场景图的API是非常底层的并且相比于简便更关注性能。从底层开始编写自定义的几何图形和素材需要编写大量的代码，虽然是一个很基本的部件。针对这个原因API包括一小部分的十分便捷的现成的类来创建最平常的用户自定义的节点。

* QSGSimpleRectNode - 一个`QSGGeometryNode`的子类定义了纯色的矩形图形
* QSGSimpleTextureNode - 一个`QSGGeometryNode`的子类定义了文本填充的矩形图形

## 场景绘制和渲染

场景图的渲染过程发生在`QQuickWindow`的内部，并且没有公开的API来访问它。然而,在少数一些地方的渲染管道用户可以附加应用程序代码。这可以用来用户自定义场景图的内容或者是渲染原生的OpenGL内容。这些点的集合是由渲染循环所定义的。

有关场景图的渲染是如何工作的可以参考[Qt Quick Scene Graph Renderer](http://doc.qt.io/qt-5/qtquick-visualcanvas-scenegraph-renderer.html)

有三种循环变量可用： basic, windows, 和 threaded。其中，basic和windows是单线程，然而threaded在一个专门的线程中执行场景图的渲染。针对不同的平台和使用的显卡驱动Qt试图选择一个合适的循环。当这不满意，或是为了测试，`QSG_RENDER_LOOP`变量可以被设置用来强制使用一个指定的方式。为了确定哪种方式正在使用，将`QSG_INFO`设置为1来启动程序。

*注意：threaded 和 windows 渲染玄幻依赖于OpenGL的对刷新实现方式。一些显卡驱动允许用户重载它的配置，或是关闭它而忽略Qt的请求。如果在交换缓存中没有阻塞，渲染循环将会渲染动画太快而导致占用CPU到100%。如果一个系统不允许提供vsync-based throttling，可以设置`QSG_RENDER_LOOP=basic`来使用基本渲染循环代替*

## 渲染循环的线程("threaded")

在大部分的配置情况，场景图的渲染将会在一个专门的渲染的线程中。这将会提高多核处理器的并行性，和更好的利用空闲时间，比如等待交换缓冲的回调。这提供了显著的性能改进,但是对何时何地与场景图的交互可能有某些限制。

下面是一个简单的轮廓关于一个框架是由哪些渲染循环线程组成的：
{% qnimg sg-renderloop-threaded.jpg title:test alt:来自七牛云的图片 'class:class1 class2' %}
1. QML的场景中出现一个变化，导致`QQuickItem::update()`被调用。这可能是来源于一个动画或是用户的输入。一个新的事件提交到渲染进程来初始化一个新的帧
2. 渲染进程准备绘制一个新的帧并且使OpenGL成为当前的上下文，然后初始化GUI的线程块。
3. 当渲染进程正在准备一个新的帧的时候，GUI线程调用`QQuickItem::updatePolish()`在条目被渲染之前做最后的修饰
4. GUI线程被阻塞
5. ` QQuickWindow::beforeSynchronizing()`信号发出。应用可以创建直接的链接（使用`Qt::DirectConnection`）到这个信号来做一些`QQuickItem::updatePaintNode()`被调用之前的准备工作。
6. 同步QML的状态到场景图。这将会通过调用`QQuickItem::updatePaintNode()`来完成所有在之前帧之后改变了的节点的重绘。这是唯一一次QML项目和场景图中的节点交互。
7. GUI的线程阻塞被释放
8. 场景图被渲染：
  * `QQuickWindow::beforeRendering()`信号被发送。应用可以创建槽直接连接（使用Qt::DirectConnection）到这个信号来使用自定义的可以在QML场景中被可视化的OpenGL调用。
  * 指定了`QSGNode::UsePreprocess`条目将会调用与它们联系的`QSGNode::preprocess()`函数。
  * 渲染器处理节点和OpenGL的函数调用。
  * `QQuickWindow::afterRendering()`信号发出，应用可以使用自定义的槽来创建（使用` Qt::DirectConnection`）自定义的的可以在QML场景中被可视化的OpenGL调用。
  * 渲染的帧被替换，` QQuickWindow::frameSwapped()`信号被发送
9. 当渲染线程运行时，GUI是空闲，这是可以来处理高级的动画，事件等。

## 非线程的渲染循环("basic" 和 "windows")

非线程的渲染循环一般在那种ANGLE窗口或是不是opengl实现的和使用 Mesa驱动的Linux中是默认使用的。对于后者，这是主要是一个预防措施，因为并对非OpenGL驱动程序和窗口系统的所有组合都进行了测试。同时，像`ANGLE `或` Mesa llvmpipe`的实现并不能使用线程正常的工作，因此不使用线程对于这个显得很重要。

在ANGLE的窗口中默认是不使用线程的，尽管basic是用于所有其他非线程需要的平台。甚至使用非线程的渲染循环你应该编写你自己的代码如果你使用线程的渲染器，因为不这样做会产生不可移植饿得代码。

接下来是关于在非线程的渲染器中的帧渲染队列的简单说明：
{% qnimg sg-renderloop-singlethreaded.jpg title:test alt:来自七牛云的图片 'class:class1 class2' %}

## 使用 QQuickRenderControl 的用户自定义渲染

当使用`QQuickRenderControl`，用于驱动渲染循环的责任转移到应用程序。在这种情况下，没有内置呈现循环使用。相反，它是由应用程序完成修饰，同步和渲染步骤，在适当的时候。这是可以实现任一类似于上面所示的那些线程纹或非线程的行为。

## 混合使用场景图和OpenGL

场景图提供用于集成的OpenGL内容两种方法：通过调用直接OpenGL命令，或者通过创建在场景图的节点。
