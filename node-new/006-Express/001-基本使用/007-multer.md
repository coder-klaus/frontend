[`multer`](https://www.npmjs.com/package/multer)专门用来解析`formdata`格式数据，可以用来识别上传的文件以及表单中的其他普通字段值

```shell
pnpm add multer
```



## 解析单个文件

```js
import express from 'express'
import multer from 'multer'

const app = express()

// 使用 multer 中间件 处理文件上传
// 如果存放文件的目录不存在，会自动创建
const upload = multer({ dest: 'uploads/' })

// 1. 一般通过特定接口处理文件上传，不是所有接口都需要的功能
// 因为可以单独应用于某一个路由中间件 => 即 局部使用，不是全局使用
app.post('/upload', upload.single('avatar', 12), (req, res) => {
  // 上传成功后，对应文件的相关元数据会被存放于 req.file 中
  console.log(req.file)
  res.send('上传成功')
})

app.listen(3000)
```

默认情况下，上传的文件名是一个随机的`hash`值，且是没有后缀的，因此默认情况下并不能直接打开

```js
import path from 'node:path'
import express from 'express'
import multer from 'multer'

const app = express()

// 1. 自定义存储方式 => 指定文件的存储位置 和 文件名
// 2. 使用 diskStorage 配置 multer后，目录不会自动创建，需要手动创建
const storage = multer.diskStorage({
  destination: function (req, file, cb) {
    cb(null, './uploads')
  },
  filename: function (req, file, cb) {
    cb(null, file.fieldname + '-' + Date.now() + path.extname(file.originalname))
  }
})

const upload = multer({ storage: storage })

// 使用 upload.single 上传单个文件 的时候可以存在非文件字段 => 但不能存在其余文件字段，否则会报错
// 1. 文件相关元数据，会被挂载到 req.file
// 2. 非文件字段，会被挂载到 req.body
app.post('/upload', upload.single('avatar', 12), (req, res) => {
  console.log(req.file) // 对象格式数据
  console.log(req.body) // 对象格式数据
  res.send('上传成功')
})

app.listen(3000)
```



## 解析多个文件

### 单个字段

```js
import path from 'node:path'
import express from 'express'
import multer from 'multer'

const app = express()

const storage = multer.diskStorage({
  destination: function (req, file, cb) {
    cb(null, './uploads')
  },
  filename: function (req, file, cb) {
    cb(null, file.fieldname + '-' + Date.now() + path.extname(file.originalname))
  }
})

const upload = multer({ storage: storage })

// upload.array 要求上传的字段 只能有 files，不能存在其余字段
app.post('/upload', upload.array('files', 8), (req, res) => {
  // 上传成功后，对应文件的相关元数据会被存放于 req.files 中
  console.log(req.files) // 格式是数组
  res.send('上传成功')
})

app.listen(3000)
```



### 多个字段

```js
import path from 'node:path'
import express from 'express'
import multer from 'multer'

const app = express()

const storage = multer.diskStorage({
  destination: function (req, file, cb) {
    cb(null, './uploads')
  },
  filename: function (req, file, cb) {
    cb(null, file.fieldname + '-' + Date.now() + path.extname(file.originalname))
  }
})

const upload = multer({ storage: storage })

// 使用 upload.fields 上传多个文件时
// 1. 可以存在多个非文件字段, 会挂载到 req.body
// 2. 相关文件字段元数据，会被挂载到 req.files
// 3. 如果存在额外的文件字段，直接报错
app.post('/upload',
  upload.fields([
    { name: 'avatar', maxCount: 1 },
    { name: 'files', maxCount: 8 }
  ]), (req, res) => {
  console.log(req.files) // 格式是 Record<fieldname, array>
  console.log(req.body) // 格式为对象 => 可以解析非文件字段
  res.send('上传成功')
})

app.listen(3000)
```





## 解析普通字段

```js
import express from 'express'
import multer from 'multer'

const app = express()

// 只解析普通字段，如果出现文件上传，会报错
app.use(multer().none())

app.post('/form', (req, res) => {
  // 非文件字段值会被存入 req.body 中
  console.log(req.body)
  res.send('上传成功')
})

app.listen(3000)
```



## 解析所有字段

```js
import path from 'node:path'
import express from 'express'
import multer from 'multer'

const app = express()

const storage = multer.diskStorage({
  destination: function (req, file, cb) {
    cb(null, './uploads')
  },
  filename: function (req, file, cb) {
    cb(null, file.fieldname + '-' + Date.now() + path.extname(file.originalname))
  }
})

const upload = multer({ storage: storage })

app.post('/upload', upload.any(), (req, res) => {
  console.log(req.body) // 格式是对象
  console.log(req.files) // 格式是数组
  res.send('上传成功')
})

app.listen(3000)
```

