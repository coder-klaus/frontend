## className

因为 `class` 是 JavaScript 的关键字，所以在 JSX 中需要用 `className` 属性来代替 HTML 的 `class`



如果直接写 `class`，React 会在控制台警告⚠️ ，但代码仍然可以运行。

```jsx
<h2 className="foo bar">Hello World</h2>
```

渲染结果：

```html
<h2 class="foo bar">Hello World</h2>
```



## 动态 class 绑定

与 Vue、Svelte 不同，React 不支持在 className 字符串中直接插入表达式。因此，像下面这样写是**无效的**：

```jsx
<div className="foo bar {this.state.isActive ? 'active' : 'inactive'}">lorem</div>
```

这样写会把整个内容当作字符串处理，表达式不会被解析。

所以最终会被渲染为

```jsx
<div class="foo bar {isActive ? 'active' : 'inactive'}">lorem</div>
```



如果要在 React 中动态绑定 class 有三种方式

1. 模板字符串拼接
2. 数组 join 拼接
3. 使用第三方库 classnames



### 模板字符串拼接

```jsx
<div className={`foo bar ${this.state.isActive ? 'active' : 'inactive'}`}>lorem</div>
```

如果`this.state.isActive` 的值为 `true`，则渲染结果为

```jsx
<div class="foo bar active">lorem</div>
```



### 数组的`join`拼接

```jsx
<div className={['foo', 'bar', this.state.isActive ? 'active' : 'inactive'].join(' ')}>lorem</div>
```

如果`this.state.isActive` 的值为 `true`，则渲染结果为

```html
<div class="foo bar active">lorem</div>
```



### 使用第三方库 classnames

首先安装：

```shell
# 这个库是全小写的
npm install classnames
```

然后导入：

```js
import classNames from 'classnames';
```

使用方式 类似于 Vue 的 class属性动态绑定 写法

```jsx
<div className={classNames('foo', 'bar', { active: this.state.isActive })}>lorem</div>
```

如果`this.state.isActive` 的值为 `true`，则渲染结果为

```jsx
<div class="foo bar active">lorem</div>
```



#### classnames

- 支持多种参数类型（字符串、对象、数组等）。
- 对象写法：key 为 class 名，value 为布尔值，true 时添加该 class。
- **falsy 值**（如 false、null、undefined、0、''） 和 **true** 会被自动过滤掉。
- 数组参数会被展开取出每一项，依次按照前三条规则进行判断



**对象形式示例：**

```jsx
<div className={classNames('foo', 'bar', { active: this.state.isActive })}>lorem</div>
```

如果`this.state.isActive` 的值为 `true`，则渲染结果为

```html
<div class="foo bar active">lorem</div>
```



**数组形式示例：**

```jsx
<div className={classNames(['foo', 'bar', true, false], [{ active: this.state.isActive }])}>lorem</div>
```

如果`this.state.isActive` 的值为 `true`，则渲染结果为

```jsx
<div class="foo bar active">lorem</div>
```



**混合参数示例**

```jsx
<div className={classNames(0, true, false, 1, 'foo', 'bar', { active: this.state.isActive })}>lorem</div>
```

如果`this.state.isActive` 的值为 `true`，则渲染结果为

```jsx
<div class="1 foo bar active">lorem</div>
```

