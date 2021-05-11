---
title: 异步调度任务，同一时间限制输出。
date: 2021-05-11 10:17:39
excerpt: 异步调度任务，同一时间限制输出。
tags: [interview,js]
categories: 项目相关
index_img: /images/dark.jpeg
position: 1
---

```
class Scheduler {
    list = [];

    count = 0;

    constructor(num) {
        this.num = num;
    }

    async add(fn) {//fn是一个异步函数，返回
        if (this.count >= this.num) {
            await new Promise((resolve) => {
                this.list.push(resolve);
            });
        }

        this.count++;

        let res = await fn();

        this.count--;

        if (this.list.length > 0) {
            this.list.shift()();
        }

        return res;
    }
}

//以下是调用执行
const schedule = new Scheduler(2);//最多同一时间让它执行3个异步函数

const asyncFacotory = (n, time) => {

    return () => {

        return new Promise((resolve) => {

            setTimeout(() => {
                resolve(n);
            }, time)

        })

    }

}

schedule.add(asyncFacotory(1, 2000)).then((n) => {
    console.log(`异步任务:${n}`)
});
schedule.add(asyncFacotory(2, 2000)).then((n) => {
    console.log(`异步任务:${n}`)
});
schedule.add(asyncFacotory(3, 2000)).then((n) => {
    console.log(`异步任务:${n}`)
});
schedule.add(asyncFacotory(4, 2000)).then((n) => {
    console.log(`异步任务:${n}`)
});
schedule.add(asyncFacotory(5, 2000)).then((n) => {
    console.log(`异步任务:${n}`)
});
schedule.add(asyncFacotory(6, 2000)).then((n) => {
    console.log(`异步任务:${n}`)
});
```
