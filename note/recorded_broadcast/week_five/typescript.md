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

接口（Interfaces）是对行为的抽象，而具体如何行动需要由类（classes）去实现（implements）。
常用于实现抽象类和对象的形状描述（类似对象结构的别名）

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
function add (arg1: number, arg2: number): number // 下边有具体函数的实现，这里不需要添加 declare 关键字

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

#### 声明文件

声明文件必需以 `.d.ts` 为后缀，一般来说，ts 会解析项目中所有的 *.ts 文件，当然也包含以 .d.ts 结尾的文件。所以当我们将 jQuery.d.ts 放到项目中时，其他所有 *.ts 文件就都可以获得 jQuery 的类型定义了。

```ts
// jQuery.d.ts
declare var jQuery: (selector: string) => any;
```