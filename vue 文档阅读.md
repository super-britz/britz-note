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



