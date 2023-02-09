---
title: Git使用技巧
date: 2020-06-18 21:40:33
categories: 技术博客
tags:
    - IT，Github
thumbnail: http://cdn.kunkunzhang.top/git-logo.jpg
---

　　Git是最常用的版本管理工具，利于协同开发

​        原来的标题是Github使用技巧，但是后来开发之后发现github和gitlab都是基于Git，因此改为Git

<!--more-->

## 多人协同开发流程

​        一般在开发产品适合，通常挑选一个分支作为可以上线的正式版本分支，比如master或者release，develop是用来开发的，可能带有bug。 当很多人参与同一个项目的时候，如果给每个人都有Commit到master和release分支的权c限是非常不合理的。这个时候，就可以使用Fork + PR/MR的方式来实现多人协作开发。 每个开发者先Fork一份代码到自己的账号下，功能完成后发PR给项目管理者，项目管理者Code Review后确认无误后即可进行Merge操作，这样协作开发效率高，问题少。

## 安装Git

windows端

下载git工具，`[这里是链接](https://git-scm.com/downloads)`，选择适合自己的版本进行安装。

mac端

苹果电脑自带Git。

linux

以centos为例

下载git源代码压缩文件

```shell
wget https://mirrors.edge.kernel.org/pub/software/scm/git/git-2.23.0.tar.xz
```

解压缩和解归档

```shell
xz -d git-2.23.0.tar.xz
tar -xvf git-2.23.0.tar
```

安装底层依赖库

```shell
yum -y install libcurl-devel
```

构建和安装

```shell
make && make install
```

## Git本地设置　　

Windows新安装Git需要设置github账户。Mac默认没有修改的情况mac使用icloud账户登录系统，提交时会提示`Your name and email address were configured automatically based on your username and hostname. Please check that they are accurate`.也需要将提交用户改为github账户。

windows在cmd窗口输入命令，mac在终端输入。

方式一：直接设置自己的用户名和邮箱

```shell
$ git config --global user.name "coliyin@163.com"
$ git config --global user.email "coliyin@163.com"
```

设置SSHkey

方式二：修改配置文件

在终端输入

```shell
git config --global --edit
```

然后会进入vi修改配置文件，将name=和email=之后的内容修改为自己的用户名和邮箱。记得将首列的#号去掉。

修改完后渐入命令使配置生效

```shell
git commit --amend --reset-author
```

也可以按照windows设置，最后使其

**通常来说，本地的Git只能建议只有一个版本，否则提交代码或者pr时会显示多个账号，会造成混乱**

### 邮箱

邮箱是GitHub验证账户的重要标识，包括SSH key的生成。所以一般入职公司之后，如果使用GitHub都会要求改成公司的，所以更换邮箱之后都要重新生成一次public key,直接使用原来的会报错，像这样子

```shell
fatal: unable to access 'https://github.com/Kun8018/Kun8018.github.io.git/': LibreSSL SSL_connect: SSL_ERROR_SYSCALL in connection to github.com:443 
```

在本地环境执行

```shell
ssh-keygen -t rsa -b 4096 -C "your-github@email.com"
```

然后找到对应的ssh key

```shell
cd .ssh
cd ~/.ssh
```

查看key中的内容

```shell
cat id_rsa.pub
```

复制输出并且在GitHub常见ssh key

在GitHub。account -> settings -> create a SSH and GPG keys，把本地的key粘贴进去就好

现在已经可以运行了，可以在本地验证一下

```shell
ssh -vT git@github.com
```

输出是这样就代表可以

```shell
debug1: channel 0: free: client-session, nchannels 1
Transferred: sent 3848, received 2040 bytes, in 0.2 seconds
Bytes per second: sent 16032.4, received 8499.5
debug1: Exit status 1
```

### git全局设置

执行 git pull 命令时，默认是用 git merge 来合并代码的。大家都知道，用 merge 合并代码的节点不在一个分支上，不方便查看节点信息，所以很多公司是采用 git rebase 来合并代码的。针对这种情况，可以在自己的电脑终端，**修改 git 的全局配置**，将 pull 的默认配置改为 rebase、

```shell
git config --global --add pull.rebase true
```

查看配置

```shell
git config --global -l
```

git一共有三个配置文件，权重如下

仓库级配置文件(--local) > 全局级配置文件(--global) >系统级配置文件(--system)

通过 **`git config`** 命令修改 git 配置

查看仓库级的config：`git config --local -l`

查看全局级的config：`git config --global -l`

查看系统级的config：`git config --system -l`

查看当前生效的配置：`git config -l` 会显示最终三个配置文件后的配置信息

