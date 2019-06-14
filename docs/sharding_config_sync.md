# MongoDB分片配置的实时同步

> 本文内容适用于MongoDB v3.2及以上版本

- [数据库分片](#数据库分片)
- [集合分片](#集合分片)
- [实时同步原理](#实时同步原理)

分片集群在config server存储集群的各种配置信息，比如节点地址、分片配置、数据分布等，可连接mongos切换到`config`数据库查询相关信息。

分片操作包括`enableSharding`和`shardCollection`命令，分别作用于数据库和集合，下面从这两个方面分别说明。

## 数据库分片

`enableSharding`命令用于开启数据库的分片状态，只有当数据库开启分片后，其集合才能进一步分片。

`config.database`集合存储全部数据库的meta信息，例如：

```shell
mongos> use config
switched to db config
mongos> db.databases.find()
{ "_id" : "test", "primary" : "s1", "partitioned" : true }
{ "_id" : "test1", "primary" : "s2", "partitioned" : false }
{ "_id" : "test2", "primary" : "s4", "partitioned" : true }
{ "_id" : "test3", "primary" : "s6", "partitioned" : true }
```

|字段|说明|
|--|--|
|_id|数据库名称|
|primary|主shard|
|partitioned|分片状态（true / false）|

数据库的meta记录遵循以下规则：

- 创建数据库，对应的meta记录写入`config.databases`
- 删除数据库，对应的meta记录从`config.databases`删除
- 对于一个已存在但未开启分片的数据库执行分片操作，其meta记录的`partitioned`字段产生状态变化（false => true）

## 集合分片

`shardCollection`命令用于对集合分片。

`config.collections`仅存储分片集合的meta信息，例如：

```shell
mongos> use config
switched to db config
mongos> db.collections.find()
{ "_id" : "test.coll", "lastmodEpoch" : ObjectId("5d03228754bfbe35e837a246"), "lastmod" : ISODate("1970-02-19T17:02:47.301Z"), "dropped" : false, "key" : { "_id" : "hashed" }, "unique" : false }
{ "_id" : "test2.coll", "lastmodEpoch" : ObjectId("5d03229c54bfbe35e837a24c"), "lastmod" : ISODate("1970-02-19T17:02:47.296Z"), "dropped" : false, "key" : { "_id" : 1 }, "unique" : true }
{ "_id" : "test3.coll", "lastmodEpoch" : ObjectId("000000000000000000000000"), "lastmod" : ISODate("2019-06-14T04:31:49.622Z"), "dropped" : true }
```

|字段|说明|
|--|--|
|_id|集合namespace|
|lastmodEpoch|更新时间点相关|
|lastmod|更新时间点相关|
|dropped|是否已删除（true / false）|
|key|片键|
|unique|片键唯一性约束（true / false）|

集合的meta记录遵循以下规则：

- 对一个新集合分片，对应的meta记录写入`config.collections`
- 删除一个分片集合，其meta记录的`dropped`字段产生状态变化（false => true，meta记录不会从`config.collections`删除)
- 对一个已经删除的分片集合重新分片，其meta记录的`dropped`字段产生状态变化（true => false，此时meta记录已经存在）

## 实时同步原理

从MongoDB 3.2版本起，分片集群config server支持复制集，为实时同步提供了基础。

分片配置同步的原理与数据同步类似，监听并回放config server的oplog，只是回放逻辑需要依据以下规则：

- `{ns: ‘config.databases’, op: ’i‘， partitioned: false}`

    新建数据库

- `{ns: 'config.databases', op: 'i'， partitioned: true}`

    新建数据库并执行分片

- `{ns: 'config.databases', op: 'd'}`

    删除数据库

- `{ns: 'config.databases', op: 'u'}`

    对数据库执行分片，此oplog表示对未分片的数据库执行分片

- `{ns: 'config.collections', op: 'i'}`

    新建集合并执行分片

- `{ns: 'config.collections', op: 'u'， dorpped: false}`

    对集合执行分片，此oplog表示对一个已经删除的分片集合重新分片

- `{ns: 'config.collections', op: 'u'， dorpped: true}`

    删除集合
