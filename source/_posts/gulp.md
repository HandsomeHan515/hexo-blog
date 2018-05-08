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

### gulpfile js

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

## 压缩 CSS

### css/test.css

```test.css
* {
  padding: 0;
  margin: 0;
}

div {
  padding: 0 20px;
}
```

### gulpfile css

```gulpfile.js
var cleanCss = require('gulp-clean-css');

gulp.task('css', function () {
  gulp.src('css/*.css')
    .pipe(cleanCss())
    .pipe(gulp.dest('dist/css'))
});

gulp.task('auto', function () {
  gulp.watch('css/*.css', ['css']);
});

gulp.task('default', ['css', 'auto']);
```

### /dist/test.css

```test.css
*{padding:0;margin:0}div{padding:0 20px}
```

## 压缩图片

### gulpfile css

```gulpfile.js
var imagemin = require('gulp-imagemin');

gulp.task('images', function () {
  gulp.src('images/*.*')
    .pipe(imagemin({
      progressive: true
    }))
    .pipe(gulp.dest('dist/images'))
});

gulp.task('auto', function () {
  gulp.watch('images/*.*', ['images']);
});

gulp.task('default', ['images', 'auto']);
```

## 编译less

### gulpfile less

```gulpfile.js
var less = require('gulp-less');

// 编译成css并且压缩
gulp.task('less', function () {
  gulp.src('less/*.less')
    .pipe(less())
    .pipe(cleanCss())
    .pipe(gulp.dest('dist/css'))
});

gulp.task('auto', function () {
  gulp.watch('less/*.less', ['less']);
});

gulp.task('default', ['less', 'auto']);
```
