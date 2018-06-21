---
layout: post
title:  "Centos6.8 企业项目禅道的安装"
categories: 禅道 项目管理 
tags:  Linux 禅道 
---

* content
{:toc}

## 禅道介绍
禅道项目管理软件 是国产的开源项目管理软件,专注研发项目管理,内置需求管理、任务管理、bug管理、缺陷管理、用例管理、计划发布等功能,实现了软件的完整生命周期管理。
禅道属于开源项目，有收费版，本次以开源版9.8.3进行搭建。禅道官网：[http://www.zentao.net/](http://www.zentao.net/)

## 环境准备

在服务器上面搭建lnmp架构，此步骤省略，可以参考网址：[https://www.cnblogs.com/xiaoit/p/3991037.html](https://www.cnblogs.com/xiaoit/p/3991037.html)

## 搭建步骤

1.创建一个存放禅道的数据目录：`mkdir /alidata/www/zentao`

2.解压安装包： `unzip ZenTaoPMS.9.8.3.zip -d /alidata/www/zentao/` 解压后的目录：

```
bin  config  db  doc  framework  lib  module  tmp  VERSION  www
```





3.nginx配置访问host,域名指向`/alidata/www/zentao/zentaopms/www/`配置如下；

```
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

4.浏览器访问 http://pms.ibearcar.com/install.php 根据页面提示安装禅道。


5.配置数据库

可在`/alidata/www/zentao/zentaopms/www/config/my.php`文件修改数据库配置。
```php
<?php
$config->installed       = true;
$config->debug           = false;
$config->requestType     = 'GET';
$config->db->host        = '127.0.0.1';//数据库访问host
$config->db->port        = '3306'; //端口号
$config->db->name        = 'zentao';//数据库名称
$config->db->user        = 'db_name';//用户名
$config->db->password    = '#2018P';//密码
$config->db->prefix      = 'zt_';
$config->webRoot         = getWebRoot();
$config->default->lang   = 'zh-cn';

```

## 安装过程遇到的问题
1.安装过程中提示一下信息：

```
您访问的域名 *.*.*.* 没有对应的公司？
```

解决方法：

检查php.ini文件中关于session.save_path的设置。要确保目录存在且可读可写。然后删除my.php，重新安装，记得清空现有数据.

2.生成配置文件界面就卡住无响应

解决方法：

内存不够用，修改php.ini 中memory_limit 参数的值 改成128M以上，重启服务后重新安装禅道。



