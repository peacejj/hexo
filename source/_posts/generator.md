---
title: generator
date: 2021-04-11 17:46:14
excerpt: generator
tags: es6
position:
image_url:
---
Generator 函数
是es6的新特性，主要配合`yield`进行**函数暂停执行**。
```
function * gen(){
    console.log('start')
    yield 'hello world'
    console.log('middle')
    yield '!!!'
    console.log('end')
}

let g = gen();
g.next();//{value: 'hello world', done: false}
g.next();//{value: '!!!', done: false}
g.next();//{value: undefined, done: true}


//注意，最后一个输出undefined，如果想有值，在最后加return 'over'即可。
```
