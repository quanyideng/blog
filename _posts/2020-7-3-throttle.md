---
layout: post
title: 前端性能优化之 Throttle 节流
---

## 节流 `Throttle`

首先来看下，经过节流函数处理后的效果

![https://user-images.githubusercontent.com/20236883/71862515-1a26ce80-3135-11ea-8e2e-01203e17379a.gif](https://user-images.githubusercontent.com/20236883/71862515-1a26ce80-3135-11ea-8e2e-01203e17379a.gif)

从上图我们可以看到，经过 `Throttle`处理后，相比于正常`Regular`情况下，事件触发的频率降低了，也就是说周期性地每隔一段时间执行一次函数。

## 为什么使用节流

使用节流目的就是减少事件执行的次数，因为浏览器去响应事件并执行函数，也是需要消耗时间和内存的。所以没有必要执行的事件触发函数就将其忽略。

## 节流的原理与实现

节流的原理：

如果你持续触发事件，每隔一段时间，执行一次事件。比如每隔 1 秒，就会去执行一次事件，如此反复，周期性地执行。

关于节流的实现，有两种主流的实现方式，一种是使用时间戳，一种是设置定时器。

1. **使用时间戳**

   当触发事件的时候，我们取出当前的时间戳，然后减去之前的时间戳(最一开始值设为 0 )，如果大于设置的时间周期，就去执行函数，然后更新时间戳为当前的时间戳，如果小于，就不执行。

   ````
   function throttle(func, wait) {
       var context, args;
       var previous = 0;
   
       return function() {
           var now = +new Date(); // ”+“ 将字符串转换成 number 类型
           context = this;
           args = arguments;
           if (now - previous > wait) {
               func.apply(context, args);
               previous = now;
           }
       }
   }
   ````

   例子依然是用讲 `debounce` 中的例子，你可以这样使用：

   ````
   container.onmousemove = throttle(getUserAction, 1000);
   ````

   

   ![使用时间戳](https://github.com/mqyqingfeng/Blog/raw/master/Images/throttle/throttle1.gif)

   我们可以看到：当鼠标移入的时候，事件立刻执行，每过 1s 会执行一次，如果在 4.2s 停止触发，以后不会再执行事件。

2. **使用定时器**

   当触发事件的时候，设置一个定时器，再触发事件的时候，如果定时器存在，就不执行，直到定时器执行，然后执行函数，清空定时器，这样就可以设置下个定时器。

   ````
   function throttle(func, wait) {
       var timeout;
       var previous = 0;
   
       return function() {
           context = this;
           args = arguments;
           if (!timeout) {
               timeout = setTimeout(function(){
                   timeout = null;
                   func.apply(context, args)
               }, wait)
           }
   
       }
   }
   ````

   将时间设置为 3 秒

   ````
   container.onmousemove = throttle(getUserAction, 3000);
   ````

   

   ![使用定时器](https://github.com/mqyqingfeng/Blog/raw/master/Images/throttle/throttle2.gif)

   

   我们可以看到：当鼠标移入的时候，事件不会立刻执行，晃了 3s 后终于执行了一次，之后每过 3s 就执行一次，当数字显示为 3 的时候，立刻移出鼠标，相当于大约 9.2s 的时候停止触发，但是依然会在第 12s 的时候执行一次事件。

   **总结：**

   比较两种方法：

   1. 第一种方法事件会立刻执行，第二种方法事件会在 n 秒后第一次执行
   2. 第一种方法事件停止触发后没有办法再执行事件，第二种方法事件停止触发后依然会再执行一次事件



参考：

>[JavaScript专题之跟着 underscore 学节流](https://github.com/mqyqingfeng/Blog/issues/26)
>
>[【进阶 7-1 期】深入浅出节流函数 throttle](https://github.com/yygmind/blog/issues/38)