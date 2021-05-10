---
title: 锚点及void运算
date: 2019-07-08 15:52:05
excerpt: 锚点及void运算
tags: 
position:
image_url:
---

```
<a href="#"></a>
<a href="#none"></a>
<a href="###"></a>
<a href="javascript:"></a>     //错误写法，没写分号
<a href="javascript:;"></a>
<a href="javascript:void(0)"></a>    //错误写法，没写分号
<a href="javascript :void(0);"></a>
```

- 第1种，点击这个链接后，会让页面跳到页面顶部，在location.href后面增加#号。
- 第2种，点击这个链接后， 如果页面里面有id为none的元素，会执行锚点机制跳转到这个元素上缘。
- 第3种，不跳转，可以阻止默认的跳转行为，但是这个在后端代码中容易识别成注释，后面的代码不显示，之前遇到过这种坑，之后再没用过。
- 后面几种使用了javascript伪协议。
- 第5和7种相等，不发生任何变化。void运算符只运算，不返回任何结果。
- 为什么要列出4和6呢，有时候有些同学会忘记写分号，这样在IE6下面点击a标签，会造成页面中的gif暂停。
