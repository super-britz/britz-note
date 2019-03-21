## typescript 

JavaScript 的类型分为两种：原始数据类型（Primitive data types）和对象类型（Object types）。

#### 原始数据类型

原始数据类型包括：boolean、number、string、null、undefined 以及 ES6 中的新类型 Symbol。

```ts
// 布尔值
let isDone: boolean = false;
let createdByBoolean: boolean = Boolean(1);
let createdByNewBoolean: Boolean = new Boolean(1); // 报错， new Boolean() 返回的是一个 Boolean 对象

// 数值
let decLiteral: number = 6;
let hexLiteral: number = 0xf00d; //  十六进制
let binaryLiteral: number = 0b1010;// 二进制
let octalLiteral: number = 0o744;// 八进制
let notANumber: number = NaN;
let infinityNumber: number = Infinity; // ES6 中的二进制和八进制表示法，会被编译为十进制数字。

// 字符串，字符串模版
let nickName1: string = 'britz';
let nickName2: string = `britz`;

// Null 和 Undefined, undefined 和 null 是所有类型的子类型
let u: undefined = undefined;
let n: null = null;
let num: number = undefined;

// 空值，void 表示没有任何返回值的函数
function name(): void {
  console.log('britz');
}
```
#### 任意值，类型推论

Any 用来表示允许赋值为任意类型。

变量声明的时候只要赋值，TypeScript 会根据值推测出一个类型
变量如果在声明的时候，未指定其类型，那么它会被识别为 any 类型。

```ts
// 下面两组代码等价
let myFavoriteNumber;
let myFavoriteNumber: any;

let myFavoriteNumber = 'seven'; 
let myFavoriteNumber: string = 'seven';
```

#### 联合类型

联合类型（Union Types）表示取值可以为多种类型中的一种。
联合类型使用 `| `分隔每个类型

当 TypeScript 不确定一个联合类型的变量到底是哪个类型的时候，我们只能访问此联合类型的所有类型里共有的属性或方法：

```ts
let myFavoriteNumber: string | number; 
// 允许 myFavoriteNumber 的类型是 string 或者 number，但是不能是其他类型。
```

#### 接口，可选属性，任意属性，只读属性

接口（Interfaces）是对类的一部分行为进行抽象，而具体如何行动需要由类（classes）去实现（implements）。
常用于实现对象的形状描述（类似对象结构的别名）

```ts
interface IPerson {
  name: string;
  age: number;
}

let britz: IPerson = {
  name: 'britz',
  age: 24
};

// 我们约束了 britz 的形状必须和接口 IPerson 一致。
// 接口一般首字母大写。有的编程语言中会建议接口的名称加上 I 前缀。
```

* 可选属性：不要完全匹配一个接口，用可选属性：`?`
* 任意属性：接口允许有任意的属性，添加`[propName: string]: any;`
* 只读属性：只能在创建的时候被赋值
```ts
interface IPerson {
  readonly id: number; // 做为变量使用的话用 const，若做为属性则使用 readonly。
  name: string;
  age?: number; // 可选属性
  [propName: string]: any; // 允许有任意的属性，值类型为 string
}

let britz: IPerson = {
  id: 1, // 创建（初始化）的时候被赋值
  name: 'britz',
  age: 24, 
  code: '123456789' // 任意
};
```

#### 数组类型

```ts
let list: number[] = [1, 1, 2, 3, 5]; // type[]

let list: Array<number> = [1, 1, 2, 3, 5]; // 数组泛型 Array<type> 

interface INumberArray { 
  [index: number]: number; // 只要 index 的类型是 number，那么值的类型必须是 number。
}
let list: INumberArray = [1, 1, 2, 3, 5]; 

let list: any[] = ['britz', 24, { code: '123456789' }]; // any 数组可以出现任意类型

// 类数组（Array-like Object）不是数组类型，比如 函数的 arguments
```

#### 函数的类型，可选参数，参数默认值，剩余参数，重载

在 JavaScript 中，有两种常见的定义函数的方式 1. 函数声明（Function Declaration）2. 函数表达式（Function Expression）

