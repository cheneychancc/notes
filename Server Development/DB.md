## 事务

> 把一组数据库操作当成一个整体，要么全部成功，要么全部失败。

### 特性

1. 原子性（Atomicity）
  要么全部成功，要么全部失败。
2. 一致性（Consistency）
  数据库必须保持正确状态。
3. 隔离性（Isolation）
  多个事务同时执行时，互相不能干扰。
4. 持久性（Durability）
  事务一旦 COMMIT，数据必须永久保存。


### 隔离

> Mysql隔离级别默认为REPEATABLE READ， 别的数据库不一定。

| 隔离级别            | 同一事务两次查询 |
| --------------- | -------- |
| READ COMMITTED  | 可能不同     |
| REPEATABLE READ | 一定相同     |
