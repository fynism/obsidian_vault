一直以来，总是听说 Git 这个东西，也知道它是能够进行版本控制的一个软件。我是自比游戏存档的机制来理解它的，但是在使用中，我发现 Git 与游戏存档的机制又相去甚远。各种指令 `pull`、`push`、`fork`... 让我眼花缭乱。在我的以就业为核心导向的学习过程中，我想要也必须更加深入、系统地了解 Git。于是便有了此文，以作学习笔记兼查找时可用的工具书。

# （一）、 起源
我超，原来 Git 是 linux 的创始人嫌当时的版本管理系统(SVN)不好用，直接手搓的吗。。**好几把吊**。。

# （二）、创建仓库 repo
在此步骤中，开始进行一些基本的git 操作。可用命令如下：
`git init`, `git add <filename>`, `git commit <filename> -m "commit notes"`

要使用 Git，首先便要创建一个**仓库(Repository)**。
1. 在合适路径下创建目录(以 linux 为例)：
```
$ mkdir learngit
$ cd learngit
$ pwd
/Users/michael/learngit
```
2. 使用 `git init` 初始化仓库
```
$ git init
Initialized empty Git repository in D:/fengye/GitTest/.git/
```

Git 仓库就建好了，并且出现了一个名为 `.git` 的目录。

如果我想要**添加**一个文件(``readme. txt``) 到仓库中，步骤如下：
1. 把 ``readme. txt`` 放到仓库目录下。
2. 用 `` git add <file>`` 命令把文件添加到仓库。
```
$ git add readme.txt
```
> [!notice] 按照廖雪峰老师的话说：如果没有任何消息，那就对了。Unix 的哲学是“没有消息就是好消息”。

3. 用 `git commit -m <message>` 命令把这一次的改动提交。
```
$ git commit -m "init readme file"
[master (root-commit) ee77b91] init readme file
 1 file changed, 1 insertion(+)
 create mode 100644 readme.txt
```
其中，``-m`` 后面双引号中的部分是这次提交的**说明**。每一次 `commit` 都要加上，对自己和别人理解都很重要。

> [!question] 为什么 `add` 和 `commit` 要分开写嘞？

> [!notice] 因为每次 `commit` 能够提交多个文件，所以我可以每次 `add` 多个不同的文件。
```plain
$ git add file1.txt
$ git add file2.txt file3.txt
$ git commit -m "add 3 files."
```

# （三）、 基本操作
## 1.版本回退
每次使用 `commit` 就相当于在版本库中保存一个快照，一旦发生什么错误便能够回退到指定 `commit` 的 版本。可用命令如下：
`git reset...`, `git log`, `git reflog`

**版本回退的步骤如下：**
1. 使用 `git log` 能够看到所有的 `commit` 记录。使用 `git log --pretty=one-line` 能够让它们显示在一行内。
```
$ git log
commit 4866b80a33b4d6a983c1097919759cd9f029c3a2 (HEAD -> master)
Author: fynism <fengye0914@foxmail.com>
Date:   Wed Feb 4 23:26:09 2026 +0800

    add you

commit ee77b9174855f82b820f844d596c92345b0b4b6b
Author: fynism <fengye0914@foxmail.com>
Date:   Wed Feb 4 21:33:48 2026 +0800

    init readme file
```
从上述内容中能够看到，我的 git 仓库存在**两个** `commit`，分别为"init readme file" 和 "add you"。

2. 使用 `git reset` 回退版本。
其中，`git reset` 有三种回退到指定版本的写法：
- `git reset HEAD^`：表示回到**上一个**版本。回到多少个版本之前就在 `HEAD` 后面加多少个 `^`。
- `git reset HEAD~100`: 表示回到 100 个版本前。本质上是第一种写法的**变体/简化**。
- `git reset --hard ee77b`: 表示回到提交版本 id 为 `ee77b……` 的版本，能够回退/前进到**指定版本**，较为常用。其中版本 id 不用写全，但是也不能写太少，因为有可能会重复。

