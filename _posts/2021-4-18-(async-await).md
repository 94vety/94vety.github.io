---
title: async/await
description: async/await 是 promise 和 generator 的语法糖，只是为了让我们书写代码时更加流畅，增强了代码的可读性.
tags:
- JavaScript
---

# 使用 async/await

1. `async`
    - `async` 用来表示函数是异步的，定义的 `async` 函数返回值是一个 `promise` 对象，可以使用 `then` 方法添加回调函数.
2. `await`
    - `await` 可以理解为是 `async wait` 的缩写，`await` 必须出现在 `async` 函数内部，不能单独使用，函数中只要使用 `await`，则当前函数必须使用 `async` 修饰.

```bash
async function func() {
    let result = await Math.random();
    console.log(result);
}

func()
# 0.6059597054815924
# Promise {<fulfilled>: undefined}
```
**使用了 `async/await` 的方法，有两个关键字 `async`，`await`，同时执行结果会返回一个 `promise` 对象.**

***

# await

1. `await` 后面可以跟任何 **JS** 表达式，虽说 `await` 可以等很多类型的东西，但是它最主要的意图是等待 `Promise` 对象的状态被 `resolved`.<br>
2. 如果 `await` 的是 `promise` 对象会造成异步函数停止执行并且等待 `promise` 的解决，如果等到的是正常的表达式则立即执行.

```bash
function sleep(second) {
    return new Promise((resolve, reject) => {
        setTimeout(() => {
            resolve('返回结果')
        }, second)
    })
} 

function normal() {
    console.log('正常执行的函数')
}

async function awaitDemo() {
    await normal();
    console.log('------')
    const result = await sleep(2000)
    console.log(result)
}

awaitDemo()
# 正常执行的函数
# ------
# Promise {<pending>}
# 返回结果
```

***

# reject 情况处理

```bash
function sleep(second) {
    return new Promise((resolve, reject) => {
        setTimeout(() => {
            reject('want to sleep~')
        }, second)
    })
}

async function errorDemo() {
    const result = await sleep(1000)
    console.log(result)
}

errorDemo()
# Uncaught (in promise) want to sleep~
```

上述代码会直接报错，为了避免这种情况的出现，我们会使用 `try/catch`

```bash
async function errorDemoSuper() {
    try {
        const result = await sleep(1000)
        console.log(result)
    } catch (err) {
        console.log(err)
    }
}

errorDemoSuper()
# want to sleep~
```