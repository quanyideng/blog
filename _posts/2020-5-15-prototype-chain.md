---
layout: post
title: 认识构造函数，原型与原型链
---

## 构造函数

构造函数是用来创建对象的，通常由`new`操作符去执行构造函数，返回一个实例对象，并将  `this  `指向这个实例对象。

````
// 构造函数名称规定首字母大写
funcition Foo() { 
	this.a = 1;
	this.b = 2
}

let f1 = new Foo()
let f2 = new Foo()

console.log(f1.a) // 1
console.log(f2.b) // 2
````

每个构造函数都有一个`prototype`属性，这个对象就是原型对像

每个实例对象都有一个`__proto__`属性，这个属性指向构造函数的`prototype`对象，

````
console.log(f1.__proto__ === Foo.Prototype) // true
````

其中的`__proto__`属性，MDN 官方不推荐使用，而是通过使用`Object.getPrototypeOf()`来获取原型

````
funcition Foo() { 
	this.a = 1;
	this.b = 2
}

let f1 = new Foo()
Foo.prototype.c = 3
Foo.prototype.d = 4

console.log(f1.a) // 1
console.log(Object.getPrototypeOf(f1)) // {c: 3, b: 4}
console.log(f1.__proto__) // {c: 3, b: 4}
````

但是我个人觉得使用`__proto__`属性有利于我们更好地理解原型链得运行机制。

## 原型

前面已经提到过，每个构造函数都有一个`prototype`属性。

这个属性是一个对象，有两个属性，分别是`constructor`和`__proto__`。其中`constructor`指向构造函数，`__proto__`指向`Object.prototype`，上面已经讲过，`__proto__`都是指向构造函数的原型对象。`Foo.prototype` 是由一个对象，由`Object()`构造函数创建，因此`Foo.prototype`的 `__proto__`属性指向`Object.prototype`。

````
funcition Foo() { 
	this.a = 1;
	this.b = 2
}

let f1 = new Foo()
Foo.prototype.c = 3
Foo.prototype.d = 4

console.log(f1.a) // 1
console.log(f1.__proto__.__proto__ === Object.prototype) // true
````

## 原型链

由`__proto__`和`prototype`的关系，组成原型链。那么`Foo`构造函数创建的实例`f1`的原型链为：

````
f1.__proto__.__proto__.__proto__ === null // true
````

上面展示了`f1`队形的原型链及其顶端，所用实例对象的原型最终都指向 `null `，由于`null`没有任何意义，因此可以认为原型链的顶端为`Object.prototype`。

最后附上一个图，看图写代码更容易理解

![prototype chain](https://i.loli.net/2020/06/18/WiqACbmeDNJfSkL.jpg)