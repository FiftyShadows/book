#####个人理解,对于所有的Web应用，本质上其实就是一个socket服务端，用户的浏览器其实就是一个socket客户端。关于socket,可以看我之前的文章.
#####另外.django,flask之类的框架本身不提供socket服务,需要额外wsgi(socket的封装?)
#####请求----->socket服务-------->业务逻辑------->返回
接下来的代码请使用2的环境,3的会有不知名报错
#####项目结构如图,我模仿的django的管理方式,做到了不错的解耦
![](/assets/20171231130700.png)
#####main文件
```python
from wsgiref.simple_server import make_server
from urls import URLS

def RunServer(environ, start_response):
	start_response('200 OK', [('Content-Type', 'text/html')])
	url = environ['PATH_INFO']
	print(url)
	if url in URLS.keys():
		func_name = URLS[url]
		ret = func_name()
	else:
		ret = "404"
	return ret

if __name__ == '__main__':
	httpd = make_server('', 8000, RunServer)
	print("Serving HTTP on port 8000...")
	httpd.serve_forever()
```
#####url文件
```python
from wsgiref.simple_server import make_server
from urls import URLS

def RunServer(environ, start_response):
	start_response('200 OK', [('Content-Type', 'text/html')])
	url = environ['PATH_INFO']
	print(url)
	if url in URLS.keys():
		func_name = URLS[url]
		ret = func_name()
	else:
		ret = "404"
	return ret

if __name__ == '__main__':
	httpd = make_server('', 8000, RunServer)
	print("Serving HTTP on port 8000...")
	httpd.serve_forever()
```
#####views文件
```python
from wsgiref.simple_server import make_server
from urls import URLS

def RunServer(environ, start_response):
	start_response('200 OK', [('Content-Type', 'text/html')])
	url = environ['PATH_INFO']
	print(url)
	if url in URLS.keys():
		func_name = URLS[url]
		ret = func_name()
	else:
		ret = "404"
	return ret

if __name__ == '__main__':
	httpd = make_server('', 8000, RunServer)
	print("Serving HTTP on port 8000...")
	httpd.serve_forever()
```