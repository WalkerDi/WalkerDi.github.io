---
layout: post
title:  "CodeIgniter 中执行定时任务"
categories: CodeIgniter
tags:  CodeIgniter PHP Linux
---

* content
{:toc}

## 编辑定时任务

```
crontab –e
```
## 每分钟执行控制器中的方法

```
*/1 * * * * /alidata/server/php-5.6.23/bin/php /alidata/www/ttaoche_git/application/admin/command.php  api/Task/update_activity_status  run dev（运行开发环境）
*/1 * * * * /usr/bin/curl  http://admin.buy.test.weponycar.com/api/Task/update_activity_status
```

其中 dev (开发环境)、test(测试环境)、pro(真实环境)

## 重启crond 服务

```
service crond restar
```

## 查看任务执行情况

```
tail -f /var/log/cron
```


