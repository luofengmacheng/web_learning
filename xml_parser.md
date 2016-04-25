## XML解析

### 1 前言

在开发系统时，如果键值对(ini)文件已经不能满足时，可以考虑使用XML作为系统的配置文件，因此，解析XML文件就成为了许多系统的标配。

### 2 Python解析XML

Python提供了三种解析XML的方式：

* SAX 事件驱动
* DOM
* Element Tree

下面的例子采用DOM的方式。

### 3 XML文件的解析

#### 3.1 XML文件的打开

``` Python
def parse(self, xml_file):
		'''

		'''
		self.xml_doc = minidom.parse(xml_file)
		self.xml_root = self.xml_doc.documentElement
```

#### 3.2 解析

``` Python
def __get_elements_by_tag_name_child(self, node, tag_name):
		'''
		获取node的所有[直接孩子标签]的标签名等于tag_name的标签
		'''
		elements = []
		for element in node.childNodes:
			if element.nodeType == element.ELEMENT_NODE and tag_name == element.nodeName:
				elements.append(element)
		return elements

	def __parse_node_to_dict(self, node):
		if node == None:
			return

		if node.nodeType is not node.ELEMENT_NODE:
			return

		key_dict = list()
		node_dict = OrderedDict()
		for element in node.childNodes:
			if element.nodeType == element.ELEMENT_NODE:
				if element.nodeName not in key_dict:
					key_dict.append(element.nodeName)
					if element.childNodes.length == 1:
						'''
						当节点只有一个子节点时，说明该节点是一个包含文本的节点
						'''
						node_dict[element.nodeName] = element.firstChild.nodeValue
					else:
						'''
						当节点不止一个孩子节点时，说明该节点是个父节点，则需要对它进行递归解析
						此时，需要判断是否跟它有重复的节点
						'''
						#child_arr = node.getElementsByTagName(element.nodeName)
						child_arr = self.__get_elements_by_tag_name_child(node, element.nodeName)
						if len(child_arr) > 1:
							node_dict[element.nodeName] = list()
							for child in child_arr:
								node_dict[element.nodeName].append(self.__parse_node_to_dict(child))
						else:
							node_dict[element.nodeName] = self.__parse_node_to_dict(element)


		return node_dict

	def xml_file_to_dict(self, dictionary):
		'''
		将XML文件转换成python中的列表或者字典
		'''
		for element in self.xml_root.childNodes:
			if element.nodeType == element.ELEMENT_NODE:
				dictionary[element.nodeName] = self.__parse_node_to_dict(element)
```