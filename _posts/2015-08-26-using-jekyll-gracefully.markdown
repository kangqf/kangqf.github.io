---
layout: post
title: "using jekyll gracefully"
date: "2015-08-26"
description: "using jekyll gracefully"
category: tools
tags: [jekyll]
---
## 为啥又折腾这货？
1. 在家，看不进书，有时间没事干，所以。。。。。。
2. 鉴于各种社交平台的太那啥，想拥有自己的一个小港湾（好扯淡）
3. 要`认真`考研了，说不定哪天就发神经想写点啥煽情的东西了
4. 觉得很多东西还是得记录一下，就像寒假把这货搞得那么透而现在啥都忘了，毕竟好记性不如烂笔头！
5. 嗯，好像可以装那啥对吧。。。。。 未完待续

## 从头开始说起
1. 首先有个github的账号，然后最好有个域名，然后创建一个`username.github.io`的`repositories`添加`CNAME`文件（里面填上自己的域名即可）让github原生的域名重定向到自己的域名。接下来配置自己域名解析到自己博客托管在github上的服务器，在自己的域名支持`CNAME`的情况下你可以直接将`CNAME`配置为`username.github.io`。像我这种穷人的域名并不支持的情况下，就用`nslookup`查到自己项目的ip，然后解析到这个ip（事实上github会给每个要求的项目分配一个ip而且这货还不变，真是爽），这些就不多说了
2. 拥有一个框架，像我用的 JekyllBootstrap 类似的，要想在本地折腾一下要装上`ruby ruby-dev jekyll rdiscount`然后win下的话要设置好环境变量等。
3. 这样就可以在本地起一个服务器了，切换到项目的根目录：
> * 开启服务器 `jekyll serve` 或 `rake preview` 这会默认在4000端口起服务器
> * 添加一篇文章  `rake post title="Hello World"`或`rake page name="pages/about.md"（不加.md默认生成.html）` post 和 page 区别在于layout的不同
> * 安装themes `rake theme:install git="https://github.com/jekyllbootstrap/theme-the-program.git"`或`rake theme:install name="THEME-NAME"`默认会将安装到`./assets/themes/THEME-NAME`
> * 切换主题 `rake theme:switch name="the-program"`
> * 自定义主题 你应该修改`./_includes/themes/THEME-NAME`下的内容而不是`_layouts`目录下的文件，因为切换主题的时候会把`_layouts`目录下的文件覆盖掉。而对于样式文件应该放在`./assets/themes/THEME-NAME`目录下，并可以使用`ASSET_PATH`变量来引用这些静态的样式。
> * 发布主题 `rake theme:package name="THEME-NAME"`将主题打包，然后可以发布
> * 懒人方法 我喜欢使用Atom来写东西，因为有一个叫jekyll的插件还是很好用的

## 目录
    .
    |-- _data                             _data:保存数据的。jekyll会自动加载这里的所有.jml或者.yaml结尾的文件。
                                          比如你有一个members.yml,那么你可以通过site.data.members访问该文件里的数据。
    |-- _drafts
        `-- jekyll-introduction-draft.md  草稿，格式是:没有日期.md 通过 jekyll serve --drafts预览 jekyll build --drafts编译
    |-- _includes                         包含一些模板，可以重复利用。这种方式是liquid语法
        |-- JB                            JekyllBootstrap库文件
        `-- themes                        主题模板配置目录
    |-- _layouts                          模板文件链接到主题配置中，里面的文件通过 `content` 包含_posts里面的文章
        |-- default.html
        |-- page.html
        `-- post.html
    |-- _posts                            存放你要发表的文章。格式YEAR-MONTH-DAY-title.MARKUP
        |-- xxx.markdown
        `-- xxxxxx.markdown
    |-- _site                             编译后生成的文件
    |-- _theme_packages                   安装或打包主题时的临时目录
    |-- assets
        |-- themes
            |-- THEME_NAME                主题的临时文件
    |-- index.html                        首页
    |-- about.html                        导航栏
    |-- archive.html                      导航栏
    |-- categories.html                   导航栏
    |-- 404.html                          404页面
    |-- tags.html                         导航栏
    |-- atom.xml                          订阅文件
    |-- rss.xml                           订阅文件
    |-- _config.yml                       存储配置数据。把配置写在这个文件里面，可以让你不用在命令行中写。
    |-- sitemap.txt
    |-- CNAME                             域名配置文件
    `-- Rakefile

## 变量
1. site变量
> * site.time:当前运行jekyll的时间
> * site.pages:所有的页面
> * site.posts:以时间逆序排序的所有的文章
> * site.data：包含从目录_data里面加载的数据列表
2. page变量
> * page.content:页面内容
> * page.title:文章标题
> * page.urL:页面地址
> * page.date:页面的日期。可以在front matter重写：2008-12-14 10:30:00 +0900或者YYYY-MM-DD HH:MM:SS
> * page.id:页面id。 在RSS feeds里面有用。
3. Paginator变量
> * paginator.per_page：每一页的文章数
> * paginator.posts：那一页可用的文章
> * paginator.page：当前页的值


## 配置

### 主要是 _config.yml 里面的东西，一看就懂！！！

****
[参考文章](http://blog.csdn.net/maoxunxing/article/details/40479753)
