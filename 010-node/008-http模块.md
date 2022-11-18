当应用程序(客户端)需要某一个资源时，可以向一台服务器，通过Http请求获取到这个资源，

提供资源的这个服务器，就是一个Web服务器

![image.png](https://s2.loli.net/2022/11/17/XrYhkSCpzgP47Mb.png) 

目前有很多开源的Web服务器:Nginx、Apache(静态)、Apache Tomcat(静态、动态)、Node.js

在Node中，提供web服务器的资源返回给浏览器，主要是通过http模块

Express，Koa等后端框架也是基于http模块来进行封装的

```js
// 因为服务是一直运行着的代码，所以如果修改了对应的服务代码
// 需要重新运行node程序，以使用最新代码重新进行执行
const http = require('http')

// 参数 是一个 回调函数 - 当客户端向服务器请求的时候，对应的回调就会被触发
//		 默认情况下，对应的回调会被触发两次 请求/ 和 请求 /favicon.ico
//     --- 参数一 请求对象 - 本质上是个读取流
//     --- request中携带了所有请求信息，包括请求的url，methods，headers，请求数据等相关信息
//
//     ---- 参数二 - 响应对象 - 本质上是一个写入流
const server = http.createServer((request, response) => {
  // 通过response写入流向客户端写入数据
  response.end('Hello World')
})

// 启动服务器
// 参数一 --- 端口号
//       --- 端口号1024以上是自定义端口号
//       --- 端口号使用两个字节表示 一共可以表示 256 * 256 = 65536
//       --- 也就是说端口的范围为 0 ~ 65535
//       --- 所以推荐端口号设置的范围在 [1024, 65535] 之间

// 参数二 --- 主机host - 通常可以传入localhost、ip地址127.0.0.1、或者ip地址0.0.0.0，默认是0.0.0.0
//       --- 可以省略
// 	localhost - 127.0.0.1 对应的域名
//  127.0.0.1 - 回环地址(Loop Back Address)，表达的意思其实是我们主机自己发出去的包，直接被自己接收
//            - 正常的数据库包经常 应用层 - 传输层 - 网络层 - 数据链路层 - 物理层
//            - 而回环地址，是在网络层直接就被获取到了，是不会经常数据链路层和物理层的
//            - 比如我们监听 127.0.0.1时，在同一个网段下的其它主机中，通过ip地址是不能访问的
//  0.0.0.0 - 监听IPV4上所有的地址，再根据端口找到不同的应用程序
//          - 比如我们监听 0.0.0.0时，在同一个网段下的主机中，通过ip地址是可以访问的

// 参数三 --- 服务开启成功后，会被触发的回调函数
// 当服务开启成功后，会阻塞在这里，一直等待客户端发生请求并作出对应的响应并不会主动去关闭对应的node程序
server.listen(3000, () => console.log('server started'))
```



```js
const http = require('http')

// 服务本质上是createServer创建处理的实例对象
// 所以可以开启多个对应的服务实例
const server1 = http.createServer((request, response) => {
  response.end('Hello 3000')
})
server1.listen(3000, () => console.log('server started in 3000'))


const server2 = http.createServer((request, response) => {
  response.end('Hello 3001')
})
server2.listen(3001, () => console.log('server started in 3001'))

// http.createServer 本质上是 去新建了一个Server实例
// 所以也可以通过new http.Server的方式去进行对应的调用
const server3 = new http.Server((request, response) => {
  response.end('Hello 3002')
})
server3.listen(3002, () => console.log('server started in 3002'))
```



## request对象

在向服务器发送请求时，我们会携带很多信息，比如:

1. 本次请求的URL，服务器需要根据不同的URL进行不同的处理
2. 本次请求的请求方式，比如GET、POST请求传入的参数和处理的方式是不同的
3. 本次请求的headers中也会携带一些信息，比如客户端信息、接受数据的格式、支持的编码格式等
4. 等等 。。。

这些信息，Node会帮助我们封装到一个request的对象中，我们可以直接来处理这个request对象

```js
const http = require('http')

// POST http://127.0.0.1:3000/login
const server = http.createServer((req, res) => {
  console.log(req.url) // 请求路径 -> /login
  console.log(req.method) // 请求方式 -> POST
  console.log(req.headers) // 请求头

  // 虽然请求头的key是 首字母大写，中划线连接的多个单词组成
  // 但是在req对象中, 将对应请求头的key设置成了小写单词，中划线组成的多个单词组成的key
  console.log(req.headers['user-agent']) // => PostmanRuntime/7.29.2
  console.log(req.headers['User-Agent']) // => undefined

  res.end('请求成功')
})

server.listen(3000, () => console.log('server running🚀'))
```



### 参数解析

`query参数`

```js
const http = require('http')
// url模块用于对url路径进行解析 --- 功能和URL类是一致的
const url = require('url')

// GET http://127.0.0.1:3000/products?offset=20&limit=10
const server = http.createServer((req, res) => {
  const urlStr = req.url // => /products?offset=20&limit=10

  // 对url地址进行解析
  const urlObj = url.parse(urlStr)

  if (urlObj.pathname === '/products') {
    // 解析query参数 使用的依旧是URLSearchParams
    const params = new URLSearchParams(urlObj.query)

    res.end(`请求${params.get('limit')}条歌词成功, 偏移量为${params.get('offset')}`)
  } else {
    res.end('路径错误')
  }
})

server.listen(3000, () => console.log('server running🚀'))
```



`body参数`

```js
const http = require('http')
const url = require('url')

// POST http://127.0.0.1:3000/login
// body -> { name: 'Klaus', age: 23 }

// req本质上是个读取流，所以其继承自ReadStream和EventEmiter
const server = http.createServer((req, res) => {
  const urlObj = url.parse(req.url)

  if (urlObj.pathname === '/login') {
    // 因为req是读取了，所以获取到的数据默认是buffer流
    // 除了toString方法和encoding配置对象外
    // 对于req对象，还可以通过调用其setEncoding方法来设置请求体的编码方式
    req.setEncoding('utf-8')

    // req本质上是读取流，所以可以通过onData事件监听body传入的数据
    // ps: body传入的数据在请求体中，不在req这个请求头对象中
    req.on('data', data => {
      // data流转换后是字符串类型数据，需要手动将其转换成json格式数据
      console.log(JSON.parse(data))
    })

    // 当数据全部读取完毕后，向客户端返回对应的数据
    req.on('end', () => {
      res.end('login success')
    })
  } else {
    res.end('login failed')
  }
})

server.listen(3000, () => console.log('server running🚀'))
```





## response对象

如果我们希望给客户端响应的结果数据，可以通过两种方式

| 方法  | 说明                           |
| ----- | ------------------------------ |
| write | 直接写出数据，但是并没有关闭流 |
| end   | 写出数据, 并且写出后会关闭流   |

1. response本质上是一个写入流
2. response对close方法进行了重写，直接调用close方法会报错，也就是response没有close方法，关闭流只能通过end方法
3. 如果我们没有调用 end，客户端将会一直等待结果。所以客户端在发送网络请求时，推荐设置上对应的超时时间

```js
const http = require('http')

// POST http://127.0.0.1:3000/login
const server = http.createServer((req, res) => {
  // 设置响应码
  res.statusCode = 404

  res.end('404 not found')
})

server.listen(3000, () => console.log('server running🚀'))
```

```js
const http = require('http')

// POST http://127.0.0.1:3000/login
const server = http.createServer((req, res) => {
  const user = {
    name: '夏日夜的晚风',
    age: 23
  }

  // 对于中文
  //  postman - 如果没有指定编码格式 - 默认使用utf-8
  //  浏览器 - 采用系统编码格式 - 中文一般是GBK - 所以会出现乱码

  // 所以对于返回的数据 需要自己设置对应的头信息
  // 1. 请求头的key 可以是全小写单词加中划线组成，也可以使用小驼峰单词加中划线组成 - setHeader方法内部会统一转换
  // 2. charset中 utf-8 === utf8
  // 3. 编码格式 需要设置成content-type的值 且文本类型不能省略, 字符编码类型可以省略
  //    Content-Type: 文本类型;[字符编码类型]
  
  // 使用setHeader一次只能设置一个响应头
  // 如果需要一次设置多个对应的响应头 需要使用writeHead方法
  res.setHeader('content-type', 'text/plain;charset=utf-8;')

  res.end(`username: ${user.name} - age: ${user.age}`)
})

server.listen(3000, () => console.log('server running🚀'))
```

```js
const http = require('http')

// POST http://127.0.0.1:3000/login
const server = http.createServer((req, res) => {
  const user = {
    name: '夏日夜的晚风',
    age: 23
  }

  // writeHead = statusCode + setHeader
  // 即同时设置对应的响应码和响应头信息
  // writeHead 可以同时设置多个响应头信息
  res.writeHead(200, {
    'content-type': 'text/plain;charset=utf-8;'
  })

  res.end(`username: ${user.name} - age: ${user.age}`)
})

server.listen(3000, () => console.log('server running🚀'))
```

