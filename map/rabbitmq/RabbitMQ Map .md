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
# 如遇到 "No curses library functions found"
shell> yum install -y ncurses-devel
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

# 5. 配置 RabbitMQ 环境变量
shell> vi /etc/profile.d/rabbitmq.sh
	#!/bin/bash
	export RABBITMQ_HOME=/opt/rabbitmq/rabbitmq_server-3.8.2
	export PATH=${PATH}:${RABBITMQ_HOME}/sbin
	
# 6. 载入环境
shell> source /etc/profile.d/rabbitmq.sh
```
