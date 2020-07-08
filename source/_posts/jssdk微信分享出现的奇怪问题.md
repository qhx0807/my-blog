---
title: jssdk微信分享出现的奇怪问题
date: 2020-07-01 21:21:00
categories: javascript
---

做一个宣传H5，有微信分享功能。

前端开发采用技术：vue vue-router vant

电脑上开发测试一切正常，分享正常

Android ok

苹果上微信分享死活不行， 99%的情况下分享不起。有时刷新一下可以。

调试：

```js
wx.config({
  debug: true,
  appId: appId,
  timestamp: timestamp,
  nonceStr: nonceStr,
  signature: signature,
  jsApiList: [
    "updateTimelineShareData",
    "updateAppMessageShareData",
    ]
})
wx.error(error => {
  console.log(error)
  this.$toast(JSON.stringify(error))
})
```

在苹果手机上报错：

`wxconfig` 大多数时候提示 `invalid signature`.

`{"errMsg":"updateTimelineShareData:fail, the permission value is offline verifying"}`
`{"errMsg":"updateAppMessageShareData:fail, the permission value is offline verifying"}`

解决方案：

实际上签名数据没有错误，是 `vue-router` 跳转页面的锅。

修改上一个页面 `this.$router.push({name: xxxx})` 为

```js
location.href = 'full url'
```
问题解决。