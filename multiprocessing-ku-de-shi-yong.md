####multiprocessing包是Python中的多进程管理包。它与 threading.Thread类似，可以利用multiprocessing.Process对象来创建一个进程。该进程可以允许放在Python程序内部编写的函数中。该Process对象与Thread对象的用法相同，拥有is_alive()、join([timeout])、run()、start()、terminate()等方法。属性有：authkey、daemon（要通过start()设置）、exitcode(进程在运行时为None、如果为–N，表示被信号N结束）、name、pid。此外multiprocessing包中也有Lock/Event/Semaphore/Condition类，用来同步进程，其用法也与threading包中的同名类一样。multiprocessing的很大一部份与threading使用同一套API，只不过换到了多进程的情境。
####这个模块表示像线程一样管理进程，这个是multiprocessing的核心，它与threading很相似，对多核CPU的利用率会比threading好的多。

####注意进程池的回调函数的使用,回调函数是在主进程里的,不用再多开进程,能节省不小的开销.



####以下代码实例
```
import multiprocessing, time
import threading, os


# def run(name):#开进程
# 	time.sleep(1)
# 	print('hello-{}'.format(name))

# def thread_run():  # 线程任务
# 	print(threading.get_ident())  # 打印当前线程id
#
#
# def run(name):  # 在进程里开线程
# 	time.sleep(1)
# 	print('hello-{}'.format(name))
# 	t = threading.Thread(target=thread_run)
# 	t.start()
#
#
# if __name__ == '__main__':
# ---------------------------------------------------------------------
# 	# 启动多进程
# 	for i in range(10):
# 		p = multiprocessing.Process(target=run, args=(i,))
# 		p.start()


# ---------------------------------------------------------------------
# windows下我不是很熟,但是linux下,每一个进程必然是其父进程启动的
def info(title):
	print(title)
	print('module name:', __name__)
	print('parent process:', os.getppid())
	print('process id:', os.getpid())
	print("\n\n")


def f(name):
	info('\033[31;1 call from function f\033[0m')#调用了info(),相当于子进程里调用info()
	print('hello', name)


if __name__ == '__main__':
	info('\033[32;1mmain process line\033[0m')#父进程的info()
	p =multiprocessing.Process(target=f, args=('bob',))#子进程的info()
	p.start()
	p.join()
	# main process line
	# module name: __main__
	# parent process: 12084
	# process id: 13492
	#
	#
	#
	# [31;1 call from function f[0m
	# module name: __mp_main__
	# parent process: 13492 #可以看到这里的父进程id就是上面的子进程id
	# process id: 16124
	#
	#
	#
	# hello bob
# ---------------------------------------------------------------------
```
###进程之间是无法互相访问的,要访问必须通过中间人,这个中间人跟queue非常的像,只不过在 multiprocessing 库里引用,这里跟线程的queue结合看效果比较好,我也是试了好久才明白的
```
import multiprocessing
import threading
import queue

#线程间通讯
#
# def f():
# 	q.put(['shen', None,1995])
#
# if __name__ == '__main__':
# 	q = queue.Queue()
# 	p = threading.Thread(target=f)
# 	p.start()
# 	print(q.get())  # prints "[42, None, 'hello']"
# 	p.join()
# ---------------------------------------------------------------------
#进程间通讯
def f(q):#传入了q
    q.put(['shen', None,1995])

if __name__ == '__main__':
    q = multiprocessing.Queue()#父进程的q
    p = multiprocessing.Process(target=f, args=(q,))#又开了一个子进程,无法直接访问q,所以把q当参数传进去.
    p.start()
    print(q.get())    # prints "[42, None, 'hello']"
    p.join()
# ---------------------------------------------------------------------
# 进程间的另一种好用的通信方式Pipe
# def f(conn):
# 	conn.send('son to father message')
# 	print(conn.recv())
# 	conn.close()
#
# if __name__ == '__main__':
# 	parent_coon,son_coon   = multiprocessing.Pipe()
# 	p = multiprocessing.Process(target=f,args=(son_coon,))
# 	p.start()
# 	print(parent_coon.recv())#父进程收到消息
# 	parent_coon.send('father to son message')#父进程发送消息
# ---------------------------------------------------------------------
# 进程间的数据互相修改
# def f(d, l):
#     d[1] = '1'
#     d['2'] = 2
#     d[0.25] = None
#     l.append(os.getpid())
#     print(l)
#
# if __name__ == '__main__':
#     with multiprocessing.Manager() as manager:
#         d = manager.dict() #生成一个字典,可以在多个进程间共享
#
#         l = manager.list(range(5))#生成一个字列表,可以在多个进程间共享
#         p_list = []
#         for i in range(10):
#             p = multiprocessing.Process(target=f, args=(d, l))
#             p.start()
#             p_list.append(p)
#
#         for res in p_list:#等待结果
#             res.join()
#
#         print(d)
#         print(l)
# ---------------------------------------------------------------------
# 进程锁的使用
# 这个锁挺无聊的,一般是为了防止打印打印在屏幕上出现问题,因为进程相互独立,但是打印的时候共享同一块屏幕
# def f(l, i):
#     l.acquire()
#     try:
#         print('hello world', i)
#     finally:
#         l.release()
#
# if __name__ == '__main__':
#     lock = multiprocessing.Lock()
#
#     for num in range(100):
#        multiprocessing.Process(target=f, args=(lock, num)).start()
# ---------------------------------------------------------------------
# 进程池的使用
# apply        #同步执行,串行
# apply_async  #异步执行,并行
def foo(i):
	time.sleep(2)
	print('in progrress', os.getpid())
	return i + 100


def bar(arg):
	print('-->exec done:', arg,os.getpid())

if __name__ == '__main__':
	print('主进程{}'.format(os.getpid()))
	for i in range(10):
		pool = multiprocessing.Pool(5)  # 允许进程池里放入5个进程
		# pool.apply(func=foo,args=(i,))   #这里每隔两秒打印了一个,变成了串行
		# pool.apply_async(func=foo,args=(i,))   #异步执行
		pool.apply_async(func=foo, args=(i,), callback=bar)  #回调函数,执行完foo之后执行bar,这个很实用,比如处理完之后自动加一个日志,而且回调函数是在主进程里
	pool.close()
	pool.join()

```
