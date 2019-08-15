---
title: nodejs生成二维码&图像拼接
date: 2019-07-21 17:28:11
categories: javascript
---


场景背景：平台上有几百个商户，每个商户有一个收款二维码，以前都是员工在后台挨个点击下载二维码，然后给UI做成收款码物料。
前段时间需要紧急给商户更换二维码，这种重复性的劳动效率太低，于是写个脚本批量生成二维码。

#### 生成二维码

每个二维码的链接类似，只是参数有变化。 生成二维码库： [qr-image](https://github.com/alexeyten/qr-image)

使用：

```js
const qr = require('qr-image')
const fs = require('fs')

let qrImg = qr.image(text, { ec_level: 'H', size: 10, type: 'png', margin: 1 })
qrImg.pipe(fs.createWriteStream('./output/' + item.name + '.png'))
```
配置项：

- `ec_level` 二维码质量 L, M, Q, H, 默认 M
- `type` 图片格式 `png`, `svg`, `pdf`, `eps`
- `size` png/svg 格式下图片大小
- `margin` 图片白边


#### GraphicsMagick 环境安装

`GraphicsMagick` 是一个短小精悍的的图片处理工具和库集合。

下载地址：[GraphicsMagick](https://sourceforge.net/projects/graphicsmagick/files/)，我的电脑是windows系统，下载 graphicsmagick-binaries文件然后安装。

`gm`: GraphicsMagick for node. [Github](http://aheckmann.github.io/gm/)，[官方文档](http://aheckmann.github.io/gm/)

```js
const fs = require('fs')
const gm = require('gm')

// resize and remove EXIF profile data
gm('./test.png')
  .resize(240, 240)
  .noProfile()
  .write('./dist/resize.png', function (err) {
    if (!err) console.log('done')
  })
```


