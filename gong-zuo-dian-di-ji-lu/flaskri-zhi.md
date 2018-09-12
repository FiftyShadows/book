```python
from logstash_formatter import LogstashFormatterV1

from logging.handlers import TimedRotatingFileHandler


def make_dir(make_dir_path):
    path = make_dir_path.strip()
    if not os.path.exists(path):
        os.makedirs(path)
    return path

log_dir_name = "logs"
log_file_name = time.strftime('%Y-%m-%d', time.localtime(time.time())) + '.log'
# log_file_name = 'flask_test.log'
log_file_folder = os.path.dirname(__file__) + os.sep + log_dir_name
make_dir(log_file_folder)
log_file_str = log_file_folder + os.sep + log_file_name
log_level = logging.DEBUG


handler = TimedRotatingFileHandler(filename= log_file_str, encoding='UTF-8')
handler.suffix = "%Y-%m-%d"
handler.setLevel(log_level)

handler.setFormatter(LogstashFormatterV1())
app.logger.addHandler(handler)

class MiddleWare:
    def __init__(self,wsgi_app):
        self.wsgi_app = wsgi_app
    def __call__(self, *args, **kwargs):
        try:
            return self.wsgi_app(*args, **kwargs)
        except Exception as e:
            import traceback
            error_traceback = traceback.format_exc()
            app.logger.error(error_traceback)
            # return  返回一个500错误页面

app.wsgi_app = MiddleWare(app.wsgi_app)
app.register_blueprint(blog, url_prefix='/index')
app.config.from_object(config_use)
db.init_app(app)
# log.set_logging_config(app)
app.debug = True
```