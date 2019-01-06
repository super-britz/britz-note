#### 你不知道的HTML
推荐字体 `Comic Sans MS`  

html语义化
- 标签语义化有助于构架良好的html结构，有利于搜索引擎的建立索引、抓取
- 清晰的结构，有利于团队的开发、维护
##### 同源策略

1. 含义：
    + 协议相同
    + 域名相同
    + 端口相同
2. 目的：
    + 为了保证用户信息的安全，防止恶意的网站窃取数据。
3. 限制范围
    + Cookie、LocalStorage 和 IndexDB 无法读取。
    + DOM 无法获得。
    + AJAX 请求不能发送。

##### 回避同源策略
1. Cookie
    + Cookie 是服务器写入浏览器的一小段信息
    + 主域名相同，子域名不同，可以通过设置document.domain共享 Cookie。
2. iframe
    + 如果两个网页不同源，就无法拿到对方的DOM
    + 主域名相同，子域名不同，可以通过设置document.domain共享
    + 完全不同源的网站
        1. `片段识别符（fragment identifier）`  
        URL的#号后面的部分，子窗口通过监听hashchange事件得到信息通知
        2. `window.name`  
        只要在同一个窗口里，前一个网页设置了这个属性，后一个网页可以读取它。
        3. `跨文档通信API（Cross-document messaging）`  
        HTML5提供API [window.postMessage](https://developer.mozilla.org/zh-CN/docs/Web/API/Window/postMessage) 方法允许跨窗口通信。
        4. LocalStorage  
        通过window.postMessage，读写其他窗口的 LocalStorage, 还可以为 LocalStorage 扩容。
3. AJAX
    + JSONP  
    老式浏览器全部支持，服务器改造非常小。  
    网页通过添加一个`不受同源政策限制的HTML标签 常用<script>`还有img、link等   
    向服务器请求GET请求，请求的查询字符串有一个callback参数，用来指定回调函数的名字。  
    服务器收到请求后，将数据放在一个指定名字的回调函数里传回来。
    + [WebSocket](https://developer.mozilla.org/zh-CN/docs/Web/API/WebSocket)
        - WebSocket是一种通信协议，使用ws://（非加密）和wss://（加密）作为协议前缀。该协议不实行同源政策，请求的请求源（origin）只要服务器支持，就可以通过它进行跨源通信。
    + [CORS](https://developer.mozilla.org/zh-CN/docs/Web/HTTP/Access_control_CORS)（跨域资源共享 Cross-origin resource sharing）

        实现CORS通信的关键是服务器。只要服务器实现了CORS接口，就可以跨源通信  
        1. 简单请求（simple request）
        ```js
        请求方法是以下三种方法之一：

        1. GET
        2. HEAD
        3. POST

        HTTP的头信息不超出以下几种字段：

        1. Accept
        2. Accept-Language
        3. Content-Language
        4. Content-Type（需要注意额外的限制）

        Content-Type的值仅限于下列三者之一：

        1. application/x-www-form-urlencoded、
        2. multipart/form-data、
        3. text/plain            
        ```
        浏览器会在请求头信息之中，添加一个Origin字段，Origin字段用来说明，本次请求来自哪个源（协议 + 域名 + 端口），服务器根据Origin，决定是否同意这次请求。
        
        ```js
        假如不在许可范围内，服务器会返回一个正常的HTTP回应。
        浏览器发现响应头信息没有包含Access-Control-Allow-Origin字段，然后抛出一个错误，被XMLHttpRequest的onerror回调函数捕获。
        (抛出错误跟http状态码无关，http返回200跨域也是报错的)

        假如在许可范围内，服务器响应头会多出几个字段。
        - Access-Control-Allow-Origin 请求时Origin字段的值，要么是一个*，表示接受任意域名的请求。（必要字段）
        - Access-Control-Allow-Credentials 表示是否允许发送Cookie，只会是true, 或者不返回该字段（可选字段）
        - Access-Control-Expose-Headers（可选字段）
        ```
        2. 非简单请求（not-so-simple request） 预检请求：  
        首先使用 OPTIONS 方法发起一个预检请求到服务器，以获知服务器是否允许该实际请求。"预检请求“的使用，可以避免跨域请求对服务器的用户数据产生未预期的影响。  
         ```js
        请求头会包括Origin字段、  
        Access-Control-Request-Method用来列出浏览器的CORS请求会用到哪些HTTP方法（必要字段）、  
        Access-Control-Request-Headers 逗号分隔的字符串，指定浏览器CORS请求会额外发送的头信息字段（可选字段）
       
        假如在许可范围内，服务器响应头会多出几个字段。
        - Access-Control-Allow-Methods (该字段必需)，它的值是逗号分隔的一个字符串，表明服务器支持的所有跨域请求的方法,返回的是所有支持的方法,避免多次"预检"请求
        - Access-Control-Allow-Headers 假如有Access-Control-Request-Headers字段对对应关系，该字段就必需，
        - Access-Control-Allow-Credentials
        - Access-Control-Max-Age 指定本次预检请求的有效期，单位为秒，允许缓存该条响应多少秒，在此期间，不用发出另一条预检请求。
        ```

        ```js
        使用了下面任一 HTTP 方法：

        1. PUT
        2. DELETE
        3. CONNECT
        4. OPTIONS
        5. TRACE
        6. PATCH

        xxx...
        除了简单请求，就是非简单请求
        ```


            
        