编辑配置文件

编辑仓库级的config：`git config --local -e`

编辑全局级的config：`git config --global -e`

编辑系统级的config：`git config --system -e`

## 从远程仓库拉取项目

GitHub可以使用http和ssh两种方式获取代码

https比较简单，但是每次fetch和push都需要账号密码

sshfetch和push不需要在输入账号密码：

```git
ssh-keygen -t rsa -b 4096 -C "1027690173@qq.com"
```

会请求你输入文件名和设置密码，可以不设置直接enter跳过，文件名为默认id_rsa，密码默认为空

在.ssh下查看文件，有id_rsa或id_dsa命名的文件即是，后缀为.pub的是公钥，没有的是私钥

```shell
cd ~/.ssh
ls
```



运行ssh-agent

```shell
eval "$(ssh-agent -s)"
```



```shell
Host * IdentityFile ~/.ssh/id_rsa
```



添加ssh key到github或gitlab

复制公钥

```shell
pbcopy < ~/.ssh/id_rsa.pub
```

粘贴到github ssh-key或者gitlab

首次下载项目

```shell
git clone 
```

获取远程修改到本地

```shell
git  git@github.com:anyangxaut/LearnGit.git
```

## 基本操作

将文件夹变成git仓库

```shell
git init 
```

当你完成了上述操作后，本地目录就变成了工作区（正在操作的工作目录）、仓库和工作区和本地仓库之间的暂存区（也称为缓存区）。

通过`git add`可以将指定的文件或所有文件添加到暂存区。

```shell
git add <file>
git add .
```

如果不希望将文件添加到暂存区，可以按照提示，使用`git rm --cached <file>`命令将文件从暂存区放回到工作区。

如果这个时候对工作区的文件又进行了修改使得工作区和暂存区的内容并不相同了，再次执行`git status`可以看到哪个或哪些文件被修改了，如果希望用暂存区的内容恢复工作区，可以使用下面的命令。

```shell
git restore <file>
git restore .
```

通过下面的命令可以将暂存区的内容纳入本地仓库，

```shell
git commit -m '本次提交的说明'
```

提交commit可以直接关联issue，在issue下面可以直接显示关联的commit代码

```shell
git commit -m '说明 #issue链接'
```

在pr的comment中添加issue的链接可以关联pr与issue，当pr被合并时issue会被自动关闭

如果提交之后才发现之前的修改少了一些内容，回到过去，修改上一次提交的那个文件。如此一来，你的commit记录只会有一条。对于一些有代码洁癖并且看中git commit记录的程序员，这点很重要

执行`git commit --amend --no-edit`之后，hash值由`c56f680`变成了`eb6c8cb`，但是message内容并没有发生变化，并且最重要的是只有一条commit记录。

如果要修改上一条的message，那么去掉`--no-edit`选项即可，`git commit --amend -m "xxxx"`。同理，commit记录同样只会有一条。

可以通过`git log`查看每次提交对应的日志。

```shell
git log
git log --graph --oneline --abbrev-commit
```

gitlog不能显示已经删除的commit记录，需要查看时使用git reflog命令

```shell
git reflog
```

reflog可以显示所有分支的操作记录，包括已经删除的commit，要回复已经删除的commit使用cherry-pick

```shell
git cherry-pick 4c97ff3
```

## 远程操作

添加远程仓库（Git服务器）

```shell
git remote add origin git@gitee.com:jackfrued/python.git
```

从远程仓库取回代码。

```shell
git pull origin master
```

将本地代码（工作成果）推送到远程仓库。

```shell
git push -u origin master:master
```

删除远程分支

执行此命令时慎重操作

```shell
git branch -r -d origin/develop
git push origin :develop

git push origin --delete develop
```

## 分支

创建分支

```shell
git branch <branch-name>
```

切换分支

```shell
git switch <branch-name>
```

分支合并

在`dev`分支上完成开发任务之后，如果希望将`dev`分支上的成果合并到`master`，可以先切回到`master`分支然后使用`git merge`来做分支合并，合并的结果如下图右上方所示。

```shell
git switch master
git merge --no-ff dev
```

在合并分支时，没有冲突的部分Git会做自动合并。如果发生了冲突（如`dev`和`master`分支上都修改了同一个文件），会看到`CONFLICT (content): Merge conflict in <filename>. Automatic merge failed; fix conflicts and then commit the result`（自动合并失败，修复冲突之后再次提交）的提示，这个时候我们可以用`git diff`来查看产生冲突的内容。解决冲突通常需要当事人当面沟通之后才能决定保留谁的版本，冲突解决后需要重新提交代码。

