`406, 'PRECONDITION_FAILED - unknown delivery tag`
多消费,单生产,速度过快引起的
https://stackoverflow.com/questions/9392478/error-unknown-delivery-tag-occurs-when-i-try-ack-messages-to-rabbitmq-using-pi

需要ack手动确认
```python
def callback(ch, method, properties, body):
    print(body)
    time.sleep(2)
    ch.basic_ack(delivery_tag=method.delivery_tag)
channel.basic_qos(prefetch_count = 3 )
channel.basic_consume(callback,
                      queue=queue_name,
                      no_ack=False)
```