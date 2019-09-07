# Docker Map

------

## CentOS 安装

```shell
# 1. 检查 linux 内核版本
shell> uname -a
# 2. 检查、安装 Device Mapper 依赖
shell> ls -l /sys/class/misc/device-mapper
shell> grep device-mapper /proc/devices
shell> yum install -y device-mapper
shell> yum install -y yum-utils device-mapper-persistent-data lvm2
# 3. 安装 repository
shell> yum-config-manager --add-repo https://download.docker.com/linux/centos/docker-ce.repo
# 4. 安装最新版本 docker engine
shell> yum install docker-ce docker-ce-cli containerd.io
# 5. 启动 docker
shell> systemctl start docker
# 6. 验证 docker 安装成功
shell> docker run hello-world
```
