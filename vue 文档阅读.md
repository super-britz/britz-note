![vue生命周期](https://cn.vuejs.org/images/lifecycle.png)

### 模版语法

#### 插值

vue 双向数据绑定，值得注意的是只有当实例被创建时就已经存在于 `data` 中的属性才是**响应式**的。

`Object.freeze()`会阻止修改现有的属性，也意味着响应系统无法再*追踪*变化。

由于 JavaScript 的限制，Vue **不能检测**数组和对象的变化。

- 文本 - 数据绑定最常见的形式就是使用“Mustache”语法 (双大括号) 
- 原始 HTML - 注意 `v-html` 很容易导致 XSS 攻击
- Attribute - 使用`v-bind` 指令
- 使用 JavaScript 表达式 - 只能包含**单个表达式**



#### 指令 (Directives) 

指令 attribute 的值预期是**单个 JavaScript 表达式** 

`v-bind:[attributeName]` 指令可以用于响应式地更新 HTML attribute 

`attributeName` 会被作为一个 JavaScript 表达式进行动态求值

`v-on:submit.prevent` 特殊后缀`.`修饰符 (modifier)

`.prevent` 修饰符告诉 `v-on` 指令对于触发的事件调用 `event.preventDefault()`



#### 计算属性和侦听器

模板内的**表达式**非常便利，但是设计它们的初衷是用于简单运算的。所以，对于任何复杂逻辑，你都应当使用**计算属性**。

**计算属性是基于它们的响应式依赖进行缓存的**，只在相关**响应式依赖**（data中的属性）发生改变时它们才会重新求值。

虽然计算属性在大多数情况下更合适，当需要在数据变化时执行异步或开销较大的操作时，**自定义的侦听器**是最有用的。



#### Class与Style绑定

当在一个自定义组件上使用 `class` 属性时，这些 class 将被添加到该组件的根元素上面。这个元素上已经存在的 class 不会被覆盖。



#### 条件渲染

当 `v-if` 与 `v-for` 一起使用时，`v-for` 具有比 `v-if` 更高的优先级。



#### 列表渲染

使用 `v-for `指令时，你也可以用 `of` 替代 `in` 作为分隔符，因为它更接近 JavaScript 迭代器的语法。

在自定义组件上，你可以像在任何普通元素上一样使用 `v-for`。



#### 事件处理

有时也需要在内联语句处理器中访问原始的 DOM 事件。可以用特殊变量 `$event` 把它传入方法。

**事件修饰符**

使用修饰符时，可以串联，所以顺序很重要；相应的代码会以同样的顺序产生。因此，用 `v-on:click.prevent.self` 会阻止**所有的点击**，而 `v-on:click.self.prevent` 只会阻止对元素自身的点击。

- `.stop` 阻止单击事件继续向下传播。
- `.prevent`  阻止事件的默认行为， 可以阻止 form 中的 submit 自己提交。
- `.capture` 添加事件监听器时使用事件捕获模式，即内部元素触发的事件先在此处理，然后才交由内部元素进行处理。
- `.self `只当在 event.target 是当前元素自身时触发处理函数，即事件不是从内部元素触发的。
- `.once`  点击事件将只会触发一次。
- `.passive` 修饰符尤其能够提升移动端的性能，passive与prevent相反，`.passive` 会告诉浏览器你*不*想阻止事件的默认行为。

**按键修饰符 **

Vue 允许为 `v-on` 在监听键盘事件时添加按键修饰符，使用 `keyCode attribute` 也是允许的。

- `v-on:keyup.enter`
- `v-on:keyup.13`
- ...

你还可以通过全局 `config.keyCodes` 对象自定义按键修饰符别名

**表单修饰符**

如果想自动将用户的输入值转为数值类型，可以给 `v-model` 添加 `number` 修饰符

如果要自动过滤用户输入的首尾空白字符，可以给 `v-model` 添加 `trim` 修饰符



#### 组件基础

**一个组件的 `data` 选项必须是一个函数**，因此每个实例可以维护一份被返回对象的独立的拷贝

为了能在模板中使用，这些组件必须先注册以便 Vue 能够识别。这里有两种组件的注册类型：**全局注册**和**局部注册**

- `Vue.component` 全局注册

**每个组件必须只有一个根元素**

**父子组件通信问题**

Vue 实例提供了一个自定义事件的系统来解决父子组件通信问题

父级组件可以像处理 native DOM 事件一样通过 `v-on` 监听子组件实例的任意事件

同时子组件可以通过调用内建的 `$emit`并传入事件名称来触发一个事件

**动态组件**通过 Vue 的 `component` 元素加一个特殊的 `is` attribute 来实现

**解析 DOM 模板**

- 字符串 (例如：template: '...')
- 单文件组件 (.vue)
- <script type="text/x-template">



## 深入了解组件

#### 组件注册

在注册一个组件的时候，我们始终需要给它一个名字。定义组件名的方式有两种：

- 使用 kebab-case `Vue.component('my-component-name', { /* ... */ })`
-  使用 PascalCase `Vue.component('MyComponentName', { /* ... */ })`

**全局注册** 用 `Vue.component` 来创建组件

**局部注册 **在 `components` **选项**中定义你想要使用的组件

**全局注册的行为必须在根 Vue 实例 (通过 `new Vue`) 创建之前发生**。



#### Prop

在 HTML 中的 attribute 名是大小写不敏感的，所以浏览器会把所有大写字符解释为小写字符。

**单向下行绑定**会防止从子组件意外改变父级组件的状态，从而导致你的应用的数据流向难以理解，跟 react 单项数据流目的类似。

注意在 JavaScript 中对象和数组是通过引用传入的，所以对于一个数组或对象类型的 prop 来说，在子组件中改变这个对象或数组本身将会**影响到父组件**的状态。

注意那些 prop 会在一个**组件实例创建之前**进行验证，所以实例的属性 (如 `data`、`computed` 等) 在 `default` 或 `validator` 函数中是不可用的。

对于绝大多数 attribute 来说，外部 attribute 会替换掉组件内的attribute，`class` 和 `style` attribute 会被合并起来



#### 自定义事件

不同于组件和 prop，事件名不存在任何自动化的大小写转换。在HTML 是大小写不敏感的，因此推荐**始终使用 kebab-case 的事件名**。

[自定义组件的 `v-model`](https://cn.vuejs.org/v2/guide/components-custom-events.html#自定义组件的-v-model)

可以使用 `v-on` 的 `.native` 修饰符，在组件上直接监听事件。

Vue 提供了一个 `$listeners` 属性，它是一个对象，里面包含了作用在这个组件上的所有监听器。

`this.$emit('update:title', newTitle)` 通过 `$emit` 修改props

`.sync` 修饰符的 `v-bind` **不能和表达式**一起使用



#### 插槽slot

如果组件模版里没有包含 `<slot></slot>` 元素，则组件引用时，`<component></component>`之间的任何内容都会被抛弃。

> 父级模板里的所有内容都是在父级作用域中编译的；子模板里的所有内容都是在子作用域中编译的。
>

 `<slot>默认值</slot>` 元素可以在标签间设置默认值。

一个不带 `name` 的 ``<slot></slot>`` 会带有隐含的`name = default`。

在向**具名插槽**提供内容的时候，我们可以在一个`<template>`元素上使用 `v-slot` 指令(也只能添加到 template 上)，并以 `v-slot` 的参数的形式提供其名称：`v-slot:header`

**作用域插槽 **

绑定在 `<slot>` 元素上的 attribute 被称为**插槽 prop**，在父级作用域中，我们可以使用带值的 `v-slot` 来定义我们提供的插槽 prop 的名字。`v-slot:default="slotProps"`

默认插槽可以缩写`v-slot="slotProps"`，具名插槽也可以缩写`#=slotProps`，**插槽 Prop**还可以解构`#="{slotProp}"`

#### 处理边界情况

`$root` 属性可以用来一个子组件中访问根实例

`$parent` 属性可以用来从一个子组件访问父组件的实例

访问子组件实例或子元素，`$refs`可以访问通过 `ref` 这个 attribute 为子组件赋予一个 ID 引用

> $refs 不是响应式的，避免在在模版和计算属性中使用

**依赖注入**

`provide` 选项允许我们指定我们想要**提供**给后代组件的数据/方法。

在任何后代组件里，我们可以使用 `inject` 选项来接收指定的`provide` 选项属性。

> 依赖注入与它们当前的组织方式耦合起来，使重构变得更加困难。同时所提供的属性是非响应式的

**程序化的事件侦听器**

- 通过 `$on(eventName, eventHandler)` 侦听一个事件
- 通过 `$once(eventName, eventHandler)` 一次性侦听一个事件
- 通过 `$off(eventName, eventHandler)` 停止侦听一个事件

```js
mounted: function () {
  this.attachDatepicker('startDateInput')
  this.attachDatepicker('endDateInput')
},
methods: {
  attachDatepicker: function (refName) {
    var picker = new Pikaday({
      field: this.$refs[refName],
      format: 'YYYY-MM-DD'
    })

    this.$once('hook:beforeDestroy', function () {
      picker.destroy()
    })
  }
}
```

**循环引用**

- 递归组件 - 组件是可以通过 `name` 选项在它们自己的模板中调用自身的。注意“max stack size exceeded”错误
- 父子组件之间的循环引用 
  - `Vue.component` 全局注册组件
  - 使用 webpack 依赖/导入组件，如何不经过其中一个组件而完全解析出另一个组件，给模块系统一个点。
  - 或者，在本地注册组件的时候，你可以使用 webpack 的异步 `import`

**强制更新**

1. 注意[数组](https://cn.vuejs.org/v2/guide/list.html#注意事项)或[对象](https://cn.vuejs.org/v2/guide/list.html#对象变更检测注意事项)的变更检测注意事项
2. 是否依赖了一个未被 Vue 的响应式系统追踪的状态。`provide/inject` 
3. 可以通过 [`$forceUpdate`](https://cn.vuejs.org/v2/api/#vm-forceUpdate) 手动强制更新



## 过渡&动画

**单元素/组件过渡** `transition` 的封装组件，添加进入/离开过渡

- 条件渲染 (使用 `v-if`)
- 条件展示 (使用 `v-show`)
- 动态组件
- 组件根节点

```vue
<transition name="fade">
   <p v-if="show">hello</p>
</transition>
```

当插入或删除包含在 `transition` 组件中的元素时，Vue 将会做以下处理：

1. 自动嗅探目标元素是否应用了 CSS 过渡或动画，如果是，在恰当的时机添加/删除 CSS 类名。
2. 如果过渡组件提供了 [JavaScript 钩子函数](https://cn.vuejs.org/v2/guide/transitions.html#JavaScript-钩子)，这些钩子函数将在恰当的时机被调用。
3. 如果没有找到 JavaScript 钩子并且也没有检测到 CSS 过渡/动画，DOM 操作 (插入/删除) 在下一帧中立即执行。(注意：此指浏览器逐帧动画机制，和 Vue 的 `nextTick` 概念不同)

![过渡的类名](https://cn.vuejs.org/images/transition.png)

