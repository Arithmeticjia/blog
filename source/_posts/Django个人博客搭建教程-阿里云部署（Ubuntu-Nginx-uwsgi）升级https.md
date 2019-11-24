---
title: Django个人博客搭建教程---阿里云部署（Ubuntu+Nginx+uwsgi）升级https
date: 2019-11-24 16:20:40
tags: [django, 阿里云, nginx, https]
category: Django
---
更安全的https
<!-- more -->
## 一、阿里云申请SSL证书
略

## 二、将pem和key放在nginx目录下
仅供参考
```shell
/etc/nginx/cert/1831344_www.guanacossj.com.pem;      # 路径/pem文件
/etc/nginx/cert/1831344_www.guanacossj.com.key;      # 路径/key文件
```
三、修改nginx.conf
```shell
http {
 
 
  server {
      listen 80;
      server_name  _;
      return 301 https://www.guanacossj.com$request_uri;
   } 
 
  server {
      listen       443 default_server;
      listen       [::]:443 default_server;
      server_name  _;
      ssl on;
      ssl_certificate   /etc/nginx/cert/1831344_www.guanacossj.com.pem;     # 路径/pem文件
      ssl_certificate_key  /etc/nginx/cert/1831344_www.guanacossj.com.key;  # 路径/key文件
      ssl_session_timeout 5m;
      ssl_ciphers ECDHE-RSA-AES128-GCM-SHA256:ECDHE:ECDH:AES:HIGH:!NULL:!aNULL:!MD5:!ADH:!RC4;
      ssl_protocols TLSv1 TLSv1.1 TLSv1.2;
      ssl_prefer_server_ciphers on;
      charset     utf-8;
      client_max_body_size 1000M;   # adjust to taste
      include /etc/nginx/default.d/*.conf;
      location /static {
      alias /home/MyBlog/static; # ָÏdjangoµÄtaticĿ¼
      }
      location /static/rest_framework/ {
      alias /usr/local/lib/python3.6/dist-packages/rest_framework/static/rest_framework/
    ;}
 
    # Finally, send all non-media requests to the Django server.
    location / {
        uwsgi_pass  127.0.0.1:8000;
        #uwsgi_pass  django;
        include     uwsgi_params; 	# the uwsgi_params file you installed           
        proxy_redirect off;
	      proxy_http_version 1.1;
        proxy_set_header Upgrade $http_upgrade;
        proxy_set_header Connection "upgrade";
	      uwsgi_send_timeout 600;
        uwsgi_connect_timeout 600;  
        uwsgi_read_timeout 600; 
    }
}
```
这里把你原来的监听80端口改为监听443端口即可，我之前一直不成功主要是我试图再加一个443端口的监听，应该也可以吧，不管了，反正这样肯定没问题，然后如果还想让http能购访问，就做个重定向。

四、重启nginx
```shell
service nginx restart
```
