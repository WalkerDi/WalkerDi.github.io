---
layout: post
title:  "微信模板消息"
categories: 微信模板消息 微信
tags:  微信 微信模板消息 
---

* content
{:toc}

本文讲解如何发送微信模板消息，以Ci框架为例进行说明，其中获取统一微信授权token`access_token()`方法，统一使用`微信分享获取签名`[https://walkerdi.github.io/2018/05/02/weixin-share/](https://walkerdi.github.io/2018/05/02/weixin-share/),讲解到方法。

示例代码如下：

```php
<?php
/**
 * 微信模板消息
 * Created by PhpStorm.
 * author: dichuanjun
 * Date: 2017/12/29
 * Time: 15:44
 */

class Wxtemplate
{
    protected $CI;
    public function __construct()
    {
        $this->CI = & get_instance();

    }
    /**
     * 模板消息发送
     * @param $open_id int 用户open_id
     * @param $template_num int 模板编号
     * @param $message_num int 消息编号
     * @param $data string 发送数据
     * @param string $url string 跳转链接
     */
    public function template_message($open_id,$template_num,$message_num,$data,$url=''){
        //模板id列表，可在微信公众号模板消息中添加可使用的消息模板id
        $template_id_list=[
            1=>'bHGu8vVBa_ccc4465-UrgSdzYt16UCvEatIjjDI',
            2=>'aHJTGUTdi4C9HSfiKf4cS2pNoIpS-123456-Gm8',
        ];
        
        
        
        
        
        
        if(empty($url)){
            $url="http://".$_SERVER['HTTP_HOST']."/share/index.html#/";
        }

        //用户账号
        if(!isset($data['phone'])){
            $data['phone']='';
        }
        //车型
        if(!isset($data['car_type'])){
            $data['car_type']='';
        }
        //活动时间
        if(!isset($data['activity_time'])){
            $data['activity_time']='';
        }
        //活动名称
        if(!isset($data['act_name'])){
            $data['act_name']='';
        }
        //活动地址
        if(!isset($data['point_address'])){
            $data['point_address']='';
        }
        //验车时间
        if(!isset($data['check_time'])){
            $data['check_time']='';
        }
        //优惠劵类型
        if(!isset($data['coupon_type'])){
            $data['coupon_type']='';
        }
        //优惠劵截止日期
        if(!isset($data['coupon_etime'])){
            $data['coupon_etime']='';
        }

        //发送消息数据拼接
        $send_data=[
            1=>[
                'first'=>[
                'value'=>"尊敬的用户，感谢报名". $data['car_type']."绿色共享活动，很抱歉您未能通过活动审核，请留意我们后续活动，谢谢支持。",
                'color'=>'#173177',
                ],
                'keyword1'=>[
                    'value'=>'线下活动',
                    'color'=>'#173177',
                ],
                'keyword2'=>[
                    'value'=>$data['car_type'].'绿色共享活动',
                    'color'=>'#173177',
                ],
                'remark'=>[
                    'value'=>'',
                    'color'=>'#173177',
                ]
            ],
            2=>[
                'first'=>[
                    'value'=>"尊敬的用户，感谢报名". $data['car_type']."绿色共享活动，恭喜您已通过活动审核，确认用户协议后即可参与活动。",
                    'color'=>'#173177',
                ],
                'keyword1'=>[
                    'value'=>'线下活动',
                    'color'=>'#173177',
                ],
                'keyword2'=>[
                    'value'=>$data['car_type'].'绿色共享活动',
                    'color'=>'#173177',
                ],
                'remark'=>[
                    'value'=>'',
                    'color'=>'',
                ]
            ],
            3=>[
                'first'=>[
                    'value'=>"尊敬的用户，您报名的车型暂未有活动排期，我们会在5个工作日内为您安排活动场次，敬请留意客服电话，谢谢。",
                    'color'=>'#173177',
                ],
                'keyword1'=>[
                    'value'=>'线下活动',
                    'color'=>'#173177',
                ],
                'keyword2'=>[
                    'value'=>$data['car_type'].'绿色共享活动',
                    'color'=>'#173177',
                ],
                'remark'=>[
                    'value'=>'',
                    'color'=>'',
                ]
            ],
            4=>[
                'first'=>[
                    'value'=>"尊敬的用户，已为您安排参加本周绿色共享免费领车答疑会。",
                    'color'=>'#173177',
                ],
                'keyword1'=>[
                    'value'=>$data['act_name'],
                    'color'=>'#173177',
                ],
                'keyword2'=>[
                    'value'=>$data['activity_time'],
                    'color'=>'#173177',
                ],
                'keyword3'=>[
                    'value'=>$data['point_address'],
                    'color'=>'#173177',
                ],
                'remark'=>[
                    'value'=>'期待您的参与，谢谢。',
                    'color'=>'#173177',
                ]
            ],
            5=>[
                'first'=>[
                    'value'=>"尊敬的用户，以下是您申请的信息。",
                    'color'=>'#173177',
                ],
                'keyword1'=>[
                    'value'=>'全款购车',
                    'color'=>'#173177',
                ],
                'keyword2'=>[
                    'value'=>date('Y-m-d H:i',time()),
                    'color'=>'#173177',
                ],
                'remark'=>[
                    'value'=>'完成付款后即可为您安排订车，谢谢。',
                    'color'=>'#173177',
                ]
            ],
            6=>[
                'first'=>[
                    'value'=>"尊敬的用户，以下是您申请的信息。",
                    'color'=>'#173177',
                ],
                'keyword1'=>[
                    'value'=>'贷款购车',
                    'color'=>'#173177',
                ],
                'keyword2'=>[
                    'value'=>date('Y-m-d H:i',time()),
                    'color'=>'#173177',
                ],
                'remark'=>[
                    'value'=>'预计贷款审核会在10个工作日内完成，谢谢。',
                    'color'=>'#173177',
                ]
            ],
            7=>[
                'first'=>[
                    'value'=>"尊敬的用户，感谢及时寄出验车材料。",
                    'color'=>'#173177',
                ],
                'keyword1'=>[
                    'value'=>$data['phone'],
                    'color'=>'#173177',
                ],
                'keyword2'=>[
                    'value'=>'待验车',
                    'color'=>'#173177',
                ],
                'keyword3'=>[
                    'value'=>"我们将于".$data['check_time']."为您进行验车，验车过程大约需要3-5个工作日，证件使用完毕后将马上安排回寄，请及时填写回寄地址，谢谢。",
                    'color'=>'#173177',
                ],
                'keyword4'=>[
                    'value'=>'验车时间为'.$data['check_time'],
                    'color'=>'#173177',
                ],
                'remark'=>[
                    'value'=>'',
                    'color'=>'',
                ]

            ],
            8=>[
                'first'=>[
                    'value'=>"恭喜您获得优惠券。",
                    'color'=>'#173177',
                ],
                'keyword1'=>[
                    'value'=>$data['act_name'],
                    'color'=>'#173177',
                ],
                'keyword2'=>[
                    'value'=>$data['coupon_type'],
                    'color'=>'#173177',
                ],
                'keyword3'=>[
                    'value'=>"自动发放电子兑换券",
                    'color'=>'#173177',
                ],
                'keyword4'=>[
                    'value'=>$data['coupon_etime'],
                    'color'=>'#173177',
                ],
                'remark'=>[
                    'value'=>'感谢您的参与。',
                    'color'=>'#173177',
                ]

            ],

        ];

        $message=[
            'touser'=>$open_id,
            'template_id'=>$template_id_list[$template_num],
            'url'=>$url,
            'data'=>$send_data[$message_num],
        ];
        $data_json=json_encode($message,JSON_UNESCAPED_UNICODE);
        $this->send_message($data_json);
    }

    /**
     * 发送模板消息
     * @param $data string 发送数据
     * @return bool
     */

    private function send_message($data){
        //微信统一授权token，可参考微信分享获取签名中的token 获取方法即可,参考地址：[https://walkerdi.github.io/2018/05/02/weixin-share/]
        $this->CI->load->library('weixin');
        $access_token=$this->CI->weixin->access_token(); 
        $url="https://api.weixin.qq.com/cgi-bin/message/template/send?access_token={$access_token}";
        $res=$this->_http_post($url,$data);
        $result=json_decode($res,true);

        if($result['errcode']==0){
            return true;
        }else{
            return false;
        }

    }

    private function _http_post($url, $data) {
        $handle = curl_init();
        curl_setopt($handle, CURLOPT_SSL_VERIFYPEER, false); // 跳过证书检查
        curl_setopt($handle, CURLOPT_SSL_VERIFYHOST, 2);  // 从证书中检查SSL加密算法是否存在
        curl_setopt($handle, CURLOPT_POST, 1);
        // curl_setopt($handle, CURLOPT_POSTFIELDS, http_build_query($data));
        curl_setopt($handle, CURLOPT_POSTFIELDS, $data);
        curl_setopt($handle, CURLOPT_TIMEOUT, 10);
        curl_setopt($handle, CURLOPT_URL, $url);
        curl_setopt($handle, CURLOPT_RETURNTRANSFER, 1);
        $response = curl_exec($handle);
        curl_close($handle);

        return $response;
    }

}

```