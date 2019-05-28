---
title: 合并单元格
date: 2018-10-11 20:18:02
tags: [vue mvvm]
categories: javascript
---

在`mvvm`框架中，表格合并单元格的方（竖向）

```javascript
const combineCell = (list) => {
  for (let field in list[0]) {
    var k = 0
    while (k < list.length) {
      list[k][field + 'span'] = 1
      list[k][field + 'dis'] = false
      for (var i = k + 1; i <= list.length - 1; i++) {
        if (list[k][field] === list[i][field] && list[k][field] !== '') {
          list[k][field + 'span']++
          list[k][field + 'dis'] = false
          list[i][field + 'span'] = 1
          list[i][field + 'dis'] = true
        } else {
          break
        }
      }
      k = i
    }
  }
  return list
}
```
原理： 利用`table>td`中的 `rowspan` 属性，给每一条数据的每个字段加上 `[field]span` 和 `[field]dis`
分别表示 td 的 `rowspan` 属性 和 `display` 属性