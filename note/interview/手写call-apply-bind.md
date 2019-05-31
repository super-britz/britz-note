#### 手写bind

1. new 绑定
2. 强绑定 bind apply call
3. 隐士绑定
5. 默认绑定 window，严格模式下 undefined

```js
Function.prototype.bind = function (oThis) { // 要绑定的this
  
  if (typeof this !== 'function') {
    throw new TypeError("您试图绑定的不是一个函数")
  }
  
  var _this = this; // 接下来在当前函数基础上绑定this
  var baseArgs = Array.prototype.slice.call(agruments,1); // 第一个是this, 剩下的是bind参数
  
  var fNOP = function(){}; 
  
  var fBound = function() { // bind 返回的函数
    var newArgs = Array.prototype.slice.call(agruments);
    // instanceof 判断是否是 new fBound 出来的
    return _this.apply(this instanceof fBound ? this : oThis,baseArgs.concat(newArgs));
  }
  
  // 需要维护被 bind 的函数原型
  if (this.prototype) { // Function.prototype 是没有原型的，所以判断
    fNOP.prototype = this.prototype;
  }
  
  fBound.prototype = new fNOP(); // new fBound() 的时候, 通过__proto__找到被 bind 的函数原型
  
  return fBound; // 返回一个函数
  
}
```

#### 手写 apply

1. 利用隐式绑定
2. 需要构造对象属性为需要绑定的函数
3. 需要删除属性函数
4. 返回函数执行结果

```js
Function.prototype.apply = function(context, args) { // context 是要绑定的对象，args是数组
  if(typeof this === 'function') {
    throw new TypeError("您试图绑定的不是一个函数");
  }
  var context = context || window;
  context.fn = this; // 构造一个属性，把当前 this 赋值为 context的属性
  
  var result = context.fn(...args); // 保存函数执行结果，留着返回
  delete context.fn;
  return result;
}
```

#### 手写 call

```js
Function.prototype.call = function(context, ...args) {
  if(typeof this === 'function') {
    throw new TypeError("您试图绑定的不是一个函数");
  }
  var context = context || window;
  context.fn = this;
  
  var result = context.fn(...args);
  delete context.fn;
  return result;
}
```

