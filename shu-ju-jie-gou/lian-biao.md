###为什么需要链表
**顺序表的构建需要预先知道数据大小来申请连续的存储空间，而在进行扩充时又需要进行数据的搬迁，所以使用起来并不是很灵活。**

**链表结构可以充分利用计算机内存空间，实现灵活的内存动态管理。**
###链表的定义
链表（Linked list）是一种常见的基础数据结构，是一种线性表，但是不像顺序表一样连续存储数据，而是在每一个节点（数据存储单元）里存放下一个节点的位置信息（即地址）。
![](/assets/4.png)

<br>

###单向链表
单向链表也叫单链表，是链表中最简单的一种形式，它的每个节点包含两个域，一个信息域（元素域）和一个链接域。这个链接指向链表中的下一个节点，而最后一个节点的链接域则指向一个空值。
![](/assets/5.png)
- 表元素域elem用来存放具体的数据。
- 链接域next用来存放下一个节点的位置（python中的标识）
- 变量p指向链表的头节点（首节点）的位置，从p出发能找到表中的任意节点。
###节点的实现
```python
class SingleNode(object):
    """单链表的结点"""
    def __init__(self,item):
        # _item存放数据元素
        self.item = item
        # _next是下一个节点的标识
        self.next = None
```
###单链表的操作
- is_empty() 链表是否为空
- length() 链表长度
- travel() 遍历整个链表
- add(item) 链表头部添加元素
- append(item) 链表尾部添加元素
- insert(pos, item) 指定位置添加元素
- remove(item) 删除节点
- search(item) 查找节点是否存在
###单链表的实现
```python
class SingleLinkList(object):
    """单链表"""
    def __init__(self):
        self._head = None

    def is_empty(self):
        """判断链表是否为空"""
        return self._head == None

    def length(self):
        """链表长度"""
        # cur初始时指向头节点
        cur = self._head
        count = 0
        # 尾节点指向None，当未到达尾部时
        while cur != None:
            count += 1
            # 将cur后移一个节点
            cur = cur.next
        return count

    def travel(self):
        """遍历链表"""
        cur = self._head
        while cur != None:
            print cur.item,
            cur = cur.next
        print ""
```
####头部添加元素
![](/assets/6.png)
```python
def add(self, item):
        """头部添加元素"""
        # 先创建一个保存item值的节点
        node = SingleNode(item)
        # 将新节点的链接域next指向头节点，即_head指向的位置
        node.next = self._head
        # 将链表的头_head指向新节点
        self._head = node
```
####尾部添加元素
```python
def append(self, item):
        """尾部添加元素"""
        node = SingleNode(item)
        # 先判断链表是否为空，若是空链表，则将_head指向新节点
        if self.is_empty():
            self._head = node
        # 若不为空，则找到尾部，将尾节点的next指向新节点
        else:
            cur = self._head
            while cur.next != None:
                cur = cur.next
            cur.next = node
```
####指定位置添加元素
![](/assets/7.png)
```python
 def insert(self, pos, item):
        """指定位置添加元素"""
        # 若指定位置pos为第一个元素之前，则执行头部插入
        if pos <= 0:
            self.add(item)
        # 若指定位置超过链表尾部，则执行尾部插入
        elif pos > (self.length()-1):
            self.append(item)
        # 找到指定位置
        else:
            node = SingleNode(item)
            count = 0
            # pre用来指向指定位置pos的前一个位置pos-1，初始从头节点开始移动到指定位置
            pre = self._head
            while count < (pos-1):
                count += 1
                pre = pre.next
            # 先将新节点node的next指向插入位置的节点
            node.next = pre.next
            # 将插入位置的前一个节点的next指向新节点
            pre.next = node
```
####删除节点
![](/assets/8.png)
```python
def remove(self,item):
        """删除节点"""
        cur = self._head
        pre = None
        while cur != None:
            # 找到了指定元素
            if cur.item == item:
                # 如果第一个就是删除的节点
                if not pre:
                    # 将头指针指向头节点的后一个节点
                    self._head = cur.next
                else:
                    # 将删除位置前一个节点的next指向删除位置的后一个节点
                    pre.next = cur.next
                break
            else:
                # 继续按链表后移节点
                pre = cur
                cur = cur.next
```
####查找节点是否存在
```python
 def search(self,item):
        """链表查找节点是否存在，并返回True或者False"""
        cur = self._head
        while cur != None:
            if cur.item == item:
                return True
            cur = cur.next
        return False
```
####测试
```python
if __name__ == "__main__":
    ll = SingleLinkList()
    ll.add(1)
    ll.add(2)
    ll.append(3)
    ll.insert(2, 4)
    print "length:",ll.length()
    ll.travel()
    print ll.search(3)
    print ll.search(5)
    ll.remove(1)
    print "length:",ll.length()
    ll.travel()
```
<br>
###链表与顺序表的对比
链表失去了顺序表随机读取的优点，同时链表由于增加了结点的指针域，空间开销比较大，但对存储空间的使用要相对灵活。

