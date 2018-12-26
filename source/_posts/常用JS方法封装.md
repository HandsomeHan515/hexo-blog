---
title: 常用JS方法封装
date: 2018-12-24 16:25:49
tags: JavaScript
categories: JavaScript
---

# 常用JS方法封装

```js
// 去掉数组中某个值
function remove(arr, item) {
    if (arr.length && arr.indexOf(item) > -1) {
        return arr.splice(index, 1)
    }
}
```