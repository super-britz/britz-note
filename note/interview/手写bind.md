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