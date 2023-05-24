#  **Redis 常用命令**

## **1.redis 前置命令**

(在 redis.conf 文件可配置，该文件很重要，后续很多操作都是这个配置文件) redis 默认自动使用 0 号库
### 1.1  沟通命令，查看状态
redis >ping 返回 PONG
解释：输入 ping，redis 给我们返回 PONG，表示 redis 服务运行正常

```shell
127.0.0.1:6379[2]> ping
PONG
```

### 1.2  查看当前数据库中 key 的数目：dbsize（redis默认使用库0，使用`select 库号`切换数据库）
语法：dbsize 作用：返回当前数据库的 key 的数量。
返回值：数字，key的数量

例：先查索引5的key个数， 再查 0 库的key个数

```shell
127.0.0.1:6379> select 5
OK
127.0.0.1:6379[5]> keys *
(empty array)
127.0.0.1:6379[5]> select 0
OK
127.0.0.1:6379> keys *
1) "user_area"
2) "blog_views_count"
3) "article_views_count"
4) "website_config"
5) "page_cover"
```

### 1.3  redis 默认使用 16 个库
Redis 默认使用 16 个库，从 0 到 15。 对数据库个数的修改，在 redis.conf 文件中

### 1.4 切换库命令：select db
使用其他数据库，命令是 select index
例1： select 5

```shell
127.0.0.1:6379> select 5
OK
127.0.0.1:6379[5]> keys *
(empty array)
```

### 1.5  删除当前库的数据：flushdb

```shell
127.0.0.1:6379[5]> keys *
1) "11"
127.0.0.1:6379[5]> flushdb
OK
127.0.0.1:6379[5]> keys *
(empty array)
```

### 1.6  redis 自带的客户端退出当前 redis 连接: exit 或 quit

### 1.7 Redis 的 Key 的操作命令
#### A、 keys
语法：keys pattern 作用：查找所有符合模式 pattern 的 key. pattern 可以使用通配符。通配符：

```shell
*: 表示 0-多个字符 ，例如：keys * 查询所有的 key。 
？：表示单个字符，例如：wo?d , 匹配 word , wood
```
例 1：显示所有的 key

```shell
127.0.0.1:6379[2]> keys *
1) "ji"
2) "jjj"
3) "jji"
```

例 2：使用 * 表示 0 或多个字符

```shell
127.0.0.1:6379[2]> keys j*
1) "ji"
2) "jjj"
3) "jji"
```


例 3：使用 ？ 表示单个字符

```shell
127.0.0.1:6379[2]> keys j?
1) "ji"
```

#### B、 exists
语法：exists key [key…]
作用：判断 key 是否存在返回值：整数，存在 key 返回 1，其他返回 0. 使用多个 key，返回存在的 key 的数量。
例 1：检查指定 key 是否存在

```shell
127.0.0.1:6379[2]> exists ji
(integer) 1
127.0.0.1:6379[2]> exists ll
(integer) 0
```

#### C、 expire
语法：expire key seconds
作用：设置 key 的生存时间，超过时间，key 自动删除。单位是秒。
返回值：设置成功返回数字 1， 其他情况是 0 。
例 1： 设置 jj 的倒计时是 5 秒

```shell
127.0.0.1:6379[2]> keys *
1) "jj"
2) "ji"
3) "jjj"
4) "jji"
127.0.0.1:6379[2]> expire jj 5
(integer) 1		#设置成功
127.0.0.1:6379[2]> keys *
1) "ji"
2) "jjj"
3) "jji"
127.0.0.1:6379[2]> expire jj 5
(integer) 0		#已过期，设置失败
```

#### D、 ttl
语法：ttl key
作用：以秒为单位，返回 key 的剩余生存时间（ttl: time to live）返回值：

-1 ：没有设置 key 的生存时间， key 永不过期。
-2 ：key 不存在
数字：key 的剩余时间，秒为单位
例 1：设置 jji 的过期时间是 10， 查看剩余时间

```shell
127.0.0.1:6379[2]> expire jj 5
(integer) 0
127.0.0.1:6379[2]> EXPIRE ji 10
(integer) 1 	#设置成功
127.0.0.1:6379[2]> ttl ji
(integer) 6		#剩余6秒
127.0.0.1:6379[2]> ttl ji
(integer) 4
127.0.0.1:6379[2]> ttl ji
(integer) 3
127.0.0.1:6379[2]> ttl ji
(integer) 2
127.0.0.1:6379[2]> ttl ji
(integer) -2 	#已过期
127.0.0.1:6379[2]> ttl jji
(integer) -1 	#无过期时间（永不过期）
```

