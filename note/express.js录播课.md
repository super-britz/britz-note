## Express 入门

核心特性：
- 中间件响应 http 请求
- 路由执行不同的 http 请求 action
- 模版传递参数来动态渲染 html 页面

express重要模块：
- body-parser - node.js 中间件，用于处理 JSON, Raw, Text 和 URL 编码的数据。
- cookie-parser - 这就是一个解析 Cookie 的工具。通过 req.cookies 可以取到传过来的 cookie，并把它们转成对象。
- multer - node.js 中间件，用于处理 enctype="multipart/form-data"（设置表单的 MIME 编码）的表单数据。

[Express](http://www.runoob.com/nodejs/nodejs-express-framework.html) 应用使用回调函数的参数： request 和 response 对象来处理请求和响应的数据。

Express 提供了内置的中间件 express.static 来设置静态文件如：图片， CSS, JavaScript 等。

#### 路由

路由包含一个 URI（或路径）和一个特定的 HTTP 请求方法（GET、POST 等），用于确定怎么响应客户端请求

```js
app.method(path, handler)
// app 是 express 的实例。
// method 是 HTTP 请求方法。
// path 是服务器上的路径。
// handler 是在路由匹配时执行的函数。
```
所谓“继承”就是在一个已存在的类的基础上建立一个新的类。
从另一角度说，从已有的类（父类）产生一个新的子类，称为类的派生。
针对派生类而言，根据基类的个数分：单继承 +  多继承

已存在的类称为“基类 (base class)”或“父类 (father class)”，新建的类称为“派生类 (derived class)”或“子类 (son class )”。

有一种特殊路由方法：app.all()，它并非派生自 HTTP 方法。
```js
app.all('/book', function (req, res, next) {
  console.log('给所有请求方法的路径/book 中添加中间件函数');
  next(); // 将控制传递给下一个处理程序
});
```
路由路径可以是字符串、字符串模式或正则表达式。查询字符串不是路由路径的一部分。

路由处理程序 handler 的形式可以是一个函数、多个函数、一组函数或者两者的结合，以类似于中间件的行为方式来处理请求。

app.route() 返回单个路由的实例，该实例可以处理带有可选中间件的 HTTP 谓词。使用 app.route（）可以避免重复的路由名称（从而避免拼写错误）。
```js
app.route('/book')
  .get(function(req, res) {
    res.send('Get a random book');
  })
  .post(function(req, res) {
    res.send('Add a book');
  })
  .put(function(req, res) {
    res.send('Update the book');
  });
```

"迷你 app" 使用 express.Router 类来创建可安装的模块化路由处理程序。
Router 实例是完整的中间件和路由系统；每个 Express 应用程序都有一个内置的 app Router。

#### 中间件

中间件函数能够访问请求对象 (req)、响应对象 (res) 以及请求/响应循环中的下一个中间件函数（next）

中间件函数：
+ 执行任何代码。
+ 对请求和响应对象进行更改。
+ 结束请求/响应循环。
+ 调用堆栈中的下一个中间件。

如果当前中间件函数没有结束请求/响应循环，那么它必须调用 next()，以将控制权传递给下一个中间件函数。否则，请求将保持挂起状态。

Express 应用程序可以使用以下类型的中间件：

+ 应用层中间件
+ 路由器层中间件
+ 错误处理中间件
+ 内置中间件
+ 第三方中间件

应用级中间件：使用 app.use() 和 app.method (method 是 HTTP 请求方法）函数将应用层中间件绑定到应用程序对象的实例。

路由器层中间件：使用 router.use() 和 router.METHOD() 函数装入路由器层中间件绑定到 express.Router() 的实例。

错误处理中间件：定义方式与其他中间件函数基本相同，差别在于错误处理函数有四个自变量而不是三个，专门具有特征符 (err, req, res, next)：

内置中间件：Express 中唯一内置的中间件函数是 express.static, 负责提供 Express 应用程序的静态资源。
[中间件函数列表](https://github.com/senchalabs/connect#middleware)

第三方中间件：[常用第三方中间件列表](http://expressjs.com/zh-cn/resources/middleware.html)

#### 使用模版引擎 

+ [swig](https://github.mayuxiao.com/swig.zh-CN/index.html)

#### 错误处理

错误处理中间件函数的定义方式与其他中间件函数基本相同，差别在于错误处理函数有四个自变量：(err, req, res, next)

一般在 app.use() 和路由调用之后，最后定义错误处理中间件。
中间件函数中的响应可以采用您首选的任何格式，例如，HTML 错误页、简单消息或 JSON 字符串。

对 next() 和 next(err) 的调用会表明当前处理程序是否完整以及处于何种状态。next(err) 将跳过链中所有剩余的处理程序（设置为 `next('route')` 处理错误的处理程序除外）。

缺省错误处理程序：Express 随附一个内置的错误处理程序，负责处理应用程序中可能遇到的任何错误。这个缺省的错误处理中间件函数添加在中间件函数集的末尾。

#### 调试 Express

Express 在内部使用调试模块来记录关于路由匹配、使用的中间件函数、应用程序模式以及请求/响应循环流程的信息。

debug 就像是扩充版的 console.log，但是与 console.log 不同，您不必注释掉生产代码中的 debug 日志。缺省情况下，日志记录功能已关闭，可以使用 DEBUG 环境变量有条件地开启日志记录。

#### 数据库集成

要将数据库连接到 Express 应用程序，只需在该应用程序中为数据库装入相应的 Node.js 驱动程序

- [MySQL](http://expressjs.com/zh-cn/guide/database-integration.html#mysql)
- [MongoDB](http://expressjs.com/zh-cn/guide/database-integration.html#mongo)
- [Redis](http://expressjs.com/zh-cn/guide/database-integration.html#redis)
- ...


supervisor、nodemon 热启动

log4 日志管理

orm，mysql 操作数据库

livereload