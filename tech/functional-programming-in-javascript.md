---
title: functional-programming-in-javascript
slug: functional-programming-in-javascript
author: [jianchengwang]
date: 2019-12-05
excerpt: "今天瞄了下just enough functional programming in javascript视频感觉还不错，如果你也感兴趣的话"
draft: false
tags: [frontend]
---

今天瞄了下`just enough functional programming in javascript`视频感觉还不错，如果你也感兴趣的话, [请戳](<https://egghead.io/courses/just-enough-functional-programming-in-javascript>)

函数式编程是一种编程的模式，在这种编程模式中最常用的函数和表达式。它强调在编程的时候用函数的方式思考问题，函数也与其他数据类型一样，处于平等地位。可以将函数作为参数传入另一个函数，也可以作为别的函数的返回值。函数式编程倾向于用一系列嵌套的函数来描述运算过程。

以下是一些学习记录跟总结，关于上述视频以及部分扩展，主要就是学习一下函数编程的思想，其他语言应该都是大同小异。

### Higher Order Functions

JavaScript的函数其实都指向某个变量。既然变量可以指向函数，函数的参数能接收变量，那么一个函数就可以接收另一个函数作为参数，这种函数就称之为高阶函数。

```js

// Higher Order Functions

// 1. Accept a function as an argument
// 2. Return a new function

const withCount = fn => {
  let count = 0

  return (...args) => {
    console.info(`Call count: ${++count}`)
    return fn(...args)
  }
}

const add = (x, y) => x + y

const countAdd = withCount(add)

console.info(countAdd(1, 2)) // Call count: 1 3
console.info(countAdd(1, 2)) // Call count: 2 3
console.info(countAdd(1, 2)) // Call count: 3 3
```

Javascript 的 `Array` 对象就提供了很多常用的高阶函数，如下所示：

这里我们简单列一下，有个印象即可，在后面的章节中会阐述函数编程的一些概念跟设计原理，到时候就会有一个比较清楚的认知。

#### map

我们可以根据传进去函数对数组每个元素进行相同的操作，当然我们也可以通过写一个循环计算出[请戳](<https://egghead.io/courses/just-enough-functional-programming-in-javascript>)结果，但是，这样看起来就不是那么的一目了然。抽象运算规则，会让代码更加的简洁明了。

```js
const pow = x => x * x
console.info([1, 2, 3, 4, 5, 6, 7, 8, 9].map(pow)) 
// [ 1, 4, 9, 16, 25, 36, 49, 64, 81 ]
```

#### reduce

这个函数必须接收两个参数，`reduce()`把结果继续和序列的下一个元素做累积计算，其效果就是：

```js
[x1, x2, x3, x4].reduce(f) = f(f(f(x1, x2), x3), x4)
```

这里简单举个求和的例子

```js
console.info([1, 2, 3, 4, 5, 6, 7, 8, 9].reduce((x, y) => x + y)) // 45
```

下面是谷歌关于`MapReduce`的描述，[原文地址](<https://ai.google/research/pubs/pub62>)

```html
MapReduce is a programming model and an associated implementation for processing and generating large data sets. Users specify a map function that processes a key/value pair to generate a set of intermediate key/value pairs, and a reduce function that merges all intermediate values associated with the same intermediate key. Many real world tasks are expressible in this model, as shown in the paper.

Programs written in this functional style are automatically parallelized and executed on a large cluster of commodity machines. The run-time system takes care of the details of partitioning the input data, scheduling the program's execution across a set of machines, handling machine failures, and managing the required inter-machine communication. This allows programmers without any experience with parallel and distributed systems to easily utilize the resources of a large dist fair to say, but... it sure hasn't seemed like as mainstream of a concept in the overall developer world until perhaps theributed system.

Our implementation of MapReduce runs on a large cluster of commodity machines and is highly scalable: a typical MapReduce computation processes many terabytes of data on thousands of machines. Programmers find the system easy to use: hundreds of MapReduce programs have been implemented and upwards of one thousand MapReduce jobs are executed on Google's clusters every day.
```

#### filter

filter也是一个常用的操作，它用于把`Array`的某些元素过滤掉，然后返回剩下的元素。

和`map()`类似，`Array`的`filter()`也接收一个函数。和`map()`不同的是，`filter()`把传入的函数依次作用于每个元素，然后根据返回值是`true`还是`false`决定保留还是丢弃该元素。

```js
console.info([1, 2, 3, 4, 5, 6, 7, 8, 9].filter((element, index, self) => {
  console.log(element); // 依次打印元素
  console.log(index); // 依次打印元素位置，从０开始
  console.log(self); // self就是变量arr
  return self % 2 == 0;
}))
```

#### sort

默认的排序是将所有元素转换为`String`类型，然后字符串根据根据ASCII码进行排序，例如

```js
console.info(['Google', 'Apple', 'Microsoft'].sort()) // ['Apple', 'Google', 'Microsoft'];
console.info(['Google', 'apple', 'Microsoft'].sort()) // ['Google', 'Microsoft", 'apple']
console.info([10, 20, 1, 2].sort()) // [1, 10, 2, 20]
```

很多情况下，这显然不符合我们的预期，所以我们可以传入一个函数编写符合我们预期的排序逻辑，sort()`方法会直接对`Array`进行修改，它返回的结果仍是当前`Array

```js
let arr = [10, 20, 1, 2]
let sortedArr = arr.sort((x, y) => x - y)
console.info(sortedArr) //  [1, 2, 10, 20]
console.info(sortedArr == arr) // true
```

#### every

`every()`方法可以判断数组的所有元素是否满足测试条件。

例如，判断一个数组里是否都能被2整除

```js
console.info([10, 20, 1, 2].every(s => s % 2 == 0)) // false
```

#### find & findIndex

`find()`方法用于查找符合条件的第一个元素，如果找到了，返回这个元素，否则，返回`undefined`：

`findIndex()`和`find()`类似，也是查找符合条件的第一个元素，不同之处在于`findIndex()`会返回这个元素的索引，如果没有找到，返回`-1`：

```js
console.info([10, 20, 1, 2].find(s => s % 2 == 1)) // 1
console.info([10, 20, 1, 2].findIndex(s => s % 2 == 1)) // 2
```

#### forEach

`forEach()`和`map()`类似，它也把每个元素依次作用于传入的函数，但不会返回新的数组。`forEach()`常用于遍历数组，因此，传入的函数不需要返回值：

注意，函数编程的中间操作都不会影响原数组，所以你`forEach`里改变遍历元素的值，原数组是不会变的。

```js
console.info([10, 20, 1, 2].forEach(s => console.info(s))) // undefined
let arr1 = [10, 20, 1, 2];
arr1.forEach(s => s = s % 2);
console.info(arr1) // [ 10, 20, 1, 2 ]
```

### Immutable Data

函数编程中使用不可变数据是必须的，可变的数据是不安全的，我们不知道在哪一环节数据突然改变，导致最终结果跟预期不一致，所以我们对数据的转换不应该影响原始的数据源，而应该创建返回更新后新的数据，这种方法纯粹而且线程安全，通俗来说就是要保证相同的输入会产生相同的输出，这也是函数编程很重要一个特性跟思想。

下面我们看一下 `mutable data` 的 示例代码

```js
const a = [1, 2, 3]
const b = a
console.info(b === a) // true
b.push(4)
console.info(a) // [1, 2, 3, 4]

const c = {foo: 'bar'}
const d = c
console.info(d === c) // true
d.foo = 'baz'
console.info(c) // {foot: 'baz'}
```

下面是 `immutable data` 的简单实现，一般都是新建一个新的数据返回，

```js
const push = value => array => {
    const clone = [...array]
    clone.push(value)
    return clone
}

const push1 = value => {
    return function(array) {
        const clone = [...array]
        clone.push(value)
        return clone
    }
}

const e = [1, 2, 3]
const f = push(4)(e)
console.info(e)
console.info(f === e)
```

`...` 是ES6的一个操作符`Spread syntax`，如果你想对ES6操作符有更多的了解，请访问 [MDN](<https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Operators/Spread_syntax>)

`push(4)(e)` 如果不了解使用的话，这里简单说明下，

上述代码中 `push` 的写法即使等价于 `push1` ，通常就是使用匿名单参数函数来实现 多参数函数的方法，

所以 `push(4)(e) = push1(4)(e) = (push1(4))(e)` 

具体请查看下一小结 `Currying`会有比较详细的介绍。

### Currying

这里有关于`Currying`的简单介绍，详细请看下[wili](<https://wiki.haskell.org/Currying>)

Currying is the process of transforming a function that takes multiple arguments in a tuple as its argument, into a function that takes just a single argument and returns another function which accepts further arguments, one by one, that the original function would receive in the rest of that tuple.

柯里化（Currying），又称部分求值（Partial Evaluation），是把接受多个参数的函数变换成接受一个单一参数（最初函数的第一个参数）的函数，并且返回接受余下的参数而且返回结果的新函数的技术。

```js
function add(x) {
    return function(y) {
        return x + y
    }
}

const addThree = add(3)
console.info(addThree(10)) // 13
console.info(addThree(20)) // 23
console.info(add(3)(30)) // 33

const add2 = x => y => x + y
const addThree2 = add2(3)
console.info(addThree2(10)) // 13
console.info(addThree2(20)) // 23
console.info(add2(3)(30)) // 33

```

我们可以很容易地实现参数复用，

下面我们可以写一个简单的柯里化实现方法

```js
function currying(fn) {
    __args = [].slice.call(arguments, 1);
    return function () {
        var __inargs = [].slice.call(arguments);
        return fn.apply(null, __args.concat(__inargs));
    };
}

function square(i) {
    return i * i;
}

function double(i) {
    return i *= 2;
}

function map(handeler, list) {
    return list.map(handeler);
}

var mapSQ = currying(map, square);
console.info(mapSQ([1, 2, 3, 4, 5])); //[1, 4, 9, 16, 25]


var mapDB = currying(map, double);
console.info(mapDB([1, 2, 3, 4, 5])); //[2, 4, 6, 8, 10]
```

关于`slice` 的用法

```js
[].slice === Array.prototype.slice; // true
[].slice.call(arguments)能将具有length属性的对象，比如这里arguments对象转成数组：
```

如果想更深入的了解，请戳[MDN](<https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/Array/slice>)

当然 `curring` 还有其他用处，比如延迟加载，提前返回等等，这里就不多叙述。另外`curring` 会影响性能，但是在现代浏览器中一般可以忽略不计。

### Partial Application

通过`Curring` 我们可以很容实现，局部调用(`partial application`)，简单来说，只传递给函数一部分参数来调用它，让它返回一个函数去处理剩下的参数，能大大减少我们的样板代码

```js
const fetch = require('node-fetch')

const getFromAPI = baseUrl => endPoint => cb => 
    fetch(`${baseUrl}${endPoint}`)
    .then(res => res.json())
    .then(data => cb(data))
    .catch(err => {
        console.error(err.message)
    })

const getGithub = getFromAPI('https://api.github.com')
const getGithubUsers = ("/users")

getGithubUsers(data => {
    console.info(data)
    console.info(data.map(user => user.login))
})
```

我们可以创建很多重用的功能，使得我们的代码更加的简洁跟易用。

### Pointfree

无参编程，将命令函数作为参数传递，以避免使用临时变量来编写匿名函数，可以提高代码的可读性，降低错误率，使我们的代码更易于组合跟单元测试。

```js
const arr = [1, 2, 3]
const double = x => x * 2

// no pointfree
console.info(arr.map(x => x * 2)) // [ 2, 4, 6 ]

// pointfree
console.info(arr.map(double)) // [ 2, 4, 6 ]
```

### Composition

`var compose = (f, g) => x => f(g(x));`

我们常常需要组合简单的函数来实现复杂的功能，在某种意义上来说，组合是函数的嵌套，将上一个函数的输出结果作为输入参数传递给下一个函数。

```js
const f = x => x + 2
const g = x => x * 3

console.info(f(g(5))) // 17

const scream = str => str.toUpperCase()
const excliam = str => `${str}!`
const repeat = str => `${str} ${str}`

console.info(
    repeat(excliam(scream('i love egghead')))
) // I LOVE EGGHEAD! I LOVE EGGHEAD!
```

如果简单用函数嵌套的话，看起来就不是那么简洁明了，而且后面假如需要改变组合顺序，我们还得去修改这边函数嵌套的顺序，很麻烦。

我们通常会实现一个高阶函数 `compose` 接受我们想要组合的函数，然后返回一个新函数以方便我们在程序中进行调用：

```js
const compose = (...fns) => x => 
    fns.reduceRight((acc, fn) => fn(acc), x)

const writeExuberance = compose(
    repeat,
    excliam,
    scream
)

console.info(writeExuberance('i love egghead'))
```

`注意：要传给 compose 函数是有规范的，首先函数的执行是从最后一个参数开始执行，一直执行到第一个，而且对于传给 compose 作为参数的函数也是有要求的，必须只有一个形参，而且函数的返回值是下一个函数的实参。`

对于 `compose ` 从最后一个函数开始求值的方式如果你不是很适应的话，你可以通过 pipe 函数来从左到右的方式。

```js
const pipe = (...fns) => x => fns.reduce((acc, fn) => fn(acc), x)

const writeExuberance2 = pipe(
    scream,
    excliam,
    repeat
)

console.info(writeExuberance2('i love egghead'))
```

### Argument Order

柯里化函数参数的顺序应该使我们应用程序受益，提高可重用性并实现函数的组合，所以一般原则是，将数据参数作为函数的最后一个结果提供，以便将一个函数的结果作为参数传递给另一个函数中，同时也方便我们进行局部调用

```js
const map = array => cb => array.map(cb)
const map_ = cb => array => array.map(cb)

const arr = [1, 2, 3, 4, 5]
const double = x => x * 2

const withArr = map(arr)
console.info(withArr(double))
console.info(withArr(n => n * 3))
console.info(arr.map(double))

const withDouble = map_(double)
console.info(withDouble(arr))
console.info(withDouble([1, 3, 9]))

// most specific => lest specific

const prop = key => obj => obj[key]

const propName = prop('name')

const peope = [
    {name: 'Jamon'},
    {name: 'Shirley'},
    {name: 'Ben'},
    {name: 'kent'}
]

console.info(map_(propName)(peope))

```

如上面的例子，显然数据参数 `array` 放在函数参数 `cb` 后面，更具有重用性。

### 相关链接

[廖雪峰javascript编程](<https://www.liaoxuefeng.com/wiki/1022910821149312>)

[egghead.io:just enough functional programming](<https://egghead.io/courses/just-enough-functional-programming-in-javascript>)

[mostly-adequate-guide](<https://mostly-adequate.gitbooks.io/mostly-adequate-guide/>)

[mostly-adequate-guide-chinese](<https://llh911001.gitbooks.io/mostly-adequate-guide-chinese>)

[Functional-Light-JS](<https://github.com/getify/Functional-Light-JS>)

[creating-an-es6ish-compose-in-javascript](<https://medium.com/@dtipson/creating-an-es6ish-compose-in-javascript-ac580b95104a>)