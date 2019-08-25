# JDK Map

## JDK 安装

```shell
# 1. 解压jdk-{version}-linux-x64.tar.gz
shell> mkdir /usr/java
shell> cd /usr/java
shell> tar -zx -f jdk-8u161-linux-x64.tar.gz

# 2. 配置Java环境变量
shell> vi /etc/profile.d/java.sh
	#!/bin/bash
	export JAVA_HOME=/usr/java/jdk1.8.0_221
	export CLASS_PATH=.:${JAVA_HOME}/lib/dt.jar:${JAVA_HOME}/lib/tools.jar
	export PATH=${JAVA_HOME}/bin:${PATH}

# 3. 载入环境
shell> . /etc/profile.d/java.sh
```

## Jar 运行

```shell
# 锁定当前窗口运行(关闭窗口，CTRL + C 中断)
shell> java -jar XXX.jar

# 不锁定窗口运行(&表示后台运行，关闭窗口中断)
shell> java -jar XXX.jar &

# 后台运行，输出重定向(默认输出到 nohup.txt)
shell> nohup java -jar XXX.jar &

# 后台运行，输出重定向到指定文件
shell> nohup java -jar XXX.jar > log.txt 2>&1 &

# 后台运行，输出重定向到回收站
shell> nohup java -jar XXX.jar > /dev/null 2>&1 &
```