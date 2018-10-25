---
title: promise 
date: 2018-09-12 11:39:05
tags: [js, promise]
categories: javascript
---

```javascript
let fun = () => {
  return new Promise((resolve, reject) => {
    resolve(data)
    if (success) {
      resolve(data) // success resolve
    } else {
      reject(error) // error  reject
    }
  })
}
```

```javascript
let result = await fun()
```

