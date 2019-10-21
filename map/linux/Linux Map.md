# Linux Map

------

## 添加 sudo 用户

```shell
# 新增用户
shell> useradd username

# 设置密码
shell> passwd password

# 编辑 /etc/sudoers
shell> vim /etc/sudoers
	...
	## Allow root to run any commands anywhere
	root		ALL=(ALL)	ALL
	guolanren	ALL=(ALL)	ALL
	...
```

------

## Installing Spring Boot Applications

1. **maven** 的 插件配置

   ```xml
   <plugin>
   	<groupId>org.springframework.boot</groupId>
   	<artifactId>spring-boot-maven-plugin</artifactId>
   	<configuration>
   		<executable>true</executable>
   	</configuration>
   </plugin>
   ```

2. 编写 **myapp.service** 文件，存放到 **/etc/systemd/system**

   ```service
   [Unit]
   Description=myapp
   After=syslog.target
   
   [Service]
   User=myapp
   ExecStart=/usr/bin/java -jar /var/myapp/myapp.jar
   ExecReload=/bin/kill -s HUP $MAINPID
   ExecStop=ps -ef | grep myapp | grep -v grep awk '{print $2}' | xargs kill -9
   
   [Install]
   WantedBy=multi-user.target
   ```

3. 管理命令

   ```shell
   # 开机自启
   shell> systemctl enable myapp.service
   # 关闭开机自启
   shell> systemctl disable myapp.service
   # 启动
   shell> systemctl start myapp.service
   # 停止
   shell> systemctl stop myapp.service
   # 重启
   shell> systemctl restart myapp.service
   # 查看日志
shell> journalctl -u myapp.service
   ```
   
   