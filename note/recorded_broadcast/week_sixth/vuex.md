## vuex

vuex 是 vue 的状态管理工具，集中式存储管理应用的所有组件的状态（vue 的 data），并以相应的规则保证状态以一种可预测的方式发生变化。

状态存储是响应式的

- state: 驱动应用的数据源
- view: 以声明方式将 state 映射到视图
- actions： 响应在 view 上的用户输入导致的状态变化

单向数据流

![vuex](../../../images/recorded_broadcast/week_sixth/vuex_flow.png)

多个组件共享状态时，单向数据流的简洁性很容易被破坏；

- 多个视图依赖于同一状态。
- 来自不同视图的行为需要变更同一状态。

vuex 架构图

![vuex](../../../images/recorded_broadcast/week_sixth/vuex.png)

#### 核心模块

- State: 单一状态树，用一个对象就包含了全部的应用层级状态。
- Getters：类似于 store 的计算属性
- Mutations：更改 Vuex 的 store 中的状态的唯一方法（必须同步执行）
- Action： Action 提交的是 mutation，而不是直接变更状态，actions 函数接收一个 context 对象（可以异步执行，返回 Promise）
- Module：将 store 分割成模块（module）