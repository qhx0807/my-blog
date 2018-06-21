---
title: svn自动更新，commit触发update
date: 2018-06-20 11:24:52
tags: [svn]
categories: 服务器
---

1. 找到SVN Server中的仓库（Repositories）文件夹的位置，在相应的项目文件夹中找到hooks文件夹。

2. 在该文件夹中添加一个post-commit.bat文件；当有commit动作发生时（提交到SVN Server时）就会执行这个post-commit.bat文件。

3. post-commit.bat里的内容如下所示：
  
  ```bash
    "C:\Program Files\VisualSVN Server\bin\svn.exe" 
    update "C:\Hongfan2018" 
    --quiet --username "用户名" --password "密码"
  ```
  svn 安装地址 `update` 项目部署地址  `--quiet --username` 用户名 `--password` 密码

4. `win+R` 运行 `services.msc` 找到 `visual svn server` 服务，右键属性 -> 登陆面板 -> 设置桌面交互或者管理员权限。

5. 提交即可自动更新。