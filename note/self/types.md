## ES5类型和语法

### Number

JavaScript 中采用的是IFEE 754标准实现的 Number 类型，实现的是 __双精度浮点数__（64位二进制） ，真正意义上没有`整数`。

注意⚠️：`. 运算符`，会被优先识别为数字常量的一部分，其次才是对象属性访问运算符。
```js
42 === 42.0 === 42.

42.toFixed(3) // Uncaught SyntaxError
(42).toFixed(3) // "42.000"
0.42.toFixed(3) // "0.420"
42..toFixed(3) // "42.000"
42 .toFixed(3) // "42.000" 中间有空格
```

二进制浮点数的问题，处理整数是安全的。

```js
0.1 + 0.2 === 0.3 //false 
0.1 + 0.2 === 0.30000000000000004 // ture

// 二进制浮点数 0.1 和 0.2 的值，并不准确。
```

设置误差范围值， 在 ES6 中定义在 `Number.ESPILON`，判断两个数相减是不是小于`Number.ESPILON`

```js
function number (a,b) {
    return Math.abs(a-b) < Number.ESPILON
}
```

整数安全范围

- 最大安全整数：`2^53 - 1` Number.MAX_SAFE_INTEGER
- 最小安全整数：`-(2^53 - 1)` Number.MIN_SAFE_INTEGER
- 无穷大 `infinity`
- 无穷小 `-infinity`

null 是特殊关键字 、 undefined 是标识符 、NaN（不是一个数字 not a number）

- NaN：表示数学运算没成功，是失败返回的结果，是一个特殊值，自己不等于自己。
    - `isNaN("foo")` 有一个 bug，判断参数是不是 NaN 或不是数字
    - `Number.isNaN("foo")` 就比较可靠了
    - 利用不自等 `return n !== n;`


在 JavaScript 中引用指向的是值（value），而不是变量（key）