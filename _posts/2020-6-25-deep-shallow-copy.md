---
layout: post
title: 深拷贝与浅拷贝
---



## 一，赋值

首先要理解**赋值**操作

赋值是将一个数值或对象赋值给某个变量的过程，分为两中情况

* 基本数据类型：赋值，赋值之后两个变量互不影响
* 引用数据类型：赋**址**，两个变量具有相同的引用，指向同一个对象，相互之间有影响

对基本类型进行赋值操作，两个变量互不影响。

````
let a = "Hawkins";
let b = a;
console.log(b);
// Hawkins

a = "change";
console.log(a);
// change
console.log(b);
// Hawkins
````

对象类型存储的是地址（指针）。当创建一个对象类型的时候，计算机会在内存中帮我们开辟一个空间来存放值，但是我们需要找到这个空间，这个空间会拥有一个地址（指针）。

````
let a = {
    name: "Hawkins",
    book: {
        title: "You Don't Know JS",
        price: "45"
    }
}
let b = a;
console.log(b);
// {
// 	name: "Hawkins",
// 	book: {title: "You Don't Know JS", price: "45"}
// } 

a.name = "Eugene";
a.book.price = "55";
console.log(a);
// {
// 	name: "Eugene",
// 	book: {title: "You Don't Know JS", price: "55"}
// } 

console.log(b);
// {
// 	name: "Eugene",
// 	book: {title: "You Don't Know JS", price: "55"}
// } 
````

对引用类型进行赋**址**操作，两个变量 a，b 指向同一个对象，改变变量 a 之后会影响变量 b。

通常在开发中并不希望改变变量 a 之后会影响到变量 b

这时就需要用到**浅拷贝**和**深拷贝**。

## 二，浅拷贝（Shallow Copy）

浅拷贝只解决了第一层的问题，拷贝第一层的**基本类型值**，以及第一层的**引用类型地址**。

1. **Object.assign**

   `**Object.assign()**` 方法用于将所有可枚举属性的值从一个或多个源对象复制到目标对象。它将返回目标对象。

   ````
   let a = {
       name: "Hawkins",
       book: {
           title: "You Don't Know JS",
           price: "45"
       }
   }
   let b = Object.assign({}, a);
   console.log(b);
   // {
   // 	name: "muyiy",
   // 	book: {title: "You Don't Know JS", price: "45"}
   // } 
   
   a.name = "Eugene";
   a.book.price = "55";
   console.log(a);
   // {
   // 	name: "Eugene",
   // 	book: {title: "You Don't Know JS", price: "55"}
   // } 
   
   console.log(b);
   // {
   // 	name: "Hawkins",
   // 	book: {title: "You Don't Know JS", price: "55"}
   // } 
   ````

   上面代码中，`b`是通过浅拷贝`a`得到的，`Object.assign` 只会拷贝所有的属性值到新的对象中，如果属性值是对象的话，拷贝的是地址。

   所以当改变对象 a 之后，对象 b 的基本属性保持不变。但是当改变对象 a 中的对象 `book` 时，对象 b 相应的位置也发生了变化。

2. **扩展运算符**

   ````
   let a = {
       name: "Hawkins",
       book: {
           title: "You Don't Know JS",
           price: "45"
       }
   }
   let b = {...a};
   console.log(b);
   // {
   // 	name: "Hawkins",
   // 	book: {title: "You Don't Know JS", price: "45"}
   // } 
   
   a.name = "Eugene";
   a.book.price = "55";
   console.log(a);
   // {
   // 	name: "Eugene",
   // 	book: {title: "You Don't Know JS", price: "55"}
   // } 
   
   console.log(b);
   // {
   // 	name: "Hawkins",
   // 	book: {title: "You Don't Know JS", price: "55"}
   // } 
   ````

   效果与`Object.assign()`是一样的。

   

## 三，深拷贝（Deep Copy）

1. `JSON.parse(JSON.stringify(object))`

   ````
   let a = {
       name: "Hawkins",
       book: {
           title: "You Don't Know JS",
           price: "45"
       }
   }
   let b = JSON.parse(JSON.stringify(a));
   console.log(b);
   // {
   // 	name: "Hawkins",
   // 	book: {title: "You Don't Know JS", price: "45"}
   // } 
   
   a.name = "Eugene";
   a.book.price = "55";
   console.log(a);
   // {
   // 	name: "Eugene",
   // 	book: {title: "You Don't Know JS", price: "55"}
   // } 
   
   console.log(b);
   // {
   // 	name: "Hawkins",
   // 	book: {title: "You Don't Know JS", price: "45"}
   // } 
   ````

   完全改变变量 a 之后对 b 没有任何影响，a 和 b 互不影响，这就是深拷贝。

   但是该方法也是有局限性的：

   - 会忽略 `undefined`
   - 会忽略 `symbol`
   - 不能序列化函数
   - 不能解决循环引用的对象

   ````
   let obj = {
     a: 1,
     b: {
       c: 2,
       d: 3,
     },
   }
   obj.c = obj.b
   obj.e = obj.a
   obj.b.c = obj.c
   obj.b.d = obj.b
   obj.b.e = obj.b.c
   let newObj = JSON.parse(JSON.stringify(obj))
   console.log(newObj)
   ````

   如果有一个循环引用对象，并不能通过该方法实现深拷贝

   ![img](https://user-gold-cdn.xitu.io/2018/3/28/1626b1ec2d3f9e41?imageView2/0/w/1280/h/960/ignore-error/1)

   

   在遇到函数、 `undefined` 或者 `symbol` 的时候，该对象也不能正常的序列化

   ````
   let a = {
     age: undefined,
     sex: Symbol('male'),
     jobs: function() {},
     name: 'yck'
   }
   let b = JSON.parse(JSON.stringify(a))
   console.log(b) // {name: "yck"}
   ````

   从上面代码可以看出来，该方法会忽略掉函数， `undefined` 和 `symbol`。

2. **浅拷贝 + 递归**

   进行浅拷贝时，判断是否为对象，如果是则递归。

   ````
   // 判断是否为对象
   function isObject(source){
       return typeof source === 'object' && source !== null
   }
   
   function deepClone(source) {
       if (!isObject(source)) return source; // 非对象返回自身
       let target = Array.isArray(source) ? [] : {}
       for(let key in source){
           if(Object.prototype.hasOwnProperty.call(source, key)) {
               if(isObject(source[key])){
                   target[key] = deepClone(source[key])
               } else {
                   target[key] = source[key]
               }
           }
       }
       return target
   }
   
   let obj = {
     a: [1, 2, 3],
     b: {
       c: 2,
       d: 3
     }
   }
   let newObj = deepClone(obj)
   newObj.b.c = 1
   console.log(obj.b.c) // 2
   ````

   

参考：

>[【进阶4-1期】详细解析赋值、浅拷贝和深拷贝的区别](https://github.com/yygmind/blog/issues/25)
>
>[MDN - Object.assign](https://developer.mozilla.org/zh-CN/docs/Web/JavaScript/Reference/Global_Objects/Object/assign)
>
>[MDN - 扩展运算符](https://developer.mozilla.org/zh-CN/docs/Web/JavaScript/Reference/Operators/Spread_syntax)

