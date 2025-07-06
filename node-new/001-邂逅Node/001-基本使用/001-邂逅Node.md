Node.js 是一个基于 V8 引擎的 JavaScript 运行时环境，使得 JavaScript 能够脱离浏览器，在服务器端或本地环境中运行，例如开发服务器程序或自动化脚本等。

Node.js 的底层架构不仅包含了 V8 引擎，还集成了 libuv 库。libuv 作为 Node.js 与操作系统之间的桥梁，负责处理文件操作、网络通信等底层功能，实现了高效的异步 I/O 能力。

本质上，Node.js 是一个主要用 C++ 实现的程序，核心通过嵌入 V8 引擎来解析和执行 JavaScript 代码。它的整体结构可以分为以下几层：

- **上层**：通过 JavaScript 向开发者提供丰富的内置 API，方便操作底层功能。
- **中层**：利用 V8 引擎负责解析和执行 JavaScript，这部分主要由 C++ 实现。
- **底层**：通过 libuv 实现异步操作和系统调用，这部分主要使用 C 语言编写。

V8 引擎本身既可以独立运行，也可以被嵌入到其他 C++ 程序中。所以，不论是在 Chrome 浏览器还是在 Node.js 中，V8 都负责 JavaScript 的解析和执行。它们的 JavaScript 代码执行流程基本一致：先将源码解析成 token，再生成抽象语法树（AST），随后编译为字节码，最后经过优化生成高效的机器码。

在 Node.js 环境下，开发者编写的 JavaScript 代码首先由 V8 引擎解析和执行。当涉及到异步操作（如文件读写、网络请求）时，Node.js 的 C++ 层会将这些任务交由 libuv 处理。libuv 提供了事件驱动机制和线程池，能够高效地管理各种异步 I/O 操作，从而实现 Node.js 的高性能和非阻塞特性。



## 应用场景

1. 包管理工具
2. web服务器
3. SSR「 服务端渲染 」
4. 桌面应用 「 Electron 」
5. 脚本工具 「 脚手架 」



## 版本

+ LTS（Long Term Support，长期支持版）
+ Current（最新版）



## 安装

```shell
# 通过查看 node 版本，判断 node 是否安装成功
node -v # v24.1.0

# 安装 node 的同时，会自动安装包管理工具 npm（Node Package Manager）
npm -v # 11.3.0
```



## 运行

```shell
# 运行脚本
node app.js

# 调试脚本 「 使用 Chrome 可视化的 node devTool 」
node --inspect app.js

# 直接输入node回车
# 就会进入 Node 的 REPL（Read-Eval-Print Loop，交互式解释器）环境
# 简单来说，这就是 Node.js 提供的一个快速测试node代码的交互式环境
# 退出方式 1）连续按两次 Ctrl+C；2）输入 .exit 并回车
node
```



### nodemon

Node.js 脚本发生改变后，**Node 不会自动重启并加载最新脚本**。

- 需要你**手动重启**
- 或者使用第三方工具 `nodemon` 实现自动重启

```shell
nodemon app.js
```



### pm2

在开发环境中，通常会用 `nodemon` 来自动重启 Node.js 应用，但 `nodemon` 依赖于当前的控制台窗口。如果关闭控制台，应用进程也会随之终止。这种情况下，推荐使用 `pm2` 来管理你的 Node.js 项目。

**PM2** 能够将 Node.js 脚本以守护进程（即后台常驻进程）的方式运行。即使关闭终端窗口，应用依然会在后台持续运行，非常适合生产环境。此外，PM2 还提供了日志管理、进程监控、开机自启动、负载均衡等丰富功能。

```shell
pm2 start app.js              # 启动 app.js
pm2 start app.js --name <app_name> # 启动并指定进程名

pm2 stop <app_name|id>       # 停止应用
pm2 restart <app_name|id>    # 重启应用
pm2 delete <app_name|id>     # 删除应用
pm2 stop all                 # 停止所有应用
pm2 restart all              # 重启所有应用
pm2 delete all               # 删除所有应用

pm2 list                     # 显示所有进程状态
pm2 logs <app_name|id>       # 查看某个应用的日志记录
```



#### 集群

PM2 提供了集群（Cluster）模式，可以将多个相同功能的进程组合在一起，对外作为一个整体服务。

这种方式主要用于**负载均衡**( Load Balancing ) ，即自动将请求分配给集群中相对空闲的节点，从而避免单个进程过载，提高应用的可用性和并发能力。



#### 集群结构与工作流程

- **主进程（master）**：负责管理和分发请求，不直接处理业务逻辑。
- **工作进程（worker）**：负责实际处理用户请求和业务逻辑，属于集群成员。

例如，以下命令会启动一个主进程和四个工作进程：

```shell
pm2 start app.js -i 4
```



**请求处理流程如下：**

1. 客户端发起请求，由主进程接收。
2. 主进程采用轮询（Round Robin）等方式，查找空闲的 worker，并将请求分发给它。
   + 所谓轮询就是master每一个worker发生查询请求，以判断那个worker处于空闲状态
3. worker 进程处理请求，并将结果通过主进程返回给客户端。

整个过程中，主进程主要负责调度和管理，**实际的业务处理都在 worker 进程中完成**。



虽然 PM2 集群模式适合在单机多核环境下提升 Node.js 应用的性能，但在实际生产环境中，通常会采用更专业的负载均衡工具，比如 **Nginx** 或 **Kubernetes**。这些工具不仅支持更灵活的流量分发策略，还能实现自动扩缩容、健康检查等高级功能，更适合大规模、高可用的业务场景。



### n

在实际开发中，常常需要维护多个项目，而这些项目可能依赖不同版本的 Node.js。为了方便在不同的 Node 版本之间切换，我们通常会借助一些版本管理工具：

- **Windows 推荐使用：nvm-windows**
- **Mac 推荐使用：n 「 推荐 」 或 nvm 「 Node Version Manager 」**

```shell
n --version      # 通过查看版本 判断是否安装成功
n                # 切换node版本 「 也可以查看安装了那些node版本 」
sudo n           # 查看列表可能不需要管理员权限，但是切换node版本可能需要管理员权限
n ls             # 列出已安装的 Node 版本

n latest         # 安装并使用最新的 Node 版本
n lts            # 安装并使用最新的 LTS 版本
n <version>      # 安装并使用指定版本，如 n 16.20.0
```

