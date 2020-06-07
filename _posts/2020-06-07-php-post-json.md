---
layout: post
title:  "PHP用CURL发送Content-type为application/json的POST请求方法"
categories: PHP
tags:  PHP  
---

* content
{:toc}

## PHP用CURL发送Content-type为application/json的POST请求方法

```php

/**
 * PHP发送Json对象数据
 *
 * @param $url 请求url
 * @param $data 发送的json字符串
 * @return array
 */
public function json_post($url, $data = NULL)
    {

        $curl = curl_init();

        curl_setopt($curl, CURLOPT_URL, $url);
        curl_setopt($curl, CURLOPT_SSL_VERIFYPEER, false);
        curl_setopt($curl, CURLOPT_SSL_VERIFYHOST, false);
        if(!$data){
            return 'data is null';
        }
        if(is_array($data))
        {
            $data = json_encode($data);
        }
        curl_setopt($curl, CURLOPT_POST, 1);
        curl_setopt($curl, CURLOPT_POSTFIELDS, $data);
        curl_setopt($curl, CURLOPT_HEADER, 0);
        curl_setopt($curl, CURLOPT_HTTPHEADER,array(
                'Content-Type: application/json; charset=utf-8',
                'Content-Length:' . strlen($data),
                'Cache-Control: no-cache',
                'Pragma: no-cache'
        ));
        curl_setopt($curl, CURLOPT_RETURNTRANSFER, 1);
        $res = curl_exec($curl);
        $errorno = curl_errno($curl);
        if ($errorno) {
            return $errorno;
        }
        curl_close($curl);
        return $res;

    }

```
## PHP接受JSON POST

```php

$data = json_decode(file_get_contents('php://input'), true);


```

