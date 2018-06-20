---
layout: post
title:  "微信网页授权"
categories: 网页授权 微信
tags:  微信 网页授权 
---

* content
{:toc}

本文讲解如何进行微信网页授。

参考地址：[https://mp.weixin.qq.com/wiki?t=resource/res_main&id=mp1445241432](https://mp.weixin.qq.com/wiki?t=resource/res_main&id=mp1445241432)

示例代码如下：





```php

**
 * 微信网页授权
 * Created by PhpStorm.
 * author: dichuanjun
 * Date: 2018/1/24
 * Time: 15:44
 */
require_once "Wxjs_sdk/Wxjs.php";
class Weixin
{

    public static $app_id = 'xxxx'; //公众号应用appid
    public static $app_secret = 'xxxxx'; //公众号授权秘钥,可在公众号中设置
    public static $response_type = 'code';
    public static $scope = "snsapi_userinfo";
    public static $redirect_uri = '';
    public static $state = '';
    public static $last_time = '';

    public function __construct()
    {
        self::$last_time = date('Y-m-d', time());
        self::$redirect_uri = urlencode("http://".$_SERVER['HTTP_HOST']."/api/user/weixin_login");
      
    }
    

/**
     * 获取微信授权code
     * @return mixed
     */
    public function get_Wexin_code(){
        $app_id=self::$app_id;
        $redirect_uri=self::$redirect_uri;
        $scope=self::$scope;
        $url="https://open.weixin.qq.com/connect/qrconnect?appid={$app_id}&redirect_uri={$redirect_uri}&response_type=code&scope={$scope}&state=STATE#wechat_redirect";
        $res=$this->_http_get($url);
        return $res;

    }

    /**
     * 获取用户信息
     * @param $CODE
     * @return mixed
     */
    public function get_user_info($CODE){
        $this->get_Wexin_code();
        $token_data=self::auth_token($CODE);
        if(!isset($token_data['access_token'])){
            return false;
        }
        $url="https://api.weixin.qq.com/sns/userinfo?access_token={$token_data['access_token']}&openid={$token_data['openid']}";
        $res=$this->_http_get($url);
      //   print_r($token_data);exit;
        $data=json_decode($res,true);
        if(isset($data['errcode'])){
            return false;
        }else{
            return $data;
        }

    }

    /**
     * 检验授权凭证（access_token）是否有效
     * @param $CODE
     * @return mixed
     */
    private function auth_token($CODE){
        $token_json=self::get_refresh_token($CODE);
        $token_data=json_decode($token_json,true);
        if(!isset($token_data['access_token'])){
            return false;
        }
        $url="https://api.weixin.qq.com/sns/auth?access_token={$token_data['access_token']}&openid={$token_data['openid']}";
        $res=$this->_http_get($url);
        $data=json_decode($res,true);
        if($data['errcode']==0){
            return $token_data;
        }else{
            return false;
        }

    }

    /**
     * 刷新或续期 access_token
     * @param $CODE
     * @return mixed
     */
    private function get_refresh_token($CODE){
        $token_json=self::get_access_token($CODE);
        $token_data=json_decode($token_json,true);
        if(!isset($token_data['access_token'])){
            return false;
        }
        $app_id=self::$app_id;
        $url="https://api.weixin.qq.com/sns/oauth2/refresh_token?appid={$app_id}&grant_type=refresh_token&refresh_token={$token_data['refresh_token']}";
        $res=$this->_http_get($url);
        return $res;
    }

    /**
     * 通过code获取access_token
     * @param $CODE
     * @return mixed
     */
    private function get_access_token($CODE){
        $app_id=self::$app_id;
        $app_secret=self::$app_secret;
        $url="https://api.weixin.qq.com/sns/oauth2/access_token?appid={$app_id}&secret={$app_secret}&code={$CODE}&grant_type=authorization_code";
        $res=$this->_http_get($url);
        return $res;
    }

    private function _http_get($url) {
        $ch = curl_init();
        curl_setopt($ch, CURLOPT_URL, $url);
        curl_setopt($ch, CURLOPT_RETURNTRANSFER, 1);
        curl_setopt($ch, CURLOPT_HEADER, 0);
        $result = curl_exec($ch);
        curl_close($ch);

        return $result;
    }

}
```