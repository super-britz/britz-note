## 语法

语句相当于句子，表达式相当于短语，运算符相当于表单符号和连词。句子是表达完整意思的一个多个短语组成，有的短语不能表达意思，有的可以。

```js
var a = 42;
console.log(a); // 42

var b = a++;
console.log(a); // 43
console.log(b); // 42

var c = ++a;
console.log(c); // 44
console.log(a); // 44
```

多个赋值语句串联（链式赋值）

```js
var a, b, c;
a = b = c = 42;
// c = 42;
// b = 42;
// a = 42;

// 注意运算符优先级，=优先级是4 .优先级是19

var obj = {};
var b = obj;
var c;

obj.a = b = c = 10;

// obj.a = 10;
// c = 10;
// b = 10;
```

代码块 `{}`
```js
[] + {} // "[object Object]"，被强制转化为字符串 "" + "[object Object]"
{} + [] // 0，{}被当作代码块，[]被强制类型转化为 0

if (true) console.log("if判断，单条语句的时候，可以省去代码块{}")
```

#### 自动分号插入;

只有在代码行末尾与换行符之间只有`空格和注释`时才会`自动分号插入;`

```js
var a = 42, b, //不会自动插入;
c; 

```

#### 错误

运行时错误：

- TypeError
- ReferenceError
- SyntaxError

`try..catch..finally`中假如都有返回值或者报错，后面的 finally 会覆盖前面 try 和 catch 的返回值或报错。

switch 中 case 的匹配算法是 === ，不会进行类型转化。

除了简单值，case 可以是表达式的结果值，用于和 true 比较。这时可以用!!，显式转化类型。

#### 宿主对象

- window
- global
- DOM 元素


#### 保留字

- 关键字 funtion、switch
- 预留关键字 enum、class、extend
- null 常量
- true/false 常量