---
title: less的使用
date: 2018-01-29 09:38:05
tags: Others
categories: Others
---

Less 将CSS赋予了动态语言的特性，如 变量、继承、运算、函数等。

## 变量

+ 变量允许我们单独定义一系列通用的样式，然后在需要的时候去调用。所以在做全局样式调整的时候我们可能只需要修改几行代码就可以了。

```less变量

@color: #4D926F;

#header {
  color: @color;
}
h2 {
  color: @color;
}

/* 生成的 CSS */

#header {
  color: #4D926F;
}
h2 {
  color: #4D926F;
}
```

## 混合

+ 混合可以将一个定义好的class A轻松的引入到另一个class B中，从而简单实现class B继承class A中的所有属性。我们还可以带参数地调用，就像使用函数一样。
+ 使用 @import 将其他文件中的设置样式导入

main.less

```main.less
@color: yellow;
@size: 30px;
@radius: 6px;
```

example.less

```less混合、函数的使用
@import 'main';

// 类似于函数，(@radius: @radius)，其中 key 为参数名称，value 是 main 中的 6px
.rounded-corners (@radius: @radius) {
  border-radius: @radius;
}

.title {
  color: @color;
  font-size: @size;
  width: 600px;
  margin: 0 auto;
  border: 1px solid #ccc;
  .rounded-corners;
}

.description {
  color: @color;
  width: 600px;
  margin: 20px auto;
  border: 1px solid #ccc;
  /* 可修改 参数的值 */
  .rounded-corners(10px);
}

/* 生成的 CSS */

.title {
  color: @color;
  font-size: @size;
  width: 600px;
  margin: 0 auto;
  border: 1px solid #ccc;
  border-radius: 6px;
}

.description {
  color: @color;
  width: 600px;
  margin: 20px auto;
  border: 1px solid #ccc;
  border-radius: 10px;
}

```

## 嵌套规则

+ 我们可以在一个选择器中嵌套另一个选择器来实现继承，这样很大程度减少了代码量，并且代码看起来更加的清晰。

```less
#header {
  h1 {
    font-size: 26px;
    font-weight: bold;
  }
  p { font-size: 12px;
    a { text-decoration: none;
      &:hover { border-width: 1px }
    }
  }
}
```

```css
/* 生成的 CSS */

#header h1 {
  font-size: 26px;
  font-weight: bold;
}
#header p {
  font-size: 12px;
}
#header p a {
  text-decoration: none;
}
#header p a:hover {
  border-width: 1px;
}
```

## 函数&运算

+ 运算提供了加，减，乘，除操作；我们可以做属性值和颜色的运算，这样就可以实现属性值之间的复杂关系。LESS中的函数一一映射了JavaScript代码，如果你愿意的话可以操作属性值。

```less
@the-border: 1px;
@base-color: #111;
@red:        #842210;

#header {
  color: @base-color * 3;
  border-left: @the-border;
  border-right: @the-border * 2;
}

#footer {
  color: @base-color + #003300;
  border-color: desaturate(@red, 10%);
}
```

```css
/* 生成的 CSS */

#header {
  color: #333;
  border-left: 1px;
  border-right: 2px;
}
#footer { 
  color: #114411;
  border-color: #7d2717;
}
```

## @arguments 变量

+ @arguments包含了所有传递进来的参数.

```less
.box-shadow (@x: 0, @y: 0, @blur: 1px, @color: #000) {
  box-shadow: @arguments;
  -moz-box-shadow: @arguments;
  -webkit-box-shadow: @arguments;
}
.box-shadow(2px, 5px);
```

```css
box-shadow: 2px 5px 1px #000;
-moz-box-shadow: 2px 5px 1px #000;
-webkit-box-shadow: 2px 5px 1px #000;
```