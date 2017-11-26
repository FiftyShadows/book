###直接开始代码实例
```
# -*- coding: UTF-8 -*-
import threading
import time

def run(task_name):
	print('this is {}'.format(task_name), threading.current_thread())
	time.sleep(3)



# # 多线程
t1 = threading.Thread(target=run, args=('task_one',))
t2 = threading.Thread(target=run, args=('task_two',))


t1.start()
t2.start()
print('done')

# 这里几乎同时打印出了
# this is task_one
# this is task_two
# done
# sleep3秒之后程序结束,这是因为子线程之间没有主仆关系,而且主线程也不会等待子线程的运行结果,也就是并行的
# 那我如何做才能让等待完t1的运行结果再运行t2,再打印done呢?也就是串行运行,答案是join()
t1.start()
t1.join()
t2.start()
t2.join()
print('done')

# -------------------------------------------------------------------------------------

# 这是快速启动多线程的方法,利用for循环
for i in range(10):
	t = threading.Thread(target=run,args=(i,))
	t.start()
# -------------------------------------------------------------------------------------


# 开启了多线程同时执行
t_objs = []#存线程的实例
for i in range(10):
	t = threading.Thread(target=run,args=(i,))
	t.start()
	t_objs.append(t)#为了不阻塞后面子线程的启动,将实例放到t_objs

# #保证所有子线程执行完再执行之后的主线程,join之后线程就结束了
for t in t_objs:
	t.join()

# -------------------------------------------------------------------------------------

# 守护线程的使用
for i in range(10):
	t = threading.Thread(target=run,args=(i,))
	# 这个命令要放在t.start()前面,表示将子线程设置为守护线程,程序不会等待守护线程的结果
	# 执行完主线程之后程序就会退出,不会再sleep3秒
	t.setDaemon(True)
	t.start()
# -------------------------------------------------------------------------------------

# 一般的运行完t1再运行t2.
run('task_one')
run('task_two')

# -------------------------------------------------------------------------------------
# 几个常用的命令
print(threading.current_thread())  # 打印当前线程
print(threading.active_count())  # 打印活跃线程个数,包括自己
# -------------------------------------------------------------------------------------

# 多线程任务的继承类的写法,跟上面的是一样的

class MyThread(threading.Thread):

	def __init__(self, task_name):
		self.task_name =task_name
		super(MyThread, self).__init__()

	def run(self):#注意,这个函数名必须是run
		print('this is {}'.format(self.task_name))
		time.sleep(3)
t1 = MyThread('task_one')
t2 = MyThread('task_two')
t1.start()
t2.start()
# -------------------------------------------------------------------------------------
# 讲一个线程锁的使用,注意,在python3里似乎会自动加这个锁,但是2里一定要手动加,不然在这个例子里可能会出现少加
num = 0
lock = threading.Lock()
#python2版本的写法

def counter_num():
	lock.acquire()
	global num
	num += 1
	lock.release()

t_objs = []#存线程的实例
for i in range(10000):
	t = threading.Thread(target=counter_num)
	t.start()
	t_objs.append(t)#为了不阻塞后面子线程的启动,将实例放到t_objs
#
# #保证所有子线程执行完再执行之后的主线程,join之后线程就结束了
for t in t_objs:
	t.join()
print(num)

# python3版本的写法
def counter_num():

	global num
	num += 1

# t_objs = []#存线程的实例
for i in range(10000):
	t = threading.Thread(target=counter_num)
	t.start()
	t_objs.append(t)#为了不阻塞后面子线程的启动,将实例放到t_objs

#保证所有子线程执行完再执行之后的主线程,join之后线程就结束了
for t in t_objs:
	t.join()
print(num)
# -------------------------------------------------------------------------------------
# Semaphore(信号量)

# 互斥锁 同时只允许一个线程更改数据，而Semaphore是同时允许一定数量的线程更改数据 ，
# 比如厕所有3个坑，那最多只允许3个人上厕所，后面的人只能等里面有人出来了才能再进去。
# semaphore = threading.BoundedSemaphore(5)  # 最多允许5个线程同时运行

def run(n):
	semaphore.acquire()
	time.sleep(2)
	print("run the thread: {}".format(n))
	semaphore.release()


for i in range(20):
	t = threading.Thread(target=run, args=(i,))
	t.start()

while threading.active_count() != 1:  # 这也是判断线程是否都结束的一个方法
	pass  # print threading.active_count()
else:
	print('----all threads done---')


# -------------------------------------------------------------------------------------
# 通过Event来实现两个或多个线程间的交互，下面是一个红绿灯的例子，即起动一个线程做交通指挥灯，
# 生成几个线程做车辆，车辆行驶按红灯停，绿灯行的规则。
event = threading.Event()
def light():
	if not event.isSet():
		event.set()  # wait就不阻塞 #绿灯状态
	count = 0
	while True:
		if count < 10:
			print('\033[42;1m--green light on---\033[0m')
		elif count < 13:
			print('\033[43;1m--yellow light on---\033[0m')
		elif count < 20:
			if event.isSet():
				event.clear()
			print('\033[41;1m--red light on---\033[0m')
		else:
			count = 0
			event.set()  # 打开绿灯
		time.sleep(1)
		count += 1


def car(n):
	while 1:
		time.sleep(2)
		if event.isSet():  # 绿灯
			print("car [%s] is running.." % n)
		else:
			print("car [%s] is waiting for the red light.." % n)



Light = threading.Thread(target=light)
Light.start()
for i in range(3):
	t = threading.Thread(target=car, args=(i,))
	t.start()
```