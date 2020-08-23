---
layout: post
title: css 边框写一个三角形
---

首先来认识以下边框的形状

1. 盒子的宽高为 0 的情况下，边框是三角形

   ![image-20200725100103578](https://i.loli.net/2020/07/25/Bgo9xnD4S5CuQUP.png)

2. 盒子的宽高都不为 0 的情况下，边框是梯形

   ![image-20200725100032473](https://i.loli.net/2020/07/25/YsEMmeC2qWKLc9t.png)


到这里就有思路使用 css 写一个边框了。将盒子的宽高都设为 0，任意三个边框颜色设为透明，剩下一个边框设置为非透明颜色，从视觉上看是一个三角形。

HTML 结构

````
<div class="container"></div>
````

CSS 样式

   ````
.container {
     width: 0;
     height: 0;
     border: 100px solid #f00283;
     border-color: transparent transparent #333 transparent;
}
   ````

   从上面代码可知，将其它边框的颜色设置为透明，就会得到一个三角形，

   ![image-20200725100216681](https://i.loli.net/2020/08/23/2xUvutGpfiYhgob.png)

以上就是 css 边框写三角形的简单实现。当然 css 并不局限于三角形，还可以实现不同形状的几何图形。

https://generative-art-with-css.commons.host/

这个网站展示了 css 可以写不同形状的图形。