#### E、 type
语法：type key
作用：查看 key 所存储值的数据类型返回值：字符串表示的数据类型

none (key 不存在)
string (字符串)
list (列表) set (集合)
zset (有序集)
hash (哈希表)
例 1：查看存储字符串的 key ：jjj

```shell
127.0.0.1:6379[2]> keys *
1) "jjj"
2) "jji"
127.0.0.1:6379[2]> type jjj
string
```

例 2：查看不存在的 key

```shell
127.0.0.1:6379[2]> type not-exists
none
```

#### F、 del
语法：del key [key…]
作用：删除存在的 key ，不存在的 key 忽略。
返回值：数字，删除的 key 的数量。

例 1：删除指定的 key

```shell
127.0.0.1:6379[2]> del jjj
(integer) 1		# 删除成功
127.0.0.1:6379[2]> del jjj
(integer) 0 	# 不存在，删除失败
```



## **2.redis的5种数据类型**

### 1.1 字符串类型 string
- 字符串类型是 Redis 中最基本的数据类型，它能存储任何形式的字符串，包括二进制数据，序列化后的数据，JSON 化的对象甚至是一张图片。最大 512M。 

  ![image-20221017194658923](C:\Users\WCY\AppData\Roaming\Typora\typora-user-images\image-20221017194658923.png)

### 1.2  哈希类型 hash –java对象
- Redis hash 是一个 string 类型的 field 和 value 的映射表，hash 特别适合用于存储对象。 

  ![image-20221017194814232](C:\Users\WCY\AppData\Roaming\Typora\typora-user-images\image-20221017194814232.png)

### 1.3  列表类型 list –java list array
- Redis 列表是简单的字符串列表，按照插入顺序排序。你可以添加一个元素到列表的头部（左边）或者尾部（右边）

  ![image-20221017195334030](C:\Users\WCY\AppData\Roaming\Typora\typora-user-images\image-20221017195334030.png)

### 1.4  集合类型 set – hashset
- Redis 的 Set 是 string 类型的无序集合，集合成员是唯一的，即集合中不能出现重复的数据.

  ![image-20221017195446354](C:\Users\WCY\AppData\Roaming\Typora\typora-user-images\image-20221017195446354.png)

### 1.5  有序集合类型 zset （sorted set） - treeset
- Redis 有序集合 zset 和集合 set 一样也是 string 类型元素的集合，且不允许重复的成员。不同的是 zset 的每个元素都会关联一个分数（分数可以重复），redis 通过分数来为集合中的成员进行从小到大的排序。

  ![image-20221017195510075](C:\Users\WCY\AppData\Roaming\Typora\typora-user-images\image-20221017195510075.png)


## **3. Redis 数据类型操作命令**
### 1.1 字符串类型（string）
字符串类型是 Redis 中最基本的数据类型，它能存储任何形式的字符串，包括二进制数据，序列化后的数据，JSON 化的对象甚至是一张图片

基本命令
#### A、 set
将字符串值 value 设置到 key 中
语法：set key value

```shell
127.0.0.1:6379[2]> set username wcy
OK
127.0.0.1:6379[2]> keys *
1) "username"
2) "jji"
127.0.0.1:6379[2]> get username
"wcy"
```

向已经存在的 key 设置新的 value，会覆盖原来的值

```shell
127.0.0.1:6379[2]> set username snail
OK
127.0.0.1:6379[2]> get username
"snail"
```

#### B、 get
获取 key 中设置的字符串值
语法： get key
例如：获取 username 这个 key 对应的 value

```shell
127.0.0.1:6379[2]> keys *
1) "username"
2) "jji"
127.0.0.1:6379[2]> get username
"snail"
```


#### C、 incr
将 key 中储存的数字值加 1，如果 key 不存在，则 key 的值先被初始化为 0 再执行 incr 操作（只能对数字类型的数据操作）
语法：incr key
例 1：操作key,值增加 1

