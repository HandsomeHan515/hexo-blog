---
title: 项目打包、上传及nginx配置
date: 2017-11-30 18:16:29
tags: Others
categories: Others
---

## 项目打包

1. 将项目克隆到本地
```
git clone http://example.com:10080/lizhongrenren/admin.git
```
2. 进入到admin文件，执行npm install，安装项目的所需的依赖包
3. 在admin文件下，执行npm run build，文件进行打包


## 上传
1. 打包完成后，进入到build文件 ...../admin/build/，将文件上传到服务器
```
scp -r ./* root@example.com:/src/data/zyr
// ":"后面是服务器上的路径
// 输入服务器密码，执行上传命令
```

## nginx配置
1. cent_os 安装nginx
2. /etc/nginx/nginx.conf # Nginx配置文件位置(ubuntu默认位置)
3. 配置
```
// nginx.conf 文件下

http {
        ##
        # Basic Settings
        ##
        sendfile on;
        tcp_nopush on;
        tcp_nodelay on;
        keepalive_timeout 65;
        types_hash_max_size 2048;
        # server_tokens off;
        # server_names_hash_bucket_size 64;
        # server_name_in_redirect off;
        include /etc/nginx/mime.types;
        default_type application/octet-stream;
        ##
        ssl_protocols TLSv1 TLSv1.1 TLSv1.2; # Dropping SSLv3, ref: POODLE
        ssl_prefer_server_ciphers on;
        ##
        # Logging Settings
        ##
        access_log /var/log/nginx/access.log;
        error_log /var/log/nginx/error.log;
        ##
        # Gzip Settings
        ##
        gzip on;
        gzip_disable "msie6";
        # gzip_vary on;
        # gzip_proxied any;
        # gzip_comp_level 6;
        # gzip_buffers 16 8k;
        # gzip_http_version 1.1;
        ##
        # Virtual Host Configs
        ##
        include /etc/nginx/conf.d/*.conf;
        include /etc/nginx/sites-enabled/*;
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
4. 配置后，重启nginx即可