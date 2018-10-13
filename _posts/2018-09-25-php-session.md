---
layout: post
title:  "PHP 如何设置一个严格控制过期时间的session"
categories: PHP session 
tags:  session 
---

* content
{:toc}

## PHP 如何设置一个严格控制过期时间的session

### 1.PHP session 有效期

PHP的session有效期默认是1440秒(24分钟),如果客户端超过24分钟没有刷新，当前session会被回,失效。当用户关闭浏览器，会话结束，session也会失效。可以修改php.ini的session.gc_maxlifetime来设置session的生命周期，但并不能保证在超过这一时间后session信息立即会删除。因为GC是按机率启动的，可能在某一个长时间内都没有被启动。那么大量的session在超过session.gc_maxlifetime后仍然有效。





### 2.session.gc_maxlifetime,session.gc_probability,session.gc_divisor说明

session.gc_maxlifetime=30表示当session文件在30秒后没有被访问，则视为过期session，等待GC回收。GC进程调用的概率是通过session.gc_probability/session.gc_divisor计算得来的，而session.gc_divisor默认是1000，如果session.gc_probability = 1000那么GC进程在每次执行session_start()时都会调用，执行回收。把session.gc_probability/session.gc_divisor的机率提高，会有帮助，但会对性能造成严重影响。

### 3.严格控制session过期方法

(1)使用memcache/Redis来保存session，设置过期时间，因为memcache/redis的回收机制不是按机率的，可以确保session过期后失效。

(2)只使用php实现，创建一个session类，在session写入时，把过期时间也写入。读取时，根据过期时间判断是否已过期。


```php
<?php
/**
 * session 控制类
 */
class Session{
    /**
     *设置session
     * @param string $name session name
     * @param mixed $data session data
     * @param int $expire 过期时间(秒)
     */
    public static function set($name,$data,$expire=60){
        $session_data=array();
        $session_data['data'] = $data;
        $session_data['expire'] = time() + $expire;
        $_SESSION[$name] = $data;
    }

    /**
     * 读取session
     * @param string $name session name
     * @return mixed
     */
    public function get($name){
       if(isset($_SESSION[$name])){
           if($_SESSION[$name]['expire'] > time()){
               return $_SESSION[$name]['data'];
           }else{
               self::clear($name);
           }
       }else{
          return false;
       }
    }
    /**
     * 清除session
     * @param string $name session name
     */
    public function clear($name){
        unset($_SESSION[$name]);
    }
	
}
```


