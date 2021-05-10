---
title: weakSet和weakMap
date: 2021-04-23 18:11:47
excerpt: weakSet和weakMap
tags:
position:
image_url:
---
### weakSet 和 Set 对象的区别有两点:
1. WeakSet 对象中只能存放对象引用, 不能存放值, 而 Set 对象都可以.
2. WeakSet 对象中存储的对象值都是被弱引用的, 如果没有其他的变量或属性引用这个对象值, 则这个对象值会被当成垃圾回收掉. 正因为这样, WeakSet 对象是无法被枚举的, 没有办法拿到它包含的所有元素


>WeakSet 不能被遍历，也没有size属性。
