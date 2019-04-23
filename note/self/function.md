## 原生函数（内建函数、内置函数）

- String()
- Number()
- Boolean()
- Array()
- Object()
- Function()
- RegExp()
- Date()
- Error()
- Sumbol()

### 内部属性

函数内部属性`[[Class]]`，看作内部分类(函数分类)，无法直接访问，一般通过`Object.prototype.toString(..)`来查看。

```js
// call 强绑定 this，用于取到当前函数的`[[Class]]`

Object.prototype.toString.call([1,2,3]) // "[object Array]"

Object.prototype.toString.call(/regexp/i) // "[object RegExp]"

// 特殊的基本类型

Object.prototype.toString.call(null) // "[object Null]"

Object.prototype.toString.call(undefined) // "[object Undefined]"

// 基本类型

Object.prototype.toString.call('abc') // "[object String]"

Object.prototype.toString.call(123) // "[object Number]"

Object.prototype.toString.call(true) // "[object Boolean]"

Object.prototype.toString.call(Symbol()) // "[object Symbol]"
```

多数情况下，对象内部的`[[Class]]`属性和对象内部构建的`原生构造函数`相对应。

- null、undefiend 是个例外。
- 字面量声明的 string、number、boolean 会被各自原生函数自动包装（包装成对象）。

因为基本类型的值没有，`.length和toString()`这样的方法，需要将`基本类型`包装成`封装对象`

`valueOf()`可以取到封装对象的`基本类型`值。

正常情况使用字面量定义就行了，不需要刻意声明封装对象。