```shell
127.0.0.1:6379[2]> incr index
(integer) 1
127.0.0.1:6379[2]> incr index
(integer) 2
```

例 2：对非数字的值操作是不行的

```shell
127.0.0.1:6379[2]> incr username
(error) ERR value is not an integer or out of range
```

#### D、 decr
将 key 中储存的数字值减1，如果 key 不存在，则么 key 的值先被初始化为 0 再执行 decr 操作（只能对数字类型的数据操作）

语法：decr key
例1：不存在的key，初值为0，再减 1 。

```shell
127.0.0.1:6379[2]> decr sal
(integer) -1
```

例2：对存在的数字值的 key ，减 1 。

```shell
127.0.0.1:6379[2]> decr sal
(integer) -2
```

incr ，decr 在实现关注人数上，文章的点击数上。

#### E、 append
语法：append key value
说明：如果 key 存在， 则将 value 追加到 key 原来旧值的末尾如果 key 不存在， 则将 key 设置值为 value
返回值：追加字符串之后的总长度

例 1：追加内容到存在的 key

```shell
127.0.0.1:6379[2]> keys *
1) "username"
2) "sal"
3) "jji"
4) "index"
127.0.0.1:6379[2]> append username yyds
(integer) 9
127.0.0.1:6379[2]> get username
"snailyyds"
```

例 2：追加到不存在的 key，同 set key value

```shell
127.0.0.1:6379[2]> append superman spider-man
(integer) 10
127.0.0.1:6379[2]> keys *
1) "superman"
2) "username"
3) "sal"
4) "jji"
5) "index"
127.0.0.1:6379[2]> get superman
"spider-man"
```

### 1.2 常用命令
#### A、 strlen
语法：strlen key
说明：返回 key 所储存的字符串值的长度返回值：
①：如果key存在，返回字符串值的长度
②：key不存在，返回0

例 1：计算存在 key 的字符串长度

```shell
127.0.0.1:6379[2]> strlen username
(integer) 9
```


设置中文 set k4 中文长度 ， 按字符个数计算

例 2：计算不存在的 key

```shell
127.0.0.1:6379[2]> get k4
(nil)
127.0.0.1:6379[2]> strlen k4
(integer) 0		# 不存在，长度为0
```

#### B、 getrange
语法：getrange key start end
作用：获取 key 中字符串值从 start 开始 到 end 结束 的子字符串,包括 start 和 end, 负数表示从字符串的末尾开始， -1 表示最后一个字符
返回值：截取的子字符串。
使用的字符串 key: school, value: Peking

例 1: 截取从 2 到 5 的字符

```shell
127.0.0.1:6379[2]> set school Peking
OK
127.0.0.1:6379[2]> getrange school 2 5
"king"		# 索引从0开始
```

例 2：从字符串尾部截取，start ,end 是负数，最后一位是 -1

```shell
127.0.0.1:6379[2]> getrange school -4 -1
"king"		# 从后往前，最后一位索引为-1
```

例 3：超出字符串范围的截取 ，获取合理的子串

```shell
127.0.0.1:6379[2]> getrange school 2 100
"king"
```

#### C、 setrange
语法：setrange key offset value
说明：用 value 覆盖（替换）key 的存储的值从 offset 开始,不存在的 key 做空白字符串。
返回值：修改后的字符串的长度

例 1：替换给定的字符串

```shell
127.0.0.1:6379[2]> setrange school 2 yyds
(integer) 6
127.0.0.1:6379[2]> get school
"Peyyds"
```

例 2：设置不存在的 key

```shell
127.0.0.1:6379[2]> setrange nobody 0 100
(integer) 3
127.0.0.1:6379[2]> get nobody
"100"
```

#### D、 mset
语法：mset key value [key value…]
说明：同时设置一个或多个 key-value 对返回值： OK

例 1：一次设置多个 key， value

```shell
127.0.0.1:6379[2]> mset v1 1 v2 2 v3 3
OK
127.0.0.1:6379[2]> keys *
 1) "v3" <-
 2) "superman"
 3) "school"
 4) "index"
 5) "v1" <-
 6) "v2" <-
 7) "nobody"
 8) "username"
 9) "sal"
10) "jji"
```

#### E、 mget
语法：mget key [key …]
作用：获取所有(一个或多个)给定 key 的值返回值：包含所有 key 的列表

