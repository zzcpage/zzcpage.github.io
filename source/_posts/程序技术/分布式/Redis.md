---
title: Redis初步了解
categorise:
    - 分布式
    - 数据库
tags:
    - 缓存数据库
    - 分布式
    - 数据库
---

#### 什么是Redis？

Redis是一个开源的使用ANSI C语言编写，遵循BSD协议，支持网络，可基于内存亦可持久化的日志型,Key-Value数据库，并提供多种语言的API。

Redis具有如下特点：
>1. 支持数据的持久化：可以将内存中的数据保存在磁盘中，重启的时候再次加载进行使用。
>2. 不仅支持简单的key-value类型的数据，同时还提供list,set,sorted set,hash等数据结构的存储，因此也被称为数据结构服务器。
>3. 支持master-slave模式的数据备份。
Redis作为现在流行的Key-Value数据库，优势在于:
>1. 基于内存，性能极高。Redis读的速度是110000次/秒，写的速度是81000次/秒
>2. 支持丰富的数据,例如key/value,list,set,sorted set,hash等数据结构
>3. 具有丰富的特性，比如支持publish/subscribe,通知,key过期等。

#### Redis的安装步骤

>1. 在官网上下载redis的安装包
Windows版本由Microsoft Open Tech group小组维护，GitHUb版本下载地址:https://github.caom/MSOpenTech/redis/releases

>2. 启动Redis服务
下载安装后，运行redis-server。exe，启动Redis服务，Redis服务端的默认连接端口是6379。

>3. Redis的配置

Windows下的配置文件位：redis.windows.conf,Linux下的配置文件为:redis.conf。
在启动redis-server的时候，可以在命令后面指定配置文件，例如:redis-server redis.conf。

Redis配置文件可用的参数如下所示:
|参数|功能|实例|
|:----:|:----:|:----:|
|daemonize|Redis默认不是以守护进程的方式运行，设置为yes,启用守护进程|daemonize yes
|pidfile|当Redis以守护进程方式运行时，Redis默认会把pid写入/var/run/redis.pid文件，可以使用pidfile指定路径|pidfile /var/run/redis.pid|
|port|指定Redis监听端口，默认端口为6379|port 6379|
|bind|绑定的主机地址，可以用于限制连接|bing 127.0.0.1|
|timeout|客户端闲置timeout时间后关闭连接，如果设置为0，则表示关闭此功能|timeout 0 |
|loglevel|指定日志记录级别。Redis总共支持四个级别：debug,verbose,notice,warning,默认为verbose|loglevel verbose|
|logfile|日志记录方式，默认为标准输出,也可以自己指定日志文件的路径，例如"xxx/xxx/xxx.txt"|logfilt stdout|
|databases|设置数据库的数量，默认数据库为0|databases 16|
|save <seconds> <changes>|指定seconds时间内，有changes次更新操作，就将数据同步到数据文件|save 900 1|
|rdbcompression|指定存储至本地数据库时是否压缩数据，默认为yes,Redis采用LZF压缩|rdbcompression yes|
|dbfilename|指定本地数据库文件名，默认值为dump.rdb|dbfilename dump.rdb|
|dir|数据库镜像备份的文件放置的路径|dir ./|
|slaveof <masterip> <masterport>| 设置改数据库为其他数据库的slvae数据库，参数为master服务的IP地址及端口，在Redis启动时，会自动从master进行数据同步|slaveof 127.0.0.1 5000|
|masterauth <masterpassword>|当master服务设置了密码保护时，slave服务连接master的密码|Masterauth password|
|requriepass|设置Redis连接密码|requirepass password|
|maxclients|设置同一时间最大客户端连接数，默认无限制|maxclients 128|
|maxmemory<bytes>|指定Redis最大内存限制，当内存满了的时候，redis会尝试剔除设置过expire信息的key，而不管改key的过期时间是否到达|Maxmemory 1024|
|appendonly|指定是否在每次更新操作后进行日志记录，Redis在默认情况下是异步的把数据写入磁盘|appendonly no|
|appendfilename|指定更新日志文件名，默认为appendonly.aof|appendflename appendonly.aof|
|appendfsync|指定更新日志条件，共有三个可选值。no:表示等操作系统进行数据缓存时同步到磁盘，最快的方式。always:表示每次更新操作后手动调用。fsync()将数据写到磁盘，安全但是有点慢。everysec：表示每秒同步一次，默认值。|appendfsync everysec|
|vm-enabled|指定是否启用虚拟内存机制，默认值为no|vm-enabled no|
|vm-swap-file|虚拟内存文件路径，默认为/tmp/redis.swap|vm-swap-file /tmp/redis.swap|
|vm-max-memory|将所有大于vm-max-memory的数据存入虚拟内存，当cm0max0memory设置为0的时候，将所有value都存在磁盘，默认值为0，最好不要使用默认值|vm-max-memory 0|
|vm-page-size|设置虚拟内存的页大小|vm-page-size 32|
|vm-pages|设置swap文件中的page数量|vm-pages 134217728|
|vm-max-threads|设置访问swap文件的线程数，最好不要超过机器的核数。默认为4|vm-max-threads 4|
|glueoutputbuf|设置在想客户端应答时，是否把较小的包合并为一个包发送，默认为开启|glueoutputbuf yes|
|hash-max-zipmap-entries|当hash中包含超过指定元素个数并且最大的元素没有超过临界时，hash将以一种特殊的编码方式来存储，可以大大减少内存使用。|hash-max-zipmap-entries 64/hash-max-zipmap-value 512|
|activerehashing|开启后，redis将在每100毫秒使用1毫秒CPU时间，来对redis的hash表进行重新hash,可以降低内存的使用，默认开启。|activerehashing yes|


#### Redis的数据类型和操作

Redis支持五种数据类型:string(字符串),hash(哈希),list(列表),set(集合)以及sorted set（有序集合）.

>#### 1. String类型

string是Redis最基本的类型，一个key对应一个value。string类型可以包含任何数据，比如jpg图片或序列化的对象。一个key最大能存储512M。

>set和get操作string类型

set s1 v2
get s1

>#### 2. hash类型

Redis的hash类型是一个string类型的field和value的映射表，特别适合用于存储对象。
string类型的key,value类型，一般用于存储单独的键值对的类型，而对于hash类型则是key-file类型，一个键对应了一个对象，而对象内部可以具有多个属性。

>hset和hget操作hash类型
hset key s1 v1 s2 v2...

>#### 3. list类型

list类型是一个双向键表，每个子元素都是string类型，最多存储2^32-1个元素，大约40多亿。
可以使用lpush和lpop进行从链表头和尾添加和删除元素。操作的key可以理解为链表的名字。

>lpush和lpop操作list类型
lpush list_name value

>#### 4. set类型

set是string类型的无序集合，最大可以包含大约40多亿个元素。集合可以添加删除，求交并差。
key可以理解为集合的名字。通过sadd添加元素。
>sadd 操作set类型

>#### 5. sorted set类型

sorted set不允许重复的成员，在set的基础上增加了一个顺序属性，并且每个元素可以关联一个double类型的score。
sorted set会根据score进行排序操作。
