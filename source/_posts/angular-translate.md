---
title: angular国际化方案
date: 2020-05-09 17:30:05
excerpt: angular
tags:
position:
image_url:
---

Angular 国际化常见有 Angular 内置和基于 @ngx-translate/core （请参考官网了解更多实现细节）两种不同国际化方案。

### 两种方案
###### 1、Angular 内置（多个app）
Angular 文档（中文版）有完整的描述，注意需要为每一种语言构建和部署单独的应用程序版本。

###### 2、@ngx-translate/core（推荐，只用一套app）
@ngx-translate/core 是社区版本的 Angular 国际化，相比较 Angular 内置它是动态性，无须针对不同语言构建和部署单独版本，并且大部分情况下可以立即呈现。

---
### @ngx-translate/core使用
###### 1、html中
```
<a>{{ 'menu.fullscreen' | i18n}}</a>
```

###### 2、ts中,动态引入语言包，获取其中的文字
```
this.i18n.fanyi('app.analysis.traffic')
```




