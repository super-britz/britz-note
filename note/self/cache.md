## 浏览器缓存

- 强缓存：Cache-Control、Expires
- 协商缓存：ETag、If-None-Match、Last-Modified、If-Modified-Since

##### 强缓存

一旦资源命中强缓存, 浏览器便不会向服务器发送请求, 而是直接读取缓存.
Chrome下的现象是 200 OK (from disk cache) 或者 200 OK (from memory cache).

##### 协商缓存

一旦资源命中协商缓存, 返回304响应

##### 内存缓存(from memory cache)

- 快速读取：内存缓存会将编译解析后的文件，直接存入该进程的内存中，占据该进程一定的内存资源，以方便下次运行使用时的快速读取
- 时效性：一旦该进程关闭，则该进程的内存则会清空。

##### 硬盘缓存(from disk cache)

硬盘缓存则是直接将缓存写入硬盘文件中，读取缓存需要对该缓存存放的硬盘文件进行I/O操作，然后重新解析该缓存内容，读取复杂，速度比内存缓存慢。

##### 软刷新(soft refresh)

相当于 reload page , 正常情况下的缓存还是会用到。

##### 强刷新(Strong refresh)

在 Request Headers 中多了个属性： 都有 Cache-Control: no-cache 的标识。
这表明每次都需要服务器重新判断有效性，不走强缓存，可以走协商缓存，而不是直接不使用缓存。

##### 显示临时报头(Provisional headers are shown)

Request Headers ⚠️  Provisional headers are shown

出现这个信息是：因为获取相关资源的请求并没有发出，所以 Request Headers 被展示并不是真正的信息。真正的header只有在服务端返回的时候会更新，当请求被拦截后，并没有返回。

- 使用缓存时经常发生这种情况。
- 跨域，请求被浏览器拦截
- 请求被浏览器插件拦截
- 服务器出错或者超时，没有真正的返回

##### 总结

可以打开浏览器看下，大部分情况下浏览器会在 js、img、font 等文件解析执行后直接存入内存缓存中，
那么当刷新页面时只需直接从内存缓存中读取(from memory cache)；当然我也看到 js 从硬盘读取缓存(from disk cache) 的情况

css文件则会存入硬盘文件中，所以每次渲染页面都需要从硬盘读取缓存(from disk cache)