删除分支

如果分支上的工作成果还没有合并，那么在删除分支时会看到`error: The branch '<branch-name>' is not fully merged.`这样的错误提示。如果希望强行删除分支，可以使用`-D`参数。

```shell
git branch -d <branch-name>
error: The branch '<branch-name>' is not fully merged.
If you are sure you want to delete it, run 'git branch -D <branch-name>'.
git branch -D <branch-name>
```

分支变基

分支合并操作可以将多个分支上的工作成果最终合并到一个分支上，但是再多次合并操作之后，分支可能会变得非常的混乱和复杂，为了解决这个问题，可以使用`git rebase`操作来实现分支变基。

```shell
git rebase master
git switch master
git merge dev
```

关联远程分支

如果当前所在的分支还没有关联到远程分支，可以使用下面的命令为它们建立关联。

```shell
git branch --set-upstream-to origin/develop
```

也可以指定别的分支关联到远程分支

```shell
git branch --set-upstream-to origin/develop <branch-name>
```

也创建分支时使用了`--track`参数，直接指定与本地分支关联的远程分支

```shell
git branch --track <branch-name> origin/develop
```

解除关联远程分支

```shell
git branch --track <branch-name> origin/develop
```

### rebase、squash与merge的区别

rebase可以尽可能保持master分支干净，并且易于识别author

squash也可以保持master分支干净，但是master中author都是maintainer，而不是原owner

merge不能保持master分支干净，但是保持了所有的commit history，大多数情况下都是不好的，个别情况好



## 子模块submodule

当你在一个git项目上工作时，你需要在其中使用另一个Git项目。也许它是一个第三方开发的库或者是你独立开发合并在多个父项目中使用。

在git中可以用子模块submodule来管理这些项目，submodule允许你将一个git仓库当作另外一个git仓库的子目录，这允许你克隆另外一个仓库到你的项目中并且保持你的提交相对独立

克隆含有子模块的项目

克隆含有子模块的项目可以先克隆父项目，再更新子模块，另一种是直接递归克隆整个项目

先克隆父项目，再更新子模块

```shell
git clone https://.../.git assets
```

此时子模块子模块还未初始化

初始化子模块

```shell
git submodule init
```

更新子模块

```shell
git submodule update
```

直接递归克隆整个项目

```shell
git clone https://.../.git assets --recursive
```

添加子模块

```shell
git submodule add https://.../.git assets
```

查看子模块

```shell
git status
git submodule
```

更新子模块

```shell
## 更新项目内子模块到最新版本
git submodule update
## 更新子模块为远程项目的最新版本
git submodule update --remote
## 更新所有子模块
git submodule foreach git pull
```

修改子模块

在子模块中修改文件后，直接提交到远程项目分支

```shell
git add .
git ci -m "commit"
git push origin HEAD:master
```

删除子模块

删除子模块比较麻烦，需要手动删除相关的文件，否则在添加子模块时有可能出现错误。

首先删除子模块文件夹

```shell
git rm --cached assets
rm -rf assets
```

删除相关子模块信息

```shell
[submodule "assets"]
	path = assets
	url = https://github.com/../.git
```

删除相关子模块信息

```shell
[submodule "assets"]
	url = https://github.com/../.git
```

删除相关子模块文件

```shell
rm -rf ./git/modules/assets
```

## 子仓库subtree

与submodule的异同

git submodule:

允许其他仓库指定以一个commit嵌入仓库的子目录

仓库clone下来要init和update

会产生文件记录和submodule版本信息

git submodule删除起来比较费劲

git subtree：

避免以上问题

管理和更新流程比较方便

git subtree合并子仓库到项目中的子目录，不用像submodule一样每次子目录修改之后都要init和update，万一每次没update就直接add，将

git 1.5之后建议使用git submodule

使用方法

如果p1项目和p2项目共用S项目

添加subtree

```shell
git subtree add --prefix=<s project path>  <s project url> <branch> --squash
```

修改代码，可以改subtree里面的代码，添加相关commit

pull&push

```shell
git subtree pull --prefix=<s project path>  <s project url> <branch> --squash
git subtree push --prefix=<s project path>  <s project url> <branch> --squash
```

拆分已有项目,比如P项目拆分出s项目

```shell
git subtree split -P <S project path> -b <tmp branch>
```

git会遍历所有commit，分离与S项目有关的commit，并存入临时分支branch中

创建子repo

```shell
mkdir 
cd s new path
git init
git pull <S project path> <tmp branch>
git remote add origin <S github>
git push origin -u master
```

