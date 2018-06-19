---
layout: post
title:  "lnmp 环境中相关服务启动命令"
categories: lnmp
tags:  Linux Nginx Msql PHP  
---

* content
{:toc}

## php 服务关闭和重启

```
killall php-fpm  //php 关闭命令
/alidata/server/php-5.6.23/sbin/php-fpm //php启动命令
```
## Nginx服务重启

```
/alidata/server/nginx-1.8.1/sbin/nginx -t  //启动nginx,并检测是否有语法错误
/alidata/server/nginx-1.8.1/sbin/nginx -s reload //重新加载配置，使修改生效
```

## Mysql服务重启

```
service mysqld restart //使用 service 启动
/etc/inint.d/mysqld restart //使用 mysqld 脚本启动
```



