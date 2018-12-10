---
title: ios 网页滚动时卡顿
date: 2018-12-02 21:01:22
tags: [ios, css]
categories: css
---
ios系统微信浏览器、safari浏览器中h5页面上拉下滑导致悬浮层脱离窗口的解决方法

很大原因是因为设置了 

```css
html,body{
  height: 100%;
}
```

删除面的css.

再加上：

```css
*{
  -webkit-overflow-scrolling: touch;
}
```

`-webkit-overflow-scrolling`: 属性控制元素在移动设备上是否使用滚动回弹效果.

  1. `auto`
    使用普通滚动, 当手指从触摸屏上移开，滚动会立即停止。
  2. `touch`
    具有回弹效果的滚动，开启硬件加速。

