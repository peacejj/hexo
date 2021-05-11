---
title: 前端模块化比较
date: 2020-03-10 18:13:15
excerpt: 前端模块化比较
tags: js
position:
image_url:
---
参考链接：https://blog.csdn.net/haochangdi123/article/details/80408874

### commonJs和es6比较总结
1. CommonJS模块输出是一个值的拷贝，ES6模块输出是值的引用。
2. CommonJS模块是运行时加载，ES6模块是编译时输出接口。
3. CommonJS模块无论require多少次，都只会在第一次加载时运行一次，然后保存到缓存中，下次在require，只会去从缓存取。


### 一、commonJs
1. 运行在服务端，node模块化，webpack就是以此写的node模块。浏览器不兼容。
2. 特性：同步。
3. 模块：引用(require) ， 定义(exports) ，标识(module)。
4. 浏览器不兼容CommonJS的根本原因，也正是在于缺少四个Node.js环境的变量。
    - module
    - exports
    - require
    - global

### 二、AMD
1. 特性：异步。代表：require.js
2. 先引入的模块，后使用引用模块的方法，所以我们称之为依赖前置。

### 三、CMD
1. 特性：异步。代表：sea.js
2. 通用模块定义。依赖就近原则

### 四、es6
上述commonJs，AMD，CMD都是ES5时期，ES6中已经实现模块化，完全可以取代他们的规范，成为浏览器和服务器通用的模块解决方案。
1. ES6 模块的设计思想是尽量的**静态化**，使得编译时就能确定模块的依赖关系，以及输入和输出的变量。CommonJS 和 AMD 模块，都只能在运行时确定这些东西。

### 五、require和import区别
1. 模块加载的时间
    - require：运行时加载
    - import：编译时加载（效率更高）【由于是编译时加载，所以import命令会提升到整个模块的头部】
2. require：模块就是对象，输入时必须查找对象属性
    - require实质上整体加载了fs对象（fs模块），然后再从fs对象上读取方法
    - ES6模块，实质上从fs模块加载对应的方法，其他方法不加载
3. CommonJS 模块输出的是一个`值的拷贝`，ES6 模块输出的是`值的引用`

### 六、module.exports 和 exports方法的区别
1. export是设置导出模块对象的指定属性，可以导出多个
2. module.export既可以设置导出模块的所有属性，又可以设置导出模块的指定属性。**如果说设置位到出所有属性，module.exports只认最后一次导出设置**

```
//exports使用方式
// 可以多次导出多个属性
exports.func = str => console.log(str)
exports.test = str => console.log(str)
// 返回结果 { func : [Function] , test : [Function] }


// module.exports导出所有，只认最后一次导出设置
module.exports = {
	func: str => console.log(str)
}
module.exports = {
	test : str => console.log(str)
}
// 返回结果 { test : [Function: test] }
```
### 七、ES6使用export和import来导出/导入模块。
1. export与export default均可用于导出常量/函数/文件/模块等；
2. 在一个文件或模块中，export/import可以有多个，export default只有一个；
3. 通过export方式导出，在导入时需要加{}，export default不需要；
4. export能导出变量/表达式，export default不可以。

总结：
- module.exports与exports ，是CommonJS的规范，被使用于Node.js中。
- export与export default ，是ES6规范，被使用于React或Vue中。
