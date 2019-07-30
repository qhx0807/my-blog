---
comments: false
---

#### VScode扩展：Staticfile CDN

> repo: `https://github.com/qhx0807/vscode-staticfile-cdn`

查找获取js库的Staticfile CDN链接. https://marketplace.visualstudio.com/items?itemName=qhx0807.staticfile-cdn

![](http://cdn.cqyyy.cn/pic/20190722113926.gif)

#### 项目文件目录结构生成命令行工具

> repo: `https://www.npmjs.com/package/file-tree-generator`

以树形结构显示文件目录结构，它非常适合给别人介绍文件目录的组成框架。默认忽略 `node_modules` 和 `.git`

#### 获取Bing每日壁纸

> repo: `https://github.com/qhx0807/bing`

每日自动抓取网站 `https://www.prohui.com/` 更新的必应壁纸，存入Mysql数据库. 使用pm2部署. 链接：http://lea.ioleo.cn/

1. `/bing/daily`, 获取每日图片信息，每天自动执行
2. `/bing/getbydate?d=xx`, 获取指定日期的数据
2. `/bing/bingimgs?p=1`, 分页查询，每页10条

技术栈： `thinkjs`, `cheerio`, `pug`, `mysql`


#### 每日Bing Electron客户端

> repo: `https://github.com/qhx0807/electron-wallpaper`

![](http://cdn.cqyyy.cn/pic/20190528163751.png)

必应壁纸客户端，左右切换日期，实现下载，换背景，浏览器打开图片等功能。

技术栈： `electron`

#### excel2json

> repo: `https://github.com/qhx0807/excel2json`

`Excel` 和 `json` 相互装换，方便的小工具。使用 `React`, 在线链接：http://e2j.ioleo.cn/

![](http://cdn.cqyyy.cn/pic/20190528164923.png)

#### 七牛api

> repo: `https://github.com/qhx0807/qiniu-manager`

七牛云资源管理 Api, 根据官方文档整理.

技术栈： `thinkjs`, `mysql`