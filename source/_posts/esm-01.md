---
title: ESModule-演进。
date: 2022-08-11 20:44:39
excerpt: ESModule。
tags: [esm]
categories: 项目相关
index_img: /images/dark.jpeg
position: 1
---

# 1、ESModule 规范
### 简介
> 在 ES6 之前，社区自发的生产了一些模块加载方案，最主流的有 CommonJS 和 AMD 两种。> 前者用于服务器，后者用于浏览器（借助第三方库的实现）。
> 
> ES6 在语言标准的层面上，实现了模块功能，而且实现得相当简单，能满足绝大部分 CommonJS 和 AMD 的需求，成为浏览器和服务器通用的模块解决方案。

在 ES6 提出了 ESModule 作为语言标准以后，浏览器会原生支持这种规范来实现 JavaScript 的模块系统。同样地，作为跑在服务端的 JavaScript，Node.js 也会支持这一规范。

```
// commonjs
const axios = require('axios')

// amd
require(['axios'], function (axios) {})

// esmodule
import axios from './node_modules/axios/axios.js'

```

*注：ECMAScript 最新提案已经通过，可以通过 importMap 来管理 module import paths，参考内容可通过识别下方二维码访问。*

### 服务端的实现
#### CommonJS
在 ESModule 规范提出以前，Node.js 核心基于内置的 Module 模块来实现了 CommonJS 的模块系统，但是这个模块系统不是 JavaScript 官方标准提出的，而是 Node.js 的开发者给 Node.js 的实现，用于实现在服务器上将 JavaScript 原生模块化。这一标准并没有在浏览器中得到支持。

#### ESModule
在 JavaScript 本身的模块系统进标准后，Node.js 势必要去实现这个标准。但是，在 ESModule 标准没有被提出之前，此前基于 CommonJS 模块规范实现的包不可能就此放弃，而此前几乎所有的第三方依赖的包都是基于 CommonJS 的标准去实现，这时，如果 Node.js 为了兼容 ESModule 而放弃 CommonJS，就会导致整个 Node.js 的生态出现破坏性的兼容问题，所以最好的方案就是新的模块系统即实现新的 ESModule 规范，又可以兼容原先的 CommonJS 模块方案

#### 兼容问题
*由于 CommonJS 本质上与 ESModule 还是存在很有底层的设计冲突，所以在兼容层的实现上还是存在很多问题，这里不做赘述，有兴趣的读者可以自行搜索，下面也提供了一篇参考文章，可通过识别下方二维码访问。*

经过一系列的讨论，最终兼容的方案确定了下来：ESModule 标准的模块以后缀名 .mjs 为结尾， CommonJS 的模块依然以 .js 文件作为后缀，同时，在 Node.js >= V13 中，在 package.json 中引入了新的模块定义字段

```
// esmA/index.mjs
export default null

// esmB/index.js
export default null

// esmB/package.json
{
    "type": "module"
}

```
即，后缀为 .mjs 的文件和后缀为 .js 但是在 package.json 中声明了 "type": "module" 的模块都会在 Node.js 中被解析为 ESModule 模块


###  浏览器实现
现今主流的浏览器，除了IE，基本上在两三年就已经默默实现了基于 ESModule 规范的模块系统。

到这里，看似 Node.js 的已经兼容了 CommonJS 和 ESModule 规范，而且主流浏览器也已经基于 ESModule 规范实现了原生的模块系统，那么是不是意味着可以在浏览器上直接加载此前基于 CommonJS 开发的 npm 包了呢？

# 2、第三方依赖困境
### Node.js  的模块解析机制
在 Node.js 中，引入第三方依赖不需要写相对路径，只需要依赖名，而且也不需要指定入口文件。

```
const axios = require('axios')
// => 自动拼接 node_modules 路径
const axios = require('/User/xxxx/workspace/node_modules/axios')
// => 自动解析 package.json，拼接入口
const axios = require('/User/xxxx/workspace/node_modules/axios/axios.js')

```

这是因为 Node.js 模块系统的核心代码中做了对第三方依赖的路径解析有很多的兼容性处理，包括但不限于：