例 1：返回多个 key 的存储值

```shell
127.0.0.1:6379[2]> mget v1 v2 v3
1) "1"
2) "2"
3) "3"
```

例 2：返回不存在的 key

```shell
127.0.0.1:6379[2]> mget v1 k2 k3
1) "1"
2) (nil)
3) (nil)
```

### 2.1 哈希类型 hash
redis hash 是一个 string 类型的 field 和 value 的映射表，hash 特别适合用于存储对象。

基本命令
#### A、 hset
语法：hset hash 表的 key field value
作用：将哈希表 key 中的域 field 的值设为 value ，如果 key 不存在，则新建 hash 表，执行赋值，如果有 field ,则覆盖值。
返回值：
①如果 field 是 hash 表中新 field，且设置值成功，返回 1
②如果 field 已经存在，旧值覆盖新值，返回 0

例 1：新的 field

```shell
127.0.0.1:6379[2]> hset website baidu http://www.baidu.com
(integer) 1
```

例 2：覆盖旧的的 field

```shell
127.0.0.1:6379[2]> hset website baidu https://www.baidu.com
(integer) 0
```

#### B、 hget
语法：hget key field
作用：获取哈希表 key 中给定域 field 的值
返回值：field 域的值，如果 key 不存在或者 field 不存在返回 nil

例 1：获取存在 key 值的某个域的值

```shell
127.0.0.1:6379[2]> hget website baidu
"https://www.baidu.com"
```

例 2：获取不存在的 field

```shell
127.0.0.1:6379[2]> hget website google
(nil)
```

#### C、 hmset
语法：hmset key field value [field value…]
说明：同时将多个 field-value (域-值)设置到哈希表 key 中，此命令会覆盖已经存在的 field， hash 表 key 不存在，创建空的 hash 表，执行 hmset.
返回值：设置成功返回 ok， 如果失败返回一个错误

例 1：同时设置多个 field-value

```shell
127.0.0.1:6379[2]> hmset website baidu https://www.baidu.com google https://www.google.com
OK
127.0.0.1:6379[2]> hmget website baidu google
1) "https://www.baidu.com"
2) "https://www.google.com"
```

例 2：key 类型不是 hash,产生错误

```shell
127.0.0.1:6379[2]> hmset jji ji sheng
(error) WRONGTYPE Operation against a key holding the wrong kind of value
```

#### D、 hmget
语法：hmget key field [field…]
作用:获取哈希表 key 中一个或多个给定域的值
返回值：返回和 field 顺序对应的值，如果 field 不存在，
返回 nil
例 1：获取多个 field 的值

```shell
127.0.0.1:6379[2]> hmget website baidu google bing
1) "https://www.baidu.com"
2) "https://www.google.com"
3) (nil)
```

#### E、 hgetall
语法：hgetall key
作用：获取哈希表 key 中所有的域和值
返回值：以列表形式返回 hash 中域和域的值 ，key 不存在，返回空 hash

例 1：返回 key 对应的所有域和值

```shell
127.0.0.1:6379[2]> hgetall website
1) "baidu"
2) "https://www.baidu.com"
3) "google"
4) "https://www.google.com"
```

例 2：不存在的 key，返回空列表

```shell
127.0.0.1:6379[2]> hgetall www
(empty array)
```

#### F、 hdel
语法：hdel key field [field…]
作用：删除哈希表 key 中的一个或多个指定域 field，不存在 field 直接忽略
返回值：成功删除的 field 的数量

例 1：删除指定的 field

```shell
127.0.0.1:6379[2]> hdel website baidu google
(integer) 2
127.0.0.1:6379[2]> hgetall website
(empty array)
```

### 2.2 常用命令
#### A、 hkeys
语法：hkeys key
作用：查看哈希表 key 中的所有 field 域
返回值：包含所有 field 的列表，key 不存在返回空列表

例 1：查看 website 所有的域名称

```shell
127.0.0.1:6379[2]> hmset website baidu https://www.baidu.com google https://www.google.com
OK
127.0.0.1:6379[2]> hkeys website
1) "baidu"
2) "google"
```

#### B、 hvals
语法：hvals key
作用：返回哈希表 中所有域的值
返回值：包含哈希表所有域值的列表，key 不存在返回空列表

例 1：显示 website 哈希表所有域的值

