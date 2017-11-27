### 先看知乎上的回答:[线程和进程的区别是什么?](https://www.zhihu.com/question/25532384)

#### 线程\(threads\)是操作系统能进行运算调度的最小单位,被包含在进程中,是进程\(processes\)的实际运作单位.一条线程指的是进程中一个单一顺序的控制流买一个进程可以并发多个线程,每个线程执行不同的任务.

#### 进程是资源的集合,线程是真正执行任务的. 一个进程下的线程共享同一块内存空间,可以直接交流,不同的进程占据不同的内存空间,进程之间的通信 要经过一个中间的代理

#### 启动一个线程快于启动进程.c

#### 一个线程可以控制同一个进程里的其他线程,进程只能操作自身的子进程.

#### 关于线程,尤其是python的线程,一般都是threading库的使用,可以看我之前的文章:[threading库的使用](https://qq976739120.gitbooks.io/book/content/threadingku-de-shi-yong.html)

#### 关于进程,尤其是python的进程,可以看我之前的文章:[multiprocessing库的使用](https://qq976739120.gitbooks.io/book/content/multiprocessing-ku-de-shi-yong.html)
