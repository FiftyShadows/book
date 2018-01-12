1**以前一直是 print 输出信息来调试,领导告诉我要用 log 做到更好的记录
django 中修改写 settings**
```python
import platform

APP_NAME = "OAuth2-Login-Example"

if 'Windows' in platform.system():
    # for windows
    LOG_ROOT = os.environ.get("APP_LOG_PATH", r'D:\logs')
else:
    # for linux
    LOG_ROOT = os.environ.get("APP_LOG_PATH", '/tmp')

if 'Windows' in platform.system():
    LOGGING = {
        'level': 'DEBUG',
        'version': 1,
        'disable_existing_loggers': True,
        'formatters': {
            'standard': {
                'format': '%(asctime)s @query-guard@ [%(threadName)s] [%(name)s:%(lineno)d] [%(levelname)s]- %(message)s'
            },
        },
        'filters': {
            'slow_sql': {
                '()': 'django.utils.log.CallbackFilter',
                'callback': lambda record: record.duration > 0.1
            },
        },
        'handlers': {
            'console': {
                'level': 'INFO',
                'class': 'logging.StreamHandler',
                'formatter': 'standard'
            },
        }
    }
else:
    LOGGING = {
        'level': 'DEBUG',
        'version': 1,
        'disable_existing_loggers': True,
        'formatters': {
            'standard': {
                'format': '%(asctime)s @query-guard@ [%(threadName)s] [%(name)s:%(lineno)d] [%(levelname)s]- %(message)s'
            },
        },
        'handlers': {
            'console': {
                'level': 'INFO',
                'class': 'logging.StreamHandler',
                'formatter': 'standard'
            },
            'default': {
                'level': 'DEBUG',
                'class': 'logging.handlers.TimedRotatingFileHandler',
                'filename': os.path.join(LOG_ROOT, APP_NAME+'.log'),
                'when': 'MIDNIGHT',
                'backupCount': 7,
                'encoding': 'utf8',
                'interval': 1,
                'formatter': 'standard',
            },
            'request_handler': {
                'level': 'INFO',
                'class': 'logging.handlers.RotatingFileHandler',
                'filename': os.path.join(LOG_ROOT, 'reques.log'),
                'maxBytes': 1024 * 1024 * 5,
                'backupCount': 5,
                'formatter': 'standard',
            },
            'importer': {
                'level': 'DEBUG',
                'class': 'logging.handlers.TimedRotatingFileHandler',
                'filename': os.path.join(LOG_ROOT, APP_NAME+'.importer.log'),
                'when': 'MIDNIGHT',
                'backupCount': 7,
                'encoding': 'utf8',
                'interval': 1,
                'formatter': 'standard',
            },
            'ui.api': {
                'level': 'DEBUG',
                'class': 'logging.handlers.TimedRotatingFileHandler',
                'filename': os.path.join(LOG_ROOT, APP_NAME+'.ui.api.log'),
                'when': 'MIDNIGHT',
                'backupCount': 7,
                'encoding': 'utf8',
                'interval': 1,
                'formatter': 'standard',
            },
            'backgroud': {
                'level': 'DEBUG',
                'class': 'logging.handlers.TimedRotatingFileHandler',
                'filename': os.path.join(LOG_ROOT, APP_NAME+'.backgroud.log'),
                'when': 'MIDNIGHT',
                'backupCount': 7,
                'encoding': 'utf8',
                'interval': 1,
                'formatter': 'standard',
            },
            'sql': {
                'level': 'DEBUG',
                'class': 'logging.handlers.TimedRotatingFileHandler',
                'filename': os.path.join(LOG_ROOT, APP_NAME+'.sql.log'),
                'when': 'MIDNIGHT',
                'backupCount': 7,
                'encoding': 'utf8',
                'interval': 1,
                'formatter': 'standard',
            },

        },
        'loggers': {
            '': {
                'handlers': ['default', 'console', ],
                'level': 'INFO',
                'propagate': False
            },
            'importer': {
                'handlers': ['importer', 'console'],
                'level': 'INFO',
                'propagate': False
            },
            'ui.api': {
                'handlers': ['ui.api', 'console'],
                'level': 'INFO',
                'propagate': False
            },
            'django': {
                'handlers': ['default', 'console'],
                'level': 'DEBUG',
                'propagate': False
            },
            'django.request': {
                'handlers': ['request_handler', 'console'],
                'level': 'INFO',
                'propagate': False
            },
            'backgroud': {
                'handlers': ['backgroud', 'console'],
                'level': 'DEBUG',
                'propagate': False
            },
            'django.db.backends': {
                'handlers': ['sql'],
                'level': 'DEBUG',
                'propagate': False
            }
        }
    }
```

**在需要 print 的文件中记得加 ` logger = logging.getLogger(__name__)
` **

**然后就可以用 `loger.info()`代替` print` 做更好的调试了**