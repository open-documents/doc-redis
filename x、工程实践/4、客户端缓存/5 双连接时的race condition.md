
当使用RESP2时，往往需要使用2个连接，一个用来Redis服务器进行数据查询（简记为D），一个用于接收缓存失效信息（简记为I）。这样，可能会出现race condition。

考虑正常业务逻辑如下。
```text
向Redis查询foo
等待结果...
修改缓存内容   
```
出现了race condition，即在等待结果的过程中先收到foo失效的信息。。
```text
[D] client -> server: GET foo
[I] server -> client: Invalidate foo (somebody else touched it)
[D] server -> client: "bar" (the reply of "GET foo")
```
一个好的解决办法是当向Redis服务器查询时，先用占位符填充缓存的值，当收到foo失效信息时，删除foo所在条目。修改业务逻辑如下。
```text
向Redis查询foo,缓存中用占位符来填充foo对应的值
等待结果...
修改foo缓存内容
```
这样当最后收到foo查询的过期值时，不进行设置。
```text
Client cache: set the local copy of "foo" to "caching-in-progress"   # 用占位符填充
[D] client-> server: GET foo.
[I] server -> client: Invalidate foo (somebody else touched it)
Client cache: delete "foo" from the local cache.
[D] server -> client: "bar" (the reply of "GET foo")
Client cache: don't set "bar" since the entry for "foo" is missing.
```

使用单连接时不存在race condition的问题。