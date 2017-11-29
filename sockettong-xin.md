####socket本质上就是在2台网络互通的电脑之间，架设一个通道，两台电脑通过这个通道来实现数据的互相传递。 我们知道网络 通信 都 是基于 ip+port 方能定位到目标的具体机器上的具体服务，操作系统有0-65535个端口，每个端口都可以独立对外提供服务，如果 把一个公司比做一台电脑 ，那公司的总机号码就相当于ip地址， 每个员工的分机号就相当于端口， 你想找公司某个人，必须 先打电话到总机，然后再转分机 。

 

####建立一个socket必须至少有2端， 一个服务端，一个客户端， 服务端被动等待并接收请求，客户端主动发起请求， 连接建立之后，双方可以互发数据。
####首先建立一个客户端的 py 文件
```
import socket

client = socket.socket()  #声明 socket 类型,生成 socket 连接对象
client.connect(('localhost',6969))


client.send(b'hello worldD')    #注意这里是 bytes
data = client.recv(1024)
client.close()
print ('data:{}'.format(data))
```
#####再建立一个服务端的 python 文件
```
import socket
server = socket.socket()
server.bind(('localhost',6969))#绑定监听端口
print()
server.listen()  #监听
conn,addr = server.accept()  #等待信号
#conn是客户端连过来而在服务端为其生成的一个连接实例
print(conn,addr)
print('开始监听了--------------')


data = conn.recv(1024)
print('recv:',data)
conn.send(data.upper())
server.close()
```


