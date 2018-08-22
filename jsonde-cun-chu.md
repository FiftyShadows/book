需要序列化和反序列化
发
```python
message = json.dumps({1:1})
channel.basic_publish(exchange='topic_logs',
                      routing_key=routing_key,
                      body=message,
                      properties=pika.BasicProperties(
                          delivery_mode=2,  # 队列消息持久化
                      )
                      )
 ```
 取
 ```python
 def callback(ch, method, properties, body):
    print(json.loads(body.decode('utf-8')))
    time.sleep(2)
    ch.basic_ack(delivery_tag=method.delivery_tag)
channel.basic_qos(prefetch_count = 3 )
channel.basic_consume(callback,
                      queue=queue_name,
                      no_ack=False)
 ```