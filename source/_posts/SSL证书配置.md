---
title: SSL证书配置
date: 2018-12-26 10:48:54
tags: Others
categories: Others
---

# SSL申请
腾讯云免费SSL申请
[https://console.cloud.tencent.com/ssl](https://console.cloud.tencent.com/ssl/detail/QGTI9GML)
# Nginx 证书部署
## 获取证书
申请证书后下载获取并解压文件获取内容如下：

![图片](1.png)

Nginx 文件夹内获得 SSL 证书文件 1_www.domain.com_bundle.crt 和私钥文件 2_www.domain.com.key。
1_www.domain.com_bundle.crt 文件包括两段证书代码 “-----BEGIN CERTIFICATE-----” 和 “-----END CERTIFICATE-----”,
2_www.domain.com.key 文件包括一段私钥代码 “-----BEGIN RSA PRIVATE KEY-----” 和 “-----END RSA PRIVATE KEY-----”
## 证书安装
将域名 [www.domain.com](http://www.domain.com) 的证书文件 1_www.domain.com_bundle.crt 、私钥文件 2_www.domain.com.key 保存到同一个目录
### 本地使用 scp 命令上传：
![图片](2.png)
### 服务器相应目录下：
![图片](3.png)
### Nginx 配置
```
server {
                listen 443;
                server_name www.domain.com;
                ssl on;
                ssl_certificate 1_www.domain.com_bundle.crt;
                ssl_certificate_key 2_www.domain.com.key;
                ssl_session_timeout 5m;
                ssl_protocols TLSv1 TLSv1.1 TLSv1.2;
                ssl_ciphers ECDHE-RSA-AES128-GCM-SHA256:HIGH:!aNULL:!MD5:!RC4:!DHE;
                ssl_prefer_server_ciphers on;

                client_max_body_size 4G;

                location /media  {
                         alias /data/venv/flower/service/media;  # your Django project's media files - amend as required
                }
                location /static {
                        alias /data/venv/flower/service/static; # your Django project's static files - amend as required
                }

                location / {
                        proxy_pass http://11.11.11.11:10013;
                        proxy_set_header Host $host;
                        proxy_set_header X-Real-IP $remote_addr;
                        proxy_set_header X-Forwarded-For $proxy_add_x_forwarded_for;
                        ### Most PHP, Python, Rails, Java App can use this header -> https ###
                        proxy_set_header X-Forwarded-Proto  $scheme;
                }
}
```

访问 https://www.domain.com 即访问  [http://11.11.11.11:10013:10013](http://11.11.11.11:10013:10013;)
![图片](4.png)

