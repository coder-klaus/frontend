## scripts脚本

### 基本使用

假设有如下 `scripts` 配置：

```js
"scripts": {
  "test": "webpack --version"
}
```

此时可以通过 `npm run test` 执行 `webpack --version` 命令。



### 特殊脚本

`start`、`stop`、`restart`、`test` 这四个特殊脚本可以省略 `run`，即：

- `npm start` 等价于 `npm run start`
- `npm stop` 等价于 `npm run stop`
- `npm restart` 等价于 `npm run restart`
- `npm test` 等价于 `npm run test`

其他自定义脚本（如 `npm build`、`npm dev` 等）**都必须用** `npm run xxx` **来执行**。



### 查找优先级

使用 `npm run` 执行脚本时，会优先使用当前项目下 `node_modules/.bin` 目录中的命令行工具。例如：

1. 全局安装了 `webpack@5`
2. 项目本地 `node_modules` 安装了 `webpack@3`

在命令行直接执行 `webpack --version`，会使用全局安装的 webpack，输出 `5.x.x`

通过 `npm run test` 执行脚本，则会优先使用本地的 webpack，输出 `3.x.x`



## npx

`npx` 是 `npm` 自带的命令行工具，安装 `npm` 后会自动包含 `npx`。

`npx`可以用于临时下载一些工具包，其优势在于每次使用时都能获得最新版本的工具包。

> 任何可执行库在安装到 `node_modules` 时，会将对应的可执行命令链接到 `node_modules/.bin` 目录中。

1. 首先查找本地 `node_modules/.bin` 目录中是否存在对应的工具包。 
2. 如果本地没有，则依次向上级目录的 `node_modules/.bin` 目录查找该工具包。 
3. 如果一直到项目根目录都没有找到，则会去全局环境中查找是否存在该工具包。 
4. 如果全局环境中依然没有，则会临时下载该工具包并使用。 
5. 工具包使用完毕后，会移除临时安装的工具包。

