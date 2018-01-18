#####python atexit 模块定义了一个 register 函数，用于在 python 解释器中注册一个退出函数，这个函数在解释器正常终止时自动执行,一般用来做一些资源清理的操作。 atexit 按注册的相反顺序执行这些函数; 例如注册A、B、C，在解释器终止时按顺序C，B，A运行。(类似 go 的 defer)

**Note：**如果程序是非正常crash，或者通过os._exit()退出，注册的退出函数将不会被调用。

#####注册 退出函数

```python
atexit.register(func, *args, **kargs)
```

将func作为要在终止时执行的函数。任何要传递给func的可选参数必须作为参数传递给register() 。可以多次注册相同的函数和参数。

当程序退出的时候，按先进后出的顺序调用注册的函数。如果退出函数在执行过程中抛出了异常，atexit会打印异常的信息，并继续执行下一下回调，直到所有退出函数执行完毕，它会重新抛出最后接收到的异常。

#####通过装饰器的方式：
```python
from atexit import register


def main():
    print('Do something.')


@register
def _atexit():
    print('Done.')


if __name__ == '__main__':
    main()
    
#Do something.
#Done.
```

#####非装饰器的方式：
```python

from atexit import register


def main():
    # pass
    print('XX')


def goodbye(name, adjective):
    print('Goodbye, %s, it was %s to meet you.' % (name, adjective))


register(goodbye, 'Donny', 'nice')
# or:
# register(goodbye, adjective='nice', name='Donny')
if __name__ == '__main__':
    main()
      
# XX
# Goodbye, Donny, it was nice to meet you.
```