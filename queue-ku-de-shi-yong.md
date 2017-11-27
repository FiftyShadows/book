#### 这个库可以用来线程间的交互

#### 简单的生产者消费者队列的实现


##### python queue模块有三种队列:
##### 1、python queue模块的FIFO队列先进先出。
##### 2、LIFO类似于堆。即先进后出。
##### 3、还有一种是优先级队列级别越低越先出来。

##### 针对这三种队列分别有三个构造函数:
##### 1、class Queue.Queue(maxsize) FIFO
##### 2、class Queue.LifoQueue(maxsize) LIFO
##### 3、class Queue.PriorityQueue(maxsize) 优先级队列

##### Queue.qsize() 返回队列的大小
##### Queue.empty() 如果队列为空，返回True,反之False
##### Queue.full() 如果队列满了，返回True,反之False
##### Queue.full 与 maxsize 大小对应
##### Queue.get([block[, timeout]]) 获取队列，timeout等待时间
##### Queue.get_nowait() 相当Queue.get(False)
##### 非阻塞 Queue.put(item) 写入队列，timeout等待时间
##### Queue.put_nowait(item) 相当Queue.put(item, False) 
##### Queue.task_done() 在完成一项工作之后，Queue.task_done() 函数向任务已经完成的队列发送一个信号
##### Queue.join() 实际上意味着等到队列为空，再执行别的操作
```
#-------------------------------------------------------
import threading,time,random
import queue


q = queue.Queue(maxsize=10)
q.put('a')
print(q.get()) #a

#-------------------------------------------------------
# 最基本的一个队列
def producer():
    for i in range(10):
        q.put("骨头 %s" % i)

    print("开始等待所有的骨头被取走...")
    q.join()
    print("所有的骨头被取完了...")


def consumer(n):
    while q.qsize() > 0:
        print("%s 取到" % n, q.get())
        q.task_done()  # 告知这个任务执行完了


q = queue.Queue()

p = threading.Thread(target=producer, )
p.start()

c1 = consumer("沈")
#-------------------------------------------------------
# 边生产边消费
q = queue.Queue()
def Producer(name):
  count = 0
  while count <20:
    time.sleep(random.randrange(3))
    q.put(count)
    print('Producer %s has produced %s baozi..' %(name, count))
    count +=1
def Consumer(name):
  count = 0
  while count <20:
    time.sleep(random.randrange(4))
    if not q.empty():
        data = q.get()
        print(data)
        print('\033[32;1mConsumer %s has eat %s baozi...\033[0m' %(name, data))
    else:
        print("-----no baozi anymore----")
    count +=1
p1 = threading.Thread(target=Producer, args=('A',))
c1 = threading.Thread(target=Consumer, args=('B',))
p1.start()
c1.start()
#-------------------------------------------------------
```