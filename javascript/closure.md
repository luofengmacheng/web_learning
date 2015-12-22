## 闭包

### 1 什么是闭包

闭包是javascript中很难理解的一部分，那么，什么是闭包呢？

简言之，闭包就是一个函数A里面的另一个函数B，函数B由于在A中，就拥有了函数A运行的环境，通常就是变量，因此，将函数B返回，就可以在函数A外面利用函数B修改函数A内部的变量的值。

下面给出一个简单的例子：

假设在函数outer()内部有个变量x，需要在函数outer()外部访问变量x的值，该怎么办呢？

```javascript
function outer() {
	var x = 4;
}

???
```

javascript中，在outer()函数中都可以访问x，然而，在outer()外面则不能访问x，更不能修改x的值。

这时，可以在outer()内部创建一个inner()闭包函数。

```javascript
function outer() {
	var x = 4;

	function inner() {
		console.log(x);
	}

	return inner;
}

var test = outer();
test();
```

如上所示，在outer()内部创建了inner()，在inner()内部访问了x，然后返回inner。最后，在outer()外面利用outer()的返回值访问x。

通过上面这个例子可以知道，`闭包就是一个内部函数，这个内部函数可以访问内部函数所在的外部函数的变量`。

### 2 常见的使用方法

上例将某个函数返回，外面就可以通过这个函数访问或者修改outer()函数的变量。

也可以返回多个函数：

```javascript
function outer() {
	var x = 4;

	return {
		increment: function() {
			x++;
		},

		get: function() {
			return x;
		},

		set: function(m) {
			x = m;
		}
	};
}

var test = outer();
console.log(test.get());
test.increment();
console.log(test.get());
```

outer()函数返回一个对象，这个对象包含了三个闭包，使用这三个闭包就可以对函数中的变量进行操作。

### 3 使用场景

#### 3.1 回调函数

javascript中大量地使用回调函数，常用的有：setTimeout()，setInterval()，ajax回调函数，事件处理函数。

setTimeout()能够设置一段时间后，执行一系列行为，而setInterval()则是每隔一段时间，执行一系列行为。

```javascript
function test() {
	var counter = 5;

	var time = setTimeout(function() {
		console.log(counter);
	}, 5000);
}

test();
```

上面的代码表示：5秒后执行一个函数，这个函数打印counter的值。

同样的，setInterval()有：

```javascript
function test() {
	var counter = 5;

	var time = setInterval(function() {
		counter++;

		if(counter >= 100) {
			clearInterval(time);
		}
	}, 5000);
}

test();
```

每隔5秒执行一个函数，该函数对counter累加，当累加的值超过99时，则清除定时器。

#### 3.4 模块化开发

利用闭包还能够建立模块化编程：

```javascript
function test() {
	var x = 5;

	function add1() {
		x += 1;
	}

	function add2() {
		x += 2;
	}

	function add3() {
		x += 3;
	}

	function get() {
		return x;
	}

	return {
		add1: add1,
		add2: add2,
		add3: add3,
		get: get
	};
}

var t = test();
console.log(t.get());
t.add1();
console.log(t.get());
t.add2();
console.log(t.get());
t.add3();
console.log(t.get());
```

test()函数返回一个对象，这个对象中包含了4个方法，它们都对x进行操作。不过，个人倒不喜欢这种模块化方式，还是使用prototype吧。

### 4 循环

假设要实现这样一个功能：遍历一个数组，打印元素在数组中的位置，但是，打印操作要推迟1秒。

```javascript
function test(arr) {
	for(var i = 0; i != arr.length; ++i) {
		setTimeout(function() {
			console.log(i + 1);
		}, 1000);
	}
}

var arr = [1, 2, 3, 4, 5];
test(arr);
```

遍历arr数组，对每个元素设置一个定时器，1秒钟后打印元素所在的位置。但是，运行结果却是：`5 5 5 5 5`

这是因为，setTimeout()的定时调用函数是一个闭包，因此，5次调用均是test()中的同一个变量，由于1秒钟以后，i已经变成5，因此，5次打印的值全是5。

为了解决这个问题，就必须让console.log()调用的i不是test()中的i，而是每次调用都创建一个新的拷贝：

```javascript
function test(arr) {
	for(var i = 0; i != arr.length; ++i) {
		setTimeout((function(j) {
			console.log(j);
		})(i), 1000);
	}
}

var arr = [1, 2, 3, 4, 5];
test(arr);
```

如上所示，每次调用时，都创建i的一个副本j传递给函数。

### 5 深入理解闭包

上面讲了这么多，都是关于闭包的使用，那么，闭包究竟是如何实现的呢？

```javascript
function test() {
	var x = 5;
}

function test2() {
	var y = 5;

	function test3() {
		console.log(y);
	}

	return test3;
}

test();

var t = test2();
t();
```

如上面代码，x是test()的局部变量，因此，在调用test()后，作为局部变量的x所占用的内存会被清除。而对y而言，虽然它是test2()是局部变量，但由于调用了test2()返回闭包，而且将它赋给全局变量t，这就需要保存test3()所在的环境，由于test2()是test3()的父函数(所以，test3()的环境依赖于test2()的环境)，因此，即使test2()，它内部使用的环境也会保存在内存中，于是，使用t()时，调用的y都是对同一个y的多次使用。

### 6 忠告

虽然有用，但是，它也会带来一些不好的影响，下面给出一些忠告：

* 不到万不得已，不要使用闭包。
* 不要在IE中使用闭包。
* 闭包所使用的变量会长期保存在内存中，而不会像普通函数的变量一样(使用后即被回收)，因此，大量地使用闭包会造成大量的内存消耗。
* 要小心地在循环中使用闭包。