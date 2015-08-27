## javascript

### 1 遍历表单的所有字段

### 2 innerHTML()和innerText()，obj.html()和obj.text()

innerHTML()和innerText()是javascript中的方法，用于返回元素的内容。
obj.text()和obj.html()是jQuery中的方法啊，也是操作元素的内容。其中，innerHTML()与obj.html()相对应，都可以对元素的内容进行操作，而且元素的内容可以包含标签；而innerText()与obj.text()相对应，也可以对元素的内容进行操作，而且元素的内容不能包含标签。也就是说，如果标签的内容只有文本，则innerHTML()与innerText()的返回值一样，但是，如果标签的内容中有标签，则innerHTML()会返回包含标签的所有内容，而innerText()只会返回元素中的文本。

例如：

``` javascript
<p><a href="http://www.qq.com">tencent</a></p>

$("p").html(); // <a href="http://www.qq.com">tencent</a>
$("p").text(); // tencent
```

需要注意的是：如果使用这些方法设置内容时，如果内容中有标签，就必须使用innerHTML()或html()。

### 3 table中的各种标签

根据表格的组成，可以将表格分为三个部分：表头(表的列名)，表干(表的数据)，表注(表的注释)。

因此，html中的table标签也分为三大部分：

表头：

* <thead></thead> 定义表头
* <th></th> 定义表头的单元格

表干：

* <tbody></tbody> 定义表干
* <tr></tr> 定义表的一行，表的三部分都要用
* <td></td> 定义表干的单元格，表干和表注要用

表注：

* <tfoot></tfoot>

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