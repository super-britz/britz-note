## 混合对象“类”

面向对象编程：封装、继承、多态。

多态：父类的通用行为可以被子类重写，用来实现更特殊的行为。

当谈到继承时，JavaScript 只有一种结构：对象。

每个实例对象`new Function()`都有`__proto__`，指向它的 prototype。该原型对象也有一个自己的`__proto__` ，层层向上传递。根据定义，null 没有原型，并作为这个原型链中的最后一个环节。

当你 __创建函数__ 时，JS 会为这个函数自动添加 prototype 属性，用来存储要共享的属性和方法。

所有普通的`[[prototype]]`都会指向`Object.prototype`。

#### [`__proto__` 和 prototype 区别](https://www.zhihu.com/question/34183746)

__显式原型：__ 用来实现基于 __原型的继承与属性的共享，__
每一个函数在创建之后都会拥有一个名为 prototype 的属性，这个属性指向函数的原型对象。

注意⚠️：通过 Function.prototype.bind 方法构造出来的函数是个例外，它没有 prototype 属性。

__隐式原型：构成原型链，__ 同样用于实现基于原型的继承。
JavaScript 中任意对象都有一个内置属性 `[[prototype]]`，通过 `__proto__` 来访问。
`__proto__` __指向创建这个对象的函数的显式原型__（prototype）, 也就是找构造函数（constructor）的 prototype。

对象的创建方式：

1. 字面量形式（内置函数的语法糖）
2. 内置函数形式，可以当作构造函数由 new 调用。
3. ES5 中的方法 Object.create(obj)，之前被称为原型式继承。新对象的隐式原型指向 obj。

隐式原型`[[prototype]]`指向创建这个对象的函数 (constructor) 的 prototype

注意⚠️：`Object.prototype.__proto__` 的值是 null，也就是说 Object.prototype 是原型链的顶端，所以包含很多通用的功能。

怎么感觉 prototype 是找爸爸？，`__proto__`是找爷爷？

#### 属性设置和屏蔽

prototype 属性的值有一个默认属性 constructor 的对象，不是空对象，new 的时候会执行构造函数，创建实例，实例的__proto__指向函数的 prototype 属性。

- 对象的__proto__指向自己构造函数的 prototype 属性。

1. 函数声明时才带有默认属性 prototype ，prototype 属性又默认有一个公有且不可枚举的属性 constructor，constructor 指向的是当前声明的函数。
2. 通过 [new 实例化的一个函数](./this.md#L91)，原型（`__proto__`）上有一个 constructor 属性，指向创建这个实例对象的函数，即“声明的函数”。
3. 函数的 prototype 属性 也有一个默认的 constructor，也是指向“声明的函数”

#### constructor

```js
function Foo (name) { // 模拟类时，建议首字母大些
    this.name = name;
}
Foo.prototype.myName = function () { // 实例化之后可以通过原型链访问 myName 属性方法
    return this.name;
}

function Bar (name, label) {
    Foo.call(this,name); // 继承父类，在 Bar 构造函数执行的时候，修改 this
    this.label = label;
}

Bar.prototype = Object.create(Foo.prototype);
// 创建建一个新对象，重写 Bar 的原型（抛弃默认的原型），但是 Object.create 创建的新对象没有 constructor 属性，所以会通过原型链访问到 Foo 上的 constructor

Bar.prototype.constructor = Bar; 
// 假如想修复 constructor 指向，就就原型上追加 constructor 关联

Bar.prototype.label = function () {
    return this.label;
}

var person = new Bar("britz", 24);
```

```js
// 注意⚠️：继承的时候不要直接 "="，和 "new Foo()"
Bar.prototype = Foo.prototype; 
// `=`是直接引用，会修改父类的 prototype。
Bar.prototype = new Foo(); 
// new 是直接调用构造函数 Foo，可能会产生一定副作用
// 1. 假如往父类构造函数传递参数的时候，父类就会被执行两次
```

ES6 提供了一种可以直接修改关联的方法，对比如下
```js
// ES5
Bar.prototype = Object.create(Foo.prototype);

// ES6
Object.setPrototypeOf(Bar.prototype,Foo.prototype)
```

#### 检查类的关系

`object instanceof constructor` 只能处理对象和函数的关系。

instanceof 用来检测，构造函数 constructor 的 prototype 属性是否出现在 object 的原型链`[[prototype]]`中

注意⚠️：通过 Function.prototype.bind 方法构造出来的函数是个例外，它没有 prototype 属性。构造函数 constructor 的 prototype 会被替换成硬绑定的 prototype。、