---
title: Git分支的常用操作
date: 2018-01-24 14:30:44
tags: [branch, git, github]
categories: [git]
---

常用的git分支操作

#### 远程仓库有master和dev分支

1. 克隆代码

```bash
git clone https://github.com/xxxxx.git  
```

2. 查看所有分支

```bash
git branch --all
# 默认有了dev和master分支，所以会看到如下三个分支
# master[本地主分支] origin/master[远程主分支] origin/dev[远程开发分支]
# 新克隆下来的代码默认master和origin/master是关联的，也就是他们的代码保持同步
# 但是origin/dev分支在本地没有任何的关联，所以我们无法在那里开发
```

3. 创建本地关联origin/dev的分支

```bash
git checkout -b dev origin/dev  
# 本地没有dev分支 要加参数 -b
```

4. 切换到dev分支进行开发

```bash
git checkout dev  # 这个是切换到dev分支，然后就是常规的开发
```

<!-- more -->

#### 远程仓库只有master分支

1. 克隆代码

```bash
git clone https://github.com/xxxxx.git  
```

2. 查看所有分支

```bash
git branch --all
# 默认只有master分支，所以会看到如下两个分支
# master[本地主分支] origin/master[远程主分支]
# 新克隆下来的代码默认master和origin/master是关联的，也就是他们的代码保持同步
```

3. 创建本地新的dev分支

```bash
git branch dev  # 创建本地分支
git branch  # 查看分支
# 这是会看到master和dev，而且master上会有一个星号
# 这个时候dev是一个本地分支，远程仓库不知道它的存在
# 本地分支可以不同步到远程仓库，我们可以在dev开发，然后merge到master，使用master同步代码，当然也可以同步
```

4. 同步dev分支的代码到远程服务器

```bash
git push origin dev:dev  # 这样远程仓库也有一个dev分支了
```

5. 在dev分支开发代码

```bash
it checkout dev  # 切换到dev分支进行开发
# 开发代码之后，有两个选择
# 第一个：如果功能开发完成了，可以合并主分支
git checkout master  # 切换到主分支
git merge dev  # 把dev分支的更改和master合并
git push  # 提交主分支代码远程
git checkout dev  # 切换到dev远程分支
git push  # 提交dev分支到远程
# 第二个：如果功能没有完成，可以直接推送
git push  # 提交到dev远程分支
# 注意：在分支切换之前最好先commit全部的改变，除非你真的知道自己在做什么
```

6. 删除分支

```bash
git push origin :dev  # 删除远程dev分支，危险命令哦
# 下面两条是删除本地分支
git checkout master  # 切换到master分支
git branch -d dev  # 删除本地dev分支
```