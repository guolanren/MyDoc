# Redis Map

------

## Redis 安装

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
