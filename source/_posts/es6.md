---
title: es6学习笔记1(基础)
date: 2017-09-14 15:12:12
tags: [javascript, es6]
categories: javascript
---
ECMAScript 6（简称ES6）是JavaScript语言的下一代标准。因为当前版本的ES6是在2015年发布的，所以又称ECMAScript 2015。

也就是说，ES6就是ES2015。

虽然目前并不是所有浏览器都能兼容ES6全部特性，但越来越多的程序员在实际项目当中已经开始使用ES6了。所以就算现在不打算使用ES6，但为了看懂别人的也该懂点ES6的语法了...

在正式理解ES6语法之前，得先了解下Babel。

[babel](https://babeljs.io/ "babel")

Babel是一个广泛使用的ES6转码器：

![](http://upload-images.jianshu.io/upload_images/1504317-d020f21868e8e84c.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

<!-- more -->

#### 最常用的ES6特性

`let, const, class, extends, super, arrow functions, template string, destructuring, default, rest arguments`这些是ES6最常用的几个语法

#### let, const

这两个的用途与var类似，都是用来声明变量的，但在实际运用中他俩都有各自的特殊用途。
首先来看下面这个例子：

```javascript
var name = 'zach'

while (true) {
    var name = 'obama'
    console.log(name)  //obama
    break
}

console.log(name)  //obama
```
使用`var `两次输出都是obama，这是因为ES5只有全局作用域和函数作用域，没有块级作用域，这带来很多不合理的场景。第一种场景就是你现在看到的内层变量覆盖外层变量。而`let`则实际上为JavaScript新增了块级作用域。用它所声明的变量，只在`let`命令所在的代码块内有效。

另外一个`var`带来的不合理场景就是用来计数的循环变量泄露为全局变量，看下面的例子：

```javascript
var a = [];
for (var i = 0; i < 10; i++) {
    a[i] = function () {
        console.log(i);
    };
}
a[6](); // 10
```
上面代码中，变量`i`是`var`声明的，在全局范围内都有效。所以每一次循环，新的`i`值都会覆盖旧值，导致最后输出的是最后一轮的`i`的值。而使用`let`则不会出现这个问题。

```javascript
var a = [];
for (let i = 0; i < 10; i++) {
    a[i] = function () {
        console.log(i);
    };
}
a[6](); // 6
```
再来看一个更常见的例子，了解下如果不用ES6，而用闭包如何解决这个问题。

```javascript
var clickBoxs = document.querySelectorAll('.clickBox')
for (var i = 0; i < clickBoxs.length; i++){
    clickBoxs[i].onclick = function(){
        console.log(i)
    }
}
```

本来希望的是点击不同的clickBox，显示不同的i，但事实是无论点击哪个`clickBox`，输出的都是5。下面来看下，如何用闭包搞定它。

```javascript
function iteratorFactory(i){
    var onclick = function(e){
        console.log(i)
    }
    return onclick;
}
var clickBoxs = document.querySelectorAll('.clickBox')
for (var i = 0; i < clickBoxs.length; i++){
    clickBoxs[i].onclick = iteratorFactory(i)
}
```

`const`也用来声明变量，但是声明的是常量。一旦声明，常量的值就不能改变。

```javascript
const PI = Math.PI

PI = 23 //Module build failed: SyntaxError: /es6/app.js: "PI" is read-only
```

当尝试去改变用const声明的常量时，浏览器就会报错。
const有一个很好的应用场景，就是当引用第三方库的时声明的变量，用const来声明可以避免未来不小心重命名而导致出现bug：

```javascript
const monent = require('moment')
```

#### class, extends, super

这三个特性涉及了ES5中最令人头疼的的几个部分：原型、构造函数，继承...你还在为它们复杂难懂的语法而烦恼吗？你还在为指针到底指向哪里而纠结万分吗？

ES6提供了更接近传统语言的写法，引入了Class（类）这个概念。新的class写法让对象原型的写法更加清晰、更像面向对象编程的语法，也更加通俗易懂。

```javascript
class Animal {
    constructor(){
        this.type = 'animal'
    }
    says(say){
        console.log(this.type + ' says ' + say)
    }
}

let animal = new Animal()
animal.says('hello') //animal says hello

class Cat extends Animal {
    constructor(){
        super()
        this.type = 'cat'
    }
}

let cat = new Cat()
cat.says('hello') //cat says hello
```

上面代码首先用`class`定义了一个“类”，可以看到里面有一个`constructor`方法，这就是构造方法，而this关键字则代表实例对象。简单地说，`constructor`内定义的方法和属性是实例对象自己的，而`constructor`外定义的方法和属性则是所有实例对象可以共享的。

`Class`之间可以通过`extends`关键字实现继承，这比ES5的通过修改原型链实现继承，要清晰和方便很多。上面定义了一个`Cat`类，该类通过extends关键字，继承了`Animal`类的所有属性和方法。

`super`关键字，它指代父类的实例（即父类的`this`对象）。子类必须在`constructor`方法中调用`super`方法，否则新建实例时会报错。这是因为子类没有自己的`this`对象，而是继承父类的`this`对象，然后对其进行加工。如果不调用`super`方法，子类就得不到`this`对象。

ES6的继承机制，实质是先创造父类的实例对象`this`（所以必须先调用`super`方法），然后再用子类的构造函数修改`this`。

P.S 如果你写`react`的话，就会发现以上三个东西在最新版React中出现得很多。创建的每个`component`都是一个继承`React.Component`的类。详见react文档

#### arrow function

这个恐怕是ES6最最常用的一个新特性了，用它来写function比原来的写法要简洁清晰很多:

```javascript
function(i){ return i + 1; } //ES5

(i) => i + 1 //ES6
```
简直是简单的不像话对吧...
如果方程比较复杂，则需要用{}把代码包起来：

```javascript
function(x, y) { 
    x++;
    y--;
    return x + y;
}
(x, y) => {x++; y--; return x+y}
```

除了看上去更简洁以外，arrow function还有一项超级无敌的功能！
长期以来，JavaScript语言的`this`对象一直是一个令人头痛的问题，在对象方法中使用`this`，必须非常小心。例如：

```javascript
class Animal {
    constructor(){
        this.type = 'animal'
    }
    says(say){
        setTimeout(function(){
            console.log(this.type + ' says ' + say)
        }, 1000)
    }
}

 var animal = new Animal()
 animal.says('hi')  //undefined says hi
```
运行上面的代码会报错，这是因为setTimeout中的this指向的是全局对象。所以为了让它能够正确的运行，传统的解决方法有两种：

1. 第一种是将this传给self,再用self来指代this
```javascript
    says(say){
        var self = this;
        setTimeout(function(){
            console.log(self.type + ' says ' + say)
        }, 1000)
``` 
2. 第二种方法是用bind(this),即
```javascript
says(say){
    setTimeout(function(){
        console.log(this.type + ' says ' + say)
    }.bind(this), 1000)
```
但现在有了箭头函数，就不需要这么麻烦了：

```javascript
class Animal {
    constructor(){
        this.type = 'animal'
    }
    says(say){
        setTimeout( () => {
            console.log(this.type + ' says ' + say)
        }, 1000)
    }
}
var animal = new Animal()
animal.says('hi')  //animal says hi
```
当使用箭头函数时，函数体内的`this`对象，就是定义时所在的对象，而不是使用时所在的对象。
并不是因为箭头函数内部有绑定`this`的机制，实际原因是箭头函数根本没有自己的`this`，它的`this`是继承外面的，因此内部的`this`就是外层代码块的`this`。

#### template string

这个东西也是非常有用，当要插入大段的html内容到文档中时，传统的写法非常麻烦，所以之前通常会引用一些模板工具库，比如mustache等等。

大家可以先看下面一段代码：

```javascript
$("#result").append(
    "There are <b>" + basket.count + "</b> " +
    "items in your basket, " +
    "<em>" + basket.onSale +
    "</em> are on sale!"
);
```
要用一堆的'+'号来连接文本与变量，而使用ES6的新特性模板字符串``后，可以直接这么来写：

```javascript
$("#result").append(`
  There are <b>${basket.count}</b> items
   in your basket, <em>${basket.onSale}</em>
  are on sale!
`);
```
用反引号（\`）来标识起始，用`${}`来引用变量，而且所有的空格和缩进都会被保留在输出之中，是不是非常爽？！

React Router从第1.0.3版开始也使用ES6语法了，比如这个例子：

`<Link to={`/taco/${taco.name}`}>{taco.name}</Link>`

#### destructuring

ES6允许按照一定模式，从数组和对象中提取值，对变量进行赋值，这被称为解构（Destructuring）。

看下面的例子：

```javascript
let cat = 'ken'
let dog = 'lili'
let zoo = {cat: cat, dog: dog}
console.log(zoo)  //Object {cat: "ken", dog: "lili"}
```
用ES6完全可以像下面这么写：

```javascript
let cat = 'ken'
let dog = 'lili'
let zoo = {cat, dog}
console.log(zoo)  //Object {cat: "ken", dog: "lili"}
```
反过来可以这么写：

```javascript
let dog = {type: 'animal', many: 2}
let { type, many} = dog
console.log(type, many)   //animal 2
```
#### default, rest

default很简单，意思就是默认值。可以看下面的例子，调用`animal()`方法时忘了传参数，传统的做法就是加上这一句`type = type || 'cat' `来指定默认值。

```javascript
function animal(type){
    type = type || 'cat'  
    console.log(type)
}
animal()
```
如果用ES6可以直接这么写：

```javascript
function animal(type = 'cat'){
    console.log(type)
}
animal()
```
最后一个rest语法也很简单，直接看例子：


```javascript
function animals(...types){
    console.log(types)
}
animals('cat', 'dog', 'fish') //["cat", "dog", "fish"]
```
而如果不用ES6的话，则得使用ES5的`arguments`。

#### 
