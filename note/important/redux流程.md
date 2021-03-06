## redux

redux纯函数式编程的库，redux就是一个函子（创建容器），实现流式编程（组合），借鉴了 reduce

1. 范畴论将世界抽象为对象和对象之间的联系，Linux 将所有I/O接口都抽象为文件，Redux将所有事件抽象为action。

2. 组合是另一种处理复杂事物的有效策略。将复杂任务拆分为多个低耦合度的简单的子任务。纯函数没有副作用，限制数据为不可变的，其选择使用流式（从右到左的数据流）操作来进行状态管理。

3. 高阶函数可以对函数抽象，因为如果函数只能以数值或对象为参数，会限制建立抽象的能力。如 map、filter 和 reduce 通过生成的具体的函数，可以解决很多问题。

4. 高阶函数组合，可以形成设计模式中的职责链，Ramda库提供一个api，pipe调用顺序从左到右。

5. [reduce](https://developer.mozilla.org/zh-CN/docs/Web/JavaScript/Reference/Global_Objects/Array/Reduce) 是对 Array 迭代操作的抽象，Redux借鉴了 reduce 的思想，是 reduce 在事件流处理上的一种特殊应用

6. redux 里的 createStore 三个参数 reducer initstate enhancer（middleware）

    - reducer 累积器，处理当前 state 和 action 的纯函数。 action可以操作store, 所以异步action函数里可以解构 dispatch, getState
    - initstate 初始值
    - 事件流列表 action list，增强 action

  Store 有以下职责：
    - 维持应用的 state；
    - 提供 getState() 方法获取 state;
    - 提供 dispatch(action) 方法更新 state;
    - 通过 subscribe(listener) 注册监听器;
    - 通过 createStore(reducer, [initialState]) 创建 Store

7. React组件本身也是纯函数，可以对 state 可以进行 memoize 缓存，固定的 state 输入输出组件。

#### 总结

1. Redux创建一个容器 store，将整个应用状态存储到 store 上。
2. 一个组件可以更新 store，而不是直接通知其它组件。action -> dispatch-> (oldState,reducer) -> newState 
3. 其它组件可以通过订阅subscribe(listener）store 中的 state 来刷新自己的视图

```js
const redux = (reducer, initialState) => { 
  let currentState = initialState; 
 
  const dispatch = action => { 
    currentState = reducer(currentState, action); 
  }; 
  const getState = () => currentState; 
 
  return ({ 
    dispatch, 
    getState, 
  }); 
}; 
 
const store = redux(reducer, initialState); 
const action = { type, payload }; 
store.dispatch(action); 
store.getState(); 
```
## react redux

组件可以通过订阅store中的状态(state)来刷新自己的视图

connect监听到store发生变化，调用setState更新组件
