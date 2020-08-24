---
layout: post
title: JavaScript 异步编程
---



## 回调函数

在用到Ajax 向后端请求数据时，都会用到回调函数。

````
ajax(url, () => {
    // 处理逻辑
})
````

上面的箭头函数就时回调函数，ajax 请求成功后，回调函数就会执行

但是回调函数有一个致命的弱点，就是容易写出回调地狱（Callback hell）。假设多个请求存在依赖性，你可能就会写出如下代码：

````
ajax(url, () => {
    // 处理逻辑
    ajax(url1, () => {
        // 处理逻辑
        ajax(url2, () => {
            // 处理逻辑
        })
    })
})
````

以上的代码，每个 ajax 请求都依赖上一个请求结果，一层嵌套一层，不断往里嵌套，这样的代码看起来不利于阅读和维护。

回调带来的问题就是：

- 代码臃肿。
- 可读性差。
- 耦合度过高，可维护性差。
- 代码复用性差。
- 容易滋生 bug。
- 只能在回调里处理异常。

当然，回调函数还存在着别的几个缺点，比如不能使用 `try catch` 捕获错误，不能直接 `return`。

## Promise

Promise 是异步编程的一种解决方案，比传统的异步解决方案【回调函数】和【事件】更合理、更强大。现已被 ES6 纳入进规范中。

````
new Promise(url1)
    .then(url2(请求结果1))
    .then(ur3(请求结果2))
    .then(url4请求结果3))
    .catch(处理异常(异常信息))
````

相比较于一下这种写法和上面的回调式的写法。我们不难发现，Promise 的写法更为直观，并且能够在外层捕获异步函数的异常信息。

Promise 的常用 API 如下：

* Promise.resolve(value)

  >类方法，该方法返回一个以 value 值解析后的 Promise 对象 
  >
  >1、如果这个值是个 thenable（即带有 then 方法），返回的 Promise 对象会“跟随”这个 thenable 的对象，采用它的最终状态（指resolved / rejected / pending / settled）
  > 2、如果传入的 value 本身就是 Promise 对象，则该对象作为 Promise.resolve 方法的返回值返回。
  > 3、其他情况以该值为成功状态返回一个 Promise 对象。

  ````
  let p0 = new Promise(function(resolve, reject) {
  	setTimeout(function () {
  		resolve(123)
  	}, 3000)
  })
  let p1 = Promise.resolve(p0)
  
  console.log(p0 === p1) // true
  ````

  返回一个状态已变成 resolved 的 Promise 对象。

  ````
  let p1 = Promise.resolve(123); 
  
  console.log(p1)
  //打印p1 可以看到p1是一个状态置为resolved的Promise对象
  ````

* Promise.reject

  >类方法，且与 resolve 唯一的不同是，返回的 promise 对象的状态为 rejected。

* Promise.prototype.then

  > 实例方法，为 Promise 注册回调函数，函数形式：fn(vlaue){}，value 是上一个任务的返回结果，then 中的函数一定要 return 一个结果或者一个新的 Promise 对象，才可以让之后的then 回调接收。

* Promise.prototype.catch

  实例方法，捕获异常，函数形式：fn(err){}, err 是 catch 注册 之前的回调抛出的异常信息。

* Promise.race

  >类方法，多个 Promise 任务同时执行，返回最先执行结束的 Promise 任务的结果，不管这个 Promise 结果是成功还是失败。 。

* Promise.all

  > 类方法，多个 Promise 任务同时执行。
  > 如果全部成功执行，则以数组的方式返回所有 Promise 任务的执行结果。 如果有一个 Promise 任务 rejected，则只返回 rejected 任务的结果。

* 一个 Promise 对象有三个状态，并且状态一旦改变，便不能再被更改为其他状态。

  - pending，异步任务正在进行。
  - resolved (也可以叫fulfilled)，异步任务执行成功。
  - rejected，异步任务执行失败。

## async await

ES7 提出的`async` 函数，终于让 JavaScript 对于异步操作有了终极解决方案。

Promise 存在的问题：

Promise 的方式虽然解决了 callback hell，但是这种方式充满了 Promise的 `then()` 方法，如果处理流程复杂的话，整段代码将充满 `then`。语义化不明显，代码流程不能很好的表示执行流程。

使用`async` 函数完流程清晰，直观、语义明显。操作异步流程就如同操作同步流程。同时 `async` 函数自带执行器，执行的时候无需手动加载。

**语法**

* async 函数返回一个 Promise 对象

  `async` 函数内部 return 返回的值。会成为 `then` 方法回调函数的参数。

  ````
  async function  f() {
      return 'hello world'
  };
  f().then( (v) => console.log(v)) // hello world
  ````

  如果 `async` 函数内部抛出异常，则会导致返回的 Promise 对象状态变为 `reject` 状态。抛出的错误而会被 `catch` 方法回调函数接收到。

  ````
  async function e(){
      throw new Error('error');
  }
  e().then(v => console.log(v))
  .catch( e => console.log(e));
  ````

* 只有当 `async` 函数内部的异步操作都执行完，才会执行 `then` 方法的回调。

  ````
  const delay = timeout => new Promise(resolve=> setTimeout(resolve, timeout));
  async function f(){
      await delay(1000);
      await delay(2000);
      await delay(3000);
      return 'done';
  }
  f().then(v => console.log(v)); // 等待6s后才输出 'done'
  ````

* 正常情况下，await 命令后面跟着的是 Promise ，如果不是的话，也会被转换成一个 立即 resolve 的 Promise

  ````
  async function  f() {
      return await 1
  };
  f().then( (v) => console.log(v)) // 1
  ````

  如果返回的是 reject 的状态，则会被 `catch` 方法捕获。

**Async 函数的错误处理**

* 当 `async` 函数中只要一个 `await` 出现 reject 状态，则后面的 `await` 都不会被执行。

  ````
  let a;
  async function f() {
      await Promise.reject('error');
      a = await 1; // 这段 await 并没有执行
  }
  f().then(v => console.log(a));
  ````

* 使用`try/catch`

  ````
  // 正确的写法
  let a;
  async function correct() {
      try {
          await Promise.reject('error')
      } catch (error) {
          console.log(error);
      }
      a = await 1;
      return a;
  }
  ````

  如果有多个 `await` 则可以将其都放在 `try/catch` 中。

