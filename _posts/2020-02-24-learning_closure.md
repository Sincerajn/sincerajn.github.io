---
layout: post
title: Javascript 闭包学习笔记
tag: Javascript 编程
description: 记录并分享我学习 JavaScript 闭包的经验与心得
---

闭包是 JavaScript 语言中的一个难点，实现许多高级应用需要依赖于闭包，理解闭包是深入学习 Javascript 中的一个关键点。之前我一直对闭包知其然而不知其所以然，经过重新研究，终于有了一些新的收获，我想把它以学习笔记的方式记录下来。

## 作用域的概念

在理解闭包前，需要先了解 JavaScript 中的作用域，函数体内部可以直接读取外部的变量：

```javascript
let n = 10 // 全局变量

function fun() {
    console.log(n) // 10
}
```

 而函数体外部则无法读取其内部的局部变量

```javascript
function fun() {
    let n = 10
}

console.log(n) // 报错：n 未找到
```

## 闭包的概念

我们已经知道，通常情况下无法读取函数内部的局部变量，除非这样

```javascript
function fun1() {
    let n = 10
    
    function fun2() {
        console.log(n) // 10
    }
}
```

虽然我们无法直接在 `fun1()` 函数外部读取其内部的变量 `n`，但是相对于函数 `fun2()` 而言，`fun1()` 是它的外部函数，`n`  便是它的外部变量，所以能读取。

形如 `fun2()` 这样能读取其它函数内部变量的函数，则被称为闭包。

## 闭包的作用

闭包最大的用处有两个，一个是上文提到的读取其它函数内部的变量，二是使这些变量始终保存在内存中。

```javascript
function fun1() {
    let n = 10
    
    add = function() { // 没有加 var 或 let 关键字，为全局方法
        n += 1
    }
    
    function fun2() {
        console.log(n)
    }
    
    return fun2
}

let result = fun1() // fun1() 返回 fun2 这个函数

result() // 10

add()

result() // 11
```

`let result = fun1()` 语句中，将 `fun2` 函数赋给了全局变量 `result` ，使 `fun2` 始终在内存中，又因为 `fun2()` 依赖于 `fun1()`，所以 `fun1()` 也始终存在于内存中；这样，`fun1()` 的局部变量 `n` 也始终存在于内存中了，在执行 `add()` 函数时，便改变了 `n` 的值。

## 实例

我们来看一个例子：实现一个计数器，先用简单的方法

```javascript
let counter = 0

function add() {
    return counter += 1
}

add() // 1
add() // 2
add() // 3
```

我们的确达到了预期的目的，但是由于 `counter` 是一个全局变量，任何函数都有可能改变它的值，这并不安全，所以我们需要改写这段代码。

```javascript
function counter() {
    let number = 0
    
    let add = function() {
        return number += 1
    } 
    
    return add
}

let add = counter()

add() // 1
add() // 2
add() // 3
```

运用上文所学的知识分析这段代码，`let add = counter()`  语句，可以看作是 `let add = function() {return number += 1}` ，这个函数又实际位于 `counter()` 函数内部，所以为闭包，能读取 `counter()` 函数内部的 `number` 变量。

上面的代码还可以再精简为

```javascript
let add = (() => {
    number = 0
    return () => number += 1
})()
```

跟上面的代码是等价的，利用了箭头函数。

## 总结

闭包的原理是作用域，通过闭包，可以将函数体内外联系起来。闭包的使用可以避免污染全局变量名，但同时潜在着造成内存泄漏的风险，使用后有必要手动将其销毁。

我在学习闭包的过程中，以下两篇文章对我的帮助很大，非常感谢，本文很大程度是二者的分析、提炼与总结。

> 阮一峰《[学习Javascript闭包（Closure）](http://www.ruanyifeng.com/blog/2009/08/learning_javascript_closures.html)》
> 
> 刘宁Leo《[为什么要使用闭包和如何使用闭包](https://segmentfault.com/a/1190000013243680)》