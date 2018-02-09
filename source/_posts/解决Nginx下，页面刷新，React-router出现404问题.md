---
title: 解决Nginx下，页面刷新，React-router出现404问题
date: 2018-01-08 15:14:21
tags: React
categories: JavaScript
---
# Nginx server 配置如下：

```code
server {
    listen 80;
    server_name app.handsomehan.cn;
    index index.html;
    root /data/web/flower/;
    location / {
            try_files $uri $uri /index.html;
    }
}
```
