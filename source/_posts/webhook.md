---
title: webhook 自动部署项目
date: 2018-01-5 11:18:45
tags: [webhook, git, github]
categories: [git]
---

实现：服务器自动拉取push到github上的项目。
github支持Webhooks及大量的第三方服务，可以很好得对repo的push等操作做出反应。
当github收到repo的操作行为时，会向指定的url发送一个带有描述操作内容的post请求。

#### 1. 对指定repo添加webhook，指向服务器上的接口。

![webhook](webhook.png)

- Payload URL: 服务器地址，接收POST请求
- Content type： 默认
- Secret： 自定义加密字符串

#### 2. 服务器上编写代码接收请求

全局安装 `github-webhook-handler`

```bash
npm install -g github-webhook-handler
```
创建 `deploy.js`

```javascript
var http = require('http')
var createHandler = require('github-webhook-handler')
var handler = createHandler({ path: '**服务器上项目文件目录**', secret: '**Github上设置的Secret**' }) 

function run_cmd(cmd, args, callback) {
  var spawn = require('child_process').spawn;
  var child = spawn(cmd, args);
  var resp = "";

  child.stdout.on('data', function(buffer) { resp += buffer.toString(); });
  child.stdout.on('end', function() { callback (resp) });
}
http.createServer(function (req, res) {
  handler(req, res, function (err) {
    res.statusCode = 404
    res.end('no such location')
  })
}).listen(7777)

handler.on('error', function (err) {
  console.error('Error:', err.message)
})

handler.on('push', function (event) {
  console.log('Received a push event for %s to %s',
    event.payload.repository.name,
    event.payload.ref);
  run_cmd('sh', ['./deploy.sh'], function(text){ console.log(text) });
})

```
创建 `deploy.sh`

```bash
#!/bin/bash

LOG_FILE="./blog_deploy.log"

date >> "$LOG_FILE"
echo "Start deployment" >>"$LOG_FILE"
cd ../blog
echo "pulling source code..." >> "$LOG_FILE"
git checkout gh-pages
git pull origin gh-pages
echo "Finished." >>"$LOG_FILE"
echo >> $LOG_FILE
```

#### 把服务器跑起来

```bash
node deploy.js
```

