---
layout: post
title: "DigitalOcean VPS转移机房"
date: "2015-08-27"
category: tips
tags: [mongo,node,grunt]
---


******
今天又是折腾自己的VPS，发现以前只是把它关机了还是会收费的，，，真是脑残，应该把droplets删了才会停止收费！所以很无语地发现自己的ss好慢啊，自从某博士的科学上网服务停止后，看视频都没劲了，果断给VPS换地方。在官网的测速网站[http://speedtest-ams1.digitalocean.com/](http://speedtest-ams1.digitalocean.com/)上测完后发现AMS1的速度快得达到满速，可惜的是那个地方不提供创建droplets了，而AWS2的速度只有1M左右其他都不理想，不过SGP1的速度貌似有6、7M但是听说不稳定管他呢，总比开始的NYC3好吧。所以先关机创建snapshot然后把它发到新加坡机房，貌似有点久半小时左右。然后创建新的droplets选择刚发过去的快照最后拿到root密码改过密码后感觉贼快了，比开始的一卡一卡的好多了，至少ssh不会卡顿，恩还是很好地说。

## 问题来了

转过来后发现自己网站的头像图片挂了其他的却正常，一想自己的头像图片是存在mongo里面的其他就没有，可能是mongo的问题
仔细一查果然 node 起不来了 mongo 也有问题了，其中mongo报错  

    Error: couldn't connect to server 127.0.0.1:27017 (127.0.0.1),
    connection attempt failed at src/mongo/shell/mongo.js:146
    exception: connect failed
Node 报错：

    Running "connect:server" (connect) task
    Waiting forever...
    Fatal error: listen EADDRNOTAVAIL


然后各种查一开始以为是两个东西的问题，就把啥锁也清了，也修复了。后面发现远程能进入mongo，本地却不行，觉得特别地奇葩，然后搜到可能是hosts的原因，可以cat /etc/hosts 发现有127.0.0.1解析到localhost啊，，，怎么办呢。我就ping 127.0.0.1 发现 unreachable ，在一搜 发现可能是hosts ,lo ,firewall ,icmp_echo_ignore出了问题。果然 ifconfig 发现没有 lo，，，，，，顿时晕了。。所以，ifconfig lo up 后**整个世界都亮了**。PS：本人的vps会开一段时间，恩，`需要ss`的可以**微博或邮件联系我**
