## angular

- 元数据：描述组件属性（对类的修饰）
- 组件类：描述组件功能，调用依赖注入的一些 service。
- 模版：定义组件的视图，html元素、其他的组件、和指令

- zone.js
- rxjs
- typescript
- webpack
- karma
- jsdoc

- 趋近于 Web Components
- 模版功能丰富
- 引入了 java 中大量的优秀概念
- 模块化，自动双向数据绑定，依赖注入，语义化标签

angular1 的时候"scope 脏检查机制"。
后来默认绑定方式为“单向绑定”，每个组件背后都维护着一个独立的变化监听器。

zone捕获到异步异常，都会通过angular执行变化操作。每次检测都始于根组件，进行深度优先遍历