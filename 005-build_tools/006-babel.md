Babel 是一个 JavaScript 编译器，主要可以完全以下几个功能

1. 将 ECMAScript 2015+ 代码转换为旧浏览器或环境中的向后兼容版本的 JavaScript

2. 将JSX语法编写的代码转换为浏览器可以识别的原生JS代码
3. 将TS代码转换为浏览器可以直接识别的JS代码

Babel 可以帮助开发人员使用最新的 JavaScript 语言特性进行代码编写，同时又可以保证代码在旧环境中仍能正常工作

包括:语法转换、源代码转换、Polyfill实现目标环境缺少的功能等

Babel 通过将新特性转换为能在旧环境中正常工作的代码来实现这一目标

目前除了babel可以实现上述功能外，`SWC`和`esbuild`也可以同样实现和babel一样的功能



Babel使用的是`微内核`架构，即babel只提供核心的架构`@babel/core`

`@babel/core`和postcss一样，能做的功能是有限的，或者说基本没有什么功能

如果我们需要使用babel的转换功能，那么我们就需要为babel添加对应的插件或预设



## 在命令行使用

babel是一个独立的工具，可以脱离构建工具，单独使用

```shell
# @babel/core:babel的核心代码，必须安装
# @babel/cli:可以让我们在命令行使用babel
npm install @babel/cli @babel/core

 # 使用babel来处理我们的源代码
 # src:是源文件的目录
 # --out-dir:指定要输出的文件夹dist
 # npx babel <源文件或文件夹路径> --out-dir <输出文件夹>
 npx babel src --out-dir dist
```

此时，babel并没有给我们转换任何的代码，即转换前后代码是基本一致的

所以如果我们需要转换，我们可以为babel配置对应的plugins

```shell
# 一个新语法转旧语言 就需要使用一个对应的插件
# @babel/plugin-transform-arrow-functions ==> 转换箭头函数
# @babel/plugin-transform-block-scoping ===> 将const 和 let 转换为 var
npm install @babel/plugin-transform-arrow-functions  @babel/plugin-transform-block-scoping -D

# 转换 使用多个插件用逗号分隔（前后不要有空格）
npx babel src --out-dir dist --plugins=@babel/plugin-transform-block-scoping,@babel/plugin-transform-arrow-functions
```

但是如果要转换的内容过多，一个个设置和下载插件是比较麻烦的，我们可以使用预设(preset)

所谓预设，其实就是插件的集合包

```shell
# 安装基本预设
# 安装了preset后，babel会根据我们所需要适配的浏览器
# 自动选择所需要抓换的代码并对其进行转换
npm install @babel/preset-env -D

# 编译
npx babel src --out-dir dist --presets=@babel/preset-env
```



## 核心原理

从一种源代码(原生语言)转换成另一种源代码(目标语言)是编译器所需要完成的工作

所以可以将babel看成就是一个`编译器`

Babel编译器的作用就是将我们的源代码，转换成浏览器可以直接识别的另外一段源代码

对应的流程可以划分为:

1. 解析阶段(Parsing) ===> 将源代码经过解析，生成AST(抽象语法树)
2. 转换阶段(Transformation) ===> 遍历阶段，将对应的节点应用对应的插件，进行代码的转换，形成新的AST
3. 生成阶段(Code Generation) ===> 将新的AST转回我们平时常见的代码，此时新生成的代码就是已经被转换后的代码

