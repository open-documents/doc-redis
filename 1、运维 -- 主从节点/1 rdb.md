
[Redis persistence | Redis](https://redis.io/docs/management/persistence/)



触发redis rdb方式的持久化，有两种方式：
- 自动保存。
- 手动保存。
下面展开介绍。

# 1、自动保存

redis对于rdb机制的自动触发，只要满足下面一个条件就会触发：
- 满足redis配置文件的配置要求。
- 执行flushdb 或 flushall命令。注意：此时触发自动保存后生成的rdb文件里面是没有数据的。
- 执行shutdown命令。


# 2、手动保存

手动触发rdb保存，有下面的两种方式：
- 执行save命令。
- 执行bgsave命令。（默认）

1）save

2）bgsave