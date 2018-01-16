在Python 3.3以前的版本中，需要另外安装mock模块，可以使用pip命令来安装：` pip install mock`
然后`import mock`
从Python 3.3开始，mock模块已经被合并到标准库中，被命名为unittest.mock，可以直接import进来使用：`from unittest import mock`


####主要功能:
- **使用mock对象替代掉指定的Python对象，以达到模拟对象的行为**

    **Mock类的定义如下：**(Mock对象其实就是个Python类而已)
    
    ```python
    class Mock(spec=None, side_effect=None, return_value=DEFAULT, wraps=None, name=None, spec_set=None, **kwargs)
    ```
    
    
####Mock对象的一般用法是这样的：

    1.  **找到你要替换的对象，这个对象可以是一个类，或者是一个函数，或者是一个类实例。**

    - **然后实例化Mock类得到一个mock对象，并且设置这个mock对象的行为，比如被调用的时候返回什么值，被访问成员的时候返回什么值等。**

    - **使用这个mock对象替换掉我们想替换的对象，也就是步骤1中确定的对象。**

    - **之后就可以开始写测试代码，这个时候我们可以保证我们替换掉的对象在测试用例执行的过程中行为和我们预设的一样.**
    
#####举个例子
新建一个 client 文件(必须是单独的文件)
```python
import requests


def send_request(url):
    r = requests.get(url)
    return r.status_code


def visit_ustack():
    return send_request('http://www.baidu.com')
```

外部模块调用`visit_ustack()`来访问百度首页。下面我们使用mock对象在单元测试中分别测试访问正常和访问不正常的情况。

再新建一个文件:
```python
import unittest
import client
import mock

import client


class TestClient(unittest.TestCase):

    def test_success_request(self):
        success_send = mock.Mock(return_value='200')
        client.send_request = success_send
        self.assertEqual(client.visit_ustack(), '200')

    def test_fail_request(self):
        fail_send = mock.Mock(return_value='404')
        client.send_request = fail_send
        self.assertEqual(client.visit_ustack(), '404')
```
1. **找到要替换的对象：我们需要测试的是visit_ustack这个函数，那么我们需要替换掉`send_request`这个函数。**

- **实例化Mock类得到一个mock对象，并且设置这个mock对象的行为。在成功测试中，我们设置mock对象的返回值为字符串“200”，在失败测试中，我们设置mock对象的返回值为字符串"404"。**

- **使用这个mock对象替换掉我们想替换的对象。我们替换掉了`client.send_request`**

- **写测试代码。我们调用`client.visit_ustack()`，并且期望它的返回值和我们预设的一样。**

上面这个就是使用mock对象的基本步骤了。在上面的例子中我们替换了自己写的模块的对象，其实也可以替换标准库和第三方模块的对象，方法是一样的：先import进来，然后替换掉指定的对象就可以了。
