---
title: react hooks模拟类组件的生命周期函数
date: 2019-11-02 19:24
tags: [react, hoots]
categories: react
---

Hook 是 React 16.8 的新增特性。它可以让你在不编写 class 的情况下使用 state 以及其他的 React 特性。

#### 模拟整个生命周期中只运行一次的方法

```js
useMemo(() => {
  // execute only once
}, [])
```

#### 模拟 `shouldComponentUpdate`

```js
const areEqual = (prevProps, nextProps) => {
  // 返回结果和shouldComponentUpdate正好相反
  // 访问不了state
}
React.memo(Foo, areEqual)
```

#### 模拟 `componentDidMount`

```js
useEffect(() => {
  // 这里在mount时执行一次
}, [])
```

#### 模拟 `componentDidUnmount`

```js
useEffect(() => {
  // 这里在mount时执行一次
  return () => {
    // 这里在unmount时执行一次
  }
}, [])
```

#### 模拟 `componentDidUpdate`

```js
const mounted = useRef()
useEffect(() => {
  if (!mounted.current) {
    mounted.current = true
  } else {
    // 这里只在update是执行
  }
})
```
