Custom Curl (嗯懒得取名字)
==========================

对 PHP 自带 curl 的一个简单的封装，支持链式操作。

## 目录

- [使用示例](#使用示例)
    - [GET 方法](#get-方法)
    - [POST 方法](#post-方法)
    - [POST 上传文件](#post-上传文件)
    - [PUT 方法](#put-方法传输-json-数据)
    - [Cookie 字符串/数组](#cookie-字符串数组)
    - [Header](#header)
    - [自定义 CurlOpt](#自定义-CurlOpt)
- [设置项](#设置项)
- [杂项](#杂项)
    - [多次请求同一地址](#多次请求同一地址)
    - [代理](#代理)
    - [设置全局配置](#设置全局配置)
    - [设置全局 CurlOpt 配置](#设置全局-CurlOpt-配置)


## 使用示例

### GET 方法

```php
$curlObj = CustomCurl::init('http://cn.bing.com/search?q=php')->exec();

if (!$curlObj->getStatus()) {
    throw new \Exception('Curl Error', $curlObj->getCurlErrNo());
}

var_dump($curlObj->getHeader(), $curlObj->getCookies(), $curlObj->getBody(), $curlObj->getInfo());
```

### POST 方法

```php
$curlObj = CustomCurl::init('http://www.w3school.com.cn/example/php/demo_php_global_post.php', 'post')
            ->set('postFields', ['fname' => 'jshensh'])
            ->exec();

if (!$curlObj->getStatus()) {
    throw new \Exception('Curl Error', $curlObj->getCurlErrNo());
}

var_dump($curlObj->getHeader(), $curlObj->getCookies(), $curlObj->getBody(), $curlObj->getInfo());
```

### POST 上传文件

```php
$curlObj = CustomCurl::init('http://127.0.0.1/examples/example_server.php', 'post')
            ->set('postFields', [
                'fname'    => 'jshensh',
                'files[0]' => new CURLFile('./README.md'),
                'files[1]' => new CURLFile('LICENSE')
            ])
            ->set('postFieldsBuildQuery', false)    // postFieldsBuildQuery 设置为 True 时，将对 postFields 进行 http_build_query，避免出现跨语言无法 POST 数据的问题。如需上传文件则需要将该项设置为 False。
            ->exec();

if (!$curlObj->getStatus()) {
    throw new \Exception('Curl Error', $curlObj->getCurlErrNo());
}

var_dump($curlObj->getHeader(), $curlObj->getCookies(), $curlObj->getBody(), $curlObj->getInfo());
```

### PUT 方法，传输 JSON 数据

```php
$curlObj = CustomCurl::init('http://lab.imjs.work/server.php', 'put')
            ->set('postFields', ['fname' => 'jshensh']) // 可以传入数组
            ->set('postFields', '{"fname": "jshensh"}') // 也可以直接传入 json 字符串
            ->set('postType', 'json')
            ->exec();

if (!$curlObj->getStatus()) {
    throw new \Exception('Curl Error', $curlObj->getCurlErrNo());
}

var_dump($curlObj->getHeader(), $curlObj->getCookies(), $curlObj->getBody(), $curlObj->getInfo());
```

### 手动设置 Cookie

```php
$cookieJar = [];
$curlObj = CustomCurl::init('http://example.com')
            ->cookieJar($cookieJar)   // 设置 CookieJar，类似于 CURLOPT_COOKIEJAR，可在多次交互过程中自动存取 Cookies
            ->setCookie('a', 'b')     // 设置 Cookie，Key => Value
            ->clearCookies()          // 清空之前设置的所有 Cookie
            ->setCookie('b', 'c')     // 重新设置 Cookie，Key => Value
            ->exec();

if (!$curlObj->getStatus()) {
    throw new \Exception('Curl Error', $curlObj->getCurlErrNo());
}

var_dump($curlObj->getHeader(), $curlObj->getCookies(), $curlObj->getBody(), $curlObj->getInfo());
```

### Cookie 字符串/数组

```php
$curlObj = CustomCurl::init('http://example.com')
            ->setCookie('a', 'b')             // 设置 Cookie，Key => Value
            ->setCookies('b=c; c=d')          // 传入字符串设置 Cookie，之前设置的 cookie 失效
            ->setCookies('d=e; c=f', true)    // 传入字符串追加设置 Cookie，Key 相同的 cookie 将会被覆盖
            ->setCookies(['e' => 'f'], true)  // 传入数组追加设置 Cookie，只允许传入一维数组
            ->setCookies(['e' => ['f', 'g']]) // 传入二维数组将忽略
            ->setCookies('dsjdhs')            // 传入不合法字符串将忽略
            ->exec();

if (!$curlObj->getStatus()) {
    throw new \Exception('Curl Error', $curlObj->getCurlErrNo());
}

var_dump($curlObj->getHeader(), $curlObj->getCookies(), $curlObj->getBody(), $curlObj->getInfo());
```

### Header

```php
$curlObj = CustomCurl::init('http://example.com/api')
            ->setHeader('X-PJAX', 'true')                         // 设置 Header，Key => Value
            ->clearHeaders()                                      // 清空之前设置的所有 Header
            ->setHeader('X-Requested-With', 'XMLHttpRequest')     // 设置 Header，Key => Value
            ->exec();

if (!$curlObj->getStatus()) {
    throw new \Exception('Curl Error', $curlObj->getCurlErrNo());
}

var_dump($curlObj->getHeader(), $curlObj->getCookies(), $curlObj->getBody(), $curlObj->getInfo());
```

### 自定义 CurlOpt

```php
$curlObj = CustomCurl::init('http://example.com')
            ->setCurlOpt(CURLOPT_SSL_VERIFYPEER, true)  // CURLOPT_SSL_VERIFYPEER，默认值 False
            ->setCurlOpt(CURLOPT_SSL_VERIFYHOST, true)  // CURLOPT_SSL_VERIFYHOST，默认值 False
            ->setCurlOpt(CURLOPT_ENCODING, '')          // CURLOPT_ENCODING，默认值 ''
            ->exec();

if (!$curlObj->getStatus()) {
    throw new \Exception('Curl Error', $curlObj->getCurlErrNo());
}

var_dump($curlObj->getHeader(), $curlObj->getCookies(), $curlObj->getBody(), $curlObj->getInfo());
```

## 设置项

```php
$curlObj = CustomCurl::init('http://cn.bing.com')
            ->set('referer', 'http://google.com')       // 设置 HTTP REFERER
            ->set('ignoreCurlError', 1)                 // 忽略 Curl 错误，默认值 False
            ->set('timeout', 1)                         // CURLOPT_TIMEOUT，单位秒，默认值 5
            ->set('reRequest', 1)                       // 遇到错误时重新尝试的次数，默认值 3
            ->set('postFields', ['fname' => 'jshensh']) // POST 提交参数，数组
            ->set('postType', 'json')                   // 提交方式，可选 ['form', 'json', 'string']，默认值 'form'
            ->set('followLocation', 1)                  // CURLOPT_FOLLOWLOCATION，默认值 False
            ->set('autoRefer', 1)                       // CURLOPT_AUTOREFERER，默认值 True
            ->set('maxRedirs', 1)                       // CURLOPT_MAXREDIRS，默认值 3
            ->set('userAgent', 'Mozilla')               // CURLOPT_USERAGENT
            ->exec();

if (!$curlObj->getStatus()) {
    throw new \Exception('Curl Error', $curlObj->getCurlErrNo());
}

var_dump($curlObj->getHeader(), $curlObj->getCookies(), $curlObj->getBody(), $curlObj->getInfo());
```

## 杂项

### 多次请求同一地址

```php
$curlObj1 = CustomCurl::init('http://cn.bing.com')
                ->set('referer', 'http://google.com');

$curlObj2 = clone $curlObj1;

$curlObj1 = $curlObj1->setHeader('X-PJAX', 'true')->exec();
$curlObj2 = $curlObj2->setHeader('X-Requested-With', 'XMLHttpRequest')->exec();

if (!$curlObj1->getStatus()) {
    throw new \Exception('Curl Error', $curlObj1->getCurlErrNo());
}

var_dump($curlObj1->getHeader(), $curlObj1->getCookies(), $curlObj1->getBody(), $curlObj1->getInfo());

if (!$curlObj2->getStatus()) {
    throw new \Exception('Curl Error', $curlObj2->getCurlErrNo());
}

var_dump($curlObj2->getHeader(), $curlObj2->getCookies(), $curlObj2->getBody(), $curlObj2->getInfo());
```

### 代理

```php
$curlObj = CustomCurl::init('http://example.com')
            ->set('proxy', '127.0.0.1')                    // 代理地址
            ->set('proxyPort', 8080)                       // 代理端口，默认 8080
            ->set('proxyUserPwd', '[username]:[password]') // 代理用户名密码，默认不设置
            ->set('proxyType', CURLPROXY_HTTP)             // 代理类型，可选 [CURLPROXY_HTTP, CURLPROXY_SOCKS4, CURLPROXY_SOCKS5, CURLPROXY_SOCKS4A, CURLPROXY_SOCKS5_HOSTNAME]，默认 CURLPROXY_HTTP，传入常量，不要加引号
            ->exec();

if (!$curlObj->getStatus()) {
    throw new \Exception('Curl Error', $curlObj->getCurlErrNo());
}

var_dump($curlObj->getHeader(), $curlObj->getCookies(), $curlObj->getBody(), $curlObj->getInfo());
```

### 设置全局配置

```php
CustomCurl::setConf('timeout', 3);                            // CURLOPT_TIMEOUT，单位秒，默认值 5
CustomCurl::setConf('reRequest', 1);                          // 遇到错误时重新尝试的次数，默认值 3
CustomCurl::setConf('maxRedirs', 1);                          // CURLOPT_MAXREDIRS，默认值 3
CustomCurl::setConf('ignoreCurlError', 1);                    // 忽略 Curl 错误，默认值 False
CustomCurl::setConf('followLocation', 1);                     // CURLOPT_FOLLOWLOCATION，默认值 True
CustomCurl::setConf('referer', 'http://google.com');          // 设置 HTTP REFERER
CustomCurl::setConf('userAgent', 'Mozilla');                  // 设置 User-Agent
CustomCurl::setConf('customHeader', ['X-PJAX: true']);        // 设置 Header，要求传入数组
CustomCurl::setConf('sendCookies', 'a=b; b=c');               // 设置 Cookies，要求传入一维数组或者字符串
CustomCurl::setConf('autoRefer', 1);                          // CURLOPT_AUTOREFERER，默认值 True
CustomCurl::setConf('postType', 'json');                      // 提交方式，可选 ['form', 'json', 'string']，默认值 'form'
CustomCurl::setConf('proxy', '127.0.0.1');                    // 代理
CustomCurl::setConf('proxyPort', 8080);                       // 代理端口
CustomCurl::setConf('proxyUserPwd', '[username]:[password]'); // 代理用户名密码
CustomCurl::setConf('proxyType', CURLPROXY_HTTP);             // 代理方式
// 以上为所有可修改的全局配置项

$curlObj = CustomCurl::init('http://lab.imjs.work/server.php')
            ->set('userAgent', 'Test') // 在当前会话中覆盖预设值
            ->exec();

if (!$curlObj->getStatus()) {
    throw new \Exception('Curl Error', $curlObj->getCurlErrNo());
}

var_dump($curlObj->getHeader(), $curlObj->getCookies(), $curlObj->getBody(), $curlObj->getInfo());

$curlObj1 = CustomCurl::init('http://lab.imjs.work/server.php')->exec();

if (!$curlObj1->getStatus()) {
    throw new \Exception('Curl Error', $curlObj1->getCurlErrNo());
}

var_dump($curlObj1->getHeader(), $curlObj1->getCookies(), $curlObj1->getBody(), $curlObj1->getInfo());

CustomCurl::resetConf('userAgent'); // 恢复 userAgent 参数为默认值

$curlObj2 = CustomCurl::init('http://lab.imjs.work/server.php')->exec();

if (!$curlObj2->getStatus()) {
    throw new \Exception('Curl Error', $curlObj2->getCurlErrNo());
}

var_dump($curlObj2->getHeader(), $curlObj2->getCookies(), $curlObj2->getBody(), $curlObj2->getInfo());

CustomCurl::resetConf(); // 恢复全部参数为默认值

$curlObj3 = CustomCurl::init('http://lab.imjs.work/server.php')->exec();

if (!$curlObj3->getStatus()) {
    throw new \Exception('Curl Error', $curlObj3->getCurlErrNo());
}

var_dump($curlObj3->getHeader(), $curlObj3->getCookies(), $curlObj3->getBody(), $curlObj3->getInfo());
```

### 设置全局 CurlOpt 配置项

```php
CustomCurl::setCurlOptConf(CURLOPT_SSL_VERIFYPEER, true);   // CURLOPT_SSL_VERIFYPEER，默认值 False
CustomCurl::setCurlOptConf(CURLOPT_SSL_VERIFYHOST, true);   // CURLOPT_SSL_VERIFYHOST，默认值 False
CustomCurl::setCurlOptConf(CURLOPT_ENCODING, 'gzip');       // CURLOPT_ENCODING，默认值 ''
// 以上为所有可修改的全局 CurlOpt 配置项

$curlObj = CustomCurl::init('http://lab.imjs.work/server.php')
            ->setCurlOpt(CURLOPT_ENCODING, '') // 在当前会话中覆盖预设值
            ->exec();

if (!$curlObj->getStatus()) {
    throw new \Exception('Curl Error', $curlObj->getCurlErrNo());
}

var_dump($curlObj->getHeader(), $curlObj->getCookies(), $curlObj->getBody(), $curlObj->getInfo());

$curlObj1 = CustomCurl::init('http://lab.imjs.work/server.php')->exec();

if (!$curlObj1->getStatus()) {
    throw new \Exception('Curl Error', $curlObj1->getCurlErrNo());
}

var_dump($curlObj1->getHeader(), $curlObj1->getCookies(), $curlObj1->getBody(), $curlObj1->getInfo());

CustomCurl::resetCurlOptConf(CURLOPT_ENCODING); // 恢复 CURLOPT_ENCODING 为默认值

$curlObj2 = CustomCurl::init('http://lab.imjs.work/server.php')->exec();

if (!$curlObj2->getStatus()) {
    throw new \Exception('Curl Error', $curlObj2->getCurlErrNo());
}

var_dump($curlObj2->getHeader(), $curlObj2->getCookies(), $curlObj2->getBody(), $curlObj2->getInfo());

CustomCurl::resetCurlOptConf(); // 恢复全部 CurlOpt 配置为默认值

$curlObj3 = CustomCurl::init('http://lab.imjs.work/server.php')->exec();

if (!$curlObj3->getStatus()) {
    throw new \Exception('Curl Error', $curlObj3->getCurlErrNo());
}

var_dump($curlObj3->getHeader(), $curlObj3->getCookies(), $curlObj3->getBody(), $curlObj3->getInfo());
```


## 版权信息

Custom Curl 遵循 GPL-3.0 开源协议发布，并提供免费使用。

版权所有Copyright © 2018 by jshensh (http://233.imjs.work)

All rights reserved。

更多细节参阅 [LICENSE](LICENSE)