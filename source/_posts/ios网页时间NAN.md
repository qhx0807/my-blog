---
title: ios网页时间NAN
date: 2018-10-01 13:28:25
tags: [wechat H5]
categories: javascript
---

使用如下方法计算时间，在Android上正常，在IOS上却`NAN`

```javascript
new Date("2018-10-28 23:59:59").getTime()
```

解决办法：

```javascript
var date = "2018-10-28 23:59:59"
var formatDate = date.replace(/-/g, "/")
new Date(formatDate).getTime()
```