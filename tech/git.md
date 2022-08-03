---
title: git
createdAt: 2020-10-09
categories: 
- soft
tags: 
- git
---

[**Git**](https://en.wikipedia.org/wiki/Git) 是一个分布式的管理系统，作者 [Linus Torvalds](https://en.wikipedia.org/wiki/Linus_Torvalds)  2005 创建它，主要是维护linux内核，现在很多人用它作为大型项目的版本控制软件，来管理源代码，或是一些纯文本的笔记或者文档。本篇文字，很多的文本描述，或者图片资源来自互联网，我只是互联网的搬运工，本文仅作为个人学习的一个记录跟总结。我将在最下方列出部分链接，感兴趣的朋友可以去看看。

<!--more-->

### 基本介绍

#### 特点

Git 有如下几个特点：

1.**直接记录快照，而非差异比较**

2.**近乎所有操作都是本地执行**，所以速度很快

3.时刻保持**数据的完整性**，在保存到 Git 之前，所有数据都要进行内容的校验和（checksum）计算，并将此结果作为数据的唯一标识和索引。换句话说，不可能在你修改了文件或目录之后，Git 一无所知。这项特性作为 Git 的设计哲学，建在整体架构的最底层。所以如果文件在传输时变得不完整，或者磁盘损坏导致文件数据缺失，Git 都能立即察觉。

Git 使用 **SHA-1** 算法计算数据的校验和，通过对文件的内容或目录的结构计算出一个 SHA-1 哈希值，作为指纹字符串。该字串由 40 个十六进制字符（0-9 及 a-f）组成，看起来就像是：

```
24b9da6552252987aa493b52f8696cd6d3b00373
```

Git 的工作完全依赖于这类指纹字串，所以你会经常看到这样的哈希值。实际上，所有保存在 Git 数据库中的东西都是用此哈希值来作索引的，而不是靠文件名。

4.**多数操作仅添加数据**，不用担心数据丢失

5.**文件的三种状态**，对于任何一个文件，在 Git 内都只有三种状态：已提交（**committed**），已修改（**modified**）和已暂存（**staged**）。已提交表示该文件已经被安全地保存在本地数据库中了；已修改表示修改了某个文件，但还没有提交保存；已暂存表示把已修改的文件放在下次提交时要保存的清单中。

由此我们看到 Git 管理项目时，文件流转的三个工作区域：Git 的工作目录，暂存区域，以及本地仓库。

![工作目录，暂存区域，以及本地仓库](https://git-scm.com/figures/18333fig0106-tn.png)

每个项目都有一个 Git 目录（如果 `git clone` 出来的话，就是其中 `.git` 的目录；如果 `git clone --bare` 的话，新建的目录本身就是 Git 目录。），它是 Git 用来保存元数据和对象数据库的地方。该目录非常重要，每次克隆镜像仓库的时候，实际拷贝的就是这个目录里面的数据。

从项目中取出某个版本的所有文件和目录，用以开始后续工作的叫做工作目录。这些文件实际上都是从 Git 目录中的压缩对象数据库中提取出来的，接下来就可以在工作目录中对这些文件进行编辑。

所谓的暂存区域只不过是个简单的文件，一般都放在 Git 目录中。有时候人们会把这个文件叫做索引文件，不过标准说法还是叫暂存区域。

#### 工作流程

基本的 Git 工作流程如下：

1. 在工作目录中修改某些文件。
2. 对修改后的文件进行快照，然后保存到暂存区域。
3. 提交更新，将保存在暂存区域的文件快照永久转储到 Git 目录中。

所以，我们可以从文件所处的位置来判断状态：如果是 Git 目录中保存着的特定版本文件，就属于已提交状态；如果作了修改并已放入暂存区域，就属于已暂存状态；如果自上次取出后，作了修改但还没有放到暂存区域，就是已修改状态。

#### 技术说明

文件内容并没有真正存储在索引(*.git/index*)或者提交对象中，而是以blob的形式分别存储在数据库中(*.git/objects*)，并用SHA-1值来校验。 索引文件用识别码列出相关的blob文件以及别的数据。对于提交来说，以树(*tree*)的形式存储，同样用对于的哈希值识别。树对应着工作目录中的文件夹，树中包含的 树或者blob对象对应着相应的子目录和文件。每次提交都存储下它的上一级树的识别码。

如果用detached HEAD提交，那么最后一次提交会被the reflog for HEAD引用。但是过一段时间就失效，最终被回收，与`git commit --amend`或者`git rebase`很像。

### 基本命令

Git 命令是一些命令行工具的集合，它可以用来跟踪，记录文件的变动。比如你可以进行保存，比对，分析，合并等等，这个过程被称之为版本控制。

#### git config

git的基本配置，配置文件在用户目录下的**.gitconfig**

**user** 设置当前系统的git用户的名称跟邮件

**alias** 设置一些别名，简化命令，提高效率，当然本文档为了介绍git的命令的使用，在下文的shell中都没有使用别名。

**http** 跟 **https** 设置shadowsocks的http代理，避免有的仓库被GWF墙，拉取不了。

```shell
git config --global user.name "jianchengwang"
git config --global user.email "jiancheng_wang@yahoo.com"

git config --global https.proxy socks5://127.0.0.1:1080
git config --global https.proxy socks5://127.0.0.1:1080
git config --global --unset http.proxy
git config --global --unset https.proxy

vim ~/.gitconfig
##
[user]
	name = jianchengwang
	email = jiancheng_wang@yahoo.com

[alias]
	co = checkout
	br = branch
	ci = commit
	st = status
	unstage = reset HEAD --
	dog = log --all --decorate --oneline --graph

[http]
	proxy = socks5://127.0.0.1:1080

[https]
	proxt = socks5://127.0.0.1:1080
```

#### git init 

Initialized empty Git repository， 在目录下为出现一个**.git**目录文件夹，是为你的项目存储所有历史和元信息的目录 - 包括所有的对象(commits,trees,blobs,tags)，这些对象指向不同的分支。

```shell
mkdir git-tutorial
cd git-tutorial
git init
```

#### git status

可以看到当前目录的git状态，比如所处分支，提交记录，未追踪的文件等，还有一个很好的命令提示作用，提示你可以使用哪些命令进行操作。

```shell
git status
##
n branch master

No commits yet

Untracked files:
  (use "git add <file>..." to include in what will be committed)

	README.md

nothing added to commit but untracked files present (use "git add" to track)
```

#### git add

未追踪的文件，我们通过这个命令告诉git将这个文件进行版本控制，添加到暂存区 **staged**，暂存区的记录在**.git/index**

```shell
git add README.md

git status
##
On branch master

No commits yet

Changes to be committed:
  (use "git rm --cached <file>..." to unstage)

	new file:   README.md

git rm --cached README.md

git add -A
```

#### git mv

移动或者重命名文件，目录，或符号链接，类比`linux mv`

```shell
git mv a.txt dir
```

如果是`window，mac`系统对大小写不敏感，一般`git config ignorecase true`，这时候也可以用`git mv`修正文件大小写问题，

```shell
git mv -f a.js A.js
```

#### git commit

通过以下命令将暂存区的内容提交到本地仓库，会生成一个版本快照。

```shell
git commit -m "add README.md"
##
[master (root-commit) 65f838e] add README.md
 1 file changed, 0 insertions(+), 0 deletions(-)
 create mode 100644 README.md
```

#### git remote

如果我们需要跟别人进行协作开发，就要把本地的代码推送到远程的服务器的仓库上，比如这里我在github上创建一个远程仓库，然后将本地仓库添加一个远程仓库地址，名字命名为**origin** 一般，这个名字是约定俗称的，不建议去修改，但是假如你有多个远程仓库，可以自定义命名。

```shell
## 添加远程仓库
git remote add origin https://github.com/jianchengwang/git-tutorial.git

## 查看远程仓库
git remote -v
origin	https://github.com/jianchengwang/git-tutorial.git (fetch)
origin	https://github.com/jianchengwang/git-tutorial.git (push)

git remote -help

```

#### git push

我们可以将本地仓库当前分支的代码推送到远程仓库的分支上，**-u** 表示set upstream for git pull/status 即设置当前远程分支为默认上游仓库，下次直接使用git push即可

```shell
# 格式
git push <远程仓库名> <本地分支名>:<远程分支名>

# 省略远程分支名，则表示将本地分支推送与之存在”追踪关系”的远程分支(通常两者同名)，如果该远程分支不存在，则会被新建
git push -u origin master
git push origin master:master

# 省略本地分支名，则表示删除指定的远程分支，因为这等同于推送一个空的本地分支到远程分支。
git push origin :master
git push origin --delete master

# 如果当前分支与远程分支之间存在追踪关系，则本地分支和远程分支都可以省略。
git push origin

# 如果当前分支只有一个追踪分支，那么主机名都可以省略。
git push

# 推送本地所有分支到远程仓库
git push --all origin

# 如果远程主机的版本比本地版本更新，推送时Git会报错，要求先在本地做git pull合并差异，然后再推送到远程主机。这时，如果你一定要推送，可以使用–force选项。使用–force选项，结果导致在远程主机产生一个”非直进式”的合并(non-fast-forward merge)。除非你很确定要这样做，否则应该尽量避免使用–force选项。
git push --force origin

# git push不会推送标签(tag)，除非使用–tags选项。
git push origin --tags
```

#### git clone

克隆远程仓库到本地文件夹git-demo，然后进行一些简单的修改，然后推送到远程仓库，现在我们有两个本地仓库了，其中git-demo的代码已经发生改变，并且推送到远程仓库，远程仓库的代码也发生改变，但是之前的本地的git-tutorial代码还是旧的，这时候我们切换到之前的git-tutorial文件目录，拉取远程仓库的代码。

```shell
git clone https://github.com/jianchengwang/git-tutorial.git git-demo
cd git-demo
echo "hello git" > README.md
git add README.md 
git commit -m 'modify README.md'
git push
```

#### git fetch | pull

取回远程主机某个分支的更新，再与本地的指定分支合并

```shell
git pull <远程仓库> <远程分支名>:<本地分支名>

git pull origin master:master

# 如果远程分支与当前分支进行合并，则冒号后面的本地分支可以省略
git pull origin master

# 等同于
git fetch origin
git merge origin/master

# 如果当前分支与远程分支存在追踪关系
git branch --set-upstream master origin/master
git pull origin

# 如果当前分支只有一个追踪分支
git pull
```

#### git branch

在实现一些新功能或者修复 BUG 的时候，我们希望所有的修改环境都是独立的，Git 给我们提供了分支，它可以保证稳定版本的代码不会被破坏、不同的功能可以由不同开发者同时开发、开发者可以专注于自己的分支，不用担心被其他人破坏了环境。

**HEAD** -> 当前分支的最后一次更新，通常默认是指向master的最后一次提交。我们可以移动这个指针或者叫坐标，就可以变更使用的分支。

提交时使用~(tilde)和^(caret)就可以指定某个提交的相对位置。最常用的就是相对于HEAD的位置。HEAD后面加上~(tilde）可以指定HEAD之前的提交记录。合并分支会有多个根节点，您可以用^(caret) 来指定使用哪个为根节点。

![用tilde和caret指定提交的相对位置](https://backlog.com/git-tutorial/cn/img/post/stepup/capture_stepup1_3_2.png)



**STASH** -> 还未提交的修改内容以及新添加的文件，留在索引区域或工作树的情况下切换到其他的分支时，修改内容会从原来的分支移动到目标分支。

但是如果在checkout的目标分支中相同的文件也有修改，checkout会失败的。这时要么先提交修改内容，要么用stash暂时保存修改内容后再checkout。

stash是临时保存文件修改内容的区域。stash可以暂时保存工作树和索引里还没提交的修改内容，您可以事后再取出暂存的修改，应用到原先的分支或其他的分支上。

![stash](https://backlog.com/git-tutorial/cn/img/post/stepup/capture_stepup1_3_3.png)



我们在所在分支创建一个分支的时候，新建的分支会拥有当前分支的一份拷贝。

```shell
# 创建分支
git branch feature1

# 切换分支
git checkout feature1

# 创建并切换
git checkout -b feature2

# checkout 后面跟文件名，会使用 HEAD 中的最新内容替换掉你的工作目录中的文件。已添加到暂存区的改动以及新文件都不会受到影响。
git checkout -- <filename>

# 查看当前分支，q键退出
git branch

# 查看远程分支
git branch -r

# 删除分支，如果当前分支处于feature2则不允许删除
git branch -d feature2

# 如果我们分支有进行版本的改变，跟master分支不一致，那么我么将分支的内容merge到master分支上，或是直接使用-D 进行强制删除
git branch -D feature2

# 推送分支到远程仓库并创建一个跟本地分支同名的分支
git push origin feature1

# 推送分支到远程仓库并创建一个名为f1的分支
git push origin feature1:f1

# 删除远程仓库的分支
git push origin :feature1
```

#### git tag 

为软件发布创建标签是推荐的。这个概念早已存在，在 SVN 中也有。你可以执行如下命令创建一个叫做 *1.0.0* 的标签

```shell
git tag 1.0.0 1b2e1d63ff
```

#### git log

通过日志，我们可以很方便的查看并且追溯各个版本快照，并且stackoverflow有人展示了很多漂亮的日志格式，我们可以借鉴下

https://stackoverflow.com/questions/1057564/pretty-git-branch-graphs

```shell
git log

# 只看某人的提交日志
git log --author=bob

# 一行显示
git log --online

# 显示前10条
git log --oneline -10

# 显示某一次日志详情
git show a2f5b8c

# 因为命令很长，所以我们可以在.gitconfig里设置别名
git log --all --decorate --oneline --graph
git dog
```

#### git merge | rebase

在做一些新特性或者修复 bug 的时候，我们通常会建立一个新的分支，而完成后我们需要做一个合并分支的操作。

合并分支默认使用fast-forward(**--ff**)模式，这种模式不会产生一个新的commit提交，如果你要使用**--no-ff**，如果是与他人协作开发，建议采用这种模式，因为它会原有的开发分支整个提交链的完整性。

使用**rebase** 会重写项目历史，能让我们的项目提交日志保持一条竖线，但是这破坏协作的工作流，所以有一条黄金法则，**绝对不要在公共分支上使用rebase**

**merge** 是合并操作，它会将两个分支的操作合并在一起，它关注点在真实的提交历史记录上

**rebase** 没有合并操作，它只是将当前分支的修改复制到目标分支的最后一次提交上面，它的关注点在开发的过程上面

如果两个分支同时对一个文件进行修改，并且commit，那么合并的时候会产生**conflict** ，我们要解决冲突后才能进行合并操作。解决冲突一般会使用一些可视化的工具进行解决，命令行的话即使是使用vimdiff感觉也比较麻烦。

```shell
# merge
git checkout master
git merge feature1 
git merge feature1 --no-ff
# 将分支issue1的所有提交合并成一个提交并导入到master分支
git merge --squash issue1

# rebase
git rebase feature1

# conflict
```

#### git cherry-pick

**git cherry-pick** 可以选择某一个分支中的一个或几个commit(s)来进行操作。例如，假设我们有个稳定版本的分支，叫v2.0，另外还有个开发版本的分支v3.0，我们不能直接把两个分支合并，这样会导致稳定版本混乱，但是又想增加一个v3.0中的功能到v2.0中，这里就可以使用cherry-pick了,其实也就是对已经存在的commit 进行再次提交.

命令集合:

- git cherry-pick <commit id>:单独合并一个提交
- git cherry-pick  -x <commit id>：同上，不同点：保留原提交者信息。
   **Git从1.7.2版本开始支持批量cherry-pick，就是一次可以cherry-pick一个区间的commit。** 
- git cherry-pick <start-commit-id>..<end-commit-id>
- git cherry-pick <start-commit-id>^..<end-commit-id>

前者表示把<start-commit-id>到<end-commit-id>之间(左开右闭，不包含start-commit-id)的提交cherry-pick到当前分支；
 后者有"^"标志的表示把<start-commit-id>到<end-commit-id>之间(闭区间，包含start-commit-id)的提交cherry-pick到当前分支。
 其中，<start-commit-id>到<end-commit-id>只需要commit-id的前6位即可，并且<start-commit-id>在时间上必须早于<end-commit-id>
 注：以上合并，需要手动push代码。

```shell
# 从其他分支复制指定的提交，然后导入到现在的分支
git cherry-pick 99daed2

git cherry-pick <commit id>
git cherry-pick --continue
git cherry-pick --quit
git cherry-pick --abo
```



#### git reset | revert

时光不能倒流，但是 Git 可以做时光机让你的提交记录回到过去。git revert 撤销某次操作，此次操作之前和之后的 commit 和 history 都会保留，并且把这次撤销作为一次最新的提交。git revert是提交一个新的版本，将需要revert的版本的内容再反向修改回去，版本会递增，不影响之前提交的内容。相比 git reset，它不会改变现在的提交历史。因此，git revert 可以用在公共分支上，git reset 应该用在私有分支上。

在reset可以遗弃不再使用的提交。执行遗弃时，需要根据影响的范围而指定不同的模式，可以指定是否复原索引或工作树的内容。

![遗弃提交](https://backlog.com/git-tutorial/cn/img/post/stepup/capture_stepup6_3_1.png)

除了默认的mixed模式，还有soft和hard模式。欲了解受各模式影响的部分，请参照下面的表格。

| 模式名称 | HEAD的位置 | 索引   | 工作树 |
| -------- | ---------- | ------ | ------ |
| soft     | 修改       | 不修改 | 不修改 |
| mixed    | 修改       | 修改   | 不修改 |
| hard     | 修改       | 修改   | 修改   |

**主要使用的场合：**

- 复原修改过的索引的状态(mixed)
- 彻底取消最近的提交(hard)
- 只取消提交(soft)

```shell
# 回退到之前一次提交
git reset master^

# 回退到之前5次提交
git reset master~5

# 回到之前某次提交
git reset --hard HEAD~
gti reset --hard 99daed2

# 在reset之前的提交可以参照ORIG_HEAD。Reset错误的时候，在ORIG_HEAD上reset 就可以还原到reset前的状态。
git reset --hard ORIG_HEAD

# 会产生一次新的commit提交
git revert --hard HEAD~
```

#### git stash

git stash 可用来暂存当前正在进行的工作， 比如想pull 最新代码， 又不想加新commit， 或者另外一种情况，为了fix 一个紧急的bug, 先stash, 使返回到自己上一个commit, 改完bug之后再stash pop, 继续原来的工作。

```shell
git statsh
do some work
git statsh pop

git stash          # save uncommitted changes
# pull, edit, etc.
git stash list     # list stashed changes in this git
git show stash@{0} # see the last stash 
git stash pop      # apply last stash and remove it from the list
git stash --help   # for more info
```



#### .gitignore

记录为.gitignore的文件是Git的非管理对象，但是需要提交.gitignore本身。

可以到 [Gitignore网站](www.gitignore.com)上找忽略模板。

#### fock | pull request

我们经常fock其他上游仓库，来pull request做贡献等，首先是保证本地仓库跟上游仓库的同步。关于pr相关操作，可以看相关链接的工作流，这里不多做介绍。

```shell
## 添加上游仓库
git remote add upstream https://github.com/jianchengwang/git-tutorial.git

## 查看远程仓库列表
git remote -v

## 拉取上游仓库dev分支，如果没有分支名，默认拉取master分支的代码
git fetch upstrem dev

## 合并分支
git checkout 
git rebase upstream/dev

## 提交到我们自己的远程仓库
git push
```

### SSH 免密登录

我们会发现每次进行git push的时候都要输入密码，这样比较麻烦，所以我们可以通过[ssh](https://en.wikipedia.org/wiki/Secure_Shell)进行免密登录。

这里只列出了linux的简单命令，其他系统或是更详细的帮助信息请自行查看github的 [相关帮助文档](https://help.github.com/en/articles/generating-a-new-ssh-key-and-adding-it-to-the-ssh-agent)

```shell
## 创建 ssh key
ssh-keygen -t rsa -b 4096 -C "jianchengwang@yahoo.com"

## 添加 ssh key -> ssh-agent
eval "$(ssh-agent -s)"
ssh-add ~/.ssh/id_rsa

## add ssh public key to your github account
cat ~/.ssh/id_rsa.pub
```

### Hub

[hub](https://hub.github.com/) is *an extension to command-line git* that helps you do everyday GitHub tasks without ever leaving the terminal

我一般比较常用`hub create`命令，你也可以设置别名 `git -> hub`

```shell
# indicate that you prefer HTTPS to SSH git clone URLs
git config --global hub.protocol https

# create a repo to host a new project on GitHub
git init
git add .
git commit -m "And so, it begins."
hub create
→ (creates a new GitHub repository with the name of the current directory)
git push -u origin HEAD

# clone your own project
hub clone dotfiles
→ git clone git://github.com/YOUR_USER/dotfiles.git

# clone another project
hub clone github/hub
→ git clone git://github.com/github/hub.git

# fast-forward all local branches to match the latest state on the remote
cd myproject
hub sync

# share log output via Gist
hub gist create --copy build.log
→ (the URL of the new private gist copied to clipboard)
```



### 相关链接

[官方文档](https://git-scm.com/docs/)

[互联网人都该懂点git](https://www.youtube.com/watch?v=lUQl6gZ5Yy0)

[猴子都能懂的git入门](https://backlog.com/git-tutorial/cn/)

[廖雪峰git教程](https://www.liaoxuefeng.com/wiki/0013739516305929606dd18361248578c67b8067c8c017b000)

[git工作流](https://git.io/fpYgR)

[Git Community Book 中文版](http://gitbook.liuhui998.com/)

[git指南](http://rogerdudler.github.io/git-guide/index.zh.html)

[Git Tutorial](https://www.vogella.com/tutorials/Git/article.html)

[hub](https://hub.github.com/)