```shell
127.0.0.1:6379[2]> hvals website
1) "https://www.baidu.com"
2) "https://www.google.com"
```

#### C、 hexists
语法：hexists key field
作用：查看哈希表 key 中，给定域 field 是否存在
返回值：如果 field 存在，返回 1， 其他返回 0

例 1：查看存在 key 中 field 域是否存在

```shell
127.0.0.1:6379[2]> hexists website baidu
(integer) 1
127.0.0.1:6379[2]> hexists website bing
(integer) 0
```

### 3.1  列表 list
Redis 列表是简单的字符串列表，按照插入顺序排序。你可以添加一个元素导列表的头部（左边）或者尾部（右边）

### 3.2 基本命令
#### A、 lpush
语法：lpush key value [value…]
作用：将一个或多个值 value 插入到列表 key 的表头（最左边），从左边开始加入值，从左到右的顺序依次插入到表头
返回值：数字，新列表的长度

```shell
127.0.0.1:6379[2]> lpush myList a b c
(integer) 3
127.0.0.1:6379[2]> lrange myList 0 -1
1) "c"
2) "b"
3) "a"
```

例 2：插入重复值到 list 列表类型

```shell
127.0.0.1:6379[2]> lpush myList a
(integer) 4
127.0.0.1:6379[2]> lrange myList 0 -1
1) "a"
2) "c"
3) "b"
4) "a"
```

#### B、 rpush
语法：rpush key value [value…]
作用：将一个或多个值 value 插入到列表 key 的表尾（最右边），各个 value 值按从左到右的顺序依次插入到表尾
返回值：数字，新列表的长度

例 1：插入多个值到列表

```shell
127.0.0.1:6379[2]> rpush myList2 a b c
(integer) 3
127.0.0.1:6379[2]> lrange myList2 0 -1
1) "a"
2) "b"
3) "c"
```

#### C、 lrange
语法：lrange key start stop
作用：获取列表 key 中指定区间内的元素，0 表示列表的第一个元素，以 1 表示列表的第二个元素；start , stop 是列表的下标值，也可以负数的下标， -1 表示列表的最后一个元素， -2 表示列表的倒数第二个元素，以此类推。 start ，stop 超出列表的范围不会出现错误。
返回值：指定区间的列表

例 1：返回列表的全部内容

```shell
127.0.0.1:6379[2]> rpush myList2 a b c
(integer) 3
127.0.0.1:6379[2]> lrange myList2 0 -1
1) "a"
2) "b"
3) "c"
```

例 2：显示列表中第 2 个元素，下标从 0 开始

```shell
127.0.0.1:6379[2]> lrange myList2 1 1
1) "b"
```

#### D、 lindex
语法：lindex key index
作用：获取列表 key 中下标为指定 index 的元素，列表元素不删除，只是查询。0 表示列表的第一个元素，以 1 表示列表的第二个元素；start , stop 是列表的下标值，也可以负数的下标， -1 表示列表的最后一个元素， -2 表示列表的倒数第二个元素，以此类推。
返回值：指定下标的元素；index 不在列表范围，返回 nil

例 1：返回下标是 1 的元素

```shell
127.0.0.1:6379[2]> lindex myList2 1
"b"
```

2：不存在的下标

```shell
127.0.0.1:6379[2]> lindex myList2 3
(nil)
```

#### E、 llen
语法：llen key
作用：获取列表 key 的长度
返回值：数值，列表的长度； key 不存在返回 0

例 1：显示存在 key 的列表元素的个数

```shell
127.0.0.1:6379[2]> llen myList2
(integer) 3
```

### 3.3 常用命令
#### A、lrem
语法：lrem key count value
作用：根据参数 count 的值，移除列表中 abs(count) 个与参数 value 相等的元素， count >0 ，从列表的左侧向右开始移除； count < 0 从列表的尾部开始移除；count = 0 移除表中所有与 value 相等的值。
返回值：数值，移除的元素个数

例 1：删除 2 个相同的列表元素

