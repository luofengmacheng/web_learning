## PHP中的正则表达式

正则表达式基本上也就那几个点，这里重新再次讲解下正则表达式，以及在PHP中的使用。

### 1 基本元字符

* `.` 匹配除换行符之外的任意一个字符
* `?` 重复前一个字符零次或1次
* `*` 重复前一个字符零次及以上
* `^` 匹配字符串的开头，`$` 匹配字符串的结尾
* `{m, n}` 重复前一个字符至少m次，最多n次
* `\w` 匹配字母数字或下划线(word)
* `\s` 匹配空白字符(space)
* `\b` 匹配单词的开始或结束
* `\d` 匹配数字

### 2 集合

当要指定多个字符时，就需要使用集合`[]`。

例如，要指定只能出现1、2、3，就可以:`[123]`。

大家知道，当要匹配`.`或者`*`时，由于它们是元字符，就需要对它们进行转义：`\.`或者`\*`，但是，如果是放在`[]`中时，就不需要转义:`[.*]`。

### 3 分支条件

在匹配时，有时候不能用一个表达式写出来要匹配的字符串，这时，就需要使用到分支条件：`|`。

`exp1|exp2`，匹配exp1或者exp2，另外，如果匹配了exp1，就不会再匹配exp2了。

### 4 分组

在匹配时，有可能想匹配一个组合，或者重复一个组合，这时，就要用到分组：`()`。

例如，要重复123三次：`(123){3}`

### 5 后向引用

后向引用用于匹配之前已经匹配的表达式。

正则表达式会对分组进行编号，第一个分组就是1，第二个分组就是2，依次类推。那么在后面如果要匹配第1个分组，就可以直接用`\1`。

### 6 零宽断言

有时，想要匹配某个内容，但是，又不想在匹配结果中得到它，这时，就要用到零宽断言，零宽断言通常是匹配一个位置。

* `(?=exp)` 匹配一个位置，该位置`后面是`exp
* `(?<=exp)` 匹配一个位置，该位置`前面是`exp
* `(?<!exp)` 匹配一个位置，该位置`前面不是`exp
* `(?!exp)` 匹配一个位置，该位置`后面不是`exp

### 7 贪婪匹配和懒惰匹配

默认情况下，会匹配尽可能多的字符，这称为`贪婪匹配`。但是，有时候要用到`懒惰匹配`，就是尽可能少的匹配字符。

在匹配时，在模式后面加上`?`就是懒惰匹配。

### 8 选项

选项可以改变正则表达式的处理方式：

* i(ignorecase) 忽略大小写
* m(multiline) 多行模式，默认情况下，`^`和`$`分别匹配`输入字符串`的开始和结束，当采用多行模式时，`^`匹配行的开始，`$`匹配`\n`和输入字符串的末尾。
* s(singleline) 单行模式，默认情况下，`.`不能匹配换行符，当采用单行模式时，`.`可以匹配输入字符串的任意字符。
* x 忽略非转义空白
* n 不捕获未命名的组

### 9 PHP中处理正则表达式

#### 9.1 preg_grep

preg_grep($pattern, $input[, $flag])

* $pattern是正则表达式，$input是数组。
* 对$input数组中的每个元素使用$pattern进行匹配，如果$flag=0，则输出$input中匹配的元素构成的数组。
* 如果$flag=PREG_GREP_INVERT,则输出$input中不匹配的元素构成的数组。

#### 9.2 preg_match

preg_match($pattern, $subject[, $match, $flag, $offset])

* $pattern是正则表达式，$subject是字符串。
* 用$pattern匹配$subject，将匹配的字符串保存到$match[0]中，而$match[1]保存匹配的第一个子组，$match[2]保存匹配的第二个子组，以此类推。
* 如果$flag=PREG_OFFSET_CAPTURE时，则$match[0]是一个由匹配的字符串和匹配的偏移量组成的数组。
* $offset指定开始匹配的位置。
* 返回成功匹配的次数，只有0或者1。

#### 9.3 preg_match_all

preg_match_all($pattern, $subject[, $match, $flag, $offset])

preg_match_all()与preg_match()类似，但是，有两点不同：

* preg_match()匹配成功一次后，就结束了。而preg_match_all()会一直匹配到末尾。
* $flag有多个不同的值。

#### 9.4 preg_replace

preg_replace($pattern, $replacement, $subject[, $limit, $count])

* $pattern是正则表达式，可以是字符串或者字符串数组，当然，$replace也是字符串或者字符串数组，正常情况下与$pattern一一对应，如果$pattern个数比$replacement多，剩下的就用空字符串替换。
* $subject是要替换的字符串或者字符串数组，如果是字符串数组，就依次遍历它的字符串，则返回值也是替换后的数组。
* $limit是每个模式在每个$subject上替换的最大次数。默认是无限大。
* $count，如果指定，则是最后完成的替换的次数。

#### 9.5 preg_filter

preg_filter($pattern, $replacement, $subject[, $limit, $count])

与preg_replace类似，只是preg_filter只返回匹配的元素替换的结果。

#### 9.6 preg_split

preg_split($pattern, $subject[, $limit, $flag])

使用$pattern正则表达式匹配的字符串作为分隔符分割$subject。

#### 9.7 preg_quote

preg_quote($str[, $delimiter])

$str是一个正则表达式，对它进行转义。

#### 9.8 preg_last_error

preg_last_error()返回最后一次preg_*函数执行出错的错误代码。

参考文档：

1 [正则表达式30分钟入门教程](http://deerchao.net/tutorials/regex/regex.htm)