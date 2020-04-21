---
title: 函数优化相关
date: 2020-04-17 16:29:28
tags:
---

## 尾调用

1. 函数A内部调用函数B，且不附加其它操作
2. 函数A在函数最后执行阶段调用函数B

```javascript
function A() {
  if (true) return B();

  return C()
}
function B() {}
function C() {}

```

### 调用帧 (调用记录)

用于存储调用位置与内部变量信息，所有的调用记录形成调用栈。
尾调用由于是函数最后一步操作，则不需保留外层fn的调用记录，因为调用位置、内部变量等信息不会再用到，
用内层fn的调用记录取代外层fn的调用记录即可。

### 尾调用优化
只保留内层fn的调用记录，在调用记录只有一项下，极大程度节省内存。

## 尾递归

函数递归本身很耗内存，可能存在n*n的调用记录，容易栈溢出，引入尾递归调用后，由于函数最后只存在一个调用记录，
所不会出现栈溢出的风险。

```javascript
// n阶乘
function factorial(n) {
  if ( n === 1 ) return 1;
  return n * factorial(n - 1); // 产生n个调用记录，复杂度O(n)
}

// ==> 尾递归调用
function factorial(n, total) {
  if ( n === 1 ) return total;
  return factorial(n - 1, n * total); // 始终产生1个调用记录，复杂度O(1)
}

// ==> 柯里化尾递归调用
function currying(fn, n) {
  return function (m) {
    return fn.call(this, m, n)
  }
}

function tailFactorial(n, total) {
  if ( n === 1 ) return total;
  return tailFactorial(n - 1, n * total);
}

const factorial = currying(tailFactorial, 1);
```