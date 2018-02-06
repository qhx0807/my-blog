---
title: 手机端1px边框
date: 2017-04-05 20:36:00
tags: [css, html5]
categories: css
---
在多数移动端上css中的1px并不等于设备的1px。不同的手机有不同的像素密度。Window.devicePixelRatio。

`@media only screen and (-webkit-min-device-pixel-ratio:1){}`
`@media only screen and (-webkit-min-device-pixel-ratio:2){}`
`@media only screen and (-webkit-min-device-pixel-ratio:2.5){}`
`@media only screen and (-webkit-min-device-pixel-ratio:3){}`

1. 背景渐变：设置1px渐变背景，0.5可见，0.5透明。
```css
.background-gradient-1px {
background:
linear-gradient(180deg, black, black 50%, transparent 50%) top    left  / 100% 1px no-repeat,
linear-gradient(90deg,  black, black 50%, transparent 50%) top    right / 1px 100% no-repeat,
linear-gradient(0,      black, black 50%, transparent 50%) bottom right / 100% 1px no-repeat,
linear-gradient(-90deg, black, black 50%, transparent 50%) bottom left  / 1px 100% no-repeat;
}
/*简写*/
.background-gradient-1px{
background: -webkit-gradient(linear, left top, left bottom, color-stop(.5, transparent), color-stop(.5, #c8c7cc), to(#c8c7cc)) left bottom repeat-x;
background-size: 100% 1px;
}```
2. 用box-shadow阴影实现。
```css
.box-shadow-1px {
	box-shadow: inset 0px -1px 1px -1px #000;
}```
3. 伪类 + transform 实现。
一条边框
```css
.scale-1px{
position: relative;
border:none;
}
.scale-1px:after{
content: '';
position: absolute;
bottom: 0;
background: #000;
width: 100%;
height: 1px;
-webkit-transform: scaleY(0.5);
transform: scaleY(0.5);
-webkit-transform-origin: 0 0;
transform-origin: 0 0;
}```
四条边框，可设置border-radius
```css
.scale-1px{
position: relative;
margin-bottom: 20px;
border:none;
}
.scale-1px:after{
content: '';
position: absolute;
top: 0;
left: 0;
border: 1px solid #000;
-webkit-box-sizing: border-box;
box-sizing: border-box;
width: 200%;
height: 200%;
-webkit-transform: scale(0.5);
transform: scale(0.5);
-webkit-transform-origin: left top;
transform-origin: left top;
}```