---
layout: post
title:  "微信分享获取签名"
categories: 微信分享 
tags:  CodeIgniter wechat 
---

* content
{:toc}

该类为微信分享获取签名包，`getSignPackage()`方法返回分享链接的签名包，这里以CI 框架为例进行说明。

## 微信分享获取签名

具体代码如下：




```php
   <?php
   
   class Wxjs {
     private $appId; //公众号appid
     private $appSecret; //公众号appSecret
     private $protocol; //分享链接
     protected $CI;
     public static $wx_access_token_key = "ibearcar_wx_access_token_key";
   
     public function __construct($appId, $appSecret,$protocol) {
       $this->appId = $appId;
       $this->appSecret = $appSecret;
       $this->protocol = $protocol;
       $this->CI = & get_instance();
       $this->CI->load->driver('cache', array('adapter' => 'redis')); //启用redis
     }
   
     public function getSignPackage() {
   
       $jsapiTicket = $this->getJsApiTicket();
      // echo $jsapiTicket;die;
   
   
       // 注意 URL 一定要动态获取，不能 hardcode.
      // $protocol = (!empty($_SERVER['HTTPS']) && $_SERVER['HTTPS'] !== 'off' || $_SERVER['SERVER_PORT'] == 443) ? "https://" : "http://";
       //$url = "$protocol$_SERVER[HTTP_HOST]$_SERVER[REQUEST_URI]";
       $url = $this->protocol;
   
       $timestamp = time();
       $nonceStr = $this->createNonceStr();
     //  echo $nonceStr;die;
   
       // 这里参数的顺序要按照 key 值 ASCII 码升序排序
       $string = "jsapi_ticket=$jsapiTicket&noncestr=$nonceStr&timestamp=$timestamp&url=$url";
   
       $signature = sha1($string);
   
       $signPackage = array(
         "appId"     => $this->appId,
         "nonceStr"  => $nonceStr,
         "timestamp" => $timestamp,
         "url"       => $url,
         "signature" => $signature,
         "rawString" => $string,
       );
       return $signPackage; 
     }
   
     private function createNonceStr($length = 16) {
       $chars = "abcdefghijklmnopqrstuvwxyzABCDEFGHIJKLMNOPQRSTUVWXYZ0123456789";
       $str = "";
       for ($i = 0; $i < $length; $i++) {
         $str .= substr($chars, mt_rand(0, strlen($chars) - 1), 1);
       }
       return $str;
     }
   
     private function getJsApiTicket() {
         $accessToken = $this->getAccessToken();
         // 如果是企业号用以下 URL 获取 ticket
         // $url = "https://qyapi.weixin.qq.com/cgi-bin/get_jsapi_ticket?access_token=$accessToken";
         $url = "https://api.weixin.qq.com/cgi-bin/ticket/getticket?type=jsapi&access_token=$accessToken";
         $res = json_decode($this->httpGet($url));
         $ticket = $res->ticket;
   
       return $ticket;
     }
   
       public function getAccessToken() {
           // access_token 应该全局存储与更新，这里保存到redis,也可以以其他形式保存
           //$this->CI->cache->delete(self::$wx_access_token_key);
           $access_token = $this->CI->cache->get(self::$wx_access_token_key);
           if(!empty($access_token)){
               //验证token 是否有效
               if(!$this->get_wx_ip($access_token)){
                   $access_token='';
               }
           }
           if(empty($access_token)){
   
               // $url = "https://qyapi.weixin.qq.com/cgi-bin/gettoken?corpid=$this->appId&corpsecret=$this->appSecret";
               $url = "https://api.weixin.qq.com/cgi-bin/token?grant_type=client_credential&appid=$this->appId&secret=$this->appSecret";
               $res = json_decode($this->httpGet($url));
               $access_token = $res->access_token;
               $this->CI->cache->delete(self::$wx_access_token_key);
               $this->CI->cache->save(self::$wx_access_token_key, $access_token, 7000);
              
           }
   
           return $access_token;
       }
   
       /**
        * 获取微信ip 列表
        * @param $access_token
        * @return bool
        */
       public function get_wx_ip($access_token){
           $url = "https://api.weixin.qq.com/cgi-bin/getcallbackip?access_token={$access_token}";
           $res = json_decode($this->httpGet($url),JSON_UNESCAPED_UNICODE);
           if(isset($res['errcode'])){
               return false;
           }
           return true;
   
       }
   
     private function httpGet($url) {
   
         $ch = curl_init();
         curl_setopt($ch, CURLOPT_URL, $url);
         curl_setopt($ch, CURLOPT_RETURNTRANSFER, 1);
         curl_setopt($ch, CURLOPT_HEADER, 0);
         $result = curl_exec($ch);
         curl_close($ch);
   
         return $result;
     }
   
     private function get_php_file($filename) {
       return trim(substr(file_get_contents($filename), 15));
     }
     private function set_php_file($filename, $content) {
       $fp = fopen($filename, "w+");
       fwrite($fp, "<?php exit();?>" . $content);
       fclose($fp);
     }
   }
   

```