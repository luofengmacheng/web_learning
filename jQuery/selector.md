## 元素选择器

### 1 基本选择器

* id选择器。选择id为file的元素：$('#file')
* class选择器。选择class为file的元素：$('.file')
* 标签选择器。选择标签为div的元素：$('div')
* 所有选择器。选择所有的元素：$('*')
* 并列选择器。选择p标签和div标签的元素：$('p, div')

### 2 层次选择器

* 直系子元素。选择div标签的第一代子span标签：$('div > span')
* 下一个兄弟元素，等同于next()方法。选择class为file的下一个div元素：$('.file + div')
* 所有的兄弟元素，等同于nextAll()。选择class为file的所有div兄弟元素：$('.file ~ div')

### 3 过滤选择器

过滤选择器会在已经得到的元素集合中查找满足某个条件的元素集合。

#### 3.1 基本过滤选择器

* :first和:last。选择第一个span元素：$('span: first')，选择最后一个span元素：$('span: last')
* :not，选择不满足某个条件的元素。选择class不是file的div元素：$('div: not(.file)')
* :even和:odd，取索引为偶数或奇数的元素。最常见的应用就是，表格中的行呈条纹显示：$('tr: even')
* :eq(i)，取指定索引的元素
* :header。取h1~h6标题元素


#### 3.2 内容过滤选择器

* :contains(text)，选择包含text的元素。选择包含'text'的td元素：$('td: contains("text")')
* :empty，选择不包含子元素或者文本为空的元素，其实就是不包含任何内容的元素。例如：<td></td>
* :has(selector)，选择包含选择器的元素
* :parent，选择包含子元素或文本的元素，与:empty相反

#### 3.3 可见性过滤选择器

* :hidden，取不可见元素。它只匹配隐藏的并且不占用空间的元素：display:none或者<input type="hidden" />。
* :visible，取可见元素，与:hidden相反

### 4 属性过滤选择器

* [attribute]，取拥有attribute属性的元素。
* [attribute=value]和[attribute!=value]，取attribute属性值等于value或者不等于value的元素
* [attribute^=value] [attribute$=value] [attribute*=value]，取属性值以value开始、以value结束、包含value的元素
* [selector1][selector2]，复合型属性过滤选择器，需要同时满足多个条件

### 5 子元素过滤选择器

* :first-child和last-child，选择属于父元素的第一个孩子的元素集合。例如，选择将div作为第一个孩子的所有元素：$('div: first-child')
* :only-child，选择属于父元素的唯一子元素的元素集合
* :nth-child(i)，选择属于父元素的第i个子元素的元素集合

### 6 表单对象选择器

* :input，选择input元素，包括input，textarea，select，button等
* :text，选择单行文本框，input[type=text]
* :password，选择密码框，input[type=password]
* :radio，选择单选框，input[type=radio]
* :checkbox，选择复选框，input[type=checkbox]
* :submit，选择提交按钮，input[type=submit]
* :reset，选择重置按钮，input[type=reset]
* :button，选择按钮元素，input[type=button]
* :file，选择上传域元素，input[type=file]
* :hidden，选择不可见元素，input[type=hidden]

### 7 表单属性选择器

* :enabled和:disabled，选择是否设置了enabled和disabled的元素
* :checked，取单选框或者复选框中选中的元素
* :selected，取下拉列表中被选择的元素

### 8 小结：

从以上的选择器可以重新分成以下几大类：

* 基本选择器，最常用的几个选择器：ID、类、标签、所有、并列。
* 层次选择器，主要有两种，一种选择相应的子元素，一种是某类元素满足父元素的某一条件的元素，分别对应上面的2和5。
* 内容选择器，返回内容满足某一条件的元素。
* 条件选择器，包括3。
* 属性选择器，包括3.3，4，7。