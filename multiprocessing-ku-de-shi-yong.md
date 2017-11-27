####multiprocessing包是Python中的多进程管理包。它与 threading.Thread类似，可以利用multiprocessing.Process对象来创建一个进程。该进程可以允许放在Python程序内部编写的函数中。该Process对象与Thread对象的用法相同，拥有is_alive()、join([timeout])、run()、start()、terminate()等方法。属性有：authkey、daemon（要通过start()设置）、exitcode(进程在运行时为None、如果为–N，表示被信号N结束）、name、pid。此外multiprocessing包中也有Lock/Event/Semaphore/Condition类，用来同步进程，其用法也与threading包中的同名类一样。multiprocessing的很大一部份与threading使用同一套API，只不过换到了多进程的情境。
####这个模块表示像线程一样管理进程，这个是multiprocessing的核心，它与threading很相似，对多核CPU的利用率会比threading好的多。

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