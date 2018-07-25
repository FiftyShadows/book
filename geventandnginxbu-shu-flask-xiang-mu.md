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