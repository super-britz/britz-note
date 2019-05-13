## webpack 原理解析

Webpack 万物都是模块 loader，打包速度取决于loader，多核打包happypack。

文件进行 loader 转化的时候：loader1 和 loader2 有一个传递的过程。

1. 把文件变成 string
2. 解析成 ast，遍历 ast 树
3. 该怎么操作怎么操作，真正的操作
4. 最后输入 string

#### 开发一个自己的loader

loaderUtils 是webpack的一个内置工具函数。getOptions(this)可以拿到当前 loader 的 options配置信息，

this 是构建运行时的上下文信息。

正则很好，但是不完善，所以用 ast

- acorn：解析器，用来 parse 字符串
- acorn-walk：遍历 ast 节点
- esprima：用来 parse 字符串
- estraverse：遍历  ast 节点，自己判断替换
- escodegen：生成 string
- [estree](https://github.com/estree/estree)：ast 格式作为操纵 JavaScript 源代码的通用语言。

```js
// loader 处理后的源文件生成抽象语法树 AST
type:描述该语句的类型 --变量声明语句
kind:变量声明的关键字 -- var
declaration: 声明的内容数组，里面的每一项也是一个对象 
  type: 描述该语句的类型
  id: 描述变量名称的对象 
    type:定义
    name: 是变量的名字 
    init: 初始化变量值得对象
    type: 类型
    value: 值 "is tree" 不带引号 
    row: "\"is tree"\" 带引号
```

#### 善用 Plugins

webpack实现插件机制的大体方式是: 

- 「创建」—— webpack在其内部对象上创建各种钩子; 
- 「注册」—— 插件将自己的方法注册到对应钩子上，交给webpack; 
- 「调用」—— webpack编译过程中，会适时地触发相应钩子，因此也就触发了插件的方法。 

```js
const pluginName = 'ConsoleLogOnBuildWebpackPlugin';
class ConsoleLogOnBuildWebpackPlugin {
  apply(compiler) { // 插件必须提供内部 apply 方法，不是 js 对象的 apply。
    compiler.hooks.run.tap(pluginName, compilation => {
      console.log('🍎The webpack build process is starting!!!');
    });
  }
}
module.exports = ConsoleLogOnBuildWebpackPlugin;
```

1. 插件必须提供内部 apply 方法，不是 js 对象的 apply。
2.  webpack.js 引入 Compiler 类进行整体编译。
3. 插件里小 compiler 是 Compiler 类的实例，也就是整个 webpack 的运行流程，compiler 可以访问整个编译生命周期。
4. 然后 webpack.js 遍历 Plugins，调用所有插件的方法。
5. compiler.hooks 本身是个对象。
6. hooks.run 指向 AsyncSeriesHook 的实例
7. AsyncSeriesHook 来自 tapable 文件，Compiler 和 Compilation 也继承自 tapable 文件的 Tapable
8. tapable 是一个第三方的消息通知的包，compiler.hooks.run.tap 表示在执行 run 的时候触发pluginName 订阅
9. compilation 代表当前运行的所有的 chunk

```js
const {
    SyncHook,          // 同步串行的钩子，不关心函数的返回值
    SyncBailHook,      // 同步串行的钩子，上一个返回值不为null，跳过剩下的逻辑
    SyncWaterfallHook, // 同步串行的钩子，上一个监听函数的返回值可以传递给下一个监听函数
    SyncLoopHook,      // 同步循环，监听函数返回值是 true 则继续循环监听，返回 undefined 退出循环
    AsyncParallelHook, // 异步并发，不关心函数的返回值
    AsyncParallelBailHook,    // 异步并发，只要监听函数的返回值不为 null，就会忽略后面的监听函数执行，直接执行这个被绑定的回调函数
    AsyncSeriesHook,          // 异步串行，不关心回调函数
    AsyncSeriesBailHook,      // 异步串行，回调函数的参数不为 null，就会直接执行回调函数
    AsyncSeriesWaterfallHook  // 异步串行，上一个监听函数的中的 callback(err, data)的第二个参数，可以作为下一个监听函数的参数
 } = require("tapable");

// tap 订阅
// call 触发钩子 
```

```js
// webpack主体函数就是一个立即执行函数
(function (modules) {
    var installedModules = {};// 缓存已经解析过后的包内容
    
    function __webpack_require__(moduleId) { // 声明一个函数，最后返回执行这个函数

        if (installedModules[moduleId]) { // 检查这个模块是否在缓存里，是就加载缓存里的 exports
            return installedModules[moduleId].exports;
        }
        
        var module = installedModules[moduleId] = { // 利用 moduleId 创建一个缓存
            exports: {}
        };
       
        /**
         * 1.module.exports：强绑定this，模块里的 this 就是 module.exports
         * 2.module：定义的一个空模块
         * 3.module.exports = {}：纯粹的对象
         * 4.__webpack_require__：传入函数
         */
        modules[moduleId].call(module.exports, module, module.exports, __webpack_require__);  // 执行这个入口函数
        
        return module.exports; // 赋值并返回 module.exports
    }
    //执行__webpack_require__
    return __webpack_require__("./src/index.js");
})({
    // 假如 index.js 引入了 async1.js
    "./src/index.js": (function (module, __webpack_exports__, __webpack_require__) {
        //1.import -> __webpack_require__
        var _async1_js__WEBPACK_IMPORTED_MODULE_0__ = __webpack_require__("./src/async1.js"); // 参数指向下一个  moduleId，返回 module.exports
        //5._async1_js__WEBPACK_IMPORTED_MODULE_0__ = module.exports
        console.log(_async1_js__WEBPACK_IMPORTED_MODULE_0__["default"]);
        console.log("webpack技术内幕");
    }),
    "./src/async1.js": (function (module, __webpack_exports__, __webpack_require__) {
        const data = "异步的脚本";
        //2.__webpack_exports__ == module.exports 是一个空对象{}
        //3.module.exports.default = data;
        //4.__webpack_require__ 执行的时候又把 module.exports.default 给 return 出去了
        this.yideng = "🍎";
        console.log("🛬",this);
        __webpack_exports__["default"] = (data);
    }),
});

// webpack 把 import 的模块，通过 __webpack_require__ 处理模块的id，然后导出
```

## webpack 5

webpack 遵守 commonJs 规范
打包的 main 文件里面新加的 startup，作为启动入口函数。
去除了 webpack4 main文件里的兼容判断，由用户控制。用于减少文件体积，提升了编译速度。
```js
optimization: {
  splitChunks: {
    cacheGroups: 
  },
  runtimeChunk:{
    name: 'runtime' // webpack 主题文件会多一个 webpackJsonpCallback 函数
  },
  moduleIds: 'size', // 上线用size，moduleIds是个数组，开发用 named moduleIds是个对象
  chunkIds: 'named', // 上线用size按模块打包的顺序，默认natural从模块起点开始计数
}
```

可以用 beyond compare 进行文件对比。
异步加载的时候，看看 chunkIds 的计数变化，浏览器缓存的时候 chunkId 变了，缓存就废了

把 chunkId 和 moduleIds 改成 hash 固定id

webpack5优化了这个id的问题，根据文件内容，实现一个简版的md5随机生成一个3-4位的数，改成可确定的 'deterministic' 参数值

webpack5 准备玩 cache: {type: 'filesystem'}
