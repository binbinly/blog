# redis基础

## 安装

### 下载

- [下载](https://redis.io/download/)
- [安装文档](https://redis.io/docs/install/install-redis/install-redis-from-source/)
- [redis常用命令参考](http://doc.redisfans.com/)

### 源码编译安装

```bash
wget https://download.redis.io/redis-stable.tar.gz

tar -xzvf redis-stable.tar.gz
cd redis-stable
make

sudo make install

redis-server
```

### 配置详解

```bash
# daemonize no 默认情况下，redis不是在后台运行的，如果需要在后台运行，把该项的值更改为yes
daemonize yes
 
# 当redis在后台运行的时候，Redis默认会把pid文件放在/var/run/redis.pid，你可以配置到其他地址。
# 当运行多个redis服务时，需要指定不同的pid文件和端口
pidfile /var/run/redis.pid
 
# 指定redis运行的端口，默认是6379
port 6379
 
# 指定redis只接收来自于该IP地址的请求，如果不进行设置，那么将处理所有请求，
# 在生产环境中最好设置该项
# bind 127.0.0.1
 
# Specify the path for the unix socket that will be used to listen for
# incoming connections. There is no default, so Redis will not listen
# on a unix socket when not specified.
#
# unixsocket /tmp/redis.sock
# unixsocketperm 755
 
# 设置客户端连接时的超时时间，单位为秒。当客户端在这段时间内没有发出任何指令，那么关闭该连接
# 0是关闭此设置
timeout 0
 
# 指定日志记录级别
# Redis总共支持四个级别：debug、verbose、notice、warning，默认为verbose
# debug 记录很多信息，用于开发和测试
# varbose 有用的信息，不像debug会记录那么多
# notice 普通的verbose，常用于生产环境
# warning 只有非常重要或者严重的信息会记录到日志
loglevel debug
 
# 配置log文件地址
# 默认值为stdout，标准输出，若后台模式会输出到/dev/null
#logfile stdout
logfile /var/log/redis/redis.log
 
# To enable logging to the system logger, just set 'syslog-enabled' to yes,
# and optionally update the other syslog parameters to suit your needs.
# syslog-enabled no
 
# Specify the syslog identity.
# syslog-ident redis
 
# Specify the syslog facility.  Must be USER or between LOCAL0-LOCAL7.
# syslog-facility local0
 
# 可用数据库数
# 默认值为16，默认数据库为0，数据库范围在0-（database-1）之间
databases 16
 
################################ 快照  #################################
#
# 保存数据到磁盘，格式如下:
#
#   save <seconds> <changes>
#
#   指出在多长时间内，有多少次更新操作，就将数据同步到数据文件rdb。
#   相当于条件触发抓取快照，这个可以多个条件配合
#    
#   比如默认配置文件中的设置，就设置了三个条件
#
#   save 900 1  900秒内至少有1个key被改变
#   save 300 10  300秒内至少有300个key被改变
#   save 60 10000  60秒内至少有10000个key被改变
 
save 900 1
save 300 10
save 60 10000
 
# 存储至本地数据库时（持久化到rdb文件）是否压缩数据，默认为yes
rdbcompression yes
 
 
# 本地持久化数据库文件名，默认值为dump.rdb
dbfilename dump.rdb
 
# 工作目录
#
# 数据库镜像备份的文件放置的路径。
# 这里的路径跟文件名要分开配置是因为redis在进行备份时，先会将当前数据库的状态写入到一个临时文件中，等备份完成时，
# 再把该该临时文件替换为上面所指定的文件，而这里的临时文件和上面所配置的备份文件都会放在这个指定的路径当中。
# 
# AOF文件也会存放在这个目录下面
# 
# 注意这里必须制定一个目录而不是文件
dir ./
 
################################# 复制 #################################
# 主从复制. 设置该数据库为其他数据库的从数据库. 
# 设置当本机为slav服务时，设置master服务的IP地址及端口，在Redis启动时，它会自动从master进行数据同步
#
# slaveof <masterip> <masterport>
 
# 当master服务设置了密码保护时(用requirepass制定的密码)
# slav服务连接master的密码
# 
# masterauth <master-password>
 
 
# 当从库同主机失去连接或者复制正在进行，从机库有两种运行方式：
#
# 1) 如果slave-serve-stale-data设置为yes(默认设置)，从库会继续相应客户端的请求
# 
# 2) 如果slave-serve-stale-data是指为no，出去INFO和SLAVOF命令之外的任何请求都会返回一个
#    错误"SYNC with master in progress"
#
slave-serve-stale-data yes
 
# 从库会按照一个时间间隔向主库发送PINGs.可以通过repl-ping-slave-period设置这个时间间隔，默认是10秒
#
# repl-ping-slave-period 10
 
# repl-timeout 设置主库批量数据传输时间或者ping回复时间间隔，默认值是60秒
# 一定要确保repl-timeout大于repl-ping-slave-period
# repl-timeout 60

################################## 安全 ###################################
# 设置客户端连接后进行任何其他指定前需要使用的密码。
# 警告：因为redis速度相当快，所以在一台比较好的服务器下，一个外部的用户可以在一秒钟进行150K次的密码尝试，这意味着你需要指定非常非常强大的密码来防止暴力破解
#
# requirepass foobared
 
# 命令重命名.
#
# 在一个共享环境下可以重命名相对危险的命令。比如把CONFIG重名为一个不容易猜测的字符。
#
# 举例:
#
# rename-command CONFIG b840fc02d524045429941cc15f59e41cb7be6c52
#
# 如果想删除一个命令，直接把它重命名为一个空字符""即可，如下：
#
# rename-command CONFIG ""

################################### 约束 ####################################
# 设置同一时间最大客户端连接数，默认无限制，Redis可以同时打开的客户端连接数为Redis进程可以打开的最大文件描述符数，
# 如果设置 maxclients 0，表示不作限制。
# 当客户端连接数到达限制时，Redis会关闭新的连接并向客户端返回max number of clients reached错误信息
#
# maxclients 128
 
# 指定Redis最大内存限制，Redis在启动时会把数据加载到内存中，达到最大内存后，Redis会先尝试清除已到期或即将到期的Key
# Redis同时也会移除空的list对象
#
# 当此方法处理后，仍然到达最大内存设置，将无法再进行写入操作，但仍然可以进行读取操作
# 
# 注意：Redis新的vm机制，会把Key存放内存，Value会存放在swap区
#
# maxmemory的设置比较适合于把redis当作于类似memcached的缓存来使用，而不适合当做一个真实的DB。
# 当把Redis当做一个真实的数据库使用的时候，内存使用将是一个很大的开销
# maxmemory <bytes>
 
# 当内存达到最大值的时候Redis会选择删除哪些数据？有五种方式可供选择
# 
# volatile-lru -> 利用LRU算法移除设置过过期时间的key (LRU:最近使用 Least Recently Used )
# allkeys-lru -> 利用LRU算法移除任何key
# volatile-random -> 移除设置过过期时间的随机key
# allkeys->random -> remove a random key, any key
# volatile-ttl -> 移除即将过期的key(minor TTL)
# noeviction -> 不移除任何可以，只是返回一个写错误
# 
# 注意：对于上面的策略，如果没有合适的key可以移除，当写的时候Redis会返回一个错误
#
#       写命令包括: set setnx setex append
#       incr decr rpush lpush rpushx lpushx linsert lset rpoplpush sadd
#       sinter sinterstore sunion sunionstore sdiff sdiffstore zadd zincrby
#       zunionstore zinterstore hset hsetnx hmset hincrby incrby decrby
#       getset mset msetnx exec sort
#
# 默认是:
#
# maxmemory-policy volatile-lru
 
# LRU 和 minimal TTL 算法都不是精准的算法，但是相对精确的算法(为了节省内存)，随意你可以选择样本大小进行检测。
# Redis默认的灰选择3个样本进行检测，你可以通过maxmemory-samples进行设置
#
# maxmemory-samples 3

############################## AOF ###############################
# 默认情况下，redis会在后台异步的把数据库镜像备份到磁盘，但是该备份是非常耗时的，而且备份也不能很频繁，如果发生诸如拉闸限电、拔插头等状况，那么将造成比较大范围的数据丢失。
# 所以redis提供了另外一种更加高效的数据库备份及灾难恢复方式。
# 开启append only模式之后，redis会把所接收到的每一次写操作请求都追加到appendonly.aof文件中，当redis重新启动时，会从该文件恢复出之前的状态。
# 但是这样会造成appendonly.aof文件过大，所以redis还支持了BGREWRITEAOF指令，对appendonly.aof 进行重新整理。
# 你可以同时开启asynchronous dumps 和 AOF
 
appendonly no
 
# AOF文件名称 (默认: "appendonly.aof")
# appendfilename appendonly.aof
 
# Redis支持三种同步AOF文件的策略:
#
# no: 不进行同步，系统去操作 . Faster.
# always: always表示每次有写操作都进行同步. Slow, Safest.
# everysec: 表示对写操作进行累积，每秒同步一次. Compromise.
#
# 默认是"everysec"，按照速度和安全折中这是最好的。
# 如果想让Redis能更高效的运行，你也可以设置为"no"，让操作系统决定什么时候去执行
# 或者相反想让数据更安全你也可以设置为"always"
#
# 如果不确定就用 "everysec".
 
# appendfsync always
appendfsync everysec
# appendfsync no
 
# AOF策略设置为always或者everysec时，后台处理进程(后台保存或者AOF日志重写)会执行大量的I/O操作
# 在某些Linux配置中会阻止过长的fsync()请求。注意现在没有任何修复，即使fsync在另外一个线程进行处理
#
# 为了减缓这个问题，可以设置下面这个参数no-appendfsync-on-rewrite
#
# This means that while another child is saving the durability of Redis is
# the same as "appendfsync none", that in pratical terms means that it is
# possible to lost up to 30 seconds of log in the worst scenario (with the
# default Linux settings).
# 
# If you have latency problems turn this to "yes". Otherwise leave it as
# "no" that is the safest pick from the point of view of durability.
no-appendfsync-on-rewrite no
 
# Automatic rewrite of the append only file.
# AOF 自动重写
# 当AOF文件增长到一定大小的时候Redis能够调用 BGREWRITEAOF 对日志文件进行重写 
# 
# 它是这样工作的：Redis会记住上次进行些日志后文件的大小(如果从开机以来还没进行过重写，那日子大小在开机的时候确定)
#
# 基础大小会同现在的大小进行比较。如果现在的大小比基础大小大制定的百分比，重写功能将启动
# 同时需要指定一个最小大小用于AOF重写，这个用于阻止即使文件很小但是增长幅度很大也去重写AOF文件的情况
# 设置 percentage 为0就关闭这个特性
auto-aof-rewrite-percentage 100
auto-aof-rewrite-min-size 64mb

################################## SLOW LOG ###################################
# Redis Slow Log 记录超过特定执行时间的命令。执行时间不包括I/O计算比如连接客户端，返回结果等，只是命令执行时间
# 
# 可以通过两个参数设置slow log：一个是告诉Redis执行超过多少时间被记录的参数slowlog-log-slower-than(微妙)，
# 另一个是slow log 的长度。当一个新命令被记录的时候最早的命令将被从队列中移除
 
 
# 下面的时间以微妙微单位，因此1000000代表一分钟。
# 注意制定一个负数将关闭慢日志，而设置为0将强制每个命令都会记录
slowlog-log-slower-than 10000
 
 
# 对日志长度没有限制，只是要注意它会消耗内存
# 可以通过 SLOWLOG RESET 回收被慢日志消耗的内存
slowlog-max-len 1024
 
################################ VM ###############################
### WARNING! Virtual Memory is deprecated in Redis 2.4
### The use of Virtual Memory is strongly discouraged.
 
# Virtual Memory allows Redis to work with datasets bigger than the actual
# amount of RAM needed to hold the whole dataset in memory.
# In order to do so very used keys are taken in memory while the other keys
# are swapped into a swap file, similarly to what operating systems do
# with memory pages.
#
# To enable VM just set 'vm-enabled' to yes, and set the following three
# VM parameters accordingly to your needs.
 
vm-enabled no
# vm-enabled yes
 
# This is the path of the Redis swap file. As you can guess, swap files
# can't be shared by different Redis instances, so make sure to use a swap
# file for every redis process you are running. Redis will complain if the
# swap file is already in use.
#
# The best kind of storage for the Redis swap file (that's accessed at random) 
# is a Solid State Disk (SSD).
#
# *** WARNING *** if you are using a shared hosting the default of putting
# the swap file under /tmp is not secure. Create a dir with access granted
# only to Redis user and configure Redis to create the swap file there.
vm-swap-file /tmp/redis.swap
 
# vm-max-memory configures the VM to use at max the specified amount of
# RAM. Everything that deos not fit will be swapped on disk *if* possible, that
# is, if there is still enough contiguous space in the swap file.
#
# With vm-max-memory 0 the system will swap everything it can. Not a good
# default, just specify the max amount of RAM you can in bytes, but it's
# better to leave some margin. For instance specify an amount of RAM
# that's more or less between 60 and 80% of your free RAM.
vm-max-memory 0
 
# Redis swap files is split into pages. An object can be saved using multiple
# contiguous pages, but pages can't be shared between different objects.
# So if your page is too big, small objects swapped out on disk will waste
# a lot of space. If you page is too small, there is less space in the swap
# file (assuming you configured the same number of total swap file pages).
#
# If you use a lot of small objects, use a page size of 64 or 32 bytes.
# If you use a lot of big objects, use a bigger page size.
# If unsure, use the default :)
vm-page-size 32
 
# Number of total memory pages in the swap file.
# Given that the page table (a bitmap of free/used pages) is taken in memory,
# every 8 pages on disk will consume 1 byte of RAM.
#
# The total swap size is vm-page-size * vm-pages
#
# With the default of 32-bytes memory pages and 134217728 pages Redis will
# use a 4 GB swap file, that will use 16 MB of RAM for the page table.
#
# It's better to use the smallest acceptable value for your application,
# but the default is large in order to work in most conditions.
vm-pages 134217728
 
# Max number of VM I/O threads running at the same time.
# This threads are used to read/write data from/to swap file, since they
# also encode and decode objects from disk to memory or the reverse, a bigger
# number of threads can help with big objects even if they can't help with
# I/O itself as the physical device may not be able to couple with many
# reads/writes operations at the same time.
#
# The special value of 0 turn off threaded I/O and enables the blocking
# Virtual Memory implementation.
vm-max-threads 4
 
############################### ADVANCED CONFIG ###############################
 
# 当hash中包含超过指定元素个数并且最大的元素没有超过临界时，
# hash将以一种特殊的编码方式（大大减少内存使用）来存储，这里可以设置这两个临界值
# Redis Hash对应Value内部实际就是一个HashMap，实际这里会有2种不同实现，
# 这个Hash的成员比较少时Redis为了节省内存会采用类似一维数组的方式来紧凑存储，而不会采用真正的HashMap结构，对应的value redisObject的encoding为zipmap,
# 当成员数量增大时会自动转成真正的HashMap,此时encoding为ht。
hash-max-zipmap-entries 512
hash-max-zipmap-value 64
 
# list数据类型多少节点以下会采用去指针的紧凑存储格式。
# list数据类型节点值大小小于多少字节会采用紧凑存储格式。
list-max-ziplist-entries 512
list-max-ziplist-value 64
 
# set数据类型内部数据如果全部是数值型，且包含多少节点以下会采用紧凑格式存储。
set-max-intset-entries 512
 
# zsort数据类型多少节点以下会采用去指针的紧凑存储格式。
# zsort数据类型节点值大小小于多少字节会采用紧凑存储格式。
zset-max-ziplist-entries 128
zset-max-ziplist-value 64
 
 
# Redis将在每100毫秒时使用1毫秒的CPU时间来对redis的hash表进行重新hash，可以降低内存的使用
# 
# 当你的使用场景中，有非常严格的实时性需要，不能够接受Redis时不时的对请求有2毫秒的延迟的话，把这项配置为no。
#
# 如果没有这么严格的实时性要求，可以设置为yes，以便能够尽可能快的释放内存
activerehashing yes
 
################################## INCLUDES ###################################
 
# 指定包含其它的配置文件，可以在同一主机上多个Redis实例之间使用同一份配置文件，而同时各个实例又拥有自己的特定配置文件
 
# include /path/to/local.conf
# include /path/to/other.conf
```

## 配置系统服务 - systemd

```bash
vi /usr/lib/systemd/system/redis.service
```
```bash
[Unit]
Description=redis
After=network.target remote-fs.target nss-lookup.target

[Service]
Type=forking
ExecStart=/usr/local/redis/bin/redis-server /usr/local/redis/etc/redis.conf 
ExecStop=/usr/local/redis/bin/redis-cli shutdown
ExecReload=/bin/kill -s HUP $MAINPID
PrivateTmp=true
RestartSec=10

User=redis
Group=redis

[Install]
WantedBy=multi-user.target
```
```bash
systemctl enable redis
systemctl start redis
systemctl status redis
```

## 卸载

如果通过yum安装，使用下面命令安装。

```bash
yum remove redis
```

编译安装，删除`/usr/local/redis`目录即可，如果配置了自启动脚本，也需要删除。

## 发布与订阅

Redis的发布与订阅功能由PUBLISH、SUBSCRIBE、PSUB-SCRIBE等命令组成。

通过执行SUBSCRIBE命令，客户端可以订阅一个或多个频道，从而成为这些频道的订阅者（subscriber）：每当有其他客户端向被订阅的频道发送消息（message）时，频道的所有订阅者都会收到这条消息。

> 订阅者

```bash
redis> SUBSCRIBE test
```

> 发布者

```bash
redis> test "Redis PUBLISH test"
```

## 事务

Redis 事务的本质是一组命令的集合。事务支持一次执行多个命令，一个事务中所有命令都会被序列化。在事务执行过程，会按照顺序串行化执行队列中的命令，其他客户端提交的命令请求不会插入到事务执行命令序列中。

### 相关命令

> MULTI 、 EXEC 、 DISCARD 和 WATCH 是 Redis 事务相关的命令。

- MULTI ：开启事务，redis会将后续的命令逐个放入队列中，然后使用EXEC命令来原子化执行这个命令系列。
- EXEC：执行事务中的所有操作命令。
- DISCARD：取消事务，放弃执行事务块中的所有命令。
- WATCH：监视一个或多个key,如果事务在执行前，这个key(或多个key)被其他命令修改，则事务被中断，不会执行事务中的任何命令。
- UNWATCH：取消WATCH对所有key的监视。

> WATCH 命令可以为 Redis 事务提供 check-and-set （CAS）行为。

> 被 WATCH 的键会被监视，并会发觉这些键是否被改动过了。 如果有至少一个被监视的键在 EXEC 执行之前被修改了， 那么整个事务都会被取消， EXEC 返回nil-reply来表示事务已经失败。

```bash
redis> multi
OK
redis> set a aaa
QUEUED
redis> set b bbb
QUEUED
redis> set c ccc
QUEUED
redis> exec
1) OK
2) OK
3) OK
```

## lua脚本

### 使用场景

Lua脚本在Redis中是以原子方式执行的，在Redis服务器执行EVAL命令时，在命令执行完毕并向调用者返回结果之前，只会执行当前命令指定的Lua脚本包含的所有逻辑，其它客户端发送的命令将被阻塞，直到EVAL命令执行完毕为止。因此LUA脚本不宜编写一些过于复杂了逻辑，必须尽量保证Lua脚本的效率，否则会影响其它客户端。

- 拓展原生指令集功能。
- 减少网络开销。多个指令集同时发出，作为整体执行。
- 原子操作。对事务功能的一个替代，避免竞争。
- 复用。发送的脚本会以函数形式保存在Redis中，其他客户端也能使用。

### EVAL命令

> Redis中使用EVAL命令来直接执行指定的Lua脚本

> EVAL luascript numkeys key [key ...] arg [arg ...]

- luascript Lua 脚本。
- numkeys 指定的Lua脚本需要处理键的数量，其实就是 key数组的长度。
-key 传递给Lua脚本零到多个键，空格隔开，在Lua 脚本中通过 KEYS[INDEX]来获取对应的值，其中1 <= INDEX <= numkeys。
- arg是传递给脚本的零到多个附加参数，空格隔开，在Lua脚本中通过ARGV[INDEX]来获取对应的值，其中1 <= INDEX <= numkeys。

```bash
redis> EVAL "return 1+1" 0
(integer) 2

redis> set hello world
redis> EVAL "return redis.call('GET',KEYS[1])" 1 hello
"world"
```

> **注意** `KEYS[1]`虽然可以直接替换为hello,但是Redis官方文档指出这种是不建议的，目的是在命令执行前会对命令进行分析，以确保Redis Cluster可以将命令转发到适当的集群节点。

### call函数和pcall函数

`call()`,`pcall()` 都可以执行命令，它们唯一的区别就在于处理错误的方式，前者执行命令错误时会向调用者直接返回一个错误；而后者则会将错误包装为一个table表格：

### 值转换

由于在Redis中存在Redis和Lua两种不同的运行环境，在Redis和Lua互相传递数据时必然发生对应的转换操作，这种转换操作是我们在实践中不能忽略的。例如如果Lua脚本向Redis返回小数，那么会损失小数精度；如果转换为字符串则是安全的。

```bash
redis> EVAL "return 3.14" 0
(integer) 3
redis> EVAL "return tostring(3.14)" 0
"3.14"
```

> **注意** 传递字符串、整数是安全的，其它需要你去仔细查看官方文档并进行实际验证

### 脚本管理

Redis服务器会将所有被EVAL命令执行过的Lua脚本，以及所有被SCRIPT LOAD命令载入过的Lua脚本都保存到lua_scripts字典里面。

+  SCRIPT LOAD

    + 加载脚本到缓存以达到重复使用，避免多次加载浪费带宽，每一个脚本都会通过SHA校验返回唯一字符串标识。需要配合EVALSHA命令来执行缓存后的脚本。

    ```bash
    redis> SCRIPT LOAD "return 'hello'"
    "1b936e3fe509bcbc9cd0664897bbe8fd0cac101b"
    redis> EVALSHA 1b936e3fe509bcbc9cd0664897bbe8fd0cac101b 0
    "hello"
    ```

+  SCRIPT FLUSH

    +  既然有缓存就有清除缓存，但是遗憾的是并没有根据SHA来删除脚本缓存，而是清除所有的脚本缓存，所以在生产中一般不会再生产过程中使用该命令。

+  SCRIPT EXISTS

    +  以SHA标识为参数检查一个或者多个缓存是否存在。
    ```bash
    redis> SCRIPT EXISTS 1b936e3fe509bcbc9cd0664897bbe8fd0cac101b  1b936e3fe509bcbc9cd0664897bbe8fd0cac1012
    1) (integer) 1
    2) (integer) 0
    ```

+  SCRIPT KILL

    +  终止正在执行的脚本。但是为了数据的完整性此命令并不能保证一定能终止成功。如果当一个脚本执行了一部分写的逻辑而需要被终止时，该命令是不凑效的。需要执行SHUTDOWN nosave在不对数据执行持久化的情况下终止服务器来完成终止脚本。

## 持久化

> 持久化是将数据永久保存在磁盘中。Redis采用RDB和AOF两种策略。

### RDB 持久化

> RDB 就是 Redis DataBase 的缩写，中文名为快照/内存快照，RDB持久化是把当前进程数据生成快照保存到磁盘上的过程，由于是某一时刻的快照，那么快照中的值要早于或者等于内存中的值。

#### 手动触发

- save命令：阻塞当前Redis服务器，直到RDB过程完成为止，对于内存 比较大的实例会造成长时间阻塞，线上环境不建议使用
- bgsave命令：Redis进程执行fork操作创建子进程，RDB持久化过程由子 进程负责，完成后自动结束。阻塞只发生在fork阶段，

#### redis.conf 配置

```bash
# 周期性执行条件的设置格式为
save <seconds> <changes>

# 默认的设置为：
# 如果900秒内有1条Key信息发生变化，则进行快照；
# 如果300秒内有10条Key信息发生变化，则进行快照
# 如果60秒内有10000条Key信息发生变化，则进行快照
save 900 1
save 300 10
save 60 10000

# 以下设置方式为关闭RDB快照功能
save ""

# RDB文件在磁盘上的名称
dbfilename dump.rdb
# 文件保存路径
dir /data/redis/
# 如果持久化出错，主进程是否停止写入
stop-writes-on-bgsave-error yes
# 是否压缩
rdbcompression yes
# 导入时是否检查
rdbchecksum yes
```

#### RDB优缺点

+ 优点

    + RDB文件是某个时间节点的快照，默认使用LZF算法进行压缩，压缩后的文件体积远远小于内存大小，适用于备份、全量复制等场景；
    + Redis加载RDB文件恢复数据要远远快于AOF方式；

+ 缺点

    + RDB方式实时性不够，无法做到秒级的持久化；
    + 每次调用bgsave都需要fork子进程，fork子进程属于重量级操作，频繁执行成本较高；
    + RDB文件是二进制的，没有可读性，AOF文件在了解其结构的情况下可以手动修改或者补全；
    + 版本兼容RDB文件问题；

### AOF 持久化

> AOF(Append Only File), Redis先执行命令，把数据写入内存，然后才记录日志。日志里记录的是Redis收到的每一条命令，这些命令是以文本形式保存。而AOF日志采用写后日志，即先写内存，后写日志。

#### redis.conf配置

```bash
# appendonly参数开启AOF持久化，默认关闭
appendonly no
# AOF持久化的文件名，默认是appendonly.aof
appendfilename "appendonly.aof"
# 文件保存路径
dir /data/redis/

# 同步策略
#每修改同步：同步持久化 每次发生数据变更会被立即记录到磁盘  性能较差但数据完整性比较好。
# appendfsync always
# 每秒同步：异步操作，每秒记录 如果一秒内宕机，有数据丢失
appendfsync everysec
# 从不同步
# appendfsync no

# aof重写期间是否同步
no-appendfsync-on-rewrite no

# 重写触发配置
auto-aof-rewrite-percentage 100
auto-aof-rewrite-min-size 64mb

# 加载aof出错如何处理
aof-load-truncated yes

# 文件重写策略
aof-rewrite-incremental-fsync yes
```

#### AOF优缺点

+ 优点：
    + AOF可以更好的保护数据不丢失，一般AOF会以每隔1秒，通过后台的一个线程去执行一次fsync操作，如果redis进程挂掉，最多丢失1秒的数据。
    + AOF以appen-only的模式写入，所以没有任何磁盘寻址的开销，写入性能非常高。
    + AOF日志文件的命令通过非常可读的方式进行记录
+ 缺点：
    + 对于同一份文件AOF文件比RDB数据快照要大，恢复速度慢于rdb。
    + AOF运行效率要慢于RDB,每秒同步策略效率较好，不同步效率和RDB相同。

> **注意**：当 dump.rdb 和 appendonly.aof 两个文件同时存在时，则默认加载 appendonly.aof 文件

## Sentinel

Sentinel（哨岗、哨兵）是Redis的高可用性（high avail-ability）解决方案：由一个或多个Sentinel实例（instance）组成的Sentinel系统（system）对主从服务器进行监视。

所谓Sentinel['sentɪnl]的高可用性指系统无中断地执行其功能的能力。Sentinel的主要功能是：

- 监控Redis整体是否正常运行。
- 某个节点出问题时，通知给其他进程（比如他的客户端）。
- 主服务器下线时，在从服务器中选举出一个新的主服务器。

```bash
redis-sentinel /usr/local/redis/conf/sentinel.conf
redis-server /usr/local/redis/conf/sentinel.conf --sentinel
```

## 常见使用场景

### 缓存

> `string` 类型 热点数据，结果集

### 分布式系统数据共享

> `string` 类型 存储session token

### 分布式锁

> `setnx` 返回 `true` 才算成功

```bash
redis> set key "lock" EX 1 XX
```

### 计数器，限流

> `int` 类型 `incr` 命令

### 位统计

> `string` 类型的 `bitcount` (`bitmap` 结构)

假设现在我们希望记录自己网站上的用户的上线频率，比如说，计算用户 A 上线了多少天，用户 B 上线了多少天，诸如此类，以此作为数据，从而决定让哪些用户参加 beta 测试等活动 —— 这个模式可以使用 `SETBIT` 和 `BITCOUNT` 来实现。

```bash
# 举个例子，如果今天是网站上线的第 100 天，而用户 peter 在今天阅览过网站，那么执行命令 
redis> SETBIT peter 100 1
# 如果明天 peter 也继续阅览网站，那么执行命令 
redis> SETBIT peter 101 1
# 当要计算 peter 总共以来的上线次数时，就使用 BITCOUNT 命令：执行 
redis> BITCOUNT peter
2
```

### 商城购物车

> `string` | `hash`

数量 `incr` `decr` 修改

### 消息队列

- 队列：先进先除：`rpush` `blpop`，左头右尾，右边进入队列，左边出队列
- 栈：先进后出：`rpush` `brpop`

### 抽奖

> `spop`  移除并返回集合中的一个随机元素

### 点赞，标签

```bash
# 点赞
redis> sadd like:<id> <uid>
# 取消点赞
redis> srem like:<id> <uid>
# 是否点赞
redis> sismember like:<id> <uid>
# 点赞所有用户
redis> smembers like:<id>
# 点赞数
redis> scard like:<id>
```

### 用户关注

```bash
# 相互关注：
redis> sadd 1:follow 2
redis> sadd 2:fans 1
redis> sadd 1:fans 2
redis> sadd 2:follow 1

# 我关注的人也关注了他(取交集)：
reids> sinter 1:follow 2:fans

# 用户1可能认识的人(差集)：
redis> sdiff 2:follow 1:follow
```

### 排行榜

```bash
# 今天新闻id 点击数 +1 
redis> zincrby news:<today time> 1 <new id>

# 获取今天点击数最多的15条
redis> zrevrange news:<today time> 0 15 withscores
```
