redux纯函数式编程的库，redux就是一个函子，实现流式编程，借鉴了reduce

1. 范畴论将世界抽象为对象和对象之间的联系，Linux 将所有I/O接口都抽象为文件，Redux将所有事件抽象为action。

2. 组合是另一种处理复杂事物的有效策略。将复杂任务拆分为多个低耦合度的简单的子任务。纯函数没有副作用，限制数据为不可变的，其选择使用流式（从右到左的数据流）操作来进行状态管理。

3. 高阶函数可以对函数抽象，因为如果函数只能以数值或对象为参数，会限制建立抽象的能力。如 map、filter 和 reduce 通过生成的具体的函数，可以解决很多问题。

4. 高阶函数组合，可以形成设计模式中的职责链，Ramda库提供一个api，pipe调用顺序从左到右。

5. [reduce](https://developer.mozilla.org/zh-CN/docs/Web/JavaScript/Reference/Global_Objects/Array/Reduce) 是对 Array 迭代操作的抽象，Redux借鉴了 reduce 的思想，是 reduce 在时间流处理上的一种特殊应用

6. redux 里的 createStore 三个参数 reducer initstate enhancer（middleware）

    - reducer 累积器，处理当前 state 和 action 的纯函数。 action可以操作store, 所以异步action函数里可以解构 dispatch, getState
    - initstate 初始值
    - 事件流列表 action list，增强 action

  Store 有以下职责：
    - 维持应用的 state；
    - 提供 getState() 方法获取 state；
    - 提供 dispatch(action) 方法更新 state；
    - 通过 subscribe(listener) 注册监听器;
    - 通过 subscribe(listener) 返回的函数注销监听器。

7. React组件本身也是纯函数，可以对 state 可以进行缓存（memoize）

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