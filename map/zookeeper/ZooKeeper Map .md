# ZooKeeper Map

------

## Install

```shell
# 1. 下载zookeeper-3.4.12.tar.gz
shell> wget http://mirror.bit.edu.cn/apache/zookeeper/stable/zookeeper-3.4.12.tar.gz
# 2. 解压zookeeper-3.4.12.tar.gz
shell> tar -zx -f zookeeper-3.4.12.tar.gz
# 3. 复制conf/zoo_sample.cfg
shell> cp zoo_sample.cfg zoo.cfg
# 4. 设置dataDir目录，配置"host:通信port:选举port"
shell> vim conf/zoo.cfg
	...
	dataDir=/var/zookeeper
	...
	server.1=192.168.225.128:2888:3888
	server.2=192.168.225.129:2888:3888
	server.3=192.168.225.130:2888:3888
# 5. 将zookeeper复制到其它主机
shell> scp -r /opt/zookeeper-3.4.12 root@slave1:/opt
shell> scp -r /opt/zookeeper-3.4.12 root@slave2:/opt
# 6. 添加myid文件
shell> mkdir /var/zookeeper
shell> vi /var/zookeeper/myid
	1
# 7. 启动zookeeper
shell> . bin/zkServer.sh [start | start-foreground]
# 8. 验证是否启动成功
# 使用telnet方式, 安装telnet
shell> yum install -y telnet
# 连接、打印zookeeper状态
shell> telnet master 2181
	stat
# 使用netcat方式, 安装netcat
shell> yum install -y nc
# 打印zookeeper状态
shell> echo stat | nc master 2181
```
