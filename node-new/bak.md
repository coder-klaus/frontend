1, cookie session 使用

2. node 事件循环
3. node 开发脚手架工具

==Node 590 - 594 「 Koa 和 Express 源码还没有看」==

---

----

----

---

---



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

node --inspect app.js

node中的定时器

+ setInterval

+ setTimeout

+ setImmediate

+ process.nextTick

  





