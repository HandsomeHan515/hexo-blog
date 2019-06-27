---
title: rss
date: 2019-06-26 11:27:56
tags: Others
categories: Others
---

### 安装插件

```code
yarn add hexo-generator-feed (or npm install hexo-generator-feed --save)

```

### _config.yml 配置

```code
plugins: 
    hexo-generator-feed

feed:
    type: atom
    path: atom.xml
    limit: 10
```
