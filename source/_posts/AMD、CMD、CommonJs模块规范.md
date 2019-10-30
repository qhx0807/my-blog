---
title: AMD、CMD、CommonJs、ES6模块规范
date: 2019-10-11 11:32:00
tags: [javascript]
categories: javascript
---

#### AMD规范

`AMD` 即Asynchronous Module Definition，中文名是异步模块定义的意思。它是一个在浏览器端模块化开发的规范。

由于不是JavaScript原生支持，使用AMD规范进行页面开发需要用到对应的库函数，也就是大名鼎鼎 `RequireJS` ，实际上AMD 是 `RequireJS` 在推广过程中对模块定义的规范化的产出。

requireJS主要解决两个问题：

1. 多个js文件可能有依赖关系，被依赖的文件需要早于依赖它的文件加载到浏览器。
2. js加载的时候浏览器会停止页面渲染，加载文件越多，页面失去响应时间越长。

```js
// 定义模块 myModule.js
define(['dependency'], function(){
  var name = 'Byron'
  function printName(){
    console.log(name)
  }

  return {
    printName: printName
  }
})

// 加载模块
require(['myModule'], function (my){
　 my.printName()
})
```

#### CMD规范

`CMD` 即Common Module Definition通用模块定义，CMD规范是国内发展出来的，就像AMD有个 `requireJS` ，CMD有个浏览器的实现 `SeaJS` ，SeaJS要解决的问题和 `requireJS` 一样，只不过在模块定义方式和模块加载（可以说运行、解析）时机上有所不同。

`Sea.js` 推崇一个模块一个文件，遵循统一的写法

```js
define(id?, deps?, factory)
```
- 一个文件一个模块，所以经常就用文件名作为模块id
- CMD推崇依赖就近，所以一般不在define的参数中写依赖，在factory中写

factory是一个函数，有三个参数，function(require, exports, module)

- require 是一个方法，接受 模块标识 作为唯一参数，用来获取其他模块提供的接口：require(id)
- exports 是一个对象，用来向外提供模块接口
- module 是一个对象，上面存储了与当前模块相关联的一些属性和方法

```js
// 定义模块  myModule.js
define(function(require, exports, module) {
  var $ = require('jquery.js')
  $('div').addClass('active')
})

// 加载模块
seajs.use(['myModule.js'], function(my){

})
```

AMD与CMD区别:

1. AMD推崇依赖前置，在定义模块的时候就要声明其依赖的模块
2. CMD推崇就近依赖，只有在用到某个模块的时候再去require

#### ES6模块化

[阮一峰 - Module 的语法](http://es6.ruanyifeng.com/#docs/module)

在ES6之前，社区指定了一些模块加载方案，最主要的是CMD和AMD这两种，ES6在语言标准上实现了模块功能，而且实现得相当简单，完全可以取代CommonJS和AMD规范，成为浏览器和服务器通用的模块解决方案。

```js
// ES6模块
import { stat, exists, readFile } from 'fs'
```
上面代码的实质是从`fs`模块加载3个方法，其他方法不加载。这种加载称为“编译时加载”或者静态加载，即ES6可以在编译时就完成模块加载，效率要比 `CommonJS` 模块的加载方式高。


#### CommonJS规范

node.js的模块系统，就是参照CommonJS规范实现的

[CommonJS规范-阮一峰](http://javascript.ruanyifeng.com/nodejs/module.html)

CommonJS规范规定，每个模块内部，module变量代表当前模块。这个变量是一个对象，它的exports属性（即module.exports）是对外的接口。加载某个模块，其实是加载该模块的module.exports属性。

```js
var x = 5
var addX = function (value) {
  return value + x
};
module.exports.x = x
module.exports.addX = addX
```

上面代码通过 `module.exports` 输出变量x和函数addX。
`require` 方法用于加载模块。

```js
var example = require('./example.js')
console.log(example.x) // 5
console.log(example.addX(1)) // 6
```

CommonJS模块的特点如下:

- CommonJS规范加载模块是同步的，也就是说，只有加载完成，才能执行后面的操作
- 所有代码都运行在模块作用域，不会污染全局作用域。
- 模块可以多次加载，但是只会在第一次加载时运行一次，然后运行结果就被缓存了，以后再加载，就直接读取缓存结果。要想让模块再次运行，必须清除缓存。
- 模块加载的顺序，按照其在代码中出现的顺序。

#### 兼容多种模块规范 UMD

UMD 叫做通用模块定义规范（Universal Module Definition）。也是随着大前端的趋势所诞生，它可以通过运行时或者编译时让同一个代码模块在使用 CommonJs、CMD 甚至是 AMD 的项目中运行。未来同一个 JavaScript 包运行在浏览器端、服务区端甚至是 APP 端都只需要遵守同一个写法就行了。

```js
;(function(name, definition) { // 检测上文环境是否为AMDCMD 
  var hasDefine = typeof define === 'function', // 检查上文环境是否为Node 
      hasExports = typeof module !== 'undefined' && module.exports

  if (hasDefine) { // AMD环境CMD环境    
    define(definition)
  } else if (hasExports) {
    // 定义为通Node模块     
    module.exports = definition()
  } else { // 将模块的执行结在window量中在器中thiswindow对象    
    this[name] = definition()
  }
})('hello', function() {
  var hello = function() {}
  return hello
})
```