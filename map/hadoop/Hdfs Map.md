# HDFS Map

------

## FileSystem Shell

```shell
# 可直接与Hadoop分布式文件系统（HDFS）以及Hadoop支持的其他文件系统（如本地文件系统，WebHDFS，S3 FS等）交互
Usage: hadoop fs <args>(hdfs dfs <args>)
```

### appendToFile

```shell
# 将本地文将内容追加到指定文件系统文件中
Usage: hadoop fs -appendToFile <localsrc> ... <dst>

# 将本地文件系统文件内容添加到指定文件系统文件内容末端
shell> hdfs dfs -appendToFile a.txt b.txt text.txt

# 将stdin内容添加到指定文件系统文件内容末端
shell> hdfs dfs -appendToFile - text.txt
```

### cat

```shell
# -ignoreCrc选项禁用checkshum验证
Usage: hadoop fs -cat [-ignoreCrc] URI [URI ...]

# 将指定文件系统文件内容输出stdout
shell> hdfs dfs -cat tetx.txt
```

### checksum

```shell
# 返回文件的检验和信息
Usage: hadoop fs -checksum URI
```

### chgrp

```shell
# 用户必须是文件的所有者
# -R:选项将通过目录结构递归地进行更改
Usage: hadoop fs -chgrp [-R] GROUP URI [URI ...]

# 更改文件用户组
shell> hdfs dfs -chgrp mygroup text.txt

# 递归更改文件夹内所有文件的用户组
shell> hdfs dfs -chgrp -R mygroup hadoop
```

### chmod

```shell
# 用户必须是文件的所有者
# -R:选项将通过目录结构递归地进行更改
Usage: hadoop fs -chmod [-R] <MODE[,MODE]... | OCTALMODE> URI [URI ...]

# 更改文件权限
shell> hdfs dfs -chmod 777 text.txt

# 递归更改文件夹内所有文件的权限
shell> hdfs dfs -chmod -R +w hadoop
```

### chown

```shell
# 用户必须是超级用户
# -R:选项将通过目录结构递归地进行更改
Usage: hadoop fs -chown [-R] [OWNER][:[GROUP]] URI [URI]

# 更改文件所有者
shell> hdfs dfs -chown guoyaozhan text.txt

# 递归更改文件夹内所有文件所有者和用户组
shell> hdfs dfs -chmod -R guoyaozhan:supergroup hadoop
```

### copyFromLocal

```shell
# 与-put命令相似，只是限制为本地文件
# -p:保留文件属性
# -f:覆盖目标，如果它已经存在
# -l:允许DataNode延迟保存文件到磁盘
# -d:跳过后缀为._COPYING_临时文件的创建
Usage: hadoop fs -copyFromLocal <localsrc> URI

# 将指定本地文件复制到文件系统中
shell> hdfs dfs -copyFromLocal text.txt .
```

### copyToLocal

```shell
# 与-get命令相似，只是限制为本地文件
Usage: hadoop fs -copyToLocal [-ignorecrc] [-crc] URI <localdst>

# 将指定文件系统文件复制到本地
shell> hdfs dfs -copyToLocal text.txt .
```

### count

```shell
# 获取配额和使用情况，输出列为：DIR_COUNT,FILE_COUNT,CONTENT_SIZE,PATHNAME
# -q:输出列为:QUOTA, REMAINING_QUOTA, SPACE_QUOTA, REMAINING_SPACE_QUOTA, DIR_COUNT, FILE_COUNT, CONTENT_SIZE, PATHNAME
# -u:输出列为:QUOTA, REMAINING_QUOTA, SPACE_QUOTA, REMAINING_SPACE_QUOTA, PATHNAME
# -t:显示每种存储类型的配额和使用情况。如果没有给出-u或-q选项，-t选项将被忽略。可在-t选项中使用的参数列表："","all","ram_disk","ssd","disk","archive"
# -h:格式化size
# -v:输出标题行
# -x:排除结果计算中的快照。如果没有-x选项（默认），结果将始终从所有INode计算得出，包括给定路径下的所有快照。如果给出-u或-q选项，则忽略-x选项
# -e:显示每个文件的擦除编码策略
Usage: hadoop fs -count [-q] [-h] [-v] [-x] [-t [<storage type>]] [-u] [-e] <paths>
```

### cp

