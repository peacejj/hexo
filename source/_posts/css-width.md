---
title: offsetWidth、clientWidth、scrollWidth、style.width区别
date: 2021-03-21 14:47:30
excerpt: offsetWidth、clientWidth、scrollWidth、style.width区别
tags: offsetWidth,clientWidth,scrollWidth
position:
image_url:
---

1. offsetWidth: contentWidth + padding + border
1. clientWidth: contentWidth + padding
1. scrollWidth: contentWidth + padding + 溢出区域的内容（不包含border）
1. style.width: 需定义在html上，如果在css上，仍然为空，且获取后有单位。
