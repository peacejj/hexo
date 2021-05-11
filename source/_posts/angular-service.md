---
title: angular服务详解
date: 2020-08-04 17:09:56
excerpt: angular服务详解
tags: angular
categories: 原理知识
position:
image_url:
---
### 根据具体使用场景， providers: [] 将有三种不同的用法：
1. 在预加载的模块的@NgModule装饰器中指定 providers: []
2. 在懒加载的模块的@NgModule装饰器中指定 providers: [] 
3. 在@Component和@Directive装饰器中指定 providers: []



`angular6`之后，我们通过使用 `@Injectable` 装饰器的新增的 `provideIn` 属性来使用它。
我们可以将`provideIn`视为以反向方式指定依赖关系。 现在不是模块申明需要哪些服务，而是服务本身宣布它应该提供给哪些模块使用
在 `providedIn` 出现之前，需要在主模块的 `providers: []` 中注入所有公共服务。然后，组件需要导入该模块，这将导致所有（可能的大量）的服务导入进该组件，即使我们只想使用其中一个服务。

------------------
###### 服务注入过程图解：
![imgage](/images/angular/service.png)
