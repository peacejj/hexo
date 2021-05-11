---
title: css3动画
date: 2021-01-11 15:04:21
excerpt: transition、animation、transform的区别
tags: css
categories: 基础知识
position:
image_url:
---

## 一、transition （动画过渡）：一定时间之内，一组css属性变换到另一组属性的动画展示过程。
###### 属性是一个简写属性，用于设置四个过渡属性：  
- transition-property  - css属性
- transition-duration   - 动画执行时长 如果为0 动画不执行
- transition-timing-function    - 动画执行方式 默认ease
- transition-delay   - 动画延迟执行时间 默认0

一般来说，将transition属性应用到最初的样式里，而不是放在结束的样式里，即定义动画开始之前的元素外观的样式。

只需要给元素设置一次transition，浏览器就会负责以动画展示从一个样式到另一个样式，再返回最初样式的变化过程。它的特性：

1. 需要一个事件来触发，比如hover，所以没法在网页加载时自动触发。
2. 是一次性的，不能重复发生，除非一再触发。
3. 只能定义开始状态和结束状态，不能定义中间状态，也就是说只有两个状态。
4. 一条transition规则，只能定义一个属性的变化，不能涉及多个属性。

为了突破这些限制，animation出现了。

## 二、animation （动画属性）：animation可以通过keyframes显式控制当前帧的属性值
###### 属性是一个简写属性，用于设置六个动画属性：
- animation-name  规定需要绑定到选择器的 keyframe 名称。
- animation-duration
- animation-timing-function
- animation-delay  
- animation-iteration-count   - 规定动画应该播放的次数。  n | infinite
- animation-direction   - 规定是否应该轮流反向播放动画。 normal | alternate;

一般来说，会结合animation-play-state使用

#### 规定动画正在运行还是暂停

```
animation-play-state: paused|running;
```

## 三、transform：
###### 向元素应用 2D 或 3D 转换。该属性允许我们对元素进行旋转、缩放、移动或倾斜。

```
transform: none|transform-functions;

transform-functions:
    matrix():定义矩阵变换。
    translate():移动元素对象。
    scale():缩放元素对象。
    rotate():旋转元素对象。
    skew():倾斜元素对象。


translate(a, b)：是transform中的一个属性，用官方的话说叫做2D转移，其实就是平面上的x轴和y轴移动，具体使用：
transform: translate(x,y)
transform: translateX(x)
transform: translateY(y)
```

## 四、渐变：

```
linear-gradient(方向，颜色1，颜色2，...)

实例：
background: linear-gradient(90deg,red 0px,green 25%,blue 50%, purple 75%);

background: linear-gradient(red,green,blue);

background: linear-gradient(red 0px,green 25%,blue 50%, purple 75%);
```

