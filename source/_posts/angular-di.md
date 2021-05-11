---
title: angular-依赖注入
date: 2020-02-04 17:17:30
excerpt: 依赖注入
tags: angular
categories: 原理知识
position:
image_url:
---
>在JavaScript语境下，依赖注入就是函数参数是函数而已（高阶函数，因为JS没有类），而在TypeScript语境下，依赖注入就是在类的构造函数中传入另一个类，类实例化时包括其参数的类也会被实例化。

依赖注入就是控制反转概念的一种实现模式。
之前是我们需要什么服务，就去主动new一个对象，那依赖注入就是服务先注册到DI框架中，我去声明我需要什么服务，DI框架再把我需要的服务注入进来。如下图：

![image](/images/angular/DI.png)

##### 依赖注入作用
&ensp; &ensp; 让框架帮你处理重要对象的生命周期的管理，不需要你显式地进行管理(对象构造和销毁)


##### 依赖注入包含三部分：
1. 提供商：负责把一个令牌（可能是字符串也可能是类）映射到一个依赖的列表，它告诉angular该如何根据指定的令牌创建对象。
2. 注入器：解析依赖并注入他们。angular不需要我们创建注入器，在启动时，它会自己创建一个应用注入器—platformBrowserDynamic().bootstrapModule(AppModule)
3. 依赖：被用于注入的对象，例如服务等等

##### 详解
1、提供商
- 类提供商 useClass
- 别名提供商 useExisting
- 值提供商 useValue
- 工厂提供商 useFactory

2、令牌：
- 类
- 类-接口
- InjectionToken

3、注册提供商：可在模块中或组件中
4、服务：在服务中@Injectable() 是必写的。**angular6之后，我们通过使用 @Injectable 装饰器的新增的 provideIn 属性来使用它。**
