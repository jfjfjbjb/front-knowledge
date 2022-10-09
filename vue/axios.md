
# 目录
- [简介](#简介)
- [特点](#特点)
- [用法](#用法)
- [基础API](#基础api)
  - [1. get](#1-get)
  - [2. post](#2-post)
  - [3. 执行多个并发](#3-执行多个并发)
  - [4. config](#4-config)
  - [5. 配置默认值default](#5-配置默认值default)
  - [6. 拦截器](#6-拦截器)
- [封装](#封装)
<br/><br/>

# 简介
## axios就是一个基于Promise的，发送http请求的一个工具库。
<br/><br/>

# 特点
+ 从浏览器中创建 XMLHttpRequests
+ 从 node.js 创建 http 请求
+ 支持 Promise API
+ 拦截请求和响应
+ 转换请求数据和响应数据
+ 取消请求
+ 自动转换 JSON 数据
+ 客户端支持防御 XSRF
<br/><br/>

# 用法
## 1.安装axios模块
```js
npm install --save axios
```
## 2.在index.js文件中引入axios
```js
import axios from 'axios'
new Vue({
  // el: '#app',
  // router,
  axios  // 加入axios
})
```
<br/><br/>

# 基础API
## 1. get
执行get请求，有两种方式

// 第一种方式  将参数直接写在url中
```js
axios.get('/getMainInfo?id=123')
.then((res) => {
  console.log(res)
})
.catch((err) => {
  console.log(err)
})
```

// 第二种方式  将参数直接写在params中
```js
axios.get('/getMainInfo', {
  params: {
    id: 123
  }
})
.then((res) => {
  console.log(res)
})
.catch((err) => {
  console.log(err)
})
```

## 2. post

执行post请求，注意执行post请求的入参，不需要写在params字段中，这个地方要注意与get请求的第二种方式进行区别。
```js
axios.post('/getMainInfo', {
  id: 123
})
.then((res) => {
  console.log(res)
})
.catch((err) => {
  console.log(err)
})
```

## 3. 执行多个并发
axios.all()和Promise.all()执行机制是一样的，要么全部成功走then，要么就走catch

```js
function getUserName() {
  return axios.get('/getUseName?id=123')
}
function getUserAge() {
  return axios.get('getUserAge?id=123')
}
axios.all([getUserName(), getUserAge()]) .then(
  axios.spread(function(acct, perms) {  // 处理并发请求的助手函数
    console.log(acct, perms)  // 全部请求成功
  })).catch(err => {
  console.log(err)  // 只要任意一个请求出错
})
```

## 4. config

这些是创建请求时可以用的配置选项。只有 url 是必需的。如果没有指定 method，请求将默认使用 get 方法。

### 下面是所有配置字段
```js
{
   // `url` 是用于请求的服务器 URL
  url: '/user',

  // `method` 是创建请求时使用的方法
  method: 'get', // default

  // `baseURL` 将自动加在 `url` 前面，除非 `url` 是一个绝对 URL。
  // 它可以通过设置一个 `baseURL` 便于为 axios 实例的方法传递相对 URL
  baseURL: 'https://some-domain.com/api/',

  // `transformRequest` 允许在向服务器发送前，修改请求数据
  // 只能用在 'PUT', 'POST' 和 'PATCH' 这几个请求方法
  // 后面数组中的函数必须返回一个字符串，或 ArrayBuffer，或 Stream
  transformRequest: [function (data, headers) {
    // 对 data 进行任意转换处理
    return data;
  }],

  // `transformResponse` 在传递给 then/catch 前，允许修改响应数据
  transformResponse: [function (data) {
    // 对 data 进行任意转换处理
    return data;
  }],

  // `headers` 是即将被发送的自定义请求头
  headers: {'X-Requested-With': 'XMLHttpRequest'},

  // `params` 是即将与请求一起发送的 URL 参数
  // 必须是一个无格式对象(plain object)或 URLSearchParams 对象
  params: {
    ID: 12345
  },


  // `data` 是作为请求主体被发送的数据
  // 只适用于这些请求方法 'PUT', 'POST', 和 'PATCH'
  // 在没有设置 `transformRequest` 时，必须是以下类型之一：
  // - string, plain object, ArrayBuffer, ArrayBufferView, URLSearchParams
  // - 浏览器专属：FormData, File, Blob
  // - Node 专属： Stream
  data: {
    firstName: 'Fred'
  },

  // 'proxy' 定义代理服务器的主机名称和端口
  // `auth` 表示 HTTP 基础验证应当用于连接代理，并提供凭据
  // 这将会设置一个 `Proxy-Authorization` 头，覆写掉已有的通过使用 `header` 设置的自定义 `Proxy-Authorization` 头。
  proxy: {
    host: '127.0.0.1',
    port: 9000,
    auth: {
      username: 'mikeymike',
      password: 'rapunz3l'
    }
  },

  // `timeout` 指定请求超时的毫秒数(0 表示无超时时间)
  // 如果请求话费了超过 `timeout` 的时间，请求将被中断
  timeout: 1000,

   // `withCredentials` 表示跨域请求时是否需要使用凭证
  withCredentials: false, // default

  // `paramsSerializer` 是一个负责 `params` 序列化的函数
  // (e.g. https://www.npmjs.com/package/qs, http://api.jquery.com/jquery.param/)
  paramsSerializer: function(params) {
    return Qs.stringify(params, {arrayFormat: 'brackets'})
  },

  // `adapter` 允许自定义处理请求，以使测试更轻松
  // 返回一个 promise 并应用一个有效的响应 (查阅 [response docs](#response-api)).
  adapter: function (config) {
    /* ... */
  },

 // `auth` 表示应该使用 HTTP 基础验证，并提供凭据
  // 这将设置一个 `Authorization` 头，覆写掉现有的任意使用 `headers` 设置的自定义 `Authorization`头
  auth: {
    username: 'janedoe',
    password: 's00pers3cret'
  },

   // `responseType` 表示服务器响应的数据类型，可以是 'arraybuffer', 'blob', 'document', 'json', 'text', 'stream'
  responseType: 'json', // default

  // `responseEncoding` indicates encoding to use for decoding responses
  // Note: Ignored for `responseType` of 'stream' or client-side requests
  responseEncoding: 'utf8', // default

   // `xsrfCookieName` 是用作 xsrf token 的值的cookie的名称
  xsrfCookieName: 'XSRF-TOKEN', // default

  // `xsrfHeaderName` is the name of the http header that carries the xsrf token value
  xsrfHeaderName: 'X-XSRF-TOKEN', // default

   // `onUploadProgress` 允许为上传处理进度事件
  onUploadProgress: function (progressEvent) {
    // Do whatever you want with the native progress event
  },

  // `onDownloadProgress` 允许为下载处理进度事件
  onDownloadProgress: function (progressEvent) {
    // 对原生进度事件的处理
  },

   // `maxContentLength` 定义允许的响应内容的最大尺寸
  maxContentLength: 2000,

  // `validateStatus` 定义对于给定的HTTP 响应状态码是 resolve 或 reject  promise 。如果 `validateStatus` 返回 `true` (或者设置为 `null` 或 `undefined`)，promise 将被 resolve; 否则，promise 将被 rejecte
  validateStatus: function (status) {
    return status >= 200 && status < 300; // default
  },

  // `maxRedirects` 定义在 node.js 中 follow 的最大重定向数目
  // 如果设置为0，将不会 follow 任何重定向
  maxRedirects: 5, // default

  // `socketPath` defines a UNIX Socket to be used in node.js.
  // e.g. '/var/run/docker.sock' to send requests to the docker daemon.
  // Only either `socketPath` or `proxy` can be specified.
  // If both are specified, `socketPath` is used.
  socketPath: null, // default

  // `httpAgent` 和 `httpsAgent` 分别在 node.js 中用于定义在执行 http 和 https 时使用的自定义代理。允许像这样配置选项：
  // `keepAlive` 默认没有启用
  httpAgent: new http.Agent({ keepAlive: true }),
  httpsAgent: new https.Agent({ keepAlive: true }),

  // `cancelToken` 指定用于取消请求的 cancel token
  // （查看后面的 Cancellation 这节了解更多）
  cancelToken: new CancelToken(function (cancel) {
  })
}
```

### (1)下面是常用的配置字段：
+ url：请求路径，string类型
+ method：请求方法，string类型
+ baseURL：baseURL会自动加在url前面，除非url是一个绝对URL，string类型
+ transformRequest：允许在向服务器发送请求之前，修改请求数据，只适用于post，put，patch请求，数组类型，数组里面的最后一个函数必须返回一个字符串
```js
[fucntion(data) {
  // 对data进行更改
  return data
}]
```
+ transformResponse：这里提前处理返回的数据
+ headers：自定义请求头
+ params：即将于请求一起发送的url参数（一般只用于get请求）**一般是对象类型**
+ data：作为请求主体被发送是数据（一般只用于post请求）**一般是对象类型**
+ timeout：超时时间，超过时间，请求将被中断
+ withCredentials：表示跨域请求时是否需要使用凭证
+ responseType：响应数据的类型，默认是'json'，可以是'text', 'json','document','arraybuffer','blob','stream'
+ maxContentLength：响应数据的最大尺寸
+ proxy：代理服务器主机名称和端口
```js
proxy: {
  host: '127.0.0.1',
  port: 9000,
  auth: : {
    username: 'mikeymike',
    password: 'rapunz3l'
  }
},
```
### (2)下面是响应的数据接口
```js
{
  // `data` 由服务器提供的响应
  data: {},

  // `status` 来自服务器响应的 HTTP 状态码
  status: 200,

  // `statusText` 来自服务器响应的 HTTP 状态信息
  statusText: 'OK',

  // `headers` 服务器响应的头
  headers: {},

  // `config` 是为请求提供的配置信息
  config: {},

  // 'request'
  //是生成此响应的请求
  //它是node.js中的最后一个ClientRequest实例（在重定向中）
  //和浏览器的XMLHttpRequest实例
  request: {}
}
```

(3)举例
// 发送 POST 请求
```js
axios({
  method: 'post',
  url: '/getMainInfo',
  data: {
    id: 123
  }
}).then(res => {
  console.log(res.data)
  console.log(res.status)
  console.log(res.statusText)
  console.log(res.headers)
  console.log(res.config)
}).catch(err => {
  console.log(err)
})
```

## 5. 配置默认值default

你可以指定将被用在各个请求的配置默认值

全局的 axios 默认值
```js
axios.defaults.baseURL = 'https://api.example.com';
axios.defaults.headers.common['Authorization'] = AUTH_TOKEN;
axios.defaults.headers.post['Content-Type'] = 'application/x-www-form-urlencoded';
```
自定义实例默认值
```js
// Set config defaults when creating the instance
const instance = axios.create({
  baseURL: 'https://api.example.com'
});

// Alter defaults after instance has been created
instance.defaults.headers.common['Authorization'] = AUTH_TOKEN;
```

## 6. 拦截器
(1)在请求之前拦截请求
```js
// 添加请求拦截器
axios.interceptors.request.use(
  function (config) {
    // 在发送请求之前做些什么
    return config;
  }, 
  function (error) {
    // 对请求错误做些什么
    return Promise.reject(error);
  }
)
```
(2)在被then，catch处理前拦截响应
```js
// 添加响应拦截器
axios.interceptors.response.use(
  function (response) {
  // 对响应数据做点什么
    return response;
  }, 
  function (error) {
  // 对响应错误做点什么
    return Promise.reject(error);
  }
);
```

(3)移除拦截器
```js
const myInterceptor = axios.interceptors.request.use(function () {/*...*/});
axios.interceptors.request.eject(myInterceptor);
```

(4)可以为自定义 axios 实例添加拦截器
```js
var instance = axios.create([config]);
instance.interceptors.request.use(function () {/*...*/});
```

# 封装
在开发中，总会有很多的http请求，封装好一个axios，使用更方便

(1)封装
```js
// request.js文件
import axios from 'axios'
import qs from 'qs'
// 引入基础参数文件  和  baseURL配置文件
import baseParams from './baseParams'  // 基本参数，比如一些登录信息之类的参数
import { BASE_URL } from './config'   // baseURL写在config.js文件中
// 默认的全局配置
axios.defaults.baseURL = BASE_URL
axios.defaults.timeout = 10000
// 请求发送之前拦截，进行处理（根据业务需求进行拦截处理）
axios.interceptors.request.use(
  success => {
    return success
  }, error => {
    return reject(error)
  }
)
// then,catch处理之前，进行拦截处理（根据业务需求进行拦截处理）
axios.interceptors.response.use(
  response => {
    return response
  }, error => {
    return Promise.reject(error)
  }
)
// 导出post请求
export function post (url, data, withBaseParams = false) {
  return new Promise((resolve, reject) => {
    axios({
      method: 'post',
      url,
      data: withBaseParams ? qs.stringify({...baseParams, data}) : qs.stringify(data),
      headers: {
        'Content-Type': 'application/x-www-form-urlencoded; charset=UTF-8'
      }
    })
      .then(res => successHandle(res, resolve))   // 将数据处理之后传给页面
      .catch(err => errorHandle(err, reject))
  })
}
// 导出get请求
export function get (url, params) {
  return new Promise((resolve, reject) => {
    axios({
      method: 'get',
      url,
      params,
      headers: {}
    })
      .then(res => successHandle(res, resolve))
      .catch(err => errorHandle(err, reject))
  })
}
// then处理执行了successHandle
function successHandle(data, resolve) {
  if (res.success && res.errorCode == '60000') {
    resolve(res)
  } else {
    // 弹出toast报错
    Toast({
      message: res.msg,
      duration: 2000
    })
  }
}
// catch处理执行了errorHandle
function errorHandle(err, reject) {
  reject(err)
}
```
(2)引入使用
```js
// 引入使用
import {get, post} from 'request.js'
post('/getMainInfo', {id: 123}, true)
.then(res => {
  console.log(res)
})
.catch(err => {
  console.log(err)  
})
```
