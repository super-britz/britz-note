#### 面向切面编程

AOP(面向切面编程)的主要作用是把一些跟核心业务逻辑模块无关的功能抽离出来，这些跟业务逻辑无关的功能通常包括数据统计、异常处理等。
比如在项目开发的结尾阶段难免要加上很多数据统计的代码，这个时候AOP就能发挥作用了。

AOP的好处首先是可以保持业务逻辑模块的纯净和高内聚性，其次是可以很方便地复用数据统计等功能模块。 

```js
Function.prototype.before = function(fn) {
  var __self = this; // 这个__self指向原函数
  return function() {
    fn.apply(this, arguments); // 新函数在原函数之前执行，apply保证this不被劫持
    return __self.apply(this, arguments); // 执行原函数并返回原函数的执行结果
  }
}

Function.prototype.after = function(fn) {
  var __self = this;
  return function() {
    var result = __self.apply(this, arguments); // 变量 result 保存结果，最后返回
    fn.apply(this,arguments); // 新函数在原函数之后执行
    return result;
  }
}
```

```js
// 原代码
var test = function () {
  console.log("2")
}

// AOP后的代码
test = test.before(function () {
  console.log("1");
}).after(function () {
  console.log("3");
} )

// 执行
test();
```

这种使用AOP的方式来给函数添加职责，也是JavaScript语言中的一种非常特别的巧妙的装饰者模式实现。