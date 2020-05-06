# 需求
最近小程序上有个需求，需要对一个文章列表根据**多个标签**进行筛选。

articles | article_tags | tags
---|---|---
id | article_id | id
 &nbsp; | tag_id |

一个简单的多对多关联模型关系

# 解决方案
需求：根据选中的多个标签查询满足条件的文章。  
一开始我的做法是
```php
$article_ids = DB::table('article_tags')->whereIn('tag_id', $tag_ids)->pluck('article_id')->all(); // 满足条件的文章ID
```
后来跟前端联调时发现逻辑不对，需要取出同时拥有这些标签的文章，而不是拥有这些标签的所有文章。满足条件的是从不定个数的数组中取交集。  
经过一番搜索，有老铁使用了 PHP 中的函数  [array_intersect_assoc](https://www.php.net/manual/zh/function.array-intersect-assoc) 和 [call_user_func_array](https://www.php.net/manual/zh/function.call-user-func-array) 来实现。

```php
$arr1 = ["16","2","3","6","9","17"];
$arr2 = ["16","2","3","9","15"];
$arr3 = ["16","2","3","6","8","17"];
$result_array = call_user_func_array ('array_intersect_assoc', array($arr1, $arr2,$arr3));
echo '<pre>';
print_r($result_array);// ["16","2","3"]
```
看起来这个做法没甚问题，但当我运用到项目中时，却怎么都得不到正确的结果。去翻了[手册](https://www.php.net/manual/zh/function.array-intersect-assoc)。 `array_intersect_assoc()` 返回一个数组，该数组包含了所有在 array1 中也同时出现在所有其它参数数组中的值。注意和 [array_intersect()](https://www.php.net/manual/zh/function.array-intersect.php) 不同的是**键名也用于比较**。原来这函数是键值都需要匹配，但是我的需求只是值相等，取交集。以下做法实现了功能需求。

```php
$article_ids = [];
if ($tag_ids) {
    foreach ($tag_ids as $key => $tag_id) {
        $id = DB::table('article_tags')
                    ->where('tag_id', $tag_id)
                    ->pluck('article_id')
                    ->all(); // 符合标签条件的文章ID
        if ($key === 0) {
            $article_ids = $id;
            continue;
        }
        $article_ids = array_intersect($article_ids, $id); // 计算两数组的交集
    }
}
return $article_ids;
```

# 总结
[array_intersect](https://www.php.net/manual/zh/function.array-intersect.php) — 计算数组的交集  
[array_intersect_assoc](https://www.php.net/manual/zh/function.array-intersect-assoc) —     带**索引**检查计算数组的交集  
[call_user_func_array()](https://www.php.net/manual/zh/function.call-user-func-array) — 调用回调函数，并把一个数组参数作为回调函数的参数

## 参考文章：
[PHP取不定个数数组交集](https://segmentfault.com/a/1190000016877744)

抛砖引玉，假如有更好的做法请告知，求知若渴。
