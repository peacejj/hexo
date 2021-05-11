---
title: DOM 解析和渲染
date: 2021-04-11 10:21:52
excerpt: DOM 解析和渲染
tags:
position:
image_url:
---
### 一、总结：
1. css不会阻止dom解析，但会阻止dom渲染
2. script标签要等前面外联的css下载完成后，再去执行。如果link的css文件太大，则没必要等待。
3. js阻塞DOM解析和渲染，但浏览器会"偷看"DOM，预先下载相关资源

### 二、阶段含义
阶段 | 含义
--|--
Send Request  |  发送http请求
Receive Response  |  接收http返回数据                            
Parse HTML | 解析html
Parse Stylesheet | 解析css文件
Evaluate Script | 执行js脚本
domContentLoadedEventEnd | dom创建完成     
Paint | 渲染页面
loadEventEnd | 页面加载完成

### 三、常见问题
##### 1、为何`<script>`与`<link>`同时在头部的话，`<script>`在上可能会更好？
script标签要等前面外联的css下载完成后，再去执行。如果link的css文件太大，则没必要等待。


##### 2、`<script>`最好放底部，`<link>`最好放头部。

##### 3、js会阻塞DOM解析和渲染，如何优化？
1. 如果js文件太大，同时没必要阻塞dom解析的话，可以延后加载，设置defer属性。
2. 如果js执行时间太长，可以用异步方法。当

##### 4、html的解析流程是什么
1. 解析HTML结构。
2. 并行加载脚本、样式表文件、img等外部资源
3. 如有js文件未下载，则等待下载完成（js文件前如有css文件，则需要先等css文件下载完成）
4. 脚本下载后立即执行，然后从当前解析处开始解析
5. DOM树构建完成。//DOMContentLoaded
6. 解析外部css文件，img图片等资源
7. paint，渲染页面
8. 页面加载完毕。//load


