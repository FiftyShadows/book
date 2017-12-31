#####承接上文,自己实现一个模板变量,不过意义不大,现在都是前后端分离了
```html
<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <title>Title</title>
</head>
<body>
<h1>This is Index</h1>
<h1>{{time}}</h1>
</body>
</html>
```
#####views文件
```python
import os
import time
def index():
	# return 'index'
	f = open(os.path.join('template','index.html'),'r')
	data = f.read()
	f.close()
	new_data = data.replace("{{time}}","{}".format(time.time()))
	return new_data


def login():
	# return 'login'
	f = open('login.html','r')
	data = f.read()
	f.close()
	return data
```
![](/assets/20171231132005.png)