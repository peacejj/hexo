---
title: happyPack多线程打包
date: 2021-03-25 17:28:21
tags:
---
##### 1、安装happypack
```
    npm i happypack
```

##### 2、实现js和css的多线程打包
```
let HappyPack = require('happypack');

module.exports = {
    ...
    module:{
        rules:[
            {
                test:/\.js$/,
                use:'HappyPack/loader?id=js'//这个id=js就代表这是打包js的
            },
            {
                test:/\.css$/,
                use:'HappyPack/loader?id=css'//这个id=css就代表这是打包css的
            }
        ]
    },
    plugins:[
        new HappyPack({这个id:js就代表这是打包js的
            id:'css',//
            use:['style-loader','css-loader']
        }),
        new HappyPack({这个id:js就代表这是打包js的
            id:'js',//
            use:[{//use是一个数组，这里写原先在rules的use里的loader配置
                loader:'babel-loader',
                options:{
                    presets:[
                        '@babel/presets-env',
                        '@babel/presets-react'
                    ]
                }
            }]
        })
    ]
}
```

