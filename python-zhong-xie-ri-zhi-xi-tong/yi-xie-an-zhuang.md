https://www.cnblogs.com/shifu204/p/6370682.html

es启动要求提高一些系统参数配置，否则会报错
a. 增大vm.max_map_count到至少262144
```
sudo vim  /etc/sysctl.conf
添加  vm.max_map_count=262144
sudo sysctl -p
```
增大文件句柄数至少 65536  ulimit -a查看
```
sudo vim /etc/security/limits.conf
* soft nofile 65536
* hard nofile 65536

```