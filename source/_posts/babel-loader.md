---
title: babel-loader的原理
date: 2020-07-10 16:01:17
excerpt: Babel包的构成
tags: webpack
categories: 原理知识
position:
image_url:
---
#### es6转es5分为3步：
1. 解析。词法分析和语法分析。
    生成ES6语法树 语法分析，生成抽象语法树
2. 转换。根据ES6 ast生成ES5 ast。
3. 代码生成。根据ES5ast生成ES5代码

###### 流程：
es6代码的输入---》babelon进行解析---》得到AST--》plugin用babel-traverse对AST树进行遍历 ---》得到新的AST树 ---》使用babel-genertor通过AST树生成es5代码。

#### Babel包的构成
###### 核心包
- babel-core：是babel转译器本身，提供转译的API，例如babel.transform等，webpack的babel-loader就是调用这些API完成转译的
- babylon：js的词法解析器
- babel-traverse：用于对AST（抽象语法树Abstract Syntax Tree）的遍历
- babel-generator：根据AST生成代码

###### 其他
- babel-cli：用于命令行转码
- babel-types：用于检验，构建和变更AST的节点
- babel-helpers：一系列预制的babel-template函数，用于提供给一些plugins使用
- babel-template：辅助函数，用于从字符串形式的代码来构建AST树节点
- babel-code-frame：用于生成错误信息，打印出错误点源代码帧以及指出出错位置
- babel-register：通过绑定node.js的require来完成自动编译
- babel-polyfill：JS标准新增的原生对象和API的shim，实现上仅仅是core-js和regenerator-runtime两个包的封装
- babel-runtime：类似于polyfill，但是不会污染全局变量

##### polyfill与runtime的区别
最主要的区别就是polyfill引入后，会将新的原生对象、API这些都直接引入到全局环境，这样就会污染全局变量，这也是polyfill的缺陷。所以就轮到babel-runtime上场了。

##### transform-runtime和babel-runtime
transform-runtime是将js中使用到新的原生对象和静态方法转译成对babel-runtime的引用，而其中babel-runtime的功能其实最终也是由core-js来实现的，其实真正的核心是上面所讲的core-js，其他的都是包装。

