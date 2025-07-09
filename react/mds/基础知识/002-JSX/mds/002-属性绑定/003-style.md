HTML 中，`style` 属性的值是字符串，多个样式用分号分隔

```html
<div style="color: red; font-size: 20px;">lorem</div>
```

而在 JSX 里，`style` 必须是一个样式对象，不能直接写字符串，否则会报错。

```jsx
<div style={{ color: 'red', fontSize: '20px' }}>lorem</div>
```

其中，外层括号表示使用插值语法，内层括号表示值是JavaScript对象



## 编写规则

1. 多单词组成的样式名推荐小驼峰写法（如 `fontSize`）
   + 这么做是为了方便底层通过 JavaScript 操作这些样式
2. 可以使用中划线 (如`font-size`)
   + 不推荐，控制台会抛出警告⚠️
   + 样式名必须使用引号包裹，因为它不是有效JavaScript变量名
3. 属性值如果是 `px`，可以直接写数字，React 会自动补全单位

```jsx
<div style={{ color: 'red', fontSize: 20 }}>lorem</div>
```

则最终渲染结果为

```jsx
<div style="color: red; font-size: 20px;">lorem</div>
```

