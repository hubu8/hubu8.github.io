# Promise与Async/Await的区别


<!--more-->

#### 一、什么是promise，及其作用

什么是：
Promise是ES6中的一个内置对象，实际是一个构造函数

特点：

①三种状态：pending（进行中）、resolved（已完成）、rejected（已失败）。只有异步操作的结果可以决定当前是哪一种状态，任何其他操作都不能改变这个状态。

②两种状态的转化：其一，从pending（进行中）到resolved（已完成）。其二，从pending（进行中）到rejected（已失败）。只有这两种形式的转变。

③Promise构造函数的原型对象上，有then()和catch()等方法，then()第一个参数接收resolved()传来的数据，catch()第一个参数接收rejected()传来的数据

作用：

①通常用来解决异步调用问题

②解决多层回调嵌套的方案

③提高代码可读性、更便于维护

#### 二、promise的使用

```js
function getJSON() {
  return new Promise((resolve, reject) => {
    setTimeout(() => {
      let json = Math.random() * 2
      if (json > 1) {
        resolve(json)
      } else {
        reject(json)
      }
    }, 2000)
  })
}

const makeRequest = () =>
  getJSON()
    .then(data => {
      console.log('data==>', data)
      return 'done'
    })
    .catch(err => {
      console.log('err==>', err)
    })

makeRequest()
```

#### 三、什么是Async/Await，及其作用

什么是：

①async/await是ES8新特性

②async/await是写异步代码的新方式，以前的方法有回调函数和Promise

③async/await是基于Promise实现的，它不能用于普通的回调函数

④async/await与Promise一样，是非阻塞的

⑤async/await使得异步代码看起来像同步代码，这正是它的魔力所在

#### 四、Async/Await的使用

```js
function getJSON() {
  return new Promise((resolve, reject) => {
    setTimeout(() => {
      let json = Math.random() * 2
      if (json > 1) {
        resolve(json)
      } else {
        reject(json)
      }
    }, 2000)
  })
}

const makeRequest = async () => {
  const value = await getJSON()
  console.log(value)
  return value
}

makeRequest()
```

#### 五、Async/await 比 Promise 更优越的表现

1、简洁干净：使用 async/await 能省去写多少行代码

2、错误处理：async/wait 能用相同的结构和好用的经典 try/catch 处理同步和异步错误，错误堆栈能指出包含错误的函数。

3、调试：async/await 的一个极大优势是它更容易调试，使用 async/await 则无需过多箭头函数，并且能像正常的同步调用一样直接跨过 await 调用。

#### 六、总结

Async/await进一步优化了Promise.then()的缺点，使代码更简洁，所以在项目中尽量使用Async/await
