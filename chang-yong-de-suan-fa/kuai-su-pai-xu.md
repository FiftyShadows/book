快速排序基本思想是：通过一趟排序将要排序的数据分割成独立的两部分，其中一部分的所有数据都比另外一部分的所有数据都要小，然后再按此方法对这两部分数据分别进行快速排序，整个排序过程可以递归进行，以此达到整个数据变成有序序列。

如序列[6，8，1，4，3，9]，选择6作为基准数。从右向左扫描，寻找比基准数小的数字为3，交换6和3的位置，[3，8，1，4，6，9]，接着从左向右扫描，寻找比基准数大的数字为8，交换6和8的位置，[3，6，1，4，8，9]。重复上述过程，直到基准数左边的数字都比其小，右边的数字都比其大。然后分别对基准数左边和右边的序列递归进行上述方法。
```python
def parttion(v, left, right):
    key = v[left]
    low = left
    high = right
    while low < high:
         while (low < high) and (v[high] >= key):
            high -= 1
         v[low] = v[high]
         while (low < high) and (v[low] <= key):
             low += 1
         v[high] = v[low]
         v[low] = key
    return low
def quicksort(v, left, right):
     if left < right:
         p = parttion(v, left, right)
         quicksort(v, left, p-1)
         quicksort(v, p+1, right)
     return v

s = [6, 8, 1, 4, 3, 9, 5, 4, 11, 2, 2, 15, 6]
print("before sort:",s)
s1 = quicksort(s, left = 0, right = len(s) - 1)
print("after sort:",s1)
```