链表与顺序表的各种操作复杂度如下所示：
![](/assets/11.png)
注意虽然表面看起来复杂度都是 O(n)，但是链表和顺序表在插入和删除时进行的是完全不同的操作。链表的主要耗时操作是遍历查找，删除和插入操作本身的复杂度是O(1)。顺序表查找很快，主要耗时的操作是拷贝覆盖。因为除了目标元素在尾部的特殊情况，顺序表进行插入和删除时需要对操作点之后的所有元素进行前后移位操作，只能通过拷贝和覆盖的方法进行。

<br>
<br>

###**单向循环链表**
单链表的一个变形是单向循环链表，链表中最后一个节点的next域不再为None，而是指向链表的头节点。

![](/assets/单向循环链表.png)

####操作
- is_empty() 判断链表是否为空
- length() 返回链表的长度
- travel() 遍历
- add(item) 在头部添加一个节点
- append(item) 在尾部添加一个节点
- insert(pos, item) 在指定位置pos添加节点
- remove(item) 删除一个节点
- search(item) 查找节点是否存在
####实现

```python
class Node(object):
    """节点"""
    def __init__(self, item):
        self.item = item
        self.next = None
        
class SinCycLinkedlist(object):
    """单向循环链表"""
    def __init__(self):
        self._head = None

    def is_empty(self):
        """判断链表是否为空"""
        return self._head == None

    def length(self):
        """返回链表的长度"""
        # 如果链表为空，返回长度0
        if self.is_empty():
            return 0
        count = 1
        cur = self._head
        while cur.next != self._head:
            count += 1
            cur = cur.next
        return count

    def travel(self):
        """遍历链表"""
        if self.is_empty():
            return
        cur = self._head
        print cur.item,
        while cur.next != self._head:
            cur = cur.next
            print cur.item,
        print ""


    def add(self, item):
        """头部添加节点"""
        node = Node(item)
        if self.is_empty():
            self._head = node
            node.next = self._head
        else:
            #添加的节点指向_head
            node.next = self._head
            # 移到链表尾部，将尾部节点的next指向node
            cur = self._head
            while cur.next != self._head:
                cur = cur.next
            cur.next = node
            #_head指向添加node的
            self._head = node

    def append(self, item):
        """尾部添加节点"""
        node = Node(item)
        if self.is_empty():
            self._head = node
            node.next = self._head
        else:
            # 移到链表尾部
            cur = self._head
            while cur.next != self._head:
                cur = cur.next
            # 将尾节点指向node
            cur.next = node
            # 将node指向头节点_head
            node.next = self._head

    def insert(self, pos, item):
        """在指定位置添加节点"""
        if pos <= 0:
            self.add(item)
        elif pos > (self.length()-1):
            self.append(item)
        else:
            node = Node(item)
            cur = self._head
            count = 0
            # 移动到指定位置的前一个位置
            while count < (pos-1):
                count += 1
                cur = cur.next
            node.next = cur.next
            cur.next = node

    def remove(self, item):
        """删除一个节点"""
        # 若链表为空，则直接返回
        if self.is_empty():
            return
        # 将cur指向头节点
        cur = self._head
        pre = None
        # 若头节点的元素就是要查找的元素item
        if cur.item == item:
            # 如果链表不止一个节点
            if cur.next != self._head:
                # 先找到尾节点，将尾节点的next指向第二个节点
                while cur.next != self._head:
                    cur = cur.next
                # cur指向了尾节点
                cur.next = self._head.next
                self._head = self._head.next
            else:
                # 链表只有一个节点
                self._head = None
        else:
            pre = self._head
            # 第一个节点不是要删除的
            while cur.next != self._head:
                # 找到了要删除的元素
                if cur.item == item:
                    # 删除
                    pre.next = cur.next
                    return
                else:
                    pre = cur
                    cur = cur.next
            # cur 指向尾节点
            if cur.item == item:
                # 尾部删除
                pre.next = cur.next

    def search(self, item):
        """查找节点是否存在"""
        if self.is_empty():
            return False
        cur = self._head
        if cur.item == item:
            return True
        while cur.next != self._head:
            cur = cur.next
            if cur.item == item:
                return True
        return False

if __name__ == "__main__":
    ll = SinCycLinkedlist()
    ll.add(1)
    ll.add(2)
    ll.append(3)
    ll.insert(2, 4)
    ll.insert(4, 5)
    ll.insert(0, 6)
    print "length:",ll.length()
    ll.travel()
    print ll.search(3)
    print ll.search(7)
    ll.remove(1)
    print "length:",ll.length()
    ll.travel()
```
<br>
<br>

