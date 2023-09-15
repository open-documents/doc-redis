
要启用rdb，需要对配置文件做说明。

rdb在配置文件中，存在于### SNAPSHOTTING ### 片段。
```text
# rdb文件的名称
dbfilename dump.rdb

# 工作目录,rdb文件和aof文件都会创建在该目录下
dir ./


save 3600 1 300 100 60 10000       # 第一个配置项

stop-writes-on-bgsave-error yes
# 是否使用LZF算法压缩,默认启动
#   - 开启压缩: 会消耗一定的CPU资源
#   - 关闭压缩: 可以节约一定CPU资源
rdbcompression yes

# 是否进行CRC64校验,校验和会附加在rdb文件的末尾,默认开启
#   - 进行CRC64校验:   会牺牲10%
#   - 不进行CRC64校验: rdb文件末尾附0
rdbchecksum yes

sanitize-dump-payload no


rdb-del-sync-files no


dir ./
```
## 第一个配置项

用来说明redis执行rdb需要满足的条件，语法是 `save <seconds> <changes> [<seconds> <changes>]`。

一个配置项有两个数字：seconds、changes。
- seconds：时间频率。即每隔这么长时间保存一次。
- changes：修改次数要求。即需要达到该修改次数才会保存。
changes的优先级是高于seconds，即即使到了需要保存的时间，但是在该时间间隔内没有达到指定的修改次数，也不会进行持久化，那么只能等到下一个时间间隔。

redis7之前和redis7之后，默认的该项配置发生了一点变化。
1）redis7之前是下面这样：
```text
save 900 1
save 300 10
save 60 10000
```
2）redis7之后是下面这样：
```text
save 3600 1 300 100 60 10000
```
可以看到，redis7之后，redis默认的保存频率变低了。

# 执行rdb

