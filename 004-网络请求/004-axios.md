在实际开发中，我们经常需要进行网络请求，如果使用原生提供的方法，会存在如下问题：

1. 需要对原生的XHR或fetch进行封装，以方便使用
2. 在浏览器中可以使用XHR或fetch，但是在node环境中只能使用http模块来发送网络请求
3. 某些情况下，我们需要拦截对应的请求和响应操作，以完成对应的功能

所以在实际开发中，我们更推荐的是使用第三方库来进行网络请求，这个库就是[axios](https://github.com/axios/axios)

axios在浏览器会使用XHR来发送网络请求，在node端会使用http模块发送对应的网络请求

无论是在浏览器端还是node端，其api都是一致的，也就是说axios帮我们屏蔽了宿主环境的差异

axios不仅支持Promise风格API，而且对原生的XHR或fetch进行了扩展，如axios可以设置拦截器监听对应的请求和响应

以方便我们执行对应的自定义操作



## 基本使用

axios支持多种请求方式

+ axios(config) --- 本质就是axios.request(config) 
+ axios.request(config) 
+ axios.get(url[, config]) 
+ axios.delete(url[, config]) 
+ axios.head(url[, config]) 
+ axios.post(url[, data[, config]]) 
+ axios.put(url[, data[, config]]) 
+ axios.patch(url[, data[, config]])
+ axios.all([]) --- 本质就是Promise.all()



### get请求

```js
// 方式一 --- 不配置method 默认情况下 使用的是get请求
const res = await axios.request({
  url: 'http://www.example.com/lyric?id=500665346'
})

// axios返回的响应对象是axios封装的对象，其中包括对应的响应体，配置项，状态码 ...
// 如果我们需要获取到实际服务器返回的数据，需要通过其中的data属性
console.log(res.data)
```

```js
// axios.get(url[, config])
// 方式二 - query参数直接跟在url后边
await axios.get('http://www.example.com/lyric?id=500665346')
```

```js
// 方式三 - query参数写在config配置项中
await axios.get('http://www.example.com/lyric', {
  // 使用params接收query参数
  params: {
    id: 500665346
  }
})
```



### post请求

```js
// axios.post(url[, data][, config])
// 当使用axios.post方法的时候，axios会自动将method配置项的值修改为post
// 参数一 - url地址
// 参数二- 配置对象
await axios.post('http://123.207.32.32:1888/02_param/postjson', {
  // 使用data定义post方法需要传递的参数
  data: {
    id: 500665346
  }
})
```

```js
// axios.post也支持以如下方式进行参数的传递
// 1. 参数一 - url地址
// 2. 参数二 - 需要传递的data参数
// 3. 参数三 - 配置对象
await axios.post('http://123.207.32.32:1888/02_param/postjson', {
  id: 500665346
})
```



## 常见配置项

| 配置项                 | 示例                                          |
| ---------------------- | --------------------------------------------- |
| 请求地址               | url: '/user'                                  |
| 请求类型 - 默认值是get | method: 'get'                                 |
| 基准路径               | baseURL: 'http://www.example.com/api'         |
| 请求前的数据处理       | transformRequest:[function(data){}]           |
| 请求后的数据处理       | transformResponse: [function(data){}]         |
| 自定义请求头           | headers:{'x-Requested-With':'XMLHttpRequest'} |
| URL查询对象            | params:{ id: 12 }                             |
| request body           | data: { key: 'aa'}                            |
| 超时设置               | timeout: 1000                                 |

