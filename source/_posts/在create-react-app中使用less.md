---
title: 在create-react-app中使用less
date: 2018-01-29 10:33:20
tags: Others
categories: Others
---

## 创建项目添加开发依赖项

```创建项目添加开发依赖项
create-react-app myapp

cd myapp

npm run eject or yarn run eject

npm install less less-loader --save-dev or yarn add less less-loader --dev
```

## 配置

+ webpack.config.dev.js和webpack.config.prod.js的配置如下

![webpack配置](less.png)
