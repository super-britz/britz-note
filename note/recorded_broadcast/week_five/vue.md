## Vue 相关

数据属性 (data)：只有当 vue 实例被创建时 data 中存在的属性才是响应式的，Object.freeze() 会阻止双向绑定。

实例属性：Vue 实例暴露了一些带前缀 $的实例属性与方法，以便与用户定义的属性区分开来。

> 不要在选项属性或回调上使用箭头函数，因为箭头函数是和父级上下文绑定在一起的，经常会因为 this 的指向而引发的错误。
> 
> 原本生命周期钩子的 this 上下文指向调用它的 Vue 实例。

#### 组件 && 组件通信

组件是可复用的 Vue 实例

- 每个组件必须只有一个根元素
- 一个组件的 data 选项必须是一个函数，因此每个实例可以维护一份被返回对象的独立的拷贝
- 通过 Prop 向子组件传递数据，v-bind 动态传递 prop

组件通信

- 父级组件可以像处理 native DOM 事件一样通过 v-on 监听子组件实例的任意事件，提供事件处理函数
- 子组件可以通过调用内建的 $emit 方法，传入事件名称和参数来触发一个事件，父组件监听处理这个组件

#### 插槽 slot

- 编译作用域，插槽在哪编译，就只能访问那个作用域的数据
- 默认内容，没有提供内容的时候被渲染默认内容
- 多个插槽，name 特性，不带 name 的插槽，出口隐含 `name="default"`, 有 `v-slot:name` 的 `<template>`会被视为默认插槽的内容（入口）
- 插槽 Prop，可以将子作用域的数据，绑定到父作用域的`<slot>`上`v-slot:default="slotProps"`，默认插槽的缩写语法`v-slot`不能和具名插槽`v-slot:name`混用
- 解构插槽 Prop，` v-slot="{ user }"`
- 动态插槽名，`v-slot:[dynamicSlotName]`
- 缩写， v-slot 也有缩写，即把参数之前的所有内容 `(v-slot:)` 替换为字符 `#`，`#`不会默认为`#default`。

#### vue 生命周期

![vue 生命周期](../../../images/recorded_broadcast/week_five/lifecycle.png)

生命周期钩子的 this 上下文指向调用它的 Vue 实例（组件类型的）。

beforeCreate: vue 实例刚刚被创建，初始化事件和组件生命周期
created : 组件实例已经创建，通过 data 挂载到 template 的 DOM 还没有生成，$el 属性还不存在
beforeMount：在挂载开始之前被调用：相关的 render 函数（模板）首次被调用。
mouted: data 已经挂载到 template 了，不能 100%保证组件已经在 document 中
beforeUpdate：数据更新时调用，发生在虚拟 DOM 重新渲染和打补丁之前
update: 数据更新后调用

* 应该避免在 update 期间更改数据状态，因为这可能会导致更新无限循环。 
* update 在服务器端渲染期间不被调用
* nextTick，Vue 完成更新 DOM ，可以在数据变化之后立即使用 Vue.nextTick(callback)
* watch，监听单个数据更新

beforeDestroy：实例销毁之前调用

destroyed: Vue 实例销毁后调用，但是还没有从 DOM 中移除

$mount 是 vue 实例没有 el 选项挂载的用法

