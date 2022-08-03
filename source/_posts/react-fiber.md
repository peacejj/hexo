---
title: React fiber 起因原理
date: 2022-07-20 15:38:18
excerpt: React fiber 起因原理
tags: React fiber
categories: 原理知识
position:
image_url:
---
# 背景：
![conv_ops](https://raw.githubusercontent.com/peacejj/static/220a3ef7bdfca90c1e2d911ec63485da3a6cf5c9/images/react/20210330131423953.gif)


当浏览器每秒刷新的次数低于60hz人眼就会感知卡顿掉帧等情况。

对于浏览器来说，在两次屏幕硬件刷新之间，浏览器正好进行一次刷新（重绘），网页也会很流畅，当然这种是理想模式。如果两次硬件刷新之间浏览器重绘多次是没意义的，只会消耗资源；如果浏览器重绘一次的时间是硬件多次刷新的时间，那么人眼将感知卡顿掉帧等， 所以浏览器对一次重绘的渲染工作需要在16ms（1000ms/60）之内完成，也就是说每一次重绘小于16ms才不会卡顿掉帧。

实际上，对用户来说，不良的体验不只是视觉上表现为卡顿与掉帧，因为在浏览器中，JS 运算、页面布局和页面绘制都是运行在浏览器的主线程当中，他们之间是互斥的关系。通常这时，对于用户在输入框输入内容这个行为来说，就体现为按下了键盘按键但是页面上不实时显示输入。

React 的理念
我们认为，React 是用 JavaScript 构建快速响应的大型 Web 应用程序的首选方式。它在 Facebook 和 Instagram 上表现优秀。 ——官网



# React15结构：
我们认为，React 是用 JavaScript 构建快速响应的大型 Web 应用程序的首选方式。它在 Facebook 和 Instagram 上表现优秀。 ——官网

# React15结构：
![image](https://raw.githubusercontent.com/peacejj/static/220a3ef7bdfca90c1e2d911ec63485da3a6cf5c9/images/react/image2022-6-30_19-41-6.png)

jsx

```
const data = {
    item1: 'bb',
    item2: 'cc'
}

const jsx =  <ul className="list">
    <li className="item" style={{ background: 'blue', color: 'pink' }} onClick={() => alert(2)}>aa</li>
    <li className="item">{data.item1}<i>xxx</i></li>
    <li className="item">{data.item2}</li>
</ul>;
```
render funtion

```
const data = {
  item1: 'bb',
  item2: 'cc'
};
const jsx = Dong.createElement("ul", {
  className: "list"
}, Dong.createElement("li", {
  className: "item",
  style: {
    background: 'blue',
    color: 'pink'
  },
  onClick: () => alert(2)
}, "aa"), Dong.createElement("li", {
  className: "item"
}, data.item1, Dong.createElement("i", null, "xxx")), Dong.createElement("li", {
  className: "item"
}, data.item2));
```
vdom

```
{
  "type": "ul",
  "props": {
      "className": "list",
      "children": [
          {
              "type": "li",
              "props": {
                  "className": "item",
                  "children": [
                    "aa"
                  ]
              }
          },
          {
            "type": "li",
            "props": {
                "className": "item",
                "children": [
                  "bb"
                ]
            }
          }
        ]
   }
}
```
### React 15 的架构可以分为两层
#### 1、Reconciler 协调器：负责找出变化的组件
每当有更新发生时，Reconciler会做如下工作：

调用函数组件、或class组件的render方法，将返回的JSX转化为虚拟DOM
将虚拟DOM和上次更新时的虚拟DOM对比
通过对比找出本次更新中变化的虚拟DOM
通知Renderer将变化的虚拟DOM渲染到页面上
#### 2、Renderer（渲染器）：负责将变化的组件渲染到页面
由于React支持跨平台，所以不同平台有不同的Renderer。我们前端最熟悉的是负责在浏览器环境渲染的Renderer —— ReactDOM。

ReactDOM渲染器，浏览器环境渲染
ReactNative渲染器，渲染App原生组件
ReactTest渲染器，渲染出纯Js对象用于测试
ReactArt渲染器，渲染到Canvas, SVG 或 VML (IE8)

**在每次更新发生时，Renderer接到Reconciler通知，将变化的组件渲染在当前宿主环境。**

在每16.6ms时间内，需要完成如下工作：JS脚本执行 ----- 样式布局 ----- 样式绘制

对于React的更新来说，递归遍历应用的所有节点由于递归执行，计算出差异，然后再更新 UI。递归是不能被打断的，所以更新一旦开始，中途就无法中断。当层级很深时，递归更新时间超过了16ms，由于JS线程和GUI线程是互斥的，所以可能会看到UI的卡顿。

另一方面，递归的方式进行渲染，使用的是 JS 引擎自身的函数调用栈，当层级很多的，可能会出现爆栈（stack overflow）的错误。当然这是递归的另一个缺点，但并不是React要优化的主要原因。

### React 16的设计思想
React16架构可以分为三层：
- Scheduler（调度器）—— 调度任务的优先级，高优任务优先进入Reconciler
- Reconciler（协调器）—— 负责找出变化的组件
- Renderer（渲染器）—— 负责将变化的组件渲染到页面上

相比React15，16增加了一个Scheduler（调度器），我们来了解一下。

#### Scheduler（调度器）
通过上面基础知识已经了解，当JS执行时间过长，带给用户的体验就是所谓的“卡顿”。那么我们要如何解决这个问题呢？

答案是：在浏览器每一帧的时间中，预留一些时间给JS线程，React利用这部分时间更新组件（可以看到，在源码中，预留的初始时间是5ms）。

当预留的时间不够用时，React将线程控制权交还给浏览器使其有时间渲染UI，React则等待下一帧时间到来，继续被中断的工作。

既然我们以浏览器是否有剩余时间作为任务中断的标准，那么我们需要一种机制，当浏览器有剩余时间时通知我们。所以React就实现了一个Scheduler（调度器），除了在空闲时触发回调的功能外，Scheduler还提供了多种调度优先级供任务设置。

#### Reconciler（协调器）
从React15到React16，协调器（Reconciler）重构的一大目的是：将老的同步更新的架构变为异步可中断更新。

异步可中断更新可以理解为：更新在执行过程中可能会被打断，可以继续执行时恢复之前执行的中间状态。

有其他更高优先级任务需要先更新：屏幕外元素的渲染和更新任务的优先级应该小于响应用户输入任务。若现在进行屏幕外组件状态更新，用户又在输入，浏览器就应该先执行响应用户输入任务。
当前帧没有剩余时间
#### 问题：同时，我们需要注意，16中的更新是可中断的，那React如何解决要是中断了，DOM渲染不完全的问题呢？
在React16中，Reconciler与Renderer不再是交替工作，就是说，不是一协调完一个就立刻通知Renderer去渲染【React15架构的Reconciler和Renderer是交替工作的】。而是当Scheduler将任务交给Reconciler后，Reconciler会为变化的虚拟DOM打上代表增/删/更新的标记，类似这样：


```
export const Placement = /*             */ 0b0000000000010;
export const Update = /*                */ 0b0000000000100;
export const PlacementAndUpdate = /*    */ 0b0000000000110;
export const Deletion = /*              */ 0b0000000001000;
```

**==整个Scheduler与Reconciler的工作都在内存中进行，不会更新到DOM上面。【所以即使反复中断，用户也不会看见更新不完全的DOM】只有当所有组件都完成Reconciler的工作，才会统一交给Renderer。==**

Reconciler 内部采用了 ==**Fiber**== 的结构。


### Renderer（渲染器）
**Renderer**根据**Reconciler**为虚拟DOM打的标记，同步执行对应的DOM操作。

![image](https://raw.githubusercontent.com/peacejj/static/220a3ef7bdfca90c1e2d911ec63485da3a6cf5c9/images/react/image2022-6-30_19-41-7.jpeg)

### Fiber简介

Fiber在英文中的意思为“纤维化”，即细化，将任务进行细化。我们可以把一个耗时长的任务分成很多小片，每一个小片的运行时间很短，虽然总时间依然很长，但是在每个小片执行完之后，都给其他任务一个执行的机会，这样唯一的线程就不会被独占，其他任务依然有运行的机会。React中的Fiber就把整个更新过程碎片化

所以要实现Fiber架构，必须要解决两个问题:

- 保证任务在浏览器空闲的时候执行；
- 将任务进行碎片化；


### Fiber Reconciler 协调器 
生成 Fiber 树，得出需要更新的节点信息。这一步是一个渐进的过程，可以被打断。


### fiber数据格式
vdom：
![image](https://raw.githubusercontent.com/peacejj/static/220a3ef7bdfca90c1e2d911ec63485da3a6cf5c9/images/react/image2022-6-30_20-8-46.png)
fiber树：
![image](https://raw.githubusercontent.com/peacejj/static/220a3ef7bdfca90c1e2d911ec63485da3a6cf5c9/images/react/image2022-6-30_20-10-9.png)

本来 vdom 里通过 children 关联父子节点，而 fiber 里面则是通过 child 关联第一个子节点，然后通过 sibling 串联起下一个，所有的节点可以 return 到父节点。

这样不就把一颗 vdom 树，变成了 fiber 链表么？

然后渲染 fiber 就可以了，和渲染 vdom 的时候一样。

### fiber工作原理：
Fiber节点可以保存对应的DOM节点。相应的，Fiber节点构成的Fiber树就对应DOM树。那么如何更新DOM呢？这需要用到被称为“双缓存”的技术。



### 双缓存是什么

当我们用canvas绘制动画，每一帧绘制前都会调用ctx.clearRect清除上一帧的画面。

如果当前帧画面计算量比较大，导致清除上一帧画面到绘制当前帧画面之间有较长间隙，就会出现白屏。

为了解决这个问题，我们可以在内存中绘制当前帧动画，绘制完毕后直接用当前帧替换上一帧画面，由于省去了两帧替换间的计算时间，不会出现从白屏到出现画面的闪烁情况。

这种在内存中构建并直接替换的技术叫做双缓存。

React使用“双缓存”来完成Fiber树的构建与替换——对应着DOM树的创建与更新。

### 双缓存Fiber树
即在更新时，react存储两个fiber数据结构，如下图： 
![image](https://raw.githubusercontent.com/peacejj/static/220a3ef7bdfca90c1e2d911ec63485da3a6cf5c9/images/react/mark_3024_0_0_0.png)

上图中，rootFiberNode是应用挂在的节点，左右两侧分别是两棵树。current指向的fiber是渲染在页面中的fiber(即出现在屏幕中的视图)，我们称它未current fiber。左侧是正在内存中构建的Fiber树称为workInProgress Fiber。current fiber和workInProgress fiber通过alternate属性连接。

当workInProgress Fiber树构建完成交给Renderer渲染在页面上后，应用根节点的current指针指向workInProgress Fiber树，此时workInProgress Fiber树就变为current Fiber树。

每次状态更新都会产生新的workInProgress Fiber树，通过current与workInProgress的替换，完成DOM更新。



