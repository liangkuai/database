# 数据库

### [事务](/docs/事务)
- 事务特性（ACID）
    - [x] 原子性
    - [x] 一致性
    - [x] 隔离性
        - [x] [事务隔离级别](/docs/事务/事务隔离级别.md)
    - [x] 持久性
- ACID 之间的关系


## MySQL

### 架构
- [x] [服务端](/docs/MySQL/架构/服务端.md)
- [存储引擎](/docs/MySQL/架构/存储引擎.md)
    - [ ] InnoDB
    - [ ] MyISAM
- [ ] 客户端

### [SQL](/docs/MySQL/SQL)
- [ ] DDL
- [ ] DML

### [MySQL 事务](/docs/MySQL/事务.md)

### [（事务）并发控制](/docs/MySQL/并发控制)
- 悲观并发控制
    - [x] [锁机制](/docs/MySQL/并发控制/锁机制.md)
        - [x] [（InnoDB）锁算法](/docs/MySQL/并发控制/锁算法.md)
- 乐观并发控制
    - 版本机制
        - [x] [多版本并发控制（MVCC）](/docs/MySQL/并发控制/多版本并发控制.md)

### 索引


### 部署模式
- [ ] [主从复制](/docs/MySQL/主从复制.md)

### 优化
- [ ] [查询优化](/docs/MySQL/优化/查询优化.md)
    - [ ] [explain](/docs/MySQL/SQL/explain.md)
- [x] [数据库设计优化](/docs/MySQL/优化/数据库设计优化.md)
    - 垂直分表
    - 水平分表
    - 读写分离