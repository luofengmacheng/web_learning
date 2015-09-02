## bootstrap学习

### 1 bootstrap基本模版的三个<meta>的含义

在bootstrap官网中有个使用bootstrap的基本模版，在head标签中有三个meta标签，它们的含义如下：

``` html
<meta charset="utf-8"> 使用UTF8字符编码
<meta http-equiv="X-UA-Compatible" content="IE=edge"> 让IE浏览器运行最新的渲染模式，使得bootstrap能够在较低版本的IE中拥有最好的绘制效果
<meta name="viewport" content="width=device-width, initial-scale=1"> 采用响应式布局，使其在各个尺寸的屏幕上表现良好
```

### 2 在html中引用bootstrap

bootstrap主要包含两个部分：css和js，因此，如果要在html中使用bootstrap，需要引入bootstrap的css和js。

``` html
<link rel="stylesheet" href="css/bootstrap.min.css">
<script src="//cdn.bootcss.com/jquery/1.11.3/jquery.min.js"></script>
<script src="js/bootstrap.min.js"></script>
```

由于bootstrap中的js插件基于jQuery，因此，需要在引入bootstrap的js之前引入jQuery。

### 3 导航

bootstrap中的导航实际上就是我们常说的tab，它的构建界面的过程中很常用。

``` html
<ul class="nav nav-tabs" id="myTab"> 
	<li><a href="#home">Home</a></li>
	<li><a href="#profile">Profile</a></li>
	<li><a href="#messages">Messages</a></li>
	<li><a href="#settings">Settings</a></li>
</ul> 

<div class="tab-content">
	<div class="tab-pane" id="home">page1</div>
	<div class="tab-pane" id="profile">page2</div>
	<div class="tab-pane" id="messages">page3</div>
	<div class="tab-pane" id="settings">page4</div>
</div>

<script>
$(function () {
	$('#myTab a:first').tab('show');//初始化显示哪个tab

	$('#myTab a').click(function (e) { 
		e.preventDefault();//阻止a链接的跳转行为 
		$(this).tab('show');//显示当前选中的链接及关联的content 
	})
})
</script>
```

上述代码使用nav中的nav-tabs类构建导航页，包含了4个tab：home、profile、messages、settings。用类tab-content创建了4个页面，分别对应4个tab。在js代码中，为每个导航链接绑定事件，并将tab中的第一个链接设为初始时显示。

### 4 分页

bootstrap中提供了分页，但是，其实只是创建了多个页面的页码的一个链接，下面是bootstrap官网的例子：

``` html
<nav>
  <ul class="pagination">
    <li>
      <a href="#" aria-label="Previous">
        <span aria-hidden="true">&laquo;</span>
      </a>
    </li>
    <li><a href="#">1</a></li>
    <li><a href="#">2</a></li>
    <li><a href="#">3</a></li>
    <li><a href="#">4</a></li>
    <li><a href="#">5</a></li>
    <li>
      <a href="#" aria-label="Next">
        <span aria-hidden="true">&raquo;</span>
      </a>
    </li>
  </ul>
</nav>
```

这个例子用了类pagination，使用ul、li标签创建了5个页码的链接。但是，实际使用过程中，页的数量通常是变动的，下面以数据库表为例展示创建动态可变的分页。

创建动态的分页的基本思想是：第一次查询总的记录数，根据总的记录数和每页要显示的记录数，得到总的页面数，以及最后一页的记录数，当点击某个页面时，获取页码，根据每页的记录数和页码计算要获取的记录的偏移量，然后就可以利用SQL中的limit语句获取要显示页面的数据，再加载到页面中。

#### 4.1 页面代码

``` html
<nav>
	<ul class="pagination" id="data_list_pager"></ul>
</nav>

<select class="form-control" style="height:40px;width:70px;" id="records_per_page">
	<option value="10" selected="selected">10</option>
	<option value="20">20</option>
	<option value="40">40</option>
</select>

<table>
	<thead></thead>
	<tbody></tbody>
</table>
```

页面上使用pagination类创建了一个分页，使用form-control创建了一个单选下拉框，用户可以选择每页显示的记录的数量。

#### 4.2 js代码

