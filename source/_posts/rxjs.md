---
title: rxjs
date: 2020-04-10 16:44:52
excerpt: rxjs
tags: rxjs
categories: 基础知识
position:
image_url:
---

Rxjs：异步编程。angular已经集成了。

###### 目前常见的异步编程的几种方法：
1. 回调函数
1. 事件监听/发布订阅
1. Promise
1. Rxjs

![image](/images/rxjs/clipboard3.png)

##### 前提：Promise 的缺点
Promise 固然是一个重大的进步，但在有些场景下仍然是不够的。比如，Promise 的特点是无论有没有人关心它的执行结果，它都会立即开始执行，并且你没有机会取消这次执行。显然，在某些情况下这么做是浪费的甚至错误的。仍然以电商为例，如果某商户的订单不允许取消，你还会去买吗？再举个编程领域的例子：如果你发起了一个 Ajax 请求，然后用户导航到了另一个路由，显然，你这个请求如果还没有完成就应该被取消，而不应该发出去。但是使用 Promise，你做不到，不是因为实现方面的原因，而是因为它在概念层（接口定义上）就无法支持取消。

此外，由于 Promise 只会承载一个值，因此当我们要处理的是一个集合的时候就比较困难了。比如对于一个随机数列（总数未知），如果我们要借助 Web API 检查每个数字的有效性，然后对前一百个有效数字进行求和，那么用 Promise 写就比较麻烦了。
我们需要一个更高级的 Promise。

##### 一、Observable
它就是可观察对象（Observable [əbˈzɜrvəbl]），Observable 顾名思义就是可以被别人观察的对象，当它变化时，观察者就可以得到通知。换句话说，它负责生产数据，别人可以消费它生产的数据。

如果你是个资深后端，那么可能还记得 MessageQueue 的工作模式，它们很像。如果不懂 MQ 也没关系，我还是用日常知识给你打个比方。

Observable 就像个传送带。这个传送带不断运行，围绕这个传送带建立了一条生产线，包括一系列工序，不同的工序承担单一而确定的职责。每个工位上有一个工人。

整个传送带的起点是原料箱，原料箱中的原料不断被放到传送带上。工人只需要待在自己的工位上，对面前的原料进行加工，然后放回传送带上或放到另一条传送带上即可，简单、高效、无意外 —— 符合程序员的审美。

而且这个生产线还非常先进 —— 不接单就不生产，非常有效地杜绝了浪费。
##### 二、FRP
这种设计，看上去很美，对吧？但光看着漂亮可不行，在编程时要怎么实现呢？实际上，这是一种编程范式，叫做函数响应式编程（FRP）。它比 Promise 可年轻多了，直到 1997 年才被人提出来。

顾名思义，FRP 同时具有函数式编程和响应式编程的特点。响应式编程是什么呢？形象的说，它的工作模式就是“饭来张口，衣来伸手”，也就是说，等待外界的输入，并做出响应。流水线每个工位上的工人正是这种工作模式。

工业上，流水线是人类管理经验的结晶，它所做的事情是什么呢？本质上就是把每个处理都局部化，以减小复杂度（降低对工人素质的要求）。而这，正是软件行业所求之不得的。响应式，就是编程领域的流水线。

那么函数式呢？函数式最显著的特征就是没有副作用，而这恰好是对流水线上每个工序的要求。显然，如果某个工序的操作会导致整个生产线平移 10 米，那么用不了多久这个生产线就要掉到海里了，这样的生产线毫无价值。
因此，响应式和函数式几乎是注定要在一起的。
##### 三、RxJS
**RXJS是Observable的Javascript实现**

RxJS 就是 ReactiveX 在 JavaScript 语言上的实现。对于 JavaScript 程序员来说，不管你是前端还是 NodeJS 后端，RxJS 都会令你受益。
由于 JavaScript 本身的缺陷，RxJS 不得不采用了很多怪异的写法。它对于 Java / C# 等背景的程序员来说可能会显得比较怪异，不过，你可以先忽略它们，聚焦在编程范式和接下来要讲的操作符语义上。

```
典型的写法
of(1,2,3).pipe(
  filter(item=>item % 2 === 1),
  map(item=>item * 3),
).subscribe(item=> console.log(item))
它会输出：
3
9
```

其中 of 称为创建器（creator），用来创建流，它返回一个 Observable 类型的对象，filter 和 map 称为操作符（operator），用来对条目进行处理。这些操作符被当作 Observable 对象的 pipe 方法的参数传进去。诚然，这个写法略显怪异，不过这主要是被 js 的设计缺陷所迫，它已经是目前 js 体系下多种解决方案中相对好看的一种了。
Observable 对象的 subscribe 方法表示消费者要订阅这个流，当流中出现数据时，传给 subscribe 方法的回调函数就会被调用，并且把这个数据传进去。这个回调函数可能被调用很多次，取决于这个流中有多少条数据。

**注意，Observable 必须被 subscribe 之后才会开始生产数据。如果没人 subscribe 它，那就什么都不会做。**


##### 四、rxjs和promise的区别
![image](/images/rxjs/clipboard.png)

promise相较于Rxjs而言功能更单一 promise 只能将一个数据的状态由pending转换成resoloved或者rejected.而Rxjs可以处理多个数据对应complete和error状态但是Rxjs同时又拥有next方法。Observable是惰性的，需要subscribe的时候才输出值。promise内部状态是不可控制的，执行了就无法终止。而Observable可以定义如何取消异步方法。这也就是我下面会讨论到的一个异步场景。

##### 五、详解
###### 在 RxJS 中用来解决异步事件管理的的基本概念是：
1. Observable (可观察对象): 表示一个概念，这个概念是一个可调用的未来值或事件的集合。
1. Observer (观察者): 一个回调函数的集合，它知道如何去监听由 Observable 提供的值。
1. Subscription (订阅): 表示 Observable 的执行，主要用于取消 Observable 的执行。
1. Operators (操作符): 采用函数式编程风格的纯函数 (pure function)，使用像 map、filter、concat、flatMap 等这样的操作符来处理集合。
1. Subject (主体): 相当于 EventEmitter，并且是将值或事件多路推送给多个 Observer 的唯一方式。
1. Schedulers (调度器): 用来控制并发并且是中央集权的调度员，允许我们在发生计算时进行协调，例如 setTimeout 或 requestAnimationFrame 或其他。

![image](/images/rxjs/clipboard2.png)

Observable 是同步的。

函数和 Observables 都是惰性运算。如果你不调用函数，console.log('Hello') 就不会执行。Observables 也是如此，如果你不“调用”它(使用 subscribe)，console.log('Hello') 也不会执行。此外，“调用”或“订阅”是独立的操作：两个函数调用会触发两个单独的副作用，两个 Observable 订阅同样也是触发两个单独的副作用。EventEmitters 共享副作用并且无论是否存在订阅者都会尽早执行，Observables 与之相反，不会共享副作用并且是延迟执行。
Observable 可以随着时间的推移“返回”多个值，这是函数所做不到的。Observables 传递值可以是同步的，也可以是异步的。

Observable 的核心关注点：
- 创建 Observables
- 订阅 Observables
- 执行 Observables
- 清理 Observables

