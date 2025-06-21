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



