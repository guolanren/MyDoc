# MySQL Map

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

## tar安装

```shell
# 1. 添加用户组
shell> groupadd mysql

# 2. 添加用户
shell> useradd -r -g mysql mysql

# 3. 进入 /usr/local
shell> cd /usr/local

# 4. 解压 tar 包
shell> tar -xv -f mysql-5.7.27-linux-glibc2.12-x86_64.tar

# 5. 解压 tar.gz 包
shell> tar -zxv -f mysql-5.7.27-linux-glibc2.12-x86_64.tar.gz

# 6. 建立软链
shell> ln -s /usr/local/mysql-5.7.27-linux-glibc2.12-x86_64/ mysql

# 7. 进入目录
shell> cd mysql

# 8. 创建 mysql-files 目录
shell> mkdir mysql-files

# 9. 设置 mysql-files 目录权限
shell> chmod 770 mysql-files

# 10. 设置所有文件拥有者为 mysql
shell> chown -R mysql .

# 11. 设置所有文件用户组为 mysql
shell> chgrp -R mysql .

# 12. 初始化 mysqld
shell> bin/mysqld --initialize --user=mysql

# 13. 安全套接字层设置
shell> bin/mysql_ssl_rsa_setup

# 14. 设置所有文件拥有者为 root
shell> chown -R root .

# 15. 设置 data mysql-files 文件拥有者为 mysql
shell> chown -R mysql data mysql-files

# 16. 安全启动 mysql
shell> bin/mysqld_safe --user=mysql &

# 17. 设置 mysql 服务开机自启
shell> cp support-files/mysql.server /etc/init.d/mysql.server
shell> chkconfig --add mysql.server

# 18. 重置密码
mysql> set password = 'newpassword';
```

## 初始密码（重置密码）

```shell
# 1. 修改 /etc/my.cnf
shell> vi /etc/my.cnd
	[mysqld]
	# 添加此行，跳过验证
	skip-grant-tables
	
# 2. 修改配置文件需重启
shell> systemctl restart mariadb

# 3.免密连接mysql服务
shell> mysql -r oot -p
## 5.7 之前
mysql> update user set password=password('newpassword') where user='root';
## 5.7 之后
mysql> update mysql.user set authentication_string=password('newpassword') where user='root' and Host = 'localhost';
mysql> exit

# 4. 注释，恢复原设置
shell> vi /etc/my.cnd
	[mysqld]
	# skip-grant-tables

# 5. 修改配置文件需重启
shell> systemctl restart mariadb
```

## 创建用户（授权用户）

```mysql
# 创建用户
CREATE USER `guolanren`@`%` IDENTIFIED BY 'password';

# 创建数据库
CREATE DATABASE `db` CHARACTER SET 'utf8mb4' COLLATE 'utf8mb4_general_ci';

# 授权用户
GRANT ALL ON `db`.* TO `guolanren`@`%`;
```

