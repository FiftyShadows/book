####socket本质上就是在2台网络互通的电脑之间，架设一个通道，两台电脑通过这个通道来实现数据的互相传递。 我们知道网络 通信 都 是基于 ip+port 方能定位到目标的具体机器上的具体服务，操作系统有0-65535个端口，每个端口都可以独立对外提供服务，如果 把一个公司比做一台电脑 ，那公司的总机号码就相当于ip地址， 每个员工的分机号就相当于端口， 你想找公司某个人，必须 先打电话到总机，然后再转分机 。

 

####建立一个socket必须至少有2端， 一个服务端，一个客户端， 服务端被动等待并接收请求，客户端主动发起请求， 连接建立之后，双方可以互发数据。
####首先建立一个客户端的 py 文件
```
import socket

client = socket.socket()  #声明 socket 类型,生成 socket 连接对象
client.connect(('localhost',6969))

while True:
    msg = input('>>:').strip()
    if len(msg)==0:
        print('不要输入空')
        continue

    # client.send(b'hello worldD')  # 注意这里是 bytes
    client.send(msg.encode('utf-8')) #中文
    data = client.recv(1024)  #收到的大小,1024字节
    print('data:{}'.format(data))

client.close()
```
#####再建立一个服务端的 python 文件,注意运行的时候先运行服务端,先监听端口
```
import socket
server = socket.socket()
server.bind(('localhost',6969))#绑定监听端口
print()
server.listen()  # 监听
while True:  #客户端断开后服务器能保证不断
    conn,addr = server.accept()  #等待信号
    #conn是客户端连过来而在服务端为其生成的一个连接实例
    print(conn,addr)
    print('开始监听了--------------')

    while True: #可以一直监听
        data = conn.recv(1024)
        if not data:
            print('client is lost...')
            break
        print('recv:',data.decode())
        conn.send(data.upper())
server.close()
```
####因为socket每次接收和发送都有最大数据量限制的，毕竟网络带宽也是有限的呀，不能一次发太多，发送的数据最大量的限制 就是缓冲区能缓存的数据的最大量，这个缓冲区的最大值在不同的系统上是不一样的，官方建议不超过8k(最好是1024整数倍),那么客户端如何完整接收一个大文件呢,服务端代码
```
import socket
import os,subprocess


server = socket.socket() #获得socket实例
server.setsockopt(socket.SOL_SOCKET, socket.SO_REUSEADDR, 1)

server.bind(("localhost",9999)) #绑定ip port
server.listen()  #开始监听

while True: #第一层loop
    print("等待客户端的连接...")
    conn,addr = server.accept() #接受并建立与客户端的连接,程序在此处开始阻塞,只到有客户端连接进来...
    print("新连接:",addr )
    while True:

        data = conn.recv(1024)
        if not data:
            print("客户端断开了...")
            break #这里断开就会再次回到第一次外层的loop
        print("收到命令:",data)
        #res = os.popen(data.decode()).read() #py3 里socket发送的只有bytes,os.popen又只能接受str,所以要decode一下
        res = subprocess.Popen(data,shell=True,stdout=subprocess.PIPE).stdout.read() #跟上面那条命令的效果是一样的
        if len(res) == 0:
            res = "cmd exec success,has not output!".encode("utf-8")
        conn.send(str(len(res)).encode("utf-8")) #发送数据之前,先告诉客户端要发多少数据给它
        print("等待客户ack应答...")
        client_final_ack = conn.recv(1024) #等待客户端响应
        print("客户应答:",client_final_ack.decode())
        print(type(res))
        conn.sendall(res) #发送端也有最大数据量限制,所以这里用sendall,相当于重复循环调用conn.send,直至数据发送完毕

server.close()
```
####客户端代码
```
import socket
import sys

client = socket.socket()

client.connect(("localhost",9999))

while True:
    msg = input(">>:").strip()
    if len(msg) == 0:continue
    client.send( msg.encode("utf-8") )

    res_return_size  = client.recv(1024) #接收这条命令执行结果的大小
    print("getting cmd result , ", res_return_size)
    total_rece_size = int(res_return_size)
    print("total size:",res_return_size)
    client.send("准备好接收了,发吧loser".encode("utf-8"))
    received_size = 0 #已接收到的数据
    cmd_res = b''
    f = open("test_copy.html","wb")#把接收到的结果存下来,一会看看收到的数据 对不对
    while received_size != total_rece_size: #代表还没收完
        data = client.recv(1024)
        received_size += len(data) #为什么不是直接1024,还判断len干嘛,注意,实际收到的data有可能比1024少
        cmd_res += data
    else:
        print("数据收完了",received_size)
        #print(cmd_res.decode())
        f.write(cmd_res) #把接收到的结果存下来,一会看看收到的数据 对不对
    #print(data.decode()) #命令执行结果

client.close()
```
###讲解 SocketServer 讲解,其实也就是 Socket 的封装
服务端代码
```
import socketserver


class MyTCPHandler(socketserver.BaseRequestHandler):

    """
    The request handler class for our server.

    It is instantiated once per connection to the server, and must
    override the handle() method to implement communication to the
    client.
    """


    def handle(self):
        while True:
            # self.request is the TCP socket connected to the client
            data = self.request.recv(1024).strip()
            print("{} wrote:".format(self.client_address[0]))
            print(data)
            # just send back the same data, but upper-cased
            self.request.sendall(data.upper())

if __name__ == "__main__":
    HOST, PORT = "localhost", 9999

    # Create the server, binding to localhost on port 9999
    server = socketserver.TCPServer((HOST, PORT), MyTCPHandler)
        server = socketserver.ForkingTCPServer((HOST, PORT), MyTCPHandler)#多进程连接
    # servefr = socketserver.ThreadingTCPServer((HOST, PORT), MyTCPHandler)#多线程连接
    # Activate the server; this will keep running until you
    # interrupt the program with Ctrl-C
    server.serve_forever()
```
####客户端代码
```
import socket

client = socket.socket()  #声明 socket 类型,生成 socket 连接对象
client.connect(('localhost',9999))

while True:
    msg = input('>>:').strip()
    if len(msg)==0:
        print('不要输入空')
        continue

    # client.send(b'hello worldD')  # 注意这里是 bytes
    client.send(msg.encode('utf-8')) #中文
    data = client.recv(1024)  #收到的大小,1024字节
    print('data:{}'.format(data))

client.close()

```