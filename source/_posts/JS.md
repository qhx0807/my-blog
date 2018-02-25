---
title: JavaScript 有用的代码片段(JavaScript编程黑科技)
date: 2018-02-20 09:54:59
tags: [javascript,黑科技,trick]
categories: javascript
---

收集有用的js代码片段

#### 浮点数取整

```javascript
const x = 123.4567
x >> 0 // 123
~~x // 123
x | 0 // 123
Math.floor(x) //123

// 注意
Math.floor(-12.53) // -13
12.53 | 0 // -12
```

#### 16 进制颜色代码生成

```javascript
'#' + ('00000' + ((Math.random() * 0x1000000) << 0).toString(16)).slice(-6)
```

#### 驼峰命名转下划线

```javascript
const str = 'componentMapModelRegistry'
str
  .match(/^[a-z][a-z0-9]+|[A-Z][a-z0-9]*/g)
  .join('_')
  .toLowerCase()
// component_map_model_registry
```

<!-- more -->

#### url 查询参数转 json 格式

```javascript
// ES6
const query = (search = '') =>
  ((querystring = '') =>
    (q => (
      querystring
        .split('&')
        .forEach(item => (kv => kv[0] && (q[kv[0]] = kv[1]))(item.split('='))),
      q
    ))({}))(search.split('?')[1])

// ES5实现
var query = function(search) {
  if (search === void 0) {
    search = ''
  }
  return (function(querystring) {
    if (querystring === void 0) {
      querystring = ''
    }
    return (function(q) {
      return (
        querystring.split('&').forEach(function(item) {
          return (function(kv) {
            return kv[0] && (q[kv[0]] = kv[1])
          })(item.split('='))
        }),
        q
      )
    })({})
  })(search.split('?')[1])
}

query('?key1=value1&key2=value2') // es6.html:14 {key1: "value1", key2: "value2"}
```

#### 获取 URL 参数

```javascript
function getQueryString(key) {
  var reg = new RegExp('(^|&)' + key + '=([^&]*)(&|$)')
  var r = window.location.search.substr(1).match(reg)
  if (r != null) {
    return
    unescape(r[2])
  }
  return null
}
```

#### n维数组展开成一维数组(扁平化数组)

```javascript
var arr = [1, [2, 3], ['4', 5, ['6',7,[8]]], [9], 10]
// 方法一
// 限制：数组项不能出现`,`，同时数组项全部变成了字符数字
arr.toString().split(','); // ["1", "2", "3", "4", "5", "6", "7", "8", "9", "10"]

// 方法二
// 转换后数组项全部变成数字了
eval('[' + arr + ']'); // [1, 2, 3, 4, 5, 6, 7, 8, 9, 10]

// 方法三
JSON.parse(`[${JSON.stringify(arr).replace(/\[|]/g, '')}]`); // [1, 2, 3, "4", 5, "6", 7, 8, 9, 10]

// 方法四
const
flatten = (ary) => ary.reduce((a, b) => a.concat(Array.isArray(b) ? flatten(b) : b), [])
flatten(arr) // [1, 2, 3, "4", 5, "6", 7, 8, 9, 10]

// 方法五
function flatten(a) {
 return Array.isArray(a) ? [].concat(...a.map(flatten)) : a
}
flatten(arr)  // [1, 2, 3, "4", 5, "6", 7, 8, 9, 10]
```

#### 统计文字个数(不记符号)

```javascript
function wordCount(data) {
 var pattern = /[a-zA-Z0-9_\u0392-\u03c9]+|[\u4E00-\u9FFF\u3400-\u4dbf\uf900-\ufaff\u3040-\u309f\uac00-\ud7af]+/g;
 var m = data.match(pattern);
 var count = 0;
 if( m === null ) return count;
 for (var i = 0; i < m.length; i++) {
   if (m[i].charCodeAt(0) >= 0x4E00) {
     count += m[i].length;
   } else {
     count += 1;
   }
 }
 return count;
}
var text = '呵呵！'
wordCount(text) // 2
```

#### 测试质数

```javascript
function isPrime(n) {
 return !(/^.?$|^(..+?)\1+$/).test('1'.repeat(n))
}
```

#### 统计字符串中相同字符出现的次数

```javascript
var str = 'abcdaabc'
var info = str.split('').reduce((p, k) => (p[k]++ || (p[k] = 1), p), {})
console.log(info)  // { a: 3, b: 2, c: 2, d: 1 }
```
#### 使用 ~x.indexOf('y')来简化 x.indexOf('y')>-1

```javascript
var str = 'hello world'
if (str.indexOf('lo') > -1) {
 // ...
}
if (~str.indexOf('lo')) {
 // ...
}

```
#### 匿名函数自执行写法(IIFE)

```javascript
function() {}() );
( function() {} )();
[ function() {}() ];
~ function() {}();
! function() {}();
+ function() {}();
- function() {}();
delete function() {}();
typeof function() {}();
void function() {}();
new function() {}();
new function() {};
var f = function() {}();
1, function() {}();
1 ^ function() {}();
1 > function() {}();
```

#### 两个整数交换数值

```javascript
// 1
var a = 20, b = 30;
a ^= b;
b ^= a;
a ^= b;

//2
[a, b] = [b, a]
```
#### 身份证验证

```javascript
function chechCHNCardId(sNo) {
    var reg =  /^[0-9]{17}[X0-9]$/
 if (!reg.test(sNo,)) {
   return false;
 }
 sNo = sNo.toString();
 var a, b, c;
 a = parseInt(sNo.substr(0, 1)) * 7 + parseInt(sNo.substr(1, 1)) * 9 + parseInt(sNo.substr(2, 1)) * 10;
 a = a + parseInt(sNo.substr(3, 1)) * 5 + parseInt(sNo.substr(4, 1)) * 8 + parseInt(sNo.substr(5, 1)) * 4;
 a = a + parseInt(sNo.substr(6, 1)) * 2 + parseInt(sNo.substr(7, 1)) * 1 + parseInt(sNo.substr(8, 1)) * 6;
 a = a + parseInt(sNo.substr(9, 1)) * 3 + parseInt(sNo.substr(10, 1)) * 7 + parseInt(sNo.substr(11, 1)) * 9;
 a = a + parseInt(sNo.substr(12, 1)) * 10 + parseInt(sNo.substr(13, 1)) * 5 + parseInt(sNo.substr(14, 1)) * 8;
 a = a + parseInt(sNo.substr(15, 1)) * 4 + parseInt(sNo.substr(16, 1)) * 2;r
 b = a % 11;
 if (b == 2) {
   c = sNo.substr(17, 1).toUpperCase();
 } else {
   c = parseInt(sNo.substr(17, 1));
 }
 switch (b) {
   case 0:
     if (c != 1) {
       return false;
     }
     break;
   case 1:
     if (c != 0) {
       return false;
     }
     break;
   case 2:
     if (c != "X") {
       return false;
     }
     break;
   case 3:
     if (c != 9) {
       return false;
     }
     break;
   case 4:
     if (c != 8) {
       return false;
     }
     break;
   case 5:
     if (c != 7) {
       return false;
     }
     break;
   case 6:
     if (c != 6) {
       return false;
     }
     break;
   case 7:
     if (c != 5) {
       return false;
     }
     break;
   case 8:
     if (c != 4) {
       return false;
     }
     break;
   case 9:
     if (c != 3) {
       return false;
     }
     break;
   case 10:
     if (c != 2) {
       return false;
     };
 }
 return true;
}

```
