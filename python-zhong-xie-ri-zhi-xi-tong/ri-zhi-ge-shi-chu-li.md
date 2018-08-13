```python

from logging.handlers import TimedRotatingFileHandler
from logstash_formatter import LogstashFormatterV1

def make_dir(make_dir_path):
    path = make_dir_path.strip()
    if not os.path.exists(path):
        os.makedirs(path)
    return path

log_dir_name = "logs"
# log_file_name = time.strftime('%Y-%m-%d', time.localtime(time.time())) + '.log'
log_file_name = 'flask_test.log'
log_file_folder = os.path.dirname(__file__) + os.sep + log_dir_name
make_dir(log_file_folder)
log_file_str = log_file_folder + os.sep + log_file_name
log_level = logging.DEBUG


handler = TimedRotatingFileHandler(filename= log_file_str, encoding='UTF-8')
handler.suffix = "%Y-%m-%d"
handler.setLevel(log_level)

handler.setFormatter(LogstashFormatterV1())
app.logger.addHandler(handler)
```

