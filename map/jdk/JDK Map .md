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
