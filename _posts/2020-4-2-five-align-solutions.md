---
layout: post
title: 盒子水平垂直居中的五大方案
---

统一 HTML 结构：

首先声明一下，以下 css 样式是基于这个 HTML 结构讲解的。

````
<div class="father">
    <div class="box" id="box">
        盒子水平垂直居中
    </div>
</div>
````

* 定位

  * 需要知道盒子的具体宽高，通过设置margin的值来实现居中效果

    ````
    .father {
        position: relative;
    } 
    必须知道盒子具体宽高，因为写样式的时候需要用宽高来计算margin值 
    .box {
        position: absolute;
        top: 50%;
        left: 50%;
        margin-top: -25px;
        margin-left: -100px;
    } 
    ````

    

  * 使用 `margin: auto`，但盒子一定要有宽高，但不需要考虑宽高是多少

    ````
    前提盒子一定要有宽高，但不需要考虑宽高是多少，因为写样式的时候不需要知道宽高
    .box {
        position: absolute;
        top: 0;
        left: 0;
        right: 0;
        bottom: 0;
        margin: auto;
    } 
    ````

    

  * CSS3，transform 属性，`trasform` 中的 `translateX` 和 `translateY` 是基于元素自身的宽高计算的。

     `translateX`值为负数表示向左移动，正数数表示向左移动

     `translateY` 值为负数表示向上移动，正数数表示向下移动

    ````
    .box {
        position: absolute;
        top: 50%;
        left: 50%;
        transform: translate(-50%, -50%)
    } 
    ````

    

* flex 布局

  * 将父盒子设置为 display: flex。flex 布局时 CSS 3的特性，缺点有兼容性问题。

    HTML 结构

    CSS 样式

    ````
    .father {
    	display: flex;
    	align-items: center;
    	justify-content: center
    }
    ````

    

* 使用 原生`JavaScript`

  * `clientHeight / clientWidth:` 表示 CSS `height` + CSS `padding` - 水平滚动条高度 (如果存在)来计算. 

    ![image-20200602093824857](C:\Users\Administrator\AppData\Roaming\Typora\typora-user-images\image-20200602093824857.png)

  * `offsetHeight/offsetWidth:` 包括元素的边框, 内边距和元素的水平滚动条（如果存在且渲染的话）

    ![image-20200602094012688](C:\Users\Administrator\AppData\Roaming\Typora\typora-user-images\image-20200602094012688.png)
    
    ````html
    <body>
        <div class="box" id="box">
            盒子水平垂直居中
        </div>
    
        <script>
            let HTML = document.documentElement,
                winW = HTML.clientWidth,
                winH = HTML.clientHeight,
                boxW = box.offsetWidth,
                boxH = box.offsetHeight;
    
            box.style.position = "absolute";
            box.style.left = (winW - boxW) / 2 + 'px';
            box.style.top = (winH - boxH) / 2 + 'px';
        </script>
    </body>
    ````

* display: table-cell

  * 要求父级具有固定宽高，而且原本是要求设置文本的。

  ````html
  <div class="father">
      <div class="box" id="box">
          盒子水平垂直居中
      </div>
  </div>
  ````

  ````css
  /* table-cell原本是设置文本水平居中的，但是盒子也可以设置成inline box，也可以实现居中 */
  /* 缺点：要求父级有固定宽高，百分比不是固定宽高 */
  .father {
      display: table-cell;
      vertical-align: middle;
      text-align: center;
      width: 500px;
      height: 500px;
      background: lightcoral;
  }
  .box{
      display: inline-block;
  }
  ````

以上就是五种盒子水平垂直居中方案，不考虑兼容性的话，就尽量使用 flex 布局，或者定位 + transform 。