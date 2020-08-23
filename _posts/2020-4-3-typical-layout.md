---
layout: post
title: 经典布局
---

## 经典布局都是要实现左右固定，中间自适应的效果

![image-20200606152253212](https://i.loli.net/2020/06/06/hmpcntWjwaOedFB.png)

## 1. 圣杯布局

左浮动 + 负 margin-left + 负 margin-right

html 结构

````html
<div class="container">
    <div class="center">center</div>
    <div class="left">left</div>
    <div class="right">right</div>
</div>
````

css 样式

````
.container {
    height: 100%;
    padding: 0 200px;
}
.left,
.right {
    width: 200px;
    min-height: 200px;
    background: lightblue;
}
.center {
    width: 100%;
    min-height: 400px;
    background: lightsalmon;
}
.left,
.right,
.center{
    float: left;
}
.left{
    margin-left: -100%;
    position: relative;
    left: -200px;
}
.right {
    margin-right: -200px;
}
````



## 2. 双飞翼布局

左浮动 + 负 margin-left

html 结构

````ht;ml
<div class="container">
    <div class="center">center</div>
</div>
<div class="left">left</div>
<div class="right">right</div>
````

css 样式

```
.left,
.right,
.container{
    float: left;
}
.container {
    width: 100%;
}
.center {
    margin: 0 200px;
    min-height: 400px;
    background: lightsalmon;
}
.left,
.right{
    width: 200px;
    min-height: 200px;;
    background: pink;
}
.left {
    margin-left: -100%;
}
.right {
    margin-left: -200px;
}
```

圣杯布局 和 双飞翼布局 最终实现的效果都是 “ 左右固定，中间自适应 ” 。



以下三种方法同样也能实现这个种效果：

## 1. Flex布局

如果不考虑兼容性问题，可以使用Flex布局

html 结构：

````html
<div class="container">
    <div class="left">left</div>
    <div class="center">center</div>
    <div class="right">right</div>
</div>
````

css 结构: 

flex 是个简写属性。用来设置 flex-grow, flex-shrink, flex-basis。其中 flex-basis 默认情况下表示width, (在 `flex-direction: column` 情况下表示`height`)

````css
.container {
    display: flex;
    justify-content: space-between;
    height: 100%;
}
.left,
.right {
    flex: 0 0 200px;
    height: 200px;
    background: lightblue;
}
.center {
    flex: 1;
    min-height: 400px;
    background: lightsalmon;
}
````



## 2. 定位

推荐使用，既无性能问题，也无需兼容性问题

html 结构: 

````html
<div class="container">
    <div class="left">left</div>
    <div class="center">center</div>
    <div class="right">right</div>
</div>
````

css 结构：

````
.container {
    position: relative;
    height: 100%;
}
.left,
.right {
    position: absolute;
    top: 0;
    width:200px;
    min-height: 200px;
    background: lightblue;
}
.left{
    left: 0;
}
.right{
    right: 0;
}
.center {
    margin: 0 200px;
    min-height: 400px;
    background: lightsalmon;
}
````



## 3. calc

在 css 中使用 calc 表达式，性能不够好，并且也会有兼容问题，calc 是css3才有的方法。

html 结构: 

````html
<div class="container">
    <div class="left">left</div>
    <div class="center">center</div>
    <div class="right">right</div>
</div>
````

css 样式：左中右都向左浮动，使三个盒子在同一行中。其中 width: calc(100% - 400px); 使得 center 盒子的width 始终自适应，400px 是由左右盒子的 width 相加得到。

````css
.container {
    width: 100%;
}
.left,
.right,
.center {
    float: left;
}
.center {
    width: calc(100% - 400px);
    min-height: 400px;
    background: lightsalmon;
}
.left,
.right {
    width: 200px;
    min-height: 200px;
    background: pink;
}
````