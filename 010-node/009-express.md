原生http在进行很多处理时，会较为复杂

有URL判断、Method判断、参数处理、逻辑代码处理等，都需要我们自己来处理和封装

所有的内容都放在一起，会非常的混乱

所以为了简化我们的操作，在Node中比较流行的Web服务器框架是express、koa



## 初体验

```shell
npm install express 
```

```js
const express = require('express')

// 创建对应的服务器
const app = express()

// 设置后端路由
app.get('/home', (req, res) => res.end('get /home'))
app.post('/login', (req, res) => res.end('login success'))

// 监听端口 并开启服务器
app.listen(3000, () => { console.log('server is running ~') })
```



## 中间件

Express是一个路由和中间件的Web框架，它本身的功能非常少:

Express应用程序本质上是一系列中间件函数的调用

中间件的本质是传递给express的一个回调函数

这个回调函数接受三个参数

+ 请求对象(request对象)
+ 响应对象(response对象)
+ next函数(在express中定义的用于执行下一个中间件的函数)

```js
const express = require('express')

const app = express()

// get的第二个回调函数就是一个中间件
app.get('/home', (req, res) => {
  // 执行任何需要执行的js代码
  console.log('home被匹配了')

  // 更改请求(request)和响应(response)对象
  req.name = 'Klaus'

  // 结束请求-响应周期(返回数据)
  res.end('home page')
})

app.listen(3000, () => {
  console.log('server running~')
})
```

```js
const express = require('express')

const app = express()

app.get('/home', (req, res, next) => {
  req.name = 'Klaus'

  // 如果当前中间件功能没有结束请求

  // 则必须调用next()将控制权传递给下一个中间件功能
  // 调用next方法后，会自动执行 下一个 被匹配上的中间件函数

  // 否则，请求将被挂起 -- 也就是服务器没有任何的响应
  next()
})

// use方法中传入的也是一个中间件
app.use((req, res) => {
  res.end('use middleware')
})

app.listen(3000, () => {
  console.log('server running~')
})
```



