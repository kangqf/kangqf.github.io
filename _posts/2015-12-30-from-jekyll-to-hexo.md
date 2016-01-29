---
title: from jekyll to hexo
tags:
  - hexo
categories:
  - config
date: 2015-12-30 16:17:53
---

#### 上面写了一篇文章，感觉真是丑爆了，内容臭，界面丑，还不是很适配手机，所以就很恼火，这时发现一个很不错的基于 [hexo](https://hexo.io/)的主题[yilia](https://github.com/litten/hexo-theme-yilia)，感觉可以有。其实之前就听过 `hexo`的大名，只是没想到比想象中的还好玩，对比之下，jekyll相当于把你的markdown放到了github然后让它给你编译，而hexo则是把你的markdown编译成html后发布到github（所以不存在jekyll的那种时延），更重要的是人家基于node，可扩展强，不用那么烦也不会老出问题，配置也是简单，重要的是人家颜值高啊，然后还有以下几点让我很心动：
<!--more-->
* 自动生成rss，我都不记得当时是怎么搞的jekyll才让它弄成了rss，反正是很烦。
* 看到有七牛的图片外链插件，你懂得github的速度，有了外链妈妈再也不怕我放图片了。
* 可以引用代码文件，这个之前不知道在jekyll里是不是有这个功能。
* 无痛迁移，可以从jekyll无痛迁移到hexo，虽然以前没有写多少东西，但是这就是人家的态度很端正啊。
### 所以果断换了。那就让我们一步步地来搭建我们的hexo blog吧。

