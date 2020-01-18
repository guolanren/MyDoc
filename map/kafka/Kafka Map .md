# Kafka Map

------

## Install

```shell
# 1. 解压 kafka_{version}.tar.gz
shell> mkdir /opt/kafka
shell> cd /opt/kafka
shell> tar -zx -f kafka_2.13-2.4.0.tgz

# 2. 修改配置文件
shell> vi /opt/kafka/kafka_2.13-2.4.0/config/server.properties
	broker.id=0|1|2
	...
	listeners=PLAINTEXT://192.168.225.128|129|130:9092
	...
	log.dirs=/var/kafka/logs
	...
	zookeeper.connect = 192.168.225.128:2181,192.168.225.129:2181,192.168.225.130:2181
	
# 3. 复制到其它主机
shell> scp -r /opt/kafka root@slave1:/opt
shell> scp -r /opt/kafka root@slave2:/opt
	
# 4. 创建 log 目录
shell> mkdir -p /var/kafka/logs

# 5. 配置 RabbitMQ 环境变量
shell> vi /etc/profile.d/kafka.sh
	#!/bin/bash
	export KAFKA_HOME=/opt/kafka/kafka_2.13-2.4.0
	export PATH=${PATH}:${KAFKA_HOME}/bin
	
# 6. 载入环境
shell> source /etc/profile.d/kafka.sh

# 7. 启动
shell> kafka-server-start.sh -daemon /opt/kafka/kafka_2.13-2.4.0/config/server.properties
```
