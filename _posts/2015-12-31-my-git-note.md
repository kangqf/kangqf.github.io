---
title: my git note
categories:
  - tips
tags:
  - git note
date: 2015-12-31 23:47:42
---

### 从13年接触git到现在已经两年半了，仍然不能很熟练的使用，遇到麻烦的问题还要在网上搜命令，虽然以前有在本子上写笔记，但是谁没事带本本子啊，所以在这里总结一下git的使用。

#### 首先git是一个很有名的版本控制系统，一开始接触（github）的时候，我以为它只是一个用来找代码的地方，当时要和学长在那里搞了半天才把代码上传到github，想想真是艰辛。github又称gayhub是使用git让你免费托管代码（就像这篇博客的html代码）的地方（国内也有类似的[Coding.net](https://coding.net/) ），相当于是一个远程的仓库。gitlab是一个使用git的开源的工具，可以让你搭建起类似github这样的环境。

#### 怎么学习git?首先推荐一个[可视化学习git](http://pcottle.github.io/learnGitBranching/)的网站，这个网站基本上覆盖了常用的命令，而且非常形象生动，写这篇文章之前我也是把里面的所有level过了一遍。再者就是看一些博客，就像我下面附上的阮老师的博客。如果想很系统地了解的话可以看看书，比较有名的有progit，因为git推出了2.0版本，所以也就有了[progit2-zh](https://github.com/progit/progit2-zh)，相关的书还有：
* `Manning.Learn.Git.in.a.Month.of.Lunches.2015.9.pdf`  
* `O'Reilly.Git.for.Teams.2015.8.pdf`
* `OReilly.Introducing.GitHub.2014.11.pdf`  
* `Manning.Git.in.Practice.2014.9.pdf`  
* `[Git.Pocket.Guide(2013.6)].Richard.E.Silverman.文字版.pdf`
* `[Version.Control.with.Git(2nd,2012.8)].Jon.Loeliger.文字版.pdf`  
* `[Pragmatic.Guide.to.Git(2010.11)].Travis.Swicegood.文字版.pdf`

#### 我列出的大都是英文的，而且有点多，我也没看过，也不知道哪本好，可以自己看看选择一下。

1. git 术语。首先先讲一下git的一些术语，参考了阮老师的博客，大概是
   * Workspace：工作区，就是当前工作目录的内容
   * Index / Stage：暂存区，就是被git管理的当前的工作区的内容
   * Repository：仓库区（或本地仓库），就是暂存区的内容保存的地方
   * Remote：远程仓库 ，就是协同开发的时候多个开发者一起维护的仓库
   * 快照，就是一系列的提交构成的仓库的一个版本
   * 检出，就是复制某一快照然后新建一个分支
   * hashValue，git用哈希值来表示一个快照，hashValue一般很长，我们使用的话一般取4位（前面）及以上就可以了。
   * HEAD，指向当前工作快照的一个指针类似的，一般指向当前分支，也可分离指向hash值代表的快照

2. config & help

3. init & clone & add & rm & mv

4. commit

5. branch & checkout

6. log & status & show

7. rabase & merge

8. reset & revert & tag

9. remote

10. fetch + merge = pull

11. push

12. diff & blame & reflog & grep

13. git flow








### 参考文章
* {% link 常用 Git 命令清单 http://www.ruanyifeng.com/blog/2015/12/git-cheat-sheet.html 进入 %}
* {% link Git分支管理策略 http://www.ruanyifeng.com/blog/2012/07/git.html 进入 %}
* {% link Git 协作流程 http://www.ruanyifeng.com/blog/2015/12/git-workflow.html 进入 %}
* {% link Github 的清点对象算法 http://www.ruanyifeng.com/blog/2015/09/git-bitmap.html 进入 %}
