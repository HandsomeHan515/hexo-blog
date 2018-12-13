---
title: Generator函数
date: 2018-12-13 13:34:09
tags: JavaScript
categories: JavaScript
---

# Generator 函数

## yield 表达式

1. 遇到 yield 语句就暂停执行后面的操作，并将紧跟在 yield 后的表达式的值作为返回的对象的 value 属性值
2. 下一次调用 next 方法时再继续往下执行，直到遇到下一条 yield 语句
3. 如果没有在遇到新的 yield 语句，就一直运行到函数结束，直到 return 语句为止，并将 return 语句后面的表达式的值作为返回对象的 value 属性值
4. 如果该函数没有 return 语句，则返回对象的 value 属性值为 undefined

## 基础用法

```js
function* helloWorld() {
    yield 'hello'
    yield 'world'
    return 'ending'
}

let hw = helloWorld()
let a = hw.next()
console.log(a) // {value: "hello", done: false}
let b = hw.next()
console.log(b) // {value: "world", done: false}
let c = hw.next()
console.log(c) // {value: "ending", done: true}
```

## next 传参

```js
function* gen() {
    let a = yield 123 + 456
    yield a * 2
    return a
}
let g = gen()
let aa = g.next()
console.log(aa) // {value: 579, done: false}
let bb = g.next(1)
console.log(bb) // {value: 2, done: false}
let cc = g.next()
console.log(cc)  // {value: 1, done: true}
```

### 同步执行 AJAX 请求

```js
function request () {
    let options = {
        data: { 
            date1: '110108209031',
            date2: '110108201832',
        }
    }
    http.post('path/to/your/url', options, res => {
        it.next(res)
    })
}

function* main(){
    let a = yield request()
    console.log('a', a) // res {}
}

let it = main()
it.next()
```

## 搭配 Co 

Git地址 [https://github.com/tj/co](https://github.com/tj/co)
yield 支持情况

+ promises
+ thunks (functions)
+ array (parallel execution)
+ objects (parallel execution)
+ generators (delegation)
+ generator functions (delegation)

JS 中的 sleep 函数实现

```
let co = require('co')

function sleep(ms){
  return function(callback){
    setTimeout(callback, ms);
  };
}

co(function* (){
  console.log('1');
  yield sleep(10000); // 10s之后输出 2
  console.log('2');
});

```