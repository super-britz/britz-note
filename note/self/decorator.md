## [装饰器](https://aotu.io/notes/2016/10/24/decorator/index.html)

Object.defineProperty() 方法会直接在一个对象上定义一个新属性，或者修改一个对象的现有属性，并返回这个对象。

#### 属性描述符

1. 数据描述符 是一个具有值的属性，该值可能是可写的，也可能不是可写的
2. 存取描述符 是由getter-setter函数对描述的属性。

描述符只能是2选1

数据描述符：
* configurable 当该属性的 configurable 为 true 时，该属性描述符才能够被改变。
* enumerable 该属性的enumerable为true时，该属性才能够出现在对象的枚举属性中。默认为 false。
* value 属性对应的值，默认为 undefined。
* writable 属性的 writable 为 true 时，value 才能被赋值运算符 (基本的赋值运算符是等号=) 改变，默认为 false。

存取描述符：
* configurable 当该属性的 configurable 为 true 时，该属性描述符才能够被改变。
* enumerable 该属性的enumerable为true时，该属性才能够出现在对象的枚举属性中。默认为 false。
* get 一个给属性提供 getter 的方法，如果没有 getter 则为 undefined。该方法返回值被用作属性值。默认为 undefined。
* set 一个给属性提供 setter 的方法，如果没有 setter 则为 undefined。该方法将接受唯一参数，并将该参数的新值分配给该属性。默认为 undefined。

#### ES6中的类

```js
// es6中的类,其实只是一个语法糖
class Cat {
  say() {
    console.log("meow ~");
  }
}
```
```js
// 实际上当我们给一个类添加一个属性的时候，会调用到 Object.defineProperty 这个方法，
// 它会接受三个参数：target 、name 和 descriptor ，所以上面的代码实际上在执行时是这样的：
function Cat() {}
Object.defineProperty(Cat.prototype, "say", {
  value: function() { console.log("meow ~"); },
  enumerable: false,
  configurable: true,
  writable: true
});
```

#### 类的装饰器

修饰器是一个对类进行处理的函数。修饰器函数的第一个参数target，就是所要修饰的目标类Cat。
```js
function isAnimal(target) {
  target.isAnimal = true;
  return target;
}
@isAnimal
class Cat {
  ...
}
console.log(Cat.isAnimal);    // true 

// 静态属性，可以操作target.prototype来添加实例属性
```

上面这段代码实际等同于:
```js
Cat = isAnimal(function Cat() { ... });
```

#### 类属性的装饰器

修饰器第一个参数是类的原型对象 xx.prototype，第二个参数是所要修饰的属性名，第三个参数是该属性的描述对象。
比如有的时候，我们希望把我们的部分属性置成只读，以避免别人对其进行修改，如果使用装饰器的话，我们可以这样来做：
```js
function readonly(target, name, descriptor) {
    discriptor.writable = false;
    return discriptor;
}
class Cat {
    @readonly
    say() {
        console.log("meow ~");
    }
}
var kitty = new Cat();
kitty.say = function() {
    console.log("woof !");
}
kitty.say()    // meow ~

// 修饰器可以接受参数 @readonly(params)，这就等于在修饰器外面再封装一层函数
// function readonly(params) {
//   return function (target, name, descriptor) {
//     discriptor.writable = false;
//     return discriptor;
//   }
// }
```

装饰器实际的作用形式是这样的：
```js
let descriptor = {
    value: function() {
        console.log("meow ~");
    },
    enumerable: false,
    configurable: true,
    writable: true
};
descriptor = readonly(Cat.prototype, "say", descriptor) || descriptor;
Object.defineProperty(Cat.prototype, "say", descriptor);
```
修饰器对类的行为的改变，是代码编译时发生的，而不是在运行时。
如果同一个方法有多个修饰器，会像`剥洋葱`一样，先从外到内进入，然后由内向外执行。

因为函数提升，所以修饰器不能作用于函数，如果一定要修饰函数，可以考虑高阶函数。

[core-decorators.js](https://github.com/jayphelps/core-decorators)提供了几个常见的修饰器