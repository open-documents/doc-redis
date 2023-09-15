
参考文档：https://redis.io/docs/interact/programmability/eval-intro/

Redis客户端能够上传脚本到Redis服务端，Redis服务端通过内嵌的脚本引擎（Lua5.1解释器）来执行Lua脚本。

Redis服务端执行Lua脚本的特点：
- 原子性。Redis服务端执行Lua脚本时，会阻塞其他命令的执行，由此来保证Redis服务端执行Lua脚本的原子性。

# 1、传递Lua脚本并执行

Redis客户端使用EVAL命令来向Redis服务器传递Lua脚本并让Redis服务器执行所传递的Lua脚本。
```
$ EVAL LUA-Script Key_parameter_num [Regular_parameter_1] [Regular_parameter_2] .. Key_parameter_1 .. Key_parameter_num
```
- LUA-Script：Redis客户端向服务端发送的需要执行的Lua脚本。
- Key_parameter_num：向Lua脚本中传递的属于Redis服务端的键的名称的参数数量。
- Regular_parameter_1、Regular_parameter_2：向Lua脚本中传递的非Redis服务端的键的名称的参数。
- Key_parameter_1、Key_parameter_num：向Lua脚本中传递的是Redis服务端的键的名称的参数。

	重要：为了保证Lua脚本在单机和集群模式下的正确执行，在Lua脚本中访问的Redis服务端的键的名称必须通过参数传递，而不能是在Lua脚本中动态生成的。

# 2.1、缓存Lua脚本（SCRIPT LOAD）、执行缓存脚本（EVALSHA）

如果每次通过上面的方式（即EVAL 脚本）多次执行相同的脚本，需要在每次的请求中附带相同的脚本内容，这会浪费网络资源，因此Redis服务端可以缓存相同的脚本内容。

1）首先使用 `SCRIPT LOAD` 命令将脚本上传到Redis服务端，并在Redis服务端缓存这个脚本，这个命令会返回脚本内容的SHA1值。
```bash
SCRIPT LOAD "return 'Immabe a cached script'"
"c664a3bf70bd1d45c4284ffebb65a6f2299bfc9f"
```
2）然后使用 `EVALSHA` 命令执行服务端缓存的脚本。EVALSHA命令和EVAL命令，除了前者传递的是脚本SHA1值，后者传递整个Lua脚本外，格式完全相同。
```
EVALSHA c664a3bf70bd1d45c4284ffebb65a6f2299bfc9f 0
"Immabe a cached script"
```

# 2.2、 缓存的Lua脚本的时效性

Redis客户端上传Lua脚本到Redis服务端，然后Redis服务端缓存的Lua脚本并不是Redis服务端数据库的一部分，并不会被持久化。这就意味着下面的情况发生时，Redis服务端缓存的Lua脚本会丢失：
- Redis服务端重启。
- Redis服务端master变成了slave。
- Redis服务端执行了SCRIPT FLUSH命令。

如果Redis客户端通过EVALSHA命令执行了一个Redis服务端不存在的缓存Lua脚本，会返回错误信息。此时，Redis客户端需要重新上传Lua脚本，并让Redis服务端缓存。
```
EVALSHA ffffffffffffffffffffffffffffffffffffffff 0
(error) NOSCRIPT No matching script
```


# 结束Redis服务端正在执行的Lua脚本（SCRIPT KILL）

SCRIPT KILL是唯一一个方法来停止Redis服务端正在执行的Lua脚本。

当Redis服务端正在执行的Lua脚本执行时间过长（超过了配置的最大执行时间），可以使用SCRIPT KILL命令来让Redis服务端结束正在执行的Lua脚本。

	注意：SCRIPT KILL 只能结束对数据库没有修改的Lua脚本, 结束对数据库产生修改的Lua脚本只能通过重启Redis服务器.


# Lua脚本内容

## Lua脚本中访问参数中传递的键名称和普通参数

向Lua脚本中传递参数时，有些参数可能是Redis中键的名称，有些参数可能仅仅只是普通的字符串。下面分别介绍如何向Lua脚本中传递键的名称和普通的字符串参数，以及如何在Lua脚本中访问它们。

## Lua脚本中调用Redis命令

在Lua脚本中需要调用Redis提供的命令，如Set等，需要使用redis.call()或redis.pcall()。

两个在正常执行的时候作用一样，不同在于执行Redis名称出现异常时。
- 



# 管道中执行缓存Lua脚本

参考文档：https://redis.io/docs/interact/programmability/eval-intro/（`EVALSHA` in the context of pipelining部分）



