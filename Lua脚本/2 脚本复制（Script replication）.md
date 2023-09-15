
参考文档：https://redis.io/docs/interact/programmability/eval-intro/#script-replication

因为Lua脚本可能会包含修改操作，因此Redis需要将Lua脚本中的写操作发送给replicas来保持主从一致性。Lua脚本中涉及修改操作时，Redis有2种方法来维持主从间的一致性：

1）全量复制（Verbatim replication）：Redis master将整个Lua脚本发送给replicas。
- 优点：很短的Lua脚本产生大量的命令（如一个简单的for语句产生大量的命令）时，可以节省replicas的带宽。
- 缺点1：replicas需要将Lua脚本在master中做的动作在自身上重新执行一遍，这在某些情况下是很浪费的（如Lua脚本中包含大量查询操作）。
- 缺点2（更重要的）：这种方式需要Lua脚本是写确定的（关于什么是写确定后续介绍）。

2）影响复制（Effects replication）：只向Redis replicas发送Lua脚本中修改命令。
- 优点：对脚本的写确定命令是没有限制的。
- 缺点：可能会因为向Redis replicas发送一系列写命令而浪费一部分网络带宽。

下表描述了不同Redis版本对两种方式的支持。

| 版本    |                                                                                                                         |
| ------- | ----------------------------------------------------------------------------------------------------------------------- |
| ~3.2    | Verbatim replication                                                                                                    |
| 3.2~5.0 | - Verbatim replication(默认)</br>- Effects replication: 需要使用lua-replicate-commands或 redis.replicate_commands()开启 |
| 5.0~7.0 | - Verbatim replication</br>- Effects replication(默认)                                                                  |
| 7.0~    |- Effects replication|

# Effects replication

通过这种方式，master执行Lua脚本的过程中会收集Lua脚本中修改了数据库的命令操作，当Lua脚本执行完成后，这些命令会被包含在一个 MULTI/EXEC 事务中，然后发送给replicas和master的aof。

出现下面的情况时，考虑使用这种方式：
- 整个Lua脚本执行时间较长，但是整个Lua脚本对数据库的影响可以用简单的几个写命令总结。
- Lua脚本中包含写不确定命令时，必须使用这种方式。
- Lua PRNG在每次调用时使用不同的种子。

	注意: 如果这种方式没有在配置中配置或者这种方式不是默认的方式时，Lua脚本中的redis.replicate_commands()需要执行在Lua脚本中所有写操作之前.


# 写确定操作

使用全量复制（Verbatim replication）脚本的方式时，向replicas和master的aof中写入的是Lua脚本的内容，而非最后影响数据库的命令。举个例子：

这种情况就称

使用影响复制（Effects replication）脚本的方式时，向replicas和master的aof中写入的是影响数据的命令，而非Lua脚本生成的命令。举个例子：

