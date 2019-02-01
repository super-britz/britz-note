## nginx 的反向代理与负载均衡

- 什么是反向代理与负载均衡
- nginx 负载均衡的实现
- http upstream 模块
- 其他负载均衡的方法

[正向代理与反向代理的区别](https://www.jianshu.com/p/208c02c9dd1d)

[什么是负载均衡 - 知乎](https://zhuanlan.zhihu.com/p/32841479)

[什么是负载均衡 - 阿里云](https://www.alibabacloud.com/help/zh/doc-detail/27539.htm)

负载均衡：通常用于将工作负载分布到服务器集群来分担服务器压力

#### http upstream 模块

[Upstream 模块](https://www.jianshu.com/p/71b3701dd35e) 是 Nginx 服务器的一个重要模块。Upstream 模块实现在轮询和客户端 ip 之间实现后端的负载均衡。（默认轮询，可以设置权重）  

- ip_hash 指令
- server 指令
- upstream 指令

#### 部署 NodeJS 上线步骤

1. 安装 [homebrew](https://brew.sh/index_zh-cn)
2. 查找 brew search nginx , 安装 brew install nginx
3. brew info nginx
4. 查看 nginx 信息 nginx -v
5. 启动 nginx, 默认 8080 端口 `nginx`

    - 假如 xampp 的 apache 开启，nginx 启动会失败
6. nginx -s reload , nginx -s stop
7. nginx -t 查看配置文件 `/usr/local/etc/nginx/nginx.conf` 
8. nginx -c xx 设置自己的配置文件


#### node.js上线部署

ssh

pm2 

shelljs


#### 腾讯地图H5 nodejs架构搭建

Varnish 缓存代理

Stupid 文件服务器

#### JavaWeb .Net

yog2

#### 前后端分离

redis

cookie

#### Hapi

适合专门做接口的服务

localhost cookie解析错误，需要删除cookie