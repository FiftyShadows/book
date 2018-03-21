给出链表 1->2->3->3->4->5->3, 和 val = 3, 你需要返回删除3之后的链表：1->2->4->5

```python
"""
Definition of ListNode
class ListNode(object):
    def __init__(self, val, next=None):
        self.val = val
        self.next = next
"""

class Solution:
    """
    @param head: a ListNode
    @param val: An integer
    @return: a ListNode
    """
    def removeElements(self, head, val):
        if head is None:
            return None
        while head.val == val:
            head = head.next
            if (head == None):
                return None
        pre = head
        while pre.next is not None:
            if pre.next.val == val:
                pre.next = pre.next.next
            else:
                pre = pre.next
        return head
```