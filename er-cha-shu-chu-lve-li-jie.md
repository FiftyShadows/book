用python 表示一颗二叉树
![](/assets/WX20171220-152346@2x.png)
#####基本的用类来表示
```python
class Tree(object):
    def __init__(self,left,right):
        self.left = left
        self.right = right
t = Tree(Tree("a","b"),Tree(None,"d"))
```