```ts
// 函数声明（Function Declaration）
function sum(x: number, y: number): number {
  return x + y;
}

// 函数表达式（Function Expression）
let sum = function (x: number, y: number): number { 
  // 等号右侧的匿名函数有类型定义，左侧 sum 的类型是通过赋值操作推断出来
  return x + y;
};

// 函数表达式（Function Expression）
let sum: (x: number, y: number) => number = function (x: number, y: number): number {
  // 手动给 sum 添加类型
  return x + y;
};

// 在 TypeScript 的类型定义中，=> 用来表示函数的定义，左边是输入类型，需要用括号括起来，右边是输出类型。
// 在 ES6 中，=> 是箭头函数

interface SearchFunc {
  (source: string, subString: string): boolean;
}
let mySearch: SearchFunc;
mySearch = function(source: string, subString?: string):boolean { // 接口函数
  return source.search(subString) !== -1;
}
```
* 可选参数：`?`表示可选的参数，需要注意的是，可选参数必须是最后一个。
* 参数默认值：`=`允许给函数的参数添加默认值
* 剩余参数：允许通过 ...rest 的方式获取函数中的剩余参数，rest 参数只能是最后一个参数
* 真重载（java）：同一个函数名参数个数不同或参数类型不同时，执行不同的函数体，注意：ts 里的重载是假重载，是函数签名（函数名+参数类型，不包括返回值）重载
```ts
// 可选参数 lastName
function buildName(firstName: string, lastName?: string) {
  if (lastName) {
  return firstName + ' ' + lastName;
  } else {
  return firstName;
  }
}
let tomcat = buildName('Tom', 'Cat');
let tom = buildName('Tom');

// 参数默认值，firstName 为 Tom, lastName 为 Cat
function buildName(firstName: string = 'Tom', lastName: string = 'Cat') {
  return firstName + ' ' + lastName;
}
let tomcat = buildName('Tom', 'Cat');
let tom = buildName('Tom');

// 剩余参数
function push(array: any[], ...items: any[]) {
  // items 是数组 [1, 2, 3]
  items.map(function(item) {
  array.push(item);
  });
}
let a = [];
push(a, 1, 2, 3);

// 重载
function add (arg1: string, arg2: string): string // 声明
function add (arg1: number, arg2: number): number 

function add (arg1: string | number, arg2: string | number) { // 实现
  if (typeof arg1 === 'string' && typeof arg2 === 'string') {
  return arg1 + arg2
  } else if (typeof arg1 === 'number' && typeof arg2 === 'number') {
  return arg1 + arg2
  }
}
add(123, '123'); // 报错，假如不声明，不会报错
```

#### 类型断言

类型断言（Type Assertion）可以用来手动指定一个值的类型。`<type>value` 或 `value as type`。
注意：在 tsx 语法（React 的 jsx 语法的 ts 版）中必须用`value as type`。

类型断言不是类型转换，断言成一个联合类型中不存在的类型是不允许的。
类型断言好比其它语言里的类型转换，但是不进行特殊的数据检查和解构。 它没有运行时的影响，只是在编译阶段起作用。 

```ts
// 尖括号语法
let someValue: any = "this is a string";
let strLength: number = (<string>someValue).length;
// as 语法
let someValue: any = "this is a string";
let strLength: number = (someValue as string).length;

function getLength(something: string | number): number {
  if (something.length) { // 会报错，因为number没有length属性
  return something.length;
  } else {
  return something.toString().length;
  }
}

function getLength(something: string | number): number {
  if ((<string>something).length) { // 使用类型断言，将 something 断言成 string
  return (<string>something).length;
  } else {
  return something.toString().length;
  }
}
```

#### [声明文件](https://ts.xcatliu.com/basics/declaration-files.html)

声明文件必需以 `.d.ts` 为后缀，当第三方库有提供声明文件时，使用 `@types` 统一管理声明文件。
如果没有生效，可以检查下 `tsconfig.json` 中的 `files`、`include` 和 `exclude` 配置，确保其包含了 `xx.d.ts `文件。

第三方库没有提供声明文件时，我们就需要自己书写声明文件。

