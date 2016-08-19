## prototype原型链继承

前言：

> 相比于C++中的继承，javascript中的prototype原型链继承不太好理解。因此，决心了解下prototype原型链。

### 1 函数与对象

C++中的基本概念包含类和实例，分别对应类型和对象。使用class关键字创建一个类，然后，就可以创建该类的实例-对象。另外，在创建类时，也可以指定该类的父类，此时，当创建该类的对象时，会先调用父类的构造函数，再调用子类的构造函数，以此来创建该对象，在调用方法时，还可以利用多态等特性。

javascript中函数的概念与C++中的类的概念类似，函数即可以当作普通函数调用，又可以理解为类的构造函数。但是，javascript中所有的都是对象，包括函数。

```javascript
function animal(name) {
	this.name = name;
}

var lion = new animal('lion');
console.log(lion.name);
```

如上所示，创建了一个函数，使用该函数创建了一个对象。但是，与C++不同的是，此处使用了this关键字(javascript中this的意义与C++的比较类似，都是指调用的当前对象)，说明就是设定当前对象的属性。然后，在下面就可以使用这个函数创建一个对象。

虽然这里看起来跟C++的差别很大，但是，换个角度看，还是比较好理解的：

C++中，创建对象的第一步就是调用构造函数，同样的，javascript中创建了一个函数，其实就是创建一个类，这个类的构造函数就是该函数本身。

### 2 通过函数创建对象带来的问题

javascript中"一切皆是对象"，当采用上面的方法为"类"添加函数属性时：

```javascript
function animal(name) {
	this.name = name;
	this.cry = function() {
		console.log(this.name + ' cry');
	}
}

var cat = new animal('cat');
var lion = new animal('lion');
```

如上代码所示，创建了一个animal函数，给该函数添加了两个属性，其中一个属性是函数cry(对象)。当创建两个对象时，由于一切皆是对象，那么，cat和lion中分别有两个属性：name和cry，name属性是很好理解的，它类似于C++中的局部变量，而cry则不同，虽然它是函数，但是它也是局部变量，也就是说，cat和lion中都有cry这两个函数(对象)，即，虽然它是个函数，但是，却不被两个cat和lion两个对象共享，而C++中，函数只是一段代码，它是被多个对象共享的。这就会造成系统资源的浪费。

此时的对象示意图如下：

![](https://github.com/luofengmacheng/web_learning/blob/master/pics/prototype1.png)

为了解决这个问题，在给函数添加函数属性时，可以将函数添加到prototype对象中：

```javascript
function animal(name) {
	this.name = name;
}

animal.prototype.cry = function() {
	console.log(this.name + ' cry');
}

var cat = new animal('cat');
var lion = new animal('lion');
```

此时(cat.cry == lion.cry) == true，也就是说，此时cat调用的cry跟lion调用的cry是同一个对象(函数)。

此时的对象示意图如下：

![](https://github.com/luofengmacheng/web_learning/blob/master/pics/prototype2.png)