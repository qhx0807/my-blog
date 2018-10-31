---
title: 微信H5自动播放音频 ios
date: 2018-09-22 13:28:25
tags: [wechat H5]
categories: javascript
---

要求微信H5一打开自动播放背景音乐，加上 `autoplay` 在ios上不管用

解决办法：

```javascript
var myAuto = document.getElementById('audio')
audio.play()
document.addEventListener("WeixinJSBridgeReady", function() {
  myAuto.play()
}, false)
```
不用引入微信`jssdk`！
