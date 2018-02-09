---
title: 项目打包、上传及nginx配置
date: 2017-11-30 18:16:29
tags: Others
categories: Others
---
# 项目打包、上传及nginx配置

## 项目打包

1. 将项目克隆到本地，**git clone http://example.com:10080/lizhongrenren/admin.git**
2. 进入到admin文件，执行npm install，安装项目的所需的依赖包
3. 在admin文件下，执行npm run build，文件进行打包

## 上传

1. 打包完成后，进入到build文件 ...../admin/build/，将文件上传到服务器， **scp -r ./* root@example.com:/src/data/admin**

## nginx配置

1. /etc/nginx/nginx.conf # Nginx配置文件位置(ubuntu默认位置)
2. 配置nginx， 配置后，重启nginx即可

```code
http {
    server {
        listen          80;
        server_name     101.200.216.121 admin.zyrwork.com;
        root            /src/data/zyr; # admin文件路径
        location / {
                index   index.html;
        }
    }
}
```