1. 自动拼接 node_moduoles 中第三方依赖的完整路径
1. 自动解析 package.json 中的入口字段，拼接入口路径到上一步的完整路径中
1. 自动解析是否需要添加后缀或者补全 index 路径

### Webpack 的模块解析机制
由于 Webpack 的存在，过去 Web App 的开发都是基于 Bundle 的开发模式。即，所有的第三方依赖最终都会被打成一个大的 bundle.js。

早先没有 ESModule 规范前，大部分包都是基于 CommonJS 开发，而 Webpack bundle 的过程中会自动注入 Node.js 的 CommonJS 模块处理逻辑，因此带给开发者的体验是，使用 Webpack 开发 Web App 时使用的 CommonJS 语法，与开发 Node.js App 时使用的 CommonJS 语法，带来感官上的体验是一致的。

但是其实这两者在底层上的实现是完全不同的， Node.js 的 CommonJS 实现是基于内置的 Module 等模块，在 Node.js Core 层实现的底层逻辑；而 Webpack 中处理 CommonJS 的逻辑是 Webpack 自己实现的一套解析逻辑，只是 Webpack 的维护团队为了使其在处理 CommonJS 时与 Node.js 中 CommonJS 处理逻辑保持一致，刻意做了一些兼容实现，来保证暴露给用户的顶层接口与 Node.js 保持一致。

因此，传统的第三方依赖能够在 Webpack CLI App 中正常使用 CommonJS 语法得益于 Webpack 实现的 CommonJS 模块解析逻辑与 Node.js 的 CommonJS 模块解析逻辑保持了一致。

### 浏览器的模块解析机制
通过 Node.js 和 Webpack 的模块解析机制，大家应该能明白，其实 Node.js 和 Webpack 之所以能加载 CommonJS 的插件，是因为它们各自做了大量的底层实现。而在浏览器中，是不支持原生的 CommonJS 模块加载机制的，因此，哪怕浏览器已经实现了基于 ESModule 规范的模块加载机制，也依然不能使用 Node.js 生态中的 CommonJS 模块。

出于这些原因，为了能够在浏览器中继续使用基于 CommonJS 规范开发的第三方依赖，下一代 Web App 开发工具应运而生。

# 3、下一代 Web App 开发模式
为了避免因为实现 ESModule 规范带来的 CommonJS 生态的第三方依赖包无法在浏览器使用的问题，许多工具随之产生。

### CommonJS To ESModule
浏览器无法使用 CommonJS 模块的最根本原因就是浏览器本身并没有实现任何 CommonJS 模块加载机制，而在主流浏览器纷纷支持 ESModule 规范之后，解决这个问题的根本办法就是将 CommonJS 模块转化为 ESModule 模块，现今社区主流的转化方案大概如下：

- JSPM （https://jspm.dev/@babel/core）
- Skypack（前 Pika）（http://cdn.skypack.dev/@babel/core）
- ESM.sh（http://esm.sh/@babel/core)
这些工具的处理方案大致都是利用转换（构建）工具，比如 Rollup、Esbuild 等，去给模块语法做升级操作，将 CommonJS 的语法升级为 ESModule 规范的语法，同时将其中引入另外第三方依赖的路径转化拼接为对应的 ESModule 路径

```
const axios = reuqire('axios')
module.exports = axios
// =>
import axios from '/esm/axios.js'
export default axios

```
当有了这些工具之后，我们就能在浏览器中直接原生的去使用这些第三方依赖。
```
//index.html
<script type="module">
    import axiso from 'http://cdn.skypack.dev/axios'
    // TODO...
</script>
```
但是笔者实践过一段时间后，用这种最简单的方式来使用第三方依赖还是会遇到很多问题，最明显的就是请求爆炸。

比如一个 Antd 组件库，里面使用到的第三方依赖就有成百上千个，由于没有了 Bundle，加载这些依赖的过程就直接变成了网络请求，这就导致下载一个简简单单的 Antd 组件库，需要发送接近 2000 个网络请求，这一方面加重了首屏渲染的压力，另一方面给整个开发链路加重了负担。

