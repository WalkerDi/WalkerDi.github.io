---
layout: post
title:  "Nginx+ssl配置http和https共存"
categories: Nginx
tags:  Nginx
---

* content
{:toc}

## Nginx+ssl配置http和https共存

nginx 支持https 访问，前提是要开启nginx的ssl模块，若没有安装则需要重新编译，编译安装时带上--with-http_ssl_module配置就可以了，另外证书在阿里云里申请免费Https证书SSL，在阿里云控制台：安全（云盾）->证书服务->购买证书里（地址：[https://common-buy.aliyun.com/?spm=5176.2020520163.cas.1.zTLyhO&commodityCode=cas#/buy](https://common-buy.aliyun.com/?spm=5176.2020520163.cas.1.zTLyhO&commodityCode=cas#/buy)）  选择免费的证书类型完成购买即可。





```
server {
        listen       80; #监听80端口，作为默认网站，即使用ip访问时默认出现的站点
	    listen	     443 ssl; #同时监听443端口为ssl
        server_name  apitest.dudubashi.com; #站点域名
        root    /alidata/www/apitest.dudubashi.com;#站点根目录

        #配置证书段，证书放在/alidata/server/nginx/cert安装目录的sslkey目录下	
        ssl on;
        ssl_certificate /alidata/server/nginx/cert/apitest.dudubashi.com.pem; #下载申请后阿里ssh提供的pem
        ssl_certificate_key /alidata/server/nginx/cert/apitest.dudubashi.com.key; #下载申请后阿里ssh提供的key
        ssl_session_timeout 5m;
        ssl_ciphers ECDHE-RSA-AES128-GCM-SHA256:ECDHE:ECDH:AES:HIGH:!NULL:!aNULL:!MD5:!ADH:!RC4;
        ssl_protocols TLSv1 TLSv1.1 TLSv1.2;
        ssl_prefer_server_ciphers on;

        location / {
            index index.php index.html index.htm;
            try_files $uri $uri/ /index.php?$query_string;
        }

       
        location ~ \.php$ {
            #fastcgi_pass   unix:/tmp/php-cgi.sock;
            fastcgi_pass   127.0.0.1:9000;
            fastcgi_index  index.php;
            include        fastcgi_params;
            fastcgi_param SCRIPT_FILENAME $document_root$fastcgi_script_name;
            fastcgi_param SERVER_NAME $http_host;
            fastcgi_ignore_client_abort on;
        }

        location ~ \.(swf|js|css|xml|gif|jpg|jpeg|png|bmp)$ {
            expires      max;
        }

        location /nginxstatus {
            stub_status on;
        }
   	
	access_log /var/log/nginx/apitest_access.log;
    	error_log /var/log/nginx/apitest_error.log;
    }

```

参考地址：

[https://www.cnblogs.com/jingxiaoniu/p/6745254.html](https://www.cnblogs.com/jingxiaoniu/p/6745254.html)
[https://www.cnblogs.com/lxf1117/p/6650647.html](https://www.cnblogs.com/lxf1117/p/6650647.html)

