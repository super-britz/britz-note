## [Performance Timing](https://developer.mozilla.org/zh-CN/docs/Web/API/PerformanceTiming)

- Performance.navigation：统计的是一些网页导航相关的数据：
- [Performance Memory](https://developers.google.com/web/tools/chrome-devtools/memory-problems/?hl=zh-cn)：代表JavaScript对内存的占用。
- Performance Timing：包含了网络、解析等一系列的时间数据，需要在页面完全加载完成之后才能使用。

![Navigation Timing](../../../images/live/week_third/timing-overview.png)

#### 计算性能指标

* DNS查询耗时 = domainLookupEnd - domainLookupStart
* TCP链接耗时 = connectEnd - connectStart
* request请求耗时 = responseEnd - responseStart
* 解析dom树耗时 = domComplete - domInteractive
* 白屏时间 = domLoading - fetchStart
* domready时间 = domContentLoadedEventEnd - fetchStart
* onload时间 = loadEventEnd - fetchStart

#### PerformanceTiming API

1. navigationStart：上一个文档卸载(unload)结束时的UNIX时间戳。如果没有上一个文档，这个值会和PerformanceTiming.fetchStart相同。
2. unloadEventStart：unload事件抛出时的UNIX时间戳，如果没有上一个文档，这个值会返回0。
3. unloadEventEnd：unload事件处理完成时的UNIX时间戳，如果没有上一个文档，这个值会返回0。
4. redirectStart：第一个HTTP重定向开始时的UNIX时间戳。如果没有重定向，或者重定向中的一个不同源，这个值会返回0。
5. redirectEnd：最后一个HTTP重定向完成时（也就是说是HTTP响应的最后一个比特直接被收到的时间）的UNIX时间戳。如果没有重定向，或者重定向中的一个不同源，这个值会返回0.
6. fetchStart：浏览器准备好使用HTTP请求来获取(fetch)文档的UNIX时间戳。这个时间点会在检查任何应用缓存之前。
7. domainLookupStart：域名查询开始的UNIX时间戳。如果使用了持续连接(persistent connection)，或者这个信息存储到了缓存或者本地资源上，这个值将和 PerformanceTiming.fetchStart一致。
8. domainLookupEnd：域名查询结束的UNIX时间戳。如果使用了持续连接(persistent connection)，或者这个信息存储到了缓存或者本地资源上，这个值将和 PerformanceTiming.fetchStart一致。
9. connectStart：返回HTTP请求开始向服务器发送时的Unix毫秒时间戳。如果使用持久连接（persistent connection），则返回值等同于fetchStart属性的值。
10. connectEnd：返回浏览器与服务器之间的连接建立时的Unix毫秒时间戳。如果建立的是持久连接，则返回值等同于fetchStart属性的值。连接建立指的是所有握手和认证过程全部结束。
11. secureConnectionStart：返回浏览器与服务器开始安全链接的握手时的Unix毫秒时间戳。如果当前网页不要求安全连接，则返回0。
12. requestStart：返回浏览器向服务器发出HTTP请求时（或开始读取本地缓存时）的Unix毫秒时间戳。
13. responseStart：返回浏览器从服务器收到（或从本地缓存读取）第一个字节时的Unix毫秒时间戳。如果传输层在开始请求之后失败并且连接被重开，该属性将会被数制成新的请求的相对应的发起时间。
14. responseEnd：返回浏览器从服务器收到（或从本地缓存读取，或从本地资源读取）最后一个字节时（如果在此之前HTTP连接已经关闭，则返回关闭时）的Unix毫秒时间戳。
15. domLoading：返回当前网页DOM结构开始解析时（即Document.readyState属性变为“loading”、相应的 readystatechange事件触发时）的Unix毫秒时间戳。
16. domInteractive：返回当前网页DOM结构结束解析、开始加载内嵌资源时（即Document.readyState属性变为“interactive”、相应的readystatechange事件触发时）的Unix毫秒时间戳。
17. domContentLoadedEventStart：返回当解析器发送DOMContentLoaded 事件，即所有需要被执行的脚本已经被解析时的Unix毫秒时间戳。
18. domContentLoadedEventEnd：返回当所有需要立即执行的脚本已经被执行（不论执行顺序）时的Unix毫秒时间戳。
19. domComplete：返回当前文档解析完成，即Document.readyState 变为 'complete'且相对应的readystatechange 被触发时的Unix毫秒时间戳。
20. loadEventStart：返回该文档下，load事件被发送时的Unix毫秒时间戳。如果这个事件还未被发送，它的值将会是0。
21. loadEventEnd：返回当load事件结束，即加载事件完成时的Unix毫秒时间戳。如果这个事件还未被发送，或者尚未完成，它的值将会是0.
