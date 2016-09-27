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

---

### 2016年9月18日

#### 3 需要使用数据生成表格

在进行系统迁移时，需要统计已经迁移的系统，这时，需要查询数据库，并将数据写入到Excel表格中。

开始就想到python中应该有操作Excel表格的模块，百度了下，果然有：xlrd和xlwt，分别对应Excel的读取和写入。

但是，发现用xlrd和xlwt优点小题大作，这里只是需要将数据写入到文件，然后可以用Excel打开就行，于是，想到了csv格式的文件：就是一个文本文件，里面的字段用逗号分隔(其实也可以用别的字符分割)，可以用Excel打开。

python中操作csv文件的是csv模块，其中主要的类是DictReader和DictWriter。

#### 4 将中文写入文件(encode和decode)以及字符串的编码问题

* 默认情况下，字符串是以unicode进行编码的。
* encode: 从unicode转换成其它编码
* decode: 从其它编码转换成unicode

#### 5 os.system()、os.popen()、commands

这两个函数都可以用于执行外部命令或者程序：

os.system('ls')，执行一个外部命令，返回这个命令的状态码。

os.popen('ls')，执行一个外部命令，打开一个对这个命令的一个管道，可以读取该命令的输出。

commands.getstatusoutput('ls')，执行一个外部命令，返回这个命令的状态码和输出。

注意：上述的状态码的高8位就是在linux中执行该命令的返回码。