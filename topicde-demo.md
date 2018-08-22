发送
```python
import pika
#----------远程连接
credentials = pika.PlainCredentials('admin', 'admin')
connection = pika.BlockingConnection(
    pika.ConnectionParameters(host='192.168.1.205', port=5672, virtual_host='test', credentials=credentials))
channel = connection.channel()
#------------声明一个exchange,exchange的名字和类型
channel.exchange_declare(exchange='topic_logs',
                         exchange_type='topic')
#------------发送消息到exchange
routing_key = "myinfo2"
message = "aaaa"
channel.basic_publish(exchange='topic_logs',
                      routing_key=routing_key,
                      body=message,
                      properties=pika.BasicProperties(
                          delivery_mode=2,  # 队列的消息需要持久化。
                      ))
connection.close()
```

消费
```python
import pika
import sys

credentials = pika.PlainCredentials('admin', 'admin')
connection = pika.BlockingConnection(
    pika.ConnectionParameters(host='192.168.1.205', port=5672, virtual_host='test', credentials=credentials))
channel = connection.channel()
channel.exchange_declare(exchange='topic_logs',
                         exchange_type='topic')
#创建一个名字叫muqueue的队列,用来投递消息
#durable = True队列持久化
result = channel.queue_declare(exclusive=True,queue='myqueue',durable = True)
queue_name = result.method.queue
#队列绑定exchange
channel.queue_bind(exchange='topic_logs',queue=queue_name,routing_key="*")


def callback(ch, method, properties, body):
    print( method.routing_key, body)

channel.basic_consume(callback,
                      queue=queue_name,
                      no_ack=True)

channel.start_consuming()
```