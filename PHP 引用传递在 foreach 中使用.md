# PHP 引用
## 引用的概念
在 PHP 中引用意味着用不同的名字访问同一个变量内容。引用可以被看作是 Unix 文件系统中的硬链接。**引用不是指针。**

## 引用的用途
PHP 的引用允许用两个变量来指向同一个内容。意思是，当这样做时：
```php
<?php
$a = 24;
$b = &$a;
var_dump($b); // int(24)
$b = 8;
var_dump($a); // int(8)
```
这意味着 $a 和 $b 指向了同一个变量。

可以将一个变量通过引用传递给函数，这样该函数就可以修改其参数的值。语法如下：
```php
<?php
function foo(&$var)
{
    $var++;
}

$a=5;
foo($a);
// $a is 6 here
```
引用传递在 `foreach` 中使用，如下：
```php
$a = [1, 2, 3, 4];
foreach($a as &$v){
    $v *= $v;
}
foreach($a as $v) {
    echo $v; // output:1499
}
// 两个 foreach 具体流程
$a[0] = &$v; // $v = $a[0] => 1; $v *= $v == 1
$a[1] = &$v; // $v = $a[1] => 2; $v *= $v == 4
$a[2] = &$v; // $v = $a[2] => 3; $v *= $v == 9
$a[3] = &$v; // $v = $a[3] => 4; $v *= $v == 16

$a = [1, 4, 9, 16];
$v = $a[0]; // 1
$v = $a[1]; // 4
$v = $a[2]; // 9 $v = $a[3] => 9
$v = $a[3]; // 9
```

## 扩展延伸
### PHP7 不向后兼容特性 —— foreach 的变化
[foreach](https://www.php.net/manual/zh/control-structures.foreach.php) 发生了细微的变化，控制结构， 主要围绕阵列的内部数组指针和迭代处理的修改。
1. foreach不再改变内部数组指针  
在PHP7之前，当数组通过 foreach 迭代时，数组指针会移动。现在开始，不再如此，见下面代码
```php
<?php
$array = [0, 1, 2];
foreach ($array as &$val) {
    // in php5 output: int(1)、int(2)、bool(false)
    // in php7 output: int(0)、int(0)、int(0)
    var_dump(current($array));  
}
```
2. foreach 通过值遍历时，操作的值为数组的副本  
当默认使用通过值遍历数组时，foreach 实际操作的是数组的迭代副本，而非数组本身。这就意味着，foreach 中的操作不会修改原数组的值。
3. foreach通过引用遍历时，有更好的迭代特性   
当使用引用遍历数组时，现在 foreach 在迭代中能更好的跟踪变化。例如，在迭代中添加一个迭代值到数组中，参考下面的代码：
```php
<?php
$array = [0];
foreach ($array as &$val) {
    // in php5 output: int(0)
    // int php7 output: int(0)、int(1)
    var_dump($val);
    $array[1] = 1;
}
```

# Reference
- [深入理解PHP之foreach](https://segmentfault.com/a/1190000015753252)
- [不向后兼容的变更](https://www.php.net/manual/zh/migration70.incompatible.php#migration70.incompatible.foreach.by-ref)