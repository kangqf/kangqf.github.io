---
title: my git note
categories:
  - tips
tags:
  - git
  - note
date: 2015-12-31 23:47:42
---

### 从13年接触git到现在已经两年半了，仍然不能很熟练的使用，遇到麻烦的问题还要在网上搜命令，虽然以前有在本子上写笔记，但是谁没事带本本子啊，所以在这里总结一下git的使用。

#### 首先git是一个很有名的版本控制系统，一开始接触（github）的时候，我以为它只是一个用来找代码的地方，当时要和学长在那里搞了半天才把代码上传到github，想想真是艰辛。github又称gayhub是使用git让你免费托管代码（就像这篇博客的html代码）的地方（国内也有类似的[Coding.net](https://coding.net/) ），相当于是一个远程的仓库。gitlab是一个使用git的开源的工具，可以让你搭建起类似github这样的环境。

#### 怎么学习git?首先推荐一个[可视化学习git](http://pcottle.github.io/learnGitBranching/)的网站，这个网站基本上覆盖了常用的命令，而且非常形象生动，写这篇文章之前我也是把里面的所有level过了一遍。还有[15分钟学习git](https://try.github.io/levels/1/challenges/1)，可以看到一些文件的变化。再者就是看一些博客，就像我下面附上的阮老师的博客。如果想很系统地了解的话可以看看书，比较有名的有progit，因为git推出了2.0版本，所以也就有了[progit2-zh](https://github.com/progit/progit2-zh)，相关的书还有：
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
  * 首先使用git时你可能要配置一些个人信息，特别是使用github，如果没有得到认证授权将会受到速率方面的一些限制，所以需要配置个人信息。还有就是编辑器方面的配置，
  * `git config --list` 来查看我们的一些配置信息
  * `git config --global --user.name=name` 配置用户名
  * `git config --global --user.email=name@xxx.xxx` 配置用户邮箱
  * `git config --global core.editor "gedit -s"` -s 的意思是将gedit作为一个的单独的窗口打开
  * 我们还可以直接修改.git文件夹下的config文件来配置git。
  * 通过`git help`来获得帮助，通过`git help command`来获得具体命令的帮助，在windows中可能会打开html文档，或与之关联的浏览器，在那里你可以获得所有的帮助信息。

3. init & clone & rm & mv & ls-files
  * `git init` 新建一个git仓库
  * `git clone URL DIR` 克隆一个仓库，URL可以是http(s)、ssh、ftp、等链接协议，-o <name>可以配置远程追踪的仓库(默认origin)，-b <name> 可以指定克隆的分支（默认是HEAD指向的分支）--depth <depth> 可以指定克隆的深度，这对于很多提交的仓库可以大大减小它的大小

  * `git rm <file> ` 将文件移出暂存区，支持正则。
  * `git mv <file> <source> <destination>` 移动或是重命名文件
  * `git ls-files` 命令的选项有-c(cached)、d(deleted)、o(others)、i(ignored)、s(stage)、u(unmerged)、k(killed)、m(modified)。

4. add & commit
  * `git add <file>` 添加文件，可以只用简单的正则进行匹配
  * `git add -A(all)` 来添加所有的文件。
  * `git commit -a -m "msg"` -a 表示将所有修改或删除的文件添加到暂存区，-m表示本次修改的提示信息
  * ` git commit --amend ` 可以用来修改最近一次提交的注释信息，但是一旦你更新到远程仓库这个命令就不管用了。

5. branch & checkout
  * branch 用来创建、查看、删除分支。
  * `git branch branchName` 创建一个新的分支。
  * `git branch ` 查看当前分支，-a可以列出所有分支信息，包括远程的分支。
  * `git branch -d|-D` 用来删除或是强制删除一个分支，可以使用-r表示对远程分支进行操作
  * `git branch -u origin/master foo`  设置远程分支与本地分支的映射关系
  * `git branch -f master HEAD~3`（强制）移动 master 指向 HEAD 的第3级父提交（注意可能会丢失当前分支的所有更改）,也可以理解为将HEAD~3命名为master即使是master已经存在
  * `git checkout branchName <remote>/<branch>` -b 可以用于在目标分支不存在时新建一个分支然后切换 -t|--track <remote>/<branch> 用于设置该分支所追踪的远程分支，默认设置远程同名分支为追踪分支

