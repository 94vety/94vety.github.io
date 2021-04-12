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

```Promise``` 有一个 ```then``` 方法，有两个参数：```onFulfilled```，```onRejected```，成功返回值，失败返回原因.<br>
1. 当状态 ```state``` 为 ```fulfilled```，即执行 ```onFulfilled```，传入 ```this.value```， 当状态 ```state``` 为 ```rejected```，即执行 ```onRejected```，传入 ```this.reason```.<br>
2. ```onFulfilled```，```onRejected``` 如果它们是函数，则必须分别在 ```fulfilled```，```rejected``` 后被调用，```value``` 或 ```reason``` 依次作为它们的第一个参数.<br>

```bash
class Promise {
    constructor(executor) {...}

    then(onFulfilled, onRejected) {
        if (this.state === 'fulfilled') {
            onFulfilled(this.value)
        } 

        if (this.state === 'rejected') {
            onRejected(this.reason)
        }
    }
}
```

***

# 解决异步实现

现在基本可以实现简单的同步代码，但是当 ```resolve``` 在 ```setTimeout``` 内执行，```then``` 时 ```state``` 还是 ```pending``` 等待状态，我们就需要在 ```then``` 调用的时候，将成功和失败存到各自的数组，一旦 ```then``` 或者 ```resolve```，将会调用它们.<br>

类似于发布订阅，先将 ```then``` 里面的两个函数存储起来，由于一个 ```promise``` 可以有多个 ```then```，所以可在同一个数组内.<br>

```bash
# 多个 then 的情况
let p = new Promise();
p.then();
p.then();
```

成功或失败时，```forEach``` 调用它们

```bash
class Promise {
    constructor(executor) {
        this.state = 'pending';
        this.value = undefined;
        this.reason = undefined;

        this.onResolvedCallbacks = [];
        this.onRejectedCallbacks = [];

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

    then(onFulfilled, onRejected) {
        if (this.state === 'fulfilled') {
            onFulfilled(this.value)
        }

        if (this.state === 'rejected') {
            onRejected(this.reason)
        }

        if (this.state === 'pending') {
            this.onResolvedCallbacks.push(() => {
                onFulfilled(this.value)
            })
            this.onRejectedCallbacks.push(() => {
                onRejected(this.reason)
            })
        }
    }
}
```

***

# 解决链式调用

我们常常用到的 ```new Promise().then().then()``` 这就是链式调用，用来解决回调地狱.<br>

1. 为了达成链式，我们默认在第一个 ```then``` 里返回一个 ```promise```，也就是说在 ```then``` 里面返回一个新的 ```promise```，```promise2 = new Promise((resolve, reject) => {})```.
    - 将这个 ```promise2``` 返回的值传递到下一个 ```then``` 中.
    - 如果返回一个普通的值，则将普通的值传递给下一个 ```then``` 中.



