---
title: fuck hexo again
categories:
  - config
tags:
  - hexo
date: 2016-01-28 11:52:34
---

### 事情的起因是这样的，前几天我在用电脑看自己的博客的时候猛然发现某网页一直在加载，还没完没了，F12一看是google-analytics，好吧自己忘记开梯子了，想想这该如何是好呢，虽说这个google-analytics好用，可是不能让它拖慢整个网页的加载速度啊，所以我就上了百度了一下，结果看到了[一篇很忧伤的文章](http://blog.csdn.net/ligaofeng/article/details/43306061)，标题是` 现在大陆网站加google-analytics.com统计的不是傻逼就是这网站没人维护了`，好吧被骂SB了，那就干脆再折腾一下了，于是乎就有了以下的折腾：
<!--more-->

1. 将`sitemap` 提交给google，让搜索引擎收录自己博客，主要参考[这里]( http://lulee007.github.io/2016/01/16/Hexo%E5%8D%9A%E5%AE%A2%E4%BC%98%E5%8C%96%E9%85%8D%E7%BD%AE%E4%B9%8B-%E4%B8%BA%E8%87%AA%E5%B7%B1%E5%8D%9A%E5%AE%A2%E6%B7%BB%E5%8A%A0%E6%90%9C%E7%B4%A2%E5%BC%95%E6%93%8E%E7%BD%91%E9%A1%B5%E6%94%B6%E5%BD%95/)。
2. 刚好那时遇上github抽风，然后就弄了个`github`和`gitcafe`双线部署，主要参考[这里](http://chitanda.me/2015/06/11/tips-for-setup-hexo/)
3. 使用匹配主题的404页面：参考[这里](http://moxfive.xyz/2015/10/16/hexo-404-page/)
4. 加上顶部加载栏，参考[这里](http://blog.waydrow.com/2016/01/26/progressBar/)
5. 添加相册，失败，有个想法，写一个插件，把`instagram`的相片同步到七牛，这个估计是没时间咯。
6. 添加网站计数功能，使用不蒜子的计数功能：[参考这里](https://github.com/netcan/hexo-theme-yelee/commit/89d0348927e210379c26bd19f998a99f46c6079b)
7. 添加随机动画效果，参考[这里](http://www.netcan666.com/2016/01/05/Hexo%E5%8D%9A%E5%AE%A2%E7%BE%8E%E5%8C%96%E4%B9%8B%E5%8A%A8%E7%94%BB%E6%95%88%E6%9E%9C%E5%8A%A0%E9%9B%AA%E8%8A%B1/)
7. 移动端的`subtitle`高度过高问题：把`source/css/_partial/main.styl里的min-height: 150px`，改成`height: 150px`。这是一个开发者在分类太多时显示不全的时候加上的，结果被作者merge了，我们一般没有那么多的分类所以就不用了。

8. 其他的还有
  * PC端的侧边栏和移动端的顶部颜色问题。
  * 移动端头像到主页的链接
  * 随机加载动画问题
  * 改用百度的分享问题
  * 回到顶部或顶部问题
这些都进行了修改，基于`yilia`源作者2015年12月17日的[提交](https://github.com/litten/hexo-theme-yilia/commit/f07c7c8b2f715f03f982b45936d3c9310d1652a2)的基础上进行的修改,修改后的在[这里](https://github.com/kangqf/hexo-theme-yilia/tree/dev),具体的修改细节参考每次的commit。
9. 增加多说的UA，本来打算折腾这个的，但是觉得没有啥意思就没有折腾了。

### 所以我折腾了一个来回把原来的初衷给忘记了，所以就把`google-analytics`给撤了，一了百了。
