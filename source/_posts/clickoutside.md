---
title: vue自定义指令ClickOutside
date: 2018-01-26 22:30:44
tags: ['directive', 'vue', 'javascript']
categories: [vue]
---

[vue 自定义指令文档](https://cn.vuejs.org/v2/guide/custom-directive.html)
点击元素外部的事件，一是点击元素本身是不会关闭的，二是点击元素以外的所有区域都要关闭。点击所有区域，可以在 document 上绑定 click 事件来实现，同时只要过滤出是否点击的是目标元素内部的元素即可。

```javascript
Vue.directive('clickoutside', {
  bind(el, binding, vnode) {
    function documentHandler(e) {
      if (el.contains(e.target)) {
        return false
      }
      if (binding.expression) {
        binding.value(e)
      }
    }
    el.__vueClickOutside__ = documentHandler
    document.addEventListener('click', documentHandler)
  },
  unbind(el, binding) {
    document.removeEventListener('click', el.__vueClickOutside__)
    delete el.__vueClickOutside__
  }
})
```
