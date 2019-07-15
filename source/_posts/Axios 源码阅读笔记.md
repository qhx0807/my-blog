---
title: Axios 源码阅读解析
date: 2019-06-21 09:36:00
tags: [axios]
categories: 源码阅读
---

> Axios 是一个基于 promise 的 HTTP 库，可以用在浏览器和 node.js 中。

Axios 的主要特性包括：

- 从浏览器中创建 [XMLHttpRequest](https://developer.mozilla.org/en-US/docs/Web/API/XMLHttpRequest)
- 从 node.js 创建 [http](https://nodejs.org/api/http.html) 请求
- 支持 [Promise](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/Promise) Api
- 拦截请求和响应
- 转换请求数据和响应数据
- 取消请求
- 自动转换 JSON 数据
- 客户端支持防御 [XSRF](https://en.wikipedia.org/wiki/Cross-site_request_forgery)

`/lib` 下目录结构
```
├── /adapters/
│  ├── http.js
│  ├── README.md
│  └── xhr.js
├── axios.js
├── /cancel/
│  ├── Cancel.js
│  ├── CancelToken.js
│  └── isCancel.js
├── /core/
│  ├── Axios.js
│  ├── createError.js
│  ├── dispatchRequest.js
│  ├── enhanceError.js
│  ├── InterceptorManager.js
│  ├── mergeConfig.js
│  ├── README.md
│  ├── settle.js
│  └── transformData.js
├── defaults.js
├── /helpers/
│  ├── bind.js
│  ├── buildURL.js
│  ├── combineURLs.js
│  ├── cookies.js
│  ├── deprecatedMethod.js
│  ├── isAbsoluteURL.js
│  ├── isURLSameOrigin.js
│  ├── normalizeHeaderName.js
│  ├── parseHeaders.js
│  ├── README.md
│  └── spread.js
└── utils.js
```
#### 工具方法

先看 `/lib/utils.js` 工具方法

```javascript
module.exports = {
  isArray: isArray,
  isArrayBuffer: isArrayBuffer,
  isBuffer: isBuffer,
  isFormData: isFormData,
  isArrayBufferView: isArrayBufferView,
  isString: isString,
  isNumber: isNumber,
  isObject: isObject,
  isUndefined: isUndefined,
  isDate: isDate,
  isFile: isFile,
  isBlob: isBlob,
  isFunction: isFunction,
  isStream: isStream,
  isURLSearchParams: isURLSearchParams,
  isStandardBrowserEnv: isStandardBrowserEnv,
  forEach: forEach,
  merge: merge,
  deepMerge: deepMerge,
  extend: extend,
  trim: trim
}
```
is 开头的方法都是判断是否为XX类型，重点看后面几个函数：

- `forEach` 遍历数组或对象

```javascript
/**
 * @param obj {Object|Array} 
 * @param fn {Function}
 */
function forEach(obj, fn) {
  // 判读obj是否为空
  if (obj === null || typeof obj === 'undefined') return

  // 如果第一个参数不是对象类型，则转换为[obj]数组
  if (typeof obj !== 'object') obj = [obj]

  if (isArray(obj)) {
    // 数组: 循环遍历 fn(item, index, arr)
    for (var i = 0, l = obj.length; i < l; i++) {
      fn.call(null, obj[i], i, obj)
    }
  } else {
    // 对象: 循环对象 fn(value, key, obj)
    // Iterate over object keys
    for (var key in obj) {
      if (Object.prototype.hasOwnProperty.call(obj, key)) {
        fn.call(null, obj[key], key, obj);
      }
    }
  }
}
```

- `merge` 合并对象 类似 `Object.assign()`

```javascript
/**
* @param {Object} 要合并的对象
* @returns {Object} 返回合并后的结果
*/
function merge() {
  var result = {}
  function assignValue(val, key) {
    // 递归合并子对象
    if (typeof result[key] === 'object' && typeof val === 'object') {
      result[key] = merge(result[key], val)
    } else {
      result[key] = val
    }
  }
  // 循环 arguments, 调用 assignValue
  for (var i = 0, l = arguments.length; i < l; i++) {
    // assignValue 的参数将会是 forEach 中的 value, key
    forEach(arguments[i], assignValue)
  }
  return result
}
```

- `deepMerge` 深度合并

```javascript
function deepMerge() {
  var result = {}
  function assignValue(val, key) {
    if (typeof result[key] === 'object' && typeof val === 'object') {
      result[key] = deepMerge(result[key], val)
    } else if (typeof val === 'object') {
      result[key] = deepMerge({}, val)
    } else {
      result[key] = val
    }
  }

  for (var i = 0, l = arguments.length; i < l; i++) {
    forEach(arguments[i], assignValue)
  }
  return result
}
```

- `extend(a, b, thisArg)` 将 b 里面的属性和方法继承给 a , 并且将 b 里面的方法的执行上个下文都绑定到 thisArg

```javascript
function extend(a, b, thisArg) {
  forEach(b, function assignValue(val, key) {
    if (thisArg && typeof val === 'function') {
      // 如果val是函数
      a[key] = bind(val, thisArg);
    } else {
      a[key] = val;
    }
  });
  return a;
}
```
`bind` 方法是在 `/helpers/bind.js`, 给 fn 指定 this, 返回一个函数

```javascript
module.exports = function bind(fn, thisArg) {
  return function wrap() {
    // 好像直接传类数组也行，apply() 的第二个参数支持类数组
    var args = new Array(arguments.length)
    for (var i = 0; i < args.length; i++) {
      args[i] = arguments[i]
    }
    return fn.apply(thisArg, args)
  }
}
```

#### 再回过头来看 `/lib/axios.js`

```javascript
// 引入工具函数
var utils = require('./utils')
var bind = require('./helpers/bind')
var Axios = require('./core/Axios')
var mergeConfig = require('./core/mergeConfig')
var defaults = require('./defaults')

/**
 * 创建 Axios 实例
 *
 * @param {Object} defaultConfig 默认配置
 * @return {Axios}  返回Axios实例
 */
function createInstance(defaultConfig) {
  // 实例化 Axios '/core/Axios.js'
  var context = new Axios(defaultConfig)
  // 将 Axios.prototype.request 的执行上下文绑定到 context
  var instance = bind(Axios.prototype.request, context)

  // 将 Axios.prototype 上的所有方法的执行上下文绑定到 context , 并且继承给 instance
  utils.extend(instance, Axios.prototype, context)

  // 将 context 继承给 instance
  utils.extend(instance, context)

  return instance
}

// 创建默认的 instance
var axios = createInstance(defaults);

// 在axios上暴露 Axios
axios.Axios = Axios;

// Factory for creating new instances
axios.create = function create(instanceConfig) {
  return createInstance(mergeConfig(axios.defaults, instanceConfig));
};

// Expose Cancel & CancelToken
axios.Cancel = require('./cancel/Cancel');
axios.CancelToken = require('./cancel/CancelToken');
axios.isCancel = require('./cancel/isCancel');

// Expose all/spread
axios.all = function all(promises) {
  return Promise.all(promises);
};
axios.spread = require('./helpers/spread');

module.exports = axios

```

#### `/lib/Axios.js`

```js
// 工具函数
var utils = require('./../utils')
// buildURL(url, params) 返回一个带序列化参数的url
var buildURL = require('../helpers/buildURL')
// 拦截器相关
var InterceptorManager = require('./InterceptorManager')
// adapter 创建请求，返回promise
var dispatchRequest = require('./dispatchRequest')
// 合并config
var mergeConfig = require('./mergeConfig')

function Axios(instanceConfig) {
  this.defaults = instanceConfig
  // 拦截器
  /**
   * 添加请求拦截器
   * axios.interceptors.request.use(function (config) {
   *   // 在发送请求之前做些什么
   *   return config
   *}, function (error) {
   *  // 对请求错误做些什么
   *  return Promise.reject(error)
   *});
  */
  this.interceptors = {
    // 请求拦截器
    request: new InterceptorManager(),
    // 响应拦截器
    response: new InterceptorManager()
  }
}

Axios.prototype.request = function request(config) {
  // Allow for axios('example/url'[, config]) a la fetch API
  // 这样就可以使用 axios('/api/userinfo', {id: 1}) 这种方式
  // arguments[1] 为第二个参数 config
  if (typeof config === 'string') {
    config = arguments[1] || {}
    config.url = arguments[0]
  } else {
    config = config || {}
  }

  config = mergeConfig(this.defaults, config)
  // method 默认为 'get'
  config.method = config.method ? config.method.toLowerCase() : 'get'

  // 拦截器中间件 数组
  var chain = [dispatchRequest, undefined]
  var promise = Promise.resolve(config)
  //将请求方法插入 chain 数组的前面 unshift(成功的, 失败的)
  this.interceptors.request.forEach(function unshiftRequestInterceptors(interceptor) {
    chain.unshift(interceptor.fulfilled, interceptor.rejected)
  })
  // 将请求方法插入 chain 数组的后面 push(成功的, 失败的)
  this.interceptors.response.forEach(function pushResponseInterceptors(interceptor) {
    chain.push(interceptor.fulfilled, interceptor.rejected)
  })
  // 加了请求拦截器和相应拦截器 chain
  // chain = [ 请求拦截器的成功方法, 请求拦截器的失败方法, dispatchRequest, undefined, 响应拦截器的成功方法, 响应拦截器的失败方法 ]。

  // 通过 shift 方法把第一个元素从其中删除，并返回第一个元素
  while (chain.length) {
    promise = promise.then(chain.shift(), chain.shift())
  }
  // 意思就是将 chainn 内的方法两两拿出来执行 成如下这样
  //  promise.then(请求拦截器的成功方法, 请求拦截器的失败方法)
  //         .then(dispatchRequest, undefined)
  //         .then(响应拦截器的成功方法, 响应拦截器的失败方法)

  return promise
}

Axios.prototype.getUri = function getUri(config) {
  // 合并配置
  config = mergeConfig(this.defaults, config)
  // 返回 url
  return buildURL(config.url, config.params, config.paramsSerializer).replace(/^\?/, '')
}

// Provide aliases for supported request methods
// 给 Axios.prototype 上面增加 delete,get,head,options 方法
// 我们就可以用 axios.get(...), axios.delete(...) 等方法
utils.forEach(['delete', 'get', 'head', 'options'], function forEachMethodNoData(method) {
  Axios.prototype[method] = function(url, config) {
    return this.request(utils.merge(config || {}, {
      method: method,
      url: url
    }))
  }
})
// post put patch 要传data
utils.forEach(['post', 'put', 'patch'], function forEachMethodWithData(method) {
  Axios.prototype[method] = function(url, data, config) {
    return this.request(utils.merge(config || {}, {
      method: method,
      url: url,
      data: data
    }))
  }
})

module.exports = Axios
```

#### 拦截器 `/lib/core/InterceptorManager.js`

```js
var utils = require('./../utils');
function InterceptorManager() {
  // 存放方法的数组
  this.handlers = []
}
// 添加拦截方法
InterceptorManager.prototype.use = function use(fulfilled, rejected) {
  this.handlers.push({
    fulfilled: fulfilled,
    rejected: rejected
  })
  return this.handlers.length - 1
}
// 移除拦截方法
// id 是上面use方法返回的
InterceptorManager.prototype.eject = function eject(id) {
  if (this.handlers[id]) {
    this.handlers[id] = null;
  }
}
// this.interceptors.response.forEach()
InterceptorManager.prototype.forEach = function forEach(fn) {
  utils.forEach(this.handlers, function forEachHandler(h) {
    if (h !== null) {
      fn(h)
    }
  })
}
module.exports = InterceptorManager
```

#### `/lib/core/dispatchRequest.js` 调用请求

```js
var utils = require('./../utils')
var transformData = require('./transformData')
var isCancel = require('../cancel/isCancel')
var defaults = require('../defaults')
var isAbsoluteURL = require('./../helpers/isAbsoluteURL')
var combineURLs = require('./../helpers/combineURLs')

// 返回 promise
module.exports = function dispatchRequest(config) {

  // 如果不是绝对地址 则前面加上baseUrl
  if (config.baseURL && !isAbsoluteURL(config.url)) {
    config.url = combineURLs(config.baseURL, config.url);
  }

  // headers 默认 {}
  config.headers = config.headers || {};

  // 转换请求数据
  config.data = transformData(
    config.data,
    config.headers,
    config.transformRequest
  )

  // 合并配置
  config.headers = utils.merge(
    config.headers.common || {},
    config.headers[config.method] || {},
    config.headers || {}
  )
  utils.forEach(
    ['delete', 'get', 'head', 'post', 'put', 'patch', 'common'],
    function cleanHeaderConfig(method) {
      delete config.headers[method]
    }
  )
  // 请求
  var adapter = config.adapter || defaults.adapter

  return adapter(config).then(function onAdapterResolution(response) {
    throwIfCancellationRequested(config)

    // 对返回的数据处理 再返回给用户
    response.data = transformData(
      response.data,
      response.headers,
      config.transformResponse
    );

    return response
  }, function onAdapterRejection(reason) {
    if (!isCancel(reason)) {
      throwIfCancellationRequested(config)

      // Transform response data
      if (reason && reason.response) {
        reason.response.data = transformData(
          reason.response.data,
          reason.response.headers,
          config.transformResponse
        );
      }
    }

    return Promise.reject(reason)
  })
}
```

#### `/lib/defaults.js` createInstance(defaults)传入的默认配置

```js
'use strict';
var utils = require('./utils')
var normalizeHeaderName = require('./helpers/normalizeHeaderName')

// 默认的 Content-Type
var DEFAULT_CONTENT_TYPE = {
  'Content-Type': 'application/x-www-form-urlencoded'
}

function setContentTypeIfUnset(headers, value) {
  if (!utils.isUndefined(headers) && utils.isUndefined(headers['Content-Type'])) {
    headers['Content-Type'] = value
  }
}

function getDefaultAdapter() {
  var adapter;
  // node 环境下 根据 process 判断是否为node环境 调用 './adapters/http.js'
  if (typeof process !== 'undefined' && Object.prototype.toString.call(process) === '[object process]') {
    adapter = require('./adapters/http');
  } else if (typeof XMLHttpRequest !== 'undefined') {
    // 浏览器环境下 调用 '././adapters/xhr.js'
    adapter = require('./adapters/xhr');
  }
  return adapter
}

// 整个默认配置
var defaults = {
  adapter: getDefaultAdapter(),

  transformRequest: [function transformRequest(data, headers) {
    normalizeHeaderName(headers, 'Accept');
    normalizeHeaderName(headers, 'Content-Type');
    if (utils.isFormData(data) ||
      utils.isArrayBuffer(data) ||
      utils.isBuffer(data) ||
      utils.isStream(data) ||
      utils.isFile(data) ||
      utils.isBlob(data)
    ) {
      return data;
    }
    if (utils.isArrayBufferView(data)) {
      return data.buffer;
    }
    if (utils.isURLSearchParams(data)) {
      setContentTypeIfUnset(headers, 'application/x-www-form-urlencoded;charset=utf-8');
      return data.toString();
    }
    if (utils.isObject(data)) {
      setContentTypeIfUnset(headers, 'application/json;charset=utf-8');
      return JSON.stringify(data);
    }
    return data;
  }],

  transformResponse: [function transformResponse(data) {
    if (typeof data === 'string') {
      try {
        data = JSON.parse(data);
      } catch (e) { /* Ignore */ }
    }
    return data
  }],

  // 超时时间
  timeout: 0,

  xsrfCookieName: 'XSRF-TOKEN',
  xsrfHeaderName: 'X-XSRF-TOKEN',

  maxContentLength: -1,

  validateStatus: function validateStatus(status) {
    return status >= 200 && status < 300;
  }
};

defaults.headers = {
  common: {
    'Accept': 'application/json, text/plain, */*'
  }
};

utils.forEach(['delete', 'get', 'head'], function forEachMethodNoData(method) {
  defaults.headers[method] = {};
})

utils.forEach(['post', 'put', 'patch'], function forEachMethodWithData(method) {
  defaults.headers[method] = utils.merge(DEFAULT_CONTENT_TYPE);
})

module.exports = defaults
```