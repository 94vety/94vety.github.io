---
title: function component 和 class component
description: function 组件与 class 组件最大的不同是 function 组件能够捕获渲染的值，而 class 组件因为 react 中 this 是可变的，所以总是能获取最新的 props 。同样 hooks 和 function 组件一样具有 capture values 特性，利用useRef 可以避免 capture values 特性.
tags:
- React
---

# Class

## 构造函数 constructor()

`constructor(param1, param2, ...)` 是在 class 内部初始化实例的一个特殊方法，这里是设置字段初始值或对象设置的地方。<br>

```js
class User {
    constructor(name) {
        this.name = name;
    }
}
```

上述代码中：<br>
1. `User` 的构造函数有一个参数 `name`，用于设置字段 `this.name` 的初始值。<br>
2. 构造函数内部的 `this` 值等于新创建的实例。<br>

***

## 字段

1. 类字段是保存信息的变量，字段可以附属两种实体：
    - class 实例字段
    - class 自有字段（静态字段）
2. 字段有两种级别的可访问性
    - 公有：字段可以任意访问
    - 私有：只能在 class 内部访问

***

### 公有实例字段

```js
class User {
    constructor(name) {
        this.name = name;
    }
}

var user = new User('Jon Snow');
console.log(user.name)
// Jon Snow
```

上述代码中：
    - 表达式 `this.name = name` 创建了一个实例字段 `name` 并设置了初始值，之后便可以通过属性的方式访问到 `name` 的值。
    - 因为 `name` 是一个公有字段，因此你在 `User` 类外部能访问到它。

1. 当字段在构造函数中隐式创建时，就很难管理字段列表，必须从构造函数的代码中破译它们。<br>
2. 更多的方式是显式的声明 class 字段，无论构造函数做什么，实例总是具有相同的字段列表。<br>

class 主体中定义字段

```js
class someUser {
    field1,
    field2 = 'Initial value';
    // ...
}
```

重新修改一下上面的代码

```js
class User {
    name;
    constructor(name) {
        this.name = name;
    }
}

var user = new User('Jon Snow');
console.log(user.name)
# Jon Snow
```

上述的代码中，以方式声明的公有字段很有表现力，快速查看字段声明就足以知晓类的数据结构。

```js
class User {
    name = 'Unknown';
    constructor() {}
}

var user = new User();
console.log(user.name)
// Unknown
```

1. class 主体内的 `name = 'Unknown'` 声明了一个 `name` 字段并设置了初始值 `'Unknown'`。<br>
2. 对公有字段的访问和更新没有限制，可以在构造函数、方法以及 class 外部读取和赋值给公有字段。<br>

***

### 私有实例字段

1. 封装是一个很重要的概念，可以隐藏很多 class 内部的细节，使用封装类的人只依赖类提供的公共的接口，而不与类的实现细节耦合。<br>
2. 隐藏对象内部数据的一种好方法是使用私有字段，这些字段只能在它们所属的类中读取和更改，外部不能直接更改私有字段。<br>
3. 在字段名前加上特殊字符 `#` 可以使字段变得私有，每次使用私有字段，前缀 `#` 必须保留（声明时、读取时、修改时）。<br>

```js
class User {
    #name
    constructor(name) {
        this.#name = name;
    }
    
    getName() {
        return this.#name;
    }
}

var user = new User('Jon Snow');
user.getName();
// Jon Snow

console.log(user.#name)
// 抛出异常
```

1. `#name` 是私有字段，你可以在 `User` 内部访问和修改 `#name`，`getName()` 方法可以访问私有字段 `#name`。<br>
2. 但是如果你尝试从 `User` 类外部访问私有变量 `#name`，就会抛出语法错误。<br>

***

### 公有静态字段

1. 在 JS 类中创建静态字段，请使用特殊的关键字 `static` 加上字段名：`static` `myStaticField`。<br>
2. 下面的代码添加一个新字段 `type`，表示用户类型：`admin` 或 `regular`。静态字段 `TYPE_ADMIN` 和 `TYPE_REGULAR` 是区分用户类型的常量。 <br>

```js
class User {
    static TYPE_ADMIN = 'admin';
    static TYPE_REGULAR = 'regular';

    name;
    type;

    constructor(name, type) {
        this.name = name;
        this.type = type;
    }
}

var user = new User('Site Admin', User.TYPE_ADMIN);

console.log(user.name === User.TYPE_ADMIN);
// true
```

`static TYPE_ADMIN` 和 `static TYPE_REGULAR` 在 `User` 类内部定义了静态变量，要访问静态字段。必须用类名加上字段名。

***

### 私有静态字段

要将静态变量设置为私有，只要在字段前面加上特殊符号 `#`：`static #myPrivateStaticField`

```js
class User {
    static #MAX_INSTANCES = 0;
    static #instance = 0;

    name;

    constructor(name) {
        User.#instance++;
        
        if (User.#instance > User.#MAX_INSTANCES) {
            throw new Error('Unable to create User instance');
        }

        this.name = name;
    }
}

new User('Jon Snow');
new User('Arya Stark');
new User('Sansa Stark');                    // 抛出错误
```

