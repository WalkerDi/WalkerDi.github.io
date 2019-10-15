---
layout: post
title:  "对称加密算法(DES/AES)加解密详解"
categories: PHP
tags:  PHP
---

* content
{:toc}

### 一、简介

   对称密钥加密机制即对称密码体系，也称为单钥密码体系和传统密码体系。对称密码体系通常分为两大类，一类是分组密码（如DES、AES算法），另一类是序列密码（如RC4算法）。

   AES 是一个新的可以用于保护电子数据的加密算法。明确地说，AES 是一个迭代的、对称密钥分组的密码，它可以使用128、192 和 256 位密钥，并且用 128 位（16字节）分组加密和解密数据。与公共密钥密码使用密钥对不同，
 对称密钥密码使用相同的密钥加密和解密数据。通过分组密码返回的加密数据的位数与输入数据相同。迭代加密使用一个循环结构，在该循环中重复置换（permutations ）和替换(substitutions）输入数据。Figure 1 显示了 AES 用192位密钥对一个16位字节数据块进行加密和解密的情形。

   那DES是什么呢？DES全称为Data Encryption Standard，即数据加密标准，是一种使用密钥加密的块算法，1977年被美国联邦政府的国家标准局确定为联邦资料处理标准（FIPS），并授权在非密级政府通信中使用，随后该算法
 在国际上广泛流传开来。需要注意的是，在某些文献中，作为算法的DES称为数据加密算法（Data Encryption Algorithm,DSA），已与作为标准的DES区分开来。

  DES设计中使用了分组密码设计的两个原则：混淆（confusion）和扩散(diffusion)，其目的是抗击敌手对密码系统的统计分析。混淆是使密文的统计特性与密钥的取值之间的关系尽可能复杂化，以使密钥和明文以及密文之间的依
赖性对密码分析者来说是无法利用的。扩散的作用就是将每一位明文的影响尽可能迅速地作用到较多的输出密文位中，以便在大量的密文中消除明文的统计结构，并且使每一位密钥的影响尽可能迅速地扩展到较多的密文位中，以防对密钥进行逐段破译。具体实现代码如下：








```php
<?php

/** 
 * 常用对称加密算法类 
 * 支持密钥：64/128/256 bit（字节长度8/16/32） 
 * 支持算法：DES/AES（根据密钥长度自动匹配使用：DES:64bit AES:128/256bit） 
 * 支持模式：CBC/ECB/OFB/CFB 
 * 密文编码：base64字符串/十六进制字符串/二进制字符串流 
 * 填充方式: PKCS5Padding（DES） 
 * 
 * @author: linvo 
 * @version: 1.0.0 
 * @date: 2013/1/10 
 */ 
class Xcrypt{  
   
  private $mcrypt;  
  private $key;  
  private $mode;  
  private $iv;  
  private $blocksize;  
   
  /** 
   * 构造函数 
   * 
   * @param string 密钥 
   * @param string 模式 
   * @param string 向量（"off":不使用 / "auto":自动 / 其他:指定值，长度同密钥） 
   */ 
  public function __construct($key, $mode = 'cbc', $iv = "off"){  
    switch (strlen($key)){  
    case 8:  
      $this->mcrypt = MCRYPT_DES;  
      break;  
    case 16:  
      $this->mcrypt = MCRYPT_RIJNDAEL_128;  
      break;  
    case 32:  
      $this->mcrypt = MCRYPT_RIJNDAEL_256;  
      break;  
    default:  
      die("Key size must be 8/16/32");  
    }  
   
    $this->key = $key;  
   
    switch (strtolower($mode)){  
    case 'ofb':  
      $this->mode = MCRYPT_MODE_OFB;  
      if ($iv == 'off') die('OFB must give a IV'); //OFB必须有向量  
      break;  
    case 'cfb':  
      $this->mode = MCRYPT_MODE_CFB;  
      if ($iv == 'off') die('CFB must give a IV'); //CFB必须有向量  
      break;  
    case 'ecb':  
      $this->mode = MCRYPT_MODE_ECB;  
      $iv = 'off'; //ECB不需要向量  
      break;  
    case 'cbc':  
    default:  
      $this->mode = MCRYPT_MODE_CBC;  
    }  
   
    switch (strtolower($iv)){  
    case "off":  
      $this->iv = null;  
      break;  
    case "auto":  
      $source = PHP_OS=='WINNT' ? MCRYPT_RAND : MCRYPT_DEV_RANDOM;  
      $this->iv = mcrypt_create_iv(mcrypt_get_block_size($this->mcrypt, $this->mode), $source);  
      break;  
    default:  
      $this->iv = $iv;  
    }  
   
  }  
   
  /** 
   * 获取向量值 
   * @param string 向量值编码（base64/hex/bin） 
   * @return string 向量值 
   */ 
  public function getIV($code = 'base64'){  
    switch ($code){  
    case 'base64':  
      $ret = base64_encode($this->iv);  
      break;  
    case 'hex':  
      $ret = bin2hex($this->iv);  
      break;  
    case 'bin':  
    default:  
      $ret = $this->iv;  
    }  
    return $ret;  
  }  
   
  /** 
   * 加密 
   * @param string 明文 
   * @param string 密文编码（base64/hex/bin） 
   * @return string 密文 
   */ 
  public function encrypt($str, $code = 'base64'){  
    if ($this->mcrypt == MCRYPT_DES) $str = $this->_pkcs5Pad($str);  
   
    if (isset($this->iv)) {  
      $result = mcrypt_encrypt($this->mcrypt, $this->key, $str, $this->mode, $this->iv);   
    } else {  
      @$result = mcrypt_encrypt($this->mcrypt, $this->key, $str, $this->mode);   
    }  
   
    switch ($code){  
    case 'base64':  
      $ret = base64_encode($result);  
      break;  
    case 'hex':  
      $ret = bin2hex($result);  
      break;  
    case 'bin':  
    default:  
      $ret = $result;  
    }  
   
    return $ret;  
   
  }  
   
  /** 
   * 解密  
   * @param string 密文 
   * @param string 密文编码（base64/hex/bin） 
   * @return string 明文 
   */ 
  public function decrypt($str, $code = "base64"){    
    $ret = false;  
   
    switch ($code){  
    case 'base64':  
      $str = base64_decode($str);  
      break;  
    case 'hex':  
      $str = $this->_hex2bin($str);  
      break;  
    case 'bin':  
    default:  
    }  
   
    if ($str !== false){  
      if (isset($this->iv)) {  
        $ret = mcrypt_decrypt($this->mcrypt, $this->key, $str, $this->mode, $this->iv);   
      } else {  
        @$ret = mcrypt_decrypt($this->mcrypt, $this->key, $str, $this->mode);   
      }  
      if ($this->mcrypt == MCRYPT_DES) $ret = $this->_pkcs5Unpad($ret);  
    }  
   
    return $ret;   
  }   
   
  private function _pkcs5Pad($text){  
    $this->blocksize = mcrypt_get_block_size($this->mcrypt, $this->mode);   
    $pad = $this->blocksize - (strlen($text) % $this->blocksize);  
    return $text . str_repeat(chr($pad), $pad);  
  }  
   
  private function _pkcs5Unpad($text){  
    $pad = ord($text{strlen($text) - 1});  
    if ($pad > strlen($text)) return false;  
    if (strspn($text, chr($pad), strlen($text) - $pad) != $pad) return false;  
    $ret = substr($text, 0, -1 * $pad);  
    return $ret;  
  }  
   
  private function _hex2bin($hex = false){  
    $ret = $hex !== false && preg_match('/^[0-9a-fA-F]+$/i', $hex) ? pack("H*", $hex) : false;    
    return $ret;  
  }  
   
}

```

使用实例

```php
header('Content-Type:text/html;Charset=utf-8;');  
   
include "xcrypt.php";  
   
echo '<pre>';  
 
$a = isset($_GET['a']) ? $_GET['a'] : '测试123';  
   
//密钥  
$key = '12345678123456781234567812345678'; //256 bit  
$key = '1234567812345678'; //128 bit  
$key = '12345678'; //64 bit  
   
//设置模式和IV  
$m = new Xcrypt($key, 'cbc', 'auto');  
   
//获取向量值  
echo '向量：';  
var_dump($m->getIV());  
   
//加密  
$b = $m->encrypt($a, 'base64');  
//解密  
$c = $m->decrypt($b, 'base64');  
   
echo '加密后：';  
var_dump($b);  
echo '解密后：';  
var_dump($c);  

echo '</pre>';


```

参考地址：

[https://www.jb51.net/article/128149.htm](https://www.jb51.net/article/128149.htm)