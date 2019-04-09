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
// 2. __proto__ 的构造函数指向 foo 函数
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

## 对象

构造函数形式 `new Object()`, 字面量形式`{}`

js 中的数据类型，除了 es6 新加的 Symbol，都是 __小写__

1. string
2. number
3. boolean
4. null
5. undefined
6. Symbol，是原始值，调用 new Symbol() 会报错。
7. object

前 6 种都是原始类型（简单基本类型）本身都不是对象，`typeof null === 'object'` 是个 bug。

object 是复杂基本类型，有许多特殊的对象子类型，被称为 __内置对象__
, 注意首字母都是 __大写__，这些内置函数可以当作构造函数，参考 new 一个函数 来使用
1. String
2. Number
3. Boolean
4. Object
5. Function，可调用的对象
6. Array
7. Date
8. RegExp, 正则表达式
9. Error

`Object.prototype.toString.call(xx)`从字面上看，是借助了 Object 的 toString 方法。

如果需要对字面量`I am a string`执行一些操作如获取长度 length，需要将字面量转换为 `String 对象`。当然聪明的 Javascript 已经自动帮你转了。

- string、number、boolean 这三种 js 引擎都会自动帮你转成对应的内置对象。
- null 和 undefined 没有对应的内置对象，所以只有字面量形式。
- Data 只有构造形式（内置 Data 对象）
- Object、Array、Function、RegExp，无论使用字面量形式还是构造形式，都是对象。
- Error 对象很少显式创建，一般是出现异常被自动创建。也可以`new Error(...)`创建。

在对象访问中，`.`语法属于“属性访问”，`[".."]`语法属于“键访问”

- 属性访问，属性名要命名规范。
- 键访问，使用字符串来访问属性，所以可以在程序中构造这个字符串。

对象中，属性名永远都是 __"字符串"__, 就算使用 string 字面量以外的值作为属性名，也会被转成字符串。

可计算属性： 
ES6 中可以通过`键访问 []`包裹表达式，将表达式作为属性名。
```js
var obj = {
  ["foo" + "bar"]: "foobar",
  [Symbol()]: "symbol"
};
```

对象属性与方法：
如果对象属性是一个`函数`，在其他语言中属于对象（也叫类）的函数叫“方法”，因此“属性访问”，也叫“方法访问”。但并不严谨，就算在对象里声明一个函数表达式，也只是对于相同函数的多个引用。

数组：数组也是对象，数组用来存储`数值下标／值`，数值下标可以看起来像 number

#### 复制对象（克隆）

- 浅复制，新对象和旧对象的`引用对象`是一致的。
- 深复制，假如有循环引用，就会死循环。

```js
// 深克隆
// 先序列化成一个 json 字符串，再解析出一个结构和值一样的对象。（只适用于部分情况）
var newObj = JSON.parse(JSON.stringify(oldObj));
``` 
```js
// 浅克隆
// 遍历一个或多个源对象的所有 “可枚举”的 "自有键", 并把他们复制（使用=赋值操作符）到目标对象
var newObj = Object.assgin({},oldObj);
```

#### 属性描述符

每个对象属性都有对应的 [属性描述符](./decorator.md#L5)

  - 数据描述符
  - 存取描述符

注意⚠️：有个例外，属性描述符`configurable:false`时，delete 属性被禁用。 但是我们还是可以将`writable:true`设置为`writable:false`，反之不行。

[可枚举属性](https://developer.mozilla.org/zh-CN/docs/Web/JavaScript/Enumerability_and_ownership_of_properties) 
是对象属性的 emumerable（可枚举）标志为 true。

- 通过直接的赋值和属性初始化的属性，默认都是 true。
- 通过 Object.defineProperty 等定义的属性，该标识值默认为 false。但是可以修改 emumerable 。

#### 不变性

所有方法创建的都是`浅不变性`, 只会影响目标对象和直接属性，引用类型的属性仍然是可变的。
1. const 定义后不能更改
2. 对象常量 `writable:false`和`configurable:false`
3. Object.preventExtensions(obj) 禁止扩展 obj 对象。
4. object.seal(obj) 把 obj 对象密封起来，实际是调用 Object.preventExtensions(obj) 并且将属性标记为`configurable:false`。
5. Object.freeze(obj) 冻结 obj 对象 ，实际是调用 object.seal(obj) 并将属性标记为 `writable:false`。