静态字段 `#MAX_INSTANCES` 设置了最大实例数量，静态字段 `#instance` 实际创建的实例数量，私有静态字段外部无法访问。

***

## 方法

### 实例方法

1. 实例方法可以访问和修改实例数据.<br>
2. 实例方法可以调用其他实例方法.<br>
3. 可以任意调用静态方法.<br>

```js
class User {
    name = 'Unknown';

    constructor(name) {
        this.name = name;
    }

    getName() {
        return this.name;
    }
}

var user = new User('Jon Snow');
user.getName();
// Jon Snow
```

`getName() {...}` 是 `User` 类中的一个方法。`user.getName()` 是一个方法调用，它会执行该方法并返回计算后的值。<br>
在类的方法和构造函数中，`this` 的值等于类的实例。可用 `this` 访问实例数据：`this.field`，或者调用其他方法 `this.method()`<br>

```js
class User {
    name;

    constructor(name) {
        this.name = name;
    }

    getName() {
        return this.name;
    }

    nameContains(str) {
        return this.getName().includes(str);
    }
}

var user = new User('Jon Snow');
user.nameContains('Jon');
// true

user.nameContains('Sark');
// false
```

`nameContains(str) {...}` 是 `User` 类的一个方法，接受一个参数 `str`，并执行了实例的另一个方法 `this.getName()`，来获取用户的名字。<br>

私有方法只需要在名字前面加上 `#` 前缀即可。

```js
class User {
    name;

    constructor(name) {
        this.name = name;
    }

    #getName() {
        return this.name;
    }

    nameContains(str) {
        return this.#getName().includes(str);
    }
}

var user = new User('Jon Snow');
user.nameContains('Jon');
// true

user.nameContains('Sark');
// false

user.#getName()
// 抛出错误
```

`#getName()` 是个私有方法，在方法 `nameContains(str)` 内部，用这种方式调用私有方法 `this.#getName()`。<br>
由于是私有的，`#getName()` 不能在 `User` 类外部被调用。

***

### getters 和 setters

1. getter 是在获取字段时执行.<br>
2. setter 是在设置值时执行.<br>

```js
class User {
    #nameValue;

    constructor(name) {
        this.name = name;
    }

    get name() {
        return this.#nameValue;
    }

    set name(name) {
        if (name === '') {
            throw new Error(`name field of User cannot be empty`);
        }

        this.#nameValue = name;
    }    
}

var user = new User('Jon Snow');
console.log(user.name);
// Jon Snow

user.name = 'Jon White';

console.log(user.name);
// Jon White

user.name = ''
// 抛出错误
```

`get name() {...}` getter 在你访问字段 `user.name` 执行。<br>
`set name(name) {...}` 在字段更新 `user.name = 'Jon White'` 时执行，为空值便会抛出错误。

***

### 静态方法

创建静态方法，使用特殊的关键字 `static`，后面加上常规的方法语法 `static myStaticMethod() { ... }`。<br>

静态方法的简单规则：
    - 静态方法可以访问静态字段。
    - 静态方法不能访问实例字段。

```js
class User {
    static #takenNames = [];

    static isNameTaken(name) {
        return User.#takenNames.includes(name);
    }

    name = 'Unknown';

    constructor(name) {
        this.name = name;
        User.#takenNames.push(name);
    }
}

var user = new User('Jon Snow');

User.isNameTaken('Jon Snow');
User.isNameTaken('Arya Stark');
```    

1. `isNameTaken()` 是个静态方法，使用了静态私有字段 `User.#takenNames` 检查被占用的名字。<br> 
2. 静态方法是可以私有的：`static #staticFunction() {...}`。<br>

***

## 继承（extends）

`class Child extends Parent {}` 中，`Child` 类继承 `Parent` 类的构造函数、字段和方法。

```js
class User {
    name;

    constructor(name) {
        this.name = name;
    }

    getName() {
        return this.name;
    }
}

class ContentWriter extends User {
    posts = [];
}

var white = new ContentWriter('John Smith');

white.name
// 'John Smith'

white.getName()
// 'John Smith'

white.posts
// []
```

`ContentWriter` 从 `User` 继承了构造函数、方法 `getName()` 和字段 `name`。<br>
父类的私有成员不能被子类继承。<br>

***

### 父类构造函数 constructor() 和 super()
 
如果你想在子类中调用父类的构造函数，需要在子类构造函数中使用特殊的 `super` 方法。

```js
class User {
    name;

    constructor(name) {
        this.name = name;
    }

    getName() {
        return this.name;
    }
}

class ContentWriter extends User {
    posts = [];

    constructor(name, posts) {
        super(name);
        this.posts = posts;
    }
}

var writer = new ContentWriter('Johb Smith', ['Why I like JS']);

writer.name;
// 'Johb Smith'

writer.posts;
// ['Why I like JS']
```

