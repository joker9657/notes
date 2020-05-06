# 前提
Redis 3.2版本后增加了对GEO(地理位置)的支持  
使用条件：redis version >= 3.2.0
# 命令用法

command | description
---|---
GEOADD | 将给定的空间元素（纬度、经度、名字）添加到指定的键里面。 
GEOPOS | 从键里面返回所有给定位置元素的位置（经度和纬度）。
GEODIST | 返回两个给定位置之间的距离。
GEORADIUS | 以给定的经纬度为中心， 返回键包含的位置元素当中， 与中心的距离不超过给定最大距离的所有位置元素。
GEORADIUSBYMEMBER | 类似GEORADIUS，区别在于中心点为 Member 而非给定的经纬度
GEOHASH | 返回一个或多个位置元素的 [Geohash](https://en.wikipedia.org/wiki/Geohash) 表示
具体参数用法 => [Redis 命令参考](http://redisdoc.com/geo/index.html)

# 原理
[降维打击](https://learnku.com/articles/30821#60645d)
# 具体使用
PHP 使用 Redis 有两种方法；
1. [phpredis](https://github.com/phpredis/phpredis) 使用 C语言实现的 PHP 扩展，需要编译[安装](https://github.com/phpredis/phpredis/blob/develop/INSTALL.markdown)。
2. [predis](https://github.com/nrk/predis) 使用 PHP 语言实现的 Redis 客户端，不依赖第三方扩展，通过 Composer 安装即可使用，方便快捷；遗憾的是，开源作者基本放弃维护这个库了，[Maintaining Predis alive needs community's help](https://github.com/nrk/predis/issues/587)

记录本文时使用第二种方法，其实推荐第一种了。

Redis geo操作中只包含了“增”和“查”的操作，并没有专门的“删除”命令。主要是因为 Redis 内部使用有序集合(zset)保存位置对象，可用 zrem 进行删除。




# 参考文章：
[Redis 命令参考](http://redisdoc.com/geo/index.html)  
[基于 Redis 的 LBS 尝试](https://learnku.com/articles/35871)