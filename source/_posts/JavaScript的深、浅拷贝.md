---
title: JavaScript的深、浅拷贝
date: 2018-05-24 14:15:38
tags: JavaScript
categories: JavaScript
---
# JavaScript的深、浅拷贝

## 数组的浅拷贝

> 数组嵌套了数组或者对象，无论是新数组还是旧数组都发生了变化，也就是说使用 concat slice 方法，克隆的并不彻底。
> 如果数组元素是基本类型，就会拷贝一份，互不影响，而如果是对象或者数组，就会只拷贝对象和数组的引用，这样我们无论在新旧数组进行了修改，两者都会发生变化。

```js
var arr = [1, 2, 3, 4];
var obj = { a: 1, b: 2 };

var arr2 = arr.slice(); // slice实现数组的浅拷贝
var obj2 = obj;
arr2[0] = 10;
arr[1] = 20;
obj2.a = 10;
console.log('arr: %o, arr1: %o', arr, arr2); // arr: [1, 20, 3, 4] arr2: [10, 2, 3, 4]
console.log('obj: %o, obj2: %o', obj, obj2); // obj: {a: 10, b: 2} obj2: {a: 10, b: 2}

var array = [{ a: 1, b: 2, c: 3 }, { a: 10, b: 20, c: 30 }];
var array2 = array.slice();

array2[0].a = 10;
console.log('array: %o, array2: %o', array, array2); // array: [{a: 10, b: 2, c: 3}, {a: 10, b: 20, c: 30}] array2: [{a: 10, b: 2, c: 3}, {a: 10, b: 20, c: 30}]
```

## 数组的深拷贝

> 深拷贝就是指完全的拷贝一个对象，即使嵌套了对象，两者也相互分离，修改一个对象的属性，也不会影响另一个。

```js
// **不能拷贝函数**
var arr = [{ a: [1, 2, 3], b: 2, c: 3 }, { a: 10, b: 20, c: 30 }];
var arr2 = JSON.parse(JSON.stringify(arr));
arr2[0].a[0] = 10;
console.log('arr: %o, arr2: %o', arr, arr2);
```

## 浅拷贝的实现

```js
function shallow(obj) {
  if (typeof obj !== 'object' || typeof obj === null) return obj;
  // 根据 obj 的类型判断是新建一个数组还是新建一个对象
  var newObj = obj instanceof Array ? [] : {};
  // 遍历 obj, 判断是 obj 的属性，然后进行拷贝
  for (var key in obj) {
    if (obj, hasOwnProperty(key)) {
      newObj[key] = obj[key];
    }
  }
  return newObj;
}
```

## 深拷贝的实现

```js
function deep(obj) {
  if (typeof obj !== 'object' || typeof obj === null) return obj;
  var newObj = obj instanceof Array ? [] : {};
  for (var key in obj) {
    if (obj.hasOwnProperty(key)) {
      // 判断 value 是否是 object 类型，若是递归调用拷贝函数
      newObj[key] = typeof obj[key] === 'object' ? deep(obj[key]) : obj[key];
    }
  }
  return newObj;
}
```