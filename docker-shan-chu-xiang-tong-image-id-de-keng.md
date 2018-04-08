####首先说下镜像,容器,仓库的概念,每次都忘记
#####容器是镜像的实例，先声明（描述）镜像，再创建容器，所以容器可以有多个;
#####镜像是文件, 容器是进程。 容器是基于镜像创建的, 即容器中的进程依赖于镜像中的文件, 这里的文件包括进程运行所需要的可执行文件， 依赖软件， 库文件， 配置文件等等...

![](/assets/WX20180408-104025@2x.png)
删除同一个` IMAGE ID`会报错
`docker rmi hyperledger/fabric-couchdb:latest`删除的时候用`REPOSITORY`:`TAG`


<br>
`docker rmi -f $(docker images --format "{{.Repository}}" |grep "^test_*")`可以批量删除 test_ 开头的镜像
`docker rm $(docker ps --all -q -f status=exited)`删除停止的容器
`docker rmi -f $(docker images | grep "<none>" | awk "{print \$3}")`删除没有使用的镜像