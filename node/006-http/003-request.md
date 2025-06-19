```js
import http from 'node:http'

// 原则上，可以创建多个web服务，监听不同端口，提供不同服务
// 即创建多个 server实例，并监听不同的端口
const server = http.createServer((req, res) => {
  res.setHeader('Content-Type', 'text/html; charset=utf-8')

  // 请求方法
  console.log(req.method) // GET

  // 请求路径
  console.log(req.url) // / 或 /profile 等

  // 请求头 「 是一个对象 」
  console.log(req.headers)

  // 调用end方法，向客户端响应内容 「 否则页面会因为得不到响应，一直处于加载状态 」
  res.end('你好~')
})

server.listen(3000)
```



## 示例

```js
import http from 'node:http'

const server = http.createServer((req, res) => {
  res.setHeader('Content-Type', 'text/html; charset=utf-8')

  const method = req.method
  const url = req.url

  if (url === '/') {
    if (method === 'GET') {
      res.end('你好~')
    } else {
      // 设置响应码
      res.statusCode = 405
      res.end('不支持的请求方法')
    }
  } else if (url === '/users') {
    if (method === 'GET') {
      res.end('用户列表')
    } else {
      res.statusCode = 405
      res.end('不支持的请求方法')
    }
  }
})

server.listen(3000)
```



### 常见响应码

+ `2xx` => 成功
+ `3xx` => 重定向
+ `4xx` => 客户端错误
+ `5xx` => 服务器错误

| 状态码 | 描述                  | 说明                                                         |
| ------ | --------------------- | ------------------------------------------------------------ |
| 200    | OK                    | 请求成功，服务器返回所请求的数据。                           |
| 201    | Created               | 请求成功且资源已创建。                                       |
| 204    | No Content            | 请求成功，但响应不包含内容。                                 |
|        |                       |                                                              |
| 301    | Moved Permanently     | 资源已永久移动到新URL。<br />更新用户代理缓存地址<br />请求新URL时，请求方式可以发生改变 |
| 302    | Found                 | 资源临时移动，客户端应使用新URL。<br />不更新用户代理缓存地址，下次还是请求源地址<br />因为资源可能被转移回来了<br />请求新URL时，请求方式可以发生改变 |
| 307    | Temporary Redirect    | 临时重定向<br />请求新URL时，请求方式不能改变<br />用于替代 状态码 302 |
| 308    | Permanent Redirect    | 永久重定向<br />请求新URL时，请求方式不能改变<br />用于替代 状态码 301 |
| 304    | Not Modified          | 资源未更改，客户端可使用缓存。                               |
|        |                       |                                                              |
| 400    | Bad Request           | 请求语法错误，服务器无法理解。                               |
| 401    | Unauthorized          | 请求需要用户认证。                                           |
| 403    | Forbidden             | 服务器拒绝请求，客户端无权限。                               |
| 404    | Not Found             | 服务器找不到请求的资源。                                     |
| 405    | Method Not Allowwed   | 请求方法（如GET、POST）不被服务器允许。                      |
| 429    | Too Many Requests     | 客户端发送过多请求，超出限额。                               |
|        |                       |                                                              |
| 500    | Internal Server Error | 服务器内部错误，无法完成请求。                               |
| 502    | Bad Gateway           | 作为网关或代理的服务器收到无效响应。                         |
| 503    | Service Unavailable   | 服务器暂时无法处理请求（如维护或过载）。                     |
| 504    | Gateway Timeout       | 网关或代理服务器未及时收到响应。                             |

