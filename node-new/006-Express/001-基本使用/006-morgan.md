 [morgan](https://www.npmjs.com/package/morgan) 是 Express 官方提供的日志记录中间件，但并没有被集成到Express中，如需使用，需要额外安装

```shell
pnpm add morgan
```



使用方式如下

```js
import fs from 'node:fs'
import express from 'express'
import morgan from 'morgan'

const app = express()

// 使用 morgan 中间件 记录请求日志
// 默认情况下，日志记录会被回显到控制台
// 可以通过设置参数 stream 来改变日志记录的输出方式
app.use(morgan('combined', {
  stream: fs.createWriteStream('./logs/access.log', {
    flags: 'a'
  })
}))

app.get('/', (req, res) => {
  res.send('hello world')
})

app.listen(3000)
```

