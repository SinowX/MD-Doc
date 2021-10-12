# Redis

 

## 基础命令

### 进入redis CMLine

```bash
redis-cli -h host -p port -a password
```

### key 命令

```bash
redis 127.0.0.1:6379> SET runoobkey redis
OK
redis 127.0.0.1:6379> DEL runoobkey
(integer) 1
```

| 命令      | 使用                                         | 描述                                       |
| --------- | -------------------------------------------- | ------------------------------------------ |
| DEL       | DEL key                                      | 删除key                                    |
| DUMP      | DUMP key                                     | 序列化给定key，并返回被序列化的值          |
| EXISTS    | EXISTS key                                   | key是否存在                                |
| EXPIRE    | EXPIRE key seconds                           | 设置过期时间，秒计                         |
| EXPIREAT  | EXPIREAT key timestamp                       | 设置过期时间，区别在于接受参数为unix时间戳 |
| PEXPIRE   | PEXPIRE key milliseconds                     | 设置过期时间，毫秒计                       |
| PEXPIREAT | PEXPIREAT key milliseconds-timestamp         | 设置过期时间，unix时间戳                   |
| KEYS      | KEY pattern                                  | 查找符合给定pattern的key                   |
| MOVE      | MOVE key db                                  | 移动key到给定数据库db                      |
| PERSIST   | PERSIST key                                  | 无过期时间                                 |
| PTTL      | PTTL key                                     | 返回剩余过期时间，毫秒                     |
| TTL       | TTL key                                      | 返回剩余过期时间，秒                       |
| RANDOMKEY | RANDOMKEY                                    | 从当前数据库随机返回一个key                |
| RENAME    | RENAME key newkey                            | 修改key名称                                |
| RENAMENX  | RENAMENX key                                 | 修改key名称，若新名称被占用，则不更改      |
| SCAN      | SCAN cursor [MATCH pattern] [COUNT count]key | 迭代数据库key                              |
| TYPE      | TYPE key                                     | 返回值类型                                 |

### String 命令

```bash
redis 127.0.0.1:6379> SET runoobkey redis
OK
redis 127.0.0.1:6379> GET runoobkey
"redis"
```

| 命令        | 使用                          | 描述                                  |
| ----------- | ----------------------------- | ------------------------------------- |
| SET         | SET key value                 | 设置value                             |
| GET         | GET key                       | 获取value                             |
| GETRANGE    | GETRANGE key start end        | 获取子串                              |
| GETSET      | GETSET key value              | 设置value并返回old_value              |
| GETBIT      | GETBIT key offset             | 获取string的指定位                    |
| MGET        | MGET key1 [key2...]           | 获取多个value                         |
| SETBIT      | SETBIT key offset value       | 设置指定位                            |
| SETEX       | SETEX key seconds value       | 将value关联到key，并设过期时间seconds |
| SETNX       | SETNX key value               | 只有key不存在设置                     |
| SETRANGE    | SETRANGE key offset value     | 从指定位开始覆写string值              |
| STRLEN      | STRLEN key                    | 返回string长度                        |
| MSET        | MSET key value [key value]    | 同时设置多个key-value                 |
| MSETNX      | MSETNX key value [key value]  | 同上，仅当给定key不存在               |
| PSETEX      | PSETEX key milliseconds value | 同SETEX，单位为milliseconds           |
| INCR        | INCR key                      | key中数值增加1                        |
| INCRBY      | INCR key increment            | key中数值加上给定值 INT               |
| INCRBYFLOAT | INCRBYFLOAT key increment     | key中数值加上给定值 FLOAT             |
| DECR        | DECR key                      | key中数值减1                          |
| DECRBY      | DECR key decrement            | key中数值减去给定值                   |
| APPEND      | APPEND key value              | string之后加上给定子串                |

### Hash 命令

```bash
127.0.0.1:6379>  HMSET runoobkey name "redis tutorial" description "redis basic commands for caching" likes 20 visitors 23000
OK
127.0.0.1:6379>  HGETALL runoobkey
1) "name"
2) "redis tutorial"
3) "description"
4) "redis basic commands for caching"
5) "likes"
6) "20"
7) "visitors"
8) "23000"
```

