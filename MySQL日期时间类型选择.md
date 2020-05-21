在设计数据库结构时，常常面临日期时间类型的选择问题，到底是选用 `datetime` 还是 `timestamp`，亦或者是 `date`，甚至是 `int`。
回想起刚入门的时候，前端要求存储**13位**的 unix 时间戳，当时不太理解原因，因为在此之前存储日期时间大都用的 `datetime` 或者 `timestamp`，只能理解为一种约定吧。今天稍微深入理解下这几种日期时间类型的区别和使用场景。

# datetime 和 timestamp
1. datetime 类型格式为 'YYYY-MM-DD hh:mm:ss'，范围是 '1000-01-01 00:00:00'——'9999-12-31 23:59:59'，占用的存储空间为8个字节    
2. timestamp 类型 存储国际标准时间(Coordinated Universal Time)，范围是 '1970-01-01 00:00:01'——'2038-01-19 03:14:07'，占用的存储空间为4个字节  
3. 除了存储时间范围跟占用空间的区别，还有一个关键点在于 `timestamp` 是带时区的，MySQL 将 TIMESTAMP 值从当前时区转换为 UTC 以进行存储，然后从 UTC 转换为当前时区以进行检索。 

# timestamp 和 unix timestamp
1. unix timestamp 通常使用 `int` 进行存储，在数据库管理工具中查看不方便。 timestamp 显得更直观。
2. unix timestamp 取出后还需要进行格式转换。

# 总结
选择使用哪种日期时间类型还是看使用场景，没有哪个类型是完美通用的。假如没有时间范围限制，我倾向于 `timestamp`。



# Reference
- [The DATE, DATETIME, and TIMESTAMP Types](https://dev.mysql.com/doc/refman/8.0/en/datetime.html)
- [Mysql时间字段格式如何选择，TIMESTAMP，DATETIME，INT？](https://segmentfault.com/q/1010000000121702)
- [选择合适的 MySQL 日期时间类型来存储你的时间
](https://learnku.com/laravel/t/2495/select-the-appropriate-mysql-date-time-type-to-store-your-time)
- [What difference between the DATE, TIME, DATETIME, and TIMESTAMP Types](https://stackoverflow.com/questions/31761047/what-difference-between-the-date-time-datetime-and-timestamp-types/56138746)
