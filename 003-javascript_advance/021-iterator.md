**迭代器**(iterator)是帮助我们`对某个数据结构或容器对象(container，例如链表或数组)进行遍历`的`对象`， 其行为类似于数据库中的光标

使用迭代器遍历某一个对象的时候，我们不需要关心被遍历的对象的内部实现细节

在各种编程语言的实现中，迭代器的实现方式各不相同，但是基本都有迭代器，比如Java、Python等

在JS中如果一个对象正确实现了的next方法，那么我们就可以认为这个对象实现了迭代器协议(iterator protocol)， 而这个对象就可以被认为是迭代器对象

这个next方法需要满足如下几点:

1. 一个无参数或者一个参数的函数
2. 该对象需要返回如下两个属性

| 属性  | 说明                                                         |
| ----- | ------------------------------------------------------------ |
| done  | boolean类型值<br />done主要用于标识该迭代器是否将对象遍历完毕 |
| value | 迭代器返回的任何 JavaScript 值<br />当done为true时，也就是迭代完成时，对应的值为undefined<br />此时value属性可以省略 |

```js
const users = ['Klaus', 'Alex', 'Steven']

function createIterator(arr) {
  let index = 0

  return {
    next() {
      return {
        done: index >= arr.length,
        value: arr[index++]
      }
    }
  }
}

const iterator = createIterator(users)

console.log(iterator.next()) // => { done: false, value: 'Klaus' }
console.log(iterator.next()) // => { done: false, value: 'Alex' }
console.log(iterator.next()) // => { done: false, value: 'Steven' }
console.log(iterator.next()) // => { done: true, value: undefined }
// 迭代器 迭代完毕后，依旧可以继续调用next方法
// 只不过返回值都是 { done: true, value: undefined }
console.log(iterator.next()) // => { done: true, value: undefined }
```

当然，并不是每一个迭代器都可以将数据遍历完毕，存在无限的迭代器，只不过比较少见

```js
function createIterator() {
  let index = 0

  return {
    next() {
      return {
        done: false,
        value: index++
      }
    }
  }
}

const iterator = createIterator()

console.log(iterator.next()) // => { done: false, value: 0 }
console.log(iterator.next()) // => { done: false, value: 1 }
console.log(iterator.next()) // => { done: false, value: 2 }
console.log(iterator.next()) // => { done: false, value: 3 }
console.log(iterator.next()) // => { done: false, value: 4 }
```



## 可迭代对象

在`iterable protocol`中, 如果一个对象正确实现了`@@iterator`方法的时候, 这个对象就被称之为可迭代对象，而`@@iterator`方法就是这个迭代这个对象时候需要使用到的迭代器

所以迭代器和可迭代对象并不是同一种东西

在JS中，`@@iterator`对应的就是`Symbol.iterator`方法，也就是说如果一个对象正确实现了`Symbol.iterator`方法，那么这个对象就被称之为可迭代对象

```js
const iterableObj = {
  users: ['Klaus', 'Alex', 'Steven'],
  // iterableObj 是可迭代对象
  // 这里的Symbol.iterator是迭代iterableObj时候使用到的迭代器
  [Symbol.iterator]() {
    let index = 0

    return {
      // next方法在这里需要使用箭头函数
      // 目的是为了next函数内部可以正常访问到正确的this
      next: () => ({
        done: index >= this.users.length,
        value: this.users[index++]
      })
    }
  }
}

const iterator = iterableObj[Symbol.iterator]()

console.log(iterator.next())
console.log(iterator.next())
console.log(iterator.next())
console.log(iterator.next())
```



## for - of

任意一个可迭代对象，都可以使用`for ... of`方法遍历可迭代对象中的值

可以认为`for ... of`是直接调用next方法进行遍历的一种语法糖

事实上我们平时创建的很多原生对象已经实现了可迭代协议，也就是说他们本身就是可迭代对象

String、Array、Map、Set、伪数组对象(如arguments对象、NodeList集合)在默认情况下都是一个可迭代对象

```js
const iterableObj = {
  users: ['Klaus', 'Alex', 'Steven'],
  [Symbol.iterator]() {
    let index = 0

    return {
      next: () => ({
        done: index >= this.users.length,
        value: this.users[index++]
      })
    }
  }
}

for (const value of iterableObj) {
  console.log(value)
}
```

这也就是为什么对象默认情况下，不能直接使用`for ... of`进行遍历，因为对象默认情况下并不是一个可迭代对象

```js
const user = {
  name: 'Klaus',
  age: 23
}

// 以下代码会直接报错 --- 是不合法的
// 注意是 对象默认不可以使用for...of进行遍历
// 不是for...in 对象默认是可以使用for...in 进行遍历属性名的
for (const value of user) {
  console.log(value)
}
```

```js
// 需要注意的是，虽然对象默认情况下不可以使用for...of遍历
// 但是对象是可以使用展开运算符和解构操作的
// 这是ES9(ES2018)中对对象进行的特殊处理，其内部使用的并不是迭代器进行实现

const user = {
  name: 'Klaus',
  age: 23
}

const { name, age } = user
console.log(name, age) // => Klaus 23

console.log({...user}) // => { name: 'Klaus', age: 23 }
```