子类 `ContentWriter` 中的 `super(name)` 执行了父类 `User` 的构造函数。<br>
在子类构造函数中必须在使用 `this` 关键字之前调用 `super()`。调用 `super()` 后才保证父类构造函数完成了实例化。<br>

```js
class Child extends Parent {
    constructor(value1, value2) {
        this.prop2 = value2;
        super(value1)
    }
}

// 抛出错误
```

***

### 父类实例，方法中的 super

子类方法中访问父类方法，可以使用特殊的快捷方式 `super`。

```js
class User {
    name;

    constructor(name) {
        this.name = name;
    }

    getName() {
        return this.name;
    }
}

class ContentWriter extends User {
    posts = [];

    constructor(name, posts) {
        super(name);
        this.posts = posts;
    }

    getName() {
        const name = super.getName();

        if (name === '') {
            return 'Unknown';
        }

        return name;
    }
}

var writer = new ContentWriter('', ['Why I like JS']);
writer.getName();
// 'Unknown'
```

可以在静态方法中使用 `super`，访问父类的静态方法。<br>

***

## 对象类型检测 instanceof 

`object instanceof Class` 是用来判断 `object` 是否为 `Class` 实例的操作符。

```js
class User {
    name;

    constructor(name) {
        this.name = name;
    }

    getName() {
        return this.name;
    }
}

var user = new User('Jon Snow');
var obj = {};

user instanceof User;
// true

obj instanceof User;
// false
```

`user` 是 `User` 类的一个实例，因此 `user instanceof User` 的值为 `true`。<br>
空对象 `{}` 不是 `User` 的实例，相应的 `obj instanceof User` 的值为 `false`。<br>

`instanceof` 是多态的，该操作符认为子类实例也是父类的实例。

```js
class User {
    name;

    constructor(name) {
        this.name = name;
    }

    getName() {
        return this.name;
    }
}

class ContentWriter extends User {
    posts = [];

    constructor(name, posts) {
        super(name);
        this.posts = posts;
    }
}

var writer = new ContentWriter('John Smith', ['Why I like JS']);

writer instanceof ContentWriter;
// true

writer instanceof User;
// true
```

`writer` 是子类 `ContentWriter` 的实例，操作符 `writer instanceof ContentWriter` 是值为 `true`。<br>
`ContentWriter` 是 `User` 的子类，所以 `writer instanceof User` 的值为 `true`。<br>

***

## 类与原型

`JavaScript` 的 `class` 语法很好地抽象了原型继承机制，但 `class` 是在原型继承的基础上构建的，每个类都是一个函数，并在作为构造函数调用时创建一个实例。

```js
// class

class User {
    constructor(name) {
        this.name = name;
    }

    getName() {
        return this.name;
    }
}

var user = new User('John');

user.getName();
// 'John'

user instanceof User;
// true

// prototype

function User(name) {
    this.name = name;
}

User.prototype.getName = function() {
    return this.name;
}

var user = new User('John');

user.getName();
// 'Johb'

user instanceof User;
// true
```

***

# class/function component

## 写法

```js
// function component
function Welcome(props) {
    return <h1>Hello, {props.name}</h1>
}

// class component
class Welcome extends React.Component {
    render() {
        return <h1>Hello, {this.props.name}</h1>
    }
}
```

***

## function component 和 class component 区别

1. **syntax 语法：**
    - `function component` 语法更简单，只需要传入一个 `props` 参数，返回一个 `React` 片段。
    - `class component` 要求先继承 `React.Component` 然后常见一个 `render` 方法，在 `render` 里面返回 `react` 片段。

```js
// function component
function Welcome(props) {
    return <h1>Hello, {props.name}</h1>
}

// babel 编译之后

function Welcome(porps) {
    return React.createElement(
        'h1',
        null,
        'Hello, ',
        props.name
    )
} 

// class component
class Welcome extends React.Component {
    render() {
        return <h1>Hello, {this.props.name}</h1>
    }
}

// babel 编译之后

class Welcome extends React.Component {
    render() {
        return React.createElement(
            'h1',
            null,
            'Hello, ',
            this.props.name
        )
    }
}
```
2. **state 状态：**
    - `function component` 是一个普通的函数不能使用 `this.state，setState()`，也是因此函数组件也被叫做无状态组件。
    - 当一个组件需要用到状态时需要 `class component`。
3. **生命周期：**
    - `function component` 不具有生命周期。
    - `class component` 所有生命周期的钩子函数来自于 `React.Component`。

***

## 两者各自的优势

1. **function component：**
    - 易于编写阅读和测试。
    - 代码量少，上手容易。
    - 只负责表现层逻辑，不考虑状态改变，利于复用。
2. **class component：**
    - 需要实现一些容器组件时，需要改变内部状态来实现自组件的改变。
    - 需要使用生命周期钩子函数。
    - 需要减少组件的渲染次数时。

***

感谢以下文章<br>
https://juejin.cn/post/6844904040162131976#heading-4<br>
https://www.cnblogs.com/chrissong/p/10445191.html<br>


