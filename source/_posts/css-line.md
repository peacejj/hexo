---
title: 单行、多行超出
date: 2020-03-24 15:36:56
excerpt: 单行、多行超出
tags: css
categories: 基础知识
position:
image_url:
---
#### 单行
```
p{
    overflow:hidden;
    text-overflow:ellipsis;
    white-space:nowrap;
}
```

#### 多行
```
p{
    display: -webkit-box;
    -webkit-line-clamp: 2;
    -webkit-box-orient: vertical;
    overflow: hidden;
}
```

#### 判断单行文本是否溢出：比较scrollWidth是否大于offsetWidth
```
<script>
    function main() {
      let box = document.querySelector('.box')
      console.log("scrollWidth: ", box.scrollWidth)
      console.log("offsetWidth: ", box.offsetWidth)
      if (box.scrollWidth > box.offsetWidth) {
        console.log("出现了省略号")
      } else {
        console.log("没有出现省略号")
      }
    }
    main()
  </script>
```

