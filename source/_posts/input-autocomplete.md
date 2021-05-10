---
title: 消除浏览器对input输入框的自动填充
date: 2019-05-07 15:42:27
excerpt: 消除浏览器对input输入框的自动填充
tags:
position:
image_url:
---
> 参考url：https://www.cnblogs.com/mamimi/p/9138555.html
> 
Mozilla官方文档建议的是 直接使用 autocomplete = ‘off’ 即可禁止输入框从浏览器cache获取数据，博主以前使用这个也就足够兼容浏览器了。

现在发现，却在chrome、firfox上有兼容性 无法解决。

后来查阅相关资料得到以下解决方法：

**1：可以设置一个默认的input用来接收浏览器的默认填充，并且设置css为dispaly：none
形如：**

```
<input type = 'text' style='display:none'>
```

这样既不会影响用户的体验，也可以兼容所有的浏览器，但经过测试却发现，在chrome上不起作用，在firefox上也只能对type != password的 输入框起作用。

**2：autocomplete = 'new-password'**
```
<input type='text' >
<input type='password' autocomplete='new-password' >
```

使用上诉代码，在chrome上既可生效，用户名与密码都不会自动填充，但是firefox上任然会自动填充用户名

**3：结合上诉两个情况
<input type='text' style='display:none'>**
```
<!-- 针对firefox -->
用户名：<input type='text' autocomplete='off'> 
密码：<input type='password' autocomplete='new-password'>
```
既可解决针对chrome与firefox内核的浏览器自动填充输入框的问题

**4：但是近期经过测试发现 这种方法还是不能解决firefox上密码框的历史输入，可以在3步骤上做这样的操作：**

```

<input type='password' autocomplete="new-password"
       style="background-color: #FFFFFF!important;"
       readonly onfocus="this.removeAttribute('readonly');"
       onblur="this.setAttribute('readonly',true);"/>
```


