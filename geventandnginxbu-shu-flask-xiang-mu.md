uwsgi真是坑惨了,都是老教程
####APP文件
```python

# -*- coding: utf8 -*-
from flask import Flask
from flask_sqlalchemy import SQLAlchemy
from config import config_use
from views import blog
from gevent.pywsgi import WSGIServer
db = SQLAlchemy()
app = Flask(__name__)



app.register_blueprint(blog, url_prefix='/index')
app.config.from_object(config_use)
db.init_app(app)


http_server = WSGIServer(("127.0.0.1",5000),app)
http_server.serve_forever()

```

####`etc/conf.d`位置下新建个proxy.conf文件,不要直接修改nginx.conf文件

proxy.conf
```conf
server {
    listen       80;
    server_name  118.89.30.217;
    location / {
	proxy_redirect off;
            proxy_set_header Host $host:80;
            proxy_set_header X-Real-IP $remote_addr;
            proxy_pass http://127.0.0.1:5000;    
            proxy_set_header X-Forwarded-For         $proxy_add_x_forwarded_for;
    }

}
```
#### 再去项目下启动 Python3 app.py




####gunicorn的配置方案

项目根目录下新建gunicorn_config.py文件,虚拟环境下`pip install gunicorn`
```python
#  -*- coding: utf-8 -*-
import multiprocessing


"""gunicorn的配置文件"""


# 预加载资源
preload_app = True
# 绑定
bind = "0.0.0.0:5000"
# 进程数
workers = multiprocessing.cpu_count() * 2 + 1
# 线程数
threads = multiprocessing.cpu_count() * 2
# 等待队列最大长度,超过这个长度的链接将被拒绝连接
backlog = 2048
# 工作模式
# worker_class = "egg:meinheld#gunicorn_worker"
worker_class = "gevent"
# 最大客户客户端并发数量,对使用线程和协程的worker的工作有影响
worker_connections = 1200
# 进程名称
proc_name = 'gunicorn.pid'
# 进程pid记录文件
pidfile = 'app_run.log'
# 日志等级
loglevel = 'debug'
# 日志文件名
logfile = 'debug.log'
# 访问记录
accesslog = 'access.log'
# 访问记录格式
access_log_format = '%(h)s %(t)s %(U)s %(q)s'

"""
运行方式
命令行 gunicorn -c gunicorn_config.py app:app
如果在虚拟环境下运行(双py的ubuntu之流需要虚拟环境,否则会路径混乱):
第一个app是app.py的文件名,第二个是flask的app
"""
```
####Supervisor的配置
只支持python2,所以安装在全局下
`etc/supervisord.d`文件夹下,新建**.ini文件,内容
```ini
[program:flasktest]
directory = /root/python_web/flasktest
command = /root/python_web/flask_test_env/bin/gunicorn -c gunicorn_config.py app:app
autostart = True
autorestart=True
#Unlinking stale socket /var/run/supervisor/supervisor.sock  傻逼一样的bug
[supervisorctl]
serverurl=unix:///var/run/supervisor.sock
[unix_http_server]
file=/tmp/supervisor.sock

#user = root　
#stdout_logfile=/var/log/uwsgi/supervisor_flask.log      # 运行日志
#stderr_logfile=/var/log/uwsgi/supervisor_flask_err.log  # 错误日志


```
然后启动`supervisord -c /etc/supervisord.conf`


