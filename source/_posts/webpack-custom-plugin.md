---
title: webpack自定义插件
date: 2020-07-20 16:11:03
excerpt: webpack自定义插件
tags: webpack
position:
image_url:
---
## webpack构建流程：
1. 初始化参数：从配置文件和 Shell 语句中读取与合并参数，得出最终的参数；
1. 开始编译：用上一步得到的参数初始化 Compiler 对象，complier可以视为一个webpack的实例，存在于webpack从启动到结束的整个过程，他包含了webpack的module、plugin等参数信息。执行对象的 run 方法开始执行编译；
1. 确定入口：根据配置中的 entry 找出所有的入口文件；
1. 创建compilation对象：可以理解为webpack一次编译的过程，包含了当前编译环境的所有资源，包括编译后的文件。
1. 翻译模块：通过配置信息，调用loader进行模块翻译，使用acorn将模块转换为AST（抽象语法树）,当遇到require依赖时，创建依赖并加入依赖数组，再找出依赖的依赖，递归处理所有的依赖。
1. 输出资源：根据入口和模块之间的依赖关系，组装成一个个包含多个模块的 Chunk，再把每个 Chunk 转换成一个单独的文件加入到输出列表，这步是可以修改输出内容的最后机会；
1. 在确定好输出内容后，根据配置确定输出的路径和文件名，把文件内容写入到文件系统。


## 一个典型的Webpack插件代码如下：
```
// 插件代码
class MyWebpackPlugin {
  constructor(options) {
  }
  
  apply(compiler) {
    // 在emit阶段插入钩子函数
    compiler.hooks.emit.tap('MyWebpackPlugin', (compilation) => {});
    
   // 
   compileer.hooks.阶段.tap函数('插件名称', (阶段回调参数) => {
  
   });
  }
}

module.exports = MyWebpackPlugin;apply方法中插入钩子的一般形式如下：
```

##### 常见钩子
Webpack会根据执行流程来回调对应的钩子，下面我们来看看都有哪些常见钩子，这些钩子支持的tap操作是什么。

钩子 | 说明 | 参数 | 类型
---|---|---|---
afterPlugins | 启动一次新的编译 | compiler | 同步
compile | 创建compilation对象之前 | compilationParams | 同步
compilation | compilation对象创建完成 | compilation | 同步
emit | 资源生成完成，输出之前 | compilation | 异步
afterEmit | 资源输出到目录完成 | compilation | 异步
done | 完成编译 | stats | 同步



## Tapable
> Tapable是Webpack的一个核心工具，Webpack中许多对象扩展自Tapable类。Tapable类暴露了tap、tapAsync和tapPromise方法，可以根据钩子的同步/异步方式来选择一个函数注入逻辑。
- tap 同步钩子
- tapAsync 异步钩子，通过callback回调告诉Webpack异步执行完毕
- tapPromise 异步钩子，返回一个Promise告诉Webpack异步执行完毕
