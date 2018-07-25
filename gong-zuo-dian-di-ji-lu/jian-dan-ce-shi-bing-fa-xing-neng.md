https://blog.csdn.net/wx19900503/article/details/56847264
https://blog.csdn.net/yhlas/article/details/80591571
ab是一种用于测试Apache超文本传输协议（HTTP）服务器的工具。apache自带ab工具，可以测试
apache、IIs、tomcat、nginx等服务器
但是ab没有Jmeter、Loadrunner那样有各种场景设计、各种图形报告和监控，只需一个命令即可，有输出描述
可以简单的进行一些压力测试
####Mac下自带apache
查看版本 apachectl -v

ab命令同时满足http及https的请求
常用请求参数：-n请求次数，-c并发数

ab -n 300  -c 300 + url
