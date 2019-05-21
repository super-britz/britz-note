## Vue 源码解析

```js
├── compiler 		// 编译模版
├── core				// 核心模块
│   ├── components 		// 模板编译的代码
│   ├── global-api 		// 最上层的文件接口
│   ├── instance 			// 生命周期
│   ├── observer 			// 数据收集与订阅
│   ├── util 					// 常用工具方法类
│   └── vdom 					// virtual-dom
├── platforms 	// 对应的 web、weex 平台的补充代码
├── server 			// 处理服务端渲染
├── sfc 				// 处理单个.vue文件
└── shared 			// 提供全局用到的工具函数
```


#### 双向数据绑定

Object.defineProperty 是ES5的API，所以IE8不支持

Object.defineProperty 只能观察对象，不能观察数组。

san.js  `__defineSetter__`  `__defineGetter__ `

VB script 和打印机相关 ActiveXObject

Proxy Reflect

#### Vue 双向数据绑定

1. Object.defineProperty，注意 new 一个第三方的库，尽量不要挂在到 vue.data 上，vue生命周期结束null，利用 Object.freeze 冻结data属性
2. Observer
3. Watcher：是订阅者
4. Dep：加上 Object.defineProperty 是发布者，Dep收集依赖，subs
5. Directive：指令

#### 观察者模式

Vue2 用的Object.defineProperty不好，对 typescript 支持也不好，所以vue3 用 proxy，

#### 订阅者模式

主要涉及 3 个对象，发布者、主题对象、订阅者，一对多的关系。

主题对象发生变化，相关依赖对象会接到通知，并自动更新。

#### observer 数据收集与订阅

Observer会观察两种类型的数据，Object 与 Array。

Vue 不能检测 Array 变化，会先重写操作 Array.prototype，重写 Object.create(Array.prototype) 

* 当数组发生变化时，触发 notify
* 如果是 push，unshift，splice 这些添加新元素的操作，则会使用 observe r观察新添加的
  数据

重写完 Array.prototype 后，遍历拿到数组中的每个数据使用 observer 观察它。

而对于Object类型的数据，则遍历它的每个key，使用 defineProperty 设置 getter 和
setter，当触发getter的时候，observer则开始收集依赖，而触发setter的时候，observe
则触发notify。

#### 指令

 vue 编译这块分了两种类型

* 文本节点
* 元素节点

#### Vue 简版分析

1. new 一个 vue 实例，传入 option
2. observer (option.data, vm)，data 数据和 vue 实例
3. 然后 observe 遍历 data，defineReactive 传入对象的key、value 和  vue 实例
4. Object.defineProperty(vm, key, value)，把数据都挂载到实例上，value有 getter 和 setter 方法
5. Dep.subs 收集依赖，有addSub、notify方法。
6. Watcher == Dep.target 类似于添加监听
7. Compile 接收一个 node节点和 vue实例，创建**文档片段 CreateDocumentFragment**，利用while递归，递归编译dom环境，把vue实例添加进去，（监听假节点不用渲染）
8. 编译 dom 的时候有文本节点 {{}} 和元素节点 v-model，创建 Watcher
9. new  Watcher, 里面定义了 Dep.target = Watcher，每个Watcher有一个名字++uid，会造成数组（内存）不连续，导致内存泄漏。
10. Watcher 何时添加到 Dep 中 , this.value = this.vm[this.name] // 读属性的时候，触发对应的 get 
11. 发送通知 notify 的时候，同步处理会卡顿，所以使用批处理和异步处理，宏任务 setTimeout、微任务Promise、MutationObserver、MessageChannel
12. batcher 批处理队列，batcher是单例的，第一次清空队列，利用Watcher id和异步队列，实现批处理
13. 缺少调度机制 fiber，异步任务不会阻塞主线程，但是还是要回到主线程，可以是个大任务
14. async 是 generate的语法糖，next 会冻结当前词法作用域，原理是 throw err catch

#### Virtual-dom

为什么要用 Virtual-dom ? 

并不是因为最快，因为 Virtual-dom 肯定要编译，只是方便我们管理庞大的dom。解决了我们这些愚蠢的程序员对Dom的低劣操作。

dom 为什么慢？

创建一个 dom 元素，元素本身会带很多方法，很庞大。操作 Dom 的时候很容易导致页面重排。

1. 解析 parse ：把 template 模板字符串转换成 AST 树。AST 元素节点总共有 3 种类型：
	1. 普通元素
	2. 表达式
	3. 纯文本
2. 优化optimize：深度遍历这个 AST 树，检查子树的静态节点，标记  static 和 staticRoot， 静态节点生成的 DOM 永远不需要改变。Prepack 插件在编译阶段减少运行时的开销。
3. 合成 codegen

#### 生命周期

Keep-alive

#### Dom diff

Vue 的 Dom diff 是对第三方库的二次封装，vue的插件直接挂载到原型上。

`.`的代价很昂贵，因为要遍历原型链的key。

vue.use在 global-api文件夹里，限制一下注册组件的类型，然后执行

1. 头尾一致
2. 头或尾一致
3. 有没有key



react 

fiber

双向链表, 找根节点

过期任务，-1的任务



react 批处理，事物机制，render时setstate才生效



redux纯函数式编程的库，redux就是一个函子，实现流式编程，借鉴了reduce