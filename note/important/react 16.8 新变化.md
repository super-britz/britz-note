## React 新变化

#### 普通组件

新增 render 的返回类型 fragments 和 string。

- React 元素，自定义组件等
- 数组或 fragments
  - <React.Fragment></React.Fragment> == 短语法<></>
- 插槽Portals，将任何可以被渲染的 react 子元素渲染到指定的 DOM 节点，ReactDOM.createPortal
- 字符串或数值类型
- 布尔类型或 null

以前必须 return 一个 html 标签，现在可以返回一个字符串、数组、多行的话可以用fragments<></>包裹

#### 函数组件

没有自身的状态，相同的props输入必然会获得完全相同的组件展示。不需要关心组件的一些生命周期函数和渲染的钩子更简洁。

#### 纯组件 PureComponent

shouldComponentUpdate 只能比较普通对象，数组如果是push，push回返回该数组的新长度，就比较不了。 setTimeout也会返回一个定时器编号。

PureComponent 会自动为我们添加**shouldComponentUpdate()**，内部需要比较计算，一般用于经常变的子节点。

可以通过immutable 创建不可变对象，来解决 shouldComponentUpdate 比较对象地址的问题。

只有render的时候才会渲染，渲染才会diff，shouldComponentUpdate可以阻止 render。

#### 高阶组件

参数是组件，返回值为**新组件**的函数。

#### 组件插槽

ReactDOM.createPortal 可以把组件内的东西，放到组件外。可以用来做弹窗等。

####  suspense 组件（悬念-->等待）

之前在react里面像异步必须写成表达式。

1. 创建一个fetcher，返回一个异步结果。
2. 成功就返回结果，失败就把异步任务 throw 出去。
3. suspense 接收 throw 出来的 async task，内部轮询。

**react-hooks-fetch**提供是一个插件

#### lazy 组件

动态加载组件

#### memo 组件

React.memo()是高阶函数，能将函数组件转换成类似于 React.PureComponent 的组件

1. 函数组件
2. 判断props的函数
3. 类似于 React.PureComponent 的组件

#### Context

订阅- 发布者模式

const { Provider, Consumer } = React.createContext("default");

父级组件Provider提供 value对象，子组件 Consumer 消费 value 对象，注意value是固定形式。

#### ref

React.createRef() 创建一个新对象，然后冻结新对象，应该是怕 ref 重复。

React.forwardRef()  把 ref 向其他组件传递。 

#### Error 错误组件

#### Hooks

React 15 里需要绑定 this，是因为在 React 的类组件中，当我们把 event 处理函数（也就是类里的方法）的**引用**作为回调函数。当方法被调用时，this 的值会回退到**默认绑定**，严格模式下就是 undefined，丢失了当前函数执行上下文。

 * useState 返回有状态值，以及更新这个状态值的函数
 * useEffect 接受包含命令式，可能有副作用代码的函数。
 * useContext 接受上下文对象（从React.createContext返回的值）并返回当前上下文值，
 * useReducer useState的替代方案。接受类型为(state，action) => newState的reducer，并返回与dispatch方法配对的当前状态。 
 * useCallback  返回一个回忆的memoized版本，该版本仅在其中一个输入发生更改时才会更改。纯函数的输入输出确定性
 * useMemo 纯的一个记忆函数
 * useRef 返回一个可变的ref对象，其.current属性被初始化为传递的参数
 * useImperativeMethods 自定义使用ref时公开给父组件的实例值
 * useMutationEffect 更新兄弟组件之前，它在React执行其DOM改变的同一阶段同步触发
 * useLayoutEffect DOM改变后同步触发。使用它来从DOM读取布局并同步重新渲染

#### React 源码

```js
// react 里面主要是一些 hooks
var hasSymbol = typeof Symbol === 'function' && Symbol.for; // 短路运算

var REACT_ELEMENT_TYPE = hasSymbol ? Symbol.for('react.element') : 0xeac7; // Symbol 或者 16进制

function createRef() { // 每次创建一个新对象，ref 的值就不会重复
  var refObject = {
    current: null
  };
  {
    Object.seal(refObject);// 封闭 refObject 对象，阻止添加新属性并将所有现有属性标记为不可配置
  }
  return refObject;
}
```

```js
// react-dom 比较难的是这个调度 scheduler, 假设以 30fps 运行
  var previousFrameTime = 33; 
  var activeFrameTime = 33;
```

react 批处理，事物机制，render时setstate才生效

