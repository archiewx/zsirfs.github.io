---
layout: 'po:st'
title: ajax之axios的使用
date: 2018-01-24 13:59:07
tags:
  - axios
  - ajax
  - http
  - javascript
categories:
  - javascript
  - http
---

> axios 是一个 Promise 的 ajax 库，同时支持浏览器和 nodejs 服务器，使用 axios 可以轻松的发送 GET/POST 请求,同时可以附加很多额外的功能，比如拦截器, 认证，代理，状态校验等

<!-- more -->

## 安装

> axios 的[GitHub 库](https://github.com/axios/axios)的地址,[点击这里](https://github.com/axios/axios)

使用 cnpm/npm/yarn 安装命令

```bash
$ npm install axios # 或者 cnpm install axios
```

兼容性: 
[](/ajax之axios的使用/compatibility.png)

## 使用

### 首先查看 API

```javascript
axios.request(config)
axios.get(url[, config])
axios.delete(url[, config])
axios.head(url[, config])
axios.options(url[, config])
axios.post(url[, data[, config]])
axios.put(url[, data[, config]])
axios.patch(url[, data[, config]])
```

除了`request`方法外，其余第二个阐述均为`config`

### config 对象说明

发送一个请求，在 config 中写出请求体, `config` 内容是:

```js
{
  // 请求地址
  url: '/api/xxx',
  // 请求方法, 默认是Get
  method: 'get',
  // baseURL就是把这个url添加到url之前，最终的请求url为: url + baseURL, 可以在axios.default.baseURL='http://xxxx'设置
  baseURL: 'https://some-domain.com/api/',
  // 这个是用在请求体可以进行转换，接受一个数组，数组中每个值都是一个回调函数,只有在PUT, POST, PATCH中使用
  transformRequest: [function (data, headers) {
    // 其他操作
    return data;
  }],
  // 返回时转换返回体转换, 使用then/catch 会把这个data回调传递
  transformResponse: [function (data) {
    // 其他操作
    return data;
  }],
  // 请求头
  headers: {'X-Requested-With': 'XMLHttpRequest'},
  // 请求的数据，params 必须是一个自然的对象
  params: {
    ID: 12345
  },
  // `paramsSerializer` is an optional function in charge of serializing `params`
  // (e.g. https://www.npmjs.com/package/qs, http://api.jquery.com/jquery.param/)
  // 请求参数序列化
  paramsSerializer: function(params) {
    return Qs.stringify(params, {arrayFormat: 'brackets'})
  },

  // Only applicable for request methods 'PUT', 'POST', and 'PATCH'
  // - string, plain object, ArrayBuffer, ArrayBufferView, URLSearchParams
  // - Browser only: FormData, File, Blob
  // - Node only: Stream, Buffer
  // 在PUT/POST/PATCH方式下，传递的请求数据
  data: {
    firstName: 'Fred'
  },
  // 如果超过1000 没有响应则请求超时
  timeout: 1000,
  // `withCredentials` indicates whether or not cross-site Access-Control requests
  // should be made using credentials
  withCredentials: false, // default
  // `adapter` allows custom handling of requests which makes testing easier.
  // Return a promise and supply a valid response (see lib/adapters/README.md).
  adapter: function (config) {
    /* ... */
  },

  // `auth` indicates that HTTP Basic auth should be used, and supplies credentials.
  // This will set an `Authorization` header, overwriting any existing
  // `Authorization` custom headers you have set using `headers`.
  auth: {
    username: 'janedoe',
    password: 's00pers3cret'
  },
  // `responseType` indicates the type of data that the server will respond with
  // options are 'arraybuffer', 'blob', 'document', 'json', 'text', 'stream'
  responseType: 'json', // default
  // `xsrfCookieName` is the name of the cookie to use as a value for xsrf token
  xsrfCookieName: 'XSRF-TOKEN', // default
  // `xsrfHeaderName` is the name of the http header that carries the xsrf token value
  xsrfHeaderName: 'X-XSRF-TOKEN', // default
  // `onUploadProgress` allows handling of progress events for uploads
  // 上传进度回调
  onUploadProgress: function (progressEvent) {
    // Do whatever you want with the native progress event
  },
  // `onDownloadProgress` allows handling of progress events for downloads
  // 下载进度回调
  onDownloadProgress: function (progressEvent) {
    // Do whatever you want with the native progress event
  },
  // `maxContentLength` defines the max size of the http response content allowed
  // 最大响应内容长度
  maxContentLength: 2000,
  // `validateStatus` defines whether to resolve or reject the promise for a given
  // HTTP response status code. If `validateStatus` returns `true` (or is set to `null`
  // or `undefined`), the promise will be resolved; otherwise, the promise will be
  // rejected.
  // 校验请求状态码 大于200 小于300 在Promise中使用then否则就catch
  validateStatus: function (status) {
    return status >= 200 && status < 300; // default
  },
  // `maxRedirects` defines the maximum number of redirects to follow in node.js.
  // If set to 0, no redirects will be followed.
  maxRedirects: 5, // default
  // `httpAgent` and `httpsAgent` define a custom agent to be used when performing http
  // and https requests, respectively, in node.js. This allows options to be added like
  // `keepAlive` that are not enabled by default.
  httpAgent: new http.Agent({ keepAlive: true }),
  httpsAgent: new https.Agent({ keepAlive: true }),

  // 'proxy' defines the hostname and port of the proxy server
  // Use `false` to disable proxies, ignoring environment variables.
  // `auth` indicates that HTTP Basic auth should be used to connect to the proxy, and
  // supplies credentials.
  // This will set an `Proxy-Authorization` header, overwriting any existing
  // `Proxy-Authorization` custom headers you have set using `headers`.
  // 代理配置
  proxy: {
    host: '127.0.0.1',
    port: 9000,
    auth: {
      username: 'mikeymike',
      password: 'rapunz3l'
    }
  },
  // `cancelToken` specifies a cancel token that can be used to cancel the request
  // (see Cancellation section below for details)
  cancelToken: new CancelToken(function (cancel) {
  })
}
```

### responseSchema 对象

```js
{
  // 服务器返回的数据
  data: {},
  // 状态码
  status: 200,
  // 返回的状态文本
  statusText: 'OK',
  // 服务器返回头， 所有的返回头都是小写字母
  headers: {},
  // 配置对象, 请求的config
  config: {},
  // nodejs环境下的ClientRequest对象，浏览器环境下的XMLHttpRequest对象
  request: {}
}
```

### 发一个 GET、POST 请求

一般情况下我们最常用的就是请求数据(GET), 保存/更新数据(POST), 上传文件(POST/设置头部), 下面使用两个示例来说明:

#### GET 请求:

```js
import axiso from 'axios'
axios
  .get('http://xxxx/v1/query', {
    params: {
      // ...
    }
  })
  .then(config => {
    // 这里返回的事responseSchema对象
    return config.data
  })
  .then(res => {
    // 这里才是返回体
  })
```

[](/ajax之axios的使用/get.png)

#### POST 请求:

```js
import axios from 'axios'
axois
  .post('http://xxxx/xxx', {
    type: 'sb'
  })
  .then(config => {
    // responseSchema对象
    return config.data
  })
  .then(res => {
    // 返回的数据
  })
```

[](/ajax之axios的使用/post.png)

## 请求/返回拦截器使用

axios 可以使用拦截器，对请求和返回拦截，定义多个拦截器将依次执行, 拦截器都挂载在`axios.interceptors`上

```js
// 下面的都是默认的拦截器配置
// Add a request interceptor
axios.interceptors.request.use(
  function(config) {
    // Do something before request is sent
    return config
  },
  function(error) {
    // Do something with request error
    return Promise.reject(error)
  }
)

// Add a response interceptor
axios.interceptors.response.use(
  function(response) {
    // Do something with response data
    return response
  },
  function(error) {
    // Do something with response error
    return Promise.reject(error)
  }
)
```

## axios 配置

### 全局配置

```js
axios.defaults.baseURL = 'https://api.example.com' // 定义访问的基础url
axios.defaults.headers.common['Authorization'] = AUTH_TOKEN // 认证token
axios.defaults.headers.post['Content-Type'] = 'application/x-www-form-urlencoded' // 请求类型
```

### 自定义配置

自定义配置的话需要构建一个 axios 实例:

```js
// Set config defaults when creating the instance
var instance = axios.create({
  baseURL: 'https://api.example.com'
})
// Alter defaults after instance has been created
instance.defaults.headers.common['Authorization'] = AUTH_TOKEN
```

配置的优先级，按照js执行顺序，后来的配置覆盖前面的配置

## 总结

axios 是目前最好的ajax库之一，如果兼容IE9以上选择axios作为请求库是第一选择的，作为前端开发者，如果你发现的请求正确，传值正确，按照API文档操作正确，那就去(dui)后端(辣��后端)
