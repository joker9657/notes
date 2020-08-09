> 什么是命名空间？从广义上来说，命名空间是一种封装事物的方法。在很多地方都可以见到这种抽象概念。例如，在操作系统中目录用来将相关文件分组，对于目录中的文件来说，它就扮演了命名空间的角色。具体举个例子，文件 foo.txt 可以同时在目录/home/greg 和 /home/other 中存在，但在同一个目录中不能存在两个 foo.txt 文件。另外，在目录 /home/greg 外访问 foo.txt 文件时，我们必须将目录名以及目录分隔符放在文件名之前得到 /home/greg/foo.txt。这个原理应用到程序设计领域就是命名空间的概念。

> 在PHP中，命名空间用来解决在编写类库或应用程序时创建可重用的代码如类或函数时碰到的两类问题：
>
> - 用户编写的代码与PHP内部的类/函数/常量或第三方类/函数/常量之间的名字冲突。
> - 为很长的标识符名称(通常是为了缓解第一类问题而定义的)创建一个别名（或简短）的名称，提高源代码的可读性。

上面两段引用是 [PHP 文档](https://www.php.net/manual/zh/language.namespaces.rationale.php) 中命名空间的含义及用途。

在了解如何使用命名空间之前，首先了解类名引入的三种方式。

> PHP 命名空间中的元素使用同样的原理。例如，类名可以通过三种方式引用：
> - 非限定名称，或不包含前缀的类名称，例如 $a=new foo(); 或 foo::staticmethod();。如果当前命名空间是 currentnamespace，foo 将被解析为 currentnamespace\foo。如果使用 foo 的代码是全局的，不包含在任何命名空间中的代码，则 foo 会被解析为foo。 警告：如果命名空间中的函数或常量未定义，则该非限定的函数名称或常量名称会被解析为全局函数名称或常量名称。详情参见 使用命名空间：后备全局函数名称/常量名称。
> - 限定名称,或包含前缀的名称，例如 $a = new subnamespace\foo(); 或 subnamespace\foo::staticmethod();。如果当前的命名空间是 currentnamespace，则 foo 会被解析为 currentnamespace\subnamespace\foo。如果使用 foo 的代码是全局的，不包含在任何命名空间中的代码，foo 会被解析为subnamespace\foo。
> - 完全限定名称，或包含了全局前缀操作符的名称，例如， $a = new \currentnamespace\foo(); 或 \currentnamespace\foo::staticmethod();。在这种情况下，foo 总是被解析为代码中的文字名(literal name)currentnamespace\foo。

最后了解一个常用的关键字 `::class`。
> 自 PHP 5.5 起，关键词 class 也可用于类名的解析。使用 ClassName::class 你可以获取一个字符串，包含了类 ClassName 的完全限定名称。这对使用了 命名空间 的类尤其有用。

```php
<?php
namespace NS {
    class ClassName {
    }
    
    echo ClassName::class; // 输出字符串 NS\ClassName
}
?>
```

## Reference
- [::class 关键词](https://www.php.net/manual/zh/language.oop5.basic.php#language.oop5.basic.class.class)
- [使用命名空间：基础](https://www.php.net/manual/zh/language.namespaces.basics.php)