6. log & reflog & status & show
  * log 用来查看16进制标识的每次提交的版本号（hash 值），使用hash值得时候你只需要输入前面几个字符就好（一般至少用4个），这个命令比较复杂，具体可以去看帮助文档，主要是一些输出的格式和显示问题，下面列出常用的格式化输出方式
  * `git log --all --decorate --graph --oneline` --decorate 会显示出tag信息，--graph 可以图形化地表示出分支合并历史， --oneline 每条log只显示一行
  * 还有其他的显示设置，包括时间、作者、关键词的筛选，以及一些变量的使用。
  * git reflog 可以查看所有分支的所有操作记录（包括（包括commit和reset的操作），包括已经被删除的commit记录，git log则不能察看已经删除了的commit记录，也就是说git reflog列出了head曾经指向过的一系列commit。要明白它们只存在于你本机中；而不是你的版本仓库的一部分，也不包含在push和merge操作中。
  * 对于大型的仓库，通常用`git fsck --lost-found`（文件系统检测）来找回丢失的提交，git fsck 相对reflog是有优势的。比方说你删除一个远程的分支然后关闭仓库。 用fsck 你可以搜索和恢复已删除的远程分支。
  * `git status` 用于显示工作区的当前状态，包括哪些文件没有被提交，以及哪些文件没有纳入版本库管理。
  * `git show` 用来查看某次提交的具体情况（包括文件的修改情况），默认查看最新一次的提交，也可以指定一个tag或hash值来查看其它的提交。当然它也可以像log一样格式化输出。

7. merge & rebase & cherry-pick & HEAD
  * 这两个命令一定要区分清楚，容易搞混。到底是使用哪个是一个习惯的问题，视自己情况而定。
  * merge 合并产生一个特殊的提交记录，它包含两个唯一父提交。
  * `git merge buxfig` 就是把bugfix的内容包含到当前的分支，相当当前分支作为默认参数放在后面，即`git merge bugfix master`，--no-ff -m <message> 可以禁止fast forward，也就是会新建一个提交。
  * `git rebase C1 C2`是在分支之间合并工作的第二种方法。
  * rebase 就是取出一条分支(前面的分支C1)的一系列新的的提交记录，"复制"它们，然后把它在另一条分支(后面的分支C2)后面新建一个提交放下来（C1->C2），这样新的分支就不会有两个父节点，默认将当前分支名作为C2参数传递。
  * `git cherry-pick <Commit1> <Commit2> <...>` 将一些提交复制到你当前的位置 HEAD 下面。按照顺序选择性的合并一条分支。就是说那个分支中的一些东西是有必要合并的而另一些是没有必要的（注意当你选取的那个提交与当前提交的内容没有区别时会报错，因为你将创建一个没有任何修改的提交）。
  * 举一个场景，我们要排掉某个bug 然后新建的一条bug分支，然后提交了5次，前面五次都没能解决bug，只有最后一次的提交是正确解决了bug的，这样我们就需要只是选取bug分支的最后一个提交就ok了，我们就可以切换到 master 然后 rebase -i bug,`git rebase -i branchname/hashvalue` 以交互式的方式让你自己选择要复制的提交(重排那些提交),同样的可以使用cherry-pick来选取需要的分支。
  * HEAD 是当前提交记录的符号名称， HEAD 总是指向最近一次提交记录，大多数修改工作树的 git 命令都开始于改变，HEAD 指向一般HEAD都是指向分支名，所以HEAD总是跟在分支后面。 ** 分离 HEAD ** 就是让其指向一个提交记录而不是分支名。
  * 相对引用 使用 ^ (可以叠加(HEAD^^) = (HEAD~2(后退多步)))来将HEAD移动到指定提交的父提交。相对引用最多的就是移动分支。你可以使用 -f 选项直接让分支指向另一个提交（详细见git branch -f）。
  * 和 ~ 修改符一样，^ 修改符之后也可以跟一个（可选的）数字。这不是用来指定向上返回几代（~ 的作用），^ 后的数字指定跟随合并提交记录的哪一个父提交。当一个合并提交有多个父提交吧，所有选择哪条路径不是那么清晰。git 默认选择跟随合并提交的"第一个"父提交，使用 ^ 后跟一个数字来改变这一默认行为。

