一直以来，总是听说 Git 这个东西，也知道它是能够进行版本控制的一个软件。我是自比游戏存档的机制来理解它的，但是在使用中，我发现 Git 与游戏存档的机制又相去甚远。各种指令 `pull`、`push`、`fork`... 让我眼花缭乱。在我的以就业为核心导向的学习过程中，我想要也必须更加深入、系统地了解 Git。于是便有了此文，以作学习笔记兼查找时可用的工具书。

# 1. 起源
我超，原来 Git 是 linux 的创始人嫌当时的版本管理系统(SVN)不好用，直接手搓的吗。。**好几把吊**。。

# 2. 创建仓库(Repository)
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

> []