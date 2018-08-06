####先看知乎的回答:[如何理解 python 装饰器](https://www.zhihu.com/question/26930016)
```python
#-------------------------------------------------------------
#装饰器的手工方式
# def first_task(func):
#     print('this is first task')
#     func()
#
# def main_task():
#     print('this is main task')
#
# first_task(main_task)
# this is first task
# this is main task
#-------------------------------------------------------------
# 简单装饰器
# def first_task(func):
#     def wrapper(*args,**kwargs):
#         print('this is first_task')
#         return func(*args,**kwargs)
#     return wrapper
#
# def main_task():
#     print('this is main_task')
#
# bar = first_task(main_task)
# bar()
# # this is first_task
# # this is main_task
#
# #使用语法糖
# @first_task
# def main_task_two():
#     print('this is main_task_two')
# main_task_two()
# this is first_task
# this is main_task_two
#-------------------------------------------------------------
#带参数的装饰器
def first_task(name):
    def decorator(func):
        def wrapper(*args,**kwargs):
            print(name)
            print('this is first_task')
            return func(*args,**kwargs)
        return  wrapper
    return decorator

@first_task('shen')
def main_task():
    print('this is main_task')

main_task()
#
# shen
# this is first_task
# this is main_task
```
####再来几个个例子
```python
def makeBold(fn):
    def wrapped():
        return "<b>" + fn() + "</b>"
    return wrapped

#定义函数：完成包裹数据
def makeItalic(fn):
    def wrapped():
        return "<i>" + fn() + "</i>"
    return wrapped

@makeBold
def test1():
    return "hello world-1"

@makeItalic
def test2():
    return "hello world-2"

@makeBold
@makeItalic
def test3():
    return "hello world-3"

print(test1())
print(test2())
print(test3())
#<b>hello world-1</b>
#<i>hello world-2</i>
#<b><i>hello world-3</i></b>
```

***
```python
from time import ctime, sleep

def timefun(func):
    def wrappedfunc():
        print("%s called at %s"%(func.__name__, ctime()))
        func()
    return wrappedfunc

@timefun
def foo():
    print("I am foo")

foo()
sleep(2)
foo()
```
>         foo = timefun(foo)
    #foo先作为参数赋值给func后,foo接收指向timefun返回的wrappedfunc
    foo()
    #调用foo(),即等价调用wrappedfunc()
    #内部函数wrappedfunc被引用，所以外部函数的func变量(自由变量)并没有释放
    #func里保存的是原foo函数对象
    
####被装饰的函数有参数
```python
from time import ctime, sleep

def timefun(func):
    def wrappedfunc(a, b):
        print("%s called at %s"%(func.__name__, ctime()))
        print(a, b)
        func(a, b)
    return wrappedfunc

@timefun
def foo(a, b):
    print(a+b)

foo(3,5)
sleep(2)
foo(2,4)
```