清理原项目中的子项目数据

```shell
cd P project
git rm -rf 
git commit -m
git branch -D 
```

在新项目中添加subtree

```shell
git subtree add --prefix=<s project path>  <s project url> <branch> --squash
git push origin master
```



## 其他操作

### 标签

`git tag -a <your_tag_name>`创建一个标签

`git tag -d <your_tag_name> `删除一个标签

`git tag --list ` 列出所有的标签

`git ls-remote --tags origin`查看所有的远程标签及commit ID

`git push origin <your_tag_name>` 推送一个标签到远程

`git push --delete origin <your_tag_name> `删除远程仓库的标签

### 回退

`git reset`：回退到指定的版本。

`git revert`：撤回提交信息。

 git reset的作用是修改HEAD的位置，即将HEAD指向的位置改变为之前存在的某个版本

git revert是用于“反做”某一个版本，以达到撤销该版本的修改的目的。比如，我们commit了三个版本（版本一、版本二、 版本三），突然发现版本二不行（如：有bug），想要撤销版本二，但又不想影响撤销版本三的提交，就可以用 git revert 命令来反做版本二，生成新的版本四，这个版本四里会保留版本三的东西，但撤销了版本二的东西
如果我们想撤销之前的某一版本，但是又想保留该目标版本后面的版本，记录下这整个版本变动流程，就可以用这种方法。

`git reset --hard HASH` 返回到某个节点，不保留修改，已有的改动会丢失。 

`git reset --soft HASH` 返回到某个节点, 保留修改，已有的改动会保留，在未提交中，git status或git diff可看。

`git clean -df` #返回到某个节点，（未跟踪文件的删除）
`git clean` 参数
   ` -n` 不实际删除，只是进行演练，展示将要进行的操作，有哪些文件将要被删除。（可先使用该命令参数，然后再决定是否执行）
    `-f` 删除文件
    `-i `显示将要删除的文件
    `-d` 递归删除目录及文件（未跟踪的）
    `-q` 仅显示错误，成功删除的文件不显示

`git reset` 删除的是已跟踪的文件，将已commit的回退。
`git clean` 删除的是未跟踪的文件

`git clean -nxdf`（查看要删除的文件及目录，确认无误后再使用下面的命令进行删除） 

`git checkout . && git clean -xdf`



`git fetch`：下载远程仓库的所有变动，可以将远程仓库下载到一个临时分支，然后再根据需要进行合并操作，`git fetch`命令和`git merge`命令可以看作是之前讲的`git pull`命令的分解动作。

```shell
git fetch origin master:temp
git merge temp
```

`git push -f`：强制提交，完全以自己的提交为准，之前其他人的提交都会被覆盖，适用于pr被block之后重新提交，提交后不需要重新提pr.

`git rebase dev`：解决合并冲突。rebase之后如果有冲突，会进入临时变基分支，手动消除冲突之后在rebase

`git checkout branch`: 切换分支

`git checkout -b｜B branch`: 创建新分支并切换到该分支

`git checkout -- a.txt` ： 将文件迁出修改到上一次提交的内容

`git checkout commit_id -- a.txt` ： 将文件迁出修改到指定的提交历史中某次提交的内容

`git checkout branch -- a.txt `：将文件迁出修改到指定分支的该文件的内容

`git checkout -- *.txt`将根目录下所有指定后缀的文件都迁出

`git checkout -- *.txt`将根目录下所有指定目录的文件都迁出

`git diff`：常用于比较工作区和仓库、暂存区与仓库、两个分支之间有什么差别。

`git diff --cached`：查看有add但没有commit的改动

`git diff HEAD`:是上面两条命令的合并

`git stash`：将当前工作区和暂存区发生的变动放到一个临时的区域，让工作区变干净。这个命令适用于手头工作还没有提交，但是突然有一个更为紧急的任务（如线上bug需要修正）需要去处理的场景。

```shell
git stash ## 保存当前的工作进度，会把暂存区和工作区的改动保存起来，使用git stash sava ‘message’ 添加一些注释
git stash list ## 显示保存进度的列表，git stash可以执行多次
## 通过git stash pop命令恢复进度后，会删除当前进度。
git stash pop ## 恢复最新的进度到工作区，git默认会把工作区和暂存区的改动都恢复到工作区
git stash pop --index ## 恢复最新的进度到工作区和暂存区
git stash pop stash@{1} ## 恢复指定的进度到工作区，stash_id为通过git stash list命令得到的
git stash apply ##恢复最新的进度到工作区，除了不删除恢复的进度外，其他和git stash pop命令一样
git stash drop [stash_id] ## 删除一个存储的进度，如果不执行stash_id则默认保存最新的存储进度
git stash clear ## 删除所有存储进度
```



