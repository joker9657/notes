# Laravel 多对多关联查询记录
问题：博客与标签多对多，查询出**同时**带有多个标签的博客数据

写法一：
```php
Article::with('tags')->whereHas('roles', function($query) {
    $query->whereIn('tag_id', [2, 4])
          ->groupBy('article_id')
          ->havingRaw('COUNT(article_id) = ?', [2]);
})->get();
```
写法二：
```php
$article_tag_ids = DB::table('article_tags')
                ->whereIn('tag_id', [2, 4])
                ->groupBy('article_id')
                ->havingRaw('COUNT(article_id) = ?', [2])
                ->get()
                ->pluck('article_id')
                ->toArray();
```


# 参考：
[Laravel 多对多关联查询](https://segmentfault.com/q/1010000018450663)