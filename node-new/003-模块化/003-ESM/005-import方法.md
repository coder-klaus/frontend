CJS是在运行时动态解析，而ESM是在构建时就通过静态分析识别整个模块依赖图

因此 import 只能用于 模块的顶层，不能嵌入函数、条件语句等逻辑代码里，否则将无法通过静态分析识别



简单来说，浏览器加载模块时

1. 扫描模块顶层的 import 语句，确定依赖
2. 并行下载所有依赖的模块
3. 解析这些模块，提取导出的内容
4. 执行模块中的代码



为了解决ESM在动态加载的不足，ESM引入了`import()`

```js
// import方法返回一个promise实例，等异步组件加载完成，会返回一个对象
// 这个对象解析规则和 * as 的规则是一样的
const math = await import('./utils/math.js')

// 默认导出
console.log(math.default(1, 2))
// 具名导出
console.log(math.add(1, 2))
console.log(math.sub(1, 2))
```

import() 函数的好处是可以在逻辑代码里动态加载模块，适合按需加载的场景

构建工具识别到`import`方法后会自动将加载的文件进行拆包处理



```js
console.log(import.meta)
/*
{
  dirname: '/Users/klaus/Desktop/node-demo',
  filename: '/Users/klaus/Desktop/node-demo/main.js',
  resolve: [Function: resolve],
  url: 'file:///Users/klaus/Desktop/node-demo/main.js'
}
*/

// 如果是 node加载本地文件，那么url是以file协议的url => file:///Users/klaus/Desktop/node-demo/main.js
// 如果是浏览器加载外部模块，那么可能是类似于 => http://127.0.0.1:5500/main.js
console.log(import.meta.url) // file:///Users/klaus/Desktop/node-demo/main.js
console.log(import.meta.filename) // /Users/klaus/Desktop/node-demo/main.js
console.log(import.meta.dirname) // /Users/klaus/Desktop/node-demo

// 解析路径, 将相对路径转换为绝对路径 「 file:// 路径 」
console.log(import.meta.resolve('./utils/math.js')) // file:///Users/klaus/Desktop/node-demo/utils/math.js
```