```shell
127.0.0.1:6379[2]> lpush course java html java html java html
(integer) 6
127.0.0.1:6379[2]> lrem course 2 html	# 从左边起删除先遇到的2个html
(integer) 2
127.0.0.1:6379[2]> lrange course 0 -1
1) "java"
2) "java"
3) "html"
4) "java"
127.0.0.1:6379[2]> lpush course java html java html java html
127.0.0.1:6379[2]> lrange course 0 -1
 1) "html"
 2) "java"
 3) "html"
 4) "java"
 5) "html"
 6) "java"
 7) "java"
 8) "java"
 9) "html"
10) "java"
127.0.0.1:6379[2]> lrem course -2 html	# 从右边起删除先遇到的2个html
(integer) 2
127.0.0.1:6379[2]> lrange course 0 -1
1) "html"
2) "java"
3) "html"
4) "java"
5) "java"
6) "java"
7) "java"
8) "java"
127.0.0.1:6379[2]> lrem course 0 java	# 删除所有 java
(integer) 6
127.0.0.1:6379[2]> lrange course 0 -1
1) "html"
2) "html"
```

#### B、 lset
语法：lset key index value 作用：将列表 key 下标为 index 的元素的值设置为 value。
返回值：设置成功返回 ok ; key 不存在或者 index 超出范围返回错误信息
例 1：设置下标 2 的 value 为“c++”。

```shell
127.0.0.1:6379[2]> lset course 1 c++
OK
127.0.0.1:6379[2]> lrange course 0 -1
1) "html"
2) "c++"
```

#### C、 linsert
语法：linsert key BEFORE|ALFTER pivot value
作用：将值 value 插入到列表 key 当中位于值 pivot 之前或之后的位置。key 不存在，pivot 不在列表中，不执行任何操作。
返回值：命令执行成功，返回新列表的长度。没有找到 pivot 返回 -1， key 不存在返回 0。

例 1：修改列表 course，在值 c++ 之后加入 java

```shell
127.0.0.1:6379[2]> linsert course after c++ java
(integer) 3
127.0.0.1:6379[2]> lrange course 0 -1
1) "html"
2) "c++"
3) "java"
```

### 4.1  集合类型 set
redis 的 Set 是 string 类型的无序集合，集合成员是唯一的，即集合中不能出现重复的数据

### 4.2 基本命令
#### A、 sadd
语法：sadd key member [member…]
作用：将一个或多个 member 元素加入到集合 key 当中，已经存在于集合的 member 元素将被忽略，不会再加入。
返回值：加入到集合的新元素的个数。不包括被忽略的元素。

例 1：添加单个元素

```shell
127.0.0.1:6379[2]> sadd myset java
(integer) 1		# 操作成功的元素个数
127.0.0.1:6379[2]> sadd myset java
(integer) 0		# java已存在，返回0
```

例 2：添加多个元素

```shell
127.0.0.1:6379[2]> sadd myset c++ html js
(integer) 3
```

#### B、 smembers
语法：smembers key
作用：获取集合 key 中的所有成员元素，不存在的 key 视为空集合

例 1：查看集合的所有元素

```shell
127.0.0.1:6379[2]> smembers myset
1) "js"
2) "html"
3) "java"
4) "c++"
```

例 2：查看不存在的集合

```shell
127.0.0.1:6379[2]> smembers myset1
(empty array)
```

#### C、 sismember
语法：sismember key member
作用：判断 member 元素是否是集合 key 的成员
返回值：member 是集合成员返回 1，其他返回 0 。

例 1：检查元素是否存在集合中

```shell
127.0.0.1:6379[2]> sismember myset java
(integer) 1		# Java 存在 myset 中
127.0.0.1:6379[2]> sismember myset css
(integer) 0		# css 不存在 myset 中
```

#### D、 scard
语法：scard key
作用：获取集合里面的元素个数
返回值：数字，key 的元素个数。 其他情况返回 0 。

例 1：统计集合的大小

```shell
127.0.0.1:6379[2]> smembers myset
1) "js"
2) "html"
3) "java"
4) "c++"
127.0.0.1:6379[2]> scard myset
(integer) 4
```

#### E、 srem
语法：srem key member [member…]
作用：删除集合 key 中的一个或多个 member 元素，不存在的元素被忽略。
返回值：数字，成功删除的元素个数，不包括被忽略的元素。

```shell
127.0.0.1:6379[2]> srem myset java
(integer) 1		# 成功删除一个元素
127.0.0.1:6379[2]> srem myset java
(integer) 0		# 无元素删除成功
```

### 4.3 常用命令

