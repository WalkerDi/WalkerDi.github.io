---
layout: post
title:  "PHP生成二维码"
categories: 二维码
tags:  CodeIgniter PHP 
---

* content
{:toc}

本文主要讲解使用phpqrcode生成二维码， phpqrcode下载地址: [https://sourceforge.net/projects/phpqrcode/](https://sourceforge.net/projects/phpqrcode/) ,这里以CI 框架为例进行说明，其他框架类似。

## PHP生成二维码

具体示例代码如下：

```

    /**
     * 生成二维码
     * phpqrcode类文件下载
     * @param string $url 二维码内容
     * @return string
     */
    public function create_qrcode($url=''){
    
        $this->load->library('Qrcode'); //引入phpqrcode类文件
        $value = $url;                  //二维码内容
        $errorCorrectionLevel = 'H';    //容错级别
        $matrixPointSize = 6;           //生成图片大小
        //生成二维码图片
        $filename = "./logs/".time().'.png';
        $qrcodename="./logs/p".time().'.png';
        chmod("./logs", 777);
        $this->qrcode->png($value,$filename , $errorCorrectionLevel, $matrixPointSize, 2);
    
        $logo = './public/image/logo_qcode.png';  //准备好的logo图片
        $QR = $filename;            //已经生成的原始二维码图
        if (file_exists($logo)) {
            $QR = imagecreatefromstring(file_get_contents($QR));        //目标图象连接资源。
            $logo = imagecreatefromstring(file_get_contents($logo));    //源图象连接资源。
            $QR_width = imagesx($QR);           //二维码图片宽度
            $QR_height = imagesy($QR);          //二维码图片高度
            $logo_width = imagesx($logo);       //logo图片宽度
            $logo_height = imagesy($logo);      //logo图片高度
            $logo_qr_width = $QR_width / 4;     //组合之后logo的宽度(占二维码的1/5)
            $scale = $logo_width/$logo_qr_width;    //logo的宽度缩放比(本身宽度/组合后的宽度)
            $logo_qr_height = $logo_height/$scale;  //组合之后logo的高度
            $from_width = ($QR_width - $logo_qr_width) / 2;   //组合之后logo左上角所在坐标点
    
            //重新组合图片并调整大小
            /*
             *  imagecopyresampled() 将一幅图像(源图象)中的一块正方形区域拷贝到另一个图像中
             */
            imagecopyresampled($QR, $logo, $from_width, $from_width, 0, 0, $logo_qr_width,$logo_qr_height, $logo_width, $logo_height);
        }
        unlink($filename); //删除原始二维码
        //输出图片
        imagepng($QR, $qrcodename);
        imagedestroy($QR);
        imagedestroy($logo);
        $this->load->service('s_file');
        $file['img'] = array(
            'tmp_name'=>$qrcodename
    
        );
        $ret = $this->s_file->upload_img($file,'img'); //上传二维码，这里使用的是阿里云存储oss,后面会讲解到
        unlink($qrcodename);
        $data['qrcode_url']=$ret;
        return $data;
    
    }
```