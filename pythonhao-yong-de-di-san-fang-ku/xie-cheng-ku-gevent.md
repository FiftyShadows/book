废话不多,上代码
```
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