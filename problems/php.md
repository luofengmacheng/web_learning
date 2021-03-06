### 2016年8月23日

#### 1 PHP采用exec调用python、shell程序时，没有错误信息的处理方式

一般的语言都会提供类似system或者exec的调用方式，其实就是执行linux中的一条命令，而该命令可以是用任何语言编写的。例如，PHP中就提供了exec，但是，它只能给出返回的状态，无法给出错误信息，导致程序员在调试时无法快速地发现问题。

此时，常用的错误处理方式是：将命令的结果重定向到另一个文件。

```
cmd > cmd.log 2>&1
```
---

### 2016年8月25日

#### 2 前端传递对象到后端，当键中有特殊字符时造成的问题

javascript通常会传递一个对象(键值对)给后端，后端获取该对象时，会转换成该语言对应的数据结构，例如，如果后端是PHP，那么javascript中的对象会转换成PHP中的array()对象。但是，此时要注意的是，键值对中的键不能包含特殊字符，如果包含特殊字符，会导致后台无法获取键名。

此时，可以将整个对象转换成json字符串：

```javascript
JSON.stringify(data);
```

在后台再将数据解析成json对象：

```php
json_decode($data, true);
```

---

### 2016年8月26日

#### 3 判断文件的编码格式

文件处理过程中，最繁琐的就是编码转换的问题。为了在前台很好的显示，需要将内容转换成utf8，而在写入文件时，需要将内容转换成原编码。在转换过程中，一个很重要的功能就是：获取文件的编码类型。

由于国内常用的文件编码就是UTF-8和GBK，因此，在处理文件编码时，只判断是否是这两种其中的一种。

```php
function check_file_encode($path) {
    $content = file_get_contents($path);

    if(preg_match('%^(?:
    	[\x09\x0A\x0D\x20-\x7E]            # ASCII
    	| [\xC2-\xDF][\x80-\xBF]             # non-overlong 2-byte
        |  \xE0[\xA0-\xBF][\x80-\xBF]        # excluding overlongs
        | [\xE1-\xEC\xEE\xEF][\x80-\xBF]{2}  # straight 3-byte
        |  \xED[\x80-\x9F][\x80-\xBF]        # excluding surrogates
        |  \xF0[\x90-\xBF][\x80-\xBF]{2}     # planes 1-3
        | [\xF1-\xF3][\x80-\xBF]{3}          # planes 4-15
        |  \xF4[\x80-\x8F][\x80-\xBF]{2}     # plane 16
    )*$%xs', $content)) {
        return 'UTF-8';
    }

    return 'GBK';
}
```

在网上看到下面的方式：

```php
$encodes = array('ASCII', 'GBK', 'UTF-8');
foreach($encodes as $encode) {
    if ($content === mb_convert_encoding(mb_convert_encoding($content, "UTF-32", $encode), $encode, "UTF-32")) {
        return $encode;
    }
}
```

亲自测试，上面的方式不太可行，建议用check_file_encode()。

---

### 2016年9月19日

#### 4 字符串分割，抽取出想要的值(例如IP)

前端给一个输入框，用户输入IP列表进行查询，但是，用户输入的IP列表传递到后台时，IP之间的字符可能是`\r\n`，也可能是`\n`。需要从其中提取出IP列表。

最开始的想法是用字符串分割的方式，但是，由于分割符不确定，而且还可能有其它的干扰字符，因为，使用正则表达式进行提取。

preg_match_all('/(\d{1,3}\.){3}\d{1,3}', $ip, $res)

其中$ip是输入的字符串，那么$res[0]中就是提取的IP数组。

这里再复习下PHP中的正则表达式处理函数：

* preg_grep 对数组的元素进行grep，输出匹配的元素构成的数组
* preg_match 对字符串进行匹配，一次匹配成功则退出
* preg_match_all 对字符串进行匹配，会匹配到末尾
* preg_replace 对字符串或者数组进行正则替换

---

### 2016年10月18日

#### 5 再遇编码问题(编码是字符串本身的属性，不是文件的属性)

在前台对文件进行编辑，然后再写回到文件时，需要检测文件的原始编码，并转换为该编码。

在检测文件的原始编码时，需要注意的是，编码只能从字符本身获取，那么，这样就会存在一定的错误率：探测字符是否是某个编码时，只能对字符的二进制进行判断，但是，有些字符集可能是重合的，因此，如果两种不同的编码的某些字符的二进制有重合，那么，当检测这些字符的编码时，无法知道是哪一种，只有二选其一。

另外，虽然常说文件的编码，实际上，文件的每行编码都可以不一样，甚至每个字符的编码都可以不一样，因此，理论上来说，还是应该称为字符的编码，而不是文件的编码。当然，一般不会在一个文件中使用多种编码。

#### 6 使用SQL查询分组的最后一条记录

需求：获取每个分组中的最后一条记录

```
select * from sPackage group by name;
```

上述语句可以获取每个包的分组中的第一条数据。

```
select * from (select * from sPackage order by packageId desc) group by name;
```

采用子查询，对一个倒序的表进行分组，结果得到的就是每个分组中的最后一条记录。

在某种场景下，上述语句中的子查询返回的数据不是倒序的，就得不到正确的结果。

```
select * from sPackage where packageId in (select max(packageId) from sPackage group by name)
```

首先，获取每个分组中ID最大的ID，然后查询出ID在该集合中的记录。