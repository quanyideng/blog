---
layout: post
title: JavaScript 面向对象编程
---



## 什么是面向对象编程

首先上一张生动形象的图来解释面向对象思想



![生动描述面向对象概念](https://user-gold-cdn.xitu.io/2017/6/8/99f977fcc9ad71ee0c197cbdaecd52b8?imageView2/0/w/1280/h/960/ignore-error/1)

面向对象的思想主要是以对象为主，将一个具体问题抽象成一个对象，并且将抽象出来的对象及其属性和方法封装成一个类。

## 面向过程编程与面向对象编程

对于“把大象放冰箱”这个问题，分别这两种编程方式实现

* 面向过程

  1. 开门（冰箱）；

  2. 装进（冰箱，大象）；

  3. 关门（冰箱）。

* 面向对象

  1. 冰箱.开门（）

  2. 冰箱.装进（大象）

  3. 冰箱.关门（）

  可以看出来面向对象和面向过程的侧重点是不同的，面向过程是以动词为主，完成一个事件就是将不同的动作函数按顺序调用。

## JavaScript基于原型的继承

首先先来讲下 `class`，其实在 JS 中并不存在类，`class` 只是语法糖，本质还是函数。

````
class Person {}
Person instanceof Function // true
````

* **组合继承**

  组合继承是最常用的继承方式，

  ````
  // 定义父类
  function Person(name, age, gender) {
      this.name = name;
      this.age = age;
      this.gender = gender;
  }
  // 方法一般写在原型上，为所有实例共享，以节约内存
  Person.prototype.greeting = function() {
      // this会指向实例
      console.log('I am ' +　this.name);
  }
  // 定义子类
  function Student(name, age, gender) {
      // 继承父类Person的属性
      Person.call(this, name, age, gender)
  }
  // 继承Person上的方法
  Student.prototype = new Person()
  
  // 创建Student实例
  const student1 = new Student('Hawkins', 22, 'male')
  
  student1.greeting() // I am Hawkins
  console.log(student1 instanceof Person) // true
  ````

  组合继承的优点：

  1. 构造函数可以传参
  2. 不会与父类引用属性共享，也就是说每个实例化的子类修改引用类型时，互不影响。
  3. 可以复用父类的函数

  组合继承的唯一弱点：

  ![image-20200618151344165](https://i.loli.net/2020/06/18/y5Apuqo8HXb6i2G.png)

  在继承父类函数的时候调用了父类构造函数，导致子类的原型上多了不需要的父类属性，存在内存上的浪费。

  

* **寄生组合继承**

  这种继承方式对组合继承进行了优化，组合继承缺点在于继承父类函数时调用了构造函数，我们只需要优化掉这点就行了。

  ````
  // 定义父类
  function Person(name, age, gender) {
      this.name = name;
      this.age = age;
      this.gender = gender;
  }
  // 方法一般写在原型上，为所有实例共享，以节约内存
  Person.prototype.greeting = function() {
      // this会指向实例
      console.log('I am ' +　this.name);
  }
  // 定义子类
  function Student(name, age, gender) {
      // 继承父类Person的属性
      Person.call(this, name, age, gender)
  }
  // 继承Person上的方法
  Student.prototype = Object.create(Person.prototype)
  
  // 手动更改子类的构造函数为自身，否则构造函数为父类Person, 这不符合预期
  // 一般由Student构造函数实例化的对象，其构造函数应为Student
  // 所以这里需要手动修改
  Student.prototype.constructor = Student
  
  // 创建Student实例
  const student1 = new Student('Hawkins', 22, 'male')
  
  student1.greeting() // I am Hawkins
  console.log(student1.constructor === Student) // Student
  ````

  

* **class 继承**

  以上两种继承方式都是通过原型去解决的，在 ES6 中，我们可以使用 `class` 去实现继承，并且实现起来很简单

  ````
  class Parent {
    constructor(value) {
      this.val = value
    }
    getValue() {
      console.log(this.val)
    }
  }
  class Child extends Parent {
    constructor(value) {
      super(value)
    }
  }
  let child = new Child(1)
  child.getValue() // 1
  child instanceof Parent // true
  ````

  `class` 实现继承的核心在于使用 `extends` 表明继承自哪个父类，并且在子类构造函数中必须调用 `super()`方法，因为这段代码可以看成 `Parent.call(this, value)`。



参考：

> [js:面向对象编程，带你认识封装、继承和多态](https://juejin.im/post/6844903480868470798)
>
> [2分钟让你明白什么是面向对象编程](https://zhuanlan.zhihu.com/p/75265007)