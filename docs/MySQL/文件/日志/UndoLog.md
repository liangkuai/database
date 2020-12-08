# Undo Log

Undo Log（撤销日志），记录事务开始前的状态，用于事务失败时的回滚操作。

用于撤消（还原）对 InnoDB 中存储的数据的变更及回滚事务，也用于实现多版本控制（MVCC）。



### 结构

Undo Log 内部由多个回滚段组成，即 Rollback segment，一共有 128 个，保存在 ibdata 系统表空间中，分别从 resg slot0 - resg slot127，每一个 resg slot，也就是每一个回滚段，内部由 1024 个 undo segment 组成。