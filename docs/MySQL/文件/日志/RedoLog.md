# Redo Log

Redo Log（重做日志）一个预写式日志（WAL），**记录事务执行后的状态，用来恢复未写入 data file 的已成功事务更新的数据**。是一种用于在数据库或数据库所在主机发生崩溃时确保数据完整性的技术。

Redo Log 记录必须在数据实际修改（buffer pool 中的脏页刷新到数据文件）之前写入磁盘。因此，对数据表做修改时，每个数据记录的修改都会写入 Redo Log Buffer 中（作为重做日志记录）。当一个页面的修改操作完成时，Redo Log Buffer 将执行 flush 到 Redo Log 文件的操作（但此时可能并未 sync 到磁盘）。


> #### 1. WAL，Write-ahead logging，预写式日志
> 关键点就是先写日志，再写磁盘。
>
> 具体来说，当有一条记录需要更新的时候，InnoDB 就会先把记录写到 Redo Log 里面。同时，InnoDB 会在适当的时候，将这个操作记录更新到磁盘里面，而这个更新往往是在系统比较空闲的时候做。当然，在 Ledo Log 被用完，或者内存达到一定的使用比例，也会促使进行更新到 data file 磁盘的操作（刷脏）。
>
> #### 2. 刷脏（flush）
> 当内存数据页跟磁盘数据页内容不一致的时候，我们称这个内存页为“脏页”。内存数据写入到磁盘后，内存和磁盘上的数据页的内容就一致了，称为“干净页”。
>
> 什么时候刷脏？
> 1. Redo Log 写满了。
> 2. 系统内存不足。当需要新的内存数据页，而内存不够用的时候，就要淘汰一些数据页，空出内存给别的数据页使用。如果淘汰的是“脏页”，就要先将脏页写到磁盘。
> 3. MySQL 认为系统“空闲”时。
> 4. MySQL 正常关闭。

### Redo Log 写入机制
事务在执行过程中，生成的 Redo Log 是要先写到 Redo Log Buffer 的。

Redo Log 的写入策略取决于 InnoDB 提供的 `innodb_flush_log_at_trx_commit` 参考取值，
1. 设置为 0 时，表示每次事务提交时都只是把 Redo Log 留在 Redo Log Buffer 中；
2. 设置为 1 时，表示每次事务提交时都将 Redo Log 直接持久化到磁盘；
3. 设置为 2 时，表示每次事务提交时都只是把 Redo Log 写到 page cache。

InnoDB 有一个后台线程，每隔 1 秒，就会把 Redo Log Buffer 中的日志，调用 write 写到文件系统的 page cache，然后调用 `fsync` 持久化到磁盘。

通常采取 “双1” 策略，`innodb_flush_log_at_trx_commit` 和 `sync_binlog` 都设置成 1。也就是说，一个事务完整提交前，需要等待两次刷盘（写到磁盘），一次是 Redo Log（prepare 阶段），一次是 Binlog。
- Redo Log 和 Binlog都是顺序写，磁盘的顺序写比随机写速度要快；
- 组提交机制，可以大幅度降低磁盘的IOPS消耗。


### 文件原理
Redo Log 是固定大小的文件，默认是 2 个。从头开始顺序写，写到末尾就又回到开头循环写。比随机写的效率高很多。

其中有两个指针一样的标记，
- write pos ：指定当前记录的位置；
- checkpoint ：当前需要进行擦除的位置，擦除记录前要把记录更新到数据文件。在故障回复时候，只需要 redo/undo 最近的一次 checkpoint 之后的操作。


### 作用
有了 Redo Log，InnoDB 就可以保证即使数据库发生异常重启，之前提交的记录都不会丢失，这个能力称为 crash-safe。