![image.png](https://s2.loli.net/2022/12/24/AzHc439lIxeqwK2.png) 

![image.png](https://s2.loli.net/2022/12/24/HNrMCzfywvAl3UK.png) 



## 结合webpack

直接在CLI中使用对应的babel，在每次构建之前都需要手动执行对应的构建逻辑，十分不方便

为此需要将babel编译转换的命令结合到webpack中，以便于每次使用webpack进行构建的时候，可以自动对对应的JS代码进行转换操作

如果需要再webpack中对某种文件进行某种形式的转换，就需要使用loader

在这里就是`babel-loader`

```shell
npm install @babel/core babel-loader -D
npm install @babel/preset-env -D
```

```js
const path = require('path')
module.exports = {
  mode: 'development',
  devtool: false,
  entry: '/src/main.js',
  output: {
    path: path.resolve(__dirname, 'build'),
    filename: 'bundle.js',
    // 自动清除上次编译生成的文件 --- 功能和cleanWebpackPlugin一致
    clean: true
  },
  module: {
    rules: [
     {
      test: /\.m?js$/,
      use: {
        // 遇到JS文件，使用babel-loader调用babel进行处理，处理完毕后，再交给webpack进行解析
        loader: 'babel-loader',
        // 不对node_modules下的第三方库进行处理
        exclude: /node_modules/,
        // 配置项
        options: {
          // 需要使用的插件列表
          // plugins: []
          
          // 需要使用的预设列表
          // babel会自动根据browserslist查询到的所需要适配的浏览器所支持的语法
          // 自动使用预设包中对应的plugin
          // 常见的预设有@bebel/preset-env, @babel/preset-typescript, @babel/preset-react
          presets: [
            // ['@babel/preset-env', { 配置对象 }]
            /*
            	例如:
            		presets: [
                  ['@babel/preset-env', {
                    // targets属性的功能和browserslist的功能是一致的
                    // targets中的配置会覆盖browserslist中的配置
                    // 所以一般不推荐设置targets
                    // 因为browserslist可以在多个工具之间共享所需要兼容的代理列表
                    // 而targets只能用于babel一个工具
                    targets: ">5%, not dead"
                  }]
                ]
            */
            '@babel/preset-env'
          ]
        }
      }
     }
    ]
  }
}
```



## stage-x

一个新特性被正式加入ECMA规范，需要经历5个阶段

| 阶段    | 说明                                                         |
| ------- | ------------------------------------------------------------ |
| stage 0 | strawman(稻草人)，任何尚未提交作为正式提案的讨论、想法变更或者补充都被认为是第 0 阶段的"稻草人" |
| stage 1 | proposal(提议)，提案已经被正式化，并期望解决此问题，还需要观察与其他提案的相互影响 |
| stage 2 | draft(草稿)，Stage 2 的提案应提供规范初稿、草稿<br />并且开始观察设置是否合理，是否会和其它提案相互冲突 |
| stage 3 | candidate(候补)，Stage 3 提案是建议的候选提案<br />在这个阶段，规范的编辑人员和评审人员必须在最终规范上签字<br />Stage 3 的提案不会有太大的改变，在对外发布之前只是修正一些问题 |
| stage 4 | finished(完成)，进入 Stage 4 的提案将包含在 ECMAScript 的下一个修订版中 |

```js
presets: [
  // stage-0 表示 使用 babel-preset-stage-0 这个对应的预设
  // 但这是babel6的配置选项，在babel7开始已经不在被推荐，babel7建议使用preset-env
  'stage-0'
]
```



## 配置文件

如果babel配置和webpack配置写在一起，会导致webpack的配置文件越来越冗余

在实际开发中，我们可以将babel的配置单独抽离出来，形成一个独立的配置文件

| 文件                                         | 说明                                                         |
| -------------------------------------------- | ------------------------------------------------------------ |
| babel.config.json(或者.js，.cjs，.mjs)       | 推荐，babel7的配置文件<br />可以直接作用于Monorepos项目的子包 |
| .babelrc.json(或者.babelrc，.js，.cjs，.mjs) | 早期babel的配置文件<br />不再推荐使用                        |

```js
module.exports = {
  presets: [
    '@babel/preset-env'
  ]
}
```



## polyfill

Babel 是用于转换 JavaScript 语法的工具，它不能转换 JavaScript API

如果你想在低版本浏览器或运行环境中使用某个不支持的 API，则需要使用 Polyfill

Polyfill 通常是一段 JavaScript 代码，用于模拟在旧版浏览器或运行环境中不存在的功能

例如，假设你想在旧版浏览器中使用 Fetch API 进行 HTTP 请求，但该浏览器不支持 Fetch API

这时你就可以使用 Polyfill 来模拟这个 API，从而让你的代码在旧版浏览器中正常运行



babel7.4.0之前，可以使用 @babel/polyfill的包，但是该包现在已经不推荐使用了

babel7.4.0之后，可以通过单独引入core-js和regenerator-runtime来完成polyfill的使用

```shell
npm install core-js regenerator-runtime -D
```



如果我们需要再项目中使用polyfill，那么我们需要在babel.config.js文件中进行配置，给preset-env配置一些属性

```js
module.exports = {
  presets: [
    ['@babel/preset-env', {
      'corejs': 3, // 指定安装的core-js的版本是V3.x.x
      // 设置以什么样的方式来使用polyfill
      // 可选值:
      // 1. false - 打包后的文件不使用polyfill来进行适配
      //          - 此时设置corejs是无效的
      //          - 因为此时相当于没有设置corejs和useBuiltIns
      'useBuiltIns': false
    }]
  ]
}
```

```js
module.exports = {
  presets: [
    ['@babel/preset-env', {
      'corejs': 3,
      // 当使用usage，会根据源代码中出现的语言特性，自动检测所需要的polyfill
      // 这样可以确保最终包里的polyfill数量的最小化，打包的包相对会小一些
      // 1. 这里引入的polyfill仅仅值包括代码中所使用到的新特性，第三方库中的新特性并不会引入对应的polyfill
      // 2. 假设使用了String.prototype.includes这个新特性，而目标浏览器不支持该特性
      //    那么会自动引入string相关的所有polyfill
      'useBuiltIns': 'usage' // === 推荐设置
    }]
  ]
}
```

```js
module.exports = {
  presets: [
    ['@babel/preset-env', {
      'corejs': 3,
      // 如果我们依赖的某一个库本身使用了某些polyfill的特性
      // 如果我们使用的是usage，转换之后用户浏览器可能会报错
      // 此时可以将useBuiltIns的值设置为entry
      // 并在项目的入口文件添加下如下代码:
      // import 'core-js/stable'
      // import 'regenerator-runtime/runtime'
      // 此时webpack在构建的时候, 会从入口文件出发
      // 根据 browserslist 目标导入所有的polyfill
      // 但是此时会导致构建后的文件体积变得很大 --- 不推荐
      'useBuiltIns': 'entry'
    }]
  ]
}
```



| 可选值 | 说明                                                         |
| ------ | ------------------------------------------------------------ |
| usage  | 只引入实际使用的特性所需的 polyfill<br />推荐                |
| entry  | 显式引入所有可能需要的 polyfill<br />会导致文件体积变大，且需要再入口文件中单独引入core-js和regenerator-runtime<br />不推荐 |


