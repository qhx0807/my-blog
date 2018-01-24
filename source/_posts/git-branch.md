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