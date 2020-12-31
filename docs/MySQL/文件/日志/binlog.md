# Binary Log

Binary Log（二进制日志），也是归档日志。记录了对数据库执行更改的所有操作，如：insert、update、delete...（但是不包括 select 和 show 等操作，因为这类操作不会修改数据。想要记录这类操作，只能使用查询日志）

- 记录的是 SQL 语句的原始逻辑，比如：“把 id=2 这一行的 c 字段加 1”。
- binlog 是可以追加写入的。写到一定大小后会切换到下一个，并不会覆盖以前的日志。
- binlog 有两种模式，`statement` 格式的话是记 SQL 语句， `row` 格式会记录行的内容，记两条，更新前和更新后都有。
- 由 Server 层实现的，所有引擎都可以使用。


### binlog 的三种格式
#### 1. statement
原样记录了一条 SQL 的语句

#### 2. row
比如：范围删除，那就会变成需要执行一条条 SQL。一个删除10万条数据的 SQL 会变成10万条 SQL。
- 优点
    1. 当 binlog_format 使用 row 格式的时候，binlog 里面记录了真实删除行的主键 id，这样 binlog 传到备库去的时候，就肯定会删除 `id = 4` 的行，不会有主备删除不同行的问题。
    2. 恢复数据：row 格式的 binlog 也会把被删掉/插入/修改的行的整行信息保存起来。
- 缺点
    1. 占用更大的空间，同时写 binlog 也要耗费 IO 资源，影响执行速度。

#### 3. mixed
MySQL 自己判断这条 SQL 语句是否可能引起主备不一致，如果有可能，就用 row 格式，否则就用 statement 格式。


### binlog 写入机制

binlog 的写入逻辑比较简单：
1. 事务执行过程中，先把日志写到 binlog cache；
2. 事务提交的时候，再把 binlog cache 写到 binlog 文件中（不过在 `fsync` 之前要确保 Redo Log 先进行 `fsync`）。

一个事务的 binlog 是不能被拆开的，因此不论这个事务多大，也要确保一次性写入。这就涉及到了 binlog cache 的保存问题。如果 cache 不够写，就写到磁盘文件，然后通过归并算法写到磁盘 binlog 文件。

`write` 和 `fsync` 的时机，是由参数 `sync_binlog` 控制的，
1. `sync_binlog = 0`，表示每次提交事务都只 `write`，不 `fsync`；
2. `sync_binlog = 1`，表示每次提交事务都会执行 `fsync`；
3. `sync_binlog = N（N > 1）`，表示每次提交事务都 `write`，但累积 N 个事务后才 `fsync`。