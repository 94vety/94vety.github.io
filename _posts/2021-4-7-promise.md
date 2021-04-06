---
title: promise
description: promise 基础，手写 promise.
tags:
- JavaScript
---

# Promise 的含义

```Promise``` 是异步编程的一种解决方案，**ES6** 将其写进了语言标准. 所谓 ```Promise``` 就是一个容器，里面保存着未来才会结束的事件（通常是一个异步操作）的结果.<br>

```Promise```**对象有以下两个特点：**
* 对象的状态不受外界影响. ```Promise``` 对象代表一个异步操作，有三种状态：```pedding```（进行中），```fulfilled/resolved```（已成功），```reject```（已失败）. 只有异步操作的结果，可以决定当前是那种状态，任何其他操作都无法改变这个状态.
* 一旦状态改变，就不会再改变，任何时候都可以得到这个结果.

