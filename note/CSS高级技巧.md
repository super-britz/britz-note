#### css3高级使用技巧
- 早期的圣杯布局、双飞翼布局 + css hack
- 基于移动端的 px 和 rem 的转化方案
- 弹性盒模型和 Reset 选择
- css 代码规范 和 团队项目规范
- css 绘制特殊图形 高级技巧
- BFC、IFC、GFC 和 FFC

##### 1. 圣杯布局和双飞翼布局(左右定宽，中间自适应) 

圣杯布局是 middle+padding，双飞翼采用子元素+margin主要都是负边距的运用。
优点：核心DOM优先渲染  
缺点：实现垂直居中很麻烦

这里有几点需要留意：
1. 先写 middle，然后是 left 和 right，因为需要先渲染 middle（html从上往下渲染）
2. left、right需设置position:relative以及相应的left、right值。
3. 理解负边距的作用，left的margin-left:-100%使它上移一行，同时right向左移占据left原先位置，同理，right的margin-left:-100px使它上移并靠右。
4. 实现假的等高，最高一列为基准，overflow:hidden; 正padding-bottom 负margin-bottom。

##### 2. 基于移动端的 px 和 rem 的转化方案

字体不建议使用rem, data-dpr属性动态设置字体，屏幕变大放更多的文字。

##### 3. flex 弹性盒模型

reset.css 重置  
normalize.css 修复  
neat.css 融合  

##### 4. 布局 BFC、IFC、GFC 和 FFC

###### BFC(Block Formatting Contexts)直译为"块级格式化上下文"  
[Block Formatting Contexts](https://juejin.im/post/5909db2fda2f60005d2093db)就是页面上的一个隔离的渲染区域，容器里面的子元素不会在布局上影响到外面的元素，反之也是如此。  
哪些元素会生成BFC？
1. 根元素
2. float的值不为none。 
3. position的值为absolute和fixed。
4. display的值为inline-block, table-cell, table-caption, flex, inline-flex中的任何一个。 
5. overflow不为visible。 

根据BFC的布局规则：
1. 内部的Box会在垂直方向，一个接一个地放置
2. Box垂直方向的距离由margin决定。属于同一个BFC的两个相邻Box的margin会发生重叠
3. 每个元素的margin box的左边， 与包含块border box的左边相接触(对于从左往右的格式化，否则相反)。即使存在浮动也是如此
4. BFC的区域不会与float box重叠。
5. BFC就是页面上的一个隔离的独立容器，容器里面的子元素不会影响到外面的元素。反之也如此。
6. 计算BFC的高度时，浮动元素也参与计算

> `block-level box` 从里到外 content box、padding box、border box、margin box

BFC的作用
1. 分属于不同的BFC时可以阻止margin重叠 （根据规则2）
2. 可以包含浮动元素——清除内部浮动 （根据规则6）
给父div加上 overflow: hidden  
清除浮动原理：触发父div的BFC属性，使下面的子div都处在父div的同一个BFC区域之内，此时已成功清除浮动。
3. 自适应两栏布局 （根据规则4）`圣杯和双飞翼布局？`

###### IFC(Inline Formatting Contexts)直译为"内联格式化上下文"
IFC的line box（线框）高度由其包含行内元素中最高的实际高度计算而来（不受到竖直方向的padding/margin影响)  
IFC作用
1. 水平居中 `text-align:center;`
2. 垂直居中 `vertical-align:middle;`
###### GFC(GridLayout Formatting Contexts)直译为"网格布局格式化上下文"
什么是GFC? `display:grid;`  
GFC作用  
1. 相比table有更加丰富的属性来控制行列，控制对齐以及更为精细的渲染语义和控制。
###### FFC(Flex Formatting Contexts)直译为"自适应格式化上下文"
什么是FFC？自适应容器 `display:flex; display:inline-block;`  
设置为 flex 的容器被渲染为一个块级元素，而设置为 inline-flex 的容器则渲染为一个行内元素。
#### 5. CSS分层与面向对象
SMACSS
BEM
SUIT
ACSS
ITCSS