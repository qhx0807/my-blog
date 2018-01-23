---
title: 检测网页过期(PC端)
date: 2017-09-12 09:26:13
tags: [javascript,html5]
categories: 前端
---

用于检测用户长时间未操作，页面过期，监听鼠标键盘事件。

```javascript
var count = 0; 
var x, y;
var outTime=5; //5分钟时间

window.setInterval(go, 1000);

function go() {
    count++;
    if (count == outTime*60) {
        alert("长时间未进行操作!");
    }
}

document.onmousemove = function (event) {
    var x1 = event.clientX;
    var y1 = event.clientY;
    if (x != x1 || y != y1) {
        count = 0;
    }
    x = x1;
    y = y1;
};

document.onkeydown = function () {
    count = 0;
};
```