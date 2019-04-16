## jenkins 安装 (MacOS)

1. 依赖 java 环境，首先下载 [JavaSDK（Java SE Development Kit）](https://www.oracle.com/technetwork/java/javase/downloads/jdk8-downloads-2133151.html)

2. [下载并运行jenkins](https://jenkins.io/zh/doc/pipeline/tour/getting-started/)

3. 拿到管理员安装密码 `sudo cat /Users/Shared/Jenkins/Home/secrets/initialAdminPassword`

4. 跳过插件安装，创建管理员账号密码（一定要记住）, 我这里账号密码都是`britz`，注意保存的时候不要用`Admin`运行，不然登陆账号就是`Admin`的

5. 使用创建的账号密码登录

6. 强制修改 Jenkins 语言为中文（一般是浏览器语言的设置）, 需要安装插件 Locale Plugin，安装成功之后
`配置[Manage Jenkins] > [Configure System] > [Locale] > [Default Language]`默认语言填 `zh_CN` 并勾选强制设置语言。

## 卸载jenkins

1. 从终端执行卸载脚本：`sh '/Library/Application Support/Jenkins/Uninstall.command'`

2. 删除相关配置：`sudo rm -rf /var/root/.jenkins ~/.jenkins`

## jenkins 项目配置

主要就是两部分：

1. Source Code Management 管理，项目从哪来的
2. Build 过程，执行 shell 脚本，构建可以发布的代码
3. 配置 ssh 服务器，传输的文件到哪个目录
4. 部署，在 ssh 服务器配置的目录下，是否需要执行脚本，停服务，起服务，PM2发布等