`git cherry-pick`：挑选某个分支的提交并作为一个新的提交引入到你当前分支上。

默认cherry-pick只挑选单次的commit，如果想转移多个commit，使用命令git cherry-pick commitid1...commitid100

...命令默认不包含第一个commit id，如果你想包含第一个commit，也就是闭区间，使用git cherry-pick A^...B

Cherry-pick的过程中如果有冲突，需要先修改冲突文件，再git add .，然后继续执行git cherry-pick --continue

在任何阶段都可以执行`git cherry-pick --abort`放弃本次cherry-pick

`git rebase`：分支变基，多用于合并commit和重新合并master分支的代码

如果一次开发提交过多commit，会有很多弊端：

1.不利于代码review：如果做一个很小的功能有很多commit，会很多。

2.会造成分支污染：如果项目充满了无用的commit，有一天项目出现紧急问题需要回滚代码，却发现海量commit，会很崩溃

合并最近四次commit

```shell
git rebase -i HEAD~4
```

rebase之后进入vim模式，把不需要的commit前面的pick改为squash就可以

合并其他分支

每次开发完都要先在master分支下拉取别的同事的远程代码，然后当前分支对master分支进行合并才不会冲突

具体操作

```shell
git:(feature1):git rebase master
```

执行命令后：

首先git会把feature1分支里面的每个commit取消掉

然后把上面的操作临时保存成一个patch文件，存在`.git/rebase`目录下

然后把feature1分支更新到最新的master分支下

最后把上面保存的patch文件应用到feature1分支上

出现冲突时需要先解决冲突，然后执行命令

```shell
git add .
git rebase --continue
```

在任何时候都可以随时取消rebase操作

```shell
git rebase --abort
```

`git alias`可以配置命令的别名，简化命令

```shell
git config --global alias.co checkout
git config --global alias.ci commit
git config --global alias.br branch

git ci -m "commit message"
```

查看文件修改历史

```shell
git log --follow -p 想要查看的文件
```



## worktree

在大型软件开发过程中可能经常需要维护一个古老的分支，比如三年前的分支，当然 git 允许你每个分支维护一个版本，但是切换 branch 的成本太高，尤其是当代码变动很大的时候，有可能改变了项目结构，甚至可能变更了 build system，如果切换 branch，IDE 可能需要花费大量的时间来重新索引和设置。

但是通过 worktree, 可以避免频繁的切换分支，将老的分支 checkout 到单独的文件夹中作为 worktree，每一个分支都可以有一个独立的 IDE 工程。当然像过去一样你也可以在磁盘上 clone 这个 repo 很多次，但这意味着很多硬盘空间的浪费，甚至需要在不同的仓库中拉取相同的变更很多次。

回到原来的问题，使用 git worktree 确实能够解决最上面提及的问题。

git worktree 的命令只有几行非常容易记住

```shell
git worktree add ../new-dir some-existing-branch
git worktree add [path] [branch]
```

这行命令将在 new-dir 目录中将 some-existing-branch 中的内容 check out 出来，就像在该目录中 clone 了一份新代码一样。新的文件地址可以在文件系统中的任何位置，但是注意千万不要将目录放到主仓库中。在此之后新目录中的内容就可以和主仓库中的内容一样，新建分支，push 到远端。

当工作结束后可以直接删除该目录，然后运行 `git worktree prune`.

git worktree 非常适合大型项目又需要维护多个分支，想要避免来回切换的情况，这里总结一些优点：

- git worktree 可以快速进行并行开发，同一个项目多个分支同时并行演进
- git worktree 的提交可以在同一个项目中共享
- git worktree 和单独 clone 项目相比，节省了硬盘空间，又因为 git worktree 使用 hard link 实现，要远远快于 clone

## pr与mr

合并代码的操作在github中叫pr，在gitlab中成为mr，本质上都是合并代码

GitHub pr

强制push之后pr不能重开



## Git Alias

开启zsh git plugin之后，会获得一群好用的git alias



## Gitflow

进入本地文件夹，打开Git bash,

执行指令进行初始化，会在原始文件夹中生成一个隐藏的文件夹.git

```shell
rm -rf .git//删掉原来的.git目录
$ git init
```

将文件添加到本地仓库,运行命令：

```shell
$ git add . 
```

输入本次提交说明

```shell
$ git commit -m "layout"
```

将本地仓库与远程仓库相关联，

```shell
$ git remote add origin https://github.com/CongliYin/CSS.git
```

