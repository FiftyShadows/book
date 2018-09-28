依旧是峰云的博客解决了我的问题
```python
import gevent
from gevent.pool import Pool
from gevent import monkey, sleep
monkey.patch_all()

def report_task(task):
    jobs = []
    for i in range(10):
        jobs.append(gevent.spawn(krun, task))
    gevent.joinall(jobs)

def krun(task):
    gevent.sleep(task)

if __name__ == "__main__":
    plist = []
    pool = multiprocessing.Pool(processes=4)
    for i in range(0,2):
        pool.apply_async(report_task, (i, ))

    pool.close()
    pool.join()
```
问题是出在 multiprocessing pool上。解决方案
1. 用什么patch，就引入啥patch。
2. monkey.patch_all(thread=False) 
3. 调整下import gevent位置，进程fork之后再惰性加载gevent相关方法，这样不污染主线程的环境。
