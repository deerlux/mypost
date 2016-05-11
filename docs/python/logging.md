# logging系统用法

``` python
from logging.handlers import SMTPHandler
logger = logging.getLogger('helloworld')
handler = SMTPHandler('localhost', 'deerlux@163.com', 'root@localhost', 'helloworld')
logger.addHandler(handler)

logger.warning('This is a test')
```


