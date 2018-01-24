---
title: html5game
date: 2017-06-30 21:39:40
tags: [javascript,html5,css,velocity]
categories: 前端
---
需求：实现一个微信公众号营销活动H5接金币小游戏，类似于大富翁。 [演示地址](http://qhxing.site/game/game.html)

启动页：
![](index.jpg)

<!-- more -->

游戏中：

{% asset_img run.jpg %}

#### 分析与设计

1. 10w+ pv  兼容性
2. 产生随机掉落的金币炸弹宝石
3. 下落动画随时间加快
4. 玩家控制人物左右移动
5. 物体与人物的碰撞检测
6. 物体下落到屏幕下方后一定要移除 
7. 计时器、随机数、touch等...

![](design.jpg)

#### 选择实现动画的方式

1. css动画 top++ 

    ```css
    @keyframes fallDown{
        0%   {top:0}
        100% {top:100%;}
    }
    .elem{
        animation: fallDown 3s linear;
    }
    ```
    不知道为什么这种方式在手机上偶尔会出现卡顿，掉帧的感觉。而且不便于控制动画。

2. jquery+Velocity.js

    Velocity 是一个简单易用、高性能、功能丰富的轻量级JS动画库。[中文文档](http://www.mrfront.com/docs/velocity.js/index.html/ "velocity")
    
    兼容性好，流畅，可使用硬件加速。

    使用：

    ```javascript
    $element.velocity({
        width: "500px",        // 动画属性 宽度到 "500px" 的动画
    }, {
        /* Velocity 动画配置项的默认值 */
        duration: 400,         // 动画执行时间
        easing: "swing",       // 缓动效果
        queue: "",             // 队列
        begin: undefined,      // 动画开始时的回调函数
        progress: undefined,   // 动画执行中的回调函数（该函数会随着动画执行被不断触发）
        complete: undefined,   // 动画结束时的回调函数
        display: undefined,    // 动画结束时设置元素的 css display 属性
        visibility: undefined, // 动画结束时设置元素的 css visibility 属性
        loop: false,           // 循环
        delay: false,          // 延迟
        mobileHA: true         // 移动端硬件加速（默认开启）
    });
    ```
    而且提供了动画开始、执行中，执行完成的回调函数。

#### 碰撞检测

![](impact.jpg)

如图所示，碰不上的情况只有四种，其他情况都是发生了碰撞。

```javascript
var t_1 = div1.offsetTop, 
    l_1 = div1.offsetLeft,
    r_1 = div1.offsetLeft + div1.offsetWidth,
    b_1 = div1.offsetTop + div1.offsetHeight;

var t_0 = div2.offsetTop,
    l_0 = div2.offsetLeft;
    r_0 = div2.offsetLeft + div2.offsetWidth,
    b_0 = div2.offsetTop + div2.offsetHeight;

if(b_1<t_0 || l_1>r_0 || t_1>b_0 || r_1<l_0){
    //未发生碰撞
}else{
    //发生碰撞
    //foo() 积分 + 或 -
}
```

#### 产生随机掉落物体

```javascript
var factory = function () {
    var dropTimer = setInterval(function () {
        var left = Math.random()*(win_width-1.9*rootsizePx); //距离左边的宽度
        var delay = Math.random()*2000; //延迟
        var index = Math.floor(Math.random()*dropConfig.length); 
        var imgObj = dropConfig[index]; // 从物体数组里随机选择一个

        if(duration>2000){
            duration -= 20   //动画执行时间
        }

        var htmlStr = '<img src="'+imgObj.path+'" data-score="'+imgObj.score+'" data-delay="'+delay+'" data-duration="'+duration+'" class="dropDown" style="left:'+left+'px;width:'+imgObj.width+'"/>';

        $("#game").append(htmlStr);

        // 调用动画函数

    }, 500)
}
```

#### touch 时间绑定

```javascript
var touchstartFoo = function (evt) {
    evt.preventDefault();
    $("#npc").css("left",evt.touches[0].pageX - $("#npc")[0].offsetWidth/2 + 'px');
}

var touchmoveFoo = function (evt) {
   evt.preventDefault();
   $("#npc").css("left",evt.touches[0].pageX - $("#npc")[0].offsetWidth/2 + 'px');
}

var touchendFoo = function (evt) {
    
}

var bindEvents = function () {
    document.querySelector("#game").addEventListener('touchstart', touchstartFoo, false);
    document.querySelector("#game").addEventListener('touchmove', touchmoveFoo, false);
    document.querySelector("#game").addEventListener('touchend', touchendFoo, false);
}
```
![](down.png)
在微信中，下拉网页会出现上面的情况，如果不想看见这个东西，可以使用：

```javascript
document.addEventListener('touchstart', function (event) {

    event.preventDefault()
    
})

```
