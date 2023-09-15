
List是简单的字符串列表。
- List的底层是双端队列。即按照插入顺序，可以添加一个元素到列表的头部（左边）或尾部（右边）。
- 最多包含2^32-1个元素（超过40亿个元素）。

操作流程：
- 如果key不存在，则创建链表。
- 如果key已经存在，则新增内容。
- 如果值全部移除，则删除链表。

特点：
- 对两端的操作性能很高。

# 1、lpush和rpush

lpush：将元素挨个从双端队列的左边加入。（和输入顺序相反）
rpush：将元素挨个从双端队列的右边加入。（因为符合输入顺序，所以用得比较多）
```redis
lpush k1 1 2 3 4    # k1 : [4 3 2 1] 
rpush k1 5 6        # k1 : [4 3 2 1 5 6]
```

# 

lpop：从双端队列左边出队。
rpop：从双端队列右边出队。
```redis
rpush list 1 2 3 4   # list : [1 2 3 4]
lpop list            # list : [2 3 4]
rpop list            # list : [2 3]
```

# 去重

语法： lrem key num value
功能：删除列表中num个值为value的元素。（num为0时代表删除所有）
```redis
rpush list 3 2 3 4   # list : [3 2 3 4]
1) lrem list 1 3        # list : [2 3 4] 
2) lrem list 0 3        # list : [2 4]
```
# lset

语法：lset key index value 

# insert

语法：insert key before/after


# 获取指定范围内的list


```redis
ltrim 
```

# 2.1、范围查询

```redis
rpush list 1 2 3 4   # list: [1 2 3 4]
lrange list 0 -1     # 1 2 3 4
```

# 按索引查询

```redis
rpush list 1 2 3 4   # list: [1 2 3 4]
lindex list 1        # 2
```

# 获取元素个数

```redis
rpush list 1 2 3 4   # list: [1 2 3 4]
llen list            # 4
```


# rpoplpush

