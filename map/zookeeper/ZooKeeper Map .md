# ZooKeeper Map

------

## Install

```shell
# 1. 解压 zookeeper-{version}.tar.gz
shell> mkdir /opt/zookeeper
shell> cd /opt/zookeeper
shell> tar -zx -f zookeeper-3.4.14.tar.gz

# 2. 复制 zoo_sample.cfg
shell> cp /opt/zookeeper/zookeeper-3.4.14/conf/zoo_sample.cfg /opt/zookeeper/zookeeper-3.4.14/conf/zoo.cfg

# 3. 设置 dataDir 目录，配置"host:通信port:选举port"
shell> vim conf/zoo.cfg
	...
	dataDir=/var/zookeeper
	...
	server.1=192.168.225.128:2888:3888
	server.2=192.168.225.129:2888:3888
	server.3=192.168.225.130:2888:3888
	
# 4. 将 zookeeper 复制到其它主机
shell> scp -r /opt/zookeeper-3.4.12 root@slave1:/opt
shell> scp -r /opt/zookeeper-3.4.12 root@slave2:/opt

# 5. 配置环境变量
shell> vi /etc/profile.d/zookeeper.sh
	#!/bin/bash
	export ZK_HOME=/opt/zookeeper/zookeeper-3.4.14
	export PATH=${PATH}:${ZK_HOME}/bin
shell> source /etc/profile.d/zookeeper.sh

# 6. 添加 myid 文件
shell> mkdir /var/zookeeper
shell> vi /var/zookeeper/myid
	1
	
# 7. 启动 zookeeper
shell> zkServer.sh [start | start-foreground]

# 8. 验证是否启动成功
# 使用 telnet 方式, 安装 telnet
shell> yum install -y telnet
# 连接、打印 zookeeper 状态
shell> telnet master 2181
	stat
# 使用 netcat 方式, 安装 netcat
shell> yum install -y nc
# 打印 zookeeper 状态
shell> echo stat | nc master 2181
```
