## 前端性能优化

目标：

- 提升用户体验
- 提升执行效率
- 减少服务器压力

限制：

- 安全性： cdn 挂了
- 一致性： 缓存数据一致
- 可维护性： 汇编，机器码性能好
- 成本

#### web 场景下的浏览器内部和 http 层性能优化

js 引擎和 dom 渲染引擎，单线程保证一致性

- js 执行效率，v8 及时编译
- 渲染引擎效率 repoint reflow
- http 请求

pc 端：

- 浏览器差异
- 网速相对快
- 处理器性能较好

mobile:

- 处理性能有限，省电
- 网速慢
- 流量成本

#### Google AMP

场景：手机的资讯页面（非 SPA) 

- 机器性能
- 浏览器性能
- 网速受限
- 非刚性需求

权衡

- 减少请求
- 减少体积
- 牺牲一部分 UE
- 牺牲一部分开发效率

图片占用网络请求，造成页面阻塞，不用默认 img，
图片大小未知，会触发 reflow，强制图片渲染前指定大小
css 必须在 head，一次初始化，避免多次 rerender
