废话不多,上代码
```python
import gevent

def func1():
    print('\033[31;1m第一次打印fun1的内容.\033[0m')
    gevent.sleep(2)
    print('\033[31;1m第二次打印fun1的内容\033[0m')

def func2():
    print('\033[32;1m第一次打印fun2的内容\033[0m')
    gevent.sleep(1)
    print('\033[32;1m第二次打印fun2的内容\033[0m')


gevent.joinall([
    gevent.spawn(func1),
    gevent.spawn(func2),
])
# 第一次打印fun1的内容.
# 第一次打印fun2的内容
# 第二次打印fun2的内容
# 第二次打印fun1的内容

#可以看到,遇到io阻塞的时候,是自动来回切换的,非常高效

```
####再来一个单线程下并发100个 socket 通信的例子,需要结合我的 socket 通信文章一起阅读.
#####服务端代码
```python
import sys
import socket
import time
import gevent

from gevent import socket, monkey

monkey.patch_all()#碰到可能 io 阻塞的记得加这句,俗称的猴子补丁

def server(port):
    s = socket.socket()
    s.bind(('0.0.0.0', port))
    s.listen(500)
    while True:
        cli, addr = s.accept()
        gevent.spawn(handle_request, cli)


def handle_request(conn):
    try:
        while True:
            data = conn.recv(1024)
            print("recv:", data)
            conn.send(data)
            if not data:
                conn.shutdown(socket.SHUT_WR)

    except Exception as  ex:
        print(ex)
    finally:
        conn.close()


if __name__ == '__main__':
    server(8001)
```
####客户端,同时并发100个连接
```python
import socket
import threading

def sock_conn():

    client = socket.socket()

    client.connect(("localhost",8001))
    count = 0
    while True:
        #msg = input(">>:").strip()
        #if len(msg) == 0:continue
        client.send( ("hello %s" %count).encode("utf-8"))

        data = client.recv(1024)

        print("[%s]recv from server:" % threading.get_ident(),data.decode()) #结果
        count +=1
    client.close()


for i in range(100):
    t = threading.Thread(target=sock_conn)
    t.start()
```