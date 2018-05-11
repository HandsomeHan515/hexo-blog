---
title: JS面向对象
date: 2018-05-11 09:31:41
tags: JavaScript
categories: JavaScript
---
# 几种创建对象的方向

```js
/**
 * JS 面向对象，ECMAScript中有两种属性： 数据属性和访问器属性。
 */

"use strict";

// 1. 对象字面量
var person = {
  name: 'HandsomeHan',
  age: 23,
  job: 'front-end-engineer',

  sayName: function () {
    console.log(this.name);
  }
}

Object.defineProperty(person, "name", {
  writable: false,
  value: 'Handsome'
})

person.name = 'han'

console.log(person);

var book = {
  _year: 2004,
  edition: 1
}

Object.defineProperty(book, 'year', {
  get: function () {
    return this._year;
  },
  set: function (value) {
    if (value > 2004) {
      this._year = value;
      this.edition += value - 2004;
    }
  }
});

book._year = 2006;
console.log(book.edition);

// 2. 工厂模式
function createPerson(name, age, job) {
  var o = new Object();
  o.name = name;
  o.age = age;
  o.job = job;
  o.sayName = function () {
    console.log(o.name);
  }
  return o;
}

var person1 = createPerson('han', 23, 'front-end-engineer');

// 3. 构造函数模式
function Person(name, age, job) {
  this.name = name;
  this.age = age;
  this.job = job;
  this.sayName = function () {
    console.log(this.name);
  }
}

var person2 = new Person('han', 23, 'front-end-engineer');
var person3 = new Person('zhang', 24, 'front-end-enginner');

console.log(person2.constructor);
console.log(person3.constructor);

// 4. 原型模式
function Person() { }

Person.prototype.name = 'han';
Person.prototype.age = 29;
Person.prototype.job = 'front-end-engineer';
Person.prototype.sayName = function () {
  console.log(this.name);
}

// 5. 组合使用构造函数模式和原型模式
function Person(name, age, job) {
  this.name = name;
  this.age = age;
  this.job = job;
}

Person.prototype = {
  constructor: Person,
  sayName: function () {
    console.log(this.name);
  }
}

// 6. 动态原型模式

function Person(name, age, job) {
  this.name = name;
  this.age = age;
  this.job = job;

  if (typeof this.sayName != 'function') {
    Person.prototype.sayName = function () {
      console.log(this.name);
    }
  }
}

// 7. 寄生构造函数模式
function Person(name, age, job) {
  var o = new Object();
  o.name = name;
  o.age = age;
  o.job = job;
  o.sayName = function () {
    console.log(o.name);
  }
  return o;
}

var person4 = new Person('han', 23, 'front-end-engineer');
```