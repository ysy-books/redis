# Redis数据类型

## 1. Redis的五大数据类型

**String（字符串）**

string是redis最基本的类型，你可以理解成与Memcached一模一样的类型，一个key对应一个value。

string类型是二进制安全的。意思是redis的string可以包含任何数据。比如jpg图片或者序列化的对象 。

string类型是Redis最基本的数据类型，一个redis中字符串value最多可以是512M

**Hash（哈希，类似java里的Map）**

Redis hash 是一个键值对集合。
Redis hash是一个string类型的field和value的映射表，hash特别适合用于存储对象。

类似Java里面的Map<String,Object>

**List（列表）**

Redis 列表是简单的字符串列表，按照插入顺序排序。你可以添加一个元素导列表的头部（左边）或者尾部（右边）。
它的底层实际是个链表

**Set（集合）**

Redis的Set是string类型的无序集合。它是通过HashTable实现实现的，

**Zset(sorted set：有序集合)**

Redis zset 和 set 一样也是string类型元素的集合,且不允许重复的成员。
不同的是每个元素都会关联一个double类型的分数。
redis正是通过分数来为集合中的成员进行从小到大的排序。zset的成员是唯一的,但分数(score)却可以重复。

**哪里去获得redis常见数据类型操作命令**

Http://redisdoc.com/


## 2. Redis 键(key)

### 2.1 常用

`del key` 删除key

`dump key` 序列化 key，并返回被序列化的值

`exists key` 是否存在 key

`expire key seconds` 设置过期时间

`expireat key timestamp` 设置过期时间 参数时间戳

`pexpire key milliseconds`

`pexpireat key milliseconds-timestamp`

`keys pattern` 查找符合模式的 key

`move key db` 将当前数据库的 key 移动到给定的数据库 db 中

`persist key` 移动 key 的过期时间，key 将持久保持

`ttl key` 剩余过期时间

`randomkey` 从当前数据库中随机返回一个 key

`rename key newkey` 修改 key 的名称

`renamenx key newkey` 仅当newkey 不存在时，将 key 改名为 newkey

`type key` 返回 key 所存储的值的类型


## 3. Redis字符串(String)

### 3.1 常用

`set key value` 设置指定 key 的值

`get key` 获取指定 key 的值

`getrange key start end` 返回 key 中字符串值的子字符

`getset key value` 将给定 key 的值设置为 value，并返回 key 的旧值

`getbit key offset` 对 key 所存储的字符串值，获取指定偏移量上的位（bit）

`mget key1 [key2...]` 获取一个或多个值

`setbit key offset value` 对 key 所存储的字符串值，设置或清除指定偏移量上的位（bit）

`setex key seconds value` 设置值并设置过期时间

`setnx key value` 当 key 不存在时设置 key 的值

`setrange key offset value` 用 value 参数覆写给定 key 所存储的字符串，
从偏移量 offset 开始

`strlen key key` 字符串值的长度

`mset key value [key value...]` 同时设置多个 key 值

`msetnx key value [key value...]` 当所有 key 都不存在时，同时设置多个 key 值

`psetex key milliseonds value` 设置过期时间 毫秒

`Incr/decr/incrby/decrby` 一定要是数字才能进行加减


## 4. Redis列表(List)

### 4.1 常用

`blpop key1 [key2] timeout` 移出并获取列表中的第一个元素，如果列表没有元素会阻塞列表
直到等待超时或发现可弹出元素为止

`brpop key1 [key2] timeout` 移出并获取列表中的最后一个元素，如果列表没有元素会阻塞列表
直到等待超时或发现可弹出元素为止

`brpoplpush source destination timout` 从列表中弹出一个值，将弹出的元素插入到
另一个列表中并返回它；如果列表没有元素会阻塞列表直到等待超时或发现可弹出元素为止

`lindex key index` 通过索引获取列表中的元素

`linsert key before|after pivot value` 在列表的元素前或后插入元素

`llen key` 获取列表长度

`lpop key` 移出并获取列表的第一个元素

`lpush key value1 [value2]` 将一个或多个值插入到列表头部

`lpushx key value` 将一个或多个值插入到已存在的列表头部

`lrange key start stop` 获取指定范围内的元素


### 4.2 案例

* lpush/rpush/lrange

* lpop/rpop

* lindex，按照索引下标获得元素(从上到下)

  通过索引获取列表中的元素 lindex key index

* llen

* lrem key 删N个value

  从left往right删除2个值等于v1的元素，返回的值为实际删除的数量

  LREM list3 0 值，表示删除全部给定的值。零个就是全部值

* ltrim key 开始index 结束index，截取指定范围的值后再赋值给key

  ltrim：截取指定索引区间的元素，格式是ltrim list的key 起始索引 结束索引

* rpoplpush 源列表 目的列表

  移除列表的最后一个元素，并将该元素添加到另一个列表并返回

* lset key index value

* linsert key  before/after 值1 值2

  在list某个已有值的前后再添加具体值


### 4.3 性能总结

* 它是一个字符串链表，left、right都可以插入添加；
* 如果键不存在，创建新的链表；
* 如果键已存在，新增内容；
* 如果值全移除，对应的键也就消失了。
* 链表的操作无论是头和尾效率都极高，但假如是对中间元素进行操作，效率就很惨淡了。


## 5. Redis集合(Set)

### 5.1 常用

`sadd key member1 [member2]` 向集合添加一个或多个成员

`scard key` 获取集合的成员数

`sdiff key1 [key2]` 返回给定所有集合的差集

`sdiffstore destination key1 [key2]` 返回给定集合的差集并存储在 destination 中

`sinter key1 [key2]` 返回给定所有集合的交集

`sinterstore destination key1 [key2]` 返回给定集合的交集并存储在 destination 中

`sismember key member` 判断 member 元素是否是集合 key 的成员

`smembers key` 返回集合中的所有成员

`smove source destination member` 将 member 元素从 source 集合移动到 destination 集合

`spop key` 移除并返回集合中的一个随机元素

`srandmember key [count]` 返回集合中一个或多个随机数

`srem key member1 [member2]` 移除集合中一个或多个成员

`sunion key1 [key2]` 返回所有给定集合的并集

`sunionstore destination key1 [key2]` 所有给定集合的并集存储到 destination 集合中

`sscan key cursor [MATCH pattern][Count count]` 迭代集合中的元素


### 5.2 案例

* sadd/smembers/sismember

* scard，获取集合里面的元素个数

* srem key value 删除集合中元素

* srandmember key 某个整数(随机出几个数)

  * 从set集合里面随机取出2个
  * 如果超过最大数量就全部取出，
  * 如果写的值是负数，比如-3 ，表示需要取出3个，但是可能会有重复值。

* spop key 随机出栈

* smove key1 key2 在key1里某个值      作用是将key1里的某个值赋给key2

* 数学集合类
  * 差集：sdiff

    在第一个set里面而不在后面任何一个set里面的项

  * 交集：sinter

  * 并集：sunion

## 6. Redis哈希(Hash)

* hset/hget/hmset/hmget/hgetall/hdel

* hlen

* hexists key 在key里面的某个值的key

* hkeys/hvals

* hincrby/hincrbyfloat

* hsetnx

  不存在赋值，存在了无效。



