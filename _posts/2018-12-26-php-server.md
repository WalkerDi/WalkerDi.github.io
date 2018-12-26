---
layout: post
title:  "$_SERVER 函数详解"
categories: PHP
tags:  PHP
---

* content
{:toc}

## $_SERVER 函数详解
### 实例：
```
1、http://localhost/index.php/Home/H1/index.html 
```
结果：
```php
$_SERVER['QUERY_STRING'] = "";
$_SERVER['REQUEST_URI']  = "/index.php/Home/H1/index.html";
 $_SERVER['SCRIPT_NAME'] = "/index.php";
 $_SERVER['PHP_SELF'] = "/index.php/Home/H1/index.html";
```

```
2、http://localhost/index.php/Home/H1/index.html?key=test (附带查询)
```
结果：
```php
$_SERVER['QUERY_STRING'] = "key=test";
$_SERVER['REQUEST_URI']  = "/index.php/Home/H1/index.html?key=test"; 
$_SERVER['SCRIPT_NAME'] = "/index.php"; 
$_SERVER['PHP_SELF'] = "/index.php/Home/H1/index.html";
``` 

由实例可知：
```
$_SERVER["QUERY_STRING"]  获取查询 语句，实例中可知，获取的是?后面的值
$_SERVER["REQUEST_URI"]   获取 http://localhost 后面的值，包括/
$_SERVER["SCRIPT_NAME"]   获取当前脚本的路径，如：index.php
$_SERVER["PHP_SELF"]      当前正在执行脚本的文件名
```