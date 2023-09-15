
Redis有很多优势，如下：

- Redis性能极高，读的速度是110000次/s，写的速度81000次/s
- Redis数据类型丰富
- Redis支持数据的持久化，可以将内存中的数据保存到磁盘中，重启的时候可以再次加载进行使用。
- Redis支持数据的备份，即master-slave模式的数据备份


- Redis命令不区分大小写，key区分大小写。



# 命名规则

如果redis版本第2位是奇数，是不稳定版本，如2.7、2.9、3.1等。
如果redis版本第2位是偶数，是稳定版本，如2.8、3.0、3.2等。
当前奇数版本就是下一个稳定版本的开发版本，如2.9版本是3.0版本的开发版本。



# 1、下载

获取redis-x.x.x.tar.gz包的途径主要有两种方式：

1）从Windows中下载，然后上传到Linux中。

2）直接在Linux中下载。
```bash
wget https://download.redis.io/releases/redis-6.2.6.tar.gz
```

# 2、安装

下面介绍向CentOS7中安装Redis。

## 1）安装依赖 

redis是由C语言开发，因此安装之前必须要确保服务器已经安装了gcc，可以通过如下命令查看机器是否安装：
```bash
gcc -v
```
如果没有安装则通过以下命令安装：
```bash
yum install -y gcc
```

## 2）解压、编译、安装

1.首先将redis包解压。
```bash
tar -xzvf redis-6.2.6.tar.gz
```
2.进入到解压后redis目录。
```bash
cd xxx # xxx为解压后redis的目录
```
3.编译。
```bash
make 
```
4.安装。

安装redis，会多出多个(6个)命令，默认情况下是安装到/usr/local/bin，可以通过PREFIX进行指定安装位置。
```bash
sudo make install PREFIX=/home/xxx/usr/ # PREFIX指定安装目录 
```
出现下面的结果，说明安装成功。
```bash
# cd src && make install
# which: no python3 in (/usr/local/sbin:/usr/local/bin:/sbin:/bin:/usr/sbin:/usr/bin:/root/bin)
# make[1]: 进入目录“/home/xcxiao/usr/redis-7.0.11/src”
# 
# Hint: It's a good idea to run 'make test' ;)
# 
#     INSTALL redis-server
#     INSTALL redis-benchmark
#     INSTALL redis-cli
# make[1]: 离开目录“/home/xcxiao/usr/redis-7.0.11/src”
```

至此redis就安装完成可以使用了。

# 卸载

redis的卸载很简单，只要删除掉安装出的redis命令就行，进入到redis命令所在目录，执行下面的命令。
```bash
rm -rf redis-*
```




前面已经安装好了redis，会多出多个命令。

- redis-benchmark：性能测试工具，服务启动后运行该命令，可以查看机器性能。
- redis-check-aof：修复有问题的AOF文件。
- redis-check-dump：修复有问题的dump.rdb文件。
- <font color="00E0FF">redis-cli</font>：客户端，操作入口。
- <font color="00E0FF">redis-serveri</font>：redis服务器启动命令。
- redis-sentinel：redis集群使用。



# 启动

在启动之前，先对配置文件做基本改动。

使用redis-server命令启动redis服务器，启动时，需要传入redis的配置文件路径。
```bash
redis-server /home/xcxiao/opt/redis-7.0.11/redis.conf
```
这样redis服务器就启动了。

# 连接

redis服务器启动后，可以使用redis-cli连接redis服务器。

```bash
redis-cli -a 123456 -p 6379
```
常用参数如下：
- -h：指定连接redis服务器的ip地址。不指定时，默认连接本地redis服务器，即127.0.0.1。
- -p：指定连接redis服务器的端口。不指定时，默认端口号是6379。
- -a(auth)：指定连接redis服务器密码。

通过这种方式连接redis服务器会出现下面的警告。
```bash
Warning: Using a password with '-a' or '-u' option on the command line interface may not be safe.
```
可以通过下面的方式连接，来屏蔽警告。
```bash
redis-cli -a 123456 -p 6379 2>/dev/null
```
执行完连接命令后，界面转变成下面这样。
```bash
127.0.0.1:6379>
```
正常做完上面的操作后，查询redis是否连接成功，使用ping命令。
```bash
127.0.0.1:6379> ping
```
出现下面的结果，表示redis已经连接成功。
```bash
PONG
```
如果在连接时没有通过-a指定连接密码，会出现下面的结果。
```bash
(error) NOAUTH Authentication required.
```
需要使用auth命令传输密码，即可正常连接到redis服务器。
```bash
127.0.0.1:6379> auth 123456
```

# 关闭

关闭redis服务器，有两种方式：

1）一种是在redix客户端内部关闭：
```bash
127.0.0.1:6379> SHUTDOWN
```
2）另外一种是借助redis-cli命令向redis服务器发送关闭命令。
```bash
redis-cli -a 123456 shutdown
```

