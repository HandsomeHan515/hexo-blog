---
title: nginx配置文件目录
date: 2018-05-04 11:02:48
tags: Others
categories: Others
---

```nginx
server {
    listen       10014;
    server_name  127.0.0.1 localhost;
    location / {
        alias  /Users/handsomehan/Documents/;
        sendfile on;
        autoindex on;
        autoindex_exact_size off;
        autoindex_localtime on;
        charset utf-8,gbk;
    }
}
```