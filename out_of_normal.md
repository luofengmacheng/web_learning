## CSS中元素脱离文档流的问题

本文摘抄自知乎：[CSS脱离文档流到底是什么意思，脱离文档流就不占据空间了吗？脱离文档流是不是指该元素从DOM树中脱离？](http://www.zhihu.com/question/24529373/answer/29135021)

Q: 脱离文档流就不占据空间了吗？

A: 可以这么说。更准确地一点说，是一个元素脱离文档流（out of normal flow）之后，其他的元素在定位的时候会当做没看见它，两者位置重叠都是可以的。

Q: 脱离文档流是不是指该元素从dom树中脱离？

A: 不是，用浏览器的审查元素就可以看到脱离文档流的元素（例如被float了）依然会出现在dom树里，下面的截图里也可以看到。那么脱离文档流是什么样子的呢？下面是上锤子的时间~ 以前面文档里写的三种布局方式为例下文中文档的HTML代码如下：

``` html
<body>
	<div id="outofnormal">
		Out of normal: 
		Lorem ipsum dolor sit amet, consectetur adipisicing elit. Sequi esse impedit autem praesentium magni culpa, amet corporis, veniam consequatur voluptates temporibus. Voluptates eius similique asperiores cupiditate fugit hic atque quisquam?
	</div>
	<h2>Normal Content</h2>
	<p>Lorem ipsum dolor sit amet, consectetur adipisicing elit. Nostrum praesentium nam tempora beatae quis nobis laboriosam alias aliquid, tenetur exercitationem. Odio, aperiam, illo! Eveniet natus dignissimos architecto velit eligendi id!</p>
	<p>Lorem ipsum dolor sit amet, consectetur adipisicing elit. Rem reprehenderit velit nam delectus distinctio at unde aliquid officia illo, tempore vitae et incidunt non, ut eos nesciunt quaerat. Enim, minus.</p>
</body>
```

CSS代码如下，为了看得更清楚，加一个padding

```css
#outofnormal {
	width: 200px;
	background-color: cyan;
	padding: 10px;
}
```

首先普通情况下（也就是在normal flow里）是这样的
要被玩的div：

![](https://github.com/luofengmacheng/web_learning/blob/master/pics/pic1.jpg)

跟在后面的h2：

![](https://github.com/luofengmacheng/web_learning/blob/master/pics/pic2.jpg)

可以看到两者是垂直排列，padding互相顶着。3D视图的话就是这样，大家排排坐：

![](https://github.com/luofengmacheng/web_learning/blob/master/pics/pic3.jpg)

第二种情况：加上float:left了之后，蓝色的div就脱离文档流了，变成了这样：

![](https://github.com/luofengmacheng/web_learning/blob/master/pics/pic4.jpg)

因为蓝色的div脱离了文档流，跟在后面的h2和p的盒子都当做没看到这个div的样子去定位，所以他们都顶着浏览器左边和顶部的边框。但是有趣的是，h2和p里面的文本（属于content flow）却都看到了这个被float的div，在自己的盒子里往右推，飘到了蓝色div的边上。这就是float的特性，其他盒子看不见被float的元素，但是其他盒子里的文本看得见。

![](https://github.com/luofengmacheng/web_learning/blob/master/pics/pic5.jpg)

3D视图的话就是这样:

![](https://github.com/luofengmacheng/web_learning/blob/master/pics/pic6.jpg)

![](https://github.com/luofengmacheng/web_learning/blob/master/pics/pic7.jpg)

![](https://github.com/luofengmacheng/web_learning/blob/master/pics/pic8.jpg)

为什么能插呢？因为蓝色div被旁边的盒子无视了呀~第三种情况：absolute positioning。删掉float: left，加上postion: absolute。和float一样的是，旁边的盒子无视了蓝色div的存在，也是顶着左边边框定位。但是~ 文本也无视了蓝色div的存在，顶着左边边框定位！

![](https://github.com/luofengmacheng/web_learning/blob/master/pics/pic9.jpg)

![](https://github.com/luofengmacheng/web_learning/blob/master/pics/pic10.jpg)

3D视图下也是成功无视之，插入~

![](https://github.com/luofengmacheng/web_learning/blob/master/pics/pic11.jpg)

总结：脱离文档流，也就是将元素从普通的布局排版中拿走，其他盒子在定位的时候，会当做脱离文档流的元素不存在而进行定位。需要注意的是，使用float脱离文档流时，其他盒子会无视这个元素，但其他盒子内的文本依然会为这个元素让出位置，环绕在周围。而对于使用absolute 
positioning脱离文档流的元素，其他盒子与其他盒子内的文本都会无视它。