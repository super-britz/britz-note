#### 渲染

网页动画能做到每秒 60 帧，就会跟显示器同步刷新，一秒60次重新渲染，每次渲染时间不能超过16.66毫秒，但因为浏览器需要花费时间将新帧绘制到屏幕上，只有 10 毫秒来执行代码。
requestAnimationFrame，
requestIdleCallback, 下几次重新渲染时

#### 触发分层

1. 获取DOM并将其分割为多个层 z-index
2. 将每个层独立的绘制进位图中
3. 将层作为纹理上传到 CPU （玩游戏的显卡，专门绘制图形的）
4. 复合多个层来生成最终的屏幕图像

DOM 子树渲染层（RenderLayer）-> RenderObject -> GraphicsContext (根元素、position、transform、半透明、CSS滤镜、Canvas2D、video、溢出) 都会触发层

Compositor 渲染层子树图形层（GraphicsLayer）-> RenderLayer -> RenderObject (CSS3D透视转换、videl、webGL、transform动画、加速css滤镜、叠加在已经触发合成层)

Compositor将所有的拥有 compositing layer 进行合成，合成过程 CPU 进行参与，合成完毕将纹理映射到一个网格的几何结构上。
chrome 采用纹理把页面中的内容分块发送给GPU。纹理能够以很低的代价映射到不同的位置，而且还能够以很低的代价通过把他们应用到一个非常简单的钜形中进行变形，这就是3D CSS的实现原理。

#### 重绘重排

网页生成的时候，至少会渲染一次。用户访问过程中还会不断重新渲染。以下三种情况。会导致网页重新渲染。

1. 修改 DOM
2. 修改样式表
3. 用户事件

重新渲染，就需要重新生成布局（重排 reflow）和重新绘制（重绘 repaint）, 重排一定会重绘，重绘不一定重排。

#### 如何减少重排

1. 样式表越简单，重排和重绘就越快，命名规范
2. 重排和重绘的DOM元素级别越高，成本就越高
3. table 元素的重排和重绘成本，要高于 div 元素
4. 尽量不要把读操作和写操作，放在一个语句里面
5. 统一修改样式
6. 缓存重排结果
7. 离线 DOM Fragment/clone
8. 虚拟 DOM react
9. 必要的时候 display:none 不可见元素不会影响重排重绘，visibility 对重排影响，不影响重绘

#### 硬件加速

texture： 即 CPU 传输到 GPU 的一个 Bitmap
GPU 能快速对 texture 进行偏移、缩放、旋转、修改透明度等操作。

[CPU（中央处理器）vs GPU（图形处理器](https://www.zhihu.com/question/19903344)

layer 模型

- 浏览器根据 css 属性为元素生成 layers
- 将 Layers 作为 texture 上传到 GPU
- 当改变 Layer 的 transform，opacity 属性时，渲染跳过 Layout，paint，直接通知 GPU 对 layer 做变换

#### layer 创建标准，可以在 GPU 里完成

- 拥有 3d transform 属性
- 使用 animation,transition 实现 opacity,transform 的动画
- video
- canvas
- Flash
- 使用 css filters 的元素
- z-index 大于某个相邻节点的 layer 的元素

1. 初次对元素进行布局(layout)
2. 绘制(paint)元素到位图(bitmap)中
3. 同步位图到合成器
4. 上传位图到GPU内存中去
5. 从GPU内存将位图显示到屏幕上
6. 开始transform CSS转换
7. 告诉合成器怎么做动画
8. 绘制GPU内存中的位图到屏幕，多次绘制到transform结束

节省了哪些时间？

CPU进行Layout,Paint的时间
CPU想GPU传输位图的时间

