#### 环境搭建

1. 通过 Composer 安装，这里可以参考[中文教程](https://www.yiichina.com/doc/guide/2.0/start-installation)
2. 安装完成，需要注意的是 `php yii serve`要在项目根目录运行，教程的项目 web 目录有些坑。
3. 需要本地数据库，这里我们安装集成开发环境xampp，启动数据库，方便接下来数据库连接。

```js
├── README.md
├── assets // 用于存放前端资源包PHP类，用于管理CSS、js等前端资源
├── commands // 控制台应用的命令类，前端不需要这个
├── composer.json // 类似package.json
├── config // 配置文件
├── controllers  // 控制层，控制器类
├── mail // 应用的前后台和命令行的与邮件相关的布局文件等。
├── models //数据层，数据模型类
├── runtime // 运行时的临时文件目录（缓存文件和日志文件）
├── tests // 用于存放测试类。
├── vendor // 存放 Yii 的源码和通过 composer 安装的项目依赖库
├── views // 视图层，视图文件
├── web // Web服务器可以访问的目录，域名指向的目录，包含入口文件，可存放css、js、img、font等静态资源
├── widgets // 用于存放一些常用的小挂件的类文件。
```

4. 