#### A、 srandmember
语法：srandmember key [count]
作用：只提供 key，随机返回集合中一个元素，元素不删除，依然在集合中；提供了 count 时，count 正数, 返回包含 count 个数元素的集合， 集合元素各不相同。count 是负数，返回一个 count 绝对值的长度的集合， 集合中元素可能会重复多次。
返回值：一个元素；多个元素的集合

例 1：随机显示集合的一个元素

```shell
127.0.0.1:6379[2]> smembers myset
1) "js"
2) "html"
3) "c++"
127.0.0.1:6379[2]> srandmember myset	# 随机返回集合中一个元素
"html"
127.0.0.1:6379[2]> srandmember myset
"html"
127.0.0.1:6379[2]> srandmember myset
"c++"
```

例 2：使用 count 参数， count 是正数

```shell
127.0.0.1:6379[2]> srandmember myset 2	# 随机返回 myset 中 2 个不同元素的集合
1) "js"
2) "c++"
127.0.0.1:6379[2]> srandmember myset 2
1) "html"
2) "c++"
```

例 3：使用 count 参数，count 是负数

```shell
127.0.0.1:6379[2]> srandmember myset -3	# 随机返回 myset 中 3 个可能相同元素的集合
1) "js"
2) "js"
3) "html"
```

#### B、 spop
语法：spop key [count]
作用：随机从集合中删除一个元素, count 是删除的元素个数。
返回值：被删除的元素，key 不存在或空集合返回 nil

例 1：随机从集合删除一个元素

```shell
127.0.0.1:6379[2]> spop myset 1
1) "js"
127.0.0.1:6379[2]> smembers myset
1) "html"
2) "c++"
```

例 2：随机删除指定个数的元素

```shell
127.0.0.1:6379[2]> smembers myset
1) "js"
2) "html"
3) "css"
4) "c++"
127.0.0.1:6379[2]> spop myset 2
1) "js"
2) "html"
127.0.0.1:6379[2]> smembers myset
1) "css"
2) "c++"
```

### 5.1  有序集合类型 zset （sorted set）
redis 有序集合zset和集合set一样也是string类型元素的集合，且不允许重复的成员。
不同的是 zset 的每个元素都会关联一个分数（分数可以重复），redis 通过分数来为集合中的成员进行从小到大的排序。

### 5.2 基本命令
#### A、 zadd
语法：zadd key score member [score member…]
作用：将一个或多个 member 元素及其 score 值加入到有序集合 key 中，如果 member 存在集合中，则更新值；score 可以是整数或浮点数
返回值：数字，新添加的元素个数

例 1：创建保存学生成绩的集合

```shell
127.0.0.1:6379[2]> zadd studentscore 80 zhangsan 92 lisi 75 wangwu
(integer) 3
```

例 2：使用浮点数作为 score

```shell
127.0.0.1:6379[2]> zadd mycourse 82.23 css 82.24 html 99.99 java
(integer) 3
```

#### B、 zrange
语法：zrange key start stop [WITHSCORES]
作用：查询有序集合，指定区间的内的元素。集合成员按 score 值从小到大来排序。 start， stop 都是从 0 开始。0 是第一个元素，1 是第二个元素，依次类推。以 -1 表示最后一个成员，-2 表示倒数第二个成员。WITHSCORES 选项让 score 和 value 一同返回。
返回值：自定区间的成员集合

例 1：显示集合的全部元素，不显示 score，不使用 WITHSCORES

```shell
127.0.0.1:6379[2]> zrange studentscore 0 -1
1) "wangwu"
2) "zhangsan"
3) "lisi"
```

2：显示集合全部元素，并使用 WITHSCORES

```shell
127.0.0.1:6379[2]> zrange studentscore 0 -1 withscores
1) "wangwu"
2) "75"
3) "zhangsan"
4) "80"
5) "lisi"
6) "92"
```

3：显示第 0,1 二个成员

```shell
127.0.0.1:6379[2]> zrange studentscore 0 1 withscores
1) "wangwu"
2) "75"
3) "zhangsan"
4) "80"
```

例 4：排序显示浮点数的 score

```shell
127.0.0.1:6379[2]> zrange mycourse 0 -1 withscores
1) "css"
2) "82.230000000000004"
3) "html"
4) "82.239999999999995"
5) "java"
6) "99.989999999999995"
```

