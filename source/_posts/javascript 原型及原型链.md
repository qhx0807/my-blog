---
title: javascript 原型及原型链
date: 2019-05-12 19:51:12
tags: [javascript]
categories: javascript
---

![](http://cdn.cqyyy.cn/pic/20190723165723.png)

<!-- more -->

#### 面向对象

JS是面对对象的编程语言，只不过它实现面对对象的思路是基于原型 `prototype`,而不是类。这种思路也叫对象关联（Object Link Other Object），即在对象上直接映射那种真实世界的关系（如继承）

#### 创建对象

在JS中，对象创建的方法有很多种：

```js
var obj = { name: 'z' }

var obj = Object.create(null)

function Foo () {
  this.name = 'z'
}

var obj = new Foo()

class Foo {
  constructor () {
    this.name = 'z'
  }
}
var obj = new Foo()
```
ES6通过引入 `class`, `extends` 等关键字，以一种语法糖的形式把构造函数包装成类的概念，更便于理解。是希望开发者不再花精力去关注原型以及原型链，也充分说明原型的设计意图和类是一样的。

#### 原型及原型链

```js
function Foo () {
  this.name = 'z'
}
var obj = new Foo()

obj.__proto__ === Foo.prototype // true
obj.__proto__.constructor === Foo
obj.constructor === Foo // true 构造函数
Foo.prototype.__proto__ === Object.prototype // true
Object.prototype.__proto__ === null // true
```

```js
var a = {name: 'z'}
var obj = Object.create(a)

obj.__proto__  === a //true
```
