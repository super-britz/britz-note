## Koa

一个基本的Koa应用 ，把中间件放到数组里，用 http 原有的 server 创建一个服务，把回调函数传进去，监听服务。

app.use 把 fn 放到 middleware 数组里。 

```js
use(fn) {
  this.middleware.push(fn);
  return this;
}
```

app.listen 就是创建一个服务，然后监听。

```js

listen(...args) {
  const server = http.createServer(this.callback());
  return server.listen(...args);
}
```

callback 组合  middleware，把 middleware 执行了。

```js
callback() {
  const fn = compose(this.middleware); // 返回一个可以用于执行的 middleware 函数

  const handleRequest = (req, res) => {
   // request, response 对象封装进一个单独模块
    const ctx = this.createContext(req, res); // 根据req和res模块，创建一个 ctx 上下文
    return this.handleRequest(ctx, fn); // 传入 ctx, middleware 函数里依赖 ctx
  };

  return handleRequest;
}
```

compose 是一个 koa-compose 库里的，接收一个 middleware ，返回一个函数带两个参数 context, next。

⚠️注意：需要留意的是 next 其实是 middleware 数组里的下一个 ctx 函数

```js
function compose (middleware) {
  return function (context, next) { // 注意，只是返回一个函数，还没执行
    let index = -1
    return dispatch(0)
    function dispatch (i) {
      if (i <= index) return Promise.reject(new Error('next() called multiple times'))
      index = i
      let fn = middleware[i]
      if (i === middleware.length) fn = next 
      // i 从 0 开始计数，当 i 等于最后一个，i 其实已经走过所有的 middleware 了。
      // 假如 context 里面有异步 await，所以形成了执行栈。 最后一个 next 其实没有指向的函数了。
      if (!fn) return Promise.resolve() // 假如 next 没有指向， 返回一个空的 Promise 对象。
      try {
        return Promise.resolve(fn(context, dispatch.bind(null, i + 1))); 
        // 首先是执行 fn，内部递归，强绑定this，并执行 dispatch，每次i+1，取到下一个middleware[i]，也咱们就是常写的 next
      } catch (err) {
        return Promise.reject(err)
      }
    }
  }
}
```

