---
layout: post
title: 前端性能优化之 Debouce 防抖
---

## 防抖 `Debounce`

首先来看下，经过防抖处理后的效果

![https://user-images.githubusercontent.com/20236883/71862515-1a26ce80-3135-11ea-8e2e-01203e17379a.gif](https://user-images.githubusercontent.com/20236883/71862515-1a26ce80-3135-11ea-8e2e-01203e17379a.gif)

从上图我们可以看到，经过 `debounce `处理后，在所有事件触发中，只执行最后一次触发。

## 为什么使用防抖

在前端开发中有很多频繁的事件触发，比如 resize，scroll，mousedown，mouseup，keyup 和 keyup等等。这些事件触发的频率非常高，如果在每个事件触发函数中进行 AJAX 请求或者更加复杂的逻辑，那就会造成卡顿现象。

先来看一下例子

HTML 结构：

````
<!DOCTYPE html>
<html lang="zh-cmn-Hans">

<head>
    <meta charset="utf-8">
    <meta http-equiv="x-ua-compatible" content="IE=edge, chrome=1">
    <title>debounce</title>
    <style>
        #container{
            width: 100%; height: 200px; line-height: 200px; text-align: center; color: #fff; background-color: #444; font-size: 30px;
        }
    </style>
</head>

<body>
    <div id="container"></div>
    <script src="debounce.js"></script>
</body>

</html>
````



js 代码：

```
var count = 1;
var container = document.getElementById('container');

function getUserAction() {
    container.innerHTML = count++;
};

container.onmousemove = getUserAction;
```



页面效果：

![debounce](https://github.com/mqyqingfeng/Blog/raw/master/Images/debounce/debounce.gif)

从左边滑到右边就触发了 165 次 getUserAction 函数！

因为这个例子很简单，所以浏览器完全反应的过来，可是如果是复杂的回调函数或是 ajax 请求呢？假设 1 秒触发了 60 次，每个回调就必须在 1000 / 60 = 16.67ms 内完成，否则就会有卡顿出现。

因此这种场景需要防抖处理。

## 防抖的原理与实现

防抖的原理就是：事件频繁地被触发，但事件必须是触发 n 秒之后才执行，如果你在一个事件触发的 n 秒内又触发了这个事件，那就以新的事件的时间为准，n 秒后才执行，总之，就是要等你触发完事件 n 秒内不再触发事件，事件才会被执行。

````
const debounce = (fn, delay=1000, immediate=false) => {
    let timer = null
    return function() {
        // 修正this指向和event对象能够被传递,
        // 保证不使用debounce函数时与使用debounce函数时 的this指向相同和参数能够被传递
        const context = this
        const args = arguments

        timer && clearTimeout(timer);
        
        if(immediate) {
            // 如果已经执行过，不再执行
            let callNow = !timer
            timer = setTimeout(function(){
                timer = null
            }, delay)
            callNow && fn.apply(context, args)
        } else {
            timer = setTimeout(function() {
                fn.apply(context, args)
            }, delay)
        }
    }
}

// 使用
var count = 1;
var container = document.getElementById('container');

function getUserAction(e) {
    // console.log('this', this);
    // console.log('e', e);
    container.innerHTML = count++;
};

container.onmousemove = debounce(getUserAction, 2000, true);
````



效果：

![debounce 第一版](https://github.com/mqyqingfeng/Blog/raw/master/Images/debounce/debounce-1.gif)



还可以进一步实现不同的需求：上面函数的一个参数 immediate 表示立即执行，也就是多次事件触发，第一次触发就先执行了，等用户不触发事件 n 秒后才去执行函数。

![debounce 第四版](https://github.com/mqyqingfeng/Blog/raw/master/Images/debounce/debounce-4.gif)



参考：

>[JavaScript专题之跟着underscore学防抖](https://github.com/mqyqingfeng/Blog/issues/22)
>
>[【进阶 7-2 期】深入浅出防抖函数 debounce](https://github.com/yygmind/blog/issues/39)