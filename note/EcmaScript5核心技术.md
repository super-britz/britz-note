#### EcmaScript5核心技术
1. 请写出弹出值，并解释为什么
```js
if (false) {  
  var a = 1; // 变量提升，当前作用域顶端，这个作用域顶端就是window
}
alert(a);    // underfined
```

```js
alert(test);         // function test() {}
var test = 1;
function test() {}   // 变量提升，函数也提升，优先级比变量高
alert(test);         // 1
```

```js
alert(a);         // function a(){alert(10)};
a();              // 10
var a=3;
function a(){
  alert(10) 
}
alert(a)          // 3
a=6; 
a();              // Uncaught TypeError: a is not a function
```

```js
function test() {
  console.log(1);
}
function init() {
  if (false) {
    function test() {  // 早期浏览器等于2，函数提升会脱离false 
      console.log(2);
    }
  }
  test();
}
init(); // chrome: Uncaught TypeError: test is not a function

// JS 引擎会有一下过程：
// 找到所有用 function 声明的变量，在环境中「创建」这些变量。
// 将这些变量「初始化」并「赋值」为 function(){ console.log(2) }。
// 开始执行代码 test()
// 因为flase，本题只有创建，没有初始化和赋值的过程
```
2. 请写出如下输出值，并解释为什么
```js
// 简单理解： this谁调用就指向谁, 没人调指 window
function test() {
  console.log(this);
}
var obj = {
  f: test
}; // 注意带分号

obj.f(); // {f: ƒ test()} 即 obj

(obj.f)(); // {f: ƒ test()} 即 obj，小闭包()() 

(false || obj.f)(); // window，(1+1)=2表达式能计算 ，逻辑短路返回值
```

```js
this.a = 20;
var test = {
  a: 40,
  init: function() {
    console.log(this.a);
  }
};

test.init();              // 40 this指向 test
var s = test.init;
s();                         // 20 this指向 window
```

```js
this.a = 20;
var test = {
  a: 40,
  init: () => { // 箭头函数会绑定父级作用域的this, 即test的this window
    console.log(this.a);
  }
};
test.init(); // 20 this指向 window
```

```js
function pp() {
  this.a = 20;
}
pp.prototype.a = 30;
(new pp).a   // 20, 把函数看作类，构造函数优先级要大于原型链的
```

```js
this.a = 20;
var test = {
  a: 40,
  init: function {
    console.log(this.a);
    function go() {
      console.log(this.a);
    }
    go(); // 没有人调用go，go就找到了宿主 window
  }
};
test.init(); // 20
```

```js
this.a = 20;
var test = {
  a: 40,
  init:()=> {
    console.log(this.a);
    function go() {
      this.a = 60;
      console.log(this.a);
    }
    go.prototype.a = 50;
    return go;
  }
};
var p = test.init(); // 60
p(); // 60
new(test.init())(); // 60 60
```

3.   请写出如下点击li的输出值，并用三种办法正确输出li里的数字
``` html
<ul>
  <li>1</li>
  <li>2</li>
  <li>3</li>
  <li>4</li>
  <li>5</li>
  <li>6</li>
</ul>

<script type="text/javascript">
  var list_li = document.getElementsByTagName("li");
  for (var i = 0; i < list_li.length; i++) {
    list_li[i].onclick = function() {
      console.log(i); // console.log(this.innerHTML)
    }
  }
</script>

// 1. 立即执行函数，构建出闭包
// 2. let 声明
// 3. innerHTML, 回归DOM的本质
```

4. 写出输出值，并解释为什么

```js
function test(m) { // 值传递，但是值的指向地址一样
  m = {v: 5}; // 重写，开辟一个新地址{}，m={}操作栈，m.v = 5操作堆
}
var m = {k: 30};
test(m);
alert(m.v); // undefined

// 值传递和引用传递（变量是值传递， 但是值本身是指向地址，所以引用传递）
```

5.请写出代码执行结果，并解释为什么? 
```js
function yideng() {
  console.log(1); 
}
(function () {
  if (false) {
    function yideng() {
      console.log(2);
    }
  }
yideng(); 
})();

// Uncaught TypeError: yideng is not a function
```
6.请用一句话算出0-100之间学生的学生等级，如90-100输出为1等生、80-90为2等生以此类推。不允许使用if switch等。

```js
10 - Math.ceil(grade/10)  //Math.ceil() 函数返回大于或等于一个给定数字的最小整数。
                          // 写代码少用if else，设计模式思维，数据结构
```

7.请用一句话遍历变量a。(禁止用for 已知var a = “abc”)
```js
// 扩展运算符
[...'abc']
Array.from('abc')
[...new Set("abc")] // 字符串去重

Array.prototype.slice()
// [].slice.call
Array.prototype.forEach()
// [].forEach.call
```

8.请在下面写出JavaScript面向对象编程的混合式继承。并写出ES6版本的继承。  
要求:Car是父类，BMW是子类。父类有颜色、价格属性，有售卖的方法。子类实现父类颜色是红色，价格是140000,售卖方法实现输出如下语句:将红色的 BMW 买给了小王价格是14万。
```js
function Car (color) {
  this.color = color;
}
Car.prototype.sail = function(){
  alert('HI! I am sell method');
}

function BMW (color) {
  Car.call(this. color)
  this.price = 140000;
}

var __proto = Object.create(Car.prototype);
__proto.constructor = BMW;
BMW.prototype = __proto;
BMW.prototype.sell = function() {
  alert('将红色的 BMW 买给了小王价格是14万');
}

var car = new BMW('red');
car.sail();
// 把原型链整明白
```
```js
class Car {
  constructor(color) {
    this.color = color;
  }
  sail() {
    alert('HI! I am sell method');
  }
}
class BMW extends Car {
  constructor(color) {
    super(color);
    this.price = 140000;
  }
  sell() {
    alert('将红色的 BMW 买给了小王价格是14万');
  }
}

BMW.prototype.sell = function() {
  alert('es6是es5的语法糖');
}
var car = new BMW('red');
car.sail();
car.sell(); // es6是es5的语法糖

```

9.请你写出如何利用EcmaScript6/7(小Demo)优化多步异步嵌套的代码?
```js
// 1. async await 配合 promise
// 2. generator * yield 配合 promise
```

10.【仔细思考】写出如下代码执行结果，并解释为什么。(12分)
```js
var length = 10;
function fn() {
  console.log(this.length); 
}
var yideng = { 
  length: 5,
  method: function (fn) { 
    fn();
    arguments[0](); 
  }
};
yideng.method(fn, 1);

// 10 2
// fn();
// arguments[0] = arguments.fn
// arguments[0]() = arguments.fn()
// window.length 返回当前窗口中包含的框架数量(框架包括frame和iframe两种元素).
```
> 深入理解let文章  [我用了两个月的时间才理解 let](https://zhuanlan.zhihu.com/p/28140450)  
> 推荐📚 1.  [重构 改善既有代码的设计]() 2. [代码的简洁之道]()