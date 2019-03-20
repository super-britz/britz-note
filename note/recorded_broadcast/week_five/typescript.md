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

// 字符串, 字符串模版
let nickName1: string = 'britz';
let nickName2: string = `britz`;

// Null 和 Undefined, undefined 和 null 是所有类型的子类型
let u: undefined = undefined;
let n: null = null;
let num: number = undefined;

// 空值, void 表示没有任何返回值的函数
function name(): void {
    console.log('britz');
}
```
#### 任意值, 类型推论

Any 用来表示允许赋值为任意类型。

变量声明的时候只要赋值，TypeScript 会根据值推测出一个类型
变量如果在声明的时候，未指定其类型，那么它会被识别为any类型。

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

```ts
let myFavoriteNumber: string | number; 
// 允许 myFavoriteNumber 的类型是 string 或者 number，但是不能是其他类型。
```

#### 接口, 可选属性, 任意属性，只读属性

接口（Interfaces）是对行为的抽象，而具体如何行动需要由类（classes）去实现（implements）。
常用于实现抽象类和对象的形状描述(类似对象结构的别名)

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
    readonly id: number; // 做为变量使用的话用const，若做为属性则使用readonly。

    name: string;
    age?: number; // 可选属性
    [propName: string]: any; // 允许有任意的属性, 值类型为 string
}

let britz: IPerson = {
    id: 1, // 创建(初始化)的时候被赋值
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

let list: any[] = ['britz', 24, { code: '123456789' }]; // any数组可以出现任意类型

// 类数组（Array-like Object）不是数组类型，比如 函数的 arguments
```

#### 函数的类型





