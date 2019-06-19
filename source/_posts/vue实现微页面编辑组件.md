---
title: vue实现微页面编辑组件
date: 2019-06-11 20:18:02
tags: [vue]
categories: vue
---
之前的定义模板页面功能太少，局限性太大，满足不了日常使用的需求，这次页面改版，之前看过有赞商户后台的微页面编辑功能，所以想在我们的系统中实现类似的功能，自定义组件组合成页面。

![](http://cdn.cqyyy.cn/pic/20190614171007.png)

<!-- more -->

#### 怎么实现

有赞的React后台组件[zent](https://youzan.github.io/zent/zh/component/design)中实现了 `design` 微页面编辑组件。作为参考。

一个组件的属性：

```javascript
{
  type: 'config', // 组件类型，将决定由哪个组件渲染
  ...
  // 组件内部需要的属性和值
}
```

一个组件的组成：
 - `editor`
    右边编辑区域，编辑每个组件的属性
    1. 混入 mixins `base/minxins/editorMixins.js`
    2. `designValue` 为组件的值 (Object)，用作表单双向绑定，组件的值根据每个功能组件具体需求实现

 - `preview`

    左边显示效果的区域，根据 `props` 实现相应的UI，所以在zent中使用了 `PureComponent`.
    1. 混入 mixins `base/minxins/previewMixins.js`
    2. 留一个 `<slot>`

 - `index.js`
    1. 引入预览组件和编辑组件
    2. 引入高阶组件 `base/combineComponents.js`将组价合并
    3. 定义组件属性：`designType`, `designDescription`
    4. 定义组件初始值 导出最终的组件。


#### 基础构建

`DesignBase.vue` 组件是整个的基础容器，根据type 类型渲染组件，组件的新增删除事件，拖动功能。

`DesignController.vue` 是 `preview` 的 `solt`，它提供的边框，前后新增按钮，删除功能。

`DesignEditor.vue` 是 `editor` 的容器组件, 负责编辑组件的样式，如 绝对定位，组件名称等。

`renderPreview.js` 根据type类型渲染组件，它要接收所有组件作为 `props`

```javascript
export default {
  name: 'renderPreview',
  props: {
    value: Object,
    designComponents: Array,
    isactive: {
      type: Boolean,
      default: false
    }
  },
  inheritAttrs: false,
  render (h) {
    let tag = this.designComponents.find(item => {
      return item.designType === this.value.type
    })
    return h(tag, {
      props: {
        value: this.value,
        isactive: this.isactive
      },
      inheritAttrs: false,
      on: this.$listeners,
      attrs: this.$attrs
    })
  }
}
```

`combineComponents.js` 联合 `preview` 和 `editor` 组件的一个高阶组件，接收组件，返回组件，

```javascript
return h('div', {
  style: {
    position: 'relative' // 
  }
}, [p, this.isactive ? e : null])
```
因为 `editor` 组件要根据父元素绝对定位，这个 `relative` 不可少。

#### 实现其他业务组件

然后就可以根据自己业务需求，添加其他组件，当然有的会需要后台配合，然后再手机端
根据类型实现相应的组件。

最终一个页面的值会是这样一个数组:

![](http://cdn.cqyyy.cn/pic/20190614174856.png)

有时间的话会把这整个design组件再好好构造一下，还有很多地方可以再封装抽象。

#### 值得总结的地方

1. vue 函数渲染 https://cn.vuejs.org/v2/guide/render-function.html

2. HOC 高阶组件，在vue中其实是用的render函数

3. 自定义组件的 `v-model`, props 设置为 value, $emit('input', '...')
https://cn.vuejs.org/v2/guide/components-custom-events.html#%E8%87%AA%E5%AE%9A%E4%B9%89%E7%BB%84%E4%BB%B6%E7%9A%84-v-model

4. [vue 混入](https://cn.vuejs.org/v2/guide/mixins.html), 估计vue3就不会有mixins了，毕竟存在值来源不清晰，多个mixins间相互依赖可能出现问题

5. `$listeners` 垮组件地传递事件，就不用在每一层上面再接收再发射。
https://cn.vuejs.org/v2/api/#vm-listeners

6. 父组件动态传递 props，子组件接下来希望将其作为一个本地的 prop 数据来使用

https://cn.vuejs.org/v2/guide/components-props.html#%E5%8D%95%E5%90%91%E6%95%B0%E6%8D%AE%E6%B5%81

```html
<!-- 组件A -->
<template>
  <div>
    <Test :parentValue="arr"></Test>
    <Button @click="cover">cover</Button>
    <Button @click="push">push</Button>
  </div>
</template>
<script>
import B from './B'
export default {
  name: 'A',
  components: {
    B
  },
  data () {
    return {
      arr: [1, 2, 3]
    }
  },
  methods: {
    // 父组件想更改传给 B 的 props 
    cover () {
      this.arr = [0, 0, 0] // 这种方法是不行的
    },
    push () {
      this.arr.push(233) // 可以！
    }
  }
}
</script>
```

```html
<!-- 组件B -->
<template>
  <div>
    <p v-for="(item, index) in arr">{{item}}</p>
  </div>
</template>
<script>
export default {
  props: {
    parentValue: {
      type: Array,
      default: () => []
    }
  },
  data () {
    return {
      arr: []
    }
  },
  created () {
    this.arr = this.parentValue
  }
}
</script>
```