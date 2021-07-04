---
title: Object.defineProperty 和 Proxy
description: Object.defineProperty 可以使用 setter 进行属性修改时的拦截，getter 获取属性时修改，而 Proxy 可以对各种操作进行拦截，通过 handler 函数进行拦截之后的操作.
tags:
- JavaScript
---

# defineProperty

**ES5** 提供了 `Object.defineProperty` 方法，该方法可以在一个对象上定义一个新的属性，或者修改一个对象的现有属性，并返回这个对象.<br>

**语法**

```js
Object.defineProperty(obj, prop, descriptor)
```

**参数**

1. obj：要在其上定义属性的对象.<br>
2. prop：要定义或修改的属性的名称.<br>
3. descriptor：将被定义或修改的属性的描述符.<br>

```js
var obj = {};

Object.defineProperty(obj, 'num', {
    value: 1,
    writable: true,
    enumerable: true,
    configurable: true
})
```

虽然我们可以直接添加属性和值，但是使用这种方式，我们能进行更多的配置.<br>
函数的第三个参数 `descriptor` 所表示的属性描述符有两种形式：**数据描述符** 和 **存取描述符**.<br>

1. 两者都具有以下两种键值
    - **enumerable**
    ```bash 
    当且仅当该属性的 enumerable 为 true 时，该属性才能够出现在对象的枚举属性中. 默认 false
    ```
    - **configurable**
    ```bash
    当且仅当该属性的 configurable 为 true 时，该属性描述符才能够被改变，也能够被删除. 默认 false
    ```
2. 数据描述符特有的可选键值
    - **value**
    ```bash
    该属性对应的值. 可以是任何有效的 JavaScript 值（数值，对象，函数等）. 默认 undefined
    ```
    - **writable**
    ```bash
    当且仅当该属性为 true 时，该属性才能被赋值运算符改变. 默认 false
    ```
3. 存取描述符特有的可选键值
    - **get**
    ```bash
    一个给属性提供 getter 的方法，如果没有 getter 则为 undefined. 该方法返回值被用作属性值. 默认 undefined
    ```
    - **set**
    ```bash
    一个给属性提供 setter 的方法，如果没有 setter 则为 undefined. 该方法将接受唯一参数，并将该参数的新值分配给该属性. 默认 undefined
    ```
4. 两种属性描述符不能同时存在

数据描述符
```js
Object.defineProperty({}, 'num', {
    value: 1,
    writable: true,
    enumerable: true,
    configurable: true
})
```

存取描述符
```js
var value = 1;

Object.defineProperty({}, 'num', {
    get: function() {
        return value
    },
    set: function(newValue) {
        value = newValue
    },
    enumerable: true,
    configurable: true
})
```

下面是错误示范
```js
Object.defineProperty({}, 'num', {
    value: 1,
    get: function() {
        return 1
    },
    enumerable: true,
    configurable: true
})
```

所有属性描述符都是非必需的，但 `descriptor` 这个字段是必须的，如果不进行任何配置，可以采用如下写法

```js
var obj = Object.defineProperty({}, 'num', {});
console.log(obj.num);
// undefined
```

***

## Setters 和 Getters

前面，`defineProperty` 的存取描述符中的 `get` 和 `set`，又被称为 `getter` 和 `setter`. 由 `getter` 和 `setter` 定义的属性被称为 '存取器属性'.<br>
1. 当程序查询存取器的属性值时
    - 调用 `getter` 方法，这个方法的返回值就是属性存取表达式的值.
2. 当程序设置一个存取器属性的值时
    - 调用 `setter` 方法，将赋值表达式右侧的值当作参数传入 `setter`.
    - 从某种意义上讲，这个方法负责 '设置' 属性值，可以忽略 `setter` 方法的返回值.

```js
var obj = {};
var value = null;

Object.defineProperty(obj, 'num', {
    get: function() {
        console.log('执行了 get 操作');
        return value;
    },
    set: function(newValue) {
        console.log('执行了 set 操作');
        value = newValue;
    }
})

obj.num = 1;
// 执行了 set 操作

obj.num
// 执行了 get 操作
```

我们封装一下，便可以实现对数据的监控

```js
function Archiver() {
    var value = null;
    var archive = [];

    Object.defineProperty(this, 'num', {
        get: function() {
            console.log('执行了 get 操作');
            return value;
        },
        set: function(newValue) {
            console.log('执行了 set 操作');
            value = newValue;
            archive.push({ val: value });
        }
    })

    this.getArchive = function() { return archive }
}

var arc = new Archiver();
arc.num;
// 执行了 get 操作

arc.num = 11;
// 执行了 set 操作

arc.num = 13;
// 执行了 set 操作

console.log(arc.getArchive())
// [ { val: 11 }, { val: 13 } ]
```

***

## watch API

既然可以监控数据的变化，那么当数据改变的时候，自动进行渲染工作.<br>

