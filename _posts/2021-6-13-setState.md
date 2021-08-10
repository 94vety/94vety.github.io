---
title: setState
description: setState 在 setTimeout 函数和原生事件中会立即执行，在合成事件以及 React 的钩子函数中会等待到下一批的批量更新操作进行执行（不会立即执行）.
tags:
- React
---

# 合成事件与原生事件

合成事件绑定

```js
<div className='dome' onClick={this.handleClick}></div>
```

普通事件绑定

```js
<div class='dome' onClick='handleClick()'></div>
```

***

## 合成事件

`React` 并不是将 `click` 事件绑在该 `div` 的真实 `DOM` 上，而是在 `document` 处监听所有支持的事件，当事件发生并冒泡至 `document` 处时，`React` 将事件内容封装并交由真正的处理函数运行. <br>
`React` 通过对象池的形式管理合成事件对象的创建和销毁，减少了垃圾的生成和新对象内存的分配，提高了性能.
 
![合成事件执行](https://z3.ax1x.com/2021/06/12/25G1WF.png)

***

## 合成事件与原生事件混合

```js
class Demo extends React.PureComponent {
    componentDidMount() {
        const $this = ReactDOM.findDOMNode(this);
        $this.addEventListener('click', this.onDOMClick, false);
    }

    onDOMClick = evt => {
        console.log('dom event');
    }

    onClick = evt => {
        console.log('react event');
    }

    render() {
        return (
            <div onClick={this.onClick}>Demo</div>
        )
    }
}

// 点击
// dom event
// react event
```

**分析：**<br>
首先 `DOM` 事件监听器被执行，然后事件继续冒泡至 `document`，合成事件监听器再被执行.

***

### 阻止冒泡

```js
class Demo extends React.PureComponent {
    componentDidMount() {
        const $this = ReactDOM.findDOMNode(this);
        $this.addEventListener('click', this.onDOMClick, false);
    }

    onDOMClick = evt => {
        console.log('dom event');
        evt.stopPropagation();
    }

    onClick = evt => {
        console.log('react event');
    }

    render() {
        return (
            <div onClick={this.onClick}>Demo</div>
        )
    }
}

// 点击
// dom event
```

**分析：**<br>
`DOM` 事件被阻止冒泡，无法到达 `document`，所以合成事件不会被触发.

### 复杂事件

```js
class Demo extends React.PureComponent {
    conponentDidMount() {
        const $parent = ReactDOM.findDOMNode(this);
        const $child = $parent.querySelector('.child');

        $parent.addEventListener('click', this.onParentDOMClick, false);
        $child.addEventListener('click', this.onChildDOMClick, false);
    }

    onParentDOMClick = evt => {
        console.log('parent dom event');
    }

    onChildDOMClick = evt => {
        console.log('child dom event');
    }

    onParentClick = evt => {
        console.log('parent react event');
    }

    onChildClick = evt => {
        console.log('child react event');
        evt.stopPropagtion();
    }

    render() {
        return (
            <div onClick={this.onParentClick}>
                <div className='child' onClick={this.onChildClick}>
                    Demo
                </div>
            </div>
        )
    }
}

// 点击
// child dom event
// parent dom event
// child react event
```

***

# setState 同步与异步

## 在钩子函数和 React 事件中

```js
constructor(props) {
    super(props);
    this.state = {
        number: 1
    }
}

componentDidMount() {
    this.setState({ number: 17 });
    console.log(this.state.number);
    this.setState({ number: 27 });
    console.log(this.state.number);
}

// 1
// 1
```

每次调用 `setState` 都会触发更新，出于性能考虑，`React` 会把多个 `setState()` 调用并成一个调用，减少重新 `render` 次数.

***

## 在异步函数和原生事件中

```js
setTimeout(() => {
    this.setState({ number: 17 });
    console.log(this.state.number);
}, 0)

// 17
```

通过结果看出类似于同步的结果.

***

## 面试题

```js
state = {
    number: 1
}

increment = () => {
    console.log('increment setState 前的 number', this.state.number);
    this.setState({
        number: this.state.number + 1
    })
    console.log('increment setState 后的 number', this.state.number);
}

triple = () => {
    console.log('triple setState 前的 number', this.state.number);
    this.setState({
        number: this.state.number + 1
    })
    this.setState({
        number: this.state.number + 1
    })
    this.setState({
        number: this.state.number + 1
    })
    console.log('triple setState 后的 number', this.state.number);
}

reduce = () => {
    setTimeout(() => {
        console.log('reduce setState 前的 number', this.state.number);
        this.setState({
            number: this.state.number - 1
        })
        console.log('reduce setState 后的 number', this.state.number);
    })
}

render() {
    return (
        <button onClick={this.increment}>点我增加</button>
        <button onClick={this.triple}>点我增加三倍</button>
        <button onClick={this.reduce}>点我减少</button>
    )
}

// 依次点击三个按钮
// increment setState 前的 number 1
// increment setState 后的 number 1
// triple setState 前的 number 2
// triple setState 后的 number 2
// reduce setState 前的 number 3
// reduce setState 后的 number 2
```

> 在 '异步' 中如果对同一个值进行多次 setState，setState 的批量更新策略会对其进行覆盖，取最后一次的执行

## 调用 setState 之后

![setState 工作流](https://z3.ax1x.com/2021/06/13/2Idzk9.png)

**isBatchingUpdates 为：**<br>
`true`：当前并未进行批量更新操作，`setState` 只能等待下一次批量更新.<br>
`false`：当前正在进行批量更新操作，`setState` 立即执行.<br>

1. `isBatchingUpdates` 是一个 `React` 全局唯一的变量，初始值为 `false`.<br>
2. 每当 `React` 去执行更新动作时，会将 `isBatchingUpdates` 置为 `true`，此时任何更新的组件都只能暂时进入 `dirtyComponents` 里排队等待下一次的批量更新.<br>
3. `isBatchingUpdates` 这个变量，在 `React` 生命周期函数以及合成事件执行前，已经被 `React` 修改为 `true`，此时所操作的 `setState` 不会立即生效.<br>
4. 当函数执行完毕，事务的 `close` 方法再把 `isBatchingUpdates` 改为 `false`.<br>
5. `isBatchingUpdates` 是在同步代码中进行的，而 `setTimeout` 的逻辑是异步执行的，当 `this.setState` 调用真正发生的时候，`isBatchingUpdates` 已被重置为 `false`，这就使得当前的 `setState` 可以立即执行.<br>

`setState` 本身不是异步的，因为 `React` 的性能优化机制体现为异步，在 `React` 的生命周期函数和合成事件为异步，在 `DOM` 原生事件以及 `setTimeout` 为异步.<br>
**注意：**<br>
&nbsp;&nbsp;&nbsp;&nbsp;以上所说的异步指的是多个 `setState` 合并到一起批量更新，并不是真正的异步.
 
***

感谢以下文章<br>
https://blog.csdn.net/qq_43293207/article/details/116669245<br>
https://juejin.cn/post/6969138101507604516#heading-4
