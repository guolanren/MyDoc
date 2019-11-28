# Elasticsearch Map

## killed

​	**bin/elasticsearch** 启动后被 **killed**。原因是内存不足。修改 **es** 的配置文件 **jvm.options** ，降低内存。

## max virtual memory areas vm.max_map_count [65530\] is too low, increase to at least [262144]

​	**bin/elasticsearch** 启动后报以上错误。修改 **/etc/sysctl.conf**，添加 **vm.max_map_count=262144**。重启。