![image.png](https://s2.loli.net/2022/11/21/EizFO82voaqs61n.png) 



express主要提供了两种方式的中间件：

+ app/router.use
+ app/router.methods



`普通中间件`

```js
// express中的中间件匹配规则
// 1. 根据请求 找到第一个符合规则的中间件函数并执行
// 2. 至于后续的匹配到的中间件函数是否执行，取决于上一个中间件函数是否有调用next方法

// app.use是最为普通的中间件，无论什么请求还是什么路径 都可以匹配该中间件
app.use((req, res) => {
  res.end('use middleware')
})
```



`路径匹配中间件`

```js
// 路径匹配中间件
// 只匹配对应的路径
// 不匹配对应的请求方法
app.use('/home', (req, res) => {
  res.end('home page')
})
```



`路径方法中间件`

```js
// app.method(path, callback)
// 1. 方法需要对应匹配 2. 路径需要对应匹配
app.get('/home', (req, res) => {
  res.end('home page')
})
```



`注册多个中间件`

```js
// 可以为一个对应方法的路径注册多个中间件
// 默认情况下只会执行第一个中间件回调函数
// 之后的中间件函数是否执行 取决于 是否调用对应的next方法
app.get('/home', (req, res, next) => {
  res.write('home page 01')
  next()
}, (req, res, next) => {
  res.write('home page 02')
  next()
}, (req, res) => {
  res.end('home page 03')
})
```



### body解析

```js
const express = require('express')

const app = express()

// 可以将通用的操作放置到普通中间件中进行优先执行
app.use((req, res, next) => {
  if (req.headers['content-type'].startsWith('application/json')) {
    let str = ''

    req.on('data', data => {
       str += data
    })

    req.on('end', () => {
      req.body = JSON.parse(str)
      next()
    })
  } else {
    next()
  }
})

app.post('/login', (req, res) => {
  // res.json()
  // 1. 将数据以json格式进行返回
  // 2. 结束响应流
  res.json(req.body)
})


app.listen(3000, () => {
  console.log('server running~')
})
```



对此，express中提供了对应的内置中间件，以达到和上述案例相同功能的效果

```js
const express = require('express')

const app = express()

// express.json()
// 1. 解析请求体
// 2. 将请求体转换为json格式数据
// 3. 挂载到res.body上
// 4. 调用next方法执行下一个中间件
app.use(express.json())

// 解析通过x-www-form-urlencoded传递过来的数据
// express.urlencoded() - 使用node内置的querystring模块进行解析 - 已经过期，不推荐，会有警告
// express.urlencoded({ extended: true }) - 使用第三方库 qs进行解析 - 推荐
app.use(express.urlencoded({ extended: true }))

app.post('/login', (req, res) => {
  res.json(req.body)
})

app.listen(3000, () r> {
  console.log('server running~')
})
```



### 第三方中间件

#### 日志记录

如果我们希望将请求日志记录下来，那么可以使用express官网开发的第三方库: [morgan](https://github.com/expressjs/morgan)

morgan默认没有被集成到express中，需要单独安装和下载

```js
const fs = require('fs')
const express = require('express')
const morgan = require('morgan')

const app = express()

// 创建写入流 -- 指定日志需要被写到那个文件中
// ps: morgan 要求 写入文件必须预先被创建 否则会报错
const writeStream = fs.createWriteStream('./logs/access.log')

// 注册中间件
app.use(morgan('combined', {
  stream: writeStream
}))

app.post('/foo', (req, res) => {
  res.end('foo page')
})

app.listen(3000, () => {
  console.log('server running~')
})
```



#### 文件上传

上传文件，我们可以使用express提供的multer中间件来完成

`单个文件上传`

```js
const express = require('express')
const multer  = require('multer')

// 创建上传对象 --- 指定上传的文件存在于那个目录下
const upload = multer({ dest: 'uploads' })

const app = express()

// 在需要上传文件的接口的处理函数调用之前，先使用multer中间件进行解析
// upload.single('avatar') --- avatar是图片文件对应的字段名
app.post('/avator', upload.single('avatar'), (req, res) => {
  res.end('upload success')
})

app.listen(3000, () => {
  console.log('server running~')
})
```



此时默认情况下，保存下来的文件是没有后缀的，且文件是hash值

因为文件没有后缀，所以可能会导致vscode等工具无法正确打开对应的文件

所以multer也提供了配置对象，来方便我们自定义对应的文件名

```js
const express = require('express')
const multer  = require('multer')

const storage = multer.diskStorage({
  // 定义图片需要被存储到那个目录下
  // 自定义情况下，对应的目录需要存在，不存在会报错
  destination: function (req, file, cb) {
    // cb是一个回调函数
    // 参数一 - error对象 - 一般省略
    // 参数二 - 目录名
    cb(null, 'uploads')
  },

  // 自定义输出文件名
  filename: function (req, file, cb) {
    // cb的功能和参数和destination中的cb是一致的
    cb(null, Date.now() + '_' + file.originalname)
  }
})

// 通过storage对象来对文件存放路径和文件名进行自定义操作
const upload = multer({ storage: storage })

const app = express()

app.post('/avator', upload.single('avatar'), (req, res) => {
  // 上传的文件相关的信息会被存储到这个对象中
  console.log(req.file)
  res.end('upload success')
})

app.listen(3000, () => {
  console.log('server running~')
})
```



`多个文件上传`

```js
const express = require('express')
const multer  = require('multer')

const storage = multer.diskStorage({
  destination: function (req, file, cb) {
    cb(null, 'uploads')
  },

  filename: function (req, file, cb) {
    cb(null, Date.now() + '_' + file.originalname)
  }
})

const upload = multer({ storage: storage })

const app = express()

// 上传多个文件
app.post('/avator', upload.array('avatar'), (req, res) => {
  res.end('upload success')
})

app.listen(3000, () => {
  console.log('server running~')
})
```



### 解析form-data

```js
const express = require('express')
const multer  = require('multer')

const app = express()
const upload = multer()

// upload.any() --- 解析form-data中任何的普通字段
// ps: 如果字段的值是文件，那么该字段会被自动忽略
app.post('/form', upload.any(), (req, res) => {
  console.log(req.body)
  res.end('form upload success')
})

app.listen(3000, () => {
  console.log('server running~')
})
```



## 解析query参数

```js
app.post('/foo', (req, res) => {
  // query参数 会被express自动解析 并存放到req的query参数中
  console.log(req.query)
  res.end('foo page')
})
```



## 解析params参数

```js
const express = require('express')
const multer  = require('multer')

const app = express()
const upload = multer()

app.post('/foo/:id/:username', (req, res) => {
  // 注意: req.params中任何一个参数对应的属性值都是字符串类型
  // 所以如果是其它类型的值，在运算前需要手动进行类型转换
  console.log(req.params) // => { id: '1810166', username: 'klaus' }
  res.end('foo page')
})

app.listen(3000, () => {
  console.log('server running~')
})
```



## 数据响应

```js
app.post('/foo', (req, res) => {
  res.write('res.write方法 可以向客户端输送数据，但不会关闭响应流')
  res.end('res.end方法会以字符串的方式向客户端传递数据，并关闭响应流')
})
```

```js
app.post('/foo', (req, res) => {
  // 通过status方法 设置响应状态码
  // 200 - 服务器请求处理成功
  // 201 - 服务器请求处理成功 - 一般用于表示对应的资源已经被创建
  // 202 - 服务器接收了请求，但请求还未被处理
  res.status(201)

  // json方法中可以传入很多的类型:object、array、string、boolean、number、null等
  // 它们会被转换成json格式后被返回，同时res.json会关闭对应的响应流
  res.json({
    name: 'Klaus',
    age: 23
  })
})
```