如果出现错误：fatal: remote origin already exists，则执行以下语句：

```shell
$ git remote rm origin
```

执行上传命令

```shell
git push origin master
```

新建远程仓库需要添加-u参数

```shell
git push -u origin master
```

如果出现错误failed to push som refs to…….，则执行以下语句，先把远程服务器github上面的文件拉先来，再push 上去。：

```shell
$ git pull origin master
```

如果出现错误fatal: refusing to merge unrelated histories，后面加上--allow-unrelated-histories

```shell
git pull origin master --allow-unrelated-histories
```

特别注意：执行命令后，git会弹出一个GitHub登陆的小界面，你登录成功后要求你输入用户名和密码。这里的密码并不是你的GitHub的密码或者本地git的密码。**而是GitHub的Personal access tokens**

https://github.com/settings/tokens

### 错误

GitHub pull之后有冲突



尚未完成合并(MERGE_HEAD存在)？

```shell
rm -rf .git/MERGE*
```

或者

```shell
git merge --quit
```



## 更新不了代码

代码加入本地仓库后，上传后显示everything -up-date，但是远程仓库没有更新

先创建新分支

```shell
git branch newbranch
```

检查分支创建是否成功

```shell
git branch
```

此时输出

```shell
* master
  newbranch
```

切换到新创建的分支

```shell
git checkout newbranch
```

将改动提交到新分支

```shell
git add .
git commit -a
```

回到主分支

```shell
git checkout master
```



将新分支与原分支合并

```shell
git merge newbranch 
```

正常合并没有冲突，如果产生冲突，查看冲突文件修改后再一次提交

```git
git diff
```

解决后就正常提交

```git
git push -u origin master
```

删除分支

```shell
git branch -d newbranch
```



## 检查版本信息

查看远程仓库信息

```shell
git remote -v
```



```shell
git status
```

检查文件或者文件夹在工作区或暂存区的状态，有三种

文件已经从工作区add到暂存区，git restore --staged filename

文件在工作区、暂存区都有，并且在工作区进行了修改或删除，没有add到暂存区

git add file

文件只在工作区

```shell
git checkout -- <file>
```

拉取暂存区文件为工作区文件

```shell
git log
```

git log 会按提交时间列出所有的更新，最近的更新排在最上面

```shell
git open
```

在git目录输入git open就能打开github对于的页面

```shell
npm install -g git-open
```

将本地仓库文件撤回至工作区

```shell
git reset --hard
git reset --mixed
```



```shell
git revert HEAD
```



```shell
git fetch origin
```

创建并更新远程分支，并拉取代码到origin，一般默认是master

git pull可以认为是git fetch和git merge的组合体

```shell
git rebase origin/master
```



```shell
git diff
```

git-diff能在命令行显示当前代码与上次提交时代码的修改，可以逐行见检查代码

## 代码检查

### Git Hooks

Git 的 hooks 允许用户在特定的时机执行用户自定义的脚本。

比如非常常见的，在提交之后自动对代码内容进行一些常规检查，如果失败则不允许提交等等。常用的 hook 有 `pre-commit`, `commit-msg`，`pre-push` 等。Git hooks 是基于事件的，Scott Chacon 在 Pro Git 书中将 hooks 分成几个类型：

- 客户端 hook，在使用者自己的本地环境中被调用。
  - 代码提交相关的 hook，在提交动作前后，通常用于检查完整性，生成提交信息，校验，发出通知等等
  - Email 相关的 hook，主要用于 Email 提交的代码。像 Linux 内核使用 Email 提交补丁会使用到。工作方式和提交类 hook 相似
  - 其他类，包括代码合并，check-out，rebase，rewrite，clean 等等
- 服务端 hook，一般在服务器端执行，用于接受推送，部署在 git 仓库的服务器上
  - 触发类，在服务器接收到一个推送之前或之后执行动作，前触发用于检查，后触发用于部署
  - 更新，类似前触发，更新的 hook 是以分支作为作用对象，在分支更新通过之前执行

