---
title: 使用Anyproxy实现代理功能
date: 2019-06-14 18:02:03
tags: [anyproxy]
categories: 代理
---

中文文档：https://github.com/alibaba/anyproxy/tree/master/docs-src/cn

#### 安装 `Anyproxy`

```bash
$ npm install -g anyproxy
```

也可以作为npm模块安装

```bash
$ npm install --save anyproxy
```

#### 启动

```bash
$ anyproxy
$ anyproxy -i // 代理https
```
- 启动后将终端http代理服务器配置为127.0.0.1:8001即可
- 访问http://127.0.0.1:8002 ，web界面上能看到所有的请求信息

#### 代理https

先下载 `RootCA.crt` 证书, 以火狐浏览器为例:

1. 设置代理

![](http://cdn.cqyyy.cn/pic/20190615211227.png)

2. 导入证书

![](http://cdn.cqyyy.cn/pic/20190615211418.png)

点击导入按钮，选择下载好的 `RootCA.crt`, 勾选 ‘信任此证书机构’ 等

3. 打开127.0.0.1:8002 就能看到代理获取的信息 baidu.com, https的能成功获取到内容：

![](http://cdn.cqyyy.cn/pic/20190615211736.png)

#### 编程式使用

```javascript
// index.js
const AnyProxy = require('anyproxy')

const options = {
  port: 8001, // 代理端口
  rule: require('./myRuleModule'),
  webInterface: {
    enable: true,
    webPort: 8002,
    wsPort: 8003
  },
  throttle: 10000,
  forceProxyHttps: true,
  silent: true
}
const proxyServer = new AnyProxy.ProxyServer(options)

proxyServer.on('ready', () => {
  console.log('satrt')
})
proxyServer.on('error', e => {
  console.log(e)
})
proxyServer.start()

// 关闭
// proxyServer.close()
```

```javascript
// myRuleModule.js
module.exports = {
  sumary: 'a rule to hack response',
  * beforeSendResponse (requestDetail, responseDetail) {
    if (requestDetail.url.indexOf('baidu.com') > -1) {
      const newResponse = responseDetail.response
      newResponse.body = 'AnyProxy Hacked!'
      return new Promise((resolve, reject) => {
        setTimeout(() => {
          resolve({ response: newResponse })
        }, 500)
      })
    }
  }
}
```
启动程序 `node index.js`, 通过代理访问百度， 页面内容会显示为 'AnyProxy Hacked!'

#### rule.js 规则格式

```javascript
module.exports = {
  // 模块介绍
  summary: '',
  // 发送请求前拦截处理
  *beforeSendRequest(requestDetail) { /* ... */ },
  // 发送响应前处理
  *beforeSendResponse(requestDetail, responseDetail) { /* ... */ },
  // 是否处理https请求
  *beforeDealHttpsRequest(requestDetail) { /* ... */ },
  // 请求出错的事件
  *onError(requestDetail, error) { /* ... */ },
  // https连接服务器出错
  *onConnectError(requestDetail, error) { /* ... */ }
}
// 除了summary，都是由 co (https://github.com/tj/co) 驱动的，函数需要满足yieldable。可以返回promise或使用generator函数。
```