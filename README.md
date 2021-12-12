# yii2-easy-wechat

> Based on the latest overtrue/wechat 4.x
WeChat SDK for yii2 , based on overtrue/wechat.support EasyWechat 4.x
This extension helps you access overtrue/wechat application in a simple & familiar way: Yii::$app->wechat.

WeChat SDK for Yii2 , Based on [overtrue/wechat](https://github.com/overtrue/wechat).     
This extension makes it easy to use yii2 to call EasyWechat:   `Yii::$app->wechat`.   
[![Latest Stable Version](http://poser.pugx.org/hjp1011/yii2-easy-wechat/v)](https://packagist.org/packages/hjp1011/yii2-easy-wechat) [![Total Downloads](http://poser.pugx.org/hjp1011/yii2-easy-wechat/downloads)](https://packagist.org/packages/hjp1011/yii2-easy-wechat) [![Latest Unstable Version](http://poser.pugx.org/hjp1011/yii2-easy-wechat/v/unstable)](https://packagist.org/packages/hjp1011/yii2-easy-wechat) [![License](http://poser.pugx.org/hjp1011/yii2-easy-wechat/license)](https://packagist.org/packages/hjp1011/yii2-easy-wechat) [![PHP Version Require](http://poser.pugx.org/hjp1011/yii2-easy-wechat/require/php)](https://packagist.org/packages/hjp1011/yii2-easy-wechat)

## Install
```
composer require hjp1011/yii2-easy-wechat
```

## Config

Add SDK to Yii2  'config/main.php' component:

```php

'components' => [
	// ...
	'wechat' => [
		'class' => 'yiiframe\easywechat\Wechat',
		'userOptions' => [],  // user identity class parameters
		'sessionParam' => 'wechatUser', // wechatUser information will be stored in the session in this key
		'returnUrlParam' => '_wechatReturnUrl', //returnUrl is stored in the session
		'rebinds' => [ // Customizable service module 
		    // 'cache' => 'common\components\Cache',
		]
	],
	// ...
]
```

Set basic configuration information and wechat payment information to 'config/params.php' :
```php
// Wechat configuration details can refer to EasyWechat
'wechatConfig' => [],

// Wechat Pay configuration
'wechatPaymentConfig' => [],

// Micro channel applets configuration
'wechatMiniProgramConfig' => [],

// Wechat Open Platform Third-party platform configuration
'wechatOpenPlatformConfig' => [],

// Wechat enterprise wechat configuration
'wechatWorkConfig' => [],

// Wechat enterprise wechat open platform
'wechatOpenWorkConfig' => [],

// Wechat small and micro merchants
'wechatMicroMerchantConfig' => [],
```

The configuration document

[Wechat configuration description document.](https://www.easywechat.com/docs/master/official-account/configuration)  
[Wechat Pay configuration documentation.](https://www.easywechat.com/docs/master/payment/jssdk)  
[Wechat applets configuration description document.](https://www.easywechat.com/docs/master/mini-program/index)  
[Wechat open Platform Third-party platform](https://www.easywechat.com/docs/master/open-platform/index)  
[Enterprise WeChat](https://www.easywechat.com/docs/master/wework/index)  
[Enterprise wechat open platform](https://www.easywechat.com/docs/master/open-work/index)  
[Small businesses](https://www.easywechat.com/docs/master/micro-merchant/index)

## Eample


Wechat web page authorization adn Get the current user information

```php
if (Yii::$app->wechat->isWechat && !Yii::$app->wechat->isAuthorized()) {
    return Yii::$app->wechat->authorizeRequired()->send();
}

// Method 1 to obtain the current user information of wechat
Yii::$app->session->get('wechatUser')

// Method 2 to obtain the current user information of wechat
Yii::$app->wechat->user
```
Obtain the wechat SDK instance

```php
$app = Yii::$app->wechat->app;
```
Obtain the wechat Pay SDK instance

```php
$payment = Yii::$app->wechat->payment;
```
Get the instance of wechat applets

```php
$miniProgram = Yii::$app->wechat->miniProgram;
```

Obtain the third-party platform instance of wechat open Platform

```php
$openPlatform = Yii::$app->wechat->openPlatform;
```

Obtain the enterprise wechat instance

```php
$work = Yii::$app->wechat->work;
```

Access to wechat enterprise wechat open platform

```php
$work = Yii::$app->wechat->openWork;
```

Get wechat small and micro merchants

```php
$microMerchant = Yii::$app->wechat->microMerchant;
```


WeChat pay(JsApi):

```php
// Pay parameters
$orderData = [ 
    'openid' => '.. '
    // ... etc. 
];

// Generate payment configuratio
$payment = Yii::$app->wechat->payment;
$result = $payment->order->unify($orderData);
if ($result['return_code'] == 'SUCCESS') {
    $prepayId = $result['prepay_id'];
    $config = $payment->jssdk->sdkConfig($prepayId);
} else {
    throw new yii\base\ErrorException('Wechat payment is abnormal, please try again later');
}  

return $this->render('wxpay', [
    'jssdk' => $payment->jssdk, // $app is retrieved from the above fetching instance
    'config' => $config
]);

```

JSSDK initiates payment
```
<script src="http://res.wx.qq.com/open/js/jweixin-1.4.0.js" type="text/javascript" charset="utf-8"></script>
<script type="text/javascript" charset="utf-8">
    //Array for JSSDK authorization available methods, as needed to add a detailed view of wechat JSSDK methods
    wx.config(<?php echo $jssdk->buildConfig(array('chooseWXPay'), true) ?>);
    // Initiate payment
    wx.chooseWXPay({
        timestamp: <?= $config['timestamp'] ?>,
        nonceStr: '<?= $config['nonceStr'] ?>',
        package: '<?= $config['package'] ?>',
        signType: '<?= $config['signType'] ?>',
        paySign: '<?= $config['paySign'] ?>', // Pay for signature
        success: function (res) {
            // Callback function after successful payment
        }
    });
</script>
```

### Smart tips

If you need an intelligent reminder from your editor (PhpStorm etc.) to use 'Yii::$app->wechat', add the following to 'Yii \ Base \Application' :
```
<?php

namespace yii\base;

use Yii;

/**
 *
 * @property \yiiframe\easywechat\Wechat $wechat Add this line to make the editor smart prompt.
 *
 * @author Qiang Xue <qiang.xue@gmail.com>
 * @since 2.0
 */
abstract class Application extends Module
{

}
```

### More documentation

 [EasyWeChat Docs](https://www.easywechat.com/docs/master).
 
 ### The instance

 [RageFrame](https://github.com/hjp1011/rageframe2)

### The problem of feedback

If you have any questions in use, please feel free to give me feedback. You can contact me through the following contact information


