## javascript

### 1 关于网站开发

* 用PhpStorm等IDE开发时，不要总是点run运行，可以直接在页面进行刷新。

* 当更改代码时，如果出现与前次结果一样(但是，应该不一样)，可以尝试清空缓存(ctrl + F5)。

* 在进行调试时，尽量使用console.log()，而不用alert()。

### 2 遍历表单的所有字段

### 3 innerHTML()和innerText()，obj.html()和obj.text()

innerHTML()和innerText()是javascript中的方法，用于返回元素的内容。
obj.text()和obj.html()是jQuery中的方法啊，也是操作元素的内容。其中，innerHTML()与obj.html()相对应，都可以对元素的内容进行操作，而且元素的内容可以包含标签；而innerText()与obj.text()相对应，也可以对元素的内容进行操作，而且元素的内容不能包含标签。也就是说，如果标签的内容只有文本，则innerHTML()与innerText()的返回值一样，但是，如果标签的内容中有标签，则innerHTML()会返回包含标签的所有内容，而innerText()只会返回元素中的文本。

例如：

``` javascript
<p><a href="http://www.qq.com">tencent</a></p>

$("p").html(); // <a href="http://www.qq.com">tencent</a>
$("p").text(); // tencent
```

需要注意的是：如果使用这些方法设置内容时，如果内容中有标签，就必须使用innerHTML()或html()。

### 4 table中的各种标签

根据表格的组成，可以将表格分为三个部分：表头(表的列名)，表干(表的数据)，表注(表的注释)。

因此，html中的table标签也分为三大部分：

表头：

``` html
<thead></thead> 定义表头
<th></th> 定义表头的单元格
```

表干：

``` html
<tbody></tbody> 定义表干
<tr></tr> 定义表的一行，表的三部分都要用
<td></td> 定义表干的单元格，表干和表注要用
```

表注：

``` html
<tfoot></tfoot>
```

因此，定义一个表格的完整格式就是：

``` html
<table>
	<thead>
		<tr>
			<th>列名1</th>
			<th>列名2</th>
		</tr>
	</thead>

	<tbody>
		<tr>
			<td>数据1</td>
			<td>数据2</td>
		</tr>

		<tr>
			<td>数据3</td>
			<td>数据4</td>
		</tr>
	</tbody>

	<tfoot>
		<tr>
			<td>表注1</td>
			<td>表注2</td>
		</tr>
	</tfoot>
</table>
```

但是，在网页上，只有表头略有区别(表头会加粗)，而且，一般都不会定义表注。而且经常会忽略<thead>和<tbody>。于是，常见的表格的表示就是：

``` html
<table>
	<tr>
		<th>列名1</th>
		<th>列名2</th>
	</tr>

	<tr>
		<td>数据1</td>
		<td>数据2</td>
	</tr>

	<tr>
		<td>数据3</td>
		<td>数据4</td>
	</tr>
</table>
```

### 5 json的使用

json是web中用于数据传输的常用格式，关于它的介绍可以参看：[JSON入门指南](http://www.ibm.com/developerworks/cn/web/wa-lo-json/#ibm-pcon)

同时，在编程时，可以在网上[Json.cn](http://json.cn/)测试字符串是否是正确的json字符串。

#### 5.1 json中的数据结构

json中的复合结构分为两种：对象和数组。其中，对象由{ }表示，包含一系列的键值对，键只能是字符串。数组由[]表示，包含多个元素，每个元素之间用,隔开。

#### 5.2 json的使用

json是一种常用的数据交换的格式，只要通信的双方都能够理解json，就能够进行正确的通信。因此，许多web编程语言中都内置了处理json的函数。

* 在jQuery的ajax方法中，有个参数规定预期的服务器响应的数据类型，常用的就是"json"。
* 服务器获取前台的数据之后，进行一些逻辑处理，会返回数据，在php中，可以使用json_encode()将返回给客户端的数据转换成json数据。

* javascript。在jQuery的ajax方法中，有个参数规定预期的服务器响应的数据类型，常用的就是"json"。在javascript中也可以使用JSON解析器，它只能处理json数据，因此，可以防止其它恶意的代码，它有两个方法stringify和parse，分别用于生成和解析json字符串。当构造了一个json对象，要进行传输时，需要转换成字符串，可以使用JSON.stringify()。当接收到json字符串，要进行解析时，需要转换成json对象，可以使用JSON.parse()。
* php。php中也有两个方法处理json：json_encode()和json_decode()，分别用于生成和解析json。

因此，只要前端的js使用JSON.*，后端使用json_*，前台和后台就可以方便的进行数据交互。

### 6 关于错误SyntaxError: Unexpected token ILLEGAL

进行web开发时，遇到了SyntaxError: Unexpected token ILLEGAL错误，开始以为错误是由错误的token码造成的，然后在google中搜索，发现当整数直接调用toString方法时会出现该错误，后来在浏览器的console中错误提示的最后面看到是由于jquery.sparkline.js造成的，点开该文件，发现错误定位在最开始的注释那里：

``` javascript
/**
*
* jquery.sparkline.js
```

于是，将最开始的两个*删除一个，再运行就好了，不过，还是不知道是为什么。

### 7 统一关闭console.log()

在网站调试阶段，会大量使用console.log()打印语句将信息打印到浏览器的调试窗口中，但是，在网站上线后，就需要关闭所有的console.log()。

* 方案一：在js文件开头加上 console.log = function() { }
* 方案二：

写一个统一的函数：

``` javascript
(function (original) {
    console.enableLogging = function () {
        console.log = original;
    };
    console.disableLogging = function () {
        console.log = function () {};
    };
})(console.log);
```

在使用时，可以调用enableLogging()和disableLoggin()打开和关闭console.log()：

``` javascript
console.enableLogging();

console.disableLogging();
```