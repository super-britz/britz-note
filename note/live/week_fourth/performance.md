#### 性能优化

一个存地址，一个存内容

basket.js

前端 ORM 存储方案 localhost

#### 缓存的优先级

1. cache-control
2. expires
3. etag/if-none-match
4. last-modified/if-modified-since

#### 重绘重排

gpu.js

#### 性能指标

- FP：仅有一个 div 根节点。 
- FCP：包含页面的基本框架，但没有数据内容。 
- FMP：包含页面所有元素及数据
- TTI：可交互时间

#### node

new weakmap() 的变量赋值为null可以立即回收

- 内存泄漏
- 压力测试
- 编码规范