## Redux Toolkit的数据不可变性

在React开发中，我们总是会强调数据的不可变性:

+ 无论是类组件中的state，还是redux中管理的state
+ 事实上在整个JavaScript编码过程中，数据的不可变性都是非常重要的

因此我们经常会进行浅拷贝来完成某些操作，但是浅拷贝事实上也是存在问题的

+ 比如过大的对象，进行浅拷贝也会造成性能的浪费
+ 比如浅拷贝后的对象，在深层改变时，依然会对之前的对象产生影响

而Redux Toolkit底层使用了immerjs的一个库来保证数据的不可变性

immer.js 使用了一个新的算法:  Persistent Data Structure(持久化数据结构或一致性数据结构)

即使用一种数据结构来保存数据，当数据被修改时，会返回一个对象，但是新的对象会尽可能的利用之前的数据结构而不会 对内存造成浪费

![Persistent Data Structure](https://mmbiz.qpic.cn/mmbiz_gif/O8xWXzAqXuuPtxc2VNSb80zpYnIGMuvn6vRJMGliaqLp8wWNEgKOVutM4vjiaiaGD0iba6tYMQ8DFV8MsYzC7via0bg/640?wx_fmt=gif)





## 自定义connect函数

```jsx
import { PureComponent } from "react"
import store from '../store'

export function connect(mapStateToProps, mapDispatchToProps) {
	return function(Cpn) {
		return class enhanceCpn extends PureComponent {
			constructor(props) {
				super(props)

				// 调用mapStateToProps方法，返回的就是我们所实际需要使用的store中的数据
				this.state = mapStateToProps(store.getState())
			 }

			 componentDidMount() {
				store.subscribe(() => {
					// 一旦发生更新重新执行mapStateToProps得到最新的state
					this.setState(mapStateToProps(store.getState()))
				})
			 }

			render() {
				let mapState = {}
        let mapDispatch = {}

				if (typeof mapStateToProps === 'function') {
					mapState = mapStateToProps(store.getState())
				}

        if (typeof mapDispatchToProps === 'function') {
          mapDispatch = mapDispatchToProps(store.dispatch)
        }

				return <Cpn { ...this.props } { ...mapState } {...mapDispatch} />
			}
		}
	}
}
```



虽然这么做依旧模拟实现了connect函数的功能，但是该函数存在一个致命的问题，那就是它需要自己主动引入store

也就是说该文件和store的位置是强耦合的

为此我们还需要模拟实现一些Provider组件

`context.js`

```jsx
import { createContext } from 'react'
export const StoreCtx = createContext()
```



`Provider.jsx`

```jsx
import React, { PureComponent } from 'react'
import { StoreCtx } from './context'

export class Provider extends PureComponent {
	render() {
		return (
			<StoreCtx.Provider value={this.props.store}>
				{ this.props.children }
			</StoreCtx.Provider>
		)
	}
}

export default Provider
```



`/src/index.jsx`

```jsx
import React from 'react';
import ReactDOM from 'react-dom/client';
import { Provider } from './utils';
import App from './App';
import store from './store'

const root = ReactDOM.createRoot(document.getElementById('root'))

root.render(
  <React.StrictMode>
    <Provider store={store}>
			<App />
		</Provider>
  </React.StrictMode>
)
```



`使用store中数据的组件`

```jsx
import { PureComponent } from "react"
import { StoreCtx } from './Provider'

export function connect(mapStateToProps, mapDispatchToProps) {
	return function(Cpn) {
		return class enhanceCpn extends PureComponent {
			static contextType = StoreCtx

      // 构造方法有第二个参数，为指定的context
			constructor(props, context) {
				super(props)

				this.state = mapStateToProps(context.getState())
			 }

			 componentDidMount() {
				this.context.subscribe(() => {
					this.setState(mapStateToProps(this.context.getState()))
				})
			 }

			render() {
				let mapState = {}
        let mapDispatch = {}

				if (typeof mapStateToProps === 'function') {
					mapState = mapStateToProps(this.context.getState())
				}

        if (typeof mapDispatchToProps === 'function') {
          mapDispatch = mapDispatchToProps(this.context.dispatch)
        }

				return <Cpn { ...this.props } { ...mapState } {...mapDispatch} />
			}
		}
	}
}
```