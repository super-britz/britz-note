#### 手写 new

1. 创建（构造）一个全新的对象即**{}**
2. 对这个新对象执行[[Prototype]]链接
3. 将步骤1新创建的对象作为**this**的上下文 
4. 如果该函数没有返回对象，则返回**this**

```js
function new () {
  var obj = Object.create(null); // 创建一个新对象{}
  
  var Constructor = Array.prototype.shift.call(agruments) // 返回类数组对象第一个值
  
  obj.__proto__ = Constructor.prototype; // 进行[[Prototype]]链接

  var res = Constructor.apply(obj, arguments); // 将 obj 作为函数上下文

  return typeof res === 'object' ? res : obj; // 返回一个对象
}
```

