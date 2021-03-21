---
title: apply、call、bind 三者区别
description: call、apply、bind 的作用都是改变 this 指向。
tags:
- JavaScript
---

# 基本语法

```bash
func.call(thisArg, param1, param2, ...)
func.apply(thisArg, [param1, param2, ...])
func.bind(thisArg, param1, param2)
```

## 返回值

**call/apply：**返回 func 的执行结果<br>
**bind：**返回 func 的拷贝（即返回一个新的函数），同时拥有指定的 this 和参数<br>

## 参数

**thisArg**（可选）：<br>
&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;1. **func** 的 **this** 将指向 **thisArg**.<br>
&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;2. 非严格模式下若 **thisArg** 指定为 **null** 或是 **undefined**，则 **func** 的 **this** 指向 window对象<br>
&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;3. 严格模式下若 **thisArg** 指定为 **null** 或是 **undefined**，则 **func** 的 **this** 指向 **undefined**.<br>
**param**（可选）：<br>
&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;1. 如果不传该参数或是传入 **null** 或 **undefined**，则表示不传入任何参数.<br>
&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;2. **apply** 的第二个参数为类数组对象，传入 **func** 的参数是数组.<br>

***

# apply、call、bind、三者的区别

## call 与 apply 的区别

**call** 和 **apply** 作用都是一致的，只有传入的参数不同，**call** 多次传入要使用的参数，而 **apply** 传入的是一个包含要使用参数的类数组。<br>
当参数数量或顺序确定时建议使用 call，不确定时使用 apply，当参数特别多时建议将参数整合为一个数组传入 apply。<br>

## call/apply 与 bind 区别

**call/apply** 与 **bind** 的返回值不同，**call/apply** 返回的是 **func** 执行的结果，改变 **this** 指向后立即执行函数。<br>
**bind** 返回的是 func 的拷贝，改变 **this** 后不会立即执行函数。<br>