[Vue 生命周期源码 - callHook](https://github.com/vuejs/vue/blob/dev/src/core/instance/lifecycle.js#L336)

1. Vue 的实例 vm（组件类型）hook（String 类型）
2. Vue.js 初始化合并 options 的过程，各个阶段的生命周期的函数也被合并到 vm.
3. 根据传入的字符串 hook，去拿到 vm.options[hook] 对应的回调函数数组
4. 执行的时候把 vm 作为函数执行的上下文。

[Vue 生命周期源码 - beforeCreate && created](https://github.com/vuejs/vue/blob/dev/src/core/instance/init.js#L16)

`Vue.prototype._init` 函数，合并 options，调用初始化函数，挂载到 DOM — $mount

```js
// 部分原码 
...
vm._self = vm
initLifecycle(vm) // 给 vm 对象添加了$parent、$root、$children、$refs 属性，以及一些其它的生命周期相关的标识。
initEvents(vm) // 初始化事件相关的属性
initRender(vm) // 添加了一些虚拟 dom、slots 等相关的属性和方法
callHook(vm, 'beforeCreate')  // 根据传入的字符串 hook，去拿到 vm.options[hook] 对应的回调函数数组，beforeCreate 拿不到 initState 初始化的数据，因为他在 initState(vm) 之后执行
initInjections(vm) // resolve injections before data/props
initState(vm) // 初始化 props / methods / data / observe data / computed / watch 数据
initProvide(vm) // resolve provide after data/props
callHook(vm, 'created') // created 可以拿到数据的，因为他在 initState(vm) 之后执行
...
if (vm.$options.el) { 
  vm.$mount(vm.$options.el) // 挂载
}
```
[Vue 生命周期源码 - mount && mounted](https://github.com/vuejs/vue/blob/dev/src/core/instance/lifecycle.js#L141)

mountComponent 函数，对于组件的 beforeMount 钩子函数，是先父后子。因为在组件的的虚拟 DOM 映射到真实 DOM 的过程中，显示父组件映射，这是会走 beforeMount 钩子，再往后发现父组件有子组件就会再执行一遍 patch，子组件会再执行 beforeMount 钩子，依次类推，最后 mounted 后分别插入父辈组件。

对于组件的 mounted 钩子函数的执行顺序是先子后父。

```js
// 部分原码 

callHook(vm, 'beforeMount') // 在执行 vm._render() 函数渲染 VNode 之前

updateComponent(...) // updateComponent 函数也就是 vm._update（将虚拟 DOM 映射到真实 DOM）

new Watcher(vm, updateComponent, noop, { // 下面 updated 要用
  before () {
    if (vm._isMounted && !vm._isDestroyed) {
      callHook(vm, 'beforeUpdate')
    }
  }
}, true /* isRenderWatcher */)
hydrating = false

if (vm.$vnode == null) {
    vm._isMounted = true
    callHook(vm, 'mounted')// 在执行完 vm._update() 后，不能保证 VNode patch 已经到了真实 DOM 
}
```

[Vue 生命周期源码 - beforeUpdate && updated](https://github.com/vuejs/vue/blob/dev/src/core/instance/lifecycle.js#L169)

beforeUpdate 发生在数据变化的前，初始化数据并不会触发
updated 发生在数据变化的后，初始化数据并不会触发
beforeUpdate 和 update 都只会在 mounted 以后调用

 [Watcher 类](https://github.com/vuejs/vue/blob/dev/src/core/observer/watcher.js#L26) 
```js
// 部分原码 

constructor (
  vm: Component,
  expOrFn: string | Function,
  cb: Function,
  options?: ?Object,
  isRenderWatcher?: boolean
  ) {
    this.vm = vm
    if (isRenderWatcher) { // 在实例化 Watcher 的过程中判断 isRenderWatcher
      vm._watcher = this // 把当前实例赋值给 vm._watcher
    }
    vm._watchers.push(this) // 还把当前 wathcer 实例 push 到 vm._watchers 中
    ...
  }
```
 [实例化 Watcher 类](https://github.com/vuejs/vue/blob/dev/src/core/instance/lifecycle.js#L197)
```js
new Watcher(vm, updateComponent, noop, { // 实例化一个渲染的 Watcher 去监听 vm 上的数据变化重新渲染
  before () { // before 函数是在数据变化的时候调用
    if (vm._isMounted && !vm._isDestroyed) { // 先判断是否 mouted 完成，说明初始化的时候不会调用该方法。
    callHook(vm, 'beforeUpdate') 
    }
  }
}, true /* isRenderWatcher */)

```
在数据变化的时候调用 [flushSchedulerQueue](https://github.com/vuejs/vue/blob/dev/src/core/observer/scheduler.js#L71) 函数，函数执行过程中判断执行 `watcher.before()`，也就是 `callHook(vm, 'beforeUpdate') `, 再去调用 callUpdatedHooks 函数。callUpdatedHooks 函数里判断执行 `callHook(vm, 'updated')`函数。

`vm._watcher` 是专门用来监听 vm 上数据变化然后重新渲染的，所以它是一个渲染相关的 watcher，因此在 callUpdatedHooks 函数中，只有 vm._watcher 的回调执行完毕后，才会执行 `callHook(vm, 'updated')`函数。

```js
// 部分原码 

const queue: Array<Watcher> = []

function flushSchedulerQueue () {
  ...
  for (index = 0; index < queue.length; index++) {
    watcher = queue[index]
    if (watcher.before) { 
      watcher.before() // 注意在调用 before 函数里有个判断
    }
    id = watcher.id
    has[id] = null
    watcher.run()
    ...
  
  const activatedQueue = activatedChildren.slice()
  const updatedQueue = queue.slice() // updatedQueue 是 更新了的 wathcer 数组

  resetSchedulerState()

  callActivatedHooks(activatedQueue)
  callUpdatedHooks(updatedQueue)  // 执行 callUpdatedHooks，

  if (devtools && config.devtools) {
    devtools.emit('flush')
  }
}

function callUpdatedHooks (queue) {
  let i = queue.length
  while (i--) {
    const watcher = queue[i]
    const vm = watcher.vm
    if (vm._watcher === watcher && vm._isMounted && !vm._isDestroyed) {
      // 判断 vm._watcher === watcher（也就是当前的渲染 watcher） 
      callHook(vm, 'updated') 
    }
  }
}
```

[Vue 生命周期源码 - beforeDestroy & destroyed](https://github.com/vuejs/vue/blob/dev/src/core/instance/lifecycle.js#L97)

```js
  Vue.prototype.$destroy = function () {
    const vm: Component = this
    if (vm._isBeingDestroyed) {
      return
    }
    callHook(vm, 'beforeDestroy') // 调用 beforeDestroy
    vm._isBeingDestroyed = true
    // remove self from parent
    const parent = vm.$parent
    if (parent && !parent._isBeingDestroyed && !vm.$options.abstract) {
      remove(parent.$children, vm) // 发现子组件，会先去销毁子组件
    }
    // teardown watchers
    if (vm._watcher) {
      vm._watcher.teardown() //  销毁 watcher
    }
    let i = vm._watchers.length
    while (i--) {
      vm._watchers[i].teardown() // 递归调用触发子组件的销毁钩子函数
    }
    // remove reference from data ob
    // frozen object may not have observer.
    if (vm._data.__ob__) {
      vm._data.__ob__.vmCount--
    }
    // call the last hook...
    vm._isDestroyed = true
    // invoke destroy hooks on current rendered tree 在当前渲染的树上调用 destroy hooks
    vm.__patch__(vm._vnode, null)
    // fire destroyed hook
    callHook(vm, 'destroyed')
    // turn off all instance listeners. 关闭所有实例监听器
    vm.$off() 
    // remove __vue__ reference
    if (vm.$el) {
      vm.$el.__vue__ = null
    }
    // release circular reference (#6759)
    if (vm.$vnode) {
      vm.$vnode.parent = null
    }
  }
}
```
