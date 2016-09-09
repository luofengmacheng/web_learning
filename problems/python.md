### 2016年9月8日

#### 1 使用logging模块打印日志时，日志级别的困扰

当在python的多个模块中使用logging打印日志时，会由于root logger和子logger以及logger和handler的日志级别的不同而造成无法理解的行为，例如：

```python
import logging

log = logging.getLogger(__name__)
fh = logging.FileHandler('test.log')
fh.setLevel(logging.DEBUG)
log.addHandler(fh)

def make_log():
    log.debug('debug')
    log.info('info')
    log.warn('warn')
    log.error('error')
    log.critical('critical')

make_log()
```

以上代码打印的日志中会有如下内容(这里只给出消息内容)：

```
warn
error
critical
```

这是为什么呢？问题解答详见[日志的继承对日志级别的影响以及使用logging模块的注意要点](https://github.com/luofengmacheng/python/blob/master/logging_level.md)

#### 2 python在终端打印中文