| hook 名称            | 触发命令                             | 描述                                                         | 参数个数描述             |
| :------------------- | :----------------------------------- | :----------------------------------------------------------- | :----------------------- |
| `applypatch-msg`     | `git am`                             | 编辑 commit 时提交的 message，通常用于验证或纠正提交的信息以符合项目标准 | 包含预备提交信息的文件名 |
| `pre-applypath`      | `git am`                             | 变更 commit 之前，如果以非 0 退出，会导致 uncommit 状态，用于 commit 之前的检查 |                          |
| `pre-applypath`      | `git am`                             | commit 完成提交之后，主要用于通知                            |                          |
| `pre-commit`         | `git commit`                         | 获取 commit message 之前，非 0 退出会取消本次 commit，检查 commit 自身，而不是 commit message |                          |
| `prepare-commit-msg` | `git commit`                         | 接收默认 commit message 之后，启动 commit message 编辑器之前。 |                          |
| `commit-msg`         | `git commit`                         | message 提交之后修改 message 的内容或退回不合格的 commit     |                          |
| `post-commit`        | `git commit`                         | commit 完成之后调用，主要用于通知                            |                          |
| `pre-rebase`         | `git rebase`                         | 执行 rebase 时，可用于中断不想要的 rebase                    |                          |
| `post-checkout`      | `git checkout` 和 `git clone`        | 更新工作树后调用 checkout 时，或执行 git clone 后，主要用于验证环境、显示变更、配置环境 |                          |
| `post-merge`         | `git merge` or `git pull`            | 合并之后调用                                                 |                          |
| `pre-push`           | `git push`                           | 推送远程之前                                                 |                          |
| `pre-receive`        | 远程 repo 进行 `git-receive-pack`    | 远程 repo 更新刚被 push 的 ref 之前调用，非 0 会中断本次     |                          |
| `update`             | 远程 repo 进行 `git-receive-pack`    | 远程 repo 每一次 ref 被 push 的时候调用                      |                          |
| `post-receive`       | 远程 repo 进行 `git-receive-pack`    | 远程 repo 所有的 ref 更新之后                                |                          |
| `post-update`        | `git-receive-pack`                   | 所有 ref 被 push 后执行一次                                  |                          |
| `pre-auto-gc`        | `git gc --auto`                      | 用于在自动清理 repo 之前做一些检查                           |                          |
| `post-rewrite`       | `git commit --amend` 或 `git rebase` | git 命令重写 rewrite 已经被 commit 的数据时调用              |                          |

### husky

Husky 的原理就是在项目的根目录中使用一个配置文件，然后在安装 Husky 的时候把配置文件和 Git hook 关联起来，在团队之间共享。使用husky

安装

```shell
npm install husky -D
```

编辑package.json  prepare 脚本并且运行

```shell
npm set-script prepare "husky install" 
npm run prepare
```

添加钩子函数

```shell
npx husky add .husky/pre-commit "npm test"
git add ./husky/pre-commit
```

然后提交commit就会检查

如果不想检查使用no-verify

```shell
git commit -m '' --no-verify
## git commit -n -m ''
```

如果husky报错，可以使用husky-init

```shell
npx husky-init
```

Husky 支持如下几种格式配置：

- .huskyrc
- .huskyrc.json
- .huskyrc.yaml
- .huskyrc.yml
- .huskyrc.js
- husky.config.js

以 `.huskyrc` 为例

```rc
{
  "hooks": {
    "pre-commit": "git restore -W -S dist examples/dist && eslint ."
  }
}
```

Husky 不支持服务端 hooks。

包括 `pre-receive`、`update`、`post-receive`

### commitizen