#### C、 zrevrange
语法：zrevrange key start stop [WITHSCORES]
作用：返回有序集 key 中，指定区间内的成员。其中成员的位置按 score 值递减(从大到小) 来排列。其它同 zrange 命令。
返回值：自定区间的成员集合

例 1：成绩榜

```shell
127.0.0.1:6379[2]> zrevrange studentscore 0 -1 withscores
1) "lisi"
2) "92"
3) "zhangsan"
4) "80"
5) "wangwu"
6) "75"
```

#### D、 zrem
语法：zrem key member [member…]
作用：删除有序集合 key 中的一个或多个成员，不存在的成员被忽略
返回值：被成功删除的成员数量，不包括被忽略的成员。

例 1：删除指定一个成员 wangwu

```shell
127.0.0.1:6379[2]> zrem studentscore wangwu
(integer) 1
127.0.0.1:6379[2]> zrevrange studentscore 0 -1
1) "lisi"
2) "zhangsan"
```

#### E、 zcard
语法：zcard key
作用：获取有序集 key 的元素成员的个数返回值：key 存在返回集合元素的个数， key 不存在，返回 0

例 1：查询集合的元素个数

```shell
127.0.0.1:6379[2]> zcard studentscore
(integer) 2
```

### 5.3 常用命令
#### A、 zrangebyscore
语法：zrangebyscore key min max [WITHSCORES ] [LIMIT offset count]
作用：获取有序集 key 中，所有 score 值介于 min 和 max 之间（包括 min 和 max）的成员，有序成员是按递增（从小到大）排序。
min ,max 是包括在内 ， 使用符号 ( 表示不包括。 min ， max 可以使用 -inf ，+inf 表示最小和最大 limit 用来限制返回结果的数量和区间。
withscores 显示 score 和 value 返回值：指定区间的集合数据使用的准备数据

例 1：显示指定具体区间的数据

```shell
127.0.0.1:6379[2]> zadd salary 2000 john 3000 tom 4500 rose 6500 jack
(integer) 4
127.0.0.1:6379[2]> zrangebyscore salary 2000 4500 withscores
1) "john"
2) "2000"
3) "tom"
4) "3000"
5) "rose"
6) "4500"
```

例 2：显示指定具体区间的集合数据，开区间（不包括 min，max）

```shell
127.0.0.1:6379[2]> zrangebyscore salary (2000 (4500 withscores
1) "tom"
2) "3000"
```

例 3：显示整个集合的所有数据

```shell
127.0.0.1:6379[2]> zrangebyscore salary -inf +inf withscores
1) "john"
2) "2000"
3) "tom"
4) "3000"
5) "rose"
6) "4500"
7) "jack"
8) "6500"
```

例 4：使用 limit 截取的数据：

```shell
127.0.0.1:6379[2]> zrangebyscore salary 2000 5000 withscores
1) "john"
2) "2000"
3) "tom"
4) "3000"
5) "rose"
6) "4500"
127.0.0.1:6379[2]> zrangebyscore salary 2000 5000 withscores limit 0 2 # 从索引 0 开始取 2 个元素
1) "john"
2) "2000"
3) "tom"
4) "3000"
```

显示从第一个位置开始，取一个元素。

```shell
127.0.0.1:6379[2]> zrangebyscore salary 2000 5000 withscores limit 1 1
1) "tom"
2) "3000"
```

#### B、 zrevrangebyscore
语法：zrevrangebyscore key max min [WITHSCORES ] [LIMIT offset count]
作用：返回有序集 key 中， score 值介于 max 和 min 之间(默认包括等于 max 或 min )的所有的成员。有序集成员按 score 值递减(从大到小)的次序排列。其他同 zrangebyscore

例 1：查询工资最高到 3100 之间的员工

```shell
127.0.0.1:6379[2]> zrevrangebyscore salary +inf 3100 withscores
1) "jack"
2) "6500"
3) "rose"
4) "4500"
```

#### C、 zcount
语法：zcount key min max 作用：返回有序集 key 中， score 值在 min 和 max 之间(默认包括 score 值等于 min 或 max ) 的成员的数量

例 1：求工资在 3000-5000 的员工数量

```shell
127.0.0.1:6379[2]> zcount salary 3000 5000
(integer) 2
```