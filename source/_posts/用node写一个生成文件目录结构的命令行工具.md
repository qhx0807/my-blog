---
title: 用node写一个生成文件目录结构的命令行工具
date: 2019-06-26 12:28:11
categories: javascript
---

以树形结构显示文件目录结构，生成txt文件，非常适合介绍项目文件目录的组成框架。[Github](https://github.com/qhx0807/file-tree-generator)

![](http://cdn.cqyyy.cn/pic/20190711160048.png)


#### 命令行工具

`#!/usr/bin/env node`

文件的头部务必加入 `#!/usr/bin/env node` 这行代码，这里表示使用node作为脚本的解释程序，node的路径通过env来查找。新建一个 index.js 文件，
想在命令行工具中输入 `filetree` 就执行 index.js.

在 `package.json` 中添加：

```json
"bin": {
  "filetree": "index.js"
}
```
#### npm link

在本地开发npm模块的时候，我们可以使用npm link命令，将npm 模块链接到对应的运行项目中去，方便地对模块进行调试和测试。
在项目中执行：

```bash
$ npm link
```

#### 编写代码

用到的node模块
```javascript
const fs = require('fs')
const { resolve } = require('path')
```

递归获取文件目录信息，第一个参数：目录路径，第二个参数：递归的深度
默认忽略 `node_modules` 和 `.git` 目录。
```javascript
const fileMap = (dir, deep) => {
  if (dir.indexOf('node_modules') > -1 || dir.indexOf('.git') > -1) return false
  let files = fs.readdirSync(dir)
  let maps = []
  files.forEach(file => {
    const filepath = resolve(dir, file)
    const isdir = fs.statSync(filepath).isDirectory()
    const obj = {
      name: file,
      isdir: isdir,
      deep: deep,
      children: []
    }
    if (isdir) {
      let d = deep + 1
      obj.children = fileMap(filepath, d)
    }
    maps.push(obj)
  })
  return maps
}
```

process.cwd()主要是进程的当前目录
```javascript
const filestack = fileMap(process.cwd(), 0)
```
拼凑字符串然后输出txt文件
```javascript
const fileOutPutLog = (arr) => {
  let str = ''
  arr.forEach((item, index) => {
    let i = 0
    while (i < item.deep) {
      str += charSet.pipe
      i++
    }
    str += (index === arr.length - 1 && item.children.length === 0) ? charSet.last : charSet.node
    str += item.isdir ? ` /${item.name}/\r\n` : ` ${item.name}\r\n`
    if (item.children.length > 0) {
      str += fileOutPutLog(item.children)
    }
  })
  return str
}
const contents = fileOutPutLog(filestack)
fs.writeFile(process.cwd() + '/filetree.txt', contents, function (err) {
  if (err) {
    return console.log(err)
  }
  console.log('-------目录树文件filetree.txt已创建-------')
})
```

#### 使用

在想生成文件结构的目录中：

```bash
$ filetree

# 出现
# -------目录树文件filetree.txt已创建-------
```