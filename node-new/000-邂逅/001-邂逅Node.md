Node 使用 V8 引擎（与 Chrome 浏览器相同）来执行 JavaScript 代码。

由于不同操作系统的底层系统调用各不相同，需要一个中间层来统一这些调用。在 Node 中，这个中间层就是用 C 语言实现的 libuv。

Node 在解析和运行 JavaScript 代码时，遇到像文件操作、网络请求等系统调用，会自动通过 libuv 调用相应的 API 来实现这些功能。

libuv 负责维护事件循环（event loop），所以 Node 中许多 API 都是基于事件机制设计的。

Node 通过 libuv 发起系统调用，调用的结果会被放入事件队列。Node 会在合适的时机从事件队列中取出结果，并执行对应的回调函数。

由于使用了 libuv，Node 实现了跨平台，无论是在 Windows、macOS 还是 Linux，Node 都能提供统一的 API 进行调用。

![image.png](https://s2.loli.net/2025/06/28/vPxOH2qSfj7rpoR.png) 