基于这种背景和发展趋势，下一代 Web App 开发工具也随之诞生。

### Snowpack & Vite
由于上述说到加载第三方依赖时可能会遇到的请求爆炸的问题，因此在本地开发时对于第三方依赖的 Bundle 这个过程其实还是不可省略的。

在 Webpack 原先的理念中，所有的文件都会被当作一个模块，并打包到最终的 Bundle 中，这就导致了整个 Bundle 的过程几乎不可复用，即每一次启动 Dev Server 时，都会重新做一次 Bundle，而这是非常耗费性能和时间的。但是在开发的过程中，第三方依赖的代码基本上是维持不变的，只用下载一次，就不会再发生变化，而且浏览器已经原生支持基于了 ESModule 规范的模块加载机制，开发工具完全可以不用再接管第三方依赖的加载，而是可以将这个过程全部交给浏览器本身来进行。

基于此，第三方依赖和源代码可以拆分成两个维度的概念，开发工具可以不用再将所有的源代码和第三方依赖全部 Bundle 到一起，而是可以将第三方依赖和源代码拆分开来单独进行处理。将不会再发生变化的第三方依赖单独 Bundle 为一个 Dep Chunks，处理一次，到处使用。

基于这个理念，Snowpack 和 Vite也逐渐地崭露头角，这里对这两种新一代的 ESM 开发工具不再展开做介绍，有兴趣的读者可以自行去了解。

# 4、推进困难
上述说到，新一代的开发工具和开发模式是利用浏览器原生的模块加载机制去加载第三方依赖，开发工具只需要将依赖 Bundle 一次，后续就不用再多做处理。

这种新的开发模式一方面降低了开发工具的复杂性（利用浏览器原生的模块加载机制，开发工具不再接管依赖的加载），另一方面大大提升的应用的启动速度，和本地开发的调试体验（不用在每次启动 dev server 时去重新处理 node_modules）。

但是从整个生态的大环境来看，这种新一代的开发模式推进情况却不容乐观。

笔者经常这段时间的使用下来，也发现了这些开发工具目前的通病：

### 生态问题
生态不完善，很多 Webpack 已经支持很好的功能还没有得到官方的支持。

### CommonJS 向 ESModule 的转换问题
对很多第三方依赖的转化处理不完善，缺失完善的解决机制。

上面也说到，要将第三方依赖的加载全部交给浏览器本身来接管，那么首先开发工具要做的就是将第三方依赖全部转换为 ESModule 的模块，而现在 npm 上的绝大部分包都是只支持 CommonJS 版本的，因此这里的转换过程通常都是由开发工具自己来接管，而这其中有很多底层的问题并没有得到好的解决。

#### 动态引入与静态解析

CommonJS 中，模块的引入与导出是动态的语法，因此要想拿到一个模块完整的导入或者导出，必须得等到那个模块具体执行的时候才能确认。

```
// mnoduleA.js
init()

function init () {
    exports.a = 1
}
// index.js
require('./moduleA') // { a: 1 }

```

而在 ESModule 中，模块语法是需要被解释器静态解析的，这就要求导出语法必须声明在作用域的最顶层。

```
// Success
export const a = 1

// Error
init ()
function init() {
    export const a = 1
}
```
#### 具名导出与默认导出

在 CommonJS 中，对具名导出和默认导出是没有具体做限定的，nodejs的一个模块中，全局的 exports === module.exports。

而在 ESModule 中，对具名导出和默认导出做出了一个具体的限定，而且在导入语法中也有明显的区别。
```
// module.js
export const a = 1
export default { a: 2 }
// index.js
import { a } from './module.js' // 1

import a from './module.js'
console.info(a.a) // 2

import * as a from './module./js'
console.info(a.a) // 1
console.info(a.default) // 2
```

同时，在 ESModule 规范推进的过程中，有许多如 exports.default、exports.__esModule 等利用语法来兼容 ESModule 和 CommonJS 的废案往往也都被 babel 实现，而且被许多开发者使用并且发布到了 npm 上，这就导致了现在 npm 上的许多包中有大量的废弃兼容性代码，而这些代码往往会对开发工具的转化造成阻碍。