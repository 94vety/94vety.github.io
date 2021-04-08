---
title: promise
description: promise 基础，手写 promise.
tags:
- JavaScript
---

# Promise 声明

promise 是一个类，所以使用 class 声明.<br>
1. 由于 ```new Promise((resolve, reject) => {})```，所以传入一个参数（函数），秘籍里叫 ```executor```.<br>
2. ```executor``` 里面有两个参数，一个叫 ```resolve```（成功），一个叫 ```reject```（失败）.<br>
3. 由于 ```resolve``` 和 ```reject``` 可执行，所以都是函数，我们用 **let** 声明.<br>

```bash
class Promise {
    constructor (executor) {
        let resolve = () => {};
        let reject = () => {};

        executor(resolve, reject)
    }
}
```

***

# 解决基本状态

1. ```Promise``` 存在三个状态（```state```）分别为 ```pending```、```fulfilled```、```rejected```.<br>
2. ```pending```（等待态）为初始态，并可以转化为 ```fulfilled```（成功态）和 ```rejected```（失败态）.<br>
3. 成功时，不可转为其他状态，且必须有一个不可改变的值（```value```）.<br>
4. 失败时，不可转为其他状态，且必须有一个不可改变的原因（```reason```）.<br>
5. ```new Promise((resolve, reject) => resolve(value))``` **resolve** 为成功，接受参数 ```value```，状态改变为 ```fulfilled```，不可再次改变.<br>
6. ```new Promise((resolve, reject) => reject(reason))``` **reject** 为失败，接受参数 ```reason```，状态改变为 ```rejected```，不可再次改变.<br>
7. ```executor``` 函数报错，直接执行 ```reject()```.<br>

修改得到以下代码

```bash
class Promise {
    constructor (executor) {
        this.state = 'pending';
        this.value = undefined;
        this.reason = undefined;

        let resolve = value => {
            if (this.state === 'pending') {
                this.state = 'fulfilled';
                this.value = value;
            }
        }

        let reject = reason => {
            if (this.state === 'pending') {
                this.state = 'rejected';
                this.reason = reason;
            }
        }

        try {
            executor(resolve, reject)
        } catch (err) {
            reject(err)
        }
    }
}
```

***

# then 方法


