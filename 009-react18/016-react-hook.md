Hook 是 React 16.8 的新增特性，Hook本质上就是一系列的函数

Hook可以让我们在不编写class的情况下使用state以及其他的React特性

Hook的出现基本可以代替我们之前所有使用class组件的地方

如果是一个旧的项目，你并不需要直接将所有的代码重构为Hooks，因为它完全向下兼容，你可以渐进式的来使用它, 例如原本的组件依旧使用class组件，而新的组件使用hooks

Hook只能在函数组件中使用，不能在类组件，或者函数组件之外的地方使用

简而言之，hook的编写比类组件更为的简单方便，同时为函数式组件提供了状态管理和生命周期等扩展性新功能

![image.png](https://s2.loli.net/2022/09/12/yMTwJjEYHc6frei.png)



## 计数器

### 使用类组件来实现

```jsx
import React, { PureComponent } from 'react'

export class App extends PureComponent {
	constructor(props) {
		super(props)

		this.state = {
			count: 0
		}
	}

	changeCount(step) {
		this.setState({
			count: this.state.count + step
		})
	}

	render() {
		const { count } = this.state

		return (
			<div>
				<h1>{ count }</h1>
				<button onClick={() => this.changeCount(1)}>+1</button>
				<button onClick={() => this.changeCount(-1)}>-1</button>
			</div>
		)
	}
}

export default App
```



### 使用函数式组件+hook来实现

```jsx
// useState来自react，需要从react中导入，它是一个hook
import React, { memo, useState } from 'react'

const App = memo(props => {
	// 使用useState方法在函数式组件中定义状态
  // 我们可以在一个组件中定义多个变量和复杂变量(数组、对象)
	// 参数值是初始渲染时候使用的默认值 - 默认值 undefined
	// 参数的是数组
	// + 数组第一项 - 对应的state状态值
	// + 数组第二项 - 对应的设置state的方法
	// 						- 当使用该方法修改了state值后，组件重新渲染，并且根据新的值返回DOM结构
	const [count, setCount] = useState(0)

	return (
		<div>
			<h1>{ count }</h1>
			<button onClick={() => setCount(count + 1)}>+1</button>
			<button onClick={() => setCount(count - 1)}>-1</button>
		</div>
	)
})

export default App
```



通过上述示例，我们可以明显的得出`函数式组件结合hooks让整个代码变得非常简洁,并且再也不用考虑this相关的问题`

使用Hook有两条基本原则：

1. 只能在函数最外层调用 Hook。不要在循环、条件判断或者子函数中调用
2. 只能在 React 的函数组件中调用 Hook。不能在其他 JavaScript 函数中调用
3. React允许我们在自定义Hook函数中去调用其它hooks方法，但不允许在普通函数中去调用其它hooks函数
   + tips: 当前仅当一个函数的函数名类似于useXXX的时候，React才会将该函数看成是自定义hook



State Hook的API就是 useState， useState会帮助我们定义一个 state变量，useState 是一种新方法，它与 class 里面的 this.state 提供的功能完全相同

一般来说，函数组件在退出后变量就会”消失”，所以state 中的变量实质上会被 React 保留

也就是说在第一次的时候，会生成对应的状态，react会将对应状态进行存储

在下次渲染函数式组件的时候，react会自动取出对应状态以供渲染

一但我们调用设置state的方法，react会重新执行对应的函数式组件

并使用最新的状态生成新的ReactElement对象，并重新进行渲染



所以，在渲染过程中，seState 总是返回给我们当前的 state，所以Hook 的名字总是以 use 开头，因为我们往往都是在使用对应的hook所存储的值
