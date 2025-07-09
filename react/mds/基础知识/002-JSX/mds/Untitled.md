为了让代码更清晰，我们在 render 方法里用小括号 () 把渲染内容包起来，表示这是一个整体。

```jsx
root.render(
  <div>
    <h2>Hello World</h2>
    <button>修改文本</button>
  </div>
);
```



```jsx
root.render(
  <div>
    <h2>{message}</h2>
    <button onClick={handleClick}>修改文本</button>
  </div>
);
```

在JSX里，动态值要用大括号 {} 包裹, 无论属性还是动态值

用 onClick（注意是驼峰命名，大写 C）：



因为React不会自动重新渲染。

件有两种类型：类组件和函数组件

，它只是普通类，不是 React 组件。要让它变成组件，需要让它继承 React.Component。继承后，这个类就有了 React 组件的能力。组件里一般需要三部分：数据（state）、方法（处理事件）和渲染逻辑（render 函数）。接下来

在 App 组件里定义一个固定的方法，叫 render。这个方法名不能随便改，必须叫 render。在这个 render 方法里，你返回啥，React 就渲染啥

state 是 React 组件用来存储动态数据的地方，专门用来管理会影响界面渲染的数据。



在 React 里，root.render 可以渲染 HTML 元素，也可以渲染组件。要创建组件，我们用 class 定义，并且让它继承 React.Component。类名必须大写，比如 App，否则 React 会以为是 HTML 元素。组件里必须实现 render 方法，返回要渲染的 JSX。

数据分两种：一种是会影响界面更新的，比如显示的文本；另一种不影响界面，比如一些配置项。会影响更新的数据要放进 state 里。

会影响界面更新的数据叫“参与数据流”，必须放进组件的 state。



在类组件里，除了构造函数和 render 方法，我们还可以定义其他实例方法。

定义好后，在 render 里给按钮绑定 onClick={this.btnClick}

在 React 组件里，只需用 this.setState 方法，它会自动完成这两步：修改数据和触发重新渲染。



当 onClick={this.btnClick} 时，React 调用 btnClick，但 this 是 undefined，因为方法被传递时失去了上下文， React 调用时没有绑定 this。

+ 可以用 bind 方法，比如 onClick={this.btnClick.bind(this)}，把 this 绑定到组件实例。
  + render 里的 this 是组件实例，bind(this) 就是把 btnClick 的 this 绑定到这个实例，确保事件回调能访问 this.setState。
+ 另一种绑定 this 的方法是在构造函数里写 this.btnClick = this.btnClick.bind(this)，提前绑定好 this。
  + 这和在 render 里用 bind 的效果一样，只是绑定时机不同。
  + 构造函数绑定只需一次，代码稍简洁，



setState 方法来自 React.Component，它会更新 state 并触发 render。setState 内部做了两件事：1. 修改指定的 state 值；2. 重新调用 render 更新界面。

状态更新后，React 自动调用 render，用最新的 message 值渲染界面。

setState 方法可以部分更新状态



在普通的JavaScript里，事件绑定时，this通常指向触发事件的DOM节点。但在React中，情况不同。你写的button最终会被React转成一个React Element对象。调用事件处理函数时，React不会自动帮你绑定this

---

---

React要求render返回一个JSX元素，且只能有一个根节点，所以我们用一个div包裹内容。

JSX本质上会被转成React.createElement调用，生成React元素

直接写`<App />`而不是App，是因为我们需要渲染组件实例，而不是直接传类。



JSX会自动渲染数组里的每个元素。

---

---



JSX代码看起来像HTML，但它本质上是JavaScript的一部分，因为它是JavaScript的对象结构。