1. 准备工作，首先你要装好node，怎么装就自己去找了，这里提醒一点，就是以前（考研之前）我玩node的时候装插件是很慢的，但是这次我不知道咋地速度杠杠的，我怀疑是npm的配置问题，因为以前好像是配置过npm的源，我在win下输，`npm` 会出现
    > >Specify configs in the ini-formatted file:
    > >   C:\Users\kqf\\.npmrc

    >然后我看了一下那个文件，是这样的
    > >`prefix=c:\ProgramFiles\nodejs\gnode_modules`
    > >registry=http://r.cnpmjs.org

    > 并且我还差异的发现有个`C:\Users\kqf\.cnpmrc`，才想起来好像装过cnpm（刚推广那会儿装的），反正是好久之前弄得了，我记得当时速度还是很慢，不知道为啥现在这么快了，毕竟过去这么久了，咋们的基础设施肯定要跟上来啊，不然哪有那么多人学node。反正现在速度是没有问题的了，大家可以去官网看看[http://cnpmjs.org/](http://cnpmjs.org/)或者淘宝的镜像[https://npm.taobao.org/](https://npm.taobao.org/)。

2. 安装一个博客，搞好上面的后就可以`npm install hexo-cli -g`安装一个hexo，然后建立一个博客，`hexo init blog`，安装模块`cd blog` `npm install` 然后选取一个主题，我的是之前提过的[yilia](https://github.com/litten/hexo-theme-yilia),所以`git clone https://github.com/litten/hexo-theme-yilia.git themes/yilia`,修改hexo根目录下的 `_config.yml` -> `theme: yilia`，然后再`hexo server(hexo s)`你就能看到一个博客了。
3. 安装插件，我们先要对博客进行配置，再对主题进行配置。考虑到其中还要对插件进行配置所以索性先把用到的几个插件装上,`npm install hexo-generator-feed hexo-generator-sitemap hexo-pdf hexo-qiniu-sync --save`
    > [hexo-generator-feed](https://github.com/hexojs/hexo-generator-feed)     ->生成rss
    > [hexo-generator-sitemap](https://github.com/hexojs/hexo-generator-sitemap)  ->生成sitemap
    > [hexo-pdf](https://github.com/superalsrk/hexo-pdf/)                 ->嵌入pdf
    > [hexo-qiniu-sync](https://github.com/gyk001/hexo-qiniu-sync)          ->插入图片外链，并且同步你的文件到七牛云

4. 然后开始配置，具体细节我就不说了，主要是配一些基本信息还有插件信息还有`deploy（使用ssh而不是http）`的信息。我把我的配置文件拿上来给参考一下[hexo_config.yml](http://kangqingfei.qiniudn.com/blog%2Fcodes%2Fconfigs%2Fhexo_config.yml) ，下面主要说几个坑
    > `language: zh-Hans`  这个语言一开始看官网的doc直接就填了zh 后面发现貌似中文变成繁体的了（估计开发者是台湾人的缘故），之后看到`themes\yilia\languages\zh-Hans.yml`才知道该填这个。
    > `code_dir: assest/codes`  这里要改成你存放代码的路径要不然后面会不能从文件引入代码块。
    > `new_post_name: :year-:month-:day-:title.md` 这里改成这个一是为了搞清楚文章发表的时间，而是为了从jekyll迁移的时候不出错，原话如下
    >  >Move all files in the Jekyll _posts folder to the source/_posts folder.
    >  >Modify the new_post_name setting in _config.yml:
    >  >new_post_name: :year-:month-:day-:title.md

    > `local_dir: source/assest` 这是七牛那个插件的配置，记得前面不能有`/`不然会出错，在这卡了好久。
    其他倒没啥了，就是**rss和sitemap一直生成不了**后来找到一个解决方案`hexo d -g`就有了。
5. 配置主题，同样的我把我的配置文件拿上来给参考一下[yilia_config.yml](http://kangqingfei.qiniudn.com/blog%2Fcodes%2Fconfigs%2Fyilia_config.yml) ，要配置的东西有，自己的社交信息，`google_analytics`的ID,多少评论的ID。
6. 写文章了，在之前先讲下他的原理，我们可以通过`hexo new "Hello Hexo" `生成一个新的文章。这个文章会默认使用`scaffolds`下的模板（layout），然后把生成的markdown放到`source/_posts`下。同理你可以猜到，`hexo new(hexo n) page "xxx"`会把生成的markdown放到`source`下面，`hexo new draft "xxx" `会放到`source/_draft`下面（鸡肋的草稿没啥用，其实post不deploy就是draft了啊）。并且我们可以自己定义layout并放到`scaffolds`下面，通过`hexo new layoutname "xxxxx" `生成一篇基于自己定义的layout的文章，他会将我们自己模板生成的markdown同样放到`source/_posts`下。这样就允许我们自己把一些常用的标签放到里面了，我自己定义了一个article的layout，里面有我常用的一些东西，发给大家参考一下[hexo_article_layout.mmd](http://kangqingfei.qiniudn.com/blog%2Fcodes%2Fconfigs%2Fhexo_article_layout.mmd)（由于**这里不能放md文件否则生成会报错**，所以只好改成mmd）。这里主要搞清楚几个用法
    > 1. 设置categories，和tags的方法。
    > 2. include_code 的路径是相对配置中的code_dir而言的，不要搞错了。
    > 3. 七牛云的图片的路径也是相对于你填的那个前缀而言的。
    > 4. 七牛云的图片处理问题：这个问题主要去[七牛](http://developer.qiniu.com/docs/v6/api/reference/fop/image/)看看,就是大小和水印的问题
7. 发布，我们的markdown可以通过`hexo generate(hexo g)`来生成静态页面即发布时的文件（至于public文件夹下）,通过`hexo clean`来清除，通过`hexo deploy(hexo d )`发布到指定的仓库。

#### 接下来看看效果了。
{% link kblog http://kangqingfei.cn go to kblog %}

<a href="mailto:kangqingfei@gmail.com" title="Email">Write to me</a>

{% include_code test.c lang:c test.c %}

{% codeblock yilia_config.ym lang:config http://kangqingfei.qiniudn.com/blog%2Fcodes%2Fconfigs%2Fyilia_config.yml 源文件 %}
code snippet
{% endcodeblock %}

{% img class names /assest/images/test.png 32 32 图 图片 %}

![图片](/assest/images/test.png)

顺便送本电子书
{% pdf http://kangqingfei.qiniudn.com/blog%2Fpdf%2Ftest.pdf %}


{% qnimg test.png title:test alt:来自七牛云的图片 'class:class1 class2' %}
{% qnimg test.png attr1:value1 attr2:value2 'attr3:value31 value32 value3n' [extend:?imageView2/2/w/600 | normal:yes] %}
