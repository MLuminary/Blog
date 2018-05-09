# FlexDisplayRoom

[项目展示地址](http://www.haoqinzz.cn/FlexDisplayRoom/)

前段时间面试发现有很多都问 flex 布局，就觉得灰常有必要去学习一下，浏览了一遍[阮一峰大神写的教程](http://www.ruanyifeng.com/blog/2015/07/flex-grammar.html?^%$)后掌握了基本理论，但始终觉得应该实践一下，偶然看到了这样一个网站

https://demos.scotch.io/visual-guide-to-css3-flexbox-flexbox-playground/demos/ 

**应该要翻墙**才能进去，这个网页在手机端需要不断的上下滑动，个人感觉有必要修改一下，就萌生了也做一个类似的想法，当然自己手机端做的也不是那么好，但是在做的过程中也学到了一些适应手机端的一些知识

[项目展示地址](http://www.haoqinzz.cn/FlexDisplayRoom/)

[项目源码地址](https://github.com/MLuminary/subentry/tree/master/FlexDisplayRoom)

## Flex 布局的基础知识

采用 flex 布局的元素，称为 Flex 容器，它的所有子元素自动成为容器成员，成为 Flex 项目

### 容器的属性

以下属性第一个取值为默认值

**flex-direction**

- row : 主轴为水平方向，起点在左端
- row-reverse : 主轴为水平方向，起点在右侧
- column : 主轴为垂直方向，起点在上沿
- column-reverse : 主轴为垂直方向，起点在下沿

**flex-wrap**

- nowrap : 不换行
- wrap : 换行，第一行在上方
- wrap-reverse: 换行，第一行在下方

**flex-flow**

`flex-flow` 是 `flex-direction` 和 `flex-wrap` 的简写形式，默认值为 `row nowrap`

**justify-content**

- flex-start :  左对齐
- flex-end : 右对齐
- center : 居中
- space-between : 两端对齐，项目之间间隔都相等
- space-around : 每个项目两侧的间隔相等

**align-items**

- stretch : 如果项目未设置高度或设为 auto ，将占满整个容器的高度
- flex-start : 交叉轴的起点对齐
- flex-end : 交叉轴的终点对齐
- center : 交叉轴的中点对齐
- baseline : 项目的第一行文字的基线对齐

**align-content**

- stretch : 轴线占满整个交叉轴
- flex-start : 与交叉轴的起点对齐。
- flex-end : 与交叉轴的终点对齐。
- center : 与交叉轴的中点对齐。
- space-between : 与交叉轴两端对齐，轴线之间的间隔平均分布。
- space-around : 每根轴线两侧的间隔都相等。所以，轴线之间的间隔比轴线与边框的间隔大一倍。

### 项目的属性

**order**

定义项目的排列顺序，属性值越小，排列越靠前，默认为 0

**flex-grow**

定义项目的放大比例，默认为 0 ，即如果存在剩余空间也不放大

**flex-shrink**

定义项目的缩小比例，默认为 1 ，即如果空间不足，项目将自动缩小，负值对该属性无效

**flex-basis**

定义了在分配多余空间之前，项目占据的主轴空间。默认值为 `auto` 即项目本来的大小

**flex**

`flex` 是 `flex-grow`、`flex-shrink` 和 `flex-basis` 的简写，默认值为 `0 1 auto` 

该属性有两个快捷值 : `auto`(`1 1 auto`) 和 `none`(`0 0 auto`)

**align-self**

`align-self` 允许单个项目与其它项目有不一样的对其方式，可覆盖 `align-items`。默认值为 `auto`，表示继承父元素的 `align-items` 属性
