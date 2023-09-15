
# 1、对hset的操作

）创建一个hset对象。<font color="00E0FF">（hset key field1_name field1_value [field2_name field2_value..]）</font>
```redis
hset student1 name zhangsan age 18
```
）删除整个对象。
```redis
del key
```
）查询所有filed:value键值对。（<font color="00E0FF">hgetall</font> key）
```redis
hgetall student1 
# name 
# zhangsan
# age
# 18
```

# 2、对属性的操作

）查询一个属性的值。（<font color="00E0FF">hget</font> key field）
```
hget student1 name      
# zhangsan
```
）查询多个属性的值。（<font color="00E0FF">hmget</font> key field1 field2 ..）
```redis
hmget student1 name age
# zhangsan
# 18
```
）查询某个属性是否存在。（<font color="00E0FF">hexists</font> key field）
```redis
hexists student1 name 
# 1
hexists student1 id
# 0 
```
）查询所有属性的名称。（<font color="00E0FF">hkeys</font> key）
```redis
hkeys student1
# name
# age
```
）查询所有属性的值。（<font color="00E0FF">hvals</font> key）
```redis
hvals student1
# zhangsan 
# 18
```
）查询key中属性数量。（<font color="00E0FF">hlen</font> key）
```redis
hlen student1
# 2
```
）添加属性，属性不存在时操作。（hsetnx key field） 
```redis
hsetnx student1 id
# 1
hsetnx student1 id
# 0
```
）删除某个属性。
```redis
hdel key field
```
）属性值增加整数数值。（<font color="00E0FF">hincrby</font> key field increment）
```redis
hincrby student age 1
# 19
```
）属性值增加浮点数数值。（<font color="00E0FF">hincrbyfloat</font>  key increment）
```redis 
hincrbyfloat student1 age 0.5
# 19.5
```

