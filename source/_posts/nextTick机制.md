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

## macroTimerFunc

```js
let macroTimerFunc

// Determine (macro) task defer implementation.
// Technically setImmediate should be the ideal choice, but it's only available
// in IE. The only polyfill that consistently queues the callback after all DOM
// events triggered in the same loop is by using MessageChannel.
/* istanbul ignore if */
if (typeof setImmediate !== 'undefined' && isNative(setImmediate)) {
    macroTimerFunc = () => {
        setImmediate(flushCallbacks)
    }
} else if (typeof MessageChannel !== 'undefined' && (
    isNative(MessageChannel) ||
    // PhantomJS
    MessageChannel.toString() === '[object MessageChannelConstructor]'
)) {
    const channel = new MessageChannel()
    const port = channel.port2
    channel.port1.onmessage = flushCallbacks
    macroTimerFunc = () => {
        port.postMessage(1)
    }
} else {
    /* istanbul ignore next */
    macroTimerFunc = () => {
        setTimeout(flushCallbacks, 0)
    }
}
```

## microTimerFunc

```js
let microTimerFunc

if (typeof Promise !== 'undefined' && isNative(Promise)) {
    const p = Promise.resolve()
    microTimerFunc = () => {
        p.then(flushCallbacks)
        // in problematic UIWebViews, Promise.then doesn't completely break, but
        // it can get stuck in a weird state where callbacks are pushed into the
        // microtask queue but the queue isn't being flushed, until the browser
        // needs to do some other work, e.g. handle a timer. Therefore we can
        // "force" the microtask queue to be flushed by adding an empty timer.
        if (isIOS) setTimeout(noop)
    }
} else {
    // fallback to macro
    microTimerFunc = macroTimerFunc
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

## 使用情景（Vue 2.5+）

> 在Vue 2.4之前的版本中，nextTick几乎都是基于microTask实现的，但是由于microTask的执行优先级非常高，在某些场景之下它甚至要比事件冒泡还要快，就会导致一些诡异的问题；但是如果全部都改成macroTask，对一些有重绘和动画的场景也会有性能的影响。所以最终nextTick采取的策略是默认走microTask，对于一些DOM的交互事件，如v-on绑定的事件回调处理函数的处理，会强制走macroTask

### microTask

```js
new Vue({
    el: '#app',
    data: {
        name: 'a'
    },
    mounted() {
        this.name = 'b';
        console.log('script')
        this.$nextTick(function () {
            console.log('nextTick')
        })
        Promise.resolve().then(function () {
            console.log('promise')
        })
    },
})
// 'script','nextTick','promise'
```

### macroTask

```html
<div id="app">
    <span>{{name}}</span>
    <button @click="handleClick">change</button>
</div>
<script>
    new Vue({
        el: '#app',
        data: {
            name: 'start'
        },
        methods: {
            handleClick: function () {
                this.nam = 'end';
                console.log('script')
                this.$nextTick(function () {
                    console.log('nextTick')
                })
                Promise.resolve().then(function () {
                    console.log('promise')
                })
            }
        }
    })
</script>
// 触发事件后 => 'script','promise','nextTick'
```
