# 数据库

Parse Server 让你使用[MongoDB](https://www.mongodb.org/) 或者[Postgres](https://www.postgresql.org/)作为数据库.

我们推荐使用MongoDB 但是 Postgres 是一个好的选项,如果你创建新项目而且希望拥有一个稳定的数据库对象集合.

## MongoDB

如果你之前没有使用过MongoDB的话, 建议你先熟悉一下.

Parse Server 需要的Mongo环境如下:

* MongoDB 版本 2.6.X, 3.0.X, or 3.2.X
* 推荐使用SSL链接 (非必需).

如果这是你第一次创建 MongoDB 生产实例, 我们推荐你使用 [mLab](http://www.mLab.com) 或者 [ObjectRocket](https://objectrocket.com/). 这是两个 database-as-a-service 服务提供商,提供你可以完整管理的 MongoDB 实例, 还可以动态调整数据库大小配置套餐.

当你的Parse应用使用MongoDB的时候, 你需要自己管理你的索引(重点). 而且当数据增长了之后需要自己去扩容你的数据库.

如果你计划在你自己的机器上运行MongoDB, 我们强烈推荐你使用[RocksDB Storage Engine](#using-mongodb--rocksdb).

## Postgres

Parse Server需要的Postgre环境如下:

* Postgres version 9.5
* PostGIS extensions 2.3

当你使用有效的Postgre URL配置时,Postgre 数据库适配器将会自动加载, 例如: `postgres://localhost:5432`.

### 附加说明

* Join表是在内存中处理, there is no performance improvements using Postgres over MongoDB for relations or pointers.
* 运行ALTER TABLE的时候Schema会变化, 因此我们推荐你在你的表结构还未完全确定的时候创建自己的Schema(节省因为修改表结构创建Schema的操作).
* 正确设置表索引使性能最大化.