8. reset & revert & tag & describe
  * reset 和 revert 又是两个需要区别的概念。
  * git reset 往回移动分支，只是撤销了提交而已,但是最近一次提交的修改仍然存在（干净的暂存区被污染了），对别人的远端分支是无效的。
  * git revert 新建一个提交用来撤销最近一次提交的,最近一次提交的修改已经不存在（干净的工作区依旧干净）。
  * `git reset [--hard|soft|mixed] [<commit>|HEAD]`：将当前的分支重设（reset）到指定的<commit>或者HEAD（默认，如果不显示指定commit，默认是HEAD，即最新的一次提交），并且根据[mode]有可能更新暂存区和工作区。hard的意思是所有内容都丢弃，并且移动HEAD，soft的意思是仅仅移动HEAD指针（文件修改被移出快照，未被commit），mixed是默认的模式，它的意思是文件修改未被add，已经移出了暂存区。
  * `git revert [<commit>|HEAD]`git revert是提交一个新的版本，将需要revert的版本的内容再反向修改回去，版本会递增，不影响之前提交的内容。也即是相当于cherry-pick 一个之前的版本。
  * `git tag version [commit]`可以永远地指向某个特定的可以永远地指向某个特定的 commit，就像是表示一个"里程碑"一样 有新的 commit 时，它们也不会移动，你不可以checkout到 tag 上面 commit，tag 的存在就像是一个在 commit tree 上的表示特定讯息的一个锚。`git tag -d [tagname]`用来删除一个tag。
  * `git describe [ref] `用来显示离你最近的锚点（也就是 tag）,[ref] 是任何一个可以被 git 解读成 commit 的位置，如果你没有指定的话，git 会以你目前所在的位置为准（HEAD）。指令的输出就像这样：`<tag>_<numCommits>_g<hash>` <tag>表示的是离 <ref> 最近的 tag， numCommits 是表示这个 tag 离 <ref> 有多少个 commit， <hash> 表示的是你所给定的 <ref> 所表示的 commit 的hash值。

9. remote
  * 使用git肯定要用到远程分支，远程分支有一个全名规范， 它们以这样的格式显示:<remote_name>/<branch_name>。大多数的开发者会将它们的远端命名为 origin。
  * `git remote -v` 可以列出远程追踪的地址，包括push和fetch。当你克隆时, git 会创建跟踪分支(就像 origin/master), 对于每个远端分支, 创建一个跟踪远端分支的本地分支 (master), 所以你经常会看到这个的命令输出:`local branch "master" set to track remote branch "origin/master" `
  * `git remote add <remotename> <url>` 添加远程仓库（适用于有多个远程仓库的情况）
  * `git remote rename <old_name> <new_name>`用于重命名远程仓库
  * `git remote rm <remotename>` 用于删除远程仓库。

10. fetch + merge = pull
  * git fetch 从远端仓库获取数据。git fetch 完成了两步:下载本地仓库未包含的提交对象，更新我们的远端分支点(如, origin/master)，git fetch不能改变你的本地状态. 你不会更新你的 master 或者 任何与文件系统相关的东西。所以, 你可以将git fetch 的执行 视为下载
  * 一般的流程是我们用 git fetch 更新了远端在本地的副本, 然后git rebase/merge合并我们的工作以映射远端的新变化, 最后再 git push
  * git pull, 就是 fetch 和 merge 的简写. 同样的 git pull --rebase 就是 fetch 和 rebase 的简写！
  * 其实有很多方法的，只要我在本地有新的提交, 你可以像合并其它分支那样合并远端分支. 具体说就是你可以执行以下命令:`git cherry-pick origin/master`,`git rebase origin/master`，`git merge origin/master`。
  * `git fetch <remote->repo> <remote->branch>` 用于获取远端的更新，`git fetch origin <remote->source>:<local->destination>`没有local->source的话会新建本地的destination分支。
  * `git push <remote->repo> <remote->place>` 推送更新到远端
  * `git push origin <local->source>:<remote->destination>`（没有local->source的话会删除远端的destination分支）
  * `git pull origin foo` 相当于：`git fetch origin foo; git merge origin/foo`; `git pull origin bar~1:bugFix` 相当于：`git fetch origin bar~1:bugFix; git merge bugFix`git pull 实际上就是 fetch + merge 的缩写, git pull 在乎的是提交在哪里结束(也就是 git fetch 所确定的 destination)

11. push

12. diff & blame & grep & stash

13. git flow

14. git extra






### 参考文章
* {% link 常用 Git 命令清单 http://www.ruanyifeng.com/blog/2015/12/git-cheat-sheet.html 进入 %}
* {% link Git分支管理策略 http://www.ruanyifeng.com/blog/2012/07/git.html 进入 %}
* {% link Git 协作流程 http://www.ruanyifeng.com/blog/2015/12/git-workflow.html 进入 %}
* {% link Github 的清点对象算法 http://www.ruanyifeng.com/blog/2015/09/git-bitmap.html 进入 %}
