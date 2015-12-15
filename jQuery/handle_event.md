## 事件处理

### 1 处理简单的事件

一般地，为某个标签添加事件有两种方式：

* 直接在标签上为某个事件添加处理函数，例如，`<a id="test" onclick="handler()"></a>`
* 在js代码中为某个标签添加处理函数

但是，涉及到界面开发的一般都强调行为和表现分离，因此，在js代码中为标签添加事件处理程序是比较好的方式。

#### 1.1 在哪里为标签添加事件

那么，问题来了，应该在哪里为标签添加事件呢？

为标签添加事件的行为应该出现在页面加载过程中，此时，有两个地方可供选择：

* window.onload 为onload添加处理函数，然后在函数中添加给标签绑定事件的代码；
* $(document).ready() 参数是匿名处理函数，可以在该处理函数中添加给标签绑定事件的代码。

它们之间的不同之处在于：

```
window.onload中的函数在所有依赖的文件都下载完毕，包括依赖的js文件、图片文件，那么，当用户在页面加载过程中进行操作时，可能会造成无法预料的行为。
$(document).ready()中的函数执行于DOM树构造完毕时，此时，可能依赖的js文件、图片文件等没有下载完毕，那么，当用户操作时，一些相关的属性(如图片的大小等)还未可知。
```

因此，可以根据需要自行选择，但是，通常的做法还是在$(document).ready()中编写事件绑定。

#### 1.2 事件绑定

jQuery中最常用的就是on了，。

并且，jQuery中提供的简写形式其实也是对on的调用：

$('#element').click(function() {});

另外，还有bind()和delegate()，而且它们低层都是调用on()，因此，建议使用on()进行事件的绑定。

on()、bind()、live()、delegate()的区别

bind()也可以将事件与处理函数绑定，它扫描文档获取所有匹配的元素，将事件绑定到元素上，这将忽略事件冒泡机制。只有当该元素

live()采用了委托机制，对任何一个元素绑定某个事件后，会将该事件委托给document，当事件冒泡到document时，判断产生该事件的元素是否匹配，如果是，则执行事件处理函数。

delegate()也采用了委托机制，只是调用对象就是委托对象。当事件冒泡到委托对象时，判断产生该事件的元素是否匹配，如果是，则执行事件处理函数。

on()是整合了前三种方式的新的事件绑定机制。而且，bind()、live()和delegate()均是通过on()实现的：

```jQuery
bind: function(types, data, fn) {
	return this.on(types, null, data, fn);
}

live: function(types, data, fn) {
	jQuery(this.context).on(types, this.selector, data, fn);
}

delegate: function(selector, types, data, fn) {
	return this.on(types, selector, data, fn);
}
```

下面详细看看on()的使用：

```jQuery
.on(events[,selector][,data],handler)
```

将events绑定到选定的对象上，当选定对象上发生该事件时，查看发生该事件的对象是否符合selector，如果是，则执行handler。当没有selector时，则将事件绑定给选定的对象。

### 2 事件冒泡

#### 2.1 什么是事件冒泡

DOM树中的元素是有层次关系的，当下层的元素捕获一个事件时，处理完成后，事件会传播到它的父元素进行处理。

#### 2.2 事件冒泡的带来的问题

当A元素捕获了一个事件，处理之后，B元素也捕获了该事件，但是，一种情况是，这个事件对A和B两个元素可能的意义是不同的，从而就会发生问题。例如，当鼠标离开A时，会触发一个mouseout事件，A捕获并处理了，B(A在B中)也捕获并处理，但是，鼠标并没有离开B，这就出现了问题。

#### 2.3 如何阻止事件冒泡

有两种方式阻止事件冒泡：

* 使用stopPropagation()，在处理函数中调用event.stopPropagation()就可以阻止该事件被别的元素处理。
* return false，在事件处理函数的最后调用`return false;`就可以阻止该事件被别的元素处理。

#### 2.4 阻止默认操作

有时，按钮或者其它元素有些默认的行为，当我们需要阻止默认行为时，需要使用preventDefault()。

注意：上面提到的return false;方法可以阻止事件冒泡，同时也能阻止默认操作。

参考文档：

1 [jQuery的.bind()、.live()和.delegate()之间的区别](http://article.yeeyan.org/view/213582/179910)