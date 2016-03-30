---
title: 从gitcafe迁移到coding
categories:
  - config
tags:
  - hexo
date: 2016-03-30 10:10:29
---

本来双线部署的时候把国内路线放到了gitcafe，可是好景不长，gitcafe被coding收购，上面的项目也必须在5.31号之前迁移到coding。而且原来在gitcafe的项目变成了只读状态，无奈，只好再折腾一番。
1. coding提供了傻瓜式的迁移方案，就是登陆你的gitcafe的账号，再登陆你的coding的账号，然后两个账号绑定在一起，然后点击一个按钮就可以开始迁移了。迁移过程有点慢，我也不清楚他什么时候迁移好了，我是20多天前点了迁移，今天刚好有时间进去发现迁移好了，理论上不需要这么久的。
<!-- more -->

2. 修改我们的博客的部署。需要在项目的pages的配置里面开启我们的coding pages的服务。
  * 首先上传你的公钥到coding，特别注意的是要在账户里面的ssh公钥里面添加，这样才有写权限。而在项目设置里面添加的部署公钥是只有读权限没有写权限的。
  * 然后在hexo的配置文件中将原来gitcafe的项目ssh地址改成coding的项目ssh地址。
  * 将原来的gitcafe的分支gitcafe-pages改成coding的分支coding-pages，所以最后我的国内线路的分支就变成了 `conding: git@git.coding.net:kangqf/kangqf.git,coding-pages`。
3. 添加域名绑定。和gitcafe一样我们需要在DNS解析那里加上{username}.coding.me的CNAME解析记录。然后在coding的pages下面绑定我们自己的域名。记得原来的CNAME的配置文件也依然不能丢。
4. 让我们的提交message支持时间。修改我们的配置文件，原来我们的deploy是：
```
deploy:
  type: git
  message: "win8 hexo delopy"
  repo:
    github: git@github.com:kangqf/kangqf.github.io.git,master
    gitcafe: git@gitcafe.com:kangqf/kangqf.git,gitcafe-pages
```
最后我们的deploy 就变成了：
```
deploy:
- type: git
  message: win8 hexo delopy at {{ now('YYYY-MM-DD HH:mm:ss') }}
  repo: git@git.coding.net:kangqf/kangqf.git
  branch: coding-pages
- type: git
  message: win8 hexo delopy at {{ now('YYYY-MM-DD HH:mm:ss') }}
  repo: git@github.com:kangqf/kangqf.github.io.git
  barnch: master
```
