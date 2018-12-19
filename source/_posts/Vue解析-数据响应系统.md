---
title: Vue解析-数据响应系统
date: 2018-12-14 15:25:51
tags: JavaScript
categories: JavaScript
---

# Vue解析-数据响应系统

## 实例对象代理访问数据 data

```js
const sharedPropertyDefinition = {
    enumerable: true,
    configurable: true,
    get: null,
    set: null
}

function proxy(target, sourceKey, key) {
    sharedPropertyDefinition.get = function proxyGetter() {
        console.log('han)
        return this[sourceKey][key]
    }
    sharedPropertyDefinition.set = function proxySetter(val) {
        this[sourceKey][key] = val
    }
    Object.defineProperty(target, key, sharedPropertyDefinition)
}

function Data() {
    this.data = {
        a: 1,
        b: 2
    }

    for(let key in this.data) {
        proxy(this, 'data', key)
    }
}

let data = new Data()
console.log(data.a , data.b) // 1 2

data.a = 10
data.b = 20
console.log(data.a , data.b) // 10 20
```