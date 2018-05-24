---
title: JavaScript从原型到原型链
date: 2018-05-24 09:44:04
tags: JavaScript
categories: JavaScript
---
# JavaScript 系列 从原型到原型链

## 使用构建函数创建一个对象

```JavaScript
// Person 就是一个构造函数，我们使用 new 创建了一个实例对象 person
function Person() {};
var person = new Person();
person.name = 'handsome';
console.log(person.name);
```

## prototype

> prototype 是函数才会有的属性

```prototype
function Person() {};
Person.prototype.name = 'handsome';
var p1 = new Person();
var p2 = new Person();
console.log('p1: %o, p2: %o', p1.name, p2.name);
```

> 函数的 prototype 属性指向了一个对象，这个对象是调用该构造函数而创建的实例的原型，也就是 p1, p2 的原型

```__proto__
person.__proto__ === Person.prototype  // true
```

![prototype](prototype.png)

## constructor

```constructor
Person === Person.prototype.constructor // true
```

![prototype3](prototype3.png)

```es5
// ES5的方法, 获得对象的原型
Object.getPrototypeOf(person) === Person.prototype // true
```

## 实例与原型

```code
function Person() {}

Person.prototype.name = 'handsome';

var person = new Person();

person.name = 'han';
console.log(person.name) // han

delete person.name;
console.log(person.name) // handsome
```

> 删除了 person 的 name 属性时，读取 person.name，从 person 对象中找不到 name 属性就会从 person 的原型也就是 person.__proto__ ，也就是 Person.prototype中查找，结果为 handsome

## 原型的原型

![prototype4](prototype4.png)

## 原型链

![prototype5](prototype5.png)