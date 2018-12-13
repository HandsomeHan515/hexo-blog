---
title: 'call,apply,bind的区别'
date: 2018-12-12 10:27:39
tags: JavaScript
categories: JavaScript
---

# Javascript call,apply,bind的区别

> call 和 apply 是为了改变 this 的指向
> 除了第一个参数外，call 可以接收一个参数列表，apply 只接受一个参数数组

```js
let a = {
    value: 'han'
}

function person(name, age) {
    console.log(name)
    console.log(age)
    console.log(this.value)
}

person.call(a, 'han', 23) // han 23 handsome
person.apply(a, ['han', 23]) // han 23 handsome
```

```js
let list = []
Array.apply(null, { length: 3 }).map((item, page) => {
    page = page + 1
    list.push({ code: page, name: `第${page}页` })
})

```

## 模拟实现 call 和 apply

```js
Function.prototype.myCall = function(context) {
    if (typeof this !== 'function') {
        throw new Error('Error')
    }
    context = context || window
    context.fn = this
    let args = [...arguments].slice(1)
    let result = context.fn(...args)
    delete context.fn
    return result
}
```

。。。待完善