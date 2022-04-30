# Laravel最佳实践

本文记录Laravel开发过程中遇到的问题以及最佳实践方法和参考来源，不定期更新

### 配置书写方式
存储在 .env 和 config/app.php 文件中，然后使用 config() 函数来读取。

> .env
> CDN_DOMAIN=cdndomain.com

> config/app.php
> 'cdn_domain' => env('CDN_DOMAIN', null),

> 程序读取方式
> config('app.cdn_domain')

参考：https://laravel-china.org/docs/laravel-specification/5.5/configuration-information-and-environment-variables/514
