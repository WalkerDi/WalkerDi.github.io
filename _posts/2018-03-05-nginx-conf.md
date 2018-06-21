---
layout: post
title:  Nginx配置
categories: Nginx
tags:  Nginx 
---

* content
{:toc}

这里记录Nginx主机一般配置，具体的配置要根据实际项目做相应的配置，这里仅供参数。

## 主配置文件nginx.conf

配置所在目录`/alidata/server/nginx-1.8.1/conf/nginx.conf`

```php
user  www www;
#根据服务器核数决定，几核就写几 
worker_processes  4; 
#worker_cpu_affinity 00000001 00000010 00000100 00001000 00010000 00100000 01000000 10000000;

error_log  logs/error.log crit;
#error_log  logs/error.log  notice;
#error_log  logs/error.log  info;

pid        logs/nginx.pid;

events {
	#使用的网络I/O模型,Linux推荐采用epoll模型,FreeBSD系统推荐采用kqueue模型
    use epoll;
	#允许连接的数量.
    worker_connections  45535;
}

#允许进程打开的文件数量
worker_rlimit_nofile 65535;


http {
    include       mime.types;
    default_type  application/octet-stream;
    server_tokens off;

    #log_format  main  '$remote_addr - $remote_user [$time_local] $request '
     #                 '$status $body_bytes_sent $http_referer '
     #                 '$http_user_agent $http_x_forwarded_for';
    log_format  main  '$remote_addr - $remote_user [$time_local] "$request" '
                      '$status $body_bytes_sent "$http_referer" '
                      '"$http_user_agent" '
                      '"$upstream_addr" "$cookie_session" "$server_name" $request_time $upstream_response_time $cookie_uid';
	
    server_names_hash_bucket_size 128;
    client_header_buffer_size 32k;
    large_client_header_buffers 4 32k;
    client_max_body_size 30m;

    sendfile          on;
    tcp_nopush        on;
    tcp_nodelay       on;

    keepalive_timeout 60;

    fastcgi_connect_timeout 300;
    fastcgi_send_timeout 1800;
    fastcgi_read_timeout 300;
    fastcgi_buffer_size 254k;
    fastcgi_buffers 16 256k;
    fastcgi_busy_buffers_size 512k;
    fastcgi_temp_file_write_size 512k;

    gzip              on;
    gzip_min_length   1k;
    gzip_buffers      4 16k;
    gzip_http_version 1.0;
    gzip_comp_level   2;
    gzip_types        text/plain application/x-javascript text/css application/xml text/javascript;
    gzip_vary         on;

    #charset      utf-8;

    access_log   /tmp/nginx_access.log;
    log_not_found off;

    error_page  400 403 405 408  /40x.html;
    error_page  500 502 503 504  /50x.html;

    #引入站点主机配置,conf.d/vhost.文件下开头的文件
    include conf.d/vhost.*; 
}

```





## 项目站点配置 


* 一般配置 
 
配置文件所在目录`/alidata/server/nginx-1.8.1/conf/conf.d/vhost.pms.ibearcar.com`

```php
server {
        listen       80;
        server_name  pms.ibearcar.com;
        root   /alidata/www/zentao/zentaopms/www/;
        location / {
            index index.php index.html;
            try_files $uri $uri/ /index.php?$query_string;
        }

        location ~ .*.php($|/) {
            fastcgi_pass   unix:/tmp/php-cgi.sock;
	    #fastcgi_pass   127.0.0.1:9000;
            fastcgi_index  index.php;
            include        fastcgi_params;
            #fastcgi_split_path_info ^(.+\.php)(.*)$;
            #fastcgi_param PATH_INFO $fastcgi_path_info;
            fastcgi_param SCRIPT_FILENAME $document_root$fastcgi_script_name;
            fastcgi_param SERVER_NAME $http_host;
            fastcgi_ignore_client_abort on;
        }
        location /nginxstatus {
            stub_status on;
        }
	#access_log /tmp/pms.ibearcar.com.access.conf;
	error_log /tmp/pms.ibearcar.com.conf;
    }
```

* 前后端分离配置

配置文件所在目录`/alidata/server/nginx-1.8.1/conf/conf.d/vhost.web.tttaoche.com.com`

```php

server {
        listen       80;
        server_name  web.tttaoche.com;
        #后端接口指向目录
        root   /alidata/www/tt_taoche_web/application/ponybuy/;
        location / {
            index index.php;
            try_files $uri $uri/ /index.php?$query_string;
        }

        location ~ .*.php($|/) {
            fastcgi_pass   unix:/tmp/php-cgi.sock;
	    #fastcgi_pass   127.0.0.1:9000;
            fastcgi_index  index.php;
            include        fastcgi_params;
            #fastcgi_split_path_info ^(.+\.php)(.*)$;
            #fastcgi_param PATH_INFO $fastcgi_path_info;
            fastcgi_param SCRIPT_FILENAME $document_root$fastcgi_script_name;
            fastcgi_param SERVER_NAME $http_host;
            fastcgi_ignore_client_abort on;
        }

        #前端静态页面指向目录
        location ~ \.(html|swf|js|css|xml|gif|jpg|jpeg|png|bmp)$ {
        root   /alidata/www/tt_taoche_web/application/ponybuy/build;
            expires      max;
        }

        location /nginxstatus {
            stub_status on;
        }
	#access_log /tmp/web.tttaoche.com.access.conf;
	error_log /tmp/web.tttaoche.com.conf;
    }

```