```shell
# 将指定文件复制到指定目录
# -f:覆盖目标，如果它已经存在
# -p:保留文件属性
Usage: hadoop fs -cp [-f] [-p | -p[topax]] URI [URI ...] <dest>
```

### createSnapshot

```shell

```

### deleteSnapshot

```shell

```

### df

```shell
# 显示指定路径的空间使用情况
# -h:格式化size
Usage: hadoop fs -df [-h] URI [URI ...]
```

### du

```shell
# 显示指定文件或路径下的文件的空间使用情况
# -s:如果目标是目录，显示该目录下所有文件的汇总信息
# -h:格式化size
# -v:输出标题行
# -x:排除结果计算中的快照。如果没有-x选项（默认），结果将始终从所有INode计算得出，包括给定路径下的所有快照。如果给出-u或-q选项，则忽略-x选项
Usage: hadoop fs -du [-s] [-h] [-v] [-x] URI [URI ...]
```

### dus

```shell
# hdfs dfs -du -s
Usage: hadoop fs -dus <args>
```

### expunge

```shell
# 永久删除文件
Usage: hadoop fs -expunge
```

### find

```shell
# 查找文件
# -name pattern:区分大小写
# -iname pattern:不区分大小写
# -print:输出
# -print0:追加ASCII NULL
# expression -a expression
# expression -and expression
# expression expression
Usage: hadoop fs -find <path> ... <expression> ...
```

### get

```shell
# 将指定文件复制到本地目录
# -f:覆盖目标，如果它已经存在
# -p:保留文件属性
# -ignorecrc:跳过对下载的文件进行CRC校验
# -crc:为下载的文件写入CRC校验和
Usage: hadoop fs -get [-ignorecrc] [-crc] [-p] [-f] <src> <localdst>
```

### getfacl

```shell
# 显示文件和目录的访问控制列表（ACL）
# -R:递归列出所有文件和目录的ACL
Usage: hadoop fs -getfacl [-R] <path>
```

### getfattr

```shell
# 显示文件或目录的扩展属性名称和值
# -R:递归列出所有文件和目录的属性
# -n name:转储指定的扩展属性值
# -d:转储与路径名关联的所有扩展属性值
# -e:编码
Usage: hadoop fs -getfattr [-R] -n name | -d [-e en] <path>
```

### getmerge

```shell
# 将指定文件拼接成一个输出至本地文件系统
# -nl:每个文件末端添加换行符（LF）
Usage: hadoop fs -getmerge [-nl] <src> <localdst>
```

### help

```shell
# 查看帮助文档
Usage: hadoop fs -help
```

### ls

```shell
# 列出指定目录、文件
# -C:仅显示文件和目录的路径
# -d:目录被列为纯文件
# -h:格式化size
# -q：打印？而不是不可打印的字符。
# -R：递归列出遇到的子目录。
# -t：按修改时间排序输出
# -S：按文件大小排序输出
# -r：反转排序顺序
# -u：使用访问时间排序输出
# -e：仅显示文件和目录的删除编码策略
Usage: hadoop fs -ls [-C] [-d] [-h] [-q] [-R] [-t] [-S] [-r] [-u] [-e] <args>
```

### lsr

```shell
# 已废弃，取而代之hadoop fs -ls -R
Usage: hadoop fs -lsr <args>
```

### mkdir

```shell
# 创建目录
# -p:父目录不存在，创建
Usage: hadoop fs -mkdir [-p] <paths>
```

### moveFromLocal

```shell
# 将本地文件移动至指定目录
Usage: hadoop fs -moveFromLocal <localsrc> <dst>
```

### moveToLocal

```shell
# 将指定文件移动至本地目录
Usage: hadoop fs -moveToLocal [-crc] <src> <dst>
```

### mv

```shell
# 将指定文件移动至指定目的地，不允许跨文件系统
Usage: hadoop fs -mv URI [URI ...] <dest>
```

###  put

```shell
# 将指定本地文件或stdin复制到指定目录
# -f:覆盖目标，如果它已经存在
# -p:保留文件属性
# -l:允许DataNode延迟保存文件到磁盘
# -d:跳过后缀为._COPYING_临时文件的创建
Usage: hadoop fs -put [-f] [-p] [-l] [-d] [ - | <localsrc1> .. ]. <dst>
```

###  renameSnapshot

```shell

```

###  rm

