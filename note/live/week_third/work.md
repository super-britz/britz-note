#### gulp 流清洗 和 webpack 动态插入

1. 创建 gulpfile.js 文件，安装 gulp、gulp-babel、gulp-watch、babel-plugin-transform-es2015-modules-commonjs
2. 创建开发环境配置，先用 watch 配置入口文件，babel 忽略掉外层 babelrc, 使用插件 babel-plugin-transform-es2015-modules-commonjs 将 es6 import 转换成 require
3. 安装 scripty
4. 写 shell 脚本，也可以写 shell.js

```json
    "server:dev": "scripty",
    "server:prod": "scripty",
    "server:lint": "scripty"
```

5. 配置上线环境，跟开发环境差不多，需要 ignore 要清洗的 config 配置文件。
6. 安装 gulp-rollup，rollup-plugin-replace
7. 配置清洗环境，rollup (tree shaking) 移除 JavaScript 上下文中的未引用代码 (dead-code)，插件 replace 删除不要的代码
8. 安装 gulp-eslint，创建文件。eslintrc，配置团队的规则
9. 处理真假路由的插件`koa2-connect-history-api-fallback`
10. 安装 webpack、webpack-cli 创建 webpack.config.js

```json
    "client:dev": "webpack --mode development",
    "client:prod": "webpack --mode production",
```
11. 使用 yargs-parser 解析命令行参数，拿到 mode，把开发环境和上线环境分开
12. 安装 glob, 查找文件类似 fs, 找到 entry 文件
13. 通过正则匹配拿到 entry 文件的 dist 和 template
14. 安装 html-webpack-plugin，注意配置 inject 为 false
15. 安装 webpack-merge 合并当前webpack.config和mode环境的配置
16. 配置 webpack.development.js,安装 copy-webpack-plugin，文件或文件夹复制到 dist
17. 根据 html-webpack-plugin 插件，将自己的插件挂在到 html-webpack-plugin 的钩子上，通过replace 属性将css和js 动态插入到指定位置
18. 安装 mini-css-extract-plugin、css-loader，提取css

