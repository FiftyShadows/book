**1. 改变对象的字符串显示**
要改变一个实例的字符串表示，可重新定义它的 `__str__() `和 `__repr__()` 方法
```python
class Pair:
    def __init__(self, x, y):
        self.x = x
        self.y = y

    def __repr__(self):
        return 'Pair({0.x!r}, {0.y!r})'.format(self)

    def __str__(self):
        return '({0.x!s}, {0.y!s})'.format(self)
```
__repr__() 方法返回一个实例的代码表示形式，通常用来重新构造这个实例。 内置的 repr() 函数返回这个字符串，跟我们使用交互式解释器显示的值是一样的。 __str__() 方法将实例转换为一个字符串，使用 str() 或 print() 函数会输出这个字符串。比如：
```python
>>> p = Pair(3, 4)
>>> p
Pair(3, 4) # __repr__() output
>>> print(p)
(3, 4) # __str__() output
>>>
```