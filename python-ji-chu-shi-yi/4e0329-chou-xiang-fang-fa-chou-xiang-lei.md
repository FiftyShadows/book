####定义接口类,强制继承的类去重写,用来做约束
```python
import abc
class Father(metaclass=abc.ABCMeta):

	@abc.abstractmethod
	def my_func(self):
		print('必须执行')

class Son(Father):
	pass

inistance  = Son().my_func()
```
报错
```
TypeError: Can't instantiate abstract class Son with abstract methods my_func
```
你必须在类里重写这个方法
```python
import abc
class Father(metaclass=abc.ABCMeta):

	@abc.abstractmethod
	def my_func(self):
		print('必须执行')

class Son(Father):
	def my_func(self):
		print('重写方法')

inistance  = Son().my_func()
```
