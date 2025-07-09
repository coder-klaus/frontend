## 依赖

| 依赖        | 说明                                                         |
| ----------- | ------------------------------------------------------------ |
| `react`     | 基于 `createElement` 调用生成虚拟DOM（vdom）                 |
| `react-dom` | `react-dom/client` 负责将 vdom 渲染为真实 DOM<br />`react-dom/server` 负责将 vdom 转换为 HTML 字符串 |
| `babel`     | 将 JSX 语法转换为 createElement 调用                         |



## 基本使用

> 注意⚠️:
>
> 从 React 19 开始，官方不再发布 UMD 包，因此**无法通过 CDN 方式引入 React 进行开发调试**，只能通过 npm 安装依赖。
>
> React 18 及之前版本仍可通过 CDN 引入。

```html
<!DOCTYPE html>
<html lang="en">
<head>
  <meta charset="UTF-8">
  <meta name="viewport" content="width=device-width, initial-scale=1.0">
  <title>Document</title>
  <!-- 注入全局对象 React -->
  <script src="https://unpkg.com/react@18/umd/react.development.js"></script>
  <!-- 注入全局对象 ReactDOM -->
  <script src="https://unpkg.com/react-dom@18/umd/react-dom.development.js"></script>
  <!-- Babel 用于解析 JSX-->
  <script src="https://unpkg.com/@babel/standalone/babel.min.js"></script>
</head>
<body>
  <!--
    #root => 挂载点
    + 渲染后内容会作为其innerHTML被插入
    + 所以挂载点中内容是应用，而挂载点本身不是
  -->
  <div id="root"></div>

  <!-- type="text/babel" 表示脚本先被babel解析处理后，再交给浏览器进行解析 -->
  <script type="text/babel">
    const msg = <h1>Hello World</h1>

    // 界面挂载 => 注意: createRoot中的参数是DOM，不是css selector string
    const root = ReactDOM.createRoot(document.getElementById('root'))
    root.render(msg)
  </script>
</body>
</html>
```



## babel

- React 本身**不支持 JSX**，必须用 Babel 先将 JSX 转换为 `createElement` 调用。
- 如果直接用 `createElement` 编写代码，则无需 Babel，但相对的代码可读性和维护性较差

因此虽然`babel`在 React 开发过程中并不是必须的，但实际应用中，还是使用 `JSX + babel`组合的方式进行开发



**直接用 createElement 的写法如下:**

```html
<script>
  const msg = React.createElement('div', null,
    React.createElement('h1', null, 'Hello World'),
    React.createElement('p', null, 'This is a paragraph')
  );
  const root = ReactDOM.createRoot(document.getElementById('root'));
  root.render(msg);
</script>

```

从上面的例子可以看出

1. JSX 其实就是 JavaScript的语法扩展，让我们可以在 JavaScript 中 编写 类HTML 代码片段
2. 这些 类 HTML 代码片段 会转换为 `React.createElement`形式的方法调用
3. 调用`React.createElement`方法后，会返回 VDOM，而VDOM 就是 React 内部用于描述对应DOM的轻量级原生JavaScript对象
4. 因此，类HTML片段的本质还是 JavaScript对象

1. JSX 是 JavaScript 的语法扩展， ，允许我们在 JavaScript 代码中直接编写类似 HTML 的结构。
2. 这些“类 HTML”结构只是开发时的“语法糖”，最终都会被 Babel 转换为 `React.createElement(...)` 调用
3. `React.createElement` 返回一个虚拟 DOM（VDOM）对象，其本质上就是React 内部用于描述 对应DOM结构的 轻量级原生JavaScript对象
4. 因此，类HTML片段 的本质依旧是 原生JavaScript对象



## 挂载点

`React`允许你存在多个挂载点，每个挂载点管理页面的某一个部分。

每个挂载点都可以渲染不同的内容，挂载点和挂载点之间是完全独立的。

不过，在实际开发中，这种做法非常少见。通常建议只使用一个挂载点来统一管理整个 Web 应用。

```html
<!DOCTYPE html>
<html lang="en">
<head>
  <meta charset="UTF-8">
  <meta name="viewport" content="width=device-width, initial-scale=1.0">
  <title>Document</title>
  <script src="https://unpkg.com/react@18/umd/react.development.js"></script>
  <script src="https://unpkg.com/react-dom@18/umd/react-dom.development.js"></script>
  <script src="https://unpkg.com/@babel/standalone/babel.min.js"></script>
</head>
<body>
  <div id="root"></div>
  <div id="app"></div>

  <script type="text/babel">
    ReactDOM.createRoot(document.getElementById('root')).render(<h1>Hello World</h1>)
    ReactDOM.createRoot(document.getElementById('app')).render(<h1>Hello React</h1>)
  </script>
</body>
</html>
```



## 额外补充

###  渲染方式

  `React18`引入了并发渲染模式，由于并发渲染模式的引入，推荐的挂载方式也发生了相应的改变

+ **React 18 及以后：**

  ```jsx
  ReactDOM.render(<h1>Hello World</h1>, document.querySelector('#root'));
  ```

+ **React 18 及以后：**

  ```jsx
  const root = ReactDOM.createRoot(document.querySelector('#root'));
  root.render(<h1>Hello World</h1>);
  ```



#### 并发渲染

并发渲染模式 「 **Concurrent Rendering** 」是指 React 把原本需要一次性完成的渲染任务，拆分成许多更小的任务模块 「 时间片 」，并为这些任务模块设置相应的执行优先级。

这样做的好处是，如果在渲染过程中有更高优先级的任务（比如用户输入或者点击），React 可以在当前时间片结束后，优先处理这些高优先级任务。等高优先级任务处理完毕后，再继续之前的渲染，从而提升应用的响应速度和流畅度。



举例来说，如果你在输入框打字，同时页面上有一个很大的列表正在渲染

+ 用老的同步渲染模式，整个页面会“卡住”，直到列表渲染完成，输入才会有响应。
+ 在并发渲染下，React 会**优先响应你的输入**，列表渲染会被“暂停”，等输入事件处理完了再继续渲染列表。



只有使用 `ReactDOM.createRoot` 挂载的 React 应用，才能启用 React 18 的并发渲染能力。

通过`ReactDOM.render`挂载的应用默认**不支持并发渲染**，它只会以同步模式渲染。

这也就是为什么虽然我们依旧可以使用`ReactDOM.render`来挂载应用，但控制台会抛出警告⚠️的原因