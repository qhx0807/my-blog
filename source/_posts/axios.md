---
title: 使用axios笔记
date: 2017-06-07 22:28:11
tags: [javascript,vue,axios]
categories: javascript
---

vue2.0中官方推荐的HTTP请求插件：axios. [官方文档](https://github.com/mzabriskie/axios/)

##### 添加一个请求拦截器
```javascript
	axios.interceptors.request.use(function(config){
	  //在请求发出之前进行一些操作
	  //console.log(config)
	  return config;
	},function(error){
	  //Do something with request error
	  return Promise.reject(error);
	});
```

##### 添加一个响应拦截器
```javascript
	axios.interceptors.response.use(function(res){
	  	//在这里对返回的数据进行处理
	  	// if(res.data.name=="qhx"){
	  	// 	return 123;
	  	// }
	  	return res;
	},function(error){
	  	//Do something with response error
	  	return Promise.reject(error);
	})

```
<!-- more -->
##### 发送网络请求
```javascript
	axios({
		method:"post",
		headers: {'Content-Type': 'application/x-www-form-urlencoded'},
		url:"http://192.168.2.231:807/SwsWeb.ashx",
		data:{
			methodName:"p_costManage.GetHJForScheId",
            parameters:"2045",
            u_login:"admin",
            u_pass:"admin",
            login_key:"",
            no_login_key:"1",
		},
		transformRequest: [function (data) {
		    let ret = ''
		    for (let it in data) {
		      ret += encodeURIComponent(it) + '=' + encodeURIComponent(data[it]) + '&'
		    }
		    return ret
		 }],
	}).then(response => {
		console.log(response)
	})
	.catch(error => {
		console.log(error)
	})
```
值得注意的是：`transformRequest` 在官方文档中的描述：

```javascript
  // `transformRequest` allows changes to the request data before it is sent to the server
  // This is only applicable for request methods 'PUT', 'POST', and 'PATCH'
  // The last function in the array must return a string or an instance of Buffer, ArrayBuffer,
  // FormData or Stream
 transformRequest: [function (data) {
    // Do whatever you want to transform the data

    return data;
  }],

  // `transformResponse` allows changes to the response data to be made before
  // it is passed to then/catch
  transformResponse: [function (data) {
    // Do whatever you want to transform the data

    return data;
  }],

  // `data` is the data to be sent as the request body
  // Only applicable for request methods 'PUT', 'POST', and 'PATCH'
  // When no `transformRequest` is set, must be of one of the following types:
  // - string, plain object, ArrayBuffer, ArrayBufferView, URLSearchParams
  // - Browser only: FormData, File, Blob
  // - Node only: Stream, Buffer
  data: {
    firstName: 'Fred'
  },

```
`transformRequest` 允许在发送请求对 `data` 进行处理。
开始没理解到，后来明白了，必须要对 `data` 进行 `encodeURIComponent()`
