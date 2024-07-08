## PHP
[windows下设置多个版本PHP 以及多个composer的方法](./multiple-versions.md)
[PHP7扩展开发入门](https://www.kancloud.cn/lifei6671/php-extention-dev/672582)
[PHP文件锁](https://www.cnblogs.com/chengwens/p/af6f4dee06ee64b9e3df780194085708.html)
[PHP 内核与原生扩展开发](https://learnku.com/docs/php-internals/php7)

### 开发常用类库
- [laradock PHP常用开发环境的docker配置](https://github.com/laradock/laradock)
- [最好用的短信扩展包 (overtrue/easy-sms)](https://github.com/overtrue/easy-sms)
- [最优雅的支付扩展包 (yansongda/pay)](https://pay.yansongda.cn/docs/v3/)
- [最方便的微信扩展包 (w7corp/easywechat)](https://github.com/w7corp/easywechat)
- [HTTP客户端 (guzzle/guzzle)](https://github.com/guzzle/guzzle)
- [日志组件 monolog](https://github.com/Seldaek/monolog)
- [UUID生成器 (ramsey/uuid)](https://github.com/ramsey/uuid)
- [Markdown语法解析器](https://github.com/erusev/parsedown)
- [路由器 FastRoute](https://github.com/nikic/FastRoute)
- [最强的PHP二维码包](https://github.com/SimpleSoftwareIO/simple-qrcode)
- [OAuth2服务端|league/oauth2-server|实际使用不好使](https://github.com/thephpleague/oauth2-server)
- [OAuth2服务端|oauth2-server-php](https://github.com/bshaffer/oauth2-server-php)
- [OAuth2客户端|league/oauth2-client](https://github.com/thephpleague/oauth2-client)
- [OAuth2客户端|PHP通用, 较league/oauth2-client方便](https://github.com/overtrue/socialite)
- [图像处理](https://github.com/Intervention/image)
- [富文本安全过滤|htmlpurifier](https://github.com/ezyang/htmlpurifier)
- [phpdotenv](https://github.com/vlucas/phpdotenv)
- [MadelineProto](https://github.com/danog/MadelineProto)
- [TelegramApiServer](https://github.com/xtrime-ru/TelegramApiServer)
- [symfony/mailer (swiftmailer替代品)](https://github.com/symfony/mailer)
- [filp/whoops 错误处理器](https://github.com/filp/whoops)
- [thephpleague/flysystem 文件系统](https://github.com/thephpleague/flysystem)
- [EmailValidator 邮件地址验证不仅仅是格式](https://github.com/egulias/EmailValidator)
- [依赖注入容器1 PHP-DI/PHP-DI ](https://github.com/PHP-DI/PHP-DI)
- [依赖注入容器2 thephpleague/container](https://github.com/thephpleague/container)
- [设备user_agent检测 serbanghita/Mobile-Detect](https://github.com/serbanghita/Mobile-Detect)
- [ip2region 16K star](https://github.com/lionsoul2014/ip2region)
- [ip2region 1K star](https://github.com/zoujingli/ip2region)
- [Url处理包](https://uri.thephpleague.com/)

### 办公文件处理
- [dompdf HTML转PDF](https://github.com/dompdf/dompdf)
- [PhpSpreadsheet 电子表格文件处理](https://github.com/PHPOffice/PhpSpreadsheet)
- [PHPWord 一个用于读写文字处理文档的纯 PHP 库](https://github.com/PHPOffice/PHPWord)

### Laravel专用
- [OAuth2客户端|Laravel专用,较league/oauth2-client方便](https://github.com/laravel/socialite)
- [tymondesigns/jwt-auth](https://github.com/tymondesigns/jwt-auth)



### Compoer
```
composer config -g repo.packagist composer https://packagist.org

composer config -g repo.packagist composer https://mirrors.aliyun.com/composer/

composer config -g repos.packagist composer https://mirrors.tencent.com/composer/
```