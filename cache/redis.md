# Redis

## ## 数据类型（5种）

- String：最大512MB
- hash：HMSET runoob field1 "Hello" field2 "World"。
- list：字符串列表，按照插入排序。lpush runoob string
- set：sadd runoob string 
- sortedSet(zset)：zadd key score member 

## Redis线程模型

非阻塞IO，多路复用

![img](http://static2.iocoder.cn/images/Redis/2019_11_22/01.png)

多路复用程序监听多个socket，并把所有socket的命令放到一个队列中。文件事件分派器每次取一个请求，并分发给不同的处理器来处理。

#### 一次redis命令通信

1.客户端向redis发送建立连接的请求。server socket会产生一个AE_READABLE事件，并放入队列。

2.文件分派器获取到这个AE_READABLE事件，分配给**连接应答处理器**，创建一个socket01，并将AE_READABLE事件与命令处理器关联。

3.客户端此时发送一个命令，此时redis的socket01会产生一个AE_READABLE事件,多路复用将此事件放入队列。因为socket01的AE_READABLE事件已经绑定过，则会分发到**命令处理器**，来进行处理。

处理完毕后，将一个AE_WRITABLE事件与**回复管理器**关联。

4.客户端在准备好接收返回数据后，会发送一个AE_WRITABLE事件，这个事件被redis压入队列，再由**回复管理器**响应。

## redis的数据淘汰（6种）

- volatile-lru

从已经过期的数据集中选取最近最少使用

- allkeys-lru

所有数据中LRU

- volatile-ttl

从已经设置过期时间的数据集中挑选

- volatile-random

从已经设置过期时间的数据集中随机挑选

- allkeys-random

所有数据集中随机淘汰

- no-enviction

数据永不过期。默认为此项