库的使用场景主要有以下几种：

* 全局变量：通过 `<script>` 标签引入第三方库，注入全局变量
* npm 包：通过 import foo from 'foo' 导入，符合 ES6 模块规范
* UMD 库：既可以通过 `<script>` 标签引入，又可以通过` import` 导入
* 模块插件：通过 `import` 导入后，可以改变另一个模块的结构
* 直接扩展全局变量：通过` <script> `标签引入后，改变一个全局变量的结构。比如为 `String.prototype` 新增了一个方法
* 通过导入扩展全局变量：通过 `import `导入后，可以改变一个全局变量的结构

**声明语句中只能定义类型，不能定义具体实现** 全局变量的声明文件主要有以下几种语法：

* declare var 声明全局变量，还有 declare let 和 declare const，一般来说，全局变量都是禁止修改的常量，建议使用declare const
* declare function 声明全局方法
* declare class 声明全局类
* declare enum 声明全局枚举类型
* declare namespace 声明全局对象（含有子属性），早期没有ES6时，为了解决模块化而创造的关键字
* interface 和 type 声明全局类型，在类型声明文件`.d.ts`中使用，防止interface，type命名冲突，可以放到 declare namespace 下，当然用的时候也需要加 namespace 的名称。
* 使用 declare global 可以在 npm 包或者 UMD 库中扩展全局变量的类型

创建一个 types 目录，专门用来管理自己写的声明文件。这种方式需要配置下 tsconfig.json 的 paths 和 baseUrl 字段。module 配置可以有很多种选项。

```js
// 在 commonjs 规范中，我们用以下方式来导出：

module.exports = foo;// 整体导出
exports.bar = bar;// 单个导出

// ts针对这种导出，有多种方式可以导入

const foo = require('foo');// 整体导入
const bar = require('foo').bar;// 单个导入

import * as foo from 'foo';// 整体导入
import { bar } from 'foo';// 单个导入

// ts 官方推荐的方式
import foo = require('foo');// 整体导入
import bar = require('foo').bar;// 单个导入
```
注意，只有 function、class 和 interface 可以直接使用ES6的 export default，其他的变量需要先通过declare定义出来，再export default：

#### 内置对象

内置对象是指根据标准在全局作用域（Global）上存在的对象。这里的标准是指 ECMAScript 和其他环境（比如 DOM）的标准。

