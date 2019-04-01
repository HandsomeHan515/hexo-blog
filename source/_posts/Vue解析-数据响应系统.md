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

## 基本实现思路

```js
let target = null
let data = { price: 5, quantity: 2 }

class Dep {
    constructor () {
        this.subscribers = []
    }

    depend () {
        if (target && !this.subscribers.includes(target)) {
            this.subscribers.push(target)
        }
    }

    notify () {
        this.subscribers.forEach(sub => sub())
    }
}

Object.keys(data).forEach(key => {
    let internalValue = data[key]
    const dep = new Dep()

    Object.defineProperty(data, key, {
        get () {
            dep.depend() // Remember the target we'er running
            return internalValue
        },
        set (newVal) {
            internalValue = newVal
            dep.notify() // Re-run stored functions
        }
    })
})

function watcher (myFunc) {
    target = myFunc
    target()
    target = null
}

watcher(() => {
    data.total = data.price * data.quantity
})

console.log(data.price) // 5
console.log(data.quantity) // 3
console.log(data.total) // 10

data.price = 20
console.log(data.total) // 40

data.quantity = 3
console.log(data.total) // 60
```