## Webpack 环境配置

* 基本环境
* 开发环境
* 上线环境

合并 webpack 配置 webpack-merge

#### 基本环境

1. entry
2. output 
	
	* 设置html-webpack-plugin
	* 清理输出的文件夹
	* [`WebpackManifestPlugin`](https://github.com/danethurber/webpack-manifest-plugin) 
3. module 解析规则，各种 loader
4. 多线程打包 HappyPack、第三方的资源独立打包DLLPlugin、配合DllReferencePlugin

#### 开发环境

我们更注重的是如何及时快速的将更新的代码展示在网页上，如何快速定位错误。

* 使用 source map 
* 代码发生变化后自动编译代码，node 自动刷新 nodemon、supervisor

  * 简单的 web server 并具有实时重新加载， webpack-dev-server 内部使用了 webpack-dev-middleware
  
  * webpack-dev-middleware 可以作为一个单独的 package 使用
  
  * 观察模式 watch mode
* 模块热替换 
  * 内置插件 [HotModuleReplacementPlugin](https://webpack.docschina.org/plugins/hot-module-replacement-plugin/)
  * [React Hot Loader](https://github.com/gaearon/react-hot-loader)

* friendly-errors-webpack-plugin 错误提示

#### 上线环境

我们注重的是如何让代码包更小、构建更迅速，代码压缩、Tree Shaking等等

1. 压缩：

	- 默认使用 TerserWebpackPlugin
	
2. [代码分离](https://webpack.docschina.org/guides/code-splitting)：
* 入口起点 entry points（手动分离）
  * SplitChunksPlugin 移除重复模块，利用 [cacheGroups](https://webpack.docschina.org/plugins/split-chunks-plugin/#splitchunks-cachegroups) 选项配置ChunkId
  * 动态导入 dynamic imports（动态分离）
    * import() 语法，*内部用到* promises
    * require.ensure
  * 预取/预加载模块
    * prefetch **将来**某些导航下可能需要的资源
    * preload **当前**导航下可能需要资源
  * bundle 分析
  
3. 懒加载：
	
	* React: [Code Splitting and Lazy Loading](https://reacttraining.com/react-router/web/guides/code-splitting)
	
4. 上线缓存：利用 client 的长效缓存机制，命中缓存来消除请求，主要是 moduleId、chunkId

   * 输出文件的文件名 output.filename
   
     * contenthash 将根据资源内容创建出唯一 hash
   * 提取引导模板（webpack 运行时的引导代码）特别是 [runtime](https://webpack.docschina.org/concepts/manifest/) 和 manifest。
   
     *  optimization.splitChunks 将 runtime 代码拆分为一个单独的 chunk。
   * 模块标识符 moduleId
     * 推荐使用 [HashedModuleIdsPlugin](https://webpack.docschina.org/plugins/hashed-module-ids-plugin)
     * NamedModulesPlugin，将使用模块的路径，其执行时间会有些长

5. PWA：基于 [Service Workers](https://developers.google.com/web/fundamentals/primers/service-workers/) 实现的 web 技术

   * [Workbox](https://github.com/GoogleChrome/workbox) workbox-webpack-plugin
* 注册 Service Worker

#### 构建性能优化

优化代码质量比构建性能更重要。

1. 通用

  - 减少不必要的 loader，include 实际需要转换的路径
  - 使用DllPlugin编译不频繁变更的代码，第三方库
  - 持久化缓存
  
2. 开发

   * 使用 webpack 的 watch mode(监听模式)

   * 在内存中（而不是写入磁盘）编译

     * webpack-dev-server
     * webpack-hot-middleware
     * webpack-dev-middleware

   * devtool 设置

   * 避免使用生产环境的工具，如 minify 压缩
   
   * 编译结果不要携带文件路径信息 options.output.pathinfo
   
   * ts-loader 开启 transpileOnly

3. 生产

  * 多个 compilation(编译时)
    * [`parallel-webpack`](https://github.com/trivago/parallel-webpack)：它允许在 worker 池中运行 compilation。
    * `cache-loader`：可以在多个 compilation 之间共享缓存。
  * 是否真的需要 source map

