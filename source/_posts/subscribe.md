---
title: 事件发布订阅
date: 2021-01-11 10:10:43
excerpt: 发布订阅
tags: interview
position:
image_url:
---

```
class Events {
    constructor() {
        this.events = new Map();
    }

    addEvent(key, fn, isOnce, ...args) {
        const value = this.events.get(key) ? this.events.get(key) : this.events.set(key, new Map()).get(key)
        value.set(fn, (...args1) => {
            fn(...args, ...args1)
            isOnce && this.off(key, fn)
        })
    }

    on(key, fn, ...args) {
        if (!fn) {
            console.error(`没有传入回调函数`);
            return
        }
        this.addEvent(key, fn, false, ...args)

        console.log(this.events);
    }

    fire(key, ...args) {
        if (!this.events.get(key)) {
            console.warn(`没有 ${key} 事件`);
            return;
        }
        for (let [, cb] of this.events.get(key).entries()) {
            cb(...args);
        }
    }

    off(key, fn) {
        if (this.events.get(key)) {
            this.events.get(key).delete(fn);
        }
    }

    once(key, fn, ...args) {
        this.addEvent(key, fn, true, ...args)
    }
}
```

思考：必须先订阅再发布吗？如何解决？
```
解决：我们可以加一个cache，保存历史新发布的消息记录，等新用户订阅时，直接执行。
list :{event:[client1,client2]}
cache ：{event:[params1,params2,...]}

var Event = function () {
  this.list = {}
  this.cache = {}
}

Event.prototype.add = function (area, client) {
  if (!this.list[area]) this.list[area] = []
  this.list[area].push(client)
  this.cache[area].forEach(price => {
    client.listen(area, price)
  })
}

Event.prototype.once = function (area, callback) {
    let wrapFanc = (...args) => {//创建一个wrapFanc函数实现单次调用后停止监听
        callback.apply(this.args)//执行wrapFanc
        this.off(event,wrapFanc)//后停止监听事件
   }

    this.add(area,wrapFanc)

  }
  
Event.prototype.remove = function (area, client) {
  if (!this.list[area]) return
  var index = this.list[area].findIndex(item => item === client)
  this.list[area].splice(index, 1)
}


Event.prototype.triggle = function (area, price) {
  if (!this.cache[area]) this.cache[area] = []
  this.cache[area].push(price)

  if (!this.list[area]) return
  this.list[area].forEach(client => {
    client.listen(area, price)
  })
}

var Client = function (name) {
  this.name = name
}

Client.prototype.listen = function (area, price) {
  console.log(`${this.name}收到${area}平的房源报价${price}`)
}


var client1 = new Client('client1')
var client2 = new Client('client2')


var event = new Event()
// event.add('80平', client1)
// event.add('100平', client1)
// event.add('80平', client2)
// event.add('300平', client1)
// event.remove('300平', client1)

event.triggle('80平', 200) // client1收到80平平的房源报价200 client2收到80平平的房源报价200
event.triggle('100平', 500) // client1收到100平平的房源报价500
event.triggle('200平', 1000) //


var client3 = new Client('client3')
event.add('80平', client3)
event.add('100平', client3)

event.add('80平', client1)
event.add('100平', client1)

event.triggle('80平', 1000) 

```
