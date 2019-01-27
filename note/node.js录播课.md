## node 使用笔记

#### REPL (Read Eval Print Loop: 交互式解释器）

- 读取 - 读取用户输入，解析输入了 Javascript 数据结构并存储在内存中。
- 执行 - 执行输入的数据结构
- 打印 - 输出结果
- 循环 - 循环操作以上步骤，直到 2 次 ctrl+c 、ctrl+d、 .exit  退出 REPL 。
- [REPL 命令](http://www.runoob.com/nodejs/nodejs-repl.html)

#### node.js npm 使用

- 安装模块 npm install xx，全局加 -g 、开发依赖 --save-dev、 项目依赖 --save
- 查看所有模块 npm list
- 查看模块版本号 npm list xx
- 更新模块 npm update xx
- 搜索模块 npm search xx
- package.json 文件 main 字段指定了程序的主入口文件，require('moduleName') 就会加载这个文件
- npm help install

#### node.js 回调函数

node.js 异步编程的直接体现就是回调，所有 API 都支持回调函数。
- 同步调用，回调，异步调用
- 回调是一种双向调用模式
- 阻塞和非阻塞
  - 阻塞 fs.readFileSync
  - 非阻塞 fs.readFile

#### node.js 事件驱动机制，非阻塞式 IO 或事件驱动 IO

事件驱动模型，当 web server 接收到请求，就把它关闭，接着进行处理，然后直接去服务下一个 web 请求。
当这个 web 请求处理完成，结果放到处理队列，等待执行队列，执行完成，结果返回给用户。
整个事件驱动的流程似于观察者模式

事件对象 -> 事件队列 -> 事件循环 -> 事件句柄

事件处理代码流程：
1. 引用 events 模块
2. 创建 eventEmitter 对象
3. 绑定事件及事件的处理程序
4. 触发事件

在 Node 应用程序中，执行异步操作的函数将回调函数作为最后一个参数， 回调函数接收错误对象作为第一个参数。

#### node.js 缓冲区 (buffer)

js 语言本身只有字符串数据类型，没有二进制数据类型。
处理像 TCP 流或文件流时，必须使用到二进制数据。
[buffer](http://www.runoob.com/nodejs/nodejs-buffer.html) 用来创建一个专门存放二进制数据的缓存区。

#### node.js 流 (stream)

[Stream](http://www.runoob.com/nodejs/nodejs-stream.html) 是一个抽象接口，有四种流类型：

- Readable - 可读操作。
- Writable - 可写操作。
- Duplex - 可读可写操作。
- Transform - 操作被写入数据，然后读出结果

#### node.js 模块化

模块化的概念和意义
- 为了让 node.js 的文件可以相互调用。
- 这个文件可能是 javascript 代码、json 代码或者编译过的 c/c++扩展 (.node 结尾）
- node.js 中存在四类模块，原生模块和 3 种文件模块
  * 原生模块 http、fs、path 等
  * 相对路径的文件模块 ./mod 或 ../mod
  * 绝对路径的文件模块 /pathtomodule/mod
  * 第三方文件模块 mod

require 文件查找策略：
- 从文件模块缓存中加载
- 从原生模块加载
- 从文件加载
- [require 详细加载流程](http://www.runoob.com/nodejs/nodejs-module-system.html)

![模块的加载流程](http://www.runoob.com/wp-content/uploads/2014/03/nodejs-require.jpg)

导出模块 module.exports

#### node.js 函数

和 js 基本一样

#### node.js 路由

路由根据请求的 URL 和需要的 get/post 参数来执行相应的代码。
Node.JS 的 url 和 querystring 模块，用来解析路由。

#### node.js 全局对象

global 对象，和 window 很像
- 在最外层定义的变量
- 全局对象的属性
- 隐式定义的变量（未定义直接赋值的变量）

__filename 表示当前正在执行的脚本的文件名，绝对路径

__dirname 表示当前执行脚本所在的目录。

[process](http://www.runoob.com/nodejs/nodejs-global-object.html) 全局变量，用于描述当前 Node.js 进程状态的对象。
- 成员方法
  * exit 当进程准备退出时触发
  *   ...
- 成员属性 
  * env 返回一个对象，成员为当前 shell 的环境变量
  * pid 当前进程的进程号
  * ...

#### node.js 常用工具

util 模块，提供常用函数的集合。
第三方的库 -> 函数式编程的那些库就可以用。

#### node.js 文件系统

[fs](http://www.runoob.com/nodejs/nodejs-fs.html) 文件系统模块
- 异步的 fs.readFile() 
- 同步的 fs.readFileSync()
- fs.open 打开文件
- fs.writeFile 写入文件
- fs.read 读取文件
- fs.close 关闭文件
- fs.unlink 删除文件
- fs.mkdir 创建目录
- ...

#### node.js 工具模块

-	OS 模块 提供基本的系统操作函数。
-	Path 模块 提供了处理和转换文件路径的工具。
-	Net 模块 用于底层的网络通信。提供了服务端和客户端的的操作。
-	DNS 模块 用于解析域名。
-	Domain 模块 简化异步代码的异常处理，可以捕捉处理 try catch 无法捕捉的。

#### node.js web 模块

Node.js 提供了 http 模块，http 模块主要用于搭建 HTTP 服务端和客户端

#### node.js RESTful API

REST（Representational State Transfer）一种软件架构风格，通常使用 JSON 数据格式。
基于 REST 架构的 Web Services 即是 RESTful

http 方法：

- GET - 用于获取数据。
- PUT - 用于更新或添加数据。
- DELETE - 用于删除数据。
- POST - 用于添加数据。