[ECMAScript 标准内置对象](https://developer.mozilla.org/zh-CN/docs/Web/JavaScript/Reference/Global_Objects)
有：Boolean、Error、Date、RegExp 等。

[DOM 和 BOM 的内置对象](https://developer.mozilla.org/zh-CN/docs/Web/API/Document_Object_Model)
有：Document、HTMLElement、Event、NodeList 等。

内置对象的定义文件，在 TypeScript 核心库的[定义文件](https://github.com/Microsoft/TypeScript/tree/master/src/lib)中。

Node.js 不是内置对象的一部分，如果想用 TypeScript 写 Node.js，则需要引入第三方声明文件。
`npm install @types/node --save-dev`

#### 类型别名，字符串字面量类型

类型别名和字符串字面量常用于联合类型，可以使用 type 创建。

```ts
// 类型别名
type Name = string;
type NameResolver = () => string;
type NameOrResolver = Name | NameResolver;
function getName(n: NameOrResolver): Name {
  if (typeof n === 'string') {
    return n;
  } else {
    return n();
  }
}

// 字符串字面量类型
type EventNames = 'click' | 'scroll' | 'mousemove';
function handleEvent(ele: Element, event: EventNames) {
    // do something
}
handleEvent(document.getElementById('hello'), 'scroll');  // 没问题
handleEvent(document.getElementById('world'), 'dbclick'); // 报错，event 没有定义'dbclick'
```

#### 元组, 枚举
元组（Tuple）合并了不同类型的对象。直接对元组类型的变量进行初始化或者赋值的时候，需要参数相同，并且要符合类型
枚举（Enum）类型用于取值被限定在一定范围内的场景，比如一周只能有七天，颜色限定为红绿蓝等。
```ts
// 元组
let info: [string, number] // 定义一对值分别为 string 和 number 的元组
info = ['britz', 24]; // 变量初始化
info[0] = 'super';
info[1] = 25;
info.push('26'); // 越界, 类型只能为联合类型
info.push(27); // 越界
info.push(true); // 报错

// 枚举
enum Days {Sun , Mon, Tue, Wed, Thu, Fri, Sat}; //手动赋值

// 等价于
var Days;
(function (Days) {
    Days[Days["Sun"] = 0] = "Sun";
    Days[Days["Mon"] = 1] = "Mon";
    Days[Days["Tue"] = 2] = "Tue";
    Days[Days["Wed"] = 3] = "Wed";
    Days[Days["Thu"] = 4] = "Thu";
    Days[Days["Fri"] = 5] = "Fri";
    Days[Days["Sat"] = 6] = "Sat";
})(Days || (Days = {}));

// Days 等于
var Days = {
  0: "Sun",1: "Mon",2: "Tue",3: "Wed",4: "Thu",5: "Fri",6: "Sat",
  Sun: 0,Mon: 1,Tue: 2,Wed: 3,Thu: 4,Fri: 5,Sat: 6,
}

// 手动赋值
enum Days {Sun = 7, Mon, Tue, Wed = 1, Thu, Fri, Sat};

// 等价于
var Days;
(function (Days) {
    Days[Days["Sun"] = 7] = "Sun";
    Days[Days["Mon"] = 8] = "Mon"; // 未手动赋值的枚举项会接着上一个枚举项递增。
    Days[Days["Tue"] = 9] = "Tue";
    Days[Days["Wed"] = 1] = "Wed";
    Days[Days["Thu"] = 2] = "Thu";
    Days[Days["Fri"] = 3] = "Fri";
    Days[Days["Sat"] = 4] = "Sat";
})(Days || (Days = {}));

// 注意：如果枚举的赋值重复，导致 值被覆盖了，ts不会发现
// 计算所得项 ‘blue’.length 和 使用类型断言<any>value, 可能会导致未手动赋值的项，因为无法获得初始值而报错
```

#### 类 class

类的相关概念：

* 类(Class)：定义了一件事物的抽象特点，包含它的属性和方法
* 对象（Object）：类的实例，通过 new 生成
* 面向对象（OOP）的三大特性：封装、继承、多态
* 封装（Encapsulation）：将对数据的操作细节隐藏起来，只暴露对外的接口。外界调用端不需要（也不可能）知道细节，就能通过对外提供的接口来访问该对象，同时也保证了外界无法任意更改对象内部的数据
* 继承（Inheritance）：子类继承父类，子类除了拥有父类的所有特性外，还有一些更具体的特性
* 多态（Polymorphism）：由继承而产生了相关的不同的类，对同一个方法可以有不同的响应。比如 Cat 和 Dog 都继承自 Animal，但是分别实现了自己的 eat 方法。此时针对某一个实例，我们无需了解它是 Cat 还是 Dog，就可以直接调用 eat 方法，程序会自动判断出来应该如何执行 eat
* 存取器（getter & setter）：用以改变属性的读取和赋值行为
* 修饰符（Modifiers）：修饰符是一些关键字，用于限定成员或类型的性质。比如 public 表示公有属性或方法
* 抽象类（Abstract Class）：抽象类是供其他类继承的基类，抽象类不允许被实例化。抽象类中的抽象方法必须在子类中被实现
* 接口（Interfaces）：不同类之间公有的属性或方法，可以抽象成一个接口。接口可以被类实现（implements）。一个类只能继承自另一个类，但是可以实现多个接口

ES6中类的用法：

* 属性和方法：使用 class 定义类，使用 constructor 定义构造函数。通过 new 生成新实例的时候，会自动调用构造函数。
* 类的继承：使用 extends 关键字实现继承，子类中使用 super 关键字来调用父类的构造函数和方法。
* 存取器：使用 getter 和 setter 可以改变属性的赋值和读取行为：
* 静态方法：使用 static 修饰符修饰的方法称为静态方法，它们不需要实例化，而是直接通过类来调用：

ES7中类的用法：

* ES6 中实例的属性只能通过构造函数中的 `this.name = 'britz'`定义, ES7 可以`name='britz'`
* ES7 中，可以使用 static 定义一个静态属性

TypeScript 中类的用法：

三种访问修饰符（Access Modifiers），TypeScript中默认修饰符public 

* public 修饰的属性或方法是公有的，可以在任何地方被访问到
* private 修饰的属性或方法是私有的，只能在类的内部访问
* protected 修饰的属性或方法是受保护的，它和 private 类似，在子类中可以被访问

**抽象类：abstract 用于定义抽象类和其中的抽象方法，抽象类是不允许被实例化的。**
抽象方法，必须由子类实现，否则报错

类的类型与接口类似：
```ts
class Animal {
  name: string;
  constructor(name: string) {
    this.name = name;
  }
  sayHi(): string {
    return `My name is ${this.name}`;
  }
}

let a: Animal = new Animal('britz');
console.log(a.sayHi()); // My name is britz
```

#### 类与接口

接口（Interfaces）是对类的一部分行为进行抽象，而具体如何行动需要由类（classes）去实现（implements）。

类实现接口：不同类之间的共性提炼成接口（interfaces），用来提高面向对象的灵活性。

* 接口与可以继承接口
* 接口也可以继承类

#### 泛型

泛型（Generics）是指在定义函数、接口或类的时候，不预先指定具体的类型，而在使用的时候再指定类型的一种特性。当然，也可以不手动指定，而让类型推论自动推算出来。

泛型函数的类型与非泛型函数的类型没什么不同，只是有一个`<T>`或多个`<T, U>`类型参数在最前面，像函数声明一样。

```ts
// 多个类型参数，逗号分割
function swap<T, U>(tuple: [T, U]): [U, T] {
  return [tuple[1], tuple[0]];
}
swap([7, 'seven']); // ['seven', 7]

// 泛型约束：只允许这个函数传入那些包含 length 属性的变量。
interface Lengthwise {
  length: number;
}
function loggingIdentity<T extends Lengthwise>(arg: T): T { 
  // extends 约束了泛型 T 必须符合接口 Lengthwise 的形状
  console.log(arg.length);
  return arg;
}

// 多个类型参数之间互相约束
function copyFields<T extends U, U>(target: T, source: U): T {
  // 要求 T extends U，说明 U 的字段 T 都有
  for (let id in source) {
      target[id] = (<T>source)[id];
  }
  return target;
}
let x = { a: 1, b: 2, c: 3, d: 4 };
copyFields(x, { b: 10, d: 20 }); //  U 的字段 T 都有，没有就会报错

// 非泛型接口
interface CreateArrayFunc {
  (length: number, value: string): string[];
}
// 泛型接口，使用含有泛型的接口来定义函数的形状
interface CreateArrayFunc { 
  <T>(length: number, value: T): Array<T>;
}
// 也可以把泛型参数<T>提前到接口名上
interface CreateArrayFunc<T> {
  (length: number, value: T): Array<T>;
}
let createArray: CreateArrayFunc;
createArray = function<T>(length: number, value: T): Array<T> {
  let result: T[] = [];
  for (let i = 0; i < length; i++) {
    result[i] = value;
  }
  return result;
}
createArray(3, 'x'); // ['x', 'x', 'x']

// 泛型参数可以设置默认类型如：<T = string>
```

#### 声明合并

如果`定义`了两个相同名字的函数、接口或类，那么它们会合并成一个类型。

* 函数的合并：重载定义多个函数类型。
* 接口的合并：注意：合并的属性的类型必须是唯一的，否则报错。
* 类的合并: 类的合并与接口的合并规则一致。
* ...

```ts
interface Alarm {
  price: number;
}
interface Alarm {
  weight: number;
}
// 相当于
interface Alarm {
  price: number;
  weight: number;
}
```

#### [代码检查](https://ts.xcatliu.com/engineering/lint.html)
目前 TypeScript 的代码检查主要有两个方案：使用 TSLint 或使用 ESLint + typescript-eslint-parser。