| 命令         | 用法 | 描述                              |
| ------------ | ---- | --------------------------------- |
| HDEL         |      | 删除一个或多个哈洗标字段          |
| HEXISTS      |      | 字段是否存在                      |
| HGET         |      | 获取value                         |
| HGETALL      |      | 获取指定key中所有字段和值         |
| HINCRBY      |      | 为key中指定字段的整数值加上指定值 |
| HINCRBYFLOAT |      | 为key中指定字段的浮点值加上指定值 |
| HKEYS        |      | 获取key中所有字段                 |
| HLEN         |      | 获取key中字段数量                 |
| HMGET        |      | 获取给定字段的值                  |
| HMSET        |      | 将多个field-value设置到key中      |
| HSET         |      | 将key/field的值赋为value          |
| HSETNX       |      | 如上，只在field不存在时           |
| HVALS        |      | 获取hash表中所有值                |
| HSCAN        |      | 迭代hash表中键值对                |

### List 命令

```bash
redis 127.0.0.1:6379> LPUSH runoobkey redis
(integer) 1
redis 127.0.0.1:6379> LPUSH runoobkey mongodb
(integer) 2
redis 127.0.0.1:6379> LPUSH runoobkey mysql
(integer) 3
redis 127.0.0.1:6379> LRANGE runoobkey 0 10

1) "mysql"
2) "mongodb"
3) "redis"
```

| 命令       | 用法                                  | 描述                                                         |
| ---------- | ------------------------------------- | ------------------------------------------------------------ |
| BLPOP      | BLPOP key1 [key2] timeout             | 移出列表第一个元素，若空则会阻塞到超时或发现可弹出元素       |
| BRPOP      | BRPOP key1 [key2] timeout             | 移出列表最后一个元素，若空则会阻塞到超时或发现可弹出元素     |
| BRPOPLPUSH | BRPOPLPUSH source destination timeout | 弹出一个值，将弹出的元素插入到另外一个列表并返回它，若空，阻塞至超时或可弹出 |
| LINDEX     | LINDEX key index                      | 根据索引获取列表中元素                                       |
| LINSERT    | LINSERT key BEFORE\|AFTER pivot value | 列表元素前或后插入元素                                       |
| LLEN       | LLEN key                              | 返回列表长度                                                 |
| LPOP       | LPOP key                              | 弹出获取第一个元素                                           |
| LPUSH      | LPUSH key value1 [value2]             | 将一个或多个值插入到列表头部                                 |
| LPUSHX     | LPUSHX key value                      | 将一个值插入到已存在的列表头部                               |
| LRANGE     | LRANGE key start stop                 | 获取指定范围内的元素                                         |
| LREM       | LREM key count value                  | 移除列表元素                                                 |
| LSET       | LSET key index value                  | 根据索引设置列表元素的值                                     |
| LTRIM      | LTRIM key start stop                  | 保留指定区间元素，删除其他元素                               |
| RPOP       | RPOP key                              | 弹出获取最后一个元素                                         |
| RPOPLPUSH  | RPOPLPUSH source destination          | 移除最后一个元素，并添加到另一个列表并返回                   |
| RPUSH      | RPUSH key value1 [value2]             | 在列表中添加一个或多个值                                     |
| RPUSHX     | RPUSHX key value                      | 为已存在的列表添加值                                         |

### Set 命令

```bash
redis 127.0.0.1:6379> SADD runoobkey redis
(integer) 1
redis 127.0.0.1:6379> SADD runoobkey mongodb
(integer) 1
redis 127.0.0.1:6379> SADD runoobkey mysql
(integer) 1
redis 127.0.0.1:6379> SADD runoobkey mysql
(integer) 0
redis 127.0.0.1:6379> SMEMBERS runoobkey

1) "mysql"
2) "mongodb"
3) "redis"
```

