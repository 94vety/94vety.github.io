---
title: var、let、const 区别
description: var、let、const 三种声明变量方式的区别
tags:
- JavaScript
---

# let 和 const 声明形成作用域

## var 和 let 在 for 循环中的区别

```bash
for (var i = 0; i < 4; i++) {
    setTimeout(function() {
        console.log(i)
    }, 0)
}
# 4 4 4 4

for (let i = 0; i < 4; i++) {
    setTimeout(function() {
        console.log(i)
    }, 0)
}
# 0 1 2 3 
```

**使用 var 变量**<br>
&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;每次 **for** 循环的时候 **setTimeout** 都会执行并被放入任务队列，但是里面的 **function** 则不会执行，只有主线上的全部执行完才会执行任务队列里的任务。所以当主线程 **for** 循环执行完之后 **i** 的值为 4，这个时候再去任务队列中执行任务，**i** 全部为 4.<br>
**使用 let 变量**<br>
&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;因为 **let i** 定义的是区块变量，每个 **i** 只能存活到打括号结束，并不会把后面的 **for** 循环的 **i** 值赋给前面的 **setTimeout** 中的 **i**，而 **var i** 则是局部变量，这个 **i** 的生命周期不受 **for** 循环的打括号限制.<br>

## 作用域外获取不到值

```bash
if (1) {
    var a = 100;
    let b = 10;
}

console.log(a)
# 100
console.log(b)
# b is not defined
```

***

# var 声明的变量会挂载在 window 上，而 let 和 const 声明的变量不会

```bash
var a = 100;
console.log('var a =', a, window.a)
# var a = 100 100

let b = 10;
console.log('let b =', b, window.b)
# var b = 10 undefined

const c = 1;
console.log('const c =', c, window.c)
# const c = 1 undefined
```

***

# var 声明变量存在变量提升，let 和 const 不存在变量提升

```bash
console.log(a)
# undefined
var a = 100;

console.log(b)
# b is not defined
let b = 10;

console.log(c)
# c is not defined
const c = 10;
```

***

# 同一个作用域下 let 和 const 不能声明同名变量，而 var 可以

```bash
var a = 100;
console.log(a); 
# 100

var a = 10;
console.log(a); 
# 10


let a = 100;
let a = 10;
# 'a' has already been declared
```

***

# 暂存死区

```bash
var a = 100;

if(1){
    a = 10;
    # 在当前块作用域中存在a使用let/const声明的情况下，给a赋值10时，只会在当前作用域找变量a，
    # 而这时，还未到声明时候，所以控制台Error:a is not defined
    let a = 1;
}
```

***

# const 定义的值不能被修改

```bash
const a = {name:'李白'};
const a = {};
console.log(a) 
# Identifier 'a' has already been declared
```



