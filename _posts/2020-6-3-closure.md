---
layout: post
title: 理解闭包
---

闭包可以让你从内部函数访问外部函数作用域。在 JavaScript 中，每当函数被创建，就会在函数生成时形成闭包。

* 首先什么是闭包？

  闭包就是**一个函数能够访问到另一个函数作用域中的变量**，比如一个 `A` 函数中定义了 `B` 函数，B 函数可以访问并操作`A`函数作用域中的变量，那么称 `B	`为闭包。

  创建闭包的常见方式是，就是在一个函数内部创建另一个函数。那么子函数可以访问到父函数作用域中的变量

  ````js
  // 父函数
  function outter() {
      var name = "Hawkins";
      // 子函数
      function inner() {
          console.log(name); // Hawkins
      }
      return displayName;
  }
  
  var myFunc = outter();
  myFunc();
  ````

  内部函数 `displayName()` 在执行前，从外部函数返回。

  `myFunc`是执行`outter`时创建的`inner`函数实例的引用。`inner` 的实例维持了一个对它的词法环境（变量 `name` 存在于其中）的引用。因此，当 `myFunc` 被调用时，变量 `name` 仍然可以被访问到，执行`myFunc`函数后，输出`Hawkins`。

  

* 那么闭包有什么用呢？或者说有什么应用场景？

  1. 封装私有变量

     ````js
     function Counter(start) {
         var count = start;
         return {
             increment: function() {
                 count++;
             },
     
             get: function() {
                 return count;
             }
         }
     }
     
     var foo = Counter(4);
     foo.increment();
     foo.get(); // 5
     ````

     `count`变量就是私有变量。`Counter` 函数返回两个闭包，函数 `increment` 和函数 `get`。 这两个函数都维持着 对外部作用域 `Counter` 的引用，因此总可以访问此作用域内定义的变量 `count`。

  2. 循环中的闭包

     一个常见的错误出现在循环中使用闭包，假设我们需要在每次循环中调用循环序号

     ````js
     for(var i = 0; i < 10; i++) {
         setTimeout(function() {
             console.log(i);  
         }, 1000);
     }
     ````

     上面的代码不会输出数字 `0` 到 `9`，而是会输出数字 `10` 十次。

     首先变量`i`是`var`命令声明的，因此在全局范围内都有效，所以全局只有一个变量`i`。在每一次循环中，变量`i`的值都会发生改变，而循环内被赋给`setTimeout`的回调函数内部的`console.log(i)`，里面的`i`指向的就是全局的`i`。也就是说，每次循环`console.log(i)`中的`i`指向的都是同一个`i`，这会导致运行时输出的是最后一轮的`i`的值，也就是 10。

     对于循环中的闭包的问题，有以下两种解决方法：

     * **使用自执行匿名函数**

       ````js
       for(var i = 0; i < 10; i++) {
           (function(e) {
               setTimeout(function() {
                   console.log(e);  
               }, 1000);
           })(i);
       }
       ````

       外部的匿名函数会立即执行，并把 `i` 作为它的参数，此时函数内 `e` 变量就拥有了 `i` 的一个拷贝。

       当传递给 `setTimeout` 的匿名函数执行时，它就拥有了对 `e` 的引用，而这个值是**不会**被循环改变的。

     * **使用`let`命令**

       ````js
       for(let i = 0; i < 10; i++) {
           setTimeout(function() {
               console.log(i);  
           }, 1000);
       }
       ````

       `let`命令是`ES6`的语法，使用`let`定义具有块级作用域的变量。变量只在`let`命令所在的代码块内有效。在代码块之外无权访问。

       回到上面问题，首先变量`i`是用`let`命令声明的，当前的`i`只在本轮循环有效，所以每一次循环的`i`其实都是一个新的变量。

       因此`setTimeout`的回调函数内部的`console.log(i)`是指向不同的`i`变量。

  > 这就是我对 JavaScript 中闭包（closure）的理解。其实从技术层面讲，所有函数都是闭包，因为我们都是在全局作用域（global）中定义函数。这些函数都可以访问全局作用域中的变量。