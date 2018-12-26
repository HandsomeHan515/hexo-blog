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
let Target = null
const data = {
    a: {
        a: 10
    },
    b: 2
}

function reactive(data) {
    for (let key in data) {
        const dep = []
        let val = data[key]
        // 如果 val 是对象，递归调用 walk 函数将其转为访问器属性
        const nativeString = Object.prototype.toString.call(val)
        if (nativeString === '[object Object]') {
            reactive(val)
        }

        Object.defineProperty(data, key, {
            set(newVal) {
                if (newVal === val) return
                val = newVal
                dep.forEach(fn => fn())
            },
            get() {
                dep.push(Target)
                return val
            }
        })
    }
}

function $watch(exp, fn) {
    Target = fn
    let pathArr,
        obj = data
    // 检查 exp 中是否包含 .
    if (/\./.test(exp)) {
        // 将字符串转为数组，例：'a.b' => ['a', 'b']
        pathArr = exp.split('.')
        // 使用循环读取到 data.a.b
        pathArr.forEach(p => {
            obj = obj[p]
        })
        return
    }
    data[exp]
}

$watch('a.a', () => {
    console.log('第一个依赖')
})

data.a.a = 20 // 第一个依赖
```

待续。。。