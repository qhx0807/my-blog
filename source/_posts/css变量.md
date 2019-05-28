---
title: css变量
date: 2019-04-17 13:29
tags: [css]
categories: 前端开发
---

在任何语言中，变量的有一点作用都是一样的，那就是可以降低维护成本，附带还有更高性能，文件更高压缩率的好处。

随着CSS预编译工具Sass/Less/Stylus的关注和逐渐流行，CSS工作组迅速跟进CSS变量的规范制定，并且，很多浏览器已经跟进，目前，在部分项目中已经可以直接使用了。

#### CSS变量语法和用法和特性

CSS中原生的变量定义语法是：`--*`，变量使用语法是：`var(--*)`，其中*表示我们的变量名称。关于命名这个东西，各种语言都有些显示，例如CSS选择器不能是数字开头，JS中的变量是不能直接数值的，但是，在CSS变量中，这些限制通通没有，例如：

```css
:root {
  --1: #369;
}
body {
  background-color: var(--1);
}
```
<!-- more -->

但是，不能包含$，[，^，(，%等字符，普通字符局限在只要是“数字[0-9]”“字母[a-zA-Z]”“下划线_”和“短横线-”这些组合，但是可以是中文，日文或者韩文，例如：

```css
body {
  --深蓝: #369;
  background-color: var(--深蓝);
}
```

变量名大小写敏感，--header-color和--Header-Color是两个不同变量。

#### `var()` 函数

`var()` 函数用于读取变量。
```css
a {
  color: var(--foo);
  text-decoration-color: var(--bar);
}
```
var()函数还可以使用第二个参数，表示变量的默认值。如果该变量不存在，就会使用这个默认值。

#### 作用域
同一个 CSS 变量，可以在多个选择器内声明。读取的时候，优先级最高的声明生效。这与 CSS 的"层叠"（cascade）规则是一致的。

```html
<style>
  :root { --color: blue; }
  div { --color: green; }
  #alert { --color: red; }
  * { color: var(--color); }
</style>

<p>蓝色</p>
<div>绿色</div>
<div id="alert">红色</div>
```
#### JavaScript 操作

JavaScript 也可以检测浏览器是否支持 CSS 变量。

```javascript
const isSupported =
  window.CSS &&
  window.CSS.supports &&
  window.CSS.supports('--a', 0)

if (isSupported) {
  /* supported */
} else {
  /* not supported */
}
```

JavaScript 操作 CSS 变量的写法如下。

```javascript
// 设置变量
document.body.style.setProperty('--primary', '#7F583F')

// 读取变量
document.body.style.getPropertyValue('--primary').trim()
// '#7F583F'

// 删除变量
document.body.style.removeProperty('--primary')
```
那些对 CSS 无用的信息，也可以放入 CSS 变量。

```javascript
--foo: if(x > 5) this.width = 10;
```
上面代码中，`--foo`的值在 CSS 里面是无效语句，但是可以被 JavaScript 读取。这意味着，可以把样式设置写在 CSS 变量中，让 JavaScript 读取。

所以，CSS 变量提供了 JavaScript 与 CSS 通信的一种途径。