---
layout: post
title: 前端性能优化之懒加载
---



## Lazy-Load

**懒加载实现思路：**

首屏加载的时候，其中的图片资源只加载用户所看到的图片，用户还没有看到的图片先不加载，直到用户将页面滚动到可视区域时，再去发起网络请求加载图片。这实际是将一部分图片延迟加载的过程，性能得到了提升，并且不影响用户的体验。这就是 Lazy-Load（懒加载）。

举例：

当我们打开掘金首页时：

![](https://user-gold-cdn.xitu.io/2018/10/5/16642f37c7f20143?w=1424&h=1092&f=png&s=251118)



当我们的页面并未滚动至包含图片的 div 元素所在的位置时，它的样式是这样的：

````
<div data-v-b2db8566="" 
    data-v-009ea7bb="" 
    data-v-6b46a625=""   
    data-src="https://user-gold-cdn.xitu.io/2018/9/27/16619f449ee24252?imageView2/1/w/120/h/120/q/85/format/webp/interlace/1"    
    class="lazy thumb thumb"    
    style="background-image: none; background-size: cover;">  
</div>
````



我们注意到 style 内联样式中，背景图片设置为了 none。也就是说这个 div 是没有内容的，它只起到一个**占位**的作用。

一旦用户通过滚动使得这个 div 出现在了可见范围内，那么 div 元素的内容就会发生变化。

div 的 style 内联样式中的背景图片属性从 none 变成了一个在线图片的 URL。也就是说，出现在可视区域的瞬间，div 元素的内容被即时地修改掉了——它被写入了有效的图片 URL，于是图片成功显示在页面。

其中“占位”的概念，就像 Youtube 首页的 HTML Preview 效果：

![youtube](https://s1.ax1x.com/2020/09/03/w9W2z4.png)

从上图可以看到，这个还没来得及被图片填充完全的网页，是用大大小小的空 div 元素来占位的。



**实现一个 Lazy-Load**

HTML & CSS 结构：

````
<!DOCTYPE html>
<html lang="en">

<head>
  <meta charset="UTF-8">
  <meta name="viewport" content="width=device-width, initial-scale=1.0">
  <meta http-equiv="X-UA-Compatible" content="ie=edge">
  <title>Lazy-Load</title>
  <style>
    .img {
      width: 400px;
      height: 600px;
      background-color: gray;
      margin: 0 auto;
    }

    .pic {}
  </style>
</head>

<body>
  <div class="container">
    <div class="img">
      // 注意我们并没有为它引入真实的src
      <img class="pic" alt="加载中" data-src="./images/1.png">
    </div>
    <div class="img">
      <img class="pic" alt="加载中" data-src="./images/2.jpg">
    </div>
    <div class="img">
      <img class="pic" alt="加载中" data-src="./images/3.png">
    </div>
    <div class="img">
      <img class="pic" alt="加载中" data-src="./images/4.png">
    </div>
    <div class="img">
      <img class="pic" alt="加载中" data-src="./images/5.png">
    </div>
    <div class="img">
      <img class="pic" alt="加载中" data-src="./images/6.png">
    </div>
    <div class="img">
      <img class="pic" alt="加载中" data-src="./images/7.png">
    </div>
    <div class="img">
      <img class="pic" alt="加载中" data-src="./images/8.jpg">
    </div>
    <div class="img">
      <img class="pic" alt="加载中" data-src="./images/9.png">
    </div>
    <div class="img">
      <img class="pic" alt="加载中" data-src="./images/10.png">
    </div>
  </div>
</body>

</html>
````



在懒加载的实现中，有两个关键的数值：

一个是**当前可视区域的高度**，另一个是**元素距离可视区域顶部的高度**。

**当前可视区域的高度**， 在和现代浏览器及 IE9 以上的浏览器中，可以用 `window.innerHeight` 属性获取。在低版本 IE 的标准模式中，可以用 `document.documentElement.clientHeight` 获取，这里我们兼容两种情况：

````
const viewHeight = window.innerHeight || document.documentElement.clientHeight 
````



而**元素距离可视区域顶部的高度**，我们这里选用 getBoundingClientRect() 方法来获取返回元素的大小及其相对于视口的位置。对此 MDN 给出了非常清晰的解释：

> 该方法的返回值是一个 `DOMRect` 对象，这个对象是由该元素的 `getClientRects() `方法返回的一组矩形的集合, 即：是与该元素相关的 CSS 边框集合 。

> `DOMRect `对象包含了一组用于描述边框的只读属性——left、top、right 和 bottom，单位为像素。除了 width 和 height 外的属性都是相对于视口的左上角位置而言的。

其中 left、top、right 和 bottom，它们对应到元素上是这样的：

![](https://user-gold-cdn.xitu.io/2018/10/5/1664360c6ceda200?w=500&h=500&f=png&s=25422)

可以看出，top 属性代表了元素距离可视区域顶部的高度，这就是我们想用的！



JavaScript 逻辑代码：

````
<script>
    // 获取所有的图片标签
    const imgs = document.getElementsByTagName('img')

    // 获取可视区域的高度，兼容低版本 IE
    const viewHeight = window.innerHeight || document.documentElement.clientHeight

    // num 用于统计当前显示到了哪一张图片，避免每次都从第一张图片开始检查是否露出
    let num = 0
    function lazyload() {
      for (let i = num; i < imgs.length; i++) {

        // 用可视区域高度减去元素顶部距离可视区域顶部的高度
        let distance = viewHeight - imgs[i].getBoundingClientRect().top

        // 如果可视区域高度大于等于元素顶部距离可视区域顶部的高度，说明元素露出，用户可以看到这个元素了
        if (distance >= 0) {

          // 给元素写入真实的src，展示图片
          imgs[i].src = imgs[i].getAttribute('data-src')

          // 前i张图片已经加载完毕，下次从第i+1张开始检查是否露出
          num = i + 1
        }
      }
    }

    // 监听Scroll事件
    window.addEventListener('scroll', lazyload, false);
 </script>
````



效果：

![image-20200903015650674](https://i.loli.net/2020/09/03/IqVcxULFJtpgrwR.png)



从上图可以看到，图片6，7，8 已经出现在用户的可视区域了，img 标签的 src 属性换成了真实的图片地址了，图片显示给用户。

而图片10 还没有出现到用户的可视区域，所以 img 标签并没有设置 src ，src 属性还没有设置为图片地址。直到图片 10 出现在用户的可视区域时，stc 才会设置为真实的图片的地址。

以上就是 Lazy-Load 的思路和代码实现。

**参考：**

>[MDN Window.innerHeight](https://developer.mozilla.org/zh-CN/docs/Web/API/Window/innerHeight)
>
>[MDN Element.getBoundingClientRect()](https://developer.mozilla.org/zh-CN/docs/Web/API/Element/getBoundingClientRect)