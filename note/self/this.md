## this && 对象原型

为了重复使用函数，需要不同的上下文对象。如果不用 this，那就需要显式为函数传递上下文对象。

this 是在函数运行时进行绑定的，this 上下文取决于函数调用时的各种条件。

this 有四种绑定规则：

1. 默认绑定，绑定到 window 上，严格模式下全局对象 window 不能进行默认绑定，就是 undefined。
2. 隐式绑定，当函数引用有上下文对象时，会把函数调用中的 this, 绑定到当前上下文对象上。只有最后一层在调用中起作用。
3. 显式绑定，直接指定 this 的绑定对象如 call、apply、bind 方法
4. new 绑定，只是对函数的“构造调用”
   1. 创建（构造）一个`全新的对象{}`
   2. 新对象会被执行`[[Prototype]]`链接
   3. 新对象会绑定到函数调用的 this
   4. 如果函数没有其他对象，那么自动返回这个新对象。

在 new 绑定中使用显式绑定（call、apply、bind），目的是`预先设置`函数的一些参数。柯里化的部分应用（除了第一个参数，剩余的参数都传递给下层函数）

```js
// 1. 默认绑定
function foo () {
    console.log(this.a)
}
var a = 2;
foo(2); // 不带任何修饰的函数引用进行调用，只能使用默认绑定。
```

```js
// 2. 隐式绑定
function foo () { // 注意声明方式
    console.log(this.a)
}
var obj = {
    a: 2,
    foo:foo // 先定义，后被添加函数引用的，会将函数的调用 this 绑定到这个上下文对象 obj 上
};
obj.foo(); // 2
```

```js
function foo () { // 注意声明方式
    console.log(this.a)
}
var obj = {
    a: 2,
    foo:foo 
};
var bar = obj.foo; // 实际引用的时 foo 函数本身，foo 的声明方式不带任何修饰，因此引用了“默认绑定”
var a = "全局环境"
bar(); // 全局环境
```

```js
function foo () {
    console.log(this.a)
}
function doFoo (fn) {
    // this.a = 3
    fn(); // fn 实际引用的时 foo
var obj = {
    a: 2,
    foo:foo 
};
var a = "全局环境"
doFoo(obj.foo); // 全局环境

```
假如打开 doFoo 注释，结果就是 3，因为 doFoo 也没有任何修饰，所以 this 指向 window，改变的是全局环境下的 a；

回调函数丢失 this 是很常见的，第三方库中的事件处理器常会强制绑定 this，将触发事件的 DOM 元素绑定到 this 上。bind 方法会把你指定的参数，设置为 this 的执行上下文，并调用。

```js
// 3. 硬绑定，创建一个包裹函数 bar，负责接收参数并返回值
function foo (something) {
    console.log(this.a + something);
    return this.a + something;
}
var obj = {
    a: 2
};
var bar = function () {
    return foo.apply(obj,arguments);
};
var b = bar(3);
```
在传统的面向类的语言中，构造函数是类中的特殊方法，使用 new 初始化的时候会调用构造函数。但是 javascript 中的构造函数只是可以被 new 操作符调用的函数。不属于类，也不会实例化一个类。

new 只是对函数的“构造调用”，构造一个新对象，并将它绑定到 foo() 调用中的 this 上。
```js
// 4.new 绑定
function foo (a) {
    this.a = a;
}
var bar = new foo(2); 
// 这里执行的 new foo(2) 等价于 {a: 2}
// 1. 创建一个新对象{}
// 2. 新对象会被执行 [[portotype]] 连接，新对象的 __proto__ 有一个 constructor 属性 指向 foo 函数。
// 3. {}会被绑定到 foo 函数调用中的 this 中，调用结束 {a:2}
// 4. 假如没有返回其他对象，就自动返回这个新对象
console.log(bar.a); // 2
```

#### 优先级

1. new 绑定
2. 显式绑定
3. 隐式绑定
4. 默认绑定

#### 出乎意料的 this

假如在 call、aplly、bind 中传入 null 或 undefined, 实际运用的是默认绑定规则。可能会有一些副作用，慎用。
常用安全的方法是创建一个 __非委托的空对象__ `Object.create(null)`没有原型。

```js
function foo () {
    console.log(this.a);
}
var a = 1;
var o = {a: 2,foo: foo};
var p = {a: 3}
o.foo(); // 2 隐式绑定 o.foo 有上下文对象
(p.foo = o.foo)(); // 1 赋值表达式返回的是目标函数的引用 foo, 这里会应用默认绑定。严格模式下 this 是 undefined
```

硬绑定（call、apply、bind）为了防止函数调用默认绑定规则，隐式绑定和显示绑定不能再修改 this, 降低了函数的灵活性。

软绑定（call、apply、bind）：加一层判断，`判断`调用时的 this, 为 undefined 或全局对象（window／global），就把传入的 obj 绑定到当前 this，否则不修改 this。

#### 箭头函数 =>

箭头函数的词法作用域由父级作用域决定，箭头函数的 this 无法被修改，只能改父级作用域。

1. 箭头函数本身没有 this、super、arguments、new.target 绑定。
2. 不能通过 new 关键字来创建，因为没有`[[construct]]`方法，不能作为构造函数
3. 没有原型，没有构造函数就没有原型。
4. 不可以改变 this 的绑定
5. 不支持 arguments 对象，只能通过“__命名参数__”和“__不定参数__“来访问函数参数
6. 不支持重复的命名参数。非严格模式下可以重复参数，但是后面会覆盖前面的。

#### JS 执行环境

- GO：全局执行上下文，global context
- EC：函数执行上下文，Execution Context
- ECS：函数执行栈，Execution Context Stack（GO对象在栈底，不会出栈，8M）
  - 爆栈，是栈放不下，大小超过 8M 。
  - 死循环，是当前函数不会出栈，其他函数没法执行。

EC 创建的时候，伴随着 AO、VO

- AO 活动对象，Active Object
- VO 变量对象，Variable Object

- scope chain 作用域链

- [[scope]] 属性

注意⚠️：闭包是存在堆里的，ECS 存的是一个引用。闭包会把 EC 保存，作用域链也就保存了。

```js
// 每个函数都有自己的 VO 对象，存储一些值
变量对象 VO
{
  1. 函数声明
  2. 函数形参
  3. 变量声明
}

// 函数执行阶段创建 AO 对象
活动对象 AO
{
  1. arguments 的 callee、length
  2. 内部定义的函数
  3. 绑定对应环境变量 
  4. 内部定义的变量
}

// AO 分为两步，定义阶段和执行阶段
// EC 内部有作用域链，由 AO 维护
EC {
  scopeChain:{Scope},
  AO {
    arguments: {
      0: a,
      length: 1
    },
    b: undefined
  }, 
  Scope:[AO, GO.vo], 
  this: 不确定, // ECS 执行的时候，this 才确定，所以this永远指向 ECS 的栈顶
}
```

Function 和 eavl 通过构造函数创建一个[[Scope]]，指向全局的词法作用域 