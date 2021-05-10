---
title: package.json中dependencies 和 devDependencies 区别
date: 2019-05-10 17:34:20
excerpt: package.json中dependencies 和 devDependencies 区别
tags: package
position:
image_url:
---
- 当项目A发布到npm时，其他人npm install A时会一起下载A的dependencies，而A的devDependencies不会一起下载。
- 对于项目A自己而言，npm install时都会下载，没什么区别。
