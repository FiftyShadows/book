因为 ide 的提示报错,我以为不能直接调用...其实是可以的
```python
class B(object):
    #调用了子类的属性
    def test_print(self):
        print(self.name)

class A(B):
    def __init__(self,name):
        self.name = name

a = A('shen').test_print()
```