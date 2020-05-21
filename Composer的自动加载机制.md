> Composer is a tool for dependency management in PHP. It allows you to declare the libraries your project depends on and it will manage (install/update) them for you.

[Composer](https://getcomposer.org/) 是 PHP 的依赖管理工具，现代化 PHP 应用的不可或缺的基石。它有一个非常重要的特点——自动加载。
这篇文章记录下我理解的 Composer 自动加载机制。

## long long ago
很久很久以前，我尚未踏足编程领域前，大约是 PHP 5 版本前，PHP 是面向过程开发的，那时候要引用外部其他文件是通过`require`、`include`、`require_once`、`include_once`等函数手动的、显式的加载。这种加载方式应该是继承自 C语言。优点是简单、直观。但是其缺点会在后面的维护阶段逐渐放大。

## until PHP 5
上面说的什么缺点呢？假设你维护的某个项目，在一个 PHP 文件中发现引入超过100个其他文件，对其做添加修改操作显得尤为困难。后来 PHP 5 完全重写了对象模型以得到更佳性能和更多特性。这是自 PHP 4 以来的最大变化。PHP 5 具有完整的对象模型。 PHP 5 内置了 [__autoload函数](https://www.php.net/manual/zh/function.autoload)，它的作用是尝试加载未定义的类。这个函数即可实现类的自动加载。
```php
<?php
// 简单示例，根据类名确定类文件名，再确定类文件所在路径，最后将其加载进来
function __autoload($classname) {
    require_once("./" . $classname . ".class.php");
}
```
autoload 机制可以使得 PHP 程序有可能在使用类时才自动包含类文件，而不是一开始就将所有的类文件 include 进来，这种机制也称为 lazy loading。这个机制看似解决了上述问题，但是后来又出现了新的问题。在一个应用中要引入其他人的类库，其类名与实际的磁盘文件之间的映射规则可能不一致，要实现所有的映射规则可能非常复杂而难以维护，如何才能更好的实现类的自动加载？

## now
PHP 5引入了 [SPL](https://www.php.net/manual/en/intro.spl.php)(Standard PHP Library) 的扩展库。其主要功能包括 autoload 机制的实现及包括各种 Iterator 接口或类。 如果需要多条 autoload 函数，[spl_autoload_register()](https://www.php.net/manual/zh/function.spl-autoload-register.php) 满足了此类需求。 它实际上创建了 autoload 函数的队列，按定义时的顺序逐个执行。相比之下， __autoload() 只可以定义一次。

# summary
Composer 的自动加载机制主要由 [spl_autoload_register()](https://www.php.net/manual/zh/function.spl-autoload-register.php)、[命名空间](https://www.php.net/manual/zh/language.namespaces.rationale.php)、[PHP PSR 标准规范](https://learnku.com/docs/psr) 等构成。


## Tips
[__autoload](https://www.php.net/manual/zh/function.autoload) 在 PHP 7.2版本后被弃用了，强烈建议不要使用这个函数。
> Warning
This feature has been DEPRECATED as of PHP 7.2.0. Relying on this feature is highly discouraged.

[SPL](https://www.php.net/manual/en/intro.spl.php):Standard PHP Library(标准PHP库)的缩写
> The Standard PHP Library (SPL) is a collection of interfaces and classes that are meant to solve common problems.
No external libraries are needed to build this extension and it is available and compiled by default in PHP 5.0.0.
SPL provides a set of standard datastructure, a set of iterators to traverse over objects, a set of interfaces, a set of standard Exceptions, a number of classes to work with files and it provides a set of functions like spl_autoload_register()

# Reference
- [Composer](https://getcomposer.org/)
- [PHP autoload 机制详解](https://segmentfault.com/a/1190000006188247)
- [PHP 自动加载功能原理解析](https://learnku.com/articles/4681/analysis-of-the-principle-of-php-automatic-loading-function)
- [深入解析 composer 的自动加载原理](https://segmentfault.com/a/1190000014948542)
- [__autoload](https://www.php.net/manual/zh/function.autoload)
- [spl_autoload_register](https://www.php.net/manual/zh/function.spl-autoload-register.php)

