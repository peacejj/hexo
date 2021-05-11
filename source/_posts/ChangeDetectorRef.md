---
title: angular的ChangeDetectorRef
date: 2020-05-10 16:58:07
excerpt: angular的ChangeDetectorRef
tags: angular
categories: 原理知识
position:
image_url:
---

### Angular有两种变化检测策略(Change Detection Strategy)
```
enum ChangeDetectionStrategy {
  // 使用 CheckOnce 策略，这意味着自动更改检测将停用，
  // 直到通过将策略设置为"Default"（总是检查）重新激活。
  OnPush: 0 

  // 使用默认的 CheckAlway 策略，其中更改检测是自动的，直到显式停用
  Default: 1
}

当使用 OnPush 策略的时候，只有在以下任一情况下，Angular才会检测此组件
1.组件的任一@Input属性被父组件设为新值，如果@Input是引用类型，则需被设为新的实例才会触发变化检测。
2.事件发生。无论此事件是否和业务有关，甚至是个空事件，都可。
```

##### 使用
```
1、引入ChangeDetectorRef模块
import { ChangeDetectorRef } from “angular”;
2、声明
@Component({
  changeDetection: ChangeDetectionStrategy.OnPush,
})
3、引用
constructor(private cd:ChangeDetectorRef) {
}
4、检测
this.cd.detectChanges();实时检测页面及其子元素的变化
```

##### ChangeDetectorRef 是组件的变化检测器的引用
**当使用 OnPush 策略的时候，若输入属性没有发生变化，组件的变化检测将会被跳过**
```
onpush策略：
如果组件众多，组件中又包含组件，那每次这种不加区分的变化检测对页面的性能却有一定的消耗，而针对这种问题优化就有了onPush的变化检测策略。

export abstract class ChangeDetectorRef {
  abstract markForCheck(): void //当组件使用OnPush变更检测策略时，把该组件显式标记为已更改，以便它再次进行检查。
  abstract detach(): void //从变更检测树中分离开组件。 已分离的组件在重新附加上去之前不会被检查。 与 detectChanges() 结合使用，可以实现局部变更检测。
  abstract reattach(): void //把以前分离开的组件重新附加到变更检测树上。 组件会被默认附加到这棵树上。
  abstract detectChanges(): void //检查该组件及其子组件。与 detach 结合使用可以实现局部变更检测。
  abstract checkNoChanges(): void //检查变更检测器及其子检测器，如果检测到任何更改，则抛出异常。
}
```

- markForCheck() - 当输入已更改或视图中发生了事件时，组件通常会标记为脏的（需要重新渲染）。调用此方法会确保即使那些触发器没有被触发，也仍然检查该组件。在组件的 metadata 中如果设置了 changeDetection: ChangeDetectionStrategy.OnPush 条件，那么变化检测不会再次执行，除非手动调用该方法。
- detach() - 从变化检测树中分离变化检测器，该组件的变化检测器将不再执行变化检测，除非手动调用 reattach() 方法。
- reattach() - 重新添加已分离的变化检测器，使得该组件及其子组件都能执行变化检测
- detectChanges() - 从该组件到各个子组件执行一次变化检测 检查该视图及其子视图。与 <a href="https://angular.cn/api/core/ChangeDetectorRef#detach">detach</a> 结合使用可以实现局部变更检测。

>脏数据：就是用户改变了数据，就为脏数据。
>在submit时，一定要标记所有的control为脏数据，且更新值和有效性校验。原因是，初始化进入可能不改动数据直接保存，这时会不满足校验规则，但不会触发校验。
