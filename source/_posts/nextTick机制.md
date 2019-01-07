---
layout: vue
title: nextTick机制
date: 2019-01-07 11:05:01
tags:
---

# nextTick 机制

> nextTick主要功能就是改变数据后让回调函数作用于dom更新后
> nextTick 源码中使用了一个异步锁的概念，即接收第一个回调函数时，先关上锁，执行异步方法。此时，浏览器处于等待执行完同步代码就执行异步代码的情况
> 相当于一群旅客准备上车，当第一个旅客上车的时候，车开始发动，准备出发，等到所有旅客都上车后，就可以正式开车了

```js
export default {
    data () {
        return {
            msg: ''
        }
    },
    mounted () {
        this.msg = 'end'
        console.log('1')
        setTimeout(() => { // macroTask
            console.log('3')
        }, 0)
        Promise.resolve().then(function () { // microTask
            console.log('promise!')
        })
        this.$nextTick(function () {
            console.log('2')
        })
        console.log(this.msg)
    }
}
// 执行顺序 1,'end', 'promise!', '2', '3'
```

## flushCallbacks 函数

```js
let callbacks = []
let pending = false

function flushCallbacks () {
    // 重置异步锁
    pending = false
    // 防止出现nextTick中包含nextTick时出现问题，在执行回调函数队列前，提前复制备份，清空回调函数队列
    const copies = callbacks.slice(0)
    callbacks.length = 0
    // 执行回调函数队列
    for (let i = 0; i < copies.length; i++) {
        copies[i]()
    }
}
```

## nextTick 函数

```js
export function nextTick (cb?: Function, ctx?: Object) {
    let _resolve
    callbacks.push(() => {
        if (cb) {
            try {
                cb.call(ctx)
            } catch (e) {
                handleError(e, ctx, 'nextTick')
            }
        } else if (_resolve) {
            _resolve(ctx)
        }
    })
    // 如果异步锁未锁上，锁上异步锁，调用异步函数，准备等同步函数执行完后，就开始执行回调函数队列
    if (!pending) {
        pending = true
        if (useMacroTask) {
            macroTimerFunc()
        } else {
            microTimerFunc()
        }
    }
    // $flow-disable-line
    if (!cb && typeof Promise !== 'undefined') {
        return new Promise(resolve => {
            _resolve = resolve
        })
    }
}

// nextTick 源码中使用了一个异步锁的概念，即接收第一个回调函数时，先关上锁，执行异步方法。此时，浏览器处于等待执行完同步代码就执行异步代码的情况
```