####先看知乎的回答:[如何理解 python 装饰器](https://www.zhihu.com/question/26930016)
```
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
