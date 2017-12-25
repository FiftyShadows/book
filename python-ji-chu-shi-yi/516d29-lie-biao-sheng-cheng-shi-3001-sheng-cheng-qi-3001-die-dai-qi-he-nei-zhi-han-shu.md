####1. 列表生成式
```python
>>> list(range(1, 11))
[1, 2, 3, 4, 5, 6, 7, 8, 9, 10]

>>> [x * x for x in range(1, 11)]
[1, 4, 9, 16, 25, 36, 49, 64, 81, 100]

```
####2. 生成器
**在Python中，这种一边循环一边计算的机制，称为生成器：generator**
创建一个generator，有很多种方法。第一种方法很简单，只要把一个列表生成式的[]改成()，就创建了一个generator
```python
>>> L = [x * x for x in range(10)]
>>> L
[0, 1, 4, 9, 16, 25, 36, 49, 64, 81]
>>> g = (x * x for x in range(10))
>>> g
<generator object <genexpr> at 0x1022ef630>
```
如果要一个一个打印出来，可以通过next()函数获得generator的下一个返回值：
```python
>>> next(g)
0
>>> next(g)
1
>>> next(g)
4
……
>>> next(g)
64
>>> next(g)
81
>>> next(g)
Traceback (most recent call last):
  File "<stdin>", line 1, in <module>
StopIteration
```
还可以用函数的方式,关键字是yield, 推一个斐波那契函数
```python
ef fib(max):
    n,a,b = 0,0,1

    while n < max:
        #print(b)
        yield  b
        a,b = b,a+b

        n += 1

    return 'done'
```
```python
>>> f = fib(6)
>>> f
<generator object fib at 0x104feaaa0>
```
####3. 迭代器
我们已经知道，可以直接作用于for循环的数据类型有以下几种： 
一类是集合数据类型，如list、tuple、dict、set、str等； 
一类是generator，包括生成器和带yield的generator function。 
这些可以直接作用于for循环的对象统称为可迭代对象：Iterable。 
可以使用isinstance()判断一个对象是否是Iterable对象：

```python
>>> from collections import Iterable
>>> isinstance([], Iterable)
True
>>> isinstance({}, Iterable)
True
>>> isinstance('abc', Iterable)
True
>>> isinstance((x for x in range(10)), Iterable)
True
>>> isinstance(100, Iterable)
False
```
可以被next()函数调用并不断返回下一个值的对象称为迭代器：Iterator。
#####总结:
1. 凡是可作用于for循环的对象都是Iterable类型；
2. 凡是可作用于next()函数的对象都是Iterator类型，它们表示一个惰性计算的序列； 
所以**生成器一定是迭代器**。
3. 集合数据类型如list、dict、str等是Iterable但不是Iterator，不过可以通过iter()函数获得一个Iterator对象。

####4. 内置函数

- filter(function_or_None,iterable) :一组数据里面过滤出符合条件的，返回迭代器
- map(func,*iterables) :对你传入的每个值进行处理，返回迭代器
- functools.reduce(function,sequence,initial=None)
```python
# filter()  一组数据过滤出你想要的来
res = filter(lambda n:n>5,range(10))
for i in res :
    print(i)
# map()  对传入的每个值进行处理返回一个列表
res = map(lambda n:n*2, range(10))
# res = [i*2 for i in range(10)]
# res = [ lambda i:i*2 for i in range(10)]
for i in res:
    print(i)
```