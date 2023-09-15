
参考文档：https://redis.io/docs/data-types/sorted-sets/

ZSet特点：
- 不重复性：和Set一样，是String类型元素的集合，也<font color=44cf57>不允许重复元素</font>。
- 有序性：ZSet的每个元素会关联一个double类型分数，1）<font color=44cf57>通过分数对元素进行排序，分数小的排前面</font>，2）<font color=44cf57>当分数一样时，按照字母表排序</font>。

# 基础操作

）添加：`zadd key score element`（时间复杂度：O(log(N))）
```bash
> zadd hackers 1940 "Alan Kay"
(integer) 1
> zadd hackers 1957 "Sophie Wilson" 1953 "Richard Stallman"
(integer) 2
```

.1）查询：`zrange key start-index end-index`（分数小的排前面）
```bash
> zrange hackers 0 -1
1) "Alan Kay"
2) "Richard Stallman"
3) "Sophie Wilson"
```
.2）反序查询：`zrevrange key  start-index end-index`（分数大的排前面）
```bash
zrevrange hackers 0 -1
1) "Sophie Wilson"
2) "Richard Stallman"
3) "Alan Kay"
```


- 底层通过哈希表实现，所以添加、删除、查询的复杂度都是O(1)。
- 最大成员数是2^32-1


# 数据结构 -- 跳表 

Redis