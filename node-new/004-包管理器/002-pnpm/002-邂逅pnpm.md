## 硬链接

`pnpm` 是一款高性能的 JavaScript 包管理器，其名字来源于 “Performant npm” 「 高性能的`npm` 」

与传统的 `npm` 和 `yarn` 不同，`pnpm`通过硬链接机制，大幅提升了磁盘空间利用率。



传统的 `npm` 和 `yarn` 都有一个全局缓存目录（如 `~/.npm`），在安装依赖时，会将包解压并复制到每个项目的 `node_modules` 目录。这意味着如果多个项目依赖同一个包的同一版本，每个项目的 `node_modules` 都会有一份完整的拷贝，造成磁盘空间浪费。



而 `pnpm` 也有一个全局的缓存目录（默认位于 `~/.pnpm-store`，通常称为 `store` 目录），但它不仅存放压缩包，还存放了解压后的内容。当项目安装依赖时，`node_modules` 下的文件不会真正复制一份，而是通过**硬链接**指向 `store` 目录中的实际文件。这样，不论有多少项目依赖同一个包的同一版本，磁盘上只会有一份真实的数据资源。

此外，`pnpm` 还能进一步优化：如果同一个包的不同版本之间有相同的文件内容，这部分内容在 `store` 中也只会存储一份，通过硬链接实现共享；只有不同的部分才会单独存储。而不同的包即使文件内容相同，依然会分别存储。

这种机制极大地节省了磁盘空间，并提升了依赖安装的速度。





## 非扁平化node_modules

早期`npm`和`yarn`使用的是非扁平`node_modules`，所有的包都被提升到模块目录的根目录。

但经常出现项目中多个库同时依赖于同一个库的情况，而非扁平依赖会导致这个共同依赖的库需要被多次安装

于是，目前`npm`和`yarn`将`node_modules`的所有库都提升到了同一个层级，即扁平化依赖

```shell
npm install axios
```

此时`node_moudles`中目录结构如下

```shell
.
├── asynckit
├── axios
├── call-bind-apply-helpers
├── combined-stream
├── delayed-stream
├── dunder-proto
├── es-define-property
├── es-errors
├── es-object-atoms
├── es-set-tostringtag
├── follow-redirects
├── form-data
├── function-bind
├── get-intrinsic
├── get-proto
├── gopd
├── has-symbols
├── has-tostringtag
├── hasown
├── math-intrinsics
├── mime-db
├── mime-types
└── proxy-from-env
```

可以看到，`axios`的依赖包被提升到了和`axios`相同层级

此时依旧会存在一个额外的问题

```js
import FormData from "form-data";

const formData = new FormData();

console.log(formData.getHeaders())
/*
  {
    'content-type': 'multipart/form-data; boundary=--------------------------384095632135317811642477'
  }
*/
```

此时会发现虽然`form-data`并没有在`package.json`中显示声明安装，但依旧可以使用

这是因为`axios`依赖了`form-data`。而`form-data`被提升到了`node_modules`下，此时按照模块查找规则就可以找到`form-data`并使用

这种没有安装，但因为模块的扁平化机制可以使用的库被称之为 **幽灵依赖** 或者叫 **隐式依赖**



而`pnpm`通过硬链接加上非扁平化依赖解决了这个问题

![image.png](https://s2.loli.net/2025/07/08/MTxNhVdcXPQk1wr.png) 

```shell
├── node_modules
│   └── axios -> .pnpm/axios@1.10.0/node_modules/axios
```

可以看到`pnpm`安装后，`node_modules`中，只有一个`axios`，没有`axios`依赖的其它库

`node_modules/axios`本身也是一个软链接，指向了`.pnpm`下具体版本的`axios`

此时进入`node_modules/axios@1.10.0`, 可以看到

```shell
.
└── node_modules
    ├── axios
    ├── follow-redirects -> ../../follow-redirects@1.15.9/node_modules/follow-redirects
    ├── form-data -> ../../form-data@4.0.3/node_modules/form-data
    └── proxy-from-env -> ../../proxy-from-env@1.1.0/node_modules/proxy-from-env
```

其中`axios`是具体的硬链接，指向`.pnpm store`中实际存储的位置

而`axios`的依赖则以软连接的形式继续链接到`.pnpm`下的对应资源



如果你打印出`.pnpm`的第一层目录

```shell
.
├── asynckit@0.4.0
├── axios@1.10.0
├── call-bind-apply-helpers@1.0.2
├── combined-stream@1.0.8
├── delayed-stream@1.0.0
├── dunder-proto@1.0.1
├── es-define-property@1.0.1
├── es-errors@1.3.0
├── es-object-atoms@1.1.1
├── es-set-tostringtag@2.1.0
├── follow-redirects@1.15.9
├── form-data@4.0.3
├── function-bind@1.1.2
├── get-intrinsic@1.3.0
├── get-proto@1.0.1
├── gopd@1.2.0
├── has-symbols@1.1.0
├── has-tostringtag@1.0.2
├── hasown@2.0.2
├── lock.yaml
├── math-intrinsics@1.1.0
├── mime-db@1.52.0
├── mime-types@2.1.35
├── node_modules
└── proxy-from-env@1.1.0
```

会发现和`npm`的扁平化模块结构基本类型

但这样非常方便使用非扁平化结构避免了幽灵依赖，使用硬链接避免了非扁平化结构中同一个库的多次重复安装
