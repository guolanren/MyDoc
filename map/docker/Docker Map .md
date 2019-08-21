# Docker Map

## yum安装

```shell
# 1. 进入 /etc/yum.repos.d 目录
shell> cd /etc/yum.repos.d

# 2. 下载 rpm
shell> wget https://repo.mysql.com//mysql80-community-release-el7-3.noarch.rpm

# 3. 解压 rpm
shell> rpm -ivh mysql80-community-release-el7-3.noarch.rpm --force --nodeps

# 4. 设置需要安装的版本 enabled=1，其他版本 enabled=0
shell> vi mysql-community.repo

# 5. 安装
shell> yum install -y mysql-community-server

# 6. 设置开机自启
shell> systemctl enable mysqld
```
