1, cookie session 使用

2. node 事件循环
3. node 开发脚手架工具

==Node 590 - 594 「 Koa 和 Express 源码还没有看」==

---

----

----

---

---





```js
// CJS是同步加载文件，可以加载 JavaScript 文件，也可以加载 JSON 文件
// ESM通过静态分析 import 和 export 来确定模块的依赖关系
// 默认情况下，JSON并不存在任何的导入和导出关键字，所以ESM默认不能加载 JSON 文件
// 同样，ESM 不能直接导入 图片、视频、音频、字体等文件

// 为了可以加载 JSON 文件，需要创建一个功能类似于 CJS 的 require 函数 来实现对应功能
import { createRequire } from 'node:module';

// ESM是异步加载的, 所有使用到的模块地址都是一个 虚拟URL路径
console.log(import.meta.url); // file:///Users/klaus/Desktop/node-demo/index.js

const require = createRequire(import.meta.url);

const res = require('./data.json');
console.log(res)
```

````js
import fs from 'node:fs/promises';

// 自己通过 fs模块去读取文件 「 可以一次性读取，也可以分批次读取 」
const res = await fs.readFile('./data.json', 'utf-8');
const data = JSON.parse(res);
console.log(data)
````



为此Node引入了 `with` 关键字

```js
import data from './data.json' with { type: 'json' };
console.log(data);
```

目前 with 仅支持 json，其余只能同读取为json后再进行解析



-----

```js
const mysql = require('mysql2');
const connection = mysql.createConnection({
    host: 'localhost',
    port: 3306,
    user: 'root',
    password: 'coderwhy123.',
    database: 'db_music'
});

const products = require('./products.json');
const stmt = 'INSERT INTO products (brand, title, price, score, voteCnt, url, pid) VALUES ?';
connection.query(stmt, [products], function(err, results) {
    if (err) throw err;
    console.log('Data inserted');
});
connection.end();
```

`insert into products set ?`

1. 一个？





---

---

---

restFUI api
