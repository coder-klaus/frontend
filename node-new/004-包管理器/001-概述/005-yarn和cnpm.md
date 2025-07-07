## yarn 

早期的 `npm` 存在以下几个问题：

1. **没有高效的缓存机制**，每次安装依赖时都会重新从远程仓库下载，无论依赖是否已经存在本地。
2. **重复下载同一依赖**，即使不同的库依赖同一个版本的库，也会被多次下载。
3. **多项目不能共享依赖**，在多个项目中使用同一个版本的依赖时，每个项目都会单独下载一份，无法共用。

为了解决这些问题，Facebook 推出了 `yarn`，它可以简单理解为“带有缓存机制的 npm”。yarn 通过本地缓存和更高效的依赖管理，大大提升了安装速度和资源利用率。

值得注意的是，从 npm 5.0 版本开始，npm 也引入了缓存机制和锁文件（`package-lock.json`），这些功能在一定程度上弥补了之前的不足。

```shell
# 安装yarn
npm install -g yarn

# 查看是否安装成功
yarn --version
```

```shell
# 初始化
yarn init
yarn init -y

# 安装
yarn add axios # 单个包
yarn add axios day.js # 多个包
yarn add webpack -D
yarn add webpack -g

# 清除缓存
yarn cache clean

# 强制重新安装全部依赖 => 更推荐的做法是 rm -rf node_modules && yarn install
yarn install --force

# 删除依赖
yarn remove webpack

# 升级依赖
yarn upgrade

# 强制重新安装
yarn install --force
```



## cnpm

npm 默认使用 `https://registry.npmjs.org/` 作为包仓库地址，但由于网络原因，国内访问速度较慢。

为了解决这个问题，淘宝团队搭建了一个 npm 镜像服务器，每隔十分钟与官方仓库同步一次数据，极大提升了国内用户的下载速度。

我们可以通过修改 npm 的源地址，让其从淘宝镜像下载依赖：

```shell
npm config set registry https://registry.npmmirror.com
```



有时候，我们希望优先使用官方源，只有在官方源下载速度慢或失败时，才切换到淘宝镜像。这种情况下，可以使用 cnpm（即 Chinese npm）。

cnpm 的使用方法很简单，只需将 `npm` 命令替换为 `cnpm` 即可。



这样，你可以根据实际情况选择使用 npm（官方源）或 cnpm（淘宝镜像源）。两者不会自动切换，需要你手动选择。

- 使用 `npm` 命令时，会根据当前配置访问官方源或你设置的源。
- 使用 `cnpm` 命令时，会始终使用淘宝镜像源（无需手动设置）。