```shell
# 删除指定文件
# -f:如果文件不存在，不显示错误信息
# -r:递归删除目录及其下的任何内容
# -R:-r
# -skipTrash:绕过回收站，完全删除
# -safely:如果删除的文件数大于设定值，显示提示信息
Usage: hadoop fs -rm [-f] [-r |-R] [-skipTrash] [-safely] URI [URI ...]
```

###  rmdir

```shell
# 删除目录
# --ignore-fail-on-non-empty:使用通配符时，如果目录仍包含文件，删除仍然不会失败
Usage: hadoop fs -rmdir [--ignore-fail-on-non-empty] URI [URI ...]
```

###  rmr

```shell
# 已废弃，取而代之hadoop fs -rm -r
Usage: hadoop fs -rmr [-skipTrash] URI [URI ...]
```

###  setfacl

```shell
# 设置文件和目录的访问控制列表（ACL）
# -b:删除除基本ACL条目以外的所有条目。用户，组和其他条目保留以与许可位兼容
# -k:删除默认ACL
# -R:递归地将操作应用于所有文件和目录
# -m:修改ACL。新条目添加到ACL中，并保留现有条目
# -x:删除指定的ACL条目。其他ACL条目被保留
# --set:完全替换ACL，丢弃所有现有条目。所述acl_spec必须包括用户，组条目和其他用于与权限位兼容性
Usage: hadoop fs -setfacl [-R] [-b |-k -m |-x <acl_spec> <path>] |[--set <acl_spec> <path>]
```

###  setfattr

```shell
# 设置文件或目录的扩展属性名称和值
# -n名称:扩展属性名称
# -v value:扩展属性值。该值有三种不同的编码方法。如果参数用双引号引起来，那么值就是引号内的字符串。如果参数以0x或0X为前缀，则将其视为十六进制数字。如果参数以0或0S开始，那么它被视为base64编码
# -x name:删除扩展属性
Usage: hadoop fs -setfattr -n name [-v value] | -x name <path>
```

###  setrep

```shell
# 更改文件的复制因子。如果path是一个目录，那么该命令将递归地更改根目录为path的目录树下的所有文件的复制因子。执行此命令时EC文件将被忽略
# -w:请求该命令等待复制完成，可能需要很长时间
# -R:被接受用于向后兼容，它不起作用
Usage: hadoop fs -setrep [-R] [-w] <numReplicas> <path>
```

###  stat

```shell
# 以指定的格式打印指定文件或目录的统计信息。
# format：
# 八进制（％a）
# 符号（％A）
# 文件大小字节（％b）
# 类型（％F）
# 所有者组名称（％g）
# 名称（％n）
# 块大小（％o）
# 复制（％r）
# 所有者用户名（％u）
# 访问日期（％x，％X）
# 修改日期（％y，％Y）
# ％x和％y将UTC日期显示为“yyyy-MM-dd HH：mm：ss”，％X和％Y显示1970年1月1日以来的毫秒。如果未指定格式，则默认使用％y
Usage: hadoop fs -stat [format] <path> ...

shell> hadoop fs -stat "type:%F perm:%a %u:%g size:%b mtime:%y atime:%x name:%n" /user
```

### tail

```shell
# 将文件的最后一个千字节显示到stdout
# -f:文件增长，追加输出
Usage: hadoop fs -tail [-f] URI
```

###  test

```shell
# 测试
# -d:f路径是一个目录，返回0
# -e:如果路径存在，则返回0
# -f:如果路径是文件，则返回0
# -s:如果路径不为空，则返回0
# -r:如果路径存在并且授予读取权限，则返回0
# -w:如果路径存在并且写入权限被授予，则返回0
# -z:如果文件长度为零，则返回0
Usage: hadoop fs -test -[defsz] URI
```

### text

```shell
# 采用文本格式输出指定文件，允许的格式是zip和TextRecordInputStream
Usage: hadoop fs -text <src>
```

###  touchz

```shell
# 创建一个零长度的文件
Usage: hadoop fs -touchz URI [URI ...]
```

###  truncate

```shell
# 将指定文件截断为指定的长度
# -w:请求命令等待块恢复完成
# 非-w:可能在恢复过程中一段时间内保持未关闭状态，在此期间文件不能重新打开以便追加
Usage: hadoop fs -truncate [-w] <length> <paths>
```

###  usage

```shell
# 返回单个命令的帮助文档
Usage: hadoop fs -usage command
```