```js
// process 是 Node 提供的全局对象
// 用于表示 当前的 Node 进程相关信息
console.log(process)

// 输出当前脚本运行工作目录
console.log(process.cwd())
```



## argv

`argv`表示node的命令行参数

`argv` => `arguments vector` => 一个包含了所有命令行参数的数组



假设启动命令为

```shell
node main.js name=klaus age=23 
```

脚本为

```js
console.log(process.argv)
```

则实际输出内容为

```shell
[
  # 前两条是内置的argv
  '/usr/local/bin/node', # node 所在目录
  '/Users/klaus/Desktop/node-demo/main.js', # 运行脚本目录
  # 自定义参数会以字符串形式直接传入
  'name=klaus', # 参数1
  'age=23' # 参数2
]
```



## env

node 的环境变量，默认是一个有一系列属性的对象

假设启动命令为

```shell
name=Klaus age=23 node main.js
```

脚本为

```js
// 参数得到了解析，自动变成 key 和 value 挂载到了 process.env 上
// 值的类型都是字符串类型值，需要手动进行转换
console.log(process.env.name) // 'Klaus'
console.log(process.env.age)  // '23'
```



