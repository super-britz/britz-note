## setState的原理解析

```js
// 先看一个例子
class Example extends React.Component {
  constructor() {
    super();
    this.state = {
      val: 0
    };
  }
  
  componentDidMount() {
    this.setState({val: this.state.val + 1});
    console.log(this.state.val);    // 第 1 次 log

    this.setState({val: this.state.val + 1});
    console.log(this.state.val);    // 第 2 次 log

    setTimeout(() => {
      this.setState({val: this.state.val + 1});
      console.log(this.state.val);  // 第 3 次 log

      this.setState({val: this.state.val + 1});
      console.log(this.state.val);  // 第 4 次 log
    }, 0);
  }
  render() {
    return null;
  }
};

// 0 0 2 3
```

#### 批处理机制 Batch Update

执行setState时，会将需要更新的state合并后放入状态**队列**，而不会立刻更新state，队列机制可以批量更新state。

直接 this.state 修改的时候，state不会放入状态队列中，下次调用 setState 时会对状态队列进行合并，会忽略之前直接被修改的 this.state。

#### 为什么要批处理

- 如果每一个 setState 之后就立即进行 dom diff，一段时间频繁触发就可能有性能问题。

- 在短时间内频繁setState。React会将state的改变压入栈中，在合适的时机，批量更新state和视图，达到提高性能的效果。

#### 如果避免被批处理  

setState是异步的，可以传递**回调函数**作为第二个参数

在setState的第一个参数中传入function，该function会被压入调用栈中，当state改变的时候，顺序执行。

#### 事务 Transaction

核心的状态判断在 [ReactUpdates.js](https://link.zhihu.com/?target=https%3A//github.com/facebook/react/blob/35962a00084382b49d1f9e3bd36612925f360e5b/src/renderers/shared/reconciler/ReactUpdates.js%23L199) 中

```js
// 排队更新 
function enqueueUpdate(component) {
  // ...

  if (!batchingStrategy.isBatchingUpdates) {
    batchingStrategy.batchedUpdates(enqueueUpdate, component); // 2
    return;
  }

  dirtyComponents.push(component); // 1
}

// 若 batchingStrategy.isBatchingUpdates 为 true，则把当前组件（即调用了 setState 的组件）放入 dirtyComponents 数组中
// 否则 batchUpdate 所有队列中的更新
```

```js
// batchingStrategy 是一个对象
var batchingStrategy = {
  isBatchingUpdates: false,

  batchedUpdates: function(callback, a, b, c, d, e) {
    // ...
    batchingStrategy.isBatchingUpdates = true;
    
    transaction.perform(callback, null, a, b, c, d, e); // 事务
  }
};
```

整个将 React 组件渲染到 DOM 中的过程就处于一个大的 Transaction 中。