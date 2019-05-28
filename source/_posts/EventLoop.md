---
title: javascript事件循环机制 EventLoop
date: 2019-05-03 13:24
tags: [EventLoop]
categories: javascript
---

> 所有任务可以分成两种，一种是同步任务（synchronous），另一种是异步任务（asynchronous）。同步任务指的是，在主线程上排队执行的任务，只有前一个任务执行完毕，才能执行后一个任务；异步任务指的是，不进入主线程、而进入"任务队列"（task queue）的任务，只有"任务队列"通知主线程，某个异步任务可以执行了，该任务才会进入主线程执行。

异步执行的运行机制如:

1. 所有同步任务都在主线程上执行，形成一个执行栈（execution context stack）。
2. 主线程之外，还存在一个"任务队列"（task queue）。只要异步任务有了运行结果，就在"任务队列"之中放置一个事件。
3. 一旦"执行栈"中的所有同步任务执行完毕，系统就会读取"任务队列"，看看里面有哪些事件。那些对应的异步任务，于是结束等待状态，进入执行栈，开始执行。
4. 主线程不断重复上面的第三步。
<!-- more -->

#### 浏览器 Event Loop

![事件循环（Event Loop）](http://www.ruanyifeng.com/blogimg/asset/2014/bg2014100802.png)

不同的异步任务被分为两类：微任务（micro task）和宏任务（macro task）

以下事件属于宏任务：

1. setInterval()
2. setTimeout()

以下事件属于微任务:

1. new Promise()
2. new MutaionObserver() [mdn](https://developer.mozilla.org/zh-CN/docs/Web/API/MutationObserver)

**同一次事件循环中，微任务永远在宏任务之前执行。**

**在执行微队列microtask queue中任务的时候，如果又产生了microtask，那么会继续添加到队列的末尾，也会在这个周期执行，直到microtask queue为空停止。**

```javascript
setTimeout(function () {
  console.log(1)
})

new Promise(function(resolve,reject){
  console.log(2)
  resolve(3)
}).then(function(val){
  console.log(val)
})
// 打印结果： 2 3 1
```

```javascript
console.log(1)

setTimeout(() => {
  console.log(2)
  Promise.resolve().then(() => {
    console.log(3)
  })
})

new Promise((resolve, reject) => {
  console.log(4) // 注意，这里是同步执行的 TODO: promise
  resolve(5)
}).then((val) => {
  console.log(val)
})

setTimeout(() => {
  console.log(6)
})

console.log(7)
// 打印结果： 1 4 7 5 2 3 6
```

```javascript
console.log(1)                        // 同步执行 1

setTimeout(() => {
  console.log(2)                      // 宏任务 7
  Promise.resolve().then(() => {
    console.log(3)                    // 8
  })
});

new Promise((resolve, reject) => {
  console.log(4)                      // 同步执行 2
  resolve(5)
}).then((val) => {
  console.log(val)                    // 微任务 4
  
  Promise.resolve().then(() => {
    console.log(6)                    // 微任务 5
  }).then(() => {
    console.log(7)                    // 微任务 6
    
    setTimeout(() => {
      console.log(8)                  // 10
    }, 0)
  })
})

setTimeout(() => {
  console.log(9)                      // 9
})

console.log(10)                       // 同步执行 3
// 打印结果：1 4 10 5 6 7 2 3 9 8
```