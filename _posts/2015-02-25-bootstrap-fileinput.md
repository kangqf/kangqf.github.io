---
layout: post
title: "Bootstrap FileInput"
description: ""
category:
tags: []
---
{% include JB/setup %}

[bootstrap-fileinput](http://plugins.krajee.com/file-input)
====================
这是一个基于`Bootstrap3.x`的加强版的文件选取插件，支持各种文件的预览功能，多选等。这个插件允许你设置一个高大上的文件选取/上传器通过使用`Bootstrap CSS3`样式。通过提供各种文件（包括images, text, html, video, audio, flash）的预览支持进一步地加强了文件选择器的功能。另外还支持AJAX上传，拖拽上传，上传进度可视，添加移除文件等



这个插件最初是由[这个博客](http://www.abeautifulsite.net/blog/2013/08/whipping-file-inputs-into-shape-with-bootstrap-3/)和[ Jasny的文件选择插件](http://jasny.github.io/bootstrap/javascript/#fileinput)启发 ，但是现在通过各种新加的特性使得这个插件相比以前更加成熟，使得这个插件已经成为web开发者的全面的文件管理工具。

伴随着`v4.0.0`版本的发布，这个插件已经支持AJAX上传---持基于大多浏览器支持的`HTML5 FormData`和`XHR2 `协议。并且还内置支持以AJAX方式删除服务器端的文件。这就拥有了一个能够快速的删除、添加和移除文件的特性。这个插件还支持拖拽形式的AJAX文件上传。对于浏览器不支持`HTML5 FormData`和`XHR2 `的情况，会自动使用普通的表单提交方式。


###文件选取特性
1. 这个插件将一个普通的 HTML文件选取变成一个高级的文件选取器，但是对于那些不支持`JQuery `h和`Javascript`的浏览器，将使用原生的HTML文件选取。

2. 这个文件选择器由以下三个部分组成，可以通过选项和模板来控制器各部分的显示：
> * 文件说明部分：显示已选文件的简短信息。
> * 文件操作按钮：用于选取、移除上传文件。
> * 文件预览部分：显示已选文件的预览（支持 image, text, flash, and video等文件格式）。

3. 这个插件自动转换的输入`type = file`为一个高级的文件选择器，如果你设置它的`class = file`。所有的选项可以使用HTML5数据属性。

4. 选择并预览多个文件的能力。使用HTML 5的文件阅读器API（` HTML 5 File reader API `）来读取和预览文件。显示文件被加载到预览区，如果选取了多个文件。

5. 提供了预定义的模板和CSS类都可以按需求改变你的文件输入显示风格。

6. 现在你可以配置插件显示初始的 预览图片/文件初始标题 (更有用更新场景)。参考`initialPreview`和`initialCaption`属性配置的插件选项部分。

7. 可以显示/隐藏的部分如下:
> * 文件说明信息部分。
> * 预览部分。
> * 上传按钮。
> * 移除按钮。

8. 定制目标容器元素的位置来显示整个插件，容器的标题、标题文本、预览容器、预览图像和预览状态。

9. 对于文本文件预览,自动调整文本缩略图的宽度,并在悬停时显示一个完整的文本链接。你可以自定义新窗口预览文件的标记(默认为…)。

10. 自定义预览、进度、已选文件的信息。

11. 上传操作默认表单提交。支持上传 路由/服务器 action 参数定制的基于ajax的上传。

12. 触发JQuery事件用于高级的开发。目前可用的事件有`filereset、fileclear、filecleared fileloaded、fileerror`。

13. 支持只读文件选取器。

14. 对于超过容器宽度的长文件名的情况，自动调整文件标题的显示长度。

15. 新的`fileimageuploaded`支持，当文件完全加载进预览容器时触发。

16. 自动调整超出预览容器大小的图片的大小。

17. 完全的模板化和可扩展的,允许配置文件选择器为开发人员想要的。

18. 可以通过`allowedPreviewTypes`配置可以预览文件的类型。