###双向链表

一种更复杂的链表是“双向链表”或“双面链表”。每个节点有两个链接：一个指向前一个节点，当此节点为第一个节点时，指向空值；而另一个指向下一个节点，当此节点为最后一个节点时，指向空值。

![](/assets/双向链表.png)

####操作is_empty() 链表是否为空
- length() 链表长度
- travel() 遍历链表
- add(item) 链表头部添加
- append(item) 链表尾部添加
- insert(pos, item) 指定位置添加
- remove(item) 删除节点
- search(item) 查找节点是否存在

####实现

```python
class Node(object):
    """双向链表节点"""
    def __init__(self, item):
        self.item = item
        self.next = None
        self.prev = None


class DLinkList(object):
    """双向链表"""
    def __init__(self):
        self._head = None

    def is_empty(self):
        """判断链表是否为空"""
        return self._head == None

    def length(self):
        """返回链表的长度"""
        cur = self._head
        count = 0
        while cur != None:
            count += 1
            cur = cur.next
        return count

    def travel(self):
        """遍历链表"""
        cur = self._head
        while cur != None:
            print cur.item,
            cur = cur.next
        print ""

    def add(self, item):
        """头部插入元素"""
        node = Node(item)
        if self.is_empty():
            # 如果是空链表，将_head指向node
            self._head = node
        else:
            # 将node的next指向_head的头节点
            node.next = self._head
            # 将_head的头节点的prev指向node
            self._head.prev = node
            # 将_head 指向node
            self._head = node

    def append(self, item):
        """尾部插入元素"""
        node = Node(item)
        if self.is_empty():
            # 如果是空链表，将_head指向node
            self._head = node
        else:
            # 移动到链表尾部
            cur = self._head
            while cur.next != None:
                cur = cur.next
            # 将尾节点cur的next指向node
            cur.next = node
            # 将node的prev指向cur
            node.prev = cur



    def search(self, item):
        """查找元素是否存在"""
        cur = self._head
        while cur != None:
            if cur.item == item:
                return True
            cur = cur.next
        return False
```
####指定位置插入节点

![](/assets/双向链表指定位置插入元素.png)

```python
def insert(self, pos, item):
        """在指定位置添加节点"""
        if pos <= 0:
            self.add(item)
        elif pos > (self.length()-1):
            self.append(item)
        else:
            node = Node(item)
            cur = self._head
            count = 0
            # 移动到指定位置的前一个位置
            while count < (pos-1):
                count += 1
                cur = cur.next
            # 将node的prev指向cur
            node.prev = cur
            # 将node的next指向cur的下一个节点
            node.next = cur.next
            # 将cur的下一个节点的prev指向node
            cur.next.prev = node
            # 将cur的next指向node
            cur.next = node
```

####删除元素

![](/assets/双向链表删除节点.png)

```python
def remove(self, item):
        """删除元素"""
        if self.is_empty():
            return
        else:
            cur = self._head
            if cur.item == item:
                # 如果首节点的元素即是要删除的元素
                if cur.next == None:
                    # 如果链表只有这一个节点
                    self._head = None
                else:
                    # 将第二个节点的prev设置为None
                    cur.next.prev = None
                    # 将_head指向第二个节点
                    self._head = cur.next
                return
            while cur != None:
                if cur.item == item:
                    # 将cur的前一个节点的next指向cur的后一个节点
                    cur.prev.next = cur.next
                    # 将cur的后一个节点的prev指向cur的前一个节点
                    cur.next.prev = cur.prev
                    break
                cur = cur.next
```

####测试
```python
if __name__ == "__main__":
    ll = DLinkList()
    ll.add(1)
    ll.add(2)
    ll.append(3)
    ll.insert(2, 4)
    ll.insert(4, 5)
    ll.insert(0, 6)
    print "length:",ll.length()
    ll.travel()
    print ll.search(3)
    print ll.search(4)
    ll.remove(1)
    print "length:",ll.length()
    ll.travel()
```