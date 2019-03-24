## ES6 模块化

模块是自动运行在“严格模式”下，并且没有办法退出运行的 JS 代码，模块中的变量仅在 **模块的顶级作用域** 中存在，并且模块必须导出一些变量或函数给外部调用。当然模块中也可以导入其他模块。模块顶级作用域中的 this 是 undefined

#### 导出 export

除了用 **default** 关键字，否则不能导出 **匿名函数或类**, 任何未显式导出的变量、函数或类都是模块私有的。

```js
// 导出变量
export var name = 'britz';
export let age = 24;
export const gender = 'male';

// 导出函数
export function sum (a, b) {
    return a+b;
}

// 先定义
class Person {
    constructor (student) {
        this.student = student;
    }
}
// 后导出类
export Person;

// 只定义，不导出，那么这个函数就是私有的
function privite (money) {
    return money;
}

```

#### 导入 import

`import` 要导入的标识符 `from` 从哪个模块导入。导入的标识符类似 const 定义的一样，无法定义同名或导入同名标识符。

```js
// 导入单个绑定
import { isArrayBuffer } from "lodash";
isArrayBuffer(value);

// 导入单个绑定
import { cloneDeep, isEmpty } from "lodash";

// 导入整个模块，所有的导出都可以作为对象 example 的属性导出
import * as example from "lodash";
example.cloneDeep(value);
example.isEmpty(value);
```

导入模块的代码执行后，**实例化过** 的模块会被保留在内存中，只要一个 import 语句就可以重复使用它。
上面代码导入了三次 `lodash`, 单`lodash`只执行一次，`isArrayBuffer、cloneDeep、isEmpty` 这些模块共用一个`lodash`实例。