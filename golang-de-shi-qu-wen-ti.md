鉴于 golang 的可执行文件只是依赖 glibc，打包时选择了官方busybox作为基础镜像，好处就是小！小到连时区的支持都缩减到只有 UTC 一个，然后程序执行的时候就悲剧了。找到了最简单的一个解决办法是，设置环境变量TZ，设为CST-8表示中国标准时间，设为JST-9表示日本标准时间。这个对于 date 命令很好使，但是对于编译好的 golang 程序顶个球用。只能选择另外一个办法，换一个带时区支持的 prom/busybox 镜像，然后在Dockerfile中增加一个命令
```
RUN mv /etc/localtime /etc/localtime.bak && \
    ln -s /usr/share/zoneinfo/Asia/Shanghai /etc/localtime
```