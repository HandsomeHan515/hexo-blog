---
title: gulp
date: 2018-05-07 09:44:55
tags: JavaScript
categories: JavaScript
---
# gulp 操作

## 安装

```install
npm install --global gulp-cli

// 项目中使用
npm install gulp --save-dev
```

## 压缩 JS

```install
npm install gulp-uglify --save-dev
```

### /js/test.js

```js/test.js
function formatDate(date) {
  return new Date().toLocaleString();
}

function year() {
  return new Date().getFullYear();
}

function month() {
  return new Date().getMonth();
}

function day() {
  return new Date().getDay();
}
```

### gulpfile.js

```gulpfile.js
var gulp = require('gulp');
var uglify = require('gulp-uglify');

gulp.task('script', function () {
  gulp.src('js/*.js')
    .pipe(uglify())
    .pipe(gulp.dest('dist/js'))
});

gulp.task('auto', function () {
  gulp.watch('js/*.js', ['script']);
});

gulp.task('default', ['script', 'auto']);
```

### 压缩完成 /dist/test.js

```test.js
function formatDate(t){return(new Date).toLocaleString()}function year(){return(new Date).getFullYear()}function month(){return(new Date).getMonth()}function day(){return(new Date).getDay()}
```