---
title: vue自定义指令 查看图片
date: 2018-06-22 13:28:25
tags: [javascript]
categories: javascript
---

`main.js`全局注册指令

```javascript
Vue.directive('imgview', {
  bind (el, binding, vnode) {
    let imgSrc = el.getAttribute('src')
    el.style.cursor = 'zoom-in'
    el.addEventListener('click', function (e) {
      if (imgSrc) {
        document.getElementById('imgViewDom').firstChild.src = imgSrc
        document.getElementById('imgViewDom').style.display = 'flex'
      }
    })
  },
  unbind (el) {
    el.removeEventListener('click', null)
  }
})
```

<!-- more -->

`app.vue` DOM结构

```html
<div id="imgViewDom" @click="onClickImgBox" style="disply: none">
  <img @click="onClickImgBox" src="" alt="">
</div>
```
`app.vue` 方法

```javascript
export default {
  name: 'App',
  methods: {
    onClickImgBox (e) {
      document.getElementById('imgViewDom').style.display = 'none'
    }
  }
}
```
样式
```css
#imgViewDom{
  position: fixed;
  top: 0;
  left: 0;
  height: 100%;
  width: 100%;
  z-index: 99999999;
  background: rgba(255, 255, 255,0.8);
  overflow: auto;
  display: flex;
  justify-content: center;
  align-items: center;
  display: none;
  transition: all ease-in-out .3s;
  img{
    cursor: zoom-out;
  }
}
```