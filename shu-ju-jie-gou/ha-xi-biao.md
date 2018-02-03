```python
from functools import reduce

class HashTable(object):
    """哈希表"""
    def __init__(self, hash_size=10007):
        self._hash_size = hash_size
        self._inner_list = [None]*hash_size
        self._key_list = []

    def _hash_key(self, key):
        """根据key值得到hash值"""
        return reduce(lambda x, y: x*10 + y, [ord(i) for i in key]) % self._hash_size

    def has_key(self, key):
        """看key存不存在"""
        hash_node = self._get_hash_node(key)
        if hash_node is None:
            return False
        return key in [i[0] for i in hash_node]

    def _get_hash_node(self, key):
        """根据key值得到hash_node"""
        hash_index = self._hash_key(key)
        return self._inner_list[hash_index]

    def clear(self):
        """清除里面的所有元素"""
        self.__init__()

    def get(self, key, default=None):
        """得到key对应的值"""
        hash_node = self._get_hash_node(key)
        if hash_node is not None:
            for i in hash_node:
                if i[0] == key:
                    return i[1]
        return default

    def set(self, key, value):
        """设置key, value"""
        hash_node = self._get_hash_node(key)
        hash_index = self._hash_key(key)
        if hash_node is None:
            self._inner_list[hash_index] = [[key, value]]
            self._key_list.append(key)
        else:
            if self.has_key(key):
                for i in self._inner_list[hash_index]:
                    if i[0] == key:
                        i[1] = value
            else:
                self._inner_list[hash_index].append([key, value])

    def __getitem__(self, key):
        return self.get(key)

    def __setitem__(self, key, value):
        self.set(key, value)
```