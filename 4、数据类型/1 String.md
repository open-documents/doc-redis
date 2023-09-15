
String是redis中最基本的数据类型，一个key对应一个value，此时的value是string类型。

- 二进制安全的，即redis中string可以代表任何数据，如图片、序列化的对象。
- 最大长度是512M。

# 1.1.1、设置单个字符串

基本语法：
```redis
set k1 v1          # 设置k1:v1键值对
```
1）设置值的指定范围。（setrange key offset）
```redis
set k1 1a23
setrange k1 1 666  # 166623
```

有下面的可选功能：

1.1）设置key的过期时间，以秒为单位。（<font color="00E0FF">参数EX</font>，等效于SETEX命令，SETEX未来可能会被淘汰）
```redis
set k1 v1 ex 5      # k1的过期时间为5s
```
1.2）设置key的过期时间，以毫秒为单位。（<font color="00E0FF">参数PX</font>，等效于PSETEX命令，PSETEX未来可能会被淘汰）
```redis
set k1 v1 px 5000   # k1的过期时间为5s
```
1.3）设置key的过期时间，以秒为单位的UNIX时间戳所对应的时间。（<font color="00E0FF">参数EXAT</font>）（6.2新增）
```redis

```
1.4）设置key的过期时间，以毫秒为单位的UNIX时间戳所对应的时间。（<font color="00E0FF">参数PXAT</font>）（6.2新增）
```redis

```
2）键存在时，才会设置键。（<font color="00E0FF">参数XX</font>）
```redis
set k1 v1 xx
```
3）键不存在时，才会设置键。（<font color="00E0FF">参数NX</font>，等效于SETNX命令，SETNX未来可能会被淘汰）
```redis
set k1 v1 nx
```
4）保留键的生存时间。（<font color="00E0FF">参数KEEPTTL</font>）
```redis
set k1 v1 ex 5     # k1的过期时间5s,即5s后过期
set k1 v2          # k1的过期时间-1,即永不过期
set k1 v3 keepttl  # k1的过期会续接还剩的过期时间,即比5s少
```
5）返回键的值。（<font color="00E0FF">参数GET</font>）（6.2新增）
```redis
set k1 oldValue 
set k1 newValue get // 返回oldValue
```
# 1.1.2、向单个字符串追加内容（append）
```redis
set k1 abc
append k1 efg    # k1 : abcdefg
```
-- --
# 1.2、设置多个字符串（mset）

1）mset
```redis
mset k1 v1 k2 v2 ... 
```
2）msetnx
```redis
msetnx k1 v1 k2 v2
```
需要注意的是，同时设置多个key:value时，只有当全部设置成功，才会成功，一个失败，整个设置都不会生效。举个例子：
```redis
set k1 v1
msetnx k1 v2 k2 v2 # k1存在,所以尽管k2不存在,也不会设置成功 
```
-- -- 
# 2.1、获取单个字符串（get）

基本语法：
```redis
get key
```
# 2.2、获取单个字符串的指定范围（getrange）
```redis
set k1 0123456
getrange k1 0 -1   # 0123456
getrange k1 0 3    # 0123
```
-- --
# 3、获取多个字符串（mget）

基本语法：
```redis
mget k1 k2 ..
```
-- --
# 4、数学运算

1）自增（incr）
```redis
set k1 100
incr k1           # 101
```
2）增加（incrby）
```redis
set k1 100
incrby k1 100     # 200
```
3）自减（decr）
```redis
set k1 100
decr k1           # 99
```
4）减少（decrby）
```redis
set k1 100
decrby k1 100     # 0
```
# 5、获取字符串长度（strlen）
```redis
set k1 123
strlen k1     # 3
```



