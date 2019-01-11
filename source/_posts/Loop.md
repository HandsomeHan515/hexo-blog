---
layout: javascript
title: Loop
date: 2018-05-03 10:00:26
tags: JavaScript
categories: JavaScript
---
# Event Loop

> JS 是门非阻塞单线程语言，因为在最初 JS 就是为了和浏览器交互而诞生的。JS 在执行的过程中会产生执行环境，这些执行环境会被顺序的加入到执行栈中。如果遇到异步的代码，会被挂起并加入到 Task 队列中。一旦执行栈为空，Event Loop 就会从 Task 队列中拿出需要执行的代码并放入执行栈中执行。
> 不同的任务源会被分配到不同的 Task 队列中，任务源可以分为 微任务（microtask） 和 宏任务（macrotask）。在 ES6 规范中，microtask 称为 jobs，macrotask 称为 task。

## 微任务(microtask)

  process.nextTick, Promise, MutationObserver

## 宏任务(macrotask)

  script, setTimeout, setInterval, setImmediate, I/O, UI rendering

> 微任务快于宏任务，这个说法是错误的。因为宏任务中包括了 script ，浏览器会先执行一个宏任务，接下来有异步代码的话就先执行微任务。

## 一次正确的Event Loop

1. 初始状态：调用栈空。micro 队列空，macro 队列里有且只有一个 script 脚本（整段代码）
2. 全局上下文（script 标签）被推入调用栈，同步代码执行。在执行的过程中，通过对一些接口的调用，可以产生新的 micro-task 和 macro-task，他们会被推入各自的任务队列里。同步代码执行完成后，script 脚本被移出 macro 队列，这个过程本质上是`队列的 macro-task 的执行和出队过程`；（macro-task 出队时，任务是一个一个执行的）
3. 处理 micro-task，会逐个执行队列里的任务并出队（micro-task 出队时，任务是一队一队执行的）
4. `执行渲染操作，更新界面`
5. 开始下一轮Event Loop，执行宏任务中的异步代码

![Micro And Micro Task](macro.png)

