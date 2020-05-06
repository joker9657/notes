通过 Composer 安装 Laravel 后，下一件应该做的事就是将应用程序的密钥设置为随机字符串。(注：7.x以后已经自动生成了)。
```shell
$ php artisan key:generate
```
> 通常来说，这个字符串长度为 32 个字符。密钥可以在 .env 环境文件中设置。前提是你要将 .env.example 文件重命名为 .env。 **如果应用程序密钥没有被设置，就不能确保你的用户会话和其他加密数据的安全！**
# 前提

因为基本上都是使用 Laravel 开发 API 接口，用的是 jwt，没有考虑过这个密钥的用途，但是社区中经常有关于这个密钥的问题，今天就稍微了解下它的原理及应用。

../vendor/laravel/framework/src/Illuminate\Foundation\Console
```php
<?php

namespace Illuminate\Foundation\Console;

use Illuminate\Console\Command;
use Illuminate\Encryption\Encrypter;
use Illuminate\Console\ConfirmableTrait;

class KeyGenerateCommand extends Command
{
    use ConfirmableTrait;

    /**
     * The name and signature of the console command.
     *
     * @var string
     */
    protected $signature = 'key:generate
                    {--show : Display the key instead of modifying files}
                    {--force : Force the operation to run when in production}';

    /**
     * The console command description.
     *
     * @var string
     */
    protected $description = 'Set the application key';

    /**
     * Execute the console command.
     *
     * @return void
     */
    public function handle()
    {
        $key = $this->generateRandomKey();

        if ($this->option('show')) {
            return $this->line('<comment>'.$key.'</comment>');
        }

        // Next, we will replace the application key in the environment file so it is
        // automatically setup for this developer. This key gets generated using a
        // secure random byte generator and is later base64 encoded for storage.
        if (! $this->setKeyInEnvironmentFile($key)) {
            return;
        }

        $this->laravel['config']['app.key'] = $key;

        $this->info('Application key set successfully.');
    }

    /**
     * Generate a random key for the application.
     *
     * @return string
     */
    protected function generateRandomKey()
    {
        return 'base64:'.base64_encode(
            Encrypter::generateKey($this->laravel['config']['app.cipher'])
        );
    }

```
当输入上面的命令时，执行了`KeyGenerateCommand`类的`handle()`方法，其中调用了`generateRandomKey()`方法用于生成密钥。再进入到`Encrypter`类看看内部到底如何生成的。

../vendor/laravel/framework/src/Illuminate\Encryption
```php
<?php

namespace Illuminate\Encryption;

use RuntimeException;
use Illuminate\Contracts\Encryption\DecryptException;
use Illuminate\Contracts\Encryption\EncryptException;
use Illuminate\Contracts\Encryption\Encrypter as EncrypterContract;

class Encrypter implements EncrypterContract
{
    /**
     * The encryption key.
     *
     * @var string
     */
    protected $key;

    /**
     * The algorithm used for encryption.
     *
     * @var string
     */
    protected $cipher;

    /**
     * Create a new encrypter instance.
     *
     * @param  string  $key
     * @param  string  $cipher
     * @return void
     *
     * @throws \RuntimeException
     */
    public function __construct($key, $cipher = 'AES-128-CBC')
    {
        $key = (string) $key;

        if (static::supported($key, $cipher)) {
            $this->key = $key;
            $this->cipher = $cipher;
        } else {
            throw new RuntimeException('The only supported ciphers are AES-128-CBC and AES-256-CBC with the correct key lengths.');
        }
    }
    /**
     * Create a new encryption key for the given cipher.
     *
     * @param  string  $cipher
     * @return string
     */
    public static function generateKey($cipher)
    {
        return random_bytes($cipher === 'AES-128-CBC' ? 16 : 32);
    }
```
这个 `$cipher` 在配置文件 `config/app.php` 中默认为 AES-256-CBC，再看看这个函数，是一个PHP内置函数，用于生成加密安全的伪随机字节。这个内置函数具体什么原理看文档。
[random_bytes](https://www.php.net/manual/zh/function.random-bytes.php) — Generates cryptographically secure pseudo-random bytes

接下来了解下这个应用密钥的用途，~~其实它只用在了一个地方，就是 Cookie加密。~~ 凡是用[Laravel内置加密机制](https://learnku.com/docs/laravel/7.x/encryption/7477#71461b)生成的密钥都跟这个 `APP_KEY` 有关系，简单地说就是用了`encrypt `辅助函数来加密的。当 `APP_KEY` 变化，你生成的密钥则无法通过 `APP_KEY` 解密。内部原理是使用了 [openssl_encrypt](https://www.php.net/manual/zh/function.openssl-encrypt) 这个PHP内置函数，使用 `APP_KEY` 作为 key 生成的密钥。
目前用到这种对称加密方式常见的是加密 Cookie，而密码一般用的是哈希加密，是单向加密的，而且并没有用到 `APP_KEY`。
# 总结
- 更改 `APP_KEY` 不会影响用户密码
- 如果您更改 `APP_KEY`，会导致 session (通过 cookie 实现) 无效，所有当前用户被注销

# Reference：
- [浅析 App_KEY 的作用](https://learnku.com/articles/31169?order_by=vote_count&)
- [Laravel 中的 App_KEY 到底有什么用？
](https://learnku.com/laravel/t/41250)



