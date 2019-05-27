## React fiber

React16 的 virtual dom 是 fiber 架构，跟以前的 reconcile 算法不一样了。

fiber 底层是基于 requestIdleCallback 来调度 diff 算法的执行，利用空闲时间来完成任务。

requestIdleCallback Polyfill 使用了一个33毫秒，也就是最低 30fps。当浏览器执行频率可以大于30fps，

取前后2次中时间大的值，进行动态压帧。

#### 组件渲染时的阶段（Phase）

- 调解 Reconcile - 任务可以被打断。
	1. 依序遍历组件，通过diff 算法，判断组件是否需要更新，给需要更新的组件加上tag。
	2. 遍历完之后，将所有带有 tag 的组件加到一个数组中。
- 渲染 Commit -  根据 Reconcile 阶段生成的数组，遍历更新 DOM，这个阶段需要一次性执行完任务。

#### fiber 流程分析

- 1.获取当前系统的开始时间
- 2.设置任务的优先级
- 3.根据优先级别设置对应的过期时间
- 4.根据过期时间进行双向链表的排序
- 5.排完序了 然后如何执行呢？？？ensureHostCallbackIsScheduled
- 6.按照每一帧之后的空闲开始进行更新requestAnimationFrameWithTimeout，先用requestAnimationFrame，tab 页面切走了就用 setTimeout 代替 requestAnimationFrame 执行。
- 7.animationTick是线索，计算帧过期时间并动态压缩帧（2次都比预计时间要快，就动态压帧）
- 8.配合 MessageChannel 进行具体的调度任务，port2送信，port1接信。 
- 9.prevScheduledCallback == flushWork最终执行调度
- 原则：有过期的执行全部过期 没过期尽可能多的执行
- 剩下没执行完轮回到5 如果插入了高优先级的立即执行

requestIdleCallback／cancelIdleCallback

之前是递归，现在利用双向链表，因为react 都是组件化开发的，组件变化，需要通过双向链表找到变化组件的根节点。