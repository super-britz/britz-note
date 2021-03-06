## 强制类型转换

值类型转换：一种类型转化成另一种类型。

- 类型转换：静态类型语言`编译阶段`（显式转换）
- 强制类型转换：动态语言`运行阶段`（隐式转换）

```js
var a = 42;
var b = a + ''; // 隐式转换，因为 "+运算符" 操作字符串, 拼接操作，强制转化
var c = String(a); // 显式转换
```

### 字符串、数字和布尔值转化规则

ToString、ToNumber、ToBoolean、ToPrimitive

#### `ToString`


| 基本类型  | 非字符串  |   字符串    |
| --------- | --------- | ----------- |
| null      | null      | "null"      |
| undefined | undefined | "undefined" |
| boolean   | true      | "true"      |
| number    | 1         | "1"         |

对象字符串化时会调用`toString()`方法，`toString()`也可以被显示调用。

| 复杂类型 | 非字符串 | 字符串  |
| -------- | -------- | ------- |
| Array    | [1,2,3]  | "1,2,3" |

#### `JSON.stringify()`

安全的 JSON 值都可以被字符串化。

- 字符串、数字、布尔值、null的`JSON.stringify()`跟`toString()`基本相同。
- 可以在`JSON.stringify()`的对象中自定义`toJSON`方法，那么可以在字符串前调用，返回一个可以被安全序列化的值。

```js
JSON.stringify("1") // ""1"" 含有双引号字符串
"1".toString(); // "1"
```

不安全的 JSON 值：

- undefined
- function(){}
- symbol()
- 循环引用（相互引用形成的无限循环）

在对象时遇到`undefined、function(){}`时，会自动忽略。

在数组时遇到`undefined、function(){}`时，返回 `null`，用来保证数组单元位置不变。

对循环引用的对象会报错。

```js
JSON.stringify(undefined) // undefined
JSON.stringify(function(){}); // undefined

JSON.stringify({a:function(){},b:"b",c:undefined}); // "{"b":"b"}"
JSON.stringify([1,function(){},undefined,4]); // "[1,null,null,4]"
```


#### `ToNumber`

| 基本类型  |   非数字   | 数字 |
| --------- | ---------- | ---- |
| null      | null       | 0    |
| undefined | undefined  | NaN  |
| boolean   | true/false | 1/0  |

转化失败的都是NaN，对以 0 开头的 16 进制按照 10 进制处理。

对象（包括数组）会先转换为`基本类型值`，再按`基本类型值`强制转换。

对象 ---> 基本类型值（ToPrimitive）--->数字（ToNumber）

ToPrimitive 会先执行Defalut操作。

- 先检查`valueOf()`方法，假如存在且返回`基本类型值`。就用`valueOf()`返回值执行`ToNumber`操作。
- 再检查`toString()`方法，存在且有`字符串返回值`。就用`toString()`返回值执行`ToNumber`操作。
- 如果都没有基本类型返回值，那就抛出 `TypeError` 错误

注意⚠️：通过Object.create(null) 创建的对象没有 prototype ，所以也没有`valueOf()、toString()`，也就没法强制转换。

#### `ToBoolean`

JavaScript定义了一些假值(false)：

- undefined
- null
- false
- +0 -0 NaN
- ""

所有JavaScript提供的对象（包括封装对象）都是真值(true)。

- `new Boolean(false) // true`
- `new Number(0) // true`
- `new String("") // true`
- `"''" // true`

但是DOM提供的为false，如`document.all`。


#### 字符串和数字之间的显式转换

通过`内置函数`(不是封装对象，不需要new)实现的

- `String()`遵循将值转化为字符串基本类型
- `Number()`遵循将值转化为数字基本类型

```js
String(1) // "1"  显式
Number("1") // 1  显式

(2).toString();// "2" 隐式+显式
+"3.14" // 3.14 普遍会认为 +运算符 是 "显式"

--"3.14" // ReferenceError "--"被当作递减运算符
- -"3.14" // 3.14
```

`Date.now() === +new Date()`

注意⚠️：`～运算符` 和 `字位运算符` 会产生类似强制转换的的作用。

#### 显式解析数字字符串

解析允许字符串中出现`非数字字符`，转换不允许出现非数字字符。

```js
// 解析
parseInt(42) // 42 默认10进制
parseInt("42px") // 42 

parseInt(1/0,19) ==> parseInt("Infinity",19) ==> parseInt("I",19) 
// i是字母a-z中第 9 位，在19进制里[0-9] + [a-i] 刚好 19 位，又是从0开始计算，所以结果时18
// parseInt 第二个参数范围 2～32

// 转换
Number("42") // 42
Number("42px") // NaN 
```

#### 显式转换为布尔值

`Boolean()`是显式的ToBoolean强制类型转换，但是最常用的还是`!!`，在if(..).. 这样的布尔值上下文中，建议使用显式转换，让代码更清晰。

#### 字符串和数字间的隐式转换

如果操作数是字符串，或能通过 ToPrimitive 转化为字符串，+则进行`拼接操作`

```js
// ⚠️注意一个坑
[] + {} // "[object Object]"
{} + [] // 0
```

#### 隐式转换布尔值

条件判断表达式

- if
- for 
- while
- do..while
- 三元表达式`? :`

#### || 和 && （也叫短路，即最短路径）

都是对第一个操作数进行条件判断

- || 如果第一个操作数是true，返回第一个数，反之返回第二个数。
- && 如果第一个操作数是true，返回第二个数，反之返回第一个数。
- ⚠️注意：并不会返回 true或者false,当作条件表达式时，会再进行隐式转换成布尔值

#### == 与 ===

`==` 允许强制类型转换，而 `===` 不允许。

JavaScript定义中，只要两个对象（包括函数、数组），只要指向同一个值就视为 `==` 宽松相等。null和undefined 宽松相等。

`==` 在比较不同类型的值的时候会发生隐式强制类型转换。

假如需要强制类型转换，__布尔值和字符串都会先转化为`数字`，再进行 == 比较。__ 对象会先转成基本类型再进行比较。


很多看起来不可思议的宽松比较== 
```js
[] == ![] // true  
// 1. 首先[]是真值true，因为!进行强制的类型转换和反转 ![]是假值false ===> 0
// 2. 又因为隐式转换 [] ===> [].toString ===> "" ===> 0

// 对象要先ToPrimitive（valueOf-->toString）再 ToNumber
// 字符串、布尔值 会 ToNumber

2 == [2] // true
'' == [null] // true 
0 == [undefined] // true 等价于 0 == ""
Number(null) // 0
Number(undefined) // NaN

// 数组[]

[].toString() // ""
Number([]) // 0

// 对象{}
({}).toString() // "[object Object]" 字符串，没法转换成数字
Number({}) // NaN，NaN也没法跟 0 比较，所以({}) != 0

// 单独用{} == 0 会报 SyntaxError
```