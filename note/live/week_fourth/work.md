1. [x-tag](https://x-tag.github.io/docs)
2. [pjax](https://github.com/defunkt/jquery-pjax) 代理 a 标签（pjax = pushState + ajax）为了网站的高性能渲染，不绑定事件，用 a 代替。用的 perfetch
3. 请求报头 x-pjax，只渲染 #app 的部分
4. [cheerio](https://github.com/cheeriojs/cheerio) 加载服务端直出的 html, 拿到需要替换 id 的 html 内容，服务器 swig 解析超快（ssr）
5. 直接返回一个 x-tag 的 html 模版，像 vue 把全局的 js 都打包成一个 main.js，我们是分开打包，所以还需要加载当前页面的 js。
6. 服务端渲染的 js，css 都是从 webpack 里来的，所以在资源标签里面加个 class，然后通过 cheerio 取到资源 `attr('src')`，cheerio 没有实现 outerHTML，我们自己给拼出来 `<script src="url"></script>`,把html、css、js 都返回给前端。`
7. 预加载 [quicklink](https://github.com/GoogleChromeLabs/quicklink)
8. `IntersectionObserver` API 提供了一种异步观察目标元素与祖先元素或顶级文档视口交叉的方法。
9. [resource-hints](https://www.w3.org/TR/resource-hints/)
   1. dns-prefetch
   2. preconnect
   3. prefetch
   4. prerender