[commitizen/cz-cli](https://link.juejin.cn/?target=https%3A%2F%2Fgithub.com%2Fcommitizen%2Fcz-cli)是一个可以实现规范的**提交说明**的工具

提供选择的提交信息类别，快速生成**提交说明**。安装cz工具

```shell
npm install -g commitizen
```

如果需要在项目中使用**commitizen**生成符合AngularJS规范的**提交说明**，初始化**cz-conventional-changelog**适配器

```shell
commitizen init cz-conventional-changelog --save --save-exact
```

初始化命令主要进行了3件事情

1. 在项目中安装**cz-conventional-changelog** 适配器依赖
2. 将适配器依赖保存到`package.json`的`devDependencies`字段信息
3. 在`package.json`中新增`config.commitizen`字段信息，主要用于配置cz工具的适配器路径：

```json
"devDependencies": {
 "cz-conventional-changelog": "^2.1.0"
},
"config": {
  "commitizen": {
    "path": "./node_modules/cz-conventional-changelog"
  }
}
```

如果想定制项目的**提交说明**，可以使用[cz-customizable](https://link.juejin.cn/?target=https%3A%2F%2Fgithub.com%2Fleonardoanalista%2Fcz-customizable)适配器

```shell
npm install cz-customizable --save-dev
```

将之前符合Angular规范的**cz-conventional-changelog**适配器路径改成**cz-customizable**适配器路径

```json
"devDependencies": {
  "cz-customizable": "^5.3.0"
},
"config": {
  "commitizen": {
    "path": "node_modules/cz-customizable"
  }
}
```

官方提供了一个`.cz-config.js`示例文件[cz-config-EXAMPLE.js](https://link.juejin.cn/?target=https%3A%2F%2Fgithub.com%2Fleonardoanalista%2Fcz-customizable%2Fblob%2Fmaster%2Fcz-config-EXAMPLE.js)

```javascript
'use strict';

module.exports = {

  types: [
    {value: '特性',     name: '特性:    一个新的特性'},
    {value: '修复',      name: '修复:    修复一个Bug'},
    {value: '文档',     name: '文档:    变更的只有文档'},
    {value: '格式',    name: '格式:    空格, 分号等格式修复'},
    {value: '重构', name: '重构:    代码重构，注意和特性、修复区分开'},
    {value: '性能',     name: '性能:    提升性能'},
    {value: '测试',     name: '测试:    添加一个测试'},
    {value: '工具',    name: '工具:    开发工具变动(构建、脚手架工具等)'},
    {value: '回滚',   name: '回滚:    代码回退'}
  ],

  scopes: [
    {name: '模块1'},
    {name: '模块2'},
    {name: '模块3'},
    {name: '模块4'}
  ],

  // it needs to match the value for field type. Eg.: 'fix'
  /*
  scopeOverrides: {
    fix: [
      {name: 'merge'},
      {name: 'style'},
      {name: 'e2eTest'},
      {name: 'unitTest'}
    ]
  },
  */
  // override the messages, defaults are as follows
  messages: {
    type: '选择一种你的提交类型:',
    scope: '选择一个scope (可选):',
    // used if allowCustomScopes is true
    customScope: 'Denote the SCOPE of this change:',
    subject: '短说明:\n',
    body: '长说明，使用"|"换行(可选)：\n',
    breaking: '非兼容性说明 (可选):\n',
    footer: '关联关闭的issue，例如：#31, #34(可选):\n',
    confirmCommit: '确定提交说明?'
  },

  allowCustomScopes: true,
  allowBreakingChanges: ['特性', '修复'],

  // limit subject length
  subjectLimit: 100

};
```

### commitLint

校验提交说明是否符合规范，安装校验工具[commitlint](https://link.juejin.cn/?target=https%3A%2F%2Fgithub.com%2Fmarionebl%2Fcommitlint)：

```shell
npm install --save-dev @commitlint/cli
```

安装符合Angular风格的校验规则

```shell
npm install --save-dev @commitlint/config-conventional 
```

在项目中新建`commitlint.config.js`文件并设置校验规则

```javascript
module.exports = {
  extends: ['@commitlint/config-conventional']
};
```

如果是使用**cz-customizable**适配器做了破坏Angular风格的提交说明配置，那么不能使用**@commitlint/config-conventional**规则进行提交说明校验，可以使用[commitlint-config-cz](https://link.juejin.cn?target=https%3A%2F%2Fgithub.com%2Fwhizark%2Fcommitlint-config-cz)对定制化提交说明进行校验

```shell
npm install commitlint-config-cz --save-dev
```

然后加入commitlint校验规则配置：

```javascript
module.exports = {
  extends: [
    'cz'
  ]
};
```

Validate-commit-msg

除了使用**commitlint**校验工具，也可以使用[validate-commit-msg](https://link.juejin.cn/?target=https%3A%2F%2Fgithub.com%2FFrikki%2Fvalidate-commit-message)校验工具对cz提交说明是否符合Angular规范进行校验。

commitizen日志

如果使用了[cz](https://link.juejin.cn/?target=https%3A%2F%2Fgithub.com%2Fcommitizen%2Fcz-cli)工具集，配套[conventional-changelog](https://link.juejin.cn/?target=https%3A%2F%2Fgithub.com%2Fconventional-changelog%2Fconventional-changelog%2Ftree%2Fmaster%2Fpackages%2Fconventional-changelog)可以快速生成开发日志

```shell
npm install conventional-changelog -D
```

在`pacage.json`中加入生成日志命令

```json
"version": "conventional-changelog -p angular -i CHANGELOG.md -s -r 0 && git add CHANGELOG.md"
```

执行`npm run version`后可查看生产的日志[CHANGELOG.md](https://link.juejin.cn/?target=https%3A%2F%2Fgithub.com%2Fziyi2%2Fcz-example%2Fblob%2Fmaster%2FCHANGELOG.md)。

## 查看commit时间分布

在项目目录下执行

```shell
curl -fsSL https://fastly.jsdelivr.net/gh/hellodigua/code996/bin/code996.sh | bash
```

就能查看项目