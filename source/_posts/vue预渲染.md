---
title: vue项目中使用预渲染
date: 2018-11-23 21:24
tags: [vue]
categories: 前端开发
---

预渲染只是用来改善少数营销页面（例如 /, /about, /contact 等）的 SEO。无需使用 web 服务器实时动态编译 HTML，而是使用预渲染方式，在构建时 (build time) 简单地生成针对特定路由的静态 HTML 文件。

<!-- more -->

在vue-cli3中使用 [prerender-spa-plugin](https://github.com/chrisvfritz/prerender-spa-plugin),`vue-cli3` 提供了一个零配置原型开发，可以快速搭建应用而不用管配置.
1. 安装 [vue-cli-plugin-prerender-spa](https://github.com/SolarLiner/vue-cli-plugin-prerender-spa) `vue-cli3` 中的 `prerender-spa-plugin` 插件

用起来都很简单，主要是在安装步骤，这里会安装 [puppeteer](https://github.com/GoogleChrome/puppeteer), 一个无头浏览器，它会下载 `Chromium ` 内核，接近200M大小，

[puppeteer文档](https://zhaoqize.github.io/puppeteer-api-zh_CN/#/) 十分强大，无界面版chrome, 可以用来做很多事。

2. 在项目下新建文件 `vue.config.js`

```javascirpt
module.exports = {
  pluginOptions: {
    prerenderSpa: {
      registry: undefined,
      renderRoutes: [
        '/',
        '/home',
        '/about'
      ],
      useRenderEvent: true,
      headless: true,
      onlyProduction: true
    }
  }
}
```

3. 在入口文件 `main.js` 中

```javascript
new Vue({
  router,
  render: h => h(App),
  mounted: () => document.dispatchEvent(new Event('x-app-rendered')) // add
}).$mount('#app')
```
4. 执行命令 `npm run build` 就会在 `dist` 目录中生成对应的静态html文件。

总结：
预渲染只适合于少数不依赖于后端数据的活动宣传营销页面，并不能大面积的使用，如果是为了真正的SEO,加载速度和用户体验，还是得上SSR.
[vue-ssr](https://ssr.vuejs.org/zh/)
