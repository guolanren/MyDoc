# Redis Map

------

## Install

```shell
# 1. 创建目录
shell> mkdir -p /opt/redis
# 2. 下载/解压 redis-5.0.5.tar.gz
shell> wget http://download.redis.io/releases/redis-5.0.5.tar.gz /opt/redis
shell> tar -zx -f redis-5.0.5.tar.gz -C /opt/redis
shell> cd /opt/redis/redis-5.0.5
shell> mkdir /opt/redis/redis-5.0.5/source && mv * /opt/redis/redis-5.0.5/source
# 3. 创建 conf 文件夹放置配置文件
shell> mkdir /opt/redis/redis-5.0.5/conf
shell> cp /opt/redis/redis-5.0.5/source/redis.conf /opt/redis/redis-5.0.5/conf
# 4. 编译依赖项
shell> cd /opt/redis/redis-5.0.5/source/deps
shell> make hiredis jemalloc linenoise lua
# 5. 编译/安装 redis
shell> cd /opt/redis/redis-5.0.5/source
shell> make && make PREFIX=/opt/redis/redis-5.0.5 install
# 6. 服务自启
shell> cp /opt/redis/redis-5.0.5/source/utils/redis_init_script /etc/init.d/redis
shell> chkconfig --add redis
```

------

## Copy

```shell
# 1. 复制配置
shell> cp /opt/redis/redis-5.0.5/conf/redis.conf /opt/redis/redis-5.0.5/conf/redis-slave.conf
# 2. 修改 slave 配置
shell> vi redis-slave.conf
	port 6380
	pidfile /var/run/redis_6380.pid
	logfile "/var/log/redis/redis-slave.log"
	dir /var/redis/slave
	replicaof 172.17.0.17 6379
	masterauth ***
```
------

## Sentinel

```shell
# 1. 复制 sentinel 配置文件
shell> cp /opt/redis/redis-5.0.5/source/sentinel.conf /opt/redis/redis-5.0.5/conf
# 2. 修改配置文件
shell> vi sentinel.conf
	daemonize yes
	pidfile "/var/run/redis-sentinel.pid"
	logfile "/var/log/redis/sentinel.log"
	sentinel monitor mymaster 172.17.0.17 6379 2
	sentinel auth-pass mymaster ****
# 3. 启动 sentinel
shell> /opt/redis/redis-5.0.5/bin/redis-server /opt/redis/redis-5.0.5/conf/sentinel.conf --sentinel
```