| 命令        | 使用                                           | 描述                                          |
| ----------- | ---------------------------------------------- | --------------------------------------------- |
| SADD        | SADD key member1 [member2]                     | 添加成员                                      |
| SCARD       | SCARD key                                      | 获取成员数                                    |
| SDIFF       | SDIFF key1 [key2]                              | 返回第一个集合与其他集合的差集                |
| SDIFFSTORE  | SDIFFSTORE destination key1 [key2]             | 返回给定所有集合的差集并存储在destination中   |
| SINTER      | SINTER key1 [key2]                             | 返回给定所有集合的交集                        |
| SINTERSTORE | SINTERSTORE destination key1 [key2]            | 返回给定所有集合的交集并存储在destination中   |
| SISMEMBER   | SISMENBER key member                           | 判断member元素是否是集合key的成员             |
| SMEMBERS    | SMEMBERS key                                   | 返回集合中所有成员                            |
| SMOVE       | SMOVE source destination member                | 将member元素从source集合移动到destination集合 |
| SPOP        | SPOP key                                       | 弹出并返回集合中随机一个元素                  |
| SRANDMEMBER | SRANDMEMBER key [count]                        | 返回集合中一个或多个随机数                    |
| SREM        | SREM key member1 [member2]                     | 移除集合中一个或多个成员                      |
| SUNION      | SUNION key1 [key2]                             | 返回给定所有集合的并集                        |
| SUNIONSTORE | SUNIONSTORE destination key1 [key2]            | 返回给定所有集合的并集并存储在destination中   |
| SSCAN       | SSCAN key cursor [MATCH pattern] [COUNT count] | 迭代集合中元素                                |

### 有序集合

```bash
redis 127.0.0.1:6379> ZADD runoobkey 1 redis
(integer) 1
redis 127.0.0.1:6379> ZADD runoobkey 2 mongodb
(integer) 1
redis 127.0.0.1:6379> ZADD runoobkey 3 mysql
(integer) 1
redis 127.0.0.1:6379> ZADD runoobkey 3 mysql
(integer) 0
redis 127.0.0.1:6379> ZADD runoobkey 4 mysql
(integer) 0
redis 127.0.0.1:6379> ZRANGE runoobkey 0 10 WITHSCORES

1) "redis"
2) "1"
3) "mongodb"
4) "2"
5) "mysql"
6) "4"
```

| 命令             | 用法                                           | 描述                                       |
| ---------------- | ---------------------------------------------- | ------------------------------------------ |
| ZADD             | ZADD key score1 member1 [score2 member2]       | 向有序集合添加多个成员，或更新存在成员分数 |
| ZCARD            | ZCARD key                                      | 获取成员数                                 |
| ZCOUNT           | ZCOUNT key min max                             | 计算有序集合指定区间分数的成员数           |
| ZINCRBY          | ZINCRBY key increment member                   | 指定成员分数加上增量                       |
| ZINTERSTORE      | ZINTERSTORE destination numkeys key [key...]   | 返回有序集合的交集并存储在destination中    |
| ZLEXCOUNT        | ZLEXCOUNT key min max                          | 计算字典区间内成员数量                     |
| ZRANGE           | ZRANGE key start stop [WITHSCORES]             | 返回有序集合指定区间内的成员               |
| ZRANGEBYLEX      | ZRANGEBYLEX key min max [LIMIT offset count]   | 返回有序集合指定字典区间内的成员           |
| ZRANGEBYSCORE    | ZRANGEBYSCORE key min max [WITHSCORES] [LIMIT] | 返回有序集合指定分数的区间的成员           |
| ZRANK            | ZRANK key member                               | 返回指定成员的索引                         |
| ZREM             | ZREM key member [member...]                    | 移除多个成员                               |
| ZREMRANGEBYLEX   | ZREMRANGEBYLEX key min max                     | 移除有序集合指定字典区间的成员             |
| ZREMRANGEBYRANK  | ZREMRANGEBYRANK key start stop                 | 移除指定排名区间的所有成员                 |
| ZREMRANGEBYSCORE | ZREMRANSCORE key min max                       | 移除指定分数区间的所有成员                 |
| ZREVRANGE        | ZREVRANGE key start stop [WITHSCORES]          | 返回指定索引区间的成员，分数降序           |
| ZREVRANGEBYSCORE | ZREVRANGEBYSCORE key max min [WITHSCORES]      | 定分数区间的成员，分数降序                 |
| ZREVRANK         | ZREVRANK key member                            | 返回指定成员的排名，分数就爱嗯序           |
| ZSCORE           | ZSCORE key member                              | 返回成员分数值                             |
| ZUNIONSTORE      | ZUNIONSTORE destination numkeys key [key...]   | 返回有序集合的并集并存储在destination中    |
| ZSCAN            | ZSCAN key cursor [MATCH pattern] [COUNT count] | 迭代有序集合中元素                         |