> [!notice] `--hard` 参数有啥意义？`--hard` 会回退到上个版本的已提交状态，而 `--soft` 会回退到上个版本的未提交状态，`--mixed` 会回退到上个版本已添加但未提交的状态（？存疑）。


> [!question] 如果回到了之前的版本之后发现无法回到原来的版本怎么办？

没关系，可以使用 `git reflog` 来查看**历史命令记录**。
```
$ git reflog
4866b80 (HEAD -> master) HEAD@{0}: reset: moving to 4866b
ee77b91 HEAD@{1}: reset: moving to ee77b
4866b80 (HEAD -> master) HEAD@{2}: reset: moving to 4866b
ee77b91 HEAD@{3}: reset: moving to HEAD^
4866b80 (HEAD -> master) HEAD@{4}: commit: add you
ee77b91 HEAD@{5}: commit (initial): init readme file
```
这样，根据历史命令中**前面**的版本号就能够知道原来的版本号是啥了。
***
## 2.Git 主要区域的概念以及修改管理

在 git 的版本管理中，存在着几种种概念: 工作区(Working Directory、暂存区(Stage)、版本库(Repo) 等。
- **工作区**是在电脑中能够直接看到的文件夹中的内容。
- **暂存区**是指将工作区中的文件进行 `git add` 操作后文件进入的区域。暂存区包含在版本库中。
- 文件在暂存区后执行 `git commit` 操作后就保存到了**分支**。

![repo.png (474×250)](https://liaoxuefeng.com/books/git/time-travel/working-stage/repo.png)

***
值得注意的是，git 是以**修改**来进行版本管理的系统，而非以文件的形式。

可以使用 `git status` 来查看 git 现在的状态(能够看到各种修改)。
1. 工作区与暂存区中都没有修改发生：
![](https://cdn.jsdelivr.net/gh/fynism/Picogo@main/img/20260209013921898.png)
2. 文件在工作区中被修改，但是没有进行 `git add`，即没有进入暂存区：
![](https://cdn.jsdelivr.net/gh/fynism/Picogo@main/img/Pasted%20image%2020260209014234.png)
3. 文件被 add 到暂存区，但是还没有 commit 到分支。
![](https://cdn.jsdelivr.net/gh/fynism/Picogo@main/img/20260209014857192.png)
***

## 3. 撤销修改
如果你想要撤销文件的修改，那么可以使用以下命令：
`git checkout -- <filename>`, `git reset HEAD <filename>`
1. 撤销**工作区**里的修改。
	可使用 `git checkout -- <filename>` 命令。此命令可以使工作区中的文件回到最近一次 `add` 或者 `commit` 之后的状态，即上一次**没有修改发生**的状态。
> [!notice] 注意区分 `git checkout -- <filename>` 和 `git checkout <filename>`。尽管他俩只差了 `--`，但是表示的意义完全不同，后者表示的是分支中的操作。
2. 撤销**暂存区**里的修改。
	可使用 `git reset HEAD <filename>` 命令。此命令可以使**暂存区**中的修改回退到**工作区**中的修改。即相当于撤回所有已经 `add` 的修改。

# （四）远程仓库

使用 `git push` 和 `git pull` 命令能够进行本地仓库与远程仓库的同步。
在推送本地 git 仓库到**远程空仓库**的时候使用 `git remote add URL` 来进行本地仓库与远程仓库的绑定。
当然，如果是使用 GitHub 上的**现有仓库**从零开发，使用 `git clone URL` 克隆到本地是最好的方法。

**创建并连接一个远程仓库：**
> （连接远程仓库似乎有好几种方式，这里以在本地新建一个 git 仓库为例。）
1. 首先在 GitHub 中新建一个 repo。
2. 在本地新建一个 git 仓库与之连接。先用 `git init` 初始化本地 git 仓库，并且使用 ` git remote add origin https://github.com/fynism/learngit.git` 使本地仓库连接到远程仓库。
3. 进行本地的**初次提交**。`git add .`, `git commit -m "first commit"`
![](https://cdn.jsdelivr.net/gh/fynism/Picogo@main/img/20260220230205792.png)
4. 使用 `git push -u origin main` 将**本地**提交推送到**远程**仓库。就能够在 GitHub 中看到推送的内容。
	（第一次 push 需要加上 `-u` 参数，将本地的 main 分支和远程的 main 分支绑定起来。之后就可以直接写 `git push` 和 `git pull`）
	![](https://cdn.jsdelivr.net/gh/fynism/Picogo@main/img/20260220230620047.png)
5. 在之后，想要将本地修改同步到远程仓库，只需要在本地进行**提交**之后，执行 `git push`。同理想从远程仓库同步到本地，执行 `git pull`。
	![](https://cdn.jsdelivr.net/gh/fynism/Picogo@main/img/20260220231133357.png)
	![](https://cdn.jsdelivr.net/gh/fynism/Picogo@main/img/20260220231201040.png)

# （五）分支管理
分支相当于一个**平行时空**，在一个分支上进行的修改不会影响其他分支。
## 1. 创建与合并
- 使用 `git switch -c <name>` 表示**创建**并转到相应分支。如果不用新建，那么不用加 ` -c ` 参数。
（老版本也使用 `git checkout <name>` 来表示分支的转换，但是容易和之前的撤销修改部分混淆，`switch` 是 git 新版本更加符合直觉的操作方法。）
- 使用 `git merge <name>` 来把相应分支上的内容合并到**现有**分支。
- 使用 `git branch` 来**查看**现有分支情况。前面带有 `*` 的是**当前所在**的分支。
- 使用 `git branch -d <name>` 来**删除**相应的已经合并入 `main` 分支、不再需要的分支。

**创建一个分支，在其上进行开发并将其合并到 `main` 分支：**
1. 使用 `git switch -c dev` **创建**并转到 `dev` 分支。
	![](https://cdn.jsdelivr.net/gh/fynism/Picogo@main/img/20260220234913798.png)
2. 在该分支上进行开发并 `commit`。
3. 转回 `main` 分支，将 `dev` 分支的内容与之合并。`git switch main`, `git merge dev`
	![](https://cdn.jsdelivr.net/gh/fynism/Picogo@main/img/20260220235202967.png)
4. 如果不再需要此分支，那么直接删除。`git branch -d dev`
	![](https://cdn.jsdelivr.net/gh/fynism/Picogo@main/img/20260220235458150.png)

***

## 2.合并冲突
在合并两个分支的时候，如果两个文件进行了不同的修改，就会出现**冲突**。这个笔记就出现过好几次这样的情况。

**例：**
1. 我新建了一个 `feature` 分支。在这个分支中，我在 `README.md ` 的最后一行加了一句 ` this is from feature1. ` 并且将其 `commit`。
	
2. 我又回到了 `main` 分支。在这个分支中，我在 `README.md ` 的最后一行加了一句 ` this is from main branch. `，并且将其 `commit`。
	
3. 当我尝试合并两个分支 `git merge feature1` 时，出现了错误：
	![](https://cdn.jsdelivr.net/gh/fynism/Picogo@main/img/20260221004049791.png)
	而 `README.md` 中最后一行内容变成了：
	![](https://cdn.jsdelivr.net/gh/fynism/Picogo@main/img/20260221004420034.png)
	用 `<<<`, `>>>`, `===` 详细标出了两分支冲突的部分。
	
4. 怎么**解决**呢？这个时候要手动**保留**想要修改的部分，去掉 `<<<`, `>>>`, `===` 之后，进行一个**新的提交**。
	![](https://cdn.jsdelivr.net/gh/fynism/Picogo@main/img/20260221004943451.png)
	
	![](https://cdn.jsdelivr.net/gh/fynism/Picogo@main/img/20260221004857086.png)
	
	![](https://cdn.jsdelivr.net/gh/fynism/Picogo@main/img/20260221005443140.png)
5. 最后，删除 `feature1` 分支。`git branch -d feature1`。冲突解决完毕。

## 3. 分支管理策略
这一部分，廖雪峰和 Gemini 给了我相似但细节处有些不相同的知识点。

