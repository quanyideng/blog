---
layout: post
title: 消息队列和时间循环
---





## 事件循环（Event Loop）

**首先要弄清楚什么进程与线程**

1. 进程是`cpu`资源分配的最小单位（是能拥有资源和独立运行的最小单位）
2. 线程是`cpu`调度的最小单位（线程是建立在进程的基础上的一次程序运行单位，一个进程中可以有多个线程）

拿浏览器来说，当你打开一个Tab页时，就创建了一个进程，一个进程中可以有多个线程，比如渲染线程，JS 引擎线程，HTTP请求线程等等。当你发起一个请求时，其实就是创建了一个线程，当请求结束，该线程就会被销毁。

`JavaScript`是单线程的，也就是说，同一个时间内只能做一件事，那为什么是单线程的呢？

`JavaScript`的单线程，跟它的用途有关，作为浏览器脚本语言，`JavaScript`的主要用途是与用户互动，以及操作DOM。这决定了它只能是单线程，否则会带来很复杂的同步问题。比如，假定`JavaScript`同时有两个线程，一个线程在某个DOM节点上添加内容，另一个线程删除了这个节点，这时浏览器应该以哪个线程为准呢？

所以，为了避免复杂性，从一诞生，`JavaScrip`t就是单线程。

**从Event Loop理解 JS 的运行机制**

1. JS 分为同步任务和异步任务

2. 同步任务都在主线程上执行，形成一个`执行栈`

3. 主线程之外，**事件触发线程**管理着一个`任务队列`，只要异步任务有了运行结果，就在`任务队列`之中放置一个事件。

4. 一旦`执行栈`中的所有同步任务执行完毕（此时JS引擎空闲），系统就会读取`任务队列`，将可运行的异步任务添加到可执行栈中，开始执行。

![img](https://user-gold-cdn.xitu.io/2018/1/21/1611938ba2b4b9f4?imageView2/0/w/1280/h/960/ignore-error/1)

有时候`setTimeout`推入的事件不能准时执行，因为可能在它推入到事件列表时，主线程还不空闲，正在执行其它代码， 所以自然有误差。

**macrotask 与 microtask**

先来看一段代码：

````
console.log('start');

setTimeout(function() {
    console.log('setTimeout');
}, 0);

Promise.resolve().then(function() {
    console.log('promise1');
}).then(function() {
    console.log('promise2');
});

console.log('end')
````

正确执行顺序为：

````
sstart
end
promise1
promise2
setTimeout
````

以上代码中，Promise 为 microtask（微任务），而 `setTimeout` 和 script标签 均为 `macrotask`（宏任务）。宏任务执行完，就会去执行微任务，然后执行宏任务，如此反复循环执行。所以执行顺序为：script标签 --> Promise --> `setTimeou`t 。

- `macrotask`：主代码块，`setTimeout`，`setInterval`等（可以看到，事件队列中的每一个事件都是一个`macrotask`）
- `microtask`：`Promise`，`process.nextTick`等

总结一下运行机制：

1. 执行一个宏任务（栈中没有就从事件队列中获取）

2. 执行过程中如果遇到微任务，就将它添加到微任务的任务队列中

3. 宏任务执行完毕后，立即执行当前微任务队列中的所有微任务（依次执行）

4. 当前宏任务执行完毕，开始检查渲染，然后GUI线程接管渲染

5. 渲染完毕后，JS线程继续接管，开始下一个宏任务（从事件队列中获取）

![img](https://user-gold-cdn.xitu.io/2018/1/21/1611938b96e93485?imageView2/0/w/1280/h/960/ignore-error/1)

参考：

>[从浏览器多进程到JS单线程，JS运行机制最全面的一次梳理](https://juejin.im/post/6844903553795014663#heading-17)
>
>[JavaScript 运行机制详解：再谈Event Loop](http://www.ruanyifeng.com/blog/2014/10/event-loop.html)