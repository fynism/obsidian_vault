一直以来，总是听说 Git 这个东西，也知道它是能够进行版本控制的一个软件。我是自比游戏存档的机制来理解它的，但是在使用中，我发现 Git 与游戏存档的机制又相去甚远。各种指令 `pull`、`push`、`fork`... 让我眼花缭乱。在我的以就业为核心导向的学习过程中，我想要也必须更加深入、系统地了解 Git。于是便有了此文，以作学习笔记兼查找时可用的工具书。

# （一）、 起源
我超，原来 Git 是 linux 的创始人嫌当时的版本管理系统(SVN)不好用，直接手搓的吗。。**好几把吊**。。

# （二）、创建仓库(Repository)
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

