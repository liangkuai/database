# explain

explain 用来分析 `select` 查询语句，根据 explain 执行结果来优化查询语句。

- id
- select_type
- table
- partitions
- type
- possible_keys
- key
- key_len
- ref
- rows
- filtered
- Extra


### id
- id 相同，执行顺序由上至下
- id 不同，如果是子查询，id 的序号会递增，id 值越大优先级越高，越先被执行
- id 相同不同，同时存在


### type
> 效率由高到低： system > const > eq_ref > ref > range > index > all
>
> 一般来说，得保证查询至少达到 range 级别，最好能达到 ref。

#### all
全表扫描

#### index
这种连接类型是另一种形式的全表扫描，只不过它的扫描顺序是按照索引的顺序。这种扫描根据索引然后回表取数据，和 all 相比，他们都是取得了全表的数据，而且 index 要先读索引而且要回表随机取数据，因此 index 不可能会比 all 快（取同一个表数据），但为什么官方的手册将它的效率说的比 all 好，唯一可能的原因在于，按照索引扫描全表的数据是有序的。这样一来，结果不同，也就没法比效率的问题了。

#### range
range 指的是有范围的索引扫描，相对于 index 的全索引扫描，有范围限制，因此要优于 index。关于 range 比较容易理解，需要记住的是出现了 range，则一定是基于索引的。常见的 between ... and，<，> 也是索引范围扫描。

#### ref
这种连接类型说明：查询条件列使用了索引而且不是主键和 unique索引。即使用了索引，但索引列的值不是唯一。

这样即使使用索引快速查找到了第一条数据，仍然不能停止，要进行目标值附近的小范围扫描。但它的好处是它并不需要扫全表，因为索引是有序的，即便有重复值，也是在一个非常小的范围内扫描。

#### ref-eq
相比于 ref，使用主键和 unique 索引。

#### const
使用主键或者唯一索引进行查询，且只有一行数据匹配。
通常情况下，如果将一个主键和唯一索引的列放置到 where 后面作为条件查询，MySQL 优化器就能把这次查询优化转化为一个常量。至于如何转化以及何时转化，这个取决于优化器。
#### system
const type 的特殊情况，表里只有一行数据时触发。


### rows
根据表统计信息及索引选用情况，大致估算出找到所需的记录所需要读取的行数，也就是说，用的越少越好。


### Extra

- `Useing index` 表示使用到覆盖索引，避免了回表操作，效率不错
- `Using index condition` 表示先条件过滤索引，过滤完索引后找到所有符合索引条件的数据行，随后用 WHERE 子句中的其他条件去过滤这些数据行
- `Using Where` 表示 server 在存储引擎收到行后会进行按 where 条件过滤，过滤条件字段无索引
- `Using filesort` 表示需要依赖排序算法来实现排序，数据较少时从内存排序，否则从磁盘排序
- `Using temporay` 表示 MySQL 需要创建一个临时表来保存结果,例如查询包含不同列的 GROUP BY 和 ORDER BY 子句。
- `Using join buffer` (Block Nested Loop) 表示 join 的时候没有用到索引，得进行 join buffer 中进行join


### 参考
- [MySQL 知识点 - AobingJava](https://mp.weixin.qq.com/s/J3kCOJwyv2nzvI0_X0tlnA)
