# 数据库

### 1. [SQL](./docs/SQL/README.md)

- [ ] DDL
- [ ] DML


### 2. [事务](./docs/事务/README.md)

- 事务特性（ACID）
    - 原子性
    - 一致性
    - 隔离性
        - [事务隔离级别](./docs/事务/事务隔离级别.md)
    - 持久性
- ACID 之间的关系



## MySQL

### 1. 架构

- [服务端](/docs/MySQL/架构/服务端.md)
- [存储引擎](/docs/MySQL/架构/存储引擎.md)
    - [ ] InnoDB
    - [ ] MyISAM
- [ ] 客户端


### 3. 索引

- [B+树](/docs/MySQL/索引/B+树.md)
- [ ] [InnoDB 索引](/docs/MySQL/索引/InnoDB索引.md)


### 4. MySQL 事务

- [intro](/docs/MySQL/事务.md)


### 5.（事务）并发控制
- [intro](/docs/MySQL/并发控制/README.md)
- 悲观并发控制
    - [x] [锁机制](/docs/MySQL/并发控制/锁机制.md)
        - [x] [（InnoDB）锁算法](/docs/MySQL/并发控制/锁算法.md)
- 乐观并发控制
    - 版本机制
        - [x] [多版本并发控制（MVCC）](/docs/MySQL/并发控制/多版本并发控制.md)

### 6. 文件
- 配置文件
- 日志
    - [ ] [Binary Log](/docs/MySQL/文件/日志/binlog.md)
    - [x] [Redo Log](/docs/MySQL/文件/日志/RedoLog.md)
    - [ ] [Undo Log](/docs/MySQL/文件/日志/UndoLog.md)

### 7. 部署模式
- [ ] [主从复制](/docs/MySQL/主从复制.md)

### 8. 优化
- [intro](/docs/MySQL/优化/README.md)
    - 数据库的瓶颈
- [ ] [1. 数据库设计优化](/docs/MySQL/优化/数据库设计优化.md)
    - [ ] [读写分离](/docs/MySQL/优化/读写分离.md)
    - [x] [分表](/docs/MySQL/优化/分表.md)
- [ ] [2. 查询优化](/docs/MySQL/优化/查询优化.md)
    - [ ] [explain](/docs/MySQL/优化/explain.md)