**HTML** 中有个 `span` 标签和 `button` 标签
```js
<span id='container'>1</span>
<button id='button'>点击加 1</button>
```

当点击按钮的时候，`span` 标签里的值加 1，以往的做法是：
```js
document.getElementById('button').addEventListener('click', function() {
    var container = document.getElementById('container');
    container.innerHTML = Number(container.innerHTML) + 1；
})
```

使用 `Object.defineProperty` 如下：
```js
var obj = { 
    value: 1
}

var value = 1;

Object.defineProperty(obj, 'value', {
    get: function() {
        return value;
    },
    set: function(newValue) {
        value = newValue;
        document.getElementById('container').innerHTML = newValue;
    }
});

document.getElementById('button').addEventListener('click', function() {
    obj.value += 1
})
```

上述代码虽然看似较多，但当我们需要改变 `<span>` 标签中的值的时候，直接修改 `obj.value` 的值就可以了.<br>
这种写法，我们需要单独声明一个变量存储 `obj.value` 的值，因为如果直接在 `set` 中 `obj.value = newValue` 就会陷入无限的循环中.<br>
下面我们封装了 `watch` 函数，方便使用.

```js
var obj = {
    value: 1
}

watch(obj, 'value', function(newValue) {
    document.getElementById('container').innerHTML = newValue;
})

document.getElementById('button').addEventListener('click', function() {
    obj.value += 1;
})
```

`watch` 封装函数

```js
(function() {
    var root = this;
    function watch(obj, name, func) {
        var value = obj[name];

        Object.defineProperty(obj, name, {
            get: function() {
                return value;
            },
            set: function(newValue) {
                value = newValue;
                func(value)
            }
        })

        if (value) obj[name] = value;
    }

    this.watch = watch;
})()
```

上述代码中我们实现了监控对象属性值的改变，并且可以根据属性值的改变，添加回调函数.

***

# Proxy

使用 `defineProperty` 只能重定义属性的读取（get）和设置（set）行为，到了 **ES6**，提供了 `Proxy`，可以重定义更多的行为，比如 `in`，`delete`，函数调用等更多行为.<br>

`Proxy` 本意是 '代理'，用在这里表示由它来 '代理' 某些操作，**ES6** 提供 `Proxy` 构造函数，用来生成 `Proxy` 实例，语法如下：

```js
var proxy = new Proxy(target, handler);
```

`Proxy` 对象的所有用法，都是上面这种形式，不同的只是 `handler` 参数的写法.<br>
其中，`new Proxy()` 表示生成一个 `Proxy` 实例，`target` 参数表示所要被拦截的对象，`handler` 参数也是一个对象，用来定制拦截行为.

```js
var proxy = new Proxy({}, {
    get: function(obj, prop) {
        console.log('执行了 get 操作');
        return obj[prop];
    },
    set: function(obj, prop, value) {
        console.log('执行了 set 操作');
        obj[prop] = value;
    }
})

proxy.time = 35;
// 执行了 set 操作

proxy.time
// 执行了 get 操作
```

除了 `set` 和 `get` 之外，`Proxy` 可以拦截多达 13 种操作，比如 `has(target, propKey)`，可以拦截 `propKey in proxy` 的操作，返回一个布尓值.

```js
// 使用 has 方法隐藏某些属性，不被 in 元素发现
var handler = {
    has(target, key) {
        if (key === '_prop') {
            return false;
        }

        return key in target;
    }
}

var target = { _prop: 'foo', prop: 'foo' };
var proxy = new Proxy(target, handler);
console.log('_prop' in proxy);
```

比如 `apply`、`call` 方法
```js
var target = function() {
    return 'I am the target';
}

var handler = {
    apply: function() {
        return 'I am the proxy'
    }
}

var p = new Proxy(target, handler);

console.log(p());
// 'I am the proxy'
```

同时，`Proxy` 的最大的问题便是浏览器的支持，此外，很多效果无法使用 `poilyfill` 来弥补。

***

## watch API 优化

使用 `Proxy` 实现 `watch` 函数

```js
(function() {
    var root = this;

    function watch(target, func) {
        var proxy = new Proxy(target, {
            get: function(target, prop) {
                return target[prop]
            },
            set: function(target, prop, newValue) {
                target[prop] = value;
                func(prop, value);
            }
        })

        return proxy;
    }

    this.watch = watch;
})()

var obj = {
    value: 1
}

var newObj = watch(obj, function(key, newValue) {
    if (key === 'value') {
        document.getElementById('container').innerHTML = newValue;
    }
})

document.getElementById('button').addEventListener('click', function() {
    newObj.value += 1;
})
```

1. 区别
    - `defineProperty`：修改原来的 `obj` 对象便可以进行拦截.
    - `Proxy`：必须修改代理对象，即 `proxy` 实例才可以触发拦截.

***

感谢以下文章<br>
https://juejin.cn/post/6844903710410162183
