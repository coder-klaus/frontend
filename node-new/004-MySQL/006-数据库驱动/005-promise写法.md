数据库查询是异步操作，因为查询结果的返回时间不确定

而之前的写法都依赖于回调函数来获取查询结果，但这种写法不优雅，容易导致“回调地狱”（Callback Hell），尤其当查询结果需要再进行其他查询或处理时，代码会变得复杂。

为此我们可以使用 Promise 风格的 API



## 单连接

```js
import mysql from 'mysql2';

const connect = mysql.createConnection({
  host: 'localhost',
  user: 'root',
  password: 'P@ssw0rd',
  database: 'demo_db'
})

const statement = 'SELECT * FROM orders WHERE amount >= ?;';
// 使用 单连接 Promise 风格的 API
const connection = connect.promise();

const [results, fields] = await connection.execute(statement, [2000]);

console.log('results', results);
console.log('fields', fields);

connection.end();
```



## 多连接

```js
import mysql from 'mysql2';

const pool = mysql.createPool({
  host: 'localhost',
  user: 'root',
  password: 'P@ssw0rd',
  database: 'demo_db',
  connectionLimit: 10 // 设置连接池中可以创建的最大连接数 「 默认是10 」
})

// 使用 Promise 风格的 API
const connection = pool.promise();

const statement = 'SELECT * FROM orders WHERE amount >= ?;';

// 返回结果是一个数组，数组中第一个元素是结果集，第二个元素是字段信息
const [results, fields] = await connection.execute(statement, [2000]);

console.log('results', results);
console.log('fields', fields);
```



