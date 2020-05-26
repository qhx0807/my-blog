---
title: koa2 源码阅读解析
date: 2019-07-01 21:21:00
tags: [koa2]
categories: 源码阅读
---

`Koa` 是一个新的 web 框架，由 `Express` 幕后的原班人马打造， 致力于成为 web 应用和 API 开发领域中的一个更小、更富有表现力、更健壮的基石。 通过利用 `async/await` 函数，丢弃回调函数，并有力地增强错误处理。 `Koa` 并没有捆绑任何中间件， 而是提供了一套优雅的方法，帮助快速而愉快地编写服务端应用程序。

![](http://cdn.cqyyy.cn/pic/20190714231335.png)

<!-- more -->

#### koa 源码结构

```
├── application.js
├── context.js
├── request.js
└── response.js
```

`koa2` 的源码目录结构的lib文件夹，lib文件夹下放着四个 `koa2` 的核心文件：`application.js`、`context.js`、`request.js`、`response.js`

#### `application.js`

`Koa` 的入口文件，暴露应用 `Application` 类，这个 `class` 继承自node的核心库 `events`, 这样就会赋予框架事件监听和事件触发的能力。application还暴露了一些常用的api，比如toJSON、listen、use等等。

listen的实现原理其实就是对http.createServer进行了一个封装，重点是这个函数中传入的callback，它里面包含了中间件的合并，上下文的处理，对res的特殊处理。

use是收集中间件，将多个中间件放入一个缓存队列中，然后通过koa-compose这个插件进行递归组合调用这一些列的中间件。

#### `context.js`

这部分就是koa的应用上下文ctx,其实就一个简单的对象暴露，里面的重点在delegate，这个就是代理，这个就是为了开发者方便而设计的，比如我们要访问ctx.repsponse.status但是我们通过delegate，可以直接访问ctx.status访问到它。

#### `request.js`


#### `response.js`

