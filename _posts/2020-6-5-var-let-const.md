---
layout: post
title: var，let 和 const的区别
---

首先要理解相关概念：提升（hoisting），暂时性死区

* 变量提升

  变量未声明之前，可以使用这个变量。

  这个行为叫做`hoisting`。`hoisting`就像是把所有的变量声明移动到函数或者全局代码的开头位置。

  ````js
  console.log(a) // undefined
  var a = 1
  
  // 以上代码等同于
  
  var a
  console.log(a)
  a = 1
  ````

   `var` 存在的问题，使用 `var` 声明的变量会被提升到作用域的顶部。

  ````js
  console.log(fn) // ƒ a() { console.log("fn") }
  function fn() {
      console.log("fn")
  }
  var fn = 1
  console.log(fn) // 1
  ````

  以上代码中用`function`声明了`fn`函数，用`var`声明了变量`fn`，两种声明都存在提升。

  函数提升优先与变量提升，函数提升会把整个函数挪到作用域顶部，变量提升只会把声明挪到作用域顶部。

* **let & const**

  **块级作用域**

  用`let`和`const`声明的变量或常量，只在`let`和`const`命令所在的代码块内有效。变量只在代码块内有效，在代码块之外是无法访问到的。

  ````
  {
    let a = 10;
    const b = 20
    var c = 1;
  }
  
  a // Uncaught ReferenceError: a is not defined
  b // Uncaught ReferenceError: b is not defined
  c // 1
  ````

  可以看到 `var` 与 `let，const`命令的区别，`var` 没有块级作用域，在`var`命令`所在的代码块之外的地方都可以访问到。

  

  ![image-20200822154807683](https://i.loli.net/2020/08/22/mZ1ACpjcQb9E7y2.png)

  1. **不存在变量提升**

     `var`命令会发生“变量提升”现象，即变量可以在声明之前使用，值为`undefined`。

     `let`和`const`则不存在这种现象，必须在声明之后，使用变量或常量

     ````
     // var 的情况
     console.log(foo); // 输出undefined
     var foo = 2;
     
     // let 的情况
     console.log(bar); // 报错ReferenceError
     let bar = 2;
     ````

     

  2. **暂时性死区**

     在块级作用域内用`let`命令声明的变量会绑定这个区域，不会受到外部影响。

     ````
     var name = 'Hawkins';
     
     if (true) {
       name = 'Eugene'; // ReferenceError
       let tmp;
     }
     
     ````

     上面代码中，存在全局变量`name`，但是块级作用域内`let`又声明了一个局部变量`name`，导致后者绑定这个块级作用域，所以在`let`声明变量前，对`name`赋值会报错。

     如果区块中存在`let`和`const`命令，这个区块对这些命令声明的变量，从一开始就形成了封闭作用域。凡是在声明之前就操作这些变量，就会报错。

     总之，在代码块内，使用`let`命令声明变量之前，该变量都是不可用的。

     

  3. **重复声明报错**

     ````
     var value = 1;
     let value = 2; 
     // Uncaught SyntaxError: Identifier 'value' has already been declared
     ````

     

  4. **不会绑定到全局作用域**

     当在全局作用域中使用 var 声明的时候，会创建一个新的全局变量作为全局对象的属性。

     ````
     var name = 'Hawkins';
     console.log(window.value); // Hawkins
     ````

     而` let` 和 `const` 不会：

     ````
     let name1 = 'Hawkins';
     const name2 = 'Eugene';
     
     console.log(window.name1); // undefined
     console.log(window.name2); // undefined
     ````

     

  5. **`let`和`const`的区别：**

     `let`用来声明变量，

     而`const` 用于声明常量，但其值一旦被设定不能再被修改，否则会报错。

     ```
     const PI = 3.1415;
     PI // 3.1415
     
     PI = 3;
     // TypeError: Assignment to constant variable.
     ```

     `const`声明时必须赋值，否则会报错

     ````
     const name;
     // Uncaught SyntaxError: Missing initializer in const declaration
     ````

     `const`能够保证变量指向的那个内存地址所保存的数据不得改动。对于简单类型的数据（数值、字符串、布尔值），值就保存在变量指向的那个内存地址，因此等同于常量。

     但对于复合类型的数据（主要是对象和数组），变量指向的内存地址，保存的只是一个指向实际数据的指针，`const`只能保证这个指针是固定的，但它指向的数据结构是可以修改的。

     
     
     ````
     const data = {
         name: 'Hawkins'
     }
     
     // 修改 data 中的数据，没有问题
     data.name = 'Eugene';
     data.anotherName = 'Cavendish';
     
     // 将 data 赋予新的对象，报错
data = { }; // Uncaught TypeError: Assignment to constant variable.
     ````
     
     
     
  
  > 总之，在平常开发中，尽量使用`let`声明变量，`cosnt`声明常量。如果浏览器不支持 ES6 语法，这是就可以使用 Babel 将 代码转换成浏览器能够识别的代码.
