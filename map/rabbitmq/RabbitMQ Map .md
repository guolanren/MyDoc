# RabbitMQ Map

------

## Install

```shell
# 1. 解压 otp_src_{version}.tar.gz
shell> mkdir /opt/erlang
shell> cd /opt/erlang
shell> tar -zx -f otp_src_{22.1}.tar.gz

# 2. 编译安装
shell> ./configure --prefix=/opt/erlang
shell> make && make install

# 3. 配置 Erlang 环境变量
shell> vi /etc/profile.d/erlang.sh
	#!/bin/bash
	export ERLANG_HOME=/opt/erlang
	export PATH=${PATH}:${ERLANG_HOME}/bin

# 4. 解压 rabbitmq-server-generic-unix-3.8.2.tar.xz
shell> mkdir /opt/rabbitmq
shell> cd /opt/rabbitmq
shell> xz -d rabbitmq-server-generic-unix-3.8.2.tar.xz
shell> tar -x -f rabbitmq-server-generic-unix-3.8.2.tar
shell> ln -s rabbitmq_server-3.8.2 rabbitmq

# 5. 配置 RabbitMQ 环境变量
shell> vi /etc/profile.d/rabbitmq.sh
	#!/bin/bash
	export RABBITMQ_HOME=/opt/rabbitmq/rabbitmq
	export PATH=${PATH}:${RABBITMQ_HOME}/sbin
	
# 6. 载入环境
shell> source /etc/profile.d/rabbitmq.sh
```

------

## Ctl

```shell
# 节点 #
#
# 启动节点
shell> rabbitmqctl start_app
# 关闭节点
shell> rabbitmqctl stop_app
# 节点状态
shell> rabbitmqctl status

# 用户 #
#
# 添加用户
shell> rabbitmqctl add_user username password
# 列出用户
shell> rabbitmqctl list_users
# 删除用户
shell> rabbitmqctl delete_user username
# 修改密码
shell> rabbitmqctl change_password username newpassword
# 设置角色（administractor）
shell> rabbitmqctl set_user_tags username tag

# 权限 #
#
# 设置权限
shell> rabbitmqctl set_permissions -p vhostpath username ".*" ".*" ".*"
# 列出权限
shell> rabbitmqctl list_user_permissions username
# 清除权限
shell> rabbitmqctl clier_permissions -p vhostpath username

# 虚拟主机 #
#
# 创建虚拟主机
shell> rabbitmqctl add_vhost vhostpath
# 列出虚拟主机
shell> rabbitmqctl list_vhosts
# 查看虚拟主机权限
shell> rabbitmqctl list_permissions -p vhostpath
# 删除虚拟主机
shell> rabbitmqctl delete_vhost vhostpath

# 队列 #
#
# 查看队列
shell> rabbitmqctl list_queues
# 清除队列消息
shell> rabbitmqctl -p vhostpath purge_queue queuename

# 集群 #
#
# 集群状态
shell> rabbitmqctl cluster_status
# 组成集群
shell> rabbitmqctl join_cluster <clusternode> [--ram|--disc]
# 修改集群存储形式
shell> rabbitmqctl change_cluster_node_type [ram|disc]
# 摘除节点
shell> rabbitmqctl forget_cluster_node [--offline]
# 修改节点名称
shell> rabbitmqctl rename_cluster_node oldnode1 newnode1 [oldnode2 newnode2 ...]

# 其他 #
#
# 移除所有数据（一般在 rabbitmqctl stop_app 之后使用）
shell> rabbitmqctl reset
```



