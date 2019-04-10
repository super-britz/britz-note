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

#### 对象的 Get、Put、Getter、Setter、

对象默认的`[[Get]]` `[[Put]]`操作可以控制，属性访问和属性赋值

在语言规范中，obj.a 实际上是在 obj 上执行了`[[Get]]`操作。
1. 首先在当前对象 obj 中找 a 属性，找到就返回值，结束。
2. 没有找到就遍历可能存在的`[[Prototype]]`原型链
3. 如果怎么都找不到，就返回 undefined

`[[Put]]`操作时实际操作取决许多因素。
1. 属性是否是`访问描述符`，也叫存取描述符（属性描述符的一种），是并且 setter 存在，就执行 setter。
2. 属性是否是`数据描述符`（属性描述符的另一种），并且里面的 `writable:false`，如果是 false，严格模式下 TypeError，非严格模式下静默失败 。
3. 如果都不是，将值赋值给该属性。

getter（取值）和 setter（赋值）可以修改部分操作，但是只能应用于`单个属性`, 且都是`隐藏函数`。

对于`访问描述符`来说，js 会忽略`数据描述符`的 value 和 writable 特性。取而代之的是关心`set`、`get`、`configurable`、`enumerable`。

- get：给属性提供的 getter 方法
- set：给属性提供的 setter 方法

注意⚠️：只要 getter 返回的值不正确，就是 setter 正确，也取不到正确结果。

#### 存在性

- in 操作符可以检查`属性名`是否在对象及对象 [[Prototype]] 原型链中。
- hasOwnProperty 方法只检查`属性名`是否在对象自身属性中，不检查原型链。

普通对象都可以通过 `Object.prototype`委托来访问`hasOwnProperty 方法`, 但是通过`Object.create(null)`创建的对象没有原型，这时可以借助显示绑定`Object.prototype.hasOwnProperty.call(obj,"a")`

in 操作符检查的是属性名，数组的属性名是下标，如 `4 in [2,3,4] === false`，最好不要在数组遍历中使用 `for ... in `

"可枚举"相当于可以出现在对象属性的遍历中

`propertyIsEnumerable`方法会检查`属性名`是否直接存在对象中，不检查原型链，并且满足"可枚举属性"