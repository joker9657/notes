记录下花了一个晚上和一个早晨的成果，为什么要在 Mac 上安装这个 Redis，原因是我的博客要用到它。例如用集合存储所有分类，有序集合来存储文章的阅读量等等，这是前提。  
安装 Redis 服务较为简单。
```shell
$ brew search redis // 这是查找可安装的版本
$ brew install redis // 这个命令直接安装，配置文件位置/usr/local/etc/redis.conf
$ brew services start redis // 启动redis服务
$ redis-cli ping // 返回PONG 说明启动成功
```
接下来安装一个 phpredis 扩展，有两种方法，一种是下载包解压编译安装，一种是使用工具 pecl 安装，在这里使用的是后者。
```shell
$ pecl install redis // 安装 redis 扩展
```
第一次时出现了权限问题，第二次直接连接失败 `Connection to 'ssl://pecl.php.net:443' failed`,查了网上很多回答是缺少证书，然而我并不是因为这个问题，所以昨晚没有搞好，今天早上起来重新安装，发现又行了..
```shell
$ openssl version // 查看openssl是否正常
LibreSSL 2.6.5
$ php -r "print_r(openssl_get_cert_locations());" // 进入证书目录找了是存在的
Array
(
    [default_cert_file] => /usr/local/etc/openssl@1.1/cert.pem
    [default_cert_file_env] => SSL_CERT_FILE
    [default_cert_dir] => /usr/local/etc/openssl@1.1/certs
    [default_cert_dir_env] => SSL_CERT_DIR
    [default_private_dir] => /usr/local/etc/openssl@1.1/private
    [default_default_cert_area] => /usr/local/etc/openssl@1.1
    [ini_cafile] => /usr/local/etc/openssl@1.1/cert.pem
    [ini_capath] => /usr/local/etc/openssl@1.1/certs
)

```
最后遇到一个问题是 `pecl` 安装的扩展在`/usr/local/Cellar/php/7.4.0/pecl`，而默认的扩展目录不是这个，需要加个软连接过去。
```shell
$ cd /usr/local/lib/php // 进入默认扩展加载目录
$ ln -s /usr/local/Cellar/php/7.4.0/pecl 
```
最后的最后，重启 PHP,
```shell
$ brew services restart php
$ php -m | grep redis
```



# Reference
- [Mac安装Redis](https://segmentfault.com/a/1190000021081966)
- [mac自带php安装redis扩展](https://panxu.net/article/8400.html)
- [Mac 编译安装 PHPRedis 模块](https://learnku.com/articles/39008)