``` javascript
// 获取总的记录数和每页的记录数
var len = data['all_len'];
var per_page = $('#records_per_page').val();

// 计算要显示的页面数和最后一页的记录数
var page_num = Math.ceil(len / per_page);
var last_page_num = len % per_page;
if(!(len % per_page)) {
    last_page_num = per_page;
}

// 根据页面数创建分页的页码链接
var data_list_pager = $('#data_list_pager');
data_list_pager.html("");
for(var i = 0; i < page_num; ++i) {
    $('<li><a href="#" class="page_link">'+(i+1)+'</a></li>').appendTo(data_list_pager);
}
// 将第一页激活
data_list_pager.children().eq(0).addClass('active');

// 保存当前页码
data_list_pager.data('curr', 0);

// 为所有的分页链接绑定函数
data_list_pager.find('li .page_link').click(function () {
	// 获取点击的页码
    var clicked_page = $(this).html().valueOf() - 1;
    // 跳转到该页
    go_to(clicked_page, per_page);
    return false;
});

function fill_data_to_table(data) {
	// 根据返回数据的表头域构造表头
	var table_th = "<tr>";
	table_th += "<th>操作</th>";
	var th_len = data['tb_fields'].length;
	for(var k = 0; k < th_len; ++k) {
	table_th += "<th>" + data['tb_fields'][k] + "</th>";
	}
	table_th += "</tr>";

	// 根据返回数据的记录数构造表体
	var table_td = "";
	var td_len = data['data_len'];

	// 展示第一页
	for(var i = 0; i < td_len; ++i) {
		table_td += "<tr>";
		table_td += "<td><a href='javascript:void(0);' onclick='edit_record(this)'>编辑</a></td>";
		for(var j = 0; j < th_len; ++j) {
			table_td += "<td>" + data['tb_data'][i][data['tb_fields'][j]] + "</td>";
		}
		table_td += "</tr>";
	}

	$('thead').html(table_th);
	$('tbody').html(table_td);
}

function go_to(clicked_page, per_page) {
	var data_list_pager = $('#data_list_pager');

	// 将所有的分页的所有链接都置为未激活，然后激活跳转到的页面
	data_list_pager.children().removeClass('active');
	data_list_pager.children().eq(clicked_page).addClass('active');

	var dbn = $('#sql_dbname').val();
	var tbn = $('#sql_tbname').val();
	var pgsize = $('#records_per_page').val();
	
	// 根据跳转的页面和每页的记录数计算跳转的页面的记录的偏移量
	var pgoff = clicked_page * per_page;
	var tb_data = {'dbname': dbn, 'tbname': tbn, 'page_off': pgoff, 'page_size': pgsize, 'where': where};

	// 通过ajax方法向后台请求数据
	$.post('index.php/Db_control/show_tb',
		tb_data,
		function(data) {
			fill_data_to_table(data);
		},
		"json"
	);
}
```

### 5 树形结构

#### 5.1 页面代码

``` html
<div class="tree well">
	<ul>
		<li>
			<span><i class="glyphicon glyphicon-folder-open"></i>msg_board</span>
			<ul>
				<li>
					<span><i class="glyphicon glyphicon-th-list"></i><a href="javascript:void(0);">message</a></span>
				</li>
				<li>
					<span><i class="glyphicon glyphicon-th-list"></i><a href="javascript:void(0);">user</a></span>
				</li>
				<li>
					<span><i class="glyphicon glyphicon-th-list"></i><a href="javascript:void(0);">firewall</a></span>
				</li>
			</ul>
		</li>
	</ul>
</div>
```

#### 5.2 js代码

``` javascript
$(function(){
	// 选择类为tree的元素，即整棵树，再选择树中所有有直接子孩子ul的li元素，即msg_board的span的父节点li
	// 为刚选择的所有的li添加类parent_li，它就是树中的可以打开的节点，即非叶节点
	// 同时，找到刚选择的所有的li的直接子孩子span，并设置title属性为Collapse this brance
	$('.tree li:has(ul)').addClass('parent_li').find(' > span').attr('title', 'Collapse this branch');

	// 为所有的可以展开的span绑定函数
	$('.tree li.parent_li > span').on('click', function (e) {
		// 找到可以展开的span的父节点中类为parent_li的li元素
		// 然后找到li元素的直接子孩子ul，再找到ul的直接子孩子li
		var children = $(this).parent('li.parent_li').find(' > ul > li');
		// 找到孩子中所有状态隐藏的元素
		if (children.is(":visible")) {
			// 如果孩子是可见的，就隐藏
			children.hide('fast');
			$(this).attr('title', 'Expand this branch');
		} else {
			// 如果孩子是隐藏的，就显示
			children.show('fast');
			$(this).attr('title', 'Collapse this branch');
		}
		e.stopPropagation();
	});
	// 将所有的孩子隐藏
	$('.tree li.parent_li > span').each(function (index, elem) {
		var children = $(this).parent('li.parent_li').find(' > ul > li');
		children.hide();
	})
});
```