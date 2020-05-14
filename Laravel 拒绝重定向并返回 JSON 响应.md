# 问题
重定向至 /login 登录页面
> Laravel 默认设置捕获异常后响应为跳转至页面，而我们希望应用只返回json格式数据。


以下为源码定位

app\Exceptions\Handle.php
```php
<?php

namespace App\Exceptions;

use Exception;
use Illuminate\Foundation\Exceptions\Handler as ExceptionHandler;

class Handler extends ExceptionHandler
{
    /**
     * A list of the exception types that are not reported.
     *
     * @var array
     */
    protected $dontReport = [
        //
    ];

    /**
     * A list of the inputs that are never flashed for validation exceptions.
     *
     * @var array
     */
    protected $dontFlash = [
        'password',
        'password_confirmation',
    ];

    /**
     * Report or log an exception.
     *
     * @param  \Exception  $exception
     * @return void
     */
    public function report(Exception $exception)
    {
        parent::report($exception);
    }

    /**
     * Render an exception into an HTTP response.
     *
     * @param  \Illuminate\Http\Request  $request
     * @param  \Exception  $exception
     * @return \Illuminate\Http\Response
     */
    public function render($request, Exception $exception)
    {
        return parent::render($request, $exception);
    }
}

```
上面是 Laravel [错误和异常处理](https://learnku.com/docs/laravel/7.x/errors/7468) `app\exceptions\handler` 类， 用于记录应用程序触发的所有异常。它继承了`Illuminate\Foundation\Exceptions\Handler`类。

Illuminate\Foundation\Exceptions\Handler
```php
···

/**
 * Render an exception into a response.
 *
 * @param  \Illuminate\Http\Request  $request
 * @param  \Exception  $e
 * @return \Illuminate\Http\Response|\Symfony\Component\HttpFoundation\Response
 */
public function render($request, Exception $e)
{
    if (method_exists($e, 'render') && $response = $e->render($request)) {
        return Router::toResponse($request, $response);
    } elseif ($e instanceof Responsable) {
        return $e->toResponse($request);
    }

    $e = $this->prepareException($e);

    if ($e instanceof HttpResponseException) {
        return $e->getResponse();
    } elseif ($e instanceof AuthenticationException) {
        return $this->unauthenticated($request, $e);
    } elseif ($e instanceof ValidationException) {
        return $this->convertValidationExceptionToResponse($e, $request);
    }

    return $request->expectsJson()
                    ? $this->prepareJsonResponse($request, $e)
                    : $this->prepareResponse($request, $e);
}

/**
 * Convert an authentication exception into a response.
 *
 * @param  \Illuminate\Http\Request  $request
 * @param  \Illuminate\Auth\AuthenticationException  $exception
 * @return \Symfony\Component\HttpFoundation\Response
 */
protected function unauthenticated($request, AuthenticationException $exception)
{
    // 重点函数
    return $request->expectsJson()
                ? response()->json(['message' => $exception->getMessage()], 401)
                : redirect()->guest($exception->redirectTo() ?? route('login'));
}

···

```

Illuminate\Http\Concerns\InteractsWithContentTypes
```php
<?php

namespace Illuminate\Http\Concerns;

use Illuminate\Support\Str;

trait InteractsWithContentTypes
{
     /**
     * Determine if the current request probably expects a JSON response.
     *
     * @return bool
     */
    public function expectsJson()
    {
        return ($this->ajax() && ! $this->pjax() && $this->acceptsAnyContentType()) || $this->wantsJson();
    }
}
```
举个例子，当我们用户认证失败时，捕获了`AuthenticationException`异常，调用了`unauthenticated`方法，这个方法返回了一个三元表达式，当`$request->expectsJson()`返回 true 时，响应为 json 格式, http code 为 401。返回 false 则跳转到指定链接，如果没有指定链接则跳转至 login 页面。

# 解决方案
1. 定义一个路由中间件，将请求头改为 `application/json`。
2. 重写 `Illuminate\Http\Request`。[Wiki](https://learnku.com/laravel/wikis/16069) 中就是这么做的，并不太推荐这种做法。

# References
- [Laravel 响应：永远返回 JSON 响应](https://learnku.com/laravel/wikis/16069)
- [教程中 “默认将会被重定向到 /login 登录页面”，请问如何修改？](https://learnku.com/laravel/t/7715/in-the-tutorial-the-default-will-be-redirected-to-the-login-login-page-may-i-ask-how-to-modify-this-default#reply39362)
