---
title: javascript中 apply,call,bind 方法的作用和区别
date: 2019-06-30 22:54:22
tags: [javascript]
categories: javascript
---
这三个方法都是为了改变某个函数运行时的上下文（context）而存在的，换句话说，就是为了改变函数体内部 this 的指向。

在 ES5 中，其实 `this` 的指向，始终坚持一个原理：**`this` 永远指向最后调用它的那个对象**。

#### `this`的指向

**`this` 永远指向最后调用它的那个对象**

```javascript
var name = 'global name'
function foo () {
  var name = 'scope name'
  console.log(name)
  console.log(this.name)
}
foo()
// 'scope name'
// 'global name'  this.name
```
上面调用 `foo` 的是 `window` (浏览器中), 相当于 `window.foo()`, 所有 `this.name` 为 `global name`

```javascript
var name = 'global name'
var foo = {
  name: 'scope name',
  fn: function () {
    console.log(this.name)
  }
}
foo.fn() // 'scope name'
```
调用 `fn` 的为 `window.foo`, 所有打印出的相当于 `window.foo.name`

```javascript
var name = 'global name'
var foo = {
  name: 'scope name',
  fn: function () {
    console.log(this.name)
  }
}
var f = foo.fn
f() // 这里会打印出什么？
```
上面会打印出 `'global name'`, 注意：`fn()` 最后是window调用的，所打出 `window.name`

#### 改变 `this` 的指向

使用 apply、call、bind 改变 `this` 的指向

```javascript
var name = 'global name'
var obj = {
  name: 'scope name'
}
function foo () {
  console.log(this.name)
}
foo() // 'global name'
foo.apply(obj)    // 'scope name'
foo.call(obj)     // 'scope name'
foo.bind(obj)()   // 'scope name'
```
#### 区别

1. apply()

> apply() 方法调用一个函数, 其具有一个指定的this值，以及作为一个数组（或类似数组的对象）提供的参数.

```javascript
fun.apply(thisArg, [argsArray])
```
* thisArg：在 `fun` 函数运行时指定的 `this` 值。需要注意的是，指定的 `this` 值并不一定是该函数执行时真正的 `this` 值，如果这个函数处于非严格模式下，则指定为 `null` 或 `undefined` 时会自动指向全局对象（浏览器中就是window对象），同时值为原始值（数字，字符串，布尔值）的 `this` 会指向该原始值的自动包装对象。

* argsArray：一个数组或者类数组对象，其中的数组元素将作为单独的参数传给 `fun` 函数。如果该参数的值为 `null` 或 `undefined`，则表示不需要传入任何参数。从ECMAScript 5 开始可以使用类数组对象。

2. call()

> call() 该方法的语法和作用与 `apply()` 方法类似，只有一个区别，就是 `call()` 方法接受的是一个参数列表，而 `apply()` 方法接受的是一个包含多个参数的数组。

```javascript
fun.call(thisArg[, arg1[, arg2[, ...]]])
```

3. bind()

> bind()方法创建一个新的函数，在bind()被调用时，这个新函数的this被bind的第一个参数指定，其余的参数将作为新函数的参数供调用时使用。

bind 是创建一个新的函数，必须要手动去调用!

fn.bind(thisArg)([, arg1[, arg2[, ...]]])

```javascript
var obj ={
  name : "my name",
  fn: function (a, b) {
    console.log(a + b)
  }
}
obj.fn.apply(obj, [1, 3]) // 4
obj.fn.call(obj, 1, 3)    // 4
obj.fn.bind(obj)(1, 3)    // 4
```

