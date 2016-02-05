## 操作DOM

### 1 操作属性

* attr() 获取或者设置属性。.attr('title')，获取属性名title的内容；.attr('title', 'test')，设置title属性为test，当要设置多个属性时，可以使用对象结构：.attr({title: 'test', rel: 'external'})。而且，还可以使用函数设置属性：

```javascript
$(selector).attr(
	rel: 'external',
	id: function(index, oldVal) {
		return 'id' + index;
	}
);
```

给选择的所有元素同时设置rel和id，由于ID必须是唯一的，因此，使用匿名函数设置ID：函数的第一个参数是该元素在所选择的所有元素中的索引，第二个函数是进行设置之前的值。

### 2 操作DOM树

在操作DOM树时，可以使用$()创建元素，并对创建的元素进行操作。

* 创建元素。直接在$()中填写要创建的元素，例如：`$('<span>element</span>')`创建了一个span元素，元素的文本是element。
* 插入元素。将创建的元素插入到想插入的位置，例如：`$('<span>element</span>').insertAfter('div')`，先创建一个span元素，再将这个span元素插入到每个div的后面。与insertAfter类似的功能还有：insertBefore、prependTo、appendTo。
* 移动元素。使用$(selector)获取元素，然后采用上面提到的方法可以将selector选取的元素插入到想插入的地方。
* 包装元素。用一个元素包含另外一个元素，主要是要搞清楚：谁包含谁？

```
$(selector)获取被包装的元素，参数就是包含的元素：$('span').wrap('<li></li>')，span选择了所有的span标签，
用li标签包含每个span标签。
$('span').wrapAll('<li></li>')，将所有选择的span标签作为一个整体，用li标签包含所有的span标签。
$('span').wrapInner('<li></li>')，选择所有的span标签，用li标签包含每个span标签的内容。
需要注意的是：这3个函数都返回原始的元素集合，而不是进行操作后的集合，因此，进行链式操作时，
会出现意想不到的情况。 例如：$('span').wrap('<li></li>').wrapAll('<ol></ol>')，首先选择所有的span标签，
然后用li标签包含每个span标签，而由于wrap()的返回值是原始的元素集合，因此wrap()的返回值依然是所有的span标签，
再调用wrapAll()后，用ol标签包含所有的span标签，结果就会变成：
<ol>
	<li><span></span><span></span><span></span></li>
</ol>
<li></li>
<li></li>
因此，如果要对所有的span标签创建列表，应该先wrapAll()，再wrap()：$('span').wrapAll('<ol></ol>').wrap('<li></li>')
```

* 反向操作。前面的insertBefore()、insertAfter、prependTo、appendTo都是将`.`前面的元素对`.`后面的元素进行插入操作，因此，`.`后面的元素必须是已有的元素，即目标必须是个选择器(选择一个已经存在的元素)。与这些方法对应的有另外的一类方法：

```
before: $(selector).before(element)，将element插入到selector选择的元素的外面的前面。
after: $(selector).after(element)，将element插入到selector选择的元素的外面的后面。
prepend: $(selector).after(element)，将element插入到selector选择的元素的里面的前面。
append: $(selector).append(element)，将element插入到selector选择的元素的里面的后面。
类似地：
1 插入的目的必须是个选择器，而不能是个新创建的元素。
2 返回值依然是$(selector)，而不是进行插入后的元素，因此，插入到外面的方法(before和after)选择的元素还是之前的元素，
而插入到里面的方法(prepend和append)还是选择的是之前的元素，但element已经插入到了里面。
```

* 复制元素。clone()会创建元素的副本，因此，应该使用selector选择元素，然后再克隆：$(selector).clone()，当要克隆标签的事件时，需要给clone()添加参数。

### 3 练习

1 修改添加back to top链接的代码，以便这些链接只从第四段后面才开始出现。

```javascript
$('<a href="#top"></a>').insertAfter('div.chapger p:gt(3)');
```

2 在单击back top top链接时，为每个链接后面添加一个新段落，其中包含You were here字样，确保链接仍然有效。

```javascript

```

3 在单击作者名字时，把文本改为粗体(通过添加一个标签，而不是操作类或CSS属性)。

```javascript
$('#f-author').click(function() {
	$(this).wrap('<b>');
});
```

4 在随后单击粗体作者名字时，删除之前添加的`<b>`元素(也就是在粗体文本与正常文本之间切换)。

```javascript

```

5 为正文中的每个段落添加一个inhabitants类，但不能调用.addClass()方法，确保不影响现有的类。

方法1：

```javascript
var element = document.getElementsByTagName('p');
if(element.className == '') {
	element.className = 'inhabitants';
}
else {
	element.className += ' inhabitants';
}
```

方法2：

```javascript
var class_attr = $('p').attr('className');
if(class_attr == '') {
	$('p').attr('className', 'inhabitants');
}
else {
	$('p').attr('className', class_attr + ' inhabitants');
}
```