### for - in vs for - of

| 迭代方式         | 说明                                                         |
| ---------------- | ------------------------------------------------------------ |
| for key in xxx   | 用于迭代对象的属性<br />for - in可以迭代一个`对象及其原型链上`的所有`可迭代`属性<br />for - in迭代出的属性是有序的(先是对象自身的再是对象的原型上的，依次类推) |
| for value of xxx | 用于迭代可迭代对象<br />迭代本质调用的是可迭代对象的`[Symbol.iterator]`方法 |



## 使用场景

1. JavaScript中语法: for ...of、展开语法(spread syntax)、yield*、解构赋值(Destructuring_assignment)
2. 创建一些对象时: new Map([Iterable])、new WeakMap([iterable])、new Set([iterable])、new WeakSet([iterable])
3. 一些方法的调用: Promise.all(iterable)、Promise.race(iterable)、Array.from(iterable)

```js
const iterableObj = {
  users: ['Klaus', 'Alex', 'Steven'],
  [Symbol.iterator]() {
    let index = 0

    return {
      next: () => ({
        done: index >= this.users.length,
        value: this.users[index++]
      })
    }
  }
}

// 可迭代对象可以使用for ... of循环
for (const value of iterableObj) {
  console.log(value)
}

// 可迭代对象可以使用展开运算符
console.log([...iterableObj]) // => [ 'Klaus', 'Alex', 'Steven' ]

// 可迭代对象可以使用解构运算符
const [user1, user2, user3] = iterableObj
console.log(user1, user2, user3) // => Klaus Alex Steven
```

```js
const iterableObj = {
  users: ['Klaus', 'Alex', 'Steven'],
  
  [Symbol.iterator]() {
    let index = 0

    return {
      next: () => ({
        done: index >= this.users.length,
        value: this.users[index++]
      })
    }
  }
}

// 某些对象在创建的时候，也可以传入迭代器作为创建时候的参数
const set = new Set(iterableObj)
console.log(set) // => Set(3) { 'Klaus', 'Alex', 'Steven' }

// 某些方法的参数也可以接收可迭代对象
Promise.all(iterableObj).then(res => console.log(res)) // => [ 'Klaus', 'Alex', 'Steven' ]
// 等价于
/*
  // 基本数据类型会使用Promise.resolve方法进行包裹
  Promise.all([
    Promsie.resolve('Klaus'),
    Promsie.resolve('Alex'),
    Promsie.resolve('Steven')
  ]).then(res => console.log(res))
*/
```



## 自定义类的迭代

我们知道Array、Set、String、Map等类创建出来的对象默认都是可迭代对象

因此我们也可以通过实现类上的`Symbol.iterator`方法，从而确保该类所创建出来的对象默认是可迭代的

```js
class School {
  constructor(students) {
    this.students = students
  }

  push(student) {
    this.students.push(student)
  }

  // 因为School类实现了正确的Symbol.iterator方法
  // 所以使用School类创建出来的方法默认都是可迭代的
  // 对School类的实例进行遍历的时候，默认会依次取出对应students数组中的各个值
  [Symbol.iterator]() {
    let index = 0

    return {
      next: () => ({
        done: index >= this.students.length,
        value: this.students[index++]
      })
    }
  }
}

const school = new School(['Alex', 'Klaus', 'Steven'])

school.push('Jhon')

for (const stu of school) {
  console.log(stu)
}
/*
  =>
    Alex
    Klaus
    Steven
    Jhon
*/
```



## 迭代器的中断

迭代器在某些情况下会在没有完全迭代的情况下中断

比如遍历的过程中通过break、continue、return、throw中断了循环操作

也就是说和forEach之类的方法不同，使用for-of之类的方法迭代可迭代对象的时候，是可以提前中止的

```js
const iterableObj = {
  users: ['Klaus', 'Alex', 'Steven'],
  [Symbol.iterator]() {
    let index = 0

    return {
      next: () => ({
        done: index >= this.users.length,
        value: this.users[index++]
      })

      // 如果迭代器在遍历的时候被终止了，那么会执行return函数
      // 以下是默认的return函数
      // 如果我们需要在iterator在遍历被终止的时候执行某些操作可以在return回调中编写对应的逻辑
      // return() {
      //   return {
      //     done: true,
      //     value: undefined
      //   }
      // }
    }
  }
}

for (const user of iterableObj) {
  if (user === 'Alex') {
    return
  } else {
    console.log(user)
  }
}
```

当解构一个可迭代对象的时候，当迭代完成后，对应的break方法也会被执行

```js
const iterableObj = {
  users: ['Klaus', 'Alex', 'Steven'],
  [Symbol.iterator]() {
    let index = 0

    return {
      next: () => ({
        done: index >= this.users.length,
        value: this.users[index++]
      }),

      return() {
        console.log('iterator breaked')

        return {
          done: true,
          value: undefined
        }
      }
    }
  }
}

// 即使所有的值全部被解构完全了，return方法依旧会被触发
const [user1, user2, user3] = iterableObj
console.log(user1, user2, user3)
```

