---
title: js运算符的优先级
date: 2019-09-30 19:21:00
categories: javascript
---

运算符的优先级决定了表达式中运算执行的先后顺序，优先级高的运算符最先被执行。

example:

```js
3 > 2 && 2 > 1 // true

3 > 2 > 1 // 因为 3 > 2 是 true，并且 true > 1 is false
```

```js
function Foo() {
  getName = function () {
    console.log(1)
  }
  return this
}

Foo.getName = function () {
  console.log(2)
}

Foo.prototype.getName = function () {
  console.log(3)
}

var getName = function () {
  console.log(4)
}

function getName() {
  console.log(5)
}

Foo.getName() //2
getName() // 4
Foo().getName() // 1
getName() // 1
new Foo.getName() // 2
new Foo().getName() // 3
new new Foo().getName() // 4
```

输出： 2-4-1-1-2-3-3


优先级表：

| 优先级 | 运算类型 | 关联性        | 运算符 |
| ------ | -------- | ------------- | ------ |
| 20     | 圆括号   | n/a（不相关） | ( … )  |
