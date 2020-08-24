---
layout: post
title: 记忆化 Fibonacci

---



## Fibonacci 数列

* **概念：**

  除了第一，第二位的数字分别为0， 1之外，

  其余的 Fibonacci 数字都是前两个 Fibonacci 数字之和。

  0，1，1，2，3，5，8，13，21，34，55，89.......

  `JavaScript`实现：

  ````js
  function Fibonacci(n) {
      return n < 2 ? n : Fibonacci(n - 1) + Fibonacci(n -2)
  }
  ````

  Fibonacci 数列的最前面的两位数都是已知的，如果`n`小于2，就返回`0`或`1`. 

  但这种实现太耗时了，如果`n`足够大，会导致爆栈。做了很多无谓的工作，去计算可能已经被计算过的值。让函数具备记忆功能，可以有效地减少运算量

  ````
  const fibonaci = (function() {
      let memo = [0, 1]
      const fib = function(n) {
          let result = memo[n]
          if(typeof result !== 'number') {
              result = fib(n-1) + fib(n-2)
              memo[n] = result
          }
          return result
      }
      return fib
  })()
  
  console.log(fibonaci(10)) // 55
  ````

  声明一个`memo`数组来保存计算出来的结果，当函数被调用时，首先去检查memo数组是否存在结果，如果已经存在，就立即返回这个结果。

  参考：

  >[JavaScript语言精粹](https://book.douban.com/subject/3590768/)

