在编程的时候，有一个共识: `错误出现的越早越好`

1. 能在写代码的时候发现错误，就不要在代码编译时再发现
2. 能在代码编译期间发现错误，就不要在代码运行期间再发现
3. 能在开发阶段发现错误，就不要在测试期间发现错误
4. 能在测试期间发现错误，就不要在上线后发现错误



但是因为JavaScript没有对我们传入的参数进行任何的限制，所以对应的错误只能等到运行阶段才能被发现

而对于JavaScript而言，一旦代码出现了错误，影响后续代码的继续执行，也就是整个项目都因为一个小小的错误而深入崩溃

而TypeScript的出现就是为了解决JavaScript没有类型检测机制这个问题

![image.png](https://s2.loli.net/2022/10/08/1YboeztLgCDNdjl.png) 



TypeScript是拥有类型的JavaScript超集，它可以编译成普通、干净、完整的JavaScript代码

1. TypeScript是加强版的JavaScript

2. JavaScript所拥有的特性，TypeScript全部都是支持的，并且它紧随ECMAScript的标准，所以ES6、ES7、ES8等新语法标准，它都是支持的

3. TypeScript在实现新特性的同时，总是保持和ES标准的同步甚至是领先

4. 在语言层面上，TypeScript不仅仅增加了类型约束，而且包括一些语法的扩展，比如枚举类型(Enum)、元组类型(Tuple)等

5. TypeScript最终会被编译成JavaScript代码，所以你并不需要担心它的兼容性问题，在编译时也可以不借助于Babel这样的工具

   （可以使用babel将TypeScript编译为原生的JavaScript代码，也可以使用tsc将ts编译为原生的js代码）



当一个项目使用TypeScript的好处

1. 在保存的时候，会自动对代码进行类型检测，如果类型不正确，就会出现错误警告，便于我们在编写代码的时候就可以直接修正对应的错误，而不需要再等到运行的时候才去发现对应的错误
2. 因为TypeScript拥有代码的类型机制，所以在调用函数或者方法，尤其是第三方库的函数或方法的时候，可以给予我们更好的提示信息



`使用JS编写代码`

```js
function baz(foo) {
  console.log(foo.length)
}

// 这些是正确的调用
baz('hello')
baz([1, 2, 3])

// 这些是错误的调用
// 但是这里的错误调用在编写代码的时候并不能被及时发现
// 只能运行代码后，才可以在浏览器的控制台中看到对应的错误
baz(123)
baz()
```



`使用TS编写代码`

```ts
// ts代码是完全兼容js代码的
// 因为ts本质上就是对于js的扩展，ts是添加了类型检验系统的js
// 所以在ts中可以编写原本的js代码，但是这样对应的变量的类型会被默认识别为any
// 也就是说直接在ts中编写js代码会导致ts失去类型检测的功能，所以不推荐

// { length: number } 就是typescript中的类型注解
// 用于约束参数foo的类型，表示传入的参数必须拥有length属性，且length属性的值必须是数值类型
function baz(foo: { length: number }) {
  console.log(foo.length)
}

baz('hello')
baz([1, 2, 3])

// 使用ts后，下面两行代码在代码编写的时候就已经出现错误提示了
baz(123)
baz()
```



## 安装

浏览器默认是不能直接识别TypeScript，浏览器默认只能直接识别JavaScript

但是TypeScript可以编译出纯净、 简洁的JavaScript代码，并且可以运行在任何浏览器上、Node.js环境中和任何支持ECMAScript 3(或更高版本)的 JavaScript引擎中

所以TypeScript最终会被编译成JavaScript来运行， 因此我们常说`TypeScript始于JavaScript，归于JavaScript`

```shell
# 安装命令
npm install typescript -g

# 查看版本
tsc --version
```



## 初体验

```ts
// 默认情况下，编写的ts代码的编译作用域是全局
// 所以为了避免出现类型冲突，会加上export {} 以告诉ts当前文件是一个模板文件
export {}

// 声明了类型后TypeScript就会进行类型检测，声明的类型可以称之为类型注解(Type Annotation)
// let/const 变量: 类型注解 = 值
// string - 字符串类型
// String - 字符串对应的包装类类型实例
const str: string = 'Hello World'
console.log(str)
```

```shell
# 将ts文件编译为js文件
tsc ./index.ts 
```

```js
// 编译后的js文件 默认会开启严格模式
"use strict";
// 下面这行代码是为了保证ts的编译作用域是模块 而不是全局
// 如果需要在浏览器中直接运行，需要移除该行代码
exports.__esModule = true;
var str = 'Hello World';
console.log(str);
```



但是实际使用中，我们不可能每编写一个对应的ts文件，就手动将其转换为对应的js文件

实际开发中

1. 如果需要运行在浏览器上，可以通过webpack，配置本地的TypeScript编译环境和开启一个本地服务，可以直接运行在浏览器上
2. 如果只是进行简单的测试，可以通过ts-node库，为TypeScript的运行提供执行环境

```shell
# ts-node需要依赖 tslib 和 @types/node
npm install tslib @types/node -g
npm install ts-node -g

# 基本使用
# ts-node xxx.ts
```



## 类型推断

在开发中，有时候为了方便起⻅我们并不会在声明每一个变量时都写上对应的数据类型

在绝大多数情况下，ts都可以正确的帮助我们推导出对应变量的类型

```ts
// 如果是变量 那么推导出的类型是通用类型
let str = 'Hello Wolrd' // str --- stirng

// 如果是常量 那么推导出的类型是字符串类型
const str = 'Hello World' // str --- Hello World
```



## 数据类型

### 和js相同的类型

| 类型            | 说明                                                         |
| --------------- | ------------------------------------------------------------ |
| number          | 数字类型是我们开发中经常使用的类型，TypeScript和JavaScript一样，不区分整数类型(int)和浮点型(double)，统一为number类型<br />和JS一样，ts中的数值类型支持二进制、八进制、十六进制表示 |
| boolean         | 只有true和false两种类型                                      |
| string          | string类型是字符串类型，可以使用单引号或者双引号或反引号表示 |
| 数组            | 在实际开发中，数组中的每一个数组项的数据类型应该是相同的<br />在TypeScript，定义数组的方式有两种<br />1. `<T>[]`<br />2. `Array<T>` |
| 对象            | object对象类型可以用于描述一个对象<br />但是object表示的是通用对象，也就是说object类型只能调用公共的属性和方法<br />如果一个对象上有自己特有的属性和方法的时候，使用object进行类型声明是没有办法进行调用的<br />推荐使用接口或者泛型来定义一个对象的数据类型 |
| symbol          | 可以使用symbol类型注解来定义一个symbol类型的变量             |
| null和undefined | null 和 undefined 各自的类型也是undefined和null，也就意味着它们既是实际的值，也是自己的类型 |



```ts
// 函数的参数需要指定对应的类型注解 - 因为ts没有办法进行类型推导 - 指定完类型的同时，参数的个数也会一并被确定
// 函数的返回值 ts可以进行类型推导，所以可以省略 - 一些第三方库为了可读性，可能并不省略对应的返回值类型
function sum(num1: number, num2: number): number {
  return num1 + num2
}
```



```ts
const nums = [1, 2, 3]

// 对于一些会被回调的匿名函数，对应的函数参数可以被正确推导
// 因为TypeScript当前回调函数的上下文正确推导出对应的数据类型
// 所以该类型也被称之为上下文类型(contextual typing)
nums.forEach(num => console.log(num))
```



### ts单独扩展的类型

| 类型    | 说明                                                         |
| ------- | ------------------------------------------------------------ |
| any     | 在某些情况下，我们确实无法确定一个变量的类型，并且可能它会发生一些变化，这个时候我们可以使用any类型<br />any类型就是不限制当前变量的类型，也不限制对当前变量的任何操作 |
| unknown | unknown是TypeScript中比较特殊的一种类型，它用于描述类型不确定的变量<br />unknown类型和any类型类似，可以给unknown类型的变量赋值任何的类型<br />但是对于unknown类型的任何操作都是非法的 |
| void    | 如果一个函数没有返回值，那么该函数的返回值就是void<br />在JS中，一个函数没有返回值，那么这个函数的默认返回值就是undefined<br />所以我们可以将undefined赋值给void类型 |
| never   | never 表示永远不会发生值的类型<br />如果一个函数中是一个死循环或者抛出一个异常，那么函数的返回值就是never |
| tuple   | tuple是元组类型<br />元组类型是一种特殊的数组，元组类型必须满足如下条件：<br />1、元组的个数是确定的<br />2、元组中每一个位置上的数据对应的数据类型也是确定的<br />所以在元组中可以存放多个不同数据类型的元素，且通过索引去获取对应元素值的时候可以正确的知道对应的值的数据类型 |



`unknown`

```js
let foo: unknown

foo = 'hello'

// 可以通过typeof对类型进行缩小操作，也就是将宽泛的类型缩小为更为具体的类型
// 类型缩小后 foo的类型就从unknown缩小为了string类型，此时就是对foo进行相应的操作
if (typeof foo === 'string') {
  console.log(foo.length)
}
```



```js
//  当基于上下文的类型推导(Contextual Typing)推导出返回类型为 void 的时候，并不会强制函数一定不能返回内容
const foos = [1, 2, 3]

foos.forEach(item => {
  console.log(item)

  // 在类型推导中 forEach函数的回调函数的返回值是void
  // 但是因为该类型是类型推导出的类型，并不是实际定义的类型
  // 所以在这里ts允许我们自己返回其余的数据类型
  // 但是这么做是不推荐的 
  return item
})
```



`never`

```ts
function foo(arg: number | string) {
  switch(typeof arg) {
    case 'number':
      console.log('number')
      break

    case 'string':
      console.log('string')
      break

      // 使用never可以确保将arg所有可用的值类型都进行对应的类型校对
      // 如果有任意一个类型校对没有完成，那么arg的类型就不会是never
      // 那么default中的逻辑代码就会报错
    default:
      let n: never = arg
      }
}
```



`tuple`

```ts
// 元组类型 最为常用的地点是 函数的返回值
// 该函数的返回值需要手动指定，如果不是手动指定，那么ts默认推导会将该函数的返回值推导为数组，而不是元组
function setState<T>(value: T): [T, (value: T) => void] {
  let v = value

  function setV(newV: T) {
    v = newV
  }

  // TS默认推导 该函数的返回值为 (T | (value: T) => void)[]
  // 对应的类型是数组类型，那么通过索引去获取对应的值的时候，并不能准确知道对应的值是T还是(value: T) => void
  // 所以在该例中，需要手动显示指定函数的返回值类型是元组类型，而不是数组类型
  return [v, setV]
}
```



